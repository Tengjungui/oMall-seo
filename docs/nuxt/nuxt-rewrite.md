### nuxt站点伪静态底层实现原理

在项目根目录下找到 <code>nuxt.config.js</code> 往router里面extendRoutes添加 <code>urlSuffix</code>方法,并且引入<code>/utils/seoModule.js</code>文件

```js
  router: {
    extendRoutes(routes, resolve) {
      urlSuffix(routes);
    }
  }
```

## urlSuffix实现方法如下
```js
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

在当前项目下找到<code>/plugins/router.js</code>文件，并且在里面引用<code>/utils/seoModule.js</code>文件下的<code>rewriteRouter</code>方法
```js
import { rewriteRouter } from "~/utils/common";
export default ({ app, route, store }) => {
  rewriteRouter(app);
};
```

## rewriteRouter具体实现逻辑

```js
/**
 * @description: 重写路由跳转 this.$router，url地址添加.html后缀
 * @param {*} router 当前路由对象
 * @param {*} type 后缀类型.html
 */
export function rewriteRouter({ router }, type = ".html") {
  // 保存原来的push函数
  const routerPush = router.push;
  router.push = function(location) {
    // 判断路由跳转是否使用path路径，非首页且不包含html后缀统一添加.html后缀
    if (
      Object.hasOwnProperty.call(location, "path") &&
      location.path !== "/" &&
      !location.path.includes(type)
    ) {
      location.path += type;
    }

    // 调用原来的push函数
    return routerPush.call(this, location);
  };
}
```