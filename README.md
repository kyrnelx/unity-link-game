# 连连看 · LinkGame

Unity **2022.3.62f3c1** · URP 14.0.12

用 Unity 打开 `LinkGame/`，运行 `Assets/Game/LinkGame/Scenes/MainScene.unity`。玩法逻辑（Core / Managers）由 AI Agent 生成。

## 玩法

点两个同图案节点，路径 0/1/2 拐点可达即消除；消完自动进下一关，无解自动重排。编辑器运行中按 `空格` 自动连一对。

## 目录

- `Assets/Game/LinkGame` — 场景、Runtime（Core / Managers / Views）、Res
- `Assets/Platform` — 平台抽象（广告 / 游戏圈）
- `Assets/Core` — Debugger、Singleton
- `Assets/Res` — 字体、音效、图案
- `Assets/Plugins/Editor/FontPruner` — 字体裁剪工具
- `Assets/WX-WASM-SDK-V2`、`Assets/WebGLTemplates` — 微信小游戏配置与模板

## 字体裁剪

`pip install fonttools brotli` → 双击 `run.bat`，覆盖输出 `Assets/Res/Fonts/msyh_compressed.ttf`。

## 微信小游戏

切到 WebGL → 菜单 `微信小游戏 / 转换小游戏` → 微信开发者工具打开输出目录。

## 依赖

 com.qq.weixin.minigame
