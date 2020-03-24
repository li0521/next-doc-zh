# 数据获取

> *该文档适用于v9.3或更新的版本，如果你在使用旧版 Next.js 请查看 [nextjs.frontendx.cn](https://nextjs.frontendx.cn/)*

在 [页面](./pages.md) 的文档中，我们介绍了 Next.js 的两种预渲染方法：**Static Generation** 和 **Server-side Rendering**。在这篇内容里，我们将深入讨论每种情况下数据获取方式的选择策略。如果你还没有阅读过 [页面](./pages.md) 中的内容，建议先阅读一下。

我们讨论了三种 Next.js 中预渲染时获取外部数据的方法：

* `'getStaticProps'`(Static Generation)：在构建是获取数据。
* `'getStaticPaths'`(Static Generation)：根据数据指定要预渲染的路由。
* `'getServerSideProps'`(Server-side Rendering)：在每次请求是获取数据。

此外，我们还会介绍下如何在客户端中获取外部数据。



### `'getStaticProps'` (Static Generation)

如果你从页面中 export 了一个叫做 `'getStaticProps'` 的 `'async'` 函数，Next.js 将会在构建使用 `'getStaticProps'` 返回的 props 来渲染页面。

```js
export async function getStaticProps(context) {
  return {
    props: {}, // 将会通过props传递给页面组件
  }
}
```

参数 `'context'` 是一个包含下面这些属性的对象：

* `'params'` 包含使用动态路由的路由参数。例如， 如果页面的文件名为 `'[id].js'` , `'params'` 的值就会像这样 `'{id: ...}'` 。想要了解更多，请查看 [动态路由]()。在使用这个参数的同时，你应该同 `'getStaticPaths'` 一起使用，稍后我们会解释为什么要这样做。
* `'preview'` 的值为 `'true'` 表示这个页面处于预览模式，否则的话为 `'false'`。想了解更多，请查看 [预览模式]()。
* '`previewData`' 包含了通过 `'setPreviewData'`设置的预览数据。想了解更多，请查看 [预览模式]()。



##### 简单示例

这里是一个 [页面](./pages.md) 中介绍过的通过 `'getStaticProps'` 从 CMS (content management system)中获取博客文章列表的示例。

```js
import fetch from 'node-fetch'

// posts将会在构建时通过getStaticProps()获取到
function Blog({ posts }) {
  return (
    <ul>
      {posts.map(post => (
        <li>{post.title}</li>
      ))}
    </ul>
  )
}

// 这个方法将会在服务端构建时调用。
// 不会在客户端中被调用， 
// 所以你甚至可以直接在这里做一些数据库的查询。参见“技术细节”部分
export async function getStaticProps() {
  // 调用接口获取posts数据
  const res = await fetch('https://.../posts')
  const posts = await res.json()

  // 通过return { props: posts },
  // Blog组件会在构建时接收到posts
  return {
    props: {
      posts,
    },
  }
}

export default Blog
```



##### 什么时候应该使用 `'getStaticProps'` ?

在下面这些场景中你应该使用  `'getStaticProps'` ：

* 在用户发出请求之前可以获取到渲染页面的所需数据。
* 数据来自于 headless CMS。
* 数据可以公开缓存（不是特定于用户的）
* 网页对性能要求高，必须使用预渲染（为了 SEO ）—— `'getStaticProps'` 生成的 HTML 和 JSON 文件都可以被 CDN 缓存。



##### TypeScript: 使用 `'GetStaticProps'`



