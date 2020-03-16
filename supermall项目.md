---
typora-root-url: mdimg
typora-copy-images-to: mdimg
---

## supermall项目

### 一、vue3搭建项目&github的使用

1.脚手架3搭建项目：

![](/../img/image-20200113220733912.png)

2.github的使用

在github新建了一个仓库是supermall，在本地使用vue create mymall（vue3），新建vue项目，把mymall文件夹的内容，与supermall仓库连接起来。

```shell
git init //初始化mymall文件夹，多了.git文件夹
git touch READMEgit //可以不要
git add README //可以不要
git commit -m 'first commit'  //提交文件
git remote add origin https://github.com/maliya0122/supermall.git
git push -u origin master
```

下次提交就可以直接用小乌龟提交了。

3.服务器地址

baseURL: http://123.207.32.32:8000/api/hy

baseURL : http://106.54.54.237:8000/api/hy

### 二、项目初始化

#### 1.划分目录结构

public：就像之前的static静态资源文件夹一样，打包的时候直接压缩到dist文件夹下；

src：主要是这个文件夹下开发

> assets：存放css和img资源；
>
> common：公共的方法，写的js工具代码；
>
> components：存放公共组件，里面细分；
>
> network：网络请求；
>
> router：路由；
>
> store：公共的常量、方法等；
>
> views：不同模块的页面；
>
> APP.vue：主文件，一般不改；
>
> main.js：入口函数；

#### 2.引入css文件

normalize.css：初始化css，github上找的；

base.css：公共css，自定义一些变量，字体；

base.css里面需要引入normalize.css才能生效，APP.vue也要引入base.css

> 注意：
>
> @import "./normalize.css"    //在样式里面引入样式，需要加上@；
>
> <style>
> @import "assets/css/base.css";
> </style>
>
> script标签里面引入文件，直接写别名即可；
>
> 在src里面引入路径，需要加上~；

注意：在base.css里面，定义了根变量，后面可以直接使用

```javascript
:root {
  --color-text: #666;
}
color: var(--color-text);
```

#### 3.配置文件路径(给文件夹配置别名)

vue2配置：webpack.base.config.js里面，

```javascript
resolve: {
    extensions: ['.js', '.vue', '.json'],
    alias: {
      '@': resolve('src'),
      'components':resolve('src/components'),
      'views':resolve('src/views'),
      'assets':resolve('src/assets')
    }
  }
```

vue3配置：新建vue.config.js

```javascript
module.exports={
    configurewebpack:{
        resolve:{
            alias:{
                'assets':'@/assets',
                'common':'@/common',
                'components':'@/components',
                'views':'@/views',
                'network':'@/network'
            }
        }
    }
}
```

vue2有一个.editorconfig文件，主要是给代码的风格进行统一的；

vue3没有了，所以最好是复制过来；

#### 4.tabbar的搭建

这是tabbar是底部导航栏，相当于大的框架，之前已经封装过。

tabbar，tabbarItem：这两个不关心业务逻辑，只是封装组件，以后直接用就行；放在common里面；

**注意：tabbar自适应，使用flex布局；**

MainBar：是加上了图片，文件这些的具体资源，属于业务相关的了；放在content里面；

```javascript
思路：
1.先写tabbarItem，加上具体资源这些进行测试，如果都是正常的，再写成slot插槽模式；
注意：插槽模式写法要在tempalte里面用v-slot；
<navbar>
      <template v-slot:center>
        <div>购物街</div>
      </template>
</navbar>

2.路由的路径path，直接配置在MainBar的tabbarItem里面，所以在tabbarItem使用path时，需要加上props
在点击tabbarItem的时候，直接使用路由跳转命令：this.$router.push(this.path);

路由组件：const home = () => import("../views/home/home.vue");

3.是否点击：isActive，都是在computed属性里面计算得到的；
是根据路由的路径是否包含this.path来判断的；以此来显示图标和文字的颜色；
文字的颜色是绑定的style：return this.isActive ? { color: this.styleColor } : {};
```

### 三、home模块开发

#### 1.获取页面数据

axios的封装，request.Js是封装axios的实例，home.js是封装home模块所需要的请求，这样便于后面的修改和维护；有一天這個axios框架不更新了，就需要一直改；所以最好是封裝一层axios的请求实例即request.js，返回的是axios的默认配置和实例对象；在home.js里面，引用request.js，传入参数就好；

```javascript
request.js的返回值：
return instance(config);

home.js：
getHomeMultidata()函数，返回值是return request({url:"/home/multidata"})，就是说返回的是一个promise对象，调用的时候直接用.then()即可；
res的赋值，函数的压栈和出栈，res的指针被释放了，res对象如果没有指针的话，就会被回收；所以在data里面创建result来指向这个res；
```

#### 2.轮播图swiper组件

这个组件是老师自己封装的，以后都可以直接使用；

**（特别注意：使用的时候，一定要加上v-if="topbarimg.length"），不然不会动起来的**

**备注**：

**1.17号自己使用的是vue-awesome-swiper插件，使用的时候碰到2个问题：（有待解决）**

**1.自动循环，没有生效，播放到最后一张就停止了；按照网上说的，又报错；**

**2.切换到其他页面，轮播图又停止了；**

Mint-ui上面有很多vue插件，以后有时间学习怎么用；

swiper模块和recommend模块，都是先搭好框架，再去里面填数据；

> 注意：要用到父组件的变量时，子组件要在props里面定义好；以对象的形式；

#### 3.tabControl组件开发

组件切换，只是传入的值不同而已；所以写在了content里面；

栏目的吸顶操作：（这个属性比较新，适合移动端，ie9之前不适配）

position: sticky;

top:44px;

```js
:class="{item, active: index === currentIndex }"      //三元运算符
```

#### 4.商品模块

##### 4.1 goods数据的模型设计：

pop，sell，new三个模块，使用goods这个变量进行存储；

```javascript
goods={"new":{page:0,list:[12]},
       "old":{page:0,list:[4,5,6]},
       "hehe":{page:0,list:[4,5,6999]}
}
//goods是一个对象，里面存储着三个键值对，取值方法：
goods.new.page  或者 goods["new"].type
```

##### 4.2 获取商品的三个模块的数据

封装接口的函数，然后在methods里面再次进行封装，直接在created里面调用即可；

```javascript
this.goods[type].list.push(...data);
...：是把data数组里的数据，一个个解析出来，然后再全部放在list里面；返回值是元素的个数；
concat，也是连接数据，返回值是新数组；
```

##### 4.3 将数据与页面组件结合起来

home.vue调用goodslist组件，goodslist组件调用goodslistitem组件，最后在item组件里面进行数据的处理和展示；样式也是写在item文件里面；样式写好以后就是栏目的切换；

那就是把controlBar的点击事件，传出参数，使用this.$emit("itemClick",index)，传给父组件进行监听和调用；

#### 5.Better-Scroll的用法

以前是iScroll插件，现在用的是Better-Scroll插件；

> 也是和axios一样的思想，万一哪天插件停止更新了，每个页面都要改；所以要对Better-Scroll先进行一下封装，比如封装成Scroll.vue文件，以后每个页面需要调用Better-Scroll插件的时候，直接导入Scroll.vue文件引用组件就好；插件要改的话，也只用改Scroll.vue文件就好；

scroll组件的格式就是外面是wrapper，里面是一个content，再里面是slot插槽；

```javascript
mounted(){
    this.scroll=new BScroll(this.$refs.wrapper,{
      probeType:3, //监听回弹滑动事件，2：不监听回弹
      pullUpLoad:true,   //上拉加载事件
      click:true   //scroll组件里面默认是不能点击的
    });}

this.scroll.scrollTo(x, y, time);  //这个方法是滑动到0,0
注意：监听回弹事件，最好不要写死，有的页面不需要实时监听回弹；
```

在home.vue里面使用的时候，home组件的高度设置：100vh（视口高度）

上面有44的topBar，下面有49的MainTab，所以中间的滑动区域content的高度，使用绝对定位来实现；

#### 6.BackTop组件的封装与使用

BackTop组件的功能：

1.上滑超过1000px的时候，backtop组件显示/隐藏；

2.点击组件，滑动到顶部；

3.上拉刷新加载新数据

思路：先封装scroll组件，新建scroll实例，在home.vue文件中直接使用<scroll>标签进行使用，scroll.vue组件封装：在挂载的时候就创建实例，这些属性值，有些页面并不需要实时监听，所以需要有默认值，假如页面需要使用这个组件的话，绑定对应的属性进行传值就好；

```javascript
mounted() {
    //1.创建scroll实例
    this.scroll = new BScroll(this.$refs.wrapper, {
      probeType: this.probeType, //监听回弹滑动事件，2：不监听回弹
      pullUpLoad: this.pullLoad, //上拉加载事件
      click: true //scroll组件里面默认是不能点击的
    });

    //2.实时监听事件，把这个position发送给父组件
    this.scroll.on("scroll",(position)=>{
      this.$emit("pscroll",position)
    });

    //3.pullingUp事件监听
    this.scroll.on("pullingUp",()=>{
      this.$emit("pullingUp")
    });
  }
```

1.BackTop组件：使用fixed定位进行，给组件绑定原生事件：@click.native="hehe"

2.scroll使用ref="scroll"，可以获取子组件，访问子组件，方法，属性，数据

3.注意：绑定属性的时候，驼峰需要改成-，绑定事件的时候，驼峰不需要改；

```javascript
:pull-load="true"
@pullingUp="loadmore"
```

4.上拉加载刷新

```javascript
this.$refs.scroll.BackTop(0,0);//滑动到顶部
this.$refs.scroll.finishPullUp();//完成上拉操作
this.$refs.scroll.refresh();//重新刷新
```

7.上拉刷新加载的bug

##### 7.1 向下刷新时，刷不动

由于better-scroll初始化的时候，会计算height高度比如是1000，但是此时图片还没有全部加载出来，

等到图片全部加载出来的时候，向下刷新的时候，就出现刷新不了的问题；

解决方法：等到图片加载完，调用refresh方法重新计算height；

图片加载完，原生js调用的是onload方法，vue调用的是load方法，加载完成，就发送这个事件给总线；

```js
总线：不同的页面都可以接收/发送事件
但是要先在main.js里面创建：Vue.prototype.$bus=new Vue()
this.$bus.$emit("imgload"，参数)  //发送事件
this.$bus.$on("imgload",回调函数)  //接收事件
```

created和mounted时期，进行接收/发送事件；

created时期：就在请求数据；

mounted时期，进行图片的加载回调，不然就会不同步；因为scroll也是在mounted时期创建的；

##### 7.2 refresh太频繁，一次性刷新了30次

防抖动debounce/节流throttle：https://www.cnblogs.com/smallpen/p/10289050.html

采用的是防抖动函数，进行刷新的限制；

#### 8.TabControl的吸顶功能

问题：由于使用了Better-Scroll导致吸顶效果失效了；

解决思路：采用offsetTop，当获取到TabControl的offsetTop高度的时候，当滑动的高度大于这个offsetTop时，就固定TabControl；

1.获取offsetTop数据：（给tabcontrol组件绑定ref）

```javascript
this.$refs.tabcontrol.$el.offsetTop
```

问题：图片没有加载完全，就计算出来了这个值，不准确；所以要等图片加载完成，才计算这个值；

解决：在轮播图组件那里监听图片加载事件，并发送给父组件进行监听；获取到offsetTop的值；

在监听页面滚动事件中加入判断，如果大于offsetTop就添加fixed属性进行固定；

2.fixed属性固定没有什么用，因为betterscroll组件，对fixed插件有影响，使用的是transform距离移动的；

解决：在scroll上面再加一个tabcontrol组件（设置成relative，占据文本位置），设置成隐藏，制造假象；

3.tab的选中项要统一；就是currentIndex=index；

#### 9.保证home的原始浏览位置

首页切换到分类页面，马上就会被销毁；所以要加上在router-view外面加上<keep-alive>标签；

页面离开：deactive，保存scrollY的值；

页面回来的时候：active，页面马上刷新到scrollY这个位置；

### 四、detail模块开发

#### 1.跳转到商品详情页

```javascript
先在路由页面，注册组件，写路由跳转路径；
1.路由跳转：  
this.$router.push({ path:"/detail", query:{  id : "123"} })
this.$router.push("/detail/" + this.goodsitem.iid);
2.在detail页面获取到iid：        
this.iid = this.$route.params.iid;
```

#### 2.详情页面的开发

2.1 顶部navbar：

使用的是navbar组件，自己加上样式；点击不同的item，样式不一样：

```javascript
@click="itemClick(index)"
:class="{ active: index === currentIndex }"
```

2.2 轮播图swiper

> 问题：这次使用的轮播图插件，没有自动轮播，加上v-if又报错；
>
> 原因：前面定义tipimgs的时候，用的是null，应该是[]；
>
> 
>
> 问题：详情页，使用columns[0]，也是报错；
>
> 原因：数据还没有加载过来；解决也是加上v-if，判断这个goods数据是否存在；
>
> 
>
> 问题：每次进来数据没有自动更新，因为加上了keep-alive，没有销毁；
>
> 解决：加上exclude；

2.3 商品信息数据

1.数据来自很多不同的值，思路：放在对象里面；使用的时候直接传值；

```javascript
export class BaseInfo{
    constructor(itemInfo, columns, services){
        this.title = itemInfo.title;
        this.desc = itemInfo.desc;
        this.lowNowPrice = itemInfo.lowNowPrice;
        this.lowPrice = itemInfo.lowPrice;
        this.discountDesc = itemInfo.discountDesc;
        this.columns = columns;
        this.services = services;
    }
}
```

2.有很多轮播图，对象使用的时候，还没有加载出来 ，浏览器就会报错；所以需要加上判断：

```javascript
v-if="Object.keys(GoodsParam).length !== 0"
```

3.flex布局很好用；

4.需要加载多少张图片，要等图片加载完，才发送imgload事件；可以使用watch属性来观察数据的变化；只请求一次；

```javas
watch: {
    detailInfo(){
      this.imglength = this.detailInfo.detailImage[0].list.length;
    }
  }
```

5.mixin的应用

home和detail页面都在监听imgload事件，当离开home页面的时候，取消监听事件；

this.$bus.$off("imgload",函数);

这两个页面都要写这串代码，所以可以把这段代码放到一个js文件中；

mixins:[js文件名]