---
title: Hexo-NexT搭建博客
date: 2016-04-01 16:46:55
updated: 2016-04-02 14:58:56
categories: Learning
tags: [Hexo, NexT]
---

# 前言

一直在找一个风格简单一点的博客，直到前几天看到Hexo和它的NexT主题，简单的操作和大方的风格，再加上支持markdown语法，不禁就喜欢上了，和我想象中的如此契合。

<!-- more -->

# 安装

- 安装[git](https://git-scm.com/download/)
- 安装[Node.js](https://nodejs.org/en/)

以上两步windows系统下可直接下载文件进行安装的。


- 安装Hexo

```shell
$ npm install -g hexo-cli
```

过程中可能会卡住或报错，不过耐心等待或多试几次应该就行了。

安装步骤可详见：[Hexo.io](https://hexo.io/docs/)

步骤都很简单，报错时不必失望或紧张，总会有解决办法的。

接下来就可以创建自己的博客了（`xiaofeig.github.io`是存放博客的文件夹）：

```shell
$ hexo init xiaofeig.github.io
$ cd xiaofeig.github.io
$ npm install
```

本地启动，启动成功后即可在浏览器上访问`http://localhost:4000`：

```shell
$ hexo server
```

# 配置

## Git


### [GitHub](https://github.com/)上新建仓库


![](/imgs/KF3`~UM~}Y~I`A1914LGEMH.png)


### 初始化本地仓库

```shell
$ cd xiaofeig.github.io
$ git init
$ git add .
$ git commit -m 'create my blog'

```

创建一个blog分支，用于保存博客的一些文件。

```shell
$ git checkout -b blog
```

完成后，这是站点配置`xiaofeig.github.io/_config.yml`中设置的选项，发布的是主分支master：

```yml
deploy:
  type: git
  repo: git@github.com:xiaofeig/xiaofeig.github.io.git
  branch: master
```

```
$ git remote add origin git@github.com:xiaofeig/xiaofeig.github.io.git
```


### 安装插件

发布需要的插件`hexo-deployer-git`

```shell
$ cd xiaofeig.github.io
$ npm install hexo-deployer-git --save
```

提交博客代码

```shell
$ git add .
$ git commit -m 'update'
$ git push -u origin blog
```

生成静态页面并发布，可访问[博客](http://xiaofeig.github.io/)试试：

```shell
$ hexo g -d
```


发布一篇文章：

```shell
$ hexo new "test"
$ vim source/_posts/test.md
```

```shell
$ git add .
$ git commit -m 'update'
$ git push -u origin blog
$ hexo g -d
```


## NexT主题

详细配置可查看[NexT](http://theme-next.iissnan.com/)

### 下载主题

```shell
$ cd xiaofeig.github.io
$ git clone https://github.com/iissnan/hexo-theme-next themes/next
```

### 设置主题

#### 站点配置

站点配置文件：`xiaofeig.github.io/_config.yml`

```shell
# 博客标题
title: Xiaofei's Blog
subtitle: Hi, xiaofei...
# 签名
description: 因为无能为力，所以顺其自然。因为心无所恃，所以随遇而安。
# 作者
author: xiaofeig
# 语言（中文）
language: zh-Hans
# 头像（在source文件夹下添加)
avatar: /images/avatar_monochrome.jpg
# 主题
theme: next
```

#### 主题配置

主题配置文件：`xiaofeig.github.io/themes/next/_config.yml`

```yml
# 博客创建时间
since: 2016
# 菜单
menu:
  home: /
  categories: /categories
  about: /about
  archives: /archives
  tags: /tags
# NexT主题外观
scheme: Mist
# Baidu Analytics ID
baidu_analytics: 7e2ee4ffda2eb8e51b51ee17456xxxxx
# Duoshuo ShortName
duoshuo_shortname: xxxxxxxx
# Show number of visitors to each article.
# You can visit https://leancloud.cn get AppID and AppKey.
leancloud_visitors:
  enable: true
  app_id: qiJ3JCiGjSvj0ypMMgf8Uj68-gzGxxxxx
  app_key: dJAUtvvyq14ljFognfJxxxxx
# 打赏（在source文件夹下添加)
reward_comment: 请博主喝咖啡，谢谢。
wechatpay: /images/wechat_reward.gif
alipay: /images/alipay_reward.gif
```

#### 添加缺少页面

- 标签页面

```shell
$ hexo new page tags
$ vim source/tags/index.md
---
title: 标签
date: 2016-04-01 13:43:39
type: "tags"
comments: false
---
```

- 分类页面

```shell
$ hexo new page categories
$ vim source/categories/index.md
---
title: 分类
date: 2016-04-01 13:48:00
type: "categories"
comments: false
---
```

- 关于页面

```shell
$ hexo new page about
$ vim source/about/index.md
---
title: 关于
date: 2016-04-01 14:57:42
type: "about"
comments: false
---
```

# 简单用法

暂时还是学习中，待完善。

- [学习链接一](http://www.zhihu.com/question/21193762)
- [学习链接二](http://www.cnblogs.com/zhcncn/p/4097881.html)

# 体会

自己对于新鲜事物总是抱着矛盾的心理，不知道大多数人都是这样，还是自己属于少数。自己在抗拒的同时又期待享受它带给自己的刺激和乐趣，但多数情况下我愿意做一些尝试，毕竟能学到新鲜事物，即使可能会遇上一些暂时的挫败感。

# 更新日志

- 2016-04-01 16:46:55 星期五 初稿
- 2016-04-02 15:01:22 星期六 调整格式，初次发布
