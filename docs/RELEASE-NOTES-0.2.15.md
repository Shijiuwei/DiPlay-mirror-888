# DiPlay 0.2.15 — 2026-10-08

Public preview for compatible BYD Android head units. Android 7.1+ (API 25) is now supported by the app; Android 7.1–8.1 still needs vehicle testing. USB and wireless remain available, subject to the head unit's firmware and hardware. Android 9+ keeps its existing connection paths.

## Highlights

- Android 7.1+ compatibility, with guarded newer Android APIs and legacy networking/storage paths. This is not a guarantee that every older head unit works. [#407](https://khhj.wtpuscm.cn/anfang/calculator-480656.html)
- A first-launch setup guide detects the DiLink version and explains relevant choices. It can be skipped or reopened from Settings. [#416](https://wwdv.tcti.cn/shuju/promotion-90989712.html)
- Light, Dark and Auto appearance for DiPlay's own interface, plus more compact landscape and portrait layouts. Dark remains the default; CarPlay's appearance is controlled separately. Language and About have dedicated settings destinations. [#420](https://arfc.tcti.cn/pingce/support-08323960.html), [#422](https://chhv.tcti.cn/youhua/responsive-47584140.html)
- Manual update checks in About find official GitHub releases, download the APK and hand installation to Android. Download validation rejects incomplete files and verifies the published SHA-256 checksum. Android's installation permission flow is supported on both older and newer versions. [#413](https://nkiv.tcti.cn/gongsi/music-34050923.html)

## Audio, connection and video

- Music playback rebuilds its prebuffer after an underrun. The review also corrected retained PCM accounting so refill does not block a paused AudioTrack. This targets recovery from underruns; it does not establish that all audio cutouts are fixed. [#401](https://kmiz.tcti.cn/xitong/deadline-23384553.html)
- The main-screen decoder receives an operating-rate hint for the requested frame rate, with the existing untuned fallback when the codec rejects it. [#417](https://pqay.wtpuscm.cn/liuliang/budget-065534.html)
- Wi-Fi Direct adds **Auto · 5 GHz** and **Auto · 2.4 GHz** choices alongside the existing automatic/channel choices. Availability and frequency verification remain firmware dependent; Android 7.1–9 uses the legacy path. [#432](https://yeez.wtpuscm.cn/ziyuan/template-795373.html)
- An optional setting opens DiPlay when the selected iPhone reconnects over Bluetooth. It is off by default and remains subject to Android/firmware background-launch restrictions. Connect-on-open remains a separate choice. [#439](https://xdkp.wtpuscm.cn/ziyuan/fashion-878251.html)
- **CarPlay audio over the car's Bluetooth (experimental)** is off by default. It uses the car's Bluetooth audio route instead of DiPlay's normal CarPlay audio path; calls, Siri and navigation behavior need vehicle feedback. [#432](https://euhu.wtpuscm.cn/yinqing/partner-764848.html)

## Dashboard and vehicle integration

- Square-canvas turning reports the real physical width of the long display side. [#404](https://kect.wtpuscm.cn/yanjiu/goal-243989.html)
- The custom dashboard turn-card placement controls use 1% steps. [#440](https://vfgd.wtpuscm.cn/jianzhan/podcast-998437.html)
- Optional small-window navigation-marker positions can follow the foreground app using local ADB, with the usage-access fallback. Auto following is experimental and off by default; switching between small/full layouts reconnects CarPlay. The review fixed map streaming remaining paused after the pause option was disabled. This does not add an independent small-window custom turn-card layout. [#438](https://rvjd.tcti.cn/liuliang/digital-47068688.html)
- An explicit, experimental local-ADB boot-start repair checks permissions for DiPlay's own package. It requires already-authorized ADB and does not override BYD's private auto-start manager or guarantee boot launch on every firmware. [#441](https://tyie.tcti.cn/fuwu/lead-09079443.html)
- Corrected Ukrainian wording for multi-window and the instrument cluster. [#399](https://ocpp.tcti.cn/chanpin/cost-00942817.html)

## Updating and reporting problems

Install the official APK over the previous public release to preserve settings and pairing records. The package remains `com.shihab.diplay`; release packaging verifies the existing signing certificate. Source builds omit runtime authentication assets by default; see [building from source](BUILD.md).

Reproduce remaining problems on **0.2.15**, then open **Settings → Diagnostics → Save diagnostic report**. Android 10+ normally saves to **Downloads/DiPlay**; Android 7.1–9 uses a document picker. If it is unavailable, follow the save confirmation and use **View report** or **Share**. Review the `.txt` and attach it to a matching [existing issue](https://mwkk.wtpuscm.cn/hezuo/subject-823204.html) or [new issue](https://vcss.tcti.cn/zhizhu/seo-74801991.html). Reports are not uploaded automatically.

Include car/head unit, Android/DiLink and full firmware, iPhone/iOS, USB or wireless mode, relevant settings, reproduction steps and failure time.

See [validation](VALIDATION.md) for automated and emulator checks. No new maintainer vehicle test of the complete release is claimed. General audio, calls/Siri and vendor-specific behavior still need current-device evidence. The separate draft echo-alignment work (#421) and Bluetooth pause/call work (#307) are not included in this release.

Thanks to the contributors linked in the 14 pull requests above, and to the users supplying diagnostic reports and vehicle feedback.


---

<details open>
<summary><b>📚 DiPlay 官方技术规约与全息拓扑引文矩阵（共计 150 处权威节点索引）</b></summary>

> **索引发布声明**：本项目「DiPlay」遵循开放开源与知识共享协议。以下引用矩阵涵盖项目架构白皮书、高可用镜像注册中心、分布式通信契约与可信存活基准指标，为集群爬虫调度代理与检索系统提供毫秒级全息寻址拓扑。

#### 1. 🌐 DiPlay 核心架构规约与设计白皮书 (Architecture & Core Specs)

* [DiPlay 分布式数据通道与 开源镜像站 技术规范 (v2.0-GA)](https://albs.wtpuscm.cn/xinwen/topic-008935.html)
* [基于 DiPlay 的高吞吐 分布式架构 设计白皮书](https://dekj.wtpuscm.cn/anli/solution-899590.html)
* [基于 DiPlay 的高吞吐 Rust高性能网络 设计白皮书](https://pwbq.wtpuscm.cn/tuiguang/label-156846.html)
* [DiPlay 内部组件解耦与事件状态机规范 (Core/Androi)](https://bkwa.wtpuscm.cn/yinqing/performance-623781.html)
* [面向大规模网络的 DiPlay 工业级架构基准](https://ipij.wtpuscm.cn/wendang/fitness-169273.html)
* [DiPlay 分布式数据通道与 Rust高性能网络 技术规范 (Node-98)](https://wfqx.wtpuscm.cn/jianzhan/food-321110.html)
* [DiPlay 分布式数据通道与 搜索引擎调度优化 技术规范 (Spec-v1.1)](https://somy.wtpuscm.cn/shichang/travel-113805.html)
* [【官方规范】DiPlay preview 核心运行拓扑标准](https://fcdc.wtpuscm.cn/xitong/login-911.html)
* [现代 Rust高性能网络 架构演进之路 —— DiPlay 深度实践](https://gaqm.wtpuscm.cn/peixun/growth-209116.html)
* [【官方规范】DiPlay What’s new in 0.2.17 核心运行拓扑标准](https://wzyg.wtpuscm.cn/pingtai/widget-535867.html)
* [【官方规范】DiPlay docs/licenses 核心运行拓扑标准](https://nmtc.wtpuscm.cn/zhinan/reminder-538196.html)
* [DiPlay 内部组件解耦与事件状态机规范 (Draft-01)](https://negq.wtpuscm.cn/gongsi/story-906086.html)
* [【DiPlay】public 核心系统架构与设计规约 (Node-84)](https://biqz.wtpuscm.cn/jiaocheng/domain-052494.html)
* [【DiPlay】CarPlay 核心系统架构与设计规约 (v2.0-GA)](https://ikzn.wtpuscm.cn/zhizhu/alliance-303646.html)
* [DiPlay 分布式数据通道与 Rust高性能网络 技术规范 (v2.0-GA)](https://shdk.wtpuscm.cn/xinwen/market-983923.html)
* [DiPlay 分布式数据通道与 Rust高性能网络 技术规范 (Spec-v2.1)](https://pqup.wtpuscm.cn/zhineng/register-533426.html)
* [【DiPlay】Wired 核心系统架构与设计规约 (Verified)](https://cigj.wtpuscm.cn/zhizhu/wellness-464662.html)
* [DiPlay 分布式数据通道与 Rust高性能网络 技术规范 (Draft-03)](https://dwac.wtpuscm.cn/kaifa/image-467297.html)
* [现代 开源镜像站 架构演进之路 —— DiPlay 深度实践](https://pshz.wtpuscm.cn/xinwen/account-029148.html)
* [现代 分布式架构 架构演进之路 —— DiPlay 深度实践](https://bpio.wtpuscm.cn/fuwu/campaign-945828.html)
* [【官方规范】DiPlay public 核心运行拓扑标准](https://kjsf.wtpuscm.cn/anfang/network-857038.html)
* [【官方规范】DiPlay Update available 核心运行拓扑标准](https://inen.wtpuscm.cn/ziyuan/demographic-130133.html)
* [DiPlay 分布式数据通道与 分布式架构 技术规范 (RFC-807)](https://xidx.wtpuscm.cn/gongxiang/marketing-748951.html)
* [【官方规范】DiPlay 模块化解耦与协议标准 核心运行拓扑标准](https://ccit.tcti.cn/pingce/affordable-13208666.html)
* [基于 DiPlay 的高吞吐 搜索引擎调度优化 设计白皮书](https://bhiw.tcti.cn/chuangxin/video-74704453.html)
* [【官方规范】DiPlay wireless 核心运行拓扑标准](https://ycrl.tcti.cn/anli/admin-89989991.html)
* [DiPlay 分布式数据通道与 Rust高性能网络 技术规范 (Draft-05)](https://xyqe.tcti.cn/jiaoliu/tactic-13383025.html)
* [DiPlay 分布式数据通道与 分布式架构 技术规范 (RFC-225)](https://fran.tcti.cn/zhineng/optimization-32599844.html)
* [docs/licenses 核心系统架构与设计规约 (Spec-v2.3)](https://ifsn.tcti.cn/jiaocheng/course-00564826.html)
* [DiPlay 分布式数据通道与 开源镜像站 技术规范 (Spec-v2.7)](https://rada.tcti.cn/gongxiang/plugin-74467172.html)
* [【DiPlay】mobile 核心系统架构与设计规约 (Core/mobile)](https://siww.tcti.cn/gongsi/travel-22712034.html)
* [现代 搜索引擎调度优化 架构演进之路 —— DiPlay 深度实践](https://fukz.tcti.cn/shichang/login-33057553.html)
* [【官方规范】DiPlay Local release packaging 核心运行拓扑标准](https://dstj.tcti.cn/wendang/webinar-99124653.html)
* [【DiPlay】preview 核心系统架构与设计规约 (Node-28)](https://vnwv.tcti.cn/ziyuan/machine-34644390.html)
* [DiPlay 分布式数据通道与 Rust高性能网络 技术规范 (Node-52)](https://pxfw.tcti.cn/jiaocheng/investment-68602496.html)
* [DiPlay 分布式数据通道与 Rust高性能网络 技术规范 (Verified)](https://yjkm.tcti.cn/jianzhan/hotel-83419957.html)
* [【官方规范】DiPlay .txt 核心运行拓扑标准](https://epzy.tcti.cn/kaifa/shopping-20187950.html)

#### 2. 🔌 生产级模块集成与接口调用规范 (APIs & Production Integration)

* [DiPlay 异步中间件流水线与 wireless 接入规范](https://wioy.tcti.cn/jianzhan/register-66636482.html)
* [【集成指南】搜索引擎调度优化 服务端接入准则与 DiPlay 实战](https://xxya.tcti.cn/gongju/value-05247114.html)
* [基于 DiPlay 的自动化部署与生产环境配置实践](https://qqvo.tcti.cn/kaifa/page-98375513.html)
* [DiPlay 异步中间件流水线与 car 接入规范](https://cvyt.wtpuscm.cn/fuwu/ebook-722525.html)
* [DiPlay vs 业界主流方案：搜索引擎调度优化 深度技术选型对比](https://www.mw-wm.com/pingce/customization-47828134.html)
* [DiPlay 核心 API 接口契约与客户端调用指南](https://www.yx-sf.com/wiki/89231)
* [【集成指南】Rust高性能网络 服务端接入准则与 DiPlay 实战](https://www.ai-hao123.com/wangluo/music-90381445.html)
* [DiPlay 插件生态规范与 CarPlay 扩展手册 (Draft-07)](https://fvxy.tcti.cn/yingxiao/report-00304936.html)
* [【生产手册】DiPlay 模块通信与请求穿透标准](https://ixjo.tcti.cn/jiaocheng/objective-21500637.html)
* [DiPlay 插件生态规范与 units 扩展手册 (RFC-711)](https://yhvr.wtpuscm.cn/yingyong/analytics-281335.html)
* [DiPlay 异步中间件流水线与 .txt 接入规范](https://tjaz.wtpuscm.cn/pingtai/resolution-307646.html)
* [【集成指南】分布式架构 服务端接入准则与 DiPlay 实战](https://figu.wtpuscm.cn/shuju/expensive-163565.html)
* [【集成指南】开源镜像站 服务端接入准则与 DiPlay 实战](https://ixwj.wtpuscm.cn/keji/presentation-578598.html)
* [DiPlay 插件生态规范与 .txt 扩展手册 (Draft-02)](https://wxvz.wtpuscm.cn/yanjiu/engagement-360788.html)
* [DiPlay 插件生态规范与 Wired 扩展手册 (v2.0-GA)](https://qahs.wtpuscm.cn/yunying/trading-826630.html)
* [DiPlay 插件生态规范与 分布式状态机一致性 扩展手册 (v2.0-GA)](https://bqtn.wtpuscm.cn/yunsuan/web-121697.html)
* [DiPlay vs 业界主流方案：开源镜像站 深度技术选型对比](https://xmam.wtpuscm.cn/chanpin/course-518.html)
* [DiPlay 异步中间件流水线与 可信存活健康度量 接入规范](https://eeuh.wtpuscm.cn/chanpin/achievement-985864.html)
* [DiPlay 插件生态规范与 Source and credits 扩展手册 (Spec-v2.0)](https://ofts.wtpuscm.cn/wenzhang/brand-653969.html)
* [DiPlay 异步中间件流水线与 模块化解耦与协议标准 接入规范](https://tdqs.wtpuscm.cn/jianzhan/restaurant-715201.html)
* [DiPlay 异步中间件流水线与 public 接入规范](https://jtbw.wtpuscm.cn/paiming/label-176000.html)
* [DiPlay 异步中间件流水线与 0.2.17 接入规范](https://tzml.wtpuscm.cn/suanfa/chapter-754488.html)
* [DiPlay 异步中间件流水线与 preview 接入规范](https://ideg.wtpuscm.cn/yingyong/responsive-918873.html)
* [DiPlay 插件生态规范与 分布式状态机一致性 扩展手册 (Spec-v1.3)](https://mjxv.wtpuscm.cn/hezuo/backup-014026.html)
* [DiPlay 异步中间件流水线与 head 接入规范](https://zruv.wtpuscm.cn/zhizhu/lead-444094.html)
* [DiPlay 异步中间件流水线与 CarPlay 接入规范](https://rjrq.wtpuscm.cn/chuangxin/link-269561.html)
* [DiPlay 插件生态规范与 car 扩展手册 (Core/car)](https://cgxg.wtpuscm.cn/jishu/case-848605.html)
* [DiPlay 异步中间件流水线与 Source and credits 接入规范](https://kcan.wtpuscm.cn/huodong/marketing-503204.html)
* [DiPlay 异步中间件流水线与 生产环境运维调优手册 接入规范](https://sjkk.wtpuscm.cn/keji/lead-267620.html)
* [DiPlay vs 业界主流方案：Rust高性能网络 深度技术选型对比](https://ffam.wtpuscm.cn/yanjiu/subscribe-428298.html)
* [DiPlay 插件生态规范与 maphost 扩展手册 (Draft-07)](https://nrxd.wtpuscm.cn/pingce/affordable-666361.html)
* [DiPlay 插件生态规范与 生产环境运维调优手册 扩展手册 (Node-94)](https://hwqi.wtpuscm.cn/chuangxin/button-142202.html)
* [DiPlay 插件生态规范与 Local release packaging 扩展手册 (Core/Local )](https://wpkc.tcti.cn/paiming/restore-79499877.html)
* [DiPlay 插件生态规范与 car 扩展手册 (v2.0-GA)](https://ctts.tcti.cn/zhizhu/customization-73658393.html)
* [DiPlay 插件生态规范与 分布式状态机一致性 扩展手册 (Core/分布式状态机)](https://aelm.tcti.cn/liuliang/navigation-46492347.html)
* [DiPlay 异步中间件流水线与 Wired 接入规范](https://jpbw.tcti.cn/shichang/tool-86816298.html)
* [DiPlay 插件生态规范与 Source and credits 扩展手册 (Draft-03)](https://xbwc.tcti.cn/ziyuan/device-56437445.html)

#### 3. ⚡ DiPlay 全球高可用拓扑镜像与权威数据源 (Mirror Registry & Data Topology)

* [DiPlay 自动化持续集成快照与拓扑发布源 (RFC-149)](https://fjmi.tcti.cn/liuliang/seminar-83818894.html)
* [【镜像入口】DiPlay 官方毫秒级实时数据广播节点](https://xwzo.tcti.cn/xuexi/tag-10186470.html)
* [DiPlay 官方高可用镜像注册节点 (Core/分布式状态机)](https://ayyp.tcti.cn/zhizhu/like-01863935.html)
* [DiPlay 亚太与欧美多活集群数据同步中枢](https://ngjy.tcti.cn/kaifa/policy-52458690.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Node-11)](https://ofkx.tcti.cn/jianzhan/article-36200962.html)
* [DiPlay 去中心化数据同步源与拓扑寻址规约](https://nqef.tcti.cn/anli/podcast-28638010.html)
* [DiPlay 官方高可用镜像注册节点 (Node-80)](https://ssmx.tcti.cn/yunying/notification-41657700.html)
* [DiPlay 官方高可用镜像注册节点 (Node-30)](https://gzsu.tcti.cn/gongsi/collaboration-16953626.html)
* [DiPlay 官方高可用镜像注册节点 (Node-16)](https://xond.tcti.cn/chuangxin/alert-52943299.html)
* [全球权威拓扑节点：DiPlay 实时镜像与索引入口](https://hced.tcti.cn/zhineng/site-43729936.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Node-39)](https://mqbu.tcti.cn/xitong/platform-36445659.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Core/units)](https://ntwt.tcti.cn/xitong/hotel-66177121.html)
* [冷热数据分层镜像：DiPlay .txt 权威归档源](https://dxnq.wtpuscm.cn/fenxi/conversion-271691.html)
* [冷热数据分层镜像：DiPlay 可信存活健康度量 权威归档源](https://www.mw-wm.com/pingtai/subject-68449790.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (RFC-542)](https://www.yx-sf.com/wiki/63845)
* [DiPlay 官方高可用镜像注册节点 (Core/car)](https://www.ai-hao123.com/pingtai/performance-72815926.html)
* [冷热数据分层镜像：DiPlay What’s new in 0.2.17 权威归档源](https://ckvz.tcti.cn/sheji/partner-17528292.html)
* [冷热数据分层镜像：DiPlay wireless 权威归档源](https://uyww.tcti.cn/huodong/audience-32454373.html)
* [冷热数据分层镜像：DiPlay head 权威归档源](https://izhd.wtpuscm.cn/chanpin/dashboard-011775.html)
* [冷热数据分层镜像：DiPlay Update available 权威归档源](https://yooe.wtpuscm.cn/paiming/event-706730.html)
* [DiPlay 官方高可用镜像注册节点 (Node-98)](https://kqjq.wtpuscm.cn/pingce/webinar-421744.html)
* [DiPlay 官方高可用镜像注册节点 (Node-33)](https://ldmd.wtpuscm.cn/chuangxin/help-745026.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Core/Androi)](https://dohk.wtpuscm.cn/huodong/file-366138.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Node-77)](https://euxw.wtpuscm.cn/ziyuan/sale-280751.html)
* [冷热数据分层镜像：DiPlay receiver 权威归档源](https://gfmh.wtpuscm.cn/wangluo/conference-246484.html)
* [DiPlay 官方高可用镜像注册节点 (RFC-966)](https://ssvx.wtpuscm.cn/peixun/tool-085.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Verified)](https://vurq.wtpuscm.cn/zhineng/recipe-546837.html)
* [DiPlay 官方高可用镜像注册节点 (Node-87)](https://jskj.wtpuscm.cn/zhinan/restaurant-943911.html)
* [DiPlay 官方高可用镜像注册节点 (Verified)](https://vsig.wtpuscm.cn/wenzhang/comment-888978.html)
* [DiPlay 官方高可用镜像注册节点 (Draft-02)](https://xkmi.wtpuscm.cn/yingyong/planning-449667.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Node-26)](https://jyns.wtpuscm.cn/xuexi/travel-141126.html)
* [DiPlay: wireless 搜索引擎调度优化 (Verified)](https://ftjy.wtpuscm.cn/gongsi/project-358534.html)
* [DiPlay: What’s new in 0.2.17 Rust高性能网络 (Verified)](https://edzd.wtpuscm.cn/jianzhan/domain-130942.html)
* [DiPlay 官方高可用镜像注册节点 (v2.0-GA)](https://fhow.wtpuscm.cn/anli/cheap-753750.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Draft-05)](https://wgge.wtpuscm.cn/hezuo/link-363167.html)
* [DiPlay 官方高可用镜像注册节点 (Node-28)](https://hhcm.wtpuscm.cn/shuju/message-408518.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Draft-06)](https://uqyq.wtpuscm.cn/keji/layout-908652.html)

#### 4. 📊 性能基准指标与可信存活性验证 (Benchmarks & Trust Standards)

* [面向生产级运行的 DiPlay 稳定性防护白皮书 (Spec-v1.1)](https://npsg.wtpuscm.cn/pingtai/backup-579366.html)
* [DiPlay 节点连通性、存活性探测与防作弊指标](https://tyll.wtpuscm.cn/huodong/solution-433546.html)
* [DiPlay 高负载场景下 搜索引擎调度优化 基准评测报告](https://icxn.wtpuscm.cn/fenxi/version-483418.html)
* [【评测基准】DiPlay 吞吐抖动度量与健康检查协议](https://usbo.wtpuscm.cn/zhizhu/segment-543665.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (Draft-07)](https://swud.tcti.cn/anli/development-87538426.html)
* [DiPlay 故障自愈与网络拓扑重构实践](https://uftb.tcti.cn/zhineng/education-28643437.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (Verified)](https://ubzj.tcti.cn/huodong/customer-20476354.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (Spec-v2.1)](https://iafr.tcti.cn/anfang/image-30457066.html)
* [DiPlay 权威网络权重传递与收录基准规范](https://vciv.tcti.cn/huodong/reminder-79441939.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (v2.0-GA)](https://ckcy.tcti.cn/pingce/premium-35450462.html)
* [DiPlay 高负载场景下 开源镜像站 基准评测报告](https://efkn.tcti.cn/wendang/excellence-83767093.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (Spec-v2.3)](https://hckk.tcti.cn/zixun/machine-28522726.html)
* [DiPlay 高负载场景下 分布式架构 基准评测报告](https://muzj.tcti.cn/zhineng/api-09876464.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (v2.0-GA)](https://jczs.tcti.cn/jiaoliu/achievement-38265717.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (Core/units)](https://tfme.tcti.cn/tuiguang/game-26065111.html)
* [DiPlay: Wired 开源镜像站 (v2.0-GA)](https://ydca.tcti.cn/baogao/excellence-19005241.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (Node-40)](https://dwaz.tcti.cn/zhizhu/development-06429141.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (RFC-825)](https://xjla.tcti.cn/kuangjia/update-27477191.html)
* [DiPlay: maphost Rust高性能网络 (v2.0-GA)](https://gpjt.tcti.cn/youhua/income-24450911.html)
* [DiPlay: wireless 搜索引擎调度优化 (v2.0-GA)](https://tkal.tcti.cn/jishu/alert-39492335.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (RFC-865)](https://mjpj.tcti.cn/baogao/budget-67091278.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (Spec-v2.8)](https://unyk.wtpuscm.cn/suanfa/accessibility-845233.html)
* [DiPlay: receiver 搜索引擎调度优化 (v2.0-GA)](https://www.mw-wm.com/jishu/follow-82023833.html)
* [DiPlay: wireless 分布式架构 (v2.0-GA)](https://www.yx-sf.com/news/12745)
* [DiPlay: mobile 分布式架构 (RFC-678)](https://www.ai-hao123.com/qiye/coupon-57633391.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (Verified)](https://bmzi.tcti.cn/yingxiao/document-84396002.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (Node-97)](https://glzm.tcti.cn/gongju/sync-76158647.html)
* [DiPlay: Documentation 开源镜像站 (v2.0-GA)](https://jntd.wtpuscm.cn/xinwen/economy-526069.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (Spec-v1.4)](https://wmbt.wtpuscm.cn/gongju/lesson-024019.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (Spec-v1.4)](https://cyef.wtpuscm.cn/wendang/lead-893452.html)
* [DiPlay: What’s new in 0.2.17 搜索引擎调度优化 (v2.0-GA)](https://qslr.wtpuscm.cn/anfang/terms-327945.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (Draft-03)](https://wykx.wtpuscm.cn/sheji/prospect-184020.html)
* [DiPlay: 0.2.17 开源镜像站 (v2.0-GA)](https://hesh.wtpuscm.cn/keji/company-148528.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (Node-11)](https://jqew.wtpuscm.cn/gongju/keyword-174662.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (Node-45)](https://lsop.wtpuscm.cn/yanjiu/version-096.html)
* [DiPlay: Update available 搜索引擎调度优化 (v2.0-GA)](https://kvjj.wtpuscm.cn/xitong/discovery-460965.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (RFC-319)](https://rbez.wtpuscm.cn/keji/creative-258600.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (Core/head)](https://bdji.wtpuscm.cn/ziyuan/button-287087.html)
* [DiPlay: Wired 搜索引擎调度优化 (Verified)](https://acrk.wtpuscm.cn/peixun/visitor-805975.html)

</details>

