---
author: "官方•Dingding OvO"
updatedAt: "2026-10-23"
title: "/titleraw  屏幕标题"
description: "在屏幕中央、副标题或动作栏显示带格式的文本，可读取分数与玩家名"
tags: ["信息", "地图制作", "服务器", "命令方块", "即时", "多目标"]
---

`/titleraw` 把一段 rawtext 显示在屏幕的三个位置之一：中央大字、副标题，或快捷栏上方的动作栏。它和 `/title` 的差别只有一处——`/title` 收纯文本，`/titleraw` 收 JSON，所以后者能读分数、插玩家名、按条件拼内容。

### 原理

三个显示位各有各的性格：

- **title**：屏幕中央的大字，一次只能有一条。
- **subtitle**：副标题，本身不会单独出现，必须跟着下一个 `title` 一起弹出。
- **actionbar**：快捷栏上方的一行，不挡视野，显示一次就淡掉。

`times` 控制标题的淡入、停留、淡出，单位是游戏刻，默认 **10 / 70 / 20**（0.5 秒淡入、3.5 秒停留、1 秒淡出）。这三个值存在客户端本地，跨存档保留，只有重启客户端才复位。

内容格式是 rawtext，四种组件：

| 组件 | 写法 | 显示什么 |
|---|---|---|
| `text` | `{"text":"你好"}` | 字符串本身，换行用 `\n` |
| `score` | `{"score":{"name":"*","objective":"金币"}}` | 计分板分数 |
| `selector` | `{"selector":"@p"}` | 实体名字 |
| `translate` | `{"translate":"…"}` | 本地化词条 |

`score` 的 `name` 写 `*` 表示"看这条显示的人自己"，所以一条 `@a` 命令能让每个人看到自己的数。

### 语法

`/titleraw <玩家> title <rawtext>`

`/titleraw <玩家> subtitle <rawtext>`

`/titleraw <玩家> actionbar <rawtext>`

`/titleraw <玩家> times <淡入> <停留> <淡出>`

`/titleraw <玩家> clear`

`/titleraw <玩家> reset`

### 参数

- `<玩家>` — 目标选择器或玩家名。
- `<rawtext>` — `{"rawtext":[...]}` 形式的 JSON，数组里至少一个元素。
- `<淡入> <停留> <淡出>` — 三个整数，单位游戏刻，只对 `title` 与 `subtitle` 有意义。`reset` 恢复成 10 / 70 / 20。

### 示例

**开场大字加副标题**：

<CmdChat>`/titleraw @a times 10 40 10`

<CmdChat>`/titleraw @a title {"rawtext":[{"text":"§l§6第 1 回合"}]}`

<CmdChat>`/titleraw @a subtitle {"rawtext":[{"text":"§7准备战斗"}]}`

先设时间，再发标题。副标题要在标题弹出的同一刻或之前发，晚了就会留到下一次标题。

**常驻状态栏**：

<CmdRepeat>`/titleraw @a actionbar {"rawtext":[{"text":"§e金币 §f"},{"score":{"name":"*","objective":"金币"}},{"text":"  §a在线 §f"},{"score":{"name":"*","objective":"在线"}}]}`

挂在延迟 2 到 5 的重复方块上。`actionbar` 没有停留时长，要它一直显示就得反复发。

**倒计时**：

<CmdRepeat>`/titleraw @a title {"rawtext":[{"text":"§e"},{"score":{"name":"*","objective":"剩余秒"}},{"text":" 秒后开始"}]}`

配合一个每秒减 1 的计分项，屏幕上就是跳秒的倒计时。`title` 不要每刻发，一秒一次足够，否则淡入淡出会被不停重置，看起来像在闪。

**多行面板**：

<CmdChat>`/titleraw @a actionbar {"rawtext":[{"text":"§l§b『 服务器状态 』\n§r在线 §f"},{"score":{"name":"*","objective":"在线"}},{"text":"\n§7输入 /菜单 打开功能"}]}`

`actionbar` 支持 `\n` 换行，行数越多越往上堆，三到四行还行，再多会挡视野。

**清掉屏幕上残留的标题**：

<CmdChat>`/titleraw @a clear`

### 基岩版注意

- `/titleraw` 是**独立命令**，不是 `/title` 的子命令。`/title` 的 `title` / `subtitle` / `actionbar` / `times` / `clear` / `reset` 六个子命令，`/titleraw` 都有对应写法。
- `/title` 收纯文本，可以写 `§` 码上色，但不能读分数、不能插玩家名。要动态内容就得用 `/titleraw`。
- 标题过长不会自动换行，会直接溢出画面。多行自己写 `\n`。
- 整条命令必须在一行内，JSON 里不能有真实回车；双引号要写成 `\"`。
- 样式只有 `§` 码，不支持 `"color"` 字段和十六进制色值。
- `score` 读的计分项不存在时不显示内容，`name` 写 `*` 是例外。
- `times` 是客户端本地值，换存档、换服务器都保留。做地图时开场先设一次，别假设玩家那边是默认值。
- `actionbar` 不受 `times` 影响，它有自己的短暂停留，要常驻只能重复发。

### 常见问题

<details>
<summary>发了 subtitle 但屏幕上什么都没有</summary>

副标题不能单独出现。它必须配一个 `title`，两者在同一刻或前后极近的位置发出，否则副标题会等下一次标题才显示，或者根本看不到。

</details>

<details>
<summary>标题每刻都在闪</summary>

把发送频率降下来。每秒发一次（重复方块延迟 20）比每刻发稳定得多，`times` 的淡入淡出不会被反复打断。

</details>

<details>
<summary>玩家看到的是别人的分数</summary>

`score` 的 `name` 写成了具体玩家名或 `@p`。改成 `*`，它表示"读这条显示的人自己"。

</details>

### 相关命令

- [`/tellraw`](../tellraw/) — 同样的 rawtext 格式，发到聊天栏
- [`/title`](../title/) — 纯文本版本的屏幕标题
- [`/scoreboard`](../scoreboard/) — 建计分项，供 `score` 组件读取
