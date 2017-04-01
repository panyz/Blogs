## 前言 

> 此笔记摘抄于[ReactNative中文网](http://reactnative.cn/)

#### 入门基础

- 组件（Component）：必须在render方法中返回一些用于渲染结构的JSX语句

- AppRegistry：告知React Native哪一个组件被注册为整个应用的根容器

- 属性(props)：大多数组件在创建时可以使用各种参数来进行定制，用于定制的这些参数被称为props

- 需要用括号来把js变量嵌入到JSX语句中。括号的意思是括号内部为一个js变量或表达式，需要执行后取值。因此可以把任意合法的
javascript表达式通过括号嵌入到JSX语句中

- View组件：常用作于其他组件的容器，来帮助控制布局和样式

- props是在父组件中指定，在被指定的组件的生命周期中则不再改变，对于需要改变的数据
，需要使用state(状态)。一般来说，需要在constructor中初始化state

- 使用典型的场景是在接受到服务器返回的新数据，或者在用户输入数据之后。也可以使用
一些“状态容器”比如Redux来统一管理数据流

- 样式：const style = StyleSheet.create({});

- 弹性（Flex）宽高：在组件样式中使用flex，可以使其在可利用的空间中动态第扩张或收缩。（类似android布局中的layout_weight）
组件能够撑满剩余空间的前提是父容器的尺寸不为0.否则无法显示

- 使用flexbox规则来指定某个子元素的布局，FlexBox可以在不同屏幕尺寸上提供一致的布局结构

- flexDirrection的默认值是column，alignItems的默认值是strech

- flexDirection可以决定布局的主轴，子元素可以沿着水平轴（row）或者竖直轴（column）方向排列

- justifyContent可以决定其子元素沿着子轴的排列方式。flex-start center flex-end space-around space-between

- alignItems可以决定其子元素沿着次轴（与主轴垂直的轴）的排列方式 flex-start center flex-end strech
。要使strech选项生效的话，子元素在次轴上不能有固定的尺寸。

- ListView组件的两个属性必须是dataSource和renderRow。dataSource是列表的数据源，而renderRow则逐个解析数据源中的数据，
然后返回一个设定好格式的组件来渲染

- 要从任意地址获取内容的话，只需要简单地将网址作为参数传递给fetch即可。fetch还有可选的第二个参数，可以用来定制HTTP请求的一些参数

- 组件起那面的export default关键字的意思是导出当前的组件，已允许其他组件引入和使用当前组件

- route（路由）：指包含了场景信息的对象

- renderScene方法是完全根据路由提供的信息来渲染场景

#### 进阶指南

- 静态图片资源 <Image source={require('./my-icon.png')} />

- 可以使用@2x、@3x这样的文件名后缀，来为不同屏幕精度提供图片

- 使用混合App的图片资源 <Image source={{uri:'app_icon'}} style={{width:40,height:40}} />

- 网络图片 <Image source={{uri: 'https://facebook.github.io/react/img/logo_og.png'}}
       style={{width: 400, height: 400}} />

- 本地文件系统中的图片 Images.xcassets

- 在需要捕捉用户点击操作时，可以使用“Touchable”开头的一系列组件。这些组件通过onPress属性接受一个点击事件的处理函数。
当一个点击操作开始并且终止于本组件时（即在本组件上按下手指并且抬起手指时也没有移开到组件外），此函数会被调用

- 全局的布局动画LayoutAnimation、用于创建更精细的交互控制的动画Animated

- Animated仅关注动画的输入与输出声明，在其中建立一个可配置的变化函数，然后使用简单的start/stop方法来控制动画顺序执行

- Animated模块，包括两个值类型，value用于单个值，而valueXY用于向量值；包括三种动画类型，spring，decay，timing，以及三种组件类型
View，Text，Image。可以使用Animated.createAnimatedComponent方法来对其他类型的组件创建动画

- 组合动画：parallel(同时执行)、sequence（顺序执行）、stagger和delay来组合使用

- 插值（interpolate）可以接受一个输入区间，然后将其映射到另一个的输出区间。还支持到字符串的映射，从而可以实现颜色以及带有单位的值的动画变换

- LayoutAnimation允许你在全局范围内创建和更新动画，这些动画会在下一次渲染或布局周期运行。它常用来更新flexbox布局，因为它可以无需测量或者计算特定属性就能直接产生动画。

- 定时器 setTimeout,clearTimeout;setInterval,clearInterval;setImmediate,clearImmediate;requestAnimationFrame,cancleAnimationFrame

- 用InteractionManager来确保在执行繁重工作之前所有的交互和动画都已经处理完毕 InteractionManager.runAfterInteractions(() => {// ...需要长时间同步执行的任务...});

- requestAnimationFrame(): 用来执行在一段时间内控制视图动画的代码
setImmediate/setTimeout/setInterval(): 在稍后执行代码。注意这有可能会延迟当前正在进行的动画。
runAfterInteractions(): 在稍后执行代码，不会延迟当前进行的动画。

- 在项目文件夹下输入npm i react-timer-mixin --save来单独安装TimerMixin
```javascript
var TimerMixin = require('react-timer-mixin');

var Component = React.createClass({
  mixins: [TimerMixin],
  componentDidMount: function() {
    this.setTimeout(
      () => { console.log('这样我就不会导致内存泄露!'); },
      500
    );
  }
});
```

- Mixin属于ES5语法，对于ES6代码来说，无法直接使用Mixin。如果项目是用ES6代码编写，同时又使用了计时器，
那么只需铭记在unmount组件时清除（clearTimeout/clearInterval）所有用到的定时器，那么也可以实现和TimerMixin同样的效果

- 什么时候使用setNativeProps？在（不得不）频繁刷新而又遇到性能瓶颈的时候

