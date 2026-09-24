# 深度笔记——《LLM 智能体评估综述》

**作者：** Asaf Yehudai、Lilach Eden、Alan Li、Guy Uziel、Yilun Zhao、Roy Bar-Haim、Arman Cohan、Michal Shmueli-Scheuer（IBM Research / Hebrew University / Yale） · **URL：** https://arxiv.org/abs/2503.16416 · **类型：** 论文 · **已找到原文：** 是

## 摘要

本文自称首份 LLM 智能体评估方法综合综述，把快速变化且碎片化的领域整理为一张地图。它从五个视角分析：智能体工作流所需核心 LLM 能力（规划、工具、自反思、记忆）；应用专项基准（网页、软件工程、科学、对话）；通用智能体评估；智能体基准的共通核心维度（数据、环境、接口、指标、安全）；以及面向开发者的框架与工具。核心诊断是，评测正转向更真实、更困难、持续更新的“实时”基准，但成本效率、安全与稳健、细粒度和可扩展评估仍有缺口。论文作为活文档，在 v2/v3 加入 GAIA2、SWE-bench Pro、τ²-Bench、HAL 等新基准，适合定向入门，而非新方法。

## 要点

- **五视角分类**区分测什么能力、在哪个应用域测，以及基准如何构建。
- **能力基准。** 规划含 PlanBench、FlowBench；工具使用含 ToolBench、BFCL v1–v3、NESTFUL、ComplexFuncBench；自反思含 LLF-Bench、LLM-Evolve；记忆含 MemGPT、StreamBench、MemBench。
- **应用基准。** 网页智能体包括 Mind2Web、WebArena、VisualWebArena、WebVoyager、WorkArena、Online-Mind2Web、ST-WebAgentBench；软件工程包括 SWE-bench 及 Verified、Lite、Multimodal、Java 变体，还有 SWT-bench、TDD-bench、Terminal-Bench、SWE-Lancer；科学类有 SciCode、ScienceAgentBench、CORE-Bench、PaperBench；客服对话有 τ-Bench、τ²-Bench、IntellAgent、ALMITA。
- **通用智能体**包括 GAIA、OSWorld、AppWorld、AgentBench 与 Holistic Agent Leaderboard。
- **核心维度最可复用。** 把基准抽象为数据整理、环境与接口、指标和安全等正交轴，可从结构而非排行榜分数比较。
- **趋势。** 静态基准很快过时、饱和并被放弃，因此出现持续更新、更真实、更对抗的实时基准。
- **缺口一：成本效率。** 现有评估偏重任务成功，忽略 token、API 成本和延迟，效率应成为一等指标。
- **缺口二：安全稳健。** 安全、可信度和策略遵循不足，尤其不利于企业部署。
- **缺口三：粒度。** 粗粒度端到端成功率不能定位工具选择、推理或恢复发生的故障，应做过程和逐步评估。
- **缺口四：裁判扩展性。** 静态人工标注是瓶颈，LLM 裁判和“智能体裁判”虽不完美但更可扩展。
- **框架层。** 综述 LangSmith、Langfuse、Arize、Galileo、Patronus AI、W&B Weave、Vertex AI、AutoGen，连接学术基准与生产可观测栈。

## 已核验引述（中文翻译）

- “本文首次全面综述这些能力日益增强的智能体的评估方法。”——https://arxiv.org/abs/2503.16416
- “我们还识别了未来必须解决的关键缺口，尤其是成本效率、安全和稳健，以及细粒度、可扩展评估。”——同上
- “当前基准没有充分关注安全、可信度和策略合规。”——https://arxiv.org/html/2503.16416v2
- “许多基准依赖粗粒度端到端成功指标，无法诊断具体智能体故障。”——同上
- “当前评估常优先性能，忽视成本与效率测量。”——同上
- “静态基准很快过时、饱和并被放弃……我们看到‘实时’基准兴起。”——同上

## 价值与贡献

本文不是基准清单，而用五视角和核心维度提供坐标系，可安放任何新基准；明确的成本、安全、粒度和裁判扩展性缺口也构成研究路线图。它把 SWE-bench、GAIA、τ-Bench 等学术基准与 LangSmith、Langfuse、Galileo 等生产工具放在同一综述中，并随版本更新。最适合作为入门定向和优质智能体评测检查清单；作为综述必然广度优先，只指向基准，较少深入批判各自构念效度。

## 主题

9 智能体专项 · 1 为什么需要评测 · 6 基准与评测 / 完整性 · 5 评测基础设施 · 8 裁判 / 验证器 · 10 安全 / 对抗 · 2 评测⇄能力
