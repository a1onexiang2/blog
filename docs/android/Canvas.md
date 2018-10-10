[Home](../../README)
[Back to View](./View.md)

# Android

## Canvas

#### PorterDuffXfermode

`Paint.setXfermode(new PorterDuffXfermode(mode))` 来设置 Xfermode，可取值参照 PorterDuff.Mode 类。

![image](https://user-images.githubusercontent.com/8423120/45747336-1ddccc00-bc38-11e8-8d43-a2f8f1b1c70a.png)

#### Shader

Shader 有五个子类，分别是：BitmapShader、LinearGradient、RadialGradient、SweepGradient 和 ComposeShader。

- **BitmapShader**
用于绘制图片。
构造方法为：`BitmapShader(Bitmap bitmap, Shader.TileMode tileX, Shader.TileMode tileY)`
- **LinearGradient**
用于绘制线性渐变内容。
构造方法有两个，分别为：
`LinearGradient(float x0, float y0, float x1, float y1, int color0, int color1, Shader.TileMode tile)`
`LinearGradient(float x0, float y0, float x1, float y1, int[] colors, float[] positions, Shader.TileMode tile)`
- **RadialGradient**
用于绘制中心渐变效果。
构造方法有两个，分别为：
`RadialGradient(float centerX, float centerY, float radius, int centerColor, int edgeColor, Shader.TileMode tileMode)`
`RadialGradient(float centerX, float centerY, float radius, int[] colors, float[] stops, Shader.TileMode tileMode)`
- **SweepGradient**
用于绘制旋转渐变效果。
构造方法有两个，分别为：
`SweepGradient(float cx, float cy, int color0, int color1)`
`SweepGradient(float cx, float cy, int[] colors, float[] positions)`
- **ComposeShader**
用于混合多个 Shader。
构造方法有两个，分别为：
`ComposeShader(Shader shaderA, Shader shaderB, Xfermode mode)`
`ComposeShader(Shader shaderA, Shader shaderB, PorterDuff.Mode mode)`

#### MaskFilter

通过调用 `Paint.setMaskFilter(maskFilter)` 方法来为 paint 设置。在 GPU 硬件加速模式下，该方法不被 GPU 支持，所以为了能够正常使用，需要将我们要绘制的 View 使用软件渲染模式。MaskFilter 有两个子类，分别是：BlurMaskFilter、EmbossMaskFilter。

- **BlurMaskFilter**
用于创建模糊阴影效果。
构造方法为：`BlurMaskFilter(float radius, BlurMaskFilter.Blur style)`

- **EmbossMaskFilter**
用于创建浮雕效果。
构造方法为：`EmbossMaskFilter(float[] direction, float ambient, float specular, float blurRadius)`

[Back to View](./View.md)
[Home](../../README)