### 2、View的事件分发机制？滑动冲突怎么解决？

#### 了解Activity的构成

​	一个Activity包含了一个Window对象，这个对象是由PhoneWindow来实现的。PhoneWindow将DecorView作为整个应用窗口的根View，而这个DecorView又将屏幕划分为两个区域：一个是TitleView，另一个是ContentView，而我们平时所写的就是展示在ContentView中的。

#### 触摸事件的类型

触摸事件对应的是MotionEvent类，事件的类型主要有如下三种：

*   ACTION\_DOWN
*   ACTION\_MOVE(移动的距离超过一定的阈值会被判定为ACTION\_MOVE操作)
*   ACTION\_UP

View事件分发本质就是对MotionEvent事件分发的过程。即当一个MotionEvent发生后，系统将这个点击事件传递到一个具体的View上。

#### 事件分发流程

事件分发过程由三个方法共同完成：

dispatchTouchEvent：方法返回值为true表示事件被当前视图消费掉；返回为super.dispatchTouchEvent表示继续分发该事件，返回为false表示交给父类的onTouchEvent处理。

onInterceptTouchEvent：方法返回值为true表示拦截这个事件并交由自身的onTouchEvent方法进行消费；返回false表示不拦截，需要继续传递给子视图。如果return super.onInterceptTouchEvent(ev)， 事件拦截分两种情况: 　

1. 如果该View存在子View且点击到了该子View, 则不拦截, 继续分发
   给子View 处理, 此时相当于return false。
2. 如果该View没有子View或者有子View但是没有点击中子View(此时ViewGroup
   相当于普通View), 则交由该View的onTouchEvent响应，此时相当于return true。

注意：一般的LinearLayout、 RelativeLayout、FrameLayout等ViewGroup默认不拦截， 而
ScrollView、ListView等ViewGroup则可能拦截，得看具体情况。

onTouchEvent：方法返回值为true表示当前视图可以处理对应的事件；返回值为false表示当前视图不处理这个事件，它会被传递给父视图的onTouchEvent方法进行处理。如果return super.onTouchEvent(ev)，事件处理分为两种情况：

*   如果该View是clickable或者longclickable的,则会返回true, 表示消费
    了该事件, 与返回true一样;
*   如果该View不是clickable或者longclickable的,则会返回false, 表示不
    消费该事件,将会向上传递,与返回false一样。

注意：在Android系统中，拥有事件传递处理能力的类有以下三种：

*   Activity：拥有分发和消费两个方法。
*   ViewGroup：拥有分发、拦截和消费三个方法。
*   View：拥有分发、消费两个方法。

三个方法的关系用伪代码表示如下：

```java
public boolean dispatchTouchEvent(MotionEvent ev) {
    boolean consume = false;
    if (onInterceptTouchEvent(ev)) {
        if (mOnTouchListener != null && (mViewFlags & ENABLED_MASK) == ENABLED &&
            mOnTouchListener.onTouch(this, event)) {
        	return true;
    	}	
        consume = onTouchEvent(ev);
    } else {
        coonsume = child.dispatchTouchEvent(ev);
    }
    
    return consume;
}

    public boolean onTouchEvent(MotionEvent event) {
        ...
        if (clickable || (viewFlags & TOOLTIP) == TOOLTIP) {
            switch (action) {
                case MotionEvent.ACTION_UP:
                    ...
                    // 使用Runnable并发布此消息，而不是直接调用performClick。
                    // 这允许在单击操作开始之前更新视图的其他可视状态
                    if (mPerformClick == null) {
                        mPerformClick = new PerformClick();
                    }
                    if (!post(mPerformClick)) {
                        performClickInternal();//onClick被调用
                    }
                    ...
                case MotionEvent.ACTION_DOWN:
                    ...
        return false;
    }

```

​	通过上面的伪代码，我们可以大致了解点击事件的传递规则：对应一个根ViewGroup来说，点击事件产生后，首先会传递给它，这是它的dispatchTouchEvent就会被调用，如果这个ViewGroup的onInterceptTouchEvent方法返回true就表示它要拦截当前事件，接着事件就会交给这个ViewGroup处理，这时如果它的mOnTouchListener被设置，则onTouch会被调用，否则onTouchEvent会被调用。在onTouchEvent中，如果设置了mOnCLickListener，则onClick会被调用。只要View的CLICKABLE和LONG\_CLICKABLE有一个为true，onTouchEvent()就会返回true消耗这个事件。如果这个ViewGroup的onInterceptTouchEvent方法返回false就表示它不拦截当前事件，这时当前事件就会继续传递给它的子元素，接着子元素的dispatchTouchEvent方法就会被调用，如此反复直到事件被最终处理。

#### 一些重要的结论：

1. 事件传递优先级：onTouchListener.onTouch > onTouchEvent > onClickListener.onClick。
2. 正常情况下，一个时间序列只能被一个View拦截且消耗。因为一旦一个元素拦截了此事件，那么同一个事件序列内的所有事件都会直接交给它处理（即不会再调用这个View的拦截方法去询问它是否要拦截了，而是把剩余的ACTION\_MOVE、ACTION\_DOWN等事件直接交给它来处理）。特例：通过将重写View的onTouchEvent返回false可强行将事件转交给其他View处理。
3. 如果View不消耗除ACTION\_DOWN以外的其他事件，那么这个点击事件会消失，此时父元素的onTouchEvent并不会被调用，并且当前View可以持续收到后续的事件，最终这些消失的点击事件会传递给Activity处理。

4. ViewGroup默认不拦截任何事件（返回false）。

5. View的onTouchEvent默认都会消耗事件（返回true），除非它是不可点击的（clickable和longClickable同时为false）。View的longClickable属性默认都为false，clickable属性要分情况，比如Button的clickable属性默认为true，而TextView的clickable默认为false。

6. View的enable属性不影响onTouchEvent的默认返回值。

7. 通过requestDisallowInterceptTouchEvent方法可以在子元素中干预父元素的事件分发过程，但是ACTION\_DOWN事件除外。

记住这个图的传递顺序,面试的时候能够画出来,就很详细了：

![image](https://camo.githubusercontent.com/aff0a3e5fd27185944a27bb0ca3c78a79c6e7b0ff923538505e74c41fe830d60/68747470733a2f2f75706c6f61642d696d616765732e6a69616e7368752e696f2f75706c6f61645f696d616765732f3934343336352d616561383231626262363133633139352e706e67 "image")

#### ACTION\_CANCEL什么时候触发，触摸button然后滑动到外部抬起会触发点击事件吗，再滑动回去抬起会么？

*   一般ACTION\_CANCEL和ACTION\_UP都作为View一段事件处理的结束。如果在父View中拦截ACTION\_UP或ACTION\_MOVE，在第一次父视图拦截消息的瞬间，父视图指定子视图不接受后续消息了，同时子视图会收到ACTION\_CANCEL事件。
*   如果触摸某个控件，但是又不是在这个控件的区域上抬起（移动到别的地方了），就会出现action\_cancel。

##### 点击事件被拦截，但是想传到下面的View，如何操作？

重写子类的requestDisallowInterceptTouchEvent()方法返回true就不会执行父类的onInterceptTouchEvent()，即可将点击事件传到下面的View。

#### 如何解决View的事件冲突？举个开发中遇到的例子？

常见开发中事件冲突的有ScrollView与RecyclerView的滑动冲突、RecyclerView内嵌同时滑动同一方向。

滑动冲突的处理规则：

*   对于由于外部滑动和内部滑动方向不一致导致的滑动冲突，可以根据滑动的方向判断谁来拦截事件。
*   对于由于外部滑动方向和内部滑动方向一致导致的滑动冲突，可以根据业务需求，规定何时让外部View拦截事件，何时由内部View拦截事件。
*   对于上面两种情况的嵌套，相对复杂，可同样根据需求在业务上找到突破点。

滑动冲突的实现方法：

*   外部拦截法：指点击事件都先经过父容器的拦截处理，如果父容器需要此事件就拦截，否则就不拦截。具体方法：需要重写父容器的onInterceptTouchEvent方法，在内部做出相应的拦截。
*   内部拦截法：指父容器不拦截任何事件，而将所有的事件都传递给子容器，如果子容器需要此事件就直接消耗，否则就交由父容器进行处理。具体方法：需要配合requestDisallowInterceptTouchEvent方法。

[加深理解，GOGOGO](https://jsonchao.github.io/2018/10/17/Android%E8%A7%A6%E6%91%B8%E4%BA%8B%E4%BB%B6%E4%BC%A0%E9%80%92%E6%9C%BA%E5%88%B6/)

### 3、View的绘制流程？

#### DecorView被加载到Window中

*   从Activity的startActivity开始，最终调用到ActivityThread的handleLaunchActivity方法来创建Activity，首先，会调用performLaunchActivity方法，内部会执行Activity的onCreate方法，从而完成DecorView和Activity的创建。然后，会调用handleResumeActivity，里面首先会调用performResumeActivity去执行Activity的onResume()方法，执行完后会得到一个ActivityClientRecord对象，然后通过r.window\.getDecorView()的方式得到DecorView，然后会通过a.getWindowManager()得到WindowManager，最终调用其addView()方法将DecorView加进去。
*   WindowManager的实现类是WindowManagerImpl，它内部会将addView的逻辑委托给WindowManagerGlobal，可见这里使用了接口隔离和委托模式将实现和抽象充分解耦。在WindowManagerGlobal的addView()方法中不仅会将DecorView添加到Window中，同时会创建ViewRootImpl对象，并将ViewRootImpl对象和DecorView通过root.setView()把DecorView加载到Window中。这里的ViewRootImpl是ViewRoot的实现类，是连接WindowManager和DecorView的纽带。View的三大流程均是通过ViewRoot来完成的。

#### 了解绘制的整体流程

绘制会从根视图ViewRoot的performTraversals()方法开始，从上到下遍历整个视图树，每个View控件负责绘制自己，而ViewGroup还需要负责通知自己的子View进行绘制操作。

#### 理解MeasureSpec

MeasureSpec表示的是一个32位的整形值，它的高2位表示测量模式SpecMode，低30位表示某种测量模式下的规格大小SpecSize。MeasureSpec是View类的一个静态内部类，用来说明应该如何测量这个View。它由三种测量模式，如下：

*   EXACTLY：精确测量模式，视图宽高指定为match\_parent或具体数值时生效，表示父视图已经决定了子视图的精确大小，这种模式下View的测量值就是SpecSize的值。
*   AT\_MOST：最大值测量模式，当视图的宽高指定为wrap\_content时生效，此时子视图的尺寸可以是不超过父视图允许的最大尺寸的任何尺寸。
*   UNSPECIFIED：不指定测量模式, 父视图没有限制子视图的大小，子视图可以是想要的任何尺寸，通常用于系统内部，应用开发中很少用到。

MeasureSpec通过将SpecMode和SpecSize打包成一个int值来避免过多的对象内存分配，为了方便操作，其提供了打包和解包的方法，打包方法为makeMeasureSpec，解包方法为getMode和getSize。

普通View的MeasureSpec的创建规则如下：

![image](https://images2015.cnblogs.com/blog/918357/201706/918357-20170618234001337-203688773.png)

对于DecorView而言，它的MeasureSpec由窗口尺寸和其自身的LayoutParams共同决定；对于普通的View，它的MeasureSpec由父视图的MeasureSpec和其自身的LayoutParams共同决定。

#### 如何根据MeasureSpec去实现一个瀑布流的自定义ViewGroup？

#### View绘制流程之Measure

*   首先，在ViewGroup中的measureChildren()方法中会遍历测量ViewGroup中所有的View，当View的可见性处于GONE状态时，不对其进行测量。
*   然后，测量某个指定的View时，根据父容器的MeasureSpec和子View的LayoutParams等信息计算子View的MeasureSpec。
*   最后，将计算出的MeasureSpec传入View的measure方法，这里ViewGroup没有定义测量的具体过程，因为ViewGroup是一个抽象类，其测量过程的onMeasure方法需要各个子类去实现。不同的ViewGroup子类有不同的布局特性，这导致它们的测量细节各不相同，如果需要自定义测量过程，则子类可以重写这个方法。（setMeasureDimension方法用于设置View的测量宽高，如果View没有重写onMeasure方法，则会默认调用getDefaultSize来获得View的宽高）

##### getSuggestMinimumWidth分析

如果View没有设置背景，那么返回android\:minWidth这个属性所指定的值，这个值可以为0；如果View设置了背景，则返回android\:minWidth和背景的最小宽度这两者中的最大值。

##### 自定义View时手动处理wrap\_content时的情形

直接继承View的控件需要重写onMeasure方法并设置wrap\_content时的自身大小，否则在布局中使用wrap\_content就相当于使用match\_parent。此时，可以在wrap\_content的情况下（对应MeasureSpec.AT\_MOST）指定内部宽/高(mWidth和mHeight)。

##### LinearLayout的onMeasure方法实现解析（这里仅分析measureVertical核心源码）

系统会遍历子元素并对每个子元素执行measureChildBeforeLayout方法，这个方法内部会调用子元素的measure方法，这样各个子元素就开始依次进入measure过程，并且系统会通过mTotalLength这个变量来存储LinearLayout在竖直方向的初步高度。每测量一个子元素，mTotalLength就会增加，增加的部分主要包括了子元素的高度以及子元素在竖直方向上的margin等。

##### 在Activity中获取某个View的宽高

由于View的measure过程和Activity的生命周期方法不是同步执行的，如果View还没有测量完毕，那么获得的宽/高就是0。所以在onCreate、onStart、onResume中均无法正确得到某个View的宽高信息。解决方式如下：

*   Activity/View#onWindowFocusChanged：此时View已经初始化完毕，当Activity的窗口得到焦点和失去焦点时均会被调用一次，如果频繁地进行onResume和onPause，那么onWindowFocusChanged也会被频繁地调用。
*   view\.post(runnable)： 通过post可以将一个runnable投递到消息队列的尾部，始化好了然后等待Looper调用次runnable的时候，View也已经初始化好了。
*   ViewTreeObserver#addOnGlobalLayoutListener：当View树的状态发生改变或者View树内部的View的可见性发生改变时，onGlobalLayout方法将被回调。
*   View\.measure(int widthMeasureSpec, int heightMeasureSpec)：match\_parent时不知道parentSize的大小，测不出；具体数值时，直接makeMeasureSpec固定值，然后调用view\..measure就可以了；wrap\_content时，在最大化模式下，用View理论上能支持的最大值去构造MeasureSpec是合理的。

#### View的绘制流程之Layout

首先，会通过setFrame方法来设定View的四个顶点的位置，即View在父容器中的位置。然后，会执行到onLayout空方法，子类如果是ViewGroup类型，则重写这个方法，实现ViewGroup中所有View控件布局流程。

##### LinearLayout的onLayout方法实现解析（layoutVertical核心源码）

其中会遍历调用每个子View的setChildFrame方法为子元素确定对应的位置。其中的childTop会逐渐增大，意味着后面的子元素会被放置在靠下的位置。

注意：在View的默认实现中，View的测量宽/高和最终宽/高是相等的，只不过测量宽/高形成于View的measure过程，而最终宽/高形成于View的layout过程，即两者的赋值时机不同，测量宽/高的赋值时机稍微早一些。在一些特殊的情况下则两者不相等：

*   重写View的layout方法,使最终宽度总是比测量宽/高大100px。
*   View需要多次measure才能确定自己的测量宽/高，在前几次测量的过程中，其得出的测量宽/高有可能和最终宽/高不一致，但最终来说，测量宽/高还是和最终宽/高相同。

#### View的绘制流程之Draw

##### Draw的基本流程

绘制基本上可以分为六个步骤：

*   首先绘制View的背景；
*   如果需要的话，保持canvas的图层，为fading做准备；
*   然后，绘制View的内容；
*   接着，绘制View的子View；
*   如果需要的话，绘制View的fading边缘并恢复图层；
*   最后，绘制View的装饰(例如滚动条等等)。

##### setWillNotDraw的作用

如果一个View不需要绘制任何内容，那么设置这个标记位为true以后，系统会进行相应的优化。

*   默认情况下，View没有启用这个优化标记位，但是ViewGroup会默认启用这个优化标记位。
*   当我们的自定义控件继承于ViewGroup并且本身不具备绘制功能时，就可以开启这个标记位从而便于系统进行后续的优化。
*   当明确知道一个ViewGroup需要通过onDraw来绘制内容时，我们需要显示地关闭WILL\_NOT\_DRAW这个标记位。

#### Requestlayout，onlayout，onDraw，DrawChild区别与联系？

requestLayout()方法 ：会导致调用 measure()过程 和 layout()过程，将会根据标志位判断是否需要ondraw。

onLayout()方法：如果该View是ViewGroup对象，需要实现该方法，对每个子视图进行布局。

onDraw()方法：绘制视图本身 (每个View都需要重载该方法，ViewGroup不需要实现该方法)。

drawChild()：去重新回调每个子视图的draw()方法。

#### invalidate() 和 postInvalidate()的区别 ？

invalidate()与postInvalidate()都用于刷新View，主要区别是invalidate()在主线程中调用，若在子线程中使用需要配合handler；而postInvalidate()可在子线程中直接调用。

[更详细的内容请点击这里](https://jsonchao.github.io/2018/10/28/Android%20View%E7%9A%84%E7%BB%98%E5%88%B6%E6%B5%81%E7%A8%8B/)