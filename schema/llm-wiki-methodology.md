---
title: "LLM Wiki 方法论与本仓架构模式"
type: methodology
tags:
  - methodology
  - llm-wiki
  - schema
status: in-progress
date_added: 2026-09-10
date_update: 2026-09-29
---

# LLM Wiki 方法论与本仓架构模式

本仓库按 [Andrej Karpathy 提出的「LLM Wiki」模式](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f) 运转：在**大工项目的原始沟通与内部系统**和**我**之间，维护一个持久、结构化、可被 LLM 读写的中间层，使「现在什么状态、当时怎么定的、材料齐不齐」这类问题不必每次重新翻聊天记录和云文档。

本页只解释设计意图。页面格式见 [page-schemas.md](page-schemas.md)，常驻协作纪律见 [../AGENTS.md](../AGENTS.md)。

## 1. 三层分工

### 原始源层

最终事实依据。AI 只读，永不改写，**也不整段搬进本仓**：

| 来源 | 承载内容 |
| --- | --- |
| 研究院 GitLab | 源码、程序包、内部手册与文档原件 |
| 飞书云文档 | 算海团队交付材料与附件原件 |
| 飞书群 / 微信消息流 | 实时沟通 |
| `suanhaitech/houzai` | 算海内部执行过程、任务 issues 与过程产出 |
| `heliangos/wechat` | 微信沟通原文与联系人上下文 |
| `structural-dynamics-software` | 结构动力学项目投标阶段源文档与代码 |
| `dut-postdoc` | 从工作中沉淀的可复用技术知识 |

### Wiki 层

本仓真正维护的内容，按证据强度分三档：

| 档次 | 页面 | 性质 |
| --- | --- | --- |
| 事实源 | 任务线的 `plan.md` / `log.md` / `artifacts.md` | 本仓对该任务线的权威结论；其他仓库只放指针，不复制正文 |
| 留证 | `meetings/`、`chats/` | 忠实转写的一手记录，回答「当时到底说了什么」，本身不充当事实源 |
| 派生 | `reports/` | 由上两档产出的对外汇报，以及按周期组织的收到材料登记（`materials/`，记来源、摘要与原件指针）与本人任务材料（`my-tasks/`）。这两类记录的是周期内的输入与产出，只是按汇报周期组织，故并入本档 |

本仓**不建概念页与文献层**。可复用的技术知识归 `dut-postdoc`；本仓只写项目事实。这条边界是硬的——判断某类内容该放哪个仓库时以 [../README.md](../README.md) 的内容去向表为准。

### Schema 层

规定页面如何组织与维护，采用「单一事实源 + 多工具导入桩」架构：

- [../AGENTS.md](../AGENTS.md) 是**工具无关常驻规则的唯一来源**，Codex、Antigravity 等直接读取。
- [../CLAUDE.md](../CLAUDE.md) 只引用 `AGENTS.md`，不含独立规则；内部系统各条通道的可用性按内容归属维护，不留在工具入口。
- [page-schemas.md](page-schemas.md) 集中页面归属、属性、索引与存储要求，`templates/` 提供骨架，本页解释理由。

导入桩不复制规则正文，杜绝规则漂移。

## 2. 三类活动

| 活动 | 在本仓的含义 | 常见产物 |
| --- | --- | --- |
| **Ingest** | 把一次会议、一段群聊、一份团队交付材料或一天工作，转成仓库页面，并把其中的结论回写到任务线事实源 | `meetings/` 纪要、`chats/` 原文、`reports/materials/` 与 `reports/my-tasks/` 归档、`reports/` 日报，以及 `plan.md` / `log.md` / `artifacts.md` 的更新 |
| **Query** | 基于任务线事实源回答项目状态、历史口径与材料缺口，区分事实、推导与判断 | 带来源指针的回答；不自动回填页面，写入须有授权 |
| **Lint** | 体检知识与组织结构 | 矛盾、过期、孤页、缺链与越界清单，以及授权后的修复 |

不是每份材料都要全文归档。Ingest 的判据是「这条信息三个月后还需要被查到吗」——需要就留证，不需要就只把结论写进 `log.md`。

### Lint 检查项

- `plan.md` 的安排与 `log.md` 的实际进度是否矛盾；`log.md` 中已被推翻的结论是否有后续更正条目。
- `artifacts.md` 的归档状态是否过期，受控链接是否失效。
- `reports/materials/` 与 `reports/my-tasks/` 下的材料是否都登记在 [../reports/README.md](../reports/README.md) 的材料清单表与本人任务清单表、所在周期子目录是否与 frontmatter 的 `cycle` 一致；`my-tasks/` 页面的状态是否与任务线 `log.md` / `artifacts.md` 的交付记录对得上；`README.md` 目录树与实际目录是否一致。
- 跨仓库指针是否仍然有效（相邻仓库的路径重组会静默打断）。
- 是否有凭据、内部路径、未授权再分发的源码或附件混入——本仓是 Public 仓库，这条优先级最高。
- 目录 `README.md` 的文件清单是否覆盖该目录实际文件。
- 相对链接是否全部解析得到。两类例外不算问题：`schema/templates/` 里的链接相对于复制后的位置写，原地不解析；根 [../log.md](../log.md) 与各任务线 `log.md` 的历史条目会指向后来被删除或改名的页面，append-only 不回改，去向由后续条目说明。
- 跨仓库指针（`<仓库名>:<路径>` 与指向相邻仓库的 URL）是否仍然有效。这类链接本地跑不出断链，只能人工核对——相邻仓库的目录重组会静默打断它们。
- 规范写下的写法与最近几周的实际写法是否已经分叉——分叉了就明确改一边，不要让两套写法并存。

## 3. 导航与维护

全库入口由根 [../index.md](../index.md) 提供，一级内容目录的 `README.md` 组织各自页面，页面间用相对 Markdown 链接互链（本仓为 Public 仓库，链接需在 GitHub 上可点击，不使用 Obsidian 双链）。索引服务于查找，不复制每页的进度；根 [../log.md](../log.md) 记录知识库自身的演化，任务线进度仍归各自 `log.md`。

人决定任务、口径与最终判断；AI 协助整理、核对和维护。知识的积累依赖可检查的来源与持续修订，不靠文件数量或自动生成篇幅衡量。

## 4. 规范入口与参考

- [Andrej Karpathy: LLM Wiki Gist](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f)：方法论原型出处。
- [../AGENTS.md](../AGENTS.md)：工具无关的常驻协作规则（含 `CLAUDE.md` 导入桩）。
- [page-schemas.md](page-schemas.md)：页面归属、属性、索引、存储与生命周期。
- [../README.md](../README.md)：仓库定位与内容去向表。
- `dut-postdoc`：同模式的个人研究知识库，本仓的技术知识去向。
