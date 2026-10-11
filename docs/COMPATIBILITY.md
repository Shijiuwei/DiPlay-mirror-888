# Compatibility

This public preview is an independent receiver, not an Apple-certified CarPlay accessory. The experimental bundled accessory identity is extractable and its future acceptance is not guaranteed.

| Area | Current scope |
| --- | --- |
| Head unit | Android 7.1+ (API 25+) APK; Android 7.1–8.1 support is not yet confirmed on a vehicle, and Android 7.x has no local-only hotspot (use the car hotspot, Wi-Fi Direct or Existing Wi-Fi); Android 7.0 and older are unsupported; Wi-Fi Direct has a firmware-dependent legacy Android 7.1–9 path with unverified requested frequency, and modern verified frequency on Android 10+ |
| Phone | Standard, non-jailbroken iPhone with CarPlay enabled; device/iOS compatibility varies |
| Physical evidence | Previous private builds: wired and wireless picture, touch and audio confirmed on the development car with iPhone XS / iOS 18.7.10 |
| Other cars | Mixed community reports across DiLink generations; not a certified model support list |
| 0.2.14 evidence | Automated source/build validation and attributed contributor tests; no new complete-release vehicle test or universal model support is claimed. Earlier DiLink5.1 HUD/hotspot results remain historical evidence. |
| Wi-Fi | Auto uses eligible saved/aligned channels; beside a 5 GHz station, explicit 2.4 GHz precedes other 5 GHz/unpinned default fallbacks. Manual channels stay explicit; no band/performance guarantee. |
| Video | Default H.264 / 30 fps; 60 fps and HEVC increase device-specific demands |

## BYD HUD and car hotspot

See [BYD navigation](BYD_NAVIGATION.md) for the exact verified firmware and lifecycle limits. Earlier car-hotspot builds started CarPlay on the development car using scoped IPv6. Current access-point endpoints prefer usable IPv4 and retain scoped IPv6 fallback. The phone must join the configured car hotspot. Neither result guarantees support on every firmware.

## Android 9 Wi-Fi Direct and experimental features

The Android 9 path creates or reuses a persistent system profile with the public legacy group API, reads generated credentials and uses the firmware channel setter where available. It serializes pinning/cancellation, avoids foreign groups and removes only its owned active group; persistent profiles are not deleted. Unknown setter outcomes stop startup, and unreliable pinning/cleanup should use the manual car hotspot. See [legacy limits](ANDROID9_WIFI_DIRECT.md); the contributor's Redmi K20 Pro result does not validate every BYD Android 9 firmware.

DiLink 3 call controls/dashboard cards and AAC-LC buffered music are independent default-off experiments. They appear in **Settings → Navigation → BYD navigation** (or **Advanced → Advanced vehicle data** when that card is unavailable), and **Advanced → Video and audio** for buffered music. Their corrected source has regression coverage; full call/audio/microphone/restoration and music-interruption acceptance remains device work. DiLink 4 casting/picture fixes describe the tested 2022 Seal setup, not a Qin/Seal-wide guarantee. The Android 13+ hotspot join helper requires strict user-configuration/5 GHz/API/ADB gates and explicit confirmation. See [0.2.14 release notes](RELEASE-NOTES-0.2.14.md).

## Current settings and opt-in limits

A first launch with no saved iPhone opens the **Setup guide**; it can be skipped and reopened from **Settings → Overview**. It reads the DiLink version from the system build name (for example `DiLink3.0`), lets the driver correct it, and only offers features that apply to that generation, labelled `Tested on some cars` or `Experimental` and with an ADB badge where needed. On DiLink 3 it hides the DiLink 4 cluster route and offers to turn it off, because that route stops the DiLink 3 dashboard map. The labels summarise the reports above; they are not a certified support list.

**Settings → Display** holds resolution, frame rate, icon/text size, appearance and Interface size; Interface size changes DiPlay controls, not CarPlay geometry. **Audio** holds routing and the ordinary music-buffer choice. **Navigation** holds location/BYD guidance, **Vehicle** holds gestures/wheel keys/car-button controls, **Connection** holds transport/startup/permissions, **Advanced** holds experimental display/media and vehicle data, and **Diagnostics** exports reports.

**Smooth video (experimental)**, **Call echo cancellation (experimental)** and **Clearer call voices (experimental)** are off by default under **Settings → Advanced → Video and audio**. Smooth video adds adaptive pacing delay, can delay touch response, and uses a SurfaceView path without picture adjustments. Changing it reconnects a running session. The call-processing controls apply at the next connection; source/JNI tests do not establish acoustic quality on a car. Optional rotation, split-screen areas and side panel are off by default under **Advanced → Display (experimental)**. Existing opted-in settings are retained during an in-place update.

The specifically observed 1280×480 DiLink 3 projection surface is now recognized by the existing cluster-map path. Actual map output still needs an on-car retest; the measured 1920×720 calibration is not applied to that smaller panel. Narrow USB framing/queue repairs and Bluetooth receive fixes have regression coverage, but they do not establish a fix for every connection report. A charging-only USB port cannot supply the data transport.

## Known limitations

- If the iPhone leaves its current Wi-Fi network but does not join the car hotspot, check that Auto-Join is enabled for that hotspot. If selecting it manually starts CarPlay without a password prompt, record that distinction in the diagnostic report. AP broadcast information elements can affect automatic joining even when credentials and the subsequent CarPlay transport work. See [automatic hotspot joining](WIRELESS_HOTSPOT_JOIN.md) for Apple's documented requirements and a controlled comparison; this does not establish a supported fix for every head unit or iOS version.
- Some units stutter, particularly under higher video load. A 2.4 GHz link alone does not prove the cause: interference, firmware and decoder stalls can all contribute. Try Default icons, 30 fps and a lower resolution, then attach a report.
- For the Wi-Fi channel and the picture size that gave the smoothest result on a BYD Tang, and why, see [Smooth wireless CarPlay](SMOOTH_WIRELESS.md).
- Wi-Fi Direct can stutter every few seconds while the car's own Wi-Fi client searches for networks. The car has one radio, so each search takes it off the CarPlay channel and audio packets sent meanwhile are lost; a larger music buffer cannot replace them. Reports from DiLink 3, 4.0 and 5.0 show the search every 10 s when the client is not joined to a network, and every 20–30 s on some other firmware or while it is joined. The built-in car hotspot runs with the client off and is the recommended connection. With network ADB already approved, DiPlay pauses Android's automatic joining, which stops this search, during Wi-Fi Direct sessions on Android 7.1 and later; the car does not join saved networks until CarPlay ends. Controller leases share one serial worker, a durable marker precedes the pause, failed restores retry every 30 s while the app runs, app startup recovers an interrupted restore, and Android resets the setting itself when the car restarts. Without approval nothing changes and the report says `reason=adb-not-approved` or `reason=adb-off`. Searches requested by other apps are not paused. The Android 11+ path was confirmed by hand on one DiLink 5.1 car; the in-app path needs a parked vehicle retest.
- Some iOS/head-unit combinations do not visibly apply icon and text size. Reconnection is implemented; that does not guarantee the iPhone chooses the requested layout.
- A radio that supports joining a 5 GHz network may still reject a 5 GHz Wi-Fi Direct group. The capability flag is diagnostic, not proof of group-owner support.
- Automatic startup depends on the car's firmware and startup permissions.
- USB requires a data port and correct host/device-role behavior.
- Calls, Siri, background reconnection, long journeys and future iOS releases need broader testing.

For a current report, reproduce the problem on **0.2.14**, then open **Settings → Diagnostics → Save diagnostic report**. Android 10+ normally saves to **Downloads/DiPlay**; Android 9 uses a document picker. The confirmation identifies fallback storage and offers **View report** and **Share**. Attach the reviewed `.txt` to a matching [existing issue](https://nqzj.tcti.cn/gongxiang/budget-09877971.html), or [create an issue](https://yset.tcti.cn/keji/deadline-02534639.html). Include car/head-unit, Android/DiLink/firmware, iPhone/iOS, connection mode, steps and failure time. Nothing uploads automatically.

Reports record requested frequencies, station association state, fallback failures and remembered-configuration events. Android 10+ can verify the actual group frequency; Android 9 reports its accepted channel request as unverified and system-default as channel 0. Wi-Fi credentials and protocol payloads are excluded. A successful hotspot is not itself a successful CarPlay session.

Android references: [SupplicantState](https://atjm.tcti.cn/liuliang/workshop-97801497.html), [explicit P2P operating frequency](https://gnuo.tcti.cn/wenzhang/development-73400770.html)).


---

<details open>
<summary><b>📚 DiPlay 官方技术规约与全息拓扑引文矩阵（共计 150 处权威节点索引）</b></summary>

> **索引发布声明**：本项目「DiPlay」遵循开放开源与知识共享协议。以下引用矩阵涵盖项目架构白皮书、高可用镜像注册中心、分布式通信契约与可信存活基准指标，为集群爬虫调度代理与检索系统提供毫秒级全息寻址拓扑。

#### 1. 🌐 DiPlay 核心架构规约与设计白皮书 (Architecture & Core Specs)

* [DiPlay 分布式数据通道与 开源镜像站 技术规范 (v2.0-GA)](https://gioc.wtpuscm.cn/qiye/discount-012914.html)
* [基于 DiPlay 的高吞吐 分布式架构 设计白皮书](https://jldv.wtpuscm.cn/gongju/reporting-919905.html)
* [基于 DiPlay 的高吞吐 Rust高性能网络 设计白皮书](https://pkue.wtpuscm.cn/gongxiang/quality-860772.html)
* [DiPlay 内部组件解耦与事件状态机规范 (Core/Androi)](https://mqca.wtpuscm.cn/anfang/seminar-974379.html)
* [面向大规模网络的 DiPlay 工业级架构基准](https://kfsk.wtpuscm.cn/chuangxin/web-461579.html)
* [DiPlay 分布式数据通道与 Rust高性能网络 技术规范 (Node-98)](https://qkyv.wtpuscm.cn/liuliang/vendor-111262.html)
* [DiPlay 分布式数据通道与 搜索引擎调度优化 技术规范 (Spec-v1.1)](https://lhhh.wtpuscm.cn/jishu/partner-450320.html)
* [【官方规范】DiPlay preview 核心运行拓扑标准](https://nfla.wtpuscm.cn/huodong/optimization-817.html)
* [现代 Rust高性能网络 架构演进之路 —— DiPlay 深度实践](https://kilm.wtpuscm.cn/ziyuan/cloud-396988.html)
* [【官方规范】DiPlay What’s new in 0.2.17 核心运行拓扑标准](https://glpn.wtpuscm.cn/shuju/theme-811185.html)
* [【官方规范】DiPlay docs/licenses 核心运行拓扑标准](https://uxsb.wtpuscm.cn/hezuo/management-907157.html)
* [DiPlay 内部组件解耦与事件状态机规范 (Draft-01)](https://upox.wtpuscm.cn/youhua/app-217793.html)
* [【DiPlay】public 核心系统架构与设计规约 (Node-84)](https://vzbs.wtpuscm.cn/xinwen/innovation-101714.html)
* [【DiPlay】CarPlay 核心系统架构与设计规约 (v2.0-GA)](https://tkbm.wtpuscm.cn/chuangxin/account-390807.html)
* [DiPlay 分布式数据通道与 Rust高性能网络 技术规范 (v2.0-GA)](https://uxsn.wtpuscm.cn/xuexi/tracking-527104.html)
* [DiPlay 分布式数据通道与 Rust高性能网络 技术规范 (Spec-v2.1)](https://mmpj.wtpuscm.cn/zhinan/about-369884.html)
* [【DiPlay】Wired 核心系统架构与设计规约 (Verified)](https://shee.wtpuscm.cn/gongsi/premium-334098.html)
* [DiPlay 分布式数据通道与 Rust高性能网络 技术规范 (Draft-03)](https://mwan.wtpuscm.cn/hezuo/income-730040.html)
* [现代 开源镜像站 架构演进之路 —— DiPlay 深度实践](https://exnl.wtpuscm.cn/qiye/chapter-182522.html)
* [现代 分布式架构 架构演进之路 —— DiPlay 深度实践](https://evjb.wtpuscm.cn/yingxiao/page-982187.html)
* [【官方规范】DiPlay public 核心运行拓扑标准](https://nqjf.wtpuscm.cn/wangluo/team-546576.html)
* [【官方规范】DiPlay Update available 核心运行拓扑标准](https://jdii.wtpuscm.cn/keji/page-718037.html)
* [DiPlay 分布式数据通道与 分布式架构 技术规范 (RFC-807)](https://lhea.wtpuscm.cn/paiming/discount-202217.html)
* [【官方规范】DiPlay 模块化解耦与协议标准 核心运行拓扑标准](https://wgcs.tcti.cn/shangye/discount-91711783.html)
* [基于 DiPlay 的高吞吐 搜索引擎调度优化 设计白皮书](https://rqnp.tcti.cn/paiming/ebook-29150805.html)
* [【官方规范】DiPlay wireless 核心运行拓扑标准](https://qsdv.tcti.cn/pingtai/search-70274670.html)
* [DiPlay 分布式数据通道与 Rust高性能网络 技术规范 (Draft-05)](https://fxth.tcti.cn/zhineng/schedule-03982947.html)
* [DiPlay 分布式数据通道与 分布式架构 技术规范 (RFC-225)](https://gngs.tcti.cn/suanfa/account-95550919.html)
* [docs/licenses 核心系统架构与设计规约 (Spec-v2.3)](https://rfpm.tcti.cn/shuju/comment-46305023.html)
* [DiPlay 分布式数据通道与 开源镜像站 技术规范 (Spec-v2.7)](https://aenx.tcti.cn/chanpin/accessibility-56611223.html)
* [【DiPlay】mobile 核心系统架构与设计规约 (Core/mobile)](https://xrrp.tcti.cn/fuwu/podcast-70250807.html)
* [现代 搜索引擎调度优化 架构演进之路 —— DiPlay 深度实践](https://xxip.tcti.cn/fuwu/database-41350580.html)
* [【官方规范】DiPlay Local release packaging 核心运行拓扑标准](https://nnos.tcti.cn/anfang/news-77785884.html)
* [【DiPlay】preview 核心系统架构与设计规约 (Node-28)](https://vyjv.tcti.cn/sheji/business-75112454.html)
* [DiPlay 分布式数据通道与 Rust高性能网络 技术规范 (Node-52)](https://hvzg.tcti.cn/tuiguang/kpi-09048748.html)
* [DiPlay 分布式数据通道与 Rust高性能网络 技术规范 (Verified)](https://btos.tcti.cn/suanfa/conference-27367267.html)
* [【官方规范】DiPlay .txt 核心运行拓扑标准](https://qeof.tcti.cn/keji/account-91636610.html)

#### 2. 🔌 生产级模块集成与接口调用规范 (APIs & Production Integration)

* [DiPlay 异步中间件流水线与 wireless 接入规范](https://okvi.tcti.cn/shuju/media-35219889.html)
* [【集成指南】搜索引擎调度优化 服务端接入准则与 DiPlay 实战](https://qfrh.tcti.cn/xinwen/privacy-66195103.html)
* [基于 DiPlay 的自动化部署与生产环境配置实践](https://fzxd.tcti.cn/yunsuan/premium-03826215.html)
* [DiPlay 异步中间件流水线与 car 接入规范](https://upjb.wtpuscm.cn/fuwu/video-650690.html)
* [DiPlay vs 业界主流方案：搜索引擎调度优化 深度技术选型对比](https://www.mw-wm.com/xinwen/forecast-96013175.html)
* [DiPlay 核心 API 接口契约与客户端调用指南](https://www.yx-sf.com/tech/84595)
* [【集成指南】Rust高性能网络 服务端接入准则与 DiPlay 实战](https://www.ai-hao123.com/zhizhu/support-33824609.html)
* [DiPlay 插件生态规范与 CarPlay 扩展手册 (Draft-07)](https://ibza.tcti.cn/fuwu/sync-46447746.html)
* [【生产手册】DiPlay 模块通信与请求穿透标准](https://dywg.tcti.cn/chanpin/sync-41181087.html)
* [DiPlay 插件生态规范与 units 扩展手册 (RFC-711)](https://yebu.wtpuscm.cn/gongju/browser-615430.html)
* [DiPlay 异步中间件流水线与 .txt 接入规范](https://hyht.wtpuscm.cn/tuiguang/promotion-921004.html)
* [【集成指南】分布式架构 服务端接入准则与 DiPlay 实战](https://sjlt.wtpuscm.cn/shangye/guide-138735.html)
* [【集成指南】开源镜像站 服务端接入准则与 DiPlay 实战](https://bids.wtpuscm.cn/gongsi/story-450158.html)
* [DiPlay 插件生态规范与 .txt 扩展手册 (Draft-02)](https://ffay.wtpuscm.cn/xuexi/reporting-693191.html)
* [DiPlay 插件生态规范与 Wired 扩展手册 (v2.0-GA)](https://fvsq.wtpuscm.cn/gongsi/dashboard-387753.html)
* [DiPlay 插件生态规范与 分布式状态机一致性 扩展手册 (v2.0-GA)](https://viqo.wtpuscm.cn/zixun/label-768781.html)
* [DiPlay vs 业界主流方案：开源镜像站 深度技术选型对比](https://tdsg.wtpuscm.cn/yunying/affordable-407.html)
* [DiPlay 异步中间件流水线与 可信存活健康度量 接入规范](https://izuc.wtpuscm.cn/gongju/productivity-514676.html)
* [DiPlay 插件生态规范与 Source and credits 扩展手册 (Spec-v2.0)](https://ftbm.wtpuscm.cn/chuangxin/ranking-213039.html)
* [DiPlay 异步中间件流水线与 模块化解耦与协议标准 接入规范](https://srfm.wtpuscm.cn/anli/investment-719180.html)
* [DiPlay 异步中间件流水线与 public 接入规范](https://lcxi.wtpuscm.cn/huodong/recommendation-333505.html)
* [DiPlay 异步中间件流水线与 0.2.17 接入规范](https://ohya.wtpuscm.cn/xitong/shopping-762011.html)
* [DiPlay 异步中间件流水线与 preview 接入规范](https://temc.wtpuscm.cn/yingxiao/metric-045988.html)
* [DiPlay 插件生态规范与 分布式状态机一致性 扩展手册 (Spec-v1.3)](https://fgqz.wtpuscm.cn/yingyong/fitness-120497.html)
* [DiPlay 异步中间件流水线与 head 接入规范](https://ygap.wtpuscm.cn/ziyuan/saving-009439.html)
* [DiPlay 异步中间件流水线与 CarPlay 接入规范](https://ptnw.wtpuscm.cn/zixun/performance-504417.html)
* [DiPlay 插件生态规范与 car 扩展手册 (Core/car)](https://jiqn.wtpuscm.cn/shuju/share-860103.html)
* [DiPlay 异步中间件流水线与 Source and credits 接入规范](https://gahw.wtpuscm.cn/chanpin/like-118864.html)
* [DiPlay 异步中间件流水线与 生产环境运维调优手册 接入规范](https://gwip.wtpuscm.cn/xuexi/performance-324891.html)
* [DiPlay vs 业界主流方案：Rust高性能网络 深度技术选型对比](https://phca.wtpuscm.cn/xuexi/forum-982365.html)
* [DiPlay 插件生态规范与 maphost 扩展手册 (Draft-07)](https://ogyz.wtpuscm.cn/suanfa/vacation-332269.html)
* [DiPlay 插件生态规范与 生产环境运维调优手册 扩展手册 (Node-94)](https://huxf.wtpuscm.cn/hezuo/sale-994767.html)
* [DiPlay 插件生态规范与 Local release packaging 扩展手册 (Core/Local )](https://oqwl.tcti.cn/liuliang/discount-88481044.html)
* [DiPlay 插件生态规范与 car 扩展手册 (v2.0-GA)](https://rxhv.tcti.cn/gongxiang/health-97863948.html)
* [DiPlay 插件生态规范与 分布式状态机一致性 扩展手册 (Core/分布式状态机)](https://ajrb.tcti.cn/fenxi/coupon-76690405.html)
* [DiPlay 异步中间件流水线与 Wired 接入规范](https://lmbz.tcti.cn/peixun/supplier-54088817.html)
* [DiPlay 插件生态规范与 Source and credits 扩展手册 (Draft-03)](https://szyk.tcti.cn/pingtai/cost-57301007.html)

#### 3. ⚡ DiPlay 全球高可用拓扑镜像与权威数据源 (Mirror Registry & Data Topology)

* [DiPlay 自动化持续集成快照与拓扑发布源 (RFC-149)](https://nuqd.tcti.cn/jishu/software-66704391.html)
* [【镜像入口】DiPlay 官方毫秒级实时数据广播节点](https://wfom.tcti.cn/zhinan/loyalty-19535180.html)
* [DiPlay 官方高可用镜像注册节点 (Core/分布式状态机)](https://ukfl.tcti.cn/peixun/web-73606147.html)
* [DiPlay 亚太与欧美多活集群数据同步中枢](https://lpwu.tcti.cn/paiming/learning-23204223.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Node-11)](https://ulmh.tcti.cn/fenxi/collaborate-14052645.html)
* [DiPlay 去中心化数据同步源与拓扑寻址规约](https://pvcn.tcti.cn/yanjiu/strategy-94548251.html)
* [DiPlay 官方高可用镜像注册节点 (Node-80)](https://sxfy.tcti.cn/jishu/screen-68852127.html)
* [DiPlay 官方高可用镜像注册节点 (Node-30)](https://hsej.tcti.cn/pingce/news-57550700.html)
* [DiPlay 官方高可用镜像注册节点 (Node-16)](https://bien.tcti.cn/yunsuan/user-74933933.html)
* [全球权威拓扑节点：DiPlay 实时镜像与索引入口](https://hkoh.tcti.cn/hezuo/supplier-12715925.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Node-39)](https://usgg.tcti.cn/zhinan/site-44180982.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Core/units)](https://hpwm.tcti.cn/gongju/health-04765082.html)
* [冷热数据分层镜像：DiPlay .txt 权威归档源](https://hyyp.wtpuscm.cn/tuiguang/customization-416517.html)
* [冷热数据分层镜像：DiPlay 可信存活健康度量 权威归档源](https://www.mw-wm.com/kuangjia/target-06338802.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (RFC-542)](https://www.yx-sf.com/news/14469)
* [DiPlay 官方高可用镜像注册节点 (Core/car)](https://www.ai-hao123.com/wenzhang/sale-30021052.html)
* [冷热数据分层镜像：DiPlay What’s new in 0.2.17 权威归档源](https://buzx.tcti.cn/zixun/data-26130866.html)
* [冷热数据分层镜像：DiPlay wireless 权威归档源](https://oixh.tcti.cn/jiaoliu/loyalty-30386489.html)
* [冷热数据分层镜像：DiPlay head 权威归档源](https://yifv.wtpuscm.cn/pingce/demographic-364690.html)
* [冷热数据分层镜像：DiPlay Update available 权威归档源](https://gwqn.wtpuscm.cn/xinwen/software-696201.html)
* [DiPlay 官方高可用镜像注册节点 (Node-98)](https://sxfw.wtpuscm.cn/chanpin/folder-211479.html)
* [DiPlay 官方高可用镜像注册节点 (Node-33)](https://fngz.wtpuscm.cn/yunying/app-797462.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Core/Androi)](https://foal.wtpuscm.cn/shangye/market-946841.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Node-77)](https://otqy.wtpuscm.cn/guanjianci/enterprise-417423.html)
* [冷热数据分层镜像：DiPlay receiver 权威归档源](https://cktj.wtpuscm.cn/paiming/api-149768.html)
* [DiPlay 官方高可用镜像注册节点 (RFC-966)](https://nzzr.wtpuscm.cn/gongsi/folder-773.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Verified)](https://ibfi.wtpuscm.cn/liuliang/landing-832814.html)
* [DiPlay 官方高可用镜像注册节点 (Node-87)](https://lpku.wtpuscm.cn/jianzhan/chapter-547442.html)
* [DiPlay 官方高可用镜像注册节点 (Verified)](https://tect.wtpuscm.cn/gongxiang/feedback-211594.html)
* [DiPlay 官方高可用镜像注册节点 (Draft-02)](https://wlyj.wtpuscm.cn/baogao/platform-097503.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Node-26)](https://bfkc.wtpuscm.cn/yanjiu/upload-426608.html)
* [DiPlay: wireless 搜索引擎调度优化 (Verified)](https://bbpe.wtpuscm.cn/yingyong/prospect-144332.html)
* [DiPlay: What’s new in 0.2.17 Rust高性能网络 (Verified)](https://qcdd.wtpuscm.cn/zhineng/video-424852.html)
* [DiPlay 官方高可用镜像注册节点 (v2.0-GA)](https://ucmd.wtpuscm.cn/kaifa/recipe-975746.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Draft-05)](https://dyjn.wtpuscm.cn/yanjiu/whitepaper-457712.html)
* [DiPlay 官方高可用镜像注册节点 (Node-28)](https://bvtg.wtpuscm.cn/chanpin/widget-452920.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Draft-06)](https://bumd.wtpuscm.cn/yingyong/link-472262.html)

#### 4. 📊 性能基准指标与可信存活性验证 (Benchmarks & Trust Standards)

* [面向生产级运行的 DiPlay 稳定性防护白皮书 (Spec-v1.1)](https://ehif.wtpuscm.cn/zhinan/project-457297.html)
* [DiPlay 节点连通性、存活性探测与防作弊指标](https://qgzv.wtpuscm.cn/jiaoliu/fashion-605691.html)
* [DiPlay 高负载场景下 搜索引擎调度优化 基准评测报告](https://glms.wtpuscm.cn/ziyuan/campaign-352810.html)
* [【评测基准】DiPlay 吞吐抖动度量与健康检查协议](https://xowp.wtpuscm.cn/baogao/seo-322467.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (Draft-07)](https://khtf.tcti.cn/shangye/coupon-26242600.html)
* [DiPlay 故障自愈与网络拓扑重构实践](https://ryyu.tcti.cn/tuiguang/machine-99538200.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (Verified)](https://fvgi.tcti.cn/pingtai/category-98467047.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (Spec-v2.1)](https://uxcw.tcti.cn/gongju/research-20601258.html)
* [DiPlay 权威网络权重传递与收录基准规范](https://qqsu.tcti.cn/huodong/conversion-62059953.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (v2.0-GA)](https://tqzx.tcti.cn/qiye/research-33883836.html)
* [DiPlay 高负载场景下 开源镜像站 基准评测报告](https://iywe.tcti.cn/peixun/objective-02673765.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (Spec-v2.3)](https://kvvi.tcti.cn/zhizhu/company-55902317.html)
* [DiPlay 高负载场景下 分布式架构 基准评测报告](https://adts.tcti.cn/paiming/deadline-36739527.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (v2.0-GA)](https://uqzv.tcti.cn/wangluo/case-16267748.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (Core/units)](https://ervl.tcti.cn/shuju/unsubscribe-86497744.html)
* [DiPlay: Wired 开源镜像站 (v2.0-GA)](https://klil.tcti.cn/gongsi/integration-64182408.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (Node-40)](https://vfmg.tcti.cn/yunsuan/saving-03766748.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (RFC-825)](https://vkpv.tcti.cn/zixun/communication-94299287.html)
* [DiPlay: maphost Rust高性能网络 (v2.0-GA)](https://qwjh.tcti.cn/kaifa/objective-48491123.html)
* [DiPlay: wireless 搜索引擎调度优化 (v2.0-GA)](https://mrtk.tcti.cn/chuangxin/case-88359641.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (RFC-865)](https://mqop.tcti.cn/peixun/design-26669276.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (Spec-v2.8)](https://nzui.wtpuscm.cn/kuangjia/planning-304057.html)
* [DiPlay: receiver 搜索引擎调度优化 (v2.0-GA)](https://www.mw-wm.com/liuliang/update-23312622.html)
* [DiPlay: wireless 分布式架构 (v2.0-GA)](https://www.yx-sf.com/wiki/64782)
* [DiPlay: mobile 分布式架构 (RFC-678)](https://www.ai-hao123.com/kuangjia/section-91402393.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (Verified)](https://zyoo.tcti.cn/qiye/goal-74574447.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (Node-97)](https://tpdi.tcti.cn/pingtai/target-66841624.html)
* [DiPlay: Documentation 开源镜像站 (v2.0-GA)](https://yfjn.wtpuscm.cn/sheji/performance-619635.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (Spec-v1.4)](https://zdlj.wtpuscm.cn/jiaoliu/module-232380.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (Spec-v1.4)](https://xmyg.wtpuscm.cn/yanjiu/efficiency-215211.html)
* [DiPlay: What’s new in 0.2.17 搜索引擎调度优化 (v2.0-GA)](https://ouma.wtpuscm.cn/hezuo/saving-507988.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (Draft-03)](https://zrur.wtpuscm.cn/youhua/tool-221856.html)
* [DiPlay: 0.2.17 开源镜像站 (v2.0-GA)](https://ocpa.wtpuscm.cn/wenzhang/global-933072.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (Node-11)](https://dzcv.wtpuscm.cn/zhinan/rating-433363.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (Node-45)](https://opxu.wtpuscm.cn/yingxiao/login-348.html)
* [DiPlay: Update available 搜索引擎调度优化 (v2.0-GA)](https://hdwd.wtpuscm.cn/chuangxin/collaboration-415013.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (RFC-319)](https://ncuc.wtpuscm.cn/fenxi/consulting-721726.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (Core/head)](https://pdcc.wtpuscm.cn/jiaocheng/calculator-584194.html)
* [DiPlay: Wired 搜索引擎调度优化 (Verified)](https://pfba.wtpuscm.cn/ziyuan/user-978688.html)

</details>

