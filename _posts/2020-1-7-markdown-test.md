---
layout:     post
title:      Markdown编写测试
subtitle:   " A Test"
created:    '2020-01-07T03:46:00.753Z'
modified:   '2020-01-07T03:46:24.287Z'
author:     "CharlesZhong"
header-style: text
catalog: true
published:  false
---

# Markdown编写测试

## 脚注
   Numeric[^1]

## 编写代码
   ~~~
   function F(a,b)
   {
     return a+b;
   }
   ~~~

   ```
   a
   ```

## 引入图片
   ![icon](/img/home-bg.jpg)

   ![](/img/tag-bg.jpg)

## 加入表情包
   可以加入表情::+1: 和::smile:,使用代码段：\:+1\: 和 \:smile\:
   [:+1:](https://baidu.com/)

   [^1]:Numeric Content

## Task
   - [ ] ok
     - [ ] o
     - [ ] k
   - [ ] not

## List
   - list1
     - list2
    
## code
   `test`

## HTML
   <mark>text</mark>

   <kbd>mark</kbd>

   <details>
     <summary>SUM</summary>
     detail content
   </details>

## 链接本博客的其他文章
   可以看我其他的博客[拽写博客]({% post_url 2020-1-8-write_blog %})