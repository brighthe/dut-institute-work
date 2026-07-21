# Git 提交与推送工作流（本仓库）

> **触发时机**：用户要求提交（commit）或推送（push）到远程时，先读本文件再操作。本文件是操作规程，不是用户发起型任务，无需启动语——用户说“提交/推送”即触发。
>
> **机器级 Git / SSH 配置已上移**：原生 Git 原则、SSH over 443 鉴权、新机器一次性配置、各机现状与排错，统一见 `workstation` 仓库的 Git 模块（[github.com/brighthe/workstation → git/README.md](https://github.com/brighthe/workstation/blob/main/git/README.md)；新机器可读 raw 版 `https://raw.githubusercontent.com/brighthe/workstation/main/git/README.md`）。本文件只保留 **dut-institute-work 特有**的提交纪律。

`dut-institute-work` 是**研究院工作管理库**，且是 **Public 仓库、内容涉及单位内部工作**——提交纪律以“脱敏”为第一位。

## 操作要点（本仓库）

- 远程：`git@github.com:brighthe/dut-institute-work.git`（SSH over 443，配置见上方 `workstation` 指针）。
- 在 Windows 上使用 PowerShell 和系统原生 Git/OpenSSH；不要使用 Cygwin、MSYS、Git Bash 或 WSL Git/SSH。

## 提交纪律（本仓库 = Public 且涉及单位内部工作，脱敏优先）

- **仅在用户明确要求时**提交或推送。
- **脱敏是硬约束**：提交前逐一检查已暂存内容的 `git diff`，确认没有账号、密码、财务信息、合同金额、内部文件原文、未公开技术成果细节或不宜公开的具体分工与进度；必要时先脱敏或粗化。完整红线见根目录 [README.md](../README.md) 的「公开与脱敏纪律」。
- **绝不提交内部文件附件**，包括 PDF、截图、表格原件；`git status` 中出现附件时必须停下核对。
- 使用 `git status` 甄别，只 `git add` 本次任务相关文件，不使用 `git add -A`。
- 提交信息使用简体中文，并在结尾附与实际协作者一致的 `Co-Authored-By` 尾注。
- 在 `main` 上直接提交，不开分支、不走 PR。
