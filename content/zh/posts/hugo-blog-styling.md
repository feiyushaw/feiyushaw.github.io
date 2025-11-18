---
title: "Hugo + Blowfish | 自装样式记录"
date: 2024-03-20T10:00:00Z
draft: false
tags: ["博客相关"]
categories: ["tech"]
summary: "记录一下自己装修博客的过程，这次搭博客真的蛮快的"
---

本文主要是想记录一下自己装修博客的过程，这次搭博客真的蛮快的，一天之内我就搞得七七八八了，这得归功于网上这么多的分享记录，所以我也来贡献一份自己的记录啦。

2024.04.04 编辑：

- 更新了【修改链接样式】部分的代码，最新版本代码限制了只在正文和目录里会应用样式。
- 更新了友链卡片的css代码，原代码关于设置链接样式的部分其实没有生效，debug的时候发现后就赶紧改过来了（

2024.05.24 编辑：

- 给以前的装修加上效果图

## 搭建博客 #

我已经确定博客框架选择Hugo，因为最多人用（虽然我也不太确定，但是身边人确实大部分都用的是Hugo），这样Debug方便，抄别人的装修作业也很方便（x）

### 挑选主题 #

我个人觉得搭建博客第一步就是先选主题，因为每个主题都会有自己的教程，跟着主题的官方教程走，出错的概率非常小。我挑选主题时最看重三个要素：

- **使用人数多不多。** 这个优先级最高，因为虽然我是程序员，但我也不太喜欢花太多时间和精力在Debug或者自己造轮子上面。如果选择很多人都在用的主题，自己遇到的问题大概率别人已经遇到并解决了。
- **完美支持Markdown，并能优雅美丽地展示Markdown。** 有些博客虽然很美丽，但只会美丽地展示一部分的Markdown元素，这些博客都会直接被我pass掉。
- **有提供足够的自定义空间**。我知道受欢迎的博客肯定也是经过多次设计迭代、被市场证明的美丽博客，但是不可能要求它全部内容都符合自己的审美，所以肯定是要自己改动一部分的。

### 版本和开发工具 #

写此博客时，我的配置如下：

Hugo版本：`v0.145.0`

BlowFish版本：`2.84.0`

命令行工具：Warp

开发工具：Webstorm

操作系统：MacOS

如果直接复制我文中提到的代码后出错了，记得查看版本是否一致哦^ ^

## 装修博客 #

### 搭建首页 #

我把首页文件放在`layouts/index.html`，BlowFish文档中说自定义首页文件要放在`layouts/partials/home/custom.html`，我实验了一下，效果是一样的。以下是我的首页代码：

```html
{{ define "main" }}  
<main class="w-full mt-20 px-6">  
    <section class="flex flex-col lg:flex-row items-center justify-between gap-16 max-w-6xl mx-auto">  
  
        <!-- 图片区块 -->  
        <div class="lg:w-1/2 order-first lg:order-none shrink-0">  
            {{ with .Params.images }}  
            {{ range first 1 . }}  
            <img width="650" src="{{ . }}" alt="Background"  
                 class="mx-auto rounded-xl shadow-md object-contain" />  
            {{ end }}  
            {{ end }}  
        </div>  
  
        <!-- 文字区块 -->  
        <div class="lg:w-1/2 text-right flex flex-col justify-center">  
            {{ with .Params.title }}  
            <h1 class="text-4xl lg:text-5xl font-semibold mb-4 leading-tight max-w-md mx-auto lg:mx-0">{{ . }}</h1>  
            {{ end }}  
  
            {{ with .Params.subtitle }}  
            <h2 class="text-sm uppercase tracking-widest font-bold text-gray-500 mb-6 max-w-md mx-auto lg:mx-0">{{ . }}</h2>  
            {{ end }}  
  
            {{ with .Params.description }}  
            <p class="text-lg text-gray-700 mb-8 mt-6 leading-relaxed max-w-md mx-auto lg:mx-0 whitespace-nowrap">{{ . | markdownify }}</p>  
            {{ end }}  
        </div>  
  
    </section>  
</main>  
{{ end }}
```
### 修改字体 #

我使用的字体列表如下：

- 中文标题：花园明朝体
- 中文正文：方正书宋体
- 英文标题：Fraunces
- 英文正文：Commissioner

每次下载字体时最好下载GBK版，意思是简繁体都适用，以及**最好**把字体格式转换为WOFF2，性能最佳。

在我修改完字体以后，页面中所有文字部分莫名变窄了，询问了GPT以后，它猜测是因为主题内定义的`max-w-prose`或`max-w-*`限制了容器宽度，我检查了一下，果然发现主题默认写了`max-w-prose`，它会把正文区域的最大宽度限制在**65ch**（大约65个字符宽），于是我加了个代码取消掉了这个限制：

```css
.max-w-prose {  
  max-width: none !important;  
}
```
## 总结 #

这么写下来，发现自己确实没怎么改动原主题呢，但是样式却跟原主题几乎两模两样，这就是UI的魅力吧！