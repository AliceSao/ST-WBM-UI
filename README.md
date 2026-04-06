# ST-WBM-UI v1.0

  > SillyTavern 世界书管理器 — 前端扩展

  ST-WBM-UI 是 [WorldBook Manager](https://github.com/AliceSao/WorldBookManager) 的 SillyTavern 前端扩展，注入双面板管理界面并注册 23 条斜杠命令。

  ---

  ## 功能

  - **内嵌管理面板**：在 SillyTavern 内部以模态窗口打开双面板世界书管理器
  - **23 条斜杠命令**：通过 STscript 管道对世界书进行批量操作
  - **5 套主题**：深海潮汐 / 星月长安 / 森林听风 / 宣纸墨韵 / 流苏绛唇
  - **移动端适配**：≤768px 全屏覆盖 + 左右面板标签切换

  ---

  ## 系统要求

  - SillyTavern（最新版）
  - [ST-WBM-Server](https://github.com/AliceSao/ST-WBM-Server) 后端插件（必须，提供 REST API）
  - JS-Slash-Runner 扩展（可选，但推荐安装以完整启用斜杠命令）

  ---

  ## 安装

  ```bash
  # ST 数据目录通常在 SillyTavern/data/default-user/extensions/third-party/
  git clone https://github.com/AliceSao/ST-WBM-UI.git \
    /path/to/ST数据目录/extensions/third-party/ST-WBM-UI
  ```

  然后在 SillyTavern → 扩展 → 启用 **世界书管理器 (WorldBook Manager)**。

  > **说明**：扩展目录名必须为 `ST-WBM-UI`，否则斜杠命令路径推断可能失败。

  ---

  ## 斜杠命令（共 23 条）

  ### 查询类（4 条）

  | 命令 | 用法 | 说明 |
  |------|------|------|
  | `/wb-list` | `/wb-list` | 列出所有世界书 |
  | `/wb-info` | `/wb-info 世界书名` | 查看世界书统计（条目数/策略分布） |
  | `/wb-search` | `/wb-search name=<名> q=<词>` | 搜索条目（标题/关键字/内容） |
  | `/wb-constants` | `/wb-constants 世界书名` | 列出所有常量（蓝灯）条目 |

  ### 世界书与条目管理（5 条）

  | 命令 | 用法 | 说明 |
  |------|------|------|
  | `/wb-new` | `/wb-new 名称` | 创建新世界书 |
  | `/wb-new-entry` | `/wb-new-entry name=<名> [title=<标>]` | 创建条目 |
  | `/wb-del-entry` | `/wb-del-entry name=<名> uid=<UID>` | 删除条目（不可撤销） |
  | `/wb-export` | `/wb-export 世界书名` | 下载世界书 JSON 文件 |
  | `/wb-copy` | `/wb-copy from=<源> to=<目标> [uids=...]` | 跨世界书复制条目 |

  ### 批量属性操作（12 条）

  所有批量命令支持可选的 `uids=0,1,2` 参数；省略则对全部条目生效。

  | 命令 | 参数 | 说明 |
  |------|------|------|
  | `/wb-set-strategy` | `name=<名> strategy=constant\|selective\|vectorized [uids=...]` | 批量设置激活策略 |
  | `/wb-set-position` | `name=<名> pos=bc\|ac\|be\|ae\|ad\|bn\|an [uids=...]` | 批量设置插入位置 |
  | `/wb-set-order` | `name=<名> order=<数值> [uids=...]` | 批量设置 Order |
  | `/wb-set-depth` | `name=<名> depth=<数值> [uids=...]` | 批量设置深度 |
  | `/wb-set-prob` | `name=<名> prob=<0-100> [uids=...]` | 批量设置触发概率 |
  | `/wb-set-name` | `name=<名> title=<新标题> [uids=...]` | 批量重命名条目 |
  | `/wb-add-keys` | `name=<名> keys=<词1,词2> [uids=...]` | 批量添加关键字（不重复） |
  | `/wb-set-keys` | `name=<名> keys=<词1,词2> [uids=...]` | 批量替换关键字 |
  | `/wb-clear-keys` | `name=<名> [uids=...]` | 批量清空关键字 |
  | `/wb-set-recursion` | `name=<名> [pi=true\|false] [po=true\|false] [uids=...]` | 批量设置递归控制 |
  | `/wb-set-effect` | `name=<名> [sticky=n] [cooldown=n] [delay=n] [uids=...]` | 批量设置效果 |
  | `/wb-enable` | `name=<名> [enabled=true\|false] [uids=...]` | 批量启用/禁用 |

  ### 工具类（2 条）

  | 命令 | 说明 |
  |------|------|
  | `/wb-ui` | 打开双面板管理界面（在 SillyTavern 内嵌） |
  | `/wb-help` | 显示完整命令列表 |

  ---

  ## 斜杠命令示例

  ```
  # 列出所有世界书
  /wb-list

  # 将"角色库"全部条目设为常量
  /wb-set-strategy name=角色库 strategy=constant

  # 搜索包含"龙"的条目，结果传入管道
  /wb-search name=世界设定 q=龙 | echo {{pipe}}

  # 将 UID 0-4 的 Order 改为 50
  /wb-set-order name=我的世界书 order=50 uids=0,1,2,3,4

  # 将"附加库"中 UID 10-20 的条目复制到"主世界书"
  /wb-copy from=附加库 to=主世界书 uids=10,11,12,13,14,15,16,17,18,19,20
  ```

  ---

  ## 命令注册机制

  扩展使用五策略渐进降级注册斜杠命令，兼容各版本 SillyTavern：

  1. **策略 0**（最优先）：`window.SillyTavern.getContext()` — 官方稳定接口
  2. **策略 1**：`window.SlashCommandParser`（现代 API + 旧版 API）
  3. **策略 2**：动态 `import` SlashCommandParser 模块
  4. **策略 3**：全局 `addSlashCommand`（ST ≤1.8 旧版）
  5. **策略 4**（兜底）：轮询等待（最多 60 秒）

  ---

  ## 主题

  | 主题 ID | 名称 | 主色 |
  |---------|------|------|
  | `ocean` | 🌊 深海·潮汐 | 青蓝 |
  | `starry` | 🌙 星月·长安 | 金黄 |
  | `forest` | 🌿 森林·听风 | 翠绿 |
  | `paper` | 📜 宣纸·墨韵 | 暖褐 |
  | `tassel` | 🎀 流苏·绛唇 | 玫红 |

  主题偏好保存在 `localStorage`（键名 `wbm_theme_v1`），刷新后自动恢复。

  ---

  ## 从源码构建 Web UI

  `index.js` 是 SillyTavern 扩展的主文件（直接可用，无需编译）。  
  `src/` 下是 Vue 3 + TypeScript 源代码，编译输出到后端的 `web/dist/`：

  ```bash
  npm install
  npm run build   # 输出到 ../ST-WBM-Server/web/dist/（或自定义路径）
  ```

  ---

  ## 目录结构

  ```
  ST-WBM-UI/
  ├── manifest.json           ← SillyTavern 扩展清单
  ├── index.js                ← 主扩展文件（1000+ 行，23 条命令实现）
  ├── style.css               ← 基础样式（SillyTavern 注入）
  ├── src/
  │   ├── App.vue             ← 根组件（导航栏/主题/Toast/双面板）
  │   ├── main.ts             ← Vue 应用入口
  │   ├── style.css           ← 全局 CSS（含 5 套主题变量）
  │   ├── components/
  │   │   ├── Panel.vue       ← 单面板（世界书选择/条目列表/编辑器）
  │   │   ├── EntryEditor.vue ← 条目内联编辑器
  │   │   └── BatchMenu.vue   ← 批量操作弹窗
  │   ├── services/
  │   │   └── api.ts          ← REST API 客户端
  │   └── utils/
  │       └── worldbook.ts    ← 世界书 JSON 解析工具
  ├── package.json
  ├── tsconfig.json
  ├── tsconfig.node.json
  └── vite.config.ts
  ```

  ---

  ## 配合使用

  - **后端插件**：[ST-WBM-Server](https://github.com/AliceSao/ST-WBM-Server)  
    提供 REST API，扩展依赖此服务进行世界书读写
  - **主仓库**：[WorldBookManager](https://github.com/AliceSao/WorldBookManager)  
    包含 Python CLI 工具与完整文档

  ---

  ## 作者

  AliceSao · MIT License
  