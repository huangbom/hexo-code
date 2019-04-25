---
title: Hexo 使用手册
date: 2017-11-21 10:59:47
category: hexo
---
Welcome to [Hexo](https://hexo.io/)! This is your very first post. Check [documentation](https://hexo.io/docs/) for more info. If you get any problems when using Hexo, you can find the answer in [troubleshooting](https://hexo.io/docs/troubleshooting.html) or you can ask me on [GitHub](https://github.com/hexojs/hexo/issues).

## Quick Start

** Create a new post **

``` bash
$ hexo new "My New Post"
```

More info: [Writing](https://hexo.io/docs/writing.html)

**Run server** 

``` bash
$ hexo server
```
More info: [Server](https://hexo.io/docs/server.html)

**Generate static files**

``` bash
$ hexo generate
```		
More info: [Generating](https://hexo.io/docs/generating.html)

**Deploy to remote sites**

``` bash
$ hexo deploy
```

More info: [Deployment](https://hexo.io/docs/deployment.html)

**生成索引**
需要集成algolia搜索
```
export HEXO_ALGOLIA_INDEXING_KEY=你的Search-Only API key
hexo algolia
```

### 修改头像

在theme下的`_config.yml`

```
# Sidebar Avatar
# in theme directory(source/images): /images/avatar.jpg
# in site  directory(source/uploads): /uploads/avatar.jpg
avatar: /images/avatar.png
```

### 修改网站标题和作者

在根目录下的`_config.yml`

```
# Site
title: 有寻有伺
subtitle: 
description: 为天地立心，为生民立民，为往圣继绝学，为万世开太平。
author: 有寻有伺
language: zh-Hans
timezone:
```
