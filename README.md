# ST-WBM-UI — 世界书管理器前端扩展

> SillyTavern 前端扩展，提供双面板世界书编辑器。

## 安装

将本仓库克隆到 SillyTavern 的扩展目录：

```
cd SillyTavern/data/default-user/extensions/
git clone https://github.com/AliceSao/ST-WBM-UI.git
```

需配合后端插件 [ST-WBM-Server](https://github.com/AliceSao/ST-WBM-Server) 使用。

## 功能

- 在 ST 设置面板注入管理器入口（后端连接状态检测）
- 点击按钮打开 Vue 3 双面板世界书编辑器（iframe 模态弹窗）
- 支持批量操作、搜索、跨世界书复制、5种主题切换

## 命令行操作

斜杠命令已在 v1.1.0 中移除。如需命令行操作，请使用 [Python CLI](https://github.com/AliceSao/WorldBookManager)。

## 版本

- **v1.1.0** — 移除斜杠命令和 JS-Slash-Runner 依赖；修复小屏幕弹窗上漂
- **v1.0.0** — 初始版本

## 源码

本仓库仅包含运行时文件。完整源码（Vue 3 SPA）维护于主仓库：
[AliceSao/WorldBookManager](https://github.com/AliceSao/WorldBookManager)
