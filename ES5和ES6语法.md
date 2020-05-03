## ES5和ES6语法笔记

### 一、ES5

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



### 二、ES6

### 1.let&const&var

let声明关键字，{}，具有块级作用域；

let没有变量提升；

let暂时性死区；for里面，i

const声明变量，内存地址不能修改；

### 2.解构赋值

数组的解构：一一对应

let [a,b,c] = [1,2,3];

对象的解构：

let {name:myname,sister='have no sister'} = {name : "lisi" ,age :10};

对name重新命名为myname；

sister，设置默认值；对象里面是undefined，才会使用默认值；

### 3.箭头函数

const fn = () =>{}

**箭头函数this的指向：**https://www.cnblogs.com/fanzhanxiang/p/8888963.html

ES6中新增了箭头函数这种语法,箭头函数以其简洁性和方便获取this的特性。下面来总结一下他们之间的区别：

**普通函数下的this:**

- 在普通函数中的thi指向的是函数的调用者，在默认情况下，this指的是window，
- 在严格模式下,没有直接调用者的函数中的this是 undefined
- call,apply,bind(ES5新增)绑定的,this指的是 绑定的对象

**箭头函数中的this:**

-  箭头函数没有自己的this, 它的this是继承父亲而来; 默认指向在**定义它时所处的对象(宿主对象),**不会因为调用方法的改变而改变；
-  而不是执行时的对象, 定义它的时候,可能环境是window,也有可能是其他的。

```javascript
var jelly={
            name:"jelly",
            hobbies : ["吃饭","睡觉","打豆豆"],
            gethobbies:function(){
                // this.hobbies.map(function(hh){
                //     console.log(this.name + hh);
                // })
                //回调函数，单独执行的时候，this指向的是window
                
                this.hobbies.map(hh=>{
                    console.log(this.name + hh);
                })
            }
        }
        jelly.gethobbies();
```

### 4.剩余参数和扩展运算符

#### 4.1 剩余参数（函数里面的）

function sum(n1,...args)

...args：放在一个数组中，然后用forEach循环出来；

const person=["apple","123",100,100,90,80];

const [name,id,...score] = person;

![image-20200202220058499](C:\Users\chen\AppData\Roaming\Typora\typora-user-images\image-20200202220058499.png)

#### 4.2 ...扩展运算符

把可遍历对象扩展放在一个新的数组中；

1.连接数组：const members = [...arr1,'mary',...arr2];

2.把字符串分割成一个个字符："l","o","v","e"



### 5.内置对象扩展

#### 5.1 数组

| 方法               | 用途                                                         | 返回值     |
| ------------------ | ------------------------------------------------------------ | ---------- |
| push(...arr)       | 连接数组                                                     | 新数组     |
| find()             | 查找对象，返回第一个符合条件的对象                           | 返回对象   |
| findIndex()        | 查找对象，返回索引值；没有就是-1                             | Boolean值  |
| includes(2)        | 是否包含某元素，返回true/false；                             | Boolean值  |
| Array.from(类数组) | 类数组转换成真正的数组；然后调用数组的方法；map，reduce这些； | 新数组     |
| Array.of(1,2,3)    | 转换成[1,2,3]                                                | 新数组     |
|                    |                                                              |            |
| forEach            | 循环遍历数组                                                 | 无返回值   |
| reduce             | 求和                                                         | 无         |
| every              | 查找元素，全部找                                             | Boolean值  |
| some               | 查找元素，找到就不找了                                       | Boolean值  |
| filter             | 筛选数组                                                     | 返回新数组 |
| map                | 循环遍历数组，不改变原数组                                   | 返回新数组 |

备注：array.reduce(function(prev, current, currentIndex, arr), initialValue)

#### 5.2 字符串

str.startWith("51"，9)；以什么开头

str.endsWith("11")：以什么结尾

str.includes("12")：包含什么

str.repeat(4)：重复字符串

### 6.模板字符串

```javascript
const person='jelly'
const sentence = `${person} is mouse`
``也可以当做参数传递给函数 getdata`data ${person}`
```

变量可以使用${变量}的方式使用；

### 7.for of 循环

for：太繁琐；

forEach：循环简洁，但是不能中止循环；

for index in fruits：循环的是属性名；缺陷：会把原型链上的都循环出来；

for item of fruits：循环的是属性值，可以中止循环。不会循环非属性值；

应用场景：对数组进行循环；

for(let [index,item] of fruits.entries())

### 8.Promise

1.简介

就是一个承诺，下单了，有个订单号，这个订单是被接单，还是被拒单，都有反馈结果；

axios是基于promise API的，直接.then()，.catch；

```javascript
const p = new Promise((resolve,reject)=>{
    setTimeout(()=>{
        resolve("success")
    },1000)
})
p.then(data=>{ console.log(data) })
 .catch(err=>{ console.log(err) });
```

2.处理多个promise：all，race

```javascript
Promise.all([pro1,pro2])
		.then(res=>{
    const [users,movie] = res;  //对象解构
})
.catch(err=>{ console.log(err) });
```

### 9.Symbol基本数据类型

1.用来创建唯一的标识符；当做属性名；

```javascript
const peter = Symbol(); //生成唯一的标识符，解决对象命名冲突，里面可以写 描述
const class={
    [Symbol('lili')]:{name:'lili',age:10},
    [Symbol('loli')]:{name:'loli',age:20},
    [Symbol('loli')]:{name:'loli',age:210}
}
```

2.给对象添加symbol属性名，要用[]，不能使用.

3.不能使用普通的遍历，for  in也不行；Object.getOwnPropertySymbols(classRoom) 

```js
let classobj = Object.getOwnPropertySymbols(class)
//返回值：就是symbol属性值

let classall = Reflect.ownKeys(obj)
//返回所有的属性值：symobol和常规的
```

### 10.ESLint

airbnb：js代码规范

配置文件：网上找配置；安装插件，选择模式；

运行：eslint bad.js

### 11.class

class：ES6新加的类；就相当于构造函数；

```javascript
class Star{
    constructor(uname){
        this.uname = uname;
    }
    sing(){
        console.log('唱歌');
    }
    static dance(){
        console.log('跳舞');//静态方法，只有Star.dance()，才能调用
    }
    set github(value){
        this.githubName = value;//set方法
    }
    get github(){
        return `http://github.com/${this.githubname}`;//get方法
    }
}
const ldh = new Star('刘德华');
```

继承：extends：儿子继承父亲

super：访问和调用父类上的函数；可以调用构造函数，也可以调用普通函数；

```javascript
class Father{
    constructor(x,y){
        this.x = x;
        this.y = y;
    }
    sum(){
        console.log(this.x + this.y);
    }
}
class Son extends Father{
    constructor(x,y){
        super(x,y); //调用父类的构造函数，相当于Father.call(this,x,y)
        this.x = x;
        this.y = y;
        super.sum(x,y);//调用父类的普通函数
    }
    super.say();
}
```

类里面的this指向：

constructor：指向创建的实例对象：ldh

方法：调用者；

### 12.模块

导出：

export default（可以自己命名，只能默认导出一个default）

export 多个变量（按照文件里的变量命名）

```javascript
//任意导出
const apikey = '123';
export default apikey;//默认导出
import apikeykey from './apikey.js'   //这里可以使用任意名字

//命名导出
export const apikey = '123';
export const url = 'www.hao.com';
import { apikey,url } from './123.js'   //使用指定的名字导出
```

babel插件：将ES6转换成ES5；浏览器不兼容ES6；

babel-polyfill，有的新属性方法，识别不来的；

### 13.遍历器Iterator

```js
它提供了一种统一的遍历数据的方法;只要数据结构，部署了symbol.iterator属性，那么就可以使用for  of进行遍历，使用next()方法进行遍历；返回值：{value，done}

数组：
colors=['red','blue','yellow']
const i = colors.entries()
i.next()   //value: Array(2), done: false

const v = colors.value()
v.next()   //value: "red", done: false
```

运用到的场合：

解构赋值，扩展运算符，yield\*，for of，Array.from，set，map



### 14.生成器generator

```js
function* helloworld(){
    yield 11+10;
    yield 'world';
    return 'ending';
}

let hw = helloworld();  //返回的是遍历器对象（Iterator Object）
console.log(hw.next());
console.log(hw.next());
```

generator函数就是iterator接口生成函数；所以需要调用next()方法，进行执行；



2.next参数：该参数就会被当作上一个`yield`表达式的返回值。













### 15.Set：新数据结构，类似于数组

Set与Array：没有索引值；数组去重；元素是唯一的；

```javascript
const colors = new Set();
1.方法：
[...colors]
colors.add('red');
colors.add(5);
colors.size;
colors.delete(5);
colors.has(5);


2.数组、字符串去重
[...new Set([11,12,11,13])]   //使用扩展运算符

const items = new Set([1, 2, 3, 4, 5]);
const array = Array.from(items);  //转换成数组


3.4种遍历方法
keys()，values()，entries()，forEach()
for(let i of set.keys()){
     console.log(i)
}
set：键和值都是一样的；遍历器默认是values
```

weakSet：成员只能是对象；对象是弱引用（没有被引用的话，就会垃圾自动回收机制）

```js
方法：add，has，delete
new weakSet([{name:'123',age:19},{name:'11',age:90}])

不能进行遍历，因为是弱引用机制，不知道啥时候会被回收；
```

### 16.Map：新数据结构，类似于对象

存储的是键值对，相当于对象；键值可以是任意类型的数据；

![image-20200223202417265](C:\Users\chen\AppData\Roaming\Typora\typora-user-images\image-20200223202417265.png)

```javascript
const map = new Map([
  ['name', '张三'],
  ['title', 'Author']
]);

1.方法
set,get,has,delete,clear,size
m.set({name:00},'123')

2.遍历
keys()，values()，entries()，forEach()

3.转换成数组:[...map]
```

weakMap：只接受对象作为键值名（除了null），键名弱引用；

应用：DOM 节点作为键名；如果删除了，就会被销毁，不存在内存泄漏问题；

### 17.await和async

使用这两个关键字，可以像同步执行一样，执行异步代码；



