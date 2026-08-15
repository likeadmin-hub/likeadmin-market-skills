# Seedance 2.0 · 创建任务

提交 Seedance 2.0 视频生成任务。支持文生/图生/视频编辑/延长/多模态参考等场景。

<details>
<summary>基本信息</summary>

| 字段 | 内容 |
| --- | --- |
| 类型 | 应用 API |
| 应用名称 | Seedance 2.0 |
| 应用编码 | `seedance` |
| API 名称 | 创建任务 |
| API 编码 | `create` |
| 请求方式 | `POST` |
| 请求路径 | `/api/v1/apps/seedance/create` |
| 调用模式 | 异步 |
| 计费方式 | 按 token（输入 0.000000 元 / 百万 token，输出 0.000000 元 / 百万 token） |

</details>

## 鉴权

```http
Authorization: Bearer <YOUR_API_KEY>
Content-Type: application/json
```

## 请求路径

```http
POST /api/v1/apps/seedance/create
```

该接口为异步任务，提交后返回 `task_id`，请通过 `GET /api/v1/tasks/{task_id}` 查询结果，或在请求中传 `callback_url` 接收完成回调。

## 业务参数

| 参数 | 类型 | 必填 | 默认值 | 可选值 / 范围 | 示例 | 说明 |
| --- | --- | --- | --- | --- | --- | --- |
| `seed` | integer | 否 | — | — | — | 随机种子，可复现结果（若服务支持） |
| `draft` | boolean | 否 | — | — | — | 是否草稿模式（若服务支持） |
| `model` | string | 是 | — | `seedance-2-text-2-video` / `seedance-2-video-2-video` / `seedance-2-fast-text-2-video` / `seedance-2-fast-video-2-video` | `seedance-2-text-2-video` | 模型标识。推荐使用 seedance-2-text-2-video / seedance-2-video-2-video / seedance-2-fast-text-2-video / seedance-2-fast-video-2-video（平台自动映射为内部模型标识 ant-2-*）。也可直接传 ant-2-*。 |
| `ratio` | string | 否 | `adaptive` | `16:9` / `4:3` / `1:1` / `3:4` / `9:16` / `21:9` / `adaptive` | `16:9` | 画面宽高比。adaptive 表示由模型根据输入自动选择 |
| `tools` | array | 否 | — | — | `[{"type":"web_search"}]` | 可选工具（仅文生视频支持 web_search 联网搜索） |
| `frames` | integer | 否 | — | — | — | 帧数相关参数（若服务支持） |
| `content` | array | 是 | — | — | `[{"text":"一只金毛犬在海边奔跑，夕阳西下","type":"text"}]` | 多模态输入列表：支持 文本 + 图片 + 视频 + 音频 的任意组合。图片最多 9 张、视频最多 3 个、音频最多 3 段，且不可单独传音频。含 video_url 时按「含视频输入」档位计费。 |
| `duration` | integer | 否 | `5` | `4` ~ `15` | `5` | 生成视频时长（秒）。取值 4~15，或 -1 由模型自动决定 |
| `watermark` | boolean | 否 | — | — | — | 是否在视频右下角添加水印 |
| `resolution` | string | 否 | `720p` | `480p` / `720p` / `1080p` | `720p` | 输出分辨率 |
| `callback_url` | string | 否 | — | — | `https://your-domain.com/webhook/seedance` | 异步任务完成/失败时的回调地址 |
| `camera_fixed` | boolean | 否 | — | — | — | 是否固定机位（若服务支持） |
| `service_tier` | string | 否 | — | — | — | 服务档位（若服务支持） |
| `generate_audio` | boolean | 否 | `1` | — | `1` | 是否生成与画面同步的音频 |

## 请求示例

```json
{
    "seed": 1,
    "draft": true,
    "model": "seedance-2-text-2-video",
    "ratio": "16:9",
    "tools": [
        {
            "type": "web_search"
        }
    ],
    "frames": 1,
    "content": [
        {
            "text": "一只金毛犬在海边奔跑，夕阳西下",
            "type": "text"
        }
    ],
    "duration": "5",
    "watermark": true,
    "resolution": "720p",
    "callback_url": "https://your-domain.com/webhook/seedance",
    "camera_fixed": true,
    "service_tier": "string",
    "generate_audio": "1"
}
```

## 成功响应

```json
{
    "task_id": "tsk_xxxxxxxxxxxxxxxx",
    "status": "pending",
    "created_at": 1740000000
}
```

### 查询任务结果

```http
GET /api/v1/tasks/{task_id}
```

```json
{
    "task_id": "tsk_xxxxxxxxxxxxxxxx",
    "status": "completed",
    "created_at": 1740000000,
    "completed_at": 1740000060,
    "result": {
        "output": "任务输出内容"
    },
    "usage": {
        "points": 100
    }
}
```

## 失败响应

```json
{
    "error": {
        "message": "点数余额不足",
        "type": "insufficient_points",
        "code": "insufficient_points"
    }
}
```

### 错误码

| HTTP | code | 含义 |
| --- | --- | --- |
| 400 | `invalid_request` | 参数缺失或格式错误 |
| 401 | `auth_failed` | API Key 缺失或无效 |
| 402 | `insufficient_points` | 点数余额不足 |
| 402 | `key_quota_exceeded` | 当前 API Key 点数额度不足 |
| 403 | `permission_denied` | 当前 API Key 无权调用该模型/应用 |
| 404 | `not_found` | 模型 / 应用 / 任务不存在 |
| 429 | `queue_limit_exceeded` | 排队任务已达上限 |
| 5xx | `server_error` | 服务异常，请稍后重试 |
