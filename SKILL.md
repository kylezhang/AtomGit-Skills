---
name: atomgit
description: |
  AtomGit Skill — 通过 AtomGit MCP Server 操作 AtomGit 代码托管平台的完整指南。
  
  **触发关键词（不限于此列表，凡涉及 AtomGit 操作均应触发）：**
  - AtomGit、atomgit.com、仓库、代码库
  - Issue、PR、Pull Request、分支、Commit
  - Release、Tag、Webhook、里程碑、标签
  - 组织、企业、成员、权限、看板
  - "我有哪些仓库"、"帮我看看这个 PR"、"新建一个 issue" 等
  
  本 Skill 包含 244 个工具，覆盖 17 个功能模块。遇到 AtomGit 相关任务，**请务必优先查阅本 Skill**。

compatibility:
  mcp_server: "@atomgit.com/atomgit-mcp-server"
  requires_token: ATOMGIT_TOKEN
---

# AtomGit Skill

## 概述

本 Skill 指导 Claude 通过 **AtomGit MCP Server** 与 AtomGit 平台交互。MCP Server 提供 244 个工具，覆盖仓库、分支、提交、Issue、Pull Request、用户、组织、企业、看板、Release、Tag、标签、成员、里程碑、搜索、Webhook 和 AIHub。

**工具命名规范：** 所有工具名称以 `atomgit_` 前缀开头（MCP server 注册后自动加前缀），例如 `atomgit_get_repository`。

## 快速开始

### 1. 安装 MCP Server

```bash
npm install -g @atomgit.com/atomgit-mcp-server
```

### 2. 获取 Access Token

访问 [AtomGit 个人令牌设置](https://atomgit.com/setting/token-classic)，创建具有所需权限的 Token。

### 3. 设置环境变量

```bash
export ATOMGIT_TOKEN="your-personal-access-token"
```

### 4. 验证连接

```
用户：我有哪些仓库？
Claude：[调用 get_current_user_repos] 您有以下仓库...
```

## 快速参考 — 常用工具

### 🗂️ 仓库

| 操作 | 工具 |
|------|------|
| 查看仓库 | `get_repository` |
| 列出我的仓库 | `get_current_user_repos` |
| 创建仓库 | `create_user_repository` |
| 获取文件内容 | `get_repository_content` |
| 更新文件 | `update_repository_file` |

### 🐛 Issue

| 操作 | 工具 |
|------|------|
| 列出 Issues | `get_repository_issues` |
| 创建 Issue | `create_repository_issue` |
| 关闭 Issue | `update_repository_issue` (state=closed) |
| 添加评论 | `create_repository_issue_comment` |

### 🔀 Pull Request

| 操作 | 工具 |
|------|------|
| 列出 PRs | `get_repository_pulls` |
| 创建 PR | `create_repository_pull` |
| 合并 PR | `merge_repository_pull` |
| 添加审查人 | `assign_repository_pull_approval_reviewers` |

### 🌿 分支

| 操作 | 工具 |
|------|------|
| 列出分支 | `get_repository_branches` |
| 创建分支 | `create_repository_branch` |
| 设置保护 | `create_branch_protection_rule` |

### 📦 Release

| 操作 | 工具 |
|------|------|
| 列出 Releases | `get_repository_releases` |
| 创建 Release | `create_repository_release` |
| 创建 Tag | `create_repository_tag` |

## 常见参数

| 参数 | 说明 | 示例 |
|------|------|------|
| `owner` | 仓库所有者 | `zkxw2008` |
| `repo` | 仓库名称 | `AtomGit-MCP-Server` |
| `branch` | 分支名称 | `main` |
| `issue_number` | Issue 编号 | `42` |
| `pull_number` | PR 编号 | `15` |
| `tag` | Tag 名称 | `v1.0.0` |

## 典型工作流

### 代码审查

```
1. get_repository_pulls(owner, repo) → 获取 PR 列表
2. get_repository_pull_files(owner, repo, pull_number) → 查看变更
3. create_repository_pull_comment(...) → 添加评论
4. process_repository_pull_review(..., action="approved") → 审核通过
```

### Issue 管理

```
1. get_repository_labels(owner, repo) → 查看标签
2. create_repository_issue(owner, repo, title, body, labels) → 创建 Issue
```

### 发版

```
1. get_repository_tags(owner, repo) → 确认 Tag
2. create_repository_release(owner, repo, tag_name, ...) → 发布
```

## 安全注意事项

⚠️ **永远不要把 Token 提交到代码仓库**

- 使用环境变量存储 Token
- 定期轮换 Token（建议 90 天）
- 按最小权限原则授予权限

## 危险操作保护

以下操作默认禁用（需设置 `ATOMGIT_ENABLE_DANGEROUS_TOOLS=true`）：
- `delete_*` — 删除操作
- `remove_*` — 移除操作
- `leave_*` — 退出操作
- `archive_*` — 归档操作
- `transfer_*` — 转移操作

**执行危险操作前，请务必向用户确认！**

## 详细文档

完整的工具列表和参数说明，请参阅 [atomgit/SKILL.md](./atomgit/SKILL.md)。

## 相关链接

- [AtomGit MCP Server](https://atomgit.com/zkxw2008/AtomGit-MCP-Server)
- [AtomGit API 文档](https://docs.atomgit.com/docs/apis/)
- [获取 Token](https://atomgit.com/setting/token-classic)