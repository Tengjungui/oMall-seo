## 使用@nuxtjs/sitemap给项目添加sitemap(网站地图)

### 如何使用?

在项目根目录下使用yarn/npm/cnpm 安装 @nuxtjs/sitemap

```js
yarn add @nuxtjs/sitemap -D
npm i @nuxtjs/sitemap -D
cnpm i @nuxtjs/sitemap -D
```

在项目根目录下找到 <code>nuxt.config.js</code> 往modules添加 <code>'@nuxtjs/sitemap'</code>,并且引入<code>\static\js\sitemap.js</code>文件
```js
modules: [
  '@nuxtjs/sitemap'
],
sitemap: sitemap,
```

在项目目录下<code>\static\js</code>文件夹,创建<code>sitemap.js</code>文件写入，文件内容如下：

```js
export default (type = 0, uri = 'http://ut.m.olightstore.cn') => {
	const api = basePath().apiPath;
	const url = basePath().hostPath || uri;
	return {
		path: '/sitemap.xml', // sitemap文件名，不用改
		hostname: url, // 网址
		cacheTime: 1000 * 60 * 60 * 24, // 更新频率，只在 generate: false有用
		gzip: true, // 生成 .xml.gz 压缩的 sitemap
		generate: false, // 允许使用 nuxt generate 生成
		// 排除不要页面
		exclude: ['/error/404'],
		// xml默认的配置
		defaults: {
			changefreq: 'always',
			priority: '0.8', //优化级 0-1
			lastmod: new Date()
		},
		// 页面路由
		routes(callback) {
			let indexRoutes = [];
			try {
				axios.defaults.headers.common['X-Client-Type'] = 'H5';
				if (!type) {
					axios
						.get(`${api}/config/api/seo/siteMap/getSiteMap/ALL`)
						.then(res => {
							const { code, data } = res.data;
							if (code === '200' && data) {
								let menuRoutes = data.map(v => {
									return {
										url: v.loc !== '' ? v.loc : '/',
										changefreq: 'daily',
										priority: v.priority,
										lastmodISO: v.lastmod
									};
								});
								callback(null, indexRoutes.concat(menuRoutes));
							}
						})
						.catch(err => {
							throw err;
						});
				} else {
					callback(
						null,
						indexRoutes.concat({
							url: '/',
							changefreq: 'always',
							priority: 0.8,
							lastmodISO: new Date()
						})
					);
				}
			} catch (err) {
				console.log('sitemap err', err);
			}
		}
	};
};
```
