---
layout: post
title: js数组Array深挖学习笔记
date: 2019-8-21
tags: JavaScript   
---

### 1、concat 【数组合并】--纯函数

#### 使用方式：

```jsx
let  myArray = array.concat(args...);
```

+ args：参数，可以是数组，也可以是字符串，参数个数不限

#### 使用范例：

*concat调用不改变原数组*

```jsx
var arr = new Array(3)
arr[0] = "George"
arr[1] = "John"
arr[2] = "Thomas"

var arr2 = new Array(3)
arr2[0] = "James"
arr2[1] = "Adrew"
arr2[2] = "Martin"

var arr3 = new Array(2)
arr3[0] = "William"
arr3[1] = "Franklin"

let myArray = arr.concat(arr2,arr3);
```

```jsx
var a = [1,2,3];
let myArray = a.concat(4,5);
```

### 2、constructor 【获取数据类型】

*这个属性函数存在于任意数据类型的属性中：*

#### 使用方式：

```jsx
let ret = myArray.constructor；
```

#### 使用范例：

*此处注意constructor与instanceof的区别，instanceof会顺着原型链的constructor属性进行对比校验，而construct比较的只是当前属性*

```jsx
[].constructor === Array; //true
[].constructor === Object //false
"".constructor === String //true
```

### 3、copyWitnin 【复制数组目标元素到指定的位置上】

#### 使用方式：

```jsx
let myArray = array.copyWithin(target, start, end)；
```

#### 使用范例：

```jsx
[1,2,3,4,5].copyWithin(3, 1, 3)
> [1, 2, 3, 2, 3]
[1,2,3,4,5].copyWithin(3, 1, 4)  //由此可见此方法不会改变数组的大小
> [1, 2, 3, 2, 3]
```

### 4、entries 【获取数组的遍历器对象】

#### 使用方式：

```jsx
let iterator = ["Banana", "Orange", "Apple", "Mango"].entries();
iterator.next();
```

### 使用范例：

```jsx
let iterator = ["Banana", "Orange", "Apple", "Mango"].entries();
console.log(iterator.next().value); //[0, "Banana"]
console.log(iterator.next().value); //[1, "Orange"]
console.log(iterator.next().value); //[2, "Apple"]
console.log(iterator.next().value); //[3, "Mango"]

//或者使用下面for循环--注意：IE浏览器不支持for...of，需要通过babel做转换
for(let item of iterator){
  console.log("item", item);
}
//[0, "Banana"]
//[1, "Orange"]
//[2, "Apple"]
//[3, "Mango"]
```

### 5、every 【检测数组的每一个元素是否都符合条件】

#### 使用方法：

```jsx
let retBoolean = array.every(function(currentValue,index,arr), thisValue);
```

#### 使用范例：

```jsx
[32, 33, 16, 40].every(e => e > 18);  //false
[32, 33, 16, 40].every(e => e > 15);  //true
```

### 6、fill 【将某一元素替换到数组的指定位置】

#### 使用方法：

```jsx
let myArray = array.fill(value, start, end);		
```

#### 使用范例：

*会改变原数组*

```jsx
let myArr = [1,2,3,4,5];
myArr.fill(9, 1, 3) //[1, 9, 9, 4, 5]
[1,2,3,4,5].fill(9, 1, 6) //[1, 9, 9, 9, 9] //由此可见此方法不会改变数组的大小
```

### 7、filter 【数组过滤器】--纯函数

#### 使用方法：

```jsx
let myArray = array.filter(function(currentValue,index,arr), thisValue);
```

#### 使用范例：

```jsx
//使用过滤器元素去重
let orgArr = [1,3,2,5,2,9,11,8,15,6,3,5];
let myArray = orgArr.filter((e, index, arr) => {
   return arr.indexOf(e) == index;
});
//orgArr > [1, 3, 2, 5, 2, 9, 11, 8, 15, 6, 3, 5]
//myArray > [1, 3, 2, 5, 9, 11, 8, 15, 6]
```

### 8、find 【查找符合条件的第一个数组元素】

#### 使用方法：

```jsx
let retItem = array.find(function(currentValue,index,arr), thisValue);	
```

#### 使用范例：

```jsx
let ret = [3, 10, 18, 20].find(e => e > 16); //18
```

### 9、findIndex 【查找符合条件的第一个数组元素的索引】

#### 使用方法：

```jsx
let retNumber = array.findIndex(function(currentValue,index,arr), thisValue);
```

#### 使用范例：

```jsx
let retNumber = [3, 10, 18, 20].findIndex(e => e > 16); //2
```

### 10、flat 【多维数组降维---想到了刘慈欣大师的“降维打击”战术策略】

#### 使用方法：

```jsx
let  myArray = array.flat(level);
```

+ level：对多维数组的降维层级

#### 使用范例：

```jsx
[1, [2, [3, [4, 5]]], 6].flat();  //[1, 2, Array(2), 6]
[1, [2, [3, [4, 5]]], 6].flat(2); //[1, 2, 3, Array(2), 6]
[1, [2, [3, [4, 5]]], 6].flat(3); //[1, 2, 3, 4, 5, 6]
```

### 11、flatMap 【映射每个元素，然后将结果压缩成一个新数组】

***此方法默认自带元素操作降维功能，最低反馈一维数组***

#### 使用方法：

```jsx
let myArray = array.flatMap(function(currentValue,index,arr), thisValue);			
```

#### 使用范例：

```jsx
[1, 2, 3, 4].flatMap(x => x * 2);  //[2, 4, 6, 8]
[1, 2, 3, 4].flatMap(x => [x * 2]); // [2, 4, 6, 8]只会将 flatMap 中的函数返回的数组 “压平” 一层

[1, 2, 3, 4].map(x => [x * 2]);     // [[2], [4], [6], [8]]
[1, 2, 3, 4].flatMap(x => [[x * 2]]); // [[2], [4], [6], [8]]

//虽然上面的代码使用 map 和 flatMap 好像都可以，但这只能展示如何使用 flatMap。
//所以，为了更好的展示 flatMap 的作用，下面我们将包含几句话的数组拆分成单个汉字组成的新数组。
let arr = ["今天天气不错", "", "早上好"]
arr.map(s => s.split("")) // [["今", "天", "天", "气", "不", "错"],[],["早", "上", "好"]]
arr.flatMap(s => s.split(""));// ["今", "天", "天", "气", "不", "错", "早", "上", "好"]
```

### 12、flatten 【数组降维--目前感觉使用和flat一致，未发现差异】

#### 使用方法：

​		略

#### 使用范例：

```jsx
//略
```

### 13、forEach 【轮询数组元素】

#### 使用方法：

```jsx
array.forEach(function(currentValue, index, arr), thisValue)
```

#### 使用范例：

```jsx
//改变原来数据的值
let myArray = [1,2,3,4,5];
//数组值求和范例
let sum = 0;
myArray.forEach((value, index, arr) => {
  sum += value;
});
console.log(sum);//15

//轮询改变数组范例
//注意forEach和map的区别，forEach没有返回值；
let myArray = [1,2,3,4,5];
myArray.forEach((value, index, arr) =>  value * 2);

console.log(myArray);//[1, 2, 3, 4, 5]

myArray.forEach((value, index, arr) => {
  arr[index] = value * 2;
});

console.log(myArray);//[2, 4, 6, 8, 10]
```

### 14、from【数组转换】

#### 使用方法：

```jsx
let myArray = Array.from(object, mapFunction, thisValue)
```

#### 使用范例：

```jsx
let myArray = Array.from("123123"); 					//["1", "2", "3", "1", "2", "3"]
let myArray = Array.from(123123);							//[]
let myArray = Array.from(["1", "2", "3"]);		//["1", "2", "3"]
let myArray = Array.from("123123", e => e*2);	//[2, 4, 6, 2, 4, 6]
```

### 15、includes 【判断数组是否包含某个值】

#### 使用方法：

```jsx
let boolean = array.includes(searchElement, fromIndex)		
```

* searchElement：需要检索的元素

* fromIndex：起始位置

#### 使用范例：

```jsx
[1, 2, 3].includes(2);     // true
[1, 2, 3].includes(4);     // false
[1, 2, 3].includes(3, 3);  // false
[1, 2, 3].includes(3, -1); // true
[1, 2, NaN].includes(NaN); // true
```

### 16、indexOf 【检索数组元素，获取下标】

#### 使用方法：

```jsx
let ret = array.indexOf(searchElement, fromIndex);			
```

* searchElement：需要检索的元素

* fromIndex：起始位置

#### 使用范例：

```jsx
[1,2,3,3,4,4,5].indexOf(3); //2
[1,2,3,3,4,4,5].indexOf(6); //-1
[1,2,3,3,4,4,5].indexOf(3, 1); //2
[1,2,3,3,4,4,5].indexOf(3, 3); //3
[1,2,3,3,4,4,5].indexOf(3, 4); //-1
```

### 17、isArray【判断数据是否为数组类型】

#### 使用方法：

```jsx
let retBoolean = Array.isArray(obj)
```

#### 使用范例：

```jsx
let retBoolean = Array.isArray([1,2,3]);	//true
let retBoolean = Array.isArray([]);				//true
let retBoolean = Array.isArray({});				//false
let retBoolean = Array.isArray(1);				//false
let retBoolean = Array.isArray("123");		//false
```

### 18、join 【数组转字符串拼装】

#### 使用方法：

```jsx
let string = array.join(separator);
```

+ separator：分隔符，默认取值为","

#### 使用范例：

```jsx
let retString;
retString = [1,2,3,4,5].join();     //"1,2,3,4,5"
retString = [1,2,3,4,5].join("|");  //"1|2|3|4|5"
retString = [1,2,3,4,5].join("");   //"12345"
retString = [1,2,3,4,5].join("\\");	//"1\2\3\4\5"		//符号"\"需要做转译，因此输入为"\\"
retString = [1,2,3,4,5].join("/");	//"1/2/3/4/5"
```

### 19、keys 【返回数组的索引遍历器，感觉貌似没啥用...】

#### 使用方法：

```jsx
let iterator = array.keys();
```

#### 使用范例：

```jsx
//待考究
let iterator = [{"Banana":"123"}, "Orange", "Apple", "Mango"].keys();
for(let item of iterator){
  console.log("item", item);
}
//item 0
//item 1
//item 2
//item 3
```

### 20、lastIndexOf 【逆向检索数组元素，获取下标】

#### 使用方法：

```jsx
let retNumber = array.lastIndexOf(searchElement, endIndex);
```

* searchElement：需要检索的元素

* endIndex：结束位置

#### 使用范例：

```jsx
//这个的理解比较绕，建议理解起来分两步
//1、按照结束位置先截取数组【包含结束角标的截取】
//2、在截取的数组中找最后一个元素的下标
[1,2,3,3,4,4,5].lastIndexOf(3); //3
[1,2,3,3,4,4,5].lastIndexOf(6); //-1
[1,2,3,3,4,4,5].lastIndexOf(3, 1); //-1
[1,2,3,3,4,4,5].lastIndexOf(3, 2); //2
[1,2,3,3,4,4,5].lastIndexOf(3, 3); //3
```

### 21、map 【对数组中每个元素做处理，然后生成新数组】

#### 使用方法：

```jsx
let myArray = array.map((value, index, arr) => {});
```

#### 使用范例：

```jsx
let myArray = [1,2,3,4,5].map((value, index, arr) => value * 2); //[2, 4, 6, 8, 10]
```

### 22、pop 【删除数组最后一个元素，并返回】

#### 使用方法：

```jsx
let item = array.pop(); //此方法会改变原来的数组
```

#### 使用范例：

```jsx
let array = [1,2,3,4,5];
let item = array.pop();

console.log("item", item);		//item 5
console.log("array", array);  //array [1, 2, 3, 4]
```

### 23、push 【给数组末尾添加新元素，并返回新数组长度】

#### 使用方法：

```jsx
let	arrayLength = array.push(agrs...);  //返回数组长度
```

#### 使用范例：

```jsx
let array = [1,2,3,4,5];
let arrayLength = array.pop("9", 12, 16);

console.log("arrayLength", arrayLength);		//arrayLength 8
console.log("array", array);  					//array [1, 2, 3, 4, 5, "9", 12, 16]
```

### 24、reduce 【单元素操作求和处理】

#### 使用方法：

```jsx
let retNumber = array.reduce(function(sum, value, index, arr), initialValue);
```

#### 使用范例：

```jsx
//数组指定算法求和
[1,2,3,4,5,6].reduce((a,b) => a+b);						//21
[1,2,3,4,5,6].reduce((a,b) => a + b*2);				//41 -- a第一次相加表示数组第一个元素
[1,2,3,4,5,6].reduce((a,b) => a + b*2, 0);		//42 -- a第一次相加时候为0

[1,2,3,,4,5,6].reduce((a,b) => a + b*2, 0);			//42 -- a第一次相加时候为0,空值当作0处理
[1,2,3,null,4,5,6].reduce((a,b) => a + b*2, 0); //42
[1,2,3,"",4,5,6].reduce((a,b) => a + b*2, 0);		//42 -- 字符串做强转，空转为0

[1,2,3,"5",4,5,6].reduce((a,b) => a + b*2, 0);	//52 -- 字符串做强转，空转为0

[1,2,3,undefined,4,5,6].reduce((a,b) => a + b*2, 0); //NaN
[1,2,3,NaN,4,5,6].reduce((a,b) => a + b*2, 0);				//NaN
```

### 25、reduceRight 【单元素操作求和处理-从右往左处理】

#### 使用方法：

```jsx
let retNumber = array.reduceRight(function(sum, value, index, arr), initialValue);
```

#### 使用范例：

```jsx
//reduce和reduceRight使用对比
[1,2,3,4,5,6].reduce((a,b) => a*10+b);						//123456
[1,2,3,4,5,6].reduceRight((a,b) => a*10+b);				//654321
```

### 26、reverse 【数组逆序，并返回新数组】

#### 使用方法：

```jsx
let myArray = array.reverse();
```

#### 使用范例：

```jsx
let myArray = [1,2,3,4,5,9].reverse();  //[9, 5, 4, 3, 2, 1]
```

### 27、shift 【删除数组第一个元素生成新数组，并返回元素值】

#### 使用方法：

```jsx
let retItem = array.shift();	//此方法会改变原数组
```

#### 使用范例：

```jsx
let array = [2,3,4,5,6];
let retItem = array.shift();

console.log("array", array);			//array [3, 4, 5, 6]
console.log("retItem", retItem);	//retItem 2
```

### 28、slice 【数组截取，并返回新数组】

#### 使用方法：

```jsx
let myArray = array.slice(start, end);	//索引参数包前不包后
```

#### 使用范例：

```jsx
let array = [1,2,3,4,5];
let myArray = array.slice(1, 3);	//slice不改变原数组，返回新数组
let myArray2 = array.slice();			//slice不改变原数组，返回新数组

console.log("array", array);			//array [1, 2, 3, 4, 5]
console.log("myArray", myArray);	//myArray [2, 3]
console.log("myArray2", myArray2);//myArray2 [1, 2, 3, 4, 5]
```

### 29、some 【判断时候有元素符合条件】

#### 使用方法:

```jsx
let retBoolean = array.some(function(currentValue,index,arr),thisValue);
```

#### 使用范例：

```jsx
[10,11,12,13,14].some((value) => value > 9);	//true
[10,11,12,13,14].some((value) => value > 13);	//true
[10,11,12,13,14].some((value) => value > 14);	//false
```

### 30、sort 【数组按指定方法排序】

#### 使用方法：

```jsx
array.sort(sortfunction) //默认升序处理
```

#### 使用范例：

```jsx
//注意：字母操作只能升序，如需要降序，则先升序，再调用reverse()逆序处理；
["b","r","c","b","a","s"].sort();					//["a", "b", "b", "c", "r", "s"] --默认字母升序
[9,5,8,3,7,4,6,3,5].sort();								//[3, 3, 4, 5, 5, 6, 7, 8, 9]
[9,5,8,3,7,4,6,3,5].sort((a,b) => a-b);		//[3, 3, 4, 5, 5, 6, 7, 8, 9]
[9,5,8,3,7,4,6,3,5].sort((a,b) => b-a);		//[9, 8, 7, 6, 5, 5, 4, 3, 3]

//注意：排序功能会改变原数组,且不会生成新的数组对象，如下所示：
let array = [9,5,8,3,7,4,6,3,5];
let myArray = array.sort();

console.log("array", array);			//array (9) [3, 3, 4, 5, 5, 6, 7, 8, 9]
console.log("myArray", myArray);	//myArray (9) [3, 3, 4, 5, 5, 6, 7, 8, 9]

myArray.push(10);
console.log("array", array);			//array (9) [3, 3, 4, 5, 5, 6, 7, 8, 9, 10]
console.log("myArray", myArray);	//myArray (10) [3, 3, 4, 5, 5, 6, 7, 8, 9, 10]
```

### 31、splice 【数组元素修改，返回被删除的元素数组，改变原有数组】

#### 使用方法：

```jsx
let myArray = array.splice(index, howmany, item1,.....,itemX); 
//从数组的index位置开始，删除howmany个元素，然后将后续的字元素添加到index位置
```

#### 使用范例：

```jsx
//此处要注意一个细节array.splice()和array.splice(0)
let array = [1,2,3,4,5,6];
let myArray = array.splice();  //从第0位开始，删除0个元素
console.log("array", array);			//array (6) [1, 2, 3, 4, 5, 6]  --原数组不变
console.log("myArray", myArray);	//myArray []

let array = [1,2,3,4,5,6];
let myArray = array.splice(0);  //从第0位开始，删除所有元素
console.log("array", array);			//array []  --清空原数组
console.log("myArray", myArray);	//myArray [1, 2, 3, 4, 5, 6]

//元素删除范例
let array = [1,2,3,4,5,6];
let myArray = array.splice(2, 2); 
console.log("array", array);			//array [1, 2, 5, 6]
console.log("myArray", myArray);	//myArray [3, 4]

//元素添加范例
let array = [1,2,3,4,5,6];
let myArray = array.splice(2, 0, 9, 9); 
console.log("array", array);			//array [1, 2, 9, 9, 3, 4, 5, 6]
console.log("myArray", myArray);	//myArray []

//元素删除与添加范例
let array = [1,2,3,4,5,6];
let myArray = array.splice(2, 3, 9, 9, 9); 
console.log("array", array);			//array [1, 2, 9, 9, 9, 6]
console.log("myArray", myArray);	//myArray [3, 4, 5]
```

### 32、toLocaleString 【数组转字符串】

#### 使用方法：某些情况下等同于toString方法

```jsx
let retString = array.toLocaleString();
```

#### 使用范例：

```jsx
[1,2,3,4,5,6].toLocaleString();			//"1,2,3,4,5,6"

//看看toLocaleString和toString的差别
let array = [10000, 20000, new Date()];

console.log(array.toString());
//10000,20000,10000,20000,Fri May 08 2020 15:41:37 GMT+0800 (中国标准时间)
console.log(array.toLocaleString());//10,000,20,000,2020/5/8 下午3:41:37
```

### 33、toString 【数组元素拼装输出符串，不改变原数组】

#### 使用方法：

```jsx
let retString = array.toString();
```

#### 使用范例：

```jsx
//元素删除与添加范例
let array = [1,2,3,4,5,6];
let retString = array.toString(); 
console.log("array", array);					//array [1, 2, 3, 4, 5, 6]
console.log("retString", retString);	//retString 1,2,3,4,5,6
```

### 34、unshift 【在数组的开头添加一个或更多元素，会改变原数组】

#### 使用方法：

```jsx
let arrayLength = array.unshift(agrs...); //原数组发生改变，返回值为数组长度
```

#### 使用范例：

```jsx
let array = [1,2,3,4,5];
let arrayLength = array.unshift(9,9,9,[3,4]);

console.log("array", array); 			//array [9, 9, 9, [3,4], 1, 2, 3, 4, 5]
console.log("arrayLength", arrayLength);	//arrayLength 9
```

### 35、valueOf【】

#### 使用方法：

```jsx
let ret = array.valueOf();
```

#### 使用范例：

```jsx
let array = [1,2,3,4,5];
let ret = array.valueOf();

console.log("array", array);
console.log("ret", ret);
```

### 36、values 【返回数组元素的遍历器】

#### 使用方法：

```jsx
//一般可以直接对数组用for...of...方法进行遍历，所以此方法基本很少用到。
let iterator = array.values();
```

#### 使用范例：

```jsx
let array = ["msj", "mnz", "yx"];
let iterator = array.values();

for(let item of iterator){
  console.log("item", item);
}
//item msj
//item mnz
//item yx

//直接遍历数组，执行无差异
for(let item of array){
  console.log("item", item);
}
//item msj
//item mnz
//item yx
```