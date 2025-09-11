# HTTP 缓存

## 关于缓存

- **什么是缓存？** 浏览器缓存指的是浏览器将用户请求过的**静态资源，储存到电脑本地磁盘中**，当浏览器再次访问时，就可以直接从本地加载，不需要再去服务端请求了。
- **为什么需要缓存？** 网络请求相比于 CPU 的计算和页面渲染是非常非常慢的。
- **哪些资源可以被缓存？** 静态资源，比如 js css img。

## 浏览器缓存的优点

1. **降低服务器的压力**，提升网站性能。
2. 加快客户端**加载网页的速度**， 提升用户体验
3. 减少重复数据请求，**避免通过网络再次加载资源**，节省流量

## 强制缓存

不需要发送请求到服务端，直接读取浏览器本地缓存，在 Chrome 的 Network 中显示的 HTTP 状态码是 200 ，在 Chrome 中，强缓存又分为 Disk Cache (存放在硬盘中）和 Memory Cache (存放在内存中），存放的位置是由浏览器控制的。是否强缓存由 Expires、Cache-Control 和 Pragma 3 个 Header 属性共同来控制。

**优先级 Pragma > Cache-Control > Expires**

#### Expires

Expires 是 http 1.0 的规范的一个**响应**首部字段，值是一个**GMT 格式的时间点字符串**，比如 Expires:Mon,18 Oct 2066 23:59:59 GMT。它指定了一个日期/时间，在这个时间/日期之前，HTTP 缓存被认为是有效的。无效的日期比如 0，表示这个资源已经过期了。**如果同时设置了`Cache-Control`响应首部字段的`max-age`，则`Expires`会被忽略**

由于和系统时间进行比较，所以当系统时间和服务器时间不一致的时候，会有缓存有效期不准的问题。目前仅为 HTTP/1.0 的向后兼容而使用。

#### Cache-Control

Cache-Control 是 HTTP/1.1 中新增的属性，在请求头和响应头中都可以使用，常用的属性值如有：

- **max-age**：单位是秒，缓存时间计算的方式是距离发起的时间的秒数，超过间隔的秒数缓存失效；即**缓存最大过期时间**。
- **no-cache**：不使用强缓存，即使用协商缓存
- **no-store**：禁止使用缓存（即强缓存和协商缓存都禁止），每次都向服务器请求最新的资源
- private：专用于个人的缓存，中间代理、CDN 等不能缓存此响应
- public：响应可以被中间代理、CDN 等缓存
- must-revalidate：在缓存过期前可以使用，过期后必须向服务器验证

```js
const express = require("express");
const app = express();
var options = {
  etag: false, // 禁用etag协商缓存
  lastModified: false, // 禁用lastModified协商缓存
  setHeaders: (res, path, stat) => {
    res.set("Cache-Control", "max-age=10"); // 强缓存超时时间为10秒
  },
};
app.use(express.static(__dirname + "/public", options));
app.listen(3000);
```

**只有响应报文首部设置`Cache-Control`为非 0 的`max-age`。同时响应报文首部中`Cache-Control`不存在`no-cache`、`no-store`，并且请求报文首部不存在`Pragma`字段，才会真正命中强缓存**

#### Pragma

`Pragma`只有一个属性值，就是 **no-cache** ，效果和 Cache-Control 中的 no-cache 一致，不使用强缓存，而是进行协商缓存。仅作为于 HTTP/1.0 的向后兼容而使用

### 协商缓存

当浏览器的强缓存失效的时候或者请求头中设置了不走强缓存。这时可由服务器来确定协商缓存资源是否可用。

主要涉及到两对成对出现的属性字段，即**第一次请求的响应头**带上某个字段, 如**Last-Modified 或者 Etag**，则后续请求头中设置了 If-Modified-Since 或者 If-None-Match 的时候，会将这两个属性值到服务端去验证是否命中协商缓存，如果命中了协商缓存，会返回 **304** 状态，加载浏览器缓存，并且请求响应头会设置 Last-Modified 或者 ETag 属性。

#### Last-Modified/If-Modified-Since

- Last-Modified：响应头，资源最后修改时间，由服务器告诉浏览器；
- If-Modified-Since：请求头，资源最后修改时间，由浏览器告诉服务器。

Last-Modified/If-Modified-Since 的值代表的都是文件的最后修改时间，第一次请求服务端会把资源的最后修改时间放到 Last-Modified 响应头中，第二次发起请求的时候，请求头会带上上一次响应头中的 Last-Modified 的时间，并放到 If-Modified-Since 请求头属性中，服务端根据文件最后一次修改时间和 If-Modified-Since 的值进行比较，如果相等，返回 304 ，并加载浏览器缓存。

#### Etag/If-None-Match

- Etag：响应头，资源标识，由服务器告诉浏览器。
- If-None-Match：请求头，缓存资源标识，由浏览器告诉服务器。

Etag/If-None-Match 的值是一串 hash 码，代表的是一个资源的标识符，当服务端的文件变化的时候，它的 hash 码会随之改变，通过请求头中的 If-None-Match 和当前文件的 hash 值进行比较，如果相等则表示命中协商缓存。

ETag 又有强弱校验之分，如果 hash 码是以 "W/" 开头的一串字符串，说明此时协商缓存的校验是弱校验的，只有服务器上的文件差异（根据 Etag 计算方式来决定）达到能够触发 hash 值后缀变化的时候，才会真正地请求资源，否则返回 304 并加载浏览器缓存。

**很多网站的资源后面都加了版本号，这样做的目的是：每次升级了 JS 或 CSS 文件后，为了防止浏览器进行缓存，强制改变版本号，客户端浏览器就会重新下载新的 JS 或 CSS 文件，以保证用户能够及时获得网站的最新更新。**

```javascript
const express = require("express");
const app = express();

const options = {
  etag: true, // 开启etag协商缓存
  lastModified: true, // 开启lastModified协商缓存
  setHeaders: (res, path, stat) => {
    res.set({
      "Cache-Control": "max-age=00", // 浏览器不走强缓存
      Pragma: "no-cache", // 浏览器不走强缓存，走协商缓存
    });
  },
};

app.use(express.static(__dirname + "/public", options));
app.listen(3001);
```

**Last-Modified 只能精确到秒级。如果资源更新的速度是秒以下单位，那么就会造成缓存命中不准确**

**Last-Modified 与 Etag 属性同时出现的时候，Etag 的优先级更高**

#### 协商缓存中，有了 Last-Modified，为什么还会出现 ETag？

Etag 的出现，主要是为了解决 Last-Modified 无法解决的一些问题：

- 某些服务器**不能精确得到文件的最后修改时间**， 这样就无法通过最后修改时间来判断文件是否更新了。
- 某些文件的修改非常**频繁**，**在秒以下的时间内进行修改**，Last-Modified 只能精确到秒，无法判断文件是否更新。
- 一些文件的**最后修改时间改变了，但是内容并未改变**， 使用 ETag 可以正确缓存。

### 强缓存和协商缓存区别

是否需要发请求给服务器；状态码不同。

1. 如果浏览器命中强缓存，则不需要给服务器发请求；而协商缓存最终由服务器来决定是否使用缓存，即客户端与服务器之间存在一次通信。

2. 在 chrome 中强缓存（虽然没有发出真实的 http 请求）的请求状态码返回是 200 (from cache)；而协商缓存如果命中走缓存的话，请求的状态码是 304 (not modified)。

### 三种刷新操作对 http 缓存的影响

- 正常操作：地址栏输入 url，跳转链接，前进后退等。

  强制缓存有效，协商缓存有效，静态资源可以直接使用本地缓存的。

- 手动刷新：f5，点击刷新按钮，右键菜单刷新。（发送一个带上 If-Modified-since 的请求）

  强制缓存失效，协商缓存有效，走协商缓存去了。

- 强制刷新：windows： ctrl + f5；mac： shift+command+r。

  强制缓存失效，协商缓存失效.就是直接把缓存给忽略，重新请求去了。

### 缓存总结

在实际的缓存机制中，强缓存策略和协商缓存策略是在一起**合作使用**的。浏览器首先会根据请求的信息判定，强缓存是否命中，如果命中则直接使用本地资源，如果不命中则根据头信息向服务器发起请求，如果使用协商缓存命中的话，则服务器不返回资源，浏览器直接使用本地资源的副本，如果协商缓存不命中，则服务器返回最新的资源给浏览器。

### cookie

- **HTTP 是无状态的协议（对于事务处理没有记忆能力，每次客户端和服务端会话完成时，服务端不会保存任何会话信息**）：每个请求都是完全独立的，服务端无法确认当前访问者的身份信息，无法分辨上一次的请求发送者和这一次的发送者是不是同一个人。所以服务器与浏览器为了进行会话跟踪（知道是谁在访问我），就必须主动的去维护一个状态，这个状态用于告知服务端前后两个请求是否来自同一浏览器。而这个状态需要通过 cookie 或者 session 去实现。
- **cookie 存储在客户端：** cookie 是服务器发送到用户浏览器并保存在本地的一小块数据，它会在浏览器下次向同一服务器再发起请求时被携带并发送到服务器上。
- **cookie 是不可跨域的：** 每个 cookie 都会绑定单一的域名，无法在别的域名下获取使用，**一级域名和二级域名之间是允许共享使用的**（**靠的是 domain）**。

**cookie 重要的属性**

| 属性           | 说明                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| -------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| **name=value** | 键值对，设置 Cookie 的名称及相对应的值，都必须是**字符串类型** - 如果值为 Unicode 字符，需要为字符编码。 - 如果值为二进制数据，则需要使用 BASE64 编码。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
| **domain**     | 指定 cookie 所属域名，默认是当前域名                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| **path**       | **指定 cookie 在哪个路径（路由）下生效，默认是 '/'**。 如果设置为 `/abc`，则只有 `/abc` 下的路由可以访问到该 cookie，如：`/abc/read`。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
| **maxAge**     | cookie 失效的时间，单位秒。如果为整数，则该 cookie 在 maxAge 秒后失效。如果为负数，该 cookie 为临时 cookie ，关闭浏览器即失效，浏览器也不会以任何形式保存该 cookie 。如果为 0，表示删除该 cookie 。默认为 -1。 - **比 expires 好用**。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
| **expires**    | 过期时间，在设置的某个时间点后该 cookie 就会失效。 一般浏览器的 cookie 都是默认储存的，当关闭浏览器结束这个会话的时候，这个 cookie 也就会被删除                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| **secure**     | 该 cookie 是否仅被使用安全协议传输。安全协议有 HTTPS，SSL 等，在网络上传输数据之前先将数据加密。默认为 false。 当 secure 值为 true 时，cookie 在 HTTP 中是无效，在 HTTPS 中才有效。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| **httpOnly**   | **如果给某个 cookie 设置了 httpOnly 属性，则无法通过 JS 脚本 读取到该 cookie 的信息，但还是能通过 Application 中手动修改 cookie，所以只是在一定程度上可以防止 XSS 攻击，不是绝对的安全**                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| **SameSite**   | **限制第三方 Cookie**，从而减少安全风险。**Strict** 称为严格模式，当前网页的 URL 与请求目标一致，才会带上 Cookie，完全禁止第三方 Cookie，跨站点时，任何情况下都不会发送 Cookie。**Lax** 模式称为宽松模式。如果这个请求是个 GET 请求 a 标签，link 标签，form method="GET" 当这个请求打开了新的页面，那么这个 cookie 可以作为第三方 cookie，其余情况下都不能作为第三方 cookie；**None** 模式 Cookie 将在所有上下文中发送，即允许跨域发送。不过，前提是必须同时设置**Secure**属性（Cookie 只能通过 HTTPS 协议发送），否则无效。设置了**Strict**或**Lax**以后，基本就杜绝了 CSRF 攻击。当然，前提是用户浏览器支持 SameSite 属性。**Chrome 80 之前 SameSite 的默认值是 none，之后是 Lax。不过在最新的 `Chrome91` 版本中这个`已经被移除`了，所以在 91 之前的版本依然可以使用** |

### 状态码分类

- 1xx - 服务器收到请求。

- 2xx - 请求成功，如 200。

  > - 200 - 成功
  > - 201 创建成功

- 3xx - 重定向，如 304。

  > - 301 - 永久重定向（配合 location，浏览器自动处理）。
  > - 302 - 临时重定向（配合 location，浏览器自动处理）。
  > - 304 - 资源未被修改，协商缓存

- 4xx - 客户端错误，如 404。

  > - 401 无权限
  > - 402 需要付费导致的无权限
  > - 403 - 没权限，禁止访问。
  > - 404 - 资源未找到

- 5xx - 服务端错误，如 500。

  > - 500 - 服务器错误。
  > - 504 - 网关超时。

---

## 现代前端缓存技术

### 1. **Service Worker 缓存**

Service Worker 提供了更强大的缓存控制能力，可以实现离线缓存和自定义缓存策略。

```javascript
// service-worker.js
const CACHE_NAME = "app-cache-v1";
const urlsToCache = [
  "/",
  "/styles/main.css",
  "/scripts/main.js",
  "/images/logo.png",
];

// 安装事件 - 缓存资源
self.addEventListener("install", (event) => {
  event.waitUntil(
    caches.open(CACHE_NAME).then((cache) => {
      return cache.addAll(urlsToCache);
    })
  );
});

// 拦截请求 - 缓存优先策略
self.addEventListener("fetch", (event) => {
  event.respondWith(
    caches.match(event.request).then((response) => {
      // 缓存命中，返回缓存
      if (response) {
        return response;
      }

      // 缓存未命中，从网络获取
      return fetch(event.request).then((response) => {
        // 检查响应是否有效
        if (!response || response.status !== 200 || response.type !== "basic") {
          return response;
        }

        // 克隆响应
        const responseToCache = response.clone();

        caches.open(CACHE_NAME).then((cache) => {
          cache.put(event.request, responseToCache);
        });

        return response;
      });
    })
  );
});
```

### 2. **IndexedDB 缓存**

IndexedDB 提供了客户端数据库存储，适合缓存大量结构化数据。

```javascript
// IndexedDB 缓存工具类
class IndexedDBCache {
  constructor(dbName, version) {
    this.dbName = dbName;
    this.version = version;
    this.db = null;
  }

  async open() {
    return new Promise((resolve, reject) => {
      const request = indexedDB.open(this.dbName, this.version);

      request.onerror = () => reject(request.error);
      request.onsuccess = () => {
        this.db = request.result;
        resolve(this.db);
      };

      request.onupgradeneeded = (event) => {
        const db = event.target.result;
        if (!db.objectStoreNames.contains("cache")) {
          db.createObjectStore("cache", { keyPath: "key" });
        }
      };
    });
  }

  async set(key, data, expires = null) {
    const transaction = this.db.transaction(["cache"], "readwrite");
    const store = transaction.objectStore("cache");

    const item = {
      key,
      data,
      timestamp: Date.now(),
      expires,
    };

    return store.put(item);
  }

  async get(key) {
    const transaction = this.db.transaction(["cache"], "readonly");
    const store = transaction.objectStore("cache");

    return new Promise((resolve, reject) => {
      const request = store.get(key);
      request.onsuccess = () => {
        const result = request.result;
        if (!result) {
          resolve(null);
          return;
        }

        // 检查是否过期
        if (result.expires && Date.now() > result.expires) {
          this.delete(key);
          resolve(null);
          return;
        }

        resolve(result.data);
      };
      request.onerror = () => reject(request.error);
    });
  }

  async delete(key) {
    const transaction = this.db.transaction(["cache"], "readwrite");
    const store = transaction.objectStore("cache");
    return store.delete(key);
  }
}

// 使用示例
const cache = new IndexedDBCache("app-cache", 1);
await cache.open();

// 缓存数据
await cache.set("user-data", { name: "John", age: 30 }, Date.now() + 3600000);

// 获取数据
const userData = await cache.get("user-data");
```

### 3. **Web Storage API**

localStorage 和 sessionStorage 的增强使用。

```javascript
// 增强的存储工具类
class StorageManager {
  constructor(storage = localStorage) {
    this.storage = storage;
  }

  set(key, value, expires = null) {
    const item = {
      value,
      timestamp: Date.now(),
      expires,
    };

    this.storage.setItem(key, JSON.stringify(item));
  }

  get(key) {
    const item = this.storage.getItem(key);
    if (!item) return null;

    try {
      const parsed = JSON.parse(item);

      // 检查是否过期
      if (parsed.expires && Date.now() > parsed.expires) {
        this.remove(key);
        return null;
      }

      return parsed.value;
    } catch (error) {
      console.error("Storage parse error:", error);
      return null;
    }
  }

  remove(key) {
    this.storage.removeItem(key);
  }

  clear() {
    this.storage.clear();
  }

  // 获取所有键
  keys() {
    return Object.keys(this.storage);
  }

  // 获取存储大小（字节）
  getSize() {
    let total = 0;
    for (let key in this.storage) {
      if (this.storage.hasOwnProperty(key)) {
        total += this.storage[key].length + key.length;
      }
    }
    return total;
  }
}

// 使用示例
const storage = new StorageManager();

// 设置带过期时间的缓存
storage.set("api-data", { users: [] }, Date.now() + 300000); // 5分钟后过期

// 获取缓存
const apiData = storage.get("api-data");
```

### 4. **HTTP/2 Server Push 缓存**

利用 HTTP/2 的服务器推送功能预加载资源。

```javascript
// 服务器端配置（Node.js + Express）
const express = require("express");
const http2 = require("http2");
const fs = require("fs");

const app = express();

// HTTP/2 服务器推送
app.get("/", (req, res) => {
  // 推送关键资源
  if (res.push) {
    res.push("/styles/main.css", {
      request: { accept: "text/css" },
      response: { "content-type": "text/css" },
    });

    res.push("/scripts/main.js", {
      request: { accept: "application/javascript" },
      response: { "content-type": "application/javascript" },
    });
  }

  res.sendFile(__dirname + "/index.html");
});

// 启动 HTTP/2 服务器
const options = {
  key: fs.readFileSync("private-key.pem"),
  cert: fs.readFileSync("certificate.pem"),
};

http2.createSecureServer(options, app).listen(443);
```

### 5. **预加载和预连接**

利用 `<link>` 标签的预加载功能。

```html
<!-- 预加载关键资源 -->
<link
  rel="preload"
  href="/fonts/main.woff2"
  as="font"
  type="font/woff2"
  crossorigin
/>
<link rel="preload" href="/images/hero.jpg" as="image" />
<link rel="preload" href="/scripts/critical.js" as="script" />

<!-- 预连接到外部域名 -->
<link rel="preconnect" href="https://fonts.googleapis.com" />
<link rel="preconnect" href="https://api.example.com" />

<!-- 预获取下一页资源 -->
<link rel="prefetch" href="/next-page.html" />
<link rel="prefetch" href="/api/user-data" />

<!-- DNS 预解析 -->
<link rel="dns-prefetch" href="//cdn.example.com" />
```

### 6. **CDN 缓存策略**

利用 CDN 的边缘缓存提升性能。

```javascript
// CDN 缓存配置示例
const cdnConfig = {
  // 静态资源 - 长期缓存
  static: {
    "Cache-Control": "public, max-age=31536000, immutable",
    "CDN-Cache-Control": "max-age=31536000",
  },

  // HTML 文件 - 短期缓存
  html: {
    "Cache-Control": "public, max-age=3600",
    "CDN-Cache-Control": "max-age=3600",
  },

  // API 响应 - 不缓存
  api: {
    "Cache-Control": "no-cache, no-store, must-revalidate",
    "CDN-Cache-Control": "no-cache",
  },
};

// 根据文件类型设置缓存头
function setCacheHeaders(res, fileType) {
  const headers = cdnConfig[fileType] || cdnConfig.static;
  Object.entries(headers).forEach(([key, value]) => {
    res.setHeader(key, value);
  });
}
```

---

## 缓存策略最佳实践

### 1. **分层缓存策略**

**缓存层次结构：**

```
用户请求 → CDN缓存 → 浏览器缓存 → 服务器缓存 → 数据库缓存
```

**各层缓存时间建议：**

| 缓存层级   | 资源类型  | 缓存时间  | 说明               |
| ---------- | --------- | --------- | ------------------ |
| **CDN**    | 静态资源  | 1 年      | 使用版本号控制更新 |
| **CDN**    | HTML 文件 | 1 小时    | 平衡性能和实时性   |
| **浏览器** | 静态资源  | 1 年      | 配合版本号使用     |
| **浏览器** | HTML 文件 | 不缓存    | 确保获取最新内容   |
| **浏览器** | API 响应  | 5-30 分钟 | 根据数据更新频率   |

### 2. **资源版本控制策略**

**文件名版本控制：**

```javascript
// webpack 配置
module.exports = {
  output: {
    filename: "[name].[contenthash].js",
    chunkFilename: "[name].[contenthash].chunk.js",
  },
  plugins: [
    new MiniCssExtractPlugin({
      filename: "[name].[contenthash].css",
    }),
  ],
};

// 生成的文件名示例
// main.a1b2c3d4.js
// styles.e5f6g7h8.css
```

**查询参数版本控制：**

```html
<!-- 不推荐：查询参数版本控制 -->
<link rel="stylesheet" href="/styles/main.css?v=1.2.3" />
<script src="/scripts/main.js?v=1.2.3"></script>

<!-- 推荐：文件名版本控制 -->
<link rel="stylesheet" href="/styles/main.a1b2c3d4.css" />
<script src="/scripts/main.e5f6g7h8.js"></script>
```

### 3. **缓存更新策略**

**Cache-First 策略：**

```javascript
// Service Worker 缓存优先策略
self.addEventListener("fetch", (event) => {
  event.respondWith(
    caches.match(event.request).then((response) => {
      if (response) {
        // 缓存命中，返回缓存
        return response;
      }

      // 缓存未命中，从网络获取
      return fetch(event.request);
    })
  );
});
```

**Network-First 策略：**

```javascript
// 网络优先策略（适合API请求）
self.addEventListener("fetch", (event) => {
  event.respondWith(
    fetch(event.request)
      .then((response) => {
        // 网络请求成功，更新缓存
        const responseClone = response.clone();
        caches.open(CACHE_NAME).then((cache) => {
          cache.put(event.request, responseClone);
        });
        return response;
      })
      .catch(() => {
        // 网络请求失败，返回缓存
        return caches.match(event.request);
      })
  );
});
```

**Stale-While-Revalidate 策略：**

```javascript
// 过期重新验证策略
self.addEventListener("fetch", (event) => {
  event.respondWith(
    caches.match(event.request).then((response) => {
      // 立即返回缓存（可能过期）
      const fetchPromise = fetch(event.request).then((networkResponse) => {
        // 更新缓存
        caches.open(CACHE_NAME).then((cache) => {
          cache.put(event.request, networkResponse.clone());
        });
        return networkResponse;
      });

      // 返回缓存或网络响应
      return response || fetchPromise;
    })
  );
});
```

### 4. **缓存失效策略**

**基于时间的失效：**

```javascript
// 设置缓存过期时间
const CACHE_EXPIRY = {
  "api-data": 5 * 60 * 1000, // 5分钟
  "user-profile": 30 * 60 * 1000, // 30分钟
  "static-assets": 24 * 60 * 60 * 1000, // 24小时
};

function setCacheWithExpiry(key, data, type = "api-data") {
  const item = {
    data,
    timestamp: Date.now(),
    expires: Date.now() + CACHE_EXPIRY[type],
  };

  localStorage.setItem(key, JSON.stringify(item));
}
```

**基于版本的失效：**

```javascript
// 版本控制缓存
const CACHE_VERSION = "v1.0.0";

function getCacheKey(key) {
  return `${CACHE_VERSION}:${key}`;
}

function setVersionedCache(key, data) {
  const versionedKey = getCacheKey(key);
  localStorage.setItem(versionedKey, JSON.stringify(data));
}

function getVersionedCache(key) {
  const versionedKey = getCacheKey(key);
  const cached = localStorage.getItem(versionedKey);
  return cached ? JSON.parse(cached) : null;
}
```

**基于标签的失效：**

```javascript
// 标签缓存系统
class TaggedCache {
  constructor() {
    this.tags = new Map(); // tag -> Set of keys
    this.cache = new Map(); // key -> { data, tags }
  }

  set(key, data, tags = []) {
    // 存储数据
    this.cache.set(key, { data, tags });

    // 更新标签索引
    tags.forEach((tag) => {
      if (!this.tags.has(tag)) {
        this.tags.set(tag, new Set());
      }
      this.tags.get(tag).add(key);
    });
  }

  get(key) {
    const item = this.cache.get(key);
    return item ? item.data : null;
  }

  invalidateByTag(tag) {
    const keys = this.tags.get(tag);
    if (keys) {
      keys.forEach((key) => {
        this.cache.delete(key);
      });
      this.tags.delete(tag);
    }
  }

  invalidateByTags(tags) {
    tags.forEach((tag) => this.invalidateByTag(tag));
  }
}

// 使用示例
const cache = new TaggedCache();

// 设置带标签的缓存
cache.set("user-123", userData, ["user", "profile"]);
cache.set("user-456", userData2, ["user", "profile"]);

// 失效所有用户相关缓存
cache.invalidateByTag("user");
```

### 5. **缓存性能优化**

**缓存大小限制：**

```javascript
// 缓存大小管理
class CacheManager {
  constructor(maxSize = 50 * 1024 * 1024) {
    // 50MB
    this.maxSize = maxSize;
    this.currentSize = 0;
    this.cache = new Map();
  }

  set(key, value) {
    const size = this.getSize(value);

    // 检查是否需要清理空间
    if (this.currentSize + size > this.maxSize) {
      this.cleanup();
    }

    // 存储数据
    this.cache.set(key, {
      value,
      size,
      timestamp: Date.now(),
    });

    this.currentSize += size;
  }

  get(key) {
    const item = this.cache.get(key);
    if (item) {
      // 更新访问时间
      item.timestamp = Date.now();
      return item.value;
    }
    return null;
  }

  cleanup() {
    // 按访问时间排序，删除最旧的缓存
    const entries = Array.from(this.cache.entries()).sort(
      (a, b) => a[1].timestamp - b[1].timestamp
    );

    // 删除最旧的20%缓存
    const toDelete = Math.ceil(entries.length * 0.2);
    for (let i = 0; i < toDelete; i++) {
      const [key, item] = entries[i];
      this.cache.delete(key);
      this.currentSize -= item.size;
    }
  }

  getSize(value) {
    return JSON.stringify(value).length * 2; // 粗略估算
  }
}
```

**缓存预热：**

```javascript
// 缓存预热策略
class CacheWarmer {
  constructor(cache) {
    this.cache = cache;
    this.priority = ["critical", "high", "medium", "low"];
  }

  async warmup(resources) {
    // 按优先级排序
    const sortedResources = resources.sort((a, b) => {
      return (
        this.priority.indexOf(a.priority) - this.priority.indexOf(b.priority)
      );
    });

    // 分批预热，避免阻塞主线程
    for (const resource of sortedResources) {
      await this.warmupResource(resource);

      // 让出主线程
      await new Promise((resolve) => setTimeout(resolve, 0));
    }
  }

  async warmupResource(resource) {
    try {
      const response = await fetch(resource.url);
      const data = await response.json();
      this.cache.set(resource.key, data);
    } catch (error) {
      console.warn(`Failed to warmup ${resource.key}:`, error);
    }
  }
}

// 使用示例
const cache = new CacheManager();
const warmer = new CacheWarmer(cache);

const resources = [
  { key: "user-profile", url: "/api/user/profile", priority: "critical" },
  { key: "app-config", url: "/api/config", priority: "high" },
  { key: "news-list", url: "/api/news", priority: "medium" },
];

// 页面加载后预热缓存
window.addEventListener("load", () => {
  warmer.warmup(resources);
});
```

## 📋 总结

HTTP 缓存是前端性能优化的重要组成部分，需要根据不同的应用场景选择合适的缓存策略：

### **核心原则**

1. **分层缓存** - 从 CDN 到浏览器，多层缓存提升性能
2. **版本控制** - 使用文件名版本控制，避免缓存问题
3. **策略选择** - 根据数据特性选择合适的缓存策略
4. **监控优化** - 持续监控缓存命中率，优化缓存效果

### **最佳实践**

- **静态资源**：长期缓存 + 版本控制
- **HTML 文件**：短期缓存或不缓存
- **API 数据**：根据更新频率设置缓存时间
- **用户数据**：谨慎缓存，注意隐私安全

### **现代技术**

- **Service Worker**：离线缓存和自定义策略
- **IndexedDB**：大量结构化数据缓存
- **HTTP/2 Push**：服务器主动推送资源
- **预加载技术**：提前加载关键资源

记住：**缓存不是万能的，需要根据实际业务场景和用户需求来设计和优化缓存策略**。
