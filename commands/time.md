---
author: "官方•Dingding OvO"
updatedAt: "2026-08-12"
title: "/time  时间控制"
category: commands
description: "查询或设置游戏内时间，控制昼夜与刻流转"
tags: ["世界", "聊天栏", "创造", "OP1", "即时"]
---

查询或设置游戏内时间。

### 语法

`/time set <值>`

`/time add <值>`

`/time query <类型>`

### 参数

- `<值>` — 整数（游戏刻，0-24000）或关键字（`day`、`noon`、`sunset`、`night`、`midnight`）
- `<类型>` — 查询类型，可选值：`daytime`（当天游戏时间）、`gametime`（世界总游戏时间）、`day`（已过的游戏天数）

### 时间值

| 值 | 对应时间 |
|------|------|
| `0` | 正午（日中，太阳最高点） |
| `1000` | 日出（清晨） |
| `6000` | 日落（傍晚） |
| `12000` | 午夜（深夜） |
| `18000` | 日出（次日清晨） |
| `day` | 设为日出（1000） |
| `noon` | 设为正午（6000） |
| `sunset` | 设为日落（12000） |
| `night` | 设为午夜（13000） |
| `midnight` | 设为深夜（18000） |

> 游戏中一天为 24000 刻（20 分钟真实时间），每秒 20 刻。0 刻为正午，6000 刻为日落，12000 刻为午夜，18000 刻为日出。`day` 等同于 1000（日出），`night` 等同于 13000（午夜后不久）。

### query 类型

| 类型 | 说明 |
|------|------|
| `daytime` | 当天游戏时间（0-24000） |
| `gametime` | 世界总游戏时间 |
| `day` | 已过的游戏天数 |

### 示例

**设置为白天：**
```mcfunction
/time set day
```

将游戏时间设为日出（1000 刻），立即进入白天。

**时间前进 1000 刻：**
```mcfunction
/time add 1000
```

在当前时间基础上前进 1000 刻（相当于 50 秒真实时间），适合用于缓慢推进时间。

**设为正午并锁定时间：**
```mcfunction
/time set 6000
```
```mcfunction
/gamerule doDaylightCycle false
```

将时间固定在正午（6000 刻），同时关闭日夜循环，实现永久白天。配合 [`/gamerule`](../commands/gamerule/) 的 `doDaylightCycle` 规则使用。

**查询当前游戏时间：**
```mcfunction
/time query daytime
```

在聊天栏显示当前世界的游戏时间数值（0-24000），可用于调试或条件判断。

### 基岩版注意

- 需要 OP 等级 1
- 基岩版中 `/time set` 和 `/time add` 的行为与 Java 版基本一致，但 `query` 的返回值仅显示在聊天栏中，无法通过 `/execute store` 存储
- 基岩版不支持 `daytime` 以外的 query 类型（如 `gametime` 和 `day`）在某些旧版本中可能不可用
- 如需永久锁定时间，建议配合 `/gamerule doDaylightCycle false` 使用

**相关命令：** [`/weather`](../commands/weather/)、[`/gamerule`](../commands/gamerule/)
