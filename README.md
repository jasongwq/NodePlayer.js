# NodePlayer.js
Pure JavaScrip live stream player, 100% written in c/c++. :joy:

## 商用优化版
http://www.nodemedia.cn/products/node-media-player/

## 简介
本项目使用emscripten技术，完全采用c++编写，最终编译为js代码。  
只使用到ffmpeg库的libavcodec，libavutil，libswresample库。  
只编译了h264,hevc,mp3,aac,nellymoser解码器，因此也算比较小巧了。  
最终-Os级编译出2.9M，经测试，基本兼容windows，linux，mac，android，ios。  
视频渲染完全由WebGL实现，性能较其他js解码器的YUV转RGB更高。640x480@30 在iphone5s上能流畅播放。

## 用途
本项目是一个实验型的开发，只是证明，可以这么实现:joy:，实际用处或许不大。  
唯二的优势或许是 iOS浏览器环境(包括微信和QQ)内低延迟(小于2秒)视频直播、能解hevc直播

但经过两周的开发，基本上摸熟了Emscripten的开发模式，真的是非常好的一个技术。源码我就不提供了，感兴趣或正在研究Emscripten可以和我技术讨论，我写了好几个版本。 
- pthread版的本来都要写完了，发现webworker对共享对象支持不好弃用。
- coroutine版也差不多写了一半，发现 -s ASYNCIFY=1 后生成的体积增大几倍，弃用。
- 当前demo版是socket_onmessage_callback + main_loop + SDL_callback的版本，基本上满足最初设想

## 特性
- 仅支持WebSocket的传输协议
- 仅支持Flv封装
- 首屏启动低于500毫秒，延迟低于2秒
- 支持H.264/H.265 视频软解码
- 支持AAC/MP3/NellyMoser 音频软解码
- 支持iOS/Android原生浏览器或WebView控件
- 支持微信、QQ内部打开
- 视频渲染 WebGL
- 音频播放 WebAudio
- 全解码版js小于3M
- 支持平铺，等比缩放，拉伸填充的视频缩放模式

## 问题
解码性能低下，如何开启ffmpeg的SIMD  
优化思路：Emscripten不能编译.S文件，也不支持inline SIMD assembly。但是支持C API的 SSE1, SSE2, SSE3, SSSE3指令集。对h264addpx_template.c,h264chroma_template.c, h264dsp_template.c, h264idct_template.c, h264pred_template.c, h264qpel_template.c 进行SIMD移植。AAC,HEVC解码器也是相同原理。  
官方文档说编译ARM NEON指令集(#include <arm_neon.h>)代码还不支持，但也可能行。   
Intel有一个项目[ARM_NEON_2_x86_SSE](https://github.com/intel/ARM_NEON_2_x86_SSE) 是否可以考虑使用NEON的API进行移植开发，后期如果Emscripte开始支持NEON则可自动支持。  
emscripten/vector.h 中直接映射了SIMD.js的API，根据[这篇介绍](https://hacks.mozilla.org/2014/10/introducing-simd-js/) ,应该是同时支持X86平台的SSE和ARM平台的NEON指令集。那直接使用这个api来优化应该更好一点。

## 支持的服务端
[Node-Media-Server](https://github.com/illuspas/Node-Media-Server)

## 开发历程
 - [x] WebSocket网络传输
 - [x] FLV解析
 - [x] 音频解码与播放
 - [x] 视频解码与渲染
 - [ ] 兼容性测试
 - [ ] 性能优化
 
## 与flv.js比较
![img](https://github.com/illuspas/NodePlayer.js/blob/master/nodeplayerjs_flvjs.png)

## 微信内打开
![img](https://github.com/illuspas/NodePlayer.js/blob/master/wechat_desktop.jpg)
