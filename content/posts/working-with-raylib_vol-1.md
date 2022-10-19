---
title: "Working with raylib Vol:1"
date: 2022-10-18T14:27:07+03:00
description: "Notes."
ready: "in progress"
draft: false
---

### Get color from hex value

```text
# color code --> #0000ff
# replace # -> 0x & add alpha value at the end; for max -> ff
Color col = GetColor(0x0000ffff);
```

### Format string

```text
TextFormat("Player: %04d", player.score);
# output for score 10 --> 0010
```

### Compile and run [c/c++]

```text
# c
gcc main.c -o demo -lraylib -lGL -lm -lpthread -ldl -lrt -lX11 && ./demo
# c++
g++ main.cpp -o demo -lraylib -lGL -lm -lpthread -ldl -lrt -lX11 && ./demo
```

### Web build code snipped

```text
#include "raylib.h"

#if defined(PLATFORM_WEB)
    #include <emscripten/emscripten.h>
#endif

#define _SCREEN_W 640
#define _SCREEN_H 360

void UpdateDrawFrame(void);

int main() {
    SetWindowState(FLAG_VSYNC_HINT);
    InitWindow(_SCREEN_W,_SCREEN_H,"Pong");
    #if defined(PLATFORM_WEB)
        emscripten_set_main_loop(UpdateDrawFrame, 0, 1);
    #else
    SetWindowPosition(0,0); // open window top-left corner
    SetTargetFPS(60);
    while (!WindowShouldClose()) {
        UpdateDrawFrame();
    }
    #endif
    CloseWindow();
    return 0;
}

void UpdateDrawFrame(void) {
    // update & input event codes here
    BeginDrawing();
        ClearBackground(BLACK);
        // draw codes here
        DrawFPS(4,4);
    EndDrawing();
}
```

```text
# build & run
# if there have resources e.g font sound etc. add " --preload-file resources "
mkdir build
emcc -o build/index.html main.c -Os -Wall -I ../raylib/src \
    -L ../raylib/src -s USE_GLFW=3 -s ASYNCIFY --shell-file minshell.html \
    -DPLATFORM_WEB -lraylib && cd build/ && python -m http.server
```


### 4 direction movement logic(grid).

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

### Drawing pyramid with bunch of rectangles(as is pixel).

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

### Marquee effect.

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

### 3d cube on 2d canvas with rotation animation.

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

### Low-res (pixel) canvas / simple setup.

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
