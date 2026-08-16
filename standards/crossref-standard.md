---
author: "官方•Dingding OvO"
updatedAt: "2026-08-12"
title: "交叉引用标准"
category: basics
hidden: true
pinned: true
description: "文档间链接的完整规范——链接格式、路径规则、命令引用格式、外部链接规则与常见错误"
tags: ["标准"]
---

## 交叉引用标准

交叉引用是文档之间建立关联的方式。正确的交叉引用帮助读者快速跳转到相关文档，形成文档网络。错误的交叉引用会导致 404 页面和读者困惑。

本文档定义 MCBECD 文档中所有链接类型的书写规范。

---

### 一、命令引用格式

在文档正文中引用其他命令时，必须使用以下格式：

```markdown
[`/give`](../../commands/give/)
```

分解说明：
- 反引号包裹命令文本：`` `/give` ``（显示为等宽字体的 `/give`）
- 方括号包裹整体作为链接文本
- 圆括号内为链接路径（相对路径，见下）

**关键规则**：

1. 命令文本必须包含 `/` 前缀
2. 命令文本必须是命令的实际名称（不使用中文翻译作为链接文本）
3. 链接路径末尾必须有 `/`
4. 链接路径不包含 `.md` / `.md` 后缀
5. 链接路径必须是能正确跳转到目标文档的相对路径（见第二节）

---

### 二、链接路径规则

#### 2.1 文档 URL 结构

站点使用 `output: export` + `trailingSlash: true`，文档 URL 由文档 ID 决定：

| 文件路径 | 文档 ID | 文档 URL |
|----------|---------|----------|
| `commands/give.md` | `commands/give` | `/docs/commands/give/` |
| `community/2.md` | `community/2` | `/docs/community/2/` |
| `standards/tag-standard.md` | `standards/tag-standard` | `/docs/standards/tag-standard/` |
| `command-syntax.md` | `command-syntax` | `/docs/command-syntax/` |

#### 2.2 相对路径的计算

链接使用**相对路径**：`href` 相对于**当前文档所在目录**解析。

- 同一个目录（例如 `commands/give` → `commands/execute`）：`../execute/`
- 从一个子目录到另一个子目录（例如 `community/2` → `commands/time`）：`../../commands/time/`
- 从子目录到根目录（例如 `commands/give` → `command-syntax`）：`../../command-syntax/`
- 从根目录到子目录（例如 `about` → `commands/give`）：`../commands/give/`
- 从根目录到根目录（例如 `about` → `command-syntax`）：`../command-syntax/`

**记忆方法**：`../` 表示「上一层」。从 `/docs/commands/give/` 出发，先 `../` 回到 `/docs/commands/`，再 `../` 回到 `/docs/`，然后接目标路径。

#### 2.3 常见场景对照表

| 源文档 | 目标文档 | 正确相对路径 |
|--------|----------|--------------|
| `commands/give` | `commands/execute` | `../execute/` |
| `commands/give` | `command-syntax` | `../../command-syntax/` |
| `commands/give` | `community/3` | `../community/3/` |
| `community/2` | `commands/time` | `../../commands/time/` |
| `community/2` | `standards/tag-standard` | `../standards/tag-standard/` |
| `about` | `commands/give` | `../commands/give/` |
| `about` | `command-syntax` | `../command-syntax/` |
| `standards/community-standard` | `standards/tag-standard` | `../tag-standard/` |

#### 2.4 路径末尾的斜杠

链接路径末尾**必须**带 `/`。这是站点路由（`trailingSlash: true`）的要求。

正确：`../execute/`
错误：`../execute`、`../execute.md`

---

### 三、外部链接

#### 3.1 站点引擎自动处理

`MDRenderer.tsx` 中的 `a` 组件会自动检测链接协议：

- `http://` 或 `https://` 开头 → 外部链接，在新标签页打开（`target="_blank"`），添加 `rel="noopener noreferrer"`
- 其他 → 内部链接，使用 Next.js 的 `<Link>` 组件

#### 3.2 外部链接书写格式

```markdown
[Minecraft Wiki](https://minecraft.wiki/w/Commands)
```

不需要手动添加 `target="_blank"` 或其他属性，站点引擎会自动处理。

#### 3.3 外部链接使用场景

外部链接用于：
- Minecraft Wiki 的参考页面
- 官方公告或更新日志
- 第三方工具的下载页面

不在以下场景使用外部链接：
- 引用其他 MCBECD 文档（使用内部相对路径）

---

### 四、引用频率要求

#### 4.1 命令文档的引用要求

每篇命令文档至少引用 **2 个**其他文档。常见的引用方式：

- 语法章节引用相关的命令
- 示例章节引用用到的命令
- 基岩版注意章节引用相关命令

#### 4.2 引用准确性

所有引用的命令必须在 MCBECD 文档库中实际存在。引用不存在的命令会产生 404 错误。

当前文档库中的命令列表：ability, clear, clone, difficulty, effect, enchant, execute, fill, gamemode, gamerule, give, help, kill, locate, particle, playsound, ride, scoreboard, setblock, summon, tag, time, title, tp, weather, xp

---

### 五、常见错误与修正

| 错误 | 修正 | 原因 |
|------|------|------|
| `详见 /give 命令` | `` 详见 [`/give`](../commands/give/) `` | 没有链接 |
| `` [`/give`](./give) `` | `` [`/give`](../commands/give/) `` | 路径缺少末尾 `/` 或缺少目录层级 |
| `` [`/give`](../give/) `` | `` [`/give`](../commands/give/) `` | 缺少 `commands/` 目录 |
| `` [`/data`](../commands/data/) `` | 删除此引用 | `/data` 不存在于基岩版 |
