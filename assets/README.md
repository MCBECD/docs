# 文档图片

文档里引用的图片放这里，目录结构与文档分类对齐：

```
assets/
├── basics/<文档名>/          # 对应 basics/<文档名>.md
├── commands/<命令名>/        # 对应 commands/<命令名>.md
└── community/<slug>/         # 对应 community/<slug>.md
```

## 命名

`<两位序号>-<英文短名>.png`，序号与正文图号一致：

```
assets/community/snowball-menu/01-menu-effect.png
assets/community/snowball-menu/02-architecture.png
```

只用小写英文与连字符。中文文件名在部分 CDN、编辑器、Windows 解压路径下会出问题。

## 引用

md 里写站点绝对路径，不带 `assets/` 前缀：

```markdown
![图 4：第一组链的摆放](/doc-assets/community/snowball-menu/04-group1-wiring.png)

*图 4 · 指令 1 在箭头起点，2–9 沿箭头依次串联。*
```

构建时 `site/scripts/copy-assets.mjs` 会把本目录复制到 `site/public/doc-assets/`，所以 `/doc-assets/xxx` 与 `assets/xxx` 是一一对应的。

## 校验

在 site 仓库跑 `npm run validate:docs`，会列出所有「引用了但文件还不存在」的图片路径。图没配完之前这条是警告，不影响构建。

## 要求

| 项 | 值 |
|----|-----|
| 宽度 | 1280–1600px |
| 体积 | 单张 ≤ 300KB |
| 格式 | UI/结构图用 PNG，场景截图可用 WebP |
| 内容 | 只放与操作直接相关的截图 |
