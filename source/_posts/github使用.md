---
title: github使用
tags: []
categories: []
poster:
  topic: 标题上方的小字
  headline: 大标题
  caption: 标题下方的小字
  color: 标题颜色
date: 2025-03-24 07:37:48
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

### Windows打不开github

#### 1、打开 Hosts 文件路径

- **需管理员权限，也可以复制出来，改完再复制回去覆盖**

```
C:\Windows\System32\drivers\etc\hosts
```

#### 2、在文件末尾添加以下内容

- **github的ip可能会变，可以在cmd输入 `ping github.com`获取最新的ip地址**

- **通过 win + X => 点击运行 => 输入cmd回车 => 弹出黑色终端 =>  输入 ping github.com**

![](https://pub-7fe6bbbffb8045bf9f5bbb3f378ea457.r2.dev/Snipaste_2025-03-24_07-54-48.png)

```
# GitHub Start
140.82.113.3      github.com
140.82.114.20     gist.github.com
185.199.108.153   assets-cdn.github.com
199.232.69.194    github.global.ssl.fastly.net
# GitHub End
```

#### 3、保存文件，刷新 DNS 缓存

- **通过 win + X => 点击运行 => 输入cmd回车 => 弹出黑色终端 => 输入下面命令**

```
命令提示符输入 ipconfig /flushdns
```



### Mac打不开github


#### 1、获取最新IP地址

- 打开终端，`command + space` 输入 terminal 打开终端
- 输入 `ping github.com`，查看ip地址

#### 2、编辑Hosts文件

- 打开终端，输入 sudo vi /etc/hosts，输入密码
- 按 i 进入编辑模式（英文输入法状态下），将上述IP和域名添加到文件末尾
- 按 Esc 退出编辑，输入 :wq 保存并退出

#### 3、刷新DNS缓存

```
执行命令：sudo killall -HUP mDNSResponder
```

**若使用其他域名（如gist.github.com），需一并添加对应的IP，操作方式如上**

### 还看不懂？

#### 直接问Deepseek

- 让Deepseek给出详细的操作步骤，进行比较详细说明，如果遇到问题，再次追问即可