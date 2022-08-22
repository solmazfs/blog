---
title: "Making Tic-Tac-Toe Game with using 'raylib' - Part1"
date: 2022-08-12T20:59:59+03:00
tags: [game-dev]
categories: ["tutorials-how-tos"]
description: "Part 1: Basic setup and drawing the game board."
ready: "in progress"
draft: false
---

I'll skip general info about what is raylib and how to setup for different platforms.
You can found these information in official repo.<small>[[1]]({{< ref "#ref1" >}})</small>

### Basic setup for ready to use

// test it before release the post
```text
#include <stdio.h>
// library that you can code game
#include "raylib.h"

const int _SCREEN_W = 512;
const int _SCREEN_H = 512;

Color color_bg = {207,188,149,255};
Color color_line = {32,54,51,255};

Rectangle boxIndex[3][3];

// methods
void game_board();
void spaces_collision_box();
bool spaces_mouse_entered();

int main() {
    InitWindow(_SCREEN_W,_SCREEN_H,"main");
    SetWindowState(FLAG_VSYNC_HINT);
    SetTargetFPS(60);

    // start here
    RenderTexture2D target = LoadRenderTexture(_SCREEN_W,_SCREEN_H);
    Rectangle source = {0,0,target.texture.width,-target.texture.height};
    BeginTextureMode(target);
    ClearBackground(color_bg);
    game_board();
    spaces_collision_box();
    EndTextureMode();


    // game loop
    while (!WindowShouldClose()) {
        BeginDrawing();
        ClearBackground(BLACK);
        DrawTextureRec(target.texture,source,(Vector2){0,0},color_bg);
        if (spaces_mouse_entered()) {
            DrawCircle(GetMousePosition().x,GetMousePosition().y,16,RED);
        }
        DrawFPS(10,10);
        EndDrawing();
    }
    CloseWindow();
    return 0;
}

void game_board() {
    float margin = (float)(_SCREEN_W - boardSize)/2;
    // DEBUG --> Center full line
    DrawLineEx((Vector2){(float)_SCREEN_W/2,0},(Vector2){(float)_SCREEN_W/2,_SCREEN_H},2,GRAY);
    DrawLineEx((Vector2){0,(float)_SCREEN_W/2},(Vector2){_SCREEN_W,(float)_SCREEN_H/2},2,GRAY);

    for (int i=1; i<3; i++) {
        // columns
        Vector2 colm_start = {((float)boardSize/3)*i+margin, margin};
        Vector2 colm_end = {((float)boardSize/3)*i+margin, boardSize+margin};
        DrawLineEx(colm_start,colm_end,8,color_line);
        // rows
        Vector2 row_start = {margin, ((float)boardSize/3)*i+margin};
        Vector2 row_end = {boardSize+margin, ((float)boardSize/3)*i+margin};
        DrawLineEx(row_start,row_end,8,color_line);
    }
}

// draw shapes for each spaces
void spaces_collision_box() {
    Color col_alpha = {64,53,33,10};
    float margin = (float)(_SCREEN_W - boardSize)/2;
    int box_margin = 30; // margin for box
    float box_w = (float)boardSize/3;
    float box_h = (float)boardSize/3;
    for (int i=0; i< 3; i++) {
        for (int j=0; j < 3; j++) {
            float x = box_w*i+margin + box_w/2;
            float y = box_h*j+margin + box_h/2;
            float xr = box_w/2-(float)box_margin/2;
            boxIndex[i][j] = (Rectangle){x-xr,y-xr,box_w-margin,box_h-margin};
            // DEBUG -> collision shapes
            DrawRectangleRec(boxIndex[i][j],col_alpha);
        }
    }
}

// check if mouse entered the area
bool spaces_mouse_entered() {
    for (int i=0; i<3; i++) {
        for (int j=0; j<3; j++) {
            if ( CheckCollisionPointRec(GetMousePosition(),boxIndex[i][j]) ) {
                currentBox = boxIndex[i][j];
                return true;
            }
        }
    }
    return false;
}

```

### Build script
```text
vim build.sh
gcc tic-tac-toe.c -lraylib -lGL -lm -lpthread -ldl -lrt -lX11 && ./a.out
```

### Try to run
```text
chmod +x build.sh
./build.sh
```

<ref id="ref1"> Raylib official repo: [https://github.com/raysan5/raylib](https://github.com/raysan5/raylib)</ref>
