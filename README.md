# Daily AI News

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Platform](https://img.shields.io/badge/Platform-Cross--platform-blue.svg)](https://github.com/mantuoluozk/daily-ai-news)
[![AI Agents](https://img.shields.io/badge/AI%20Agents-Claude%20%7C%20MiMo%20%7C%20Cursor-brightgreen.svg)](https://github.com/mantuoluozk/daily-ai-news)

> 🚀 每日 AI 新闻自动发布：拉取热点 → 选择主题 → 写文章 → 发布到微信公众号

## 问题背景

运营微信公众号的开发者经常会遇到：

- 📰 每天要花时间找 AI 圈热点新闻
- ✍️ 找到新闻后还要写文章，耗时耗力
- 🎨 写完文章还要做封面图
- 📤 最后还要手动发布到公众号

**这个 skill 让你一句话完成从选题到发布的全流程。**

## ✨ 特性

- 🤖 **AI 推荐主题** - 自动分析新闻热度，推荐最佳选题
- 📰 **热点拉取** - 自动获取 aihot.virxact.com 精选 AI 新闻
- ✍️ **自动撰文** - 秋芝式风格，口语化，有深度
- 🖼️ **插图添加** - 自动从网络获取相关插图
- 🎨 **封面生成** - Chrome headless 自动生成封面图
- 📤 **一键发布** - 直接发布到微信公众号草稿箱

## 🛠️ 支持的 AI 工具

| 工具 | 支持状态 |
|------|---------|
| [MiMo Code](https://github.com/XiaomiMiMo/MiMo-Code) | ✅ 完全支持 |
| Claude Code | ✅ 完全支持 |
| [Cursor](https://cursor.sh) | ✅ 完全支持 |
| OpenCode | ✅ 完全支持 |
| 其他 AI 编码工具 | ✅ 通用兼容 |

## 📦 前置条件

- ✅ 安装 [aihot](https://aihot.virxact.com) skill（获取新闻数据）
- ✅ 安装 [baoyu-post-to-wechat](https://github.com/JimLiu/baoyu-skills) skill（发布到公众号）
- ✅ 配置微信公众号 API 凭证（AppID + AppSecret）
- ✅ 安装 Chrome 浏览器（用于生成封面图）
- ✅ 安装 [Bun](https://bun.sh) 运行时

## 🚀 快速开始

### 1. 安装依赖 Skills

```bash
# 安装 baoyu-skills（包含 baoyu-post-to-wechat）
# 参见 https://github.com/JimLiu/baoyu-skills
```

### 2. 配置微信公众号 API

在 `~/.baoyu-skills/.env` 中配置：

```
WECHAT_APP_ID=你的AppID
WECHAT_APP_SECRET=你的AppSecret
```

### 3. 安装本 Skill

告诉你的 Agent：

> 请帮我安装 github.com/mantuoluozk/daily-ai-news

或者手动克隆：

```bash
git clone https://github.com/mantuoluozk/daily-ai-news.git ~/.claude/skills/
```

### 4. 开始使用

直接对 Agent 说：

> 发一篇 AI 新闻

## 📝 工作流程

```
用户说"发一篇 AI 新闻"
    ↓
Step 1: 拉取 aihot 精选新闻
    ↓
Step 2: 展示列表 + AI 推荐主题
    ↓
Step 3: 用户选择主题
    ↓
Step 4: 获取详细内容 + 写文章（自动添加插图）
    ↓
Step 5: 生成封面图
    ↓
Step 6: 发布到微信公众号草稿箱
    ↓
完成！提示用户去后台确认
```

## 📁 目录结构

```
daily-ai-news/
├── README.md                      # 本文件
├── SKILL.md                       # 核心指南（AI 代理读取）
├── LICENSE                        # MIT 许可证
└── skills/
    └── daily-ai-news/
        └── SKILL.md               # Skill 定义
```

## 🔧 核心功能详解

### 新闻拉取

自动获取 aihot.virxact.com 最近 24 小时的精选 AI 新闻，包含标题、来源、摘要。

### AI 推荐主题

AI 会根据以下标准推荐 1-2 个最佳主题：

| 维度 | 说明 |
|------|------|
| 话题性 | 大众关心程度 |
| 热度 | 来源权重（大厂优先） |
| 实用性 | 读者能用上 |
| 新闻价值 | 时效性 |

### 文章风格

- **秋芝式开场**：痛点前置 + 先说结论
- **口语化**：像跟朋友聊天
- **结构清晰**：每段 2-3 句，用表格做对比
- **有深度**：不只是新闻转述，还有解读分析
- **带插图**：文章中间加 1-2 张网络插图

### 封面生成

使用 Chrome headless 自动生成 1280×544 的封面图，支持自定义 HTML 模板。

## 💡 使用示例

### 示例 1：发一篇 AI 新闻

**用户说**："发一篇 AI 新闻"

**Agent 自动执行：**

```
拉取新闻中...
获取到 16 条新闻

今日 AI 新闻精选：

1. ChatGPT 语音大升级：Bidi 1 已上线测试 - IT之家
2. 豆包专业版上线：能操作你电脑的 AI 办公助手 - 豆包
3. ...

【推荐】#1 ChatGPT 语音升级 - 大厂动态，话题性强

请选择主题（输入数字）：1

生成文章中...
生成封面中...
发布中...

发布完成！
文章：ChatGPT 语音大升级：能边说边听了
请去 mp.weixin.qq.com 草稿箱确认发布
```

### 示例 2：选择豆包新闻

**用户说**："基于豆包这个新闻写一篇吧"

**Agent 自动执行：**

1. 获取豆包专业版详细内容
2. 写一篇深度解读文章
3. 自动添加产品截图作为插图
4. 生成封面图
5. 发布到公众号

## 🤝 贡献

欢迎贡献！请遵循以下步骤：

1. Fork 本仓库
2. 创建你的分支 (`git checkout -b feature/AmazingFeature`)
3. 提交你的更改 (`git commit -m 'Add some AmazingFeature'`)
4. 推送到分支 (`git push origin feature/AmazingFeature`)
5. 创建一个 Pull Request

### 贡献内容

- 🐛 报告 Bug
- 💡 提出新功能
- 📝 改进文档
- 🧪 添加测试用例
- 🌍 翻译成其他语言

## 📄 许可证

本项目采用 MIT 许可证 - 查看 [LICENSE](LICENSE) 文件了解详情

## 🙏 致谢

- 感谢 [aihot](https://aihot.virxact.com) 提供 AI 新闻数据
- 感谢 [baoyu-skills](https://github.com/JimLiu/baoyu-skills) 提供微信公众号发布能力
- 感谢所有使用这个 skill 的开发者

---

**遇到问题？** 提交 [Issue](https://github.com/mantuoluozk/daily-ai-news/issues)