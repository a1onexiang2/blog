[Home](../../README.md)

# Android

## VideoPlayer

#### MediaPlayer
MediaPlayer 是自带的多媒体播放器，支持大部分音频、视频文件。
MediaPlayer 负责加载源，但不负责呈现，需要调用 `MediaPlayer.setDisplay()` 方法为其设置 Surface（SurfaceView、SurfaceHolder）来展示画面。
自带 `setNextMediaPlayer()` 方法，但要实现分段加载无缝播放，需要在 Listener 中进行大量逻辑操作，不易维护。

#### VideoView
继承自 SurfaceView，内部封装了 MediaPlayer，但是只支持 MP4、3GP 格式。

#### ExoPlayer
ExoPlayer 支持动态的自适应流 HTTP (DASH) 和平滑流，支持任何目前 MediaPlayer 支持的视频格式，同时它还支持 HTTP 直播（HLS）、MP4、MP3、WebM、M4A、MPEG-TS 和 AAC。ExoPlayer 支持高级的 HLS 特性，例如正确处理 EXT-X-DISCONTINUITY 标签。
支持自定义和扩展。

#### JiaoziVideoPlayer
基于 MediaPlayer、Surface 实现的视频播放器库。

#### GSYVideoPlayer
可切换内核的视频播放器，支持 ijkPlayer、ExoPlayer、MediaPlayer 等内核。

#### ijkPlayer
ijkplayer 是可以根据需要编译的解码器。在编译的时候通过 `ln -s module-default.sh module.sh` 选择要编译的解码器。
支持倍速播放，分段播放。

[Home](../../README.md)