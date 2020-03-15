## uniapp

### 1.项目创建

```js
npm install -g @vue/cli
vue create -p dcloudio/uni-preset-vue my-project
npm run dev:mp-weixin      //启动项目
使用微信小程序开发工具导入项目：地址是 dist/dev/mp-weixin
```

### 2.样式

使用的是sass，需要安装依赖

```js
npm i node-sass sass-loader
在style标签里面加上  lang="scss"
```

### 3.基础语法

{{}}：使用变量；

:data-color:"color"，自定义属性前面加上冒号；

```js
vue的语法： 前面加冒号，:goodslist="goodslist"
小程序的语法：title="{{titles}}"
```



循环数组，和vue的语法一样；

```js
v-for="(item,index) in list" :key="item.id"
```



条件编译：v-if，v-show

适合频繁使用：v-show；只是css样式的改变；

不适合频繁使用：v-if；直接dom元素有无；



计算属性：computed

### 4.事件的基本使用

```js
@click="handlechange($event)"
data-index自定义属性的方式传参，那就使用event.currentTarget.dataset.index获取属性（小程序写法）
```

vue：

```js
data-radius='100'
event.target.dataset.index
```

### 5.组件的使用

组件传值，组件的注册，和vue是一样的；

全局共享数据实现的方式：

1.通过vue的原型prototype共享数据；

2.globalData（小程序的写法），获取数据

getdata().globalData.baseurl



vue中组件传值：

props，$children，$parent，$on，$emit，Ref，slot，vuex，event bus，$attrs，$listeners



### 6.插槽

slot的使用；



### 7.生命周期







