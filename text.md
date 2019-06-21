##webpack基础篇
1.初始化  npm init -y
2.webpack安装 npm install webpack webpack-cli -D
3.创建文件夹  dist  src
 -dist  index.html
 -src  index.js
4.手动配置webpack  
    webpack.config.js||webpackfile.js
5.在package.json配置脚本命令
    "scripts": {
        "test": "echo \"Error: no test specified\" && exit 1",
        "build": "webpack --config webpack.config.js",
        "dev": "webpack-dev-server",
        "start": "npm run dev"
    },
6.配置打包环境  npm install webpack webpack-dev-server -D
    在webpack.config.js的module.exports中配置
    devServer: {
        port: 3000,
        contentBase: "./dist",  //目录，没有dist文件夹时自动生成
        open: true,    //自动打开浏览器
        compress: true,  //是否开启gzip压缩
        proxy: {
            // 可配置跨域
        }
    }

7.插件处理html
    -src文件夹下  -index.js， -index.html
    npm install html-webpack-plugin -D
    let HtmlWebpackPlugin = require("html-webpack-plugin");
    plugins: [
        new HtmlWebpackPlugin({
            template: "./src/index.html",  //使用哪里的html作为模版
            filename: "index.html",   //编译后的文件名
        })
    ],

8.插件处理样式
    npm install css-loader style-loader -D
    -src 文件夹  -index1.html -index.js -style.css
    index.js下 require("style.css")
    module:{
        rules:[
            {
                test:/\.css$/,
                use:["style-loader","css-loader"]
            }
        ]
    }
9.配置less编译
    npm install less less-loader -D
    - src文件夹   index.html  index.js  style.css   b.less
    index.js下 require("b.less")
    - webpack.config.js文件里
    module:{
        rules:[
              {
                tesnt:/\.less$/,
                    use:[
                    'style-loader',
                    'css-loader',
                    'less-loader'
                    ]
              }
        ]
    }

10.抽离css
   npm install  mini-css-extract-plugin -D

   在webpack.config.js里配置
   let MiniCssExtractPlugin = require("mini-css-extract-plugin");
            {
                test: /\.css$/,
                use: [MiniCssExtractPlugin.loader, "css-loader"]
            },
   new MiniCssExtractPlugin({
        filename: "main.css"   //抽离出来的文件名
    }),

11.处理js es6转es5
    1)npm install babel-loader @babel/core @babel/preset-env
    @babel/core babel核心模块
    @babel/preset-env  标准语法转成低级语法
        {
            test: /\.js$/,
            use: 'babel-loader',
        }
    2)npm install @babel/plugin-transform-runtime @babel/runtime  生产环境下需要下载的
        作用：去除重复代码

    3)babel也可以进行独立配置，文件名字.babelrc
    配置时loader直接写成babel-loader,其他配置写在.babelrc
    {
        "presets": [
            "@babel/preset-env" //标准语法转化为低级语法
        ], //插件预设
        "plugins": [
            "plugins": [
                "@babel/plugin-transform-runtime",
                {
                    "corejs": 2
                }
            ],
            "@babel/plugin-syntax-dynamic-import",
        ]
    }

12.配置优化项
    npm install optimize-css-assets-webpack-plugin  压缩css
    npm install terser-webpack-plugin -D  压缩js
    npm install @babel/plugin-syntax-dynamic-import

    let optimizeCssAssetsWebpackPlugin = require("optimize-css-assets-webpack-plugin")
    let terserWebpackPlugin = require("terser-webpack-plugin")

    与devServer同级
    optimization: { //优化
        minimizer: [
            new optimizeCssAssetsWebpackPlugin({}),
            new terserWebpackPlugin({}),
        ]
    },

13.第三方模块的使用
    npm install jquery
    npm install expose-loader -D  负责把变量暴露给全局的loader

    -index.js中  import $ from "expose-loader?$!jquery"
    
    - webpack.config.js中
    let webpack = require("webpack")
    new webpack.ProvidePlugin({
        $: "jquery"
    })
    {
        test: require.resolve("jquery"),
        use: "expose-loader?$"
    },

14.在webpack中引入图片的几种方式
    1）在index.js中引入图片
        import logo from './logo.png';
        let img = new Image() ;
        img.src = logo
        document.body.appengChild(img)
    2)在css中引入background(url)
    3)<img src="logo.png">  需要把图片放到dist文件夹

15.图片处理
  npm install file-loader html-withimg-loader url-loader -D
  html-withimg-loader 解决打包路径不对的问题

  在webpack.config.js中module-rules配置
    {
        test:/\.(png,jpg,gif)$/,
        use:"file-loader"
    },
    {
        test:/\.html$/,
        use:'html-withimg-loader'
    },
    {
        test: /\.(jpg|png|gif|jpeg)$/,
        use:"url-loader"
    }
  
       
   
16.打包文件分类
    1）图片url-loader的options里面添加
        {
            test: /\.(jpg|png|gif|jpeg)$/,  //图片的格式
            use: {
                loader: 'url-loader',
                options: {
                    limit: 1 * 1024,   //图片的格式
                    outputPath: '/img/',    //图片打包之后的路径
                }
            }
        }
    2)css添加在css插件里面
        new MiniCssExtractPlugin({
            filename: "css/main.css"   //抽离出来的文件名
        }),
    3)js添加到filename前面

##webpack配置篇

17.webpack常用插件
    1）npm install clean-webpack-plugin -D  清除缓存插件，可以写字符串，也可以写数组
        let {CleanWebpackPlugin} = require("clean-webpack-plugin")
        new CleanWebpackPlugin(),    
    2)npm install copy-webpack-plugin -D 拷贝插件
        let CopyWebpackPlugin = require("copy-webpack-plugin")
        new CopyWebpackPlugin({
            {
                from: './src/static',
                to: './static'
            }
        }),
    3） 版权插件  webpack自带插件
18.配置环境变量
    node提供的环境变量：process.env.NODE_ENV
    new webpack.DefinePlugin({
        "development":JSON.stringify("development")
    })
19.webpack处理跨域问题（webpack自带express）
    1）代理的方式，把请求代理到express的服务器上
    devServer:{
    ...
    proxy:{ //
        '/api':{
            target:'http://localhost:3000',  访问http://localhost:3000等于访问服务器的下面api
            pathRewrite:{'/api':''}  重写路径  api/user等于访问localhost:3000/user
            }// 配置了一个代理
        }
    }
    2)使用webpack提供mock数据 webpack自带express
        webpack提供一个方法before  参数是app
        let express = require("express")
        let app = express()
        before(app) {  //在devServer
           app.get('/abc', function (req, res) {
                res.json('这是webpack提供的数据');
            })
        },
    3)可以直接在node的服务端启动webpack，端口是服务端端口，不需要npm run dev来启动webpack
        npm install webpack-dev-middleware -D

20.resolve用法  配置 Webpack 如何寻找模块所对应的文件

    在webpack.config.js中配置与devServer同级
    resolve:{
        modules:[path.resolve('node_modules')],  
        extensions:['.js','.css','.json','.vue'],  //拓展名
        mainFields:['style','main']   //可以配置先找哪个入口
        mainFiles:[], // 入口文件的名字 index.js
        alias:{ // 别名 bootstrap:'bootstrap/dist/css/bootstrap.css'
        bootstrap:'bootstrap/dist/css/bootstrap.css'
        }
    }

21.webpack配置vue的使用
    npm install vue vue-loader vue-template-compiler

    vue-loader 解析文件     vue-template-compiler 解析vue中的template
    
    1）webpack能找到以.vue结尾的文件名，配置扩展名
        在webpack.config.js中配置，与devServer同级
        // 配置vue
        resolve: {
            extensions: ['.js', '.json', '.vue'],
            alias: {
                "@": resolve('src'),
                vue$: "vue/dist/vue.esm.js"
            }
        },
    2）vue-loader需要使用VueLoaderPlugin
            {
                test: /\.vue$/,
                use: "vue-loader",
            },
        let VueLoaderPlugin = require("vue-loader/lib/plugin")
        在优化项中配置  new VueLoaderPlugin(),
        optimization: { //优化
        minimizer: [
                new OptimizeCssAssetsWebpackPlugin({}),
                new TerserWebpackPlugin({}),
                new VueLoaderPlugin(),
            ]
        },
    3）解决vue样式的问题.
        在index.html中 添加<div id="app"></div> 
        - 在index.js中引入
        import Vue from 'vue'
        import App from './App'
        new Vue({
            render: h => h(App)
        }).$mount('#app');
        在webpack.config.js中
       
18.给浏览器添加前缀 
    npm install postcss-loader autoprefixer -D
    autoprefixer 自动添加浏览器前缀的插件
    npm install postcss-preset-env -D 允许使用 css 未来特性的插件  

     需要配置 postcss 默认文件 名字
      在根目录下创建 postcss.config.js/.postcssrc.js
      放到所有 cssloader 后面，执行顺序原因  
        let autoprefixer = require('autoprefixer')
        const postcssPresetEnv = require('postcss-preset-env');
        module.exports = {
            plugins: [postcssPresetEnv, autoprefixer({})]
        };

##区分开发和生产环境
19.webpack.config.js
    新建文件webpack.prod.config.js（生产环境）  webpack.dev.config.js（开发环境）
    下载webpack 合并配置的插件 npm install webpack-merge

    创建一个build文件夹
    - build
        - webpack.config.js  - webpack.dev.config.js   - webpack.prod.config.js  - config.js
        
    1）webpack.config.js的配置
        // 开发环境 config和dev配置合并
        // 生产环境 config和prod配置合并
        let merge = require("webpack-merge");
        let config = require("./config")
        let prod = require("./webpack.prod.config")
        let dev = require("./webpack.dev.config")
        // npm run serve {development:true}
        // npm run build {production:true}
        module.exports = env => {
            // console.log(env)
            if (env.development) {
                return merge(config, dev)
            } else {
                return merge(config, prod)
            }
        }
    2）config.js的配置
        let path = require("path");
        let express = require("express")
        let app = express()

        module.exports = {
            entry: "./src/index.js",
            output: {
                path: path.resolve(__dirname, 'dist'),   //输出路径，打包后的文件夹名称
                filename: "[name].js",     //打包后的文件名称  
            },
        }
    3）webapck.dev.config.js的配置
        // 开发环境 development
        let path = require("path");
        let { smart } = require("webpack-merge")
        let base = require("./webpack.config.js")
        //reslove函数用来返回一个绝对路径
        let resolve = file => {
            return path.resolve(__dirname, file);
        };
        module.exports = smart(base, {
            mode: "development",
            // 配置vue
            resolve: {
                extensions: ['.js', '.json', '.vue'],
                alias: {
                    "@": resolve('src'),
                    vue$: "vue/dist/vue.esm.js"
                }
            },
            devServer: {
                port: 3000,
                contentBase: "./dist",  //目录，没有dist文件夹时自动生成
                open: true,    //自动打开浏览器
                compress: true,  //是否开启gzip压缩
                proxy: {
                    // 可配置跨域
                },

                before(app) {
                    app.get('/abc', function (req, res) {
                        res.json('这是webpack提供的数据');
                    })
                },
            },
        })
    4）webpack.prod.config.js
    // 生产环境 production  配置其他loader和plugins
        let path = require("path");
        let { smart } = require("webpack-merge")
        let base = require("./webpack.config.js")
        let HtmlWebpackPlugin = require("html-webpack-plugin");
        //reslove函数用来返回一个绝对路径
        let resolve = file => {
            return path.resolve(__dirname, file);
        };
        module.exports = smart(base, {
            // 配置vue
            resolve: {
                extensions: ['.js', '.json', '.vue'],
                alias: {
                    "@": resolve('src'),
                    vue$: "vue/dist/vue.esm.js"
                }
            },
            optimization: { //优化
                minimizer: [
                    new OptimizeCssAssetsWebpackPlugin({}),
                    new TerserWebpackPlugin({}),
                    new VueLoaderPlugin(),
                ]
            },
            plugins: [
                new HtmlWebpackPlugin({
                    template: "./src/index.html",  //使用哪里的html作为模版
                    filename: "index.html",   //编译后的文件名
                    hash: true,
                    minify: {  //配置优化项
                        removeAttributeQuotes: true,  //折叠空格
                        collapseWhitespace: true  // 去除双引号
                    }
                }),
            ],
            module: {
                rules: [
                    {
                        test: /\.vue$/,
                        use: "vue-loader",
                    },
            },
        })

20.多线程打包
    npm install happypack
    let happyPack = require("happypack")
    new happyPack({
        id: "js",
        use: ["babel-loader"]
    })