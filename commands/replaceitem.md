---
author: "官方•Dingding OvO"
updatedAt: "2026-10-23"
title: "/replaceitem  替换槽位物品"
description: "直接向指定槽位写入物品，可清空原有物品，是基岩版唯一的槽位操作命令"
tags: ["物品", "玩家", "命令方块", "地图制作", "即时", "多目标"]
---

`/replaceitem` 把一件物品直接放进某个槽位——快捷栏第几格、头盔位、箱子第几格。`/give` 只会把物品塞进物品栏的空位，管不了"哪个格子"；要精确控制槽位，只能用这条。

它也是做菜单、道具、职业装备时最常用的命令：塞一件锁死的物品到固定格子，玩家就始终拿着它。

### 原理

物品栏是一排编号的槽位，每个槽位有自己的类型和序号。`/replaceitem` 做的事就是"把指定槽位的内容改成指定物品"，不经过掉落物，也不管物品栏有没有空位。

两种目标：

- **entity**：改实体的槽位。玩家、盔甲架、箱子矿车都算实体。
- **block**：改方块容器的槽位。箱子、熔炉、漏斗这类带容器界面的方块。

两种可选的"旧物品处理方式"：

- `destroy` — 先把该槽位原有的物品清掉，再放入新的。
- `keep` — 与 `destroy` 相对的处理方式。

不写这一项时按默认方式替换。

### 语法

**改实体槽位**：

`/replaceitem entity <目标> <槽位类型> <槽位号> <物品> [数量] [数据] [组件]`

`/replaceitem entity <目标> <槽位类型> <槽位号> destroy <物品> [数量] [数据] [组件]`

**改方块容器**：

`/replaceitem block <位置> slot.container <槽位号> <物品> [数量] [数据] [组件]`

`/replaceitem block <位置> slot.container <槽位号> destroy <物品> [数量] [数据] [组件]`

### 参数

- `<目标>` — 选择器或玩家名。写 `@a` 就是给所有人各塞一份。
- `<槽位类型>` — 见下表，只能写枚举值。
- `<槽位号>` — 整数。`slot.hotbar` 的编号是 0 到 8，对应快捷栏九格（0 是最左）。
- `<物品>` — 物品 ID，如 `snowball`、`diamond_sword`。写 `air` 表示清空该槽位。
- `[数量]` — 整数，默认 1。
- `[数据]` — 物品变体数据值，多数物品用不上。
- `[组件]` — JSON，可写 `minecraft:item_lock`、`minecraft:keep_on_death`、`minecraft:can_place_on`、`minecraft:can_destroy`、`minecraft:display_name` 等。

常用槽位类型：

| 槽位类型 | 指向 |
|---|---|
| `slot.weapon.mainhand` | 主手 |
| `slot.weapon.offhand` | 副手 |
| `slot.armor.head` | 头盔 |
| `slot.armor.chest` | 胸甲 |
| `slot.armor.legs` | 护腿 |
| `slot.armor.feet` | 靴子 |
| `slot.hotbar` | 快捷栏，编号 0–8 |
| `slot.inventory` | 主物品栏，编号 0–26 |
| `slot.enderchest` | 末影箱 |
| `slot.container` | 方块容器，配合 `block` 使用 |

### 示例

**把雪球钉死在快捷栏第九格**：

<CmdRepeat>`/replaceitem entity @a slot.hotbar 8 destroy snowball 16 0 {"minecraft:item_lock":{"mode":"lock_in_slot"},"minecraft:display_name":{"value":"雪球菜单"}}`

`lock_in_slot` 让这件物品不能被移出这个格子，配合每刻补发，雪球就永远丢不完。这是做"丢物品触发菜单"的标配写法。

**清空主手**：

<CmdChat>`/replaceitem entity @s slot.weapon.mainhand 0 air 1`

**给所有人发一顶头盔**：

<CmdChat>`/replaceitem entity @a slot.armor.head 0 iron_helmet 1`

**往箱子的第一格放一组钻石**：

<CmdChat>`/replaceitem block 100 64 100 slot.container 0 diamond 64`

### 基岩版注意

- 基岩版**没有** `/item replace`。那是 Java 1.20.5 之后的语法，照抄过来会直接报错。`/replaceitem` 是唯一的槽位操作手段。
- `slot.hotbar` 编号从 0 开始，第九格是 8，不是 9。
- 目标槽位不存在（比如给没有物品栏的实体改 `slot.inventory`）时命令失败，不会静默新建。
- 组件里的 `minecraft:item_lock` 有两个模式：`lock_in_slot` 锁死在这一格，`lock_in_inventory` 允许在物品栏内移动但不能丢弃。
- 这条命令每执行一次就写一次槽位，放在重复方块上等同于每刻覆盖，玩家刚把物品移走就会被塞回来。
- `destroy` 与 `keep` 两种模式的差别只在"原有物品怎么处理"，不给这一项时用默认方式替换。

### 常见问题

<details>
<summary>物品塞进去了但玩家一捡东西就没了</summary>

那是有物品自动合并到了这一格。用 `minecraft:item_lock` 的 `lock_in_slot` 锁住，并在重复方块上每刻补发。

</details>

<details>
<summary>能不能给副手放盾牌</summary>

能，`slot.weapon.offhand` 就是副手位，编号写 0。

</details>

<details>
<summary>为什么给箱子放东西没反应</summary>

先确认坐标上有容器方块，且槽位号在容量范围内（单个箱子是 0–26）。`block` 模式只认 `slot.container` 这一种槽位类型，写别的枚举会失败。

</details>

### 相关命令

- [`/give`](../give/) — 发到物品栏空位，管不了具体格子
- [`/clear`](../clear/) — 清空物品栏，按物品或全清
- [`/scoreboard`](../scoreboard/) — 常与槽位操作配合做菜单状态
