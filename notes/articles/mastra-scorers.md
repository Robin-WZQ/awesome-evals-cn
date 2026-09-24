# 深度笔记——《Mastra Scorers 发布》

**作者：** Yujohn Nattrass（Mastra 软件工程师） · **URL：** https://mastra.ai/blog/mastra-scorers · **类型：** 工程博客 · **已找到原文：** 是

## 摘要

TypeScript 智能体框架 Mastra 用新原语 **Scorers** 替代旧 `evals` API。Scorer 是可组合函数，在智能体或工作流步骤响应后异步运行，输出归一化的 **0–1 质量信号和原因字符串**。核心洞见是 LLM 无法稳定直接生成数值，所以 Mastra 把评分拆为结构化提取和确定性 `generateScore`：LLM 输出结构数据，再由代码映射为分数。四步流水线是 `preprocess`→`analyze`→`generateScore`→`generateReason`，只有 `generateScore` 必需；底层复用 Mastra 工作流引擎，自动获得异步执行、错误处理与重试。Scorer 可通过 `sampling.rate` 附着到智能体并抽样线上流量，结果写入 `mastra_scorers` 表和 Playground 的 Scorers 页签。因此它不仅是离线评测，也是在线评测与可观测性方案。

## 要点

- **命名体现设计。** 团队认为 evaluator 过于学术，选择 scorer，因为“它做的就是评分”，强调实践和去术语化。
- **不要让 LLM 直接给数字。** 同一模型五次给 0–1 分会出现五个数字；应让 LLM 输出结构化判断，再由确定性 `generateScore` 映射为 0–1。
- **四阶段流水线。** `preprocess` 做数据准备，`analyze` 执行评测逻辑，`generateScore` 确定性打分，`generateReason` 生成人可读解释。只有打分必需，可从单行规则扩展到完整 LLM 裁判。
- **三类 scorer。** 模型评分、规则或确定性评分、统计评分统一返回 0–1，因而可互换比较。
- **异步不阻塞。** 响应返回后再评分，不进入请求关键路径。
- **线上抽样。** `sampling: { type: "ratio", rate: 0.5 }` 表示评分一半流量；1 为全部。它是控制线上 LLM 裁判成本和覆盖率的旋钮。
- **内置 Bias 与 Answer Relevancy scorer，** 可指定裁判模型，如 `createAnswerRelevancyScorer({ model: openai("gpt-4o") })`。
- **用自家产品实现。** 每个评分阶段本身就是 Mastra 工作流步骤，因此复用异步、错误处理和重试。
- **持久化与界面。** 配置存储后自动写入 `mastra_scorers`；`mastra dev` 中的 Playground 页签自动展示结果。
- **迁移。** 旧 `evals` 包装为 `createScorer`，核心逻辑不变；安装命令为 `pnpm add @mastra/core@latest @mastra/evals@latest`。
- **尚缺金标准答案。** 参考答案对比仍在路线图中，当前主要是相关性和偏差等无参考评分。

## 已核验引述（中文翻译）

1. “LLM 很不擅长稳定生成数值分数——让同一模型五次给 0–1 分，会得到五个不同数字。因此我们让 LLM 输出结构化数据，再用确定性 `generateScore` 函数转成数字。”——https://mastra.ai/blog/mastra-scorers
2. “每个 scorer 都会在智能体响应后异步运行，在不阻塞响应的情况下评估输出。”——同上
3. “所以我们选择‘scorers’，因为它们做的就是评分。”——同上
4. “我们使用 Mastra 工作流运行评分流水线。每个步骤……都是工作流步骤，因此免费获得异步执行、错误处理和其他工作流能力。”——同上

## 价值与贡献

“LLM 结构化判断+代码确定性打分”是最可迁移的洞见，它把模糊判断和可复现数值分离，直接处理裁判方差。统一 0–1 协议也说明 TypeScript 智能体生态正在独立收敛到模型、规则、统计评分三分法。抽样率与响应后异步设计把重点放到生产在线评估，并明确提供成本旋钮。最小接口只要求 `generateScore`，让正则规则和多步裁判共享抽象；但参考答案评分尚未交付。

## 主题

1 为什么需要评测 · 3 模型 / 工具链 / 技能 · 4 可观测性 · 5 评测基础设施 · 8 裁判 / 验证器 · 9 智能体专项
