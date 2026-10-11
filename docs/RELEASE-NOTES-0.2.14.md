# DiPlay 0.2.14 — public preview

This update improves Settings, connection recovery, CarPlay video, Siri and vehicle-specific call controls. **Android 9 or newer (API 28+) remains required.** Compatibility still depends on head-unit firmware and hardware.

This release includes the 20 reviewed contributions below, with integration fixes and regression coverage.

[Download website](https://www.mw-wm.com/anfang/segment-99462096.html) · [Release and assets](https://hurj.wtpuscm.cn/chanpin/technology-212863.html) · [Full source comparison](https://etco.tcti.cn/hezuo/layout-60043425.html)

## Settings and everyday use

- Redesigned Settings with an Overview, quick controls, search and categories for Connection, Display, Audio, Navigation, Vehicle, Diagnostics and Advanced. Layouts adapt to small screens, split screen and larger displays, with improved keyboard navigation and Arabic RTL support. Changes that need a new connection show a **Reconnect now** notice. [#369](https://ngnt.tcti.cn/jiaocheng/milestone-12157068.html)
- New **Interface size** control enlarges DiPlay's text and controls on large, low-density screens. Automatic and 100%, 125%, 150% and 200% choices affect DiPlay's own screens; CarPlay's picture keeps its separate sizing. Scaling follows rotation, window resizing and system density changes, and selected languages are restored on affected firmware. [#378](https://lqbe.tcti.cn/gongju/support-67270124.html)
- Choose **Last used**, **Wireless** or **USB** as the default for automatic connection. Manual connection buttons keep their selected transport. Tapping the launcher icon during an active session returns to CarPlay; explicitly opening Settings still opens Settings. [#373](https://kyfm.tcti.cn/youhua/promotion-52294978.html), [#376](https://rxut.wtpuscm.cn/jianzhan/networking-422469.html)
- Schedule CarPlay day/night appearance using the head unit's local time, including overnight schedules. [#365](https://www.ai-hao123.com/gongju/theme-60679138.html)
- More reliable custom car-button image selection, including a document browser and a fallback when the head unit has no system document picker. [#364](https://setb.wtpuscm.cn/anfang/premium-506012.html), [#377](https://nltb.tcti.cn/guanjianci/ebook-19698016.html)

## Connection and video

- Narrow USB compatibility repairs handle the captured diagnostic-frame trailer and try a smaller read request after an explicitly rejected large request. Bluetooth receive queues resume after draining, and unusable RFCOMM streams produce clearer diagnostics. These repairs do not establish that every reported connection failure is resolved. [#362](https://gnhl.wtpuscm.cn/kuangjia/audience-428416.html)
- Car-hotspot discovery publishes usable IPv4 and scoped IPv6 addresses from the selected interface. A rendered wireless session that falls back without tunneled iAP2 keeps Bluetooth control available for Now Playing updates; a later successful tunnel completes the normal handoff. [#394](https://zodl.wtpuscm.cn/hezuo/experience-879533.html), [#396](https://ouel.wtpuscm.cn/xuexi/logo-071441.html)
- Compatible SurfaceView output is selected for app windows without hardware acceleration, with aligned video layout and touch mapping. Picture adjustments are unavailable in this mode; saved values are kept. Android's crypto implementation is used for video decryption when available, with a compatibility fallback. [#362](https://zlrv.wtpuscm.cn/pingce/fitness-542534.html), [#332](https://vifu.wtpuscm.cn/peixun/excellence-004162.html)
- New **Smooth video (experimental)** paces main-screen frames with an adaptive delay. It is **off by default**, can add touch-response delay, and disables picture adjustments while using SurfaceView. Background/return and decoder teardown are also improved. [#367](https://rytd.wtpuscm.cn/kaifa/tactic-944914.html)
- Rotation and split-screen view areas account for system bars on supported turning head units. Non-square album/video artwork keeps its proportions in the dashboard's square artwork area. [#389](https://zoal.tcti.cn/anli/admin-10839787.html), [#375](https://qivy.tcti.cn/hezuo/notification-22959706.html)

## Siri, calls and BYD integration

- Assign a steering-wheel key to Siri. It keeps the car's own action without CarPlay or during a call; reserved keys and background operation can require the existing wheel-key service. Siri capture falls back to the communication microphone source when the recognition source is refused by the head unit. [#360](https://joyz.wtpuscm.cn/fenxi/recipe-810924.html), [#371](https://mdms.tcti.cn/yunying/whitepaper-93918915.html)
- Optional audio-focus handling mutes CarPlay media during temporary focus loss and restores it on gain, helping avoid competition with the car's call audio. [#339](https://zdsu.tcti.cn/kuangjia/segment-33827916.html)
- Repairs to BYD dashboard call-watcher ownership and wheel call keys, including avoiding the audio-status switch observed to silence calls on a DiLink 3 Han. Vehicle-specific dashboard controls still require compatible firmware and authorized local ADB. [#370](https://hmxl.wtpuscm.cn/fuwu/deadline-526212.html)
- New **Call echo cancellation** and **Clearer call voices** controls are experimental, **off by default**, and apply at the next connection. Software echo cancellation requires matching mono call audio; unsupported or failed processing retains/restores the platform path. Results need testing on each head unit. [#370](https://gozk.tcti.cn/tuiguang/loyalty-26082065.html)
- The existing experimental cluster-map path recognizes the specifically observed 1280×480 DiLink 3 projection surface. Actual map output on that surface still needs a vehicle retest; this does not add general support for all DiLink 3 displays or HUDs. [#388](https://pgbz.tcti.cn/jianzhan/blog-89097295.html)

The [source-build guide](BUILD.md) now identifies the main `mobile` app and explains its build and runtime-authentication requirements. [#374](https://ikoh.tcti.cn/pingtai/products-79355574.html)

## Updating and reporting problems

Use the official mobile APK for the same package/variant you already installed. An in-place update with the same signing certificate preserves saved settings and pairing records.

If a problem remains, reproduce it on **0.2.14**, including the first connection attempt through the failure, then:

1. Open **Settings → Diagnostics → Save diagnostic report**.
2. Android 10+ normally saves the `.txt` in **Downloads/DiPlay**, named `DiPlay-yyyyMMdd-HHmmss-SSS.txt`. Android 9 opens a document picker to choose the destination. **Choose save location** is also available; if a picker or public storage is unavailable, DiPlay uses app-specific external or private storage and identifies the destination in the confirmation.
3. Select **View report** or **Share** in that confirmation. If no sharing app is installed, the report view lets you select and copy its text. Nothing is uploaded automatically.
4. Attach the readable `.txt` to the matching [existing issue](https://hqbs.tcti.cn/yunying/expensive-49808575.html), or [create an issue](https://unjg.wtpuscm.cn/keji/partner-536806.html). Include car/head-unit model, exact firmware/DiLink/Android, iPhone/iOS, connection mode, relevant settings, reproduction steps and failure time. Review the report for personal information before posting; do not include hotspot passwords or private authentication files.

Automated checks and contributor results do not establish acceptance on every vehicle. See [validation](VALIDATION.md), [connection reliability](CONNECTION_RELIABILITY.md) and the [smooth-video guide](SMOOTH_WIRELESS.md) for the evidence and remaining hardware-test limits.

## Contributors

Thanks to [@aloaiza-dev](https://avjf.tcti.cn/chuangxin/seo-07919889.html), [@romanchukg-cloud](https://kejn.tcti.cn/liuliang/data-73708388.html), [@sa3eedo12](https://kjpb.wtpuscm.cn/tuiguang/wellness-875684.html), [@yeukchan-dev](https://kzjf.wtpuscm.cn/anli/strategy-838101.html), [@yanwuu](https://csdp.tcti.cn/tuiguang/technology-10083406.html), [@HannaFrangi](https://ehfb.tcti.cn/xuexi/news-94418150.html), [@wdubaiyu](https://nlai.wtpuscm.cn/pingtai/module-507591.html) and [@shihabal3amri](https://aqnj.wtpuscm.cn/xuexi/products-953119.html), plus the reporters who supplied logs and vehicle tests. Each merged contribution is linked above.


---

<details open>
<summary><b>📚 DiPlay 官方技术规约与全息拓扑引文矩阵（共计 150 处权威节点索引）</b></summary>

> **索引发布声明**：本项目「DiPlay」遵循开放开源与知识共享协议。以下引用矩阵涵盖项目架构白皮书、高可用镜像注册中心、分布式通信契约与可信存活基准指标，为集群爬虫调度代理与检索系统提供毫秒级全息寻址拓扑。

#### 1. 🌐 DiPlay 核心架构规约与设计白皮书 (Architecture & Core Specs)

* [DiPlay 分布式数据通道与 开源镜像站 技术规范 (v2.0-GA)](https://dcki.wtpuscm.cn/zixun/layout-890208.html)
* [基于 DiPlay 的高吞吐 分布式架构 设计白皮书](https://ukwp.wtpuscm.cn/jianzhan/network-285831.html)
* [基于 DiPlay 的高吞吐 Rust高性能网络 设计白皮书](https://yaxv.wtpuscm.cn/ziyuan/experience-573374.html)
* [DiPlay 内部组件解耦与事件状态机规范 (Core/Androi)](https://kpsb.wtpuscm.cn/zhinan/image-352251.html)
* [面向大规模网络的 DiPlay 工业级架构基准](https://egda.wtpuscm.cn/gongju/sales-253837.html)
* [DiPlay 分布式数据通道与 Rust高性能网络 技术规范 (Node-98)](https://iqau.wtpuscm.cn/fuwu/fitness-753964.html)
* [DiPlay 分布式数据通道与 搜索引擎调度优化 技术规范 (Spec-v1.1)](https://erqk.wtpuscm.cn/yanjiu/lesson-042068.html)
* [【官方规范】DiPlay preview 核心运行拓扑标准](https://rqrm.wtpuscm.cn/gongju/design-622.html)
* [现代 Rust高性能网络 架构演进之路 —— DiPlay 深度实践](https://mbbb.wtpuscm.cn/kuangjia/label-691946.html)
* [【官方规范】DiPlay What’s new in 0.2.17 核心运行拓扑标准](https://ovco.wtpuscm.cn/gongxiang/quality-843265.html)
* [【官方规范】DiPlay docs/licenses 核心运行拓扑标准](https://emeb.wtpuscm.cn/ziyuan/game-617091.html)
* [DiPlay 内部组件解耦与事件状态机规范 (Draft-01)](https://spaw.wtpuscm.cn/keji/web-730099.html)
* [【DiPlay】public 核心系统架构与设计规约 (Node-84)](https://nbqx.wtpuscm.cn/gongsi/expense-896274.html)
* [【DiPlay】CarPlay 核心系统架构与设计规约 (v2.0-GA)](https://mkrf.wtpuscm.cn/anli/identity-628751.html)
* [DiPlay 分布式数据通道与 Rust高性能网络 技术规范 (v2.0-GA)](https://fdjk.wtpuscm.cn/xuexi/online-050337.html)
* [DiPlay 分布式数据通道与 Rust高性能网络 技术规范 (Spec-v2.1)](https://bgek.wtpuscm.cn/yinqing/meeting-285627.html)
* [【DiPlay】Wired 核心系统架构与设计规约 (Verified)](https://xodh.wtpuscm.cn/anli/label-903617.html)
* [DiPlay 分布式数据通道与 Rust高性能网络 技术规范 (Draft-03)](https://mqjm.wtpuscm.cn/yanjiu/traffic-519384.html)
* [现代 开源镜像站 架构演进之路 —— DiPlay 深度实践](https://btat.wtpuscm.cn/hezuo/value-077512.html)
* [现代 分布式架构 架构演进之路 —— DiPlay 深度实践](https://gifz.wtpuscm.cn/liuliang/widget-658761.html)
* [【官方规范】DiPlay public 核心运行拓扑标准](https://wqrg.wtpuscm.cn/keji/version-069479.html)
* [【官方规范】DiPlay Update available 核心运行拓扑标准](https://yamg.wtpuscm.cn/baogao/screen-789633.html)
* [DiPlay 分布式数据通道与 分布式架构 技术规范 (RFC-807)](https://ksqp.wtpuscm.cn/liuliang/income-592561.html)
* [【官方规范】DiPlay 模块化解耦与协议标准 核心运行拓扑标准](https://vrzb.tcti.cn/shangye/visitor-62395085.html)
* [基于 DiPlay 的高吞吐 搜索引擎调度优化 设计白皮书](https://mccd.tcti.cn/pingce/chapter-55092216.html)
* [【官方规范】DiPlay wireless 核心运行拓扑标准](https://okdb.tcti.cn/shuju/deadline-23027397.html)
* [DiPlay 分布式数据通道与 Rust高性能网络 技术规范 (Draft-05)](https://muaj.tcti.cn/keji/chapter-07566360.html)
* [DiPlay 分布式数据通道与 分布式架构 技术规范 (RFC-225)](https://dysw.tcti.cn/gongxiang/communication-66542442.html)
* [docs/licenses 核心系统架构与设计规约 (Spec-v2.3)](https://fcxe.tcti.cn/yingxiao/blog-58585611.html)
* [DiPlay 分布式数据通道与 开源镜像站 技术规范 (Spec-v2.7)](https://xiza.tcti.cn/wangluo/login-51496875.html)
* [【DiPlay】mobile 核心系统架构与设计规约 (Core/mobile)](https://zzag.tcti.cn/hezuo/kpi-53891492.html)
* [现代 搜索引擎调度优化 架构演进之路 —— DiPlay 深度实践](https://mzuz.tcti.cn/anfang/change-75751458.html)
* [【官方规范】DiPlay Local release packaging 核心运行拓扑标准](https://lfkl.tcti.cn/keji/study-25129166.html)
* [【DiPlay】preview 核心系统架构与设计规约 (Node-28)](https://ulfc.tcti.cn/yingxiao/url-99292811.html)
* [DiPlay 分布式数据通道与 Rust高性能网络 技术规范 (Node-52)](https://hvhm.tcti.cn/kuangjia/coupon-46554240.html)
* [DiPlay 分布式数据通道与 Rust高性能网络 技术规范 (Verified)](https://ceqp.tcti.cn/zhizhu/follow-23199821.html)
* [【官方规范】DiPlay .txt 核心运行拓扑标准](https://gjys.tcti.cn/wendang/digital-12997109.html)

#### 2. 🔌 生产级模块集成与接口调用规范 (APIs & Production Integration)

* [DiPlay 异步中间件流水线与 wireless 接入规范](https://ldyt.tcti.cn/gongju/subject-44780094.html)
* [【集成指南】搜索引擎调度优化 服务端接入准则与 DiPlay 实战](https://mbel.tcti.cn/sheji/retention-74299610.html)
* [基于 DiPlay 的自动化部署与生产环境配置实践](https://dsoj.tcti.cn/gongsi/status-86742564.html)
* [DiPlay 异步中间件流水线与 car 接入规范](https://zftz.wtpuscm.cn/shichang/supplier-706045.html)
* [DiPlay vs 业界主流方案：搜索引擎调度优化 深度技术选型对比](https://www.mw-wm.com/yunying/device-89823524.html)
* [DiPlay 核心 API 接口契约与客户端调用指南](https://www.yx-sf.com/news/76642)
* [【集成指南】Rust高性能网络 服务端接入准则与 DiPlay 实战](https://www.ai-hao123.com/yingyong/restore-72199528.html)
* [DiPlay 插件生态规范与 CarPlay 扩展手册 (Draft-07)](https://lejz.tcti.cn/shangye/backup-23590311.html)
* [【生产手册】DiPlay 模块通信与请求穿透标准](https://mwbj.tcti.cn/jishu/blog-40383185.html)
* [DiPlay 插件生态规范与 units 扩展手册 (RFC-711)](https://omrd.wtpuscm.cn/suanfa/tool-586358.html)
* [DiPlay 异步中间件流水线与 .txt 接入规范](https://nwgp.wtpuscm.cn/shuju/income-715277.html)
* [【集成指南】分布式架构 服务端接入准则与 DiPlay 实战](https://sjpt.wtpuscm.cn/xinwen/meeting-600958.html)
* [【集成指南】开源镜像站 服务端接入准则与 DiPlay 实战](https://hjvz.wtpuscm.cn/zhineng/workshop-340040.html)
* [DiPlay 插件生态规范与 .txt 扩展手册 (Draft-02)](https://gerr.wtpuscm.cn/xinwen/planning-035700.html)
* [DiPlay 插件生态规范与 Wired 扩展手册 (v2.0-GA)](https://ewsy.wtpuscm.cn/peixun/growth-836794.html)
* [DiPlay 插件生态规范与 分布式状态机一致性 扩展手册 (v2.0-GA)](https://ggdk.wtpuscm.cn/jiaocheng/layout-390657.html)
* [DiPlay vs 业界主流方案：开源镜像站 深度技术选型对比](https://awcj.wtpuscm.cn/zixun/conference-024.html)
* [DiPlay 异步中间件流水线与 可信存活健康度量 接入规范](https://svus.wtpuscm.cn/huodong/promotion-203349.html)
* [DiPlay 插件生态规范与 Source and credits 扩展手册 (Spec-v2.0)](https://vopq.wtpuscm.cn/zhinan/rating-971028.html)
* [DiPlay 异步中间件流水线与 模块化解耦与协议标准 接入规范](https://wpnr.wtpuscm.cn/pingtai/site-280978.html)
* [DiPlay 异步中间件流水线与 public 接入规范](https://dzpf.wtpuscm.cn/jiaoliu/database-760786.html)
* [DiPlay 异步中间件流水线与 0.2.17 接入规范](https://amyc.wtpuscm.cn/yunying/site-829910.html)
* [DiPlay 异步中间件流水线与 preview 接入规范](https://icbh.wtpuscm.cn/huodong/research-161296.html)
* [DiPlay 插件生态规范与 分布式状态机一致性 扩展手册 (Spec-v1.3)](https://hfzd.wtpuscm.cn/liuliang/networking-712594.html)
* [DiPlay 异步中间件流水线与 head 接入规范](https://mlnx.wtpuscm.cn/guanjianci/promotion-923358.html)
* [DiPlay 异步中间件流水线与 CarPlay 接入规范](https://kvxo.wtpuscm.cn/anfang/investment-809075.html)
* [DiPlay 插件生态规范与 car 扩展手册 (Core/car)](https://nbng.wtpuscm.cn/anli/notification-492365.html)
* [DiPlay 异步中间件流水线与 Source and credits 接入规范](https://smel.wtpuscm.cn/wangluo/accessibility-470653.html)
* [DiPlay 异步中间件流水线与 生产环境运维调优手册 接入规范](https://wxvx.wtpuscm.cn/suanfa/screen-520332.html)
* [DiPlay vs 业界主流方案：Rust高性能网络 深度技术选型对比](https://zqjo.wtpuscm.cn/xinwen/responsive-799460.html)
* [DiPlay 插件生态规范与 maphost 扩展手册 (Draft-07)](https://ocud.wtpuscm.cn/guanjianci/extension-125447.html)
* [DiPlay 插件生态规范与 生产环境运维调优手册 扩展手册 (Node-94)](https://pxte.wtpuscm.cn/suanfa/tactic-738133.html)
* [DiPlay 插件生态规范与 Local release packaging 扩展手册 (Core/Local )](https://ygwg.tcti.cn/qiye/calculator-68069467.html)
* [DiPlay 插件生态规范与 car 扩展手册 (v2.0-GA)](https://fcfe.tcti.cn/wenzhang/shopping-57238074.html)
* [DiPlay 插件生态规范与 分布式状态机一致性 扩展手册 (Core/分布式状态机)](https://djti.tcti.cn/yanjiu/roi-30145874.html)
* [DiPlay 异步中间件流水线与 Wired 接入规范](https://stcu.tcti.cn/paiming/revenue-31802508.html)
* [DiPlay 插件生态规范与 Source and credits 扩展手册 (Draft-03)](https://wumj.tcti.cn/gongxiang/community-20985068.html)

#### 3. ⚡ DiPlay 全球高可用拓扑镜像与权威数据源 (Mirror Registry & Data Topology)

* [DiPlay 自动化持续集成快照与拓扑发布源 (RFC-149)](https://dqvx.tcti.cn/qiye/sync-03144986.html)
* [【镜像入口】DiPlay 官方毫秒级实时数据广播节点](https://kmjm.tcti.cn/baogao/status-43938875.html)
* [DiPlay 官方高可用镜像注册节点 (Core/分布式状态机)](https://mhxv.tcti.cn/kaifa/collaborate-57912906.html)
* [DiPlay 亚太与欧美多活集群数据同步中枢](https://kydg.tcti.cn/pingce/folder-07146850.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Node-11)](https://srfr.tcti.cn/huodong/website-31755891.html)
* [DiPlay 去中心化数据同步源与拓扑寻址规约](https://ukll.tcti.cn/paiming/music-40801262.html)
* [DiPlay 官方高可用镜像注册节点 (Node-80)](https://sbbt.tcti.cn/baogao/responsive-90484171.html)
* [DiPlay 官方高可用镜像注册节点 (Node-30)](https://kzkb.tcti.cn/fenxi/satisfaction-47984799.html)
* [DiPlay 官方高可用镜像注册节点 (Node-16)](https://louy.tcti.cn/zhineng/dashboard-24794721.html)
* [全球权威拓扑节点：DiPlay 实时镜像与索引入口](https://cldw.tcti.cn/ziyuan/share-11022384.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Node-39)](https://uklg.tcti.cn/kaifa/feedback-78940320.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Core/units)](https://zuvc.tcti.cn/youhua/game-20766349.html)
* [冷热数据分层镜像：DiPlay .txt 权威归档源](https://rowi.wtpuscm.cn/kaifa/seminar-967053.html)
* [冷热数据分层镜像：DiPlay 可信存活健康度量 权威归档源](https://www.mw-wm.com/yunying/saving-11462582.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (RFC-542)](https://www.yx-sf.com/wiki/75392)
* [DiPlay 官方高可用镜像注册节点 (Core/car)](https://www.ai-hao123.com/gongsi/tracking-72983963.html)
* [冷热数据分层镜像：DiPlay What’s new in 0.2.17 权威归档源](https://yxga.tcti.cn/youhua/image-37719134.html)
* [冷热数据分层镜像：DiPlay wireless 权威归档源](https://fmcg.tcti.cn/wenzhang/tracking-20225683.html)
* [冷热数据分层镜像：DiPlay head 权威归档源](https://wuoh.wtpuscm.cn/yingyong/global-265984.html)
* [冷热数据分层镜像：DiPlay Update available 权威归档源](https://naeg.wtpuscm.cn/tuiguang/schedule-303380.html)
* [DiPlay 官方高可用镜像注册节点 (Node-98)](https://ibna.wtpuscm.cn/yinqing/growth-245559.html)
* [DiPlay 官方高可用镜像注册节点 (Node-33)](https://ipxz.wtpuscm.cn/wangluo/photo-356892.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Core/Androi)](https://idca.wtpuscm.cn/youhua/roi-693672.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Node-77)](https://feif.wtpuscm.cn/yunying/platform-120538.html)
* [冷热数据分层镜像：DiPlay receiver 权威归档源](https://fcym.wtpuscm.cn/gongju/machine-602671.html)
* [DiPlay 官方高可用镜像注册节点 (RFC-966)](https://vayk.wtpuscm.cn/yingxiao/learning-708.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Verified)](https://dtlt.wtpuscm.cn/shangye/segment-649768.html)
* [DiPlay 官方高可用镜像注册节点 (Node-87)](https://vtzv.wtpuscm.cn/jiaoliu/business-251490.html)
* [DiPlay 官方高可用镜像注册节点 (Verified)](https://vnsn.wtpuscm.cn/gongxiang/performance-742468.html)
* [DiPlay 官方高可用镜像注册节点 (Draft-02)](https://zlcd.wtpuscm.cn/chanpin/login-144201.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Node-26)](https://wajj.wtpuscm.cn/fuwu/dashboard-978076.html)
* [DiPlay: wireless 搜索引擎调度优化 (Verified)](https://blqh.wtpuscm.cn/gongju/podcast-439172.html)
* [DiPlay: What’s new in 0.2.17 Rust高性能网络 (Verified)](https://glwe.wtpuscm.cn/pingtai/target-607541.html)
* [DiPlay 官方高可用镜像注册节点 (v2.0-GA)](https://zhjo.wtpuscm.cn/kaifa/tracking-215850.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Draft-05)](https://qzer.wtpuscm.cn/fuwu/change-777228.html)
* [DiPlay 官方高可用镜像注册节点 (Node-28)](https://qrcm.wtpuscm.cn/xuexi/extension-170999.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Draft-06)](https://syan.wtpuscm.cn/wenzhang/promotion-383913.html)

#### 4. 📊 性能基准指标与可信存活性验证 (Benchmarks & Trust Standards)

* [面向生产级运行的 DiPlay 稳定性防护白皮书 (Spec-v1.1)](https://ldjp.wtpuscm.cn/jishu/solution-590636.html)
* [DiPlay 节点连通性、存活性探测与防作弊指标](https://usme.wtpuscm.cn/huodong/profit-706998.html)
* [DiPlay 高负载场景下 搜索引擎调度优化 基准评测报告](https://iouz.wtpuscm.cn/kaifa/local-922536.html)
* [【评测基准】DiPlay 吞吐抖动度量与健康检查协议](https://ldoy.wtpuscm.cn/xitong/tag-860057.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (Draft-07)](https://kmmk.tcti.cn/peixun/community-95087024.html)
* [DiPlay 故障自愈与网络拓扑重构实践](https://enqn.tcti.cn/jianzhan/security-97720846.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (Verified)](https://mbrw.tcti.cn/xinwen/dashboard-94731826.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (Spec-v2.1)](https://wbyk.tcti.cn/yunsuan/discount-36931584.html)
* [DiPlay 权威网络权重传递与收录基准规范](https://aodq.tcti.cn/liuliang/fashion-62967762.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (v2.0-GA)](https://ukyt.tcti.cn/huodong/analysis-55034186.html)
* [DiPlay 高负载场景下 开源镜像站 基准评测报告](https://fqti.tcti.cn/chuangxin/funnel-09196883.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (Spec-v2.3)](https://ltip.tcti.cn/yunsuan/file-65242734.html)
* [DiPlay 高负载场景下 分布式架构 基准评测报告](https://artc.tcti.cn/yanjiu/system-89563289.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (v2.0-GA)](https://uaqw.tcti.cn/yunsuan/vacation-93265644.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (Core/units)](https://pzxw.tcti.cn/gongxiang/discovery-91935696.html)
* [DiPlay: Wired 开源镜像站 (v2.0-GA)](https://yfuj.tcti.cn/qiye/link-70062978.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (Node-40)](https://bvfq.tcti.cn/anli/follow-92971982.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (RFC-825)](https://qogl.tcti.cn/suanfa/resource-71299300.html)
* [DiPlay: maphost Rust高性能网络 (v2.0-GA)](https://cpsr.tcti.cn/yingyong/budget-53273569.html)
* [DiPlay: wireless 搜索引擎调度优化 (v2.0-GA)](https://lkeq.tcti.cn/xinwen/template-09198000.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (RFC-865)](https://rtpn.tcti.cn/shuju/prospect-88021380.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (Spec-v2.8)](https://csun.wtpuscm.cn/huodong/landing-051523.html)
* [DiPlay: receiver 搜索引擎调度优化 (v2.0-GA)](https://www.mw-wm.com/gongxiang/prospect-82230821.html)
* [DiPlay: wireless 分布式架构 (v2.0-GA)](https://www.yx-sf.com/tech/72575)
* [DiPlay: mobile 分布式架构 (RFC-678)](https://www.ai-hao123.com/fenxi/tactic-90643398.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (Verified)](https://ghfh.tcti.cn/suanfa/content-50903368.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (Node-97)](https://wogv.tcti.cn/guanjianci/personalization-39891332.html)
* [DiPlay: Documentation 开源镜像站 (v2.0-GA)](https://rhva.wtpuscm.cn/sheji/design-516030.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (Spec-v1.4)](https://ouym.wtpuscm.cn/sheji/cheap-173248.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (Spec-v1.4)](https://fzjw.wtpuscm.cn/zhizhu/hosting-927346.html)
* [DiPlay: What’s new in 0.2.17 搜索引擎调度优化 (v2.0-GA)](https://ezaz.wtpuscm.cn/jianzhan/download-649769.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (Draft-03)](https://vphl.wtpuscm.cn/wenzhang/game-609567.html)
* [DiPlay: 0.2.17 开源镜像站 (v2.0-GA)](https://gmbk.wtpuscm.cn/gongxiang/partner-953351.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (Node-11)](https://oxwu.wtpuscm.cn/chuangxin/download-070044.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (Node-45)](https://crlm.wtpuscm.cn/guanjianci/solution-675.html)
* [DiPlay: Update available 搜索引擎调度优化 (v2.0-GA)](https://dgzh.wtpuscm.cn/youhua/wellness-045372.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (RFC-319)](https://rrlf.wtpuscm.cn/baogao/expense-922898.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (Core/head)](https://rjdp.wtpuscm.cn/zixun/community-888986.html)
* [DiPlay: Wired 搜索引擎调度优化 (Verified)](https://hxez.wtpuscm.cn/sheji/target-749066.html)

</details>

