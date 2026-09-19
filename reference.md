# 鸿蒙上架 · 命令与表单

路径按本机默认。换机器时先确认 DevEco。

```text
DevEco: E:\APP\DevEco Studio
hvigor: E:\APP\DevEco Studio\tools\hvigor\bin\hvigorw.js
node:   E:\APP\DevEco Studio\tools\node\node.exe
java:   E:\APP\DevEco Studio\jbr\bin\java.exe
sign:   E:\APP\DevEco Studio\sdk\default\openharmony\toolchains\lib\hap-sign-tool.jar
SDK:    E:\APP\DevEco Studio\sdk
```

## 网页 → rawfile

`vite.harmony.config.ts`：`formats: ["iife"]`，`fileName: () => "app.js"`，`assetFileNames: "app.css"`，`inlineDynamicImports: true`。

`copy-harmony-web.mjs`：清空 rawfile → 拷 `dist-harmony` 和 `public` → 写：

```html
<link rel="stylesheet" href="./app.css"/>
<script src="./insets.js"></script>
<script src="./app.js"></script>
```

不要 `type="module"`。

ArkTS：`setWindowLayoutFullScreen(true)`；读 `TYPE_SYSTEM` 与 `TYPE_NAVIGATION_INDICATOR`；把 `--sat`/`--sab` 写入 insets.js 和 URL。根 `build()` 包一层 `Column`。用 `getUIContext().getHostContext()`。

嵌入壳判断：Capacitor 原生，或 href 含 `rawfile` / `resource://` / `file:` → HashRouter。`?studio=1` 除外。

```bash
# 产品根
npm run harmony:web
# 或：vite build --config vite.harmony.config.ts && node scripts/copy-harmony-web.mjs

# 打未签名 APP（在 harmony/）
set DEVECO_SDK_HOME=E:\APP\DevEco Studio\sdk
hvigorw --mode project -p product=default -p buildMode=release assembleApp
# 产物：harmony/build/outputs/default/*-unsigned.app
```

## 密钥与签名

密钥目录（不进 git）：`<产品>/.sdks/harmony-sign/`

```bash
keytool -genkeypair -alias xiangchi -keyalg EC -groupname secp256r1 -sigalg SHA256withECDSA -validity 3650 -dname "CN=Owner,OU=App,O=App,L=Beijing,ST=Beijing,C=CN" -keystore xiangchi.p12 -storetype PKCS12 -storepass PASS -keypass PASS -noprompt

keytool -certreq -alias xiangchi -file xiangchi.csr -keystore xiangchi.p12 -storetype PKCS12 -storepass PASS -keypass PASS
```

主人上传 CSR 后，把下载文件改名为 `xiangchi.cer`、`xiangchi.p7b`。

```bash
java -jar hap-sign-tool.jar verify-profile -inFile xiangchi.p7b -outFile profile-verify.json
# 必须 type=release，bundle-name=工程包名

java -jar hap-sign-tool.jar sign-app -mode localSign -keyAlias xiangchi -keyPwd PASS -appCertFile xiangchi.cer -profileFile xiangchi.p7b -inFile unsigned.app -inForm zip -signAlg SHA256withECDSA -keystoreFile xiangchi.p12 -keystorePwd PASS -outFile app-signed.app -signCode 1

java -jar hap-sign-tool.jar verify-app -inFile signed.hap -outCertChain out-cert.cer -outProfile out-profile.p7b
```

`.app` 用 `-inForm zip`。`.hap` 加 `-compatibleVersion 12`（按工程 minAPI）。

`build-profile.json5` 的 `signingConfigs` 可空，用工具另签即可，避免明文密码进仓库。

## 截图

视口 `360×640` @ 3x = 1080×1920。先注入 localStorage，再带 cache-bust 打开页。欢迎页与已开户页不要用同一 URL 不刷新。

## 隐私表（单机、本机存储）

产品简介：一句话功能，不要写没有的能力。

摘要：做什么、哪些字段仅本机、不上传、不广告、分享走系统面板、重置即删。

收集使用：列出称呼 / 选填手机 / 本机业务记录。演示支付不读银行卡。

个性化推荐 / 广告营销 / 三方共享 / 三方 SDK：不涉及。

权限：单机则「不申请网络及敏感权限」。

未成年人：不面向 14 岁以下；监护人指导。

管理：无云账号；重置/卸载即删除。

存储：仅本机；期限到卸载或重置。国家格可写「中国境内。仅本机存储，不上传。」跨境：不涉及。

联系我们：齐赛军式「开发者真名 + 包名 + 真电话 + 真邮箱」。假号会打回。

个人信息收集：是。只加开户称呼、选填手机、本机业务记录、用户自填内容。上传服务器=否。

## 错误码

| 码 | 含义 | 处理 |
|---|---|---|
| 991 | 包非法 | 用 DevEco/hvigor 打的 APP，不要改后缀 |
| 992 | 包内名称不一致 / 对不上 Profile | 未签名、包名与 Profile 不一致、传错应用。只传 signed.app |
| 993 | Profile 非法 | 重新下当前应用的**发布** Profile |
| 996 | 解析失败 | 多半未签名或文件损坏 |
| 997 | 包名与 AGC 应用不一致 | 对齐 bundleName |
| 999 | Profile 是调试 | 换发布 Profile |
| 1001 | 证书与 Profile 不是一套 | 重新绑同一张发布证书 |
| 完整性校验不通过 | 未签名或被改 | 重签后再传 |

AGC 列表点错误码看详情。官方：https://developer.huawei.com/consumer/cn/doc/app/agc-help-package-errorcode-0000002312513009

## DevEco 编译坑

- `onPageBegin` / `onConsole` 类型不对：先删回调
- `getContext` 弃用：`getUIContext().getHostContext()`
- 模拟器 008010055：空闲内存 <4GB，改真机
- Waiting for emulator：先等桌面再 Run
- 打开了预览台：DevEco 打开了产品根而不是 `harmony/`

介绍文档：https://developer.huawei.com/consumer/cn/doc/app/50104-01
发布准备：https://developer.huawei.com/consumer/cn/doc/doccenter-submission/agc-help-release-app-prepare-0000002306311921
