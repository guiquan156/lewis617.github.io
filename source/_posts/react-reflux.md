
---
title: react+reflux入门教程
date: 2016-01-14 03:26:00
tags: [React, Reflux]
---

为了简化react的flux带来的冗余操作，社区的同仁们给我们带来了很多优秀的轮子，诸如redux，reflux等。今天我们就通过逐行讲解代码实例的方法，感受一番reflux的设计之美。

## 例子

![](http://images2015.cnblogs.com/blog/814069/201601/814069-20160114102503522-1162796830.gif)

这个例子是非常简单的todo例子，学习语言从helloworld开始，学习框架从todo开始，这是我们码农界的文化传统！

## 组件

components/todo.js

    
    
    import React from 'react'
    import Reflux from 'reflux'
    import ReactMixin from 'react-mixin'
    import store from '../stores/store'
    import actions from '../actions/actions'
    
    export default class Todo extends React.Component{
    
      //组件渲染完成后，通过action获取所有的数组，刷新绑定到this.state上
      componentDidMount() {
        actions.getAll();
      }
    
      add(){
        var item =this.refs.item.value;
        this.refs.item.value='';
        actions.add(item);
    
      }
    
      remove(i){
        actions.remove(i);
      }
    
      render() {
        //items用于乘放li的集合
        let items;
        if(this.state.list){
          items=this.state.list.map( (item,i)=> {
                  //设置key是因为react的diff算法，是通过key来计算最小变化的
                  return <li key={i}>
                    {item.name}
                    <button onClick={this.remove.bind(this,i)}>remove</button>
                  </li>
                })
        }
        return (
            <div>
              <input type="text" ref="item"/>
              <button onClick={this.add.bind(this)}>add</button>
              <ul>
                {items}
              </ul>
            </div>
        )
      }
    }
    
    // ES6 mixin写法，通过mixin将store的与组件连接，功能是监听store带来的state变化并刷新到this.state
    ReactMixin.onClass(Todo, Reflux.connect(store));

上述代码，我们干了3件事：

  1. 渲染了一个组件，这个组件包括一个input，一个add按钮，一个列表，列表每项包含名称和remove按钮
  2. 给这个组件添加了几个方法，其中componentDidMount()在组件渲染完成后触发，componentDidMount()、add()和remove()方法分别调用actions的方法去更新状态
  3. 最后一行代码，使用es6的mixin写法，使得组件监听store带来的state变化，并刷新界面。

看到这里，很多没有接触过reflux的同学可能已经晕了，我来图解下reflux的功能流程吧！

![](data:image/png;base64,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)

组件就是用户界面，actions就是组件的动作，store用于执行actions的命令，并返回一个state对象给组件。组件通过state来更新界面。

这里我想说说react和angular的某个相同之处，就是将数据和界面绑定起来，通过操作数据来更新界面（不用苦逼的操作dom了）。我们把数据和界面的规则建好后，更新数据，界面自动就变化了。在这里，数据指的是this.state，界面指的是组件。

那么为何要用actions和store这么多层去更新state呢？为了以后项目业务逻辑变复杂后便于管理。为什么便于管理，因为actions有很多钩子，钩子就是"触发之前，触发之后的回调什么的"，这些钩子我们以后会用得上。

## actions和store两个好基友开始更新状态

actions/actions.js

    
    
    import Reflux from 'reflux'
    
    export default Reflux.createActions(['getAll','add','remove']);

stores/store.js

    
    
    import Reflux from 'reflux'
    import actions from '../actions/actions'
    
    //给数组添加remove方法，用于去除指定下标的元素
    Array.prototype.remove=function(dx)
    {
        if(isNaN(dx)||dx>this.length){return false;}
        for(var i=0,n=0;i<this.length;i++)
        {
            if(this[i]!=this[dx])
            {
                this[n++]=this[i]
            }
        }
        this.length-=1
    };
    
    export default Reflux.createStore({
        items:[],
        //监听所有的actions
        listenables: [actions],
        //on开头的都是action触发后的回调函数
        onGetAll () {
            //更新状态（就是个对象）
            this.trigger({list:this.items});
        },
        onAdd(item){
            this.items.push({name:item});
            this.trigger({list:this.items});
        },
        onRemove(i){
            this.items.remove(i);
            this.trigger({list:this.items});
        }
    });

上述代码，我们干了3件事：

  1. 给数组对象的原型添加一个remove的方法，用于删除指定下标的元素
  2. 创建一个store，监听actions的方法
  3. 在store里，on开头的都是actions对应的回调函数，this.trigger()，负责更新state（这里指的是{list:this.items}这个对象）

## 渲染组件

index.js

    
    
    import React from 'react';
    import ReactDOM from 'react-dom';
    import Todo from './components/todo';
    
    ReactDOM.render(
      <Todo>
      </Todo>,
      document.querySelector('#app')
    );

## 最后，用webpack编译

webpack.config.js

    
    
    var path = require('path');
    var webpack = require('webpack');
    
    module.exports = {
        entry: {
            app:path.join(__dirname, 'src'),
            vendors: ['react','reflux','react-mixin']
        },
        output: {
            path: path.join(__dirname, 'dist'),
            filename: '[name].js'
        },
        module: {
            loaders: [
                {
                    test:/\.js?$/,
                    exclude:/node_modules/,
                    loader:'babel',
                    query:{
                        presets:['react','es2015']
                    }
                }
            ]
        },
        plugins: [
            new webpack.optimize.CommonsChunkPlugin('vendors', 'vendors.js')
        ]
    };

## 总结

相比较redux而言，

  1. reflux没有reducer的概念，取而代之，和action做基友的是store
  2. reflux没有把状态的一部分值绑定在组件的props上，而是将状态绑定在组件的state上，我们来看react dev tool的截图![](data:image/png;base64,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)
  3. reflux可以直接调用action的方法，而redux必须将方法绑定在组件的props上，或者使用props的dispatch方法来执行actions的方法
  4. ……

有此看来，reflux好理解的多，但是redux的单一state是实际项目中是非常好用的，所以，redux在github上的星星比reflux多得多！两个都是社区同仁智慧的结晶，都是优秀的值得学习的轮子！

源代码：

<https://github.com/lewis617/react-redux-tutorial/tree/master/todo-reflux>

运行方法：

npm install

npm run build

手动打开index.html



* * *

# 教程源代码及目录

如果您觉得本博客教程帮到了您，就赏颗星吧！

<https://github.com/lewis617/react-redux-tutorial>


