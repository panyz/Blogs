### **前言** ###
上周的那篇[一个案例教你简单地玩转ViewPager（一）之带指示点的引导页](https://panyz.github.io/2016/08/22/%E4%B8%80%E4%B8%AA%E6%A1%88%E4%BE%8B%E6%95%99%E4%BD%A0%E7%AE%80%E5%8D%95%E5%9C%B0%E7%8E%A9%E8%BD%ACViewPager%EF%BC%88%E4%B8%80%EF%BC%89%E4%B9%8B%E5%B8%A6%E6%8C%87%E7%A4%BA%E7%82%B9%E7%9A%84%E5%BC%95%E5%AF%BC%E9%A1%B5/)挖了一个关于学习ViewPager的坑，这周肯定要来填一下这个坑啦。这个系列坑完全是写在同一个Demo中的，所以需要用到的还是希望从第一篇看起，放心，代码还是相当容易的。

ps：不知道大家有没有看过我之前写的那篇文章[RadioButton+Fragment实现底部导航栏](https://panyz.github.io/2016/08/01/RadioButton-Fragment%E5%AE%9E%E7%8E%B0%E5%BA%95%E9%83%A8%E5%AF%BC%E8%88%AA%E6%A0%8F/)，没错，这次我还是用这样布局来写Demo的，所以到时Fragment会比较多，大家区分好。（这次效果就是在一个Fragment上再增加3个可滑动的Fragment，哈！）看代码吧！

嗯嗯，这次带来的是ViewPager+TabLayout+Fragment顶部标签界面滑动，效果如下：

![界面滑动](http://upload-images.jianshu.io/upload_images/2355123-71da08ea342eda64.gif?imageMogr2/auto-orient/strip)

### **Talk is cheap,show u the code** ###

- 添加上TabLayout的依赖

```java
compile 'com.android.support:design:23.4.0'
compile 'com.android.support:support-v4:23.4.0'
```

- 布局

```xml
    <android.support.design.widget.TabLayout
        android:id="@+id/mTabLayout"
        android:layout_width="match_parent"
        android:layout_height="wrap_content">

    </android.support.design.widget.TabLayout>

    <android.support.v4.view.ViewPager
        android:id="@+id/mViewPager1"
        android:layout_width="match_parent"
        android:layout_height="0dp"
        android:layout_weight="1">

    </android.support.v4.view.ViewPager>
```

- Fragment1上的代码（ps:Fragment1中包含了3个VPFragment滑动页面）

```java
  private ViewPager mViewPager1;
    private TabLayout mTabLayout;
    private String[] tabTitle = {"第一页面","第二页面","第三页面"};//每个页面顶部标签的名字

    @Nullable
    @Override
    public View onCreateView(LayoutInflater inflater, @Nullable ViewGroup container, @Nullable Bundle savedInstanceState) {
        View rootView = inflater.inflate(R.layout.fragment1_layout, container, false);
        initViews(rootView);
        initData();
        return rootView;

    }

    private void initViews(View rootView) {
        mViewPager1 = (ViewPager) rootView.findViewById(R.id.mViewPager1);
        mTabLayout = (TabLayout) rootView.findViewById(R.id.mTabLayout);
    }

    private void initData() {
        for (int i=0; i<tabTitle.length; i++) {
            mTabLayout.addTab(mTabLayout.newTab().setText(tabTitle[i]));
        }
        mTabLayout.setTabGravity(TabLayout.GRAVITY_FILL);
        //设置顶部标签指示条的颜色和选中页面时标签字体颜色
        mTabLayout.setSelectedTabIndicatorColor(Color.parseColor("#7CCD7C"));
        mTabLayout.setTabTextColors(Color.GRAY,Color.parseColor("#FF4081"));

        //这里注意的是，因为我是在fragment中创建MyFragmentStatePagerAdapter，所以要传getChildFragmentManager()
        mViewPager1.setAdapter(new MyFragmentStatePagerAdapter(getChildFragmentManager(),tabTitle));
        //在设置viewpager页面滑动监听时，创建TabLayout的滑动监听
        mViewPager1.addOnPageChangeListener(new TabLayout.TabLayoutOnPageChangeListener(mTabLayout));

        mTabLayout.setOnTabSelectedListener(new TabLayout.OnTabSelectedListener() {
            @Override
            public void onTabSelected(TabLayout.Tab tab) {
                //在选中的顶部标签时，为viewpager设置currentitem
                mViewPager1.setCurrentItem(tab.getPosition());
            }

            @Override
            public void onTabUnselected(TabLayout.Tab tab) {

            }

            @Override
            public void onTabReselected(TabLayout.Tab tab) {

            }
        });
    }
```

因为是结合Fragment一起使用，所以这次要用到的适配器就是FragmentStatePagerAdapter，使用起来也是比较简单的，如下：

```java
 private String[] tabTilte;

    public MyFragmentStatePagerAdapter(FragmentManager fm, String[] tabTitle) {
        super(fm);
        this.tabTilte = tabTitle;
    }

    @Override
    public Fragment getItem(int position) {
        switch (position) {
            case 0:
                return new VPFragment1();//第一页面的fragment
            case 1:
                return new VPFragment2();//第二页面的fragment
            case 2:
                return new VPFragment3();//第三页面的fragment
        }
        return null;
    }

    @Override
    public int getCount() {
        return tabTilte.length;
    }
```

### **待续** ###

请关注，下一篇，一个案例教你简单地玩转ViewPager（三）之带指示点的图片自动轮播效果

最后

**小弟不才，还望多多指教！**