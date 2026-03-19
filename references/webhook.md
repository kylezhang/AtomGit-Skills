# Webhook 操作

## 列出与查看 Webhook

```bash
# 列出仓库所有 Webhook
curl -fsS -H "Authorization: Bearer $ATOMGIT_TOKEN" \
  "$ATOMGIT_API/repos/$OWNER/$REPO/hooks" | \
  jq '.[] | {id, url: .config.url, events, active}'
```

## 创建 Webhook

```bash
curl -fsS -X POST -H "Authorization: Bearer $ATOMGIT_TOKEN" \
  -H "Content-Type: application/json" \
  -d "$(jq -nc \
    --arg url "https://your-server.com/webhook" \
    --arg secret "your-secret-token" \
    '{
       config: {url: $url, content_type: "json", secret: $secret},
       events: ["push","pull_request","issues"],
       active: true
     }')" \
  "$ATOMGIT_API/repos/$OWNER/$REPO/hooks" | \
  jq '{id, url: .config.url, events}'
```

## 测试 Webhook

```bash
# 发送 ping 测试
curl -fsS -X POST -H "Authorization: Bearer $ATOMGIT_TOKEN" \
  "$ATOMGIT_API/repos/$OWNER/$REPO/hooks/$HOOK_ID/tests"
echo "Ping 已发送，请查看目标服务器是否收到请求"
```

## 更新与删除 Webhook

```bash
# 禁用 Webhook
curl -fsS -X PATCH -H "Authorization: Bearer $ATOMGIT_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"active":false}' \
  "$ATOMGIT_API/repos/$OWNER/$REPO/hooks/$HOOK_ID"

# 删除 Webhook
curl -fsS -X DELETE -H "Authorization: Bearer $ATOMGIT_TOKEN" \
  "$ATOMGIT_API/repos/$OWNER/$REPO/hooks/$HOOK_ID"
```

## 常见 Webhook 事件

| 事件 | 触发时机 |
|------|---------|
| `push` | 代码推送 |
| `pull_request` | PR 创建/更新/合并 |
| `issues` | Issue 创建/更新/关闭 |
| `issue_comment` | Issue 评论 |
| `release` | Release 发布 |
| `create` | 分支/Tag 创建 |
| `delete` | 分支/Tag 删除 |
