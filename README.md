# Wixot · 影视聚合播放器

[![Release](https://img.shields.io/github/v/release/siyu0209/Wixot?include_prereleases&sort=semver&color=0b101a)](https://github.com/siyu0209/Wixot/releases)
[![License: MIT](https://img.shields.io/badge/license-MIT-0b101a)](LICENSE)
[![Platform](https://img.shields.io/badge/Android-7.0%2B%20(API%2024)-0b101a?logo=android)](https://developer.android.com)
[![Size](https://img.shields.io/badge/APK-2.51%20MB-0b101a)](#下载)

> 把「多源聚合搜索」装进一个 HTML5 播放器。
> 一个 2.5 MB 的 Android 客户端：内置本地 HTTP 服务 + Next.js 前端 + Apple CMS 采集源聚合 + HLS 反代，装完即用，不需要注册服务器、不需要外挂脚本。

---

## 下载

| 版本 | 文件 | 大小 | 最低系统 |
| --- | --- | --- | --- |
| **5.2.0** (versionCode 5) | [`Wixot-5.2.0.apk`](https://github.com/siyu0209/Wixot/releases/latest/download/Wixot-5.2.0.apk) | 2,636,830 字节（2.51 MB） | Android 7.0（API 24） |

```
SHA-256  5bbbf7364670af6f06755326fff4037963ebba73b2640924fb4dcd8e08bbce46
包名     com.wixot.player
签名     v1 + v2 + v3（证书 SHA-256 1c10fe5bdb72b299eb775e4e091b57876c0a3eb573989585ea9dd72b24743416）
```

安装：下载 APK → 允许「安装未知来源应用」→ 安装。首次启动会先在本地起一个 HTTP 服务，再加载内置前端，**没有开屏广告、没有账号墙、不联网也能进界面**。

---

## 它是什么

Wixot 不是一个自带片库的播放器，而是一个**播放器外壳 + 源聚合器**：

- 前端是一套 Next.js 静态导出的 SPA，打包在 APK 的 `assets/web/` 里；
- `MainActivity` 启动 `LocalServer`，在 `127.0.0.1` 上把这套静态资源跑起来，WebView 打开 `http://127.0.0.1:<port>/`；
- 前端的所有请求都打到本地服务的 `/api/*`，由 Java 层负责跨域、防盗链、分片改写、并发搜索、本地账号等 HTML5 做不到的事；
- 视频源来自用户自配或内置的 **Apple CMS（苹果CMS）采集接口** 与 **IPTV/M3U 直播列表**，Wixot 本身不存储、不转码、不代理整片内容。

一句话：**UI 在 WebView 里，能力和网络在 Java 里，内容在第三方源里。**

---

## 功能

### 聚合搜索与发现
- **多源并发搜索**：一次性向所有已勾选的点播源并发请求，单源独立超时（`SEARCH_SOURCE_TIMEOUT_MS`），慢源不拖累整体；结果按标题归一化去重合并。
- **源健康度**：连续超时/失败的源会被临时停用，到期自动恢复；长期失败的需在设置里手动恢复，避免每次搜索都白等。
- **豆瓣榜单 / 热门**、**Bangumi 番组日历** 作为发现入口；封面优先走本地代理并伪装 Referer，失败回退公共镜像与直连。
- 搜索历史、最近搜索、分页加载。

### 播放
- WebView + **原生全屏容器**：横屏锁定、沉浸式状态栏、播放时屏幕常亮、返回键退出全屏。
- **HLS 反代**：`Proxy` 为播放列表、分片与密钥补上正确的 `Referer` / `User-Agent`，直连被跨域、防盗链、混合内容拦下时整集流量走本地中转。
- **多码率择优**：解析 `#EXT-X-STREAM-INF`，按分辨率挑选变体；单码率/直链也能放。
- **广告切片过滤**：按分片 URL 特征、片头插入、长片间超短中插识别并剔除，不改动 `DISCONTINUITY` 时间轴。
- 倍速、自动连播、断点续看、全屏手势、音量与进度恢复。

### 缓存与下载
- **分片缓存**：播放/暂停时把后续分片写入浏览器本地存储（IndexedDB），二次播放与弱网卡顿直接命中；不计入配置导出。
- **下载管理**：直连源站，或开启「下载中转」由本地服务转发（带宽由设备承担）；不支持 `BYTERANGE` 分片的集会明确提示。
- 无磁盘直写能力时退化为内存聚合后交给浏览器下载，并提示可能卡顿。

### 源与订阅
- 点播源 / 直播源分栏管理：增删改、启用停用、可用性测试（搜索耗时与结果量）、单独恢复。
- **订阅**：一份 JSON 可同时下发点播源与直播源，支持 Wixot 源列表与 **TVBOX 配置**（仅导入可直接使用的接口，Spider 类站点会跳过）；重新同步以远端为准整体替换。
- **一键发布订阅**：把当前启用的源打包上传到公开粘贴板，生成可直接分享的订阅链接。
- **配置导入导出**：数据源、订阅、偏好设置整机迁移。

### 账号与同步
- **本机账号**：仅用户名 + 密码（至少 4 位），加盐多轮哈希后存本机文件；不含手机号、邮箱等任何附加信息，也不上传。
- 收藏、观看记录与进度、搜索记录、偏好设置。
- **可选跨设备同步**：自填同步服务地址后，多设备按「每条数据最后操作时间取较新」增量合并，断网时照常读写本地，恢复后后台补同步。

### 界面
- 深色 / 浅色 / 跟随系统三态主题，默认深色（`#0b101a`）。
- 成人内容过滤（按关键词标记源与条目）。
- 全屏、大屏与横屏适配；`largeHeap` 应对大体积聚合。

---

## 架构

```
┌─────────────────────────────── Android App ───────────────────────────────┐
│                                                                           │
│  MainActivity                                                             │
│   ├─ WebView  ──────────────┐   http://127.0.0.1:<port>                    │
│   ├─ 全屏容器 / 沉浸式 / 常亮 │                                             │
│   └─ 外链与下载拦截           │                                             │
│                              ▼                                            │
│  LocalServer  ──  静态资源  assets/web  (Next.js 静态导出)                  │
│   ├─ /api/search  /api/detail  /api/douban  /api/hot-list                 │
│   ├─ /api/bangumi/calendar                                                │
│   ├─ /api/live/{playlist,epg,probe,stream}                                │
│   ├─ /api/proxy/*        → Proxy   补 Referer/UA, 分片与密钥转发            │
│   ├─ /api/source-list  /api/source/test  /api/publish  /api/quality       │
│   ├─ /api/status                                                          │
│   └─ /api/user/{register,login,logout,me,favorites,history}                │
│                                                                           │
│  Api    并发聚合 / 路由 / TTL 缓存     Cms   Apple CMS 解析 / 成人过滤        │
│  M3u8   播放列表改写 / 变体择优        Proxy 反向代理 / 直播拉流              │
│  Net    HttpURLConnection 客户端      Http  极简 HTTP/1.1 服务端原语         │
│  Sources 内置源清单                   Users 本机账号（加盐哈希）             │
│  Cache  带 TTL 的软上限缓存                                                │
└───────────────────────────────────────────────────────────────────────────┘
```

| 类 | 职责 |
| --- | --- |
| `MainActivity` | WebView 宿主、沉浸式与全屏容器、屏幕常亮、外链与下载拦截、先起服务再载入前端 |
| `LocalServer` | 内嵌 HTTP 服务：静态文件 + MIME 表 + 固定端口回退 + 线程池 |
| `Http` | HTTP/1.1 请求解析、头/体上限、URL 编解码、流式写出 |
| `Net` | `HttpURLConnection` 客户端，统一 UA（`WixotAndroid/5.2.0`）与超时 |
| `Api` | 路由与聚合引擎：并发搜索、详情/画质 TTL 缓存、豆瓣榜单、Bangumi 日历、直播 EPG 与探活 |
| `Cms` | 苹果 CMS 接口解析：搜索、详情、剧集提取（`M3U8_PATTERN`）、标题归一化、成人关键词过滤 |
| `M3u8` | 播放列表改写、`#EXT-X-STREAM-INF` 变体择优、相对地址绝对化 |
| `Proxy` | 分片/密钥反向代理，注入 `Referer` 与 UA，流式转发不落盘 |
| `Sources` | 内置点播/直播源清单（JSON） |
| `Users` | 本机账号：注册、登录、令牌、收藏与历史持久化 |
| `Cache` | 带过期时间与软上限的内存缓存 |

### 本地接口一览

| 方法 | 路径 | 说明 |
| --- | --- | --- |
| GET | `/api/search` | 多源聚合搜索（`wd`、分页、源过滤） |
| GET | `/api/detail` | 影片详情 + 剧集列表 |
| GET | `/api/douban` | 豆瓣榜单 / 热门条目 |
| GET | `/api/hot-list` | 热门聚合 |
| GET | `/api/bangumi/calendar` | Bangumi 番组日历 |
| GET | `/api/live/playlist` | 直播频道列表 |
| GET | `/api/live/epg` | 直播节目单 |
| GET | `/api/live/probe` | 直播源探活 |
| GET | `/api/live/stream` | 直播流转发 |
| GET | `/api/proxy/*` | 通用反代（补 Referer/UA） |
| POST | `/api/source/test` | 源可用性与测速 |
| GET/POST | `/api/source-list` | 源列表读写 |
| POST | `/api/publish` | 发布订阅到公开粘贴板 |
| GET/POST | `/api/quality` | 画质偏好与失败记忆 |
| GET | `/api/status` | 运行状态 |
| POST | `/api/user/register` · `/login` · `/logout` | 本机账号 |
| GET | `/api/user/me` · `/favorites` · `/history` | 账号数据 |

---

## 数据与隐私

- **不上传观看数据。** 搜索历史、观看进度、收藏、偏好保存在本机（`IndexedDB` + `localStorage` + 应用私有文件）。
- **账号只在本机。** 用户名 + 密码加盐多轮哈希后写入应用私有目录，不上传、不含手机号/邮箱；卸载即失效（可用导出的配置迁移）。
- **只申请两个权限**：`INTERNET`、`ACCESS_NETWORK_STATE`。没有存储权限、没有位置、没有通讯录、没有通知。
- **允许明文 HTTP**：因为大量第三方采集站仍是 `http://`，客户端需要直连；HTTPS 站点不受影响。
- **不做遥测**：没有统计 SDK、没有崩溃上报、没有广告 SDK。

---

## 运行要求与已知限制

- Android 7.0（API 24）及以上；`targetSdk 35`。
- 无原生库，纯 Java + WebView，安装包 2.5 MB。
- 直播/点播是否可播取决于源本身，源失效需要在设置里更换或等待订阅更新。
- `BYTERANGE` 分片的集不支持本地缓存与下载。
- HTTP 源在 HTTPS 页面下会被混合内容拦截，此时需开启「下载中转」或更换源。

---

## 仓库内容

```
Wixot-5.2.0.apk        安装包（Release 附件同源）
web/                   从 APK 的 assets/web/ 原样提取的前端静态产物（未做任何修改）
docs/icon-512.png      应用图标
README.md              本文件
CHANGELOG.md           版本记录
THIRD_PARTY_NOTICES.md 第三方组件与商标声明
LICENSE                MIT
```

> `web/` 是构建产物（Next.js 静态导出，JS 已压缩），用于审阅界面行为与接口调用，不是可再构建的源码工程。Java 宿主层的源码不在本次发布范围内，仓库按「构建产物 + 前端资源 + 文档」开源。

---

## 免责声明

Wixot 是一个**空壳播放器**：不存储、不制作、不转码、不分发任何视频内容，也不内置任何片库。所有内容均来自用户自行添加或内置的**第三方公开接口**，其合法性与可用性由对应数据源负责。

请仅将本软件用于访问你有权访问的内容，并遵守你所在地区的法律法规与内容提供方的服务条款。因使用第三方数据源产生的任何纠纷，与本项目无关。

本项目与豆瓣、Bangumi、Bilibili、抖音、TVBOX 及任何采集站均无关联，相关名称与商标归各自所有者所有。

---

## License

[MIT](LICENSE) © 2026 siyu0209

---

## English

**Wixot** is a 2.5 MB Android video aggregator player. The UI is a Next.js static export bundled inside the APK; `MainActivity` boots an embedded HTTP server on `127.0.0.1` that serves those assets and exposes a small `/api/*` surface implemented in Java — concurrent multi-source (Apple CMS) search, HLS playlist rewriting with variant selection, a reverse proxy that injects the right `Referer`/`User-Agent` for anti-leech protected segments, ad-slice filtering, local segment caching, a local-only account store, and optional self-hosted cross-device sync.

It ships **no content**: every title comes from a third-party public API that you configure. Only `INTERNET` and `ACCESS_NETWORK_STATE` are requested; no analytics, no ads, no telemetry.

Requires Android 7.0 (API 24)+. Grab the APK from [Releases](https://github.com/siyu0209/Wixot/releases/latest). Licensed MIT.
