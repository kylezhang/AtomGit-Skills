---
name: atomgit
description: AtomGit 全功能操作技能。当用户需要操作 AtomGit 上的任何内容时使用，包括：浏览/搜索仓库和代码文件、管理 Issue（创建/查询/评论/关闭）、管理 Pull Request（创建/Review/合并）、发版（Tag/Release/Changelog）、管理分支、管理组织成员和权限、里程碑追踪、标签治理、Webhook 配置、企业成员管理、项目看板、以及调用 AtomGit AI Hub 进行智能代码审查和文档生成。关键词：atomgit、仓库、repo、issue、PR、pull request、release、tag、分支、branch、组织、org、成员、webhook、看板、kanban、AI review、文档生成。
compatibility:
  bins: curl, jq, git
  env: ATOMGIT_TOKEN
---

# AtomGit Skill

用 `curl + jq` 调用 AtomGit OpenAPI，用 `git` 做本地仓库操作。

## Setup

```bash
export ATOMGIT_API="https://api.atomgit.com/api/v5"
export ATOMGIT_TOKEN="your-personal-access-token"  # https://atomgit.com/setting/token-classic
# 企业 API（v8，仅企业管理员需要）
export ATOMGIT_API_V8="https://api.atomgit.com/api/v8"
```

**获取 Token**：AtomGit → 设置 → 私人令牌 → 按需勾选权限（repos/issues/pull_requests）

⚠️ 永远不要把 token 粘贴进对话，保持在环境变量中。

## API 约定

- 认证：所有请求带 `-H "Authorization: Bearer $ATOMGIT_TOKEN"`
- JSON 请求体：始终用 `jq -nc` 构建，避免特殊字符转义问题
- 失败即报错：使用 `curl -fsS`，HTTP 错误会直接暴露
- 分页：默认 `page=1&per_page=20`，大量数据时递增 page

```bash
# 标准 GET 模板
curl -fsS -H "Authorization: Bearer $ATOMGIT_TOKEN" \
  "$ATOMGIT_API/repos/$OWNER/$REPO/..."

# 标准 POST 模板
curl -fsS -X POST -H "Authorization: Bearer $ATOMGIT_TOKEN" \
  -H "Content-Type: application/json" \
  -d "$(jq -nc --arg foo "bar" '{foo: $foo}')" \
  "$ATOMGIT_API/repos/$OWNER/$REPO/..."
```

---

## 能力索引

根据用户任务，选择对应模块 — 详细命令示例见 `references/` 目录。

| 用户说… | 参考文件 |
|--------|---------|
| 搜索/浏览仓库、读取文件、查看目录结构 | `references/repo.md` |
| 创建/查询/评论/关闭 Issue | `references/issue.md` |
| 创建/Review/合并 Pull Request | `references/pr.md` |
| 发版、创建 Tag、生成 Release Notes | `references/release.md` |
| 管理分支、设置保护规则 | `references/branch.md` |
| 管理组织、成员、仓库权限 | `references/org.md` |
| 里程碑追踪、迭代进度报告 | `references/milestone.md` |
| 标签体系管理、跨仓库标签同步 | `references/label.md` |
| 配置/测试 Webhook | `references/webhook.md` |
| 企业成员管理、权限审计（v8 API）| `references/enterprise.md` |
| 项目看板管理 | `references/kanban.md` |
| AI 智能代码审查（AI Hub）| `references/ai-review.md` |
| AI 自动生成文档/README/Release Notes | `references/ai-doc.md` |

**使用方式**：在同一次对话中，用 `view` 工具读取对应的 references 文件，然后按其中的命令范式完成任务。如果一个任务跨多个模块（如"发版并生成 AI Release Notes"），同时读取 `release.md` 和 `ai-doc.md`。

---

## 常用快捷命令

无需查阅子文件，这些命令最高频：

### 读取文件内容

```bash
# 获取文件（内容为 base64 编码）
curl -fsS -H "Authorization: Bearer $ATOMGIT_TOKEN" \
  "$ATOMGIT_API/repos/$OWNER/$REPO/contents/README.md" | \
  jq -r '.content' | base64 -d
```

### 搜索仓库

```bash
curl -fsS -H "Authorization: Bearer $ATOMGIT_TOKEN" \
  "$ATOMGIT_API/search/repositories?q=关键词&language=go" | \
  jq '.[] | {full_name, description, stargazers_count}'
```

### 列出 Issue

```bash
curl -fsS -H "Authorization: Bearer $ATOMGIT_TOKEN" \
  "$ATOMGIT_API/repos/$OWNER/$REPO/issues?state=open" | \
  jq '.[] | "#\(.number) [\(.state)] \(.title)"'
```

### 列出 PR

```bash
curl -fsS -H "Authorization: Bearer $ATOMGIT_TOKEN" \
  "$ATOMGIT_API/repos/$OWNER/$REPO/pulls?state=open" | \
  jq '.[] | "#\(.number) \(.title) ← \(.head.label)"'
```

### 克隆仓库

```bash
git clone "https://atomgit.com/$OWNER/$REPO.git"
# 或 SSH
git clone "git@atomgit.com:$OWNER/$REPO.git"
```

---

## jq 常用过滤器

```bash
# 提取列表中的关键字段
jq '.[] | {number, title, state}'

# 只看 open 状态
jq '[.[] | select(.state == "open")]'

# 格式化输出带序号
jq -r '.[] | "#\(.number) \(.title) @\(.user.login)"'

# 提取第一个元素的某字段
jq -r '.[0].html_url'
```

---

## 参考文档

- AtomGit OpenAPI 文档：https://docs.atomgit.com/docs/apis/
- AtomGit AI Hub API：https://docs.atomgit.com/docs/apis/post-api-v-5-chat-completions
- 如果某个操作本文件未涵盖，查阅上方文档并沿用相同的 `curl + jq` 模式即可
