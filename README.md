# MCBECD Docs

Minecraft 基岩版命令文档 —— 社区驱动的 Bedrock Edition 命令参考。

[![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)](./LICENSE)

## 这是什么？

MCBECD Docs 是 [MCBECD 站点](https://github.com/MCBECD/site) 的**文档内容仓库**，以 Git 子模块（挂载到站点的 `content/docs`）形式被消费。站点在构建时扫描本仓库，**不依赖任何数据库** —— 本仓库就是唯一内容源。

专注于 Minecraft **基岩版（Bedrock Edition）** 命令系统：参数格式、返回值、可用命令均与 Java 版有差异，本文档以基岩版为准。

## 目录结构

站点按**一级目录名**识别文档分类，分类名直接决定 URL 前缀：

```
MCBECD/docs/
├── basics/        # 基础文档   → /<文件名>/
│   ├── about.md            # 关于 MCBECD
│   ├── command-syntax.md   # 命令语法基础
│   └── getting-started.md  # 快速开始
│
├── commands/      # 命令文档   → /commands/<文件名>/
│   ├── give.md
│   ├── execute.md
│   └── …（共 26 篇）
│
├── community/     # 社区文档   → /community/<文件名>/
│   ├── 1.md   # 在线时间
│   ├── 2.md   # 雪球填平
│   ├── 3.md   # 雪球菜单
│   ├── 4.md   # 获取全套钻石装备
│   └── 5.md   # 玩家互传（TPA）
│
├── standards/     # 文档规范（唯一规范，不在站点展示）
│   └── README.md  # 目录分类、Frontmatter、统一章节顺序、标签、组件等
│
├── README.md      # 本文件
└── CONTRIBUTING.md
```

> **新增分类目录时**，必须同步修改站点的 `lib/docs.ts`（`SCAN_CATEGORIES`）并新增对应路由 `app/<分类>/[slug]/page.tsx`，否则文档不会被站点收录。

## 被谁使用？

| 项目 | 关系 |
|------|------|
| [MCBECD/site](https://github.com/MCBECD/site) | Git 子模块 `content/docs`，构建时拉取 |
| [mcbecd.pages.dev](https://mcbecd.pages.dev) | 线上站点 |

## 文档格式

每份 `.md` 文件由 **frontmatter** + **Markdown 正文**组成：

```yaml
---
title: "/give  给予物品"
author: "官方•Dingding OvO"
updatedAt: "2026-08-12"
description: "给予玩家指定物品，支持数量、数据值与组件"
tags: ["物品", "玩家", "聊天栏", "生存", "创造", "OP1", "多目标"]
---
```

### Frontmatter 字段

| 字段 | 必填 | 说明 |
|------|------|------|
| `title` | ✅ | 文档标题。命令文档格式：`/command  中文名称`（两个空格）；其余文档为纯中文名称 |
| `author` | ✅ | 作者署名（MCBECD 社区名称或 GitHub 用户名） |
| `updatedAt` | ✅ | 最后更新日期，格式 `YYYY-MM-DD` |
| `description` | ✅ | 一句话描述，15-40 个汉字 |
| `tags` | ⚠️ | 标签数组。命令文档和社区文档**必填**，基础文档可选。标签名使用简体中文 |
| `hidden` | ❌ | 是否在列表分区中隐藏（仍可通过站点的「隐藏文档」入口访问） |

## 命令文档要求

每个命令文档**必须**包含以下章节：

1. **开头段落**（无标题）—— 1-3 句话概括命令功能
2. **语法**（`### 语法`）—— 完整命令格式
3. **参数**（`### 参数`）—— 列出每个参数的类型与说明
4. **示例**（`### 示例`）—— 至少 **3 个**可直接使用的命令示例，从简单到复杂排列
5. **基岩版注意**（`### 基岩版注意`）—— 与 Java 版的差异、版本兼容性、OP 等级要求

## 标签要求

标签从预定义的标签集中选取，不得自行创造新标签。

- **命令文档**：3-7 个标签，必须对照 [文档规范](./standards/README.md) 第五章的标签表检查
- **社区文档**：3-6 个标签，包含 1 个内容类型标签 + 1-2 个技术栈标签
- **基础文档**：0-2 个标签

标签书写格式：`tags: ["标签A", "标签B"]`

## 添加新文档

1. Fork 本仓库
2. 在对应分类目录下创建 `.md` 文件（命令文档用命令名；社区文档用下一个可用数字编号）
3. 填写正确的 frontmatter（确保所有必填字段已填写）
4. **使用相对链接**引用其他文档：[`/give`](../../commands/give/)（从 `community/` 出发）
5. 提交 PR 到 `main` 分支

详见 [CONTRIBUTING.md](./CONTRIBUTING.md)。

## License

MIT — 自由使用、修改、分发。
