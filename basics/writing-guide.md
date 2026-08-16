---
author: "官方•Dingding OvO"
updatedAt: "2026-08-12"
title: "写作指南"
hidden: true
description: "图标系统、自定义 MD 组件与文档写作规范"
---

## 写作指南

本文档介绍 MCBECD 站点的 Markdown/MD 写作规范，包括图标系统、自定义组件、代码高亮和文档结构。

### Frontmatter 规范

每个 `.md` 文件必须包含以下 frontmatter：

```yaml
---
author: "作者名称"
updatedAt: "YYYY-MM-DD"
title: "/command  中文名称"
description: "一句话描述"
tags: ["标签1", "标签2"]
---
```

| 字段 | 必填 | 说明 |
|------|------|------|
| `title` | ✅ | 文档标题 |
| `description` | ✅ | 简短描述 |
| `author` | ✅ | 作者名称 |
| `updatedAt` | ✅ | 更新日期，格式 `YYYY-MM-DD` |
| `tags` | 可选 | 标签数组，用于筛选 |

### 命令方块图标组件

在 MD 中可以使用 7 种命令方块图标组件，用于展示命令在命令方块中的执行方式：

| 组件 | 图标 | 用途 |
|------|------|------|
| `<CmdImpulse>` | 脉冲 | 收到红石信号时执行一次 |
| `<CmdRepeat>` | 重复 | 每个游戏刻持续执行 |
| `<CmdChain>` | 连锁 | 在指向它的命令方块执行后触发 |
| `<CmdConditionalImpulse>` | 条件脉冲 | 仅在上一个命令方块成功时执行 |
| `<CmdConditionalRepeat>` | 条件重复 | 仅在上一个成功时每个刻执行 |
| `<CmdConditionalChain>` | 条件连锁 | 仅在上一个成功时触发 |
| `<CmdChat>` | 聊天 | 通过聊天栏执行 |

用法示例：

```markdown
<CmdConditionalChain>`/give @p diamond 64`

<CmdRepeat>`/execute as @a at @s run setblock ~ ~-1 ~ gold_block`

<CmdChain>`fill ~ ~1 ~ ~ ~3 ~ air`
```

渲染效果：在命令文本前显示一个 16×16 像素的 Minecraft 命令方块图标，后跟等宽字体命令文本。

### GitHub 风格提示框

支持 GitHub 风格的提示框语法，在文档中插入不同级别的提示信息：

```markdown
> [!NOTE]
> 这是一条普通提示信息。

> [!TIP]
> 这是一个实用技巧。

> [!IMPORTANT]
> 这是重要信息，需要特别注意。

> [!WARNING]
> 这是警告信息。

> [!CAUTION]
> 这是危险操作警告，请谨慎执行。
```

也可以自定义标题：

```markdown
> [!WARNING] 基岩版独有
> 此命令在 Java 版中不可用。
```

| 类型 | 颜色 | 适用场景 |
|------|------|---------|
| `NOTE` | 蓝色 | 补充说明、额外信息 |
| `TIP` | 绿色 | 实用技巧、最佳实践 |
| `IMPORTANT` | 紫色 | 重要信息、关键要点 |
| `WARNING` | 橙色 | 注意事项、潜在问题 |
| `CAUTION` | 红色 | 危险操作、不可逆操作 |

### 代码块与语法高亮

支持标准代码围栏语法，带有 Shiki 语法高亮和一键复制功能：

````markdown
<>`/give @s diamond_sword 1 0 {"enchantments":[{"id":"sharpness","level":5}]}`
````

#### `mcfunction` 语言

站点内置了自定义的 `mcfunction` 语法高亮规则，专门用于 Minecraft 命令。它会自动识别：

- 命令名称（如 `give`、`execute`）— 函数名高亮
- 选择器（如 `@a`、`@p`、`@s`、`@e`）— 标签高亮
- 字符串（`"text"` 或 `'text'`）— 字符串高亮
- 布尔值（`true`、`false`）— 常量高亮
- 数字和坐标（`100`、`~ ~1 ~`、`^ ^ ^1`）— 数值高亮
- 参数名（如 `sharpness`、`speed`）— 参数高亮

#### 内联代码

使用单个反引号包裹内联代码：

```markdown
使用 `<玩家>` 选择器指定目标。
```

### 命令链接约定

在文档中引用其他命令时，使用反引号包裹命令文本作为链接文字：

```markdown
详见 [`/give`](../commands/give/) 命令文档。
```

这种格式在列表页和正文中都能提供一致的视觉体验。链接必须使用**相对路径**（如 `../commands/give/`），不要使用绝对路径（如 `/docs/give`）。

### 折叠内容

支持 `<details>` / `<summary>` 创建可折叠内容：

```markdown
<details>
<summary>点击展开详细信息</summary>

折叠的详细内容放在这里。

</details>
```

### 键盘快捷键

使用 `<kbd>` 标签显示键盘按键：

```markdown
按 <kbd>Ctrl</kbd> + <kbd>C</kbd> 复制代码。
```

### 表格

使用标准 GFM 表格语法：

```markdown
| 参数 | 类型 | 说明 |
|------|------|------|
| `<玩家>` | Target | 目标玩家选择器 |
| `<物品>` | Item | 物品 ID |
```

### 任务列表

支持 GFM 任务列表：

```markdown
- [x] 已完成的任务
- [ ] 待完成的任务
```

### 文档结构模板

新建命令文档时，推荐使用以下结构：

```markdown
---
author: "官方•Dingding OvO"
updatedAt: "2026-08-09"
title: "/command  中文名称"
description: "简短描述"
tags: ["标签1", "标签2"]
---

一句话描述命令的功能。

### 语法

`/command <参数1> <参数2> [可选参数]`

### 参数

- `<参数1>` — 必填参数说明
- `<参数2>` — 必填参数说明
- `[可选参数]` — 可选参数说明

### 示例

<>`/command @p value`

描述这个示例的效果。

>[!NOTE]
> - 需要 OP 等级 1
> - 与 Java 版的差异
> - 其他注意事项
```

### 脚手架工具

可以使用项目自带的脚手架脚本快速创建新文档：

```sh
node scripts/new-doc.mjs <命令名> ["标题"] [排序号]
```

示例：

```sh
node scripts/new-doc.mjs camera "摄像机控制" 32
```

### 校验工具

提交前运行校验脚本检查 frontmatter 完整性：

```sh
node scripts/validate-docs.mjs
```

校验规则：
- 必填字段：`title`、`description`、`author`、`updatedAt`
- `updatedAt` 必须符合 `YYYY-MM-DD` 格式
- 链接应使用相对路径（绝对路径会产生警告）

### 文件存放

文档存放在仓库根目录，命令文档存放在 `commands/` 子目录下

```
MCBECD/docs/
├── about.md
├── getting-started.md
├── command-syntax.md
├── commands/
│   ├── execute.md
│   ├── give.md
│   ├── tp.md
│   └── kill.md
├── 1.md
├── 2.md
├── 3.md
├── standards/
│   └── *.md
├── CONTRIBUTING.md
└── README.md
```