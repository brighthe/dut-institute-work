---
title: "页面归属与结构规范"
type: schema
tags:
  - schema
  - page-structure
status: in-progress
date_added: 2026-09-10
date_update: 2026-09-29
---

# 页面归属与结构规范

定义本仓页面的归属、命名、属性、索引与存储要求。常驻协作纪律见 [../AGENTS.md](../AGENTS.md)，设计意图见 [llm-wiki-methodology.md](llm-wiki-methodology.md)，写法见 `templates/`。

**本页只管结构。** 各目录「写什么、不写什么」的内容纪律由该目录自己的 `README.md` 维护，本页只给指针，不复述——重复即制造第二套事实源。

## 1. 页面归属与模板

| 位置 | 职责 | 模板 |
| --- | --- | --- |
| 根 [../index.md](../index.md) | 全库总导航 | 不套模板，格式见 §4 |
| 根 [../log.md](../log.md) | 知识库维护时间线 | 格式见 §6.6 |
| `schema/` | 页面规范、方法论与模板；不放操作规程与协作纪律 | 不套 Wiki 内容模板 |
| `<任务线>/` | 一条任务线的完整事实源与配套手册 | [templates/task-line.md](templates/task-line.md) |
| `meetings/` | 会议与讨论纪要 | [templates/meeting-note.md](templates/meeting-note.md) |
| `reports/` | 日报及双周、阶段汇报 | [templates/daily-report.md](templates/daily-report.md) |
| `reports/materials/` | 双周汇报周期内收到的工作输入材料：来源、摘要与原件指针 | 不套模板，结构见 §6.7 |
| `reports/my-tasks/` | 双周汇报周期内本人承担任务的相关材料 | 不套模板，结构见 §6.5 |
| `chats/` | 飞书群聊逐条原文归档 | 不套模板，结构见 §6.4 |

研究院分派新任务线时，在根目录按 `hpc/` 的同构方式新开一个文件夹，并同步登记到 [../README.md](../README.md) 的内容去向表与 [../index.md](../index.md)。

工具配置与缓存目录（`.claude/`、`.obsidian/` 等）不属于 Wiki 内容，不建索引。

## 2. 命名

- 文件与文件夹名一律英文 kebab-case，文档正文简体中文。
- 会议纪要：`meetings/YYYY-MM-DD-主题.md`，主题用英文 kebab-case。
- 日报周期文档：`reports/daily/biweekly-NN-YYYY-MM-DD-to-YYYY-MM-DD.md`，`NN` 为两位汇报次数。
- 周期材料：`reports/materials/biweekly-NN/<材料名>.md`，`NN` 与所属周期文档一致；材料名用英文 kebab-case，原文件名登记在 [../reports/README.md](../reports/README.md) 的材料清单表中。
- 群聊归档：`chats/feishu-<群标识>.md`，一群一文件长期累积。
- 本人任务材料：`reports/my-tasks/biweekly-NN/<材料名>.md`，`NN` 为所属汇报周期；材料名用英文 kebab-case，登记在 [../reports/README.md](../reports/README.md) 的本人任务清单表中。
- 中文 Markdown 保持 UTF-8；用 PowerShell 整体读写文件时显式指定 `-Encoding UTF8`，修改后检查乱码与 Mojibake。

## 3. 页面属性与状态

每个 Wiki 页面以 YAML frontmatter 开头，至少填写 `title`、`type`、`status`、`date_added`、`date_update`；`tags` 可选。按真实情况填写，未知事实明确标注，**不保留占位符**，不适用的可选字段直接删除。

### type 取值

`index`、`methodology`、`schema`、`plan`、`log`、`artifacts`、`manual`、`checklist`、`meeting`、`report`、`chat-archive`、`material`、`my-task`。

`material` 与 `my-task` 的区别在产出方，不在归属：`material` 是**他人发出**的材料，按收到日期归入 `reports/materials/`，页面只记来源、核心内容摘要与原件指针（§6.7）；`my-task` 是**本人产出**的对外交付稿及其撰写状态记录，按所属周期归入 `reports/my-tasks/`。

`my-task` 页面不承担事实源角色：任务安排以任务线 `plan.md` 为准，进度进 `log.md`，交付物与归档状态进 `artifacts.md`；该页只记承担范围、撰写来源与成稿状态，并指回上述事实源。

`templates/` 下的骨架文件**不另设 `template` 类型**，直接写目标页面的 `type`（如会议纪要骨架写 `type: meeting`），这样复制出去的文件类型天然正确，无需改字段。骨架靠所在目录区分，不靠类型区分。

### status 取值

| 页面类型 | 取值 | 含义 |
| --- | --- | --- |
| 索引（`index.md`、目录 `README.md`） | `in-progress` \| `done` | 仅描述索引自身是否覆盖当前内容 |
| 任务线 `plan.md` | `active` \| `on-hold` \| `closed` | 任务线在推进 / 暂停 / 已收尾 |
| 日志 `log.md`、清单 `artifacts.md`、群聊归档 | `living` | 持续追加，不设完成态 |
| 手册与规范类 | `draft` \| `in-progress` \| `done` | 初稿 / 维护中 / 本页内容已完成 |
| 清单类（checklist） | `in-progress` \| `done` | 是否已逐项核实完毕 |
| 会议纪要 | `draft` \| `final` | 成稿后置 `final`，不再改写结论 |
| 日报周期文档 | `in-progress` \| `done` | 周期进行中 / 周期已结束 |
| `reports/materials/` 材料 | `final` \| `draft` | 对方已定稿 / 对方自标未定稿待更新版 |
| `reports/my-tasks/` 材料 | `in-progress` \| `submitted` \| `done` | 本人撰写中 / 已提交待评审 / 已定稿交付（交付事实同时写入任务线 `log.md` 与 `artifacts.md`） |

### 类型专属可选字段

| 类型 | 字段 |
| --- | --- |
| `meeting` | `date`（会议实际日期）、`venue`（线上 / 现场，可省） |
| `report` | `cycle`（周期起止，如 `2026-09-07..2026-09-18`） |
| `chat-archive` | `channel`（群名）、`coverage`（已归档消息的时间范围） |
| `material` | `origin`（原文件名）、`author`、`origin_date`（材料产出日期）、`cycle`（归入的周期起止） |
| `my-task` | `task_line`（所属任务线目录名）、`cycle`（所属周期起止） |
| `artifacts` / `plan` / `log` | `task_line`（所属任务线目录名） |

### 例外

以下页面不加 frontmatter：

- 根目录三个门面文件 [../README.md](../README.md)、[../AGENTS.md](../AGENTS.md)、[../CLAUDE.md](../CLAUDE.md)。它们分别是 GitHub 仓库首页和 AI 工具的加载入口，不是 Wiki 内容页，属性表对它们没有意义。
- `.gitignore` 排除的本机文件（如 `hpc/dev-access.md`）。它们不属于本仓公开内容，不纳入规范与 Lint 范围。

## 4. 索引规则

- 根 `index.md` 只链接一级内容目录与少量跨目录稳定入口，不下沉到具体页面。
- **每个一级内容目录保留一份 `README.md` 作为该目录索引，不建二级 `README.md`。** 本仓是 Public 仓库，GitHub 只自动渲染 `README.md`，因此索引沿用该文件名，内容按 [templates/directory-index.md](templates/directory-index.md) 组织。
- 目录索引的结构：frontmatter → 一句话说明收录范围（收什么、不收什么）→ 文件清单表 → 内容纪律 → 相关链接。
- **索引不复制具体页面的状态、进度或正文。** 任务线的进度看 `log.md`，材料状态看 `artifacts.md`，索引只负责导航。
- `schema/templates/`，以及 `reports/materials/`、`reports/my-tasks/` 下的周期子目录不单独设索引；周期材料与本人任务材料分别统一登记在 [../reports/README.md](../reports/README.md) 的材料清单表与本人任务清单表。

## 5. 单一事实源三件套

每条任务线固定三个事实源页面，角色不可混：

| 文件 | 角色 | 硬约束 |
| --- | --- | --- |
| `plan.md` | 工作安排与任务拆解 | **单一事实来源**。更新安排只改这里；其他仓库（`heliangos/wechat`、`dut-postdoc`）只放指针，不复制正文 |
| `log.md` | 进度日志 | **append-only**。新条目加在最上面，格式 `## [YYYY-MM-DD] <简述>`；历史条目只增不改。结论被推翻时新写一条更正，明确指出推翻了哪条、原结论在当时是否成立 |
| `artifacts.md` | 交付物清单 | 记录文件名、版本、用途、归档状态与事实源。用户明确授权时可记录受控链接及登记时间，**仍不保存原件、正文或附件** |

任务线目录下的其余页面（环境、构建、开发流程、命令卡、清单等）是手册与派生材料，不承担事实源角色。

## 6. 特定页面要求

### 6.1 任务线目录

必备 `README.md` + 三件套。新开任务线用 [templates/task-line.md](templates/task-line.md)。任务线之间相互独立，跨任务线的内容不互相记录，只在 `README.md` 中说明关系。

### 6.2 会议纪要

- 一次会议一个文件，正文区分**会上事实**与**会后行动项**；行动项落地后由对应任务线 `log.md` 承接，纪要本身不再改写。
- 同一次会议涉及多条任务线时，纪要完整保留，各任务线 `log.md` 只摘取与自己相关的结论并指回纪要。
- 结构见 [templates/meeting-note.md](templates/meeting-note.md)。

### 6.3 报告

日报的内容边界与写法由 [../reports/README.md](../reports/README.md) 规定。结构要求：一个双周周期一个文档，文档内以日期为一级标题倒序或顺序排列（沿用该目录既定顺序），不预先铺设空标题。骨架见 [templates/daily-report.md](templates/daily-report.md)。

### 6.4 群聊原文归档

- 文件内按 `## [YYYY-MM-DD]` 分小节表示**实际会话日期**，倒序排列；跨日抓取按实际日期拆分。
- 同一天来自多次抓取时用 `### 归档来源（YYYY-MM-DD 抓取）` 保留抓取时间与**覆盖范围**。飞书 Web 端只渲染已加载消息，未向上加载的早期历史必须如实写明不在归档内。
- 转写纪律（忠实照录、保留回复关系、时间戳按实际、凭据只记文件名）见 [../chats/README.md](../chats/README.md)。**没有真正读到的对话不得凭预览行推测补全。**

### 6.5 reports/my-tasks 本人任务材料

- 按**所属汇报周期**归入 `biweekly-NN/` 子目录，收本人在该周期承担任务时自己产出的稿件本体与状态记录；他人发出的材料一律走 §6.7。
- 页面记承担范围、撰写来源与成稿状态，**不承担事实源角色**：安排看任务线 `plan.md`，进度进 `log.md`，交付物与归档状态进 `artifacts.md`，页面只留指回这三者的指针。
- 正文已在别处成立时不留副本：源文档在飞书或 GitLab 的只写指针，初稿属他人材料的指向 `reports/materials/` 对应周期的归档件。
- 随文图片存入同周期目录下 `media/<同名目录>/`，按图号命名。
- 登记在 [../reports/README.md](../reports/README.md) 的本人任务清单表；状态推进（`in-progress` → `submitted` → `done`）时两处同步。

### 6.6 根 log.md

只记录**知识库自身**的维护动作：结构调整、规范变更、批量重组、目录新增或归档、Lint 结果。项目进度不进这里，归各任务线 `log.md`。

格式与任务线日志一致：append-only，新条目加在最上面，`## [YYYY-MM-DD] <动作> | <简述>`，动作取 `add`、`refactor`、`revise`、`archive`、`lint`。

### 6.7 reports/materials 周期材料

- 按**收到日期**归入周期子目录，与内容归属无关；内容归属仍由对应任务线索引维护。收到日期或归属一时确定不了的，归入最接近的周期并在材料清单表的状态栏标「待确认」，不推断、不另设暂存区中转。
- 页面只写三节，自 2026-09-29 起执行：
  - **来源**：作者、日期、发出渠道、原件形式与大小，与 [../reports/README.md](../reports/README.md) 的材料清单表登记一致。来源不明的标「待确认」，不推断作者与日期。
  - **核心内容与结论**：对原件的忠实摘要。不补全，也不加入原件没有的判断；原件没有定论的，直接写明未定。
  - **原始文件**：原件指针。原件存放在本机同目录的，用相对链接；原件在 GitLab 或飞书的，只写仓库名或文档名与文件名，不写仓库内目录和内部地址。
- 不录原文正文与图片，不设 `media/` 目录。
- 此前按原样归档的材料保持原状，不强制回改；需要改写的，逐份经用户确认。
- 材料不承担事实源角色：由它得出的结论写进对应任务线的 `log.md`，交付物状态写进 `artifacts.md`。

## 7. 存储与来源

- **本仓是 Public 仓库。** 有依据且可公开的项目事实默认完整记录；账号、密码、Token、VPN 密钥、私钥、个人隐私，以及未经授权公开再分发的源码、程序包、模型和内部附件不得写入。各目录的具体禁写项由该目录自己的 `README.md` 维护。
- 唯一例外是用户明确授权的内部文档链接：仅可登记在任务线 `artifacts.md`，不复制原件、正文或附件。
- 原件不入 Git。研究院材料留在 GitLab 与飞书云文档，本仓只登记清单与归档状态；`.gitignore` 执行此边界（`*.exe`、`*.zip`、`*.docx`、`/private/`、`/hpc/dev-access.md`，以及本机保存在周期材料目录下的原件 `reports/materials/**/*.html`）。
- 跨仓库引用写成 `<仓库名>:<仓库内路径>`（如 `heliangos:wechat/indexes/by-repository.md`），不写机器绝对路径。
- 图件放所属目录的 `media/<同名目录>/`，用相对路径嵌入；根目录不设 `assets/`。
