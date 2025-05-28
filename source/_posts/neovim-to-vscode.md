---
title: 从Neovim迁移到VScode的过程记录
date: 2025-05-24 21:20:48
tags:
- Neovim
- VScode
- IDE
catgories:
- 工作流
---

## WHY

没错, 你没看错, 你可能听说过很多如何从Vscode迁移到Vim\Neovim的案例, 但是我的经历有点不太一样. 一般人的经历可能是从文本编辑器->简单的集成环境(VC6\Dev-C++)->现代IDE(Visual Studio\Visual Studio Code). 我的经历是这样的: 文本编辑器->VC6.0->Vim->Neovim.

这要从我在刚开始学计算机的时候接触的一个名为"计算机教育中缺失的一课"的通识课程讲起, 这个课程给予了Vim极高的评价, 我至今还记得其评价Vim为唯一能跟上使用者思维的编辑器. 当然这个评价的确很贴切(这也是我要坚持在vscode中使用vim mode的原因), 但是却让我陷入了与vim的各种配置"斗智斗勇"无法自拔的泥潭. 总之, 为了让自己用的顺手, 我在各种配置上花费了大量的时间, 也花费了很多时间去调试各种插件, 这给了我一种hacker的错觉, 但事实是这些配置非但没有帮助我提升自己的编程能力, 反而还让我把大量的时间浪费在这些与提升编程能力无关的事情上. 综上, 恰逢微软在前两天的Build的大会上宣布要将vscode打造成一个开源的ai编辑器, 我便萌生了从neovim迁移到vscode的打算.

我的计划如下:

1. 首先熟悉vscode的设计理念, 熟悉其使用逻辑, 重点熟悉重要的功能
2. 安装vim插件, 先保证基本的编辑体验, 调整一些快捷键来符合自己的使用习惯, 比如LSP相关的快捷键.
3. 寻找neovim比较好用的插件提供的功能的替代方案
4. ...

这篇文章会进行持续更新和记录我的迁移过程, 直到我觉得满意或放弃迁移(如果放弃这篇文章大概也会删掉吧...).

## Step1. 熟悉VScode

先把官方文档中的**GET STARTED**章节简单过了一遍, 文档中阐述了很多关键词或者关键概念, 无论是对于我能够对VSCode有一个整体的认识和把我, 抑或能够帮助我与他人进行针对性的沟通交流，还是遇到问题时可以定位到具体的部分并进行针对性的检索, 这些概念的熟悉都至关重要。

![layout](https://cdn.jsdelivr.net/gh/ryan1iu/ryan1iu.github.io@imgbk/images/20250528163039239.png)

上面是我从官方文档借来的一张图，非常清晰直观的展示了VScode的用户界面布局。

- Editor - The main area to edit your files. You can open as many editors as you like side by side vertically and horizontally.
- Primary Side Bar - Contains different views like the Explorer to assist you while working on your project.
- Secondary Side Bar - Opposite the Primary Side Bar. By default, contains the Chat view. Drag and drop views from the Primary Side Bar to the Secondary Side Bar to move them.
- Status Bar - Information about the opened project and the files you edit.
- Activity Bar - Located on the far left-hand side. Lets you switch between views and gives you additional context-specific indicators, like the number of outgoing changes when Git is enabled. You can change the position of the Activity Bar.
- Panel - An additional space for views below the editor region. By default, it contains output, debug information, errors and warnings, and an integrated terminal. The Panel can also be moved to the left or right for more vertical space.

左侧的那一排图标被称为Activity Bar，里面的工具可以提供不同的views，这些views所在的Container被称为Primary Side Bar。除了Primary Side Bar，在最右侧还有一个Secondary Side Bar，默认提供Copilot Chat还有Outline views。不同的views可以通过拖拽放置到不同的Container中。最下方的Container被称为Panel，默认包含了一些output、debug information、integrated termianl等等views。

最中间当然也是我最关心的部分就是Editor，也是将来vim extension主要工作的地方。

文档中写的很详细，包括各种views的介绍，还有一些vscode自身的特性等等，我并不想一开始就陷入到阅读这些繁杂的内容当中，我当前的目标是能够保证一个基本的编辑体验，so, Let's get started.

## Step2. 安装VIM Extension

OK, vim插件安装完毕，我遇到的几个核心痛点：

1. 当处于编辑模式中时，vim会劫持CTRL键，从而导致vscode默认提供的快捷键失效
2. 没有LSP相关的快捷键
3. 中英文输入法切换很麻烦

### Problem1

针对我之前提到的vim会劫持Ctrl的问题，文档中提到了一种办法，大概是这样配置：

```json
"vim.handleKeys": {
        "<C-j>": false,
        "<C-p>": false,
        "<C-b>": false
    },
```

意思是vim会停止接管这些按键并交给vscode处理，这样我在编辑的时候就可以通过Ctrl+j键方便的打开终端了，很实用。

### Problem2

阅读了一下vim extension官方文档，有两种办法，一种是改全局快捷键绑定，比如你想设置["g", "g"]为Go to definiton, 直接把默认的f12快捷键换成gg，但是我试了一下，这样做有个问题，就是如果你想在其他地方比如temrinal中输入g时，输不进去了，它会一直等待另一个g，反正是行不太通。

第二种办法就是改vim配置，文档写的很多，大意是需要针对不同的模式进行针对性配置，具体可以看文档。比如我像在normal模式下按下gr键触发show References，我就可以这么配置：

```json
"vim.normalModeKeyBindings": [
        {
            "before": [
                [
                    "g",
                    "r"
                ]
            ],
            "commands": [
                "editor.action.goToReferences"
            ]
        }
    ]
```

意思是说当我在Normal模式下按下gr时，触发editor.action.goToReferences动作。其他的不同模式下的自定义配置都是类似的方法。

除了gr，我还设置了一些其他的供参考：

```json
    "vim.leader": " ",
    "vim.normalModeKeyBindings": [
        
        {
            "before": [
                "leader",
                "e"
            ],
            "commands": [
                "workbench.view.explorer",
            ]
        },
        {
            "before": [
                [
                    "g",
                    "d"
                ]
            ],
            "commands": [
                "editor.action.revealDefinition"
            ]
        },
        {
            "before": [
                [
                    "g",
                    "D"
                ]
            ],
            "commands": [
                "editor.action.revealDeclaration"
            ]
        },
        {
            "before": [
                [
                    "K"
                ]
            ],
            "commands": [
                "editor.action.showHover"
            ]
        },
        {
            "before": [
                [
                    "g",
                    "i"
                ]
            ],
            "commands": [
                "editor.action.goToImplementation"
            ]
        },
    ]
```

### Problem3

我选择在具体的项目中用英文...

对于博客这种必须要写中文的，emmm，反正我更新的也很慢，先忍忍，后面再说。
