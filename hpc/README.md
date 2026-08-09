# 任务线：HPC 求解器性能与多后端并行

研究院当前分派给我的主任务线：针对现有求解器（SGFEM）开展性能调优与多后端并行计算，重点是异构并行计算。2026-07-20 由李宁宁部长在「研究院-高性能计算」群内发布任务拆解，工作由李部长负责协调安排。

## 文件索引

| 文件 | 作用 |
| --- | --- |
| [plan.md](plan.md) | 工作安排与任务拆解（**单一事实来源**，更新安排只改这里） |
| [log.md](log.md) | 进度日志（append-only，新条目加在最上面） |
| [artifacts.md](artifacts.md) | 交付物清单、版本、用途与归档状态 |
| [environment.md](environment.md) | 项目级环境事实、ABI 约束与已验证边界（接入细节见本地 `dev-access.md`） |
| [build-and-run.md](build-and-run.md) | WSL 唯一操作手册：三仓库同步、环境准备、增量编译、源码验证、PETSc/Hypre 切换与 Artifact 对照 |
| [quick-commands.md](quick-commands.md) | 命令卡：高频命令与红线，贴墙速查 |
| [development-workflow.md](development-workflow.md) | WSL/C++ 开发流程：概念、分支、测试、调试与分阶段检查表 |
| [sgsim-architecture-sgpsolver.md](sgsim-architecture-sgpsolver.md) | SGSim 架构、线性静力求解流程、已核验能力与权限边界、自定义求解器 SGPSolver 设计 |

## 相关链接

- 项目日报目录：[reports/daily/](../reports/daily/)
- 阶段计划的来源讨论：[meetings/2026-07-09-stage-plan-discussion.md](../meetings/2026-07-09-stage-plan-discussion.md)
- 与杜阳老师的落地路径讨论：[meetings/2026-07-20-duyang-discussion.md](../meetings/2026-07-20-duyang-discussion.md)
- 算海内部周会与任务分工：[meetings/2026-07-20-suanhai-internal-weekly.md](../meetings/2026-07-20-suanhai-internal-weekly.md)
- 与李宁宁部长确认节点与汇报机制：[meetings/2026-07-27-liningning-discussion.md](../meetings/2026-07-27-liningning-discussion.md)
- 与魏华祎老师确认汇报、日报与沟通口径：[meetings/2026-07-27-suanhai-discussion.md](../meetings/2026-07-27-suanhai-discussion.md)
- 算海项目过程仓（内部，任务 issues 与产出沉淀）：`suanhaitech/houzai`
- 沟通上下文（李宁宁、陈玉震、魏华祎等联系人的档案与聊天记录）：`heliangos:wechat/indexes/by-repository.md#dut-institute-work`
- 相关技术知识沉淀（matrix-free、PIML 方向调研）：`dut-postdoc/research/postdoc-plan/long-term/direction-1-piml-matrix-free/`
