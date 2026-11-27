---
name: fsd-sketch
description: "将浏览器真实界面状态转化为可编辑原生 Sketch（.sketch）原型与高清 FSD 插图（PNG）的专项 Skill。覆盖四大高频场景：（1）入口导航标注图（支持展开菜单与被遮挡导航同屏错位排布）；（2）应用功能页面与表单/列表/工作区重建；（3）数据看板/大屏重建（含跨时间窗一致脱敏样本数据规范）；（4）已有 .sketch 画板的精准裁框与重导出。可直接消费 fsd-writer 生成的 [VISUAL-PLACEHOLDER | TYPE: entry-screenshot / sketch] 占位符。当需要从网页采集状态生成可编辑 .sketch 原型、导出干净的 FSD 界面插图，或调整已有 .sketch 画板边界时使用。"
---

# fsd-sketch（网页真实状态转原生 Sketch 与 FSD 插图）

从真实浏览器界面或已有 `.sketch` 源文件出发，产出**可编辑原生 `.sketch` 文件**及**高质量、口径一致的 FSD 插图 PNG**。直接承接 `fsd-writer` 生成的 `entry-screenshot` 与 `sketch` 两类视觉占位符。

---

## 何时使用

- 消费 `fsd-writer` 中的 `[VISUAL-PLACEHOLDER | TYPE: entry-screenshot]` 或 `[VISUAL-PLACEHOLDER | TYPE: sketch]`
- 需要可长期维护、二次编辑的真实 `.sketch` 文件（而非一次性 HTML 临时稿）
- 需要将真实网页状态净化、统一语言、调整视口或处理“展开下拉菜单遮挡左侧导航”后导出为干净的 FSD 界面插图
- 需要基于用户已手改的 `.sketch` 源文件做画板裁框（Frame Fitting）或局部组件更新并重导出 PNG

---

## Reference 按需读取索引

**仅在进入对应任务场景时读取相应参考文件：**

| 触发场景 | 必读 Reference |
|---|---|
| 浏览器真实状态采集（DOM / Shadow Root / A11y）、应用页重建、**展开菜单防遮挡错位排布**、大屏看板重建与一致脱敏数据集构造 | [`references/capture-and-reconstruction.md`](references/capture-and-reconstruction.md) |
| 使用 macOS `sketchtool run-script` 与 `require('sketch/dom')` 生成原生图层/矢量图表，以及导出与零警告核验 | [`references/native-sketch-api.md`](references/native-sketch-api.md) |
| 对用户已有的 `.sketch` 文件做画板边界裁切、壳层局部刷新（保留内嵌产品锁定层）或归档级精准修改 | [`references/frame-fitting-and-archive.md`](references/frame-fitting-and-archive.md) |

---

## 核心范围与保真铁律

动手采集或绘制前，必须锁定以下底线：

1. **基线优先与只改授权范围**：
   - 基于网页重建或错位组合时，以原始真实 UI 的宽度、栏结构、字号、配色、间距、项顺序与选中态为视觉基线；
   - 基于用户已编辑 `.sketch` 迭代时，必须以用户**最新保存版本**为基线，并保存为新的版本化副本，**绝不覆盖原文件**。
2. **业务状态精准匹配**：
   - 画面必须严格匹配 FSD 图注与正文描述的业务状态；**首次使用空态 / 筛选无结果空态 / 内嵌加载失败**属于完全不同的状态，禁止拿最容易截到的页面顶替。
   - 严禁为了凑界面数据而在真实业务系统中凭空造业务记录；需脱敏替换样本数据时必须事先获授权，并构建**全页数学自洽的单一数据集**。
3. **图文语言 100% 一致**：
   - 严格对齐 `VISUAL-PLACEHOLDER` 的 `LANG` 要求（英文 FSD 全部界面标签、图例、表头、示例条均须为英文），同时保留正确的专有产品名或真实项目名。
4. **维护端到端映射账本**：
   - 为每份 FSD 维护 `Placeholder ID / 原文位置 → Sketch 画板名 → 导出 PNG 路径` 映射；只交付最终从可编辑 `.sketch` 导出的 PNG，将采集原图、证据页与生成脚本独立存放，不混入交付图集。

---

## 四类作业模式路由

1. **模式 A — 入口路径与防遮挡组合图（`TYPE: entry-screenshot`）**
   - 读取 `references/capture-and-reconstruction.md`（第 2–3 节）。
   - 若展开应用切换器/下拉菜单会遮挡下方关键导航项：同视口分别采集**展开态**与**收起态** → 将展开菜单置入触发器下方文档流 → 测算应用白名单调整后的实际菜单高度 → 将下方导航分组统一向下平移相同偏移量并加高画板 → 用醒目圆角红框标注各级入口。
2. **模式 B — 应用功能页 / 组件重建（`TYPE: sketch`）**
   - 读取 `references/capture-and-reconstruction.md`（第 1–3 节）与 `references/native-sketch-api.md`。
   - 区分应用控件与浏览器/插件浮层；按区块分组重建可编辑导航、文本、控件与图标；组件级插图按目标组件精准裁剪，全页插图保持应用视口纵横比。
3. **模式 C — 大屏看板重建（`TYPE: sketch` · Dashboard）**
   - 读取 `references/capture-and-reconstruction.md`（第 4 节）与 `references/native-sketch-api.md`。
   - 按画布原生比例重建（排除编辑器工具栏、标尺与选中框）；若使用脱敏示例数据，先构造单一自洽数据集（确保 `Today / 7 days / This month`、日柱状图总和与明细表条数严格对齐）；普通图表用原生矢量路径与独立文字层绘制。
4. **模式 D — 已有 `.sketch` 精准裁框与壳层更新**
   - 读取 `references/frame-fitting-and-archive.md`。
   - 对用户已保存的 `.sketch` 仅调整目标画板边界或外围壳层（内嵌产品区域作为锁定层记录裁剪坐标与像素哈希），导出 PNG 并核对原文件哈希未变。

---

## 出厂验收清单（交付前必查）

1. **文件与脚本校验**：`sketchtool run-script` 出错也可能返回退出码 `0`——必须同时校验目标 `.sketch` 文件落盘、错误日志为空、图层数量符合预期、ZIP 结构完整且无重复图层 ID。
2. **导出与目视核验**：通过 `sketchtool export artboards FILE --output=DIR --formats=png --scales=1` 导出 PNG，零字体警告；放大目视核对密集表格、表单字段、单行截断是否仍保持单行、展开菜单是否与下移标题留足空隙、底部账号栏未被裁切。
3. **浏览器环境复原**：若采集过程中切换过标签页、路由、菜单状态或视口覆写，交付前必须完整恢复初始状态并断开连接。
4. **诚实披露缺口**：近似替代的 Logo/图标/头像须在交付说明中披露；无法采集的缺失页面或受阻内嵌内容如实注明，绝不冒充完整复现。
