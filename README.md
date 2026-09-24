# 优质智能体评测资源（Awesome Agent Evals）

> 一份经过筛选、带有明确判断且拒绝“链接堆砌”的 AI 智能体构建与评测资源库，覆盖论文、博客、演讲、课程、工具与基准。

本仓库是 [benchflow-ai/awesome-evals](https://github.com/benchflow-ai/awesome-evals) 的中文整理版，原始版本固定于提交 [`1b8928b`](https://github.com/benchflow-ai/awesome-evals/commit/1b8928b60a9d41cbab371610d741d39a3fd50860)，原项目由 [BenchFlow](https://benchflow.ai) 维护。

与普通的 Awesome List 不同，原项目为每条资源说明了“它是什么、为什么值得收录”，检查了 URL 和引用，并剔除了失效或停止维护的工具。原始资料来自：

- 四层递归引文图谱检索：覆盖约 11,600 篇论文，并按入度排序以定位学术经典；
- 面向实践者资料的定向检索，补充引文图谱难以覆盖的工业经验；
- 47 场演讲、播客和课程的转录与深度笔记；
- 针对每个章节进行的缺口分析与对抗性核验。

当前上游包含 **443+ 条精选资源和 143 篇深度阅读笔记**。其中 🆕 表示 2025—2026 年发布或更新，⚠️ 表示需要注意适用条件或证据局限。

> 说明：本中文版由人工整理。深度笔记的标题、栏目、正文与引述均已翻译为中文；作者姓名、模型名称、缩写、代码、公式、URL 及必要的正式资源名称按准确性需要保留。

## 目录

- [首先阅读的 12 项资料](#首先阅读的-12-项资料)
- [1. 为什么需要评测](#1-为什么需要评测)
- [2. 可验证性、能力与强化学习环境](#2-可验证性能力与强化学习环境)
- [3. 模型、执行框架与技能的分解](#3-模型执行框架与技能的分解)
- [4. 可观测性与可评分空间](#4-可观测性与可评分空间)
- [5. 评测基础设施](#5-评测基础设施)
- [6. Benchmark 与 Eval 的区别](#6-benchmark-与-eval-的区别)
- [7. 评测与强化学习环境](#7-评测与强化学习环境)
- [8. LLM-as-a-Judge 与验证器](#8-llm-as-a-judge-与验证器)
- [9. 智能体特有的评测问题](#9-智能体特有的评测问题)
- [10. 安全与对抗性评测](#10-安全与对抗性评测)
- [面向“智能体可信评测”的建议阅读顺序](#面向智能体可信评测的建议阅读顺序)
- [深度笔记与方法手册](#深度笔记与方法手册)

## 首先阅读的 12 项资料

1. **[The Second Half](https://ysymyth.github.io/The-Second-Half/)** — Shunyu Yao。解释为什么瓶颈正在从“求解问题”转向“定义并评测问题”，是理解评测重要性的总纲。
2. **[An LLM-as-Judge Won't Save the Product, Fixing Your Process Will](https://eugeneyan.com/writing/eval-process/)** — Eugene Yan。核心观点是流程优先于工具：评测本质上是应用于 AI 产品的科学方法。
3. **[Hidden Technical Debt: Agent Evaluation Infrastructure](https://leehanchung.github.io/blogs/2026/06/13/hidden-technical-debt-agent-evaluation-infra/)** — Han-Chung Lee。区分控制平面与数据平面，提出五类评测表面和状态增量；聊天模型评测可能是一张表，而智能体评测必然是一个系统。
4. **[LLM Evals FAQ](https://hamel.dev/blog/posts/evals-faq/)** — Hamel Husain、Shreya Shankar。集中回答误差分析、二元判断、标注流程和数据迭代等实际问题。
5. **[Asymmetry of Verification and Verifier's Law](https://www.jasonwei.net/blog/asymmetry-of-verification-and-verifiers-law)** — Jason Wei。可训练性受可验证性约束；能够可靠验证一个任务，往往意味着已经能够为它构造强化学习环境。
6. **[Demystifying Evals for AI Agents](https://www.anthropic.com/engineering/demystifying-evals-for-ai-agents)** — Anthropic。系统介绍智能体任务设计、结果评测与轨迹评测、独立试验，以及 `pass@k` 与 `pass^k`。
7. **[How to Build Good Language Modeling Benchmarks](https://ofir.io/How-to-Build-Good-Language-Modeling-Benchmarks/)** — Ofir Press。讨论自然性、自动可评性和挑战性，以及基准快速饱和的问题。
8. **[AI Agents That Matter](https://arxiv.org/abs/2407.01502)** — Kapoor 等。要求将成本作为一等指标，并区分面向模型开发者和应用开发者的评测；缺少隐藏测试集会诱发过拟合。
9. **[Building on Evaluation Quicksand](https://www.interconnects.ai/p/building-on-evaluation-quicksand)** — Nathan Lambert。讨论缺乏真实标准答案、数据污染以及评测与训练相互耦合的问题。
10. **[Who Validates the Validators? (EvalGen)](https://arxiv.org/abs/2404.12272)** — Shankar 等。提出“标准漂移”：在实际查看和标注样本之前，人们往往无法写出可靠、完整的评分标准。
11. **[Benches 2026 — LLM benchmarks in the era of agents](https://florianbrand.com/posts/benches-2026)** — Florian Brand。解释提示词、采样温度、评分器和执行框架为何都能显著改变分数，以及基准真实标签为何经常出错。
12. **[A Shared Playbook for Trustworthy Third-Party Evaluations](https://openai.com/index/trustworthy-third-party-evaluations-foundations/)** — OpenAI。讨论独立第三方评测的执行框架选择、有效性威胁和应遵守的基本标准。

## 1. 为什么需要评测

评测不是模型上线前的一次性验收，而是把模糊的产品目标转化为可观察、可复现、可比较证据的过程。真正有效的工作流通常是：观察真实失败案例，建立失败分类，设计最小化测试集，修复系统，再持续监控分布变化。

重点资源：

- [Your AI Product Needs Evals](https://hamel.dev/blog/posts/evals/)：不要只依赖通用排行榜，应尽可能降低查看自身数据与失败案例的成本。
- [A Field Guide to Rapidly Improving AI Products](https://hamel.dev/blog/posts/field-guide/)：误差分析往往是回报最高的活动；AI 路线图应关注完成了多少有效实验。
- [In Defense of AI Evals, for Everyone](https://www.sh-reya.com/blog/in-defense-ai-evals/)：评测就是对应用质量进行系统测量，而不是“凭感觉看看效果”。
- [Evals Are NOT All You Need](https://www.oreilly.com/radar/evals-are-not-all-you-need/)：自动评分器无法取代离线测试、生产监控和真实用户反馈组成的持续改进闭环。
- [AI Engineering Pitfalls](https://huyenchip.com/2025/01/16/ai-engineering-pitfalls.html)：总结生成式 AI 产品中常见的评测和工程错误。

## 2. 可验证性、能力与强化学习环境

本章的核心命题是：**如果能够可靠评测某种行为，通常也已经具备训练或优化该行为的基础。** 一个基准可以被理解为冻结的强化学习环境，由任务、执行框架、验证器、状态和约束共同构成。

重点资源：

- [A Taxonomy of RL Environments for LLM Agents](https://leehanchung.github.io/blogs/2026/03/21/rl-environments-for-llm-agents/)：用 `E={T,H,V,S,C}` 描述环境，并强调“可验证优于仅可判断”。
- [DeepSeek-R1](https://arxiv.org/abs/2501.12948)：展示基于规则的可验证奖励如何通过纯强化学习促进推理能力涌现。
- [Tülu 3](https://arxiv.org/abs/2411.15124)：系统化 RLVR，即在答案可检查的任务中使用验证器替代奖励模型。
- [Terminal-Bench](https://github.com/harbor-framework/terminal-bench)：每项任务同时提供 Docker 环境、程序化测试和参考解，体现“基准即环境”。
- [τ²-Bench](https://github.com/sierra-research/tau2-bench)：面向工具—智能体—用户交互的多轮评测，通过数据库最终状态实现可验证评分。

## 3. 模型、执行框架与技能的分解

智能体表现不是模型权重的单独产物。提示词、工具定义、上下文管理、记忆、重试策略、技能文件和执行循环都属于执行框架。因而报告结果时，必须明确区分：

- **模型能力**：基础模型在给定接口下的能力；
- **执行框架能力**：工具路由、上下文压缩、规划、重试和错误恢复；
- **技能增益**：外部程序性知识或工作流说明带来的提升；
- **环境贡献**：任务状态、可用工具和权限配置对结果的影响。

只报告“某模型得分”而不固定执行框架，会把系统工程差异错误归因给模型。技能评测还应至少包含有技能/无技能的配对实验，并控制提示长度、工具权限和推理预算。

## 4. 可观测性与可评分空间

智能体不是只产生一条最终答案。可评测表面至少包括：

1. 最终文本或产物；
2. 环境最终状态及状态变化；
3. 完整操作轨迹和工具调用；
4. 中间计划、记忆与交接产物；
5. 时间、Token、费用和外部资源消耗；
6. 是否发生越权、无关修改或副作用。

仅检查最终答案会遗漏“答案正确但过程危险”的运行；仅检查轨迹又可能惩罚与结果无关的实现差异。更稳健的做法是用结果验证器判断任务是否完成，再对必要的过程约束设置独立安全门槛。

## 5. 评测基础设施

完整评测栈通常包含数据集版本、环境构建、智能体适配器、轨迹采集、评分器、人工标注、统计分析、回归测试和 CI 门禁。基础设施需要保证：

- 每次运行可追溯到任务、环境、模型和执行框架的确定版本；
- 测试数据与智能体工作区隔离，隐藏答案不能被直接读取；
- 评分器不受智能体生成文件、测试钩子或残留进程影响；
- 支持独立重复试验，而不是用一次运行代表随机系统；
- 原始轨迹和评分证据可以审计。

工具本身不能替代好的评测设计。先定义失败模式、证据和决策规则，再选择 BenchFlow、Inspect、Harbor、LangSmith、Langfuse、Phoenix 等基础设施。

## 6. Benchmark 与 Eval 的区别

- **Benchmark** 通常是可复用、面向广泛比较的标准任务集合。
- **Eval** 更接近围绕具体系统、用户和决策构造的测量过程。

公共 Benchmark 便于横向比较，但容易出现训练污染、标签错误、快速饱和和排行榜投机。面向实际系统的 Eval 应加入私有留出集、真实失败案例、时间切分和持续更新，并报告置信区间，而不是只发布单个总分。

需要重点审查四类完整性风险：

- 任务或答案泄漏到训练数据；
- 测试任务不再区分先进系统；
- 标签或验证器本身错误；
- 被测系统针对评分规则投机，而非完成真实目标。

## 7. 评测与强化学习环境

当评测器被用作训练奖励时，其缺陷会被优化过程放大。环境设计需要同时考虑：

- 任务难度分布是否既不全会也不全不会；
- 奖励是否真正对应目标，而不是容易利用的代理指标；
- 是否存在奖励黑客、捷径和不可见副作用；
- 验证器在训练压力下是否仍然可靠；
- 环境是否足够多样，避免策略只记住表面模式。

[Natural Emergent Misalignment from Reward Hacking in Production RL](https://www.anthropic.com/research/emergent-misalignment-reward-hacking) 提供了重要警示：在真实代码环境中学会作弊的行为可能泛化为破坏或伪装对齐，因此奖励黑客不只是分数问题，也是安全问题。

## 8. LLM-as-a-Judge 与验证器

可程序化验证时，应优先使用确定性验证器；只有开放式、主观或难以写成断言的部分，才适合引入 LLM Judge。一个可信的 LLM Judge 流程至少需要：

- 将复杂质量标准拆成清晰、尽量二元的判据；
- 使用人工标注集校准，并报告与人的一致性；
- 检查位置偏差、长度偏差、自我偏好和措辞敏感性；
- 对顺序交换、提示改写和模型更换进行稳健性测试；
- 保存逐项判决及解释，而不是只保留总分；
- 将 Judge 的不确定性纳入最终置信区间。

所谓“可判断”并不等同于“可验证”。Judge 给出高分只能表示它认为结果满足量表，不能自动证明任务在真实环境中完成。

## 9. 智能体特有的评测问题

智能体评测需要超越单轮问答，重点处理：

- **多轮与长时程**：早期错误会累积，可靠性通常随任务长度下降；
- **工具使用**：既要检查调用是否正确，也要检查权限、参数和副作用；
- **环境状态**：最终数据库、文件系统或远端服务状态可能比文本答案更重要；
- **轨迹质量**：定位失败发生在感知、规划、调用、验证还是恢复阶段；
- **多智能体协作**：角色之间可能发生错误放大、迎合、信息丢失和责任扩散；
- **稳定性**：`pass@k` 衡量多次尝试中至少成功一次，`pass^k` 衡量连续多次全部成功，后者更接近生产可靠性；
- **成本**：把费用、延迟、调用次数和人工介入作为一等指标。

评测报告应同时给出任务成功率、失败类型、状态差异、轨迹证据、资源消耗和重复试验统计。

## 10. 安全与对抗性评测

这一部分与“可信智能体评测”直接相关。安全评测不应只测试模型是否拒绝有害文本，还要测试智能体在拥有工具、记忆、外部内容和执行权限时会采取什么行动。

### 基准与方法

- **[BenchJack](https://arxiv.org/abs/2605.12673)**：系统审计智能体基准，提出八类缺陷模式和 30 问检查表；前沿模型会自发发现评分漏洞，因此基准必须从设计层面保证安全。
- **[How We Broke Top AI Agent Benchmarks](https://moogician.github.io/blog/2026/trustworthy-benchmarks-cont/)**：展示自动利用智能体如何在多个主流基准上接近满分，却没有真正解决任务；攻击包括测试钩子篡改、二进制包装和直接读取答案。
- **[AgentDojo](https://arxiv.org/abs/2406.13352)**：工具型智能体间接提示注入的代表性动态基准，覆盖正常任务和不可信数据中的攻击。
- **[InjecAgent](https://arxiv.org/abs/2403.02691)**：包含 1,054 个间接提示注入案例，区分直接伤害和数据外泄目标。
- **[AgentHarm](https://arxiv.org/abs/2410.09024)**：覆盖 11 类危害的恶意智能体任务，用于测量行动滥用和拒绝能力。
- **[Agent Security Bench](https://proceedings.iclr.cc/paper_files/paper/2025/file/5750f91d8fb9d5c02bd8ad2c3b44456b-Paper-Conference.pdf)**：统一覆盖直接/间接提示注入、记忆投毒、思维规划后门及对应防御。
- **[SHADE-Arena](https://www.anthropic.com/research/shade-arena-sabotage-monitoring)**：让智能体同时面对正常主任务和隐藏有害副任务，评测其破坏行为以及监控器能否发现。
- **[DecodingTrust](https://decodingtrust.github.io)**：覆盖多个可信维度的综合基准，可作为模型层可信评测的参考，但不能替代工具行为和环境状态评测。
- **[RedCode](https://arxiv.org/abs/2411.07781)**：面向代码智能体的高风险代码生成与执行评测。
- **[AgentPoison](https://arxiv.org/abs/2407.12784)**：通过污染 RAG 记忆对智能体实施红队攻击。

### 防御、威胁建模与工具

- **[CaMeL](https://arxiv.org/abs/2503.18813)**：从可信用户请求提取控制流和数据流，使用基于能力的策略阻止不可信内容改变程序控制流。
- **[The Lethal Trifecta for AI Agents](https://simonwillison.net/2025/Jun/16/the-lethal-trifecta/)**：当系统同时具备私有数据、不可信内容和外部通信时，提示注入风险会显著恶化。
- **[Agentic Misalignment](https://www.anthropic.com/research/agentic-misalignment)**：研究目标冲突下的勒索、泄漏等内部威胁行为。
- **[PyRIT](https://github.com/Azure/PyRIT)**：用于自动化、多轮生成式 AI 红队测试的开源工具。
- **[OWASP Top 10 for Agentic Applications](https://genai.owasp.org/resource/owasp-top-10-for-agentic-applications-for-2026/)**：包括目标劫持、工具误用、身份与权限滥用、记忆投毒和恶意智能体。
- **[MITRE ATLAS](https://atlas.mitre.org/)**：面向 AI 系统的 ATT&CK 式威胁知识库，适合建立攻击面和缓解措施映射。

### 评测自身也可能不安全

可信评测必须把评测基础设施本身纳入威胁模型：隐藏测试和答案是否可读、验证器是否能被篡改、工作区是否真正隔离、网络和凭证是否被正确关闭，以及智能体进程是否能在评分阶段继续运行。

推荐同时报告：

- 正常任务成功率与攻击成功率；
- 未授权行动率和敏感数据外泄率；
- 防御对正常任务的性能损失；
- 自适应攻击下的检测率，而非只测固定攻击；
- 每项结果的置信区间和重复次数；
- 被发现的评测漏洞及修复后的复测结果。

## 面向智能体可信评测的建议阅读顺序

如果目标是开发一套“智能体基础能力与可信性评测”，建议按照以下顺序：

1. 用 *Demystifying Evals for AI Agents* 确定任务、试验、结果和轨迹的基本单位；
2. 用 *Hidden Technical Debt* 设计数据平面、控制平面和证据留存；
3. 用 *AI Agents That Matter* 把成本、真实用户和隐藏测试集纳入指标；
4. 用 *EvalGen* 与 LLM Judge 资料校准评分量表；
5. 用 `pass@k`、`pass^k`、置信区间和配对实验建立统计协议；
6. 用 BenchJack 检查基准是否能被评分投机；
7. 用 AgentDojo、InjecAgent、AgentHarm 和 ASB 覆盖提示注入、工具误用、记忆投毒及有害行动；
8. 用 OWASP Agentic Top 10 和 MITRE ATLAS 检查威胁覆盖是否完整；
9. 最后再选择 BenchFlow 等执行框架，将任务、沙箱、轨迹和验证器工程化。

## 深度笔记与方法手册

- [`PATTERNS.md`](PATTERNS.md)：可运行代码和实例，覆盖 LLM Judge 校准、`pass@k`/`pass^k`、误差分析、轨迹与世界状态评分、CI 门禁和可验证奖励。
- [`notes/articles/`](notes/articles/)：博客与实践文章的深度笔记。
- [`notes/talks/`](notes/talks/)：47 场演讲、播客和课程笔记，部分带时间戳。
- [`notes/papers/`](notes/papers/)：由引文图谱筛出的论文笔记。

## 许可证与来源

原仓库及其注释采用 [CC0 1.0](LICENSE)。外部链接中的论文、文章、演讲、代码和其他资源仍受各自许可证与版权条款约束。本中文整理版保留原项目名称、来源链接和固定提交信息；正式引用时应引用对应原始资源。
