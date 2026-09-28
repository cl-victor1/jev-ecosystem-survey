**English** | [简体中文](README.zh-CN.md)

# The open-source ecosystem around TypeSafe Jev: survey and use-case classification

Survey date: 2026-09-27. Model used for classification: `jev-1.13.0`. Scope: every public GitHub repository that uses, integrates, extends, studies, catalogues, or reimplements Jev, as far as GitHub search, GitHub code search, and 14 community lists can find them.

## Summary

- Jev is about two weeks old in public (the launch post reached Hacker News on 2026-09-15), and GitHub already holds **14,742 public repositories that relate to it**. 12,923 of them were created in September 2026, with a peak of 1,783 new repositories on 2026-09-21.
- The official `typesafe-ai` organization publishes 9 repositories. 4 of them are Jev tools (two client libraries, an agent skill, and an adapter that runs the same interface on other large language models). The other 5 are infrastructure and forks. The `TypeSafeAI` organization (8 repositories, jev.works) is an **unofficial** community organization.
- Across all 14,742 repositories, the most common use-case category of the TypeSafe use-case map is **AI Automation Software** (30.4%), followed by **Harness Engineering** (25.3%). Weighted by stars, **Harness Engineering** leads with 52.1% of all stars, because the large agent frameworks use Jev inside their harness.
- The two smallest categories are **Universal Verification** (6.4%) and **AI Map Reduce over Big Data** (6.6%), although the official cookbooks show map-reduce and verification workloads.
- Among the 11,771 applications, developer tools, and libraries, the leading example automation use cases are **gaming** (8.7%), **search and retrieval** (7.6%), **model routing** (7.4%), and **large language model guardrails** (6.4%). 48.0% are general-purpose and fit no single industry use case.
- 101 established open-source projects with 1,000 or more stars that GitHub code search returned have Jev integration code on their default branch, checked by reading that code. This is a lower bound: section 12 lists 3 more merged integrations in projects with 1,000 or more stars that code search did not return (Langfuse, LanceDB, and OpenInference). In 94 of the 101 the public code calls Jev, directly or on behalf of its users. The other 7 do not: Unsloth Studio and colibri serve a Jev-compatible endpoint that a local model answers; PostHog's features call JevK5, an open replica that PostHog hosts itself, and its TypeSafe client has no caller on master; MCPJam Inspector calls Jev only from its private backend; Opik and Sentry only trace Jev calls that their users make; and models.dev only lists Jev in its model catalogue. Examples of real integrations: LangChain, LiteLLM, DSPy, pydantic-ai, the Vercel AI SDK, Pipecat, OpenClaw, goose, and deer-flow. Most are opt-in.
- Of 10 open-weight "Jev replacements" checked against independent benchmarks, the main claims of 5 are contradicted, 3 hold up (they claim little), 1 is self-reported only, and 1 is disputed. 9 of the 10 have a published independent accuracy result, and Jev scores higher in each of them with one exception (for Kev and openJev-verdict-2.0, outsiders measured only older or smaller models, not the headline Kev-27B or Verdict 2.0). The exception is a Laya checkpoint fine-tuned on the train split of one benchmark, which beat Jev without fine-tuning on that benchmark, a comparison that the dataset card calls not comparable. An unpublished JevBench draft also put JevK5 `v0.3` above Jev. NanoJev has no independent comparison. On the community Jev Decision Index (68 open entrants), no model scores above Jev. The JevBench composite, which also weighs cost and speed, ranks three open models above Jev.
- Jev classified every repository from its own README, description, and topics. Two independent blind reviewers then labelled all 16,128 repositories, and a third settled three-way disagreements. Jev agreed with the reviewer consensus on relatedness 91.4%, project kind 83.9%, use case 82.0%, and top-level category 69.6% of the time. The top-level categories overlap, which makes the category the hardest label: the two reviewers agree with each other on it 89.2% of the time.

## 1. What Jev is

Jev is the flagship model of TypeSafe AI and the first model of what TypeSafe calls "System One". It is not a chat model and does not generate prose. A request carries one `state` (text or JSON) and a map of typed questions, and the answer to each question comes back as a typed value. All questions in one request are evaluated in parallel against the same state.

| Question type | What it asks | What comes back |
| --- | --- | --- |
| Choice | Pick one option from a named set (up to 255 options) | The chosen option, a probability for every option, and a confidence value |
| Score | Place the state on an ordered rubric (2 to 10 levels) | A fractional score, a probability for every level, and a confidence value |
| Noul | A yes or no question | The probability that the answer is yes |

Facts from the official documentation (docs.typesafe.ai, read on 2026-09-27):

- Endpoint: `POST https://api.typesafe.ai/v1/systemone`, bearer key authentication. The current model is `jev-1.13.0`. The aliases `jev-latest` and `jev-preview` both point to it.
- Price: $0.042 per million input tokens. Output tokens are free.
- Limits: 250,000 tokens per second and 1,200 requests per minute (the documentation says these limits are adjusting dynamically and can change without notice while TypeSafe adds capacity). 64,000 tokens per request, and 32,000 tokens for the state plus the longest question. Text input only.
- Other access paths (sources in section 12): the Vercel AI Gateway (model `typesafe-ai/jev`), OpenRouter (model `typesafe/jev-1.13`), DigitalOcean Serverless Inference, and the Cloudflare AI model catalog, which lists Jev as a third-party model. The classification in section 2.2 used none of them; it called the TypeSafe endpoint directly.
- Known weaknesses listed by TypeSafe on the "Jev 1.13 jaggedness" page: literal reading, arithmetic and counting, date comparison, indirection, large states full of irrelevant detail, adversarial content in the state, contradictory instructions and criteria, common-sense structural invariants (for example, a Noul and the Noul of its negation need not add up to 1, and a threshold tuned on a Noul does not carry over to a Choice), and text generation.

## 2. Method

### 2.1 Collection

| Step | Repositories |
| --- | ---: |
| Found in 14 community lists and 9 GitHub repository searches | 17,099 |
| Found only through GitHub code search for the TypeSafe endpoint and model names | 497 |
| Unique candidates | 17,596 |
| Removed: created before August 2026 and matched only by the word "jev" in the name or description, with no TypeSafe mention | 999 |
| Metadata and README fetched | 16,597 |
| Removed: deleted, private, or renamed | 19 |
| Removed: neither a README nor a description | 415 |
| Removed: duplicates of the same repository under an old and a new name | 35 |
| Classified by Jev | 16,128 |

A keyword filter was used first to skip repositories that never name TypeSafe. The blind review of a random sample of the skipped repositories (pool G below) found that most of them are related to Jev, for example projects that reach Jev through OpenRouter or copy its interface without naming TypeSafe. The filter was therefore dropped, and every repository with a README or a description was classified.

GitHub search returns at most 1,000 results per query, so each query ran once for all repositories created before September 2026 and once per creation day from 2026-09-01 to 2026-09-27. Windows that still exceeded 1,000 results were split into smaller time windows until each piece fitted, with two limits. The pre-September window of the name search for "jev" (3,315 results) was not split on purpose, because those repositories predate the public launch of Jev and are mostly unrelated to it, so only its first 1,000 results were read. The pre-September window of the README search for "jev" and "typesafe" (1,832 results) was split only from 2024-05-01 onward, shortly before TypeSafe created its GitHub organization on 2024-05-28. The 14 community lists are the "awesome" lists named in section 10.

### 2.2 Classification with Jev

Each repository was one request to `POST https://api.typesafe.ai/v1/systemone` with the model pinned to `jev-1.13.0`. The state held the repository name, its GitHub description, its topics, its primary language, its homepage, and its README (cleaned of images and HTML, up to 60,000 characters). For the 1,311 classified repositories that GitHub code search found, the state also held the paths of the files that call the TypeSafe application programming interface and an excerpt of the most relevant file, because many large projects mention Jev only in code. When a state exceeded the token limit, the README was shortened and the request repeated.

Each request asked 39 questions at once:

- 1 Noul: is the repository related to Jev at all.
- 4 Choices: project kind (8 options), use-case category (the 5 categories of the use-case map plus "none of these"), example automation use case (the 19 use cases of the map plus "general purpose or other"), and decision shape (the 10 task categories of the map).
- 34 Nouls, one per named option of the category, use case, and decision shape Choices: 5 categories, 19 use cases, and 10 task categories. The two catch-all options, "none of these" and "general purpose or other", have no Noul. Each Noul is absolute and each Choice is relative, so the pair gives a second signal for each named label.

The option descriptions are condensed from the use-case map at https://docs.typesafe.ai/concepts/use-case-map, and some were widened beyond it. Gaming also covers agents that play or control games: the map's Gaming entry lists player reports, chat moderation, frustration and engagement scores, and churn signals, and it names playing games only in the Real-time applications card. Real-time applications also covers driving robots or devices and reacting to voice, Risk assessment also covers trading and market risk decisions, and the Routing decision shape also covers the next action in an agent or game loop. The reviewers in section 2.3 used the same descriptions, so the gaming and risk assessment shares in section 6.3 include game-playing agents and trading tools that the map does not list under those use cases. The instructions told Jev to judge the problem each project exists to solve, not the sample data in its README (many quickstarts use a support ticket as a toy example, which had pulled software development kits toward "customer support" in the pilot). 16,128 requests used 128,356,429 input tokens, which costs $5.39 at the list price.

### 2.3 Verification of the Jev labels

Two independent reviewer agents read the same repository data and labelled each repository blind, without seeing the Jev answer. One reviewer judged from what the README says. The other was told to be skeptical of marketing text and to judge from the functionality the project actually implements. For each field, the final label is the majority of Jev and the two reviewers. When all three disagreed, a third blind reviewer chose between the candidate labels.

| Pool | How it was chosen | Repositories |
| --- | --- | ---: |
| A | Every repository that Jev judged related with 20 or more stars and that is not in pool G, H, or I, and every official or community-organization repository that Jev judged related | 678 |
| B | A random sample of the repositories that Jev judged related with fewer than 20 stars | 300 |
| C | A random sample of the repositories that Jev judged unrelated | 100 |
| D | The other repositories with 20 or more stars that Jev judged unrelated, outside pools C, G, H, and I | 90 |
| E | Every other repository that Jev judged unrelated | 950 |
| F | Every other repository that Jev judged related (the long tail) | 12,255 |
| G | A random sample of the repositories that the first keyword filter had skipped | 104 |
| H | Every other repository that the first keyword filter had skipped | 1,570 |
| I | The large integrations of section 7 that are not in pool A (the other 23 of the 104 are in pool A). For all 104, relatedness and the final category come from reading their source code | 81 |
| All | Every classified repository | 16,128 |

Agreement of Jev with the reviewer consensus (the cases where both reviewers agree), per pool:

| Pool | Related or not | Project kind | Use case | Top-level category | Reviewers agree with each other on the category |
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
| All | 91.4% | 83.9% | 82.0% | 69.6% | 89.2% |

Pool B is the unbiased estimate for the long tail, because it is a random sample of what Jev judged related. There, Jev is reliable on relatedness and project kind, good on the use case, and weakest on the top-level category. The two reviewers also disagree most often on the category, because the five categories overlap: a tool-call gate is Harness Engineering and Universal Verification at the same time, and a game or trading bot can be both AI Automation Software and Real-time applications.

Pools C, D, and E show the main weakness of the Jev relatedness answer: it is conservative. Many repositories that mention Jev briefly, copy its interface, or reach it through OpenRouter got a probability below 0.5. The reviewers judged most of them related, so every repository that Jev called unrelated was reviewed, and the counts in this report include those corrections.

Pools G and H hold the repositories that a first keyword filter had skipped because they never name TypeSafe. The reviewers judged many of them related (for example projects that call Jev through OpenRouter or copy its interface), so the filter was dropped and all of them were classified and reviewed.

Pool I holds 81 of the 104 large integrations of section 7, and the other 23 are in pool A. For all 104, relatedness and the final category come from reading their source code, not from the reviewer vote. That reading found no Jev integration code in 3 of them, so they count as not related. For the other 101, the kind and use case come from the majority of Jev and the two reviewers, with one use-case correction from the source code (section 7). The 3 without integration code have the kind `unrelated` and the use case `general_or_other`, as in section 14.

### 2.4 Research and verification of integrations, replicas, and evaluations

- A general research pass (web search, source reading, and a three-vote adversarial check of every claim) covered the official and community organizations and the pricing.
- 104 of the projects with 1,000 or more stars that GitHub code search found referencing Jev (section 7 names the 6 that were left out) got one investigator, who read the integration code on the default branch, and one skeptical reviewer, who tried to prove each field wrong and corrected it. 101 of the 104 have Jev integration code, and in 94 of them the public code calls Jev.
- Each of 10 open-weight replicas got one investigator and three skeptics, who voted on the verdict.
- Each of 6 evaluation sources (5 third-party studies and the Hacker News launch thread, where TypeSafe staff also took part) got one reader and two skeptics, who checked every finding against the source text.
- Three completeness critics then searched for missing replicas, evaluations, integrations, and risks. Each new item was investigated and checked by two skeptics.

## 3. The official `typesafe-ai` organization

The organization (display name "TypeSafe", created 2024-05-28, website typesafe.ai) is linked from docs.typesafe.ai. GitHub does not mark it as verified.

| Repository | Stars | Created | License | What it is | Final category label |
| --- | ---: | --- | --- | --- | --- |
| [`typesafe-ai/skills`](https://github.com/typesafe-ai/skills) | 2,277 | 2026-08-24 | MIT | Agent skill for Claude Code and other agents: designs TypeSafe workflows and finds current documentation and cookbooks | None of the five categories |
| [`typesafe-ai/system-one-adapter-python`](https://github.com/typesafe-ai/system-one-adapter-python) | 311 | 2026-08-08 | MIT | Python adapter with the same `system_one` interface, answered by other large language models instead of Jev | None of the five categories |
| [`typesafe-ai/typesafe-sdk-js`](https://github.com/typesafe-ai/typesafe-sdk-js) | 247 | 2026-09-04 | MIT | Official JavaScript and TypeScript client library (`@typesafe-ai/sdk` on npm) | None of the five categories |
| [`typesafe-ai/typesafe-sdk-python`](https://github.com/typesafe-ai/typesafe-sdk-python) | 237 | 2026-09-04 | MIT | Official Python client library (`typesafe-sdk` on PyPI) | None of the five categories |
| [`typesafe-ai/daggerverse`](https://github.com/typesafe-ai/daggerverse) | 22 | 2026-04-17 | Apache-2.0 | Shared Dagger continuous integration modules (uv, GitHub, Twingate, pinact, zizmor, deptry); no Jev code | Not related to Jev |
| [`typesafe-ai/LLaDA`](https://github.com/typesafe-ai/LLaDA) | 12 | 2025-07-13 | MIT | Fork of `ML-GSAI/LLaDA`, a diffusion language model; purpose not documented | Not classified (no Jev use) |
| [`typesafe-ai/vllm`](https://github.com/typesafe-ai/vllm) | 3 | 2025-05-17 | Apache-2.0 | Fork of `vllm-project/vllm`, a model serving engine; purpose not documented | Not classified (no Jev use) |
| [`typesafe-ai/pulumi-clickhouse`](https://github.com/typesafe-ai/pulumi-clickhouse) | 3 | 2026-07-08 | Apache-2.0 | Fork of `pulumiverse/pulumi-clickhouse`, an infrastructure provider; purpose not documented | Not classified (no Jev use) |
| [`typesafe-ai/typesafe-ai.github.io`](https://github.com/typesafe-ai/typesafe-ai.github.io) | 2 | 2024-05-28 | none | Organization website repository; no README | Not classified (no README or description) |

The two client libraries return typed answers whose types follow the questions. The agent skill teaches coding agents to design Jev workflows. The adapter `system-one-adapter-python` answers the same `system_one` interface with other large language models (OpenAI-compatible, Anthropic, and Gemini providers), which helps to compare Jev with a large language model on cost, speed, and quality.

## 4. The unofficial `TypeSafeAI` community organization

`github.com/TypeSafeAI` ("TypeSafe Community", website jev.works) was created on 2026-09-18. 4 of its repositories are older than the organization: they were created on 2026-09-16 and 2026-09-17 under the personal account @BunsDev and moved in later, so the Created column shows when each repository was created, not when it joined the organization. Its profile says "UNOFFICIAL COMMUNITY GITHUB — NOT THE OFFICIAL TYPESAFE AI TEAM" and names @BunsDev, whom it calls "VC Moderator", as its creator. The README files of 7 of its 8 repositories repeat that they are independent community work. The `modex` README has no such notice; only the organization profile describes `modex` as a community project. The name differs from the official organization only by a hyphen, so readers can confuse the two.

| Repository | Stars | Created | What it is | Final category label |
| --- | ---: | --- | --- | --- |
| [`TypeSafeAI/typesafe-playground`](https://github.com/TypeSafeAI/typesafe-playground) | 21 | 2026-09-16 | Next.js playground with 110 examples in 22 packs; 10 packs are games, dilemmas, and model challenges | AI Automation Software |
| [`TypeSafeAI/jev-harness`](https://github.com/TypeSafeAI/jev-harness) | 20 | 2026-09-22 | Research-stage review gate: a large language model proposes one action, Jev answers four yes or no questions, code decides | Harness Engineering |
| [`TypeSafeAI/typesafe-ui`](https://github.com/TypeSafeAI/typesafe-ui) | 6 | 2026-09-17 | shadcn-style React components for Jev interfaces; a private workspace package, not published | None of the five categories |
| [`TypeSafeAI/typesafe-router`](https://github.com/TypeSafeAI/typesafe-router) | 4 | 2026-09-17 | TypeScript library that picks one tool or model from a fixed set with a Choice question and deterministic fallbacks | Harness Engineering |
| [`TypeSafeAI/clarity-judge`](https://github.com/TypeSafeAI/clarity-judge) | 3 | 2026-09-16 | Writing checker with separate named checks (hedging, filler, tone, passive voice), each with its own verdict | AI Automation Software |
| [`TypeSafeAI/modex`](https://github.com/TypeSafeAI/modex) | 2 | 2026-09-25 | Desktop application that drives Claude Code and Codex; its "Auto" mode uses Jev to pick the model and reasoning effort per turn | Harness Engineering |
| [`TypeSafeAI/community-blog`](https://github.com/TypeSafeAI/community-blog) | 1 | 2026-09-18 | Static site with short introductory community notes | Not classified (no Jev use) |
| [`TypeSafeAI/.github`](https://github.com/TypeSafeAI/.github) | 0 | 2026-09-19 | Organization profile and contribution guidance | Not classified (no Jev use) |

The `jev-harness` benchmark result in its README (7 of 25 bad proposals caught without Jev, 25 of 25 with Jev) comes from scripted mock values on synthetic fixtures, not from measurements of Jev. The project says so itself.

## 5. Size and growth

New Jev-related repositories per creation day in September 2026:

| Day | New repositories |
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

1,819 related repositories were created before September 2026. They include established projects that added a Jev integration later (section 7) and older repositories that were reused for Jev work.

| Stars | Repositories |
| --- | ---: |
| 1,000 or more | 130 |
| 100 to 999 | 255 |
| 20 to 99 | 406 |
| 5 to 19 | 831 |
| 1 to 4 | 3,451 |
| none | 9,669 |

65.6% of the related repositories have no stars. The ecosystem is a long tail of small experiments around a few large projects.

| Primary language | Repositories |
| --- | ---: |
| Python | 5,620 |
| TypeScript | 3,987 |
| JavaScript | 1,920 |
| No primary language | 636 |
| HTML | 569 |
| Rust | 475 |
| Go | 419 |
| Swift | 153 |
| Java | 115 |
| C# | 114 |

## 6. Use-case classification

All numbers in this section use the final labels: the majority of Jev and two blind reviewers for all 16,128 repositories, a third blind reviewer where all three disagreed, and source-code verification for the relatedness and the category of the 104 large integrations. Section 2.3 gives the agreement of each label.

### 6.1 Project kinds

| Kind | Repositories | Share | Share of all stars |
| --- | ---: | ---: | ---: |
| Application | 6,169 | 41.8% | 33.8% |
| Developer tool | 4,305 | 29.2% | 23.6% |
| Library or integration | 1,297 | 8.8% | 29.1% |
| Benchmark or evaluation | 1,215 | 8.2% | 0.1% |
| Alternative or replica model | 1,136 | 7.7% | 8.2% |
| Directory or guide | 611 | 4.1% | 5.2% |
| Account tooling | 9 | 0.1% | under 0.1% |

### 6.2 Use-case map categories

| Category | Repositories | Share | Share of all stars |
| --- | ---: | ---: | ---: |
| AI Automation Software | 4,476 | 30.4% | 20.8% |
| Harness Engineering | 3,733 | 25.3% | 52.1% |
| Real-time applications | 2,447 | 16.6% | 4.3% |
| None of the five categories | 2,173 | 14.7% | 9.8% |
| AI Map Reduce over Big Data | 966 | 6.6% | 6.3% |
| Universal Verification | 947 | 6.4% | 6.7% |

One repository often fits more than one category. The share of related repositories for which Jev answered yes (probability 0.5 or more) to the Noul "does the core purpose fit this category":

| Category | Share of related repositories |
| --- | ---: |
| AI Automation Software | 68.8% |
| Harness Engineering | 39.3% |
| Real-time applications | 14.3% |
| Universal Verification | 8.4% |
| AI Map Reduce over Big Data | 3.0% |

Primary category by star tier:

| Stars | AI Automation Software | Harness Engineering | Real-time applications | Universal Verification | AI Map Reduce over Big Data | None of the five categories |
| --- | ---: | ---: | ---: | ---: | ---: | ---: |
| 1,000 or more | 24.6% | 36.9% | 6.9% | 11.5% | 5.4% | 14.6% |
| 100 to 999 | 19.2% | 32.5% | 14.1% | 3.9% | 5.9% | 24.3% |
| 20 to 99 | 21.9% | 31.5% | 14.5% | 5.2% | 7.4% | 19.5% |
| 5 to 19 | 23.9% | 33.1% | 14.0% | 6.0% | 5.9% | 17.1% |
| 1 to 4 | 27.0% | 29.4% | 16.3% | 6.0% | 5.9% | 15.4% |
| none | 32.8% | 22.6% | 17.2% | 6.7% | 6.8% | 13.8% |

### 6.3 Example automation use cases

Among the 11,771 applications, developer tools, and libraries:

| Use case | Repositories | Share |
| --- | ---: | ---: |
| General purpose or other | 5,651 | 48.0% |
| Gaming | 1,021 | 8.7% |
| Search and retrieval | 890 | 7.6% |
| Model routing | 874 | 7.4% |
| Large language model guardrails | 759 | 6.4% |
| Semantic code linting | 616 | 5.2% |
| Risk assessment | 538 | 4.6% |
| Moderation and trust and safety | 353 | 3.0% |
| Customer support | 272 | 2.3% |
| Recruiting | 165 | 1.4% |
| Scientific discovery | 124 | 1.1% |
| E-commerce marketplaces | 97 | 0.8% |
| Lead generation | 95 | 0.8% |
| Legal and compliance | 94 | 0.8% |
| Graphs and knowledge graphs | 75 | 0.6% |
| Advertising | 74 | 0.6% |
| Financial crime | 31 | 0.3% |
| Feature extraction for predictive modeling | 19 | 0.2% |
| Insurance claims | 13 | 0.1% |
| Demand forecasting | 10 | 0.1% |

### 6.4 Decision shapes

Jev labels only (the reviewers did not label decision shapes):

| Decision shape | Repositories | Share |
| --- | ---: | ---: |
| Routing | 5,238 | 35.5% |
| Classification | 4,865 | 33.0% |
| Detection | 1,232 | 8.4% |
| Scoring | 1,166 | 7.9% |
| Verification | 1,110 | 7.5% |
| Ranking | 593 | 4.0% |
| Retrieval | 188 | 1.3% |
| Structured data extraction | 163 | 1.1% |
| Search | 162 | 1.1% |
| Machine learning feature extraction | 25 | 0.2% |

### 6.5 Where the ecosystem concentrates

By count, the ecosystem sits in two categories. AI Automation Software (30.4%) is mostly applications and bots in which ordinary code owns the control flow and Jev answers one narrow question per step. Harness Engineering (25.3%) is mostly developer tools: coding-agent plugins, Model Context Protocol servers, routers, and tool-call gates. Real-time applications (16.6%) is led by games (43% of the category have the gaming use case), and a whole-word keyword check finds game, gaming, gameplay, chess, poker, Doom, Mario, Pokémon, Minecraft, browser, desktop, voice, speech, robot, robotics, drone, MuJoCo, or smart home terms in 60% of its repositories.

By stars, the picture shifts further toward Harness Engineering (52.1% of all stars). The large projects that adopted Jev are agent frameworks and gateways, and they use Jev to route models, gate tool calls, compact context, and rank tools. Among repositories with 1,000 or more stars, 36.9% are Harness Engineering.

AI Map Reduce over Big Data (6.6%) and Universal Verification (6.4%) are the gaps. The official cookbooks show batch workloads (re-ranking legal passages, hierarchical classification of patents, entity alignment over 450 candidate pairs), but few open-source projects run Jev over large corpora. Verification of other models appears mostly inside developer tools (40.0%) and applications (31.2%) of the category; benchmark or evaluation projects are only 15.8%. Its largest named use cases are large language model guardrails (41.0%) and semantic code linting (16.9%); another 26.0% are general purpose or other and fit no single use case.

Among the example use cases, gaming is the largest domain (8.7%). Search and retrieval (7.6%) and model routing (7.4%) are the largest industry-neutral uses and are almost tied, and large language model guardrails (6.4%), semantic code linting (5.2%), and risk assessment (4.6%) follow. In risk assessment, a whole-word keyword check for trading, stock, cryptocurrency, market, finance, portfolio, investment, and hedge fund terms (with their word forms), decentralized finance (the keyword `defi`), and the names Polymarket, Kalshi, and Hyperliquid finds them in 70% of the repositories, so much of it is trading bots and market tools. The three smallest industry domains of the use-case map are demand forecasting (10 repositories), insurance claims (13 repositories), and financial crime (31 repositories), so these industries have almost no open-source projects yet. Feature extraction for predictive modeling, an industry-neutral use, is also small (19 repositories).

### 6.6 Notable projects per category

The largest projects by stars in each category. The 101 large framework integrations have their own table in section 7, and directories are left out.

#### AI Automation Software

| Repository | Stars | Kind | GitHub description (quoted) |
| --- | ---: | --- | --- |
| [`OpenByteInc/QuantDinger`](https://github.com/OpenByteInc/QuantDinger) | 12,233 | Application | "Open-source AI Trading OS, agent trading, and vibe trading, with Jev System One integratio..." |
| [`ruvnet/RuVector`](https://github.com/ruvnet/RuVector) | 4,529 | Alternative or replica model | "RuVector provides High Performance, Real-Time decisions and agent memory , Self-Learning A..." |
| [`TheoLeeCJ/SemIf-OpenJev`](https://github.com/TheoLeeCJ/SemIf-OpenJev) | 4,446 | Alternative or replica model | "Semantic ifs from open models, on a 3090 at home. Independent; not affiliated with Jev or ..." |
| [`deepopen-com/deepopen`](https://github.com/deepopen-com/deepopen) | 1,044 | Alternative or replica model | (description not in English) |
| [`awlevin/typesafe-computer-use`](https://github.com/awlevin/typesafe-computer-use) | 1,030 | Application | "Computer use for about $0.0002 a step: OCR the screen, classify the next action with TypeS..." |
| [`CelestoAI/celesto`](https://github.com/CelestoAI/celesto) | 984 | Developer tool | "Secure and persistent computer for AI agents" |
| [`SynaLinks/synalinks-skills`](https://github.com/SynaLinks/synalinks-skills) | 907 | Developer tool | "Coding Agents skills for Synalinks OSS" |
| [`duriantaco/skylos`](https://github.com/duriantaco/skylos) | 838 | Developer tool | "Open source local-first PR scanner that finds dead code, security bugs, secrets, quality r..." |
| [`vercel-labs/ai-cli`](https://github.com/vercel-labs/ai-cli) | 817 | Developer tool | "Generate anything from your terminal" |
| [`TypeLLM/TypeLLM`](https://github.com/TypeLLM/TypeLLM) | 801 | Alternative or replica model | "TypeLLM: LLMs with type-safe generation" |

#### Harness Engineering

| Repository | Stars | Kind | GitHub description (quoted) |
| --- | ---: | --- | --- |
| [`tamaratran/fast-jev-compaction`](https://github.com/tamaratran/fast-jev-compaction) | 7,015 | Developer tool | "Claude Code plugin that replaces the compaction summary with Jev decisions: every tool cal..." |
| [`zilliztech/memsearch`](https://github.com/zilliztech/memsearch) | 2,664 | Developer tool | "A persistent, unified memory layer for all your AI agents (e.g. Claude Code, Codex, DSH), ..." |
| [`Contrastive-LM/CLM`](https://github.com/Contrastive-LM/CLM) | 1,871 | Alternative or replica model | (no description) |
| [`autonomous-ai/openharness`](https://github.com/autonomous-ai/openharness) | 985 | Developer tool | "The ultimate harness for coding agents and beyond. All your agents. All your machines. One..." |
| [`Raudaschl/rag-fusion`](https://github.com/Raudaschl/rag-fusion) | 958 | Benchmark or evaluation | "RAG-Fusion: multi-query generation + Reciprocal Rank Fusion for better retrieval-augmented..." |
| [`kerpopule/hermes-jev-skills`](https://github.com/kerpopule/hermes-jev-skills) | 865 | Developer tool | "Jev-powered model routing, memory, compaction, skill selection, computer and browser use f..." |
| [`bastani-inc/atomic`](https://github.com/bastani-inc/atomic) | 834 | Developer tool | "The verifiable coding agent runtime. Define your coding agent's process in natural languag..." |
| [`kitfunso/hippo-memory`](https://github.com/kitfunso/hippo-memory) | 764 | Developer tool | "Biologically-inspired memory for AI agents. Decay, retrieval strengthening, consolidation...." |
| [`samuelfaj/distill`](https://github.com/samuelfaj/distill) | 692 | Developer tool | "Get FAR MORE done with FAR FEWER tokens 🔥" |
| [`dzhng/jevgrep`](https://github.com/dzhng/jevgrep) | 677 | Developer tool | "Find code by asking what it does. A CLI for coding agents that uses Jev to discover releva..." |

#### Real-time applications

| Repository | Stars | Kind | GitHub description (quoted) |
| --- | ---: | --- | --- |
| [`browser-use/jev-ultrafast`](https://github.com/browser-use/jev-ultrafast) | 20,786 | Application | "Fastest and cheapest web agent" |
| [`mizorewww/laya-mlx`](https://github.com/mizorewww/laya-mlx) | 6,472 | Alternative or replica model | "Native MLX runtime for Laya typed decision models — 7–14 ms short decisions on M3 Max. No ..." |
| [`jarrodwatts/jev-trader`](https://github.com/jarrodwatts/jev-trader) | 2,596 | Application | "One AI trade decision every Monad block. Jev on Kuru MON-USDC." |
| [`TianyuCodings/NanoJev`](https://github.com/TianyuCodings/NanoJev) | 2,344 | Alternative or replica model | "A nano replica of Jev: parallel decisions, dynamic candidates, and an end-to-end training ..." |
| [`wfzyx/von`](https://github.com/wfzyx/von) | 724 | Alternative or replica model | "The open-source System One decision model. Sub-15ms, non-autoregressive, local drop-in alt..." |
| [`anishfn/shapeshift`](https://github.com/anishfn/shapeshift) | 705 | Application | "An input that becomes what you mean: one text box that morphs into the right UI as you typ..." |
| [`milind-soni/tiptour-macos`](https://github.com/milind-soni/tiptour-macos) | 665 | Application | "Open-Source fast local computer use" |
| [`duanebester/gooey`](https://github.com/duanebester/gooey) | 632 | Library or integration | "Gooey is a hybrid immediate/retained mode UI framework designed for building fast, GPU-ren..." |
| [`rmalde/minecraft-agent`](https://github.com/rmalde/minecraft-agent) | 560 | Application | "Astra planner and JEV controller for Minecraft, with native recording, tested routes, and ..." |
| [`jev-chat/jev-chat-jarvis-mac`](https://github.com/jev-chat/jev-chat-jarvis-mac) | 417 | Application | (description not in English) |

#### Universal Verification

| Repository | Stars | Kind | GitHub description (quoted) |
| --- | ---: | --- | --- |
| [`Pluviobyte/rnskill`](https://github.com/Pluviobyte/rnskill) | 1,615 | Developer tool | (description not in English) |
| [`JoasASantos/NeuroSploit`](https://github.com/JoasASantos/NeuroSploit) | 1,396 | Developer tool | "NeuroSploit is an advanced, AI-powered penetration testing framework designed to automate ..." |
| [`bitsocialnet/seedit`](https://github.com/bitsocialnet/seedit) | 416 | Application | "A peer-to-peer Reddit alternative." |
| [`coldteadotai/abide`](https://github.com/coldteadotai/abide) | 364 | Developer tool | "Make your coding agent abide by all your project rules" |
| [`edinetdb/dexter-jp`](https://github.com/edinetdb/dexter-jp) | 310 | Application | (description not in English) |
| [`SREGym/SREGym`](https://github.com/SREGym/SREGym) | 305 | Benchmark or evaluation | "Can AI agents resolve production incidents?" |
| [`NiazMorshed2007/jev-review`](https://github.com/NiazMorshed2007/jev-review) | 230 | Developer tool | "Local-first MCP plugin for continuous software-quality review by AI coding agents, powered..." |
| [`liuyanghejerry/Clausura`](https://github.com/liuyanghejerry/Clausura) | 203 | Developer tool | "CI-native agent CLI tool for deterministic pipeline gating." |
| [`wlsdks/ontology-atlas`](https://github.com/wlsdks/ontology-atlas) | 139 | Developer tool | "Understand what your codebase builds, why it is structured that way, and what a change cou..." |
| [`zetaalphavector/RAGElo`](https://github.com/zetaalphavector/RAGElo) | 133 | Developer tool | "RAGElo is a set of tools that helps you selecting the best RAG-based LLM agents by using a..." |

#### AI Map Reduce over Big Data

| Repository | Stars | Kind | GitHub description (quoted) |
| --- | ---: | --- | --- |
| [`genspark-ai/genoffice`](https://github.com/genspark-ai/genoffice) | 7,980 | Application | "Free, open-source AI Office suite: Docs, Sheets, Slides, PDF, Markdown and HTML editors wi..." |
| [`LinklyAI/best-skills`](https://github.com/LinklyAI/best-skills) | 605 | Application | "Daily-updated Top 100 Agent Skills rankings — installs, growth, and social buzz   aggregat..." |
| [`superagents-lab/jev-search`](https://github.com/superagents-lab/jev-search) | 473 | Application | "Search the web with TypeSafe's Jev: source selection, query understanding and relevance ra..." |
| [`kyotofin/tax-doc-classifier`](https://github.com/kyotofin/tax-doc-classifier) | 469 | Application | "Tax document page classifier built on Jev decisions. 100% strict accuracy across 261 IRS f..." |
| [`mrmps/classifier-dev`](https://github.com/mrmps/classifier-dev) | 423 | Developer tool | "Zero-shot text classification over plain HTTP — no API key, no account. One Cloudflare Wor..." |
| [`Extelligence-ai/bagel`](https://github.com/Extelligence-ai/bagel) | 397 | Developer tool | "Query robotics, drone, and IoT data in plain English through an MCP server, with an intell..." |
| [`realZachi/pg-jev`](https://github.com/realZachi/pg-jev) | 372 | Library or integration | "Ask your Postgres tables questions in plain language. A PostgreSQL extension powered by Ty..." |
| [`noperator/siftrank`](https://github.com/noperator/siftrank) | 219 | Developer tool | "Use LLMs to find the needles in your haystack" |
| [`ielab/llm-rankers`](https://github.com/ielab/llm-rankers) | 213 | Benchmark or evaluation | "Document Ranking with Large Language Models." |
| [`uehaj/jev-semgrep`](https://github.com/uehaj/jev-semgrep) | 144 | Developer tool | "grep by meaning, across languages. TypeSafe Jev scores every line against a meaning; combi..." |

#### None of the five categories

| Repository | Stars | Kind | GitHub description (quoted) |
| --- | ---: | --- | --- |
| [`vllm-project/vllm`](https://github.com/vllm-project/vllm) | 92,792 | Alternative or replica model | "A high-throughput and memory-efficient inference and serving engine for LLMs" |
| [`NandhaKishorM/laya`](https://github.com/NandhaKishorM/laya) | 26,619 | Alternative or replica model | "Non-autoregressive System 1 decision engine. Typed choice, score and yes/no decisions over..." |
| [`jaredpalmer/kev`](https://github.com/jaredpalmer/kev) | 7,394 | Alternative or replica model | "Jev-like family of decision models built on top of Qwen3.5/3.8 you can train and run on yo..." |
| [`typesafe-ai/skills`](https://github.com/typesafe-ai/skills) | 2,277 | Developer tool | "Agent skills for building with TypeSafe's System One API" |
| [`bespokelabsai/nimble`](https://github.com/bespokelabsai/nimble) | 1,865 | Alternative or replica model | "Local typed decisions, contrastive data curation, and model evaluation." |
| [`vinnylarouge/jevlike`](https://github.com/vinnylarouge/jevlike) | 1,318 | Alternative or replica model | (no description) |
| [`ENTERPILOT/GoModel`](https://github.com/ENTERPILOT/GoModel) | 1,189 | Library or integration | "AI gateway / AI control plane / AI proxy written in Go. Unified OpenAI-compatible and Anth..." |
| [`feder-cr/jev`](https://github.com/feder-cr/jev) | 1,053 | Alternative or replica model | "jevos is an open-source alternative to Jev for yes/no decisions that runs on your laptop." |
| [`Mapika/decider`](https://github.com/Mapika/decider) | 819 | Alternative or replica model | "A family of System One-style models fine-tuned from Qwen3.5, designed for one-pass typed d..." |
| [`nokia-applied-research/AnyJev`](https://github.com/nokia-applied-research/AnyJev) | 819 | Alternative or replica model | "Turn any LLM into a Jev-style decision model: typed decisions, real probabilities, no trai..." |

### 6.7 Notable projects per use case

The largest applications, developer tools, and libraries by stars in each use case of section 6.3, apart from general purpose or other. Other kinds are left out: directories, benchmarks, replicas, and account tooling. Unlike section 6.6, this table includes the large integrations of section 7.

| Use case | Largest applications, developer tools, and libraries by stars |
| --- | --- |
| Gaming | [`rmalde/minecraft-agent`](https://github.com/rmalde/minecraft-agent) (560), [`fhshaik/typesafe-mario`](https://github.com/fhshaik/typesafe-mario) (407), [`CharTyr/STS2-Agent`](https://github.com/CharTyr/STS2-Agent) (329), [`standardagents/jevpilot`](https://github.com/standardagents/jevpilot) (193) |
| Search and retrieval | [`volcengine/OpenViking`](https://github.com/volcengine/OpenViking) (38,790), [`assafelovic/gpt-researcher`](https://github.com/assafelovic/gpt-researcher) (29,649), [`genspark-ai/genoffice`](https://github.com/genspark-ai/genoffice) (7,980), [`zilliztech/memsearch`](https://github.com/zilliztech/memsearch) (2,664) |
| Model routing | [`BerriAI/litellm`](https://github.com/BerriAI/litellm) (59,728), [`Hmbown/Codewhale`](https://github.com/Hmbown/Codewhale) (41,030), [`lidge-jun/opencodex`](https://github.com/lidge-jun/opencodex) (16,454), [`2FastLabs/agent-squad`](https://github.com/2FastLabs/agent-squad) (7,774) |
| Large language model guardrails | [`bytedance/deer-flow`](https://github.com/bytedance/deer-flow) (83,050), [`confident-ai/deepeval`](https://github.com/confident-ai/deepeval) (18,469), [`AIPentest/CyberStrikeAI`](https://github.com/AIPentest/CyberStrikeAI) (7,049), [`FailproofAI/failproofai`](https://github.com/FailproofAI/failproofai) (5,178) |
| Semantic code linting | [`sickn33/agentic-awesome-skills`](https://github.com/sickn33/agentic-awesome-skills) (46,993), [`duriantaco/skylos`](https://github.com/duriantaco/skylos) (838), [`devagrawal09/jev-review`](https://github.com/devagrawal09/jev-review) (625), [`dxos/dxos`](https://github.com/dxos/dxos) (524) |
| Risk assessment | [`TauricResearch/TradingAgents`](https://github.com/TauricResearch/TradingAgents) (108,900), [`koala73/worldmonitor`](https://github.com/koala73/worldmonitor) (87,472), [`virattt/ai-hedge-fund`](https://github.com/virattt/ai-hedge-fund) (63,771), [`OpenByteInc/QuantDinger`](https://github.com/OpenByteInc/QuantDinger) (12,233) |
| Moderation and trust and safety | [`dubinc/dub`](https://github.com/dubinc/dub) (24,834), [`MillionSend/millionsend`](https://github.com/MillionSend/millionsend) (170), [`rokcso/bluenoise`](https://github.com/rokcso/bluenoise) (91), [`TiraelSedai/ClubDoorman`](https://github.com/TiraelSedai/ClubDoorman) (69) |
| Customer support | [`chatwoot/chatwoot`](https://github.com/chatwoot/chatwoot) (37,242), [`MattiaIppoliti/ciele`](https://github.com/MattiaIppoliti/ciele) (249), [`GiesN/typesafe-jev-workflow`](https://github.com/GiesN/typesafe-jev-workflow) (12), [`rayanweragala/jev-call-router`](https://github.com/rayanweragala/jev-call-router) (12) |
| Recruiting | [`skeptrunedev/jev-recruiter`](https://github.com/skeptrunedev/jev-recruiter) (50), [`hqman/JevScout`](https://github.com/hqman/JevScout) (38), [`fusei1008/boss-auto-job-helper`](https://github.com/fusei1008/boss-auto-job-helper) (9), [`gtaras7/typesafe-jev`](https://github.com/gtaras7/typesafe-jev) (8) |
| Scientific discovery | [`PKU-YuanGroup/OpenAI4S`](https://github.com/PKU-YuanGroup/OpenAI4S) (594), [`Sreehari05055/thesys-core`](https://github.com/Sreehari05055/thesys-core) (156), [`topherchris420/james_library`](https://github.com/topherchris420/james_library) (72), [`choxos/jev-reviewer`](https://github.com/choxos/jev-reviewer) (35) |
| E-commerce marketplaces | [`campusx-official/jev-demo`](https://github.com/campusx-official/jev-demo) (15), [`littlewindy123/jev-weekend-shopping-chrome`](https://github.com/littlewindy123/jev-weekend-shopping-chrome) (8), [`Emenowicz/jev-sap-commerce`](https://github.com/Emenowicz/jev-sap-commerce) (8), [`jaturapornchai/bccrm`](https://github.com/jaturapornchai/bccrm) (4) |
| Lead generation | [`twentyhq/twenty`](https://github.com/twentyhq/twenty) (57,572), [`getanyapi-com/lurk`](https://github.com/getanyapi-com/lurk) (101), [`ZeroGold/call-coach-ai`](https://github.com/ZeroGold/call-coach-ai) (44), [`oguzhankayan/reddit-radar`](https://github.com/oguzhankayan/reddit-radar) (41) |
| Legal and compliance | [`kyotofin/tax-doc-classifier`](https://github.com/kyotofin/tax-doc-classifier) (469), [`edinetdb/dexter-jp`](https://github.com/edinetdb/dexter-jp) (310), [`stella/stella`](https://github.com/stella/stella) (255), [`qpiai/anchor`](https://github.com/qpiai/anchor) (10) |
| Graphs and knowledge graphs | [`semantica-agi/semantica`](https://github.com/semantica-agi/semantica) (13,494), [`nimbalyst/nimbalyst`](https://github.com/nimbalyst/nimbalyst) (1,787), [`davide-desio-eleva/kirograph`](https://github.com/davide-desio-eleva/kirograph) (152), [`jexp/neo4jev`](https://github.com/jexp/neo4jev) (145) |
| Advertising | [`realZachi/typesafe-adblock`](https://github.com/realZachi/typesafe-adblock) (85), [`artemnovitckii/creator-lab`](https://github.com/artemnovitckii/creator-lab) (82), [`tomascupr/reelql`](https://github.com/tomascupr/reelql) (27), [`ehui1226/hookmeter-jev`](https://github.com/ehui1226/hookmeter-jev) (22) |
| Financial crime | [`klauswg/jev-guard`](https://github.com/klauswg/jev-guard) (37), [`sandeco/pix-golpe`](https://github.com/sandeco/pix-golpe) (25), [`1aifanatic/jev-uipath-coded-agent`](https://github.com/1aifanatic/jev-uipath-coded-agent) (2), [`ndolinschi/cartshield`](https://github.com/ndolinschi/cartshield) (1) |
| Feature extraction for predictive modeling | [`edamame-labs/tab-jev`](https://github.com/edamame-labs/tab-jev) (4), [`Kaos599/jev-writer`](https://github.com/Kaos599/jev-writer) (1), [`sedthh/xjevboost`](https://github.com/sedthh/xjevboost) (1), [`chen-junluo/jev-measure`](https://github.com/chen-junluo/jev-measure) (1) |
| Insurance claims | [`vishalbitit/jev-prior-auth-triage`](https://github.com/vishalbitit/jev-prior-auth-triage) (0), [`akhilkoduriak/jev-claim-processor`](https://github.com/akhilkoduriak/jev-claim-processor) (0), [`sureshmanem/typesafe_jev_poc`](https://github.com/sureshmanem/typesafe_jev_poc) (0), [`franciscojunqueira/jev-tiss`](https://github.com/franciscojunqueira/jev-tiss) (0) |
| Demand forecasting | [`Orcaset/jev-revenue-forecaset`](https://github.com/Orcaset/jev-revenue-forecaset) (1), [`abhisingh9696/sop-solver-mcp`](https://github.com/abhisingh9696/sop-solver-mcp) (1), [`londrwus/techeu_agentichack`](https://github.com/londrwus/techeu_agentichack) (1), [`igun997/laya-research`](https://github.com/igun997/laya-research) (1) |

## 7. Jev inside established open-source projects

GitHub code search for `api.typesafe.ai`, `typesafe-ai/jev`, and `jev-latest` found 1,328 repositories that reference Jev in their source code. 110 of them have 1,000 or more stars, and 104 of these were checked by reading their code on the default branch. The other 6 were not checked: the replicas `NandhaKishorM/laya`, `jaredpalmer/kev`, `TianyuCodings/NanoJev`, and `bespokelabsai/nimble` are in section 8; `vllm-project/vllm` matches only through an example server in `examples/features/structured_diffusion` that answers `POST /v1/systemone` requests with a local diffusion model, the same shape as Unsloth Studio and colibri; and `yibie/awesome-jev` is a community list (section 10) whose only match is its own curation agent skill. 101 of the 104 have Jev integration code on the default branch. The other 3 do not: `kunchenguid/no-mistakes` removed its integration on 2026-09-22, `op7418/CodePilot` mentions Jev only in research documents, and `fy-agent/fyagent` keeps only archived task records of its developers' coding agents, which called Jev through an external Model Context Protocol server. A skeptical second reader agreed with the first reader on whether an integration is present in all 104 cases, and corrected the description of what Jev decides in 23 of them. The report applies three rules to both readers' results. FyAgent counts as having no integration code, because it keeps only archived task records. colibri gets no category, like Unsloth Studio, because neither colibri nor Unsloth Studio uses the decisions that its local endpoint returns; the final labels give both the kind "Alternative or replica model". Rowboat gets the use case general purpose, because its Jev decisions route chat drafts and never pick a model, so the model routing label of the majority vote does not apply.

Integration types among the verified projects: classifier component 29, model provider 28, model or tool router 12, guardrail or gate 11, agent middleware or plugin 9, other 5, reranker or retrieval 5, model catalog or configuration entry 1, agent skill or prompt 1. Status: opt-in and in a stable release 43, opt-in but merged only, in no tagged release 22, experimental or alpha 18, shipped and on by default 9, opt-in and only in a release candidate or pre-release 7, example only 1, on by default but merged only, in no tagged release 1. Category of the Jev use inside each project, or, for a library, provider, or gateway that makes no decision itself (for example pi, goose, Rig, and Bifrost), the category of the use that its documentation or examples show: Harness Engineering 44, AI Automation Software 27, Universal Verification 13, AI Map Reduce over Big Data 6, none of the five 6, Real-time applications 5.

Common patterns: a model provider or client (OpenClaw, pydantic-ai, DSPy, the Vercel AI SDK, ruby_llm, goose); a router that picks the model tier or reasoning effort per turn (LiteLLM, Codewhale, the LangChain middleware); a gate on risky tool calls or a context compactor (deer-flow, LiteLLM, Hermes Agent evaluation); a classification step in a workflow or business tool (Twenty, AutoGPT, Apache Airflow, Chatwoot); and a reranker (OpenViking). A few projects (Unsloth Studio, colibri) serve their own Jev-compatible endpoint that a local model answers, so Jev itself is not called. PostHog's product features call JevK5, an open replica that PostHog hosts itself, and its TypeSafe client has no caller on master. MCPJam Inspector calls Jev only from its private backend.

| Project | Stars | Integration | What Jev decides | Status | Category |
| --- | ---: | --- | --- | --- | --- |
| [`openclaw/openclaw`](https://github.com/openclaw/openclaw) | 390,656 | Official TypeSafe plugin that registers Jev as OpenClaw's decision model provider. | Choice, score, and yes/no questions an agent or plugin asks, for example support routing. | shipped, opt-in | Harness Engineering |
| [`NousResearch/hermes-agent`](https://github.com/NousResearch/hermes-agent) | 249,467 | Evaluation-only context compaction arm, plus catalog pointers to 14 community Jev plugins. | Whether each tool call and its result stay during compaction; scorecard says do not adopt. | experimental, evaluation only | Harness Engineering |
| [`Significant-Gravitas/AutoGPT`](https://github.com/Significant-Gravitas/AutoGPT) | 187,589 | Seven Jev workflow blocks in the AutoGPT Platform, using each user's TypeSafe key. | Choice, score, yes/no, routing, best pick, and filtering decisions inside user-built agent graphs. | shipped, opt-in | AI Automation Software |
| [`langchain-ai/langchain`](https://github.com/langchain-ai/langchain) | 147,158 | First-party `langchain-typesafe` partner package with a classifier and two experimental agent middlewares. | Caller-supplied classifications, which chat model handles an agent run, and which tool calls are risky. | alpha release on PyPI | Harness Engineering |
| [`Shubhamsaboo/awesome-llm-apps`](https://github.com/Shubhamsaboo/awesome-llm-apps) | 139,981 | Two example applications: Needle semantic find-in-page and Ripple Google Docs conflict checker. | Which page passages match a search, and which document sentences conflict with an edit. | example only | AI Map Reduce over Big Data |
| [`earendil-works/pi`](https://github.com/earendil-works/pi) | 109,753 | Built-in TypeSafe classifier provider and `classify()` method in the pi-ai model library. | Nothing in pi itself; library users call it for choice, score, and yes/no questions. | merged, opt-in, not yet released | Harness Engineering |
| [`TauricResearch/TradingAgents`](https://github.com/TauricResearch/TradingAgents) | 108,900 | Direct HTTP client that screens social posts for the Sentiment Analyst agent. | Which StockTwits and Reddit posts are on topic, and their bullish, bearish, or neutral stance. | shipped in `v0.5.1`, opt-in | Harness Engineering |
| [`koala73/worldmonitor`](https://github.com/koala73/worldmonitor) | 87,472 | Shadow second labeler for headline threat levels, plus a maintainer internal-link tool. | Headline threat level in shadow only; internal links and related reading on built pages. | merged, opt-in, shadow mode, not yet released | Universal Verification |
| [`bytedance/deer-flow`](https://github.com/bytedance/deer-flow) | 83,050 | Opt-in TypeSafe guardrail provider for tool calls, plus memory gates and example plugins. | Whether to deny a risky tool call, and whether a conversation batch merits memory extraction. | merged, opt-in, not yet released | Harness Engineering |
| [`unslothai/unsloth`](https://github.com/unslothai/unsloth) | 76,870 | Unsloth Studio serves its own Jev-compatible endpoint, answered by a local Laya model. | Nothing; a local Laya model answers Jev-format questions, and Studio uses none of them. | merged, opt-in, not yet released | None of the five categories |
| [`virattt/ai-hedge-fund`](https://github.com/virattt/ai-hedge-fund) | 63,771 | Opt-in TypeSafe provider that runs investor-persona agents on Jev typed questions. | Each persona's bullish, bearish, or neutral signal and its conviction strength per ticker and date. | shipped in `2.3.0`, opt-in | AI Automation Software |
| [`BerriAI/litellm`](https://github.com/BerriAI/litellm) | 59,728 | Jev complexity router, tool-result compaction guardrail, and TypeSafe pass-through in LiteLLM. | Which model tier serves an `auto` request, and which unneeded tool results are dropped. | shipped, opt-in | Harness Engineering |
| [`twentyhq/twenty`](https://github.com/twentyhq/twenty) | 57,572 | A "Classify (Jev)" workflow step in Twenty that sends typed questions to Jev. | Choice, score, and boolean answers that later workflow steps use to route or branch records. | shipped since `v2.42.0`, opt-in | AI Automation Software |
| [`aaif-goose/goose`](https://github.com/aaif-goose/goose) | 54,712 | Generic decision provider in the goose provider library, exposed through the goose Development Kit. | Nothing inside goose; developers call it for typed yes/no, choice, and score questions. | alpha, library only | Harness Engineering |
| [`apache/airflow`](https://github.com/apache/airflow) | 46,993 | Optional `typesafe` extra in `apache-airflow-providers-common-ai`, plus classifier retry and branch gates from follow-up pull requests. | Task failure category for retries, which downstream task to branch to, and text labels. | merged, opt-in, release candidate `0.10.0rc1` only | AI Automation Software |
| [`sickn33/agentic-awesome-skills`](https://github.com/sickn33/agentic-awesome-skills) | 46,993 | Maintainer-only script that asks Jev five review questions about each changed skill directory. | Advisory security, provenance, and priority flags that rank pull requests for inspection; never merges or blocks. | shipped, opt-in, advisory only | Universal Verification |
| [`siyuan-note/siyuan`](https://github.com/siyuan-note/siyuan) | 46,534 | Optional decision model setting and an agent-only decision tool that call Jev. | Batch classification, filtering, option picks, and scores for note blocks that the main agent delegates. | shipped in `v3.8.5`, off by default | AI Map Reduce over Big Data |
| [`Hmbown/Codewhale`](https://github.com/Hmbown/Codewhale) | 41,030 | Opt-in decision router for Auto model routing in a Rust terminal coding agent. | The fast or strong model tier and the thinking level for each Auto turn. | merged, opt-in, not yet released | Harness Engineering |
| [`tinyhumansai/openhuman`](https://github.com/tinyhumansai/openhuman) | 40,140 | Jev tool-search ranker and a Jev browser controller in a Rust agent harness. | Which shortlisted deferred tool fits each tool search, and each next browser action. | shipped, on by default; browser opt-in | Harness Engineering |
| [`PostHog/posthog`](https://github.com/PostHog/posthog) | 39,974 | TypeSafe egress client with no caller on master, plus a self-hosted copy of the open JevK5 replica (`posthog/hogference/jevk5-fp8-0.2`, see section 8) that several product features call. | Nothing through TypeSafe on master. The JevK5 copy decides signal actionability and safety, emoji picks, filter tabs, event matches, and replay reranking. | experimental, mostly behind feature flags | Real-time applications |
| [`Yeachan-Heo/oh-my-claudecode`](https://github.com/Yeachan-Heo/oh-my-claudecode) | 39,375 | Jev client and shadow judgment resolver inside a Claude Code orchestration plugin's hooks. | Nothing; Jev answers are only logged in shadow mode. Of the 10 declared judgment points, only 6 have a caller outside tests, all behind the TypeScript hook bridge, and default installs send no requests. | experimental, shadow only | Harness Engineering |
| [`volcengine/OpenViking`](https://github.com/volcengine/OpenViking) | 38,790 | `JevRerankClient` rerank provider in OpenViking, a context database for agents. | Relevance of each retrieved candidate to the query in `search()` thinking mode, not `find()`. | merged, opt-in, not yet released | Harness Engineering |
| [`stanfordnlp/dspy`](https://github.com/stanfordnlp/dspy) | 38,378 | Experimental `dspy.experimental.TypeSafe` language model client, installed through the `dspy[typesafe]` extra. | Yes/no, score, and choice output fields of a `Predict` program; thresholds apply locally. | experimental, released in `3.4.0` | AI Automation Software |
| [`JustVugg/colibri`](https://github.com/JustVugg/colibri) | 37,931 | Local `/v1/systemone` route that copies Jev's contract, in the Python gateway of a pure C model engine. | Nothing; the locally served model answers Jev-format yes/no, choice, and score questions. | shipped, on by default | None of the five categories |
| [`chatwoot/chatwoot`](https://github.com/chatwoot/chatwoot) | 37,242 | Captain Classifier and Enterprise Conversation Monitors call Jev through OpenRouter. | Suggested labels, conversation priority, and whether conversations match natural-language monitor conditions for reports. | merged, opt-in, not yet released | AI Automation Software |
| [`esengine/DeepSeek-Reasonix`](https://github.com/esengine/DeepSeek-Reasonix) | 35,705 | Go client and opt-in `system_one` tool in the Reasonix Studio terminal coding agent. | Typed questions the main chat model asks through the tool, plus a connectivity probe. | shipped, opt-in, pre-release builds | Harness Engineering |
| [`can1357/oh-my-pi`](https://github.com/can1357/oh-my-pi) | 33,479 | Coding agent with TypeSafe as the native provider for its `judge` role | Reasoning effort, unexpected stops, semantic `find` results, rule warnings, and which git changes to stage | shipped; on by default with a TypeSafe or OpenRouter key for `find` and git staging, the rest opt-in | Harness Engineering |
| [`zeroclaw-labs/zeroclaw`](https://github.com/zeroclaw-labs/zeroclaw) | 32,904 | Standard operating procedure engine calls Jev as its default decision model | Whether a matched event starts a procedure, and which execution mode the run uses | merged, opt-in, not yet released | AI Automation Software |
| [`agentscope-ai/agentscope`](https://github.com/agentscope-ai/agentscope) | 32,454 | Provider-independent classifier layer whose only backend is `JevClassifierModel` | Typed questions the developer defines, and which chat model handles each agent reply | merged, opt-in, not yet released | Harness Engineering |
| [`davila7/claude-code-templates`](https://github.com/davila7/claude-code-templates) | 31,985 | Six opt-in Claude Code hook plugins that call Jev over HTTP | Model tier and effort, skill suggestions, prompt safety, tool permissions, Bash sandboxing, and chess moves | merged, opt-in, early access, not yet released | Harness Engineering |
| [`ComposioHQ/composio`](https://github.com/ComposioHQ/composio) | 30,337 | First-party TypeSafe (Jev) provider for Composio tools in TypeScript and Python | Which tool to call, some arguments, whether to act now, and vetoes of mismatched calls | shipped, opt-in | Harness Engineering |
| [`simstudioai/sim`](https://github.com/simstudioai/sim) | 29,741 | TypeSafe native model provider for the workflow Agent block | Only the typed questions that workflow builders write; Sim hard-codes no Jev decision | shipped, opt-in | AI Automation Software |
| [`assafelovic/gpt-researcher`](https://github.com/assafelovic/gpt-researcher) | 29,649 | Jev is the default context filter for scraped research passages | Which scraped passages reach the writer model, by a usefulness score from 0 to 3 | shipped, default with TypeSafe key | Harness Engineering |
| [`mlflow/mlflow`](https://github.com/mlflow/mlflow) | 28,153 | TypeSafe gateway provider, plus Jev as a judge model through `typesafe:/jev-latest` | Evaluation verdicts for built-in and custom judges, such as relevance, correctness, and safety | merged, opt-in, not yet released | Universal Verification |
| [`vercel/ai`](https://github.com/vercel/ai) | 26,993 | Official `@ai-sdk/typesafe-ai` provider for `experimental_evaluate`, plus a gateway model identifier | Named choice, score, and boolean questions about one state; application code sets thresholds | experimental, published on npm | AI Automation Software |
| [`dubinc/dub`](https://github.com/dubinc/dub) | 24,834 | Jev screens destination URLs before Dub creates short links on dub.sh and dub.link | Whether a URL is malicious: above 0.5 blocks the link, above 0.8 blacklists the domain | shipped, on by default | AI Automation Software |
| [`different-ai/openwork`](https://github.com/different-ai/openwork) | 23,758 | Testkit verification-dictionary compiler, plus an inert pull request coverage advisory | Which dictionary checks a natural-language intent asks for, and whether the dictionary covers it | experimental, opt-in | AI Automation Software |
| [`comet-ml/opik`](https://github.com/comet-ml/opik) | 22,260 | Observability integration that traces and prices Jev System One calls | None inside Opik; Opik only logs Jev calls that users make | shipped, opt-in | None of the five categories |
| [`pydantic/pydantic-ai`](https://github.com/pydantic/pydantic-ai) | 20,214 | Jev model provider (`TypeSafeModel`) that fills structured agent outputs with typed questions | Structured output fields, and which tool or output route the text calls for | shipped, opt-in extra | Harness Engineering |
| [`1jehuang/jcode`](https://github.com/1jehuang/jcode) | 20,169 | Rust terminal coding agent with a shared Jev decisions client | Which memories to recall, the next browser action, and voice intents (library only) | shipped, default when credentials exist | Harness Engineering |
| [`confident-ai/deepeval`](https://github.com/confident-ai/deepeval) | 18,469 | First-class TypeSafe provider that lets Jev judge evaluation metrics in Python and TypeScript | Verdicts for supported built-in metrics (not `GEval`), classifier labels, and custom `JevEval` questions | shipped, opt-in | Universal Verification |
| [`vercel-labs/json-render`](https://github.com/vercel-labs/json-render) | 18,334 | Experimental functions that compose user interface specifications from application-supplied element candidates | Root component, which candidates to include, their positions, and each follow-up edit operation | experimental, in npm `0.21.0` | Real-time applications |
| [`langchain-ai/langchainjs`](https://github.com/langchain-ai/langchainjs) | 18,232 | `@langchain/typesafe` package with `TypeSafeClassifier` and two experimental agent middlewares | Developer-defined typed questions, which chat model a run uses, and whether tool calls are risky | shipped, opt-in; middleware unreleased | Harness Engineering |
| [`rowboatlabs/rowboat`](https://github.com/rowboatlabs/rowboat) | 17,982 | A TypeScript Jev client powers two Spaces chat features: Auto routing and `/find` | Whether a draft is a new message or a thread reply, mention suggestions, and search matches | merged, opt-in, not yet released | Real-time applications |
| [`lidge-jun/opencodex`](https://github.com/lidge-jun/opencodex) | 16,454 | Provider proxy for Codex and Claude Code with an opt-in `jev` Combo routing strategy | The first target model and reasoning effort for each call, from an operator allowlist | shipped, opt-in | Harness Engineering |
| [`Effect-TS/effect`](https://github.com/Effect-TS/effect) | 16,240 | `@effect/ai-typesafe` package that backs Effect's `DecisionModel` service with Jev | Classification labels, ratings, and yes or no probabilities for an Effect program's named decisions | opt-in, release candidate, marked unstable | AI Automation Software |
| [`pipecat-ai/pipecat`](https://github.com/pipecat-ai/pipecat) | 15,925 | First-party `JevClassifier` backend for Pipecat's `BaseClassifier` interface in voice agents | Voicemail versus conversation, `UIWorker` screen decisions, and evaluation judge verdicts | shipped, opt-in extra | Real-time applications |
| [`semantica-agi/semantica`](https://github.com/semantica-agi/semantica) | 13,494 | Decision-only `Jev` and `AsyncJev` provider classes in `semantica.llms` | Routing, classification, binary checks, and rubric scores about caller-supplied application state | experimental, merged, not yet released | AI Automation Software |
| [`elie222/inbox-zero`](https://github.com/elie222/inbox-zero) | 12,354 | Decision model layer whose only provider is a TypeSafe adapter for email processing | Email rule choice, cold emails, sender categories, thread status, sender patterns, reply memories, unsubscribe pages | merged, opt-in, not yet released | AI Automation Software |
| [`Arize-ai/phoenix`](https://github.com/Arize-ai/phoenix) | 11,635 | TypeScript classification evaluators accept Jev as a model through the Vercel AI SDK | The evaluator label, for example hallucinated or grounded, as one choice question | shipped, opt-in | Universal Verification |
| [`EKKOLearnAI/ekko-studio`](https://github.com/EKKOLearnAI/ekko-studio) | 11,214 | Per-profile TypeSafe settings for optional Jev decisions in a multi-agent workspace | Memory recall and write review, skill routing, learning preflight, browser element matches, and outcome checks | merged, opt-in, not yet released | Harness Engineering |
| [`yihong0618/bilingual_book_maker`](https://github.com/yihong0618/bilingual_book_maker) | 9,811 | `JevBackend` plan-mode classifier for bilingual ebook translation | Whether each tag signature is book content to translate or text to skip | shipped, opt-in | AI Automation Software |
| [`getsentry/sentry-javascript`](https://github.com/getsentry/sentry-javascript) | 8,745 | Sentry tracing records Vercel AI SDK Jev evaluate calls as spans. | None. Sentry only observes Jev calls that user applications make. | experimental upstream application programming interface (`experimental_evaluate`), merged, not yet released | None of the five categories |
| [`0xPlaygrounds/rig`](https://github.com/0xPlaygrounds/rig) | 8,745 | Provider crate `rig-typesafeai` with a typed Jev client and two example packages. | Library decides nothing. Examples: support route and clarification need, which pick the chat agent's policy. | experimental, opt-in feature | Harness Engineering |
| [`maximhq/bifrost`](https://github.com/maximhq/bifrost) | 8,394 | Open-source gateway with a built-in TypeSafe provider that forwards decision requests to Jev. | None for Bifrost itself. Gateway clients send their own questions, for example support-ticket triage. | shipped, opt-in | AI Automation Software |
| [`YaoApp/yao`](https://github.com/YaoApp/yao) | 8,035 | TypeSafe decision provider plus a built-in `decision_decide` agent tool and skill. | Typed answers for agents: ticket routing, quality grades, intent, sentiment, urgency probability. | opt-in, release candidates only (`v1.0.0-rc23` and later) | AI Automation Software |
| [`2FastLabs/agent-squad`](https://github.com/2FastLabs/agent-squad) | 7,774 | Built-in `JevClassifier` in TypeScript and Python that routes each user turn to an agent. | Which registered agent handles each user input, or unknown, with calibrated confidence. | shipped, opt-in | Harness Engineering |
| [`opengeos/GeoLibre`](https://github.com/opengeos/GeoLibre) | 7,700 | Desktop map assistant uses Jev for a fast command path and Whitebox tool search. | Which simple map command to run without the agent, and which Whitebox tools to shortlist. | shipped, opt-in | Harness Engineering |
| [`AIPentest/CyberStrikeAI`](https://github.com/AIPentest/CyberStrikeAI) | 7,049 | Go TypeSafe client used as an optional backend for the human-in-the-loop audit agent. | Whether each non-allowlisted tool call is approved or rejected, based on destructive-risk answers. | shipped, opt-in | Harness Engineering |
| [`anomalyco/models.dev`](https://github.com/anomalyco/models.dev) | 7,018 | Model metadata database lists Jev under five providers, in six model entries, as a new `decision` model type. | None. The project stores Jev metadata and never calls Jev. | shipped, opt-in | None of the five categories |
| [`tbphp/gpt-load`](https://github.com/tbphp/gpt-load) | 6,994 | Self-hosted gateway with a built-in Jev Decisions channel and two experimental Jev features. | Which model preset serves each automatic request, and whether prompt content breaks guardrail rules. | experimental, off by default | Harness Engineering |
| [`BuilderIO/agent-native`](https://github.com/BuilderIO/agent-native) | 6,863 | Optional Jev prefetch in the agent framework core, plus Jev rules in template applications. | Tools, skills and memories to preload; mail rule matches; privacy flags; invitation rules; duplicate records. | shipped, opt-in | Harness Engineering |
| [`jev-chat/jev-chat-jarvis`](https://github.com/jev-chat/jev-chat-jarvis) | 6,770 | Android chat overlay that uses Jev to judge on-screen chats and rank replies. | Intent, danger level, needs, best action, tension resolved, and the ranking of 3 candidate replies. | shipped, on by default | Real-time applications |
| [`GreptimeTeam/greptimedb`](https://github.com/GreptimeTeam/greptimedb) | 6,715 | Experimental SQL functions `ai_match`, `ai_choose` and `ai_score` that call Jev per row. | Per row: whether a statement holds, which option label fits, and a level rating. | experimental, environment flag required | AI Map Reduce over Big Data |
| [`ThinkInAIXYZ/deepchat`](https://github.com/ThinkInAIXYZ/deepchat) | 6,347 | Electron assistant with a `jev` provider protocol and an opt-in per-agent judgment model. | Tool-permission risk in auto-approve mode, and which stale tool results to prune from context. | shipped in prerelease, opt-in | Harness Engineering |
| [`apache/camel`](https://github.com/apache/camel) | 6,345 | First-party `camel-typesafe-ai` component and predicate language for Jev questions in integration routes. | Semantic route decisions: content-based routing, team classification, and scoring such as urgency. | experimental | AI Automation Software |
| [`oomol-lab/pdf-craft`](https://github.com/oomol-lab/pdf-craft) | 6,333 | Scanned-book PDF converter uses Jev to review each page's footnote analysis. | Whether each page passes a strict standard; risky pages go to large language model repair. | merged, opt-in, not yet released | Universal Verification |
| [`Human-Agent-Society/reef`](https://github.com/Human-Agent-Society/reef) | 6,328 | Agent rules tell the reefine agent to call Jev from pi extensions it writes. | Only in generated extensions: tool-call risk, stuck or done checks, selection, routing, context pruning. | shipped, opt-in | Harness Engineering |
| [`oomol-lab/open-connector`](https://github.com/oomol-lab/open-connector) | 5,905 | TypeSafe AI provider in an authentication gateway, with agent-callable list-models and evaluate actions. | None. It passes caller questions through to Jev and returns answers unchanged. | shipped, opt-in | None of the five categories |
| [`agentscope-ai/agentscope-java`](https://github.com/agentscope-ai/agentscope-java) | 5,805 | Optional Java extension with a typed Jev client, Spring Boot starter, and three middlewares. | In the reference middlewares: tool selection, model routing, and tool-call safety for agents. | opt-in, on main, unreleased | Harness Engineering |
| [`vercel/eve`](https://github.com/vercel/eve) | 5,388 | Agent framework whose `evaluate` wrapper defaults to Jev through the Vercel AI Gateway. | Per-turn model choice, tool-call approval, subagent routing, and evaluation judging. | shipped, opt-in | Harness Engineering |
| [`FailproofAI/failproofai`](https://github.com/FailproofAI/failproofai) | 5,178 | Coding-agent policy hooks: regular expression policies first, then a Jev review. | Risk of each gated tool call; can clear reviewable denies or add its own. | shipped, opt-in | Harness Engineering |
| [`Kiln-AI/Kiln`](https://github.com/Kiln-AI/Kiln) | 5,111 | Native TypeSafe AI model provider with a `JevAdapter` and a built-in Jev 1.13 model. | Outputs of single-turn structured tasks, and pass, fail, or star ratings as a judge. | merged, opt-in, not yet released | Universal Verification |
| [`langwatch/langwatch`](https://github.com/langwatch/langwatch) | 4,881 | Jev is the judge behind the classifier interface for instant evaluations over traces. | Customer-written yes, score, and category judgments about each conversation, trace, or model call. | experimental | AI Map Reduce over Big Data |
| [`latitude-dev/latitude-llm`](https://github.com/latitude-dev/latitude-llm) | 4,686 | Private package `@platform/ai-jev` powering an opt-in Jev preclassifier for session flaggers. | Which of 11 failure-mode flaggers get full classification for a sampled-out agent session. | shipped, opt-in | Universal Verification |
| [`crmne/ruby_llm`](https://github.com/crmne/ruby_llm) | 4,417 | Built-in TypeSafe provider backing a typed judgment feature, `RubyLLM::Judge`, defaulting to Jev. | Library decides nothing. Documentation examples: ticket urgency, department routing, customer frustration. | opt-in, on main, unreleased | AI Automation Software |
| [`TanStack/ai`](https://github.com/TanStack/ai) | 3,141 | `decide()` evaluate activity with four Jev adapters, including `@tanstack/ai-typesafe` | Answers typed questions that application code branches on; picks subagents and their order per turn | shipped, opt-in, published on npm | Harness Engineering |
| [`spiceai/spiceai`](https://github.com/spiceai/spiceai) | 3,094 | Rust evaluation model provider `typesafe:jev` behind a new `POST /v1/evaluate` endpoint | None inside Spice; answers typed questions that application code sends through the runtime | merged, opt-in, not yet released | AI Automation Software |
| [`ianarawjo/ChainForge`](https://github.com/ianarawjo/ChainForge) | 3,030 | Jev judge through OpenRouter in the large language model scorer node | Grades each scored item with a noul, choice, or score answer, compared against human labels | shipped, opt-in | Universal Verification |
| [`elie222/rakazo`](https://github.com/elie222/rakazo) | 3,003 | Optional Jev Auto Review checker for consequential bot tool calls | Whether a bot tool call auto-passes or asks the user for approval | merged, opt-in, not yet released | Universal Verification |
| [`ax-llm/ax`](https://github.com/ax-llm/ax) | 2,946 | `typesafe` provider and native client mapping signature outputs to Jev questions | Required boolean and class output fields of an Ax signature, for example ticket triage | shipped, opt-in | AI Automation Software |
| [`erupts/erupt`](https://github.com/erupts/erupt) | 2,872 | `erupt-ai-decision` module with Jev as a built-in System One provider | Typed answers about a business state that Java code thresholds for escalation or routing | shipped in `2.3.0`, opt-in | AI Automation Software |
| [`HarleyCoops/Math-To-Manim`](https://github.com/HarleyCoops/Math-To-Manim) | 2,666 | Jev as an independent text evaluator at every animation pipeline checkpoint | Pass or reject at each checkpoint, the repair target, and the next investigation tool | merged, on by default, advisory, not yet released | Universal Verification |
| [`xerj-org/xerj`](https://github.com/xerj-org/xerj) | 2,510 | Optional `rerank` search stage that reorders hits by Jev probabilities | Probability that each top search hit answers the query; this sets the new order | opt-in, release candidates only (`v1.0.0-rc.75` and later) | AI Map Reduce over Big Data |
| [`lioensky/VCPToolBox`](https://github.com/lioensky/VCPToolBox) | 2,333 | Shared Node.js Jev client used in five agent middleware features | Tool folding, context pruning, context and memory reranking, and an experimental virtual tool | merged, off by default, not yet released | Harness Engineering |
| [`MCPJam/inspector`](https://github.com/MCPJam/inspector) | 2,222 | Advisory Rubric checks judge for hosted evaluation suites, run in a private backend | Yes or no per grading criterion, plus choice or score answers to authored questions | shipped, on by default, advisory | Universal Verification |
| [`oficcejo/aiagents-stock`](https://github.com/oficcejo/aiagents-stock) | 1,958 | Opt-in Jev structured decision engine for a multi-agent stock analysis system | Final stock rating, major-risk flag, four scores, and news stance, urgency, and relevance | merged, opt-in, not yet released | AI Automation Software |
| [`szczyglis-dev/py-gpt`](https://github.com/szczyglis-dev/py-gpt) | 1,952 | Built-in `jev_evaluate` tool plugin that lets the chat model call Jev | No fixed decisions; answers questions the chat model writes at run time | shipped in `2.8.31`, off by default | Harness Engineering |
| [`Paca-AI/paca`](https://github.com/Paca-AI/paca) | 1,865 | Go Jev client used for four project management features, with per-project keys | Agent routing, task field auto-fill, task assignee, and automation workflow branches | shipped, opt-in per project | AI Automation Software |
| [`nimbalyst/nimbalyst`](https://github.com/nimbalyst/nimbalyst) | 1,787 | Alpha knowledge curator that sorts work events, plus an `ask_jev` Model Context Protocol tool | Whether a work event is knowledge, its wiki area, target item, and contradicted claims | experimental, alpha | AI Automation Software |
| [`LLPhant/LLPhant`](https://github.com/LLPhant/LLPhant) | 1,710 | `JevClassifier` class for typed questions in a PHP framework | Typed questions that applications ask directly, for example support message triage | shipped, opt-in | AI Automation Software |
| [`theopenco/llmgateway`](https://github.com/theopenco/llmgateway) | 1,663 | TypeSafe catalog provider, `/v1/systemone` proxy, and Jev inside the gateway pipeline | Moderation category flags for the content filter, and request difficulty for smart model routing, a beta feature of the hosted gateway that is newer than the latest release | shipped, opt-in | Harness Engineering |
| [`antvis/AVA`](https://github.com/antvis/AVA) | 1,571 | Opt-in subset analysis strategy that uses Jev to trim text-to-SQL context | Which table and column profile statistics are relevant to the user question | experimental, opt-in | Harness Engineering |
| [`agentconnect-md/agentconnect`](https://github.com/agentconnect-md/agentconnect) | 1,436 | TypeSafe as the only provider behind the reusable Decisions feature | Agent activation, conversation and code-host routing, session runtime and model, and repositories | opt-in, release candidate `v1.61.0-rc.*` tags only | Harness Engineering |
| [`remorses/kimaki`](https://github.com/remorses/kimaki) | 1,424 | OpenCode plugin `@kimaki/automode` that gates pending agent tool calls with Jev | Whether one pending tool call may run automatically; fails closed on errors | shipped in `0.2.0`, opt-in | Harness Engineering |
| [`mohitagw15856/pm-claude-skills`](https://github.com/mohitagw15856/pm-claude-skills) | 1,407 | Vendor-neutral Jev decision layer with a zero-dependency client and hosted worker | By design: skill routing, input guards, crisis routing, decision contracts, and quality gates. No real Jev answers yet: a Claude adapter (`claude-haiku-4-5`) answers everything while TypeSafe sign-ups are closed and the Vercel AI Gateway and Cloudflare Workers AI need a payment method | shipped, opt-in | Harness Engineering |
| [`astaxie/TokenHub`](https://github.com/astaxie/TokenHub) | 1,343 | Jev Smart Routing strategy, TypeSafe provider plugin, and `/v1/systemone` gateway endpoint | Which administrator-configured upstream model serves each chat or Responses request | shipped, opt-in | Harness Engineering |
| [`heymrun/heym`](https://github.com/heymrun/heym) | 1,290 | Decision Model credential and Decision workflow node backed by Jev | Workflow branch answers, the large language model per request, and evaluation judge scores | shipped, opt-in | AI Automation Software |
| [`caliber-ai-org/ai-setup`](https://github.com/caliber-ai-org/ai-setup) | 1,287 | Jev compaction plugin and `caliber compact` command for agent session transcripts | Which tool calls and results to keep, truncate, or drop during compaction | shipped in `1.54.0`, opt-in | Harness Engineering |
| [`databuddy-analytics/Databuddy`](https://github.com/databuddy-analytics/Databuddy) | 1,165 | `@databuddy/scan` command-line tool and insights application that classify with Jev | Event coverage, product category, and priority per code segment; investigation candidate pre-filtering | shipped, on by default | AI Map Reduce over Big Data |
| [`webbrain-one/webbrain`](https://github.com/webbrain-one/webbrain) | 1,120 | Opt-in Jev assistive model in an open-source browser agent, pinned to `jev-1.13.0` | Whether reported scheduler successes are real, plus fast classifications and experimental browser actions | shipped in `36.8.0`, opt-in | Universal Verification |

## 8. Open-weight alternatives and replicas

Many projects offer an open-weight or self-hosted model that answers the same Choice, Score, and Noul questions, often through the same `POST /v1/systemone` wire format so the official client libraries work unchanged. 10 of them were chosen before the full classification (the 6 that the first research pass named and the 4 large replica projects that code search returned) and checked against every independent measurement that could be found. They are not the 10 with the most stars: `vinnylarouge/jevlike` (1,318 stars) and `feder-cr/jev` (1,053 stars) each have more stars than 5 of the 10, and decider, which ranks above Jev on JevBench release `v1.4.2.2`, was found later by the completeness check (section 12). The measurements came from JevBench (fstandhartinger/jevbench, run by Benchmark Heaven, with 308 sealed items), jabr/classifier-benchmark (944 locked synthetic cases, Jev called through OpenRouter), the Jev Decision Index (a Hugging Face Space), DecisionBench, and blog measurements.

9 of the 10 have a published independent accuracy result, and Jev scores higher in each of them with one exception (for Kev and openJev-verdict-2.0, outsiders measured only older or smaller models, not the headline Kev-27B or Verdict 2.0). The exception: an outside run (Luni/laya-jev-benchmark) reproduced about 0.767 for the Laya checkpoint fine-tuned on the train split of the LocalLLaMA/typed-decisions set, above the 0.727 that the dataset author measured for Jev without fine-tuning, and the dataset card says that the two modes are not comparable. NanoJev has no independent comparison with Jev, and an unpublished JevBench draft (`v1.4.3`, closed without merge) put JevK5 `v0.3` above Jev. Jev scored 0.967 combined micro accuracy on jabr/classifier-benchmark, where the best open model reached about 0.75, and 36.7% on the sealed JevBench items (chance is 29.3%), where several replicas scored at or below chance. The JevBench composite score also weighs cost and speed, and its release `v1.4.2.2` (GitHub, 2026-09-27, 91 ranked systems) places three open models (Imajev-4B, Plumb-4B, and decider-4b v2) above Jev, which is 4th; section 12 confirms that ranking and the decider family. Unless a cell names another JevBench release, each rank in the Independent measurements column comes from release `v1.4.2.1` (Jev 3rd of 90 ranked), which the benchmarkheaven.com board showed earlier on 2026-09-27. Later that day the board moved to `v1.4.2.2`, which adds Imajev-4B at the top and leaves every score unchanged, so each of those ranks is one place lower on the live board. The ranks in the Main claim versus Jev column are what each project says about itself. On the Jev Decision Index, whose headline number is a chance-corrected skill score over a 38-benchmark panel (Jev 57.91), not an accuracy, no entrant scores above Jev.

| Project | What it is | Main claim versus Jev | Independent measurements | Verdict |
| --- | --- | --- | --- | --- |
| [`wfzyx/von`](https://github.com/wfzyx/von) | Fine-tune of ModernBERT-large, about 395 million parameters, non-autoregressive encoder scoring one mask marker per option. | Sub-15 millisecond local drop-in alternative to Jev; 9.00 ViZDoom kills against Jev's 5.62; about 18 against 115 milliseconds. | Benchmark Heaven's JevBench: 27.5 (rank 47) against Jev's 63.3 (rank 3); sealed accuracy 27.9%, below chance. jabr: 0.742 against 0.967, with 48 of 78 version 1 cases in training data. | claims contradicted by independent evidence |
| [`allebee/jevk5`](https://github.com/allebee/jevk5) | Qwen3.5-4B with a merged rank-16 Low-Rank Adaptation; reads answer-letter logits in one forward pass, no generated tokens. | The README says JevBench release 1.4 ranks version 0.2 second of 76 systems and first among open entrants, at 62.04 against Jev's 63.29 (Jev first); sealed accuracy 33.1% against 36.7%. | The JevBench evaluator's re-run confirms 62.04 against 63.29 and sealed 33.1% against 36.7%. multimodalart's Jev Decision Index: skill 38.81 against Jev's 57.91; Jev leads every category. | claims supported by independent evidence |
| [`Heman10x-NGU/openJev-verdict-2.0`](https://github.com/Heman10x-NGU/openJev-verdict-2.0) | ModernBERT-base, 149.6 million parameters, per-option mask-marker head, supervised fine-tune on the typed-decisions train split. | Verdict 2.0 beats Jev 1.13.0 on typed-decisions: 77.10% against 72.70% top-1 accuracy, Brier 0.0636 against 0.1480. Leaderboard images that the project drew itself show version 1.4 ranked second on JevBench at 74.9 against Jev's 75.4. | Nobody outside the project measures Verdict 2.0; its weights are not downloadable, and the dataset card says that a fine-tuned specialist and a zero-shot Jev are not comparable. Benchmark Heaven ranks version 1.4 59th (19.0 against Jev's 63.3); Hanno-Labs DecisionBench: version 1 at 0.284 against 0.720. | claims contradicted by independent evidence |
| [`Heman10x-NGU/Verdict-open-jev`](https://github.com/Heman10x-NGU/Verdict-open-jev) | ModernBERT-base with a GLiClass bi-encoder head, 151 million parameters, supervised fine-tune on Banking77 and CLINC150 data. | Leaderboard images that the project drew itself show version 1.4 ranked second on JevBench at 74.9 against Jev's 75.4, with decisions under 35 milliseconds. | Benchmark Heaven: 19.0, rank 59, against Jev's 63.3, rank 3; sealed accuracy 27.9%, below chance. Hanno-Labs DecisionBench: 28.43% against 72.03%. umstek's `zero-shot-ie-bench`: 39.6% against 93.8%. | claims contradicted by independent evidence |
| [`TheoLeeCJ/SemIf-OpenJev`](https://github.com/TheoLeeCJ/SemIf-OpenJev) | No training or weights; prompts frozen Qwen3.5-4B and reads answer-letter logits in one forward pass. | On a 102-row TypeSafe public subset, agreement is 0.845 against Jev's 0.883; says it does not establish near-Jev capability. | Benchmark Heaven JevBench: rank 12 at 47.7 against Jev's rank 3 at 63.3; sealed 26.3% against 36.7%. JevBench version 1.2: hard tier 59.5% against 74.1%. | claims supported by independent evidence |
| [`logan-markewich/jeff`](https://github.com/logan-markewich/jeff) | Server implementing Jev's wire format on third-party zero-shot GLiFormer-large (575.6 million parameters); no fine-tuning, one fitted temperature. | Self-hosted drop-in Jev replacement, cheaper but less accurate: about $2.6 against $15.6 per million requests; JevBench release `v1.2.2`: 66.9 against 75.3. | JevBench maintainer, release `v1.2.2`: 66.9 (rank 9 of 18) against Jev's 75.3 (rank 2), hard tier 37.7% against 74.1%. Re-run in release `v1.4.2.2`: 30.58, rank 42 of 91, against Jev's 63.29, rank 4. jabr: 0.563 against 0.967. mandu5 jevcompat: 30 of 32 required checks. | claims supported by independent evidence |
| [`NandhaKishorM/laya`](https://github.com/NandhaKishorM/laya) | ModernBERT-large encoder with a two-layer decision head, 421 million parameters, scores each option at a mask marker. | Beats Jev on typed-decisions, 0.766 against 0.727; claims expected calibration error 0.081 against 0.246 and 7.8 times faster. | An outside run (Luni/laya-jev-benchmark) measured 0.767 for the Laya checkpoint fine-tuned on the LocalLLaMA/typed-decisions train split, which reproduces the claim, above the 0.727 that the typed-decisions dataset author measured for Jev without fine-tuning; the typed-decisions dataset card says the two modes are not comparable. Every paired run finds Jev ahead: jabr 0.588 against 0.967 (0.625 for the fine-tuned checkpoint), Benchmark Heaven rank 42 against 3, Jev Decision Index 6.04 against 57.91. Faster only on a graphics processing unit. | claims contradicted by independent evidence |
| [`jaredpalmer/kev`](https://github.com/jaredpalmer/kev) | Rank-16 Low-Rank Adaptation adapter plus a pointer head on Qwen bases, 0.8 to 27 billion parameters. | On new-source transfer suites, Kev-27B is within a point of Jev, 0.848 against 0.857; Kev-4B and Kev-9B within four points. | multimodalart's Jev Decision Index: Kev-9B 38.48 and Kev-4B 34.64 against Jev's 57.91. JevBench team, older previews: hard tier 0.42 to 0.47 against 0.74. Nobody measures Kev-27B. | claims contradicted by independent evidence |
| [`TianyuCodings/NanoJev`](https://github.com/TianyuCodings/NanoJev) | Full fine-tune of Qwen3-0.6B with added choice, boolean and score heads, trained on four game tasks. | Beats Jev on ViZDoom Basic, 128 of 128 against 56 of 128, and Predict Position, 27 against 11 of 128. | No third party compares NanoJev with Jev; jabr and JevBench omit it. Third parties check only latency and file integrity. Recordings show Jev shoots on every Basic step. | self-reported only |
| [`bespokelabsai/nimble`](https://github.com/bespokelabsai/nimble) | Rank-16 Low-Rank Adaptation adapter on Qwen3.5-9B; softmax over one-token answer-code logits, no text generation. | Jev leads on the project's 324-item held-out set, 93.21% against 90.12%, and a public suite, 76.0% against 74.8%. | Benchmark Heaven JevBench: 18.7, rank 61, against Jev's 63.3; public 79.7% against 86.6%, sealed 28.9% against 36.7%. Raw median latency 0.39 against 0.65 seconds. | disputed: the investigator and 1 of 3 skeptics judged the claims contradicted by independent evidence; the other 2 skeptics judged them supported, because the project itself says Jev leads |

The final labels mark 1,136 repositories as alternative or replica models in total. Most are small and have no independent measurement.

## 9. Third-party studies of Jev and the launch discussion

This section covers 6 sources: 5 third-party studies and the Hacker News launch thread, where TypeSafe staff also took part. Two of the studies did not call Jev: the Andrew Yourtchenko post copies its Jev figures from the jabr run, and the SemIf results come from TypeSafe's own published evaluation records. The jabr benchmark started on the issue tracker of Von, a competing replica, and the SemIf author builds a competing open reproduction.

**PriorBench** (github.com/priorbench/jev, pseudonymous author, 2026-09-20). A black-box study through OpenRouter that the author calls pre-registered: 21 experiments, 5,721 calls, total cost $0.176. The pre-registration file, the code, and all raw data arrived in one commit, so nobody can check that the predictions came before the data. Zero-shot accuracy on the author's French 4-category benchmark was 95.9%, against 77.2% for keywords and 66.0% for a supervised term-frequency baseline. Confidence ranks answers but is not calibrated: accuracy above the threshold stays flat from 0.50 to 0.95 and reaches 100% only at 0.99, which covers 60.2% of traffic. Jev always answers, even on an empty string, and forces out-of-scope messages into a category at 0.99 confidence unless the question offers an "other" option. Deliberately wrong criteria descriptions drop accuracy to 16.7%, below the 25% random floor. Noul values near 0.5 are not reproducible (0.46 to 0.54 over 60 identical calls). Of the failure modes that the vendor documentation lists, the study confirmed two: counting (48% on 40 items with similar distractors) and literal reading of the criteria (the 16.7% result above). Number and date comparisons (518 of 520) and negation were better than the vendor documentation says. Median latency from Western Europe was 475 milliseconds, and 800 questions in one call took 985 milliseconds.

**Rajesh Beri, beri.net** (2026-09-20, a secondary analysis). It combines several studies. On a phishing benchmark, a single "is this phishing" question scored 62.6% against 81.3% for Claude Haiku 4.5, but five narrow questions in one call plus a logistic regression, fitted on 1,000 labelled emails and scored on the other 1,000, reached 95.0% against 93.2%, a gap that was not statistically significant (p = 0.063). Jev cost 12 times less than Haiku for the single question and 27 times less for the five questions, and its median latency was about 2.9 times lower. An out-of-distribution calibration study found an expected calibration error of 0.107 on fresh synthetic tickets, with Noul answers underconfident and Choice and Score answers overconfident, and 44.7% accuracy at a mean stated probability of 0.74 on a rule that was not in the ticket text. The article notes that the vendor headline claims measure agreement with the average of two frontier models, that "0% hallucination" is a schema guarantee, and that the launch post publishes no calibration metric, neither an expected calibration error nor a reliability curve.

**jabr/classifier-benchmark** (started in wfzyx/von issue 3, 2026-09-19 to 2026-09-27). 944 locked synthetic cases: 78 in the original 8-task suite and 866 in the new 49-task suite. Every model got the same question JSON. Jev: 0.967 combined micro accuracy, with 0.974 on the original suite and 0.967 on the new one, a drop of under 1 point, and a mean of about 330 milliseconds per case including the network. The best local models reached 0.747 (GLiNER2.5-Decide) and 0.742 (Von 1.2). The error analysis names three Jev failure modes: it over-flags borderline "no" cases (for example secret leaks at 0.875), its misses on ordinal scales are usually one level off (two misses were two levels off), and it confuses near-synonym categories.

**Andrew Yourtchenko, "Three Jev clones and a 27B"** (2026-09-20). The author ran the 78-case original suite of jabr/classifier-benchmark on four local models but did not call Jev. The Jev figures in the post are copied from the jabr run above, so this post is not a separate measurement of Jev. Against that Jev score of 0.974, the local runs scored 0.885 for a 1-bit 27-billion-parameter chat model, 0.795 for GLiNER2, 0.769 for Von, and 0.590 for Laya. Jev's only weak task in that run was an ordered severity rubric (0.778), where Von scored 1.000.

**Hacker News launch thread** (item 49717558, 2026-09-15, 1,984 points). Commenters criticised the vendor evaluation method (agreement with frontier models instead of ground truth), the absence of public benchmark results, and the "0% hallucination" chart. Hands-on reports: a browser agent made 21 to 23 correct decisions for about $0.001, a chess demo played weak moves, and code generation failed, as expected for a model that does not generate text. TypeSafe staff said there is no prompt caching and that outputs cannot be strings.

**SemIf results** (TheoLeeCJ/SemIf-OpenJev, 2026-09-18). On a selected 102-row subset of TypeSafe's public evaluation records, the published Jev answers agreed with the reference 0.883 of the time, against 0.845 for a direct readout of an open 4-billion-parameter model. The author says this does not show near-Jev capability.

## 10. Community lists

The 14 lists below were used as sources. "Linked" counts the GitHub repositories each list links that still exist and were classified. "Related" counts those whose final label is related to Jev.

| List | Linked repositories | Related to Jev |
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

Together the lists link 1,713 unique repositories that were classified. 1,631 of them are related to Jev, which is 11.1% of the 14,742 related repositories this survey found. GitHub repository search found 12,647 of the rest, and the other 464 were found only through GitHub code search.

## 11. Risks and caveats in the ecosystem

- **Account-registration tools.** The final labels put 9 repositories in the account tooling kind. 6 of them automate TypeSafe sign-up, account registration, or key creation (for example `Futureppo/typesafe_register`, `2951461586/Jev-Register-Tool`, and `dengyie/ai-register-machine`). The other 3 are a reseller store for Jev access, a monitor for key expiry and credits, and an empty placeholder. Bulk registration to get more free usage most likely breaks the TypeSafe terms of service. This report does not describe how these tools work.
- **Confusable organizations.** `typesafe-ai` is official, and `TypeSafeAI` is an unofficial community organization whose name differs only by a hyphen. 4 of the 14 community lists link its repositories, and none of them lists those repositories as official. Check the owner before you treat a repository as official.
- **Self-reported numbers.** Many READMEs publish benchmark comparisons with Jev that do not hold up. Examples found in this survey: scripted mock values presented as a benchmark, with no measurement of Jev (`TypeSafeAI/jev-harness`, which discloses it), specialist models fine-tuned on a benchmark's own train split and compared with a zero-shot Jev score that the dataset card calls not comparable, and leaderboard images drawn by the project itself (`Heman10x-NGU/Verdict-open-jev`).
- **Calibration.** Two independent studies, PriorBench and jabr/classifier-benchmark, find that Jev's confidence ranks its answers well but is loose as a probability: jabr calls the probabilities "directionally right but not tight", and on PriorBench's own benchmark, accuracy above the threshold stayed flat as the threshold rose from 0.50 to 0.95. The one out-of-distribution calibration study in this survey (scienthoon, section 12) measured an expected calibration error of 0.107 on fresh synthetic tickets, 4.4 times its noise floor, while public benchmarks that may be in the training data came out well calibrated. Only PriorBench tested inputs where no option applies, and Jev still answered them confidently. Production gates need an explicit "other" or "insufficient evidence" option and thresholds tuned on the target data.
- **Vendor claims.** TypeSafe publishes its own evaluation records with Jev's answers (the SemIf comparison in sections 8 and 9 uses 102 of them), but it publishes no results on public third-party benchmarks and no calibration metric. Its headline speed and cost ratios compare Jev with generative models on agreement, not on accuracy against ground truth.
- **Operations.** The documentation says rate limits can change without notice while TypeSafe adds capacity, and the `jev-latest` alias moves when a new release ships. Pin `jev-1.13.0` when thresholds are tuned. Section 12 lists verified reports about paused sign-ups, request shedding, the status page, and the customer agreement.
- **Prompt injection.** The state is data, and TypeSafe says Jev does not treat it as hostile by default. Section 12 lists a preprint on prompt-injection attacks against typed decisions. Gates that act on untrusted text need their own defenses.

## 12. Additional items found by the completeness check

Three critics searched for items that the earlier passes missed. Each item was read by an investigator and checked by two skeptics. The summaries below use only the facts that both skeptics confirmed, with three exceptions. The OpenRouter row compares the OpenRouter context length with the TypeSafe limits in section 1, which come from the TypeSafe documentation. The LanceDB row adds this survey's own code search result, star count, and final label for `lancedb/lancedb`. The Ollaya row adds a fact from the section 8 checks: the typed-decisions dataset author measured the 0.727 for Jev.

### Distribution partners

| Item | Date | Summary |
| --- | --- | --- |
| [Vercel: Jev fastest-adopted model in AI Gateway history](https://vercel.com/blog/ai-gateway-jev-model-launch) | 2026-09-18 | Vercel blog post dated 2026-09-18. Within 24 hours, nearly 13% of paid AI Gateway teams used Jev, 2 times the GPT-5.6 family and over 6 times Fable 5.1. Vercel notes the next test is whether adoption lasts. |
| [OpenRouter documentation hub for using Jev](https://openrouter.ai/docs/guides/community/jev) | 2026-09-24 | Model `typesafe/jev-1.13`, with alias `~typesafe/jev-latest`, is open to any OpenRouter key holder with no waitlist. OpenRouter gives the context as 32,000 tokens, which matches the TypeSafe limit for the state plus the longest question, not the 64,000 tokens per request (section 1). Input tokens are billed at $0.000000042 each, and output is free. Two endpoints: Decisions and System One. |
| [DigitalOcean Serverless Inference adds Jev](https://ideas.digitalocean.com/changelog/now-available-jev-from-typesafe-ai) | 2026-09-23 | Changelog entry published 2026-09-23 makes Jev available through DigitalOcean Serverless Inference at $42 per billion input tokens, with output free. Documentation lists model identifier `typesafe-jev-1.13.0`, with aliases `typesafe-jev-latest` and `typesafe-jev-preview` resolving to it. |
| [Cloudflare AI model catalog lists Jev](https://developers.cloudflare.com/ai/models/typesafe/jev/) | 2026-09-17 | Catalog entry `typesafe/jev`, created 2026-09-17, is labeled Third-party and Zero data retention. Price is $0.042 per million input tokens, output free, plus a 5% Unified Billing credit fee. No Cloudflare changelog feed mentions it as of 2026-09-27. |
| [Vercel AI Gateway Jev requests failing with HTTP 429](https://community.vercel.com/t/typesafe-ai-jev-requests-shed-with-429-and-providerattemptcount-0-per-team-throttling/49779) | 2026-09-26 | Unanswered forum report from 2026-09-26: almost every `typesafe-ai/jev` request to `/v1/evaluate` returns HTTP 429 with `providerAttemptCount: 0`, after over 1,500 successes on 2026-09-25. A September 19 archive of Vercel's Jev page showed free promotional pricing ending September 25. |

### Integrations in pull requests

| Item | Date | Summary |
| --- | --- | --- |
| [Langfuse pull request adds experimental Jev decision-model evaluators](https://github.com/langfuse/langfuse/pull/17733) | 2026-09-22 | Pull request 17733, merged 2026-09-22, changed 110 files with 6,322 additions behind the `decisionModelEvaluators` flag. Only bot reviewers reviewed it, and no live TypeSafe call was tested. Follow-up pull request 17798 added Vercel AI Gateway and OpenRouter upstreams. |
| [OpenInference instrumentation package for TypeSafe Jev](https://github.com/Arize-ai/openinference/pull/3773) | 2026-09-18 | Merged 2026-09-18. `TypeSafeAIInstrumentor` records `system_one` calls from `typesafe-sdk` 0.6.0 or later as OpenInference large language model spans. Version 0.1.1 reached the Python Package Index that day; pypistats counted 698 downloads in the 30 days before 2026-09-27. Feature issue #3769 stays open. |
| [Microsoft Agent Framework TypeSafe Jev provider for .NET](https://github.com/microsoft/agent-framework/pull/8563) | 2026-09-20 | Open, unmerged pull request from outside contributor joslat, created 2026-09-20: 54 files, 5,404 added lines, an experimental `IDecisionClient` and a preview `Microsoft.Agents.AI.TypeSafe` package. Build workflows have not run. Commenter mo3in wants the provider in `Microsoft.Extensions.AI`; the author agreed. |
| [Apache Airflow support for TypeSafe Jev classifier models](https://github.com/apache/airflow/pull/73363) | 2026-09-20 | Kaxil Naik merged it on 2026-09-20: 9 files, no provider code, an example directed acyclic graph, and a `typesafe` extra requiring `typesafe-sdk>=0.6.0`. The extra appears in `apache-airflow-providers-common-ai` `0.10.0rc1`, uploaded 2026-09-24; its testing issue marks nothing tested yet. |
| [Bifrost gateway TypeSafe provider and decisions endpoint](https://github.com/maximhq/bifrost/pull/7355) | 2026-09-21 | Merged 2026-09-21: 179 files, 5,920 additions, a `typesafe` provider and `POST /v1/decisions`, mirroring TypeSafe's System One endpoint, with a static Jev catalog that lists `jev-1.13.0` and its two aliases, `jev-latest` and `jev-preview`. Released in core `v1.10.0` on 2026-09-22 and HTTP transports `v2.2.2` on 2026-09-23. |
| [LanceDB TypeSafe reranker with request batching follow-up](https://github.com/lancedb/lancedb/pull/4209) | 2026-09-17 | Merged 2026-09-17, 26 minutes after opening. `TypeSafeReranker` scores each result with a yes-or-no Jev question. Pull request 4316, merged 2026-09-24, added batching: at batch size 40, 6,851 test calls became 199, and median latency fell from 877 to 351 milliseconds. GitHub code search did not return this repository, and its README names neither Jev nor TypeSafe, so the final labels mark `lancedb/lancedb` (11,541 stars) as not related to Jev. It is not in the counts of sections 5 and 6 or among the projects of section 7. |

### More open models and replicas

| Item | Date | Summary |
| --- | --- | --- |
| [decider open-weights decision model family by Mapika](https://github.com/Mapika/decider) | 2026-09-16 | Apache-2.0 open reproduction of TypeSafe AI's System One model class, not affiliated with TypeSafe AI, built on Qwen3.5 base models. On JevBench `v1.4.2.2`, decider-4b v2 ranks 3rd at 64.1, above Jev 1.13.0 at 63.3. |
| [OpenJev open-weights decision model with Jev-compatible shim](https://huggingface.co/openjev/openjev) | 2026-09-20 | Fine-tune of `Qwen/Qwen3.8-27B` with 27,356,728,560 parameters, weights under Creative Commons Attribution-NonCommercial 4.0. Its own model card reports that on 10,000 unpublished questions, hosted Jev scored 85.4%, OpenJev 84.0%, and the untuned base 80.4%. The project used 3,078 of those questions during development, and the card names no Jev version and no run date. It ships a `/v1/systemone` shim. |
| [jevlike: small one-pass option-scoring starter model](https://github.com/vinnylarouge/jevlike) | 2026-09-16 | Independent starter model with Jev's input and output shape, 1,318 stars on 2026-09-27. The default byte scorer has 41,280 trainable parameters. The project says it did not show equal quality with Jev. None of 4 pull requests is merged. |
| [AnyJev: turn open language models into Jev-style decision models](https://github.com/nokia-applied-research/AnyJev) | 2026-09-21 | Python library that reads a decision from one prefill of an open model's next-token distribution, without training its weights. Its Nokia link is self-declared, and the GitHub organization `nokia-applied-research` is not verified. On Qwen3-8B BANKING77 (300 test items), accuracy rose from 0.747 raw to 0.803 with no labels and to 0.807 with 100 to 500 labels. The labels mainly cut expected calibration error, from 0.184 to 0.095. |
| [Ollaya: local server for open decision models](https://github.com/ollaya-dev/ollaya) | 2026-09-23 | Rust server claiming wire-identical TypeSafe endpoints, 13 releases from 2026-09-23 to 2026-09-27. Recommended `winnow:e4b` scores 0.722 on typed decisions in Ollaya's own run, against 0.738 for Jev from the Winnow author's run of Jev 1.13 through OpenRouter. The typed-decisions dataset author measured Jev at 0.727 on the same 2,000 decisions, the figure that section 8 uses. It is not affiliated with TypeSafe. |
| [Simple Jev: Featherless server turning open models into classifiers](https://github.com/featherless-ai/simple-jev) | 2026-09-18 | Reads next-token logits for allowed answer labels, served at `/v1/classifier` with a `/v1/systemone` alias. On Decision Index 0.2.1, the Qwen3.8-27B entry ranks 4th of 68 at 55.74 against Jev's 57.91. Expected calibration error is 0.113 versus 0.074. |
| [Together AI Tev1-4B-experimental, a Jev-inspired open-weight model](https://github.com/togethercomputer/tev1) | 2026-09-23 | Fine-tuned on Qwen3.5-4B with 37,840 training examples and no data from Jev. It keeps Qwen's standard next-token head. On 2,000 phishing items it scored 50.6% against Jev's 62.9%; ticket routing was 26 versus 27 of 27. |

### More benchmarks and evaluations

| Item | Date | Summary |
| --- | --- | --- |
| [Jev Decision Index community leaderboard, edition 0.2.1](https://huggingface.co/spaces/multimodalart/jev-decision-index) | 2026-09-27 | Unofficial community Hugging Face Space, edition 0.2.1 dated 2026-09-27, measured against `jev-1.13.0`. Jev scores 57.91 chance-corrected; top entrant Surogate Rune 26B-A4B v3 scores 57.44. No entrant scores above Jev. Hosted Jev median latency is 524.1 milliseconds. |
| [JevBench benchmark by Benchmark Heaven, release `v1.4.2.2`](https://github.com/fstandhartinger/jevbench) | 2026-09-27 | One-person hobby benchmark, not affiliated with TypeSafe AI. Release `v1.4.2.2`, dated 2026-09-27, lists 95 systems and ranks 91 of them. Jev 1.13.0 is 4th at 63.29, behind Imajev-4B at 67.37. Jev shows 86.6% public versus 36.7% sealed accuracy. |
| [Jev calibration study by scienthoon](https://github.com/scienthoon/jev-ood-calibration) | 2026-09-19 | Study made 4,621 calls through Vercel AI Gateway. On 900 synthetic items Jev had 75.1% accuracy and expected calibration error 0.107, 4.4 times the 0.024 noise floor. A 2026-09-22 correction cut the refit Choice temperature from 3.29 to 1.30. |
| [Decision Hijacking: prompt injection attacks on Jev decisions](https://arxiv.org/abs/2609.28613) | 2026-09-23 | Unreviewed Nanyang Technological University preprint, 2026-09-23, testing `jev-1.13.0` on 510 InjecAgent cases. The original attack picked the attacker's tool in 1.8% of cases; optimized attacks reached 3.5%. Jev returned no undeclared action, but injected text shifted probabilities. |

### Risks, policy, and operations

| Item | Date | Summary |
| --- | --- | --- |
| [Futureppo bulk TypeSafe account registration script](https://github.com/Futureppo/typesafe_register) | 2026-09-21 | Python script, created 2026-09-21, that bulk-registers TypeSafe console accounts and creates keys. 126 stars and 47 forks on 2026-09-27. TypeSafe paused new Jev sign-ups about 17 hours after the repository was created. |
| [jev-accounts-hub gateway pooling many TypeSafe accounts](https://github.com/antTing/jev-accounts-hub) | 2026-09-21 | Go repository, created 2026-09-21, 3 stars, that pools many TypeSafe accounts behind one gateway. The TypeSafe customer agreement bans credential sharing and extra promotional-credit accounts. |
| [TypeSafe pauses new Jev signups](https://jevainews.com/news/typesafe-signups-paused/) | 2026-09-22 | Unofficial Jev News digest. TypeSafe paused new Jev signups on 2026-09-22, two days after removing the waitlist, with existing accounts still working. On 2026-09-23 Diogo Almeida said people abused signups to get around rate limits. No official reopening announcement appears. |
| [TypeSafe AI Master Customer Agreement, September 23 update](https://typesafe.ai/legal/mca) | 2026-09-23 | Updated 2026-09-23. It bans distillation, competing products, credential sharing and extra promotional-credit accounts. Liability cap: greater of 12 months of fees or $50. Compared with the copy archived on 2026-09-22, the only substantive change is clause 2.3(l), which cites the Acceptable Use Policy. The clause is restored, not new: a copy archived on 2026-09-20, also dated September 19, had it with an older link, and the copies archived on 2026-09-21 and 2026-09-22 did not. |
| [Wunderlandmedia legal review of TypeSafe Jev contracts](https://wunderlandmedia.com/typesafe-ai-jev-terms-of-service-gdpr) | 2026-09-18 | Kemal Esensoy's 2026-09-18 review of four TypeSafe legal documents flagged a benchmark publication ban, unrestricted telemetry processing and a $50 minimum liability cap. The September 23 agreement lacks the benchmark ban but keeps the telemetry and liability terms. |

### Other

| Item | Date | Summary |
| --- | --- | --- |
| [TypeSafe AI status page](https://status.typesafe.ai) | 2026-09-27 | Better Stack page, 2 monitors. Over 90 days, the application programming interface had 99.827% uptime, with downtime on 25 days totaling about 3.7 hours. All 4 incident reports date from 2026-09-21 to 2026-09-24; July and August list none despite downtime. |
| [the-jev-enator circuit breaker proposal for Jev outages](https://github.com/jakenbear/the-jev-enator/issues/38) | 2026-09-25 | Open 2026-09-25 issue for Jev-backed Claude Code hooks: each gated tool call can wait 12 seconds before failing open, about 30 times the 425 millisecond 95th percentile. Pull request #47 made 12 seconds a hard deadline, without a breaker. |
| [awesome-jev-by-typesafe, a repurposed community list](https://github.com/Anil-matcha/awesome-jev-by-typesafe) | 2026-09-17 | Anil Matcha's repository, formerly Chat-Youtube and awesome-claude-fable-5, switched to Jev content on 2026-09-17 and kept its stars: 226 in June, 846 by 2026-09-25. Its README calls it unofficial and omits this history. |

## 13. Limitations of this survey

- Coverage depends on GitHub search, GitHub code search, and 14 community lists. Code search indexes default branches only. Private repositories, repositories outside GitHub, and projects that never name Jev or TypeSafe are not in the data.
- 415 repositories without a README and without a description, and 999 repositories created before August 2026 that matched only by the word "jev" in their name or description, were excluded without classification.
- The reviewers are language-model agents, not people. They read the same metadata and README as Jev, so a misleading README misleads all three. The consensus is a better estimate than any single label, but it is not ground truth.
- The five top-level categories of the use-case map overlap (a tool-call gate is both Harness Engineering and Universal Verification), so the category label has the lowest agreement of all fields, also between the two reviewers.
- Jev judged relatedness conservatively: it marked many projects as unrelated that mention Jev briefly, imitate its interface, or reach it through OpenRouter. Every repository that Jev judged unrelated was therefore reviewed, and the final counts include the corrections.
- The labels describe what each project says it does. Only the integrations in section 7 and the 6 integration pull requests in section 12 were checked against their code. Only the 10 replicas in section 8 were checked against every independent measurement that could be found. Two skeptics checked each item in section 12, including its 7 replicas, against the sources that the item cites.
- Each state held the cleaned README up to 60,000 characters, not only the passages that each question needs. The TypeSafe documentation warns that accuracy falls when the state holds a lot of irrelevant detail, so this trades some accuracy for coverage. Longer READMEs were cut, and states over the token limit were cut further, so Jev did not read the text past the cut.
- Decision-shape labels come from Jev alone.
- Jev has been public for about two weeks (since 2026-09-15), and 12,530 of the 14,742 related repositories (85.0%) were created in that time. Stars, versions, and repository counts change daily.

## 14. Data

[`data/jev-ecosystem-classification.csv`](data/jev-ecosystem-classification.csv) holds one row for each classified repository: its name and GitHub URL, stars, creation date, whether it is related to Jev, its final kind, category and use case, the Jev labels and confidences, and the source of the final labels (`label_source`). The value "source code verification" marks the 104 large integrations of section 7: whether each one is related to Jev and its category come from reading its source code. For the 101 of them with integration code, the kind and use case come from the majority of Jev and two blind reviewers, except the one use-case correction named in section 7. The other 3 are not related to Jev, so they have the kind `unrelated` and the use case `general_or_other`. The value "Jev and two blind reviewers" marks every other row: all its final labels come from that majority, with a third blind reviewer where all three disagreed. The one exception is the kind of 6 related repositories: their final kind was `unrelated`, which contradicts their relatedness, so they have the Jev kind instead, or `application` where Jev also chose `unrelated`.

[`data/jev-integration-evidence.csv`](data/jev-integration-evidence.csv) holds one row for each source file that was read to verify the integrations of section 7: repository, whether an integration is present, integration type, file path, a URL for that evidence, the date the main integration file was first added, and a note for that date. The URL points to the file on the default branch, or to the commit, pull request, release, or package page that the evidence names; 2 files that are gone from the default branch point to the commit that added them. For 90 of the 104 repositories the note names the commit or release that added the file. For the other 14 it repeats the date only, because the source data recorded no commit or release for them.

## 15. Sources

- TypeSafe documentation: https://docs.typesafe.ai/concepts/use-case-map, https://docs.typesafe.ai/api, https://docs.typesafe.ai/models, https://docs.typesafe.ai/model-jaggedness/jev-1.13, https://docs.typesafe.ai/llms.txt
- Official organization: https://github.com/typesafe-ai
- Unofficial community organization: https://github.com/TypeSafeAI
- Community lists: the 14 repositories in section 10
- JevBench: https://github.com/fstandhartinger/jevbench and https://benchmarkheaven.com/jev-models
- jabr/classifier-benchmark: https://github.com/jabr/classifier-benchmark and https://github.com/wfzyx/von/issues/3
- Jev Decision Index: https://huggingface.co/spaces/multimodalart/jev-decision-index
- DecisionBench: https://github.com/Hanno-Labs/decision-bench-results/pull/26
- umstek/zero-shot-ie-bench: https://github.com/umstek/zero-shot-ie-bench/pull/4
- mandu5/jevcompat: https://github.com/mandu5/jevcompat/blob/main/results/jeff/report.md
- PriorBench: https://github.com/priorbench/jev
- Rajesh Beri: https://www.beri.net/article/typesafe-jev-typed-decision-model-calibration-decomposition-shadow-eval
- Andrew Yourtchenko: https://ayourtch-llm.github.io/apchat-blog/posts/2026-09-20-jev-clones-measured/
- Hacker News: https://news.ycombinator.com/item?id=49717558
- SemIf results: https://github.com/TheoLeeCJ/SemIf-OpenJev/blob/master/docs/RESULTS.md
- LangChain integration: https://docs.langchain.com/oss/python/integrations/providers/typesafe
- Vercel guide: https://vercel.com/kb/guide/typesafe-jev-and-ai-sdk
- Each integration in section 7 links to its repository; [`data/jev-integration-evidence.csv`](data/jev-integration-evidence.csv) lists the files that were read.
