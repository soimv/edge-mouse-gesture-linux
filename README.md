# Edge Gesture Helper

[![Version](https://img.shields.io/badge/version-1.0.0-blue.svg)]()
[![License](https://img.shields.io/badge/license-MIT-green.svg)]()

> Enable Edge's built-in mouse gestures on **Linux** by intercepting the right-click (context menu) event.
>
> 在 **Linux** 上让 Edge 原生的鼠标手势正常工作 —— 通过拦截右键菜单事件，解决手势被菜单遮挡的问题。

| Platform | Status |
|----------|--------|
| Linux (Wayland) | ✅ Works |
| Linux (X11)     | ⚠️ Untested, likely works |
| Windows         | ❌ Not needed — Edge gestures work natively |
| macOS           | ❌ Not needed — Native support added in newer Edge versions |

---

## Overview / 概述

Edge 浏览器在 Linux 下内置了鼠标手势功能（按住右键滑动），但按下右键时系统上下文菜单（右键菜单）会同时弹出并遮挡页面，导致手势无法正常使用。

This extension suppresses the first right-click (so Edge's gesture engine can take over), and only shows the context menu when you **double-right-click** within 400 ms.

本扩展的工作方式是：
- **单击右键** → 拦截右键菜单 → Edge 手势引擎正常响应
- **双击右键**（400ms 内） → 放行事件 → 显示上下文菜单

---

## Features / 特性

- ✅ Enables Edge mouse gestures on Linux / 在 Linux 上启用 Edge 鼠标手势
- ✅ Double-right-click to access the native context menu / 双击右键依然可以呼出原生菜单
- ✅ On/Off toggle via popup / 弹出面板一键开关
- ✅ Lightweight, zero dependencies / 轻量零依赖
- ✅ Works in all frames (iframes, about:blank) / 支持所有 frame

---

## How It Works / 原理

`content.js` 监听全局 `contextmenu` 事件（捕获阶段）：

1. 记录每次右键的时间戳和坐标
2. 如果两次点击间隔 **< 400ms** 且距离 **< 10px** → 放行，显示菜单
3. 否则 → `preventDefault()` 拦截事件，手势引擎接管

通过 `chrome.storage.local` 同步开关状态，popup 页面可实时切换。

---

## Installation / 安装

Since this extension is **not published to the Edge Add-ons store**, you need to load it manually in developer mode.

由于未上架 Edge 扩展商店，需要手动加载未打包的扩展。

### Steps / 步骤

1. Open **Edge** and go to `edge://extensions`
2. Enable **Developer mode** (toggle in bottom-left corner)
3. Click **Load unpacked** → select the `edge-gesture-helper` folder
4. The extension icon should appear in the toolbar

---

1. 打开 Edge 浏览器，访问 `edge://extensions`
2. 开启左下角的 **开发者模式**
3. 点击 **加载解压缩的扩展** → 选择 `edge-gesture-helper` 文件夹
4. 扩展图标即出现在工具栏

> **Tip:** Pin the extension to the toolbar for quick access to the toggle.
>
> **提示:** 将扩展固定到工具栏，方便随时开关。

---

## Usage / 使用

- **Single right-click** → hold and drag → Edge gesture works
- **Double right-click** (quickly, within ~400ms) → context menu appears

| Action | Result |
|--------|--------|
| 单击右键拖拽 | 鼠标手势生效 |
| 快速双击右键 | 弹出上下文菜单 |

Disable the extension from the popup if you need the context menu for normal browsing.

如果暂时不需要手势功能，可通过弹窗关闭扩展。

---

## Why Only Linux / 为什么仅限 Linux

Edge 基于 Chromium，在 Linux 上使用 **GTK** 绘制右键菜单，菜单弹出时机早于 Chromium 的手势引擎，导致手势被阻挡。而在 Windows 和新版 macOS 上，Edge 使用原生菜单实现，手势优先于菜单，没有这个问题。

Edge is built on Chromium. On Linux it renders the context menu via **GTK**, which fires before Chromium's gesture engine can intercept it. On Windows and newer macOS builds, Edge uses its own menu implementation where gestures take priority — no issue there.

---

## Build / 构建

No build step required. This is a plain Manifest V3 extension with no dependencies.

无需构建。这是一个纯 Manifest V3 扩展，零依赖，直接加载即可使用。

```
edge-gesture-helper/
├── manifest.json      # Extension manifest
├── content.js         # Core: context menu interceptor
├── popup.html         # Popup UI
├── popup.js           # Popup logic
└── icons/
    └── icon.svg       # Extension icon
```

---

## License / 许可

[MIT](LICENSE)
