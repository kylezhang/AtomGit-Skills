# AtomGit Skill

跨平台 AtomGit skill，面向 Codex、Claude Code 和 OpenClaw。仓库根目录就是 skill 根目录，因此核心入口放在 [`SKILL.md`](./SKILL.md)，更细的说明按需拆分到 [`references/`](./references/)。

## 结构

- `SKILL.md`：给 agent 读的主入口，只保留触发语义、工具命名约定和高频工作流
- `references/`：按 17 个 AtomGit 分类拆分的渐进披露文档，另含独立 setup/safety 文档
- `agents/openai.yaml`：Codex UI 元数据

## 依赖

- [AtomGit MCP Server](https://atomgit.com/zkxw2008/AtomGit-MCP-Server)
- `ATOMGIT_TOKEN`

AtomGit MCP Server 需要在第一次执行 AtomGit 任务之前，先按客户端级别完成安装和配置。当前仓库采用与官方 README 一致的思路：优先使用 `npx -y @atomgit.com/atomgit-mcp-server`，并在 MCP 客户端配置中注入 `ATOMGIT_TOKEN`。

安装、权限和安全边界请看 [references/setup-and-safety.md](./references/setup-and-safety.md)。

## 设计目标

- 只在明确的 AtomGit 上下文中触发，避免和 GitHub、GitLab 等平台混淆
- 用渐进披露组织内容，减少主 skill 的上下文负担
- 统一使用 `atomgit_*` 形式的 canonical method 名称，并提醒运行时名称可能带额外命名空间

## License

MIT-0
