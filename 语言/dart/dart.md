**1**、**Dart**语法中**dynamic**，**var**，**object**三者的区别

> var定义的类型是不可变的，dynamic和object类型是可以变的，而dynamic 与object 的最大的区别是在静态类型检查上

**2**、**const**和**final**的区别

> 均表示不可被修改
>  相同点
>  1、final、const必须初始化
>  2、 final、const只能赋值一次
>  不同点
>  1、 final可修饰实例变量、const不可以修饰实例变量
>  2、访问类中const修饰的变量需要static修饰
>  3、const修饰的List集合任意索引不可修改，final修饰的可以修改
>  4、const 用来修饰变量 只能被赋值一次，在编译时赋值
>  final 用来修饰变量 只能被赋值一次，在运行时赋值
>  5、final 只可用来修饰变量， const 关键字即可修饰变量也可用来修饰 常量构造函数
>  当const修饰类的构造函数时，它要求该类的所有成员都必须是final的。

**3**、**Dart**中 **??** 与 **??=** 的区别

> A??B
>  左边如果为空返回右边的值，否则不处理。
>  A??=B
>  左边如果为空把B的值赋值给A

**4**、什么是**flutter**里的**key?** 有什么用？

> key是Widgets，Elements和SemanticsNodes的标识符。
>  key有LocalKey 和 GlobalKey两种。
>
> LocalKey 如果要修改集合中的控件的顺序或数量。GlobalKey允许 Widget 在应用中的 任何位置更改父级而不会丢失 State。

**5**、**Flutter**中的**GlobalKey**是什么，有什么作用

> GlobalKey可以获取到对应的Widget的State对象
>
> 需求：当我们页面内容很多时，而需要改变的内容只有很少的一部分且在树的底层的时 候，我们如何去实现增量更新？
>
> 通常情况下有两种方式，第一种是通过方法的回调，去实现数据更新，第二种是通过GlobalKey，在StatelessWidget引用StatefulWidget。

**6**、**main()** 和**runApp()** 函数在**flutter**的作用分别是什么？有什么关系吗？

> main函数是类似于java语言的程序运行入口函数
>
> runApp函数是渲染根widget树的函数
>
> 一般情况下runApp函数会在main函数里执行

**7**、什么是**widget?** 在**flutter**里有几种类型的**widget**？分别有什么区别？能分别说一下生命周期吗？

> widget在flutter里基本是一些UI组件
>
> 有两种类型的widget，分别是statefulWidget 和 statelessWidget两种
>
> statelessWidget不会自己重新构建自己，但是statefulWidget会

**statelessWidget**生命周期：

> 1. 构造函数
> 2. build方法

**StatefulWidget**生命周期：

> 1. widget的构造方法
> 2. widget的createState方法
> 3. state的构造方法
> 4. state的initState方法(重写该方法时，必须要先调用super. initState())
> 5. didChangeDependencies方法，分两种情况：
>
> 调用initState方法后，会调用该方法
>
> 从其他widget中依赖一些数据发生改变时，比如用InheritedWidget，provider来监听数据的改变
>
> 1. state的build方法（当调用setState方法，会重新调用build进行渲染）
> 2. state的deactivate方法（当state被暂时从视图移除的时候会调用，页面push走、pop回来的时候都会调用。因为push、pop会改变widget在视图树位置，需要先移除再添加。重写该方法时，必须要先调用super.deactivate()）
> 3. state的dispose方法。页面被销毁的时候调用，如：pop操作。通常情况下，自己的释放逻辑放在super.dispose()之前，先操作子类在操作父类。

**8**、简单说一下在**Flutter**里**async**和**await**？

> await的出现会把await之前和之后的代码分为两部分，await并不像字面意思所表示的程序运行到这里就阻塞了，而是立刻结束当前函数的执行并返回一个Future，函数内剩余代码通过调度异步执行。
>
> async是和await搭配使用的，await只在async函数中出现。在async 函数里可以没有await或者有多个await。

**9**、**future**和**steam**有什么不一样？

> 在 Flutter 中有两种处理异步操作的方式 Future 和 Stream，Future 用于处理单个异步操作，Stream 用来处理连续的异步操作。

**10**、**flutter**中**Widget**、**Element**、**RenderObject**、**Layer**都有什么关系？

> 首先看一下这几个对象的含义及作用。
>
> **Widget**：仅用于存储渲染所需要的信息。
>
> **RenderObject**：负责管理布局、绘制等操作。
>
> **Element**：才是这颗巨大的控件树上的实体。

> Widget会被inflate（填充）到Element，并由Element管理底层渲染树。Widget并不会直接管理状态及渲染,而是通过State这个对象来管理状态。Flutter创建Element的可见树，相对于Widget来说，是可变的，通常界面开发中，我们不用直接操作Element,而是由框架层实现内部逻辑。就如一个UI视图树中，可能包含有多个TextWidget(Widget被使用多次)，但是放在内部视图树的视角，这些TextWidget都是填充到一个个独立的Element中。Element会持有renderObject和widget的实例。记住，Widget 只是一个配置，RenderObject 负责管理布局、绘制等操作。
>
> 在第一次创建 Widget 的时候，会对应创建一个 Element， 然后将该元素插入树中。如果之后 Widget 发生了变化，则将其与旧的 Widget 进行比较，并且相应地更新 Element。重要的是，Element 不会被重建，只是更新而已。

**11**、简述**state**的生命周期

> ![20200614142936682.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/b31c704994534c76b5e8d1c986503141~tplv-k3u1fbpfcp-zoom-in-crop-mark:1512:0:0:0.awebp?)

**12**、简述**flutter**中自定义**View**流程？

> 1，已有控件（widget）的继承，组合
>
> 2，自定义绘制widget,也就是利用paint，cavans等进行绘制视图。

**13**、**flutter_boost**的优缺点，内部实现

**14**、**flutter**的渲染机制

> ![Graphics Pipeline.webp](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/d5b92b67e6aa492bbb93d9ceeaeed6d7~tplv-k3u1fbpfcp-zoom-in-crop-mark:1512:0:0:0.awebp?) Flutter只关心向 GPU提供视图数据，GPU的 VSync信号同步到 UI线程，UI线程使用 Dart来构建抽象的视图结构，这份数据结构在 GPU线程进行图层合成，视图数据提供给 Skia引擎渲染为 GPU数据，这些数据通过 OpenGL或者 Vulkan提供给 GPU。

**15**、**flutter**和**native**的优缺点

**16**、**flutter**支不支持 **120hz**

**17**、状态管理熟悉哪些

> Flutter中的状态和前端React中的状态概念是一致的。React框架的核心思想是组件化，应用由组件搭建而成，组件最重要的概念就是状态，状态是一个组件的UI数据模型，是组件渲染时的数据依据。
>
> Flutter的状态可以分为全局状态和局部状态两种。常用的状态管理有ScopedModel、BLoC、Redux / FishRedux和Provider。

**18**、多线程怎么处理

**19**、**flutter**中大图片上传

**20**、**await for** 如何使用

> await for是不断获取stream流中的数据，然后执行循环体中的操作。它一般用在直到stream什么时候完成，并且必须等待传递完成之后才能使用，不然就会一直阻塞。

**21**、Stream有两种订阅模式

> Stream 用来处理连续的异步操作，Stream 是一个抽象类，用于表示一系列异步数据的源。它是一种产生连续事件的方式，可以生成数据事件或者错误事件，以及流结束时的完成事件
>
> Stream 分单订阅流和广播流。
>
> 网络状态的监控

**22**、**flutter butild** 方法中的 **BuildContext** 具体是什么东西

> BuildContext底层原理实现实际上就是Element of(context)原理，其实就是通过调用BuildContext各种实现方法遍历widget tree和Element tree 从而获取到指定的对象来达到数据共享的目的
>
> 【【Flutter核心类分析】深入理解BuildContext | 航行学园】[www.voycn.com/article/flu…](https://link.juejin.cn?target=http%3A%2F%2Fwww.voycn.com%2Farticle%2Fflutterhexinleifenxi-shenrulijiebuildcontext)

**23**、**flutter** 打包成**web** 移动端 ****桌面端的过程是怎么样的

**24**、**dart**是值传递还是引用传递

> 值传递

**25**、**dart**是弱引用还是强引用

> 强引用

**26**、**get set**方法实现

**27**、**Flutter** 是如何与原生**Android**、**iOS**进行通信的？

> Flutter 通过 PlatformChannel 与原生进行交互，其中 PlatformChannel 分为三种：
>
> BasicMessageChannel ：用于传递字符串和半结构化的信息。
>
> MethodChannel ：用于传递方法调用（method invocation）。
>
> EventChannel : 用于数据流（event streams）的通信。
>
> 同时 Platform Channel 并非是线程安全的

**28**、简述**Flutter** 的热重载

> Flutter 的热重载是基于 JIT 编译模式的代码增量同步。由于 JIT 属于动态编译，能够将 Dart 代码编译成生成中间代码，让 Dart VM 在运行时解释执行，因此可以通过动态更新中间代码实现增量同步。
>
> 热重载的流程可以分为 5 步，包括：扫描工程改动、增量编译、推送更新、代码合并、Widget 重建。Flutter 在接收到代码变更后，并不会让 App 重新启动执行，而只会触发 Widget 树的重新绘制，因此可以保持改动前的状态，大大缩短了从代码修改到看到修改产生的变化之间所需要的时间。
>
> 另一方面，由于涉及到状态的保存与恢复，涉及状态兼容与状态初始化的场景，热重载是无法支持的，如改动前后 Widget 状态无法兼容、全局变量与静态属性的更改、main 方法里的更改、initState 方法里的更改、枚举和泛型的更改等。
>
> 可以发现，热重载提高了调试 UI 的效率，非常适合写界面样式这样需要反复查看修改效果的场景。但由于其状态保存的机制所限，热重载本身也有一些无法支持的边界。

**29**、怎么理解**Isolate**？

> isolate是Dart对actor并发模式的实现。 isolate是有自己的内存和单线程控制的运行实体。isolate本身的意思是“隔离”，因为isolate之间的内存在逻辑上是隔离的。isolate中的代码是按顺序执行的，任何Dart程序的并发都是运行多个isolate的结果。因为Dart没有共享内存的并发，没有竞争的可能性所以不需要锁，也就不用担心死锁的问题

**30**、**Dart** 的作用域

> Dart 没有 「public」「private」等关键字，默认就是公开的，私有变量使用下划线 _开头。

**31**、**Dart** 当中的 ****「 **..** 」表示什么意思？

> Dart 当中的 「..」意思是 「级联操作符」，为了方便配置而使用。「..」和「.」不同的是 调用「..」后返回的相当于是 this，而「.」返回的则是该方法返回的值 。

**32**、**Dart** 是不是单线程模型？是如何运行的？

> Dart 是单线程模型，运行的的流程如下图。
>
> ![Start app.webp](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/46bcfacd02084e5e974ace0d892bf5c5~tplv-k3u1fbpfcp-zoom-in-crop-mark:1512:0:0:0.awebp?)
>
> 简单来说，Dart 在单线程中是以消息循环机制来运行的，包含两个任务队列，一个是“微任务队列” microtask queue，另一个叫做“事件队列” event queue。
>
> 当Flutter应用启动后，消息循环机制便启动了。首先会按照先进先出的顺序逐个执行微任务队列中的任务，当所有微任务队列执行完后便开始执行事件队列中的任务，事件任务执行完毕后再去执行微任务，如此循环往复，生生不息。

**33**、**Dart** 是如何实现多任务并行的？

> 前面说过， Dart 是单线程的，不存在多线程，那如何进行多任务并行的呢？其实，Dart的多线程和前端的多线程有很多的相似之处。Flutter的多线程主要依赖Dart的并发编程、异步和事件驱动机制。
>
> 简单的说，在Dart中，一个Isolate对象其实就是一个isolate执行环境的引用，一般来说我们都是通过当前的isolate去控制其他的isolate完成彼此之间的交互，而当我们想要创建一个新的Isolate可以使用Isolate.spawn方法获取返回的一个新的isolate对象，两个isolate之间使用SendPort相互发送消息，而isolate中也存在了一个与之对应的ReceivePort接受消息用来处理，但是我们需要注意的是，ReceivePort和SendPort在每个isolate都有一对，只有同一个isolate中的ReceivePort才能接受到当前类的SendPort发送的消息并且处理。

**34**、说一下**Dart**异步编程中的 **Future**关键字？

> 前面说过，Dart 在单线程中是以消息循环机制来运行的，其中包含两个任务队列，一个是“微任务队列” microtask queue，另一个叫做“事件队列” event queue。
>
> 在Java并发编程开发中，经常会使用Future来处理异步或者延迟处理任务等操作。而在Dart中，执行一个异步任务同样也可以使用Future来处理。在 Dart 的每一个 Isolate 当中，执行的优先级为 ： Main > MicroTask > EventQueue。

**35**、说一下 **mixin**机制？

> mixin 是Dart 2.1 加入的特性，以前版本通常使用abstract class代替。简单来说，mixin是为了解决继承方面的问题而引入的机制，Dart为了支持多重继承，引入了mixin关键字，它最大的特殊处在于： mixin定义的类不能有构造方法，这样可以避免继承多个类而产生的父类构造方法冲突。
>
> mixins的对象是类，mixins绝不是继承，也不是接口，而是一种全新的特性，可以mixins多个类，mixins的使用需要满足一定条件。

**36**、介绍下**Flutter**的**FrameWork**层和**Engine**层，以及它们的作用

> Flutter的FrameWork层是用Dart编写的框架（SDK），它实现了一套基础库，包含Material（Android风格UI）和Cupertino（iOS风格）的UI界面，下面是通用的Widgets（组件），之后是一些动画、绘制、渲染、手势库等。这个纯 Dart实现的 SDK被封装为了一个叫作 dart:ui的 Dart库。我们在使用 Flutter写 App的时候，直接导入这个库即可使用组件等功能。
>
> Flutter的Engine层是Skia 2D的绘图引擎库，其前身是一个向量绘图软件，Chrome和 Android均采用 Skia作为绘图引擎。Skia提供了非常友好的 API，并且在图形转换、文字渲染、位图渲染方面都提供了友好、高效的表现。Skia是跨平台的，所以可以被嵌入到 Flutter的 iOS SDK中，而不用去研究 iOS闭源的 Core Graphics / Core Animation。Android自带了 Skia，所以 Flutter Android SDK要比 iOS SDK小很多。

**37**、简述**Flutter**的线程管理模型

> ![APp.webp](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/39f89b2ff8074facbe0ef7b38ccd4c14~tplv-k3u1fbpfcp-zoom-in-crop-mark:1512:0:0:0.awebp?)
>
> 默认情况下，Flutter Engine层会创建一个Isolate，并且Dart代码默认就运行在这个主Isolate上。必要时可以使用spawnUri和spawn两种方式来创建新的Isolate，在Flutter中，新创建的Isolate由Flutter进行统一的管理。
>  事实上，Flutter Engine自己不创建和管理线程，Flutter Engine线程的创建和管理是Embeder负责的，Embeder指的是将引擎移植到平台的中间层代码，Flutter Engine层的架构示意图如下图所示。
>
> 在Flutter的架构中，Embeder提供四个Task Runner，分别是Platform Task Runner、UI Task Runner Thread、GPU Task Runner和IO Task Runner，每个Task Runner负责不同的任务，Flutter Engine不在乎Task Runner运行在哪个线程，但是它需要线程在整个生命周期里面保持稳定。

**38**、介绍下**Flutter**的理念架构

> ![Framework.webp](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/059ea661a3b84e76922ee4253b3f617e~tplv-k3u1fbpfcp-zoom-in-crop-mark:1512:0:0:0.awebp?)
>
> 由上图可知，Flutter框架自下而上分为Embedder、Engine和Framework三层。其中，Embedder是操作系统适配层，实现了渲染 Surface设置，线程设置，以及平台插件等平台相关特性的适配；Engine层负责图形绘制、文字排版和提供Dart运行时，Engine层具有独立虚拟机，正是由于它的存在，Flutter程序才能运行在不同的平台上，实现跨平台运行；Framework层则是使用Dart编写的一套基础视图库，包含了动画、图形绘制和手势识别等功能，是使用频率最高的一层。

**39. Future**和**Isolate**有什么区别？

> future是异步编程，调用本身立即返回，并在稍后的某个时候执行完成时再获得返回结果。在普通代码中可以使用await 等待一个异步调用结束。
>
> isolate是并发编程，Dartm有并发时的共享状态，所有Dart代码都在isolate中运行，包括最初的main()。每个isolate都有它自己的堆内存，意味着其中所有内存数据，包括全局数据，都仅对该isolate可见，它们之间的通信只能通过传递消息的机制完成，消息则通过端口(port)收发。isolate只是一个概念，具体取决于如何实现，比如在Dart VM中一个isolate可能会是一个线程，在Web中可能会是一个Web Worker。

**40**、什么是**Navigator? MaterialApp**做了什么？

> Navigator是在Flutter中负责管理维护页面堆栈的导航器。MaterialApp在需要的时候，会自动为我们创建Navigator。Navigator.of(context)，会使用context来向上遍历Element树，找到MaterialApp提供的_NavigatorState再调用其push/pop方法完成导航操作。
