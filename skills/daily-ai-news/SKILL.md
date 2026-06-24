---
name: daily-ai-news
description: 每日 AI 新闻自动发布：拉取 aihot 精选新闻，选择主题，写文章，发布到微信公众号
version: 1.0.0
---

# 每日 AI 新闻自动发布

## 使用方式

当用户说"发一篇 AI 新闻"、"daily ai news"、"今天的 AI 新闻"、"AI 日报发文"时触发。

## 依赖

- **aihot** - 获取 AI 新闻数据源
- **baoyu-post-to-wechat** - 发布到微信公众号

## 工作流程

### Step 1: 拉取 AI 新闻

**重要：使用 webfetch 工具获取，不要用 PowerShell 的 Invoke-WebRequest（会有编码问题）**

```
webfetch https://aihot.virxact.com/api/public/items?mode=selected&take=30 format: text
```

如果获取为空，扩大时间窗口重新获取。

### Step 2: 展示新闻列表并推荐主题

将获取到的新闻以编号列表形式展示给用户，包含：
- 标题
- 来源
- 摘要（截取前 80 字）

**AI 推荐**：在列表后给出 1-2 个推荐主题，说明推荐理由（话题性、热度、实用性）。

让用户输入数字选择要写的主题。

### Step 3: 获取详细内容

用 webfetch 获取新闻原文，提取关键信息：

```
webfetch <新闻URL> format: markdown
```

### Step 4: 写文章

根据新闻内容写一篇公众号文章：

**文章结构：**
1. 秋芝式开场（痛点前置 + 先说结论）
2. 新闻核心内容
3. 解读/分析
4. 总结 + 互动引导

**文章风格：**
- 口语化，像跟朋友聊天
- 每段 2-3 句，不堆大段文字
- 用表格做对比
- 有代码/命令可复制

**插图要求：**
- 文章中间至少加 1-2 张插图
- 插图从网络上找，优先使用：
  - 新闻原文中的配图
  - 产品官网截图
  - 相关技术图解
- 在文章中用 markdown 图片语法引用：`![描述](URL)`
- 插图要与内容相关，能辅助说明

**保存位置：**
`D:\cc_project\微信公众号\articles\ai-daily-YYYY-MM-DD-<slug>\article.md`

### Step 5: 生成封面

创建封面 HTML 并用 Chrome headless 截图：

```powershell
$htmlPath = "<文章目录>\cover.html"
$pngPath = "<文章目录>\cover.png"
& "C:\Program Files\Google\Chrome\Application\chrome.exe" --headless --disable-gpu --screenshot="$pngPath" --window-size=1280,544 "file:///$($htmlPath -replace '\\','/')"
```

### Step 6: 发布到微信公众号

```bash
skillDir="C:\Users\zhengke\.claude\plugins\marketplaces\baoyu-skills\skills\baoyu-post-to-wechat"
bun "$skillDir/scripts/wechat-api.ts" "<article.md路径>" --theme default --author "阿派" --cover "<cover.png路径>"
```

### Step 7: 完成报告

告诉用户：
- 文章标题
- 保存位置
- 去 mp.weixin.qq.com 草稿箱确认

## 文件命名规范

- 目录：`ai-daily-YYYY-MM-DD-<slug>`（slug 为新闻关键词，如 `chatgpt-voice`、`doubao-pro`）
- 文章：`article.md`
- 封面：`cover.html` + `cover.png`

## 注意事项

- 每天只发一篇
- AI 给出推荐，但最终让用户选择主题
- 使用 webfetch 获取新闻，避免编码问题
- 文章必须有实质内容，不能只是新闻转述
- 文章中间加 1-2 张网络插图，增强可读性