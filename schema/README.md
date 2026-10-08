---
title: "规范与模板"
type: index
tags:
  - schema
status: in-progress
date_added: 2026-09-10
date_update: 2026-09-10
---

# 规范与模板

本目录是知识库的 **Schema 层**：规定页面怎么组织、怎么命名、怎么写。它只描述**结构**，不承载项目事实，也不放操作规程——项目事实归各任务线的 `plan.md` / `log.md` / `artifacts.md`，技术知识归 `dut-postdoc`。

常驻协作纪律不在本目录，在根 [../AGENTS.md](../AGENTS.md)：那份文件由各工具的入口自动加载。各目录「写什么、不写什么」由该目录自己的 `README.md` 维护。

## 文件清单

| 文件 | 内容 |
| --- | --- |
| [llm-wiki-methodology.md](llm-wiki-methodology.md) | 三层分工与 Ingest / Query / Lint 在本仓的定义，以及 Lint 检查项清单 |
| [page-schemas.md](page-schemas.md) | 页面归属、命名、frontmatter 属性与状态取值、索引规则、存储与来源要求 |
| [templates/directory-index.md](templates/directory-index.md) | 目录 `README.md` 骨架 |
| [templates/task-line.md](templates/task-line.md) | 新开任务线的目录骨架与三件套起始文件 |
| [templates/meeting-note.md](templates/meeting-note.md) | 会议纪要骨架 |
| [templates/daily-report.md](templates/daily-report.md) | 双周周期日报文档骨架 |

`templates/` 不单独设索引，文件由本表直接收录。

## 内容纪律

- **规范只写一遍。** 各目录「写什么、不写什么」由该目录自己的 `README.md` 维护，本目录只给指针，不复述——重复即制造第二套事实源。
- 模板里的链接是**相对于复制后的位置**写的，在 `templates/` 原地不解析。Lint 断链检查跳过本目录，改由使用时核对。
- 模板中的占位符用 `{{...}}` 标注，复制后必须全部替换；写作提示注释在成稿时删除。
- 规范变更后，受影响的既有页面要么当次改齐，要么在根 [../log.md](../log.md) 里写明未改齐的范围，不留隐式待办。

## 相关链接

- 全库总导航：[../index.md](../index.md)
- 仓库定位与内容去向：[../README.md](../README.md)
- 知识库维护日志：[../log.md](../log.md)
