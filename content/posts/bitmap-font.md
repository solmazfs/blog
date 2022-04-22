---
title: "Bitmap Font (GODOT)"
date: 2022-04-08T13:47:04+03:00
categories: ["game-dev"]
tags: [godot,game-dev]
ready: "in progress"
draft: false
---

### drawing part [libresprite]
Create 110x50 canvas

pick color palette in presets menu, e.g Nes

fill background a bit bright color e.g. #92d3ff

make 8x8 square for each letter, number and character etc. and fill darker color e.g. #2000b2

we are ready to draw some letters on it

you can download it here

### godot part

```text
extends Node2D

# font texture
const FONT_TEXTURE:Texture = preload("res://bitmap-font.png")
# texture size
const T_SIZE = {w=110,h=50}
# project window size
const WIND = {w=128,h=128}
export(bool) var drw_grid = false
onready var bitmap_font:BitmapFont = BitmapFont.new()
var chars:String = "abcdefghiklmnoprstuvwyz123456789!©xµ<>£#$:-.' "

func _ready():
	gen_bitmap_font(T_SIZE.w,T_SIZE.h)

func _draw() -> void:
	draw_grid(drw_grid)
	draw_textbox_window(["hello world!","'hello'","world."])

func draw_grid(value:bool) -> void:
	# draw grid
	var col:Array = [Color.blueviolet,Color.aqua]
	match value:
		true:
			for i in range(8,128,8):
				draw_line(Vector2(i,8),Vector2(i,120),col[0],2)
				draw_line(Vector2(8,i),Vector2(120,i),col[0],2)
			draw_line(Vector2(128/2,0),Vector2(128/2,128),col[1],2)
			draw_line(Vector2(0,128/2),Vector2(128,128/2),col[1],2)
		false:
			return

# generating bitmap font
func gen_bitmap_font(_w:int,_h:int) -> void:
	bitmap_font.add_texture(FONT_TEXTURE)
	bitmap_font.height = 8 # using for Label -> new line "\n"

	var char_map:Array = []
	for y in range(1,_h,10):
		for x in range(1,_w,10):
			char_map.append(Vector2(x,y))
	for i in range(0,chars.length()):
		bitmap_font.add_char(chars.ord_at(i),0,Rect2(char_map[i],Vector2(8,8)),Vector2.ZERO,8)

# draw text window
func draw_textbox_window(_text:Array) -> void:
	var col:Array = [Color("#fcfcfc"),Color.black]
	var longest:String= ""
	for i in _text:
		if longest.length() < i.length():
			longest = i
	var box_w:int = longest.length()*8+8
	var box_h:int = _text.size()*9+9
	var center:Dictionary = {x=WIND.w/2-(box_w)/2, y=WIND.h/2-(box_h)/2}
	for m in range(3):
		draw_rect(Rect2(center.x+m,center.y+m,box_w-m*2,box_h-m*2),col[m-1]) # a little hack for col arr
	for t in _text.size():
		draw_string(bitmap_font,Vector2(center.x+3+2,center.y+3+2+t*9),_text[t],col[0])
```
