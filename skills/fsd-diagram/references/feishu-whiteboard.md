# 飞书画板发布与局部编辑适配器（可选）

仅当用户明确要求将图表**写入或修改飞书云文档画板（Feishu Whiteboard）**时读取本文件；通用 Markdown / Notion / Word 场景直接产出 SVG / PNG 即可，无需依赖本文件。

---

## 1. 本地 SVG 渲染与自动检查

生成符合 `style-tokens.md` 的 SVG 后，使用 `@larksuite/whiteboard-cli` 渲染本地预览并跑几何自检：

```bash
npx -y @larksuite/whiteboard-cli@^0.2.13 -i diagram.svg -f svg -o diagram.png
npx -y @larksuite/whiteboard-cli@^0.2.13 -i diagram.svg -f svg --check
```

- 目标：`textOverflow`、`nodeOverlap`、`textOcclusion` 均为 `0`（注意区分背景泳道/容器的有意重叠与真实节点碰撞，不能机械只看警告总数）。
- **必须目视检查 `diagram.png`**：逐框确认无截字、无箭头伸入卡片、Pill 标签距上下卡片 `>= 25px`、折线避开竖向虚线、英文图零 CJK 残留。
- 飞书画板线宽映射：`stroke-width: 2 → narrow`，`stroke-width: 3 → medium`；带 `stroke-dasharray` 的 `<line>` / `<path>` 原生映射为 `border_style: dash`。

---

## 2. 新建或已授权整板写入

- **务必先备份原画板**（若编辑已有画板）：将当前 `raw` JSON 与 `preview` 导出到独立恢复目录，与新稿目录隔离，原始恢复文件保持不可变。
- 仅在新建空白画板或**用户已明确授权结构重建**时，才使用 `whiteboard +update --input_format svg`（或 `--overwrite`）整板写入。
- 若需在已有画板中复用现有 `whiteboard_token`，禁止新建空白画板替代。

---

## 3. 修改已有画板：优先原生节点局部增删（禁止轻易 `--overwrite`）

当画板已存在且只需局部微调（改文案、调字号/框高、换线型、删多余元素、补几根线或分支）时，**直接通过 OpenAPI 局部增删原生节点**，避免整板重绘破坏协作者调整过的布局：

1. **读取并备份现状**：
   ```bash
   lark-cli api GET /open-apis/board/v1/whiteboards/<token>/nodes --as user > backup_raw.json
   ```
2. **计算替代节点几何**：在节点列表按 `type`、`style.fill_color`、`width`/`height`、`x`/`y` 筛出目标节点；合并碎片文本框时遵循 `references/board-roundtrip.md` 第 3 节。
3. **确认全链路权限后先加后删**：
   - 新增前确认 `POST` 与 `DELETE` 均已获授权，避免“加上去了删不掉”留下重影。
   - 新增节点（**不要带 `parent_id`、`z_index`**；连线节点带 `x`/`y`/`width`/`height`、`connector.start/end.position`、`shape: "straight"`、`specified_coordinate: true`）：
     ```bash
     lark-cli api POST /open-apis/board/v1/whiteboards/<token>/nodes \
       --params '{"client_token":"<unique_id>"}' --data @new.json --as user
     ```
   - 不确定原生 JSON 结构时，先写一小段 SVG 用转换命令照抄：
     ```bash
     npx -y @larksuite/whiteboard-cli@^0.2.13 -i snippet.svg -f svg --to openapi --format json
     ```
   - 批量删除旧节点（每批最多 100 个）：
     ```bash
     lark-cli api DELETE /open-apis/board/v1/whiteboards/<token>/nodes/batch_delete \
       --params '{"client_token":"<unique_id>"}' --data '{"ids":[...]}' --as user
     ```
4. **写入后双核验（写入成功 ≠ 视觉验收成功）**：
   - 重新导出 `raw` 核对节点数、文本完整性、无碎片化断句与连线端点；
   - 导出真实服务器 `preview`（**输出路径不带扩展名**，让工具按返回类型自动补 `.jpg`/`.png`）并目视检查：
     ```bash
     lark-cli whiteboard +export --as user --whiteboard-token <token> --output-type preview --output ./verify_preview --overwrite
     ```
   - 若 `raw` 已更新但 `preview` 仍显示旧缓存，等待 2–3 秒重新导出 `preview` 对照，**不要**因为缓存旧预览而重复执行写操作。
