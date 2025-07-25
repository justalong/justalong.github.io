---
title: cladue实现工具小程序
tags: ['AI编程', 'Claude开发']
categories: ['AI']
poster:
  topic: 用claude编程，1天实现工具小程序
  headline: 大标题
  caption: 标题下方的小字
  color: 标题颜色
date: 2025-04-07 22:22:06
description: 分享使用Claude AI在1天内开发微信小程序工具集的完整经验，包含单位转换、二维码生成、计时器、证件照制作等10+实用工具的开发过程，详细介绍了AI编程的提示词技巧、UI设计规范、数据存储方案，以及从简单需求描述到精确技术约束的演进过程，为AI辅助开发提供实战参考。
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

## 一、功能概览和本文核心

**本次开发，不是1天干撸，而是在下班后或早起搞的，总体加和计算了一下，大概1天的时间**（12个小时），平常下班都是9点的衰仔，好在还有双休，谢天谢地。

### 1.1 一图介绍小程序功能

**快速应用工具**

![Snipaste\_2025-04-05\_17-13-31.png](https://pub-7fe6bbbffb8045bf9f5bbb3f378ea457.r2.dev/claude_fun.png)

### 1.2 本文核心

*   大概介绍一下，小程序功能，以及设计小程序的出发点
*   介绍一下整体开发过程，体验一下claude初步评估一下研发水平
*   总结一部分心得，再用claude写小程序时，发现的一些问题和设计思路
*   介绍一下开发中，在没有付费开通云服务时，小程序图片/音频/数据怎么存储

## 二、功能介绍和设计出发点

### 2.1 小程序功能

**首页工具**

*   单位转换：主要用来临时换算单位
*   生成二维码：把一些文本/密码/链接等生成二维码，方便快速传播，或者解码二维码获取文本
*   全屏时钟：可以用来计时，大屏效果好，适合专注工作计时，点击可以隐藏年月
*   计时器：支持倒计时/计时器，而且支持同时多个存在，适合批量计时
*   应援弹幕：设置字体颜色，大小和速度，演唱会上举起手机（哥哥/姐姐，你太美，此刻我已等待2年半）
*   证件照：1寸/2寸/小2寸，可以自己拖拽裁切
*   表情制作：单纯的图片拼接文字，没什么复杂内容（后面搞一组现成的词语表）
*   称呼计算器：回家过年必备好吧，爸爸的爸爸叫什么？叫爸爸爸爸？
*   房贷计算器：算一算，就不想恋爱/结婚啦，可以说是恋爱/结婚冷静神器。
*   BMI计算器：看看自己真实的斤两，PS和美图只能骗骗自己。
*   复杂计算器：比系统自带的计算器，提供了更多的计算器内容。

![](https://pub-7fe6bbbffb8045bf9f5bbb3f378ea457.r2.dev/claude_screen.png)

**导航工具**

*   **这个主要是为了适配公众号，因为有些东西不能在公众号去写，而且公众号发了就不太好修改了**，没法做实时补充内容。
*   另外这个和github的通用导航库是互相映射的，github是公开开源内容， 如果有兴趣/或有喜欢的推荐网址，提交代码 <https://github.com/justalong/navjson>

**休闲娱乐**

*   划划水玩玩小游戏。
*   敲敲木鱼，平复一下上班的负面情绪，远离垃圾人和事。

### 2.2 设计出发点

*   一直想做工具小程序，但是时间上真的没有那么多，所以存在小程序项目 hellow world 基础，但没建设任何内容
*   claude或Deepseek等众多AI产品的爆发，以及能力的飞升，想测测AI能力，但是缺少真实项目练习，缺少项目规划
*   AI的发展，以及个人对AI在编程上影响非常感兴趣，在把上面两点一结合，本次的初步尝试就诞生了，而且总体大大超出我的预期。

## 三、介绍claude整体开发过程

### 概况介绍

先整体看一下开发内容，其实写了很多需求描述，为什么写这么多，多数由下面几点导致的（大的方向：**长度限制和功能错误和UI不达标**）

![claude.gif](https://pub-7fe6bbbffb8045bf9f5bbb3f378ea457.r2.dev/claude.gif)


*   功能点复杂，输出长度超出了最大限制
*   输出的功能有BUG，多次对话修改，达到长度限制
*   多次改动后，代码输出混乱（因为修改bug问题，它有时候只给出部分修改内容，会出现函数丢失）
*   使用不正确的API，导致功能异常
*   UI个界面布局过于丑陋，大小和颜色适配不合理
*   功能不符合预期，输出简陋/数据不足/方法设计不合理

### 功能介绍

![](https://pub-7fe6bbbffb8045bf9f5bbb3f378ea457.r2.dev/claude_style.png)

Claude 四种模式主要区别语言风格、内容深度和适用场景：

1.  **Normal（标准模式）**
    *   **风格**：自然口语化
    *   **长度**：中等内容，保留一些必要细节
    *   **特点**：日常对话等通用场景

2.  **Concise（简明模式）**
    *   **风格**：精简概括总结，电报式表达
    *   **长度**：平均比标准模式短40-50%
    *   **特点**：适合简短，重点获取，移动端查看
    *   **典型回答**："1.xxx;2.xxx;3.xxx"

3.  **Explanatory（解析模式）**
    *   **风格**：教学模式，逻辑清晰
    *   **长度**：比标准模式长30-100%
    *   **特点**：适合复杂概念/深度理解
    *   **典型回答**："1.详细介绍xxx技术点，算法，公式，处理机制都会详细介绍"

4.  **Formal（正式模式）**
    *   **风格**：商务模式，严谨规范
    *   **长度**：与标准模式相当
    *   **特点**：适合专业场景/正式文件
    *   **典型回答**："尊敬的\[姓名/职务]：谨定于\[日期] \[时间]在\[地点]举行关于\[主题]的会"

**场景选择**：

*   日常咨询 → Normal；快速备忘 → Concise；学术研究 → Explanatory；商务沟通 → Formal；内容创作 → Normal/Formal；技术文档 → Explanatory/Formal

**使用技巧**：

*   指令叠加模式（如"用Formal模式解释XXX"）实现更精细的内容控制，可根据需要灵活切换。

### 开发方式

*   主要是通过通过不断对话来实现需求，调试bug问题。
*   通过转换语句/词语的内容，来更好的保持逻辑性和让AI能够理解你的表述内容。
*   合理的拆分功能模块，避免输出超限，将功能解耦在人工组合产出新的完整功能。

### 问题处理

*   结合编辑器，适当的进行功能运行，通过编辑器查看错误。
*   将错误变量/函数/对象等，反馈给AI，让其修复问题。
*   添加适当的console输出，针对事件适当补充console。

## 四、心得和设计思路

这块内容，个人感觉还是有点意思的，用AI开发，需要一些经验积累，从最开始简单几句话编程到后面通过详细表述需求，来达到实现自身预期的目标，这是一个必然的过程。

随着功能开发，你会发觉语言的表达方式，词语的运用对结果都有一定影响，在没拿到想要的结果时，很难确定哪些表达它可以理解的更准确。

### 4.1 先看个样板变化（从首次提问到后面提问内容的转变）

#### 4.1.1 首次提问

先看我第一个提问“**微信小程序二维码生产（还写错了字）和二维码解析实现**”，很符合绝大多数人的处理方式。

**想用一句话给我产出一个符合我预期的需求**（这在业务开发中，如果PM提出一句话，研发肯定会心里骂娘，立刻拿起40米大刀，好在AI心平气和）

![](https://pub-7fe6bbbffb8045bf9f5bbb3f378ea457.r2.dev/claude_ti.png)

结果可想而知，肯定是没符合我的要求，输出的内容也完全不符合我的预期，我期望是一个完整的功能，而不是一个方法。

    // 1. Basic QR code generation
    wx.createQRCode({
      content: 'Your content here',
      width: 200, // Size in px
      success: function(res) {
        // res.path contains the path to the generated QR code image
        const qrCodePath = res.path;
        // You can use this path to display the QR code
      },
      fail: function(error) {
        console.error('QR code generation failed:', error);
      }
    });

    // 2. Alternative: Use canvas to draw QR code
    // First, add a canvas element in your WXML file
    // <canvas canvas-id="qrcode" style="width: 200px; height: 200px;"></canvas>

    // Then in your JS file
    import QRCode from 'path/to/qrcode-library'; // Import a QR code library

    Page({
      generateQRCode: function() {
        const ctx = wx.createCanvasContext('qrcode');
        const qrcode = new QRCode({
          content: 'Your content here',
          width: 200,
          height: 200
        });
        qrcode.draw(ctx);
        ctx.draw();
      }
    });

#### 4.1.2 后面提问

1.  只写page页面相关，不用app.js全局变量；
2.  支持最新小程序api；
3.  采用rpx布局方式；
4.  主体背景黑色，小程序头部同意采用黑色背景白色字体；
5.  不要使用button和text标签；
6.  主要字体采用28rpx; 大号字体使用32rpx;超大号字体使用36rpx；
7.  禁止在wxml里使用函数方法做数组字符串操作；
8.  禁止在wxml里使用includes方法；
9.  创建BMI计算器，区分男女；

![Snipaste\_2025-04-05\_21-05-52.png](https://pub-7fe6bbbffb8045bf9f5bbb3f378ea457.r2.dev/claude_wxml.png)

从两次描述，其实你就能发现，表述差距和细节约束一下增加很多，从 “PM人” =>  “RD人”的角度，肯定是后面的表述，更符合人的理解。

而且需求表述的越详细，“研发”理解的越容易，理解的越“准确”，到最后的产出就越符合预期，修改“沟通”的次数就越少。

### 4.2 设计思路

回看上面的功能截图，细看会发现，开始的前三个，功能背景颜色和字体都不是很统一，字体大小没约束，背景颜色也黑白都有，但是后三个则更像一个产品的功能风格。

**这也是不断约束提示词，才有的更加统一的效果预期。**

#### 4.2.1 统一UI

*   用必要且详细的表述你对UI视觉的预期
*   明确约束一套字体大小规范（大中小）
*   明确约束核心内容背景色和字体设计规范（主体背景色值，字体颜色色值，区域背景色值，按钮背景色值，头部标题背景色值，文字色值，都可以提供多段色值）
*   明确约束布局尺寸方案（rpx还是px）
*   明确约束是否使用通栏头部

#### 4.2.2 统一页面结构体

*   让其制定一套项目目录结构，将结构输出，并将其作为你的项目设计核心
*   单纯的使用claude是没有办法完美实现持久化记忆能力的（应该可以通过Agent或者工作流平台可以）所以这个目录结构可以自己截图保存，后续做功能设计时，合理的让其理解该结构，并适当拆分功能（这个使用准确性不太好，但可以用用看）
*   我这里统一采用了pages维度设计，也就是一个功能一个pages下的目录，特点是它给我输出四个文件内容，我自己拷贝过去。（**只写page页面相关，不用app.js全局变量**）

#### 4.2.3 统一API使用

*   统一API的使用规范，这里主要的表现的是，使用全新的API，避免使用一些老旧的API导致非预期的效果
*   **支持最新小程序api；使用Canvas最新的API；（我建议适当的罗列出相关API）** 这里能避免一些媒体选择API使用旧版。
*   wx.getSystemInfo；wx.getSystemInfoSync；这两个就可能需要单独罗列；

#### 4.2.4 统一WXML数据规范

*   这里主要是约束WXML规范，避免一些不合理的使用导致页面渲染异常
*   如果没有约束，在WXML里可能存在includes，findIndex以及其他数组操作方法，这些方法都会带来异常
*   **禁止在wxml里使用函数方法做数组字符串操作；禁止在wxml里使用includes方法；**

#### 4.2.5 统一WXML标签规范

*   这里主要是约束一些标签使用，AI可能更愿意尝试语义化的表达，更具有逻辑性，但是往往带来一些问题
*   经常使用text标签，导致布局异常
*   经常使用button标签，导致事件和UI布局异常

### 4.3 心得经验

除了上面的一些大的方向的约束，在开发过程中还有一些小的点也会带来问题，这里我主要总结我当前遇到过的一些问题。

#### 4.3.1 经验和问题总结

*   如果不明确说明以page维度输出，经常会使用全局变量，并且生成app.js和app.json相关配置
*   本地存储storage，AI更喜欢使用移步方法进行set/get，但是有时候，或者多数时候，使用同步的set/get更符合我们预期
*   默认输出，会随机使用px单位或rpx单位，建议统一约束
*   输出的wxml的样式和变量，绑定内容过长，AI会进行换行操作，但是换行会导致异常，可以自己修改或者约束wxml属性不换行
*   动画一般会采用小程序的api实现(wx.createAnimation)，如果不是很需要或者使用不多，可以约束其动画实现效果。
*   音频内容不能使用本地，但是AI生成是没法处理这种问题的，需要自己去更换一下。
*   AI更多的是负责生成功能的实现方案，不会在意你的限制条件，需要特别明确出来（比如我希望给gif添加字幕，用来制作表情包，它给我了云服务的实现方案，但是我没有买）必须明确只用前端方案。
*   过多的条件约束，偶尔会出现能力实现丢失，虽然最后它总结时说实现哪些功能，但是代码层面会没有。这种时候可以追加补充功能提示词就行，它会自己完善。
*   **过长代码输出，会触发终止，需要输入continue来完成后续的持续输出**

![Snipaste\_2025-04-06\_08-19-34.png](https://pub-7fe6bbbffb8045bf9f5bbb3f378ea457.r2.dev/claude_continue.png)

#### 4.3.2 贴近真实项目的实战效果

项目主题是，开发一个高中教师排课的课表功能，这个需求的出发的点就是老婆那边使用课表比较费劲，每学期的课表都是保存个图片，修改后也不方便调换。另外就是她们排课也比较复杂，没有什么快捷方式，一般都是excel逐步去处理。

这个项目的目标就是用小程序实现一个初步的排课表功能，可以实现在手机上完成排课，并且可以共享课表，让每个老师都能看到这个课表，并且可以通过课表过滤，只看自己的课程和自己的自习时间。

这项目其实本身复杂度并不是很高，只不过为了达到一个标准的且符合用户习惯的使用效果，需要处理很多边界和交互能力。

*   添加课程时，可以对这个课程（老师）进行备注，打标签
*   比如孕期老师，可能不适合安排上午最后一节课，这种条件都通过标签实现
*   需要支持设置课程时间和课间时间，这个直接影响每次添加课程的开始和结束时间默认值
*   添加课程时，下一次添加要以上一次结束时间+课间间隔作为时间选择器的开始时间
*   时间选择器的结束时间，默认是开始时间 + 课程时间
*   需要自动校验时间重叠问题，如果课程时间有重叠，要禁止添加，给出提示
*   每天的时间选择器设置时间是互相隔离的，比如周一的添加和周二的添加，时间选择器的开始和结束时间互不影响
*   支持数据本地存储，方便快速启动渲染
*   每次添加的数据是按照周一到周日隔离，互不影响

![Snipaste\_2025-04-06\_13-55-28.png](https://pub-7fe6bbbffb8045bf9f5bbb3f378ea457.r2.dev/claude_kk.png)

*   在不做人为修改的情况下，这个能力基本上每次都不符合预期，而且几乎总是有错误。
*   但随着提示词的改变，也是有逐步转好，表现得越来越接近预期。
*   第一个大的变化：从最开始的没有合理的UI布局超出屏幕，且只有一个添加按钮，数据无法准确添加，存在边界case导致添加异常，到后来变成，有不错的UI界面，每天下面有个添加按钮，且数据能够准确添加，**尽管还有时间问题，但是如果使用的人能够自己检查时间，那本次功能其实也基本算可用。** 这期间**AI不能准确理解时间隔离，但是能更好的理解每天有一个添加功能按钮** 也凸显出提示词，甚至说语言呈现的结构体，对AI是有非常大影响的。
*   个人认为**面对AI编程，语言的逻辑性和语言表达出来的结构性，将是AI辅助型研发的主要差距点**，因此**AI编程的艺术，更像是语言表达的艺术**。

#### 4.3.3 粗浅的结论

*   从本次结果和消耗的时间来看，AI对工作效率有相当大提升，但取代研发说法还是为时尚早，但是研发模式的变革已经悄然来临了，只是本次的一些小小的功能测试，也能体现出AI的恐怖能力。
*   个人的观点认为，当前程序研发和AI工具发展情况，可以尝试类比农民和农具（反正也是码农），当前处在工具的快速发展，百花齐放的阶段。农具的使用大幅提升效率和批量产出能力，自然会降级基础劳动力的需求，但是对于熟练使用工具的人需求同样会增加。另外效率溢出，产能过剩，研发时间降低，自会催生更多产品，甚至逐步产生独立的个人产品，也许会变成公众号的愿景“**在小的个体也会有自己独立的品牌**”

## 五、小程序数据存储

### 5.1 图片资源

**目前小程序图片主要分成2部分**

*   一部分在本地，图片压缩体积，尽量保持足够小，这类主要是存放一些ICON
*   一部分存储在cloudflare平台，这真是“大善人”，没啥可说的，免费提供对象存储，而且提供https能力
*   当然你还可以使用一些免费的图床平台，比如“路过图床”，可以去搜搜有很多，此图床不提供https能力，这对小程序来说可不咋友好

### 5.2 音频资源

*   因为小程序不支持本地音频，所以只能用云端数据，那当然也放到了 cloudflare 上了。

### 5.3 数据资源

*   比如一些静态JSON数据，不需要动态改变的内容，理想状态是放到CDN上，但是几乎所有的免费都有时间限制，所以我给他设计成了npm包形式，通过unpkg实现，我可以把静态json打包，然后可以通过unpkg直接请求JSON内容。然后在本地存储一份，每日更新一次。
*   如果你不知道unpkg是什么，可以搜“cnpm 视频事件”，一位老哥把视频搞上去了，我记得当时也是一段热搜。

