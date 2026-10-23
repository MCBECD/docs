---
title: "计分板状态机"
description: "用 dummy 目标存玩家状态、假名玩家存全局变量，用 scores 条件驱动命令分支"
author: "官方•Dingding OvO"
updatedAt: "2026-10-23"
tags: ["计分板", "命令方块"]
---

基岩版的计分板只有 `dummy` 一种准则，分数只能由命令读写。这个限制反而让它成了一个干净的变量容器：一个目标可以给每个玩家存一个整数，也可以用一个不存在的"假名玩家"存全局变量。菜单、计时、阶段、开关，都是靠它搭出来的。

### 原理

**分数就是状态。** `scoreboard players set @s 菜单 3` 把某个玩家的状态写成 3，选择器 `@a[scores={菜单=3}]` 就把"状态为 3 的玩家"筛出来。整套逻辑因此可以写成"读分数 → 命中分支 → 写回分数"。

**选择器过滤是瞬时求值。** `@a[scores={菜单=3}]` 不是"记住哪些人是 3"，而是每次执行到这一句时重新判断。所以同一刻内改了分数，后面的命令就会按新值走。

**假名玩家是全局变量。** 计分板允许把分数挂在任何名字上，哪怕这个名字不是玩家。`scoreboard players set 计时 时间 60` 里的 `计时` 永远不会被 `@a` 匹配到，但分数照样存得住。这正好用来做全服共享的计数器：倒计时、轮次、总开关。

**整表删除是最干净的复位。** `scoreboard objectives remove 传送` 一条命令就把这个目标和它所有玩家的分数一起删掉，不用逐个 `set 0`，离线玩家留下的旧值也一并清掉。代价是目标本身没了，下次要用得重新 `add`。

### 值的编码

一个整数能存 32 位，所以一个目标可以塞多个状态位：

| 用法 | 写法 | 适合 |
|---|---|---|
| 单值状态 | `菜单 = 0..6`，0 表示关闭 | 菜单、阶段 |
| 布尔标记 | `已签到 = 0/1` | 是否领过奖励 |
| 区间分档 | `等级 = 1..10`，用 `matches 5..` 判断 | 阈值触发 |
| 位编码 | `状态 = 位1*1 + 位2*2 + 位4*4` | 省目标数，但极难读 |

前三种够用。位编码只在目标数量受限时才值得，读起来很痛苦。

### 示例

**六项菜单的状态**：

<CmdChat>`/scoreboard objectives add 菜单 dummy`

<CmdChain>`/scoreboard players add @p[r=2] 菜单 1`

<CmdChain>`/scoreboard players set @a[scores={菜单=7..}] 菜单 1`

<CmdChain>`/titleraw @a[scores={菜单=1..6}] actionbar {"rawtext":[{"text":"当前选项："},{"score":{"name":"@s","objective":"菜单"}}]}`

第一条建目标，第二条在丢物品时给附近玩家加 1，第三条把超过 6 的分拉回 1 形成轮转，第四条按分数渲染提示。复位就是 `scoreboard players set @a 菜单 0`。

**假名玩家当倒计时**：

<CmdChat>`/scoreboard players set 计时 时间 60`

<CmdChain>`/execute if score 计时 时间 matches 0..60 run scoreboard players remove 计时 时间 1`

<CmdChain>`/execute if score 计时 时间 matches ..-1 run tellraw @a {"rawtext":[{"text":"时间到"}]}`

第二条挂在延迟 20 的重复方块上，每秒减 1；第三条在归零后触发一次收尾。`计时` 不是玩家，所以这段逻辑与在线人数无关。

**用分数差检测死亡**：

<CmdChain>`/scoreboard players add @a 存活 1`

<CmdChain>`/scoreboard players set @e[type=player] 存活 0`

<CmdChain>`/scoreboard players add @a[scores={存活=1}] 死亡 1`

每刻先给所有人的"存活"加 1，再给在线玩家的"存活"清零——只有在这一刻死掉的玩家还留着 1，于是"死亡"加 1。这是基岩版没有死亡事件时最常用的替代写法。

### 基岩版注意

- 只有 `dummy` 一种准则。Java 版那些 `deathCount`、`health`、`playerKillCount` 在基岩版都不存在，全部要靠上面的差值法自己算。
- 分数随世界存档持久化。忘了复位，玩家下次进来会带着上次的旧状态。
- `@a` 匹配不到离线玩家，离线玩家的分数留在板上不动。用整表删除而不是逐个清零，能顺手解决这个残留问题。
- `scores={目标=值}` 里的值支持区间：`1..`、`..5`、`3..5`、`!0`（不等于 0）。
- 显示位置只有 `list`、`sidebar`、`belowname` 三处，每处只能挂一个目标。
- `players operation` 支持在两个分数之间做加减乘除与取余，具体运算符见 [`/scoreboard`](../commands/scoreboard/)。
