### 谈谈你对 SPA 单页面模式的理解，优缺点 ？

SPA（ single-page application ）仅在 Web 页面初始化时加载相应的 HTML、JavaScript 和 CSS

- 典型使用 SPA 模式的前端框架有：React、Angular、Vue
- 一旦页面加载完成，SPA 不会因为用户的操作而进行页面的重新加载或跳转；取而代之的是利用路由机制实现 HTML 内容的变换，UI 与用户的交互，避免页面的重新加载。

**优点：**

- 用户体验好、快，内容的改变不需要重新加载整个页面，避免了不必要的跳转和重复渲染；
- 基于上面一点，SPA 相对对服务器压力小；
- 前后端职责分离，架构清晰，前端进行交互逻辑，后端负责数据处理；

**缺点：**

- 初次加载耗时多：为实现单页 Web 应用功能及显示效果，需要在加载页面的时候将 JavaScript、CSS 统一加载，部分页面按需加载；
- 前进后退路由管理：由于单页应用在一个页面中显示所有的内容，所以不能使用浏览器的前进后退功能，所有的页面切换需要自己建立堆栈管理；
- SEO 难度较大：由于所有的内容都在一个页面中动态替换显示，所以在 SEO 上其有着天然的弱势。

### mvc和mvvm理解

mvc：

MVC 即 Model View Controller，简单来说就是通过 controller 的控制去操作 model 层的数据，并且返回给 view 层展示。

- View 接受用户交互请求
- View 将请求转交给Controller处理
- Controller 操作Model进行数据更新保存
- 数据更新保存之后，Model会通知View更新
- View 更新变化数据使用户得到反馈

MVVM：

MVVM 即 Model-View-ViewModel，Model 层代表数据模型，View 代表 UI 组件，ViewModel 是 View 和 Model 层的桥梁，数据会绑定到 viewModel 层并自动将数据渲染到页面中，视图变化的时候会通知 viewModel 层更新数据。。MVVM的优点是低耦合、可重用性、独立开发。

- View 接收用户交互请求
- View 将请求转交给 ViewModel
- ViewModel 操作 Model 数据更新
- Model 更新完数据，通知 ViewModel 数据发生变化
- ViewModel 更新 View 数据

MVVM模式和MVC有些类似，但有以下不同：

- ViewModel 替换了 Controller，在UI层之下
- ViewModel 向 View 暴露它所需要的数据和指令对象
- ViewModel 接收来自 Model 的数据

概括起来，MVVM 是由 MVC 发展而来，通过在 Model 之上而在 View 之下增加一个非视觉的组件将来自 Model 的数据映射到 View中。

![mvvm](https://cchroot.github.io/interview/assets/img/vue_3.24fb4ce8.png)

**MVVM 解决了什么问题 ？**

三个痛点问题：

- 开发者在代码中大量调用相同的 DOM API，处理繁琐 ，操作冗余，使得代码难以维护
- 大量的 DOM 操作使页面渲染性能降低，加载速度变慢，影响用户体验。
- 当 Model 频繁发生变化，开发者需要主动更新到 View ；当用户的操作导致 Model 发生变化，开发者同样需要将变化的数据同步到 Model 中，这样的工作不仅繁琐，而且很难维护复杂多变的数据状态。

其实，早期 jQuery 的出现就是为了前端能更简洁的操作 DOM 而设计的，但它只解决了第一个问题，另外两个问题始终伴随着前端一直存在 ! MVVM 的出现，完美解决了以上三个问题 。

### 生命周期函数

- beforeCreate(创建前) vue 实例的挂载元素 $el 和数据对象 data都是undefined, 还未初始化
- created(创建后) 完成了 data 数据初始化, el 还未初始化
- beforeMount(载入前) vue实例的$el和data都初始化了, 相关的render函数首次被调用
- mounted(载入后) 此过程中进行 ajax 交互
- beforeUpdate(更新前)
- updated(更新后)
- beforeDestroy(销毁前)
- destroyed(销毁后)

**父子组件生命周期关系**

- 创建： 父 created --> 子 created --> 子 mounted --> 父 mounted
- 更新： 父 beforeUpdate --> 子 beforeUpdate --> 子 updated --> 父 updated
- 销毁： 父 beforeDestroy --> 子 beforeDestroy --> 子 destroyed --> 父 destroyed

### Vue 组件中 data 为什么必须是一个函数 ？

复用组件的时候，都会返回一份新的 data，相当于每个组件实例都有自己私有的数据空间，不会共享同一个 data 对象。

因为组件是可以复用的，JS 里对象是引用关系,如果组件 data 是一个对象,那么子组件中的 data 属性值会互相污染,产生副作用。

所以一个组件的 data 选项必须是一个函数,因此每个实例可以维护一份被返回对象的独立的拷贝。new Vue 的实例是不会被复用的,因此不存在以上问题。

### vue给对象新增属性页面没有响应

由于 Vue 会在初始化实例时对属性执行 getter/setter 转化，所以属性必须在 data 对象上存在才能让 Vue 将它转换为响应式的。Vue 提供了 $set 方法用来触发视图更新。

### v-if和v-show区别

- v-if 在编译过程中会被转化成三元表达式，条件不满足时不渲染此节点。表达式通过的情况下将元素渲染到 DOM 中。
- v-show 会被编译成指令，条件不满足时控制样式将对应节点隐藏 （display:none）。条件满足时基于 CSS display 属性来显示元素
- v-if 支持 v-else 和 v-else-if 指令，而 v-show 不支持 else 指令。
- v-if 有更高的切换开销，而 v-show 有更高的初始化渲染开销
- v-if 支持 选项卡，但 v-show 不支持

使用场景

- v-if 适用于在运行时很少改变条件，不需要频繁切换条件的场景
- v-show 适用于需要非常频繁切换条件的场景

### v-if 与 v-for 为什么不建议一起使用 ？

v-for 和 v-if 不要在同一个标签中使用，因为解析时先解析 v-for 再解析 v-if（v-for 指令比 v-if 优先级高）。

如果遇到需要同时使用时可以考虑写成计算属性的方式。（有两种情况下会尝试这种组合）如下：

（1）过滤列表的项

```vue
<!--  例如，如果你尝试使用 v-if 标记过滤列表 -->
<ul>
    <li
        v-for="user in users"
        v-if="user.isActive"
        :key="user.id"
    >
        {{ user.name }}
    <li>
</ul>


<!-- 这可以使用在初始列表上使用计算属性预过滤来避免。 -->
computed: { 
	activeUsers: function () { 
		return this.users.filter(function (user){
			return user.isActive 
		})
	 }
}
<ul>
    <li
        v-for="user in activeUsers"
        :key="user.id"
    >
        {{ user.name }}
    <li>
</ul>
```

（2）避免渲染应该被隐藏的列表

```vue
<!-- 例如，你想根据条件检查用户是否显示还是隐藏 -->
<ul>
    <li
        v-for="user in users"
        v-if="shouldShowUsers"
        :key="user.id"
    >
        {{ user.name }}
    <li>
</ul>

<!-- 可以移动条件到父级避免对每一个用户检查 -->
<ul v-if="shouldShowUsers">
    <li
        v-for="user in users"
        :key="user.id"
    >
        {{ user.name }}
    <li>
</ul>
```

### scoped属性作用

在 Vue 文件中的 style 标签上有一个特殊的属性，scoped。当一个 style 标签拥有 scoped 属性时候，它的 css 样式只能用于当前的Vue组件，可以使组件的样式不相互污染。如果一个项目的所有 style 标签都加上了scoped属性，相当于实现了样式的模块化。

scoped 属性的实现原理是给每一个dom元素添加了一个独一无二的动态属性，给 css 选择器额外添加一个对应的属性选择器，来选择组件中的 dom。

```vue
<template>
    <div class="box">dom</div>
</template>
<style lang="scss" scoped>
.box{
    background:red;
}
</style>
```

vue将代码转译成如下：

```vue
.box[data-v-11c6864c]{
    background:red;
}
<template>
    <div class="box" data-v-11c6864c>dom</div>
</template>
```

### scoped样式穿透

scoped 虽然避免了组件间样式污染，但是很多时候我们需要修改组件中的某个样式，但是又不想去除 scoped 属性。

1. 使用 `/deep/`
2. 使用两个 style 标签

### ref的作用

1. 获取 dom 元素： `this.$refs.box`
2. 获取子组件中的 data： `this.$refs.box.msg`
3. 调用子组件中的方法： `this.$refs.box.open()

### 组件之间的传值通信

1. 父组件给子组件传值通过 props
2. 子组件给父组件传值通过 $emit 触发回调
3. 兄弟组件通信，通过实例一个 vue 实例 eventBus 作为媒介，要相互通信的兄弟组件之中，都引入 eventBus

```javascript
//main.js
import Vue from 'vue'
export const eventBus = new Vue()

//brother1.vue
import eventBus from '@/main.js'
export default{
	methods: {
	    toBus () {
	        eventBus.$emit('greet', 'hi brother')
	    }
	}
}

//brother2
import eventBus from '@/main.js'
export default{
    mounted(){
        eventBus.$on('greet', (msg)=>{
            this.msg = msg
        })
    }
}
```

更多请看 [vue组件间通信八种方式(opens new window)](https://cchroot.github.io/2020/04/12/vue组件间通信八种方式/)

### Vue 组件间通信有哪几种方式？

（1）props / $emit 适用 父子组件通信

这种方法是 Vue 组件的基础，相信大部分同学耳闻能详，所以此处就不举例展开介绍。

（2）ref 与 $parent / $children 适用 父子组件通信

ref：如果在普通的 DOM 元素上使用，引用指向的就是 DOM 元素；如果用在子组件上，引用就指向组件实例 $parent / $children：访问父 / 子实例 （3）EventBus （$emit / $on） 适用于 父子、隔代、兄弟组件通信

这种方法通过一个空的 Vue 实例作为中央事件总线（事件中心），用它来触发事件和监听事件，从而实现任何组件间的通信，包括父子、隔代、兄弟组件。

（4）$attrs/$listeners 适用于 隔代组件通信

$attrs：包含了父作用域中不被 prop 所识别 (且获取) 的特性绑定 ( class 和 style 除外 )。当一个组件没有声明任何 prop 时，这里会包含所有父作用域的绑定 ( class 和 style 除外 )，并且可以通过 v-bind="$attrs" 传入内部组件。通常配合 inheritAttrs 选项一起使用。 $listeners：包含了父作用域中的 (不含 .native 修饰器的) v-on 事件监听器。它可以通过 v-on="$listeners" 传入内部组件 

（5）provide / inject 适用于 隔代组件通信

祖先组件中通过 provider 来提供变量，然后在子孙组件中通过 inject 来注入变量。 provide / inject API 主要解决了跨级组件间的通信问题，不过它的使用场景，主要是子组件获取上级组件的状态，跨级组件间建立了一种主动提供与依赖注入的关系。

（6）Vuex 适用于 父子、隔代、兄弟组件通信

Vuex 是一个专为 Vue.js 应用程序开发的状态管理模式。每一个 Vuex 应用的核心就是 store（仓库）。“store” 基本上就是一个容器，它包含着你的应用中大部分的状态 ( state )。

### computed 和 watch 有什么区别及运用场景?

**区别**:

computed 计算属性 : 依赖其它属性值,并且 computed 的值有缓存,只有它依赖的属性值发生改变,下一次获取 computed 的值时才会重新计算 computed 的值。

watch 侦听器 : 更多的是「观察」的作用,**无缓存性**,类似于某些数据的监听回调,每当监听的数据变化时都会执行回调进行后续操作。

**运用场景**:

当我们需要进行数值计算,并且依赖于其它数据时,应该使用 computed,因为可以利用 computed 的缓存特性,避免每次获取值时,都要重新计算。

当我们需要在数据变化时执行异步或开销较大的操作时,应该使用 watch,使用 watch 选项允许我们执行异步操作 ( 访问一个 API ),限制我们执行该操作的频率,并在我们得到最终结果前,设置中间状态。这些都是计算属性无法做到的。

### Vue 和 React 中的 key 到底有什么用？

key 是给每一个 vnode 的唯一 id,依靠 key 可以跟踪每个节点的特征，从而重用和重排存在的元素，让我们的 diff 操作可以更准确、更快速 (对于简单列表页渲染来说 diff 节点也更快,但会产生一些隐藏的副作用,比如可能不会产生过渡效果,或者在某些节点有绑定数据（表单）状态，会出现状态错位。)

diff 算法的过程中,先会进行新旧节点的首尾交叉对比,当无法匹配的时候会用新节点的 key 与旧节点进行比对,从而找到相应旧节点。

更准确 : 因为带 key 就不是就地复用了,在 sameNode 函数 a.key === b.key 对比中可以避免就地复用的情况。所以会更加准确,如果不加 key,会导致之前节点的状态被保留下来,会产生一系列的 bug。

更快速 : key 的唯一性可以被 Map 数据结构充分利用,相比于遍历查找的时间复杂度 O(n),Map 的时间复杂度仅仅为 O(1),源码如下:

```js
function createKeyToOldIdx(children, beginIdx, endIdx) {
  let i, key;
  const map = {};
  for (i = beginIdx; i <= endIdx; ++i) {
    key = children[i].key;
    if (isDef(key)) map[key] = i;
  }
  return map;
}
```

因此，只要有可能，总是建议给 v-for 提供一个 key 值，除非迭代的 DOM 内容很简单。

注：**不应该使用非基本类型的值，如对象和数组作为 v-for 的 key，使用 String 或 Number 类型替代。**

### Vue 中 key 值为什么不能用索引

Vue中，使用key属性是为了在Vue的虚拟DOM diff算法中，识别出每个节点的唯一性，以便更高效地更新和重用DOM元素。

当使用v-for指令渲染列表时，Vue会生成一组相同类型的元素。如果没有提供key属性，Vue会使用默认的索引作为key，即列表中每个元素的索引值。然而，将索引作为key存在一些问题：

- 重新排序：当列表中的元素发生重新排序时，索引作为key可能会导致意外的结果。因为Vue的diff算法会基于key来判断哪些元素被修改、删除或添加，如果只是简单地使用索引作为key，则无法正确地识别出元素的变化，可能会导致错误的更新。
- 添加/删除元素：当在列表中添加或删除元素时，索引作为key也会导致问题。因为索引是根据元素在列表中的位置来确定的，当添加或删除元素时，索引会发生变化，可能会导致错误的更新或重渲染。

为了避免这些问题，建议使用具有唯一标识的属性作为key，而不是简单的索引。这样可以确保每个元素都有一个唯一的key，即使列表的顺序发生变化或有元素被添加/删除，Vue也能够正确地识别出元素的变化，从而实现更准确和高效的更新。

使用具有唯一标识的属性作为key，可以是一个唯一的ID、全局唯一的标识符或具有唯一性的属性值。这样可以确保在列表中的每个元素都有一个唯一且稳定的key，从而避免潜在的问题。

### $route 和 $router 有什么区别 ？

- $route 是路由信息对象，包括 path，params，hash，query，fullPath，matched，name 等路由信息参数。
- 而$router 是路由实例对象，包括了路由的跳转方法，钩子函数等。\

### vue 组件延迟加载的原理是什么

Vue组件的延迟加载是通过异步组件和Webpack的代码分割功能实现的。

在Vue中，可以使用import()语法来定义异步组件。这样的组件在需要时才会被加载，而不是在应用初始化时就加载所有组件。Webpack会将异步组件的代码分割成独立的文件，并在需要时按需加载。

具体的延迟加载原理如下：

- 定义异步组件：通过使用import()语法定义异步组件。例如： `const AsyncComponent = () => import('./AsyncComponent.vue');`
- Webpack代码分割：Webpack会将异步组件的代码分割成独立的文件。每个异步组件都会生成一个单独的文件，这些文件会在应用运行时按需加载。
- 动态加载组件：当需要使用异步组件时，Vue会动态加载对应的组件文件。这发生在组件被渲染到视图中时，或者通过组件缓存的情况下。
- 异步组件的加载状态：在异步组件加载完成之前，Vue可以渲染一个占位符组件，例如显示一个加载动画。一旦异步组件加载完成，Vue会将其替换为真正的组件。

通过延迟加载组件，可以减少初始加载时的文件体积，提高应用的性能。只有在需要使用某个组件时才会加载对应的代码，避免了不必要的网络请求和资源加载。

### VueJS 与 ReactJS 相比有什么优势 ？

与 React 相比，Vue 具有以下优势

- Vue 更小且更快
- 方便的模板简化了开发过程
- 相比学习 JSX 它有更简单的 JavaScript 语法
- 深受国内企业和开发者喜爱

### ReactJS 与 VueJS 相比有什么优势 ？

与 Vue 相比，React 具有以下优势

- ReactJS 在大型应用程序开发中提供了更大的灵活性
- 易于测试
- 非常适合创建移动应用程序
- 生态系统规模大，成熟度高