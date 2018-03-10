### javascript中this的使用
This指针的使用：
谁调用就指向谁，比如：
> window.m=100
function test(){
alert(this.m)
}
test();
再比如：
this.m=1000
var obj={
m:100,
test:function(){
    alert(this.m)
  }
}
obj.test();
输出100

**这其中有一个特殊情况，就是闭包**


**总而言之，this就是谁调用它就指向谁**
在一个方法内部，this是一个特殊变量，它始终指向当前对象
* 作用域与闭包：
JS是函数级作用域，在内部的变量，内部都能访问，外部的不能访问内部的，但是内部的可以访问外部的。
～把函数作为表达式
函数外拿函数里面的值叫做闭包
