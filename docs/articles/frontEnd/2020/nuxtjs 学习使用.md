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



### 相关插件地址

[cookie-universal-nuxt](https://github.com/microcipcip/cookie-universal/tree/master/packages/cookie-universal-nuxt)




