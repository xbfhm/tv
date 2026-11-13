# 第三方组件与许可声明

AnimeTV（本项目）以 **GPL-3.0** 发布。以下为运行期依赖的第三方开源组件，
其版权归各自作者所有，许可条款以各自仓库为准。分发本项目的二进制时，
请一并保留本文件与各组件的许可声明。

---

## 运行期依赖

| 组件 | 版本 | 许可证 | 项目地址 | 用途 |
|---|---|---|---|---|
| AndroidX AppCompat | 1.6.1 | Apache-2.0 | https://github.com/androidx/androidx | 兼容主题 / 基础 Activity |
| AndroidX RecyclerView | 1.3.2 | Apache-2.0 | https://github.com/androidx/androidx | 海报墙 / 选集列表 |
| AndroidX CardView | 1.0.0 | Apache-2.0 | https://github.com/androidx/androidx | 圆角卡片 |
| AndroidX Media3 (ExoPlayer) | 1.3.1 | Apache-2.0 | https://github.com/androidx/media | 视频解码 / 播放 |
| AndroidX Media3 UI | 1.3.1 | Apache-2.0 | https://github.com/androidx/media | PlayerView / 画面比例 |
| AndroidX Media3 HLS | 1.3.1 | Apache-2.0 | https://github.com/androidx/media | m3u8 播放 |
| AndroidX Media3 Datasource OkHttp | 1.3.1 | Apache-2.0 | https://github.com/androidx/media | 自定义网络数据源 |
| OkHttp | 4.12.0 | Apache-2.0 | https://github.com/square/okhttp | HTTP 客户端 |
| Okio | 3.x | Apache-2.0 | https://github.com/square/okio | OkHttp 依赖 |
| Glide | 4.16.0 | BSD-2-Clause / MIT / Apache-2.0 | https://github.com/bumptech/glide | 封面图加载 |
| jsoup | 1.15.3 | MIT | https://github.com/jhy/jsoup | HTML 解析（规则引擎）|
| JsoupXpath | 2.5.3 | Apache-2.0 | https://github.com/zhegexiaohuozi/JsoupXpath | XPath 选择器 |
| Apache Commons Lang | 3.12.0 | Apache-2.0 | https://github.com/apache/commons-lang | 字符串工具 |
| ANTLR 4 Runtime | 4.7.2 | BSD-3-Clause | https://github.com/antlr/antlr4 | JsoupXpath 依赖 |
| SLF4J API | 1.7.36 | MIT | https://github.com/qos-ch/slf4j | 日志门面 |
| Desugar JDK Libs | 2.0.4 | Apache-2.0 | https://developer.android.com/studio/write/java8-support | Android 5.x Java 8 API 回填 |

---

## 数据 / 规则来源

| 名称 | 许可证 | 说明 |
|---|---|---|
| [Predidit/KazumiRules](https://github.com/Predidit/KazumiRules) | GPL-3.0 | 内置 `assets/rules.json` 剧集解析规则，本项目对其做了筛选与字段适配 |
| AGE 动漫公开接口 | 由原站点所有 | 仅作为内容索引与播放地址来源，本项目不存储任何视频文件 |

---

## 许可证全文

- Apache-2.0：https://www.apache.org/licenses/LICENSE-2.0
- MIT：https://opensource.org/licenses/MIT
- BSD-2-Clause：https://opensource.org/licenses/BSD-2-Clause
- BSD-3-Clause：https://opensource.org/licenses/BSD-3-Clause
- GPL-3.0：见本仓库 [LICENSE](LICENSE)

---

## 关于 GPL-3.0 与规则文件

`assets/rules.json` 来源于 GPL-3.0 的 KazumiRules 项目，因此本项目整体选择
GPL-3.0 发布，以保证与上游许可兼容。

如果你只想复用本项目的**播放器代码**而不想受 GPL 约束，
可自行移除 `assets/rules.json` 与 `com.animetv.rule` 包后按需重写。
