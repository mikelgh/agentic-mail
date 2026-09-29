# Agentic Mail

智能邮件助手 - GOSIM 2026 黑客松参赛项目

## 功能

- **邮件读取**：通过 OctoSense 邮件服务读取收件箱
- **日程识别**：自动检测邮件中的日程信息（会议、时间、地点）
- **智能提取**：从邮件正文提取日期、时间、地点等关键信息
- **回复草稿**：根据邮件内容自动生成回复草稿
- **Agentic 特性**：主动分析、智能识别、跨应用联动

## 技术栈

- **平台**：OctoSense App Hub
- **语言**：Splash (OctoScript)
- **能力**：mail（邮件读取）、storage（本地存储）
- **架构**：Script App（独立逻辑应用）

## 项目结构

```
agentic-mail/
  bundle/
    manifest.json      # 应用清单（ID、版本、能力）
    listing.json       # 商店展示信息
    main.splash        # 主程序（Splash 脚本）
    assets/
      icon.svg         # 应用图标
    screenshots/
      01-main.png      # 截图（待生成）
  AGENTS.md            # AI Agent 协作指南
  README.md            # 本文件
```

## 开发流程

### 1. 构建 Hub 工具

```bash
cd /c/DevOps/app-hub
cargo build --release -p octosense-app-hub --bin hub
cargo build --release -p octosense-card-host --bin card-host
```

### 2. 运行应用

```bash
export HUB_REPO="/c/DevOps/app-hub"
export HUB_BIN="$HUB_REPO/target/release/hub"
export CARD_HOST_BIN="$HUB_REPO/target/release/card-host"
export APP_REPO="/c/DevOps/agentic-mail"

# 盖章（计算 bundle 哈希）
"$HUB_BIN" stamp "$APP_REPO/bundle"

# 运行（开发模式）
cd "$HUB_REPO"
MAKEPAD_REMOTE=8151 "$CARD_HOST_BIN" --bundle "$APP_REPO/bundle" \
  --app-data "$APP_REPO/.local-state" --allow-unsigned &

# 截图
curl --fail --silent --show-error "127.0.0.1:8151/g?raw=1" \
  -o "$APP_REPO/bundle/screenshots/01-main.png"
curl --fail --silent --show-error "127.0.0.1:8151/quit"
```

### 3. 检查与提交

```bash
# 检查
"$HUB_BIN" check "$APP_REPO/bundle" --allow-unsigned

# 扫描（生成审查包）
mkdir -p "$APP_REPO/build"
"$HUB_BIN" scan "$APP_REPO/bundle" --packet "$APP_REPO/build/review.json"

# 签名和提交（需要发布者密钥）
# 参见 app-hub/docs/PUBLISHING.md
```

## 评审要点

### 初赛评审要求

1. **一次完整操作**：
   - 读取邮件 → 分析日程 → 生成回复，三步成链
   
2. **可核对的结果**：
   - 提取的日程可回溯原文
   - 回复草稿基于邮件内容
   
3. **失败或空状态**：
   - 空收件箱 → 显示"暂无邮件"
   - 无日程内容 → 不显示日程面板
   - 网络失败 → 显示错误信息

### Agentic 特性体现

- **主动监控**：自动同步并分析邮件
- **智能识别**：基于关键词和模式匹配提取日程
- **跨应用联动**：邮件 → 日程 → 回复，形成完整工作流
- **数据聚合**：统一管理邮件中的日程信息

## 数据来源与限制

### 数据来源

| 数据 | 来源 | 说明 |
|---|---|---|
| 邮件内容 | OctoSense 设备邮件服务 | 通过 `host.request("mail.*")` API 读取，用户主动在设备端登录邮箱账号后授权 |
| 账号信息 | OctoSense 主机 | 密码由设备保管，应用只获取账号 ID 和地址 |
| 日程识别 | 邮件正文分析 | 基于关键词和模式匹配的本地文本分析，不依赖外部服务 |
| 回复草稿 | 邮件内容生成 | 基于邮件原文的模板化草稿，本地生成 |

### 数据限制

- **不存储邮件原文**：应用不持久化邮件内容，每次从设备服务实时读取
- **不访问外部网络**：不需要 `net` 能力，所有分析在本地完成
- **不收集敏感信息**：不请求密码、PIN 或一次性验证码
- **存储仅限本地**：`storage` 能力仅用于应用自身的配置数据，在设备沙箱内

### 运行前提

- 需要 OctoSense 设备环境
- 用户需在设备上登录至少一个邮箱账号
- 邮箱需支持 IMAP 协议（由设备端邮件服务处理）

## 下一步

- [ ] 编译 Hub 工具
- [ ] 运行应用并截图
- [ ] 测试邮件读取和日程识别
- [ ] 优化 UI 和交互
- [ ] 准备演示脚本
- [ ] 提交到 App Hub

## 参考

- [App Hub 文档](/c/DevOps/app-hub/docs/FIRST-APP.md)
- [发布指南](/c/DevOps/app-hub/docs/PUBLISHING.md)
- [系统邮件应用](/c/DevOps/system-apps/apps/mail/bundle/main.splash)
- [Splash API](https://github.com/OctoSense-org/OctoScript-App-Design-Flow/blob/main/docs/SCRIPT-API.md)
