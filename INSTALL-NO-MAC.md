# 无 Mac 安装 IPhoneAgent：GitHub Actions 云构建 + SideStore 侧载

适用场景：没有 Mac，只有 Windows + iPhone。全程不需要 Apple 开发者付费账号。

## 第 1 步：Fork 仓库并加入构建 Workflow

1. 打开 https://github.com/onehumanbeing/esp32-iphone-agent ，点右上角 **Fork**， fork 到自己的账号。
2. 在你的 fork 里新增文件 `.github/workflows/ipa.yml`，内容就用本目录下的同名文件
   （网页操作：进入 fork → Add file → Create new file → 文件名填 `.github/workflows/ipa.yml` → 粘贴内容 → Commit）。
   注意：GitHub 网页编辑器里路径中的 `/` 会自动创建目录。

## 第 2 步：云端构建 IPA

1. 进入你 fork 的 **Actions** 页，首次会提示启用 workflows，点启用。
2. 左侧选 **IPA (unsigned, for sideloading)** → 右侧 **Run workflow** → 确认。
3. 等待约 10-20 分钟（macOS runner 装 XcodeGen + 编译）。
4. 成功后在该次运行页面底部的 **Artifacts** 下载 `IPhoneAgent-unsigned-ipa`，解压得到 `IPhoneAgent-unsigned.ipa`。

## 第 3 步：Windows 上安装 SideStore

1. 先装 Apple 官网版本的 iTunes 和 iCloud（**不能用 Microsoft Store 版**）：
   - iTunes: https://www.apple.com/itunes/download/win64
   - iCloud: https://updates.cdn-apple.com/2020/windows/001-39935-20200911-1A70AA56-F448-11EA-8CC0-99D41950005E/iCloudSetup.exe
2. 下载 SideStore Windows 安装器：https://sidestore.io/ （SideStoreSetup.exe），安装。
3. iPhone 用数据线连电脑，信任电脑；运行 SideStore 安装器把 SideStore 装进 iPhone
   （期间登录你的 Apple ID，仅用于本地签名）。
4. 按 SideStore 提示在 iPhone 上启用其内置 VPN（StosVPN，本地签名用，流量不出本机）。

## 第 4 步：侧载 IPhoneAgent

1. 把 `IPhoneAgent-unsigned.ipa` 传到 iPhone（邮件/QQ/文件共享均可，存到「文件」App）。
2. 打开 iPhone 上的 SideStore → My Apps → 左上角 `+` → 选这个 IPA，登录同一 Apple ID 签名安装。
3. 安装后到 设置 → 通用 → VPN与设备管理 里信任该证书（如提示）。

## 第 5 步：开始使用

1. ESP32 板子通电，用板子的**原生 USB 口**（不是烧录用的 CH343 口）插 iPhone。
2. 打开 IPhoneAgent，按提示连板子的 BLE（ESP32 Agent XXXX）和热点 `ESP32Agent-DC11`
   （免费账号没有 Hotspot Configuration 能力，App 会显示热点密码，手动到设置里连一次即可）。
3. 控制中心 → 屏幕镜像 → 选 **ESP32 Agent**，板子开始收屏。
4. 在 App 里配置你自己的大模型 API（Base URL / Model / Key），即可让 Agent 接管操作。

## 已知限制

- 免费 Apple ID 签名 **7 天过期**：SideStore 会在同 Wi-Fi 下自动重签，尽量不要让 SideStore 超过 7 天不开。
- 云端构建的 IPA 未签名属正常，签名发生在侧载时；不要改 bundle id，否则 widget 的 App Group 会失效。
- Artifact 只保留 7 天，下载后自己存好 IPA 文件。
