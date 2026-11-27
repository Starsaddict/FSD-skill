# 原生 Sketch（`sketchtool`）生成 API 避坑与核验规范

本文件沉淀 macOS 环境下通过 Sketch 内置 `sketchtool run-script` + `require('sketch/dom')` 编程生成可编辑 `.sketch` 文件与导出 PNG 的实测要点。

> **跨平台提示**：本参考文件适用于已安装 Sketch 的 macOS 环境。若当前执行环境无 Sketch（如远程 Linux 容器），可先完成状态采集清单与结构规范，或在用户明确同意下产出临时审查预览，但**不得将一次性 HTML/位图伪称为原生可编辑 `.sketch` 文件**。

---

## 1. `sketchtool run-script` 实测 API 要点

| 易踩坑点 | 实测正确做法 |
|---|---|
| **传文件路径报错** | `sketchtool run-script` 接收的是 **JavaScript 源码字符串**，不是文件路径（传路径会被当 JS 解析并报 `Invalid regular expression: invalid flags`）：<br/>`subprocess.run([sketchtool, 'run-script', script_path.read_text(), '--without-activating=YES'], check=True)` |
| **大脚本内联参数语法误报** | 生成脚本较长时，先将完整 UTF-8 源码落盘，再传短加载器作为参数（即便源码已过 `node --check`，超长内联参数仍可能触发误导性语法错）：<br/>`eval(String(NSString.stringWithContentsOfFile_encoding_error_('/abs/path/gen.js', 4, null)))` |
| **全局 `sketch` 变量冲突** | `run-script` 已预注入 `sketch` 绑定，严禁写 `const sketch = ...` 重复声明。 |
| **无 `@skpm/fs` 模块** | 裸 `run-script` 环境没有 `@skpm/fs`；读文件用 `NSString.stringWithContentsOfFile_encoding_error_(path, 4, null)`，写文件用 `NSString` 的原子 UTF-8 写入方法。 |
| **同步保存无 Promise** | `doc.save(path)` 是同步方法且返回 `undefined`，**禁止链式调用 `.then()`**；保存后仅关闭自己创建的文档 `doc.close()`，不要误关用户已打开的文档。 |
| **圆角与字重映射** | `ShapePath` 矩形通过 `points[].cornerRadius` 设置圆角；原生文本 `fontWeight` 范围为 **`0–12`**，不是 CSS 的 `100–900`。 |
| **导入 SVG 矢量图标** | 使用 `D.createLayerFromData(NSString.stringWithString_(svg).dataUsingEncoding_(4), 'svg')` 创建矢量层，随后再设置 `parent`、`name` 与 `frame`。 |
| **字体选型防警告** | 系统 UI 文本用 `.AppleSystemUIFont`；站点明确要求时用 `Avenir Next`；标识码/等宽字用 `Menlo`；中文显式用 **`PingFang SC`**。**禁止使用 `.PingFangUITextSC-Regular`**（导出时会触发 CoreText fallback 警告）。 |
| **现代 CSS 色彩函数** | CSS `oklab(...)`、`oklch(...)` 与 `color(srgb ...)` 必须先转换为标准 Hex/sRGB 再传入原生 Sketch API；同时显式保留 `text-transform` 与省略号 `…`。 |
| **渐变方向与圆角遮罩** | 未显式指定角度的原生线性渐变默认应设为垂直方向；封面或带圆角的背景图必须使用**圆角编组遮罩**（否则内部直角子图层会覆盖外层圆角）：将组内第一个 `ShapePath` 调用 `sketchObject.setHasClippingMask(true)` 遮罩后续子图层。 |

### 最小生成骨架示例

```js
var D = require('sketch/dom');
var doc = new D.Document();
var page = doc.pages[0];
var ab = new D.Artboard({
  parent: page,
  name: 'Overview',
  frame: { x: 0, y: 0, width: 1512, height: 1100 }
});
new D.Text({
  parent: ab,
  text: 'Overview',
  frame: { x: 32, y: 24, width: 300, height: 40 },
  style: {
    fontFamily: '.AppleSystemUIFont',
    fontSize: 24,
    fontWeight: 9,
    textColor: '#20231f'
  },
  fixedWidth: true
});
doc.save('/absolute/path/new-output.sketch');
doc.close();
```

---

## 2. 看板图表原生矢量构建原则

- **折线图 / 面积图 / 基线**：使用带精确坐标点的 SVG `<path>` / `<line>`（基线加 `stroke-dasharray`）经 `D.createLayerFromData(..., 'svg')` 导入为可编辑矢量，坐标轴刻度、图例与终点数值全部使用独立 `D.Text` 图层，确保二次改数无需重画整图。
- **柱状图 / 堆叠条 / 进度条**：直接用 `D.ShapePath` 矩形按统一比例尺生成，统一归入对应图表 `Group` 中，方便设计师微调配色与柱宽。
- **严禁混入参考页**：看板类交付 `.sketch` 仅包含正式可编辑画板，不把浏览器参考截图页或数据核对草稿页塞入正式文件（除非用户明确要求附加参考画板）。

---

## 3. 导出与自动化核验清单

1. **拒绝只看退出码**：`sketchtool run-script` 内部抛错仍可能返回 shell exit `0`。必须显式检查：
   - 目标 `.sketch` 文件实际生成且非空；
   - 脚本错误日志为空；
   - 解析归档内的图层数与预期画板数一致（**注意：Sketch 2026 将 API 创建的 Artboard 存为 `_class: "group"` 且 `groupBehavior: 1`**，统计画板时须计入此类 Frame，不要只查旧版 `artboard` 类名）。
2. **PNG 导出命令**：
   ```bash
   sketchtool export artboards INPUT.sketch --output=DIR --formats=png --scales=1
   ```
3. **完整性与视觉终审**：
   - 验证 `.sketch` ZIP 归档完整、所有图层 ID 唯一、引用的位图资源全部存在、导出 PNG 可正常读取、命令行零字体警告。
   - 分别统计参考截图数量与可编辑界面画板数量，不混为一谈。
   - 目视放大审阅高密度表格、表单字段、封面圆角遮罩及画板四边是否有意外裁切。
