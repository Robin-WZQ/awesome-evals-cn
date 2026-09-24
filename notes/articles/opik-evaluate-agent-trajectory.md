# 笔记——《评估智能体轨迹》

**作者：** Comet / Opik 文档 · **链接：** https://www.comet.com/docs/opik/evaluation/evaluate_agent_trajectory · **类型：** 工具 · **已找到：** 是

## 摘要

这是一篇 Opik（Comet 的开源大模型可观测性平台）实操指南，它评估的不只是智能体的最终输出，而是其**轨迹**——为得到答案而经历的步骤、工具调用和中间决策序列。其核心论点是，轨迹评测可在系统进入生产之前，发现工具选择错误和低效推理路径。其实现是一套“跟踪优先”的流程：用 Opik 跟踪为智能体添加仪器；构建包含输入与期望工具调用的数据集；编写自定义指标，通过可选的 `task_span` 参数读取已捕获轨迹；再运行 `evaluate()` 为每个案例评分。页面提供两组可运行代码：通过 `OpikTracer` 支持 LangChain，通过 `@track`/`track_openai` 支持 OpenAI；还提供两个参考指标——考虑顺序的“严格工具遵循”和忽略顺序的“工具遵循”——它们递归遍历跨度树，提取实际使用的工具。配套页面还介绍了 `TrajectoryAccuracy` 大模型裁判指标，对 ReAct 的思考/动作/观测序列给出 0.0–1.0 分。这篇笔记的价值，是它为“跟踪仪器化→程序化、数据集驱动的轨迹评分”提供了具体桥梁。

## 要点

- **前提：只看输出不够。**评估智能体不只要检查最终输出，还需评估其**轨迹**，包括工具选择、推理链和中间决策。轨迹评测专门用于在上线前发现工具选择错误和低效推理路径。
- **跟踪是必要前提，而非可选项。**只有捕获了轨迹才能评估它。工作流要求先为智能体集成 Opik 跟踪：LangChain 使用 `OpikTracer` 回调；原生 OpenAI 使用 `track_openai()` 和 `@track` 装饰器。工具函数需使用 `@track(type="tool")` 标记，才会显示为工具跨度。
- **`task_span` 是核心原语。**自定义指标可接收可选的 `task_span` 参数（`SpanModel`），它会暴露本次运行的完整嵌套跨度层级。指标因此能进入真实执行轨迹，而不是只看输入/输出对；文档将它称为“这个指标的关键”。
- **通过递归遍历跨度树提取工具。**两个参考指标都实现 `find_tools()`，它递归遍历 `task_span.spans`，并在 `span.type == "tool"` 时收集 `span.name`。因此，工具检测基于跟踪跨度的结构，而非从文本中解析。
- **两种工具遵循指标。**“严格工具遵循”比较 `tool_used == expected_tool`，即列表相等，所以顺序和重复次数均有意义。“工具遵循”比较 `set(tool_used) == set(expected_tool)`，不考虑顺序。两者框架相同，只相差一行，清楚展示了评测严格程度是一项设计选择。
- **指标是普通 `BaseMetric` 子类，返回 `ScoreResult`。**每个指标返回 `value=1.0/0.0` 与人类可读的 `reason`（如 `f"Used {tool_used}, expected {expected_tool}"`），使用户可在界面中直接理解失败原因。
- **数据集格式刻意保持宽松。**条目可以是任意字典，例如 `{"input": "What is the weather in SF?", "expected_tool": ["get_weather"]}`。负例使用 `"expected_tool": []`：一个纯数学问题不应调用工具，从而测试过度使用工具。数据集条目格式很灵活，各条可包含任意字段。
- **运行闭环使用标准 Opik `evaluate()`。**定义一个调用智能体（回调中带跟踪器）的 `evaluation_task(dataset_item)`，并返回 `{"output": ...}`；随后向 `opik.evaluation.evaluate` 传入 `dataset`、`task`、`scoring_metrics=[StrictToolAdherenceMetric()]` 和 `project_name`。
- **Opik 2.0 强制项目作用域。**页面中的醒目提示警告，数据集与实验归属于项目；创建数据集和运行实验时必须传入 `project_name`，否则它们无法正确关联。
- **配套的大模型裁判指标 `TrajectoryAccuracy`。**指标页面说明，`TrajectoryAccuracy` 检查 ReAct 式智能体是否遵循“合理的思考、动作和观测序列，以实现指定目标”，并返回带理由的 0.0–1.0 分数。它接收 `goal`（字符串）、`trajectory`（由 `{thought, action, observation}` 字典组成的列表）和 `final_result`（字符串），默认裁判模型为 `gpt-5-nano`。它适用于“审计复杂工作流智能体和强化学习轨迹”。
- **结果分析与轨迹联动。**实验仪表板会为每个案例打分；点击某一行后，可通过 `Trace` 按钮查看智能体的完整执行轨迹。即分数和原始轨迹被联结到同一调试视图中。
- **提供 CI/CD 与框架接口。**页面链接到 CrewAI、LangGraph、OpenAI Agents 等框架的专用设置，以及 PyTest 集成，因此可在流水线中将轨迹评测作为门禁。

## 已核验引述（中文翻译）

> “评估智能体不只要检查最终输出。你需要评估**轨迹**——智能体为得到答案而经历的步骤，包括工具选择、推理链和中间决策。”—— https://www.comet.com/docs/opik/evaluation/evaluate_agent_trajectory

> “智能体轨迹评测可帮助你发现工具选择错误、识别低效推理路径，并在智能体行为进入生产之前对其进行优化。”—— https://www.comet.com/docs/opik/evaluation/evaluate_agent_trajectory

> “这个指标的关键是使用可选的 `task_span` 参数；它对所有自定义指标都可用，并可用于访问智能体轨迹。”—— https://www.comet.com/docs/opik/evaluation/evaluate_agent_trajectory

> “如果单击某个特定测试案例行，就可以使用 `Trace` 按钮查看智能体执行的完整轨迹。”—— https://www.comet.com/docs/opik/evaluation/evaluate_agent_trajectory

## 价值与优点

大多数“评估你的智能体”内容，都停留在用大模型裁判器评分最终答案。这个页面的特别之处，是它打通了**可观测性**与**评测**：指标直接读取真实的、已跟踪执行图（`task_span` → 嵌套跨度），而不是再次解析文本转录，所以工具使用检查落地于真实执行。两个几乎相同的指标（列表相等与集合相等）也是很好的教学设计：只用一行代码就把“轨迹检查应当多严格？”的决策具体化。数学问题的负例（`expected_tool: []`）展示了常被忽略的**过度用工具**失败，而不只是用错工具。页面也诚实地将仪器化置于首位：无法评估没有捕获的轨迹。配合 `TrajectoryAccuracy` 大模型裁判指标，该方案同时提供两个互补杠杆：便宜、确定性的结构检查（工具遵循），以及用于评估推理质量的分级裁判器；这是更正确的心智模型。对知识库来说，需注意这是厂商文档，所以完全围绕 Opik 原语组织；参考指标还从看似内部路径的 `opik.message_processing.emulation.models` 导入，它可能会随版本变动。

## 主题

- **9 智能体专属**——全文都关于智能体轨迹/工具使用评测。
- **4 可观测性**——跟踪仪器化（`OpikTracer`、`@track`、跨度层级）是基础；仪表板还将分数与轨迹链接。
- **8 裁判器/验证器**——`TrajectoryAccuracy` 是面向 ReAct 思考/动作/观测序列的大模型裁判器；工具遵循指标是确定性验证器。
- **5 评测基础设施**——数据集、实验、`evaluate()`、项目作用域与 PyTest/CI-CD 集成。
- **1 为什么需要评测**——开篇即阐明为何要用轨迹评测替代单纯的输出评测，以在上线前捕获错误。
- **7 强化学习环境（轻度）**——`TrajectoryAccuracy` 被明确定位用于“审计……强化学习轨迹”。
