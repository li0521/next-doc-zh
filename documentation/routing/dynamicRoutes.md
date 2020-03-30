# 动态路由

对于一个复杂的应用来说使用预定义的路径来定义路由是远远不够的，在 Next.js 中你可以使用方括号（`'[param]'`）来创建动态路由。

考虑下下面这个页面 `'pages/post/[pid].js'` ：

```jsx
import { useRouter } from 'next/router'

const Post = () => {
  const router = useRouter()
  const { pid } = router.query

  return <p>Post: {pid}</p>
}

export default Post
```

类似于 `'/post/1'`，`'/post/abc'` 这样的路由都会匹配到 `'pages/post/[pid].js'`。匹配的路径参数将作为查询参数发送到页面，并且会和其他查询参数合并在一起。

例如，`'post/abc'` 的 `'query'` 参数为下面这样：

```jsx
{ "pid": "abc" }
```

同样的，`'post/abc?foo=bar'` 的 `'query'` 参数如下：

```js
{ "foo": "bar", "pid": "abc" }
```

但是，如果路由参数和查询参数拥有同样的名字，路由参数将会覆盖查询参数。例如，`'/post/abc?pid=123'` 的 `'query'` 参数如下：

```js
{ "pid": "abc" }
```

多个动态路由字段也是一样，页面 `'/pages/post/[pid]/[comment].js'` 将会匹配到路由 `'post/abc/a-comment'`，`'query'` 参数如下：

```js
{ "pid": "abc", "comment": "a-comment" }
```

客户端跳转到动态路由页面将会被 `'next/link'` 处理。



**贪婪路由**

可以通过添加三个点（`'...'`）让动态路由进行贪婪匹配，举个例子：

- `'pages/post/[...slug].js'` 会匹配到 `'/post/a'`，同样也会匹配到 `'/post/a/b'` 和 `'/post/a/b/c'`。

> 注意：你可以使用其他名字，例如 `'[...params]'`

被匹配到参数将会被当作一个参数（这个例子中是 `'slug'`）传递给页面，并且这个参数会是一个数组，所以路由 `'/post/a'` 的参数如下：

```js
{ "slug": ["a"] }
```

对于另一个例子 `'/post/a/b'`，以及其他被匹配到的任何路由，参数将会被添加到数组里面，像这样：

```js
{ "slug": ["a", "b"] }
```

> 贪婪路由一个很好的例子就是 [Next.js 文档](https://nextjs.org/docs/getting-started)，一个叫做 `'pages/docs/[...slug].js'` 的页面来处理所有的文档。



### 警告

- 预定义路由的优先级高于动态路由，动态路由优先级高于贪婪路由。看下下面的例子：

  - `'pages/post/create.js'` -匹配到 `'/post/create'`
  - `'pages/post/[pid].js'` -匹配到 `'/post/1'`，`'/post/abc'` 但不会匹配到 `'/post/create'`
  - `'pages/post/[...slug].js'` -匹配到 `'/post/1/2'`，`'/post/a/b/c'` 但不会匹配到 `'/post/create'` 和 `'/post/abc'`

- 通过自动静态优化进行静态优化的页面在没提供路由参数是，`'query'` 将会为空 (`'{}'`)。

  之后，Next.js将触发你的应用程序更新，在 `'query'` 对象中提供路由参数。