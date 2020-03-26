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
    - GitHub Pages

---

# 一 创建和使用

> 以下安装都是在Windows上安装的
可以直接按照Start和Address里面的内容去直接搜索安装就可以了，也可以看后面较详细的步骤来安装，有可能会出现一些问题，也可以看这里有没有需要要解决的问题

## 1.开始
- 1.在<kbd>GitHub</kbd>上创建仓库
- 2.<kbd>Ruby</kbd>语言安装
- 3.<kbd>MSYS2</kbd>安装
- 4.<kbd>DevKit</kbd>
- 5.<kbd>RubyGems</kbd>安装
- 6.<kbd>bundler</kbd>安装
- 7.<kbd>Jekyll</kbd>下载和安装

## 2.地址
- Ruby
  - [Ruby官网](https://rubyinstaller.org/downloads)
  - [Ruby镜像](https://gems.ruby-china.com/)
  - [RubyGems](https:rubygems.org/) &#160;&#160;&#160;&#160;&#160; [RubyGems更具体下载](https://rubygems.org/pages/download)
- MSYS2
  - [MSYS2](https://www.msys2.org/) &#160;&#160;&#160;或者&#160;&#160;&#160;[MSYS2 Github](https://msys2.github.io)
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
1. 现在<kbd>GitHub</kbd>上申请域名，这个需要有自己的账号，没有的话可以申请一个先，然后到里面创建一个仓库，按照设置就能调整出一个静态博客的页面来,不用里面的设置也可以fork大佬的博客模板，改一下仓库名为自己想要的域名，之后改里面的内容就好.  
具体到[Github帮助](https://help.github.com)也有教程

2. 接着到<kbd>Ruby</kbd>的官网下载镜像文件，直接点击安装就好.
![](/img/in-post/post-blog/ruby-download.jpg)
上面的一般需要科学上网
![](/img/in-post/post-blog/ruby-devkit-setup.jpg)

3. 一般安装好Ruby之后会有个窗口弹出来，提示继续安装<kbd>MSYS2</kbd>，会有三个选项，直接第三个继续安装就好
![](/img/in-post/post-blog/msys2-install-1.jpg)
![](/img/in-post/post-blog/msys2-install-2.jpg)
![](/img/in-post/post-blog/msys2-install-3.jpg)
![](/img/in-post/post-blog/msys2-install-4.jpg)

4. 安装<kbd>DevKit</kbd>，DevKit属于Ruby的工具集.
![](/img/in-post/post-blog/devkit-install-1.jpg)
![](/img/in-post/post-blog/devkit-install-2.jpg)
![](/img/in-post/post-blog/devkit-install-3.jpg)
![](/img/in-post/post-blog/devkit-install-4.jpg)
![](/img/in-post/post-blog/devkit-install-5.jpg)
![](/img/in-post/post-blog/devkit-install-6.jpg)

5. 更改<kbd>gem</kbd>的源  
![](/img/in-post/post-blog/gem-source-1.jpg)  
![](/img/in-post/post-blog/gem-source-2.jpg)  
![](/img/in-post/post-blog/gem-source-3.jpg)  
![](/img/in-post/post-blog/gem-source-4.jpg)  
![](/img/in-post/post-blog/gem-source-5.jpg)

6. 安装<kbd>RubyGems</kbd>，RubyGems相当于安装包控制管理中心一样，可以在里面去下载所需要的包.

7. 安装<kbd>bundler</kbd>  
直接打开cmd，然后输入<kbd>gem install bundler</kbd>，之后等待安装完成就好.
![](/img/in-post/post-blog/bundler-install-1.jpg)  

8. 安装<kbd>Jekyll</kbd>，如果前面的步骤都安装好并且安装无误后，使用gem可以快速的下载这个包.  
![](/img/in-post/post-blog/jekyll-install-1.jpg)  
![](/img/in-post/post-blog/jekyll-install-2.jpg)  
![](/img/in-post/post-blog/jekyll-install-3.jpg)  

9. 使用新建博客或者使用模块博客.  
先是基本的新建博客操作，到想要的工程目录下，然后用<kbd>jekyll new xxx</kbd>(xxx是想要的博客名字，比如我的新建博客名是test，就是<kbd>jekyll new test</kbd>).
![](/img/in-post/post-blog/jekyll-use-1.jpg)  
![](/img/in-post/post-blog/jekyll-use-2.jpg)  
然后到工程目录下，就可以看到新建好的Jekyll工程.
![](/img/in-post/post-blog/jekyll-use-3.jpg)  
接着是使用，不管是自己新建好的，还是使用模板的，都可以用<kbd>jekyll serve</kbd>指令来运行工程(如果是使用其他人的模板，可能还得用另一个指令生成Gemfile文件)，更多的操作指令可以到Jekyll官网找.
![](/img/in-post/post-blog/jekyll-use-4.jpg)  
![](/img/in-post/post-blog/jekyll-use-5.jpg)  
用指令运行工程后，看到cmd面板中说在浏览器中输入<kbd>http://127.0.0.1:4000</kbd>可以看到博客的样子.
![](/img/in-post/post-blog/jekyll-use-6.jpg)  
![](/img/in-post/post-blog/jekyll-use-7.jpg)  

## 4.其他快捷操作
- <kbd>cmd</kbd>操作
  - 我是使用的window系统，所以就快捷键<kbd>window+R</kbd>，弹出快速运行窗口，输入<kbd>cmd</kbd>，就可以运行控制台。

- <kbd>cd</kbd>操作
  - 主要用控制台进入文件夹中，比如我的工程在G盘的Github文件夹下，则需要先到G盘下，直接输入<kbd>G:</kbd>，之后到具体文件夹下，输入<kbd>cd G:/Github</kbd>或者<kbd>cd Github</kbd>.

## 二 引用和使用模板
也可以选择使用模板，这里我直接记下Github上的大佬博客，以及一些博客的官网，一般看到英文的都自动跳过了，因为排版实在不适合.一般按照下面找到的都是Github上的工程，fork下来改名、改内容、改域名就好.

大佬博客:  
[Huxpro](https://github.com/Huxpro/huxpro.github.io)&#160;&#160;(我的博客就是使用的这个模板，感谢大佬)

模板官网：  
[Jekyll Theme](http://jekyllthemes.org/)  
百度“jekyll模板”

## 三 域名
创建好自己的博客或者使用了他人的模板，都是可以将博客地址挂在注册的域名上

这里的域名服务商可以选择国外的，也可以选择国内的，如果是国外的话，有一些网址就需要科学上网了，而且填信息时也不方便，所以我就选择了国内的，国内的我搜了一下，选了腾讯云，这里可以自行百度，都可以搜到，下面就记一些使用(默认是已经有了一个<kbd>Github Pages</kbd>的仓库)。

国外的：  
[Godaddy](https://sg.godaddy.com/)&#160;&#160;&#160;&#160;
[gandi](https://www.gandi.net/)&#160;&#160;&#160;&#160;
[Google domains](https://domains.google.com/)&#160;&#160;&#160;&#160;

前面的使用主要就是注册腾讯云，然后在里面进行搜索域名，购买，按照里面的操作基本都看的明白，主要是对域名进行解析的时候就看不太懂了，所以记录一下。  
一个是对主机记录的调整，另一个是对记录值的调整。
![](/img/in-post/post-blog/domain-3.jpg)
![](/img/in-post/post-blog/domain-4.jpg)

## 四 完成  
最后放上我博客的完成版  
Github的仓库地址：  
[http://github.com/Charles-Zhong/xiaohao.github.io](http://github.com/Charles-Zhong/xiaohao.github.io)  
挂在了域名上的地址：  
[xiaohao.website](xiaohao.website)