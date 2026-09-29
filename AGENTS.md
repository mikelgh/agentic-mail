# Agentic Mail - AI Agent 协作指南

本项目是 GOSIM 2026 黑客松参赛项目，使用 OctoSense App Hub 平台。

## 项目目标

构建一个智能邮件应用，体现 Agentic 特性：
- 自动读取和分析邮件
- 识别日程信息（时间、地点）
- 生成回复草稿
- 展示主动监控和智能分析能力

## 技术约束

1. **平台**：OctoSense App Hub（script app 类型）
2. **语言**：Splash（OctoScript 脚本语言）
3. **能力**：只能使用 `mail` 和 `storage` capability
4. **安全**：不能直接访问 IMAP，必须通过宿主邮件服务
5. **大小**：bundle 不超过 8MB

## 关键文件

- `bundle/main.splash` - 主程序逻辑
- `bundle/manifest.json` - 应用清单（ID、能力）
- `bundle/listing.json` - 商店展示信息

## 开发规范

### 代码风格
- 使用 Splash 语法（类似 JavaScript）
- 函数命名：snake_case
- 变量命名：snake_case
- 注释：中文，说明"为什么"而非"做什么"

### 邮件服务 API
```
host.request("mail.accounts", {}, callback)
host.request("mail.sync", {account, folder}, callback)
host.request("mail.list", {account, folder, offset, limit}, callback)
host.request("mail.message", {account, folder, message}, callback)
host.request("mail.send", {account, to, subject, body}, callback)
```

### 日程识别逻辑
1. 关键词匹配：会议、meeting、日程、schedule、时间、time 等
2. 日期模式：月/日/号、周一/周二等、明天/后天/今天
3. 时间模式：点、:、时、分、am/pm
4. 地点模式：地点、location、在、at、会议室

## 评审对齐

### 初赛要求
- ✅ 一次完整操作：读取 → 分析 → 回复
- ✅ 可核对结果：日程可回溯原文
- ✅ 失败状态：空收件箱、无日程、网络错误

### Agentic 特性
- ✅ 主动监控：自动同步分析
- ✅ 智能识别：关键词 + 模式匹配
- ✅ 跨应用联动：邮件 → 日程 → 回复

## 常见错误

1. **不要**直接实现 IMAP - 使用 mail service
2. **不要**在 bundle 中包含外部 URL - 使用 `{{assets}}` 占位符
3. **不要**请求未声明的 capability
4. **不要**在代码中包含密码或密钥

## 测试清单

- [ ] 空收件箱显示"暂无邮件"
- [ ] 有邮件时显示列表
- [ ] 包含日程的邮件显示 📅 标记
- [ ] 打开邮件显示提取的日程信息
- [ ] 显示生成的回复草稿
- [ ] 可以编辑并发送回复
- [ ] 网络错误时显示错误信息

## 提交检查

1. `hub stamp` 计算 bundle 哈希
2. `hub check` 通过门控检查
3. `hub scan` 生成审查包
4. 截图已捕获并验证
5. manifest 和 listing 完整准确

## 参考资源

- [App Hub FIRST-APP](/c/DevOps/app-hub/docs/FIRST-APP.md)
- [PUBLISHING 指南](/c/DevOps/app-hub/docs/PUBLISHING.md)
- [系统邮件应用示例](/c/DevOps/system-apps/apps/mail/bundle/main.splash)
- [Splash API 文档](https://github.com/OctoSense-org/OctoScript-App-Design-Flow/blob/main/docs/SCRIPT-API.md)
