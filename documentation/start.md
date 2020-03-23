# 开始

*如果你刚开始学习 Next.js，建议首先从 [learn course](https://nextjs.org/learn/basics/getting-started) 开始。*



**系统要求**

- Node.js 10 或者更高
- MacOS, Windows (包括 WSL), 或者 Linux



**构建**

官方推荐使用  `'create-next-app'` 去创建一个新项目，它会自动帮你做好一些设置。可以通过以下命令创建：

```bash
#npm 
npm init next-app
#yarn
yarn create next-app
```

等待安装程序运行完毕后，按照下面的说明启动开发模式，尝试修改 `'pages/index.js'` 然后在浏览器上查看结果。



**手动构建**

首先在你的项目中安装 `'next'`, `'react'` 和 `'react-dom'`：

```bash
#npm 
npm install next react react-dom
#yarn
yarn add next react react-dom
```

打开 `'package.json'` 然后添加以下 `'scripts'`：

```json
"scripts": {
  "dev": "next",
  "build": "next build",
  "start": "next start"
}
```

这些脚本是用于应用开发的不同阶段：

- `'dev'` - 运行 `'next'` 开启开发模式
- `'build'` - 运行 `'next build'` 打包用于生产环境的应用
- `'start'` - 运行 `'next start'` 启动生产模式

Next.js 是基于页面的概念，页面是来自于 `'pages'` 目录下，`'.js'`，`'.jsx'`，`'.ts'`，`'.tsx'` 文件中导出的 React 组件。

页面通过这些文件的文件名与路由联系起来。例如 `'pages/about.js'` 映射到 `'/about'` 这个路由。你也可以使用文件名来创建一个动态路由。

在你的项目中创建一个 `'pages'` 目录。

在 `'pages'` 目录中添加一个 `'index.js'` 文件，内容如下：

```javascript
function HomePage() {
  return <div>Welcome to Next.js!</div>
}

export default HomePage
```

运行 `'npm run dev'` 将会启动一个开发服务器，你可以通过 `'http://localhost:3000'` 访问。



现在，我们得到了这样一个应用：

- 自动编译和打包（基于Webpack和Babel）
- 代码热更新
-  `'pages'` 目录下页面的服务端渲染
- 静态文件服务. `'./public/'` 映射到 `'/'`

