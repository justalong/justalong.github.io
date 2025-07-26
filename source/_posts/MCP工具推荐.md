---
title: MCP工具推荐
categories:
  - AI技术
  - AI工具
tags:
  - AI
  - 人工智能
  - AI工具
  - AI教程
keywords: AI, 人工智能, AI工具, AI教程, AI应用
description: 本文介绍MCP工具推荐相关内容，包括使用方法、技巧和最佳实践。
sticky: false
mermaid: false
katex: false
mathjax: false
comments: true
indexing: true
breadcrumb: true
date: 2025-07-26 08:51:06
cover:
banner:
topic:
---

### 一、简单介绍 MCP 发展

#### 1.1 早期问题

早期，大家对大模型应用，一般是复制或上传内容，或者研发通过API接入Token，**很难与本地或远程工具/软件/插件/数据库等进行串联**，存在较多的局限性。只能进行“纸上谈兵”，缺少现实中实际应用。

#### 1.2 初次改进 - Function Call

为了解决上面的问题，OpenAI推出`Function Call`函数调用，它允许大型语言模型在生成文本的过程中调用外部函数或服务的功能，开启了工具调用的工业化时代。尽管 `Function Call` 解决了一些问题，但其本身也存在一些问题

- **可拓展性差** 没有标准的统一规范，各平台实现方式有差异
- **可靠性差** 错误处理简陋，函数调用失败频繁
- **延迟问题** 明显的等待时间，用户体验中断
- **成本高昂** 额外的计算成本，API调用费用，资源浪费，需要处理所有可用函数的定义
- **能力局限性** 只能做简单操作，缺乏上下文记忆

##### 1.2.1 **Function Call（函数调用）** 

是一种让大模型与外部工具、API或代码交互的核心技术，**允许模型在生成文本时，主动触发预定义的函数并返回结构化数据**，从而突破纯文本生成的限制，实现动态能力扩展。`Function Call`定义工具调用的规范结构，但本身并不会调用函数或工具，是一种工具使用机制。

##### 1.2.2 **都干了什么**

- 让LLM知道哪些工具可用
- 定义工具数据结构，输入输出，说明工具作用
- 模型如何选择工具（自动选择、强行选择）

##### 1.2.3 核心流程

**模型决策**  
- 用户提问 → 大模型分析问题 → **判断是否需要调用外部函数**（查天气）

**生成调用请求**  
- 若需要，模型返回一个**结构化请求**（JSON），包含：函数名和所需参数
```
functions = [
    {
        "name": "get_current_weather",
        "description": "获取指定城市的天气",
        "parameters": {
            "type": "object",
            "properties": {
                "location": {"type": "string", "description": "城市名称"},
                "unit": {"type": "string", "enum": ["celsius", "fahrenheit"]}
            },
            "required": ["location"]
        }
    }
]

```

**本地执行函数**  
- 开发者收到请求后，**在本地或服务器执行对应的函数**（避免模型直接操作外部系统）

```
if response.choices[0].message.get("function_call"):
    func_name = response["function_call"]["name"]
    args = json.loads(response["function_call"]["arguments"])
    if func_name == "get_current_weather":
        result = weather_api(args["location"])  # 调用真实API
```

**返回结果给模型**  
- 将函数执行结果（如天气数据、计算结果）重新输入模型 → 模型整合结果生成最终回复。

```
final_response = openai.ChatCompletion.create(
    model="gpt-4",
    messages=[
        {"role": "user", "content": "北京现在多少度？"},
        {"role": "function", "name": func_name, "content": str(result)} 
    ]
)
print(final_response.choices[0].message.content)
# 输出："北京当前气温25℃，晴间多云。"
```

#### 1.3 统一标准 - MCP诞生

模型上下文 Model Context Protocol (MCP) 由 Claude 背后公司 Anthropic 开发的开放标准。核心目标 “**通过一致方式，让 AI 代理与工具、服务和数据连接**，无论它们位于何处或如何构建”。

**MCP是一个标准协议**，较好的类比是TYPE-C接口，你手机用的TYPE-C接口，那你就可以正常使用相关标准的充电器，无关手机或充电器品牌。

MCP 直接在 AI 与数据之间架桥联通，通过 MCP 服务器和 MCP 客户端，大家只要都遵循这套协议，就能实现“万物互联”。

![](https://pub-7fe6bbbffb8045bf9f5bbb3f378ea457.r2.dev/11b8575513014853814e30a6fc41a5a5.webp)

##### 1.3.1 MCP后台工作原理

- MCP Host（左侧）是AI驱动的应用程序，Claude Desktop、IDE，Agent等工具
- 主机连接到多个 MCP 服务器，每个服务器都公开不同的工具或资源
- 某些服务器会访问本地资源（计算机上的文件系统或数据库）
- 其他的可以访问远程资源（API 或 云服务）

#### 1.4 MCP 和 Function Call 对比

- Function Call 是一种让AI模型能够调用预定义函数或工具的机制
- MCP 是一个开放标准，用于在AI应用程序和外部数据源及工具之间建立安全、标准化的连接

##### 1.4.1 主要区别

**架构层次**
-   Function Call：操作级别的工具调用
-   MCP：协议级别的系统集成框架

**状态管理**
-   Function Call：通常无状态，每次调用独立
-   MCP：支持有状态的持久连接和上下文管理

**复杂度**
-   Function Call：相对简单，适合单一任务
-   MCP：更复杂，适合企业级集成和复杂工作流

**标准化程度**
-   Function Call：实现方式相对灵活
-   MCP：遵循严格的开放标准和协议规范

**安全性**
-   Function Call：基本的权限控制
-   MCP：更完善的安全框架和权限管理

##### 1.4.2 应用场景

**Function Call 典型使用场景**

主要集中在简单的工具调用（简单/轻量/无状态工具使用）

-   网络搜索
-   数学计算
-   天气查询
-   数据库查询
-   API调用

**MCP 典型使用场景**

主要集中在复杂系统应用（持久化上下文依赖，多步骤工作流，安全性要求严格）

-   连接企业数据库和文件系统
-   集成复杂的业务系统
-   提供持续的数据访问能力
-   支持多步骤的复杂任务流程

**二者本身定义并不相同，Function Call更像MCP的一个子集，但也有各自更为契合的应用场景**

### 二、MCP资源

#### 2.1 MCP官方
https://github.com/modelcontextprotocol/servers

**开发文档**

https://modelcontextprotocol.io/introduction

#### 2.2 Cursor官方
* https://docs.cursor.com/tools
* https://cursor.directory/

#### 2.3 awesome
https://github.com/punkpeye/awesome-mcp-servers

#### 2.4 魔塔
https://www.modelscope.cn/home

#### 2.5 pulemcp
https://www.pulsemcp.com/

#### 2.6 glama
https://glama.ai/mcp/servers

#### 2.7 smithery
https://smithery.ai/

#### 2.8 linuxdo
https://linux.do/t/topic/499069

#### 2.9 Awesome-MCP-ZH
https://github.com/yzfly/Awesome-MCP-ZH

### 三、好用的MCP

**使用MCP前，一定先看文档，看清楚文档中MCP的要求（Node版本）和接入方式**，另外MCP现在已经非常多了，我这里仅说一些前端开发或者通用型的MCP。

#### 3.1 Context7 

https://github.com/upstash/context7

这个确实很好的解决了使用三方框架时，API使用不准确的问题。

**作用**

- 直接从源中提取最新的、特定版本的文档和代码示例 - 并将它们直接放入您的提示中
- 举例：我接入Fabric框架时，因为用错版本API导致异常，通过context7进行了纠正

**如何使用**
- 在Cursor的对话中，通过提示词 “使用context7” 触发context7 MCP调用

**接入配置**

- **永远要关注node版本**

```
// 安装
npm i -g @upstash/context7-mcp

// mcp.json配置
"context7": {
    "command": "npx",
    "args": [
        "-y",
        "@upstash/context7-mcp"
    ],
    "env": {
        "DEFAULT_MINIMUM_TOKENS": "6000"
    }
},
```


#### 3.2 figma MCP

https://www.figma.com/blog/introducing-figmas-dev-mode-mcp-server/

如果你们团队再用figma的话，那这个算是前端必须的工具，接入figma后可以可以让你少完成30%-50%前端页面布局设计。

**作用**

- AI直接基于figma的数据进行页面UI设计，还原质量和UI设计的规范化程度有关系

**如何使用**

- 设置授权token，让cursor的mcp能请求figma的数据内容，权限就是能给“读写”权限就给，不能就只给“写”。反正是`读写 > 写 > 读`

![](https://pub-7fe6bbbffb8045bf9f5bbb3f378ea457.r2.dev/mcp/1.webp)


![](https://pub-7fe6bbbffb8045bf9f5bbb3f378ea457.r2.dev/mcp/2.webp)


- 复制链接，在cursor对话中说明使用figma mcp 实现功能。可以分区域也可以整个页面

![](https://pub-7fe6bbbffb8045bf9f5bbb3f378ea457.r2.dev/mcp/3.webp)


**接入配置**

- **永远要关注node版本**

```
// 安装
npm i -g figma-developer-mcp

// 配置，token换掉
{
    "mcpServers": {
        "Framelink Figma MCP": {
            "command": "npx",
            "args": [
                "-y",
                "figma-developer-mcp",
                "--figma-api-key={yourtoken}",
                "--stdio"
            ]
        }
    }
}
```

#### 3.3 git或github

https://gitmcp.io/

https://github.com/github/github-mcp-server

业务开发，我是不怎么用这两个的，适合经常活跃github的研发。不细说直接参考文档即可。

**git mcp** 

- AI 助手可以深入了解代码存储库，读取 llms.txt、llms-full.txt、readme.md 等，响应更加准确减少幻觉
- 与任何公共 GitHub 存储库和 GitHub Pages 无缝协作，使 AI 工具能够访问文档和代码

**github mcp**

- 自动化 GitHub 工作流程和流程
- 从 GitHub 存储库中提取和分析数据

#### 3.4 sequential-thinking 

https://github.com/modelcontextprotocol/servers/tree/main/src/sequentialthinking

核心用来做复杂任务设计和拆分，是深度应用多步任务执行必备工具。

**作用**

- 将复杂问题分解为可管理的步骤
- 随着理解的加深而修改和完善思想
- 分支到其他推理路径
- 动态调整 THOUGHT 总数
- 生成并验证解决方案假设

**如何使用**

- 使用 sequential-thinking 进行3轮思考；

![](https://pub-7fe6bbbffb8045bf9f5bbb3f378ea457.r2.dev/mcp/4.webp)

- 使用sequential-thinking额外的思考步骤

![](https://pub-7fe6bbbffb8045bf9f5bbb3f378ea457.r2.dev/mcp/5.webp)


**接入配置**

```
// 先安装在使用
npm i -g @modelcontextprotocol/server-sequential-thinking


// 配置
"sequential-thinking": {
    "command": "npx",
    "args": [
        "-y",
        "@modelcontextprotocol/server-sequential-thinking"
    ]
},
```

#### 3.5 Playwright 

https://github.com/microsoft/playwright-mcp

提供浏览器自动化功能的模型上下文协议 （MCP） 服务器。此服务器使 LLM 能够通过结构化的辅助功能快照与网页进行交互，而无需屏幕截图或视觉调整模型


**作用**

- 抓取浏览器网络信息，分析浏览器中的错误。

- 分析网站性能辅助网站开发

**如何使用**

- 从mcp的设置处，可以看到mcp提供的工具都有哪些，有什么工具就可以用什么能力

![](https://pub-7fe6bbbffb8045bf9f5bbb3f378ea457.r2.dev/mcp/6.webp)

**接入配置**

- 核心思路就是安装 => 配置，保证路径关系正确即可，比如我用nvm，全局安装在nvm下。

- 如果不全局安装，也可以使用项目级别的node_modules

```
// 先安装
npm i -g @playwright

// 在配置使用
{
  "mcpServers": {
    "playwright": {
            "command": "npx",
            "args": [
                "node",
                "D:/nvm4w/nodejs/node_modules/@playwright/mcp/cli.js"
            ]
        }
  }
}
```

#### 3.6 Chrome MCP Server

https://github.com/hangwin/mcp-chrome

基于 Chrome 扩展程序的模型上下文协议 (MCP) 服务器，它将您的 Chrome 浏览器功能开放给 Claude 等 AI 助手，从而实现复杂的浏览器自动化、内容分析和语义搜索。

与传统的浏览器自动化工具（例如 Playwright）不同，Chrome MCP 服务器直接使用您日常使用的 Chrome 浏览器，利用现有的用户习惯、配置和登录状态，让各种大型模型或聊天机器人控制您的浏览器

**作用**

- 分析浏览器网站内容，调用浏览器工具，抓取浏览器数据

- 可以通过mcp来打通本地IDE与浏览器关系，一些浏览器异常可以直接用本地IDE的AI进行分析。

**如何使用**

- 对话中明确要求使用 MCP 工具

![](https://pub-7fe6bbbffb8045bf9f5bbb3f378ea457.r2.dev/mcp/7.webp)

**接入配置**

```
// 先安装
npm install -g mcp-chrome-bridge

// 方案1
"chrome-mcp-stdio": {
    "command": "npx",
    "args": [
        "node",
        "D:/nvm4w/nodejs/node_modules/mcp-chrome-bridge/dist/mcp/mcp-server-stdio.js"
    ]
}

// 方案2
"chrome-mcp-server": {
    "type": "streamableHttp",
    "url": "http://127.0.0.1:12306/mcp"
}
```

#### 3.7 browser-tools

https://github.com/AgentDeskAI/browser-tools-mcp

一个强大的浏览器监控和交互工具，它使 AI 驱动的应用程序能够通过 Anthropic 的模型上下文协议 (MCP) 通过 Chrome 扩展程序捕获和分析浏览器数据。**这个偏向监控，但和上面有些类似**，不细说啦。


### 四、知识参考

MCP官方：https://modelcontextprotocol.io/introduction

https://medium.com/@elisowski/mcp-explained-the-new-standard-connecting-ai-to-everything-79c5a1c98288

https://www.53ai.com/news/LargeLanguageModel/2025042846019.html

https://zhuanlan.zhihu.com/p/27327515233