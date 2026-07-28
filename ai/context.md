# dut-institute-work 通用上下文（所有 AI 共享）

> 本文件是 `dut-institute-work` 对**所有 AI 助手**（Claude Code / Codex / Antigravity 等）通用工作规则的唯一来源。各工具的入口见根目录 [CLAUDE.md](../CLAUDE.md) 或 [AGENTS.md](../AGENTS.md)。

**`dut-institute-work` 是何亮（brighthe）在大连工业软件创新发展研究院工作期间的大工项目个人公开总档案。** 本仓统一记录项目事实、自有项目文档与交付物清单；可复用技术知识、聊天原文、公司执行资料和研究院原始交付物仍由各自的单一事实来源承载。仓库定位、内容去向和目录结构详见 [README.md](../README.md)，内容纪律以本文件为准。

## 交流与写作

- **交流语言**：一律简体中文；代码、命令、专有名词保留英文。
- 文档正文使用简体中文，文件与文件夹名使用英文 kebab-case；会议纪要按 `YYYY-MM-DD-主题.md` 命名。
- 编辑中文 Markdown 时保持 UTF-8，修改后检查乱码和 Mojibake。

## 内容纪律

- **本仓库为 Public**：有依据且可公开的项目事实默认完整记录；账号密码、Token、VPN 密钥、私钥、个人隐私，以及未经授权公开再分发的源码、程序包、模型和内部附件不得写入。
- **不替用户编造**：任务安排、时间节点、他人说过的话只写有依据的内容；拿不准时标注「待确认」。
- [hpc/plan.md](../hpc/plan.md) 是研究院 HPC 工作安排的**单一事实来源**：其他仓库（`heliangos/wechat`、`dut-postdoc`）只放指针，不复制正文；更新安排时只改这里。
- [hpc/log.md](../hpc/log.md) 是 append-only 进度日志：新条目只在文件头部追加，不修改历史条目。
- [hpc/artifacts.md](../hpc/artifacts.md) 是 HPC 交付物清单：记录文件名、版本、用途、归档状态和事实源，不保存未获公开授权的原件或内部访问地址。

## 跨仓库边界

- 可复用的技术知识（方法调研、调优经验、文献笔记）属于 `dut-postdoc`；本仓库只留一句话链接。
- 微信聊天原文属于 `heliangos/wechat`；本仓库记录项目结论与指针，不保存聊天原文或截图。
- 算海团队执行任务与内部产出属于 `suanhaitech/houzai`；研究院源码、程序包、模型和内部文档原件属于研究院 GitLab。本仓统一索引，不复制这些事实源。
- 结构动力学软件投标项目属于 `structural-dynamics-software`，不并入本仓库。

内容放错地方比暂时不写更糟。遇到边界不清的材料时，先提醒用户确认归属。

## Git

仅当用户明确要求 commit 或 push 时，读取并遵守 [git-workflow.md](git-workflow.md)。机器级 Git/SSH 配置由 `workstation` 仓库统一维护，本仓库只规定自身的提交与公开发布纪律。
