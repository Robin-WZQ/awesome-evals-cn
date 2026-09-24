# Scan：自主审计与更新工作流

本仓库可以自动维护自身。**Scan** 是一个定期运行的 [Claude Code](https://docs.anthropic.com/en/docs/claude-code) 工作流：它会发现新的高质量智能体评测内容，根据严格的“不吹不黑”标准审查，并创建包含新条目的 PR。它会**按计划在云端运行**（GitHub Actions），也可以先在本地试用。

## 它做什么

1. **读取** `README.md` 和 `CONTRIBUTING.md`；收集已列出的每个 URL，建立去重集。
2. **发现**新来源：并行检索已知作者、公司工程博客、评测播客、新基准/工具，以及**一般智能体演讲/文章中对评测的提及**。
3. **审查**每个候选项，使用严格判断标准：只接受真实数据/代码/方法/洞见，拒绝 SEO、营销文、单薄回顾和重复条目。**存疑就不收录。**
4. **整合**通过审查的项目，将其作为可点击、有注释的条目放到正确章节。
5. **创建 PR**：创建 `scan/<date>` 分支、提交（不附加 AI 归属），并创建逐项说明收录理由的 PR。绝不直接推送到 `main`。

门槛与 [CONTRIBUTING.md](CONTRIBUTING.md) 中完全相同：**展示实际工作；用一句话解释“为什么”；验证 URL；清理失效项；质量高于数量。**

## 云端设置（使用你的 Claude 订阅）

GitHub Action 位于 [`.github/workflows/eval-scan.yml`](.github/workflows/eval-scan.yml)，每日 08:23 UTC 运行，也支持手动 `workflow_dispatch`。它使用 **Claude 订阅 OAuth 令牌**认证，无需按量计费的 API 密钥。

**一次性设置：**

```bash
# 1. 生成订阅令牌（会打开浏览器，使用你的 Pro/Max 计划）
claude setup-token

# 2. 将其添加为仓库密密
gh secret set CLAUDE_CODE_OAUTH_TOKEN -R Robin-WZQ/awesome-evals-cn
#   在提示时粘贴第 1 步得到的令牌

# 3. 立即测试（无需等待计划时间）
gh workflow run "Eval Scan (audit + update)" -R Robin-WZQ/awesome-evals-cn
#   可选聚焦范围：-f focus=podcasts
```

Scan 会创建由你审查和合并的 PR，因此自主运行不会悄然改动主清单。

## 在本地试用

在信任云端工作流之前，先在本机运行相同闭环：

```bash
# 使用 Scan 提示以无头模式运行 Claude Code（只打印它打算添加的内容）
claude -p "$(sed -n '/Follow the workflow/,/curation, not volume/p' SCAN.md)"
```

也可直接运行源项目中的研究/审查工作流（见 `desearch/scripts/`），并在合并前检查 PR 差异。
