# 内置 CSS 支持

Next.js 允许你在 JavaScript 文件中导入 CSS 文件，因为 Next.js 将 `'import'` 的概念扩展到了 JavaScript 之外。



### 添加全局样式

为了在你的应用中添加全局样式，请在 `'pages/_app.js'` 中导入 CSS 文件。

例如，现在有个一叫做 `'styles.css'` 的样式文件：

```css
body {
  font-family: 'SF Pro Text', 'SF Pro Icons', 'Helvetica Neue', 'Helvetica',
    'Arial', sans-serif;
  padding: 20px 20px 60px;
  max-width: 680px;
  margin: 0 auto;
}
```

如果不存在 `'pages/_app.js'` 的话先创建一个，然后引入这个 `'styles.css'`。

```css
import '../styles.css'

// This default export is required in a new `pages/_app.js` file.
export default function MyApp({ Component, pageProps }) {
  return <Component {...pageProps} />
}
```

这些样式（`'style.css'`）将会在你的应用中的所有页面和组件中生效。因为这些样式具有全局性并且为了避免冲突，你只能在 `'pages/_app.js'` 中引入这些样式。

在开发环境中，以这种方式引入可让你在编辑样式时对其样式进行热更新，并且可以保持应用程序的状态。

在生产环境中，所有的 CSS 文件都会把压缩到一个 `'.css'` 文件中。



### 为组件添加样式

Next.js 使用 `'[name].module.css'` 命名规则来支持 CSS Modules。

CSS Modules 通过自动创建唯一的类名来局部作用于CSS，所以这允许你在不同的文件中使用相同的CSS类名，而不必担心冲突。

这使得 CSS Modules 成为支持组件级别样式的理想方式，CSS Modules 能在你的应用中的任何地方被引用。

举个例子，假设在你的 `'components/'` 目录下有一个 `'Button'` 组件：

首先，创建一个如下内容的 `'components/Button.module.css'` 样式文件：

```css
/*
你不用担心类名是否会于其他`.css`或`.module.css`文件中的类名产生冲突
*/
.error {
  color: white;
  background-color: red;
}
```

然后创建一个 `'components/Button.js'` 引入上面的 CSS 文件：

```jsx
import styles from './Button.module.css'

export function Button() {
  return (
    <button
      type="button"
      // 注意"error"类是如何在被导入的"style"的对象中作为属性访问的
      className={styles.error}
    >
      Destroy
    </button>
  )
}
```

CSS Modules 是一个可选的功能，只有在扩展名为 `'.module.css'` 的文件中启用，常规的 `'<link>'` 样式表和全局 CSS 文件任然被支持。

在生产环境中，所有的 CSS Module 文件会被压缩打包到不同的 `'.css'` 文件中，这些`'.css'`文件代表应用程序中的热执行路径，确保为应用程序渲染所需的样式文件加载量最小。



### CSS-in-JS

可以使用任何现有的CSS-in-JS解决方案。最简单的是内联样式：

```jsx
function HiThere() {
  return <p style={{ color: 'red' }}>hi there</p>
}

export default HiThere
```

我们提供了 styled -jsx 来支持独立作用域的CSS。目的是为了支持类似于 Web组件 的 “shadow CSS”，但遗憾的是，这些组件不支持服务端渲染，仅支持js。

使用 `'styled-jsx'` 的组件看起来是这样的：

```jsx
function HelloWorld() {
  return (
    <div>
      Hello world
      <p>scoped!</p>
      <style jsx>{`
        p {
          color: blue;
        }
        div {
          background: red;
        }
        @media (max-width: 600px) {
          div {
            background: blue;
          }
        }
      `}</style>
      <style global jsx>{`
        body {
          background: black;
        }
      `}</style>
    </div>
  )
}

export default HelloWorld
```

请查看 [styled-jsx](https://github.com/zeit/styled-jsx) 文档获取更多示例。



### 支持 Sass

Next.js 支持你使用 `'.scss'`  和 `'sass'` 扩展来引入 Sass 文件，你可以通过 `'.module.scss'` 或者 `'.module.sass'` 扩展来使用组件级别的 Sass。

在使用 Next.js 内置的 Sass 之前，请确保你安装了 `'sass'`。

```bash
npm install sass
```

Next.js 对 Sass 的支持具有与上面详细介绍的 CSS 支持相同的优点和限制。



### 支持 Less 和 Stylus

为了支持导入 `'.less'` 或者 `'.styl'` 文件，你可以使用一下插件：

* [@zeit/next-less](https://github.com/zeit/next-plugins/tree/master/packages/next-less)
* [@zeit/next-stylus](https://github.com/zeit/next-plugins/tree/master/packages/next-stylus)

