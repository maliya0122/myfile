### vue-supermall项目

### 0. 复习项目

1.脚手架搭建

```javascript
npm install -g @vue/cli
vue create supermall_test
npm run serve
```

2.项目结构

标题：navbar：44px

底部：MainBar：49px

中间滚动区域：content：position：absolute；

```javascript
.content{
  position: absolute;
  top: 44px;
  bottom: 49px;
  left: 0;
  right: 0;
  overflow: hidden;
}
```

3.功能

（1）better-scroll插件的使用与封装

封装：上拉加载刷新，刷新操作，跳转到哪里（scrollTo）

（2）tab-control组件

先获取三个类型的数据，放在goodlist数组中，点击哪个就切换哪个；

### 1.详情页的联动效果

#### 1.1 点击标题跳转到对应的内容

难点：要获取对应组件的offsetTop，所以要等图片加载完，再获取；

![image-20200203162544488](C:\Users\chen\AppData\Roaming\Typora\typora-user-images\image-20200203162544488.png)

使用this.$refs.scroll.scrollTop方法，根据距离进行页面跳转；（scroll里面封装的方法名可能不一致）

```javascript
获取组件的offsetTop：this.$refs.comment.$el.offsetTop
将offsetTop值一个个存储：this.scrollTopY.push(this.$refs.goodslist.$el.offsetTop);
```

#### 1.2 滑动内容，标题加上对应样式

1.获取滑动的距离

调用scroll.pscroll方法，这是自己封装的名字；然后还要开启属性：:probe-type="3"，不然不能传值；

```javascript
//2.循环比较scrollTopY数组里面的值[0, 14639, 15888, 16104, 1.7976931348623157e+308]
for(let i=0;i<length-1;i++){
    if(this.currentIndex !== i && positionY > this.scrollTopY[i] && positionY < this.scrollTopY[i+1]){
      this.currentIndex = i;
      this.$refs.detailNavBar.currentIndex = this.currentIndex;
    }
}
```

2.思路：通过position和数组里面每个值进行比较，在哪个区间，i是哪个值；最后一个max是自己加上的，不然还要分开比较；hack技术；

length：5；i的取值范围应该是0,1,2,3；

0~14639：i=0；  14639~15888：i=1；  15888~16104：i=2；    16104~max：i=3；

this.currentIndex !== i，这是为了防止重复赋值比较；

#### 1.3 bottomBar

加入购物车事件，主要是保存product数据，放在vuex里面，进行储存；然后在购物车页面就可以使用了；

this.$store.state存储数据，mutations主要是更改，actions进行复杂逻辑；

#### 1.4 BackTop组件的mixin.js

将这个组件相关的内容全部抽取出来，放在mixin.js文件里面，然后导入，注册，就可以了；主要是要封装；没有试过；

### 2.购物车页面

![image-20200205134336653](C:\Users\chen\AppData\Roaming\Typora\typora-user-images\image-20200205134336653.png)

#### 2.1 itemInfo数据

数据：在详情页获取到的数据，要在购物车页面进行使用；所以存储在VUEX里面，this.$store.state里面；

1.数据判断

添加过该商品，那么就是更新count属性；

未添加过该商品，那么就是count=1，将这个商品添加到数组里面；

2.方法

mutations里面进行判断，看到底是addCount，还是addProduct

3.Actions

这个里面进行复杂逻辑的判断；

4.getters

对数组进行整理操作，比如需要的是length，需要的是list

#### 2.2 页面渲染

单个商品的详情页，底部bottomBar组件的封装；

样式都用的是flex布局；加上scroll组件；主要是cartList模块，里面的content需要加上高度；

```javascript
<div class="cartList">
    <Scroll ref="scroll" class="content">
      <div>
        <cart-list-item v-for="item in cartList" 
        :key="item.iid" 
        :item-info="item"
        ></cart-list-item>
      </div>
    </Scroll>
  </div>

//使用高度
.cartList {
  height: calc(100vh - 44px -49px);
  position: relative;
  overflow: hidden;
}
.content {
  height: 100%;
}
//使用定位
.content {
  position: absolute;
  top: 44px;
  bottom: 49px;
  left: 0;
  right: 0;
  overflow: hidden;
}
```

备注：文字...显示，不生效的时候，父组件也要overflow: hidden;

#### 2.3 item的选中状态

商品有选中/未选中状态，这个组件是通过checked属性来显示的；

这个button用在两个地方，一个是item，另一个是bottomBar；



item的被选中/未选中，商品的checked属性就会被修改；

bottomBar的去计算和合计，也要被相应的计算出来；

```javascript
totalprice(){
    return this.cartList.filter(item=>{
        return item.checked
    }).reduce((prev,item)=>{
        return prev + item.price*item.count;
    },0).toFixed(2)
}
//在computed里面，计算totalprice属性
首先筛选被选中的商品，返回的是arr，然后调用reduce方法，计算和；
array.reduce(function(prev, current, currentIndex, arr), initialValue)
//toFixed(2)是保留两位小数的意思，es6新加的
```

bottomBar的被选中/未选中，是通过两种进行判断：

（1）状态，商品全部被选中，那么全选自动就应该是true；否则就是false；

（2）事件，点击按钮，那么全选就应该是true；否则就是false；（组件内的事件  @click.native="allclick"）

#### 2.4 toast组件的封装

vuex里面，actions封装成返回promise对象，和getters一样，可以进行引用；

toast弹窗，老师自己封装的；好复杂；

反正是要先写组件，再在main.js里面，进行配置；

#### 2.5 其他插件

1.fastclick：减少移动端的延迟300ms

2.lazyload：图片懒加载框架，只是刷到图片，才加载；还有占位图；

3.px2vw：css单位转化插件；把px转换成vw；

4.nginx：部署服务器；windows下部署，开启服务以后，把打包好的dist文件夹放在html文件夹下就可以了；

### 3.vue的响应式原理

![image-20200205164344169](C:\Users\chen\AppData\Roaming\Typora\typora-user-images\image-20200205164344169.png)

vue中数据更新，页面也随之更新；不是理所当然的，里面的实现是很复杂的；

### 4.分类页面

使用的是左右滑动，页面布局：

```js
最大的盒子：category
顶部导航栏：NavBar
内容区域：content
样式：使用的是绝对定位
.content{
  position: absolute;
  top: 44px;
  bottom: 49px;
  left: 0;
  right: 0;
  overflow: hidden;
  display: flex;
  flex-direction: row;
}
content里面写上两个scroll组件，每个组件加上left，right；
.left{
  flex:3;
  height: 100%;
}
.right{
  flex:7;
  height: 100%;
}
```




