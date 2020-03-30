# 命令式

`'next/link'` 应该能覆盖你的大部分的路由需求，但是你也可以在做客户端的页面跳转时不使用它，看一看 [Router API]()。

下面这个例子展示了 Router API 的基础用法：

```jsx
import Router from 'next/router'

function ReadMore() {
  return (
    <div>
      Click <span onClick={() => Router.push('/about')}>here</span> to read more
    </div>
  )
}

export default ReadMore
```

