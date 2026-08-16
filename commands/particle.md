---
author: "官方•Dingding OvO"
updatedAt: "2026-08-12"
title: "/particle  粒子效果"
description: "在指定位置生成粒子效果，纯视觉无交互，常用于命令方块动画与地图特效"
tags: ["粒子", "命令方块", "地图制作", "自动化", "OP1", "批量"]
---

在指定位置生成粒子效果，用于装饰、动画和状态提示。基岩版的粒子 ID 与 Java 版完全不通用，且语法远简于 Java 版——没有计数、速度和扩散参数。

### 原理

`/particle` 生成的既不是方块也不是实体，而是一个由客户端渲染的「粒子发射器」。基岩版把每种粒子外观定义成资源包 `particles` 目录下的 JSON 文件，每个定义都对应一个形如 `minecraft:basic_flame_particle` 的命名空间标识符。命令执行时，游戏引擎根据你传入的标识符在已加载的粒子定义中查找对应效果，找不到就会报错或在内容日志中输出警告。这就是粒子 ID 必须写全 `minecraft:` 前缀、且必须使用基岩版实际存在的名称的原因。

命令本身在服务端执行，但粒子是纯客户端效果。执行 `/particle` 后，加载了该区域的客户端会在对应位置实例化一个粒子发射器并开始渲染。因此粒子不会进入实体列表、没有碰撞箱、不参与任何游戏逻辑，也无法用选择器选中。它只存在于渲染层，玩家走出渲染距离或粒子生命周期结束后就会消失，不会写入存档。

正因为不改动世界状态和实体状态，`/particle` 是最安全的命令之一，效果可逆、可以任意重复执行，常用于命令方块动画、地图特效和状态提示。它的开销集中在客户端渲染：同一时刻屏幕上活跃的粒子发射器越多，对低端设备的帧率影响越明显。所以大量粒子应通过重复命令方块少量、分批地生成，而不是单次堆积数百个。

边界情况：省略位置参数时粒子默认生成在执行者所在位置，可用 `~` 相对坐标和 `^` 局部坐标定位。部分粒子依赖实体上下文或特定环境，例如有的只在水中可见、有的需要依附某个实体作为发射源；这类粒子通过命令触发时可能正常生成但显示异常，或在内容日志中输出错误。因此选用粒子时应以目标版本实测为准。

### 语法

`/particle <effect: string> [position: x y z]`

基岩版没有 Java 版的 `<delta>`、`<speed>`、`<count>` 等参数，每次只生成粒子效果定义中默认的数量与运动方式。

### 参数

- `<effect: string>` — 字符串，要生成的粒子效果的命名空间标识符（如 `minecraft:heart_particle`）。必须写全 `minecraft:` 前缀。基岩版只识别实际存在于资源包粒子定义中的名称，填入不存在的 ID 会导致命令报错或输出内容日志警告。
- `[position: x y z]` — 三维坐标向量，指定粒子生成的位置。支持绝对坐标（`100 64 100`）、`~` 相对坐标（`~ ~2 ~`）和 `^` 局部坐标。该参数可省略，省略时默认生成在执行者所在位置。

### 常用粒子 ID

基岩版粒子 ID 与 Java 版不同，请使用下表所列的基岩版名称。下表来自原版粒子定义，均带 `minecraft:` 前缀。

| 粒子 ID | 说明 |
|---------|------|
| `minecraft:basic_flame_particle` | 基础火焰粒子 |
| `minecraft:blue_flame_particle` | 蓝色火焰（灵魂火）粒子 |
| `minecraft:basic_smoke_particle` | 基础烟雾粒子 |
| `minecraft:campfire_smoke_particle` | 营火烟雾粒子 |
| `minecraft:heart_particle` | 飘浮爱心粒子 |
| `minecraft:enchanting_table_particle` | 附魔台符文粒子 |
| `minecraft:villager_happy` | 开心的村民粒子（绿色星光） |
| `minecraft:villager_angry` | 愤怒的村民粒子（灰色十字） |
| `minecraft:basic_crit_particle` | 暴击粒子 |
| `minecraft:portal_directional` | 传送门粒子 |
| `minecraft:endrod` | 末地烛粒子 |
| `minecraft:totem_particle` | 不死图腾粒子 |
| `minecraft:lava_particle` | 熔岩粒子 |
| `minecraft:lava_drip_particle` | 熔岩滴落粒子 |
| `minecraft:water_splash_particle` | 水花飞溅粒子 |
| `minecraft:water_drip_particle` | 水滴粒子 |
| `minecraft:snowflake_particle` | 雪花粒子 |
| `minecraft:underwater_torch_particle` | 水下火把粒子 |
| `minecraft:note_particle` | 音符粒子 |
| `minecraft:redstone_wire_dust_particle` | 红石粉尘粒子 |
| `minecraft:dragon_breath_trail` | 末影龙吐息尾迹粒子 |
| `minecraft:falling_dust_sand_particle` | 沙子下落粉尘粒子 |
| `minecraft:glow_particle` | 发光粒子 |

### 示例

**在自己位置生成一颗爱心粒子：**
<>`/particle minecraft:heart_particle ~ ~ ~`

在执行者脚下生成一颗飘浮爱心，短暂上升后消失。由于位置参数可省略，直接输入 `/particle minecraft:heart_particle` 效果相同。

**在固定坐标生成火焰粒子：**
<>`/particle minecraft:basic_flame_particle 100 64 100`

在世界坐标 (100, 64, 100) 处生成一个基础火焰粒子，适合在建筑或场景中标记固定位置。

**在头顶上方生成开心的村民粒子：**
<>`/particle minecraft:villager_happy ~ ~2 ~`

在执行者头顶上方 2 格处生成绿色星光粒子，可用于表示玩家升级或完成任务等正面反馈。

**在所有玩家头顶生成附魔符文粒子：**
<>`/execute @a ~ ~ ~ particle minecraft:enchanting_table_particle ~ ~2 ~`

借助 [`/execute`](../execute/) 改变执行者，在每个玩家头顶上方 2 格处生成附魔符文粒子，一次对全体在线玩家生效。

**配合重复命令方块持续生成雪花：**
<>`/particle minecraft:snowflake_particle ~ ~5 ~`

把这条命令放进重复命令方块，每个游戏刻都会在执行者上方 5 格处生成一片雪花，持续运行即可营造下雪氛围。

### 基岩版注意

- 需要 OP 等级 1，且世界需开启「允许作弊」才能执行（基岩版对应「游戏导演」权限层级）
- 粒子 ID 必须写完整的 `minecraft:` 命名空间前缀，否则无法解析
- 基岩版语法远简于 Java 版：没有 `<delta>`、`<speed>`、`<count>` 和观看者参数，每次只生成粒子效果定义的默认数量
- 基岩版粒子 ID 与 Java 版完全不通用，必须使用基岩版实际存在的名称（如 `minecraft:heart_particle`）
- 粒子为纯视觉效果，不与实体交互、不影响游戏逻辑，也不会被写入存档
- 大量粒子会加重客户端渲染负担，建议用重复命令方块少量、分批生成
- 部分粒子依赖实体上下文或仅在特定环境（如水中）可见，选用时应以目标版本实测为准

### 常见问题

<details>
<summary>为什么我在指定位置看不到粒子？</summary>

常见原因有四种：距离太远超出渲染距离、粒子 ID 拼写错误或并非基岩版名称、粒子依赖特定环境（如仅水下可见）或实体上下文、客户端资源包与生成端不一致。建议先在脚下用 `minecraft:heart_particle` 验证命令本身可执行。

</details>

<details>
<summary>为什么输入 Java 版的粒子名会报错？</summary>

基岩版与 Java 版的粒子命名体系完全不同。例如 Java 版的 `minecraft:flame` 在基岩版中是 `minecraft:basic_flame_particle`。请查阅上方「常用粒子 ID」表格使用基岩版名称。

</details>

<details>
<summary>能不能一次生成多个粒子？</summary>

基岩版 `/particle` 没有计数参数，每次只能生成粒子效果定义中的默认数量。需要大量粒子时，可以把命令放进重复命令方块持续执行，或用 [`/execute`](../execute/) 改变位置后多次触发。

</details>

### 相关命令

- [`/execute`](../execute/) — 改变执行者与位置，在任意实体或坐标处生成粒子
- [`/playsound`](../playsound/) — 播放声音，与粒子搭配实现视听反馈
- [`/effect`](../effect/) — 状态效果，其自带粒子可通过隐藏粒子参数关闭后改用 `/particle` 自定义替代
- [`/summon`](../summon/) — 召唤真正的实体，粒子并非实体、无法被选择器选中
