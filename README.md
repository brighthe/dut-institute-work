# dut-institute-work

我（何亮 / brighthe）在**大连工业软件创新发展研究院**（大连理工博士后期间）承担大工项目工作的个人公开总档案：统一记录任务安排、阶段计划、进度、会议纪要、自有项目文档与交付物清单。

按 [Karpathy「LLM Wiki」模式](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f) 运转——由多种 AI 工具增量构建与维护的、相互链接的 Markdown wiki。工具无关的常驻规则见 [AGENTS.md](AGENTS.md)（Codex、Antigravity 直接加载），[CLAUDE.md](CLAUDE.md) 是 Claude Code 的导入桩（只引用 `AGENTS.md`，不含独立规则），各工具指向同一份规则。全库内容入口见 [index.md](index.md)。

全局 AI 工具配置与跨设备迁移说明由个人工具仓库 `workstation`（GitHub: `brighthe/workstation`）维护；本仓库只记录 `dut-institute-work` 的项目级规则、工作流与项目状态。

## 仓库用途

在**大工项目的原始沟通与内部系统**和**我**之间，维护一个持久、结构化、可被 LLM 读写的中间层，使「现在什么状态、当时怎么定的、材料齐不齐」这类问题不必每次重新翻聊天记录和云文档。三层架构：

- **原始源层**：研究院 GitLab、飞书云文档、聊天流与相邻仓库；AI 只读不改，原件不入 Git
- **Wiki 层**：任务线事实源（`plan` / `log` / `artifacts`）、会议纪要、群聊原文、日报与周期材料
- **Schema 层**：`schema/` + 根目录工具入口 + `schema/templates/` 定义页面约定；工具无关的常驻协作规则在 `AGENTS.md`

## 内容去向

本仓库是大工项目的统一公开入口；相邻仓库和内部系统继续作为各类内容的单一事实来源，避免重复维护：

| 内容 | 去处 |
| --- | --- |
| 项目任务、安排、分工、进度、汇报节点、日报、自有项目文档与交付物清单 | 本仓库 |
| 从工作中沉淀的可复用知识（matrix-free 方法、PETSc 调优经验、文献） | `dut-postdoc` |
| 大工项目相关飞书群聊原文 | 本仓库 `chats/`；项目结论仍进 `hpc/` |
| 与老师们的微信沟通原文与联系人上下文 | `heliangos/wechat`；本仓记录项目结论与指针 |
| 算海团队面向研究院的规范化交付材料（阶段汇报文档、对比测试报告等） | 本仓库 `reports/materials/biweekly-NN/`，按收到日期归入对应汇报周期；收到日期或归属不确定时归入最接近的周期并在材料清单标「待确认」 |
| 本人承担撰写的对外交付稿及其撰写状态（研究院规划章节、方案稿等） | 本仓库 `reports/my-tasks/biweekly-NN/`，按所属汇报周期归入；正文已在飞书或 GitLab 成立的只留指针，交付事实仍写任务线 `log.md` / `artifacts.md` |
| 算海团队内部执行过程、任务 issues 与过程产出 | `suanhaitech/houzai`；本仓记录项目状态与指针 |
| 研究院提供的源码、程序包、模型与内部文档原件 | 研究院 GitLab；本仓记录交付物清单与归档状态 |
| 结构动力学软件模块项目：投标阶段源文档与软件代码 | `structural-dynamics-software`（与研究院无关） |
| 结构动力学软件模块项目：验收阶段安排、材料状态与进度 | 本仓库 `structural-dynamics/` |

## 目录结构

```
dut-institute-work/
├── README.md      # 本说明：定位、内容去向、目录结构（面向人的门面）
├── index.md       # 全库总导航：任务线、内容领域与稳定入口
├── log.md         # 知识库维护时间线（结构与规范变更；项目进度归任务线）
├── AGENTS.md      # 工具无关常驻协作规则（唯一来源）
├── CLAUDE.md      # Claude Code 导入桩：只引用 AGENTS.md，不含独立规则
├── schema/        # Schema 层：页面规范、方法论与模板（不放操作规程）
│   ├── README.md
│   ├── llm-wiki-methodology.md
│   ├── page-schemas.md
│   └── templates/
├── hpc/           # 任务线：HPC 求解器性能与多后端并行
├── structural-dynamics/  # 任务线：结构动力学软件模块验收
├── meetings/      # 会议与讨论纪要，按 YYYY-MM-DD-主题.md 命名
├── reports/       # 项目报告：日报、双周汇报周期文档，以及周期内收到的材料（摘要与原件指针）与本人任务材料
└── chats/         # 飞书群聊原文归档，一群一文件长期累积
```

每个文件夹内的文件清单与内容纪律由该文件夹的 `README.md` 维护，本树不逐个复述：任务线看 [hpc/README.md](hpc/README.md) 与 [structural-dynamics/README.md](structural-dynamics/README.md)，纪要看 [meetings/README.md](meetings/README.md)，报告、周期材料与本人任务材料看 [reports/README.md](reports/README.md)，群聊原文看 [chats/README.md](chats/README.md)，规范与模板看 [schema/README.md](schema/README.md)。

将来研究院分派新的任务线，按 [schema/templates/task-line.md](schema/templates/task-line.md) 在根目录新开一个文件夹，并在本 README 的内容去向表与 [index.md](index.md) 登记。

## 三个核心操作

见 [方法论](schema/llm-wiki-methodology.md)：

- **Ingest**：一次会议 / 一段群聊 / 一份团队交付材料 / 一天工作 → 落成纪要、`chats/` 原文、`reports/` 周期材料或日报，并把结论回写任务线 `plan.md` / `log.md` / `artifacts.md`
- **Query**：基于任务线事实源回答项目状态、历史口径与材料缺口，带来源指针；不自动回填页面
- **Lint**：体检 plan 与 log 是否矛盾、artifacts 是否过期、`reports/` 的材料清单与本人任务清单有没有漏登或过期的行、跨仓库指针是否失效、有没有内容越过 Public 边界

## AI 架构

本仓库同时服务 Claude Code、Codex、Antigravity 等 AI 工具，采用「单一事实源 + 多工具导入桩」架构：

- [AGENTS.md](AGENTS.md) 是**工具无关常驻规则的唯一来源**，Codex、Antigravity 等直接读取。
- [CLAUDE.md](CLAUDE.md) 只引用 `AGENTS.md`，不含独立规则。各内部系统可用的读取通道按内容归属维护：GitLab 见 [hpc/environment.md](hpc/environment.md) 3.4，飞书见 [chats/README.md](chats/README.md)。
- [schema/page-schemas.md](schema/page-schemas.md) 集中页面归属、属性、索引与存储要求，`schema/templates/` 提供骨架。
- 各目录「写什么、不写什么」由该目录自己的 `README.md` 维护，内容去向见上文内容去向表；常驻规则里不复述。

导入桩不复制规则正文，杜绝规则漂移。当前没有需要放入 `.claude/`、`.codex/` 或 `.agents/` 的技能、命令、子代理或 Hook。
