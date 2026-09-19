---
name: harmony-app-publish
description: 按国内华为 HarmonyOS NEXT 上架路径，从网页壳、包名对齐、发布签名、商店文案、9:16 截图到 AGC 提审。Use when 鸿蒙上架、华为应用市场、AppGallery Connect、AGC、HAP、APP、软著、992、993、单机应用、发布证书、Profile、想吃先停式套壳。
---

# 鸿蒙应用上架

主人是不写代码的小白。大白话。技术你定。新应用先写目标框，等他说「开始」再写业务；上架机械步骤直接做。

```text
给谁用：
要解决啥：
做成啥：能在鸿蒙手机打开的 App
这版包含：一条能点完的主路径
这版不做：真支付 / 云同步 / Google Play / 社交群控
怎么算做好：主路径能点完；商店材料齐
```

一次只动一个产品目录。不要提审已上架的微信小程序，除非他亲口说改小程序。

## 默认技术

能做成网页的，做成网页，再用鸿蒙 WebView 壳。不要一上来纯 ArkTS 重写整套 UI。

- 网页：`base: "./"`。鸿蒙包打成 **一条 IIFE**（`app.js` + `app.css`），ArkWeb 吃不下 `type=module`，会白屏「正在打开…」。
- 启动：rawfile 拷到 `filesDir`，用 `file://` 打开，写入 `--sat` / `--sab`。
- DevEco **只打开** `harmony/`，不要打开整个产品根。
- 包名：先在 AGC 建 **HarmonyOS 应用**（不要建成安卓应用），拿到包名再写入 `AppScope/app.json5`。建议 `com.主人拼音.产品拼音.hmos`，避免和安卓包名撞。
- 单机产品：不要申请 `ohos.permission.INTERNET`。选「单机 APP」时包里绝不能带网络权限。
- 不接真支付。演示支付必须在介绍和审核备注里写清。

完整命令、表单粘贴、错误码见 [reference.md](reference.md)。想吃先停对照见 [examples.md](examples.md)。

## 作业单（按序勾）

```
- [ ] 1 主路径在浏览器点通
- [ ] 2 AGC 已建 HarmonyOS 应用，包名写入 app.json5
- [ ] 3 IIFE 网页拷进 rawfile，DevEco Run 真机（模拟器可选，空闲内存要 ≥4GB）
- [ ] 4 软著已递（没证：可填资料，不能点最终提交）
- [ ] 5 隐私正文与软件一致；网上能打开的隐私网址（提审才必须）
- [ ] 6 图标 1024 PNG；介绍截图 3–10 张，9:16，1080×1920，PNG≤5MB
- [ ] 7 介绍 / 权限 / 个人信息表与软件一致
- [ ] 8 发布证书 + 发布 Profile（禁止用自动签名）
- [ ] 9 只上传 *-signed.app
- [ ] 10 单机就选单机；联网才填工信部备案
```

## 1. 网页打进鸿蒙

1. Chrome 里先把主路径点通。
2. `vite.harmony.config.ts`：IIFE，`outDir: dist-harmony`，`assetsInlineLimit` 加大。
3. 脚本拷到 `harmony/entry/src/main/resources/rawfile`，写不带 module 的 `index.html`。
4. `Index.ets`：`getUIContext().getHostContext()`；根节点必须是容器；先不要 `onPageBegin` / `onConsole`。
5. 改网页后必须先同步 rawfile，再 DevEco Run。浏览器预览不能当上架包。

白屏：是不是又打成了 ES module；rawfile 有没有 `app.js`。

## 2. AGC 建应用

1. https://developer.huawei.com/consumer/cn/service/josp/agc/index.html
2. 项目 → **添加 HarmonyOS 应用**。
3. 记下包名、APP ID。工程 `bundleName` 必须与此完全一致。
4. 分类、标签按产品；电话邮箱必须是主人能接到的。

## 3. 商店材料

你写，他粘。禁止：官方、最佳、真扣款、疗效/收益承诺。演示能力必须写「不产生真实交易」。截图必须是真界面。

**介绍截图**

- 竖屏 9:16，最低 1080×1920，PNG/JPG≤5MB（或 WEBP≤200KB），3–10 张且互不相同。
- 网页加 `?shot=1` 去预览台。
- 无头 Chrome：逻辑像素 `360×640`、`deviceScaleFactor: 3`。
- **先** `evaluateOnNewDocument` 写入演示数据，再打开目标页。同一 URL 不会自动刷新。
- 不要交 1080×2340。

**隐私 / 个人信息表**

- 本机有称呼、手机号、订单就选「涉及个人信息收集」。
- 不勾广告、个性化推荐、统计分析、推送、位置。
- 上传服务器：否。分享不收集通讯录。
- 单机：权限写「不申请网络权限」，不要再写 INTERNET。
- 注销 = 应用内重置 + 卸载。没有云账号。

**备案**

- 单机且包内无网络权限：选单机 APP，备案不涉及。
- 包内有 INTERNET 却选单机：审核必挂。

## 4. 发布签名（上架关键）

自动签名只能调试，传商店会报 992 / 完整性失败。

1. 本地生成 PKCS12 + CSR（ECC / SHA256withECDSA），密钥放产品外的 `.sdks/harmony-sign/`，不进 git。
2. 主人在 AGC：**证书 → 新增 → 发布证书**，上传 CSR，下载 `.cer`。
3. **Profile → 新增 → 发布**，绑当前 HarmonyOS 应用和这张证书，下载 `.p7b`。
4. 用 `hap-sign-tool.jar sign-app` 签 **unsigned.app**，得到 `*-signed.app`。
5. 先 `verify-profile`：`type=release` 且 `bundle-name` 等于工程包名。再上传。

只传 `*-signed.app`。不要传 unsigned.app / .hap。

主人说「证书好了」时：先在桌面、下载、产品目录找 `.cer` / `.p7b`（文件名常被浏览器加成 `.cer.cer`、`.p7bRelease.p7b`），拷到签名目录再打。

## 5. 提审

没软著、没网上隐私网址：资料可先填，不要点最终提交。

审核备注默认：

```text
本应用不提供真实配送，不进行真实收款。涉及支付或进度的界面均为演示，不会产生扣款。
```

单机则补一句：不申请网络权限，数据仅保存在本机。

## 主人必须亲自做

华为实名、软著、AGC 登录、下载 cer/p7b、填真人电话邮箱、把隐私页挂到能打开的网址、点最终提交。其余你做。

## 禁止

- 把网页地址交给华为当 App
- 用调试证书 / 自动签名上传
- 介绍与软件不一致
- 为「单机」撒谎却保留 INTERNET
- 把 keystore 密码、cer/p7b 写进 git 或交接手册正文
