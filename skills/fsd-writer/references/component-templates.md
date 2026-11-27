# 组件级 FSD 写作模板手册

起草任何具体页面或能力模块时，必须先识别组件类型，再严格按以下模板顺序与表格结构写作。

> **通用写作铁律**：
> 1. **先写 Purpose（做什么用），再写有什么**：切忌只列“页面里包含 A/B/C 字段”，必须先一句话说清用户来这里是为了看什么、判断什么或完成什么动作。
> 2. **先说从哪来（Entry），再讲页内细节**：任何二级页、抽屉、弹窗或独立工作区，开头必须交代触发入口与相邻页面的定位边界。
> 3. **多卡片/多字段必须拆表**：禁止把 3 张卡片或 6 个字段压缩成一段自然语言。
> 4. **术语全篇一致**：统计指标卡统一称为 **Metric Cards**；状态名、按钮名、页签名与界面文案保持 100% 一致。

---

## 1. Entry Path（入口导航）

**适用场景**：每个 Feature 的主入口、配置中心入口、或容易迷路的独立功能页。

**模板**：
```markdown
### Entry Path

**Navigation Path:** `{Platform} → {App Switcher (App Name)} → {Left Navigation: Module} → {Top Tab}`

> [VISUAL-PLACEHOLDER | ID: FIG-XX | TYPE: entry-screenshot | ...]

Opening `{Feature Name}` lands on the **`{Default Tab}`** tab by default, alongside **`{Tab B}`** and **`{Tab C}`** in the top bar. `{Conditional Entry, e.g., Settings icon in upper-right}` is visible only to `{Allowed Roles}` (see Section 2.2.1).
```

**反例（避免）**：只贴一张未标注红框的整屏大图，不写面包屑路径，也不说明默认打开哪个页签。

---

## 2. Metric Cards（统计指标卡组）

**适用场景**：页面顶部的 KPI / 汇总统计卡片区（如今日告警、聚合率、MTTA、MTTR 等）。

**模板**：
```markdown
#### Metric Cards

Displays `{N}` `{read-only / interactive}` metric cards across the top of `{Page Name}` to summarize `{业务目的}`.

> [VISUAL-PLACEHOLDER | ID: FIG-XX | TYPE: sketch | ...]

| Card | Time / State Scope | Description, Formula & Empty-Value Rules |
|---|---|---|
| **`{Card Name 1}`** | `{Today (00:00 to now) / Real-time snapshot}` | `{主数值含义 + 副标签含义}`。Formula: `{精确公式}`。`{统计窗口归属字段、排除项说明；分母为 0 或无数据时留白，不以 0 填充}`。 |
| **`{Card Name 2}`** | `{...}` | `{...}` |

**Card Interactions:** `{若为纯展示，明确写“Pure display (non-clickable)”；若可交互，写清悬停展示内容、点击切换哪个下方图表或跳转至哪个详情页}`。
```

**反例（避免）**：把 3 张卡片写成一句“包含 A 卡片（展示 x）、B 卡片（展示 y）和 C 卡片（展示 z）”；或只写指标名不写公式和分母为 0 时的表现。

---

## 3. List & Table View（列表 / 工作队列）

**适用场景**：工作队列、记录清单、报告列表、实时明细表。

**模板**：
```markdown
#### `{List Name}`

**Purpose & Entry:** `{本列表用于查看/处理哪类对象}`。Displayed when **`{Tab Name}`** is selected under `{Parent Page}`.

> [VISUAL-PLACEHOLDER | ID: FIG-XX | TYPE: sketch | ...]

**Field Specifications:**

| Field | Description & Business Rules |
|---|---|
| **`{Column 1}`** | `{业务含义、枚举值或字符长度上限、折叠规则（如多值显示 +N）、是否与源系统字段区分}` |
| **`{Column 2}`** | `{...}` |

**List Controls & Navigation:**
- **Status / Queue Tabs:** `{Tab A}` / `{Tab B}` / `{All}`（each showing item count）.
- **Search & Filter:** Inline search by `{检索字段}`; filter by `{筛选维度列表}`.
- **Sort & Pagination:** Sort options (`{默认排序}` / `{其他排序}`), refresh action, pagination.
- **Row Click Destination:** Clicking any row opens **`{Target View / Drawer Name}`** (see Section `{X.X.X}`).
- **Empty & Error States:** `{无数据提示 / 加载失败提示}`.
```

---

## 4. Form & Settings Page（表单 / 配置页）

**适用场景**：新建/编辑表单、数据源配置、集成配置、参数设置。

**强制三段式顺序**：**① 顶部运行时状态与原型图 → ② 表单项定义表 → ③ 表单交互与校验规则**。

**模板**：
```markdown
#### `{Form / Settings Name}`

**Purpose:** `{本表单用于配置什么，配置结果被哪些功能模块消费（附章节号）}`。

**1) Header Runtime State & Prototype**
- **Status & Stats（如有）:** `{如顶部 Running/Stopped 状态徽标、最近一次拉取时间与计数}`.
- **Header Controls（如有）:** `{如独立 Pull 开关、只读 Trial read 按钮、更多菜单 ···}`.

> [VISUAL-PLACEHOLDER | ID: FIG-XX | TYPE: sketch | ...]

**2) Field Specifications**

<!-- 若表单内部有多个分组卡片，可按小标题分别列小表，或在表首按需增加 Group 列 -->

| Field | Description & Constraints | Example / Note |
|---|---|---|
| **`{Field Name 1}`** | `{控件类型（输入框/下拉/开关/多选等）}`；`{必填/选填、默认值、取值范围或可选枚举}`。`{业务含义；级联显隐/只读条件（如未选上游项时不展示本项选项）；校验规则与阻断提示文案（精确引号给出）}`。 | `{示例值、客户占位符说明或 [Open Check]}` |
| **`{Field Name 2}`** | `{...}` | `{...}` |

*(注：若表单简单且无需示例/备注，可省略 `Example / Note` 列，保留 `Field | Description & Constraints` 两列。)*

**3) Form Interactions & Lifecycle Rules**
- **Trial / Preview（如有）:** `{明确只读范围、预览展示哪些结果，且不写入正式数据、不推进下次定时游标}`.
- **Save & Activation:** `{点击保存后的生效时机（如立即在下一次调度生效，无需单独发布）；修改起始游标等关键项时的页内提示}`.
- **Unsaved Changes Protection:** `{切换项或关闭弹窗时触发确认框文案及选项（如 "Discard unsaved changes?" — Keep editing / Discard）}`.
- **Disable / Delete Impact（如有）:** `{禁用或删除后是否立即停止执行，以及已产生的历史数据如何保留}`.
```

---

## 5. Detail Workspace & Tabs（分栏 / 多 Tab 工作区）

**适用场景**：左对话+右详情面板、主从双栏视图、多 Tab 复合详情页。

**强制 4 步自顶向下结构（操作随区走，不将操作流放至文末）**：

**模板**：
```markdown
#### `{Workspace Name}`

**1) Workspace Purpose & Entry**
- **Purpose:** `{一句话说明用户进入该工作区要完成的端到端任务}`.
- **Entry & Initial Load:** Clicking any row in `{Source List}` opens this workspace. Opening the page renders `{最新已保存状态}` immediately and `{是否触发新的后台计算/AI 分析}`.

**2) Full Layout & Zone Map**

> [VISUAL-PLACEHOLDER | ID: FIG-XX | TYPE: sketch | ...]

| Zone | Purpose | Key Interactions |
|---|---|---|
| **Left Pane — `{Pane Name}`** | `{让用户来看/做什么}` | `{提问、纠偏、下发操作指令、展开执行日志等}` |
| **Right Pane — `{Pane Name}`** | `{让用户来看/做什么}` | `{切换 Tab、点击顶部动作按钮、图谱缩放等}` |

**3) Zone-by-Zone Breakdown**

##### Left Pane: `{Pane Name}`
- **Displayed Content:** `{按从上到下说明头部、主体信息卡片/对话轮次、输出物卡片与输入框}`.
- **Supported User Operations:**

| Operation | Trigger / Entry | Execution & Side-Effect Rules |
|---|---|---|
| **`{Operation 1}`** | `{用户如何触发（输入指令 / 点击卡片）}` | `{执行前提、确认机制、对当前对象及关联状态的影响}` |

##### Right Pane: `{Pane Name}`

- **Tab 1: `{Tab Name}`**
  - **Purpose:** `{先讲清楚用户切到这个 Tab 是为了看什么、判断什么！}`
  - > [VISUAL-PLACEHOLDER | ID: FIG-XX | TYPE: sketch | ...]
  - **Content & Modules:**

  | Field / Module | Description & Business Rules |
  |---|---|
  | **`{Item 1}`** | `{含义、长度/句数限制、编号稳定性、空态表现}` |

  - **Tab Actions & Controls（如有）:** `{本 Tab 独有的顶部按钮、二次确认及后续联动}`.

- **Tab 2: `{Tab Name}`**（按同样结构依次展开：`Purpose → 局部图 → 字段/内容表 → 本 Tab 控件`）

**4) Cross-Zone Sync & Exception Rules**
- **Cross-Zone Refresh:** `{左区操作/回复成功后如何刷新右区；若生成校验失败或保存失败，保留上次成功快照}`.
- **Concurrency Lock:** `{正在处理当前轮次时再次提交如何提示，禁止并行会话}`.
- **Load Failure:** `{详情加载失败时的明确 UI 错误提示文案}`.
```

---

## 6. Analytical Charts（分析 / 趋势图表）

**适用场景**：折线图、堆叠柱状图、Top N 排行图、双指标对比图。

**模板**：
```markdown
#### `{Chart Name}`

- **Purpose & Entry:** `{说明图表回答什么分析问题}`。Selected by default on `{Page}`, or switched to via `{Metric Card / Tab Name}`.

> [VISUAL-PLACEHOLDER | ID: FIG-XX | TYPE: sketch | ...]

- **Header & Summary Strip（如有）:** `{标题、同步时间、周期汇总指标及其与上方 Metric Cards 口径异同说明}`.
- **Axes, Series & Formulas:**
  - **Time Window & X-Axis:** `{如 Last 30 Days / Trailing 7 days / Month-to-date，截至今日}`.
  - **Series & Baselines:** `{每条线/柱子的含义与计算口径、实线 Mean / 虚线 Median / 参考基线来源}`.
- **Empty & Missing-Date Rules:** `{缺失日期或无数据区间留白，严禁以 0 填充}`.
- **Interactions:** `{纯展示 / Hover 浮层显示内容 / 是否支持点击下钻}`.
```

---

## 7. Report / Generated Document（报告 / 生成式文档）

**适用场景**：定时周报、专项分析报告、运营复盘报告、审计报告等具有**统一固定章节结构**的文档型产物。

**模板**：
```markdown
#### `{Report Type Name, e.g., Analysis Reports / Weekly Reports}`

**1) Report Purpose, Trigger & Scope**
- **Purpose:** `{说明报告面向谁、解决什么复盘/决策问题}`.
- **Trigger & Window:** Triggered `{Manually / On schedule}`; evaluates `{Lookback period / Reporting period}`; delivered as `{complete Markdown document / PDF}`. `{若支持生成中取消，写明入口与取消失败提示}`.

**2) Entry & Preview Layout**
- **Entry:** `{从列表行点击 / Metric Card 来源链接 / 手动触发按钮进入}`（若使用左对话+右报告分栏工作区，遵循模板 5 说明区域交互）。

> [VISUAL-PLACEHOLDER | ID: FIG-XX | TYPE: sketch | ...]

**3) Fixed Report Structure（固定报告章节规格表 — 核心必填）**

每类报告必须明确其统一章节骨架及每章固定讲述的内容（周期报告需注明相对常规报告额外增加的固定章节，如环比对比与下期重点）：

| Section / Chapter | Purpose & Fixed Content | Data Scope & Integrity Rules |
|---|---|---|
| **1. `{Chapter 1, e.g., Operational conclusion}`** | `{本章节固定回答的核心结论与决策摘要}` | `{严格基于本次观察窗口的已确认统计结果}` |
| **2. `{Chapter 2, e.g., Observation scope & coverage}`** | `{固定说明统计时间窗、时钟口径与采集覆盖范围}` | `{采集缺失或覆盖盲区如实披露}` |
| **3. `{Chapter 3, e.g., Key metrics & trends}`** | `{固定展示的核心指标、趋势分布与明细排行}` | `{缺值留白不补零；不估算代理指标}` |
| **4. `{Chapter 4, e.g., Findings, recommendations & gaps}`** | `{固定输出的发现项、可验证建议与证据缺口}` | `{不将相关性表述为因果；周报额外包含环比对比与下期重点}` |

**4) Post-Save Sync, Follow-up Q&A & Failure Handling**
- **Post-Save Refresh:** `{报告保存成功后，自动刷新概览页摘要条、Metric Cards 与图表}`.
- **Follow-up Q&A & Immutability:** `{用户可在报告会话中就数据口径、证据或结论追问；回答严格基于已保存报告及对应观察窗口，严禁修改或覆盖已完成的历史报告}`.
- **Failure & Cancellation:** `{生成或保存失败时状态置为 Failed 并展示明确错误文案（未完成保存绝不宣称成功）；生成中支持取消及取消失败提示}`.
```

---

## 8. Process Flow & State Machine（业务流程、时间窗口与状态机）

**适用场景**：后台采集标准化管线、多步判定规则、对象生命周期状态转换、多计时器边界。

**模板**：
```markdown
#### `{Process / Lifecycle Name}`

**Trigger & Scope:** `{由什么定时任务/事件/人工操作触发，覆盖哪类对象}`.

> [VISUAL-PLACEHOLDER | ID: FIG-XX | TYPE: diagram | ...]

**Step-by-Step Rules / Transitions:**
1. **`{Step / Path 1}`:** `{正常路径规则与判定表}`.
2. **`{Exception / Failure Branch}`:** `{读取失败游标是否推进、转换失败保留哪些原始信息、谁可手动重试、重试失败如何记录}`.

**Time Windows & Boundaries（涉及计时器时必填）:**

| Time Rule | Default | Boundary, Reset & Expiry Behavior |
|---|---|---|
| **`{Window Name}`** | `{如 6 hours}` | `{起算时间戳；哪些事件重置、哪些不重置；是否与其他窗口叠加；到期后的状态流转、是否仍可被查看及是否发送通知}` |

**Termination & Cross-System Impact（涉及生命周期终态时必填）:**
- **Terminal State & Irreversibility:** `{终态名称；是否不可逆/不可重新打开；是否影响底层源对象状态}`.
- **Cross-System Side Effects:** `{明确区分“超时过期”与“人工关闭/规则关闭”对外部工单或下游系统的不同影响}`.
```

---

## 9. External Integration & Callback（外部系统集成与回调）

**适用场景**：对接外部工单系统、告警源、审批流或接收异步回调。

**模板**：
```markdown
#### `{External System}` Integration, Statuses, and Callbacks

**Creation, Mapping & Closing Rules:**
- **Trigger Conditions & Concurrency Cap:** `{满足什么条件自动触发；单对象是否最多 1 条活跃外部记录}`.
- **Business Field Mapping:** `{优先级/紧急度映射、标题描述模板来源、申请人/操作人身份归属}`.
- **Closing Sync & Timeout/Failure Fallback:** `{外部关闭失败时本地状态是否仍关闭及失败记录位置；创建超时/未确认时保持何种状态且禁止重复创建}`.

**Canonical Status Mapping（全文档唯一权威状态表）:**

| Status | Source | Meaning & Rules |
|---|---|---|
| **`{Status 1}`** | `{Local System / External Code XX}` | `{业务含义、是否计入已认领/已关闭、外部删除时的降级显示}` |

**Callback & Security Validation（若接收回调）:**
- **Endpoint Pattern:** `{仅标明业务配置路径模板与最小入参语义，不暴露无关内部架构}`。
- **Conversation / Trigger Behavior:** `{回调是否触发对话或仅做状态更新}`.

| Validation Check | Rule |
|---|---|
| **Caller identity** | `{专用回调身份鉴权，未认证请求直接拒绝且不改数据}` |
| **Status source** | `{回调仅作通知信号，忽略回调体内状态并回查外部系统权威状态，防止伪造/重放}` |
| **Entity match** | `{严格匹配同工作空间由本系统创建的记录，未命中则 0 更新}` |
| **Invalid request** | `{必填标识缺失或为空时返回校验错且不改数据}` |
| **External read failure** | `{回查失败时保持原状态、记录失败日志并返回错误}` |
| **Update scope** | `{明确仅允许更新哪几个字段}` |
```

---

## 10. Access Control and User Roles（角色与权限矩阵）

**适用场景**：`2.2.1 Access Control and User Roles`（放在功能讲解之后）。

**模板**：
```markdown
### Access Control and User Roles

Access across `{System Name}` follows `{N}` permission tiers: **`{Role 1}`**, **`{Role 2}`**, ... `{数据隔离边界说明，如原始证据仅对同工作空间已认证成员可见}`。

| Role | Access Scope | Allowed Capabilities |
|---|---|---|
| **`{Read-only user}`** | `{可见页面与数据范围}` | `{允许的只读查看动作；明确列出禁止执行的关键操作}` |
| **`{Member}`** | `{...}` | `{在只读基础上增加的日常业务操作；明确禁止配置与高危运维}` |
| **`{Administrator}`** | `{...}` | `{在成员基础上增加的配置、重试、规则与运维能力}` |
| **`{Workspace Owner}`** | `{...}` | `{最高治理权限：角色分配、连接器归属与治理控制}` |
```

---

## 11. Maintenance & Destructive Operations（维护与破坏性操作）

**适用场景**：数据重置、批量清空、不可逆删除。

**模板**：
```markdown
### Maintenance and `{Operation Name}`

> [VISUAL-PLACEHOLDER | ID: FIG-XX | TYPE: sketch | ...]

Provides `{用途说明，如上线前测试清理或重新初始化}` while preserving `{保留内容概括}`:

| Group | Affected Data & Behavior |
|---|---|
| **Clears** | `{会被永久删除的数据清单}` |
| **Keeps** | `{完整保留的配置、规则与任务清单}` |
| **Post-action Resume Behavior** | `{执行后哪些启用项会自动恢复运行以及起始读取位置}` |

**Execution & Production Safeguards:**
- **Confirmation & Feedback:** Clicking **`{Action Button}`** requires a secondary confirmation dialog; on completion, the UI displays `{删除条数与重置结果摘要}`.
- **Production Safeguard:** `{明确生产环境是否禁用，或是否强制执行前导出、双人审批与审计日志；如待业务拍板，显式标出 *[Open Check: ...]* }`.
```
