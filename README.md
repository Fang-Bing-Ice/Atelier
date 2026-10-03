# Atelier · 图片创作工作台

一个本地运行的图片创作工作台，视觉语言参考 Claude：米黄纸感底色、陶土橙点缀、衬线标题与克制的卡片布局。

## 功能

- 图片海报化：单一主题 / 纸张质感两种风格
- 图片水彩化：淡彩浓度、水彩纸肌理，以及色卡数量、形状、位置
- 签名动画：自定义文字、风格、背景和 3–10 秒时长，可播放与导出 WebM / PNG
- 图片拼贴化：建筑拼贴蒙太奇，可调碎片密度、色调、文字与纸张质感
- 图片简笔化：扁平矢量色阶、轮廓线、纸张留白与编号排版
- 图片漫画风格化：赛璐璐、吉卜力、新海诚、宫崎骏、羊毛毡、皮克斯、水墨、油画
- **AI 助手（API易）**：右上角「AI 助手」抽屉，支持流式对话与 gpt-image 出图 / 参考图改图

## 启动

双击 `preview.cmd`，或在当前目录执行：

```powershell
python tools/apiyi_server.py --port 4173
```

然后访问 <http://127.0.0.1:4173>。前六种工具完全在本地浏览器处理，不上传、不排队。

## AI 助手（API易）

`tools/apiyi_server.py` 同时做两件事：托管静态文件 + 把 `/api/*` 转发到 API易，**API Key 只留在服务端，不会出现在前端代码里**。

### 配置

在项目根目录建 `.env`（已被 `.gitignore` 忽略）：

```dotenv
APIYI_API_KEY=sk-你的密钥        # https://api.apiyi.com/token 复制
APIYI_BASE_URL=https://api.apiyi.com/v1
```

也可改用同名环境变量，环境变量优先级更高。

### 接口

| 接口 | 说明 |
|---|---|
| `GET /api/health` | 自检：Key 是否就位（不返回 Key 内容） |
| `GET /api/models` | 代理 `GET /v1/models`，带 5 分钟缓存 |
| `POST /api/chat` | Chat Completions，支持 `"stream": true` SSE 透传 |
| `POST /api/image` | 文生图，返回 `dataUrl` 并落盘到 `out/ai/` |
| `POST /api/image-edit` | 参考图 / 蒙版改图（multipart 原样转发） |

### 命令行示例

```powershell
$env:APIYI_API_KEY = "sk-..."
python tools/apiyi_chat.py "用一句话介绍你自己"
python tools/apiyi_server.py --port 4173
```

### 注意事项

- 服务默认只监听 `127.0.0.1`，不要改成 `0.0.0.0`。
- 图片是**同步长请求**：默认超时 600s，请勿中途关闭页面 —— 客户端断开服务端仍会计费。
- 模型 ID 区分大小写且版本用点号（`gpt-5.4-mini`，不是 `gpt-5-4-mini`）；完整列表见 <https://docs.apiyi.com/models>。
- 接入文档技能包在 `.agents/skills/apiyi/`，纯 Markdown 版见 <https://docs.apiyi.com/skill.md>。
