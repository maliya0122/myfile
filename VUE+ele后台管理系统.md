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

#### 1.2 后台环境

1.安装mysql数据库

phpStudy软件，导入mysql文件；有很多个；

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

跳转到登录页面，还是用 this.$router.push('/login');

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

5.点击item，进行路由跳转

  supermall项目的路由跳转，是通过path属性跳转，push；

  这个后台项目是通过开启菜单的router属性，通过index来跳转的；

6.激活状态：有问题；以后再改；

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

#### 1.table展开，权限删除

table展开，使用的是table的属性；

删除某个角色下面对应的权限，如果调用this.getdata，页面就会自动刷新一次；栏目也关闭起来；对用户体验不好；

解决方法：

返回值data是最新的权限列表，**将data赋值给scope.row.children，那么数据就会自动刷新；而不用刷新页面；**

#### 2.角色下面对应权限的展示

采用的是树形结构，数据也是tree类型；

默认展示对应的权限：arr类型，需要递归调用

```javascript
//递归获取角色下的第三层权限
getdefaultRights(node,arr){
  //如果没有children了，说明是最后一层
  if(!node.children){
    return arr.push(node.id)
  }else{
    //有下一层就继续进行递归调用，直到没有children
    node.children.forEach( value => {
      this.getdefaultRights(value,arr)
    })
  } 
```

#### 3.商品参数页面

1.不允许页面跳转：return false

### 5.优化与编译

#### 5.1 优化

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

#### 5.2 serve命令时警告

一般是eslint检验的报错

#### 5.3 build命令

发布阶段：不能有console.log命令，使用插件：babel-plugin-tranform-remove-console，在文件中进行配置

开发阶段：有console.log命令

在文件里配置就行了；babel.config.js

```javascript
// 这是项目发布阶段需要用到的 babel 插件
const prodPlugins = []
if (process.env.NODE_ENV === 'production') {
  prodPlugins.push('transform-remove-console')
}

module.exports = {
  presets: ['@vue/app'],
  plugins: [
    [
      'component',
      {
        libraryName: 'element-ui',
        styleLibraryName: 'theme-chalk'
      }
    ],
    // 发布产品时候的插件数组
    ...prodPlugins,
    '@babel/plugin-syntax-dynamic-import'
  ]
}
```

#### 5.4 打包报告

1.命令行参数生成报告

2.UI面板生成报告：看到项目中存在的问题；依赖项体积太大；

#### 5.5 vue.config.js文件

需要修改webpack默认配置，

```javascript
module.exports = {}
```

#### 5.6 配置不同的打包入口

vue项目的开发模式和发布模式，共用一个打包的入口文件：src/main.js，

开发模式：src/main-dev.js

发布模式：src/main-prod.js

chainWebpack自定义打包入口：

​	1.修改main.js文件，修改文件名

​	2.在vue.config.js文件中修改配置

```javascript
chainWebpack: config => {
    // 发布模式
    config.when(process.env.NODE_ENV === 'production', config => {
      config.entry('app').clear().add('./src/main-prod.js')
    })
    
    // 开发模式
    config.when(process.env.NODE_ENV === 'development', config => {
      config.entry('app').clear().add('./src/main-dev.js')
    })
```

#### 5.7 通过externals加载外部CDN资源

通过import语法导入的第三方依赖包，最终会被打包合并到同一个文件中，从而导致打包成功后，单文件体积过大的问题；

解决：通过webpack的externals节点，配置加载外部的CDN资源；凡是声明在externals中的第三方依赖包，都不会被打包；

步骤：1.在vue.config.js里面，加上配置项

​			2.在index.html文件，导入css样式表

​			3.在index.html文件，导入js文件

```javascript
config.set('externals', {
        vue: 'Vue',
        'vue-router': 'VueRouter',
        axios: 'axios',
        lodash: '_',
        echarts: 'echarts',
        nprogress: 'NProgress',
        'vue-quill-editor': 'VueQuillEditor'
      })
```



课程地址：https://www.bilibili.com/video/av74592164

以后有时间了再看吧，不想做了

demo：http://119.3.183.37/#/login

