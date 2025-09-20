🚀 一、部署流程
1. 准备环境
a. 注册并配置 Telegram Bot
在 Telegram 中搜索 @BotFather，创建新机器人：

发送 /newbot

设置机器人名称和用户名（以 bot 结尾）

保存生成的 Bot Token（格式如 123456:ABC-DEF1234ghIkl-zyx57W2v1u123ew11）

b. 获取必要ID
ADMIN_UID：你的 Telegram User ID（可通过 @userinfobot 获取）

GROUP_ID：管理群组的 ID（需将机器人拉入群组，发送一条消息，访问 https://api.telegram.org/bot<TOKEN>/getUpdates 查看 chat.id）

CHANNEL_ID：频道 ID（格式为 @频道用户名 或 -100 开头的数字ID）

2. 创建 Cloudflare Worker
登录 Cloudflare Dashboard

进入 Workers & Pages → Create Worker

给 Worker 起名（如 tg-bot），点击 Deploy

进入 Worker 的 Settings → Variables，添加以下环境变量：

变量名	值	说明
ENV_BOT_TOKEN	你的 Bot Token	必填
ENV_BOT_SECRET	自定义字符串（用于 Webhook 验证）	可选但建议设置
ENV_ADMIN_UID	你的 User ID	必填
ENV_GROUP_ID	管理群组 ID	必填
ENV_CHANNEL_ID	频道 ID	必填
3. 创建 KV 命名空间
机器人使用 4 个 KV 存储：

KV 名称	用途
USER_TOPICS	用户话题、消息映射、广告、禁词等
USER_BLOCKS	用户屏蔽状态
KEYWORD	关键词回复配置
TIMES	定时任务（广告、自动删除）
创建方法：

进入 Workers & Pages → KV

创建四个命名空间，并绑定到 Worker：

在 Worker 的 Settings → Variables → KV Namespace Bindings 中绑定

4. 部署代码
进入 Worker 的 Edit Code

将你提供的完整代码粘贴到左侧编辑器中

点击 Save and Deploy

5. 设置 Webhook
访问以下 URL 以设置 Webhook（替换 YOUR_WORKER_URL）：

https://YOUR_WORKER_URL.workers.dev/registerWebhook
成功后你会看到类似：

Webhook设置成功: https://your-worker.workers.dev/webhook
若要移除 Webhook，访问：

https://YOUR_WORKER_URL.workers.dev/unRegisterWebhook

📖 二、使用教程
1. 用户命令（私聊机器人）
命令	说明
/start	开始使用，查看用户信息
/help	查看帮助信息
/me	查看我的信息（状态、模式等）
/mode	切换投稿/私聊模式
/vpn	免费VPN（需配置关键词回复）
/ch	汉化（需配置关键词回复）
2. 管理员命令（在群组或私聊中使用）
用户管理
命令	说明
/ban	屏蔽用户（回复或输入ID）
/unban	解除屏蔽
/info	查看用户信息
/list	列出所有用户
/biu <消息>	向所有用户广播消息
/status	查看统计信息
投稿审核
命令	说明
/approve	通过投稿（回复消息）
/approve_anon	匿名通过投稿
/reject	拒绝投稿
关键词回复
命令	说明
/add 关键词	添加关键词回复（在数据库组中回复消息）
/rm 关键词	删除关键词
/keywords	列出所有关键词
/toggle_keywords	开关关键词回复功能
群组管理
命令	说明
/addgroup <群组ID>	添加被管理群组
/rmgroup <群组ID>	移除被管理群组
/group	开关群组管理功能
/fdb 禁词1 禁词2	添加禁词
/no 禁词1 禁词2	删除禁词
/forbid	列出禁词
/toggle_fdb	开关禁词功能
广告管理
命令	说明
/ad 13:10 60m	添加广告（在广告组中回复消息）
/ads	列出广告
/del <消息ID>	删除广告
/toggle_ad	开关广告轮播
3. 功能说明
✅ 投稿模式
用户发送消息给机器人 → 消息转发到审核组

管理员可点击按钮审核（通过/匿名通过/拒绝）

✅ 私聊模式
用户消息直接转发到管理员私聊话题中

管理员可直接回复用户

✅ 关键词自动回复
在数据库组中回复消息：/add 关键词1 关键词2 [时间]

用户触发关键词时自动回复

✅ 广告轮播
在广告组中回复消息：/ad 13:10 60m

每天 13:10 发送一次，每隔 60 分钟轮播

✅ 禁词过滤
在管理群组中自动删除包含禁词的消息

❓ 如何设置关键词回复？
在数据库组中发送一条消息（如“欢迎使用”）

回复该消息：/add 欢迎 你好

用户发送“欢迎”或“你好”时，机器人会自动回复该消息

❓ 如何设置广告？
在广告组中发送一条广告消息

回复该消息：/ad 14:00 120m

每天14:00发送一次，每2小时轮播一次
