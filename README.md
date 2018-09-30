[![npm version](https://badge.fury.io/js/microsoft-speech-browser-sdk.svg)](https://www.npmjs.com/package/microsoft-speech-browser-sdk)

# September 2018: New Microsoft Cognitive Services Speech SDK available

> 源码来源见 https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript。以下文档为个人汉化，整天对着英文，烦！

我们发布了一个支持新的统一语音服务的新的语音SDK。  [Unified Speech Service](https://azure.microsoft.com/services/cognitive-services/speech-services) 新的语音SDK附带Windows、Android、Linux、JavaScript和iOS的支持。


请查看[Microsoft认知服务语音SDK](https://aka.ms/csspeech)以获得文档、到下载页面的链接和示例。


> 注意：此存储库的内容支持[Bing语音服务]（https://docs.microsoft.com/en-us/azure/.-services/Speech/Home），而不是新的[统一语音服务]（https://azure.microsoft.com/services/.-services/.-services）。

## 先决条件/Prerequisites

### 订阅语音识别API，并获得免费试用订阅密钥。先拿到key

语音API是认知服务的一部分。您可以从[认知服务订阅]（https://azure.microsoft.com/try/.-services/）页面获得免费试用订阅密钥。选择语音API后，选择**get API密钥**来获得密钥。它返回主键和副键。两个键都绑定到相同的配额，所以可以使用任意一个密钥。

**提示:** 在使用Speech客户端库之前，必须具有[订阅密钥]（https://azure.microsoft.com/try/.-services/）。

## 开始Get started

在本节中，我们将介绍加载HTML HTML页面的必要步骤。该示例位于我们的[Github存储库]（http://Github.com／Azure示例/ Script脚本文本WebSoSoSjavaScript）中。您可以从存储库中直接打开**，或者**从存储库的本地副本**打开示例。

**Note:** 有些浏览器在不安全的来源上阻止麦克风访问。因此，建议在HTTPS上托管“示例”/“应用程序”，使其在所有支持的浏览器上工作。

### 打开示例 Open the sample directly

获取如上所述的订阅密钥。然后打开[链接到样本]（HTTPS://HTMLPROVIEW.GITHUBIO/？）http://Github.com／Azure示例/ Script脚本文本Web套接字JavaScript /BLB/预览/Simult/Browser/Simult.html）。这将将页面加载到默认浏览器中（使用[htmlPreview]（https://github.com/htmlp./htmlp..github.com）呈现）。

### 从本地副本打开示例 Open the sample from a local copy

若要在本地尝试示例，请克隆此存储库：

```
git clone https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript
```

typescript编译的源和/ browserfy束成一个单一的他们的JavaScript文件（npm ] [（http：/ / / www.npmjs.com）需要被安装在你的机器）。变更为《根》和《运行库cloned commands：

```
cd SpeechToText-WebSockets-Javascript && npm run bundle
```

打开 `samples\browser\Sample.html` 在你最喜欢的浏览器中。

## 下一步

### 安装依赖

一个NPM包的微软语音JavaScript WebSu水套SDK是可用的。安装[NPM包]（HTTPS://www. nPMJS.COM/PACKEG/MICROSOFT-SPECHE-BROSELS-SDK）运行
```
npm install microsoft-speech-browser-sdk
```

### 作为node包使用

如果您正在构建一个节点应用程序，并且希望使用语音SDK，那么您需要做的就是添加以下导入语句：

```javascript
import * as SDK from 'microsoft-speech-browser-sdk';
```

<a name="reco_setup"></a>and setup the recognizer:

```javascript
function RecognizerSetup(SDK, recognitionMode, language, format, subscriptionKey) {
    let recognizerConfig = new SDK.RecognizerConfig(
        new SDK.SpeechConfig(
            new SDK.Context(
                new SDK.OS(navigator.userAgent, "Browser", null),
                new SDK.Device("SpeechSample", "SpeechSample", "1.0.00000"))),
        recognitionMode, // SDK.RecognitionMode.Interactive  (Options - Interactive/Conversation/Dictation)
        language, // Supported languages are specific to each recognition mode Refer to docs.
        format); // SDK.SpeechResultFormat.Simple (Options - Simple/Detailed)

    // Alternatively use SDK.CognitiveTokenAuthentication(fetchCallback, fetchOnExpiryCallback) for token auth
    let authentication = new SDK.CognitiveSubscriptionKeyAuthentication(subscriptionKey);

    return SDK.Recognizer.Create(recognizerConfig, authentication);
}

function RecognizerStart(SDK, recognizer) {
    recognizer.Recognize((event) => {
        /*
            Alternative syntax for typescript devs.
            if (event instanceof SDK.RecognitionTriggeredEvent)
        */
        switch (event.Name) {
            case "RecognitionTriggeredEvent" :
                UpdateStatus("Initializing");
                break;
            case "ListeningStartedEvent" :
                UpdateStatus("Listening");
                break;
            case "RecognitionStartedEvent" :
                UpdateStatus("Listening_Recognizing");
                break;
            case "SpeechStartDetectedEvent" :
                UpdateStatus("Listening_DetectedSpeech_Recognizing");
                console.log(JSON.stringify(event.Result)); // check console for other information in result
                break;
            case "SpeechHypothesisEvent" :
                UpdateRecognizedHypothesis(event.Result.Text);
                console.log(JSON.stringify(event.Result)); // check console for other information in result
                break;
            case "SpeechFragmentEvent" :
                UpdateRecognizedHypothesis(event.Result.Text);
                console.log(JSON.stringify(event.Result)); // check console for other information in result
                break;
            case "SpeechEndDetectedEvent" :
                OnSpeechEndDetected();
                UpdateStatus("Processing_Adding_Final_Touches");
                console.log(JSON.stringify(event.Result)); // check console for other information in result
                break;
            case "SpeechSimplePhraseEvent" :
                UpdateRecognizedPhrase(JSON.stringify(event.Result, null, 3));
                break;
            case "SpeechDetailedPhraseEvent" :
                UpdateRecognizedPhrase(JSON.stringify(event.Result, null, 3));
                break;
            case "RecognitionEndedEvent" :
                OnComplete();
                UpdateStatus("Idle");
                console.log(JSON.stringify(event)); // Debug information
                break;
        }
    })
    .On(() => {
        // The request succeeded. Nothing to do here.
    },
    (error) => {
        console.error(error);
    });
}

function RecognizerStop(SDK, recognizer) {
    // recognizer.AudioSource.Detach(audioNodeId) can be also used here. (audioNodeId is part of ListeningStartedEvent)
    recognizer.AudioSource.TurnOff();
}
```

### 在浏览器中，使用webpack

目前，这个SDK中的TypeScript代码是使用默认模块系统（CommonJS）编译的，这意味着编译会产生许多不同的JS源文件。为了使SDK在浏览器中可用，它首先需要“浏览器化”（所有的JavaScript源需要被粘在一起）。为此，这是你需要做的：

1. Add `require` statement to you web app source file, for instance (take a look at [sample_app.js](samples/browser/sample_app.js)):

    ```javascript
        var SDK = require('<path_to_speech_SDK>/Speech.Browser.Sdk.js');
    ```

2. Setup the recognizer, same as [above](#reco_setup).

3. Run your web-app through the webpack (see "bundle" task in [gulpfile.js](gulpfile.js), to execute it, run `npm run bundle`).

4. Add the generated bundle to your html page:

    ```
    <script src="../../distrib/speech.sdk.bundle.js"></script>
    ```

### 在浏览器中，作为本地ES6模块

...in progress, will be available soon

### 基于令牌的认证

To use token-based authentication, please launch a local node server, as described [here](https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript/blob/master/samples/browser/README.md)

## 文档
SDK是语音WebSoSt协议的参考实现. Check the [API reference](https://docs.microsoft.com/en-us/azure/cognitive-services/speech/API-reference-rest/bingvoicerecognition#websocket) and [Websocket protocol reference](https://docs.microsoft.com/en-us/azure/cognitive-services/speech/API-reference-rest/websocketprotocol) for more details.

## 浏览器支持
The SDK depends on WebRTC APIs to get access to the microphone and read the audio stream. Most of todays browsers(Edge/Chrome/Firefox) support this. For more details about supported browsers refer to [navigator.getUserMedia#BrowserCompatibility](https://developer.mozilla.org/en-US/docs/Web/API/Navigator/getUserMedia#Browser_compatibility)

**Note:** The SDK currently depends on [navigator.getUserMedia](https://developer.mozilla.org/en-US/docs/Web/API/Navigator/getUserMedia#Browser_compatibility) API. However this API is in process of being dropped as browsers are moving towards newer [MediaDevices.getUserMedia](https://developer.mozilla.org/en-US/docs/Web/API/MediaDevices/getUserMedia) instead. The SDK will add support to the newer API soon.

## 贡献
This project has adopted the [Microsoft Open Source Code of Conduct](https://opensource.microsoft.com/codeofconduct/). For more information see the [Code of Conduct FAQ](https://opensource.microsoft.com/codeofconduct/faq/) or contact [opencode@microsoft.com](mailto:opencode@microsoft.com) with any additional questions or comments.
