---
title: Git 一些问题总结
date: 2020-03-11
author: 范了饭饭
categories:
 - 后端
tags: 
 - 部署
 - Git
---
:::tip
Git 遇到的一些小问题, 以后不定时更新
:::

<!-- more -->
## 1. 删除文件及文件夹问题

```
// 流程
git pull orign master

git rm -r 文件夹

git rm  文件1 文件2

git add ./

git commit -m"delete"

// 强制上传 git push -u origin master
git push origin master 

```



## 2. 创建业务流程( Actions )

1. 进入 github 中, 找到 Actions, 创建新的流程


2. 选择  Simple workflow, 然后编辑文件

```yml

name: Deploy GitHub Pages

# 触发条件：在 push 到 master 分支后
on:
  push:
    branches:
      - master

# 任务
jobs:
  build-and-deploy:
    # 服务器环境：最新版 Ubuntu
    runs-on: ubuntu-latest
    steps:
      # 拉取代码
      - name: Checkout
        uses: actions/checkout@master
        with:
          persist-credentials: false

      # 生成静态文件
      - name: Build
        run: npm install && npm run build

      # 部署到 GitHub Pages
      - name: Deploy
        uses: JamesIves/github-pages-deploy-action@releases/v3
        with:
          ACCESS_TOKEN: ${{ secrets.ACCESS_TOKEN }}
          BRANCH: gh-pages
          FOLDER: public
```

3. 点击 `人头像` 下面 `Settings` -> `Developer settings` -> `Personal access tokens ` -> `Generate new token` , 生成新的 token , 复制下 token; 

4. 设置 `ACCESS_TOKEN`, 先找到该项目下的 `Settings` -> `Secrets` -> `Secrets` -> `Add a new secret`
   
```
设置 Name  为: ACCESS_TOKEN
设置 Value 为: token
```
   
5. 完成设置, `clone` 项目时会带有 `.git/workflows/xxx.yml` 文件了, 然后你上传代码后就可以自动编译了

#### 遇到的小问题

```
The deploy step encountered an error: The process '/usr/bin/git' failed with exit code 128 ❌
```
这个是由于 设置 `ACCESS_TOKEN` 中的 Value 值不对导致的

## 3. 分支问题

```shell
// 查看所有分支
git branch -a

// 切换分支
git checkout gh-pages

```
切换分支后, 先使用 `git pull xxx` 拉下代码, 进行操作, 然后再 `git push origin xxx`