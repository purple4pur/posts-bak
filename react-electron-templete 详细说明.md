# react-electron-templete 详细说明

> [GitHub 链接](https://github.com/purple4pur/react-electron-templete) | 为了能更好地使用此模板，请仔细阅读此文。

## 为什么使用此模板

#### 以 React 为主

此模板服务的对象应该是了解 React 开发流程，但不了解 Electron 的开发者。使用者可以暂时忽略 Electron 的各种配置，而使用熟悉的流程 - 即开发 React 应用的流程，来完成一个简单桌面应用的开发。此模板完全以 React 为主，Electron 仅仅负责构建桌面应用，分工非常明确，即使完全没接触过 Electron，也能借助它搭建出理想的桌面 GUI。

#### 一次配置永久可用

由于此模板特殊的流程，在调试和构建桌面应用之间切换时，无需重复更改某些配置项，既简化了流程，也减少了犯错误的机会，可以说是一个简单可行且一劳永逸的取巧方案。这对于上面提到的目标使用者而言，无疑是非常有帮助的。

## 核心原理

模板的主体其实就是一个最简单的 React 应用，外面套了一层 Electron 的外衣。Electron 的入口由 `package.json` 的 `main` 这一项指向了根目录下的 `main.js`，`main.js` 再找到 `build/index.html` 来创建应用窗口。`build` 文件夹即 React 打包出来的完整项目，`build/index.html` 就是 React 项目的入口。所以流程就是 `package.json` 找到 Electron 的入口，Electron 再找到 React 的入口，最终实现了在窗口里打开 React 项目的效果。

## 脚本说明

模板一共预设了五个 NPM 脚本。其中 `start` 和 React 提供的 `start` 一致，可以打开默认浏览器进行调试。

`pview` 里面原本是 Electron 的调试命令，但由于 Electron 打开的是已经打包好的 React 应用，所以不会实时响应项目的更新，只能起到预览应用的作用。

`pack-win` 执行了 electron-packager 的打包命令，打包后可以得到适用于 Windows 的 32 位 .exe 和其他运行所需的文件，理论上生成的这个文件夹就是可以直接带着使用的版本了。考虑到模板的工作原理，这条脚本已经把无用的 `node_modules` / `src` / `public` / `package.lock` 四个文件或文件夹去掉了，打包时不会再占用空间。如需打包其他平台的版本，或者更改打包出来的应用名等，可以参照 electron-packager 的 [文档](https://github.com/electron/electron-packager#usage) 对脚本进行修改。

`prepview` 和 `prepack-win` 作为前两条脚本的前置命令，执行的就是 React 的 `build` 脚本，以此保证在预览和打包桌面应用时，操作对象是当前最新的版本。

## 开发和调试说明

#### 调试 UI 和数据

所有的调试都在浏览器中进行，和开发 React 应用时完全一致，最终在桌面应用里实现的效果，和调试时在浏览器看到的效果是一样的，因为都运行在浏览器内核上。

#### 窗口大小

`main.js` 中有代码明确地提到了 `width` 和 `height` 属性，这两个值决定了桌面窗口的宽高，单位是 px。后面还有两个限制，`resizable` 设为 `false` 限制了窗口无法拖拉改变大小，`useContentSize` 设为 `true` 可以让前面的宽高属性作用在窗口的内容区域，而不包括标题栏和菜单栏，这对于使用者根据内容大小来控制窗口大小是有利的。

此外，为了便于在开发调试阶段明确窗口的大小，`src/index.css` 里对 `body` 的宽高也进行了指定，并且设置了 `outline` 用作参考线，这样也尽可能地减少无法在窗口调试带来的负面影响。

基于以上两点，当需要更改窗口大小时，一定要同步更改 `main.js` 和 `src/index.css` 里的宽高，并且设置为对应一致的值。

## 优劣势分析

#### 优势

1. 对 React 开发者非常友好
2. 一次配置永久可用
3. 结构简单，后期拓展性强

#### 劣势

1. 无法在窗口中调试
2. 每次预览和打包前都要执行 React 的打包，增加等待时间
