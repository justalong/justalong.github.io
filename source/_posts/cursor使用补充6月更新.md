---
title: cursor使用补充6月更新
categories:
  - AI技术
  - AI工具
tags:
  - AI
  - 人工智能
  - AI工具
  - AI教程
keywords: AI, 人工智能, AI工具, AI教程, AI应用
description: 本文介绍cursor使用补充6月更新相关内容，包括使用方法、技巧和最佳实践。
sticky: false
mermaid: false
katex: false
mathjax: false
comments: true
indexing: true
breadcrumb: true
date: 2025-08-02 13:18:30
cover:
banner:
topic:
---


[AI编程专栏（二） - Cursor 深度使用指南](https://blog.csdn.net/liuyongshun2/article/details/149061166?spm=1001.2014.3001.5502)

### 偷偷摸摸涨价

每个计划都包含按 API 价格计算的每月代理使用预算。例如，Pro 包括每月至少 20 USD 的使用费。每条代理消息都根据模型的 API 定价从此预算中扣除。

![cursor价格token](https://pub-7fe6bbbffb8045bf9f5bbb3f378ea457.r2.dev/cursor2/1.webp)

一旦每月使用限制用尽，您将收到明确通知，并显示一条消息，按其表述，有三种处理方式

- 切换到 Auto

- 启用使用量定价以继续使用同一模型（按 API 价格收费）

- 升级到更高的订阅级别

### 一、待办事项（To-Do）

![cursor 待办TODO](https://pub-7fe6bbbffb8045bf9f5bbb3f378ea457.r2.dev/cursor2/2.webp)

#### 1.1 功能特点

- 支持在代码、文档、对话中记录待办事项

#### 1.2 使用方式

##### 1.2.1 代码注释

- Prompt: 检查代码中TODO生成TODO列表

```js
// TODO: 优化这个函数的性能
function slowFunction() {
    // 现有代码
}
```

##### 1.2.2 项目ToDo

- Prompt: 检查项目中待办事项，生成TODO列表

```markdown
# 项目待办事项
## 高优先级
- [ ] 修复登录页面的内存泄漏
- [ ] 优化首页加载速度
- [ ] 添加错误处理机制

## 中优先级
- [ ] 重构用户管理模块
- [ ] 添加单元测试
- [ ] 更新文档

## 低优先级
- [ ] 美化UI界面
- [ ] 添加动画效果
```
##### 1.2.3 Agent 直接生成

- Prompt: 根据内容生成一个待办清单，并按优先级排序

##### 1.2.4 搭配rule使用

- 当要求Cursor编码时，它遇到TODO后，会自动触发rule，按要求添加TODO

```
---
description: 管理项目待办事项
alwaysApply: true
---

在处理代码时，请注意：
1. 遇到 TODO 注释时，评估是否可以立即处理
2. 如果发现新的待办事项，请添加 TODO 清单
3. 完成 TODO 项目后，请删除相应注释
```

##### 1.2.5 搭配memories使用

- 通过明确要求记住ToDo，添加上下文，打开新的chat也能直接知道ToDo。

```
请帮我记住这个项目的待办事项：
检查docs目录下所有文档内容的完整性和规范性
根据检查结果，补充或完善docs目录下缺失或不规范的文档内容
统一docs目录下所有文档的格式和风格
最终校对并确认docs目录下所有文档已完善
```

**新的chat可以查看到**

![cursor todo](https://pub-7fe6bbbffb8045bf9f5bbb3f378ea457.r2.dev/cursor2/4.webp)

### 二、后台代理（Background Agent）

![cursor background](https://pub-7fe6bbbffb8045bf9f5bbb3f378ea457.r2.dev/cursor2/3.webp)

#### 2.1 功能特点

- 支持在远程环境中**并行、异步**执行复杂或耗时任务
- 可同时运行多个 Agent，互不干扰
- 任务完成后自动通知，无需等待
- 适合大规模自动化、批量处理、长时间运行任务

#### 2.2 使用方式

##### 2.2.1 启用后台代理

- 在设置中开启 Background Agent

- 聊天界面点击云朵图标，或使用快捷键 `Cmd/Ctrl+E` 启动

![Background Agent](https://pub-7fe6bbbffb8045bf9f5bbb3f378ea457.r2.dev/cursor2/5.webp)

##### 2.2.2 对话示例

```
请在后台批量修复所有 lint 错误，并生成修复报告
```

##### 2.2.3 多任务并行

- 可同时分配多个 Agent 处理不同任务（如修复 bug、生成文档、优化性能等）
- 可在后台 Agent 面板查看进度、状态、结果

#### 2.3 应用场景

- 批量代码修复、重构、格式化
- 大型项目的自动化迁移、依赖分析
- 长时间运行的测试、文档生成、数据处理
- 团队协作下的任务分工与异步处理

### 三、记忆（Memories）

#### 3.1 功能特点

- 自动/手动记录项目中的重要事实、决策、TODO、上下文说明
- 记忆内容**持久化**，下次打开项目自动恢复
- 可在设置中管理、编辑、删除记忆
- 支持与 To-Do、Agent 等功能联动，提升上下文智能

#### 3.2 使用方式

##### 3.2.1 主动记录 To-Do 到记忆
```
请帮我记住以下待办事项：
1. 优化首页加载速度
2. 重构用户认证模块
3. 添加错误日志系统
```

##### 3.2.2 自动记录

- 设置里面设置开启memories

![cursor memories](https://pub-7fe6bbbffb8045bf9f5bbb3f378ea457.r2.dev/cursor2/6.webp)

- 启用 Memories 后，Cursor 会自动记录对话、操作、重要上下文
- 可通过设置界面查看和管理所有记忆

##### 3.2.3 结合 Agent 自动更新

```
请定期扫描项目 TODO，并自动更新记忆中的待办清单
```

#### 3.3 应用场景

- 项目长期开发、上下文持续追踪
- 团队成员交接、知识传递
- 重要决策、技术选型、架构变更的记录与回溯
- 跨会话、跨阶段的任务延续

### 四、BugBot（自动代码审查）

![cursor BugBot](https://pub-7fe6bbbffb8045bf9f5bbb3f378ea457.r2.dev/cursor2/7.webp)

#### 4.1 功能特点

- 自动为 GitHub PR 进行代码审查，发现潜在 bug 和问题
- 审查结果以评论形式反馈在 PR 页面
- 支持一键跳回 Cursor 编辑器，自动生成修复建议

#### 4.2 使用方式

- 在项目关联的 GitHub 仓库中开启 BugBot 功能（详见官方文档）
- 提交 PR 后，BugBot 自动分析并评论问题
- 点击“Fix in Cursor”按钮，可直接在 Cursor 中定位并修复问题

#### 4.3 应用场景

- 团队协作下的自动化代码审查
- 及时发现和修复潜在 bug
- 提升代码质量与审查效率

### 五、MCP 一键安装与 OAuth 支持

![MCP](https://pub-7fe6bbbffb8045bf9f5bbb3f378ea457.r2.dev/cursor2/8.webp)

#### 5.1 功能特点

- 支持一键集成官方/第三方 MCP（Model Context Protocol）工具链

#### 5.2 使用方式

https://docs.cursor.com/tools/mcp

在官方mcp列表，点击想要的mcp即可唤起cursor，一键完成
