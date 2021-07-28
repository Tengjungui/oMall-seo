## Router Construction Options

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