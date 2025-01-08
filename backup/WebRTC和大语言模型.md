最近看到一家startup做的工作，将WebRTC和大语言多模态结合起来，做的一套实时音视频的SDK。

他们的代码看起来现在是开源在github上，有两套代码，之前是叫realtime，后来改名字叫outspeed了。办公司名字也很重要，应该是之前的名字没撒辨识度。

官方提供了几个例子:

- 3d_avatar_chatbot: 使用websocket和客户端交互，处理文本和音频。在收到客户端传过来的音频和文本后，会将音频给到DeepgramSTT转成文字，然后和传过来的文本一起作为输入，给到GroqLLM，可以看到prompt是将回复的内容转化成了json格式，包含文字，表情，动画帧。最后通过AzureTTS合成语音。客户端则是直接用浏览器的音频采集发送到服务端，然后根据服务端返回的语音进行播放，并根据返回的文本，生产3D动画。

- chat_embed: 将3d_avatar_chatbot封装成了一个可以嵌入到任何页面的组件
multimodal_ai_demos CookingAssistant，做饭的助手。音频流作为输入，给到DeepgramSTT转文字，然后和视频流一起作为输入给到OpenAIVision大模型处理，结果输出再给到ElevenLabsTTS转回成语音和文本。其中有用到VAD检测，如果有人说话，会中断相关的处理，起到打断助手的效果。

- multimodal_ai_demos poker_commentator，扑克评论员。音频流作为输入，给到DeepgramSTT转文字，然后视频流使用KeyFrameDetector提取关键帧，然后文本和关键帧一起作为输入给到GeminiVision处理，结果输出为语音，文本。
multimodal_ai_demos voice_bot，音频流作为输入，给到DeepgramSTT转文字，给到FireworksLLM处理，生成的结果再通过CartesiaTTS生成为语音。

- ocr，逐帧的提取视频帧，调用google vision api对图片中的文本进行标注。

- video_surveillance，这个就是直接调用的google.generativeai做检测，输入是视频文件，根据prompt输出是否发生了事故，事故的时间，还有描述信息。

定位是做realtime AI应用的SDK，方便集成各种AI的云服务，提供了服务端和客户端的SDK。

代码仓库:
1. https://github.com/xAlpha8
2. https://github.com/outspeed-ai

参考官方的例子，快速搭建了一个LiveRescue的demo: [LiveRescue](https://gist.github.com/fuji246/09c2a3d074c317404253a12c34ae21cd)，通过语音交互的方式为灾民提供实时的指导。