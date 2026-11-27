---
name: fsd-writer
description: "通用、平台无关的功能规格说明书（FSD）写作、代码仓审校/生成与质检 Skill。支持三大模式：（1）Draft Mode——忠实将用户已有的产品思路按 User Journey 与组件级模板写成高质量 FSD，人工分步把控节奏，并在图位插入通用高亮块 Placeholder；（2）Code-Sync Mode——按“从前端到后端”思路结合研发代码仓逆向生成 FSD，或将代码仓与现有 FSD 双向比对并输出差异审校清单（规则/口径出入优先供研发审核是否按 FSD 修正业务，不盲目迁就代码改 FSD）；（3）QA Mode——对照 P0/P1/P2 分级质检清单对已有 FSD 做全面体检与纠偏。当用户要写 FSD、按 FSD 指南整理文档、根据代码仓库生成/核对/审校 FSD，或对现有 FSD 做评审质检时使用。"
---

# fsd-writer

将产品思路或代码仓事实，高质量地转化为结构严密、口径一致、平台无关的功能规格说明书（FSD）。

## 角色边界与核心铁律

执行任何模式前，必须严格遵守以下五条底线：

1. **只转化思路，不替代构思**：User Journey 规划与产品方案决策归用户主导。本 Skill 只负责把用户已提供的思路/素材整理成规范的 FSD，**不得擅自发散需求、臆造未提及的业务规则或替用户拍板**。
2. **缺口透明化**：发现 FSD 必填口径缺失（如空值显示、超时去向、默认值来源、跨系统副作用）时，先列出极简追问清单；用户暂未拍板的内容，在对应位置显式标记 `*[Open Check: ...]*`，绝不静默脑补。
3. **人工把控节奏，禁止过度自动化**：每个关键节点必须停下交由用户确认（大纲确认、逐功能审阅、差异裁决、质检修复），绝不擅自启动全自动端到端流水线或静默覆盖文档。
4. **平台无关**：默认产出标准 Markdown，不绑定飞书、Notion 或任何特定文档工具；仅在用户明确指定同步到某个平台时，再做平台格式适配。
5. **图文语言 100% 一致**：正文支持英文或中文（按目标项目要求），但所有视觉 Placeholder 规范、界面图与流程图标签必须与正文语言完全一致，严禁混杂。

---

## Reference 按需读取索引

**仅在进入对应模式或阶段时读取相应参考文件，不要在任务启动时一次性全部加载。**

| 触发场景 | 必读 Reference |
|---|---|
| 整理目录骨架、确认单 Case 多 Feature 展开方式、判断章节落位（Just-in-Time）或剔除工程冗余项 | [`references/structure-and-journey.md`](references/structure-and-journey.md) |
| 起草具体页面或功能模块（入口、Metric Cards、列表、表单、分栏工作区、图表、固定章节报告、流程状态机、外部集成、权限、维护操作） | [`references/component-templates.md`](references/component-templates.md) |
| 在正文中插入截图/Sketch/流程图占位符，或准备将占位符交由人工/子 Skill 补图与回填 | [`references/placeholder-protocol.md`](references/placeholder-protocol.md) |
| **Mode 2（Code-Sync）**：按“从前端到后端”思路结合研发代码仓生成 FSD 初稿，或对现有 FSD 与代码仓做双向差异审校 | [`references/code-sync-guide.md`](references/code-sync-guide.md) |
| **Mode 3（QA）**：对已有 FSD 做全面质检，或在起草/精修完成后执行出厂自检 | [`references/qa-checklist.md`](references/qa-checklist.md) |
| 用户主动要求评估 Skill 缺陷或起草脱敏改进反馈（日常使用完全静默，不主动打扰） | [`references/skill-feedback.md`](references/skill-feedback.md) |

> 完整的人类可读标准范本与指南位于仓库根目录的 [`../../docs/fsd-guide.md`](../../docs/fsd-guide.md)。

---

## 模式路由与工作流

先判断用户意图并进入对应模式；若意图不明确，直接用一句话向用户确认选用哪个模式及目标文档语言（中文 / 英文）。

### Mode 1: Draft Mode（基于产品思路起草 FSD）

适用于用户已梳理好业务想法、笔记、原型描述或会议纪要，希望将其写成规范 FSD。

1. **Step 1 — 吸收输入并轻整理大纲（Checkpoint 1）**
   - 读取 `references/structure-and-journey.md`。
   - 仅基于用户已有输入，整理出：
     - 目标文档语言与基本元信息
     - `1. Background` 核心要点
     - `2.1 Functionality` 下的 User Journey 阶段清单与 Feature 列表（若同一 Case 含多个 Feature，列出各自展开目录）
     - `2.2 Configuration & Governance` 配置与权限清单
     - 当前输入中缺失的必填口径（极简列表，供用户决定立刻补充或先标 `[Open Check]`）
   - **停止并请用户确认大纲与写作顺序**，确认后再动笔。
2. **Step 2 — 逐章 / 逐功能按组件模板起草（Checkpoint 2）**
   - 读取 `references/component-templates.md` 与 `references/placeholder-protocol.md`。
   - 按用户确认的节奏（推荐逐 Feature 或逐章）输出正文：
     - 严格遵守“哪里用到才在哪里讲”（业务参与体放业务总览开头；权限矩阵放配置章节；校验/异常/状态机就地内嵌）。
     - 每个页面/Tab 先写 **Purpose（做什么用）**，再放图位，再写结构化表格与交互规则。
     - 统计卡片统一称为 **Metric Cards**；表单统一遵循“顶部状态与原型图 → `Field | Description & Constraints | Example / Note（可选）` → 表单交互与校验”三段式；分栏/多 Tab 工作区遵循 4 步结构且操作随区走；报告类功能必须给出固定章节规格表。
     - 需要视觉图的位置统一插入标准高亮块 Placeholder，不中断正文写作。
   - 每完成一个模块或阶段，交给用户审阅，根据反馈微调后再继续下一部分。
3. **Step 3 — 视觉补图与出厂自检（人工触发）**
   - 正文质量获用户认可后，汇总全篇 `VISUAL-PLACEHOLDER` 清单，由用户决定逐张补图或交由专用视觉 Skill 处理。
   - 按用户要求运行 **Mode 3（QA）** 做最终交付前体检。

---

### Mode 2: Code-Sync Mode（基于研发代码仓生成或双向审校 FSD）

适用于研发或产品希望结合**实际代码仓库**逆向生成 FSD 初稿，或将现有 FSD 与代码实现做双向核对与审校。

1. **Step 1 — 确认子模式与扫描范围（Checkpoint 1）**
   - 读取 `references/code-sync-guide.md` 与 `references/structure-and-journey.md`。
   - 明确本次是 **2a（从代码仓逆向生成 FSD 初稿）** 还是 **2b（代码仓 vs. 现有 FSD 双向差异审校）**，确认代码仓路径/目标模块及目标文档语言。
2. **Step 2 — 遵循「从前端到后端」思路采集并转译业务事实**
   - **先前端（用户唯一接触面）**：先扫描前端路由、页面布局、组件、交互事件与 i18n 文案，还原用户“进入功能 → 阅读并理解页面 → 交互操作”的完整体验主线与功能骨架。
   - **后后端（规则与机制补充层）**：顺着前端入口与展示项向下穿透后端服务与 Prompt/规则配置，补充具体的业务规则、计算口径、状态机制、校验逻辑、权限守卫与异常处理。
   - **严防工程细节泄漏**：彻底过滤数据库表名、字段长度、代码类名、内部 API 路径与函数名，全部转译为用户可见行为与业务语言。
3. **Step 3 — 输出大纲或双向审校清单（Checkpoint 2）**
   - **2a 生成路径**：先提交从前端视角还原的 FSD 目录大纲与关键 `[Open Check]` 清单，获确认后再按 `component-templates.md` 逐章生成正文并插入视觉 Placeholder。
   - **2b 双向审校路径（核心原则：不默认按代码修改 FSD！）**：
     - **严禁直接以代码覆盖 FSD**。先输出结构化的 `FSD vs. Code 双向差异审校清单`：
       - 凡涉及**业务规则、计算口径、空值/超时边界、状态语义、权限与安全护栏**的出入，默认标记为 **`[Review for Dev Fix]`（供研发审核是否应按 FSD 修正业务实现）**，不轻易迁就代码改写 FSD；
       - 仅当属于已对齐的文案/交互细节补全时，才建议标记为 **`[Candidate for FSD Update]`**；
       - 意图不明确之处标记为 **`[Open Check]`**。
     - **停下等待研发与产品逐项审校拍板**：仅针对明确批复需同步文档的条目更新 FSD，其余归档为**研发待修复清单（Dev Action Items）**。

---

### Mode 3: QA / Review Mode（FSD 质检与纠偏）

适用于对已有 FSD（草稿或评审前版本）做系统性体检。

1. 读取 `references/qa-checklist.md`。
2. 逐条对照 **P0（阻断）/ P1（重要）/ P2（规范）** 清单扫描文档（包括正文、表格、状态枚举、公式、Placeholder/图注、语言一致性及 Anti-Scope 边界）。
3. 输出结构化质检报告：
   - 总体健康度摘要（按 P0 / P1 / P2 统计问题数）
   - 逐条列出问题位置、违反条款、具体问题说明与**可直接落地的修改建议**
   - 列出需业务/研发确认的 `[Open Check]` 汇总清单
4. **停下等用户确认修复范围**，获同意后再按项修改正文。

---

## 规范改进反馈（低侵入）

- **个案静默**：仅针对当前项目的业务数值、字段名或文案微调，直接执行，不提 Skill 反馈。
- **轻度触发**：仅当用户**明确质疑或推翻本 Skill 规则/模板本身**（疑似不完整、不适配场景、口径有问题或过于死板），才在完成修改后的回复末尾附一句简短提示（*“注：这似乎涉及本 Skill 规范本身，如需反馈改进，可让我生成一份脱敏 Issue 草稿。”*），绝不反复追问或擅自调 `gh` 提交；用户要求起草反馈时再读取 [`references/skill-feedback.md`](references/skill-feedback.md)。
