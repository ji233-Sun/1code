# GitHub Actions 部署指南

## 工作流文件说明

已创建两个 GitHub Actions 工作流：

### 1. `release-mac.yml` - 签名版本（推荐用于正式发布）
- 需要 Apple 开发者证书
- 需要配置以下 GitHub Secrets：
  - `APPLE_IDENTITY`: Apple 开发者身份
  - `APPLE_TEAM_ID`: Apple 团队 ID
  - `APPLE_ID`: Apple ID
  - `APPLE_APP_SPECIFIC_PASSWORD`: App 专用密码

### 2. `release-mac-unsigned.yml` - 未签名版本（快速测试）
- 不需要证书
- 构建速度更快
- 用户需要绕过 macOS 安全检查

## 使用方法

### 方式一：通过 Git 标签触发（推荐）

```bash
# 创建并推送标签
git tag v0.0.22
git push origin v0.0.22
```

### 方式二：手动触发

1. 进入 GitHub 仓库
2. 点击 "Actions" 标签
3. 选择工作流（release-mac 或 release-mac-unsigned）
4. 点击 "Run workflow"
5. 选择分支并点击 "Run workflow"

## 配置 GitHub Secrets（仅签名版本需要）

如果使用签名版本，需要在 GitHub 仓库中配置以下 Secrets：

1. 进入仓库设置：Settings → Secrets and variables → Actions
2. 点击 "New repository secret"
3. 添加以下 Secrets：

```
APPLE_IDENTITY: "Developer ID Application: Your Name (TEAM_ID)"
APPLE_TEAM_ID: "你的团队ID"
APPLE_ID: "your@email.com"
APPLE_APP_SPECIFIC_PASSWORD: "xxxx-xxxx-xxxx-xxxx"
```

### 获取 Apple App 专用密码

1. 访问 https://appleid.apple.com
2. 登录你的 Apple ID
3. 在"安全"部分，点击"App 专用密码"
4. 点击"生成密码"
5. 输入标签名（如 "1Code GitHub Actions"）
6. 复制生成的密码到 GitHub Secrets

## Release 产物

构建成功后，Release 将包含：
- `1Code-{version}-arm64-mac.dmg` - Apple Silicon (M1/M2/M3) 安装包
- `1Code-{version}-arm64-mac.zip` - 压缩包版本
- `1Code-{version}-arm64.dmg.blockmap` - 增量更新映射文件
- 其他构建产物（如果配置了 Intel 版本）

## 本地测试

在推送到 GitHub 之前，可以本地测试构建：

```bash
# 安装依赖
bun install

# 下载 Claude 二进制文件
bun run claude:download

# 构建应用
bun run build

# 打包 macOS 版本
bun run package:mac

# 查看构建产物
ls -lah release/
```

## 故障排查

### 构建失败

1. 检查 Actions 日志中的错误信息
2. 确认所有依赖都已正确安装
3. 验证 Claude 二进制文件是否下载成功

### 代码签名失败（签名版本）

1. 验证所有 Secrets 是否正确配置
2. 确认 Apple 开发者证书有效
3. 检查团队 ID 是否正确

### Release 创建失败

1. 确认推送的是标签（tag）而不是普通提交
2. 检查标签格式是否为 `v*`（如 v0.0.22）
3. 确认有 release 目录且包含构建产物

## 下一步

1. 选择合适的工作流（签名或未签名）
2. 如果使用签名版本，配置必要的 Secrets
3. 创建并推送标签以触发构建
4. 在 GitHub Actions 中监控构建进度
5. 构建成功后，在 Releases 页面查看发布的版本

## 参考资源

- [electron-builder 文档](https://www.electron.build/)
- [GitHub Actions 文档](https://docs.github.com/en/actions)
- [Apple 代码签名指南](https://developer.apple.com/support/code-signing/)
