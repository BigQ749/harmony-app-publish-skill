# 对照：想吃先停

不要抄业务文案。只抄结构。

| 项 | 值 |
|---|---|
| 产品根 | `D:\APP\changui` |
| 小程序（只读，不提审） | `D:\APP\changui-mp` |
| 鸿蒙工程 | `D:\APP\changui\harmony` |
| 包名 | `com.xiangchi.xianting.hmos` |
| APP ID | `6917616721623998959` |
| 显示名 | 想吃先停 |
| 类型 | 单机；已去掉 INTERNET |
| 网页端口 | 5188；`?shot=1` 打截图 |
| 数据键 | `changui-v5` |
| 主路径店 | `s-mixue` / 冰城柠檬水 / `mx1` |
| 图标 | `store/华为图标-1024.png`（主人照片，不要再生成卡通脸） |
| 截图 | `store/华为截图/01`–`10`，1080×1920 |
| 介绍 | `store/上架材料.md` |
| 隐私填写 | `store/华为隐私声明填写.md` |
| 签名材料 | `.sdks/harmony-sign/` |
| 上传物 | 桌面 `想吃先停-1.0.0-signed.app` |

## 这款踩过的坑

1. 包名先写成 `com.xiangchi.xianting`，AGC 已是 `.hmos`，必须改工程。
2. rawfile ES module → 白屏「正在打开想吃先停」。改 IIFE + `file://`。
3. 未签名 `.app` 上传 → 解析失败 / 完整性失败 / **992**。
4. 截图先打成 1080×2340，后台要求 9:16 / 1080×1920。
5. 截图脚本先 goto 再写 localStorage，React 把空状态写回去，10 张都停在开户页。
6. 主人说「证书好了」时文件名是 `xiangchi.cer.cer`、`xiangchi.p7bRelease.p7b`。
7. 有 INTERNET 不能选单机。已删权限并重签。
8. 模拟器内存不够不是必过关。真机优先。

## 下一款怎么套

1. 先框产品，抄 IIFE 壳和 `Index.ets`，不抄店名和克制文案。
2. AGC 新建 HarmonyOS 应用，包名带 `.hmos`。
3. 能离线就单机，不要申请网络。
4. 按 SKILL 作业单走完签名再传 `*-signed.app`。
