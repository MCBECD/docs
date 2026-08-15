---
author: "官方•Dingding OvO"
updatedAt: "2026-08-12"
title: "/scoreboard  计分板"
category: commands
description: "管理计分板目标、玩家分数和显示设置"
tags: ["计分板", "数据追踪", "命令方块", "聊天栏", "OP1", "多人", "循环"]
---

管理计分板目标、玩家分数和显示设置。计分板是基岩版中实现数据追踪、条件判断和复杂游戏逻辑的核心工具。

### 子命令一览

#### 目标管理

`/scoreboard objectives add <名称> <准则> [显示名称]`

`/scoreboard objectives remove <名称>`

`/scoreboard objectives list`

`/scoreboard objectives setdisplay <栏位> <名称> [排序]`

#### 玩家分数

`/scoreboard players add <玩家> <目标> <数值>`

`/scoreboard players remove <玩家> <目标> <数值>`

`/scoreboard players set <玩家> <目标> <数值>`

`/scoreboard players reset <玩家> [目标]`

`/scoreboard players list [玩家]`

`/scoreboard players get <玩家> <目标>`

`/scoreboard players test <玩家> <目标> <最小> <最大>`

#### 玩家操作

`/scoreboard players operation <目标玩家> <目标计分项> <运算> <源玩家> <源计分项>`

### 语法

`/scoreboard objectives <add | remove | list | setdisplay> ...`

`/scoreboard players <add | remove | set | reset | list | get | test | operation> ...`

### 参数

- `<名称>` — 计分板目标名称（不含空格，可用下划线）
- `<准则>` — 计分准则类型
- `[显示名称]` — 目标的显示名称
- `<栏位>` — 显示栏位
- `<玩家>` — 目标玩家或实体（用 `*` 表示所有已追踪玩家）
- `<数值>` — 数值

### 常用准则

| 准则 | 说明 |
| ------ | ------ |
| `dummy` | 不自动计分，仅由命令更改（最常用） |
| `deathCount` | 死亡次数（自动计数） |
| `playerKillCount` | 击杀玩家次数（自动计数） |
| `totalKillCount` | 总击杀数（自动计数） |
| `health` | 生命值（只读，实时更新） |
| `xp` | 当前经验等级（只读） |
| `foodLevel` | 饥饿值（只读） |
| `level` | 经验等级（只读） |

### 显示栏位

| 栏位 | 说明 |
| ------ | ------ |
| `sidebar` | 屏幕右侧分数列表 |
| `list` | Tab 列表中玩家名称旁 |
| `belowname` | 玩家名称标签下方 |

### 运算符

| 运算 | 说明 |
| ------ | ------ |
| `=` | 设为值 |
| `+=` | 加 |
| `-=` | 减 |
| `*=` | 乘 |
| `/=` | 整数除法 |
| `%=` | 取余 |
| `<` | 取最小值 |
| `>` | 取最大值 |
| `><` | 交换两个玩家的值 |

### 示例

**创建计分板目标：**
```mcfunction
/scoreboard objectives add money dummy "金币"
```

创建一个名为 money 的 dummy 类型计分板目标，在侧边栏显示名称为「金币」。

**在侧边栏显示计分板：**
```mcfunction
/scoreboard objectives setdisplay sidebar money
```

将 money 计分板目标的分数显示在屏幕右侧的侧边栏上，所有玩家均可看到。

**给玩家加 10 分：**
```mcfunction
/scoreboard players add @p money 10
```

给最近的玩家在 money 目标上加 10 分。如果该玩家之前没有此目标的分数记录，则从 0 开始累加。

**设置玩家的分数：**
```mcfunction
/scoreboard players set @p money 100
```

将最近玩家在 money 目标上的分数直接设为 100，覆盖原有值。

**查询玩家分数：**
```mcfunction
/scoreboard players get @p money
```

在聊天栏中显示最近玩家在 money 目标上的当前分数。

**条件判断分数范围：**
```mcfunction
/scoreboard players test @p money 50 100
```

测试最近玩家在 money 目标上的分数是否在 50 到 100 之间（含边界值）。若在范围内则命令成功，否则失败，可用于命令方块条件判断。

**分数运算 — 将玩家 A 的分数加倍：**
```mcfunction
/scoreboard players operation @p money *= @s money
```

将最近玩家在 money 目标上的分数乘以执行者自身的分数，可用于实现分数翻倍等运算逻辑。

**两个玩家的分数相加：**
```mcfunction
/scoreboard players operation Steve money += Alex money
```

将 Alex 的 money 分数加到 Steve 的 money 分数上，实现玩家间分数转移或合并。

**移除计分板目标：**
```mcfunction
/scoreboard objectives remove money
```

彻底删除名为 money 的计分板目标，同时清除所有玩家在该目标上的分数数据。

**重置玩家的分数：**
```mcfunction
/scoreboard players reset @p money
```

清除最近玩家在 money 目标上的分数，但保留该计分板目标本身。

**重置玩家所有分数：**
```mcfunction
/scoreboard players reset @p
```

清除最近玩家在所有计分板目标上的分数数据，相当于完全重置该玩家的计分板记录。

### 基岩版注意

- 需要 OP 等级 1
- 基岩版不支持 `/scoreboard teams` 子命令，无法像 Java 版一样管理队伍计分板
- 基岩版可用准则类型少于 Java 版，Java 版额外支持 `trigger`、`teamkill`、`killedByTeam` 等准则
- Java 版支持在目标选择器中使用 `scores={目标=最小值..最大值}` 语法直接筛选分数，基岩版需配合 `/execute if score` 实现类似功能
- Java 版支持 `/scoreboard objectives modify` 子命令修改已有目标的显示名称和渲染类型，基岩版需删除目标后重新创建
- `dummy` 准则最常用，不会自动计分，完全由命令控制
- 自动计分准则（如 `deathCount`）的分数无法手动修改
- `health`、`foodLevel` 等只读准则只能查询，不能设置
- 计分板值为整数，范围 -2147483648 到 2147483647
- 通配符 `*` 可指定所有已追踪玩家
- 显示名称支持格式化代码（§），如 `§e金币`
- 结合 [`/execute`](../commands/execute/) 命令（特别是 `/execute if score`）可实现复杂的条件逻辑判断
- 配合 [`/tag`](../commands/tag/) 命令可以为实体添加标记，再通过计分板实现更精细的数据分类和逻辑控制
- 常与 [`/effect`](../commands/effect/) 命令结合使用，例如根据计分板分数决定是否给予玩家特定效果
- `/scoreboard players test` 返回值可用于命令方块的条件判断
