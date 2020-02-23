## ES5语法整理

### 1.构造函数、原型链、继承

#### 1.1 构造函数

```javascript
var obj = new Object();   // 对象
var obj = {};             //字面量
function Star(name,age){		 //构造函数
    this.name = name;
    this.age = age;
}        

//把公共的方法，放在prototype原型对象上面
Star.prototype.sing = function(){
    console.log("唱歌")
}

//以对象的方式，会覆盖构造函数，所以要手动重新指向
Star.prototype = {
    constructor : Star;
    sing:function(){};
}

var ldh = new Star("刘德华",18);   //实例化对象
ldh.sing();

//注意：实例对象自带__proto__属性，指向构造函数的原型对象；
ldh.__proto__ = Star.prototype;
```

#### 1.2 原型链：一切事物皆对象；

![image-20200131155228453](C:\Users\chen\AppData\Roaming\Typora\typora-user-images\image-20200131155228453.png)

#### 1.3 this指向

1.在构造函数中，this指向的是实例对象：ldh；

2.在原型对象的函数里，this指向的是调用者：ldh；

#### 1.4 继承

```javascript
在function Son(){
    //子继承父的属性
	Father.call(this,name,age);
}
//子继承父的方法
Son.prototype = new Father();//Son的原型对象指向Father实例，会覆盖Son的构造函数；
Son.constructor = Son;
```

### 2.新增的方法

#### 2.1 数组

数组的增删改查：

| 用途               | 方法                                              | 返回值           |
| ------------------ | ------------------------------------------------- | ---------------- |
| 增加元素：放在尾部 | arr.push(1)                                       | 数组长度         |
| 增加元素：放在头部 | arr.unshift(1)                                    | 数组长度         |
| 删除尾部的元素     | arr.pop()                                         | 被删除的元素     |
| 删除头部的元素     | arr.shift()                                       | 被删除的元素     |
| 万能函数：splice   | arr.splice(第几个，删除几个，增加的元素)          | 被删除的元素     |
|                    | arr.splice(1,1)：从第一个开始，删除一个           | 被删除的元素     |
|                    | arr.splice(1,0,'11','22')：从第一个开始，添加元素 | 0                |
| 连接两个数组       | nums1.concat(nums2)                               | 新数组           |
| 数组变成字符串     | arr.join('---')                                   | 字符串           |
| 找元素对应的索引   | arr.indexOf(200)                                  | 索引值；-1       |
| 切割数组           | arr.slice(2)                                      | 返回切割后的数组 |

数组的遍历：

| 方法    | 用途                                                         | 返回值       |
| ------- | ------------------------------------------------------------ | ------------ |
| forEach | 循环遍历数组                                                 | 无返回值     |
| map     | 循环遍历数组，不改变原数组（是一种映射函数）                 | 返回新数组   |
| filter  | 筛选数组                                                     | 返回新数组   |
| some    | 查找元素，找到就不找了                                       | Boolean值    |
| every   | 查找元素，全部找                                             | Boolean值    |
| reduce  | array.reduce(function(prev, current, currentIndex, arr), initialValue) |              |
| find    | 查找元素                                                     | 查找到的元素 |

备注：参数：function(value,index,array){}

reduce：接收一个函数作为累加器；

```js
var numbers = [1, 2, 3, 4]
var sum = arr.reduce((prev,curr,index,arr)=>{
            console.log(prev,curr,index)
            return prev + curr
},0)

pre中每次传入的参数是不固定的, 而是上次执行函数时的结果保存在了pre中
第一次执行时, pre为0, cur为1
第二次执行时, pre为1 (0+1, 上次函数执行的结果), cur为2
当cur为4时, 数组中的元素遍历完了, 就直接将第四次的结果, 作为reduce函数的返回值进行返回.
```

#### 2.2 字符串

String.trim()     //去掉左右两边的空格；表单提交

#### 2.3 对象

```javascript
Object.defineProperty(obj,prop,descriptor)
//定义对象中型属性或者修改原来属性
Object.defineProperty(obj,prop,{
    value : 1000,
    writable :true/false,
    enumerable:true/false,
    configurable:true/false
})
```

遍历对象的方法：

```javascript
1.for(item in obj)
2.Object.keys(item)  Object.values(obj)
3.Object.getOwnPropertyNames(obj)
4.Object.getOwnPropertySymbols(obj)
```

### 3.函数

#### 3.1 理解

所有函数都是Function的实例，函数也是对象；

#### 3.2 this指向

| 函数类型     | this指向       |
| ------------ | -------------- |
| 普通函数     | window         |
| 构造函数     | 实例对象       |
| 对象方法     | 该方法所属对象 |
| 事件绑定函数 | 绑定事件的对象 |
| 定时器函数   | window         |
| 立即执行函数 | window         |

#### 3.3 改变this的指向：apply，call，bind

| 方法  | 参数                           | 应用                           |
| ----- | ------------------------------ | ------------------------------ |
| call  | fn.call(对象，参数1，参数2)    | 实行继承                       |
| apply | fn.apply(对象，[参数1，参数2]) | 借助math来求数组最大值         |
| bind  | fn.bind(对象，参数1，参数2)    | 不会调用函数，只是返回新的函数 |

bind的应用最广；不需要立即调用；需要改变this指向；

比如：按钮被点击后，是禁用状态，在3秒后，恢复成可点击状态；

### 4.严格模式

```javascript
'use strict'
变量：先定义再使用；不能delete已经定义好的变量；
this：全局作用域下的this是undefined；
函数的参数不能重名；
if，for里面不能写函数；
```

### 5.高阶函数

定义：函数fn接收函数作为参数 或者 return 函数；

```javascript
function fn(callback){
    callback&&callback();
}
function fn(){
    return function(){}
}
```

### 6.闭包

定义：闭包是指有权访问另一个函数作用域中变量的函数；

理解：一个作用域可以访问另一个函数内部的局部变量；

闭包 ：被访问变量的函数；

```java
function fn(){
    var num = 10;
    return function(){
        console.log(num);
    }
}

var mly = fn(); //mly就是返回的函数；
mly();//执行函数，就可以访问到num
```

应用：全局作用域下，访问函数的局部变量；

缺点：内存泄漏；函数执行完，变量应该立即被销毁；但是闭包函数里面，还有其他函数需要访问这个变量，所以不能销毁；

#### 闭包面试题

1.点击li输出对应的索引值

方法一：给每个li绑定index属性；

方法二：闭包，立即执行函数

```javascript
for(var i=0;i<li.length;i++){
    (function(i){
        li[i].onclick=function(){
            console.log(i)
        }
    })(i);
}
```

立即执行函数，传入i；for循环创建了4个立即执行函数，传入了i值，发生了点击事件，显示对应的i；

### 7.递归

定义：函数自己调用自己；

容易发生死循环，栈溢出，所以必须要加上退出条件return；

求：兔子序列（斐波拉契）：1,1,2,3,5,8,13.....

只需要知道n-1，n-2项就好；

```javascript
function fb(n){
    if(n==1 || n==2){
        return 1;
    }
    return fb(n-1)+fb(n-2)
}
```

### 8.浅拷贝&深拷贝

浅拷贝：只是copy一层；复杂对象的话，只是copy了引用地址；

ES6写法：Object.assign(o,obj);



深拷贝：copy多层，每一级别都被拷贝；复杂对象，新建内存空间；

函数实现：递归实现；

进行判断：1.简单数据，直接复制；2.数组，先赋值成[]，再递归调用数组里面的值；3.对象同理；

### 9.以前常用的方法

| 方法        | 参数           | 作用               | 返回值 |
| ----------- | -------------- | ------------------ | ------ |
| split       | str.split("-") | 根据符号切割字符串 | 数组   |
| subStr      | str.subStr()   | 截取字符串         |        |
| join        | arr.join(" ")  | 连接数组成字符串   |        |
| charAt      |                | 第几个字符         |        |
| toUpperCase |                | 转成大写字母       |        |
|             |                |                    |        |
|             |                |                    |        |
|             |                |                    |        |
|             |                |                    |        |

