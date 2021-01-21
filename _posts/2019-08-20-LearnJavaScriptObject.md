---
layout: post
title: js对象Object深挖学习笔记
date: 2019-8-20
tags: JavaScript   
---

### 1、constructor 【构造函数】

#### 使用方式：

```jsx
let retObj = object.constructor；
```

#### 使用范例：

```jsx
let object = new Object();
console.log(object.constructor);  //ƒ Object() { [native code] }
```

### 2、hasOwnProperty 【判断是否包含某个直属属性】

#### 使用方式：

```jsx
let retBoolean = object.hasOwnProperty(key)；
```

#### 使用范例：

```jsx
let object = {
  name: "mashaojie",
  age: 19,
  sex: "公"
}

console.log(object.hasOwnProperty("name1"));	//false
console.log(object.hasOwnProperty("name"));		//true		---hasOwnProperty只检索直属属性
console.log("name" in object);								//true		---in会检索继承链属性
```

### 3、isPrototypeOf 【判断一个对象是否是另一个对象的原型父对象】

#### 使用方式：

```jsx
let retBoolean = object.isPrototypeOf(subObj);
```

#### 使用范例：

```jsx
let Person = function(name){
  this.name = name;
  this.sayHey = function(){
    console.log("hey "+ name);
  }
}
let Student = function(name){
  Person.call(this, name);
 	this.getName = function(){
    console.log(name);
  }
}

Student.prototype = new Person();
Student.prototype.constructor = Student;

let student = new Student("msj");
let person = new Person("mnz");

student.sayHey();   //hey msj
student.getName();	//msj

person.sayHey();		//hey mnz

console.log(Person.prototype.isPrototypeOf(student)); //true
console.log(person.isPrototypeOf(student));	//false
console.log(student.isPrototypeOf(person));	//false
```

### 4、propertyIsEnumerable 【判断某一个属性的值是否可枚举】

#### 使用方式：

```jsx
let retBoolean = object.propertyIsEnumerable(name);
```

#### 使用范例：

```jsx
let array = ["Banana", "Orange", "Apple", "Mango"];
let object = {
  name: "msj",
  age: 20,
  num: 2,
  array: array
};

for(let item in array){
  console.log(item);
}
//0
//1
//2
//3

for(let item in object){
  console.log(item);
}
//name
//age
//num
//array

console.log(array.propertyIsEnumerable(0)); 				//true
console.log(array.propertyIsEnumerable("length")); 	//false --length这个属性不太一般啊！
console.log(object.propertyIsEnumerable("name"));		//true
console.log(object.propertyIsEnumerable("age"));		//true
console.log(object.propertyIsEnumerable("num"));		//true
console.log(object.propertyIsEnumerable("array"));	//true
```

### 5、toLocaleString 【对象转字符串-目前没发现啥用途】

#### 使用方法：

```jsx
let retString = object.toLocaleString();
```

#### 使用范例：

```jsx
let obj = {name: "msj"};
obj.toLocaleString();			//"[object Object]"
```

### 6、toString  【对象转字符串-目前没发现啥用途】

#### 使用方法：

```jsx
let retString = object.toString();
```

#### 使用范例：

```jsx
let obj = {name: "msj"};
obj.toString();			//"[object Object]"
```

### 7、valueOf 【返回对象本身】

#### 使用方法：

```jsx
let myObject = object.valueOf();
```

#### 使用范例：

```jsx
let obj = {};
obj.valueOf();		//{}
```

### 8、preventExtensions(), seal(), freeze()的区别

| 方法                 | 修改属性描述 | 修改属性值 |  添加属性  |
| ------------------- | :------: | :--------: | :----------: |
| 其他对象             |    ✔️     |     ✔️      |      ✔️       |
| preventExtensions() |   ✔️        |    ✔️       | ❌ |
| seal()           |     ✔️     | ❌ | ❌ |
| freeze()            | ❌ | ❌ | ❌ |



