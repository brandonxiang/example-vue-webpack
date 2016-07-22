# example-vue-webpack
An example for vue and webpack

## Build Setup

``` bash
# install dependencies
npm install

# serve with hot reload at localhost:8080
npm run dev

# build for production with minification
npm run build
```

For detailed explanation on how things work, consult the [docs for vue-loader](http://vuejs.github.io/vue-loader).

# Vue笔记二：进阶[译]用Webpack构建Vue

> 我一直想试着一下翻译别人的文章，因为总是觉得自己的文章写的不是很好。希望从中学习一下。

>原文[Vue.js build set-up from scratch with webpack, vue-loader and hot reload](http://www.skyronic.com/2015/12/28/vue-project-scratch/)。译文内容和原文内容可能有点出入，可能会带有我个人的思路，敬请原谅，版权由原文所有。

#通过webpack，vue-loader 和 hot reload来构建Vue.js

Vue.js是我如今最喜欢的JavaScript界面构建工具。这篇文章会告诉你如何去通过Vue.js，热模块替换，webpack和整个vue生态去构建一个项目。

![图片来自 http://cn.vuejs.org/](http://upload-images.jianshu.io/upload_images/685800-29d6ea5b237d929d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

##介绍

从2007年起开发Javascript以来，我喜欢angular，react和ember。但是真正让我看到闪光点的是vuejs。对于所有人所有问题来说，它不是最完美的解决方案，但是我看到了它的价值。

这是真的Javascript的一个潮流，特别是在react社区做一个模块实现一个功能，并允许用户将它们模块黏合在一起。这篇文章受[文章](https://medium.com/@ericclemmons/javascript-fatigue-48d4011b6fc4#.7fzmd2bxh)的激发。这里有很多[react 模板](https://github.com/search?utf8=%E2%9C%93&q=react+boilerplate)，包括一些过时，一些存在问题，一些不能工作的。

在写文章的这段时间，Vue的开发者完善了**官方入门者工具**，包括[命令行](https://github.com/vuejs/vue-cli)构建一个新项目，给出了一个明确和易于维护的起点。

但是，你自己构建项目也有几个优点：

- 你的项目需求和模板的不同
- 你需要一些新东西，如预处理和工具链，你不要拆散这些工具
- 你不需要模板的一些库

那就是说，作者觉得模板是非常值得去学的资源，而且你可以拿到一些提示，你读代码后你会选择现成的模板。

##开始前

我验证了一下操作说明在2015/12/28在最新版Node.js 在Ubuntu Linux。

所有的命令列在下面

```
$ ls                           <- Run this in root directory of our project
$ (public/) ls                 <- Run this in the 'public' directory
```

###1: 创建基本结构
创建一个项目叫做**myapp**在一个空白文件夹中。这个目录将会是项目的根目录。

```
$ mkdir myapp
$ cd myapp/
```
创建一个没有任何依赖关系的`package.json`，可以通过`npm init`。
```
{
"name": "myapp",
"version": "0.0.1",
"description": "My app",
"dependencies": { },
"devDependencies": { },
"author": "You"
}
```
创建一个`index.html`在该文件。这是真正显示在服务器的html。
```
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="utf-8">
    <title>Vue Example</title>
  </head>
  <body>
  	<h3>{{ message }}</h3>
    <script src="dist/build.js"></script>
  </body>
</html>
```
注意下面的两点：
- `dist/build.js`并不存在
- `{{message}}`的数据会被vue文件所填入

创建一个`src`文件夹和加上文件`src/main.js`:
```
import Vue from 'vue'

new Vue({
  el: 'body',
  data: {
  	message: "Hello Vue"
  }
})
```
这样我们就完成了一个关于vuejs骨架，但是它还需要丰富。

###2.基本webpack构建
创建一个`webpack.config.js`：
```
module.exports = {
  // 这是一个主文件包括其他模块
  entry: './src/main.js',
  // 编译的文件路径
  output: {
  	//`dist`文件夹
    path: './dist',
    // 文件 `build.js` 即 dist/build.js
    filename: 'build.js'
  },
  module: {
    // 一些特定的编译规则
    loaders: [
      {
        // 让webpack去验证文件是否是.js结尾将其转换
        test: /\.js$/,
        // 通过babel转换
        loader: 'babel',
        // 不用转换的node_modules文件夹
        exclude: /node_modules/
      }
    ]
  }
}
```
创建一个文件`.babelrc`。Babel是一个工具你可以转换ES6到现在的Javascript。Vue需要配置`es2015`和`stage-0`：
```
{
 "presets": ["es2015", "stage-0"],
 "plugins": ["transform-runtime"]
}
```
在命令行中安装webpack：
```
$ npm install -g webpack
```
安装本地库（作为`dev dependencies`），需要在`package.json`中添加`devDependencies`的部分。
```
"babel-core": "^6.1.2",
"babel-loader": "^6.1.0",
"babel-plugin-transform-runtime": "^6.1.2",
"babel-preset-es2015": "^6.1.2",
"babel-preset-stage-0": "^6.1.2",
"babel-runtime": "^5.8.0",
"webpack": "^1.12.2"
```
在保存后，运行：
```
$ npm install
```
注意我推荐用的是Vue初学者工具中最佳支持的版本，因为有时候最新的版本不能很好的支持。

最后，vuejs库安装到你的`dependencies`中。
```
$ npm install --save vue
```
如今你可以创建一个app用WebPack，运行
```
$ webpack
```
你可以看到输出文件：
```
Hash: 6568e32467dc12c8aeeb
Version: webpack 1.12.9
Time: 743ms
   Asset    Size  Chunks             Chunk Names
build.js  246 kB       0  [emitted]  main
    + 3 hidden modules
```
你打开`index.html`。如果你在浏览器上看到**Hello Vue**，那就做的非常好。恭喜，你基本完成基本的Vue项目。

###3.vue-loader和.vue文件
现在我们要去做vuejs最奇妙的部分，构建组件通过`.vue`文件。

保存你的`index.html`如这样。
```
<!DOCTYPE html>
<html lang="en">
 <head>
 <meta charset="utf-8">
 <title>Vue Example</title>
 </head>
 <body>
 <app></app>
 <script src="dist/build.js"></script>
 </body>
</html>
```

注意如今，用一个viewmodel替换根节点，用一个叫`app`的组件嫁接在`<app></app>`。

把你的main.js的代码改成:

```
import Vue from 'vue'
import App from './app.vue'

new Vue({
 el: 'body',
 components: { App }
})
```

现在，注意我通过`app.vue`取一个部件叫App，并且将模板镶嵌在body元素中。

我们会**创建一个文件`src/app.vue`**，并加上这些代码:

```
<template>
<div class="message">{{ msg }}</div>
</template>

<script>
export default {
 data () {
 return {
 msg: 'Hello from vue-loader!'
 }
 }
}
</script>

<style>
.message {
 color: blue;
}
</style>
```

在这个文件中，我们设置了style，定义了脚本的一些功能和定义了HTML的模板。如果你想知道它是怎么执行的，参考[vue文档](http://cn.vuejs.org/guide/)。

重新运行一下`webpack`，我们看到变化。
```
Hash: c71cc00f645706203ac4
Version: webpack 1.12.9
Time: 749ms
 Asset Size Chunks Chunk Names
build.js 246 kB 0 [emitted] main
 [3] ./src/app.vue 0 bytes [built] [failed]
 + 3 hidden modules

ERROR in ./src/app.vue
Module parse failed: /home/anirudh/work/misc/vue-scaffold/example/src/app.vue Line 1: Unexpected token <
You may need an appropriate loader to handle this file type.
| <template>
| <div class="message">{{ msg }}</div>
| </template>
 @ ./src/main.js 7:11-31
```
Webpack不懂得如何去处理`.vue`的新语法。修改你的webpack配置文件。
```
module.exports = {
 entry: './src/main.js',
 output: {
 path: './dist',
 publicPath: 'dist/',
 filename: 'build.js'
 },
 module: {
 loaders: [
 {
 test: /\.js$/,
 loader: 'babel',
 exclude: /node_modules/
 },
 {
 test: /\.vue$/,
 loader: 'vue'
 }
 ]
 },
 vue: {
 loaders: {
 js: 'babel'
 }
 }
}
```
现在你可以加上一些库到你的`package.json`在`devDependencies`:
```
"css-loader": "^0.23.0",
 "style-loader": "^0.13.0",
 "vue-loader": "^7.3.0",
 "vue-html-loader": "^1.0.0",
```

运行`npm install`获取新的库，然后，运行`webpack`。

```
Hash: 740a1d3c85161f03a0cf
Version: webpack 1.12.9
Time: 1355ms
 Asset Size Chunks Chunk Names
build.js 258 kB 0 [emitted] main
 + 11 hidden modules
```

当你打开浏览器，你可以看到标题"Hello from vue-loader"蓝色字。这就意味着你的样式和JS都编译成功。

###4.热模块替代/热更新
热模块替代或热更新是当今最热门新的技术。它让你保存 JavaScript文件，就把对应的组件实时更新。

原来的表现如下：
- 写一个App
- 在浏览器加载，并试用它
- App进入一个状态被Vue所渲染在屏幕

这时，你想要一个快速修改或修复一个bug。你需要重新加载页面，操作所有的步骤到那个指定状态。

热更新让整个步骤变得简单：

- 打开一个App，操作到指定状态
- 修改源代码并保存
- Webpack识别到代码变化，它重新编译被更改的指定模块
- Webpack利用类似websockets的技术上传代码和更改线上浏览器的效果
- Vue检测新的数据模型和热补丁, 和重新渲染app并保存着完整的状态

第一步我们需要用WebPack的`dev server`。 首先，修改你的`devDependencies`在`package.json`。
```
"vue-hot-reload-api": "^1.2.0",
```

运行一下代码
```
$ npm install
$ npm install -g webpack-dev-server
$ webpack-dev-server --inline --hot
```

当你用`webpack-dev-server`，你会看到一个很大的输出：

```
http://localhost:8080/
webpack result is served from /dist/
content is served from /home/anirudh/work/misc/vue-scaffold/example
Hash: ef5ed1df9062de968cb6
Version: webpack 1.12.9
Time: 1773ms
   Asset    Size  Chunks             Chunk Names
build.js  511 kB       0  [emitted]  main
chunk    {0} build.js (main) 465 kB [rendered]
    [0] multi main 52 bytes {0} [built]
    [2] (webpack)-dev-server/client?http://localhost:8080 2.48 kB {0} [built]
    [3] (webpack)/~/url/url.js 22.3 kB {0} [built]
    [... omitted for brevity ...]
   [85] ./~/vue-html-loader!./~/vue-loader/lib/selector.js?type=template&index=0!./src/app.vue 58 bytes {0} [built]
   [86] ./~/vue-hot-reload-api/index.js 5.62 kB {0} [built]
webpack: bundle is now VALID.
```

当你打开浏览器输入`http://localhost:8080`之后，你能看到一样的结果。但是不能真正的展示出Vue的热模块替换的牛逼之处。

接下来，你改你的`src/app.vue`:

```
<template>
<div class="message">Value is: {{ count }}</div>
<a href="#" @click.prevent="increment">Increment</a>
</template>

<script>
export default {
  data () {
    return {
      count: 0
    }
  },
  methods: {
    increment () {
      this.count ++;
    }
  }
}
</script>

<style>
</style>
```

刷新`http://localhost:8080`，你可以看到一个计数器和增加按钮。 你点击计数器就会增加。
现在你更新代码，改样式，改按钮名字或按钮的功能。你保存，组件就更新但是计数器的值还保持不变。

##接下来

这并不是你项目需要的所有，但是它是你需要构建Vue的全部。但是在你要开始写App前，你还要花时间去Google和创建：

###分开开发和产品的构建方式

你如果想独立为产品最小构建方式。你可以参考一下[vue-loader-example webpack config](https://github.com/vuejs/vue-loader-example/tree/master/build)。

注意，移动你的`webpack.config`至一个特定文件夹需要你在命令行特别声明。

###测试

测试其实超出了这教程的范围，测试取决于你App本身。 参考[vue-loader-example test example](https://github.com/vuejs/vue-loader-example/tree/master/test/unit)的代码。

###图片，静态文件，CSS

你需要自定义CSS和图片文件，webpack可以通过loader像`file-loader`，`url-loader`来优化开发流程。

###检查错误

你可以用`eslint-loader`配置`eslint`直接运行Webpack。[vue-loader-example](https://github.com/vuejs/vue-loader-example) 设置好`.eslintrc`文件。

转载，请表明出处。[总目录前段收集器](http://www.jianshu.com/p/c1e3b96c1293)
