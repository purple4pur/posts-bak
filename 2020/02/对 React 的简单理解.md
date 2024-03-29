# 对 React 的简单理解

> 学一个月 React 能会点啥？

为了完成搭建动态博客的小目标，我自学了一个月时间的 React 架构，边学边练边做，目前已经独立地用 React 完成了博客的简单前端。这篇文章里我用自己的语言表达一下我所理解的 React，希望对还不了解，但希望学习 React 的你会有帮助。

## React 是什么，能做什么

有一说一我这个一个月前才开始学习 React 的人没啥资格回答这个问题，事实上我也的确不能准确描述，但我能在这里提一些关键词。

#### 前端

一个完整的 web 应用包括前端和后端，React 完成的是前端的工作，不包括任何的后端。事实上 web 应用开发的一个思想就是前后端分离，前端就干好前端的事，不要插手后端。

#### 三大主流前端框架之一

哪三大？Angular、React、Vue。以我在写这篇文章的了解，Angular 比较老牌；React 由 Facebook 推出，功能强大；Vue 还比较新，由国人推出，融合了不少优秀框架的优点，写起来也相对简单，但是作为大型 web 应用的框架可能就有点勉强了，在这方面还是用 React 的居多。

#### webpack

可能有的和我一样刚接触 React 的同学还不知道这个东西，不过不知道好像也不太影响学习就是了。React 是基于 webpack 的前端库大杂烩，虽然 React 已经帮我们包装好了，我们看不到里面的各种接口和 loader，但是 React 实现的每一个小功能，背后基本都是一个或几个库来完成的。稍微了解一下 webpack 的工作流程和配置能更好地理解 React 打包应用的过程。

#### 动态 web 应用

动态是相对于静态而言的，所谓静态就是整个应用的每一个页面，都对应着一个独立的 html 文件，比如 hugo 之类的框架生成的网页应用。而动态的 web 应用只有一个或几个 html 作为「模板文件」，其余的页面全部由对应的 js 在访问网页的时候动态生成 dom 节点，并插入到模板指定的位置上。静态应用访问速度快，因为只有跳转和浏览器解析 html 的过程。动态应用打开会慢些，因为涉及到很多 js 运算，整个页面都是由 js 从无到有现场施工的，但是功能上更多也更灵活。前面提到的三大前端框架都是做动态 web 应用的。

## React 核心概念

这里介绍 React 的三个核心概念，懂了这三个就已经能用 React 写一个简单的应用了。

### Component

中文「组件」，是 React 最最最核心的概念，一切都从「组件」开始。

设想手打一个购物网站的 html，我们需要有一个 header 作为网站导航栏，一个 footer 说明网站信息，中间用 ul 来展示各种商品。当点进一个商品详情，header 和 footer 不变，中间变成这个商品的信息。如果有十件不同的商品，在这个情况下我们就要出现十遍 header 和 footer 的代码，而最致命的是，不同的商品信息要出现在相同的展示框架里整整十遍，如果还有新商品加入，那就直接完蛋了，首页也要改，详情也要加，996 都不够用。

组件就像一个函数或者类一样（组件真的就是函数或类），把重复的代码段包裹起来，有需要就暴露出接口接收参数。在上面的例子中，header 和 footer 在哪儿都一样，那就写成两个组件，每个页面都渲染（执行函数）它们。中间的部分需要两个带接口的组件，一个展示商品列表，接收参数为十个商品名，一个展示商品详情，接收参数为商品的详细信息。这样一来整个开发工作就简单多了，整个购物网站就只用一个 html 作为「模板」，根据路由选择两种渲染方式之一：

方式一：

```js
// <Component /> 表示一个名为「Component」的组件

<Header />
<商品列表 接收参数：10 个商品名 />
<Footer />
```

方式二：

```js
<Header />
<商品详情 接收参数：一个商品的详细信息 />
<Footer />
```

要添加新的商品也容易了，只需要稍微改改参数就得，从此上班天天摸鱼，不仅混底薪，还能拿最多的奖金。美滋滋！

这就是组件的作用，一是简化重复代码，二是通过传参使页面的逻辑更简单。

### Props

Props 应该是 properties 的简写，也就是属性，在 React 里指传入组件的参数，比如上面例子的 `10 个商品名` 和 `一个商品的详细信息`。幸运的是，我们在使用 Props 的时候，不必再费心思设计怎样暴露接口和接收参数了，React 全都帮我们打包好了。

以类组件为例（函数组件差不多的，查一下就会了），我们通过

```js
<PrintText content="Hello world!" />
```

就可以把一个名为 `content` 值为字符串 `Hello world!` 的 prop 传入名为 `PrintText` 的组件。在类组件的内部通过

```js
this.props.content
```

就能访问到这个参数的值。

传入参数的过程看着像 html，但要记住这里所有的代码都是写在 js 里的（准确地说是 jsx），所以入参的类型就很灵活了，只要是 js 支持的类型都可以作为入参。在商品列表的例子里，一个长度为 10 的数组就很适合作为入参。

### State

字面意思，「状态」，指只暴露给组件内部的变量，跟类的 `private` 差不多。State 的作用就是保存组件内部的状态，比如一个输入框，就可以用 `value={this.state.inputValue}` 来控制输入框上显示的文本，也能随时把这个值拿去其他地方用。

Props 和 State 进行比较，有以下明显区别：

1. Props 从组件外传入，State 在组件内声明，在组件外无法访问
2. Props 无法改值，State 随便你怎么改
3. Props 可以赋值给 State 作为一个初始值（非受控组件），State 无法给 Props 赋值，这样的操作也是没有意义的

在实际的使用中，State 可以作为一个「数据集散中心」，比如通过

```js
constructor(props) {
  supur(props)

  this.state({
    username: 'Purple4pur',
    password: '123456',
    gender: 1,
    isAdmin: true
  })
}
```

来指定一个用户信息的初始状态，在后续的操作中进行更新，最后再统一把所有的数据一并提交到其他地方处理，比如后端。

## 尾巴

从零入手 React，最重要的就是理解 React 的功能和核心概念，不一定要马上都会用，但一定要理解整个 React 应用的逻辑。这个可以通过看一些教程来逐步理解，主要一定要跟着写一遍才有用，我不相信人人都是把教程从头看到尾，一提笔就是出来 Facebook 的天才。

React 的基础部分我在 b 站找的教程，是一个 up 上传的千锋教育的网课，[链接](https://www.bilibili.com/video/av75396447?p=1) 也贴在这里，有兴趣的朋友可以看看。我也没全看，基础刷完了就挑了几个重点来学。

后续我会继续分享我在开发中遇到的问题，以及我是如何解决的，大概可以参照我项目里的 [ISSUELOG](https://github.com/purple4pur/blog-with-cms/blob/master/ISSUELOG.md)。
