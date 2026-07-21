# CLAUDE.md

本文件指导 **Claude Code** 在 `dut-institute-work` 中工作。

## 共享上下文（import 自动加载）

下行 import 会在会话启动时加载所有 AI 共享的通用上下文，包括仓库定位、交流与写作约定、内容纪律、单一事实来源、跨仓库边界与隐私要求：

@ai/context.md

## Claude Code 专用补充

本仓库目前没有 `.claude/` 下的技能、命令、子代理或 Hook；Claude Code 直接遵守上述共享上下文即可。
