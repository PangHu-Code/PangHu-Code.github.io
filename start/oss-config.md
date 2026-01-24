# OSS 配置说明

本文档说明如何配置和部署远程 OSS 配置文件。

---

## 配置文件结构

### 原始 JSON 格式

```json
{
  "domain": [
    "https://api1.example.com/api/v1",
    "https://api2.example.com/api/v1",
    "https://api3.example.com/api/v1"
  ],
  "version": {
    "windows": "1.5.0",
    "macos": "1.5.0",
    "android": "1.5.0"
  },
  "minSupportedVersion": "1.5.0",
  "download": "https://download.example.com",
  "website": "https://www.example.com",
  "invitationWebsite": "https://invite.example.com",
  "notes": "版本更新说明",
  "imgbbApiKey": "your_imgbb_api_key_here",
  "logo": "https://your-oss.com/logo.png",
  "customerServiceType": "chatwoot",
  "customerServiceToken": "your_customer_service_token_here"
}
```

### 字段说明

| 字段 | 类型 | 必填 | 说明 |
|------|------|------|------|
| `domain` | Array | ✅ | API 服务器地址列表，客户端会按顺序尝试连接 |
| `version` | Object | ✅ | 各平台最新版本号 |
| `version.windows` | String | ✅ | Windows 客户端最新版本 |
| `version.macos` | String | ✅ | macOS 客户端最新版本 |
| `version.android` | String | ✅ | Android 客户端最新版本 |
| `minSupportedVersion` | String | ❌ | 最低支持版本，低于此版本的用户将被**强制更新**（无法跳过） |
| `download` | String | ✅ | 客户端下载页面地址 |
| `website` | String | ✅ | 官网地址，用于"打开官网"功能（可以是官网、地址发布页或导航站） |
| `invitationWebsite` | String | ❌ | 拼接邀请地址的网址。如未填写，客户端内点击复制邀请码则仅复制邀请码 code |
| `notes` | String | ❌ | 版本更新说明/公告 |
| `imgbbApiKey` | String | ❌ | ImgBB 图床 API Key（用于上传图片） |
| `logo` | String | ✅ | 应用 Logo 的 URL 地址（格式要求见下方说明） |
| `customerServiceType` | String | ❌ | 在线客服类型，目前仅支持 `chatwoot` 或 `crisp` |
| `customerServiceToken` | String | ❌ | 客服系统的密钥 (Token) |

---

## Logo 配置说明

### 格式要求

| 要求 | 说明 |
|------|------|
| 尺寸 | 1024 × 1024 像素 |
| 格式 | PNG |
| 圆角 | 四个角均为 80px |

### 制作步骤

1. 准备一张 **1024×1024** 像素的 PNG 格式 Logo 图片
2. 访问在线圆角工具：https://www.dute.org/image-round
3. 上传 Logo 图片
4. 设置四个角的圆角值均为 **80**
5. 导出并下载处理后的图片
6. 将处理好的 Logo 上传到图床或 OSS
7. 获取 Logo 的公开访问 URL，填入配置文件的 `logo` 字段

---

## 强制更新说明

通过设置 `minSupportedVersion` 字段可以远程控制强制更新：

- 当用户的 App 版本 < `minSupportedVersion` 时，会弹出强制更新界面
- 用户无法跳过，必须更新才能继续使用
- 适用于有破坏性变更（如 API 变化、加密方式变更）时使用

**示例**：
```json
{
  "version": {
    "windows": "1.5.0",
    "macos": "1.5.0",
    "android": "1.5.0"
  },
  "minSupportedVersion": "1.5.0"
}
```
上述配置表示：版本低于 1.5.0 的用户会被强制更新，1.5.0 及以上用户可正常使用。

---

## 配置步骤

### 步骤 1：获取 ImgBB API Key（可选）

如需图片上传功能：

1. 访问 https://imgbb.com/
2. 注册/登录账号
3. 进入 https://api.imgbb.com/
4. 点击 "Get API Key" 获取 API Key
5. 将 Key 填入配置文件的 `imgbbApiKey` 字段

### 步骤 2：编写配置文件

创建 `config.json` 文件：

```json
{
  "domain": [
    "https://api1.example.com/api/v1",
    "https://api2.example.com/api/v1"
  ],
  "version": {
    "windows": "1.5.0",
    "macos": "1.5.0",
    "android": "1.5.0"
  },
  "minSupportedVersion": "1.5.0",
  "download": "https://download.example.com",
  "website": "https://www.example.com",
  "invitationWebsite": "https://invite.example.com",
  "notes": "v1.0.0 首次发布",
  "imgbbApiKey": "a1b2c3d4e5f6g7h8i9j0",
  "logo": "https://your-oss.com/logo.png",
  "customerServiceType": "chatwoot",
  "customerServiceToken": "a1b2c3d4e5f6g7h8i9j0"
}
```

> ⚠️ **注意**：JSON 格式要求严格，数组/对象最后一个元素后面**不能有逗号**！

**错误示例**：
```
"https://api2.example.com",   ← 多余的逗号，会导致解析失败
```

### 步骤 3：验证 JSON 格式

上传前请先验证 JSON 格式是否正确：

- 在线验证：https://jsonlint.com/

### 步骤 4：加密并上传到 OSS

1. 访问 https://encrypt.panghu.wiki/
2. 粘贴 JSON 内容进行非对称加密
3. 下载加密后的文件
4. 登录阿里云 OSS / 腾讯云 COS / 其他对象存储，上传文件
5. 设置文件为**公开读取**权限，获取访问 URL

---

## 完整流程示例

```
1. 编写原始 JSON
      ↓
2. 验证 JSON 格式（jsonlint.com）
      ↓
3. 在线加密（panghu.com）
      ↓
4. 下载加密文件并上传到 OSS
      ↓
5. 获取公开访问 URL
```

---

## 常见问题

### Q: 配置更新后客户端多久生效？

A: 客户端每次启动时会重新获取配置。如需立即生效，用户需要重启应用。

### Q: domain 数组配置多个地址有什么用？

A: 客户端会按顺序尝试连接，如果第一个地址不可用，会自动尝试下一个，提高可用性。

### Q: JSON 解析报错怎么办？

A: 检查以下常见问题：
- 数组/对象最后一个元素后面是否有多余逗号
- 字符串是否使用双引号（不能用单引号）
- 是否有中文标点符号（如：，"" 等）
