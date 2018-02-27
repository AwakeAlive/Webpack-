# ES6
通常我们需要把采用 ES6 编写的代码转换成目前已经支持良好的 ES5 代码，这包含2件事：
1. 把新的ES6语法用ES5实现，例如ES6的`class`语法用ES5的`prototype`实现。
2. 给新的API注入`polyfill`，例如使用新的`fetch API`时注入对应的`polyfill`后才能让低端浏览器正常运行。

## Babel
Babel 是一个 JavaScript 编译器，能将 ES6 代码转为 ES5 代码
在 Babel 执行编译的过程中，会从项目根目录下的 `.babelrc` 文件读取配置。`.babelrc` 是一个 JSON 格式的文件，内容大致如下：
```js
{
  "plugins": [
    [
      "transform-runtime",
      {
        "polyfill": false
      }
    ]
   ],
  "presets": [
    [
      "es2015",
      {
        "modules": false
      }
    ],
    "stage-2",
    "react"
  ]
}
```
## Plugins
`plugins` 属性告诉 Babel 要使用哪些插件，插件可以控制如何转换代码。
以上配置文件里的 `transform-runtime` 对应的插件全名叫做 `babel-plugin-transform-runtime`，即在前面加上了 `babel-plugin-`，要让 Babel 正常运行我们必须先安装它：
```js
npm i -D babel-plugin-transform-runtime
```
`babel-plugin-transform-runtime` 是Babel官方提供的一个插件，作用是减少冗余代码。Babel在把ES6代码转换成ES5代码时通常需要一些ES5写的辅助函数来完成新语法的实现, 需要和`babel-runtime`配套使用，使用了`babel-plugin-transform-runtime`后一定需要`babel-runtime`。

## Presets
`presets` 属性告诉 Babel 要转换的源码使用了哪些新的语法特性，一个 Presets 对一组新语法特性提供支持，多个 Presets 可以叠加。 Presets 其实是一组 Plugins 的集合，每一个 Plugin 完成一个新语法的转换工作。Presets 是按照 ECMAScript 草案来组织的，通常可以分为以下三大类：
1. 已经被写入 ECMAScript 标准里的特性，由于之前每年都有新特性被加入到标准里，所以又可细分为：
* [es2015](https://babeljs.io/docs/plugins/preset-es2015/)包含在2015里加入的新特性
* [es2016](https://babeljs.io/docs/plugins/preset-es2016/)包含在2016里加入的新特性
* [es2017](https://babeljs.io/docs/plugins/preset-es2017/)包含在2017里加入的新特性
* [es2018](https://babeljs.io/docs/plugins/preset-es2018/)包含在2018里加入的新特性
* [env](https://babeljs.io/docs/plugins/preset-env/) 包含当前所有 ECMAScript 标准里的最新特性。
它们之间的关系图：

![es系列关系](./image/3-1presets-es.png)

2. 被社区提出来的但还未被写入 ECMAScript 标准里特性，这其中又分为以下四种：
* [stage0](https://babeljs.io/docs/plugins/preset-stage-0/) 只是一个美好激进的想法，有 Babel 插件实现了对这些特性的支持，但是不确定是否会被定为标准；
* [stage1](https://babeljs.io/docs/plugins/preset-stage-1/) 值得被纳入标准的特性；
* [stage2](https://babeljs.io/docs/plugins/preset-stage-2/) 该特性规范已经被起草，将会被纳入标准里；
* [stage3](https://babeljs.io/docs/plugins/preset-stage-3/) 该特性规范已经定稿，各大浏览器厂商和 Node.js 社区开始着手实现；
* stage4 在接下来的一年将会加入到标准里去。
它们之间的关系图：

![stage关系](./image/3-1presets-stage.png)

为了支持一些特定应用场景下的语法，和 ECMAScript 标准没有关系，例如 `babel-preset-react` 是为了支持 React 开发中的 JSX 语法。
在实际应用中，你需要根据项目源码所使用的语法去安装对应的 Plugins 或 Presets。
# 使用TypeScript语言
TypeScript是JavaScript的一个超集。[了解TypeScript webpack](http://webpack.wuhaolin.cn/3%E5%AE%9E%E6%88%98/3-2%E4%BD%BF%E7%94%A8TypeScript%E8%AF%AD%E8%A8%80.html)
# 使用Flow检查器
Flow是一个Facebook开源的JavaScript静态类型检查器，它是JavaScript语言的超集。
Flow 使用效果如下：
 ```js
 // @flow

// 静态类型检查
function square1(n: number): number {
  return n * n;
}
square1('2'); // Error: square1 需要传入 number 作为参数

// 类型推断检查
function square2(n) {
  return n * n; // Error: 传入的 string 类型不能做乘法运算
}
square2('2');
```
> 需要注意的时代码中的第一行 // @flow 告诉 Flow 检查器这个文件需要被检查。

## 使用 Flow
以上只是让你了解 Flow 的功能，下面教你如何运行 Flow 去检查代码。 Flow 检测器由高性能跨平台的 OCaml 语言编写，它的可执行文件可以通过
```js
npm i -D flow-bin
```
安装，安装完成后通过先配置 `Npm Script`
```js
"scripts": {
   "flow": "flow"
}
```
再通过 `npm run flow` 去调用 Flow 执行代码检查。

除此之外你还可以通过
```js
npm i -g flow-bin
```
把 Flow 安装到全局后，再直接通过 flow 命令去执行代码检查。

安装成功后，在项目根目录下执行 Flow 后，Flow 会遍历出所有需要检查的文件并对其进行检查，输出错误结果到控制台，例如：
```
Error: show.js:6
  6: export function show(content) {
                          ^^^^^^^ parameter `content`. Missing annotation

Found 1 error
```
采用了 Flow 静态类型语法的 JavaScript 是无法直接在目前已有的 JavaScript 引擎中运行的，要让代码可以运行需要把这些静态类型语法去掉。 例如：
```
// 采用 Flow 的源代码
function foo(one: any, two: number, three?): string {}

// 去掉静态类型语法后输出代码
function foo(one, two, three) {}
```
有两种方式可以做到这点：

* flow-remove-types 可单独使用，速度快。
* babel-preset-flow 与 Babel 集成。
## 集成 Webpack
由于使用了 Flow 项目一般都会使用 ES6 语法，所以把 Flow 集成到使用 Webpack 构建的项目里最方便的方法是借助 Babel， 下面来修改在3-1 使用 ES6 语言中使用的项目，为其加入 Flow 代码检查。 改动很少，如下：

1. 安装 `npm i -D babel-preset-flow `依赖到项目。
2. 修改 `.babelrc` 配置文件，加入 Flow Preset：
```js
"presets": [
...[],
"flow"
]
```
往源码里加入静态类型后重新构建项目，你会发现采用了 Flow 的源码还是能正常在浏览器中运行。
要明确构建的目的只是为了去除源码中的 Flow 静态类型语法，而代码检查和构建无关。 许多编辑器已经整合 Flow，可以实时在代码中高亮指出 Flow 检查出的问题。
添加.flowconfig文件
```js
[ignore]

[include]

[libs]

[lints]

[options]
```