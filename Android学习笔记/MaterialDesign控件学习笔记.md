### 前言

> 此笔记摘抄于网上的一些学习资源

- CoordinatorLayout：作为界面的根布局，协调子View，通知子View之间状态的变换

    - Behavior：执行定制的动作
    - Child：指的是能够改变行为的CoordinatorLayout的子View
    - Dependency：指的是Child依赖的View。就是说Dependency发生了变化，那么Child就要发生相应的变化

    - 自定义Behavior：每次UI发生变化的时候就会调用layoutDependsOn()方法，鉴定完dependency后返回true，
之后就开始调用onDependentViewChanged()方法，在这个方法中我们利用dependency来实现动画、转换等动作

- AppBarLayout：可以定制当某个可滚动的View的滚动手势发生变化时，其内部的子View实现何种动作。

    app:layout_scrollFlags属性值：

    - scroll：view会跟随这滚动事件一起发生移动。注意两点：一，如果使用了其他值，必定使用这个值才能起作用。
    二，如果在这个子View前面的任何其他子View没有设置这个值，那么这个子View的设置将失去作用

    - enterAlways：当可滚动的控件往下滚动时，该view会直接往下滚动。

    - enterAlwaysCollapsed：是enterAlways的附加选项，一般跟enterAlways一起使用，它是指，View在往下“出现”的时候，
    首先是enterAlways效果，当View的高度达到最小高度时，View就暂时不去往下滚动，直到ScrollView滑动到顶部不再滑动时，View再继续往下滑动，直到滑到View的顶部结束。

    - exitUntiCollapsed：值设为exitUntilCollapsed的View，当这个View要往上逐渐“消逝”时，会一直往上滑动，直到剩下的的高度达到它的最小高度后，再响应ScrollView的内部滑动事件。

- CollapsingToolbarLayout：用来对ToolBar进行再次包装的ViewGroup，主要是用于实现折叠的效果

    - 折叠Title（Collapsing title）：当布局内容全部显示出来时，title是最大的，但是随着View逐步移出屏幕顶部，title变得越来越小。你可以通过调用setTitle函数来设置title。

    - 内容纱布（Content scrim）：根据滚动的位置是否到达一个阀值，来决定是否对View“盖上纱布”。可以通过setContentScrim(Drawable)来设置纱布的图片.

    - 状态栏纱布（Status bar scrim)：根据滚动位置是否到达一个阀值决定是否对状态栏“盖上纱布”，你可以通过setStatusBarScrim(Drawable)来设置纱布图片，但是只能在LOLLIPOP设备上面有作用。

    - 视差滚动子View(Parallax scrolling children):子View可以选择在当前的布局当时是否以“视差”的方式来跟随滚动。（PS:其实就是让这个View的滚动的速度比其他正常滚动的View速度稍微慢一点）。将布局参数app:layout_collapseMode设为parallax

    - 将子View位置固定(Pinned position children)：子View可以选择是否在全局空间上固定位置，这对于Toolbar来说非常有用，因为当布局在移动时，可以将Toolbar固定位置而不受移动的影响。 将app:layout_collapseMode设为pin。