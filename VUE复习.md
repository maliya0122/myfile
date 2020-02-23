## VUE语法

## 1.VUEX

### 1.1 定义及使用场景

定义：状态管理工具；统一存放公共数据和方法；进行保存和管理，还是响应式的；

**State**：存放状态；（store唯一更新的方式：提交**Mutation**）

**Getter**：相当于compputed模块，计算属性；

**Mutation**：相当于事件；必须是同步事件；参数（state，payload），向 `this.$store.commit('xxx')` 传递事件；

**Action**：操作行为（比如点击），action提交的是**Mutation**，可以包含任意异步操作；在组件中使用 `this.$store.dispatch('xxx')` 分发 action；

**Module**：模块，里面可以划分私有vuex；

![image-20200203200929159](C:\Users\chen\AppData\Roaming\Typora\typora-user-images\image-20200203200929159.png)

```javascript
//mutations：里面的方法，使用state的变量
addCart(state,payload){
      const oldProduct = state.carList.find(item=>{
        return item.iid === payload.iid;
      })

      if(oldProduct){
        oldProduct.count+=1
      }else{
        payload.count=1;
        state.carList.push(payload);
      }
    }
```

这是最原始的方法：定义carList变量，遍历这个数组，通过iid进行对比，如果存在oldProduct，那么就把这个商品的count加1，如果不存在，就把这个商品添加到数组中，并把这个商品的count赋值成1；

最好是进行分离到各个文件，然后将函数名设置成常量；（这个没做）

### 1.2 state

主要用来存储全局变量，状态这些；

```javascript
const carList=[];
```

### 1.3 getter

主要是对数据进行计算，比如得到carList的长度，carList的数据；

````javascript
const getters = {
    cartList(state) {
      return state.cartList
    },
    cartCount(state, getters) {
      return getters.cartList.length
    }
  }
export default getters
````

`mapGetters` 辅助函数仅仅是将 store 中的 getter 映射到局部计算属性：

```javascript
import { mapGetters } from 'vuex';
computed: {
    ...mapGetters(['cartCount','cartList']);//一一对应
    ...mapGetters({
        doneCount: 'doneTodosCount'
    });//取别名使用
 }
```

### 1.4 mutation

主要是用来更新state的状态，单一函数：

```javascript
//mutations只完成单一的操作，比较好追踪
mutations: {
    addCounter(state, payload){
      payload.count++;
    },
    addToCart(state, payload){
      payload.checked = true;
      state.cartList.push(payload);
    }
  }
```

### 1.5 action

主要是做函数的逻辑处理，也可以使用mapActions进行引用；

```javascript
 //actions完成复杂的函数逻辑，参数是context上下文，payload参数
  actions: {
    addCart(context,payload){
      const oldProduct = context.state.cartList.find(item=>{
        return item.iid === payload.iid;
      })

      if(oldProduct){
        context.commit('addCounter',oldProduct);
      }else{
        payload.count = 1;
        context.commit('addToCart',payload);
      }
    }
  }

import { mapActions } from 'vuex';
methods: {
    ...mapActions(['cartCount','cartList']);//一一对应
    ...mapGetters({
        doneCount: 'doneTodosCount'
    });//取别名使用
 }
```

## 2.面试题

### 2.1 v-if和v-show

![image-20200205184206527](C:\Users\chen\AppData\Roaming\Typora\typora-user-images\image-20200205184206527.png)

应用：需要频繁切换，那就用v-show；条件判断，就用v-if；

### 2.2 文件中的css不被影响

1.css   

```javascript
 <style scoped>
```

2.scss

```javascript
样式穿透
 <style scoped lang="scss">
     父元素 /deep/  子元素
	.parent /deep/ .child
```

3.stylus

```javascript
父元素 /deep/  子元素
父元素 >>>  子元素
```

### 2.3 v-model的使用

实现双向绑定，应用在表单元素；主要是value值；

在input，select，文本域中配合value使用；

### 2.4 标签绑定事件

![image-20200205190254506](C:\Users\chen\AppData\Roaming\Typora\typora-user-images\image-20200205190254506.png)

### 2.5 vue-loader的用途

加载vue文件，可以写es6，scss，template

### 2.6 NextTick

说明：$nextTick是在下次DOM更新循环结束之后执行延迟回调，在修改数据之后，使用$nextTick，则可以在回调中获取更新后的DOM。

## 3.生命周期

定义：相当于vue实例的每一个阶段；

比如：created相当于刚出生这个阶段，mounted相当于上学阶段；

这个阶段内，可以在里面写自己的回调函数；当vue实例走到某个阶段，就会执行里面的函数；

## 4.slot插槽

### 4.1 基本使用

旧的写法：

```javascript
//子组件
<template id="cpn">
    <div>
        <div>我是组件</div>
        <!-- 简单插槽的使用 -->
        <slot></slot>
    </div>
</template>

//父组件
<div id="app">
    <h3>这是父元素的数据</h3>
    <h3>{{mess}}</h3>
    <cpn>这是插槽的位置</cpn>
</div>
```



新的写法：

```javascript
与旧的写法一致
```



### 4.2 具名插槽

旧的写法：

```javascript
//子组件
<template id="cpn">
    <div>
        <div>我是组件</div>
        <div>我也是组件</div>
        <!-- 具名插槽 -->
        <slot name="left">左边</slot>
        <slot name="center">中间</slot>
        <slot name="right">右边</slot>
    </div>
</template>

//父组件
<div id="app">
    <h3>这是父元素的数据</h3>
    <h3>{{mess}}</h3>
    <cpn>
        <!-- 修改左边插槽 -->
        <button slot="left">这儿是左边</button>
    </cpn>
</div>
```



新的写法：

```javascript
//子组件
<template id="cpn">
  <div class="container">
    <header>
      <slot name="header">这是头部</slot>
    </header>
    <main>
      <slot>这是默认内容</slot>
    </main>
    <footer>
      <slot name="footer">这是footer</slot>
    </footer>
  </div>
</template>

//父组件
<cpn>
    <template v-slot:header>
        <h1>Here might be a page title</h1>
    </template>

    <p>这是main部分</p>

    <!-- <template v-slot:default>
        <p>A paragraph for the main content.</p>
        <p>And another one.</p>
    </template> -->
</cpn>
```

使用方法：需要使用template标签和v-slot



### 4.3 作用域插槽

目的：父组件替换插槽的标签，但是内容由子组件提供；

例题：

子组件的数据：color:['red','blue','green']

父组件只是去展示数据，但是想写成别的样式；

![image-20200210214108589](C:\Users\chen\AppData\Roaming\Typora\typora-user-images\image-20200210214108589.png)

```javascript
//子组件，使用的是子组件的data
<template id="cpn">
    <div>
        <hr></hr>
        <div>我是组件</div>
        <div>我也是组件</div>
        <!-- 作用域插槽的使用 -->
        <slot :data='colors'>    //子组件将colors数据绑定到data属性上
            <h4>诗句：</h4>
            <div v-for="(item,i) in colors" :key="i">
                {{item}}
            </div>
        </slot>
    </div>
</template>
//父组件：使用子组件的数据，只是改下样式
<cpn>
    <template slot-scope="scope">   	//父组件使用slot-scope属性可以访问子组件
        <h4>这是新的插槽：</h4>
        <div v-for="(item,i) in scope.data" :key="i">  	//scope.data就是访问组件的data
            <h4>{{item}}</h4>
        </div>
    </template>
</cpn>
```

父组件使用slot-scope属性可以访问子组件，为子组件取的别名是scope，上面有data属性，可以访问到colors数据；



父=>子：父：绑定属性传值，子：props接收

子=>父：通过事件的触发，this.$emit(事件，数据)

​				通过slot方式，也可以发送给父组件



新的写法：

```javascript
//子组件
<slot :data='colors' name='child'>
    <h4>诗句：</h4>
    <div v-for="(item,i) in colors" :key="i">
        {{item}}
    </div>
</slot>

//父组件
<template v-slot:'child' = "slotProps">
    <h4>这是新的插槽：</h4>
    <div v-for="(item,i) in slotProps.data" :key="i">
        <h4>{{item}}</h4>
    </div>
</template>
```

写法只是从slot-scope变成了v-slot，后面是slot的name；

## 5.vue-router路由

#### 5.1 hash和history

1.改变url的hash：就是锚点(#)，本质上是改变window.location.href属性，页面不会进行刷新

```javascript
location.hash = 'aaa'		//页面的url改变了，但是不会向后端发送请求
```

2.html5的history对象修改url（一般用的mode：history）

```javascript
history.pushState({},'','home')
history.replaceState({},'','home')
history.back()
```

#### 5.2 router-link

1.to：跳转到/home路径

2.tag：渲染成什么组件

3.replace：没有返回按钮

```javascript
<router-link to='/home' tag='button'></router-link>
<router-view></router-view>  //占位符
```

#### 5.3 通过代码跳转路由

点击按钮，路径修改为/home

```javascript
this.$router.push('/home')
```

#### 5.4 动态路由

1.path:'/user/:userid'，需要加上用户id

所以在 to那里，要加上userid

2.页面上想显示userid的值

```javascript
this.$route.params.userid
```

#### 5.5 路由懒加载

打包的时候，会一次性把js文件都请求下来，文件很大，花费的时间就很长；

路由进行懒加载后，把不同路由对应的组件打包成不同的js代码，然后当路由被访问的时候才加载对应组件；

```js
const Foo = () => import('../Foo.vue')
```

#### 5.6 路由的嵌套

/home下面还有路径：/home/user，/home/phone

```javascript
{
    path: '/home',
    component: Home,
    redirect: '/welcome',
    children: [
      { path: '/welcome', component: Welcome }
    ]
}
```

#### 5.7 路由的参数传递

1.配置动态路由：params

:to="/home"+userid传递

this.$route.params.abc使用数据

2.使用query传递

```javascript
this.$router.push({
    path:'/home',
    query:{
        name:'123'
    }
})
```

#### 5.8 导航守卫

来回跳转进行监听，跳转过程中做事情；

比如：跳转之前，检查是否登录，是否有token

```js
const Foo = {
  template: `...`,
  beforeRouteEnter (to, from, next) {
    // 在渲染该组件的对应路由被 confirm 前调用
    // 不！能！获取组件实例 `this`
    // 因为当守卫执行前，组件实例还没被创建
  },
  beforeRouteUpdate (to, from, next) {
    // 在当前路由改变，但是该组件被复用时调用
    // 举例来说，对于一个带有动态参数的路径 /foo/:id，在 /foo/1 和 /foo/2 之间跳转的时候，
    // 由于会渲染同样的 Foo 组件，因此组件实例会被复用。而这个钩子就会在这个情况下被调用。
    // 可以访问组件实例 `this`
  },
  beforeRouteLeave (to, from, next) {
    // 导航离开该组件的对应路由时调用
    // 可以访问组件实例 `this`
  }
}
```

#### 5.9 keep-alive

页面跳转后，再跳转之前的页面，希望保持以前的位置

页面跳转后，是每次都创建新的；created

使用keep-alive后，那就是调用actived

```js
<keep-alive>
    <router-view></router-view>
</keep-alive>
```

