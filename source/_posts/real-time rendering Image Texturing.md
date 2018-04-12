---
title: Real-Time Rendering - Image Texturing
layout: draft
tags: rtr3, render, note
---

# 纹理采样 - Image Texturing

## 放大纹理 - Magnification
![Figure-6.8](/images/rtr3/resize_Figure-6.8.png)
- 最近邻值 - nearest neighbor
最近邻值因为多个像素对应一个纹理像素值会让图片像素化, 有很明显的像素块
- 双线性插值 - bilinear interpolation
取像素点附近的四个纹理像素点，左上右上右下左下, 做三次线性插值(ct=lt_rt, cb=lb_rb, ct_cb)得到当前纹理颜色
这种方式解决了像素块的问题, 但是会导致画面很模糊, 却少细节, 使用`detail texture`是一个很常见的解决方案.
- 三次方插值 - cubic convolution

参考:Green, Chris, "Improved Alpha-Tested Magnification for Vector Textures and Special Effects" SIGGRAPH 2007 

## 缩小纹理 - Minification
![Figure-6.13](/images/rtr3/resize_Figure-6.13.png)
当纹理缩小, 一个像素会覆盖多个纹理像素, 如果想要得到正确的颜色值, 需要计算每个纹理像素第像素的贡献度(据说这样做很不高效).
- 最近邻值
上面图片中最上面的是使用此种采样方式, 有明显的锯齿, 移动时会更加明显(temporal aliasing).
- 双线性插值
取附近的四个纹理像素点做插值, 与放大纹理时做的一样, 但是当一个像素覆盖的纹理像素超过四个的时候, 就会出现想最近邻采样一样的锯齿(不过会更模糊).

更好的解决方案参见采样和过滤的内容(sampling and filtering)

## Mipmapping(multum in parvo)
