---
title: "T 显"
description: "用 title 与 titleraw 把分数、状态和公告钉在屏幕上，基岩版做 HUD 的唯一手段"
author: "官方•Dingding OvO"
updatedAt: "2026-10-23"
tags: ["信息", "计分板"]
---

T 显就是用 `title` / `titleraw` 在玩家屏幕上持续显示信息——金币数、在线时长、血量、排行榜、公告。基岩版没有 Boss 栏自动更新、也没有办法给屏幕叠自定义界面，想让玩家一直看到某个数值，只能靠这个。

它之所以能成体系，是因为 `rawtext` 里的 `score` 组件可以**直接读计分板分数**，不需要先用命令把数字拼成字符串。一条命令，屏幕上的数字就跟着分数变。

### 原理

**三个显示位**：

| 位置 | 写法 | 外观 |
|---|---|---|
| `title` | `/title @a title 文本` | 屏幕中央的大字 |
| `subtitle` | `/title @a subtitle 文本` | 大字下方的小字，随下一个 title 一起出现 |
| `actionbar` | `/title @a actionbar 文本` | 快捷栏上方的一行，不挡视野 |

`times` 控制标题的淡入、停留、淡出（单位游戏刻），默认 10 / 70 / 20，也就是 0.5 秒淡入、3.5 秒停留、1 秒淡出。这三个值存在客户端，跨存档保留，重开游戏才复位。

**纯文本与 rawtext 是两条路**：`/title` 只吃纯文本，想加颜色、插分数、插玩家名，必须走 `/titleraw`：

<CmdChat>`/titleraw @a actionbar {"rawtext":[{"text":"金币："},{"score":{"name":"*","objective":"金币"}}]}`

`rawtext` 是个数组，里面的元素按顺序拼成一句话。每个元素只能是四种内容之一：

| 类型 | 字段 | 说明 |
|---|---|---|
| 纯文本 | `text` | 普通字符串，换行用 `\n` |
| 分数 | `score` | `{"name":…,"objective":…}`，直接读计分板 |
| 实体名称 | `selector` | 显示目标的名字，没名字就显示实体类型 |
| 翻译文本 | `translate` | 本地化键名，用 `with` 填占位符 |

一个元素里同时写了多个字段时，按 `translate` > `text` > `score` > `selector` 的优先级只取一个。

**`*` 表示"读这句话的人"**。`{"score":{"name":"*","objective":"金币"}}` 不是读某个固定玩家，而是每个收到这条消息的玩家各读各的。所以一条命令发给 `@a`，每人看到的是自己的数字——这是做 HUD 的关键。

### 一个状态栏

下面的 `objective` 是计分项的名字，要先建出来才有内容可读：`/scoreboard objectives add 金币 dummy`。计分项不存在时，`score` 组件什么都不显示。

<CmdRepeat>`/titleraw @a actionbar {"rawtext":[{"text":"§e金币 §f"},{"score":{"name":"*","objective":"金币"}},{"text":"  §a在线 §f"},{"score":{"name":"*","objective":"在线"}},{"text":"  §b等级 §f"},{"score":{"name":"*","objective":"等级"}}]}`

挂在重复命令方块上，保持开启、延迟 2。每人看到自己的三项数据，每秒刷新 10 次。颜色用 `§` 码，基岩版 rawtext 不支持 Java 那种 `"color"` 字段和十六进制色值。

### 多行面板

`\n` 换行，多个元素连起来就是一块面板：

<CmdRepeat>`/titleraw @a actionbar {"rawtext":[{"text":"§l§b『 服务器状态 』\n§r在线 §f"},{"score":{"name":"*","objective":"在线"}},{"text":" §7| §r金币 §f"},{"score":{"name":"*","objective":"金币"}},{"text":"\n§7输入 /菜单 打开功能"}]}`

actionbar 支持换行，行数越多越往上堆。三到四行没问题，再多就会挡视野。

### 示例

**开场大字与倒计时**：

<CmdChat>`/titleraw @a times 10 40 10`

<CmdChat>`/titleraw @a title {"rawtext":[{"text":"§l§6第 1 回合"}]}`

<CmdChat>`/titleraw @a subtitle {"rawtext":[{"text":"§7准备战斗"}]}`

先设时间，再发标题，副标题会在同一屏里跟出来。倒计时可以用延迟 20 的重复方块每刻改一次 title 文本，配合 `score` 组件读剩余秒数。

**全服公告带玩家名**：

<CmdChat>`/titleraw @a actionbar {"rawtext":[{"text":"§e"},{"selector":"@a[tag=just_finished]"},{"text":" §r刚刚完成了挑战"}]}`

`selector` 会把它匹配到的实体名字逐个显示出来，能匹配多个，用逗号分隔；没自定义名字就显示实体类型。这里用标签筛人，因为命令方块执行时没有执行者，写 `@s` 会匹配不到任何目标。

**只给达标的人看**：

<CmdChain>`/titleraw @a[scores={金币=1000..}] actionbar {"rawtext":[{"text":"§6金币已满，去找商人兑换"}]}`

T 显不是只能做常驻 HUD，条件命令一样能筛人。

### 刷新与开销

`actionbar` 没有"停留时长"，显示一次就淡掉。想让它常驻，必须反复发——这是 T 显唯一的花费。

- 每刻发（延迟 0）：最顺滑，但 20 次/秒 × 在线人数，玩家多时开销明显。
- 延迟 2 到 5：肉眼几乎无感，开销降到 1/2 到 1/4，**推荐**。
- 数值变化才发：用分数比较做条件，只在变的时候刷新，最省，但写起来麻烦。

`title` 不要循环发。它是大字，每刻重发会闪，而且 `times` 的淡入淡出会一直被重置。

### 基岩版注意

- `/title` 只吃纯文本，任何带 JSON 的内容都要用 `/titleraw`。两个命令的 `title` / `subtitle` / `actionbar` / `times` / `clear` / `reset` 子命令完全对应。
- `score` 组件读的计分项不存在时**不显示内容**；`name` 写 `*` 是例外，即使计分项不存在也会显示。
- `selector` 匹配不到目标时同样不显示，所以"玩家名 + 固定文字"的公告在没匹配到时会缺一块，不会报错。
- 标题过长不会自动换行，会直接溢出画面。多行内容用 `\n` 自己控制。
- 换行只能写 `\n`，JSON 里真实的回车会让命令解析失败。整条命令必须在一行内。
- `score` 和 `selector` 两种组件只在流动文本里生效——聊天和屏幕标题可以，告示牌、成书里不行。
- 分数是整数，没有小数位。要显示"1.5 万"这种格式化文本，只能靠命令自己算好写进另一个计分项。
- `times` 设置是客户端本地的，换存档也保留，重开客户端才复位。做地图时不要依赖玩家那边的当前值是默认值，开场先设一次。
