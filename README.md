# AnimeTV · 免费追番电视应用（Java 原生版）

<p align="center">
  <b>基于 AGE 动漫官方 API 的免费追番 TV 应用</b><br>
  原生 Java + ExoPlayer 硬解 · 专为老电视优化 · 遥控器优先交互
</p>

<p align="center">
  <img alt="version" src="https://img.shields.io/badge/version-7.3-FF3D71">
  <img alt="minSdk" src="https://img.shields.io/badge/minSdk-21-7C5CFF">
  <img alt="targetSdk" src="https://img.shields.io/badge/targetSdk-26-7C5CFF">
  <img alt="license" src="https://img.shields.io/badge/license-GPL--3.0-blue">
</p>

> **v7.3**（2026-11）— 修复播放页 `ClassCastException`（黑屏真因）；v7.2 的错误上屏与诊断信息保留。
> v7.1 的跨源自动换源、v7.0 的界面 / 键位 / 秒开优化全部保留。详细变更见 [CHANGELOG.md](CHANGELOG.md)。

---

## 目录

- [为什么是原生 Java](#为什么是原生-java)
- [v7.0 亮点](#v70-亮点)
- [功能一览](#功能一览)
- [遥控器操作（重点）](#遥控器操作重点)
- [键位自定义](#键位自定义)
- [设置项说明](#设置项说明)
- [界面结构](#界面结构)
- [技术架构](#技术架构)
- [构建 APK](#构建-apk)
- [目录结构](#目录结构)
- [兼容性](#兼容性)
- [常见问题](#常见问题)
- [已知限制](#已知限制)
- [贡献](#贡献)
- [许可与致谢](#许可与致谢)
- [免责声明](#免责声明)

---

## 为什么是原生 Java

最早的版本是 Kivy/Python + buildozer，在电视上受限于 SDL2 软渲染和 ffpyplayer 软解，
1080p 卡顿、老 GPU 兼容差、APK 体积大（字体 + 解释器）。v5.0 起重写为原生 Java：

| | Kivy / Python 版 | Java 原生版（本仓库） |
|---|---|---|
| 渲染 | SDL2 + OpenGL 自绘 | **Android 系统控件 + 硬件加速** |
| 播放 | ffpyplayer 软解 | **ExoPlayer 硬解（走电视芯片解码器）** |
| 内存 | 解释器 + SDL2 ≈ 250MB | 原生 ≈ 80MB |
| 老系统兼容 | 闪退风险 | **minSdk 21 / targetSdk 26**（云视听式宽适配） |
| 中文字体 | 需打包 5.7MB 字体 | **系统自带 CJK 字体** |
| APK 体积 | ≈ 200MB（含浏览器内核） | **≈ 9MB** |

---

## v7.3 修复（黑屏真因）

播放页把 `menuPanel`(ScrollView) 和 6 个面板行(LinearLayout) 声明成了 `LinearLayout`/`TextView`，
`bindViews()` 第一处强转就崩 → 黑屏。v7.3 全部改用 `View` 接收，并加了「布局控件类型 ↔ Java 字段类型」
自动比对，当前 0 处不匹配。

## v7.2 重点（不再黑屏、返回必生效）

| 现象 | 原因 | 处理 |
|---|---|---|
| 点播放后**全黑** | 播放页进入时控制条是隐藏的，起播失败就什么都看不到 | 进入即显示标题/进度/按钮；失败时画面中央显示「错误码 + 线路 + 地址」 |
| **返回键没反应** | `IMMERSIVE_STICKY + HIDE_NAVIGATION` 全屏，部分 Android 5.x 电视会吞掉第一次 BACK | 只用 `FULLSCREEN`，并在 `dispatchKeyEvent` 最外层拦截，**一次退出** |
| 卡在加载中 | 播放器没起来时没有任何反馈 | 「先画一帧再初始化播放器」+ 12 秒看门狗 + 10 秒后自动换源 |
| 闪退只有黑屏，查不到原因 | 崩溃后进程直接被杀 | 崩溃会拉起 `CrashActivity` 显示堆栈；「设置 → 诊断信息」随时可看 |

> 遥控器没有菜单键？**长按 OK** 也能打开「更多功能」。

## v7.1 重点（修复"播不了"）

| 问题 | 根因 | 修复 |
|---|---|---|
| **所有 AGE 线路都瞬间失败** | v6.0 引入 Kazumi 支持时，AGE 线路的 `Episode.source` 从未赋值，`"AGE".equals(source)` 恒为 false，于是每条线路都被当成"网页嗅探"去解析一个空地址 | 显式标记 `source="AGE"`，并把类型默认值设为 `"AGE"`；解析前先判断是否已是 m3u8/mp4 直链 |
| 只有 5 条线路可选，全挂就没救 | AGE 只暴露 5 条免费线，且都依赖同一个解析站 | 新增 **非凡资源**（独立 10 万+ 片库，m3u8 直链）；全部失效时**自动跨源找同一集** |
| 换源要手动、还慢 | 只能在线路间轮换 | 「更多功能 → 自动换源」一键找，且失败时自动触发（先查非凡，再并行搜 25 条规则源） |
| 线路解析串行等待 | 5 条线路 × 8 秒 | 改成 4 线程并行，成功即停 |

新增「非凡资源」搜索源：首页顶栏 **源 → 非凡资源**，独立片库，AGE 挂了他也能用。

## v7.0 亮点

### 1. 焕新的界面

- **深海夜色 + 霓虹渐变**主题（`#070A11 → #121B2C`，品红 `#FF3D71` → 紫 `#7C5CFF`）。
- 顶部**胶囊导航**：聚焦时整颗胶囊渐变点亮，选中态常亮。
- **全屏海报墙**：圆角卡片 + 底部渐变标题 + 聚焦霓虹描边，格数按屏幕宽度自适应。
- 详情改为**底部抽屉**（覆盖式滑出），不再挤压海报墙。
- 全部动效走 `ViewPropertyAnimator`（属性动画 + 硬件图层），
  Mali-450 / 2GB 内存的老电视也能跑满帧，不用 RenderScript、不做实时模糊。

### 2. 遥控器交互按"手感"重做

| 按键 | 行为 |
|---|---|
| **OK / 确定** | 播放 / 暂停（**始终**是播放暂停，不会再误触按钮）|
| **← / → 单击** | 快退 / 快进 **5 秒**（秒数可调）|
| **← / → 长按** | **连续拖动进度条**，按住越久每步越大（1s → 10s），松手定位 |
| **↑ / ↓** | 音量 + / -（中央 HUD 提示）|
| **菜单键** | 唤出「更多功能」面板：倍速 / 画质 / 线路 / 选集 / 画面比例 / 播放信息 |
| **返回** | 关面板 → 收控制条 → 退出播放 |

> 拖动时自动暂停、松手自动续播，避免老电视因连续 seek 反复缓冲。

### 3. 键位可自定义

「设置 → 键位设置」里，选中任意一项按 OK，然后**直接按遥控器上想绑定的键**即可。
播放/暂停、更多功能、快进、快退、音量±、上/下一集全部可改，并会提示键位冲突。

### 4. 打开就快

| 优化 | 说明 |
|---|---|
| 首页缓存直出 | 先用磁盘缓存渲染海报墙（0 等待），再后台静默刷新 |
| 焦点预取 | 海报获得焦点 350ms 后，后台预取该番详情 |
| 直链预解析 | 打开详情时后台就把当前集直链解析好放进内存 LRU |
| 解析并发 | 最多 4 条线路并行解析，成功即停 |
| 播放器缓冲 | 起播 0.9s（可调 0.5s），缓冲池 20s / 60s |
| 图片 | Glide RGB_565 + 固定采样尺寸 + 150ms 淡入，滚动不卡 |
| 硬件加速 | 全局 `hardwareAccelerated=true`（旧版关掉了，是滚动卡顿主因）|

---

## 功能一览

- 🔥 **首页**：AGE 最新更新 + 本周新番，封面海报墙；顶部「继续观看」卡片一键续播
- 🔍 **搜索**：AGE 官方 / 非凡资源（10 万+ 独立大库）/ 聚合搜索 / 25 条 Kazumi 规则源，可随时切换
- 📺 **追番目录**：2026 年冬 / 春 / 夏三季新番
- 🕘 **播放历史**：OK 恢复进度，长按删除单条
- ▶ **内置播放器**：ExoPlayer 硬解、HLS 支持、全屏沉浸、自动连播
- 🎚 **进度控制**：单击 ±5s、长按拖动、触屏拖进度条
- ⏩ **倍速**：0.5x / 0.75x / 1.0x / 1.25x / 1.5x / 1.75x / 2.0x，跨集跨会话记忆
- 🎞 **画质**：自适应 / 1080p / 720p / 480p / 360p，运行时立即生效
- 🖼 **画面比例**：适应 / 拉伸 / 裁剪（解决不同比例片源黑边）
- 🔁 **自动换源**：线路轮换 → 跨源搜索（非凡 + 25 条规则源），全自动
- 💾 **缓存管理**：设置页可查看占用并一键清理

---

## 遥控器操作（重点）

### 主界面

| 按键 | 行为 |
|---|---|
| 方向键 | 在导航 / 海报墙 / 选集之间移动焦点 |
| OK | 打开详情（海报）或执行按钮 |
| 返回 | 详情 → 首页；首页连按两次退出 |
| 搜索按钮 | 弹出搜索框（遥控器直接输入） |
| 源按钮 | 切换搜索来源 |

### 播放页

见 [v7.0 亮点 - 遥控器交互](#2-遥控器交互按手感重做)。

`更多功能` 面板内的操作：↑↓ 选行，OK 打开选项，←→ 关闭面板后仍可拖动进度。

---

## 键位自定义

位置：**设置 → 键位设置**

```
播放 / 暂停        OK / 确定
更多功能菜单       菜单键
快进 / 长按拖动    右
快退 / 长按拖动    左
音量 +            上
音量 -            下
下一集            媒体下一曲
上一集            媒体上一曲
```

- 点击任意一项 → 弹窗「按下遥控器上要绑定的按键」→ 按键即生效。
- 若与其他动作冲突，会提示冲突项（仍会保存，播放键优先）。
- 「恢复默认键位」一键还原。
- 映射存在 `SharedPreferences("player_pref")`，升级不丢。

---

## 设置项说明

| 分组 | 项目 | 说明 |
|---|---|---|
| 播放 | 默认画质 | 进入播放器时的画质上限 |
| 播放 | 默认倍速 | 进入播放器的初始倍速 |
| 播放 | 自动连播 | 本集播完是否自动下一集 |
| 播放 | 快进步长 | 单击 ←/→ 的秒数：3 / 5 / 10 / 15 / 30 |
| 播放 | 长按拖动速度 | 慢 / 中 / 快（影响长按拖动时每步时长）|
| 播放 | 起播缓冲 | 极速 0.5s / 均衡 0.9s / 稳定 2s |
| 键位 | 键位设置 | 见上一节 |
| 界面 | 界面模式 | 电视端（遥控器）/ 手机端（触屏）|
| 界面 | 首页列数 | 自动 / 3 / 4 / 5 / 6 列 |
| 界面 | 控制条自动隐藏 | 2 / 4 / 8 秒 / 不自动隐藏 |
| 缓存 | 接口缓存 | 显示占用，点击清理 |
| 关于 | 版本 / 规则源 / 许可 / 声明 | — |

> 所有行都支持遥控器 **←→ 直接改值**（不必进弹窗）。

---

## 界面结构

```
Activity_main (TV)
├── Header        品牌 / 胶囊导航（首页·追番·历史·设置）/ 搜索 / 源
├── tvListTitle   当前列表标题 + 数量
└── FrameLayout
    ├── RecyclerView   海报墙（GridLayoutManager 自适应列数）
    ├── loadingBox     加载态（淡入淡出）
    └── detailPanel    底部抽屉（滑入）：封面 + 信息 + 选集胶囊 + 操作按钮

activity_player
├── PlayerView        视频画面（surface_view）
├── loadingBox        缓冲 / 解析提示
├── tvCenterPlay      暂停时浮现的中央播放键
├── hudText           快进/快退/音量/倍速 HUD
├── topBar            返回 + 当前集 + 菜单提示
├── controlBar        进度条 + 播放/上集/下集/倍速/画质/旋转/更多/退出
└── menuPanel         右侧「更多功能」面板（6 行 + 操作提示）
```

---

## 技术架构

| 层 | 选型 |
|---|---|
| UI | 系统控件 + AppCompat + RecyclerView + CardView（无 Material Components，省体积）|
| 播放 | AndroidX Media3 / ExoPlayer 1.3.1（`media3-exoplayer-hls` + OkHttp 数据源）|
| 网络 | OkHttp 4.12（全信任 TLS 适配 Android 5 老证书库、HTTP/1.1）|
| 图片 | Glide 4.16（RGB_565 + 磁盘缓存）|
| 解析 | 自有 JSON 解析（`org.json`）+ Kazumi 规则引擎（jsoup / JsoupXpath）|
| 存储 | SharedPreferences（历史 / 偏好）+ 文件磁盘缓存（接口 JSON）|

核心类：

```
com.animetv
├── MainActivity        主界面（导航 / 海报墙 / 详情 / 播放调度）
├── PlayerActivity      播放器（控制条 / HUD / 更多功能面板 / 键位处理）
├── SettingsActivity    设置（选项 / 键位捕获 / 缓存 / 关于）
├── api/AgeApi          AGE 接口、直链解析、磁盘缓存
├── rule/*              Kazumi 规则引擎（JsonPath / 嗅探）
└── util
    ├── Prefs           统一偏好（画质/倍速/步长/缓冲/列数…）
    ├── KeyMap          键位映射与命名
    ├── Motion          属性动画小工具
    ├── FocusAnim       焦点缩放/浮起
    ├── UrlCache        播放直链 LRU（秒开关键）
    ├── DiskCache       接口 JSON 缓存（支持"忽略有效期"直读）
    └── HistoryStore    播放历史 / 续播进度
```

---

## 构建 APK

### 方式一：GitHub Actions（推荐，无需电脑）

1. 仓库根目录放入源码包 `ad7.0-java.zip`（本仓库已包含）；
2. 推送到 `main` 分支，或在 Actions 页手动 `Run workflow`；
3. 等待 5~15 分钟，构建产物：
   - Actions → Artifacts → `animetv-tv-apk`
   - 或直接下载 Release：`https://github.com/xbfhm/tv/releases/download/latest/app-debug.apk`

工作流会**自动选取文件名版本号最大**的 `ad*-java.zip`（`sort -V`），
所以仓库里残留旧包也不会构建错版本。

### 方式二：本地 Android Studio

```bash
unzip ad7.0-java.zip -d ad70_java
cd ad70_java
./gradlew assembleDebug        # 或 gradle assembleDebug
```

环境要求：JDK 17、Android SDK Platform 34、Gradle 8.5、AGP 8.2.2。

### 签名

debug 构建可直接安装（需允许"未知来源"）。
自建 release 签名时记得修改 `app/build.gradle` 的 `signingConfigs`；
不同签名的 APK **不能覆盖安装**，切换前先卸载旧版。

---

## 目录结构

```
tv/
├── README.md                  本文件
├── CHANGELOG.md               版本变更记录
├── LICENSE                    GPL-3.0 全文
├── THIRD-PARTY-NOTICES.md     第三方组件与许可
├── CONTRIBUTING.md            贡献指南
├── .github/workflows/build.yml 云端构建 + 自动发布 Release
├── ad7.0-java.zip             当前版本源码包（CI 直接解压构建）
└── ad6.2-java.zip             历史版本（保留）
```

源码包内：

```
ad70_java/
├── build.gradle / settings.gradle / gradle.properties
├── README.md
└── app/
    ├── build.gradle
    └── src/main/
        ├── AndroidManifest.xml
        ├── assets/rules.json             Kazumi 规则
        ├── java/com/animetv/…            源码
        └── res/                          布局 / 颜色 / 图形资源
```

---

## 兼容性

| 项目 | 说明 |
|---|---|
| 最低系统 | Android 5.0（API 21） |
| 推荐系统 | Android 5.1 ~ 9（`targetSdk 26`，规避 9+ 的存储/权限限制）|
| ABI | arm64-v8a + armeabi-v7a（32/64 位镜像都能装）|
| 实测设备 | 海信 VIDAA V1（MStar MSD6A828 / Mali-450 / 2GB / Android 5.1.1）、realme RMX3820（Android 16）|
| 遥控器 | 标准 DPAD / OK / MENU 键即可，键位可自定义 |

---

## 常见问题

**Q：首页一直转圈 / 报 403？**
AGE 有轻度反爬，App 启动时会预热主站 cookie 并在 403 时重试一次。
若长时间失败，试试：设置 → 接口缓存 → 清理，然后切换「源」为其他规则源。

**Q：版权番点开提示"仅 VIP 源"？**
这类番（爱奇艺 / 腾讯 / 优酷 / 芒果 / B 站）只有会员线路，App 只聚合免费线路，属正常。

**Q：某集黑屏但其他集正常？**
点「更多功能 → 线路」换一条线路；或退出重进（会自动换源）。

**Q：播放卡顿？**
1) 设置 → 画质 调到 720p / 480p；
2) 设置 → 起播缓冲 改成「稳定」；
3) 确认电视没开"节能模式"（会限制 GPU）。

**Q：遥控器按键不灵 / 想换个键？**
设置 → 键位设置，重新绑定即可；映射冲突会有提示。

**Q：改了界面模式没生效？**
切换后会回到主界面并自动重建；若没变化，退出 App 重进一次。

---

## 已知限制

- 部分规则源依赖网页结构，站点改版后可能失效（可在「源」里换一个）。
- 老电视同时解码 1080p H.265 可能力不从心，建议 720p。
- 不支持 DRM 内容，不支持需要登录的会员片源。
- 仅做聚合播放，不提供下载与离线缓存功能。

---

## 贡献

欢迎 Issue / PR：见 [CONTRIBUTING.md](CONTRIBUTING.md)。
新增解析规则请优先向上游 [Predidit/KazumiRules](https://github.com/Predidit/KazumiRules) 提交。

---

## 许可与致谢

- 本项目以 **GNU General Public License v3.0** 发布，全文见 [LICENSE](LICENSE)。
- 第三方组件许可见 [THIRD-PARTY-NOTICES.md](THIRD-PARTY-NOTICES.md)：AndroidX、Media3/ExoPlayer、OkHttp、Glide、jsoup、JsoupXpath、Commons Lang、ANTLR、SLF4J。
- 解析规则来自 [Predidit/KazumiRules](https://github.com/Predidit/KazumiRules)（GPL-3.0），特此致谢。
- 感谢所有在 Issues 中反馈问题的朋友。

---

## 免责声明

本项目**不存储、不制作、不上传**任何视频文件，所有片源均来自第三方公开接口，
版权归原权利方所有。本软件仅用于**学习 Android 开发与播放器技术**，
请勿用于商业用途。喜欢作品请支持正版平台与官方周边。
如有侵权，请联系作者删除相关代码。
