# MCBECD Docs

Minecraft 基岩版命令文档 —— 社区驱动的 Bedrock Edition 命令参考。

[![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)](./LICENSE)

## 这是什么？

MCBECD Docs 是 [MCBECD 站点](https://github.com/MCBECD/site) 的**文档内容仓库**，以 Git 子模块形式被站点消费。专注于 Minecraft **基岩版（Bedrock Edition）** 命令系统 —— 参数格式、返回值、可用命令均与 Java 版有差异，本文档以基岩版为准。

## 文档结构

所有文档为扁平 `.mdx` 文件，放在仓库根目录：

```
MCBECD/docs/
├── about.mdx                # 项目介绍
├── getting-started.mdx       # 快速开始
├── command-syntax.mdx        # 命令语法基础
├── writing-guide.mdx         # 文档写作指南
│
├── commands/
│   ├── give.mdx              # /give 命令
│   ├── effect.mdx            # /effect 命令
│   ├── execute.mdx           # /execute 命令
│   ...（22 个官方命令）
│
├── community-1.mdx           # 社区贡献：「在线时间」
├── community-2.mdx           # 社区贡献：「雪球填平」
├── community-get-diamonds.mdx # 社区贡献：「获取全套钻石装备」
│
├── README.md                 # 本文件
└── CONTRIBUTING.md           # 贡献指南
```

### 命名规则

| 类型 | 命名方式 | 示例 | category 值 |
|------|---------|------|-----------|
| **基础文档** | 语义化英文 | `getting-started.mdx` | `basics/0` |
| **官方命令** | `commands/` 子目录 + 命令名 | `commands/give.mdx` | `commands` |
| **社区文档** | `community-` 前缀 + 数字/名称 | `community-1.mdx` | `community` |

> 社区文档用 `community-` 前缀避免与基础文档和命令文档冲突，站点渲染时使用 frontmatter 中的 `title` 字段。

### 子目录（已废弃）

不再使用 `{name}/index.mdx + meta.json` 文件夹格式，统一扁平 `.mdx`。

## 文档格式

每份 `.mdx` 文件由 **frontmatter** + **Markdown 正文**组成：

```yaml
---
title: "/give  给予物品"
order: 110
category: commands
description: "给予玩家指定物品"
author: "官方"
updatedAt: "2026-08-09"
---
```

### Frontmatter 字段

| 字段 | 必填 | 说明 |
|------|-----|------|
| `title` | ✅ | 文档标题，显示在页面和侧边栏 |
| `order` | ✅ | 排序权重，越小越靠前 |
| `category` | ✅ | 分类：`basics/N` / `commands/Type` / `community` |
| `description` | ✅ | SEO 描述，120 字以内 |
| `author` | ❌ | 作者署名 |
| `updatedAt` | ❌ | 最后更新日期 |
| `pinned` | ❌ | 是否置顶（`true` / `false`） |
| `tags` | ❌ | 标签数组 |

## 命令文档要求

每个命令文档应包含以下章节：

1. **语法** — 完整命令格式
2. **参数** — 表格列出每个参数的类型、说明、可选值
3. **示例** — 至少 2 个可直接使用的命令示例
4. **基岩版注意** — 与 Java 版的差异、版本兼容性

## 添加新文档

1. Fork 本仓库
2. 创建 `.mdx` 文件（社区文档用下一个可用数字 ID，命令文档用命令名）
3. 填写正确的 frontmatter
4. **使用相对链接**引用其他文档：`[语法基础](./command-syntax)`
5. 提交 PR 到 `main` 分支

详见 [CONTRIBUTING.md](./CONTRIBUTING.md)。

## 被谁使用？

| 项目 | 关系 |
|------|------|
| [MCBECD/site](https://github.com/MCBECD/site) | Git 子模块，构建时拉取 |
| [mcbecd.pages.dev](https://mcbecd.pages.dev) | 线上站点 |

## License

MIT — 自由使用、修改、分发。
