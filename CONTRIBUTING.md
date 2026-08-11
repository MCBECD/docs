# 贡献指南

感谢你为 MCBECD 文档做出贡献！无论你是修复错别字还是添加新命令文档，都请遵循以下流程。

## 快速开始

### 前置要求

- Git
- GitHub 账号
- 熟悉 Markdown 语法
- 了解 Minecraft 基岩版命令系统

### 环境搭建

```bash
# 1. Fork 本仓库
# 2. Clone 你的 fork
git clone https://github.com/YOUR_USERNAME/docs.git
cd docs

# 3. 创建分支
git checkout -b add-weather-command
```

## 文档写作规范

### 文件命名

| 类型 | 命名 | 示例 |
|------|------|------|
| 命令文档 | `{命令名}.mdx` | `weather.mdx` |
| 社区/教程 | `{下一个数字}.mdx` | `3.mdx` |

### Frontmatter 模板

**命令文档：**
```yaml
---
title: "/weather  设置天气"
order: 200
category: commands
description: "更改当前世界的天气状态"
author: "你的名字"
updatedAt: "2026-08-10"
---
```

**社区文档：**
```yaml
---
title: "你的教程标题"
order: 10003
category: commands
description: "简短描述"
author: "你的名字"
updatedAt: "2026-08-10"
---
```

### order 分配

| 范围 | 用途 |
|------|------|
| 1–99 | 基础文档（intro, getting-started） |
| 100–9999 | 官方命令（10 间隔，如 110, 120） |
| 10000+ | 社区内容（递增，如 10001, 10002, 10003） |

### 必须包含的章节（命令文档）

```markdown
## 语法
\`\`\`mcfunction
/command <required> [optional: type]
\`\`\`

## 参数
| 参数 | 类型 | 说明 | 可选值 |
|------|------|------|--------|
| `required` | 字符串 | 必填说明 | — |
| `optional` | 整数 | 可选说明 | 0–100 |

## 示例
\`\`\`mcfunction
/command value1 50
/command value1 100
\`\`\`

## 基岩版注意
- 需要 OP 等级 X
- 与 Java 版的差异
```

### Markdown 规范

- **内部链接必须用相对路径**：`[effect 命令](./effect)`（不带 `.mdx` 后缀）
- 代码块指定语言：` ```mcfunction `
- 表格对齐、表头完整
- 中英文之间加空格

## 提交流程

1. **本地验证** — 确保 `.mdx` 文件格式正确，frontmatter 无遗漏
2. **Commit** — 使用清晰的中文提交信息
   ```
   add: /weather 命令文档
   fix: 修正 /give 参数说明
   ```
3. **Push** — 推送到你的 fork
4. **Pull Request** — 提交到 `MCBECD/docs` 的 `main` 分支
5. **Review** — 等待维护者审核

## PR 要求

- [ ] 新文档包含完整的 frontmatter
- [ ] order 不与其他文档冲突（用 10 的间隔）
- [ ] 内部链接使用相对路径
- [ ] 至少 2 个实用示例
- [ ] 包含「基岩版注意」章节（命令文档）
- [ ] 仅修改你打算修改的内容

## 需要帮助？

- 参考已有文档：[give.mdx](./give) | [effect.mdx](./effect)
- 查看语法文档：[command-syntax.mdx](./command-syntax)
- 提交 Issue 讨论

---

感谢你的贡献！🫡
