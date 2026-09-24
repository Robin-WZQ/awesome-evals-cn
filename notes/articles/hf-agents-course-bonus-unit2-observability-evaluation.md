# 笔记——《AI 智能体可观测性与评测（奖励单元 2）——Hugging Face 智能体课程》

**作者：** Hugging Face（智能体课程团队，与 Langfuse 联合开发）· **网址：** https://huggingface.co/learn/agents-course/en/bonus-unit2/introduction · **类型：** 课程 · **已找到：** 是

## 摘要（3–6 句）
这门免费 Hugging Face 智能体课程的奖励单元是一套以 notebook 实操为主的教程，讲解如何对生产智能体进行插桩、监控和评测。它先通过 OpenInference 的 `SmolagentsInstrumentor` 在 `smolagents` 中接入基于 OpenTelemetry 的追踪并发送至 Langfuse，再监控 token 成本、延迟与错误追踪。随后介绍两种互补评测：在线评测采集真实用户的赞／踩反馈，并用大模型裁判近实时评估毒性与正确性；离线评测则以整理好的基准数据集为准，将 GSM8K 数学题上传为 Langfuse 数据集，横向比较模型与工具配置。课程明确面向准备把智能体交给用户的实践者，强调成本、准确率和延迟的权衡，以及生产信号与离线回归测试之间的反馈闭环。理论不深，但工程步骤具体，是一套可运行的端到端流程。

## 要点
- **插桩栈完整且可运行。** 安装 `langfuse`、`smolagents[telemetry]`、`openinference-instrumentation-smolagents`、`datasets`、`smolagents[gradio]` 和 `gradio`；一行 `SmolagentsInstrumentor().instrument()` 即可自动捕获每次大模型调用和工具调用的嵌套 span，基本路径无需手工布线。
- **Langfuse 通过 OTLP 充当后端。** 使用 `LANGFUSE_PUBLIC_KEY`、`LANGFUSE_SECRET_KEY`、`LANGFUSE_HOST` 和用于 HF 推理的 `HF_TOKEN` 鉴权，再以 `get_client()` 与 `auth_check()` 验证连接。这种 OpenTelemetry 到厂商后端的模式可迁移到 Langfuse 之外。
- **以工程方式定义 trace 与 span。** 一条 trace 是从开始到结束的完整智能体任务，例如处理一次用户请求；span 是其中某个步骤，例如调用语言模型或检索数据。
- **明确的指标分类：** 延迟要同时测整项任务和单个步骤；成本要判断额外调用是否换来足够边际收益；请求错误需要跨供应商回退与重试；用户反馈包括显式评分／评论和隐式改写／重试；准确率则要求先定义成功标准。
- **在线评测的用户反馈路径：** Gradio 聊天界面采集赞／踩，映射为 1／0 分，并用 `langfuse.create_score()` 记录。这是最廉价的生产信号，课程展示了完整接线方式。
- **在线评测的大模型裁判路径：** 独立评测模型近实时地评价智能体输出的毒性或正确性，再把结果写回 Langfuse 分数；裁判模板基于提示词，并随在线流量运行。
- **丰富追踪以便切片：** 通过 `span.update_trace()` 给 span 添加 `user_id`、`session_id`、标签和元数据，从而按用户或会话调试。
- **GSM8K 离线评测是亮点。** 从 HF 加载带标准答案的小学数学数据集，通过 `create_dataset()` 创建 Langfuse 数据集，以 `create_dataset_item()` 添加输入与期望输出，再在 `item.run()` 上下文管理器中把每次智能体运行与样本关联，由此获得可重复的准确率指标。
- **离线评测重点是配置比较：** notebook 让多种模型／工具配置在同一数据集上运行，例如无工具的 `CodeAgent(tools=[])` 与添加 `DuckDuckGoSearchTool` 的版本。`run_smolagent()` 辅助函数在 `start_as_current_generation()` span 中封装执行。
- **明确的线上与线下闭环：** 离线评测给出可重复的标准答案准确率，在线评测捕获实验室遗漏的意外情形和模型漂移；成功的评测体系应在持续迭代中结合两者。

## 已核验引述（中文翻译）
- “可观测性是通过日志、指标和追踪等外部信号，理解 AI 智能体内部正在发生什么。”——https://huggingface.co/learn/agents-course/en/bonus-unit2/what-is-agent-observability-and-evaluation
- “Trace 表示从开始到结束的一项完整智能体任务，例如处理一次用户请求。”——同上
- “Span 是 trace 中的单个步骤，例如调用语言模型或检索数据。”——同上
- “这意味着在受控环境中评测智能体，通常使用测试数据集，而不是真实用户请求。”（离线评测）——同上

## 它带来了什么／为什么值得读
多数智能体评测文章停留在概念层面，而本单元提供了一条可直接照做的端到端流水线：真实包名、环境变量、单行插桩、Gradio 反馈闭环、连接到评分的大模型裁判，以及装入数据集实体并用 `.run()` 工具比较配置的 GSM8K 基准。对于第一次建立智能体可观测性的团队，它能把“应该追踪智能体”迅速变成可按用户切片查看追踪、成本和准确率的仪表板。它还正确刻画了可观测性与评测的关系：追踪是底座，在线裁判与反馈是廉价但嘈杂的生产信号，带标准答案的离线基准是可重复的回归门槛，两者必须结合。OpenTelemetry 也使这些技能可迁移到任意 OTLP 后端。局限是课程带有 Langfuse 与 smolagents 的厂商色彩，较少讨论裁判可靠性、统计严谨性或裁判与人工的一致性；它教的是工程接线，而非可信判断的科学。

## 主题
1 为什么要评测 · 4 可观测性 · 5 评测基础设施 · 6 基准与评测 · 8 裁判／验证器 · 9 智能体专项
