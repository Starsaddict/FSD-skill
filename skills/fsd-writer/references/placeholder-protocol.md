# 通用视觉 Placeholder 规范（平台无关）

本规范用于将 **FSD 正文写作** 与 **截图 / Sketch / 流程图制作** 解耦：主线起草阶段不中断心流，统一在图位插入结构化的高亮块占位符；待正文质量经人工确认后，再由人工或后续视觉 Skill 按规格补图并原位替换。

---

## 1. 标准 Placeholder 格式（通用 Markdown Callout）

所有占位符使用标准 GitHub Flavored Markdown Callout 语法，确保在任意 Markdown 编辑器中清晰高亮、且方便脚本或人工批量检索（统一前缀 `[VISUAL-PLACEHOLDER`）：

```markdown
> [!NOTE] [VISUAL-PLACEHOLDER | ID: FIG-01 | TYPE: sketch | LANG: English]
> - **目标视图**：Event Studio Settings → Alert Sources 配置表单
> - **展示状态**：Running 状态；展示 Pulling（interval=10, Start point=Last 1 day）与 Processing 4 项及底部 Delete source 危险区
> - **关键元素与示例值**：Pull 开关开启、Trial read 按钮可见；示例值需与正文表格完全一致
> - **画幅与净化要求**：1440px 宽屏视角无横向截断；清除测试脏数据与卡顿动画；全英文界面无中文残留
> - **回填图注**：`Figure: Event Studio Settings — Alert Sources Field Specifications`
```

### 字段说明

| 字段 | 必填 | 说明 |
|---|---|---|
| `ID` | 是 | 全篇唯一递增编号（`FIG-01`、`FIG-02`… 或 `DIAG-01`、`DIAG-02`…），便于核对是否全部回填 |
| `TYPE` | 是 | 三选一：`entry-screenshot`（入口红框标注图，推荐 Sketch 制作/导出）\| `sketch`（干净界面原型图）\| `diagram`（泳道图/流程图/状态图/时间轴图） |
| `LANG` | 是 | 强制与 FSD 正文语言一致（如 `English` / `中文`） |
| `目标视图` | 是 | 精准指明对应的页面、Tab、弹窗或业务流程名称 |
| `展示状态` | 是 | 明确要展示的状态（如 Active / Empty / Running / Hover 公式态 / 异常分支） |
| `关键元素与示例值` | 是 | 需在图中清晰可见的字段、按钮、状态徽标或流转节点，且与正文表格口径一致 |
| `画幅与净化要求` | 推荐 | 针对不同 `TYPE` 注明防错要点（见下文分类要求） |
| `回填图注` | 是 | 补图完成后保留在图下或 alt 文本中的标准图注 |

---

## 2. 三类视觉资产的规格要点

### A. `entry-screenshot`（入口标注图）
- 用途：专门用于 `Entry Path`，帮助读者直观找到点击路径。
- 规格要点：
  - **推荐制作方式**：优先使用 **Sketch 制作/导出**，保留真实应用导航壳层（应用切换器、左侧菜单、顶部页签）。
  - **允许轻微布局调整以避免遮挡**：必要时可对界面元素的位置关系做轻微调整（例如：实际界面中展开下拉框会遮挡下方关键导航项时，可将展开面板与导航项错位排布，确保各级入口同时清晰可见）。
  - 使用清晰圆角红框标注出面包屑对应的每一级点击入口。
  - 裁剪掉无关空白区域，保持文字清晰可读。
  - 补图执行时，可直接交由同仓库配套的 `fsd-sketch` Skill（模式 A：展开菜单防遮挡错位排布）制作并导出。

### B. `sketch`（功能界面 / 表单 / 列表 / 看板原型图）
- 用途：Metric Cards、列表队列、配置表单、分栏详情工作区、图表、大屏看板。
- 规格要点：
  - 优先展示干净、脱敏、与正文表格示例完全一致的界面状态。
  - 调整到适配该模块信息密度的视口宽度，避免列被挤压截断或滚动条遮挡。
  - 杜绝截屏瞬间卡在半路的滚动数字、乱码测试名、以及与正文语言不符的 UI 文案。
  - 补图执行时，可直接交由同仓库配套的 `fsd-sketch` Skill（模式 B/C/D：页面重建、看板一致脱敏数据集与画板精准裁框）生成可编辑 `.sketch` 并导出 PNG。

### C. `diagram`（泳道图 / 流程图 / 状态迁移图 / 时间轴图）
- 用途：User Journey 总览、数据处理管线与异常重试分支、对象生命周期状态机、多时间窗口边界。
- 规格要点：
  - 在 Placeholder 中列出**必须包含的泳道/角色、编号阶段、主路径节点与关键异常/终止分支**。
  - 注明主流程（实线）与异步回调/重试（虚线）的区分，避免图与正文规则脱节。
  - 补图执行时，可直接交由同仓库配套的 `fsd-diagram` Skill 按 4+1 语义配色与 5 类图表原型规范出图。

---

## 3. 跨文档平台映射表

`fsd-writer` 默认只输出通用 Markdown Callout。仅当用户明确要求将文档导入特定平台时，按以下方式映射：

| 目标平台 | Placeholder 承载方式 | 补图后的替换方式 |
|---|---|---|
| **Markdown / GitHub** | `> [!NOTE] [VISUAL-PLACEHOLDER ...]` | 替换为 `![Figure: ...](path/to/img.png)` 或嵌入 Mermaid/SVG |
| **飞书云文档（Feishu Docx）** | 高亮块 `<callout emoji="🖼️">...</callout>` | 替换为上传的图片块（`sketch`/`entry-screenshot`）或嵌入式画板（`diagram`） |
| **Notion / Confluence** | Callout Block / Info Panel | 替换为 Image Block 或 Diagram Embed |
| **Word / Google Docs** | 浅灰底色边框文本框 | 替换为内联图片并保留标准图注 |

---

## 4. 补图回填与验收规则（人工把控）

1. **汇总清单**：主线正文经用户确认后，按顺序导出全篇 `[VISUAL-PLACEHOLDER` 列表，列出每张图的 `ID`、`TYPE`、所在章节与绘制要点。
2. **人工派发或逐张补图**：由用户决定自行补图或调用已有的 Sketch / Diagram 专用 Skill 生成。
3. **回填验收（四必查）**：
   - 图中所有文字与正文语言 **100% 一致**；
   - 图中出现的按钮名、状态词、指标口径与正文表格 **100% 一致**；
   - 画幅完整无截断、无测试脏数据；
   - 原 `[VISUAL-PLACEHOLDER]` 高亮块已彻底删除，无残留占位符。
