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

对于TypeScript，你可以使用 `'next'` 中的 `'GetStaticProps'` 类型：

```js
import { GetStaticProps } from 'next'

export const getStaticProps: GetStaticProps = async context => {
  // ...
}
```



##### 读取文件：使用 `'process.cwd()'`

在 `'getStaticProps'` 中你可以直接读取文件。

为此，你必须知道完整的文件路径。

由于 Next.js 会把你的代码编译到一个单独的目录下，所以你不能使用 `'__dirname'` ，因为他的路径和 pages 的路径并不一样。

你可以使用 `'process.cwd()'` ，它会给你 Next.js 的正确执行路径。

```js
import fs from 'fs'
import path from 'path'

// posts在构建时会通过getStaticProps()传递
function Blog({ posts }) {
  return (
    <ul>
      {posts.map(post => (
        <li>
          <h3>{post.filename}</h3>
          <p>{post.content}</p>
        </li>
      ))}
    </ul>
  )
}

// 这个方法将会在服务端构建时调用。
// 不会在客户端中被调用， 
// 所以你甚至可以直接在这里做一些数据库的查询。参见“技术细节”部分
export async function getStaticProps() {
  const postsDirectory = path.join(process.cwd(), 'posts')
  const filenames = fs.readdirSync(postsDirectory)

  const posts = filenames.map(filename => {
    const filePath = path.join(postsDirectory, filename)
    const fileContents = fs.readFileSync(fullPath, 'utf8')

    // 通常你会去解析或者转换这些内容
    // 例如你可以把markdown转换为HTML

    return {
      filename,
      content: fileContents,
    }
  })
  // 通过return { props: posts },
  // 组件会在构建时接收到posts
  return {
    props: {
      posts,
    },
  }
}

export default Blog
```



##### 技术细节



**只在构建时运行**

因为 `'getStaticProps'` 只在构建时运行，所以它在渲染 HTML 时不会接收那些在发送请求时才有的参数，例如查询参数，HTTP header。



**直接写服务端代码**

注意，`'getStaticProps'` 只会在服务端运行。它永远不会在客户端运行，所以它甚至并不包含在浏览器端的 JS 包中。那么你就可以直接数据库的查询操作。你不应该在 `'getStaticProps'` 中调用接口，而是直接编写服务端的代码。



**静态生成 HTML 和 JSON**

当一个带有 `'getStaticProps'`  的页面在构建中被渲染时，除了生成一个 HTML 页面文件之外，Next.js 还会生成一个带有 `'getStaticProps'`  运行结果的 JSON 文件。

 这个 JSON 文件将会被客户端路由 `'next/link'` ([文档]()) 或 `'next/router'` ([文档]()) 所使用。当你跳转到一个使用了 `'getStaticProps'`  的页面时，Next.js 会去获取这个 JSON 文件（在构建时生成）然后把它当作 props 传递给这个页面组件。 所以在客户端进行路由跳转时也不会去调用 `'getStaticProps'` ，只会使用导出的 JSON 文件。



**只能在 Page 中使用**

`'getStaticProps'`  只能在从 **page** 中导出，你不能在一个非 page 文件中导出（不能在子组件中使用）。

其中的一个原因是 React 在渲染组件之前必须获取到所有用到的数据。

而且，你必须使用 `'export async function getStaticProps() {}'` ， 如果你把  `getStaticProps` 写成一个 page 组件的属性并不会起作用。



**在开发环境中每次请求时都会运行**

在开发环境中（`'next dev'`）， `'getStaticProps'` 会在每次请求时被调用。



**Preview Mode**

在某些情况下，你可能希望暂时绕过Static Generation，并在 **请求时** 而不是在构建时渲染页面。例如，你可能正在使用 headless CMS，并希望在草稿发布之前对其进行预览。

这种情况下可以通过 Next.js 的 **Preview Mode** 功能实现。在 [**Preview Mode**]() 中查看更多内容。





### `'getStaticPaths'`(Static Generation)

如果一个页面使用了动态路由 [(文档)]() 和  `'getStaticProps'`， 那么就需要在构建时明确需要渲染 HTML 的路径 。

如果你 export 了一个叫做  `'getStaticPaths'` 的 `'async'` 函数，Next.js 将会静态预渲染有  `'getStaticPaths'` 指定的路径。

```js
export async function getStaticPaths() {
  return {
    paths: [
      { params: { ... } } // 参见下面的“paths”
    ],
    fallback: true or false // 参见下面的“fallback”
  };
}
```

  

##### `'paths'` （必填）

`'paths'` 决定了哪些路径将会被预渲染。举个例子，假设你有一个具有动态路由的页面叫做 `'pages/posts/[id].js'`，如果你从页面中 export `'getStaticPaths'` 并且返回了像下面这样的 `'paths'`：

```js
return {
  paths: [
    { params: { id: '1' } },
    { params: { id: '2' } }
  ],
  fallback: ...
}
```

 然后，Next.js 将使用`'pages/posts/[id].js'`中的页面组件在构建时生成`'posts/1'`和`'posts/2'`。

注意，每个参数的值必须与页面名称中使用的参数匹配：

* 如果页面名称是 `'pages/posts/[postId]/[commentId]'`，则 `'params'` 应包含 `'postId'` 和 `'commentId'`。
* 如果页面名称使用 catch-all 路由，例如 `'pages/[...slug]'`，则参数应包含slug，它是一个数组。 例如，如果此数组为 `'['foo', 'bar']'`，则Next.js将会为`'/foo/bar'` 生成页面。



##### `'fallback'` （必填）

`'getStaticPaths'` 返回的对象必须包含一个布尔类型的 `'fallbakc'`。



**`'fallbakc: false'`**

如果 `'fallback'` 值为 `'false'`，那么所有不在 `'getStaticPaths'` 返回值中的路由将会跳转到404页面。当你有少量的路由需要在构建时预渲染你就可以这样做，你不经常添加新页面时也是很有用的。如果你添加了大量的项目并且需要渲染新页面时，你就需要重新构建一次。

这里有一个预渲染每一篇博客文章的页面叫做 `'pages/posts/[id].js'`，文章列表将会通过 CMS 获取并由 `'getStaticPaths'` 返回。然后对于每篇文章的内容，将会通过 `'getStaticProps'` 从 CMS 获取。这个例子在 [页面](./pages.md) 中也出现过。

```js
/ pages/posts/[id].js
import fetch from 'node-fetch'

function Post({ post }) {
  // 渲染post...
}

// 这个方法将会在服务端构建时调用
export async function getStaticPaths() {
  // 调用接口获取博客列表
  const res = await fetch('https://.../posts')
  const posts = await res.json()

  // 得到我们想要渲染的路由
  const paths = posts.map(post => ({
    params: { id: post.id },
  }))

  // 我们在构建时只会渲染这些路由
  // { fallback: false } 其他路由将会返回404
  return { paths, fallback: false }
}

// 这个方法同样会在服务端构建时调用
export async function getStaticProps({ params }) {
  // params包含了文章`id`.
  // 如果路由是/posts/1,那么params.id 就是 1
  const res = await fetch(`https://.../posts/${params.id}`)
  const post = await res.json()

  // 通过props传递post
  return { props: { post } }
}

export default Post
```



**`'fallback: true'`**

如果 `'fallback'` 的值为 `'true'`，`'getStaticprops'` 的行为将会发生变化：

* 由 `'getStaticprops'` 返回的路由会在构建时生成 HTML。
* 不包含在 `'getStaticprops'` 返回的路由中的路由将不会跳转到404页面。在第一请求这样的路由时 Next.js 将会提供一个 ”fallback“ 版本。
* 在后端，Next.js 会生成请求路径的 HTML 和 JSON，包括调用 `'getStaticProps'`。
* 完成后，浏览器会接收到带有生成的路由的 JSON 文件，这会被用于自动渲染页面。从用户的角度来看，页面将从 ”fallback“ 页面切换到完整页面。
* 于此同时，Next.js 会把这个路径添加到之前预渲染页面的路径列表中去，之后对于这同一路径的请求就会想之前需渲染的页面一样了。

