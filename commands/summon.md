---
author: "官方•Dingding OvO"
updatedAt: "2026-08-12"
title: "/summon  生成实体"
category: commands
description: "在指定位置生成实体，支持自定义名称和朝向"
tags: ["实体", "聊天栏", "命令方块", "地图制作", "OP1", "即时", "网易差异"]
---

在指定位置生成实体。

### 语法

`/summon <实体类型> [位置] [命名规则:NameTag]`

### 参数

- `<实体类型>` — 实体 ID（如 `zombie`、`creeper`）
- `[位置]` — 生成坐标 `x y z`（默认：命令执行位置）
- `[命名规则:NameTag]` — 实体的自定义名称（可选，使用 `~` 前缀显示名称始终可见）

### 示例

**生成僵尸**

```mcfunction
/summon zombie ~ ~ ~
```

在当前位置生成僵尸。

**在指定坐标生成苦力怕**

```mcfunction
/summon creeper 100 64 100
```

在指定坐标生成苦力怕。

**生成闪电**

```mcfunction
/summon lightning_bolt ~ ~ ~
```

在当前位置生成闪电，会点燃周围方块并造成伤害。

**生成村民**

```mcfunction
/summon villager ~ ~ ~
```

在当前位置生成村民。

**生成 TNT**

```mcfunction
/summon tnt ~ ~1 ~
```

在头顶生成 TNT（会被立刻激活，生成后引爆）。

**生成自定义命名实体**

```mcfunction
/summon zombie ~ ~ ~ "~Guard"
```

生成一个始终显示名称的僵尸，名称为「Guard」。

### 常用实体 ID

| 实体 | ID |
|------|-----|
| 僵尸 | `zombie` |
| 骷髅 | `skeleton` |
| 苦力怕 | `creeper` |
| 蜘蛛 | `spider` |
| 末影人 | `enderman` |
| 烈焰人 | `blaze` |
| 凋灵骷髅 | `wither_skeleton` |
| 末影龙 | `ender_dragon` |
| 凋灵 | `wither` |
| 村民 | `villager` |
| 流浪商人 | `wandering_trader` |
| 铁傀儡 | `iron_golem` |
| 雪傀儡 | `snow_golem` |
| 恶魂 | `ghast` |
| 岩浆怪 | `magma_cube` |
| 史莱姆 | `slime` |
| 尸壳 | `husk` |
| 爬行者 | `cave_spider` |
| 女巫 | `witch` |
| 幻翼 | `phantom` |
| 掠夺者 | `pillager` |
| 卫道士 | `vindicator` |
| 唤魔者 | `evoker` |
| TNT | `tnt` |
| 闪电 | `lightning_bolt` |
| 盔甲架 | `armor_stand` |
| 矿车 | `minecart` |
| 掉落物 | 不可用 `/summon`（使用 [`/give`](../give/)） |

### 基岩版注意

- 基岩版不支持 Java 版的 NBT 数据参数（如 `{Passengers:[...]}`），无法通过命令直接生成骑乘实体
- 基岩版中使用 `~` 前缀命名时，名称始终可见且不受距离影响
- 某些实体 ID 在基岩版中与 Java 版不同（如末影龙为 `ender_dragon`）
- 基岩版不支持生成带有 `Event` 标签的实体

### 相关命令

- [`/give`](../give/) — 给予玩家物品（掉落物需要用此命令）
- [`/kill`](../kill/) — 移除实体
- [`/tp`](../tp/) — 传送实体到指定位置
- [`/fill`](../fill/) — 批量填充方块（搭配实体生成制作机关）

>[!NOTE]
> - 实体 ID 必须使用小写英文名称
> - 掉落物（item）无法使用 `/summon` 生成，需要使用 `/give` 命令
> - 某些实体（如闪电）生成时会立即产生效果（闪电会点燃周围方块）
> - 命名规则使用 `~名称` 可使实体名称始终可见（不受到距离限制）
> - 生成范围有限制，不能在未加载的区块中生成实体
> - 需要 OP 等级 1
