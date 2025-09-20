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
