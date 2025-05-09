---
title: 为Cactus引入霞鹜文楷并使用CDN加速访问
date: 2025-05-09 09:25:42
tags:
  - cactus
categories:
  - 折腾
---

[霞鹜文楷](https://github.com/lxgw/LxgwWenKai)是我本人比较喜欢的一款开源中文字体,兼有仿宋和楷体的特点.
本文介绍如何为Cactus主题添加霞鹜文楷并为其启用CDN加速.

## 字体选择

因为中文字体普遍包含数千到数万个汉字,所以字体的文件体积较大. 所以为了优化字体的加载体验,我最开始选择了其提供的LxgwWenKai-Lite版本,但是后来发现它也有提供LxgwWenKai-Screen版本,因为默认的字体的字重比较细,不太适合屏幕阅读, Screen对自动进行了一些调整使其能够带来更好的阅读体验. 而且我发现Screen版本的字体大小和Lite版本差不多大, 因此便选择使用Screen版本.

## 使用CDN加速

我们借助[lxgwwenkai-webfont](https://github.com/chawyehsu/lxgw-wenkai-webfont)项目来进行. 这个项目将tff转换为了更适合网站使用的woff2,并提供了CDN包.

### 修改主题配置

打开主题下的\_config.yml, 找到cdn配置的部分,添加以下内容:

```yml
cdn:
  enable: true
  lxgw_wenkai: https://cdn.jsdelivr.net/npm/lxgw-wenkai-screen-webfont@1.1.0/lxgwwenkaiscreen.css
```

然后找到layout/\_partial/styles.ejs, 在最下方添加如下内容:

```ejs
<% if (isCdnEnable('lxgw_wenkai')) { %>
  <%- getCdnLink('lxgw_wenkai', {preload: true}) %>
<% } else { %>
  <link
    rel="preload"
    href="<%- url_for('/lib/lxgw-wenkai-screen-webfont-1.1.0/package/lxgwwenkaiscreen.css') %>"
    as="style"
    onload="this.onload=null;this.rel='stylesheet'"
  />
  <noscript
    ><link
      rel="stylesheet"
      href="<%- url_for('/lib/lxgw-wenkai-screen-webfont-1.1.0/package/lxgwwenkaiscreen.css') %>"
  /></noscript>
<% } %>
```

如果你不想开启CDN加速,可以将这个包下载到本地的主题下的lib目录下. [下载地址](https://www.jsdelivr.com/package/npm/lxgw-wenkai-screen-webfont),注意下载的版本,我选择的是1.1.0版本,因为用的人最多,我觉得可能加速效果会好一些吧.

找到source/css/\_variables.styl, 设置霞鹜文楷优先被加载.

```yml
$font-family-body = "LXGW WenKai Screen", "Menlo", "Meslo LG", monospace
```

## 效果预览

```bash
hexo generate
hexo server
```

即可预览最终效果. 无论你有问题或建议,欢迎在下方留言讨论.
