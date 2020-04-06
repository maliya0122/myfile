## webpack4.0的使用

### 1.安装4.0和cli

```js
//安装本地的webpack
npm init -y  //初始化文件夹
npm install webpack webpack-cli --save-dev   //项目本地安装webpack4.0和cli

//在src目录中新建js文件
npx webpack  	//运行打包命令
```

```js
//配置信息webpack.config.js
const path = require('path')
const HtmlWebpackPlugin = require('html-webpack-plugin')

module.exports = {
    devServer:{//开发服务器的配置
        port:3000,
        progress:true,
        contentBase:'./dist',
        compress:true
    },
    mode:'development',		//模式：production  development
    entry:'./src/index.js',	//定义打包入口文件
    output:{
        path:path.resolve(__dirname,'dist'),	//绝对路径
        filename:'bundle.js',	//打包后的文件名
        publicPath:'dist/'      //图片打包的路径
    },
    plugins:[   //数组 存放所有的webpack插件
        new HtmlWebpackPlugin({
            template:'./src/index.html',
            filename:'index.html',
            minify:{
                removeAttributeQuotes:true,//去掉双引号
                collapseWhitespace:true,//折叠成一行
            }
        })
    ]
}
```

### 2.html插件

```js
npm install webpack-dev-server --save-dev   //webpack本地服务，以端口号访问
npx webpack-dev-server		//运行命令

//可以简化这个命令，就是在package.json里面进行配置
"dev":"npx webpack-dev-server"
npm run dev    //就是在执行上面那个命令
```

但是index.html想在src目录中是个模板，然后自动打包后，把js的链接塞进去；

插件：html-webpack-plugin

```js
npm install html-webpack-plugin --save-dev		//1.安装依赖包
//2.在webpack.config.js中进行配置plugins

npx webpack			//3.进行打包

npm run dev 	//4.进行预览
```

### 3.css文件

同样的跟3.6.0一样，需要解析loader；

有个插件：mini-css-extract-plugin，用来抽离css样式文件的；

```js
npm install mini-css-extract-plugin --save-dev
```

### 4.ES6转成ES5

```js
npm install babel-loader babel-core babel-preset-env --save-dev  
module: {
  rules: [
    {
      test: /\.js$/,
      exclude: /(node_modules|bower_components)/,
      use: {
        loader: 'babel-loader',
        options: {
          presets: ['@babel/preset-env']
        }
      }
    }
  ]
}
```

### 5.webpack的小插件

1.cleanWebpackPlugin     	 需要清空哪些文件夹？

2.copyWebpackPlugin			拷贝文件到哪些文件夹？

3.bannerPlugin			webpack内置的插件，在打包的文件上加上备注申明；

### 6.跨域的配置

