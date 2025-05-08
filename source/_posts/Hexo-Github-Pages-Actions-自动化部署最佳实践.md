---
title: Hexo + Github Pages/Actions 自动化部署实践
date: 2025-05-08 12:16:35
tags:
  - Hexo
categories:
  - 工作流
---

本文不会介绍如何安装以及配置Hexo,而是着重于对我摸索出的一套Hexo管理和自动化部署的实践流程进行梳理和总结.阅读完本文,你将会学到:

- 如何使用submodules管理主题
- 如何实现在维护自己的个人主题配置的同时能够同步主题上游的最新改动.
- 如何使用Github Action实现自动化部署

## 测试环境

我的当前的机器环境为:

```bash
❯ hexo -v
hexo: 7.3.0
hexo-cli: 4.3.2
os: linux 5.15.167.4-microsoft-standard-WSL2 Ubuntu 24.04.1 LTS
node: 22.15.0
```

本文假设你已经安装并配置好了Hexo环境并且熟悉基本的git和github操作.如果不熟悉的话推荐先阅读一下[beej's guide to git](https://beej.us/guide/bggit/)

## 主题管理

本节介绍如何添加并维护一个主题的配置.如果你不打算使用第三方主题,可以跳过.
假设我们希望使用[hexo-theme-cactus](https://github.com/probberechts/hexo-theme-cactus)作为博客的主题.首先将这个主题仓库fork一份到自己的Github仓库,然后将fork出来的仓库以git submodules的方式添加到本地:

```bash
git submodules add https://github.com/ryan1iu/hexo-theme-cactus.git themes/cactus
```

然后进入主题文件夹,创建一个新的分支比如dev,用来保存自己的自定义配置.

```bash
git switch -c dev
```

在我们完成自己的配置之后就可以将修改commit并push到远程仓库

```bash
git add .
git commit
git push -u origin
```

### 与上游保持同步

为了能够在后续主题更新时能够同步更新,将原主题仓库添加为上游仓库:

```bash
git remote add upstream https://github.com/probberechts/hexo-theme-cactus.git
```

之后可以通过pull来同步主题的更改.

```bash
git pull upstream master
```

## 自动部署

本节介绍如何利用Github Action实现自动化构建和部署

在本地创建一个工作流,新建.github/workflows/deploy.yml,写入以下内容(需要根据你自己的环境进行必要的调整):

```yml
name: Deploy Hexo site to GitHub Pages

# 触发条件：每次推送到 master 分支时触发部署
on:
  push:
    branches:
      - master

jobs:
  build:
    runs-on: ubuntu-24.04

    permissions:
      # Give the default GITHUB_TOKEN write permission to commit and push the
      # added or changed files to the repository.
      contents: write

    steps:
      - name: Checkout repository
        uses: actions/checkout@v4
        with:
          fetch-depth: 0

      - name: Update submodules
        run: |
          git submodule update --init --recursive  # 拉取并初始化子模块

      # 安装 Node.js 和 Hexo
      - name: Set up Node.js
        uses: actions/setup-node@v2
        with:
          node-version: "22.15.0"

      - name: Install dependencies
        run: |
          npm install hexo-cli -g
          npm install

      # 构建 Hexo 站点
      - name: Build the site
        run: hexo generate

      # 部署到 GitHub Pages
      - name: Deploy 🚀
        uses: JamesIves/github-pages-deploy-action@v4
        with:
          branch: gh-pages
          folder: public
          clean: true
          single-commit: true
```

保存修改并提交.

### 创建远程仓库并同步

手动在GitHub创建一个仓库来存放博客源码,比如我的博客源码仓库为`https://github.com/ryan1iu/ryan1iu.github.io.git`,将本地博客源码同步到远程仓库

```bash
git remote add origin https://github.com/ryan1iu/ryan1iu.github.io.git
git push -u origin master
```

在每次推送到master分支后都会出发我们上面设置好的任务,具体来说,该任务会自动进行:

1. 创建一个ubuntu 24.04虚拟环境.
2. clone当前仓库.
3. 初始化并更新子模块.
4. 设置Nodejs环境.
5. 根据package.json安装依赖.
6. 执行hexo generate构建站点并输出到public文件夹.
7. 将public文件夹下的文件部署到gp-pages分支(这个分支时github actions自动创建的,无需手动创建).

### Github pages设置

在博客源码仓库的设置页面找到Pages设置页,将Build and deployment设置为Deploy from a Branch,Branch选择gh-pages,Path设置为/(root).
之后就可以visit site了.

## 写在最后

文章写的比较"粗糙",旨在提供一个大致的思路.如果有什么问题欢迎在评论区留言讨论.如果你有更好的办法,欢迎不吝赐教.
