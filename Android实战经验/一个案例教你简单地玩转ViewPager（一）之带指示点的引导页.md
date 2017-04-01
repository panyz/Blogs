### **前言** ###

上一周我整理了一下我自己的资料，写了篇[Android自学资源大整合](http://www.jianshu.com/p/9f354b41fb5f),看到不少童鞋喜欢和收藏了，所以我接下来会留意一些网站，博客，技术系列文章等等Android学习资源，持续更新那篇软文的，哈！希望大家能有推荐的，就在评论写下或者留言给我，壮大我们自学军团：D

嗯，按照惯例，又说了一通与此次主题无关的废话了。接下来我们直奔主题，拥抱一下ViewPager！解释不了这么多了，快上车！

ViewPager能用来干什么呢？我觉得可以有如下3点：

1. 应用第一次打开时的**引导页**。

![引导页](http://upload-images.jianshu.io/upload_images/2355123-22af77dfb86c05ab.gif?imageMogr2/auto-orient/strip)

2. 通过ViewPaper+TabLayout+Fragment形成顶部带有标签的**界面滑动**。

![界面滑动](http://upload-images.jianshu.io/upload_images/2355123-71da08ea342eda64.gif?imageMogr2/auto-orient/strip)

3. 通过自定义ViewPager,带有**图片自动轮播**效果的控件。

![图片自动轮播](http://upload-images.jianshu.io/upload_images/2355123-32768597cc853934.gif?imageMogr2/auto-orient/strip)


接下来我的案例将按上述的几点来一一展示，跟着我的步伐，一步两步来玩转ViewPager吧。

### **Talk is cheap,show u the code** ###

- WelcomeActivity

一般应用第一次打开时，会有一个引导页的效果，但当第二次再打开的时候，将直接跳转到主页，所以这个Activity，就是用来做此判断的。代码也是比较简单。

```java
  private boolean isFirst = false;//是否第一次打开App

    private Handler handler = new Handler();

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        requestWindowFeature(Window.FEATURE_NO_TITLE);
        setContentView(R.layout.activity_welcome);
        initData();
    }

    private void initData() {
        SharedPreferences sp = getSharedPreferences("WelcomeActivity", 0);
        isFirst = sp.getBoolean("isFirst",true);
        //判断是否第一次打开App,是的话跳转到引导页，否则跳转到主页
        if (isFirst) {
            handler.postDelayed(new Runnable() {
                @Override
                public void run() {
                    gotoStartActivity();
                }
            }, 2000);
            SharedPreferences.Editor editor = sp.edit();
            editor.putBoolean("isFirst",false);
            editor.commit();
        } else {
            handler.postDelayed(new Runnable() {
                @Override
                public void run() {
                    gotoMainActivity();
                }
            }, 2000);
        }
    }

    private void gotoMainActivity() {
        Intent intent = new Intent(WelcomeActivity.this, MainActivity.class);
        startActivity(intent);
        finish();
    }

    private void gotoStartActivity() {
        Intent intent = new Intent(WelcomeActivity.this, StartActivity.class);
        startActivity(intent);
        finish();
    }
```

- StartActivity

在这个Activity下就是展示引导页的时候了，先看看布局文件代码。

```xml
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
                android:layout_width="match_parent"
                android:layout_height="match_parent"
                android:orientation="vertical">

    <android.support.v4.view.ViewPager
        android:id="@+id/mViewPager"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content">

    </android.support.v4.view.ViewPager>

    <LinearLayout
        android:layout_width="wrap_content"
        android:layout_height="10dp"
        android:layout_alignParentBottom="true"
        android:layout_centerHorizontal="true"
        android:layout_marginBottom="15dp"
        android:orientation="horizontal">

        <ImageView
            android:id="@+id/point1"
            android:layout_width="10dp"
            android:layout_height="wrap_content"
            android:src="@mipmap/page_indicator_focused"
        />

        <ImageView
            android:id="@+id/point2"
            android:layout_width="10dp"
            android:layout_height="wrap_content"
            android:src="@mipmap/page_indicator_unfocused"
        />

        <ImageView
            android:id="@+id/point3"
            android:layout_width="10dp"
            android:layout_height="wrap_content"
            android:src="@mipmap/page_indicator_unfocused"
        />
    </LinearLayout>

</RelativeLayout>
```

ViwePager不用说啦，是这次的主角，而接下来那几个ImageView，是用来当指示点的，相信这大家都明白，这里我直接用控件来显示指示点，做法也是简单的。

```java
 private ViewPager mViewPager;
    private List<View> viewList = new ArrayList<View>();
    private MyPagerAdapter myAdapter;

    private ImageView[] indicationPoint;//指示点控件
    private int[] points = {R.id.point1,R.id.point2,R.id.point3};

    private Button btnStart;

    @Override
    protected void onCreate(@Nullable Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        requestWindowFeature(Window.FEATURE_NO_TITLE);
        setContentView(R.layout.start_layout);
        initData();
        initViews();
    }

    private void initData() {
        LayoutInflater inflater = LayoutInflater.from(this);
        viewList.add(inflater.inflate(R.layout.start1_layout,null));
        viewList.add(inflater.inflate(R.layout.start2_layout,null));
        viewList.add(inflater.inflate(R.layout.start3_layout,null));

        indicationPoint = new ImageView[viewList.size()];
        //实例化每个指示点控件
        for (int i=0; i<viewList.size(); i++) {
            indicationPoint[i] = (ImageView) findViewById(points[i]);
        }
    }

    private void initViews() {
        myAdapter = new MyPagerAdapter(viewList);
        mViewPager = (ViewPager) this.findViewById(R.id.mViewPager);
        mViewPager.setAdapter(myAdapter);
        mViewPager.setPageTransformer(true, new DepthPageTransformer());
        mViewPager.setOffscreenPageLimit(viewList.size());// 加载缓存的页面个数
        mViewPager.setOnPageChangeListener(this);

        btnStart = (Button) viewList.get(viewList.size()-1).findViewById(R.id.start);
        btnStart.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent(StartActivity.this,MainActivity.class);
                startActivity(intent);
                finish();
            }
        });
    }

    @Override
    public void onPageScrolled(int position, float positionOffset, int positionOffsetPixels) {

    }

    @Override
    public void onPageSelected(int position) {
        for (int i=0; i<points.length; i++){
            if (position == i) {
                indicationPoint[i].setImageResource(R.mipmap.page_indicator_focused);
            } else {
                indicationPoint[i].setImageResource(R.mipmap.page_indicator_unfocused);
            }
        }
    }

    @Override
    public void onPageScrollStateChanged(int state) {

    }
```

如果你熟悉ListView的用法，相信使用ViewPager时也会比较简单，因为其也是要设置一个PagerAdapter的适配器，将数据传递过去。这里注意的是：

1. ``` mViewPager.setPageTransformer(true, new DepthPageTransformer()); ```这是在页面滑动的时候，起到一个动画效果。如果你想更换另一种效果，可以上google官方培训课程中，找到ViewPager，那里有相关的介绍，把上面的代码copy到你的项目中，再调用即可。
2. ``` btnStart = (Button) viewList.get(viewList.size()-1).findViewById(R.id.start); ```因为当你滑动到最后一张时，就要有个按钮跳转到主页吧，这里这个按钮控件id并不是在Activity的布局上，所以要在你最后一张页面上的布局获取其id。

```java
private List<View> viewList;

    public MyPagerAdapter(List<View> viewList) {
        this.viewList = viewList;
    }

    // 更新视图
    @Override
    public Object instantiateItem(ViewGroup container, int position) {
        container.addView(viewList.get(position));
        return viewList.get(position);
    }

    // 销毁某个元素时候调用
    @Override
    public void destroyItem(ViewGroup container, int position, Object object) {
        container.removeView(viewList.get(position));
    }

    //返回视图的个数
    @Override
    public int getCount() {
        return viewList.size();
    }

    // 判断是否是生成的对象
    @Override
    public boolean isViewFromObject(View view, Object object) {
        return view == object;
    }
```

这是继承了PagerAdapter的一个适配器类，写法也是比较的简单。到这里，一个引导页也就完成啦！接下来就是怎样通过ViewPaper+TabLayout+Fragment形成带有标签的**滑动页面**吧。

### **待续** ###

如果接着写下去的话，此文篇幅将会很长，没点耐心会越看越烦躁的，所以我还是分开来，写成一个系列吧，请关注，下一篇，一个案例教你简单地玩转ViewPager（二）之ViewPaper+TabLayout+Fragment顶部标签界面滑动。

最后

**小弟不才，还望多多指教！**


