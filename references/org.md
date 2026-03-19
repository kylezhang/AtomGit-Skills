# 组织与成员操作

## 组织仓库

```bash
# 获取组织下的仓库列表
curl -fsS -H "Authorization: Bearer $ATOMGIT_TOKEN" \
  "$ATOMGIT_API/orgs/$ORG/repos?per_page=50" | \
  jq '.[] | {name, private, description, updated_at}'

# 在组织下创建仓库
curl -fsS -X POST -H "Authorization: Bearer $ATOMGIT_TOKEN" \
  -H "Content-Type: application/json" \
  -d "$(jq -nc \
    --arg name "new-repo" \
    --arg description "仓库描述" \
    --argjson private false \
    --argjson auto_init true \
    '{name: $name, description: $description, private: $private, auto_init: $auto_init}')" \
  "$ATOMGIT_API/orgs/$ORG/repos" | \
  jq '{full_name, html_url}'
```

## 组织成员管理

```bash
# 获取组织成员列表
curl -fsS -H "Authorization: Bearer $ATOMGIT_TOKEN" \
  "$ATOMGIT_API/orgs/$ORG/members" | \
  jq '.[] | {login, role}'

# 检查某用户是否是成员
curl -fsS -H "Authorization: Bearer $ATOMGIT_TOKEN" \
  "$ATOMGIT_API/orgs/$ORG/members/$USERNAME" && echo "是成员" || echo "非成员"

# 移除成员
curl -fsS -X DELETE -H "Authorization: Bearer $ATOMGIT_TOKEN" \
  "$ATOMGIT_API/orgs/$ORG/members/$USERNAME"
```

## 仓库协作者（Collaborator）

```bash
# 列出仓库协作者及权限
curl -fsS -H "Authorization: Bearer $ATOMGIT_TOKEN" \
  "$ATOMGIT_API/repos/$OWNER/$REPO/collaborators" | \
  jq '.[] | {login, permissions}'

# 添加协作者（permission: pull/push/admin）
curl -fsS -X PUT -H "Authorization: Bearer $ATOMGIT_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"permission":"push"}' \
  "$ATOMGIT_API/repos/$OWNER/$REPO/collaborators/$USERNAME"

# 移除协作者
curl -fsS -X DELETE -H "Authorization: Bearer $ATOMGIT_TOKEN" \
  "$ATOMGIT_API/repos/$OWNER/$REPO/collaborators/$USERNAME"

# 查看协作者权限
curl -fsS -H "Authorization: Bearer $ATOMGIT_TOKEN" \
  "$ATOMGIT_API/repos/$OWNER/$REPO/collaborators/$USERNAME/permission" | \
  jq '{permission}'
```
