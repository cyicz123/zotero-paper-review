# Zotero 论文精读

`zotero-paper-review` 是一个 Agent Skill。它按 15 个问题阅读 Zotero 中的指定论文，核对原文、最接近的相关工作与复现材料，并将报告写入对应论文条目的子笔记。

## 安装

需要支持 `gh skill` 的 GitHub CLI，以及已配置、能访问 Zotero 库的 `zotero-cli`。本地模式需要启动 Zotero 并启用本地 API。安装技能后，在使用前运行 `zotero-cli config` 检查连接，并按 CLI 文档配置笔记写入权限。[zotero-cli 文档](https://github.com/cookjohn/zotero-cli)

```sh
gh skill install cyicz123/zotero-paper-review zotero-paper-review --agent codex --scope user
```

## 使用

```text
$zotero-paper-review <论文标题、DOI、arXiv ID 或 Zotero 条目 key>
```

技能优先读取 Zotero 的原文附件，按模板区分论文主张、实验结果和分析判断。写入后会读回笔记核对。用户明确要求只读总结时，报告在对话中交付。

[技能说明](skills/zotero-paper-review/SKILL.md) · [15 问模板](skills/zotero-paper-review/references/review-template.md)

## 许可证

[MIT](LICENSE)
