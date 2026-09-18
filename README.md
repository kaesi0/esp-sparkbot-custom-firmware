# ESP-SparkBot 自定义固件编译仓

在 GitHub Actions 上把 [78/xiaozhi-esp32](https://github.com/78/xiaozhi-esp32) 的
**v2.5.0** 标签 + [`esp-sparkbot-custom-tools.patch`](./esp-sparkbot-custom-tools.patch)
编译成可直接烧录的 `merged-binary.bin`。

## 产物

每次推送 `main`（或手动 Run workflow）后：

- **Artifacts**：`xiaozhi-esp-sparkbot-custom-<sha>`（需登录下载）
- **Releases**：`fw-<run_number>` 附件里的 `merged-binary.bin`（免登录下载）

## 烧录

用鱼易平台烧录 `merged-binary.bin`，起始地址 **0x0**。

> 合并固件已含 assets 分区，但 **NVS 会被清零**，烧录后需重新配网。

## 补丁内容

| 项 | 说明 |
|---|---|
| 全屏绘图层 | `self.screen.show_image` / `self.screen.hide_image`，在 240×240 上全屏显示 |
| 实时取景 | `self.camera.start_viewfinder` / `self.camera.stop_viewfinder`，摄像头画面全屏（8–12fps） |
| 面板按键 | 启用 GPIO1 上的 4 键 ADC 电阻分压键盘（官方固件此前完全未使用） |
| 图片解码 | LVGL(LODEPNG) 优先，失败回退 `jpeg_to_image`，兼容生图服务返回的 JPEG |
| 版本号 | `PROJECT_VER` 抬到 `9.9.9`，避免官方 OTA 覆盖自定义固件 |
| 原有工具 | 7 个底盘工具 + 摄像头翻转 + 全部全局工具，零删改 |

## 改基线版本

改 `.github/workflows/build.yml` 里的 `UPSTREAM_REF` 即可（补丁目前只保证对 v2.5.0 零偏移）。
