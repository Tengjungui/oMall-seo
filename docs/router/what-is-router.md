# Router 是什么？ 我们对路由真的了解么？

> 时间是一切财富中最宝贵的财富。 —— 德奥弗拉斯多

<!-- 1、我们对路由真的了解么？ -->
## 什么是Router，它的定义是什么？
Vue路由是指根据url分配到对应的处理程序；作用就是解析URL，调用对应的控制器（的方法，并传递参数）。Vue路由有助于在浏览器的URL或历史记录与Vue组件之间建立链接，从而允许某些路径渲染与之关联的任何一个视图。

Web里根据网址找到能处理这个URL的程序或模块。

## Router有那些参数配置（针对当前seo模块介绍的参数）

### [#](https://router.vuejs.org/api/#routes)routes

- type: `Array<RouteConfig>`

  Type declaration for `RouteConfig`:

  ```ts
  interface RouteConfig = {
    path: string,
    component?: Component,
    name?: string, // for named routes
    components?: { [name: string]: Component }, // for named views
    redirect?: string | Location | Function,
    props?: boolean | Object | Function,
    alias?: string | Array<string>,
    children?: Array<RouteConfig>, // for nested routes
    beforeEnter?: (to: Route, from: Route, next: Function) => void,
    meta?: any,
  
    // 2.6.0+
    caseSensitive?: boolean, // use case sensitive match? (default: false)
    pathToRegexpOptions?: Object // path-to-regexp options for compiling regex
  }
  ```

### [#](https://router.vuejs.org/zh/api/#router-getmatchedcomponents)router.getMatchedComponents

函数签名：

```js
const matchedComponents: Array<Component> = router.getMatchedComponents(location?)
```

返回目标位置或是当前路由匹配的组件数组 (是数组的定义/构造类，不是实例)。通常在服务端渲染的数据预加载时使用。

###  router.addRoutes

*已废弃*：使用 [`router.addRoute()`](https://router.vuejs.org/zh/api/#router-addroute) 代替。

函数签名：

```js
router.addRoutes(routes: Array<RouteConfig>)
```

动态添加更多的路由规则。参数必须是一个符合 `routes` 选项要求的数组。

### 