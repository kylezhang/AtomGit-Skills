# AtomGit Skills for Claude

一键安装的 AtomGit 全功能 AI 助手技能包，通过 [AtomGit MCP Server](https://atomgit.com/zkxw2008/AtomGit-MCP-Server) 提供 **244 个工具**，覆盖 **17 个功能模块**。

## 安装方式

直接安装此 Skill，无需配置多个独立 Skill。

## 快速开始

```bash
# 1. 安装 AtomGit MCP Server
npm install -g @atomgit.com/atomgit-mcp-server

# 2. 设置环境变量
export ATOMGIT_TOKEN="your-personal-access-token"

# 3. 获取 Token
# 访问：https://atomgit.com/setting/token-classic
```

## 能力覆盖

本 Skill 通过 AtomGit MCP Server 提供完整的平台操作能力：

| 模块 | 工具数 | 主要能力 |
|------|--------|----------|
| **仓库** | 36 | 创建/查看/更新/删除/Fork/归档/转移/文件管理 |
| **Pull Request** | 45 | 创建/审核/合并/评论/分配审查人/测试人 |
| **Issue** | 29 | 创建/更新/关闭/评论/标签/里程碑/看板 |
| **分支** | 8 | 创建/删除/保护规则 |
| **Commit** | 12 | 提交历史/对比/评论/统计 |
| **Release** | 8 | Tag/Release/版本发布 |
| **Tag** | 8 | 标签管理/保护标签 |
| **标签(Labels)** | 8 | 标签系统管理 |
| **里程碑** | 5 | 里程碑追踪/进度报告 |
| **用户** | 22 | 用户信息/SSH Keys/通知 |
| **组织** | 18 | 组织管理/成员管理/自定义角色 |
| **企业** | 15 | 企业成员管理(v8 API)/里程碑 |
| **看板** | 8 | 项目看板/任务管理 |
| **Webhook** | 6 | 事件推送配置 |
| **搜索** | 3 | 仓库/Issue/用户搜索 |
| **成员** | 6 | 仓库成员权限管理 |
| **AIHub** | 7 | AI 文本生成/语音识别/图像检测/视频生成 |

**详细工具列表请参阅 [atomgit/SKILL.md](./atomgit/SKILL.md)**

## 设计思路

- **单 Skill，一次安装**：用户只需安装一个 Skill，Claude 自动识别并调用相应工具
- **MCP 原生集成**：通过 [AtomGit MCP Server](https://atomgit.com/zkxw2008/AtomGit-MCP-Server) 直接调用，无需手动处理 API
- **范式驱动**：提供典型命令范式，Claude 举一反三处理变体需求

## 工具依赖

详见 [TOOLS.md](./TOOLS.md)

## 相关项目

- [AtomGit MCP Server](https://atomgit.com/zkxw2008/AtomGit-MCP-Server) — AtomGit 的 MCP 工具层
- [AtomGit API 文档](https://docs.atomgit.com/docs/apis/) — 官方 API 参考

## License

MIT-0