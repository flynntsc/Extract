## 工具
- [谷歌速度测试与优化建议](https://developers.google.com/speed/pagespeed/insights/)
- [速度评分](gtmetrix.com)
- [页面测试](www.webpagetest.org)

## 应用性能三要素

1. 取得资源
2. 页面布局和渲染
3. 执行JavaScript

1执行完毕，2&3交错并发执行

## 时间

**TTFB**

是从浏览器发起网络请求到从服务器接收到第一个字节的这段时间，它包含了 TCP连接时间，发送HTTP请求时间和获得响应消息第一个字节的时间。（包括DNS、socket连接和请求响应时间，反映服务端响应速度重要指标）

1. 减少DNS查询
2. 使用CDN
3. 提早Flush（如提前生成好整个静态页面，有更新再重新生成）
4. 添加周期头

**TTSR**

是开始渲染时间，指某些非空元素开始在浏览器显示时的时间。

1. 优化TTFB
2. 会阻塞页面渲染的js放后

**TTDC**

是文档完成时间，指页面结束加载，可供用户进行操作的时间，等价于浏览器的onload事件触发点

1. 优化TTFB
2. 优化TTSR
3. 优化首屏时间，将不必要页面放到onload后加载

**TTFL**

是完全加载时间，指页面在onload之前和onload事件之后额外加载的内容所花费的时间的总和，即页面完完全全加载完毕消耗的总时间。

1. 优化前三步
2. 延迟加载
3. 异步加载
4. 按需加载

DSN解析慢 -> 域名解析服务器差 -> 更换NS 服务器

初始化连接久 -> 网络不好 -> 更换机房

使用 Keep-Alive 可以从第二次开始节省 TCP 握手时间，不用每次都三次握手。

现代浏览器同域并发数为6个，权衡域名散列VS每个域的开销

## 解决

**让TTFB提前**

- 设置合理的 DNS TTL 时间
- 合理的服务端技术选型（Node.js - ThinkJS）
- 合理的服务端部署（Docker 多机房部署、CDN）
- 合理利用服务端缓存
- 减少重定向（HSTS Preloading）
- 提前输出（Transfer-Encoding:chunked ）

**服务端做缓存**

**数据预取与缓存的被动更新**

数据预取：博客中的第三方数据如评论数，接口缓慢。可以定时预取，存入缓存。

缓存的被动更新：某些页面数据量大，但访问量小。可以优先返回缓存数据，同事出发缓存更新机制。

**提前输出（Transfer-Encoding:chunked ）**

优先分块传输头部搜索框等静态内容

**让用户更早看到主体框架**

**让用户知道当前状态**

加载中的图示

**减少传输大小**

- 去掉无用 / 冗余代码，精简异步接口
- 代码压缩（HTML、CSS、JS）
- 图片压缩（Webp、Guetzli）
- 矢量图标（CSS 3 / Web Font / SVG）
- 使用 Video 替换 Gif
- 服务端开启响应压缩（Gzip，br）
- 缓存（Cache）

**减少请求连接数**

- 合并请求
 - 异步接口合并（Batch Ajax Request）
 - 图片合并（CSS Sprite）
 - CSS、JS 合并（Concatenation）
 - CSS、JS 内联（Inline）
 - 图片、音频内联（Data URI）
- 缓存（Cache）
- 异步加载 / 按需加载

**合并请求的弊端**

- 可用时间变晚（木桶效应）
- 浪费网络流量
- 浪费浏览器内存
- 降低缓存命中率

**HTTP/2**

- 多路复用，可以同时传输 256 个流，无需合并
- Server Push，重要资源在主页面之前推送，无需内联
- 头部压缩解决大量请求引入额外头部开销的问题


**HTTP缓存**

- Last-Modified
- ETag
- Expires
- Cache-Control

**Service Worker**

常用策略

- networkFirst：仅在无网络时才返回 Cache，确保离线可用（缺点是不能提高响应速度，甚至变慢）
- cacheFirst：优先返回 Cacahe 同时异步更新 Cache，确保响应速度（缺点是页面可能不是最新）

**WEB 网络技术选型**

|| 特点 | 适合场景|
| --- | --- | --- |
| XHR | 异步、浏览器支持良好| 异步文件上传、获取/提交数据|
| SSE​​（Server-Sent Events）| 长连接、浏览器支持良好、服务端 => 浏览器 | 消息推送、实时报表 |
| WebSocket | 双向通讯、事件驱动 | 即时聊天，实时调试 |
| WebRTC（RTCDataChannel）|点对点传输任意数据、低延时 | 实时音视频传输、屏幕共享、文件 P2P 传输 |


