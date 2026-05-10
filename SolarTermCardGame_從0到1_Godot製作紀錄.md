# SolarTermCardGame 製作紀錄：從 0 到 1 學會 Godot 做一款簡單配對遊戲

## 1. 專案簡介

本作品是一款使用 **Godot 4.6** 製作的簡單記憶配對遊戲。  
主題結合：

- 二十四節氣
- 花卉
- 台灣神明
- Q 版日系手遊風格
- 廟宇幻想背景

遊戲玩法是常見的 **Memory Card Game / 配對卡牌遊戲**：

1. 玩家看到 8 張蓋住的卡牌。
2. 點擊卡牌翻面。
3. 每次翻兩張。
4. 如果兩張卡牌是同一組，則配對成功。
5. 如果不同，卡牌會自動翻回背面。
6. 全部配對成功後，顯示過關提示。
7. 遊戲包含背景音樂、翻牌音效、配對成功音效與步數計算。

---

## 2. 使用工具

### 2.1 Godot Engine

本專案使用：

```text
Godot 4.6.2
```

Godot 是免費、開源的遊戲引擎，可以製作：

- 2D 遊戲
- 3D 遊戲
- Web 遊戲
- Android 遊戲
- XR / VR / AR 互動應用

本次先從最簡單的 2D 配對遊戲開始，目標是先做出可以玩的作品原型。

---

### 2.2 使用到的素材

專案中使用到：

- 背景圖：日系廟宇幻想花園
- 卡牌正面圖：二十四節氣、花卉、神明
- 卡牌背面圖：統一的卡背設計
- BGM：背景音樂
- SFX：翻牌音效、配對成功音效

---

## 3. 專案建立

### 3.1 建立 Godot 專案

在 Godot 專案管理器中建立新專案：

```text
Project Name: SolarTermCardGame
Project Path: C:/Users/user/solar-term-card/solar-term-card-game
Renderer: Forward+
Version Control: Git
```

雖然一開始只是練習，但建立專案時就開啟 Git 是好的習慣，方便未來上傳 GitHub 與部署到 Netlify。

---

## 4. 專案資料夾結構

建立完成後，整理專案資料夾：

```text
res://
├── assets
│   ├── background
│   │   └── temple_bg.png
│   ├── cards
│   │   ├── card_back.png
│   │   ├── 立春.png
│   │   ├── 春分.png
│   │   ├── 雨水.png
│   │   └── 驚蟄.png
│   ├── bgm
│   │   └── bgm.ogg
│   ├── sfx
│   │   ├── flip.wav
│   │   └── match.wav
│   ├── effects
│   └── ui
├── scenes
│   ├── Main.tscn
│   └── card.tscn
├── scripts
│   └── Main.gd
├── project.godot
└── icon.svg
```

### 4.1 資料夾用途

| 資料夾 | 用途 |
|---|---|
| assets/background | 放遊戲背景圖 |
| assets/cards | 放卡牌正面與卡背 |
| assets/bgm | 放背景音樂 |
| assets/sfx | 放音效 |
| assets/effects | 之後可放粒子、特效素材 |
| assets/ui | 之後可放按鈕、UI 圖 |
| scenes | 放 Godot 場景 |
| scripts | 放 GDScript 程式 |

---

## 5. 建立主場景 Main.tscn

### 5.1 建立 Node2D

建立第一個 2D 場景：

```text
Scene → New Scene → 2D Scene
```

根節點為：

```text
Node2D
```

這個場景是整個遊戲的主場景，之後背景、卡牌、UI、音樂、音效都放在這裡。

存檔為：

```text
res://scenes/Main.tscn
```

---

## 6. 加入背景

### 6.1 新增 Sprite2D

在 `Node2D` 底下新增：

```text
Sprite2D
```

命名為：

```text
Background
```

### 6.2 設定背景圖片

選取 `Background`，在右側 Inspector 的 `Texture` 中指定：

```text
res://assets/background/temple_bg.png
```

### 6.3 調整背景大小與位置

如果背景太小或太大，可在右側調整：

```text
Transform → Position
Transform → Scale
```

背景是遊戲的視覺基礎，因此要先把它放到畫面中適合的位置。

---

## 7. 建立卡牌場景 card.tscn

### 7.1 新增 Area2D

新增一個獨立場景：

```text
Scene → New Scene → Other Node → Area2D
```

命名為：

```text
card
```

這個 `Area2D` 是卡牌的本體。

### 7.2 為什麼使用 Area2D？

`Area2D` 可以偵測滑鼠點擊與碰撞範圍。  
在本遊戲中，玩家要點擊卡牌，所以卡牌需要能接收輸入事件。

---

### 7.3 加入 CardSprite

在 `card` 底下新增：

```text
Sprite2D
```

命名為：

```text
CardSprite
```

用途：

```text
顯示卡牌圖片
```

---

### 7.4 加入 CollisionShape2D

在 `card` 底下新增：

```text
CollisionShape2D
```

用途：

```text
決定卡牌可以被點擊的範圍
```

如果沒有 `CollisionShape2D`，Godot 不知道玩家點擊的範圍在哪裡，卡牌就不會有反應。

---

### 7.5 設定 RectangleShape2D

選取 `CollisionShape2D`，在右側 Inspector 設定：

```text
Shape → New RectangleShape2D
```

再調整大小，讓它包住整張卡牌。

---

### 7.6 card.tscn 結構

```text
card (Area2D)
├── CardSprite (Sprite2D)
└── CollisionShape2D
```

存檔為：

```text
res://scenes/card.tscn
```

---

## 8. 建立卡牌互動程式 card.gd

在 `card` 根節點掛上腳本：

```text
res://scenes/card.gd
```

完整程式如下：

```gdscript
extends Area2D

signal card_clicked(card)

@export var card_id: String = "lichun"

var is_flipped := false
var is_matched := false
var original_scale := Vector2.ONE
var front_texture: Texture2D
var back_texture = preload("res://assets/cards/card_back.png")

@onready var card_sprite: Sprite2D = $CardSprite

func _ready():
	original_scale = scale
	front_texture = card_sprite.texture
	card_sprite.texture = back_texture
	input_event.connect(_on_input_event)

func _on_input_event(viewport, event, shape_idx):
	if is_matched:
		return

	if is_flipped:
		return

	if event is InputEventMouseButton:
		if event.pressed and event.button_index == MOUSE_BUTTON_LEFT:
			card_clicked.emit(self)

func flip_front():
	is_flipped = true

	var tween = create_tween()
	tween.tween_property(self, "scale:x", 0.0, 0.12)
	tween.tween_callback(show_front)
	tween.tween_property(self, "scale:x", original_scale.x, 0.12)

func flip_back():
	is_flipped = false

	var tween = create_tween()
	tween.tween_property(self, "scale:x", 0.0, 0.12)
	tween.tween_callback(show_back)
	tween.tween_property(self, "scale:x", original_scale.x, 0.12)

func show_front():
	card_sprite.texture = front_texture
	card_sprite.modulate = Color(1, 1, 1)

func show_back():
	card_sprite.texture = back_texture
	card_sprite.modulate = Color(1, 1, 1)

func mark_matched():
	is_matched = true
	card_sprite.texture = front_texture
	card_sprite.modulate = Color(1, 1, 1)
```

---

## 9. card.gd 程式說明

### 9.1 extends Area2D

```gdscript
extends Area2D
```

代表這個腳本掛在 `Area2D` 節點上。

---

### 9.2 signal card_clicked(card)

```gdscript
signal card_clicked(card)
```

這是一個自訂訊號。  
當玩家點擊卡牌時，卡牌會發出這個訊號給 Main 場景。

---

### 9.3 card_id

```gdscript
@export var card_id: String = "lichun"
```

`card_id` 用來判斷兩張卡是不是同一組。

例如：

```text
card1.card_id = "lichun"
card2.card_id = "lichun"
```

代表這兩張卡是同一組。

---

### 9.4 is_flipped

```gdscript
var is_flipped := false
```

用來記錄卡牌目前是不是翻開狀態。

---

### 9.5 is_matched

```gdscript
var is_matched := false
```

用來記錄卡牌是否已經配對成功。

如果已經配對成功，就不能再被翻動。

---

### 9.6 front_texture 與 back_texture

```gdscript
var front_texture: Texture2D
var back_texture = preload("res://assets/cards/card_back.png")
```

- `front_texture`：卡牌正面圖
- `back_texture`：卡牌背面圖

遊戲一開始會把卡牌顯示為背面。

---

### 9.7 _ready()

```gdscript
func _ready():
	original_scale = scale
	front_texture = card_sprite.texture
	card_sprite.texture = back_texture
	input_event.connect(_on_input_event)
```

這段在遊戲開始時執行。

它做了幾件事：

1. 記錄卡牌原始縮放大小。
2. 把目前 CardSprite 上的圖片存成正面圖。
3. 一開始把卡牌換成背面。
4. 連接滑鼠點擊事件。

---

### 9.8 _on_input_event()

```gdscript
func _on_input_event(viewport, event, shape_idx):
```

這是接收玩家點擊事件的函式。

如果：

- 卡牌已配對成功
- 卡牌已經翻開

就不處理點擊。

否則當滑鼠左鍵按下時，發出 `card_clicked` 訊號。

---

### 9.9 flip_front()

```gdscript
func flip_front():
```

這是翻到正面的函式。

使用 Tween 做出：

```text
縮小 X 軸
→ 換成正面圖片
→ 展開 X 軸
```

形成簡單的翻牌動畫。

---

### 9.10 flip_back()

```gdscript
func flip_back():
```

這是翻回背面的函式。

用法跟 `flip_front()` 類似，只是換成背面圖片。

---

### 9.11 mark_matched()

```gdscript
func mark_matched():
```

當兩張卡牌配對成功時呼叫。  
會把 `is_matched` 設成 `true`，並保持顯示正面。

---

## 10. 把卡牌放到 Main 場景

回到 `Main.tscn`。

從左下角檔案系統把：

```text
res://scenes/card.tscn
```

拖進 `Node2D` 底下。

接著複製成 8 張卡牌：

```text
Ctrl + D
```

最後結構會像這樣：

```text
Node2D
├── Background
├── card
├── card2
├── card3
├── card4
├── card5
├── card6
├── card7
├── card8
├── MoveLabel
├── WinLabel
├── BGM
├── FlipSFX
└── MatchSFX
```

---

## 11. 設定 8 張卡牌位置

使用 2 排 4 欄排列。

範例座標：

```text
card  → X = 350, Y = 180
card2 → X = 520, Y = 180
card3 → X = 690, Y = 180
card4 → X = 860, Y = 180

card5 → X = 350, Y = 420
card6 → X = 520, Y = 420
card7 → X = 690, Y = 420
card8 → X = 860, Y = 420
```

卡片縮放建議：

```text
Scale X = 0.23
Scale Y = 0.23
```

---

## 12. 設定卡牌 ID

每兩張要設定同樣的 `card_id`。

範例：

```text
card  = lichun
card2 = lichun

card3 = chunfen
card4 = chunfen

card5 = yushui
card6 = yushui

card7 = jingzhe
card8 = jingzhe
```

### 12.1 如何設定 card_id？

點選卡牌節點，例如 `card`，右側 Inspector 中會看到：

```text
Card Id
```

輸入對應 ID。

---

## 13. 更換每張卡的正面圖片

如果卡牌是從 `card.tscn` 實例化出來的，預設無法直接展開內部子節點。

要先：

```text
右鍵卡牌 → Editable Children / 可編輯子項目
```

然後展開：

```text
card2
├── CardSprite
└── CollisionShape2D
```

選取 `CardSprite`，在右側 `Texture` 更換圖片。

---

## 14. 新增 UI：步數 MoveLabel

在 `Node2D` 底下新增：

```text
Label
```

命名為：

```text
MoveLabel
```

用途：

```text
顯示目前步數
```

### 14.1 設定 MoveLabel 外觀

建議設定：

```text
Font Size = 28
Font Color = White
Outline Size = 6
Outline Color = Black
```

如果不設定黑色外框，文字會被亮色背景吃掉，看不清楚。

---

## 15. 新增 UI：勝利提示 WinLabel

在 `Node2D` 底下新增：

```text
Label
```

命名為：

```text
WinLabel
```

文字：

```text
配對成功！
```

### 15.1 設定 WinLabel 外觀

建議設定：

```text
Font Size = 48
Font Color = Yellow
Outline Size = 8
Outline Color = Black
```

一開始將它隱藏：

```text
Visible = Off
```

過關時再用程式顯示。

---

## 16. 新增背景音樂 BGM

在 `Node2D` 底下新增：

```text
AudioStreamPlayer
```

命名為：

```text
BGM
```

指定音樂：

```text
res://assets/bgm/bgm.ogg
```

設定：

```text
Volume dB = -10
Looping = On
```

之後在程式中呼叫：

```gdscript
bgm.play()
```

---

## 17. 新增音效 FlipSFX 與 MatchSFX

在 `Node2D` 底下新增兩個：

```text
AudioStreamPlayer
```

分別命名為：

```text
FlipSFX
MatchSFX
```

用途：

| 節點 | 用途 |
|---|---|
| FlipSFX | 翻牌音效 |
| MatchSFX | 配對成功音效 |

---

## 18. 建立 Main.gd

在 `Node2D` 根節點掛上：

```text
res://scripts/Main.gd
```

完整程式：

```gdscript
extends Node2D

var first_card = null
var second_card = null
var can_click := true
var move_count := 0
var matched_count := 0
var total_pairs := 0

@onready var move_label: Label = $MoveLabel
@onready var win_label: Label = $WinLabel
@onready var bgm: AudioStreamPlayer = $BGM
@onready var flip_sfx: AudioStreamPlayer = $FlipSFX
@onready var match_sfx: AudioStreamPlayer = $MatchSFX

func _ready():
	randomize()
	bgm.play()

	move_label.add_theme_font_size_override("font_size", 28)
	move_label.add_theme_color_override("font_color", Color.WHITE)
	move_label.add_theme_color_override("font_outline_color", Color.BLACK)
	move_label.add_theme_constant_override("outline_size", 6)

	win_label.visible = false
	win_label.add_theme_font_size_override("font_size", 48)
	win_label.add_theme_color_override("font_color", Color.YELLOW)
	win_label.add_theme_color_override("font_outline_color", Color.BLACK)
	win_label.add_theme_constant_override("outline_size", 8)

	update_move_label()

	var cards = []

	for child in get_children():
		if child.has_signal("card_clicked"):
			cards.append(child)
			child.card_clicked.connect(_on_card_clicked)

	total_pairs = cards.size() / 2
	shuffle_card_positions(cards)

func shuffle_card_positions(cards):
	var positions = []

	for card in cards:
		positions.append(card.position)

	positions.shuffle()

	for i in range(cards.size()):
		cards[i].position = positions[i]

func _on_card_clicked(card):
	if not can_click:
		return

	card.flip_front()
	flip_sfx.play()

	if first_card == null:
		first_card = card
		return

	second_card = card
	can_click = false
	move_count += 1
	update_move_label()

	if first_card.card_id == second_card.card_id:
		match_sfx.play()

		await get_tree().create_timer(0.4).timeout
		first_card.mark_matched()
		second_card.mark_matched()
		matched_count += 1
		reset_cards()

		if matched_count == total_pairs:
			move_label.text = "過關！步數：" + str(move_count)
			show_win_effect()
	else:
		await get_tree().create_timer(0.8).timeout
		first_card.flip_back()
		second_card.flip_back()
		reset_cards()

func reset_cards():
	first_card = null
	second_card = null
	can_click = true

func update_move_label():
	move_label.text = "步數：" + str(move_count)

func show_win_effect():
	win_label.text = "配對成功！"
	win_label.visible = true
	win_label.modulate.a = 0

	var tween = create_tween()
	tween.tween_property(win_label, "modulate:a", 1.0, 0.5)
	tween.tween_interval(1.0)
	tween.tween_property(win_label, "modulate:a", 0.0, 0.5)
```

---

## 19. Main.gd 程式說明

### 19.1 變數

```gdscript
var first_card = null
var second_card = null
```

記錄目前翻開的第一張與第二張卡牌。

---

```gdscript
var can_click := true
```

避免玩家在判斷期間連續點擊。

---

```gdscript
var move_count := 0
```

記錄玩家翻牌次數。  
每翻兩張算一步。

---

```gdscript
var matched_count := 0
var total_pairs := 0
```

記錄已完成幾組，以及總共有幾組。

---

### 19.2 _ready()

```gdscript
func _ready():
```

遊戲開始時執行。

主要做：

1. 播放 BGM。
2. 設定 UI 文字樣式。
3. 尋找所有卡牌。
4. 連接卡牌點擊訊號。
5. 計算總配對數量。
6. 隨機打亂卡牌位置。

---

### 19.3 shuffle_card_positions()

```gdscript
func shuffle_card_positions(cards):
```

這個函式不是打亂圖片，而是打亂卡牌的位置。

作法：

1. 先記錄所有卡牌原本位置。
2. 把位置陣列洗牌。
3. 再把洗牌後的位置分配給每張卡。

這樣每次遊戲開始，卡牌排列都會不同。

---

### 19.4 _on_card_clicked()

```gdscript
func _on_card_clicked(card):
```

這是整個遊戲最核心的邏輯。

流程如下：

```text
玩家點第一張卡
→ 翻開
→ 記錄為 first_card

玩家點第二張卡
→ 翻開
→ move_count + 1
→ 判斷 card_id 是否相同
```

如果一樣：

```text
播放成功音效
→ 標記配對成功
→ matched_count + 1
```

如果不同：

```text
等待 0.8 秒
→ 兩張翻回背面
```

---

### 19.5 show_win_effect()

```gdscript
func show_win_effect():
```

過關後顯示勝利文字。

使用 Tween 做：

```text
淡入
停留
淡出
```

---

## 20. 設定主場景

要讓遊戲執行時載入 `Main.tscn`。

Godot 上方：

```text
Project → Project Settings → Run
```

Main Scene 設定為：

```text
res://scenes/Main.tscn
```

如果沒有設定正確，匯出後可能只會開啟 `card.tscn`，導致畫面只出現一張卡。

---

## 21. 測試遊戲

在 Godot 內按：

```text
F6
```

測試目前場景。

或按：

```text
F5
```

測試整個專案。

### 21.1 成功狀態

遊戲成功時應該有：

- 8 張卡牌
- 一開始都是卡背
- 點擊後翻開
- 翻兩張後判斷是否配對
- 不同會翻回去
- 相同會保留
- 步數會增加
- 全部完成後顯示過關
- 有 BGM 與音效

---

## 22. 匯出 Windows 版

### 22.1 安裝 Export Templates

Godot 上方：

```text
Editor → Manage Export Templates
```

下載並安裝匯出模板。

---

### 22.2 匯出 Windows

Godot 上方：

```text
Project → Export
```

新增：

```text
Windows Desktop
```

匯出後會產生：

```text
SolarTermCardGame.exe
SolarTermCardGame.pck
SolarTermCardGame.console
```

正式發布時保留：

```text
SolarTermCardGame.exe
SolarTermCardGame.pck
```

`.console` 是除錯版本，可以不用放給玩家。

---

## 23. 匯出 Web 版

### 23.1 新增 Web Export

Godot 上方：

```text
Project → Export
```

新增：

```text
Web
```

匯出路徑：

```text
export/web/SolarTermCardGame.html
```

---

### 23.2 Web 匯出檔案

匯出後會出現：

```text
SolarTermCardGame.html
SolarTermCardGame.js
SolarTermCardGame.wasm
SolarTermCardGame.pck
SolarTermCardGame.audio.worklet.js
SolarTermCardGame.audio.position.worklet.js
SolarTermCardGame.icon.png
SolarTermCardGame.apple-touch-icon.png
```

其中最重要：

```text
.html
.js
.wasm
.pck
```

少一個就可能卡在 Godot loading 畫面。

---

### 23.3 Web Loading 卡住問題

如果上傳到 Netlify 後卡在 Godot loading 畫面，常見原因是：

1. `.wasm` 沒有成功上傳。
2. `.pck` 沒有成功上傳。
3. GitHub 網頁上傳漏掉大檔。
4. Web 匯出設定與瀏覽器不相容。

解法：

- 確認 `.wasm` 與 `.pck` 都存在。
- 不要只上傳 html。
- 可以改用 Netlify 手動拖曳 `export/web` 資料夾。
- Web 匯出中的 `Thread Support` 可以先關閉。

---

## 24. 部署到 Netlify

### 24.1 使用 GitHub 部署

流程：

```text
Godot 匯出 Web
→ 上傳到 GitHub
→ Netlify 連接 GitHub
→ 自動部署
```

Netlify 設定：

```text
Build command: 空白
Publish directory: .
```

---

### 24.2 使用 Netlify 手動部署

如果 GitHub 上傳 `.wasm` 不穩，可以直接使用 Netlify Drop。

操作：

1. 打開 Netlify。
2. 進入 Projects。
3. 找到拖曳部署區域。
4. 把整個 `export/web` 資料夾拖進去。
5. 等待部署完成。

---

## 25. 本次遇到的問題與解法

### 問題 1：點卡牌沒反應

原因：

- 沒有設定 `CollisionShape2D`
- `Input Pickable` 沒開
- 腳本沒有掛在 `Area2D`

解法：

- 確認 card 是 `Area2D`
- 確認有 `CollisionShape2D`
- 確認 Shape 是 `RectangleShape2D`
- 確認有掛 `card.gd`

---

### 問題 2：翻牌後卡牌變扁

原因：

程式把 `scale:x` 還原成 `1.0`，但卡牌本身在 Main 中已縮放成 `0.23` 或 `0.3`。

解法：

用：

```gdscript
original_scale = scale
```

記錄原本大小，再還原回：

```gdscript
original_scale.x
```

---

### 問題 3：匯出 exe 後只看到一張卡

原因：

主場景沒有設定成 `Main.tscn`，Godot 把 `card.tscn` 當成遊戲入口。

解法：

```text
Project Settings → Run → Main Scene
```

設定為：

```text
res://scenes/Main.tscn
```

---

### 問題 4：Web 部署後卡在 Godot loading

原因：

通常是 `.wasm` 或 `.pck` 沒有成功上傳。

解法：

- 確認 GitHub 或 Netlify 裡有 `.wasm`
- 確認有 `.pck`
- 直接用 Netlify 手動拖 `export/web` 資料夾

---

### 問題 5：文字看不清楚

原因：

白色文字直接放在亮色背景上。

解法：

使用黑色描邊：

```gdscript
move_label.add_theme_color_override("font_outline_color", Color.BLACK)
move_label.add_theme_constant_override("outline_size", 6)
```

---

## 26. 目前完成的功能

目前遊戲已完成：

- 2D 主場景
- 背景圖
- 8 張卡牌
- 卡背
- 翻牌動畫
- 配對判斷
- 失敗翻回
- 成功保留
- 隨機發牌
- 步數計算
- 勝利提示
- BGM
- 翻牌音效
- 配對成功音效
- Windows 匯出
- Web 匯出
- Netlify 部署

---

## 27. 下一步可以新增的功能

### 27.1 遊戲內容

- 擴充到 24 節氣
- 每個節氣加入花卉介紹
- 每個節氣加入對應神明介紹
- 增加不同關卡

---

### 27.2 視覺效果

- 配對成功發光
- 花瓣粒子特效
- 卡牌 Hover 放大
- 過關動畫
- 開始畫面
- 結算畫面

---

### 27.3 音效

- 翻牌音效
- 錯誤音效
- 勝利音效
- 點擊音效
- 花瓣飄落環境音

---

### 27.4 AR / XR 延伸

未來可以將作品延伸為：

```text
手機掃描實體卡牌
→ 開啟 AR 畫面
→ 在 AR 中顯示卡牌遊戲
```

可能技術路線：

- WebAR + MindAR
- Three.js / A-Frame
- Godot XR
- Android XR
- Quest / OpenXR

目前 Godot 版本可以先當作遊戲原型，未來再與 AR 平台整合。

---

## 28. 學到的 Godot 核心觀念

透過這個專案，學到了：

### 28.1 Scene 場景概念

Godot 的核心是場景：

```text
Main.tscn 是主遊戲場景
card.tscn 是可重複使用的卡牌物件
```

---

### 28.2 Node 節點概念

每個功能都由節點組成：

| 節點 | 功能 |
|---|---|
| Node2D | 2D 場景根節點 |
| Sprite2D | 顯示圖片 |
| Area2D | 偵測點擊 |
| CollisionShape2D | 點擊範圍 |
| Label | 顯示文字 |
| AudioStreamPlayer | 播放音樂與音效 |

---

### 28.3 Signal 訊號

卡牌被點擊時，發出：

```gdscript
signal card_clicked(card)
```

Main 接收到後處理配對邏輯。

這是 Godot 很重要的設計方式。

---

### 28.4 Tween 動畫

翻牌動畫使用：

```gdscript
create_tween()
```

做出縮放與淡入淡出。

---

### 28.5 Export 匯出

學會匯出：

- Windows exe
- Web HTML5

並部署到 Netlify。

---

## 29. 專案總結

這個專案從 0 開始，完成了一款可玩的 Godot 配對遊戲 Prototype。

雖然目前功能還簡單，但已經包含完整遊戲開發流程：

```text
建立專案
→ 整理素材
→ 建立場景
→ 建立卡牌物件
→ 撰寫互動程式
→ 製作 UI
→ 加入音樂音效
→ 匯出遊戲
→ 部署到網頁
```

這是一個很好的 Godot 入門作品，也可以作為未來 AR / XR 互動專題的基礎。

未來可以繼續往：

```text
二十四節氣完整卡牌
AR 掃描互動
3D 卡牌展示
手機版操作
作品集發布
```

方向擴充。

---

## 30. 最終成果

目前成果：

```text
作品名稱：SolarTermCardGame
主題：二十四節氣 × 花卉 × 台灣神明
類型：Memory Card Game
引擎：Godot 4.6
平台：Windows / Web
狀態：Prototype Demo
```

這代表我已經完成了第一個從 0 到 1 的 Godot 小遊戲。
