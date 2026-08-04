# CLAUDE.md

本文件指导 **Claude Code** 在 `dut-institute-work` 中工作。

## 共享上下文（import 自动加载）

下行 import 会在会话启动时加载所有 AI 共享的通用上下文，包括仓库定位、交流与写作约定、内容纪律、单一事实来源、跨仓库边界与隐私要求：

@ai/context.md

## Claude Code 专用补充

本仓库目前没有 `.claude/` 下的技能、命令、子代理或 Hook；Claude Code 直接遵守上述共享上下文即可。

### 读取研究院 GitLab（需登录态）

按共享上下文「内部系统的访问」，需要读取 `git.ai4sim.com` 上的手册、README、配置文件等内容时，**用 Chrome 扩展工具**（`claude-in-chrome`）——它复用用户真实 Chrome 中已登录的 GitLab 会话。另外两种方式都不可用，不必再试：

| 方式 | 结果 |
| --- | --- |
| `claude-in-chrome` | ✅ 可读，走用户已登录会话 |
| `WebFetch` | ❌ 无登录态，只会拿到 302 跳转登录页 |
| 内置浏览器（`Claude_Browser`） | ❌ 该站点返回「requires per-action approval，Browser read tools are not available」 |

命令行侧（`curl`、`git`）能连通，但同样无登录态：匿名请求返回 302，`git` 走明文 HTTP 会被 Git Credential Manager 拒绝。**需要拉取内容时走 SSH**，配置见 [hpc/environment.md](hpc/environment.md)。

**默认行为**：用户直接贴出 `git.ai4sim.com` 的链接时，视为要求读取该页面，直接用 Chrome 扩展打开并取正文，不要先试其他方式、也不必反问。若返回的是登录页，说明会话已过期，告知用户重新登录即可，**不要尝试代为登录**。

访问前提（任一不满足会失败，需向用户说明而非反复重试）：VPN 已连接、Chrome 正在运行且扩展已连接、GitLab 登录态未过期。

### 读取飞书聊天记录

飞书的可用通道与 GitLab **正好相反**，不要套用上一节的结论：

| 方式 | 结果 |
| --- | --- |
| `claude-in-chrome`（Chrome 扩展） | ❌ `Navigation to this domain is not allowed`，域名层面被拒，无解 |
| 内置浏览器的 `navigate` | ❌ `navigation to ... was denied or failed` |
| **内置浏览器的 `preview_start`** | ✅ **可用**，`navOk: true`，且复用内置浏览器自身的飞书登录态 |

**关键差别在 `preview_start` 与 `navigate`**：同为内置浏览器，前者能打开飞书，后者被拒。被拒过一次不要放弃，换 `preview_start` 重试。

**标准流程**：

1. `preview_start` 传入飞书消息页 URL（**不要先试 `navigate`**）；
2. `get_page_text` / `read_page` 读取——**面板隐藏也能读**，此时可拿到会话列表与每条会话的最新一条预览；
3. 需要点开某个会话读完整对话时，**浏览器面板必须处于显示状态**。面板隐藏时页面不合成画面，元素没有布局盒，`computer` 点击会落在 `(0,0)` 而静默失效，`screenshot` 则直接报 `the Browser pane is not displayed`。遇到这种情况请用户显示面板，或请用户自己点开目标会话，不要反复重试点击。

**登录态失效**时会看到登录页，告知用户重新登录，**不要尝试代为登录**。

读到的聊天内容按共享上下文「内容纪律」处理：结论进 [hpc/log.md](hpc/log.md)，原文若需留证按 [chats/README.md](chats/README.md) 归档，**聊天中出现的凭据只记文件名不摘录内容**。**没有真正读到的对话不得凭预览行推测补全**——归档要求原文照录。
