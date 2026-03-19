# Pull Request 操作

## 列出 PR

```bash
# 列出所有 open PR
curl -fsS -H "Authorization: Bearer $ATOMGIT_TOKEN" \
  "$ATOMGIT_API/repos/$OWNER/$REPO/pulls?state=open" | \
  jq '.[] | "#\(.number) \(.title) | \(.head.label) → \(.base.label) @\(.user.login)"'
```

## 创建 PR

```bash
curl -fsS -X POST -H "Authorization: Bearer $ATOMGIT_TOKEN" \
  -H "Content-Type: application/json" \
  -d "$(jq -nc \
    --arg title "feat: 新功能描述" \
    --arg head "feature-branch" \
    --arg base "main" \
    --arg body "## 变更描述\n...\n\nclosess #123" \
    '{title: $title, head: $head, base: $base, body: $body}')" \
  "$ATOMGIT_API/repos/$OWNER/$REPO/pulls" | \
  jq '{number, title, html_url}'
```

## 查看 PR 文件变更

```bash
# 变更文件列表（含增删行数）
curl -fsS -H "Authorization: Bearer $ATOMGIT_TOKEN" \
  "$ATOMGIT_API/repos/$OWNER/$REPO/pulls/$PR_NUMBER/files" | \
  jq '.[] | "\(.status) \(.filename)  +\(.additions) -\(.deletions)"'

# 查看 PR 的所有 Commit
curl -fsS -H "Authorization: Bearer $ATOMGIT_TOKEN" \
  "$ATOMGIT_API/repos/$OWNER/$REPO/pulls/$PR_NUMBER/commits" | \
  jq '.[] | {sha: .sha[:8], message: .commit.message, author: .commit.author.name}'
```

## 评论 PR

```bash
# 添加 PR 评论
curl -fsS -X POST -H "Authorization: Bearer $ATOMGIT_TOKEN" \
  -H "Content-Type: application/json" \
  -d "$(jq -nc --arg body "LGTM! 代码逻辑清晰。" '{body: $body}')" \
  "$ATOMGIT_API/repos/$OWNER/$REPO/pulls/$PR_NUMBER/comments" | \
  jq '{id, html_url}'
```

## 合并 PR

```bash
# merge 方式合并（保留所有 commits）
curl -fsS -X PUT -H "Authorization: Bearer $ATOMGIT_TOKEN" \
  -H "Content-Type: application/json" \
  -d "$(jq -nc \
    --arg merge_method "merge" \
    --arg commit_title "feat: 合并新功能" \
    '{merge_method: $merge_method, commit_title: $commit_title}')" \
  "$ATOMGIT_API/repos/$OWNER/$REPO/pulls/$PR_NUMBER/merge"

# squash 合并（所有 commits 压缩为一个）
# 将 merge_method 改为 "squash"

# rebase 合并
# 将 merge_method 改为 "rebase"
```

## 更新/关闭 PR

```bash
# 关闭 PR（不合并）
curl -fsS -X PATCH -H "Authorization: Bearer $ATOMGIT_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"state":"closed"}' \
  "$ATOMGIT_API/repos/$OWNER/$REPO/pulls/$PR_NUMBER"
```
