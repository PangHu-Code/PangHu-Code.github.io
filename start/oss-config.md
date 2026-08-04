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
    "windows": "2.4.0",
    "macos": "2.4.0",
    "android": "2.4.0"
  },
  "minSupportedVersion": "2.0.0",
  "download": "https://download.example.com",
  "website": "https://www.example.com",
  "invitationWebsite": "https://invite.example.com",
  "notes": "版本更新说明",
  "imgbbApiKey": "your_imgbb_api_key_here",
  "logo": "https://your-oss.com/logo.png",
  "customerServiceType": "chatwoot",
  "customerServiceToken": "your_customer_service_token_here",
  "latencyReductionPct": "10",
  "minLatencyThresholdMs": "200",
  "userAgents": [
    { "label": "测试自定义UA", "value": "clash-verge/v2.4.0" }
  ]
}
```



### 字段说明


| 字段                      | 类型     | 必填  | 说明                                                                                       |
| ----------------------- | ------ | --- | ---------------------------------------------------------------------------------------- |
| `domain`                | Array  | ✅   | API 服务器地址列表，客户端会按顺序尝试连接                                                                  |
| `version`               | Object | ✅   | 各平台最新版本号                                                                                 |
| `version.windows`       | String | ✅   | Windows 客户端最新版本                                                                          |
| `version.macos`         | String | ✅   | macOS 客户端最新版本                                                                            |
| `version.android`       | String | ✅   | Android 客户端最新版本                                                                          |
| `minSupportedVersion`   | String | ❌   | 最低支持版本，低于此版本的用户将被**强制更新**（无法跳过）                                                          |
| `download`              | String | ✅   | 客户端下载页面地址                                                                                |
| `website`               | String | ✅   | 官网地址，用于"打开官网"功能（可以是官网、地址发布页或导航站）                                                         |
| `invitationWebsite`     | String | ❌   | 拼接邀请地址的网址。如未填写，客户端内点击复制邀请码则仅复制邀请码 code                                                   |
| `notes`                 | String | ❌   | 版本更新说明/公告                                                                                |
| `imgbbApiKey`           | String | ❌   | ImgBB 图床 API Key（用于上传图片）                                                                 |
| `logo`                  | String | ✅   | 应用 Logo 的 URL 地址（格式要求见下方说明）                                                              |
| `customerServiceType`   | String | ❌   | 在线客服类型，目前仅支持 `chatwoot` 或 `crisp`                                                        |
| `customerServiceToken`  | String | ❌   | 客服系统的密钥 (Token)                                                                          |
| `latencyReductionPct`   | String | ❌   | 降低延迟的百分比（例如 `"10"` 表示在阈值之上再降低 10%）。**可选**；若填写则必须同时填写 `minLatencyThresholdMs`，由最小阈值参与控制逻辑 |
| `minLatencyThresholdMs` | String | ❌   | 延迟最小阈值（毫秒）。**可选**；当配置了 `latencyReductionPct` 时**必填**，与百分比配合使用                            |
| `userAgents`            | Array  | ❌   | 客户端可选的 User-Agent 预设列表，用户可在客户端内切换                                                        |
| `userAgents[].label`    | String | ❌   | 预设项在客户端中显示的名称（如 `"CFW测试"`）                                                               |
| `userAgents[].value`    | String | ❌   | 实际发送的 User-Agent 字符串（如 `"`clash-verge/v2.4.0`"`）                                         |


`userAgents` **说明**：可选配置，用于向客户端下发一组 User-Agent 预设。每项包含 `label`（展示名称）和 `value`（请求时使用的 UA 字符串）。可配置多项，客户端会展示为可选项供用户切换；不填则使用客户端默认行为。

**成对约束**：`latencyReductionPct` 与 `minLatencyThresholdMs` 均可不填；一旦填写 `latencyReductionPct`，必须填写 `minLatencyThresholdMs`，否则配置不完整。

---



## Logo 配置说明



### 格式要求


| 要求  | 说明             |
| --- | -------------- |
| 尺寸  | 1024 × 1024 像素 |
| 格式  | PNG            |
| 圆角  | 四个角均为 80px     |




### 制作步骤

1. 准备一张 **1024×1024** 像素的 PNG 格式 Logo 图片
2. 访问在线圆角工具：[https://www.dute.org/image-round](https://www.dute.org/image-round)
3. 上传 Logo 图片
4. 设置四个角的圆角值均为 **80**
5. 导出并下载处理后的图片
6. 将处理好的 Logo 上传到大厂 OSS（强烈建议），不推荐上传到图床
7. 获取 Logo 的公开访问 URL，填入配置文件的 `logo` 字段

> **说明**：`logo` 只会在**打包时**拉取一次，不会在每次启动时重复拉取，因此不会持续消耗用户流量。  
> 为保证打包拉取稳定性，强烈建议使用阿里云 OSS、腾讯云 COS 等大厂对象存储；不推荐使用图床。

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
    "windows": "2.4.0",
    "macos": "2.4.0",
    "android": "2.4.0"
  },
  "minSupportedVersion": "2.0.0"
}
```

上述配置表示：版本低于 2.0.0 的用户会被强制更新，2.0.0 及以上用户可正常使用。

---



## 配置步骤



### 步骤 1：获取 ImgBB API Key（可选）

如需图片上传功能：

1. 访问 [https://imgbb.com/](https://imgbb.com/)
2. 注册/登录账号
3. 进入 [https://api.imgbb.com/](https://api.imgbb.com/)
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
    "windows": "2.4.0",
    "macos": "2.4.0",
    "android": "2.4.0"
  },
  "minSupportedVersion": "2.0.0",
  "download": "https://download.example.com",
  "website": "https://www.example.com",
  "invitationWebsite": "https://invite.example.com",
  "notes": "v1.0.0 首次发布",
  "imgbbApiKey": "a1b2c3d4e5f6g7h8i9j0",
  "logo": "https://your-oss.com/logo.png",
  "customerServiceType": "chatwoot",
  "customerServiceToken": "a1b2c3d4e5f6g7h8i9j0",
  "latencyReductionPct": "10",
  "minLatencyThresholdMs": "200",
  "userAgents": [
    { "label": "CFW测试", "value": "ClashforWindows/99.99.99" }
  ]
}
```

> ⚠️ **注意**：JSON 格式要求严格，数组/对象最后一个元素后面**不能有逗号**！

**错误示例**：

```
"https://api2.example.com",   ← 多余的逗号，会导致解析失败
```



### 步骤 3：验证 JSON 格式

上传前请先验证 JSON 格式是否正确：

- 在线验证：[https://jsonlint.com/](https://jsonlint.com/)
- JSON 格式化/转换：[https://tool.oschina.net/codeformat/json](https://tool.oschina.net/codeformat/json)



### 步骤 4：加密并上传到 OSS

1. 打开 TG 机器人：[https://t.me/panghu_client_bot](https://t.me/panghu_client_bot)
2. 按机器人提示提交 JSON 内容进行加密
3. 获取加密后的文件
4. 登录阿里云 OSS / 腾讯云 COS / 其他对象存储，上传文件
5. 设置文件为**公开读取**权限，获取访问 URL

---



## 完整流程示例

```
1. 编写原始 JSON
      ↓
2. 验证 JSON 格式
      ↓
3. 在线加密
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

### Q: API 地址打开 404、请求不通怎么排查？

A: 可按以下步骤排查：

- 直接复制 `domain` 里配置的地址，在后面拼接 `/guest/comm/config` 后用浏览器打开。
- 例如：`domain` 配的是 `https://api1.example.com/s`，那就访问 `https://api1.example.com/s/guest/comm/config`。
- 如果能正常返回配置信息，说明这个 API 地址可用。
- 如果打开是 404 或不通，通常是 `domain` 地址写错了（比如后缀少了、后缀写错、拼写错误）；按你实际地址改正确后再测试。



### Q: Logo 会在每次启动时都重新拉取吗？建议上传到哪里？

A: 不会。`logo` 只会在打包时拉取一次，不会在每次启动时重复拉取，因此不会持续消耗用户流量。
强烈建议将 Logo 上传到阿里云 OSS、腾讯云 COS 等大厂对象存储，以保证打包拉取稳定性；不推荐上传到图床。

### Q: JSON 解析报错怎么办？

A: 检查以下常见问题：

- 数组/对象最后一个元素后面是否有多余逗号
- 字符串是否使用双引号（不能用单引号）
- 是否有中文标点符号（如：，"" 等）

