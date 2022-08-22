---
title: "Gimp Create Noise/Grain/Dust Texture"
categories: ["tutorial-how-tos"]
tags: [gimp,digital-art]
description: "How to make procedural noise/grain/dust texture with gimp."
date: 2022-08-02T09:53:53+03:00
ready: "in progress"
draft: false
---

Create new document:
    - 720x720 & 300dpi
    - Fill with: Transparency

Filters > Render > Noise > Solid Noise
    - X/Y Size: 16, Detail: 15, Tileable: x, Random seed: 8

Colors > Threshold
    - 43x255

Filters > Noise > CIE Ich Noise
    - Dulling: 1, Lightness: 100, Chroma: 0, Hue: 0, Random seed: 3

More noise > Pick -> play around

Tweaking these may help you get better results.
