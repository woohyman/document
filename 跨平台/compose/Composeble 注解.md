- [x] **@Composable的作用是什么？**



# Composeble 注解到底做了什么？

## 前言

了解过 `Compose` 的同学都知道，只需要添加一个 `@Composeble` 注解就可以将函数转化成 `Composeble` 函数，同时 `Composeble` 函数也只能在 `Composeble` 函数中运行。这看起来似乎跟协程比较像，`@Composeble`是不是也像协程一样，往函数中添加了一些参数呢？

我们就一起来看下，`@Composeble`到底做了什么，又是怎么做到的。

## 前置知识

一看到 `@Composeble` 注解，我们很容易就想到注解处理器，但是 `@Composeble` 的解析并不是通过注解处理器来实现的，因为注解处理器只能生成代码，不能修改代码
而 `KCP`（`Kotlin Compiler Plugin`）：即 `kotlin` 编译插件，支持跨平台，`android` 开发可以将它类比为 `kapt`+`transform` 机制，既可以生成代码，也可以修改代码

`@Composeble` 注解的解析就是通过 `KCP` 来实现的

### 什么是 `KCP`

`Kotlin` 编译过程简单来说，就是将 `Kotlin` 源码编译成字节码的过程，具体步骤如下所示：

![](/home/woo/document/图片/image_1-53b266ac5261460d4268fc7dce3b441d.webp)

而 `Kotlin` 编译期插件则在编译过程中提供 `Hook` 时机，让我们可以解析符号，修改字节码生成结果等。 `Kotlin` 库中的不少语法糖都用到了`KCP`，比如 `Kotlin-android-extension`，`@Parcelize` 等，`@Composeble` 注解同样也是通过 `KCP` 解析的

相比`KAPT`，`KCP`主要有以下优点：

1. `KAPT` 是基于注解处理器的，它需要将 `Kotlin` 代码转化成 `Stub` 再解析注解生成代码，常常转化成 `Stub` 的时间比生成代码还要长，而 `KCP` 则是直接解析 `Kotlin` 的符号，因此在编译速度上 `KCP` 比 `KAPT` 要强的多
2. `KAPT` 只能生成代码，不能修改代码，而 `KCP` 不仅可以生成代码，也可以修改代码，可以看作是 `kapt`+`transorm`机制

而`KCP`的缺点则在于，`KCP` 的开发成本太高，涉及 `Gradle Plugin`、`Kotlin Plugin` 等的使用，`API` 涉及一些编译器知识的了解，一般开发者很难掌握。
因此如果只是需要处理注解生成代码，不需要修改代码，通常使用`KSP`就足够了，`KSP` 是对 `KCP` 的一个封装，如果对 `KSP` 的使用详情感兴趣可参见：[告别KAPT！使用 KSP 为 Kotlin 编译提速](https://juejin.cn/post/6979759813467062309)

### `KCP` 的基本概念

上面也说到了，`KCP`的开发成本较高，主要包括以下内容：

![](/home/woo/document/图片/image_2-51099fe551e89ae397eee7970ddf9083.webp)

- `Plugin`：`Gradle` 插件用来读取 `Gradle` 配置传递给 `KCP`（`Kotlin Plugin`）
- `Subplugin`：为 `KCP` 提供自定义 `KP` 的 `maven` 库地址等配置信息
- `CommandLineProcessor`：负责将`Plugin`传过来的参数转换并校验
- `ComponentRegistrar`：负责将用户自定义的各种`Extension`注册到`KP`中，并在合适时机调用

`ComponentRegistrar`是核心入口，所有的`KCP`自定义功能都需要通过这个类注册一些`Extension`接口来实现。

下面列举一些常用的`Extension`接口，大家可以根据需求选用：

- `IrGenerationExtension`，用于增/删/改/查代码
- `DiagnosticSuppressor`，用于抑制语法错误，`Jetpack Compose`有使用
- `StorageComponentContainerContributor`，用于实现`IOC`

## `@Composeble`注解的作用

上面介绍了 `KCP` 的基本概念,下面看下在 `Jetpack Compose` 中 `@Composeble` 注解到底是怎么解析的，又起了什么作用

### 注册 `IrGenerationExtension`

上面我们介绍了 `ComponentRegistrar` 是核心入口，负责将用户自定义的各种 `Extension` 注册到 `KP` 中，并在合适时机调用，而 `IrGenerationExtension` 可以用于修改代码
`Compose`插件的入口为[ComposePlugin](https://github.com/androidx/androidx/blob/35cea032eb39619f1dcad49623727ef3f7ccbc95/compose/compiler/compiler-hosted/src/main/java/androidx/compose/compiler/plugins/kotlin/ComposePlugin.kt)，其中也包括一个`ComposeComponentRegistrar`，`IrGenerationExtension`的注册就是在这里完成的

```kotlin
class ComposeComponentRegistrar : ComponentRegistrar {
    override fun registerProjectComponents(
        project: MockProject,
        configuration: CompilerConfiguration
    ) {
        registerProjectExtensions(
            project as Project,
            configuration
        )
    }
    
    fun registerProjectExtensions(
            project: Project,
            configuration: CompilerConfiguration
    ) {
      IrGenerationExtension.registerExtension(
            project,
            ComposeIrGenerationExtension(
              //...
            )
        )

    }

}    
```



如上所示，注册了 `IrGenerationExtension`,接下来 `IrGenerationExtension` 会调用 `ComposerParamTransformer` 的相关方法，完成参数的填充，后续的主要处理工作都是在 `ComposerParamTransformer` 中处理了

### 添加 `$composer`

上文说到，后续在函数中添加参数的工作主要是在 `ComposerParamTransformer` 中完成的，具体调用了 `IrFunction.withComposerParamIfNeeded`

```kotlin
    private fun IrFunction.withComposerParamIfNeeded(): IrFunction {
        // 如果不是`Compose`函数，则直接返回，后续不再处理
        if (!this.hasComposableAnnotation()) {
            return this
        }

        // 如果此函数是作为参数的`Lambda`，并且不是`Compose`函数，则直接返回
        if (isNonComposableInlinedLambda()) return this

        // 不处理expect函数
        if (isExpect) return this

        // 缓存转换的结果
        return transformedFunctions[this] ?: copyWithComposerParam()
    }
```



如上所示，主要就是判断一下函数是否有 `@Composeble` 注解，如果没有则直接返回不再处理，有则继续处理并缓存结果，后续调用`copyWithComposerParam`方法

```kotlin
    private fun IrFunction.copyWithComposerParam(): IrSimpleFunction {
      //...

        return copy().also { fn ->
            // $composer
            val composerParam = fn.addValueParameter {
                name = KtxNameConventions.COMPOSER_PARAMETER
                type = composerType.makeNullable()
                origin = IrDeclarationOrigin.DEFINED
                isAssignable = true
            }

           //...
        }
    }
```



如上所示，在所有 `Composeble` 函数中插入了一个 `$composer`，这有效地使 `composer` 可用于任何子树，提供实现 `Composable` 树并保持更新所需的所有信息。

![](/home/woo/document/图片/image_3-ceeb888819e3e9d7a363ac32a302398b.webp)

### 添加 `$changed`

我们知道 `Compose` 存在智能重组机制，当输入完全相同时允许跳过重组，而编译器除了 `$composer`，还会注入 `$changed` 参数。 此参数用于提供有关当前 `Composable` 的输入参数与一次发生组件后是否相同，如果相同则允许跳过重组。

```kotlin
    private fun IrFunction.copyWithComposerParam(): IrSimpleFunction {
      //...

        return copy().also { fn ->
            // $changed[n]
            val changed = KtxNameConventions.CHANGED_PARAMETER.identifier
            //changedparamCount,计算$changed数量
            for (i in 0 until changedParamCount(realParams, fn.thisParamCount)) {
                fn.addValueParameter(
                    if (i == 0) changed else "$changed$i",
                    context.irBuiltIns.intType
                )
            }            

           //...
        }
    }
```



如上所示，添加的是个 `$changed[n]`,这个 `n` 是从何而来呢？这是因为每个参数的状态有5种情况,`compose` 中定义了一个枚举，如下所示：

```kotlin
enum class ParamState(val bits: Int) {
    Uncertain(0b000),
    Same(0b001),
    Different(0b010),
    Static(0b011),
    Unknown(0b100),
    Mask(0b111);
}
```



如上所示，`$changed` 通过位运算的方式来表示参数是否发生变化：

1. `$changed` 是 `Int` 类型，一个占32位
2. 每个参数有5种类型，因此一个参数需要3位来表示
3. 因此一个 `$changed` 可以表示10个参数是否发生变化，如果超出则需要再添加一个 `$changed` 参数

编译器注入`$changed`之后效果如下所示：

```kotlin
    @Composable
    fun A(x: Int, $composer: Composer<*>, $changed: Int) {
        var $dirty = $changed
        if ($changed and 0b0110 === 0) {
            $dirty = $dirty or if ($composer.changed(x)) 0b0010 else 0b0100
        }
        if (%dirty and 0b1011 !== 0b1010 || !$composer.skipping) {
            f(x)
        } else {
            $composer.skipToGroupEnd()
        }
    }
```



### 添加`$default`

```
Kotlin` 支持的默认参数不适用于可组合函数的参数，因为可组合函数需要在函数的作用域（生成的组）内为其参数执行默认表达式。 为此，`Compose` 提供了默认参数解析机制的替代实现。即在 `Compose` 方法中添加 `$defaulut
    private fun IrFunction.copyWithComposerParam(): IrSimpleFunction {
    //...

       // $default[n]
       if (oldFn.requiresDefaultParameter()) {
           val defaults = KtxNameConventions.DEFAULT_PARAMETER.identifier
           for (i in 0 until defaultParamCount(realParams)) {
               fn.addValueParameter(
                   if (i == 0) defaults else "$defaults$i",
                   context.irBuiltIns.intType,
                   IrDeclarationOrigin.MASK_FOR_DEFAULT_FUNCTION
               )
           }
       }            

      //...
   }
```



`$default` 与 `$changed` 类似，也是通过位运算来表示参数状态的，不过 `$default` 比较简单，只有两种状态，使用还是不使用默认值，因此一个 `$changed` 参数可表示31个参数是否使用默认值，如果超出再添加一个`$changed`
编译器注入`$default`后的效果如下所示：

```kotlin
    @Composable
    fun A(x: Int, $default: Int) {
        val x = if ($default and 0b1 != 0) 0 else x
        f(x)
    }
```



## 总结

本文主要简单介绍了什么是 `KCP` 及 `KCP` 是如何处理 `@Composeble` 注解的，从中可以看到 `KCP` 的强大与复杂，如果你只需要解析注解生成代码的话，可以使用 `KSP` 取代 `KAPT`，如果有更多需求，可以尝试使用 `KCP`
同时也可以看到 `Compose` 设计的巧妙，将框架背后的复杂度完全隐藏，背后做了这么多工作，使用都却只需添加一个 `@Composeble` 注解，就能将一个普通的函数变成 `Composeble` 函数，的确是挺简洁优雅的，感兴趣的同学也可以直接查看源码~