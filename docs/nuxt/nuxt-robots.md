### 使用@nuxtjs/robots给项目添加robots
> 在使用 Nuxt 项目时，通常我们都是为了提供更好的 SEO 体验，所以我们需要给 Nuxt 项目添加 Robots.txt 文件，来标识哪些资源可以被爬虫嗅探，哪些资源不能够被爬取

在项目根目录下使用yarn/npm/cnpm 安装 @nuxtjs/robots

```js
yarn add @nuxtjs/robots -D
npm i @nuxtjs/robots -D
cnpm i @nuxtjs/robots -D
```

在项目根目录下找到 <code>nuxt.config.js</code> 往modules添加 <code>'@nuxtjs/robots'</code>,并且引入<code>\utils\robots.js</code>文件
```js
modules: [
  '@nuxtjs/robots'
],
robots: robots,
```

在项目目录下<code>\utils</code>文件夹,创建<code>robots.js</code>文件写入，文件内容如下：
```js
/**
 * @description: 获取Robots数据
 * @type {*} 开-1、关-0 （默认1）
 */
export default async (type = 1) => {
  const api = basePath().apiPath;
  let robots = {};
  let reg = /\{[^\{]+\}/g
  try {
    if (type) {
      axios.defaults.headers.common["X-Client-Type"] = "PC";
      let { code, data } = (
        await axios.get(`${api}/config/api/seo/siteMap/getRobots`)
      ).data;
      if (code === "200" && data !== "" && data !== "{}") {
        if (reg.test(data)) {
          Object.assign(robots, JSON.parse(data));
        }
      }
    }
  } catch (err) {
    console.log("robots err", err);
  }
  return robots;
};
```