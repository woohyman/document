### 介绍一下 Vue 的内部运行机制

Vue的内部运行机制主要包括以下几个方面：

1. 模板编译：Vue使用模板来描述组件的结构和渲染逻辑。在Vue的内部，模板会被编译成渲染函数。编译过程将模板解析成抽象语法树（AST），然后将AST转换为渲染函数。渲染函数是一个JavaScript函数，它返回虚拟DOM（Virtual DOM）。
2. 响应式系统：Vue的响应式系统是实现数据驱动视图更新的核心机制。当Vue应用初始化时，Vue会遍历组件的data对象中的属性，并使用Object.defineProperty方法将这些属性转换为getter和setter。这样一来，当这些属性被读取或修改时，Vue能够捕获到并触发相应的更新操作。
3. 虚拟DOM和Diff算法：Vue使用虚拟DOM来提高渲染性能。在每次组件更新时，Vue会生成新的虚拟DOM树，并与之前的虚拟DOM树进行对比。这个对比过程使用Diff算法来找出两个虚拟DOM树之间的差异。然后，Vue将这些差异应用到实际的DOM上，实现最小化的DOM操作，从而提高性能。
4. 组件生命周期：Vue组件具有生命周期钩子函数，它们在组件的不同阶段被调用。例如，beforeCreate、created、beforeMount、mounted等。这些钩子函数允许开发者在组件的不同生命周期阶段执行相应的逻辑，例如初始化数据、发送网络请求、订阅事件等。
5. 指令和组件：Vue提供了一系列内置指令（例如v-if、v-for、v-bind等）和组件（例如Vue Router、Vuex等），用于扩展Vue的功能。指令允许开发者在模板中添加特定的行为和逻辑，而组件可以将应用拆分为可重用和独立的部分。

总体而言，Vue的内部运行机制通过模板编译、响应式系统、虚拟DOM和Diff算法等核心机制，实现了数据驱动的视图更新，提供了灵活和高效的开发体验。

### 说说 Vue 的渲染过程

![](/home/woo/document/图片/vue_2.46637dce.png)

1. 解析模板：Vue 在渲染过程中首先会解析 Vue 组件的模板。这个过程会将模板解析为抽象语法树（AST），用于后续的编译和渲染过程。
2. 编译模板：在编译阶段，Vue 将抽象语法树（AST）转换为渲染函数。渲染函数是一个 JavaScript 函数，用于描述组件的渲染逻辑。编译过程还会对模板中的指令、表达式等进行静态分析和优化。
3. 创建虚拟 DOM：在组件实例化时，Vue 会根据渲染函数创建一个虚拟 DOM（Virtual DOM）树。虚拟 DOM 是一个轻量级的 JavaScript 对象树，它描述了组件的当前状态和结构。
4. 更新虚拟 DOM：当组件的状态发生变化时，Vue 会触发更新过程。在更新过程中，Vue 会比较前后两个虚拟 DOM 树的差异，找出需要更新的部分。
5. 生成补丁：根据虚拟 DOM 树的差异，Vue 会生成一组补丁（Patch），用于描述如何将前后两个虚拟 DOM 树同步。补丁包含了新增、删除和修改节点等操作。
6. 应用补丁：在应用补丁阶段，Vue 会将生成的补丁应用到虚拟 DOM 树上，通过更新虚拟 DOM 树来反映组件状态的变化。
7. 渲染到真实 DOM：在完成虚拟 DOM 的更新后，Vue 会将最终的虚拟 DOM 树渲染到真实的 DOM 上。这个过程会将变化的部分更新到真实 DOM 上，以保持页面的同步。
8. 响应用户交互：一旦组件渲染到真实 DOM 上，Vue 会监听用户的交互事件，如点击、输入等。当用户触发事件时，Vue 会根据事件处理函数的逻辑进行相应的响应。
9. 组件销毁：当组件不再需要渲染或被销毁时，Vue 会执行组件的销毁过程。在销毁过程中，Vue 会清理组件的相关资源、解绑事件监听器等，以确保内存的释放和性能的优化。

### 能简单说下vue的响应式原理么

![vue_1.593d9d8d](/home/woo/document/图片/vue_1.593d9d8d.png)

**核心实现类**:

Observer : 它的作用是给对象的属性添加 getter 和 setter，用于依赖收集和派发更新

Dep : 用于收集当前响应式对象的依赖关系,每个响应式对象包括子对象都拥有一个 Dep 实例（里面 subs 是 Watcher 实例数组）,当数据有变更时,会通过 dep.notify()通知各个 watcher。

Watcher : 观察者对象, 实例分为渲染 watcher (render watcher),计算属性 watcher (computed watcher),侦听器 watcher（user watcher）三种

**Watcher 和 Dep 的关系**:

watcher 中实例化了 dep 并向 dep.subs 中添加了订阅者,dep 通过 notify 遍历了 dep.subs 通知每个 watcher 更新。

**依赖收集**

1. initState 时,对 computed 属性初始化时,触发 computed watcher 依赖收集
2. initState 时,对侦听属性初始化时,触发 user watcher 依赖收集
3. render() 的过程,触发 render watcher 依赖收集
4. re-render 时,vm.render()再次执行,会移除所有 subs 中的 watcer 的订阅,重新赋值。

**派发更新**

1. 组件中对响应的数据进行了修改,触发 setter 的逻辑
2. 调用 dep.notify()
3. 遍历所有的 subs（Watcher 实例）,调用每一个 watcher 的 update 方法。

**原理**

当创建 Vue 实例时,vue 会遍历 data 选项的属性,利用 Object.defineProperty 为属性添加 getter 和 setter 对数据的读取进行劫持（getter 用来依赖收集,setter 用来派发更新）,并且在内部追踪依赖,在属性被访问和修改时通知变化。

每个组件实例会有相应的 watcher 实例,会在组件渲染的过程中记录依赖的所有数据属性（进行依赖收集,还有 computed watcher,user watcher 实例）,之后依赖项被改动时,setter 方法会通知依赖与此 data 的 watcher 实例重新计算（派发更新）,从而使它关联的组件重新渲染。

一句话总结:

vue.js 采用数据劫持结合发布-订阅模式,通过 Object.defineproperty 来劫持各个属性的 setter,getter,在数据变动时发布消息给订阅者,触发响应的监听回调

**更加详细的说明**

![vue_reactive.c9e2ac37](/home/woo/document/图片/vue_reactive.c9e2ac37.png)

vue 的响应式原理核心就是去观测数据的变化，当我们这些数据发生变化之后，通知我们对应的观察者来实行相关的逻辑。整个响应式最核心的就是 Dep 的实现，它实际上是连接数据和数据对应观察者的桥梁。

1. 在 vue 的初始化阶段，会对 vue 上定义的数据分别做一些不同的处理
2. 对于 data 和 props 会通过 observe 或 defineReactive 做一系列的操作，把 props 和 data 定义的对象和每一个属性都变成响应式的，同时它们内部会持有一个 Dep 的实例，当我们访问到这些属性的时候，就会触发 Dep 的 depend() 方法来收集依赖。收集的依赖是当前正在进行计算的 watcher (Dep.target)，这些依赖就会做为当前的订阅者，来订阅这些数据的变化，然后当我们去修改这些数据的时候，就会通过调用 Dep.notify() 方法来通知我们这些订阅者去做 updata 的逻辑
3. 对于 computed 属性而言，它实际上内部会创建一个 computed watcher 这样特殊的 watcher 。每个 computed watcher 会持有一个 Dep 实例，当我们去访问 computed 属性的时候，会调用我们 computed watcher 的 evaluate() 计算方法，这个时候就会触发内部 Dep 的 depend 方法去收集依赖，和 data/props 一样会收集当时正在计算的 watcher，把收集到的 watcher 做为 Dep 的 subs（订阅者）收集起来。收集起来的作用就是当我们的计算属性依赖的值发生了变化，就会触发 computed watcher 进行重新计算，重新计算的结果如果变了，就会调用 Dep.notify 通知订阅 computed 变化的这些订阅者来触发它们的更新。
4. 对于 watch 而言，实际上会创建一个 user watcher，user watcher 实际上可以理解为用户自定义的 watcher，它可以观测 data,props 的变化，也可以观测 computed 的变化。当我们的观测的属性发生变化的时候，就会通知 Dep 去遍历我们所有的 user watcher，去调用它们的 updata 方法，当发现新旧值发生变化就会调用 run 方法，来执行用户定义的回调方法。
5. 对于 vue 的渲染和重新渲染就是基于响应式系统的。在 vue 的创建过程中，对于每一个组件而言，都会执行组件的 mount 方法，mount 过程中内部会创建一个唯一的 render watcher， 当执行 render 渲染的时候，也就是去渲染成 vnode 的过程中，会访问 props,data 等定义的数据或 computed 等等。然后 render watcher 就会作为订阅者订阅了那些渲染需要的数据和计算属性的变化。一旦数据发生变化，就会触发 data,props 或 computed 的 notify 方法，然后就会触发 render watcher 的 update 方法，就会执行它们的 run 方法，在执行 run 方法的过程中，实际上最后会调用 updateComponent 方法重新去做一次渲染。

### Vue 是如何实现数据双向绑定的？

View 变化更新 Data ，可以通过事件监听的方式来实现，所以 Vue 的数据双向绑定的工作主要是如何根据 Data 变化更新 View。

Vue 主要通过以下 4 个部分实现数据双向绑定的：

1. 实现一个监听器 Observer：对数据对象进行遍历，包括子属性对象的属性，利用 Object.defineProperty() 对属性都加上 setter 和 getter。这样的话，给这个对象的某个值赋值，就会触发 setter，那么就能监听到了数据变化。
2. 实现一个解析器 Compile：解析 Vue 模板指令，将模板中的变量都替换成数据，然后初始化渲染页面视图，并将每个指令对应的节点绑定更新函数，添加监听数据的订阅者，一旦数据有变动，收到通知，调用更新函数进行数据更新。
3. 实现一个订阅者 Watcher：Watcher 订阅者是 Observer 和 Compile 之间通信的桥梁 ，主要的任务是订阅 Observer 中的属性值变化的消息，当收到属性值变化的消息时，触发解析器 Compile 中对应的更新函数。
4. 实现一个订阅器 Dep：订阅器采用 发布-订阅 设计模式，用来收集订阅者 Watcher，对监听器 Observer 和 订阅者 Watcher 进行统一管理。

**Vue 的双向绑定设计原理可以概括为以下几个步骤**：

1. 数据劫持：Vue 使用了一个称为“响应式系统”的机制来实现双向绑定。在 Vue 组件实例化时，Vue 会遍历组件的数据对象，将其转换为响应式对象。这个过程中，Vue 使用了 JavaScript 的 Object.defineProperty() 方法来劫持对象的属性访问，从而能够捕获属性的读取和修改操作。
2. 监听器和依赖收集：在数据劫持的过程中，Vue 会为每个属性创建一个依赖收集器。当属性被访问时，Vue 会将正在执行的代码（称为“观察者”）添加到属性的依赖收集器中。这样，当属性的值发生变化时，Vue 就能够通知到所有依赖该属性的观察者，从而触发相应的更新。
3. 模板编译：在组件的渲染过程中，Vue 会将组件的模板编译为渲染函数。在编译过程中，Vue 会解析模板中的指令和表达式，并生成对应的更新函数。
4. 更新视图：当组件的数据发生变化时，Vue 会根据之前收集的依赖关系，触发相应的更新函数。这些更新函数会将数据的变化反映到组件的视图上，实现视图的更新。
5. 用户交互和事件处理：在视图更新的同时，Vue 还会监听用户的交互事件，如点击、输入等。当用户触发事件时，Vue 会执行相应的事件处理函数，从而改变数据的状态，进而触发视图的更新。

通过以上的设计原理，Vue 实现了数据的双向绑定，即当数据发生变化时，视图会自动更新；同时，当用户与视图进行交互时，数据也会相应地发生变化。这种双向绑定的设计使得开发者可以更方便地处理数据和视图之间的同步，提高了开发效率。

### Vue 框架怎么实现数组的监听（变异）？

```js
const arrayProto = Array.prototype;
export const arrayMethods = Object.create(arrayProto);const methodsToPatch = [
  "push",
  "pop",
  "shift",
  "unshift",
  "splice",
  "sort",
  "reverse"
];
/** * Intercept mutating methods and emit events */methodsToPatch.forEach(function(method) {
  // cache original method
  const original = arrayProto[method];
  def(arrayMethods, method, function mutator(...args) {
    const result = original.apply(this, args);
    const ob = this.__ob__;
    let inserted;
    switch (method) {
      case "push":
      case "unshift":
        inserted = args;
        break;
      case "splice":
        inserted = args.slice(2);
        break;
    }
    if (inserted) ob.observeArray(inserted);
    // notify change
    ob.dep.notify();
    return result;
  });});
/** * Observe a list of Array items. */Observer.prototype.observeArray = function observeArray(items) {
  for (var i = 0, l = items.length; i < l; i++) {
    observe(items[i]);
  }
};
```

简单来说,Vue 通过原型拦截的方式重写了数组的 7 个方法,首先获取到这个数组的ob,也就是它的 Observer 对象,如果有新的值,就调用 observeArray 对新的值进行监听,然后手动调用 notify,通知 render watcher,执行 update

### 说说你对虚拟 DOM 的了解，虚拟 DOM是怎么生成的？为什么要用虚拟 DOM?

**什么是虚拟 DOM**

虚拟 DOM 本质上是 JavaScript 对象,这个对象就是更加轻量级的对 DOM 的描述和抽象

**虚拟 DOM是怎么生成的**

虚拟DOM的生成过程如下：

- 初始渲染：当Vue应用初始化时，Vue会通过解析组件的模板或渲染函数生成初始的虚拟DOM树。这个初始的虚拟DOM树与实际的DOM结构一一对应，但是它只是存在于内存中，还没有被渲染到浏览器的页面上。
- 数据变更：当Vue应用中的数据发生变化时，Vue会触发重新渲染的过程。在重新渲染之前，Vue会生成新的虚拟DOM树。
- 对比更新：Vue会将新生成的虚拟DOM树与之前的虚拟DOM树进行对比，找出两者之间的差异。这个对比的过程称为虚拟DOM的diff算法。
- 更新实际DOM：根据对比的结果，Vue会确定需要进行更新的具体DOM节点，并将这些差异应用到实际的DOM树上。这个过程称为DOM的patch操作。

通过使用虚拟DOM，Vue可以将对实际DOM的操作最小化，从而提高性能。相比直接操作实际的DOM，虚拟DOM的对比和更新过程更高效，因为它是在内存中进行的，不涉及实际的页面渲染。

需要注意的是，虚拟DOM并不是所有情况下都会带来性能提升。在某些简单的场景下，直接操作实际的DOM可能更加高效。虚拟DOM的优势主要体现在复杂的页面结构和频繁的数据变更场景下。

**为什么需要虚拟 DOM**

- 频繁变动 DOM 会造成浏览器的回流（重排）或者重绘,因此我们需要这一层抽象，在 patch 过程中尽可能地一次性将差异更新到 DOM 中，这样就尽可能的减少了 DOM 的操作，提高了程序性能
- 数据驱动 DOM 的更新，省略手动 DOM 操作可以大大提高开发效率
- 虚拟 DOM 最初的目的是更好的跨平台，例如 Node.js 就没有 DOM,如果想实现 SSR(服务端渲染),那么一个方式就是借助虚拟 DOM,因为 拟 DOM 本身是 JavaScript 对象

### Vue的 nextTick 原理

**JS 运行机制**

JS 执行是单线程的，它是基于事件循环的。事件循环大致分为以下几个步骤:

1. 所有同步任务都在主线程上执行，形成一个执行栈（execution context stack）。
2. 主线程之外，还存在一个"任务队列"（task queue）。只要异步任务有了运行结果，就在"任务队列"之中放置一个事件。
3. 一旦"执行栈"中的所有同步任务执行完毕，系统就会读取"任务队列"，看看里面有哪些事件。那些对应的异步任务，于是结束等待状态，进入执行栈，开始执行。
4. 主线程不断重复上面的第三步。

主线程的执行过程就是一个 tick，而所有的异步结果都是通过 “任务队列” 来调度。 消息队列中存放的是一个个的任务（task）。 规范中规定 task 分为两大类，分别是 macro task（宏任务） 和 micro task（微任务），并且每个 macro task 结束后，都要清空所有的 micro task。

```javascript
for (macroTask of macroTaskQueue) {
  // 1. Handle current MACRO-TASK
  handleMacroTask();
  // 2. Handle all MICRO-TASK
  for (microTask of microTaskQueue) {
    handleMicroTask(microTask);
  }}
```

在浏览器环境中 :

常见的 macro task 有 setTimeout、MessageChannel、postMessage、setImmediate

常见的 micro task 有 MutationObsever 和 Promise.then

**异步更新队列**

可能你还没有注意到，Vue 在更新 DOM 时是异步执行的。只要侦听到数据变化，Vue 将开启一个队列，并缓冲在同一事件循环中发生的所有数据变更。

数据改变后，触发渲染 watcher 的 update，但是 watchers 的 flush 是在 nextTick 后，所以重新渲染是异步的。

如果同一个 watcher 被多次触发，只会被推入到队列中一次。这种在缓冲时去除重复数据对于避免不必要的计算和 DOM 操作是非常重要的。(这就是为什么 Vue的数据为什么频繁变化但只会更新一次)

然后，在下一个的事件循环“tick”中，Vue 刷新队列并执行实际 (已去重的) 工作。

Vue 在内部对异步队列尝试使用原生的 `Promise.then`、`MutationObserver` 和 `setImmediate`，如果执行环境不支持，则会采用 `setTimeout(fn, 0)` 代替。

在 vue2.5 的源码中，macrotask 降级的方案依次是：`setImmediate、MessageChannel、setTimeout`

vue 的 nextTick 方法的实现原理:

1. vue 用异步队列的方式来控制 DOM 更新和 nextTick 回调先后执行
2. microtask 因为其高优先级特性，能确保队列中的微任务在一次事件循环前被执行完毕
3. 考虑兼容问题,vue 做了 microtask 向 macrotask 的降级方案

### 说一下 Vue 的 keep-alive 是如何实现的，具体缓存的是什么？

```js
export default {
  name: "keep-alive",  abstract: true, // 抽象组件属性 ,它在组件实例建立父子关系的时候会被忽略,发生在 initLifecycle 的过程中
  props: {
    include: patternTypes, // 被缓存组件
    exclude: patternTypes, // 不被缓存组件
    max: [String, Number] // 指定缓存大小
  },
  created() {
    this.cache = Object.create(null); // 缓存
    this.keys = []; // 缓存的VNode的键
  },
  destroyed() {
    for (const key in this.cache) {
      // 删除所有缓存
      pruneCacheEntry(this.cache, key, this.keys);
    }
  },
  mounted() {
    // 监听缓存/不缓存组件
    this.$watch("include", val => {
      pruneCache(this, name => matches(val, name));
    });
    this.$watch("exclude", val => {
      pruneCache(this, name => !matches(val, name)); 
   });
  },
  render() {
    // 获取第一个子元素的 vnode
    const slot = this.$slots.default;
    const vnode: VNode = getFirstComponentChild(slot);
    const componentOptions: ? VNodeComponentOptions =
      vnode && vnode.componentOptions;
    if (componentOptions) {
      // name不在inlcude中或者在exlude中 直接返回vnode
      // check pattern
      const name: ? string = getComponentName(componentOptions);
      const { include, exclude } = this;
      if (
        // not included
        (include && (!name || !matches(include, name))) ||
        // excluded
        (exclude && name && matches(exclude, name))
      ) {
        return vnode;
      }
      const { cache, keys } = this;
      // 获取键，优先获取组件的name字段，否则是组件的tag
      const key: ? string =
        vnode.key == null ? 
	    // same constructor may get registered as different local components
        // so cid alone is not enough (#3269)
            componentOptions.Ctor.cid +
            (componentOptions.tag ? `::${componentOptions.tag}` : "")
          : vnode.key;
      // 命中缓存,直接从缓存拿vnode 的组件实例,并且重新调整了 key 的顺序放在了最后一个
      if (cache[key]) {
        vnode.componentInstance = cache[key].componentInstance;
        // make current key freshest
        remove(keys, key);
        keys.push(key);
      }
      // 不命中缓存,把 vnode 设置进缓存
      else {
        cache[key] = vnode;
        keys.push(key);
        // prune oldest entry
        // 如果配置了 max 并且缓存的长度超过了 this.max，还要从缓存中删除第一个
        if (this.max && keys.length > parseInt(this.max)) {
          pruneCacheEntry(cache, keys[0], keys, this._vnode);
        }
      }
      // keepAlive标记位
      vnode.data.keepAlive = true;
    }
    return vnode || (slot && slot[0]);
  }
};
```

原理：

1. 获取 `keep-alive` 包裹着的第一个子组件对象及其组件名
2. 根据设定的 include/exclude（如果有）进行条件匹配,决定是否缓存。不匹配,直接返回组件实例
3. 根据组件 ID 和 tag 生成缓存 Key,并在缓存对象中查找是否已缓存过该组件实例。如果存在,直接取出缓存值并更新该 key 在 `this.keys` 中的位置(更新 key 的位置是实现 LRU 置换策略的关键)
4. 在 `this.cache` 对象中存储该组件实例并保存 key 值,之后检查缓存的实例数量是否超过 max 的设置值,超过则根据 LRU 置换策略删除最近最久未使用的实例（即是下标为 0 的那个 key）
5. 最后组件实例的 keepAlive 属性设置为 true,这个在渲染和执行被包裹组件的钩子函数会用到,这里不细说

**LRU 缓存淘汰算法**

LRU（Least recently used）算法根据数据的历史访问记录来进行淘汰数据,其核心思想是“如果数据最近被访问过,那么将来被访问的几率也更高”。

```
keep-alive` 的实现正是用到了 LRU 策略,将最近访问的组件 push 到 `this.keys` 最后面,`this.keys[0]` 也就是最久没被访问的组件,当缓存实例超过 max 设置值,删除 `this.keys[0]
```

这题可能还会提到 leetcode 上的算法题 [146. LRU 缓存机制(opens new window)](https://leetcode-cn.com/problems/lru-cache/)

### 实现一个简单的双向绑定（v-model）

```js
let obj = {}
let input = document.getElementById('input')
let span = document.getElementById('span')
// 数据劫持
Object.defineProperty(obj, 'text', {
  configurable: true,
  enumerable: true,
  get() {
    console.log('获取数据了')
  },
  set(newVal) {
    console.log('数据更新了')
    input.value = newVal
    span.innerHTML = newVal
  }
})
// 输入监听
input.addEventListener('keyup', function(e) {
  obj.text = e.target.value
})
```

vue 双向数据绑定是通过 **数据劫持** 结合 **发布订阅模式** 的方式来实现的，也就是说数据和视图同步，数据发生变化，视图跟着变化，视图变化，数据也随之发生改变； 核心：Object.defineProperty()方法。

v-model本质上是语法糖，v-model 在内部为不同的输入元素使用不同的属性并抛出不同的事件

### 双向绑定和vuex是否冲突？

是有冲突的，其实官网上就有解释 [https://vuex.vuejs.org/zh/guide/forms.html (opens new window)](https://vuex.vuejs.org/zh/guide/forms.html)`v-model` 会去修改 state 的值，但是 vuex 数据修改又必须经过 mutation，这样就冲突了

简单的办法就是不要使用 `v-model`，自己进行数据绑定即可

### 页面刷新后vuex的state数据丢失怎么解决？

store 里的数据是保存在运行内存中的,当页面刷新时，页面会重新加载 vue 实例，store 里面的数据就会被重新赋值初始化。理论上我们是不需要持久存储vuex的值的，因为请求我们会去接口拿数据，进行重新渲染，和第一次进入一样

但是如果非要保存上一次的临时状态，其实可以使用 localStorage 进行持久化存储，但是这个时候又得去处理和服务端数据同步的问题

### 为啥要有vuex，使用localStorage本地存储不行么？

Vuex 是一个专为 Vue.js 应用程序开发的状态管理模式，Vuex 的状态存储是响应式的。当 Vue 组件从 store 中读取状态的时候，若 store 中的状态发生变化，那么相应的组件也会相应地得到高效更新。

当多个组件拥有同一个状态的时候，vuex 能够很好的帮我们处理，可以很好的使用 vue 开发者工具调试 vuex 的状态，这些优势是 localStorage 不能够很好的模拟的。

### vue组件里写的原生addEventListeners监听事件，要手动去销毁吗？为什么？

要在 beforeDestroy 手动销毁，否则如果在 mounted 中使用 addEventListeners，可能会多次重复注册导致内存泄漏。

### diff 算法时间复杂度多少，为什么

正常需要 O(n^3): 先将两个DOM树的所有节点两两对比，时间复杂度 O(n^2)，再进行树的编辑(插入、替换、删除)需要遍历一次，因此时间复杂度为 O(n^3)

React Diff算法 => O(n) => 简单粗暴，所有的节点按层级比较，只会遍历一次:

按叶子节点位置比较:

```text
[0,0]              :  pA => lA      #相同，不理会
[0.0,0.0]        :  pD => lB      #不同，删除pD，添加lB
[0.1,0.1]        :  pB => lD      #不同，删除pB，添加lD
[0.1.0,0.1.0]  :  pC => Null   #last树没有该节点，直接删除pC
[0.1.2,0.1.2]  :  Null => lC    #prev树没有该节点，添加lC到该位置
```

React认为：一个ReactElement的type不同，那么内容基本不会复用，所以直接删除节点，添加新节点，这是一个非常大的优化，大大减少了对比时间复杂度。

### 网上都说操作真实 DOM 慢，但测试结果却比 React 更快，为什么？

这是一个性能 vs. 可维护性的取舍。框架的意义在于为你掩盖底层的 DOM 操作，让你用更声明式的方式来描述你的目的，从而让你的代码更容易维护。没有任何框架可以比纯手动的优化 DOM 操作更快，因为框架的 DOM 操作层需要应对任何上层 API 可能产生的操作，它的实现必须是普适的。针对任何一个 benchmark，我都可以写出比任何框架更快的手动优化，但是那有什么意义呢？在构建一个实际应用的时候，你难道为每一个地方都去做手动优化吗？出于可维护性的考虑，这显然不可能。框架给你的保证是，你在不需要手动优化的情况下，我依然可以给你提供过得去的性能。

主流的框架 + 合理的优化，足以应对绝大部分应用的性能需求。如果是对性能有极致需求的特殊情况，其实应该牺牲一些可维护性采取手动优化：比如 Atom 编辑器在文件渲染的实现上放弃了 React 而采用了自己实现的 tile-based rendering；又比如在移动端需要 DOM-pooling 的虚拟滚动，不需要考虑顺序变化，可以绕过框架的内置实现自己搞一个。

[跟多请看尤大的回答(opens new window)](https://www.zhihu.com/question/31809713/answer/53544875)

### ssr怎么实现，你们怎么做【描述】【举例】

将动态渲染逻辑做到后端去，并把最终 html 结果直接返回。我们这边是数据动静分离 + 部分 ssr 直出，重要的数据 ssr，比较慢的接口还是放前端。

### Vue 中父组件可以监听到子组件的生命周期吗？

**使用 on 和 emit**

```js
// Parent.vue
<Child @mounted="doSomething"/>
// Child.vue
mounted() {
  this.$emit("mounted");
}
```

**使用 hook 钩子函数**

```js
//  Parent.vue
<Child @hook:mounted="doSomething" ></Child>

doSomething() {
   console.log('父组件监听到 mounted 钩子函数 ...');
},
//  Child.vue
mounted(){
   console.log('子组件触发 mounted 钩子函数 ...');
},
// 以上输出顺序为：
// 子组件触发 mounted 钩子函数 ...
// 父组件监听到 mounted 钩子函数 ...
```

### 怎么给 Vue 定义全局方法

**一、将方法挂载到 Vue.prototype 上面**(缺点：调用这个方法的时候没有提示)

```js
// global.js
const RandomString = (encode = 36, number = -8) => {
  return Math.random() // 生成随机数字, eg: 0.123456
    .toString(encode) // 转化成36进制 : "0.4fzyo82mvyr"
    .slice(number);
},
export default {
	RandomString,
  ...
}
```



```js
// 在项目入口的main.js里配置
import Vue from "vue";
import global from "@/global";
Object.keys(global).forEach((key) => {
  Vue.prototype["$global" + key] = global[key];
});
```



```js
// 挂载之后，在需要引用全局变量的模块处(App.vue)，不需再导入全局变量模块，而是直接用this就可以引用了，如下:
export default {
  mounted() {
    this.$globalRandomString();
  },
};
```



**二、利用全局混入mixin**(优点：因为mixin里面的methods会和创建的每个单文件组件合并。这样做的优点是调用这个方法的时候有提示)

```js
// mixin.js
import moment from 'moment'
const mixin = {
  methods: {
    minRandomString(encode = 36, number = -8) {
      return Math.random() // 生成随机数字, eg: 0.123456
        .toString(encode) // 转化成36进制 : "0.4fzyo82mvyr"
        .slice(number);
    },
    ...
  }
}
export default mixin
```



```js
// 在项目入口的main.js里配置
import Vue from 'vue'
import mixin from '@/mixin'
Vue.mixin(mixin)
```



```js
export default {
 mounted() {
   this.minRandomString()
 }
}
```



**三、使用Plugin方式**(Vue.use的实现没有挂载的功能，只是触发了插件的install方法，本质还是使用了Vue.prototype。)

```js
// plugin.js
function randomString(encode = 36, number = -8) {
  return Math.random() // 生成随机数字, eg: 0.123456
    .toString(encode) // 转化成36进制 : "0.4fzyo82mvyr"
    .slice(number);
}
const plugin = {
  // install 是默认的方法。
  // 当外界在 use 这个组件或函数的时候，就会调用本身的 install 方法，同时传一个 Vue 这个类的参数。
  install: function(Vue){
    Vue.prototype.$pluginRandomString = randomString
    ...
  },
}
export default plugin
```



```js
// 在项目入口的main.js里配置
import Vue from 'vue'
import plugin from '@/plugin'
Vue.use(plugin)
```



```js
export default {
 mounted() {
   this.$pluginRandomString()
 }
}
```



**四、任意 vue 文件中写全局函数**

```js
// 创建全局方法
this.$root.$on("test", function () {
  console.log("test");
});
// 销毁全局方法
this.$root.$off("test");
// 调用全局方法
this.$root.$emit("test");
```



### 说一下 vm.$set 原理

在 Vue.js 里面只有 data 中已经存在的属性才会被 Observe 为响应式数据，如果你是新增的属性是不会成为响应式数据，因此 Vue 提供了一个 api(vm.$set)来解决这个问题。

vm.$set()在 new Vue()时候就被注入到 Vue 的原型上。

从源码可以看出 set 主要逻辑如下：

1. 类型判断
2. target 为数组：调用 splice 方法
3. target 为对象，且 key 不是原型上的属性处理：直接修改
4. target 不能是 Vue 实例，或者 Vue 实例的根数据对象，否则报错
5. target 是非响应式数据时，我们就按照普通对象添加属性的方式来处理
6. target 为响应数据，且 key 为新增属性，我们 key 设置为响应式，并手动触发其属性值的更新

总结；

- 当 target 为数组时，直接调用数组方法 splice 实现；
- 如果目标是对象，会先判读属性是否存在、对象是否是响应式
- 最终如果要对属性进行响应式处理，则是通过调用 defineReactive 方法进行响应式处理
  - defineReactive 方法就是 Vue 在初始化对象时，给对象属性采用 Object.defineProperty 动态添加 getter 和 setter 的功能所调用的方法

### Vue中的 watch 实现原理

watch的分类：

- deep watch（深层次监听）
- user watch（用户监听）
- computed watcher（计算属性）
- sync watcher（同步监听）

watch实现过程：

对于 watch 而言，实际上会创建一个 user watcher，user watcher 实际上可以理解为用户自定义的 watcher，它可以观测 data,props 的变化，也可以观测 computed 的变化。当我们的观测的属性发生变化的时候，就会通知 Dep 去遍历我们所有的 user watcher，去调用它们的 updata 方法，当发现新旧值发生变化就会调用 run 方法，来执行用户定义的回调方法。

### computed 运行原理

对于 computed 属性而言，它实际上内部会创建一个 computed watcher 这样特殊的 watcher 。每个 computed watcher 会持有一个 Dep 实例，当我们去访问 computed 属性的时候，会调用我们 computed watcher 的 evaluate() 计算方法，这个时候就会触发内部 Dep 的 depend 方法去收集依赖，和 data/props 一样会收集当时正在计算的 watcher，把收集到的 watcher 做为 Dep 的 subs（订阅者）收集起来。收集起来的作用就是当我们的计算属性依赖的值发生了变化，就会触发 computed watcher 进行重新计算，重新计算的结果如果变了，就会调用 Dep.notify 通知订阅 computed 变化的这些订阅者来触发它们的更新。

- computed 本质是一个惰性求值的观察者。
- computed 内部实现了一个惰性的 watcher,也就是 computed watcher,computed watcher 不会立刻求值,同时持有一个 dep 实例。
- 它的创建过程和被访问会触发 getter 以及依赖更新
- 其内部通过 this.dirty 属性标记计算属性是否需要重新求值
- 当 computed 的依赖状态发生改变时,就会通知这个惰性的 watcher
- computed watcher 通过 this.dep.subs.length 判断有没有订阅者
- 有的话,会重新计算,然后对比新旧值,如果变化了,会重新渲染。 (Vue 想确保不仅仅是计算属性依赖的值发生变化，而是当计算属性最终计算的值发生变化时才会触发渲染 watcher 重新渲染，本质上是一种优化。)
- 没有的话,仅仅把 this.dirty = true。 (当计算属性依赖于其他数据时，属性并不会立即重新计算，只有之后其他地方需要读取属性的时候，它才会真正计算，即具备 lazy（懒计算）特性。)

### computed计算值为什么还可以依赖另外一个computed计算值？

在Vue.js中，computed属性是一种特殊的属性，它的值是通过计算得出的，可以依赖于其他数据属性（data属性）或其他computed属性。

当一个computed属性依赖于另一个computed属性时，Vue.js会自动建立它们之间的依赖关系。这意味着当被依赖的computed属性发生变化时，依赖它的computed属性也会重新计算。

Vue.js使用了响应式系统来实现这种依赖追踪和自动更新。当一个computed属性被访问时，Vue.js会追踪它所依赖的数据属性和其他computed属性。如果任何一个被依赖的属性发生变化，Vue.js会知道需要重新计算该computed属性，并将最新的值返回。

这种设计的好处是：

- 模块化和可读性：将计算逻辑分解为多个computed属性可以使代码更加模块化和可读性更高。每个computed属性只关注它所依赖的数据和其他computed属性，使代码更易于维护和理解。
- 缓存和性能优化：computed属性的结果会被缓存，只有在其依赖项发生变化时才会重新计算。如果一个computed属性依赖于另一个computed属性，当被依赖的属性发生变化时，只有直接或间接依赖它的属性才会重新计算，从而避免了不必要的计算。
- 响应式更新：当依赖的属性发生变化时，依赖它的 computed 属性会自动更新，从而保持界面与数据的同步。

需要注意的是，在Vue.js中，computed属性应该是纯粹的，即不应该有副作用或修改数据的行为。这样可以确保computed属性的可预测性和一致性。

总结：Vue.js中的computed属性可以依赖于其他computed属性，这样的设计有助于代码的模块化、性能优化和响应式更新。

### 谈谈 Vue 事件机制,手写$on,$off,$emit,$once

Vue 事件机制 本质上就是 一个 发布-订阅 模式的实现。

```js
class Vue {
  constructor() {
    //  事件通道调度中心
    this._events = Object.create(null);
  }
  $on(event, fn) {
    if (Array.isArray(event)) {
      event.map(item => {
        this.$on(item, fn);
      });
    } else {
      (this._events[event] || (this._events[event] = [])).push(fn);
    }
    return this;
  }
  $once(event, fn) {
    function on() {
      this.$off(event, on);
      fn.apply(this, arguments);
    }
    on.fn = fn;
    this.$on(event, on);
    return this;
  }
  $off(event, fn) {
    if (!arguments.length) {
      this._events = Object.create(null);
      return this;
    }
    if (Array.isArray(event)) {
      event.map(item => {
        this.$off(item, fn);
      });
      return this;
    }
    const cbs = this._events[event];
    if (!cbs) {
      return this;
    }
    if (!fn) {
      this._events[event] = null;
      return this;
    }
    let cb;
    let i = cbs.length;
    while (i--) {
      cb = cbs[i];
      if (cb === fn || cb.fn === fn) {
        cbs.splice(i, 1);
        break;
      }
    }
    return this;
  }
  $emit(event) {
    let cbs = this._events[event];
    if (cbs) {
      const args = [].slice.call(arguments, 1);
      cbs.map(item => {
        args ? item.apply(this, args) : item.call(this);
      });
    }
    return this;
  }
}
```



### vm.$set()实现原理是什么?

受现代 JavaScript 的限制 (而且 Object.observe 也已经被废弃)，Vue 无法检测到对象属性的添加或删除。

由于 Vue 会在初始化实例时对属性执行 getter/setter 转化，所以属性必须在 data 对象上存在才能让 Vue 将它转换为响应式的。

对于已经创建的实例，Vue 不允许动态添加根级别的响应式属性。但是，可以使用 Vue.set(object, propertyName, value) 方法向嵌套对象添加响应式属性。

那么 Vue 内部是如何解决对象新增属性不能响应的问题的呢?

```js
export function set(target: Array<any> | Object, key: any, val: any): any {
  // target 为数组
  if (Array.isArray(target) && isValidArrayIndex(key)) {
    // 修改数组的长度, 避免索引>数组长度导致splice()执行有误
    target.length = Math.max(target.length, key);
    // 利用数组的splice变异方法触发响应式
    target.splice(key, 1, val);
    return val;
  }
  // target为对象, key在target或者target.prototype上 且必须不能在 Object.prototype 上,直接赋值
  if (key in target && !(key in Object.prototype)) {
    target[key] = val;
    return val;
  }
  // 以上都不成立, 即开始给target创建一个全新的属性
  // 获取Observer实例
  const ob = (target: any).__ob__;
  // target 本身就不是响应式数据, 直接赋值
  if (!ob) {
    target[key] = val;
    return val;
  }  // 进行响应式处理
  defineReactive(ob.value, key, val);
  ob.dep.notify();
  return val;
}
```

1. 如果目标是数组,使用 vue 实现的变异方法 splice 实现响应式
2. 如果目标是对象,判断属性存在,即为响应式,直接赋值
3. 如果 target 本身就不是响应式,直接赋值
4. 如果属性不是响应式,则调用 defineReactive 方法进行响应式处理

### 说一下对vue3.0的了解？

- 响应式原理的改变 Vue3.x 使用 Proxy 取代 Vue2.x 版本的 Object.defineProperty
- 组件选项声明方式 Vue3.x 使用 Composition API setup 是 Vue3.x 新增的一个选项， 他是组件内使用 Composition API 的入口。
- 模板语法变化 slot 具名插槽语法 自定义指令 v-model 升级
- 其它方面的更改 Suspense 支持 Fragment（多个根节点）和 Protal（在 dom 其他部分渲染组建内容）组件，针对一些特殊的场景做了处理。 基于 treeshaking 优化，提供了更多的内置功能
- 等等 ....

### [#](https://cchroot.github.io/interview/pages/interview questions/vue进阶面试题.html#vue-响应式数据原理-vue2-x-vue3-0-有什么区别)Vue 响应式数据原理（Vue2.x & Vue3.0）有什么区别 ？

Vue2.x

- 在初始化数据时，会使用 Object.defineProperty 重新定义 data 中的所有属性。
- 当页面使用对应属性时，首先会进行依赖收集（收集当前组件的 watcher），如果属性发生变化会通知相关依赖进行派发更细（发布订阅模式）

vue3.0

- 采用 ES6 中的 proxy 代替 Object.defineProperty 做数据监听。

### Proxy 与 Object.defineProperty 的优劣对比 ?

Proxy 的优势如下:

- Proxy 可以直接监听对象而非属性
- Proxy 可以直接监听数组的变化
- Proxy 有多达 13 种拦截方法，不限于 apply、ownKeys、deleteProperty、has 等等是 Object.defineProperty 不具备的
- Proxy 返回的是一个新对象,我们可以只操作新的对象达到目的，而 Object.defineProperty 只能遍历对象属性直接修改
- Proxy 作为新标准将受到浏览器厂商重点持续的性能优化，也就是传说中的新标准的性能红利

Object.defineProperty 的优势如下:

- 兼容性好，支持到 IE9

### vue3.0为什么要引入CompositionAPI？

1. 更好的代码组织，options api 造成了代码的跳来跳去
2. 逻辑复用更加的方便，虽然 mixin 也能够很好的复用代码，但是当 mixin 多了以后就不知道变量哪里来的了，还会造成命名冲突
3. 没有让人捉摸不透的 this

### vue3.0为啥不需要时间分片

在Web应用程序中，丢帧（janky）更新通常是由于同步的高CPU时间和原生DOM的更新造成的。

时间切片是在CPU工作期间保持应用响应的一种尝试，但它只影响CPU工作。DOM的刷新必须是同步的，以确保最终DOM状态的一致性。

只有在频繁进行超过100ms的纯CPU任务更新时，时间切片才实际有用

有趣的地方在于，这样的场景更经常地发生在React中，因为：

1. React 的虚拟 DOM 操作（reconciliation ）天生就比较慢，因为它使用了大量的 Fiber 架构；
2. React 使用 JSX 来渲染函数相对较于用模板来渲染更加难以优化，模板更易于静态分析。
3. React Hooks 将大部分组件树级优化（即防止不必要的子组件的重新渲染）留给了开发人员，开发人员在大多数情况下需要显式地使用 useMemo。而且，不管什么时候 React 接收到了 children 属性，它几乎总要重新渲染，因为每次的子组件都是一棵新的 vdom 树。这意味着，一个使用 Hook 的 React 应用在默认配置下会过度渲染。更糟糕的是，像 useMomo 这类优化不能轻易地自动应用，因为：
   1. 它需要正确的deps数组；
   2. 盲目地任意使用它可能会阻塞本该进行的更新，类似与PureComponent。

不幸的是，大多数开发人员都很懒，不会积极地优化他们的应用。所以大多数使用 Hook 的 React 应用会做很多不必要的 CPU 工作。

相比之下，Vue就上面的问题做一下比较：

1. 本质上更简单，因此虚拟DOM操作更快（没有时间切片-> 没有fiber-> 更少的开销）；
2. 通过分析模板进行了大量的 AOT 优化，减少了虚拟 DOM 操作的基本开销。 Benchmark 显示，对于一个典型的 DOM 代码块来说，动态与静态内容的比例大约是1:4，Vue3 的原生执行速度甚至比 Svelte 更快，在CPU上花费的时间不到 React 的1/10。

3。 智能组件树级优化通过响应式跟踪，将插槽编译成函数（避免子元素重复渲染）和自动缓存内联句柄（避免内联函数重复渲染）。除非必要，否则子组件永远不需要重新渲染。这一切不需要开发人员进行任何手动优化。

这意味着对于同一个更新，React 应用可能造成多个组件重新渲染，但在 Vue 中大部分情况下只会导致一个组件重新渲染。

默认情况下， Vue3 应用比 React 应用花费更少的 CPU 工作时间， 并且 CPU 工作时间超过 100ms 的机会大幅度减少了，除非在一些极端的情况下，DOM 可能成为更主要的瓶颈。

现在，时间切片或并发模式带来了另一个问题：因为框架现在安排和协调了所有更新，它在优先级、失效、重新实例化等方面产生了大量额外的复杂性。所有这些逻辑处理都不可能被 tree-shaken，这将导致运行时基线的大小膨胀。即使包含了 Suspense 和所有的tree-shaken，Vue 3 的运行时仍然只有当前 React + React DOM 的 1/4 大小。

注意，这并不是说并发模式是一个不好的主意。它提供一个有趣的新方法解决某一类问题(尤其是相关协调异步状态转换),但时间切片（作为并发的一个子特性）特别解决了React中比其他框架更突出的问题，同时也带来了成本。对于 Vue 3 来说，这种权衡似乎是不值得的。

答案来自: [为什么Vue3不使用时间切片（Time Slicing）(opens new window)](https://juejin.cn/post/6844904134945030151)

[Why remove time slicing from vue3? #89(opens new window)](https://github.com/vuejs/rfcs/issues/89)

### Vue 有了数据响应式，为何还要 diff ？

**核心原因：粒度**

- React通过setState 知道有变化了，但不知道哪里变化了，所以需要通过 diff 找出变化的地方并更新 dom。
- Vue 已经可以通过响应式系统知道哪里发生了变化，但是所有变化都通过响应式会创建大量 Watcher，极其消耗性能，因此 vue 采用的方式是通过响应式系统知道哪个组件发生了变化，然后在组件内部使用 diff。

这样的中粒度策略，即不会产生大量的 Watcher，也使 diff 的节点减少了，一举两得。

### 在实际项目中，vue 组件通信有哪些注意点 ？

1. 明确通信方式：在组件通信之前，明确使用何种通信方式是很重要的。Vue中提供了多种通信方式，如props和事件总线（Event Bus）、Vuex、provide/inject等。根据具体情况选择适合的通信方式。
2. 组件解耦：组件之间应该尽量保持解耦，即一个组件不应该过于依赖其他组件的内部实现细节。通过明确定义props和事件接口，组件可以更加独立地工作，提高复用性和可维护性。
3. 单向数据流：在Vue中，数据流是单向的，自上而下。父组件通过props向子组件传递数据，子组件通过事件向父组件发送消息。遵循这个单向数据流可以使数据流动更加可控和可预测。
4. 避免过度通信：过多的组件通信可能导致代码复杂性增加。在设计组件通信时，要避免过度通信，只传递必要的数据和事件，以保持代码的简洁性和可读性。
5. 适当使用事件总线：事件总线（Event Bus）是一种简单的组件通信方式，可以在组件之间进行事件的发布和订阅。但是，过度使用事件总线可能导致代码难以维护和调试，因此应该谨慎使用，并在必要时考虑其他更适合的通信方式。
6. 谨慎使用provide/inject：provide/inject是一种高级的组件通信方式，可以在祖先组件中提供数据，并在后代组件中注入使用。然而，过度使用provide/inject可能导致组件之间的依赖关系不明确，难以追踪和理解，因此需要谨慎使用。
7. 考虑跨组件通信方案：有时候需要在非父子组件之间进行通信，比如兄弟组件或跨层级组件之间的通信。这时可以考虑使用事件总线、Vuex或其他第三方库来实现跨组件通信。
8. 使用Vuex进行状态管理：如果组件之间需要共享状态或进行复杂的通信，考虑使用Vuex进行状态管理。Vuex提供了一个集中式的状态管理方案，可以更好地管理和共享组件之间的状态。

总之，组件通信在实际项目中是一个重要的考虑因素。通过选择合适的通信方式、保持组件解耦、遵循单向数据流原则以及适当使用状态管理等技巧，可以有效地管理组件之间的通信，提高代码的可维护性和可扩展性。