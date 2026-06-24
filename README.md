# daily-ai-news

每日 AI 新闻自动发布 Skill，适用于 [MiMo Code](https://github.com/XiaomiMiMo/MiMo-Code)、[Claude Code](https://github.com/anthropics/claude-code) 等 AI 编程助手。

一键获取 AI 热点新闻，自动生成公众号文章并发布。

## 功能

- 自动拉取 [aihot.virxact.com](https://aihot.virxact.com) 精选 AI 新闻
- AI 推荐最佳主题，用户选择后自动写文章
- 文章自动添加网络插图
- 一键发布到微信公众号草稿箱

## 依赖的 Skills

本 Skill 依赖以下两个 Skill 协同工作：

### 1. aihot

用于获取 AI 新闻数据源。

- **功能**：拉取 aihot.virxact.com 的精选 AI 动态
- **安装**：参见 [aihot Skill](https://aihot.virxact.com)
- **触发词**：用户问"今天 AI 圈有什么"、"AI 日报"、"AI 资讯"等

### 2. baoyu-post-to-wechat

用于发布文章到微信公众号。

- **功能**：通过 API 或 Chrome CDP 发布文章到微信公众号
- **安装**：参见 [baoyu-skills](https://github.com/JimLiu/baoyu-skills)
- **依赖**：
  - Bun 运行时
  - 微信公众号 API 凭证（AppID + AppSecret）
  - Chrome 浏览器（用于截图生成封面）

## 安装

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

将 `skills/daily-ai-news` 目录复制到你的 Skills 目录：

```bash
# MiMo Code
cp -r skills/daily-ai-news ~/.claude/skills/

# 或 Claude Code
cp -r skills/daily-ai-news ~/.claude/skills/
```

## 使用

在 AI 编程助手中说：

```
发一篇 AI 新闻
```

或：

```
daily ai news
```

### 工作流程

1. 自动拉取最近 24 小时 AI 精选新闻
2. 展示新闻列表，AI 推荐 1-2 个最佳主题
3. 用户选择主题
4. AI 获取详细内容并写文章（自动添加插图）
5. 生成封面图
6. 发布到微信公众号草稿箱
7. 提示用户去后台确认发布

## 文章风格

- 秋芝式开场（痛点前置 + 先说结论）
- 口语化，像跟朋友聊天
- 每段 2-3 句，不堆大段文字
- 用表格做对比
- 文章中间加 1-2 张网络插图

## 文件结构

```
articles/
└── ai-daily-YYYY-MM-DD-<slug>/
    ├── article.md      # 文章内容
    ├── cover.html      # 封面 HTML
    └── cover.png       # 封面截图
```

## 示例

```
> 发一篇 AI 新闻

[拉取新闻中...]

今日 AI 新闻精选：

1. ChatGPT 语音大升级：Bidi 1 已上线测试 - IT之家
2. 豆包专业版上线：能操作你电脑的 AI 办公助手 - 豆包
3. ...

【推荐】#1 ChatGPT 语音升级 - 大厂动态，话题性强，用户可直接体验

请选择主题（输入数字）：1

[生成文章中...]
[生成封面中...]
[发布中...]

发布完成！
文章：ChatGPT 语音大升级：能边说边听了
请去 mp.weixin.qq.com 草稿箱确认发布
```

## 相关链接

- [aihot.virxact.com](https://aihot.virxact.com) - AI 新闻数据源
- [baoyu-skills](https://github.com/JimLiu/baoyu-skills) - 微信公众号发布工具
- [MiMo Code](https://github.com/XiaomiMiMo/MiMo-Code) - AI 编程助手

## License

MIT