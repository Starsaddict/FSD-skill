# FSD-skill

通用、平台无关的功能规格说明书（FSD）编写指南与 AI Agent Skill 仓库。

本仓库沉淀了一套经企业级项目实战打磨的 FSD 编写标准：以 **User Journey** 为主线、遵循 **哪里用到才在哪里讲（Just-in-Time）** 原则、剔除开发设计与测试用例越界项，并通过组件级固定模板与分级质检清单保障交付质量。

---

## 仓库结构

```text
FSD-skill/
├── README.md
├── docs/
│   └── fsd-guide.md                       # 唯一的 FSD 标准范本与编写指南（平台无关）
└── skills/
    ├── fsd-writer/                        # 核心主 Skill：起草 / 代码仓双向审校与生成 / 质检
    │   ├── SKILL.md
    │   └── references/
    │       ├── structure-and-journey.md   # User Journey 骨架、单 Case 多 Feature 展开与 Anti-Scope
    │       ├── component-templates.md     # 11 类高频界面与业务组件写作模板（含固定章节报告）
    │       ├── placeholder-protocol.md    # 通用视觉高亮块 Placeholder 规范
    │       ├── code-sync-guide.md         # 从前端到后端扫描代码仓与 FSD vs. Code 双向审校协议
    │       └── qa-checklist.md            # P0 / P1 / P2 分级质检清单
    ├── fsd-diagram/                       # 图表与白板美学 Skill：5 类 FSD 原型 + 4+1 配色 + 可选飞书画板适配
    │   ├── SKILL.md
    │   └── references/
    │       ├── diagram-archetypes.md      # 5 类 FSD 高频图表选型思路与版面骨架
    │       ├── style-tokens.md            # 实测 4+1 语义色板、4 色连线/Pill 标签、字号与防碰撞数值
    │       ├── feishu-whiteboard.md       # 可选：飞书画板渲染自检与原生节点局部增删流程
    │       └── board-roundtrip.md         # 可选：已有飞书画板备份、几何重建与文本合并规范
    └── fsd-sketch/                        # 界面原型与插图 Skill：网页真实状态 → 可编辑原生 .sketch + PNG
        ├── SKILL.md
        └── references/
            ├── capture-and-reconstruction.md  # 浏览器采集、页面重建、展开菜单防遮挡错位、看板一致样本数据
            ├── native-sketch-api.md           # macOS sketchtool API 实测避坑、原生矢量图表与零警告核验
            └── frame-fitting-and-archive.md   # 已有 .sketch 精准裁框、归档微调与壳层锁定层保护
```

---

## 核心特性

### 1. 唯一的 FSD 编写指南（[`docs/fsd-guide.md`](docs/fsd-guide.md)）
- **User Journey 叙事流**：按用户进入并使用系统的真实路径展开，支持同一 Case 下多个 Feature 按统一结构重复展开。
- **Just-in-Time 落位**：业务参与体（如 Digital Employees）放在所属业务总览开头；Access Control & User Roles 放在功能讲解之后的 `2.2 Configuration & Governance`；校验、状态机、异常与外部回调就地内嵌。
- **纯功能边界（Anti-Scope）**：明确剔除内部 DB 表结构、代码扩展点/钩子、内部 API 契约、数据迁移、底层数仓模型与 QA Test Cases。
- **11 类组件固定模板**：统一 **Metric Cards** 命名、表单三段式（原型图 → `Field | Description & Constraints | Example / Note` → 交互与校验）、分栏/多 Tab 工作区 4 步结构（操作随区走）、以及**报告固定章节规格表**等。

### 2. 核心主 Skill（[`skills/fsd-writer/SKILL.md`](skills/fsd-writer/SKILL.md)）
- **忠实转化，不过度自动化**：只协助把你已有的产品思路写成规范 FSD，不替代你构思需求；所有关键节点人工把控节奏。
- **三大工作模式**：
  - **Mode 1 — Draft Mode（起草模式）**：基于已有产品思路轻整理工作流大纲（人工确认）→ 逐功能套用组件模板起草 → 图位插入平台无关的高亮块 Placeholder。
  - **Mode 2 — Code-Sync Mode（研发代码仓生成 / 双向审校模式）**：遵循**从前端到后端**思路（前端还原用户视角体验骨架 → 后端补充业务规则、机制与校验），支持逆向生成 FSD 初稿或对现有 FSD 做双向差异审校——规则与口径出入默认列出供研发审核是否应按 FSD 修正业务实现，不盲目迁就代码改写 FSD；内置工程到业务语言转译护栏。
  - **Mode 3 — QA Mode（质检模式）**：对照 P0 / P1 / P2 质检清单全面扫描已有 FSD，输出可落地的分级体检报告。
- **平台无关**：默认输出标准 Markdown，可自由用于 Markdown、飞书、Notion、Confluence、Word 等任意平台。

### 3. 图表与白板美学 Skill（[`skills/fsd-diagram/SKILL.md`](skills/fsd-diagram/SKILL.md)）
- **平台无关内核 + 可选飞书画板适配器**：默认产出高质量 SVG / PNG，按需支持飞书云文档画板写入与原生节点局部增删。
- **完整 4+1 语义配色与防碰撞规范**：基于实测数值沉淀暖色 / 蓝色 / 绿色 / 红色（失败与不可逆终态）/ 浅蓝灰信息容器五套色板、4 色语义连线与 Pill 边标签、零阴影方块美学与防溢出/防遮挡几何护栏。
- **5 类 FSD 高频图表选型**：端到端跨角色泳道图、管线+异常重试泳道图、无泳道分阶段状态迁移图、决策流+侧边规则栏/底部关系面板、多独立时间轴图；直接对接 `fsd-writer` 的 `[VISUAL-PLACEHOLDER | TYPE: diagram]`。

### 4. 界面原型与插图 Skill（[`skills/fsd-sketch/SKILL.md`](skills/fsd-sketch/SKILL.md)）
- **四大作业模式**：入口路径与展开菜单防遮挡错位排布（`entry-screenshot`）、应用功能页/表单/列表重建（`sketch`）、大屏看板重建与跨时间窗数学自洽脱敏数据集、以及已有 `.sketch` 画板的归档级精准裁框与壳层局部刷新。
- **直接承接 `fsd-writer` 界面占位符**：维护 `Placeholder ID → Sketch 画板 → 导出 PNG` 映射，确保图文语言 100% 一致，沉淀全套 macOS `sketchtool` 实测 API 避坑与零警告核验规范。

---

## 安装与使用

### 安装 Skill
本仓库遵循标准 Agent Skills 目录结构（`skills/<skill-name>/SKILL.md`），可直接通过支持 Agent Skills 的工具安装：

```bash
# 使用 skills CLI（如 Claude Code / Cursor / Codex / Antigravity 等）
npx skills add Starsaddict/FSD-skill
```

或将 `skills/fsd-writer/`、`skills/fsd-diagram/`、`skills/fsd-sketch/` 目录直接复制到对应工具的 skills 目录中。

### 典型调用示例
- **起草模式（Mode 1）**：`用 fsd-writer 帮我把这份产品笔记整理成英文 FSD，先出目录大纲给我确认。`
- **代码仓生成 / 双向审校（Mode 2）**：`用 fsd-writer 的 Code-Sync 模式从前端到后端扫描当前仓库的告警分诊模块，对照我现有的 FSD 输出双向差异审校清单。`
- **质检模式（Mode 3）**：`用 fsd-writer 的 QA 模式检查这份 FSD 草稿，按 P0/P1/P2 输出体检报告。`
- **图表 / 白板绘制**：`用 fsd-diagram 按这份 FSD 里的 [VISUAL-PLACEHOLDER | TYPE: diagram] 规格绘制状态迁移图。`
- **界面 Sketch 与入口标注**：`用 fsd-sketch 采集当前浏览器页面状态并生成可编辑 .sketch 与 FSD 入口标注 PNG（展开菜单避免遮挡左侧导航）。`
