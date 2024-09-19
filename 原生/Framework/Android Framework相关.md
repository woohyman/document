### 1、Android系统架构

![image](https://camo.githubusercontent.com/38b0734dd87beabf80f4e520d727d0b67e462aafd2a437aae92c8e3000a07e99/68747470733a2f2f646576656c6f7065722e616e64726f69642e636f6d2f67756964652f706c6174666f726d2f696d616765732f616e64726f69642d737461636b5f32782e706e673f686c3d7a682d636e "image")

Android 是一种基于 Linux 的开放源代码软件栈，为广泛的设备和机型而创建。下图所示为 Android 平台的五大组件：

1.应用程序

​	Android 随附一套用于电子邮件、短信、日历、互联网浏览和联系人等的核心应用。平台随附的应用与用户可以选择安装的应用一样，没有特殊状态。因此第三方应用可成为用户的默认网络浏览器、短信 Messenger 甚至默认键盘（有一些例外，例如系统的“设置”应用）。

​	系统应用可用作用户的应用，以及提供开发者可从其自己的应用访问的主要功能。例如，如果您的应用要发短信，您无需自己构建该功能，可以改为调用已安装的短信应用向您指定的接收者发送消息。

2、Java API 框架

您可通过以 Java 语言编写的 API 使用 Android OS 的整个功能集。这些 API 形成创建 Android 应用所需的构建块，它们可简化核心模块化系统组件和服务的重复使用，包括以下组件和服务：

*   丰富、可扩展的视图系统，可用以构建应用的 UI，包括列表、网格、文本框、按钮甚至可嵌入的网络浏览器
*   资源管理器，用于访问非代码资源，例如本地化的字符串、图形和布局文件
*   通知管理器，可让所有应用在状态栏中显示自定义提醒
*   Activity 管理器，用于管理应用的生命周期，提供常见的导航返回栈
*   内容提供程序，可让应用访问其他应用（例如“联系人”应用）中的数据或者共享其自己的数据

开发者可以完全访问 Android 系统应用使用的框架 API。

3、系统运行库

1\)原生 C/C++ 库

​	许多核心 Android 系统组件和服务（例如 ART 和 HAL）构建自原生代码，需要以 C 和 C++ 编写的原生库。Android 平台提供 Java 框架 API 以向应用显示其中部分原生库的功能。例如，您可以通过 Android 框架的 Java OpenGL API 访问 OpenGL ES，以支持在应用中绘制和操作 2D 和 3D 图形。如果开发的是需要 C 或 C++ 代码的应用，可以使用 Android NDK 直接从原生代码访问某些原生平台库。

2\)Android Runtime

​	对于运行 Android 5.0（API 级别 21）或更高版本的设备，每个应用都在其自己的进程中运行，并且有其自己的 Android Runtime (ART) 实例。ART 编写为通过执行 DEX 文件在低内存设备上运行多个虚拟机，DEX 文件是一种专为 Android 设计的字节码格式，经过优化，使用的内存很少。编译工具链（例如 Jack）将 Java 源代码编译为 DEX 字节码，使其可在 Android 平台上运行。

ART 的部分主要功能包括：

*   预先 (AOT) 和即时 (JIT) 编译
*   优化的垃圾回收 (GC)
*   更好的调试支持，包括专用采样分析器、详细的诊断异常和崩溃报告，并且能够设置监视点以监控特定字段

在 Android 版本 5.0（API 级别 21）之前，Dalvik 是 Android Runtime。如果您的应用在 ART 上运行效果很好，那么它应该也可在 Dalvik 上运行，但反过来不一定。

Android 还包含一套核心运行时库，可提供 Java API 框架使用的 Java 编程语言大部分功能，包括一些 Java 8 语言功能。

4、硬件抽象层 (HAL)

​	硬件抽象层 (HAL) 提供标准界面，向更高级别的 Java API 框架显示设备硬件功能。HAL 包含多个库模块，其中每个模块都为特定类型的硬件组件实现一个界面，例如相机或蓝牙模块。当框架 API 要求访问设备硬件时，Android 系统将为该硬件组件加载库模块。

5、Linux 内核

​	Android 平台的基础是 Linux 内核。例如，Android Runtime (ART) 依靠 Linux 内核来执行底层功能，例如线程和低层内存管理。使用 Linux 内核可让 Android 利用主要安全功能，并且允许设备制造商为著名的内核开发硬件驱动程序。

#### 对于Android应用开发来说，最好能手绘下面的系统架构图：

![image](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/3/1/17095ea677939c5b~tplv-t2oaga2asx-zoom-in-crop-mark:1512:0:0:0.awebp)

### 4、跨进程通信。

#### Android中进程和线程的关系？区别？

*   线程是CPU调度的最小单元，同时线程是一种有限的系统资源；而进程一般指一个执行单元，在PC和移动设备上指一个程序或者一个应用。
*   一般来说，一个App程序至少有一个进程，一个进程至少有一个线程（包含与被包含的关系），通俗来讲就是，在App这个工厂里面有一个进程，线程就是里面的生产线，但主线程（即主生产线）只有一条，而子线程（即副生产线）可以有多个。
*   进程有自己独立的地址空间，而进程中的线程共享此地址空间，都可以并发执行。

#### 如何开启多进程？应用是否可以开启N个进程？

在AndroidManifest中给四大组件指定属性android\:process开启多进程模式，在内存允许的条件下可以开启N个进程。

#### 为何需要IPC？多进程通信可能会出现的问题？

所有运行在不同进程的四大组件（Activity、Service、Receiver、ContentProvider）共享数据都会失败，这是由于Android为每个应用分配了独立的虚拟机，不同的虚拟机在内存分配上有不同的地址空间，这会导致在不同的虚拟机中访问同一个类的对象会产生多份副本。比如常用例子（通过开启多进程获取更大内存空间、两个或者多个应用之间共享数据、微信全家桶）。

一般来说，使用多进程通信会造成如下几方面的问题:

*   静态成员和单例模式完全失效：独立的虚拟机造成。
*   线程同步机制完全失效：独立的虚拟机造成。
*   SharedPreferences的可靠性下降：这是因为Sp不支持两个进程并发进行读写，有一定几率导致数据丢失。
*   Application会多次创建：Android系统在创建新的进程时会分配独立的虚拟机，所以这个过程其实就是启动一个应用的过程，自然也会创建新的Application。

#### Android中IPC方式、各种方式优缺点？

![这里写图片描述](https://img-blog.csdn.net/20170902130927615?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMDA3MjcxMQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast "这里写图片描述")

#### 讲讲AIDL？如何优化多模块都使用AIDL的情况？

AIDL(Android Interface Definition Language，Android接口定义语言)：如果在一个进程中要调用另一个进程中对象的方法，可使用AIDL生成可序列化的参数，AIDL会生成一个服务端对象的代理类，通过它客户端可以实现间接调用服务端对象的方法。

AIDL的本质是系统提供了一套可快速实现Binder的工具。关键类和方法：

*   AIDL接口：继承IInterface。
*   Stub类：Binder的实现类，服务端通过这个类来提供服务。
*   Proxy类：服务端的本地代理，客户端通过这个类调用服务端的方法。
*   asInterface()：客户端调用，将服务端返回的Binder对象，转换成客户端所需要的AIDL接口类型的对象。如果客户端和服务端位于同一进程，则直接返回Stub对象本身，否则返回系统封装后的Stub.proxy对象。
*   asBinder()：根据当前调用情况返回代理Proxy的Binder对象。
*   onTransact()：运行在服务端的Binder线程池中，当客户端发起跨进程请求时，远程请求会通过系统底层封装后交由此方法来处理。
*   transact()：运行在客户端，当客户端发起远程请求的同时将当前线程挂起。之后调用服务端的onTransact()直到远程请求返回，当前线程才继续执行。

当有多个业务模块都需要AIDL来进行IPC，此时需要为每个模块创建特定的aidl文件，那么相应的Service就会很多。必然会出现系统资源耗费严重、应用过度重量级的问题。解决办法是建立Binder连接池，即将每个业务模块的Binder请求统一转发到一个远程Service中去执行，从而避免重复创建Service。

工作原理：每个业务模块创建自己的AIDL接口并实现此接口，然后向服务端提供自己的唯一标识和其对应的Binder对象。服务端只需要一个Service并提供一个queryBinder接口，它会根据业务模块的特征来返回相应的Binder对象，不同的业务模块拿到所需的Binder对象后就可以进行远程方法的调用了。

#### 为什么选择Binder？

为什么选用Binder，在讨论这个问题之前，我们知道Android也是基于Linux内核，Linux现有的进程通信手段有以下几种：

*   管道：在创建时分配一个page大小的内存，缓存区大小比较有限；
*   消息队列：信息复制两次，额外的CPU消耗；不合适频繁或信息量大的通信；
*   共享内存：无须复制，共享缓冲区直接附加到进程虚拟地址空间，速度快；但进程间的同步问题操作系统无法实现，必须各进程利用同步工具解决；
*   套接字：作为更通用的接口，传输效率低，主要用于不同机器或跨网络的通信；
*   信号量：常作为一种锁机制，防止某进程正在访问共享资源时，其他进程也访问该资源。因此，主要作为进程间以及同一进程内不同线程之间的同步手段。 不适用于信息交换，更适用于进程中断控制，比如非法内存访问，杀死某个进程等；

既然有现有的IPC方式，为什么重新设计一套Binder机制呢。主要是出于以上三个方面的考量：

*   1、效率：传输效率主要影响因素是内存拷贝的次数，拷贝次数越少，传输速率越高。从Android进程架构角度分析：对于消息队列、Socket和管道来说，数据先从发送方的缓存区拷贝到内核开辟的缓存区中，再从内核缓存区拷贝到接收方的缓存区，一共两次拷贝，如图：

![](https://img.kancloud.cn/94/05/9405ffc5c0009d727c5207df02c53a66_500x185.jpg)

而对于Binder来说，数据从发送方的缓存区拷贝到内核的缓存区，而接收方的缓存区与内核的缓存区是映射到同一块物理地址的，节省了一次数据拷贝的过程，如图：

![](https://img.kancloud.cn/1e/73/1e7313618381def50e03e647f776d482_485x234.jpg)

共享内存不需要拷贝，Binder的性能仅次于共享内存。

*   2、稳定性：上面说到共享内存的性能优于Binder，那为什么不采用共享内存呢，因为共享内存需要处理并发同步问题，容易出现死锁和资源竞争，稳定性较差。Socket虽然是基于C/S架构的，但是它主要是用于网络间的通信且传输效率较低。Binder基于C/S架构 ，Server端与Client端相对独立，稳定性较好。
*   3、安全性：传统Linux IPC的接收方无法获得对方进程可靠的UID/PID，从而无法鉴别对方身份；而Binder机制为每个进程分配了UID/PID，且在Binder通信时会根据UID/PID进行有效性检测。

#### Binder机制的作用和原理？

Linux系统将一个进程分为用户空间和内核空间。对于进程之间来说，用户空间的数据不可共享，内核空间的数据可共享，为了保证安全性和独立性，一个进程不能直接操作或者访问另一个进程，即Android的进程是相互独立、隔离的，这就需要跨进程之间的数据通信方式。普通的跨进程通信方式一般需要2次内存拷贝，如下图所示：

![img](https://pic1.zhimg.com/80/v2-aab2affe42958a659ea8a517ffaff5a0_1440w.jpg)

一次完整的 Binder IPC 通信过程通常是这样：

*   首先 Binder 驱动在内核空间创建一个数据接收缓存区。
*   接着在内核空间开辟一块内核缓存区，建立内核缓存区和内核中数据接收缓存区之间的映射关系，以及内核中数据接收缓存区和接收进程用户空间地址的映射关系。
*   发送方进程通过系统调用 copyfromuser() 将数据 copy 到内核中的内核缓存区，由于内核缓存区和接收进程的用户空间存在内存映射，因此也就相当于把数据发送到了接收进程的用户空间，这样便完成了一次进程间的通信。

![img](https://pic4.zhimg.com/80/v2-cbd7d2befbed12d4c8896f236df96dbf_1440w.jpg)

#### Binder框架中ServiceManager的作用？

Binder框架 是基于 C/S 架构的。由一系列的组件组成，包括 Client、Server、ServiceManager、Binder驱动，其中 Client、Server、Service Manager 运行在用户空间，法运行在内核空间。如下图所示：

![Android Binder 之ServiceManager (基于android 12.0/S)_binder_bai1995-华为开发者空间](https://img-blog.csdnimg.cn/edbdbcf000eb4cda8fa7f3ae43487e06.png)

*   Server\&Client：服务器&客户端。在Binder驱动和Service Manager提供的基础设施上，进行Client-Server之间的通信。
*   ServiceManager（如同DNS域名服务器）服务的管理者，将Binder名字转换为Client中对该Binder的引用，使得Client可以通过Binder名字获得Server中Binder实体的引用。
*   Binder驱动（如同路由器）：负责进程之间binder通信的建立，计数管理以及数据的传递交互等底层支持。

最后，结合[Android跨进程通信：图文详解 Binder机制 ](https://blog.csdn.net/carson_ho/article/details/73560642)的总结图来综合理解一下：



#### Binder 的完整定义

*   从进程间通信的角度看，Binder 是一种进程间通信的机制；
*   从 Server 进程的角度看，Binder 指的是 Server 中的 Binder 实体对象；
*   从 Client 进程的角度看，Binder 指的是 Binder 代理对象，是 Binder 实体对象的一个远程代理;
*   从传输过程的角度看，Binder 是一个可以跨进程传输的对象；Binder 驱动会对这个跨越进程边界的对象对一点点特殊处理，自动完成代理对象和本地对象之间的转换。

#### 手写实现简化版AMS（AIDL实现）

与Binder相关的几个类的职责:

*   IBinder：跨进程通信的Base接口，它声明了跨进程通信需要实现的一系列抽象方法，实现了这个接口就说明可以进行跨进程通信，Client和Server都要实现此接口。
*   IInterface：这也是一个Base接口，用来表示Server提供了哪些能力，是Client和Server通信的协议。
*   Binder：提供Binder服务的本地对象的基类，它实现了IBinder接口，所有本地对象都要继承这个类。
*   BinderProxy：在Binder.java这个文件中还定义了一个BinderProxy类，这个类表示Binder代理对象它同样实现了IBinder接口，不过它的很多实现都交由native层处理。Client中拿到的实际上是这个代理对象。
*   Stub：这个类在编译aidl文件后自动生成，它继承自Binder，表示它是一个Binder本地对象；它是一个抽象类，实现了IInterface接口，表明它的子类需要实现Server将要提供的具体能力（即aidl文件中声明的方法）。
*   Proxy：它实现了IInterface接口，说明它是Binder通信过程的一部分；它实现了aidl中声明的方法，但最终还是交由其中的mRemote成员来处理，说明它是一个代理对象，mRemote成员实际上就是BinderProxy。

aidl文件只是用来定义C/S交互的接口，Android在编译时会自动生成相应的Java类，生成的类中包含了Stub和Proxy静态内部类，用来封装数据转换的过程，实际使用时只关心具体的Java接口类即可。为什么Stub和Proxy是静态内部类呢？这其实只是为了将三个类放在一个文件中，提高代码的聚合性。通过上面的分析，我们其实完全可以不通过aidl，手动编码来实现Binder的通信，下面我们通过编码来实现ActivityManagerService：

1、首先定义IActivityManager接口：

```java
public interface IActivityManager extends IInterface {
    //binder描述符
    String DESCRIPTOR = "android.app.IActivityManager";
    //方法编号
    int TRANSACTION_startActivity = IBinder.FIRST_CALL_TRANSACTION + 0;
    //声明一个启动activity的方法，为了简化，这里只传入intent参数
    int startActivity(Intent intent) throws RemoteException;
}
```

2、然后，实现ActivityManagerService侧的本地Binder对象基类：

```java
// 名称随意，不一定叫Stub
public abstract class ActivityManagerNative extends Binder implements IActivityManager {

    public static IActivityManager asInterface(IBinder obj) {
        if (obj == null) {
            return null;
        }
        IActivityManager in = (IActivityManager) obj.queryLocalInterface(IActivityManager.DESCRIPTOR);
        if (in != null) {
            return in;
        }
        //代理对象，见下面的代码
        return new ActivityManagerProxy(obj);
    }

    @Override
    public IBinder asBinder() {
        return this;
    }

    @Override
    protected boolean onTransact(int code, Parcel data, Parcel reply, int flags) throws RemoteException {
        switch (code) {
            // 获取binder描述符
            case INTERFACE_TRANSACTION:
                reply.writeString(IActivityManager.DESCRIPTOR);
                return true;
            // 启动activity，从data中反序列化出intent参数后，直接调用子类startActivity方法启动activity。
            case IActivityManager.TRANSACTION_startActivity:
                data.enforceInterface(IActivityManager.DESCRIPTOR);
                Intent intent = Intent.CREATOR.createFromParcel(data);
                int result = this.startActivity(intent);
                reply.writeNoException();
                reply.writeInt(result);
                return true;
        }
        return super.onTransact(code, data, reply, flags);
    }
}
```

3、接着，实现Client侧的代理对象：

```java
public class ActivityManagerProxy implements IActivityManager {
    private IBinder mRemote;

    public ActivityManagerProxy(IBinder remote) {
        mRemote = remote;
    }

    @Override
    public IBinder asBinder() {
        return mRemote;
    }

    @Override
    public int startActivity(Intent intent) throws RemoteException {
        Parcel data = Parcel.obtain();
        Parcel reply = Parcel.obtain();
        int result;
        try {
            // 将intent参数序列化，写入data中
            intent.writeToParcel(data, 0);
            // 调用BinderProxy对象的transact方法，交由Binder驱动处理。
            mRemote.transact(IActivityManager.TRANSACTION_startActivity, data, reply, 0);
            reply.readException();
            // 等待server执行结束后，读取执行结果
            result = reply.readInt();
        } finally {
            data.recycle();
            reply.recycle();
        }
        return result;
    }
}
```

4、最后，实现Binder本地对象（IActivityManager接口）：

```java
public class ActivityManagerService extends ActivityManagerNative {
    @Override
    public int startActivity(Intent intent) throws RemoteException {
        // 启动activity
        return 0;
    }
}
```

简化版的ActivityManagerService到这里就已经实现了，剩下就是Client只需要获取到AMS的代理对象IActivityManager就可以通信了。

#### 简单讲讲 binder 驱动吧？

从 Java 层来看就像访问本地接口一样，客户端基于 BinderProxy 服务端基于 IBinder 对象，从 native 层来看来看客户端基于 BpBinder 到 ICPThreadState 到 binder 驱动，服务端由 binder 驱动唤醒 IPCThreadSate 到 BbBinder 。跨进程通信的原理最终是要基于内核的，所以最会会涉及到 binder\_open 、binder\_mmap 和 binder\_ioctl这三种系统调用。

#### 跨进程传递大内存数据如何做？

binder 肯定是不行的，因为映射的最大内存只有 1M-8K，可以采用 binder + 匿名共享内存的形式，像跨进程传递大的 bitmap 需要打开系统底层的 ashmem 机制。

请按顺序仔细阅读下列文章提升对Binder机制的理解程度：

[写给 Android 应用工程师的 Binder 原理剖析](https://juejin.im/post/5acccf845188255c3201100f)

[Binder学习指南](http://weishu.me/2016/01/12/binder-index-for-newer/)

[Binder设计与实现](https://blog.csdn.net/universus/article/details/6211589)

[老罗Binder机制分析系列或Android系统源代码情景分析Binder章节](https://blog.csdn.net/luoshengyang/article/details/6618363)

### 5、Android系统启动流程是什么？（提示：init进程 -> Zygote进程 –> SystemServer进程 –> 各种系统服务 –> 应用进程）

Android系统启动的核心流程如下：

*   1、**启动电源以及系统启动**：当电源按下时引导芯片从预定义的地方（固化在ROM）开始执行，加载引导程序BootLoader到RAM，然后执行。
*   2、**引导程序BootLoader**：Bootloader主要是在系统加载前，初始化硬件设备，建立内存空间的映像图，为最终调用系统内核准备好环境。*初始化硬件*：*Bootloader*检查*硬件设备*，包括处理器、内存、外设等，并进行必要的*初始化*操作，以确保系统在启动时处于正常工作状态。
*   3、**Linux内核启动**：当内核启动时，设置缓存、被保护存储器、计划列表、加载驱动。当其完成系统设置时，会先在系统文件中寻找init.rc文件，并启动init进程。
*   4、**init进程启动**：初始化和启动属性服务，并且启动Zygote进程。
*   5、**Zygote进程启动**：创建JVM并为其注册JNI方法，创建服务器端Socket，启动SystemServer进程。
*   6、**SystemServer进程启动**：启动Binder线程池和SystemServiceManager，并且启动各种系统服务。
*   7、**Launcher启动**：被SystemServer进程启动的AMS会启动Launcher，Launcher启动后会将已安装应用的快捷图标显示到系统桌面上。

#### 需要更详细的分析请查看以下系列文章：

[Android系统启动流程之init进程启动](https://jsonchao.github.io/2019/02/18/Android%E7%B3%BB%E7%BB%9F%E5%90%AF%E5%8A%A8%E6%B5%81%E7%A8%8B%E4%B9%8Binit%E8%BF%9B%E7%A8%8B%E5%90%AF%E5%8A%A8/)

[Android系统启动流程之Zygote进程启动](https://jsonchao.github.io/2019/02/24/Android%E7%B3%BB%E7%BB%9F%E5%90%AF%E5%8A%A8%E6%B5%81%E7%A8%8B%E4%B9%8BZygote%E8%BF%9B%E7%A8%8B%E5%90%AF%E5%8A%A8/)

[Android系统启动流程之SystemServer进程启动](https://jsonchao.github.io/2019/03/03/Android%E7%B3%BB%E7%BB%9F%E5%90%AF%E5%8A%A8%E6%B5%81%E4%B9%8BSystemServer%E8%BF%9B%E7%A8%8B%E5%90%AF%E5%8A%A8/)

[Android系统启动流程之Launcher进程启动](https://jsonchao.github.io/2019/03/09/Android%E7%B3%BB%E7%BB%9F%E5%90%AF%E5%8A%A8%E6%B5%81%E7%A8%8B%E4%B9%8BLauncher%E8%BF%9B%E7%A8%8B%E5%90%AF%E5%8A%A8/)

#### 系统是怎么帮我们启动找到桌面应用的？

通过意图，PMS 会解析所有 apk 的 AndroidManifest.xml ，如果解析过会存到 package.xml 中不会反复解析，PMS 有了它就能找到了。

### 6、启动一个程序，可以主界面点击图标进入，也可以从一个程序中跳转过去，二者有什么区别？

是因为启动程序（主界面也是一个app），发现了在这个程序中存在一个设置为\<category android\:name="android.intent.category.LAUNCHER" />的activity,
所以这个launcher会把icon提出来，放在主界面上。当用户点击icon的时候，发出一个Intent：

```java
Intent intent = mActivity.getPackageManager().getLaunchIntentForPackage(packageName);
mActivity.startActivity(intent);   
```

跳过去可以跳到任意允许的页面，如一个程序可以下载，那么真正下载的页面可能不是首页（也有可能是首页），这时还是构造一个Intent，startActivity。这个intent中的action可能有多种view，download都有可能。系统会根据第三方程序向系统注册的功能，为你的Intent选择可以打开的程序或者页面。所以唯一的一点
不同的是从icon的点击启动的intent的action是相对单一的，从程序中跳转或者启动可能样式更多一些。本质是相同的。

### 7、AMS家族重要术语解释。

1. ActivityManagerServices，简称AMS，服务端对象，负责系统中所有Activity的生命周期。
2. ActivityThread，App的真正入口。当开启App之后，调用main()开始运行，开启消息循环队列，这就是传说的UI线程或者叫主线程。与ActivityManagerService一起完成Activity的管理工作。
3. ApplicationThread，用来实现ActivityManagerServie与ActivityThread之间的交互。在ActivityManagerSevice需要管理相关Application中的Activity的生命周期时，通过ApplicationThread的代理对象与ActivityThread通信。

4. ApplicationThreadProxy，是ApplicationThread在服务器端的代理，负责和客户端的ApplicationThread通信。AMS就是通过该代理与ActivityThread进行通信的。

5. Instrumentation，每一个应用程序只有一个Instrumetation对象，每个Activity内都有一个对该对象的引用，Instrumentation可以理解为应用进程的管家，ActivityThread要创建或暂停某个Activity时，都需要通过Instrumentation来进行具体的操作。

6. ActivityStack，Activity在AMS的栈管理，用来记录经启动的Activity的先后关系，状态信息等。通过ActivtyStack决定是否需要启动新的进程。

7. ActivityRecord，ActivityStack的管理对象，每个Acivity在AMS对应一个ActivityRecord，来记录Activity状态以及其他的管理信息。其实就是服务器端的Activit对象的映像。

8. TaskRecord，AMS抽象出来的一个“任务”的概念，是记录ActivityRecord的栈，一个“Task”包含若干个ActivityRecord。AMS用TaskRecord确保Activity启动和退出的顺序。如果你清楚Activity的4种launchMode，那么对这概念应该不陌生。

### 8、App启动流程（Activity的冷启动流程）。

点击应用图标后会去启动应用的Launcher Activity，如果Launcer Activity所在的进程没有创建，还会创建新进程，整体的流程就是一个Activity的启动流程。

Activity的启动流程图（放大可查看）如下所示：

![](https://i-blog.csdnimg.cn/blog_migrate/3bf229c17d983e07c960e5e51617c161.png)

整个流程涉及的主要角色有：

*   Instrumentation: 监控应用与系统相关的交互行为。
*   AMS：组件管理调度中心，什么都不干，但是什么都管。
*   ActivityStarter：Activity启动的控制器，处理Intent与Flag对Activity启动的影响，具体说来有：1 寻找符合启动条件的Activity，如果有多个，让用户选择；2 校验启动参数的合法性；3 返回int参数，代表Activity是否启动成功。
*   ActivityStackSupervisior：这个类的作用你从它的名字就可以看出来，它用来管理任务栈。
*   ActivityStack：用来管理任务栈里的Activity。
*   ActivityThread：最终干活的人，Activity、Service、BroadcastReceiver的启动、切换、调度等各种操作都在这个类里完成。

注：这里单独提一下ActivityStackSupervisior，这是高版本才有的类，它用来管理多个ActivityStack，早期的版本只有一个ActivityStack对应着手机屏幕，后来高版本支持多屏以后，就有了多个ActivityStack，于是就引入了ActivityStackSupervisior用来管理多个ActivityStack。

整个流程主要涉及四个进程：

*   调用者进程，如果是在桌面启动应用就是Launcher应用进程。
*   ActivityManagerService等待所在的System Server进程，该进程主要运行着系统服务组件。
*   Zygote进程，该进程主要用来fork新进程。
*   新启动的应用进程，该进程就是用来承载应用运行的进程了，它也是应用的主线程（新创建的进程就是主线程），处理组件生命周期、界面绘制等相关事情。

有了以上的理解，整个流程可以概括如下：

*   1、点击桌面应用图标，Launcher进程将启动Activity（MainActivity）的请求以Binder的方式发送给了AMS。
*   2、AMS接收到启动请求后，交付ActivityStarter处理Intent和Flag等信息，然后再交给ActivityStackSupervisior/ActivityStack 处理Activity进栈相关流程。同时以Socket方式请求Zygote进程fork新进程。
*   3、Zygote接收到新进程创建请求后fork出新进程。
*   4、在新进程里创建ActivityThread对象，新创建的进程就是应用的主线程，在主线程里开启Looper消息循环，开始处理创建Activity。
*   5、ActivityThread利用ClassLoader去加载Activity、创建Activity实例，并回调Activity的onCreate()方法，这样便完成了Activity的启动。

最后，再看看另一幅启动流程图来加深理解：

![image](http://img.mp.itc.cn/upload/20170329/ca9567ce3bf04c4abdb4d124cebfee76_th.jpeg)

### 9、ActivityThread工作原理。

### 10、说下四大组件的启动过程，四大组件的启动与销毁的方式。

#### 广播发送和接收的原理了解吗？

*   继承BroadcastReceiver，重写onReceive()方法。
*   通过Binder机制向ActivityManagerService注册广播。
*   通过Binder机制向ActivityMangerService发送广播。
*   ActivityManagerService查找符合相应条件的广播（IntentFilter/Permission）的BroadcastReceiver，将广播发送到BroadcastReceiver所在的消息队列中。
*   BroadcastReceiver所在消息队列拿到此广播后，回调它的onReceive()方法。

### 11、AMS是如何管理Activity的？

### 12、理解Window和WindowManager。

1.Window用于显示View和接收各种事件，Window有三种型：应用Window(每个Activity对应一个Window)、子Widow(不能单独存在，附属于特定Window)、系统window(toast和状态栏)

2.Window分层级，应用Window在1-99、子Window在1000-1999、系统Window在2000-2999.WindowManager提供了增改View的三个功能。

3.Window是个抽象概念：每一个Window对应着一个ViewRootImpl，Window通过ViewRootImpl来和View建立联系，View是Window存在的实体，只能通过WindowManager来访问Window。

4.WindowManager的实现是WindowManagerImpl，其再委托WindowManagerGlobal来对Window进行操作，其中有四种List分别储存对应的View、ViewRootImpl、WindowManger.LayoutParams和正在被删除的View。

5.Window的实体是存在于远端的WindowMangerService，所以增删改Window在本端是修改上面的几个List然后通过ViewRootImpl重绘View，通过WindowSession(每Window个对应一个)在远端修改Window。

6.Activity创建Window：Activity会在attach()中创建Window并设置其回调(onAttachedToWindow()、dispatchTouchEvent())，Activity的Window是由Policy类创建PhoneWindow实现的。然后通过Activity#setContentView()调用PhoneWindow的setContentView。

### 13、WMS是如何管理Window的？

#### Window 、WindowManager、WMS、SurfaceFlinger

*   WIndow：抽象概念不是实际存在的，而是以 View 的形式存在，通过 PhoneWindow 实现
*   WindowManager：外界访问 Window 的入口，内部与 WMS 交互是个 IPC 过程
*   WMS：管理窗口 Surface 的布局和次序，作为系统级服务单独运行在一个进程
*   SurfaceFlinger：将 WMS 维护的窗口按一定次序混合后显示到屏幕上

### 14、大体说清一个应用程序安装到手机上时发生了什么？

*   首先要解压 APK，资源、so等放到应用目录
*   Dalvik 会将 dex 处理成 ODEX ；ART 会将 dex 处理成 OAT；
*   OAT 包含 dex 和安装时编译的机器码

APK的安装流程如下所示：

![](https://github.com/guoxiaoxing/android-open-source-project-analysis/raw/master/art/app/package/apk_install_structure.png)

复制APK到/data/app目录下，解压并扫描安装包。

资源管理器解析APK里的资源文件。

解析AndroidManifest文件，并在/data/data/目录下创建对应的应用数据目录。

然后对dex文件进行优化，并保存在dalvik-cache目录下。

将AndroidManifest文件解析出的四大组件信息注册到PackageManagerService中。

安装完成后，发送广播。

### 15、Android的打包流程？（即描述清点击 Android Studio 的 build 按钮后发生了什么？）apk里有哪些东西？签名算法的原理？

#### apk打包流程

*   1.aapt 打包资源文件生成 R.java 文件；aidl 生成 java 文件
*   2.将 java 文件编译为 class 文件
*   3.将工程及第三方的 class 文件转换成 dex 文件
*   4.将 dex 文件、so、编译过的资源、原始资源等打包成 apk 文件
*   5.签名
*   6.资源文件对齐，减少运行时内存

Android的包文件APK分为两个部分：代码和资源，所以打包方面也分为资源打包和代码打包两个方面，下面就来分析资源和代码的编译打包原理。

APK整体的的打包流程如下图所示：

![image](https://github.com/guoxiaoxing/android-open-source-project-analysis/raw/master/art/native/vm/apk_package_flow.png)

具体说来：

*   通过AAPT工具进行资源文件（包括AndroidManifest.xml、布局文件、各种xml资源等）的打包，生成R.java文件。
*   通过AIDL工具处理AIDL文件，生成相应的Java文件。
*   通过Java Compiler编译R.java、Java接口文件、Java源文件，生成.class文件。
*   通过dex命令，将.class文件和第三方库中的.class文件处理生成classes.dex，该过程主要完成Java字节码转换成Dalvik字节码，压缩常量池以及清除冗余信息等工作。
*   通过ApkBuilder工具将资源文件、DEX文件打包生成APK文件。
*   通过Jarsigner工具，利用KeyStore对生成的APK文件进行签名。
*   如果是正式版的APK，还会利用ZipAlign工具进行对齐处理，对齐的过程就是将APK文件中所有的资源文件距离文件的起始距位置都偏移4字节的整数倍，这样通过内存映射访问APK文件的速度会更快，并且会减少其在设备上运行时的内存占用。

### 16、[说下安卓虚拟机和java虚拟机的原理和不同点](https://blog.csdn.net/jason0539/article/details/50440669)?（JVM、Davilk、ART三者的原理和区别）

#### JVM 和Dalvik虚拟机的区别

JVM:.java -> javac -> .class -> jar -> .jar

架构: 堆和栈的架构.

DVM:.java -> javac -> .class -> dx.bat -> .dex

架构: 寄存器(cpu上的一块高速缓存)

#### Android2个虚拟机的区别（一个5.0之前，一个5.0之后）

*   Dalvik
    *   谷歌设计专用于 Android 平台的 Java 虚拟机，可直接运行 .dex 文件，适合内存和处理速度有限的系统
    *   JVM 指令集是基于栈的；Dalvik 指令集是基于寄存器的，代码执行效率更优
*   ART
    *   Dalvik 每次运行都要将字节码转换成机器码；ART 在应用安装时就会转换成机器码，执行速度更快
    *   ART 存储机器码占用空间更大，空间换时间

什么是Dalvik：Dalvik是Google公司自己设计用于Android平台的Java虚拟机。Dalvik虚拟机是Google等厂商合作开发的Android移动设备平台的核心组成部分之一，它可以支持已转换为.dex(即Dalvik Executable)格式的Java应用程序的运行，.dex格式是专为Dalvik应用设计的一种压缩格式，适合内存和处理器速度有限的系统。Dalvik经过优化，允许在有限的内存中同时运行多个虚拟机的实例，并且每一个Dalvik应用作为独立的Linux进程执行。独立的进程可以防止在虚拟机崩溃的时候所有程序都被关闭。

什么是ART\:Android操作系统已经成熟，Google的Android团队开始将注意力转向一些底层组件，其中之一是负责应用程序运行的Dalvik运行时。Google开发者已经花了两年时间开发更快执行效率更高更省电的替代ART运行时。ART代表Android Runtime,其处理应用程序执行的方式完全不同于Dalvik，Dalvik是依靠一个Just-In-Time(JIT)编译器去解释字节码。开发者编译后的应用代码需要通过一个解释器在用户的设备上运行，这一机制并不高效，但让应用能更容易在不同硬件和架构上运行。ART则完全改变了这套做法，在应用安装的时候就预编译字节码为机器语言，这一机制叫Ahead-Of-Time(AOT)编译。在移除解释代码这一过程后，应用程序执行将更有效率，启动更快。

ART优点：

*   系统性能的显著提升。
*   应用启动更快、运行更快、体验更流畅、触感反馈更及时。
*   更长的电池续航能力。
*   支持更低的硬件。

ART缺点：

*   更大的存储空间占用，可能会增加10%-20%。
*   更长的应用安装时间。

#### AOT和JIT以及混合编译的区别、优势

AOT和JIT是什么？AOT,即Ahead-of-time,指预先编译. JIT,即Just-In-Time,指即时编译.

区别: 主要区别在于是否在“运行时”进行编译.

优劣: AOT优点：1.在程序运行前编译,可以避免在运行时的编译性能消耗和内存消耗. 2.可以在程序运行初期就达到最高性能. 3.可以显著的加快程序的启动. AOT缺点：1.在程序运行前编译会使程序安装的时间增加. 2.牺牲Java的一致性. 3.将提前编译的内容保存会占用更多的外存.

JIT优点：1.可以根据当前硬件情况实时编译生成最优机器指令(ps\:AOT也可以做到,在用户使用是使用字节码根据机器情况在做一次编译). 2.可以根据当前程序的运行情况生成最优的机器指令序列. 3.当程序需要支持动态链接时,只能使用JIT. 4.可以根据进程中内存的实际情况调整代码,使内存能够更充分的利用. JIT缺点：1.编译需要占用运行时资源,会导致进程卡顿. 2.由于编译时间需要占用运行时间,对于某些代码的编译优化不能完全支持,需要在程序流畅和编译时间之间做权衡. 3.在编译准备和识别频繁使用的方法需要占用时间,使得初始编译不能达到最高性能.

混合编译: Android N引入了使用编译+解释+JIT的混合运行时,以获得安装时间,内存占用,电池消耗和性能之间的最佳折衷. 优点: 即使是大型应用程序的安装时间也减少到几秒钟. 系统更新安装得更快,因为它们不需要优化步骤. 应用程序的RAM占用空间较小,在某些情况下降至50％. 改善了表现. 降低电池消耗.

#### ART和Davlik中垃圾回收的区别？

### 17、安卓采用自动垃圾回收机制，请说下安卓内存管理的原理？

#### 开放性问题：如何设计垃圾回收算法？

### 18、Android中App是如何沙箱化的,为何要这么做？

### 19、[一个图片在app中调用R.id后是如何找到的](https://my.oschina.net/u/255456/blog/608229)？

### 20、JNI

#### Java中 long、float 字节数

```java
short s; 2字节
int i; 4字节 float f; 4字节
long l; 8字节 double d; 8字节
char c; 2字节（C语⾔中是1字节）
byte b; 1字节
boolean bool; false/true 1字节
```

#### Java调用C++

*   在Java中声明Native方法（即需要调用的本地方法）
*   编译上述 Java源文件javac（得到 .class文件） 3。 通过 javah 命令导出JNI的头文件（.h文件）
*   使用 Java需要交互的本地代码 实现在 Java中声明的Native方法
*   编译.so库文件
*   通过Java命令执行 Java程序，最终实现Java调用本地代码

#### C++调用Java

* 从classpath路径下搜索ClassMethod这个类，并返回该类的Class对象。

* 获取类的默认构造方法ID。

* 查找实例方法的ID。

* 创建该类的实例。

* 调用对象的实例方法。

      JNIEXPORT void JNICALL Java_com_study_jnilearn_AccessMethod_callJavaInstaceMethod  
      (JNIEnv *env, jclass cls)  
      {  
        jclass clazz = NULL;  
        jobject jobj = NULL;  
        jmethodID mid_construct = NULL;  
        jmethodID mid_instance = NULL;  
        jstring str_arg = NULL;  
        // 1、从classpath路径下搜索ClassMethod这个类，并返回该类的Class对象  
        clazz = (*env)->FindClass(env, "com/study/jnilearn/ClassMethod");  
        if (clazz == NULL) {  
            printf("找不到'com.study.jnilearn.ClassMethod'这个类");  
            return;  
        }  
        
        // 2、获取类的默认构造方法ID  
        mid_construct = (*env)->GetMethodID(env,clazz, "<init>","()V");  
        if (mid_construct == NULL) {  
            printf("找不到默认的构造方法");  
            return;  
        }  
      
        // 3、查找实例方法的ID  
        mid_instance = (*env)->GetMethodID(env, clazz, "callInstanceMethod", "(Ljava/lang/String;I)V");  
        if (mid_instance == NULL) {  
      
            return;  
        }  
      
        // 4、创建该类的实例  
        jobj = (*env)->NewObject(env,clazz,mid_construct);  
        if (jobj == NULL) {  
            printf("在com.study.jnilearn.ClassMethod类中找不到callInstanceMethod方法");  
            return;  
        }  
      
        // 5、调用对象的实例方法  
        str_arg = (*env)->NewStringUTF(env,"我是实例方法");  
        (*env)->CallVoidMethod(env,jobj,mid_instance,str_arg,200);  
      
        // 删除局部引用  
        (*env)->DeleteLocalRef(env,clazz);  
        (*env)->DeleteLocalRef(env,jobj);  
        (*env)->DeleteLocalRef(env,str_arg);  
      }  

#### 如何在jni中注册native函数，有几种注册方式？

#### so 的加载流程是怎样的，生命周期是怎样的？

这个要从 java 层去看源码分析，是从 ClassLoader 的 PathList 中去找到目标路径加载的，同时 so 是通过 mmap 加载映射到虚拟空间的。生命周期加载库和卸载库时分别调用 JNI\_OnLoad 和 JNI\_OnUnload() 方法。
