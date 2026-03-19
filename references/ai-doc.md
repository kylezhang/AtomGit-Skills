# AI 文档生成（AI Hub）

利用 AtomGit AI Hub 自动生成项目文档。

## 生成 README

```bash
# 步骤 1：收集项目上下文
LANGUAGES=$(curl -fsS -H "Authorization: Bearer $ATOMGIT_TOKEN" \
  "$ATOMGIT_API/repos/$OWNER/$REPO/languages" | jq -r 'to_entries | .[] | "\(.key): \(.value)"')

TREE=$(curl -fsS -H "Authorization: Bearer $ATOMGIT_TOKEN" \
  "$ATOMGIT_API/repos/$OWNER/$REPO/git/trees/HEAD?recursive=0" | \
  jq -r '.tree[] | "\(.type) \(.path)"')

CONTRIBUTORS=$(curl -fsS -H "Authorization: Bearer $ATOMGIT_TOKEN" \
  "$ATOMGIT_API/repos/$OWNER/$REPO/contributors" | \
  jq -r '.[] | "@\(.login) (\(.contributions) commits)"')

# 步骤 2：调用 AI Hub 生成 README
README=$(curl -fsS -X POST -H "Authorization: Bearer $ATOMGIT_TOKEN" \
  -H "Content-Type: application/json" \
  -d "$(jq -nc \
    --arg context "仓库：$OWNER/$REPO\n语言：$LANGUAGES\n目录：$TREE\n贡献者：$CONTRIBUTORS" \
    '{
       model: "MODEL_NAME",
       messages: [{
         role: "user",
         content: ("请根据以下信息生成专业的 README.md：\n" + $context + "\n\n包含：项目简介、功能特性、快速开始、贡献指南、License")
       }]
     }')" \
  "$ATOMGIT_API/chat/completions" | \
  jq -r '.choices[0].message.content')

echo "$README"
```

## 生成 Release Notes

```bash
# 步骤 1：获取版本间 Commit
COMMITS=$(curl -fsS -H "Authorization: Bearer $ATOMGIT_TOKEN" \
  "$ATOMGIT_API/repos/$OWNER/$REPO/compare/$PREV_TAG...$NEW_TAG" | \
  jq -r '.commits[] | "- \(.commit.message | split("\n")[0]) (@\(.author.login // .commit.author.name))"')

# 步骤 2：AI 生成 Release Notes
curl -fsS -X POST -H "Authorization: Bearer $ATOMGIT_TOKEN" \
  -H "Content-Type: application/json" \
  -d "$(jq -nc \
    --arg version "$NEW_TAG" \
    --arg commits "$COMMITS" \
    '{
       model: "MODEL_NAME",
       messages: [{
         role: "user",
         content: ("请将以下 Git commits 转化为用户友好的 Release Notes（版本：" + $version + "）。\n分类为：新功能/Bug修复/其他改动。\n\nCommits：\n" + $commits)
       }]
     }')" \
  "$ATOMGIT_API/chat/completions" | \
  jq -r '.choices[0].message.content'
```

## 写入文件回仓库

```bash
# 将生成的内容写入仓库（先检查文件是否存在）
EXISTING=$(curl -fsS -H "Authorization: Bearer $ATOMGIT_TOKEN" \
  "$ATOMGIT_API/repos/$OWNER/$REPO/contents/README.md" 2>/dev/null)

CONTENT=$(echo "$README" | base64)

if echo "$EXISTING" | jq -e '.sha' > /dev/null 2>&1; then
  # 文件存在，更新
  SHA=$(echo "$EXISTING" | jq -r '.sha')
  curl -fsS -X PUT -H "Authorization: Bearer $ATOMGIT_TOKEN" \
    -H "Content-Type: application/json" \
    -d "$(jq -nc \
      --arg message "docs: update README via AI" \
      --arg content "$CONTENT" \
      --arg sha "$SHA" \
      '{message: $message, content: $content, sha: $sha}')" \
    "$ATOMGIT_API/repos/$OWNER/$REPO/contents/README.md"
else
  # 文件不存在，创建
  curl -fsS -X POST -H "Authorization: Bearer $ATOMGIT_TOKEN" \
    -H "Content-Type: application/json" \
    -d "$(jq -nc \
      --arg message "docs: add README via AI" \
      --arg content "$CONTENT" \
      '{message: $message, content: $content}')" \
    "$ATOMGIT_API/repos/$OWNER/$REPO/contents/README.md"
fi
```
