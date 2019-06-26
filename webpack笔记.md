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
- *.zip   过滤zip后缀文件
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

#####接下来我们根据上面的实际例子逐条解析，balabalabala～～～开始了：

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
    loader:'style-loader,
    //这种写法可以加 options:{
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

#####需要新建文件：在webpac.config.js同级目录下新建postcss.config.js文件

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

## 把模式改成'pruduction'
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
                    options:{//用babel-loader 把es6转换成es5
                      presets:[ //presets是预设意思，(大插件集合)这里添加大的插件库,把es6转化成es5
                                        '@babel/preset-env'
                          ],
              plugins:[
                                          // 更高级的语法 需要配置 到官网babeljs.io查看,如查decorators
                                            ["@babel/plugin-proposal-decorators", { "legacy": true }],//如用语法@log装饰器,下载这插件
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
                              "@babel/plugin-transform-runtime"  //@babel/plugin-transform-runtime @babel/runtime -D
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
#####需要安装:@babel/polyfill -S，如:
    'aaa'.includes('a');

#####在需要用到的js下配置:

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
## 如安装juqery
    需要安装:jquery -S

#####使用方法一：js文件中直接引入

    import $ from 'jquery';
    console.log($); 

#####使用方法二(有点变态)：暴露全局(给window对象)的 expose-loader  （目前loader使用方式共:
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


    
    

