# 页面

> *该文档适用于v9.3或更新的版本，如果你在使用旧版 Next.js 请查看 [nextjs.frontendx.cn](https://nextjs.frontendx.cn/)*

在 Next.js 中，页面是来自于 `'pages'` 目录下，`'.js'`，`'.jsx'`，`'.ts'`，`'.tsx'` 文件中导出的 React 组件，每个文件通过文件名和路由关联起来。

例如：如果你新建了一个 `'pages/about.js'` 文件，然后像下面一样导出一个 React 组件，就可以通过 `'/about'` 这个路由访问到这个页面。
```javascript
function About() {
  return <div>About</div>
}

export default About
```



### 使用动态路由的页面

Next.js 的页面支持动态路由。例如，如果你使用带 `'[]'` 的文件名创建了一个文件 `'pages/posts/[id].js'`，然后你可以通过 `'post/1'`，`'post/2'` 这些路由来访问到这个页面。

> 想了解更多有关动态路由的信息，请查看 [动态路由文档]()



### 预渲染

再默认情况下，Next.js 将会预渲染每个页面。这意味着 Next.js 会提前为每个页面生成 HTML 代码，而不是由客户端的 Javascript 生成。预渲染可以带来更好的性能和 SEO。



##### 两种预渲染的方式

Next.js 提供了两种方式进行预渲染：**Static Generation** 和 **Server-side Rendering**。这两种方式的区别是为页面生成 HTML 的时机。

* [**Static Generation (推荐)**]()：在打包编译时生成 HTML ，将会复用到每一个请求。
* [**Server-side Rendering**]()：在收到每个请求时生成 HTML 。

Next.js 允许你为每个页面选择你想要的预渲染的方式。你可以通过对大部分页面采用 Static Generation ，其余页面采用 Server-side Rendering 的方式来创建一个 “混合” Next.js 应用。

出于对性能的考虑，我们**推荐**使用 **Static Generation**。CDN 可以对静态生成的页面进行缓存以提升性能。但是，在某些情况下我们不得不使用 Server-side Rendering。

最后，你可以在使用 Static Generation 或 Server-side Rendering 的同时使用 **Client-side Rendering**。这意味着一部分的页面完全可以通过客户端的 Javascript 生成。想了解更多，请查看[数据获取]()。



### Static Generation（推荐）

如果页面采用了 **Static Generation** 的方式，页面的 HTML 将会在构建时生成。意味着在生产环境中，页面的 HTML 代码将在你运行 `'next build'` 时生成。生成的 HTML 将会复用到每一请求上，它们将会被 CDN 缓存下来。

在 Next.js 中，你可以静态生成一个带数据或者不带数据的页面，让我们看下下面的例子。



##### Static Generation（不需要外部数据）

默认情况下，Next.js 通过不需要外部数据的 Static Generation 预渲染页面。就像下面的例子。

```javascript
function About() {
  return <div>About</div>
}

export default About
```

注意到上面这个页面在预渲染时不需要去获取外部数据。Next.js 在这种情况下直接在构建时为每个这样的文件生成一个 HTML 文件。



##### Static Generation（需要外部数据）

一些页面需要在预渲染时需要去获取外部数据。这里有两个使用场景，在每个场景中你可以使用一些 Next.js 提供的函数。

1. 你的页面的内容依赖一些外部数据：使用 `'getStaticProps'`。
2. 你的页面路径依赖外部数据：使用 `'getStaticPaths'` （通常是除了 `'getStaticProps'` 的情况）。



**场景1：你的页面的内容依赖一些外部数据**

例如：你的博客页面需要通过CMS（content management system）去获取博客列表。

```javascript
// TODO: 在渲染这个页面之前需要获取 `posts` (通过一些API调用)
function Blog({ posts }) {
  return (
    <ul>
      {posts.map(post => (
        <li>{post.title}</li>
      ))}
    </ul>
  )
}

export default Blog
```

为了获取这些外部数据，Next.js 允许你在这个文件中 `'export'` 一个叫做 `'getStaticProps'` 的`'async'`函数。这个函数将会在构建时调用，然后让你在预渲染时通过页面的 `'props'` 来传递获取到的外部数据。

```javascript
// 你可以用任意一个数据请求库
import fetch from 'node-fetch'

function Blog({ posts }) {
  // 渲染posts...
}

// 这个函数将会在构建时调用
export async function getStaticProps() {
  // 请求接口来获取posts
  const res = await fetch('https://.../posts')
  const posts = await res.json()

  // 返回 { props: posts }, 
  // Blog组件在构建时通过prop的方式获取posts
  return {
    props: {
      posts,
    },
  }
}

export default Blog
```

想了解更多关于 `'getStaticProps'` 的细节，请查看[数据获取]()。



**场景2：你的页面路径依赖外部数据**

Next.js 允许你创建一个具有**动态路由**的页面。例如：你可以创建一个叫做  `'pages/posts/[id].js'` 的页面通过  `'id'` 去展示一篇文章。 当你访问 `'posts/1'` 时，为你展示 id 为1的文章。

> 想了解更多有关动态路由的信息，请查看 [动态路由文档]()

然而，在预渲染时这个 `'id'` 依赖于外部的数据。

**例如**：假设你在数据库中只添加了一篇 id 为1的文章。这种情况下，你只想在构建时渲染 `'posts/1'` 。

之后你在数据库中添加了第二篇 id 为2的文章，你也想渲染出 `'posts/2'`的页面。

所以在预渲染时你的页面路径依赖与外部的数据。为了处理这种情况，Next.js 允许你在这个动态页面中 `'export'` 一个叫做 `'getStaticPaths'` 的`'async'`函数（在这个例子中是 `'pages/posts/[id].js'`）。这个函数将在构建时调用然后指定你想要渲染的路径。

```javascript
import fetch from 'node-fetch'

// 这个函数将在构建时调用
export async function getStaticPaths() {
  // 请求接口来获取posts
  const res = await fetch('https://.../posts')
  const posts = await res.json()

  // 通过posts生成想要渲染的路径
  const paths = posts.map(post => `/posts/${post.id}`)

  // 我们在构建时只会渲染这些路径
  // { fallback: false } 代表其他路径应该返回404
  return { paths, fallback: false }
}
```

在 `'pages/posts/[id].js'` 中，为了通过 id 获取文章信息你仍然需要export `'getStaticProps'`：

```js
import fetch from 'node-fetch'

function Post({ post }) {
  // 渲染post...
}

export async function getStaticPaths() {
  // ...
}

// 同样在构建时调用
export async function getStaticProps({ params }) {
  // 参数包含文章`id`.
  // 如果页面路由为/posts/1, params.id 就为 1
  const res = await fetch(`https://.../posts/${params.id}`)
  const post = await res.json()

  // 通过props传递post
  return { props: { post } }
}

export default Post
```

想了解更多关于 `'getStaticPaths'` 的细节，请查看[数据获取]()。



##### 什么时候应该用 Static Generation？

我们推荐你尽可能使用 **Static Generation**（需要或者不需要外部数据），因为这样可以构建一次然后通过 CDN 缓存，这样速度会比通过服务器去渲染每个请求的页面快很多。

你可以为很多类型的页面使用 Static Generation，包括：

* 营销活动页
* 博客文章
* 电商产品列表
* 帮助和说明文档

你可以这样问你自己：“我可以在用户请求之前对这个页面进行渲染吗？”，如果你的答案时肯定的，那么你就应该选择使用 Static Generation。

另一方面，如果你不能在用户请求之前渲染页面，那么就不适合使用 Static Generation。你的页面可能需要频繁的刷新数据，每个页面的内容可能也会随着请求的不同而产生变化。

在这种情况下，你可以选择使用下面的两种方式：

* 使用 Static Generation 的同时使用 **Client-side Rendering**：你可以选择跳过预渲染那些需要使用客户端 Javascript 渲染的部分。想了解更多关于此方法的信息，请查看[数据获取]()。
* 使用 **Server-Side Rendering**：Next.js 将会为每个请求去渲染页面。因为不能使用 CDN 进行缓存，所以将会变得慢一些。但每个渲染出来的页面的内容总会是最新的，接下来我们会讨论这种方法。



###  Server-side Rendering

> 也叫做 “SSR” 或者 “动态渲染”

如果一个页面使用 **Server-side Rendering**，那么这个页面的 HTML 将会通过每个请求生成。

为了使用 Server-side Rendering，你需要 `export` 一个叫做 `getServerSideProps` 的  `async` 函数。这个函数将会在每次请求时调用。

例如，你页面需要频繁的更新数据（通过请求接口），你可以在  `getServerSideProps` 中请求这些数据然后像下面这样传递给页面。

```js
import fetch from 'node-fetch'

function Page({ data }) {
  // 渲染数据...
}

// 将会在每次请求时调用
export async function getServerSideProps() {
  // 通过接口请求数据
  const res = await fetch(`https://.../data`)
  const data = await res.json()

  // 通过props传递数据
  return { props: { data } }
}

export default Page
```

你可以看到，`'getServerSideProps'` 的使用方法类似于 `'getStaticProps'`，但不同之处在于 `'getServerSideProps'` 会在每次请求时运行而不是在构建时。

想了解更多关于 `'getServerSideProps'` 的细节，请查看[数据获取]()。



### 总结

我们已经讨论了 Next.js 的两种预渲染方式。

* **Static Generation (推荐)**：HTML 在构建时生成并复用到每个请求。想要页面使用 tatic Generation，需要 export 这个页面组件或者 export  `'getStaticProps'` （如果有需要，还有 `'getStaticPaths'`）。你还可以和 Client-side Rendering 一起使用，去引入那些外部的数据。
* **Server-side Rendering**：HTML 通过每个请求来生成。想要页面使用 Server-side Rendering， export  `'getServerSideProps'` 。因为 Server-side Rendering 性能不如 Static Generation，所以请在必要情况下使用。

