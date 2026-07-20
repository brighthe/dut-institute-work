# CLAUDE.md

Claude Code 在本仓库工作时遵循以下规则。仓库定位、目录结构与边界见 [README.md](README.md)，先读它。

## 交流与写作

- **交流语言**：一律简体中文；代码、命令、专有名词保留英文。
- 文档正文简体中文，文件与文件夹名用英文 kebab-case（会议纪要按 `YYYY-MM-DD-主题.md`）。

## 内容纪律

- **本仓库 Public**，脱敏纪律见 [README.md](README.md)「公开与脱敏纪律」，写入前逐条对照，是硬约束。
- **不替我编造**：任务安排、时间节点、他人说过的话，只写有依据的；拿不准标注「待确认」。
- `hpc/plan.md` 是工作安排的**单一事实来源**：其他仓库（heliangos/wechat、dut-postdoc）只放指针，不复制正文。更新安排时改这里，不要在别处另写一份。
- `hpc/log.md` 为 append-only 进度日志：只在文件头部追加新条目，不改历史条目。

## 边界（内容放错地方比不写更糟）

- 可复用的技术知识（方法调研、调优经验、文献笔记）→ 提醒我放 `dut-postdoc`，本仓库只留一句话链接。
- 微信聊天原文 → 属于 `heliangos/wechat`，本仓库不存聊天记录。

## Git

提交/推送前先读 [ai/common/git-workflow.md](ai/common/git-workflow.md)（SSH-over-443、只在用户要求时提交、`main` 直提不开分支、逐 diff 脱敏核查）。
