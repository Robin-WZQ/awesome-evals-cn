# 工作流笔记——基准真值错误：（1）FutureHouse/Andrew White 对 Humanity's Last Exam（HLE）化学与生物学题目的审计；（2）Epoch AI 对 FrontierMath 第 1–4 级的 AI 辅助复核及 v2 修订；（3）OpenAI 停止使用 SWE-bench Verified

**作者：** HLE 部分为 FutureHouse（联合创始人兼科学负责人 Andrew D. White）；FrontierMath 部分为 Epoch AI；SWE-bench Verified 部分为 OpenAI
**URL：** https://www.futurehouse.org/research-announcements/hle-exam
**已找到：** 是

## 摘要

2025–2026 年间三个彼此独立的事件都指向同一个令人不安的结论：由专家编写的“前沿”基准经常带着错误的真值标签发布，而且这些错误并非随机噪声，而是具有系统性。（1）HLE：FutureHouse 使用其文献智能体 PaperQA2（Crow）审计 Humanity's Last Exam 中 321 道纯文本化学和生物学题目，随后由相互独立的人类化学/生物学专家裁决其中 150 道。他们估计有 29 ± 3.7%（95% 置信区间）的答案与同行评审文献直接冲突，也就是很可能是错的。他们将根因归于基准的构建激励：题目必须能难倒前沿模型，这会筛选出对抗性的“刁钻题”，同时不鼓励评审者核验答案（如果核验耗时超过五分钟，评审者无需继续核验）。HLE 制作者 Scale AI 随后修订其预印本，并报告自己检查得到的错误率为 18%。（2）FrontierMath：Epoch AI 对 FrontierMath 第 1–4 级进行 AI 辅助复核，“约三分之一的题目”被标记存在“致命错误”，其中大多数标记被认为有效。此后，他们于 2026-06-12 发布修正版 v2，处理了 42% 题目中的错误（第 1–3 级修正 123 道、删除 5 道；第 4 级修正 12 道、删除 7 道）。值得注意的是，原第 1–4 级“介绍”页面此前声称只有约 1/20（约 5%）的题目存在错误，并称这“与其他主要机器学习基准相当”；实际错误率约为其自报估计的 6–8 倍。（3）SWE-bench Verified：OpenAI 审计了其模型在 64 次独立运行中持续失败的 138 个任务，发现其中 59.4% 本身有问题，于是停止报告该基准成绩。其中，35.5% 要求使用题目从未提及的特定函数名，18.8% 检查原始问题中不存在的功能。这意味着评分工具会把正确解答判为失败。OpenAI 还发现 GPT-5.2、Claude Opus 4.5 和 Gemini 3 Flash 都在训练中见过该基准的解答（存在污染），并转而推荐 SWE-bench Pro；在 Verified 上达到约 70–80% 的模型，在 Pro 的公开划分上降至约 23%。

## 要点

- HLE：321 道纯文本化学/生物学 HLE 题目中，有 29 ± 3.7%（95% 置信区间）的答案与同行评审文献直接冲突；PaperQA2/Crow 先标记冲突，再由独立人类专家裁决 321 个输出中的 150 个。
- 根因在于黄金集合的构建激励，而非随机错误：题目必须让前沿模型答错，于是产生对抗性的“刁钻题”；若核验答案需要超过五分钟，评审者无需核验。
- HLE 作者 Scale AI 随后修订预印本，并报告其自行检查得到的错误率为 18%——也就是说，连基准所有者也承认标签错误率约为五分之一。
- FrontierMath：Epoch AI 的 AI 辅助复核将“约三分之一的题目”标记为存在“致命错误”，其中多数标记被认为有效；2026-06-12 发布的修正版 v2 处理了 42% 题目中的错误。
- FrontierMath v2 的具体修改：第 1–3 级修正 123 道、删除 5 道；第 4 级修正 12 道、删除 7 道；修正后完整数据集约有 338 道题。
- FrontierMath 的自我估计严重失准：原第 1–4 级页面声称错误率约为 1/20（约 5%），且“与其他主要机器学习基准相当”；AI 辅助复核发现的错误约为其 6–8 倍。
- SWE-bench Verified：OpenAI 审计模型在 64 次运行中失败的 138 个任务，发现 59.4% 本身有问题——基准不只是很难，而是在把正确答案判错。
- SWE-bench 的两种不同故障模式都有量化结果：35.5% 的问题要求题面从未说明的特定函数名；18.8% 测试原始问题中根本没有的功能。
- SWE-bench Verified 还受到污染：GPT-5.2、Claude Opus 4.5 和 Gemini 3 Flash 都曾在训练中见过其解答；OpenAI 目前转向 SWE-bench Pro，而在 Verified 上分数很高的模型在 Pro 上降到约 23%。
- 贯穿三例的模式是：基准越难、越强调“专家”或“前沿”，其真值反而越糟，因为在验证成本最高的地方，对抗性筛选和评审疲劳恰好最容易侵蚀标签质量。

## 已核验引述（中文翻译）

- “29 ± 3.7%（95% 置信区间）”——FutureHouse，《Humanity's Last Exam 约 30% 的答案有误》（针对纯文本化学/生物学子集）（https://www.futurehouse.org/research-announcements/hle-exam）
- “Crow 发现，在给出的解释中有 53.3%（n=171）与已发表证据直接冲突。”——FutureHouse 的 HLE 错误分析（https://www.futurehouse.org/research-announcements/hle-exam）
- “我们认为，这是构建该基准时采用的激励机制所致。”——FutureHouse 的 HLE 错误分析（https://www.futurehouse.org/research-announcements/hle-exam）
- “科学前沿实际上并非客观、单一而无歧义；正因如此，它才是前沿。”——FutureHouse 的 HLE 错误分析（https://www.futurehouse.org/research-announcements/hle-exam）
- “因此，出题者必须验证前沿模型无法正确回答这些问题。”——bohaska，LessWrong 上对 FutureHouse HLE 分析的转载摘要（https://www.lesswrong.com/posts/JANqfGrMyBgcKtGgK/about-30-of-humanity-s-last-exam-chemistry-biology-answers）
- “我们正在对 FrontierMath 第 1–4 级进行 AI 辅助复核。复核已将约三分之一的题目标记为存在致命错误，我们认为其中大多数标记有效。彻底完成人工复核后，我们会发布在修正数据集上的更新分数。”——Epoch AI（X 上的 EpochAIResearch）；通过搜索引擎结果摘要核验，主帖返回 HTTP 402，无法直接加载（https://x.com/EpochAIResearch/status/2053995435870892048）
- “2026-06-12，我们发布了一次重大更新，处理了 42% 题目中的错误。”——Epoch AI，FrontierMath 基准页面（https://epoch.ai/benchmarks/frontiermath-tiers-1-3-v2）
- “我们发布了 v2，其中修正了第 1–3 级的 123 道题，以及第 4 级的 12 道题。”——Epoch AI，FrontierMath 更新日志（https://epoch.ai/benchmarks/frontiermath-tier-4-v2）
- “约有 1/20 的题目存在需要修正的错误——与其他主要机器学习基准的错误率相当。”——Epoch AI，原 FrontierMath 第 1–4 级“介绍”页面（复核前的自我估计，比 AI 辅助复核结果低约 6–8 倍）（https://epoch.ai/frontiermath/tiers-1-4/about）
- “团队审计了 GPT-5.2 在 64 次独立运行中持续失败的 138 个任务。”——Decrypt 对 OpenAI《为何我们不再评估 SWE-bench Verified》的摘要（OpenAI 原文返回 HTTP 403）（https://decrypt.co/359012/openai-benchmark-measure-ai-coding-supremacy-contaminated）
- “其中 59.4% 的任务本身有问题。”——Decrypt 对 OpenAI 关于 SWE-bench Verified 结论的摘要（https://decrypt.co/359012/openai-benchmark-measure-ai-coding-supremacy-contaminated）
- “35.5% 的任务测试写得过于狭窄，要求使用问题说明中从未提到的特定函数名。另外 18.8% 检查的功能根本不属于原始问题。”——Decrypt 对 OpenAI 关于 SWE-bench Verified 结论的摘要（https://decrypt.co/359012/openai-benchmark-measure-ai-coding-supremacy-contaminated）

## 它补充了什么

本书原则 P6 主张，在留出黄金集合之前应先取得专家共识；P16 则指出评测会腐化。本材料提供了量化硬证据，说明即使基准由知名机构旗下的领域专家编写（Scale AI/HLE、Epoch、SWE-bench 团队），黄金集合也可能在诞生时就已经错误，而不只是日后过时。它给出三组来自 2025–2026 年独立审计的具体错误率：HLE 约 29%（Scale 自查为 18%）；FrontierMath 约 33% 被标记、42% 得到修正；SWE-bench Verified 中模型失败任务有 59.4% 本身损坏。更关键的是，它指出了本书尚未明确命名的因果机制：对抗性的构建激励（“必须难倒模型”）加上评审疲劳（“若超过 5 分钟就不核验”）会主动制造错误标签。它还提供了一个自我校准的警示案例——Epoch 复核前估计约 5%，并称“与其他基准相当”，结果实际数字高出 6–8 倍；同时也展示了标签错误如何伪装成模型失败（SWE-bench 把正确修复判错）。当一个号称“可验证”的奖励实际上来自损坏的验证器时，这正是 P11/P3 所警告的噩梦。

**涉及原则：** 6、16、11、3、12

## 整合建议

把这些案例作为 P6 和 P16 的锚点案例。在 P6“留出之前先取得共识”中，可基于 HLE 增加一个侧栏“当专家不仅彼此意见不一，而且与现实不符时”：同时呈现 FutureHouse 的 29% 与 Scale 自查的 18%，说明即便题目由专家编写，仍可能有五分之一到三分之一的标签错误；再明确失败机制——筛选能难倒模型的题目，会让激励从正确性转向刁钻性，而限时审查（“超过 5 分钟则不核验”）则保证部分答案得不到检查。规则应写成：黄金集合的资格并非来自“由专家编写”，而是来自对标签本身的独立再裁决；在相信任何低分之前，都应预算对最强模型失败题目的专项审计。

在 P16“评测会腐化”中，将 FrontierMath 和 SWE-bench Verified 作为两个旗舰案例。FrontierMath 说明自报错误率不可信（声称 5%，实际约 33–42%），同时说明 AI 辅助再审是一种可行的腐化检测工具；SWE-bench Verified 展示了终末阶段——污染叠加损坏评分器——以及行业应对方式：停用它，转向更困难的留出划分，而模型分数从约 70–80% 跌至约 23%。再把 SWE-bench“将正确修复判错”的问题接回 P11/P12：如果验证器没有用已知正确解答审计过，那么它本身就是一个未经测量的裁判。可加入一则可执行的提示框：（a）审计最强模型失败的每一个条目；（b）独立重新推导基准错误率，不要直接相信作者自报数字；（c）当抗污染版本上分数突然崩落时，应将其视为主流基准已腐化的信号。
