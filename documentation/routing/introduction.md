# 路由

Next.js 有一个具有页面概念的文件系统路由。

在 `'pages'` 目录中添加文件时，将自动作为一个可访问的路由。

`'pages'` 目录中的文件可用于定义最常见的模式。



**Index routes**

路由系统会自动把目录下的 `'index'` 文件当作当前的根目录。

* `'pages/index.js'` -> `'/'`
* `pages/blog/index.js` -> `'/blog'`



**Nested routes**

路由系统支持嵌套文件，如果创建嵌套的文件夹结构，则文件将以相同的方式自动路由。

* `'pages/blog/first-post.js'` -> `'/blog/first-post'`
* `'pages/dashboard/setting/username.js'` -> `'/dashboard/setting/username'`



**Dynamic route segments**

为了匹配动态字段，你可以使用括号语法，这允许你去匹配参数。

* `'pages/blog/[slug].js'` -> `'/blog/:slug'` (`'/blog/hello-world'`)
* `'pages/[username]/settings.js'` -> `'/:username/settings'` (`'/foo/settings'`)
* `'pages/post/[...all].js'` -> `'/post/*'` (`'/post/2020/id/title'`)



### 链接两个页面

Next.js 允许你在客户端使用类似于单页应用的页面切换，提供了一个特殊的 React 组件 `'Link'` 来提供客户端的路由转换。

```jsx
import Link from 'next/link'

function Home() {
  return (
    <ul>
      <li>
        <Link href="/">
          <a>Home</a>
        </Link>
      </li>
      <li>
        <Link href="/about">
          <a>About Us</a>
        </Link>
      </li>
    </ul>
  )
}

export default Home
```

当你链接到一个具有动态路由字段的页面时，你必须提供 `'href'` 和 `'as'` 来让路由确定哪个文件将会被加载。

* `'href'` -`'pages'`目录中的文件名字，比如 `'/blog/[slug]'`。
* `'as'` -浏览器将要展示的url，比如 `'/blog/hello-world'`。

```jsx
import Link from 'next/link'

function Home() {
  return (
    <ul>
      <li>
        <Link href="/blog/[slug]" as="/blog/hello-world">
          <a>To Hello World Blog post</a>
        </Link>
      </li>
    </ul>
  )
}

export default Home
```



### 注入路由

为了在 React 中访问 `'route'` 对象，你可以使用 `'useRouter'` 或者 `'withRouter'`。

通常情况下推荐使用 `'useRouter'`。