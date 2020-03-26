---
layout:       post
title:        Unity3D编辑器端实现协程Coroutine
subtitle:     "Editor的生命周期实现Monobehavior中的Coroutine"
created:      '2020-03-21T03:28:17.672Z'
modified:     '2020-03-21T03:30:44.882Z'
author:       "CharlesZhong"
header-style: text
catalog:      true
...published:    false
tags:
    - Unity3D
    - Unity3D Editor
---

# Unity编辑器内实现Coroutine的跑法

- 目前只是找到的一些小实现方法，主要在Editor下挂起一个委托(delegate)，然后由Application.update去每帧的调用判断是否触发。

Github地址:  
[https://github.com/Charles-Zhong/UnityEditorSimpleCoroutine](https://github.com/Charles-Zhong/UnityEditorSimpleCoroutine)