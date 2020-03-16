## VUE+ele后台管理系统

### 1.项目初始化

#### 1.1 前端环境

```
npm install -g @vue/cli
vue ui
```

1.安装vue脚手架（3.0版本以上；进行界面化初始化项目）

https://segmentfault.com/a/1190000015366009

2.添加插件：vue-cli-plugin-element，进行配置：import ondemond

3.配置axios库，安装依赖：axios

4.github托管项目

https://github.com/maliya0122/vue_shop.git

git commit -m 'first commit'

远程连接，推送，然后在网站上就能看到了

5.启动项目

在任务栏目，serve选项进行运行，就可以运行项目了；

```js
顺序：
1.先打开php软件，启动mysql服务
2.在后台api_serve文件夹下，运行：node .\app.js
3.在backend文件夹下，编译，运行：npm run serve
编译好：http://localhost:8080/
```

#### 1.2 后台环境

1.安装mysql数据库

phpStudy软件，导入mysql文件；有很多个；

注意：phpstudy创建的数据库，密码是root，用户名是root；

```js
"db_config" : {
    "protocol" : "mysql",
    "host" : "127.0.0.1",
    "database" : "mydb",
    "user" : "root",
    "password" : "root",
    "port" : 3306
}
```

2.vue_api_server后台环境

下载依赖包，node .\app.js，然后使用postman

```java
http://127.0.0.1:8888/api/private/v1/login
```

### 2.登录login

#### 1.创建子分支login

```javascript
git status
git checkout -b login  	//创建分支
git branch 				//查看当前项目的分支
```

#### 2.登录路由的创建

（1）创建Login.vue组件

（2）在index.js中，注册这个组件，然后填写路由配置

（3）在App.vue里面，加上占位符<router-view>查看

（4）进行路由的重定向，只要一访问/，就自动跳转到/login

```javascript
const routes = [
  { path: '/', redirect: '/login' },
  { path: '/login', component: Login }
]
在router里面配置模式，去掉#
mode: 'history'
```

登录：

在乐淘的后台管理系统中，登录校验，跳转到哪个页面的时候，会发送请求，然后根据后台返回的值，比如400，那就是未登录，那就强制跳转到登录页面；登录成功以后，再跳转到原先的页面；returnURL记录原先的地址；

#### 3.login页面的样式

用的是less语法，所以需要安装依赖less，less-loader（开发时依赖）

main.js里面：导入全局样式，导入字体文件；

要使用element-ui组件，就要在plugin.js里面进行导入



对表单加上ref属性，直接获取到表单元素，然后调用表单的函数；

点击 重置，进行表单的重置；resetFields()

点击 登录，进行表单的预校验；validate()，有回调函数；

```javascript
login() {
      //res是boolean值
      this.$refs.loginFormRef.validate(async res=>{
        if(!res) return;
        //使用解构赋值，将data取出来
        const { data:result } = await this.$http.post('login',this.loginForm)
        if(result.meta.status !== 200) return this.$message.error('登录失败');
        this.$message.success('登录成功');
        //登录成功，保存token到sessionStorage中，使用编程式导航router跳转到/home页面
        //console.log(result);
        window.sessionStorage.setItem('token',result.data.token);
        this.$router.push('/home');
      })
    }
```

async，await：es6中的新语法；返回的是promise对象；

使用message组件，进行消息提示；同样的也需要在plugin.js里面注册和调用；

#### 4.路由导航守卫控制访问权限

router.beforeEach(to,from,next){}，对路径和token进行判断：

> 如果访问的路径是/login，那么就next()放行；
>
> 如果访问的路径不是/login，那么就判断token是否存在：
>
> ​	token存在，就放行；
>
> ​	token不存在，那么就强制跳转到/login页面

```javascript
router.beforeEach(( to,from,next )=>{
  if(to.path == '/login') return next();
  
  const token = window.sessionStorage.getItem('token');
  if(!token) return next('/login');
  next();
})
```

#### 5.退出功能

清空token：window.sessionStorage.clear()

跳转到登录页面，还是用 **this.$router.push('/login');**

#### 6. eslint格式化工具

eslint有很多规范，导致我很烦；所以我就关了；

老师说，可以在文件里面加上.prettierrc文件，进行修改；

也可以在eslint里面加配置规则；rules

#### 7.提交分支代码

```javascript
git checkout -b user			 //创建并切换到user分支
git add .						 //将文件添加到仓库
git commit -m '完成用户管理模块'	//提交到本地仓库
git push -u origin user			//将user分支推送到github（首次）

git checkout master				//切换到主分支
git branch						//查看分支
git merge user					//合并本机电脑上的分支
git push 						//将master推送到github

git checkout login				//切换到login分支
```

### 3.首页home

#### 1.页面布局

1.使用element布局；

2.接口请求预处理，发送请求时，都需要带上token字段；使用axios进行预处理：（main.js）

```javascript
//导入axios包
import axios from 'axios'
//配置请求的根路径
axios.defaults.baseURL = 'http://127.0.0.1:8888/api/private/v1'
//请求预处理
axios.interceptors.request.use(config=>{
  config.headers.Authorization = window.sessionStorage.getItem('token')
  return config
})
//挂载到vue原型链上
Vue.prototype.$http = axios


//请求menulist数据
//获取菜单数据
async getMenuList(){
  const { data: result } = await this.$http.get('/menus');
  //console.log(result);
  if(result.meta.status !== 200 ) return this.$message.error(result.meta.msg)
  this.MenuList = result.data;
}
```

3.点击菜单栏，index是一样的，所以就会全部一起动。index传入的是str字符串，item.id是num，加上一个空格就把num转换成str       :index="item.id + ''"

4.展开收齐按钮，通过isCollapse属性来控制菜单item的显示/隐藏；

##### 1.1.点击item，进行路由跳转

  supermall项目的路由跳转，是通过path属性跳转，push；

  后台项目是通过开启菜单的router属性，通过index来跳转的；

```js
<el-menu router>   //开启路由，以 index 作为 path 进行路由跳转
保存每一个子菜单的path，window.sessionStorage.setItem('activepath',activepath)

:index="'/'+subitem.path"
```

#### 2.用户页面

1.具名插槽的使用：通过scope.row获取数据

```javascript
<template slot-scope="scope">
	<el-switch v-model="scope.row.mg_state"></el-switch>
</template>
```

2.put请求，修改用户状态

```javascript
const { data: result } = await this.$http.put(`users/${info.id}/state/${info.mg_state}`)
```

使用的是es6的语法；

3.弹框：添加用户

在里面写的是表单验证，简单的表单验证，直接在rules里面写就行了；

要自定义验证手机号，邮箱这些，就要按照配置；

先在data里面，配置正则；然后在rules里面，加上一句配置；

```javascript
//验证邮箱的正则规则
var checkEmail = (rule, value, callback) => {
  // 验证邮箱的正则表达式
  const regEmail = /^([a-zA-Z0-9_-])+@([a-zA-Z0-9_-])+(\.[a-zA-Z0-9_-])+/

  if (regEmail.test(value)) {
    // 合法的邮箱
    return callback()
  }

  callback(new Error('请输入合法的邮箱'))
}
{ validator: checkPhone, trigger: 'blur' }
```

4.编辑，删除用户

都是大同小异，按照添加用户的步骤写就行了；

### 4.权限管理

#### 1.角色列表：表单展开

使用的组件：table：展开行属性，下面的结构使用的混合布局；布局完成后，再加上标签tag；

![image-20200228202226125](C:\Users\chen\AppData\Roaming\Typora\typora-user-images\image-20200228202226125.png)

页面和样式完成后，就是事件，删除单个权限；

#### 2.删除单个权限

table展开，使用的是table的属性；

删除某个角色下面对应的权限，如果调用this.getdata，页面就会自动刷新一次；栏目也关闭起来；对用户体验不好；

解决方法：

返回值data是最新的权限列表，**将data赋值给scope.row.children，那么数据就会自动刷新；而不用刷新页面；**

#### 3.权限分配：树形结构

树形结构撑开，首先要展示所有的权限；其次还要展示 已存在的权限；

点击权限以后，确定，发送请求；

### 5.商品管理

#### 5.1 添加商品

步骤条组件，图片上传，vue-quilt-edit富文本插件；

#### 5.2 商品分类

商品分类下的table展开，使用的是 vue-table-with-tree插件；

![image-20200228202757099](C:\Users\chen\AppData\Roaming\Typora\typora-user-images\image-20200228202757099.png)

#### 5.3 订单管理

级联选择器的使用：

````js
<el-cascader expand-trigger="hover" :options="cateList" :props="props" v-model="cateInfo" @change="handleChange"></el-cascader>

options:数据；prop：配置选项；v-model：选择之后的数据显示；change：事件；
props:{
    value: 'cat_id',
    label: 'cat_name',
    children: 'children'
  }
````

使用axios的时候，使用``来进行url拼接，里面的变量参数使用${} 来进行拼接;

```js
url:categories/:id/attributes

`categories/${this.cateid}/attributes`
```

axios发送get请求，post请求

````js
this.$http.get('/goods', { params: this.queryinfo }).then(res=>{})
//发送get请求的时候，传递参数，使用的是params，因为参数要加在url上
this.$http.post('users', data).then(res => {})
//post,put这些参数，是放在d请求体里面，所以直接放在data里面
````

表单校验：

```js
<el-form ref="adduserFormRef" :model="adduserForm" :rules="adduserFormrules">
//rule校验
adduserFormrules: {
        username: [
          { required: true, message: '请输入用户名', trigger: 'blur' },
          { min: 3, max: 5, message: '长度在 3 到 5 个字符', trigger: 'blur' }
        ],
//确定事件，需要对表单进行校验  
adduser() {
  this.$refs.adduserFormRef.validate(valid => {
    if (!valid) {
      return
    }
}
```

#### 5.4 数据统计

echarts的使用；配置options选项；

使用lodash插件进行合并数据；

#### 5.5 商品参数

1.联动效果，不需要

解决方法：在获取res.data之后，就加上input属性；设置成null；

（以前将这个input设置成全局的时候，就一起在变化，下面的参数也被影响了）

那么在使用v-for循环之后，每一项数据就有了自己单独的input值；

```js
//把attr_vals切割成数组
result.data.forEach(item => {
  item.attr_vals = item.attr_vals ? item.attr_vals.split(' ') : []
  // 控制文本框的显示与隐藏
  item.inputVisible = false
  // 文本框中输入的值
  item.inputValue = ''
})
```

使用的时候，template：slot-scope进行使用；每一行的数据都是 scope.row进行获取数据；



2.标签页切换这个模块，很难

做的有点晕；难点：要给每行的数据都加上属性值；不然，修改第一行的数据，第二行的数据也跟着修改了；

获取参数，row进行获取；



------

### 6.优化与上线

优化策略：

1.生成打包报告

2.第三方库启用CDN

3.Ele UI组件按需加载

4.路由懒加载

5.首页内容定制

#### 6.1 进度条展示

加上nprogress插件，进行进度条的展示

配置：main.js里面配置

```javascript
// 导入 NProgress 包对应的JS和CSS
import NProgress from 'nprogress'
import 'nprogress/nprogress.css'

axios.interceptors.request.use(config=>{
  NProgress.start()
  config.headers.Authorization = window.sessionStorage.getItem('token')
  return config
})

// 在 response 拦截器中，隐藏进度条 NProgress.done()
axios.interceptors.response.use(config => {
  NProgress.done()
  return config
})
```

#### 6.2 解决eslint报错

1.i3定义了没有使用；scopes也是；

2.elint和代码格式化工具prettierrc冲突：格式化就会自动换行；

```js
"printWidth":200   //默认是80就换行了
```

3.不能使用attr_id命名，要使用驼峰命名attId

#### 6.3 build编译

1.在build阶段把所有的console.log移除掉

babel-plugin-transform-remove-console插件，开发时依赖

```js
在babel.config.js中，进行配置
在"plugins" 里面，加上   'transform-remove-console'
```

2.开发阶段，console.log还是可以使用

根据上面的配置，会全部移除的，开发环境的也打印不出来了；

```js
//项目发布阶段要用到的babel插件
const prodPlugins = []
if(process.env.NODE_ENV === 'production'){
  prodPlugins.push('transform-remove-console')
}

在"plugins" 里面，加上   ...prodPlugins
```

#### 6.4优化build下的文件体积

1.自定义webpack的默认配置，创建vue.config.js

2.为开发模式与发布模式指定不同的打包入口

在vue.config.js里面进行配置；开发模式：src/main-dev.js    发布模式：src/main-prod.js

3.通过externals加载外部的CDN资源

默认情况下，会把所有的依赖包全部打包在一个chrunk.js文件中，导致体积很大；所以使用外部CDN资源，当配置了的时候，window会自动的去全局查找和使用；

步骤：先在vue.config.js中的prod环境下配置；然后在index.html中 使用那些CDN链接；

4.优化ele-ui的打包

步骤：1.先在index.html中，使用ele的css和js链接；

​			2.在main-prod.js中注释ele.js的引入；

```js
//vue.config.js文件的配置
module.exports = {
  chainWebpack: config => {
    //发布模式
    config.when(process.env.NODE_ENV === 'production', config => {
      config
        .entry('app')
        .clear()
        .add('./src/main-prod.js')

      //加载外部资源
      config.set('externals', {
        vue: 'Vue',
        'vue-router': 'VueRouter',
        axios: 'axios',
        lodash: '_',
        echarts: 'echarts',
        nprogress: 'NProgress',
        'vue-quill-editor': 'VueQuillEditor'
      })

    })

    //开发模式
    config.when(process.env.NODE_ENV === 'development', config => {
      config
        .entry('app')
        .clear()
        .add('./src/main-dev.js')
    })
  }
}
```

#### 6.5 首页内容定制

dev环境，不显示CDN的标签，标题有dev；prod环境，显示CDN的标签，标题没有dev

主要是在index.html里面，进行配置；显示与否；

其次，要在vue.config.js文件中，进行配置；

```js
config.plugin('html').tap(args => {
    args[0].isProd = true
    return args
  })
```

#### 6.6 路由懒加载

步骤：

1.安装babel/plugin-syntax-dynamic-import包；

2.在babel.config.js中配置文件声明该插件；

3.将路由改为按需加载；

这是把三个js文件打包到一个文件下

```js
//import Login from '../components/Login.vue'
const Login = () => import(/* webpackChunkName: "Login_Home_Welcome" */ '../components/Login.vue')

//import Home from '../components/Home.vue'
const Home = () => import(/* webpackChunkName: "Login_Home_Welcome" */ '../components/Home.vue')

//import Welcome from '../components/Welcome.vue'
const Welcome = () => import(/* webpackChunkName: "Login_Home_Welcome" */ '../components/Welcome.vue')
```

#### 6.7 上线配置

1.通过node创建web服务器

把dist文件夹复制一份到文件夹下，然后创建app.js，进行配置；

```js
const express = require('express')
const compression = require('compression')
const app = express()

app.use(compression())
//托管静态资源
app.use(express.static('./dist'))

app.listen(5001,()=>{
    console.log('server running ....')
})
```

2.开启文件的Gzip网络传输压缩：compression

需要下载compression插件，然后要配置；

3.配置https服务

后台配置的；了解既可；