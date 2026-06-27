# 飞牛TV 弹幕版 🎯

> 基于飞牛影视 API 的第三方 Android 客户端，采用 **多模块 + TV/手机双 Flavor** 架构，支持弹幕、自动连播、TV 遥控器优化操作。

---

## 📥 下载与使用

### 方式一：直接下载 APK
从 [Releases](https://github.com/rgcaafe/fnos_tv_danmu/releases) 页面下载最新版本的 APK 安装包：
- **TV 版**：`FNTV_tv_release_*.apk` — Android TV / 电视盒子
- **手机版**：`FNTV_mobile_release_*.apk` — Android 手机

### 方式二：本地构建

```bash
# 1. 克隆项目
git clone https://github.com/rgcaafe/fnos_tv_danmu.git
cd fnos_tv_danmu

# 2. 编译（需要 Android SDK API 33）
# TV 版
./gradlew assembleTvRelease

# 手机版
./gradlew assembleMobileRelease

# 同时编译两个版本
./gradlew assembleTvRelease assembleMobileRelease

# 3. 编译完成后 APK 位于：
# app/build/outputs/apk/tv/release/FNTV_tv_release_*.apk
# app/build/outputs/apk/mobile/release/FNTV_mobile_release_*.apk
```

> 注意：本地构建需要安装 [Android Studio](https://developer.android.com/studio) 并配置好 Android SDK（API 33）。

### 方式三：GitHub Actions 自动编译发布

项目已配置 GitHub Actions，**无需本地 Android 环境**即可编译。

#### 自动 CI 构建

推送到 `master` 或 PR 时会自动触发 [Android CI](.github/workflows/build.yml)：

- 编译 TV + 手机两个 Release APK
- 产物可在 Actions 页面的 **Artifacts** 中下载（保留 14 天）

#### 正式发布到 GitHub Releases

1. 在 `app/build.gradle` 中更新 `versionCode` / `versionName`
2. 提交并推送到 `master`
3. 打 tag 并推送（tag 必须以 `v` 开头）：

```bash
git tag v1.2.0
git push origin v1.2.0
```

推送 tag 后会自动触发 [Release](.github/workflows/release.yml) 工作流，并：

- 编译 `FNTV_tv_release_v{版本}.apk` 和 `FNTV_mobile_release_v{版本}.apk`
- 创建 GitHub Release 并上传 APK
- 附带 `update.json`（含 GitHub 下载地址，供 App 内更新检测）

> **说明**：当前 Release 使用默认 debug 签名，适合 sideload 安装。如需上架商店，可在仓库 Settings → Secrets 中配置签名密钥后扩展 workflow。

---

## 🏗 项目结构（方案 B：多模块 Monorepo）

```
fnos_tv_danmu/
├── app/                 # 应用壳：Activity、Flavor 分端 UI
│   ├── src/main/        # 共享代码与资源（登录、播放器）
│   ├── src/tv/          # TV 专用：侧栏导航、TV Banner、Leanback 启动
│   └── src/mobile/      # 手机专用：底部 Tab、触屏优化
├── core-api/            # 飞牛影视 API（Retrofit + 模型）
├── core-common/         # 通用工具（图片加载、ExoPlayer 数据源）
├── core-player/         # 播放核心（云直链、剧集管理）
└── feature-danmu/       # 弹幕功能（匹配、渲染、设置）
```

| Flavor | applicationId | 启动方式 |
|--------|---------------|----------|
| `tv` | `com.fntv.app` | `LEANBACK_LAUNCHER` |
| `mobile` | `com.fntv.app.mobile` | `LAUNCHER` |

---

## 📦 功能特点

- **弹幕支持** — 集成 Danmu API，自动匹配番剧弹幕，支持搜索手动选择剧集
- **继续观看** — 记录播放进度，首页快速续播
- **剧集自动连播** — 播放完成后自动播放下一集
- **TV 遥控器优化** — 全界面 DPAD 焦点导航，适配电视遥控器
- **倍速播放** — 支持 0.5x ~ 2.0x 播放速度
- **播放比例** — 适应 / 拉伸 两种模式
- **硬解/软解切换** — 支持硬件解码与软件解码
- **锁定模式** — 锁定后隐藏控制栏，防止误触
- **自动更新** — 内置更新检测，支持 CDN 加速下载安装

---

## 🛠 技术栈

- **开发语言**: Java
- **播放器**: ExoPlayer 2.18.7
- **网络请求**: Retrofit2 + OkHttp3
- **弹幕渲染**: 自定义 Canvas 弹幕引擎
- **最低支持**: Android 4.4 (API 19)
- **目标 SDK**: Android 12 (API 32)

---

## 🔐 登录说明

登录账号密码为 **飞牛影视的账号密码**，非本应用的独立账号。

- 服务器地址格式：`http://<你的NAS地址>:<端口>`
- 默认端口通常为 `5666`
- 支持勾选"记住密码"实现自动登录

---

## 🙏 致谢

- [**fntv-electron**](https://github.com/QiaoKes/fntv-electron) — 本项目中的飞牛影视 API 接口逻辑参考并解析自该开源项目
- [**Danmu API**](https://github.com/huangxd-/danmu_api) — 弹幕数据服务
- **ExoPlayer** — 高性能 Android 播放器

---

## ⚠️ 声明

本项目为**第三方客户端**，与飞牛影视官方无关。使用前请确保遵守相关服务条款。

---

## ❓ 常见问题

### Q：弹幕无法加载？
1. 确保已在"设置"中正确配置弹幕服务器地址（默认 `http://<NAS>:9321`）
2. 检查弹幕服务是否正常运行
3. 可通过弹幕面板的"弹幕搜索"手动选择剧集

### Q：自动匹配弹幕不准确？
- 自动匹配基于文件名，可能会匹配错误的弹幕数据
- 匹配失败时可使用搜索面板手动选择，或重新播放再次匹配

### Q：遥控器无法操作？
- 控制栏显示时，方向键在按钮间移动焦点
- 按 `↑` 移到弹幕/锁定按钮，再按一次隐藏控制栏
- 按 `←` 返回移除焦点，再按一次退出播放（需确认）

### Q：播放卡顿？
- 尝试在设置中切换"解码模式"为硬解或软解
- 检查网络连接质量

### Q：如何更新？
- 设置页 → 检查更新，支持 CDN 加速下载安装
- 也可手动下载 APK 安装

---

## 📄 License

[GNU General Public License v3.0](LICENSE)

本项目基于 GPLv3 协议开源。
