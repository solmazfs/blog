---
title: "Challenge 1-2: Marquee effect&Cube animation"
date: 2022-09-28T14:10:52+03:00
categories: ["challenges"]
tags: [raylib,game-dev]
description: "Marquee effect for text animation and drawing 3d looking cube."
ready: "in progress"
draft: false
---

In this challenge 1, the goal is make text infinite scrolling along the y axis.
It might be usefull for intro or something.
I've also add animating 3d looking cube for making it more interesting and this should be challenge 2.
For these i've used "raylib" videogame library.

### The code:
```text
#include <stdio.h>
#include <math.h> // calculating sin and cos
#include "raylib.h"

int main() {
    const int screenWidth = 512;
    const int screenHeight = 512;
    const int canvasWidth = 128;
    const int canvasHeight = 128;
    InitWindow(screenWidth,screenHeight,"challenge 1-2");
    SetWindowState(FLAG_VSYNC_HINT);
    SetTargetFPS(60);

    // initialize texture & camera
    RenderTexture2D target = LoadRenderTexture(canvasWidth,canvasHeight);
    Rectangle source = {0,-canvasHeight,target.texture.width,-target.texture.height};
    Rectangle dest = {0,0,screenWidth,screenHeight};
    Camera2D camera = {0};
    camera.zoom = 1.0f;

    Font alagard = LoadFont("resources/alagard.png"); // custom font

    char *intro = "HELLO WORLD!";
    float scroll_speed = 1.0f;
    float current_left = canvasWidth+8;
    float left_corner = -(MeasureText(intro, alagard.baseSize*1.0f)-8);

    // cube timer & drawing position
    float t = 0;
    float rotation_speed = 0.03f;
    Vector2 p[4];

    while (!WindowShouldClose()) {
        // scroll timer
        current_left -= scroll_speed;
        if (current_left <= left_corner ) {
            current_left = canvasWidth+8;
        }
        // cube timer - "not perfect one"
        t += rotation_speed;
        if (t >=1.5f) {
            t = 0;
        }
        BeginTextureMode(target);
            ClearBackground(RAYWHITE);
            BeginMode2D(camera);
                DrawFPS(4,4);
                // drawing rectangle - for the bg and seeing pixel scale
                Rectangle rec = {(float)128/2,(float)128/2-40,60,60};
                Vector2 og = {0,0};
                DrawRectanglePro(rec,og,45,BLACK);
                // drawing animated 3d looking cube
                for (int i=0; i<4; i++) {
                    float x = 64+cos(t+((float)i/4.0*M_PI*2))*20;
                    float y = 56+sin(t+((float)i/4.0*M_PI*2))*20;
                    p[i] = (Vector2){x,y};
                }
                for (int i=1; i<5; i++) {
                    int j = i%4+1;
                    DrawLine(p[i-1].x,p[i-1].y,p[i-1].x,p[i-1].y+20,RAYWHITE);
                    DrawLine(p[i-1].x,p[i-1].y,p[j-1].x,p[j-1].y,RAYWHITE);
                    DrawLine(p[i-1].x,p[i-1].y+20,p[j-1].x,p[j-1].y+20,RAYWHITE);
                }
                // drawing text with custom font
                DrawTextEx(alagard,intro,(Vector2){current_left,108},alagard.baseSize*1.0f,1.0f,RED);
            EndMode2D();
        EndTextureMode();

        BeginDrawing();
            DrawTexturePro(target.texture,source,dest,(Vector2){0,0},0,WHITE);
        EndDrawing();
    }
    UnloadRenderTexture(target);
    UnloadFont(alagard);
    CloseWindow();
    return 0;
}
```

### The output will look like this:

<small>*drop fps happened while recording.</small>

<img alt="folder" src="/images/game-dev/raylib_marquee-output.gif" width="300px" loading="lazy"/>

### References
<ref id="ref1">This is the reference i've used for the pixel perfect camera: [smooth pixel-perfect camera](https://www.raylib.com/examples/core/loader.html?name=core_smooth_pixelperfect)</ref>
<ref id="ref2">Custom font image from raylib repo: [alagard.png](https://github.com/raysan5/raylib/blob/0abba4dc1854e71f58b252e840058178ea845e25/examples/text/resources/fonts/alagard.png)</ref>
<ref id="ref2">This is the reference i've used for the 3d cube animation: [PICO8 (fake 3D?) CAD BGF 2019](https://www.youtube.com/watch?v=HIFhRacJXPY)</ref>

