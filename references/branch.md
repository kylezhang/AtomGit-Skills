# 分支操作

## 列出与获取分支

```bash
# 列出所有分支
curl -fsS -H "Authorization: Bearer $ATOMGIT_TOKEN" \
  "$ATOMGIT_API/repos/$OWNER/$REPO/branches" | \
  jq '.[] | {name, protected, commit: .commit.sha[:8]}'

# 获取单个分支
curl -fsS -H "Authorization: Bearer $ATOMGIT_TOKEN" \
  "$ATOMGIT_API/repos/$OWNER/$REPO/branches/main" | \
  jq '{name, protected, commit: .commit.sha}'
```

## 创建与删除分支

```bash
# 创建分支（从 main 分叉）
curl -fsS -X POST -H "Authorization: Bearer $ATOMGIT_TOKEN" \
  -H "Content-Type: application/json" \
  -d "$(jq -nc \
    --arg branch_name "feature/new-feature" \
    --arg refs "main" \
    '{branch_name: $branch_name, refs: $refs}')" \
  "$ATOMGIT_API/repos/$OWNER/$REPO/branches"

# 删除分支
curl -fsS -X DELETE -H "Authorization: Bearer $ATOMGIT_TOKEN" \
  "$ATOMGIT_API/repos/$OWNER/$REPO/branches/feature/old-feature"
```

## 分支保护规则

```bash
# 设置分支保护（禁止强推 + 要求 PR 合并）
curl -fsS -X PUT -H "Authorization: Bearer $ATOMGIT_TOKEN" \
  -H "Content-Type: application/json" \
  -d "$(jq -nc \
    --argjson required_pull_request_reviews 1 \
    --argjson enforce_admins true \
    '{required_pull_request_reviews: $required_pull_request_reviews, enforce_admins: $enforce_admins}')" \
  "$ATOMGIT_API/repos/$OWNER/$REPO/branches/main/protection"

# 取消分支保护
curl -fsS -X DELETE -H "Authorization: Bearer $ATOMGIT_TOKEN" \
  "$ATOMGIT_API/repos/$OWNER/$REPO/branches/main/protection"
```

## Commit 历史

```bash
# 查看最近 10 条 Commit
curl -fsS -H "Authorization: Bearer $ATOMGIT_TOKEN" \
  "$ATOMGIT_API/repos/$OWNER/$REPO/commits?per_page=10" | \
  jq '.[] | "\(.sha[:8]) \(.commit.message | split("\n")[0]) - \(.commit.author.name)"'

# 查看某个 Commit 的文件变更
curl -fsS -H "Authorization: Bearer $ATOMGIT_TOKEN" \
  "$ATOMGIT_API/repos/$OWNER/$REPO/commits/$SHA" | \
  jq '.files[] | "\(.status) \(.filename)  +\(.additions) -\(.deletions)"'
```
