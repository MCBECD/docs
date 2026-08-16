---
author: "官方•Dingding OvO"
updatedAt: "2026-08-12"
title: "文件命名标准"
hidden: true
description: "所有 .md 文件的命名规则——命令文档、社区文档、基础文档的命名格式、禁止规则与迁移映射"
tags: ["标准"]
---

## 文件命名标准

文件命名是文档系统的基础。站点的文档引擎（`lib/docs.ts`）通过文件名生成文档 ID（`DocMeta.id`），文档 ID 用于路由、链接和缓存。如果文件命名不规范，会导致链接断裂、路由错误和搜索失败。

本文档定义所有 `.md` 文件的命名规则。任何不符合本标准的文件名都应在审核中被拒绝。

---

### 一、通用规则

1.1 文件扩展名必须为 `.md`。不允许使用 `.md`、`.markdown`、`.txt` 等其他扩展名。

1.2 文件名只允许包含以下字符：
- 小写英文字母 `a-z`
- 数字 `0-9`
- 连字符 `-`
- 点号 `.`（仅作为扩展名分隔符）

1.3 文件名不允许包含：
- 大写字母（`A-Z`）
- 空格
- 中文字符
- 下划线 `_`
- 特殊字符（`!@#$%^&*()` 等）
- 连续的连字符 `--`
- 以连字符开头或结尾

1.4 文件名应具有描述性，让人一眼就能看出文件的内容。

1.5 文件名长度建议在 3-40 个字符之间。

---

### 二、命令文档命名

命令文档存放在 `commands/` 子目录下。

#### 2.1 命名规则

文件名 = 命令名（小写，不含 `/`）+ `.md`

规则分解：
- 不含 `/` 前缀（`/give` 的文件名是 `give.md`，不是 `/give.md`）
- 全部小写
- 不含空格
- 不含参数、不含版本号、不含中文翻译

正确示例：

| 命令 | 文件名 |
|------|--------|
| `/give` | `commands/give.md` |
| `/execute` | `commands/execute.md` |
| `/scoreboard` | `commands/scoreboard.md` |
| `/setblock` | `commands/setblock.md` |
| `/playsound` | `commands/playsound.md` |
| `/tp` | `commands/tp.md` |

错误示例：

| 错误文件名 | 错误原因 |
|------------|----------|
| `commands/Give.md` | 大写字母 |
| `commands/give_command.md` | 包含下划线 |
| `commands/give 命令.md` | 包含中文和空格 |
| `commands/-give.md` | 以连字符开头 |
| `commands/give-items.md` | 添加了不必要的单词 |
| `commands/give1.md` | 添加了数字 |
| `give.md` | 不在 commands/ 子目录下 |

#### 2.2 文档 ID 生成规则

站点引擎将 `commands/give.md` 的文档 ID 生成为 `commands/give`。这个 ID 用于：

- 路由：`/docs/commands/give`
- 链接：`[文本](../../commands/give/)`
- 缓存键
- 收藏和历史记录

如果文件名改变，所有指向该文档的链接都会断裂。因此文件名一旦确定，不应轻易修改。

---

### 三、社区文档命名

社区文档存放在仓库根目录。

#### 3.1 命名规则

文件名 = 纯数字编号 + `.md`

规则分解：
- 编号从 1 开始递增
- 不补零（`1.md`，不是 `01.md`）
- 不加任何前缀（`1.md`，不是 `community-1.md`）
- 不加任何英文描述（`1.md`，不是 `1-snowball-menu.md`）
- 不跳号（`1.md`、`2.md`、`3.md`，不能跳过 4）

正确示例：

```
1.md
2.md
3.md
4.md
5.md
```

错误示例：

| 错误文件名 | 错误原因 |
|------------|----------|
| `community-1.md` | 旧格式，含前缀 |
| `community-get-diamonds.md` | 旧格式，含英文描述 |
| `01.md` | 补零 |
| `example-1.md` | 使用了单词前缀 |
| `snowball-menu.md` | 使用了英文描述 |

#### 3.2 编号分配规则

1. 查看根目录下已有的最大编号（如已有 `3.md`，下一个编号为 4）
2. 使用下一个连续编号
3. 不重用已删除文档的编号
4. 不跳号

#### 3.3 文档 ID 生成

`1.md` 的文档 ID 为 `1`。路由为 `/docs/1`。

---

### 四、基础文档命名

基础文档存放在仓库根目录。

#### 4.1 命名规则

文件名 = 语义化英文小写（单词间用连字符 `-` 分隔）+ `.md`

规则分解：
- 全部小写
- 使用有意义的英文单词或短语
- 多个单词用单个连 `-` 连接
- 名词或名词短语

正确示例：

```
about.md
getting-started.md
command-syntax.md
writing-guide.md
tag-standard.md
standards.md
frontmatter-standard.md
naming-standard.md
```

错误示例：

| 错误文件名 | 错误原因 |
|------------|----------|
| `About.md` | 大写字母 |
| `getting_started.md` | 下划线 |
| `getting started.md` | 空格 |
| `commandSyntax.md` | 驼峰命名 |
| `1-intro.md` | 以数字开头（与社区文档混淆） |

---

### 五、目录结构

```
MCBECD/docs/
├── *.md                    # 基础文档（根目录）
├── commands/                 # 命令文档（子目录）
│   └── *.md
├── [0-9]+.md               # 社区文档（根目录，纯数字命名）
├── standards/                # 标准规范文档（子目录）
│   └── *.md
├── README.md
├── CONTRIBUTING.md
└── LICENSE
```

关键结构规则：
- 命令文档**必须**在 `commands/` 子目录下
- 社区文档**必须**在根目录
- 基础文档**必须**在根目录
- 标准文档**必须**在 `standards/` 子目录下
- 不得创建其他子目录（如 `examples/`、`tutorials/` 等）

---

### 六、旧命名迁移

以下命名已废弃，所有文件必须迁移到新格式：

| 旧文件名 | 新文件名 | 迁移说明 |
|----------|----------|----------|
| `community-1.md` | `1.md` | 去掉前缀 |
| `community-2.md` | `2.md` | 去掉前缀 |
| `community-get-diamonds.md` | `4.md` | 去掉英文描述，用下一个编号 |
| `examples/xxx.md` | `N.md`（根目录） | 去掉子目录，用编号命名 |
| `{name}/index.md` | `{name}.md` | 扁平化 |

---

### 七、引擎的文件扫描逻辑

`lib/docs.ts` 中的 `scanDirectory()` 函数递归扫描 `content/docs/` 目录：

1. 跳过所有以 `.` 开头的文件（如 `.gitignore`）
2. 跳过 `README.md`、`CONTRIBUTING.md`、`LICENSE`
3. 对于目录，递归扫描
4. 对于 `.md` 文件，解析 frontmatter 生成 `DocMeta`
5. 文档 ID = 文件相对于 `content/docs/` 的路径（去掉 `.md`）

示例路径映射：

| 文件路径 | 文档 ID | 路由 |
|----------|---------|------|
| `content/docs/give.md` | `give` | `/docs/give` |
| `content/docs/commands/give.md` | `commands/give` | `/docs/commands/give` |
| `content/docs/1.md` | `1` | `/docs/1` |
| `content/docs/standards/tag-standard.md` | `standards/tag-standard` | `/docs/standards/tag-standard` |

理解这个映射关系对于正确编写交叉引用链接至关重要。