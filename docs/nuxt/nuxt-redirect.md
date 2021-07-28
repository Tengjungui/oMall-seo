## 如何使用 NuxtJS 处理 301 重定向
> 301 重定向对于良好的技术 SEO 必不可少，这可以使用 NuxtJS 来完成。

SEO 和 301 重定向
301 重定向用于将一个网页重定向到另一个网页，但正确实施它们对于优秀的技术 SEO 更为重要。

什么是 301 重定向？
网站的 HTTP 301 响应表明网页已从一个位置永久移动到另一个位置。

示例：sit-m.olightstore.ca/product32.html被重定向到sit-m.olightstore.ca/product.html
```js
>>> https://sit-m.olightstore.ca/product32.html

> --------------------------------------------
> 301 Moved Permanently
> --------------------------------------------

Request URL: https://sit-m.olightstore.ca/product32.html
Request Method: GET
Status Code: 301 
Remote Address: 13.35.15.126:443
Referrer Policy: strict-origin-when-cross-origin

date: Wed, 28 Jul 2021 09:24:16 GMT
location: /product.html
via: 1.1 ed9908577fd6427c647d93076edebd26.cloudfront.net (CloudFront)
x-amz-cf-id: 6G4oHTRRqb7fidpSz4-nTXGt9Bbu5R9MLCVPAGuxTM-ItC7tjBNzFg==
x-amz-cf-pop: SIN5-C1
x-cache: Miss from cloudfront
X-DNS-Prefetch-Control: off
```
为什么 SEO 需要 301 重定向？
在 2016 年之前，301 重定向可能会在 Google 中失去一些搜索排名因素，但是从 2016 年开始，Google 改变了这一逻辑，现在如果您将一个网址重定向到另一个网址，重定向页面将获得前一个网址的排名因素，这可能是真的有用。让我们看一些例子：

删除的网址：如果您决定从您的网站上删除一个在 Google 中具有任何排名的页面，那么您应该将此页面重定向到您网站上最相关的另一个现有页面，以便删除页面的全部或部分搜索流量将转到重定向的页面。
重复内容：如果你有两个内容非常相似的页面，它们在google中相互竞争相似的关键字，那么你应该将不太强大的url重定向到更强大的页面，这样更强大的页面就会获得两者的排名页面的。
站点迁移：在网站迁移（到新域、架构或大重构）的情况下，您将拥有应重定向到新 url 的页面。
单页应用301重定向问题
处理 301 重定向对于服务器端呈现的网站（如基于 PHP、Wordpress 或 Python 的 Web 应用程序）来说很简单，但对于单页应用程序（如 Vue.js 或 React）来说则存在问题。

Vue.js 本身无法为 Google 等搜索引擎提供 301 重定向响应，因为它仅在客户端运行，它所能做的就是替换当前浏览器 url，而这不是搜索引擎正确处理的。

也许您可以在您的托管服务提供商处配置 301 重定向，但它可能很棘手或不可用，例如 Firebase 托管不支持 301 重定向（仅重定向整个域）。

NuxtJS 可以做 301 重定向
除了 NuxtJS 的许多其他优点外，它可以很容易地进行 301 重定向，让我们看看如何。

第 1 步 - 创建一个 redirects.js serverMiddleware
让我们redirects.js在您的 serverMiddleware 文件夹（例如serverMiddleware/redirects.js）中创建一个。然后让我们把这个文件添加到nuxt.config.js


  serverMiddleware: [
    // Server-side redirects
    '~/serverMiddleware/redirects',
  ],
第 2 步 - 实现重定向逻辑
将此重定向逻辑放入redirects.js：


const redirects = []

module.exports = function (req, res, next) {
  const host = req.headers.host;
  const fullUrl = req.url;
  var url = req.url.split('?')[0];
  var urlParams = null;
  if (req.url.includes("?")) {
    urlParams = '?' + req.url.split('?')[1]
  }

  const redirect = redirects.find(r => r.from === url)
  if (redirect) {
    var newLocation;
    if (urlParams) {
      newLocation = redirect.to + urlParams;
    } else {
      newLocation = redirect.to;
    }
    res.writeHead(301, {
      Location: newLocation
    });
    res.end()
  } else {
    next()
  }
}
此逻辑也使用 url 参数重定向页面。

第 3 步 - 收集重定向 URL
现在你只需redirects要用你的重定向来填充变量。例子：


const redirects =
[
  { from: '/old-deleted-url', to: '/new-working-url' },
  { from: '/outdated-blogpost-1', to: '/shiny-new-blogpost' },
  { from: '/outdated-blogpost-2', to: '/shiny-new-blogpost' },
]
现在，如果用户（或 Google 搜索引擎抓取工具）访问您的/old-deleted-url网页，它将发送 301 重定向到/new-working-url