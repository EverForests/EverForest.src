---
title: Hexo Butterfly 配置指南
tags: [美化, 指南]
categories: "指南"
cover: https://raw.githubusercontent.com/EverForests/PicGo/master/shu.png
description: "博客建站指南"
mathjax: true
---



## 基础环境配置

Hexo框架的下载使用以及部分主题方面的美化，主要参照[Nick Xu 建站指南](https://nickxu.me/2022/02/13/Hexo + Butterfly 建站指南（〇）前言/?highlight=butter)。

正文

- [Hexo + Butterfly 建站指南（〇）前言](https://nickxu.me/2022/02/13/Hexo + Butterfly 建站指南（〇）前言/)
- [Hexo + Butterfly 建站指南（一）Hexo 框架](https://nickxu.me/2022/02/13/Hexo + Butterfly 建站指南（一）Hexo-框架/)
- [Hexo + Butterfly 建站指南（二）部署至个人站点或 GitHub Pages](https://nickxu.me/2022/02/14/Hexo-Butterfly-建站指南（二）部署至个人站点或-GithubPages/)
- [Hexo + Butterfly 建站指南（三）Butterfly 主题](https://nickxu.me/2022/02/17/Hexo-Butterfly-建站指南（三）Butterfly-主题/)
- [Hexo + Butterfly 建站指南（四）Twikoo 评论系统](https://nickxu.me/2022/02/19/Hexo-Butterfly-建站指南（四）Twikoo-评论系统/)
- [Hexo + Butterfly 建站指南（五）日常写作](https://nickxu.me/2022/02/20/Hexo-Butterfly-建站指南（五）日常写作/)
- [Hexo + Butterfly 建站指南（六）PicGO + Gitee 图床（已废弃）](https://nickxu.me/2022/02/23/Hexo-Butterfly-建站指南（六）PicGO-+-Gitee-图床/)
- [Hexo + Butterfly 建站指南（七）阿里云 OSS 图床](https://nickxu.me/2022/03/28/Hexo-Butterfly-建站指南（七）阿里云-OSS-图床/)
- [Hexo + Butterfly 建站指南（八）使用 KaTeX 数学公式](https://nickxu.me/2022/04/17/Hexo-Butterfly-建站指南（八）使用-KaTeX-数学公式/)

这里讲得足够详细，关于环境配置，可以参照一二章，关于美化部分，可以看看第三章，其它部分酌情参考即可（留出更多的DIY空间～）。

## 主题配置

主题方面的配置主要围绕[butterfly 官方文档](https://butterfly.js.org/posts/21cfbf15/)进行，作者为我们预留了许多美化空间。

> 📚 文档目录
>
> 🚀 [快速开始](https://butterfly.js.org/posts/21cfbf15/) - 📑 [主题页面](https://butterfly.js.org/posts/dc584b87/) - 📌 [主题配置-1](https://butterfly.js.org/posts/4aa8abbe/) - ⚔️ [主题配置-2](https://butterfly.js.org/posts/ceeb73f/) - ❓ [主题问答](https://butterfly.js.org/posts/98d20436/) - ⚡️ [进阶教程](https://butterfly.js.org/posts/4073eda/)

在快速开始中，完成主题以及渲染插件的下载。

在主题页面中，学习丰富博客的结构，完善导航栏。除了基本post页面，还有标签页、分类页、友链、娱乐页等。

主题配置1和2侧重于对博客细节进行调整，这里会单独来讲。

剩下的两个部分主要解决一些配置过程中的问题，同时谈论了一些可进一步拓展的方向。

## 配置索引

> 综合上述主题配置1和2，我基于配置源代码做了一些索引以便后续便捷地调整。

### 基础设置索引

+ 语言与站点基本资料：_config.yml > #Site
+ 导航栏设置：Navigation bar settings
+ 代码块设置：Code Blocks
+ 社交图标：Social Settings
+ 图像
  + 头像：avatar
  + 顶部图：top_img
  + 文章封面：cover
+ 主页文章展示
  + 展示meta：post_meta
  + 节选：index_post_content
  + 锚点：anchor
  + 内容图片描述：photofigcaption
+ 文章页内部
  + 复制版权：copy settings
  + 目录：toc
  + 版权：post_copyright
  + 打赏：reward
  + 编辑按钮：post_edit
  + 相关文章：related_post
  + 末尾跳转方式：post_pagination
  + 过期提醒：noticeOutdate
+ 页脚设置：Footer Settings
+ 侧边栏设置：aside
  + 访问人数：busuanzi
  + 运行时间：runtimeshow
  + 最新评论：newest_comments
  + [自定义边栏](https://butterfly.js.org/posts/ea33ab97/)
+ 右下角按钮：Botton right button

+ [html标签外挂](https://butterfly.js.org/posts/4aa8abbe/#標籤外掛（Tag-Plugins）)

### 增值服务索引

+ 数学公式渲染：Math
+ 搜索：search
+ 分享：Share System
+ 评论：Comments System
+ 在线聊天：Chat Services
+ 分析统计：Analysis
+ 广告：Advertisement
+ 网站/站点验证：Verification
+ 字数统计：wordcount
+ 图片大图查看：Lightbox
+ Pjax：pjax
+ 弹窗：Snackbar
+ 预加载：instantpage
+ 中英文间加空格：pangu
+ 图片懒加载：Lazyload
+ 进阶网站：PWA
+ 开图：Open Graph
+ CSS前缀：css_prefix
+ 特效（css or js）注入：Inject
+ 加速：CDN



### 特效美化索引

+ 自定义主题色：Theme color for customize
+ 主页top_img显示大小：index_site_info_top
+ 文字左右对齐：text_align_justify
+ 网站（文章展示部分）背景：Website Background
+ 页脚背景：footer_bg
+ 打字效果：Typewriter Effect
+ 背景特效：Background effects
+ 鼠标点击效果：Mouse Click
+ markdown页面美化：Beautify
+ 自定义字体和字体大小：Global font settings
+ 水平分割线图标设置：hr_icon
+ 网站副标题（打字部分）：subtitle
+ 页面加载动画：preloader



