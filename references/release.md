# Release 与 Tag 操作

## Tag 操作

```bash
# 列出 Tag
curl -fsS -H "Authorization: Bearer $ATOMGIT_TOKEN" \
  "$ATOMGIT_API/repos/$OWNER/$REPO/tags" | \
  jq '.[] | {name, commit: .commit.sha[:8]}'

# 创建 Tag（轻量 Tag，指向某个 SHA）
curl -fsS -X POST -H "Authorization: Bearer $ATOMGIT_TOKEN" \
  -H "Content-Type: application/json" \
  -d "$(jq -nc \
    --arg tag_name "v1.2.0" \
    --arg ref "main" \
    '{tag_name: $tag_name, ref: $ref}')" \
  "$ATOMGIT_API/repos/$OWNER/$REPO/tags"
```

## Release 操作

```bash
# 获取最新 Release
curl -fsS -H "Authorization: Bearer $ATOMGIT_TOKEN" \
  "$ATOMGIT_API/repos/$OWNER/$REPO/releases/latest" | \
  jq '{tag_name, name, created_at, html_url}'

# 列出所有 Release
curl -fsS -H "Authorization: Bearer $ATOMGIT_TOKEN" \
  "$ATOMGIT_API/repos/$OWNER/$REPO/releases" | \
  jq '.[] | {id, tag_name, name, prerelease, created_at}'

# 创建 Release
curl -fsS -X POST -H "Authorization: Bearer $ATOMGIT_TOKEN" \
  -H "Content-Type: application/json" \
  -d "$(jq -nc \
    --arg tag_name "v1.2.0" \
    --arg name "v1.2.0 - 功能增强版" \
    --arg body "## 新功能\n- ...\n\n## Bug 修复\n- ..." \
    --argjson prerelease false \
    '{tag_name: $tag_name, name: $name, body: $body, prerelease: $prerelease}')" \
  "$ATOMGIT_API/repos/$OWNER/$REPO/releases" | \
  jq '{id, tag_name, html_url}'
```

## 获取两个版本间的 Commit（用于自动生成 ChangeLog）

```bash
# 对比 v1.1.0 到 v1.2.0 之间的变更
curl -fsS -H "Authorization: Bearer $ATOMGIT_TOKEN" \
  "$ATOMGIT_API/repos/$OWNER/$REPO/compare/v1.1.0...v1.2.0" | \
  jq '.commits[] | {sha: .sha[:8], message: .commit.message, author: .commit.author.name}'

# 只提取 commit message（送给 AI 生成 Release Notes）
curl -fsS -H "Authorization: Bearer $ATOMGIT_TOKEN" \
  "$ATOMGIT_API/repos/$OWNER/$REPO/compare/v1.1.0...v1.2.0" | \
  jq -r '.commits[] | "- \(.commit.message)"'
```

## SemVer 速查

- `v2.0.0`：有 breaking change（不向后兼容）
- `v1.3.0`：新功能，向后兼容
- `v1.2.1`：Bug 修复
- `v1.2.0-rc.1`：发布候选（预发布）
