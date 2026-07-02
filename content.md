## 基础课 Lesson 1：呈现出数字人

### 1\. 前置准备

在使用 SDK 之前，需要先在魔珐星云平台获取身份验证凭据 (`AppID` 和 `AppSecret`) 并配置数字人形象

****步骤 1：****注册与登录 访问 [魔珐星云官网 (xingyun3d.com)](https://xingyun3d.com/)，注册并登录您的开发者账号

****步骤 2：****创建驱动应用

1\. 登录后进入 “控制台”，点击左侧导航栏的 “应用管理”  
2\. 在 “驱动应用” 列表下，点击 “创建新应用”  
3\. 按流程指引选择数字人的形象、场景、音色和表演

****步骤 3：****创建成功后，点击App密钥，您将看到以下关键信息，请妥善保存

1\. App ID：应用的唯一标识（用于 SDK `appId` 字段）  
2\. App Secret：应用密钥（用于 SDK `appSecret` 字段）

### 2\. 关键环节

1\. 定义一个装载数字人的容器（横屏宽高比=16:9，竖屏宽高比=9:16）

2\. 引入JS SDK：`<script src="https://media.xingyun3d.com/xingyun3d/general/litesdk/xmovAvatar@latest.js"></script>`

> 注：若私有化环境需要采用私有化地址并固定版本号

3\. 创建XmovAvatar SDK实例 `new XmovAvatar`

> 填入之前定义的数字人容器`containerId`  
> 填入魔珐星云驱动应用 `appid`、`appsecret`  
> 定义`onMessage`方法

4\. 初始化示例（`init`），显示数字人

### 3\. 预期效果

### 4\. 代码示例

<!DOCTYPE html\>  
<html lang\="zh-CN"\>  
  <head\>  
    <meta charset\="UTF-8" /\>  
    <meta name\="viewport" content\="width=device-width, initial-scale=1.0" /\>  
    <!\-- 页面标题 \--\>  
    <title\>我的第一个数字人</title\>  
    <style\>  
      <!\-- \[关键配置\] 容器比例需与您在控制台选择的"应用类型"保持一致 \--\>  
      #avatar\-container {  
        width: 800px;  
        height: 450px;  
        position: relative;  
      }  
       <!\-- 定义数字人呈现的容器样式 \--\>  
      #sdk {  
        width: 100%;  
        height: 100%;  
      }  
    </style\>  
  </head\>  
  <body\>  
    <!\-- 定义SDK容器 \--\>  
    <div id\="avatar-container"\>  
      <!\-- 数字人将在此容器中显示 \--\>  
      <div id\="sdk"\></div\>  
    </div\>  
  
    <!\-- 引入魔珐星云数字人SDK \--\>  
    <script src\="https://media.xingyun3d.com/xingyun3d/general/litesdk/xmovAvatar@latest.js"\></script\>  
  
    <!\-- 初始化SDK \--\>  
    <script\>  
      // 创建XmovAvatar SDK实例  
      const sdk \= new XmovAvatar({  
        // 指定数字人渲染的容器ID（CSS选择器格式）  
        containerId: "#sdk",  
        // 魔珐星云具身驱动应用App ID（用于身份验证）  
        appId: "your\_appid",  
        // 魔珐星云具身驱动应用App Secret（用于身份验证）  
        appSecret: "your\_appsecret",  
        // 数字人模型资源网关服务器地址  
        gatewayServer: "https://nebula-agent.xingyun3d.com/user/v1/ttsa/session",  
        // 数字人消息通知回调函数  
        onMessage: (message) \=> {  
          console.log("SDK message:", message);  
        },  
      });  
  
      // 初始化SDK实例并设置回调函数  
      sdk.init({  
        // 资源下载进度回调函数  
        onDownloadProgress: (progress) \=> {  
          console.log("资源下载进度:", progress + "%");  
        },  
      });  
    </script\>  
  </body\>  
</html\>

  

## 基础课 Lesson 2：数字人状态切换和播报

### 1\. 关键环节

1\. 定义数字人状态切换的方法

2\. 定义触发机制（本demo示意为按钮点击触发）

### 2\. 预期效果

### 3\. 代码示例

<!DOCTYPE html\>  
<html lang\="zh-CN"\>  
  
<head\>  
    <meta charset\="UTF-8"\>  
    <meta name\="viewport" content\="width=device-width, initial-scale=1.0"\>  
    <!\-- 页面标题 \--\>  
    <title\>我的第一个数字人</title\>  
    <style\>  
        <!\-- 页面基本样式 \--\>  
        body {  
            font\-family: Arial, sans\-serif;  
            margin: 0;  
            padding: 20px;  
            background\-color: #f5f5f5;  
        }  
  
        <!\-- 主容器样式 \--\>  
        .container {  
            display: flex;  
            flex\-direction: column;  
            gap: 20px;  
            max\-width: 1200px;  
            margin: 0 auto;  
        }  
  
        <!\-- SDK容器样式 \- 数字人显示区域 \--\>  
        #sdk {  
            width: 800px;  
            height: 450px;  
            background\-color: #000;  
            border: 2px solid #ddd;  
            border\-radius: 8px;  
        }  
  
        <!\-- 控制面板样式 \--\>  
        .control\-panel {  
            background\-color: #fff;  
            padding: 20px;  
            border\-radius: 8px;  
            box\-shadow: 0 2px 10px rgba(0, 0, 0, 0.1);  
        }  
  
        <!\-- 控制按钮样式 \--\>  
        .control\-btn {  
            background\-color: #4CAF50;  
            color: white;  
            border: none;  
            padding: 10px 20px;  
            text\-align: center;  
            text\-decoration: none;  
            display: inline\-block;  
            font\-size: 16px;  
            margin: 4px 2px;  
            cursor: pointer;  
            border\-radius: 4px;  
        }  
  
        .control\-btn:hover {  
            background\-color: #45a049;  
        }  
    </style\>  
</head\>  
  
<body\>  
    <!\-- 主容器 \- 包含数字人显示区域和控制面板 \--\>  
    <div class\="container"\>  
        <!\-- SDK容器 \- 数字人将在此区域渲染 \--\>  
        <div id\="sdk"\></div\>  
  
        <!\-- 状态控制区域 \--\>  
        <div class\="control-panel"\>  
            <h3\>状态控制</h3\>  
            <!\-- 离线模式按钮 \--\>  
            <button class\="control-btn" onclick\="interactiveidle()"\>interactiveidle（等待互动/打断）</button\>  
            <!\-- 离线模式按钮 \--\>  
            <button class\="control-btn" onclick\="speak()"\>speak（说话）</button\>  
        </div\>  
    </div\>  
  
    <!\-- 引入魔珐星云数字人SDK \--\>  
    <script src\="https://media.xingyun3d.com/xingyun3d/general/litesdk/xmovAvatar@latest.js"\></script\>  
  
    <!\-- 初始化SDK \--\>  
    <script\>  
        // 创建XmovAvatar SDK实例  
        const sdk \= new XmovAvatar({  
            // 指定数字人渲染的容器ID（CSS选择器格式）  
            containerId: '#sdk',  
            // 魔珐星云具身驱动应用App ID（用于身份验证）  
            appId: 'your\_appid',  
            // 魔珐星云具身驱动应用App Secret（用于身份验证）  
            appSecret: 'your\_appsecret',  
            // 数字人模型资源网关服务器地址  
            gatewayServer: 'https://nebula-agent.xingyun3d.com/user/v1/ttsa/session',  
            // 数字人消息通知回调函数  
            onMessage: (message) \=> {  
                console.log('SDK message:', message);  
            }  
        });  
  
        // 初始化SDK实例并设置回调函数  
        sdk.init({  
            // 资源下载进度回调函数  
            onDownloadProgress: (progress) \=> {  
                console.log('资源下载进度:', progress + '%');  
            }  
        }).catch(error \=> {  
            console.error('SDK初始化失败:', error);  
        });  
  
        // 等待互动/打断函数  
        function interactiveidle() {  
            if (sdk) {  
                sdk.interactiveidle();  
            } else {  
                console.error('SDK未初始化');  
            }  
        }  
        function speak() {  
            if (sdk) {  
                sdk.speak("欢迎使用魔珐星云具身驱动sdk", is\_start \= true, is\_end \= true);  
            } else {  
                console.error('SDK未初始化');  
            }  
        }  
    </script\>  
</body\>  
</html\>

### 4\. 详细方法

类型

中文名

英文名

终端对应功能说明

API方法

参数

返回值

连接与销毁

初始化连接

init

初始化数字人实例，连接服务，拉取必要资源

`init(config)`

****config**** (对象，必填): 初始化配置项。

包含以下属性：

**`**onDownloadProgress**`** (函数，可选):接收一个`number`用于监听资源加载进度。

****Promise****

初始化完成的 Promise 对象

销毁

destroy

关闭数字人连接

`destroy()`

无

无

模式切换

离线模式

offlineMode

进入离线模式，数字人循环播放缓存视频

`offlineMode()`

此状态下不消耗积分

无

无

在线模式

onlineMode

从离线模式回到在线模式

`onlineMode()`

无

无

状态切换

待机等待

idle

数字人长时间等待

`idle()`

无

无

待机互动

interactive\_idle

交互状态切换前的循环状态

`interactiveidle()`

可用于打断当前状态，回到待机状态

无

无

倾听

listen

用户输入语音，数字人处于倾听状态

`listen()`

无

无

思考

think

用户提问后，未开始回复的状态

`think()`

无

无

数字人说话

speak

控制虚拟人说话

`speak(ssml, is_start, is_end)`

详见 Speak详解

`ssml`：String

`isStart`：boolean

`isEnd`：boolean

无

## 进阶课 Lesson 3：方法详解

### 1\. 方法详解

#### ****1.1 代码配置说明****

在初始化 SDK 时，除了基础的鉴权参数外，最重要的部分是配置 `proxyWidget`（代理渲染器）。通过配置该参数，您可以拦截 SDK 内部的标准事件（如字幕、图片、视频信令），从而实现自定义的 UI 交互逻辑。

以下是一个包含常用 Widget 事件拦截的初始化代码示例：

#### 1.2 初始化方法

const sdk \= new XmovAvatar({  
  containerId: '#sdk',  
  appId: 'your\_app\_id',  
  appSecret: 'your\_app\_secret',  
  gatewayServer: 'https://nebula-agent.xingyun3d.com/user/v1/ttsa/session',  
  
  // 代理渲染器 (proxyWidget)   
  // 配合 SDK 默认渲染逻辑使用。在此处定义的方法会拦截 SDK 的默认行为。  
  // 可以在此处实现自定义的 UI 逻辑（如：弹窗、视频播放器、字幕样式等）。  
  proxyWidget: {  
    // 1. 拦截图片事件  
    // 【注意】服务端返回的图片字段名为 image，而不是 url  
    "widget\_pic": (data) \=> {  
      console.log("收到图片指令，地址:", data.image);  
      // TODO: 在此处编写您的自定义图片展示逻辑，例如弹出一个 img 标签  
    },  
  
    // 2. 拦截视频事件  
    // SDK 默认不包含视频播放器 UI，必须在此处自行实现  
    "widget\_video": (data) \=> {  
      console.log("收到视频指令:", data);  
      // data.src: 视频地址  
      // data.action: "play" 或 "stop"  
    },  
  
    // 3. 拦截 PPT 幻灯片事件  
    // SDK 默认不包含 PPT 渲染逻辑，必须在此处自行实现  
    "widget\_slideshow": (data) \=> {  
      console.log("收到 PPT 指令:", data);  
    },  
  
    // 4. 拦截字幕 (可选)  
    // SDK 默认有黑底白字字幕。如果您需要自定义样式，可以在此拦截  
    "subtitle\_on": (data) \=> {  
      console.log("显示字幕:", data.text);  
    },  
    // 隐藏字幕  
    "subtitle\_off": (data) \=> {  
      console.log("隐藏字幕");  
    }  
  },  
  
  // 网络状态回调  
  onNetworkInfo(networkInfo) {  
    console.log('networkInfo:', networkInfo);  
  },  
  
  // SDK 消息回调  
  onMessage(message) {  
    console.log('SDK message:', message);  
  },  
  
  // 状态变更回调  
  onStateChange(state) {  
    console.log('SDK State Change:', state);  
  },  
  
  enableLogger: false, // 是否在控制台打印 SDK 内部日志  
});

> ☝️ ****注意：**** SDK 支持多种 Widget 事件，您可以在 `proxyWidget` 中进行拦截。完整支持列表如下：

****Widget Key (事件名)****

****功能说明****

****触发时机****

****data 参数详解****

****备注****

`subtitle_on`

显示字幕

数字人开始说话时

`{ text: "字幕内容", axis_id: 1000 }`

SDK 默认会渲染黑底白字字幕。拦截此事件可自定义字幕样式或位置。

`subtitle_off`

隐藏字幕

一句话说完时

`{ type: "subtitle_off" }`

此时应清空或隐藏字幕容器。

`widget_pic`

展示图片

剧本配置图片动作时

`{ image: "图片URL", axis_id: 1 }`

****注意****：图片地址字段名为 `image`，非 url。

`widget_video`

播放视频

剧本配置视频动作时

`{ src: "视频URL", action: "play/stop" }`

SDK 默认****无****视频播放器 UI，****必须****通过 proxyWidget 自行实现。

`widget_slideshow`

PPT 展示

剧本配置 PPT 动作时

`{ url: "图片URL", page: 1, ... }`

SDK 默认****无**** PPT 渲染逻辑，****必须****通过 proxyWidget 自行实现。

`widget_text`

纯文本卡片

剧本配置文本卡片时

`{ text: "文本内容", ... }`

这是一个隐藏能力，SDK 默认无渲染逻辑，需自行实现。

#### 1.3 渲染优先级与覆盖机制

****在使用 SDK 进行界面定制时，请注意**** `proxyWidget` ****和**** `onWidgetEvent` ****的区别与优先级，以免造成页面显示异常。****

> ****💡**** 核心机制说明：  
> 1\. `proxyWidget` ****(局部拦截)****  
> 含义：您只想修改 SDK 默认行为的一小部分（例如：去掉字幕、修改图片点击效果）。  
> 结果：SDK 会保留默认的背景图、视频播放器等基础功能，只执行您在 `proxyWidget` 里定义的逻辑。  
> 2\. `onWidgetEvent` ****(全量接管)****  
> 含义：具有最高优先级。一旦配置了此回调，SDK 会认为您需要完全自定义 UI。  
> 结果：SDK 的****默认渲染逻辑会被覆盖****。这意味着默认的字幕条、背景图将不再自动显示，您必须在此回调中手动处理所有渲染逻辑。

> ⚠️ ****常见避坑：**** 如果您在初始化时配置了 `onWidgetEvent`（即使只是打印了一行日志），却发现****背景图消失了****或****字幕不显示****，这是正常现象。 ****建议：****除非需要重写整个播放器 UI，否则建议优先使用 `proxyWidget`。

### 2\. 小技巧

#### 2.1 调整字幕效果

****2.1.1 去掉字幕****

<script\>  
    // 创建XmovAvatar SDK实例  
    const sdk \= new XmovAvatar({  
        // 指定数字人渲染的容器ID  
        containerId: '#sdk',  
        // 鉴权信息  
        appId: 'your\_appid',  
        appSecret: 'your\_appsecret',  
        gatewayServer: 'https://nebula-agent.xingyun3d.com/user/v1/ttsa/session',  
        // 消息回调  
        onMessage: (message) \=> {  
            console.log('SDK message:', message);  
        },  
        // 通过 proxyWidget 覆盖默认行为  
        // 原理：SDK 默认会渲染字幕，通过传入空的箭头函数 () => {}，  
        // 相当于拦截了字幕事件但不做任何处理，从而实现"隐藏字幕"的效果。  
        proxyWidget: {  
            "subtitle\_on": (data) \=> {  
                // 空函数：不执行任何渲染逻辑  
            },  
            "subtitle\_off": (data) \=> {  
                // 空函数  
            }  
        }  
    });  
</script\>

****2.1.2 复写字幕样式****

1\. HTML结构改动

在原有的SDK容器内添加了字幕显示区域：

<!\-- SDK容器 \- 数字人将在此区域渲染 \--\>  
<div id\="sdk"\>  
    <!\-- 字幕显示区域 \--\>  
    <div id\="subtitle-container"\>  
        <div id\="subtitle-text"\></div\>  
    </div\>  
</div\>

 改动说明：

在 # sdk 容器内部添加了一个字幕容器 # subtitle-container

字幕容器内包含一个字幕文本元素 # subtitle-text

2\. CSS样式改动

a. 字幕容器样式 ( # subtitle-container )

#subtitle\-container {  
    position: absolute;  
    bottom: 20px;  
    left: 0;  
    right: 0;  
    text\-align: center;  
    z\-index: 9999;  
    padding: 10px;  
}

各项属性说明：

· position: absolute; - 设置为绝对定位，相对于父元素( # sdk )进行定位  
· bottom: 20px; - 距离容器底部20像素  
· left: 0; right: 0; - 左右边缘与容器对齐，配合text-align实现居中  
· text-align: center; - 内容水平居中  
· z-index: 9999; - 层级设置为最高，确保字幕在最上层显示  
· padding: 10px; - 内边距，防止字幕紧贴容器边缘

b. 字幕文本样式 ( # subtitle-text )

#subtitle\-text {  
    display: inline\-block;  
    background\-color: rgba(0, 0, 0, 0.7);  
    color: white;  
    font\-size: 18px;  
    padding: 10px 20px;  
    border\-radius: 20px;  
    max\-width: 80%;  
    word\-wrap: break\-word;  
    opacity: 0;  
    transition: opacity 0.3s ease\-in\-out;  
}  
  
#subtitle\-text.show {  
    opacity: 1;  
}

各项属性说明：

display: inline-block; - 行内块元素，使容器宽度适应内容  
background-color: rgba(0, 0, 0, 0.7); - 半透明黑色背景，提高可读性  
color: white; - 白色文字  
font-size: 18px; - 字体大小  
padding: 10px 20px; - 内边距，使文字不紧贴边框  
border-radius: 20px; - 圆角边框，美观  
max-width: 80%; - 最大宽度限制，防止过长  
word-wrap: break-word; - 长单词自动换行  
opacity: 0; - 默认隐藏状态（透明度为0）  
transition: opacity 0.3s ease-in-out; - 透明度变化动画效果  
.show 类： opacity: 1; - 显示状态（完全不透明）

3\. JavaScript 功能改动

更新了SDK配置中的 proxyWidget 回调函数：

proxyWidget:{  
    "subtitle\_on": (data) \=> {  
        console.log("subtitle\_on", data);  
        // 显示字幕  
        const subtitleElement \= document.getElementById('subtitle-text');  
        if (subtitleElement && data && data.text) {  
            subtitleElement.textContent \= data.text;  
            subtitleElement.classList.add('show');  
        }  
    },  
    "subtitle\_off": (data) \=> {  
        console.log("subtitle\_off", data);  
        // 隐藏字幕  
        const subtitleElement \= document.getElementById('subtitle-text');  
        if (subtitleElement) {  
            subtitleElement.classList.remove('show');  
        }  
    },  
}

功能说明：

subtitle\_on 回调：

当SDK触发字幕开始事件时调用  
获取字幕文本元素  
将传入的数据中的文本内容设置为字幕内容  
添加 show 类，使字幕显示（通过CSS过渡动画）

subtitle\_off 回调：

当SDK触发字幕结束事件时调用  
获取字幕文本元素  
移除 show 类，使字幕隐藏（通过CSS过渡动画）

#### 2.2 数字人流式播报（模拟大模型流式输入）

function speak() {  
    if (sdk) {  
        sdk.speak("欢迎", is\_start \= true, is\_end \= false);  
        sdk.speak("使用魔珐", is\_start \= false, is\_end \= false);  
        sdk.speak("星云具身", is\_start \= false, is\_end \= false);  
        sdk.speak("驱动sdk", is\_start \= false, is\_end \= true);  
    } else {  
        console.error('SDK未初始化');  
    }  
}

#### 2.3 实现说话后打断紧接再一次说话调用

****场景描述**** 在 LLM（大语言模型）对话场景中，用户可能会随时打断数字人的发言。由于 SDK 的语音指令是异步执行的，若在发送“打断指令”后立即发送“新语音指令”，可能会因上一条语音流未释放而导致指令冲突。因此，需要建立一种****“指令排队机制”****，确保在数字人彻底停止当前动作（进入 `end` 或 `idle` 状态）后，再执行下一条播报。

****2.3.1 交互时序逻辑****

****1\. 入队****：先将下一句要播放的文本保存到一个“待播报变量”中。  
****2\. 打断****：调用 `sdk.interactiveidle()` 停止当前播报。  
****3\. 监听****：在 `onVoiceStateChange` 回调中持续监听状态变化。  
****4\. 消费****：一旦检测到状态变为 `end` 或 `idle` 且存在待播报文本时，立即执行 `sdk.speak()`。

****2.3.2 代码实现****

建议在业务层封装 `SpeechManager` 来统一管理语音状态。

// 最佳实践示例：封装语音状态管理器  
const avatarManager \= {  
    \_pendingText: null, // 内部变量：暂存待播报文本  
    // 业务调用入口：打断并插话  
    // @param {string} text - 需要立即播放的新内容  
    interruptAndSpeak(text) {  
        this.\_pendingText \= text; // 1. 入队：暂存新指令  
        sdk.interactiveidle();    // 2. 信号：发送打断指令  
    },  
    // 状态监听器 (需绑定到 SDK 的 onVoiceStateChange)  
    handleVoiceState(res) {  
        // 状态标准化处理  
        const state \= (typeof res \=== 'string') ? res : (res.state 

 res.data);  
        // 3. 消费：仅在上一指令彻底结束后（end/idle），才执行新指令  
        if ((state \=== 'end' 

 state \=== 'idle') && this.\_pendingText) {  
            console.log("\[Manager\] 状态重置完毕，执行插话...");  
            const nextText \= this.\_pendingText;  
            this.\_pendingText \= null; // 清空队列  
            // 4. 执行：发送新语音  
            sdk.speak(nextText, true, true);  
        }  
    }  
};  
  
// 初始化 SDK 时绑定监听  
const sdk \= new XmovAvatar({  
    // ... 其他配置  
    onVoiceStateChange: (res) \=> avatarManager.handleVoiceState(res)  
});

#### 2.4 自适应设备屏幕

当JS SDK被使用在移动端时，为了视觉效果，往往需要全屏显示数字人。此时，设备屏幕无法不固定，但直接使用sdk的时候，会出现数字人不居中，有位置偏移的问题。

****2.4.1 SDK Canvas 布局机制说明****

模版

![](https://media.youyan.xyz/youyan/platform_operation/154113_f3e2dc3e7a134aef9f4e6e.png)

****2.4.2 样式解析****

> 这个是SDK通过读的配置，默认生成样式

SDK 生成的 canvas 元素使用以下样式实现自适应布局：

position: absolute;  
inset: 0px;  
z\-index: 100;  
height: 100%;  
object\-fit: contain;  
left: calc(\-0.30029154518950435  *100%);  
top: calc(\-0.29720853858784896*100%);  
transform: scale(1.6059113300492611, 1.6059113300492611);  
transform\-origin: top left;

****2.4.3 工作原理图解****

┌─────────────────────────────────────────────────────────────┐  
│                    父容器 (.sdk\-container)                    │  
│                    宽度: 100%, 高度: 100%                     │  
│                                                               │  
│  ┌─────────────────────────────────────────────────────┐   │  
│  │  步骤1: inset: 0 (初始状态)                           │   │  
│  │  Canvas 占满整个父容器                                 │   │  
│  │  ┌──────────────────────────────────────────────┐   │   │  
│  │  │                                              │   │   │  
│  │  │            Canvas (100% × 100%)             │   │   │  
│  │  │                                              │   │   │  
│  │  └──────────────────────────────────────────────┘   │   │  
│  └─────────────────────────────────────────────────────┘   │  
│                                                               │  
│  ┌─────────────────────────────────────────────────────┐   │  
│  │  步骤2: 负偏移 (left: \-30%, top: \-29.7%)            │   │  
│  │  向左上角移动，为后续缩放做准备                       │   │  
│  │                                                       │   │  
│  │  ┌──────────────────────────────────────────────┐   │   │  
│  │  │                                              │   │   │  
│  │  │         Canvas (向左上角偏移)                │   │   │  
│  │  │                                              │   │   │  
│  │  └──────────────────────────────────────────────┘   │   │  
│  └─────────────────────────────────────────────────────┘   │  
│                                                               │  
│  ┌─────────────────────────────────────────────────────┐   │  
│  │  步骤3: 缩放 (scale: 1.606, origin: top left)        │   │  
│  │  从左上角放大，填满容器并保持比例                     │   │  
│  │  ┌──────────────────────────────────────────────┐   │   │  
│  │  │                                              │   │   │  
│  │  │                                              │   │   │  
│  │  │     Canvas (放大1.606倍，从左上角开始)        │   │   │  
│  │  │                                              │   │   │  
│  │  │                                              │   │   │  
│  │  └──────────────────────────────────────────────┘   │   │  
│  └─────────────────────────────────────────────────────┘   │  
└─────────────────────────────────────────────────────────────┘

****2.4.4 详细说明****

SDK 通过****三步变换****实现数字人画面的自适应居中与填充，确保在不同屏幕比例下都能正确显示。

****第一步：占满容器**** (`inset: 0px`) Canvas 元素首先通过绝对定位占满整个父容器（100% × 100%），建立基础布局框架。此时元素完全覆盖父容器区域。

****第二步：负向偏移**** (`left: -30%, top: -29.7%`) 为了后续的缩放操作，Canvas 向左上角方向移动。负的 left 和 top 值将元素向左上方偏移约30%和29.7%，为放大操作预留空间。这个偏移量是 SDK 根据容器与数字人原始比例（9:16）的差异动态计算的。

****第三步：缩放变换**** (`scale: 1.606, transform-origin: top left`) 最关键的一步：从左上角（`transform-origin: top left`）开始，将 Canvas 放大1.606倍。由于之前已经向左上角偏移，放大后的元素能够：

保持数字人的原始宽高比（9:16）  
完全填满父容器（类似 `object-fit:` contain 的效果）  
超出容器的部分被 `overflow: hidden` 裁剪

****最终效果****：数字人画面在保持比例的同时填满容器，实现视觉上的居中与完整显示。这种方案比单纯的 CSS `object-fit` 更灵活，允许 SDK 根据实际渲染尺寸进行精确计算和调整。

****2.4.5 数值含义****

> 这些值都是通过从星云应用管理的选择模版后生成的位置

left: -30%：向左偏移容器宽度的30%，为放大预留空间  
top: -29.7%：向上偏移容器高度的29.7%，为放大预留空间  
scale: 1.606：放大倍数，确保放大后能填满容器  
transform-origin: top left：变换原点在左上角，保证缩放从左上角开始

 这些数值是 SDK 根据以下公式动态计算的：

偏移量 \= (容器比例 \- 数字人比例) / 2  
缩放比 \= max(容器宽度/数字人宽度, 容器高度/数字人高度)

  

****2.4.6 为什么需要这样？****

****1\. 保持比例****：数字人原始比例是 9:16（竖屏），但容器可能是其他比例  
****2\. 填满屏幕****：需要类似 `object-fit: cover` 的效果，确保画面充满容器  
****3\. 精确控制****：SDK 需要根据实际渲染尺寸进行精确计算，而不是依赖 CSS 的自动处理  
****4\. 居中显示****：通过负偏移 + 缩放的组合，确保数字人主体在视觉上居中

****2.4.7 与 CSS object-fit 的对比****

****特性****

****SDK 方案****

****CSS object-fit****

控制精度

精确到像素级

浏览器自动处理

计算方式

SDK 动态计算

CSS 自动计算

兼容性

需要父容器居中

浏览器原生支持

灵活性

可自定义算法

固定几种模式

SDK 方案的优势在于可以根据实际渲染内容进行更精确的控制和优化。

****2.4.8 应用加工****

上述原理解析了 SDK 内部的默认布局逻辑。但在实际应用开发中，为了确保数字人画面在各种屏幕尺寸下都能保持水平居中且自适应缩放，****推荐在应用层直接控制容器尺寸****：只需确保外层包装容器 `.avatar-render` 和 SDK 实际挂载的节点 `.sdk-container` 都设置为****撑满整个父容器（视口）****。这样配合 SDK 内部的自适应算法，即可实现完美的居中效果。

****应用层只需要对应的**** ****html**** ****结构和**** ****CSS**** ****样式是这样既可****

<div class\="avatar-render"\>  
    <div id\="sdk" class\="sdk-container"\></div\>  
</div\>  
  
<style\>  
    / *确保父容器占满空间或具有弹性布局* /  
    .avatar\-render {  
        flex: 1;  
        position: relative;  
        width: 100%;  
        height: 100%;  
    }  
  
    / *SDK 容器撑满父级* /  
    .sdk\-container {  
        width: 100%;  
        height: 100%;  
    }  
</style\>

#### 2.5 背景配置与切换

SDK 渲染出的数字人自带透明通道。因此，无需在 SDK 内部设置背景，而是通过 ****HTML/CSS 图层叠加**** 的方式，在数字人下方放置一张图片或视频作为背景。

****2.5.1 实现原理****

本方案采用“绝对定位”将 UI 分为三层：

****底层 (z-index: 0)****：背景层（图片 `<img>` 或视频 `<video>`）。  
****中间层 (z-index: 1)****：SDK 渲染层（数字人 Canvas）。  
****顶层 (z-index: >1)****：交互层（按钮、字幕等）。

****2.5.2 代码实现****

1\. HTML 结构改造

在 `containerId` 指定的容器内，预先放置一个背景标签：

<div id\="sdk" style\="position: relative; width: 800px; height: 450px;"\><img id\="bg-image"   
         src\="https://images.unsplash.com/photo-1618221195710-dd6b41faaea6?auto=format&fit=crop&w=1600&q=80"   
         style\="position: absolute; width: 100%; height: 100%; object-fit: cover; z-index: 0;"   
         alt\="背景" /\></div\>

2\. 监听服务端指令自动切换 

`proxyWidget`剧本中经常会有“切换背景”的动作（对应事件 `widget_pic`）。需在初始化时拦截该事件，并更新底层的图片。

const sdk \= new XmovAvatar({  
    // ... 其他基础配置 ...  
     
    // 使用 proxyWidget 接管背景切换逻辑  
    proxyWidget: {  
        "widget\_pic": (data) \=> {  
            // 1. 获取服务端下发的图片地址 (字段名为 image)  
            const bgUrl \= data.image;   
              
            // 2. 找到背景图元素并替换 src  
            const bgElement \= document.getElementById('bg-image');  
            if (bgElement && bgUrl) {  
                bgElement.src \= bgUrl;  
                bgElement.style.display \= 'block'; // 确保图片显示  
                console.log("背景已切换为:", bgUrl);  
            }  
        }  
    }  
});

****⚠️注意事项****

在配置背景时，请特别注意以下 3 个细节，以避免画面显示异常：

> ****1\. 图层层级 (Z-index)****  
> 背景图的 CSS `z-index` 必须设置为 ****0**** 或更小，确保留出更高的层级给 SDK Canvas（默认为 1），否则背景图会覆盖住数字人。  
> ****2\. 图片链接安全性 (HTTPS)****  
> 如果您的网页运行在 HTTPS 环境下，背景图片的链接也必须是 ****HTTPS**** 开头。  
> ****关键点****：链接必须直接指向图片文件（如以 `.jpg`、`.png` 结尾），不能使用跳转的网页地址。  
> ****3\. 纯色背景实现****  
> SDK 不支持直接通过 API 设置背景色。如果需要纯色背景，请直接操作 DOM：将底层的 `<img>` 标签隐藏 (`display: none`)，然后给父容器 `#sdk` 添加 `background-color` 样式即可。  
> ****4\. 本地 / 项目内图片引用****  
> ****静态引用****：在 HTML 初始化时，您可以直接使用****相对路径****引用项目文件夹内的图片（****注意：**** 严禁使用硬盘绝对路径如 `C:/Users/...`，浏览器会因安全限制拦截）。

## 进阶课 Lesson 4：安全鉴权与生产环境避坑

### ****1\. 开发调试避坑指南****

#### 1.1 解决 "10005 房间限流" 报错

在开发调试过程中，频繁刷新页面可能会导致控制台出现以下红色报错，导致数字人无法加载：

`[SDK Error] [ResourceManager] Error: 10005, 房间限流，请关闭之前的房间后再连接`

****问题成因：****当您在浏览器中直接****刷新页面****或关闭标签页时，浏览器端的 WebSocket 连接会立即断开。但是，****服务端的旧连接****往往需要等待几十秒的心跳超时才会彻底释放。 如果您在旧连接释放前立刻再次初始化 SDK（即刷新页面），服务端会认为同一个 Session 同时开启了多个房间，从而触发并发限制规则，拒绝新的连接。

****解决方案：****为了避免此问题，需要在浏览器页面“即将关闭/刷新”的一瞬间，主动向 SDK 发送销毁指令。

请在您的 HTML 代码 `<script>` 标签的最末尾，添加以下 ****生命周期监听代码****：

// 监听浏览器关闭/刷新事件  
// 在页面卸载前强制销毁 SDK，通知服务端立刻释放连接资源  
window.addEventListener('beforeunload', () \=> {  
    if (sdk) {  
        // destroy() 是同步操作，能确保在连接断开前发出“退出房间”的信号  
        sdk.destroy();   
    }  
});

****应急处理**** 如果您已经遇到了该报错且无法连接：

****方法一****：等待约 1~2 分钟，让服务端的旧连接自动超时断开，即可恢复正常。  
****方法二****：在代码中添加一个手动的“断开连接”按钮（调用 `sdk.destroy()`），养成“先点击断开，再刷新页面”的调试习惯。

#### 1.2 优雅断开连接

除了处理页面刷新导致的异常外，在业务逻辑中，通常还需要提供主动断开机制，以便在用户退出、路由跳转或重置状态时正确释放资源。

****核心实现代码****

建议在全局封装一个通用的销毁函数，统一管理断开逻辑：

// 统一销毁入口  
// 作用：断开 WebSocket，停止推流，清理内存  
function destroySDK() {  
    if (sdk) {  
        try {  
            console.log('正在断开 SDK 连接...');  
            sdk.destroy(); // SDK 提供的标准销毁方法  
            sdk \= null;    // 清空实例引用，防止内存泄漏  
        } catch (e) {  
            console.error('断开连接异常:', e);  
        }  
    }  
    if (typeof window !== 'undefined') {  
        try {  
            (window).LiteSDK \= null;  
        } catch (e) {  
            console.warn('全局对象清理警示:', e); // 建议加上 try-catch 防御  
        }  
    }  
}