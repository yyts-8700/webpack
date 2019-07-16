# webpack-study


## webpack 安装

#####初始化一个项目
    -npm init
#####安装本地的webpack
    （局部安装，并且可以根据项目的不同安装不同版本的webpack）
    （通过安装npm install webpack@3.6.0 -D等等旧版本--项目构建初期，当package.json已经指定，就直接npm install）

    -npm install webpack webpack-cli -D
#####额外：
- 输入 touch .gitignore ，生成“.gitignore”文件
- 添加：node_modules/   表示过滤这个文件夹
- .zip   过滤zip后缀文件
- demo.html   过滤该文件
## webpack可以进行0配置
#####定义：
    打包工具->输出后结果（js模块）
#####打包
    （支持我们js的模块化）
#####变化
    相比较以前版本webpack，把一些配置项变成默认配置项，如：
      webpack 4.x提供了约定大于配置的概念:目的是为了尽量减少 配置文件的提及

        默认约定了：
          1.打包入口是 src -> index.js
          2.打包的输出文件默认是 dist -> main.js
          3. 4.x中新增了mode选项，可选的值为: production development
## 手动配置webpack
#####默认配置文件的名字: webpack.config.js
    package.json 配置
    {
      "name": "webpack-study",
      "version": "1.0.0",
      "description": "",
      "main": "index.js",
      "scripts": {
        "test": "echo \"Error: no test specified\" && exit 1"，
    "build":"webpack --config webpack.config.js" //可配置npm run build命令
      },
      "author": "",
      "license": "ISC",
      "devDependencies": {
        "webpack": "^4.32.2",
        "webpack-cli": "^3.3.2"
      }
    }


- 重点:webpack打包大致流程
- 主要功能是：把解析的所有模块变成一个个对象，然后通过唯一入口，去加载这些对象，依次实现递归的依赖关系，然后通过入口运行所有的模块（文件）
- 重点:Html插件
## webpack-dev-server
#####需要安装:webpack-dev-server	html-webpack-plugin

    不会真实的打包文件，只是在内存中打包，把文件写入内存中，生成localhost:3000端口
      -npm install webpack-dev-server -D

    运行：npx webpack-dev-server

#####在package.json下配置:

    "scripts":{
      "dev":"webpack-dev-server"
    }

#####在webpack.config.js下配置

      let HtmlWebpackPlugin = require('html-webpack-plugin');
      module.exports = {
          devServer:{
          port:8000,  //启动端口号修改
          progress:true, //内存中打包，希望有进度条
          contentBase:'./build',//以这个文件夹为启动后静态目录,即使没有打包页面默认内存中生成这个目录（需要 html-webpack-plugin -D）
          open:true,  //打开浏览器
          compress:true //采用gzip压缩,当它被设置为true的时候对所有的服务器资源采用gzip压缩 
        }，

#####针对contentBase之后配置
      plugins:[
      new HtmlWebpackPlugin({
      template:'./src/index.html', //指定模板路径
            filename:'index.html', //修改打包后的html文件名称,如果不是index.html，则启动服务后就要自己选择,默认和template指定模板名称一样
            minify:{ //压缩html打包后代码
                      removeAttributeQuotes:true, //删除html内的双引号
                      collapseWhitespace:true, //折叠空行，所有代码压缩成一整行
                  },
            hash:true //添加hash，每次文件改动，html引入的文件路径都加入了hash
      })
      ]

#####针对hash:true，ouput打包后文件也加上hash//可以防止覆盖和缓存问题

      output:{ //出口
      filename:'index.[hash:8].js',  //打包后文件名,加hash，每次打包产生不同的xxx[hash].js文件
      path:path.resolve(__dirname,'build'),  //路径有要求，必须是一个绝对路径,引入node的path模块，将相对路径解析成绝对路径
          },
      }

#####新增：
      devServer的一个项目中使用的实际例子：

        devServer: {
            contentBase:'./build',//以这个文件夹为启动后静态目录
            historyApiFallback: true,//不跳转
            host:'0.0.0.0',
            port:7000,
            hot:true,
            inline: true,//实时刷新
            hot:true,//Enable webpack's Hot Module Replacement feature
            compress:true,//Enable gzip compression for everything served
            overlay: true, //Shows a full-screen overlay in the browser
            stats: "errors-only" ,//To show only errors in your bundle
            open:true, //When open is enabled, the dev server will open the browser.
            proxy: {
                "/api": {
                    target: "http://localhost:3000",
                    pathRewrite: {"^/api" : ""}
                }
            }，//重定向

#####接下来我们根据上面的实际例子逐条解析：

    1、contentBase
    Tell the server where to serve content from. This is only necessary if you want to serve static files. devServer.publicPath will be used to determine where the bundles should be served from, and takes precedence.

    用于告诉服务器文件的根目录。这主要用来需要引用静态文件的时候。devServer.publicPath被用来规定变异文件的路径地址，在下面将详细介绍

    By default it will use your current working directory to serve content, but you can modify this to another directory:

    默认情况下就是当前工作的文件夹地址，但是可以修改为其他的地址

    contentBase: path.join(__dirname, "public")

    2、historyApiFallback
    When using the HTML5 History API, the index.html page will likely have to be served in place of any 404 responses. Enable this by passing:

    这个配置属性是用来应对返回404页面时定向到特定页面用的

    By passing an object this behavior can be controlled further using options like rewrites:

    语法是向historyApiFallback对象中的rewrites属性传一个对象格式，如下：

    historyApiFallback:{
      rewrites:[
          {from:/./,to:'/404.html'}
      ]
      }

    3、host
    Specify a host to use. By default this is localhost. If you want your server to be accessible externally, specify it like this:

    设置服务器的主机号，默认是localhost，但是可以自己进行设置，如：

    host: "0.0.0.0"

    此时，localhost:7000和0.0.0.0:7000都能访问成功 


    4、port
    Specify a port number to listen for requests on:

    设置端口号，如下面的7000

    devServer: {
      port:7000
    }

    5、hot 和 inline
    自动刷新和模块热替换机制

    5.1 hot
    Enable webpack’s Hot Module Replacement feature:

    热模块替换机制

    hot: true

    Note that webpack.HotModuleReplacementPlugin is required to fully enable HMR. If webpack or webpack-dev-server are launched with the –hot option, this plugin will be added automatically, so you may not need to add this to your webpack.config.js.

    注意，如果你的项目中使用了热模块替换机制，HotModuleReplacementPlugin插件会自动添加到项目中，而不需要再在配置文件中做添加。

    5.2 inline
    Toggle between the dev-server’s two different modes. By default the application will be served with inline mode enabled. This means that a script will be inserted in your bundle to take care of live reloading, and build messages will appear in the browser console.

    webpack-dev-server有两种模式可以实现自动刷新和模块热替换机制 
    1. Iframe mode(默认,无需配置) 
    页面被嵌入在一个iframe里面，并且在模块变化的时候重载页面 
    2.inline mode（需配置）添加到bundle.js中 
    当刷新页面的时候，一个小型的客户端被添加到webpack.config.js的入口文件中

    6、compress
    这是一个布尔型的值，当它被设置为true的时候对所有的服务器资源采用gzip压缩 
    采用gzip压缩的优点和缺点：

    优点：对JS，CSS资源的压缩率很高，可以极大得提高文件传输的速率，从而提升web性能
    缺点：服务端要对文件进行压缩，而客户端要进行解压，增加了两边的负载

    7、overlay
    Shows a full-screen overlay in the browser when there are compiler errors or warnings. Disabled by default. If you want to show only compiler errors:

    用于在浏览器输出编译错误的，默认是关闭的，需要手动打开：

    overlay: true

    如果你想江warnings一同打印出来，可设置：

    overlay: {
      warnings: true,
      errors: true
    }

    8、stats
    This option lets you precisely control what bundle information gets displayed. This can be a nice middle ground if you want some bundle information, but not all of it.

    这个配置属性用来控制编译的时候shell上的输出内容，因为我们并不需要所有的内容，而只是需要部分的如errors等

    To show only errors in your bundle:

    在shell中只输出errors：

    stats: "errors-only"

    9、open
    When open is enabled, the dev server will open the browser.

    当open选项被设置为true时，dev server将直接打开浏览器

    10、proxy
    Proxying some URLs can be useful when you have a separate API backend development server and you want to send API requests on the same domain.

    重定向是解决跨域的好办法，当后端的接口拥有独立的API，而前端想在同一个domain下访问接口的时候，可以通过设置proxy实现。

    如果后端接口地址是10.10.10.10:3000,你可以这样设置：

    proxy: {
      "/api": "http://10.10.10.10:3000"
    }

    一个 “/api/users”地址的请求将被重定向到”http://10.10.10.10:3000/api/users“,如果不希望”api”在传递中被传递过去，可以使用rewrite的方式实现：

    proxy: {
      "/api": {
        target: "http://localhost:3000",
        pathRewrite: {"^/api" : ""}
      }
    }

    11、publicPath
    The bundled files will be available in the browser under this path. 
    Imagine that the server is running under http://localhost:8080 and output.filename is set to bundle.js. By default the publicPath is “/”, so your bundle is available as http://localhost:8080/bundle.js.

    用于设置编译后文件的路径，假设服务器的运行地址是 http://localhost:8080，输出文件名设置为bundle.js，那么默认情况下publicPath是”/”，因此文件地址为http://localhost:8080/bundle.js 如果想要设置为别的路径可以这样：

    publicPath: "/assets/"

    设置后文件地址为：http://localhost:8080/assets/bundle.js 

- 重点:webpack中解析css模块
## 引入css，通过css-loader
#####处理css需要安装:css-loader  style-loader
    处理less sass需要安装:下面备注

#####在webpack.config.js下配置：

    module.exports = {
    module:{ //模块
            rules:[ //规则 
                    //css-loader主要负责解析@import这种语法的，把css压缩成一个css ()
                    //style-loader 他是把css插入到html页面head的标签中
                    //loader的特点 希望单一
                    //loader的用法 一个loader字符串，多个loader需要数组[ ]
                    //loader的顺序 默认从右向左执行 从下到上执行
                {test:/\.css$/,use:[
                  {
                    loader:'style-loader, //这种写法可以加
                    options:{
                      insertAt:'top' //希望style标签在html内插入到head顶部
                    }
                   },
                  'css-loader'//@import 解析路径
                ]
                },
                //处理less文件 
                //less([cnpm install] less less-loader)
                //sass ([cnpm install] node-sass sass-loader)
                //stylus ([cnpm install] stylus stylus-loader)
                {test:/\.less$/,use:[
                        {
    loader:'style-loader,
    //这种写法可以加 options:{
    insertAt:'top' //希望style标签在html内插入到head顶部
    }
            },'css-loader',//@import 解析路径
    'less-loader' //把less -> css
                    ]
                }
            ]
        },
    }

- __重点:以link标签形式,抽离css样式__
## 抽离css样式
#####需要安装:mini-css-extract-plugin -D

#####在webpack.config.js下配置

      let MiniCssExtractPlugin = require('mini-css-extract-plugin');

      module.exports = {
      plugins:[
        new MiniCssExtractPlugin({
          filename:'main.css'
        })
      ],
      module:{
      rules:[
      {test:/\.css$/,use:[
                          MinCssExtractPlugin.loader,//使用MiniCssExtractPlugin插件的loader
                          'css-loader',
                          'postcss-loader', //css 自动添加前缀 ([cnpm install] postcss-loader autoprefixer,在外部添加postcss.config.js配置)
                        ]
                    },
      {test:/\.less$/,use:[
                          MinCssExtractPlugin.loader,
                          'css-loader',
                          'postcss-loader',
                          'less-loader' //less转换成css
                ]
          }
      ]
      }
      }
#####可以针对css抽离一份，以及less或者sass等抽离一份，配置：
      let MiniCssExtractPlugin = require('mini-css-extract-plugin');
      let MiniLessExtractPlugin = require('mini-css-extract-plugin');
      module.exports = {
      plugins:[
        new MiniCssExtractPlugin({
        filename:'main.css'
        }),
        new MiniLessExtractPlugin({
        filename:'less.css'
        })
      ],
        module:{
          rules:[
          {test:/\.css$/,use:[
                              MinCssExtractPlugin.loader,//使用MiniCssExtractPlugin插件的loader
                              'css-loader',
                              'postcss-loader', //css 自动添加前缀 ([cnpm install] postcss-loader autoprefixer,在外部添加postcss.config.js配置)
                            ]
                        },
          {test:/\.less$/,use:[
                              MinLessExtractPlugin.loader,
                              'css-loader',
                              'postcss-loader',
                              'less-loader' //less转换成css
                    ]
              }
          ]
        }
      }

## 自动添加前缀
#####需要安装:postcss-loader autoprefixer -D

#####需要新建文件：在webpack.config.js同级目录下新建postcss.config.js文件

#####postcss.config.js文件内容为：
    module.exports = {
        plugins:[require('autoprefixer')] //导出插件autoprefixer
    }

#####在webpack.config.js下配置：
    module.exports = {
      plugins:[
        new MiniCssExtractPlugin({
        filename:'main.css'
        })
      ],
      module:{
        rules:[
        {test:/\.css$/,use:[
                            MinCssExtractPlugin.loader,//使用MiniCssExtractPlugin插件的loader
                            'css-loader',
                            'postcss-loader', //css 自动添加前缀 ([cnpm install] postcss-loader autoprefixer,在外部添加postcss.config.js配置)要在解析css之前添加
                          ]
                      },
        {test:/\.less$/,use:[
                            MinCssExtractPlugin.loader,
                            'css-loader',
                            'postcss-loader',//css 自动添加前缀 ([cnpm install] postcss-loader autoprefixer,在外部添加postcss.config.js配置)要在解析css之前添加
                            'less-loader' //less转换成css
                  ]
            }
        ]
      }
    }

## 把模式改成'production'
#####css打包压缩,需要进入npm官网查找mini-css-extract-plugin，看看文档

#####需要安装:optimize-css-assets-webpack-plugin	terser-webpack-plugin

    let OptimizeCSSAssetsPlugin = require('optimize-css-assets-webpack-plugin'); //打包后的css文件
    let TerserJSPlugin = require('terser-webpack-plugin');   

                  
    module.exports = {
      optimization: {  //css打包代码压缩优化项 
        minimizer: [new TerserJSPlugin({}), new OptimizeCSSAssetsPlugin({})],//用terser-webpack-plugin替换掉uglifyjs-webpack-plugin解决uglifyjs不支持es6语法问题
      }
    ｝

- 重点:babel
## es6转换
#####需要安装:babel-loader @babel/core @babel/preset-env

#####(进行转换加载器) (babel核心模块,调用transform，转换源代码) (告诉如何转换,把es6转换成低级语法)

    module.exports = {
      module:{
        rules:[
          {
          test:/\.js$/,
            use:{
              loader:'babel-loader',
                    options:{//用babel-loader 把es6转换成es5
                      presets:[ //presets是预设意思，(大插件集合)这里添加大的插件库,把es6转化成es5
                                        '@babel/preset-env'
                          ]
                  }
            }
          }
        ]
      }
    }
## 针对更高级语法
    如：
    // class A{  //es7 等于 new A() a = 1
    //     a = 2;
    // }
#####需要安装:不用记住，自己打包或运行会提示需要的插件(@babel/plugin-proposal-class-properties -D)

    module.exports = {
      module:{
        rules:[
          {
          test:/\.js$/,
            use:{
              loader:'babel-loader',
                    options:{   //用babel-loader 把es6转换成es5
                    presets:[   //presets是预设意思，(大插件集合)这里添加大的插件库,把es6转化成es5
                                '@babel/preset-env'
                            ],
                    plugins:[
                              // 更高级的语法 需要配置 到官网babeljs.io查看,如查decorators
                              ["@babel/plugin-proposal-decorators", { "legacy": true }],//如用语法@log装饰器,下载这个插件
                              ["@babel/plugin-proposal-class-properties", { "loose" : true }]
                          ]
                  }
            }
          }
        ]
      }
    }


- __重点:es7实例上的方法补丁，以及ESlint__
## es7转换
#####需要安装:@babel/plugin-transform-runtime -D  @babel/runtime -S（--save）

    module.exports = {
      module:{
        rules:[
          {
          test:/\.js$/,
          use:{
            loader:'babel-loader',
                  options:{//用babel-loader 把es6转换成es5
                    presets:[ //presets是预设意思，(大插件集合)这里添加大的插件库,把es6转化成es5
                                      '@babel/preset-env'
                        ],
            plugins:[   // 更高级的语法 需要配置 到官网babeljs.io查看,如查decorators
                        ["@babel/plugin-proposal-decorators", { "legacy": true }],//如用语法@log装饰器,下载这插件
                        ["@babel/plugin-proposal-class-properties", { "loose" : true }]，
                        "@babel/plugin-transform-runtime"  //@babel/plugin-transform-runtime -D @babel/runtime -S
                    ]
                }
          }，
          include:path.resolve(__dirname,'src'), //包括
          exclude:/node_modules/ //排除 node_modules
          }
        ]
      }
    }

## 更高级的语法
#####更高级的语法
    需要安装:@babel/polyfill -S，如:
    'aaa'.includes('a');
#####在需要用到的js下配置:<font color=red>注意在用到不识别的语法的js文件里引入，不要在webpack总配置</font>
    require('@babel/polyfill');

    'aaa'.includes('a');

## ESlint (官网eslint.org)
#####需要安装:eslint eslint-loader -D

    module.exports = {
      rules:[
        {
        test:/\.js$/,
          use:{
          loader:'eslint-loader',
            options:{
              enforce:'pre' //enforce(执行) 'pre' 在js解析前执行 'post'在js之后执行 （npmjs.com官网查eslint可以看文档配置）
            }
          }，
        }
      ]
    }

- __外部配置：官网下载 .eslintrc.json文件，放到webpack.config.js同目录下__

- __重点:通过第三方模块的使用__
## 如安装jquery
    需要安装:jquery -S

#####使用方法一：js文件中直接引入

    import $ from 'jquery';
    console.log($); 

#####使用方法二(有点变态)：暴露全局(给window对象)的 expose-loader  目前loader使用方式共有:
    1.pre 前面执行loader
    2.normal 普通loader
    3.内联的loader
    4.后置 postloader） 	
    使用方式：内联的loader   
    改：
    import $ from 'expose-loader?$!jquery';
    console.log(window.$); 

#####使用方法三：直接在webpack.config.js下配置
    module.exports = {
      module:{
        rules:[
          {
          test:require.resolve('juqery'),
          use:'expose-loader?$'
          }
        ]
      }
    }
#####然后在js下配置
    import $ from 'jquery';
    console.log(window.$); 

#####使用方法四：在每个模块中注入$对象（需要webpack插件）
    let webpack = reuqire('webpack');
    module.exports = {
      plugins:[
        new webpack.ProvidePlugin:{ //在每个模块中注入$对象
          $:'jquery'
        }
      ]
    }
#####然后在js下配置
    console.log($); 
---
#####补充：
    module.exports = {
      externals:{
        jquery:'jQuery'
      }
    }

- 重点:webpack打包我们的图片
## 图片打包方式

#####打包方式：
    1）在js中创建图片引入
    2）在css引入 background:url('')
    3）在html中引入,<img src="" alt=""/>

#####1）需要安装:file-loader          //默认会在内部生成一张图片 到build目录下，把生成图片的名字返回
    import logo from './logo.png' //把图片引入，返回的结果是一个新的图片地址，内部会把logo.png发射处去(loader的帮助导致)
    module.exports = {
      module:{
        relus:[
          {
          test:/\.(png|jpg|gif)$/,
          use:'file-loader'
          }
        ]
      }
    }
#####然后在js下配置


    import logo from './logo.png';//let logo = require('./logo.png');
    let image = new Image();
    image.src = logo;

#####2）需要安装:css-loader  //可能之前装了,css-loader会把background:url("./logo.png")转换成background:url(require("./logo.png"))

#####在css文件下
    body{
      background:url("./logo.png");
    }

#####3）需要安装:html-withimg-loader -D
    module.exports = {
      module:{
        rules:[
          {
          test:/\.html$/,
          use:'html-withimg-loader'
          }
        ]
      }
    }
#####4）补充：针对图片小，就不去发送http请求图片，而是以base64（base64比源文件会大1/3）方式加载，一般不直接用file-loader，而是用url-loader -D

    优点：做一个限制，当我们的图片小于多少k的时候，就用base64来转化,否则用file-loader产生真实的图片（需要请求那种）

    module.exports = {
      module:{
          rules:[
          {
          test:/\.(png|jpg|gif)$/,
            use:{
            loader:'url-loader',
              options:{
              limit:200*1024, //不大于200k的图片以base64方式产出
              }
            }
          }
        ]
      }
    }

- 重点:静态资源分类
## 公共路径

    module.exports = {
      output:{
      filename:'index.[hash:8].js',
                    path:path.resolve(__dirname,'build'),
      publicPath:'http://www.baidu.com/' //会在所有资源引入地址前，加入这个地址
      }
    }

## img分配目录
#####直接在webpack.config.js下配置（会把所有图片地址自动修改成该地址）

    module.exports = {
      module:{
        rules:[
          {
          test:/\.(png|jpg|gif)$/,
            use:{
            loader:'url-loader',
              options:{
              limit:200*1024, 
              outputPath:'img/'  //把图片打包到指定目录下
              //publicPath:'http://www.baidu.com/' //可以单独给图片引入地址加我们的服务器域名地址
              }
            }
          }
        ]
      }
    }
## css分配目录
#####(详情看 6.样式处理2)

    module.exports = {
      plugins:[
        new MinCssExtractPlugin ({
              filename:'css/main.css' //打包到css文件夹下
        }),
      ]
    }
##打包多页应用
#####打包多入口文件
    // 多入口
    entry:{
        index: "./src/index.js",
        miemiezi: "./src/miemiezi.js",
    },
    output:{
        // [name]表示index和meimeizi,打包后生成两个文件
        // filename: "[name].[hash:5].js",也可以加hash值
        filename: "[name].js",
        path: path.join(__dirname,"/dist")
    },
    plugins:[
        // 想生成多页面，不能用[name]形式来命名，需要调用多次插件
        new htmlWebpackPlutin({
            template:"./index.html",
            filename: "index.html",
            // 不设置chunks的时候，html会把所有js引入，配置了chunks可以定制引入的js
            chunks:["index"]
        }) ,
        new htmlWebpackPlutin({
            template:"./index.html",
            filename: "miemiezi.html",
            chunks:["miemiezi"]

        })
    ]
##配置source-map
    在入口同级添加devtool项，设置源码映射，打包后生成一个源码文件，生产环境（看不到源码结构，缩成一行了）下方便调试
    // 1)会生成源码文件，并指出错误行和列
    devtool:'source-map',
    // 2)不会生产源码文件，只会指出错误行和列
    devtool: "eval-source-map",
    // 3) 会产生源码文件，不会指出错误列
    devtool: "cheap-module-source-map",
    // 4) 不会产生源码文件，不会指出错误列
    devtool: "cheap-module-eval-source-map",
##watch用法（实时打包）
#####实时打包
    // 设置了watch之后可以实时build文件
    watch:true, //是否开启实时打包
    watchOptions:{ // 监控打包的选项
        poll: 1000, // 每秒监听多少次
        aggregateTimeout: 500, // 当第一个文件更改了，在重新构建之前增加延迟，俗称防抖,单位毫秒
        ignored: /node_modules/ // 不需要监听的文件
    },
#####几个实用插件
    1) cleanWebpackPlugin
    作用：在build之前清空dist文件夹，保证每次都只有最新状态的打包文件存在
    使用：
        //必须用{}结构引出，否则报错不是构造器
        const {CleanWebpackPlugin} = require('clean-webpack-plugin')
        //plugins里添加如下
        new CleanWebpackPlugin()
    2) copyWebpackPlugin
    作用：build时，复制内容到打包后文件夹中
    使用：
        const CopyPlugin = require('copy-webpack-plugin')
        //plugins里添加如下，数组中可以添加多个复制关系，to的位置和output配置有关
        new CopyPlugin([
            {from:'./doc',to:"./"}
        ]),
    3) bannerPlugin 内置
    作用：build后在js文件开头添加信息
    使用：
        const {BannerPlugin} = require("webpack")
        /plugins里添加如下
        new BannerPlugin("contribute by yhy 2019/7/12:15:14")
## webpack跨域
##### 搭建临时服务器
    let express = require("express");
    let app = express();
    app.get("/api/user",(req,res)=>{
        res.json({name:"yhy"})
    })
    app.listen(3000)
    // 通过node来执行这个js文件，启动服务器
    // 访问这个接口会404，因为端口不对，一个3000一个8080，所谓跨域。
##### 在入口配置同级配置devServer项、
    1)常规跨域处理，和后台对接口时的跨域
    devServer:{
        proxy:{
            "/api": {  //前端部分以/api开头的接口都会使用代理
                target: "http://localhost:3000",
                pathRewrite:{ // 重写路径，因为后端接口是没有我们自己设置的'/api'的，要把地址中的'/api'消除才能正确访问后端接口
                    "/api": ''
                }
            }
        }
    },
    2)前端自己模拟数据的跨域（全都在前端处理）
    原理：通过启动开发服务器的before钩子函数来编写接口内容，接口就在开发服务器上，不存在跨域问题
    devServer:{
        before(app){ // 启动服务器之前执行以下代码
            app.get("/user",(req,res)=>{  // express里写接口部分代码
                res.json({name:"yhy"})
            })
        }
    },
    3)在服务器端启动前端项目（webpack），也不需要跨域处理（全都在服务端处理）
    先安装中间件插件cnpm i webpack-dev-middleware -D
    启动服务器的server.js代码如下：
        let express = require("express");
        // 引入webpack
        let webpack = require("webpack");
        let app = express();
        // 引入中间件
        let middle = require("webpack-dev-middleware");
        // 引入webpack.config.js文件
        let config = require("./webpack.config.js");
        // 将配置文件作为参数传入webpack函数，返回一个编译对象
        let compiler = webpack(config);
        // app对象使用中间件对象，并把webpack返回的对象传入中间件函数
        app.use(middle(compiler));4
        app.get("/user",(req,res)=>{
            res.json({name:"yhy"})
        })
        app.listen(3000)
    完成以上操作，就可以在启动后台服务器的同时也启动前端服务器，因为在同一个端口下启动，不会发生跨域问题
## resolve配置
    resolve:{// 解析配置的意思
        modules: [path.resolve("node_modules")],//表示引入第三方包时，只从这个文件里找
        alias: { // 别名,仅限于路径引入
            bootstrap: 'bootstrap/dist/css/bootstrap.css' //前面是后面的别名
        },
        mainFields:["style","main"], //有一些第三方模块会针对不同环境提供几分代码，该配置决定先采用哪份代码
        extensions:[".js",".css"] // 省略后缀，有顺序问题，优先找写在前面的，找不到才会寻找下一个
    },
##开发环境和上线环境
#####定义环境变量
    用到DefinePlugin插件，是webpack内置的插件，只需引入webpack
    new webpack.DefinePlugin({
        DEV: JSON.stringify("dev"), 
        hello: "true"  //写在双引号里的内容会被当做变量，如果要得到true字符串需要套上两个引号，或者使用JSON.stringify方法
    })
#####定义环境配置文件
    1)更改配置文件命名
    webpack.base.js 基础配置文件
    webpack.dev.js 开发配置文件
    webpack.prod.js 生产配置文件

    2)安装 cnpm i webpack-merge -D
    
    3)在webpack.dev.js中引入webpack-merge
    let {smart} = require("webpack-merge") // 引入webpack-merge的smart方法，将两个配置文件合并
    let base = require("./webpack.base.js") //引入基础配置文件
    module.exports = smart(base,{  // 在下面写开发环境独有的配置项
        mode: 'development'
    })
    注意：npm run build -- --config webpack.dev.js， npm run传参要加--，写在scripts里就不用加前面的--了
##优化
#####noParse（不解析）
    module:{
        noParse: /jquery/,//不去解析jquery中的依赖关系，加快编译速度
        rules:[],
    },
#####排除和包含（设置一个即可）
    module:{
        rules:[
            {
                test:/\.js$/,
                exclude: /node_modules/, //不查找node_modules里面的文件
                include: path.resolve("src"), // 只查找/src里面的文件，二者设置一个即可
                use:{
                    loader: "babel-loader",
                    options:{
                        presets:["@babel/preset-env","@babel/preset-react"]
                    }
                }
            }
        ]
    },
##### ignorePlugin(webpack内置插件)
    首先cnpm i moment -S ，一个时间转换的包，其中语言包很大，使用ignorePlugin配置来不打包语言包，然后按需引入中文包
###### index.js代码
    import moment from "moment"
    // 手动引入语言包，配置了ignorePlugin后需要引入，才能转换语言
    import "moment/locale/zh-cn"
    // 调用语言包，在配置ignorePlugin之前生效
    moment.locale("zh-cn");  
    // 测试moment用法
    let a = moment().endOf("day").fromNow();
    console.log(a);
###### plugins配置
    plugins:[
        new webpack.IgnorePlugin(/\.\/locale/,/moment/),//第一个参数表示忽略的内容，第二个参数表示引用的哪个插件
        new Hwp({
            template:"./src/index.html",
            filename: "index.html"
        })
    ]
#####动态链接库（dynamic link library）（dll）
###### library和libraryTarget配置
    当被打包的js文件暴露出了内容时，打包后的文件会返回module.exports,但是没有变量来接收，访问不到，设置了library和libraryTarget后才会有变量接收
    output: {
        filename: "[name].js",
        path: path.join(__dirname,"/dist"),
        library: "aaa", // 打包后接收变量的名字
        libraryTarget: "var" // 使用的打包格式，默认是使用var创建变量接收
    }
###### 打包react和react-dom为dll文件并生成任务清单
    1)--webpack.config.react.js配置
    module.exports = {
        mode: "development",
        entry: {
            react: ["react","react-dom"] //多个文件入口可写成数组
        },
        output: { // 导出dll文件
            filename: "_dll_[name].js",  // 输出js名最好加_dll_
            path: path.join(__dirname,"/dist"),
            library: "_dll_[name]"
        },
        plugins:[
            new webpack.DllPlugin({ // 这个插件是webpack内置的，用来生成任务清单文件
                name: "_dll_[name]",  // 暴露出的dll的函数名，name要等于library
                path: path.resolve(__dirname,"dist/manifest.json") //生成的任务清单路径，任务去生成的js文件里找
            })
        ]
    }
    执行webpack --config=webpack.config.react.js,生成dll文件和清单对象文件

    2)在src下的html中引入dll,与打包成功与否无关，主要是dev时能拿到react的包
      <script src="/dist/_dll_react.js"></script>

    3)在webpack.config.js中配置插件，打包文件时，看看有无任务清单对象，有的话就不会打包react，没有就打包react
      注意：判断关键在于有无任务清单对象，不是有没有dll文件，dll文件仅在执行打包后文件时起作用。
      new webpack.DllReferencePlugin({ //webpack内置插件
          manifest: path.resolve(__dirname,"dist/manifest.json") //指向任务清单对象
      })
######总结：
在通常的打包过程中，你所引用的诸如：jquery、bootstrap、react、react-router、redux、antd、vue、vue-router、vuex 等等众多库也会被打包进 bundle 文件中。由于这些库的内容基本不会发生改变，每次打包加入它们无疑是一种巨大的性能浪费。Dll 的技术就是在第一次时将所有引入的库打包成一个 dll.js 的文件，将自己编写的内容打包为 bundle.js 文件，这样之后的打包只用处理 bundle 部分。
#####happyPack（多线程打包大文件快一点）
    1) -cnpm i happypack -D  //安装
    2) let HappyPack = require("happypack") //引入
    3) new HappyPack({  // plugins里配置
        id: "js",  // 起个名字
        use:[{  // 原来写在module里面use的内容,现在转移到插件注册里
            loader: "babel-loader",
            options:{
                presets:["@babel/preset-env","@babel/preset-react"]
            }
        }]
    }),
    4) {  // module的rules里面内容
        test:/\.js$/,
        include: path.resolve("src"), 
        use: "HappyPack/loader?id=js"  // 现在module的rules里这样配置，id和plugin里的id对应
    },
    5) 要打包多种文件，需要new多个插件
#####webpack自带优化
######大前提是在生产环境下打包
    1) import引入的js文件（tree-shaking）
       会自动去掉没用到的代码，这种打包方式叫tree-shaking（摇树，把没用的叶子摇掉）
    2) require引入的js文件
       require引入es6语法暴露的对象，相当于用*来接收的import方式导出，所有内容放到一个对象中。
       无论用不用的到都会引入，不支持tree-shaking模式，推荐使用import引入
    3) webpack会自动省略一些可以简化的代码（scope hosting 作用域提升）
#####抽离公共代码
######应用场景
    多个入口js文件引用了相同的文件（手写js或者第三方库），把公共部分抽离出来生成缓存js文件，节约资源
######配置（entry同级）
    optimization:{  //优化项配置
        splitChunks:{ // 分割代码块
            cacheGroups:{  // 缓存组
                common:{  //抽离公共代码
                    minSize: 0, //抽离出来的最小代码大小
                    minChunks: 2, //公共代码调用的最小次数
                    chunks: "initial" //从入口处就开始提取代码
                },
                vendor:{ // 抽离第三方模块
                    priority: 1, // 权重，表示优先抽离这块代码，不配置这个的话，所有内容都抽离到common文件里了
                    test: /node_modules/, // 引用了node_modules里的东西就把它抽离出来（第三方安装到node_modules里）
                    chunks: "initial",
                    minChunks: 2,
                    minSize:0
                },
                
            }
        }
    },
##### 懒加载(按需加载)
    使用import("./source.js").then((data)=>{console.log(data)})
    import(),引入文件会返回一个promise对象
    build后会把加载的js文件生成一个1.js文件放到dist文件夹里，加载了多个文件就按数字往后排。
##### 热更新
###### 应用场景
    正常情况下，代码变化，页面会整体刷新，如果想只更新变化的部分，就要使用热更新
###### 配置
    devServer:{
        hot: true, // 启用热更新
        open: true,
        port: 5000
    },
    plugins:[
        new Hwp({
            template:"./src/index.html",
            filename: "index.html"
        }),
        new webpack.HotModuleReplacementPlugin(), // 启用热更新插件
        new webpack.NamedModulesPlugin()  // 打印更新的模块路径
    ]
###### js文件里使用
    import {a} from "./source.js"  // 引入资源，暴露一个字符串
    console.log(a) // 打印暴露的资源
    if(module.hot){  // 判断如果配置了热更新
        module.hot.accept("./source.js",()=>{  // 调用模块热更新方法，第一个参数为监听热更新的文件，第二个是回调函数
            console.log("热更新了")
            let a = require("./source.js");// 如果更新了文件，重新引入
            console.log(a.a)  //打印暴露出来的变量
        })
    }
    每当引入的模块有变化时，页面不会全部刷新了，只会局部刷新
## tapable
Webpack 本质上是一种事件流的机制，它的工作流程就是将各个插件串联起来，而实现这一切的核心就是 tapable，Webpack 中最核心的，负责编译的 Compiler 和负责创建 bundles 的 Compilation 都是 tapable 构造函数的实例。
##### tapable使用
    -cnpm i tapable
    



    
    

