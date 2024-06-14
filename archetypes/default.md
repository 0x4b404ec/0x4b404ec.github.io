---

# 创建时间
date: {{ .Date }}

# 设置页面顺序或将文章钉在列表的顶部
# weight: 1

# 多作者
# author: ["Me", "You"] # multiple authors

title: '{{ replace .File.ContentBaseName "-" " " | title }}'            # 标题
tags: ["Normal"]                                                        # 标签
description: "Show Post Description under Title"                        # 描述


UseHugoToc: false                                                       # TOC
showToc: true                                                           # 显示目录
TocOpen: true                                                           # 目录默认展开
hidemeta: false                                                         # 隐藏元元素:日期，阅读时间，作者和可用翻译页面
comments: true                                                          # 显示/隐藏评论功能
disableAnchoredHeadings: false                                          # 禁用锚定标题
#ShowCanonicalLink: false                                               # 为帖子添加规范URL 
#canonicalURL: "https://canonical.url/to/page"
disableHLJS: false                                                      # 关闭代码高亮 # to disable highlightjs 
disableShare: false                                                     # 关闭分享
hideSummary: false                                                      # 隐藏在列表页面中显示的摘要    
ShowBreadCrumbs: true                                                   # 面包屑导航
hideFooter: false                                                       # 将页脚文本隐藏在页面末尾 -like: © 2023 0x4b404ec Powered by Hugo & PaperMod
ShowPostNavLinks: true                                                  # 在单个帖子下添加上一个/下一个帖子建议 # Other Posts suggestion below a post
ShowWordCount: true                                                     # 显示字数
ShowRssButtonInSectionTermList: false                                   # 在章节、术语和列表页面显示RSS图标
ShowReadingTime: true                                                   # 显示阅读时间(估计的时间，以分钟为单位，它需要阅读的内容。)
ShowShareButtons: false                                                 # 在每个帖子的底部显示分享按钮
ShareButtons: ["linkedin", "twitter"]
ShowCodeCopyButtons: true                                               # 代码复制按钮
searchHidden: false                                                     # 是否在搜索中隐藏


draft: false                                                            # 是否为草稿


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



---