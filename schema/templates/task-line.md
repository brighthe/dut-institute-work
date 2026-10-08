---
title: "任务线 · {{任务线名称}}"
type: index
tags: []
status: "in-progress"        # in-progress | done（描述索引自身）
date_added: {{YYYY-MM-DD}}
date_update: {{YYYY-MM-DD}}
---

# 任务线 · {{任务线名称}}

<!-- 新开任务线时复制本文件到 <任务线目录>/README.md，替换占位符并删除写作提示。
     同时按下方「起始文件」建立三件套。要求见 ../page-schemas.md §5、§6.1。
     建完后同步登记到 ../../README.md 的内容去向表与 ../../index.md。 -->

{{一段话说明：这条任务线是什么、甲方或委派方是谁、由谁协调、边界在哪。
有合同或项目编号的写明编号与判定口径。}}

本任务线与 {{其他任务线}} 相互独立：{{说明为何独立——合同、对接人与交付物互不相干}}。

## 事实源分工

<!-- 这条任务线的内容分别以什么为准。本仓之外的事实源写清仓库或系统名称。 -->

| 内容 | 事实源 |
| --- | --- |
| 工作安排与任务拆解 | 本目录 [plan.md](plan.md)（**单一事实来源**，更新安排只改这里） |
| 进度 | 本目录 [log.md](log.md)（append-only） |
| 交付物与材料状态 | 本目录 [artifacts.md](artifacts.md) |
| 会议纪要 | [../meetings/](../meetings/README.md)，按日期命名 |
| {{原件 / 源码 / 内部文档}} | {{研究院 GitLab / 飞书云文档 / 相邻仓库}}，本仓只登记清单与归档状态 |

## 文件清单

| 文件 | 作用 |
| --- | --- |
| [plan.md](plan.md) | 工作安排与任务拆解（单一事实来源） |
| [log.md](log.md) | 进度日志（append-only，新条目加在最上面） |
| [artifacts.md](artifacts.md) | 交付物清单、版本、用途与归档状态 |

<!-- 手册、环境、流程、清单等配套页面按需追加到上表；它们不承担事实源角色。 -->

## 内容纪律

<!-- 本任务线特有的约束。没有则整节删除，不抄 AGENTS.md 的通用条款。 -->

-

## 相关链接

-

---

## 起始文件骨架

<!-- 以下为建线时需要一并创建的三个文件的最小内容，创建后从本 README 删除本节。 -->

**`plan.md`**

```markdown
---
title: "{{任务线名称}} · 工作安排"
type: plan
task_line: {{目录名}}
status: active
date_added: {{YYYY-MM-DD}}
date_update: {{YYYY-MM-DD}}
---

# 工作安排 · {{任务线名称}}

> 本文件是本任务线安排的**单一事实来源**：更新安排只改这里，其他仓库只放指针。
```

**`log.md`**

```markdown
---
title: "{{任务线名称}} · 进度日志"
type: log
task_line: {{目录名}}
status: living
date_added: {{YYYY-MM-DD}}
date_update: {{YYYY-MM-DD}}
---

# 进度日志 · {{任务线名称}}

> Append-only：新条目加在最上面，格式 `## [YYYY-MM-DD] <简述>`；只增不改历史条目。
> 结论被推翻时新写一条更正，指明推翻了哪条、原结论在当时是否成立。
```

**`artifacts.md`**

```markdown
---
title: "{{任务线名称}} · 交付物清单"
type: artifacts
task_line: {{目录名}}
status: living
date_added: {{YYYY-MM-DD}}
date_update: {{YYYY-MM-DD}}
---

# 交付物清单 · {{任务线名称}}

> 记录文件名、版本、用途、归档状态与事实源。经用户明确授权可登记受控链接及登记时间，
> **不保存原件、正文或附件**。

| 交付物 | 版本 | 用途 | 归档状态 | 事实源 |
| --- | --- | --- | --- | --- |
```
