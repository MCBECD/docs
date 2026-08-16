---
author: "官方•Dingding OvO"
updatedAt: "2026-08-12"
title: "/give  给予物品"
category: commands
description: "给予玩家指定物品，支持数量、数据值与组件"
tags: ["物品", "玩家", "聊天栏", "生存", "创造", "OP1", "多目标"]
---

`/give` 直接向一名或多名玩家发放物品，是制作地图、发放奖励与调试玩法时最常用的命令之一。基岩版 1.20.50 起弃用了物品变体数据值，改用独立物品 ID；但 `[数据]` 参数仍保留在语法中，且仅对烟花类物品有意义。

### 原理

`/give` 与「在世界上生成一个掉落物让玩家捡起来」的流程完全不同。它不创建掉落物实体，而是把构造好的物品堆（ItemStack）直接写入目标玩家的物品栏容器，因此目标无论身处何处、是否开启拾取，都能立刻收到物品。这是它与 `/summon`、`/structure`、`/loot` 等间接发放手段最本质的区别。

命令执行时，引擎先把物品参数解析为物品堆：把物品 ID 字符串在物品注册表（Item 枚举）中解析为基础物品，再依次套用数量、数据值与组件 JSON，合成一个携带完整属性的物品堆。组件 JSON 是一组键值对，键为组件 ID（如 `minecraft:item_lock`），值为该组件的配置；基岩版通过 `/give` 只能写入 `can_place_on`、`can_destroy`、`keep_on_death`、`item_lock` 四类组件，无法像 Java 版那样用 NBT 直接附魔或改名。

物品堆进入物品栏时，会先尝试与已有的同类物品堆叠合并，受该物品最大堆叠上限约束；合并不完的部分再占用空槽位。若 `<玩家>` 是 `@a` 这类多目标选择器，命令会对解析出的每个目标各执行一次发放，这正是 `/give` 被归类为多目标命令的原因。

四个可写组件分别挂接到不同系统：`can_place_on` 与 `can_destroy` 会写入物品的「可放置/可破坏方块白名单」，仅在冒险模式的交互判定中被读取；`keep_on_death` 相当于给单个物品打上「死亡不掉落」标记，效果与游戏规则 `keepInventory` 类似，但按物品细分；`item_lock` 则修改物品栏的移动与丢弃规则，`lock_in_inventory` 阻止丢弃、移出与合成，`lock_in_slot` 进一步禁止在同一物品栏内挪动。

### 语法

`/give <玩家> <物品> [数量] [数据] [组件]`

**1.20.50 及以上：** `[数据]` 不再用于物品变体，仅烟花类物品使用该值；变体物品请直接填写独立物品 ID（如 `granite`）。

**1.20.50 以下：** `[数据]` 用于选择物品变体，例如 `stone 1` 表示花岗岩、`stone 3` 表示闪长岩、`stone 5` 表示安山岩。

### 参数

- `<玩家>` — 目标选择器或玩家名。指定接收物品的目标，支持 `@p`、`@a`、`@s` 及带条件的 `@a[参数]`，也可直接填写玩家名（如 `Steve`）。
- `<物品>` — 物品 ID，类型为字符串。要给予的物品的唯一标识符，例如 `diamond`、`diamond_sword`、`stone`。
- `[数量]` — 整数，默认 1。要给予的物品数量，受该物品最大堆叠上限约束（多数物品为 64 个一组）。
- `[数据]` — 整数。1.20.50 之前用于选择物品变体；1.20.50 起普通物品不再使用该值，仅烟花类物品用它编码信息，给普通物品时填 0 占位即可。
- `[组件]` — JSON 对象。附加在物品上的组件集合，键为组件 ID、值为组件配置，仅支持 `can_place_on`、`can_destroy`、`keep_on_death`、`item_lock` 四类。

### 物品组件

基岩版通过 `[组件]` 参数只能写入四类物品组件，且必须写成 JSON 对象。组件 ID 的 `minecraft:` 命名空间可以省略，例如 `minecraft:item_lock` 可简写为 `item_lock`。

| 组件 ID | JSON 示例 | 说明 |
| --- | --- | --- |
| `minecraft:can_place_on` | `{"minecraft:can_place_on":{"blocks":["planks"]}}` | 指定冒险模式下该方块可放置在哪些方块上；仅在冒险模式生效，方块被放置后再次捡起会丢失该组件 |
| `minecraft:can_destroy` | `{"minecraft:can_destroy":{"blocks":["planks","wood"]}}` | 指定冒险模式下可破坏的方块白名单；仅在冒险模式生效 |
| `minecraft:keep_on_death` | `{"minecraft:keep_on_death":{}}` | 死亡后保留在物品栏，效果类似游戏规则 `keepInventory`，但只针对单个物品 |
| `minecraft:item_lock` | `{"minecraft:item_lock":{"mode":"lock_in_inventory"}}` | 锁定物品；`mode` 取 `lock_in_inventory`（禁止丢弃、移出、合成、改名）或 `lock_in_slot`（进一步禁止在物品栏内移动） |

### 常用物品 ID

| 物品 | 物品 ID |
| --- | --- |
| 钻石 | `diamond` |
| 钻石剑 | `diamond_sword` |
| 金苹果 | `golden_apple` |
| 附魔金苹果 | `enchanted_golden_apple` |
| 命令方块 | `command_block` |
| 基岩 | `bedrock` |
| 石头 | `stone` |
| 花岗岩 | `granite` |
| 闪长岩 | `diorite` |
| 安山岩 | `andesite` |

### 示例

**给予最近玩家 64 个钻石：**

<>`/give @p diamond 64`

给予距离执行者最近的玩家 64 个钻石，物品直接进入其物品栏，无需拾取。

**给予所有在线玩家一把钻石剑：**

<>`/give @a diamond_sword`

省略数量时默认给予 1 个，服务器中每个在线玩家各获得一把钻石剑。

**给自己一块只能放置在木板上的圆石：**

<>`/give @s cobblestone 1 0 {"minecraft:can_place_on":{"blocks":["planks"]}}`

在聊天栏执行时 `@s` 指执行者本人。你获得 1 块带放置限制的圆石，它只能被放置在木板上，且该限制只在冒险模式下生效。

**给予所有玩家一个锁定在物品栏里的金苹果：**

<>`/give @a golden_apple 1 0 {"minecraft:item_lock":{"mode":"lock_in_inventory"}}`

每个在线玩家各获得 1 个金苹果，物品左上角显示黄色三角标记，无法被丢弃、移出物品栏或用于合成。

**给予指定玩家一把死亡后保留的弓：**

<>`/give Steve bow 1 0 {"minecraft:keep_on_death":{}}`

玩家 `Steve` 获得 1 把弓。即使他死亡且世界未开启 `keepInventory`，这把弓也会保留在物品栏中，不会随死亡掉落。

### 基岩版注意

- 需要 OP 等级 1；在聊天栏执行时还需世界已开启「允许作弊」。
- 与 Java 版的主要差异：基岩版不支持 NBT 标签，`/give` 无法直接附魔、改名或写 lore，只能写入 `can_place_on`、`can_destroy`、`keep_on_death`、`item_lock` 四类组件。附魔请改用 [`/enchant`](../enchant/)，带自定义属性的物品可通过 `/structure`、`/loot` 或附加包获得。
- 1.20.50 起物品变体数据值被独立物品 ID 取代（花岗岩从 `stone 1` 变为 `granite`），`[数据]` 参数仅对烟花类物品有意义。
- `[组件]` 是位置参数：要写组件时必须先把 `[数量]` 与 `[数据]` 填好（如 `1 0`），否则 JSON 会被解析到错误的位置。
- `can_place_on` 与 `can_destroy` 只在冒险模式生效；其中 `can_place_on` 的方块被放置后再次捡起会丢失该组件。

### 常见问题

<details>
<summary>为什么我写的附魔组件不生效？</summary>

基岩版 `/give` 不支持 `enchantments` 之类的附魔组件，这与 Java 版不同。要给物品附魔，请先用 `/give` 得到物品，再手持它执行 [`/enchant`](../enchant/)。

</details>

<details>
<summary>为什么写了组件却报错或没有效果？</summary>

组件是位置参数，前面必须先填 `[数量]` 和 `[数据]` 两个占位值，例如 `1 0`。此外请检查组件 ID 是否拼写正确，以及 `can_place_on`、`can_destroy` 是否在冒险模式下测试。

</details>

<details>
<summary>如何给满物品栏的玩家发放物品？</summary>

物品会优先合并到已有的同类堆叠、再占用空槽位；物品栏确实没有空间时的具体行为建议在目标版本中实测确认（待验证）。

</details>

### 相关命令

- [`/enchant`](../enchant/) — 为手持物品添加附魔，弥补 `/give` 无法直接附魔的缺口。
- [`/clear`](../clear/) — 从物品栏移除物品，是 `/give` 的反向操作。
- [`/summon`](../summon/) — 在世界上生成掉落物实体，需要「丢在地上」时可作为替代方案。
