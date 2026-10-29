# 连连看 · LinkGame

Unity **2022.3.62f3c1** + **URP 14.0.12**，竖屏 UI 连连看，可出 Windows / WebGL，已接入**微信小游戏（WXSDK）**。

**运行**：Unity 打开本目录 → `Assets/Game/LinkGame/Scenes/MainScene.unity` → Play（运行中按 <kbd>Space</kbd> 自动连一对）。

## 结构

```
Assets/
├─ Game/LinkGame/              # 场景 + Runtime(Core/Managers/Views) + Res
├─ Platform/Runtime/           # 平台抽象（广告 / 游戏圈）
├─ Core/Runtime/               # Debugger、Singleton
├─ Res/                        # 字体、音效、图案
├─ Plugins/Editor/FontPruner/  # 字体裁剪工具 + 原始字体
├─ WX-WASM-SDK-V2/             # 小游戏工程配置
└─ WebGLTemplates/             # 小游戏 WebGL 模板
```

## 游戏逻辑（AI Agent 生成）

`Runtime/Core`、`Runtime/Managers` 的玩法逻辑由 **AI Agent 生成**：纯 C#、不依赖场景对象，可 EditMode 单测。点两个同图案节点，路径 **0/1/2 拐点**可达即消除；消完自动进下一关，无解自动重排。

规则门面 `LinkManager`，棋盘 `LinkBoard` + 寻路 `LinkPathfinder` + 可解性 `BoardSolver`，计分/关卡 `ScoreCalculator` / `LevelProgress`。表现层（`GameManager`、`NodeSpawner`、连线绘制、UI/音频）为人工实现。

## SDK 框架

**组成**：`com.qq.weixin.minigame`（manifest 中 git 源，锁定 commit `ed4ad28f`）+ `Assets/WX-WASM-SDK-V2/`（`MiniGameConfig.asset`、`link.xml`、加载背景）+ `Assets/WebGLTemplates/`（4 套模板，本工程用 `WXTemplate2022`）。

包内结构：`Editor/`（转换面板与核心、多包融合、Brotli 压缩、Node 工具链、纹理压缩）、`Runtime/`（`WXBase`、asmdef `Wx`、`wx-runtime.dll`）、`WebGLTemplates/`。
菜单：`微信小游戏 / 转换小游戏`、`/ 多包融合工具`、`/ 转换小游戏试玩`。

**MiniGameConfig**：名称「连连冒险」、AppID `wx815cd00d1df25235`、输出目录 `D:\Code\WxMiniGame`、MemorySize 256、`FontOptions.CustomUnicode`（小游戏侧预置字符）。

**Player / WebGL**：Template `PROJECT:WXTemplate2022`、Compression `Disabled`（压缩交给 SDK）、内存 32/2048MB（`TOTAL_MEMORY=256MB`）、Stripping `High`、IL2CPP `OptimizeSize`、Define `WebGL: WECHAT`。

**平台抽象**（`Assets/Platform`）：`Kyrnel.Platform.PlatformManager` 按宏选实现 —— 编辑器/非微信用 `MockPlatform`（广告直接成功），`#if WECHAT` 用 `WeChatPlatform`（WeChatWASM）。接口 `IPlatform = IAdService + IGameClubeService`（激励视频 / 游戏圈），广告位 `adunit-de82036eaa70f864`；道具先看广告再发放。

**构建**：切 WebGL → 确认模板 → `微信小游戏 / 转换小游戏` → 微信开发者工具打开输出目录。

## 字体裁剪（FontPruner）

`Assets/Plugins/Editor/FontPruner/`，用 `fontTools` 子集化：只保留项目实际用到的字 + 3500 常用字，20MB 级字体 → 1~3MB。

- **文件**：`raw_fonts/msyh.ttc`（原始字体）、`3500_common.txt`（常用字）、`my_words.txt`（自定义词）、`font_pruner.py`、`run.bat`
- **使用**：`pip install fonttools brotli` → 双击 `run.bat`
- **输出**：`Assets/Res/Fonts/msyh_compressed.ttf`（覆盖，GUID 不变，场景内所有 Text 立即生效）
- **取字**：ASCII + 3500 常用字 + 自定义词表 + 扫描 `Assets/` 下的 prefab/unity/json/csv/txt/asset/xml（含 `\uXXXX`）
- **配置**：`ENABLE_PROJECT_SCAN = False` 只按常用字裁（最快）；`CUSTOM_TEXT_FILE` 追加词表

## 其它

- 菜单：`Tools/字体替换工具`、`Tools/预制体脚本查找工具`、`Tools/URP 材质紫色修复工具`、路径复制
- 缺字 → 用 `my_words.txt` 补后重跑；材质变紫 → URP 修复工具；首次打开需联网拉取 WXSDK 包
