[English](README.md) | **简体中文**

# TypeSafe Jev 的开源生态：调查与用例分类

调查日期：2026-09-27。分类所用模型：`jev-1.13.0`。范围：所有使用、集成、扩展、研究、编目或重新实现 Jev 的公开 GitHub 仓库，以 GitHub 搜索、GitHub 代码搜索和 14 个社区列表所能找到的为限。

> 本文是英文报告 [README.md](README.md) 的中文译本。两个版本内容不一致时，以英文版为准。

**术语对照**：TypeSafe 用例地图的五个类别和 Jev 的三种问题类型是官方名称，正文保留英文原名。

| 英文原名 | 中文含义 |
| --- | --- |
| AI Automation Software | AI 自动化软件 |
| Real-time applications | 实时应用 |
| AI Map Reduce over Big Data | 大数据上的 AI Map Reduce |
| Universal Verification | 通用验证 |
| Harness Engineering | Harness 工程 |
| Choice | 单选题：从一组命名选项中选一个 |
| Score | 评分题：在有序等级上打分 |
| Noul | 是非题：回答为“是”的概率 |

## 摘要

- Jev 公开亮相约两周（发布文章于 2026-09-15 登上 Hacker News），GitHub 上已有 **14,742 个与之相关的公开仓库**。其中 12,923 个创建于 2026 年 9 月，峰值出现在 2026-09-21，当天新增 1,783 个仓库。
- 官方 `typesafe-ai` 组织发布了 9 个仓库。其中 4 个是 Jev 工具（两个客户端库、一个 agent 技能，以及一个在其他大语言模型上运行同一接口的适配器）。其余 5 个是基础设施和分叉。`TypeSafeAI` 组织（8 个仓库，jev.works）是一个**非官方**的社区组织。
- 在全部 14,742 个仓库中，TypeSafe 用例地图里最常见的用例类别是 **AI Automation Software**（30.4%），其次是 **Harness Engineering**（25.3%）。按 star 加权，**Harness Engineering** 以占全部 star 的 52.1% 居首，因为大型 agent 框架在其 harness 内部使用 Jev。
- 最小的两个类别是 **Universal Verification**（6.4%）和 **AI Map Reduce over Big Data**（6.6%），尽管官方示例手册展示了 map-reduce 和验证类工作负载。
- 在 11,771 个应用、开发者工具和库中，占比最高的示例自动化用例是**游戏**（8.7%）、**搜索与检索**（7.6%）、**模型路由**（7.4%）和**大语言模型护栏**（6.4%）。48.0% 属于通用类，无法归入任何单一的行业用例。
- 在 GitHub 代码搜索返回的、star 数为 1,000 及以上的成熟开源项目中，有 101 个在默认分支上含有 Jev 集成代码，这一点通过阅读该代码核实。这是一个下限：第 12 节另列出 3 个已合并的集成，它们位于 star 数为 1,000 及以上、但代码搜索未返回的项目中（Langfuse、LanceDB 和 OpenInference）。在这 101 个项目中，有 94 个的公开代码调用 Jev，或直接调用，或代表其用户调用。其余 7 个则不然：Unsloth Studio 和 colibri 提供一个由本地模型应答的 Jev 兼容端点；PostHog 的功能调用的是 JevK5，这是 PostHog 自行托管的一个开放复刻，而其 TypeSafe 客户端在 master 分支上没有调用方；MCPJam Inspector 仅从其私有后端调用 Jev；Opik 和 Sentry 只追踪其用户发起的 Jev 调用；models.dev 只是在其模型目录中列出 Jev。真实集成的例子：LangChain、LiteLLM、DSPy、pydantic-ai、Vercel AI SDK、Pipecat、OpenClaw、goose 和 deer-flow。大多数需手动开启。
- 在对照独立基准检验的 10 个开放权重 "Jev replacements"（Jev 替代品）中，5 个的主要声明被推翻，3 个站得住脚（它们声称的内容很少），1 个仅有自报结果，1 个存在争议。10 个中有 9 个已有公开的独立准确率结果，除一个例外，Jev 在每一项中得分都更高（对于 Kev 和 openJev-verdict-2.0，外部人员只测量了较旧或较小的模型，而不是主打的 Kev-27B 或 Verdict 2.0）。这个例外是一个在某一基准的训练集划分上微调过的 Laya 检查点，它在该基准上击败了未经微调的 Jev，数据集卡片称这种比较不具可比性。一份未发布的 JevBench 草稿也把 JevK5 `v0.3` 排在 Jev 之上。NanoJev 没有独立比较。在社区的 Jev Decision Index（68 个开放参赛模型）上，没有模型得分高于 Jev。同时将成本和速度纳入权重的 JevBench 综合评分把三个开放模型排在 Jev 之上。
- Jev 根据每个仓库自身的 README、描述和主题标签对其分类。随后两名独立的盲评审阅者为全部 16,128 个仓库打标签，由第三名审阅者裁决三方分歧。Jev 与审阅者共识的一致率为：相关性 91.4%，项目类型 83.9%，用例 82.0%，顶层类别 69.6%。顶层类别之间存在重叠，这使类别成为最难的标签：两名审阅者彼此在这一标签上的一致率为 89.2%。

## 1. Jev 是什么

Jev 是 TypeSafe AI 的旗舰模型，也是 TypeSafe 所称 "System One" 的第一个模型。它不是聊天模型，也不生成成段的文字。一个请求携带一个 `state`（文本或 JSON）和一个类型化问题的映射，每个问题的答案都以类型化的值返回。同一请求中的所有问题都针对同一个 state 并行评估。

| 问题类型 | 所问内容 | 返回内容 |
| --- | --- | --- |
| Choice | 从一个命名集合中选出一个选项（最多 255 个选项） | 所选选项、每个选项的概率以及一个置信度值 |
| Score | 把 state 放到一个有序评分标准上（2 到 10 个等级） | 一个小数分数、每个等级的概率以及一个置信度值 |
| Noul | 一个是或否的问题 | 答案为“是”的概率 |

来自官方文档（docs.typesafe.ai，于 2026-09-27 阅读）的事实：

- 端点：`POST https://api.typesafe.ai/v1/systemone`，采用 bearer 密钥认证。当前模型是 `jev-1.13.0`。别名 `jev-latest` 和 `jev-preview` 都指向它。
- 价格：每百万输入 token 收费 $0.042。输出 token 免费。
- 限制：每秒 250,000 个 token，每分钟 1,200 个请求（文档称这些限制正在动态调整，在 TypeSafe 增加容量期间可能不经通知而变更）。单个请求上限 64,000 个 token，state 与最长问题合计上限 32,000 个 token。仅支持文本输入。
- 其他访问途径（来源见第 12 节）：Vercel AI Gateway（模型 `typesafe-ai/jev`）、OpenRouter（模型 `typesafe/jev-1.13`）、DigitalOcean Serverless Inference，以及 Cloudflare AI 模型目录，后者将 Jev 列为第三方模型。第 2.2 节中的分类没有使用其中任何一个；它直接调用了 TypeSafe 端点。
- TypeSafe 在 "Jev 1.13 jaggedness" 页面上列出的已知弱点：字面化理解、算术与计数、日期比较、间接指代、充满无关细节的大型 state、state 中的对抗性内容、相互矛盾的指令与标准、常识性的结构不变量（例如，一个 Noul 与其否定形式的 Noul 之和不一定为 1，在 Noul 上调好的阈值也不能沿用到 Choice 上），以及文本生成。

## 2. 方法

### 2.1 收集

| 步骤 | 仓库 |
| --- | ---: |
| 在 14 个社区列表和 9 次 GitHub 仓库搜索中找到 | 17,099 |
| 仅通过 GitHub 代码搜索 TypeSafe 端点和模型名称找到 | 497 |
| 去重后的候选 | 17,596 |
| 移除：创建于 2026 年 8 月之前，且仅因名称或描述中的单词 "jev" 而匹配，未提及 TypeSafe | 999 |
| 已获取元数据和 README | 16,597 |
| 移除：已删除、私有或已改名 | 19 |
| 移除：既无 README 也无描述 | 415 |
| 移除：同一仓库在旧名和新名下的重复项 | 35 |
| 由 Jev 分类 | 16,128 |

最初使用了一个关键词过滤器，以跳过从未提及 TypeSafe 的仓库。对被跳过仓库的随机样本所做的盲评（见下文池 G）发现，其中大多数与 Jev 相关，例如通过 OpenRouter 访问 Jev、或在不提及 TypeSafe 的情况下复制其接口的项目。因此放弃了该过滤器，每个有 README 或描述的仓库都进行了分类。

GitHub 搜索每个查询最多返回 1,000 个结果，因此每个查询对所有创建于 2026 年 9 月之前的仓库运行一次，并从 2026-09-01 到 2026-09-27 按创建日期每天运行一次。仍超过 1,000 个结果的窗口被继续拆分为更小的时间窗口，直到每一段都不超过上限，但有两个限制。对 "jev" 的名称搜索中 9 月之前的窗口（3,315 个结果）是有意不拆分的，因为这些仓库早于 Jev 的公开发布，大多与 Jev 无关，所以只读取了其前 1,000 个结果。对 "jev" 和 "typesafe" 的 README 搜索中 9 月之前的窗口（1,832 个结果）只从 2024-05-01 起拆分，这一日期略早于 TypeSafe 在 2024-05-28 创建其 GitHub 组织。这 14 个社区列表就是第 10 节中列出的 "awesome" 列表。

### 2.2 使用 Jev 分类

每个仓库对应一次对 `POST https://api.typesafe.ai/v1/systemone` 的请求，模型固定为 `jev-1.13.0`。state 包含仓库名称、其 GitHub 描述、主题标签、主要语言、主页以及 README（已清除图片和 HTML，最多 60,000 个字符）。对于 GitHub 代码搜索找到的 1,311 个已分类仓库，state 还包含调用 TypeSafe 应用程序接口的文件路径以及最相关文件的摘录，因为许多大型项目只在代码中提到 Jev。当 state 超过 token 上限时，会缩短 README 并重新发送请求。

每个请求一次提出 39 个问题：

- 1 个 Noul：该仓库究竟是否与 Jev 相关。
- 4 个 Choice：项目类型（8 个选项）、用例类别（用例地图的 5 个类别加上 "none of these"（以上都不是））、示例自动化用例（用例地图的 19 个用例加上 "general purpose or other"（通用或其他））以及决策形态（用例地图的 10 个任务类别）。
- 34 个 Noul，对应类别、用例和决策形态 Choice 中的每个具名选项：5 个类别、19 个用例和 10 个任务类别。两个兜底选项 "none of these" 和 "general purpose or other" 没有对应的 Noul。每个 Noul 是绝对判断，每个 Choice 是相对判断，因此两者配对为每个具名标签提供了第二个信号。

选项描述浓缩自用例地图 <https://docs.typesafe.ai/concepts/use-case-map>，其中一些描述的范围超出了用例地图。游戏也涵盖玩游戏或控制游戏的 agent：用例地图的游戏条目列出了玩家举报、聊天审核、挫败感与参与度评分以及流失信号，只在 Real-time applications 卡片中提到玩游戏。Real-time applications 也涵盖驾驶机器人或设备以及对语音作出反应，风险评估也涵盖交易与市场风险决策，路由决策形态也涵盖 agent 或游戏循环中的下一步动作。第 2.3 节中的审阅者使用了相同的描述，因此第 6.3 节中游戏和风险评估的占比包括了用例地图并未列在这些用例下的玩游戏 agent 和交易工具。指令要求 Jev 判断每个项目存在的目的是解决什么问题，而不是其 README 中的示例数据（许多快速入门教程用一张支持工单作为玩具示例，这在试点中曾把软件开发工具包拉向“客户支持”用例）。16,128 个请求共使用了 128,356,429 个输入 token，按标价计算费用为 $5.39。

### 2.3 Jev 标签的验证

两个相互独立的审阅者 agent 读取相同的仓库数据，并在看不到 Jev 答案的情况下，对每个仓库进行盲评标注。一个审阅者依据 README 的内容作判断。另一个审阅者被要求对营销文字保持质疑，并依据项目实际实现的功能作判断。对于每个字段，最终标签取 Jev 与两个审阅者的多数意见。当三者意见各不相同时，由第三个盲评审阅者在候选标签中作出选择。

| 池 | 选取方式 | 仓库数 |
| --- | --- | ---: |
| A | Jev 判定为相关、star 数不少于 20 且不在池 G、H、I 中的每个仓库，以及 Jev 判定为相关的每个官方或社区组织仓库 | 678 |
| B | 从 Jev 判定为相关且 star 数少于 20 的仓库中随机抽取的样本 | 300 |
| C | 从 Jev 判定为无关的仓库中随机抽取的样本 | 100 |
| D | Jev 判定为无关、star 数不少于 20 且不在池 C、G、H、I 中的其余仓库 | 90 |
| E | Jev 判定为无关的所有其余仓库 | 950 |
| F | Jev 判定为相关的所有其余仓库（长尾） | 12,255 |
| G | 从第一道关键词过滤器跳过的仓库中随机抽取的样本 | 104 |
| H | 第一道关键词过滤器跳过的所有其余仓库 | 1,570 |
| I | 第 7 节中不在池 A 中的大型集成（这 104 个中的另外 23 个在池 A 中）。这批大型集成共 104 个，它们是否相关以及最终类别都来自对其源代码的阅读 | 81 |
| 全部 | 所有已分类的仓库 | 16,128 |

按池统计的 Jev 与审阅者共识的一致率（共识指两个审阅者意见相同的情形）：

| 池 | 是否相关 | 项目类型 | 用例 | 顶层类别 | 两个审阅者之间在类别上的一致率 |
| --- | ---: | ---: | ---: | ---: | ---: |
| A | 95.0% | 83.5% | 84.7% | 65.4% | 87.5% |
| B | 98.3% | 88.8% | 80.6% | 73.3% | 86.0% |
| C | 43.6% | 51.1% | 83.3% | 72.1% | 86.0% |
| D | 35.6% | 48.1% | 89.0% | 54.8% | 81.1% |
| E | 36.1% | 56.3% | 77.8% | 64.6% | 86.7% |
| F | 98.2% | 88.7% | 81.8% | 69.5% | 89.7% |
| G | 81.4% | 68.8% | 78.6% | 73.6% | 83.7% |
| H | 74.2% | 65.7% | 85.5% | 75.8% | 89.0% |
| I | 75.0% | 64.5% | 77.3% | 55.7% | 86.4% |
| 全部 | 91.4% | 83.9% | 82.0% | 69.6% | 89.2% |

池 B 是长尾的无偏估计，因为它是从 Jev 判定为相关的仓库中随机抽取的样本。在池 B 中，Jev 在是否相关和项目类型上可靠，在用例上表现良好，在顶层类别上最弱。两个审阅者之间也最常在类别上产生分歧，因为五个类别相互重叠：一个工具调用闸门同时属于 Harness Engineering 和 Universal Verification，而一个游戏或交易机器人可以同时属于 AI Automation Software 和 Real-time applications。

池 C、D、E 显示了 Jev 相关性答案的主要弱点：它偏保守。许多仓库只是简短提到 Jev、复制其接口，或通过 OpenRouter 访问它，却得到了低于 0.5 的概率。审阅者判定其中大多数与 Jev 相关，因此 Jev 判定为无关的每个仓库都经过了审阅，本报告中的计数包含这些更正。

池 G 和 H 包含第一道关键词过滤器因其从未提及 TypeSafe 而跳过的仓库。审阅者判定其中许多与 Jev 相关（例如通过 OpenRouter 调用 Jev 或复制其接口的项目），因此弃用了该过滤器，并对这些仓库全部进行了分类和审阅。

池 I 包含第 7 节 104 个大型集成中的 81 个，另外 23 个在池 A 中。这批大型集成共 104 个，它们是否相关以及最终类别都来自对其源代码的阅读，而不是来自审阅者投票。这次阅读在其中 3 个里没有找到 Jev 集成代码，因此它们计为与 Jev 无关。其余 101 个集成的类型和用例取 Jev 与两个审阅者的多数意见，另有一处用例依据源代码作了更正（第 7 节）。这 3 个没有集成代码的项目，类型为 `unrelated`，用例为 `general_or_other`，与第 14 节一致。

### 2.4 集成、复刻与评测的研究和验证

- 一轮通用研究（网络搜索、阅读源代码，以及对每条声明进行三票对抗性检查）覆盖了官方组织、社区组织和定价。
- GitHub 代码搜索找到的引用 Jev 且 star 数不少于 1,000 的项目中，有 104 个（第 7 节列出了被排除的 6 个）各配有一名调查者和一名质疑审阅者：调查者阅读默认分支上的集成代码，质疑审阅者试图证明每个字段有误并加以更正。这 104 个中有 101 个含有 Jev 集成代码，其中 94 个的公开代码会调用 Jev。
- 10 个开放权重复刻各配有一名调查者和三名质疑审阅者，由他们对结论投票。
- 6 个评测来源（5 项第三方研究，以及 TypeSafe 员工也参与了讨论的 Hacker News 发布讨论帖）各配有一名阅读者和两名质疑审阅者，他们对照原文检查每一项发现。
- 随后，三名完整性审阅者搜寻遗漏的复刻、评测、集成和风险。每个新条目都经过调查，并由两名质疑审阅者检查。

## 3. 官方 `typesafe-ai` 组织

该组织（显示名称 "TypeSafe"，创建于 2024-05-28，网站为 typesafe.ai）在 docs.typesafe.ai 上有指向它的链接。GitHub 没有将其标记为已验证。

| 仓库 | star 数 | 创建时间 | 许可证 | 内容 | 最终类别标签 |
| --- | ---: | --- | --- | --- | --- |
| [`typesafe-ai/skills`](https://github.com/typesafe-ai/skills) | 2,277 | 2026-08-24 | MIT | 面向 Claude Code 及其他 agent 的 agent 技能：设计 TypeSafe 工作流，并查找最新的文档和示例手册 | 不属于五个类别 |
| [`typesafe-ai/system-one-adapter-python`](https://github.com/typesafe-ai/system-one-adapter-python) | 311 | 2026-08-08 | MIT | 具有相同 `system_one` 接口的 Python 适配器，由其他大语言模型而非 Jev 作答 | 不属于五个类别 |
| [`typesafe-ai/typesafe-sdk-js`](https://github.com/typesafe-ai/typesafe-sdk-js) | 247 | 2026-09-04 | MIT | 官方 JavaScript 和 TypeScript 客户端库（npm 上的 `@typesafe-ai/sdk`） | 不属于五个类别 |
| [`typesafe-ai/typesafe-sdk-python`](https://github.com/typesafe-ai/typesafe-sdk-python) | 237 | 2026-09-04 | MIT | 官方 Python 客户端库（PyPI 上的 `typesafe-sdk`） | 不属于五个类别 |
| [`typesafe-ai/daggerverse`](https://github.com/typesafe-ai/daggerverse) | 22 | 2026-04-17 | Apache-2.0 | 共享的 Dagger 持续集成模块（uv、GitHub、Twingate、pinact、zizmor、deptry）；不含 Jev 代码 | 与 Jev 无关 |
| [`typesafe-ai/LLaDA`](https://github.com/typesafe-ai/LLaDA) | 12 | 2025-07-13 | MIT | `ML-GSAI/LLaDA` 的分叉，这是一个扩散语言模型；用途未说明 | 未分类（未使用 Jev） |
| [`typesafe-ai/vllm`](https://github.com/typesafe-ai/vllm) | 3 | 2025-05-17 | Apache-2.0 | `vllm-project/vllm` 的分叉，这是一个模型服务引擎；用途未说明 | 未分类（未使用 Jev） |
| [`typesafe-ai/pulumi-clickhouse`](https://github.com/typesafe-ai/pulumi-clickhouse) | 3 | 2026-07-08 | Apache-2.0 | `pulumiverse/pulumi-clickhouse` 的分叉，这是一个基础设施提供方；用途未说明 | 未分类（未使用 Jev） |
| [`typesafe-ai/typesafe-ai.github.io`](https://github.com/typesafe-ai/typesafe-ai.github.io) | 2 | 2024-05-28 | 无 | 组织网站仓库；没有 README | 未分类（没有 README 或描述） |

这两个客户端库返回类型化的答案，其类型随问题而定。该 agent 技能教编码 agent 设计 Jev 工作流。适配器 `system-one-adapter-python` 用其他大语言模型（兼容 OpenAI 的提供方、Anthropic 提供方和 Gemini 提供方）回答相同的 `system_one` 接口，这有助于在成本、速度和质量上将 Jev 与大语言模型进行比较。

## 4. 非官方的 `TypeSafeAI` 社区组织

`github.com/TypeSafeAI`（"TypeSafe Community"，网站为 jev.works）创建于 2026-09-18。它的 4 个仓库比该组织更早：它们于 2026-09-16 和 2026-09-17 在个人账户 @BunsDev 下创建，之后才移入，因此“创建时间”列显示的是每个仓库的创建时间，而不是其加入组织的时间。其组织简介写着 "UNOFFICIAL COMMUNITY GITHUB — NOT THE OFFICIAL TYPESAFE AI TEAM"，并指明其创建者为 @BunsDev，称其为 "VC Moderator"。其 8 个仓库中有 7 个的 README 文件重申自己是独立社区作品。`modex` 的 README 没有这样的声明；只有组织简介将 `modex` 描述为社区项目。其名称与官方组织仅相差一个连字符，因此读者可能会混淆两者。

| 仓库 | star 数 | 创建时间 | 内容 | 最终类别标签 |
| --- | ---: | --- | --- | --- |
| [`TypeSafeAI/typesafe-playground`](https://github.com/TypeSafeAI/typesafe-playground) | 21 | 2026-09-16 | Next.js 演练场，含 22 个包共 110 个示例；其中 10 个包是游戏、两难问题和模型挑战 | AI Automation Software |
| [`TypeSafeAI/jev-harness`](https://github.com/TypeSafeAI/jev-harness) | 20 | 2026-09-22 | 处于研究阶段的审查闸门：由大语言模型提出一个动作，Jev 回答四个是非问题，由代码作出决定 | Harness Engineering |
| [`TypeSafeAI/typesafe-ui`](https://github.com/TypeSafeAI/typesafe-ui) | 6 | 2026-09-17 | 用于 Jev 界面的 shadcn 风格 React 组件；是私有工作区包，未发布 | 不属于五个类别 |
| [`TypeSafeAI/typesafe-router`](https://github.com/TypeSafeAI/typesafe-router) | 4 | 2026-09-17 | TypeScript 库，用一个 Choice 问题和确定性的回退机制从固定集合中选出一个工具或模型 | Harness Engineering |
| [`TypeSafeAI/clarity-judge`](https://github.com/TypeSafeAI/clarity-judge) | 3 | 2026-09-16 | 写作检查器，包含多项独立命名的检查（含糊措辞、冗余填充词、语气、被动语态），每项检查都有各自的判定 | AI Automation Software |
| [`TypeSafeAI/modex`](https://github.com/TypeSafeAI/modex) | 2 | 2026-09-25 | 驱动 Claude Code 和 Codex 的桌面应用；其 "Auto" 模式使用 Jev 为每一轮选择模型和推理强度 | Harness Engineering |
| [`TypeSafeAI/community-blog`](https://github.com/TypeSafeAI/community-blog) | 1 | 2026-09-18 | 包含简短社区入门笔记的静态站点 | 未分类（未使用 Jev） |
| [`TypeSafeAI/.github`](https://github.com/TypeSafeAI/.github) | 0 | 2026-09-19 | 组织简介和贡献指南 | 未分类（未使用 Jev） |

`jev-harness` 的 README 中的基准结果（不使用 Jev 时 25 个不良提议中拦截了 7 个，使用 Jev 时 25 个中拦截了 25 个）来自合成测试数据上的脚本化模拟值，而不是对 Jev 的实测。该项目自己也说明了这一点。

## 5. 规模与增长

2026 年 9 月按创建日统计的每日新增与 Jev 相关的仓库数：

| 日期 | 新增仓库 |
| --- | ---: |
| 2026-09-01 | 21 |
| 2026-09-02 | 28 |
| 2026-09-03 | 25 |
| 2026-09-04 | 27 |
| 2026-09-05 | 28 |
| 2026-09-06 | 23 |
| 2026-09-07 | 26 |
| 2026-09-08 | 24 |
| 2026-09-09 | 27 |
| 2026-09-10 | 31 |
| 2026-09-11 | 36 |
| 2026-09-12 | 30 |
| 2026-09-13 | 33 |
| 2026-09-14 | 34 |
| 2026-09-15 | 43 |
| 2026-09-16 | 178 |
| 2026-09-17 | 694 |
| 2026-09-18 | 1,071 |
| 2026-09-19 | 1,413 |
| 2026-09-20 | 1,584 |
| 2026-09-21 | 1,783 |
| 2026-09-22 | 1,513 |
| 2026-09-23 | 1,289 |
| 2026-09-24 | 904 |
| 2026-09-25 | 744 |
| 2026-09-26 | 776 |
| 2026-09-27 | 538 |

有 1,819 个相关仓库创建于 2026 年 9 月之前。其中包括后来加入 Jev 集成的成熟项目（第 7 节），以及被重新用于 Jev 工作的较早仓库。

| star 数 | 仓库数 |
| --- | ---: |
| 1,000 或以上 | 130 |
| 100 至 999 | 255 |
| 20 至 99 | 406 |
| 5 至 19 | 831 |
| 1 至 4 | 3,451 |
| 无 | 9,669 |

65.6% 的相关仓库没有 star。这个生态是一条由小型实验构成的长尾，围绕着少数几个大型项目。

| 主要语言 | 仓库数 |
| --- | ---: |
| Python | 5,620 |
| TypeScript | 3,987 |
| JavaScript | 1,920 |
| 无主要语言 | 636 |
| HTML | 569 |
| Rust | 475 |
| Go | 419 |
| Swift | 153 |
| Java | 115 |
| C# | 114 |

## 6. 用例分类

本节所有数字均使用最终标签：全部 16,128 个仓库采用 Jev 与两名盲评审阅者的多数结果，三方意见互不相同时再加入第三名盲评审阅者，104 个大型集成的相关性和类别则采用源代码核验的结果。第 2.3 节给出了每个标签的一致程度。

### 6.1 项目类型

| 类型 | 仓库数 | 占比 | 占全部 star 的比例 |
| --- | ---: | ---: | ---: |
| 应用 | 6,169 | 41.8% | 33.8% |
| 开发者工具 | 4,305 | 29.2% | 23.6% |
| 库或集成 | 1,297 | 8.8% | 29.1% |
| 基准或评测 | 1,215 | 8.2% | 0.1% |
| 替代或复刻模型 | 1,136 | 7.7% | 8.2% |
| 目录或指南 | 611 | 4.1% | 5.2% |
| 账户工具 | 9 | 0.1% | 低于 0.1% |

### 6.2 用例地图类别

| 类别 | 仓库数 | 占比 | 占全部 star 的比例 |
| --- | ---: | ---: | ---: |
| AI Automation Software | 4,476 | 30.4% | 20.8% |
| Harness Engineering | 3,733 | 25.3% | 52.1% |
| Real-time applications | 2,447 | 16.6% | 4.3% |
| 不属于五个类别 | 2,173 | 14.7% | 9.8% |
| AI Map Reduce over Big Data | 966 | 6.6% | 6.3% |
| Universal Verification | 947 | 6.4% | 6.7% |

一个仓库常常符合不止一个类别。对于 Noul 问题 "does the core purpose fit this category"，Jev 回答为是（概率 0.5 或以上）的相关仓库所占比例：

| 类别 | 占相关仓库的比例 |
| --- | ---: |
| AI Automation Software | 68.8% |
| Harness Engineering | 39.3% |
| Real-time applications | 14.3% |
| Universal Verification | 8.4% |
| AI Map Reduce over Big Data | 3.0% |

按 star 档位划分的主要类别：

| star 数 | AI Automation Software | Harness Engineering | Real-time applications | Universal Verification | AI Map Reduce over Big Data | 不属于五个类别 |
| --- | ---: | ---: | ---: | ---: | ---: | ---: |
| 1,000 或以上 | 24.6% | 36.9% | 6.9% | 11.5% | 5.4% | 14.6% |
| 100 至 999 | 19.2% | 32.5% | 14.1% | 3.9% | 5.9% | 24.3% |
| 20 至 99 | 21.9% | 31.5% | 14.5% | 5.2% | 7.4% | 19.5% |
| 5 至 19 | 23.9% | 33.1% | 14.0% | 6.0% | 5.9% | 17.1% |
| 1 至 4 | 27.0% | 29.4% | 16.3% | 6.0% | 5.9% | 15.4% |
| 无 | 32.8% | 22.6% | 17.2% | 6.7% | 6.8% | 13.8% |

### 6.3 示例自动化用例

在 11,771 个应用、开发者工具和库中：

| 用例 | 仓库数 | 占比 |
| --- | ---: | ---: |
| 通用或其他 | 5,651 | 48.0% |
| 游戏 | 1,021 | 8.7% |
| 搜索与检索 | 890 | 7.6% |
| 模型路由 | 874 | 7.4% |
| 大语言模型护栏 | 759 | 6.4% |
| 语义代码检查 | 616 | 5.2% |
| 风险评估 | 538 | 4.6% |
| 内容审核与信任安全 | 353 | 3.0% |
| 客户支持 | 272 | 2.3% |
| 招聘 | 165 | 1.4% |
| 科学发现 | 124 | 1.1% |
| 电商平台 | 97 | 0.8% |
| 潜在客户开发 | 95 | 0.8% |
| 法律与合规 | 94 | 0.8% |
| 图与知识图谱 | 75 | 0.6% |
| 广告 | 74 | 0.6% |
| 金融犯罪 | 31 | 0.3% |
| 预测建模的特征提取 | 19 | 0.2% |
| 保险理赔 | 13 | 0.1% |
| 需求预测 | 10 | 0.1% |

### 6.4 决策形态

仅为 Jev 给出的标签（审阅者没有标注决策形态）：

| 决策形态 | 仓库数 | 占比 |
| --- | ---: | ---: |
| 路由 | 5,238 | 35.5% |
| 分类 | 4,865 | 33.0% |
| 检测 | 1,232 | 8.4% |
| 评分 | 1,166 | 7.9% |
| 验证 | 1,110 | 7.5% |
| 排序 | 593 | 4.0% |
| 检索 | 188 | 1.3% |
| 结构化数据提取 | 163 | 1.1% |
| 搜索 | 162 | 1.1% |
| 机器学习特征提取 | 25 | 0.2% |

### 6.5 生态集中在哪里

按数量计，生态集中在两个类别。AI Automation Software（30.4%）主要是应用和机器人程序，其中由普通代码掌控控制流，Jev 在每一步回答一个范围很窄的问题。Harness Engineering（25.3%）主要是开发者工具：编码 agent 插件、Model Context Protocol 服务器、路由器和工具调用闸门。Real-time applications（16.6%）以游戏为首（该类别中 43% 具有游戏用例），并且一项整词关键词检查在其 60% 的仓库中找到了 game、gaming、gameplay、chess、poker、Doom、Mario、Pokémon、Minecraft、browser、desktop、voice、speech、robot、robotics、drone、MuJoCo 或 smart home 中的至少一个词。

按 star 计，格局进一步偏向 Harness Engineering（占全部 star 的 52.1%）。采用 Jev 的大型项目是 agent 框架和网关，它们用 Jev 来路由模型、把关工具调用、压缩上下文以及对工具排序。在 star 数为 1,000 或以上的仓库中，36.9% 属于 Harness Engineering。

AI Map Reduce over Big Data（6.6%）和 Universal Verification（6.4%）是缺口。官方示例手册展示了批处理工作负载（对法律段落重排序、对专利做层级分类、在 450 个候选对上做实体对齐），但很少有开源项目在大型语料库上运行 Jev。对其他模型的验证主要出现在该类别的开发者工具（40.0%）和应用（31.2%）中；基准或评测项目只占 15.8%。该类别最大的具名用例是大语言模型护栏（41.0%）和语义代码检查（16.9%）；另有 26.0% 属于通用或其他，不符合任何单一用例。

在示例用例中，游戏是最大的领域（8.7%）。搜索与检索（7.6%）和模型路由（7.4%）是最大的行业中立用途，二者几乎持平，其后是大语言模型护栏（6.4%）、语义代码检查（5.2%）和风险评估（4.6%）。在风险评估中，一项整词关键词检查针对 trading、stock、cryptocurrency、market、finance、portfolio、investment 和 hedge fund 等词（包括其词形变化）、去中心化金融（关键词 `defi`）以及 Polymarket、Kalshi 和 Hyperliquid 这些名称，在 70% 的仓库中找到了它们，因此其中很大一部分是交易机器人和市场工具。用例地图中最小的三个行业领域是需求预测（10 个仓库）、保险理赔（13 个仓库）和金融犯罪（31 个仓库），因此这些行业目前几乎还没有开源项目。预测建模的特征提取是一种行业中立用途，规模同样很小（19 个仓库）。

### 6.6 各类别的知名项目

各类别中按 star 数计最大的项目。101 个大型框架集成在第 7 节中有单独的表格，目录类项目不列入。

#### AI Automation Software

| 仓库 | star 数 | 类型 | GitHub 描述（原文引用） |
| --- | ---: | --- | --- |
| [`OpenByteInc/QuantDinger`](https://github.com/OpenByteInc/QuantDinger) | 12,233 | 应用 | "Open-source AI Trading OS, agent trading, and vibe trading, with Jev System One integratio..." |
| [`ruvnet/RuVector`](https://github.com/ruvnet/RuVector) | 4,529 | 替代或复刻模型 | "RuVector provides High Performance, Real-Time decisions and agent memory , Self-Learning A..." |
| [`TheoLeeCJ/SemIf-OpenJev`](https://github.com/TheoLeeCJ/SemIf-OpenJev) | 4,446 | 替代或复刻模型 | "Semantic ifs from open models, on a 3090 at home. Independent; not affiliated with Jev or ..." |
| [`deepopen-com/deepopen`](https://github.com/deepopen-com/deepopen) | 1,044 | 替代或复刻模型 | （描述非英文） |
| [`awlevin/typesafe-computer-use`](https://github.com/awlevin/typesafe-computer-use) | 1,030 | 应用 | "Computer use for about $0.0002 a step: OCR the screen, classify the next action with TypeS..." |
| [`CelestoAI/celesto`](https://github.com/CelestoAI/celesto) | 984 | 开发者工具 | "Secure and persistent computer for AI agents" |
| [`SynaLinks/synalinks-skills`](https://github.com/SynaLinks/synalinks-skills) | 907 | 开发者工具 | "Coding Agents skills for Synalinks OSS" |
| [`duriantaco/skylos`](https://github.com/duriantaco/skylos) | 838 | 开发者工具 | "Open source local-first PR scanner that finds dead code, security bugs, secrets, quality r..." |
| [`vercel-labs/ai-cli`](https://github.com/vercel-labs/ai-cli) | 817 | 开发者工具 | "Generate anything from your terminal" |
| [`TypeLLM/TypeLLM`](https://github.com/TypeLLM/TypeLLM) | 801 | 替代或复刻模型 | "TypeLLM: LLMs with type-safe generation" |

#### Harness Engineering

| 仓库 | star 数 | 类型 | GitHub 描述（原文引用） |
| --- | ---: | --- | --- |
| [`tamaratran/fast-jev-compaction`](https://github.com/tamaratran/fast-jev-compaction) | 7,015 | 开发者工具 | "Claude Code plugin that replaces the compaction summary with Jev decisions: every tool cal..." |
| [`zilliztech/memsearch`](https://github.com/zilliztech/memsearch) | 2,664 | 开发者工具 | "A persistent, unified memory layer for all your AI agents (e.g. Claude Code, Codex, DSH), ..." |
| [`Contrastive-LM/CLM`](https://github.com/Contrastive-LM/CLM) | 1,871 | 替代或复刻模型 | （无描述） |
| [`autonomous-ai/openharness`](https://github.com/autonomous-ai/openharness) | 985 | 开发者工具 | "The ultimate harness for coding agents and beyond. All your agents. All your machines. One..." |
| [`Raudaschl/rag-fusion`](https://github.com/Raudaschl/rag-fusion) | 958 | 基准或评测 | "RAG-Fusion: multi-query generation + Reciprocal Rank Fusion for better retrieval-augmented..." |
| [`kerpopule/hermes-jev-skills`](https://github.com/kerpopule/hermes-jev-skills) | 865 | 开发者工具 | "Jev-powered model routing, memory, compaction, skill selection, computer and browser use f..." |
| [`bastani-inc/atomic`](https://github.com/bastani-inc/atomic) | 834 | 开发者工具 | "The verifiable coding agent runtime. Define your coding agent's process in natural languag..." |
| [`kitfunso/hippo-memory`](https://github.com/kitfunso/hippo-memory) | 764 | 开发者工具 | "Biologically-inspired memory for AI agents. Decay, retrieval strengthening, consolidation...." |
| [`samuelfaj/distill`](https://github.com/samuelfaj/distill) | 692 | 开发者工具 | "Get FAR MORE done with FAR FEWER tokens 🔥" |
| [`dzhng/jevgrep`](https://github.com/dzhng/jevgrep) | 677 | 开发者工具 | "Find code by asking what it does. A CLI for coding agents that uses Jev to discover releva..." |

#### Real-time applications

| 仓库 | star 数 | 类型 | GitHub 描述（原文引用） |
| --- | ---: | --- | --- |
| [`browser-use/jev-ultrafast`](https://github.com/browser-use/jev-ultrafast) | 20,786 | 应用 | "Fastest and cheapest web agent" |
| [`mizorewww/laya-mlx`](https://github.com/mizorewww/laya-mlx) | 6,472 | 替代或复刻模型 | "Native MLX runtime for Laya typed decision models — 7–14 ms short decisions on M3 Max. No ..." |
| [`jarrodwatts/jev-trader`](https://github.com/jarrodwatts/jev-trader) | 2,596 | 应用 | "One AI trade decision every Monad block. Jev on Kuru MON-USDC." |
| [`TianyuCodings/NanoJev`](https://github.com/TianyuCodings/NanoJev) | 2,344 | 替代或复刻模型 | "A nano replica of Jev: parallel decisions, dynamic candidates, and an end-to-end training ..." |
| [`wfzyx/von`](https://github.com/wfzyx/von) | 724 | 替代或复刻模型 | "The open-source System One decision model. Sub-15ms, non-autoregressive, local drop-in alt..." |
| [`anishfn/shapeshift`](https://github.com/anishfn/shapeshift) | 705 | 应用 | "An input that becomes what you mean: one text box that morphs into the right UI as you typ..." |
| [`milind-soni/tiptour-macos`](https://github.com/milind-soni/tiptour-macos) | 665 | 应用 | "Open-Source fast local computer use" |
| [`duanebester/gooey`](https://github.com/duanebester/gooey) | 632 | 库或集成 | "Gooey is a hybrid immediate/retained mode UI framework designed for building fast, GPU-ren..." |
| [`rmalde/minecraft-agent`](https://github.com/rmalde/minecraft-agent) | 560 | 应用 | "Astra planner and JEV controller for Minecraft, with native recording, tested routes, and ..." |
| [`jev-chat/jev-chat-jarvis-mac`](https://github.com/jev-chat/jev-chat-jarvis-mac) | 417 | 应用 | （描述非英文） |

#### Universal Verification

| 仓库 | star 数 | 类型 | GitHub 描述（原文引用） |
| --- | ---: | --- | --- |
| [`Pluviobyte/rnskill`](https://github.com/Pluviobyte/rnskill) | 1,615 | 开发者工具 | （描述非英文） |
| [`JoasASantos/NeuroSploit`](https://github.com/JoasASantos/NeuroSploit) | 1,396 | 开发者工具 | "NeuroSploit is an advanced, AI-powered penetration testing framework designed to automate ..." |
| [`bitsocialnet/seedit`](https://github.com/bitsocialnet/seedit) | 416 | 应用 | "A peer-to-peer Reddit alternative." |
| [`coldteadotai/abide`](https://github.com/coldteadotai/abide) | 364 | 开发者工具 | "Make your coding agent abide by all your project rules" |
| [`edinetdb/dexter-jp`](https://github.com/edinetdb/dexter-jp) | 310 | 应用 | （描述非英文） |
| [`SREGym/SREGym`](https://github.com/SREGym/SREGym) | 305 | 基准或评测 | "Can AI agents resolve production incidents?" |
| [`NiazMorshed2007/jev-review`](https://github.com/NiazMorshed2007/jev-review) | 230 | 开发者工具 | "Local-first MCP plugin for continuous software-quality review by AI coding agents, powered..." |
| [`liuyanghejerry/Clausura`](https://github.com/liuyanghejerry/Clausura) | 203 | 开发者工具 | "CI-native agent CLI tool for deterministic pipeline gating." |
| [`wlsdks/ontology-atlas`](https://github.com/wlsdks/ontology-atlas) | 139 | 开发者工具 | "Understand what your codebase builds, why it is structured that way, and what a change cou..." |
| [`zetaalphavector/RAGElo`](https://github.com/zetaalphavector/RAGElo) | 133 | 开发者工具 | "RAGElo is a set of tools that helps you selecting the best RAG-based LLM agents by using a..." |

#### AI Map Reduce over Big Data

| 仓库 | star 数 | 类型 | GitHub 描述（原文引用） |
| --- | ---: | --- | --- |
| [`genspark-ai/genoffice`](https://github.com/genspark-ai/genoffice) | 7,980 | 应用 | "Free, open-source AI Office suite: Docs, Sheets, Slides, PDF, Markdown and HTML editors wi..." |
| [`LinklyAI/best-skills`](https://github.com/LinklyAI/best-skills) | 605 | 应用 | "Daily-updated Top 100 Agent Skills rankings — installs, growth, and social buzz   aggregat..." |
| [`superagents-lab/jev-search`](https://github.com/superagents-lab/jev-search) | 473 | 应用 | "Search the web with TypeSafe's Jev: source selection, query understanding and relevance ra..." |
| [`kyotofin/tax-doc-classifier`](https://github.com/kyotofin/tax-doc-classifier) | 469 | 应用 | "Tax document page classifier built on Jev decisions. 100% strict accuracy across 261 IRS f..." |
| [`mrmps/classifier-dev`](https://github.com/mrmps/classifier-dev) | 423 | 开发者工具 | "Zero-shot text classification over plain HTTP — no API key, no account. One Cloudflare Wor..." |
| [`Extelligence-ai/bagel`](https://github.com/Extelligence-ai/bagel) | 397 | 开发者工具 | "Query robotics, drone, and IoT data in plain English through an MCP server, with an intell..." |
| [`realZachi/pg-jev`](https://github.com/realZachi/pg-jev) | 372 | 库或集成 | "Ask your Postgres tables questions in plain language. A PostgreSQL extension powered by Ty..." |
| [`noperator/siftrank`](https://github.com/noperator/siftrank) | 219 | 开发者工具 | "Use LLMs to find the needles in your haystack" |
| [`ielab/llm-rankers`](https://github.com/ielab/llm-rankers) | 213 | 基准或评测 | "Document Ranking with Large Language Models." |
| [`uehaj/jev-semgrep`](https://github.com/uehaj/jev-semgrep) | 144 | 开发者工具 | "grep by meaning, across languages. TypeSafe Jev scores every line against a meaning; combi..." |

#### 不属于五个类别

| 仓库 | star 数 | 类型 | GitHub 描述（原文引用） |
| --- | ---: | --- | --- |
| [`vllm-project/vllm`](https://github.com/vllm-project/vllm) | 92,792 | 替代或复刻模型 | "A high-throughput and memory-efficient inference and serving engine for LLMs" |
| [`NandhaKishorM/laya`](https://github.com/NandhaKishorM/laya) | 26,619 | 替代或复刻模型 | "Non-autoregressive System 1 decision engine. Typed choice, score and yes/no decisions over..." |
| [`jaredpalmer/kev`](https://github.com/jaredpalmer/kev) | 7,394 | 替代或复刻模型 | "Jev-like family of decision models built on top of Qwen3.5/3.8 you can train and run on yo..." |
| [`typesafe-ai/skills`](https://github.com/typesafe-ai/skills) | 2,277 | 开发者工具 | "Agent skills for building with TypeSafe's System One API" |
| [`bespokelabsai/nimble`](https://github.com/bespokelabsai/nimble) | 1,865 | 替代或复刻模型 | "Local typed decisions, contrastive data curation, and model evaluation." |
| [`vinnylarouge/jevlike`](https://github.com/vinnylarouge/jevlike) | 1,318 | 替代或复刻模型 | （无描述） |
| [`ENTERPILOT/GoModel`](https://github.com/ENTERPILOT/GoModel) | 1,189 | 库或集成 | "AI gateway / AI control plane / AI proxy written in Go. Unified OpenAI-compatible and Anth..." |
| [`feder-cr/jev`](https://github.com/feder-cr/jev) | 1,053 | 替代或复刻模型 | "jevos is an open-source alternative to Jev for yes/no decisions that runs on your laptop." |
| [`Mapika/decider`](https://github.com/Mapika/decider) | 819 | 替代或复刻模型 | "A family of System One-style models fine-tuned from Qwen3.5, designed for one-pass typed d..." |
| [`nokia-applied-research/AnyJev`](https://github.com/nokia-applied-research/AnyJev) | 819 | 替代或复刻模型 | "Turn any LLM into a Jev-style decision model: typed decisions, real probabilities, no trai..." |

### 6.7 各用例的知名项目

第 6.3 节每个用例（通用或其他除外）中 star 数最多的应用、开发者工具和库。其他类型不在此列：目录、基准、复刻和账户工具。与第 6.6 节不同，此表包含第 7 节中的大型集成。

| 用例 | star 数最多的应用、开发者工具和库 |
| --- | --- |
| 游戏 | [`rmalde/minecraft-agent`](https://github.com/rmalde/minecraft-agent) (560), [`fhshaik/typesafe-mario`](https://github.com/fhshaik/typesafe-mario) (407), [`CharTyr/STS2-Agent`](https://github.com/CharTyr/STS2-Agent) (329), [`standardagents/jevpilot`](https://github.com/standardagents/jevpilot) (193) |
| 搜索与检索 | [`volcengine/OpenViking`](https://github.com/volcengine/OpenViking) (38,790), [`assafelovic/gpt-researcher`](https://github.com/assafelovic/gpt-researcher) (29,649), [`genspark-ai/genoffice`](https://github.com/genspark-ai/genoffice) (7,980), [`zilliztech/memsearch`](https://github.com/zilliztech/memsearch) (2,664) |
| 模型路由 | [`BerriAI/litellm`](https://github.com/BerriAI/litellm) (59,728), [`Hmbown/Codewhale`](https://github.com/Hmbown/Codewhale) (41,030), [`lidge-jun/opencodex`](https://github.com/lidge-jun/opencodex) (16,454), [`2FastLabs/agent-squad`](https://github.com/2FastLabs/agent-squad) (7,774) |
| 大语言模型护栏 | [`bytedance/deer-flow`](https://github.com/bytedance/deer-flow) (83,050), [`confident-ai/deepeval`](https://github.com/confident-ai/deepeval) (18,469), [`AIPentest/CyberStrikeAI`](https://github.com/AIPentest/CyberStrikeAI) (7,049), [`FailproofAI/failproofai`](https://github.com/FailproofAI/failproofai) (5,178) |
| 语义代码检查 | [`sickn33/agentic-awesome-skills`](https://github.com/sickn33/agentic-awesome-skills) (46,993), [`duriantaco/skylos`](https://github.com/duriantaco/skylos) (838), [`devagrawal09/jev-review`](https://github.com/devagrawal09/jev-review) (625), [`dxos/dxos`](https://github.com/dxos/dxos) (524) |
| 风险评估 | [`TauricResearch/TradingAgents`](https://github.com/TauricResearch/TradingAgents) (108,900), [`koala73/worldmonitor`](https://github.com/koala73/worldmonitor) (87,472), [`virattt/ai-hedge-fund`](https://github.com/virattt/ai-hedge-fund) (63,771), [`OpenByteInc/QuantDinger`](https://github.com/OpenByteInc/QuantDinger) (12,233) |
| 内容审核与信任安全 | [`dubinc/dub`](https://github.com/dubinc/dub) (24,834), [`MillionSend/millionsend`](https://github.com/MillionSend/millionsend) (170), [`rokcso/bluenoise`](https://github.com/rokcso/bluenoise) (91), [`TiraelSedai/ClubDoorman`](https://github.com/TiraelSedai/ClubDoorman) (69) |
| 客户支持 | [`chatwoot/chatwoot`](https://github.com/chatwoot/chatwoot) (37,242), [`MattiaIppoliti/ciele`](https://github.com/MattiaIppoliti/ciele) (249), [`GiesN/typesafe-jev-workflow`](https://github.com/GiesN/typesafe-jev-workflow) (12), [`rayanweragala/jev-call-router`](https://github.com/rayanweragala/jev-call-router) (12) |
| 招聘 | [`skeptrunedev/jev-recruiter`](https://github.com/skeptrunedev/jev-recruiter) (50), [`hqman/JevScout`](https://github.com/hqman/JevScout) (38), [`fusei1008/boss-auto-job-helper`](https://github.com/fusei1008/boss-auto-job-helper) (9), [`gtaras7/typesafe-jev`](https://github.com/gtaras7/typesafe-jev) (8) |
| 科学发现 | [`PKU-YuanGroup/OpenAI4S`](https://github.com/PKU-YuanGroup/OpenAI4S) (594), [`Sreehari05055/thesys-core`](https://github.com/Sreehari05055/thesys-core) (156), [`topherchris420/james_library`](https://github.com/topherchris420/james_library) (72), [`choxos/jev-reviewer`](https://github.com/choxos/jev-reviewer) (35) |
| 电商平台 | [`campusx-official/jev-demo`](https://github.com/campusx-official/jev-demo) (15), [`littlewindy123/jev-weekend-shopping-chrome`](https://github.com/littlewindy123/jev-weekend-shopping-chrome) (8), [`Emenowicz/jev-sap-commerce`](https://github.com/Emenowicz/jev-sap-commerce) (8), [`jaturapornchai/bccrm`](https://github.com/jaturapornchai/bccrm) (4) |
| 潜在客户开发 | [`twentyhq/twenty`](https://github.com/twentyhq/twenty) (57,572), [`getanyapi-com/lurk`](https://github.com/getanyapi-com/lurk) (101), [`ZeroGold/call-coach-ai`](https://github.com/ZeroGold/call-coach-ai) (44), [`oguzhankayan/reddit-radar`](https://github.com/oguzhankayan/reddit-radar) (41) |
| 法律与合规 | [`kyotofin/tax-doc-classifier`](https://github.com/kyotofin/tax-doc-classifier) (469), [`edinetdb/dexter-jp`](https://github.com/edinetdb/dexter-jp) (310), [`stella/stella`](https://github.com/stella/stella) (255), [`qpiai/anchor`](https://github.com/qpiai/anchor) (10) |
| 图与知识图谱 | [`semantica-agi/semantica`](https://github.com/semantica-agi/semantica) (13,494), [`nimbalyst/nimbalyst`](https://github.com/nimbalyst/nimbalyst) (1,787), [`davide-desio-eleva/kirograph`](https://github.com/davide-desio-eleva/kirograph) (152), [`jexp/neo4jev`](https://github.com/jexp/neo4jev) (145) |
| 广告 | [`realZachi/typesafe-adblock`](https://github.com/realZachi/typesafe-adblock) (85), [`artemnovitckii/creator-lab`](https://github.com/artemnovitckii/creator-lab) (82), [`tomascupr/reelql`](https://github.com/tomascupr/reelql) (27), [`ehui1226/hookmeter-jev`](https://github.com/ehui1226/hookmeter-jev) (22) |
| 金融犯罪 | [`klauswg/jev-guard`](https://github.com/klauswg/jev-guard) (37), [`sandeco/pix-golpe`](https://github.com/sandeco/pix-golpe) (25), [`1aifanatic/jev-uipath-coded-agent`](https://github.com/1aifanatic/jev-uipath-coded-agent) (2), [`ndolinschi/cartshield`](https://github.com/ndolinschi/cartshield) (1) |
| 预测建模的特征提取 | [`edamame-labs/tab-jev`](https://github.com/edamame-labs/tab-jev) (4), [`Kaos599/jev-writer`](https://github.com/Kaos599/jev-writer) (1), [`sedthh/xjevboost`](https://github.com/sedthh/xjevboost) (1), [`chen-junluo/jev-measure`](https://github.com/chen-junluo/jev-measure) (1) |
| 保险理赔 | [`vishalbitit/jev-prior-auth-triage`](https://github.com/vishalbitit/jev-prior-auth-triage) (0), [`akhilkoduriak/jev-claim-processor`](https://github.com/akhilkoduriak/jev-claim-processor) (0), [`sureshmanem/typesafe_jev_poc`](https://github.com/sureshmanem/typesafe_jev_poc) (0), [`franciscojunqueira/jev-tiss`](https://github.com/franciscojunqueira/jev-tiss) (0) |
| 需求预测 | [`Orcaset/jev-revenue-forecaset`](https://github.com/Orcaset/jev-revenue-forecaset) (1), [`abhisingh9696/sop-solver-mcp`](https://github.com/abhisingh9696/sop-solver-mcp) (1), [`londrwus/techeu_agentichack`](https://github.com/londrwus/techeu_agentichack) (1), [`igun997/laya-research`](https://github.com/igun997/laya-research) (1) |

## 7. 成熟开源项目中的 Jev

在 GitHub 代码搜索中检索 `api.typesafe.ai`、`typesafe-ai/jev` 和 `jev-latest`，找到 1,328 个在源代码中引用 Jev 的仓库。其中 110 个有 1,000 或更多 star，这些仓库中有 104 个通过阅读默认分支上的代码进行了检查。其余 6 个未检查：复刻 `NandhaKishorM/laya`、`jaredpalmer/kev`、`TianyuCodings/NanoJev` 和 `bespokelabsai/nimble` 见第 8 节；`vllm-project/vllm` 仅通过 `examples/features/structured_diffusion` 中的一个示例服务器匹配，该服务器用本地扩散模型响应 `POST /v1/systemone` 请求，与 Unsloth Studio 和 colibri 的形态相同；`yibie/awesome-jev` 是一个社区列表（第 10 节），其唯一匹配是它自己的整理用 agent 技能。104 个中有 101 个在默认分支上有 Jev 集成代码。其余 3 个没有：`kunchenguid/no-mistakes` 移除其集成的日期是 2026-09-22，`op7418/CodePilot` 仅在研究文档中提及 Jev，`fy-agent/fyagent` 仅保留其开发者的编码 agent 的归档任务记录，这些编码 agent 通过外部 Model Context Protocol 服务器调用 Jev。持质疑态度的第二位读者在全部 104 个案例中都与第一位读者就是否存在集成达成一致，并更正了其中 23 个案例中对 Jev 所做决策的描述。本报告对两位读者的结果应用三条规则。FyAgent 算作没有集成代码，因为它仅保留归档任务记录。colibri 与 Unsloth Studio 一样不归入任何类别，因为 colibri 和 Unsloth Studio 都不使用其本地端点返回的决策；最终标签给两者的类型都是“替代或复刻模型”。Rowboat 的用例归为通用，因为它的 Jev 决策路由聊天草稿，从不选择模型，所以多数投票得出的模型路由标签不适用。

已核实项目中的集成类型：分类组件 29，模型提供方 28，模型或工具路由器 12，护栏或闸门 11，agent 中间件或插件 9，其他 5，重排序或检索 5，模型目录或配置项 1，agent 技能或提示词 1。状态：需手动开启且已进入稳定版 43，需手动开启，仅已合并，未进入任何带标签的版本 22，实验性或 alpha 18，已发布且默认开启 9，需手动开启，仅在候选版本或预发布中 7，仅示例 1，默认开启，仅已合并，未进入任何带标签的版本 1。各项目内部 Jev 用途的类别（对于本身不做决策的库、提供方或网关，例如 pi、goose、Rig 和 Bifrost，则为其文档或示例所展示用途的类别）：Harness Engineering 44，AI Automation Software 27，Universal Verification 13，AI Map Reduce over Big Data 6，不属于五个类别 6，Real-time applications 5。

常见模式：模型提供方或客户端（OpenClaw、pydantic-ai、DSPy、Vercel AI SDK、ruby_llm、goose）；每一轮选择模型档位或推理强度的路由器（LiteLLM、Codewhale、LangChain 中间件）；针对有风险工具调用的闸门或上下文压缩器（deer-flow、LiteLLM、Hermes Agent 评测）；工作流或业务工具中的分类步骤（Twenty、AutoGPT、Apache Airflow、Chatwoot）；以及重排序器（OpenViking）。少数项目（Unsloth Studio、colibri）提供自己的 Jev 兼容端点，由本地模型响应，因此并不调用 Jev 本身。PostHog 的产品功能调用 JevK5，这是 PostHog 自行托管的一个开放复刻，而其 TypeSafe 客户端在 master 分支上没有调用方。MCPJam Inspector 仅从其私有后端调用 Jev。

| 项目 | star 数 | 集成 | Jev 决定什么 | 状态 | 类别 |
| --- | ---: | --- | --- | --- | --- |
| [`openclaw/openclaw`](https://github.com/openclaw/openclaw) | 390,656 | 官方 TypeSafe 插件，将 Jev 注册为 OpenClaw 的决策模型提供方。 | agent 或插件提出的选择、评分和是/否问题，例如客户支持路由。 | 已发布，需手动开启 | Harness Engineering |
| [`NousResearch/hermes-agent`](https://github.com/NousResearch/hermes-agent) | 249,467 | 仅用于评测的上下文压缩实验组，外加目录中指向 14 个社区 Jev 插件的条目。 | 压缩期间每个工具调用及其结果是否保留；评分卡结论为不采用。 | 实验性，仅用于评测 | Harness Engineering |
| [`Significant-Gravitas/AutoGPT`](https://github.com/Significant-Gravitas/AutoGPT) | 187,589 | AutoGPT Platform 中的七个 Jev 工作流块，使用每个用户自己的 TypeSafe 密钥。 | 用户构建的 agent 图中的选择、评分、是/否、路由、最佳选取和筛选决策。 | 已发布，需手动开启 | AI Automation Software |
| [`langchain-ai/langchain`](https://github.com/langchain-ai/langchain) | 147,158 | 第一方 `langchain-typesafe` 合作伙伴包，包含一个分类器和两个实验性 agent 中间件。 | 调用方提供的分类、由哪个聊天模型处理一次 agent 运行，以及哪些工具调用有风险。 | PyPI 上的 alpha 版本 | Harness Engineering |
| [`Shubhamsaboo/awesome-llm-apps`](https://github.com/Shubhamsaboo/awesome-llm-apps) | 139,981 | 两个示例应用：Needle 语义页内查找和 Ripple Google Docs 冲突检查器。 | 哪些页面段落与搜索匹配，以及哪些文档句子与某次编辑冲突。 | 仅示例 | AI Map Reduce over Big Data |
| [`earendil-works/pi`](https://github.com/earendil-works/pi) | 109,753 | pi-ai 模型库中内置的 TypeSafe 分类器提供方和 `classify()` 方法。 | pi 本身不做任何决策；库的用户调用它来处理选择、评分和是/否问题。 | 已合并，需手动开启，尚未发布 | Harness Engineering |
| [`TauricResearch/TradingAgents`](https://github.com/TauricResearch/TradingAgents) | 108,900 | 直接调用的 HTTP 客户端，为 Sentiment Analyst agent 筛选社交媒体帖子。 | 哪些 StockTwits 和 Reddit 帖子切题，以及它们看涨、看跌或中性的立场。 | 已在 `v0.5.1` 中发布，需手动开启 | Harness Engineering |
| [`koala73/worldmonitor`](https://github.com/koala73/worldmonitor) | 87,472 | 用于新闻标题威胁等级的影子第二标注器，另有一个维护者使用的内部链接工具。 | 新闻标题威胁等级（仅影子模式）；构建出的页面上的内部链接和相关阅读。 | 已合并，需手动开启，影子模式，尚未发布 | Universal Verification |
| [`bytedance/deer-flow`](https://github.com/bytedance/deer-flow) | 83,050 | 需手动开启的 TypeSafe 工具调用护栏提供方，另有记忆闸门和示例插件。 | 是否拒绝有风险的工具调用，以及一批对话是否值得提取记忆。 | 已合并，需手动开启，尚未发布 | Harness Engineering |
| [`unslothai/unsloth`](https://github.com/unslothai/unsloth) | 76,870 | Unsloth Studio 提供自己的与 Jev 兼容的端点，由本地 Laya 模型作答。 | 不决定任何事；本地 Laya 模型回答 Jev 格式的问题，而 Studio 自身不使用其中任何一项。 | 已合并，需手动开启，尚未发布 | 不属于五个类别 |
| [`virattt/ai-hedge-fund`](https://github.com/virattt/ai-hedge-fund) | 63,771 | 需手动开启的 TypeSafe 提供方，用 Jev 类型化问题运行投资者人格 agent。 | 每个人格针对每个股票代码和日期给出的看涨、看跌或中性信号，及其确信强度。 | 已在 `2.3.0` 中发布，需手动开启 | AI Automation Software |
| [`BerriAI/litellm`](https://github.com/BerriAI/litellm) | 59,728 | LiteLLM 中的 Jev 复杂度路由器、工具结果压缩护栏和 TypeSafe 透传。 | 由哪个模型层级处理 `auto` 请求，以及丢弃哪些不需要的工具结果。 | 已发布，需手动开启 | Harness Engineering |
| [`twentyhq/twenty`](https://github.com/twentyhq/twenty) | 57,572 | Twenty 中的 "Classify (Jev)" 工作流步骤，把类型化问题发送给 Jev。 | 选择、评分和布尔答案，后续工作流步骤用它们对记录进行路由或分支。 | 自 `v2.42.0` 起已发布，需手动开启 | AI Automation Software |
| [`aaif-goose/goose`](https://github.com/aaif-goose/goose) | 54,712 | goose 提供方库中的通用决策提供方，通过 goose Development Kit 对外提供。 | goose 内部不做任何决策；开发者调用它来处理类型化的是/否、选择和评分问题。 | alpha，仅作为库 | Harness Engineering |
| [`apache/airflow`](https://github.com/apache/airflow) | 46,993 | `apache-airflow-providers-common-ai` 中可选的 `typesafe` 额外依赖，另有后续 pull request 加入的分类器重试闸门和分支闸门。 | 用于重试的任务失败类别、要分支到哪个下游任务，以及文本标签。 | 已合并，需手动开启，仅在候选版本 `0.10.0rc1` 中 | AI Automation Software |
| [`sickn33/agentic-awesome-skills`](https://github.com/sickn33/agentic-awesome-skills) | 46,993 | 仅供维护者使用的脚本，就每个改动过的技能目录向 Jev 提出五个审查问题。 | 仅供参考的安全、来源和优先级标记，用于给待检查的 pull request 排序；从不合并或阻止。 | 已发布，需手动开启，仅供参考 | Universal Verification |
| [`siyuan-note/siyuan`](https://github.com/siyuan-note/siyuan) | 46,534 | 可选的决策模型设置，以及一个仅供 agent 使用的决策工具，二者都调用 Jev。 | 主 agent 委派的笔记块批量分类、过滤、选项挑选和评分。 | 已在 `v3.8.5` 中发布，默认关闭 | AI Map Reduce over Big Data |
| [`Hmbown/Codewhale`](https://github.com/Hmbown/Codewhale) | 41,030 | 一个 Rust 终端编码 agent 中用于 Auto 模型路由的需手动开启的决策路由器。 | 每个 Auto 轮次使用快速还是强模型层级，以及思考级别。 | 已合并，需手动开启，尚未发布 | Harness Engineering |
| [`tinyhumansai/openhuman`](https://github.com/tinyhumansai/openhuman) | 40,140 | 一个 Rust agent harness 中的 Jev 工具搜索排序器和 Jev 浏览器控制器。 | 每次工具搜索时哪个入围的延迟加载工具合适，以及每个下一步浏览器操作。 | 已发布，默认开启；浏览器功能需手动开启 | Harness Engineering |
| [`PostHog/posthog`](https://github.com/PostHog/posthog) | 39,974 | 在 master 上没有调用方的 TypeSafe 出站客户端，另有开放 JevK5 复刻的自托管副本（`posthog/hogference/jevk5-fp8-0.2`，见第 8 节），多个产品功能会调用它。 | master 上不通过 TypeSafe 决定任何事。JevK5 副本决定信号的可操作性和安全性、表情符号挑选、筛选标签页、事件匹配和回放重排序。 | 实验性，大多位于功能开关之后 | Real-time applications |
| [`Yeachan-Heo/oh-my-claudecode`](https://github.com/Yeachan-Heo/oh-my-claudecode) | 39,375 | 一个 Claude Code 编排插件的钩子中的 Jev 客户端和影子判断解析器。 | 不决定任何事；Jev 的回答只在影子模式下记录。在声明的 10 个判断点中，只有 6 个在测试之外有调用方，全部位于 TypeScript 钩子桥接层之后，且默认安装不发送任何请求。 | 实验性，影子模式 | Harness Engineering |
| [`volcengine/OpenViking`](https://github.com/volcengine/OpenViking) | 38,790 | OpenViking（一个面向 agent 的上下文数据库）中的 `JevRerankClient` 重排序提供方。 | 在 `search()` 思考模式（而非 `find()`）中，每个检索到的候选项与查询的相关性。 | 已合并，需手动开启，尚未发布 | Harness Engineering |
| [`stanfordnlp/dspy`](https://github.com/stanfordnlp/dspy) | 38,378 | 实验性的 `dspy.experimental.TypeSafe` 语言模型客户端，通过 `dspy[typesafe]` 额外依赖安装。 | `Predict` 程序的是/否、评分和选择输出字段；阈值在本地应用。 | 实验性，已在 `3.4.0` 中发布 | AI Automation Software |
| [`JustVugg/colibri`](https://github.com/JustVugg/colibri) | 37,931 | 一个纯 C 模型引擎的 Python 网关中复制了 Jev 契约的本地 `/v1/systemone` 路由。 | 不决定任何事；本地提供的模型回答 Jev 格式的是/否、选择和评分问题。 | 已发布，默认开启 | 不属于五个类别 |
| [`chatwoot/chatwoot`](https://github.com/chatwoot/chatwoot) | 37,242 | Captain Classifier 和 Enterprise Conversation Monitors 通过 OpenRouter 调用 Jev。 | 建议的标签、对话优先级，以及对话是否匹配用于报告的自然语言监控条件。 | 已合并，需手动开启，尚未发布 | AI Automation Software |
| [`esengine/DeepSeek-Reasonix`](https://github.com/esengine/DeepSeek-Reasonix) | 35,705 | Reasonix Studio 终端编码 agent 中的 Go 客户端和需手动开启的 `system_one` 工具。 | 主聊天模型通过该工具提出的类型化问题，另有一个连通性探测。 | 已发布，需手动开启，预发布构建 | Harness Engineering |
| [`can1357/oh-my-pi`](https://github.com/can1357/oh-my-pi) | 33,479 | 以 TypeSafe 作为其 `judge` 角色原生提供方的编码 agent | 推理强度、意外停止、语义 `find` 结果、规则警告，以及要暂存哪些 git 更改 | 已发布；有 TypeSafe 或 OpenRouter 密钥时，`find` 和 git 暂存默认开启，其余需手动开启 | Harness Engineering |
| [`zeroclaw-labs/zeroclaw`](https://github.com/zeroclaw-labs/zeroclaw) | 32,904 | 标准操作程序引擎把 Jev 作为默认决策模型调用 | 匹配到的事件是否启动一个程序，以及该次运行使用哪种执行模式 | 已合并，需手动开启，尚未发布 | AI Automation Software |
| [`agentscope-ai/agentscope`](https://github.com/agentscope-ai/agentscope) | 32,454 | 与提供方无关的分类器层，其唯一后端是 `JevClassifierModel` | 开发者定义的类型化问题，以及由哪个聊天模型处理每条 agent 回复 | 已合并，需手动开启，尚未发布 | Harness Engineering |
| [`davila7/claude-code-templates`](https://github.com/davila7/claude-code-templates) | 31,985 | 六个需手动开启的 Claude Code 钩子插件，通过 HTTP 调用 Jev | 模型层级和推理强度、技能建议、提示词安全、工具权限、Bash 沙箱，以及国际象棋走法 | 已合并，需手动开启，抢先体验，尚未发布 | Harness Engineering |
| [`ComposioHQ/composio`](https://github.com/ComposioHQ/composio) | 30,337 | 面向 Composio 工具的第一方 TypeSafe（Jev）提供方，支持 TypeScript 和 Python | 调用哪个工具、部分参数、是否立即行动，以及否决不匹配的调用 | 已发布，需手动开启 | Harness Engineering |
| [`simstudioai/sim`](https://github.com/simstudioai/sim) | 29,741 | 工作流 Agent 块的 TypeSafe 原生模型提供方 | 只有工作流构建者编写的类型化问题；Sim 没有硬编码任何 Jev 决策 | 已发布，需手动开启 | AI Automation Software |
| [`assafelovic/gpt-researcher`](https://github.com/assafelovic/gpt-researcher) | 29,649 | Jev 是抓取到的研究段落的默认上下文过滤器 | 按 0 到 3 的有用性评分，决定哪些抓取到的段落进入写作模型 | 已发布，有 TypeSafe 密钥时默认开启 | Harness Engineering |
| [`mlflow/mlflow`](https://github.com/mlflow/mlflow) | 28,153 | TypeSafe 网关提供方，另可通过 `typesafe:/jev-latest` 把 Jev 用作评审模型 | 内置评审和自定义评审的评测结论，例如相关性、正确性和安全性 | 已合并，需手动开启，尚未发布 | Universal Verification |
| [`vercel/ai`](https://github.com/vercel/ai) | 26,993 | 用于 `experimental_evaluate` 的官方 `@ai-sdk/typesafe-ai` 提供方，另有一个网关模型标识符 | 关于一个 state 的具名选择、评分和布尔问题；阈值由应用代码设定 | 实验性，已发布到 npm | AI Automation Software |
| [`dubinc/dub`](https://github.com/dubinc/dub) | 24,834 | Dub 在 dub.sh 和 dub.link 上创建短链接之前，由 Jev 筛查目标 URL | URL 是否恶意：高于 0.5 则阻止该链接，高于 0.8 则把该域名列入黑名单 | 已发布，默认开启 | AI Automation Software |
| [`different-ai/openwork`](https://github.com/different-ai/openwork) | 23,758 | Testkit 验证词典编译器，另有一个不生效的 pull request 覆盖率参考提示 | 一个自然语言意图要求哪些词典检查，以及词典是否覆盖该意图 | 实验性，需手动开启 | AI Automation Software |
| [`comet-ml/opik`](https://github.com/comet-ml/opik) | 22,260 | 追踪 Jev System One 调用并计算其费用的可观测性集成 | Opik 内部不决定任何事；Opik 只记录用户发出的 Jev 调用 | 已发布，需手动开启 | 不属于五个类别 |
| [`pydantic/pydantic-ai`](https://github.com/pydantic/pydantic-ai) | 20,214 | Jev 模型提供方（`TypeSafeModel`），用类型化问题填充结构化的 agent 输出 | 结构化输出字段，以及文本要求使用哪个工具或输出路由 | 已发布，作为需手动开启的额外依赖 | Harness Engineering |
| [`1jehuang/jcode`](https://github.com/1jehuang/jcode) | 20,169 | 带有共享 Jev 决策客户端的 Rust 终端编码 agent | 召回哪些记忆、下一步浏览器操作，以及语音意图（仅在库中） | 已发布，存在凭据时默认开启 | Harness Engineering |
| [`confident-ai/deepeval`](https://github.com/confident-ai/deepeval) | 18,469 | 一等支持的 TypeSafe 提供方，让 Jev 在 Python 和 TypeScript 中评判评测指标 | 受支持的内置指标（不含 `GEval`）的结论、分类器标签，以及自定义 `JevEval` 问题 | 已发布，需手动开启 | Universal Verification |
| [`vercel-labs/json-render`](https://github.com/vercel-labs/json-render) | 18,334 | 根据应用提供的元素候选项组合用户界面规格的实验性函数 | 根组件、包含哪些候选项、它们的位置，以及每个后续编辑操作 | 实验性，包含在 npm `0.21.0` 中 | Real-time applications |
| [`langchain-ai/langchainjs`](https://github.com/langchain-ai/langchainjs) | 18,232 | `@langchain/typesafe` 包，含 `TypeSafeClassifier` 和两个实验性 agent 中间件 | 开发者定义的类型化问题、一次运行使用哪个聊天模型，以及工具调用是否有风险 | 已发布，需手动开启；中间件尚未发布 | Harness Engineering |
| [`rowboatlabs/rowboat`](https://github.com/rowboatlabs/rowboat) | 17,982 | 一个 TypeScript Jev 客户端驱动两个 Spaces 聊天功能：Auto routing 和 `/find` | 草稿是新消息还是线程回复、提及建议，以及搜索匹配 | 已合并，需手动开启，尚未发布 | Real-time applications |
| [`lidge-jun/opencodex`](https://github.com/lidge-jun/opencodex) | 16,454 | 面向 Codex 和 Claude Code 的提供方代理，带有需手动开启的 `jev` Combo 路由策略 | 每次调用的首个目标模型和推理强度，取自运营者的允许列表 | 已发布，需手动开启 | Harness Engineering |
| [`Effect-TS/effect`](https://github.com/Effect-TS/effect) | 16,240 | `@effect/ai-typesafe` 包，用 Jev 支撑 Effect 的 `DecisionModel` 服务 | Effect 程序中具名决策的分类标签、评级，以及是或否的概率 | 需手动开启，候选版本，标记为不稳定 | AI Automation Software |
| [`pipecat-ai/pipecat`](https://github.com/pipecat-ai/pipecat) | 15,925 | 为语音 agent 中 Pipecat 的 `BaseClassifier` 接口提供的第一方 `JevClassifier` 后端 | 语音信箱还是对话、`UIWorker` 屏幕决策，以及评测裁判的裁决 | 已发布，需手动开启的可选依赖 | Real-time applications |
| [`semantica-agi/semantica`](https://github.com/semantica-agi/semantica) | 13,494 | `semantica.llms` 中仅做决策的 `Jev` 和 `AsyncJev` 提供方类 | 关于调用方提供的应用状态的路由、分类、二元检查和评分标准分数 | 实验性，已合并，尚未发布 | AI Automation Software |
| [`elie222/inbox-zero`](https://github.com/elie222/inbox-zero) | 12,354 | 决策模型层，其唯一提供方是用于邮件处理的 TypeSafe 适配器 | 邮件规则选择、冷邮件、发件人类别、线程状态、发件人模式、回复记忆、退订页面 | 已合并，需手动开启，尚未发布 | AI Automation Software |
| [`Arize-ai/phoenix`](https://github.com/Arize-ai/phoenix) | 11,635 | TypeScript 分类评估器通过 Vercel AI SDK 接受 Jev 作为模型 | 评估器标签（例如 hallucinated 或 grounded），作为一个 choice 问题 | 已发布，需手动开启 | Universal Verification |
| [`EKKOLearnAI/ekko-studio`](https://github.com/EKKOLearnAI/ekko-studio) | 11,214 | 多 agent 工作区中按配置档设置的 TypeSafe 选项，用于可选的 Jev 决策 | 记忆召回与写入审查、技能路由、学习预检、浏览器元素匹配，以及结果检查 | 已合并，需手动开启，尚未发布 | Harness Engineering |
| [`yihong0618/bilingual_book_maker`](https://github.com/yihong0618/bilingual_book_maker) | 9,811 | 用于双语电子书翻译的 `JevBackend` 计划模式分类器 | 每个标签签名是需要翻译的书籍内容，还是应跳过的文本 | 已发布，需手动开启 | AI Automation Software |
| [`getsentry/sentry-javascript`](https://github.com/getsentry/sentry-javascript) | 8,745 | Sentry 追踪把 Vercel AI SDK 的 Jev evaluate 调用记录为 span。 | 无。Sentry 只观察用户应用发起的 Jev 调用。 | 实验性上游应用程序接口（`experimental_evaluate`），已合并，尚未发布 | 不属于五个类别 |
| [`0xPlaygrounds/rig`](https://github.com/0xPlaygrounds/rig) | 8,745 | 提供方 crate `rig-typesafeai`，带类型化 Jev 客户端和两个示例包。 | 库本身不做任何决策。示例：客服路由和是否需要澄清，用于选择聊天 agent 的策略。 | 实验性，需手动开启的功能特性 | Harness Engineering |
| [`maximhq/bifrost`](https://github.com/maximhq/bifrost) | 8,394 | 开源网关，内置 TypeSafe 提供方，把决策请求转发给 Jev。 | 对 Bifrost 本身而言无。网关客户端发送自己的问题，例如客服工单分诊。 | 已发布，需手动开启 | AI Automation Software |
| [`YaoApp/yao`](https://github.com/YaoApp/yao) | 8,035 | TypeSafe 决策提供方，外加内置的 `decision_decide` agent 工具和技能。 | 为 agent 提供类型化答案：工单路由、质量等级、意图、情感、紧急程度概率。 | 需手动开启，仅在候选版本中（`v1.0.0-rc23` 及之后） | AI Automation Software |
| [`2FastLabs/agent-squad`](https://github.com/2FastLabs/agent-squad) | 7,774 | TypeScript 和 Python 中内置的 `JevClassifier`，把每个用户轮次路由到一个 agent。 | 由哪个已注册 agent 处理每条用户输入，或为未知，并附带已校准的置信度。 | 已发布，需手动开启 | Harness Engineering |
| [`opengeos/GeoLibre`](https://github.com/opengeos/GeoLibre) | 7,700 | 桌面地图助手用 Jev 实现快速命令路径和 Whitebox 工具搜索。 | 无需 agent 即可运行哪条简单地图命令，以及将哪些 Whitebox 工具列入候选名单。 | 已发布，需手动开启 | Harness Engineering |
| [`AIPentest/CyberStrikeAI`](https://github.com/AIPentest/CyberStrikeAI) | 7,049 | Go 语言 TypeSafe 客户端，用作人在回路审计 agent 的可选后端。 | 根据破坏性风险答案，每个不在允许列表中的工具调用是批准还是拒绝。 | 已发布，需手动开启 | Harness Engineering |
| [`anomalyco/models.dev`](https://github.com/anomalyco/models.dev) | 7,018 | 模型元数据数据库在五个提供方下、六个模型条目中列出 Jev，作为新的 `decision` 模型类型。 | 无。该项目存储 Jev 元数据，从不调用 Jev。 | 已发布，需手动开启 | 不属于五个类别 |
| [`tbphp/gpt-load`](https://github.com/tbphp/gpt-load) | 6,994 | 自托管网关，内置 Jev Decisions 渠道和两个实验性 Jev 功能。 | 每个自动请求由哪个模型预设处理，以及提示词内容是否违反护栏规则。 | 实验性，默认关闭 | Harness Engineering |
| [`BuilderIO/agent-native`](https://github.com/BuilderIO/agent-native) | 6,863 | agent 框架核心中可选的 Jev 预取，外加模板应用中的 Jev 规则。 | 要预加载的工具、技能和记忆；邮件规则匹配；隐私标记；邀请规则；重复记录。 | 已发布，需手动开启 | Harness Engineering |
| [`jev-chat/jev-chat-jarvis`](https://github.com/jev-chat/jev-chat-jarvis) | 6,770 | Android 聊天悬浮层，用 Jev 判断屏幕上的聊天并为回复排序。 | 意图、危险等级、需求、最佳行动、紧张是否化解，以及 3 条候选回复的排序。 | 已发布，默认开启 | Real-time applications |
| [`GreptimeTeam/greptimedb`](https://github.com/GreptimeTeam/greptimedb) | 6,715 | 实验性 SQL 函数 `ai_match`、`ai_choose` 和 `ai_score`，逐行调用 Jev。 | 逐行判断：某个陈述是否成立、哪个选项标签合适，以及等级评分。 | 实验性，需要设置环境变量开关 | AI Map Reduce over Big Data |
| [`ThinkInAIXYZ/deepchat`](https://github.com/ThinkInAIXYZ/deepchat) | 6,347 | Electron 助手，带有 `jev` 提供方协议和按 agent 配置、需手动开启的判断模型。 | 自动批准模式下的工具权限风险，以及要从上下文中剪除哪些过期的工具结果。 | 已在预发布版中发布，需手动开启 | Harness Engineering |
| [`apache/camel`](https://github.com/apache/camel) | 6,345 | 第一方 `camel-typesafe-ai` 组件和谓词语言，用于在集成路由中提出 Jev 问题。 | 语义路由决策：基于内容的路由、团队分类，以及评分，例如紧急程度。 | 实验性 | AI Automation Software |
| [`oomol-lab/pdf-craft`](https://github.com/oomol-lab/pdf-craft) | 6,333 | 扫描书籍 PDF 转换器用 Jev 审查每页的脚注分析。 | 每页是否通过严格标准；有风险的页面交由大语言模型修复。 | 已合并，需手动开启，尚未发布 | Universal Verification |
| [`Human-Agent-Society/reef`](https://github.com/Human-Agent-Society/reef) | 6,328 | agent 规则指示 reefine agent 在其编写的 pi 扩展中调用 Jev。 | 仅在生成的扩展中：工具调用风险、卡住或完成检查、选择、路由、上下文剪除。 | 已发布，需手动开启 | Harness Engineering |
| [`oomol-lab/open-connector`](https://github.com/oomol-lab/open-connector) | 5,905 | 身份认证网关中的 TypeSafe AI 提供方，带有 agent 可调用的 list-models 和 evaluate 操作。 | 无。它把调用方的问题透传给 Jev，并原样返回答案。 | 已发布，需手动开启 | 不属于五个类别 |
| [`agentscope-ai/agentscope-java`](https://github.com/agentscope-ai/agentscope-java) | 5,805 | 可选的 Java 扩展，带类型化 Jev 客户端、Spring Boot starter 和三个中间件。 | 在参考中间件中：agent 的工具选择、模型路由和工具调用安全。 | 需手动开启，位于 main 分支，未发布 | Harness Engineering |
| [`vercel/eve`](https://github.com/vercel/eve) | 5,388 | agent 框架，其 `evaluate` 封装默认通过 Vercel AI Gateway 使用 Jev。 | 每轮的模型选择、工具调用批准、子 agent 路由，以及评测评判。 | 已发布，需手动开启 | Harness Engineering |
| [`FailproofAI/failproofai`](https://github.com/FailproofAI/failproofai) | 5,178 | 编码 agent 策略钩子：先执行正则表达式策略，再做 Jev 审查。 | 每个受管控工具调用的风险；可以撤销可复审的拒绝，或自行添加拒绝。 | 已发布，需手动开启 | Harness Engineering |
| [`Kiln-AI/Kiln`](https://github.com/Kiln-AI/Kiln) | 5,111 | 原生 TypeSafe AI 模型提供方，带 `JevAdapter` 和内置的 Jev 1.13 模型。 | 单轮结构化任务的输出，以及作为裁判给出的通过、失败或星级评分。 | 已合并，需手动开启，尚未发布 | Universal Verification |
| [`langwatch/langwatch`](https://github.com/langwatch/langwatch) | 4,881 | Jev 是分类器接口背后的裁判，用于对追踪记录做即时评测。 | 客户编写的关于每个对话、追踪记录或模型调用的是否、分数和类别判断。 | 实验性 | AI Map Reduce over Big Data |
| [`latitude-dev/latitude-llm`](https://github.com/latitude-dev/latitude-llm) | 4,686 | 私有包 `@platform/ai-jev`，驱动一个需手动开启的 Jev 预分类器，服务于会话标记器。 | 对于被抽样排除的 agent 会话，11 个故障模式标记器中的哪些要进行完整分类。 | 已发布，需手动开启 | Universal Verification |
| [`crmne/ruby_llm`](https://github.com/crmne/ruby_llm) | 4,417 | 内置 TypeSafe 提供方，支撑类型化判断功能 `RubyLLM::Judge`，默认使用 Jev。 | 库本身不做任何决策。文档示例：工单紧急程度、部门路由、客户不满情绪。 | 需手动开启，位于 main 分支，未发布 | AI Automation Software |
| [`TanStack/ai`](https://github.com/TanStack/ai) | 3,141 | `decide()` evaluate 活动，带四个 Jev 适配器，包括 `@tanstack/ai-typesafe` | 回答应用代码据以分支的类型化问题；每轮挑选子 agent 及其顺序 | 已发布，需手动开启，已发布到 npm | Harness Engineering |
| [`spiceai/spiceai`](https://github.com/spiceai/spiceai) | 3,094 | Rust 评测模型提供方 `typesafe:jev`，位于新的 `POST /v1/evaluate` 端点之后 | Spice 内部无；回答应用代码通过运行时发送的类型化问题 | 已合并，需手动开启，尚未发布 | AI Automation Software |
| [`ianarawjo/ChainForge`](https://github.com/ianarawjo/ChainForge) | 3,030 | 在大语言模型评分节点中通过 OpenRouter 使用的 Jev 裁判 | 用 noul、choice 或 score 答案为每个被评分条目打分，并与人工标签比较 | 已发布，需手动开启 | Universal Verification |
| [`elie222/rakazo`](https://github.com/elie222/rakazo) | 3,003 | 可选的 Jev Auto Review 检查器，用于有重大后果的机器人工具调用 | 机器人工具调用是自动通过，还是请求用户批准 | 已合并，需手动开启，尚未发布 | Universal Verification |
| [`ax-llm/ax`](https://github.com/ax-llm/ax) | 2,946 | `typesafe` 提供方和原生客户端，把签名输出映射为 Jev 问题 | Ax 签名中必需的布尔和类别输出字段，例如工单分诊 | 已发布，需手动开启 | AI Automation Software |
| [`erupts/erupt`](https://github.com/erupts/erupt) | 2,872 | `erupt-ai-decision` 模块，以 Jev 作为内置的 System One 提供方 | 关于业务状态的类型化答案，Java 代码对其设定阈值以决定升级或路由 | 已在 `2.3.0` 中发布，需手动开启 | AI Automation Software |
| [`HarleyCoops/Math-To-Manim`](https://github.com/HarleyCoops/Math-To-Manim) | 2,666 | Jev 作为动画流水线每个检查点上的独立文本评估器 | 每个检查点的通过或拒绝、修复目标，以及下一个调查工具 | 已合并，默认开启，仅供参考，尚未发布 | Universal Verification |
| [`xerj-org/xerj`](https://github.com/xerj-org/xerj) | 2,510 | 可选的 `rerank` 搜索阶段，按 Jev 概率对命中结果重新排序 | 每个靠前的搜索命中结果回答该查询的概率；它决定新的顺序 | 需手动开启，仅在候选版本中（`v1.0.0-rc.75` 及更高版本） | AI Map Reduce over Big Data |
| [`lioensky/VCPToolBox`](https://github.com/lioensky/VCPToolBox) | 2,333 | 在五项 agent 中间件功能中共用的 Node.js Jev 客户端 | 工具折叠、上下文裁剪、上下文与记忆重排序，以及一个实验性虚拟工具 | 已合并，默认关闭，尚未发布 | Harness Engineering |
| [`MCPJam/inspector`](https://github.com/MCPJam/inspector) | 2,222 | 用于托管评测套件、仅供参考的 Rubric checks 评判器，在私有后端中运行 | 每条评分标准的是或否，以及对编写好的问题给出的 Choice 或 Score 答案 | 已发布，默认开启，仅供参考 | Universal Verification |
| [`oficcejo/aiagents-stock`](https://github.com/oficcejo/aiagents-stock) | 1,958 | 面向多 agent 股票分析系统、需手动开启的 Jev 结构化决策引擎 | 最终股票评级、重大风险标记、四项评分，以及新闻立场、紧迫性和相关性 | 已合并，需手动开启，尚未发布 | AI Automation Software |
| [`szczyglis-dev/py-gpt`](https://github.com/szczyglis-dev/py-gpt) | 1,952 | 内置的 `jev_evaluate` 工具插件，让聊天模型调用 Jev | 没有固定的决策；回答聊天模型在运行时编写的问题 | 已在 `2.8.31` 中发布，默认关闭 | Harness Engineering |
| [`Paca-AI/paca`](https://github.com/Paca-AI/paca) | 1,865 | 用于四项项目管理功能的 Go Jev 客户端，每个项目使用各自的密钥 | agent 路由、任务字段自动填充、任务负责人，以及自动化工作流分支 | 已发布，按项目需手动开启 | AI Automation Software |
| [`nimbalyst/nimbalyst`](https://github.com/nimbalyst/nimbalyst) | 1,787 | 对工作事件进行分拣的 alpha 版知识整理器，以及一个 `ask_jev` Model Context Protocol 工具 | 工作事件是否属于知识、它的 wiki 区域、目标条目，以及被推翻的声明 | 实验性，alpha | AI Automation Software |
| [`LLPhant/LLPhant`](https://github.com/LLPhant/LLPhant) | 1,710 | PHP 框架中用于类型化问题的 `JevClassifier` 类 | 应用直接提出的类型化问题，例如客户支持消息分诊 | 已发布，需手动开启 | AI Automation Software |
| [`theopenco/llmgateway`](https://github.com/theopenco/llmgateway) | 1,663 | TypeSafe 目录提供方、`/v1/systemone` 代理，以及网关流水线中的 Jev | 供内容过滤器使用的内容审核类别标记，以及供智能模型路由使用的请求难度；智能模型路由是托管网关的一项测试版功能，比最新版本更新 | 已发布，需手动开启 | Harness Engineering |
| [`antvis/AVA`](https://github.com/antvis/AVA) | 1,571 | 需手动开启的子集分析策略，使用 Jev 精简文本到 SQL 的上下文 | 哪些表和列的画像统计信息与用户问题相关 | 实验性，需手动开启 | Harness Engineering |
| [`agentconnect-md/agentconnect`](https://github.com/agentconnect-md/agentconnect) | 1,436 | TypeSafe 作为可复用 Decisions 功能背后唯一的提供方 | agent 激活、对话与代码托管平台路由、会话运行时与模型，以及仓库 | 需手动开启，仅在候选版本 `v1.61.0-rc.*` 标签中 | Harness Engineering |
| [`remorses/kimaki`](https://github.com/remorses/kimaki) | 1,424 | OpenCode 插件 `@kimaki/automode`，用 Jev 对待执行的 agent 工具调用进行把关 | 某个待执行的工具调用能否自动运行；出错时默认拒绝 | 已在 `0.2.0` 中发布，需手动开启 | Harness Engineering |
| [`mohitagw15856/pm-claude-skills`](https://github.com/mohitagw15856/pm-claude-skills) | 1,407 | 供应商中立的 Jev 决策层，带有零依赖客户端和托管 worker | 按设计：技能路由、输入防护、危机路由、决策契约和质量闸门。目前还没有真实的 Jev 答案：在 TypeSafe 注册关闭、Vercel AI Gateway 和 Cloudflare Workers AI 需要支付方式的情况下，由一个 Claude 适配器（`claude-haiku-4-5`）回答所有问题 | 已发布，需手动开启 | Harness Engineering |
| [`astaxie/TokenHub`](https://github.com/astaxie/TokenHub) | 1,343 | Jev Smart Routing 策略、TypeSafe 提供方插件，以及 `/v1/systemone` 网关端点 | 由管理员配置的哪个上游模型处理每个聊天或 Responses 请求 | 已发布，需手动开启 | Harness Engineering |
| [`heymrun/heym`](https://github.com/heymrun/heym) | 1,290 | 由 Jev 支撑的 Decision Model 凭据和 Decision 工作流节点 | 工作流分支答案、每个请求使用的大语言模型，以及评测评判分数 | 已发布，需手动开启 | AI Automation Software |
| [`caliber-ai-org/ai-setup`](https://github.com/caliber-ai-org/ai-setup) | 1,287 | 用于 agent 会话记录的 Jev 压缩插件和 `caliber compact` 命令 | 压缩时保留、截断或丢弃哪些工具调用和结果 | 已在 `1.54.0` 中发布，需手动开启 | Harness Engineering |
| [`databuddy-analytics/Databuddy`](https://github.com/databuddy-analytics/Databuddy) | 1,165 | 使用 Jev 进行分类的 `@databuddy/scan` 命令行工具和 insights 应用 | 每个代码片段的事件覆盖、产品类别和优先级；调查候选项预筛选 | 已发布，默认开启 | AI Map Reduce over Big Data |
| [`webbrain-one/webbrain`](https://github.com/webbrain-one/webbrain) | 1,120 | 开源浏览器 agent 中需手动开启的 Jev 辅助模型，固定为 `jev-1.13.0` | 报告的调度器成功是否真实，以及快速分类和实验性浏览器操作 | 已在 `36.8.0` 中发布，需手动开启 | Universal Verification |

## 8. 开放权重替代品与复刻

许多项目提供开放权重或自托管的模型，用来回答同样的 Choice、Score 和 Noul 问题，而且常常沿用同一个 `POST /v1/systemone` 线路格式，因此官方客户端库无需改动即可使用。其中 10 个是在完整分类之前选定的（第一轮研究点名的 6 个，以及代码搜索返回的 4 个大型复刻项目），并与能找到的每一项独立测量结果进行了核对。它们并非按 star 数排名的前 10：`vinnylarouge/jevlike`（1,318 个 star）和 `feder-cr/jev`（1,053 个 star）的 star 数都超过这 10 个中的 5 个；而 decider 在 JevBench 版本 `v1.4.2.2` 上排名高于 Jev，它是后来由完整性检查（第 12 节）找到的。这些测量结果来自 JevBench（fstandhartinger/jevbench，由 Benchmark Heaven 运行，包含 308 道封存题目）、jabr/classifier-benchmark（944 个锁定的合成用例，通过 OpenRouter 调用 Jev）、Jev Decision Index（一个 Hugging Face Space）、DecisionBench，以及博客中的测量结果。

这 10 个中有 9 个有公开的独立准确率结果，除一个例外之外，Jev 在每一项中得分都更高（对于 Kev 和 openJev-verdict-2.0，外部人员只测量了较旧或较小的模型，而不是主打的 Kev-27B 或 Verdict 2.0）。例外是：一次外部运行（Luni/laya-jev-benchmark）为在 LocalLLaMA/typed-decisions 数据集训练集划分上微调的 Laya 检查点复现出约 0.767，高于数据集作者为未经微调的 Jev 测得的 0.727，而数据集卡片说明这两种模式不可比较。NanoJev 没有与 Jev 的独立比较，而一份未发布的 JevBench 草稿（`v1.4.3`，未合并即关闭）将 JevK5 `v0.3` 排在 Jev 之上。Jev 在 jabr/classifier-benchmark 上的综合微平均准确率为 0.967，而最好的开放模型约为 0.75；Jev 在 JevBench 封存题目上为 36.7%（随机水平为 29.3%），而若干复刻项目的得分在随机水平或以下。JevBench 综合分还会权衡成本和速度，其版本 `v1.4.2.2`（GitHub，2026-09-27，91 个参与排名的系统）将三个开放模型（Imajev-4B、Plumb-4B 和 decider-4b v2）排在 Jev 之上，Jev 位列第 4；第 12 节确认了这一排名以及 decider 系列。除非某个单元格注明了其他 JevBench 版本，“独立测量”列中的每个排名都来自版本 `v1.4.2.1`（Jev 在 90 个参与排名的系统中位列第 3），这是 benchmarkheaven.com 排行榜在 2026-09-27 早些时候显示的版本。当天晚些时候，排行榜切换到 `v1.4.2.2`，该版本在榜首新增了 Imajev-4B，且所有分数不变，因此这些排名在实时排行榜上都各低一位。“相对 Jev 的主要声明”列中的排名是各项目对自身的说法。Jev Decision Index 的核心数字是基于 38 个基准组成的评测组合计算的机会校正后的技能分（Jev 为 57.91），而不是准确率；在该指数上，没有任何参赛者得分高于 Jev。

| 项目 | 是什么 | 相对 Jev 的主要声明 | 独立测量 | 结论 |
| --- | --- | --- | --- | --- |
| [`wfzyx/von`](https://github.com/wfzyx/von) | 基于 ModernBERT-large 的微调模型，约 395 百万个参数，非自回归编码器，为每个选项的一个掩码标记打分。 | 低于 15 毫秒、可在本地直接替换 Jev 的替代品；ViZDoom 击杀数 9.00 对 Jev 的 5.62；约 18 毫秒对 115 毫秒。 | Benchmark Heaven 的 JevBench：27.5（第 47 名）对 Jev 的 63.3（第 3 名）；封存准确率 27.9%，低于随机水平。jabr：0.742 对 0.967，其中第 1 版的 78 个用例中有 48 个出现在训练数据中。 | 独立证据推翻其声明 |
| [`allebee/jevk5`](https://github.com/allebee/jevk5) | Qwen3.5-4B 加上合并的秩为 16 的低秩适配；在一次前向传播中读取答案字母的 logits，不生成 token。 | README 称，JevBench 1.4 版将 0.2 版排在 76 个系统中的第二、开放参赛者中的第一，得分 62.04 对 Jev 的 63.29（Jev 第一）；封存准确率 33.1% 对 36.7%。 | JevBench 评测方的重新运行确认了 62.04 对 63.29，以及封存 33.1% 对 36.7%。multimodalart 的 Jev Decision Index：技能分 38.81 对 Jev 的 57.91；Jev 在每个类别中都领先。 | 独立证据支持其声明 |
| [`Heman10x-NGU/openJev-verdict-2.0`](https://github.com/Heman10x-NGU/openJev-verdict-2.0) | ModernBERT-base，149.6 百万个参数，按选项的掩码标记头，在 typed-decisions 训练集划分上进行监督微调。 | Verdict 2.0 在 typed-decisions 上胜过 Jev 1.13.0：top-1 准确率 77.10% 对 72.70%，Brier 0.0636 对 0.1480。项目自己绘制的排行榜图片显示 1.4 版在 JevBench 上排名第二，74.9 对 Jev 的 75.4。 | 项目之外没有人测量过 Verdict 2.0；其权重无法下载，且数据集卡片说明，微调的专用模型与零样本的 Jev 不可比较。Benchmark Heaven 将 1.4 版排在第 59 名（19.0 对 Jev 的 63.3）；Hanno-Labs DecisionBench：第 1 版为 0.284 对 0.720。 | 独立证据推翻其声明 |
| [`Heman10x-NGU/Verdict-open-jev`](https://github.com/Heman10x-NGU/Verdict-open-jev) | ModernBERT-base 加 GLiClass 双编码器头，151 百万个参数，在 Banking77 和 CLINC150 数据上进行监督微调。 | 项目自己绘制的排行榜图片显示 1.4 版在 JevBench 上排名第二，74.9 对 Jev 的 75.4，决策耗时低于 35 毫秒。 | Benchmark Heaven：19.0，排名第 59，对 Jev 的 63.3，排名第 3；封存准确率 27.9%，低于随机水平。Hanno-Labs DecisionBench：28.43% 对 72.03%。umstek 的 `zero-shot-ie-bench`：39.6% 对 93.8%。 | 独立证据推翻其声明 |
| [`TheoLeeCJ/SemIf-OpenJev`](https://github.com/TheoLeeCJ/SemIf-OpenJev) | 没有训练，也没有权重；对冻结的 Qwen3.5-4B 进行提示，并在一次前向传播中读取答案字母的 logits。 | 在 102 行的 TypeSafe 公开子集上，一致率为 0.845 对 Jev 的 0.883；并称这并不能证明其具备接近 Jev 的能力。 | Benchmark Heaven JevBench：第 12 名，47.7，对 Jev 的第 3 名，63.3；封存 26.3% 对 36.7%。JevBench 1.2 版：困难档 59.5% 对 74.1%。 | 独立证据支持其声明 |
| [`logan-markewich/jeff`](https://github.com/logan-markewich/jeff) | 在第三方零样本模型 GLiFormer-large（575.6 百万个参数）上实现 Jev 线路格式的服务器；没有微调，只拟合了一个温度参数。 | 可自托管、可直接替换 Jev，更便宜但准确度更低：每百万次请求约 $2.6 对 $15.6；JevBench 版本 `v1.2.2`：66.9 对 75.3。 | JevBench 维护者，版本 `v1.2.2`：66.9（18 个中排名第 9）对 Jev 的 75.3（排名第 2），困难档 37.7% 对 74.1%。在版本 `v1.4.2.2` 中重新运行：30.58，排名第 42/91，对 Jev 的 63.29，排名第 4。jabr：0.563 对 0.967。mandu5 jevcompat：32 项必需检查中通过 30 项。 | 独立证据支持其声明 |
| [`NandhaKishorM/laya`](https://github.com/NandhaKishorM/laya) | ModernBERT-large 编码器加一个两层决策头，421 百万个参数，在掩码标记处为每个选项打分。 | 在 typed-decisions 上胜过 Jev，0.766 对 0.727；声称预期校准误差为 0.081 对 0.246，且速度快 7.8 倍。 | 一次外部运行（Luni/laya-jev-benchmark）测得，在 LocalLLaMA/typed-decisions 训练集划分上微调的 Laya 检查点得分为 0.767，复现了该声明，高于 typed-decisions 数据集作者在未微调的情况下为 Jev 测得的 0.727；typed-decisions 数据集卡片说明这两种模式不可比较。每一次配对运行都发现 Jev 领先：jabr 0.588 对 0.967（微调检查点为 0.625），Benchmark Heaven 排名第 42 对第 3，Jev Decision Index 6.04 对 57.91。只有在图形处理器上才更快。 | 独立证据推翻其声明 |
| [`jaredpalmer/kev`](https://github.com/jaredpalmer/kev) | 在 Qwen 基座模型上使用秩为 16 的低秩适配器加一个指针头，参数量为 0.8 到 27 十亿。 | 在新来源迁移测试集上，Kev-27B 与 Jev 相差不到一分，0.848 对 0.857；Kev-4B 和 Kev-9B 相差不到四分。 | multimodalart 的 Jev Decision Index：Kev-9B 为 38.48，Kev-4B 为 34.64，对 Jev 的 57.91。JevBench 团队，较早的预览版：困难档 0.42 到 0.47 对 0.74。没有人测量过 Kev-27B。 | 独立证据推翻其声明 |
| [`TianyuCodings/NanoJev`](https://github.com/TianyuCodings/NanoJev) | 对 Qwen3-0.6B 进行全量微调，并增加选择、布尔和评分头，在四个游戏任务上训练。 | 在 ViZDoom Basic 上胜过 Jev，128/128 对 56/128，在 Predict Position 上为 27 对 11（共 128 个）。 | 没有第三方将 NanoJev 与 Jev 进行比较；jabr 和 JevBench 都没有收录它。第三方只检查了延迟和文件完整性。录像显示 Jev 在每一个 Basic 步骤都开枪。 | 仅有自报结果 |
| [`bespokelabsai/nimble`](https://github.com/bespokelabsai/nimble) | 在 Qwen3.5-9B 上使用秩为 16 的低秩适配器；对单 token 答案代码的 logits 做 softmax，不生成文本。 | 在该项目 324 题的留出集上 Jev 领先，93.21% 对 90.12%；在一个公开测试集上也领先，76.0% 对 74.8%。 | Benchmark Heaven JevBench：18.7，排名第 61，对 Jev 的 63.3；公开题 79.7% 对 86.6%，封存题目 28.9% 对 36.7%。原始延迟中位数 0.39 秒对 0.65 秒。 | 存在争议：调查者和 3 位质疑审阅者中的 1 位判定独立证据推翻其声明；另外 2 位质疑审阅者判定其声明得到支持，因为该项目自己也说 Jev 领先 |

最终标签共将 1,136 个仓库标记为替代或复刻模型。其中大多数规模很小，且没有独立测量。

## 9. 关于 Jev 的第三方研究与发布讨论

本节涵盖 6 个来源：5 项第三方研究，以及 Hacker News 上的发布讨论帖，TypeSafe 员工也参与了该讨论。其中两项研究没有调用 Jev：Andrew Yourtchenko 的文章从 jabr 的运行中照搬了 Jev 的数据，而 SemIf 的结果来自 TypeSafe 自己公开的评测记录。jabr 基准起源于竞争复刻项目 Von 的问题跟踪器，而 SemIf 的作者正在构建一个作为竞品的开放复现。

**PriorBench**（github.com/priorbench/jev，化名作者，2026-09-20）。这是一项通过 OpenRouter 进行的黑盒研究，作者称其为预注册研究：21 个实验，5,721 次调用，总成本 $0.176。预注册文件、代码和全部原始数据都在同一次提交中出现，因此没有人能检查预测是否先于数据产生。在作者的法语 4 类别基准上，零样本准确率为 95.9%，而关键词方法为 77.2%，有监督的词频基线为 66.0%。置信度能对答案排序，但未经校准：阈值以上的准确率在 0.50 到 0.95 之间保持平稳，只有在 0.99 时才达到 100%，而该阈值覆盖 60.2% 的流量。Jev 总是给出回答，即使输入是空字符串也是如此；除非问题提供 "other" 选项，否则它会以 0.99 的置信度把超出范围的消息强行归入某个类别。故意写错的标准描述使准确率降到 16.7%，低于 25% 的随机下限。接近 0.5 的 Noul 值不可复现（60 次相同调用中为 0.46 到 0.54）。在供应商文档列出的失败模式中，该研究证实了两种：计数（在含相似干扰项的 40 道题上为 48%）和对标准的字面理解（即上文的 16.7% 结果）。数字和日期比较（520 道中答对 518 道）以及否定的表现好于供应商文档的说法。从西欧测得的延迟中位数为 475 毫秒，一次调用中的 800 个问题耗时 985 毫秒。

**Rajesh Beri, beri.net**（2026-09-20，一项二次分析）。它综合了多项研究。在一个钓鱼邮件基准上，单个 "is this phishing" 问题得分为 62.6%，而 Claude Haiku 4.5 为 81.3%；但在一次调用中提出五个窄问题，再加上一个逻辑回归，拟合用 1,000 封已标注邮件，评分用其余的 1,000 封，达到 95.0% 对比 93.2%，这一差距在统计上不显著（p = 0.063）。单个问题时 Jev 的成本比 Haiku 低 12 倍，五个问题时低 27 倍，其延迟中位数约低 2.9 倍。一项分布外校准研究在新生成的合成工单上测得预期校准误差为 0.107，其中 Noul 回答置信不足，Choice 和 Score 回答过度自信；在一条不在工单文本中的规则上，平均声明概率为 0.74 时准确率为 44.7%。文章指出，供应商的主要声明衡量的是与两个前沿模型平均结果的一致程度，"0% hallucination" 是输出结构（schema）层面的保证，而且发布文章没有公布任何校准指标，既没有预期校准误差，也没有可靠性曲线。

**jabr/classifier-benchmark**（起源于 wfzyx/von 的问题 3，2026-09-19 至 2026-09-27）。944 个锁定的合成用例：原始 8 任务测试集中有 78 个，新的 49 任务测试集中有 866 个。每个模型得到的问题 JSON 都相同。Jev：综合微平均准确率为 0.967，其中原始测试集上为 0.974，新测试集上为 0.967，下降不到 1 分；平均每个用例约 330 毫秒（含网络）。最好的本地模型达到 0.747（GLiNER2.5-Decide）和 0.742（Von 1.2）。误差分析指出了 Jev 的三种失败模式：它会对临界的 "no" 案例过度标记（例如密钥泄露上为 0.875），它在有序量表上的失误通常只差一个等级（有两次失误差了两个等级），以及它会混淆近义类别。

**Andrew Yourtchenko，"Three Jev clones and a 27B"**（2026-09-20）。作者在四个本地模型上运行了 jabr/classifier-benchmark 的 78 用例原始测试集，但没有调用 Jev。文中的 Jev 数据照搬自上文的 jabr 运行，因此这篇文章并不是对 Jev 的独立测量。对比 Jev 的 0.974 分，本地运行的得分为：一个 1 比特、27 十亿参数的对话模型 0.885，GLiNER2 0.795，Von 0.769，Laya 0.590。在那次运行中，Jev 唯一的弱项是一个有序严重程度评分细则（0.778），而 Von 在该任务上得分为 1.000。

**Hacker News 发布讨论帖**（条目 49717558，2026-09-15，1,984 分）。评论者批评了供应商的评测方法（与前沿模型的一致程度，而不是真实标签）、公开基准结果的缺失，以及 "0% hallucination" 图表。上手体验报告：一个浏览器 agent 以约 $0.001 的成本做出了 21 到 23 个正确决策，一个国际象棋演示走出了弱棋，代码生成失败，这对于一个不生成文本的模型来说在意料之中。TypeSafe 员工表示没有提示词缓存，并且输出不能是字符串。

**SemIf 结果**（TheoLeeCJ/SemIf-OpenJev，2026-09-18）。在从 TypeSafe 公开评测记录中选出的 102 行子集上，已公布的 Jev 答案与参考答案的一致率为 0.883，而直接读取一个开放的 4 十亿参数模型的结果为 0.845。作者表示，这并不表明其具备接近 Jev 的能力。

## 10. 社区列表

下面的 14 个列表被用作来源。“链接的仓库”一列统计每个列表所链接、且仍然存在并已完成分类的 GitHub 仓库。“与 Jev 相关”一列统计其中最终标签为与 Jev 相关的仓库。

| 列表 | 链接的仓库 | 与 Jev 相关 |
| --- | ---: | ---: |
| [`kydlikebtc/awesome-jev`](https://github.com/kydlikebtc/awesome-jev) | 1,124 | 1,094 |
| [`hellogumbo/awesome-jev`](https://github.com/hellogumbo/awesome-jev) | 1,001 | 996 |
| [`heyjunpenn/awesome-jev`](https://github.com/heyjunpenn/awesome-jev) | 892 | 866 |
| [`yibie/awesome-jev`](https://github.com/yibie/awesome-jev) | 378 | 366 |
| [`valentynkit/awesome-jev-typesafe`](https://github.com/valentynkit/awesome-jev-typesafe) | 330 | 328 |
| [`cobanov/awesome-jev`](https://github.com/cobanov/awesome-jev) | 222 | 220 |
| [`AbdelStark/awesome-typesafe-jev`](https://github.com/AbdelStark/awesome-typesafe-jev) | 217 | 210 |
| [`walidboulanouar/awesome-jev-use-cases`](https://github.com/walidboulanouar/awesome-jev-use-cases) | 193 | 189 |
| [`AnotiaWang/awesome-jev`](https://github.com/AnotiaWang/awesome-jev) | 163 | 162 |
| [`kraayenjon/awesome-jev`](https://github.com/kraayenjon/awesome-jev) | 160 | 159 |
| [`fatwang2/awesome-jev`](https://github.com/fatwang2/awesome-jev) | 158 | 154 |
| [`v-modal/awesome-jev-tools`](https://github.com/v-modal/awesome-jev-tools) | 150 | 144 |
| [`OmniJev/awesome-jev-gallery`](https://github.com/OmniJev/awesome-jev-gallery) | 112 | 93 |
| [`Anil-matcha/awesome-jev-by-typesafe`](https://github.com/Anil-matcha/awesome-jev-by-typesafe) | 95 | 89 |

这些列表合计链接了 1,713 个已完成分类的不重复仓库。其中 1,631 个与 Jev 相关，占本次调查发现的 14,742 个相关仓库的 11.1%。其余仓库中，12,647 个由 GitHub 仓库搜索找到，另外 464 个只通过 GitHub 代码搜索找到。

## 11. 生态中的风险与注意事项

- **账户注册工具。** 最终标签把 9 个仓库归入账户工具类型。其中 6 个用于自动完成 TypeSafe 注册、账户注册或密钥创建（例如 `Futureppo/typesafe_register`、`2951461586/Jev-Register-Tool` 和 `dengyie/ai-register-machine`）。另外 3 个分别是一个转售 Jev 访问权限的商店、一个监控密钥到期和额度的工具，以及一个空的占位仓库。通过批量注册获取更多免费用量，很可能违反 TypeSafe 的服务条款。本报告不描述这些工具的工作方式。
- **容易混淆的组织。** `typesafe-ai` 是官方组织，而 `TypeSafeAI` 是一个非官方社区组织，两者名称只差一个连字符。14 个社区列表中有 4 个链接了它的仓库，但没有一个把这些仓库列为官方仓库。在把某个仓库当作官方仓库之前，请先检查其所有者。
- **自报数字。** 许多 README 公布了与 Jev 的基准对比，但这些对比站不住脚。本次调查发现的例子包括：以基准名义展示的脚本化模拟数值，并未实际测量 Jev（`TypeSafeAI/jev-harness`，该仓库自己披露了这一点）；在某个基准自身的训练集划分上微调的专用模型，与一个零样本 Jev 分数对比，而数据集卡片称该分数不可比；以及由项目自己绘制的排行榜图片（`Heman10x-NGU/Verdict-open-jev`）。
- **校准。** 两项独立研究，PriorBench 和 jabr/classifier-benchmark，发现 Jev 的置信度能很好地为其答案排序，但作为概率并不严格：jabr 称这些概率 "directionally right but not tight"；在 PriorBench 自己的基准上，当阈值从 0.50 升到 0.95 时，高于阈值部分的准确率保持平稳。本次调查中唯一一项分布外校准研究（scienthoon，第 12 节）在全新的合成工单上测得预期校准误差为 0.107，是其噪声下限的 4.4 倍，而可能出现在训练数据中的公开基准则显示校准良好。只有 PriorBench 测试了没有任何选项适用的输入，而 Jev 仍然给出了自信的回答。生产环境闸门需要一个明确的 "other" 或 "insufficient evidence" 选项，以及在目标数据上调整过的阈值。
- **供应商声明。** TypeSafe 公布了自己的评测记录，其中包含 Jev 的回答（第 8 节和第 9 节中的 SemIf 对比使用了其中 102 条），但它没有公布任何公开第三方基准上的结果，也没有公布任何校准指标。其主打的速度与成本倍数是基于一致率把 Jev 与生成式模型进行比较，而不是基于相对真实标签的准确率。
- **运营。** 文档称，在 TypeSafe 扩充容量期间，速率限制可能在不另行通知的情况下变化，并且新版本发布时 `jev-latest` 别名会随之指向新版本。在调整阈值时，请固定使用 `jev-1.13.0`。第 12 节列出了关于暂停注册、请求丢弃、状态页和客户协议的已核实报告。
- **提示词注入。** state 是数据，TypeSafe 表示 Jev 默认不会把它视为恶意内容。第 12 节列出了一篇关于针对类型化决策的提示词注入攻击的预印本。对不可信文本采取行动的闸门需要自己的防御措施。

## 12. 完整性检查发现的其他条目

三位完整性审阅者搜索了之前几轮遗漏的条目。每个条目都由一名调查者阅读，并由两位质疑审阅者核查。下面的摘要只使用两位质疑审阅者都确认过的事实，但有三个例外。OpenRouter 一行把 OpenRouter 的上下文长度与第 1 节中的 TypeSafe 限制进行比较，这些限制来自 TypeSafe 文档。LanceDB 一行加入了本次调查自己对 `lancedb/lancedb` 的代码搜索结果、star 数和最终标签。Ollaya 一行加入了第 8 节核查中的一个事实：Jev 的 0.727 是由 typed-decisions 数据集的作者测得的。

### 分发合作伙伴

| 条目 | 日期 | 摘要 |
| --- | --- | --- |
| [Vercel：Jev 是 AI Gateway 历史上采用最快的模型](https://vercel.com/blog/ai-gateway-jev-model-launch) | 2026-09-18 | Vercel 博客文章，日期为 2026-09-18。在 24 小时内，将近 13% 的 AI Gateway 付费团队使用了 Jev，是 GPT-5.6 系列的 2 倍，是 Fable 5.1 的 6 倍以上。Vercel 指出，下一个考验是这种采用能否持续。 |
| [OpenRouter 关于使用 Jev 的文档中心](https://openrouter.ai/docs/guides/community/jev) | 2026-09-24 | 模型 `typesafe/jev-1.13`（别名 `~typesafe/jev-latest`）向任何持有 OpenRouter 密钥的用户开放，无需候补名单。OpenRouter 给出的上下文为 32,000 个 token，这与 TypeSafe 对 state 加最长问题的限制一致，而不是每次请求 64,000 个 token 的限制（第 1 节）。输入 token 按每个 $0.000000042 计费，输出免费。有两个端点：Decisions 和 System One。 |
| [DigitalOcean Serverless Inference 新增 Jev](https://ideas.digitalocean.com/changelog/now-available-jev-from-typesafe-ai) | 2026-09-23 | 2026-09-23 发布的更新日志条目使 Jev 可以通过 DigitalOcean Serverless Inference 使用，价格为每十亿输入 token $42，输出免费。文档列出的模型标识符为 `typesafe-jev-1.13.0`，别名 `typesafe-jev-latest` 和 `typesafe-jev-preview` 都指向它。 |
| [Cloudflare 人工智能模型目录收录 Jev](https://developers.cloudflare.com/ai/models/typesafe/jev/) | 2026-09-17 | 目录条目 `typesafe/jev` 创建于 2026-09-17，标注为 Third-party 和 Zero data retention。价格为每百万输入 token $0.042，输出免费，另加 5% 的 Unified Billing 额度手续费。截至 2026-09-27，没有任何 Cloudflare 更新日志订阅源提到它。 |
| [Vercel AI Gateway 的 Jev 请求因 HTTP 429 失败](https://community.vercel.com/t/typesafe-ai-jev-requests-shed-with-429-and-providerattemptcount-0-per-team-throttling/49779) | 2026-09-26 | 2026-09-26 的一篇无人回复的论坛报告：在 2026-09-25 成功超过 1,500 次之后，几乎每个发往 `/v1/evaluate` 的 `typesafe-ai/jev` 请求都返回 HTTP 429，并带有 `providerAttemptCount: 0`。9 月 19 日的一份 Vercel Jev 页面存档显示，免费促销价格于 9 月 25 日结束。 |

### pull request 中的集成

| 条目 | 日期 | 摘要 |
| --- | --- | --- |
| [Langfuse pull request 新增实验性 Jev 决策模型评估器](https://github.com/langfuse/langfuse/pull/17733) | 2026-09-22 | pull request 17733 于 2026-09-22 合并，改动了 110 个文件，新增 6,322 行，这些改动由 `decisionModelEvaluators` 开关控制。只有机器人审阅者审阅过它，也没有测试过任何实际的 TypeSafe 调用。后续的 pull request 17798 加入了 Vercel AI Gateway 和 OpenRouter 上游。 |
| [面向 TypeSafe Jev 的 OpenInference 插桩包](https://github.com/Arize-ai/openinference/pull/3773) | 2026-09-18 | 于 2026-09-18 合并。`TypeSafeAIInstrumentor` 把来自 `typesafe-sdk` 0.6.0 或更高版本的 `system_one` 调用记录为 OpenInference 大语言模型 span。版本 0.1.1 当天发布到 Python Package Index；pypistats 统计到在 2026-09-27 之前的 30 天内下载 698 次。功能 issue #3769 仍未关闭。 |
| [Microsoft Agent Framework 面向 .NET 的 TypeSafe Jev 提供方](https://github.com/microsoft/agent-framework/pull/8563) | 2026-09-20 | 由外部贡献者 joslat 于 2026-09-20 创建的一个未合并的开放 pull request：54 个文件，新增 5,404 行，包含一个实验性的 `IDecisionClient` 和一个预览版 `Microsoft.Agents.AI.TypeSafe` 包。构建工作流尚未运行。评论者 mo3in 希望把该提供方放进 `Microsoft.Extensions.AI`；作者表示同意。 |
| [Apache Airflow 支持 TypeSafe Jev 分类器模型](https://github.com/apache/airflow/pull/73363) | 2026-09-20 | Kaxil Naik 于 2026-09-20 合并：9 个文件，没有提供方代码，包含一个示例有向无环图，以及一个要求 `typesafe-sdk>=0.6.0` 的 `typesafe` 可选依赖（extra）。该可选依赖出现在 2026-09-24 上传的 `apache-airflow-providers-common-ai` `0.10.0rc1` 中；其测试 issue 尚未把任何一项标记为已测试。 |
| [Bifrost 网关的 TypeSafe 提供方与 decisions 端点](https://github.com/maximhq/bifrost/pull/7355) | 2026-09-21 | 于 2026-09-21 合并：179 个文件，新增 5,920 行，包含一个 `typesafe` 提供方和 `POST /v1/decisions`（对应 TypeSafe 的 System One 端点），以及一个静态 Jev 目录，其中列出 `jev-1.13.0` 及其两个别名 `jev-latest` 和 `jev-preview`。已于 2026-09-22 在 core `v1.10.0` 中发布，并于 2026-09-23 在 HTTP transports `v2.2.2` 中发布。 |
| [LanceDB 的 TypeSafe 重排序器及后续请求批处理](https://github.com/lancedb/lancedb/pull/4209) | 2026-09-17 | 于 2026-09-17 合并，距创建仅 26 分钟。`TypeSafeReranker` 用一个是或否的 Jev 问题为每个结果打分。2026-09-24 合并的 pull request 4316 加入了批处理：批大小为 40 时，6,851 次测试调用减少到 199 次，延迟中位数从 877 毫秒降到 351 毫秒。GitHub 代码搜索没有返回这个仓库，其 README 既未提到 Jev 也未提到 TypeSafe，因此最终标签把 `lancedb/lancedb`（11,541 个 star）标记为与 Jev 无关。它不计入第 5 节和第 6 节的统计，也不在第 7 节的项目之列。 |

### 更多开放模型与复刻

| 条目 | 日期 | 摘要 |
| --- | --- | --- |
| [Mapika 的 decider 开放权重决策模型系列](https://github.com/Mapika/decider) | 2026-09-16 | 采用 Apache-2.0 许可、对 TypeSafe AI 的 System One 模型类别的开放复现，与 TypeSafe AI 无关联，基于 Qwen3.5 基座模型构建。在 JevBench `v1.4.2.2` 上，decider-4b v2 以 64.1 排名第 3，高于 Jev 1.13.0 的 63.3。 |
| [OpenJev：带有 Jev 兼容层的开放权重决策模型](https://huggingface.co/openjev/openjev) | 2026-09-20 | 基于 `Qwen/Qwen3.8-27B` 的微调版本，共 27,356,728,560 个参数，权重采用 Creative Commons Attribution-NonCommercial 4.0 许可。其自己的模型卡片报告称，在 10,000 道未公开的问题上，托管版 Jev 得分 85.4%，OpenJev 为 84.0%，未微调的基座模型为 80.4%。该项目在开发过程中使用了其中 3,078 道问题，而且模型卡片没有注明 Jev 版本和运行日期。它提供一个 `/v1/systemone` 兼容层。 |
| [jevlike：小型单遍选项评分入门模型](https://github.com/vinnylarouge/jevlike) | 2026-09-16 | 独立的入门模型，输入和输出形式与 Jev 相同，2026-09-27 时有 1,318 个 star。默认的字节评分器有 41,280 个可训练参数。该项目表示，它并未证明自己与 Jev 质量相当。4 个 pull request 均未合并。 |
| [AnyJev：把开放语言模型变成 Jev 风格的决策模型](https://github.com/nokia-applied-research/AnyJev) | 2026-09-21 | 一个 Python 库，通过开放模型的一次预填充，从其下一个 token 的分布中读出决策，无需训练模型权重。它与 Nokia 的关联是自我声明的，GitHub 组织 `nokia-applied-research` 未经验证。在 Qwen3-8B 的 BANKING77（300 个测试题目）上，准确率从原始的 0.747 提升到无标签时的 0.803，以及使用 100 到 500 个标签时的 0.807。标签的主要作用是降低预期校准误差，从 0.184 降到 0.095。 |
| [Ollaya：面向开放决策模型的本地服务器](https://github.com/ollaya-dev/ollaya) | 2026-09-23 | 一个 Rust 服务器，声称提供线路格式完全一致的 TypeSafe 端点，从 2026-09-23 到 2026-09-27 共发布 13 个版本。在 Ollaya 自己的运行中，推荐的 `winnow:e4b` 在 typed-decisions 上得分 0.722，而 Winnow 作者通过 OpenRouter 运行 Jev 1.13 得到的 Jev 分数为 0.738。typed-decisions 数据集的作者在同样的 2,000 个决策上测得 Jev 为 0.727，第 8 节使用的就是这个数字。它与 TypeSafe 无关联。 |
| [Simple Jev：Featherless 推出的把开放模型变成分类器的服务器](https://github.com/featherless-ai/simple-jev) | 2026-09-18 | 读取允许的答案标签对应的下一个 token 的 logits，服务地址为 `/v1/classifier`，并提供 `/v1/systemone` 别名。在 Decision Index 0.2.1 上，Qwen3.8-27B 条目以 55.74 在 68 个中排名第 4，而 Jev 为 57.91。预期校准误差为 0.113 对 0.074。 |
| [Together AI 的 Tev1-4B-experimental：受 Jev 启发的开放权重模型](https://github.com/togethercomputer/tev1) | 2026-09-23 | 在 Qwen3.5-4B 上用 37,840 个训练样本微调，没有使用任何来自 Jev 的数据。它保留了 Qwen 的标准下一个 token 输出头。在 2,000 个钓鱼题目上，它得分 50.6%，而 Jev 为 62.9%；在工单路由上，它在 27 个中答对 26 个，Jev 则答对 27 个。 |

### 更多基准与评测

| 条目 | 日期 | 摘要 |
| --- | --- | --- |
| [Jev Decision Index 社区排行榜，0.2.1 版](https://huggingface.co/spaces/multimodalart/jev-decision-index) | 2026-09-27 | 非官方的社区 Hugging Face Space，0.2.1 版，日期为 2026-09-27，针对 `jev-1.13.0` 测量。Jev 机会校正后得分为 57.91；排名最高的参赛模型 Surogate Rune 26B-A4B v3 得分为 57.44。没有参赛模型得分高于 Jev。托管版 Jev 的延迟中位数为 524.1 毫秒。 |
| [Benchmark Heaven 的 JevBench 基准，版本 `v1.4.2.2`](https://github.com/fstandhartinger/jevbench) | 2026-09-27 | 个人业余基准，与 TypeSafe AI 无隶属关系。版本 `v1.4.2.2` 的日期为 2026-09-27，列出 95 个系统，并对其中 91 个进行排名。Jev 1.13.0 以 63.29 分排名第 4，落后于得分 67.37 的 Imajev-4B。Jev 在公开题目上的准确率为 86.6%，在封存题目上为 36.7%。 |
| [scienthoon 的 Jev 校准研究](https://github.com/scienthoon/jev-ood-calibration) | 2026-09-19 | 该研究通过 Vercel AI Gateway 发出了 4,621 次调用。在 900 道合成题目上，Jev 的准确率为 75.1%，预期校准误差为 0.107，是 0.024 噪声下限的 4.4 倍。2026-09-22 的一次更正把重新拟合的 Choice 温度从 3.29 降到 1.30。 |
| [Decision Hijacking：针对 Jev 决策的提示词注入攻击](https://arxiv.org/abs/2609.28613) | 2026-09-23 | 南洋理工大学未经评审的预印本，2026-09-23，在 510 个 InjecAgent 案例上测试 `jev-1.13.0`。原始攻击在 1.8% 的案例中选中了攻击者的工具；优化后的攻击达到 3.5%。Jev 没有返回任何未声明的动作，但注入的文本改变了概率。 |

### 风险、政策与运营

| 条目 | 日期 | 摘要 |
| --- | --- | --- |
| [Futureppo 的 TypeSafe 账户批量注册脚本](https://github.com/Futureppo/typesafe_register) | 2026-09-21 | Python 脚本，创建于 2026-09-21，用于批量注册 TypeSafe 控制台账户并创建密钥。2026-09-27 时有 126 个 star 和 47 个 fork。该仓库创建约 17 小时后，TypeSafe 暂停了 Jev 的新用户注册。 |
| [jev-accounts-hub：把大量 TypeSafe 账户汇集到一个网关后面](https://github.com/antTing/jev-accounts-hub) | 2026-09-21 | Go 语言仓库，创建于 2026-09-21，3 个 star，把大量 TypeSafe 账户汇集到一个网关后面。TypeSafe 客户协议禁止共享凭据，也禁止额外的促销额度账户。 |
| [TypeSafe 暂停 Jev 新用户注册](https://jevainews.com/news/typesafe-signups-paused/) | 2026-09-22 | 非官方的 Jev News 摘要。TypeSafe 在 2026-09-22，即取消候补名单两天之后，暂停了 Jev 新用户注册，现有账户仍可正常使用。2026-09-23，Diogo Almeida 表示有人滥用注册来绕过速率限制。未见官方重新开放注册的公告。 |
| [TypeSafe AI 主客户协议，9 月 23 日更新](https://typesafe.ai/legal/mca) | 2026-09-23 | 更新于 2026-09-23。它禁止蒸馏、竞争产品、共享凭据和额外的促销额度账户。责任上限：12 个月费用与 $50 两者中的较高者。有一份副本存档于 2026-09-22，与之相比，唯一的实质性变化是第 2.3(l) 条，该条引用了《可接受使用政策》。该条款是恢复的，不是新增的：另一份副本存档于 2026-09-20，落款日期同样是 9 月 19 日，它包含该条款，但链接较旧；而 2026-09-21 和 2026-09-22 存档的副本不包含该条款。 |
| [Wunderlandmedia 对 TypeSafe Jev 合同的法律审查](https://wunderlandmedia.com/typesafe-ai-jev-terms-of-service-gdpr) | 2026-09-18 | Kemal Esensoy 于 2026-09-18 对 TypeSafe 四份法律文件进行审查，指出了禁止发布基准结果、不受限制的遥测数据处理以及 $50 的最低责任上限。9 月 23 日版协议没有禁止发布基准结果的条款，但保留了遥测和责任条款。 |

### 其他

| 条目 | 日期 | 摘要 |
| --- | --- | --- |
| [TypeSafe AI 状态页](https://status.typesafe.ai) | 2026-09-27 | Better Stack 页面，2 个监控项。在 90 天内，应用程序接口的可用率为 99.827%，其中 25 天出现停机，合计约 3.7 小时。全部 4 份事故报告的日期都在 2026-09-21 至 2026-09-24 之间；7 月和 8 月虽有停机，却没有列出任何事故报告。 |
| [the-jev-enator：针对 Jev 故障的断路器提案](https://github.com/jakenbear/the-jev-enator/issues/38) | 2026-09-25 | 2026-09-25 提出、仍未关闭的 issue，针对由 Jev 支撑的 Claude Code 钩子：每次受闸门控制的工具调用在以放行方式失败之前可能等待 12 秒，约为 425 毫秒第 95 百分位延迟的 30 倍。pull request #47 把 12 秒设为硬性截止时间，但没有加入断路器。 |
| [awesome-jev-by-typesafe，一个被改作他用的社区列表](https://github.com/Anil-matcha/awesome-jev-by-typesafe) | 2026-09-17 | Anil Matcha 的仓库，曾名 Chat-Youtube 和 awesome-claude-fable-5，于 2026-09-17 改为 Jev 内容，并保留了原有的 star：6 月时为 226 个，到 2026-09-25 为 846 个。它的 README 称其为非官方列表，但没有提及这段历史。 |

## 13. 本调查的局限

- 覆盖范围取决于 GitHub 搜索、GitHub 代码搜索和 14 个社区列表。代码搜索只索引默认分支。私有仓库、GitHub 之外的仓库，以及从未提及 Jev 或 TypeSafe 的项目都不在数据中。
- 415 个既没有 README 也没有描述的仓库，以及 999 个创建于 2026 年 8 月之前、仅因名称或描述中含有 "jev" 一词而匹配的仓库，未经分类即被排除。
- 审阅者是语言模型 agent，不是人。它们读取与 Jev 相同的元数据和 README，因此一份具有误导性的 README 会同时误导三者。共识是比任何单一标签都更好的估计，但它不是真实标签。
- 用例地图的五个顶层类别相互重叠（工具调用闸门既属于 Harness Engineering，也属于 Universal Verification），因此类别标签在所有字段中一致性最低，在两位审阅者之间也是如此。
- Jev 对相关性的判断偏保守：它把许多只是简短提及 Jev、模仿其接口或通过 OpenRouter 调用它的项目标为无关。因此，所有被 Jev 判为无关的仓库都经过了复核，最终计数已包含这些更正。
- 标签描述的是每个项目自称做了什么。只有第 7 节中的集成和第 12 节中的 6 个集成 pull request 对照其代码进行了检查。只有第 8 节中的 10 个复刻对照了所有能找到的独立测量结果进行了检查。两位质疑审阅者对照每个条目所引用的来源，逐一检查了第 12 节中的每个条目，包括其中的 7 个复刻。
- 每个 state 包含最多 60,000 个字符的清理后 README，而不只是每个问题所需的段落。TypeSafe 文档警告说，当 state 包含大量无关细节时准确率会下降，因此这是以部分准确率换取覆盖面。更长的 README 被截断，超出 token 上限的 state 又被进一步截断，因此 Jev 没有读到截断点之后的文本。
- 决策形态标签仅来自 Jev。
- Jev 公开发布约两周（自 2026-09-15 起），14,742 个相关仓库中有 12,530 个（85.0%）创建于这段时间内。star 数、版本和仓库数量每天都在变化。

## 14. 数据

[`data/jev-ecosystem-classification.csv`](data/jev-ecosystem-classification.csv) 为每个已分类的仓库保存一行：star 数、创建日期、是否与 Jev 相关、最终的类型、类别和用例、Jev 的标签及置信度，以及最终标签的来源（`label_source`）。值 "source code verification" 标记第 7 节的 104 个大型集成：每个集成是否与 Jev 相关及其类别，都来自对其源代码的阅读。其中 101 个含有集成代码，它们的类型和用例来自 Jev 与两位盲评审阅者的多数意见，第 7 节中提到的那一处用例更正除外。另外 3 个与 Jev 无关，因此类型为 `unrelated`，用例为 `general_or_other`。值 "Jev and two blind reviewers" 标记其余所有行：这些行的所有最终标签都来自该多数意见，在三者意见均不一致时加入第三位盲评审阅者。唯一的例外是 6 个相关仓库的类型：它们的最终类型是 `unrelated`，与其相关性矛盾，因此改用 Jev 给出的类型；若 Jev 也选择了 `unrelated`，则使用 `application`。

[`data/jev-integration-evidence.csv`](data/jev-integration-evidence.csv) 为验证第 7 节的集成而阅读的每个源文件保存一行：仓库、是否存在集成、集成类型、文件路径、主集成文件首次添加的日期，以及关于该日期的备注。在 104 个仓库中，有 90 个的备注写明了添加该文件的提交或发布版本。其余 14 个的备注只重复日期，因为源数据没有为它们记录提交或发布版本。

## 15. 来源

- TypeSafe 文档：<https://docs.typesafe.ai/concepts/use-case-map>、<https://docs.typesafe.ai/api>、<https://docs.typesafe.ai/models>、<https://docs.typesafe.ai/model-jaggedness/jev-1.13>、<https://docs.typesafe.ai/llms.txt>
- 官方组织：<https://github.com/typesafe-ai>
- 非官方社区组织：<https://github.com/TypeSafeAI>
- 社区列表：第 10 节中的 14 个仓库
- JevBench：<https://github.com/fstandhartinger/jevbench> 和 <https://benchmarkheaven.com/jev-models>
- jabr/classifier-benchmark：<https://github.com/jabr/classifier-benchmark> 和 <https://github.com/wfzyx/von/issues/3>
- Jev Decision Index：<https://huggingface.co/spaces/multimodalart/jev-decision-index>
- DecisionBench：<https://github.com/Hanno-Labs/decision-bench-results/pull/26>
- umstek/zero-shot-ie-bench：<https://github.com/umstek/zero-shot-ie-bench/pull/4>
- mandu5/jevcompat：<https://github.com/mandu5/jevcompat/blob/main/results/jeff/report.md>
- PriorBench：<https://github.com/priorbench/jev>
- Rajesh Beri：<https://www.beri.net/article/typesafe-jev-typed-decision-model-calibration-decomposition-shadow-eval>
- Andrew Yourtchenko：<https://ayourtch-llm.github.io/apchat-blog/posts/2026-09-20-jev-clones-measured/>
- Hacker News：<https://news.ycombinator.com/item?id=49717558>
- SemIf 结果：<https://github.com/TheoLeeCJ/SemIf-OpenJev/blob/master/docs/RESULTS.md>
- LangChain 集成：<https://docs.langchain.com/oss/python/integrations/providers/typesafe>
- Vercel 指南：<https://vercel.com/kb/guide/typesafe-jev-and-ai-sdk>
- 第 7 节中的每个集成都链接到其仓库；[`data/jev-integration-evidence.csv`](data/jev-integration-evidence.csv) 列出了阅读过的文件。
