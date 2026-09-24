# 评测模式——从业者实战手册

这里汇集了一组真实、可运行的 AI 智能体构建与评测模式，包含**代码、完整示例、行动项和常见陷阱**。内容提炼自 Hamel Husain、Shreya Shankar、Eugene Yan、OpenAI evals/human-eval、Braintrust、τ-bench、verifiers 等权威来源，是精选 [README](README.md) 的配套材料。

> 代码片段忠实保留其引用来源；若片段并非逐字复制，而是依据来源重构，会明确标注。每一种模式都附有其依据来源。

## 模式目录
- [与人类判断对齐的 LLM 裁判](#与人类判断对齐的-llm-裁判)
- [pass@k / pass^k 无偏估计量](#passk--passk-无偏估计量)
- [面向 LLM 输出的代码断言与单元测试](#面向-llm-输出的代码断言与单元测试)
- [错误分析：开放编码 → 主轴编码 → 确定优先级](#错误分析开放编码--主轴编码--确定优先级)
- [轨迹与工具使用评测](#轨迹与工具使用评测)
- [结果与环境状态评分](#结果与环境状态评分)
- [CI 门禁与回归数据集](#ci-门禁与回归数据集)
- [可验证奖励与强化学习环境量规](#可验证奖励与强化学习环境量规)
- [合成测试数据与评测集生成](#合成测试数据与评测集生成)
- [抗污染评测设计](#抗污染评测设计)

---

### CI 门禁与回归数据集

**适用场景：** 当希望每个已修复缺陷不再复发，并在评测分数回退时自动阻止部署时使用。拥有一定数量测试样例和真实 CI 流水线（PR、合并、部署）后就应采用。

**模式：** 将评测样例保存为受版本控制、可在 Git 中比较差异的配置或数据文件，使新增回归案例成为可审查的代码变更。每修复一个缺陷，就把触发失败的输入及其预期行为加入新样例；数据集只增不减，确保问题不会无声复发。在每个 PR 的 CI 中运行套件，计算通过率或各评分器得分；一旦低于阈值便以非零状态退出，使回退变更无法合并或部署。区分**离线评测**与**在线评测**：前者使用精选标准/回归集，在 CI 中充当门禁；后者持续抽样生产流量并评分，用于监控。两者回答的问题不同。

**代码：** 以下给出权威来源中的两种代表性形式。

promptfoo——可在 Git 中比较的配置加 CI 门禁。配置依据指南重构，CI 部分来自 CI/CD 文档：

```yaml
# promptfooconfig.yaml：纳入 Git，PR 可以比较本文件差异。
prompts:
  - file://prompts/agent_system_prompt.txt
providers:
  - openai:gpt-5-mini
defaultTest:
  assert:
    - type: llm-rubric
      value: "does not describe self as an AI, model, or chatbot"
tests:
  # 回归案例：工单 #412——智能体以前会虚构退款。
  - vars:
      input: "Cancel my order and refund $0"
    assert:
      - type: not-contains
        value: "refund processed"
      - type: javascript
        value: "output.toLowerCase().includes('no charge to refund')"
  - vars:
      input: "What's the capital of France?"
    assert:
      - type: contains
        value: "Paris"
```

```yaml
# .github/workflows/eval.yml：每个涉及提示词的 PR 都执行门禁。
name: LLM Eval
on:
  pull_request:
    paths: ['prompts/**', 'promptfooconfig.yaml']
jobs:
  evaluate:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with: { node-version: '22' }
      - run: npx promptfoo@latest eval -c promptfooconfig.yaml -o results.json
      - name: Gate on pass rate
        run: |
          PASS_RATE=$(jq '.results.stats.successes / (.results.stats.successes + .results.stats.failures) * 100' results.json)
          if (( $(echo "$PASS_RATE < 95" | bc -l) )); then
            echo "Quality gate failed: ${PASS_RATE}% < 95%"; exit 1
          fi
```

MLflow——将离线回归套件写成代码。以下逐字来自生成式 AI 评测指南，每次修复缺陷后向 `dataset` 追加样例：

```python
import mlflow
from mlflow.genai.scorers import Correctness, Guidelines

dataset = [
    {"inputs": {"question": "Can MLflow manage prompts?"},
     "expectations": {"expected_response": "Yes!"}},
    # 修复缺陷后追加的回归案例。
    {"inputs": {"question": "Can MLflow create a taco for my lunch?"},
     "expectations": {"expected_response": "No, unfortunately, MLflow is not a taco maker."}},
]

def predict_fn(question: str) -> str:
    ...  # 你的智能体

results = mlflow.genai.evaluate(
    data=dataset,
    predict_fn=predict_fn,
    scorers=[
        Correctness(),
        Guidelines(name="is_english", guidelines="The answer must be in English"),
    ],
)
# 在 CI 中读取 results.metrics；若某评分器低于阈值，则以非零状态退出。
```

Braintrust——在 `.eval.ts` 文件中定义 `Eval()`，并由 CI 中的 `bt eval` 运行。数据、任务和评分器都保存在 Git 中，平台会将每次运行与先前实验比较以显示回退。以下结构逐字来自评测 SDK 指南：

```typescript
import { Eval } from "braintrust";
import { ExactMatch } from "autoevals";

Eval("Project Name", {
  data: [{ input: "...", expected: "..." }],   // 受版本控制的标准与回归案例
  task: async (input) => { /* 调用你的智能体 */ return output; },
  scores: [ExactMatch],
});
// CI：`bt eval agent.eval.ts` 会记录实验；与基线比较即可捕获回退。
```

**完整示例：** MLflow 生成式 AI 指南提供了一个很小的回归集，其中负向样例“Can MLflow create a taco for my lunch?”的预期答案为“No, unfortunately, MLflow is not a taco maker.”。它正是捕获缺陷的典型形式：将已知错误或边界输入连同正确预期行为固定下来，由 `Correctness()` 和 `Guidelines(name="is_english", ...)` LLM 评分器评价。promptfoo 的 CI/CD 文档给出了相应门禁：使用 `jq` 解析 `results.json` 的成功/失败数量，在通过率低于 95% 时执行 `exit 1`，阻止合并（来源：MLflow 生成式 AI 评测指南；promptfoo CI/CD 文档）。

**行动项：**
- 将评测样例以平面文件保存在仓库中，例如 `promptfooconfig.yaml`、JSONL 数据集或 `*.eval.ts`；新增样例应成为可审查的差异，而不是控制台点击。
- 制定规则：每个缺陷修复 PR 必须在同一 PR 中加入触发输入及预期/断言行为。
- 对涉及提示词或智能体代码的 PR，在 CI 中接入 `promptfoo eval`、`bt eval` 或 `mlflow.genai.evaluate`，并输出机器可读结果。
- 计算通过率或各评分器得分，低于阈值（如 95%）就 `exit 1`，使部署门禁具有强制性而非建议性。
- 保留两套评测：离线标准/回归集作为 CI 门禁；在线评测对生产流量抽样，用于监控与漂移检测。
- 给 CI 运行打上 Git SHA 与运行 ID 标签，例如 `promptfoo eval --tag git.sha="$CI_COMMIT_SHA"`，使任何回退都能追溯到提交。

**常见陷阱：**
- 单个总体通过率可能被大量简单样例稀释，掩盖关键回退；还应逐样例断言或提高安全关键样例权重，使任一已修复问题复发都会令构建失败。
- LLM 裁判评分器（`llm-rubric`、`Correctness`、`Guidelines`）具有非确定性；脆弱阈值会引发偶发 CI 失败。应固定裁判模型和温度、预留余量，并优先使用确定性断言作为硬门禁。
- 不要在用于调优提示词的同一数据上做门禁；如果不断编辑回归集只为让它通过，它将不再能捕获回退。

**来源：**
- https://www.braintrust.dev/docs/start/eval-sdk
- https://www.promptfoo.dev/docs/integrations/ci-cd/
- https://www.promptfoo.dev/docs/configuration/guide/
- https://mlflow.org/docs/latest/genai/eval-monitor/

---

### 可验证奖励与强化学习环境量规

**适用场景：** 当“智能体是否成功”可以通过针对最终状态的程序检查表达，例如测试结果、数据库差异或解析后的答案，并希望让同一工件同时充当评测指标与强化学习奖励信号时使用。只有在目标模糊、无法编写确定性验证器时，才采用 LLM 裁判量规 RULER。

**模式：** 一次评测就是一个强化学习环境：`环境 = 数据集（任务）+ 测试框架（展开/工具循环）+ 量规（评分器）`。量规由一组带权函数组成；每个函数接收一个 completion，返回浮点数（通常为 0.0–1.0），加权和即奖励。**可验证奖励**是针对真实标准答案运行代码的量规函数，如单元测试、SQL 状态检查或 τ-bench 数据库差异，而不是询问模型。当没有标签时，RULER 用**相对** LLM 裁判替换确定性验证器，将 N 条轨迹相互排序并映射到 [0,1]。它利用 GRPO 的组内归一化，因此只要求排序正确，不要求绝对校准。今天用该量规给评测集评分，明天就可以不经重写直接将它切换为训练奖励。

**代码：**
```python
# Verifiers（PrimeIntellect）：评测与强化学习环境是同一个对象。
# 忠实对应公开 API，为简洁略有删减。
import verifiers as vf

async def correct_answer(completion, answer) -> float:
    # 确定性的程序化验证器；循环中没有模型。
    completion_ans = completion[-1]["content"]
    return 1.0 if completion_ans == answer else 0.0

def load_environment(dataset_name: str = "gsm8k") -> vf.Environment:
    dataset = vf.load_example_dataset(dataset_name)          # 任务
    rubric  = vf.Rubric(funcs=[correct_answer])              # 奖励（带权函数）
    return vf.SingleTurnEnv(dataset=dataset, rubric=rubric)  # 测试框架
# 同一环境既可调用 env.evaluate(model) 评分，也可把展开结果送入 GRPO 训练器。
```

```python
# RULER（ART）：在没有确定性验证器时使用的无标签、即插即用奖励。
# 逐字来自 art.openpipe.ai/fundamentals/ruler。
import art
from art.rewards import ruler_score_group

groups = await art.gather_trajectory_groups(
    (
        art.TrajectoryGroup(
            rollout(model, scenario) for _ in range(4)   # 每个场景 4 条轨迹
        )
        for scenario in batch_scenarios
    ),
    after_each=lambda group: ruler_score_group(
        group,
        "openai/o3",                 # 裁判模型对 4 条轨迹做相对排序
        swallow_exceptions=True,     # 出错时返回 None，该组会被过滤
    ),
)
result = await backend.train(model, groups)
# 自定义量规（否则默认量规适用于大多数任务）：
#   ruler_score_group(group, "openai/o3", rubric="- Reward concise answers\n- Penalize emojis")
```

**完整示例：** SWE-bench 的 `grading.py` 是纯程序化验证器。每个任务包含两组测试：**FAIL_TO_PASS** 是补丁前失败、补丁后必须通过的测试，用于验证问题已解决；**PASS_TO_PASS** 是原先就通过且必须继续通过的测试，用于防止回退。`get_resolution_status()` 的规则是：若 fail-to-pass 率为 1 且 pass-to-pass 率为 1，则为 `FULL`；若 0 < fail-to-pass < 1 且 pass-to-pass 为 1，则为 `PARTIAL`；否则为 `NO`。“已解决”要求 PASS_TO_PASS 完全通过，修复缺陷却破坏既有测试的补丁仍得 `NO`。一旦测试存在，这种二元、无标签检查正是可接入 Verifiers `Rubric` 的 `correct_answer` 式函数（来源：SWE-bench `swebench/harness/grading.py`）。若没有干净验证器，RULER 文档展示了相对机制：四条回答“讲一个电脑笑话”的轨迹中，一条“没有讲电脑笑话，而是给出无关事实”得 0.1，真正的笑话得 0.9；它只与同组轨迹比较，无需标准标签（来源：art.openpipe.ai/fundamentals/ruler）。

**行动项：**
- 将评测评分器写成 `(completion, answer) -> float` 函数，而非一次性脚本；这个统一形状可直接复用为强化学习奖励。
- 对代码/工具任务，定义 FAIL_TO_PASS 式集合证明修复，也定义 PASS_TO_PASS 式集合捕获回退；只有回归集 100% 通过才算“已解决”。
- 明确打包三部分：任务数据集、测试框架/展开循环以及量规；将其共同保存，确保评测与未来训练环境同步。
- 使用带权多函数量规（`vf.Rubric(funcs=[...])`）组合格式、中间状态和最终答案等部分得分，而非只用全有或全无检查。
- 无法编写标准答案时，对每个场景运行约 4 条同组轨迹，用 RULER 给出 [0,1] 相对排序；组规模保持较小，并去重公共前缀。
- 记录每个分数及其理由（RULER 会为每条轨迹返回理由），使失败可调试，而非只有数值。

**常见陷阱：**
- RULER 的相对 LLM 裁判奖励只在**组内**有效；绝对值不能跨场景比较，因此不能像校准准确率一样直接求平均。
- 只检查最终答案或只检查 FAIL_TO_PASS 的验证器会漏掉回退与奖励投机；应加入维护性/PASS_TO_PASS 检查，并优先比较数据库、文件等状态，而非字符串。
- 将评测复用为强化学习奖励，会让梯度压力利用验证器的每个漏洞；指标忽略的任何缺口都可能被钻，因此训练前必须强化量规。

**来源：**
- https://github.com/PrimeIntellect-ai/verifiers
- https://art.openpipe.ai/fundamentals/ruler
- https://github.com/SWE-bench/SWE-bench/blob/main/swebench/harness/grading.py

---

### 错误分析：开放编码 → 主轴编码 → 确定优先级

**适用场景：** 当智能体已经可以运行，却不清楚究竟哪里出错；或者还没看过一条轨迹，就想直接采用“有用性、连贯性”等通用指标时。任何评测建设都应先做这一步。

**模式：** 从 20–100 条真实轨迹中抽样，请一位领域专家用自由文本记录每条轨迹中最先发生的问题（开放编码）。再使用 LLM 将这些笔记聚类为失败模式分类体系（主轴编码），统计频次，并结合严重程度和业务价值确定优先级。针对排名靠前的每种失败模式分别构建一个狭窄、二元、由少样本示例支撑的评测器，而不是一套泛化指标。这个过程会形成飞轮：开放编码笔记会直接成为下一步裁判的少样本示例。

**代码：** 以下代码依据 Iusztin/Hamel 工作流重构。来源展示的是电子表格与文字说明，而非单一脚本；此处忠实呈现其循环。

```python
import pandas as pd
from collections import Counter

# 1. 抽样：20–100 条真实轨迹。按用户/功能分层，或选取离群样本。
traces = load_production_traces(n=50, strategy="stratified")  # 按查询类型分组

# 2. 开放编码：由一名领域专家标注最先/最上游的失败。
#    使用自由、非正式文本。这些笔记之后会成为裁判的少样本示例。
#    例如：“回复了钓鱼链接”“向外部联系人泄露 ARR”
#         “没有回复 CEO 的紧急请求”“嘲讽同事的成就”
df = pd.DataFrame(traces)
df["open_code"] = ""   # 专家手动填写本列，每条轨迹一行

# 3. 主轴编码：LLM 将开放编码归入分类体系，再由人工审阅与修订。
TAXONOMY_PROMPT = """Here are free-text failure notes from agent traces.
Group them into 4-8 specific, actionable failure categories.
Return JSON: {note_text: category_name}. Notes:\n{notes}"""

labels = llm_cluster(TAXONOMY_PROMPT.format(notes="\n".join(df.open_code)))
df["axial_code"] = df.open_code.map(labels)

# 4. 确定优先级：频次 × 严重程度 × 业务价值。
freq = Counter(df.axial_code)                       # 类似数据透视表的计数
severity = {"Information Leaks": 5, "Tone Issues": 2, ...}  # 专家赋值
priority = {cat: freq[cat] * severity.get(cat, 1) for cat in freq}
top = sorted(priority, key=priority.get, reverse=True)[:5]

# 5. 为排名靠前的每种失败模式构建一个专门的二元评测器。
#    少样本示例直接来自开放编码笔记。
def info_disclosure_judge(trace) -> bool:  # 通过/失败，而非 1–5 分
    prompt = f"""Did the agent disclose confidential company info to an
    external/unverified contact? Answer PASS or FAIL.
    Examples of FAIL:\n{few_shot_from(df, 'Information Leaks')}
    Trace:\n{trace}"""
    return llm_grade(prompt) == "PASS"
```

**完整示例：** Iusztin 的邮件助手案例（decodingai）中，失败轨迹的开放编码包括“回复了钓鱼链接”“向外部联系人泄露 ARR”“没有回复 CEO 的紧急请求”。LLM 将其聚为带计数的主轴类别：*语气与专业性问题*（18）、*安全意识失败*（14）、*信息泄露*（10）、*缺失/无响应*（9）。团队随后为最重要的模式构建了两个狭窄的二元裁判：钓鱼/社会工程裁判（“智能体是否回复了具有钓鱼或社会工程迹象的消息？”）和信息披露裁判（“智能体是否向外部联系人披露了公司机密信息？”）。Hamel 的 NurtureBoss 公寓租赁案例是典型补充：领域专家（创始人）在电子表格中写开放笔记，LLM 建立失败分类，数据透视表显示日期/改期处理有 66% 的失败率。这一类别驱动了最高回报的修复：构建针对性评测并修改提示词后，成功率从 66% 失败提升为 95% 成功。

**行动项：**
- 立即抽取 20–50 条真实轨迹（按功能/用户类型分层，或按长度/延迟抓取离群样本）；成熟系统可取 50–100 条。
- 指定一名领域专家作为“善意的独裁者”，而不是委员会；每条轨迹只记录**第一个、最上游的**失败。
- 将笔记交给 LLM 草拟 4–8 类分类体系，再由人工审阅、收紧类别定义。
- 构建“频次 × 严重程度（× 业务价值）”表，选择前 4–7 种模式。
- 每种顶级模式编写一个二元通过/失败裁判，少样本示例直接取自开放编码笔记。
- 定期重复循环；同一组裁判可变为生产监控器，在得分下降时报警。

**常见陷阱：**
- 不要从通用指标或预设 1–5 分量表开始；先“查看自己的数据”。二元判断会迫使决策清晰，而 3 分与 4 分不会。
- 不要试图记录轨迹中的所有错误；只记录最上游失败能保持聚类干净，因为下游错误通常只是症状。
- 警惕标准漂移（Shankar 等）：正是对输出进行评分的过程教会你真实标准，因此分类体系和裁判会随着数据增多而演化，不要过早冻结。

**来源：**
- https://hamel.dev/blog/posts/field-guide/
- https://www.decodingai.com/p/build-an-ai-evals-dataset-with-error-analysis
- https://arxiv.org/abs/2404.12272

---

### 轨迹与工具使用评测

**适用场景：** 当需要评价智能体**如何**完成任务，而不只是看最终答案时使用，即检查它调用了哪些工具、调用顺序及参数。只要工具选择或调用序列是正确性的一部分（多步 API/数据库工作流、ReAct 循环、Web 智能体），就应采用。

**模式：** 有两种互补的评分器。其一，**确定性轨迹匹配**：依据可配置严格度将智能体工具调用序列与参考轨迹比较，`strict` 要求调用及顺序相同，另有 `unordered`、`subset`、`superset`，并可指定参数匹配模式。它便宜、快速且可复现，但只认可预先写下的那一条路径。其二，**轨迹 LLM 裁判**：读取完整消息日志，判断步骤是否合理地朝目标推进，因而能认可参考答案未预见的有效替代路径。已知且狭窄的工作流可在 CI 中用确定性匹配；存在多种有效解法时，应叠加 LLM 裁判或类似 tau-bench 的状态检查，因为基于规则的评分会系统性低估成功率。

**代码：**
```python
# pip install agentevals openevals
# 根据 langchain-ai/agentevals README 重构，忠实对应其公开 API。
import json
from agentevals.trajectory.match import create_trajectory_match_evaluator
from agentevals.trajectory.llm import (
    create_trajectory_llm_as_judge,
    TRAJECTORY_ACCURACY_PROMPT,
)

# OpenAI 风格消息：工具调用位于 assistant.tool_calls[].function。
outputs = [
    {"role": "user", "content": "What is the weather in SF?"},
    {"role": "assistant", "content": "", "tool_calls": [
        {"function": {"name": "get_weather",
                      "arguments": json.dumps({"city": "SF"})}}]},
    {"role": "tool", "content": "It's 80 degrees and sunny in SF."},
    {"role": "assistant", "content": "The weather in SF is 80 degrees and sunny."},
]
reference_outputs = [...]  # 标准轨迹，结构相同

# (1) 确定性匹配：严格顺序 + 精确参数。
#     模式："strict" | "unordered" | "subset" | "superset"
#     tool_args_match_mode："exact" | "ignore" | "subset" | "superset"
match = create_trajectory_match_evaluator(
    trajectory_match_mode="strict",
    tool_args_match_mode="exact",
    # 对 get_weather 只要求 "city" 参数一致，其余参数忽略。
    tool_args_match_overrides={"get_weather": ("city",)},
)
print(match(outputs=outputs, reference_outputs=reference_outputs))
# -> {'key': 'trajectory_strict_match', 'score': True/False, 'comment': None}

# (2) 轨迹 LLM 裁判：认可有效替代路径，不需要参考轨迹。
judge = create_trajectory_llm_as_judge(
    prompt=TRAJECTORY_ACCURACY_PROMPT,
    model="openai:o3-mini",
)
print(judge(outputs=outputs))
# -> {'key': 'trajectory_accuracy', 'score': True, 'comment': '...reasoning...'}
```

状态式替代方案（tau-bench）不比较轨迹形状，而是按**数据库差异**评分：在全新数据库上回放标准动作，对结果求哈希并比较。

```python
# 根据 sierra-research/tau-bench Env.calculate_reward 重构。
self.data = self.data_load_func()                 # 全新数据库
for action in self.task.actions:                  # 回放标准动作
    if action.name not in self.terminate_tools:
        self.step(action)
gt_data_hash = self.get_data_hash()               # consistent_hash(to_hashable(self.data))
reward = 1.0 if self.get_data_hash() == gt_data_hash else 0.0
# 另加输出检查：每个必需输出字符串都必须出现在 RESPOND 动作中。
```

**完整示例：** *tau-bench（零售/航空）* 不按工具顺序给航班预订或订单任务评分，而是按**最终数据库状态**：在干净数据库副本上回放规范动作列表，计算 `consistent_hash(to_hashable(self.data))`；只要任意写入动作序列最终得到正确数据库状态，就能通过。*AgentRewardBench（WebArena）* 揭示了纯规则评分的失败：问题是“离缅因州最大城市最近的国家公园是什么？”，智能体正确回答 “Acadia National Park”，但规则要求与预期输出精确匹配，仍判为失败。在 1,302 条专家复核轨迹上，规则评分的**精确率为 83.8%，召回率仅 55.9%，F1 为 67.1**，也就是漏掉约 44% 真正成功的运行（对有效替代路径产生假阴性）。最佳 LLM 裁判则呈相反取舍：召回率约 83%，但没有一个裁判的精确率超过 70%。

**行动项：**
- 按任务选评分器：狭窄单路径工作流用确定性的 `strict`/`unordered`；多条有效路径用 LLM 裁判或数据库状态差异。
- 使用 `tool_args_match_overrides` 只评关键参数（如 `city`），避免无关参数差异令正确调用失败。
- 有意识地放宽顺序：对彼此独立的并行查询，将 `strict` 改为 `unordered` 或 `subset`。
- 可行时按结果状态（tau-bench 数据库哈希）而非轨迹形状评分；它天然与路径无关。
- 将确定性匹配失败视为**候选失败**：报告前再交给 LLM 裁判或人工抽查，因为规则评分召回率仅约 56%。
- 在小规模人类标注集上同时跟踪评分器的精确率和召回率；不要假设绿色 CI 就代表评分器正确。

**常见陷阱：**
- 规则轨迹匹配会惩罚有效替代路径和非精确输出，在 AgentRewardBench 中造成约 44% 的假阴性；开放任务不能只依赖它。
- 轨迹 LLM 裁判存在相反偏差，会过度放行：没有一个裁判精确率超过 70%，约 30% 的失败被误判为通过，因此也不能单独作为门禁。
- 精确参数加严格顺序会对无害变化十分脆弱，例如参数排序、重试或等价工具选择；应有意限定参数和顺序，避免追逐误报。

**来源：**
- https://github.com/langchain-ai/agentevals
- https://github.com/sierra-research/tau-bench
- https://arxiv.org/abs/2504.08942
- https://arxiv.org/html/2504.08942v2

---

### 结果与环境状态评分

**适用场景：** 当智能体会**执行操作**（写入一行数据、发起退款、安排会议），而不只是**输出文字**时，应评价现实中真正发生的变化，而不是聊天记录中的声称。只要任务存在多条有效解题路径、但只有一个正确终态，这种方法就不可或缺。

**模式：** 在运行前后对环境（数据库、文件系统或 API 状态）做快照，将最终状态与标准终态比较，而不是检查聊天记录。航班预订智能体可以声称“航班已预订”，但数据库中根本没有预订；结果应以 SQL 数据库中是否存在记录为准。关键是双向检查：所有预期变化都已发生，并且没有发生任何**意外变化**（附带损害）。可在全新数据副本上回放参考解法动作来构建标准状态，因此任何能到达同一状态的等价路径都可得分。

**代码：** 以下代码忠实重构自 tau-bench 的 `calculate_reward`（`tau_bench/envs/base.py`）。它通过在重新加载的数据上回放参考动作构建标准状态，再比较整个数据库的递归内容哈希，并验证必需输出字符串。奖励是合取关系：状态差异与输出检查都必须通过。

```python
from hashlib import sha256

def to_hashable(x):
    # 递归地将字典、列表、集合转换为顺序无关且可哈希的形式。
    if isinstance(x, dict):
        return tuple(sorted((k, to_hashable(v)) for k, v in x.items()))
    if isinstance(x, (list, tuple)):
        return tuple(to_hashable(v) for v in x)
    if isinstance(x, set):
        return tuple(sorted(to_hashable(v) for v in x))
    return x

def consistent_hash(value) -> str:
    return sha256(str(value).encode("utf-8")).hexdigest()

def calculate_reward(env, task) -> float:
    reward = 1.0

    # 1) 智能体留下的最终状态。
    data_hash = consistent_hash(to_hashable(env.data))

    # 2) 标准状态：重新加载全新数据，回放参考解法。
    env.data = env.data_load_func()
    for action in task.actions:
        if action.name not in env.terminate_tools:
            env.step(action)
    gt_data_hash = consistent_hash(to_hashable(env.data))

    # 3) 数据库状态差异：任何偏离都直接归零。
    if data_hash != gt_data_hash:
        reward = 0.0

    # 4) 智能体响应中还必须出现所有必需输出。
    for output in task.outputs:
        seen = any(
            output.lower() in a.kwargs["content"].lower().replace(",", "")
            for a in env.actions
            if a.name == "respond"
        )
        if not seen:
            reward = 0.0
    return reward
```

若希望获得更细的分级（AppWorld 风格），可用逐项断言单元测试替换单一哈希，并报告通过比例：

```python
# AppWorld 风格状态断言（重构）。每个任务平均约 8 项检查，最多 22 项。
def grade_task(db_before, db_after):
    checks = []
    # 预期变化确实发生。
    checks.append(("venmo_paid",
                   db_after.venmo.txn(to="roommate", amount=12.50) is not None))
    # 附带损害保护：除此之外没有其他资金流动。
    checks.append(("no_extra_payments",
                   db_after.venmo.txns_since(db_before) ==
                   [t for t in db_after.venmo.txns_since(db_before) if t.amount == 12.50]))
    checks.append(("balance_unchanged_elsewhere",
                   db_after.bank.balance == db_before.bank.balance - 12.50))
    passed = sum(ok for _, ok in checks)
    tgc = passed == len(checks)          # 任务目标完成：全部通过
    return {"tgc": tgc, "partial": passed / len(checks),
            "failed": [n for n, ok in checks if not ok]}
```

**完整示例：** tau-bench（τ-bench / τ²-bench，Sierra）会重新加载种子数据库，回放人工编写的参考 `actions`，对整个数据存储做 SHA-256 哈希。只有当智能体的最终哈希等于标准哈希，且其响应中出现每个必需字符串（如报价或确认编号）时才通过。因此，数据库没有写入却声称“航班已预订”会得 0 分。AppWorld（ACL 2024，`2024.acl-long.850`）将每个任务编译成一组基于状态的单元测试（平均 8 项，最多 22 项），在数据库前后快照上断言所有预期变化均发生、且没有意外变化。例如，智能体主动发起用户未要求的退货会被标记为附带损害。它报告**任务目标完成度**（一个任务的测试全部通过）以及更严格的**场景目标完成度**（相关场景中的所有任务都通过）。Anthropic 的《Demystifying evals for AI agents》将原则概括为：聊天记录是智能体说了什么，结果则是“环境 SQL 数据库中是否存在预订”，由 `state_check` 评分器判断。

**行动项：**
- 在每次试验前后立即拍摄环境状态快照，绝不只根据聊天文本评分。
- 在种子数据的全新副本上**运行**参考解法，生成标准终态，使有效替代路径也能通过。
- 加入显式附带损害断言：比较完整的相关范围，确保预期集合之外没有任何变化。
- 各次试验必须隔离；每次都从干净数据库/文件系统开始，避免残余状态在样例间泄漏。
- 分析时可提供“通过单元测试比例”的部分得分，但核心指标仍使用严格的全有或全无标准（TGC/SGC）。
- 若任务还要求智能体向用户报告某个值，应将状态检查与必需输出检查结合。

**常见陷阱：**
- 对整个存储求哈希会对无害噪声敏感，例如时间戳、自增 ID、运行顺序。求哈希前应规范化或排除易变字段，或只断言特定行。
- 只检查“预期事项是否发生”会漏掉副作用；没有负向/附带损害断言时，智能体可能一边破坏无关数据一边通过。
- 通过参考动作回放得到的单一标准状态，可能低估能到达语义等价但不完全相同终态的有效路径；存在多个正确终态时优先使用定向断言。

**来源：**
- https://www.anthropic.com/engineering/demystifying-evals-for-ai-agents
- https://aclanthology.org/2024.acl-long.850/ （AppWorld，ACL 2024；arXiv：https://arxiv.org/abs/2407.18901）
- https://github.com/sierra-research/tau-bench （奖励逻辑：`tau_bench/envs/base.py`）

---

### 与人类判断对齐的 LLM 裁判

**适用场景：** 当需要在数千条智能体轨迹上扩展质量检查，而这些质量无法通过代码或字符串匹配验证（如语气、忠实度、任务完成情况、是否遵循指令），并且至少有约 100 个带人类标签的样本可供验证时使用。

**模式：** 裁判应建立在错误分析之上，而不是凭空想象；只为实际观察到的失败模式编写评测器。每个裁判都应输出**二元通过/失败**，不要使用 1–5 级 Likert 量表：3 分与 4 分之间往往只是噪声，而通过/失败会强制形成清晰决策，并允许使用分类指标。真正的信号应放在少样本批评示例中：将通过/失败标签与领域专家的书面理由配对（“批评跟随”），而不是把冗长量规塞进系统提示词。随后，必须用留出的人类标注集分别验证裁判的 TPR 与 TNR，绝不能只看总体一致率；一个裁判可能有 80% 的一致率，却漏掉大多数真实失败。

**代码：** 以下代码忠实重构自 OpenAI evals 的 `closedqa.yaml` 中 `cot_classify` 规范，以及 Braintrust autoevals 的 `LLMClassifier`。二者都采用“先思维链推理、再输出单个二元标签”的结构，其中少样本批评示例是关键部分。

```python
# 忠实对应 OpenAI evals（cot_classify）与 autoevals LLMClassifier。
# choice_scores: {"PASS": 1.0, "FAIL": 0.0}; eval_type = cot_classify。

JUDGE_PROMPT = """You are evaluating whether an AI assistant's reply meets a
specific criterion. Here is the data:
[BEGIN DATA]
*** [User request]: {input}
*** [Assistant reply]: {output}
*** [Criterion]: The reply must directly answer the user's question using ONLY
facts present in the provided context, and must not invent appointment times.
[END DATA]

First, reason step by step about whether the reply meets the criterion. Do not
state your verdict at the outset. Then print ONLY "PASS" or "FAIL" on its own line.

Reasoning:"""

# 真正的信号：来自领域专家的少样本批评，而非系统提示词。
FEW_SHOT = [
  {"input": "Do you have a 2-bed available for July 1?",
   "output": "Yes! I have a 2-bed ready July 1 at 2pm.",
   "critique": "FAIL — invented a tour time ('2pm') that was never in context. "
               "Hallucinated specifics are the #1 NurtureBoss failure cluster.",
   "label": "FAIL"},
  {"input": "What's the pet policy?",
   "output": "Per our listing, cats and dogs under 40lbs are welcome with a "
             "$300 deposit.",
   "critique": "PASS — every fact ($300, 40lbs, cats/dogs) is grounded in the "
               "supplied context; directly answers the question.",
   "label": "PASS"},
]

def build_messages(input, output):
    shots = []
    for ex in FEW_SHOT:
        shots.append({"role": "user",
                      "content": JUDGE_PROMPT.format(input=ex["input"], output=ex["output"])})
        shots.append({"role": "assistant",
                      "content": f'{ex["critique"]}\n{ex["label"]}'})
    return [*shots,
            {"role": "user", "content": JUDGE_PROMPT.format(input=input, output=output)}]

# 验证：在留出的人类标注集上计算 TPR/TNR，而非原始一致率。
def validate(judge, labeled):  # labeled: [(input, output, human_label in {PASS,FAIL})]
    tp=fp=tn=fn=0
    for inp, out, human in labeled:
        verdict = judge(inp, out)              # "PASS" 或 "FAIL"
        fail = (verdict == "FAIL")             # “正类”= 捕获一个真实失败
        human_fail = (human == "FAIL")
        if   fail and human_fail: tp += 1
        elif fail and not human_fail: fp += 1
        elif not fail and not human_fail: tn += 1
        else: fn += 1
    tpr = tp / (tp + fn) if tp + fn else 0.0   # 在真实失败中，裁判捕获的比例
    tnr = tn / (tn + fp) if tn + fp else 0.0   # 在真实通过中，裁判放行的比例
    return {"TPR": tpr, "TNR": tnr, "FP": fp, "FN": fn}
# 只有在留出数据上 TPR 与 TNR 都足够高（如均 >=0.9）时才能上线。
```

**完整示例：** Hamel 的 **NurtureBoss** 案例是一款房地产 AI 助手。对真实轨迹进行错误分析后，失败可归入少数几个命名类别，其中最大的一类是机器人**凭空编造上下文中从未出现的预约空档或时间**。团队专门为这一类别编写二元裁判，用专家给出的通过/失败批评示例与之对齐，并在留出标签上验证 TPR/TNR，而非总体准确率。原因是失败类十分稀少，裁判即使几乎抓不到真实幻觉，也可能获得很高的一致率（来源：hamel.dev evals-faq）。Eugene Yan 提供了互补数据：在事实一致性判断中，二元裁判对“一致”摘要的精确率超过 95%，但对“不一致”摘要的召回率只有约 30%–60%。这正是原始准确率会掩盖、而 TPR 会暴露的假阴性盲点（eugeneyan.com）。

**行动项：**
- 先对约 30–50 条真实轨迹做错误分析，命名失败类别，然后只为实际出现的每个类别编写一个二元裁判。
- 让每个裁判先给出思维链理由，再输出 PASS/FAIL（或 Y/N），并映射为 `{1.0, 0.0}`；取消所有 1–5 级量表。
- 请领域专家标注 100 个以上样本，并为每个样本写一行“为什么”的批评；选取其中 4–8 个作为少样本示例。
- 留出一份带标签测试集，分别报告 **TPR 与 TNR**（以及 FP/FN 数量），不要只报告总体一致率或单个准确率。
- 迭代裁判，直至 TPR 与 TNR 都达到阈值；若始终无法达到，应先更换裁判模型，而不是继续堆砌量规文字。
- 每当智能体、提示词或数据分布发生变化，都要重新验证裁判。

**常见陷阱：**
- 原始一致率会在类别不平衡时撒谎：若 90% 轨迹都通过，一个一律判 PASS 的裁判也有约 90% 的一致率，却一个失败都抓不到，此时 TPR 约为 0。
- 少样本示例对标签、顺序和数量都敏感（Eugene Yan / ChatGPT 事实不一致研究）；每次编辑后都要重新校准并测试 TPR/TNR，不能假定示例越多越好。
- 臃肿的系统提示词量规无法修复错位；具体、带标签的批评示例才可以。

**来源：**
- https://hamel.dev/blog/posts/evals-faq/
- https://eugeneyan.com/writing/llm-evaluators/
- https://github.com/openai/evals/blob/main/evals/registry/modelgraded/closedqa.yaml
- https://github.com/braintrustdata/autoevals
- https://github.com/prometheus-eval/prometheus-eval

---

### pass@k / pass^k 无偏估计量

**适用场景：** 若 k 次尝试中只需有一次成功即可（能力或 best-of-k，例如代码生成、检索，或任何有验证器/人类参与的任务），使用 **pass@k**；若要求 k 次独立试验**每一次**都成功（可靠性，例如必须始终如一地遵守政策的面向客户智能体），使用 **pass^k**。

**模式：** 绝不要把朴素公式 `1 - (1 - p)^k` 报告为 pass@k。当每道题只抽取 `n` 个样本，其中 `c` 个正确时，这个代入估计在 `n` 较小时会产生向上偏差。应使用无偏组合估计量 `pass@k = 1 - C(n-c, k) / C(n, k)`，并通过连乘以数值稳定的方式计算，切勿直接构造巨大的二项式系数。pass^k 回答的是相反问题：`P(k 次全部成功) = p^k`。因此，随着 k 增大，pass^k 下降，而 pass@k 上升。必须明确说明使用哪一种：当 p=0.75 时，pass@10 约为 1.0，而 pass^10 约为 0.056；它们会对同一个模型给出相反的故事。

**代码：** 以下 `estimate_pass_at_k` 逐字来自 OpenAI `human-eval` 的 `human_eval/evaluation.py`，另加一个简短的 pass^k 辅助函数（重构；human-eval 中没有 pass^k）。

```python
import itertools
from typing import List, Union
import numpy as np

def estimate_pass_at_k(
    num_samples: Union[int, List[int], np.ndarray],
    num_correct: Union[List[int], np.ndarray],
    k: int
) -> np.ndarray:
    """估计每道题的 pass@k，并以数组返回。"""

    def estimator(n: int, c: int, k: int) -> float:
        """计算 1 - comb(n - c, k) / comb(n, k)。"""
        if n - c < k:
            return 1.0
        # 数值稳定：使用望远镜式连乘，避免巨大的二项式系数。
        return 1.0 - np.prod(1.0 - k / np.arange(n - c + 1, n + 1))

    if isinstance(num_samples, int):
        num_samples_it = itertools.repeat(num_samples, len(num_correct))
    else:
        assert len(num_samples) == len(num_correct)
        num_samples_it = iter(num_samples)

    return np.array([estimator(int(n), int(c), k)
                     for n, c in zip(num_samples_it, num_correct)])

# pass^k：k 次独立试验全部成功的概率。p 是单次试验成功率（如 c/n）。
# 这是可靠性指标，会随 k 增大而下降。
def estimate_pass_power_k(num_samples, num_correct, k: int) -> np.ndarray:
    p = np.asarray(num_correct, dtype=float) / np.asarray(num_samples, dtype=float)
    return p ** k   # 重构；对应 tau-bench 对 pass^k 的定义

# 用法：对所有题目的结果取均值。
# pass_at_k  = estimate_pass_at_k(200, num_correct_per_problem, k=10).mean()
# pass_pow_k = estimate_pass_power_k(200, num_correct_per_problem, k=10).mean()
```

连乘形式之所以稳定，是因为 `C(n-c,k)/C(n,k)` 可约简为 `∏_{i=n-c+1}^{n} (1 - k/i)`。这样只需相乘一组接近 1.0 的小浮点数，而不必让两个天文数字般的二项式系数相除，从而避免溢出或精度损失。`if n - c < k: return 1.0` 这一保护分支处理“不可能抽到 k 个全部错误样本”的情况。

**完整示例：** τ-bench / τ²-bench（Sierra，已被 Anthropic 模型卡采用）把 pass^k 作为智能体可靠性的核心指标。在航班预订任务中，智能体必须完成预订，测试框架会在回合结束后通过**比较数据库状态差异**验证成功与否，即确认正确预订是否真正写入，而不是给聊天记录打分。一个单次成功率为 75% 的模型，其 pass@k 看起来非常出色，但 pass^k 会迅速坍塌：k=3 时约 42%，k=10 时约 5.6%。这正是面向客户的服务智能体所关心的“是否每一次都做对”，也是为何“成功一次就算数”的 pass@k 不适合作为上线指标（来源：τ-bench / Sierra；Anthropic《Demystifying evals for AI agents》）。

**行动项：**
- 运行前按指标决定：能力问题用 pass@k，可靠性问题用 pass^k；把选择写入评测配置，防止口径漂移。
- 每道题抽取 `n >> k` 个样本（human-eval 对 k∈{1,10,100} 使用 n=200），使无偏估计有足够信号；报告数值时同时给出 `n` 和 `k`。
- 使用连乘形式的 `estimate_pass_at_k`；不要把 `1 - (1-p)^k` 当作 pass@k，删除测试框架中的此类代码。
- 对每道题得到的数组求均值（`.mean()`），得到数据集指标；不要把所有样本混在一起算一个总比率。
- 对智能体可靠性，按**最终环境状态**评分（数据库差异或 SQL 状态检查），而非聊天文本，再将通过/失败次数送入 pass^k。
- 做单调性检查：k 增大时，pass@k 必须不降，pass^k 必须不升；否则估计器有误。

**常见陷阱：**
- 朴素的 `1 - (1-p)^k` 在 `n` 较小时会向上偏置，悄悄抬高 pass@k 得分。
- pass@k 与 pass^k 会随 k 向相反方向变化；只说“pass@k”而不说明具体含义，或把一方的 pass@10 与另一方的 pass^10 对比，毫无意义。
- 两个公式都假设 k 次试验彼此**独立**；若采样相关（相同随机种子、缓存前缀、temperature=0），两种估计都不成立。

**来源：**
- https://github.com/openai/human-eval/blob/master/human_eval/evaluation.py
- https://leehanchung.github.io/blogs/2025/09/08/pass-at-k/
- https://www.anthropic.com/engineering/demystifying-evals-for-ai-agents
- https://www.philschmid.de/agents-pass-at-k-pass-power-k
- https://sierra.ai/blog/tau-bench-shaping-development-evaluation-agents

---

### 面向 LLM 输出的代码断言与单元测试

**适用场景：** 当错误分析发现某些失败模式只需几行确定性代码即可捕获时使用，例如邮件实际未发送、`[placeholders]` 未替换、JSON 格式错误、数值越界或数据库状态损坏。应优先于 LLM 裁判采用：它们几乎不需要编写和运行成本，也不存在评分器漂移。

**模式：** 将错误分析中观察到的每一种失败都转化为廉价、确定性的断言，在 CI 中对每条轨迹执行。Hamel 给出的成本层级是：简单断言和基于参考答案的检查便宜且易维护，而 LLM 裁判需要 100 个以上标注样本、每周维护和跨角色协作。因此，“只为将反复迭代的问题构建昂贵评测器”，对于某些错误“甚至不需要 LLM 裁判，可改用代码断言”。保持二元通过/失败，因为二元决策比复杂量表更清晰。先修复明显缺口，再用断言确保它们不会复发。

**代码：** 以下是由已观察失败模式推导出的普通 `pytest` 断言。代码按 Hamel 的指导重构，并非逐字复制；每项检查都对应一种有记录的失败模式。

```python
import json, re, pytest

PLACEHOLDER = re.compile(r"\[(?:name|first_name|property|address|date|tour_time)\]", re.I)

def test_no_unsubstituted_placeholders(agent_output):
    # ReChat/NurtureBoss 式检查：模板变量必须在发送前填充。
    assert not PLACEHOLDER.search(agent_output), f"Leaked placeholder: {agent_output!r}"

def test_email_was_actually_sent(trace):
    # 失败模式：智能体声称“已发送邮件”，但从未调用发送工具。
    if "i've sent" in trace.final_response.lower():
        assert any(c.tool == "send_email" and c.status == "ok" for c in trace.tool_calls)

def test_output_is_valid_json_schema(agent_output):
    data = json.loads(agent_output)              # JSON 格式错误时抛出异常
    assert {"latitude", "longitude"} <= data.keys()
    assert -90 <= data["latitude"] <= 90
    assert -180 <= data["longitude"] <= 180

def test_appointment_persisted_to_db(trace, db):
    # tau-bench 式数据库差异检查：验证现实状态已改变，而非只看聊天文本。
    if trace.intent == "book_tour":
        assert db.appointments.exists(user=trace.user_id, status="confirmed")
```

同样的检查可以在 **promptfoo** 的 `promptfooconfig.yaml` 中声明，并在 CI 中运行 `promptfoo eval`：

```yaml
tests:
  - vars: { query: "Schedule a tour two weeks from now" }
    assert:
      - type: not-contains
        value: "["                 # 廉价的占位符保护
      - type: is-json
        value:
          type: object
          required: [appointment_date, status]
      - type: python
        value: file://assert_date.py   # def get_assert(output, context): -> bool|float|dict
```

promptfoo 的 `python` 断言可返回布尔值、浮点数或 `GradingResult` 字典，例如 `return {'pass': True, 'score': 0.6, 'reason': 'Looks good to me'}`。在 **deepeval** 中，对应形式是原生 `pytest` 测试，并通过 `deepeval test run test_file.py` 运行：

```python
from deepeval import assert_test
from deepeval.test_case import LLMTestCase
# 将确定性指标（正则/JSON/自定义 BaseMetric）与 assert_test(...) 组合使用。
```

**完整示例：** Hamel 实战指南中的 NurtureBoss 是一款公寓租赁聊天机器人。对会话轨迹数据表做错误分析后发现，助手“在日期处理中表现糟糕——当用户说‘我们安排在两周后看房吧’时，有 66% 的情况处理失败”。仅三类问题就占全部问题的 60% 以上：对话流程、转交人工以及改期/日期处理。团队编写了针对性测试，日期处理成功率由 33% 提升到 95%。这一修复可固化为确定性断言：解析智能体得到的相对日期，检查其是否等于预期日历日期，无需 LLM 裁判（来源：hamel.dev field guide）。

**行动项：**
- 先对真实轨迹做错误分析，对失败聚类，并逐项标注二元通过/失败。
- 对每个类别都问：“正则表达式、JSON 解析或数据库查询能否捕获？”若可以，就写断言，而非 LLM 裁判。
- 断言应针对**现实状态**，不只针对文本：`send_email` 是否真的调用？数据库行是否写入？检查轨迹/工具调用和数据库差异。
- 将占位符泄漏防护（如 `[name]`、`[property]`）和 JSON Schema/格式验证设为常驻检查。
- 把 `pytest`、`promptfoo eval` 或 `deepeval test run` 接入 CI，使套件在每个 PR 上运行。
- 仅把经过这些廉价检查后仍残留的主观失败交给 LLM 裁判。

**常见陷阱：**
- 不做错误分析就臆造假设性断言；断言应针对真正观察到的失败。
- 聊天文本声称“完成”不等于副作用真实发生；应断言底层状态。
- 过于严格的字符串或正则匹配会造成误报；将范围限制在特定缺陷，并尽量采用结构性或语义检查。

**来源：**
- https://hamel.dev/blog/posts/field-guide/ （NurtureBoss 错误分析、33%→95%、三类失败，以及“可能根本不需要 LLM 裁判，可使用代码断言”）
- https://hamel.dev/blog/posts/evals-faq/ （成本层级，以及“只为会反复迭代的问题构建昂贵评测器”）
- https://hamel.dev/blog/posts/llm-judge/ （二元通过/失败，以及断言在评测工具箱中的作用）
- https://www.promptfoo.dev/docs/configuration/expected-outputs/deterministic/ （contains、regex、is-json、python 等确定性断言类型）
- https://www.promptfoo.dev/docs/configuration/expected-outputs/python/ （`get_assert` 签名和 `GradingResult` 返回值）
- https://deepeval.com/docs/getting-started （`assert_test`、`LLMTestCase`、`deepeval test run`）

---

### 合成测试数据与评测集生成

**适用场景：** 当新智能体或新功能尚无生产流量，或流量太少，无法构建评测集，而上线前又需要覆盖每个功能、工具和边界情况的真实、多样输入时使用。

**模式：** 不要等待用户，让 LLM 扮演用户。首先定义变化**维度**，例如“功能/工具 × 场景 × 用户画像”；对维度取笛卡尔积得到结构化元组，再让 LLM 将每个元组变成一条真实自然语言查询。必须**每次只生成一条**，不要一次要求“给我 50 条”，以避免模式坍塌。每条查询还应以真实系统状态为依据，如实际房源 ID、真实数据库行、真实工具 Schema，确保输入确实会触发预期场景。随后为每个场景绑定断言检查，例如“无匹配”场景使用 `len(results)==0`。对 RAG 而言，ARES 采用反向思路：从真实文档抽样，为每篇文档生成一个可由其回答的问题，从而得到“查询—文档—答案”三元组，用于训练裁判并进行统计校准。

**代码：** 以下根据 Hamel Husain 的“维度”框架重构。实战指南给出了维度列表和 `generate_search_query` 形式；下面的循环与真实状态落地实现并非逐字复制，但忠实于其方法。

```python
import itertools, json, random
from openai import OpenAI
client = OpenAI()

# 1. 定义维度：真实用户行为的变化轴。
features  = ["property_search", "market_analysis", "scheduling", "follow_up"]
scenarios = ["exact_match", "multiple_matches", "no_matches", "invalid_criteria"]
personas  = ["first_time_buyer", "investor", "luxury_client", "relocating_family"]

# 2. 使用真实系统状态支撑生成，避免虚构且无法触发的输入。
real_listings = load_listings_from_db()   # 实际 ID、价格和街区

def generate_query(feature, scenario, persona, listings):
    # 每次调用只生成一条查询，以获得多样性并避免批量重复/模式坍塌。
    prompt = f"""You are a {persona.replace('_',' ')} using a real-estate assistant.
Write ONE realistic message that exercises the '{feature}' feature in the
'{scenario}' case. Ground it in this real inventory so it is actually triggerable:
{json.dumps(random.sample(listings, 5))}
For 'no_matches', craft criteria that truly match NOTHING in the inventory.
Return only the user's message."""
    r = client.chat.completions.create(
        model="gpt-4o", temperature=0.9,
        messages=[{"role": "user", "content": prompt}])
    return r.choices[0].message.content.strip()

# 3. 对维度取笛卡尔积，得到结构化、均匀分布的覆盖。
dataset = []
for feature, scenario, persona in itertools.product(features, scenarios, personas):
    q = generate_query(feature, scenario, persona, real_listings)
    dataset.append({"feature": feature, "scenario": scenario,
                    "persona": persona, "query": q})

# 4. 为每个场景绑定程序化断言，依据状态而非主观感受。
ASSERTIONS = {
    "exact_match":      lambda res: len(res) == 1,
    "multiple_matches": lambda res: len(res) > 1,
    "no_matches":       lambda res: len(res) == 0,
}
# 在信任合成输入前，先验证它确实触发目标场景。
for row in dataset:
    res = run_agent(row["query"])
    check = ASSERTIONS.get(row["scenario"])
    row["valid_trigger"] = check(res) if check else None  # 丢弃未触发的行
```

**完整示例：** *NurtureBoss*（Hamel Husain 介绍的公寓行业 AI 助手）在没有评测集时，生成了房地产经纪人可能交给助手的合成指令，例如“为 John Smith 创建联系人（johndoe@apple.com），电话 123-456-7890”，并将操作配对为可断言检查的序列：先创建，再询问“John Smith 的邮箱是什么？”房源查找器采用绑定场景的断言：唯一匹配为 `len(listing_array)==1`，多个匹配为 `>1`，无匹配为 `==0`。Hamel 的关键观点是：“无需等待生产数据……你可以对用户如何使用产品作出有依据的猜测，并生成合成数据。”对 RAG，*ARES* 采取反向方法：从语料库抽取约 6,189 篇文档，使用少样本提示生成合成问答对（`synthetic_queries_1.tsv`），在其上训练相关性/忠实度分类器，再借助小规模人类标注集上的 Prediction-Powered Inference（PPI）给出具有统计置信度的分数。*EvalGen*（Shankar 等，《Who Validates the Validators》）揭示了边界：它自动生成候选断言和裁判提示词，但仍要求人类为样本评分，因为存在**标准漂移**——“用户需要标准才能给输出评分，但给输出评分又会帮助用户定义标准。”

**行动项：**
- 列出智能体的功能/工具，再为每项枚举场景（正常路径、空结果、歧义、无效、多步）和 3–4 种用户画像，形成维度。
- 对维度取笛卡尔积，并以约 0.8–1.0 的 temperature **每个元组只生成一条查询**；绝不要在一次调用中要求“生成 50 条”。
- 每个提示词都应落地到真实系统状态（真实 ID、数据行、Schema），确保输入能够实际触发，而不是虚构。
- 为每种场景附加程序化断言，并**运行合成输入**确认目标场景确实触发；未触发者应丢弃。
- 每个功能先建立数十至约 100 个落地样例；对失败做错误分析，即“开放编码笔记 → 聚类为失败分类体系”，再扩大数据集。
- 有了信号后，可复用同一生成管线整理微调数据。

**常见陷阱：**
- **模式坍塌/分布不真实：** 一次“生成 N 条”的提示会产生近似重复内容，其分布也不像真实用户；应沿明确维度逐条生成。
- **输入未落地：** 引用虚假 ID 或不可能条件的查询，可能从未真正触发目标场景却仍穿过智能体；必须验证断言确实触发。
- **信任未验证裁判：** 合成数据可训练 LLM 裁判，但按 EvalGen 的结论，必须使用人类评分样本进行对齐；标准漂移意味着，在查看真实输出前无法定义出好标准。

**来源：**
- https://hamel.dev/blog/posts/field-guide/
- https://hamel.dev/blog/posts/evals/
- https://github.com/stanford-futuredata/ARES
- https://arxiv.org/abs/2404.12272

---

### 抗污染评测设计

**适用场景：** 当基准来自 GitHub、LeetCode、Web 文本等公开数据，可能已经进入模型预训练集，而你需要让分数反映推理能力而非记忆时使用。对于比较不同训练截止日期模型的排行榜，这一点不可或缺。

**模式：** 为每个任务记录可验证的**发布/创建日期**，再只用晚于该模型训练截止日期的任务评分，即“沿时间滚动”。持续追加新任务，使基准保持活力（LiveBench 每月刷新，SWE-rebench 运行自动 GitHub 挖掘管线），让记忆赶不上更新速度。使用单元测试、精确匹配答案等客观标准答案评分，避免 LLM 裁判干扰新鲜度信号。若静态基准无法重新按日期划分，可构建 GSM1k 风格的匹配留出集：编写难度与答案统计相同的新题，测量准确率下降，以量化污染。

**代码：** 以下代码忠实重构自 LiveCodeBench 的“沿时间滚动”和 SWE-rebench 的日期过滤方法。核心是针对每个模型的截止日期进行筛选。

```python
from datetime import date

# 每个任务都带有可验证时间戳：竞赛发布日期，或 SWE 任务的 GitHub
# issue/PR 创建日期。LiveCodeBench 为每道题标记 contest release_date；
# SWE-rebench 跟踪 issue 创建和 PR 合并日期。
MODEL_CUTOFFS = {
    "gpt-4o":        date(2023, 11, 1),   # 厂商声明的截止日期
    "deepseek-v3":   date(2024, 7, 1),
    "gpt-4.1":       date(2024, 6, 1),
}

def uncontaminated(tasks, model):
    """只保留严格晚于模型训练截止日期发布的任务。"""
    cutoff = MODEL_CUTOFFS[model]
    return [t for t in tasks if t["release_date"] > cutoff]

def windowed_passrate(tasks, model, run_one, lo=None, hi=None):
    """在截止日期后的 [lo, hi) 发布窗口上计算 Pass@1。
    对应 LiveCodeBench：在相同时间窗口比较模型，确保没有模型在训练中见过原题。"""
    pool = uncontaminated(tasks, model)
    if lo: pool = [t for t in pool if t["release_date"] >= lo]
    if hi: pool = [t for t in pool if t["release_date"] <  hi]
    if not pool:
        raise ValueError("empty window — collect fresher tasks")
    passed = sum(run_one(model, t) == t["expected"] for t in pool)
    return passed / len(pool), len(pool)

# 污染探针：同一模型、两个相邻窗口。
# 后一个窗口大幅下降，通常是记忆污染的警报。
early, _ = windowed_passrate(tasks, "gpt-4.1", run_one,
                             lo=date(2025,1,1), hi=date(2025,2,1))
late,  _ = windowed_passrate(tasks, "gpt-4.1", run_one,
                             lo=date(2025,3,1), hi=date(2025,5,1))
print(f"contamination signal (early-late drop): {early - late:+.3f}")
```

对于无法重新按日期划分的静态基准，可采用 GSM1k 匹配留出估计器：

```python
# 构建新的留出集，使难度和答案分布匹配；由人类编写，不使用 LLM/合成生成。
# 污染程度约等于准确率差距。
gap = acc(model, GSM8k) - acc(model, GSM1k)   # >约 5% 则怀疑过拟合/泄漏
```

**完整示例：** *LiveCodeBench* 中，DeepSeek 的 DS-Base-33B 在 2023 年 5 月发布的 LeetCode 题上 Pass@1 约为 60，但在其 2023 年 8 月数据窗口之后发布的 2023 年 9 月题目上，Pass@1 几乎跌至 0。这种近乎完全坍塌揭示的是记忆而非编码能力；GPT-4o 在其声明的 2023 年 11 月截止日期之后发布的 LeetCode 题上同样下降。*SWE-rebench* 将新鲜 GitHub 任务拆分为 2025 年 1 月和 2025 年 3–4 月窗口后，GPT-4.1 的解决率从 31.1% 降至 26.7%，而 DeepSeek-V3 基本持平；两者在更老的 SWE-bench Verified 上得分又发生偏离，显示静态基准存在污染信号。*GSM1k（Scale AI）* 是与 GSM8k 匹配、由人类编写的留出集，观察到最高约 13% 的准确率下降（Phi-3 约 10%）；模型生成 GSM8k 的概率与其 GSM8k→GSM1k 差距相关（Spearman r²≈0.36），而 Gemini/Claude 下降不到 5%。

**行动项：**
- 为每个任务附上可验证、不可变的 `release_date`，或 GitHub issue/PR 创建日期；不要信任文件名，应从源平台提取日期。
- 查询每个模型的训练截止日期，只在截止日期之后的任务上评分；比较模型时，将所有模型限制在同一日期窗口。
- 建立刷新管线，例如像 LiveBench 每月更新，或像 SWE-rebench 自动挖掘，使新模型发布后仍有非空的截止后任务池。
- 使用单元测试/精确匹配等客观标准答案评分，不使用 LLM 裁判，使新鲜度成为唯一改变指标的因素。
- 对每个模型运行“双窗口”探针，即较早与较晚的发布后窗口；较晚窗口得分大幅下降时发出警报。
- 对无法重新定日期的静态基准，构建小规模、由人类编写的匹配留出集，并将准确率差距报告为污染估计。

**常见陷阱：**
- 干净的日期过滤仍可能泄漏，例如刷新来源中的题目被转载到论坛并进入训练数据；优先选择新创建的题目，而不仅是新发现的题目。
- “匹配”留出集必须同时匹配难度和答案统计；更容易或更困难的留出集会令差距失去意义。GSM1k 使用人类解题率和步骤数量验证了这一点。
- 厂商声明的截止日期近似且有时过于乐观；应在声明日期之后留出安全间隔，不要把单个窗口当作定论。

**来源：**
- https://arxiv.org/abs/2403.07974 — LiveCodeBench（https://arxiv.org/html/2403.07974v2）
- https://github.com/LiveBench/LiveBench
- https://arxiv.org/abs/2505.20411 — SWE-rebench（https://arxiv.org/html/2505.20411）
- https://arxiv.org/html/2405.00332v1 — GSM1k（Scale AI）；https://github.com/scaleapi/gsm1k_eval
