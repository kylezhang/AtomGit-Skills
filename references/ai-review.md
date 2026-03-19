# AI 智能代码审查（AI Hub）

利用 AtomGit AI Hub 对 PR 进行自动代码审查。

## AI Hub API

```bash
curl -fsS -X POST -H "Authorization: Bearer $ATOMGIT_TOKEN" \
  -H "Content-Type: application/json" \
  -d "$(jq -nc \
    --arg model "MODEL_NAME" \
    --arg system "你是一个专业的代码审查员" \
    --arg content "请审查以下代码：..." \
    '{
       model: $model,
       messages: [
         {role: "system", content: $system},
         {role: "user", content: $content}
       ]
     }')" \
  "$ATOMGIT_API/chat/completions" | \
  jq -r '.choices[0].message.content'
```

## 完整 Code Review 工作流

```bash
# 步骤 1：获取 PR 变更文件列表
FILES=$(curl -fsS -H "Authorization: Bearer $ATOMGIT_TOKEN" \
  "$ATOMGIT_API/repos/$OWNER/$REPO/pulls/$PR_NUMBER/files")

# 步骤 2：提取 diff 内容（每个文件）
echo "$FILES" | jq -r '.[] | "文件: \(.filename)\n状态: \(.status)\n+\(.additions) -\(.deletions)\n"'

# 步骤 3：对每个文件的 diff 调用 AI Hub 审查
DIFF_CONTENT=$(echo "$FILES" | jq -r '.[0].patch // "无 diff"')

REVIEW=$(curl -fsS -X POST -H "Authorization: Bearer $ATOMGIT_TOKEN" \
  -H "Content-Type: application/json" \
  -d "$(jq -nc \
    --arg content "请对以下代码变更进行审查，关注：正确性、安全性、性能、可读性。\n\n$DIFF_CONTENT" \
    '{
       model: "MODEL_NAME",
       messages: [{role: "user", content: $content}]
     }')" \
  "$ATOMGIT_API/chat/completions" | \
  jq -r '.choices[0].message.content')

# 步骤 4：将 AI Review 结果发布为 PR 评论
curl -fsS -X POST -H "Authorization: Bearer $ATOMGIT_TOKEN" \
  -H "Content-Type: application/json" \
  -d "$(jq -nc --arg body "🤖 **AI Code Review**\n\n$REVIEW" '{body: $body}')" \
  "$ATOMGIT_API/repos/$OWNER/$REPO/pulls/$PR_NUMBER/comments"
```

## Review Prompt 模板

```
你是一个专业代码审查员。请对以下代码变更进行审查：

文件：{filename}
语言：{language}

Diff：
{diff_content}

请按以下格式输出：
🔴 严重问题（必须修改）：...
🟡 建议改进（可以优化）：...
🟢 良好实践（值得肯定）：...

如无问题，简要说明代码质量良好的原因。
```

## 注意事项
- diff 内容建议每次不超过 200 行，避免超出 token 限制
- 大型 PR 建议按文件分批调用
- AI 审查结果仅供参考，合并决策由人工确认
