## Better-Scroll和rem适配等插件的使用

### 一、Better-Scroll

#### 1.页面导入css

在assets文件夹下新建base.css文件，然后在APP.vue文件里导入

```js
<style scoped>
  @import "./assets/normalize.css";
</style>
```

使用ul>li循环出100个数据：

```js
ul>li{$}*4
```

#### 2.原生写法

```js
//这是content内容部分，只是设置了margin值 
.content{
    margin:50px 0 80px 0;
  }
//这是content里面的static部分，固定高度的滚动模块
.static{
    width: 100%;
    height: 300px;
    background-color: cornflowerblue;
    overflow-y: scroll;
  }
```

#### 3.页面使用better-scroll

```js
npm install better-scroll -S
import BScroll from 'better-scroll'


//页面布局
<div class="wrapper" ref="wrapper">
    <ul class="content">
        <li>这是内容</li>
	</ul>
</div>

//wrapper设置css样式
.wrapper{
    margin:50px 0 80px 0;
    overflow: hidden;
    height: 300px;
    background-color: cornflowerblue;
  }

//使用better-scroll，在data里面先注册scroll变量
mounted(){
    this.scroll = new BScroll(this.$refs.wrapper,{
      probeType:3,       //监听position
      pullUpLoad:true,   //上拉加载
      click:true        //监听点击事件
    })

    this.scroll.on('scroll',(position)=>{
      console.log(position)
    })

    this.scroll.on('pullingUp',()=>{
      console.log('上拉加载更多')
    })
  }
```

思路：wrapper需要固定高度，content是内容高度，不需要设置；

#### 4.封装better-scroll组件

封装的结构：有wrapper和content，里面是slot，以后直接是内容替换掉；

```js
<div class="wrapper">
      <div class="content">
          <slot></slot>
      </div>
</div>
```

封装好之后，直接使用：

```js
<scroll class="box">   			//在这里设置高度
    <home-swiper></home-swiper>
	<home-swiper></home-swiper>
	<home-swiper></home-swiper>
</scroll>

.box {
  margin-top: 50px;
  width: 100%;
  height: 300px;
  background-color: royalblue;
  overflow: hidden;
}
//这里设置的content的高度，就是这个组件的高度，所以这个组件的结构就变成了：
<div class="wrapper box">
    <div class="content">
         <home-swiper></home-swiper>
		<home-swiper></home-swiper>
		<home-swiper></home-swiper>
      </div>
</div>
```

注意：

如果ref绑定在组件中，那么通过this.$refs.wrapper获取到的是一个组件对象；

如果ref绑定在普通元素中，那么获取到的就是一个元素对象；

#### 5.box高度的设置

页面结构：

顶部tabBar：50px，底部bottomBar：80px；中间是滚动区域；

##### 5.1 calc()计算高度：不推荐

```js
height: calc(100vh - 50px -80px);   //这种方式不好，需要减
```

##### 5.2定位方式计算高度

```js
.home{
    height: 100vh;
    position: relative;
}
.content {
  width: 100%;
  /* height: calc(100vh - 50px -80px); */
  overflow: hidden;

  position: absolute;
  top: 50px;
  bottom: 80px;
  left: 0;
  right: 0;
}
```

#### 6.其他事件

```js
<!--scroll组件  -->
<template>
  <div ref="wrapper" class="wrapper">
    <div class="content">
      <slot></slot>
    </div>
  </div>
</template>
<script>
import BScroll from "better-scroll";

export default {
  name: "scroll",
  components: {},
  props:{
    probeType:{   //监听方式：3是监听位置
      type:Number,
      default(){
        return 0
      }
    },
    pullLoad:{	 //上拉加载更多
      type:Boolean,
      default(){
        return false
      }
    }
  },
  data() {
    return {
      scroll: null,
    };
  },
  mounted() {
    //1.创建scroll实例
    this.scroll = new BScroll(this.$refs.wrapper, {
      probeType: this.probeType, //监听回弹滑动事件，2：不监听回弹
      pullUpLoad: this.pullLoad, //上拉加载事件
      click: true 				 //scroll组件里面默认是不能点击的
    });

    //2.实时监听事件，把这个position发送给父组件 :probe-type="3"
    this.scroll.on("scroll",(position)=>{
      this.$emit("scroll",position);
    });

    //3.pullingUp上拉加载事件监听
    this.scroll.on("pullingUp",()=>{
      this.$emit("pullingUp");
    });

  },
  methods: {
    //对scroll方法进行封装，方便调用，为了保证scroll已经挂载成功
    BackTop(x, y, time = 300) {
      this.scroll && this.scroll.scrollTo(x, y, time);
    },
    //刷新操作，重新计算height
    refresh(){
      this.scroll && this.scroll.refresh();
    },
    //完成上拉刷新操作
    finishPullUp(){
      this.scroll && this.scroll.finishPullUp();
    },
    //返回页面的y值
    getscrollY(){
      return this.scroll ? this.scroll.y : 0;
    }
  },
  computed: {},
  watch: {}
};
</script>
<style scoped></style>
```

```js
//保持页面状态
activated(){
    //页面回来的时候，回到这个位置，最好重新刷新一次
    this.$refs.scroll.BackTop(0,this.scrollY,0);
    this.$refs.scroll.refresh();
  },
deactivated(){
    //页面离开的时候记住scrollY值
    this.scrollY = this.$refs.scroll.getscrollY();
  }

//上拉加载更多数据的时候，请求下一页的数据
getHomeGoods(type) {
      const page = this.goods[type].page + 1;
      getHomeGoods(page, type).then(res => {
        var data = res.data.data.list;
        this.goods[type].list.push(...data);
        this.goods[type].page += 1;
      });
    },
```

### 二、fastclick移动端延迟

移动设备上的浏览器默认会在用户点击300ms后才会触发点击事件，这是为了检测用户是否在做双击，为了立即响应用户的点击事件，才有了fastclick。

```js
npm install fastclick --save
//在main.js里面进行导入
import fastclick from 'fastclick'
//调用attach方法
fastclick.attach(document.body)
```

### 三、vue-lazyload图片懒加载

图片需要显示在屏幕上时才加载；

```js
npm install vue-lazyload --save

//导入
import VueLazyload from 'vue-lazyload'
//安装
Vue.use(VueLazyload,{
  loading:require('./assets/img/common/placeholder.png')	//占位图
})

//代码里面使用
把img里面的 :src="showimage"  改成 v-lazy="showimage"
```

### 四、vant框架

移动端的UI框架：vant

```js
npm i vant -S

//引入方式：全局引入，按需引入，CDN引入

//1.自动按需引入组件 (推荐)
npm i babel-plugin-import -D	
//在babel.config.js中配置
module.exports = {
  presets: [
    '@vue/cli-plugin-babel/preset'
  ],
  plugins: [
    ['import', {
      libraryName: 'vant',
      libraryDirectory: 'es',
      style: true
    }, 'vant']
  ]
}
//在main.js中进行配置
import { Button } from 'vant';
Vue.use(Button);

//2.通过CDN引入，感觉和ele引入一样

```

### 五、Rem 适配

- postcss-pxtorem是一款 postcss 插件，用于将单位转化为 rem；
- lib-flexible用于设置 rem 基准值；

```js
npm install postcss-px2rem --save-dev
npm i -S  lib-flexible

//main.js引入
import 'lib-flexible'
//index.html下的viewport标签去掉
//配置vue.config.js
module.exports = {
    css: {
        loaderOptions: {
          css: {},
          postcss: {
            plugins: [
              require('postcss-px2rem')({
                remUnit: 37.5
              })
            ]
          }
        }
    },
}
```

或者新建postcss.js文件，里面按照官网上面的进行配置；











