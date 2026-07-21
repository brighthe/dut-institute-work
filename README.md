# dut-institute-work

我（何亮 / brighthe）在**大连工业软件创新发展研究院**（大连理工博士后期间）承担工作的管理仓库：任务安排、阶段计划、进度记录与会议纪要。

**这里管"事"，不管"知识"，也不管"人"** —— 与相邻仓库的边界：

| 内容 | 去处 |
| --- | --- |
| 研究院分派的任务、安排、分工、进度、汇报节点 | 本仓库 |
| 从工作中沉淀的可复用知识（matrix-free 方法、PETSc 调优经验、文献） | `dut-postdoc` |
| 与老师们的微信沟通记录与未决事项 | `heliangos/wechat`（只留一句话指针指回本仓库） |
| 结构动力学软件投标项目 | `structural-dynamics-software`（独立项目，与研究院无关） |

## 目录结构

```
dut-institute-work/
├── README.md                # 本说明：定位、边界、纪律
├── CLAUDE.md                # Claude Code 根入口（自动加载共享上下文）
├── AGENTS.md                # Codex / Antigravity 等 AI 的根入口
├── ai/                      # 工具无关的 AI 规则，文件直接平铺在本层
│   ├── context.md           # 所有 AI 共享工作规则的唯一来源
│   └── git-workflow.md      # 本仓库特有的提交与推送纪律
├── hpc/                     # 任务线：HPC 求解器性能与多后端并行（当前唯一任务线）
│   ├── README.md            # 任务线概况与文件索引
│   ├── plan.md              # 工作安排与任务拆解（单一事实来源）
│   └── log.md               # 进度日志（append-only）
└── meetings/                # 会议与讨论纪要，按 YYYY-MM-DD-主题.md 命名
```

将来研究院分派新的任务线，在根目录按 `hpc/` 的同构方式新开一个文件夹，并在本 README 登记。

## AI 架构

本仓库同时服务 Claude Code、Codex、Antigravity 等 AI 工具，文档按职责分为三类：

- [README.md](README.md) 是面向人的仓库门面，说明定位、目录结构、边界与公开脱敏纪律。
- 根目录 [CLAUDE.md](CLAUDE.md) 和 [AGENTS.md](AGENTS.md) 是各工具会自动发现的入口，只保留加载方式或运行时差异。
- [ai/context.md](ai/context.md) 是所有 AI 共享工作规则的唯一来源；[ai/git-workflow.md](ai/git-workflow.md) 只在用户明确要求 commit 或 push 时加载。

`ai/` 下不再按工具或“通用”类别建立子目录；工具无关规则直接平铺在 `ai/` 根。当前也没有需要放入 `.claude/`、`.codex/` 或 `.agents/` 的技能、命令、子代理或 Hook。

## 公开与脱敏纪律（重要）

**本仓库为 Public、公开可见**，但内容涉及研究院内部工作。写入与提交前从严把关：

- **绝不写入**：账号、密码、财务信息、合同金额、未公开的技术成果细节、内部文件原文/附件。
- **谨慎写入**：具体分工与进度只记到"我能对外说"的粒度；拿不准的，粗化表述或标注「细节线下记录」。
- 对他人的评价性内容一律不写；涉及承诺、责任的措辞留有余地。
- 提交前逐一 `git diff` 已暂存内容核查，规程见 [ai/git-workflow.md](ai/git-workflow.md)。
