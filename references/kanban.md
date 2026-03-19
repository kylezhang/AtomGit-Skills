# 项目看板操作

> 看板 API 属于 Dashboard 模块，路径为 `/api/v5/org/:owner/kanban/`

## 列出与获取看板

```bash
# 列出组织看板
curl -fsS -H "Authorization: Bearer $ATOMGIT_TOKEN" \
  "$ATOMGIT_API/org/$ORG/kanban/list" | \
  jq '.[] | {id, name, description}'

# 获取看板详情
curl -fsS -H "Authorization: Bearer $ATOMGIT_TOKEN" \
  "$ATOMGIT_API/org/$ORG/kanban/$KANBAN_ID" | jq .

# 获取看板内容（列和卡片）
curl -fsS -H "Authorization: Bearer $ATOMGIT_TOKEN" \
  "$ATOMGIT_API/org/$ORG/kanban/$KANBAN_ID/content" | jq .
```

## 创建与更新看板

```bash
# 创建看板
curl -fsS -X POST -H "Authorization: Bearer $ATOMGIT_TOKEN" \
  -H "Content-Type: application/json" \
  -d "$(jq -nc \
    --arg name "Sprint 12" \
    --arg description "2024年Q1第一个迭代" \
    '{name: $name, description: $description}')" \
  "$ATOMGIT_API/org/$ORG/kanban" | \
  jq '{id, name}'

# 更新看板
curl -fsS -X PATCH -H "Authorization: Bearer $ATOMGIT_TOKEN" \
  -H "Content-Type: application/json" \
  -d "$(jq -nc --arg name "Sprint 12 - 已完成" '{name: $name}')" \
  "$ATOMGIT_API/org/$ORG/kanban/$KANBAN_ID"
```

## 看板任务关联

```bash
# 将 Issue 添加到看板
curl -fsS -X POST -H "Authorization: Bearer $ATOMGIT_TOKEN" \
  -H "Content-Type: application/json" \
  -d "$(jq -nc \
    --arg owner "$OWNER" \
    --arg repo "$REPO" \
    --argjson issue_number 42 \
    '{owner: $owner, repo: $repo, issue_number: $issue_number}')" \
  "$ATOMGIT_API/org/$ORG/kanban/$KANBAN_ID/items"
```
