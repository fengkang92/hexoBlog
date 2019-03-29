---
title: '浅谈javaScript深浅拷贝'
date: 2019-03-28 16:06:21
tags:
- JavaScript
- 深拷贝
categories: 
- JavaScript
---

最近遇到了不少深浅拷贝的问题，自己也查阅了不少资料，在这里总结一下，也希望能帮助到更多有需要的小伙伴~

<!--more-->

## 基本数据类型和引用数据类型
在JS中，分为两种数据类型。其中基本数据类型指的是简单的数据段，包括Number，String，Null，Undefined，Boolean五种。
引用数据类型指的是**可能包含多个值的对象**，比如Object，Array，函数等。
基本数据类型保存在栈内存，而引用类型保存在堆内存中。那再进一步问：为什么要分两种保存方式呢？根本原因在于保存在栈内存的必须是大小固定的数据，引用类型的大小不固定，只能保存在堆内存中，但是我们可以把它的地址写在占内存中以供我们访问。
```
var a = 1;//定义了一个number类型
var obj1 = {//定义了一个objr类型
    name:'obj'
};
```
这段代码执行后，在内存中会是这样的：
![image.png](https://upload-images.jianshu.io/upload_images/6337462-dd994e89ddb2dc74.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
因为这种保存方式的存在，所以我们在操作变量的时候，**如果是基本数据类型，则按值访问，操作的就是变量保存的值**；**如果是引用类型的值，我们只是通过保存在变量中的引用类型的地址类操作实际对象**。

## 基本数据类型的复制方式
```
var a = 1;
var b = a;//复制
console.log(b)//1
a = 2;//改变a的值
console.log(b)//1
```
在上面代码中，我们复制完b以后，即使改变a的值，b也不会改变，因为a和b是相互独立的，按照上面的图，也就是在栈内存中创建了一个变量b 保存的值也是2。
![image.png](https://upload-images.jianshu.io/upload_images/6337462-efe701ce18d2367a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 引用类型的复制方式
```
var color1 = ['red','green'];
var color2 = color1;//复制
console.log(color2)//['red','green'];
color1.push('black') ;//改变color1的值
console.log(color2)//['red','green','black']
```
我们只是**复制了一次引用类型的地址**而已，所以，不管接下来我们是操作color1还是color2，本质上都是操作同一个数组对象。
![image.png](https://upload-images.jianshu.io/upload_images/6337462-9bdbc91a4ab65df1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 浅拷贝
如果我们想得到上面color1的数组的复制，我们可以这样：
```
var color1 = ['red','green']; 
var color2 = [];
//复制
for(var i = 0;i < color1.length;i++){
    color2[i] = color1[i]; 
}
console.log(color2)//['red','green'];
color1.push('black') ;//改变color1的值
console.log(color2)//['red','green']
```
新建一个空的数组color2，去循环遍历数组color1中的每一项，然后把他的值复制给color2的每一项，这样color1和color2是相互独立的。同样的，对于对象，我们可以采用for in的方法去遍历每一项，得到两个互相独立的对象。

这样的方法表面上看是解决了问题，但其实还是属于浅拷贝。**从直接使用 = 符号赋值进行拷贝到浅拷贝，能够拷贝成功(成功是指拷贝的结果与拷贝源完全独立)，是因为我们拷贝的对象都是基本类型**。
```
function simpleClone(initalObj) {
    var obj = {};
    for ( var i in initalObj) {
        obj[i] = initalObj[i];
    }
    return obj;
}

var obj = {
    a: "hello",
    b: {
        a: "world",
        b: 21
    },
    c: ["Bob", "Tom", "Jenny"],
    d: function() {
        alert("hello world");
    }
}
var cloneObj = simpleClone(obj); // 对象拷贝
console.log(cloneObj.b); // {a: "world", b: 21}
console.log(cloneObj.c); // ["Bob", "Tom", "Jenny"]
console.log(cloneObj.d); // function() { alert("hello world"); }

// 修改拷贝后的对象
cloneObj.b.a = "changed";
cloneObj.c = [1, 2, 3];
cloneObj.d = function() { alert("changed"); };
 
console.log(obj.b); // {a: "changed", b: 21} // // 原对象所引用的对象被修改了
 
console.log(obj.c); // ["Bob", "Tom", "Jenny"] // 原对象所引用的对象未被修改
console.log(obj.d); // function() { alert("hello world"); } // 原对象所引用的函数未被修改
```
大家可以看上面代码，在使用for in进行循环后，我们改变`cloneObj.b.a`的值后，对应的 `obj.b.a`的值也被改变了。
其实我们在复制`cloneObj.b`的时候，因为他是一个对象，是一个引用数据类型，我们我们在使用`=`进行复制的时候，对`obj.b`的复制，又变成了我们前面提到的只复制了地址的情况，而没有去对对象的每一个值进行复制。

## 深拷贝
从上面浅拷贝的方法来看，我们可以这样来实现深拷贝：
* 在复制基本数据类型时，我们直接使用 = 完成复制
* 在复制所有引用类型的时候，我们循环遍历对象，对每个属性或值使用 = 完成复制

所以我们可以采用递归的方式去进行：
```
// 对于传入的参数，首先判断是否为引用类型，如果不是，直接返回即可；
//如果是，循环遍历该对象的属性，如果某个属性还是引用类型，则针对该属性再次调用deepCopy函数，从而完成深复制
function deepCopy (obj) {
    var result;

    //引用类型分数组和对象分别递归
    if (Object.prototype.toString.call(obj) == '[object Array]') {
      result = []
      for (i = 0; i < obj.length; i++) {
        result[i] = deepCopy(obj[i])
      }
    } else if (Object.prototype.toString.call(obj) == '[object Object]') {
      result = {}
      for (var attr in obj) {
        result[attr] = deepCopy(obj[attr])
      }
    }
    //值类型直接返回
    else {
      return obj
    }
    return result
}

```
## Object.assign()
对于一个简单的对象，即对象中每个属性的值都是一个基本数据类型，可以使用Object.assign()完成浅拷贝。
**Object.assign()拷贝的是属性值。假如源对象的属性值是一个对象的引用，那么它也只指向那个引用。**
所以它只能进行浅拷贝，当然如果目标对象只是一个简单的对象，其中每个属性的值均为基本数据类型，可以达到拷贝结果与拷贝源互不影响的效果。

## Array.slice()
这个方法可以完成对数组的浅拷贝。条件同Object.assign()一样，即数组的每个元素均为基本数据类型。

## JSON对象的parse和stringify
我们还可以借用JSON对象的这两个方法来实现深拷贝：
```
function deepClone(obj){
    let _obj = JSON.stringify(obj),
        objClone = JSON.parse(_obj);
    return objClone
}    
let a=[0,1,[2,3],4],
    b=deepClone(a);
a[0]=1;
a[2][0]=1;
console.log(a,b);
```
![image.png](https://upload-images.jianshu.io/upload_images/6337462-62ec2f2391db986f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

当然这个方法有一定的局限性，就是当对象中含有函数的时候，无法进行处理。
```
const originObj = {
 name:'axuebin',
 sayHello:function(){
 console.log('Hello World');
 }
}
console.log(originObj); // {name: "axuebin", sayHello: ƒ}
const cloneObj = JSON.parse(JSON.stringify(originObj));
console.log(cloneObj); // {name: "axuebin"}
```
这是因为undefined、function、symbol 会在转换过程中被忽略。

参考文章：
https://segmentfault.com/a/1190000008838101
https://www.cnblogs.com/echolun/p/7889848.html
