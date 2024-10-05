- [x]  **Jetpack Compose多线程执行是如何实现的？**

- [x]  **DisposableEffect、SideEffect、LaunchedEffect之间的区别？**


  

# Effect(生命周期)

在 Jetpack Compose 中，没有像传统 Android 中的生命周期函数那样的概念。

相反，Compose 依赖于函数式编程范式，它通过函数调用和状态变化来管理 UI 的渲染和更新。

Compose 中最重要的概念是 Composable 函数，这些函数负责描述 UI 的外观和行为，它们在需要时被调用来重新构建 UI。

尽管没有像传统 Android 中那样的生命周期函数，但您可以通过使用 Jetpack Compose 中提供的一些特定函数来模拟一些生命周期事件。

这些函数包括：

## DisposableEffect

当 composable 进入树时，执行一个效果，并在 composable 从树中移除时清理资源。类似于 `onDestroy()`。

```
DisposableEffect(Unit) {
    // 初始化操作，类似于 onCreate()
    
    onDispose {
        // 清理资源，类似于 onDestroy()
    }
}
```

## LaunchedEffect

这个和上面2个作用就不太一样了

用于启动一个协程来执行特定的操作，是在Compose组件被第一次创建时开始，并在Compose组件的生命周期中自动取消该协程。

`LaunchedEffect`的执行是异步的。

这个**Effect主要的作用主要是在Compose中启动一个协程** 而且具有2个特点

1. **在重组过程完成以后 才会启动协程**
2. **key 发生变化的时候 也会启动协程**

组件创建时调用

```
LaunchedEffect(Unit) {
}
```

监听值变化进行调用

```
LaunchedEffect(appViewModel.selectGroupId.value) {
    Log.i(TAG, "刷新")
}
```

对比

`DisposableEffect` 与 `LaunchedEffect` 在组件创建时确实都会执行，但它们的主要目的和用法不同。

- `DisposableEffect` 主要用于在组件被销毁时执行清理操作。

  当组件被创建时，`DisposableEffect` 中的代码会执行，但其主要功能是确保在组件被销毁时执行清理工作，比如取消异步操作或释放资源。

- `LaunchedEffect` 主要用于在组件生命周期内启动异步任务。虽然它也会在组件创建时执行，但其主要目的是为了启动一个异步任务，这个任务可能会在组件生命周期内的其他时刻完成。

因此，虽然两者都会在组件创建时执行，但它们的作用和用法是不同的。

## SideEffect

在 composable 的每次重新组合时都会运行的效果。

**SideEffect 只会在重组结束之后 被执行**

```
SideEffect {
    // 每次重新组合时都会运行
}
```

注意

> 这个只是类似于 `onResume()` ，并不是在`onResume`生命周期执行。

## 示例

这些函数可以用于执行各种操作，以模拟传统 Android 生命周期中的行为。

但是请注意，Compose 的方式更加灵活和函数式，因此可能需要调整您的思维方式来适应这种新的 UI 构建模式。

```
@Composable
fun CounterView() {
    var count by remember { mutableStateOf(0) }
    DisposableEffect(Unit) {
        // 初始化操作，类似于 onCreate()
        Log.i("Effect","DisposableEffect-Create")
        onDispose {
            // 清理资源，类似于 onDestroy()
            Log.i("Effect","SideEffect-Dispose")
        }
    }
    LaunchedEffect(Unit) {
        // 初始化操作，类似于 onCreate()
        Log.i("Effect","LaunchedEffect")
    }
    SideEffect {
        Log.i("Effect","SideEffect")
    }
    Column(
        modifier = Modifier.padding(16.dp),
        horizontalAlignment = Alignment.CenterHorizontally
    ) {
        Text("数量: $count")
        Spacer(modifier = Modifier.height(16.dp))
        Button(onClick = { count++ }) {
            Text("计数器")
        }
    }
}
```

组件加载实际掉用的顺序

```
DisposableEffect-Create` => `SideEffect` => `LaunchedEffect`=>`DisposableEffect-onDispose
```

# Kotlin 协程

Kotlin 协程是 Kotlin 标准库中的一个功能强大且流行的特性，用于简化异步编程。它允许开发者以顺序化的方式编写异步代码，而无需手动管理线程。

在 Android 开发中，Kotlin 协程与 Jetpack Compose 结合使用可以更轻松地处理异步操作，并且使 UI 代码更加清晰和易于维护。

1. **协程作用域 (Coroutine Scope)**：用于定义协程的生命周期和作用范围。
2. **协程构建器 (Coroutine Builders)**：例如 `launch`、`async`、`runBlocking` 等，用于启动新的协程或创建异步任务。
3. **挂起函数 (Suspending Functions)**：可以被暂停并在后续恢复执行的函数，使用 `suspend` 关键字声明。
4. **调度器 (Dispatcher)**：用于指定协程执行的线程或线程池，包括 `Dispatchers.Default`、`Dispatchers.IO` 和 `Dispatchers.Main` 等。
5. **协程上下文 (Coroutine Context)**：包含协程的各种配置信息，例如调度器、异常处理器等。
6. **协程作用域中的取消 (Cancellation)**：通过取消协程作用域来取消所有相关的协程，防止内存泄漏和资源浪费。

# Jetpack Compose中使用协程

在 Jetpack Compose 中，您可以使用 Kotlin 协程来处理异步任务，例如从网络请求数据、执行数据库操作等。

**定义协程作用域**：

在 Composable 函数中创建一个协程作用域，以确保协程在正确的生命周期范围内执行。

```
@Composable
fun MyComposable() {
    val coroutineScope = rememberCoroutineScope()

    // 在协程作用域中启动异步任务
    coroutineScope.launch {
        // 执行异步操作，例如网络请求或数据库查询
    }
}
```

创建协程作用域有两种方式

```
val coroutineScope = rememberCoroutineScope()
```

或者

```
val coroutineScope = CoroutineScope(Dispatchers.Main)
```

设置参数指定线程

```
coroutineScope.launch(Dispatchers.IO) {
    
}
```

**使用协程构建器**：

在协程作用域中使用协程构建器（例如 `launch` 或 `async`）启动异步任务。

```
val coroutineScope = rememberCoroutineScope()

coroutineScope.launch {
    // 在后台线程执行耗时操作
    val result = withContext(Dispatchers.IO) {
        // 执行耗时操作，例如网络请求或数据库查询
        // 等待3秒钟
        Log.i("Thread", "请求数据 Current thread: ${Thread.currentThread().name}")
        delay(3000L)
        "我是返回的数据"
    }

    // 在主线程更新 UI
    Log.i("Thread", "Data: ${result} Current thread: ${Thread.currentThread().name}")
}
```

**处理协程中的异常**：

使用 `try-catch` 块或 `CoroutineExceptionHandler` 来处理协程中可能出现的异常。

```
coroutineScope.launch {
    try {
        // 执行可能抛出异常的代码
    } catch (e: Exception) {
        // 处理异常
    }
}
```

**取消协程**：

在 Composable 组件的生命周期结束时，取消相关的协程以释放资源并避免内存泄漏。

```
@Composable
fun MyComposable() {
    val coroutineScope = rememberCoroutineScope()

    DisposableEffect(Unit) {
        onDispose {
            // 取消协程作用域中的所有协程
            coroutineScope.cancel()
        }
    }

    // 在协程作用域中启动异步任务
    coroutineScope.launch {
        // 执行异步操作
    }
}
```

通过以上步骤，您可以在 Jetpack Compose 中有效地利用 Kotlin 协程来管理异步任务，提高代码的可读性和可维护性。

同时，确保适当处理取消操作和异常，以确保应用程序的稳定性和性能。

# 协程作用域

协程作用域的作用是提供一种机制，用于控制变量的可见性和生命周期，从而帮助开发者更好地管理和组织代码。

`CoroutineScope(Dispatchers.Main)` 和 `rememberCoroutineScope()`的区别：

`CoroutineScope(Dispatchers.Main)` 和 `rememberCoroutineScope()` 都是用于创建协程作用域的方法

## 全局作用域

```
GlobalScope.launch(Dispatchers.IO){
}
```

## 自定义作用域

```
CoroutineScope(Dispatchers.Main).launch {
    Toast.makeText(context, message, Toast.LENGTH_SHORT).show()
}
```

或者

```
CoroutineScope(Dispatchers.IO).launch {
    Toast.makeText(context, message, Toast.LENGTH_SHORT).show()
}
```

**CoroutineScope(Dispatchers.Main)**：

- 这是一个函数调用，用于创建一个新的协程作用域，并指定其所在的调度器为主线程（Dispatchers.Main）。
- 每次调用 `CoroutineScope(Dispatchers.Main)` 都会创建一个新的协程作用域对象，这意味着它可能在每次调用时创建新的作用域，而不考虑之前是否已存在作用域。
- 如果在 Composable 函数中的多个地方需要使用相同的协程作用域，可能会导致创建多个不必要的作用域对象，从而增加了资源消耗和管理复杂度。

## Composable作用域

**rememberCoroutineScope()**：

- 这是一个 Composable 函数，用于在 Composable 中创建一个记住的（remembered）协程作用域。

- `rememberCoroutineScope()` 会创建一个协程作用域对象，并将其与当前 Composable 的生命周期相关联。

  这意味着，当 Composable 重新组合（recompose）时，它会保留相同的协程作用域对象，而不是每次重新组合都创建一个新的。

- 因此，使用 `rememberCoroutineScope()` 可以确保在同一个 Composable 函数中共享相同的协程作用域，而不会导致额外的对象创建和资源浪费。

总的来说，`CoroutineScope(Dispatchers.Main)` 适用于那些不需要记住作用域对象的简单情况，而 `rememberCoroutineScope()` 则更适合于需要在 Composable 中共享和记住作用域对象的情况。

## ViewModel作用域

`ViewModel`中的`viewModelScope`默认也在主线程，我们可以设置参数指定线程。

```
viewModelScope.launch(Dispatchers.IO) {
    
}
```

# 协程绑定页面生命周期

在 Android 中，您可以在页面销毁后取消协程作用域以关闭它。

通常，在 Activity 或 Fragment 的生命周期方法中执行此操作是一个不错的选择，例如在 `onDestroy()` 方法中。

以下是一个示例：

```
import kotlinx.coroutines.CoroutineScope
import kotlinx.coroutines.Dispatchers
import kotlinx.coroutines.cancel // 这是你需要导入的取消函数

class YourActivity : AppCompatActivity() {

    // 在页面中定义协程作用域
    private val coroutineScope = CoroutineScope(Dispatchers.IO)

    override fun onDestroy() {
        super.onDestroy()

        // 取消协程作用域
        coroutineScope.cancel()
    }
}
```

上面的示例是在 Activity 中取消协程作用域的一个简单方法。

在 Fragment 中，您可以在 `onDestroyView()` 方法中执行相同的操作。

确保在适当的时候取消协程以避免可能导致内存泄漏或意外行为的问题。