# 里程碑操作

## 列出与获取里程碑

```bash
# 列出所有里程碑（含进度）
curl -fsS -H "Authorization: Bearer $ATOMGIT_TOKEN" \
  "$ATOMGIT_API/repos/$OWNER/$REPO/milestones?state=open" | \
  jq '.[] | {number, title, open_issues, closed_issues, due_on,
             progress: ((.closed_issues / ((.open_issues + .closed_issues) | if . == 0 then 1 else . end)) * 100 | round | tostring + "%")}'
```

## 创建里程碑

```bash
curl -fsS -X POST -H "Authorization: Bearer $ATOMGIT_TOKEN" \
  -H "Content-Type: application/json" \
  -d "$(jq -nc \
    --arg title "v1.3.0" \
    --arg description "本次迭代目标：完成用户中心重构" \
    --arg due_on "2024-03-31T00:00:00Z" \
    '{title: $title, description: $description, due_on: $due_on}')" \
  "$ATOMGIT_API/repos/$OWNER/$REPO/milestones" | \
  jq '{number, title, html_url}'
```

## 更新与关闭里程碑

```bash
# 关闭里程碑
curl -fsS -X PATCH -H "Authorization: Bearer $ATOMGIT_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"state":"closed"}' \
  "$ATOMGIT_API/repos/$OWNER/$REPO/milestones/$MILESTONE_NUMBER"

# 修改截止日期
curl -fsS -X PATCH -H "Authorization: Bearer $ATOMGIT_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"due_on":"2024-04-15T00:00:00Z"}' \
  "$ATOMGIT_API/repos/$OWNER/$REPO/milestones/$MILESTONE_NUMBER"
```

## 里程碑进度报告

```bash
# 获取里程碑下所有 Issue
curl -fsS -H "Authorization: Bearer $ATOMGIT_TOKEN" \
  "$ATOMGIT_API/repos/$OWNER/$REPO/issues?milestone=$MILESTONE_NUMBER&state=all&per_page=100" | \
  jq 'group_by(.state) | map({state: .[0].state, count: length})'

# 找出超时未更新的 Issue（超过 7 天无更新的 open issue）
curl -fsS -H "Authorization: Bearer $ATOMGIT_TOKEN" \
  "$ATOMGIT_API/repos/$OWNER/$REPO/issues?milestone=$MILESTONE_NUMBER&state=open" | \
  jq --argjson now $(date +%s) \
  '[.[] | select((.updated_at | fromdateiso8601) < ($now - 604800)) | {number, title, updated_at}]'
```
