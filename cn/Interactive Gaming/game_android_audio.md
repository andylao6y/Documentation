
---
title: 游戏 API
description: 
platform: Java
updatedAt: Mon Feb 11 2019 03:31:36 GMT+0000 (UTC)
---
# 游戏 API
互动游戏 API 由 **Java 接口** 和 **C++ 接口** 部分组成。

本文介绍游戏 SDK 在 Android 平台上的 Java 接口。如果想参考 C++ 接口的调用方法，请点击 [C++ 接口](../../cn/Interactive%20Gaming/game_cpp.md)。


## 主要方法 (RtcEngine)

**软件包: io.agora.rtc**

`RtcEngine` 类包含应用程序调用的主要方法，调用 `RtcEngine` 的接口最好在同一个线程进行，不建议在不同的线程同时调用。在先前的版本中，该类被命名为 AgoraAudio，自 v1.0 以后重命名为 RtcEngine。

### 创建 RtcEngine 对象 (create)

```
public static synchronized RtcEngine create(Context context,
                                             String appId,
                                             IRtcEngineEventHandler handler);
```

本方法创建 `RtcEngine` 对象。

目前 Agora Native SDK 只支持一个 `RtcEngine` 实例，每个应用程序仅创建一个 `RtcEngine` 对象 。 `RtcEngine` 类的所有接口函数，如无特殊说明，都是异步调用，对接口的调用建议在同一个线程进行。所有返回值为 int 型的 API，如无特殊说明，返回值 0 为调用成功，返回值小于 0 为调用失败。

<table>
<colgroup>
<col/>
<col/>
</colgroup>
<tbody>
<tr><td><strong>名称</strong></td>
<td><strong>描述</strong></td>
</tr>
<tr><td>context</td>
<td>安卓活动 (Android Activity) 的上下文</td>
</tr>
<tr><td>appId</td>
<td>Agora 为应用程序开发者签发的 App ID，详见 <a href="../../cn/Advanced%20Guide/token.md"><span>获取 App ID</span></a></td>
</tr>
<tr><td>handler</td>
<td>IRtcEngineEventHandler 是一个提供了缺省实现的抽象类，SDK 通过该抽象类向应用程序报告 SDK 运行时的各种事件</td>
</tr>
<tr><td>返回值</td>
<td><code>RtcEngine</code> 对象</td>
</tr>
</tbody>
</table>

### 实现语音直播

#### 设置频道属性 (setChannelProfile)

```
public abstract int setChannelProfile(int profile);
```

该方法用于设置频道模式 (Profile)。Agora RtcEngine 需知道应用程序的使用场景(例如通信模式或直播模式), 从而使用不同的优化手段。

目前 Agora Native SDK 支持以下几种模式:

<table>
<colgroup>
<col/>
<col/>
</colgroup>
<tbody>
<tr><td><strong>模式</strong></td>
<td><strong>描述</strong></td>
</tr>
<tr><td>通信</td>
<td>通信为默认模式，用于常见的一对一或群聊，频道中的任何用户可以自由说话</td>
</tr>
<tr><td>直播</td>
<td>直播模式有主播和观众两种用户角色，可以通过调用 setClientRole 设置。主播可收发语音和视频，但观众只能收，不能发</td>
</tr>
<tr><td>游戏语音</td>
<td>频道内任何用户可自由发言。该模式下默认使用低功耗低码率的编解码器</td>
</tr>
</tbody>
</table>

> -   同一频道内只能同时设置一种模式。如果想要切换模式，则需要先调用 destroy 销毁当前引擎，然后在 `create` 方法中传入 **不同** 的 App ID 重新创建引擎，再调用该方法切换频道模式。
> -   不同的频道模式必须使用不同的 App ID。
> -   该方法必须在加入频道前调用和进行设置，进入频道后无法再设置。


<table>
<colgroup>
<col/>
<col/>
</colgroup>
<tbody>
<tr><td><strong>名称</strong></td>
<td><strong>描述</strong></td>
</tr>
<tr><td>profile</td>
<td><p>频道模式：</p>
<ul>
<li>CHANNEL_PROFILE_COMMUNICATION = 0: 通信模式 (默认)</li>
<li>CHANNEL_PROFILE_LIVE_BROADCASTING = 1: 直播模式</li>
<li>CHANNEL_PROFILE_GAME = 2: 游戏语音模式</li>
</ul>
</td>
</tr>
<tr><td>返回值</td>
<td><ul>
<li>0：方法调用成功</li>
<li>&lt; 0：方法调用不成功</li>
</ul>
</td>
</tr>
</tbody>
</table>


#### 设置用户角色 (setClientRole)

```
public abstract int setClientRole(int role);
```

直播模式下，在加入频道前，用户需要通过本方法设置观众(默认)或主播模式。在加入频道后，用户可以通过本方法切换用户模式。

<table>
<colgroup>
<col/>
<col/>
</colgroup>
<tbody>
<tr><td><strong>名称</strong></td>
<td><strong>描述</strong></td>
</tr>
<tr><td>role</td>
<td><p>直播场景里的用户角色</p>
<div><ul>
<li>CLIENT_ROLE_BROADCASTER = 1; 主播</li>
<li>CLIENT_ROLE_AUDIENCE = 2; 观众(默认)</li>
</ul>
</div>
</td>
</tr>
<tr><td>返回值</td>
<td><ul>
<li>0: 方法调用成功</li>
<li>&lt; 0: 方法调用失败</li>
</ul>
</td>
</tr>
</tbody>
</table>



#### 打开音频 (enableAudio)

```
public abstract int enableAudio();
```

打开音频(默认为开启状态)。

<table>
<colgroup>
<col/>
<col/>
</colgroup>
<tbody>
<tr><td><strong>名称</strong></td>
<td><strong>描述</strong></td>
</tr>
<tr><td>返回值</td>
<td><ul>
<li>0: 方法调用成功</li>
<li>&lt; 0: 方法调用失败</li>
</ul>
</td>
</tr>
<tr/>
</tbody>
</table>

> 该方法设置内部引擎为启用状态，在 `leaveChannel` 后仍然有效。

#### 关闭/重新开启本地语音功能 (enableLocalAudio)

```
public abstract int enableLocalAudio(boolean enabled);
```

当 App 加入频道时，它的语音功能默认是开启的。该方法可以关闭或重新开启本地语音功能，停止或重新开始本地音频采集及处理。
该方法不影响接收或播放远端音频流，适用于只听不发的用户场景。
语音功能关闭或重新开启后，会收到回调 `onMicrophoneEnabled`。

> - 该方法需要在 `joinChannel` 之后调用才能生效。
> - 该方法与 `muteLocalAudioStream` 的区别在于：
>    *  `enableLocalAudio`：开启或关闭本地语音采集及处理
>    *  `muteLocalAudioStream`：停止或继续发送本地音频流

<table>
<colgroup>
<col/>
<col/>
</colgroup>
<tbody>
<tr><td><strong>名称</strong></td>
<td><strong>描述</strong></td>
</tr>
<tr><td>enabled</td>
<td>
<ul>
<li>true：重新开启本地语音功能，即开启本地语音采集或处理</li>
<li>false：关闭本地语音功能，即停止本地语音采集或处理</li>
</ul>
</td>
</tr>
<tr><td>返回值</td>
<td><ul>
<li>0: 方法调用成功</li>
<li>&lt; 0: 方法调用失败</li>
</ul>
</td>
</tr>
</tbody>
</table>

#### 关闭音频 (disableAudio)

```
public abstract int disableAudio();
```

关闭音频。

<table>
<colgroup>
<col/>
<col/>
</colgroup>
<tbody>
<tr><td><strong>名称</strong></td>
<td><strong>描述</strong></td>
</tr>
<tr><td>返回值</td>
<td><ul>
<li>0: 方法调用成功</li>
<li>&lt; 0: 方法调用失败</li>
</ul>
</td>
</tr>
<tr/>
</tbody>
</table>


> 该方法设置内部引擎为禁用状态，在 `leaveChannel` 后仍然有效。



#### 加入频道 (joinChannel)

```
public abstract int joinChannel(String token,
                                String channelName,
                                String optionalInfo,
                                int optionalUid);
```

该方法让用户加入通话频道，在同一个频道内的用户可以互相通话，多个用户加入同一个频道，可以群聊。 使用不同 App ID 的应用程序是不能互通的。如果已在通话中，用户必须调用 `leaveChannel()` 退出当前通话，才能进入下一个频道。


> 同一个频道里不能出现两个相同的 UID。如果你的 App 支持多设备同时登录，即同一个用户账号可以在不同的设备上同时登录(例如微信支持在 PC 端和移动端同时登录)，请保证传入的 UID 不相同。 例如你之前都是用同一个用户标识作为 UID, 建议从现在开始加上设备 ID, 以保证传入的 UID 不相同 。如果你的 App 不支持多设备同时登录，例如在电脑上登录时，手机上会自动退出，这种情况下就不需要在 UID 上添加设备 ID。

<table>
<colgroup>
<col/>
<col/>
</colgroup>
<tbody>
<tr><td><strong>名称</strong></td>
<td><strong>描述</strong></td>
</tr>
<tr><td>token</td>
<td><ul>
<li>安全要求不高: 将值设为 null</li>
<li>安全要求高: 将值设置为 Token 值。如果你已经启用了 App Certificate，请务必使用 Token。 关于如何获取 Token，详见 <a href="../../cn/Advanced%20Guide/token.md"><span>密钥说明</span></a> 。</li>
</ul>
</td>
</tr>
<tr><td>channelName</td>
<td>标识通话的频道名称，长度在64字节以内的字符串。以下为支持的字符集范围（共89个字符）：
<li>26 个小写英文字母 a-z</li>
<li>26 个大写英文字母 A-Z</li>
<li>10 个数字 0-9</li>
<li>空格</li>
<li>"!", "#", "$", "%", "&", "(", ")", "+", "-", ":", ";", "<", "=", ".", ">", "?", "@", "[", "]", "^", "_", " {", "}", "|", "~", ","</li></td>
</tr>
<tr><td>optionalInfo</td>
<td>(非必选项) 开发者需加入的任何附加信息。一般可设置为空字符串，或频道相关信息。该信息不会传递给频道内的其他用户</td>
</tr>
<tr><td>optionalUid</td>
<td><p>(非必选项) 用户ID，32位无符号整数。建议设置范围：1到 (2^32-1)，并保证唯一性。如果不指定（即设为0），SDK 会自动分配一个，并在 <code>onJoinChannelSuccess</code> 回调方法中返回，App 层必须记住该返回值并维护，SDK不对该返回值进行维护</p>
<p>uid 在 SDK 内部用 32 位无符号整数表示，由于 java 不支持无符号整数，uid 被当成 32 位有符号整数处理，对于过大的整数，java 会表示为负数，如有需要可以用(uid&amp;0xffffffffL)转换成 64 位整数</p>
</td>
</tr>
<tr><td>返回值</td>
<td><ul>
<li>0：方法调用成功</li>
<li>&lt; 0：方法调用失败</li>
<ul>
<li>ERR_INVALID_ARGUMENT (-2)：传递的参数无效</li>
<li>ERR_NOT_READY (-3)：没有成功初始化</li>
</ul>
</li>
</ul>
</td>
</tr>
</tbody>
</table>



#### 离开频道 (leaveChannel)

```
public abstract int leaveChannel();
```

离开频道，即挂断或退出通话。

当调用 `joinChannel()` API 方法后，必须调用 `leaveChannel()` 结束通话，否则无法开始下一次通话。 不管当前是否在通话中，都可以调用 `leaveChannel()`，没有副作用。该方法会把会话相关的所有资源释放掉。该方法是异步操作，调用返回时并没有真正退出频道。在真正退出频道后，SDK 会触发 `onLeaveChannel` 回调。

> 如果你调用了 `leaveChannel` 后立即调用 `destroy()`，SDK 将无法触发 `onLeaveChannel` 回调。

<table>
<colgroup>
<col/>
<col/>
</colgroup>
<tbody>
<tr><td>名称</td>
<td>描述</td>
</tr>
<tr><td>返回值</td>
<td><ul>
<li>0：方法调用成功</li>
<li>&lt; 0：方法调用不成功</li>
</ul>
</td>
</tr>
</tbody>
</table>

#### 设置本地语音音调 (setLocalVoicePitch)

```
public abstract int setLocalVoicePitch(double pitch);
```

该方法改变本地说话人声音的音调。

<table>
<colgroup>
<col/>
<col/>
</colgroup>
<tbody>
<tr><td><strong>名称</strong></td>
<td><strong>描述</strong></td>
</tr>
<tr><td>pitch</td>
<td>语音频率可以 [0.5, 2.0] 范围内设置。默认值为 1.0</td>
</tr>
<tr><td>返回值</td>
<td><ul>
<li>0: 方法调用成功</li>
<li>&lt; 0: 方法调用失败</li>
</ul>
</td>
</tr>
</tbody>
</table>


#### 设置远端用户的语音位置 (setRemoteVoicePosition)

```
public int setRemoteVoicePosition(int uid, double pan, double gain);
```

该方法设置远端用户的语音位置。

> 该方法只在耳机模式下有效，在外放模式下无效。

<table>
<colgroup>
<col>
</col>
</colgroup>
<tbody>
<tr><td><strong>名称</strong></td>
<td><strong>描述</strong></td>
</tr>
<tr><td>uid</td>
<td><p>远端用户的 UID</p>
</td></tr>
<tr><td>pan</td>
<td><p>设置是否改变音效的空间位置。取值范围为 [-1, 1]：
<ul>
<li>0：表示该音效出现在正前方</li>
<li>-1：表示该音效出现在左边</li>
<li>1：表示该音效出现在右边</li>
</ul>
</td>
</tr>
<tr><td>gain</td>
<td><p>设置是否改变单个音效的音量。取值范围为 [0.0, 100.0]。
默认值为 100.0。取值越小，音效的音量越低。</p>
</td></tr>
<tr><td>返回值</td>
<td><ul>
<li>0: 方法调用成功</li>
<li>&lt; 0: 方法调用失败</li>
</ul>
</td>
</tr>
</tbody>
</table>

#### 设置仅语音模式 (setVoiceOnlyMode)

```
public int setVoiceOnlyMode(boolean enable);
```

该方法设置仅语音模式。即仅传输语音流，而不传输其他流，例如，敲键盘的声音等。

<table>
<colgroup>
<col/>
<col/>
</colgroup>
<tbody>
<tr><td><strong>名称</strong></td>
<td><strong>描述</strong></td>
</tr>
<tr><td>enable</td>
<td>
<ul>
<li>true：启用仅语音模式</li>
<li>false：禁用仅语音模式</li>
</ul>
</td>
</tr>
<tr><td>返回值</td>
<td><ul>
<li>0: 方法调用成功</li>
<li>&lt; 0: 方法调用失败</li>
</ul>
</td>
</tr>
</tbody>
</table>


#### 设置本地语音音效均衡 (setLocalVoiceEqualization)

```
public abstract int setLocalVoiceEqualization(int bandFrequency, int bandGain);
```

该方法设置本地语音音效均衡。

<table>
<colgroup>
<col/>
<col/>
</colgroup>
<tbody>
<tr><td><strong>名称</strong></td>
<td><strong>描述</strong></td>
</tr>
<tr><td>bandFrequency</td>
<td>取值范围是 [0-9]，分别代表音效的 10 个 band 的中心频率 [31，62，125，250，500，1k，2k，4k，8k，16k]Hz</td>
</tr>
<tr><td>bandGain</td>
<td>每个 band 的增益，单位是 dB，每一个值的范围是 [-15，15]</td>
</tr>
</tbody>
</table>



#### 设置本地音效混响 (setLocalVoiceReverb)

```
public abstract int setLocalVoiceReverb(int reverbKey, int value);
```

该方法设置本地音效混响。

<table>
<colgroup>
<col/>
<col/>
</colgroup>
<tbody>
<tr><td><strong>名称</strong></td>
<td><strong>描述</strong></td>
</tr>
<tr><td>reverbKey</td>
<td>混响音效 Key。该方法共有 5 个混响音效 Key，分别如 value 栏列出</td>
</tr>
<tr><td>value</td>
<td><p>各混响音效 Key 所对应的值：</p>
<ul>
<li>AUDIO_REVERB_DRY_LEVEL = 0：(dB, [-20,10])，原始声音效果，即所谓的 dry signal</li>
<li>AUDIO_REVERB_WET_LEVEL = 1：(dB, [-20,10])，早期反射信号效果，即所谓的 wet signal</li>
<li>AUDIO_REVERB_ROOM_SIZE = 2：([0，100])， 所需混响效果的房间尺寸</li>
<li>AUDIO_REVERB_WET_DELAY = 3：(ms, [0, 200])，wet signal 的初始延迟长度，以毫秒为单位</li>
<li>AUDIO_REVERB_STRENGTH = 4：([0，100])，后期混响长度</li>
</ul>
</td>
</tr>
</tbody>
</table>


### 实现视频直播

#### 创建渲染视图 (createRendererView)

```
public static SurfaceView createRendererView (Context context);
```

该方法创建视频渲染视图，返回 SurfaceView 的类型。view 的操作和布局由 App 管理, Agora SDK 在 App 提供的 view 上进行渲染。显示视频视图必须调用该方法，而不是直接调用 SurfaceView。

<table>
<colgroup>
<col/>
<col/>
</colgroup>
<tbody>
<tr><td><strong>名称</strong></td>
<td><strong>描述</strong></td>
</tr>
<tr><td>context</td>
<td>安卓活动 (Android Activity) 的上下文</td>
</tr>
</tbody>
</table>



#### 打开视频模式 (enableVideo)

```
public abstract int enableVideo();
```

该方法用于打开视频模式。可以在加入频道前或者通话中调用，在加入频道前调用，则自动开启视频模式，在通话中调用则由音频模式切换为视频模式。 调用 `disableVideo()` 方法可关闭视频模式。

<table>
<colgroup>
<col/>
<col/>
</colgroup>
<tbody>
<tr><td><strong>名称</strong></td>
<td><strong>描述</strong></td>
</tr>
<tr><td>返回值</td>
<td><ul>
<li>0: 方法调用成功</li>
<li>&lt; 0: 方法调用失败</li>
</ul>
</td>
</tr>
<tr/>
</tbody>
</table>


> 该方法设置内部引擎为启用状态，在 `leaveChannel` 后仍然有效。

#### 关闭视频模式 (disableVideo)

```
public abstract int disableVideo();
```

该方法用于关闭视频。可以在加入频道前或者通话中调用，在加入频道前调用，则自动开启纯音频模式，在通话中调用则由视频模式切换为纯音频模式。 调用 `enableVideo()` 方法可开启视频模式。

<table>
<colgroup>
<col/>
<col/>
</colgroup>
<tbody>
<tr><td><strong>名称</strong></td>
<td><strong>描述</strong></td>
</tr>
<tr><td>返回值</td>
<td><ul>
<li>0: 方法调用成功</li>
<li>&lt; 0: 方法调用失败</li>
</ul>
</td>
</tr>
<tr/>
</tbody>
</table>


> 该方法设置内部引擎为禁用状态，在 `leaveChannel` 后仍然有效。

#### 启用本地视频 (enableLocalVideo)

```
public abstract int enableLocalVideo(boolean enabled);
```

禁用/启用本地视频功能。该方法用于只看不发的视频场景。

请在 `enableVideo` 后调用该方法，否则该方法可能无法正常使用。 调用 `enableVideo` 后，本地视频即默认开启。使用该方法可以开启或关闭本地视频，且不影响接收远端视频。

<table>
<colgroup>
<col/>
<col/>
</colgroup>
<tbody>
<tr><td>名称</td>
<td>描述</td>
</tr>
<tr><td>enabled</td>
<td><p>是否启用本地视频：</p>
<div><ul>
<li>true：开启本地视频采集和渲染（默认）</li>
<li>false：关闭使用本地摄像头设备。关闭后，远端用户会接收不到本地用户的视频流；但本地用户依然可以接收远端用户的视频流。设置为 false 时，该方法不需要本地有摄像头。</li>
</ul>
</div>
</td>
</tr>
<tr><td>返回值</td>
<td><ul>
<li>0：方法调用成功</li>
<li>&lt; 0：方法调用失败</li>
</ul>
</td>
</tr>
</tbody>
</table>


> 该方法设置内部引擎为启用/禁用状态，在 `leaveChannel` 后仍然有效。

#### 设置视频属性 (setVideoProfile)

```
public abstract int setVideoProfile(int profile, boolean swapWidthAndHeight);
```

该方法设置视频的编码属性。如果用户加入频道后不需要重新设置视频编码属性，则 Agora 建议在 `enableVideo` 前调用该方法，可以加快首帧出图的时间。

<table>
<colgroup>
<col/>
<col/>
</colgroup>
<tbody>
<tr><td><strong>名称</strong></td>
<td><strong>描述</strong></td>
</tr>
<tr><td>profile</td>
<td>视频属性 (profile)，详见下表中的定义</td>
</tr>
<tr><td>swapWidthAndHeight</td>
<td><p>SDK 会按照你选择的视频属性 (profile) 输出固定宽高的视频。该参数设置是否交换宽和高：</p>
<ul>
<li>true：交换宽和高，交换后视频为 Portrait（竖屏）布局，即宽 &lt; 高</li>
<li>false：（默认）不交换宽和高，视频为 Landscape（横屏）布局，即宽 &gt; 高</li>
</ul>
</td>
</tr>
<tr><td>返回值</td>
<td><ul>
<li>0：方法调用成功</li>
<li>&lt; 0：方法调用失败</li>
</ul>
</td>
</tr>
</tbody>
</table>

**视频属性 (Profile) 表**

<table>
<colgroup>
<col/>
<col/>
<col/>
<col/>
</colgroup>
<tbody>
<tr><td>视频属性</td>
<td>分辨率（宽x高）</td>
<td>帧率（fps）</td>
<td>码率 (Kbps)</td>
</tr>
<tr><td>120P</td>
<td>160x120</td>
<td>15</td>
<td>65</td>
</tr>
<tr><td>120P_3</td>
<td>120x120</td>
<td>15</td>
<td>50</td>
</tr>
<tr><td>180P</td>
<td>320x180</td>
<td>15</td>
<td>140</td>
</tr>
<tr><td>180P_3</td>
<td>180x180</td>
<td>15</td>
<td>100</td>
</tr>
<tr><td>180P_4</td>
<td>240x180</td>
<td>15</td>
<td>120</td>
</tr>
<tr><td>240P</td>
<td>320x240</td>
<td>15</td>
<td>200</td>
</tr>
<tr><td>240P_3</td>
<td>240x240</td>
<td>15</td>
<td>140</td>
</tr>
<tr><td>240P_4</td>
<td>424x240</td>
<td>15</td>
<td>220</td>
</tr>
<tr><td>360P</td>
<td>640x360</td>
<td>15</td>
<td>400</td>
</tr>
<tr><td>360P_3</td>
<td>360x360</td>
<td>15</td>
<td>260</td>
</tr>
<tr><td>360P_4</td>
<td>640x360</td>
<td>30</td>
<td>600</td>
</tr>
<tr><td>360P_6</td>
<td>360x360</td>
<td>30</td>
<td>400</td>
</tr>
<tr><td>360P_7</td>
<td>480x360</td>
<td>15</td>
<td>320</td>
</tr>
<tr><td>360P_8</td>
<td>480x360</td>
<td>30</td>
<td>490</td>
</tr>
<tr><td>360P_9</td>
<td>640X360</td>
<td>15</td>
<td>600</td>
</tr>
<tr><td>360P_10</td>
<td>640x360</td>
<td>24</td>
<td>800</td>
</tr>
<tr><td>360P_11</td>
<td>640X360</td>
<td>24</td>
<td>800</td>
</tr>
<tr><td>480P</td>
<td>640x480</td>
<td>15</td>
<td>500</td>
</tr>
<tr><td>480P_3</td>
<td>480x480</td>
<td>15</td>
<td>400</td>
</tr>
<tr><td>480P_4</td>
<td>640x480</td>
<td>30</td>
<td>750</td>
</tr>
<tr><td>480P_6</td>
<td>480x480</td>
<td>30</td>
<td>600</td>
</tr>
<tr><td>480P_8</td>
<td>848x480</td>
<td>15</td>
<td>610</td>
</tr>
<tr><td>480P_9</td>
<td>848x480</td>
<td>30</td>
<td>930</td>
</tr>
<tr><td>480P_10</td>
<td>640x480</td>
<td>10</td>
<td>400</td>
</tr>
<tr><td>720P</td>
<td>1280x720</td>
<td>15</td>
<td>1130</td>
</tr>
<tr><td>720P_3</td>
<td>1280x720</td>
<td>30</td>
<td>1710</td>
</tr>
<tr><td>720P_5</td>
<td>960x720</td>
<td>15</td>
<td>910</td>
</tr>
<tr><td>720P_6</td>
<td>960x720</td>
<td>30</td>
<td>1380</td>
</tr>
</tbody>
</table>

> 视频能否达到 720P 或以上的分辨率取决于设备的性能，在性能配备较低的设备上有可能无法实现。如果采用 720P 分辨率而设备性能跟不上，则有可能出现帧率过低的情况。


#### 手动设置视频属性（setVideoProfile)

```
public abstract int setVideoProfile(int width, int height, int frameRate, int bitrate);
```

如果上表中的 Profile 和你需求的不一致，也可以使用该接口手动设置视频的宽、高、帧率和码率。如果用户加入频道后不需要重新设置视频编码属性，则 Agora 建议在 `enableVideo` 前调用该方法，可以加快首帧出图的时间。

<table>
<colgroup>
<col/>
<col/>
</colgroup>
<tbody>
<tr><td>名称</td>
<td>描述</td>
</tr>
<tr><td>width</td>
<td>你想要设置的视频宽度，最高值不超过 1280</td>
</tr>
<tr><td>height</td>
<td>你想要设置的视频高度，最高值不超过 720</td>
</tr>
<tr><td>frameRate</td>
<td>你想要设置的视频帧率，最高值不超过 30，如： 5、10、15、24、30 等</td>
</tr>
<tr><td>bitrate</td>
<td><p>你想要设置的视频码率，需要开发者根据想要设置的视频的宽、高和帧率，并结合上表，手动推算出合适值。宽和高固定的情况下，码率随帧率的变化而变化</p>
<ul>
<li>若选取的帧率为 5 FPS，则推算码率为上表推荐码率除以 2</li>
<li>若选取的帧率为 15 FPS，则推算码率为上表推荐码率</li>
<li>若选取的帧率为 30 FPS，则推算码率为上表码率乘以 1.5</li>
<li>若选取其余帧率，等比例推算即可</li>
</ul>
<p>若设置的视频码率超出合理范围，SDK 会自动按照合理区间处理码率</p>
</td>
</tr>
</tbody>
</table>

#### 设置本地视频显示属性 (setupLocalVideo)

```
public abstract int setupLocalVideo(VideoCanvas local);
```

该方法设置本地视频显示信息。应用程序通过调用此接口绑定本地视频流的显示视窗(view)，并设置视频显示模式。 在应用程序开发中，通常在初始化后调用该方法进行本地视频设置，然后再加入频道。退出频道后，绑定仍然有效，如果需要解除绑定，可以指定空(NULL)View 调用 `setupLocalVideo()` 。

<table>
<colgroup>
<col/>
<col/>
</colgroup>
<tbody>
<tr><td>名称</td>
<td>描述</td>
</tr>
<tr><td>local</td>
<td><p>设置视频属性。Class VideoCanvas：</p>
<ul>
<li>view：视频显示视窗</li>
<li>renderMode：视频显示模式<ul>
<li>RENDER_MODE_HIDDEN (1)：视频尺寸等比缩放。优先保证视窗被填满。因视频尺寸与显示视窗尺寸不一致而多出的视频将被截掉。</li>
<li>RENDER_MODE_FIT (2)：视频尺寸等比缩放。优先保证视频内容全部显示。因视频尺寸与显示视窗尺寸不一致造成的视窗未被填满的区域填充黑色。</li>
</ul>
</li>
<li>返回值：本地用户 ID，与 joinChannel 中的 uid 保持一致</li>
</ul>
</td>
</tr>
<tr><td>返回值</td>
<td><ul>
<li>0：方法调用成功</li>
<li>&lt; 0：方法调用失败</li>
</ul>
</td>
</tr>
</tbody>
</table>


#### 设置远端视频显示属性 (setupRemoteVideo)

```
public abstract int setupRemoteVideo(VideoCanvas remote);
```

该方法绑定远程用户和显示视图，即设定 uid 指定的用户用哪个视图显示。调用该接口时需要指定远程视频的 uid，一般可以在进频道前提前设置好。

如果应用程序不能事先知道对方的 uid，可以在 APP 收到 onUserJoined 事件时设置。如果启用了视频录制功能，视频录制服务会做为一个哑客户端加入频道，因此其他客户端也会收到它的 onUserJoined 事件，APP 不应给它绑定视图（因为它不会发送视频流），如果 APP 不能识别哑客户端，可以在 onFirstRemoteVideoDecoded 事件时再绑定视图。解除某个用户的绑定视图可以把 view 设置为空。退出频道后，SDK 会把远程用户的绑定关系清除掉。

<table>
<colgroup>
<col/>
<col/>
</colgroup>
<tbody>
<tr><td>名称</td>
<td>描述</td>
</tr>
<tr><td>remote</td>
<td><p>设置视频属性。Class VideoCanvas：</p>
<ul>
<li>view：视频显示视窗</li>
<li>renderMode：视频显示模式<ul>
<li>RENDER_MODE_HIDDEN (1)：视频尺寸等比缩放。优先保证视窗被填满。因视频尺寸与显示视窗尺寸不一致而多出的视频将被截掉。</li>
<li>RENDER_MODE_FIT (2)：视频尺寸等比缩放。优先保证视频内容全部显示。因视频尺寸与显示视窗尺寸不一致造成的视窗未被填满的区域填充黑色。</li>
</ul>
</li>
</ul>
</td>
</tr>
<tr><td>返回值</td>
<td><ul>
<li>0：方法调用成功</li>
<li>&lt; 0：方法调用失败</li>
</ul>
</td>
</tr>
</tbody>
</table>

#### 用双流/单流模式 (enableDualStreamMode)

```
public abstract int enableDualStreamMode(boolean enabled);
```

使用该方法设置单流（默认）或者双流模式。发送端开启双流模式后，接收端可以选择接收大流还是小流。 其中，大流指高分辨率、高码率的视频流，小流指低分辨率、低码率的视频流。

<table>
<colgroup>
<col/>
<col/>
</colgroup>
<tbody>
<tr><td><strong>名称</strong></td>
<td><strong>描述</strong></td>
</tr>
<tr><td>enabled</td>
<td><p>指定双流或者单流模式:</p>
<ul>
<li>true: 双流</li>
<li>false: 单流(默认)</li>
</ul>
</td>
</tr>
<tr/>
<tr/>
<tr><td>返回值</td>
<td><ul>
<li>0: 方法调用成功</li>
<li>&lt; 0: 方法调用失败</li>
</ul>
</td>
</tr>
<tr/>
</tbody>
</table>



#### 设置视频大小流 (setRemoteVideoStreamType)

```
public abstract int setRemoteVideoStreamType(int uid, int streamType);
```

当远端用户发送了双流 (视频大流和小流) 时，使用该方法本地用户可以选择接收远端用户的视频大流还是小流。 该方法可以根据视频窗口的大小动态调整对应视频流的大小，以节约带宽和计算资源。 本方法调用状态将在下文的 onApiCallExecuted 中返回。Agora SDK 默认收到视频大流。如需使用视频小流，调用本方法进行切换。

<table>
<colgroup>
<col/>
<col/>
</colgroup>
<tbody>
<tr><td><strong>名称</strong></td>
<td><strong>描述</strong></td>
</tr>
<tr><td>uid</td>
<td>用户 ID</td>
</tr>
<tr><td>streamType</td>
<td><p>设置视频流大小。视频流类型如下：</p>
<div><ul>
<li>VIDEO_STREAM_HIGH = 0：视频大流</li>
<li>VIDEO_STREAM_LOW = 1：视频小流</li>
</ul>
</div>
</td>
</tr>
<tr><td>返回值</td>
<td><ul>
<li>0：方法调用成功</li>
<li>&lt; 0：方法调用失败</li>
</ul>
</td>
</tr>
</tbody>
</table>

#### 设置远端视频的默认视频流类型 (setRemoteDefaultVideoStreamType)

```
public abstract int setRemoteDefaultVideoStreamType(int streamType);
```

该方法设置远端视频默认为大流或小流。

<table>
<colgroup>
<col/>
<col/>
</colgroup>
<tbody>
<tr><td><strong>名称</strong></td>
<td><strong>描述</strong></td>
</tr>
<tr><td>streamType</td>
<td><p>设置视频流类型。视频流类型如下：</p>
<div><ul>
<li>VIDEO_STREAM_HIGH = 0：视频大流</li>
<li>VIDEO_STREAM_LOW = 1：视频小流</li>
</ul>
</div>
</td>
</tr>
<tr><td>返回值</td>
<td><ul>
<li>0：方法调用成功</li>
<li>&lt; 0：方法调用失败</li>
</ul>
</td>
</tr>
</tbody>
</table>



#### 设置视频优化选项 (setVideoQualityParameters)

```
public abstract int setVideoQualityParameters(boolean preferFrameRateOverImageQuality);
```

该方法允许用户设置视频的优化选项。

<table>
<colgroup>
<col/>
<col/>
</colgroup>
<tbody>
<tr><td>名称</td>
<td>描述</td>
</tr>
<tr><td>preferFrameRateOverImageQuality</td>
<td><p>支持两种优化选项：</p>
<ul>
<li>true：画质和流畅度里，优先保证流畅度</li>
<li>false：画质和流畅度里，优先保证画质 (默认)</li>
</ul>
</td>
</tr>
<tr><td>返回值</td>
<td><ul>
<li>0：方法调用成功</li>
<li>&lt; 0：方法调用失败</li>
</ul>
</td>
</tr>
</tbody>
</table>



#### 开启视频预览 (startPreview)

```
public abstract int startPreview();
```

该方法用于在进入频道前启动本地视频预览。调用该 API 前，必须：

-   调用 setupLocalVideo 设置预览窗口及属性

-   调用 enableVideo() 开启视频功能。

> 使用该方法开启本地视频预览后，如果直接调用 `leaveChannel` 退出频道，并不会关闭视频预览。如需关闭预览，请调用 `stopPreview`。

<table>
<colgroup>
<col/>
<col/>
</colgroup>
<tbody>
<tr><td><strong>名称</strong></td>
<td><strong>描述</strong></td>
</tr>
<tr><td>返回值</td>
<td><ul>
<li>0: 方法调用成功</li>
<li>&lt; 0: 方法调用失败</li>
</ul>
</td>
</tr>
<tr/>
</tbody>
</table>



#### 停止视频预览 (stopPreview)

```
public abstract int stopPreview();
```

该方法用于停止本地视频预览。

<table>
<colgroup>
<col/>
<col/>
</colgroup>
<tbody>
<tr><td><strong>名称</strong></td>
<td><strong>描述</strong></td>
</tr>
<tr><td>返回值</td>
<td><ul>
<li>0: 方法调用成功</li>
<li>&lt; 0: 方法调用失败</li>
</ul>
</td>
</tr>
<tr/>
</tbody>
</table>

#### 设置本地视频显示模式 (setLocalRenderMode)

```
public abstract int setLocalRenderMode(int mode);
```

该方法设置本地视频显示模式。应用程序可以多次调用此方法更改显示模式。

<table>
<colgroup>
<col/>
<col/>
</colgroup>
<tbody>
<tr><td>名称</td>
<td>描述</td>
</tr>
<tr><td>mode</td>
<td><p>设置视频显示模式：</p>
<div><ul>
<li>RENDER_MODE_HIDDEN (1)：如果视频尺寸与显示视窗尺寸不一致，则视频流会按照显示视窗的比例进行周边裁剪或图像拉伸后填满视窗</li>
<li>RENDER_MODE_FIT (2)：如果视频尺寸与显示视窗尺寸不一致，在保持长宽比的前提下，将视频进行缩放后填满视窗，缩放后的视频四周会有一圈黑边</li>
</ul>
</div>
</td>
</tr>
<tr><td>返回值</td>
<td><ul>
<li>0：方法调用成功</li>
<li>1：方法调用失败</li>
</ul>
</td>
</tr>
</tbody>
</table>



#### 设置远端视频显示模式 (setRemoteRenderMode)

```
public abstract int setRemoteRenderMode(int uid, int mode);
```

该方法设置远端视频显示模式。应用程序可以多次调用此方法更改显示模式。

<table>
<colgroup>
<col/>
<col/>
</colgroup>
<tbody>
<tr><td>名称</td>
<td>描述</td>
</tr>
<tr><td>uid</td>
<td>远端用户 UID</td>
</tr>
<tr><td>mode</td>
<td><p>设置视频显示模式：</p>
<div><ul>
<li>RENDER_MODE_HIDDEN (1)：如果视频尺寸与显示视窗尺寸不一致，则视频流会按照显示视窗的比例进行周边裁剪或图像拉伸后填满视窗</li>
<li>RENDER_MODE_FIT (2)：如果视频尺寸与显示视窗尺寸不一致，在保持长宽比的前提下，将视频进行缩放后填满视窗，缩放后的视频四周会有一圈黑边</li>
<li>RENDER_MODE_ADAPTIVE (3)：该模式已废弃，不推荐使用</li>
</ul>
</div>
</td>
</tr>
<tr><td>返回值</td>
<td><ul>
<li>0：方法调用成功</li>
<li>1：方法调用失败</li>
</ul>
</td>
</tr>
</tbody>
</table>

#### 设置本地视频镜像 (setLocalVideoMirrorMode)

```
public abstract int setLocalVideoMirrorMode(int mode);
```

该方法设置本地视频镜像，须在开启本地预览前设置。如果在开启预览后设置，需要重新开启预览才能生效。

<table>
<colgroup>
<col/>
<col/>
</colgroup>
<tbody>
<tr><td><strong>名称</strong></td>
<td><strong>描述</strong></td>
</tr>
<tr><td>mode</td>
<td><p>设置本地视频镜像模式</p>
<div><ul>
<li>0：默认镜像模式，即由 SDK 决定镜像模式</li>
<li>1：启用镜像模式</li>
<li>2：关闭镜像模式</li>
</ul>
</div>
</td>
</tr>
<tr><td>返回值</td>
<td><ul>
<li>0：该方法调用成功</li>
<li>&lt; 0：该方法调用失败</li>
</ul>
</td>
</tr>
</tbody>
</table>


### 管理摄像头

#### 切换前置/后置摄像头 (switchCamera)

```
public abstract int switchCamera();
```

该方法用于在前置/后置摄像头间切换。

<table>
<colgroup>
<col/>
<col/>
</colgroup>
<tbody>
<tr><td><strong>名称</strong></td>
<td><strong>描述</strong></td>
</tr>
<tr><td>返回值</td>
<td><ul>
<li>0: 方法调用成功</li>
<li>&lt; 0: 方法调用失败</li>
</ul>
</td>
</tr>
<tr/>
</tbody>
</table>



#### 检测设备是否支持相机缩放功能 (isCameraZoomSupported)

```
public abstract boolean isCameraZoomSupported();
```

返回值:

-   true: 设备支持相机缩放功能
-   false: 设备不支持相机缩放功能


#### 检测设备是否支持闪光灯常开 (isCameraTorchSupported)

```
public abstract boolean isCameraTorchSupported();
```

返回值:

-   true: 设备支持闪光灯常开
-   false: 设备不支持闪光灯常开

> 一般情况下，App 默认开启前置摄像头，因此如果你的前置摄像头不支持闪光灯常开，直接使用该方法会返回 false。如果需要检查后置摄像头是否支持闪光灯常开，需要先使用 `switchCamera` 切换摄像头，再使用该方法。

#### 检测设备是否支持手动对焦功能 (isCameraFocusSupported)

```
public abstract boolean isCameraFocusSupported();
```

返回值:

-   true: 设备支持手动对焦功能
-   false: 设备不支持手动对焦功能


#### 检测设备是否支持人脸对焦功能 (isCameraAutoFocusFaceModeSupported)

```
public abstract boolean isCameraAutoFocusFaceModeSupported();
```

返回值:

-   true: 设备支持人脸对焦功能;
-   false: 设备不支持人脸对焦功能;


#### 设置相机缩放比例 (setCameraZoomFactor)

```
public abstract int setCameraZoomFactor(float factor);
```

该方法设置相机的缩放比例。

<table>
<colgroup>
<col/>
<col/>
</colgroup>
<tbody>
<tr><td><strong>名称</strong></td>
<td><strong>描述</strong></td>
</tr>
<tr><td>factor</td>
<td>相机缩放比例，有效范围从 1.0 到最大缩放</td>
</tr>
</tbody>
</table>



#### 获取相机支持最大缩放比例 (getCameraMaxZoomFactor)

```
public abstract float getCameraMaxZoomFactor();
```

返回值: 该相机支持的最大缩放比例。

#### 设置手动对焦位置，并触发对焦 (setCameraFocusPositionInPreview)

```
public abstract int setCameraFocusPositionInPreview(float positionX, float positionY);
```

<table>
<colgroup>
<col/>
<col/>
</colgroup>
<tbody>
<tr><td><strong>名称</strong></td>
<td><strong>描述</strong></td>
</tr>
<tr><td>positionX</td>
<td>触摸点相对于视图的横坐标</td>
</tr>
<tr><td>positionY</td>
<td>触摸点相对于视图的纵坐标</td>
</tr>
</tbody>
</table>

#### 设置是否打开闪光灯 (setCameraTorchOn)

```
public abstract int setCameraTorchOn(boolean isOn);
```

<table>
<colgroup>
<col/>
<col/>
</colgroup>
<tbody>
<tr><td><strong>名称</strong></td>
<td><strong>描述</strong></td>
</tr>
<tr><td>isOn</td>
<td><p>是否打开闪光灯:</p>
<div><ul>
<li>true: 打开</li>
<li>false: 关闭</li>
</ul>
</div>
</td>
</tr>
</tbody>
</table>


#### 设置是否开启人脸对焦功能 (setCameraAutoFocusFaceModeEnabled)

```
public abstract int setCameraAutoFocusFaceModeEnabled(boolean enabled);
```

<table>
<colgroup>
<col/>
<col/>
</colgroup>
<tbody>
<tr><td><strong>名称</strong></td>
<td><strong>描述</strong></td>
</tr>
<tr><td>enabled</td>
<td><p>是否开启人脸对焦功能:</p>
<div><ul>
<li>true: 打开</li>
<li>false: 关闭</li>
</ul>
</div>
</td>
</tr>
</tbody>
</table>


### 打开与 Web SDK 的互通 (enableWebSdkInteroperability)

```
public abstract int enableWebSdkInteroperability(boolean enabled);
```

该方法打开或关闭与 Agora Web SDK 的互通。

<table>
<colgroup>
<col/>
<col/>
</colgroup>
<tbody>
<tr><td><strong>名称</strong></td>
<td><strong>描述</strong></td>
</tr>
<tr><td>enabled</td>
<td><p>是否已打开与 Agora Web SDK 的互通：</p>
<div><ul>
<li>true：打开互通</li>
<li>false：关闭互通</li>
</ul>
</div>
</td>
</tr>
<tr><td>返回值</td>
<td><ul>
<li>0：方法调用成功</li>
<li>&lt; 0：方法调用失败</li>
</ul>
</td>
</tr>
</tbody>
</table>

### 设置语音路由

#### 修改默认的语音路由 (setDefaultAudioRouteToSpeakerphone)

```
public abstract int setDefaultAudioRoutetoSpeakerphone(boolean defaultToSpeaker);
```

> -  该方法需要在 `joinChannel` 前设置，否则不生效。


如有必要，调用该方法修改默认的语音路由。默认的语音路由如下:

<table>
<colgroup>
<col/>
<col/>
</colgroup>
<tbody>
<tr><td><strong>频道模式</strong></td>
<td><strong>默认语音路由</strong></td>
</tr>
<tr><td>通信</td>
<td><ul>
<li>语音通话: 听筒</li>
<li>视频通话: 外放</li>
<li>视频通话中关闭视频 <sup>[2]</sup> : 听筒</li>
</ul>
</td>
</tr>
<tr/>
<tr/>
<tr><td>直播</td>
<td>外放</td>
</tr>
<tr><td>游戏语音</td>
<td>外放</td>
</tr>
</tbody>
</table>

> [2] 已调用 disableVideo 或 已调用 muteLocalVideoStream/muteAllRemoteVideoStreams

**如有需要**， 根据下表修改上述默认的语音路由:

<table>
<colgroup>
<col/>
<col/>
</colgroup>
<tbody>
<tr><td><strong>名称</strong></td>
<td><strong>描述</strong></td>
</tr>
<tr><td>defaultToSpeaker</td>
<td><ul>
<li>true: 默认路由改为外放(扬声器)</li>
<li>false: 默认路由改为听筒</li>
</ul>
<p>无论语音是从外放还是听筒出声，如果插上耳机或连接蓝牙，语音路由会发生相应改变。拔出耳机或断开蓝牙后语音路由将恢复成默认路由。</p>
</td>
</tr>
<tr/>
<tr/>
<tr><td>返回值</td>
<td><ul>
<li>0: 方法调用成功</li>
<li>&lt; 0: 方法调用失败</li>
</ul>
</td>
</tr>
<tr/>
</tbody>
</table>



#### 打开扬声器 (setEnableSpeakerphone)

```
public abstract int setEnableSpeakerphone(boolean enabled);
```

该方法将语音路由强制设置为扬声器（外放）。调用该方法后，SDK 将返回 `onAudioRouteChanged` 回调提示状态已更改。


> 在调用该方法前，请先阅读 `setDefaultAudioRouteToSpeakerphone` 里关于默认语音路由的说明，确认是否需要调用该方法。

<table>
<colgroup>
<col/>
<col/>
</colgroup>
<tbody>
<tr><td><strong>名称</strong></td>
<td><strong>描述</strong></td>
</tr>
<tr><td>enabled</td>
<td><ul>
<li><p>true:</p>
<div><ul>
<li>如果用户已在频道内，无论之前语音是路由到耳机，蓝牙，还是听筒，调用该 API 后均会默认切换到从外放(扬声器)出声。</li>
<li>如果用户尚未加入频道，调用该API后，无论用户是否有耳机或蓝牙设备，在加入频道时均会默认从外放(扬声器)出声。</li>
</ul>
</div>
</li>
<li><p>false: 语音会根据默认路由出声，详见 <code>setDefaultAudioRouteToSpeakerphone</code></p>
</li>
</ul>
</td>
</tr>
<tr/>
<tr/>
<tr/>
<tr><td>返回值</td>
<td><ul>
<li>0: 方法调用成功</li>
<li>&lt; 0: 方法调用失败</li>
</ul>
</td>
</tr>
<tr/>
</tbody>
</table>



#### 是否是扬声器状态 (isSpeakerphoneEnabled)

```
public abstract boolean isSpeakerphoneEnabled();
```

该方法检查扬声器是否已开启。

<table>
<colgroup>
<col/>
<col/>
</colgroup>
<tbody>
<tr><td><strong>名称</strong></td>
<td><strong>描述</strong></td>
</tr>
<tr><td>返回值</td>
<td><ul>
<li>true: 表明输出到扬声器</li>
<li>false: 表明输出到非扬声器(听筒，耳机等)</li>
</ul>
</td>
</tr>
</tbody>
</table>



#### 启用耳返监听 (enableInEarMonitoring)

```
public abstract int enableInEarMonitoring(boolean enabled);
```

该方法打开或关闭耳返监听功能。

<table>
<colgroup>
<col/>
<col/>
</colgroup>
<tbody>
<tr><td><strong>名称</strong></td>
<td><strong>描述</strong></td>
</tr>
<tr><td>enabled</td>
<td><ul>
<li>true: 启用耳返监听功能</li>
<li>false: 禁用耳返监听功能(默认)</li>
</ul>
</td>
</tr>
<tr><td>返回值</td>
<td><ul>
<li>0: 方法调用成功</li>
<li>&lt; 0：方法调用失败</li>
</ul>
</td>
</tr>
</tbody>
</table>

### 设置语音音量

#### 启用说话者音量提示 (enableAudioVolumeIndication)

```
public abstract int enableAudioVolumeIndication(int interval, int smooth);
```

该方法允许 SDK 定期向应用程序反馈当前谁在说话以及说话者的音量。启用该方法后，无论频道内是否有人说话，都会在 `onAudioVolumeIndication` 回调中按设置的间隔时间返回音量提示。

<table>
<colgroup>
<col/>
<col/>
</colgroup>
<tbody>
<tr><td><strong>名称</strong></td>
<td><strong>描述</strong></td>
</tr>
<tr><td>interval</td>
<td><p>指定音量提示的时间间隔：</p>
<ul>
<li>&lt;= 0：禁用音量提示功能</li>
<li>&gt; 0：返回音量提示的间隔，单位为毫秒。建议设置到大于 200 毫秒。最小不得少于 10 毫秒，否则会收不到 <code>onAudioVolumeIndication</code> 回调</li>
</ul>
</td>
</tr>
<tr><td>smooth</td>
<td>平滑系数，指定音量提示的灵敏度。取值范围为 [0-10]，建议值为 3，数字越大，波动越灵敏；数字越小，波动越平滑。</td>
</tr>
<tr><td>返回值</td>
<td><ul>
<li>0：方法调用成功</li>
<li>&lt; 0：方法调用失败</li>
</ul>
</td>
</tr>
</tbody>
</table>


#### 设置耳返音量 (setInEarMonitoringVolume)

```
public abstract int setInEarMonitoringVolume(int volume);
```

该方法设置耳返的音量。

<table>
<colgroup>
<col/>
<col/>
</colgroup>
<tbody>
<tr><td><strong>名称</strong></td>
<td><strong>描述</strong></td>
</tr>
<tr><td>volume</td>
<td>设置耳返音量，取值范围在 0 到 100 间，默认值为 100</td>
</tr>
</tbody>
</table>

### 暂停音频/视频流

#### 将自己静音 (muteLocalAudioStream)

```
public abstract int muteLocalAudioStream(boolean muted);
```

静音/取消静音。该方法用于允许/禁止往网络发送本地音频流。

> 该方法不影响录音状态，并没有禁用麦克风。
> 该方法只在频道内有效。用户离开频道后，之前设置的 mute 状态会全部清除。开发者需注意代码逻辑的设计。

<table>
<colgroup>
<col/>
<col/>
</colgroup>
<tbody>
<tr><td><strong>名称</strong></td>
<td><strong>描述</strong></td>
</tr>
<tr><td>muted</td>
<td><ul>
<li>true: 麦克风静音</li>
<li>false: 取消静音</li>
</ul>
</td>
</tr>
<tr/>
<tr><td>返回值</td>
<td><ul>
<li>0: 方法调用成功</li>
<li>&lt; 0: 方法调用失败</li>
</ul>
</td>
</tr>
<tr/>
</tbody>
</table>



#### 静音所有远端音频 (muteAllRemoteAudioStreams)

```
public abstract int muteAllRemoteAudioStreams(boolean muted);
```

该方法用于允许/禁止接收和播放远端用户的音频流，即对所有远端用户进行静音与否。

> 该方法只在频道内有效。用户离开频道后，之前设置的 mute 状态会全部清除。开发者需注意代码逻辑的设计。

<table>
<colgroup>
<col/>
<col/>
</colgroup>
<tbody>
<tr><td><strong>名称</strong></td>
<td><strong>描述</strong></td>
</tr>
<tr><td>muted</td>
<td><ul>
<li>true: 停止接收和播放所有远端音频流</li>
<li>false: 允许接收和播放所有远端音频流</li>
</ul>
</td>
</tr>
<tr><td>返回值</td>
<td><ul>
<li>0: 方法调用成功</li>
<li>&lt; 0: 方法调用失败</li>
</ul>
</td>
</tr>
</tbody>
</table>



#### 静音指定用户音频 (muteRemoteAudioStream)

```
public abstract int muteRemoteAudioStream(int uid, boolean muted);
```

静音指定远端用户/对指定远端用户取消静音。


> 如果之前有调用过 `muteAllRemoteAudioStream` (true) 对所有远端音频进行静音，在调用本 API 之前请确保你已调用 `muteAllRemoteAudioStreams` (false) 。 `muteAllRemoteAudioStreams` 是全局控制，`muteRemoteAudioStream` 是精细控制。
> 该方法只在频道内有效。用户离开频道后，之前设置的 mute 状态会全部清除。开发者需注意代码逻辑的设计。

<table>
<colgroup>
<col/>
<col/>
</colgroup>
<tbody>
<tr><td><strong>名称</strong></td>
<td><strong>描述</strong></td>
</tr>
<tr><td>uid</td>
<td>指定用户 ID</td>
</tr>
<tr><td>muted</td>
<td><ul>
<li>true: 停止接收和播放指定音频流</li>
<li>false: 允许接收和播放指定音频流</li>
</ul>
</td>
</tr>
<tr><td>返回值</td>
<td><ul>
<li>0: 方法调用成功</li>
<li>&lt; 0: 方法调用失败</li>
</ul>
</td>
</tr>
</tbody>
</table>

#### 暂停本地视频流 (muteLocalVideoStream)

```
public abstract int muteLocalVideoStream(boolean muted);
```

该方法暂停发送本地视频流。

> 调用该方法时，SDK 不再发送本地视频流，但摄像头仍然处于工作状态。相比于 `enableLocalVideo (false)` 用于控制本地视频流发送的方法，该方法响应速度更快。该方法不影响本地视频流获取，没有禁用摄像头。
> 该方法只在频道内有效。用户离开频道后，之前设置的 mute 状态会全部清除。开发者需注意代码逻辑的设计。

<table>
<colgroup>
<col/>
<col/>
</colgroup>
<tbody>
<tr><td><strong>名称</strong></td>
<td><strong>描述</strong></td>
</tr>
<tr><td>mute</td>
<td><ul>
<li>true: 不发送本地视频流</li>
<li>false: 发送本地视频流</li>
</ul>
</td>
</tr>
<tr/>
<tr><td>返回值</td>
<td><ul>
<li>0: 方法调用成功</li>
<li>&lt; 0: 方法调用失败</li>
</ul>
</td>
</tr>
<tr/>
</tbody>
</table>



#### 暂停所有远端视频流 (muteAllRemoteVideoStreams)

```
public abstract int muteAllRemoteVideoStreams(boolean muted);
```

该方法用于允许/禁止接收和播放所有人的视频流。

> 该方法只在频道内有效。用户离开频道后，之前设置的 mute 状态会全部清除。开发者需注意代码逻辑的设计。

<table>
<colgroup>
<col/>
<col/>
</colgroup>
<tbody>
<tr><td><strong>名称</strong></td>
<td><strong>描述</strong></td>
</tr>
<tr><td>muted</td>
<td><ul>
<li>true: 停止接收和播放所有远端视频流</li>
<li>false: 允许接收和播放所有远端视频流</li>
</ul>
</td>
</tr>
<tr><td>返回值</td>
<td><ul>
<li>0: 方法调用成功</li>
<li>&lt; 0: 方法调用失败</li>
</ul>
</td>
</tr>
</tbody>
</table>



#### 暂停指定远端视频流 (muteRemoteVideoStream)

```
public abstract int muteRemoteVideoStream(int uid, boolean muted);
```

该方法用于允许/禁止接收指定的远端视频流。


> 如果之前有调用过 `muteAllRemoteVideoStreams` (true) 暂停播放所有远端视频流，在调用本 API 之前请确保你已调用 `muteAllRemoteVideoStreams` (false) 。 `muteAllRemoteVideoStreams` 是全局控制，`muteRemoteVideoStream` 是精细控制。
> 该方法只在频道内有效。用户离开频道后，之前设置的 mute 状态会全部清除。开发者需注意代码逻辑的设计。

<table>
<colgroup>
<col/>
<col/>
</colgroup>
<tbody>
<tr><td><strong>名称</strong></td>
<td><strong>描述</strong></td>
</tr>
<tr><td>uid</td>
<td>指定用户的 uid</td>
</tr>
<tr><td>muted</td>
<td><ul>
<li>true: 停止接收和播放指定用户的视频流</li>
<li>false: 允许接收和播放指定用户的视频流</li>
</ul>
</td>
</tr>
<tr><td>返回值</td>
<td><ul>
<li>0: 方法调用成功</li>
<li>&lt; 0: 方法调用失败</li>
</ul>
</td>
</tr>
</tbody>
</table>


### 混音

#### 开始播放伴奏 (startAudioMixing)

```
public abstract int startAudioMixing(String filePath,
                                     boolean loopback,
                                     boolean replace,
                                     int cycle);
```

指定本地音频文件来和麦克风采集的音频流进行混音和替换(用音频文件替换麦克风采集的音频流)， 可以通过参数选择是否让对方听到本地播放的音频和指定循环播放的次数。该 API 也支持播放在线音乐。


> -   如需调用该方法，请确保使用 Android 4.2 或以上设备，且 API Level >=16。
> -   请在频道内调用该方法，如果在频道外调用该方法可能会出现问题。
> -   如果在模拟器上使用该 API，暂时只支持存放在 /sdcard/ 中的 mp3 文件。


<table>
<colgroup>
<col/>
<col/>
</colgroup>
<tbody>
<tr><td><strong>名称</strong></td>
<td><strong>描述</strong></td>
</tr>
<tr><td>filePath</td>
<td><p>指定需要混音的本地音频文件名和文件路径名:</p>
<ul>
<li>如果用户提供的目录以 /assets/ 开头，则去 assets 里面查找该文件</li>
<li>如果用户提供的目录不是以 /assets/ 开头，一律认为是在绝对路径里查找该文件</li>
</ul>
<p>支持以下音频格式: mp3，aac，m4a, 3gp, wav, flac</p>
</td>
</tr>
<tr/>
<tr/>
<tr/>
<tr><td>loopback</td>
<td><ul>
<li>true: 只有本地可以听到混音或替换后的音频流</li>
<li>false: 本地和对方都可以听到混音或替换后的音频流</li>
</ul>
</td>
</tr>
<tr/>
<tr><td>replace</td>
<td><ul>
<li>true: 音频文件内容将会替换本地录音的音频流</li>
<li>false: 音频文件内容将会和麦克风采集的音频流进行混音</li>
</ul>
</td>
</tr>
<tr/>
<tr><td>cycle</td>
<td><p>指定音频文件循环播放的次数:</p>
<ul>
<li>正整数: 循环的次数</li>
<li>-1：无限循环</li>
</ul>
</td>
</tr>
<tr/>
<tr/>
<tr><td>返回值</td>
<td><ul>
<li>0：方法调用成功</li>
<li>&lt; 0: 方法调用失败</li>
</ul>
</td>
</tr>
<tr/>
</tbody>
</table>



#### 停止播放伴奏 (stopAudioMixing)

```
public abstract int stopAudioMixing();
```

该方法停止播放伴奏。请在频道内调用该方法。

<table>
<colgroup>
<col/>
<col/>
</colgroup>
<tbody>
<tr><td><strong>名称</strong></td>
<td><strong>描述</strong></td>
</tr>
<tr><td>返回值</td>
<td><ul>
<li>0：方法调用成功</li>
<li>&lt; 0: 方法调用失败</li>
</ul>
</td>
</tr>
<tr/>
</tbody>
</table>



#### 暂停播放伴奏 (pauseAudioMixing)

```
public abstract int pauseAudioMixing();
```

该方法暂停播放伴奏。请在频道内调用该方法。

<table>
<colgroup>
<col/>
<col/>
</colgroup>
<tbody>
<tr><td><strong>名称</strong></td>
<td><strong>描述</strong></td>
</tr>
<tr><td>返回值</td>
<td><ul>
<li>0：方法调用成功</li>
<li>&lt; 0: 方法调用失败</li>
</ul>
</td>
</tr>
<tr/>
</tbody>
</table>



#### 恢复播放伴奏 (resumeAudioMixing)

```
public abstract int resumeAudioMixing();
```

该方法恢复混音，继续播放伴奏。请在频道内调用该方法。

<table>
<colgroup>
<col/>
<col/>
</colgroup>
<tbody>
<tr><td><strong>名称</strong></td>
<td><strong>描述</strong></td>
</tr>
<tr><td>返回值</td>
<td><ul>
<li>0：方法调用成功</li>
<li>&lt; 0: 方法调用失败</li>
</ul>
</td>
</tr>
<tr/>
</tbody>
</table>



#### 调节伴奏音量 (adjustAudioMixingVolume)

```
public abstract int adjustAudioMixingVolume(int volume);
```

该方法调节混音里伴奏的音量大小。请在频道内调用该方法。

<table>
<colgroup>
<col/>
<col/>
</colgroup>
<tbody>
<tr><td><strong>名称</strong></td>
<td><strong>描述</strong></td>
</tr>
<tr><td>volume</td>
<td>伴奏音量范围为 0~100。默认 100 为原始文件音量</td>
</tr>
<tr><td>返回值</td>
<td><ul>
<li>0：方法调用成功</li>
<li>&lt; 0: 方法调用失败</li>
</ul>
</td>
</tr>
<tr/>
</tbody>
</table>



#### 获取伴奏时长 (getAudioMixingDuration)

```
public abstract int getAudioMixingDuration();
```

该方法获取伴奏时长，单位为毫秒。请在频道内调用该方法。如果返回值 <0，表明调用失败。

#### 获取伴奏播放进度 (getAudioMixingCurrentPosition)

```
public abstract int getAudioMixingCurrentPosition();
```

该方法获取当前伴奏播放进度，单位为毫秒。请在频道内调用该方法。

#### 拖动语音进度条 (setAudioMixingPosition)

```
public abstract int setAudioMixingPosition(int pos);
```

该方法可以拖动播放音频文件的进度条，这样你可以根据实际情况播放文件，而不是非得从头到尾播放一个文件。

<table>
<colgroup>
<col/>
<col/>
</colgroup>
<tbody>
<tr><td><strong>名称</strong></td>
<td><strong>描述</strong></td>
</tr>
<tr><td>pos</td>
<td>整数。进度条位置，单位为毫秒</td>
</tr>
</tbody>
</table>


### 录音

#### 开始客户端录音 (startAudioRecording)

```
public abstract int startAudioRecording(String filePath, int quality);
```

Agora SDK 支持通话过程中在客户端进行录音。该方法录制频道内所有用户的音频，并生成一个包含所有用户声音的录音文件，录音文件格式可以为:

- wav: 文件大，音质保真度高
- aac: 文件小，有一定的音质保真度损失


请确保应用程序里指定的目录存在且可写。该接口需在加入频道之后调用。如果调用 `leaveChannel()` 时还在录音，录音会自动停止。

<table>
<colgroup>
<col/>
<col/>
</colgroup>
<tbody>
<tr><td><strong>名称</strong></td>
<td><strong>描述</strong></td>
</tr>
<tr><td>filePath</td>
<td>录音文件的本地保存路径，由用户自行指定，需精确到文件名及格式，例如：<code>/dir1/dir2/dir3/audio.aac</code></td>
</tr>
<tr><td>quality</td>
<td><p>录音音质：</p>
<ul>
<li>AUDIO_RECORDING_QUALITY_LOW = 0: 低音质。录制 10 分钟的文件大小为 1.2 M 左右</li>
<li>AUDIO_RECORDING_QUALITY_MEDIUM = 1: 中音质。录制 10 分钟的文件大小为 2 M 左右</li>
<li>AUDIO_RECORDING_QUALITY_HIGH = 2: 高音质。录制 10 分钟的文件大小为 3.75 M 左右</li>
</ul>
</td>
</tr>
<tr><td>返回值</td>
<td><ul>
<li>0：方法调用成功</li>
<li>&lt; 0：方法调用失败</li>
</ul>
</td>
</tr>
</tbody>
</table>



#### 停止客户端录音 (stopAudioRecording)

```
public abstract int stopAudioRecording();
```

停止录音。该接口需要在 `leaveChannel()` 之前调用，不然会在调用 `leaveChannel()` 时自动停止。

<table>
<colgroup>
<col/>
<col/>
</colgroup>
<tbody>
<tr><td><strong>名称</strong></td>
<td><strong>描述</strong></td>
</tr>
<tr><td>返回值</td>
<td><ul>
<li>0: 方法调用成功</li>
<li>&lt; 0: 方法调用失败</li>
</ul>
</td>
</tr>
<tr/>
</tbody>
</table>



#### 调节录音信号音量 (adjustRecordingSignalVolume)

```
public abstract int adjustRecordingSignalVolume(int volume);
```

该方法调节录音信号音量。

<table>
<colgroup>
<col/>
<col/>
</colgroup>
<tbody>
<tr><td><strong>名称</strong></td>
<td><strong>描述</strong></td>
</tr>
<tr><td>volume</td>
<td><p>录音信号音量可在 0~400 范围内进行调节:</p>
<ul>
<li>0: 静音</li>
<li>100: 原始音量</li>
<li>400: 最大可为原始音量的 4 倍(自带溢出保护)</li>
</ul>
</td>
</tr>
<tr/>
<tr/>
<tr/>
<tr><td>返回值</td>
<td><ul>
<li>0: 方法调用成功</li>
<li>&lt; 0: 方法调用失败</li>
</ul>
</td>
</tr>
<tr/>
</tbody>
</table>



#### 调节播放信号音量 (adjustPlaybackSignalVolume)

```
public abstract int adjustPlaybackSignalVolume(int volume);
```

该方法调节播放信号音量。

<table>
<colgroup>
<col/>
<col/>
</colgroup>
<tbody>
<tr><td><strong>名称</strong></td>
<td><strong>描述</strong></td>
</tr>
<tr><td>volume</td>
<td><p>播放信号音量可在 0~400 范围内进行调节:</p>
<ul>
<li>0: 静音</li>
<li>100: 原始音量</li>
<li>400: 最大可为原始音量的 4 倍(自带溢出保护)</li>
</ul>
</td>
</tr>
<tr/>
<tr/>
<tr/>
<tr><td>返回值</td>
<td><ul>
<li>0: 方法调用成功</li>
<li>&lt; 0: 方法调用失败</li>
</ul>
</td>
</tr>
<tr/>
</tbody>
</table>


### 加密

#### 启用内置加密，并设置数据加密密码 (setEncryptionSecret)

```
public abstract int setEncryptionSecret(String secret);
```

在加入频道之前，应用程序需调用 `setEncryptionSecret()` 指定 `secret` 来启用内置的加密功能，同一频道内的所有用户应设置相同的 `secret`。 当用户离开频道时，该频道的 `secret` 会自动清除。如果未指定 `secret` 或将 `secret` 设置为空，则无法激活加密功能。

<table>
<colgroup>
<col/>
<col/>
</colgroup>
<tbody>
<tr><td><strong>名称</strong></td>
<td><strong>描述</strong></td>
</tr>
<tr><td>secret</td>
<td>加密密码</td>
</tr>
<tr><td>返回值</td>
<td>
<ul>
<li>0: 方法调用成功</li>
<li>&lt; 0: 方法调用失败</li>
</ul>
</td>
</tr>
</tbody>
</table>



#### 设置内置的加密方案 (setEncryptionMode)

```
public abstract int setEncryptionMode(String encryptionMode);
```

Agora Native SDK 支持内置加密功能，默认使用 `AES-128-XTS` 加密方式。如需使用其他加密方式，可以调用该 API 设置。

同一频道内的所有用户必须设置相同的加密方式和 `secret` 才能进行通话。关于这几种加密方式的区别，请参考 `AES` 加密算法的相关资料。


> 在调用本方法前，请先调用 `setEncryptionSecret()` 启用内置加密功能。

<table>
<colgroup>
<col/>
<col/>
</colgroup>
<tbody>
<tr><td><strong>名称</strong></td>
<td><strong>描述</strong></td>
</tr>
<tr><td>encryptionMode</td>
<td><p>加密方式。目前支持以下几种:</p>
<ul>
<li>“aes-128-xts”: 128 位 AES 加密， XTS 模式</li>
<li>“aes-128-ecb”: 128 位 AES 加密， ECB 模式</li>
<li>“aes-256-xts”: 256 位 AES 加密，XTS 模式</li>
<li>“”: 设置为空字符串时，使用默认加密方式 “aes-128-xts”</li>
</ul>
</td>
</tr>
<tr/>
<tr/>
<tr/>
<tr/>
<tr><td>返回值</td>
<td><ul>
<li>0: 方法调用成功</li>
<li>&lt; 0: 方法调用失败</li>
</ul>
</td>
</tr>
<tr/>
</tbody>
</table>


### 建立数据流

#### 创建数据流 (createDataStream)

```
public abstract int createDataStream(boolean reliable, boolean ordered);
```

该方法用于创建数据流。频道内每人最多只能创建 5 个数据流。频道内数据通道最多允许数据延迟 5 秒，若超过 5 秒接收方尚未收到数据流，则数据通道会向应用程序报错。

> 请将 `reliable` 和 `ordered` 同时设置为 true 或 false, 暂不支持交叉设置。

<table>
<colgroup>
<col/>
<col/>
</colgroup>
<tbody>
<tr><td>名称</td>
<td>描述</td>
</tr>
<tr><td>reliable</td>
<td><ul>
<li>true：接收方会在 5 秒内收到发送方所发送的数据，否则会收到 onStreamMessageError 回调获得相应报错信息。</li>
<li>false：接收方不保证收到，就算数据丢失也不会报错。</li>
</ul>
</td>
</tr>
<tr><td>ordered</td>
<td><ul>
<li>true：接收方 5 秒内会按照发送方发送的顺序收到数据包。</li>
<li>false：接收方不保证按照发送方发送的顺序收到数据包。</li>
</ul>
</td>
</tr>
<tr><td>返回值</td>
<td><ul>
<li>&lt; 0: 创建数据流失败的错误码 <sup>[4]</sup></li>
<li>&gt; 0: 数据流 ID</li>
</ul>
</td>
</tr>
</tbody>
</table>



> [4] 返回的错误码是负数，对应错误代码和警告代码里的正整数。例如返回的错误码为 -2，则对应错误代码和警告代码里的 2: ERR_INVALID_ARGUMENT 。

#### 发送数据流 (sendStreamMessage)

```
public abstract int sendStreamMessage(int streamId, byte[] message);
```

该方法发送数据流消息到频道内所有用户。频道内每秒最多能发送 30 个包，且每个包最大为 1 KB。 API 须对数据通道的传送速率进行控制: 每个客户端每秒最多能发送 6 KB 数据。频道内每人最多能同时有 5 个数据通道。

<table>
<colgroup>
<col/>
<col/>
</colgroup>
<tbody>
<tr><td><strong>名称</strong></td>
<td><strong>描述</strong></td>
</tr>
<tr><td>streamId</td>
<td>数据流 ID，<code>createDataStream()</code> 的返回值。</td>
</tr>
<tr><td>message</td>
<td>待发送的数据</td>
</tr>
<tr><td>返回值</td>
<td><ul>
<li>0: 发送成功</li>
<li>&lt; 0: 发送失败，返回错误码
<ul>
<li>ERR_SIZE_TOO_LARGE (114)</li>
<li>ERR_TOO_OFTEN (12)</li>
<li>ERR_BITRATE_LIMIT (115)</li>
<li>ERR_INVALID_ARGUMENT (2)</li>
</ul>
</li>
</ul>
</td>
</tr>
</tbody>
</table>


### 测试与检测

#### 开始语音通话测试 (startEchoTest)

```
public abstract int startEchoTest();
```

该方法启动语音通话测试，目的是测试系统的音频设备（耳麦、扬声器等）和网络连接是否正常。 在测试过程中，用户先说一段话，在 10 秒后，声音会回放出来。如果 10 秒后用户能正常听到自己刚才说的话，就表示系统音频设备和网络连接都是正常的。

> - 调用 startEchoTest 后必须调用 stopEchoTest 以结束测试，否则不能进行下一次回声测试，或者调用 `joinChannel()` 进行通话。
> - 直播模式下，只有主播用户才能调用。如果用户由通信模式切换到直播模式，请务必调用 `setClientRole()` 方法将用户的橘色设置为。


<table>
<colgroup>
<col/>
<col/>
</colgroup>
<tbody>
<tr><td><strong>名称</strong></td>
<td><strong>描述</strong></td>
</tr>
<tr><td>返回值</td>
<td>
<ul>
<li>0: 方法调用成功</li>
<li>&lt; 0: 方法调用失败
<ul>
<li>ERR_REFUSED (-5)：无法启动测试，可能没有成功初始化</li>
</ul>
</li>
</ul>
</td>
</tr>
<tr/>
<tr/>
</tbody>
</table>



#### 停止语音通话测试 (stopEchoTest)

```
public abstract int stopEchoTest();
```

该方法停止语音通话测试。

<table>
<colgroup>
<col/>
<col/>
</colgroup>
<tbody>
<tr><td><strong>名称</strong></td>
<td><strong>描述</strong></td>
</tr>
<tr><td>返回值</td>
<td>
<ul>
<li>0: 方法调用成功</li>
<li>&lt; 0: 方法调用失败
<ul>
<li>ERR_REFUSED (-5)：不能启动测试，可能语音通话测试没有成功启动</li>
</ul>
</li>
</ul>
</td>
</tr>
<tr/>
<tr/>
</tbody>
</table>

#### 启动网络测试 (enableLastmileTest)

```
public abstract int enableLastmileTest();
```

该方法启用网络连接质量测试，用于检测用户网络接入质量。默认该功能为关闭状态。该方法主要用于以下场景:

用户加入频道前，可以调用该方法判断和预测目前的上行网络质量是否足够好。启用该方法会消耗一定的网络流量，影响通话质量。在收到 `onLastmileQuality` 回调后须调用 `disableLastmileTest()` 停止测试，再加入频道。

<table>
<colgroup>
<col/>
<col/>
</colgroup>
<tbody>
<tr><td><strong>名称</strong></td>
<td><strong>描述</strong></td>
</tr>
<tr><td>返回值</td>
<td><ul>
<li>0: 方法调用成功</li>
<li>&lt; 0: 方法调用失败</li>
</ul>
</td>
</tr>
<tr/>
</tbody>
</table>


#### 禁用网络测试 (disableLastmileTest)

```
public abstract int disableLastmileTest();
```

该方法禁用网络测试。

<table>
<colgroup>
<col/>
<col/>
</colgroup>
<tbody>
<tr><td><strong>名称</strong></td>
<td><strong>描述</strong></td>
</tr>
<tr><td>返回值</td>
<td><ul>
<li>0: 方法调用成功</li>
<li>&lt; 0: 方法调用失败</li>
</ul>
</td>
</tr>
<tr/>
</tbody>
</table>


### 反馈

#### 获取通话 ID (getCallId)

```
public abstract String getCallId();
```

获取当前的通话 ID。

客户端在每次 `joinChannel()` 后会生成一个对应的 CallId，标识该客户端的此次通话。有些方法如 `rate`, `complain` 需要在通话结束后调用，向SDK提交反馈，这些方法必须指定 CallId 参数。使用这些反馈方法，需要在通话过程中调用 `getCallId` 方法获取 CallId，在通话结束后在反馈方法中作为参数传入。

<table>
<colgroup>
<col/>
<col/>
</colgroup>
<tbody>
<tr><td><strong>名称</strong></td>
<td><strong>描述</strong></td>
</tr>
<tr><td>返回值</td>
<td>通话 ID</td>
</tr>
</tbody>
</table>



#### 给通话评分 (rate)

```
public abstract int rate(String callId,
                         int rating,
                         String description);
```

该方法能够让用户为通话评分，一般在通话结束后调用。

<table>
<colgroup>
<col/>
<col/>
</colgroup>
<tbody>
<tr><td><strong>名称</strong></td>
<td><strong>描述</strong></td>
</tr>
<tr><td>callId</td>
<td>通话 getCallId 函数获取的通话 ID</td>
</tr>
<tr><td>rating</td>
<td>给通话的评分，最低 1 分，最高 10 分</td>
</tr>
<tr><td>description</td>
<td>(非必选项) 给通话的描述，可选，长度应小于 800 字节</td>
</tr>
<tr><td>返回值</td>
<td><ul>
<li>0: 方法调用成功</li>
<li>&lt; 0: 方法调用失败
<ul>
<li>ERR_INVALID_ARGUMENT(-2): 传入的参数无效，例如 callId 无效</li>
<li>ERR_NOT_READY(-3)：SDK 不在正确的状态，可能是因为没有成功初始化</li>
</ul>
</li>
</ul>
</td>
</tr>
</tbody>
</table>



#### 投诉通话质量 (complain)

```
public abstract int complain(String callId,
                             String description);
```

该方法让用户就通话质量进行投诉。一般在通话结束后调用。

<table>
<colgroup>
<col/>
<col/>
</colgroup>
<tbody>
<tr><td><strong>名称</strong></td>
<td><strong>描述</strong></td>
</tr>
<tr><td>callId</td>
<td>通话 getCallId 函数获取的通话 ID</td>
</tr>
<tr><td>description</td>
<td>(非必选项) 给通话的描述，可选，长度应小于 800 字节</td>
</tr>
<tr><td>返回值</td>
<td><ul>
<li>0: 方法调用成功</li>
<li>&lt; 0: 方法调用失败
<ul>
<li>ERR_INVALID_ARGUMENT(-2): 传入的参数无效，例如 callId 无效</li>
<li>ERR_NOT_READY(-3)：SDK 不在正确的状态，可能是因为没有成功初始化</li>
</ul>
</li>
</ul>
</td>
</tr>
</tbody>
</table>

### 其他功能

#### 更新 Token (renewToken)

```
public abstract int renewToken(String token);
```

该方法用于更新 Token。如果启用了 Token 机制，过一段时间后使用的 Token 会失效。

当：

-   `onError` 回调报告 ERR_TOKEN_EXPIRED(109) 时，
-   `onRequestToken` 回调报告 ERR_TOKEN_EXPIRED(109) 时，

应用程序应重新获取 Token，然后调用该 API 更新 Token，否则 SDK 无法和服务器建立连接。

<table>
<colgroup>
<col/>
<col/>
</colgroup>
<tbody>
<tr><td><strong>名称</strong></td>
<td><strong>描述</strong></td>
</tr>
<tr><td>token</td>
<td>指定要更新的 Token</td>
</tr>
<tr><td>返回值</td>
<td><ul>
<li>0：方法调用成功</li>
<li>&lt; 0：方法调用不成功</li>
</ul>
</td>
</tr>
</tbody>
</table>



#### 设置日志文件 (setLogFile)

```
public abstract int setLogFile(String filePath);
```

设置 SDK 的输出 log 文件。SDK 运行时产生的所有 log 将写入该文件。应用程序必须保证指定的目录存在而且可写。

<table>
<colgroup>
<col/>
<col/>
</colgroup>
<tbody>
<tr><td><strong>名称</strong></td>
<td><strong>描述</strong></td>
</tr>
<tr><td>filePath</td>
<td>日志文件的完整路径。该日志文件为 UTF-8 编码。</td>
</tr>
<tr><td>返回值</td>
<td><ul>
<li>0:  方法调用成功</li>
<li>&lt; 0: 方法调用失败</li>
</ul>
</td>
</tr>
<tr/>
</tbody>
</table>


Android 平台下日志文件的默认地址为：sdcard/<appname\>/agorasdk.log。appname 为应用名称。

#### 设置日志过滤器 (setLogFilter)

```
public abstract int setLogFilter(int filter);
```

设置 SDK 的输出日志过滤等级。不同的过滤等级可以单独或组合使用。

日志级别顺序依次为 *OFF*、*CRITICAL*、*ERROR*、*WARNING*、*INFO* 和 *DEBUG*。选择一个级别，你就可以看到在该级别之前所有级别的日志信息。

例如，你选择 *WARNING* 级别，就可以看到在 *CRITICAL*、*ERROR* 和 *WARNING* 级别上的所有日志信息。

<table>
<colgroup>
<col/>
<col/>
</colgroup>
<tbody>
<tr><td><strong>名称</strong></td>
<td><strong>描述</strong></td>
</tr>
<tr><td>filter</td>
<td><p>设置过滤器等级:</p>
<div><ul>
<li>LOG_FILTER_OFF = 0：不输出日志信息</li>
<li>LOG_FILTER_DEBUG = 0x80f：输出所有 API 日志信息</li>
<li>LOG_FILTER_INFO = 0x0f：输出 CRITICAL、ERROR、WARNING 和 INFO 级别的日志信息</li>
<li>LOG_FILTER_WARNING = 0x0e：输出 CRITICAL、ERROR 和 WARNING 级别的日志信息</li>
<li>LOG_FILTER_ERROR = 0x0c：输出 CRITICAL 和 ERROR 级别的日志信息</li>
<li>LOG_FILTER_CRITICAL = 0x08：输出 CRITICAL 级别的日志信息</li>
</ul>
</div>
</td>
</tr>
<tr><td>返回值</td>
<td><ul>
<li>0: 方法调用成功</li>
<li>&lt; 0: 方法调用失败</li>
</ul>
</td>
</tr>
</tbody>
</table>

### 销毁引擎实例 (destroy)

```
public static synchronized void destroy();
```

该方法释放 Agora SDK 使用的所有资源。有些应用程序只在用户需要时才进行语音通话，不需要时则将资源释放出来用于其他操作，该方法对这类程序可能比较有用。 只要调用了 `destroy()`, 用户将无法再使用和回调该 SDK 内的其它方法。如需再次使用通信功能，必须重新初始化 sharedEngineWithappId 来新建一个 AgoraRtcEngineKit 实例（instance）。

> -   该方法需要在子线程中操作。
> -   该方法为同步调用。在等待 IRtcEngine 对象资源释放后再返回。APP 不应该在 SDK 产生的回调中调用该接口，否则由于 SDK 要等待回调返回才能回收相关的对象资源，会造成死锁。


### 查询 SDK 版本号 (getSdkVersion)

```
public static String getSdkVersion();
```

该方法返回 SDK 版本号字符串。

<a id = "iaudioeffectmanager"></a>

### 音效管理方法 (IAudioEffectManager)

#### 获取音效音量 (getEffectsVolume)

```
public double getEffectsVolume();
```

该方法获取音效的音量，范围为 [0.0, 100.0]。

#### 设置音效音量 (setEffectsVolume)

```
public int setEffectsVolume(double volume);
```

该方法设置音效的音量。

<table>
<colgroup>
<col/>
<col/>
</colgroup>
<tbody>
<tr><td><strong>名称</strong></td>
<td><strong>描述</strong></td>
</tr>
<tr><td>volume</td>
<td>取值范围为 [0.0, 100.0]。 100.0 为默认值</td>
</tr>
<tr><td>返回值</td>
<td><ul>
<li>0: 方法调用成功</li>
<li>&lt; 0: 方法调用失败</li>
</ul>
</td>
</tr>
<tr/>
</tbody>
</table>



#### 实时调整音效音量 (setVolumeOfEffect)

```
public int setVolumeOfEffect(int soundId, double volume);
```

该方法实时调整指定音效的音量。

<table>
<colgroup>
<col/>
<col/>
</colgroup>
<tbody>
<tr><td><strong>名称</strong></td>
<td><strong>描述</strong></td>
</tr>
<tr><td>soundId</td>
<td>指定音效的 ID。每个音效均有唯一的 ID</td>
</tr>
<tr><td>volume</td>
<td>取值范围为 [0.0, 100.0]。 100.0 为默认值</td>
</tr>
<tr><td>返回值</td>
<td><ul>
<li>0: 方法调用成功</li>
<li>&lt; 0: 方法调用失败</li>
</ul>
</td>
</tr>
<tr/>
</tbody>
</table>



#### 播放指定音效 (playEffect)

```
public int playEffect(int soundId, String filePath, int loopCount, double pitch, double pan, double gain, boolean publish);
```

该方法播放指定音效。

<table>
<colgroup>
<col/>
<col/>
</colgroup>
<tbody>
<tr><td><strong>名称</strong></td>
<td><strong>描述</strong></td>
</tr>
<tr><td>soundId</td>
<td>指定音效的 ID。每个音效均有唯一的 ID <sup>[5]</sup></td>
</tr>
<tr><td>filepath</td>
<td>音效文件的绝对路径</td>
</tr>
<tr><td>loopCount</td>
<td><p>设置音效循环播放的次数：</p>
<div><ul>
<li>0：播放音效一次</li>
<li>1：播放音效两次</li>
<li>-1：无限循环播放音效，直至调用 <code>stopEffect</code> 或 <code>stopAllEffects</code> 后停止</li>
</ul>
</div>
</td>
</tr>
<tr><td>pitch</td>
<td>设置音效的音调
取值范围为 [0.5, 2.0]。默认值为 1.0，表示不需要修改音调。取值越小，则音调越低</td>
</tr>
<tr><td>pan</td>
<td><p>设置是否改变本地听到的音效的空间位置。取值范围为 [-1.0, 1.0]：</p>
<div><ul>
<li>0.0：音效出现在正前方</li>
<li>-1.0：音效出现在左边</li>
<li>1.0：音效出现在右边</li>
</ul>
</div>
</td>
</tr>
<tr><td>gain</td>
<td>设置是否改变单个音效的音量。取值范围为 [0.0, 100.0]。默认值为 100.0。取值越小，则音效的音量越低</td>
</tr>
<tr><td>publish</td>
<td><p>设置是否将音效传到远端：</p>
<div><ul>
<li>true：音效在本地播放的同时，会发布到 Agora 云上，因此远端用户也能听到该音效</li>
<li>false：音效不会发布到 Agora 云上，因此只能在本地听到该音效</li>
</ul>
</div>
</td>
</tr>
<tr><td>返回值</td>
<td><ul>
<li>0：该方法调用成功</li>
<li>&lt; 0：该方法调用不成功</li>
</ul>
</td>
</tr>
</tbody>
</table>



> [5] 如果你已通过 `preloadEffect` 将音效加载至内存，确保这里设置的 `soundId` 与 `preloadEffect` 设置的 `soundId` 相同。
> 该接口在 v2.1.x 及之前的版本中如下所示，不推荐使用。

```
public int playEffect(int soundId, String filePath, boolean loop, double pitch, double pan, double gain);
```

#### 停止播放指定音效 (stopEffect)

```
public int stopEffect(int soundId);
```

该方法停止播放指定音效。

<table>
<colgroup>
<col/>
<col/>
</colgroup>
<tbody>
<tr><td><strong>名称</strong></td>
<td><strong>描述</strong></td>
</tr>
<tr><td>soundId</td>
<td>指定音效的 ID。每个音效均有唯一的 ID</td>
</tr>
<tr><td>返回值</td>
<td><ul>
<li>0: 方法调用成功</li>
<li>&lt; 0: 方法调用失败</li>
</ul>
</td>
</tr>
<tr/>
</tbody>
</table>



#### 停止播放所有音效 (stopAllEffects)

```
public int stopAllEffects();
```

该方法停止播放所有音效。

#### 预加载音效 (preloadEffect)

```
public int preloadEffect(int soundId, String filePath);
```

该方法将指定音效文件(压缩的语音文件)预加载至内存。


> 为保证通信畅通，请注意预加载音效文件的大小，并在 `joinChannel` 前就使用该方法完成音效预加载。

<table>
<colgroup>
<col/>
<col/>
</colgroup>
<tbody>
<tr><td><strong>名称</strong></td>
<td><strong>描述</strong></td>
</tr>
<tr><td>soundId</td>
<td>指定音效的 ID。每个音效均有唯一的 ID</td>
</tr>
<tr><td>filePath</td>
<td>音效文件的绝对路径</td>
</tr>
<tr><td>返回值</td>
<td><ul>
<li>0: 方法调用成功</li>
<li>&lt; 0: 方法调用失败</li>
</ul>
</td>
</tr>
<tr/>
</tbody>
</table>



#### 释放音效 (unloadEffect)

```
public int unloadEffect(int soundId);
```

该方法将指定预加载的音效从内存里释放出来。

<table>
<colgroup>
<col/>
<col/>
</colgroup>
<tbody>
<tr><td><strong>名称</strong></td>
<td><strong>描述</strong></td>
</tr>
<tr><td>soundId</td>
<td>指定音效的 ID。每个音效均有唯一的 ID</td>
</tr>
<tr><td>返回值</td>
<td><ul>
<li>0: 方法调用成功</li>
<li>&lt; 0: 方法调用失败</li>
</ul>
</td>
</tr>
<tr/>
</tbody>
</table>



#### 暂停音效播放 (pauseEffect)

```
public int pauseEffect(int soundId);
```

该方法暂停播放指定音效。

<table>
<colgroup>
<col/>
<col/>
</colgroup>
<tbody>
<tr><td><strong>名称</strong></td>
<td><strong>描述</strong></td>
</tr>
<tr><td>soundId</td>
<td>指定音效的 ID。每个音效均有唯一的 ID</td>
</tr>
<tr><td>返回值</td>
<td><ul>
<li>0: 方法调用成功</li>
<li>&lt; 0: 方法调用失败</li>
</ul>
</td>
</tr>
<tr/>
</tbody>
</table>


#### 暂停所有音效播放 (pauseAllEffects)

```
public int pauseAllEffects();
```

该方法暂停播放所有音效。

<table>
<colgroup>
<col/>
<col/>
</colgroup>
<tbody>
<tr><td><strong>名称</strong></td>
<td><strong>描述</strong></td>
</tr>
<tr><td>返回值</td>
<td><ul>
<li>0: 方法调用成功</li>
<li>&lt; 0: 方法调用失败</li>
</ul>
</td>
</tr>
<tr/>
</tbody>
</table>


#### 恢复播放指定音效 (resumeEffect)

```
public int resumeEffect(int soundId);
```

该方法恢复播放指定音效。

<table>
<colgroup>
<col/>
<col/>
</colgroup>
<tbody>
<tr><td><strong>名称</strong></td>
<td><strong>描述</strong></td>
</tr>
<tr><td>soundId</td>
<td>指定音效的 ID。每个音效均有唯一的 ID</td>
</tr>
<tr><td>返回值</td>
<td><ul>
<li>0: 方法调用成功</li>
<li>&lt; 0: 方法调用失败</li>
</ul>
</td>
</tr>
<tr/>
</tbody>
</table>



#### 恢复播放所有音效 (resumeAllEffects)

```
public int resumeAllEffects();
```

该方法恢复播放所有音效。


<a id = "irtcengineeventhandler"></a>
## 主回调事件 (IRtcEngineEventHandler)

**软件包: io.agora.rtc**

`IRtcEngineEventHandler` 接口类用于SDK向应用程序发送回调事件通知，应用程序通过继承该接口类的方法获取 SDK 的事件通知。

接口类的所有方法都有缺省（空）实现，应用程序可以根据需要只继承关心的事件。在回调方法中，应用程序不应该做耗时或者调用可能会引起阻塞的 API（如 SendMessage），否则可能影响 SDK 的运行。

所有回调在主线程中返回。

#### 加入频道回调 (onJoinChannelSuccess)

```
public void onJoinChannelSuccess(String channel,
                                 int uid,
                                 int elapsed);
```

表示客户端已经登入服务器，且分配了频道 ID 和用户 ID。频道 ID 的分配是根据 `join()` API 中指定的频道名称。如果调用 `join()` 时并未指定用户 ID，服务器就会分配一个。

<table>
<colgroup>
<col/>
<col/>
</colgroup>
<tbody>
<tr><td><strong>名称</strong></td>
<td><strong>描述</strong></td>
</tr>
<tr><td>channel</td>
<td>频道名</td>
</tr>
<tr><td>uid</td>
<td>用户 ID。如果 <code>joinChannel()</code> 中指定了 <code>uid</code>，则此处返回该 ID；否则使用 Agora 服务器自动分配的 ID</td>
</tr>
<tr><td>elapsed</td>
<td>从 <code>joinChannel()</code> 开始到该事件产生的延迟（毫秒)</td>
</tr>
</tbody>
</table>

#### 重新加入频道回调 (onRejoinChannelSuccess)

```
public void onRejoinChannelSuccess(String channel,
                                   int uid,
                                   int elapsed);
```

有时候由于网络原因，客户端可能会和服务器失去连接，SDK 会进行自动重连，自动重连成功后触发此回调方法。

<table>
<colgroup>
<col/>
<col/>
</colgroup>
<tbody>
<tr><td><strong>名称</strong></td>
<td><strong>描述</strong></td>
</tr>
<tr><td>channel</td>
<td>频道名</td>
</tr>
<tr><td>uid</td>
<td>用户 ID 。如果 <code>joinChannel()</code> 中指定了 <code>uid</code>，则此处返回该 ID；否则使用 Agora 服务器自动分配的 ID</td>
</tr>
<tr><td>elapsed</td>
<td>从 <code>joinChannel()</code> 开始到该事件产生的延迟（毫秒)</td>
</tr>
</tbody>
</table>



#### 发生警告回调 (onWarning)

```
public void onWarning(int warn);
```

该回调方法表示 SDK 运行时出现了（网络或媒体相关的）警告。通常情况下，SDK 上报的警告信息应用程序可以忽略，SDK 会自动恢复。 例如和服务器失去连接时，SDK 可能会上报 `ERR_OPEN_CHANNEL_TIMEOUT` 警告，同时自动尝试重连。

<table>
<colgroup>
<col/>
<col/>
</colgroup>
<tbody>
<tr><td>名称</td>
<td>描述</td>
</tr>
<tr><td>warn</td>
<td>警告代码</td>
</tr>
</tbody>
</table>

#### 发生错误回调 (onError)

```
public void onError(int err);
```

表示 SDK 运行时出现了（网络或媒体相关的）错误。 通常情况下，SDK 上报的错误意味着 SDK 无法自动恢复，需要 APP 干预或提示用户。 例如启动通话失败时，SDK 会上报 `ERR_START_CALL` 错误。APP 可以提示用户启动通话失败，并调用 `leaveChannel()` 退出频道。

<table>
<colgroup>
<col/>
<col/>
</colgroup>
<tbody>
<tr><td><strong>名称</strong></td>
<td><strong>描述</strong></td>
</tr>
<tr><td>err</td>
<td>错误代码</td>
</tr>
</tbody>
</table>


#### API 已执行回调 (onApiCallExecuted)

```
public void onApiCallExecuted(int error, String api, String result)
```

当 SDK 执行相应的 API 后，会触发该回调。

<table>
<colgroup>
<col/>
<col/>
</colgroup>
<tbody>
<tr><td><strong>名称</strong></td>
<td><strong>描述</strong></td>
</tr>
<tr><td>error</td>
<td>错误码。如果方法调用失败，会返回错误码；如果返回 0，则表示方法调用成功</td>
</tr>
<tr><td>api</td>
<td>SDK 所调用的 API</td>
</tr>
<tr><td>result</td>
<td>SDK 调用 API 的调用结果</td>
</tr>
</tbody>
</table>



#### 离开频道回调 (onLeaveChannel)

```
public void onLeaveChannel(RtcStats stats);
```

应用程序调用 `leaveChannel()` 方法时，SDK 提示应用程序离开频道成功。在该回调方法中，应用程序可以得到此次通话的总通话时长、SDK 收发数据的流量等信息。

**RtcStats 类定义**

```
public static class RtcStats {
    public int totalDuration; // in seconds
    public int txBytes;
    public int rxBytes;
    public int txKBitRate;
    public int rxKBitRate;
    public int txAudioKBitRate;
    public int rxAudioKBitRate;
    public int txVideoKBitRate;
    public int rxVideoKBitRate;
    public int lastmileDelay;
    public int users;
    public double cpuTotalUsage;
    public double cpuAppUsage;
}
```

<table>
<colgroup>
<col/>
<col/>
</colgroup>
<tbody>
<tr><td>名称</td>
<td>描述</td>
</tr>
<tr><td>stats</td>
<td><p>通话相关的统计信息：</p>
<div><ul>
<li>totalDuration：通话时长，单位为秒，累计值</li>
<li>txBytes：发送字节数 (bytes)，累计值</li>
<li>rxBytes：接收字节数 (bytes)，累计值</li>
<li>txKBitRate：发送码率 (kbps)，瞬时值</li>
<li>rxKBitRate：接收码率 (kbps)，瞬时值</li>
<li>txAudioKBitRate：音频发送码率 (kbps)，瞬时值</li>
<li>rxAudioKBitRate：音频接收码率 (kbps)，瞬时值</li>
<li>txVideoKBitRate：视频发送码率 (kbps)，瞬时值</li>
<li>rxVideoKBitRate：视频接收码率 (kbps)，瞬时值</li>
<li>lastmileDelay：客户端到 vos 服务器的延迟（毫秒）</li>
<li>users：用户离开频道时频道内的瞬时人数</li>
<li>cpuTotalUsage：当前系统的 CPU 使用率 (%)</li>
<li>cpuAppUsage：当前应用程序的 CPU 使用率 (%)</li>
</ul>
</div>
</td>
</tr>
</tbody>
</table>



#### 语音路由已变更回调 (onAudioRouteChanged)

```
public void onAudioRouteChanged(int routing);
```

当调用 `setEnableSpeakerphone` 成功时， SDK 会通过该回调通知 App 语音路由状态已发生变化。该回调返回当前的语音路由已切换至听筒，外放(扬声器)，耳机或蓝牙。 其中 routing 定义如下:

```
public static final int AUDIO_ROUTE_DEFAULT = -1;
public static final int AUDIO_ROUTE_HEADSET = 0;
public static final int AUDIO_ROUTE_EARPIECE = 1;
public static final int AUDIO_ROUTE_HEADSETNOMIC = 2;
public static final int AUDIO_ROUTE_SPEAKERPHONE = 3;
public static final int AUDIO_ROUTE_LOUDSPEAKER = 4;
public static final int AUDIO_ROUTE_HEADSETBLUETOOTH = 5;
```

<table>
<colgroup>
<col/>
<col/>
</colgroup>
<tbody>
<tr><td>routing</td>
<td><p>设置语音路由：</p>
<div><ul>
<li>-1：使用默认的语音路由</li>
<li>0：使用耳机为语音路由</li>
<li>1：使用听筒为语音路由</li>
<li>2：使用不带麦的耳机为语音路由</li>
<li>3：使用手机的扬声器为语音路由</li>
<li>4：使用外接的扬声器为语音路由</li>
<li>5：使用蓝牙耳机为语音路由</li>
</ul>
</div>
</td>
</tr>
</tbody>
</table>


#### 麦克风状态已改变回调 (onMicrophoneEnabled)

```
public void onMicrophoneEnabled(boolean enabled)
```

<table>
<colgroup>
<col/>
<col/>
</colgroup>
<tbody>
<tr><td><strong>名称</strong></td>
<td><strong>描述</strong></td>
</tr>
<tr><td>enabled</td>
<td>
<ul>
<li>true：麦克风已弃用</li>
<li>false：麦克风已禁用</li>
</ul>
</td>
</tr>
</tbody>
</table>

#### 音频质量回调 (onAudioQuality)

```
public void onAudioQuality(int uid,
                           int quality,
                           short delay,
                           short lost);
```

在通话中，该回调方法每两秒触发一次，报告当前通话的音频质量(嘴到耳)。默认启用。

<table>
<colgroup>
<col/>
<col/>
</colgroup>
<tbody>
<tr><td><strong>名称</strong></td>
<td><strong>描述</strong></td>
</tr>
<tr><td>uid</td>
<td>用户 ID</td>
</tr>
<tr><td>quality</td>
<td><p>语音质量：</p>
<ul>
<li>QUALITY_UNKNOWN(0)：网络质量未知</li>
<li>QUALITY_EXCELLENT(1)：网络质量极好</li>
<li>QUALITY_GOOD(2)：用户主观感觉和 excellent 差不多，但码率可能略低于 excellent</li>
<li>QUALITY_POOR(3)：用户主观感受有瑕疵但不影响沟通</li>
<li>QUALITY_BAD(4)：勉强能沟通但不顺畅</li>
<li>QUALITY_VBAD(5)：网络质量非常差，基本不能沟通</li>
<li>QUALITY_DOWN(6)：网络断开，完全无法沟通</li>
</ul>
</td>
</tr>
<tr><td>delay</td>
<td>延迟，单位为毫秒</td>
</tr>
<tr><td>lost</td>
<td>丢包率，单位为百分比</td>
</tr>
</tbody>
</table>



#### 说话声音音量提示回调 (onAudioVolumeIndication)

```
public void onAudioVolumeIndication(AudioVolumeInfo[] speakers, int totalVolume);
```

该回调提示频道内谁在说话以及说话者的音量。默认禁用。可以通过 `enableAudioVolumeIndication` 方法开启；开启后，无论频道内是否有人说话，都会按方法中设置的时间间隔返回提示音量。

<table>
<colgroup>
<col/>
<col/>
</colgroup>
<tbody>
<tr><td><strong>名称</strong></td>
<td><strong>描述</strong></td>
</tr>
<tr><td>speakers</td>
<td><p>说话者（数组）。每个 <code>speaker()</code> 包括：</p>
<ul>
<li>uid：说话者的用户 ID。如果返回的 uid 为 0，则默认为本地用户</li>
<li>volume：说话者的音量（0~255）</li>
</ul>
</td>
</tr>
<tr><td>totalVolume</td>
<td>（混音后的）总音量（0~255）</td>
</tr>
</tbody>
</table>

在返回的 speakers 数组中：

-   如果返回的 uid 为 0，即当频道内的说话者为本地用户时，说话者的音量 `volume` 为 `totalVolume`，即频道内的总音量。
-   如果返回的 uid 不是 0，且 volume 为 0，则默认该 uid 对应的远端用户没有说话。
-   如果有 uid 出现在上次返回的数组中，但不在本次返回的数组中，则默认该 uid 对应的远端用户没有说话。


#### 用户/主播加入频道回调 (onUserJoined)

```
public void onUserJoined(int uid, int elapsed);
```

该回调提示有用户/主播加入了频道，并返回该用户/主播的 ID。如果在加入之前，已经有主播在频道中了，新加入的用户也会收到已有主播加入频道的回调。Agora 建议连麦主播不超过 17 人。

> 直播场景下:
> -   主播间能相互收到新主播加入频道的回调，并能获得该主播的 uid
> -   观众也能收到新主播加入频道的回调，并能获得该主播的 uid
> -   当 Web 端加入直播频道时，只要 Web 端有推流，SDK 会默认该 Web 端为主播，并触发该回调


<table>
<colgroup>
<col/>
<col/>
</colgroup>
<tbody>
<tr><td><strong>名称</strong></td>
<td><strong>描述</strong></td>
</tr>
<tr><td>uid</td>
<td>主播 ID</td>
</tr>
<tr><td>elapsed</td>
<td>从调用 <code>joinChannel()</code> 到触发该回调的延迟(毫秒)</td>
</tr>
</tbody>
</table>



#### 用户/主播离开频道回调 (onUserOffline)

```
public void onUserOffline(int uid, int reason);
```

提示有用户/主播离开了频道（或掉线）。SDK 判断用户/主播离开频道（或掉线）的依据是超时：在一定时间内（15 秒）没有收到对方的任何数据包，判定为对方掉线。在网络较差的情况下，可能会有误报。建议可靠的掉线检测应该由信令来做。

<table>
<colgroup>
<col/>
<col/>
</colgroup>
<tbody>
<tr><td><strong>名称</strong></td>
<td><strong>描述</strong></td>
</tr>
<tr><td>uid</td>
<td>主播 ID</td>
</tr>
<tr><td>reason</td>
<td><p>离线原因:</p>
<ul>
<li>USER_OFFLINE_QUIT(0): 用户主动离开</li>
<li>USER_OFFLINE_DROPPED(1): 因过长时间收不到对方数据包，超时掉线。注意：由于 SDK 使用的是不可靠通道，也有可能对方主动离开本方没收到对方离开消息而误判为超时掉线</li>
<li>USER_OFFLINE_BECOME_AUDIENCE(2): 用户身份从主播切换为观众时触发</li>
</ul>
</td>
</tr>
<tr/>
<tr/>
<tr/>
</tbody>
</table>



#### 用户静音回调 (onUserMuteAudio)

```
public void onUserMuteAudio(int uid, boolean muted);
```

提示有其他用户将他的音频流静音/取消静音。

<table>
<colgroup>
<col/>
<col/>
</colgroup>
<tbody>
<tr><td><strong>名称</strong></td>
<td><strong>描述</strong></td>
</tr>
<tr><td>uid</td>
<td>用户 ID</td>
</tr>
<tr><td>muted</td>
<td><ul>
<li>true: 该用户已静音音频</li>
<li>false: 该用户已取消音频静音</li>
</ul>
</td>
</tr>
<tr/>
</tbody>
</table>



#### Rtc Engine统计数据回调 (onRtcStats)

```
public void onRtcStats(RtcStats stats);
```

该回调定期上报 Rtc Engine 的运行时的状态，每两秒触发一次。

<table>
<colgroup>
<col/>
<col/>
</colgroup>
<tbody>
<tr><td>名称</td>
<td>描述</td>
</tr>
<tr><td>stats</td>
<td>RtcEngine 数据，详见 <a href="#onleavechannel-live-android"><span>离开频道回调 (onLeaveChannel)</span></a> 中的描述</td>
</tr>
</tbody>
</table>



#### 网络质量报告回调 (onLastmileQuality)

```
public void onLastmileQuality(int quality);
```

报告本地用户的网络质量。当你调用 enableLastmileTest 之后，该回调函数每 2 秒触发一次。

<table>
<colgroup>
<col/>
<col/>
</colgroup>
<tbody>
<tr><td><strong>名称</strong></td>
<td><strong>描述</strong></td>
</tr>
<tr><td>quality</td>
<td><p>网络质量：</p>
<div><ul>
<li>QUALITY_UNKNOWN(0)：网络质量未知</li>
<li>QUALITY_EXCELLENT(1)：网络质量极好</li>
<li>QUALITY_GOOD(2)：用户主观感觉和 excellent 差不多，但码率可能略低于 excellent</li>
<li>QUALITY_POOR(3)：用户主观感受有瑕疵但不影响沟通</li>
<li>QUALITY_BAD(4)：勉强能沟通但不顺畅</li>
<li>QUALITY_VBAD(5)：网络质量非常差，基本不能沟通</li>
<li>QUALITY_DOWN(6)：网络断开，完全无法沟通</li>
</ul>
</div>
</td>
</tr>
</tbody>
</table>



#### 频道内网络质量报告回调 (onNetworkQuality)

```
public void onNetworkQuality(int uid, int txQuality, int rxQuality);
```

该回调每 2 秒触发，向 APP 报告频道内所有用户当前的上行、下行网络质量。

<table>
<colgroup>
<col/>
<col/>
</colgroup>
<tbody>
<tr><td><strong>名称</strong></td>
<td><strong>描述</strong></td>
</tr>
<tr><td>uid</td>
<td>用户 ID。表示该回调报告的是持有该 ID 的用户的网络质量。当 uid 为 0 时，返回的是本地用户的网络质量</td>
</tr>
<tr><td>txQuality</td>
<td><p>该用户的上行网络质量：</p>
<ul>
<li>QUALITY_UNKNOWN(0)：网络质量未知</li>
<li>QUALITY_EXCELLENT(1)：网络质量极好</li>
<li>QUALITY_GOOD(2)：用户主观感觉和 excellent 差不多，但码率可能略低于 excellent</li>
<li>QUALITY_POOR(3)：用户主观感受有瑕疵但不影响沟通</li>
<li>QUALITY_BAD(4)：勉强能沟通但不顺畅</li>
<li>QUALITY_VBAD(5)：网络质量非常差，基本不能沟通</li>
<li>QUALITY_DOWN(6)：网络断开，完全无法沟通</li>
</ul>
</td>
</tr>
<tr><td>rxQuality</td>
<td><p>该用户的下行网络质量：</p>
<ul>
<li>QUALITY_UNKNOWN(0)：网络质量未知</li>
<li>QUALITY_EXCELLENT(1)：网络质量极好</li>
<li>QUALITY_GOOD(2)：用户主观感觉和 excellent 差不多，但码率可能略低于 excellent</li>
<li>QUALITY_POOR(3)：用户主观感受有瑕疵但不影响沟通</li>
<li>QUALITY_BAD(4)：勉强能沟通但不顺畅</li>
<li>QUALITY_VBAD(5)：网络质量非常差，基本不能沟通</li>
<li>QUALITY_DOWN(6)：网络断开，完全无法沟通</li>
</ul>
</td>
</tr>
</tbody>
</table>

#### 远端视频流状态发生改变回调 (onRemoteVideoStateChanged)

```
public void onRemoteVideoStateChanged(int uid, int state);
```

该回调方法表示远端的视频流状态发生了改变。

<table>
<colgroup>
<col/>
<col/>
</colgroup>
<tbody>
<tr><td><strong>名称</strong></td>
<td><strong>描述</strong></td>
</tr>
<tr><td>uid</td>
<td>发生视频流状态改变的远端用户的 ID</td>
</tr>
<tr><td>state</td>
<td><p>远端视频流状态：</p>
<div><ul>
<li>1：远端视频正常</li>
<li>2：远端视频卡住</li>
</ul>
</div>
</td>
</tr>
</tbody>
</table>

#### 连接中断回调 (onConnectionInterrupted)

```
public void onConnectionInterrupted();
```

该回调方法表示 SDK 和服务器失去了网络连接。

与 `onConnectionLost` 回调的区别是: `onConnectionInterrupted` 回调在 SDK 刚失去和服务器连接时触发，`onConnectionLost` 在失去连接且尝试自动重连失败后才触发。 失去连接后，除非 APP 主动调用 leaveChannel()，不然 SDK 会一直自动重连。

#### 连接丢失回调 (onConnectionLost)

```
public void onConnectionLost();
```

在 SDK 和服务器失去了网络连接后，会触发 `onConnectionInterrupted` 回调，并自动重连。如果重连一段时间（默认 10 秒）后仍未连上，会触发该回调。 如果 SDK 在调用 `joinChannel` 后 10 秒内没有成功加入频道，也会触发该回调。 该回调触发后，SDK 仍然会尝试重连，重连成功后会触发 `onRejoinChannelSuccess` 回调。

#### 连接已被禁止回调 (onConnectionBanned)

```
public void onConnectionBanned();
```

当你被服务端禁掉连接的权限时，会触发该回调。

#### 已发送本地音频首帧回调 (onFirstLocalAudioFrame)

```
public void onFirstRemoteAudioFrame(int elapsed)
```

当发送完成本地音频第一帧时，触发此回调。

<table>
<colgroup>
<col/>
<col/>
</colgroup>
<tbody>
<tr><td><strong>名称</strong></td>
<td><strong>描述</strong></td>
</tr>
<tr><td>elapsed</td>
<td>从调用 <code>joinChannel</code>加入频道开始到该回调触发的延迟（毫秒）</td>
</tr>
</tbody>
</table>

#### 已接收远端音频首帧回调 (onFirstRemoteAudioFrame)

```
public void onFirstRemoteAudioFrame(int uid, int elapsed)
```

当接收到远端音频第一帧时，触发此回调。

<table>
<colgroup>
<col/>
<col/>
</colgroup>
<tbody>
<tr><td><strong>名称</strong></td>
<td><strong>描述</strong></td>
</tr>
<tr><td>uid</td>
<td>用户 UID，表示收到的是哪个用户的音频流</td>
</tr>
<tr><td>elapsed</td>
<td>加入频道开始到该回调触发的延迟（毫秒）</td>
</tr>
</tbody>
</table>


#### 已发送本地视频首帧回调 (onFirstLocalVideoFrame)

```
public void onFirstLocalVideoFrame(int width, int height, int elapsed);
```

提示第一帧本地视频画面已经显示在屏幕上。

<table>
<colgroup>
<col/>
<col/>
</colgroup>
<tbody>
<tr><td><strong>名称</strong></td>
<td><strong>描述</strong></td>
</tr>
<tr><td>width</td>
<td>视频流宽(像素)</td>
</tr>
<tr><td>height</td>
<td>视频流高(像素)</td>
</tr>
<tr><td>elapsed</td>
<td>从调用 <code>joinChannel</code> 方法加入频道开始到该回调触发的延迟（毫秒）。如果在 <code>joinChannel</code> 之前调用了 <code>startPreview</code>，则返回的是从调用 <code>startPreview</code> 直至该回调被触发的延迟（毫秒）</td>
</tr>
</tbody>
</table>

#### 已完成远端视频首帧解码回调 (onFirstRemoteVideoDecoded)

```
public void onFirstRemoteVideoDecoded(int uid, int width, int height, int elapsed);
```

收到第一帧远程视频流并解码成功时，触发此调用。应用程序可以在此回调中设置该用户的视图。

<table>
<colgroup>
<col/>
<col/>
</colgroup>
<tbody>
<tr><td><strong>名称</strong></td>
<td><strong>描述</strong></td>
</tr>
<tr><td>uid</td>
<td>用户 ID，指定是哪个用户的视频流</td>
</tr>
<tr><td>width</td>
<td>视频流宽(像素)</td>
</tr>
<tr><td>height</td>
<td>视频流高(像素)</td>
</tr>
<tr><td>elapsed</td>
<td>从调用 <code>joinChannel</code>加入频道开始到该回调触发的延迟(毫秒)</td>
</tr>
</tbody>
</table>

#### 已显示远端视频首帧回调 (onFirstRemoteVideoFrame)

```
public void onFirstRemoteVideoFrame(int uid, int width, int height, int elapsed);
```

第一帧远程视频显示在视图上时，触发此调用。应用程序可在此调用中获知出图时间（elapsed）。

<table>
<colgroup>
<col/>
<col/>
</colgroup>
<tbody>
<tr><td><strong>名称</strong></td>
<td><strong>描述</strong></td>
</tr>
<tr><td>uid</td>
<td>用户 ID，指定是哪个用户的视频流</td>
</tr>
<tr><td>width</td>
<td>视频流宽(像素)</td>
</tr>
<tr><td>height</td>
<td>视频流高(像素)</td>
</tr>
<tr><td>elapsed</td>
<td>从调用 <code>joinChannel</code> 加入频道开始到该回调触发的延迟(毫秒)</td>
</tr>
</tbody>
</table>


#### 远端用户静音回调 (onUserMuteAudio)

```
public void onUserMuteAudio(int uid, boolean muted);
```

提示有其他用户将他的音频流静音/取消静音。

<table>
<colgroup>
<col/>
<col/>
</colgroup>
<tbody>
<tr><td>名称</td>
<td>描述</td>
</tr>
<tr><td>uid</td>
<td>用户 ID，提示是哪个用户的音频流</td>
</tr>
<tr><td>muted</td>
<td><ul>
<li>true：该用户已暂停发送音频流</li>
<li>false：该用户已恢复发送音频流</li>
</ul>
</td>
</tr>
</tbody>
</table>


#### 其他用户已停发/已重发视频流回调 (onUserMuteVideo)

```
public void onUserMuteVideo(int uid, boolean muted);
```

提示有其他用户暂停/恢复了视频流的发送。

<table>
<colgroup>
<col/>
<col/>
</colgroup>
<tbody>
<tr><td>名称</td>
<td>描述</td>
</tr>
<tr><td>uid</td>
<td>用户 ID，提示是哪个用户的视频流</td>
</tr>
<tr><td>muted</td>
<td><ul>
<li>true：该用户已暂停发送视频流</li>
<li>false：该用户已恢复发送视频流</li>
</ul>
</td>
</tr>
</tbody>
</table>

#### 其他用户启用/关闭视频 (onUserEnableVideo)

```
public void onUserEnableVideo(int uid, boolean enabled);
```

提示有其他用户启用/关闭了视频功能。关闭视频功能是指该用户只能进行语音通话，不能显示、发送自己的视频，也不能接收、显示别人的视频。

<table>
<colgroup>
<col/>
<col/>
</colgroup>
<tbody>
<tr><td>名称</td>
<td>描述</td>
</tr>
<tr><td>uid</td>
<td>用户 ID，提示是哪个用户的视频流</td>
</tr>
<tr><td>enabled</td>
<td><ul>
<li>true：该用户已启用视频功能。启用后，该用户可以进行视频通话或直播</li>
<li>false：该用户已关闭视频功能。关闭后，该用户只能进行语音通话或直播，不能显示、发送自己的视频，也不能接收、显示别人的视频</li>
</ul>
</td>
</tr>
</tbody>
</table>

#### 其他用户启用/关闭本地视频 (onUserEnableLocalVideo)

```
public void onUserEnableLocalVideo(int uid, boolean enabled)
```

提示有其他用户启用/关闭了本地视频功能。

<table>
<colgroup>
<col/>
<col/>
</colgroup>
<tbody>
<tr><td>名称</td>
<td>描述</td>
</tr>
<tr><td>uid</td>
<td>用户 ID，提示是哪个用户的视频流</td>
</tr>
<tr><td>enabled</td>
<td><ul>
<li>true：该用户已启用本地视频功能。启用后，其他用户可以接收到该用户的视频流</li>
<li>false：该用户已关闭视频功能。关闭后，该用户仍然可以接收其他用户的视频流，但其他用户接收不到该用户的视频流</li>
</ul>
</td>
</tr>
</tbody>
</table>

#### 本地视频统计回调 (onLocalVideoStats)

```
public void onLocalVideoStats(LocalVideoStats stats) {
};
```

报告更新本地视频统计信息，该回调函数每两秒触发一次。LocalVideoStats 定义如下:

```
public static class LocalVideoStats {
    public int sentBitrate;
    public int sentFrameRate;
}
```

<table>
<colgroup>
<col/>
<col/>
</colgroup>
<tbody>
<tr><td><strong>名称</strong></td>
<td><strong>描述</strong></td>
</tr>
<tr><td>stats</td>
<td><p>本地视频相关的统计信息，包含:</p>
<ul>
<li>sentBitrate:（上次统计后）发送的码率(kbps)</li>
<li>sentFrameRate:（上次统计后）发送的帧率(fps)</li>
</ul>
</td>
</tr>
<tr/>
<tr/>
</tbody>
</table>

#### 远端视频统计回调 (onRemoteVideoStats)

```
public void onRemoteVideoStats(RemoteVideoStats stats) {
};
```

SDK 定期向应用程序报告远程视频流的统计信息。该回调函数针对每个远端主播每 2 秒触发一次。如果远端同时存在多个主播，该回调每 2 秒会被触发多次。

```
public static class RemoteVideoStats {
       public int uid;
       public int delay;
       public int width;
       public int height;
       public int receivedBitrate;
       public int receivedFrameRate;
       public int rxStreamType;
 }
```

<table>
<colgroup>
<col/>
<col/>
</colgroup>
<tbody>
<tr><td>名称</td>
<td>描述</td>
</tr>
<tr><td>stats</td>
<td><p>远端视频相关的统计信息，包含：</p>
<ul>
<li>uid: 用户ID，指定是哪个用户的视频流</li>
<li>delay: 延迟（毫秒）</li>
<li>width: 远端视频流宽度</li>
<li>height: 远端视频流高度</li>
<li>receivedBitrate: 接收码率(kbps)</li>
<li>receivedFrameRate: 接收帧率(fps)</li>
<li>rxStreamType: 视频流类型，大流或小流</li>
</ul>
</td>
</tr>
</tbody>
</table>

#### 摄像头启用回调 (onCameraReady)

```
public void onCameraReady();
```

提示已成功打开摄像头，可以开始捕获视频。如果摄像头打开失败，可在 `onError()` 中处理相应错误。

#### 视频功能停止回调 (onVideoStopped)

```
public void onVideoStopped();
```

提示视频功能已停止。应用程序如需在停止视频后对 view 做其他处理（例如显示其他画面），可以在这个回调中进行。

#### 接收到对方数据流消息的回调 (onStreamMessage)

```
public void onStreamMessage(int uid, int streamId, byte[] data);
```

该回调表示已在 5 秒内按照顺序收到了对方发送的数据包。

<table>
<colgroup>
<col/>
<col/>
</colgroup>
<tbody>
<tr><td><strong>名称</strong></td>
<td><strong>描述</strong></td>
</tr>
<tr><td>uid</td>
<td>用户 ID</td>
</tr>
<tr><td>streamId</td>
<td>数据流 ID</td>
</tr>
<tr><td>data</td>
<td>接收到的数据</td>
</tr>
</tbody>
</table>

#### 接收对方数据流消息错误的回调 (onStreamMessageError)

```
public void onStreamMessageError(int uid, int streamId, int error, int missed, int cached);
```

该回调表示没有在 5 秒内收到对方发送的数据包。

<table>
<colgroup>
<col/>
<col/>
</colgroup>
<tbody>
<tr><td><strong>名称</strong></td>
<td><strong>描述</strong></td>
</tr>
<tr><td>uid</td>
<td>用户 ID</td>
</tr>
<tr><td>streamId</td>
<td>数据流 ID</td>
</tr>
<tr><td>error</td>
<td><ul>
<li>ERR_OK = 0, 没有错误</li>
<li>ERR_NOT_IN_CHANNEL=113，用户不在频道内</li>
</ul>
<p>更多错误码描述，详见 错误代码和警告代码</p>
</td>
</tr>
<tr/>
<tr/>
<tr><td>missed</td>
<td>丢失的消息数量</td>
</tr>
<tr><td>cached</td>
<td>数据流中断时，后面缓存的消息数量</td>
</tr>
</tbody>
</table>


#### 监测到活跃用户回调 (onActiveSpeaker)

```
public void onActiveSpeaker(int uid);
```

如果用户开启了 `enableAudioVolumeIndication` 功能，则当音量检测模块监测到频道内有新的活跃用户说话时，会通过本回调返回该用户的 uid。

<table>
<colgroup>
<col/>
<col/>
</colgroup>
<tbody>
<tr><td><strong>名称</strong></td>
<td><strong>描述</strong></td>
</tr>
<tr><td>uid</td>
<td>当前时间段声音最大的用户的 uid。如果返回的 uid 为 0，则默认为本地用户</td>
</tr>
</tbody>
</table>


> -   你需要开启 `enableAudioVolumeIndication` 方法才能收到该回调。
> -   uid 返回的是当前时间段内声音最大的用户 uid，而不是瞬时声音最大的用户 uid。


#### Token 已过期回调 (onRequestToken)

```
public void onRequestToken();
```

在调用 `joinChannel()` 时如果指定了 Token，由于 Token 具有一定的时效，在通话过程中 SDK 可能由于网络原因和服务器失去连接，重连时可能需要新的 Token。该回调通知 APP 需要生成新的 Token，并需调用 renewToken() 为 SDK 指定新的 Token。 之前的处理方法是在 onError() 回调报告 ERR_TOKEN_EXPIRED(109)、ERR_INVALID_TOKEN(110) 时，APP 需要生成新的 Token。在新版本中，原来的处理仍然有效，但建议把相关逻辑放进该回调里。

#### 伴奏播放已结束回调 (onAudioMixingFinished)

```
public void onAudioMixingFinished();
```

当调用 `startAudioMixing()` 播放伴奏音乐结束后，会触发该回调。 如果调用 `startAudioMixing()` 失败，会在 `onError` 回调里，返回错误码 *WARN_AUDIO_MIXING_OPEN_ERROR* 。

#### 音效播放已结束回调 (onAudioEffectFinished)

```
public void onAudioEffectFinished(int soundId)
```

当指定的音效播放结束后，会触发该回调。

<table>
<colgroup>
<col/>
<col/>
</colgroup>
<tbody>
<tr><td>名称</td>
<td>描述</td>
</tr>
<tr><td>soundId</td>
<td>指定音效的 ID。每个音效均有唯一的 ID</td>
</tr>
</tbody>
</table>

#### 上下麦回调 (onClientRoleChanged)

```
public void onClientRoleChanged(int oldRole, int newRole);
```

直播场景下，当用户上下麦时会触发此回调，即主播切换为观众时，或观众切换为主播时。

<table>
<colgroup>
<col/>
<col/>
</colgroup>
<tbody>
<tr><td><strong>名称</strong></td>
<td><strong>描述</strong></td>
</tr>
<tr><td>oldRole</td>
<td>切换前的角色</td>
</tr>
<tr><td>newRole</td>
<td>切换后的角色</td>
</tr>
</tbody>
</table>

#### 相机对焦区域已改变回调 (onCameraFocusAreaChanged)

```
public void onCameraFocusAreaChanged (Rect rect){};
```

该回调表示相机的对焦区域发生了改变。

<table>
<colgroup>
<col/>
<col/>
</colgroup>
<tbody>
<tr><td><strong>名称</strong></td>
<td><strong>描述</strong></td>
</tr>
<tr><td>rect</td>
<td>镜头内表示对焦区域的长方形</td>
</tr>
</tbody>
</table>

#### 视频大小改变回调 (onVideoSizeChanged)

```
public void onVideoSizeChanged(int uid, int width, int height, int rotation)
```

该回调用于通知：视频帧的大小已经改变。

<table>
<colgroup>
<col/>
<col/>
</colgroup>
<tbody>
<tr><td><strong>参数</strong></td>
<td><strong>描述</strong></td>
</tr>
<tr><td>uid</td>
<td>User ID 。</td>
</tr>
<tr><td>width</td>
<td>视频帧的宽度 (像素) 。</td>
</tr>
<tr><td>height</td>
<td>视频帧的高度 (像素) 。</td>
</tr>
<tr><td>rotation</td>
<td>视频新的旋转角度。它的值包括： 0, 90, 180, or 270 。默认为 0。</td>
</tr>
</tbody>
</table>






