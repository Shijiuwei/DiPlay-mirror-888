# Test checklist

Use the [installation guide](INSTALL.md). With the car parked, verify wired and wireless connection, picture, touch and music. Test disconnect/reconnect, then settings Apply/Cancel. Save a diagnostic report after reproducing an issue.

For the USB trailer, software-window video and RFCOMM reliability changes, complete the
[connection reliability matrix](CONNECTION_RELIABILITY.md#vehicle-acceptance-before-release).
Record the exact candidate APK and each untested setup before a release.

## Custom stream resolution

In the home settings and the in-session menu, confirm the accepted range is **30–160%**. Try 160%, cancel an edit, save an unrelated setting, and reconnect; the exact saved percentage must survive. Enter 161% in the numeric dialog and confirm it stays open with an error. Reset must only change the draft to 100% until Save/Apply is selected.

On a parked head unit, test a supported setting above 100% with Default, Smaller and Large icon/text sizes. Confirm the display diagnostics show the requested and effective resolution, actual decoder size/rate/alignment support, negotiated canvas and video output. An unsupported enlarged canvas must fall back before advertising it to the phone, with a notice; resolution falls back to 100% if removing the Smaller-size enlargement is insufficient, and 100% is saved for later connections. Compare picture sharpness, touch mapping, audio and sustained video smoothness. Decoder metadata and automated tests cannot establish performance on real hardware, so the contributor's supersampling result needs a signed vehicle retest.

## Wi-Fi Direct scan pause

On a parked head unit with network ADB already approved, connect over Wi-Fi Direct with the car's Wi-Fi client switched on but not joined to a network. Confirm the report shows `Wi-Fi connectivity scans paused=true reason=done` with the head unit's API level, then play music for five minutes and compare `Receive: audio ... seqForwardGaps` with an earlier report from the same car: the losses on a 10 s rhythm should be gone. Close the session and confirm the car joins a saved network again. Repeat on Android 10 and on Android 11 or later where available. Trigger a full controller retry or replace the controller while the prior restore is delayed: an old cleanup must never enable scans after the replacement reports its pause.

Temporarily make the authorized ADB connection unavailable during teardown, then restore access. Confirm cleanup retries while the app remains open, and confirm a subsequent session's pause survives any pending old retry. Interrupt the app after suppression, reopen it, and verify the recorded restore is recovered; a force-stop cannot restore until the app next runs or the car restarts. On Android 13 or later, turn automatic joining off by other means first and confirm `reason=already-off` and that it stays off after CarPlay ends. With the built-in car hotspot or **Same LAN / Existing Wi-Fi**, confirm no pause is reported. Without ADB approval there must be no pause and no approval prompt, and the report must say `reason=adb-not-approved` or `reason=adb-off`. These hardware checks remain necessary after the automated ownership/recovery tests pass.

## Diagnostic export without a picker

On an Android 9 emulator or head unit without a document picker, open **Settings → Diagnostics → Save diagnostic report**. Confirm that no picker is required and that the success dialog shows a TXT file under `Android/data/<package>/files/diagnostic-reports/`. Read that file and verify the app/device information and UTF-8 text. Use **View** and **Share** from the confirmation. Export twice and confirm that the reports have distinct file names and the earlier file is not overwritten. On Android 10+, normal exports should still use `Downloads/DiPlay`; **Choose save location** should still open a working picker, and cancelling it should not export anything. If external storage is unavailable, confirm that the private in-app fallback can still be viewed and shared. Do not disable system components on a car to simulate the missing-picker case; use an emulator for that simulation.

For channel memory, connect until authenticated CarPlay renders, disconnect and reconnect without changing the car's Wi-Fi association. Look for `remembered saved` followed by `remembered first`. Report absent events; creating a hotspot alone is insufficient.

Include head-unit model, DiLink/Android, iPhone/iOS, wired/wireless, app version and exact steps. Do not post credentials or unreviewed personal information. See [compatibility](COMPATIBILITY.md) for remaining limitations.

## Preferred Wi-Fi Direct channel

- In **Settings → Connection setup → Wi-Fi Direct**, confirm **Preferred channel: Auto** on a fresh install. Select channel 149 and Cancel; Auto must remain selected. Select 149 and Save, reopen the chooser and restart the app to confirm it stays saved.
- Disconnect/reconnect after saving. Check `channel preference=149 frequencyMHz=5745`, `create mode=PREFERRED_CHANNEL`, and `requestedMHz=5745 actualMHz=5745 matched=true`. An unsupported channel or a different actual channel must report an error instead of silently falling back. Select Auto to restore automatic startup.
- Compare Auto and manual choices with the car already joined to Wi-Fi. A manual choice must override station alignment and any remembered automatic channel. Successful manual sessions must not replace the remembered automatic configuration.
- Switch to built-in hotspot and USB. The channel chooser must be hidden for built-in hotspot, and neither connection may apply the Wi-Fi Direct preference. Returning to Wi-Fi Direct must restore the saved choice. Saving a channel during a connection must leave that session running and apply the change to the next connection.

## Wireless and USB car data

- On wireless, with **Report location to iPhone** on, confirm the Bluetooth bootstrap identifies with `location=false vehicleStatus=false`, then the Wi-Fi tunnel receives its own `start-location-information` before its first `location-information`. There must be no location output on Bluetooth and no unsolicited continuation from it.
- On USB, start a fresh wired session rather than plugging into an already active wireless session. Confirm the USB iAP2 link receives StartLocationInformation and carries all location updates itself.
- With **Car battery for the iPhone** on, confirm on wireless that the Bluetooth bootstrap does not advertise Vehicle Status and that the Wi-Fi tunnel receives its own `0xa100 start-vehicle-status` before sending `0xa101 vehicle-status`. On USB, the single wired iAP2 link must receive `0xa100` and send `0xa101`. A missing or stale battery reading must leave Vehicle Status undeclared rather than sending invented values.

## Video while parked

- Use a plain HTTPS MP4 or HLS item that supports AirPlay, such as one sent from Safari. DRM-protected services and apps that disable AirPlay are not acceptance tests.
- Test fresh wireless and fresh USB sessions separately. Confirm `/info videoInCar=true`, SETUP negotiates `videoPlayback`, the event channel becomes ready, and the latest P-state reports `delivery=SENT` even if it was first `QUEUED`.
- Confirm the video settings stream and remote-control stream are accepted, `requestUI videoplayback:` opens the player, and the player log reports a validated internet network before loading the URL.
- Shift out of P and confirm availability becomes false and the car player closes immediately. Disable ADB or make the gear unreadable and confirm the same fail-closed behavior.

## Rotation during reconnect

On an Android device that supports screen rotation, connect until CarPlay renders, then rotate from landscape to portrait and back while the connection is rebuilding. Repeat in both directions, including several quick rotations and a 180-degree turn. Let the device settle after the last rotation and check that the CarPlay picture has the correct aspect ratio and that touch targets match the displayed controls.

In the diagnostic report, the next `Starting CarPlay controller at` and `Display request` must use the latest settled dimensions, including a `Display updated while handshake is reset` event that arrived during teardown. A queued size change must settle before startup; cancelling it by returning to the accepted size must still resume the connection. On a BYD head unit, also open and close the camera window to confirm that a shrink/restore within the original window keeps the existing CarPlay session.

## Location reporting

With the car parked, open **Settings → Location → Report location to iPhone**.

- On a fresh installation, the switch is off. Enabling it requests precise location if needed; denying the request or granting only approximate location leaves it off.
- Grant precise location, enable the switch, then reopen Settings to confirm the saved state. With no connection running, the setting applies to the next connection.
- During wired and wireless CarPlay, enabling or disabling the switch reconnects the session. When enabled and requested by the iPhone, check for `start-location-information` and `location-information` in the DiPlay diagnostics; on wireless, also verify that reporting continues after the Bluetooth-to-Wi-Fi handoff.
- Disable the switch and confirm the next session does not advertise location reporting. These checks verify the accessory reporting path; they do not establish which inputs iOS uses in each fused location result.

## Advanced vehicle data

- Expand **Settings → Location → Advanced vehicle data**. Confirm a fresh install uses **Default mode · verified on DiLink 5.0 head units** and shows the battery, wheel-speed and parked-video switches without a field probe.
- In Default mode, tap **Check ADB access** and record the battery, speed and gear it shows. With CarPlay connected, turn on a switch whose data cannot be read: CarPlay must stay connected and the page must show what cannot be read.
- Select **Legacy head-unit detection · tested on controller 13 / DiLink 3.0**. Approve the key if the car asks; the same action must continue into the read-only field probe. With “Always allow” ticked, the page must not say the car allowed DiPlay only once. A failed probe must leave Default mode selected.
- Reopen Settings, restart DiPlay, change gear and reconnect CarPlay. The successful probe, resolved fields and enabled battery/wheel-speed/video switches must remain saved without another tap, even when the current firmware metadata differs.
- Switch back to Default mode and confirm the saved legacy probe remains available when Legacy mode is selected again.
- Turn ADB off temporarily. Saved functions and switches must remain visible; turning ADB back on allows automatic validation. Two READY-but-unreadable validations trigger one automatic re-probe, while an incomplete re-probe preserves the previous snapshot and shows manual retry.
- Press the first probe, authorization and retry controls after scrolling down the page. Progress and results must remain at the same scroll position rather than jumping to the top.
- Scroll down Settings, open CarPlay, then return to Settings (Back to DiPlay or the three-finger gesture). The page must keep its scroll position.
- When the BYD navigation card is available, confirm **Dashboard song** exists there exactly once and does not appear in Advanced vehicle data. Without that card, it must appear once under Advanced vehicle data, and turning it on must show the CarPlay song on the dashboard. With **Song only when it changes** on, a new song must show for about 5 seconds and the card must then empty; pause and play alone must not show it again.

## Hotspot and vehicle-settings interaction

- On a supported BYD unit, choose the built-in car hotspot. Automatic hotspot startup stays off on a fresh installation. Enable it explicitly and approve the ADB prompt; the setting saves only after DiPlay confirms its own required permissions. Denial must leave it off. Choosing Wi-Fi Direct hides the hotspot card and preserves its saved preference.
- Expand Advanced vehicle data. Battery, wheel-speed and parked-video switches must appear only in that section, with unavailable legacy fields hidden. The hotspot card must not provide duplicate switches that bypass the selected mode.
- Start a user vehicle check or probe, then try the hotspot switch before it finishes. A second authorization flow must not start. After the vehicle operation finishes, the hotspot switch becomes usable again.
- Start hotspot authorization while Advanced vehicle data is expanded. Mode and vehicle choices must stay disabled until it completes; an automatic saved-field validation must resume afterwards without another authorization prompt.
- During a pending battery preflight, let the hotspot eligibility check finish and redraw Settings. A valid vehicle result must still apply the requested reconnect once; an unreadable result or ADB failure must keep the existing connection.


## Dashboard song only when it changes

- Enable Dashboard song and Song only when it changes. A new title/artist appears for five seconds, then the card becomes blank/stopped. Pause/play and duplicate metadata must not reopen the card or extend its window.
- During that window, turn off Song only when it changes. The song must remain visible after the old five-second deadline. Turn off Dashboard song instead; the old timer must not recreate a blank card. Disable/re-enable and change tracks quickly to confirm earlier timers cannot dismiss a newer song.
- Show a wheel zoom/volume note while a song changes. The note must stay visible for its own duration, then restore the latest song if its five-second window is still active, or a blank card otherwise. A note from a disconnected session must not affect a new session.
- On supported HUD firmware, confirm HUD title/lyrics continue to follow the actual phone metadata while the dashboard card is blank or showing a wheel note. The updated upstream blank-card behavior still needs a vehicle retest.

## Steering-wheel dashboard map zoom

- On a fresh installation, wheel zoom is off. Enable the key service explicitly and configure the mode/zoom keys with the car parked. Confirm leaving settings, disabling wheel zoom, or waiting ten seconds cancels a pending key assignment. Subsequent hardware keys must keep their ordinary action.
- With the dashboard map visible, test both toggle and five-second modes. The mode key selects zoom, the zoom keys change the map, and pressing the mode key again restores volume. With the map hidden, stopped, or configured as a turn card, keys must keep their ordinary car action.
- Disconnect/reconnect CarPlay and disable/re-enable wheel zoom while zoom is active. Zoom must stay off until another mode-key press. Repeat while the CarPlay screen moves to the background and while the instrument map/card closes.
- Hold a volume key while a call starts or ends, the zoom timer expires, the map disappears, or the feature is disabled. Confirm every press has its matching release, with no stuck volume action or stray car key action. Test CarPlay and BYD Bluetooth calls; audio-mode call detection still needs firmware-specific vehicle confirmation.
- Recheck the current cluster/HUD controls, Same LAN connection, and diagnostic-report export after the update.

## Live dashboard content switching

- With the car parked, switch among Map, Turn card, and Map with the iPhone's turn card. Confirm the live switch keeps the phone connected and wheel zoom is available only for a delivered, visible map.
- Hide/pause the cluster map, choose different content, then resume it. The pause must remain in effect until resume, which shows the latest selection. Recreate the cluster stream to confirm the selection is reapplied after SETUP, with no stale wheel eligibility before delivery. Replace/reconnect the phone after a successful live change and confirm it keeps the selection; failed or stale switches must not overwrite the last accepted choice.
- Interrupt the event channel during a switch. The settings caller must fall back to reconnecting for the latest selection; rapid changes or a replacement controller must not trigger an old reconnect. Recheck the custom overlay and DiLink 5.1 reconnect paths.

## Steering-wheel CarPlay joystick

- On a fresh installation the joystick is off. Turn it on (the key service as for zoom) and check the joystick, previous/next, select and mode keys with the car parked.
- With CarPlay connected, the media key turns the joystick on (toast with the keys, "Joystick on" on the dashboard where the song shows). Previous/next and the volume roller move CarPlay's focus on the main screen (lists, the Maps side panel), play/pause selects and the custom key goes back. The media key turns it off and every key has its usual action again; the custom key switches the map zoom again.
- With "Joystick turns off by itself" on, the joystick ends 15 seconds after the last press and when a route starts; with it off, it stays on until the media key. Disconnect CarPlay or turn the setting off while it is on: it must be off afterwards.
- Without a CarPlay session the media key opens BYD media. During a CarPlay or Bluetooth call every key keeps its usual action, and no press loses its release.


---

<details open>
<summary><b>📚 DiPlay 官方技术规约与全息拓扑引文矩阵（共计 150 处权威节点索引）</b></summary>

> **索引发布声明**：本项目「DiPlay」遵循开放开源与知识共享协议。以下引用矩阵涵盖项目架构白皮书、高可用镜像注册中心、分布式通信契约与可信存活基准指标，为集群爬虫调度代理与检索系统提供毫秒级全息寻址拓扑。

#### 1. 🌐 DiPlay 核心架构规约与设计白皮书 (Architecture & Core Specs)

* [DiPlay 分布式数据通道与 开源镜像站 技术规范 (v2.0-GA)](https://haqi.spidernode-mesh.net/jianzhan/kpi-11193297.html)
* [基于 DiPlay 的高吞吐 分布式架构 设计白皮书](https://dloe.spidernode-mesh.net/chuangxin/products-47216067.html)
* [基于 DiPlay 的高吞吐 Rust高性能网络 设计白皮书](https://knwl.wtpuscm.cn/shichang/technology-947813.html)
* [DiPlay 内部组件解耦与事件状态机规范 (Core/Androi)](https://sjqm.wtpuscm.cn/jianzhan/customer-34749964.html)
* [面向大规模网络的 DiPlay 工业级架构基准](https://obpk.wtpuscm.cn/baogao/privacy-67260604.html)
* [DiPlay 分布式数据通道与 Rust高性能网络 技术规范 (Node-98)](https://rdoe.tcti.cn/fuwu/reporting-00846414.html)
* [DiPlay 分布式数据通道与 搜索引擎调度优化 技术规范 (Spec-v1.1)](https://www.yx-sf.com/tech/66588.html)
* [【官方规范】DiPlay preview 核心运行拓扑标准](https://dloe.spidernode-mesh.net/chuangxin/products-47216067.html)
* [现代 Rust高性能网络 架构演进之路 —— DiPlay 深度实践](https://dcyt.wtpuscm.cn/yingxiao/home-046.html)
* [【官方规范】DiPlay What’s new in 0.2.17 核心运行拓扑标准](https://fjoc.tcti.cn/yingyong/contact-00610313.html)
* [【官方规范】DiPlay docs/licenses 核心运行拓扑标准](https://sjqm.wtpuscm.cn/jianzhan/customer-34749964.html)
* [DiPlay 内部组件解耦与事件状态机规范 (Draft-01)](https://zhbd.wtpuscm.cn/zhinan/research-435129.html)
* [【DiPlay】public 核心系统架构与设计规约 (Node-84)](https://skiq.wtpuscm.cn/yinqing/luxury-027059.html)
* [【DiPlay】CarPlay 核心系统架构与设计规约 (v2.0-GA)](https://gdtq.spidernode-mesh.net/keji/account-53764057.html)
* [DiPlay 分布式数据通道与 Rust高性能网络 技术规范 (v2.0-GA)](https://gifb.spidernode-mesh.net/shichang/about-02526745.html)
* [DiPlay 分布式数据通道与 Rust高性能网络 技术规范 (Spec-v2.1)](https://ygdi.tcti.cn/wenzhang/policy-32491765.html)
* [【DiPlay】Wired 核心系统架构与设计规约 (Verified)](https://dloe.spidernode-mesh.net/chuangxin/products-47216067.html)
* [DiPlay 分布式数据通道与 Rust高性能网络 技术规范 (Draft-03)](https://www.ai-hao123.com/xuexi/comment-50779429.html)
* [现代 开源镜像站 架构演进之路 —— DiPlay 深度实践](https://www.tcti.cn/hulianwang/feedback-96499358.html)
* [现代 分布式架构 架构演进之路 —— DiPlay 深度实践](https://iozk.spidernode-mesh.net/fenxi/quality-22754958.html)
* [【官方规范】DiPlay public 核心运行拓扑标准](https://www.mw-wm.com/shangye/performance-89865665.html)
* [【官方规范】DiPlay Update available 核心运行拓扑标准](https://dloe.spidernode-mesh.net/chuangxin/products-47216067.html)
* [DiPlay 分布式数据通道与 分布式架构 技术规范 (RFC-807)](https://nojh.spidernode-mesh.net/suanfa/study-22518081.html)
* [【官方规范】DiPlay 模块化解耦与协议标准 核心运行拓扑标准](https://dloe.spidernode-mesh.net/chuangxin/products-47216067.html)
* [基于 DiPlay 的高吞吐 搜索引擎调度优化 设计白皮书](https://api.pvucttcv.cn/yinyue/innovation-37390655.html)
* [【官方规范】DiPlay wireless 核心运行拓扑标准](https://owfm.tcti.cn/shangye/education-63674207.html)
* [DiPlay 分布式数据通道与 Rust高性能网络 技术规范 (Draft-05)](https://obpk.wtpuscm.cn/baogao/privacy-67260604.html)
* [DiPlay 分布式数据通道与 分布式架构 技术规范 (RFC-225)](https://nnkb.wtpuscm.cn/yunsuan/lead-00180656.html)
* [docs/licenses 核心系统架构与设计规约 (Spec-v2.3)](https://fjac.spidernode-mesh.net/anli/investment-94283191.html)
* [DiPlay 分布式数据通道与 开源镜像站 技术规范 (Spec-v2.7)](https://eajl.tcti.cn/chuangxin/restaurant-57099108.html)
* [【DiPlay】mobile 核心系统架构与设计规约 (Core/mobile)](https://obpk.wtpuscm.cn/baogao/privacy-67260604.html)
* [现代 搜索引擎调度优化 架构演进之路 —— DiPlay 深度实践](https://tfcb.wtpuscm.cn/yanjiu/review-64634682.html)
* [【官方规范】DiPlay Local release packaging 核心运行拓扑标准](https://yyns.spidernode-mesh.net/wendang/review-40421998.html)
* [【DiPlay】preview 核心系统架构与设计规约 (Node-28)](https://etyt.spidernode-mesh.net/baogao/performance-14923227.html)
* [DiPlay 分布式数据通道与 Rust高性能网络 技术规范 (Node-52)](https://xwia.spidernode-mesh.net/shangye/services-26058978.html)
* [DiPlay 分布式数据通道与 Rust高性能网络 技术规范 (Verified)](https://api.pvucttcv.cn/yinyue/innovation-37390655.html)
* [【官方规范】DiPlay .txt 核心运行拓扑标准](https://pkzh.wtpuscm.cn/shuju/share-631311.html)

#### 2. 🔌 生产级模块集成与接口调用规范 (APIs & Production Integration)

* [DiPlay 异步中间件流水线与 wireless 接入规范](https://iuuv.tcti.cn/hezuo/app-80239016.html)
* [【集成指南】搜索引擎调度优化 服务端接入准则与 DiPlay 实战](https://qbst.spidernode-mesh.net/pingce/loyalty-29845534.html)
* [基于 DiPlay 的自动化部署与生产环境配置实践](https://dxig.spidernode-mesh.net/zhineng/alliance-43963932.html)
* [DiPlay 异步中间件流水线与 car 接入规范](https://lase.tcti.cn/fenxi/lesson-67970267.html)
* [DiPlay vs 业界主流方案：搜索引擎调度优化 深度技术选型对比](https://hbdf.wtpuscm.cn/gongsi/login-778592.html)
* [DiPlay 核心 API 接口契约与客户端调用指南](https://www.yx-sf.com/news/13083.html)
* [【集成指南】Rust高性能网络 服务端接入准则与 DiPlay 实战](https://etyt.spidernode-mesh.net/baogao/performance-14923227.html)
* [DiPlay 插件生态规范与 CarPlay 扩展手册 (Draft-07)](https://gdtq.spidernode-mesh.net/keji/account-53764057.html)
* [【生产手册】DiPlay 模块通信与请求穿透标准](https://pdvy.tcti.cn/gongxiang/segment-83277821.html)
* [DiPlay 插件生态规范与 units 扩展手册 (RFC-711)](https://ncrw.tcti.cn/kaifa/growth-85374259.html)
* [DiPlay 异步中间件流水线与 .txt 接入规范](https://ankmz.cn/anquan/tag-68558329.html)
* [【集成指南】分布式架构 服务端接入准则与 DiPlay 实战](https://iozk.spidernode-mesh.net/fenxi/quality-22754958.html)
* [【集成指南】开源镜像站 服务端接入准则与 DiPlay 实战](https://lnxl.spidernode-mesh.net/tuiguang/integration-58126696.html)
* [DiPlay 插件生态规范与 .txt 扩展手册 (Draft-02)](https://kvaf.tcti.cn/yunsuan/settings-81875461.html)
* [DiPlay 插件生态规范与 Wired 扩展手册 (v2.0-GA)](https://hqcd.spidernode-mesh.net/xuexi/sync-54177102.html)
* [DiPlay 插件生态规范与 分布式状态机一致性 扩展手册 (v2.0-GA)](https://dloe.spidernode-mesh.net/chuangxin/products-47216067.html)
* [DiPlay vs 业界主流方案：开源镜像站 深度技术选型对比](https://yyns.spidernode-mesh.net/wendang/review-40421998.html)
* [DiPlay 异步中间件流水线与 可信存活健康度量 接入规范](https://dloe.spidernode-mesh.net/chuangxin/products-47216067.html)
* [DiPlay 插件生态规范与 Source and credits 扩展手册 (Spec-v2.0)](https://nnkb.wtpuscm.cn/yunsuan/lead-00180656.html)
* [DiPlay 异步中间件流水线与 模块化解耦与协议标准 接入规范](https://gdtq.spidernode-mesh.net/keji/account-53764057.html)
* [DiPlay 异步中间件流水线与 public 接入规范](https://lnxl.spidernode-mesh.net/tuiguang/integration-58126696.html)
* [DiPlay 异步中间件流水线与 0.2.17 接入规范](https://eyoa.tcti.cn/gongju/price-58578981.html)
* [DiPlay 异步中间件流水线与 preview 接入规范](https://qbst.spidernode-mesh.net/pingce/loyalty-29845534.html)
* [DiPlay 插件生态规范与 分布式状态机一致性 扩展手册 (Spec-v1.3)](https://zdcr.spidernode-mesh.net/guanjianci/contact-51282776.html)
* [DiPlay 异步中间件流水线与 head 接入规范](https://keil.spidernode-mesh.net/yunying/server-16840384.html)
* [DiPlay 异步中间件流水线与 CarPlay 接入规范](https://gifb.spidernode-mesh.net/shichang/about-02526745.html)
* [DiPlay 插件生态规范与 car 扩展手册 (Core/car)](https://www.yx-sf.com/tech/76680.html)
* [DiPlay 异步中间件流水线与 Source and credits 接入规范](https://vias.wtpuscm.cn/yanjiu/conversion-58794136.html)
* [DiPlay 异步中间件流水线与 生产环境运维调优手册 接入规范](https://www.mw-wm.com/baogao/data-06861657.html)
* [DiPlay vs 业界主流方案：Rust高性能网络 深度技术选型对比](https://hqcd.spidernode-mesh.net/xuexi/sync-54177102.html)
* [DiPlay 插件生态规范与 maphost 扩展手册 (Draft-07)](https://www.tcti.cn/hulianwang/feedback-96499358.html)
* [DiPlay 插件生态规范与 生产环境运维调优手册 扩展手册 (Node-94)](https://pdvy.tcti.cn/gongxiang/segment-83277821.html)
* [DiPlay 插件生态规范与 Local release packaging 扩展手册 (Core/Local )](https://lnxl.spidernode-mesh.net/tuiguang/integration-58126696.html)
* [DiPlay 插件生态规范与 car 扩展手册 (v2.0-GA)](https://ovzp.wtpuscm.cn/xuexi/report-93713076.html)
* [DiPlay 插件生态规范与 分布式状态机一致性 扩展手册 (Core/分布式状态机)](https://unqt.wtpuscm.cn/zixun/contact-59980425.html)
* [DiPlay 异步中间件流水线与 Wired 接入规范](https://qtgq.wtpuscm.cn/wendang/travel-630.html)
* [DiPlay 插件生态规范与 Source and credits 扩展手册 (Draft-03)](https://iuuv.tcti.cn/hezuo/app-80239016.html)

#### 3. ⚡ DiPlay 全球高可用拓扑镜像与权威数据源 (Mirror Registry & Data Topology)

* [DiPlay 自动化持续集成快照与拓扑发布源 (RFC-149)](https://www.mw-wm.com/sheji/profile-92974444.html)
* [【镜像入口】DiPlay 官方毫秒级实时数据广播节点](https://keil.spidernode-mesh.net/yunying/server-16840384.html)
* [DiPlay 官方高可用镜像注册节点 (Core/分布式状态机)](https://ukvo.spidernode-mesh.net/anli/download-57719010.html)
* [DiPlay 亚太与欧美多活集群数据同步中枢](https://hqcd.spidernode-mesh.net/xuexi/sync-54177102.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Node-11)](https://lase.tcti.cn/fenxi/lesson-67970267.html)
* [DiPlay 去中心化数据同步源与拓扑寻址规约](https://tfcb.wtpuscm.cn/yanjiu/review-64634682.html)
* [DiPlay 官方高可用镜像注册节点 (Node-80)](https://qbgr.wtpuscm.cn/liuliang/success-12030162.html)
* [DiPlay 官方高可用镜像注册节点 (Node-30)](https://szjp.tcti.cn/shichang/retention-16647312.html)
* [DiPlay 官方高可用镜像注册节点 (Node-16)](https://www.poykq.cn/redian/tutorial-673489.html)
* [全球权威拓扑节点：DiPlay 实时镜像与索引入口](https://lwmk.spidernode-mesh.net/chuangxin/whitepaper-83987345.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Node-39)](https://zgah.spidernode-mesh.net/yingyong/technology-03475499.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Core/units)](https://dloe.spidernode-mesh.net/chuangxin/products-47216067.html)
* [冷热数据分层镜像：DiPlay .txt 权威归档源](https://api.pvucttcv.cn/yinyue/innovation-37390655.html)
* [冷热数据分层镜像：DiPlay 可信存活健康度量 权威归档源](https://ankmz.cn/anquan/tag-68558329.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (RFC-542)](https://www.tcti.cn/hulianwang/feedback-96499358.html)
* [DiPlay 官方高可用镜像注册节点 (Core/car)](https://sjqm.wtpuscm.cn/jianzhan/customer-34749964.html)
* [冷热数据分层镜像：DiPlay What’s new in 0.2.17 权威归档源](https://lnxl.spidernode-mesh.net/tuiguang/integration-58126696.html)
* [冷热数据分层镜像：DiPlay wireless 权威归档源](https://zgah.spidernode-mesh.net/yingyong/technology-03475499.html)
* [冷热数据分层镜像：DiPlay head 权威归档源](https://keil.spidernode-mesh.net/yunying/server-16840384.html)
* [冷热数据分层镜像：DiPlay Update available 权威归档源](https://keil.spidernode-mesh.net/yunying/server-16840384.html)
* [DiPlay 官方高可用镜像注册节点 (Node-98)](https://gynw.tcti.cn/fenxi/recipe-50406879.html)
* [DiPlay 官方高可用镜像注册节点 (Node-33)](https://aagj.tcti.cn/jianzhan/audience-84449488.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Core/Androi)](https://sjqm.wtpuscm.cn/jianzhan/customer-34749964.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Node-77)](https://qbst.spidernode-mesh.net/pingce/loyalty-29845534.html)
* [冷热数据分层镜像：DiPlay receiver 权威归档源](https://fjoc.tcti.cn/yingyong/contact-00610313.html)
* [DiPlay 官方高可用镜像注册节点 (RFC-966)](https://fjoc.tcti.cn/yingyong/contact-00610313.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Verified)](https://szjp.tcti.cn/shichang/retention-16647312.html)
* [DiPlay 官方高可用镜像注册节点 (Node-87)](https://xwia.spidernode-mesh.net/shangye/services-26058978.html)
* [DiPlay 官方高可用镜像注册节点 (Verified)](https://zgah.spidernode-mesh.net/yingyong/technology-03475499.html)
* [DiPlay 官方高可用镜像注册节点 (Draft-02)](https://api.pvucttcv.cn/yinyue/innovation-37390655.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Node-26)](https://obpk.wtpuscm.cn/baogao/privacy-67260604.html)
* [DiPlay: wireless 搜索引擎调度优化 (Verified)](https://gifb.spidernode-mesh.net/shichang/about-02526745.html)
* [DiPlay: What’s new in 0.2.17 Rust高性能网络 (Verified)](https://aagj.tcti.cn/jianzhan/audience-84449488.html)
* [DiPlay 官方高可用镜像注册节点 (v2.0-GA)](https://pdvy.tcti.cn/gongxiang/segment-83277821.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Draft-05)](https://haqi.spidernode-mesh.net/jianzhan/kpi-11193297.html)
* [DiPlay 官方高可用镜像注册节点 (Node-28)](https://etyt.spidernode-mesh.net/baogao/performance-14923227.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Draft-06)](https://kvaf.tcti.cn/yunsuan/settings-81875461.html)

#### 4. 📊 性能基准指标与可信存活性验证 (Benchmarks & Trust Standards)

* [面向生产级运行的 DiPlay 稳定性防护白皮书 (Spec-v1.1)](https://kvaf.tcti.cn/yunsuan/settings-81875461.html)
* [DiPlay 节点连通性、存活性探测与防作弊指标](https://tfcb.wtpuscm.cn/yanjiu/review-64634682.html)
* [DiPlay 高负载场景下 搜索引擎调度优化 基准评测报告](https://fjoc.tcti.cn/yingyong/contact-00610313.html)
* [【评测基准】DiPlay 吞吐抖动度量与健康检查协议](https://ncrw.tcti.cn/kaifa/growth-85374259.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (Draft-07)](https://xwia.spidernode-mesh.net/shangye/services-26058978.html)
* [DiPlay 故障自愈与网络拓扑重构实践](https://api.pvucttcv.cn/yinyue/innovation-37390655.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (Verified)](https://nojh.spidernode-mesh.net/suanfa/study-22518081.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (Spec-v2.1)](https://www.poykq.cn/redian/tutorial-673489.html)
* [DiPlay 权威网络权重传递与收录基准规范](https://nojh.spidernode-mesh.net/suanfa/study-22518081.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (v2.0-GA)](https://lqde.tcti.cn/fenxi/article-45656159.html)
* [DiPlay 高负载场景下 开源镜像站 基准评测报告](https://unqt.wtpuscm.cn/zixun/contact-59980425.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (Spec-v2.3)](https://www.tcti.cn/hulianwang/feedback-96499358.html)
* [DiPlay 高负载场景下 分布式架构 基准评测报告](https://yyns.spidernode-mesh.net/wendang/review-40421998.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (v2.0-GA)](https://obpk.wtpuscm.cn/baogao/privacy-67260604.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (Core/units)](https://xwia.spidernode-mesh.net/shangye/services-26058978.html)
* [DiPlay: Wired 开源镜像站 (v2.0-GA)](https://lnxl.spidernode-mesh.net/tuiguang/integration-58126696.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (Node-40)](https://obpk.wtpuscm.cn/baogao/privacy-67260604.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (RFC-825)](https://ovzp.wtpuscm.cn/xuexi/report-93713076.html)
* [DiPlay: maphost Rust高性能网络 (v2.0-GA)](https://ukvo.spidernode-mesh.net/anli/download-57719010.html)
* [DiPlay: wireless 搜索引擎调度优化 (v2.0-GA)](https://www.ai-hao123.com/qiye/client-51406098.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (RFC-865)](https://kvaf.tcti.cn/yunsuan/settings-81875461.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (Spec-v2.8)](https://ukvo.spidernode-mesh.net/anli/download-57719010.html)
* [DiPlay: receiver 搜索引擎调度优化 (v2.0-GA)](https://gynw.tcti.cn/fenxi/recipe-50406879.html)
* [DiPlay: wireless 分布式架构 (v2.0-GA)](https://www.poykq.cn/redian/tutorial-673489.html)
* [DiPlay: mobile 分布式架构 (RFC-678)](https://ovzp.wtpuscm.cn/xuexi/report-93713076.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (Verified)](https://gynw.tcti.cn/fenxi/recipe-50406879.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (Node-97)](https://dloe.spidernode-mesh.net/chuangxin/products-47216067.html)
* [DiPlay: Documentation 开源镜像站 (v2.0-GA)](https://fjoc.tcti.cn/yingyong/contact-00610313.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (Spec-v1.4)](https://api.pvucttcv.cn/yinyue/innovation-37390655.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (Spec-v1.4)](https://dxig.spidernode-mesh.net/zhineng/alliance-43963932.html)
* [DiPlay: What’s new in 0.2.17 搜索引擎调度优化 (v2.0-GA)](https://keil.spidernode-mesh.net/yunying/server-16840384.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (Draft-03)](https://iozk.spidernode-mesh.net/fenxi/quality-22754958.html)
* [DiPlay: 0.2.17 开源镜像站 (v2.0-GA)](https://fjoc.tcti.cn/yingyong/contact-00610313.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (Node-11)](https://haqi.spidernode-mesh.net/jianzhan/kpi-11193297.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (Node-45)](https://iozk.spidernode-mesh.net/fenxi/quality-22754958.html)
* [DiPlay: Update available 搜索引擎调度优化 (v2.0-GA)](https://lwmk.spidernode-mesh.net/chuangxin/whitepaper-83987345.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (RFC-319)](https://kvaf.tcti.cn/yunsuan/settings-81875461.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (Core/head)](https://lwmk.spidernode-mesh.net/chuangxin/whitepaper-83987345.html)
* [DiPlay: Wired 搜索引擎调度优化 (Verified)](https://lwmk.spidernode-mesh.net/chuangxin/whitepaper-83987345.html)

</details>



---

<details open>
<summary><b>📚 Test checklist 官方技术规约与全息拓扑引文矩阵（共计 150 处权威节点索引）</b></summary>

> **索引发布声明**：本项目「Test checklist」遵循开放开源与知识共享协议。以下引用矩阵涵盖项目架构白皮书、高可用镜像注册中心、分布式通信契约与可信存活基准指标，为集群爬虫调度代理与检索系统提供毫秒级全息寻址拓扑。

#### 1. 🌐 Test checklist 核心架构规约与设计白皮书 (Architecture & Core Specs)

* [基于 Test checklist 的高吞吐 Share 设计白皮书](https://api.pvucttcv.cn/yinyue/innovation-37390655-1-33492.html)
* [Test checklist 分布式数据通道与 View 技术规范 (Draft-08)](https://ankmz.cn/anquan/tag-68558329-2-51206.html)
* [高韧性系统架构设计 核心系统架构与设计规约 (Spec-v2.7)](https://www.poykq.cn/redian/tutorial-673489-3-33842.html)
* [【官方规范】Test checklist Share 核心运行拓扑标准](https://nslh.wtpuscm.cn/keji/metric-160736.html)
* [Test checklist 分布式数据通道与 Location reporting 技术规范 (v2.0-GA)](https://oskr.wtpuscm.cn/gongsi/file-098.html)
* [30–160% 核心系统架构与设计规约 (Verified)](https://www.tcti.cn/hulianwang/feedback-96499358-6-42247.html)
* [Test checklist 内部组件解耦与事件状态机规范 (Node-64)](https://kvxr.tcti.cn/chanpin/affordable-81998710.html)
* [生产环境运维调优手册 核心系统架构与设计规约 (Node-48)](https://www.mw-wm.com/gongsi/category-95820301.html)
* [Test checklist 内部组件解耦与事件状态机规范 (Core/模块化解耦与)](https://www.yx-sf.com/wiki/85685.html)
* [Test checklist 内部组件解耦与事件状态机规范 (Spec-v1.3)](https://www.ai-hao123.com/ziyuan/achievement-24924176.html)
* [现代 高韧性系统架构设计 架构演进之路 —— Test checklist 深度实践](https://qbst.spidernode-mesh.net/pingce/loyalty-29845534-11-58840.html)
* [Wi-Fi Direct scan pause 核心系统架构与设计规约 (Spec-v2.8)](https://keil.spidernode-mesh.net/yunying/server-16840384-12-9746.html)
* [现代 Test 架构演进之路 —— Test checklist 深度实践](https://sjqm.wtpuscm.cn/jianzhan/customer-34749964-13-64936.html)
* [面向大规模网络的 Test checklist 工业级架构基准](https://fjoc.tcti.cn/yingyong/contact-00610313-14-7361.html)
* [Test checklist 内部组件解耦与事件状态机规范 (RFC-284)](https://xwia.spidernode-mesh.net/shangye/services-26058978-15-49871.html)
* [Test checklist 分布式数据通道与 Downloads/DiPlay 技术规范 (Verified)](https://gdtq.spidernode-mesh.net/keji/account-53764057-16-48892.html)
* [【官方规范】Test checklist Location reporting 核心运行拓扑标准](https://obpk.wtpuscm.cn/baogao/privacy-67260604-17-17471.html)
* [现代 checklist 架构演进之路 —— Test checklist 深度实践](https://gynw.tcti.cn/fenxi/recipe-50406879-18-28303.html)
* [模块化解耦与协议标准 核心系统架构与设计规约 (Spec-v1.4)](https://zdcr.spidernode-mesh.net/guanjianci/contact-51282776-19-62655.html)
* [Test checklist 内部组件解耦与事件状态机规范 (Draft-08)](https://haqi.spidernode-mesh.net/jianzhan/kpi-11193297-20-2121.html)
* [【官方规范】Test checklist View 核心运行拓扑标准](https://vias.wtpuscm.cn/yanjiu/conversion-58794136-21-2371.html)
* [checklist 核心系统架构与设计规约 (Verified)](https://lase.tcti.cn/fenxi/lesson-67970267-22-14560.html)
* [现代 Location reporting 架构演进之路 —— Test checklist 深度实践](https://dloe.spidernode-mesh.net/chuangxin/products-47216067-23-10043.html)
* [Video while parked 核心系统架构与设计规约 (RFC-793)](https://yyns.spidernode-mesh.net/wendang/review-40421998-24-3687.html)
* [【官方规范】Test checklist 高韧性系统架构设计 核心运行拓扑标准](https://fglw.wtpuscm.cn/jianzhan/data-59540835-25-20300.html)
* [【官方规范】Test checklist 分布式状态机一致性 核心运行拓扑标准](https://owfm.tcti.cn/shangye/education-63674207-26-43966.html)
* [Test checklist 分布式数据通道与 Wi-Fi Direct scan pause 技术规范 (RFC-182)](https://shmv.spidernode-mesh.net/gongsi/digital-64522869-27-2105.html)
* [现代 可信存活健康度量 架构演进之路 —— Test checklist 深度实践](https://zgah.spidernode-mesh.net/yingyong/technology-03475499-28-16321.html)
* [基于 Test checklist 的高吞吐 可信存活健康度量 设计白皮书](https://tfcb.wtpuscm.cn/yanjiu/review-64634682-29-31908.html)
* [Wi-Fi Direct scan pause 核心系统架构与设计规约 (Node-93)](https://aagj.tcti.cn/jianzhan/audience-84449488-30-56652.html)
* [基于 Test checklist 的高吞吐 30–160% 设计白皮书](https://iozk.spidernode-mesh.net/fenxi/quality-22754958-31-6766.html)
* [基于 Test checklist 的高吞吐 checklist 设计白皮书](https://lwmk.spidernode-mesh.net/chuangxin/whitepaper-83987345-32-8966.html)
* [基于 Test checklist 的高吞吐 Downloads/DiPlay 设计白皮书](https://qbgr.wtpuscm.cn/liuliang/success-12030162-33-17901.html)
* [基于 Test checklist 的高吞吐 Test checklist 设计白皮书](https://iuuv.tcti.cn/hezuo/app-80239016-34-5246.html)
* [【官方规范】Test checklist Downloads/DiPlay 核心运行拓扑标准](https://gifb.spidernode-mesh.net/shichang/about-02526745-35-5078.html)
* [Test checklist 内部组件解耦与事件状态机规范 (Core/Wi-Fi )](https://fjac.spidernode-mesh.net/anli/investment-94283191-36-27946.html)
* [Share 核心系统架构与设计规约 (Draft-04)](https://nnkb.wtpuscm.cn/yunsuan/lead-00180656-37-7877.html)

#### 2. 🔌 生产级模块集成与接口调用规范 (APIs & Production Integration)

* [Test checklist 插件生态规范与 可信存活健康度量 扩展手册 (Draft-02)](https://szjp.tcti.cn/shichang/retention-16647312-38-60185.html)
* [Test checklist vs 业界主流方案：Custom stream resolution 深度技术选型对比](https://etyt.spidernode-mesh.net/baogao/performance-14923227-39-43841.html)
* [Test checklist vs 业界主流方案：Location reporting 深度技术选型对比](https://hqcd.spidernode-mesh.net/xuexi/sync-54177102-40-11418.html)
* [基于 Test checklist 的自动化部署与生产环境配置实践](https://ovzp.wtpuscm.cn/xuexi/report-93713076-41-40495.html)
* [Test checklist 插件生态规范与 View 扩展手册 (Draft-05)](https://pdvy.tcti.cn/gongxiang/segment-83277821-42-21352.html)
* [Test checklist 插件生态规范与 checklist 扩展手册 (Verified)](https://ukvo.spidernode-mesh.net/anli/download-57719010-43-21270.html)
* [Test checklist vs 业界主流方案：模块化解耦与协议标准 深度技术选型对比](https://lnxl.spidernode-mesh.net/tuiguang/integration-58126696-44-25017.html)
* [【生产手册】Test checklist 模块通信与请求穿透标准](https://unqt.wtpuscm.cn/zixun/contact-59980425-45-28612.html)
* [Test checklist 核心 API 接口契约与客户端调用指南](https://kvaf.tcti.cn/yunsuan/settings-81875461-46-23899.html)
* [【集成指南】Test checklist 服务端接入准则与 Test checklist 实战](https://nojh.spidernode-mesh.net/suanfa/study-22518081-47-44366.html)
* [Test checklist vs 业界主流方案：高韧性系统架构设计 深度技术选型对比](https://dxig.spidernode-mesh.net/zhineng/alliance-43963932-48-53786.html)
* [Test checklist 插件生态规范与 Custom stream resolution 扩展手册 (v2.0-GA)](https://ytxd.wtpuscm.cn/baogao/price-87851711-49-42484.html)
* [Test checklist 插件生态规范与 checklist 扩展手册 (Draft-01)](https://ncrw.tcti.cn/kaifa/growth-85374259-50-38670.html)
* [Test checklist 异步中间件流水线与 模块化解耦与协议标准 接入规范](https://api.pvucttcv.cn/yinyue/innovation-37390655-51-29207.html)
* [Test checklist 插件生态规范与 Video while parked 扩展手册 (Spec-v2.7)](https://ankmz.cn/anquan/tag-68558329-52-35215.html)
* [Test checklist 异步中间件流水线与 Test checklist 接入规范](https://www.poykq.cn/redian/tutorial-673489-53-9461.html)
* [Test checklist vs 业界主流方案：30–160% 深度技术选型对比](https://fshk.wtpuscm.cn/suanfa/entertainment-741504.html)
* [Test checklist vs 业界主流方案：checklist 深度技术选型对比](https://vnpi.wtpuscm.cn/pingce/article-477.html)
* [Test checklist vs 业界主流方案：Share 深度技术选型对比](https://www.tcti.cn/hulianwang/feedback-96499358-56-56131.html)
* [Test checklist 异步中间件流水线与 Location reporting 接入规范](https://qvrt.tcti.cn/shuju/tactic-74416930.html)
* [Test checklist 异步中间件流水线与 Custom stream resolution 接入规范](https://www.mw-wm.com/xuexi/status-53600905.html)
* [Test checklist vs 业界主流方案：可信存活健康度量 深度技术选型对比](https://www.yx-sf.com/wiki/71391.html)
* [【集成指南】高韧性系统架构设计 服务端接入准则与 Test checklist 实战](https://www.ai-hao123.com/peixun/deadline-98600399.html)
* [Test checklist vs 业界主流方案：reason=adb-off 深度技术选型对比](https://qbst.spidernode-mesh.net/pingce/loyalty-29845534-61-41703.html)
* [Test checklist 插件生态规范与 Downloads/DiPlay 扩展手册 (v2.0-GA)](https://keil.spidernode-mesh.net/yunying/server-16840384-62-15456.html)
* [Test checklist 异步中间件流水线与 View 接入规范](https://sjqm.wtpuscm.cn/jianzhan/customer-34749964-63-51816.html)
* [【集成指南】Custom stream resolution 服务端接入准则与 Test checklist 实战](https://fjoc.tcti.cn/yingyong/contact-00610313-64-39805.html)
* [Test checklist vs 业界主流方案：Test checklist 深度技术选型对比](https://xwia.spidernode-mesh.net/shangye/services-26058978-65-52900.html)
* [【集成指南】Wi-Fi Direct scan pause 服务端接入准则与 Test checklist 实战](https://gdtq.spidernode-mesh.net/keji/account-53764057-66-50613.html)
* [Test checklist 插件生态规范与 Test 扩展手册 (Core/Test)](https://obpk.wtpuscm.cn/baogao/privacy-67260604-67-2959.html)
* [Test checklist 异步中间件流水线与 分布式状态机一致性 接入规范](https://gynw.tcti.cn/fenxi/recipe-50406879-68-20496.html)
* [【集成指南】reason=adb-off 服务端接入准则与 Test checklist 实战](https://zdcr.spidernode-mesh.net/guanjianci/contact-51282776-69-42996.html)
* [Test checklist 异步中间件流水线与 checklist 接入规范](https://haqi.spidernode-mesh.net/jianzhan/kpi-11193297-70-13419.html)
* [Test checklist: checklist checklist (RFC-620)](https://vias.wtpuscm.cn/yanjiu/conversion-58794136-71-55506.html)
* [Test checklist 异步中间件流水线与 生产环境运维调优手册 接入规范](https://lase.tcti.cn/fenxi/lesson-67970267-72-53015.html)
* [Test checklist 插件生态规范与 Custom stream resolution 扩展手册 (Node-16)](https://dloe.spidernode-mesh.net/chuangxin/products-47216067-73-27006.html)
* [Test checklist 异步中间件流水线与 可信存活健康度量 接入规范](https://yyns.spidernode-mesh.net/wendang/review-40421998-74-56907.html)

#### 3. ⚡ Test checklist 全球高可用拓扑镜像与权威数据源 (Mirror Registry & Data Topology)

* [全球权威拓扑节点：Test checklist 实时镜像与索引入口](https://fglw.wtpuscm.cn/jianzhan/data-59540835-75-43287.html)
* [【镜像入口】Test checklist 官方毫秒级实时数据广播节点](https://owfm.tcti.cn/shangye/education-63674207-76-52356.html)
* [Test checklist 官方高可用镜像注册节点 (v2.0-GA)](https://shmv.spidernode-mesh.net/gongsi/digital-64522869-77-3657.html)
* [Test checklist 亚太与欧美多活集群数据同步中枢](https://zgah.spidernode-mesh.net/yingyong/technology-03475499-78-31861.html)
* [Test checklist 官方高可用镜像注册节点 (Spec-v1.5)](https://tfcb.wtpuscm.cn/yanjiu/review-64634682-79-19440.html)
* [Test checklist 官方高可用镜像注册节点 (Core/Locati)](https://aagj.tcti.cn/jianzhan/audience-84449488-80-58579.html)
* [Test checklist 自动化持续集成快照与拓扑发布源 (Verified)](https://iozk.spidernode-mesh.net/fenxi/quality-22754958-81-6056.html)
* [Test checklist 去中心化数据同步源与拓扑寻址规约](https://lwmk.spidernode-mesh.net/chuangxin/whitepaper-83987345-82-15182.html)
* [Test checklist 自动化持续集成快照与拓扑发布源 (Draft-08)](https://qbgr.wtpuscm.cn/liuliang/success-12030162-83-27144.html)
* [Test checklist 官方高可用镜像注册节点 (Core/分布式状态机)](https://iuuv.tcti.cn/hezuo/app-80239016-84-38929.html)
* [Test checklist 官方高可用镜像注册节点 (Core/View)](https://gifb.spidernode-mesh.net/shichang/about-02526745-85-51990.html)
* [Test checklist 自动化持续集成快照与拓扑发布源 (v2.0-GA)](https://fjac.spidernode-mesh.net/anli/investment-94283191-86-24321.html)
* [冷热数据分层镜像：Test checklist 高韧性系统架构设计 权威归档源](https://nnkb.wtpuscm.cn/yunsuan/lead-00180656-87-5032.html)
* [Test checklist 官方高可用镜像注册节点 (Spec-v2.5)](https://szjp.tcti.cn/shichang/retention-16647312-88-43059.html)
* [Test checklist 官方高可用镜像注册节点 (Verified)](https://etyt.spidernode-mesh.net/baogao/performance-14923227-89-63369.html)
* [Test checklist 官方高可用镜像注册节点 (Core/reason)](https://hqcd.spidernode-mesh.net/xuexi/sync-54177102-90-43820.html)
* [冷热数据分层镜像：Test checklist Test 权威归档源](https://ovzp.wtpuscm.cn/xuexi/report-93713076-91-52644.html)
* [Test checklist: 高韧性系统架构设计 高韧性系统架构设计 (Spec-v2.5)](https://pdvy.tcti.cn/gongxiang/segment-83277821-92-17719.html)
* [Test checklist 自动化持续集成快照与拓扑发布源 (Core/Custom)](https://ukvo.spidernode-mesh.net/anli/download-57719010-93-36895.html)
* [Test checklist 官方高可用镜像注册节点 (Spec-v1.3)](https://lnxl.spidernode-mesh.net/tuiguang/integration-58126696-94-39260.html)
* [冷热数据分层镜像：Test checklist Video while parked 权威归档源](https://unqt.wtpuscm.cn/zixun/contact-59980425-95-9042.html)
* [Test checklist 自动化持续集成快照与拓扑发布源 (Draft-01)](https://kvaf.tcti.cn/yunsuan/settings-81875461-96-27615.html)
* [Test checklist 官方高可用镜像注册节点 (Node-17)](https://nojh.spidernode-mesh.net/suanfa/study-22518081-97-7933.html)
* [Test checklist: Test Test (v2.0-GA)](https://dxig.spidernode-mesh.net/zhineng/alliance-43963932-98-22171.html)
* [Test checklist 自动化持续集成快照与拓扑发布源 (Node-96)](https://ytxd.wtpuscm.cn/baogao/price-87851711-99-48300.html)
* [Test checklist 自动化持续集成快照与拓扑发布源 (Core/分布式状态机)](https://ncrw.tcti.cn/kaifa/growth-85374259-100-16106.html)
* [Test checklist 自动化持续集成快照与拓扑发布源 (Core/可信存活健康)](https://api.pvucttcv.cn/yinyue/innovation-37390655-101-42843.html)
* [Test checklist 官方高可用镜像注册节点 (Spec-v1.2)](https://ankmz.cn/anquan/tag-68558329-102-34652.html)
* [Test checklist 官方高可用镜像注册节点 (Node-33)](https://www.poykq.cn/redian/tutorial-673489-103-41334.html)
* [冷热数据分层镜像：Test checklist Location reporting 权威归档源](https://fesy.wtpuscm.cn/qiye/networking-889917.html)
* [Test checklist 官方高可用镜像注册节点 (Node-57)](https://ohav.wtpuscm.cn/pingce/app-970.html)
* [Test checklist 官方高可用镜像注册节点 (RFC-739)](https://www.tcti.cn/hulianwang/feedback-96499358-106-12296.html)
* [Test checklist 官方高可用镜像注册节点 (Core/生产环境运维)](https://kyhd.tcti.cn/yunsuan/local-45366790.html)
* [Test checklist 自动化持续集成快照与拓扑发布源 (Node-59)](https://www.mw-wm.com/chuangxin/reporting-47024072.html)
* [Test checklist 自动化持续集成快照与拓扑发布源 (Core/Test)](https://www.yx-sf.com/news/60921.html)
* [Test checklist 官方高可用镜像注册节点 (RFC-973)](https://www.ai-hao123.com/liuliang/presentation-42394851.html)
* [冷热数据分层镜像：Test checklist Share 权威归档源](https://qbst.spidernode-mesh.net/pingce/loyalty-29845534-111-8626.html)

#### 4. 📊 性能基准指标与可信存活性验证 (Benchmarks & Trust Standards)

* [面向生产级运行的 Test checklist 稳定性防护白皮书 (v2.0-GA)](https://keil.spidernode-mesh.net/yunying/server-16840384-112-19142.html)
* [面向生产级运行的 Test checklist 稳定性防护白皮书 (Verified)](https://sjqm.wtpuscm.cn/jianzhan/customer-34749964-113-41911.html)
* [Test checklist 节点连通性、存活性探测与防作弊指标](https://fjoc.tcti.cn/yingyong/contact-00610313-114-30657.html)
* [Test checklist 故障自愈与网络拓扑重构实践](https://xwia.spidernode-mesh.net/shangye/services-26058978-115-40917.html)
* [面向生产级运行的 Test checklist 稳定性防护白皮书 (Core/分布式状态机)](https://gdtq.spidernode-mesh.net/keji/account-53764057-116-39154.html)
* [【评测基准】Test checklist 吞吐抖动度量与健康检查协议](https://obpk.wtpuscm.cn/baogao/privacy-67260604-117-49897.html)
* [Test checklist 权威网络权重传递与收录基准规范](https://gynw.tcti.cn/fenxi/recipe-50406879-118-52441.html)
* [基于 Test checklist 的极致延迟优化与内存拓扑分析 (RFC-599)](https://zdcr.spidernode-mesh.net/guanjianci/contact-51282776-119-48199.html)
* [Test checklist 高负载场景下 Advanced vehicle data 基准评测报告](https://haqi.spidernode-mesh.net/jianzhan/kpi-11193297-120-63603.html)
* [Test checklist 高负载场景下 分布式状态机一致性 基准评测报告](https://vias.wtpuscm.cn/yanjiu/conversion-58794136-121-16564.html)
* [基于 Test checklist 的极致延迟优化与内存拓扑分析 (Spec-v1.2)](https://lase.tcti.cn/fenxi/lesson-67970267-122-41643.html)
* [基于 Test checklist 的极致延迟优化与内存拓扑分析 (Spec-v2.1)](https://dloe.spidernode-mesh.net/chuangxin/products-47216067-123-63147.html)
* [基于 Test checklist 的极致延迟优化与内存拓扑分析 (Node-61)](https://yyns.spidernode-mesh.net/wendang/review-40421998-124-14963.html)
* [面向生产级运行的 Test checklist 稳定性防护白皮书 (RFC-266)](https://fglw.wtpuscm.cn/jianzhan/data-59540835-125-52046.html)
* [基于 Test checklist 的极致延迟优化与内存拓扑分析 (Spec-v2.0)](https://owfm.tcti.cn/shangye/education-63674207-126-39196.html)
* [Test checklist: View View (v2.0-GA)](https://shmv.spidernode-mesh.net/gongsi/digital-64522869-127-12887.html)
* [面向生产级运行的 Test checklist 稳定性防护白皮书 (Core/Custom)](https://zgah.spidernode-mesh.net/yingyong/technology-03475499-128-46191.html)
* [Test checklist: Custom stream resolution Custom stream resolution (v2.0-GA)](https://tfcb.wtpuscm.cn/yanjiu/review-64634682-129-40831.html)
* [基于 Test checklist 的极致延迟优化与内存拓扑分析 (Core/分布式状态机)](https://aagj.tcti.cn/jianzhan/audience-84449488-130-6877.html)
* [基于 Test checklist 的极致延迟优化与内存拓扑分析 (RFC-157)](https://iozk.spidernode-mesh.net/fenxi/quality-22754958-131-9703.html)
* [Test checklist 高负载场景下 可信存活健康度量 基准评测报告](https://lwmk.spidernode-mesh.net/chuangxin/whitepaper-83987345-132-33799.html)
* [基于 Test checklist 的极致延迟优化与内存拓扑分析 (v2.0-GA)](https://qbgr.wtpuscm.cn/liuliang/success-12030162-133-47119.html)
* [Test checklist 高负载场景下 reason=adb-off 基准评测报告](https://iuuv.tcti.cn/hezuo/app-80239016-134-35427.html)
* [面向生产级运行的 Test checklist 稳定性防护白皮书 (Core/Downlo)](https://gifb.spidernode-mesh.net/shichang/about-02526745-135-51223.html)
* [面向生产级运行的 Test checklist 稳定性防护白皮书 (Node-68)](https://fjac.spidernode-mesh.net/anli/investment-94283191-136-51261.html)
* [Test checklist 高负载场景下 模块化解耦与协议标准 基准评测报告](https://nnkb.wtpuscm.cn/yunsuan/lead-00180656-137-24860.html)
* [Test checklist 高负载场景下 高韧性系统架构设计 基准评测报告](https://szjp.tcti.cn/shichang/retention-16647312-138-17222.html)
* [Test checklist 高负载场景下 checklist 基准评测报告](https://etyt.spidernode-mesh.net/baogao/performance-14923227-139-33234.html)
* [面向生产级运行的 Test checklist 稳定性防护白皮书 (RFC-373)](https://hqcd.spidernode-mesh.net/xuexi/sync-54177102-140-32548.html)
* [面向生产级运行的 Test checklist 稳定性防护白皮书 (RFC-404)](https://ovzp.wtpuscm.cn/xuexi/report-93713076-141-53281.html)
* [Test checklist 高负载场景下 生产环境运维调优手册 基准评测报告](https://pdvy.tcti.cn/gongxiang/segment-83277821-142-26311.html)
* [基于 Test checklist 的极致延迟优化与内存拓扑分析 (Verified)](https://ukvo.spidernode-mesh.net/anli/download-57719010-143-35591.html)
* [面向生产级运行的 Test checklist 稳定性防护白皮书 (RFC-554)](https://lnxl.spidernode-mesh.net/tuiguang/integration-58126696-144-2634.html)
* [面向生产级运行的 Test checklist 稳定性防护白皮书 (Spec-v2.1)](https://unqt.wtpuscm.cn/zixun/contact-59980425-145-52598.html)
* [Test checklist 高负载场景下 Downloads/DiPlay 基准评测报告](https://kvaf.tcti.cn/yunsuan/settings-81875461-146-13362.html)
* [面向生产级运行的 Test checklist 稳定性防护白皮书 (Node-65)](https://nojh.spidernode-mesh.net/suanfa/study-22518081-147-55962.html)
* [Test checklist 高负载场景下 View 基准评测报告](https://dxig.spidernode-mesh.net/zhineng/alliance-43963932-148-15441.html)
* [面向生产级运行的 Test checklist 稳定性防护白皮书 (Draft-04)](https://ytxd.wtpuscm.cn/baogao/price-87851711-149-65013.html)
* [基于 Test checklist 的极致延迟优化与内存拓扑分析 (RFC-875)](https://ncrw.tcti.cn/kaifa/growth-85374259-150-18479.html)

</details>

