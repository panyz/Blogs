### **前言** ###

这周因为工作上的问题，搞到都没时间写博客了。之前自己对于很多公司上的招聘要求的工作经验都还没什么大概印象，现在倒是明白了。一个困扰自己几天的问题，问了一下有多年经验的人，一下子就能给你提供到突破点的建议。这也明白到自己需要提高的地方太多了。嗯，现在还是一步一步地提高自己吧！

这周这篇给大家带来的是ViewPager系列的最后一篇-图片轮播效果

![图片自动轮播](http://upload-images.jianshu.io/upload_images/2355123-32768597cc853934.gif?imageMogr2/auto-orient/strip)

如果你已经错过上两篇，可以戳下面的传送门哦：D

[一个案例教你简单地玩转ViewPager（一）之带指示点的引导页](https://panyz.github.io/2016/08/22/%E4%B8%80%E4%B8%AA%E6%A1%88%E4%BE%8B%E6%95%99%E4%BD%A0%E7%AE%80%E5%8D%95%E5%9C%B0%E7%8E%A9%E8%BD%ACViewPager%EF%BC%88%E4%B8%80%EF%BC%89%E4%B9%8B%E5%B8%A6%E6%8C%87%E7%A4%BA%E7%82%B9%E7%9A%84%E5%BC%95%E5%AF%BC%E9%A1%B5/)

[一个案例教你简单地玩转ViewPager（二）之ViewPaper+TabLayout+Fragment顶部标签界面滑动](https://panyz.github.io/2016/08/27/%E4%B8%80%E4%B8%AA%E6%A1%88%E4%BE%8B%E6%95%99%E4%BD%A0%E7%AE%80%E5%8D%95%E5%9C%B0%E7%8E%A9%E8%BD%ACViewPager%EF%BC%88%E4%BA%8C%EF%BC%89%E4%B9%8BViewPaper-TabLayout-Fragment%E9%A1%B6%E9%83%A8%E6%A0%87%E7%AD%BE%E7%95%8C%E9%9D%A2%E6%BB%91%E5%8A%A8/)

### **Talk is cheap,show u the code** ###

- 自定义ViewPager-CarouselViewPager

```java
private int displayTime = 3000;//图片展示的时间，默认为3秒
    private CarouselDirection direction = CarouselDirection.LEFT;//图片自动滑动的方向向左

    public CarouselViewPager(Context context) {
        super(context);
    }

    public CarouselViewPager(Context context, AttributeSet attrs) {
        super(context, attrs);
    }

    /**
     * 设置图片展示时间
     * @param time
     */
    public void setDisplayTime(int time){
        displayTime = time;
    }

    /**
     * 设置图片自动滑动的方向
     * @param direction
     */
    public void setDirection(CarouselDirection direction) {
        this.direction = direction;
    }

    /**
     * 开始自动轮播
     */
    public void start(){
        stop();
        postDelayed(automaticDisplay,displayTime);
    }

    /**
     * 停止自动轮播
     */
    public void stop(){
        removeCallbacks(automaticDisplay);
    }

    /**
     * 图片轮播方向枚举类
     */
    public enum CarouselDirection {
        LEFT,RIGHT
    }

    private Runnable automaticDisplay = new Runnable() {
        @Override
        public void run() {
            display(direction);
        }
    };

    /**
     * 图片轮播
     * @param direction
     */
    private synchronized void display(CarouselDirection direction) {
        PagerAdapter pagerAdapter = getAdapter();
        if (pagerAdapter != null ) {
            int count = pagerAdapter.getCount();//图片的张数
            int currentItem = getCurrentItem();//当前展示到第几张

            switch (direction) {
                case LEFT:
                    currentItem++;
                    //当前展示的图片为最后一张时，则返回第一张
                    if (currentItem >= count ){
                        currentItem = 0;
                    }
                    break;
                case RIGHT:
                    currentItem--;
                    //当前展示的图片为最后一张时，则返回第一张
                    if (currentItem < 0){
                        currentItem = count-1;
                    }
                    break;
            }
            setCurrentItem(currentItem);
        }
        start();
    }

    @Override
    protected void onFinishInflate() {
        addOnPageChangeListener(new OnPageChangeListener() {
            @Override
            public void onPageScrolled(int position, float positionOffset, int positionOffsetPixels) {

            }

            @Override
            public void onPageSelected(int position) {

            }

            @Override
            public void onPageScrollStateChanged(int state) {
                if (state == SCROLL_STATE_IDLE){
                    start();
                } else if (state == SCROLL_STATE_DRAGGING) {
                    stop();
                }
            }
        });
    }
```

- 贴上布局

```xml
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
              android:orientation="vertical"
              android:layout_width="match_parent"
              android:layout_height="match_parent">

<com.pyz.viewpagerdemo.ui.CarouselViewPager
    android:id="@+id/mCarouselView"
    android:layout_width="match_parent"
    android:layout_height="300dp">

</com.pyz.viewpagerdemo.ui.CarouselViewPager>

    <!--轮播图片的指示点-->
    <LinearLayout
        android:id="@+id/pointLayout"
        android:layout_width="wrap_content"
        android:layout_height="10dp"
        android:layout_centerHorizontal="true"
        android:layout_below="@+id/mCarouselView"
        android:orientation="horizontal">

    </LinearLayout>

</RelativeLayout>
```

- 在Fragment中使用CarouselViewPager自定义控件

```java
   private CarouselViewPager mCarouselView;
    private List<ImageView> ivList = new ArrayList<ImageView>();
    private int[] ivIds = {R.mipmap.pic1, R.mipmap.pic2, R.mipmap.pic3, R.mipmap.pic4};

    private ImageView[] indicationPoint;//指示点控件
    private LinearLayout pointLayout;

    @Nullable
    @Override
    public View onCreateView(LayoutInflater inflater, @Nullable ViewGroup container,
                             @Nullable Bundle savedInstanceState) {
        View rootView = inflater.inflate(R.layout.fragment2_layout, container, false);
        initViews(rootView);
        initData();
        return rootView;
    }

    private void initViews(View rootView) {
        mCarouselView = (CarouselViewPager) rootView.findViewById(R.id.mCarouselView);
        pointLayout = (LinearLayout) rootView.findViewById(R.id.pointLayout);
    }

    private void initData() {
        for (int i = 0; i < ivIds.length; i++) {
            ImageView iv = new ImageView(getActivity());
            iv.setImageResource(ivIds[i]);
            ivList.add(iv);
        }

        //动态生成指示点
        indicationPoint = new ImageView[ivList.size()];
        for (int i = 0; i < indicationPoint.length; i++) {
            ImageView point = new ImageView(getActivity());
            LinearLayout.LayoutParams layout = new LinearLayout.LayoutParams(10, 10);
            layout.setMargins(10, 0, 10, 0);
            point.setLayoutParams(layout);

            indicationPoint[i] = point;
            if (i == 0) {
                indicationPoint[i].setBackgroundResource(R.mipmap.page_indicator_focused);
            } else {
                indicationPoint[i].setBackgroundResource(R.mipmap.page_indicator_unfocused);
            }
            pointLayout.addView(point);
        }

        mCarouselView.setAdapter(new CarouselPagerAdapter(ivList));
        mCarouselView.addOnPageChangeListener(this);
        //设置图片展示的时间
        mCarouselView.setDisplayTime(2000);
        //图片开始轮播
        mCarouselView.start();

    }

    @Override
    public void onPageScrolled(int position, float positionOffset, int positionOffsetPixels) {

    }

    @Override
    public void onPageSelected(int position) {
        setPointColor(position % ivList.size());

    }

    @Override
    public void onPageScrollStateChanged(int state) {

    }

    private void setPointColor(int selectItem) {
        for (int i = 0; i < indicationPoint.length; i++) {
            if (i == selectItem) {
                indicationPoint[i].setBackgroundResource(R.mipmap.page_indicator_focused);
            } else {
                indicationPoint[i].setBackgroundResource(R.mipmap.page_indicator_unfocused);
            }

        }
    }
```
### **源码下载** ###

[ViewPagerDemo](https://github.com/panyz/ViewPagerDemo)

喜欢的可以点点Star哦！

### **总结** ###

在代码上注释也写得比较清楚明白的了，不过在这篇中大家应该可以看到与第一篇不同的是，这次的指示点是根据图片的张数而动态创建的，这也解决了在布局文件上的直接使用控件造成的代码冗余。通过这三篇的ViewPager系列文章，相信大家可以轻松简单地掌握其用法了吧。

参考文章：[手把手教你用ViewPager自定义实现Banner轮播](http://blog.csdn.net/yanzhenjie1003/article/details/51327392)

最后

**小弟不才，还望多多指教！**

