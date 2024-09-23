---
layout: post
title: Android内核项目简介
author: SmaliYuu
tags: 技术
excerpt_separator: <!--more-->
---

Android开源系统项目在整体上包含AOSP、内核两大块。其中AOSP主要是侧重Android系统层、应用层的实现，内核部分就是专门为Android适配的Linux内核，其项目组织结构和标准的linux内核项目没有太大差异。

<!--more-->

在近几个Android版本中，Google为Android内核引入了一些新概念。

## GKI
Generic Kernel Image 通用内核映像。支持GKI的设备可以单独编译内核并更新到设备，而不需要和驱动程序一起编译。


## 为什么设计GKI
在以前的Android版本，内核代码通常由芯片厂和手机厂共同开发维护，其中很多特定外设的驱动代码需要编译打包进内核镜像，这使很多手机内核停留在手机发布时的版本。这样的窘境一方面导致了新的内核补丁无法在这些设备上生效；另一方面，新版的Android系统会使用新的内核特性，碎片化的内核版本也导致上层Android系统维护成本变高。为了解决这个问题，谷歌将内核和驱动之间调用接口抽离出来，在内核接口不变的情况下，内核维护的组织可以单独对内核进行维护并发布新版本，而新版本的内核依然能够与原有的驱动正常工作。这里所说的内核和驱动相互调用的接口就叫KMI。

## 国内厂商和GKI
目前有相当一部分厂商选择直接在Google发布的GKI上进行驱动适配和系统开发，节约了大量的开发成本。

## KMI
Kernel Module Interface 内核模块接口。


## 设备上通用内核的范围和版本号说明
这一段引用自KernelSU的说明文档：[https://kernelsu.org/zh_CN/guide/installation.html](https://kernelsu.org/zh_CN/guide/installation.html)  

KMI 全称 Kernel Module Interface，相同 KMI 的内核版本是兼容的 这也是 GKI 中“通用”的含义所在；反之，如果 KMI 不同，那么这些内核之间无法互相兼容，刷入与你设备 KMI 不同的内核镜像可能会导致死机。  

具体来说，对 GKI 的设备，其内核版本格式应该如下：
```
KernelRelease :=
Version.PatchLevel.SubLevel-AndroidRelease-KmiGeneration-suffix
w      .x         .y       -zzz           -k            -something
```

其中，w.x-zzz-k 为 KMI 版本。例如，一个设备内核版本为5.10.101-android12-9-g30979850fc20，那么它的 KMI 为 5.10-android12-9；理论上刷入其他这个 KMI 的内核也能正常开机。  

请注意，内核版本中的 SubLevel 不属于 KMI 的范畴！也就是说 5.10.101-android12-9-g30979850fc20 与 5.10.137-android12-9-g30979850fc20 的 KMI 相同！