---
layout: post
title: js中=连续复制的分析详解和创建链表
date: 2019-12-30
tags: JavaScript   
---

### 1、var和let定义的差别

```js
//var为全局定义变量，let为局部定义变量
//一般Vue项目开发中推荐使用let定义变量，以防止变量污染
var x = 100;
let y = 100;
{
	var x = 200;
  let y = 200;
  console.log("x:"+x, "y:"+y);//x:200 y:200
}
console.log("x:"+x, "y:"+y);//x:200 y:100

Object.getOwnPropertyDescriptor(globalThis, "x");
//{value: 200, writable: true, enumerable: true, configurable: false}
Object.getOwnPropertyDescriptor(globalThis, "y");
//undefined
```

### 2、默认全局变量

```js
//不进行声明的变量等价于var声明的全局变量，但不完全等价
//1、进行var申明的变量会将变量名存入变量名列表
//2、不进行声明的变量名不会存入变量名列表
//不进行声明的变量在严格模式下会报错，项目中不建议使用
x = 100;
var y = 100;
{
  x = 200;
  var y = 200;
  z = 200;
  console.log("x:"+x, "y:"+y);//x:200 y:200
}
console.log("x:"+x, "y:"+y);//x:200 y:200
console.log("x:"+x, "y:"+y, "z:"+z,);//x:200 y:200 z:200

Object.getOwnPropertyDescriptor(globalThis, "x");
//{value: 200, writable: true, enumerable: true, configurable: true}
Object.getOwnPropertyDescriptor(globalThis, "y");
//{value: 200, writable: true, enumerable: true, configurable: false}

//因为不进行声明的变量的configurable属性描述为true，意味着属性可修改，可执行delete操作
delete x; //true
delete y; //false
```

### 3、等号连续赋值分析

#### A、var x=y=100

```js
//对于基本类型赋值的语句，没什么特别之处
//关键注意点是=中间的变量是未声明的全局变量
var x=y=100;
//这个表达式等价于
var x=100;
y=100;
//或者
y=100;
var x=y;
```

#### B、node.next=node=new Object();

```js
//1、场景1
let node = {name:"美男子"};
node.next = node;

console.log(node); 
//{name: "美男子", next: {name: "美男子", next: {name: "美男子", next: {…}}}}
//执行如下图场景1所示
//结论：这个现场从js的对象栈内存和堆内存存储角度就能理解
```

![image-20200525151117623](https://mashaojie.club/download/source/typora-user-images/image-20200525151117623.png)

```js
//2、场景2
let node = {name:"美男子"};
node.next = node = {newName:"我怎么这么好看！"};

console.log(node);
//{newName: "我怎么这么好看！"}
//按照表达式从左向右直行的原则，其相当于在场景1的基础上执行了node = {newName:"我怎么这么好看！"};
//执行如下图场景2所示
//注意：注意：注意：这样理解虽然能说明问题，然而理解的并不完整，存在错误！！！
```

![image-20200525165147140](https://mashaojie.club/download/source/typora-user-images/image-20200525165147140.png)

```js
//3、场景3
//上面说场景2的解释理解并不完整，直接上代码先抛出问题
let node = {name:"美男子"};
let rootNode = node;

for(let i=0; i<3; i++){
  node.next = node = {newName:"我怎么这么好看！"};
  node.key = "msj" + (i + 1);
}

console.log(node);
//{newName: "我怎么这么好看！", key: "msj3"}
console.log(rootNode);
//结果如下图所示
//{name: "美男子", next: {newName: "我怎么这么好看！", key: "msj1", next: {newName: "我怎么这么好看！", key: "msj2", next: {newName: "我怎么这么好看！", key: "msj3"}}}}

//分析：此时你会发现，场景2中得出的结论并不能解释现在rootNode的结果。一直以来我们都只是在数据存储方面来理解，而并没有从代码运行解析层面去考虑，所以得到的思维模型并不完整。
//结论：完整的理解这一运行结果需要三个知识点：
//a、对象的堆存储
//b、表达式的从左向右执行
//c、执行赋值语句前，先要对=前后表达式做处理

//因此`node.next = node = {newName:"我怎么这么好看！"}`这句的执行细节：
//{newName:"我怎么这么好看！"}创建堆内存变量->node.next赋值指向新创建的堆内存变量->node赋值指向新创建的堆内存变量
```

![image-20200525153655471](https://mashaojie.club/download/source/typora-user-images/image-20200525153655471.png)

![image-20200525164748387](https://mashaojie.club/download/source/typora-user-images/image-20200525164748387.png)