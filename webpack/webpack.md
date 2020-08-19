# webpack

> 本学习文档以webpack3.8.1 学习为主，新版本待学会此版本后再做回顾

## 一、入门

### 1-1 前端的发展

Web前端技术从复杂庞大的管理后台到对性能要求苛刻的移动网页，再到类似ReactNative 的原生应用开发方案，Web前端工程师在面临更多机遇的同时也会面临更大的挑战。通过直接编写JavaScript、CSS、HTML开发Web应用的方式已经无法应对当前Web应用的发展。

#### 模块化

模块化是指把一个复杂的系统分解到多个模块以方便编码。

在此之前开发网页的JS文件需要通过命名空间的方式来组织代码，缺点如下：

- 命名空间冲突，两个库可能会使用同一个名称，例如`Zepto`和`jQuery`都被放在`window.$`下；
- 无法合理地管理项目的依赖和版本
- 无法方便地控制依赖的加载顺序

当项目变大时这种方式将变得难以维护，因此需要用模块化的思想来组织代码

#### CommonJS

CommonJS是一种广泛使用的JavaScript模块化规范，核心思想是通过require方法来同步地加载依赖的其他模块，通过`module.exports`导出需要暴露的接口。CommonJS规范的流行得益于Node.js采用了这种方式，后来这种方式被引入到了网页开发中。

采用CommonJS导入及导出的代码如下：

```javascript
// 导入
const moduleA = require('./moduleA')

// 导出
module.exports = moduleA.someFunc;
```

CommonJS的优点在于：

- 代码可复用于Node.js环境下并运行，例如做同构应用；
- 通过NPM发布的很多第三方模块都采用了CommonJS规范。

CommonJS的缺点在于这样的代码无法直接运行在浏览器环境下，必须通过工具转换成标准的ES5。

#### AMD

AMD 也是一种JavaScript 模块化规范，与CommonJS最大的不同在于它***采用异步***的方式去加载依赖的模块。AMD规范主要是为了解决针对***浏览器环境***的模块化问题，最具代表性的实现是`requirejs`。

采用AMD导入及导出时的代码如下：

```javascript
// 定义一个模块
define('module', ['dep'], function(dep) {
  return exports;
})

// 导入和使用
require(['module'], function(module) {
});
```

AMD的优点在于：

- 可在不转换代码的情况下直接在浏览器中运行； 
- 可异步加载依赖；
- 可并行加载多个依赖；
- 代码可运行在浏览器环境和Node.js环境下。
- AMD的缺点在于JavaScript运行环境没有原生支持AMD，需要先导入实现了AMD的库后才能正常使用。

#### ES6 模块化

ES6在语言的层面上实现了模块化。浏览器厂商和Node.js都宣布要原生支持该规范。它将逐渐取代CommonJS和AMD规范，成为浏览器和服务器通用的模块解决方案。

```javascript
// 导入
import { readFile } from 'fs';
import React from 'react';
// 导出
export function hello() {};
export default {
  // ...
};
```

ES6 模块虽然是终极模块化方案，但它的缺点在于目前无法直接运行在大部分JavaScript运行环境下，必须通过工具转换成标准的ES5后才能正常运行。

#### 样式文件中的模块化

除了JavaScript 开始模块化改造，前端开发里的样式文件也支持模块化，以SCSS为例，把一些常用的样式片段放进一个通用的文件里，再在另一个文件里通过`@import`语句导入和使用这些样式片段。

```scss
// util.scss文件 
// 定义样式片段
@mixin center {
  // 水平竖直居中
  position: absolute;
  left: 50%;
  top: 50;
  transform: translate(-50%, -50%);
}

// main.scss 文件 
// 导入和使用util.scss 中定义的样式片段
@import "util";
#box {
  @include center;
}
```

#### 新框架

在Web应用变得庞大复杂时，采用直接操作DOM的方式去开发将会使代码变得复杂和难以维护，许多新思想被引入到网页开发中以减少开发难度、提升开发效率。

##### React

React 框架引入JSX语法到JavaScript语言层面中，以更灵活地控制视图的渲染逻辑。

##### Vue

Vue 框架把一个组件相关的HTML模板，JavaScript逻辑代码、CSS样式代码都写在一个文件里

##### Angular2

Angular2 推崇采用TypeScript语言去开发应用，并且可以通过注解的语法描述组件的各种属性。

#### 新语言

JavaScript 最初被设计用于完成一些简单的工作，在用它开发大型应用时一些语言缺陷会暴露出来。CSS只能用表态的语法描述元素的样式，无法像写JavaScript 那样增加逻辑判断与共享变量。为了解决这些问题，许多新语言诞生了。

##### ES6

ECMAScript 6.0 是JavaScript语言的下一代标准。它在语言层面为JavaScript 引入了许多新语法和API，使得JavaScript 语言可以编写复杂的大型应用程序

- 规范模块化
- Class 语法
- 用`let`声明代码块内有效的变量，用`const`声明常量
- 箭头函数
- async函数
- Set 和 Map数据结构

但遗憾的是不同浏览器对这些特性的支持不一致，使用了这些特性的代码可能会在部分浏览器下无法运行。为了解决兼容性总是，需要把ES6代码转换成ES5代码，Babel是目前解决这个问题最好的工具。Babel的插件机制让它可灵活配置，支持把任何新语法转换成ES5语法。

##### TypeScript

TypeScript 是JavaScript 的一个超集，由Microsoft 开发并开源，除了支持ES6的所有功能，还提供了静态类型检查。采用TypeScript编写的代码可以被编译成符合ES5、ES6标准的JavaScript。将TypeScript用于开发大型项目时，其优点才能体现出来，因为大型项目由多个模块组合而成，不同模块可能又由不同人编写，在对接不同模块时静态类型检查会在编译阶段找出可能存在的问题。TypeScript的缺点在于语法相对于JavaScript更加啰嗦，并且无法直接支持在浏览器或Node.js环境下

##### Flow

Flow也是JavaScript的一个超集，它的主要特点是为JavaScript提供静态类型检查，和TypeScript相似但更灵活，可以让你只在需要的地方加上类型检查。

##### SCSS

SCSS可以让你用程序员的方式写CSS。它是一种CSS预处理器，基本思想是用和CSS相似的编程语言写完后再编译成正常的CSS文件。和SCSS类似的CSS预处理器还有LESS等。

---

使用新语言可以提升编码效率，但是必须把源代码转换成可以直接在浏览器环境下运行的代码。

### 1-2常见的构建工具及对比

在上一节中所提到的新思想和框架，都有一个共同的特点：源代码无法直接运行，必须通过转换后才可以正常运行。***构建***就是做这件事情的，把源代码转换成发布到线上可执行的JavaScript、CSS、HTML代码，包括如下的工作内容

- 代码转换：TypeScript 编译成JavaScript 、SCSS编译成CSS等
- 文件优化：压缩JavaScript、CSS、HTML代码，压缩合并图片等
- 代码分割：提取多个页面的公共代码、提取首屏不需要执行部分的代码让其异步加载。
- 模块合并：在采用模块化的项目里会有很多个模块和文件 ，需要构建功能把模块分类合并成一个文件 
- 自动刷新：监听本地源代码的变化，自动重新构建、刷新浏览器
- 代码校验：在代码被提交到仓库前需要校验代码是否符合规范，以及单元测试是否通过
- 自动发布：更新完代码后，自动构建出线上发布代码并传输给发布系统

构建是工程化、自动化思想在前端开发中的体现，把一系列流程用代码去实现，让代码自动化地执行这一系列复杂的流程。

历史上先后出现过一系列构建工具，各自有其优缺点。由于前端工程师很熟悉JavaScript，Node.js又可以胜任所有构建需求，所以大多数构建工具都是用Node.js开发的。

#### Npm Script

Npm Script 是一个任务执行者。Npm 是在安装Node.js 时附带的包管理器，Npm Script 则是Npm 内置的一个功能，允许在`package.json`文件里面使用`scripts`字段定义任务：

```javascript
{
  "scripts": {
    "dev": "node dev.js"，
    "pub": "node build.js"
  }
}
```

里面的`scripts`字段是一个对象，每个属性对应一段Shell 脚本，其底层实现原理是通过调用Shell 去运行脚本命令，例如执行`npm run pub`命令等同于执行`node build.js`

Npm Script 的优点是内置，无须安装其他依赖，其缺点是功能太简单，虽然提供了`pre`和`post`两个钩子，但不能方便地管理多个任务之间的依赖。

#### Grunt

Grunt 和 Npm Scirpt 类似，也是一个任务执行者。Grunt 有大量现成的插件封装了常见的任务，也能管理任务之间的依赖关系，自动化执行依赖的任务，每个任务的具体执行代码和依赖关系写在配置文件`Gruntfile.js`里

```javascript
module.exports = function(grunt) {
  // 所有插件的配置信息
  grunt.initConfig({
    // uglify 插件的配置信息
    uglify: {
      app_task: {
        files: {
          'build/app.min.js': ['lib/index.js', 'lib/test.js']
        }
      }
    },
    // watch 插件的配置信息
    watch: {
      another: {
          files: ['lib/*.js'],
      }
    }
  });

  // 告诉 grunt 我们将使用这些插件
  grunt.loadNpmTasks('grunt-contrib-uglify');
  grunt.loadNpmTasks('grunt-contrib-watch');

  // 告诉grunt当我们在终端中启动 grunt 时需要执行哪些任务
  grunt.registerTask('dev', ['uglify','watch']);
};
```

在项目根目录下执行命令`grunt dev`就会启动JavaScript 文件压缩和自动刷新功能。

Grunt 的优点是：

- 灵活，它只负责执行你定义的任务
- 大量的可复用插件封装好了常见的构建任务

Grunt 的缺点是集成度不高，要写很多配置后才可以用，无法做到开箱即用。

Grunt 相当于进化版的Npm Script，它的诞生是为了弥补Npm Script 的不足。

#### Gulp

Gulp 是一个基于流的自动化构建工具。除了可以管理和执行任务，还支持监听文件、读写文件。Gulp 被设计得非常简单，只通过下面5个方法就可以胜任几乎所有构建场景：

- 通过`gulp.task`注册一个任务
- 通过`gulp.run`执行任务
- 通过`gulp.watch`监听文件变化
- 通过`gulp.src` 读取文件 
- 通过`gulp.dest`写文件 

Gulp 的最大特点是引入了流的概念，同时提供了一系列常用的插件去处理流，流可以在插件之间传递，使用方式如下：

```javascript
// 引入 Gulp
var gulp = require('gulp'); 
// 引入插件
var jshint = require('gulp-jshint');
var sass = require('gulp-sass');
var concat = require('gulp-concat');
var uglify = require('gulp-uglify');

// 编译 SCSS 任务
gulp.task('sass', function() {
  // 读取文件通过管道喂给插件
  gulp.src('./scss/*.scss')
    // SCSS 插件把 scss 文件编译成 CSS 文件
    .pipe(sass())
    // 输出文件
    .pipe(gulp.dest('./css'));
});

// 合并压缩 JS
gulp.task('scripts', function() {
  gulp.src('./js/*.js')
    .pipe(concat('all.js'))
    .pipe(uglify())
    .pipe(gulp.dest('./dist'));
});

// 监听文件变化
gulp.task('watch', function(){
  // 当 scss 文件被编辑时执行 SCSS 任务
  gulp.watch('./scss/*.scss', ['sass']);
  gulp.watch('./js/*.js', ['scripts']);    
});
```

Gulp 的优点是好用又不失灵活，既可以单独完成构建也可以和其它工具搭配使用。其缺点和Grunt 类似，集成度不高，要写很多配置后才可以用，无法做到开箱即用。

可以将Gulp 看作Grunt 的加强版。相对于Grunt， Gulp 增加了监听文件、读写文件、流式处理的功能。

#### Fis3

Fis3 是一个来自百度的优秀国产构建工具。相对于Grunt、Gulp这些只提供基本功能的工具，Fis3集成了Web开发中的常用构建功能，如下所述。

- 读写文件：通过`fis.match`读取文件，`release`配置文件输出路径
- 资源定位：解析文件之间的依赖关系和文件位置。
- 文件指纹：通过`useHash`配置输出文件时给文件URL加上md5戳来优化浏览器缓存
- 文件编译：通过`parser`配置文件解析器做文件转换，例如把ES6编译成ES5
- 压缩资源：通过`optimizer`配置代码压缩方法。
- 图片合并：通过`spriter`配置合并CSS里导入的图片到一个文件来减少HTTP请求数。

使用方式

```javascript
// 加 md5
fis.match('*.{js,css,png}', {
  useHash: true
});

// fis3-parser-typescript 插件把 TypeScript 文件转换成 JavaScript 文件
fis.match('*.ts', {
  parser: fis.plugin('typescript')
});

// 对 CSS 进行雪碧图合并
fis.match('*.css', {
  // 给匹配到的文件分配属性 `useSprite`
  useSprite: true
});

// 压缩 JavaScript
fis.match('*.js', {
  optimizer: fis.plugin('uglify-js')
});

// 压缩 CSS
fis.match('*.css', {
  optimizer: fis.plugin('clean-css')
});

// 压缩图片
fis.match('*.png', {
  optimizer: fis.plugin('png-compressor')
});
```

Fis3 很强大，内置了许多功能，无须做太多配置就能完成大量工作。Fis3的优点是集成了各种Web开发所需的构建功能，配置简单开箱即用。其缺点是目前官方已经不再更新和维护，不支持最新版本的Node.js。

Fis3是一种专注于Web开发的完整解决方案。

#### Webpack

Webpack是一个打包模块化JavaScript的工具，在Webpack里一切文件皆模块，通过Loader 转换文件，通过Plugin注入钩子，最后输出由多个模块组合成的文件。Webpack专注于构建模块化项目。

![image-20200818220746370](webpack.assets/image-20200818220746370.png)

一切文件 ：JavaScript、CSS、SCSS、图片、模板，在Webpack眼中都是一个个模块，这样的好处是能清晰的描述出各个模块之间的依赖关系，以方便Webpack对模块进行组合和打包。经过Webpack的处理，最终会输出浏览器能使用的静态资源。

使用如下：

```javascript
module.exports = {
  // 所有模块的入口，Webpack 从入口开始递归解析出所有依赖的模块
  entry: './app.js',
  output: {
    // 把入口所依赖的所有模块打包成一个文件 bundle.js 输出 
    filename: 'bundle.js'
  }
}
```

Webpack的优点是：

- 专注于处理模块化的项目，能做到开箱即用一步到位；
- 通过Plugin扩展，完整好用又不失灵活；
- 使用场景不仅限于Web开发；
- 社区庞大活跃，经常引入紧跟时代发展的新特性，能为大多数场景找到已有的开源扩展
- 良好的开发体验

Webpack的缺点是只能用于采用模块化开发的项目。

#### Rollup

Rollup 是一个和Webpack很类似但专注于ES6的模块打包工具。Rollup的亮点在于能针对ES6源码进行Tree Shaking 以去除那些已被定义但没被使用的代码，以及Scope Hoisting 以减小输出文件大小提升运行性能。然后Rollup的这些亮点随后就被Webpack模仿和实现。

---

#### 为什么选择Webpack?

经过多年的发展， Webpack 已经成为构建工具中的首选，这是有原因的：

- 大多数团队在开发新项目时会采用紧跟时代的技术，这些技术几乎都会采用“模块化+新语言+新框架”，Webpack 可以为这些新项目提供一站式的解决方案；
- Webpack 有良好的生态链和维护团队，能提供良好的开发体验和保证质量；
- Webpack 被全世界的大量 Web 开发者使用和验证，能找到各个层面所需的教程和经验分享。

### 1-3安装Webpack

**安装Webpack到本项目**

```shell
# npm i -D 是 npm install --save-dev 的简写，是指安装模块并保存到 package.json 的 devDependencies
# 安装最新稳定版
npm i -D webpack

# 安装指定版本
npm i -D webpack@<version>

# 安装最新体验版本
npm i -D webpack@beta
```

安装完成后可以通过以下途径运行安装到本项目的Webpack

- 在项目根目录下对应的命令行里通过 `node_modules/.bin/webpack`运行Webpack可执行文件 

  或执行`npx webpack`相当于执行上边的代码

- 在Npm Script 里定义的任务会优先使用本项目下的Webpack

    ```javascript
  "scripts": {
    "start": "webpack --config webpack.config.js"
  }
  ```

**安装Webpack到全局**

安装到全局后你可以在任何地方共用一个webpack可执行文件，而不用各个项目重复安装

```shell
npm i -g webpack
```

推荐安装到当前项目，以防止不同项目依赖不同版本的webpack而导致冲突

**使用Webpack**

Webpack在执行构建时默认会从项目根目录下的`webpack.config.js`文件读取配置

```javascript
const path = require('path');

module.exports = {
  // JavaScript 执行入口文件
  entry: './main.js',
  output: {
    // 把所有依赖的模块合并输出到一个 bundle.js 文件
    filename: 'bundle.js',
    // 输出文件都放到 dist 目录下
    path: path.resolve(__dirname, './dist'),
  }
};
```

由于Webpack构建运行在Node.js环境下，所以该文件最后需要通过CommonJS规范导出一个描述如何构建的`Object`对象。

在项目根目录下执行`npx webpack`命令运行构建，就会生成一个bundle.js 里面包含了两个模块`main.js`和`show.js`以及内置的`webpackBootstrap`启动函数。

Webpack是一个打包模块化JavaScript 的工具，它会从`main.js`出发，识别出源码中的模块化导入语句，递归的寻找出入口文件的所有依赖，把入口和其所有依赖打包到一个单独的文件中。从Webpack2开始，已经内置了对ES6、CommonJS、AMD模块化语句的支持。

### 1-4使用Loader

Webpack不原生支持解析CSS文件。要支持非JavaScript 类型的文件 ，需要使用Webpack的Loader 机制。webpack的配置修改如下：

```javascript
const path = require('path');

module.exports = {
  // JavaScript 执行入口文件
  entry: './main.js',
  output: {
    // 把所有依赖的模块合并输出到一个 bundle.js 文件
    filename: 'bundle.js',
    // 输出文件都放到 dist 目录下
    path: path.resolve(__dirname, './dist'),
  },
  module: {
    rules: [
      {
        // 用正则去匹配要用该 loader 转换的 CSS 文件
        test: /\.css$/,
        use: ['style-loader', 'css-loader?minimize'],
      }
    ]
  }
};
```

Loader 可以看作具有文件转换功能的翻译员，配置里的`module.rules`数组配置了一组规则，告诉Webpack在遇到哪些文件时使用哪些Loader 去加载和转换。如上配置告诉Webpack在遇到以`.css`结尾的文件时先使用`css-loader`读取文件，再交给`style-loader`把CSS内容注入到JavaScript 里。在配置Loader 时需要注意：

- `use`属性的值需要是一个由Loader 名称组成的数组，Loader 的执行顺序是***由后到前的***；
- 每个Loader 都可以通过URL querystring 的方式传入参数，例如`css-loader?minimize`中的`minimize`告诉`css-loader`要开启CSS压缩

在重新执行webpack构建前需要先安装新引入的Loader

```shell
npm i -D style-loader css-loader
```

重新构建后，`bundle.js`文件被更新，里面注入了css内容，这其实是`style-loader`的功劳，它的工作原理大概是把CSS内容用JavaScript里的字符串存储起来，在网页执行JavaScript时通过DOM操作动态地往HTML head 标签里插入HTML style 标签

给Loader 传入属性的方式除了有querystring 外，还可以通过Object 传入，以上的Loader 配置可以修改为如下：

```javascript
use: [
  'style-loader', 
  {
    loader:'css-loader',
    options:{
      minimize:true,
    }
  }
]
```

除了在`webpack.config.js`配置文件中配置Loader 外，还可以在源码中指定用什么Loader 去处理文件。以加载CSS文件为例：

```javascript
require('style-loader!css-loader?minimize!./main.css');
```

这样就能指定对./main.css 这个文件先采用css-loader再采用style-loader转换。

### 1-5使用Plugin

Plugin 是用来扩展Webpack功能的，通过在构建流程里注入钩子实现，它给Webpack带来了很大的灵活性。

将上一节的CSS文件的提取取单独的文件中，配置如下：

```javascript
const path = require('path');
const ExtractTextPlugin = require('extract-text-webpack-plugin');

module.exports = {
  // JavaScript 执行入口文件
  entry: './main.js',
  output: {
    // 把所有依赖的模块合并输出到一个 bundle.js 文件
    filename: 'bundle.js',
    // 把输出文件都放到 dist 目录下
    path: path.resolve(__dirname, './dist'),
  },
  module: {
    rules: [
      {
        // 用正则去匹配要用该 loader 转换的 CSS 文件
        test: /\.css$/,
        use: ExtractTextPlugin.extract({
          // 转换 .css 文件需要使用的 Loader
          use: ['css-loader'],
        }),
      }
    ]
  },
  plugins: [
    new ExtractTextPlugin({
      // 从 .js 文件中提取出来的 .css 文件的名称
      filename: `[name]_[contenthash:8].css`,
    }),
  ]
};
```

需要安装新引入的插件

```shell
npm i -D extract-text-webpack-plugin
```

重新构建后，会发现css 已经变成了一个单独的文件，在index.html中重新加入css 的引用即可。

Webpack是通过`plugins`属性来配置需要使用的插件列表的。`plugins`属性是一个数组，里面的每一项都是插件的一个实例，在实例化一个组件时可以通过构造函数传入这个组件支持的配置属性。

### 1-6使用 DevServer

前面的几节只是让Webpack正常运行起来，但在实际开发中你可能会需要：

1. 提供HTTP服务而不是使用本地文件预览
2. 监听文件的变化并自动刷新网页，做到实时预览
3. 支持SourceMap，以方便调试

对于这些，官方提供的开发工具DevServer 可以很方便的做到，DevServer会启动一个Http服务器用于服务网页请求，同时会启动Webpack, 并接收Webpack发出的文件变更信号，通过WebSocket 协议自动刷新网页做到实时预览

为之前的项目安装WebpackDevServer

```shell
npm i -D webpack-dev-server
```

安装成功后执行`webpack-dev-server`命令，DevServer 就启动了，DevServer 启动后会一直驻留在后台保持运行，但是这时用浏览器访问地址，会报404找不到./dist/bundle.js的错误，原因是DevServer 会把Webpack构建出的文件保存在内存中，在要访问输出的文件时，必须通过HTTP服务访问。由于DevServer 不会理会webpack.config.js里配置的output.path属性，所以要获取bundle.js 的正确URL是http://localhost:8080/bundle.js，对应的index.html 同时需要进行修改。

**实时预览**

webpack在启动时可以开启监听模式，开启监听模式后webpack会监听本地文件系统的变化，发生变化时重新构建出新的结果。webpack默认是关闭监听模式的，你可以在启动webpack时通过`webpack --watch`来开启监听模式。

通过DevServer 启动的webpack会开启监听模式，当发生变化时重新执行完构建后通知DevServer。DevServer 会让webpack 在构建出的JavaScript代码里注入一个代理客户端用于控制网页，网页和DevServer之间通过websocket 协议通信，以方便DevServer主动向客户端发送命令。DevServer 在收到来自webpack的文件变化通知时通过注入的客户端控制见面刷新。

>**注：**如果尝试修改index.html文件并保存，不会触发上述的机制，导致这个问题的原因是webpack在启动时会以配置里的entry为入口去递归解析出entry所依赖的文件，只有entry本身和依赖的文件才会被webpack添加到监听列表里，而index.html 文件是脱离了JavaScript模块化系统的，所以webpack不知道它的存在。

**模块热替换**

除了通过重新刷新整个网页来实现实时预览，DevServer 还有一种被称为模块热替换的刷新技术。模块热替换能做到在不重新加载整个网页的情况下，通过将被更新过的模块替换老的模块，再重新执行一次来实现实时预览。模块热替换相对于默认的刷新机制能提供更快的响应和更好的开发体验。模块热替换默认是关闭的，要开启模块热替换，只需要在启动DevServer时带上`--hot`参数，重启DevServer 后再去更新文件就能体验到模块热替换了。

**支持SourceMap**

在浏览器中运行的JavaScript 代码都是编译器输出的代码，这些代码可读性差。如果需要在开发过程中进行断点调试，可以通过Source Map映射代码，让你在源代码上断点调试。webpack支持生成Source Map，在启动命令上带上`--devtool source-map`参数。加上重启DevServer后刷新页面，再打开Chrome 浏览器的开发者工具，就可在Sources栏中看到可调试的源代码了。

```shell
npx webpack-dev-server --devtool source-map
```



![image-20200819204934023](webpack.assets/image-20200819204934023.png)



### 1-7核心概念

- **Entry**：入口，webpack执行构建的第一步将从Entry 开始，可抽象成输入。
- **Module**: 模块，在webpack里一切皆模块，一个模块对应着一个文件。webpack会从配置的Entry开始递归找出所有依赖的模块。
- **Chunk**: 代码块，一个Chunk由多个模块组合而成，用于代码合并与分割。
- **Loader**: 模块转换器，用于把模块原内容按照需求转换成新内容。
- **Plugin**: 扩展插件，在webpack构建流程中的特定时机注入扩展逻辑来改变构建结果或做你想要的事情。
- **Output**: 输出结果，在webpack经过一系列处理并得出最终想要的代码后输出结果。

Webpack启动后会从Entry里配置的Module 开始递归解析Entry 依赖的所有Module。每找到一个Module，就会根据配置的Loader 去找出对应的转换规则，对Module进行转换后，再解析出当前Module 依赖的Module。这些模块会以Entry 为单位进行分组，一个Entry和其所有依赖的Module 被分到一个组也就是一个Chunk。最后webpack会把所有Chunk转换成文件输出。在整个流程中webpack会在恰当的时机执行Plugin里定义的逻辑。

## 二、配置

> 配置webpack的方式有两种：
>
> 1. 通过一个JavaScript 文件描述配置，例如使用webpack.config.js文件里的配置
> 2. 执行webpack可执行文件时通过命令行参数传入，例如webpack --devtool source-map
>
> 这两种方式可以相互搭配，例如执行webpack时通过命令`webpack --config webpack-dev.config.js`指定配置文件，再去`webpack-dev.config.js`文件里描述部分配置。

### 2-1 Entry

> `entry`是配置模块的入口，可抽象成输入，webpack执行构建的第一步将从入口开始搜寻及递归解析出所有入口依赖的模块。
>
> `entry`配置是***必填***的，若不填写则将导致webpack报错退出

**context**

webpack在寻找相对路径的文件时会以`context`为根目录，`context`默认为执行启动webpack时所有的当前工作目录。如果想改变`context`的默认位置，则可以在配置文件里进行设置

```javascript
module.exports = {
  context: path.resolve(__dirname, 'app')
}
```

> **注意：** `context`必须是一个绝对路径的字符串。除此之外，还可以在启动webpack时带上参数`webpack --context`来设置`context`

**Entry 类型**

Entry 类型可以是以下三种中的一种或者相互组合

| 类型   | 例子                                                         | 含义                                 |
| ------ | ------------------------------------------------------------ | ------------------------------------ |
| string | `'./app/entry'`                                              | 入口模块的文件路径，可以是相对路径。 |
| array  | `['./app/entry1', './app/entry2']`                           | 入口模块的文件路径，可以是相对路径。 |
| object | `{a: './app/entry-a', b: ['./app/entry-b1', './app/entry-b2']}` | 配置多个入口，每个入口生成一个Chunk  |

如果是`array`类型，则搭配`output.library`配置项使用时，只有数组里的最后一个入口文件的模块会被导出

**Chunk 名称**

webpack 会为每个生成的Chunk取一个名称，Chunk的名称和Entry 的配置有关：

- 如果entry 是一个string 或 array，就只会生成一个chunk，这时chunk 的名称是`main`
- 如果entry 是一个object，就可能会出现多个chunk，这时chunk 的名称是object 键值对里键的名称。

**配置动态Entry**

假如项目里有多个页面需要为每个页面的入口配置一个Entry，但这些页面的数量可能会不断增长，则这时Entry的配置会受到其他因素影响导致不能写成静态的值。其解决方法是把Entry 设置成一个函数去动态返回上面所叙述的值，代码如下：

```javascript
// 同步函数
entry: () => {
  return {
    a: './pages/a',
    b: './pages/b'
  }
};
// 异步函数
entry: () => {
  return new Promise((resolve) => {
    resolve({
      a: './pages/a',
    	b: './pages/b'
    });
  })
}
```

### 2-2 Output

`output`配置如何输出最终想要的代码。output是一个object 里面包含一系列配置项

**filename**

`output.filename`配置输出文件的名称，为string类型，如果只有一个输出文件，则可以把它写成静态不变的

```javascript
filename: 'bundle.js'
```

但是在多个chunk 要输出时，需要借助模板和变量。webpack会为每个chunk 取一个名称，可以根据chunk的名称来区分输出的文件名

```javascript
filename: '[name].js'
```

代码里的`[name]`代表用内置的name 变量去替换[name]，这时你可以把它看作一个字符串模块函数，每个要输出的chunk都会通过这个函数去拼接出输出的文件名称

内置变量

| 变量名    | 含义                      |
| --------- | ------------------------- |
| id        | chunk 的唯一标识，从0开始 |
| name      | chunk的名称               |
| hash      | chunk 的唯一标识的hash值  |
| chunkhash | chunk内容的hash值         |

其中`hash`和`chunkhash`的长度是可指定的，`[hash:8]`代表取8位Hash值，默认是20位。

**chunkFilename**

`output.chunkFilename`配置无入口的chunk在输出时的文件名称，chunkFilename和上面的filename 非常类似，但chunkFilename只用于指定在运行过程中生成的Chunk在输出时的文件名称。常见的会在运行时生成Chunk场景有在使用CommonChunkPlugin、使用`import('path/to/module')`

**path**

`output.path`配置输出文件存放在本地的目录，必须是string类型的绝对路径。通过通过Node.js的`path`模块去获取绝对路径

```javascript
path: path.resolve(__dirname, 'dist_[hash]')
```

**publicPath**

在复杂的项目里可能会有一些构建出的资源需要异步加载，加载这些异步资源需要对应的URL地址。

`output.publicPath`配置发布到线上资源的URL前缀，为string类型。默认值是空字符串`''`，即相对路径

```javascript
filename: '[name]_[chunkhash:8].js',
publicPath: 'https://cdn.example.com/assets/'
```

这时发布到线上的HTML在引入JavaScript文件时就需要

```html
<script src="https://cdn.example.com/assets/a_12345678.js"></script>
```

使用该配置项时要小心，可能会导致`404`错误

`output.path`和`output.publicPath`都支持字符串模板，内置变量只有一个`hash`代表一次编译操作的Hash值

**crossOriginLoading**

webpack输出的部分代码块可能需要异步加载，而异步加载是通过JSONP方式实现的。JSONP的原理是动态地向HTML中插入一个`<script src="url"></script>`标签去加载异步资源。

`output.crossOriginLoading`则是用于配置这个异步插入的标签的`crossorigin`值

script标签的crossorigin属性可以取以下值

- `anonymous`(默认)在加载此脚本资源时不会带上用户的Cookies;
- `use-credentials`在加载此脚本资源时会带上用户的Cookies

通常用设置crossorigin 来获取异步加载的脚本执行时的详细错误信息。

**libraryTarget和library**

当用webpack去构建一个可以被其他模块导入使用(给别人)的库时需要用到它们。

- output.libraryTarget配置以何种方式导出库
- output.library 配置导出库的名称

它们通常搭配在一起使用

output.libraryTarget 是字符串的枚举类型

| 枚举值     |
| ---------- |
| var (默认) |
| commonjs   |
| commonjs2  |
| this       |
| window     |
| global     |

**libraryExport**

`output.libraryExport`配置要导出的模块中哪些子模块需要被导出。它只有在`output.libraryTarget`被设置成`commonjs`或者`commonjs2`时才有意义

### 2-3 Module

>`module`配置如何处理模块

**配置Loader**

`rules`配置模块的读取和解析规则，通常用来配置Loader。其类型是一个数组，数组里每一项都描述了如何去处理部分文件。可通过以下方式进行配置

- 条件匹配：通过`test`、`include` 、`exclude`三个配置项来命中Loader要应用规则的文件
- 应用规则：对选中后的文件通过`use`配置项来应用Loader ，可以只应用一个Loader 或者按照从后往前的顺序应用一组Loader，同时还可以分别给Loader传入参数
- 重置顺序：一组Loader的执行顺序默认是从右到左执行，通过`enforce`选项可以让其中一个Loader的执行顺序放到最前或最后

```javascript
module: {
  rules: [
    {
      // 命中JavaScript文件 
      test: /\.js$/,
      // 用babel-loader 转换JavaScript 文件
      // ?cacheDirectory表示传给babel-loader的参数，用于缓存babel 编译结果加快重新编译速度
      use: ['babel-loader?cacheDirectory'],
      // 只命中src目录里的js文件，加快webpack搜索速度
      include: path.resolve(__dirname, 'src')
    },
    {
      // 命中SCSS文件 
      test: /\.scss$/,
      // 使用一组Loader 去处理SCSS文件 
      // 处理顺序为从后到前
      use: ['style-loader', 'css-loader', 'sass-loader'],
      // 排除node_modules 目录下的文件
      exclude: path.resolve(__dirname, 'node_modules')
    },
    {
      // 对非文本文件采用file-loader 加载
      test: /\.(gif|png|jpe?g|eot|woff|ttf|svg|pdf)$/,
      use: ['file-loader']
    }
  ]
}
```

在Loader 需要传入很多参数时，你还可以通过一个Object 来描述

```javascript
use: [
  {
    loader: 'babel-loader',
    options: {
      cacheDirectory: true
    },
    // enforce: 'post'是把Loader 的执行顺序放到最后
    // enforce: 'pre'是把Loader 的执行顺序放到最前面
    enforce: 'post'
  },
  // 省略其他Loader
]
```

`test`、`include`、`exclude`这三个命中文件的配置项还都支持数组类型

```javascript
{
  test: [
    /\.jsx?$/,
    /\.tsx?$/
  ],
  include: [
    path.resolve(__dirname, 'src'),
    path.resolve(__dirname, 'tests')
  ],
  exclude: [
    path.resolve(__dirname, 'node_modules'),
    path.resolve(__dirname, 'bower_modules')
  ]
}
```

数组里的每项之间是或的关系，即文件路径符合数组中的任何一个条件就会被命中。

**noParse**

`noParse`配置项可以让webpack忽略对部分没采用模块化的文件的递归解析和处理，这样做的好处是能提高构建性能。比如jQuery、ChartJS它们庞大又没有采用模块化标准，让webpack去解析这些文件耗时又没有意义。

`noParser`是可选配置项，类型需要是`RegExp`、`[RegExp]`、`function`其中一个

```javascript
// 使用正则表达式
noParse: /jquery|chartjs/

// 使用函数，从webpack 3.0.0开始支持
noParse: (content) => {
  // content 代表一个模块的文件路径
  // 返回true or false
  return /jquery|chartjs/.test(content)
}
```

> 被忽略的文件里不应该包含`import`、`require`、`define`等模块化语句

**parser**

因为webpack是以模块化为JavaScript文件为入口，所以内置了对模块化JavaScript的解析功能，支持AMD、CommonJS、SystemJS、ES6。`parser`属性可以更细粒度的配置哪些模块语法要解析哪些不解析，和`noParse`配置项的区别在于`parser`可以精确到语法层面，而`noParse`只能控制哪些文件不被解析

```javascript
module: {
  rules: [
    {
      test: /\.js$/,
      use: ['babel-loader'],
      parser: {
        amd: false, // 禁用AMD
        commonjs: false, // 禁用CommonJS
        system: false, // 禁用SystemJS
        harmony: false, // 禁用ES6 import/export
        requireInclude: false, // 禁用require.include
        requireEnsure: false, // 禁用require.ensure
        requireContext: false, // 禁用require.context
        browserify: false, // 禁用browserify
        requireJs: false, // 禁用requirejs
      }
    }
  ]
}
```

### 2-4 Resolve

> webpack 在启动后会从配置的入口模块出发找出所有依赖的模块，Resolve配置webpack如何寻找模块所对应的文件。webpack内置JavaScript模块化语法解析功能，默认会采用模块化标准里约定好的规则去寻找，但你也可以根据自己的需要修改默认的规则。

**alias**

`resolve.alias`配置项通过别名来把原导入路径映射成一个新的导入路径

```javascript
// webpack alias 配置
resolve: {
  alias: {
    components: './src/components/'
  }
}
```

当你通过`import Button from 'components/button'`导入时，实际上被`alias`等价替换成了`import Button from './src/components/button'`

以上alias 配置的含义是把导入语句里的components关键字替换成./src/components/

alias 还支持`$`符号来缩小范围到只命中以关键字结尾的导入语句：

```javascript
resolve: {
  alias: {
    'react$': '/path/to/react.min.js'
  }
}
```

`react$`只会命中以`react`结尾的导入语句，只会把`import 'react'`关键字替换成`import '/path/to/react.min.js'`

**mainFields**

有一些第三方模块会针对不同环境提供几份代码。例如分别提供采用ES5和ES6的2份代码，这2份代码的位置写在`package.json`文件中

```json
{
  // 采用ES6语法的代码入口文件
  "jsnext:main": "es/index.js",
  // 采用ES5 语法的代码入口文件
  "main": "lib/index.js"
}
```

webpack 会根据`mainFields`的配置去决定优先采用哪份代码

```javascript
mainFields: ['browser', 'main']
```

webpack 会按数组里的顺序去`package.json`文件里查找，只会使用找到的第一个。

假如你想优先采用ES6的那份代码，可以这样配置

```javascript
mainFields: ['jsnext:main', 'browser', 'main']
```

**extensions**

在导入语句没带文件后缀时，webpack会自动带上后缀去尝试访问文件是否存在。`resolve.extensions`用于配置在尝试过程中用到的后缀列表，默认如下

```javascript
extensions: ['.js', '.json']
```

即当遇到`require('./data')`这样的导入语句时，webpack先去寻找`./data.js`文件，如果该文件不存在就去寻找`./data.json`文件，如果还是找不到就报错。

假如想让webpack优先使用目录下的TypeScript文件，可以这样配置

```javascript
extensions: ['.ts', '.js', '.json']
```

**modules**

`resolve.modules`配置webpack去哪些目录下寻找第三方模块，默认只会去`node_modules`目录下寻找。有时你的项目里会有一些模块会大量被其它模块依赖和导入，由于其它模块的位置分布不定，针对不同的文件都要去计算被导入模块文件的相对路径，这个路径有时候会很长，就像这样`import '../../../components/button'`这时你可以利用modules 配置项优化，假如那些被大量导入的模块都在./src/components 目录下，把`modules`进行配置

```javascript
modules: ['./src/components', 'node_modules']
```

之后再进行调用，可以通过`import 'button'`导入

**descriptionFiles**

`resolve.descritpionFiles`配置描述第三方模块的文件名称，也就是`package.json`文件，默认如下

```javascript
descriptionFiles: ['package.json']
```

**enforceExtension**

`resolve.enforceExtension`如果配置为true所有导入语句都必须带文件后缀，例如开启前`import './foo'`能正常工作，开启后必须写成`import './foo.js'`

**enforceModuleExtension**

与`enforceExtension`作用类似，但是其只对`node_modules`下的模块生效，通常搭配`enforceExtension`使用，当`enforceExtension`为true时，因为安装的第三方模块中大多数导入语句没带文件后缀，所以这时通过配置`enforceModuleExtension`为false来兼容第三方模块。

### 2-5 Plugin

>  Plugin 用于扩展webpack功能，各种各样的Plugin几乎让webpack可以做任何构建相关的事情。

**配置Plugin**

Plugin 的配置很简单，`plugins`配置项接受一个数组，数组里每一项都是一个要使用的Plugin的实例，Plugin需要的参数通过构造函数传入。

```javascript
const CommonsChunkPlugin = require('webpack/lib/optimize/CommonChunkPlugin');

module.exports = {
  plugins: [
    // 所有页面都会用到的公共代码提取到common代码块中
    new CommonsChunkPlugin({
      name: 'common',
      chunks: ['a', 'b']
    })
  ]
}
```

使用Plugin 的难点在于掌握Plugin本身提供的配置项，而不是如何在Webpack中接入Plugin。

### 2-6 devServer

> 除了在配置文件里通过`devServer`传入参数外，还可以通过命令行参数传入。注意只有在通过DevServer去启动webpack时配置文件里devServer 才会生效，因为这些参数所对应的功能都是DevServer提供的，webpack本身并不认识devServer 配置项。

**hot**

`devServer.hot`配置是否启用模块热替换功能。DevServer 默认的行为是在发现源代码被更新后会通过自动刷新整个页面来做到实时预览，开启模块热替换功能后将在不刷新整个页面的情况下通过用新模块替换老模块来做到实时预览。

**inline**

DevServer 的实时预览功能依赖一个注入到页面里的代理客户端去接受来自DevServer的命令和负责刷新网页的工作。`devServer.inline`用于配置是否自动注入这个代理客户端到将运行在页面里的Chunk里去，默认是会自动注入。DevServer会根据你是否开启`inline`来调整它的自动刷新策略：

- 如果开启`inline`，DevServer 会在构建完变化后的代码时通过代理客户端控制网页刷新 
- 如果关闭`inline`，DevServer 将无法直接控制要开发的网页。这时它会通过iframe 的方式去运行要开发的网页，当构建完变化后的代码时通过刷新iframe 来实现实时预览。这时你需要去`http://locahost:8080/webpack-dev-server`实时预览你的网页

建议默认开启就可以

**historyApiFallback**

`devServer.historyApiFallback`用于方便的开发使用了HTML5 History API的单页应用。这类单页应用要求服务器在针对任何命中路由时都返回一个对应的HTML文件，这类单页应用要求服务器在针对任何命中的路由时都返回一个对应的HTML文件，例如在访问`http://localhost/user` 和 `http://localhost/home` 时都返回 `index.html` 文件， 浏览器端的 JavaScript 代码会从 URL 里解析出当前页面的状态，显示出对应的界面。

配置 `historyApiFallback` 最简单的做法是：

```js
historyApiFallback: true
```

这会导致任何请求都会返回 `index.html` 文件，这只能用于只有一个 HTML 文件的应用。

如果你的应用由多个单页应用组成，这就需要 DevServer 根据不同的请求来返回不同的 HTML 文件，配置如下：

```js
historyApiFallback: {
  // 使用正则匹配命中路由
  rewrites: [
    // /user 开头的都返回 user.html
    { from: /^\/user/, to: '/user.html' },
    { from: /^\/game/, to: '/game.html' },
    // 其它的都返回 index.html
    { from: /./, to: '/index.html' },
  ]
}
```

**contentBase**

`devServer.contentBase`配置DevServer Http 服务器的文件根目录。默认情况下为当前执行目录，通常是项目根目录，一般情况下不必进行设置，除非你有额外的文件需要被DevServer服务。例如你想把项目根目录下的`public`目录设置成DevServer服务器的文件根目录

```javascript
devServer: {
  contentBase: path.join(__dirname, 'public')
}
```

DevServer 服务器通过HTTP服务暴露出的文件分为两类

- 暴露本地文件
- 暴露webpack构建出的结果，由于构建出的结果交给了DevServer，所以你在使用了DevServer时在本地找不到构建出的文件

`contentBase`只c有用来配置暴露本地文件的规则，你可以通过`contentBase:false`来关闭暴露本地文件

**headers**

`devServer.headers`配置项可以在HTTP响应中注入一些HTTP响应头

```javascript
devServer: {
  headers: {
    'X-foo': 'bar'
  }
}
```

**host**

`devServer.host`配置项用于配置DevServer服务监听的地址。默认为`127.0.0.1`只能本地可以访问DevServer的HTTP服务，如果你想要局域网中的其它设备访问你本地服务，可以在启动DevServer 时带上 `--host 0.0.0.`

**port**

`devServer.port`配置项用于配置DevServer服务监听的端口，默认使用8080。如果被占用就继续向上加1

**allowedHosts**

`devServer.allowedHosts`配置一个白名单列表，只有HTTP请求的HOST在列表里才正常返回

```javascript
allowedHosts: [
  // 匹配单个域名
  'host.com',
  'sub.host.com',
  // host2.com 和所有的子域名 *.host2.com 都将匹配
  '.host2.com'
]
```

**disableHostCheck**

`devServer.disableHostCheck`配置项用于配置是否关闭用于DNS重绑定的HTTP请求的HOST检查。DevServer默认只接受来自本地请求，关闭后可以接受来自任何HOST的请求。它通常用于搭配`--host 0.0.0.0`使用

**https**

DevServer 默认使用HTTP协议服务，它也能通过 HTTPS 协议服务。 有些情况下你必须使用 HTTPS，例如 HTTP2 和 Service Worker 就必须运行在 HTTPS 之上。 要切换成 HTTPS 服务，最简单的方式是：

```js
devServer:{
  https: true
}
```

DevServer 会自动的为你生成一份 HTTPS 证书。

如果你想用自己的证书可以这样配置：

```js
devServer:{
  https: {
    key: fs.readFileSync('path/to/server.key'),
    cert: fs.readFileSync('path/to/server.crt'),
    ca: fs.readFileSync('path/to/ca.pem')
  }
}
```

**clientLogLevel**

`devServer.clientLogLevel`配置在客户端的日志等级，这会影响到你在浏览器开发者工具控制台里看到的日志内容。`clientLogLevel` 是枚举类型，可取如下之一的值 `none | error | warning | info`。 默认为 `info` 级别，即输出所有类型的日志，设置成 `none` 可以不输出任何日志。

**compress**

`devServer.compress`配置是否启用gzip压缩，`boolean`为类型，默认为`false`

**open**

`devServer.open` 用于在DevServer 启动且第一次构建完时自动用你系统上的默认的浏览器去打开要开发的网页。同时还提供`devServer.openPage`配置项我用于打开指定URL的网页。




























































































