---
author: "官方•Dingding OvO"
updatedAt: "2026-08-12"
title: "/locate  定位结构"
category: commands
description: "定位最近的生物群系或结构坐标，支持限定仅搜索新生成区块"
tags: ["世界", "聊天栏", "创造", "地图制作", "OP1", "即时"]
---

查找当前维度中距离执行者最近的指定结构或生物群系的坐标，并把结果输出到聊天栏。基岩版 1.19.30 起，旧版 `/locate <结构>` 语法被 `structure` 与 `biome` 两个子命令取代；1.21.100 起，生物群系 ID 必须带 `minecraft:` 命名空间。

### 原理

`/locate` 定位结构时并不扫描世界里的方块，而是查询世界的结构放置数据。世界生成时，每种结构都根据世界种子和它所属结构集的间距规则，被预先安排在固定的网格位置上，这些位置在区块真正生成之前就已经由种子确定。因此命令能在尚未探索、甚至尚未生成的区块中找到结构，这正是基岩版独有参数 `useNewChunksOnly` 能够成立的原因。

命令只搜索执行者当前所在的维度，并且有有限的搜索范围。如果目标结构根本不生成在当前维度（例如在主世界搜索 `fortress`），命令会直接失败并提示该维度不生成此结构；如果范围内找不到任何实例，则返回无法定位的错误。返回的坐标是结构「起点」的近似水平位置，垂直坐标被忽略（输出中用 `~` 表示），因为结构的实际高度由落地处的地形决定，不参与定位计算。基岩版的精确搜索范围官方未明确公开，本处标「待验证」。

`/locate biome` 走另一条路径，查询的是生物群系数据。命令在搜索范围内以 32 格的水平分辨率、64 格的垂直分辨率对生物群系进行采样，因此过于狭窄或细长的生物群系可能因为采样点恰好落在相邻生物群系而找不到。与结构不同，生物群系定位会返回完整的三维坐标。

从状态角度看，`/locate` 是一次只读查询：它不会生成区块、不会传送实体，也不会改写任何世界状态或计分板。它只读取由种子衍生的结构位置数据或生物群系数据，把坐标组织成聊天消息返回。在命令方块或 [`/execute`](../execute/) 中，命令成功时返回成功计数 1、失败返回 0，可用于条件判断；但基岩版不会像 Java 版那样把距离作为 `result` 值供 `/execute store` 读取。

### 语法

**结构定位（1.19.30+）：**

`/locate structure <结构> [useNewChunksOnly]`

**生物群系定位（1.19.10+）：**

`/locate biome <生物群系>`

1.19.30 之前旧版直接写 `/locate <结构>`，现已弃用。

### 参数

**`/locate structure` 的参数：**

- `<结构>` — 结构 ID（StructureFeature 枚举），要定位的结构类型。基岩版结构 ID 不带 `minecraft:` 命名空间，完整列表见下方「结构 ID」表。
- `[useNewChunksOnly]` — 布尔值（`true` 或 `false`），指定是否只在新生成的区块中查找。默认 `false`，即在所有区块中查找；设为 `true` 时只返回尚未生成区块里的结构，适合寻找未探索区域内的建筑。

**`/locate biome` 的参数：**

- `<生物群系>` — 生物群系 ID（Biome 枚举），要定位的生物群系。1.21.100 起必须带 `minecraft:` 命名空间（如 `minecraft:desert`），常见值见「生物群系 ID」表。

### 结构 ID

| 结构 ID | 说明 | 所在维度 |
|---------|------|----------|
| `village` | 村庄 | 主世界 |
| `stronghold` | 要塞（含末地传送门） | 主世界 |
| `mineshaft` | 废弃矿井 | 主世界 |
| `mansion` | 林地府邸 | 主世界 |
| `monument` | 海底遗迹 | 主世界 |
| `shipwreck` | 沉船 | 主世界 |
| `ruins` | 海底废墟 | 主世界 |
| `buried_treasure` | 埋藏的宝藏 | 主世界 |
| `pillager_outpost` | 掠夺者前哨站 | 主世界 |
| `ruined_portal` | 废弃传送门 | 主世界、下界 |
| `ancient_city` | 远古城市 | 主世界 |
| `trail_ruins` | 古迹废墟 | 主世界 |
| `trial_chambers` | 试炼密室 | 主世界 |
| `temple` | 沙漠神殿、雪屋、丛林神庙、沼泽小屋（共用） | 主世界 |
| `fortress` | 下界要塞 | 下界 |
| `bastion_remnant` | 堡垒遗迹 | 下界 |
| `end_city` | 末地城 | 末地 |

### 生物群系 ID

| 生物群系 ID | 说明 |
|-------------|------|
| `minecraft:plains` | 平原 |
| `minecraft:desert` | 沙漠 |
| `minecraft:mushroom_island` | 蘑菇岛 |
| `minecraft:swampland` | 沼泽 |
| `minecraft:mesa` | 恶地 |
| `minecraft:deep_dark` | 深暗之域 |
| `minecraft:warped_forest` | 诡异森林（下界） |
| `minecraft:crimson_forest` | 绯红森林（下界） |
| `minecraft:the_end` | 末地 |

### 示例

**定位最近的村庄：**
<>`/locate structure village`

在主世界查找最近的村庄，聊天栏返回其近似水平坐标和距离，y 坐标显示为 `~`。

**定位最近的远古城市：**
<>`/locate structure ancient_city`

查找最近的远古城市。远古城市生成在深暗之域的地下，返回坐标后需向下挖掘才能到达。

**只在未探索区块中查找试炼密室：**
<>`/locate structure trial_chambers true`

加 `true` 后只搜索尚未生成的新区块，返回离执行者最近、且位于未探索区域内的试炼密室坐标，避免重复指向已经到访过的建筑。

**定位最近的沙漠生物群系：**
<>`/locate biome minecraft:desert`

返回最近沙漠生物群系的一个采样点三维坐标。与结构定位不同，这里会给出完整的 `x y z` 坐标。

**定位下界的诡异森林（需先进入下界）：**
<>`/locate biome minecraft:warped_forest`

在下界中查找最近的诡异森林生物群系。若在主世界执行，会因当前维度不存在该生物群系而失败。

### 基岩版注意

- 需要 OP 等级 1（需开启作弊）。
- 与 Java 版差异：基岩版没有 `/locate poi`（兴趣点）子命令；结构 ID 不带命名空间，而 Java 版使用命名空间 ID 并支持结构标签（如 `#village`）。
- 结构名与 Java 版不同：海底废墟是 `ruins` 而非 `ocean_ruin`，四类神殿（沙漠神殿、雪屋、丛林神庙、沼泽小屋）在基岩版共用一个 `temple` ID。
- `useNewChunksOnly` 是基岩版独有参数，Java 版没有对应选项。
- 版本兼容：1.19.30 起旧 `/locate <结构>` 语法弃用，改用子命令；1.19.30.21 起结构名改用下划线写法（如 `ancient_city`、`pillager_outpost`，旧版无下划线）；1.21.100 起 `/locate biome` 必须写命名空间。
- 命令只搜索当前维度，跨维度的结构（如下界要塞、末地城）需先传送到对应维度再执行。
- 返回的坐标是结构起点的近似水平位置，不是精确入口，实际建筑可能有一定偏移。

### 常见问题

<details>
<summary>为什么在主世界执行 /locate structure fortress 会失败？</summary>

下界要塞只在下界生成，而 `/locate` 只搜索执行者当前所在的维度。你需要先传送到下界，再执行 `/locate structure fortress`。

</details>

<details>
<summary>/locate 能找到还没探索过的区块里的结构吗？</summary>

能。结构位置由世界种子决定，命令读取的是结构放置数据而非扫描已生成的方块，因此能定位未生成区块中的结构。用 `useNewChunksOnly true` 可以限定只返回新生成区块里的结构。

</details>

<details>
<summary>返回的坐标里为什么 y 是 ~？</summary>

结构定位会忽略垂直坐标，因为结构的具体高度由它落地处的地形决定，不参与定位计算。需要你到达返回的水平坐标后，再根据地形自行寻找入口。

</details>

### 相关命令

- [`/tp`](../tp/) — 传送到 `/locate` 返回的坐标。
- [`/execute`](../execute/) — 在其它维度或其它执行位置运行 `/locate`，或对定位结果做条件判断。
