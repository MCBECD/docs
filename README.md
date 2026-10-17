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

## 怎么写文档

格式、章节结构、标签这些规则统一写在 [文档规范](./standards/README.md)，那一份是唯一标准，这里不重复。

提交流程见 [CONTRIBUTING.md](./CONTRIBUTING.md)。

## License

MIT — 自由使用、修改、分发。
