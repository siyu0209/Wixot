# 第三方组件与声明

Wixot 的宿主层（`com.wixot.player`）是本项目自有代码，以 MIT 许可发布。
下面列出随安装包或前端产物一同分发的第三方组件及其许可。

## 前端运行时（`web/`）

| 组件 | 版本线索 | 许可 |
| --- | --- | --- |
| Next.js | App Router 静态导出（`react-server-dom` flight 产物，构建 ID `sUs_hcBFTutaafUCFRVpW`） | MIT |
| React / React DOM | 19.x（`react-server-dom-webpack` 客户端运行时） | MIT |
| React Server Components | Flight 协议载荷（各路由 `index.txt`） | MIT |

`web/` 为 Next.js 构建产物，JS 已压缩。相关版权归 Vercel, Inc. 与 Meta Platforms, Inc. 所有，均以 MIT 许可分发。

## Android 构建与运行时

| 组件 | 许可 |
| --- | --- |
| Android SDK / AndroidX（随平台分发的基础库） | Apache License 2.0 |
| Android WebView / Chromium（系统组件，未随包分发） | BSD-style / 系统许可 |

安装包内**不含**任何原生库（`.so`），不含 FFmpeg、不含 Python 解释器、不含统计或广告 SDK。

## 图标

`docs/icon-512.png`、`web/icons/*` 与 `res/mipmap-*` 为本项目自有图形，随 MIT 许可发布。

## 数据源

Wixot 不打包任何视频内容。默认源清单中的条目均为**第三方公开接口**，本项目对其内容、可用性与合法性不作任何主张，也不承担由此产生的责任。第三方站点的名称与商标归各自所有者所有。

本项目与豆瓣、Bangumi、哔哩哔哩、抖音、TVBOX 及任何采集站均无隶属或合作关系。
