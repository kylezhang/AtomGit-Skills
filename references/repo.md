# 仓库操作

## 获取目录树

```bash
# 获取根目录（默认分支）
curl -fsS -H "Authorization: Bearer $ATOMGIT_TOKEN" \
  "$ATOMGIT_API/repos/$OWNER/$REPO/git/trees/HEAD?recursive=0" | \
  jq '.tree[] | {path, type}'

# 递归获取完整目录树（大仓库慎用）
curl -fsS -H "Authorization: Bearer $ATOMGIT_TOKEN" \
  "$ATOMGIT_API/repos/$OWNER/$REPO/git/trees/HEAD?recursive=1" | \
  jq '[.tree[] | select(.type=="blob") | .path]'
```

## 读取文件内容

```bash
# 读取文件（base64 解码）
curl -fsS -H "Authorization: Bearer $ATOMGIT_TOKEN" \
  "$ATOMGIT_API/repos/$OWNER/$REPO/contents/$FILE_PATH" | \
  jq -r '.content' | base64 -d

# 获取 raw 内容（直接文本，无需解码）
curl -fsS -H "Authorization: Bearer $ATOMGIT_TOKEN" \
  "$ATOMGIT_API/repos/$OWNER/$REPO/raw/$FILE_PATH"
```

## 创建/更新文件

```bash
# 创建新文件
CONTENT=$(echo "文件内容" | base64)
curl -fsS -X POST -H "Authorization: Bearer $ATOMGIT_TOKEN" \
  -H "Content-Type: application/json" \
  -d "$(jq -nc \
    --arg message "feat: add new file" \
    --arg content "$CONTENT" \
    '{message: $message, content: $content}')" \
  "$ATOMGIT_API/repos/$OWNER/$REPO/contents/$FILE_PATH"

# 更新已有文件（需要当前文件的 SHA）
FILE_SHA=$(curl -fsS -H "Authorization: Bearer $ATOMGIT_TOKEN" \
  "$ATOMGIT_API/repos/$OWNER/$REPO/contents/$FILE_PATH" | jq -r '.sha')
NEW_CONTENT=$(echo "新内容" | base64)
curl -fsS -X PUT -H "Authorization: Bearer $ATOMGIT_TOKEN" \
  -H "Content-Type: application/json" \
  -d "$(jq -nc \
    --arg message "update file" \
    --arg content "$NEW_CONTENT" \
    --arg sha "$FILE_SHA" \
    '{message: $message, content: $content, sha: $sha}')" \
  "$ATOMGIT_API/repos/$OWNER/$REPO/contents/$FILE_PATH"
```

## 仓库信息与统计

```bash
# 获取语言分布
curl -fsS -H "Authorization: Bearer $ATOMGIT_TOKEN" \
  "$ATOMGIT_API/repos/$OWNER/$REPO/languages" | jq .

# 获取贡献者列表
curl -fsS -H "Authorization: Bearer $ATOMGIT_TOKEN" \
  "$ATOMGIT_API/repos/$OWNER/$REPO/contributors" | \
  jq '.[] | {login, contributions}'

# 获取仓库动态
curl -fsS -H "Authorization: Bearer $ATOMGIT_TOKEN" \
  "$ATOMGIT_API/repos/$OWNER/$REPO/events" | \
  jq '.[] | {type, created_at, actor: .actor.login}'

# Fork 仓库
curl -fsS -X POST -H "Authorization: Bearer $ATOMGIT_TOKEN" \
  "$ATOMGIT_API/repos/$OWNER/$REPO/forks" | \
  jq '{full_name, html_url}'
```

## 搜索仓库

```bash
# 按关键词搜索
curl -fsS -H "Authorization: Bearer $ATOMGIT_TOKEN" \
  "$ATOMGIT_API/search/repositories?q=KEYWORD&language=python&page=1&per_page=10" | \
  jq '.[] | {full_name, description, stargazers_count, language}'

# 搜索用户
curl -fsS -H "Authorization: Bearer $ATOMGIT_TOKEN" \
  "$ATOMGIT_API/search/users?q=USERNAME" | \
  jq '.[] | {login, name, html_url}'
```

## 仓库管理

```bash
# 更新仓库描述/可见性
curl -fsS -X PATCH -H "Authorization: Bearer $ATOMGIT_TOKEN" \
  -H "Content-Type: application/json" \
  -d "$(jq -nc --arg desc "新描述" '{description: $desc}')" \
  "$ATOMGIT_API/repos/$OWNER/$REPO"

# 获取推送规则
curl -fsS -H "Authorization: Bearer $ATOMGIT_TOKEN" \
  "$ATOMGIT_API/repos/$OWNER/$REPO/push-config" | jq .
```
