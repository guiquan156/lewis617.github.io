
---
title: react+redux教程（三）reduce()、filter()、map()、some()、every()、...展开属性
date: 2016-01-21 09:23:00
tags: [React, Redux]
---

reduce()、filter()、map()、some()、every()、...展开属性
这些概念属于es5、es6中的语法，跟react+redux并没有什么联系，我们直接在<https://developer.mozilla.org/en-
US/> 这里可以搜索到相关api文档。

但是redux的官方示例中包含了这些语法的用法，我们正好可以在程序中学习这些语法。这里全部默认使用es6的写法。

# 例子

这是官方的todomvc的例子（<https://github.com/lewis617/react-redux-tutorial/tree/master
/redux-examples/todomvc>）：

![](data:image/png;base64,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)

# reduce()

遍历数组，在每一项元素后面触发一个回调函数，经过计算返回一个累加的值。

components/MainSection.js 62行

    
    
    const completedCount = todos.reduce((count, todo) =>
          todo.completed ? count + 1 : count,
          0
        )



todos是个数组，reduce()的第一个参数是个箭头语法，也就是个回调函数，这个回调函数的第一个参数是上一个返回值（但是这里被初始化为0）,第二个参数是当前元素的值。reduce()的第二个参数是个初始化值（不必需），初始化了上一个元素的值（这里是count）

遍历数组todos的第一个值的时候，count为0，todo是todos的第一项，返回值加一或者不变。（**条件 ? 结果1 : 结果2三元运算）**

遍历数组todos的第二个值的时候，count为上一个返回值，todo是todos的第二项，返回值加一或者不变。

……

遍历结束后，即可得到数组中，completed属性为true的个数，也就是已完成的任务的个数。

# filter()

遍历数组，在每一项元素后面触发一个回调函数，通过判断，保留或移除当前项，最后返回一个新数组。

顾名思义就是过滤。

reducers/todos.js 24行

    
    
    return state.filter(todo =>
            todo.id !== action.id
          )

state是个任务数组，filter()里面只有一个参数，就是个箭头函数，该函数只有一个参数是todo，也就是数组的每一项元素，箭头后面那个判断语句，如果返回true则保留当前项，反之移除当前项。

有的同学会问，todo.id !==
action.id前为什么没有return，这是箭头函数的语法，箭头两端就是输入输出，不用写return。如果用es5的写法就是：

    
    
    return state.filter(function(todo) {  
    　　return todo.id !== action.id  
    　　}  
    )

该代码段的作用是，过滤掉任务数组中，id与指定id相同的任务。返回一个新的任务数组。

# map()

遍历数组，在每一项元素后面触发一个回调函数，通过计算，返回一个新的当前项，最后返回一个新数组。

reducers/todos.js 29行

    
    
    return state.map(todo =>
            todo.id === action.id ?
              Object.assign({}, todo, { text: action.text }) :
              todo
          )

箭头后面的值是个三元运算符，也就是return的新元素。如果id匹配，则通过Object.assign()合并一个新的属性，也就是给todo添加或者重写一个text属性，属性值为action.text。

Object.assign()第一个参数是target，就是目标，第二个第三个以及后面的参数都是source，也就是拷贝的源，是不是很像jquery插件中的extend？

这个代码的作用是给数组中指定的任务更新text属性。

# some()、every()

遍历数组，在每一项元素后面触发一个回调函数，通过判断，返回一个布尔值。some()是只要有一个满足判断，就返回true，every()是只要有一项不满足判断，就返回false。

components/MainSection.js  19 行

    
    
     const atLeastOneCompleted = this.props.todos.some(todo => todo.completed)

遍历任务数组，有一个任务的属性completed为true，就返回true。

reducers/todos.js 43行

    
    
    const areAllMarked = state.every(todo => todo.completed)

遍历任务数组，每一项任务的completed属性均为true时候，返回true。

# ...展开属性

reducers/todos.js 20行

    
    
    return [
            {
              id: state.reduce((maxId, todo) => Math.max(todo.id, maxId), -1) + 1,
              completed: false,
              text: action.text
            }, 
            ...state
          ]

展开state数组的每一项到当前的数组

components/MainSection.js  72 行

    
    
    <TodoItem key={todo.id} todo={todo} {...actions} />

展开actions的每一个属性到组件中，最后在props上可以获取到。

* * *

# 教程源代码及目录

如果您觉得本博客教程帮到了您，就赏颗星吧！

<https://github.com/lewis617/react-redux-tutorial>


