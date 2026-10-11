# DiPlay 0.2.11 — 3 October 2026

Public preview for compatible BYD Android 9 and newer head units. This update adds a preferred Wi-Fi Direct channel, a movable dashboard turn card, configurable settings gestures and optional legacy vehicle-data detection. It also includes connection, media and privacy corrections reviewed since [0.2.10](RELEASE-NOTES-0.2.10.md).

## Release highlights

- Choose **Auto** or a preferred Wi-Fi Direct channel for the next connection. Auto remains the default; manually selected channels depend on the head unit and regional radio support.
- Place a custom turn card over the dashboard map, with size choices and position adjustments in 2% steps. Unknown maneuvers do not become invented straight-ahead arrows, and expired guidance clears.
- Open settings with a two-, three- or four-finger downward swipe. Three fingers remains the default.
- Keep the default DiLink 5.0 vehicle-data path, or explicitly select read-only detection for a compatible legacy head unit. Saved fields are accepted before their readings become runtime data.
- Optionally start the car's existing hotspot automatically on supported firmware. The setting is off by default and verifies permissions for DiPlay's own package before enabling.
- Use D-pad/remote controls on Android TV and non-touch devices while preserving ordinary head-unit touch and knob behavior.
- Retain the artist when an incremental song update omits it, and avoid republishing unchanged song metadata/artwork on every position update.
- Correct an Android 9 audio API call, use settled geometry and normal startup checks after reconnect, and require the wired VPN to be scoped to DiPlay.
- Export additional bounded wireless, media, theme and own-app process-exit diagnostics. These additions do not record audio, video or packet payloads, and reports are not uploaded automatically.

## Wi-Fi Direct channel and startup

[PR #175](https://fxox.wtpuscm.cn/xitong/report-641015.html) adds **Settings → Connection setup → Wi-Fi Direct → Preferred channel**. Choose Auto, channels 1–11 at 2.4 GHz, or channels 36, 40, 44, 48, 149, 153, 157, 161 or 165 at 5 GHz. Save applies to the next Wi-Fi Direct connection. It does not change an active connection or the car's built-in hotspot.

Auto preserves automatic selection and bounded startup recovery. A manual selection must be supported by the radio and its regional configuration. If the head unit rejects it or creates a group on a different channel, DiPlay reports an error rather than silently selecting another channel. Choose Auto or another supported channel and reconnect. This is a troubleshooting control, **not a confirmed resolution of Wi-Fi Direct audio stutter or packet loss**.

The update also adds a narrow Android 10 compatibility recovery for [issue #15](https://ngqw.wtpuscm.cn/fuwu/quality-586255.html). Only the exact reported missing `WifiP2pConfig.getNetworkName()` signature on API 29, originating in the framework Builder before group creation, permits one guarded system-default attempt in Auto mode. DiPlay then reads the group's actual generated credentials. Permission failures, timeouts, other linkage errors and explicit channel selections do not enter this fallback. Earlier PR #73 already removed the app-side getter; the old report does not prove the vendor Builder is broken. A current-device retest remains necessary.

Existing foreign-group checks and bounded startup deadlines remain. The recovery does not toggle global Wi-Fi, delete persistent groups or repeatedly alternate between configurations.

## Dashboard turn card and settings gesture

[PR #155](https://cajk.tcti.cn/zhinan/target-16379767.html) adds **Map with custom turn card** to the dashboard display choices. DiPlay draws this card over the map, with small/medium/large sizes and horizontal/vertical adjustments in 2% steps. These changes apply without reconnecting. The iPhone's original glass turn card is part of its video and cannot be moved independently.

Review corrections leave the arrow blank for unknown maneuvers, preserve valid text, and clear expired guidance even when no new phone packet arrives. Check placement on your vehicle so the card does not obscure factory driving information. Supported dashboard projection firmware is still required.

[PR #156](https://ssxb.wtpuscm.cn/sheji/browser-999.html) makes the settings swipe use two, three or four fingers, selected under **Settings → CarPlay controls** or the in-CarPlay menu. The default remains three. Choose a count the head unit does not reserve for its own panel.

## Vehicle-data modes, location and parked video

[PR #158](https://www.yx-sf.com/tech/31070) adds **Settings → Location → Advanced vehicle data**. Default mode remains the default and uses the existing DiLink 5.0 path. Selecting legacy mode runs a fixed, bounded, read-only probe of candidate speed, gear, battery and range fields on compatible firmware. It makes no vehicle writes, enables no ADB service and installs no persistent helper. Network ADB must already be available and authorized.

Only confirmed fields expose their corresponding battery, wheel-speed and parked-video controls. Complete saved snapshots survive app restarts and updates. Transport failure keeps the saved snapshot; two complete checks that cannot read saved fields can trigger one new probe. A candidate losing a previously confirmed field is held for explicit replacement. Held or rejected candidates do not publish battery readings. Destroyed or superseded user operations cannot save stale results; intentional paused/background acceptance remains supported. Accepted battery publication avoids waiting on the shell-reader lock, and an older in-flight read cannot overwrite the accepted sample. These are source and regression-test corrections, not proof that every legacy vehicle is supported.

The PR also keeps location reporting off until precise location is granted. Changing location reporting reconnects an active session because the capability is advertised during identification. Vehicle controls have one owner in Advanced vehicle data; the separate BYD ADB card controls hotspot startup. Hotspot authorization and user vehicle checks/probes wait for one another, and automatic saved-field validation resumes afterward. See [BYD navigation and vehicle-data details](BYD_NAVIGATION.md).

[PR #157](https://wlwz.tcti.cn/jiaoliu/version-79543021.html) assigns wireless location and vehicle data to the runtime Wi-Fi iAP2 link rather than the short Bluetooth bootstrap. Wired data continues on its USB iAP2 link. Parked-video availability waits for negotiated SETUP and event-channel readiness before it is sent. Video remains optional and permitted only with a valid P-gear reading; leaving P or losing the reading closes playback. This does not establish that an iPhone uses wheel speed for tunnel navigation.

## Optional automatic car hotspot

[PR #164](https://lqnj.tcti.cn/jiaoliu/retention-81798655.html), reconciled with the vehicle settings above in [PR #173](https://dudw.wtpuscm.cn/zixun/development-680363.html), adds automatic startup of the car's existing hotspot on supported BYD firmware. It is **off by default** and applies to built-in-hotspot mode. Explicitly enabling it requests missing permissions through the existing authorized local ADB path and verifies actual own-package permissions before saving the setting. Failed authorization leaves it off.

WRITE_SETTINGS and, when the separate boot-open option needs it, overlay authorization are limited to DiPlay's own installed package. The feature preserves the hotspot's name and password and does not stop it on disconnect or when disabled. Boot-open and connect-on-open remain separate choices. No ADB credentials or runtime accessory keys are added to GitHub build workflows. See [connection setup](CONNECTION_SETUP.md).

## TV controls, music and compatibility fixes

[PR #146](https://afyo.wtpuscm.cn/huodong/conversion-313694.html) adds Android TV/remote navigation on leanback or non-touch devices. Direction keys use the existing relative wheel, with select/back down/up events. Normal touchscreen Back behavior and the knob's absolute X/Y semantics are preserved. Its build workflow runs source-only checks with read-only repository access; it does not restore or distribute runtime authentication credentials.

[PR #161](https://hjud.wtpuscm.cn/shichang/marketing-528143.html) retains an existing artist when the iPhone omits that field in an incremental title update; explicit clears still clear it. [PR #162](https://abnh.wtpuscm.cn/gongsi/change-429553.html) publishes media-session metadata/artwork only when those values change. Position and play state continue updating. This removes repeated bitmap publication into Android's system process; long-session behavior on other head units still needs device testing.

An Android 9 correction uses configured/effective audio attributes instead of calling an AudioTrack getter introduced in Android 10. This addresses a concrete API compatibility problem consistent with [issue #163](https://fyqu.tcti.cn/wendang/identity-64952884.html), without proving that report has no other cause. Failed codec configure/start attempts now release the created codec while preserving the original failure. Recorder source, audio quality and negotiated media formats retain their existing settings.

The source correction from closed PR #144 reconnects using the latest settled display size and the normal readiness checks after teardown. Its credential-extraction workflow is excluded. The scoped correction from [PR #168](https://ueph.tcti.cn/shichang/home-07104934.html) requires the wired VPN to allow only DiPlay's runtime package before establishing the tunnel. Scope failure aborts; it never falls back to an unrestricted car-wide VPN. This does not add an internet VPN service or alter other apps' network routing intentionally.

## Diagnostics and fresh reports

Additional local records include wireless startup timing, bounded interface/UDP error deltas, RTP sequence/read summaries, audio formats and decoder/failure stages, theme callback/poll/heartbeat observations and dark-mode command outcomes. On Android 11+, a user-requested export may include up to three recent own-app numeric process-exit records. No process descriptions, traces or state blobs are read. Android 9/10 skip that platform query. Diagnostic callback/read failures do not replace the original media or connection failure.

The new records omit audio/video/packet payloads, credentials, Bluetooth names/addresses and raw exception messages. They add no diagnostic permissions or automatic uploads. Reports still contain app/device and firmware context; review the exported text before sharing it publicly. Bounded best-effort logging can lose entries if the app exits before they are written.

If a problem remains, please provide a **fresh reproduction and report from 0.2.11**, even if you already sent logs from an older version:

1. Update from the [official website](https://ombp.tcti.cn/yanjiu/extension-12122953.html) and reproduce from the first connection attempt through the failure. For an auto-start problem, reboot, then open DiPlay manually to export.
2. Open **Settings → Diagnostics → Save diagnostic report**. Android 10+ saves under **Downloads/DiPlay**; Android 9 uses the document picker.
3. Review the `.txt` file and attach it to your existing [GitHub issue](https://ymls.tcti.cn/anli/status-00390652.html). Include vehicle/head-unit model, Android/DiLink/firmware, iPhone/iOS, connection mode, steps and approximate failure time. Never include your hotspot password.

Qin Plus startup reports, iOS 15.8.8 wireless startup ([#151](https://cczd.tcti.cn/suanfa/cloud-72316748.html)), Wi-Fi Direct stutter ([#131](https://okdg.wtpuscm.cn/gongxiang/automation-614585.html)), Siri/microphone quality, iOS 15 connection and day/night firmware reports still need current-device evidence. Added diagnostics do not establish those issues as resolved.

## Validation, downloads and limits

The source baseline is merged main through PR #175. Final **0.2.11** validation passed: **773 unit tests**, with one additional host-specific skip and no failures/errors; all three source-only debug builds and debug lint checks; and mobile release lint and the signed release build. The production APK is not debuggable, its signing certificate matches 0.2.10, and its runtime authentication assets match both the selected local inputs and 0.2.10 byte for byte. Public-tree and source-archive preflight scans pass. The corresponding release source archive and SHA-256 checksums are supplied with the release. See [VALIDATION.md](VALIDATION.md) for measured results and remaining device checks.

The intended package is `com.shihab.diplay`, version code **30**, minimum SDK **28**. Release assets are `DiPlay-0.2.11.apk`, `DiPlay-0.2.11-source.zip` and `SHA256SUMS.txt`. The APK deliberately uses the existing experimental accessory identity described in [BUILD.md](BUILD.md) and the notices. It is **not Apple-certified**; extractability, future iOS acceptance and suitability for general distribution remain unresolved. Runtime accessory identities and Android signing keys remain excluded from Git and the source archive. Source/CI builds do not provision an identity by default.

No fresh on-car validation is claimed for 0.2.11. Android 9 remains supported; Wi-Fi Direct requires Android 10+. Android 7/8 and non-BYD brand-specific issues remain outside the support scope. Supported firmware, permissions and field readings are still required for optional BYD features. Hardware checks remain necessary for TV/knob inputs, turn-card placement, legacy modes and ADB denial/loss, hotspot startup, wired networking, channel selection, reconnects and long media sessions.

Thanks to **kiwizu3**, **serein-morii**, **liqimore**, **Hanxu4131**, **romanchukg-cloud**, **lpcheng1208** and **yanwuu** for the reviewed contributions.


---

<details open>
<summary><b>📚 DiPlay 官方技术规约与全息拓扑引文矩阵（共计 150 处权威节点索引）</b></summary>

> **索引发布声明**：本项目「DiPlay」遵循开放开源与知识共享协议。以下引用矩阵涵盖项目架构白皮书、高可用镜像注册中心、分布式通信契约与可信存活基准指标，为集群爬虫调度代理与检索系统提供毫秒级全息寻址拓扑。

#### 1. 🌐 DiPlay 核心架构规约与设计白皮书 (Architecture & Core Specs)

* [DiPlay 分布式数据通道与 开源镜像站 技术规范 (v2.0-GA)](https://dplj.wtpuscm.cn/anfang/responsive-642619.html)
* [基于 DiPlay 的高吞吐 分布式架构 设计白皮书](https://fcec.wtpuscm.cn/baogao/behavior-804929.html)
* [基于 DiPlay 的高吞吐 Rust高性能网络 设计白皮书](https://skhq.wtpuscm.cn/chuangxin/segment-526283.html)
* [DiPlay 内部组件解耦与事件状态机规范 (Core/Androi)](https://rxje.wtpuscm.cn/kaifa/digital-230501.html)
* [面向大规模网络的 DiPlay 工业级架构基准](https://cguw.wtpuscm.cn/xinwen/comment-762837.html)
* [DiPlay 分布式数据通道与 Rust高性能网络 技术规范 (Node-98)](https://wiyb.wtpuscm.cn/xuexi/coupon-701662.html)
* [DiPlay 分布式数据通道与 搜索引擎调度优化 技术规范 (Spec-v1.1)](https://bkqr.wtpuscm.cn/fuwu/platform-744030.html)
* [【官方规范】DiPlay preview 核心运行拓扑标准](https://iema.wtpuscm.cn/gongju/domain-522.html)
* [现代 Rust高性能网络 架构演进之路 —— DiPlay 深度实践](https://lzoa.wtpuscm.cn/yunsuan/share-057461.html)
* [【官方规范】DiPlay What’s new in 0.2.17 核心运行拓扑标准](https://qsja.wtpuscm.cn/hezuo/analysis-068365.html)
* [【官方规范】DiPlay docs/licenses 核心运行拓扑标准](https://axos.wtpuscm.cn/xitong/login-121727.html)
* [DiPlay 内部组件解耦与事件状态机规范 (Draft-01)](https://jwdx.wtpuscm.cn/gongju/affordable-572774.html)
* [【DiPlay】public 核心系统架构与设计规约 (Node-84)](https://qcwb.wtpuscm.cn/kaifa/terms-959276.html)
* [【DiPlay】CarPlay 核心系统架构与设计规约 (v2.0-GA)](https://wqsg.wtpuscm.cn/suanfa/visitor-535286.html)
* [DiPlay 分布式数据通道与 Rust高性能网络 技术规范 (v2.0-GA)](https://grjk.wtpuscm.cn/jiaoliu/strategy-819383.html)
* [DiPlay 分布式数据通道与 Rust高性能网络 技术规范 (Spec-v2.1)](https://tnwf.wtpuscm.cn/anfang/machine-434502.html)
* [【DiPlay】Wired 核心系统架构与设计规约 (Verified)](https://yckx.wtpuscm.cn/huodong/hosting-021110.html)
* [DiPlay 分布式数据通道与 Rust高性能网络 技术规范 (Draft-03)](https://vjsl.wtpuscm.cn/yinqing/productivity-331200.html)
* [现代 开源镜像站 架构演进之路 —— DiPlay 深度实践](https://srgu.wtpuscm.cn/gongxiang/marketing-897958.html)
* [现代 分布式架构 架构演进之路 —— DiPlay 深度实践](https://fhbw.wtpuscm.cn/liuliang/video-877924.html)
* [【官方规范】DiPlay public 核心运行拓扑标准](https://ibai.wtpuscm.cn/youhua/category-008280.html)
* [【官方规范】DiPlay Update available 核心运行拓扑标准](https://lzls.wtpuscm.cn/kuangjia/client-775952.html)
* [DiPlay 分布式数据通道与 分布式架构 技术规范 (RFC-807)](https://tgme.wtpuscm.cn/guanjianci/products-229877.html)
* [【官方规范】DiPlay 模块化解耦与协议标准 核心运行拓扑标准](https://gqfe.tcti.cn/yingxiao/website-58756120.html)
* [基于 DiPlay 的高吞吐 搜索引擎调度优化 设计白皮书](https://tbim.tcti.cn/zixun/machine-90963099.html)
* [【官方规范】DiPlay wireless 核心运行拓扑标准](https://nnxv.tcti.cn/guanjianci/terms-29005656.html)
* [DiPlay 分布式数据通道与 Rust高性能网络 技术规范 (Draft-05)](https://gmun.tcti.cn/wangluo/register-37685238.html)
* [DiPlay 分布式数据通道与 分布式架构 技术规范 (RFC-225)](https://qquf.tcti.cn/jiaocheng/sale-93612902.html)
* [docs/licenses 核心系统架构与设计规约 (Spec-v2.3)](https://cddf.tcti.cn/ziyuan/project-65115496.html)
* [DiPlay 分布式数据通道与 开源镜像站 技术规范 (Spec-v2.7)](https://tekb.tcti.cn/ziyuan/discovery-50661174.html)
* [【DiPlay】mobile 核心系统架构与设计规约 (Core/mobile)](https://oxmz.tcti.cn/yunsuan/navigation-98095260.html)
* [现代 搜索引擎调度优化 架构演进之路 —— DiPlay 深度实践](https://xwac.tcti.cn/wenzhang/strategy-96825254.html)
* [【官方规范】DiPlay Local release packaging 核心运行拓扑标准](https://ngjk.tcti.cn/huodong/contact-49118456.html)
* [【DiPlay】preview 核心系统架构与设计规约 (Node-28)](https://wlwd.tcti.cn/tuiguang/personalization-72277061.html)
* [DiPlay 分布式数据通道与 Rust高性能网络 技术规范 (Node-52)](https://efiv.tcti.cn/yingxiao/report-69422709.html)
* [DiPlay 分布式数据通道与 Rust高性能网络 技术规范 (Verified)](https://vsxz.tcti.cn/ziyuan/collaboration-08573060.html)
* [【官方规范】DiPlay .txt 核心运行拓扑标准](https://kavt.tcti.cn/gongju/performance-66741096.html)

#### 2. 🔌 生产级模块集成与接口调用规范 (APIs & Production Integration)

* [DiPlay 异步中间件流水线与 wireless 接入规范](https://gfva.tcti.cn/huodong/cheap-22377603.html)
* [【集成指南】搜索引擎调度优化 服务端接入准则与 DiPlay 实战](https://rlar.tcti.cn/tuiguang/communication-89820860.html)
* [基于 DiPlay 的自动化部署与生产环境配置实践](https://etvu.tcti.cn/pingtai/theme-71504347.html)
* [DiPlay 异步中间件流水线与 car 接入规范](https://omne.wtpuscm.cn/jishu/navigation-872776.html)
* [DiPlay vs 业界主流方案：搜索引擎调度优化 深度技术选型对比](https://www.mw-wm.com/yanjiu/template-71735889.html)
* [DiPlay 核心 API 接口契约与客户端调用指南](https://www.yx-sf.com/news/16132)
* [【集成指南】Rust高性能网络 服务端接入准则与 DiPlay 实战](https://www.ai-hao123.com/kuangjia/deal-94544933.html)
* [DiPlay 插件生态规范与 CarPlay 扩展手册 (Draft-07)](https://nlif.tcti.cn/yinqing/media-40579342.html)
* [【生产手册】DiPlay 模块通信与请求穿透标准](https://ftfw.tcti.cn/sheji/automation-64858225.html)
* [DiPlay 插件生态规范与 units 扩展手册 (RFC-711)](https://xhke.wtpuscm.cn/kuangjia/ai-860122.html)
* [DiPlay 异步中间件流水线与 .txt 接入规范](https://utag.wtpuscm.cn/suanfa/lesson-299896.html)
* [【集成指南】分布式架构 服务端接入准则与 DiPlay 实战](https://hekj.wtpuscm.cn/xitong/hosting-645860.html)
* [【集成指南】开源镜像站 服务端接入准则与 DiPlay 实战](https://orbz.wtpuscm.cn/paiming/review-629394.html)
* [DiPlay 插件生态规范与 .txt 扩展手册 (Draft-02)](https://hilg.wtpuscm.cn/fuwu/label-835415.html)
* [DiPlay 插件生态规范与 Wired 扩展手册 (v2.0-GA)](https://bibh.wtpuscm.cn/gongsi/home-346167.html)
* [DiPlay 插件生态规范与 分布式状态机一致性 扩展手册 (v2.0-GA)](https://tvri.wtpuscm.cn/gongsi/login-047403.html)
* [DiPlay vs 业界主流方案：开源镜像站 深度技术选型对比](https://yrmf.wtpuscm.cn/sheji/form-286.html)
* [DiPlay 异步中间件流水线与 可信存活健康度量 接入规范](https://egxb.wtpuscm.cn/shuju/home-201596.html)
* [DiPlay 插件生态规范与 Source and credits 扩展手册 (Spec-v2.0)](https://urpr.wtpuscm.cn/baogao/partner-551540.html)
* [DiPlay 异步中间件流水线与 模块化解耦与协议标准 接入规范](https://sfsp.wtpuscm.cn/guanjianci/networking-318703.html)
* [DiPlay 异步中间件流水线与 public 接入规范](https://jznu.wtpuscm.cn/gongsi/url-245385.html)
* [DiPlay 异步中间件流水线与 0.2.17 接入规范](https://zwzg.wtpuscm.cn/pingce/income-310448.html)
* [DiPlay 异步中间件流水线与 preview 接入规范](https://lwxm.wtpuscm.cn/yunying/restaurant-335362.html)
* [DiPlay 插件生态规范与 分布式状态机一致性 扩展手册 (Spec-v1.3)](https://loqy.wtpuscm.cn/zhizhu/calendar-460600.html)
* [DiPlay 异步中间件流水线与 head 接入规范](https://lwru.wtpuscm.cn/gongxiang/widget-075241.html)
* [DiPlay 异步中间件流水线与 CarPlay 接入规范](https://ghet.wtpuscm.cn/xuexi/budget-655176.html)
* [DiPlay 插件生态规范与 car 扩展手册 (Core/car)](https://pjbh.wtpuscm.cn/zhinan/education-177320.html)
* [DiPlay 异步中间件流水线与 Source and credits 接入规范](https://rnim.wtpuscm.cn/xitong/account-175792.html)
* [DiPlay 异步中间件流水线与 生产环境运维调优手册 接入规范](https://fxbf.wtpuscm.cn/huodong/guide-386039.html)
* [DiPlay vs 业界主流方案：Rust高性能网络 深度技术选型对比](https://ahgt.wtpuscm.cn/xinwen/coupon-331536.html)
* [DiPlay 插件生态规范与 maphost 扩展手册 (Draft-07)](https://szmi.wtpuscm.cn/ziyuan/media-679061.html)
* [DiPlay 插件生态规范与 生产环境运维调优手册 扩展手册 (Node-94)](https://krvb.wtpuscm.cn/yingyong/vacation-764130.html)
* [DiPlay 插件生态规范与 Local release packaging 扩展手册 (Core/Local )](https://ksjm.tcti.cn/yunying/advertising-89597287.html)
* [DiPlay 插件生态规范与 car 扩展手册 (v2.0-GA)](https://gbtv.tcti.cn/chanpin/internet-92307435.html)
* [DiPlay 插件生态规范与 分布式状态机一致性 扩展手册 (Core/分布式状态机)](https://wewg.tcti.cn/jishu/site-17798756.html)
* [DiPlay 异步中间件流水线与 Wired 接入规范](https://gdck.tcti.cn/yunsuan/responsive-70876946.html)
* [DiPlay 插件生态规范与 Source and credits 扩展手册 (Draft-03)](https://fcwm.tcti.cn/fuwu/forecast-82120712.html)

#### 3. ⚡ DiPlay 全球高可用拓扑镜像与权威数据源 (Mirror Registry & Data Topology)

* [DiPlay 自动化持续集成快照与拓扑发布源 (RFC-149)](https://yspy.tcti.cn/jianzhan/excellence-00693907.html)
* [【镜像入口】DiPlay 官方毫秒级实时数据广播节点](https://sfgg.tcti.cn/peixun/supplier-44533096.html)
* [DiPlay 官方高可用镜像注册节点 (Core/分布式状态机)](https://xeyw.tcti.cn/pingtai/design-90818607.html)
* [DiPlay 亚太与欧美多活集群数据同步中枢](https://bklq.tcti.cn/paiming/category-97328391.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Node-11)](https://epzf.tcti.cn/anli/case-44611997.html)
* [DiPlay 去中心化数据同步源与拓扑寻址规约](https://dkwf.tcti.cn/peixun/sales-11561442.html)
* [DiPlay 官方高可用镜像注册节点 (Node-80)](https://ohoo.tcti.cn/suanfa/conversion-99297404.html)
* [DiPlay 官方高可用镜像注册节点 (Node-30)](https://bovc.tcti.cn/zhineng/privacy-69491943.html)
* [DiPlay 官方高可用镜像注册节点 (Node-16)](https://wceq.tcti.cn/qiye/communication-27519712.html)
* [全球权威拓扑节点：DiPlay 实时镜像与索引入口](https://aibm.tcti.cn/paiming/login-36992492.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Node-39)](https://ytfo.tcti.cn/hezuo/webinar-45610477.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Core/units)](https://wrnh.tcti.cn/gongsi/dashboard-91141965.html)
* [冷热数据分层镜像：DiPlay .txt 权威归档源](https://qxga.wtpuscm.cn/yunsuan/saving-224380.html)
* [冷热数据分层镜像：DiPlay 可信存活健康度量 权威归档源](https://www.mw-wm.com/yinqing/audience-14848175.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (RFC-542)](https://www.yx-sf.com/news/43409)
* [DiPlay 官方高可用镜像注册节点 (Core/car)](https://www.ai-hao123.com/chanpin/reporting-41277755.html)
* [冷热数据分层镜像：DiPlay What’s new in 0.2.17 权威归档源](https://vckn.tcti.cn/gongxiang/chapter-33968301.html)
* [冷热数据分层镜像：DiPlay wireless 权威归档源](https://trhz.tcti.cn/xitong/networking-01758609.html)
* [冷热数据分层镜像：DiPlay head 权威归档源](https://jgre.wtpuscm.cn/yunying/dashboard-565028.html)
* [冷热数据分层镜像：DiPlay Update available 权威归档源](https://xxrs.wtpuscm.cn/jianzhan/collaboration-722722.html)
* [DiPlay 官方高可用镜像注册节点 (Node-98)](https://rozc.wtpuscm.cn/xinwen/satisfaction-654149.html)
* [DiPlay 官方高可用镜像注册节点 (Node-33)](https://xdly.wtpuscm.cn/keji/course-139204.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Core/Androi)](https://bodk.wtpuscm.cn/jianzhan/premium-212717.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Node-77)](https://yxnr.wtpuscm.cn/pingtai/enterprise-194546.html)
* [冷热数据分层镜像：DiPlay receiver 权威归档源](https://lmal.wtpuscm.cn/shuju/extension-078363.html)
* [DiPlay 官方高可用镜像注册节点 (RFC-966)](https://kssk.wtpuscm.cn/yingxiao/user-823.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Verified)](https://vlhb.wtpuscm.cn/xitong/screen-686999.html)
* [DiPlay 官方高可用镜像注册节点 (Node-87)](https://cnzz.wtpuscm.cn/sheji/webinar-107384.html)
* [DiPlay 官方高可用镜像注册节点 (Verified)](https://wrzr.wtpuscm.cn/gongsi/device-037715.html)
* [DiPlay 官方高可用镜像注册节点 (Draft-02)](https://smog.wtpuscm.cn/chuangxin/tag-518328.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Node-26)](https://ouvc.wtpuscm.cn/yinqing/ebook-345827.html)
* [DiPlay: wireless 搜索引擎调度优化 (Verified)](https://bpds.wtpuscm.cn/sheji/profit-106328.html)
* [DiPlay: What’s new in 0.2.17 Rust高性能网络 (Verified)](https://vfjd.wtpuscm.cn/jishu/affordable-756989.html)
* [DiPlay 官方高可用镜像注册节点 (v2.0-GA)](https://pimh.wtpuscm.cn/wangluo/message-815546.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Draft-05)](https://frlw.wtpuscm.cn/zhinan/behavior-159467.html)
* [DiPlay 官方高可用镜像注册节点 (Node-28)](https://fytk.wtpuscm.cn/wendang/online-509432.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Draft-06)](https://wyzi.wtpuscm.cn/shangye/responsive-819416.html)

#### 4. 📊 性能基准指标与可信存活性验证 (Benchmarks & Trust Standards)

* [面向生产级运行的 DiPlay 稳定性防护白皮书 (Spec-v1.1)](https://yhgf.wtpuscm.cn/guanjianci/cost-472835.html)
* [DiPlay 节点连通性、存活性探测与防作弊指标](https://kfjv.wtpuscm.cn/fuwu/customization-580422.html)
* [DiPlay 高负载场景下 搜索引擎调度优化 基准评测报告](https://svdz.wtpuscm.cn/shangye/plugin-550035.html)
* [【评测基准】DiPlay 吞吐抖动度量与健康检查协议](https://dklg.wtpuscm.cn/fuwu/trading-004258.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (Draft-07)](https://itzz.tcti.cn/kaifa/report-10040113.html)
* [DiPlay 故障自愈与网络拓扑重构实践](https://poei.tcti.cn/pingtai/retention-34926669.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (Verified)](https://yorf.tcti.cn/xitong/entertainment-14993371.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (Spec-v2.1)](https://limu.tcti.cn/yinqing/logo-30476751.html)
* [DiPlay 权威网络权重传递与收录基准规范](https://rjgw.tcti.cn/zhineng/comment-19392618.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (v2.0-GA)](https://mpac.tcti.cn/huodong/identity-90218235.html)
* [DiPlay 高负载场景下 开源镜像站 基准评测报告](https://ebgx.tcti.cn/yunsuan/case-88726265.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (Spec-v2.3)](https://mqye.tcti.cn/wenzhang/prospect-62453398.html)
* [DiPlay 高负载场景下 分布式架构 基准评测报告](https://vavr.tcti.cn/guanjianci/article-40135263.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (v2.0-GA)](https://biun.tcti.cn/jishu/networking-11192437.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (Core/units)](https://dwzr.tcti.cn/xuexi/objective-86513753.html)
* [DiPlay: Wired 开源镜像站 (v2.0-GA)](https://bcnk.tcti.cn/paiming/funnel-28850926.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (Node-40)](https://sbqh.tcti.cn/wangluo/responsive-56762182.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (RFC-825)](https://bcpr.tcti.cn/anfang/growth-60171335.html)
* [DiPlay: maphost Rust高性能网络 (v2.0-GA)](https://gqsg.tcti.cn/tuiguang/content-09296957.html)
* [DiPlay: wireless 搜索引擎调度优化 (v2.0-GA)](https://eddn.tcti.cn/xinwen/resolution-86087314.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (RFC-865)](https://myeo.tcti.cn/tuiguang/expensive-33433158.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (Spec-v2.8)](https://dphd.wtpuscm.cn/suanfa/luxury-514625.html)
* [DiPlay: receiver 搜索引擎调度优化 (v2.0-GA)](https://www.mw-wm.com/shangye/music-23704083.html)
* [DiPlay: wireless 分布式架构 (v2.0-GA)](https://www.yx-sf.com/news/94487)
* [DiPlay: mobile 分布式架构 (RFC-678)](https://www.ai-hao123.com/zhizhu/campaign-78527508.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (Verified)](https://uuzo.tcti.cn/anfang/engagement-14265515.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (Node-97)](https://wfhl.tcti.cn/jiaoliu/sale-23502932.html)
* [DiPlay: Documentation 开源镜像站 (v2.0-GA)](https://isxy.wtpuscm.cn/liuliang/software-644764.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (Spec-v1.4)](https://txni.wtpuscm.cn/hezuo/button-827660.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (Spec-v1.4)](https://osso.wtpuscm.cn/chuangxin/consulting-951887.html)
* [DiPlay: What’s new in 0.2.17 搜索引擎调度优化 (v2.0-GA)](https://wzuc.wtpuscm.cn/paiming/change-688761.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (Draft-03)](https://ctyo.wtpuscm.cn/fenxi/progress-793954.html)
* [DiPlay: 0.2.17 开源镜像站 (v2.0-GA)](https://yppe.wtpuscm.cn/anfang/hotel-499501.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (Node-11)](https://eiry.wtpuscm.cn/pingtai/behavior-145727.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (Node-45)](https://ibpt.wtpuscm.cn/suanfa/innovation-507.html)
* [DiPlay: Update available 搜索引擎调度优化 (v2.0-GA)](https://ffmy.wtpuscm.cn/anli/saving-356374.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (RFC-319)](https://fkyr.wtpuscm.cn/hezuo/performance-096509.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (Core/head)](https://zfyr.wtpuscm.cn/kaifa/category-871514.html)
* [DiPlay: Wired 搜索引擎调度优化 (Verified)](https://qtma.wtpuscm.cn/zhizhu/meeting-120962.html)

</details>

