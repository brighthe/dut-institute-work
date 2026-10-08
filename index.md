---
title: "全库总导航"
type: index
tags:
  - wiki
  - navigation
status: in-progress
date_added: 2026-09-10
date_update: 2026-09-29
---

# 全库总导航

大工项目个人公开总档案，按 [LLM Wiki](schema/llm-wiki-methodology.md) 模式维护。先从领域入口进入，再按任务线查找具体页面。

仓库定位与内容去向见 [README.md](README.md)，协作规则见 [AGENTS.md](AGENTS.md)，知识库演化记录见 [log.md](log.md)。

## 任务线

每条任务线自带 `plan.md` / `log.md` / `artifacts.md` 三件套，安排以 `plan.md` 为单一事实来源。

| 任务线 | 入口与内容 |
| --- | --- |
| HPC 求解器性能与多后端并行 | [hpc/README.md](hpc/README.md)：研究院分派的主任务线，SGFEM 性能调优与异构并行 |
| 结构动力学软件模块验收 | [structural-dynamics/README.md](structural-dynamics/README.md)：DUTAWZ-2026097 项目 C 包验收阶段 |

## 内容领域

| 领域 | 入口与内容 |
| --- | --- |
| 会议纪要 | [meetings/README.md](meetings/README.md)：会议与讨论的会上事实、结论口径与行动项 |
| 项目报告 | [reports/README.md](reports/README.md)：日报、双周汇报周期文档，以及周期内收到的工作输入材料（摘要与原件指针）与本人任务材料 |
| 群聊原文 | [chats/README.md](chats/README.md)：飞书群聊逐条原文归档，回答「当时到底说了什么」 |
| 规范与模板 | [schema/README.md](schema/README.md)：页面归属规范、方法论与模板 |

## 稳定入口

- [README.md](README.md#内容去向)：内容去向表——判断某类内容归哪个仓库时以它为准。
- [hpc/plan.md](hpc/plan.md)：HPC 任务线的工作安排与任务拆解。
- [structural-dynamics/plan.md](structural-dynamics/plan.md)：验收准备安排、合同硬约束与行动项。
- [schema/llm-wiki-methodology.md](schema/llm-wiki-methodology.md)：三层分工与 Ingest / Query / Lint 的本仓定义。

具体页面由所属任务线或领域索引收录，状态以对应页面为准。本页仅在稳定入口或全库高层导航变化时更新。
