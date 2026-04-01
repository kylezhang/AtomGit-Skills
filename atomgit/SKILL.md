---
name: atomgit
description: |
  AtomGit MCP Skill — 让 Claude 通过 AtomGit MCP Server 操作 AtomGit 代码托管平台的完整指南。
  
  **必须使用本 Skill 的场景（不限于此列表，凡涉及 AtomGit 操作均应触发）：**
  - 用户提到"AtomGit"、"atomgit.com"、"仓库"、"代码库"等关键词
  - 需要管理仓库（创建/查看/更新/删除/Fork/归档/转移）
  - 需要操作分支（创建/删除/查看/设置保护规则）
  - 需要操作 Commit（查看/对比/评论/统计）
  - 需要管理 Issue（创建/更新/关闭/评论/标签/里程碑/看板）
  - 需要管理 Pull Request（创建/审核/合并/评论/分配审查人/测试人）
  - 需要操作用户和组织（查看信息/管理成员/权限）
  - 需要管理 Release、Tag、Webhook
  - 需要使用 AIHub（AI 文本生成/音频识别/图像检测/视频生成）
  - 用户问"我有哪些仓库"、"帮我看看这个 PR"、"新建一个 issue"等
  
  本 Skill 覆盖 17 个功能模块，244 个工具。遇到 AtomGit 相关任务，**请务必优先查阅本 Skill**，而不是猜测工具参数。
compatibility:
  mcp_server: "@atomgit.com/atomgit-mcp-server"
  requires_token: ATOMGIT_TOKEN (Personal Access Token)
---

# AtomGit Skill

## 概述

本 Skill 指导 Claude 通过 **AtomGit MCP Server** 与 AtomGit 平台交互。MCP Server 提供 244 个工具，覆盖仓库、分支、提交、Issue、Pull Request、用户、组织、企业、看板、Release、Tag、标签、成员、里程碑、搜索、Webhook 和 AIHub。

**工具命名规范：** 所有工具名称以 `atomgit_` 前缀开头（MCP server 注册后自动加前缀），例如 `atomgit_get_repository`。

## 快速参考 — 按场景选择工具

### 🗂️ 仓库操作

| 场景 | 工具 |
|------|------|
| 查看某个仓库详情 | `get_repository` |
| 列出当前用户所有仓库 | `get_current_user_repos` |
| 列出某用户的仓库 | `get_user_repos` |
| 创建个人仓库 | `create_user_repository` |
| 创建组织仓库 | `create_organization_repository` |
| 更新仓库信息 | `update_repository` |
| 删除仓库 | `delete_repository` |
| Fork 仓库 | `fork_repository` |
| 归档仓库 | `archive_repository` |
| 转移仓库 | `transfer_repository` / `transfer_repository_to_org` |
| 获取仓库文件内容 | `get_repository_content` |
| 创建文件 | `create_repository_file` |
| 更新文件 | `update_repository_file` |
| 删除文件 | `delete_repository_file` |
| 上传文件 | `upload_repository_file` |
| 获取仓库目录树 | `get_repository_tree` |
| 获取文件列表 | `get_repository_file_list` |
| 获取 raw 文件 | `get_repository_raw_file` |
| 获取贡献者 | `get_repository_contributors` |
| 获取仓库语言 | `get_repository_languages` |
| 获取仓库设置 | `get_repository_settings` |
| 更新仓库设置 | `update_repository_settings` |
| 获取 Star 用户 | `get_repository_stargazers` |
| 获取 Watch 用户 | `get_repository_subscribers` |

### 🌿 分支操作

| 场景 | 工具 |
|------|------|
| 列出所有分支 | `get_repository_branches` |
| 获取单个分支 | `get_repository_branch` |
| 创建分支 | `create_repository_branch` |
| 删除分支 | `delete_repository_branch` |
| 获取分支保护规则 | `get_branch_protection_rules` |
| 创建分支保护规则 | `create_branch_protection_rule` |
| 更新分支保护规则 | `update_branch_protection_rule` |
| 删除分支保护规则 | `delete_branch_protection_rule` |

### 📝 Commit 操作

| 场景 | 工具 |
|------|------|
| 列出所有 Commit | `get_repository_commits` |
| 获取单个 Commit | `get_repository_commit` |
| 对比两个 Commit | `compare_repository_commits` |
| 获取 Commit diff | `get_repository_commit_diff` |
| 获取 Commit patch | `get_repository_commit_patch` |
| 创建 Commit 评论 | `create_repository_commit_comment` |
| 获取 Commit 评论 | `get_repository_commit_comments` |
| 获取贡献统计 | `get_repository_commit_statistics` |

### 🐛 Issue 操作

| 场景 | 工具 |
|------|------|
| 列出仓库 Issues | `get_repository_issues` |
| 获取单个 Issue | `get_repository_issue` |
| 创建 Issue | `create_repository_issue` |
| 更新 Issue | `update_repository_issue` |
| 获取当前用户 Issues | `get_user_issues` |
| 获取组织 Issues | `get_organization_issues` |
| 获取企业 Issues | `get_enterprise_issues` |
| 创建 Issue 评论 | `create_repository_issue_comment` |
| 获取 Issue 评论 | `get_repository_issue_comments` |
| 获取 Issue 关联分支 | `get_repository_issue_related_branches` |
| 获取 Issue 关联 PR | `get_repository_issue_pull_requests` |

### 🔀 Pull Request 操作

| 场景 | 工具 |
|------|------|
| 列出仓库 PRs | `get_repository_pulls` |
| 获取单个 PR | `get_repository_pull` |
| 创建 PR | `create_repository_pull` |
| 更新 PR | `update_repository_pull` |
| 合并 PR | `merge_repository_pull` |
| 获取 PR 文件变更 | `get_repository_pull_files` |
| 获取 PR Commits | `get_repository_pull_commits` |
| 创建 PR 评论 | `create_repository_pull_comment` |
| 获取 PR 所有评论 | `get_repository_pull_comments` |
| 分配审查人 | `assign_repository_pull_approval_reviewers` |
| 分配责任人 | `assign_repository_pull_assignees` |
| 分配测试人 | `assign_repository_pull_testers` |
| 处理审核（通过/拒绝）| `process_repository_pull_review` |
| 回复讨论评论 | `reply_pull_request_discussion` |
| 关联 Issue | `link_repository_pull_issues` |
| 检查是否可合并 | `get_repository_pull_merge_status` |
| 获取企业 PRs | `get_enterprise_pull_requests` |
| 获取组织 PRs | `get_organization_pull_requests` |

### 👤 用户操作

| 场景 | 工具 |
|------|------|
| 获取当前用户信息 | `get_current_user` |
| 更新当前用户资料 | `update_current_user` |
| 获取某用户信息 | `get_user` |
| 获取当前用户邮箱 | `get_current_user_emails` |
| 获取当前用户 SSH Keys | `get_current_user_keys` |
| 添加 SSH Key | `add_user_key` |
| 删除 SSH Key | `delete_user_key` |
| 列出当前用户组织 | `get_current_user_organizations` |
| 获取当前用户 PR 列表 | `get_current_user_pull_requests` |
| 仓库通知标记已读 | `mark_repository_notifications_read` |

### 🏢 组织操作

| 场景 | 工具 |
|------|------|
| 获取组织信息 | `get_organization` |
| 更新组织信息 | `update_organization` |
| 列出组织成员 | `get_organization_members` |
| 邀请组织成员 | `invite_organization_member` |
| 移除组织成员 | `remove_organization_member` |
| 退出组织 | `leave_organization` |
| 获取组织仓库 | `get_organization_repositories` |
| 获取组织自定义角色 | `get_organization_customized_roles` |

### 🏭 企业操作

| 场景 | 工具 |
|------|------|
| 获取企业成员 | `get_enterprise_members_v8` |
| 邀请企业成员 | `invite_enterprise_member_v8` |
| 更新企业成员权限 | `update_enterprise_member_v8` |
| 删除企业成员 | `delete_enterprise_members_v8` |
| 创建企业里程碑 | `create_enterprise_milestone_v8` |
| 获取企业里程碑 | `get_enterprise_milestones_v8` |
| 获取企业 Issues | `get_enterprise_issues_v8` |

### 📦 Release 操作

| 场景 | 工具 |
|------|------|
| 列出所有 Releases | `get_repository_releases` |
| 获取最新 Release | `get_latest_release` |
| 按 Tag 获取 Release | `get_release_by_tag` |
| 创建 Release | `create_repository_release` |
| 更新 Release | `update_repository_release` |
| 下载 Release 附件 | `download_release_asset` |

### 🏷️ Tag 操作

| 场景 | 工具 |
|------|------|
| 列出所有 Tags | `get_repository_tags` |
| 创建 Tag | `create_repository_tag` |
| 删除 Tag | `delete_repository_tag` |
| 创建保护 Tag | `create_repository_protected_tag` |
| 获取保护 Tags | `get_repository_protected_tags` |

### 🏷 标签（Labels）操作

| 场景 | 工具 |
|------|------|
| 获取仓库标签 | `get_repository_labels` |
| 创建标签 | `create_repository_label` |
| 更新标签 | `update_repository_label` |
| 删除标签 | `delete_repository_label` |
| Issue 替换全部标签 | `replace_repository_issue_all_labels` |

### 🎯 里程碑操作

| 场景 | 工具 |
|------|------|
| 列出里程碑 | `get_repository_milestones` |
| 创建里程碑 | `create_repository_milestone` |
| 更新里程碑 | `update_repository_milestone` |
| 删除里程碑 | `delete_repository_milestone` |

### 📋 看板（Dashboard/Kanban）操作

| 场景 | 工具 |
|------|------|
| 列出组织看板 | `get_organization_kanbans` |
| 获取看板详情 | `get_organization_kanban` |
| 获取看板内容 | `get_organization_kanban_content` |
| 添加 Item 到看板 | `add_org_kanban_item` |
| 移除看板 Item | `delete_org_kanban_remove_item` |
| 更新看板状态 | `update_org_kanban_state` |

### 🔔 Webhook 操作

| 场景 | 工具 |
|------|------|
| 列出 Webhooks | `get_repository_webhooks` |
| 创建 Webhook | `create_repository_webhook` |
| 更新 Webhook | `update_repository_webhook` |
| 删除 Webhook | `delete_repository_webhook` |
| 测试 Webhook | `test_repository_webhook` |

### 🔍 搜索

| 场景 | 工具 |
|------|------|
| 搜索仓库 | `search_repositories` |
| 搜索 Issues | `search_issues` |
| 搜索用户 | `search_users` |

### 🤖 AIHub（AI 能力）

| 场景 | 工具 |
|------|------|
| AI 文本对话/生成 | `chat_completion` |
| AI 语音识别（ASR）| `audio_transcription` |
| AI 音频分类 | `audio_classification` |
| AI 物体检测（YOLO）| `object_detection` |
| 句子相似度计算 | `sentence_similarity` |
| 图像生成视频（创建任务）| `video_generation_create` |
| 图像生成视频（查询状态）| `video_generation_status` |

---

## 工作流程

### 基本工作流

1. **识别需求** — 理解用户想要做什么
2. **选择工具** — 参考上方快速参考表
3. **确定参数** — 通常需要 `owner`（仓库所有者用户名）和 `repo`（仓库名称）
4. **调用工具** — 执行操作
5. **处理结果** — 向用户清晰展示结果

### 常见参数说明

| 参数 | 说明 | 示例 |
|------|------|------|
| `owner` | 仓库所有者（用户名或组织名）| `zkxw2008` |
| `repo` | 仓库名称 | `AtomGit-MCP-Server` |
| `branch` | 分支名称 | `main` |
| `sha` | Commit SHA 值 | `abc1234...` |
| `issue_number` | Issue 编号 | `42` |
| `pull_number` | PR 编号 | `15` |
| `tag` | Tag 名称 | `v1.0.0` |

### 参数获取流程

**如果不知道 owner/repo：**
1. 先用 `get_current_user` 获取当前用户名
2. 再用 `get_current_user_repos` 列出仓库
3. 或用 `search_repositories` 搜索

**如果需要文件 SHA（更新/删除文件时必须）：**
1. 先用 `get_repository_content` 获取文件信息，其中包含 `sha` 字段

---

## 典型工作流示例

### 示例 1：代码审查工作流

```
目标：查看某仓库最新 PR 并添加审查意见

1. get_repository_pulls(owner, repo, state="open")
   → 获取所有开放的 PR 列表

2. get_repository_pull(owner, repo, pull_number)
   → 获取具体 PR 详情

3. get_repository_pull_files(owner, repo, pull_number)
   → 查看改动文件

4. create_repository_pull_comment(owner, repo, pull_number, body)
   → 添加评论

5. process_repository_pull_review(owner, repo, pull_number, action="approved")
   → 审核通过
```

### 示例 2：Issue 管理工作流

```
目标：创建 Issue 并分配标签和里程碑

1. get_repository_labels(owner, repo)
   → 查看可用标签

2. get_repository_milestones(owner, repo)
   → 查看里程碑

3. create_repository_issue(owner, repo, title, body, labels, milestone)
   → 创建 Issue
```

### 示例 3：Release 发布工作流

```
目标：基于某个 Tag 创建新 Release

1. get_repository_tags(owner, repo)
   → 确认 Tag 存在

2. create_repository_release(owner, repo, tag_name, name, body)
   → 创建 Release
```

---

## 注意事项

### 危险操作保护

以下操作默认被禁用（需要设置 `ATOMGIT_ENABLE_DANGEROUS_TOOLS=true`）：
- `delete_*` 系列（删除仓库、文件、分支等）
- `remove_*` 系列（移除成员等）
- `leave_*` 系列（退出组织等）
- `archive_*`（归档仓库）
- `transfer_*`（转移仓库）

**在执行删除等危险操作前，请务必向用户确认。**

### 权限说明

不同操作需要 Access Token 具备不同权限：
- 读取公开仓库：基础权限
- 写入/创建内容：需要仓库写权限
- 管理组织成员：需要组织管理权限
- 企业相关操作：需要企业管理权限

### 分页处理

列表类接口通常支持分页参数：
- `page`：页码（从 1 开始）
- `per_page`：每页数量（默认 20，最大 100）

---

## 详细工具参考

本 Skill 核心工具参数已内置。如需查看完整的 API 映射文档，请参考：
- AtomGit API 文档：https://docs.atomgit.com/docs/apis/
- API 工具映射表：https://atomgit.com/zkxw2008/AtomGit-MCP-Server/blob/main/docs/api_tool_map.md

### 分模块工具总览

| 模块 | 工具数 | 主要功能 |
|------|--------|---------|
| Repositories | 36 | 仓库 CRUD、文件管理、设置管理 |
| PullRequest | 45 | PR 全生命周期管理 |
| Issues | 29 | Issue 管理、评论、标签 |
| User | 22 | 用户信息、SSH Keys、通知 |
| Organization | 18 | 组织管理、成员管理 |
| Enterprise | 15 | 企业成员、里程碑管理 |
| Branch | 8 | 分支管理、保护规则 |
| Commit | 12 | 提交历史、对比、评论 |
| Dashboard | 8 | 看板管理 |
| Release | 8 | 版本发布管理 |
| Tag | 8 | 标签管理、保护标签 |
| Labels | 8 | 标签系统 |
| Member | 6 | 仓库成员权限 |
| Milestone | 5 | 里程碑管理 |
| Search | 3 | 全局搜索 |
| Webhooks | 6 | 事件推送配置 |
| AIHub | 7 | AI 能力接口 |
