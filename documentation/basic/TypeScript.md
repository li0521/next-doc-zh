# TypeScript

Next.js 提供了一种开箱即用的 `'TypeScript'` 体验，类似于一个 IDE。

首先，在你的项目中新建一个 `'tsconfig.json'` 文件：

```bash
touch tsconfig.json
```

Next.js 会自动配置这个文件的默认值，支持提供你自己的 `'tsconfig.json'` 配置以及定制编译选项。

> Next.js 使用 Babel 来处理 TypeScript，有一些 [注意事项](https://babeljs.io/docs/en/babel-plugin-transform-typescript#caveats) 和一些[编译器选项处理的不同](https://babeljs.io/docs/en/babel-plugin-transform-typescript#typescript-compiler-options)

然后，执行 `'next'`（通常是 `'npm run dev'`）Next.js 会指导你安装依赖包来完成设置：

```bash
npm run dev

# You'll see instructions like these:
#
# Please install typescript, @types/react, and @types/node by running:
#
#         yarn add --dev typescript @types/react @types/node
#
# ...
```

现在你可以开始把 `'.js'` 文件转换成 `'tsx'` 来享受 TypeScript 的优势了！

> 你的根目录中将会被创建一个 `'next-env.d.ts'` 文件，这个文件确保 TypeScript 编译器会选择 Next.js 的类型。你不能删除掉这个文件，但可以编辑它（但你不需要这么做）。

>Next.js 的 `'严格模式'` 在默认情况下是被禁用的。当您对 TypeScript 感到适应时，建议在 `'tsconfig.json'` 中打开它。

默认情况下，Next.js 会在开发过程中提示当前页面 TypeScript 错误，非当前页面的 TypeScript 错误不会阻碍开发的进程。

如果你想忽略掉这些报错，请参考 [忽略TypeScript报错]()。



### Static Generation 和 Server-side Rendering

对于 `'getStaticProps'`，`'getStaticPaths'` 和 `'getServerSideProps'`，你可以分别使用 `'GetStaticProps'`，`'GetStaticPaths'` 和 `'GetServerSideProps'` 类型：

```jsx
import { GetStaticProps, GetStaticPaths, GetServerSideProps } from 'next'

export const getStaticProps: GetStaticProps = async context => {
  // ...
}

export const getStaticPaths: GetStaticPaths = async () => {
  // ...
}

export const getServerSideProps: GetServerSideProps = async context => {
  // ...
}
```

> 如果你在使用 `'getInitialProps'` ，你可以按照这上面的[指导](https://nextjs.org/docs/api-reference/data-fetching/getInitialProps#typescript)。



### API 路由

下面是为API路由使用内置类型的示例：

```jsx
import { NextApiRequest, NextApiResponse } from 'next'

export default (req: NextApiRequest, res: NextApiResponse) => {
  res.status(200).json({ name: 'John Doe' })
}
```

你还可以定义响应数据的类型：

```jsx
import { NextApiRequest, NextApiResponse } from 'next'

type Data = {
  name: string
}

export default (req: NextApiRequest, res: NextApiResponse<Data>) => {
  res.status(200).json({ name: 'John Doe' })
}
```



### 自定义 `'App'`

如果你使用了 [`自定义'App'`]()，你可以使用内置的 `'AppProps'` 类型，像这样：

```jsx
import { AppProps } from 'next/app'

function MyApp({ Component, pageProps }: AppProps) {
  return <Component {...pageProps} />
}

export default MyApp
```

