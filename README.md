# Light-Engine
一個在JavaScript上的輕量級2D遊戲引擎。

## 安裝
Light Engine使用比較麻煩(~~笨~~)的安裝方式

1.您需要先前往 https://mega.nz/file/yN5FibCT#x3MJSIYq9WhjH49huIKi-zm2GGBX_aKNJMm2Ishc0vw 下載Light Engine的安裝檔案

2.將ZIP檔案拖移到您的專案資料夾裡面並解壓縮

3.呼叫下載的函示

```js
const { download } = require('./LightEngine/connect.js')
download()
```
運行此檔案，Light Engine應該很快就會開始下載，正常來講可能會需要5~10秒的時間(除非我們可愛的Replit不在線)

4.嘗試導入Light Engine [導入Light Engine](#LE)，如果沒有發生錯誤，代表安裝成功！

### 同時Light Engine還會需要使用到一些套件: 
+ node-canvas｜npm i canvas｜https://www.npmjs.com/package/canvas
+ @kmamal/sdl｜npm i @kmamal/sdl｜https://www.npmjs.com/package/@kmamal/sdl

## 函式庫

+ [LE](#LE)
  + [LE.create.game()](#creategame)
  + [LE.plugin()](#plugin)
    + [Builder](#builder)
      + [GameBuilder()](#gamebuilder)
      + [TextureBuilder()](#gametexture)
      + [ObjectBuilder()](#objecttexture)
+ [game](#game)
  + [game.display_operations()](#gamedisplay_operations)
  + [game.display()](#gamedisplay)
  + [game.setWidth()](#gamesetWidth)
  + [game.setHeight()](#gamesetHeight)
  + [game.setCameraX()](#gamesetCameraX)
  + [game.setCameraY()](#gamesetCameraY)
  + [game.getFPS()](#gamegetFPS)
  + [game.create](#gamecreate)
    + [game.create.object()](#gamecreateobject)
  + [game.window](#gamewindow)
    + [game.window.setFullScreen()](#gamewindowsetFullScreen)
    + [game.window.setMaxSize()](#gamewindowsetMaxSize)
    + [game.window.setMinimize()](#gamewindowsetMinimize)
    + [game.window.setResizable()](#gamewindowsetResizable)
    + [game.window.setVisible()](#gamewindowsetVisible)
    + [game.window.setBorderless()](#gamewindowsetBorderless)
    + [game.window.event()](#game.window.event)
+ [參數](#參數)
  + [value.game](#valuegame)
  + [value.window](#valuewindow)

# LE
LE是Light Engine中最上層的Class，你可以用他來創建遊戲或導入插件。

```js
//導入LE
const { LE } = require('./LightEngine/LE')
```

## create.game()

你可以透過使用[LE.create.game]搭配[GameBuilder](#gamebuilder)來創建一個新的遊戲。

```js
//使用Builder來創建遊戲的範例
let game = LE.create.game(
  new GameBuilder()
    .id('Game1')
    .name('A Game')
    .type('sdl')
    .width(500)
    .height(500)
)

//使用Object來創建
//參數必須是完整的，否則將會報錯
let game = LE.create.game({
  id: 'Game1',
  name: 'A Game',
  type: 'sdl',
  width: 500,
  height: 500,
  camera_x: 0,
  camera_y: 0,
  preload_range: 50
})
```
> [所有的參數](#valuegame)

創建後他會返回一個Class，你可以透過返回的Class來取得遊戲的參數，或對遊戲的參數做改動。

## plugin
你可以透過使用[LE.plugin]來導入Light Engine的插件，呼叫這個函示時會像伺服器索取 下載/更新 的資料，在啟動時會花費一些時間。
如果不想要自動更新插件的話可以在 LightEngine/settings.js 裡將 auto_update 設為 false。

```js
//導入插件GifDisplay的範例
async funciton game_start () {
  const { GifDisplay } = await LE.plugin('GifDisplay') //需要使用await
}
game_start()
```

## Builder
Builder是在Light Engine裡讓您可以簡單.方便的創建遊戲.物件.材質等等的一個插件

## ObjectBuilder()
你可以透過使用[ObjectBuilder]搭配[game.create.object](#gamecreateobject)來創建一個新的物件。

```js
let object = new ObjectBuilder(game) //指定的遊戲的ID或遊戲的Class
  .id('Object1') //物件ID｜必要參數
  .location(0, 0) //位置(x, y)｜必須為數字 (預設0, 0)
  .texture('Texture1') //材質的ID｜必要參數
  .layer(1) //層｜必須為數字 (預設1)
  .size(0) //大小｜必須為數字 (預設0)
  .width() //寬度｜必須為數字 (預設0)
  .height() //高度｜必須為數字 (預設0)
  .angle(90) //角度｜必須為數字 (預設0)
  .effect(effect) //效果｜必須為 效果Builder 或 效果Object
```

# game
game是在[創建遊戲](#create)後返回的一個Class，你可以透過這個Class來取得遊戲的參數，或對遊戲的參數做改動。

(您不能透過改變Class的參數來對遊戲進行改動，只能透過呼叫Class的函示來改動。)

取得遊戲的參數
```js
//取得遊戲的ID範例
console.log(game.id)
```

> [所有的參數](#valuegame)

## game.display_operations()
顯示優化是Light Engine優化效能的一個功能，呼叫這個函示會運算所有物件的位置.大小並且將在螢幕內的物件添加到"顯示緩存"，你可以透過增加[preload_range(預加載範圍)](#valuegame)來添加螢幕外的物件到"顯示緩存"。請注意，[畫]不適用於顯示優化，所以過多的"畫資料"還是會照成卡頓。當然，如果您的遊戲的鏡頭是不移動的，那您只需要在程序開始的時候呼叫一次就好。

顯示優化建議根據遊戲類型.預加載範圍來決定每次要間隔多久呼叫一次，可能會為每10幀進行顯示優化一次。
```js
game.display_operations()
```

## game.display()
顯示類型會根據"遊戲類型"的不同而有不同的顯示類型
+ canvas｜save_jpeg, save_png
+ sdl｜save_jpeg, save_png, sdl_display
(save_jpeg, save_png會將儲存的照片存到 LightEngine/Save 名稱會為 "遊戲名稱".jpeg, png)

(sdl_display 會將畫面顯示到SDL的Window上)
```js
game.display('sdl_display')
```

## game.setWidth()
設定視窗的寬度
```js
game.setWidth(value) 
```

## game.setHeight()
設定視窗的高度
```js
game.setHeight(value)
```

## game.setCameraX()
設定鏡頭的X
```js
game.setCameraX(value)
```

## game.setCameraY()
設定鏡頭的Y
```
game.setCameraY(value)
```

## game.getFPS()
取得遊戲的FPS (只有類型為 sdl 的遊戲才能使用)
```
game.getFPS()
```

# game.create
create是在是在Class[遊戲](#game)裡的一個Class，你可以用它來創建材質.物件.音頻等等

## game.create.object
你可以透過使用[game.create.object]搭配[ObjectBuilder](#objectbuilder)來創建一個新的物件。

```js
//使用Builder來創建物件的範例
let object = game.create.object(
  new ObjectBuilder()
    .id('Object1')
    .texture('Texture1')
)

//使用Object來創建
//參數必須是完整的，否則將會報錯
let game = LE.create.game({
  game: game, //指定的遊戲的ID或遊戲返回的Class
  id: 'Object1',
  x: 0,
  y: 0,
  texture: 'Texture',
  larer: 1,
  width: 0,
  height: 0,
  angle: 0,
  effect: {
    blur: 0,
    opacity: 0,
    brightness: 0,
    grayscale: 0,
    hueRotate: 0,
    invert: 0,
    saturate: 0
  }
})


# game.window
window是在Class[遊戲](#game)裡的一個Class，他只會在[遊戲](#game)類別為 sdl 時創建，你可以用它來取得視窗的參數，或控制的視窗的參數。

(您不能透過改變Class的參數來對遊戲進行改動，只能透過呼叫Class的函示來改動。)

> [所有的參數](#valuewindow)

## game.window.setFullScreen()
將視窗變成全螢幕
```js
game.window.setFullScreen(true) //傳入一個布林值

//返回傳入的布林值
console.log(game.window.setFullScreen(true)) //true
```

## game.window.setMaxSize()
將視窗最大化
(必須先將 [game.window.setResizable](#gamewindowresizable) 設為true後才能將視窗最大化)
```js
game.window.setMaxSize(true) //傳入一個布林值

//返回傳入的布林值
console.log(game.window.setMaxSize(true)) //true
```

## game.window.setMinimize()
將視窗最小化
```js
game.window.setMinimize(true) //傳入一個布林值

//返回傳入的布林值
console.log(game.window.setMinimize(true)) //true
```

## game.window.setResizable()
將視窗設為可/否調整大小 (為true的話用戶可以自行調整大小，且可以將視窗最大化)
```js
game.window.setResizable(true) //傳入一個布林值

//返回傳入的布林值
console.log(game.window.setResizable(true)) //true
```

## game.window.setVisible
設定視窗是否可視
```js
game.window.setVisible(true) //傳入一個布林值

//返回傳入的布林值
console.log(game.window.setVisible(true)) //true
```

## game.window.setBorderless()
將視窗的邊框去除
```js
game.window.setResizable(true) //傳入一個布林值

//返回傳入的布林值
console.log(game.window.setResizable(true)) //true
```

## game.window.event()
取得window的事件，如視窗移動.視窗大小改變等等

**所有的event**
> resize｜視窗大小改動｜返回 {width, height}

> maximize｜視窗最大化｜不返回任何東西

> minimize｜視窗最小化｜不返回任何東西

> move｜視窗移動｜返回 {x, y}

> show｜視窗顯示｜不返回任何東西

> hide｜視窗隱藏｜不返回任何東西

> restore

> close｜視窗關閉

> hover｜鼠標進到視窗｜不返回任何東西

> leave｜鼠標離開視窗｜不返回任何東西

```js
//事件 move 的範例
game.window.event('move', (data) => {
  console.log(data)
})
```

# 參數

## value.game
```js
{
  id, //遊戲ID (string or number)
  name, //遊戲名稱 (string or number)
  type, //遊戲類型(sdl 或 canvas)
  width, //遊戲畫面寬度 (number)
  height, //遊戲畫面高度 (number)
  camera_x, //鏡頭X (number)
  camera_y, //鏡頭Y (number)
  preload_range //預加載範圍，數字越高會在顯示優化的時候加載更多螢幕外的內容 (number)
}
```

## value.windows
```js
  x, //視窗的X (number)
  y, //視窗的Y (number)
  width, //視窗的寬度 (number)
  height, //視窗的高度 (number)
  visible, //視窗是否可視 (boolean)
  fullscreen, //視窗是否為全螢幕 (boolean)
  maximize, //視窗是否為最大化 (boolean)
  maximize, //視窗是否為最小化 (boolean)
  resizable, //視窗是否可以調整大小 (boolean)
  borderless //視窗是否去除邊框 (boolean)
```
