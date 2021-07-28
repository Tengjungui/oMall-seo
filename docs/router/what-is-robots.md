## 什么是robots文件、robots.txt文件的作用、robots.txt该怎么写？
>robots.txt是一种存放于网站根目录下的ASCII编码的文本文件，它通常告诉网络搜索引擎的漫游器（又称网络爬虫/蜘蛛），此网站中的哪些内容是不能被搜索引擎的漫游器获取的，哪些是可以被获取的。当robots访问一个网站时，首先会检查该网站中是否存在这个文件，如果机器人找到这个文件，它就会根据这个文件的内容，来确定它访问权限的范围因为一些系统中的URL是大小写敏感的，所以robots.txt的文件名应统一为小写。

### 注意：

1. 所有字母需要用英文输入法
2. 与 * 之间需有一个英文输入法的空格
3. 与 / 之间需有一个英文输入法的空格

### robots.txt文件的作用：

   1. 屏蔽网站内的死链接。
   2. 屏蔽搜索引擎蜘蛛抓取站点内重复内容和页面。
   3. 阻止搜索引擎索引网站隐私性的内容。

因此建立robots.txt文件是很有必要的，网站中重复的内容、页面或者404信息过多，搜索引擎蜘蛛就会认为该网站价值较低，从而降低对该网站的“印象分”，这就是我们经常听到的“降低权重”，这样网站的排名就不好了。

robots.txt文件需要注意的最大问题是：必须放置在一个站点的根目录下，而且文件名必须全部小写。

robots.txt文件的基本语法只有两条，第一条是：User-agent，即搜索引擎蜘蛛的名称；第二条是：Disallow，即要拦截的部分。

下面我们看一下撰写基本的robots.txt文件所需要知道的一些语法和作用。

（1），允许所有的搜索引擎访问网站的所有部分或者建立一个空白的文本文档，命名为robots.txt。

##### 写法
  > user-agent: * </br> Disallow:
##### 或者
  > user-agent: * </br> Allow: /

### 示例：

#### 只允许搜索引擎访问网站首页：
  > User-agent: * </br> Disallow: /*

#### 如果允许搜索引擎访问网站的某个目录中的某些特定网址
>  user-agent: * </br> Allow: /admin/my </br> Allow: /css/html </br> Allow: /images/index </br> Disallow: /admin/ </br> Disallow: /css/ </br> Disallow: /imgaes/


#### 禁止访问网站中的动态页面：
>  User-agent: * Disallow: /*?*

### robots如何实现： [实现robots](/nuxt/nuxt-robots.md)
