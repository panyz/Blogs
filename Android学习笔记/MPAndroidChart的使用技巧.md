### **前言** ###
因为在之前的一些项目用到图表的次数较多，如果由自己一步步来画的话，目前的水平和效果或者完成不了需求。而且现在网上的关于图表的“轮子”也是比较丰富的，所以决定要找个“轮子”来玩玩。方便以后要是再需要用到图表这样的情况下，自己能熟悉地运用到。

打开github，在输入框上输入Android Charts就罗列出了一大堆比较火的图表开源项目。在一一浏览之下，最后我选择了一个叫hellocharts-android的项目（--!，为什么标题还写MPAndroidChart）。在折腾一两天之后，发现丫的这个项目最后的一些效果不太符合我想要的那种效果，就弃用了。最后就选择了这次我要写的这个开源项目MPAndroidChart。（这个故事教训我们，怎样在网上搜索适合的资源也是一门学问，如果选错了，多浪费时间和精力啊！）好了，说了这么多废话，现在言归正传吧。

### **使用教程** ###
- **获取依赖**

一般我们想要用上github上的开源项目时，都是直接在app模块下的build.gradle文件里直接compile。但这次我是直接把项目下载下来，再把MPChartLib导入到自己的项目中，方便以后直接拿过来用。（PS：在这里我把MPChartLib改名为JNChartLib了，因为我在其中做了一些简单的封装）

- **导入JNChart依赖包**
 - File-> New->Import Moudle,在路径上选择JNChartLib。


 - 打开setting.gradle文件 `include ':JNChartLib'`


 - 打开app模块下的build.gradle文件 `compile project (':JNChartLib')`


- **图表控件（以折线图为例）**

在这个项目中我的基础显示布局采用的是[RadioButton+Fragment实现底部导航栏](https://panyz.github.io/2016/08/01/RadioButton-Fragment%E5%AE%9E%E7%8E%B0%E5%BA%95%E9%83%A8%E5%AF%BC%E8%88%AA%E6%A0%8F/) (感兴趣的可以戳进入看看哦！)嗯，一般我学习新的技术点需要敲代码练练手都是用这样的布局。所以这次的图表控件都放在Fragment的布局上。

```xml
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
              android:orientation="vertical"
              android:layout_width="match_parent"
              android:layout_height="match_parent">
    <com.jn.chart.charts.LineChart
        android:id="@+id/lineChart"
        android:layout_width="match_parent"
        android:layout_height="300dp"/>
</LinearLayout>
```

- **在Fragment上初始化图表控件，设置数据等**
```java
public class Fragment1 extends android.support.v4.app.Fragment {
    private LineChart mLineChart;
    private Context context;
    @Nullable
    @Override
    public View onCreateView(LayoutInflater inflater, @Nullable ViewGroup container, @Nullable Bundle savedInstanceState) {
        View view = inflater.inflate(R.layout.fragment1_layout, container, false);
        this.context = getActivity();
        mLineChart = (LineChart) view.findViewById(R.id.lineChart);
        //设置图表的描述
        mLineChart.setDescription("全省移网");

        //设置x轴的数据
        ArrayList<String> xValues = new ArrayList<>();
        for (int i = 0; i < 15; i++) {
            xValues.add("" + i);
        }

        //设置y轴的数据
        ArrayList<Entry> yValue = new ArrayList<>();
        yValue.add(new Entry(13, 1));
        yValue.add(new Entry(6, 2));
        yValue.add(new Entry(3, 3));
        yValue.add(new Entry(7, 4));
        yValue.add(new Entry(2, 5));
        yValue.add(new Entry(5, 6));
        yValue.add(new Entry(12, 7));
        //设置折线的名称
        LineChartManager.setLineName("当月值");
        //创建一条折线的图表
        //LineChartManager.initSingleLineChart(context,mLineChart,xValues,yValue);

        //设置第二条折线y轴的数据
        ArrayList<Entry> yValue1 = new ArrayList<>();
        yValue1.add(new Entry(17, 1));
        yValue1.add(new Entry(3, 2));
        yValue1.add(new Entry(5, 3));
        yValue1.add(new Entry(4, 4));
        yValue1.add(new Entry(3, 5));
        yValue1.add(new Entry(7, 6));
        yValue1.add(new Entry(10, 7));
        LineChartManager.setLineName1("上月值");
        //创建两条折线的图表
        LineChartManager.initDoubleLineChart(context,mLineChart,xValues,yValue,yValue1);
        return view;
    }
    
}
```

代码中的LineChartManager类就是我把图表的创建，图表的样式，坐标轴的样式的一个封装类，这样我们只需要在Activity中或Fragment中直接设置好图表的数据，图表的描述等就行，无须关心其他属性的设定，从代码中的注释相信大家应该都能看懂吧。当然，如果你想把图表定制成你想要的样子，可以直接在LineChartManager这个封装类中修改。下面来看看这个类吧。 
```java
public class LineChartManager {

    private static String lineName = null;
    private static String lineName1 = null;

    /**
     * @Description:创建两条折线
     * @param context 上下文
     * @param mLineChart 折线图控件
     * @param xValues 折线在x轴的值
     * @param yValue 折线在y轴的值
     */
    public static void initSingleLineChart(Context context, LineChart mLineChart, ArrayList<String> xValues,
                                           ArrayList<Entry> yValue) {
        initDataStyle(context,mLineChart);
        //设置折线的样式
        LineDataSet dataSet = new LineDataSet(yValue, lineName);
        dataSet.setColor(Color.RED);
        dataSet.setCircleColor(Color.RED);
        dataSet.setDrawValues(false);
//        dataSet.setValueFormatter(new PercentFormatter(new DecimalFormat("%").format()));

        ArrayList<ILineDataSet> dataSets = new ArrayList<>();
        dataSets.add(dataSet);

        //构建一个LineData  将dataSets放入
        LineData lineData = new LineData(xValues, dataSets);
        //将数据插入
        mLineChart.setData(lineData);

        //设置动画效果
        mLineChart.animateY(2000, Easing.EasingOption.Linear);
        mLineChart.animateX(2000, Easing.EasingOption.Linear);
        mLineChart.invalidate();
    }
    /**
     * @Description:创建两条折线
     * @param context 上下文
     * @param mLineChart 折线图控件
     * @param xValues 折线在x轴的值
     * @param yValue 折线在y轴的值
     * @param yValue1 另一条折线在y轴的值
     */
    public static void initDoubleLineChart(Context context, LineChart mLineChart, ArrayList<String> xValues,
                                           ArrayList<Entry> yValue, ArrayList<Entry> yValue1) {

        initDataStyle(context,mLineChart);

        LineDataSet dataSet = new LineDataSet(yValue, lineName);
        dataSet.setColor(Color.RED);
        dataSet.setCircleColor(Color.RED);
        dataSet.setDrawValues(false);

        LineDataSet dataSet1 = new LineDataSet(yValue1, lineName1);
        dataSet1.enableDashedLine(10f, 10f, 0f);//将折线设置为曲线
        dataSet1.setColor(Color.parseColor("#66CDAA"));
        dataSet1.setCircleColor(Color.parseColor("#66CDAA"));
        dataSet1.setDrawValues(false);

        //构建一个类型为LineDataSet的ArrayList 用来存放所有 y的LineDataSet   他是构建最终加入LineChart数据集所需要的参数
        ArrayList<ILineDataSet> dataSets = new ArrayList<>();

        //将数据加入dataSets
        dataSets.add(dataSet);
        dataSets.add(dataSet1);

        //构建一个LineData  将dataSets放入
        LineData lineData = new LineData(xValues, dataSets);
        //将数据插入
        mLineChart.setData(lineData);
        //设置动画效果
        mLineChart.animateY(2000, Easing.EasingOption.Linear);
        mLineChart.animateX(2000, Easing.EasingOption.Linear);
        mLineChart.invalidate();
    }

    /**
     *  @Description:初始化图表的样式
     * @param context
     * @param mLineChart
     */
    private static void initDataStyle(Context context, LineChart mLineChart) {
        //设置图表是否支持触控操作
        mLineChart.setTouchEnabled(true);
        mLineChart.setScaleEnabled(false);
        //设置点击折线点时，显示其数值
//        MyMakerView mv = new MyMakerView(context, R.layout.item_mark_layout);
//        mLineChart.setMarkerView(mv);
        //设置折线的描述的样式（默认在图表的左下角）
        Legend title = mLineChart.getLegend();
        title.setForm(Legend.LegendForm.LINE);
        //设置x轴的样式
        XAxis xAxis = mLineChart.getXAxis();
        xAxis.setPosition(XAxis.XAxisPosition.BOTTOM);
        xAxis.setAxisLineColor(Color.parseColor("#66CDAA"));
        xAxis.setAxisLineWidth(5);
        xAxis.setDrawGridLines(false);
        //设置是否显示x轴
        xAxis.setEnabled(true);

        //设置左边y轴的样式
        YAxis yAxisLeft = mLineChart.getAxisLeft();
        yAxisLeft.setAxisLineColor(Color.parseColor("#66CDAA"));
        yAxisLeft.setAxisLineWidth(5);
        yAxisLeft.setDrawGridLines(false);

        //设置右边y轴的样式
        YAxis yAxisRight = mLineChart.getAxisRight();
        yAxisRight.setEnabled(false);

    }

    /**
     * @Description:设置折线的名称
     * @param name
     */
    public static void setLineName(String name){
        lineName = name;
    }

    /**
     * @Description:设置另一条折线的名称
     * @param name
     */
    public static void setLineName1(String name){
        lineName1 = name;
    }
}
```

- **备注**


在这次案例中，在代码上我只封装了折线图，柱状图和折线柱状混合图这三种图表，上面讲解了折线图的用法，其他两种图表的用法也很相似。柱状图的创建封装在了BarChartManager类中，混合图的创建封装在了CombinedChartManager类中。更多具体用法我就不贴代码了，可直接去我的github上直接clone下来参考。其中我还重写了MarkView类，作用是当点击折线点或柱状图时，会弹出一个视图显示其数值。要使用这个效果，就必须单独创建一个只有textview的布局界面，然后再需要用到的管理类中调用,如下：

```java
//设置点击折线点时，显示其数值
MyMakerView mv = new MyMakerView(context, R.layout.item_mark_layout);
mLineChart.setMarkerView(mv);
```

- **API**


这个项目的具体的API调用，可参考文档
[https://jitpack.io/com/github/PhilJay/MPAndroidChart/v3.0.0-beta1/javadoc/](https://jitpack.io/com/github/PhilJay/MPAndroidChart/v3.0.0-beta1/javadoc/)

我还找到一个简洁版的（不过是繁体字）
[https://github.com/25sprout/ChartLib-Demo-Android#ValueFormatter](https://github.com/25sprout/ChartLib-Demo-Android#ValueFormatter)

- **案例代码的github地址**

>  [JNChartDemo](https://github.com/panyz/JNChartDemo)

- **效果图**
![LineChart](http://img.blog.csdn.net/20160802171848169)
![BarChart](http://img.blog.csdn.net/20160802171941576)
![CombinedChart](http://img.blog.csdn.net/20160802172010107)