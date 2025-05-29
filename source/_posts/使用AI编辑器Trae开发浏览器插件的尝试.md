---
title: 使用AI编辑器Trae开发浏览器插件的尝试
date: 2025-05-29 09:13:45
tags:
- AI
- 浏览器插件
categories:
- 折腾
---

最近字节出了一款对标cursor的ai编辑器Trae，我安装的是海外版，目前可以免费使用sonnect 3.5/3.7、gemini等大模型。我尝试用它来实现一个简单的浏览器插件，效果确实很惊艳。我感觉ai在你不熟悉但是你知道有很多人熟悉的领域很有用，可以快速的帮你构建起一个大体的框架。我自己只熟悉一些基本的html、css、js知识，对于浏览器插件相关的开发规范所知甚少。整个项目的框架以及80%以上的代码都是由ai完成的，我再其中更多的充当是的一个提供信息源的角色。

![momo-sync](https://cdn.jsdelivr.net/gh/ryan1iu/ryan1iu.github.io@imgbk/images/20250529092152191.png)

具体来说，这个插件用来将网页中的单词添加到墨墨背单词app中的云词库，从而可以利用其提供的记忆算法来帮助加深记忆。墨墨背单词提供了开放API来帮助开发者实现云词库的同步功能。

<div style="display: flex; gap: 10px;">
  <!-- 左边两张短图 -->
  <div style="display: flex; flex-direction: column; gap: 10px; flex: 1;">
    <img src="https://cdn.jsdelivr.net/gh/ryan1iu/ryan1iu.github.io@imgbk/images/20250529092641967.png" alt="promot" style="width: 100%;" />
    <img src="https://cdn.jsdelivr.net/gh/ryan1iu/ryan1iu.github.io@imgbk/images/20250529093322619.png" style="width: 100%;" alt="图片1">
  </div>

  <!-- 右边一张长图 -->
  <div style="flex: 1;">
    <img src="https://cdn.jsdelivr.net/gh/ryan1iu/ryan1iu.github.io@imgbk/images/20250529093411299.png" style="width: 100%;" alt="图片2">
  </div>
</div>

上图是我对ai的初始指令，在我下达命令之后ai很快的就生成了基本的代码框架，并且可以“正确”的运行。但是最初生成的代码存在很多问题，例如前端的ui非常不协调、对墨墨背单词开放api的调用不符合规范、不能够正确理解墨墨背单词api提供的功能。这些问题都需要我去查明并指出ai的错误，然后给出足够的信息或者示例来让他进行改正。

最终花了一个下午的时间把这个插件的基本功能开发完成了，后面的logo设计也是由ai完成的。感觉目前ai还不能够完全替代人类，但是按照现在的发展速度，可能那一天也很快会到来吧。到那个时候一个人再搭配上各种agent就能组成一个开发团队了，可能个人开发者的职能会慢慢的向产品经理靠拢了。
