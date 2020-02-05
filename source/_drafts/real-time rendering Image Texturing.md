---
title: Real-Time Rendering - Image Texturing
tags: [rtr3, render, note]
---

# 纹理采样 - Image Texturing

## 放大纹理 - Magnification
![Figure-6.8](https://blog-res-1301215015.cos.ap-guangzhou.myqcloud.com/resize_Figure-6.8.png)
- 最近邻值 - nearest neighbor
最近邻值因为多个像素对应一个纹素(纹理元素)值会让图片像素化, 有很明显的像素块
- 双线性插值 - bilinear interpolation
取像素点附近的四个纹素点，左上右上右下左下, 做三次线性插值(ct=lt_rt, cb=lb_rb, ct_cb)得到当前纹理颜色
这种方式解决了像素块的问题, 但是会导致画面很模糊, 却少细节, 使用`detail texture`是一个很常见的解决方案.
- 三次方插值 - cubic convolution

参考:[Green, Chris, "Improved Alpha-Tested Magnification for Vector Textures and Special Effects" SIGGRAPH 2007](http://www.valvesoftware.com/publications/2007/SIGGRAPH2007_AlphaTestedMagnification.pdf)

## 缩小纹理 - Minification
![Figure-6.13](https://blog-res-1301215015.cos.ap-guangzhou.myqcloud.com/resize_Figure-6.13.png)
当纹理缩小, 一个像素会覆盖多个纹素, 如果想要得到正确的颜色值, 需要计算每个纹素对像素的贡献度(据说这样做很不高效).
- 最近邻值
上面图片中最上面的是使用此种采样方式, 有明显的锯齿, 移动时会更加明显(temporal aliasing).
- 双线性插值
取附近的四个纹素点做插值, 与放大纹理时做的一样, 但是当一个像素覆盖的纹素超过四个的时候, 就会出现想最近邻采样一样的锯齿(不过会更模糊).

更好的解决方案参见采样和过滤的内容(sampling and filtering)

## Mipmapping(Latin:multum in parvo - many things in a samll place)
生成mipmap时, 小图上的像素值一般取大图四个像素值的平局值.
mipmap的质量有两个关键因素, 一个好的过滤器(filter)和伽马矫正(gamma correction)
- 纹理过滤器这里的水很深, 可以简单参考下[wiki的介绍](https://zh.wikipedia.org/wiki/%E7%BA%B9%E7%90%86%E6%BB%A4%E6%B3%A2)
- 伽马矫正的文章可以参考[冯乐乐的Gamma介绍](https://blog.csdn.net/candycat1992/article/details/46228771)
里面提到因为mipmap的降采样操作是线性空间下处理的, 而存储的纹理值是非线性空间的, 所以需要先转换到线性空间做降采样然后在转换会非线性空间做存储.

使用mipmap最重要的部分是如何采样, 这里有几种常用的方法
先计算mipmap level
- 最近邻(Nearest Point Sampling)如上所述, 直接采样纹素做放大缩小.
- 双线性插值(Bilinear)取level附近的四个纹素做线性插值.
- 三线性插值(Trilinear)对相近的两个level做双线性采样然后把两个值做一次线性插值.
- [各向异性过滤](https://zh.wikipedia.org/wiki/%E5%90%84%E5%90%91%E5%BC%82%E6%80%A7%E8%BF%87%E6%BB%A4)(Anisotropic Filtering)可以很好的表现地面, 墙面等这种与视线有夹角的平面.
图形API会提供一个用户控制变量d(level of detail bias - LOD bias), 这个偏移加上计算出的mipmap level来做采样, 比如d > 0 图片会变的模糊. 用户根据不同的图片类型设置d的值. 为了精细的控制, d的值可以在任何着色器阶段提供.

## Unconstranied Anisotropic Filtering(无约束各向异性过滤)
什么叫各向异性? 各向异性表示纹理在水平和竖直方向的缩放比不一样, 比如把一个平行于xy平面的平面绕x轴旋转, 假设x轴方向的缩放比位px, y轴方向的缩放比位py, px/py总是大于1的.
而我们的前面说的最近邻、双线性过滤、三线性过滤对两个方向的缩放比总是一致的(各项同性 - isotropic). 从像素的uv在纹理上的投影看来, 各向同性的投影总是正方形, 而各向异性可以是长方形、梯形或者其他的形状. 各向异性的实现可以看"implementing an anisotropic texture filter"这篇论文
参考: [An introduction to shader derivative functions](http://www.aclockworkberry.com/shader-derivative-functions/)介绍了ddx, ddy
[这篇blog](http://blog.sina.com.cn/s/blog_7cb69c550102xvog.html)翻译了上面这篇

## Volume Texture
