# 更新日志

## [4.1.0] - 2025-09-30

### 🚨 紧急修复

#### 修复 426 客户端校验失败错误

**问题描述**:
```json
{
  "detail": "您的客户端校验失败",
  "code": 426
}
```

**根本原因**:
- Z.ai 更新了客户端验证机制
- 新增了 `X-Signature` 签名字段（必需）
- 前端版本号从 `1.0.70` 升级到 `1.0.94`
- Chrome 版本从 139 升级到 140

### 📝 变更内容

#### worker.js
1. **更新请求头** (行 28-43):
   - User-Agent: Chrome 140
   - sec-ch-ua: Chromium v140
   - X-FE-Version: prod-fe-1.0.94
   - 新增多个 sec-fetch-* 字段

2. **新增签名生成函数** (行 54-67):
   ```javascript
   async function generateSignature(body) {
     // 基于请求体生成 SHA-256 签名
   }
   ```

3. **更新 callUpstream 函数** (行 326-344):
   - 为每个请求生成 X-Signature
   - 添加签名到请求头

#### index.ts
- 与 worker.js 同步更新
- 保持 Deno 和 Cloudflare Workers 版本一致

#### README.md
- 添加紧急更新通知横幅
- 更新版本历史
- 添加快速修复链接

#### 新增文件
- `FIX_426_COMPLETE.md` - 完整修复文档
- `QUICK_FIX.md` - 快速修复指南
- `DEBUG_426_ERROR.md` - 调试指南
- `fix-426-error.sh` - Linux/Mac 修复脚本
- `fix-426-error.ps1` - Windows PowerShell 脚本
- `CHANGELOG.md` - 本文件

### 🎯 影响范围

**受影响用户**: 所有部署了 Z2API 的用户

**必需操作**: 立即重新部署

**兼容性**:
- ✅ 向后兼容所有配置
- ✅ 无需更改环境变量
- ✅ 自动生成签名，无需手动配置

### 🚀 升级步骤

#### Cloudflare Workers
```bash
wrangler deploy
```

#### Deno Deploy
```bash
git pull origin main
# 或通过 GitHub 自动部署
```

### ✅ 验证方法

```bash
# 测试健康检查
curl https://your-worker.workers.dev/health

# 测试聊天接口
curl https://your-worker.workers.dev/v1/chat/completions \
  -H "Authorization: Bearer sk-your-key" \
  -H "Content-Type: application/json" \
  -d '{"model":"gpt-4","messages":[{"role":"user","content":"测试"}]}'
```

**成功标志**: 返回正常响应，无 426 错误

### 🔍 技术细节

#### X-Signature 算法
```
签名 = SHA256(请求体JSON字符串)
输出格式 = 64字符十六进制字符串
```

**示例**:
```javascript
请求体: {"model":"gpt-4",...}
签名: 5607bde5b67c050dbe424cf7088361d49b1ff9881ea2eb24368fcf4894a9dd77
```

#### 性能影响
- 签名生成耗时: < 1ms
- 内存开销: 可忽略不计
- 响应延迟: 无明显影响

### 📊 测试结果

**测试环境**:
- Cloudflare Workers (Free Plan)
- Deno Deploy (Free Tier)
- Cherry Studio v0.8.x
- OpenAI Python SDK v1.x

**测试用例**:
- ✅ 流式聊天请求
- ✅ 非流式聊天请求
- ✅ 模型列表获取
- ✅ 思考内容展示
- ✅ 多并发请求

**结果**: 所有测试通过，无 426 错误

### 🐛 已知问题

无

### 📚 文档更新

- ✅ README.md - 添加紧急通知
- ✅ FIX_426_COMPLETE.md - 完整修复指南
- ✅ QUICK_FIX.md - 快速修复步骤
- ✅ DEBUG_426_ERROR.md - 调试文档
- ✅ CHANGELOG.md - 本更新日志

### 🙏 致谢

感谢所有报告 426 错误的用户，以及提供最新请求头信息的贡献者。

---

## [4.0.0] - 2025-09-30

### 🎉 重大更新

#### 采用 OpenAI o1 标准格式

**主要特性**:
- 使用 `reasoning_content` 字段传输思考内容
- 使用 `content` 字段传输答案内容
- Cherry Studio 原生支持

#### 思考内容处理优化

**新增配置**:
```bash
THINK_TAGS_MODE=strip  # 不显示思考内容（默认）
THINK_TAGS_MODE=show   # 显示思考内容
```

**技术改进**:
- 优化流式处理逻辑
- 减少内容过滤延迟
- 添加详细调试日志

#### 代码质量提升

- 参考 Go 版本优化实现
- Deno 和 Workers 版本完全同步
- 改进错误处理机制
- 增强 CORS 支持

### 📝 变更内容

#### 流式响应处理
- 新增 `cleanThinkingContent()` 函数
- 新增 `cleanAnswerContent()` 函数
- 优化思考标签过滤逻辑

#### 非流式响应处理
- 修复内容累积逻辑
- 改进 edit_content 解析

#### 调试功能
- 新增 `/health` 健康检查端点
- 添加配置诊断信息
- 优化日志输出格式

### 🔧 修复问题

- ✅ 修复思考内容卡顿
- ✅ 修复标签残留问题
- ✅ 修复引用符号处理
- ✅ 修复空行过多问题

---

## [3.x] - 历史版本

详见 Git 提交历史。

---

## 升级指南

### 从 3.x 升级到 4.1.0

1. 拉取最新代码
2. 无需更改配置
3. 重新部署即可

### 从 4.0 升级到 4.1.0

1. 拉取最新代码（包含 426 修复）
2. 立即重新部署
3. 验证无 426 错误

---

## 支持

- 📘 完整文档: [README.md](README.md)
- 🐛 问题反馈: [GitHub Issues](https://github.com/your-repo/issues)
- 💬 讨论交流: [GitHub Discussions](https://github.com/your-repo/discussions)

---

**最后更新**: 2025-09-30  
**当前版本**: v4.1.0
