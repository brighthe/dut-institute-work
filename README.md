# dut-institute-work

我（何亮 / brighthe）在**大连工业软件创新发展研究院**（大连理工博士后期间）承担大工项目工作的个人公开总档案：统一记录任务安排、阶段计划、进度、会议纪要、自有项目文档与交付物清单。

本仓库是大工项目的统一公开入口；相邻仓库和内部系统继续作为各类内容的单一事实来源，避免重复维护：

| 内容 | 去处 |
| --- | --- |
| 项目任务、安排、分工、进度、汇报节点、日报、自有项目文档与交付物清单 | 本仓库 |
| 从工作中沉淀的可复用知识（matrix-free 方法、PETSc 调优经验、文献） | `dut-postdoc` |
| 大工项目相关飞书群聊原文 | 本仓库 `chats/`；项目结论仍进 `hpc/` |
| 与老师们的微信沟通原文与联系人上下文 | `heliangos/wechat`；本仓记录项目结论与指针 |
| 算海团队面向研究院的规范化交付材料（阶段汇报文档、对比测试报告等） | 本仓库；归属未定时先入 `staging/` |
| 算海团队内部执行过程、任务 issues 与过程产出 | `suanhaitech/houzai`；本仓记录项目状态与指针 |
| 研究院提供的源码、程序包、模型与内部文档原件 | 研究院 GitLab；本仓记录交付物清单与归档状态 |
| 结构动力学软件模块项目：投标阶段源文档与软件代码 | `structural-dynamics-software`（与研究院无关） |
| 结构动力学软件模块项目：验收阶段安排、材料状态与进度 | 本仓库 `structural-dynamics/` |

## 目录结构

```
dut-institute-work/
├── README.md      # 本说明：定位、内容去向、目录结构
├── CLAUDE.md      # Claude Code 根入口（自动加载共享上下文）
├── AGENTS.md      # Codex / Antigravity 等 AI 的根入口
├── ai/            # 工具无关的 AI 规则：context.md（工作纪律）、git-workflow.md（提交纪律）
├── hpc/           # 任务线：HPC 求解器性能与多后端并行
├── structural-dynamics/  # 任务线：结构动力学软件模块验收
├── drafts/        # 待双方确认的讨论稿；定稿后迁往正式归属位置
├── staging/       # 已成稿但归属未定的材料暂存区；归位后迁往正式目录
├── reports/       # 项目报告：日报及后续可能的双周、阶段汇报
├── meetings/      # 会议与讨论纪要，按 YYYY-MM-DD-主题.md 命名
└── chats/         # 飞书群聊原文归档，一群一文件长期累积
```

每个文件夹内的文件清单与写作规则由该文件夹的 README 维护，本树不逐个复述：任务线看 [hpc/README.md](hpc/README.md) 与 [structural-dynamics/README.md](structural-dynamics/README.md)，报告看 [reports/README.md](reports/README.md)，群聊原文看 [chats/README.md](chats/README.md)，暂存材料看 [staging/README.md](staging/README.md)。

将来研究院分派新的任务线，在根目录按 `hpc/` 的同构方式新开一个文件夹，并在本 README 登记。

## AI 架构

本仓库同时服务 Claude Code、Codex、Antigravity 等 AI 工具，文档按职责分为三类：

- [README.md](README.md) 是面向人的仓库门面，说明定位、内容去向与目录结构。
- 根目录 [CLAUDE.md](CLAUDE.md) 和 [AGENTS.md](AGENTS.md) 是各工具会自动发现的入口，只保留加载方式或运行时差异。
- [ai/context.md](ai/context.md) 是所有 AI 共享工作规则的唯一来源；[ai/git-workflow.md](ai/git-workflow.md) 只在用户明确要求 commit 或 push 时加载。

`ai/` 下不再按工具或“通用”类别建立子目录；工具无关规则直接平铺在 `ai/` 根。当前也没有需要放入 `.claude/`、`.codex/` 或 `.agents/` 的技能、命令、子代理或 Hook。
