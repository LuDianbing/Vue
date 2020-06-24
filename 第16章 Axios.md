参考：

- [Axios 中文文档](https://www.kancloud.cn/yunye/axios/234845)
- [DATA API](http://www.wwtliu.com/sxtstu/)

# 一、概述

Axios 是一个基于 promise 的 HTTP 库，可以用在浏览器和 node.js 中。

**\> 特性：**

- 从浏览器中创建 [XMLHttpRequests](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest)
- 从 node.js 创建 [http](http://nodejs.org/api/http.html) 请求
- 支持 [Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise) API
- 拦截请求和响应
- 转换请求数据和响应数据
- 取消请求
- 自动转换 JSON 数据
- 客户端支持防御 [XSRF](http://en.wikipedia.org/wiki/Cross-site_request_forgery)

# 二、安装 *

```shell
# NPM
$ npm install axios
```

# 三、引入	 *

```js
// 1. 通过Webpack plugins全局导入
new webpack.ProvidePlugin({
  Axios: 'axios',
});
// 2. ES6
import Axios from 'axios';
// 3. commonJS
const Axios = require('axios');
```

导入之后，将axios挂在到Vue的实例上：

```js
Vue.prototype.$axios = Axios
```

> 提示：
>
> - axios 请求一般在 **create** 生命周期钩子上进行。

# 四、示例

API 参考：<https://www.showapi.com/>

## 1. GET *

```javascript
// => 直接将请求参数拼接在资源地址后
const url = "http://10.2.1.2:8081/phones?pages=1&size=8";
this.$axios.get(url).then(res => {
  // 请求成功
  console.log(res);
}).catch(err => {
  // 请求失败
  console.log(err.message);
})
// => 通过params配置请求参数
const url = "http://10.2.1.2:8081/phones";
const params = {
  pages: 1,
  size: 8
};
this.$axios.get(url, {
  params
}).then(res => {
  // 请求成功
  console.log(res);
}).catch(err => {
  // 请求失败
  console.log(err.message);
})
```

## 2. POST *

```javascript
this.$axios.post(url, {
  // post 参数直接在第2个参数中以key-value对形式设置
})
.then(res => {
  console.log(res)
})
.catch(error => {
  console.log(error);
})
```

## 3. 并发请求

处理并发请求的助手函数

##### axios.all(iterable)

##### axios.spread(callback)

```js
function getOrders() {
  return axios.get('/orders');
}
function getArticles() {
  return axios.get('/articles/');
}

axios.all([getOrders(), getArticles()])
  .then(axios.spread(function (orders, articles) {
    // 两个请求现在都执行完成
  }));
```

# 五、参数传输格式

参数传输格式主要有两种：

- form-data：?pages=1&size=8

- x-www-form-urlencode：{pages:1, size=8}

如果要将  x-www-form-urlencode 转换为 form-data 格式操作如下：

\1.  -> 首先你需要引入 **qs** 库，qs库无需安装，直接引入即可：

```js
import Qs from 'qs';
```

\2. -> 在使用 axios.post 请求时使用 Qs.stringify() 方法包裹传递参数即可进行转换：

```js
this.$axios.post('url',Qs.stringify({
	// 请求参数key-value对
}))
.then(res => {
	console.log(res);
}).catch(error => {
	console.log(error);
})
```

# 六、请求配置

这些是创建请求时可以用的配置选项。只有 `url` 是必需的。如果没有指定 `method`，请求将默认使用 `get` 方法。

```js
{
  // `url` 是用于请求的服务器 URL
  url: '/user',

  // `method` 是创建请求时使用的方法
  method: 'get', // 默认是 get

  // `baseURL` 将自动加在 `url` 前面，除非 `url` 是一个绝对 URL。
  // 它可以通过设置一个 `baseURL` 便于为 axios 实例的方法传递相对 URL
  baseURL: 'https://some-domain.com/api/',

  // `transformRequest` 允许在向服务器发送前，修改请求数据
  // 只能用在 'PUT', 'POST' 和 'PATCH' 这几个请求方法
  // 后面数组中的函数必须返回一个字符串，或 ArrayBuffer，或 Stream
  transformRequest: [function (data) {
    // 对 data 进行任意转换处理

    return data;
  }],

  // `transformResponse` 在传递给 then/catch 前，允许修改响应数据
  transformResponse: [function (data) {
    // 对 data 进行任意转换处理

    return data;
  }],

  // `headers` 是即将被发送的自定义请求头
  headers: {'X-Requested-With': 'XMLHttpRequest'},

  // `params` 是即将与请求一起发送的 URL 参数
  // 必须是一个无格式对象(plain object)或 URLSearchParams 对象
  params: {
    ID: 12345
  },

  // `paramsSerializer` 是一个负责 `params` 序列化的函数
  // (e.g. https://www.npmjs.com/package/qs, http://api.jquery.com/jquery.param/)
  paramsSerializer: function(params) {
    return Qs.stringify(params, {arrayFormat: 'brackets'})
  },

  // `data` 是作为请求主体被发送的数据
  // 只适用于这些请求方法 'PUT', 'POST', 和 'PATCH'
  // 在没有设置 `transformRequest` 时，必须是以下类型之一：
  // - string, plain object, ArrayBuffer, ArrayBufferView, URLSearchParams
  // - 浏览器专属：FormData, File, Blob
  // - Node 专属： Stream
  data: {
    firstName: 'Fred'
  },

  // `timeout` 指定请求超时的毫秒数(0 表示无超时时间)
  // 如果请求话费了超过 `timeout` 的时间，请求将被中断
  timeout: 1000,

  // `withCredentials` 表示跨域请求时是否需要使用凭证
  withCredentials: false, // 默认的

  // `adapter` 允许自定义处理请求，以使测试更轻松
  // 返回一个 promise 并应用一个有效的响应 (查阅 [response docs](#response-api)).
  adapter: function (config) {
    /* ... */
  },

  // `auth` 表示应该使用 HTTP 基础验证，并提供凭据
  // 这将设置一个 `Authorization` 头，覆写掉现有的任意使用 `headers` 设置的自定义 `Authorization`头
  auth: {
    username: 'janedoe',
    password: 's00pers3cret'
  },

  // `responseType` 表示服务器响应的数据类型，可以是 'arraybuffer', 'blob', 'document', 'json', 'text', 'stream'
  responseType: 'json', // 默认的

  // `xsrfCookieName` 是用作 xsrf token 的值的cookie的名称
  xsrfCookieName: 'XSRF-TOKEN', // default

  // `xsrfHeaderName` 是承载 xsrf token 的值的 HTTP 头的名称
  xsrfHeaderName: 'X-XSRF-TOKEN', // 默认的

  // `onUploadProgress` 允许为上传处理进度事件
  onUploadProgress: function (progressEvent) {
    // 对原生进度事件的处理
  },

  // `onDownloadProgress` 允许为下载处理进度事件
  onDownloadProgress: function (progressEvent) {
    // 对原生进度事件的处理
  },

  // `maxContentLength` 定义允许的响应内容的最大尺寸
  maxContentLength: 2000,

  // `validateStatus` 定义对于给定的HTTP 响应状态码是 resolve 或 reject  promise 。如果 `validateStatus` 返回 `true` (或者设置为 `null` 或 `undefined`)，promise 将被 resolve; 否则，promise 将被 rejecte
  validateStatus: function (status) {
    return status >= 200 && status < 300; // 默认的
  },

  // `maxRedirects` 定义在 node.js 中 follow 的最大重定向数目
  // 如果设置为0，将不会 follow 任何重定向
  maxRedirects: 5, // 默认的

  // `httpAgent` 和 `httpsAgent` 分别在 node.js 中用于定义在执行 http 和 https 时使用的自定义代理。允许像这样配置选项：
  // `keepAlive` 默认没有启用
  httpAgent: new http.Agent({ keepAlive: true }),
  httpsAgent: new https.Agent({ keepAlive: true }),

  // 'proxy' 定义代理服务器的主机名称和端口
  // `auth` 表示 HTTP 基础验证应当用于连接代理，并提供凭据
  // 这将会设置一个 `Proxy-Authorization` 头，覆写掉已有的通过使用 `header` 设置的自定义 `Proxy-Authorization` 头。
  proxy: {
    host: '127.0.0.1',
    port: 9000,
    auth: : {
      username: 'mikeymike',
      password: 'rapunz3l'
    }
  },

  // `cancelToken` 指定用于取消请求的 cancel token
  // （查看后面的 Cancellation 这节了解更多）
  cancelToken: new CancelToken(function (cancel) {
  })
}
```

响应结构

某个请求的响应包含以下信息

```js
{
  // `data` 由服务器提供的响应
  data: {},

  // `status` 来自服务器响应的 HTTP 状态码
  status: 200,

  // `statusText` 来自服务器响应的 HTTP 状态信息
  statusText: 'OK',

  // `headers` 服务器响应的头
  headers: {},

  // `config` 是为请求提供的配置信息
  config: {}
}
```

使用 `then` 时，你将接收下面这样的响应：

```js
axios.get('/user/12345')
  .then(function(response) {
    console.log(response.data);
    console.log(response.status);
    console.log(response.statusText);
    console.log(response.headers);
    console.log(response.config);
  });
```

在使用 `catch` 时，或传递 [rejection callback](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/then) 作为 `then` 的第二个参数时，响应可以通过 `error` 对象可被使用。

# 七、全局默认配置

你可以指定将被用在各个请求的配置默认值

```js
Axios.defaults.baseURL = 'https://api.example.com';
Axios.defaults.headers.common['Authorization'] = AUTH_TOKEN;
Axios.defaults.headers.post['Content-Type'] = 'application/x-www-form-urlencoded';
```

全局配置的好处在于在请求当前域名下的资源时，我们无需在加域名，只需要写资源相对路径即可。

# 八、拦截器

在请求或响应被 `then` 或 `catch` 处理前拦截它们。

```javascript
// 添加请求拦截器
Axios.interceptors.request.use(function (config) {
  // 在发送请求之前做些什么
  console.log( config)
  return config;
}, function (error) {
  // 对请求错误做些什么
  return Promise.reject(error);
});

// 添加响应拦截器
Axios.interceptors.response.use(function (response) {
  // 对响应数据做点什么
  console.log(response)
  return response;
}, function (error) {
  // 对响应错误做点什么
  return Promise.reject(error);
});
```

为什么需要拦截器呢？拦截器可以做什么事情？

我们可以通过拦截器判断参数是否合理？请求有没有问题？请求的数据是否有问题？

比如，我们之前对数据进行请求时，需要对请求参数做转换，我们可以把转换的逻辑扔在拦截器中进行处理，如下所示：

```js
import Qs from 'qs';
Axios.interceptors.request.use(function (config) {
  if(config.method == 'post') {
    config.data = Qs.stringify(config.data);
  }
  return config;
}, function (error) {
  return Promise.reject(error);
});
```

这样一来，我们就相当于全局配置了请求设置，而无需在每一次请求时设置了。

# 九、跨域解决方案

## 1. Vue-cli 3.x 之前

1. 修改 ./config/index.js 文件：

```js
proxyTable: {
  '/api': {
    target: 'http://api.douban.com/v2', // 跨域地址
    changeOrigin: true, // 是否跨域
    secure: false, // 是否使用https
    pathRewrite: {
      '^/api': '/api'
    }
  }
}
```

2. 在main.js 设置基础路径

```js
Axios.defaults.baseURL = "/api";
```

3. 请求数据

```js
this.$axios.get("/movie/top250", {
  params: { count: 10, start: 0}
})
.then(res => {
  console.log(res);
}).catch(error => {
  console.log(error);
})
```

> 注意：此种跨域解决方案，只适用于测试阶段，打包的时候，不具备服务器，就不能跨域了，交给后端处理。产品上线之后，就不存在跨域问题啦。

> 提示：一旦修改了配置文件，需重新执行‘npm run dev’，否则配置不生效。

## 2. Vue-cli 3.x 之后

1. 在根目录创建 vue.config.js ，配置如下：

```js
module.exports = {
    dev: {
        proxyTable: {
            '/api': {
                target: 'http://api.douban.com/v2', // 跨域地址
                changeOrigin: true, // 是否跨域
                secure: false, // 是否使用https
                pathRewrite: {
                    '^/api': '/api'
                }
            }
        }
    }
}
```











