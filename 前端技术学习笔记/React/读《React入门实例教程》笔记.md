### 前言 ###

> 此笔记摘抄于阮一峰大神的博客《React入门实例教程》。

- 凡是使用JSX的地方，都要加上 type="text/babel" 。

- React.js:核心库，react-dom.js:提供与dom相关功能，brower.min.js：将JSX语法转为javascript语法。

- JSX语法：HTML语法直接写在javascript中，不叫任何引号，允许HTML与javascript混写。

- JSX语法规则，遇到HTML标签，就用HTML规则解析，遇到代码块就用javascript规则解析。

- React允许将代码封装成组件（component），然后向插入HTML标签一样，使用这个组件。React.createClass()方法就用与生成一个组件类。

- 所有组件类都必须有自己的render方法，用于输出组件。注意：组件类第一个字母必须要大写，只能包含一个顶层标签。

- 组件的属性可以在组件类的this.props对象上获取。注意：class要写成className，for写成htmlFor。

- this.props.Children属性，表示组件的所有子节点。如果没有子节点它就是undefined，如果只有一个子节点，数据类型就是object，
如果有多个子节点，数据类型就是array。可用React.Children.map()方法来遍历子节点。

- 组件类的PropTypes属性，用来验证组件实例属性是否符合要求，getDefaultProps方法可用来设置组件属性的默认值。

- 组件看成是一个状态机，一开始有一个初始状态，然后用户互动，导致状态变化，从而触发重新渲染UI。

- this.props表示那些一旦定义了就不再改变的特性，this.state是会随着用户互动而产生变化的特性。

- 组件的生命周期：

    - Mounting:已插入真实的DOM；
    - Updating:正在被重新渲染；
    - Unmounting:已移出真实DOM；

- 组件生命周期处理函数（will在进入状态前调用，did在进入状态后调用）：

    - componentWillMount();
    - componentDidMount();
    - componentWillUpdate(object nextProps, object nextState);
    - componentDidUpdate(object nextProps, object nextState);
    - componentWillUnmount();

- 特殊状态的处理函数

    - componentWillReceiveProps(object nextProps);//已加载组件收到新的参数时调用
    - shouldComponentUpdate(object nextProps, object nextState);//组件判断是否重新渲染时调用
