---
title: Real-Time Rendering - Image Texturing
layout: draft
tags: rtr3, render, note
---

# 纹理采样 - Image Texturing
## 放大纹理 - Magnification
![Figure-6.8](images/rtr3/Figure-6.8.png)
- 最近邻值 - nearest neighbor
- 双线性插值 - bilinear interpolation
取像素点附近的四个纹理像素点，左上右上右下左下, 做三次线性插值(ct=lt_rt, cb=lb_rb, ct_cb)得到当前纹理颜色
- 三次方插值 - cubic convolution
Green, Chris, "Improved Alpha-Tested Magnification for Vector Textures and Special Effects" SIGGRAPH 2007 
## 缩小纹理 - 
