# Daily AI News

> 一句话从「今天有什么 AI 新闻」到「公众号草稿箱里有一篇成稿」。
> 这个仓库是**编排层**，渲染和发布交给 [wxpub](https://github.com/mantuoluozk/wxpub)。

```bash
pip install git+https://github.com/mantuoluozk/wxpub.git
wxpub doctor      # 体检：凭证、字体、出口 IP、接口权限
```

> wxpub 还没发到 PyPI，先用 git 装，等价的一条命令。发布后这里会换成 `pip install wxpub`。

装上后对你的 AI 工具说：**发一篇 AI 新闻**。

---

## 它解决什么

每天固定流程：翻新闻 → 挑一条 → 写 → 做封面 → 发。前两件需要判断力，
后三件是纯机械劳动，而且每一步都有坑（封面被裁、图片是 WebP 传不上去、
`40164` 白名单、`45166` 正文校验）。

这个 skill 只做前两件。后面全部调 `wxpub` 一条命令完成。

## 前置条件

| 需要 | 说明 |
|---|---|
| 一个 AI 编码工具 | Claude Code / Cursor / MiMo / WorkBuddy 都行 |
| 装 wxpub | `pip install git+https://github.com/mantuoluozk/wxpub.git`，装完跑 `wxpub doctor` |
| 公众号 AppID + AppSecret | 存在 `~/.wxpub/.env` 或环境变量里 |

**就这三行。** 不需要 Chrome，不需要 Bun，不需要另外装发布 skill ——
v1 版本要装 5 个东西，90% 的人在那一步就关页面了。这是 v2 最重要的改动。

`wxpub doctor` 会一次性检查：凭证是否有效、草稿箱接口有没有权限、
当前出口 IP 是多少、中文字体能不能找到。缺什么、去哪补，它直接告诉你。

## 工作流程

```
"发一篇 AI 新闻"
    ↓
① 拉 aihot 精选新闻（webfetch，不装额外 skill）
    ↓
② 列出来 + 推荐 1-2 条，等你选
    ↓
③ 取原文详情
    ↓
④ 写文章（带 frontmatter，wxpub 靠它取标题和摘要）
    ↓
⑤ wxpub cover      → 封面，Pillow 直画
    ↓
⑥ wxpub publish --dry-run → 你先看成品
    ↓
⑦ wxpub publish    → 进草稿箱
```

## 安装

```bash
git clone https://github.com/mantuoluozk/daily-ai-news.git ~/.claude/skills/daily-ai-news
```

或者直接让 Agent 装：

> 帮我安装 github.com/mantuoluozk/daily-ai-news

## 目录结构

```
daily-ai-news/
├── README.md      # 本文件
├── SKILL.md       # 核心指南，Agent 读这个
└── LICENSE
```

产物长这样：

```
articles/ai-daily-2026-11-02-chatgpt-voice/
├── article.md     # 正文 + frontmatter
├── cover.png      # 1280×544
└── out/
    ├── preview.html   # 手机宽度预览
    └── article.html   # 可直接粘进后台的成品
```

## 为什么封面是 Pillow 画的

v1 用 Chrome headless 截 HTML。问题是要装浏览器、字体加载时序不稳、
Windows 下的文件路径转义反复出错，而且截一张要 2 秒。

另一条路是 AI 生图。问题是外文模型对中文字形的建模一直不可靠，
标题超过四五个汉字基本必错 —— 缺笔画、错位、像甲骨文。这不是换提示词能解决的。

现在封面由 `wxpub cover` 用 Pillow 直接画：深色底、双径向光晕、居中标题，
11 个主题 × 6 种布局。中文字 100% 可控，改一个字重跑 0.1 秒，零浏览器依赖。

## 常见故障

| 现象 | 原因 | 处理 |
|---|---|---|
| `40164 invalid ip` | 出口 IP 不在白名单 | `wxpub doctor` 打印当前 IP，去后台加上 |
| 加了白名单还报 | 白名单有缓存延迟 | 等 1-5 分钟 |
| `45166` | 正文里有 `<a>` 标签 | wxpub 提交前清洗，别自己拼 HTML |
| `48001` | 个人未认证号没有草稿箱权限 | 需要认证，代码解决不了 |

完整 21 条在 [wxpub 的 PITFALLS.md](https://github.com/mantuoluozk/wxpub/blob/main/PITFALLS.md)。

## License

MIT
