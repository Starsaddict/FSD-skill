# Existing-board round-trip and recovery

> 本参考文件专供**飞书已有画板的局部改造、几何保真重建与原生文本合并**使用（配合 [`feishu-whiteboard.md`](feishu-whiteboard.md)）。

## Choose the edit path before mutating

1. Read current native nodes and save an immutable raw snapshot and server preview. Identify collaborators' changes, node IDs, group relationships, attached connector endpoints and authorized edits.
2. Prefer native local editing for label and branch changes. Verify the complete replacement path, including deletion, before creating replacement nodes; creation alone does not establish that cleanup can succeed.
3. Treat raw export as evidence and a recovery snapshot, not an automatically valid import payload. Inspect image tokens, parent/child references and connector attachments before replay. Inline SVG in an image node is source content, not an uploaded image token. After a failed import, inspect the live board before retrying; it may be unchanged, partially written or already correct.
4. If a write succeeded and only its following export failed, inspect return code and stderr, then retry that read separately. Do not replay the mutation to obtain a missing receipt. For a temporary “whiteboard not ready” response, wait 2–3 seconds and retry the export; use a preview output path without an extension so a JPEG response is not rejected as a PNG mismatch. A successful first preview may still be stale or blank: compare with current raw nodes and re-export before judging the write.
5. Use a source-based rebuild only within an already authorized scope. Reuse maintained source when it still represents the live board; otherwise reconstruct only node types whose geometry and styling can be faithfully mapped. Do not flatten unsupported collaborative objects silently.

## Geometry-preserving SVG reconstruction

Use this fallback for simple boards composed of rectangles, native text, orthogonal connectors and inline-SVG arrow images. It is not a reason to redesign an existing board.

1. Sort current nodes by z-order and preserve position, size, colors, line width, corner radius and dash pattern. Use live geometry rather than a stale generator.
2. Map rectangles to SVG rectangles. Keep each text node's font, weight, color and alignment. Native text coordinates describe a box while SVG uses a baseline; derive the baseline from matching live text/source geometry and verify visually rather than treating a numeric offset as universal.
3. Resolve an attached endpoint from the current target shape and relative attachment position. When connector turning points are relative to the connector bounding box, add its x/y offsets to recover absolute waypoints. Preserve explicit start and end coordinates.
4. For an inline-SVG arrow image, preserve its viewBox and translate the inner content by the difference between native image position and viewBox origin. Do not use the literal SVG string as an uploaded-image token.
5. When removing a branch, inventory its card, title/body text, branch label, connector and arrowhead. Remove all and only that branch's components, then check remaining connector attachments for references to deleted objects.
6. Render and inspect the revised SVG locally. Preserve the approved square-card style and omit additional explanation panels. Publish once using the authorized SVG path, then export live raw nodes and the actual server preview.
7. Verify changed labels and branches, unchanged geometry outside scope, remapped connector relationships and absence of duplicates. Compare actual previews as well as counts. If raw is current but the preview is stale, re-export the preview without repeating the write.

## Native text consolidation and typography verification

1. Build a node inventory containing text, font size, bounding box, parent group, backing shape and connector relationships. Match split fragments by sentence meaning and geometry rather than merging all text in a card.
2. Keep one surviving text node per complete sentence/title, assign the full string without layout newlines, and set its width slightly inside the backing shape. Remove redundant text nodes and their IDs from group children; preserve independent field rows and all business connectors.
3. Use integer `font_size` values in native payloads. Apply the `SKILL.md` / `style-tokens.md` hierarchy consistently, then enlarge boxes and move following content to fit natural wrapping. When moving geometry, update group bounds, separators, bullets, connector endpoint positions and relative turning points together; moving a box alone does not move every explicit connector coordinate.
4. After writing, compare normalized full-text inventories and connector counts/relationships with the intended result. Verify titles, descriptions and line labels by category against their required font sizes, and ensure no sentence is still divided across multiple text nodes. Inspect the actual server preview for wrapping, clipped labels and collisions; node-count equality alone is not visual verification.

Keep recovery artifacts separate from revised sources. Report any unverified visual or semantic difference instead of claiming full preservation.
