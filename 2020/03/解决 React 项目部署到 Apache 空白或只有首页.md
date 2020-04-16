# 解决 React 项目部署到 Apache 空白或只有首页

## 错误现象

React 项目搭建好了，本地调试非常满意，开开心心上传到服务器端的 Apache，结果在浏览器一看傻眼了，直接是空白页啥都没有，或者是只有首页显示了出来，其他项目内的页面打不开。

## 错误原因

React 打包出来的是一个动态网页应用，实际上有且仅有 `index.html` 这一个 html 文件（一般情况下）。Apache 在解析页面和子域名的时候，默认是去寻找对应名字的文件夹。假设根目录是 `/website`，主页地址 `www.domain.com`，子域名 `www.domain.com/page1`，则访问子域名时，Apache 会寻找 `/website/page1` 并进入入口文件。但问题是在 React 应用里，根本就没有 `/website/page1` 这个文件夹，所以页面无法正常打开。

## 解决方法

正确的访问方法是，把 `www.domain.com/page1` 的请求先指向主页，然后让 React 生成的 js 帮助我们解析地址，并生成正确的页面元素。所以实际上无论是什么 url 地址，所有的页面都是在 `index.html` 上渲染出来的。

具体操作如下：

1. 在 Apache 工作的根目录新建配置文件 `.htaccess`
2. 写入配置

   ```config
   Options -MultiViews
   RewriteEngine On
   RewriteCond %{REQUEST_FILENAME} !-f
   RewriteRule ^ index.html [QSA,L]
   ```

   这段配置的意义是开启重定向功能，并将所有页面重定向到根目录的 `index.html`。
3. （***可能是多余的设置，没有做过测试，但是这样做可以解决另一个玄学的问题***）在项目的 `package.json` 文件中添加一项

   ```json
   {
      "homepage": "/"
      // 或者 "homepage": "."，但这个不能解决那个玄学的问题，上面的可以
      // 额外提醒：package.json 中不能写注释
   }
   ```

## 其他注意

1. Apache 没有显示应该首先检查 Apache 配置，例如是否设置了正确的根目录等等
2. 考虑到 `/public` 内的文件会原封不动地复制到 `/build` 里，可以把 `.htaccess` 放在 `/public` 里，这样每次打包的时候就会自动出现在 `/build` 中，一般也正好 Apache 工作的根目录
3. 如果修改了 `package.json`，记得重新打包一次项目
