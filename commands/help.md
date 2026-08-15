---
author: "3-1415f"
updatedAt: "2026-08-12"
title: "/help  帮助"
category: commands
description: "获取单个或全部命令的帮助信息与语法说明"
tags: ["信息", "聊天栏", "生存", "创造"]
---

获取单个或多个命令的帮助信息

### 语法
`/help <命令>`
获取指定命令的帮助信息。必须为单个词或者双引号（"）括起的字符串。
`/help [页码]`
列出所有可用命令。未指定或小于1的会被视为1，大于总页数会被默认为展示最后一页。

### 示例

```mcfunction
/help clone
```
获取`clone`命令的帮助信息。

```mcfunction
/help
```
显示命令列表（第一页）

> [!NOTE]
> - `?` 命令是 `help` 命令的别名
> - 如需查看特定命令的详细用法，可查阅各命令文档，如 [`/give`](../give/)、[`/execute`](../execute/) 等
