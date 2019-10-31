---
layout: post
title: 유니티에서 Alpha 포함 비디오 사용을 위한 인코딩 옵션
categories: Development, Unity, Alpha Video, ffmpeg
---

```
ffmpeg -i [input file name] -c:v libvpx -pix_fmt yuva420p -b:v 1M -auto-alt-ref 0 -metadata:s:v:0 alpha_mode="1" -c:a libvorbis output.webm
```