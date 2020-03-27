# 静态文件服务

Next.js 在根目录下的 `'public'` 文件夹中提供像图片之类的静态文件服务，你可以在你的代码中通过（`'/'`）引入 `'public'` 目录下的文件。

比如说，你可以添加一张图片到 `'public/my-image.png'` ，然后通过如下的代码访问到这张图片：

```jsx
function MyImage() {
  return <img src="/my-image.png" alt="my image" />
}

export default MyImage
```

这个文件夹对 `'robot.txt'`，Google 网站验证等一些其他静态文件同样有效（包括 `'.html'`）!

> **注意**：不要把 `'public'` 文件夹改成其他任何名字，这是提供静态资源的唯一目录

> **注意**：确保没有与`'pages/'`目录中的文件同名的静态文件，否则将会出错。
>
> 了解更多：[http://err.sh/next.js/conflicting-public-file-page](http://err.sh/next.js/conflicting-public-file-page)

