---
layout: post
title: Object.assgin特性记录
date: 2020-01-20 
tags: JavaScript    
---


> 参照原著作者：帅气滴糟老头
> 链接：https://www.jianshu.com/p/d5f572dd3776

## 使用规则

```jsx
Object.assign(target, source...);
```

## 使用范例

```jsx
let object1 = {
  a:1,
  b:2
};
let object2 = {
  c:3
};

let object = Object.assign({}, object1, object2);
console.log(object1);	//{a: 1, b: 2}
console.log(object2);	//{c: 3}
console.log(object);  //{a: 1, b: 2, c: 3}
```

## 特性

### 1、元素覆盖特性

说明：如果目标对象与源对象有同名属性，或多个源对象有同名属性，则后面的属性会覆盖前面的属性，且这种覆盖发生在直属属性层。

```jsx
//范例1
let target = { a: 1, b: 1 };
let source1 = { b: 2, c: 2 };
let source2 = { c: 3 };

let object = Object.assign(target, source1, source2);
console.log(object);  // {a:1, b:2, c:3}

//范例2
let source = { a: { b: 1, c: 1 }, d: 1 };
let source1 = { a: { b: 2 } };

let object = Object.assign(source, source1);
console.log(object);  // { a: { b: 2 }, d: 1 }
```

### 2、类型转换合并

+ 忽略数值和布尔值
+ 字符串转成字符串数组再转对象合并
+ undefined和null在target位置会报错，在source位置会被忽略
+ 数组当成对象处理，返回数组

```jsx
Object.assign({a : 1, b : 2}, {c : 3}, 9, true, {d : 4}); //{a: 1, b: 2, c: 3, d: 4}

Object.assign({a : 1, b : 2},"12ab");//{0: "1", 1: "2", 2: "a", 3: "b", a: 1, b: 2}

Object.assign(undefined); //Uncaught TypeError: Cannot convert undefined or null to object
Object.assign(null);			//Uncaught TypeError: Cannot convert undefined or null to object
Object.assign({a : 1, b : 2}, {c : 3}, undefined, null); //{a : 1, b : 2, c : 3}

Object.assign([1, 2, 3], [4, 5]);	//[4 , 5, 3]
```

### 3、浅拷贝

```jsx
const obj1 = {a: {b: 1}, c: 1};
let obj = Object.assign({}, obj1);

obj1.a.b = 2;
obj1.c = 2;

console.log(obj1);  //{a: {b: 2}, c: 2}
console.log(obj);		//{a: {b: 2}, c: 1}
```

### 4、对象数组字符串混合合并

```jsx
//source位置上的数据类型决定合并后的最终数据类型【数组or对象】
Object.assign([1, 2, 3], [4, 5], "123");  			//["1", "2", "3"]
Object.assign({}, [1, 2, 3], [4, 5], "123"); 		//{0: "1", 1: "2", 2: "3"}
Object.assign({a: 1, b: 2}, [1, 2, 3], [4, 5]);	//{0: 4, 1: 5, 2: 3, a: 1, b: 2}
```

### 5、取值函数的处理

```jsx
//如果要复制的值是一个取值函数，那么将求值后再复制。
let source = {
  get foo() { return 1 }
};

Object.assign({}, source);	// { foo: 1 }
```

## 典型使用场景

### 1、为对象添加属性

```jsx
class Point {
  constructor(x, y) {
    Object.assign(this, {x, y});
  }
}
```

上面方法通过`Object.assign`方法，将`x`属性和`y`属性添加到`Point`类的对象实例。

### 2、为对象添加方法

```jsx
Object.assign(SomeClass.prototype, {
  someMethod(arg1, arg2) {
    ···
  },
  anotherMethod() {
    ···
  }
});

// 等同于下面的写法
SomeClass.prototype.someMethod = function (arg1, arg2) {
  ···
};
SomeClass.prototype.anotherMethod = function () {
  ···
};
```

上面代码使用了对象属性的简洁表示法，直接将两个函数放在大括号中，再使用`assign`方法添加到`SomeClass.prototype`之中。

### 3、浅拷贝对象

```jsx
function clone(origin) {
  return Object.assign({}, origin);
}
```

上面代码将原始对象拷贝到一个空对象，就得到了原始对象的克隆。

不过，采用这种方法克隆，只能克隆原始对象自身的值，不能克隆它继承的值。如果想要保持继承链，可以采用下面的代码。

```jsx
function clone(origin) {
  let originProto = Object.getPrototypeOf(origin);
  return Object.assign(Object.create(originProto), origin);
}
```
```jsx
//测试案例
function Person(name){
  this.name = name;
}

function Student(name, age){
    Person.call(this, name);
    this.age = age;
}

function clone(student){
  let o = Object.getPrototypeOf(student);
  return Object.assign(Object.create(o), student);
}

let stu1 = new Student("msj", 10);
let stu2 = Object.assign({}, stu1);
let stu3 = clone(stu1);

console.log(stu1);
console.log(stu2);
console.log(stu3);
```

测试截图：

![image-20200510141504895](https://mashaojie.cn/blog/source/typora-user-images/image-20200510141504895.png)

### 4、合并多个对象

将多个对象合并到某个对象。

```jsx
const merge = (target, ...sources) => Object.assign(target, ...sources);
```

如果希望合并后返回一个新对象，可以改写上面函数，对一个空对象合并。

```jsx
const merge = (...sources) => Object.assign({}, ...sources);
```

### 5、为属性指定默认值

```jsx
const DEFAULTS = {
  logLevel: 0,
  outputFormat: 'html'
};

options = Object.assign({}, DEFAULTS, options);
```