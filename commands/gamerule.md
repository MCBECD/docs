---
author: "官方•Dingding OvO"
updatedAt: "2026-08-12"
title: "/gamerule  游戏规则"
description: "设置或查询世界游戏规则，控制昼夜循环、生物生成、掉落、命令方块与玩家伤害等全局行为"
tags: ["状态", "聊天栏", "服务器", "地图制作", "OP1", "即时", "版本敏感"]
---

设置或查询世界的游戏规则（game rule）。游戏规则是一组保存在存档里的全局开关，控制昼夜循环、生物生成、掉落、命令方块与玩家伤害等世界级行为；基岩版规则名统一使用小写，并且旧版中已被移除自建规则的能力。

### 原理

游戏规则的底层本质是存档级的一组键值对。每个世界在 LevelDB 格式的关卡数据里维护一张 GameRules 表，键是规则名、值是布尔值或整数。这些值并不主动「触发」任何事件，而是散布在游戏主循环的各处作为条件闸门：昼夜系统每游戏刻先检查 `dodaylightcycle` 是否为 true，才把世界时间向前推进 1；生物生成系统在尝试生成前检查 `domobspawning`；伤害结算在应用溺水、摔落、火焰、冰冻伤害前分别检查 `drowningdamage`、`falldamage`、`firedamage`、`freezedamage`。因此规则一旦被改写，依赖它的系统从下一个游戏刻起就按新值运行，这也是 `/gamerule` 的效果几乎立即可见的原因。

`/gamerule` 命令本身走一条「解析 → 校验 → 写入 → 广播」的流水线。它先把规则名解析为枚举类型（`BoolGameRule` 或 `IntGameRule`），再校验值的类型：布尔规则只接受 `true` 或 `false`，整数规则接受整数值，类型不符会直接报错而不作任何修改。校验通过后，新值写入世界的 GameRules 表并随存档持久保存，同时服务器把变化同步给所有在线客户端，让客户端侧的显示（如 `showcoordinates` 的坐标 HUD）即时刷新。

游戏规则是「世界级」状态而非「玩家级」或「维度级」状态。你无法给不同玩家或不同维度设置不同的规则，改一处即对整个世界的所有维度、所有玩家生效，并且跨退出重进仍保留。规则之间还构成依赖关系：`commandblocksenabled` 为 false 时命令方块的执行被整体跳过，但聊天栏命令不受影响；`sendcommandfeedback` 与 `commandblockoutput` 分别控制聊天栏里的命令回显与命令方块输出；`randomtickspeed` 直接决定每个区块段每游戏刻执行多少次随机刻（默认 1），从而影响作物生长、冰面冻结融化、火焰蔓延与树叶凋零，设为 0 即完全关闭随机刻。`playerssleepingpercentage` 则规定跳过夜晚所需的睡觉玩家比例。

最后是边界情况：基岩版的规则名大小写不敏感，自动补全统一显示为小写，但拼错规则名或尝试写入不存在的规则会直接报错——基岩版早已移除旧版「虚拟规则（dummy gamerule）」能力，无法自建规则。整数规则有各自的取值范围，例如 `randomtickspeed` 上限为 4096、`spawnradius` 范围为 0 到 128，超出范围的写法可能被拒绝或无效。

### 语法

设置或查询布尔型规则：

`/gamerule <规则: BoolGameRule> [值: Boolean]`

设置或查询整数型规则：

`/gamerule <规则: IntGameRule> [值: int]`

26.30+ 新增的路径点规则：

`/gamerule playerwaypoints <值: playerwaypointsValues>`

省略 `[值]` 时查询规则当前值，结果回显在聊天栏。三种写法遵循同一规则：规则名在前、可选的值在后。

### 参数

- `<规则: BoolGameRule>` — 布尔型游戏规则名，取值只能是 `true` 或 `false`。规则名统一使用小写（如 `keepinventory`、`dodaylightcycle`），基岩版输入时大小写不敏感。
- `<规则: IntGameRule>` — 整数型游戏规则名，取值是整数（如 `randomtickspeed`、`spawnradius`、`playerssleepingpercentage`）。
- `[值: Boolean]` — 布尔值，`true` 表示开启、`false` 表示关闭。省略时查询该规则当前值。
- `[值: int]` — 整数值，取值范围由具体规则决定。省略时查询该规则当前值。
- `<值: playerwaypointsValues>` — 路径点规则 `playerwaypoints` 的取值，可选 `everyone`（所有人可见）或 `off`（关闭），仅 26.30+ 可用。

### 常用规则

#### 布尔型规则

| 规则 | 默认 | 说明 |
|------|------|------|
| `keepinventory` | `false` | 玩家死亡时是否保留物品栏与经验 |
| `dodaylightcycle` | `true` | 世界时间是否每游戏刻自动推进 |
| `doweathercycle` | `true` | 天气是否自动变化 |
| `domobspawning` | `true` | 生物是否自然生成 |
| `dofiretick` | `true` | 火焰是否自然蔓延与熄灭 |
| `mobgriefing` | `true` | 苦力怕爆炸、末影人搬方块等生物破坏是否生效 |
| `dotiledrops` | `true` | 方块被破坏后是否掉落物品 |
| `domobloot` | `true` | 生物死亡后是否掉落战利品 |
| `doentitydrops` | `true` | 船、矿车等非生物实体是否掉落 |
| `showcoordinates` | `false` | 屏幕上是否显示坐标（基岩版独有） |
| `commandblocksenabled` | `true` | 命令方块是否能够执行 |
| `commandblockoutput` | `true` | 命令方块的输出是否显示在聊天栏 |
| `sendcommandfeedback` | `true` | 命令的回显是否显示在聊天栏 |
| `showdeathmessages` | `true` | 死亡信息是否显示 |
| `doimmediaterespawn` | `false` | 死亡后是否跳过死亡界面立即重生 |
| `naturalregeneration` | `true` | 饱食度充足时是否自然回血 |
| `pvp` | `true` | 玩家之间是否能互相造成伤害 |
| `tntexplodes` | `true` | TNT 是否爆炸 |
| `drowningdamage` / `falldamage` / `firedamage` / `freezedamage` | `true` | 分别控制溺水、摔落、火焰、冰冻伤害是否生效 |

#### 整数型规则

| 规则 | 默认 | 说明 |
|------|------|------|
| `randomtickspeed` | `1` | 随机刻速度，控制作物生长、冰霜融化等随机事件频率，范围 0-4096 |
| `spawnradius` | `10`（1.20.40+） | 出生点分布半径，范围 0-128，旧版默认 5 |
| `playerssleepingpercentage` | `100` | 跳过夜晚所需的睡觉玩家百分比 |
| `functioncommandlimit` | `10000` | 单个函数单次执行的命令数量上限（最大 10000） |
| `maxcommandchainlength` | 待验证 | 一条命令链允许的最大长度 |

以上为常用规则，并非全部；完整列表可查阅 [Minecraft Wiki](https://minecraft.wiki/w/Game_rule)。

### 示例

**查询 keepinventory 的当前值：**
<CmdChat>`/gamerule keepinventory`

返回该规则当前设置（例如 `keepinventory = false`），不修改任何值。

**开启死亡不掉落：**
<CmdChat>`/gamerule keepinventory true`

玩家死亡后保留物品栏中的所有物品与经验值，不再掉落。

**锁定当前时间：**
<CmdChat>`/gamerule dodaylightcycle false`

世界时间不再每游戏刻自动推进，昼夜停在当前时刻。之后用 `/time set` 仍可手动改变时间。

**加快作物生长：**
<CmdChat>`/gamerule randomtickspeed 3`

把随机刻速度提高到 3 倍，作物生长、冰霜融化等随机事件明显加快。

**在屏幕显示坐标：**
<CmdChat>`/gamerule showcoordinates true`

屏幕左上角持续显示你当前的三维坐标。

**制作静音命令方块系统：**

<CmdChat>`/gamerule sendcommandfeedback false`
<CmdChat>`/gamerule commandblockoutput false`

关闭命令回显与命令方块输出，让命令方块批量执行时不再刷屏聊天栏。

### 基岩版注意

- 需要 OP 等级 1；Java 版则需要等级 2。
- 规则名统一小写且大小写不敏感（自动补全显示小写）；Java 版使用驼峰命名且区分大小写，例如基岩版写 `keepinventory`，Java 版写 `keepInventory`。
- `showcoordinates`、`showtags`、`respawnblocksexplode`、`showdaysplayed`、`showrecipemessages` 等规则为基岩版独有，Java 版没有对应项。
- 部分规则（如 `showcoordinates`）可在未开启作弊时修改，其余多数规则需要开启作弊；世界创建与编辑界面也允许直接改一部分规则。
- 基岩版已移除「虚拟规则」能力，写入不存在的规则名会报错，无法自建规则。
- `spawnradius` 默认值在 1.20.40 起由 5 改为 10，与 Java 版对齐；`showdaysplayed`、`showrecipemessages`、`projectilescanbreakblocks`、`tntexplosiondropdecay` 等为较新版本才加入的规则。
- 游戏规则是世界级全局设置，无法按玩家或维度单独设置；修改后自动保存到存档，重启世界仍生效。
- 网易版与国际版在规则集与默认值上可能存在细微差异，本文档基于国际版编写。

### 常见问题

<details>
<summary>规则名应该用大写还是小写？</summary>

基岩版不区分大小写，自动补全统一显示为小写。建议一律写小写（如 `keepinventory`、`dodaylightcycle`）。Java 版才使用驼峰命名并严格区分大小写。

</details>

<details>
<summary>`/gamerule keepInventory true` 没有生效？</summary>

基岩版规则名大小写不敏感，`keepInventory` 与 `keepinventory` 等价，都能被识别。若仍无效，检查你是否已获得 OP 等级 1、该世界是否开启作弊（多数规则需要），以及命令是否在聊天栏或命令方块中正确执行。

</details>

<details>
<summary>能只对某个玩家或某个维度设置规则吗？</summary>

不能。游戏规则是世界级全局设置，作用于整个世界的所有维度与所有玩家，无法单独指定目标。

</details>

<details>
<summary>关闭 `domobspawning` 后 `/summon` 还能召唤生物吗？</summary>

能。`domobspawning` 只关闭自然生成，`/summon` 直接召唤实体不受该规则限制。

</details>

<details>
<summary>如何把某条规则恢复为默认值？</summary>

直接设置为默认值即可，例如 `/gamerule keepinventory false`、`/gamerule randomtickspeed 1`。默认值可查阅上方「常用规则」表格。

</details>

### 相关命令

- [`/time`](../time/) — 配合 `dodaylightcycle` 控制世界时间是否推进，实现完全的时间控制
- [`/weather`](../weather/) — `doweathercycle` 控制天气是否自动变化，配合手动设置天气
- [`/difficulty`](../difficulty/) — 难度设置与部分规则（如和平难度禁止敌对生物生成）相关
