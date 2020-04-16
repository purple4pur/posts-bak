# 解决 React 网页报错 Uncaught SyntaxError: Unexpected token <

## 错误现象和原因

这个报错好像有点玄学，因为我遇到这个报错的时候反复确认过自己的代码，的确是没有问题的，React 这边也没有报错，可以正常执行调试，之前这么写也从来没有遇到问题，不知道怎么就突然不显示任何内容了，浏览器 console 显示错误 `Uncaught SyntaxError: Unexpected token <`。

Google 了一圈我也没整太明白，但根据大家的说法，大概猜想是浏览器没有正确处理 React 代码里 js 和 html 混写的部分，所谓 `Unexpected token <` 指的就是 html tag 两边的尖括号。

## 解决办法

最后在一条 GitHub Issue 上找到了对我有效的解决办法，看起来也颇为玄学（老爹说得对，要用魔法打败魔法），在之前的一篇文章 [解决 React 项目部署到 Apache 空白或只有首页](https://purple4pur.com/post/15) 里也提到了这个修改。

在项目的 `package.json` 文件中添加一项 `homepage` 并设为 `/`：

```json
{
  "homepage": "/"
}
```

另外进行一个不负责任的猜想，如果报错是因为没有正确处理 js 和 html 混写，那把项目里有混写的文件都指定后缀名为 `.jsx` 会不会解决问题呢，毕竟我的项目里后缀用的全是 `.js`。但打包都通过了，这个看起来就好像没啥道理了……
