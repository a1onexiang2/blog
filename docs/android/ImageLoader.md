[Home](../../README.md)

# Android

## ImageLoader
常用的图片加载库有 Glide、Fresco、Picasso、Universal-Image-Loader。

#### Bitmap 缓存策略
常见的 Bitmap 缓存策略有很多种，常见的有：LRU（最后使用）策略、先进先出策略、最大删除策略、时间过期策略、使用频率最低删除策略、全部弱引用策略。

#### Universal-Image-Loader
最早的图片加载库。年久失修，可视为被废弃。

#### Glide
`Glide.with()` 方法会自动绑定生命周期。
默认加载图片格式为 `RGB_565` 而非 `ARGB_8888`。
缓存时会带上缩放尺寸而非原图，可能会导致重复下载图片，但可以通过设定同时缓存缩略图与原图。
支持 GIF、WebP。
可以加载一些媒体格式的第一帧。
支持 thumbnail 先加载预览图后加载原图。
支持图片加载时的动画效果。

#### Picasso
API 类似 Glide。
库精简，体积小，方法数少。
不支持磁盘缓存。
加载图片只支持渐进动画。

#### Fresco
包较大，配置复杂，需要使用自定义控件 DraweeView，无法使用原生 ImageView。
在 Android 5.0 以下系统，图片不存储在 Java heap，而是存储在 ashmem heap，中间的字节 buffer 同样位于 native heap。使应用有更多内存空间，降低 OOM 风险，减少 GC 次数。
图片可以在任意点进行裁剪，而不是中心，即自定义居中焦点。
JPEG 可以在 native 进行 resize，避免了在缩小图片时的 OOM 风险。


[Home](../../README.md)