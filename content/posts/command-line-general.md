---
title: "Command Line General (Linux)"
date: 2022-04-08T13:53:58+03:00
description: "Make life easier."
categories: ["Tutorials & How-to's"]
tags: [linux]
ready: "in progress"
draft: false
---

1. [imagemagick](#imagemagick)
1. [didder](#didder)
1. [ffmpeg](#ffmpeg)
1. [other](#other)

### imagemagick
Resize image;
```text
convert -resize 50% input.png output.png
```
Add border to an image;
```text
convert -bordercolor white -border 3 -bordercolor black -border 86 input.png output.png
```

### didder
Image dithering;
```text
didder --palette 'black white' -i input.png -o output.png bayer 16x16
```

### ffmpeg
Cut specific timeline from video;
```text
ffmpeg -ss < starting point > -i < input > -t < how long > -vcodec copy -acodec copy < output >
#e.g.
ffmpeg -ss 00:00:30 -i input.mp4 -t 00:00:05 -vcodec copy -acodec copy output.mp4
```

Merge multiple videos;
```text
vim files.txt
    file 'vid_1.mp4'
    file 'vid_2.mp4'
ffmpeg -f concat -safe 0 -i files.txt -c copy output.mp4
```

Convert mp4 to mp3;
```text
ffmpeg -i input.mp4 -vn -acodec libmp3lame -ac 2 -ab 160k -ar 48000 output.mp3
```

### other 
sync two files;
```text
rsync ~/.bashrc backup/.bashrc
```
Get specific page(s) from pdf;
```text
pdftk < input > cat < start >-< end > output < output >
```
