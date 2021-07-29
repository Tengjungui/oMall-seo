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

##  动态路由匹配
我们经常需要把某种模式匹配到的所有路由，全都映射到同个组件。例如，我们有一个 User 组件，对于所有 ID 各不相同的用户，都要使用这个组件来渲染。那么，我们可以在 vue-router 的路由路径中使用“动态路径参数”(dynamic segment) 来达到这个效果：

```js
const User = {
  template: '<div>User</div>'
}

const router = new VueRouter({
  routes: [
    // 动态路径参数 以冒号开头
    { path: '/user/:id', component: User }
  ]
})
```

现在呢，像 /user/foo 和 /user/bar 都将映射到相同的路由。

一个“路径参数”使用冒号 : 标记。当匹配到一个路由时，参数值会被设置到 this.$route.params，可以在每个组件内使用。于是，我们可以更新 User 的模板，输出当前用户的 ID：
```js
const User = {
  template: '<div>User {{ $route.params.id }}</div>'
}
```
你可以看看这个在线例子 (opens new window)。

你可以在一个路由中设置多段“路径参数”，对应的值都会设置到 $route.params 中。例如：

| 模式                          | 匹配路径            | $route.params                        |
| :---------------------------- | ------------------- | ------------------------------------ |
| /user/:username               | /user/evan          | { username: 'evan' }                 |
| /user/:username/post/:post_id | /user/evan/post/123 | { username: 'evan', post_id: '123' } |
除了 $route.params 外，$route 对象还提供了其它有用的信息，例如，$route.query (如果 URL 中有查询参数)、$route.hash 等等。你可以查看 API 文档 的详细说明。

## 响应路由参数的变化
提醒一下，当使用路由参数时，例如从 /user/foo 导航到 /user/bar，原来的组件实例会被复用。因为两个路由都渲染同个组件，比起销毁再创建，复用则显得更加高效。不过，这也意味着组件的生命周期钩子不会再被调用。

复用组件时，想对路由参数的变化作出响应的话，你可以简单地 watch (监测变化) $route 对象：

```js
const User = {
  template: '...',
  watch: {
    $route(to, from) {
      // 对路由变化作出响应...
    }
  }
}
```
或者使用 2.2 中引入的 beforeRouteUpdate 导航守卫：
```js
const User = {
  template: '...',
  beforeRouteUpdate(to, from, next) {
    // react to route changes...
    // don't forget to call next()
  }
}
```
## 捕获所有路由或 404 Not found 路由
常规参数只会匹配被 / 分隔的 URL 片段中的字符。如果想匹配任意路径，我们可以使用通配符 (*)：
```js
{
  // 会匹配所有路径
  path: '*'
}
{
  // 会匹配以 `/user-` 开头的任意路径
  path: '/user-*'
}
```
当使用通配符路由时，请确保路由的顺序是正确的，也就是说含有通配符的路由应该放在最后。路由 { path: '*' } 通常用于客户端 404 错误。如果你使用了History 模式，请确保正确配置你的服务器。

当使用一个通配符时，$route.params 内会自动添加一个名为 pathMatch 参数。它包含了 URL 通过通配符被匹配的部分：
```js
// 给出一个路由 { path: '/user-*' }
this.$router.push('/user-admin')
this.$route.params.pathMatch // 'admin'
// 给出一个路由 { path: '*' }
this.$router.push('/non-existing')
this.$route.params.pathMatch // '/non-existing'
```
## 高级匹配模式
vue-router 使用 path-to-regexp作为路径匹配引擎，所以支持很多高级的匹配模式，例如：可选的动态路径参数、匹配零个或多个、一个或多个，甚至是自定义正则匹配。查看它的文档学习高阶的路径匹配，还有这个例子 展示 vue-router 怎么使用这类匹配。

## 匹配优先级
有时候，同一个路径可以匹配多个路由，此时，匹配的优先级就按照路由的定义顺序：路由定义得越早，优先级就越高。
