---
author: "官方•Dingding OvO"
updatedAt: "2026-10-23"
title: "/tellraw  聊天消息"
description: "向指定玩家发送完全自定义的聊天消息，可插入分数、玩家名与颜色"
tags: ["信息", "聊天栏", "服务器", "多人", "即时", "多目标"]
---

`/tellraw` 在聊天栏发一条消息，内容完全由你决定。和 `/say` 的区别在于：`/say` 只发纯文本、还会带上说话者名字；`/tellraw` 不发名字，颜色、排版、插入的分数和玩家名都能自己控制。

### 原理

`/tellraw` 的第二个参数是一段 **rawtext**（原始 JSON 文本）。游戏把这段 JSON 解析成若干"内容组件"，依次拼成一句话，再渲染到目标玩家的聊天栏里。

内容组件只有四种：

| 组件 | 写法 | 显示什么 |
|---|---|---|
| `text` | `{"text":"你好"}` | 这段字符串本身 |
| `score` | `{"score":{"name":"*","objective":"金币"}}` | 计分板上的分数 |
| `selector` | `{"selector":"@p"}` | 匹配到的实体名字 |
| `translate` | `{"translate":"commands.op.success"}` | 本地化词条 |

一个组件里只显示一种内容，按 `translate` > `text` > `score` > `selector` 的优先级取第一个存在的。想同时显示文字和分数，就写两个组件。

`score` 的 `name` 写 `*` 表示**读这条消息的人自己**，所以一条发给 `@a` 的命令，每个人看到的是自己的数。

消息只发给参数里指定的玩家。要全服可见就写 `@a`。

### 语法

`/tellraw <玩家> <rawtext>`

### 参数

- `<玩家>` — 目标选择器或玩家名。写 `@a` 是全服，写 `@s` 是自己。
- `<rawtext>` — 一段 JSON，必须以 `{"rawtext":[...]}` 的形式给出，数组里至少一个元素。

### 示例

**一条带颜色的消息**：

<CmdChat>`/tellraw @a {"rawtext":[{"text":"§e服务器将在 5 分钟后重启"}]}`

**插入玩家名**：

<CmdChat>`/tellraw @a {"rawtext":[{"text":"§b"},{"selector":"@a[tag=just_joined]"},{"text":" §r加入了服务器"}]}`

`selector` 会把匹配到的名字逐个列出来，多个之间用逗号分隔。命令方块执行时没有执行者，写 `@s` 会匹配不到，用标签筛人更稳。

**插入分数**：

<CmdChat>`/tellraw @a {"rawtext":[{"text":"你当前有 §6"},{"score":{"name":"*","objective":"金币"}},{"text":" §r枚金币"}]}`

每人看到自己的余额。

**多行**：

<CmdChat>`/tellraw @s {"rawtext":[{"text":"§l任务提示\n§r去村庄找铁匠\n奖励：3 颗钻石"}]}`

换行必须写 `\n`，JSON 里出现真实回车会让整条命令解析失败。

**只发给达标的人**：

<CmdChain>`/tellraw @a[scores={金币=1000..}] {"rawtext":[{"text":"§6你的金币已满，去找商人兑换"}]}`

### 基岩版注意

- 基岩版必须用 `{"rawtext":[...]}` 包起来。Java 版那种直接写 `{"text":"..."}` 的组件在基岩版会报错。
- 不支持 Java 的 `clickEvent` / `hoverEvent`，做不出"点击执行命令"的文本。基岩版没有可点击文本这一特性。
- 样式只有 `§` 码，没有 `"color"` 字段，也不支持十六进制色值。
- 整条命令必须一行写完；字符串里的双引号写成 `\"`。
- `score` 读的计分项不存在时那一块直接不显示，`name` 写 `*` 是例外。
- `selector` 匹配不到目标时同样不显示，所以"名字 + 固定文字"的消息可能缺一块，不会报错。
- `translate` 里的 `%` 要写成 `%%`。

### 常见问题

<details>
<summary>消息发出去了但完全是空的</summary>

检查 `rawtext` 数组是不是空的。空数组会解析失败；也有可能是里面所有组件都因为分数或选择器没匹配到而没内容——比如 `score` 的 `objective` 拼错。

</details>

<details>
<summary>能不能只发给某一个人</summary>

能，第一个参数写玩家名或 `@p`。`/tellraw` 不是广播命令，它只发给参数指定的目标。

</details>

<details>
<summary>能不能做"点击就传送"的按钮</summary>

基岩版不行。可点击文本是 Java 版特性，基岩版的替代方案是用 NPC 的 `/dialogue` 或者雪球菜单那类"视角确认"的交互。

</details>

### 相关命令

- [`/titleraw`](../titleraw/) — 同样的 rawtext 格式，显示在屏幕标题或动作栏
- [`/scoreboard`](../scoreboard/) — 建计分项，供 `score` 组件读取
