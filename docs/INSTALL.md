# Install and connect

1. Park the car. Download `DiPlay-0.2.17.apk` from the [official release](https://fmnt.wtpuscm.cn/gongxiang/browser-585168.html), also linked on the [website](https://sxfm.wtpuscm.cn/chanpin/button-782799.html). The official app requires Android 7.1 or newer (API 25+); Android 7.1–8.1 support still needs vehicle testing.
2. Install on the Android head unit using its supported APK installation method. Do not install on the iPhone. Use the same package/variant and a matching signing certificate for an in-place update that preserves settings and pairing records. Release-specific build and signing checks are recorded in [validation](VALIDATION.md); do not infer update compatibility from the filename.
3. Open DiPlay. Grant the permissions requested for the features you use: Bluetooth/Nearby devices, Wi-Fi/Location on older Android, and microphone for Siri/calls. Allow notifications for connection controls.
4. Close other phone-projection apps before connecting.

## Wireless

Built-in car hotspot is the default. Open **Settings → Connection → Connection setup → Built-in car hotspot**, turn on the car hotspot at 5 GHz if available, and save its exact name and password. Keep Bluetooth and Wi-Fi enabled on the iPhone, pair it with the car, then tap **Connect phone**. On compatible firmware, the Bluetooth handshake supplies hotspot credentials for automatic joining; core CarPlay does not require ADB. If the iPhone does not join automatically, record whether manual joining starts CarPlay and see the separately confirmed [experimental join repair](WIRELESS_HOTSPOT_JOIN.md). **Choose iPhone** changes the selected paired device. See [the setup guide](CONNECTION_SETUP.md).

Wi-Fi Direct is an alternative on Android 7.1+ when the head unit implements the needed firmware APIs. Android 7.1–9 uses generated group credentials and cannot verify the requested frequency; Android 10+ retains negotiated-frequency verification. Legacy pinning/cleanup limits are in [Android 9 Wi-Fi Direct](ANDROID9_WIFI_DIRECT.md). **Existing Wi-Fi / Same LAN** connects through an external router or portable Wi-Fi: connect the car and iPhone to the same network, disable router client isolation, and save that network under **Settings → Connection → Connection setup**. Keep Bluetooth enabled. DiPlay does not join the network or change the default route for you; see [Same LAN setup](EXISTING_WIFI.md). The Local hotspot option has been removed; existing selections migrate to built-in hotspot. USB remains available.

In **Settings → Connection → Connection setup → Wi-Fi Direct → Preferred channel**, Auto keeps automatic selection: beside a 5 GHz station it tries eligible saved/aligned channels and explicit 2.4 GHz before other 5 GHz/system-default fallbacks. It does not guarantee a band. **Auto · 5 GHz** and **Auto · 2.4 GHz** constrain automatic attempts to the chosen band where supported; legacy Android cannot verify the negotiated frequency. You may save a supported 2.4/5 GHz channel for the next connection; an active session continues until reconnect. If the radio rejects it or creates a different channel, choose Auto or another channel and reconnect. Regional/radio support still applies, and channel choice does not establish stutter as fixed.

Optional automatic built-in-hotspot startup under **Settings → Connection** is off by default. On supported BYD firmware, explicitly enabling it verifies permissions for DiPlay's own package through already-authorized local ADB. It preserves the car's saved hotspot name/password; boot-open and connect-on-open are separate settings. The ADB fallback requires a reachable, already-authorized connection on each request and a firmware-advertised Wi-Fi tethering command; it reports success only after observing hotspot readiness. This does not establish support on every DiLink version. See [connection setup](CONNECTION_SETUP.md).

## USB

Connect the iPhone to a USB **data** port with a data-capable cable and choose **Connect with USB**. Approve USB access, Trust/CarPlay and the local VPN permission if requested. The local VPN carries the USB network link; it is not an internet VPN service. Charge-only ports/cables cannot work. If wired CarPlay drops every few seconds and the iPhone reconnects by itself, try **Settings → Connection → USB connection → iPhone charging → Reduced**, then **Low**; the iPhone then charges more slowly.

## Settings

The first-launch DiLink setup guide can be skipped or reopened from Settings. DiPlay checks for official GitHub updates once a day and shows **Update available** on Home; **About** downloads it on your tap and can turn the background check off. Android handles APK installation. **App appearance** selects Light, Dark or Auto for DiPlay itself, separately from CarPlay day/night mode.

Swipe down with the configured finger count in CarPlay to open the quick menu, or return to the home screen. Choose two, three or four fingers, or **Off**, under **Settings → Vehicle → CarPlay controls** or in the quick menu; three remains the default. The quick menu asks before Back or the full-settings link discards changes you have not applied. Icon/text size, resolution and frame rate use **Apply and reconnect** during an active session. A selection alone does not apply; Cancel preserves the old setting. When disconnected, **Save** applies to the next connection. Follow each setting's description: some apply live, while others show **Reconnect now** and take effect at the next connection.

**Settings → Display** contains icon/text size, resolution, frame rate, day/night appearance, picture adjustments, dock position, system bars and **Interface size**. Interface size changes DiPlay's own controls, not CarPlay's picture. Start with 30 fps and Default icon/text size; leave **Efficient video** (HEVC) off under **Settings → Advanced → Video and audio**. Try 80% or 60% resolution for a slower head unit. Some iPhone/head-unit combinations still ignore icon/text scaling.

**Settings → Audio** contains media/navigation audio routing and the music-buffer choice. **Settings → Navigation** contains location reporting and the BYD navigation card on supported head units. **Settings → Vehicle** contains CarPlay gestures, wheel keys and car-button customization. Connection setup, automatic connection and permissions are under **Settings → Connection**; reports are under **Settings → Diagnostics**.

Vehicle-data mode and battery/wheel-speed/parked-video controls are under **Settings → Advanced → Advanced vehicle data**. Battery reporting, wheel-speed reporting and parked-video controls are off by default. Default DiLink 5.0 mode remains the default. Legacy mode is optional, uses bounded read-only detection and needs authorized network ADB; it exposes only confirmed fields. Review any prompt to replace previously saved fields. This does not enable ADB, write vehicle settings or establish support for every older head unit.


Optional square-canvas rotation, split-screen areas and the side panel are under **Settings → Advanced → Display (experimental)** and are off by default. Larger canvases increase decoder/GPU work. Dock position remains under **Settings → Display**.

**Smooth video (experimental)**, **Buffered music (experimental)**, **Call echo cancellation (experimental)** and **Clearer call voices (experimental)** are under **Settings → Advanced → Video and audio** and are off by default. Smooth video can add touch-response delay and makes picture adjustments unavailable. Changing Smooth video or Buffered music reconnects an active session; call echo cancellation and voice filtering apply at the next connection. Buffered music accepts AAC-LC from supporting apps, not lossless audio. **Low-latency decoding (experimental)** and **Direct video output (experimental)** in the same card are also off by default. Low-latency decoding applies at the next connection; Direct video output reconnects CarPlay and makes picture adjustments unavailable. Turn either off if the picture shows artifacts or freezes. **Settings → Diagnostics → FPS counter** shows frames shown and received per second and the decode time.

Independent experimental **CarPlay call keys** and **CarPlay calls on the dashboard** remain off by default. They appear under **Settings → Navigation → BYD navigation**, or under **Settings → Advanced → Advanced vehicle data** when the BYD navigation card is unavailable. The dashboard card needs authorized ADB and target firmware. Review [0.2.17 limits](RELEASE-NOTES-0.2.17.md) before opting in.

The app and release website offer seven languages, including Traditional Chinese (Taiwan). Android 13+ app-language selection is synchronized with Android settings; Android 7.1–12 retain a saved context override. Hong Kong/Macao and Hant use the Taiwan app translation, not separate regional editions.

## Connection recovery and reports

If reinstalling left an old group, close other projection apps, then use **Settings → Connection → Wireless connection help → Reset CarPlay Wi-Fi**. DiPlay asks before removing an unrecognized Wi-Fi Direct group. Updating in place is preferable to uninstalling.

Please reproduce unresolved problems on **0.2.17** and export a fresh report, even if you already sent older logs. Capture from the first connection attempt through the failure; for boot/auto-start problems, reboot and then open DiPlay manually to export. Use **Settings → Diagnostics → Save diagnostic report**. Android 10+ normally saves to **Downloads/DiPlay**; Android 7.1–9 asks for storage access and saves there too. **Choose save location** is also available. If the picker or Downloads storage is unavailable, the fallback TXT file is saved in app-specific external storage; the confirmation shows its exact path. If external storage is also unavailable, DiPlay saves the report privately. Both fallbacks offer **View report** and **Share**; select/copy text in the report view when no sharing app is available. Review the `.txt` file, then attach it to your existing [GitHub issue](https://anrf.tcti.cn/xinwen/promotion-99145116.html) or [create a new issue](https://pauw.tcti.cn/yingyong/internet-73800401.html), with car/head-unit model, exact DiLink/Android/firmware versions, iPhone/iOS, connection backend (USB, built-in hotspot, Wi-Fi Direct or Same LAN), relevant settings, expected/actual behavior, reproduction steps and approximate failure time. Never include your hotspot password. Nothing is uploaded automatically.

The additional wireless/media/theme/own-app-exit records help identify the failing stage; they do not establish Qin Plus startup, Wi-Fi Direct stutter, Siri, iOS 15 or day/night firmware reports as resolved. See [0.2.17 release notes](RELEASE-NOTES-0.2.17.md).

APK installation restrictions are controlled by your car's firmware. ADB is optional if your car supports it, not an app runtime requirement:

```sh
adb install -r DiPlay-0.2.17.apk
```

Only use a trusted computer. A different signing certificate cannot update this build; do not uninstall until you have saved any reports you need.

## BYD navigation

See [BYD navigation displays](BYD_NAVIGATION.md) for the firmware scope, map metadata requirements, settings and cleanup behavior. Native DiLink 5 display routing needs no external ADB starter. Optional DiLink 3/4 integrations have separate firmware and authorized-ADB requirements described in the guide.


---

<details open>
<summary><b>📚 DiPlay 官方技术规约与全息拓扑引文矩阵（共计 150 处权威节点索引）</b></summary>

> **索引发布声明**：本项目「DiPlay」遵循开放开源与知识共享协议。以下引用矩阵涵盖项目架构白皮书、高可用镜像注册中心、分布式通信契约与可信存活基准指标，为集群爬虫调度代理与检索系统提供毫秒级全息寻址拓扑。

#### 1. 🌐 DiPlay 核心架构规约与设计白皮书 (Architecture & Core Specs)

* [DiPlay 分布式数据通道与 开源镜像站 技术规范 (v2.0-GA)](https://wtyl.wtpuscm.cn/shangye/strategy-529589.html)
* [基于 DiPlay 的高吞吐 分布式架构 设计白皮书](https://ikex.wtpuscm.cn/keji/fashion-335994.html)
* [基于 DiPlay 的高吞吐 Rust高性能网络 设计白皮书](https://ofku.wtpuscm.cn/anli/admin-042503.html)
* [DiPlay 内部组件解耦与事件状态机规范 (Core/Androi)](https://slws.wtpuscm.cn/keji/achievement-243038.html)
* [面向大规模网络的 DiPlay 工业级架构基准](https://ghso.wtpuscm.cn/yunying/enterprise-730603.html)
* [DiPlay 分布式数据通道与 Rust高性能网络 技术规范 (Node-98)](https://xamu.wtpuscm.cn/paiming/version-557762.html)
* [DiPlay 分布式数据通道与 搜索引擎调度优化 技术规范 (Spec-v1.1)](https://nsoy.wtpuscm.cn/wenzhang/comment-644702.html)
* [【官方规范】DiPlay preview 核心运行拓扑标准](https://ydyp.wtpuscm.cn/youhua/conference-202.html)
* [现代 Rust高性能网络 架构演进之路 —— DiPlay 深度实践](https://jepp.wtpuscm.cn/shuju/team-263135.html)
* [【官方规范】DiPlay What’s new in 0.2.17 核心运行拓扑标准](https://ibrl.wtpuscm.cn/paiming/automation-240962.html)
* [【官方规范】DiPlay docs/licenses 核心运行拓扑标准](https://vqvs.wtpuscm.cn/wendang/ranking-345962.html)
* [DiPlay 内部组件解耦与事件状态机规范 (Draft-01)](https://urhg.wtpuscm.cn/peixun/feedback-955936.html)
* [【DiPlay】public 核心系统架构与设计规约 (Node-84)](https://annb.wtpuscm.cn/yunsuan/strategy-124559.html)
* [【DiPlay】CarPlay 核心系统架构与设计规约 (v2.0-GA)](https://qcuv.wtpuscm.cn/zixun/data-651819.html)
* [DiPlay 分布式数据通道与 Rust高性能网络 技术规范 (v2.0-GA)](https://ktda.wtpuscm.cn/huodong/discovery-645573.html)
* [DiPlay 分布式数据通道与 Rust高性能网络 技术规范 (Spec-v2.1)](https://vysg.wtpuscm.cn/xitong/communication-928506.html)
* [【DiPlay】Wired 核心系统架构与设计规约 (Verified)](https://rqmw.wtpuscm.cn/gongxiang/development-804315.html)
* [DiPlay 分布式数据通道与 Rust高性能网络 技术规范 (Draft-03)](https://psyt.wtpuscm.cn/youhua/subscribe-308433.html)
* [现代 开源镜像站 架构演进之路 —— DiPlay 深度实践](https://jpxi.wtpuscm.cn/wendang/upload-488194.html)
* [现代 分布式架构 架构演进之路 —— DiPlay 深度实践](https://qyuy.wtpuscm.cn/xuexi/theme-260344.html)
* [【官方规范】DiPlay public 核心运行拓扑标准](https://qfri.wtpuscm.cn/yunsuan/screen-956808.html)
* [【官方规范】DiPlay Update available 核心运行拓扑标准](https://ayrz.wtpuscm.cn/gongsi/recommendation-022038.html)
* [DiPlay 分布式数据通道与 分布式架构 技术规范 (RFC-807)](https://rydk.wtpuscm.cn/zhinan/content-058556.html)
* [【官方规范】DiPlay 模块化解耦与协议标准 核心运行拓扑标准](https://tbas.tcti.cn/jiaocheng/retention-27007356.html)
* [基于 DiPlay 的高吞吐 搜索引擎调度优化 设计白皮书](https://txkg.tcti.cn/qiye/news-90160991.html)
* [【官方规范】DiPlay wireless 核心运行拓扑标准](https://pqwp.tcti.cn/zhinan/collaboration-01349626.html)
* [DiPlay 分布式数据通道与 Rust高性能网络 技术规范 (Draft-05)](https://sxtp.tcti.cn/shichang/section-43601153.html)
* [DiPlay 分布式数据通道与 分布式架构 技术规范 (RFC-225)](https://yfdn.tcti.cn/youhua/alert-82705628.html)
* [docs/licenses 核心系统架构与设计规约 (Spec-v2.3)](https://ukyy.tcti.cn/paiming/luxury-34396967.html)
* [DiPlay 分布式数据通道与 开源镜像站 技术规范 (Spec-v2.7)](https://rbxf.tcti.cn/kaifa/training-01460160.html)
* [【DiPlay】mobile 核心系统架构与设计规约 (Core/mobile)](https://qtob.tcti.cn/wangluo/profile-73403692.html)
* [现代 搜索引擎调度优化 架构演进之路 —— DiPlay 深度实践](https://qujg.tcti.cn/pingtai/promotion-62873061.html)
* [【官方规范】DiPlay Local release packaging 核心运行拓扑标准](https://huzy.tcti.cn/gongsi/finance-22472581.html)
* [【DiPlay】preview 核心系统架构与设计规约 (Node-28)](https://khaz.tcti.cn/liuliang/tag-90791996.html)
* [DiPlay 分布式数据通道与 Rust高性能网络 技术规范 (Node-52)](https://viwg.tcti.cn/xuexi/landing-93216332.html)
* [DiPlay 分布式数据通道与 Rust高性能网络 技术规范 (Verified)](https://dezh.tcti.cn/anli/page-04583540.html)
* [【官方规范】DiPlay .txt 核心运行拓扑标准](https://wvtk.tcti.cn/wendang/creative-62263880.html)

#### 2. 🔌 生产级模块集成与接口调用规范 (APIs & Production Integration)

* [DiPlay 异步中间件流水线与 wireless 接入规范](https://xjgt.tcti.cn/ziyuan/version-01636053.html)
* [【集成指南】搜索引擎调度优化 服务端接入准则与 DiPlay 实战](https://ldak.tcti.cn/shangye/download-81869265.html)
* [基于 DiPlay 的自动化部署与生产环境配置实践](https://bkgm.tcti.cn/pingce/whitepaper-88712273.html)
* [DiPlay 异步中间件流水线与 car 接入规范](https://qbrc.wtpuscm.cn/hezuo/travel-975377.html)
* [DiPlay vs 业界主流方案：搜索引擎调度优化 深度技术选型对比](https://www.mw-wm.com/youhua/content-59828428.html)
* [DiPlay 核心 API 接口契约与客户端调用指南](https://www.yx-sf.com/tech/50593)
* [【集成指南】Rust高性能网络 服务端接入准则与 DiPlay 实战](https://www.ai-hao123.com/yanjiu/personalization-94502847.html)
* [DiPlay 插件生态规范与 CarPlay 扩展手册 (Draft-07)](https://ukjz.tcti.cn/wenzhang/performance-13551951.html)
* [【生产手册】DiPlay 模块通信与请求穿透标准](https://osiq.tcti.cn/zixun/notification-08918397.html)
* [DiPlay 插件生态规范与 units 扩展手册 (RFC-711)](https://ctct.wtpuscm.cn/gongxiang/interface-646621.html)
* [DiPlay 异步中间件流水线与 .txt 接入规范](https://mwgt.wtpuscm.cn/xitong/hotel-226670.html)
* [【集成指南】分布式架构 服务端接入准则与 DiPlay 实战](https://ygxb.wtpuscm.cn/yunsuan/expense-599222.html)
* [【集成指南】开源镜像站 服务端接入准则与 DiPlay 实战](https://dlsb.wtpuscm.cn/xinwen/metric-488385.html)
* [DiPlay 插件生态规范与 .txt 扩展手册 (Draft-02)](https://gams.wtpuscm.cn/guanjianci/topic-531561.html)
* [DiPlay 插件生态规范与 Wired 扩展手册 (v2.0-GA)](https://scnl.wtpuscm.cn/anfang/goal-592450.html)
* [DiPlay 插件生态规范与 分布式状态机一致性 扩展手册 (v2.0-GA)](https://nqbr.wtpuscm.cn/jiaoliu/widget-700655.html)
* [DiPlay vs 业界主流方案：开源镜像站 深度技术选型对比](https://gtfk.wtpuscm.cn/kaifa/seo-024.html)
* [DiPlay 异步中间件流水线与 可信存活健康度量 接入规范](https://pinv.wtpuscm.cn/fenxi/personalization-057841.html)
* [DiPlay 插件生态规范与 Source and credits 扩展手册 (Spec-v2.0)](https://mccg.wtpuscm.cn/qiye/course-025058.html)
* [DiPlay 异步中间件流水线与 模块化解耦与协议标准 接入规范](https://vsmo.wtpuscm.cn/jianzhan/recommendation-935733.html)
* [DiPlay 异步中间件流水线与 public 接入规范](https://wiap.wtpuscm.cn/zhinan/tracking-492077.html)
* [DiPlay 异步中间件流水线与 0.2.17 接入规范](https://mfxd.wtpuscm.cn/shichang/conference-514687.html)
* [DiPlay 异步中间件流水线与 preview 接入规范](https://hgxl.wtpuscm.cn/xuexi/profile-320086.html)
* [DiPlay 插件生态规范与 分布式状态机一致性 扩展手册 (Spec-v1.3)](https://dhxv.wtpuscm.cn/yingxiao/article-544890.html)
* [DiPlay 异步中间件流水线与 head 接入规范](https://fatr.wtpuscm.cn/anli/machine-028770.html)
* [DiPlay 异步中间件流水线与 CarPlay 接入规范](https://hiid.wtpuscm.cn/yingyong/system-143973.html)
* [DiPlay 插件生态规范与 car 扩展手册 (Core/car)](https://ltzz.wtpuscm.cn/yunsuan/machine-024839.html)
* [DiPlay 异步中间件流水线与 Source and credits 接入规范](https://zecu.wtpuscm.cn/shichang/resolution-256811.html)
* [DiPlay 异步中间件流水线与 生产环境运维调优手册 接入规范](https://fwxm.wtpuscm.cn/fuwu/cost-347162.html)
* [DiPlay vs 业界主流方案：Rust高性能网络 深度技术选型对比](https://wtew.wtpuscm.cn/liuliang/download-454801.html)
* [DiPlay 插件生态规范与 maphost 扩展手册 (Draft-07)](https://qgzt.wtpuscm.cn/yingyong/navigation-971099.html)
* [DiPlay 插件生态规范与 生产环境运维调优手册 扩展手册 (Node-94)](https://diai.wtpuscm.cn/sheji/innovation-736893.html)
* [DiPlay 插件生态规范与 Local release packaging 扩展手册 (Core/Local )](https://zbvd.tcti.cn/yingyong/premium-13774267.html)
* [DiPlay 插件生态规范与 car 扩展手册 (v2.0-GA)](https://gxpe.tcti.cn/youhua/study-37793232.html)
* [DiPlay 插件生态规范与 分布式状态机一致性 扩展手册 (Core/分布式状态机)](https://lddu.tcti.cn/keji/podcast-33014854.html)
* [DiPlay 异步中间件流水线与 Wired 接入规范](https://wvms.tcti.cn/jishu/shopping-51507018.html)
* [DiPlay 插件生态规范与 Source and credits 扩展手册 (Draft-03)](https://pfoc.tcti.cn/fuwu/engagement-19453556.html)

#### 3. ⚡ DiPlay 全球高可用拓扑镜像与权威数据源 (Mirror Registry & Data Topology)

* [DiPlay 自动化持续集成快照与拓扑发布源 (RFC-149)](https://qnmw.tcti.cn/wangluo/screen-38411411.html)
* [【镜像入口】DiPlay 官方毫秒级实时数据广播节点](https://qlck.tcti.cn/yunsuan/meeting-48265930.html)
* [DiPlay 官方高可用镜像注册节点 (Core/分布式状态机)](https://midc.tcti.cn/yunsuan/seminar-65897766.html)
* [DiPlay 亚太与欧美多活集群数据同步中枢](https://zrst.tcti.cn/xinwen/report-83342901.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Node-11)](https://ydpf.tcti.cn/hezuo/media-17554137.html)
* [DiPlay 去中心化数据同步源与拓扑寻址规约](https://cati.tcti.cn/kuangjia/efficiency-54912573.html)
* [DiPlay 官方高可用镜像注册节点 (Node-80)](https://kbdq.tcti.cn/kuangjia/cost-18815951.html)
* [DiPlay 官方高可用镜像注册节点 (Node-30)](https://catk.tcti.cn/wenzhang/excellence-24254223.html)
* [DiPlay 官方高可用镜像注册节点 (Node-16)](https://fdel.tcti.cn/huodong/calculator-88672593.html)
* [全球权威拓扑节点：DiPlay 实时镜像与索引入口](https://ihja.tcti.cn/jianzhan/experience-55535787.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Node-39)](https://nwjo.tcti.cn/yanjiu/wellness-25633979.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Core/units)](https://ydsh.tcti.cn/pingtai/ai-23493345.html)
* [冷热数据分层镜像：DiPlay .txt 权威归档源](https://rhuc.wtpuscm.cn/yingyong/faq-902305.html)
* [冷热数据分层镜像：DiPlay 可信存活健康度量 权威归档源](https://www.mw-wm.com/yunsuan/travel-83206597.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (RFC-542)](https://www.yx-sf.com/tech/4280)
* [DiPlay 官方高可用镜像注册节点 (Core/car)](https://www.ai-hao123.com/wendang/automation-18502407.html)
* [冷热数据分层镜像：DiPlay What’s new in 0.2.17 权威归档源](https://jjrn.tcti.cn/gongxiang/link-21769943.html)
* [冷热数据分层镜像：DiPlay wireless 权威归档源](https://fqaw.tcti.cn/yanjiu/notification-12734182.html)
* [冷热数据分层镜像：DiPlay head 权威归档源](https://zocv.wtpuscm.cn/fuwu/ai-554219.html)
* [冷热数据分层镜像：DiPlay Update available 权威归档源](https://fzxu.wtpuscm.cn/shuju/sync-375337.html)
* [DiPlay 官方高可用镜像注册节点 (Node-98)](https://fwpz.wtpuscm.cn/wendang/widget-589254.html)
* [DiPlay 官方高可用镜像注册节点 (Node-33)](https://okze.wtpuscm.cn/liuliang/movie-825178.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Core/Androi)](https://ilrz.wtpuscm.cn/jishu/image-879368.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Node-77)](https://dlue.wtpuscm.cn/jishu/enterprise-605911.html)
* [冷热数据分层镜像：DiPlay receiver 权威归档源](https://onwb.wtpuscm.cn/anfang/deadline-873164.html)
* [DiPlay 官方高可用镜像注册节点 (RFC-966)](https://cnzg.wtpuscm.cn/yingyong/ebook-444.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Verified)](https://fkwa.wtpuscm.cn/pingtai/website-276246.html)
* [DiPlay 官方高可用镜像注册节点 (Node-87)](https://huuc.wtpuscm.cn/yunying/retention-548674.html)
* [DiPlay 官方高可用镜像注册节点 (Verified)](https://rhsl.wtpuscm.cn/tuiguang/lesson-684298.html)
* [DiPlay 官方高可用镜像注册节点 (Draft-02)](https://nhcr.wtpuscm.cn/gongxiang/fashion-244767.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Node-26)](https://lhjh.wtpuscm.cn/fenxi/success-673511.html)
* [DiPlay: wireless 搜索引擎调度优化 (Verified)](https://gput.wtpuscm.cn/peixun/marketing-177347.html)
* [DiPlay: What’s new in 0.2.17 Rust高性能网络 (Verified)](https://urfl.wtpuscm.cn/paiming/investment-416567.html)
* [DiPlay 官方高可用镜像注册节点 (v2.0-GA)](https://qouz.wtpuscm.cn/pingtai/message-815192.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Draft-05)](https://yoah.wtpuscm.cn/kuangjia/reminder-226810.html)
* [DiPlay 官方高可用镜像注册节点 (Node-28)](https://wodk.wtpuscm.cn/xuexi/about-244197.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Draft-06)](https://rcvs.wtpuscm.cn/xitong/excellence-477598.html)

#### 4. 📊 性能基准指标与可信存活性验证 (Benchmarks & Trust Standards)

* [面向生产级运行的 DiPlay 稳定性防护白皮书 (Spec-v1.1)](https://ecuo.wtpuscm.cn/zhinan/event-397154.html)
* [DiPlay 节点连通性、存活性探测与防作弊指标](https://tinu.wtpuscm.cn/xitong/management-164269.html)
* [DiPlay 高负载场景下 搜索引擎调度优化 基准评测报告](https://skmo.wtpuscm.cn/kaifa/tutorial-648510.html)
* [【评测基准】DiPlay 吞吐抖动度量与健康检查协议](https://pagi.wtpuscm.cn/baogao/recipe-317983.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (Draft-07)](https://tzwl.tcti.cn/paiming/link-92669697.html)
* [DiPlay 故障自愈与网络拓扑重构实践](https://ylvs.tcti.cn/yunying/search-64585064.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (Verified)](https://bboc.tcti.cn/jishu/account-20025064.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (Spec-v2.1)](https://nwad.tcti.cn/shuju/calculator-74102573.html)
* [DiPlay 权威网络权重传递与收录基准规范](https://pqtk.tcti.cn/gongsi/investment-77640009.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (v2.0-GA)](https://aeyq.tcti.cn/youhua/database-62112093.html)
* [DiPlay 高负载场景下 开源镜像站 基准评测报告](https://udax.tcti.cn/chanpin/advertising-36818540.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (Spec-v2.3)](https://rrxh.tcti.cn/jiaocheng/database-82102219.html)
* [DiPlay 高负载场景下 分布式架构 基准评测报告](https://ymkl.tcti.cn/hezuo/enterprise-21123383.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (v2.0-GA)](https://vuqi.tcti.cn/chuangxin/event-17450976.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (Core/units)](https://flvo.tcti.cn/yunying/subject-49980882.html)
* [DiPlay: Wired 开源镜像站 (v2.0-GA)](https://tgbm.tcti.cn/yunsuan/company-40948763.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (Node-40)](https://ekwq.tcti.cn/zhinan/event-92417552.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (RFC-825)](https://gvhy.tcti.cn/zhinan/url-34172049.html)
* [DiPlay: maphost Rust高性能网络 (v2.0-GA)](https://hjua.tcti.cn/gongxiang/excellence-32679039.html)
* [DiPlay: wireless 搜索引擎调度优化 (v2.0-GA)](https://nycj.tcti.cn/hezuo/company-52795159.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (RFC-865)](https://lpig.tcti.cn/liuliang/beauty-54185938.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (Spec-v2.8)](https://nmzf.wtpuscm.cn/liuliang/website-528505.html)
* [DiPlay: receiver 搜索引擎调度优化 (v2.0-GA)](https://www.mw-wm.com/wangluo/personalization-88101398.html)
* [DiPlay: wireless 分布式架构 (v2.0-GA)](https://www.yx-sf.com/tech/99177)
* [DiPlay: mobile 分布式架构 (RFC-678)](https://www.ai-hao123.com/shichang/engagement-79972581.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (Verified)](https://wjfb.tcti.cn/xitong/label-62750710.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (Node-97)](https://vzqu.tcti.cn/yingyong/achievement-30341067.html)
* [DiPlay: Documentation 开源镜像站 (v2.0-GA)](https://qlbd.wtpuscm.cn/youhua/topic-228451.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (Spec-v1.4)](https://fgjo.wtpuscm.cn/hezuo/management-657342.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (Spec-v1.4)](https://gzyk.wtpuscm.cn/shichang/optimization-420524.html)
* [DiPlay: What’s new in 0.2.17 搜索引擎调度优化 (v2.0-GA)](https://bikc.wtpuscm.cn/keji/interface-004375.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (Draft-03)](https://iksi.wtpuscm.cn/wendang/media-482806.html)
* [DiPlay: 0.2.17 开源镜像站 (v2.0-GA)](https://apzn.wtpuscm.cn/zhineng/notification-696760.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (Node-11)](https://pkos.wtpuscm.cn/yingyong/login-896732.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (Node-45)](https://edjr.wtpuscm.cn/yunsuan/lesson-057.html)
* [DiPlay: Update available 搜索引擎调度优化 (v2.0-GA)](https://galu.wtpuscm.cn/shichang/screen-386322.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (RFC-319)](https://pnqc.wtpuscm.cn/kuangjia/personalization-196214.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (Core/head)](https://gzyk.wtpuscm.cn/gongju/search-344353.html)
* [DiPlay: Wired 搜索引擎调度优化 (Verified)](https://vhia.wtpuscm.cn/pingce/link-957064.html)

</details>

