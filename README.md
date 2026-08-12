# MCBECD Docs

Minecraft 基岩版命令文档 —— 社区驱动的 Bedrock Edition 命令参考。

[![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)](./LICENSE)

## 这是什么？

MCBECD Docs 是 [MCBECD 站点](https://github.com/MCBECD/site) 的**文档内容仓库**，以 Git 子模块形式被站点消费。专注于 Minecraft **基岩版（Bedrock Edition）** 命令系统 —— 参数格式、返回值、可用命令均与 Java 版有差异，本文档以基岩版为准。

## 文档结构

所有文档为扁平 `.mdx` 文件，放在仓库根目录或子目录中：

```
MCBECD/docs/
├── about.mdx                # 项目介绍
├── getting-started.mdx       # 快速开始
├── command-syntax.mdx        # 命令语法基础
├── writing-guide.mdx         # 文档写作指南
├── standards.mdx             # 标准规范总纲
│
├── commands/
│   ├── give.mdx              # /give 命令
│   ├── effect.mdx            # /effect 命令
│   ├── execute.mdx           # /execute 命令
│   ...（22 个官方命令）
│
├── 1.mdx                     # 社区贡献：「在线时间」
├── 2.mdx                     # 社区贡献
├── 3.mdx                     # 社区贡献
│
├── standards/
│   ├── frontmatter-standard.mdx
│   ├── structure-standard.mdx
│   ├── tag-standard.mdx
│   ...（12 个标准规范文档）
│
├── README.md                 # 本文件
└── CONTRIBUTING.md           # 贡献指南
```

### 命名规则

| 类型 | 命名方式 | 示例 | category 值 |
|------|---------|------|-------------|
| **基础文档** | 语义化英文 | `getting-started.mdx` | `basics` |
| **官方命令** | `commands/` 子目录 + 命令名 | `commands/give.mdx` | `commands` |
| **社区文档** | 纯数字编号 | `3.mdx` | `community` |

> 社区文档使用纯数字编号（从 1 递增），放在仓库根目录。站点渲染时使用 frontmatter 中的 `title` 字段作为显示名称。

## 文档格式

每份 `.mdx` 文件由 **frontmatter** + **Markdown 正文**组成：

```yaml
---
title: "/give  给予物品"
author: "官方•Dingding OvO"
updatedAt: "2026-08-12"
category: commands
description: "给予玩家指定物品，支持数量、数据值与组件"
tags: ["物品", "玩家", "聊天栏", "生存", "创造", "OP1", "多目标"]
---
```

### Frontmatter 字段

| 字段 | 必填 | 说明 |
|------|------|------|
| `title` | ✅ | 文档标题。命令文档格式：`/command  中文名称`（两个空格）；社区文档：纯中文名称 |
| `author` | ✅ | 作者署名（MCBECD 社区名称或 GitHub 用户名） |
| `updatedAt` | ✅ | 最后更新日期，格式 `YYYY-MM-DD` |
| `category` | ✅ | 分类：`basics` / `commands` / `community` |
| `description` | ✅ | 一句话描述，15-40 个汉字 |
| `tags` | ⚠️ | 标签数组。命令文档和社区文档**必填**，基础文档可选。标签名使用简体中文 |
| `pinned` | ❌ | 是否置顶（仅限 `basics` 文档使用） |
| `hidden` | ❌ | 是否在列表中隐藏 |

## 命令文档要求

每个命令文档**必须**包含以下章节：

1. **开头段落**（无标题）—— 1-3 句话概括命令功能
2. **语法**（`### 语法`）—— 完整命令格式
3. **参数**（`### 参数`）—— 列出每个参数的类型与说明
4. **示例**（`### 示例`）—— 至少 **3 个**可直接使用的命令示例，从简单到复杂排列
5. **基岩版注意**（`### 基岩版注意`）—— 与 Java 版的差异、版本兼容性、OP 等级要求

正文长度不低于 800 字（不含代码块）。

## 标签要求

标签从预定义的标签集中选取，不得自行创造新标签。

- **命令文档**：3-7 个标签，必须对照[标签对照表](./standards/tag-standard.mdx)检查
- **社区文档**：3-6 个标签，包含 1 个内容类型标签 + 1-2 个技术栈标签
- **基础文档**：0-2 个标签

标签书写格式：`tags: ["标签A", "标签B"]`

## 添加新文档

1. Fork 本仓库
2. 创建 `.mdx` 文件（社区文档用下一个可用数字编号，命令文档用命令名）
3. 填写正确的 frontmatter（确保所有必填字段已填写）
4. **使用相对链接**引用其他文档：[`/give`](../commands/give/)
5. 提交 PR 到 `main` 分支

详见 [CONTRIBUTING.md](./CONTRIBUTING.md)。

## 被谁使用？

| 项目 | 关系 |
|------|------|
| [MCBECD/site](https://github.com/MCBECD/site) | Git 子模块，构建时拉取 |
| [mcbecd.pages.dev](https://mcbecd.pages.dev) | 线上站点 |

## License

MIT — 自由使用、修改、分发。
