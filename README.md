# AtomGit Skills for Claude

一键安装的 AtomGit 全功能 AI 助手技能包，覆盖 13 个核心使用场景。

## 安装方式

直接安装此 Skill，无需配置多个独立 Skill。

## Setup

```bash
export ATOMGIT_API="https://api.atomgit.com/api/v5"
export ATOMGIT_TOKEN="your-personal-access-token"
# 企业 API（仅企业管理员需要）
export ATOMGIT_API_V8="https://api.atomgit.com/api/v8"
```

获取 Token：[AtomGit 个人令牌设置](https://atomgit.com/setting/token-classic)

## 能力覆盖

| 模块 | 场景 |
|------|------|
| `references/repo.md` | 搜索/浏览仓库、读取文件、目录树 |
| `references/issue.md` | Issue 全生命周期管理 |
| `references/pr.md` | Pull Request 创建/Review/合并 |
| `references/release.md` | Tag / Release / Changelog 发版 |
| `references/branch.md` | 分支管理与保护规则 |
| `references/org.md` | 组织成员与仓库权限管理 |
| `references/milestone.md` | 里程碑追踪与进度报告 |
| `references/label.md` | 标签体系治理与跨仓库同步 |
| `references/webhook.md` | Webhook 配置与集成 |
| `references/enterprise.md` | 企业版成员管理（v8 API）|
| `references/kanban.md` | 项目看板管理 |
| `references/ai-review.md` | AI Hub 智能代码审查 |
| `references/ai-doc.md` | AI Hub 自动生成文档/README |

## 设计思路

- **单 Skill，一次安装**：用户只需安装一个 Skill，Claude 按需加载子模块
- **双模式兼容**：优先通过 MCP Server 调用（若已安装 [AtomGit-MCP-Server](https://atomgit.com/zkxw2008/AtomGit-MCP-Server)），降级时使用 `curl + jq` 直接调用 API
- **范式驱动**：每个子模块提供典型命令范式，Claude 举一反三处理变体需求

## 相关项目

- [AtomGit MCP Server](https://atomgit.com/zkxw2008/AtomGit-MCP-Server) - AtomGit 的 MCP 工具层

## License

MIT-0
