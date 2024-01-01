---

# 创建时间
date: 2024-01-01T12:58:16+08:00

# 设置页面顺序或将文章钉在列表的顶部
# weight: 1

# 多作者
# author: ["Me", "You"] # multiple authors

# TOC
UseHugoToc: false
# 显示目录
showToc: true
# 目录默认展开
TocOpen: true

# 隐藏元元素:日期，阅读时间，作者和可用翻译页面
hidemeta: false

# 显示/隐藏评论功能
comments: true

# 禁用锚定标题
disableAnchoredHeadings: false

# 为帖子添加规范URL 
#ShowCanonicalLink: false
#canonicalURL: "https://canonical.url/to/page"

# 关闭代码高亮
disableHLJS: false # to disable highlightjs

# 关闭分享
disableShare: false

# 隐藏在列表页面中显示的摘要
hideSummary: false

# 面包屑导航
ShowBreadCrumbs: true

# 将页脚文本隐藏在页面末尾 -like: © 2023 0x4b404ec Powered by Hugo & PaperMod
hideFooter: false

# Other Posts suggestion below a post
# 在单个帖子下添加上一个/下一个帖子建议
ShowPostNavLinks: true

# 显示字数
ShowWordCount: true

# 在章节、术语和列表页面显示RSS图标
ShowRssButtonInSectionTermList: false

# Show post reading time
# 显示阅读时间(估计的时间，以分钟为单位，它需要阅读的内容。)
ShowReadingTime: true

# Share Buttons on post
# 在每个帖子的底部显示分享按钮
ShowShareButtons: false
ShareButtons: ["linkedin", "twitter"]

# Code Copy Button
# 代码复制按钮
ShowCodeCopyButtons: true

# 封面
cover:
    image: "https://en.ryte.com/magazine/wp-content/uploads/2017/10/1500x800-404EInstellungen-1.png"
    # can also paste direct link from external site
    # ex. https://i.ibb.co/K0HVPBd/paper-mod-profilemode.png
    alt: "404"
    caption: "no found"
    # To use relative path for cover image, used in hugo Page-bundles
    # 为封面图片使用相对路径，在hugo页面中使用
    relative: false
    # To reduce generation time and size of the site, you can disable this feature using
    # 要减少网站的生成时间和大小，可以使用
    #responsiveImages: false
    # To enable hyperlinks to the full image size on post pages, use
    # 若要在发布页面上启用指向完整图像大小的超链接，请使用
    #linkFullImages: true

# 是否在搜索中隐藏
searchHidden: false

# 标题
title: 'Hugo PaperMod主题添加utterances评论'

# 标签
tags: ["Hugo","PaperMod","utterances"]

# 是否为草稿
draft: false

# 描述
description: "Show Post Description under Title"

---



# Hugo PaperMod主题添加utterances评论



## What is 'utterances'

> https://utteranc.es/

[utterances](https://utteranc.es/) 是一款基于 GitHub issues 的开源评论插件. 主打免费开源～

## 安装Utterances

### 申请Github App

1. 打开 [Utterances](https://github.com/apps/utterances) 进行安装

2. 选择要连接的库
   1. 确保选择的库是公开（public）的，否则你的读者将无法查看问题/评论。
   2. 确保在对应库安装应用程序，否则用户将无法发表评论。
   3. 如果你的库是fork的一个分支，在设置页面中并确保issues功能是打开的。

### 修改站点配置文件

1. 在根目录`layouts\partials`下创建`comments.html`文件并编辑（注意不是主题文件夹中的layouts）

   ```html
   {{- /* Comments area start */ -}}
   {{- /* to add comments read => https://gohugo.io/content-management/comments/ */ -}}
   <script src="https://utteranc.es/client.js"
       repo="安装app的个人库名"
       issue-term="title"
       label="Comment"
       theme="github-light"
       crossorigin="anonymous"
       async>
   </script>
   {{- /* Comments area end */ -}}
   
   ```

   其中 repo格式类似于：`0x4b404ec/0x4b404ec.github.io`

2. 在根目录修改`config.yaml`文件, 修改site参数`params.comments`为`true`（同时也要确保page的配置文件中`comments`也为`true`）

   ```ymal
   params:
   	comments: true
   ```