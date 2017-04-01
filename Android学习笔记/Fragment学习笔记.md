## 前言

> 此笔记摘抄于[Hongyang的博客](http://blog.csdn.net/lmj623565791)
> - [Android Fragment 真正的完全解析（上）](http://blog.csdn.net/lmj623565791/article/details/37970961)
> - [Android Fragment 真正的完全解析（下）](http://blog.csdn.net/lmj623565791/article/details/37992017)
> - [Android Fragment 你应该知道的一切](http://blog.csdn.net/lmj623565791/article/details/42628537)

- Fragment的生命周期：

onAttach() -> onCreate() -> onCreateView() -> onActivityCreated() -> onstart() ->onResume() -> onPause()
-> onStop() -> onDestroyView() -> onDestroy() -> onDetach()

- 静态使用Fragment

    1. 集成Fragment，重写onCreateView（）决定Fragment的布局
    2. 在Activity中声明此Fragment，就当和普通控件一样使用

- 动态使用Fragment

    1. FragmentManager fm = getFragmentManager();
    2. FragmentTransaction transaction = fm.beginTransaction();
    3. transaction.replace(R.id.xxx,new Fragment());
    4. transaction.commit();

- fragment.detach():会将View从UI中移除，fragment.attach():重建View视图，附加到UI上显示

- commit()方法一定要在Activity.onSaveInstance（）之前调用

- 如何更好使用Fragment
？要清楚一些方法，哪个会销毁视图，哪个会销毁实例，哪个仅仅是隐藏

    1. 保留用户操作：使用hide（）、show（）
    2. 不保留用户操作：使用replace（）
    3. 当Activity一直存在的时候，在不希望保留用户操作，优先使用detached（）

- 添加Fragment事务到回退栈：FragmentTransaction.addTOBackStack(strinh);

- Activity与Fragment的通信：

    1. Activity中包含自己管理的Fragment的引用，可以通过引用直接的访问所有的fragment的public方法
    2. 可以通过getFragmentManager.findFragmentByTag/findFragmentById获取Fragment的实例，然后进行操作
    3. 在Fragment中，可以通过getActivity（）的到当前绑定的Activity实例

- 没有布局的fragment的作用？ 当Activity重启时，用来保存大量数据做准备的

- Activity向Fragment传递参数

```java
public static Fragment newInstance(Stirng params) {
    Bundle bundle = new Bundle();
    bundle.putString("xxx",params);
    Fragment fragment = new Fragment();
    fragment.setArguments(bundle);
    return fragment;
}
```

- 在Fragment中的onCreate（）中进行获取参数，setArgument（）方法必须在fragment创建后，添加给Activity前完成