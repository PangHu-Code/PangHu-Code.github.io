# 自定义套餐说明

同时支持两种显示方案，客户端会自动处理。

---

## 方案 1：HTML 格式

支持自定义 HTML 语法，可自由编写套餐介绍内容。

### 示例

```html
<div style="font-size:13px; line-height:1.8; color:#111;">
  <p>
    <b style="color:#111;"></b>
    此处支持 <b style="color:#2563eb;">HTML 语法</b>，可以自由编写套餐介绍内容。
  </p>

  <ul style="margin:6px 0 8px 18px; padding:0;">
    <li>支持使用 <b>加粗文字</b></li>
    <li>支持设置 <span style="color:#e11d48;">文字颜色</span>、<span style="color:#16a34a;">高亮内容</span></li>
    <li>支持使用列表、段落、以及科技风 Emoji（如：📡 🔗 ⚡ 💠）</li>
  </ul>

  <p style="margin-top:6px;">
    例如，你可以写成：
  </p>
  <p style="
    padding:8px 10px;
    background:#f9fafb;
    border-radius:8px;
    border:1px dashed #e5e7eb;
    font-size:12px;
    color:#374151;
  ">
    &lt;b&gt;套餐说明：&lt;/b&gt;&lt;br /&gt;
    📡 支持流媒体播放&lt;br /&gt;
    🔗 不限制使用人数&lt;br /&gt;
    💠 不限制到期时间&lt;br /&gt;
    ⚡ 不限制网络速度
  </p>

  <p style="font-size:12px; color:#6b7280; margin-top:6px;">
    上述只是示例，你可以根据业务需求自由编辑文案和 HTML 标签。
  </p>
</div>
```

---

## 方案 2：潮汐客户端格式（JSON）

使用 JSON 数组格式，每项包含 `feature` 和 `support` 字段。

### 示例

```json
[
    {
        "feature": "每月 300G 流量",
        "support": true
    },
    {
        "feature": "全球 25 个国家接入",
        "support": true
    },
    {
        "feature": "99.99% SLA 保证",
        "support": true
    },
    {
        "feature": "1Gbps 带宽保证",
        "support": false
    }
]
```

