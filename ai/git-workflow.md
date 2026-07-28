# Git 提交与推送工作流（本仓库）

> **触发时机**：用户要求提交（commit）或推送（push）到远程时，先读本文件再操作。本文件是操作规程，不是用户发起型任务，无需启动语——用户说“提交/推送”即触发。
>
> **机器级 Git / SSH 配置已上移**：原生 Git 原则、SSH over 443 鉴权、新机器一次性配置、各机现状与排错，统一见 `workstation` 仓库的 Git 模块（[github.com/brighthe/workstation → git/README.md](https://github.com/brighthe/workstation/blob/main/git/README.md)；新机器可读 raw 版 `https://raw.githubusercontent.com/brighthe/workstation/main/git/README.md`）。本文件只保留 **dut-institute-work 特有**的提交纪律。

`dut-institute-work` 是**大工项目个人公开总档案**，且是 **Public 仓库、内容涉及单位内部工作**——提交前必须完成公开发布检查。

## 操作要点（本仓库）

- 远程：`git@github.com:brighthe/dut-institute-work.git`（SSH over 443，配置见上方 `workstation` 指针）。
- 在 Windows 上使用 PowerShell 和系统原生 Git/OpenSSH；不要使用 Cygwin、MSYS、Git Bash 或 WSL Git/SSH。

## 提交纪律（本仓库 = Public 且涉及单位内部工作）

- **仅在用户明确要求时**提交或推送。
- **公开发布检查是硬约束**：提交前逐一检查已暂存内容的 `git diff`，确认没有账号、密码、Token、VPN 密钥、私钥、个人隐私、非公开财务或合同信息，以及未经授权公开再分发的源码、程序包、模型或内部附件。有依据且可公开的项目事实无需删减或粗化。完整要求见 [context.md](context.md) 的「内容纪律」。
- `git status` 中出现 PDF、截图、表格、压缩包等附件时必须停下核对权利方的公开授权；没有明确授权时不得提交。
- 使用 `git status` 甄别，只 `git add` 本次任务相关文件，不使用 `git add -A`。
- 提交信息使用简体中文，并在结尾附与实际协作者一致的 `Co-Authored-By` 尾注。
- 在 `main` 上直接提交，不开分支、不走 PR。
