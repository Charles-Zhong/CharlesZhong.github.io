---
layout:     post
title:      使用GitHub和jekyll打造自己的博客
subtitle:   " Just Do It"
created:    '2020-01-07T03:04:08.458Z'
modified:   '2020-01-07T03:05:01.214Z'
author:     "CharlesZhong"
header-style: text
catalog: true
tags:
    - Ruby
    - Jekyll

---

# 创建和使用

> 以下安装都是在Windows上安装的
可以直接按照Start和Address里面的内容去直接搜索安装就可以了，也可以看后面较详细的步骤来安装，有可能会出现一些问题，也可以看这里有没有需要要解决的问题

## 1.开始
- 1.在GitHub上申请域名以及建立一个仓库
- 2.Ruby语言安装
- 3.MSYS2安装
- 4.DevKit
- 5.RubyGems安装
- 6.bundler安装
- 7.Jekyll下载和安装

## 2.地址
- Ruby
  - [Ruby官网](https://rubyinstaller.org/downloads)
  - [Ruby镜像](https://gems.ruby-china.com/)
  - [RubyGems](https:rubygems.org/) &#160;&#160;&#160;&#160;&#160; [RubyGems更具体下载](https://rubygems.org/pages/download)
- Jekyll
  - [Jekyll官网英文版](https://jekyllrb.com/) &#160;&#160;&#160;&#160;&#160; [Jekyll官网中文版](https://jekyll.comptechs.cn/)
  - [Jekyll模板](https://jekyllthemes.dev/)
  - [Jekyll官网文档](https://jekyllrb.com/docs/usage/)
- GitHub
  - [GitHub按钮集](https://ghbtns.com/)
- YAML
  - [具体语法](https://yaml.org/)
- HTML CSS LESS JS等
可自行网上搜索，目前暂时用不到，但是后面要改自己博客的样子的时候，就需要到了，如果只是需要一个博客的模板而已，就没必要了解那么深入

## 3.详细步骤
1. 现在GitHub上申请域名，这个需要有自己的账号，没有的话可以申请一个先，然后到界面中.

2. 接着到Ruby的官网下载镜像文件，直接点击安装就好.
![](/img/in-post/post-blog/ruby-download.jpg)
上面的一般需要科学上网
![](/img/in-post/post-blog/ruby-devkit-setup.jpg)

3. 一般安装好Ruby之后会有个窗口弹出来，提示继续安装MSYS2，会有三个选项，直接第三个继续安装就好
![](/img/in-post/post-blog/msys2-install-1.jpg)
![](/img/in-post/post-blog/msys2-install-2.jpg)
![](/img/in-post/post-blog/msys2-install-3.jpg)
![](/img/in-post/post-blog/msys2-install-4.jpg)

4. 安装DevKit，DevKit属于Ruby的工具集.

5. 安装RubyGems，RubyGems相当于安装包控制管理中心一样，可以在里面去下载所需要的包.

6. 安装bundler

7. 安装Jekyll，如果前面的步骤都安装好并且安装无误后，使用gem可以快速下载包.

## 4.其他快捷操作

# 引用和使用第三方模板
当然可以直接下载其他人