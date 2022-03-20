## service-worker 最佳实践

`!!!发布到生产环境前，请务必充分调试，确保html是可以更新，不然可能会带来难以想象的灾难`

特点：

缓存静态资源，优先取缓存的，同时发起一次请求

用返回内容替换缓存，保证下次刷新 是最新的内容

第一时间，用缓存的资源返回浏览器，保证页面加载速度

## 注意

1、本地开发要使用 `localhost`，而不是127.0.0.1

因为 serviceWorker 的安全策略是 https, http下的 localhost也被认可

2、最好只在生产环境中启动 serviceWorker，避免本地开发 资源不及时更新 带来的 调试困难

```js
if(navigator.serviceWorker && location.origin === "https://yourhost.com"){
    // 注册逻辑代码
}
```

## 启动

```sh
npm i -g http-server
http-server ./
```

## 如何注销

将页面上注册逻辑 注释，改为 下面的 注销 代码即可

注销文件unsw.js 不需要内容，但是如果没有文件，浏览器会报错。本质上用sw.js也是可以的

## 总结

1、sw.js 最好注册在 根目录下，这样能控制所有请求，包括CDN的

```js
register('/sw.js') // /index.html 注册
```

以下请求受 serviceWorker 控制

```html
<script type="text/javascript" src="https://cdnjs.cloudflare.com/ajax/libs/echarts/4.8.0/echarts.min.js" ></script>
<link rel="stylesheet" href="/index.css" />
<script src="/public/index.js"></script>
```

2、如果sw.js没有注册到根目录下, 不能控制

```js
register('/public/sw.js') // /index.html 注册
```

以下请求均不受 serviceWorker 控制, 因为 serviceWorker 仅能控制 /public/ 下发出的请求

```html
<script type="text/javascript" src="https://cdnjs.cloudflare.com/ajax/libs/echarts/4.8.0/echarts.min.js" ></script>
<link rel="stylesheet" href="/index.css" />
<script src="/public/index.js"></script>
```

3、html文件在 public目录下

```js
register('/public/sw.js') // /public/index.html 注册
```

serviceWorker 能控制以下所有请求, 因为请求是从 /public/下的index.html发出的，和 sw.js 注册的scope /public/ 在同一scope下

```html
<script type="text/javascript" src="https://cdnjs.cloudflare.com/ajax/libs/echarts/4.8.0/echarts.min.js" ></script>
<link rel="stylesheet" href="/index.css" />
<script src="/public/index.js"></script>
```
