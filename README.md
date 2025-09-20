## 🚀 一、部署流程 ##
1. 准备环境 <br>
a. 注册并配置 Telegram Bot <br>
在 Telegram 中搜索 @BotFather，创建新机器人： <br>

发送 /newbot <br>

设置机器人名称和用户名（以 bot 结尾） <br>

保存生成的 Bot Token（格式如 123456:ABC-DEF1234ghIkl-zyx57W2v1u123ew11） <br>

b. 获取必要ID <br>
ADMIN_UID：你的 Telegram User ID（可通过 @userinfobot 获取） <br>

GROUP_ID：管理群组的 ID（需将机器人拉入群组，发送一条消息，访问 https://api.telegram.org/bot<TOKEN>/getUpdates 查看 chat.id） <br>

CHANNEL_ID：频道 ID（格式为 @频道用户名 或 -100 开头的数字ID） <br>

2. 创建 Cloudflare Worker <br>
登录 Cloudflare Dashboard <br>

进入 Workers & Pages → Create Worker <br>

给 Worker 起名（如 tg-bot），点击 Deploy <br>

进入 Worker 的 Settings → Variables，添加以下环境变量： <br>

变量名	值	说明 <br>
ENV_BOT_TOKEN	你的 Bot Token	必填 <br>
ENV_BOT_SECRET	自定义字符串（用于 Webhook 验证）	可选但建议设置 <br>
ENV_ADMIN_UID	你的 User ID	必填 <br>
ENV_GROUP_ID	管理群组 ID	必填 <br>
ENV_CHANNEL_ID	频道 ID	必填 <br>
3. 创建 KV 命名空间 <br>
机器人使用 4 个 KV 存储： <br>

KV 名称	用途 <br>
USER_TOPICS	用户话题、消息映射、广告、禁词等 <br>
USER_BLOCKS	用户屏蔽状态 <br>
KEYWORD	关键词回复配置 <br>
TIMES	定时任务（广告、自动删除） <br>
创建方法： <br>

进入 Workers & Pages → KV <br>

创建四个命名空间，并绑定到 Worker： <br>

在 Worker 的 Settings → Variables → KV Namespace Bindings 中绑定 <br>

4. 部署代码 <br>
进入 Worker 的 Edit Code <br>

将你提供的完整代码粘贴到左侧编辑器中 <br>

点击 Save and Deploy <br>

5. 设置 Webhook <br>
访问以下 URL 以设置 Webhook（替换 YOUR_WORKER_URL）： <br>

https://YOUR_WORKER_URL.workers.dev/registerWebhook <br>
成功后你会看到类似： <br>

Webhook设置成功: https://your-worker.workers.dev/webhook <br>
若要移除 Webhook，访问： <br>

https://YOUR_WORKER_URL.workers.dev/unRegisterWebhook <br>

📖 二、使用教程 <br>
1. 用户命令（私聊机器人） <br>
命令	说明 <br>
/start	开始使用，查看用户信息 <br>
/help	查看帮助信息 <br>
/me	查看我的信息（状态、模式等） <br>
/mode	切换投稿/私聊模式 <br>
/vpn	免费VPN（需配置关键词回复） <br>
/ch	汉化（需配置关键词回复） <br>
2. 管理员命令（在群组或私聊中使用） <br>
用户管理 <br>
命令	说明 <br>
/ban	屏蔽用户（回复或输入ID） <br>
/unban	解除屏蔽 <br>
/info	查看用户信息 <br>
/list	列出所有用户 <br>
/biu <消息>	向所有用户广播消息 <br>
/status	查看统计信息 <br>
投稿审核 <br>
命令	说明 <br>
/approve	通过投稿（回复消息） <br>
/approve_anon	匿名通过投稿 <br>
/reject	拒绝投稿 <br>
关键词回复 <br>
命令	说明 <br>
/add 关键词	添加关键词回复（在数据库组中回复消息） <br>
/rm 关键词	删除关键词 <br>
/keywords	列出所有关键词 <br>
/toggle_keywords	开关关键词回复功能 <br>
群组管理 <br>
命令	说明 <br>
/addgroup <群组ID>	添加被管理群组 <br>
/rmgroup <群组ID>	移除被管理群组 <br>
/group	开关群组管理功能 <br>
/fdb 禁词1 禁词2	添加禁词 <br>
/no 禁词1 禁词2	删除禁词 <br>
/forbid	列出禁词 <br>
/toggle_fdb	开关禁词功能 <br>
广告管理 <br>
命令	说明 <br>
/ad 13:10 60m	添加广告（在广告组中回复消息） <br>
/ads	列出广告 <br>
/del <消息ID>	删除广告 <br>
/toggle_ad	开关广告轮播 <br>
3. 功能说明 <br>
✅ 投稿模式 <br>
用户发送消息给机器人 → 消息转发到审核组 <br>

管理员可点击按钮审核（通过/匿名通过/拒绝） <br>

✅ 私聊模式 <br>
用户消息直接转发到管理员私聊话题中 <br>

管理员可直接回复用户 <br>

✅ 关键词自动回复 <br>
在数据库组中回复消息：/add 关键词1 关键词2 [时间] <br>

用户触发关键词时自动回复 <br>

✅ 广告轮播 <br>
在广告组中回复消息：/ad 13:10 60m <br>

每天 13:10 发送一次，每隔 60 分钟轮播 <br>

✅ 禁词过滤 <br>
在管理群组中自动删除包含禁词的消息 <br>

❓ 如何设置关键词回复？ <br>
在数据库组中发送一条消息（如“欢迎使用”） <br>

回复该消息：/add 欢迎 你好 <br>

用户发送“欢迎”或“你好”时，机器人会自动回复该消息 <br>

❓ 如何设置广告？ <br>
在广告组中发送一条广告消息 <br>

回复该消息：/ad 14:00 120m <br>

每天14:00发送一次，每2小时轮播一次 <br>
