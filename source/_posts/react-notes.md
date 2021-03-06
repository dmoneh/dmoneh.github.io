title: react 学习笔记
date: 2015-11-09 21:15:21
tags: 学习笔记
---
![](/images/react-logo.png)
（持续更新中）
# 环境准备

react是facebook出品的前端lib，利用它，可以非常方面的创建功能强大的组件。

下面是一些小例子，用于记录react的一些比较常用的特性，细节。

<!--more-->

# 准备本地环境
开始写react脚本之前，需要一些相关的库。

* react.js
* react.dom.js

# 练习
## 1 第一个例子
创建一个组件
```
var FirstComponent = React.createClass({
  getInitialState:function(){
    return {};
  },
  render:function(){
    return
    <div>
    Hello World
    </div>
   ;
  }
});
```
然后将组件添加到一个真实存在的dom中
```
ReactDOM.render(
 <FirstComponent/>,
 document.getElementById('mydiv')
);
```
有几个容易出错的点，注意一下：
* 组件名必须以大写字母开始
* 组件中至少要包含 getInitialState 和 render 两个函数才能正常功能

上面提到真实存在的dom ，也就是 mydiv ，与之对应的就是 FirstComponent 这个虚拟dom，这也是react力推的技术 - virtal dom。

## 2 传递数据
很多时候，我们需要向组件中传递一些数据。假设有一个数据对象 data
```
ReactDOM.render(
 <FirstComponent outerData={data},
 document.getElementById('mydiv')
);
```
数据变量名必须用{}括起来。
然后在组件内部，通过 props 对象来获取数据。

```
var data = this.props.outerData;
```
this 代表组件对象，outerData 是传递数据时指定的变量名。

## 3 父子组件
可以在组件 Parent 中，渲染组件 Child ，Child 就是 Parent 的子组件。
```
var Parent = React.createClass({
    getInitialState:function(){
    return {};
   },
   render:function(){
    return
    <div>
    Parent component
    <Child/>
    </div>
   }
  }),
  Child = React.createClass({
   getInitialState:function(){
    return {};
   },
   render:function(){
    return
    <div>
     Child component
    </div>

   }
  });
```
然后，只需要在dom中添加一个 Parent，它的子组件 Child 也会添加进去。

## 4 State
state 表示组件的状态，其实它是一个对象，可以像访问普通对象的方式一样访问它的属性。
```
this.state
```
但是如果想要修改state，必须使用下面这个函数
```
this.setState(function(state){
    return {
    date:new Date()
   }
});
```
需要注意的是，返回的对象并不会替代 state ，而是更新对应的属性。比如，返回的对象包含 date 这个属性，所以 state 的 date 属性会被更新。

## 5 控制组件局部的显示和隐藏
react 支持三元表达式，可以用于显示/隐藏元素
```
render:function(){
 return
  <div>
   {if(this.state.title)?
   <span>{this.state.title}</span>
   :null}
  </div>
  ;
}
```
如上，我们可以通过控制 title 的值，来决定是否显示这个标题

## 6 动态组装组件
render函数的返回结果可以写死，或者根据条件动态组装。
```
render:function(){
 var vdom = [];
 if(this.state.needTitle){
  vdom.push(<span>title</span>);
 }
 return <div>vdom</div>
}
```
## 7 获取 dom 节点对象
有些时候，我们必须获取真实dom，比如获取输入框的值。

指定 ref 属性
```
render:function(){
 return
  <div>
   <input type="text" ref="name" name="name"/>
  </div>
}
```

然后通过 ReactDOM.findDOMNode 来获取 dom 对象
```
var nameDom = ReactDOM.findDOMNode(this.refs.name)
```
nameDom 就是对应的 dom 对象。



