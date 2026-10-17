# 贡献指南

感谢你为 MCBECD 文档做出贡献！无论你是修复错别字还是添加新命令文档，都请遵循以下流程。

## 快速开始

### 前置要求

- Git
- GitHub 账号
- 熟悉 Markdown 语法
- 了解 Minecraft 基岩版命令系统

### 环境搭建

```sh
# 1. Fork 本仓库
# 2. Clone 你的 fork
git clone https://github.com/YOUR_USERNAME/docs.git
cd docs

# 3. 创建分支
git checkout -b add-weather-command
```

## Frontmatter 模板

命令文档：
```yaml
---
title: "/weather  设置天气"
description: "更改当前世界的天气状态"
author: "你的名字"
updatedAt: "2026-08-10"
tags: ["领域标签", "场景标签", "属性标签"]
---
```

社区文档：
```yaml
---
title: "你的教程标题"
description: "简短描述，15-40字"
author: "你的名字"
updatedAt: "2026-08-10"
tags: ["内容类型", "技术栈"]
---
```

文件命名、章节结构、标签、链接写法统一见 [文档规范](./standards/README.md)，那一份是唯一标准。

## 提交流程

1. **本地验证** — 确保 `.md` 文件格式正确，frontmatter 无遗漏
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
- [ ] 内部链接使用相对路径
- [ ] 至少 3 个实用示例（命令文档）
- [ ] 包含「基岩版注意」章节（命令文档）
- [ ] 仅修改你打算修改的内容

## 需要帮助？

- 参考已有文档：[commands/give.md](./commands/give.md) | [commands/effect.md](./commands/effect.md)
- 查看语法文档：[basics/command-syntax.md](./basics/command-syntax.md)
- 提交 Issue 讨论

---

感谢你的贡献！🫡
