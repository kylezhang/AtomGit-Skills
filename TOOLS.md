# TOOLS.md - 工具依赖

本 Skill 依赖以下工具和环境配置。

## 必需依赖

| 依赖 | 类型 | 说明 | 安装/获取方式 |
|------|------|------|---------------|
| AtomGit MCP Server | MCP 服务 | 提供 244 个 AtomGit 操作工具 | `npm install -g @atomgit.com/atomgit-mcp-server` |
| ATOMGIT_TOKEN | 环境变量 | 个人访问令牌 | [获取 Token](https://atomgit.com/setting/token-classic) |

## 可选依赖

| 依赖 | 说明 | 用途 |
|------|------|------|
| ATOMGIT_API | API 地址 | 默认 `https://api.atomgit.com/api/v5` |
| ATOMGIT_API_V8 | 企业 API | 企业管理需要，默认 `https://api.atomgit.com/api/v8` |
| ATOMGIT_ENABLE_DANGEROUS_TOOLS | 启用危险操作 | 设置为 `true` 启用删除等操作 |

## Token 权限要求

不同操作需要 Token 具备不同权限：

| 操作类型 | 所需权限 | 说明 |
|----------|----------|------|
| 读取公开仓库 | 基础权限 | 无需额外配置 |
| 读取私有仓库 | `repo` (read) | 仓库读取权限 |
| 写入/创建内容 | `repo` (write) | 仓库写权限 |
| 管理组织成员 | `write:org` | 组织管理权限 |
| 企业管理操作 | `admin:enterprise` | 企业管理员权限 |

### 获取 Token 步骤

1. 登录 [AtomGit](https://atomgit.com)
2. 进入 [个人令牌设置](https://atomgit.com/setting/token-classic)
3. 点击「生成新令牌」
4. 勾选所需权限
5. 复制令牌并保存到环境变量

```bash
# Linux/macOS
export ATOMGIT_TOKEN="your-token-here"

# Windows PowerShell
$env:ATOMGIT_TOKEN="your-token-here"
```

## 安全须知

### ⚠️ Token 安全

- **永远不要把 Token 提交到代码仓库**
- 不要在聊天中直接发送 Token
- 使用环境变量存储 Token
- 定期轮换 Token（建议 90 天）
- 不再使用的 Token 立即撤销

### ⚠️ 危险操作

以下操作默认禁用：

```
delete_*       — 删除仓库、文件、分支等
remove_*       — 移除成员等
leave_*        — 退出组织等
archive_*      — 归档仓库
transfer_*     — 转移仓库
```

如需启用，设置环境变量：

```bash
export ATOMGIT_ENABLE_DANGEROUS_TOOLS=true
```

### ⚠️ 权限最小化

遵循最小权限原则：
- 只授予必需的权限
- 临时操作完成后撤销 Token
- 敏感操作使用专用 Token

## 配置示例

### 基础配置

```bash
# ~/.bashrc 或 ~/.zshrc
export ATOMGIT_TOKEN="your-token"
```

### 企业用户配置

```bash
export ATOMGIT_TOKEN="your-token"
export ATOMGIT_API_V8="https://api.atomgit.com/api/v8"
```

### 开发者配置

```bash
export ATOMGIT_TOKEN="your-token"
export ATOMGIT_ENABLE_DANGEROUS_TOOLS=true  # 启用危险操作
```

## 故障排除

### Token 无效

```
错误：401 Unauthorized
解决：检查 Token 是否正确设置
     echo $ATOMGIT_TOKEN
```

### 权限不足

```
错误：403 Forbidden
解决：Token 缺少必要权限，重新生成并勾选相应权限
```

### 工具未找到

```
错误：Unknown tool: atomgit_xxx
解决：确认 MCP Server 已正确安装和配置
```

## 相关链接

- [AtomGit MCP Server](https://atomgit.com/zkxw2008/AtomGit-MCP-Server)
- [AtomGit API 文档](https://docs.atomgit.com/docs/apis/)
- [Token 设置页面](https://atomgit.com/setting/token-classic)