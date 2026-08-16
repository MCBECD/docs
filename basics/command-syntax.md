---
author: "官方•Dingding OvO"
updatedAt: "2026-08-12"
title: "命令语法基础"
description: "基岩版命令的参数格式、目标选择器与坐标系统"
tags: ["入门"]
---

基岩版命令由命令名和参数组成。如果你是第一次接触命令，建议先阅读 [快速开始](../getting-started/)。

### 语法约定

| 符号 | 含义 |
|------|------|
| `<参数>` | 必填 |
| `[参数]` | 可选 |
| `a\|b` | 二选一 |

以 [`/give`](../commands/give/) 为例：`/give <玩家> <物品> [数量]`——玩家和物品必填，数量可选。

### 目标选择器

| 选择器 | 含义 |
|--------|--------|
| `@p` | 最近玩家 |
| `@a` | 所有玩家 |
| `@r` | 随机玩家 |
| `@s` | 执行者自身 |
| `@e` | 所有实体 |

选择器加参数可限定范围：

<>`/tp @a 0 64 0`

传送所有玩家到坐标 `0 64 0`。详见 [`/tp`](../commands/tp/) 命令文档。

<>`/effect give @a[r=10] speed 30 1`

给 10 格内所有玩家施加速度效果。详见 [`/effect`](../commands/effect/) 命令文档。

<>`/kill @e[type=zombie]`

清除所有僵尸。详见 [`/kill`](../commands/kill/) 命令文档。

### 坐标系统

- **绝对** `100 64 100` — 世界精确位置
- **相对** `~ ~1 ~` — 相对执行位置偏移
- **局部** `^ ^ ^1` — 相对视线方向

<>`/setblock ~ ~-1 ~ diamond_block`

在脚下放置钻石块。详见 [`/setblock`](../commands/setblock/) 命令文档。

<>`/fill ~-5 ~-1 ~-5 ~5 ~-1 ~5 stone`

铺设 11×11 石头地板。详见 [`/fill`](../commands/fill/) 命令文档。

### 命令组合

用 [`/execute`](../commands/execute/) 在不同上下文中执行命令：

<>`/execute as @a at @s run setblock ~ ~-1 ~ gold_block`

在每个玩家脚下放置金块。详见 [`/execute`](../commands/execute/) 命令文档。

配合 [`/gamerule`](../commands/gamerule/) 控制世界规则：

<>`/gamerule keepInventory true`

死亡后保留物品栏。详见 [`/gamerule`](../commands/gamerule/) 命令文档。

>[!NOTE]
> - 基岩版不支持 `/data`、`/loot`、`/item` 等部分 Java 版命令
> - 基岩版从 1.19.50 开始支持旁观模式
> - 返回值使用整数（如 0、1、2），与 Java 版不同
> - 支持 `@initiator` 选择器（仅 NPC 交互时有效）
> - 命令方块中输入命令**不需要**前导斜杠 `/`
> - 聊天栏中输入命令**需要**前导斜杠 `/`

全部命令列表可用 [`/help`](../commands/help/) 查看。