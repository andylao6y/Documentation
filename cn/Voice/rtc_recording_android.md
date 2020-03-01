test test test
---
title: 客户端录制
description: 录制通话音频
platform: Android
updatedAt: Sun Sep 29 2019 08:19:31 GMT+0800 (CST)
---
# 客户端录制
abcdefg
## 功能描述

在通话的过程中，将通话各方的声音录制下来，存放在本地，相当于手机上面的通话录音功能，录制下来的声音可用于回放。

Agora SDK 支持通话过程中在客户端进行录音。该方法录制频道内所有用户的音频，并生成一个包含所有用户声音的录音文件，录音文件格式可以为：

- WAV：文件大，音质保真度高
- AAC：文件小，有一定的音质损失

## 实现方法

开始前请确保已在你的项目中实现基本的实时音视频功能。 详见[开始音视频通话](../../cn/Voice/start_call_android.md)或[开始互动直播](../../cn/Voice/start_live_android.md)。
加入频道后调用 `startAudioRecording` 即可开始录音。

```Java
// 开始录音
rtcEngine.startAudioRecording(
  "path/to/file",                   // 录音文件的本地保存路径，由用户自行指定，需精确到文件名及格式
  AUDIO_RECORDING_QUALITY_HIGH      // 录音音质，分 LOW, MEDIUM, HIGH
);

// 结束录音
rtcEngine.stopAudioRecording();
```

### API 参考

- [`startAudioRecording()`](https://docs.agora.io/cn/Voice/API%20Reference/java/classio_1_1agora_1_1rtc_1_1_rtc_engine.html#a44744695d723b7d18c704a57f828cddb)
- [`stopAudioRecording()`](https://docs.agora.io/cn/Voice/API%20Reference/java/classio_1_1agora_1_1rtc_1_1_rtc_engine.html#a2d751055a21611b3cf99fe39d24bb1a0)

## 注意事项

- 开启录音须在进入频道之后调用
- 离开频道会自动停止录音
