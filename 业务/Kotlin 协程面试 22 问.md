# Q1: 什么是并发？

在巩固学习协程的相关知识之前，这是必须要知道的问题

并发程序是具备以下特点的：

- 看起来像是同时执行的多个任务
- 并发任务可以是完全独立的，也可以具有按特定顺序工作的相互依赖性。

为什么说是看起来像是同时执行的多个任务呢？打个比方吧，射雕英雄传应该看过吧，周伯通教郭靖一手画圆，一手画方，两只手同时操作，左右互搏，这个是并行；但是呢，我先左手画一笔，右手画一笔，同一时候只有一只手在操作，来回交替，直到完成图案，这个就是并发。

# Q2：什么是结构化并发？

将并发任务放到同一个作用域里，做到统一启动，统一关闭。结构化并发有以下好处：

任务需要执行的时候，可以继续跟踪，任务不需要执行的时候，可以取消，做到随叫随到 当任务失败时，可以发出错误信号表明有错误发生 统一处理并发任务，避免任务泄漏

协程就是用于将并发引入 Kotlin 应用程序的框架，并且协程对于结构化并发是完全支持。

# Q3：对于Kotlin中的协程有什么理解？

每次看到这个问题的时候,看到官方的回答大多数基本是都是这样的：协程视为一种轻量级线程，可用于提高并发代码的性能。这样的一句解释，看到是不是一头雾水。

首先，我们可以先尝试着理解下Kotlin官网说的这段话

> 可以将协程视为一种轻量级线程。和线程一样，协程可以并行运行，相互等待和通信。最大的区别是协程非常便宜，几乎是免费的：我们可以创建成千上万个协程，并且在性能方面支付的费用很少。另一方面，真正的线程的启动和维护成本很高。一千个线程对于现代机器来说可能是一个严峻的挑战。

其实这个回答对笔者来说是挺抽象的，不是特别好理解，所以就重新梳理下，首先我们从协程的英文名词来拆解下

**Coroutines = Co + Rountines**

这里 `Co` 指的是合作，而 `Routines` 代表的是电脑执行的一些例行程式，什么意思呢？就是意味着当这些函数程式相互协作的时候，我们就称之为协程。

![img](https://files.mdnice.com/user/14085/45556452-6dfa-48a3-b8e9-f9d8a9f1f632.png)

通过上面这张图，笔者用一个例子来方便自己理解，为了更直观的感受协程的魅力，这里使用了when关键字进行辅助。假设有两个函数它们分别是functionA和functionB

functionA如下代码所示：

```
fun functionA(case: Int) {
    when (case) {
        1 -> {
            taskA1()
            functionB(1)
        }
        2 -> {
            taskA2()
            functionB(2)
        }
        3 -> {
            taskA3()
            functionB(3)
        }
        4 -> {
            taskA4()
            functionB(4)
        }
    }
}
```

functionB如下代码所示：

```
fun functionB(case: Int) {
    when (case) {
        1 -> {
            taskB1()
            functionA(2)
        }
        2 -> {
            taskB2()
            functionA(3)
        }
        3 -> {
            taskB3()
            functionA(4)
        }
        4 -> {
            taskB4()
        }
    }
}
```

然后，我们调用functionA，此时会发生什么事呢？

```
functionA(1)
```

在这里，functionA将执行taskA1 并交给functionB控制执行taskB1；然后，functionB将执行taskB1并将控制权交还给functionA执行taskA2等等，如此类推下去，重要的是，functionA与functionB彼此合作。

现在我们使用Kotlin协程就可以非常轻松地完成上述工作，而无需使用例子所示的when, 只是方便自己理解。现在，我们可以暂时的理解为协程就是函数之间的相互协作，由于这些功能的协作性质，存在着无限的可能性。

- 它可以执行几行 functionA，然后执行几行 functionB，然后再执行几行 functionA，依此类推。**当一个线程处于空闲状态并且什么都不做时，这将很有帮助，在这种情况下，它可以执行另一个函数的几行。这样，它就可以充分利用线程，有助于多任务处理**
- 支持以同步的方式编写异步代码

总而言之，协程让多任务处理变得非常简单，可以说协程和线程都是多任务的，但不同的是，线程由操作系统管理，协程由用户管理，因为它拥有可以利用协作执行几行代码的功能。简单来说，协程就是一个基于实际编写的优化框架，利用函数的协作特性使其轻巧而强大。所以，我们总是说协程是一个轻量级的线程，这也意味着，它不映射到本机线程，因此不需要在处理器上进行上下文切换，因此协程速度更快。

可能有些同学已经注意到上面我所说的，”不映射到本机线程“，这是什么意思呢？一般来说,基本上有两种类型的协程

- 无堆叠
- 堆积如山的

而Kotlin实现的是无堆栈的协程，说明协程没有自己的堆栈，因此它们不会映射到本机线程。现在，我们反过头来理解Kotlin官网说的定义，才真正明白，协程并没有取代线程，它其实更像是一个框架来管理着它们。

综上所述，笔者对协程(Coroutines)有了更加确切的理解：它是一种更高效和更简单的方式管理并发的框架，其轻量级线程编写在实际线程框架之上，通过利用函数的协作性质来充分利用它。

# Q4: 协程比线程更高效的原因是什么？

协程比线程更高效，因为它们是轻量级的，可以挂起和恢复而不会产生上下文切换的开销。这意味着它们可用于执行否则会阻塞线程的任务，而不会导致相同的性能损失。这句话是什么意思呢？我们都知道，线程是操作系统管理的，而协程不是被操作系统内核所管理，而完全是由程序所控制（也就是在用户态执行），这样带来的好处就是性能得到了很大的提升，不会像线程切换那样消耗资源。稍微总结下，大致就三个特点：

- 协程是轻量级的，创建一个线程栈大概需要1M左右，而一个协程栈大概只需要几K或者几十K
- 减少了线程切换的成本，协程可以挂起和恢复，它不会产生额外的开销，由程序自身控制
- 不需要多线程的锁机制：因为只有一个线程，也不存在同时写变量冲突，在协程中控制共享资源不加锁，只需要判断状态就好了，所以执行效率比多线程高很多。

# Q5: 协程框架中主要组成部分？

协程框架大致有如下部分组成：

- 协程作用域(CoroutineScope)
- 协程上下文(CoroutineContext)
- 协程调度器(CoroutineDispatcher)
- 作业(Job)

以上其实是基于整个协程框架来细化的，如果基于语言层面来说的话，协程中的提供的标准库，一些拦截器，以及非常重要的挂起函数都可以归类到它的组成部分当中，如下图所示

![img](https://files.mdnice.com/user/14085/d6f5f521-d156-4e93-9f22-f72df5b539ca.png)

# Q6: 关于协程作用域CoroutineScope?

我们可以这么理解, CoroutineScope是一种用于启动协程的盒子。这就是为什么我们需要它来启动任何协程。因为它是一个盒子，我们可以同时对盒子里的所有协程执行操作，比如一次性取消盒子里的所有子协程。

它在我们实际开发中非常有用，因为我们需要Activity被销毁后立即取消后台任务。一般来说，我们都是通过考虑Activity、ViewModel等的生命周期而创建的自定义范围作用域Scope。

#### Activity范围示例

假设我们是在一个Activity中，并且一旦这个Activity被销毁掉，那么我们的后台任务也随之被取消。在Activity中，我们一般使用lifecycleScope来启动协程

```
class MainActivity : AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)

        lifecycleScope.launch {
            val user = fetchUser()
            // show user
        }

    }
    suspend fun fetchUser(): User {
        return withContext(Dispatchers.IO) {
            // fetch user
            // return user
        }
    }
}
```

一旦 Activity 被销毁，如果它正在运行，任务将被取消，因为我们已经使用了绑定到Activity 的 LifeCycle 的范围。

#### ViewModel范围示例

假设我们的ViewModel是作用域，一旦ViewModel被销毁，后台任务就应该被取消。在ViewModel中，

我们一般使用viewModelScope来启动协程

```
class MainViewModel : ViewModel() {

    fun fetch() {
        viewModelScope.launch {
            val user = fetchUser()
            // show user
        }
    }

    suspend fun fetchUser(): User {
        return withContext(Dispatchers.IO) {
            // fetch user
            // return user
        }
    }
}
```

一旦ViewModel被销毁，如果任务正在运行，它就会被取消，因为我们已经使用了绑定到 ViewModel 的生命周期的范围。

# Q7: CoroutinesScope取消和取消CoroutinesScope的子取消有什么区别？

- 一旦CoroutinesScope被取消了，我们就不能再从该范围创建新的协程，它不会给出任何错误/异常，只是默默地失败了
- 如果取消了Scopes children的时候，可以再次创建一个新的Children Coroutine并启动它

# Q8: 解释协程中的调度程序Dispatcher？

它是在特定线程或线程组上执行协程的必要步骤。并且单个Coroutine 可以使用多个CoroutineDispatcher。什么意思呢？简单来说就是它们作为调度员负责将协程”分派“到底层线程，它决定着协程内部的代码将在哪个线程上执行。

### **Dispatchers.Main & Dispatchers.Main.immediate**

Dispatchers.Main 和 Dispatchers.Main.immediate 在 Android应用程序的 UI（主）线程上执行代码。有人就会问了，它们都是在UI线程上执行代码，那么它们之间有什么不同么？

我们做个类比，`Dispatchers.Main.immediate` 的行为类似于 `Activity.runOnUiThread(…)`，而 `Dispatchers.Main` 的行为就类似于 `Handler(Looper.getMainLooper()).post(...)`，也就是说 runOnUiThread 在UI线程上运行指定的操作，如果当前线程是UI线程的话，该操作会立即执行，否则相关操作会被投递到UI线程的事件队列中去，这就是它与Handler的不同之处。

#### Dispatchers.Default & Dispatchers.IO

Dispatchers.Default和Dispatchers.IO都可以允许在后台执行任务

- Dispatchers.Default 由线程池支持，最大线程数为 2 或 CPU 核心数。它可以用于计算密集型任务。
- Dispatchers.IO 类似于Default，但最大线程数为 64 或 CPU 核心数。通过调整系统属性可以进一步增加最大线程数。用于 IO 任务，例如大部分时间都处于等待的工作，而非密集型。

#### Dispatchers.unconfined

简单来说，它只是在调用启动函数的线程上执行代码，并且它会立即执行。

# Q9: 关于协程中的作业Job?

根据官方文档，Job的定义是这样的:

> 作业是一个可取消的事物，其生命周期在其完成时达到顶峰。协程作业是通过启动协程构建器创建的。它运行指定的代码块并在该块完成时完成。

每个协程都与一个作业相关联。每当启动新协程时，它都会返回对作业的引用。协程的作业是可取消的，取消它会取消协程本身。但是如果我们想处理范围内的所有协程，就不再需要通过单独的作业来完成，我们可以使用CoroutineScope。

同样的，在日常开发中，我们可以通过Job提供的一些接口函数来控制协程，主要如下：

#### start() 开始

start()函数很直接，就是用来启动协程，这里就不过多描述

#### join() 加入

*join()*函数是一个挂起函数，即它可以从协程或另一个挂起函数中调用。作业阻塞所有线程，直到写入它的协程或上下文完成其工作。只有当协程完成时，才会执行join()函数之后的行。

#### cancel() 关闭

cancel()方法用于取消协程，而不用等待它完成它的工作。可以说它与join方法正好相反，在某种意义上，*join()方法等待协程完成其全部工作并阻塞所有其他线程，而cancel()*方法在遇到时杀死协程协程（即停止协程）。

# Q10: 关于协程中的SupervisorJob?

对于SupervisorJob其实和协程中的普通Job非常类似，唯一的区别在于如果子协程出现了异常，不会导致父协程以及其他兄弟协程取消关闭。

![img](https://files.mdnice.com/user/14085/12d871d0-95f9-4162-b4bc-3863e970842b.png)简单举个例子：

```
val supervisorJob = SupervisorJob()
val scope = CoroutineScope(Dispatchers.IO + supervisorJob)

val job1 = scope.launch { 
              while(isActive) {
                 delay(2000)
              }
           }

val job2 = scope.launch {
              throw Exception()
            }

val job3 = scope.launch { 
              while(isActive) {
                 delay(2000)
              }
           }
```

可以看到我们使用SupervisorJob作为作用域，启动了三个协程。第二个协程抛出异常，在此事件期间，其他协程不受影响并继续执行

综上所述，SupervisorJob更适合干一些独立互相不影响的任务，这样一旦某个任务出现了问题，对其他任务是没有任何影响的，比如说日常开发中一些UI需求，如果我点击的一个按钮出现了异常，但并不会影响手机状态栏的刷新

# Q11：简单说说suspend挂起函数？

从字面意思上理解，可以启动、暂停然后恢复的函数称为挂起函数。

关于挂起函数要记住的最重要的事情之一是它们只能从另一个挂起函数或在协程中调用。挂起函数只是标准的Kotlin函数加上了suspend修饰符，表示它们可以在不阻塞当前线程的情况下挂起协程执行。这意味着我们正在查看的代码可能会在调用暂停函数时暂停执行，并在稍后重新开始执行，但是需要注意的是，它没有提及与此同时当前线程会发生什么。

日常开发中，我们经常使用的 `delay()` 函数就是一个典型的挂起函数，我们尝试从协程外部调用 `delay()` 函数，会发生什么呢？直接会抛出如下错误：

![img](https://files.mdnice.com/user/14085/360a46c3-2fc9-4d52-8377-080edf067ecb.png)

由于dely函数本身就是一个挂起函数，我们需要在一个协程中或者在另一个挂起函数中才能调用`delay()`函数，它在不阻塞线程的情况下将协程延迟给定时间，并在指定时间后恢复，所以我们可以这么写：

```
 GlobalScope.launch(Dispatchers.Main) {
            delay(5000L)
        }
 suspend fun doDelayTask(time: Long) {
        delay(time)
        Log.d("Test","start")
    }
```

值得注意的是，挂起函数在执行完成之后，协程会重新切回它原先的线程。

# Q12: 从另一个挂起函数调用一个挂起函数会发生什么？

首先，我们要理清两个概念：

- 挂起，就是一个稍后会被自动切回来的线程调度操作
- 阻塞，其实是线程中的概念，相当于我线程卡了，或者在主线中进行一些耗时的操作，你必须等待耗时任务结束才能继续执行，这就是我们人为认知的卡顿

两个概念，它们最大的区别就是协程中的挂起是非阻塞式的，只是它能用看起来阻塞的代码写出非阻塞的操作，简单来说就是可以自动来回的切线程，从而不会造成主线程的阻塞

他们会造成什么影响呢？我们试着直接从主线程中下载一百张图片然后显示界面列表中，这一看就是耗时操作吧，我们必须拿到图片后去再刷新界面UI，**在图形化 GUI 系统中 , 一般都在主线程中更新 UI , 主线程中都有一个无限循环 , 不断刷新界面，所以我们也叫做UI线程，** 这时候主线程中执行了耗时操作，就会影响到界面刷新，出现掉帧，甚至直接ANR了；那如果我们将下载操作使用协程挂起了呢，在这段等待的时间内是不会影响UI刷新操作的，直到拿到结果再自动切换到UI线程去刷新界面数据

# Q14: 启动协程的launch() 和 async() 有什么区别？在某些情况下应该使用哪个？

launch() 和 async() 之间的主要区别在于 :

- launch() 将创建一个新的协程并立即启动它
- async() 将创建一个新的协程但不会启动它直到某些东西在结果Deferred 上调用 await()

一般来说，当我们想让协程在后台运行而不阻塞主线程时，应该使用launch() ，而当我们需要等待协程的结果再继续时，应该使用 async() ，不够具体？launch 更多是用来发起一个无需结果的耗时任务（如批量文件删除、创建），这个工作不需要返回结果。async 函数则是更进一步，用于异步执行耗时任务，并且需要返回值（如网络请求、数据库读写、文件读写），在执行完毕通过await() 函数获取返回值。

如何选择这两个函数就看我们自己业务的需求啦，比如只是需要切换协程执行耗时任务，就用launch函数。如果想把原来的回调式的异步任务用协程的方式实现，就用async函数。

# Q15: 区分 Kotlin 中的 launch / join 和 async / await

#### launch/join：

launch用于启动和停止协程。如果launch 中的代码抛出异常，它会被视为线程中的未捕获异常，通常会在JVM程序中写入 stderr 并导致 Android 应用程序崩溃。join 用于在传播其异常之前等待启动的协程完成。另一方面，崩溃的子协程会用匹配的异常取消其父协程。

#### async/await：

async 关键字用于启动计算返回结果的协程。我们必须对结果使用 await，它由Deferred 的实例表示。异步代码中未捕获的异常保存在生成的 Deferred中，不会传输到其他任何地方。它们在处理之前不会被执行。

# Q16: 协程中的 GlobalScope 以及为什么要避免它？

通常，不鼓励使用GlobalScope。知道为什么吗？可以看下Kotlin官方对于全局作用域的中定义：

> “全局作用域用于启动在整个应用程序生命周期内运行且不会过早取消的顶级协程。”

GlobalScope 创建全局协程，这些协程不是某个特定范围的子级。因此，开发人员有责任跟踪所有此类全局协程并在完成工作后销毁它们。这种手动维护协程生命周期的负担可能会导致开发人员付出额外的努力。此外，如果处理不当，很可能会导致内存泄漏。所以在日常开发中应避免使用GlobalScope。

然而，正如我们所见，所有协程都必须在某个协程范围内创建。那么，推荐的方式是什么？

> 正如Kotlin 的CoroutineScope 文档中提到的那样，获取范围的独立实例的最佳方法是CoroutineScope 和 MainScope 工厂。

# Q17: 如果协程内部抛出异常会怎么样?

如果在协程中抛出异常，则协程将被取消。协程的所有子程序也将被取消，并且这些协程中的任何未完成的工作都将丢失。

# Q18: CoroutineScope.launch {} 中的异常如何工作？

假设我们从一个CoroutineScope作用域中启动了 3 个协程

![img](https://files.mdnice.com/user/14085/a1b22645-94ec-40c8-b693-c000fa2fa18c.png)

在这里，Coroutine3抛出一个使用launch {} 构建器的异常

![img](https://files.mdnice.com/user/14085/282f5caf-dd11-4432-b428-5e8b24f1a7c4.png)

然后Coroutine3会被取消

![img](https://files.mdnice.com/user/14085/44b7cb09-07a2-4daf-b26f-9b5c149fbca5.png)

这个取消操作最终会被传输到CoroutineScope，那么它也将取消关闭

![img](https://files.mdnice.com/user/14085/192c064e-9220-4489-a623-2bf2d90b2e93.png)

我们都知道，如果CoroutineScope被取消的话，那么它的所有子协程也会被取消

并且这个时候异常也会传播到异常处理程序当中，我们可以添加自定义的异常处理程序，默认情况下协程会提供一个异常处理程序，这个默认的异常处理程序会导致应用程序崩溃。

![img](https://files.mdnice.com/user/14085/d8bd3b99-7f44-406b-97bb-9d1c254d25f9.png)

# Q19: CoroutineScope.async {} 中的异常如何工作？

同样的假设我们从一个CoroutineScope启动了 3 个协程

![img](https://files.mdnice.com/user/14085/3f475502-c79c-44ee-a17b-002b91e37513.png)

在这里，Coroutine3 抛出一个使用 async {} 构建器的异常

![img](https://files.mdnice.com/user/14085/4eb6c61a-3186-4475-9722-668e9efbc29e.png)

然后Coroutine3同样会被取消

![img](https://files.mdnice.com/user/14085/cfe91799-3d1b-4901-952f-5322593b8372.png)

这个取消操作最终会被传输到CoroutineScope，那么它也将取消关闭

![img](https://files.mdnice.com/user/14085/fbb9c611-f2b5-4580-baa0-881532eac59e.png)

我们都知道，如果CoroutineScope被取消的话，那么它的所有子协程也会被取消

但是和launch不同的是，它抛出的异常不会委托给协程异常处理程序。相反，只要我们调用*Deferred.await()*函数，异常就会被重新抛出，在这种情况下不会调用协程异常处理程序。

![img](https://files.mdnice.com/user/14085/8a712310-9d35-40fa-abdb-3fc959cd1fbb.png)

# Q20: 平常使用协程时有碰到哪些错误？

这个问题需要结合实际项目中阐述，一般来说，每个人所遇到的问题不尽相同，所以也会有不同的想法；

这里笔者只是罗列出日常开发中一些使用协程出现的常见错误：

- 我们在启动协程的时候没有使用正确的上下文,导致协程在不应该取消的时候被取消，或者无法访问我们需要的数据
- 没有使用结构化并发方法，这可能导致竞争条件和其他一些问题
- 使用try/catch来捕获协程的异常

# Q21: 使用 Kotlin 协程时有哪些好的做法可以遵循？

一般来说我们使用协程时候有一些良好的做法，当然，具体要开发者在实际开发中自行体会。下面简单罗列下:

将协程用于短期后台任务 将协程用于可以并行执行的任务 将协程用于需要在与 UI 线程不同的线程上执行的任务 不要将协程用于需要执行的任务在 UI 线程上 不要对需要同步执行的任务使用协程

# Q22: Kotlin协程比Rxjava/RxKotlin好在哪里?

就目前日常开发来说，一些比较新的项目都采用协程而不再使用Rxjava/Kotlin来处理异步问题，原因有两点：

- 协程作为一个线程管理框架，它编写代码更加简洁，对于已经熟悉面向对象编程和并发/多线程/异步的同学来说，它们非常直观，同时协程已经提供了并发和结构化并发的简单实现，易于维护和扩展，有效提高编码效率；反观Rxjava /RxKotlin，作为响应式编程框架，同样可以优化代码以提高应用程序的响应能力，非常容易扩展，但由于过度使用它而很难维护，而且因为它的复杂性不是那么好上手，使得代码非常复杂，更难调试，那开发同学如果不是特别熟悉Rxjava的话，就要花更多时间去解决问题，因为它不会返回错误，而且唯一的调试方法非常原始。
- 其次就是在性能方面，Coroutines 比 RxJava更高效，因为它使用更少的资源来执行相同的任务，同时执行速度更快。RxJava使用更多的内存并需要更多的 CPU时间，这转化为更高的电池消耗和用户可能的UI中断。