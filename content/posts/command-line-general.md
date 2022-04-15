---
title: "Command Line General (Linux)"
date: 2022-04-08T13:53:58+03:00
categories: ["Tutorials & How-to's"]
tags: [linux]
draft: false
---

1. [ffmpeg](#ffmpeg)

## ffmpeg
- cut specific timeline from video
    - ffmpeg -ss < starting point > -i < input > -t < how long > -vcodec copy -acodec copy < output >
    - eg; ffmpeg -ss 00:00:30 -i input.mp4 -t 00:00:05 -vcodec copy -acodec copy output.mp4
---
- merge multiple videos
    - vim files.txt
    ```bash
    file 'vid_1.mp4'
    file 'vid_2.mp4'
    ```
    - ffmpeg -f concat -safe 0 -i files.txt -c copy output.mp4
---
- convert mp4 to mp3
    - ffmpeg -i input.mp4 -vn -acodec libmp3lame -ac 2 -ab 160k -ar 48000 output.mp3

## 
- sync two files
    - rsync ~/.bashrc backup/.bashrc
