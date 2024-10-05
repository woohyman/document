### 1、你项目中用到哪些开源库？说说其实现原理？

#### 一、[网络底层框架：OkHttp实现原理](https://juejin.im/post/5e1be39b6fb9a02fcd130d1f)

##### 这个库是做什么用的？

网络底层库，它是基于http协议封装的一套请求客户端，虽然它也可以开线程，但根本上它更偏向真正的请求，跟HttpClient, HttpUrlConnection的职责是一样的。其中封装了网络请求get、post等底层操作的实现。

##### 为什么要在项目中使用这个库？

*   OkHttp 提供了对最新的 HTTP 协议版本 HTTP/2 和 SPDY 的支持，这使得对同一个主机发出的所有请求都可以共享相同的套接字连接。
*   如果 HTTP/2 和 SPDY 不可用，OkHttp 会使用连接池来复用连接以提高效率。
*   OkHttp 提供了对 GZIP 的默认支持来降低传输内容的大小。
*   OkHttp 也提供了对 HTTP 响应的缓存机制，可以避免不必要的网络请求。
*   当网络出现问题时，OkHttp 会自动重试一个主机的多个 IP 地址。

##### 这个库都有哪些用法？对应什么样的使用场景？

get、post请求、上传文件、上传表单等等。

##### 这个库的优缺点是什么，跟同类型库的比较？

*   优点：在上面
*   缺点：使用的时候仍然需要自己再做一层封装。

##### 这个库的核心实现原理是什么？如果让你实现这个库的某些核心功能，你会考虑怎么去实现？

OkHttp内部的请求流程：使用OkHttp会在请求的时候初始化一个Call的实例，然后执行它的execute()方法或enqueue()方法，内部最后都会执行到getResponseWithInterceptorChain()方法，这个方法里面通过拦截器组成的责任链，依次经过用户自定义普通拦截器、重试拦截器、桥接拦截器、缓存拦截器、连接拦截器和用户自定义网络拦截器以及访问服务器拦截器等拦截处理过程，来获取到一个响应并交给用户。其中，除了OKHttp的内部请求流程这点之外，缓存和连接这两部分内容也是两个很重要的点，掌握了这3点就说明你理解了OkHttp。

##### 各个拦截器的作用：

*   interceptors：用户自定义拦截器
*   retryAndFollowUpInterceptor：负责失败重试以及重定向
*   BridgeInterceptor：请求时，对必要的Header进行一些添加，接收响应时，移除必要的Header
*   CacheInterceptor：负责读取缓存直接返回（根据请求的信息和缓存的响应的信息来判断是否存在缓存可用）、更新缓存
*   ConnectInterceptor：负责和服务器建立连接

ConnectionPool：

1、判断连接是否可用，不可用则从ConnectionPool获取连接，ConnectionPool无连接，创建新连接，握手，放入ConnectionPool。

2、它是一个Deque，add添加Connection，使用线程池负责定时清理缓存。

3、使用连接复用省去了进行 TCP 和 TLS 握手的一个过程。

*   networkInterceptors：用户定义网络拦截器
*   CallServerInterceptor：负责向服务器发送请求数据、从服务器读取响应数据

##### 你从这个库中学到什么有价值的或者说可借鉴的设计思想？

使用责任链模式实现拦截器的分层设计，每一个拦截器对应一个功能，充分实现了功能解耦，易维护。

##### 手写拦截器？

##### OKhttp针对网络层有哪些优化？

##### 网络请求缓存处理，okhttp如何处理网络缓存的？

##### HttpUrlConnection 和 okhttp关系？

##### Volley与OkHttp的对比：

Volley：支持HTTPS。缓存、异步请求，不支持同步请求。协议类型是Http/1.0, Http/1.1，网络传输使用的是 HttpUrlConnection/HttpClient，数据读写使用的IO。
OkHttp：支持HTTPS。缓存、异步请求、同步请求。协议类型是Http/1.0, Http/1.1, SPDY, Http/2.0, WebSocket，网络传输使用的是封装的Socket，数据读写使用的NIO（Okio）。
SPDY协议类似于HTTP，但旨在缩短网页的加载时间和提高安全性。SPDY协议通过压缩、多路复用和优先级来缩短加载时间。

Okhttp的子系统层级结构图如下所示：

![image](https://github.com/guoxiaoxing/android-open-framwork-analysis/raw/master/art/okhttp/okhttp_structure.png)

网络配置层：利用Builder模式配置各种参数，例如：超时时间、拦截器等，这些参数都会由Okhttp分发给各个需要的子系统。
重定向层：负责重定向。
Header拼接层：负责把用户构造的请求转换为发送给服务器的请求，把服务器返回的响应转换为对用户友好的响应。
HTTP缓存层：负责读取缓存以及更新缓存。
连接层：连接层是一个比较复杂的层级，它实现了网络协议、内部的拦截器、安全性认证，连接与连接池等功能，但这一层还没有发起真正的连接，它只是做了连接器一些参数的处理。
数据响应层：负责从服务器读取响应的数据。
在整个Okhttp的系统中，我们还要理解以下几个关键角色：

OkHttpClient：通信的客户端，用来统一管理发起请求与解析响应。
Call：Call是一个接口，它是HTTP请求的抽象描述，具体实现类是RealCall，它由CallFactory创建。
Request：请求，封装请求的具体信息，例如：url、header等。
RequestBody：请求体，用来提交流、表单等请求信息。
Response：HTTP请求的响应，获取响应信息，例如：响应header等。
ResponseBody：HTTP请求的响应体，被读取一次以后就会关闭，所以我们重复调用responseBody.string()获取请求结果是会报错的。
Interceptor：Interceptor是请求拦截器，负责拦截并处理请求，它将网络请求、缓存、透明压缩等功能都统一起来，每个功能都是一个Interceptor，所有的Interceptor最 终连接成一个Interceptor.Chain。典型的责任链模式实现。
StreamAllocation：用来控制Connections与Streas的资源分配与释放。
RouteSelector：选择路线与自动重连。
RouteDatabase：记录连接失败的Route黑名单。

##### 自己去设计网络请求框架，怎么做？

##### 从网络加载一个10M的图片，说下注意事项？

##### http怎么知道文件过大是否传输完毕的响应？

##### 谈谈你对WebSocket的理解？

##### WebSocket与socket的区别？

#### 二、[网络封装框架：Retrofit实现原理](https://juejin.im/post/5e1fb9386fb9a0300a4501a6)

##### 这个库是做什么用的？

Retrofit 是一个 RESTful 的 HTTP 网络请求框架的封装。Retrofit 2.0 开始内置 OkHttp，前者专注于接口的封装，后者专注于网络请求的高效。

##### 为什么要在项目中使用这个库？

1、功能强大：

*   支持同步、异步
*   支持多种数据的解析 & 序列化格式
*   支持RxJava

2、简洁易用：

*   通过注解配置网络请求参数
*   采用大量设计模式简化使用

3、可扩展性好：

*   功能模块高度封装
*   解耦彻底，如自定义Converters

##### 这个库都有哪些用法？对应什么样的使用场景？

任何网络场景都应该优先选择，特别是后台API遵循Restful API设计风格 & 项目中使用到RxJava。

##### 这个库的优缺点是什么，跟同类型库的比较？

*   优点：在上面
*   缺点：扩展性差，高度封装所带来的必然后果，如果服务器不能给出统一的API形式，会很难处理。

##### 这个库的核心实现原理是什么？如果让你实现这个库的某些核心功能，你会考虑怎么去实现？

Retrofit主要是在create方法中采用动态代理模式（通过访问代理对象的方式来间接访问目标对象）实现接口方法，这个过程构建了一个ServiceMethod对象，根据方法注解获取请求方式，参数类型和参数注解拼接请求的链接，当一切都准备好之后会把数据添加到Retrofit的RequestBuilder中。然后当我们主动发起网络请求的时候会调用okhttp发起网络请求，okhttp的配置包括请求方式，URL等在Retrofit的RequestBuilder的build()方法中实现，并发起真正的网络请求。

##### 你从这个库中学到什么有价值的或者说可借鉴的设计思想？

内部使用了优秀的架构设计和大量的设计模式，在我分析过Retrofit最新版的源码和大量优秀的Retrofit源码分析文章后，我发现，要想真正理解Retrofit内部的核心源码流程和设计思想，首先，需要对它使用到的九大设计模式有一定的了解，下面我简单说一说：

1、创建Retrofit实例：

*   使用建造者模式通过内部Builder类建立了一个Retroift实例。
*   网络请求工厂使用了工厂方法模式。

2、创建网络请求接口的实例：

*   首先，使用外观模式统一调用创建网络请求接口实例和网络请求参数配置的方法。
*   然后，使用动态代理动态地去创建网络请求接口实例。
*   接着，使用了建造者模式 & 单例模式创建了serviceMethod对象。
*   再者，使用了策略模式对serviceMethod对象进行网络请求参数配置，即通过解析网络请求接口方法的参数、返回值和注解类型，从Retrofit对象中获取对应的网络的url地址、网络请求执行器、网络请求适配器和数据转换器。
*   最后，使用了装饰者模式ExecuteCallBack为serviceMethod对象加入线程切换的操作，便于接受数据后通过Handler从子线程切换到主线程从而对返回数据结果进行处理。

3、发送网络请求：

*   在异步请求时，通过静态delegate代理对网络请求接口的方法中的每个参数使用对应的ParameterHanlder进行解析。

4、解析数据

5、切换线程：

*   使用了适配器模式通过检测不同的Platform使用不同的回调执行器，然后使用回调执行器切换线程，这里同样是使用了装饰模式。

6、处理结果

##### Android：主流网络请求开源库的对比（Android-Async-Http、Volley、OkHttp、Retrofit）

<https://www.jianshu.com/p/050c6db5af5a>

#### 三、[响应式编程框架：RxJava实现原理](https://juejin.im/post/5e4c9d45518825496e7847b1)

##### RxJava到底是什么？

RxJava是基于Java虚拟机上的响应式扩展库，它通过**使用可观察的序列将异步和基于事件的程序组合起来**。
与此同时，它**扩展了观察者模式来支持数据/事件序列**，并且添加了操作符，这些**操作符允许你声明性地组合序列**，同时抽象出要关注的问题：比如低级线程、同步、线程安全和并发数据结构等。

##### 为什么多次执行subscribeOn()，只有第一次有效？

从上面的分析，我们可以很容易了解到**被观察者被订阅时是从最外面的一层（ObservableSubscribeOn）通知到里面的一层（ObservableOnSubscribe）**，当连续执行了到多次subscribeOn()的时候，其实就是先执行倒数第一次的subscribeOn()方法，直到最后一次执行的subscribeOn()方法，这样肯定会覆盖前面的线程切换。

##### RxJava 变换操作符 map flatMap concatMap buffer？

*   map：【数据类型转换】将被观察者发送的事件转换为另一种类型的事件。
*   flatMap：【化解循环嵌套和接口嵌套】将被观察者发送的事件序列进行拆分 & 转换 后合并成一个新的事件序列，最后再进行发送。
*   concatMap：【有序】与 flatMap 的 区别在于，拆分 & 重新合并生成的事件序列 的顺序与被观察者旧序列生产的顺序一致。
*   buffer：定期从被观察者发送的事件中获取一定数量的事件并放到缓存区中，然后把这些数据集合打包发射。

##### [RxJava中map和flatmap操作符的区别及底层实现](https://www.jianshu.com/p/af13a8278a05)

##### 手写rxjava遍历数组。

##### 你认为Rxjava的线程池与你们自己实现任务管理框架有什么区别？

#### 四、[图片加载框架：Glide实现原理](https://juejin.im/post/5e2109e25188254c257c40c6)

##### 这个库是做什么用的？

Glide是Android中的一个图片加载库，用于实现图片加载。

##### 为什么要在项目中使用这个库？

1、多样化媒体加载：不仅可以进行图片缓存，还支持Gif、WebP、缩略图，甚至是Video。

2、通过设置绑定生命周期：可以使加载图片的生命周期动态管理起来。

3、高效的缓存策略：支持内存、Disk缓存，并且Picasso只会缓存原始尺寸的图片，内Glide缓存的是多种规格，也就是Glide会根据你ImageView的大小来缓存相应大小的图片尺寸。

4、内存开销小：默认的Bitmap格式是RGB\_565格式，而Picasso默认的是ARGB\_8888格式，内存开销小一半。

##### 这个库都有哪些用法？对应什么样的使用场景？

1、图片加载：Glide.with(this).load(imageUrl).override(800, 800).placeholder().error().animate().into()。

2、多样式媒体加载：asBitamp、asGif。

3、生命周期集成。

4、可以配置磁盘缓存策略ALL、NONE、SOURCE、RESULT。

##### 这个库的优缺点是什么，跟同类型库的比较？

库比较大，源码实现复杂。

##### 这个库的核心实现原理是什么？如果让你实现这个库的某些核心功能，你会考虑怎么去实现？

*   Glide\&with：

1、初始化各式各样的配置信息（包括缓存，请求线程池，大小，图片格式等等）以及glide对象。

2、将glide请求和application/SupportFragment/Fragment的生命周期绑定在一块。

*   Glide\&load：

设置请求url，并记录url已设置的状态。

3、Glide\&into：

1、首先根据转码类transcodeClass类型返回不同的ImageViewTarget：BitmapImageViewTarget、DrawableImageViewTarget。

2、递归建立缩略图请求，没有缩略图请求，则直接进行正常请求。

3、如果没指定宽高，会根据ImageView的宽高计算出图片宽高，最终执行到onSizeReay()方法中的engine.load()方法。

4、engine是一个负责加载和管理缓存资源的类

*   常规三级缓存的流程：强引用->软引用->硬盘缓存

当我们的APP中想要加载某张图片时，先去LruCache中寻找图片，如果LruCache中有，则直接取出来使用，如果LruCache中没有，则去SoftReference中寻找（软引用适合当cache，当内存吃紧的时候才会被回收。而weakReference在每次system.gc（）就会被回收）（当LruCache存储紧张时，会把最近最少使用的数据放到SoftReference中），如果SoftReference中有，则从SoftReference中取出图片使用，同时将图片重新放回到LruCache中，如果SoftReference中也没有图片，则去硬盘缓存中中寻找，如果有则取出来使用，同时将图片添加到LruCache中，如果没有，则连接网络从网上下载图片。图片下载完成后，将图片保存到硬盘缓存中，然后放到LruCache中。

*   Glide的三层缓存机制：

Glide的缓存机制，主要分为2种缓存，一种是内存缓存，一种是磁盘缓存。之所以使用内存缓存的原因是：防止应用重复将图片读入到内存，造成内存资源浪费。之所以使用磁盘缓存的原因是：防止应用重复的从网络或者其他地方下载和读取数据。正式因为有着这两种缓存的结合，才构成了Glide极佳的缓存效果。

Glide缓存机制大致分为三层：内存缓存、弱引用缓存、磁盘缓存。

取的顺序是：Lru 算法缓存（内存）、弱引用、磁盘。

存的顺序是：弱引用、Lru 算法缓存（内存）、磁盘。

三层存储的机制在Engine中实现的。先说下Engine是什么？Engine这一层负责加载时做管理内存缓存的逻辑。持有MemoryCache、Map\<Key, WeakReference\<EngineResource\<?>>>。通过load（）来加载图片，加载前后会做内存存储的逻辑。如果内存缓存中没有，那么才会使用EngineJob这一层来进行异步获取硬盘资源或网络资源。EngineJob类似一个异步线程或observable。Engine是一个全局唯一的，通过Glide.getEngine()来获取。

需要一个图片资源，如果Lrucache中有相应的资源图片，那么就返回，同时从Lrucache中清除，放到activeResources中。activeResources map是盛放正在使用的资源，以弱引用的形式存在。同时资源内部有被引用的记录。如果资源没有引用记录了，那么再放回Lrucache中，同时从activeResources中清除。如果Lrucache中没有，就从activeResources中找，找到后相应资源引用加1。如果Lrucache和activeResources中没有，那么进行资源异步请求（网络/diskLrucache），请求成功后，资源放到diskLrucache和activeResources中。

##### Glide源码机制的核心思想：

使用一个弱引用map activeResources来盛放项目中正在使用的资源。Lrucache中不含有正在使用的资源。资源内部有个计数器来显示自己是不是还有被引用的情况，把正在使用的资源和没有被使用的资源分开有什么好处呢？？因为当Lrucache需要移除一个缓存时，会调用resource.recycle()方法。注意到该方法上面注释写着只有没有任何consumer引用该资源的时候才可以调用这个方法。那么为什么调用resource.recycle()方法需要保证该资源没有任何consumer引用呢？glide中resource定义的recycle（）要做的事情是把这个不用的资源（假设是bitmap或drawable）放到bitmapPool中。bitmapPool是一个bitmap回收再利用的库，在做transform的时候会从这个bitmapPool中拿一个bitmap进行再利用。这样就避免了重新创建bitmap，减少了内存的开支。而既然bitmapPool中的bitmap会被重复利用，那么肯定要保证回收该资源的时候（即调用资源的recycle（）时），要保证该资源真的没有外界引用了。这也是为什么glide花费那么多逻辑来保证Lrucache中的资源没有外界引用的原因。

##### 你从这个库中学到什么有价值的或者说可借鉴的设计思想？

Glide的高效的三层缓存机制，如上。

##### Glide加载一个一兆的图片（100 \* 100），是否会压缩后再加载，放到一个300 \* 300的view上会怎样，800\*800呢，图片会很模糊，怎么处理？

当我们调整imageview的大小时，Picasso会不管imageview大小是什么，总是直接缓存整张图片，而Glide就不一样了，它会为每个不同尺寸的Imageview缓存一张图片，也就是说不管你的这张图片有没有加载过，只要imageview的尺寸不一样，那么Glide就会重新加载一次，这时候，它会在加载的imageview之前从网络上重新下载，然后再缓存。

举个例子，如果一个页面的imageview是300 \* 300像素，而另一个页面中的imageview是100 \* 100像素，这时候想要让两个imageview像是同一张图片，那么Glide需要下载两次图片，并且缓存两张图片。

```java
public <R> LoadStatus load() {
    // 根据请求参数得到缓存的键
    EngineKey key = keyFactory.buildKey(model, signature, width, height, transformations,
        resourceClass, transcodeClass, options);
}
```

可以看到，缓存Key的生成条件之一就是控件的长宽。

##### 如果在一个页面中使用Glide加载了一张图片，图片正在获取中，如果突然关闭页面，这个页面会造成内存泄漏吗？

因为Glide 在加载资源的时候，如果是在 Activity、Fragment 这一类有生命周期的组件上进行的话，会创建一个透明的 RequestManagerFragment 加入到FragmentManager 之中，感知生命周期，当 Activity、Fragment 等组件进入不可见，或者已经销毁的时候，Glide 会停止加载资源。但是如果，是在非生命周期的组件上进行时，会采用Application 的生命周期贯穿整个应用，所以 applicationManager 只有在应用程序关闭的时候终止加载。

##### 计算一张图片的大小

图片占用内存的计算公式：图片高度 \* 图片宽度 \* 一个像素占用的内存大小。所以，计算图片占用内存大小的时候，要考虑图片所在的目录跟设备密度，这两个因素其实影响的是图片的宽高，android会对图片进行拉升跟压缩。

##### 加载bitmap过程（怎样保证不产生内存溢出）

由于Android对图片使用内存有限制，若是加载几兆的大图片便内存溢出。Bitmap会将图片的所有像素（即长x宽）加载到内存中，如果图片分辨率过大，会直接导致内存OOM，只有在BitmapFactory加载图片时使用BitmapFactory.Options对相关参数进行配置来减少加载的像素。

BitmapFactory.Options相关参数详解：

(1).Options.inPreferredConfig值来降低内存消耗。

比如：默认值ARGB\_8888改为RGB\_565,节约一半内存。

(2).设置Options.inSampleSize 缩放比例，对大图片进行压缩 。

(3).设置Options.inPurgeable和inInputShareable：让系统能及时回收内存。

    A：inPurgeable：设置为True时，表示系统内存不足时可以被回收，设置为False时，表示不能被回收。
    
    B：inInputShareable：设置是否深拷贝，与inPurgeable结合使用，inPurgeable为false时，该参数无意义。

(4).使用decodeStream代替decodeResource等其他方法。

### Android中软引用与弱引用的应用场景。

Java 引用类型分类：

![img](https://segmentfault.com/img/remote/1460000042313867)

在 Android 应用的开发中，为了防止内存溢出，在处理一些占用内存大而且生命周期较长的对象时候，可以尽量应用软引用和弱引用技术。

*   1、软/弱引用可以和一个引用队列（ReferenceQueue）联合使用，如果软引用所引用的对象被垃圾回收器回收，Java 虚拟机就会把这个软引用加入到与之关联的引用队列中。利用这个队列可以得知被回收的软/弱引用的对象列表，从而为缓冲器清除已失效的软 / 弱引用。
*   2、如果只是想避免 OOM 异常的发生，则可以使用软引用。如果对于应用的性能更在意，想尽快回收一些占用内存比较大的对象，则可以使用弱引用。
*   3、可以根据对象是否经常使用来判断选择软引用还是弱引用。如果该对象可能会经常使用的，就尽量用软引用。如果该对象不被使用的可能性更大些，就可以用弱引用。

##### Android里的内存缓存和磁盘缓存是怎么实现的。

内存缓存基于LruCache实现，磁盘缓存基于DiskLruCache实现。这两个类都基于Lru算法和LinkedHashMap来实现。

LRU算法可以用一句话来描述，如下所示：

LRU是Least Recently Used的缩写，最近最少使用算法，从它的名字就可以看出，它的核心原则是如果一个数据在最近一段时间没有使用到，那么它在将来被访问到的可能性也很小，则这类数据项会被优先淘汰掉。

##### LruCache原理

之前，我们会使用内存缓存技术实现，也就是软引用或弱引用，在Android 2.3（APILevel 9）开始，垃圾回收器会更倾向于回收持有软引用或弱引用的对象，这让软引用和弱引用变得不再可靠。

其实LRU缓存的实现类似于一个特殊的栈，把访问过的元素放置到栈顶（若栈中存在，则更新至栈顶；若栈中不存在则直接入栈），然后如果栈中元素数量超过限定值，则删除栈底元素（即最近最少使用的元素）。

它的内部存在一个 LinkedHashMap 和 maxSize，把最近使用的对象用强引用存储在 LinkedHashMap 中，给出来 put 和 get 方法，每次 put 图片时计算缓存中所有图片的总大小，跟 maxSize 进行比较，大于 maxSize，就将最久添加的图片移除，反之小于 maxSize 就添加进来。

LruCache的原理就是利用LinkedHashMap持有对象的强引用，按照Lru算法进行对象淘汰。具体说来假设我们从表尾访问数据，在表头删除数据，当访问的数据项在链表中存在时，则将该数据项移动到表尾，否则在表尾新建一个数据项。当链表容量超过一定阈值，则移除表头的数据。

详细来说就是LruCache中维护了一个集合LinkedHashMap，该LinkedHashMap是以访问顺序排序的。当调用put()方法时，就会在结合中添加元素，并调用trimToSize()判断缓存是否已满，如果满了就用LinkedHashMap的迭代器删除队头元素，即近期最少访问的元素。当调用get()方法访问缓存对象时，就会调用LinkedHashMap的get()方法获得对应集合元素，同时会更新该元素到队尾。

##### LruCache put方法核心逻辑

在添加过缓存对象后，调用trimToSize()方法，来判断缓存是否已满，如果满了就要删除近期最少使用的对象。trimToSize()方法不断地删除LinkedHashMap中队头的元素，即近期最少访问的，直到缓存大小小于最大值（maxSize）。

##### LruCache get方法核心逻辑

当调用LruCache的get()方法获取集合中的缓存对象时，就代表访问了一次该元素，将会更新队列，保持整个队列是按照访问顺序排序的。

为什么会选择LinkedHashMap呢？

这跟LinkedHashMap的特性有关，LinkedHashMap的构造函数里有个布尔参数accessOrder，当它为true时，LinkedHashMap会以访问顺序为序排列元素，否则以插入顺序为序排序元素。

##### LinkedHashMap原理

LinkedHashMap 几乎和 HashMap 一样：从技术上来说，不同的是它定义了一个 Entry\<K,V> header，这个 header 不是放在 Table 里，它是额外独立出来的。LinkedHashMap 通过继承 hashMap 中的 Entry\<K,V>,并添加两个属性 Entry\<K,V> before,after,和 header 结合起来组成一个双向链表，来实现按插入顺序或访问顺序排序。

##### DisLruCache原理

DiskLruCache与LruCache原理相似，只是多了一个journal文件来做磁盘文件的管理，如下所示：

    libcore.io.DiskLruCache
    1
    1
    1
    
    DIRTY 1517126350519
    CLEAN 1517126350519 5325928
    REMOVE 1517126350519

注：这里的缓存目录是应用的缓存目录/data/data/pckagename/cache，未root的手机可以通过以下命令进入到该目录中或者将该目录整体拷贝出来：

    //进入/data/data/pckagename/cache目录
    adb shell
    run-as com.your.packagename 
    cp /data/data/com.your.packagename/
    
    //将/data/data/pckagename目录拷贝出来
    adb backup -noapk com.your.packagename

我们来分析下这个文件的内容：

第一行：libcore.io.DiskLruCache，固定字符串。
第二行：1，DiskLruCache源码版本号。
第三行：1，App的版本号，通过open()方法传入进去的。
第四行：1，每个key对应几个文件，一般为1.
第五行：空行
第六行及后续行：缓存操作记录。
第六行及后续行表示缓存操作记录，关于操作记录，我们需要了解以下三点：

DIRTY 表示一个entry正在被写入。写入分两种情况，如果成功会紧接着写入一行CLEAN的记录；如果失败，会增加一行REMOVE记录。注意单独只有DIRTY状态的记录是非法的。
当手动调用remove(key)方法的时候也会写入一条REMOVE记录。
READ就是说明有一次读取的记录。
CLEAN的后面还记录了文件的长度，注意可能会一个key对应多个文件，那么就会有多个数字。

##### Bitmap 压缩策略

加载 Bitmap 的方式：

BitmapFactory 四类方法：

*   decodeFile( 文件系统 )
*   decodeResourece( 资源 )
*   decodeStream( 输入流 )
*   decodeByteArray( 字节数 )

BitmapFactory.options 参数:

*   inSampleSize 采样率，对图片高和宽进行缩放，以最小比进行缩放（一般取值为 2 的指数）。通常是根据图片宽高实际的大小/需要的宽高大小，分别计算出宽和高的缩放比。但应该取其中最小的缩放比，避免缩放图片太小，到达指定控件中不能铺满，需要拉伸从而导致模糊。
*   inJustDecodeBounds 获取图片的宽高信息，交给  inSampleSize 参数选择缩放比。通过 inJustDecodeBounds = true，然后加载图片就可以实现只解析图片的宽高信息，并不会真正的加载图片，所以这个操作是轻量级的。当获取了宽高信息，计算出缩放比后，然后在将 inJustDecodeBounds = false,再重新加载图片，就可以加载缩放后的图片。

高效加载 Bitmap 的流程:

*   1、将 BitmapFactory.Options 的 inJustDecodeBounds 参数设为 true 并加载图片
*   2、从 BitmapFactory.Options 中取出图片原始的宽高信息，对应于 outWidth 和 outHeight 参数
*   3、根据采样率规则并结合目标 view 的大小计算出采样率 inSampleSize
*   4、将 BitmapFactory.Options 的 inJustDecodeBounds 设置为 false 重新加载图片

##### Bitmap的处理：

当使用ImageView的时候，可能图片的像素大于ImageView，此时就可以通过BitmapFactory.Option来对图片进行压缩，inSampleSize表示缩小2^(inSampleSize-1)倍。

BitMap的缓存：

1.使用LruCache进行内存缓存。

2.使用DiskLruCache进行硬盘缓存。

##### 实现一个ImageLoader的流程

同步异步加载、图片压缩、内存硬盘缓存、网络拉取

*   1.同步加载只创建一个线程然后按照顺序进行图片加载
*   2.异步加载使用线程池，让存在的加载任务都处于不同线程
*   3.为了不开启过多的异步任务，只在列表静止的时候开启图片加载

具体为：

*   1、ImageLoader作为一个单例，提供了加载图片到指定控件的方法：直接从内存缓存中获取对象，如果没有则用一个ThreadPoolExecutor去执行Runnable任务来加载图片。ThreadPoolExecutor的创建需要指定核心线程数CPU数+1，最大线程数CPU数\*2+1，线程闲置超市时长10s,这几个关键数据，还可以加入ThreadFactory参数来创建定制化的线程。

*   2、ImageLoader的具体实现loadBitmap：先从内存缓存LruCache中加载，如果为空再从磁盘缓存中加载，加载成功后记得存入内存缓存，如果为空则从网络中直接下载输出流到磁盘缓存，然后再从磁盘中加载，如果为空并且磁盘缓存没有被创建的话，直接通过BitmapFactory的decodeStream获取网络请求的输入流获取Bitmap对象。

*   3、v4包的LruCache可以兼容到2.2版本，LruCache采用LinkedHashMap存储缓存对象。创建对象只需要提供缓存容量并重写sizeOf方法：作用是计算缓存对象的大小。有时需要重写entryRemoved方法，用于回收一些资源。

*   4、DiskLruCache通过open方法创建，设置缓存路径，缓存容量。缓存添加通过Editor对象创建输出流，下载资源到输出流完成后，commit，如果失败则abort撤回。然后刷新磁盘缓存。缓存查找通过Snapshot对象获取输入流，获取FileDescriptor，通过FileDescriptor解析出Bitmap对象。

*   5、列表中需要加载图片的时候，当列表在滑动中不进行图片加载，当滑动停止后再去加载图片。

##### Bitmap在decode的时候申请的内存如何复用，释放时机

##### 图片库对比

<http://stackoverflow.com/questions/29363321/picasso-v-s-imageloader-v-s-fresco-vs-glide>

<http://www.trinea.cn/android/android-image-cache-compare/>

##### Fresco与Glide的对比：

Glide：相对轻量级，用法简单优雅，支持Gif动态图，适合用在那些对图片依赖不大的App中。
Fresco：采用匿名共享内存来保存图片，也就是Native堆，有效的的避免了OOM，功能强大，但是库体积过大，适合用在对图片依赖比较大的App中。

Fresco的整体架构如下图所示：

![image](https://github.com/guoxiaoxing/android-open-framwork-analysis/raw/master/art/fresco/fresco_structure.png)

DraweeView：继承于ImageView，只是简单的读取xml文件的一些属性值和做一些初始化的工作，图层管理交由Hierarchy负责，图层数据获取交由负责。
DraweeHierarchy：由多层Drawable组成，每层Drawable提供某种功能（例如：缩放、圆角）。
DraweeController：控制数据的获取与图片加载，向pipeline发出请求，并接收相应事件，并根据不同事件控制Hierarchy，从DraweeView接收用户的事件，然后执行取消网络请求、回收资源等操作。
DraweeHolder：统筹管理Hierarchy与DraweeHolder。
ImagePipeline：Fresco的核心模块，用来以各种方式（内存、磁盘、网络等）获取图像。
Producer/Consumer：Producer也有很多种，它用来完成网络数据获取，缓存数据获取、图片解码等多种工作，它产生的结果由Consumer进行消费。
IO/Data：这一层便是数据层了，负责实现内存缓存、磁盘缓存、网络缓存和其他IO相关的功能。
纵观整个Fresco的架构，DraweeView是门面，和用户进行交互，DraweeHierarchy是视图层级，管理图层，DraweeController是控制器，管理数据。它们构成了整个Fresco框架的三驾马车。当然还有我们 幕后英雄Producer，所有的脏活累活都是它干的，最佳劳模👍

理解了Fresco整体的架构，我们还有了解在这套矿建里发挥重要作用的几个关键角色，如下所示：

Supplier：提供一种特定类型的对象，Fresco里有很多以Supplier结尾的类都实现了这个接口。
SimpleDraweeView：这个我们就很熟悉了，它接收一个URL，然后调用Controller去加载图片。该类继承于GenericDraweeView，GenericDraweeView又继承于DraweeView，DraweeView是Fresco的顶层View类。
PipelineDraweeController：负责图片数据的获取与加载，它继承于AbstractDraweeController，由PipelineDraweeControllerBuilder构建而来。AbstractDraweeController实现了DraweeController接口，DraweeController 是Fresco的数据大管家，所以的图片数据的处理都是由它来完成的。
GenericDraweeHierarchy：负责SimpleDraweeView上的图层管理，由多层Drawable组成，每层Drawable提供某种功能（例如：缩放、圆角），该类由GenericDraweeHierarchyBuilder进行构建，该构建器 将placeholderImage、retryImage、failureImage、progressBarImage、background、overlays与pressedStateOverlay等 xml文件或者Java代码里设置的属性信息都传入GenericDraweeHierarchy中，由GenericDraweeHierarchy进行处理。
DraweeHolder：该类是一个Holder类，和SimpleDraweeView关联在一起，DraweeView是通过DraweeHolder来统一管理的。而DraweeHolder又是用来统一管理相关的Hierarchy与Controller
DataSource：类似于Java里的Futures，代表数据的来源，和Futures不同，它可以有多个result。
DataSubscriber：接收DataSource返回的结果。
ImagePipeline：用来调取获取图片的接口。
Producer：加载与处理图片，它有多种实现，例如：NetworkFetcherProducer，LocalAssetFetcherProducer，LocalFileFetchProducer。从这些类的名字我们就可以知道它们是干什么的。 Producer由ProducerFactory这个工厂类构建的，而且所有的Producer都是像Java的IO流那样，可以一层嵌套一层，最终只得到一个结果，这是一个很精巧的设计👍
Consumer：用来接收Producer产生的结果，它与Producer组成了生产者与消费者模式。
注：Fresco源码里的类的名字都比较长，但是都是按照一定的命令规律来的，例如：以Supplier结尾的类都实现了Supplier接口，它可以提供某一个类型的对象（factory, generator, builder, closure等）。 以Builder结尾的当然就是以构造者模式创建对象的类。

##### Bitmap如何处理大图，如一张30M的大图，如何预防OOM?

<http://blog.csdn.net/guolin_blog/article/details/9316683>

<https://blog.csdn.net/lmj623565791/article/details/493009890>

使用BitmapRegionDecoder动态加载图片的显示区域。

##### Bitmap对象的理解。

##### 对inBitmap的理解。

##### 自己去实现图片库，怎么做？（对扩展开发，对修改封闭，同时又保持独立性，参考Android源码设计模式解析实战的图片加载库案例即可）

##### 写个图片浏览器，说出你的思路？

#### 八、[依赖全局管理框架：Dagger2实现原理](https://juejin.im/post/5e58779f518825493f6ce7eb)

##### 为什么要使用 Dagger2？

当项目越来越大时，类之间的调用层次会越来越深，并且有些类是Activity/Fragment，有些是单例，而且**它们的生命周期也不是一致的**，所以创建这些对象时要处理的各个对象的依赖关系和生命周期时的任务会很繁重，因此，为了解决这个问题Dagger2应运而生。**相比ButterKnife的轻量级使用，Dagger2会显得更重量级和锋利一些，它能够掌控全局，对项目中几乎所有的依赖进行集成管理**。如果有对Binder架构体系比较了解的朋友应该知道，其中的服务大管家ServiceManager负责所有的服务（引导服务、核心服务、其它服务）的管理，而Dagger2其实就是将项目中的依赖进行了集成管理。

##### mAndroidInjector 的作用？

mAndroidInjector是一个类型为DispatchingAndroidInjector\<Activity>的对象，可以这样理解它：它能够执行Android框架下的核心成员如Activity、Fragment的成员注入，在我们项目下的Application中将DispatchingAndroidInjector的泛型指定为Activity就说明它承担起了所有Activity成员依赖的注入。
