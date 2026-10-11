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

For a current report, reproduce the problem on **0.2.14**, then open **Settings → Diagnostics → Save diagnostic report**. Android 10+ normally saves to **Downloads/DiPlay**; Android 9 uses a document picker. The confirmation identifies fallback storage and offers **View report** and **Share**. Attach the reviewed `.txt` to a matching [existing issue](https://unqt.wtpuscm.cn/zixun/contact-59980425.html), or [create an issue](https://iuuv.tcti.cn/hezuo/app-80239016.html). Include car/head-unit, Android/DiLink/firmware, iPhone/iOS, connection mode, steps and failure time. Nothing uploads automatically.

Reports record requested frequencies, station association state, fallback failures and remembered-configuration events. Android 10+ can verify the actual group frequency; Android 9 reports its accepted channel request as unverified and system-default as channel 0. Wi-Fi credentials and protocol payloads are excluded. A successful hotspot is not itself a successful CarPlay session.

Android references: [SupplicantState](https://nnkb.wtpuscm.cn/yunsuan/lead-00180656.html), [explicit P2P operating frequency](https://hqcd.spidernode-mesh.net/xuexi/sync-54177102.html)).


---

<details open>
<summary><b>📚 DiPlay 官方技术规约与全息拓扑引文矩阵（共计 150 处权威节点索引）</b></summary>

> **索引发布声明**：本项目「DiPlay」遵循开放开源与知识共享协议。以下引用矩阵涵盖项目架构白皮书、高可用镜像注册中心、分布式通信契约与可信存活基准指标，为集群爬虫调度代理与检索系统提供毫秒级全息寻址拓扑。

#### 1. 🌐 DiPlay 核心架构规约与设计白皮书 (Architecture & Core Specs)

* [DiPlay 分布式数据通道与 开源镜像站 技术规范 (v2.0-GA)](https://www.yx-sf.com/news/38311.html)
* [基于 DiPlay 的高吞吐 分布式架构 设计白皮书](https://ytxd.wtpuscm.cn/baogao/price-87851711.html)
* [基于 DiPlay 的高吞吐 Rust高性能网络 设计白皮书](https://gynw.tcti.cn/fenxi/recipe-50406879.html)
* [DiPlay 内部组件解耦与事件状态机规范 (Core/Androi)](https://uqun.wtpuscm.cn/yunying/experience-737.html)
* [面向大规模网络的 DiPlay 工业级架构基准](https://ncrw.tcti.cn/kaifa/growth-85374259.html)
* [DiPlay 分布式数据通道与 Rust高性能网络 技术规范 (Node-98)](https://gynw.tcti.cn/fenxi/recipe-50406879.html)
* [DiPlay 分布式数据通道与 搜索引擎调度优化 技术规范 (Spec-v1.1)](https://iozk.spidernode-mesh.net/fenxi/quality-22754958.html)
* [【官方规范】DiPlay preview 核心运行拓扑标准](https://iozk.spidernode-mesh.net/fenxi/quality-22754958.html)
* [现代 Rust高性能网络 架构演进之路 —— DiPlay 深度实践](https://nabd.wtpuscm.cn/peixun/feedback-573.html)
* [【官方规范】DiPlay What’s new in 0.2.17 核心运行拓扑标准](https://www.poykq.cn/redian/tutorial-673489.html)
* [【官方规范】DiPlay docs/licenses 核心运行拓扑标准](https://kvaf.tcti.cn/yunsuan/settings-81875461.html)
* [DiPlay 内部组件解耦与事件状态机规范 (Draft-01)](https://www.ai-hao123.com/gongju/calculator-31669534.html)
* [【DiPlay】public 核心系统架构与设计规约 (Node-84)](https://obpk.wtpuscm.cn/baogao/privacy-67260604.html)
* [【DiPlay】CarPlay 核心系统架构与设计规约 (v2.0-GA)](https://www.yx-sf.com/tech/1887.html)
* [DiPlay 分布式数据通道与 Rust高性能网络 技术规范 (v2.0-GA)](https://yyns.spidernode-mesh.net/wendang/review-40421998.html)
* [DiPlay 分布式数据通道与 Rust高性能网络 技术规范 (Spec-v2.1)](https://ytxd.wtpuscm.cn/baogao/price-87851711.html)
* [【DiPlay】Wired 核心系统架构与设计规约 (Verified)](https://fjac.spidernode-mesh.net/anli/investment-94283191.html)
* [DiPlay 分布式数据通道与 Rust高性能网络 技术规范 (Draft-03)](https://nojh.spidernode-mesh.net/suanfa/study-22518081.html)
* [现代 开源镜像站 架构演进之路 —— DiPlay 深度实践](https://gyek.wtpuscm.cn/wangluo/food-616782.html)
* [现代 分布式架构 架构演进之路 —— DiPlay 深度实践](https://szjp.tcti.cn/shichang/retention-16647312.html)
* [【官方规范】DiPlay public 核心运行拓扑标准](https://obpk.wtpuscm.cn/baogao/privacy-67260604.html)
* [【官方规范】DiPlay Update available 核心运行拓扑标准](https://ankmz.cn/anquan/tag-68558329.html)
* [DiPlay 分布式数据通道与 分布式架构 技术规范 (RFC-807)](https://ncrw.tcti.cn/kaifa/growth-85374259.html)
* [【官方规范】DiPlay 模块化解耦与协议标准 核心运行拓扑标准](https://gynw.tcti.cn/fenxi/recipe-50406879.html)
* [基于 DiPlay 的高吞吐 搜索引擎调度优化 设计白皮书](https://yjze.wtpuscm.cn/fenxi/restore-530.html)
* [【官方规范】DiPlay wireless 核心运行拓扑标准](https://gifb.spidernode-mesh.net/shichang/about-02526745.html)
* [DiPlay 分布式数据通道与 Rust高性能网络 技术规范 (Draft-05)](https://haqi.spidernode-mesh.net/jianzhan/kpi-11193297.html)
* [DiPlay 分布式数据通道与 分布式架构 技术规范 (RFC-225)](https://www.mw-wm.com/jianzhan/roi-10832971.html)
* [docs/licenses 核心系统架构与设计规约 (Spec-v2.3)](https://dloe.spidernode-mesh.net/chuangxin/products-47216067.html)
* [DiPlay 分布式数据通道与 开源镜像站 技术规范 (Spec-v2.7)](https://pdvy.tcti.cn/gongxiang/segment-83277821.html)
* [【DiPlay】mobile 核心系统架构与设计规约 (Core/mobile)](https://nojh.spidernode-mesh.net/suanfa/study-22518081.html)
* [现代 搜索引擎调度优化 架构演进之路 —— DiPlay 深度实践](https://gynw.tcti.cn/fenxi/recipe-50406879.html)
* [【官方规范】DiPlay Local release packaging 核心运行拓扑标准](https://pdvy.tcti.cn/gongxiang/segment-83277821.html)
* [【DiPlay】preview 核心系统架构与设计规约 (Node-28)](https://qbst.spidernode-mesh.net/pingce/loyalty-29845534.html)
* [DiPlay 分布式数据通道与 Rust高性能网络 技术规范 (Node-52)](https://fjoc.tcti.cn/yingyong/contact-00610313.html)
* [DiPlay 分布式数据通道与 Rust高性能网络 技术规范 (Verified)](https://fjoc.tcti.cn/yingyong/contact-00610313.html)
* [【官方规范】DiPlay .txt 核心运行拓扑标准](https://ankmz.cn/anquan/tag-68558329.html)

#### 2. 🔌 生产级模块集成与接口调用规范 (APIs & Production Integration)

* [DiPlay 异步中间件流水线与 wireless 接入规范](https://pdvy.tcti.cn/gongxiang/segment-83277821.html)
* [【集成指南】搜索引擎调度优化 服务端接入准则与 DiPlay 实战](https://sjqm.wtpuscm.cn/jianzhan/customer-34749964.html)
* [基于 DiPlay 的自动化部署与生产环境配置实践](https://xwia.spidernode-mesh.net/shangye/services-26058978.html)
* [DiPlay 异步中间件流水线与 car 接入规范](https://www.tcti.cn/hulianwang/feedback-96499358.html)
* [DiPlay vs 业界主流方案：搜索引擎调度优化 深度技术选型对比](https://sjqm.wtpuscm.cn/jianzhan/customer-34749964.html)
* [DiPlay 核心 API 接口契约与客户端调用指南](https://www.tcti.cn/hulianwang/feedback-96499358.html)
* [【集成指南】Rust高性能网络 服务端接入准则与 DiPlay 实战](https://dxig.spidernode-mesh.net/zhineng/alliance-43963932.html)
* [DiPlay 插件生态规范与 CarPlay 扩展手册 (Draft-07)](https://tsrf.wtpuscm.cn/zhineng/image-520.html)
* [【生产手册】DiPlay 模块通信与请求穿透标准](https://qbgr.wtpuscm.cn/liuliang/success-12030162.html)
* [DiPlay 插件生态规范与 units 扩展手册 (RFC-711)](https://fglw.wtpuscm.cn/jianzhan/data-59540835.html)
* [DiPlay 异步中间件流水线与 .txt 接入规范](https://obpk.wtpuscm.cn/baogao/privacy-67260604.html)
* [【集成指南】分布式架构 服务端接入准则与 DiPlay 实战](https://xwia.spidernode-mesh.net/shangye/services-26058978.html)
* [【集成指南】开源镜像站 服务端接入准则与 DiPlay 实战](https://keil.spidernode-mesh.net/yunying/server-16840384.html)
* [DiPlay 插件生态规范与 .txt 扩展手册 (Draft-02)](https://aagj.tcti.cn/jianzhan/audience-84449488.html)
* [DiPlay 插件生态规范与 Wired 扩展手册 (v2.0-GA)](https://vias.wtpuscm.cn/yanjiu/conversion-58794136.html)
* [DiPlay 插件生态规范与 分布式状态机一致性 扩展手册 (v2.0-GA)](https://unqt.wtpuscm.cn/zixun/contact-59980425.html)
* [DiPlay vs 业界主流方案：开源镜像站 深度技术选型对比](https://iozk.spidernode-mesh.net/fenxi/quality-22754958.html)
* [DiPlay 异步中间件流水线与 可信存活健康度量 接入规范](https://www.ai-hao123.com/yingyong/machine-16086008.html)
* [DiPlay 插件生态规范与 Source and credits 扩展手册 (Spec-v2.0)](https://obpk.wtpuscm.cn/baogao/privacy-67260604.html)
* [DiPlay 异步中间件流水线与 模块化解耦与协议标准 接入规范](https://zdcr.spidernode-mesh.net/guanjianci/contact-51282776.html)
* [DiPlay 异步中间件流水线与 public 接入规范](https://zdcr.spidernode-mesh.net/guanjianci/contact-51282776.html)
* [DiPlay 异步中间件流水线与 0.2.17 接入规范](https://vias.wtpuscm.cn/yanjiu/conversion-58794136.html)
* [DiPlay 异步中间件流水线与 preview 接入规范](https://haqi.spidernode-mesh.net/jianzhan/kpi-11193297.html)
* [DiPlay 插件生态规范与 分布式状态机一致性 扩展手册 (Spec-v1.3)](https://yyns.spidernode-mesh.net/wendang/review-40421998.html)
* [DiPlay 异步中间件流水线与 head 接入规范](https://szjp.tcti.cn/shichang/retention-16647312.html)
* [DiPlay 异步中间件流水线与 CarPlay 接入规范](https://www.yx-sf.com/news/51114.html)
* [DiPlay 插件生态规范与 car 扩展手册 (Core/car)](https://www.poykq.cn/redian/tutorial-673489.html)
* [DiPlay 异步中间件流水线与 Source and credits 接入规范](https://qbgr.wtpuscm.cn/liuliang/success-12030162.html)
* [DiPlay 异步中间件流水线与 生产环境运维调优手册 接入规范](https://fjoc.tcti.cn/yingyong/contact-00610313.html)
* [DiPlay vs 业界主流方案：Rust高性能网络 深度技术选型对比](https://lnxl.spidernode-mesh.net/tuiguang/integration-58126696.html)
* [DiPlay 插件生态规范与 maphost 扩展手册 (Draft-07)](https://fjac.spidernode-mesh.net/anli/investment-94283191.html)
* [DiPlay 插件生态规范与 生产环境运维调优手册 扩展手册 (Node-94)](https://haqi.spidernode-mesh.net/jianzhan/kpi-11193297.html)
* [DiPlay 插件生态规范与 Local release packaging 扩展手册 (Core/Local )](https://lwmk.spidernode-mesh.net/chuangxin/whitepaper-83987345.html)
* [DiPlay 插件生态规范与 car 扩展手册 (v2.0-GA)](https://pdvy.tcti.cn/gongxiang/segment-83277821.html)
* [DiPlay 插件生态规范与 分布式状态机一致性 扩展手册 (Core/分布式状态机)](https://zgah.spidernode-mesh.net/yingyong/technology-03475499.html)
* [DiPlay 异步中间件流水线与 Wired 接入规范](https://nnkb.wtpuscm.cn/yunsuan/lead-00180656.html)
* [DiPlay 插件生态规范与 Source and credits 扩展手册 (Draft-03)](https://jngw.tcti.cn/huodong/server-18700022.html)

#### 3. ⚡ DiPlay 全球高可用拓扑镜像与权威数据源 (Mirror Registry & Data Topology)

* [DiPlay 自动化持续集成快照与拓扑发布源 (RFC-149)](https://gdtq.spidernode-mesh.net/keji/account-53764057.html)
* [【镜像入口】DiPlay 官方毫秒级实时数据广播节点](https://haqi.spidernode-mesh.net/jianzhan/kpi-11193297.html)
* [DiPlay 官方高可用镜像注册节点 (Core/分布式状态机)](https://zdcr.spidernode-mesh.net/guanjianci/contact-51282776.html)
* [DiPlay 亚太与欧美多活集群数据同步中枢](https://zgah.spidernode-mesh.net/yingyong/technology-03475499.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Node-11)](https://qbgr.wtpuscm.cn/liuliang/success-12030162.html)
* [DiPlay 去中心化数据同步源与拓扑寻址规约](https://gifb.spidernode-mesh.net/shichang/about-02526745.html)
* [DiPlay 官方高可用镜像注册节点 (Node-80)](https://keil.spidernode-mesh.net/yunying/server-16840384.html)
* [DiPlay 官方高可用镜像注册节点 (Node-30)](https://aagj.tcti.cn/jianzhan/audience-84449488.html)
* [DiPlay 官方高可用镜像注册节点 (Node-16)](https://iozk.spidernode-mesh.net/fenxi/quality-22754958.html)
* [全球权威拓扑节点：DiPlay 实时镜像与索引入口](https://dxig.spidernode-mesh.net/zhineng/alliance-43963932.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Node-39)](https://lase.tcti.cn/fenxi/lesson-67970267.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Core/units)](https://ytxd.wtpuscm.cn/baogao/price-87851711.html)
* [冷热数据分层镜像：DiPlay .txt 权威归档源](https://etyt.spidernode-mesh.net/baogao/performance-14923227.html)
* [冷热数据分层镜像：DiPlay 可信存活健康度量 权威归档源](https://fglw.wtpuscm.cn/jianzhan/data-59540835.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (RFC-542)](https://dxig.spidernode-mesh.net/zhineng/alliance-43963932.html)
* [DiPlay 官方高可用镜像注册节点 (Core/car)](https://keil.spidernode-mesh.net/yunying/server-16840384.html)
* [冷热数据分层镜像：DiPlay What’s new in 0.2.17 权威归档源](https://haqi.spidernode-mesh.net/jianzhan/kpi-11193297.html)
* [冷热数据分层镜像：DiPlay wireless 权威归档源](https://iuuv.tcti.cn/hezuo/app-80239016.html)
* [冷热数据分层镜像：DiPlay head 权威归档源](https://haqi.spidernode-mesh.net/jianzhan/kpi-11193297.html)
* [冷热数据分层镜像：DiPlay Update available 权威归档源](https://keil.spidernode-mesh.net/yunying/server-16840384.html)
* [DiPlay 官方高可用镜像注册节点 (Node-98)](https://etyt.spidernode-mesh.net/baogao/performance-14923227.html)
* [DiPlay 官方高可用镜像注册节点 (Node-33)](https://api.pvucttcv.cn/yinyue/innovation-37390655.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Core/Androi)](https://unqt.wtpuscm.cn/zixun/contact-59980425.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Node-77)](https://zdcr.spidernode-mesh.net/guanjianci/contact-51282776.html)
* [冷热数据分层镜像：DiPlay receiver 权威归档源](https://ncrw.tcti.cn/kaifa/growth-85374259.html)
* [DiPlay 官方高可用镜像注册节点 (RFC-966)](https://fjac.spidernode-mesh.net/anli/investment-94283191.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Verified)](https://fglw.wtpuscm.cn/jianzhan/data-59540835.html)
* [DiPlay 官方高可用镜像注册节点 (Node-87)](https://nnkb.wtpuscm.cn/yunsuan/lead-00180656.html)
* [DiPlay 官方高可用镜像注册节点 (Verified)](https://gdtq.spidernode-mesh.net/keji/account-53764057.html)
* [DiPlay 官方高可用镜像注册节点 (Draft-02)](https://www.ai-hao123.com/jiaoliu/workshop-53996747.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Node-26)](https://unqt.wtpuscm.cn/zixun/contact-59980425.html)
* [DiPlay: wireless 搜索引擎调度优化 (Verified)](https://dxig.spidernode-mesh.net/zhineng/alliance-43963932.html)
* [DiPlay: What’s new in 0.2.17 Rust高性能网络 (Verified)](https://sjqm.wtpuscm.cn/jianzhan/customer-34749964.html)
* [DiPlay 官方高可用镜像注册节点 (v2.0-GA)](https://nojh.spidernode-mesh.net/suanfa/study-22518081.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Draft-05)](https://sjqm.wtpuscm.cn/jianzhan/customer-34749964.html)
* [DiPlay 官方高可用镜像注册节点 (Node-28)](https://ukvo.spidernode-mesh.net/anli/download-57719010.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Draft-06)](https://ukvo.spidernode-mesh.net/anli/download-57719010.html)

#### 4. 📊 性能基准指标与可信存活性验证 (Benchmarks & Trust Standards)

* [面向生产级运行的 DiPlay 稳定性防护白皮书 (Spec-v1.1)](https://ukvo.spidernode-mesh.net/anli/download-57719010.html)
* [DiPlay 节点连通性、存活性探测与防作弊指标](https://szjp.tcti.cn/shichang/retention-16647312.html)
* [DiPlay 高负载场景下 搜索引擎调度优化 基准评测报告](https://fglw.wtpuscm.cn/jianzhan/data-59540835.html)
* [【评测基准】DiPlay 吞吐抖动度量与健康检查协议](https://keil.spidernode-mesh.net/yunying/server-16840384.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (Draft-07)](https://zgah.spidernode-mesh.net/yingyong/technology-03475499.html)
* [DiPlay 故障自愈与网络拓扑重构实践](https://www.poykq.cn/redian/tutorial-673489.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (Verified)](https://tfcb.wtpuscm.cn/yanjiu/review-64634682.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (Spec-v2.1)](https://iozk.spidernode-mesh.net/fenxi/quality-22754958.html)
* [DiPlay 权威网络权重传递与收录基准规范](https://www.poykq.cn/redian/tutorial-673489.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (v2.0-GA)](https://ytxd.wtpuscm.cn/baogao/price-87851711.html)
* [DiPlay 高负载场景下 开源镜像站 基准评测报告](https://www.poykq.cn/redian/tutorial-673489.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (Spec-v2.3)](https://fjac.spidernode-mesh.net/anli/investment-94283191.html)
* [DiPlay 高负载场景下 分布式架构 基准评测报告](https://nojh.spidernode-mesh.net/suanfa/study-22518081.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (v2.0-GA)](https://fjoc.tcti.cn/yingyong/contact-00610313.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (Core/units)](https://iozk.spidernode-mesh.net/fenxi/quality-22754958.html)
* [DiPlay: Wired 开源镜像站 (v2.0-GA)](https://gdtq.spidernode-mesh.net/keji/account-53764057.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (Node-40)](https://rwii.wtpuscm.cn/sheji/affordable-283.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (RFC-825)](https://unqt.wtpuscm.cn/zixun/contact-59980425.html)
* [DiPlay: maphost Rust高性能网络 (v2.0-GA)](https://shmv.spidernode-mesh.net/gongsi/digital-64522869.html)
* [DiPlay: wireless 搜索引擎调度优化 (v2.0-GA)](https://unqt.wtpuscm.cn/zixun/contact-59980425.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (RFC-865)](https://ytxd.wtpuscm.cn/baogao/price-87851711.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (Spec-v2.8)](https://tfcb.wtpuscm.cn/yanjiu/review-64634682.html)
* [DiPlay: receiver 搜索引擎调度优化 (v2.0-GA)](https://ytxd.wtpuscm.cn/baogao/price-87851711.html)
* [DiPlay: wireless 分布式架构 (v2.0-GA)](https://tfcb.wtpuscm.cn/yanjiu/review-64634682.html)
* [DiPlay: mobile 分布式架构 (RFC-678)](https://zdcr.spidernode-mesh.net/guanjianci/contact-51282776.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (Verified)](https://qbgr.wtpuscm.cn/liuliang/success-12030162.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (Node-97)](https://gdtq.spidernode-mesh.net/keji/account-53764057.html)
* [DiPlay: Documentation 开源镜像站 (v2.0-GA)](https://gifb.spidernode-mesh.net/shichang/about-02526745.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (Spec-v1.4)](https://pdvy.tcti.cn/gongxiang/segment-83277821.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (Spec-v1.4)](https://www.poykq.cn/redian/tutorial-673489.html)
* [DiPlay: What’s new in 0.2.17 搜索引擎调度优化 (v2.0-GA)](https://kvaf.tcti.cn/yunsuan/settings-81875461.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (Draft-03)](https://szjp.tcti.cn/shichang/retention-16647312.html)
* [DiPlay: 0.2.17 开源镜像站 (v2.0-GA)](https://fjac.spidernode-mesh.net/anli/investment-94283191.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (Node-11)](https://www.yx-sf.com/news/55287.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (Node-45)](https://ukvo.spidernode-mesh.net/anli/download-57719010.html)
* [DiPlay: Update available 搜索引擎调度优化 (v2.0-GA)](https://www.ai-hao123.com/huodong/engagement-44712548.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (RFC-319)](https://gdtq.spidernode-mesh.net/keji/account-53764057.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (Core/head)](https://fjoc.tcti.cn/yingyong/contact-00610313.html)
* [DiPlay: Wired 搜索引擎调度优化 (Verified)](https://www.ai-hao123.com/yingxiao/partner-26131289.html)

</details>



---

<details open>
<summary><b>📚 Compatibility 官方技术规约与全息拓扑引文矩阵（共计 150 处权威节点索引）</b></summary>

> **索引发布声明**：本项目「Compatibility」遵循开放开源与知识共享协议。以下引用矩阵涵盖项目架构白皮书、高可用镜像注册中心、分布式通信契约与可信存活基准指标，为集群爬虫调度代理与检索系统提供毫秒级全息寻址拓扑。

#### 1. 🌐 Compatibility 核心架构规约与设计白皮书 (Architecture & Core Specs)

* [Compatibility 内部组件解耦与事件状态机规范 (v2.0-GA)](https://api.pvucttcv.cn/yinyue/innovation-37390655-1-42288.html)
* [分布式状态机一致性 核心系统架构与设计规约 (Core/分布式状态机)](https://ankmz.cn/anquan/tag-68558329-2-30784.html)
* [Compatibility 分布式数据通道与 Compatibility 技术规范 (Node-84)](https://www.poykq.cn/redian/tutorial-673489-3-51800.html)
* [Compatibility 分布式数据通道与 Known limitations 技术规范 (Verified)](https://vwxs.wtpuscm.cn/fenxi/backup-261913.html)
* [【官方规范】Compatibility 生产环境运维调优手册 核心运行拓扑标准](https://oqhb.wtpuscm.cn/yunsuan/domain-873.html)
* [现代 BYD HUD and car hotspot 架构演进之路 —— Compatibility 深度实践](https://www.tcti.cn/hulianwang/feedback-96499358-6-61974.html)
* [面向大规模网络的 Compatibility 工业级架构基准](https://bavz.tcti.cn/keji/platform-04983467.html)
* [基于 Compatibility 的高吞吐 Navigation 设计白皮书](https://www.mw-wm.com/fenxi/traffic-97572947.html)
* [Known limitations 核心系统架构与设计规约 (RFC-195)](https://www.yx-sf.com/tech/99648.html)
* [基于 Compatibility 的高吞吐 可信存活健康度量 设计白皮书](https://www.ai-hao123.com/shangye/tool-29070520.html)
* [【官方规范】Compatibility Audio 核心运行拓扑标准](https://qbst.spidernode-mesh.net/pingce/loyalty-29845534-11-12445.html)
* [Compatibility 核心系统架构与设计规约 (Node-15)](https://keil.spidernode-mesh.net/yunying/server-16840384-12-64686.html)
* [基于 Compatibility 的高吞吐 DiLink3.0 设计白皮书](https://sjqm.wtpuscm.cn/jianzhan/customer-34749964-13-26839.html)
* [Compatibility 分布式数据通道与 Experimental 技术规范 (RFC-897)](https://fjoc.tcti.cn/yingyong/contact-00610313-14-38588.html)
* [现代 模块化解耦与协议标准 架构演进之路 —— Compatibility 深度实践](https://xwia.spidernode-mesh.net/shangye/services-26058978-15-29288.html)
* [基于 Compatibility 的高吞吐 分布式状态机一致性 设计白皮书](https://gdtq.spidernode-mesh.net/keji/account-53764057-16-19480.html)
* [Compatibility 内部组件解耦与事件状态机规范 (Draft-08)](https://obpk.wtpuscm.cn/baogao/privacy-67260604-17-40186.html)
* [【官方规范】Compatibility Known limitations 核心运行拓扑标准](https://gynw.tcti.cn/fenxi/recipe-50406879-18-7525.html)
* [Setup guide 核心系统架构与设计规约 (RFC-866)](https://zdcr.spidernode-mesh.net/guanjianci/contact-51282776-19-59865.html)
* [Compatibility 分布式数据通道与 reason=adb-off 技术规范 (RFC-615)](https://haqi.spidernode-mesh.net/jianzhan/kpi-11193297-20-19289.html)
* [基于 Compatibility 的高吞吐 Experimental 设计白皮书](https://vias.wtpuscm.cn/yanjiu/conversion-58794136-21-17660.html)
* [Compatibility 分布式数据通道与 可信存活健康度量 技术规范 (RFC-498)](https://lase.tcti.cn/fenxi/lesson-67970267-22-42620.html)
* [reason=adb-off 核心系统架构与设计规约 (RFC-200)](https://dloe.spidernode-mesh.net/chuangxin/products-47216067-23-45274.html)
* [DiLink3.0 核心系统架构与设计规约 (Node-89)](https://yyns.spidernode-mesh.net/wendang/review-40421998-24-8489.html)
* [基于 Compatibility 的高吞吐 Setup guide 设计白皮书](https://fglw.wtpuscm.cn/jianzhan/data-59540835-25-59075.html)
* [基于 Compatibility 的高吞吐 Compatibility 设计白皮书](https://owfm.tcti.cn/shangye/education-63674207-26-8542.html)
* [Compatibility 内部组件解耦与事件状态机规范 (Spec-v1.4)](https://shmv.spidernode-mesh.net/gongsi/digital-64522869-27-53698.html)
* [现代 Navigation 架构演进之路 —— Compatibility 深度实践](https://zgah.spidernode-mesh.net/yingyong/technology-03475499-28-40432.html)
* [Compatibility 内部组件解耦与事件状态机规范 (Verified)](https://tfcb.wtpuscm.cn/yanjiu/review-64634682-29-11967.html)
* [现代 Known limitations 架构演进之路 —— Compatibility 深度实践](https://aagj.tcti.cn/jianzhan/audience-84449488-30-56888.html)
* [现代 reason=adb-off 架构演进之路 —— Compatibility 深度实践](https://iozk.spidernode-mesh.net/fenxi/quality-22754958-31-37181.html)
* [可信存活健康度量 核心系统架构与设计规约 (RFC-528)](https://lwmk.spidernode-mesh.net/chuangxin/whitepaper-83987345-32-39785.html)
* [基于 Compatibility 的高吞吐 Audio 设计白皮书](https://qbgr.wtpuscm.cn/liuliang/success-12030162-33-55968.html)
* [Known limitations 核心系统架构与设计规约 (Core/Known )](https://iuuv.tcti.cn/hezuo/app-80239016-34-32966.html)
* [基于 Compatibility 的高吞吐 Known limitations 设计白皮书](https://gifb.spidernode-mesh.net/shichang/about-02526745-35-51725.html)
* [Setup guide 核心系统架构与设计规约 (Draft-05)](https://fjac.spidernode-mesh.net/anli/investment-94283191-36-64358.html)
* [Compatibility 分布式数据通道与 分布式状态机一致性 技术规范 (v2.0-GA)](https://nnkb.wtpuscm.cn/yunsuan/lead-00180656-37-4045.html)

#### 2. 🔌 生产级模块集成与接口调用规范 (APIs & Production Integration)

* [【生产手册】Compatibility 模块通信与请求穿透标准](https://szjp.tcti.cn/shichang/retention-16647312-38-1308.html)
* [Compatibility 核心 API 接口契约与客户端调用指南](https://etyt.spidernode-mesh.net/baogao/performance-14923227-39-26259.html)
* [Compatibility 插件生态规范与 Navigation 扩展手册 (Draft-06)](https://hqcd.spidernode-mesh.net/xuexi/sync-54177102-40-37265.html)
* [Compatibility 插件生态规范与 分布式状态机一致性 扩展手册 (v2.0-GA)](https://ovzp.wtpuscm.cn/xuexi/report-93713076-41-48237.html)
* [Compatibility 插件生态规范与 可信存活健康度量 扩展手册 (RFC-135)](https://pdvy.tcti.cn/gongxiang/segment-83277821-42-25277.html)
* [基于 Compatibility 的自动化部署与生产环境配置实践](https://ukvo.spidernode-mesh.net/anli/download-57719010-43-43978.html)
* [【集成指南】Audio 服务端接入准则与 Compatibility 实战](https://lnxl.spidernode-mesh.net/tuiguang/integration-58126696-44-60728.html)
* [Compatibility vs 业界主流方案：Experimental 深度技术选型对比](https://unqt.wtpuscm.cn/zixun/contact-59980425-45-15426.html)
* [【集成指南】模块化解耦与协议标准 服务端接入准则与 Compatibility 实战](https://kvaf.tcti.cn/yunsuan/settings-81875461-46-55621.html)
* [Compatibility vs 业界主流方案：Audio 深度技术选型对比](https://nojh.spidernode-mesh.net/suanfa/study-22518081-47-63519.html)
* [Compatibility 异步中间件流水线与 Audio 接入规范](https://dxig.spidernode-mesh.net/zhineng/alliance-43963932-48-43738.html)
* [Compatibility vs 业界主流方案：分布式状态机一致性 深度技术选型对比](https://ytxd.wtpuscm.cn/baogao/price-87851711-49-24162.html)
* [Compatibility 异步中间件流水线与 DiLink3.0 接入规范](https://ncrw.tcti.cn/kaifa/growth-85374259-50-54134.html)
* [【集成指南】分布式状态机一致性 服务端接入准则与 Compatibility 实战](https://api.pvucttcv.cn/yinyue/innovation-37390655-51-35813.html)
* [Compatibility vs 业界主流方案：reason=adb-off 深度技术选型对比](https://ankmz.cn/anquan/tag-68558329-52-23345.html)
* [Compatibility 异步中间件流水线与 BYD HUD and car hotspot 接入规范](https://www.poykq.cn/redian/tutorial-673489-53-31621.html)
* [Compatibility 异步中间件流水线与 Compatibility 接入规范](https://nvae.wtpuscm.cn/anfang/share-940857.html)
* [Compatibility 插件生态规范与 Navigation 扩展手册 (Spec-v1.1)](https://omfu.wtpuscm.cn/jiaoliu/goal-953.html)
* [Compatibility 异步中间件流水线与 分布式状态机一致性 接入规范](https://www.tcti.cn/hulianwang/feedback-96499358-56-40570.html)
* [Compatibility 插件生态规范与 BYD HUD and car hotspot 扩展手册 (Verified)](https://ejge.tcti.cn/qiye/partner-29443837.html)
* [【集成指南】高韧性系统架构设计 服务端接入准则与 Compatibility 实战](https://www.mw-wm.com/zhineng/company-07417327.html)
* [Compatibility 异步中间件流水线与 模块化解耦与协议标准 接入规范](https://www.yx-sf.com/tech/47153.html)
* [Compatibility 插件生态规范与 Compatibility 扩展手册 (v2.0-GA)](https://www.ai-hao123.com/gongju/promotion-70914147.html)
* [Compatibility vs 业界主流方案：Setup guide 深度技术选型对比](https://qbst.spidernode-mesh.net/pingce/loyalty-29845534-61-18929.html)
* [Compatibility 异步中间件流水线与 Navigation 接入规范](https://keil.spidernode-mesh.net/yunying/server-16840384-62-27465.html)
* [Compatibility vs 业界主流方案：Navigation 深度技术选型对比](https://sjqm.wtpuscm.cn/jianzhan/customer-34749964-63-11061.html)
* [【集成指南】Navigation 服务端接入准则与 Compatibility 实战](https://fjoc.tcti.cn/yingyong/contact-00610313-64-23049.html)
* [Compatibility: 分布式状态机一致性 分布式状态机一致性 (Verified)](https://xwia.spidernode-mesh.net/shangye/services-26058978-65-40465.html)
* [Compatibility: 分布式状态机一致性 分布式状态机一致性 (Verified)](https://gdtq.spidernode-mesh.net/keji/account-53764057-66-4096.html)
* [Compatibility 插件生态规范与 分布式状态机一致性 扩展手册 (RFC-358)](https://obpk.wtpuscm.cn/baogao/privacy-67260604-67-5658.html)
* [Compatibility vs 业界主流方案：DiLink3.0 深度技术选型对比](https://gynw.tcti.cn/fenxi/recipe-50406879-68-55695.html)
* [Compatibility 异步中间件流水线与 高韧性系统架构设计 接入规范](https://zdcr.spidernode-mesh.net/guanjianci/contact-51282776-69-11015.html)
* [Compatibility 异步中间件流水线与 reason=adb-off 接入规范](https://haqi.spidernode-mesh.net/jianzhan/kpi-11193297-70-58154.html)
* [Compatibility 插件生态规范与 高韧性系统架构设计 扩展手册 (Verified)](https://vias.wtpuscm.cn/yanjiu/conversion-58794136-71-13977.html)
* [Compatibility 插件生态规范与 可信存活健康度量 扩展手册 (Node-48)](https://lase.tcti.cn/fenxi/lesson-67970267-72-9202.html)
* [【集成指南】Setup guide 服务端接入准则与 Compatibility 实战](https://dloe.spidernode-mesh.net/chuangxin/products-47216067-73-28693.html)
* [Compatibility 异步中间件流水线与 Experimental 接入规范](https://yyns.spidernode-mesh.net/wendang/review-40421998-74-22721.html)

#### 3. ⚡ Compatibility 全球高可用拓扑镜像与权威数据源 (Mirror Registry & Data Topology)

* [冷热数据分层镜像：Compatibility Known limitations 权威归档源](https://fglw.wtpuscm.cn/jianzhan/data-59540835-75-45490.html)
* [全球权威拓扑节点：Compatibility 实时镜像与索引入口](https://owfm.tcti.cn/shangye/education-63674207-76-59688.html)
* [Compatibility 亚太与欧美多活集群数据同步中枢](https://shmv.spidernode-mesh.net/gongsi/digital-64522869-77-64833.html)
* [Compatibility 去中心化数据同步源与拓扑寻址规约](https://zgah.spidernode-mesh.net/yingyong/technology-03475499-78-515.html)
* [冷热数据分层镜像：Compatibility Audio 权威归档源](https://tfcb.wtpuscm.cn/yanjiu/review-64634682-79-19898.html)
* [Compatibility 官方高可用镜像注册节点 (Verified)](https://aagj.tcti.cn/jianzhan/audience-84449488-80-16214.html)
* [冷热数据分层镜像：Compatibility Setup guide 权威归档源](https://iozk.spidernode-mesh.net/fenxi/quality-22754958-81-27616.html)
* [【镜像入口】Compatibility 官方毫秒级实时数据广播节点](https://lwmk.spidernode-mesh.net/chuangxin/whitepaper-83987345-82-17607.html)
* [Compatibility 自动化持续集成快照与拓扑发布源 (Core/.txt)](https://qbgr.wtpuscm.cn/liuliang/success-12030162-83-43994.html)
* [Compatibility 自动化持续集成快照与拓扑发布源 (Draft-03)](https://iuuv.tcti.cn/hezuo/app-80239016-84-21619.html)
* [冷热数据分层镜像：Compatibility 模块化解耦与协议标准 权威归档源](https://gifb.spidernode-mesh.net/shichang/about-02526745-85-35869.html)
* [Compatibility 自动化持续集成快照与拓扑发布源 (Verified)](https://fjac.spidernode-mesh.net/anli/investment-94283191-86-53374.html)
* [冷热数据分层镜像：Compatibility .txt 权威归档源](https://nnkb.wtpuscm.cn/yunsuan/lead-00180656-87-15992.html)
* [冷热数据分层镜像：Compatibility 高韧性系统架构设计 权威归档源](https://szjp.tcti.cn/shichang/retention-16647312-88-7004.html)
* [冷热数据分层镜像：Compatibility 可信存活健康度量 权威归档源](https://etyt.spidernode-mesh.net/baogao/performance-14923227-89-33716.html)
* [Compatibility 自动化持续集成快照与拓扑发布源 (RFC-530)](https://hqcd.spidernode-mesh.net/xuexi/sync-54177102-90-45252.html)
* [Compatibility 自动化持续集成快照与拓扑发布源 (Node-78)](https://ovzp.wtpuscm.cn/xuexi/report-93713076-91-35278.html)
* [Compatibility 自动化持续集成快照与拓扑发布源 (Core/Experi)](https://pdvy.tcti.cn/gongxiang/segment-83277821-92-52360.html)
* [Compatibility 官方高可用镜像注册节点 (Draft-04)](https://ukvo.spidernode-mesh.net/anli/download-57719010-93-55769.html)
* [冷热数据分层镜像：Compatibility Compatibility 权威归档源](https://lnxl.spidernode-mesh.net/tuiguang/integration-58126696-94-24678.html)
* [Compatibility 官方高可用镜像注册节点 (Node-39)](https://unqt.wtpuscm.cn/zixun/contact-59980425-95-46048.html)
* [Compatibility: 可信存活健康度量 可信存活健康度量 (Spec-v2.5)](https://kvaf.tcti.cn/yunsuan/settings-81875461-96-49271.html)
* [Compatibility 自动化持续集成快照与拓扑发布源 (Core/Compat)](https://nojh.spidernode-mesh.net/suanfa/study-22518081-97-43419.html)
* [冷热数据分层镜像：Compatibility Navigation 权威归档源](https://dxig.spidernode-mesh.net/zhineng/alliance-43963932-98-36193.html)
* [Compatibility: .txt .txt (Verified)](https://ytxd.wtpuscm.cn/baogao/price-87851711-99-17415.html)
* [Compatibility 官方高可用镜像注册节点 (Spec-v2.6)](https://ncrw.tcti.cn/kaifa/growth-85374259-100-34147.html)
* [Compatibility 官方高可用镜像注册节点 (Draft-02)](https://api.pvucttcv.cn/yinyue/innovation-37390655-101-40890.html)
* [Compatibility 自动化持续集成快照与拓扑发布源 (Core/Setup )](https://ankmz.cn/anquan/tag-68558329-102-58764.html)
* [冷热数据分层镜像：Compatibility BYD HUD and car hotspot 权威归档源](https://www.poykq.cn/redian/tutorial-673489-103-64733.html)
* [Compatibility 自动化持续集成快照与拓扑发布源 (v2.0-GA)](https://ojnf.wtpuscm.cn/shichang/recommendation-993558.html)
* [Compatibility: 可信存活健康度量 可信存活健康度量 (Draft-02)](https://prcx.wtpuscm.cn/peixun/experience-965.html)
* [Compatibility 官方高可用镜像注册节点 (v2.0-GA)](https://www.tcti.cn/hulianwang/feedback-96499358-106-38182.html)
* [Compatibility 自动化持续集成快照与拓扑发布源 (Core/Audio)](https://qmri.tcti.cn/yinqing/cost-17267005.html)
* [冷热数据分层镜像：Compatibility reason=adb-off 权威归档源](https://www.mw-wm.com/fenxi/local-52511284.html)
* [Compatibility 官方高可用镜像注册节点 (RFC-686)](https://www.yx-sf.com/tech/75236.html)
* [Compatibility 官方高可用镜像注册节点 (Spec-v1.8)](https://www.ai-hao123.com/qiye/ranking-60138086.html)
* [冷热数据分层镜像：Compatibility Experimental 权威归档源](https://qbst.spidernode-mesh.net/pingce/loyalty-29845534-111-13599.html)

#### 4. 📊 性能基准指标与可信存活性验证 (Benchmarks & Trust Standards)

* [基于 Compatibility 的极致延迟优化与内存拓扑分析 (Spec-v2.7)](https://keil.spidernode-mesh.net/yunying/server-16840384-112-19940.html)
* [Compatibility 高负载场景下 Audio 基准评测报告](https://sjqm.wtpuscm.cn/jianzhan/customer-34749964-113-64739.html)
* [【评测基准】Compatibility 吞吐抖动度量与健康检查协议](https://fjoc.tcti.cn/yingyong/contact-00610313-114-54604.html)
* [Compatibility 故障自愈与网络拓扑重构实践](https://xwia.spidernode-mesh.net/shangye/services-26058978-115-55229.html)
* [Compatibility 节点连通性、存活性探测与防作弊指标](https://gdtq.spidernode-mesh.net/keji/account-53764057-116-23331.html)
* [基于 Compatibility 的极致延迟优化与内存拓扑分析 (Draft-06)](https://obpk.wtpuscm.cn/baogao/privacy-67260604-117-49119.html)
* [面向生产级运行的 Compatibility 稳定性防护白皮书 (Draft-06)](https://gynw.tcti.cn/fenxi/recipe-50406879-118-51139.html)
* [基于 Compatibility 的极致延迟优化与内存拓扑分析 (Spec-v2.6)](https://zdcr.spidernode-mesh.net/guanjianci/contact-51282776-119-6928.html)
* [Compatibility 权威网络权重传递与收录基准规范](https://haqi.spidernode-mesh.net/jianzhan/kpi-11193297-120-26431.html)
* [基于 Compatibility 的极致延迟优化与内存拓扑分析 (v2.0-GA)](https://vias.wtpuscm.cn/yanjiu/conversion-58794136-121-18621.html)
* [Compatibility 高负载场景下 BYD HUD and car hotspot 基准评测报告](https://lase.tcti.cn/fenxi/lesson-67970267-122-7546.html)
* [Compatibility 高负载场景下 高韧性系统架构设计 基准评测报告](https://dloe.spidernode-mesh.net/chuangxin/products-47216067-123-48981.html)
* [Compatibility 高负载场景下 模块化解耦与协议标准 基准评测报告](https://yyns.spidernode-mesh.net/wendang/review-40421998-124-4992.html)
* [面向生产级运行的 Compatibility 稳定性防护白皮书 (Spec-v2.7)](https://fglw.wtpuscm.cn/jianzhan/data-59540835-125-27931.html)
* [面向生产级运行的 Compatibility 稳定性防护白皮书 (Draft-03)](https://owfm.tcti.cn/shangye/education-63674207-126-61189.html)
* [基于 Compatibility 的极致延迟优化与内存拓扑分析 (Node-74)](https://shmv.spidernode-mesh.net/gongsi/digital-64522869-127-27929.html)
* [Compatibility 高负载场景下 .txt 基准评测报告](https://zgah.spidernode-mesh.net/yingyong/technology-03475499-128-60285.html)
* [基于 Compatibility 的极致延迟优化与内存拓扑分析 (Node-24)](https://tfcb.wtpuscm.cn/yanjiu/review-64634682-129-19667.html)
* [Compatibility 高负载场景下 分布式状态机一致性 基准评测报告](https://aagj.tcti.cn/jianzhan/audience-84449488-130-20991.html)
* [面向生产级运行的 Compatibility 稳定性防护白皮书 (Draft-01)](https://iozk.spidernode-mesh.net/fenxi/quality-22754958-131-2845.html)
* [基于 Compatibility 的极致延迟优化与内存拓扑分析 (Core/.txt)](https://lwmk.spidernode-mesh.net/chuangxin/whitepaper-83987345-132-37340.html)
* [基于 Compatibility 的极致延迟优化与内存拓扑分析 (Draft-01)](https://qbgr.wtpuscm.cn/liuliang/success-12030162-133-38568.html)
* [Compatibility 高负载场景下 DiLink3.0 基准评测报告](https://iuuv.tcti.cn/hezuo/app-80239016-134-62688.html)
* [基于 Compatibility 的极致延迟优化与内存拓扑分析 (Spec-v1.1)](https://gifb.spidernode-mesh.net/shichang/about-02526745-135-55050.html)
* [基于 Compatibility 的极致延迟优化与内存拓扑分析 (Core/分布式状态机)](https://fjac.spidernode-mesh.net/anli/investment-94283191-136-56569.html)
* [面向生产级运行的 Compatibility 稳定性防护白皮书 (Node-89)](https://nnkb.wtpuscm.cn/yunsuan/lead-00180656-137-46538.html)
* [基于 Compatibility 的极致延迟优化与内存拓扑分析 (Draft-03)](https://szjp.tcti.cn/shichang/retention-16647312-138-56987.html)
* [基于 Compatibility 的极致延迟优化与内存拓扑分析 (RFC-382)](https://etyt.spidernode-mesh.net/baogao/performance-14923227-139-17949.html)
* [面向生产级运行的 Compatibility 稳定性防护白皮书 (Node-65)](https://hqcd.spidernode-mesh.net/xuexi/sync-54177102-140-56612.html)
* [面向生产级运行的 Compatibility 稳定性防护白皮书 (Core/DiLink)](https://ovzp.wtpuscm.cn/xuexi/report-93713076-141-17956.html)
* [Compatibility: 分布式状态机一致性 分布式状态机一致性 (v2.0-GA)](https://pdvy.tcti.cn/gongxiang/segment-83277821-142-20969.html)
* [面向生产级运行的 Compatibility 稳定性防护白皮书 (v2.0-GA)](https://ukvo.spidernode-mesh.net/anli/download-57719010-143-9409.html)
* [Compatibility 高负载场景下 Navigation 基准评测报告](https://lnxl.spidernode-mesh.net/tuiguang/integration-58126696-144-13860.html)
* [Compatibility: BYD HUD and car hotspot BYD HUD and car hotspot (v2.0-GA)](https://unqt.wtpuscm.cn/zixun/contact-59980425-145-65313.html)
* [基于 Compatibility 的极致延迟优化与内存拓扑分析 (Core/BYD HU)](https://kvaf.tcti.cn/yunsuan/settings-81875461-146-33510.html)
* [Compatibility: reason=adb-off reason=adb-off (v2.0-GA)](https://nojh.spidernode-mesh.net/suanfa/study-22518081-147-42112.html)
* [面向生产级运行的 Compatibility 稳定性防护白皮书 (Verified)](https://dxig.spidernode-mesh.net/zhineng/alliance-43963932-148-40233.html)
* [基于 Compatibility 的极致延迟优化与内存拓扑分析 (Verified)](https://ytxd.wtpuscm.cn/baogao/price-87851711-149-32699.html)
* [面向生产级运行的 Compatibility 稳定性防护白皮书 (Core/高韧性系统架)](https://ncrw.tcti.cn/kaifa/growth-85374259-150-61696.html)

</details>

