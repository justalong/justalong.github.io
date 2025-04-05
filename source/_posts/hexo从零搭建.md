---
title: hexo从零搭建
tags: ['静态网站', 'hexo', '个人博客']
categories: ['工具教程']
poster:
  topic: 标题上方的小字
  headline: 大标题
  caption: 标题下方的小字
  color: 标题颜色
date: 2025-03-25 23:47:53
description:
cover:
banner:
sticky:
mermaid:
katex:
mathjax:
topic:
comments:
indexing:
breadcrumb:
---

### 准备工作

- 下载安装 node.js，推荐稳定18.x.x以上版本
- 下载安装 git 软件
- 去github官网注册github账号

### 一、本地搭建 hexo 静态博客，提升成就感

#### 第一步：验证node安装成功与否

**win + x => 点击运行 => 输入cmd => 打开 cmd 运行**

- node 安装成功与否，可以通过cmd输入`node -v`会打印版本，类似如下

```
PS D:\myMedia\github> node -v
v20.11.0
```

#### 第二步：验证git安装

**git默认安装到右键快捷键上，可以通过右键查看**

- git 终端安装后可以替代cmd的终端，也可以继续用cmd


#### 第三步：初始化 hexo 框架

- 如果上面Node.js正常安装，npm就自然有了
  
```
// 全局安装 hexo-cli 包工具
npm install -g hexo-cli
```

#### 第四步：初始化基本信息

- 新建一个文件夹，如 MyBlog ，进入该文件夹内，当前目录打开cmd输入
`hexo init`**（可以在文件夹目录cmd）**

```
// 初始化结束，应该有个类似的package.json文件
{
  "name": "qiyue",
  "version": "1.0.0",
  "private": true,
  "scripts": {
    "build": "hexo generate",
    "clean": "hexo clean",
    "deploy": "hexo deploy",
    "publish": "npm run build && npm run deploy",
    "server": "hexo clean && hexo g && hexo s"
  },
  "hexo": {
    "version": "7.3.0"
  },
  "dependencies": {
    "hexo": "^7.3.0",
    "hexo-deployer-git": "^4.0.0",
    "hexo-generator-archive": "^2.0.0",
    "hexo-generator-category": "^2.0.0",
    "hexo-generator-index": "^4.0.0",
    "hexo-generator-tag": "^2.0.0",
    "hexo-related-popular-posts": "^5.0.1",
    "hexo-renderer-ejs": "^2.0.0",
    "hexo-renderer-marked": "^7.0.0",
    "hexo-renderer-stylus": "^3.0.1",
    "hexo-server": "^3.0.0"
  }
}

```

- 生成完 hexo 模板，执行 `npm install`安装所有依赖包（核心记住package.json文件在哪里，就在那个目录执行`npm install`）
  
#### 第五步：本地启动，验证效果

- 此时，网站主体部分到此已经完成了，可以本地运行查看效果

```
// npm run 行为，默认执行package.json的script下的命令
npm run server
// 或者
hexo server
```

这时候打开浏览器，输入 localhost:4000 就可以看到博客目前的样子了

#### 第六步：掌握基本命令，编辑文章

```
// 清理构建产出，每次构建会生成public 文件，这个就是完好的浏览器页面
hexo clean

// 构建浏览器页面产出，生成public文件和内部内容
hexo generate 或者 hexo g

// 部署public产出到github上，变成可访问的网站
hexo deploy 或者 hexo d

// 本地启动预览服务，预览部署后效果
hexo server
```
### 详细设计
