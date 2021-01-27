---
title: nuxtjs 学习使用
date: 2020-10-12
author: FANLE
categories:
 - 前端
tags:
 - Node
 - nuxt
---


## Nuxt 生命周期

![Alt text](https://segmentfault.com/img/remote/1460000022742269)
![Alt text](https://img-blog.csdnimg.cn/20200930160933602.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQxMjkzNTcz,size_16,color_FFFFFF,t_70#pic_center)

### 重要的点

1. 获取数据 asyncData() && Fetch()
2. 中间件  middleware
```

// 列如在middleware目录中创建baseurl.js文件来定义中间件函数

import axios from 'axios'
export default function ({ route, store, redirect }) {
    if (store.state) {
        alert('抱歉您没有token，请先登录')
        return redirect('/search')
    }
}


// 之后在nuxt-config.js中配置，之后就可以在每一个页面中使用

router: {
    middleware: 'baseurl'    // 是js文件名字
}



// 但是如果不想在每一个页面中使用，可以在需要的页面中指定

// 指定中间件函数
middleware: 'baseurl',

```

3. store 持久化数据(vuex-persistedstate)
4. validate 动态路由检查数据
5. vuex 状态树

  actions  dispatch   异步
  mutations  commit   同步

6. 全局与局部的 css
7. auth 鉴权
8. 路由自动生成
9.  head 设置
10. 插件系统
11. 路由传参
12. nuxtServerInit 首屏获取服务端数据



### tips

1. 修改 默认启动端口

```
"server":{
 "host":"127.0.0.1",
 "port":"3304"
}
```

2. middleware中的文件抛出错误

```
export default function({ store, error, redirect }) {
  if (!store.state.user.userInfo.auth) {
      error({
       message: '没有权限哦！',
      statusCode: 403
     })
  }
}
```

3. asyncData 挂载时没有 this 对象


4. 修改成后缀为 html 的页面
```
<a @click="$router.push(`/strA-${'参数id'}.html`)">去到页面A</a>

```

5. 获取当前路由名称和路径
```
获取当前路由名称

$nuxt.$route.path
获取当前路由路径

$nuxt.$route.name

```

6.  在子组件中不要再根元素上使用 v-if 而应该使用 v-show

```
// bad
<div v-if="isVip">

</div>
```

```
//good
<div>

</div>
```

7. 遇到 Vue 错误时候
   ```
    [Vue warn]: The client-side rendered virtual DOM tree is not matching server-rendered content ( Nuxt / Vue / lerna monorepo )
   ``
   服务端与客户端渲染不一致的问题, 这个问题解决方法有两种
  1. 使用 <clinet-only></clinet-only> 标签进行包裹,使其只在客户端加载
  2. 查找代码,用 v-if 切换成 v-show 进行解决
  3. 在 nuxt.config.js 中的 使用(未尝试)
  ```
  extend (config, ctx) {
      config.resolve.symlinks = false
    }
  ```
8. nuxt项目`Failed to execute 'appendChild' on 'Node'`，有没有什么好办法？

  nuxt项目，刷新页面之后报错`Error while initializing app DOMException: Failed to execute 'appendChild' on 'Node': This node type does not support this method`.有没有什么好的解决办法？

  

  ```javascript
  1.
  一般在开发环境下，日志会有warning:The client-side rendered virtual DOM tree is not matching server-rendered content. This is likely caused by incorrect HTML markup, for example nesting block-level elements inside <p>, or missing <tbody>. Bailing hydration and performing full client-side render.

  但是不影响使用，而且一般都是在刷新当前页面的时候才会报这个警告。但是一旦build发布到线上就会发生DOMException: Failed to execute 'appendChild' on 'Node': This node type does not support this method的问题。

  我的解决方案是，直接在疑似产生The client-side rendered virtual DOM tree is not matching server-rendered content问题的代码上包裹一层<client-only>标签，直接不让后台渲染这部分代码就解决这个问题了

  2. 使用 v-if 切换成 v-show
  ```

9. nuxt 项目 `TypeError: [nuxt] Error while mounting app: Cannot read property 'indexOf' of undefined`
  同 第 8 问题 解决方法一致

### 相关插件地址

[cookie-universal-nuxt](https://github.com/microcipcip/cookie-universal/tree/master/packages/cookie-universal-nuxt)




