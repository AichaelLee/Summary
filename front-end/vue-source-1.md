## Vue的本质

Vue的本质就是用一个Function实现的Class，然后在它的原型prototype和本身上面扩展一些属性和方法。

它的定义是在src/core/instance/index.js里面定义

使用ES5的方式，即用函数来实现一个class，不用ES6来实现class的原因：在ES5中，是可以往Vue的原型上挂很多方法，并且可以将不同的原型方法拆分到不同的文件下，这样方便代码的管理，不用再单个文件上把Vue的原型方法都定义一遍

Vue中的全局方法定义在src/core/global-api里面：定义Vue的全局配置

## 一：数据驱动

Vue.js 一个核心思想是数据驱动。所谓数据驱动，是指视图是由数据驱动生成的，对视图的修改，不会直接操作 DOM，而是通过修改数据。它相比我们传统的前端开发，如使用 jQuery 等前端库直接修改 DOM，大大简化了代码量。特别是当交互复杂的时候，只关心数据的修改会让代码的逻辑变的非常清晰，因为 DOM 变成了数据的映射，我们所有的逻辑都是对数据的修改，而不用碰触 DOM，这样的代码非常利于维护

可以采用简洁的模板语法来声明式的将数据渲染为 DOM

数据驱动的两个核心思想：模板和数据是如何渲染成最终的DOM；数据更新驱动视图变化

问题：vue中模板和数据如何渲染成最终的DOM？？？？

## 2、new Vue()发生了什么？

1）new关键字实例化一个对象，Vue()是一个类，在js中类用Function定义

2）在Vue()函数中调用初始化函数：Vue 初始化主要就干了几件事情，合并配置，初始化生命周期，初始化事件中心，初始化渲染，初始化data、props、computed、watcher 等等。Vue 的初始化逻辑写的非常清楚，把不同的功能逻辑拆成一些单独的函数执行，让主线逻辑一目了然

3）在初始化的最后，检测到如果有 el 属性，则调用 vm.$mount 方法挂载 vm，挂载的目标就是把模板渲染成最终的 DOM

## 在data()属性里面定义了变量之后，就可以通过this.变量名访问到在data()里面定义的变量，这是为什么呢？

通过在初始化函数中调用initState(vm)，然后调用初始化data的函数initData()，在里面将数据赋给vm_.data，然后通过proxy()将vm._data.key替换成vm.key，在proxy()中对vm._data.key设置setter  getter，
然后通过Object.defineProperty(target,key,sharedPropertyDefinition)来实现代理的

## Virtual  DOM：用VNode类描述

用一个原生的 JS 对象去描述一个 DOM 节点，所以它比创建一个 DOM 的代价要小很多。在 Vue.js 中，Virtual DOM 是用 VNode 这么一个 Class 去描述，它是定义在 src/core/vdom/vnode.js 中的

 VNode 是对真实 DOM 的一种抽象描述，它的核心定义无非就几个关键属性，标签名、数据、子节点、键值等，其它属性都是用来扩展 VNode 的灵活性以及实现一些特殊 feature 的。由于 VNode 只是用来映射到真实 DOM 的渲染，不需要包含操作 DOM 的方法，因此它是非常轻量和简单的

 

Virtual DOM映射到真实的DOM上要经历过VNode的create    diff   patch等过程，Vnode的create是通过createElement()方法创建的

## vm.optionn
把 Vue 上的一些 option 扩展到了 vm.$option 

mergeOptions()的作用：把 Vue 构造函数的 options 和用户传入的 options 做一层合并到 vm.$options 上

 
## vue.extend
Vue.extend()：src/core/global-api/extend.js

作用：就是构造一个 Vue 的子类，使用原型继承的方式把一个纯对象转换一个继承于 Vue 的构造器 Sub 并返回

然后对 Sub 这个对象本身扩展一些属性，如扩展 options、添加全局 API 等

对配置中的 props 和 computed 做了初始化工作

最后对这个 Sub 构造函数做了缓存，避免多次执行 Vue.extend 的时候对同一个子组件重复构造

 

实例化 Sub 的时候，就会执行 this._init 逻辑再次走到了 Vue 实例的初始化逻辑



