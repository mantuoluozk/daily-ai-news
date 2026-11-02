---
name: daily-ai-news
description: 每日 AI 新闻自动发布：拉取 aihot 精选新闻，选定主题，写文章，用 wxpub 生成封面并推进公众号草稿箱
version: 2.0.0
---

# 每日 AI 新闻自动发布

一个**编排层** skill。它只负责「选题 → 写文章」这两件需要判断力的事，
渲染、封面、上传、进草稿箱全部交给 [wxpub](https://github.com/mantuoluozk/wxpub)。

## 使用方式

当用户说"发一篇 AI 新闻"、"daily ai news"、"今天的 AI 新闻"、"AI 日报发文"时触发。

## 依赖

只有一个：

- **wxpub** —— 负责封面、渲染、发布

```bash
pip install git+https://github.com/mantuoluozk/wxpub.git
wxpub doctor          # 先体检：凭证、字体、出口 IP、接口权限
```

> wxpub 尚未发布到 PyPI，用 git 地址安装。命令都是 `wxpub`，不影响用法。

`wxpub doctor` 会告诉你缺什么、怎么补。**不要跳过这一步**，
它是后面所有失败里最省时间的一道检查。

新闻数据用 webfetch 直接取，不需要额外装 skill。

## 工作流程

### Step 1: 拉取 AI 新闻

**用 webfetch 工具，不要用 PowerShell 的 Invoke-WebRequest（会有编码问题）**

```
webfetch https://aihot.news/api/public/items?mode=selected&take=30 format: text
```

> 注意：`aihot.virxact.com` 已经 301 到 `aihot.news`。用 curl 手动取的话必须带 `-sSL`，
> 否则会写出 0 字节文件，解析时报 `no element found`。

如果结果为空，扩大时间窗口重取。

### Step 2: 展示新闻列表并推荐主题

以编号列表展示，每条包含标题、来源、摘要（截前 80 字）。

**推荐**：给出 1-2 个建议主题并说明理由（话题性、来源权重、实用性、时效性）。
让用户输入数字选择。**不要替用户决定。**

### Step 3: 获取详细内容

```
webfetch <新闻URL> format: markdown
```

提取：发生了什么、关键数字、和同类产品的差异、值得展开的点。

### Step 4: 写文章

**文章结构：**
1. 开场：痛点前置 + 先说结论（不要"最近，XX 发布了"这种起手式）
2. 新闻核心
3. 解读 / 分析（这一段必须有自己的判断，不能是转述）
4. 结尾：一句能带走的话，不要"欢迎留言讨论"

**风格：**
- 口语化，像跟朋友聊天
- 每段 2-3 句，不堆大段
- 对比用表格
- 命令和代码用代码块，方便复制

**插图：**
- 1-2 张，优先用新闻原文配图 / 产品官网截图
- markdown 里写 `![描述](URL)`，wxpub 会自动下载、转格式、压到 1MB 以内、上传换 CDN 地址

**文件格式**（wxpub 靠 frontmatter 拿元数据，必须写）：

```markdown
---
title: 不超过 30 字
subtitle: 一句话补充，显示在封面上
date: 2026-11-02
author: 你的署名
tags: [AI, 大模型]
theme: midnight
layout: stack
summary: 120 字以内的摘要，微信会用它做分享描述
cover: cover.png
---
正文从这里开始
```

**保存位置**：用户没指定就放在当前工作目录的 `articles/ai-daily-YYYY-MM-DD-<slug>/article.md`。
不要把绝对路径写死在任何地方。

### Step 5: 生成封面

```bash
wxpub cover article.md -o <文章目录>/cover.png
```

不带参数时，`wxpub cover` 直接读 frontmatter 里的 `title` / `subtitle` / `date` / `theme` / `layout`。

想要挑风格就先看图：

```bash
wxpub cover --grid all -o /tmp/themes.png   # 11 主题 × 6 布局 一张拼图
```

**为什么不用 Chrome headless 截图或 AI 生图：**

| 方案 | 问题 |
|---|---|
| AI 生图 | 中文字形建模不可靠，标题超过四五个汉字就缺笔画、错位 |
| Chrome headless | 要装浏览器、要处理字体加载、Windows 路径转义老出错 |
| Pillow 直画（wxpub） | 中文字 100% 可控，改一个字重跑 0.1 秒，零浏览器依赖 |

### Step 6: 发布

**先给用户看成品，再发**：

```bash
wxpub publish article.md --dry-run -o <文章目录>/out
```

产出 `out/preview.html`（手机宽度预览）+ 成品 HTML。让用户确认标题和排版。

确认后推草稿箱：

```bash
wxpub publish article.md -o <文章目录>/out
```

`--yes` 可跳过确认，但在 Agent 流程里**建议保留确认**，因为标题发出去就改不了了。

### Step 7: 完成报告

告诉用户：
- 文章标题
- 本地位置（article.md / cover.png / out/preview.html）
- 去 mp.weixin.qq.com 草稿箱确认

## 故障处理

先跑 `wxpub doctor`。报错信息里已经带了原因和修法，按提示做。

几个最高频的：

| 现象 | 真正原因 | 处理 |
|---|---|---|
| `40164 invalid ip` | 出口 IP 不在白名单 | doctor 会打印当前 IP，去后台「IP 白名单」加上 |
| 加了白名单还报 40164 | 白名单有缓存延迟 | 等 1-5 分钟；同时确认改动的是**同一个**公众号 |
| `45166 invalid content hint` | 正文里有 `<a>` 标签或零宽字符 | wxpub 提交前会清洗，如果还报，说明是别处拼进来的 HTML |
| `48001 api unauthorized` | 个人未认证订阅号没有草稿箱接口权限 | 需要认证，这个改代码解决不了 |
| 正文图片全裂 | 预览没生成图片副本 | 用 `--dry-run` 让 wxpub 自己复制，不要手拼 HTML |

更多见 [wxpub 的 PITFALLS.md](https://github.com/mantuoluozk/wxpub/blob/main/PITFALLS.md)。

## 文件命名规范

- 目录：`ai-daily-YYYY-MM-DD-<slug>`（slug 用新闻关键词，如 `chatgpt-voice`）
- 文章：`article.md`
- 封面：`cover.png`
- 发布产物：`out/`

## 注意事项

- 每天只发一篇
- 推荐主题，但最终让用户选
- 文章必须有实质内容，不能只是新闻转述
- 用 webfetch 取新闻，避免编码问题
- **不要自己拼 HTML 提交给微信接口**，一律走 wxpub，它带清洗和诊断
