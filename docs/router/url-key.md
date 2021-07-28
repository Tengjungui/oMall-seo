Url-key

```json
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

```javascript
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
  let path = route.fullPath;
  let Row =
    (query?.channel || query?.streamerId || JSON.stringify(query) === "{}") &&
    Category &&
    Category.find(item => path.includes(item.urlKey));
  return Row?.refId;
}

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

```javascript
/**
 * @description: 路由添加后缀
 * @param {*} routes 路由列表
 * @param {*} type 后缀类型.html
 */
export function urlSuffix(routes, type = ".html") {
  routes.forEach(item => {
    if (item.path === "/") {
      item.path = "/";
    } else {
      item.path = item.path.includes("/:")
        ? item.path.replace(/\/:/gi, `${type}/:`)
        : `${item.path}${type}`;
    }
  });
}
```

