---
name: zotero-paper-review
description: 按 15 个问题精读 Zotero 中的指定论文，核对原文、相关工作和复现证据，并把完整阅读报告写到对应条目的子笔记中。适用于要求 Zotero 论文精读并归档报告的请求。
---

# Zotero 论文精读

以 Zotero 中的论文标题、关键词、DOI、arXiv ID、条目 key、链接或已明确的选中条目为输入。输出一份 Markdown 论文阅读报告，并写成论文父条目下的子笔记。显式调用本技能即请求这套工作流。用户只要求只读总结或明确限制写入时，按该范围执行。报告默认用中文，保留论文、方法、指标与数据集的原名。

## 定位与阅读

1. 使用 `zotero-cli`，先运行 `zotero-cli config`。标题等线索可用 `zotero-cli --json search "<线索>" --limit 10`，必要时查其他库或用结构化字段确认 DOI。用 `get metadata <父条目KEY>` 和 `get children <父条目KEY>` 复核作者、标题、标识符及附件。若输入是附件，解析其父条目。发现多个无法区分的候选时，只问足以消除歧义的问题。
2. 以明确指定的版本或 Zotero 的原文附件为准。用 `zotero-cli --json read <PDF附件KEY> --start-page <N> --end-page <M>` 分段读摘要、引言、相关工作、方法、实验、局限及相关补充材料。对关键公式、图表、图例和脚注，必要时渲染原页面复核。标明实际读到的版本、日期和证据范围。附件不可读时可用核实过的公开原文，并说明版本是否有差异。只有摘要可用时，说明缺失并标记无法回答的问题。
3. 核对最接近的研究原文，比较任务定义、信息条件、方法和已验证的能力。用作者仓库或项目页核对公开代码、数据及复现入口。记录审阅过的提交或版本。发现论文和代码配置不一致时，标明两者的口径，说明能否确定论文图表实际使用的配置。研究不到的材料标成未核实，区分读文献、查源码和实际复跑。

## 15 问阅读模板

阅读论文前，读取 [15 问阅读模板](references/review-template.md)，并按模板原顺序撰写报告。

## 写入 Zotero 并核验

- 报告首行用 `# <论文简称> 论文阅读报告`，随后标明论文身份、阅读版本和来源。使用 Zotero 能呈现的 Markdown，例如标题、表格、代码块和链接。外部证据使用可点击的原始来源，使笔记离开聊天后仍能查证。文字中标明作者主张、实测结果和分析判断。
- 先用 `zotero-cli --json notes list --item-key <父条目KEY> --full --raw-html` 检查已有笔记。用正文标题、阅读版本和内容判断是否已有等同报告，兼容旧笔记的 `CLI Note` 默认标题。找到等同报告时复用并核验。用户要求更新既有报告时，再定位对应笔记。生成新版时按论文版本区分，保留既有笔记。
- 显式传入笔记标题，通过 UTF-8 stdin 传送完整 Markdown。PowerShell 示例中的 `$report` 是完整 Markdown 字符串，`$parentKey` 是已核实的父条目 key，`$noteTitle` 是报告名。

  ```powershell
  $previousEncoding = $OutputEncoding
  try {
      $OutputEncoding = [System.Text.UTF8Encoding]::new($false)
      $report | zotero-cli --json notes create --item-key $parentKey --title $noteTitle --text -
  } finally {
      $OutputEncoding = $previousEncoding
  }
  ```

- 从成功结果取 note key，并用 `zotero-cli --json notes list --item-key <父条目KEY> --full --raw-html` 读回。检查对应父条目、目标 note key、标题及全部 15 节和末尾内容。写入结果不明确时先查询再决定是否重试，避免创建重复笔记。
- 如果工具缺失或写入受阻，用可用且已授权的 Zotero 接口完成等价操作。仍无法写入时，交付完整 Markdown 并说明具体阻塞。完成后在聊天中简报论文名、笔记名和 note key。
