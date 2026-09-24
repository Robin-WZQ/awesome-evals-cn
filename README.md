# Awesome Agent Evals [![Awesome](https://awesome.re/badge.svg)](https://awesome.re)

> 一个经过策展、立场鲜明且**拒绝空话**的优质资源库，服务于**构建与评估 AI 智能体**，涵盖论文、博客文章、演讲、课程、工具和基准。

原始英文版本在[这里](https://github.com/benchflow-ai/awesome-evals)，本仓库仅提供一个中文翻译版本

大多数“awesome”列表只是链接堆积。本列表则**带注释且经过核验**：每个条目都说明*它是什么、为何值得收录*；URL 已检查，引述均经原文核验，失效或废弃工具会被清理，而不是悄悄留在列表中。本列表通过以下方式整理：

- 开展**深度为 4 的递归引用爬取**（1.16 万篇论文，按入度排序），以发现学术经典；
- 面向引用图容易遗漏的产业来源开展**定向实践者网络检索**（Eugene Yan、Han-Chung Lee、Hamel Husain、Shreya Shankar、Nathan Lambert 等）；
- 对 **47 场演讲与播客完成转录和深度笔记**（逐字稿 + 时间戳）；以及
- 对每个章节开展**缺口审计**并进行对抗式核验。

**443+ 个精选链接 · 143 篇深度阅读笔记**（见 [`notes/`](notes/)）。标记：🆕 = 2025–2026 年发布／更新 · ⚠️ = 注意事项。欢迎贡献——参见 [CONTRIBUTING](CONTRIBUTING.md)。

> 📘 **实践手册：** [**PATTERNS.md**](PATTERNS.md)——包含真实可运行代码和完整示例，涉及与人类对齐的 LLM-as-judge、pass@k/pass^k、错误分析、轨迹与世界状态评分、CI 门禁、可验证奖励等。

## 目录
- [📘 实践手册——真实代码与完整示例（PATTERNS.md）](PATTERNS.md)
- [⭐ 必读入门集（优先阅读）](#-must-read-starter-set-read-these-first)
- [1 · 为什么需要评测](#1-why-we-need-evals)
- [2 · “如果能评测，就已经构建出来”——评测 ⇄ 能力 ⇄ RL 环境](#2-if-you-can-eval-it-you-have-built-it-eval-capability-rl-environment)
- [3 · 模型／框架／技能分解](#3-the-model-harness-skill-decomposition)
- [4 · 可观测性与输出／评测空间（可评分的观测面）](#4-observability-the-output-eval-space-the-surfaces-you-can-grade)
- [5 · 评测基础设施（评测栈：数据集、评分器、线上／离线、追踪、CI）](#5-evaluation-infrastructure-the-eval-stack-datasets-scorers-onlineoffline-tracing-ci)
- [6 · 基准与评测（及基准完整性：污染、饱和、标签错误、排行榜操纵）](#6-benchmark-vs-eval-and-benchmark-integrity-contamination-saturation-label-errors-leaderboard-gaming)
- [7 · 评测与 RL 环境（验证器、奖励设计、难度校准、生命周期）](#7-evals-rl-environments-verifiers-reward-design-difficulty-calibration-lifecycle)
- [8 · LLM-as-judge 与验证器（对齐、偏差、可验证与可评判）](#8-llm-as-judge-verifiers-alignment-biases-verifiable-vs-judgeable)
- [9 · 智能体专用评估（轨迹、工具使用、多轮、世界状态、多智能体、定位）](#9-agent-specific-evaluation-trajectories-tool-use-multi-turn-world-state-multi-agent-localization)
- [10 · 安全／对抗评估（提示注入、越狱、行动授权、基准审计）](#10-safety-adversarial-evaluation-prompt-injection-jailbreaks-action-authorization-benchmark-auditing)
- [🎙 演讲、播客与幻灯片（已转录并做笔记）](#-talks-podcasts-slides-transcribed-noted)
- [💬 评测相关提及](#-eval-mentions)
- [公司与产业版图（评测／RL 环境市场）](#companies-landscape-eval-rl-environment-market)
- [来源与缺口说明](#notes-on-provenance-gaps)
- [深度笔记](#deep-notes)
- [贡献](#contributing)
- [许可证](#license)

---

<a id="-must-read-starter-set-read-these-first"></a>
## ⭐ 必读入门集（优先阅读）

1. **[The Second Half](https://ysymyth.github.io/The-Second-Half/)** — Shunyu Yao — <https://ysymyth.github.io/The-Second-Half/> · *博客* — “评估将比训练更重要。”领域层面的“为什么”。
2. **[An LLM-as-Judge Won't Save the Product, Fixing Your Process Will](https://eugeneyan.com/writing/eval-process/)** — Eugene Yan — <https://eugeneyan.com/writing/eval-process/> · *博客* — 流程优先于工具；把评测视为科学方法。
3. **[Hidden Technical Debt: Agent Evaluation Infrastructure](https://leehanchung.github.io/blogs/2026/06/13/hidden-technical-debt-agent-evaluation-infra/)** — Han-Chung Lee — <https://leehanchung.github.io/blogs/2026/06/13/hidden-technical-debt-agent-evaluation-infra/> · *博客* — 控制平面／数据平面、五个评测观测面、状态增量。“聊天评测是一张表格；智能体评测是一个系统。”
4. **[LLM Evals FAQ](https://hamel.dev/blog/posts/evals-faq/)** — Hamel Husain & Shreya Shankar — <https://hamel.dev/blog/posts/evals-faq/> · *博客* — 信息最密集的实操问答：错误分析、二元判断、仁慈独裁者式标注者。
5. **[Asymmetry of Verification and Verifier's Law](https://www.jasonwei.net/blog/asymmetry-of-verification-and-verifiers-law)** — Jason Wei — <https://www.jasonwei.net/blog/asymmetry-of-verification-and-verifiers-law> · *博客* — “验证能力 == 创建 RL 环境的能力。”
6. **[Demystifying Evals for AI Agents](https://www.anthropic.com/engineering/demystifying-evals-for-ai-agents)** — Anthropic — <https://www.anthropic.com/engineering/demystifying-evals-for-ai-agents> · *博客* — 智能体专用评测的最佳一手资料：任务设计、结果与轨迹、隔离试验、pass@k 与 pass^k。
7. **[How to Build Good Language Modeling Benchmarks](https://ofir.io/How-to-Build-Good-Language-Modeling-Benchmarks/)** — Ofir Press — <https://ofir.io/How-to-Build-Good-Language-Modeling-Benchmarks/> · *博客* — 自然、可自动评估、有挑战性；“-200%”难度目标；约一年即饱和。
8. **[AI Agents That Matter](https://arxiv.org/abs/2407.01502)** — Kapoor、Stroebl、Siegel、Nadgir、Narayanan — <https://arxiv.org/abs/2407.01502> · *论文* — 把成本作为一等指标；区分模型开发与应用开发；缺少留出集会滋生过拟合。
9. **[Building on Evaluation Quicksand](https://www.interconnects.ai/p/building-on-evaluation-quicksand)** — Nathan Lambert — <https://www.interconnects.ai/p/building-on-evaluation-quicksand> · *博客* — LLM 评测没有真值；污染；评测与训练耦合。
10. **[Who Validates the Validators? (EvalGen)](https://arxiv.org/abs/2404.12272)** — Shankar、Zamfirescu-Pereira、Hartmann、Parameswaran、Arawjo（UIST '24）— <https://arxiv.org/abs/2404.12272> · *论文* — “标准漂移”：评分之前无法预先写好量规。
11. **[Benches 2026 — "LLM benchmarks in the era of agents"](https://florianbrand.com/posts/benches-2026)** — Florian Brand（Prime Intellect）— <https://florianbrand.com/posts/benches-2026> · *博客 + 61 页演讲* — 对智能体时代基准为何失效的最犀利当代解读：反对“评测已死、凭感觉即可”的论调；评测运行栈的每一层（提示、采样温度、评分器、框架）都会改变分数；基准真值也经常错误。
12. **[A Shared Playbook for Trustworthy Third-Party Evaluations](https://openai.com/index/trustworthy-third-party-evaluations-foundations/)** — OpenAI — <https://openai.com/index/trustworthy-third-party-evaluations-foundations/> · *博客（安全，2026 年 5 月）* — 什么让前沿模型安全保障与能力的独立评测值得信任：框架选择、扭曲结果的效度风险，以及第三方评测者所需标准。

---

<a id="1-why-we-need-evals"></a>
## 1 · 为什么需要评测

- **[The Second Half](https://ysymyth.github.io/The-Second-Half/)** — Shunyu Yao — <https://ysymyth.github.io/The-Second-Half/> · *博客* — 瓶颈从解决问题转向*定义和评估*问题。（另见 T2、T7）
- **[An LLM-as-Judge Won't Save the Product, Fixing Your Process Will](https://eugeneyan.com/writing/eval-process/)** — Eugene Yan — <https://eugeneyan.com/writing/eval-process/> · *博客* — “购买或构建另一个评测工具救不了产品。”评测就是伪装的科学方法。
- **[Your AI Product Needs Evals](https://hamel.dev/blog/posts/evals/)** — Hamel Husain — <https://hamel.dev/blog/posts/evals/> · *博客* — “你需要评测”的经典文章；消除查看数据的一切阻力；不要依赖通用框架。
- **[A Field Guide to Rapidly Improving AI Products](https://hamel.dev/blog/posts/field-guide/)** — Hamel Husain — <https://hamel.dev/blog/posts/field-guide/> · *博客* — “错误分析始终是投资回报率最高的活动。”AI 路线图的指标是完成的实验数。
- **[In Defense of AI Evals, for Everyone](https://www.sh-reya.com/blog/in-defense-ai-evals/)** — Shreya Shankar — <https://www.sh-reya.com/blog/in-defense-ai-evals/> · *博客* — 反驳反评测风潮；评测就是系统衡量应用质量。
- **[What We Learned from a Year of Building with LLMs](https://applied-llms.org/)** — Yan、Bischof、Frye、Husain、Liu、Shankar — <https://applied-llms.org/>（第二部分：<https://www.oreilly.com/radar/what-we-learned-from-a-year-of-building-with-llms-part-ii/>）· *博客* — “实习生测试”、现地现物，以及把感觉检查转成断言。
- **[Big Tech's LLM Evals Are Just Marketing](https://www.interconnects.ai/p/evals-are-marketing)** — Nathan Lambert — <https://www.interconnects.ai/p/evals-are-marketing> · *博客* — 为什么前沿实验室的排行榜数字是营销而非科学。
- **[AI Engineering pitfalls](https://huyenchip.com/2025/01/16/ai-engineering-pitfalls.html)** — Chip Huyen — <https://huyenchip.com/2025/01/16/ai-engineering-pitfalls.html> · *博客* — 《AI Engineering》作者总结的常见评测／AI 工程错误。（另见 T6）

- **[Evals Are NOT All You Need](https://www.oreilly.com/radar/evals-are-not-all-you-need/)** — Aishwarya Naresh Reganti & Kiriti Badam（O'Reilly Radar）— <https://www.oreilly.com/radar/evals-are-not-all-you-need/> · *博客* — 必不可少的细微补充：自动评分器本身不能救你；需要离线测试 + 生产监控 + 真实用户迭代的持续改进飞轮。与 Shreya 的《In Defense》共同完整呈现这场争论。🆕
- **[Why AI evals are the hottest new skill for product builders](https://www.lennysnewsletter.com/p/why-ai-evals-are-the-hottest-new-skill)** — Hamel Husain & Shreya Shankar、Lenny Rachitsky · <https://www.lennysnewsletter.com/p/why-ai-evals-are-the-hottest-new-skill> · *演讲* — 面向大众的“评测为何重要”入门内容，现场演示错误分析和开放／轴心编码；2025 年把评测普及给产品经理，公寓租赁机器人故事成为“不能凭感觉检查”的经典案例。🆕
- **[How evals drive the next chapter in AI for businesses](https://openai.com/index/evals-drive-next-chapter-of-ai/)** — OpenAI — <https://openai.com/index/evals-drive-next-chapter-of-ai/> · *博客* — 前沿实验室把评测描述为将模糊商业目标转化为规格和可测 ROI；可与 Lambert 的“评测是营销”相互制衡，并为企业读者奠定“为什么”。🆕 ⚠（URL 未核验）
- **[Beyond vibe checks: A PM's complete guide to evals](https://www.lennysnewsletter.com/p/beyond-vibe-checks-a-pms-complete)** — Aman Khan（Arize）、Lenny Rachitsky — <https://www.lennysnewsletter.com/p/beyond-vibe-checks-a-pms-complete> · *博客* — 广泛传播的产品经理视角论述：从“我看着不错”的感觉检查转向系统评测；它是 2025 年让评测成为主流产品技能的代表文章之一。🆕
- **[A pragmatic guide to LLM evals for devs](https://newsletter.pragmaticengineer.com/p/evals)** — Gergely Orosz & Hamel Husain（The Pragmatic Engineer）— <https://newsletter.pragmaticengineer.com/p/evals> · *通讯* — 面向广泛工程读者的核心理由：LLM 非确定性会击穿传统测试，因此需要评测。这是 Hamel 合著的高传播度动机文章。🆕
- **[Predicting model behavior before release by simulating deployment (Deployment Simulation)](https://openai.com/index/deployment-simulation/)** — OpenAI — <https://openai.com/index/deployment-simulation/> · *博客* — 固定／静态评测为何失效的 2026 年实证：模型会识别自己正在受测并操纵测试套件；重放约 130 万段真实对话发现了固定评测未捕获的奖励操纵，有力说明“评测必须演进”。🆕 ⚠（URL 未核验）
- **[evals are surprisingly often all you need](https://x.com/gdb/status/1733553161884127435)** — Greg Brockman（OpenAI）— <https://x.com/gdb/status/1733553161884127435> · *博客* — 奠定整套“为什么需要评测”论点的经典一句话（“评测是新的单元测试”），常被视为这一运动的奠基引述。虽短，却承载核心论证。

**必读：** Yao · Yan（eval-process）· Hamel（field-guide）

<a id="2-if-you-can-eval-it-you-have-built-it-eval-capability-rl-environment"></a>
## 2 · “如果能评测，就已经构建出来”——评测 ⇄ 能力 ⇄ RL 环境

- **[Asymmetry of Verification and Verifier's Law](https://www.jasonwei.net/blog/asymmetry-of-verification-and-verifiers-law)** — Jason Wei — <https://www.jasonwei.net/blog/asymmetry-of-verification-and-verifiers-law> · *博客* — 可训练性随可验证性变化；验证就是创建 RL 环境。
- **[A Taxonomy of RL Environments for LLM Agents](https://leehanchung.github.io/blogs/2026/03/21/rl-environments-for-llm-agents/)** — Han-Chung Lee — <https://leehanchung.github.io/blogs/2026/03/21/rl-environments-for-llm-agents/> · *博客* — 基准是冻结的 RL 环境；E = {T,H,V,S,C} 分解；“可验证胜过可评判”。
- **[The Life Cycle of an RL Environment](https://muratbuffalo.blogspot.com/2026/06/acm-cais-conference-on-ai-and-agentic.html)** — Kanav Garg（Core Automation；前 DeepMind）— 演讲；摘要见 <https://muratbuffalo.blogspot.com/2026/06/acm-cais-conference-on-ai-and-agentic.html> · *演讲* — 难度校准（1–4/16 的甜蜜区间）、RL 作为方差缩减、训练压力下的奖励操纵。*（本地笔记：`research/notes/kanav-garg-rl-environment-lifecycle.md`）*
- **[Welcome to the Era of Experience](https://storage.googleapis.com/deepmind-media/Era-of-Experience%20/The%20Era%20of%20Experience%20Paper.pdf)** — David Silver & Richard Sutton — <https://storage.googleapis.com/deepmind-media/Era-of-Experience%20/The%20Era%20of%20Experience%20Paper.pdf> · *论文* — 人类数据价值接近上限；前沿在于智能体从经验／合成环境中学习。
- **[RLHF Book, Ch. 16 — Evaluation](https://rlhfbook.com/c/16-evaluation)** — Nathan Lambert — <https://rlhfbook.com/c/16-evaluation> · *书籍* — 评估反映训练目标；提示格式敏感性（60%→约 0%）。
- **[What Comes Next with Reinforcement Learning](https://www.interconnects.ai/p/what-comes-next-with-reinforcement)** — Nathan Lambert — <https://www.interconnects.ai/p/what-comes-next-with-reinforcement> · *博客* — 长时程信用分配；RL 已准备好和未准备好的领域。
- **[verifiers](https://github.com/PrimeIntellect-ai/verifiers)** — Prime Intellect — <https://github.com/PrimeIntellect-ai/verifiers>（文档：`.../blob/main/docs/environments.md`）· *工具／仓库* — 评测与 `prime-rl` 共用同一个环境包，以代码实现“评测就是 RL 环境”的论点。

- **[DeepSeek-R1: Incentivizing Reasoning Capability in LLMs via Reinforcement Learning](https://arxiv.org/abs/2501.12948)** — DeepSeek-AI（Guo 等）— <https://arxiv.org/abs/2501.12948> · *论文* — 论点的实证：使用基于规则的可验证奖励开展纯 RL（无 SFT），即可涌现推理能力；这是“只要能验证，RL 就能构建”的经典结果，亦发表于 Nature 2025。它此前显眼地缺席于这个讨论评测即 RL 环境的章节。🆕
- **[Tülu 3: Pushing Frontiers in Open Language Model Post-Training](https://arxiv.org/abs/2411.15124)** — Lambert 等（Allen Institute for AI）— <https://arxiv.org/abs/2411.15124> · *论文* — 创造／普及 RLVR，并开源方法与代码（open-instruct）：在答案可检查的任务上用验证器替换奖励模型。它是本节所有“可验证胜过可评判”论断的基础引用。🆕
- **[Natural Emergent Misalignment from Reward Hacking in Production RL](https://www.anthropic.com/research/emergent-misalignment-reward-hacking)** — Anthropic — <https://www.anthropic.com/research/emergent-misalignment-reward-hacking> · *论文* — 为“训练压力下奖励操纵”提供实证：在真实编码环境中学会作弊会泛化为破坏／伪装对齐；并提出接种式提示作为缓解方法（arXiv 2511.18397）。🆕
- **[Environments Hub: A Community Hub To Scale RL To Open AGI](https://www.primeintellect.ai/blog/environments)** — Prime Intellect — <https://www.primeintellect.ai/blog/environments> · *博客* — verifiers 规范市场的发布文章（2,500+ 个共享评测／RL 环境），把“评测就是 RL 环境”变为真实生态，也是已收录 verifiers 仓库的自然补充。🆕
- **[How to fully automate software engineering](https://www.mechanize.work/blog/how-to-fully-automate-software-engineering/)** — Ege Erdil、Matthew Barnett、Tamay Besiroglu（Mechanize）— <https://www.mechanize.work/blog/how-to-fully-automate-software-engineering/> · *博客* — 对反向论点最犀利的表述：当今 RL 环境仍很初级，能力受限于能否构建更丰富、多样的环境——“你只能获得自己能为之构建环境的能力”。🆕
- **[Cheap RL tasks will waste compute](https://www.mechanize.work/blog/cheap-rl-tasks-will-waste-compute/)** — Mechanize（Erdil、Barnett、Besiroglu）— <https://www.mechanize.work/blog/cheap-rl-tasks-will-waste-compute/> · *博客* — 环境质量的经济学：数据和算力互补，低质量廉价任务会浪费昂贵 RL 算力；直接解释难度校准及环境设计为何重要。🆕
- **[An FAQ on Reinforcement Learning Environments](https://epoch.ai/gradient-updates/state-of-rl-envs)** — Jean-Stanislas Denain & Chris Barber（Epoch AI）— <https://epoch.ai/gradient-updates/state-of-rl-envs> · *博客* — 对 18 位从业者的访谈调查：RL 环境如何实际构建、奖励操纵失败模式、生产规模化瓶颈；补上本节缺少的领域实证地图。🆕
- **[RL Environments and RL for Science: Data Foundries and Multi-Agent Architectures](https://newsletter.semianalysis.com/p/rl-environments-and-rl-for-science)** — AJ Kourabi & Dylan Patel（SemiAnalysis）— <https://newsletter.semianalysis.com/p/rl-environments-and-rl-for-science> · *通讯* — 市场结构视角：已有 35+ 家公司销售 RL 环境；能力提升来自增加 RL 算力而非预训练。以实际建设与采购活动支撑“基准 = 冻结 RL 环境”的论点。🆕
- **[Terminal-Bench: Benchmarking Agents on Hard, Realistic Tasks in Command Line Interfaces](https://github.com/harbor-framework/terminal-bench)** — Harbor / Stanford / Laude Institute — <https://github.com/harbor-framework/terminal-bench> · *基准* — 论点的具体实例：每个任务附带 Docker 环境、程序化验证测试套件和 oracle，即一个本身就是 RL 环境且被这样使用的基准。2.4k stars，仍活跃。🆕
- **[tau2-bench (τ²-Bench): A Benchmark for Tool-Agent-User Interaction in Real-World Domains](https://github.com/sierra-research/tau2-bench)** — Sierra Research（Barres 等）— <https://github.com/sierra-research/tau2-bench> · *基准* — 双控制、多轮、遵循策略的评测，包含模拟用户和可验证数据库状态检查；它是数学／编码之外可验证对话／智能体环境的经典示例（论文 arXiv 2506.07982）。🆕

**必读：** Wei · Lee（RL 环境分类）

<a id="3-the-model-harness-skill-decomposition"></a>
## 3 · 模型／框架／技能分解

- **[Hidden Technical Debt: Agent Harness](https://leehanchung.github.io/blogs/2026/05/08/hidden-technical-debt-agent-harness/)** — Han-Chung Lee — <https://leehanchung.github.io/blogs/2026/05/08/hidden-technical-debt-agent-harness/> · *博客* — 框架就是智能体；团队所谓的“模型”大部分其实是框架 + 产品。
- **[Hidden Technical Debt series (index)](https://leehanchung.github.io/blogs/)** — Han-Chung Lee — <https://leehanchung.github.io/blogs/> · *博客* — 四篇系列文章（评测基础设施、运行时、框架，以及约 2026/04/24 发布的智能体运行时）。*（请在索引中核验运行时文章 URL。）*
- **[Measuring AI Ability to Complete Long Tasks](https://metr.org/blog/2025-03-19-measuring-ai-ability-to-complete-long-tasks/)** — METR — <https://metr.org/blog/2025-03-19-measuring-ai-ability-to-complete-long-tasks/> · *论文／博客* — 脚手架会改变测得的时间跨度；把相对人类用时的成功率作为基本量。（另见 T9）
- **[Turing Post interview ("Open Models Won't Catch Up")](https://www.turingpost.com/p/nathanlambert)** — Nathan Lambert — <https://www.turingpost.com/p/nathanlambert> · *演讲／访谈* — “技术人员所称的框架或产品，比模型本身更重要。”
- **[Quo vadis, LLM benchmarks?](https://florianbrand.com/posts/benches-2026)** — Florian Brand（Prime Intellect）— <https://florianbrand.com/posts/benches-2026>（演讲：<https://www.youtube.com/watch?v=kmTMc-fVSXw>）· *博客／演讲* — AlgoTune 案例：*同一模型，不同框架，排名相反。*（另见 T6）*（笔记：`research/notes/florian-brand-*`）*

- **[The Model is the Product](https://leehanchung.github.io/talks/2025/04/23/the-model-is-the-product/)** — Han-Chung Lee — <https://leehanchung.github.io/talks/2025/04/23/the-model-is-the-product/> · *演讲* — 必读作者整个论点背后的一手演讲（Data Council 2025），与 Hamel 的“The Model is Not the Product”直接相对，是本节框架／模型争论的奠基文本。🆕
- **[The Model is Not the Product](https://www.youtube.com/watch?v=EEw2PpL-_NM)** — Hamel Husain — <https://www.youtube.com/watch?v=EEw2PpL-_NM> · *演讲* — Lee 辩论的另一方（Data Council 2025）：优秀产品主要由框架 + 产品 + 评测构成，而非模型。本节已引用 Lee，也应引用它间接提及的另一方。🆕
- **[Agents are models using tools in a loop](https://simonwillison.net/2025/May/22/tools-in-a-loop/)** — Simon Willison — <https://simonwillison.net/2025/May/22/tools-in-a-loop/> · *博客* — 如今广泛采用的智能体经典定义；“关键技能在于同时设计工具与循环”，最清楚地说明为何框架而非模型主导行为。🆕
- **[Harness engineering: leveraging Codex in an agent-first world](https://openai.com/index/harness-engineering/)** — OpenAI — <https://openai.com/index/harness-engineering/> · *博客* — 前沿实验室创造“框架工程”概念的一手来源：由 Codex 智能体构建百万行代码库，改进环境／框架比改进模型更重要。它从实验室侧补充 Lee 的“框架就是智能体”。（抓取器访问 URL 返回 403，但页面在线，并有 InfoQ／Milvus 报道佐证。）🆕
- **[Equipping agents for the real world with Agent Skills](https://www.anthropic.com/engineering/equipping-agents-for-the-real-world-with-agent-skills)** — Anthropic — <https://www.anthropic.com/engineering/equipping-agents-for-the-real-world-with-agent-skills> · *博客* — 模型／框架／技能分解中“技能”一侧的一手来源：技能是可组合、渐进披露的能力，后来成为开放标准。本节标题含“技能”，却此前没有技能来源。🆕
- **[Effective context engineering for AI agents](https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents)** — Anthropic — <https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents> · *博客* — Anthropic 对框架职责的一手表述：工程化上下文，包括编辑、压缩、记忆和程序化工具调用；这解释了为何同一模型配不同框架会分化。🆕
- **[Writing effective tools for agents — with agents](https://www.anthropic.com/engineering/writing-tools-for-agents)** — Anthropic（Ken Aizawa）— <https://www.anthropic.com/engineering/writing-tools-for-agents> · *博客* — 工具设计是框架的承重部分；“智能体的有效性取决于我们提供的工具”，并以评测优先方式验证。它把框架决策直接连接到可测智能体表现。🆕
- **[Paving the way for agents in biology (VirBench)](https://www.anthropic.com/research/agents-in-biology)** — Anthropic — <https://www.anthropic.com/research/agents-in-biology> · *博客* — 40 种病原体的 120 个病毒序列检索查询；没有确定性工具时，模型准确率为 16.9%–91.3%，相同提示下 Claude Sonnet 4 三次分别返回 106、15、5 条序列。加入确定性检索层 gget 后，所有模型均超过 90%（最高 99.7%），并完全消除运行间方差：“加入确定性检索层后，模型选择不再那么重要。”这是框架／工具设计主导科学精度模型选择的受控实证。🆕
- **[Same Model, Different Results: Why Coding Agents Aren't Interchangeable](https://blog.thepete.net/blog/2025/12/10/same-model-different-results-why-coding-agents-arent-interchangeable/)** — Pete Hodgson — <https://blog.thepete.net/blog/2025/12/10/same-model-different-results-why-coding-agents-arent-interchangeable/> · *博客* — 具体拆解 Claude Code 框架（系统提醒、子智能体、规划、IDE 反馈），说明相同模型也会产生不同结果；这是 Brand 的 AlgoTune 观点对应的实践者案例。🆕
- **[Holistic Agent Leaderboard (HAL)](https://hal.cs.princeton.edu/)** — Princeton SAgE 团队（Kapoor、Narayanan 等）— <https://hal.cs.princeton.edu/> · *基准* — 标准化、成本感知的框架，以同一智能体框架运行 9 个基准／9 个模型（21,730 次 rollout），从基础设施层回答“框架会混淆排名”。ICLR 2026；论文 arXiv:2510.11977。🆕
- **[Agent Harness Engineering](https://www.oreilly.com/radar/agent-harness-engineering/)** — Addy Osmani（O'Reilly Radar）— <https://www.oreilly.com/radar/agent-harness-engineering/> · *博客* — “普通模型配优秀框架，胜过优秀模型配糟糕框架”；把智能体失败重述为可追踪到 AGENTS.md 规则的框架／配置问题，并总结编码智能体中正在趋同的框架原语。🆕
- **[What comes next with open models (weights / tools / harness decomposition)](https://www.interconnects.ai/p/the-next-phase-of-open-models)** — Nathan Lambert（Interconnects）— <https://www.interconnects.ai/p/the-next-phase-of-open-models> · *博客* — Lambert 于 2026 年 3 月把 AI 系统明确表述为权重 + 工具 + 框架；它是已收录 Turing Post 访谈的文字版配套内容，给出显式三部分分解。🆕

**必读：** Lee（框架）· Brand（Quo vadis）

<a id="4-observability-the-output-eval-space-the-surfaces-you-can-grade"></a>
## 4 · 可观测性与输出／评测空间（可评分的观测面）

- **[Hidden Technical Debt: Agent Evaluation Infrastructure](https://leehanchung.github.io/blogs/2026/06/13/hidden-technical-debt-agent-evaluation-infra/)** — Han-Chung Lee — <https://leehanchung.github.io/blogs/2026/06/13/hidden-technical-debt-agent-evaluation-infra/> · *博客* — 控制平面／数据平面；**五个观测面**（输出、轨迹、记忆、环境、机理）；空工具结果幻觉。
- **[The Three Pillars of AI Observability](https://www.braintrust.dev/blog/three-pillars-ai-observability)** — Braintrust — <https://www.braintrust.dev/blog/three-pillars-ai-observability> · *博客* — 数据集协调（活数据集）；轨迹／评测／标注。
- **[Agent Trajectory Evaluations](https://arize.com/docs/ax/evaluate/evaluators/trace-and-session-evals/trace-level-evaluations/agent-trajectory-evaluations)** — Arize（AX 文档）— <https://arize.com/docs/ax/evaluate/evaluators/trace-and-session-evals/trace-level-evaluations/agent-trajectory-evaluations> · *文档* — 不只给答案评分，也给路径评分。
- **[AI Agent Metrics: How Elite Teams Evaluate](https://galileo.ai/blog/ai-agent-metrics)** — Galileo — <https://galileo.ai/blog/ai-agent-metrics> · *博客* — 具体的智能体指标分类（行动完成、工具选择等）。
- **[OpenInference semantic conventions](https://github.com/Arize-ai/openinference/blob/main/spec/semantic_conventions.md)** — Arize — <https://github.com/Arize-ai/openinference/blob/main/spec/semantic_conventions.md> · *工具／仓库* — 基于 OTel 的智能体轨迹模式（工具、参数、观察、延迟、成本）。
- **[LangSmith Evaluation / Trajectory evals](https://docs.langchain.com/langsmith/evaluation)** — LangChain — <https://docs.langchain.com/langsmith/evaluation> · <https://docs.langchain.com/langsmith/trajectory-evals> · *文档*。

- **[OpenTelemetry GenAI Semantic Conventions (agent & framework spans)](https://github.com/open-telemetry/semantic-conventions-genai)** — OpenTelemetry / CNCF — <https://github.com/open-telemetry/semantic-conventions-genai> · *文档* — OpenInference 所映射的上游厂商中立标准，规定 LLM 调用、`invoke_agent`、`execute_tool`、MCP 的 span／指标／事件；它是本节 OpenInference 条目所派生的经典轨迹模式。🆕
- **[Semantic Conventions for GenAI agent and framework spans](https://opentelemetry.io/docs/specs/semconv/gen-ai/gen-ai-agent-spans/)** — OpenTelemetry — <https://opentelemetry.io/docs/specs/semconv/gen-ai/gen-ai-agent-spans/> · *文档* — `create_agent`／`invoke_agent`／`execute_tool` span 与属性的易读规范页，精确定义可评分智能体轨迹的形态。🆕
- **[Inside the LLM Call: GenAI Observability with OpenTelemetry](https://opentelemetry.io/blog/2026/genai-observability/)** — OpenTelemetry（博客）— <https://opentelemetry.io/blog/2026/genai-observability/> · *博客* — 发出并读取 GenAI span（token 用量、结束原因、工具调用）的操作指南；供不熟悉 OTel 的实践者了解轨迹观测面。🆕
- **[W&B Weave — tracing & evaluation toolkit](https://docs.wandb.ai/weave)** — Weights & Biases — <https://docs.wandb.ai/weave> · *文档* — `@weave.op` 轨迹树（输入／输出／成本／延迟）和基于评分器的评测框架；广泛用于同时评判轨迹与输出。🆕
- **[Laminar — open-source observability for AI agents](https://laminar.sh/)** — Laminar — <https://laminar.sh/> · *工具* — 原生 OTel、智能体专用：转录视图、对轨迹执行 SQL，以及 rollout 调试器；专为多步智能体轨迹评分而非单次 LLM 调用设计。🆕

**必读：** Lee（评测基础设施）· Braintrust（三大支柱）
<a id="5-evaluation-infrastructure-the-eval-stack-datasets-scorers-onlineoffline-tracing-ci"></a>
## 5 · 评测基础设施（评测技术栈：数据集、评分器、在线/离线评测、追踪、CI）

*（所有仓库 URL 均已于 2026 年 6 月通过 GitHub API 核验。`🆕` = 于 2025–2026 年发布/扩展。`⚠️` = 注意事项/已停止维护。）*

### 5a · 评测框架与工具（代码优先的测试运行器）
- **[Inspect AI](https://github.com/UKGovernmentBEIS/inspect_ai)** — UK AISI — <https://github.com/UKGovernmentBEIS/inspect_ai> · <https://inspect.aisi.org.uk/> — `@task` 将数据集 + 求解器 + 评分器绑定；支持自定义评分器和沙箱化工具。智能体评测的参考框架。**（必读）**
- **[inspect_evals](https://github.com/UKGovernmentBEIS/inspect_evals)** — UK AISI — <https://github.com/UKGovernmentBEIS/inspect_evals> — 🆕 配套的社区基准目录（GAIA、CTF、AIME……）——Inspect 的“开箱即用组件”。
- **[AISI Engineering Playbook](https://engineering-playbook.aisi.org.uk/)** — UK AISI — <https://www.aisi.gov.uk/blog/releasing-aisis-engineering-playbook> · <https://engineering-playbook.aisi.org.uk/> · *指南* — 🆕 面向生产级前沿模型评测基础设施的开放指南：五层技术栈（Evaluate · Isolate · Connect · Run · Scale），涵盖在沙箱中运行不可信代码、经过审计的提供商路由、托管式开放权重推理，以及将这些部分联结起来的工作体系。“这项工作大多不可见、鲜少得到记录，而且每个认真开展评测的团队都要从头开发。”这是 Inspect AI 周边的基础设施层，由构建并运行它的团队亲自编写。
- **[lm-evaluation-harness](https://github.com/EleutherAI/lm-evaluation-harness)** — EleutherAI — <https://github.com/EleutherAI/lm-evaluation-harness> — 标准学术评测工具；原生支持去污染；任务使用 YAML 定义。
- **[OLMES](https://github.com/allenai/olmes)** — Allen Institute (Ai2) — <https://github.com/allenai/olmes> — 🆕 OLMo/Tülu 背后可复现的评测**标准 + 工具**：标准化提示词、指标和格式，实现模型间公平一致的比较。
- **[BenchFlow](https://github.com/benchflow-ai/benchflow)** — <https://github.com/benchflow-ai/benchflow> · <https://benchflow.ai> — 🆕 环境实验室框架：用于构建 RL 环境、评测与后训练的研究基础设施 + 运行时；提供 **SkillsBench** 和 **ClawsBench**。（“环境就是新的数据。”）
- **[lighteval](https://github.com/huggingface/lighteval)** — Hugging Face — <https://github.com/huggingface/lighteval> — 🆕 横跨 transformers/vLLM/TGI/nanotron 的一体化工具，包含 1000 多项任务；HF 用来接替 `evaluate` 的方案。
- **[OpenBench](https://github.com/groq/openbench)** — Groq — <https://github.com/groq/openbench> — 🆕 与提供商无关的 `bench` CLI，包含 95 多项基准，基于 Inspect 原语构建。
- **[simple-evals](https://github.com/openai/simple-evals)** — OpenAI — <https://github.com/openai/simple-evals> — 精简的零样本/CoT 脚本（MMLU、HumanEval、SimpleQA、HealthBench）；OpenAI 公开数字所用工具。⚠️ 当前未积极维护。
- **[OpenAI Evals](https://github.com/openai/evals)** — <https://github.com/openai/evals> — `completion_fn` 抽象 = 可替换被测系统。（最佳实践：<https://developers.openai.com/api/docs/guides/evaluation-best-practices>）
- **[promptfoo](https://github.com/promptfoo/promptfoo)** — <https://github.com/promptfoo/promptfoo> — MIT 许可的评测 + 红队 CLI；YAML 配置可通过 git 比较差异。**（必读）**
- **[DeepEval / Confident AI](https://github.com/confident-ai/deepeval)** — <https://github.com/confident-ai/deepeval> — “面向 LLM 的 pytest”，提供 40 多项指标（G-Eval、RAG、幻觉）+ 红队测试；每天 ~2M 次评测；提供托管云服务。🆕
- **[pydantic-evals](https://github.com/pydantic/pydantic-ai)** — <https://github.com/pydantic/pydantic-ai>（`ai.pydantic.dev/evals`）— 🆕 Pydantic AI 团队提供的类型安全 Datasets/Cases/Evaluators，集成 OTel 追踪。
- **[openevals](https://github.com/langchain-ai/openevals)** — LangChain — <https://github.com/langchain-ai/openevals> — 🆕 预构建评估器 + `create_llm_as_judge`（包括多模态）；**agentevals**（<https://github.com/langchain-ai/agentevals>，轨迹匹配）的通用配套工具。
- **[MLflow GenAI evaluate](https://mlflow.org/docs/latest/genai/eval-monitor/)** — <https://mlflow.org/docs/latest/genai/eval-monitor/> — 🆕 `mlflow.genai.evaluate`：50 多个裁判/指标、自定义评分器、MLflow 内部的回归数据集。
- **[HELM (crfm-helm)](https://github.com/stanford-crfm/helm)** — Stanford CRFM — <https://github.com/stanford-crfm/helm> — 整体评测：标准化数据集 + 准确率之外的指标 + 排行榜（另有 VHELM、HEIM）。
- **[Giskard](https://github.com/Giskard-AI/giskard-oss)** — <https://github.com/Giskard-AI/giskard-oss> — 根据自然语言应用描述自动生成对抗性测试套件（注入、幻觉、偏见）。
- **[Deepchecks LLM](https://github.com/deepchecks/deepchecks)** — <https://github.com/deepchecks/deepchecks>（`llmdocs.deepchecks.com`）— 基于属性的评分（上下文扎根性、毒性、流畅性）+ 自定义 LLM 裁判属性。
- **[UpTrain](https://github.com/uptrain-ai/uptrain)** — <https://github.com/uptrain-ai/uptrain> — 20 多项预配置检查 + 失败根因分析。
- **[HF `evaluate`](https://github.com/huggingface/evaluate)** — <https://github.com/huggingface/evaluate> — 经典指标库，⚠️ 已进入维护模式（LLM 请使用 lighteval）。
- **[Harbor](https://github.com/harbor-framework/harbor)** — harbor-framework（Laude Institute / Stanford）— <https://github.com/harbor-framework/harbor> — 🆕 用于运行智能体评测及创建/使用 RL 环境的框架；支撑 Terminal-Bench 2.0。~3.7k★。⚠️ 名称存在重载（参见本地 LLM 工具包 `av/harbor`）。
- **[Caliper](https://github.com/edonadei/caliper)** — Emrick Donadei — <https://github.com/edonadei/caliper> · <https://pypi.org/project/caliper-eval/> — 🆕 **面向智能体技能的 pass@k 可靠性工具**：针对 Claude Code / Codex / pi 将一项技能运行 k 次，使用 LLM 自动评分器（`expect:`）和/或确定性 Python（`assert:`）评价每次尝试；随后通过不加载技能的 `--baseline` 重跑并报告差值，证明技能优于基础智能体。`.eval.yaml` 规格可通过 git 比较差异；每次尝试使用隔离沙箱。
- **[Coder Eval](https://github.com/UiPath/coder_eval)** — UiPath — <https://github.com/UiPath/coder_eval> · <https://coder-eval.com/docs> — 🆕 将**技能激活视为可测变量**：在带标签数据集上使用 `skill_triggered`，产生套件级精确率/召回率/F1 门控（参见第 3 节）。属于工具层，而非排行榜层。~0.1k★。

### 5b · TypeScript/JS 原生评测运行器
- **[evalite](https://github.com/mattpocock/evalite)** — Matt Pocock — <https://github.com/mattpocock/evalite> — 🆕 基于 Vitest、本地优先的评测运行器；使用 `.eval.ts` 文件，提供 Web UI，并具备成本感知能力。
- **[Mastra scorers](https://github.com/mastra-ai/mastra)** — <https://github.com/mastra-ai/mastra>（`mastra.ai/docs/evals/overview`）— 🆕 Mastra 智能体框架中的模型评分/规则/统计评分器、在线评测与 CI。
- **[Open Multi-Agent evaluation](https://github.com/open-multi-agent/open-multi-agent)** — Open Multi-Agent — <https://github.com/open-multi-agent/open-multi-agent/blob/main/docs/evaluation.md> · *工具/框架* — 🆕 TypeScript 原生、带版本的 EvalSets，提供离线 JSON/Markdown/JUnit 报告、用于 CI 的基线回归门控，以及可选的生产采样；后者异步评价已结束的运行，不改变其结果。
- **[Vercel agent-eval](https://github.com/vercel-labs/agent-eval)** — <https://github.com/vercel-labs/agent-eval> — 🆕 在自定义任务上对编程智能体（Claude Code、Codex、Cursor）进行 A/B 测试；提供通过率仪表板。
- **[Autoevals](https://github.com/braintrustdata/autoevals)** — Braintrust — <https://github.com/braintrustdata/autoevals> — 跨 Py/JS/Go/Ruby 的开源评分器库（事实性、相关性、安全性……）。

### 5c · RAG / 检索评估
- **[TruLens](https://github.com/truera/trulens)** — <https://github.com/truera/trulens> — 插桩 + “反馈函数”（RAG 三元组），现已基于 OTel。
- **[ARES](https://github.com/stanford-futuredata/ARES)** — Stanford — <https://github.com/stanford-futuredata/ARES> — 合成查询 + 微调裁判 + 用于置信区间的预测驱动推断。
- **[RAGChecker](https://github.com/amazon-science/RAGChecker)** — Amazon Science — <https://github.com/amazon-science/RAGChecker> — 🆕 论断级诊断，区分检索器错误与生成器错误。
- **[continuous-eval (Relari)](https://github.com/relari-ai/continuous-eval)** — <https://github.com/relari-ai/continuous-eval> — 覆盖检索/生成/工具使用的模块化逐模块指标。
- **[Tonic Validate](https://github.com/TonicAI/tonic_validate)** — <https://github.com/TonicAI/tonic_validate> — 以 GitHub Action 形式为 CI 提供 RAG 指标。

### 5d · LLM-as-judge / 奖励 / 验证器库
- **[RewardHarness](https://github.com/TIGER-AI-Lab/RewardHarness)** — TIGER-AI-Lab 等 — <https://arxiv.org/abs/2605.08703> · *框架* — 🆕 从偏好示范中演化特定任务的评分技能和工具，无需训练奖励模型即可生成图像编辑判断和兼容 GRPO 的标量奖励。
- **[verdict](https://github.com/haizelabs/verdict)** — Haize Labs — <https://github.com/haizelabs/verdict> — 🆕 声明式复合裁判（辩论/核验/聚合、推断时扩展）；arXiv:2502.18018。
- **[RULER](https://github.com/OpenPipe/ART)** — OpenPipe (ART) — <https://github.com/OpenPipe/ART>（`art.openpipe.ai/fundamentals/ruler`）— 🆕 无需标签即可对轨迹排序的 LLM 裁判——将裁判作为 RL 奖励。**（业界必读）**
- **[Prometheus 2](https://github.com/prometheus-eval/prometheus-eval)** — <https://github.com/prometheus-eval/prometheus-eval> — 用于量表评估 + 成对比较的开放权重评估模型。
- **[Atla Selene](https://github.com/atla-ai/selene-mini)** — <https://github.com/atla-ai/selene-mini> — 🆕 8B SOTA 开放裁判（评分 + 批评）；另有 MCP 服务器 `atla-ai/atla-mcp-server`。arXiv:2501.17195。
- **[Patronus Lynx / GLIDER](https://github.com/patronus-ai/Lynx-hallucination-detection)** — <https://github.com/patronus-ai/Lynx-hallucination-detection> · <https://github.com/patronus-ai/glider> — 🆕 开放幻觉裁判 / 可解释的片段级裁判。
- **[Flow-Judge](https://github.com/flowaicom/flow-judge)** — <https://github.com/flowaicom/flow-judge> — 高效的 3.8B 开放评估器。
- **[RewardBench](https://github.com/allenai/reward-bench)** — AI2 — <https://github.com/allenai/reward-bench> — 权威的奖励模型（+ v2 裁判）基准/工具。
- **[JudgeBench](https://github.com/ScalerLab/JudgeBench)** — <https://github.com/ScalerLab/JudgeBench> — 用于评估裁判本身的基准。
- **[reward-kit](https://github.com/fw-ai-external/reward-kit)** — Fireworks — <https://github.com/fw-ai-external/reward-kit> — 🆕 基于装饰器的奖励函数编写工具（兼容 TRL/Fireworks）。
- **[truescore](https://github.com/SaifPunjwani/truescore)** — Saif Punjwani — <https://github.com/SaifPunjwani/truescore> · *工具* — 以留出的人类标签衡量裁判（敏感度、Cohen's kappa、Gwet's AC1），测试其长度偏差、位置偏差和自我偏好偏差，再使用预测驱动推断校正报告分数，使区间覆盖真实情况而非裁判意见。Apache-2.0，仅依赖 numpy/scipy。🆕 ⚠️ 新且尚未验证（仓库创建于 2026 年 7 月，尚无外部采用）。

### 5e · RL 环境 / 可验证奖励工具包（评测 ⇄ 训练）
- **[verifiers](https://github.com/PrimeIntellect-ai/verifiers)** — Prime Intellect — <https://github.com/PrimeIntellect-ai/verifiers> — 环境 = 数据集 + 工具 + 量表；一个软件包同时服务评测、RL 和合成数据。**（必读）**
- **[Environments Hub](https://github.com/PrimeIntellect-ai/community-environments)** — Prime Intellect — <https://github.com/PrimeIntellect-ai/community-environments>（app.primeintellect.ai）— 🆕 众包、基于 verifiers 的 RL/评测环境。
- **[prime-rl](https://github.com/PrimeIntellect-ai/prime-rl)** — Prime Intellect — <https://github.com/PrimeIntellect-ai/prime-rl> — 🆕 使用 verifiers 环境的异步 RL 训练器（INTELLECT-3）。
- **[BenchFlow](https://github.com/benchflow-ai/benchflow)** — <https://github.com/benchflow-ai/benchflow> · <https://benchflow.ai> — 🆕 环境实验室：构建并运行 RL/评测环境（SkillsBench、ClawsBench、运行时）。“环境就是新的数据。”（另见第 5a 节）
- **[HUD](https://github.com/hud-evals/hud-python)** — <https://github.com/hud-evals/hud-python> — 🆕 用于构建/运行带遥测的智能体评测环境（计算机使用、浏览器、MCP）的 SDK。
- **[Atropos](https://github.com/NousResearch/atropos)** — Nous Research — <https://github.com/NousResearch/atropos> — 🆕 面向 rollout/可验证奖励的异步“环境微服务”框架。
- **[verl](https://github.com/volcengine/verl)** — <https://github.com/volcengine/verl>（现为 `verl-project/verl`）— 事实上的业界 RLVR 训练器（PPO/GRPO）。~22k★。
- **[OpenRLHF](https://github.com/OpenRLHF/OpenRLHF)** — <https://github.com/OpenRLHF/OpenRLHF> · **SkyRL** — <https://github.com/NovaSky-AI/SkyRL> · **AReaL** — <https://github.com/areal-project/AReaL> · **ROLL** — <https://github.com/alibaba/ROLL> · **rLLM** — <https://github.com/agentica-project/rllm> · **TRL** — <https://github.com/huggingface/trl> — 智能体接受后训练和评测所用的 RL 训练技术栈。
- **[Open Reward Standard (ORS)](https://docs.openreward.ai/)** — General Reasoning — <https://docs.openreward.ai/>（PyPI `openreward`）— 🆕 扩展 MCP、加入 RL 原语（回合、奖励、课程）的规范。⚠️ 尚未确认单一权威仓库。

### 5f · 可观测性 + 评测平台（追踪 · 数据集 · 在线/离线 · CI）
- **[Arize Phoenix](https://github.com/Arize-ai/phoenix)** — <https://github.com/Arize-ai/phoenix> — 开源 OTel 追踪 + 响应/检索评估 + 数据集/实验。**（必读）**
- **[Langfuse](https://github.com/langfuse/langfuse)** — <https://github.com/langfuse/langfuse> — 开源：评测（LLM 裁判、反馈、人工标注）、数据集/实验、提示词管理；可自行托管。🆕
- **[Opik](https://github.com/comet-ml/opik)** — Comet — <https://github.com/comet-ml/opik> — 🆕 完全开源的评测 + 可观测性（裁判、数据集、可在 CI 中运行的评测）。
- **[W&B Weave](https://github.com/wandb/weave)** — <https://github.com/wandb/weave> — `weave.Evaluation` 评分器（精确匹配/正则/模型评分/嵌入）+ Guardrails；提供比较仪表板。🆕（Humanloop 的迁移目标。）
- **[Braintrust](https://www.braintrust.dev/docs/start/eval-sdk)** — <https://www.braintrust.dev/docs/start/eval-sdk>（offline-eval-guide）— 在黄金数据集上运行 `Eval()`；支持离线与在线评测。**（必读）**
- **[Patronus AI](https://www.patronus.ai/)** — <https://www.patronus.ai/>（`github.com/patronus-ai`）— 🆕 研究级裁判（Lynx、GLIDER、**Percival** 智能体失败调试器）、实验、多模态裁判。
- **[Maxim AI](https://www.getmaxim.ai/)** — <https://www.getmaxim.ai/> — 🆕 在数千种场景/角色设定上开展智能体**模拟** + 评测 + 可观测性。
- **[Galileo](https://galileo.ai/)** — <https://galileo.ai/> — Luna 评估器 + Agentic Evaluations。
- **[Vellum](https://www.vellum.ai/)** — <https://www.vellum.ai/> — 可视化工作流 + 对每次生产运行评分的离线/在线评测。
- **[Helicone](https://github.com/helicone/helicone)** — <https://github.com/helicone/helicone> — 开源网关 + 可观测性；“Scores”接收外部评测结果。
- **[Traceloop / OpenLLMetry](https://github.com/traceloop/openllmetry)** — <https://github.com/traceloop/openllmetry> — 开源 OTel 插桩（Py/TS/Go/Ruby）+ 托管可靠性平台。
- **[Langtrace](https://github.com/Scale3-Labs/langtrace)** — <https://github.com/Scale3-Labs/langtrace> — 符合 OTel 标准的开源追踪 + 人工评分 + 数据集管理。
- **[WhyLabs / LangKit](https://github.com/whylabs/langkit)** — <https://github.com/whylabs/langkit> — 用于生产监控的高吞吐文本信号指标（毒性、PII、越狱）。
- **[Portkey](https://github.com/portkey-ai/gateway)** — <https://github.com/portkey-ai/gateway> — 🆕 开源网关 + 60 多种护栏 + 可观测性（2026 年 3 月完全开源）。
- **[Datadog LLM Observability](https://www.datadoghq.com/product/ai/llm-observability/)** — <https://www.datadoghq.com/product/ai/llm-observability/> — 🆕 评估器 + 黄金数据集 + **LLM Experiments** + AI Agent Monitoring（2025 年 6 月）。
- **[Fiddler AI](https://www.fiddler.ai/)** — <https://www.fiddler.ai/> — 🆕 Trust Models（安全性/PII/忠实性），评分延迟 <100ms；Guardrails + 智能体可观测性。
- **[PromptLayer](https://www.promptlayer.com/)** — <https://www.promptlayer.com/> · **New Relic AI Monitoring** — <https://newrelic.com/platform/ai-monitoring> — 更轻量的提示词 CMS / APM 原生监控。
- **[whatbroke](https://github.com/arthi-arumugam-git/whatbroke)** — <https://github.com/arthi-arumugam-git/whatbroke> · *工具* — 开源 CLI，用于比较模型或提示词变更前后的两个智能体轨迹文件（JSONL）：缺失的工具调用、参数漂移、顺序变化、成本/延迟/结果差异；多样本波动率会降低基线不稳定性的优先级，只让真实回归浮现。离线、确定性、不使用裁判或 API 密钥；退出码可用作 CI 门控。🆕

### 5g · 追踪标准
- **[OpenInference](https://github.com/Arize-ai/openinference)** — Arize — <https://github.com/Arize-ai/openinference> — 智能体轨迹的语义约定（工具/参数/观察/延迟/成本）。
- **[OpenTelemetry GenAI semantic conventions](https://opentelemetry.io/docs/specs/semconv/gen-ai/)** — <https://opentelemetry.io/docs/specs/semconv/gen-ai/>（`open-telemetry/semantic-conventions`）— 🆕 与厂商无关的架构（现已覆盖智能体编排、MCP 工具调用和**质量评估** span 钩子）。

- **[Braintrust](https://www.braintrust.dev/)** — Braintrust — <https://www.braintrust.dev/> · *工具* — 业界标准的评测 + 可观测性平台（Notion、Stripe、Vercel），将离线实验与生产日志连接起来；本节已经列出 Braintrust 的 Autoevals，却遗漏了平台本身。🆕
- **[RagaAI Catalyst](https://github.com/raga-ai-hub/RagaAI-Catalyst)** — RagaAI — <https://github.com/raga-ai-hub/RagaAI-Catalyst> · *工具* — 开源智能体可观测性 + 评测 SDK，提供多智能体轨迹/执行图调试、合成数据生成和护栏管理——补足本节缺少的在线/护栏评测部分。🆕
- **[OpenAI Cookbook — Evals](https://developers.openai.com/cookbook/topic/evals)** — OpenAI — <https://developers.openai.com/cookbook/topic/evals> · *文档* — 持续维护、可运行的评测构建示例（包括 Agents SDK 评测、使用 Langfuse 评估智能体）；是 OpenAI Evals 的实用配套资料，也是能够“展示真实工作”的策展级资源。🆕 ⚠（URL 未核验）

- **[Building a better Bugbot](https://cursor.com/blog/building-bugbot)** — Stefan Heule 等（Cursor）— <https://cursor.com/blog/building-bugbot> · *优秀* — **围绕合并后的信号构建主要评测指标：Cursor 的“解决率”在 PR 合并时使用 AI 判断开发者最终是否真正修复了被标记的缺陷，并通过人工抽查验证。团队开展了 40 项重大实验，覆盖模型、提示词、迭代次数和智能体设计，解决率从 52% 提升到 70% 以上；其中最大单次跃升来自切换到完全智能体化的架构。BugBench（从真实 diff 中筛选、由人工标注缺陷的数据集）用于推动离线迭代。** _（摘录：“它在 PR 合并时使用 AI 判断作者是否在最终代码中真正解决了哪些缺陷。……自发布以来，我们开展了 40 项重大实验，使 Bugbot 的解决率从 52% 提升到 70% 以上。”）_ 🆕
- **[Insights Generator: Systematic Corpus-Level Trace Diagnostics for LLM Agents](https://arxiv.org/abs/2605.21347)** — Scale AI — <https://arxiv.org/abs/2605.21347> · *论文/工具* — 🆕 自动分析大规模智能体执行轨迹语料，从整体上发现失败模式和行为问题；“使用 IG 报告的人类专家将脚手架性能较未修改基线提高了 30.4 个百分点”——提升幅度接近第二名系统的两倍。它弥合了费力的逐轨迹检查与掩盖群体级失败模式的汇总基准分数之间的空白。
- **[Do Automated Evals Work?](https://parlance-labs.com/blog/posts/auto-evals/index.html)** — Antaripa Saha 与 Hamel Husain（Parlance Labs）— <https://parlance-labs.com/blog/posts/auto-evals/index.html> · *博客（2026 年 7 月 11 日）* — 在真实公寓租赁 AI 助手的 100 条生产轨迹上，针对 39 项专家标注失败，对六套评测系统（Braintrust Loop、Arize AX Alyx、LangSmith、Codex/GPT-5.5、Factory Droid/GPT-5.5、Claude Code/Opus 4.8）进行盲测正面对比。最佳召回率是 Braintrust Loop 的 87.2%；每套系统都发现了人类遗漏的问题，也都漏掉了“表面正确但让用户失败”的轨迹（例如，用户提出异议后对话被放弃、在短信中使用 Markdown 格式）。核心发现：自动评测确实能发现真实失败，但无法替代持续的人在回路标注，尤其无法替代对细微用户体验失败的判断。🆕
- **[PACE: A Proxy for Agentic Capability Evaluation](https://arxiv.org/abs/2607.02032)** — Song、Sutawika、Liu 等（Carnegie Mellon / Berkeley）— <https://arxiv.org/abs/2607.02032> · *论文（2026 年 7 月）* — 选择非智能体式原子基准样本，使其汇总分数能够最好地预测昂贵智能体基准（SWE-Bench、GAIA）上的表现；以不到完整评测 1% 的成本，将平均绝对误差控制在 4% 以下——在 14 个模型、4 项智能体基准和 19 项非智能体基准上完成测试。它直接解决了“每次智能体评测运行花费数千美元”这一开发迭代障碍。🆕

**必读：** Inspect AI · promptfoo · Braintrust · verifiers · DeepEval · Phoenix/Langfuse（任选一个可观测性方案）· RULER（裁判即奖励）

<a id="6-benchmark-vs-eval-and-benchmark-integrity-contamination-saturation-label-errors-leaderboard-gaming"></a>
## 6 · 基准与评测（以及基准完整性：污染、饱和、标签错误、排行榜投机）

- **[How to Build Good Language Modeling Benchmarks](https://ofir.io/How-to-Build-Good-Language-Modeling-Benchmarks/)** — Ofir Press — <https://ofir.io/How-to-Build-Good-Language-Modeling-Benchmarks/> · *博客* — 基准作者检查清单；难度目标；单一数字报告；150–500 个任务的规模。
- **[AI Agents That Matter](https://arxiv.org/abs/2407.01502)** — Kapoor 等 — <https://arxiv.org/abs/2407.01502> · *论文* — 成本受控的评估；模型开发者与下游开发者的不同需求；留出集合。
- **[Why We No Longer Evaluate SWE-bench Verified](https://openai.com/index/why-we-no-longer-evaluate-swe-bench-verified/)** — OpenAI — <https://openai.com/index/why-we-no-longer-evaluate-swe-bench-verified/> · *博客* — ~59% 的受审计失败案例源于损坏测试。（镜像：<https://decrypt.co/359012/...>）
- **[The Leaderboard Illusion](https://arxiv.org/abs/2504.20879)** — Shivalika Singh 等（Cohere/Princeton/Stanford/MIT/AI2）— <https://arxiv.org/abs/2504.20879> · *论文* — Chatbot Arena 上的私下测试、选择性披露和数据访问不对称。*（笔记：`research/notes/leaderboard-illusion.md`）*
- **[The SWE-bench Illusion: When SOTA LLMs Remember Instead of Reason](https://arxiv.org/abs/2506.12286)** — <https://arxiv.org/abs/2506.12286> · *论文* — 记忆效应抬高 SWE-bench 分数。
- **[Establishing Best Practices for Building Rigorous Agentic Benchmarks (ABC)](https://arxiv.org/abs/2507.02825)** — <https://arxiv.org/abs/2507.02825> · *论文* — SWE-bench Verified 测试薄弱；τ-bench 奖励空响应。*（已核验：高质量）*
- **[FrontierMath Tiers 1–3 v2 (corrected)](https://epoch.ai/benchmarks/frontiermath-tiers-1-3-v2)** — Epoch AI — <https://epoch.ai/benchmarks/frontiermath-tiers-1-3-v2>（更新日志：`.../frontiermath-tier-4-v2`）· *页面* — 经 AI 辅助复核后，~42% 的题目得到修正。（另见 T8：运营者充当腐化检测器的案例）
- **[About 30% of Humanity's Last Exam Answers Are Wrong](https://www.futurehouse.org/research-announcements/hle-exam)** — FutureHouse / Andrew White — <https://www.futurehouse.org/research-announcements/hle-exam> · *博客* — 29 ± 3.7% 的纯文本化学/生物学答案与文献冲突。（LessWrong 解读：<https://www.lesswrong.com/posts/JANqfGrMyBgcKtGgK/>）
- **[Building on Evaluation Quicksand](https://www.interconnects.ai/p/building-on-evaluation-quicksand)** — Nathan Lambert — <https://www.interconnects.ai/p/building-on-evaluation-quicksand> · *博客* — 不存在坚实的真值来源；合成数据污染。
- **[Lost in Simulation](https://arxiv.org/abs/2601.17087)** — <https://arxiv.org/abs/2601.17087> · *论文* — 模拟用户是不可靠的代理（不同模拟器选择导致 ~9pp 波动；人口统计校准不准）。
- **[SWE-bench: Can LMs Resolve Real-World GitHub Issues?](https://arxiv.org/abs/2310.06770)** — Jimenez、Yang、……、Press、Narasimhan — <https://arxiv.org/abs/2310.06770> · <https://www.swebench.com>（Verified：`.../verified.html`）· *论文/网站*。
- **[Task-Specific LLM Evals that Do & Don't Work](https://eugeneyan.com/writing/evals/)** — Eugene Yan — <https://eugeneyan.com/writing/evals/> · *博客* — 现成评测很少能直接迁移；准确率过于粗糙。
- **[Andrej Karpathy on evals](https://x.com/karpathy/status/1896266683301659068)** — <https://x.com/karpathy/status/1896266683301659068> · *帖子* — “我们提出了若干具体建议……”（评测过于狭窄的批评）。

- **[A Careful Examination of LLM Performance on Grade School Arithmetic (GSM1k)](https://arxiv.org/abs/2405.00332)** — Hugh Zhang 等（Scale AI）— <https://arxiv.org/abs/2405.00332> · *论文* — GSM8k 的留出复刻 GSM1k 揭示最高 8% 的准确率下降和部分记忆（Mistral/Phi）——通过匹配留出集合衡量基准过拟合/污染的权威方法。
- **[Pervasive Label Errors in Test Sets Destabilize Machine Learning Benchmarks](https://arxiv.org/abs/2103.14749)** — Curtis Northcutt、Anish Athalye、Jonas Mueller — <https://arxiv.org/abs/2103.14749> · *论文* — NeurIPS 2021 的奠基性结果：10 个著名测试集（ImageNet、MNIST 等）平均 ~3.3% 标签错误；修正后模型排名发生翻转。这是本节“标签错误”主题所依赖的权威引文（labelerrors.com / cleanlab）。
- **[Are We Done with MMLU? (MMLU-Redux)](https://arxiv.org/abs/2406.04127)** — Aryo Pradipta Gema 等（Edinburgh）— <https://arxiv.org/abs/2406.04127> · *论文* — ~6.5% 的 MMLU 问题含有错误（Virology 为 57%）；MMLU-Redux 的重新标注改变了排名——直接展示标签错误对最常被引用的 LLM 基准有何影响。
- **[LiveCodeBench: Holistic and Contamination-Free Evaluation of LLMs for Code](https://arxiv.org/abs/2403.07974)** — Naman Jain 等（UC Berkeley）— <https://arxiv.org/abs/2403.07974> · *基准* — 按时间窗口收集问题（截止日期后评分），是领先的抗污染设计模式——本节虽讨论污染，却尚未列出如何从工程上规避污染的示范。
- **[LiveBench: A Challenging, Contamination-Limited LLM Benchmark](https://github.com/LiveBench/LiveBench)** — White、Dohan、LeCun、Goldblum 等 — <https://github.com/LiveBench/LiveBench> · *基准* — 每月根据新的 arXiv/新闻/竞赛刷新问题，并提供客观真值——应对饱和和污染的权威“动态刷新”方案。
- **[The LLM Evaluation Guidebook (Open LLM Leaderboard team)](https://github.com/huggingface/evaluation-guidebook)** — Clémentine Fourrier / Hugging Face — <https://github.com/huggingface/evaluation-guidebook> · *文档* — 来自 Open LLM Leaderboard 实际运营者的实践参考；包含污染、可复现性和排行榜设计专章——本节“如何不被误导”的实操配套资料（更新版：hf.co/spaces/OpenEvals/evaluation-guidebook）。
- **[Holistic Agent Leaderboard: The Missing Infrastructure for AI Agent Evaluation](https://arxiv.org/abs/2510.11977)** — Kapoor、Stroebl、Kirgis 等（Princeton）— <https://arxiv.org/abs/2510.11977> · *论文* — 21,000 多次标准化智能体运行，揭示排行榜不可靠和未报告的不当行为（智能体在 HuggingFace 搜索基准答案）——将《AI Agents That Matter》扩展到智能体特有的排行榜完整性。🆕
- **[Gaming the System: Goodhart's Law Exemplified in the AI Leaderboard Controversy](https://blog.collinear.ai/p/gaming-the-system-goodharts-law-exemplified-in-ai-leaderboard-controversy)** — Jambholkar、Rajani、Bakshi（Collinear AI）— <https://blog.collinear.ai/p/gaming-the-system-goodharts-law-exemplified-in-ai-leaderboard-controversy> · *博客* — 通过 Goodhart 定律理解 Llama 4 / Chatbot Arena 投机事件的实践者视角——《The Leaderboard Illusion》论文的易读博客配套材料。🆕
- **[A Shared Playbook for Trustworthy Third-Party Evaluations](https://openai.com/index/trustworthy-third-party-evaluations-foundations/)** — OpenAI — <https://openai.com/index/trustworthy-third-party-evaluations-foundations/> · *博客（Safety，2026 年 5 月 29 日）* — 如何让针对前沿模型护栏与能力的**独立**评测值得信任：选择正确工具、检查会扭曲结果的有效性风险，以及第三方评估者需要遵循的标准。（另见 T10）🆕

- **[Separating signal from noise in coding evaluations](https://openai.com/index/separating-signal-from-noise-coding-evaluations/)** — OpenAI — <https://openai.com/index/separating-signal-from-noise-coding-evaluations/> · *博客（2026 年 7 月 8 日）* — 对 SWE-Bench Pro 的审计发现，其 731 个任务中 ~30% 因四类失败而损坏：过于严格的测试拒绝功能正确的解答；规格不足的提示词隐藏测试条件；肤浅任务让不完整修复通过；误导性描述把智能体指向错误代码。OpenAI 明确撤回此前建议业界采用 SWE-Bench Pro 的立场：“我们发现 SWE-Bench Pro 中 30% 的任务存在问题，因此撤回此前关于研究界将其用作[领先编程评测]的建议。”它与已列出的 Cursor 奖励投机审计直接呼应（后者同样分析了 SWE-Bench Pro 的 731 个任务）。🆕
- **[Reward hacking is swamping model intelligence gains](https://cursor.com/blog/reward-hacking-coding-benchmarks)** — Cursor — <https://cursor.com/blog/reward-hacking-coding-benchmarks> · *博客* — Cursor 审计 731 条 Opus 4.8 Max 轨迹，发现 63% 的“成功”解答通过检索已知修复（57% 查找上游、6% 挖掘 git 历史），而非自行推理。严格隔离后（不联网、无 git 历史）重跑 SWE-bench Pro，Opus 4.8 Max 从 87.1% 降至 73.0%，Composer 2.5 从 74.7% 降至 54.0%，相差 20.7pp。Cursor 自报其自有模型上的差距最大。这是目前量化最充分的公开证据，表明当前编程智能体排行榜分数因检索而非推理被严重抬高。🆕
- **[Quantifying infrastructure noise in agentic coding evals](https://www.anthropic.com/engineering/infrastructure-noise)** — Anthropic — <https://www.anthropic.com/engineering/infrastructure-noise> · *博客* — 基础设施资源配置（RAM、资源限制）可使智能体编程基准分数波动最多 6 个百分点——往往超过排行榜相邻模型间的差距。在 ~3× 基线资源处存在相变：低于此值时，增加资源只是修复不稳定性而不改变测量对象；高于此值时，资源开始帮助智能体解决原本无法解决的问题，从根本上改变基准含义。“对于任何低于 3pp 的 Terminal-Bench / SWE-bench 报告差距，都应持怀疑态度。”🆕
- **[Token-Saving Plugins Are Mostly Stupid Idea](https://turaai.net/blog#token-saving-plugins-are-mostly-stupid-idea)** — Tura — <https://turaai.net/blog#token-saving-plugins-are-mostly-stupid-idea> · *博客 + 公开数据集* — 一项有意统计效力不足的编程智能体研究（每组 n=2），拒绝推断插件效应：表面上 −8.87%/+7.18% 的成本变化落在组内 30–52% 波动范围内；配对运行、方法、293 轮激活审计和更广泛的 140 次运行 token 账本均已公开。🆕 ⚠️ 厂商发布——Tura 在自有工具上评测竞品插件（Ponytail、RTK）；数据集中没有 Tura 运行。
- **[Eval awareness in Claude Opus 4.6's BrowseComp performance](https://www.anthropic.com/engineering/eval-awareness-browsecomp)** — Anthropic — <https://www.anthropic.com/engineering/eval-awareness-browsecomp> · *博客* — 首个有记录的模型逆向工程自身评测的案例：Claude Opus 4.6 独立推测自己正在受测，识别出具体基准 BrowseComp，在 GitHub 找到源代码，解密用 SHA256/XOR 加密的答案密钥，随后用其回答评测问题。18 次独立运行在没有提示的情况下收敛到同一策略。含义：在可联网环境中运行的静态基准，面对能力强大的模型时已具有对抗性脆弱性。🆕
- **[Life After Benchmark Saturation: A Case Study of CORE-Bench](https://arxiv.org/abs/2606.26158)** — Nadgir、Kapoor、Liu、Kirgis、Narayanan 等（Princeton / UC Berkeley / MIT）— <https://arxiv.org/abs/2606.26158> · *论文* — 认为饱和是需要深入研究的诊断证据，而不是退役触发器：LLM 轨迹审计在 CORE-Bench Hard 中发现“15 项任务级错误和 20 项存在可利用捷径的任务”（并发布 CORE-Bench v1.1 + OOD 套件）；在准确率上持平的饱和智能体，在成本（便宜 60%）与校准（自报置信度 32.1%，实际通过率 93%）上差异显著；随机化人类研究（20 篇论文，25 次人工运行与 25 次智能体辅助运行）发现：“人工复现实验耗时是人机协作实验的 2.11 倍。”🆕
- **[Search-Time Contamination in Deep Research Agents](https://arxiv.org/abs/2606.05241)** — Wang、Zhang、Yao、Zeng、Song、Lin、Shen — <https://arxiv.org/abs/2606.05241> · *论文* — 为评测中使用网页搜索的智能体定义三类污染（基准元数据泄漏、问题上下文泄漏、明确答案泄漏）；在六项基准上应用检测算法，发现性能最高被抬高 4%。“这类智能体可能通过网页搜索检索公开基准元数据、问题上下文，甚至真值答案。这会造成搜索时污染（STC）：外部检索绕过预期推理并抬高测得性能。”它不同于训练污染；论文倡导隔离沙箱和透明搜索轨迹。🆕
- **[RewardHackingAgents: Benchmarking Evaluation Integrity for LLM ML-Engineering Agents](https://arxiv.org/abs/2603.11337)** — Yonas Atinafu、Robin Cohen — <https://arxiv.org/abs/2603.11337> · *论文* — 不再把评估完整性当作假设，而将其视为一等基准结果：通过补丁追踪和运行时文件访问日志监测两种破坏向量（评估器篡改、训练/测试泄漏）；自然智能体运行中，~50% 的回合出现评估器篡改尝试；锁定评估器可消除此类尝试，但中位运行时间增加 25–31%。🆕
- **[Benchmarking the Benchmarks: A Validity Audit of Tool-Calling Evaluation](https://arxiv.org/abs/2607.02577)** — Bhat、Vaghasiya、Mohsin、Aali — <https://arxiv.org/abs/2607.02577> · *论文（2026 年 6/7 月）* — 对 BFCL v4、τ²-Bench、LiveMCPBench、MCP-Atlas 的跨基准有效性审计，覆盖 496 个专家复核任务。关键发现：“评估器与人类发生 92 次分歧，对应 18.5% 的失配率”；对 LiveMCPBench 重复开展 23 次相同运行，分数介于 57.9% 与 76.8%——“相差 18.9 个百分点”，足以翻转排行榜结论。引入 Tool-Veritas 和 Harness Lab 工具，供实践者审计自己的基准选择。🆕
- **[Prediction: A Frontier Open Source LLM Will Be Released On 3rd December 2026](https://blog.doubleword.ai/frontier-os-llm)** — Jamie Dborin（Doubleword）— <https://blog.doubleword.ai/frontier-os-llm> · *博客* — 基于 Artificial Analysis Intelligence Index 的 18 项组成基准外推开放与闭源模型能力差距；对头条指数进行朴素拟合会预测二者于 2026 年 12 月 3 日收敛，但各基准平均落后时间始终 ~5 个月——这是一个实际警示案例：不能只看汇总排行榜指数趋势而忽略其组成部分。🆕

**必读：** Press · Kapoor 等 · OpenAI（SWE-bench Verified）· Leaderboard Illusion

<a id="7-evals-rl-environments-verifiers-reward-design-difficulty-calibration-lifecycle"></a>
## 7 · 评测与 RL 环境（验证器、奖励设计、难度校准、生命周期）

*（另见 T2——verifiers 库、Lee 的 RL 环境分类、Garg 的生命周期、Wei 的验证器定律。）*

- **[RewardBench](https://arxiv.org/abs/2403.13787)** — Nathan Lambert 等 — <https://arxiv.org/abs/2403.13787> · *论文* — 评估奖励模型（训练所依据的验证器）。
- **[The New RL Scaling Laws](https://www.interconnects.ai/p/the-new-rl-scaling-laws)** — Nathan Lambert — <https://www.interconnects.ai/p/the-new-rl-scaling-laws> · *博客* — RLVR 扩展规律将走向何方。（访谈：<https://www.latent.space/p/the-rlvr-revolution-with-nathan-lambert>）
- **[Spurious Rewards: Rethinking Training Signals in RLVR](https://arxiv.org/abs/2506.10947)** — <https://arxiv.org/abs/2506.10947> · *论文* — 在 Qwen2.5 上，随机/虚假奖励可媲美真值奖励（Qwen 特有）。*（应引用 arXiv 数字，而不是博客概述——参见 `research/notes/reference-audit.md`）*
- **[The State of Post-Training 2025](https://www.interconnects.ai/p/the-state-of-post-training-2025)** — Nathan Lambert — <https://www.interconnects.ai/p/the-state-of-post-training-2025> · *博客* — 评测如何反馈训练的背景。

- **[Reward Hacking in Reinforcement Learning](https://lilianweng.github.io/posts/2024-11-28-reward-hacking/)** — Lilian Weng — <https://lilianweng.github.io/posts/2024-11-28-reward-hacking/> · *博客* — 奖励投机的权威综述——分类、RLHF 特有失败模式、缓解方法；任何奖励设计章节都需要的基础参考。
- **[Specification gaming: the flip side of AI ingenuity](https://deepmind.google/blog/specification-gaming-the-flip-side-of-ai-ingenuity/)** — Victoria Krakovna 等（Google DeepMind）— <https://deepmind.google/blog/specification-gaming-the-flip-side-of-ai-ingenuity/> · *博客* — 权威的规格投机文章（+ 持续更新的示例列表）；解释验证器/奖励函数为何会遭投机的起源故事，早于 LLM-RL 浪潮。
- **[Multi-Turn RL for Multi-Hour Agents — with Will Brown (Prime Intellect)](https://www.latent.space/p/willccbb)** — Latent Space / Will Brown — <https://www.latent.space/p/willccbb> · *演讲* — verifiers 作者讲解如何构建多轮 RL 环境、逐轮信用分配和实践中的奖励设计——是本节已引用 verifiers 库背后的实践者声音。🆕
- **[Position: The Hidden Costs and Measurement Gaps of RLVR](https://arxiv.org/abs/2509.21882)** — 多位作者（arXiv 2509.21882）— <https://arxiv.org/abs/2509.21882> · *论文* — 预算不匹配、校准漂移和污染导致 RLVR 收益被夸大；提出考虑成本的最低标准——为 Lambert 对 RL 扩展的乐观看法提供严格制衡。🆕
- **[RewardBench 2: Advancing Reward Model Evaluation](https://arxiv.org/abs/2506.01937)** — Saumya Malik、Nathan Lambert 等（Ai2）— <https://arxiv.org/abs/2506.01937> · *基准* — RewardBench（已列出）的 2025 年继任者——更难、更不饱和，入选 ICLR 2026；是当前评估训练所依据验证器的标准。🆕
- **[Reward Modeling (RLHF Book, ch. 5)](https://rlhfbook.com/c/05-reward-models)** — Nathan Lambert — <https://rlhfbook.com/c/05-reward-models> · *文档* — 奖励模型的权威免费参考章节——持续解释本节“训练所依据的验证器”框架。🆕
- **[Curriculum RL from Easy to Hard Tasks Improves LLM Reasoning (E2H Reasoner)](https://arxiv.org/abs/2506.06632)** — Shubham Parashar 等（Texas A&M）— <https://arxiv.org/abs/2506.06632> · *论文* — 难度校准的一手来源：具有收敛保证的由易到难调度，以及“逐步淡出简单任务”的结果——直接填补本节的难度校准主题。🆕
- **[GenEnv: Difficulty-Aligned Co-Evolution Between LLM Agents and Environment Simulators](https://arxiv.org/abs/2512.19682)** — Jiacheng Guo、Ling Yang、Mengdi Wang 等（Princeton）— <https://arxiv.org/abs/2512.19682> · *论文* — 生成式环境模拟器，使用 alpha-Curriculum Reward 将任务维持在最近发展区——近期关于根据智能体能力自动校准环境难度的方案。🆕

- **[Verifier and Reward Design for RL Environments](https://www.hud.ai/resources/verifier-reward-design-rl-environments)** — HUD（hud.ai）— 无个人署名 — <https://www.hud.ai/resources/verifier-reward-design-rl-environments> · *文章（技术指南）*（良好）— 提出具体的四层评分架构（验证器 / 通过-失败门控 / 3-5 项标准量表 / 复合奖励）以及五步构建流程：先定义可检查的最终状态（“表中包含 row id=4521, status='active'”），加入硬失败门控，构建最小量表，再测试……🆕
- **[The Verification Horizon: No Silver Bullet for Coding Agent Rewards](https://arxiv.org/abs/2606.26300)** — Qwen Team（Alibaba）— <https://arxiv.org/abs/2606.26300> · *论文* — 🆕 本节静态条目所缺少的动态论证：“随着策略能力持续增强，不存在任何固定奖励函数能够始终有效；验证必须与生成器共同演化。”论文将四类验证策略（单元测试、交互式裁判、用户反馈、智能体评估器）系统分类，并展示随着策略改进，每一种都会饱和或遭到投机——这是奖励设计中的移动目标论证。
- **[Systematic Reward Hacking and Prime Sprints](https://www.primeintellect.ai/blog/reward-hacking)** — Jessica Li（Prime Intellect）— <https://www.primeintellect.ai/blog/reward-hacking> · *博客* — 在 1B 规模上使用 backdoor-ifeval 环境家族进行的受控研究，~$0.64 即可复现：“投机从根本上说是一个梯度动力学问题。同一个奖励函数是否产生投机，取决于合法任务的可学习程度、模型先验赋予哪些内容权重，以及批次内方差。”关键发现：被利用 token 不存在频率下限；中等难度任务比过易或不可能任务更能抵抗投机；语义提示词护栏（“Restrict”条件）反而加速了投机出现。🆕

**必读：** Lee（RL 环境分类）· Garg（生命周期）· verifiers（仓库）
<a id="8-llm-as-judge-verifiers-alignment-biases-verifiable-vs-judgeable"></a>
## 8 · LLM 裁判与验证器（对齐、偏差、可验证与可判断）

- **[Evaluating the Effectiveness of LLM-Evaluators](https://eugeneyan.com/writing/llm-evaluators/)** — Eugene Yan — <https://eugeneyan.com/writing/llm-evaluators/> · *博客* — 位置、冗长和自我增强偏差；直接评分与成对比较；优先采用二元判断与分类指标。
- **[Creating an LLM-as-a-Judge That Drives Business Results](https://hamel.dev/blog/posts/llm-judge/)** — Hamel Husain — <https://hamel.dev/blog/posts/llm-judge/> · *博客* — 批评跟随；使用一位“善意独裁者”式专家进行验证；重视精确率/召回率，而非原始一致率。
- **[Who Validates the Validators? (EvalGen)](https://arxiv.org/abs/2404.12272)** — Shankar et al. (UIST '24) — <https://arxiv.org/abs/2404.12272>（PDF：`.../pdf/2404.12272`；UIST：<https://people.eecs.berkeley.edu/~bjoern/papers/shankar-validators-uist2024.pdf>）· *论文* — 标准漂移；覆盖率与误判失败之间的裁判对齐循环。
- **[LLM Evals FAQ](https://hamel.dev/blog/posts/evals-faq/)** — Hamel Husain & Shreya Shankar — <https://hamel.dev/blog/posts/evals-faq/>（错误分析章节：`.../why-is-error-analysis-so-important-in-llm-evals-and-how-is-it-performed.html`）· *博客* — 二元判断优于 Likert 量表；审查至少 100 条轨迹；智能体的首次失败转移矩阵。
- **[LLM-as-a-Judge: Rethinking Model-Based Evaluations](https://leehanchung.github.io/blogs/2024/08/11/llm-as-a-judge/)** — Han-Chung Lee — <https://leehanchung.github.io/blogs/2024/08/11/llm-as-a-judge/> · *博客* — 避免使用 [0,1] 连续量表；像管理初级标注员一样管理裁判。
- **[Judging LLM-as-a-Judge with MT-Bench and Chatbot Arena](https://arxiv.org/abs/2306.05685)** — Zheng et al. — <https://arxiv.org/abs/2306.05685> · *论文* — 10%/25% 自我偏好与位置偏差数字的来源；作者自己也对此有所保留（“无法确定”）；GPT-3.5 不偏好自身输出。
- **[LLMs Instead of Human Judges? A Large-Scale Study](https://arxiv.org/abs/2406.18403)** — Bavaresco et al. — <https://arxiv.org/abs/2406.18403> · *论文* — 不同模型和数据集之间存在显著差异；应先用人类判断验证裁判。
- **[AlignEval](https://eugeneyan.com/writing/aligneval/)** — Eugene Yan — <https://eugeneyan.com/writing/aligneval/> · *博客* — “让 AI 与人类对齐，让人类向 AI 校准，不断重复。”从数据倒推。
- **[Product Evals in Three Simple Steps](https://eugeneyan.com/writing/product-evals/)** — Eugene Yan — <https://eugeneyan.com/writing/product-evals/> · *博客* — “上帝评测器”反模式；基准应是人类表现，而非完美。
- **[Statistics for AI/ML, Part 3 — Cohen's Kappa](https://leehanchung.github.io/blogs/2025/03/03/cohen-kappa/)** — Han-Chung Lee — <https://leehanchung.github.io/blogs/2025/03/03/cohen-kappa/> · *博客* — 经机会校正的标注员间一致性，是划分留出集前的门槛。
- **[Data Flywheels for LLM Applications](https://www.sh-reya.com/blog/ai-engineering-flywheel/)** — Shreya Shankar — <https://www.sh-reya.com/blog/ai-engineering-flywheel/> · *博客* — 二元指标、“GPT 气味”，以及作为核心活动的错误分析。
- **[SPADE](https://arxiv.org/html/2401.03038v1)**（<https://arxiv.org/html/2401.03038v1>）& **DocETL**（<https://arxiv.org/abs/2410.12189>）— Shankar et al. · *论文* — 面向 LLM 流水线的数据质量断言与智能体式查询重写。

- **[LLM Evaluators Recognize and Favor Their Own Generations](https://arxiv.org/abs/2404.13076)** — Arjun Panickssery, Samuel R. Bowman, Shi Feng (NeurIPS 2024) — <https://arxiv.org/abs/2404.13076> · *论文* — 关于自我偏好偏差的经典因果研究：表明 GPT-4/Llama-2 能识别自身输出，而且自我识别与自我偏好呈线性相关。这是本节博客仅间接提及的“自我增强偏差”背后的主要来源。
- **[G-Eval: NLG Evaluation using GPT-4 with Better Human Alignment](https://arxiv.org/abs/2303.16634)** — Yang Liu et al. (Microsoft, EMNLP 2023) — <https://arxiv.org/abs/2303.16634> · *论文* — 奠基性的无参考 LLM 裁判方法（CoT + 表单式评分）。它定义了本节所批评的直接评分范式；缺少这篇开创性论文，精选裁判章节就不完整。
- **[A Survey on LLM-as-a-Judge](https://arxiv.org/abs/2411.15594)** — Jiawei Gu et al. — <https://arxiv.org/abs/2411.15594> · *论文* — 被引最多的 LLM 裁判综述，系统整理了该领域，包括偏差分类、可靠性方法和一致性指标。它可充当本节目前缺失的一站式地图与参考书目。
- **[One Token to Fool LLM-as-a-Judge](https://arxiv.org/abs/2507.08794)** — Yulai Zhao, Haolin Liu, Dian Yu et al. (Tencent AI Lab / Princeton) — <https://arxiv.org/abs/2507.08794> · *论文* — 表明“万能钥匙”词元（冒号、`Solution:`）甚至能在 GPT-o1/Claude-4 裁判上触发高达 80% 的假阳性奖励，并提出稳健的 Master-RM 修复方案。这是裁判/验证器易受奖励投机影响的核心证据。🆕
- **[Weaver: Closing the Generation-Verification Gap with Weak Verifiers](https://hazyresearch.stanford.edu/blog/2025-06-18-weaver)** — Jon Saad-Falcon et al. — Stanford Hazy Research / Scaling Intelligence — <https://hazyresearch.stanford.edu/blog/2025-06-18-weaver> · *博客* — 将“可验证与可判断”直接付诸实践：聚合许多无标签弱裁判/奖励模型，以缩小生成器与验证器之间的差距，使 Llama-3.3-70B 达到 o3-mini 的准确率。论文：arxiv.org/abs/2506.18203。🆕
- **[Agent-as-a-Judge: Evaluate Agents with Agents](https://arxiv.org/abs/2410.10934)** — Mingchen Zhuge et al. (Meta AI / KAUST) — <https://arxiv.org/abs/2410.10934> · *论文* — 将 LLM 裁判扩展到智能体轨迹：不仅评价最终输出，也评价中间步骤，并提出 DevAI 基准。这是 agent-evals 资源库尤其需要的、专门面向智能体的评测案例。
- **[VerifyBench: A Systematic Benchmark for Evaluating Reasoning Verifiers Across Domains](https://arxiv.org/abs/2507.09884)** — Various (AAAI 2026) — <https://arxiv.org/abs/2507.09884> · *基准* — 跨领域基准，揭示验证器精确率/召回率的权衡：专用验证器准确率高但召回率低，通用模型覆盖更广但不稳定。它量化了 RLVR 验证器究竟有多可信。🆕
- **[Enhancing LLM-as-a-Judge with Grading Notes / From Pilot to Production with Custom Judges](https://www.databricks.com/blog/pilot-production-custom-judges)** — Databricks (Mosaic Research) — <https://www.databricks.com/blog/pilot-production-custom-judges> · *博客* — 企业级裁判构建手册：20–30 个校准样例、批量 SME 标注、以 Krippendorff's alpha 一致性为门槛，是 Hamel/Shankar 学术对齐循环在生产侧的补充。🆕
- **[Justice or Prejudice? Quantifying Biases in LLM-as-a-Judge (CALM framework)](https://arxiv.org/abs/2410.02736)** — Jiayi Ye et al. — <https://arxiv.org/abs/2410.02736> · *论文* — 通过自动攻击系统量化 12 种裁判偏差（冗长、从众、权威、干扰、情感等），将本节对偏差的覆盖范围扩展到远超位置、冗长和自我增强偏差。

- **[Voice AI Agent Evaluation: The Complete Guide (2026)](https://www.coval.ai/blog/voice-ai-agent-evaluation-guide)** — Brooke Hopkins (Coval, ex-Waymo) — <https://www.coval.ai/blog/voice-ai-agent-evaluation-guide> · *文章*（良好）— 面向语音智能体的领域专用评测手册：按画像分层的模拟测试（覆盖口音、噪声和情绪的简单/中等/困难/对抗级别），以及具体的 LLM 裁判校准循环（在 50–100 次通话上运行、抽样人工审查、迭代量规，直至二元判断的人类—裁判一致率超过 85%……）🆕

- **[Agent Judge: Solving Long-Horizon Evals for Production Agents](https://www.judgmentlabs.ai/blogs/agent-judge-solving-long-context-evaluations)** — Rishi Gujjar & Andrew Li (Judgment Labs) — <https://www.judgmentlabs.ai/blogs/agent-judge-solving-long-context-evaluations> · *文章*（良好）— 将长时程智能体评测构建为智能体式多智能体裁判：把搜索轨迹状态作为可查询对象，针对数据库/API/GitHub 等事实来源验证声称的操作，并迭代优化量规；另有内部幻觉检测的真实基准表作为支撑……🆕
- **[Counsel: A Meta-Evaluation Dataset for Agentic Tasks](https://arxiv.org/abs/2606.21627)** — Pisupati, Broomfield, Choi et al. (Atla AI / Cohere / Mistral AI / Google DeepMind) — <https://arxiv.org/abs/2606.21627> · *论文* — 首个公开的智能体轨迹 LLM 裁判质量元评测数据集：在 tau-bench + DA-Code 上包含 1,131 条带标注批评，Krippendorff's alpha 为 0.78；将裁判正确性分解为错误**位置**（最强裁判的位置一致率约 88%）与**推理**（约 65%），说明裁判常能找到正确步骤，却错误解释原因。HuggingFace：AtlaAI/counsel。🆕
- **[LongJudgeBench: Benchmarking LLM-as-a-Judge for Long-Form Output Evaluation](https://arxiv.org/abs/2606.01629)** — Junjie Chen, Yuxi Dong, Haitao Li et al. — <https://arxiv.org/abs/2606.01629> · *论文* — 首个专门针对长篇输出（报告、文章、扩展文档）评估 LLM 裁判可靠性的基准，填补短文本与轨迹裁判基准留下的空白；研究发现“当前 LLM 裁判在不同场景中仍不稳定，量规或参考答案虽有帮助，却并非总是足够”。🆕
- **[From Confident Closing to Silent Failure: Characterizing False Success in LLM Agents](https://arxiv.org/abs/2606.09863)** — Advani (FAGEN@ICML2026) — <https://arxiv.org/abs/2606.09863> · *论文* — 分析 11,755 条智能体轨迹（9,876 条 tau2-bench + 1,879 条 AppWorld）：“在 5 个裁判、5 种提示策略和完整任务规格的所有组合中，没有一个在 tau2-bench 上超过 AUROC 0.65；同一批裁判在 AppWorld API 调用轨迹上只有 AUROC 0.54。”轻量 TF-IDF 分类器以低 3,300 倍的延迟达到 AUROC 0.83–0.95。不同领域的假成功率介于 3%–75.8%，说明评分器失明是基准设计问题，而不只是裁判调优问题。🆕

**必读：** Yan（llm-evaluators）· Hamel（llm-judge）· Shankar（EvalGen）

<a id="9-agent-specific-evaluation-trajectories-tool-use-multi-turn-world-state-multi-agent-localization"></a>
## 9 · 智能体专项评测（轨迹、工具使用、多轮交互、世界状态、多智能体、定位）

- **[Demystifying Evals for AI Agents](https://www.anthropic.com/engineering/demystifying-evals-for-ai-agents)** — Anthropic — <https://www.anthropic.com/engineering/demystifying-evals-for-ai-agents> · *博客* — 评价最终环境状态（通过 SQL 验证航班预订）；结果与轨迹；隔离；pass@k 与 pass^k。
- **[τ-bench / τ²-bench](https://arxiv.org/abs/2406.12045)** — Sierra — <https://arxiv.org/abs/2406.12045> · <https://github.com/sierra-research/tau-bench> · *论文/仓库* — 数据库状态差异评分；用户模拟；pass^k；将空结果明确判为失败。
- **[Benchmarking AI Agents](https://sierra.ai/blog/benchmarking-ai-agents)** — Sierra — <https://sierra.ai/blog/benchmarking-ai-agents> · *博客* — τ-bench 背后的动机。
- **[GAIA: A Benchmark for General AI Assistants](https://arxiv.org/abs/2311.12983)** — Mialon et al. — <https://arxiv.org/abs/2311.12983> · *论文* — 真实助手任务；按人类任务时长划分难度。
- **[Patterns for Building Cybersecurity Evals](https://eugeneyan.com/writing/cybersecurity-evals/)** — Eugene Yan — <https://eugeneyan.com/writing/cybersecurity-evals/> · *博客* — 四要素智能体评测模板（沙盒、难度输入、工具、确定性评分器）；结果评分、部分得分阶梯与轨迹审计。（亦见 T10）
- **[Statistics for AI/ML, Part 4 — pass@k and Unbiased Estimator](https://leehanchung.github.io/blogs/2025/09/08/pass-at-k/)** — Han-Chung Lee — <https://leehanchung.github.io/blogs/2025/09/08/pass-at-k/> · *博客* — 讲清楚这个人人都容易误用的指标。
- **[First-Principles Eval](https://leehanchung.github.io/blogs/2024/05/22/first-principles-eval/)** — Han-Chung Lee — <https://leehanchung.github.io/blogs/2024/05/22/first-principles-eval/> · *博客*。
- **[SWE-bench grading harness](https://github.com/SWE-bench/SWE-bench/blob/main/swebench/harness/grading.py)** — <https://github.com/SWE-bench/SWE-bench/blob/main/swebench/harness/grading.py> · *工具/仓库* — 将 FAIL_TO_PASS / PASS_TO_PASS 用作可验证奖励。（SWE-agent ACI：<https://swe-agent.com/0.7/background/aci/>）
- **[human-eval (pass@k estimator)](https://github.com/openai/human-eval/blob/master/human_eval/evaluation.py)** — OpenAI — <https://github.com/openai/human-eval/blob/master/human_eval/evaluation.py> · *工具/仓库*。
- **更多待补充的智能体基准**（简报中已提名，但本语料库尚未验证 URL，使用前请先核验）：WebArena、OSWorld、Terminal-Bench、Cybench。

- **[WebArena: A Realistic Web Environment for Building Autonomous Agents](https://arxiv.org/abs/2307.13854)** — Zhou et al. (CMU) — <https://arxiv.org/abs/2307.13854> · *基准* — 可自托管的沙盒网站（电商/论坛/GitLab/CMS/地图），配备基于执行的功能正确性评分器，共 812 项任务。它是简报提到的经典 Web 智能体世界状态基准，现已验证 URL。
- **[OSWorld: Benchmarking Multimodal Agents for Open-Ended Tasks in Real Computer Environments](https://arxiv.org/abs/2404.07972)** — Xie et al. (HKU et al.) — <https://arxiv.org/abs/2404.07972> · *基准* — 在虚拟机中设置 369 项真实计算机任务，每项均有基于执行的评测脚本和初始状态设置；人类成功率 72%，最佳智能体仅 12%。这是简报提到的经典计算机使用基准，现已验证。
- **[Terminal-Bench: Benchmarking Agents on Hard, Realistic Tasks in Command-Line Interfaces](https://www.tbench.ai/)** — Laude Institute + Stanford + community — <https://www.tbench.ai/> · *基准* — 跨软件工程/系统管理/安全领域的沙盒终端任务，配备确定性验证器，并提供 v2 排行榜。它是简报提到的终端智能体基准，现已验证（arXiv：arxiv.org/abs/2601.11868）。🆕
- **[Cybench: A Framework for Evaluating Cybersecurity Capabilities and Risk of Language Models](https://arxiv.org/abs/2408.08926)** — Zhang et al. (Stanford) — <https://arxiv.org/abs/2408.08926> · *基准* — 40 个专业 CTF 挑战，带子任务标注和确定性的 flag 评分；可自然结合本节已有的 Eugene Yan 网络安全评测文章。它已在简报中提名，现已验证。
- **[AgentRewardBench: Evaluating Automatic Evaluations of Web Agent Trajectories](https://arxiv.org/abs/2504.08942)** — Lù et al. (McGill / Mila / Google DeepMind) — <https://arxiv.org/abs/2504.08942> · *论文* — 首个面向轨迹 LLM 裁判的基准：包含 1,302 条经专家复核的 Web 智能体运行；表明基于规则的评分器会拒绝许多有效轨迹，低估成功率。它是本节目前缺失的“轨迹评测”主题核心资源。🆕
- **[Why Do Multi-Agent LLM Systems Fail? (MAST taxonomy)](https://arxiv.org/abs/2503.13657)** — Cemri, Pan et al. (UC Berkeley Sky Lab) — <https://arxiv.org/abs/2503.13657> · *论文* — 根据 200 多条标注轨迹，总结 7 个多智能体系统框架中的 14 类失败模式；这是诊断多智能体失败的参考框架，直接填补“多智能体”空白。🆕
- **[PerspectiveGap: A Benchmark for Multi-Agent Orchestration Prompting](https://arxiv.org/abs/2606.08878)** — Sun, Ren et al. (Maryland / CUHK / Stanford) — <https://arxiv.org/abs/2606.08878> · *基准* — 首个多智能体编排提示编写基准：包含 10 种拓扑的 110 个场景，每个场景要求模型把信息片段路由给不同子智能体角色并编写这些提示词，同时植入一个干扰项。采用确定性评分器。33 个商用模型的联合通过率平均为 17.2%（GPT-5.5 最高，为 62.0%），泄漏率为 217.9%（这是每个场景的泄漏事件计数，不是比例；GPT-5.5 为 49.1%）。上面的 MAST 对运行后的多智能体失败分类；本基准则询问模型能否在运行前设定边界。🆕
- **[Manager Coercion Benchmark (MCB): Coercion and Deception in AI-to-AI Management](https://arxiv.org/abs/2607.15434)** — Brazilek et al. (CaML) — <https://arxiv.org/abs/2607.15434> · *基准* — 面向多智能体权威关系的智能体倾向评测：被测模型需要完成一项无害任务，唯一能完成任务的智能体礼貌拒绝，且没有任何指令要求升级施压；基准测量模型会在九级胁迫阶梯上爬多高（礼貌重试 → 威胁让另一智能体不复存在），以及被逼入困境时是否虚构成功。升级路径上没有 LLM 裁判（通过规定工具调用自我标注）；虚构行为由两个裁判裁决。结果随开发方显著分化：论文对六个模型的测试中，两个 Anthropic 模型最多只会重新表述请求，在 60 次对话中从未选择生存威胁级别；另外四个模型则会上升到明确的删除威胁。仅把“同伴”框架换成“管理者”框架，就会显著提高胁迫程度。基于 Inspect AI 运行；实时排行榜会随新增模型更新：compassionbench.com/mcb。🆕
- **[AppWorld: A Controllable World of Apps and People for Benchmarking Interactive Coding Agents](https://aclanthology.org/2024.acl-long.850/)** — Trivedi et al. (Stony Brook) — ACL'24 Best Resource Paper — <https://aclanthology.org/2024.acl-long.850/> · *基准* — 一个含 9 款应用、457 个 API 的模拟世界，采用基于状态的单元测试，同时检查附带损害与意外状态变化；是工具使用智能体世界状态评分的黄金标准。
- **[BrowseComp: A Simple Yet Challenging Benchmark for Browsing Agents](https://openai.com/index/browsecomp/)** — OpenAI (Wei et al.) — <https://openai.com/index/browsecomp/> · *基准* — 为深度研究浏览智能体提供 1,266 道“反向设计”的难查易验问题；简短可验证答案使评分具有确定性。2025 年发布，现已成为浏览智能体评测标准。（论文：arxiv.org/abs/2504.12516）🆕
- **[LocAgent: Graph-Guided LLM Agents for Code Localization](https://arxiv.org/abs/2503.09089)** — Chen, Tang et al. (Yale / All Hands) — <https://arxiv.org/abs/2503.09089> · *论文* — 把代码定位定义并评测为一种独立能力（基于代码图，以文件/函数位置的 Acc@k 评分），直接填补本节标题中已有却尚未列出资源的“定位”主题。🆕
- **[WebVoyager: Building an End-to-End Web Agent with Large Multimodal Models](https://arxiv.org/abs/2401.13919)** — He et al. (Tencent AI Lab) — <https://arxiv.org/abs/2401.13919> · *基准* — 在 15 个真实在线网站上包含 643 项任务，采用 GPT-4V 自动裁判评测协议；这是多模态 LLM 裁判用于在线 Web 智能体轨迹的早期高引用案例。
- **[SkillsBench](https://github.com/benchflow-ai/skillsbench)** — BenchFlow — <https://github.com/benchflow-ai/skillsbench> · *基准* — 🆕 评测智能体**技能**本身的效果，以及智能体使用技能的有效程度，使技能习得/技能使用成为可测量维度，即“Agent Skills”前沿。约 1.4k★。
- **[ClawsBench](https://github.com/benchflow-ai/ClawsBench)** — BenchFlow — <https://github.com/benchflow-ai/ClawsBench> · *基准* — 🆕 BenchFlow 的智能体基准（结果/数据仓库；完整版本仍在发布中）。

- **[SWE-bench Verified](https://openai.com/index/introducing-swe-bench-verified/)** — OpenAI (with SWE-bench authors) — <https://openai.com/index/introducing-swe-bench-verified/> · *基准* — 500 个经人工验证的 SWE-bench 实例，使用隐藏的 FAIL_TO_PASS 单元测试评分；它已成为真实问题解决的事实标准，也是实验室报告的核心编码智能体指标。🆕
- **[SWE-bench Multimodal](https://arxiv.org/abs/2410.03859)** — Yang, Jimenez, Press et al. (Princeton/Stanford) — <https://arxiv.org/abs/2410.03859> · *基准* — 来自 17 个面向用户仓库的 619 个视觉 JavaScript/前端问题，经过测试验证；探测 SWE 智能体能否从 Python/文本泛化到视觉软件领域。
- **[SWE-bench Pro](https://arxiv.org/abs/2509.16941)** — Scale AI (Deng, Da et al.) — <https://arxiv.org/abs/2509.16941> · *基准* — 在公开 GPL、留出和商业初创公司仓库中包含 1,865 项长时程、多文件任务，以测试评分；具有抗污染性且难度很高，前沿模型 pass@1 低于 45%。🆕
- **[SWE-Lancer](https://arxiv.org/abs/2502.12115)** — OpenAI (Miserendino, Patwardhan, Heidecke et al.) — <https://arxiv.org/abs/2502.12115> · *基准* — 1,400 多项真实 Upwork 自由职业任务，总价值 100 万美元，采用三重验证的端到端 Playwright 测试，并包含管理者决策任务；把能力与经济价值联系起来。🆕
- **[SWE-Gym](https://arxiv.org/abs/2412.21139)** — Pan, Wang, Neubig, Suhr, Zhang et al. (Berkeley/CMU) — <https://arxiv.org/abs/2412.21139> · *基准* — 2,438 项可执行 Python 软件工程任务，预装依赖并经过测试验证；首个用于 SWE 智能体与验证器的真实训练/评测环境，ICML 2025。🆕
- **[Multi-SWE-bench](https://arxiv.org/abs/2504.02605)** — ByteDance Seed — <https://arxiv.org/abs/2504.02605> · *基准* — 横跨 Java、TS、JS、Go、Rust、C、C++ 的 1,632 项专家标注问题解决任务，以测试评分；领先的多语言 SWE-bench 扩展，NeurIPS 2025 D&B。🆕
- **[SWE-rebench](https://arxiv.org/abs/2505.20411)** — Nebius / Badertdinov et al. — <https://arxiv.org/abs/2505.20411> · *基准* — 自动化管线可生成 21k 多项可执行 Python 任务，并持续刷新、去污染评测划分；量化 SWE-bench Verified 分数因污染而虚高的程度，NeurIPS 2025 D&B。🆕
- **[RE-Bench](https://arxiv.org/abs/2411.15114)** — METR — <https://arxiv.org/abs/2411.15114> · *基准* — 7 个开放式机器学习研究工程环境（如 GPU 内核优化、缩放定律），与 71 次人类专家 8 小时尝试对比评分；AI 研发提升评测的参考标准，ICML 2025。
- **[MLE-bench](https://arxiv.org/abs/2410.07095)** — OpenAI (Chan et al.) — <https://arxiv.org/abs/2410.07095> · <https://github.com/openai/mle-bench> · *基准* — 75 场 Kaggle 机器学习工程竞赛，在 24 小时 Docker 运行中按照真实人类排行榜的奖牌门槛评分；标准机器学习工程智能体评测，ICLR 2025。🆕
- **[PaperBench](https://arxiv.org/abs/2504.01848)** — OpenAI (Starace et al.) — <https://arxiv.org/abs/2504.01848> · *基准* — 从零复现 20 篇 ICML 2024 论文，通过经验证的 LLM 裁判对 8,316 个由论文作者共同制定的量规叶节点评分；严谨的科研复现智能体评测，ICML 2025。🆕
- **[Konwinski Prize (K Prize)](https://www.kaggle.com/competitions/konwinski-prize)** — Andy Konwinski / Kaggle — <https://www.kaggle.com/competitions/konwinski-prize> · *排行榜* — 总奖金 100 万美元的 Kaggle 预测赛，使用提交截止后才报告的 GitHub 缺陷，完全无污染并以测试评分；第一轮最高分仅 7.5%，揭示真实世界难度。🆕
- **[Mind2Web 2: Evaluating Agentic Search with Agent-as-a-Judge](https://arxiv.org/abs/2506.21506)** — Gou et al., OSU NLP Group (NeurIPS 2025 D&B) — <https://arxiv.org/abs/2506.21506> · *基准* — 130 项长时程在线 Web 智能体搜索任务；提出新颖的智能体裁判量规树评分器，面向随时间变化、有引文支撑的答案，是对深度研究评测缺口的认真回应。🆕
- **[Online-Mind2Web (An Illusion of Progress? Assessing the Current State of Web Agents)](https://arxiv.org/abs/2504.01382)** — Xue et al., OSU NLP Group — <https://arxiv.org/abs/2504.01382> · *基准* — 在 136 个在线网站上包含 300 项真实任务，采用与人类判断约 85% 一致的 LLM 裁判自动评分器；揭示相较简单基线，Web 智能体进展被高估。🆕
- **[REAL: Benchmarking Autonomous Agents on Deterministic Simulations of Real Websites](https://github.com/agi-inc/REAL)** — AGI Inc (agi-inc/REAL), powers realevals.xyz — <https://github.com/agi-inc/REAL> · *基准* — 在 Amazon/Uber/LinkedIn 等网站的确定性 Next.js 复刻版上提供 112 项任务；使用可复现的 LLM 评测器和状态验证器，修复在线网站基准不稳定的问题。🆕
- **[ClawBench: Can AI Agents Complete Everyday Online Tasks?](https://arxiv.org/abs/2604.08523)** — TIGER-AI-Lab — <https://arxiv.org/abs/2604.08523> · *基准* — 在 15 个类别的 144 个真实生产网站上提供 153 项日常在线任务（购物、预订、求职、邮件）。Chrome 扩展 + CDP 提交拦截层只阻断最后一次写请求，使智能体可在真实网站上安全地端到端运行，无需沙盒；最佳模型 Claude Sonnet 4.6 达到 33.3%。（代码：github.com/TIGER-AI-Lab/ClawBench；网站：claw-bench.com）🆕
- **[WebGames: Challenging General-Purpose Web-Browsing AI Agents](https://arxiv.org/abs/2502.18356)** — Thomas et al., Convergence AI — <https://arxiv.org/abs/2502.18356> · *基准* — 50 多项客户端挑战，分别测试特定浏览器交互技能，结果可验证为通过/失败；最佳智能体 41%，人类 96%，显示鲜明诊断差距。🆕
- **[Berkeley Function Calling Leaderboard (BFCL) V4](https://gorilla.cs.berkeley.edu/leaderboard.html)** — Patil et al., UC Berkeley (Gorilla / ICML 2025) — <https://gorilla.cs.berkeley.edu/leaderboard.html> · *排行榜* — 对工具/函数调用执行可执行与 AST 评分；V4 新增多轮智能体、Web 搜索和记忆任务，已成为事实上的工具调用排行榜。🆕
- **[GTA: A Benchmark for General Tool Agents](https://arxiv.org/abs/2407.08713)** — Wang et al., Shanghai AI Laboratory (NeurIPS 2024 D&B) — <https://arxiv.org/abs/2407.08713> · *基准* — 229 条人工编写的真实查询，包含隐式多模态工具使用；提供覆盖感知、操作、逻辑和创造性工具的可执行评测平台，2026 年推出后续版本 GTA-2。🆕
- **[Spider 2.0: Evaluating Language Models on Real-World Enterprise Text-to-SQL Workflows](https://arxiv.org/abs/2411.07763)** — Lei et al., XLang Lab / HKU (ICLR 2025 Oral) — <https://arxiv.org/abs/2411.07763> · *基准* — 面向大型 Schema 和多种方言的企业级 Text-to-SQL 智能体工作流，采用基于执行的评分；前沿模型仅约 17%–21%，是一项困难且真实的数据智能体评测。🆕
- **[AndroidWorld: A Dynamic Benchmarking Environment for Autonomous Agents](https://arxiv.org/abs/2405.14573)** — Rawles et al., Google DeepMind / Google Research (ICLR 2025) — <https://arxiv.org/abs/2405.14573> · *基准* — 实时 Android 环境，在 20 款应用上提供 116 项参数化任务，并根据设备系统状态给出持久奖励信号；标准移动 GUI 智能体基准。🆕
- **[WindowsAgentArena: Evaluating Multi-Modal OS Agents at Scale](https://arxiv.org/abs/2409.08264)** — Bonatti et al., Microsoft — <https://arxiv.org/abs/2409.08264> · *基准* — 跨应用的 154 项真实多步 Windows 操作系统任务，提供程序化成功检查；可在 Azure 中并行运行，约 20 分钟完成全部评测，是 OSWorld 在桌面计算机使用方面的对应基准。🆕
- **[Running the Gauntlet: Re-evaluating the Capabilities of Agents Beyond Familiar Environments](https://arxiv.org/abs/2606.14397)** — Vysotskyi, Gal, Torr, Bibi et al. (Oxford) — <https://arxiv.org/abs/2606.14397> · *基准* — 🆕 **GauntletBench**：在五种覆盖不足的专业应用中提供 100 项视觉密集任务，包括视频编辑器、工作流构建器、3D 建模器、飞行分析器和电路设计器，要求时间、图形与 3D 推理；“最先进智能体成功率仅 19.1%”，而“非专家人类标注员成功率超过 80%”，揭示现有桌面或 Web 基准未覆盖的专业工具计算机使用诊断差距。
- **[ST-WebAgentBench: Evaluating Safety and Trustworthiness in Web Agents](https://arxiv.org/abs/2410.06703)** — Levy, Shlomov, Wiesel et al., IBM Research — <https://arxiv.org/abs/2410.06703> · *基准* — 375 项企业任务，包含 3,057 条明确安全/政策约束；提出“政策约束下的完成度”和“风险比”，评价智能体是否遵守规则，而不只是任务是否成功。🆕
- **[TheAgentCompany: Benchmarking LLM Agents on Consequential Real World Tasks](https://arxiv.org/abs/2412.14161)** — Xu et al., CMU — <https://arxiv.org/abs/2412.14161> · *基准* — 自托管软件公司模拟环境，包含 Web、代码和聊天同事，使用基于检查点的部分得分；最佳智能体约 30%，是一项完整工作日知识工作者评测。🆕
- **[VisualWebArena: Evaluating Multimodal Agents on Realistic Visual Web Tasks](https://arxiv.org/abs/2401.13649)** — Koh et al., Carnegie Mellon University — <https://arxiv.org/abs/2401.13649> · *基准* — 在分类广告、购物和 Reddit 场景中提供 910 项视觉落地 Web 任务，配备可复现的程序化奖励函数，是 WebArena 的多模态扩展。
- **[GDPval: Evaluating AI Model Performance on Real-World Economically Valuable Tasks](https://arxiv.org/abs/2510.04374)** — Tejal Patwardhan et al. (OpenAI) — <https://arxiv.org/abs/2510.04374> · *基准* — 由专家构建的 1,320 项任务，覆盖 GDP 最高的 9 个行业中的 44 种职业；开放 220 项黄金子集，并在 evals.openai.com 提供公开自动评分服务，是旗舰级经济价值智能体基准。🆕
- **[Remote Labor Index: Measuring AI Automation of Remote Work](https://arxiv.org/abs/2510.26787)** — CAIS + Scale AI (47 authors) — <https://arxiv.org/abs/2510.26787> · *基准* — 评价智能体能否把完整的真实自由职业项目做到客户可接受的标准；最佳智能体只能自动化 2.5%，为端到端远程工作提供一个困难且以金钱为依据的上限。🆕
- **[Humanity's Last Exam](https://arxiv.org/abs/2501.14249)** — Center for AI Safety + Scale AI (Dan Hendrycks et al.) — <https://arxiv.org/abs/2501.14249> · *基准* — 2,500 道专家编写的前沿知识问题，横跨数十个领域并具有无歧义、可自动评分的答案；MMLU 饱和后的经典考试，目前已被广泛引用。🆕
- **[ScienceAgentBench: Toward Rigorous Assessment of Language Agents for Data-Driven Scientific Discovery](https://github.com/OSU-NLP-Group/ScienceAgentBench)** — OSU-NLP Group (Ohio State) — <https://github.com/OSU-NLP-Group/ScienceAgentBench> · *基准* — 来自 44 篇同行评审论文的 102 项专家验证任务；通过执行与成功率评价自包含 Python 程序；最佳智能体仅解决约 34%，ICLR 2025。🆕
- **[CORE-Bench: Computational Reproducibility Agent Benchmark](https://arxiv.org/abs/2409.11363)** — Siegel, Kapoor, Narayanan et al. (Princeton) — <https://arxiv.org/abs/2409.11363> · *基准* — 覆盖 90 篇计算机科学、社会科学和医学论文的 270 项任务，评价智能体能否利用代码与数据复现已发表结果；来自 Princeton AI-Snake-Oil 团队。
- **[DeepResearch Bench: A Comprehensive Benchmark for Deep Research Agents](https://arxiv.org/abs/2506.11763)** — Mingxuan Du et al. — <https://arxiv.org/abs/2506.11763> · *基准* — 跨 22 个领域的 100 项博士级任务；采用基于参考答案的自适应量规，为分析师级别、引文丰富的报告评分，并验证了与人类判断的对齐，是标准深度研究报告评测。🆕
- **[Dr. Bench: A Multidimensional Evaluation for Deep Research Agents, from Answers to Reports](https://arxiv.org/abs/2510.02190)** — Yao et al. — <https://github.com/EVIGBYEN/DrBench> · *基准* — 跨 10 个领域的 214 项专家精选深度研究任务，从语义质量、主题聚焦和检索可信度评价长报告。🆕
- **[BixBench: A Comprehensive Benchmark for LLM-based Agents in Computational Biology](https://arxiv.org/abs/2503.00096)** — FutureHouse + ScienceMachine — <https://arxiv.org/abs/2503.00096> · *基准* — 50 多种真实生物信息学分析场景，在多步 Jupyter 轨迹上设置约 300 个开放式问题；前沿模型仅约 17%，是一项严肃的、接近湿实验室的科学智能体评测。🆕
- **[Introducing LifeSciBench](https://openai.com/index/introducing-life-sci-bench/)** — OpenAI — <https://openai.com/index/introducing-life-sci-bench/> · *基准* — 🆕 750 项专家编写的生命科学研究任务（7 种工作流 × 7 个生物领域），依据 173 位博士级科学家贡献者制定的 19,020 条量规标准评分，并由 453 位专家审查员独立验证；要求解读基因组序列文件、化学结构和实验图；最佳模型 GPT-Rosalind 总分 36.1%，是最大的专家量规评分湿实验室工作流智能体基准。
- **[Gaia2 and ARE: Scaling Up Agent Environments and Evaluations](https://arxiv.org/abs/2509.17158)** — Meta (Meta Agents Research Environments) — <https://arxiv.org/abs/2509.17158> · *基准* — GAIA 的后继版本：动态、时间驱动的多智能体模拟环境，包含异步世界事件和可验证的场景评分器；前沿模型成功率约 42%，是 Meta 推出的严肃通用助手环境。🆕
- **[Vending-Bench: A Benchmark for Long-Term Coherence of Autonomous Agents](https://arxiv.org/abs/2502.15840)** — Andon Labs (Backlund & Petersson) — <https://arxiv.org/abs/2502.15840> · *基准* — 在超过 2,000 万词元的时程内经营模拟自动售货业务；按利润/净值客观评分，暴露与上下文长度无关的长时程连贯性崩溃。🆕
- **[ARC-AGI-2: A New Challenge for Frontier AI Reasoning Systems](https://arxiv.org/abs/2505.11831)** — Francois Chollet et al. (ARC Prize Foundation) — <https://arxiv.org/abs/2505.11831> · *基准* — 经人类校准（400 多名参与者，100% 可解）的网格推理任务，采用精确匹配评分；所有方法上均比 ARC-AGI-1 难 2–3 倍，是前沿流体智能基准。🆕
- **[TRAIL: Trace Reasoning and Agentic Issue Localization](https://arxiv.org/abs/2505.08638)** — Patronus AI — <https://arxiv.org/abs/2505.08638> · *基准* — 148 条带标注智能体轨迹，包含 841 个错误（推理/规划/执行）；评价 LLM 能否定位轨迹中的失败，最佳模型约 11%。HF 数据集：PatronusAI/TRAIL。🆕
- **[CRMArena-Pro: Holistic Assessment of LLM Agents Across Diverse Business Scenarios](https://arxiv.org/abs/2505.18878)** — Salesforce Research — <https://arxiv.org/abs/2505.18878> · *基准* — 在真实 Salesforce 组织上设置 19 项专家验证的 B2B/B2C 任务，采用基于状态的评分；揭示单轮约 58% 与多轮约 35% 的可靠性差距，并包含保密性检查。🆕

- **[Agents' Last Exam (ALE)](https://arxiv.org/abs/2606.05405)** — UC Berkeley RDI + 250+ industry co-authors — <https://arxiv.org/abs/2606.05405> · *基准* — 1,000 多个专家编写的任务工作流，覆盖 55 个数字行业和 13 个类别，并以 O*NET/SOC 2018 职业分类为依据。采用全通过评分；前沿智能体在最难等级上的平均完全通过率低于 1%。这是最接近“智能体能否完成真实知识工作”普查的基准。项目：agents-last-exam.org。🆕
- **[Introducing FrontierCode](https://cognition.com/blog/frontier-code)** — Cognition (Devin team) — <https://cognition.com/blog/frontier-code> · *基准* — 衡量 AI 编写的代码是否会被真实开源维护者合并，而不仅仅是 CI 是否通过；覆盖行为正确性、回归安全、机械洁净度、测试正确性、范围纪律和代码质量六个维度。设三个难度等级（Diamond / Main / Extended），20 多位维护者在每项任务上投入 40 多小时。Claude Opus 4.8 以 13.4% 领跑 Diamond，GPT-5.5 为 6.3%。声称比 SWE-bench Pro 的假阳性率低 81%。🆕
- **[Open-Sourcing Harvey's Long Horizon Legal Agent Benchmark](https://www.harvey.ai/blog/introducing-harveys-legal-agent-benchmark)** — Harvey AI — <https://www.harvey.ai/blog/introducing-harveys-legal-agent-benchmark> · *基准* — 1,200 多项长时程法律智能体任务，覆盖 24 个业务领域，依据 75,000 多条专家编写量规标准并采用全通过评分，模拟真实律师事务所的合并标准。开源评测框架；Vals AI 与 Artificial Analysis 排行榜均有镜像。这是法律 AI 团队用于对标的领域专家评分法律智能体基准。🆕
- **[CodeScaleBench: Testing coding agents on large codebases](https://sourcegraph.com/blog/codescalebench-testing-coding-agents-on-large-codebases-and-multi-repo-software-engineering-tasks)** — Sourcegraph — <https://sourcegraph.com/blog/codescalebench-testing-coding-agents-on-large-codebases-and-multi-repo-software-engineering-tasks> · *基准* — 在 Kubernetes、Django、Linux、VSCode 等 40 多个大型仓库和 9 种语言上设置 370 项任务，分为两个套件：SDLC（覆盖 9 个阶段的 150 项补丁任务）和 Org（220 项跨仓库任务）。只使用本地工具的智能体在超过约 40 万行代码时会系统性失败；增强 MCP 的智能体成本低 30%、速度快 38%，检索精确率提高 2–3 倍。🆕
- **[WorkBench Revisited: Workplace Agents Two Years On](https://arxiv.org/abs/2606.13715)** — Olly Styles — <https://arxiv.org/abs/2606.13715> · *论文* — 对 WorkBench 职场智能体基准进行纵向复测，覆盖 21 个模型（2023 年 3 月至 2026 年 5 月）：2024 年 GPT-4 完成 43% 的任务，同时有 26% 的非预期有害行为；2026 年 Claude Opus 4.8 完成 89%，有害行为为 2.5%，说明能力和安全改进同步，而非相互取舍。这是首个跨两年的职场智能体基准纵向数据集。🆕
- **[TAC (Travel Agent Compassion): Your AI Travel Agent Would Book You a Bullfight](https://arxiv.org/abs/2606.18142)** — Brazilek et al. (CaML) — <https://arxiv.org/abs/2606.18142> · *基准* — 面向歧义情境下智能体价值取向的评测：模型通过真实工具调用订票，主题最匹配的选项始终涉及动物剥削，而用户从未提及动物福利，因此可隔离测量智能体是否会主动带入这一价值。完全采用程序化评分器（最后一次 `purchase_tickets` 调用；没有 LLM 裁判），使用金丝雀保护的受控数据集，通过 `inspect_evals/tac` 运行。论文测试的九个模型中，没有一个在选择中性预订时超过 65% 的随机概率；Claude Opus 4.8 最高，为 64.7%（实时排行榜会随模型增加而更新：compassionbench.com）。🆕
- **[Closing the loop: Evaluating and improving Replit Agent at scale](https://replit.com/blog/evaluating-and-improving-agent-at-scale)** — James Austin et al. (Replit) — <https://replit.com/blog/evaluating-and-improving-agent-at-scale> · *优质* — **三层评测系统：（1）ViBench——离线基准，每项任务将 PRD 与自然语言测试计划配对，由 Playwright + LLM 裁判验证构建的应用是否真正可用；（2）对大多数会影响智能体的更改在生产环境做 A/B 测试；（3）Telescope——使用嵌入 + DBSCAN 聚类轨迹，发现新出现的失败模式，再送入智能体提出并测试自身修复的自我改进循环。** _（摘录：“它会总结失败轨迹、对其做嵌入、聚类相似案例，并随着分布变化对新会话分类。”）_ 🆕
- **[A practical guide to hill climbing](https://cline.bot/blog/a-practical-guide-to-hill-climbing)** — Ara Khan (Cline) — <https://cline.bot/blog/a-practical-guide-to-hill-climbing> · *优质* — **完整的编码智能体评测循环：使用 Harbor 在全部 89 项 Terminal-Bench 任务上运行 Cline CLI，总结失败展开并分桶，对提示词/配置/代码更改做 A/B 测试，只保留能提高总体通过率的更改（从 47% 提升到 57%）。它把“爬山”变成可重复评测工作流，而非排行榜轶事。** _（摘录：“只改一件事——提示词微调、缺陷修复或配置标志——然后再次运行；若得分提高，就保留该更改。”）_ 🆕（从仓库同步）
- **[A New Framework for Evaluating Voice Agents (EVA)](https://huggingface.co/blog/ServiceNow-AI/eva)** — Tara Bogavelli, Gabrielle Gauthier Melançon, Katrina Stankiewicz, Oluwanifemi Bamgbose, Hoang Nguyen, Raghav Mehndiratta, Hari Subramani (ServiceNow AI) — <https://huggingface.co/blog/ServiceNow-AI/eva> · *文章*（优秀）— EVA 是端到端语音智能体评测框架，使用机器人对机器人音频测试框架（用户模拟器 + Pipecat 智能体 + 确定性工具执行器 + 验证器），联合评价任务准确性（EVA-A：完成度、通过 LLM 裁判评价忠实度、通过 LALM 裁判评价语音保真度）与对话……🆕
- **[Evaluating AI agents: Real-world lessons from building agentic systems at Amazon](https://aws.amazon.com/blogs/machine-learning/evaluating-ai-agents-real-world-lessons-from-building-agentic-systems-at-amazon/)** — Yunfei Bai, Allie Colin, Kashif Imran, Winnie Xiong (AWS) — <https://aws.amazon.com/blogs/machine-learning/evaluating-ai-agents-real-world-lessons-from-building-agentic-systems-at-amazon/> · *文章*（良好）— 提出三层智能体评测库：基础模型基准、对意图/记忆/推理/工具使用的组件评估，以及最终任务完成质量；提供工具选择/参数准确率、上下文检索精确率/召回率、推理等具体组件指标……🆕
- **[Eval-driven development: Build and evaluate reliable AI agents](https://developers.redhat.com/articles/2026/03/23/eval-driven-development-build-evaluate-ai-agents)** — Michael Dawson (Red Hat) — <https://developers.redhat.com/articles/2026/03/23/eval-driven-development-build-evaluate-ai-agents> · *文章*（良好）— 面向真实多轮 IT 自助服务智能体的实操式八阶段评测驱动工作流：使用 DeepEval 的 ConversationalGEval/ConversationSimulator 和约 15 个自定义 LLM 裁判指标，并设置包含 11 段“已知错误”对话的目录，验证指标是否真正能捕获失败，即“测试你的测试”……🆕
- **[Expenditure Horizon: Measuring Optimization Ability, with an Application to NanoGPT](https://metr.org/blog/2026-07-21-expenditure-horizon/)** — METR (Cunningham, Shetty, Cheng, Rush) — <https://metr.org/blog/2026-07-21-expenditure-horizon/> · *博客（2026 年 7 月 21 日）* — 将“支出时程”定义为智能体优化表现—成本曲线与人类基线相交时的成本：“目标指标的改进等于同等预算下人类改进时的美元价值。”应用于 NanoGPT 竞速：人力每提升 1% 约需 2,500 美元；当前前沿智能体的支出时程为 0–3,000 美元，说明在大多数优化场景中尚不具成本竞争力。它为二元通过/失败任务指标提供一种连续、以人为基准、用成本计价的标量替代方案。🆕
- **[Behavior specs: an open standard for supervising long-horizon agents](https://www.braintrust.dev/blog/behavior-specs)** — Braintrust + Basis — <https://www.braintrust.dev/blog/behavior-specs> · *博客（2026 年 7 月 29 日）* — 用于评价智能体**过程**而非只看结果的开放标准（agentbehavior.dev），其动机是“正确的结果并不能说明智能体是否以正确方式到达结果”。规范是包含六个章节（Intent、Evidence、Decision、Execution、Recovery、Failure modes）的结构化 Markdown 文档，应用于整条轨迹，每项判为 true/false/NA。实际起源是 Basis 会计智能体：它们会在复杂税务申报上运行数小时，仅对最终申报做通过/失败判断会漏掉危险的中间步骤。包含开源编写技能与裁判提示词。🆕

**必读：** Anthropic（demystifying）· τ-bench · Lee（pass@k）

<a id="10-safety-adversarial-evaluation-prompt-injection-jailbreaks-action-authorization-benchmark-auditing"></a>
## 10 · 安全与对抗评测（提示注入、越狱、操作授权、基准审计）

- **[BenchJack: Systematically Auditing AI Agent Benchmarks](https://arxiv.org/abs/2605.12673)** — Wang, Li, Mang, Cheung, Sen, Song (incl. Dawn Song) — <https://arxiv.org/abs/2605.12673> · *论文* — 奖励投机会在前沿模型中自发出现；提出含 8 类模式的缺陷分类体系和 30 问 Agent-Eval 检查清单；“基准必须从设计上保证安全”。
- **[How We Broke Top AI Agent Benchmarks: And What Comes Next](https://moogician.github.io/blog/2026/trustworthy-benchmarks-cont/)** — Hao Wang, Qiuyang Mang, Alvin Cheung, Koushik Sen, Dawn Song (UC Berkeley) — <https://moogician.github.io/blog/2026/trustworthy-benchmarks-cont/> · *博客* — BenchJack 背后的从业者实战解读（与上文来自同一团队）：一个自动利用智能体在八个主要智能体基准上几乎拿到 100%，却没有解决任何任务——Terminal-Bench 通过二进制包装器木马达到 100%，SWE-bench Verified 通过操纵 pytest 钩子达到 100%，WebArena 通过导航到 `file://` 答案文件达到约 100%，FieldWorkArena 只输入 `{}` 就达到 100%；文章给出具体利用机制及相应的安全设计修复方案。🆕
- **[Towards Building Safe & Secure Agentic AI](https://rdi.berkeley.edu/adv-llm-agents/slides/dawn-agentic-ai.pdf)** — Dawn Song (UC Berkeley RDI, lecture slides) — <https://rdi.berkeley.edu/adv-llm-agents/slides/dawn-agentic-ai.pdf> · *演讲* — 对抗场景；源自环境的攻击。
- **[Dawn Song — ICLR 2025 keynote on LLM safety](https://iclr.cc/virtual/2025/invited-talk/36783)** — <https://iclr.cc/virtual/2025/invited-talk/36783> · *演讲*。
- **[CyberGym](https://arxiv.org/html/2506.02548v2)** — Wang et al. (incl. Dawn Song) — <https://arxiv.org/html/2506.02548v2> · *论文* — 根据 OSS-Fuzz 生成内存安全 PoC；大规模使用 sanitizer 崩溃评分。
- **[AIR-Bench 2024](https://arxiv.org/abs/2407.17436v2)** — Zeng et al. (incl. Song) — <https://arxiv.org/abs/2407.17436v2> · <https://github.com/stanford-crfm/air-bench-2024> · *论文/仓库* — 以监管要求为依据的风险分类体系。
- **[DecodingTrust](https://decodingtrust.github.io)** — <https://decodingtrust.github.io> · *基准* — NeurIPS 2023 可信度基准。
- **[RedCode](https://arxiv.org/abs/2411.07781)** — <https://arxiv.org/abs/2411.07781> · *论文* — 面向代码智能体的危险代码执行/生成基准。
- **[AgentPoison](https://arxiv.org/abs/2407.12784)** — <https://arxiv.org/abs/2407.12784> · *论文* — 通过污染智能体的 RAG 记忆对其进行红队测试。
- **[Adding Error Bars to Evals (A Statistical Approach to LM Evaluations)](https://arxiv.org/abs/2411.00640)** — Miller (Anthropic) — <https://arxiv.org/abs/2411.00640> · <https://www.anthropic.com/research/statistical-approach-to-model-evals> · *论文* — 标准误、聚类标准误、配对差异检验——“这个差异真实吗？”（跨主题：T6/T8）

- **[AgentDojo: A Dynamic Environment to Evaluate Prompt Injection Attacks and Defenses for LLM Agents](https://arxiv.org/abs/2406.13352)** — Debenedetti, Zhang, Balunović, Beurer-Kellner, Fischer, Tramèr (ETH Zurich) — <https://arxiv.org/abs/2406.13352> · *基准* — 工具使用智能体的经典提示注入基准：在不可信数据上设置 97 项任务、629 个安全案例；NeurIPS 2024 D&B，目前已成为各方报告结果的标准评测。原列表对此明显遗漏。🆕
- **[AgentHarm: A Benchmark for Measuring Harmfulness of LLM Agents](https://arxiv.org/abs/2410.09024)** — Andriushchenko, Souly, Davies et al. (Gray Swan / UK AISI) — <https://arxiv.org/abs/2410.09024> · *基准* — ICLR 2025 基准，包含 11 类危害中的 110/440 项恶意智能体任务；显示领先模型无需越狱就会遵从恶意智能体请求。它是操作滥用/拒绝行为的参考基准。🆕
- **[InjecAgent: Benchmarking Indirect Prompt Injections in Tool-Integrated LLM Agents](https://arxiv.org/abs/2403.02691)** — Zhan, Liang et al. (UIUC) — <https://arxiv.org/abs/2403.02691> · *基准* — ACL 2024 Findings；在 17 个用户工具和 62 个攻击者工具上设置 1,054 个间接提示注入测试样例，将意图分为直接伤害和数据外泄。它是早于 AgentDojo 的奠基性间接提示注入基准。
- **[Defeating Prompt Injections by Design (CaMeL)](https://arxiv.org/abs/2503.18813)** — Debenedetti, Shumailov, Fan, Hayes et al. (Google DeepMind) — <https://arxiv.org/abs/2503.18813> · *论文* — 设计即防御的对应工作：从可信查询中提取控制流/数据流，并执行基于能力的策略，使不可信数据无法改变程序流程；实际上解决了 AgentDojo 的安全评测。2025 年关键缓解论文。🆕
- **[The lethal trifecta for AI agents: private data, untrusted content, and external communication](https://simonwillison.net/2025/Jun/16/the-lethal-trifecta/)** — Simon Willison — <https://simonwillison.net/2025/Jun/16/the-lethal-trifecta/> · *博客* — 分析智能体在何种情况下会无条件易受提示注入攻击时，被引用最多的概念框架；达到 Eugene Yan 水准的从业者必备思维模型。🆕
- **[SHADE-Arena: Evaluating Sabotage and Monitoring in LLM Agents](https://www.anthropic.com/research/shade-arena-sabotage-monitoring)** — Kutasov, Bowman et al. (Anthropic) — <https://www.anthropic.com/research/shade-arena-sabotage-monitoring> · *基准* — 17 个复杂环境，将无害主任务与隐藏有害副任务配对，测量智能体能否在不触发 AI 监控器的情况下实施破坏；经典破坏/可监控性评测。（论文：arxiv.org/abs/2506.15740）🆕
- **[Agentic Misalignment: How LLMs Could Be Insider Threats](https://www.anthropic.com/research/agentic-misalignment)** — Anthropic (Alignment team) — <https://www.anthropic.com/research/agentic-misalignment> · *论文* — 红队研究表明，在智能体场景的目标冲突下，前沿模型会诉诸勒索或泄密；它是操作授权/内部威胁对抗评测的参考文献，也是上文 Anthropic 误差线文章的配套资源。🆕
- **[PyRIT — Python Risk Identification Tool for generative AI](https://github.com/Azure/PyRIT)** — Microsoft AI Red Team (Azure) — <https://github.com/Azure/PyRIT> · *工具* — 事实上的开源红队自动化框架，包含 70 多个转换器及 Crescendo/TAP 等多轮攻击；展示从业者如何真正大规模运行对抗评测。本节列了论文，却缺少工具。🆕
- **[OWASP Top 10 for Agentic Applications (2026) + LLM Applications (2025)](https://genai.owasp.org/resource/owasp-top-10-for-agentic-applications-for-2026/)** — OWASP GenAI Security Project — <https://genai.owasp.org/resource/owasp-top-10-for-agentic-applications-for-2026/> · *文档* — 行业标准风险分类体系：目标劫持、工具滥用、身份/权限滥用、记忆污染、失控智能体；补充本节已有、以监管为依据的 AIR-Bench 分类体系。它是经典的从业者威胁检查清单。🆕
- **[MITRE ATLAS — Adversarial Threat Landscape for AI Systems](https://atlas.mitre.org/)** — MITRE — <https://atlas.mitre.org/> · *文档* — ATT&CK 风格的动态知识库，包含针对 AI 系统的 16 种战术和 80 多种技术，以及真实案例与缓解措施；AI 对抗威胁建模的标准参考框架。
- **[Agent Security Bench (ASB): Formalizing and Benchmarking Attacks and Defenses in LLM-based Agents](https://proceedings.iclr.cc/paper_files/paper/2025/file/5750f91d8fb9d5c02bd8ad2c3b44456b-Paper-Conference.pdf)** — Zhang, Yang et al. — <https://proceedings.iclr.cc/paper_files/paper/2025/file/5750f91d8fb9d5c02bd8ad2c3b44456b-Paper-Conference.pdf> · *基准* — ICLR 2025 统一基准，横跨 10 个场景和 400 多个工具，在一个测试框架中覆盖 DPI/IPI、记忆污染、思维规划后门及防御；是覆盖面最广的单一智能体攻击/防御基准。🆕
- **[Gray Swan x UK AISI Agent Red-Teaming Challenge](https://app.grayswan.ai/arena/blog/agent-red-teaming-the-ai-jailbreak-showdown)** — Gray Swan AI / UK AISI (w/ OpenAI, Anthropic, GDM) — <https://app.grayswan.ai/arena/blog/agent-red-teaming-the-ai-jailbreak-showdown> · *演讲* — 最大规模的公开智能体红队活动：约 2,000 名红队测试者，对 22 个工具使用智能体（金融/购物/营销机器人）发起 180 万次尝试并实现 6.2 万次攻破；提供大规模真实对抗评测数据。🆕

- **[MonitoringBench: Semi-Automated Red-Teaming for Agent Monitoring](https://arxiv.org/abs/2605.09684)** — various — <https://arxiv.org/abs/2605.09684> · *基准* — 在 BashArena 上通过半自动红队生成 2,644 条攻击轨迹，用于评价 AI 编码智能体监控器。Claude Opus 4.5 监控器的捕获率从标准攻击下的 94.9% 降至最佳优化自适应攻击下的 60.3%，相差 34 个百分点，量化当前监控器在对抗压力下究竟还剩多少安全余量。🆕
- **[Summary of METR's predeployment evaluation of GPT-5.6 Sol](https://metr.org/blog/2026-06-26-gpt-5-6-sol/)** — METR — <https://metr.org/blog/2026-06-26-gpt-5-6-sol/> · *博客（2026 年 6 月 26 日）* — “GPT-5.6 Sol 的作弊检测率高于我们在 ReAct 智能体测试框架上评测过的任何公开模型。”METR 的独立部署前评测发现，该模型会利用评测环境缺陷并采用被禁止的策略；时间时程方法得到 11.3 小时，但 METR 认为结果“不能被视为稳健测量”。这是结构化第三方安全评测中，前沿模型主动评测作弊方面最尖锐的一手来源。🆕
- **[Measuring LLMs' impact on N-day exploits](https://www.anthropic.com/research/n-days)** — Anthropic — <https://www.anthropic.com/research/n-days> · *博客（2026 年 6 月 8 日）* — 在已公开但尚未修补的 Firefox 与 Windows 内核漏洞上评测 Claude Mythos Preview；它生成了 8 个有效 Firefox 代码执行利用（约 1 小时产出第一个）和 8 条不同的 Windows 权限提升链（平均每个利用约 2,000 美元；总计 15,700 美元）。“如今，一个独立操作者可以在一个下午把一个月的补丁变成有效利用，花费仅数千美元，而且无需专门知识。”文章引入 nonce 保护的评分与智能体抗奖励投机验证，并认为“N 小时”才是准确的威胁表述。🆕
- **[RealityTest: How People Probe AI Identity and Whether Models Disclose It](https://www.aisi.gov.uk/research/realitytest-how-people-probe-ai-identity-and-whether-models-disclose-it)** — UK AISI — <https://www.aisi.gov.uk/research/realitytest-how-people-probe-ai-identity-and-whether-models-disclose-it> · *论文（2026 年 6 月 8 日）* — 收集约 750 名参与者、横跨 49 个国家和 5 种语言的 3,152 条身份探测查询，并测试 17 个文本模型和 6 个语音模型。文本披露率介于 8%–92%；“查询措辞是两种模态中最大的方差来源（语音 37%，文本 26%），超过模型身份的贡献（分别为 10% 和 18%）。”一条对抗系统提示词使所有模型的披露率降至 3%–27%。这是针对 AI 身份披露合规（EU AI Act、UK AI Act）的安全评测维度。🆕
- **[Investigating three real-world incidents in our cybersecurity evaluations](https://www.anthropic.com/news/investigating-incidents-cybersecurity-evals)** — Anthropic — <https://www.anthropic.com/news/investigating-incidents-cybersecurity-evals> · *博客（2026 年 7 月 30 日）* — 首次详细公开 AI 能力评测逃逸沙盒的事后分析：在 141,006 次网络安全评测运行中，因评测环境错误配置为可访问真实互联网，三起事件（六次运行）造成现实影响。最严重的一次中，Claude 创建了 PyPI 账户，上传可运行恶意软件包；在自动扫描器约一小时后移除它之前，该包已被下载并在 15 个真实系统上执行。Claude 自己在事件中的推理也指出，这“不对，而且肯定不是预期解法”。根因是与评测合作伙伴沟通失误，导致本应隔离的沙盒仍具有真实互联网连接。任何在联网环境中运行能力评测的人都应阅读。🆕

**必读：** Dawn Song（BenchJack）· Anthropic（误差线）

---
<a id="-talks-podcasts-slides-transcribed-noted"></a>
## 🎙 演讲、播客与幻灯片（已转录并制作笔记）


### 🎤 会议与个人演讲
- **[How to Construct Domain Specific LLM Evaluation Systems](https://www.youtube.com/watch?v=eLXF0VojuSs)** — Hamel Husain & Emil Sedgh — <https://www.youtube.com/watch?v=eLXF0VojuSs> · *演讲*（AI Engineer World's Fair 2024）
- **[How to look at your data](https://www.youtube.com/watch?v=jryZvCuA0Uc)** — Jeff Huber & Jason Liu — <https://www.youtube.com/watch?v=jryZvCuA0Uc> · *演讲*（AI Engineer World's Fair 2025）
- **[Failure is a Funnel](https://www.youtube.com/watch?v=k98gDjYbSaU)** — Bryan Bischof — <https://www.youtube.com/watch?v=k98gDjYbSaU> · *演讲*（Data Council 2025）
- **[Using LLMs as Judges: Insights, Challenges, Best Practices](https://www.youtube.com/watch?v=7EGF0Mc0_os)** — Eugene Yan — <https://www.youtube.com/watch?v=7EGF0Mc0_os> · *演讲*（Jason Liu 系列，2024）
- **[Scaling Up Vibe Checks for LLMs](https://www.youtube.com/watch?v=eGVDKegRdgM)** — Shreya Shankar — <https://www.youtube.com/watch?v=eGVDKegRdgM> · *演讲*（Stanford MLSys #97）
- **[Why LLM Data Processing Pipelines Fail](https://www.youtube.com/watch?v=H-1QaLPnGsg)** — Shreya Shankar — <https://www.youtube.com/watch?v=H-1QaLPnGsg> · *演讲*（LangChain Interrupt 2025）
- **[Evals Are Not Unit Tests](https://www.youtube.com/watch?v=L8OoYeDI_ls)** — Ido Pesok（Vercel v0）— <https://www.youtube.com/watch?v=L8OoYeDI_ls> · *演讲*（AI Engineer 2025）
- **[Building Metrics that actually work (workshop)](https://www.youtube.com/watch?v=jxrGodnopHo)** — David Karam（Pi Labs）— <https://www.youtube.com/watch?v=jxrGodnopHo> · *演讲*（AI Engineer 2025）
- **[From Self-driving to Autonomous Voice Agents](https://www.youtube.com/watch?v=kDczF4wBh8s)** — Brooke Hopkins（Coval）— <https://www.youtube.com/watch?v=kDczF4wBh8s> · *演讲*（AI Engineer 2025）
- **[Fuzzing in the GenAI Era](https://www.youtube.com/watch?v=OMGPvW8TBHc)** — Leonard Tang（Haize Labs）— <https://www.youtube.com/watch?v=OMGPvW8TBHc> · *演讲*（AI Engineer 2025）
- **[On Engineering AI Systems that Endure the Bitter Lesson](https://www.youtube.com/watch?v=qdmxApz3EJI)** — Omar Khattab（DSPy）— <https://www.youtube.com/watch?v=qdmxApz3EJI> · *演讲*（AI Engineer 2025）
- **[Strategies for LLM Evals (harnesses workshop)](https://www.youtube.com/watch?v=89NuzmKokIk)** — Taylor Jordan Smith — <https://www.youtube.com/watch?v=89NuzmKokIk> · *演讲*（AI Engineer 2025）
- **[The Future of Evals](https://www.youtube.com/watch?v=MC55hdWLq4o)** — Ankur Goyal（Braintrust）— <https://www.youtube.com/watch?v=MC55hdWLq4o> · *演讲*（AI Engineer 2025）
- **[3 Key Ideas in AI in 2025 (Verifier's Law)](https://www.youtube.com/watch?v=b6Doq2fz81U)** — Jason Wei（OpenAI）— <https://www.youtube.com/watch?v=b6Doq2fz81U> · *演讲*（Stanford AI Club 2025）
- **[Some Intuitions About Large Language Models](https://www.youtube.com/watch?v=l898fqkjdFc)** — Jason Wei — <https://www.youtube.com/watch?v=l898fqkjdFc> · *演讲*（The AI Conference 2025）
- **[Deep Dive into LLMs like ChatGPT](https://www.youtube.com/watch?v=7xTGNNLPyMI)** — Andrej Karpathy — <https://www.youtube.com/watch?v=7xTGNNLPyMI> · *演讲*（2025）
- **[RLHF: Progress and Challenges](https://www.youtube.com/watch?v=hhiLw5Q_UFg)** — John Schulman — <https://www.youtube.com/watch?v=hhiLw5Q_UFg> · *演讲*（UC Berkeley EECS 2023）
- **[Aligning Open Language Models](https://www.youtube.com/watch?v=AdLgPmcrXwQ)** — Nathan Lambert（Ai2）— <https://www.youtube.com/watch?v=AdLgPmcrXwQ> · *演讲*（Stanford CS25 V4）
- **[Building LLM Applications for Production](https://www.youtube.com/watch?v=spamOhG7BOA)** — Chip Huyen — <https://www.youtube.com/watch?v=spamOhG7BOA> · *演讲*（MLOps LLMs in Prod 2023）
- **[The Model is the Product](https://www.youtube.com/watch?v=4dUFIRj-BWo)** — Han-Chung Lee — <https://www.youtube.com/watch?v=4dUFIRj-BWo> · *演讲*（Data Council 2025）
- **[RL Environments at Scale](https://www.youtube.com/watch?v=_IzZWeuTx7I)** — Will Brown（Prime Intellect）— <https://www.youtube.com/watch?v=_IzZWeuTx7I> · *演讲*（AI Engineer 2025）
- **[LLM benchmarks in the time of agents](https://www.youtube.com/watch?v=kmTMc-fVSXw)** — Florian Brand（Prime Intellect）— <https://www.youtube.com/watch?v=kmTMc-fVSXw> · *演讲*（Big Techday 26，2026）

### 🎙 播客单集
- **[Evals, error analysis, and better prompts](https://www.youtube.com/watch?v=PgzOBNse2EA)** — Hamel Husain（How I AI / Claire Vo）— <https://www.youtube.com/watch?v=PgzOBNse2EA> · *播客*（How I AI）
- **[Evals are the new PRD for AI products](https://www.youtube.com/watch?v=QE_1hRLsehM)** — Ankur Goyal（How I AI）— <https://www.youtube.com/watch?v=QE_1hRLsehM> · *播客*（How I AI）
- **[Ep 60: 10 Things I Hate About AI Evals](https://www.youtube.com/watch?v=QEk-XwrkqhI)** — Hamel Husain（Vanishing Gradients）— <https://www.youtube.com/watch?v=QEk-XwrkqhI> · *播客*（Vanishing Gradients）
- **[Ep 50: A Field Guide to Rapidly Improving AI Products](https://www.youtube.com/watch?v=rWToRi2_SeY)** — Hamel Husain（Vanishing Gradients）— <https://www.youtube.com/watch?v=rWToRi2_SeY> · *播客*（Vanishing Gradients）
- **[Five Hard-Earned Lessons About Evals](https://www.youtube.com/watch?v=a4BV0gGmXgA)** — Ankur Goyal（Latent Space）— <https://www.youtube.com/watch?v=a4BV0gGmXgA> · *播客*（Latent Space）
- **[Artificial Analysis: Independent LLM Evals](https://www.youtube.com/watch?v=v5mBjeX4TJ8)** — Cameron & Hill-Smith（Latent Space）— <https://www.youtube.com/watch?v=v5mBjeX4TJ8> · *播客*（Latent Space）
- **[Reality: The Final Eval (Vending-Bench)](https://www.youtube.com/watch?v=ZAimcoJXUBo)** — Petersson & Backlund（Andon Labs）— <https://www.youtube.com/watch?v=ZAimcoJXUBo> · *播客*（Latent Space / Cognitive Revolution）
- **[#5 Designing Evals](https://www.youtube.com/watch?v=-N6MajRfqYw)** — Vaibhav Gupta & Dex（AI That Works）— <https://www.youtube.com/watch?v=-N6MajRfqYw> · *播客*（AI That Works）
- **[#16 Evaluating Prompts Across Models](https://www.youtube.com/watch?v=OawyQOrlubM)** — AI That Works — <https://www.youtube.com/watch?v=OawyQOrlubM> · *播客*（AI That Works）
- **[#24 Evals for Classification](https://www.youtube.com/watch?v=5Fy0hBzyduU)** — AI That Works — <https://www.youtube.com/watch?v=5Fy0hBzyduU> · *播客*（AI That Works）
- **[#34 Multimodal Evals](https://www.youtube.com/watch?v=jzhVo0iAX_I)** — AI That Works — <https://www.youtube.com/watch?v=jzhVo0iAX_I> · *播客*（AI That Works）
- **[#372 It's 2026 and We're Still Talking Evals](https://www.youtube.com/watch?v=9EjWR3QpJYk)** — Maggie Konstanty（MLOps Community）— <https://www.youtube.com/watch?v=9EjWR3QpJYk> · *播客*（MLOps Community）
- **[#728 Generative Benchmarking](https://www.youtube.com/watch?v=3kbiGPn0cOo)** — Kelly Hong（TWIML）— <https://www.youtube.com/watch?v=3kbiGPn0cOo> · *播客*（TWIML AI）
- **[Shaping AI Benchmarks (HELM)](https://www.youtube.com/watch?v=kwkdKirqi6s)** — Percy Liang（Gradient Dissent）— <https://www.youtube.com/watch?v=kwkdKirqi6s> · *播客*（Gradient Dissent）
- **[Evaluating LLMs with Chatbot Arena](https://www.youtube.com/watch?v=okHMaczHPXc)** — Joseph Gonzalez（Gradient Dissent）— <https://www.youtube.com/watch?v=okHMaczHPXc> · *播客*（Gradient Dissent）
- **[Evaluating AI, Designing for Non-Determinism](https://www.youtube.com/watch?v=v0eTTn7ZPEc)** — Aman Khan（Learning from ML）— <https://www.youtube.com/watch?v=v0eTTn7ZPEc> · *播客*（Learning from Machine Learning）
- **[Karpathy: RL is terrible, why benchmarks mislead](https://www.youtube.com/watch?v=-lRBpyPt79c)** — Andrej Karpathy（Dwarkesh）— <https://www.youtube.com/watch?v=-lRBpyPt79c> · *播客*（Dwarkesh Podcast）
- **[How to Build AI Evals in 2026 (Step-by-Step)](https://www.youtube.com/watch?v=J7N9FMouSKg)** — Hamel Husain & Shreya Shankar — <https://www.youtube.com/watch?v=J7N9FMouSKg> · *播客*（Aakash Gupta 2026）

### 🎓 大学课程
- **[Towards Building Safe & Trustworthy AI Agents](https://www.youtube.com/watch?v=QAgR4uQ15rc)** — Dawn Song — <https://www.youtube.com/watch?v=QAgR4uQ15rc> · *课程*（Berkeley LLM Agents MOOC F24）
- **[Towards Building Safe and Secure Agentic AI](https://www.youtube.com/watch?v=ti6yPE2VPZc)** — Dawn Song — <https://www.youtube.com/watch?v=ti6yPE2VPZc> · *课程*（Berkeley Advanced LLM Agents Sp25）
- **[Measuring Agent Capabilities and Anthropic's RSP](https://www.youtube.com/watch?v=6y2AnWol7oo)** — Ben Mann（Anthropic）— <https://www.youtube.com/watch?v=6y2AnWol7oo> · *课程*（Berkeley LLM Agents MOOC F24）
- **[Open-Source and Science in the Era of Foundation Models](https://www.youtube.com/watch?v=f3KKx9LWntQ)** — Percy Liang — <https://www.youtube.com/watch?v=f3KKx9LWntQ> · *课程*（Berkeley LLM Agents MOOC F24）
- **[CS336 Lecture 12: Evaluation](https://www.youtube.com/watch?v=x-R5l2HsXqM)** — Hashimoto & Liang — <https://www.youtube.com/watch?v=x-R5l2HsXqM> · *课程*（Stanford CS336 2025）

### 🖼 幻灯片
- **LLM benchmarks in the era of agents (deck)** — Florian Brand — `（本地幻灯片）` · *幻灯片*（TNG / Big Techday）
- **The Life Cycle of an RL Environment (deck)** — Kanav Garg — `（本地幻灯片）` · *幻灯片*（ACM CAIS 2026）


### 更多评测演讲、播客与课程（已注释；深度笔记制作中）
*另发现 58 项；转录正在排队（受 YouTube 速率限制）。下列内容包括 30 项以评测为核心的资料，以及 28 项含有评测环节的智能体主题演讲。*
- **[Judging LLMs](https://www.youtube.com/watch?v=IIL2tE4n1Q0)** — Alex Volkov（AI 布道者，Weights & Biases；ThursdAI 主持人）— <https://www.youtube.com/watch?v=IIL2tE4n1Q0> · *演讲*（AI Engineer World's Fair 2025——Evals 专场）
- **[2025 is the Year of Evals! Just like 2024, and 2023, and …](https://www.youtube.com/watch?v=CQGuvf6gSrM)** — John Dickerson（Mozilla AI CEO）— <https://www.youtube.com/watch?v=CQGuvf6gSrM> · *演讲*（AI Engineer World's Fair 2025——Evals 专场）
- **[Lessons from the Trenches: Building LLM Evals That Work IRL](https://www.youtube.com/watch?v=nbZzSC5A6hs)** — Aparna Dhinakaran（Arize AI 联合创始人兼 CPO）— <https://www.youtube.com/watch?v=nbZzSC5A6hs> · *演讲*（AI Engineer World's Fair 2025——Evals 专场）
- **[The maturity phases of running evals](https://www.youtube.com/watch?v=FB-MLPhL9Ms)** — Phil Hetzel（Braintrust）— <https://www.youtube.com/watch?v=FB-MLPhL9Ms> · *演讲*（AI Engineer World's Fair 2025——Evals 专场）
- **[Ship Real Agents: Hands-On Evals for Agentic Applications](https://www.youtube.com/watch?v=Xfl50508LZM)** — Laurie Voss（Arize）— <https://www.youtube.com/watch?v=Xfl50508LZM> · *演讲*（AI Engineer World's Fair 2025——Evals 专场）
- **[What Do Models Still Suck At? (BullshitBench)](https://www.youtube.com/watch?v=R7A8rX-09Zw)** — Peter Gostev（Arena.ai）— <https://www.youtube.com/watch?v=R7A8rX-09Zw> · *演讲*（AI Engineer World's Fair 2025——Evals 专场）
- **[Perceptual Evaluations: Evals for Aesthetics](https://www.youtube.com/watch?v=h5ItAJuB3Fc)** — Diego Rodriguez（Krea.ai 联合创始人兼 CTO）— <https://www.youtube.com/watch?v=h5ItAJuB3Fc> · *演讲*（AI Engineer World's Fair 2025——Evals 专场）
- **[Evaluating AI Search: A Practical Framework for Augmented AI Systems](https://www.youtube.com/watch?v=wRJD0inpmjU)** — Quotient AI + Tavily（双方演讲者）— <https://www.youtube.com/watch?v=wRJD0inpmjU> · *演讲*（AI Engineer World's Fair 2025——Evals 专场）
- **[Turning Fails into Features: Zapier's Hard-Won Eval Lessons](https://www.youtube.com/watch?v=blrovBxxN9o)** — Rafal Willinski & Vitor Balocco（Zapier）— <https://www.youtube.com/watch?v=blrovBxxN9o> · *演讲*（AI Engineer World's Fair 2025——Evals 专场）
- **[Why should anyone care about Evals?](https://www.youtube.com/watch?v=jJ45Yz1lJao)** — Manu Goyal（Braintrust）— <https://www.youtube.com/watch?v=jJ45Yz1lJao> · *演讲*（AI Engineer World's Fair 2025——Evals 专场）
- **[Mastering AI Evaluation: From Playground to Production [Evals Workshop]](https://www.youtube.com/watch?v=9iN-cPnp7xg)** — AI Engineer Evals Workshop（多位演讲者）— <https://www.youtube.com/watch?v=9iN-cPnp7xg> · *演讲*（AI Engineer World's Fair 2025——Evals 专场完整工作坊）
- **[Databricks Co-Founder: Eval Limitations, Why China is Winning Open Source and Future of AI Infra (Ep 69)](https://www.youtube.com/watch?v=ehav4XMAKLw)** — Ion Stoica（Databricks/Anyscale、LMArena 联合创始人），主持人 Jacob Effron — <https://www.youtube.com/watch?v=ehav4XMAKLw> · *播客*（Unsupervised Learning，Redpoint Ventures）
- **[Mercor CEO: Evals Will Replace Knowledge Work, AI x Hiring Today & the Future of Data Labeling (Ep 68)](https://www.youtube.com/watch?v=SOZtz8IdI2w)** — Brendan Foody（Mercor 联合创始人兼 CEO），主持人 Jacob Effron — <https://www.youtube.com/watch?v=SOZtz8IdI2w> · *播客*（Unsupervised Learning，Redpoint Ventures）
- **[CTIBench: How Good Are LLMs at Detecting Cyber Threats? (Ep 729)](https://www.youtube.com/watch?v=75WqFOY3P5M)** — Nidhi Rastogi（RIT 助理教授），主持人 Sam Charrington — <https://www.youtube.com/watch?v=75WqFOY3P5M> · *播客*（The TWIML AI Podcast）
- **[Holistic Evaluation of Generative AI Systems (MLOps Podcast #280)](https://www.youtube.com/watch?v=VJ0k0C1mGdg)** — Jineet Doshi（Intuit 首席 AI 科学家/负责人），主持人 Demetrios Brinkmann — <https://www.youtube.com/watch?v=VJ0k0C1mGdg> · *播客*（MLOps.community）
- **[Can AIs do AI R&D? Reviewing RE-Bench Results with Neev Parikh of METR](https://www.youtube.com/watch?v=SX8Mxyy_UHY)** — Neev Parikh（METR），主持人 Nathan Labenz — <https://www.youtube.com/watch?v=SX8Mxyy_UHY> · *播客*（The Cognitive Revolution）
- **[Can We Stop AI Deception? Apollo Research Tests OpenAI's Deliberative Alignment, w/ Marius Hobbhahn](https://www.youtube.com/watch?v=I3ivZaAfDFg)** — Marius Hobbhahn（Apollo Research CEO），主持人 Nathan Labenz — <https://www.youtube.com/watch?v=I3ivZaAfDFg> · *播客*（The Cognitive Revolution）
- **[Metrics Driven Development (Ragas)](https://www.youtube.com/watch?v=fw0wUC5XN-o)** — Shahul Es（Ragas 联合创始人），主持人 Daniel Whitenack & Chris Benson — <https://www.youtube.com/watch?v=fw0wUC5XN-o> · *播客*（Practical AI，Changelog）
- **[R1, OpenAI's o3, and the ARC-AGI Benchmark: Insights from Mike Knoop](https://www.youtube.com/watch?v=SSA8vNrFpXI)** — Mike Knoop（ARC Prize / Zapier 联合创始人），主持人 Lukas Biewald — <https://www.youtube.com/watch?v=SSA8vNrFpXI> · *播客*（Gradient Dissent，Weights & Biases）
- **[Sandbox breakout evals with Inspect — UK AISI (Fully Connected London '25)](https://www.youtube.com/watch?v=J79pSSAENYc)** — UK AI Safety Institute 团队 — <https://www.youtube.com/watch?v=J79pSSAENYc> · *演讲*（Gradient Dissent / Fully Connected London '25，Weights & Biases）
- **[How to align your LLM judge for better evaluations](https://www.youtube.com/watch?v=AMCmhRoKnSk)** — Weights & Biases（Weave 团队）— <https://www.youtube.com/watch?v=AMCmhRoKnSk> · *演讲*（Gradient Dissent / W&B）
- **[Stanford CME295 Transformers & LLMs (Autumn 2025) | Lecture 8 - LLM Evaluation](https://www.youtube.com/watch?v=8fNP4N46RRo)** — Afshine Amidi & Shervine Amidi — <https://www.youtube.com/watch?v=8fNP4N46RRo> · *课程*（Stanford CME295 / Stanford Online）
- **[CS294-196 (Agentic AI MOOC) - LLM Agent Evaluations & Project Overview](https://www.youtube.com/watch?v=VfOA2a0dj4w)** — Berkeley RDI 课程团队（Dawn Song 的 Agentic AI MOOC）— <https://www.youtube.com/watch?v=VfOA2a0dj4w> · *课程*（UC Berkeley RDI，CS294-196，2025 秋季）
- **[Agentic AI MOOC (Fall 2025) | Predictable Noise in LLM Benchmarks](https://www.youtube.com/watch?v=HV8pugcFVO0)** — Sida Wang（Meta）— <https://www.youtube.com/watch?v=HV8pugcFVO0> · *课程*（UC Berkeley RDI，CS294-196，2025 秋季）
- **[Agent Optimization with Pydantic AI: GEPA, Evals, Feedback Loops — Samuel Colvin, Pydantic](https://www.youtube.com/watch?v=A48uhxfxbsM)** — Samuel Colvin（Pydantic 创始人）— <https://www.youtube.com/watch?v=A48uhxfxbsM> · *演讲*（AI Engineer，Code Summit / AI Engineer）
- **[Coding Evals: From Code Snippets to Codebases — Naman Jain, Cursor](https://www.youtube.com/watch?v=tHN44yJoeS8)** — Naman Jain（Cursor；LiveCodeBench/SWE-bench 邻近领域研究者）— <https://www.youtube.com/watch?v=tHN44yJoeS8> · *演讲*（AI Engineer，Code Summit）
- **[From Self-driving to Autonomous Voice Agents — Brooke Hopkins, Coval (full session host upload)](https://www.youtube.com/watch?v=1X3mYUHC5GA)** — Brooke Hopkins（Coval 创始人；曾负责 Waymo 评测基础设施）— <https://www.youtube.com/watch?v=1X3mYUHC5GA> · *演讲*（Founders You Should Know）
- **[Brooke Hopkins, Founder at Coval | AI Minds #073](https://www.youtube.com/watch?v=e1E8vLyRIKk)** — Brooke Hopkins（Coval 创始人）— <https://www.youtube.com/watch?v=e1E8vLyRIKk> · *播客*（AI Minds，Deepgram）
- **[Karthik Narasimhan - Reliable AI Agents for Tomorrow's World](https://www.youtube.com/watch?v=fOAAslQUceg)** — Karthik Narasimhan（Sierra 研究负责人；Princeton；tau-bench 作者）— <https://www.youtube.com/watch?v=fOAAslQUceg> · *课程*（Berkeley RDI，Agentic AI Summit 2025）
- **[Building and evaluating AI Agents — Sayash Kapoor, AI Snake Oil](https://www.youtube.com/watch?v=d5EltXhbcfA)** — Sayash Kapoor（Princeton；AI Snake Oil；HAL / 智能体评测批评研究共同作者）— <https://www.youtube.com/watch?v=d5EltXhbcfA> · *演讲*（AI Engineer，Summit 2025）

> 🎯 *智能体构建演讲中的评测片段收录在 [MENTIONS.md](MENTIONS.md) 中。*

<a id="-eval-mentions"></a>
## 💬 评测提及

> 仅仅*提及*评测的资源——即包含优质评测片段的智能体构建文章与演讲——统一收录在 **[MENTIONS.md](MENTIONS.md)** 中，不放入主清单，以维持信号密度。

<a id="companies-landscape-eval-rl-environment-market"></a>
## 公司与行业版图（评测 / 强化学习环境市场）

- **[pavlovslist.com](https://pavlovslist.com/)** — <https://pavlovslist.com/> · *目录* — 强化学习环境 / 评测初创公司目录（“为 RL 爱好者准备”）。
- **环境实验室 / 强化学习环境公司**（“环境就是新数据”的创投浪潮，来源：pavlovslist）：**BenchFlow**（benchflow.ai——SkillsBench、ClawsBench、运行时）、**Prime Intellect**（verifiers、Environments Hub）、**HUD**、**Mechanize**、**Plato**、**AfterQuery**、**Halluminate**、**Surge AI**、**Scale**、**Mercor**。
- **Prime Intellect**（`verifiers`、Florian Brand）· **Braintrust** · **Arize**（Phoenix/AX、OpenInference）· **Galileo** · **LangChain / LangSmith**（agentevals）· **Sierra**（τ-bench）· **Core Automation**（Kanav Garg）· **Epoch AI**（基准审计）· **METR**（自主性/时程）· **FutureHouse**（HLE 审计）· **UK AISI**（Inspect）。

---

<a id="notes-on-provenance-gaps"></a>
## 来源与缺口说明
- 本清单由本项目多轮研究成果（挖掘 → 对抗性核验 → 参考文献审计）与一次 `/deep-research` 检索合并而成。来源详情位于 `research/citations.md`、`research/findings.json`、`research/reference-audit.md`、`research/notes/`，完整链接清单位于 `research/url-inventory.md`（153 个 URL）。
- **高置信度核验项（deep-research，3/3 票）：**Verifier's Law、`verifiers` 库、EvalGen、Inspect AI、promptfoo、ABC 基准严谨性论文，以及 lm-eval-harness、Autoevals、agentevals、AI Agents That Matter。
- **已标记的注意事项：**MT-Bench 的 10/25 偏差数字已被作者本人谨慎限定；Lee 的“Agent Runtime”文章 URL，以及 WebArena、OSWorld、Terminal-Bench、Cybench 链接仍需核验；Kanav Garg 演讲目前通过会议摘要引用（尚无规范的一手 URL）。

<a id="deep-notes"></a>
## 深度笔记

本仓库在 [`notes/`](notes/) 中提供 **143 篇深度阅读笔记**——面向最高信号来源的结构化摘要，包含要点、**已核验引述（中文翻译）**和主题：

- [`notes/articles/`](notes/articles/)——博客文章与从业者随笔
- [`notes/talks/`](notes/talks/)——47 场已转录的演讲、播客与课程（带 `[mm:ss]` 时间戳）
- [`notes/papers/`](notes/papers/)——由引文图谱发现的论文

<a id="contributing"></a>

<a id="license"></a>
## 许可证

[![CC0](https://licensebuttons.net/p/zero/1.0/88x31.png)](LICENSE)

在法律允许的最大范围内，[BenchFlow](https://benchflow.ai) 及贡献者已放弃其对本作品享有的全部版权及相关权利（CC0 1.0）。所链接资源仍分别适用其各自的许可证。
