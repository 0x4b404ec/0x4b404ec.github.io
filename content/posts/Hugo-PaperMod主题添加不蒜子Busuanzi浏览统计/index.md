---

# 创建时间
date: 2024-01-03T22:17:42+08:00

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
    image: "https://iknowyouask.com/wp-content/uploads/2019/06/cj-busuanzi.png"
    # can also paste direct link from external site
    # ex. https://i.ibb.co/K0HVPBd/paper-mod-profilemode.png
    alt: "404"
    caption: "busuanzi"
    # To use relative path for cover image, used in hugo Page-bundles
    # 为封面图片使用相对路径，在hugo页面中使用
    relative: false
    # To reduce generation time and size of the site, you can disable this feature using
    # 要减少网站的生成时间和大小，可以使用
    responsiveImages: false
    # To enable hyperlinks to the full image size on post pages, use
    # 若要在发布页面上启用指向完整图像大小的超链接，请使用
    linkFullImages: false

# 是否在搜索中隐藏
searchHidden: false

# 标题
title: 'Hugo PaperMod主题添加不蒜子Busuanzi浏览统计'

# 标签
tags: ["Hugo","PaperMod","Busuanzi","不蒜子","浏览统计"]

# 是否为草稿
draft: false

# 描述
description: "Hugo PaperMod主题中使用 不蒜子Busuanzi 进行站点的浏览统计"

---



# 不蒜子Busuanzi浏览统计

> [Busuanzi](https://busuanzi.ibruce.info/)

我是使用了Git的子模块进行主题的管理，所以为了保持子模块的完整性，所有修改都在Hugo框架中进行。

## head.html

复制

`head.html`文件位于`[Hugo_blog]/themes/PaperMod/layouts/partials/head.html` 

至

`[Hugo_blog]/layouts/partials/head.html`

**(如没有文件夹需要自己创建)**

搜索`{{- /* Styles */}}` 在其上一行添加代码：

```html
{{- if .Site.Params.busuanzi.enable -}}
  <script async src="//busuanzi.ibruce.info/busuanzi/2.3/busuanzi.pure.mini.js"></script>
  <meta name="referrer" content="no-referrer-when-downgrade">
{{- end -}}
```



## footer.html

复制

`footer.html`文件位于`[Hugo_blog]/themes/PaperMod/layouts/partials/footer.html` 

至

`[Hugo_blog]/layouts/partials/footer.html`

**(如没有文件夹需要自己创建)**

在footer标签中，添加新代码：

```html
{{ if .Site.Params.busuanzi.enable -}}
<div class="busuanzi-footer">
<span id="busuanzi_container_site_pv">
    本站总访问量<span id="busuanzi_value_site_pv"></span>次
</span>
<span id="busuanzi_container_site_uv">
    本站访客数<span id="busuanzi_value_site_uv"></span>人次
</span>
</div>
{{- end -}}
```



## single.html

复制

`single.html`文件位于`[Hugo_blog]/themes/PaperMod/layouts/_default/single.html` 

至

`[Hugo_blog]/layouts/_default/single.html`

**(如没有文件夹需要自己创建)**

搜索`{{- partial "post_canonical.html" . -}}`在其下一行添加代码：

```html
{{ if .Site.Params.busuanzi.enable -}}
<div  class="meta-item">&nbsp·&nbsp
  <span id="busuanzi_container_page_pv">本文阅读量<span id="busuanzi_value_page_pv"></span>次</span>
</div>
{{- end }}
```



## Config.yaml

启动Busuanzi统计功能，返回本目录在配置文件params中添加参数

```yaml
params:  
    busuanzi:
        enable: true # 是否启动Busuanzi统计
```



