---
author: "官方•Dingding OvO"
updatedAt: "2026-08-12"
title: "/scoreboard  计分板"
description: "管理计分板目标、玩家分数和显示设置，基岩版仅支持 dummy 准则"
tags: ["计分板", "数据追踪", "命令方块", "聊天栏", "OP1", "多人", "循环"]
---

管理计分板目标、玩家分数和显示设置。计分板是基岩版命令系统中最核心的数据存储与条件判断工具，几乎所有需要记录数值、计时、计数或状态标记的玩法都建立在它之上。`/scoreboard` 自基岩版 1.7.0 加入，但基岩版始终只支持 `dummy` 这一种准则类型。

### 原理

计分板的本质是一张由服务器维护的持久化键值表：以「计分项 + 分数持有者」作为键，映射到一个 32 位有符号整数。计分项（objective）是这张表的「列」，由 `/scoreboard objectives add` 创建，只携带内部名称、显示名称和准则三个属性。分数持有者（score holder）是这张表的「行」，它可以是真实在线的玩家、某个实体的唯一 ID，也可以是一个从未存在过的字符串名（俗称假名或虚拟分数）。基岩版只提供 `dummy` 一种准则，意味着这些数值永远不会被游戏事件自动改变，只能由命令读写——这正是计分板能充当基岩版「全局变量」的原因：你写入什么值，下次读到的就是什么值，它不会像血量、饥饿值那样自己变动。

分数持有者不需要真的存在。向一个尚未被任何计分项记录的字符串写入分数时，游戏会惰性地为它创建一条记录，并把它纳入「已被计分板追踪」的集合；只有当某个持有者在至少一个计分项中还保有分数时，它才算处于被追踪状态。`players reset` 会清空指定持有者在某个计分项中的分数，一旦它在所有计分项里都没有分数了，就会从追踪集合中被移除。这个机制让你可以把倒计时、开关标志、经济余额等全局数据挂在 `timer`、`flag`、`money` 这类假名上，而不必绑定任何真实玩家。计分板数据随世界存档持久化，重进游戏或重启服务器都不会丢失。

显示槽位是计分板数据之上的「视图」。同一个计分项可以被挂到 `sidebar`、`list` 或 `belowname` 等槽位，游戏界面会实时读取底层数据并渲染，你修改分数后界面立即刷新；同一个槽位同一时刻只能显示一个计分项，再次 `setdisplay` 会顶掉之前挂载的计分项。在条件判断层面，[`/execute`](../execute/) 的 `if score` 子命令直接读取计分板数值作为真值来源，配合重复命令方块，就能构成每游戏刻执行一次的状态机——计分板负责存状态，`/execute` 负责读状态并决定分支走向。有几个边界情况值得注意：`operation` 的 `/=` 与 `%=` 在除数为 0 时，基岩版会让命令「成功但什么都不改」，而 Java 版会直接判为失败；`add`、`remove` 传入负数会反向运算；`players test` 的最小值与最大值允许用 `*` 通配，分别代表 32 位整数的下界与上界。

### 语法

**目标管理：**

`/scoreboard objectives add <目标> dummy [显示名称]`

`/scoreboard objectives remove <目标>`

`/scoreboard objectives list`

`/scoreboard objectives setdisplay <list | sidebar> [目标] [ascending | descending]`

`/scoreboard objectives setdisplay belowname [目标]`

**玩家分数：**

`/scoreboard players add <玩家> <目标> <数值>`

`/scoreboard players remove <玩家> <目标> <数值>`

`/scoreboard players set <玩家> <目标> <数值>`

`/scoreboard players random <玩家> <目标> <最小> <最大>`

`/scoreboard players reset <玩家> [目标]`

`/scoreboard players list [玩家]`

`/scoreboard players test <玩家> <目标> <最小> [最大]`

`/scoreboard players operation <目标玩家> <目标计分项> <运算符> <源玩家> <源计分项>`

### 参数

**目标管理参数：**

- `<目标>` — 计分项名称（字符串）。标识一个计分项数据列，用于后续读写。必须是单个不含空格的词，或用引号包裹的字符串（引号内可用 `\` 转义）；计分项一旦创建就不能改名，只能删除后重建。
- `dummy` — 准则类型（固定值）。基岩版只支持 `dummy`，表示该计分项不会被游戏自动更新，分数只能由命令修改。
- `[显示名称]` — 计分项的显示名称（字符串，可选）。显示在侧边栏标题等处；省略时默认等于 `<目标>` 名称。必须是「非数字的单个词」或用引号包裹的字符串。
- `<list | sidebar>` — 显示槽位（枚举）。`list` 表示玩家列表，`sidebar` 表示屏幕右侧侧边栏。
- `[目标]`（`setdisplay`）— 要挂到该槽位的计分项名称（可选）。省略时清空该显示槽位，恢复为不显示任何计分项。
- `[ascending | descending]` — 排序方式（枚举，可选）。`ascending` 为升序（小到大），`descending` 为降序（大到小）；仅 `list`、`sidebar` 槽位支持，`belowname` 槽位不接受该参数。

**玩家分数参数：**

- `<玩家>` — 分数持有者（选择器或字符串）。可以是目标选择器（`@p`、`@a`、`@s`、`@e` 等）、玩家名、实体的唯一 ID，或 `*`（表示所有被计分板追踪的持有者）。也可以是一个不存在的字符串名（假名），用于存储与具体玩家无关的全局数据；基岩版可同时指定多个持有者。
- `<数值>` — 整数。要增加、减少或设置的分数值，范围在 32 位有符号整数内（-2,147,483,648 到 2,147,483,647）。在 `add`、`remove` 中传入负数会反向运算。
- `<最小>`、`[最大]` — 整数或通配符。`test` 用来判断分数是否落在 `[最小, 最大]` 区间（含两端）。两者都可用 `*` 通配：`*` 作最小值代表 -2,147,483,648，作最大值代表 2,147,483,647。
- `<目标玩家>`、`<目标计分项>` — 运算的目标侧：被写入结果的那一方。目标玩家可以是选择器或玩家名，目标计分项为计分项名称。
- `<运算符>` — 算术运算符（枚举）。可选值见「运算符」章节。
- `<源玩家>`、`<源计分项>` — 运算的源侧：提供参与运算数值的那一方。

### 子命令一览

| 子命令 | 作用 |
|--------|------|
| `objectives add` | 创建新计分项（仅 `dummy` 准则） |
| `objectives remove` | 删除计分项及其所有分数 |
| `objectives list` | 列出全部计分项及其准则 |
| `objectives setdisplay` | 把计分项显示到指定槽位 |
| `players set` | 把分数设为指定值 |
| `players add` | 给分数加上指定值 |
| `players remove` | 给分数减去指定值 |
| `players random` | 把分数设为区间内的随机值 |
| `players reset` | 清空指定（或全部）计分项的分数 |
| `players list` | 列出持有者及其被追踪的计分项 |
| `players test` | 测试分数是否落在指定区间 |
| `players operation` | 用另一个分数对本分数做算术运算 |

### 常用准则

基岩版的 `/scoreboard` 只支持一种准则：

| 准则 | 说明 |
|------|------|
| `dummy` | 不会被游戏自动更新，分数只能由命令读写，是基岩版唯一可用、也最常用的准则 |

Java 版额外支持的 `deathCount`（死亡数）、`playerKillCount`（击杀玩家数）、`health`（生命值）、`xp`（经验）、`foodLevel`（饥饿值）、`level`（经验等级）、`trigger` 等准则在基岩版中均不可用，不要照搬 Java 版教程的准则写法。

### 显示栏位

| 栏位 | 说明 |
|------|------|
| `sidebar` | 在屏幕右侧以列表显示持有该计分项的分数 |
| `list` | 在玩家列表（Tab 键菜单）中每位玩家名称旁显示分数 |
| `belowname` | 在每位玩家头顶的名称标签下方显示分数 |

`list` 与 `sidebar` 支持额外指定 `ascending` 或 `descending` 排序；`belowname` 不接受排序参数。

### 运算符

`/scoreboard players operation` 支持的算术运算符如下：

| 运算符 | 说明 |
|--------|------|
| `=` | 赋值：把源分数直接赋给目标 |
| `+=` | 加法：目标 = 目标 + 源 |
| `-=` | 减法：目标 = 目标 - 源 |
| `*=` | 乘法：目标 = 目标 × 源 |
| `/=` | 整数除法：目标 = 目标 ÷ 源（向下取整） |
| `%=` | 取模：目标除以源后取余数 |
| `<` | 取较小值：目标 = min(目标, 源) |
| `>` | 取较大值：目标 = max(目标, 源) |
| `><` | 交换：互换目标与源的分数 |

注意：`/=` 与 `%=` 在源分数为 0 时，基岩版会让命令成功但目标分数保持不变。

### 示例

**创建计分项：**
<CmdChat>`/scoreboard objectives add money dummy "金币"`

创建一个名为 `money`、准则为 `dummy` 的计分项，其显示名称为「金币」。由于是 `dummy` 准则，它的分数不会自动变化，只能由命令修改。

**在侧边栏显示：**
<CmdChat>`/scoreboard objectives setdisplay sidebar money descending`

把 `money` 计分项挂到屏幕右侧的侧边栏，并按降序显示分数，分数高的条目排在前面。

**给最近玩家加分：**
<CmdChat>`/scoreboard players add @p money 10`

给最近的玩家在 `money` 上增加 10 分；如果该玩家此前没有这条记录，会从 0 开始累加。

**把分数设为固定值：**
<CmdChat>`/scoreboard players set @p money 100`

把最近玩家在 `money` 上的分数直接覆盖为 100，原来的值被丢弃。

**用假名保存全局数据：**
<CmdChat>`/scoreboard players set global_var timer 60`

给名为 `global_var` 的假分数持有者写入 60。`global_var` 并不是真实存在的玩家，但游戏会为它创建记录，这样就能把倒计时这类全局状态存在计分板里，供命令方块读取。

**判断分数区间：**
<CmdChat>`/scoreboard players test @p money 50 100`

测试最近玩家在 `money` 上的分数是否落在 50 到 100 之间（含两端）。命令方块会依据这条命令的「成功 / 失败」结果决定后续分支。

**把一位玩家的分数转给另一位：**
<CmdChat>`/scoreboard players operation Steve money += Alex money`

把 Alex 在 `money` 上的分数加到 Steve 的 `money` 上，实现玩家之间的分数转移；这条命令不会改动 Alex 自己的分数。

**给自己翻倍：**
<CmdChat>`/scoreboard players operation @p money += @p money`

目标与源都指向最近的玩家，等价于把该玩家在 `money` 上的分数加到自己身上，实现翻倍。

**投骰子：**
<CmdChat>`/scoreboard players random @a dice 1 6`

给所有玩家在 `dice` 计分项上各写入一个 1 到 6 之间的随机整数，相当于掷一颗六面骰子；使用前需先用 `objectives add` 创建 `dice` 计分项。

**清空分数：**
<CmdChat>`/scoreboard players reset @p money`

清空最近玩家在 `money` 上的分数记录，但保留 `money` 计分项本身；若该玩家在所有计分项里都没有分数了，就会从追踪集合中移除。

### 基岩版注意

- 需要 OP 等级 1，且世界需开启作弊（命令权限）。
- 基岩版只支持 `dummy` 准则；`deathCount`、`playerKillCount`、`health`、`xp`、`foodLevel`、`level`、`trigger` 等准则均为 Java 版专用，基岩版无法使用。
- 基岩版没有 `scoreboard players get` 子命令，无法直接查询并输出分数；判断分数请用 `players test` 或 [`/execute`](../execute/) 的 `if score`。
- 基岩版没有 `/scoreboard teams`（队伍）与 `objectives modify`（修改已有计分项），需要改名或改准则时只能删除后重建。
- Java 版 1.13 起移除了 `players test`（改用 `/execute if score`），基岩版仍保留 `players test`；因此 Java 版教程里的 `execute if score` 写法可以直接照用，但不要期待基岩版有 `players get`。
- Java 版选择器支持 `scores={目标=最小值..最大值}` 直接按分数筛选，基岩版不支持，需配合 `/execute if score` 实现同样效果。
- 分数是 32 位有符号整数，范围 -2,147,483,648 到 2,147,483,647；超出该范围的溢出行为待验证。
- `players random` 为基岩版特有的子命令，可生成指定区间内的随机分数。
- `operation` 的 `/=`、`%=` 在除数为 0 时命令成功但目标分数不变（Java 版判为失败）。
- `add`、`remove` 传入负数会反向运算（`add -5` 等价于减 5）。

### 常见问题

<details>
<summary>为什么我无法创建 deathCount 之类的准则？</summary>

基岩版只支持 `dummy` 准则。`deathCount`、`health` 等准则只在 Java 版存在，基岩版必须通过命令方块主动计数（例如用 [`/execute`](../execute/) 检测死亡事件后再 `players add`）来实现类似效果。

</details>

<details>
<summary>计分板能存小数吗？</summary>

不能。计分板的值是 32 位有符号整数。需要表示小数时，可以约定「乘以 100 存整数，读取时再除以 100」的方式换算。

</details>

<details>
<summary>怎么做一个每游戏刻 +1 的计时器？</summary>

创建一个 `dummy` 计分项，把一条 `scoreboard players add <玩家> timer 1` 放进「保持开启」的重复命令方块，它会每游戏刻给该持有者加 1 分（每秒约 20 分）。再用 `/execute if score` 判断是否到达目标值即可。

</details>

<details>
<summary>reset 和 remove 有什么区别？</summary>

`players reset` 只清除某个持有者的分数，计分项本身保留；`objectives remove` 会删除整个计分项，同时清空所有持有者在该计分项上的分数。

</details>

### 相关命令

- [`/execute`](../execute/) — 用 `if score` 读取计分板数值做条件判断与状态机控制。
- [`/tag`](../tag/) — 给实体打标签，与计分板配合实现更精细的数据分类。
- [`/effect`](../effect/) — 根据计分板分数决定是否给予状态效果。
- [`/title`](../title/) — 结合计分板分数向玩家展示信息。
