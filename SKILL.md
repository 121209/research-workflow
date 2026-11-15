---
name: research-workflow
description: Use when doing academic research in Codex—literature search, literature review, questionnaire or experimental data analysis, or a thesis defense PPT. Sets up one task folder with AGENTS.md and requires real search, real papers, and real code instead of invented citations or charts.
---

# Codex 科研任务工作流

方法论整理自兔老师（[@DrRabbitAI](https://x.com/DrRabbitAI)）的文章[《如何用Codex辅助我们做科研：从文献搜索到答辩PPT》](https://x.com/DrRabbitAI/status/2121590571689287967)。把 Codex 当成会执行的任务型 Agent，而不是聊天机器人。

## 硬性规则

- 一个具体任务建一个独立文件夹，并在其中放 `AGENTS.md` 作为任务规则。
- 进入任何任务文件夹后，第一条指令必须是先读上下文，不要直接生成最终结果。见 [assets/prompts.md](assets/prompts.md) 的「通用起步」。
- 不得凭模型记忆编造论文、DOI、PMID、统计数字或图表。文献必须来自真实检索或用户提供的文件；统计结果和统计图必须由 Python 或 R 对真实数据跑出来，并保留代码。
- 不确定的信息标记为「待核查」，不要写成确定结论。
- 统计方法是否合适、结果如何解释，由研究者判断。Codex 负责写代码、跑分析、整理材料和打磨图表。
- 不要因为某个 Skill 很火就直接拿来写论文。先判断它的默认流程是否匹配当前学科和任务。

## 安装其他 Skill 之后

先不要让它干活。用 [assets/prompts.md](assets/prompts.md) 里的「先读 Skill」提示，让 Codex 用中文总结：它解决什么问题、需要哪些输入、会调用哪些工具、输出什么、明确不适合做什么，以及是否适配当前任务。不适配就改规则或换 Skill，不要硬用。

可选工具（按任务选用，不是必须）：

- 社科 / 教育 / 管理：能真实检索的 Google Scholar 类 Skill
- 医学 / 生物医学 / 公共卫生：[pubmed-paper-finder](https://github.com/yilaai/pubmed-paper-finder)（走 NCBI，不编文献）
- 已有论文列表需要阅读优先级：paper-quality-filter（按来源证据排序，不替你判断学术价值）
- 数据分析：Data Analyzer 等，仍须先读 SKILL.md 再判断是否适用
- 答辩 PPT：`codex-ppt-skill`（图片式 PPT，视觉统一但不易逐元素编辑），或可编辑 PPT 类 Skill（例如 [stanford-lecture-ppt-skill](https://github.com/121209/stanford-lecture-ppt-skill)）

安装方式是直接告诉 Codex：「请帮我从 GitHub 安装这个 Skill：`<仓库地址>`。安装后先阅读 SKILL.md，告诉我它的流程、输入、输出和限制。」

## 开始时先问清任务

如果用户还没说做哪一类，先确认是下面哪一种，再按对应流程建文件夹：

1. 文献搜索
2. 文献综述
3. 数据分析
4. 答辩 PPT

主题、学科、已有材料（论文 PDF、数据、变量说明、终稿）由用户提供。缺少关键材料时先列出缺口，不要假装已经齐了。

## 工作流 1：文献搜索

原则：先设计检索路径，再真实检索，最后整理可核查结果。不要直接问「请推荐 20 篇关于某某的论文」。

1. 建立文件夹（模板见 [assets/literature-search.AGENTS.md](assets/literature-search.AGENTS.md)）：

```
literature-search/
├── AGENTS.md
├── topic.md
├── seed-papers.md
├── search-results/
└── notes/
```

2. 把研究主题写入 `topic.md`。把用户已有的种子文献写入 `seed-papers.md`。
3. 执行「通用起步」提示。
4. 生成概念矩阵：核心概念、英文关键词、同义词、可能相关理论、Google Scholar 英文检索式、中文数据库检索式。这一步只生成检索语言，不要推荐具体论文。
5. 基于概念矩阵生成约 10 组检索式，每组聚焦一个角度，并说明想找到哪类论文。输出到 `search-results/search-queries.md`。
6. 用真实检索工具执行这些检索式（按学科选择 Scholar 或 PubMed 等）。每组取前若干条，合并去重，保留标题、作者、年份、来源、引用数、DOI 或可访问链接，并初步判断相关性。输出 `search-results/literature_search_results.md` 和 `search-results/literature_matrix.csv`。
7. 不确定的元数据标「待核查」。需要阅读优先级时，再对已有列表使用来源过滤类 Skill。

完整提示见 [assets/prompts.md](assets/prompts.md) 的「文献搜索」。

## 工作流 2：文献综述

原则：AI 适合做阅读理解和主题归纳，但必须基于真实论文。顺序不能反：先读真实论文 → 主题矩阵 → 大纲 → 正文。不要直接问「请帮我写一篇关于某某的文献综述」。

1. 建立文件夹（模板见 [assets/literature-review.AGENTS.md](assets/literature-review.AGENTS.md)）：

```
literature-review/
├── AGENTS.md
├── papers/
├── literature_matrix.csv
├── notes/
└── draft/
```

2. 把已检索或用户提供的真实论文放入 `papers/`，读书笔记放入 `notes/`。没有真实论文就先回到工作流 1，不要开写。
3. 执行「通用起步」提示。
4. 先输出 `literature_theme_matrix.md`：主题板块、代表文献、研究对象、方法、核心发现、研究不足、与本研究问题的关系。只能使用已提供的真实论文。
5. 再写 `review_outline.md`。按研究主题和问题逻辑组织，不要按「张三说、李四说」排列。每一节说明：回答什么问题、使用哪些文献、要引出什么研究不足。
6. 最后写 `draft/literature_review_v1.md`。每段有明确中心句；每个重要判断标注对应文献；没有来源的泛泛评价不要写；不确定的判断写入 `unchecked_claims.md`。

面向 ML/CV/NLP 的论文写作 Skill 可以选用，但教育学、新闻传播、管理学、社会学等不要硬套。完整提示见 [assets/prompts.md](assets/prompts.md) 的「文献综述」。

## 工作流 3：数据分析

原则：让 Codex 写代码、跑代码、根据报错修改，再保存结果。不要让大模型凭空画统计图。

1. 建立文件夹（模板见 [assets/data-analysis.AGENTS.md](assets/data-analysis.AGENTS.md)）：

```
data-analysis/
├── AGENTS.md
├── data.csv
├── variable-dictionary.md
├── scripts/
├── figures/
└── reports/
```

2. 放入数据文件和变量说明。缺变量说明时先请用户补，或根据数据草案列一版待确认的字典，不要直接推断。
3. 执行「通用起步」提示。
4. 先做数据质量检查，不要一上来做统计推断。输出 `reports/data_quality_report.md`：样本量、各变量类型、缺失比例、明显异常值、量表取值是否符合变量说明。
5. 再写 `reports/analysis_plan.md`：先描述性统计，再相关分析，变量条件允许时再回归。每一种分析说明使用原因、变量选择和结果解释边界。不适合的方法要说明原因，不要硬做。把计划交给用户确认后再跑。
6. 按计划写并运行分析代码，保存到 `scripts/analysis.py`（或 R 脚本）。结果和图表必须来自数据文件。同时输出描述统计表、相关矩阵、回归结果（如有）和 `figures/`。不要用图片生成模型直接画统计图。
7. 如需美化图表，只能基于同一份代码和同一份数据改配色、字体、标签、图例和导出格式，不得改动任何统计结果。

完整提示见 [assets/prompts.md](assets/prompts.md) 的「数据分析」。

## 工作流 4：答辩 PPT

原则：论文到 PPT 不是换格式，而是重新组织答辩逻辑。先拆逻辑，再出大纲并等用户确认，最后才生成 PPT。

1. 建立文件夹（模板见 [assets/defense-ppt.AGENTS.md](assets/defense-ppt.AGENTS.md)）：

```
defense-ppt/
├── AGENTS.md
├── final-paper.md
├── figures/
├── tables/
└── ppt-output/
```

2. 放入论文终稿和真实图表。没有终稿就不要生成 PPT。
3. 执行「通用起步」提示。
4. 先输出 `defense_logic.md`：研究问题、为何值得研究、综述引出的不足、方法设计、主要发现、创新点、答辩时最可能被问到的问题。先不要生成 PPT。
5. 再输出 `outline.md`（约 12 页，可按实际调整）。每页包括页标题、核心信息、建议图表、讲述重点。结构建议：研究背景、研究问题、文献基础、研究方法、主要发现、创新点、不足与展望。每页只讲一个核心信息。等用户确认大纲后再往下走。
6. 使用已安装且已读过的 PPT Skill 生成幻灯片、`speech.md` 逐页讲稿，并把讲稿写入备注。必须使用论文中的关键图表，不得编造新数据、新结果或新结论。若大纲还不适合答辩，先提出修改建议，不要直接出片。

图片式 PPT 视觉统一、出稿快，但往往不便逐元素编辑；若用户需要后期精修，改用可编辑 PPT Skill，或提示其在 PowerPoint 里调整关键页。完整提示见 [assets/prompts.md](assets/prompts.md) 的「答辩 PPT」。

## 执行时怎么做

1. 确认任务类型和已有材料。
2. 若文件夹还不存在，先按对应结构创建，并复制对应的 `AGENTS.md` 模板，把其中的主题和路径改成当前任务。
3. 运行「通用起步」，根据缺口向用户要材料。
4. 严格按该工作流的步骤顺序执行，一步的输出是下一步的输入。
5. 每一步把结果写进文件夹，不要只在对话里给出长文本。
