# React 生命周期钩子(React v16.8)
# 概念---什么是生命周期钩子
React官方文档中说到：在组件类上声明特殊的方法，当组件挂载或卸载时，来运行一些代码，这些方法被称作生命周期钩子。

# 生命周期图解
![clipboard.png](https://image-static.segmentfault.com/128/212/1282127980-5c8879dbc8086_articlex)

在官方文档给出的组件生命周期的图中，我们可以很清楚的认识到，一个React组件的生命周期主要分为3个阶段：创建时、更新时、卸载时。
## 创建时阶段
当组件处于初始化阶段时，依次调用：

 1. defaultProps（默认状态，一般用于如果父组件调用子组件的时候不给子组件传值，可以在子组件中使用defaultProps定义的默认值）
```
// defaultProps初始化props，只会调用一次
class Hello extends React.Component {
    static defaultProps = {
      name: 'Lily',
      age: 13
    }
    render: function(){
            return (
                <div>Hello,{this.props.name}</div>
            )
        }
}
```
`注意: React.PropTypes 自 React v15.5 起已弃用，官方推荐使用使用`[prop-types][1]库`代替`。
```
// 类型检测
import PropTypes from 'prop-types';

class Greeting extends React.Component {
  render() {
    return (
      <h1>Hello, {this.props.name}</h1>
    );
  }
}

Greeting.propTypes = {
  name: PropTypes.string
};
```


----------


 2. constructor

> If you don’t initialize state and you don’t bind methods, you don’t need to implement a constructor for your React component.

> The constructor for a React component is called before it is mounted. When implementing the constructor for a React.Component subclass, you should call super(props) before any other statement. Otherwise, this.props will be undefined in the constructor, which can lead to bugs.   


----------
官方文档中描述到`constructor`只是是用来初始化状态和为实例绑定函数的，并且在组件挂载之前被调用。

```
class Hello extends React.Component {
  constructor(props) {
    // 
    super(props);
    // 此处不可以调用this.setState();
    this.state = {data: 'Hello world'};
  }

  render() {
    return (
      <div>
        <h1>{this.state.data}</h1>
      </div>
    );
  }
}
```
`同时也要注意，this.state = { color: props.color };这种写法是不正确的。并且避免在constructor中声明介绍 side-effects（副作用） 或者 subscriptions（订阅）`   


----------


 3\. componentWillMount（UNSAFE_componentWillMount()）
  componentWillMount发生在组件挂载之前 ，在render之前调用，因此在此方法中同步调用setState（）不会触发额外的渲染。
`componentWillMount也不能声明介绍 side-effects（副作用） 或者 subscriptions（订阅）`


----------


 4\. render
render()方法是唯一一个类组件必需的方法。当调用时会检查this.props 和 this.state，然后返回以下类型：
* React elements，类似于： <div /> ，<MyComponent />。
* Arrays and fragments。Fragments可以让你在不新增DOM节点的同时返回一个组件列表。
```
render() {
  return (
    <React.Fragment>
      <ChildA />
      <ChildB />
      <ChildC />
    </React.Fragment>
  );
}
```
* Portals。正常情况下，子组件挂载在最接近的父节点下，但是Portals可以将子级呈现到父组件的DOM层次结构之外的DOM节点中，`ReactDOM.createPortal(child, container)`包含两个参数：child---任意可渲染的 React 组件， container--- DOM节点。
```
render() {
  // React does *not* create a new div. It renders the children into `domNode`.
  // `domNode` is any valid DOM node, regardless of its location in the DOM.
  return ReactDOM.createPortal(
    this.props.children,
    domNode
  );
}
```
* String 和 numbers。 
* Booleans 和 null。
 
render 是纯粹的, 不能在 render 方法中修改组件状态，同时 render 也不直接和浏览器交互。

----------


   
 5\. componentDidMount
componentDidMount() 在组件挂载之后立即调用，可以在此处初始化需要的节点，也可以在此处调用服务和事件订阅。因为该方法被调用时，真实DOM已经被渲染，所以可以用来访问DOM节点，类似于Modal、tooltips等组件。官方文档建议尽量不要在此方法内使用setState()，因为尽管用户不会看见中间状态，但是也可能引发性能问题。

## 更新时阶段
在更新时阶段，也可以说是运行时阶段，组件已经渲染完成，用户可以和组件进行交互，并触发状态改变，进入新的生命周期。
 1\. componentWillReceiveProps
在一个已经挂载完成的组件的收到一个新的props，将会调用componentWillReceiveProps方法去比较 `this.props` 和 `nextProps `是否不一样，然后this.setState()更新state。
```
componentWillReceiveProps(nextProps){
    if(nextProps.value === this.props.value){
        this.setState({...});
    }
}
```


----------


 2\. shouldComponentUpdate
shouldComponentUpdate(),是生命周期的一个性能优化点，能够知道组件的输出是否不受当前状态或属性更改的影响。我们可以在这个方法里通过返回 false 来阻止组件的重新渲染，返回 false 则不会执行 render ，componentWillUpdate，componentDidUpdate 方法。但是返回 false 不会阻止子组件在状态改变后重新渲染。

```
shouldComponentUpdate(nextProps, nextState){
    return true;
}
``` 


----------


 3\. componentWillUpdate
componentWillUpdate 方法在组件接收到了新的props或者state即将重新渲染之前被调用。但是注意不能在此方法使用this.setState()，也不能在componentWillUpdate()方法返回之前进行其他能够更新React 组件的操作。
 
`UNSAFE_componentWillUpdate(nextProps, nextState)`


----------


 4\. componentDidUpdate 
componentDidUpdate(object prevProps, object prevState) 方法在组件重新被渲染之后会被调用，可以在这里操作 DOM和状态修改。在修改状态时，要注意setState() 方法要在条件嵌套中调用，否则会引起死循环。
```
componentDidUpdate(prevProps) {
  if (this.props.value !== prevProps.value) {
    this.fetchData(this.props.value);
  }
}
```
## 卸载时阶段
componentWillUnmount 方法在组件卸载和销毁之前被调用，可以在这个方法里执行清除。比如，定时器和事件订阅，以及撤销网络请求。

# 总结
![组件生命周期][2]
## 关于内容
React官方文档特别详述了生命周期钩子函数以及各个钩子函数的关系，个人建议从官方文档阅读总结。
## 关于文章
第一次写技术文章，可能很多地方写的不是很好，希望大家多多指正，争取在下一篇，写的更好（这可能是个flag）。
 

# 疑问
1、在官方文档中componentWillMount有一句描述，个人不是很理解：This is the only lifecycle method called on server rendering. 希望有大佬可以为我答疑。
# 参考文章
[React组件][3]
[React： 组件的生命周期][4]  


  [1]: https://www.npmjs.com/package/prop-types
  [2]: /img/bVbpG5G
  [3]: https://zh-hans.reactjs.org/docs/react-component.html
  [4]: https://segmentfault.com/a/1190000004168886?utm_source=tag-newest#articleHeader10