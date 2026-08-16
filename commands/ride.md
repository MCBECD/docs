---
author: "官方•Dingding OvO, 3-1415f"
updatedAt: "2026-08-12"
title: "/ride  骑乘"
category: commands
description: "管理实体间的骑乘关系，支持骑乘、下骑、逐出乘客及召唤坐骑与乘客"
tags: ["实体", "聊天栏", "命令方块", "地图制作", "OP1", "批量"]
---

`/ride` 控制实体之间的骑乘关系，让实体骑上或被其他实体骑乘、停止骑乘、逐出乘客，以及在指定位置召唤坐骑或乘客。该命令在基岩版 1.16.100 的测试版（beta 1.16.100.52）中首次加入，如今在正式版中可直接使用（需开启作弊）。

### 原理

在基岩版中，「骑乘」不是简单的位置跟随，而是两个实体之间的一组相互引用的状态。被骑乘的实体（坐骑）身上带有一个 `minecraft:rideable` 组件，它维护着一张「座位表」（`seats`）：每个座位是一个独立插槽，只能容纳一名乘客，并记录该乘客在坐骑上的相对位置（`position`）、朝向锁定（`lock_rider_rotation`）与朝向偏移（`rotate_rider_by`）。反过来，乘客实体也保存着一个指向其当前坐骑的引用。在脚本 API 中，这两个方向分别对应 `EntityRideableComponent`（坐骑侧）与 `EntityRidingComponent`（乘客侧）。游戏在每个游戏刻都会读取这对引用，据此同步乘客与坐骑的位置和朝向。

骑乘是否被允许，完全由数据决定。坐骑的 `minecraft:rideable` 组件带有一个 `family_types` 过滤器，其中列出一组「类型族」（type family）；乘客实体则通过自己的 `minecraft:type_family` 组件声明自己属于哪些族。只有当乘客的类型族能匹配坐骑的过滤器时，乘客才被允许坐上某个座位。这正是 `/ride` 无法强行制造任意组合的根本原因：例如原版中猪的过滤器不含僵尸族，所以用 `/ride` 让僵尸骑猪会失败。`/ride` 调用的其实是与玩家手动交互「骑上」同一套挂载逻辑，座位数量、座位上限、族过滤器这些规则对它一视同仁。

每个子命令都是对这组状态的读写操作。`start_riding` 把每名乘客挂到坐骑的一个空座位上；`stop_riding` 与 `evict_riders` 分别从乘客侧和坐骑侧解除这条引用。传送规则只决定在挂载前把哪一方移动到另一方身边，与挂载本身无关。`summon_rider` 与 `summon_ride` 相当于「`/summon` 加 `start_riding`」的组合：先在目标实体位置生成新实体（可附带生成事件和名字），随后立刻尝试挂载，因此同样要经过族过滤器和座位容量的检查。

由于坐骑必须是一个明确的个体，`start_riding` 在坐骑选择器解析出多个实体时会直接失败；而使用 `teleport_ride`（把坐骑传送给乘客）时，乘客也必须唯一，否则传送没有明确的终点。同理，如果坐骑已经满座，或者族过滤器不匹配，命令会失败而不是「硬塞」。这些约束保证了骑乘关系始终是一棵合法、可解析的树，避免出现一个座位挂多个实体之类的损坏状态。

### 语法

`/ride <乘客> start_riding <坐骑> [传送规则] [填充方式]`
让指定乘客骑乘指定坐骑。

`/ride <乘客> stop_riding`
让指定乘客停止骑乘。

`/ride <坐骑> evict_riders`
让指定坐骑逐出身上的所有乘客。

`/ride <坐骑> summon_rider <实体类型> [生成事件] [名称]`
在每个坐骑位置召唤实体，并让其骑到坐骑上成为乘客。

`/ride <乘客> summon_ride <实体类型> [骑乘规则] [生成事件] [名称]`
在每个乘客位置召唤实体作为坐骑，并让乘客骑乘上去。

### 参数

- `<乘客>` — 目标选择器或玩家名，指定骑乘的一方（乘客）。在 `start_riding`、`stop_riding`、`summon_ride` 中使用。
- `<坐骑>` — 目标选择器或玩家名，指定被骑乘的一方（坐骑）。在 `start_riding` 中必须解析为单个实体，在 `evict_riders`、`summon_rider` 中可解析为多个实体。
- `[传送规则]` — 字符串枚举，决定挂载前移动哪一方。可选值：`teleport_ride`（把坐骑传送到乘客身边）、`teleport_rider`（把乘客传送到坐骑身边，默认值）。
- `[填充方式]` — 字符串枚举，决定多名乘客如何坐满坐骑。可选值：`until_full`（逐个尝试挂载直到坐骑满载，默认值）、`if_group_fits`（仅当所有乘客能同时坐上时才全部挂载，否则全部失败）。
- `<实体类型>` — 实体类型 ID（字符串），指定要召唤的实体，如 `zombie`、`horse`、`creeper`。必须是可被 `/summon` 召唤的实体 ID。
- `[生成事件]` — 字符串，指定召唤实体时触发的行为包实体事件（spawn event），如 `minecraft:as_baby_jockey`、`minecraft:become_charged`。省略则不触发额外事件。
- `[名称]` — 字符串，指定被召唤实体的名称。含空格的名称需用双引号括起，特殊字符用 `\` 转义。
- `[骑乘规则]` — 字符串枚举，决定 `summon_ride` 如何处理已在骑乘或正被骑乘的乘客。可选值见「骑乘规则」一节。

### 传送规则与填充方式

| 值 | 说明 | 是否默认 |
|----|------|----------|
| `teleport_rider` | 把乘客传送到坐骑身边，再让乘客骑乘 | 是 |
| `teleport_ride` | 把坐骑传送到乘客身边，再让乘客骑乘 | 否 |
| `until_full` | 逐个尝试挂载，直到坐骑满载 | 是 |
| `if_group_fits` | 仅当所有乘客能同时坐上时才全部挂载，否则全部失败 | 否 |

### 骑乘规则

`[骑乘规则]` 仅用于 `summon_ride`，控制是否为「已经骑在别的实体上」或「正被别的实体骑乘」的乘客召唤坐骑。

| 值 | 说明 |
|----|------|
| `skip_riders` | 只为当前没有在骑乘的实体召唤坐骑，已在骑乘的乘客被跳过 |
| `no_ride_change` | 只为既没有在骑乘、也没有被骑乘的实体召唤坐骑 |
| `reassign_rides` | 先让已在骑乘的乘客下骑，再为所有乘客召唤坐骑（默认值） |

### 示例

**让最近玩家骑乘最近的一匹马：**
```mcfunction
/ride @p start_riding @e[type=horse,limit=1]
```

默认使用 `teleport_rider`，把最近的玩家传送到最近一匹马身边，并让其骑乘上去。

**把坐骑传送到玩家身边再骑乘：**
```mcfunction
/ride @p start_riding @e[type=horse,limit=1] teleport_ride
```

改用 `teleport_ride`，让那匹马被传送到玩家身边，再由玩家骑乘。适合马离玩家较远、想让坐骑主动过来的场景。

**让所有玩家下骑：**
```mcfunction
/ride @a stop_riding
```

解除所有玩家与其坐骑之间的骑乘关系，玩家会在坐骑附近落地。

**让所有马逐出乘客：**
```mcfunction
/ride @e[type=horse] evict_riders
```

从坐骑侧解除关系，每匹马上骑乘的实体都会被逐出，效果与 `stop_riding` 相反方向。

**为所有鸡召唤小僵尸乘客（鸡骑士）：**
```mcfunction
/ride @e[type=chicken] summon_rider zombie minecraft:as_baby_jockey
```

在每只鸡的位置召唤一只僵尸，并通过 `minecraft:as_baby_jockey` 事件将其设为小僵尸形态，使其能通过鸡的骑乘过滤，形成原版的鸡骑士。

**为带标签 A 的玩家召唤坐骑：**
```mcfunction
/ride @a[tag=A] summon_ride horse reassign_rides "A 的坐骑"
```

在每个带标签 `A` 的玩家位置召唤一匹名为「A 的坐骑」的马，并让玩家骑乘上去；`reassign_rides` 会先让已在骑乘的玩家下骑再重新召唤。

### 基岩版注意

- 需要 OP 等级 1，且需在开启作弊的存档中执行。
- 与 Java 版差异很大：Java 版的 `/ride` 使用 `mount` / `dismount` 子命令，基岩版则使用 `start_riding` / `stop_riding` / `evict_riders` / `summon_rider` / `summon_ride` 五种子命令，并额外支持直接召唤坐骑与乘客。
- 骑乘组合受原版数据驱动：基岩版无法让实体骑乘原版不允许的组合，能否骑乘由行为包中坐骑的 `minecraft:rideable` 组件的 `family_types` 决定；Java 版则几乎允许任意实体互相骑乘。
- 该命令在基岩版 1.16.100 的测试版（beta 1.16.100.52）中首次加入，本文档基于最新正式版编写。
- `start_riding` 的坐骑必须解析为单个实体；使用 `teleport_ride` 时乘客也必须唯一，否则命令失败。
- `summon_rider` 与 `summon_ride` 在和平难度下无法召唤敌对生物。

### 常见问题

<details>
<summary>为什么 /ride 无法让某个实体骑上另一个实体？</summary>

基岩版的骑乘由坐骑的 `minecraft:rideable` 组件的 `family_types` 过滤器决定，乘客的类型族必须匹配才能骑乘。原版不允许的组合无法通过 `/ride` 强行实现，需要修改行为包中的实体数据。

</details>

<details>
<summary>start_riding 报错，提示坐骑只能有一个？</summary>

`start_riding` 要求 `<坐骑>` 解析为单个实体。如果你的选择器匹配到了多个实体（例如 `@e[type=horse]` 匹配了多匹马），请加上 `limit=1`，或改用更精确的选择器。

</details>

<details>
<summary>如何让实体在生成时就骑上另一个实体？</summary>

使用 `summon_rider`（召唤乘客并让其骑上指定坐骑）或 `summon_ride`（召唤坐骑并让指定乘客骑上）。两者都会在目标实体位置生成新实体并立即尝试挂载，但仍受骑乘过滤器和座位容量的限制。

</details>

<details>
<summary>/ride 和 /tp 有什么区别？</summary>

`/tp` 只改变实体的坐标，不建立骑乘关系；`/ride` 建立或解除的是持久的骑乘状态，乘客会随坐骑移动。`/ride` 的传送规则只决定挂载前谁移动到谁身边。

</details>

### 相关命令

- [`/summon`](../summon/) — 召唤实体，`summon_rider` 与 `summon_ride` 的底层生成逻辑与之相同。
- [`/tp`](../tp/) — 传送实体，`/ride` 的传送规则只移动挂载双方的位置。
- [`/execute`](../execute/) — 在不同上下文（位置、维度、条件）中执行骑乘命令。
- [`/tag`](../tag/) — 给实体添加标签，便于用 `@e[tag=...]` 精确选择骑乘对象。
