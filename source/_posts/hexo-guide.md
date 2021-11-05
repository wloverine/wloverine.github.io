---
title: Hexo Guide
date: 2019-11-26 21:57:41
tags:
- hexo
---
## 简介
> A fast, simple & powerful blog framework, powered by Node.js.

## Next主题

**Hexo拥有很多精美主题，我们用的是Next。**

### Next 各版本的仓库

| 年份        | 版本    | 仓库                                          |
| ----------- | ------- | --------------------------------------------- |
| 2014 ~ 2017 | v5      | https://github.com/iissnan/hexo-theme-next    |
| 2018 ~ 2019 | v6 ~ v7 | https://github.com/theme-next/hexo-theme-next |
| 2020        | v8      | https://github.com/next-theme/hexo-theme-next |

### Next与Hexo版本适配关系

![image-20211105133645530](https://cdn.jsdelivr.net/gh/wloverine/PicGo/img/20211105133647.png)

## 快速开始 

- 代码地址（hexo相关配置在hexo分支上，发布分支是master）
  
  ```bash
  git clone https://github.com/wloverine/wloverine.github.io
  ```
  
- 本地运行 
  
  ```bash
  hexo s
  ```
  
- 发布到github
  ```bash
  hexo clean & hexo g & hexo d
  ```

- 写新文章
  进入博客主目录，输入以下命令
  
  ```bash
  hexo new "新文章"
  ```
<!--more-->
##  版本升级

### 全局升级 Hexo 版本

```bash
# 清理NPM缓存
$ npm cache clean -f

# 全局安装版本检测、版本升级工具
$ npm install -g npm-check
$ npm install -g npm-upgrade

# 全局检测哪些模块可以升级，这里可以根据打印的提示信息，手动安装最新版本的模块
$ npm-check -g

# 全局更新模块
$ npm update -g

# 全局安装或更新Hexo的最新版本
$ npm install --global hexo
```

### 博客升级Hexo版本

```bash
# 进入博客的根目录
$ cd /blog-root

# 检测Hexo哪些模块可以升级
$ npm-check

# 删除package-lock.json
# rm -rf package-lock.json

# 更新package.json
$ npm-upgrade

# 删除整个模块目录，这样可以避免很多坑
$ rm -rf node_modules

# 更新Hexo的模块
$ npm update --save

# 若出现依赖的问题，用以下命令检查一下，然后把报错的统一修复一下即可
$ npm audix

# 或者强制更新
$ npm update --save --force
```

### 升级Next主题

```bash
# 进入主题文件夹
cd themes

# 备份旧版数据
mv next next-bak

# 拉取新版本代码（由于已经将next主题文件夹内的配置文件分离到根目录，只需拉取代码完全替代老版代码即可，无需修改配置文件）
git clone xxx
```

