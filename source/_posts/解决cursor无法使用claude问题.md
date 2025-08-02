---
title: 解决cursor无法使用claude问题
categories:
  - AI技术
  - AI工具
tags:
  - AI
  - 人工智能
  - AI工具
  - AI教程
keywords: AI, 人工智能, AI工具, AI教程, AI应用
description: 本文介绍解决cursor无法使用claude问题
sticky: false
mermaid: false
katex: false
mathjax: false
comments: true
indexing: true
breadcrumb: true
date: 2025-08-02 13:33:53
cover:
banner:
topic:
---

### 解决方案

#### 官方建议

按照cursor官方给出的说法，你可以做四个选择。

- 继续使用 Cursor 并结合使用可用模型。“自动”选项将为每个请求选择一个可用模型。
- 手动选择您帐户中仍然启用的任何模型。
- 请携带您自己的 API 密钥（如支持），用于您所在地区的提供商。请参阅API 密钥。
- 如果提供商的承保范围大幅降低了您付费计划的价值，请申请取消帐户并按比例退款。

#### 目前保持可用claude的方案

**先说简单方案：cursor配置**

- 目前在我这里生效的最简单方式，将http2改成http1.1或者http1.0，改完后重启，所有模型都能用。

![cursor 修改http](https://pub-7fe6bbbffb8045bf9f5bbb3f378ea457.r2.dev/common/cursor.webp)

**再说工具方案：使用tun模式**

- 再说魔法工具，开启tun模式，也就是虚拟网卡模式，将ip换成国际，然后重启cursor，所有模型都能用。

- 特别注意，如果你改了魔法工具的ip地址，那可能还需要重启cursor。

![使用tun模式](https://pub-7fe6bbbffb8045bf9f5bbb3f378ea457.r2.dev/common/cursor2.webp)
