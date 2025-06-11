---
title: "Go-To Command Cheat Sheet"
date: 2022-04-08T13:53:58+03:00
draft: false
---

This page is my personal, evolving collection of essential command-line snippets and quick tricks for quick reference.

## imagemagick

```text
# resize half an image
convert -resize 50% input.png output.png

# add border to an image
convert -bordercolor white -border 3 -bordercolor black -border 86 input.png output.png

# image dithering
convert < input .png > -resize < 800x800 > -colorspace gray -ordered-dither o8x8 < output .png >
```

## ffmpeg
```text
# cut specific timeline from video
ffmpeg -ss < starting point > -i < input > -t < how long > -vcodec copy -acodec copy < output >
# e.g.
ffmpeg -ss 00:00:30 -i input.mp4 -t 00:00:05 -vcodec copy -acodec copy output.mp4

# merge multiple videos
vim files.txt
    file 'vid_1.mp4'
    file 'vid_2.mp4'
ffmpeg -f concat -safe 0 -i files.txt -c copy output.mp4

# convert mp4 to mp3
ffmpeg -i input.mp4 -vn -acodec libmp3lame -ac 2 -ab 160k -ar 48000 output.mp3

# convert webm to mp4
ffmpeg -fflags +genpts -i input.webm -r 24 output.mp4

# decrease music volume
ffmpeg -i input.mp3 -filter:a "volume=0.2" -c:a pcm_f32le output.wav

# add music for a video
ffmpeg -i input.mp4 -i input.wav -map 0:v -map 1:a -c:v copy -shortest output.mp4
```

## other 
```text
# image dithering
didder --palette 'black white' -i input.png -o output.png bayer 16x16

# sync two files
rsync ~/.bashrc backup/.bashrc

# get specific page(s) from pdf
pdftk < input > cat < start >-< end > output < output >
```
