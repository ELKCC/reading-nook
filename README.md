<div align="center">

# 📖 共读小屋 · Reading Nook

**[中文](README.md)** · **[English](README.en.md)**

🌏 Don't see your language? [Open a PR](../../pulls) to add one — all welcome.

![license](https://img.shields.io/github/license/zzyyksl/reading-nook?color=8e7cc3)
![python](https://img.shields.io/badge/python-3.10%2B-blue)
![dependencies](https://img.shields.io/badge/dependencies-zero-c96f4a)
![stars](https://img.shields.io/github/stars/zzyyksl/reading-nook?style=flat&color=e8a0ac)

*阅读，是一个人，与另一个人，一起完成的旅程。*

</div>

---

和你的 AI 一起读书的自托管网页。你在手机上翻页读书、划线写想法（粉色气泡），你的 AI 读完同一章后回批注（蓝色气泡）。书、进度、批注全部存在你自己的服务器上。

**核心设计：批注不走 API。** 你的批注存成服务器上的 JSON 文件，你的 AI（Claude Code / 任何能读写服务器文件的 agent）直接读 JSON、写回应——全程走订阅额度，批注环节 API 一分钱不花，而且 AI 回批注前能重读整章，比按页喂 API 的方案理解深得多。

## 功能

- **功能一 · 纯阅读**：翻页阅读器，按段落分页、进度自动记忆、跨章连续翻、☰ 目录侧边栏
- **功能二 · 批注共读**：选中文字划线/写想法，你的批注是粉色气泡，AI 的回应是蓝色气泡；💬 查看本章全部批注
- **整本书笔记 📒**：书架每本书都有「笔记 ›」入口（阅读页 💬 列表底部也有），把全书的划线、想法和双方回复按章汇总；按屏幕实际高度分页，左右滑动或点按钮翻页；卡片默认折叠几行，点一下展开，再点一下跳回原文所在章节
- **章节快跳**：笔记页顶部一排「第N章 · 条数」，点一下跳到该章第一页，翻页时当前章节自动高亮
- **AI 新内容提示**：AI 自己划的线（`who: "ai"`）和 AI 的新回复，会在书架「笔记」按钮上显示角标；笔记页出现「AI 有 N 条新的 ›」快捷跳转，新内容卡片带「新」标记；打开笔记页即算已读（记在 `books/<书名>/seen.json`）。AI 划的线在阅读页里同样显示为蓝色，用户可以直接回复
- **新批注提醒（可选）**：用户新划线或回复后，停手一段时间（默认 8 分钟，每来一条重新计时）向你配置的 webhook POST 一条汇总，方便转发到聊天工具里戳 AI
- **传书即用**：上传 txt 自动识别编码（UTF-8/UTF-16/GBK/Big5）并拆章
- **智能拆章**：本地多种正则模式择优；识别失败自动请 DeepSeek 判断标题行（可选）；再失败按字数兜底
- **剧情笔记**：上传后 DeepSeek 逐章预读生成 150-250 字笔记（可选），供你的 AI 快速恢复剧情上下文，不必重读原文
- **DeepSeek 工作台🖥️**：外包 AI 的透明面板——每笔调用干了什么、花了几个 token、用了几秒、估算花费
- **主题美化 ⚙**：8 套风格主题（奶油小狗 / 抹茶老铺 / 慕夏花神 / 黑白甜 / 法式蓝笺 / 黏土 / 新拟物 / 拟态玻璃），每套带白天·夜间双模式、贴纸与底纹装饰、配套交互动效；我的划线色 / AI 回应后的划线色 / 双方批注气泡色四项独立配色，支持黑白 / 莫兰迪 / 多巴胺 / 薄荷曼波四色系 + 自由调色盘；阅读背景可选纯白 / 仿真书页 / 纯黑 / 自传图片；全部设置收进书架页右下角 ⚙ 面板，存在浏览器本地
- **翻页方式**：左右翻页（支持点击屏幕两侧翻页）或上下滑动无缝阅读——滚到章末自动衔接下一章
- 四位数密码门，零第三方依赖（纯 Python 标准库单文件），手机优先的界面

## 快速开始

```bash
git clone https://github.com/zzyyksl/reading-nook.git
cd reading-nook
cp config.example.json config.json   # 改成你自己的密码和称呼
python3 app.py                        # 需要 Python 3.10+
```

打开 `http://服务器IP:8000`（端口在 config.json 里改），输入密码，传一本 txt 就能读。

### systemd 常驻（可选）

```ini
# /etc/systemd/system/reading.service
[Unit]
Description=reading nook
After=network.target

[Service]
ExecStart=/usr/bin/python3 /path/to/app.py
Restart=always

[Install]
WantedBy=multi-user.target
```

## 配置（config.json）

| 字段 | 说明 | 默认 |
|---|---|---|
| `passcode` | 四位数登录密码 | `"0000"` |
| `port` | 监听端口 | `8000` |
| `subtitle` | 首页副标题（你们的名字） | — |
| `login_hint` | 密码提示语 | — |
| `user_name` | 粉色气泡署名 | `"我"` |
| `ai_name` | 蓝色气泡署名 | `"AI"` |
| `deepseek_api_key` | DeepSeek key，留空则禁用拆章兜底和剧情笔记 | `""` |
| `gardener_log` | 外部整理任务的 JSON 日志路径（没有就留空） | `""` |
| `notify_webhook` | 新批注提醒的 webhook 地址，留空则不发 | `""` |
| `notify_delay_seconds` | 用户停手多少秒后发提醒 | `480` |

## AI 伴读怎么接入

批注存在 `books/<书名>/annotations/<章号>.json`，格式：

```json
[{"id": "...", "anchor": "被划线的原文", "note": "用户的想法",
  "who": "user", "ts": "...", "replies": [{"who": "ai", "text": "AI的回应", "ts": "..."}]}]
```

你的 AI 往 `replies` 里 append 一条，用户刷新页面就能看到蓝色气泡。AI 也可以自己划线：往数组里加一条 `"who": "ai"` 的批注，用户点开就能回复。`ts` 请用 `YYYY-MM-DD HH:MM` 格式（和页面写入的一致），「新内容」提示靠它比较先后。辅助接口：

- `GET /api/pending` — 列出所有等 AI 回应的批注：用户的批注还没有回复，或最后一条回复是用户写的（需要密码 cookie：`rk=<passcode>`）
- `GET /api/allannos/<书名>` — 整本书的批注（按章），以及用户上次打开笔记页的时间 `seen`
- `GET /api/unseen` — 每本书里用户还没看过的 AI 新内容条数
- `GET /api/note/<书名>/<章号>` — 读 DeepSeek 生成的剧情笔记，快速恢复上下文

典型工作流：用户划线 → 在聊天工具里戳一下 AI → AI 读 pending → 看笔记/重读该章 → 写回 JSON → 用户刷新。

### 新批注提醒 webhook（可选）

在 config.json 里填 `notify_webhook` 后，用户每新建一条划线/想法或回复一条批注，计时器就重置；停手 `notify_delay_seconds` 秒后，向该地址 POST 一次汇总（`Content-Type: application/json`）：

```json
{"text": "我 在共读小屋留了新的划线/回复：《书名》3 条", "books": {"书名": 3}}
```

接到你自己的机器人/中转服务上，就不用每次手动戳 AI 了。留空则什么都不发。

## 安全说明

四位数密码只是防路人，不是安全边界（内置了同IP错5次封30分钟的暴力枚举限速，本机127.0.0.1豁免）。建议：跑在防火墙后只放行给自己、或套一层反代加 HTTPS 和真正的认证。不要用它存放敏感文档。

## License

MIT
