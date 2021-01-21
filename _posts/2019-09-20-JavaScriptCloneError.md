---
layout: post
title: Js浅拷贝理解误区纠正和深拷贝最佳方案
date: 2019-9-20
tags: JavaScript   
---

### 如果条件允许，建议直接使用lodash库

>  浅拷贝和深拷贝只是针对对象引用类型而言的。因为有C语言和java语言的背景，所以对于对象的指针存储的理解并不难，也知道js现在越来越像java类语言，只是没有想到竟然会如此的像。

我一直以来对浅拷贝的认知误区是：我以为=赋值就是浅拷贝，而我这种认知源于之前看过的一篇误人子弟的博客，也怪了没有去深究，因为出于项目研发解决问题的目的，当时我想重点学习的深拷贝方法已经学到了，也只是附带看了一下浅拷贝的内容，最后得出来一个错误的结论：js某些点还是不如java全面。



***~~反面教材案例【有8W+访问量，不能说一无是处，只能说不够严谨】：~~***

https://blog.csdn.net/baidu_36065997/article/details/80309991

![image-20200507130924134](https://mashaojie.club/download/source/typora-user-images/image-20200507130924134.png)



**我看到的比较好的教材案例【相当深刻，便于理解，却只有1500+的访问量】：**

https://www.jianshu.com/p/a8dc699e9534

![image-20200507131205730](https://mashaojie.club/download/source/typora-user-images/image-20200507131205730.png)

> 为了做好记录，便于他人学习，我发挥了我不摇碧莲的身手拿来主义【申明一下：我一向尊重原创】



## 开始学习了

学习之前，先了解下**堆**和**栈**知识，js 的**数据类型**

### 堆和栈的区别

其实深拷贝和浅拷贝的主要区别就是其在内存中的存储类型不同。
堆和栈都是内存中划分出来用来存储的区域。

> 栈（stack）为自动分配的内存空间，它由系统自动释放;
>  而堆（heap）则是动态分配的内存，大小不定也不会自动释放。

### 数据类型

> - 基本数据类型：String、Number、Boolean、Symbol、Undefined、Null
> - 引用数据类型：Object

- **基本数据类型存放在栈中**：存放在栈内存中的简单数据段，数据大小确定，内存空间大小可以分配，是直接按值存放的，所以可以直接访问。
   *基本数据类型值不可变：*基本数据类型的值是不可变的，动态修改了基本数据类型的值，它的原始值也是不会改变的
   例如：

```jsx
    var str = "abc";
    console.log(str[1]="f");    // f
    console.log(str);           // abc
```

- 引用类型存放在堆中

  引用类型（object）是存放在堆内存中的，变量实际上是一个存放在栈内存的指针，这个指针指向堆内存中的地址。每个空间大小不一样，要根据情况开进行特定的分配

  ![image-20200507131830382](https://mashaojie.club/download/source/typora-user-images/image-20200507131830382.png)



*引用数据类型值可变*：

```jsx
    var a = [1,2,3];
    a[1] = 5;
    console.log(a[1]); // 5
```

### 浅拷贝 VS深拷贝

- **传值和传址**（通过基本数据和引用数据类型的却别之后，我们就应该能明白传值与传址的区别了）。
   在我们进行赋值操作的时候，基本数据类型的赋值（=）是在内存中新开辟一段栈内存，然后再把再将值赋值到新的栈中

```jsx
var a = 10;
var b = a;
a ++ ;
console.log(a); // 11
console.log(b); // 10
```

![image-20200507132343411](https://mashaojie.club/download/source/typora-user-images/image-20200507132343411.png)



所以说，基本类型的赋值的两个变量是两个独立相互不影响的变量。

但是引用类型的赋值是传址。只是改变指针的指向，例如，也就是说引用类型的赋值是对象保存在栈中的地址的赋值，这样的话两个变量就指向同一个对象，因此两者之间操作互相有影响



```jsx
var a = {}; // a保存了一个空对象的实例
var b = a;  // a和b都指向了这个空对象

a.name = 'jozo';
console.log(a.name); // 'jozo'
console.log(b.name); // 'jozo'

b.age = 22;
console.log(b.age);// 22
console.log(a.age);// 22

console.log(a == b);// true
```

![image-20200507132506853](https://mashaojie.club/download/source/typora-user-images/image-20200507132506853.png)



### 浅拷贝

***【这一句是重点】看完上面的文章，其实上面的代码是我们常用的赋值引用，还不能算浅拷贝***

那么

- 赋值和浅拷贝有什么区别？

```ruby
    var obj1 = {
        'name' : 'zhangsan',
        'age' :  '18',
        'language' : [1,[2,3],[4,5]],
    };

    var obj2 = obj1;


    var obj3 = shallowCopy(obj1);
    function shallowCopy(src) {
        var dst = {};
        for (var prop in src) {
            if (src.hasOwnProperty(prop)) {
                dst[prop] = src[prop];
            }
        }
        return dst;
    }

    obj2.name = "lisi";
    obj3.age = "20";

    obj2.language[1] = ["二","三"];
    obj3.language[2] = ["四","五"];

    console.log(obj1);  
    //obj1 = {
    //    'name' : 'lisi',
    //    'age' :  '18',
    //    'language' : [1,["二","三"],["四","五"]],
    //};

    console.log(obj2);
    //obj2 = {
    //    'name' : 'lisi',
    //    'age' :  '18',
    //    'language' : [1,["二","三"],["四","五"]],
    //};

    console.log(obj3);
    //obj3 = {
    //    'name' : 'zhangsan',
    //    'age' :  '20',
    //    'language' : [1,["二","三"],["四","五"]],
    //};
```

- obj1 原始数据
- obj2 赋值数据
- obj3 浅拷贝数据
   **接下来说下浅拷贝。**
   为什么改变了赋值得到的对象 obj2 和浅拷贝得到的 obj3 中的 *language*） 属性的第二个值和第三个值（*language* 是一个数组，也就是**引用类型**）。结果见输出，可以看出来，无论是修改赋值得到的对象 obj2 和浅拷贝得到的 obj3 都会改变原始数据。

**【因为浅拷贝只复制一层对象的属性，并不包括对象里面的为引用类型的数据。所以就会出现改变浅拷贝得到的 obj3 中的引用类型时，会使原始数据得到改变。】**
 **用一句话简单理解 ：**

> 浅拷贝就是拷贝了一层，除了对象是拷贝的引用类型，其他（一开始讲到的**基本数据类型**）都是直接将值传递，有自己的内存空间的

深拷贝：将 B 对象拷贝到 A 对象中，包括 B 里面的子对象，
 浅拷贝：将 B 对象拷贝到 A 对象中，但不包括 B 里面的子对象

### 深拷贝

看完对浅拷贝的理解，可以知道：深拷贝就是对对象以及对象的所有子对象进行拷贝。
 接下来改探讨的就是如何对对象进行深拷贝？

- 1、用 JSON.stringify 把对象转成字符串，再用 JSON.parse 把字符串转成新的对象（使用JSON）。

```jsx
var obj1 = { body: { a: 10 } };
var obj2 = JSON.parse(JSON.stringify(obj1));
obj2.body.a = 20;
console.log(obj1);
// { body: { a: 10 } } <-- 沒被改到
console.log(obj2);
// { body: { a: 20 } }
console.log(obj1 === obj2);
// false
console.log(obj1.body === obj2.body);
// false
```

坏处：它会抛弃对象的constructor。也就是深拷贝之后，不管这个对象原来的构造函数是什么，在深拷贝之后都会变成Object。所以只适合 Number, String, Boolean, Array 的扁平对象

- **2、递归拷贝**

```jsx
function deepClone(obj) {
  let objClone = Array.isArray(obj) ? [] : {};
  if (obj && typeof obj === "object") {
    for (key in obj) {
      if (obj.hasOwnProperty(key)) {
        //判断ojb子元素是否为对象，如果是，递归复制
        if (obj[key] && typeof obj[key] === "object") {
          objClone[key] = deepClone(obj[key]);
        } else {
          //如果不是，简单复制
          objClone[key] = obj[key];
        }
      }
    }
  }
  return objClone;
} 

var obj1 = {
   a: 1,
   b: 2,
   c: {
     d: 3
   }
}
var obj2 = deepClone(obj1);
obj2.a = 3;
obj2.c.d = 4;
alert(obj1.a); // 1
alert(obj2.a); // 3
alert(obj1.c.d); // 3
alert(obj2.c.d); // 4
```

或者使用es6中的 Object.create()方法

```jsx
function deepClone(initalObj, finalObj) {    
  var obj = finalObj || {};    
  for (var i in initalObj) {        
    var prop = initalObj[i];        // 避免相互引用对象导致死循环，如initalObj.a = initalObj的情况
    if(prop === obj) {            
      continue;
    }        
    if (typeof prop === 'object') {
      //马少杰---20190507---此处修复了源代码的一处错误
      obj[i] = (prop.constructor === Array) ? deepClone(prop, []): Object.create(prop);
    } else {
      obj[i] = prop;
    }
  }    
  return obj;
}
```



## 最后总结

### 经验总结：学习要严谨

### 技术总结：

​		这块有一个概念，js对象里面包个对象，这个现象在js里面的官话叫js原型链的继承，跟java当中的子类继承父类思想是一样的。一直以来我以为JSON.parse(JOSN.stringify(Object));是最牛逼通用的对象深拷贝方法，今天才知道不是，因为这样做会丢失对象的构造函数。

> 修补了原博主“使用es6中的 Object.create()方法”深拷贝的一处代码错误

希望各位学习指正，如有疏漏请与我联系：Wechat/QQ：1215458034