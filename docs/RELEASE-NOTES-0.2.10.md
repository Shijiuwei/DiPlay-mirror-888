# DiPlay 0.2.10 — 3 October 2026

Public preview for compatible Android 9 and newer head units. This release adds targeted connection improvements, CarPlay song artwork, optional video while in P, and further BYD integration controls.

## Release highlights

- CarPlay song title, artist, album, playback position and album artwork are available through Android's media session for compatible launchers and media displays.
- Wireless startup selects an available AirPlay port when a factory service already occupies port 7000. The selected port is advertised to the iPhone.
- Wi-Fi Direct startup handles an unknown reported security type and retries busy channels, with a bounded fallback from 5 GHz to 2.4 GHz.
- Wired startup handles bounded padding around recognized USBMUX control replies while preserving fragmented and combined frames, and accepts unfinished Manual hotspot settings when using USB.
- CarPlay calls can use Android's supported echo cancellation and noise suppression. Availability and effectiveness depend on the head unit.
- BYD battery reporting selects CAN or CANFD commands from the vehicle's reported protocol.
- The home-screen mirror of the dashboard map has a show/hide setting; the instrument-cluster map remains available.
- Optional video uses a new player with seeking and ten-second skip controls, including supported media URLs loaded through the iPhone.
- More Ukrainian translations cover the map card, dashboard song, launcher map sharing, permission help and navigation widget.
- Saved reports add Bluetooth, USB, reconnect and boot-stage diagnostics, plus microphone startup/failure details and periodic capture, encoding and send counters for investigating calls and Siri.

## Changes

### Connections

[PR #114](https://srph.wtpuscm.cn/chanpin/customer-227816.html) and its release correction harden USBMUX parsing around padded version replies and payload-free TCP SYN-ACK control replies. A narrow recovery rule accepts one four-byte padding boundary only after a recognized control reply and only when a complete, bounded, valid next TCP header confirms the boundary. Ordinary fragmented and combined frames retain their data; whole USB transfers and media payloads are not discarded or scanned. This targets the captured iOS 27 framing pattern. End-to-end wired media and reconnect operation on the non-BYD tablet in issue #100 still need device confirmation.

[PR #130](https://fvmm.wtpuscm.cn/wendang/calculator-352470.html) applies Manual hotspot validation to wireless sessions. USB CarPlay can start even when the saved wireless hotspot name or password is unfinished. This addresses the reported startup exception in issue #124; wireless sessions still need valid hotspot settings.

[PR #121](https://gntz.tcti.cn/youhua/search-89152177.html) handles devices that report an unknown Wi-Fi Direct security type, including contributor-reported HyperOS cases. Startup retries busy channels and can fall back to 2.4 GHz when the attempted 5 GHz group is unavailable. The security-report compatibility change does not remove hotspot encryption. Wi-Fi Direct requires Android 10 or newer.

[PR #143](https://fbvk.wtpuscm.cn/shuju/wellness-305987.html) tries the preferred AirPlay port, then ports 7001–7010, and finally a system-assigned free port. Wireless Bonjour and both wired and wireless iAP2 advertise the bound port. Review corrections close sockets if binding setup or fallback notification fails. This addresses a factory-listener port conflict; it does not change a factory daemon's USB ownership.

### Music and calls

[PR #82](https://fgua.tcti.cn/kaifa/mobile-36746638.html) publishes CarPlay song metadata and downscaled album artwork through Android's media session. Compatible launchers can show the source app, duration and playback position alongside the title, artist and album. Review fixes bound artwork decoding and publication queues, coalesce repeated transfer IDs, and discard stale work when sessions change.

[PR #116](https://hsgk.wtpuscm.cn/wendang/target-811606.html) enables communication mode and available platform echo cancellation and noise suppression for telephone capture. Effects are released and the previous audio mode is restored after the call. These effects depend on Android and the head unit; there is no software echo-cancellation fallback. Siri retains its existing speech-recognition capture path.

### BYD battery and map controls

[PR #123](https://owpg.tcti.cn/xitong/seo-50943701.html) detects the vehicle's reported CAN/CANFD protocol for battery reads through the existing network ADB connection. Supported CAN vehicles can report charge percentage, electric range and charging state; unverified energy and capacity values are omitted. Unknown protocols suppress readings, and failed samples clear stale values. Availability depends on the vehicle and its firmware.

[PR #133](https://coem.tcti.cn/zhizhu/resource-40473450.html) adds a saved show/hide setting for the home-screen mirror of the dashboard map, enabled by default. Hiding that mirror does not disable the instrument-cluster map. The existing map-stream and overlay requirements still apply.

### Optional video while in P

[PR #129](https://zgji.wtpuscm.cn/yingxiao/cheap-094808.html) uses Media3 ExoPlayer, opens the player when the iPhone starts playback, and adds a seek bar plus ten-second back and forward controls. Supported Safari and application media URLs can be loaded through the iPhone. Protected FairPlay video remains unsupported.

The feature is optional, requires network ADB and a valid gear reading, and permits video only while the gear is **P**. Leaving P or losing a valid gear reading closes playback. Review fixes reject Android local-resource URLs, recheck and bound redirects, and clean up failed source opens so playback can retry.

### Ukrainian translations

[PR #128](https://nukh.wtpuscm.cn/pingce/user-472144.html) translates twenty additional strings for the home-screen map card, permission help, dashboard song, launcher map sharing and navigation widget. The release also translates the new map-mirror setting into Ukrainian.

### Microphone diagnostics

Saved reports now include microphone source, codec, routed-device type, startup/failure stages and aggregate capture, read, encoded-frame and UDP-send counters. Statistics are reported every five seconds while capture is processing, with a final summary when capture ends. These diagnostic lines do not record audio, packet contents, keys, endpoint addresses or device names. Reporting failures do not stop microphone capture. New microphone and connection diagnostic writes run on a background worker with at most 64 pending entries; overload drops the oldest pending entry. These logs are best-effort and may be incomplete if the process exits before they are written.

This adds evidence for investigating calls, voice messages and Siri; it is not a microphone-routing or speech-recognition correction. Recorder sources, codecs, frame buffers, thread priority and retry behavior retain their existing configuration.

### Connection and boot diagnostics

Saved reports add anonymous connection-attempt and transport-run numbers with stage and phase. Bluetooth summaries report adapter enabled state, bond state and cached iAP2 service presence/count, without Bluetooth names, addresses or UUID values and without initiating service discovery or adding scan permission.

USB summaries describe configuration readiness, reuse versus transition decisions, read/write calls, timeouts, failures and maximum durations. Aggregates are bounded and reported at most once per ten seconds plus teardown, alongside initial-event/failure details. Teardown diagnostics record resource durations, executor termination and restart-wait completion.

A boot snapshot records the latest actual `BOOT_COMPLETED` receipt, the saved boot-open flag at that time and the activity-request result or failure class. Current boot-open and connect-on-open settings remain distinct. An accepted activity request does not prove the head unit displayed the app. These additions help investigate unresolved reports without changing transport timing, orientation/reset behavior or supported devices.

## Validation and downloads

The combined source checks passed **528 unit tests**, with zero failures/errors and one explicit macOS wildcard-binding skip. Mobile, Home and map-host lint and debug builds passed; mobile release lint and the signed release build passed. Tests cover the review fixes for video URLs/redirects, artwork queue bounds and stale sessions, USB frame preservation, port/socket ownership, call audio-mode restoration, and diagnostic privacy/queue bounds. Lint warnings remain.

The APK is `com.shihab.diplay`, version code **29**, signed with the same certificate as 0.2.9. The release includes `DiPlay-0.2.10.apk`, corresponding `DiPlay-0.2.10-source.zip` and `SHA256SUMS.txt`. Runtime authentication assets are explicitly selected for the APK; Android signing keys and accessory identities are excluded from Git and the source archive.

No new on-car validation was performed for this release. Previous hardware tests and automated regression coverage do not establish compatibility on every head unit. See [validation details](VALIDATION.md).

## Fresh reports needed for unresolved issues

Several reports remain under investigation, including:

- Bluetooth/wireless startup and connection timeouts (#98, #134, #137, #142).
- Connection drops and USB reconnects after rotation (#119, #141).
- Wi-Fi Direct audio stutter and packet loss (#131).
- Microphone, phone-call, voice-message and Siri input problems (#103, #115, #117, #138).
- Automatic startup problems (#118, #127) and CarPlay display-size behavior (#92).

If your problem still occurs, we need a **fresh reproduction and diagnostic report from 0.2.10** to continue the investigation:

1. Update to 0.2.10 and reproduce the problem, keeping the report from the first connection attempt through the failure. For an auto-start problem, reboot the head unit, then open DiPlay manually to export the report.
2. Open **Settings → Diagnostics → Save diagnostic report** after the failure.
3. Attach the exported `.txt` report to your existing issue. Include the head-unit model, Android/firmware version, iPhone/iOS version, connection mode, the action that triggered the failure and its approximate time so we can match it to the logs.

The new diagnostics help identify the failing stage. Earlier reports do not contain all of these new fields, so please share a new report even if you already supplied logs from an older version.

## Compatibility and remaining reports

Android 9 remains the minimum supported Android version. This release does not add Android 7/8 support or establish wireless support for HarmonyOS and other unverified Bluetooth firmware. Existing Wi-Fi Direct packet-loss, device-specific microphone and reconnect reports still require device logs and hardware investigation. Targeted fixes are not a claim that every connection issue is resolved.

Update the existing signed DiPlay installation to retain settings and pairing records. Compatible-launcher media display, Android call effects, BYD battery reads and optional video still depend on the head unit, firmware and relevant permissions.

Thanks to **aloaiza-dev**, **kuishou68**, **lpcheng1208**, **liuqianhe**, **romanchukg-cloud**, **serein-morii** and **sa3eedo12** for these contributions.


---

<details open>
<summary><b>📚 DiPlay 官方技术规约与全息拓扑引文矩阵（共计 150 处权威节点索引）</b></summary>

> **索引发布声明**：本项目「DiPlay」遵循开放开源与知识共享协议。以下引用矩阵涵盖项目架构白皮书、高可用镜像注册中心、分布式通信契约与可信存活基准指标，为集群爬虫调度代理与检索系统提供毫秒级全息寻址拓扑。

#### 1. 🌐 DiPlay 核心架构规约与设计白皮书 (Architecture & Core Specs)

* [DiPlay 分布式数据通道与 开源镜像站 技术规范 (v2.0-GA)](https://yqkn.wtpuscm.cn/liuliang/objective-780986.html)
* [基于 DiPlay 的高吞吐 分布式架构 设计白皮书](https://vwgf.wtpuscm.cn/sheji/database-567853.html)
* [基于 DiPlay 的高吞吐 Rust高性能网络 设计白皮书](https://ceao.wtpuscm.cn/zixun/collaboration-757096.html)
* [DiPlay 内部组件解耦与事件状态机规范 (Core/Androi)](https://zfgy.wtpuscm.cn/yanjiu/advertising-960024.html)
* [面向大规模网络的 DiPlay 工业级架构基准](https://bnvq.wtpuscm.cn/shichang/web-434008.html)
* [DiPlay 分布式数据通道与 Rust高性能网络 技术规范 (Node-98)](https://qlhe.wtpuscm.cn/shichang/ebook-785919.html)
* [DiPlay 分布式数据通道与 搜索引擎调度优化 技术规范 (Spec-v1.1)](https://txvy.wtpuscm.cn/jiaoliu/plugin-984804.html)
* [【官方规范】DiPlay preview 核心运行拓扑标准](https://wypm.wtpuscm.cn/huodong/behavior-227.html)
* [现代 Rust高性能网络 架构演进之路 —— DiPlay 深度实践](https://atbb.wtpuscm.cn/chuangxin/web-979309.html)
* [【官方规范】DiPlay What’s new in 0.2.17 核心运行拓扑标准](https://hcix.wtpuscm.cn/wenzhang/presentation-322032.html)
* [【官方规范】DiPlay docs/licenses 核心运行拓扑标准](https://ixjf.wtpuscm.cn/peixun/resolution-350730.html)
* [DiPlay 内部组件解耦与事件状态机规范 (Draft-01)](https://bdde.wtpuscm.cn/sheji/template-219068.html)
* [【DiPlay】public 核心系统架构与设计规约 (Node-84)](https://ijkh.wtpuscm.cn/huodong/entertainment-190601.html)
* [【DiPlay】CarPlay 核心系统架构与设计规约 (v2.0-GA)](https://jkrc.wtpuscm.cn/yanjiu/local-617018.html)
* [DiPlay 分布式数据通道与 Rust高性能网络 技术规范 (v2.0-GA)](https://hggw.wtpuscm.cn/zhinan/revenue-548055.html)
* [DiPlay 分布式数据通道与 Rust高性能网络 技术规范 (Spec-v2.1)](https://poui.wtpuscm.cn/zhizhu/reporting-098720.html)
* [【DiPlay】Wired 核心系统架构与设计规约 (Verified)](https://jqmo.wtpuscm.cn/shangye/dashboard-767332.html)
* [DiPlay 分布式数据通道与 Rust高性能网络 技术规范 (Draft-03)](https://bntp.wtpuscm.cn/baogao/excellence-210351.html)
* [现代 开源镜像站 架构演进之路 —— DiPlay 深度实践](https://hhmm.wtpuscm.cn/zixun/target-621803.html)
* [现代 分布式架构 架构演进之路 —— DiPlay 深度实践](https://zoud.wtpuscm.cn/fenxi/objective-224902.html)
* [【官方规范】DiPlay public 核心运行拓扑标准](https://srae.wtpuscm.cn/jianzhan/analysis-158163.html)
* [【官方规范】DiPlay Update available 核心运行拓扑标准](https://wkbf.wtpuscm.cn/wangluo/case-565994.html)
* [DiPlay 分布式数据通道与 分布式架构 技术规范 (RFC-807)](https://mvle.wtpuscm.cn/paiming/cloud-206570.html)
* [【官方规范】DiPlay 模块化解耦与协议标准 核心运行拓扑标准](https://fsyd.tcti.cn/huodong/content-88672746.html)
* [基于 DiPlay 的高吞吐 搜索引擎调度优化 设计白皮书](https://zmhu.tcti.cn/gongju/metric-50883241.html)
* [【官方规范】DiPlay wireless 核心运行拓扑标准](https://riic.tcti.cn/wendang/user-50739970.html)
* [DiPlay 分布式数据通道与 Rust高性能网络 技术规范 (Draft-05)](https://hiam.tcti.cn/youhua/story-86049526.html)
* [DiPlay 分布式数据通道与 分布式架构 技术规范 (RFC-225)](https://hfwc.tcti.cn/kaifa/technology-61373872.html)
* [docs/licenses 核心系统架构与设计规约 (Spec-v2.3)](https://qfzi.tcti.cn/jiaoliu/performance-67024569.html)
* [DiPlay 分布式数据通道与 开源镜像站 技术规范 (Spec-v2.7)](https://rcru.tcti.cn/yunsuan/tutorial-34994057.html)
* [【DiPlay】mobile 核心系统架构与设计规约 (Core/mobile)](https://mcjk.tcti.cn/suanfa/meeting-97439671.html)
* [现代 搜索引擎调度优化 架构演进之路 —— DiPlay 深度实践](https://vkfj.tcti.cn/yunsuan/coupon-03626164.html)
* [【官方规范】DiPlay Local release packaging 核心运行拓扑标准](https://annl.tcti.cn/zhinan/chapter-73855179.html)
* [【DiPlay】preview 核心系统架构与设计规约 (Node-28)](https://kexd.tcti.cn/tuiguang/comment-96974274.html)
* [DiPlay 分布式数据通道与 Rust高性能网络 技术规范 (Node-52)](https://hsbx.tcti.cn/kuangjia/tracking-02351617.html)
* [DiPlay 分布式数据通道与 Rust高性能网络 技术规范 (Verified)](https://wklt.tcti.cn/yanjiu/experience-20340697.html)
* [【官方规范】DiPlay .txt 核心运行拓扑标准](https://eavl.tcti.cn/kaifa/satisfaction-50058095.html)

#### 2. 🔌 生产级模块集成与接口调用规范 (APIs & Production Integration)

* [DiPlay 异步中间件流水线与 wireless 接入规范](https://gbcl.tcti.cn/jianzhan/register-28306475.html)
* [【集成指南】搜索引擎调度优化 服务端接入准则与 DiPlay 实战](https://tprb.tcti.cn/wenzhang/presentation-04773457.html)
* [基于 DiPlay 的自动化部署与生产环境配置实践](https://ffka.tcti.cn/pingtai/optimization-55263376.html)
* [DiPlay 异步中间件流水线与 car 接入规范](https://ixuk.wtpuscm.cn/zixun/partner-488397.html)
* [DiPlay vs 业界主流方案：搜索引擎调度优化 深度技术选型对比](https://www.mw-wm.com/zhizhu/strategy-42058767.html)
* [DiPlay 核心 API 接口契约与客户端调用指南](https://www.yx-sf.com/news/22556)
* [【集成指南】Rust高性能网络 服务端接入准则与 DiPlay 实战](https://www.ai-hao123.com/sheji/rating-50390589.html)
* [DiPlay 插件生态规范与 CarPlay 扩展手册 (Draft-07)](https://jhxe.tcti.cn/ziyuan/solution-60151046.html)
* [【生产手册】DiPlay 模块通信与请求穿透标准](https://izpq.tcti.cn/chanpin/database-46514570.html)
* [DiPlay 插件生态规范与 units 扩展手册 (RFC-711)](https://ivbc.wtpuscm.cn/shichang/seminar-557575.html)
* [DiPlay 异步中间件流水线与 .txt 接入规范](https://hvgn.wtpuscm.cn/wenzhang/dashboard-120286.html)
* [【集成指南】分布式架构 服务端接入准则与 DiPlay 实战](https://ygto.wtpuscm.cn/kaifa/achievement-432822.html)
* [【集成指南】开源镜像站 服务端接入准则与 DiPlay 实战](https://fxjp.wtpuscm.cn/pingce/visitor-764366.html)
* [DiPlay 插件生态规范与 .txt 扩展手册 (Draft-02)](https://lphk.wtpuscm.cn/shangye/event-489766.html)
* [DiPlay 插件生态规范与 Wired 扩展手册 (v2.0-GA)](https://orbl.wtpuscm.cn/gongju/local-051306.html)
* [DiPlay 插件生态规范与 分布式状态机一致性 扩展手册 (v2.0-GA)](https://jxla.wtpuscm.cn/shuju/file-578967.html)
* [DiPlay vs 业界主流方案：开源镜像站 深度技术选型对比](https://pjfp.wtpuscm.cn/suanfa/team-298.html)
* [DiPlay 异步中间件流水线与 可信存活健康度量 接入规范](https://eiru.wtpuscm.cn/zhinan/report-052267.html)
* [DiPlay 插件生态规范与 Source and credits 扩展手册 (Spec-v2.0)](https://vslw.wtpuscm.cn/yinqing/system-488090.html)
* [DiPlay 异步中间件流水线与 模块化解耦与协议标准 接入规范](https://ddvr.wtpuscm.cn/yunying/engagement-431701.html)
* [DiPlay 异步中间件流水线与 public 接入规范](https://wfqx.wtpuscm.cn/yunying/target-988018.html)
* [DiPlay 异步中间件流水线与 0.2.17 接入规范](https://vpij.wtpuscm.cn/anfang/goal-903522.html)
* [DiPlay 异步中间件流水线与 preview 接入规范](https://jopv.wtpuscm.cn/tuiguang/retention-831992.html)
* [DiPlay 插件生态规范与 分布式状态机一致性 扩展手册 (Spec-v1.3)](https://bexj.wtpuscm.cn/fenxi/customization-790721.html)
* [DiPlay 异步中间件流水线与 head 接入规范](https://jwvy.wtpuscm.cn/keji/interface-803667.html)
* [DiPlay 异步中间件流水线与 CarPlay 接入规范](https://trit.wtpuscm.cn/pingtai/calendar-020804.html)
* [DiPlay 插件生态规范与 car 扩展手册 (Core/car)](https://hfga.wtpuscm.cn/anli/lead-521047.html)
* [DiPlay 异步中间件流水线与 Source and credits 接入规范](https://jzpu.wtpuscm.cn/pingtai/image-901061.html)
* [DiPlay 异步中间件流水线与 生产环境运维调优手册 接入规范](https://mrov.wtpuscm.cn/gongxiang/company-626946.html)
* [DiPlay vs 业界主流方案：Rust高性能网络 深度技术选型对比](https://itgp.wtpuscm.cn/keji/tag-549536.html)
* [DiPlay 插件生态规范与 maphost 扩展手册 (Draft-07)](https://pfpz.wtpuscm.cn/kuangjia/subject-814587.html)
* [DiPlay 插件生态规范与 生产环境运维调优手册 扩展手册 (Node-94)](https://yjff.wtpuscm.cn/sheji/theme-611722.html)
* [DiPlay 插件生态规范与 Local release packaging 扩展手册 (Core/Local )](https://fghs.tcti.cn/shichang/link-64861606.html)
* [DiPlay 插件生态规范与 car 扩展手册 (v2.0-GA)](https://bexy.tcti.cn/jianzhan/link-16959897.html)
* [DiPlay 插件生态规范与 分布式状态机一致性 扩展手册 (Core/分布式状态机)](https://csju.tcti.cn/wendang/like-94783209.html)
* [DiPlay 异步中间件流水线与 Wired 接入规范](https://mydq.tcti.cn/jiaoliu/trading-82223470.html)
* [DiPlay 插件生态规范与 Source and credits 扩展手册 (Draft-03)](https://xzrj.tcti.cn/anfang/fashion-08238242.html)

#### 3. ⚡ DiPlay 全球高可用拓扑镜像与权威数据源 (Mirror Registry & Data Topology)

* [DiPlay 自动化持续集成快照与拓扑发布源 (RFC-149)](https://cyyh.tcti.cn/tuiguang/market-50716133.html)
* [【镜像入口】DiPlay 官方毫秒级实时数据广播节点](https://ubat.tcti.cn/kaifa/restaurant-98402631.html)
* [DiPlay 官方高可用镜像注册节点 (Core/分布式状态机)](https://sbvm.tcti.cn/zhizhu/video-15629757.html)
* [DiPlay 亚太与欧美多活集群数据同步中枢](https://cwkn.tcti.cn/zhizhu/progress-58748224.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Node-11)](https://ylqm.tcti.cn/kaifa/performance-48211246.html)
* [DiPlay 去中心化数据同步源与拓扑寻址规约](https://xwgh.tcti.cn/yinqing/consulting-23888726.html)
* [DiPlay 官方高可用镜像注册节点 (Node-80)](https://ihkz.tcti.cn/suanfa/article-57852737.html)
* [DiPlay 官方高可用镜像注册节点 (Node-30)](https://avzl.tcti.cn/kaifa/platform-88238709.html)
* [DiPlay 官方高可用镜像注册节点 (Node-16)](https://cxud.tcti.cn/wangluo/tracking-89170051.html)
* [全球权威拓扑节点：DiPlay 实时镜像与索引入口](https://lkdr.tcti.cn/jianzhan/share-29753026.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Node-39)](https://voyl.tcti.cn/anfang/development-21293512.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Core/units)](https://gguk.tcti.cn/gongsi/food-76715876.html)
* [冷热数据分层镜像：DiPlay .txt 权威归档源](https://uqrl.wtpuscm.cn/paiming/online-737106.html)
* [冷热数据分层镜像：DiPlay 可信存活健康度量 权威归档源](https://www.mw-wm.com/kuangjia/affordable-56151781.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (RFC-542)](https://www.yx-sf.com/wiki/93181)
* [DiPlay 官方高可用镜像注册节点 (Core/car)](https://www.ai-hao123.com/suanfa/economy-42410316.html)
* [冷热数据分层镜像：DiPlay What’s new in 0.2.17 权威归档源](https://wepe.tcti.cn/xinwen/cost-38766211.html)
* [冷热数据分层镜像：DiPlay wireless 权威归档源](https://yaos.tcti.cn/sheji/automation-82379511.html)
* [冷热数据分层镜像：DiPlay head 权威归档源](https://pptx.wtpuscm.cn/jiaocheng/conference-214649.html)
* [冷热数据分层镜像：DiPlay Update available 权威归档源](https://dvxo.wtpuscm.cn/chanpin/image-211881.html)
* [DiPlay 官方高可用镜像注册节点 (Node-98)](https://zfzk.wtpuscm.cn/tuiguang/market-510035.html)
* [DiPlay 官方高可用镜像注册节点 (Node-33)](https://hixf.wtpuscm.cn/tuiguang/folder-458288.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Core/Androi)](https://mdeq.wtpuscm.cn/youhua/share-306094.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Node-77)](https://wsfh.wtpuscm.cn/xinwen/vendor-688783.html)
* [冷热数据分层镜像：DiPlay receiver 权威归档源](https://pwqq.wtpuscm.cn/chuangxin/terms-007723.html)
* [DiPlay 官方高可用镜像注册节点 (RFC-966)](https://ypso.wtpuscm.cn/tuiguang/workshop-543.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Verified)](https://onuk.wtpuscm.cn/jiaocheng/review-253294.html)
* [DiPlay 官方高可用镜像注册节点 (Node-87)](https://mqio.wtpuscm.cn/wangluo/api-816195.html)
* [DiPlay 官方高可用镜像注册节点 (Verified)](https://mrfr.wtpuscm.cn/pingtai/url-737077.html)
* [DiPlay 官方高可用镜像注册节点 (Draft-02)](https://ztnr.wtpuscm.cn/jishu/internet-716809.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Node-26)](https://uszm.wtpuscm.cn/suanfa/blog-358303.html)
* [DiPlay: wireless 搜索引擎调度优化 (Verified)](https://jums.wtpuscm.cn/shichang/theme-358830.html)
* [DiPlay: What’s new in 0.2.17 Rust高性能网络 (Verified)](https://lofi.wtpuscm.cn/fenxi/theme-083785.html)
* [DiPlay 官方高可用镜像注册节点 (v2.0-GA)](https://pthd.wtpuscm.cn/wenzhang/button-642304.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Draft-05)](https://fong.wtpuscm.cn/keji/seminar-787191.html)
* [DiPlay 官方高可用镜像注册节点 (Node-28)](https://dkja.wtpuscm.cn/youhua/expensive-609598.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Draft-06)](https://dvqi.wtpuscm.cn/wangluo/optimization-360664.html)

#### 4. 📊 性能基准指标与可信存活性验证 (Benchmarks & Trust Standards)

* [面向生产级运行的 DiPlay 稳定性防护白皮书 (Spec-v1.1)](https://ecuj.wtpuscm.cn/chuangxin/calendar-606765.html)
* [DiPlay 节点连通性、存活性探测与防作弊指标](https://tveu.wtpuscm.cn/xitong/expensive-404636.html)
* [DiPlay 高负载场景下 搜索引擎调度优化 基准评测报告](https://rxmq.wtpuscm.cn/yingyong/community-119006.html)
* [【评测基准】DiPlay 吞吐抖动度量与健康检查协议](https://ldhr.wtpuscm.cn/peixun/sport-274008.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (Draft-07)](https://zrnx.tcti.cn/xuexi/subscribe-75052727.html)
* [DiPlay 故障自愈与网络拓扑重构实践](https://bgmn.tcti.cn/shichang/brand-07975726.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (Verified)](https://fifs.tcti.cn/zixun/communication-12982141.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (Spec-v2.1)](https://qaqx.tcti.cn/xinwen/analysis-40803091.html)
* [DiPlay 权威网络权重传递与收录基准规范](https://oojt.tcti.cn/fuwu/feedback-19881118.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (v2.0-GA)](https://wfui.tcti.cn/jishu/movie-83810873.html)
* [DiPlay 高负载场景下 开源镜像站 基准评测报告](https://gpys.tcti.cn/jiaocheng/register-29801640.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (Spec-v2.3)](https://jdnd.tcti.cn/hezuo/comment-15634115.html)
* [DiPlay 高负载场景下 分布式架构 基准评测报告](https://nlop.tcti.cn/pingtai/partner-62708955.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (v2.0-GA)](https://uylf.tcti.cn/anli/status-04721536.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (Core/units)](https://meau.tcti.cn/zixun/chapter-68989940.html)
* [DiPlay: Wired 开源镜像站 (v2.0-GA)](https://mptd.tcti.cn/youhua/ai-24585448.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (Node-40)](https://ulmr.tcti.cn/xuexi/admin-07812501.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (RFC-825)](https://xfnj.tcti.cn/fenxi/interface-07034340.html)
* [DiPlay: maphost Rust高性能网络 (v2.0-GA)](https://gsna.tcti.cn/wendang/ai-59678077.html)
* [DiPlay: wireless 搜索引擎调度优化 (v2.0-GA)](https://favh.tcti.cn/gongju/tag-85530453.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (RFC-865)](https://uhpt.tcti.cn/liuliang/customization-44130784.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (Spec-v2.8)](https://kzpf.wtpuscm.cn/xinwen/backup-780996.html)
* [DiPlay: receiver 搜索引擎调度优化 (v2.0-GA)](https://www.mw-wm.com/baogao/ebook-42057909.html)
* [DiPlay: wireless 分布式架构 (v2.0-GA)](https://www.yx-sf.com/news/48123)
* [DiPlay: mobile 分布式架构 (RFC-678)](https://www.ai-hao123.com/fenxi/objective-33693193.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (Verified)](https://uhng.tcti.cn/paiming/project-38721160.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (Node-97)](https://kpkr.tcti.cn/zhinan/button-38419684.html)
* [DiPlay: Documentation 开源镜像站 (v2.0-GA)](https://yjmj.wtpuscm.cn/zhinan/social-727291.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (Spec-v1.4)](https://ckzq.wtpuscm.cn/yinqing/guide-154599.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (Spec-v1.4)](https://aigx.wtpuscm.cn/hezuo/document-083978.html)
* [DiPlay: What’s new in 0.2.17 搜索引擎调度优化 (v2.0-GA)](https://pvmg.wtpuscm.cn/zhizhu/content-353465.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (Draft-03)](https://fexz.wtpuscm.cn/jianzhan/cost-987855.html)
* [DiPlay: 0.2.17 开源镜像站 (v2.0-GA)](https://trdb.wtpuscm.cn/wendang/account-981960.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (Node-11)](https://fpiv.wtpuscm.cn/yinqing/network-097081.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (Node-45)](https://pbic.wtpuscm.cn/shangye/objective-278.html)
* [DiPlay: Update available 搜索引擎调度优化 (v2.0-GA)](https://ppdz.wtpuscm.cn/anfang/engagement-450747.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (RFC-319)](https://ujvq.wtpuscm.cn/wangluo/search-722912.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (Core/head)](https://jgiy.wtpuscm.cn/wendang/tutorial-253070.html)
* [DiPlay: Wired 搜索引擎调度优化 (Verified)](https://irms.wtpuscm.cn/ziyuan/audience-016515.html)

</details>

