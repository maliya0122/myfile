## Nuxt

### 1.介绍与安装

1.nuxt是基于ssr服务器首屏渲染，首屏页面是由服务端渲染完成，其他页面由客户端渲染完成；

```js
npx create-nuxt-app nuxt_demo01
各种选项
cd nuxt_demo01
npm run dev
```

### 2.路由

####  1.普通路由:/a

####  2.嵌套路由:/a/b

#### 3.动态路由:/a/123

```js
pages/
--| a /
-----| b.vue
--| users/
-----| _id.vue
-----| index.vue
--| a.vue

{
    path: "/user",
    component: _abf5ef8a,
    name: "user"
  }, {
    path: "/user/:id",
    component: _ebc4453a,
    name: "user-id"
  }

1.子路由对应的组件：nuxt-child
比如在a页面里面，加上<nuxt-child></nuxt-child>

2.动态路由
访问：/user?id=123(这种是传参)
console.log(this.$route.query.id)   //123

访问：/user/1234
console.log(this.$route.params.id)  //1234

3.嵌套路由
/a/b
```

#### 4.跳转方式

```js
1.nuxt-link标签：
<nuxt-link to="/a">a页面</nuxt-link>
<nuxt-link to="/user/123">动态路由页面</nuxt-link>
<nuxt-link to="/a/b">嵌套路由页面</nuxt-link>
<nuxt-link to="/a?id=123">传参方式页面</nuxt-link>

2.JS方法跳转：
this.$router.push('/a')
this.$router.push({name:'user-id',params:{id:123}})     //路径是/user/123
this.$router.push('/a/b')  //路径是/a/b
this.$router.push({path:'/user',query:{id:123}})    //路径是/user?id=123
```

### 3.配置全局css文件

```js
在nuxt.config.js里面进行配置
css: [
    "@/assets/css/reset.css"
  ]
```

### 4. 引入插件

#### 4.1 引入swiper插件

```js
1.安装swiper
npm install swiper vue-awesome-swiper --save

2.在plugins下新建vue-swiper.js文件
import Vue from 'vue'
import VueAwesomeSwiper from 'vue-awesome-swiper'
Vue.use(VueAwesomeSwiper)

3.在nuxt.config.js中进行配置
css:[
  "@/assets/css/reset.css",
  "swiper/css/swiper.css"
],
plugins:[
  { src: "@/plugins/vue-swiper.js" },
]
 
4.在页面中使用举例
```

#### 4.2 引入scss

```js
npm install node-sass sass-loader --save-dev
npm run dev
```

#### 4.3 引入ele-ui

```js
npm i element-ui -S
import Vue from 'vue';
import ElementUI from 'element-ui';
Vue.use(ElementUI);
```

### 5.中间件middleware

#### 5.1 定义

相当于路由的导航守卫，在服务器端找到对应的组件之前所执行的操作；

**如何判断是服务端渲染还是客户端渲染？**

**只要页面进行了刷新，那就是服务端渲染；**

**如果是点击页面里面的链接，进行跳转，页面没有进行刷新，那就是客户端渲染；**

#### 5.2 登录校验

https://blog.csdn.net/umufeng/article/details/80524766?utm_source=blogxgwz5

使用js-cookie来进行判断权限；可以在本地设置需要存储的属性

```js
因为在服务端，不能使用window对象，不能使用对应的api：localstorage，alert等；
```

```js
npm install js-cookie --save

//在middleware里面新建requireAuth.js文件
//思路：不管是客户端渲染（直接点链接）还是服务端渲染（刷新页面）进入到info页面，进入之前都要对token进行判断，token不存在就跳转到登录页面

export default function ({route, req, res, redirect}) {
  let isClient = process.client;      //process进程，判断当前是客户端环境还是服务端环境
  let isServer = process.server;		//服务端
  let redirectURL = '/login';			//重定向的页面：login
  var token, path
  //在服务端，获取token
  if (isServer) {
    let cookies = utils.getcookiesInServer(req)
    token = cookies.token ? cookies.token : ''
  }
  //在客户端，获取token
  if (isClient) {
    token = utils.getcookiesInClient('token')
  }
  //如果进入的是info页面，没有token，就登录；
  if(route.path == '/info'){
    if (!token) {
        redirect(redirectURL)
   }
  }
}

//然后在nuxt.config.js里面进行配置
router: {
    middleware: 'requireAuth'
}
```

```js
//utils文件，封装获取cookie的方法
import Cookie from 'js-cookie'
export default {
  //获取服务端cookie
  getcookiesInServer:function (req) {
    let service_cookie = {};
    req && req.headers.cookie && req.headers.cookie.split(';').forEach(function (val) {
      let parts = val.split('=');
      service_cookie[parts[0].trim()] = (parts[1] || '').trim();
    });
    return service_cookie;
  },
  //获取客户端cookie
  getcookiesInClient:function (key) {
    return Cookie.get(key) ? Cookie.get(key) : ''
  }
}
```

### 6.页面切换效果

#### 6.1全局效果

```css
//在全局css里面写代码
.page-enter-active, .page-leave-active {
  transition: opacity .5s;
}
.page-enter, .page-leave-active {
  opacity: 0;
}
```

#### 6.2指定页面切换效果

```js
//也是在文件css里面写，效果名称：test
.test-enter-active, .test-leave-active {
    transition: opacity .5s;
}
.test-enter, .test-leave-active {
    opacity: 0;
}

在对应的组件里面加上：transition: 'test'
```

### 7.错误页面和个性meta头

1.错误页面

在layout文件夹新建error.vue，里面进行配置页面

2.meta设置

```js
//在goods页面设置head头信息
head () {
    return {
      title: "商品页",
      meta: [
        { name: '淘宝', content: '天猫双十一狂欢购物' }
      ]
    }
  }
```

### 8.asyncData方法获取数据

理解：在页面渲染数据之前，通过这个方法向对应的页面渲染数据；

```js
//安装axios插件，在plugin里面新建文件，进行配置
//在页面中进行导入axios
async asyncData ({ params }) {
    const { data } = await axios.get(`https://my-api/posts/${params.id}`)
    return { title: data.title }
  }
```

### 9.打包项目

```js
1.npm run build
2.复制项目里面的.nuxt文件夹、static文件夹、nuxt.config.js、package.json到服务器某个文件夹中；
3.安装项目依赖npm install
4.运行npm start
//修改端口
server: {
    port: 8082, // default: 3000
    host: '0.0.0.0' // default: localhost
  }
```

### 10.使用vuex

```js
//1.同样的当做插件来使用
npm install vuex --save
//2.pligin里面store.js，引入，挂载，暴露接口
import vue,vuex
Vue.use(vuex)
const store = new vuex.store({
    state
})
vue.prototype.$store = store;
//3.nuxt.config.js里面进行引入配置
plugins:[
  { src: "@/plugins/vue-swiper.js" },
]

//4.使用
{{$store.state.count}}
```

