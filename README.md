# 🧬 AI 3D 虚拟伴侣

> 一个会说话、可打断、能实时互动的 3D AI 伴侣网页应用。
>
> 用户只需输入魔珐星云 App ID/Secret 和 LLM API Key，即可拥有专属的 3D 虚拟 AI 助手。

---
<img width="1815" height="872" alt="image" src="https://github.com/user-attachments/assets/3f82a0ac-0083-4b03-94e5-ad24dbc48d9f" />

## 📺 效果演示

运行前端服务后，在设置面板中输入凭证，点击「连接并启动」，即可加载 3D 数字人。连接成功后可在右侧对话面板中与 AI 交流，虚拟人会通过语音、表情和动作进行回应。

---

## 📖 目录

- [技术原理](#技术原理)
- [前置准备](#前置准备)
- [项目结构](#项目结构)
- [快速开始](#快速开始)
- [核心实现详解](#核心实现详解)
  - [1. SDK 容器布局](#1-sdk-容器布局)
  - [2. SDK 初始化](#2-sdk-初始化)
  - [3. 大模型对话](#3-大模型对话)
  - [4. 数字人播报](#4-数字人播报)
  - [5. 状态管理](#5-状态管理)
  - [6. SSML 动作触发](#6-ssml-动作触发)
  - [7. 打断与队列管理](#7-打断与队列管理)
  - [8. 生命周期管理](#8-生命周期管理)
- [进阶功能](#进阶功能)
  - [自定义对话框位置](#自定义对话框位置)
  - [流式播报](#流式播报)
  - [音量控制与调试](#音量控制与调试)
- [常见问题](#常见问题)
- [参考资料](#参考资料)

---

## 技术原理

系统由三个核心层组成：

```
┌─────────────────────────────────────────────────┐
│                   用户界面                        │
│   设置面板  │  3D 数字人  │  聊天面板             │
└──────────────────┬──────────────────────────────┘
                   │
┌──────────────────▼──────────────────────────────┐
│              魔珐星云 SDK (XmovAvatar)            │
│   3D 渲染  │  TTS  │  口型同步  │  动作驱动       │
│   表情控制  │  状态管理  │  事件回调              │
└──────────────────┬──────────────────────────────┘
                   │
┌──────────────────▼──────────────────────────────┐
│              大模型 API (LLM)                     │
│   DeepSeek / OpenAI / 通义千问 等兼容 API         │
└─────────────────────────────────────────────────┘
```

- **魔珐星云具身驱动 SDK**：负责 3D 数字人的加载、渲染、语音合成、口型同步、表情和肢体动作
- **大模型 API**：负责语义理解和内容生成（兼容 OpenAI 协议）
- **前端 UI**：提供凭证配置、对话交互、状态展示

---

## 前置准备

### 1. 魔珐星云账号

1. 访问 [魔珐星云官网](https://xingyun3d.com) 注册并登录
2. 进入控制台 →「应用管理」→「驱动应用」→「创建新应用」
3. 按流程选择数字人形象、场景、音色和表演风格
4. 创建成功后，点击「接入 SDK」查看并复制 **App ID** 和 **App Secret**

### 2. LLM API Key

本应用兼容 OpenAI 协议的 API，支持：

| 平台 | API 地址 | 模型示例 |
|------|---------|---------|
| DeepSeek | `https://api.deepseek.com` | `deepseek-chat` |
| OpenAI | `https://api.openai.com/v1` | `gpt-4o`, `gpt-4o-mini` |
| 阿里通义千问 | `https://dashscope.aliyuncs.com/compatible-mode/v1` | `qwen-plus` |

---

## 项目结构

```
ai-3d/
├── index.html       # 主页面（完整应用，单页 HTML）
├── 3d.md            # 原始掘金文章（项目灵感来源）
├── content.md       # 魔珐星云 SDK 技术文档
└── README.md        # 本文件（项目说明文档）
```

整个应用是一个**单页 HTML 文件**，无需构建工具，只需一个静态服务器即可运行。

---

## 快速开始

### 使用 Python 启动

```bash
# 在项目目录下
cd ai-3d
python3 -m http.server 8080
```

### 使用 Node.js

```bash
npx serve .
```

### 使用 VS Code

安装 Live Server 插件，右键 `index.html` → Open with Live Server。

### 配置与使用

1. 打开浏览器访问 `http://localhost:8080`
2. 在设置面板中填写凭证：
   - **魔珐星云 App ID / App Secret**（必填）
   - **LLM API Key**（必填）
   - API 地址和模型（可选，默认使用 DeepSeek）
3. 点击「连接并启动」
4. 连接成功后，在聊天面板中输入问题，AI 会通过 3D 虚拟人语音回复

---

## 核心实现详解

### 1. SDK 容器布局

根据魔珐星云官方文档，SDK 容器需要遵循特定的布局规则。SDK 内部的 Canvas 使用 `absolute` 定位 + `scale` 变换实现自适应居中，因此外层容器必须是普通的块级元素。

```html
<!-- 正确的容器结构 -->
<div class="avatar-area">                <!-- flex: 1; position: relative -->
  <div id="sdk-container"></div>          <!-- width: 100%; height: 100%; position: relative -->
</div>
```

**关键 CSS：**

```css
.avatar-area {
  flex: 1;
  position: relative;
  overflow: hidden;
}

#sdk-container {
  width: 100%;
  height: 100%;
  position: relative;   /* 注意：不是 absolute，让 SDK 内部管理 Canvas 定位 */
  z-index: 2;
}
```

> **⚠️ 不要**给 `#sdk-container` 设置 `position: absolute`，否则会与 SDK 内部的 `left/top/scale` 自适应算法冲突，导致数字人偏移或不可见。

SDK 的 Canvas 自适应算法：

```css
/* SDK 内部动态生成的 Canvas 样式 */
position: absolute;
inset: 0px;
z-index: 100;
height: 100%;
object-fit: contain;
left: calc(-0.30029154518950435 * 100%);   /* 负偏移为缩放预留空间 */
top: calc(-0.29720853858784896 * 100%);     /* 负偏移为缩放预留空间 */
transform: scale(1.6059113300492611);       /* 放大填满容器 */
transform-origin: top left;
```

---

### 2. SDK 初始化

**引入 SDK：**

```html
<script src="https://media.xingyun3d.com/xingyun3d/general/litesdk/xmovAvatar@latest.js"></script>
```

**创建实例并初始化：**

```javascript
const sdk = new XmovAvatar({
  containerId: '#sdk-container',
  appId: 'your_appid',
  appSecret: 'your_appsecret',
  gatewayServer: 'https://nebula-agent.xingyun3d.com/user/v1/ttsa/session',

  onMessage(message) {
    console.log('SDK message:', message);
    // 错误码说明：
    // 10001 - 容器不存在
    // 10002 - Socket 连接异常
    // 10003 - 会话创建失败（检查 appId/appSecret）
    // 30001 - 背景加载失败
    // 40001 - 音频解码失败
  },

  onVoiceStateChange(status) {
    // 'voice_start' / 'start' - 开始播报
    // 'voice_end' / 'end'     - 播报结束
  },
});

await sdk.init({
  onDownloadProgress(progress) {
    console.log(`资源加载进度：${progress}%`);
  },
});
```

---

### 3. 大模型对话

应用使用 OpenAI 兼容 API 格式，默认对接 DeepSeek：

```javascript
async function callLLM(messages) {
  const url = 'https://api.deepseek.com/chat/completions';

  const res = await fetch(url, {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json',
      'Authorization': `Bearer ${API_KEY}`,
    },
    body: JSON.stringify({
      model: 'deepseek-chat',
      messages: messages,        // 包含 system + 对话历史
      temperature: 0.7,
      max_tokens: 2000,
      stream: false,
    }),
  });

  const data = await res.json();
  return data.choices?.[0]?.message?.content;
}
```

**对话流程：**

```
用户输入 → 添加到对话历史 → sdk.think() → 请求 LLM → 
隐藏思考态 → sdk.speak(reply) → 显示回复
```

---

### 4. 数字人播报

**播报完整文本：**

```javascript
sdk.speak('你好，我是你的 AI 伴侣！', true, true);
//                                     ↑is_start  ↑is_end
```

**流式播报（分段）：**

```javascript
sdk.speak('你好，我是你的 AI 伴侣，',  true,  false);  // 第一段
sdk.speak('我可以陪你聊天、讲故事，',  false, false);  // 中间段
sdk.speak('也可以帮你解答问题。',      false, true);   // 最后一段
```

> 流式播报建议先积累一小段文本再调用，避免文本过短导致频繁等待。前一轮播报结束后，建议通过 `interactiveidle()` 做一次状态切换再开始下一轮。

---

### 5. 状态管理

数字人在不同交互阶段应处于不同状态：

```javascript
// 待机状态（长时间等待）
sdk.idle();

// 互动待机（可用于打断当前播报）
sdk.interactiveidle();

// 思考状态（用户提问后，回复前）
sdk.think();

// 离线模式（不消耗积分）
sdk.offlineMode();
sdk.onlineMode();   // 切回在线模式
```

**典型交互状态流转：**

```
待机 → 用户输入 → 思考 → 播报 → 待机
                  ↑         ↓
                  └── 打断 ──┘
```

---

### 6. SSML 动作触发

通过 SSML 标签，数字人可以在说话时执行指定动作：

```javascript
// 打招呼动作
const ssml = `
<speak>
  <ue4event>
    <type>ka</type>
    <data><action_semantic>Hello</action_semantic></data>
  </ue4event>
  欢迎来到星云具身 3D 数字人平台，很高兴见到你。
</speak>
`;

sdk.speak(ssml, true, true);
```

支持的动作语义：`Hello`（打招呼）、`Dance`（跳舞）、`Welcome`（欢迎）、`Bye`（再见）等。

---

### 7. 打断与队列管理

当用户想打断当前播报时：

```javascript
function handleInterrupt() {
  sdk.interactiveidle();  // 立即停止播报，回到待机
}
```

在 LLM 对话中，需要实现「打断 → 等待就绪 → 播报新内容」的队列逻辑：

```javascript
const avatarManager = {
  _pendingText: null,

  interruptAndSpeak(text) {
    this._pendingText = text;
    sdk.interactiveidle();  // 打断当前播报
  },

  handleVoiceState(res) {
    const state = typeof res === 'string' ? res : res.state;

    if ((state === 'end' || state === 'idle') && this._pendingText) {
      const nextText = this._pendingText;
      this._pendingText = null;
      sdk.speak(nextText, true, true);
    }
  },
};
```

---

### 8. 生命周期管理

为防止页面刷新导致服务端连接未释放（10005 房间限流错误），需在页面关闭时销毁 SDK：

```javascript
window.addEventListener('beforeunload', () => {
  if (sdk) {
    sdk.destroy();    // 同步操作，通知服务端释放连接
  }
});
```

---

## 进阶功能

### 自定义对话框位置

对话框采用**浮动式面板**设计，支持多种位置自定义方式：

- **⇔ 按钮**：一键切换左侧/右侧
- **拖拽 ⠿ 手柄**：点击对话框顶部的拖拽手柄，自由移动位置
- **拖拽边缘**：调整对话框宽度（280px ~ 600px）
- **折叠按钮**：点击「—」折叠对话框，给虚拟人腾出完整空间
- **设置面板**：连接前可选择默认位置

**实现原理：**

```javascript
// 切换左右侧
function toggleChatSide() {
  chatPanel.classList.remove('pos-right', 'pos-left');
  chatPanel.classList.add('pos-' + newPos);
}

// 拖拽移动
dragHandle.addEventListener('mousedown', (e) => {
  // 记录起始位置，在 mousemove 中通过 transform: translate(x, y) 实现移动
});

// 拖拽调整宽度
resizeHandle.addEventListener('mousedown', (e) => {
  // 根据鼠标移动动态修改 chatPanel.style.width
});
```

### 流式播报

当大模型流式返回内容时，可以边接收边播报：

```javascript
async function streamAndSpeak(question) {
  const res = await fetch(url, {
    method: 'POST',
    body: JSON.stringify({ ...messages, stream: true }),
  });

  const reader = res.body.getReader();
  let buffer = '';
  let isFirst = true;

  while (true) {
    const { done, value } = await reader.read();
    if (done) break;

    buffer += decodeChunk(value);

    // 每积累一句就播报一段
    if (buffer.includes('。') || buffer.includes('！') || buffer.includes('？')) {
      const sentences = buffer.split(/(?<=[。！？])/);
      buffer = sentences.pop() || '';

      for (const sentence of sentences) {
        sdk.speak(sentence, isFirst, false);
        isFirst = false;
      }
    }
  }

  if (buffer.trim()) {
    sdk.speak(buffer, isFirst, true);  // 最后一段
  }
}
```

### 音量控制与调试

```javascript
// 音量控制（0 ~ 1）
sdk.setVolume(0);     // 静音
sdk.setVolume(0.5);   // 一半音量
sdk.setVolume(1);     // 最大音量

// 调试信息
sdk.showDebugInfo();  // 显示调试面板
sdk.hideDebugInfo();  // 隐藏调试面板
```

---

## 常见问题

### Q: 数字人加载失败，控制台报错 10003

检查 App ID 和 App Secret 是否正确，以及魔珐星云控制台中的应用配置是否完整。

### Q: 页面刷新后无法连接，报错 "10005 房间限流"

浏览器刷新时旧连接未及时释放。解决方法：
1. 等待 1~2 分钟自动恢复
2. 代码中已添加 `beforeunload` 事件处理，正常情况下不会触发此问题

### Q: 数字人加载后位置偏移或不可见

检查 `#sdk-container` 的 CSS：
- 必须是 `position: relative`（不是 `absolute`）
- 父容器 `.avatar-area` 必须是 `position: relative`
- 不要覆盖 SDK 内部 Canvas 的样式

### Q: 如何切换数字人形象？

在[魔珐星云控制台](https://xingyun3d.com)创建新的驱动应用，更换 App ID 和 App Secret 即可。

### Q: 对话框挡住了数字人？

点击顶栏的 **⇔** 按钮切换到另一侧，或拖动对话框顶部的 **⠿** 手柄移动位置。

---

## 参考资料

- [掘金原文：AI具身交互：实现一个会说话的3D虚拟伴侣](https://juejin.cn/post/7656050265523372032)
- [魔珐星云官网](https://xingyun3d.com)
- [魔珐星云开发者文档](https://xingyun3d.com/developers)
- [DeepSeek API 文档](https://platform.deepseek.com/)
- [OpenAI API 文档](https://platform.openai.com/docs/api-reference)

---

## License

MIT
---

## 友情链接
  <a href="https://linux.do" alt="LINUX DO"><img src="https://shorturl.at/ggSqS" /></a>

