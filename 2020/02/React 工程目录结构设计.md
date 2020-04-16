# React 工程目录结构设计

无论你的项目是通过 `create-react-app` 模板生成的，还是通过自己安装三个 React 的核心库 (`React`, `react-dom`, `react-scripts`) 得到的，项目都离不开两个核心的目录 - `public` 和 `src`。本文以此博客为例，简述一下一个 React 应用的工程目录可以怎样设计。

## `/public`

`public` 目录有三点需要注意：

1. 必须在根目录有一个 `index.html` 作为模板文件，否则会无法运行
2. 此目录下的文件在打包时会原封不动地复制到 `build` 的根目录中
3. `public` 和 `src` 不互通，不能访问到对方目录下的文件。（根据我目前的了解）

额外提一句我服务器上 apache 的配置。我在 `git push` 的时候是把整个工程目录一起推送过去的，apache 里设置网站的根目录为 `/path/to/git/build`。

依赖上述的三个特点和我的服务器设置，我的 `public` 目录中除了模板 html 外，还有我希望出现在网站根目录下的文件，目前包括：

1. 所有后端文件
2. 额外的 apache 配置 (`.htaccess`)

> 关于此处的 `.htaccess` 文件，我在后续的文章会解释其作用。

## `/src`

`src` 目录就是真正的工程目录了，先看一眼此博客的目录结构（仅一级文件夹）：

```
src
  ├─components
  ├─containers
  ├─redux
  ├─services
  └─views
```

`src` 目录下也有一个文件不能缺 - `index.js`，这是 React 的入口文件，缺少则无法运行。下面我分别具体说明各个目录放什么、怎么用。

### `./components` 和 `./containers`

这两个目录其实基本相同，里面就是各个组件。所谓 component 和 container 的区别只是概念上的。container 指作为容器的组件，本身只有框架的信息，等着数据填进来，比如文章列表、文章详情这些；而 compoennt 就是普通的组件了，比如 header 和 footer 这些，在调用的时候直接渲染就 ok 了。需不需要分开这两个目录看个人喜好（或者团队要求），我这个博客项目前面好长时间都没有分开，所有的组件都放在 `components` 里面了，也没啥问题。

在文件夹内部，管理各个组件也可以使用一个更规范的方式。首先，一个大组件使用一个单独的文件夹存放，（数量不多的）组件需要嵌套时，私有的小组件就可以直接放在父组件的文件夹里。比如 `<PostList />` 内部嵌套了一个 `<PostItem />`，目录可以如下：

```
containers
  └─PostList
        index.js // 暴露出 <PostList />
        PostItem.js // 暴露出 <PostItem />
```

因为 `conponents / containers` 下需要暴露出数个组件，因此在其根目录下使用一个 `index.js` 来统一暴露组件。

```js
// src/containers/index.js

export { default as PostList } from './PostList'
/**
 * 实际上是 export ... from './PostList/index.js'，
 * 但是 index.js 会作为默认的入口文件，因此可以省略
 */
```

如此，我们就只暴露出了 `<PostList />` 而没有 `<PostItem />`。当需要在其他文件调用 `<PostList />` 时，引入这样写：

```js
// src/index.js

import { PostList } from 'path/to/containers'
/**
 * 注意这里的括号，PostList 是从 containers 的众多 export 中单独解构出来的一个，
 * 因此需要引号。这与直接从 containers/PostList 引入是不同的，直接引入不用括号。
 */
```

按照这样的规范，当需要添加新的 container 组件时，有以下流程：

```js
新建 NewComp 文件夹并创建 index.js

--> 在 containers 的 index.js 中添加
    export { default as NewComp } from './NewComp'

--> 使用该组件时添加
    import { PostList, NewComp } from 'path/to/containers'
```

### `./views`

`views` 是配合路由使用的，主要放的是不同路由渲染的不同页面，也有项目命名这样的文件夹为 `pages`。在 Raect 里万物皆组件，所谓「页面」也不例外，就是个特别大的组件罢了。因此它的目录结构和前面组件目录的结构可以是一样一样的。细节暂按下不表。

### `./services`

此项目里的 `services` 只有一个 `index.js`，保存了前端沟通后端时需要的信息，包括后端页面、请求方式、headers 等等。细节暂按下不表。

### `./redux`

这个目录配合库 `redux` 使用。在我看的教程里面其实没有把 redux 相关的文件都放在一起，直接散养在根目录，但是这样做一方面我觉得有点乱，因为其实文件也不少，另一方面 redux 官方文档的 example 里都放在一起了，所以我也遵循了这样的方式。细节暂按下不表。

## 尾巴

以上是我在博客这个工程里目录的结构，自认为比较规整，也比较有参考性。相比于随意而凌乱的代码和结构，我相信还是整洁的工程更赏心悦目。希望本文能给你带来帮助。
