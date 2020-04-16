# 在 React 工程内调用绝对路径

本文解决一个小问题，即如何在 React 工程内调用绝对路径。

## 必要性和问题

其实就是绝对路径的必要性，这个不用多说了，优点很多，尤其是工程规模稍微起来，连我这样的小博客都不想再 `'../../../redux/actions'` 这样调用了。但是很不巧的是，工程默认是无法直接调用绝对路径的，因为 React 并不知道你工程的根路径在哪，所以就需要额外的配置。

## 解决方法

来自 Stack Overflow 和博客的大神提供了很多方法，但有一些我没成功，有一些太麻烦了。幸运的是，最后我找到了来自 [官方文档](https://create-react-app.dev/docs/importing-a-component/#absolute-imports) 的解决办法：

在工程的根目录（注意不是 `/src`）新建 js 的配置文件 `jsconfig.json` 并添加如下配置：

```json
{
  "compilerOptions": {
    "baseUrl": "src"
  },
  "include": ["src"]
}
```

这样工程就知道你的根路径指向 `/src` 了，引用直接写成 `'redux/actions'`，非常舒服。

## 注意

绝对引用只在 `/src` 内生效。

正如 [另一篇博客](https://purple4pur.com/post/9) 里强调的，`/public` 和 `/src` 是不能互通的，因此在 `/public` 使用 `/src` 里的绝对路径没有意义。
