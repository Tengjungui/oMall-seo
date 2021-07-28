# 动态路由和静态路由的区别？动态路由如何实现？

**优点：**

  静态路由：简单、高效、可靠、网络安全、转发效率高。</br>
  动态路由：灵活，能够适时适应网络结构的变化，无需管理员手工维护，减轻了管理员的工作负担。

**缺点：**

  静态路由：不能灵活的适应网络的动态变化。</br>
  动态路由：占用网络带宽（用于传输路由更新信息）。



## 动态路由

有时无法知道路由的名称，例如当我们调用 API 以获取用户列表或博客文章时。我们称这些为动态路由。要创建动态路由，您需要`_`在`.vue`文件名或目录名之前添加下划线 ( ) 。您可以随意命名文件或目录，但必须用下划线作为前缀。

这个文件树：

```text
pages/
--| _slug/
-----| comments.vue
-----| index.vue
--| users/
-----| _id.vue
--| index.vue
```

会自动生成：

```js
router: {
  routes: [
    {
      name: 'index',
      path: '/',
      component: 'pages/index.vue'
    },
    {
      name: 'users-id',
      path: '/users/:id?',
      component: 'pages/users/_id.vue'
    },
    {
      name: 'slug',
      path: '/:slug',
      component: 'pages/_slug/index.vue'
    },
    {
      name: 'slug-comments',
      path: '/:slug/comments',
      component: 'pages/_slug/comments.vue'
    }
  ]
}
```

如您所见，命名的路由 `users-id` 具有`:id?` 使其可选的路径 ，如果您想使其成为必需，请`index.vue` 在`users/_id` 目录中创建一个 文件 。