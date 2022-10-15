---
title: "Mixedlog"
date: 2022-10-06T19:17:44+03:00
ready: "!!!"
description: "..."
draft: false
---


# 2022
## October

[Blender] My project gets harder.

{{< image_render face_new.png 140 "face topology new" >}}
{{< image_render face_old.png 140 "face topology old" >}}
{{< image_render knee_part.png 140 "knee topology" >}}

[Orca & Surge XT] - Kinda good tho ...

{{< audio test1.mp3 >}}

[raylib] - 4 direction movement logic(grid).

```text
enum MOVE {UP,DOWN,LEFT,RIGHT};
Vector2 move_dir[4] = {{0,-1},{0,1},{-1,0},{1,0}};
int move_dir_size = sizeof(move_dir)/sizeof(move_dir[0]);
for (int i=0; i<move_dir_size; i++) {
    move_dir[i] = Vector2Scale(move_dir[i],16);
}

// move e.g
position.x += move_dir[RIGHT].x
position.y += move_dir[RIGHT].y
```

[raylib] - Drawing pyramid with bunch of rectangles(as is pixel).

```text
int s = 40;
int w = 10;

for (int i=1;i<=s;i+=2) {
    for (int j=1; j<=i; j++) {
        int x = (screen_w/2)-(s/2);
        int y = (screen_h/2)-(s/2*w/2);
        DrawRectangle(x-(i/2)*w+j*w,y+(i/2)*w,w,w,RED);
    }
}
```
![pyramid](/images/mixed-log/pyramid.png "140")

[raylib] - Marquee effect.

----- Custom font image from raylib repo: [alagard.png](https://github.com/raysan5/raylib/blob/0abba4dc1854e71f58b252e840058178ea845e25/examples/text/resources/fonts/alagard.png)
```text
Font alagard = LoadFont("resources/alagard.png"); // custom font
char *intro = "HELLO WORLD!";
float scroll_speed = 1.0f;
float current_left = canvasWidth+8;
float left_corner = -(MeasureText(intro, alagard.baseSize*1.0f)-8);

// while loop...
    current_left -= scroll_speed;
    if (current_left <= left_corner ) {
        current_left = canvasWidth+8;
    }
    // drawing...
    DrawTextEx(alagard,intro,(Vector2){current_left,108},alagard.baseSize*1.0f,1.0f,RED);

// unload font
UnloadFont(alagard);
```
<small>!drop fsp comes while recording.</small>

![marquee](/images/mixed-log/marquee.gif "140")

[raylib] - 3d cube on 2d canvas with rotation animation.

----- [PICO8 (fake 3D?) CAD BGF 2019](https://www.youtube.com/watch?v=HIFhRacJXPY)

```text
float t = 0;
float rotation_speed = 0.03f;
Vector2 p[4];

// while loop
    t += rotation_speed;
    if (t >=1.5f) {
        t = 0;
    }
    // drawing...
    // background
    Rectangle rec = {(float)128/2,(float)128/2-40,60,60};
    Vector2 og = {0,0};
    DrawRectanglePro(rec,og,45,BLACK);
    // cube
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
```

![cube-anim](/images/mixed-log/cube-anim.gif "140")

## September
[raylib] - Low-res (pixel) canvas / simple setup.

----- [smooth pixel-perfect camera](https://www.raylib.com/examples/core/loader.html?name=core_smooth_pixelperfect)

```text
#include <stdio.h>
#include "raylib.h"
int main() {
    const int screenWidth = 512;
    const int screenHeight = 512;
    const int canvasWidth = 128;
    const int canvasHeight = 128;
    InitWindow(screenWidth,screenHeight,"low-res");
    SetWindowState(FLAG_VSYNC_HINT);
    SetTargetFPS(60);

    RenderTexture2D target = LoadRenderTexture(canvasWidth,canvasHeight);
    Rectangle source = {0,-canvasHeight,target.texture.width,-target.texture.height};
    Rectangle dest = {0,0,screenWidth,screenHeight};
    Camera2D camera = {0};
    camera.zoom = 1.0f;

    while (!WindowShouldClose()) {
        BeginTextureMode(target);
            ClearBackground(RAYWHITE);
            BeginMode2D(camera);
                DrawFPS(4,4);
                // drawing test
                Rectangle rec = {(float)128/2,(float)128/2-40,60,60};
                Vector2 og = {0,0};
                DrawRectanglePro(rec,og,45,BLACK);
            EndMode2D();
        EndTextureMode();
        BeginDrawing();
            DrawTexturePro(target.texture,source,dest,(Vector2){0,0},0,WHITE);
        EndDrawing();
    }
    UnloadRenderTexture(target);
    CloseWindow();
    return 0;
}
```

![test](/images/mixed-log/low-res_rectangle.png "140")

## June

I found a nice vim plugin "wiki.vim" where I can take my notes without leaving vim.
I'm pretty excited about learning this thing.
Also I should mention that I've using "Left" and it is still very useful to taking my notes.

----- [wiki.vim](https://github.com/lervag/wiki.vim)

----- [left](https://100r.co/site/left.html)
