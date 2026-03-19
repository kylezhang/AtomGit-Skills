# Issue 操作

## 列出 Issue

```bash
# 列出 open 的 Issue
curl -fsS -H "Authorization: Bearer $ATOMGIT_TOKEN" \
  "$ATOMGIT_API/repos/$OWNER/$REPO/issues?state=open&per_page=20" | \
  jq '.[] | "#\(.number) [\(.state)] \(.title) @\(.user.login)"'

# 按标签筛选
curl -fsS -H "Authorization: Bearer $ATOMGIT_TOKEN" \
  "$ATOMGIT_API/repos/$OWNER/$REPO/issues?labels=bug&state=open" | \
  jq '.[] | {number, title, labels: [.labels[].name]}'

# 按里程碑筛选（milestone 传编号）
curl -fsS -H "Authorization: Bearer $ATOMGIT_TOKEN" \
  "$ATOMGIT_API/repos/$OWNER/$REPO/issues?milestone=1&state=open" | \
  jq '.[] | {number, title, state}'
```

## 创建 Issue

```bash
curl -fsS -X POST -H "Authorization: Bearer $ATOMGIT_TOKEN" \
  -H "Content-Type: application/json" \
  -d "$(jq -nc \
    --arg title "Bug: 登录页面无法加载" \
    --arg body "## 描述\n...\n## 复现步骤\n1. \n2. " \
    '{title: $title, body: $body}')" \
  "$ATOMGIT_API/repos/$OWNER/issues?repo=$REPO" | \
  jq '{number, title, html_url}'
```

> 注意：AtomGit Issue 创建 API 路径为 `/repos/:owner/issues`，repo 名通过 query 参数传入。

## 更新 Issue（关闭/重新打开/修改标题）

```bash
# 关闭 Issue
curl -fsS -X PATCH -H "Authorization: Bearer $ATOMGIT_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"state":"closed"}' \
  "$ATOMGIT_API/repos/$OWNER/$REPO/issues/$ISSUE_NUMBER"

# 重新打开
curl -fsS -X PATCH -H "Authorization: Bearer $ATOMGIT_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"state":"open"}' \
  "$ATOMGIT_API/repos/$OWNER/$REPO/issues/$ISSUE_NUMBER"

# 指派负责人 + 关联里程碑
curl -fsS -X PATCH -H "Authorization: Bearer $ATOMGIT_TOKEN" \
  -H "Content-Type: application/json" \
  -d "$(jq -nc \
    --arg assignee "username" \
    --argjson milestone 1 \
    '{assignee: $assignee, milestone: $milestone}')" \
  "$ATOMGIT_API/repos/$OWNER/$REPO/issues/$ISSUE_NUMBER"
```

## 评论

```bash
# 添加评论
curl -fsS -X POST -H "Authorization: Bearer $ATOMGIT_TOKEN" \
  -H "Content-Type: application/json" \
  -d "$(jq -nc --arg body "评论内容" '{body: $body}')" \
  "$ATOMGIT_API/repos/$OWNER/$REPO/issues/$ISSUE_NUMBER/comments" | \
  jq '{id, html_url}'

# 列出评论
curl -fsS -H "Authorization: Bearer $ATOMGIT_TOKEN" \
  "$ATOMGIT_API/repos/$OWNER/$REPO/issues/$ISSUE_NUMBER/comments" | \
  jq '.[] | {id, body: .body[:80], user: .user.login}'
```

## 标签操作

```bash
# 给 Issue 打标签
curl -fsS -X POST -H "Authorization: Bearer $ATOMGIT_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"labels":["bug","priority:high"]}' \
  "$ATOMGIT_API/repos/$OWNER/$REPO/issues/$ISSUE_NUMBER/labels"

# 删除某个标签
curl -fsS -X DELETE -H "Authorization: Bearer $ATOMGIT_TOKEN" \
  "$ATOMGIT_API/repos/$OWNER/$REPO/issues/$ISSUE_NUMBER/labels/bug"
```
