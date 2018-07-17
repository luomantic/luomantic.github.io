---
title: Android 5.0(Lollipop)中的SurfaceTexture，TextureView, SurfaceView和GLSurfaceView
tags: hexo
---

* 关键字   
View  
SurfaceView  
GLSurfaceView  
TextureView  
这三个后缀都是View， 所以这三个东西都是用来显示的， 和普通的View差不多， 类似TextView等.

通过历史来说明：  

SurfaceView 出现最早， 解决类似视频播放的问题（可以用单独一个线程来渲染UI）。  

后来发现用起来不方便， 渲染线程需要单独编写， 一大堆都可以独立成模板。  

所以后来就出现了 GLSurfaceView, 概括一句话就是 使用了模板的 SurfaceView。
再后来发现GLSurfaceView不能根据屏幕的变化而变化， 这是由于GLSurfaceView同应用的Surface不是在同一层导致的问题。  

人们就想到把这个GLSurfaceView弄到应用的Surface中， 所以就产生了TextureView.

* 关键字   
Texture  
SurfaceTexture  
这个东西不是用来显示给用户看的， 它是一个Texture, 意识是纹理， 可以想象成一个View的中间件。  
举个例子：  
1 : Camera 把视频采集的内容交给 SurfaceView, 就变成了照相机。  
2：Camera 把视频采集的内容交给 SurfaceTexture， SurfaceTexture 在对内容做个美颜， 然后SurfaceTexture 再把内容交给 SurfaceView， 就变成了美图秀秀。

[https://blog.csdn.net/jinzhuojun/article/details/44062175](https://blog.csdn.net/jinzhuojun/article/details/44062175)