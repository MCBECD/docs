---
author: "官方•Dingding OvO"
updatedAt: "2026-08-12"
title: "Frontmatter 标准规范"
category: basics
hidden: true
pinned: true
description: "文档元数据字段的完整规范——每个字段的类型、格式、校验规则、示例、错误案例与迁移指南"
tags: ["标准", "frontmatter"]
---

## Frontmatter 标准规范

Frontmatter 是 MD 文件顶部的 YAML 元数据块，被 `---` 包裹。MCBECD 站点通过 `gray-matter` 库解析 frontmatter，将其转化为 `DocMeta` 对象用于前端渲染。frontmatter 中的每一个字段都直接影响文档在站点上的显示行为、搜索索引、分类归组和排序逻辑。本文档以法律条文的严谨性，逐一定义每个字段的规范。

---

### 一、总则

1.1 每篇 `.md` 文件必须以 frontmatter 开头，不得省略。没有 frontmatter 的文件将被文档引擎忽略，不会出现在站点上。

1.2 frontmatter 必须用三个减号 `---` 作为起始和结束标记。起始 `---` 必须是文件的第一行，前面不得有空行、空格或其他字符。结束 `---` 后必须紧跟一个空行，然后才是正文内容。

1.3 frontmatter 内部使用 YAML 语法。YAML 的缩进规则必须严格遵守：使用空格缩进（不使用 Tab），同级元素对齐，嵌套关系通过缩进表达。

1.4 字段名称区分大小写。`title` 和 `Title` 是不同的字段。MCBECD 只识别小写的字段名，大写或混合大小写的字段名将被忽略。

1.5 字符串值可以使用单引号或双引号包裹，也可以不包裹。但包含特殊字符（冒号、井号、花括号、方括号、感叹号等）的字符串必须用引号包裹。

1.6 不允许使用 YAML 锚点（`&`）和别名（`*`）。

1.7 不允许使用 YAML 多行字符串语法（`|`、`>`）。所有值必须写在同一行。

1.8 不允许出现未在本文档中定义的字段。如果贡献者添加了未定义字段，该字段将被忽略，但在审核时应提醒贡献者移除。

---

### 二、字段定义

#### 2.1 title（标题）

**类型**：string（必填）

**用途**：文档在站点上的显示标题。出现在文档列表页、文档详情页标题栏、浏览器标签页标题、搜索结果中。

**命令文档格式**：

命令文档的 title 必须遵循以下精确格式：

```
/command  中文名称
```

分解说明：

- 第一个字符必须是正斜杠 `/`
- 紧跟命令名称（全小写英文字母，不含空格）
- 命令名与中文名称之间**必须**是恰好**两个**半角空格
- 中文名称为命令的常用中文叫法，使用简体中文
- 不使用 Markdown 格式（不加粗、不斜体、不代码包裹）

正确示例：

```yaml
title: "/give  给予物品"
title: "/execute  执行命令"
title: "/scoreboard  计分板"
title: "/particle  粒子效果"
title: "/weather  设置天气"
```

错误示例及原因：

```yaml
title: "give 给予物品"          # 缺少 / 前缀
title: "/give 给予物品"          # 只有一个空格，必须两个
title: "/give   给予物品"         # 三个空格，必须恰好两个
title: "/Give  给予物品"          # 命令名大写，必须全小写
title: "/give  Give Item"        # 中文名称使用了英文
title: "**/give**  给予物品"     # 不要 Markdown 格式
title: "/give_command  给予物品"  # 命令名含下划线
title: "give 命令"               # 完全偏离格式
```

**社区文档格式**：

社区文档的 title 直接使用功能的中文名称，不加前缀。

规则：
- 简洁明确，一般 2-8 个汉字
- 不加任何前缀或后缀
- 不使用 Markdown 格式

正确示例：

```yaml
title: "雪球菜单"
title: "在线时间"
title: "雪球填平"
title: "获取全套钻石装备"
```

错误示例：

```yaml
title: "【雪球菜单】"        # 不要装饰符号
title: "1. 雪球菜单"          # 不要序号
title: "Snowball Menu"        # 不要英文
title: "雪球菜单教程"          # 不要加"教程"后缀
```

**基础文档格式**：

基础文档无固定格式，但应简洁明确。

正确示例：

```yaml
title: "MCBECD 文档标准总纲"
title: "标签标准"
title: "Frontmatter 标准规范"
```

#### 2.2 category（分类）

**类型**：string（必填）

**用途**：决定文档在站点上的分类归组。站点前端根据此字段进行分类筛选。

**合法值**：

此字段只接受以下三个值，不接受其他任何值：

| 值 | 适用范围 | 说明 |
|----|----------|------|
| `basics` | 基础文档 | 项目介绍、快速开始、语法基础、各类标准规范 |
| `commands` | 命令文档 | 所有 Minecraft 基岩版命令的详细参考文档 |
| `community` | 社区文档 | 社区贡献的教程、工具、玩法等 |

**严禁使用的值**：

以下值已在历史版本中使用，现已全部废弃，任何情况下不得使用：

```yaml
category: "commands/Player"    # 废弃
category: "commands/World"     # 废弃
category: "commands/Building"  # 废弃
category: "commands/Entity"    # 废弃
category: "commands/UI"        # 废弃
category: "commands/Advanced"  # 废弃
category: "examples"           # 废弃，改用 community
category: "intro"              # 废弃，改用 basics
category: "basics/0"           # 废弃，统一为 basics
category: "basics/1"           # 废弃，统一为 basics
category: "basics/2"           # 废弃，统一为 basics
category: "basics/3"           # 废弃，统一为 basics
```

**迁移规则**：

如果文档使用了上述废弃值，必须修改为对应的新值。具体映射：

- `commands/X`（X 为任意子分类）→ `commands`
- `examples` → `community`
- `intro` → `basics`
- `basics/N` → `basics`

#### 2.3 description（描述）

**类型**：string（必填）

**用途**：文档的一句话描述。显示在文档列表页的卡片上、文档详情页的 SEO meta 标签中、搜索结果的摘要中。

**长度限制**：

- 最短：15 个汉字（约 30 个英文字符）
- 最长：40 个汉字（约 80 个英文字符）
- 推荐长度：20-30 个汉字

**内容要求**：

- 必须是一个有意义的完整句子或短语
- 描述命令的核心功能或文档的核心内容
- 不使用「本文档介绍……」「本教程讲解……」之类的元描述
- 不使用 Markdown 格式
- 不使用感叹号或问号

正确示例：

```yaml
description: "给予玩家指定物品，支持数量、数据值与组件"
description: "丢雪球打开菜单，支持传送主城、去世、切换模式、设重生点、发起/接受传送"
description: "基岩版命令的参数格式、目标选择器与坐标系统"
description: "管理计分板目标、玩家分数和显示设置"
description: "以其他实体的身份、位置或维度执行命令，支持条件判断"
```

错误示例：

```yaml
description: "介绍give命令"                    # 太短，信息量为零
description: "give"                            # 只有一个词
description: "这是一个非常强大的命令，可以给玩家物品，也可以给物品附加附魔，还可以锁定物品在背包中不会被丢失"  # 超过 40 字
description: "**给予物品**"                     # 不要 Markdown
description: "give命令怎么用？"                  # 不要问号
description: "本文档详细介绍了 /give 命令的用法"  # 元描述，浪费空间
```

**搜索引擎优化（SEO）要求**：

- description 会被写入 HTML 的 `<meta name="description">` 标签
- 搜索引擎通常只显示 description 的前 160 个字符
- 因此中文 description 不应超过 40 个汉字
- description 中应包含用户可能搜索的关键词

#### 2.4 author（作者）

**类型**：string（必填）

**用途**：文档的作者署名，显示在文档详情页。

**格式要求**：

- 使用你的 MCBECD 社区名称或 GitHub 用户名
- 字符串格式，不加引号外的括号
- 长度建议 2-20 个字符
- 不使用 HTML 标签或特殊字符

正确示例：

```yaml
author: "官方•Dingding OvO"
author: "Steve"
author: "Alex"
author: "Alex, Steve"
```

**多人协作**：

当文档由多人共同编写时，使用逗号加空格分隔：

```yaml
author: "Alex, Steve, Notch"
```

#### 2.5 updatedAt（更新日期）

**类型**：string（必填）

**用途**：记录文档的最后更新日期。显示在文档详情页的元信息区域。

**格式**：必须严格遵循 ISO 8601 日期格式 `YYYY-MM-DD`。

正确示例：

```yaml
updatedAt: "2026-08-12"
updatedAt: "2026-01-01"
updatedAt: "2025-12-31"
```

错误示例：

```yaml
updatedAt: "2026/08/12"     # 使用了斜杠而非连字符
updatedAt: "2026-8-12"      # 月份没有补零
updatedAt: "08-12-2026"     # 顺序错误
updatedAt: "2026年8月12日"  # 使用中文格式
updatedAt: "2026-08"        # 缺少日期
updatedAt: "August 12, 2026" # 使用英文格式
```

**更新规则**：

- 每次修改文档的正文内容时，必须将 updatedAt 更新为修改当天的日期
- 仅修改 frontmatter（如添加标签）不算内容修改，但建议也更新日期
- 修复错别字、修正事实性错误时必须更新日期
- 不得使用未来日期
- 不得使用 2020 年之前的日期（MCBECD 项目创建于 2026 年）

#### 2.6 tags（标签）

**类型**：string[]（命令文档和社区文档必填，基础文档可选）

**用途**：文档的检索标签。站点前端根据标签进行筛选、搜索和推荐。

**完整规范见**[标签标准](../tag-standard/)。

此处仅列出书写语法规则：

```yaml
tags: ["标签A", "标签B", "标签C"]
```

- 使用方括号 `[]` 包裹数组
- 每个标签用双引号 `""` 包裹
- 标签之间用逗号 `,` 分隔，逗号后加一个空格
- 标签名为简体中文，不含空格
- 命令文档 3-7 个标签
- 社区文档 3-6 个标签
- 基础文档 0-2 个标签

#### 2.7 pinned（置顶）

**类型**：boolean（可选）

**用途**：将基础文档固定在文档列表顶部。

**规则**：

- 只有 `category: basics` 的文档可以使用此字段
- 值为 `true` 或 `false`
- 命令文档（`category: commands`）不得使用
- 社区文档（`category: community`）不得使用
- 建议只将最重要的 3-4 篇基础文档置顶

正确示例：

```yaml
pinned: true
pinned: false
```

错误示例：

```yaml
pinned: "true"        # 字符串，应为布尔值
pinned: 1              # 数字，应为布尔值
pinned: yes            # YAML 布尔别名，不要使用
```

---

### 三、完整模板

#### 3.1 命令文档模板

```yaml
---
author: "你的名字"
updatedAt: "2026-08-12"
title: "/command  中文名称"
category: commands
description: "一句话描述命令功能，15-40字"
tags: ["领域标签", "场景标签", "属性标签"]
---

一段话描述命令的功能。

### 语法

`/command <必填参数> [可选参数]`

### 参数

- `<必填参数>` — 参数说明
- `[可选参数]` — 参数说明

### 示例

**简单示例标题：**
```mcfunction
/command @p value
```

效果说明。

>[!NOTE]
> - 需要 OP 等级 X
> - 与 Java 版的差异
```

#### 3.2 社区文档模板

```yaml
---
author: "你的名字"
updatedAt: "2026-08-12"
title: "功能名称"
category: community
description: "一句话描述功能，15-40字"
tags: ["内容类型", "技术栈"]
---

功能描述。

## 前置指令

<CmdChat>`/命令1`
<CmdChat>`/命令2`

## 步骤

说明。

<CmdRepeat>`/命令`

## 常见问题

<details>
<summary>问题标题</summary>

解答。

</details>
```

#### 3.3 基础文档模板

```yaml
---
author: "你的名字"
updatedAt: "2026-08-12"
title: "文档标题"
category: basics
hidden: true
description: "一句话描述，15-40字"
tags: ["标准"]
pinned: true
---

正文内容。
```

---

### 四、校验规则

MCBECD 文档引擎在解析 frontmatter 时执行以下校验。校验失败的文档不会出现在站点上，但也不会导致构建失败（仅输出警告日志）。

| 校验项 | 规则 | 失败行为 |
|--------|------|----------|
| title 存在性 | 字符串非空 | 警告，使用文件名作为回退标题 |
| category 存在性 | 字符串为 basics/commands/community | 警告，文档不可见 |
| description 存在性 | 字符串非空 | 警告，使用空字符串 |
| updatedAt 格式 | 匹配 /^\d{4}-\d{2}-\d{2}$/ | 警告 |
| tags 类型 | 为数组（可不存在） | 忽略非数组值 |
| tags 元素类型 | 每个元素为非空字符串 | 忽略空字符串元素 |

---

### 五、常见错误与修正

#### 5.1 title 格式错误

```yaml
# 错误：缺少 /
title: "give  给予物品"

# 正确：
title: "/give  给予物品"
```

```yaml
# 错误：空格数量不对
title: "/give 给予物品"
title: "/give   给予物品"

# 正确：恰好两个空格
title: "/give  给予物品"
```

#### 5.2 category 使用废弃值

```yaml
# 错误：
category: "commands/Player"

# 正确：
category: commands
```

```yaml
# 错误：
category: "examples"

# 正确：
category: community
```

#### 5.3 description 太短或太长

```yaml
# 错误：太短
description: "give命令"

# 正确：
description: "给予玩家指定物品，支持数量、数据值与组件"
```

```yaml
# 错误：太长
description: "这个命令非常强大，可以给玩家各种物品，包括钻石剑、金苹果、附魔书等，还可以设置物品的数量、附魔等级和自定义名称"

# 正确：
description: "给予玩家指定物品，支持数量、数据值与组件"
```

#### 5.4 updatedAt 格式错误

```yaml
# 错误：
updatedAt: "2026/08/12"
updatedAt: "8月12日"

# 正确：
updatedAt: "2026-08-12"
```

#### 5.5 tags 格式错误

```yaml
# 错误：不用引号
tags: [标签A, 标签B]

# 正确：
tags: ["标签A", "标签B"]
```

```yaml
# 错误：使用英文标签
tags: ["player", "command_block"]

# 正确：
tags: ["玩家", "命令方块"]
```

---

### 六、与站点代码的对应关系

frontmatter 字段在站点代码中的映射：

| 字段 | DocMeta 属性 | 代码位置 | 用途 |
|------|-------------|----------|------|
| title | `meta.title` | `lib/docs.ts` → `buildMeta()` | 文档列表/详情页标题 |
| category | `meta.category` | `lib/docs.ts` → `buildMeta()` | 分类筛选 |
| description | `meta.description` | `lib/docs.ts` → `buildMeta()` | SEO meta / 搜索摘要 |
| author | `meta.author` | `lib/docs.ts` → `buildMeta()` | 详情页署名 |
| updatedAt | `meta.updatedAt` | `lib/docs.ts` → `buildMeta()` | 详情页更新时间 |
| tags | `meta.tags` | `lib/docs.ts` → `asStringArray()` | 标签筛选/搜索 |
| pinned | 未直接映射 | — | 基础文档置顶排序 |

`DocMeta` 接口定义（`lib/docs.ts`）：

```typescript
export interface DocMeta {
  id: string;
  title: string;
  description?: string;
  category?: string;
  tags?: string[];
  author?: string;
  updatedAt?: string;
}
```

解析过程（`lib/docs.ts` → `parseMdMeta`）：

1. 读取 `.md` 文件全文
2. 使用 `gray-matter` 库的 `matter()` 函数提取 frontmatter
3. `matter()` 返回 `data`（frontmatter 对象）和 `content`（正文）
4. `buildMeta()` 函数从 `data` 中提取各字段，通过 `asString()` 和 `asStringArray()` 进行类型安全转换
5. `asString()` 确保值是字符串且非空，否则返回 `undefined`
6. `asStringArray()` 确保值是数组且元素为非空字符串，否则返回 `undefined`
