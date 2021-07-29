## 什么是URL key？
> URL key应由带连字符的小写字符组成，以分隔单词。精心设计的“搜索引擎友好”URL key可能包含产品名称和关键字，以改进搜索引擎对其进行索引的方式。

    URL key是描述产品或类别的静态 URL 的一部分。创建产品或类别时，会根据名称自动生成初始 URL key。要更改 URL key，请参阅产品信息的搜索引擎优化部分。

## URL key 实现原理与思路


> ## 解决方案一
设置一个公用的URL key 规则列表
```js
// url-key 配置列表
export const urlKeyConf = [
  { key:'', prefix: '', name: 'ALL', desc: '查询所有的', code: 0 },
  { key:'product', prefix: 'category', name: 'CATEGORY', desc: '分类seo', code: 1 },
  { key:'markting', prefix: 'flash', name: 'MODULE', desc: '模块seo', code: 2 },
  // { key:'markting-id', prefix: 'flash-sale', name: 'MODULE', desc: '模块seo', code: 2 },
  { key:'detail-id', prefix: 'product', name: 'PRODUCT', desc: '商品seo', code: 3 },
  { key:'', prefix: 'm-flash-sale', name: 'ACTIVITY', desc: '活动seo', code: 4 },
  { key:'', prefix: '', name: 'CUSTOM', desc: '自定义SiteMap', code: 5 },
  { key:'', prefix: 'special-sale', name: 'SPECIAL_ACTIVITY', desc: '专题活动', code: 6 },
  { key:'', prefix: '', name: 'ADVERTISING_LIST', desc: '广告列表', code: 7 },
  { key:'', prefix: 'blog', name: 'INFO_LIST', desc: '信息列表', code: 8 }      
];
```
```js
// 根据url-key生成路由规则配置
export const routesConf = _  => {
  let conf = {}
  urlKeyConf.map((obj => ({key, prefix}) => key && Object.assign(obj, {[key]: prefix}))(conf))
  return conf
}
```
在项目根目录下找到 <code>nuxt.config.js</code> 往router里面extendRoutes添加 <code>setRouteAlias</code>方法,并且引入<code>/utils/seoModule.js</code>文件

```js
  router: {
    extendRoutes(routes, resolve) {
      urlSuffix(routes)
      routes.push({
        name: "404",
        path: "*",
        component: resolve(__dirname, "pages/error/404.vue")
      },{
        name: "markting",
        alias: "/flash/*",
        path: "/markting",
        component: resolve(__dirname, "pages/markting/index.vue")
      });
      // 6: {path: "/markting", alias: "/flash", name: "markting", component: ƒ}
      // 设置路由别名
      setRouteAlias(routes, conf())
    }
  },
```

```js
// conf() : {
//   product: 'category',
//   'markting-id': 'flash',
//   'detail-id': 'product'
// }
/**
 * @description: 设置路由列表别名
 * @param {*} obj url-key配置列表
 * @param {*} routes 路由列表
 */
 export function setRouteAlias(routes, obj = { 'detail-id': 'p/*' }) {
	Object.entries(obj).map(([k, v]) => {
		routes.forEach(item => {
      if (item.name === k) {
        // /\/\w*?\/:/g ||  /\/\w+/g
        let isArg = item.path.includes('/:')
        let patt = new RegExp(`\\/\\w${isArg?'*?\/:':'+'}`,'g')
				Object.assign(item, {
          alias: item.path.replace(patt, `/${v + (isArg ? '/:' : '/*') }`)
				});
			}
		});
	});
}
```
#### 优点：
无需请求后端api数据
#### 缺点：
地址栏存在前缀或者多一层目录如：<code>/flash/*</code>、<code>/F_*</code>

> ## 解决方案二
```js
plugins: [
  { src: "@/plugins/url-keys", ssr: true },
]
```

#### 前期准备条件
> 后台api接口数据
```js
[{
  "urlKey": "torch.html",
  "type": {
    "name": "CATEGORY",
    "desc": "分类seo",
    "sort": 1,
    "code": 1
  },
  "refId": "1415477801909043203",
  "updateTime": "2021-07-19 08:35:20"
},
{
  "urlKey": "测试-1.html",
  "type": {
    "name": "SPECIAL_ACTIVITY",
    "desc": "专题活动",
    "sort": 2,
    "code": 6
  },
  "refId": "1417406832714653698",
  "updateTime": "2021-07-20 04:51:38"
}, {
  "urlKey": "test-summer.html",
  "type": {
    "name": "PRODUCT",
    "desc": "商品seo",
    "sort": 4,
    "code": 3
  },
  "refId": "1417101774116728834",
  "updateTime": "2021-07-19 08:39:26"
}]
```
设置一个公用的URL key 规则列表
```js
// url-key 配置列表
export const urlKeyConf = [
  { key: "", name: "ALL", desc: "查询所有的", code: 0 },
  { key: "product", name: "CATEGORY", desc: "分类seo", code: 1 },
  { key: "markting", name: "MODULE", desc: "模块seo", code: 2 },
  { key: "detail-id", name: "PRODUCT", desc: "商品seo", code: 3 },
  { key: "", name: "ACTIVITY", desc: "活动seo", code: 4 },
  { key: "", name: "CUSTOM", desc: "自定义SiteMap", code: 5 },
  { key: "activities", name: "SPECIAL_ACTIVITY", desc: "专题活动", code: 6 },
  { key: "article", name: "INFO_LIST", desc: "信息列表", code: 7 }
];
```

在目录<code>/plugins</code>下新建<code>url-keys.js</code>文件，具体内容如下：

```js
export default ({ app, store }, inject) => {
  let row = {};
  let DIC = {};
  let routes = app.router.options.routes;
  app.router.beforeEach(async (to, from, next) => {
    const matched = app.router.getMatchedComponents(to);
    try {
      let { code, data } = await app.$getUrlKey();
      if (code === "200" && nonEmptyArr(data)) {
        // !根据urlKeyConf数据进行筛选分类
        urlKeyConf.map(({ name, key, code }) => {
          DIC[name] = Array.from(
            data.filter(item => item.type.code === code),
            ({ urlKey, ...res }) => ({
              urlKey: encodeURI(parseUrl(urlKey)),
              ...res
            })
          );
          // ! 获取当前组件urlKey列表
          if (key) {
            Object.assign(row, {
              [key]: Array.from(DIC[name], ({ urlKey }) => urlKey)
            });
          }
        });
      }

      // ! 匹配当前路由并添加别名列表
      let newRoute = Array.from(Object.entries(row), ([k, v]) => ({
        ...routes.find(item => item.name === k),
        alias: v
      }));
      // 判断当前路径是否存在url-key列表中
      let isBreak = Object.values(row).some(item => item.includes(to.path));

      if (matched.length) {
        next();
      } else {
        if (isBreak) {
          app.router.addRoutes(newRoute);
          next(to.fullPath);
        } else {
          next({ name: "error-404" });
        }
      }
    } catch (err) {
      console.log("err :>> ", err);
    }
  });

  // !兼容问题写在beforeEach可能未定义（npm 安装未定义）
  inject("DIC", (name = "") => {
    return name ? DIC[name] : DIC;
  });
};
```

在页面上分别需要使用两种方法去获取路径和当前地址id参数</br>
> #### findById(slef, query, route, key = "PRODUCT") 
<!-- [# <font color="#273849">findById(slef, query, route, key = "PRODUCT")</font>](#	"ddd") -->

**参数：**</br>
+ <code>{Object} slef</code>
+ <code>{Object} query</code>
+ <code>{Object} route</code>
+ <code>{string} key</code>

**返回值：** 返回refId</br>
**用法：**
该函数是在URL key内容页面使用，根据URL key生成的页面地址（当前地址栏网址去掉参数）过滤地址字典库（$DIC）的列表，返回当前页面的id参数值。
```js
/**
 * @description: 根据当前地址栏过滤url-key列表，查询唯一id
 * @param {*} slef 当前this
 * @param {*} query 页面参数
 * @param {*} route 当前路由
 * @param {*} key 类目名称
 * @return refId
 */
export function findById(slef, query, route, key = "PRODUCT") {
  let Category = slef.$DIC && slef.$DIC(key);
  // 处理当前路径
  let path = route.fullPath.split("?")[0];
  let Row =
    (query?.channel || query?.streamerId || JSON.stringify(query) === "{}") &&
    Category &&
    Category.find(item => path === item.urlKey);
  return Row?.refId;
}
```
> #### findByPath(slef, id, key = "PRODUCT") 

**参数：**</br>
+ <code>{Object} slef</code>
+ <code>{string} id</code>
+ <code>{string} key</code>

**返回值：** 返回urlKey</br>
**用法：**
该函数是在设置path路径或者跳转路径时使用，根据当前数据的商品id过滤地址字典库（$DIC）的内容，从而获取当前数据的path路径。
```js
/**
 * @description: 根据id过滤url-key列表，获取path路径
 * @param {*} slef 当前this
 * @param {*} key 类目名称
 * @param {*} id 当前id
 * @return urlKey
 */
export function findByPath(slef, id, key = "PRODUCT") {
  let Category = slef.$DIC && slef.$DIC(key);
  let Row = Category && Category.find(item => item.refId === id);
  return Row?.urlKey;
}
```
#### 优点：
可以随意配置url地址
#### 缺点：
请求次数频繁，需要实时更新

> 使用示例如下：
##### findByPath在页面上如何使用
```js
  convertUrl(item) {
    let path = findByPath(this, item.productId);
    const params = {
      path: "/detail" + this.$urlSuffix,
      query: {
        productId: item.productId,
        skuId: item.skuId
      }
    };
    return path ?? `${params.path}?${qs.stringify(params.query)}`;
  },
```
##### findById在页面上如何使用
```js
  let productId = query?.productId ?? findById(app, query, route);
  let { data: detailData, code: detailCode } = productId ? await app.$productDetail(productId) : await app.$detailBySkuId(skuId);
```

