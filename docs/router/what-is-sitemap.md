## 什么是sitemap?对网站排名有什么作用？

> sitemap即网站地图，存储网站所有网址url的索引，用于通知搜索引擎网站上有哪些可供抓取的网页。一般放置在网站根目录，通过域名+sitemap.xml的形式访问，如https://olightstore.ca/sitemap.xml

常见的sitemap文件格式有xml、txt，一般使用xml即可，百度、谷歌等主流搜索引擎都是使用xml文件，以下为官网自动生成的sitemap.xml文件格式如下：
```js
This XML file does not appear to have any style information associated with it. The document tree is shown below.
<urlset xmlns="http://www.sitemaps.org/schemas/sitemap/0.9" xmlns:news="http://www.google.com/schemas/sitemap-news/0.9" xmlns:xhtml="http://www.w3.org/1999/xhtml" xmlns:mobile="http://www.google.com/schemas/sitemap-mobile/1.0" xmlns:image="http://www.google.com/schemas/sitemap-image/1.1" xmlns:video="http://www.google.com/schemas/sitemap-video/1.1">
  <url>
    <loc>https://www.olightstore.ca/www.olightstore.ca</loc>
    <lastmod>2021-07-28T07:13:05.330Z</lastmod>
    <changefreq>daily</changefreq>
    <priority>1.0</priority>
  </url>
  <url>
    <loc>https://www.olightstore.ca/new-all-products.html</loc>
    <lastmod>2021-07-20T01:28:53.000Z</lastmod>
    <changefreq>daily</changefreq>
    <priority>0.8</priority>
  </url>
  <url>
    <loc>https://www.olightstore.ca/accessories.html</loc>
    <lastmod>2021-07-19T01:45:18.000Z</lastmod>
    <changefreq>daily</changefreq>
    <priority>0.8</priority>
  </url>
</urlset>
```

网站地图的主要作用是告诉搜索引擎网站的url链接，以及最近更新网页信息，便于搜索引擎抓取收录，因此对于网站的排名具有积极意义。

sitemap文件可以手动制作，也可以使用网络上的免费工具手动生成，成熟的框架一般都会内置sitemap自动生成功能，每次添加文章后便会自动更新sitemap中的url等信息，建议大家选用专业成熟的框架，这样更利于网站的SEO排名。

### 站点地图如何实现： [实现站点地图](/nuxt/nuxt-siteMap.md)
