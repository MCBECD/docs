---
author: "官方•Dingding OvO"
updatedAt: "2026-08-12"
title: "关于 MCBECD"
category: basics
description: "MCBECD 项目介绍、技术栈、参与贡献指南"
tags: ["入门"]
---

## MCBECD 是什么？

MCBECD（Minecraft Bedrock Edition Commands Documentation）是一个由社区驱动的开源项目，致力于为 Minecraft 基岩版提供全面、准确的命令参考文档。无论你是刚接触命令方块的新手，还是经验丰富的地图作者，MCBECD 都能帮助你快速查找命令用法与语法。

目前 MCBECD 已经收录了 26 条常用命令的详细文档，涵盖语法说明、参数解释、实用示例以及基岩版特有的注意事项。所有文档以 MD 格式编写，支持丰富的排版和代码高亮。写作规范详见 [写作指南](../writing-guide/)。

MCBECD 网站支持 7 种界面语言（简体中文、English、繁體中文、日本語、한국어、Deutsch、Français），你可以随时在设置中切换。

## 技术栈

- **Next.js 16** — 网站前端基于 Next.js 16 构建，使用 App Router 进行页面路由，支持静态站点生成（SSG），部署在 Cloudflare Pages 上，全球访问速度极快。
- **TypeScript + Tailwind CSS 4** — UI 部分使用 TypeScript + Tailwind CSS 4 开发，采用 CSS 自定义属性实现主题系统，内置浅色/深色模式以及多套配色方案。
- **Git Submodule** — 文档内容以 MD 格式存储在独立的 Git 子模块仓库中，通过子模块机制与网站代码分离管理，方便社区成员单独贡献文档内容。
- **i18n** — 网站支持 7 种界面语言的国际化（i18n），所有 UI 文本均通过翻译文件管理，文档内容也计划实现多语言版本。

## 参与贡献

MCBECD 是一个完全由社区贡献的项目，我们欢迎所有人参与。你可以帮助我们补充新的命令文档、改进现有内容、修正错误，或者添加新的语言翻译。

详细的贡献指南请查看仓库中的 CONTRIBUTING.md 文件，其中包含了文档结构说明、模板和提交规范：

- [网站仓库 CONTRIBUTING.md](https://github.com/MCBECD/site/blob/main/CONTRIBUTING.md)
- [文档仓库 CONTRIBUTING.md](https://github.com/MCBECD/docs/blob/main/CONTRIBUTING.md)

## 作者

MCBECD 由以下作者共同维护：

- **丁丁QZ** — [GitHub](https://github.com/DingdingOvO) · [哔哩哔哩](https://b23.tv/v9QuaTO) · [快手](https://v.kuaishou.com/JTZL7bix)
- **官方•Dingding OvO** — [GitHub](https://github.com/DingdingOvO)

网站前端（含导航栏、设置面板等）的源代码完全开源，可在 [GitHub 仓库](https://github.com/MCBECD/site) 中查看。

## 开源许可

MCBECD 网站和文档内容均基于 MIT 许可证开源，你可以自由使用、修改和分发。

Minecraft 是 Mojang Studios 的商标。本项目与 Mojang Studios 或 Microsoft Corporation 没有任何隶属、认可或关联关系。

## 下一步

- [快速开始](../getting-started/) — 启用作弊、获取权限、使用命令方块
- [命令语法基础](../command-syntax/) — 选择器与坐标
- [写作指南](../writing-guide/) — MD 组件、提示框与写作规范
- [获取全套钻石装备](../community/4/) — 社区贡献的实用命令示例
