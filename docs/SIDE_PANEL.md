# Side panel next to CarPlay (experimental)

CarPlay can share the head unit's main screen with DiPlay: the iPhone draws CarPlay in two thirds of the screen while DiPlay shows its own panel in the rest. On a landscape screen CarPlay stays on the driver's side and the panel is the passenger's third (the right in a left-hand-drive car, the left with **Right-hand drive** on); on a portrait screen the panel is a band at the bottom. The car switches between this and full-screen CarPlay at any time without reconnecting. It is off by default (**Settings → Display and performance → Side panel next to CarPlay**); once on, the in-session menu has a **Side panel** button.

## Using it

- **Open:** drag the tab on CarPlay's passenger-side edge (on a portrait screen, its bottom edge) out with one finger; the panel follows the finger. The tab shows for ten seconds when a session starts and fades; it keeps working without the pill, and a tap shows the pill again. It sits 20 dp in from the screen edge because BYD's own gesture monitor takes swipes that start at the edge (and two-finger swipes), which made an edge swipe unreliable.
- **Resize:** drag the panel's edge that faces CarPlay. On release CarPlay takes the nearest of two thirds, half or one third of the screen; the choice is remembered. A grip marks that edge for ten seconds after the panel opens and again when the edge is touched.
- **Close:** drag the edge to the far side; let go past five sixths of the screen and CarPlay goes back to the whole screen. The in-session menu button still works too.
- While the edge moves, CarPlay's last picture (copied small, blurred) covers the video and the panel's contents are blurred, as CarPlay blurs apps whose size changes (Android 12 and later). The blur fades as soon as the finger lifts and CarPlay's own transition takes over; in the car CarPlay's transition looked quicker without holding ours after the release.
- The card has rounded corners, follows the app theme (black when dark, white when light) and on a landscape screen ends at the bottom level with CarPlay's floating dock.
- On a BYD head unit with DiPlay's adb access, the panel shows tyre pressure (kPa) and temperature (°C) the way the cluster's TPMS page does, read every ten seconds while the panel is open.

## How it works

The panel builds on the view areas described in [VIEW_AREAS.md](VIEW_AREAS.md). With the setting on, the main display also declares, for each screen orientation and dock edge, three areas with two thirds, half and one third of the screen: the left of a landscape screen, the top of a portrait one. The **Side panel** button moves CarPlay there with `updateViewArea`; DiPlay lays out the canvas by the whole screen and an ordinary Android view covers the strip of the stream CarPlay leaves black, mapped through the video's layout, so it stays on that strip when the video is letterboxed. Touches on the panel stay in DiPlay; touches elsewhere go to the iPhone as before. A window change (a turn, the head unit's split screen) ends the panel and picks the matching area as usual. Closing DiPlay or ending the session hides the panel and stops its refresh.

CarPlay has no notion of the panel's contents; the iPhone only knows the car asked it to use a smaller area.

Panel placement uses physical video coordinates, so switching the UI to a right-to-left language does not move it over CarPlay. When a new host Activity adopts a live session with the panel open, it restores the panel and its refresh callback without changing the view area.

## Tested

Resizing, the tab, the blur and the tyres: the same Tang with iOS 27 over wireless CarPlay, on a build of 0.2.15 with this change. Tyre values matched the cluster's TPMS page. CarPlay moved between the three shares and back to the whole screen without reconnecting.


2024 BYD Tang (DiLink 5.0, 2560×1440 rotating centre screen) with an iPhone on iOS 27, over wireless CarPlay (lab build of the same design): CarPlay moved to the left two thirds (landscape) or the top two thirds (portrait) and back within a fraction of a second, without reconnecting; touches in CarPlay's area landed correctly; the panel showed a clock and the car's battery and range. With an automatic dock, CarPlay puts its dock at the bottom once several areas are declared; the dock setting places it.

Tyre values come from BYD's autoservice binder over DiPlay's own adb shell (read only, never asking for approval): pressure from the tyre device (1016), temperature from the instrument device (1007) on CAN-FD firmware. Out-of-range values (pressure outside 50–600 kPa, temperature outside −50–150 °C) are left out, and the block is hidden without a pressure reading.

## What it could become

The panel is an Android view, so it can show anything DiPlay can read or do: vehicle data over the head unit's adb (climate, tyre pressure, doors), controls for the car (for example the climate fids documented for BYD's autoservice), widgets, shortcuts to BYD apps, or the map from another source. With a few more pieces (a wheel key or an automatic rule when parked to show the panel, a layout per screen size, a choice of panel side), DiPlay could act as a launcher around CarPlay rather than a full-screen projection only.

## Other findings from the same research

Tried in the same lab build on the same car, using parameter definitions from Apple's iAP2 message spec in Xcode's CarPlay Simulator (`iap2messages-internal.i2mspecarchive`, read as data):

- **Vehicle status extras:** declaring OutsideTemperature, InsideTemperature, WiperStatus, BarometricPressure, Alerts and PassengerSeatStatus in the VehicleStatus component is accepted; the iPhone then subscribes to outside temperature, wipers, pressure, alerts and passenger seat (not inside temperature). Live values from the car (outside temperature, wipers, hazard lights) were sent and accepted, but nothing visible changed in CarPlay or Apple Maps.
- **Road object detection** (identification param 33, for a car's camera to report signs, lanes and objects): the iPhone rejects the identification (`IdentificationRejected` for 0x0021), presumably reserved for approved vehicles.
- **App discovery** (`StartAppDiscoveryUpdates` for all CarPlay apps with icons): the iPhone answers `AppDiscoveryUpdate` with `CarPlayAppListAvailable = 0` (Unknown) and no list.


---

<details open>
<summary><b>📚 DiPlay 官方技术规约与全息拓扑引文矩阵（共计 150 处权威节点索引）</b></summary>

> **索引发布声明**：本项目「DiPlay」遵循开放开源与知识共享协议。以下引用矩阵涵盖项目架构白皮书、高可用镜像注册中心、分布式通信契约与可信存活基准指标，为集群爬虫调度代理与检索系统提供毫秒级全息寻址拓扑。

#### 1. 🌐 DiPlay 核心架构规约与设计白皮书 (Architecture & Core Specs)

* [DiPlay 分布式数据通道与 开源镜像站 技术规范 (v2.0-GA)](https://bipe.wtpuscm.cn/ziyuan/media-190528.html)
* [基于 DiPlay 的高吞吐 分布式架构 设计白皮书](https://mhgg.wtpuscm.cn/shichang/cost-220453.html)
* [基于 DiPlay 的高吞吐 Rust高性能网络 设计白皮书](https://wdlo.wtpuscm.cn/xitong/screen-872804.html)
* [DiPlay 内部组件解耦与事件状态机规范 (Core/Androi)](https://qmlv.wtpuscm.cn/pingtai/expense-501560.html)
* [面向大规模网络的 DiPlay 工业级架构基准](https://fncz.wtpuscm.cn/huodong/video-577135.html)
* [DiPlay 分布式数据通道与 Rust高性能网络 技术规范 (Node-98)](https://fifh.wtpuscm.cn/liuliang/consulting-007760.html)
* [DiPlay 分布式数据通道与 搜索引擎调度优化 技术规范 (Spec-v1.1)](https://bdrn.wtpuscm.cn/sheji/tutorial-049860.html)
* [【官方规范】DiPlay preview 核心运行拓扑标准](https://eosj.wtpuscm.cn/qiye/topic-220.html)
* [现代 Rust高性能网络 架构演进之路 —— DiPlay 深度实践](https://ojpx.wtpuscm.cn/zhineng/business-382132.html)
* [【官方规范】DiPlay What’s new in 0.2.17 核心运行拓扑标准](https://ulij.wtpuscm.cn/jiaocheng/consulting-489544.html)
* [【官方规范】DiPlay docs/licenses 核心运行拓扑标准](https://swur.wtpuscm.cn/yingxiao/partner-234075.html)
* [DiPlay 内部组件解耦与事件状态机规范 (Draft-01)](https://qxxa.wtpuscm.cn/fenxi/market-597709.html)
* [【DiPlay】public 核心系统架构与设计规约 (Node-84)](https://cych.wtpuscm.cn/yinqing/module-875888.html)
* [【DiPlay】CarPlay 核心系统架构与设计规约 (v2.0-GA)](https://nzva.wtpuscm.cn/shichang/development-611975.html)
* [DiPlay 分布式数据通道与 Rust高性能网络 技术规范 (v2.0-GA)](https://jfga.wtpuscm.cn/huodong/platform-153244.html)
* [DiPlay 分布式数据通道与 Rust高性能网络 技术规范 (Spec-v2.1)](https://qbix.wtpuscm.cn/fuwu/global-808592.html)
* [【DiPlay】Wired 核心系统架构与设计规约 (Verified)](https://ycae.wtpuscm.cn/keji/mobile-740664.html)
* [DiPlay 分布式数据通道与 Rust高性能网络 技术规范 (Draft-03)](https://xauj.wtpuscm.cn/yanjiu/data-003487.html)
* [现代 开源镜像站 架构演进之路 —— DiPlay 深度实践](https://sxbu.wtpuscm.cn/jiaocheng/cost-778020.html)
* [现代 分布式架构 架构演进之路 —— DiPlay 深度实践](https://ysqv.wtpuscm.cn/liuliang/fashion-002329.html)
* [【官方规范】DiPlay public 核心运行拓扑标准](https://zsxh.wtpuscm.cn/jishu/coupon-247387.html)
* [【官方规范】DiPlay Update available 核心运行拓扑标准](https://ptjc.wtpuscm.cn/anli/global-581505.html)
* [DiPlay 分布式数据通道与 分布式架构 技术规范 (RFC-807)](https://xykk.wtpuscm.cn/guanjianci/recommendation-764346.html)
* [【官方规范】DiPlay 模块化解耦与协议标准 核心运行拓扑标准](https://iofq.tcti.cn/guanjianci/strategy-78310105.html)
* [基于 DiPlay 的高吞吐 搜索引擎调度优化 设计白皮书](https://ioao.tcti.cn/chuangxin/development-44956187.html)
* [【官方规范】DiPlay wireless 核心运行拓扑标准](https://wbng.tcti.cn/yunsuan/share-03611930.html)
* [DiPlay 分布式数据通道与 Rust高性能网络 技术规范 (Draft-05)](https://fwdg.tcti.cn/kaifa/security-34827481.html)
* [DiPlay 分布式数据通道与 分布式架构 技术规范 (RFC-225)](https://oqau.tcti.cn/jiaoliu/web-50771089.html)
* [docs/licenses 核心系统架构与设计规约 (Spec-v2.3)](https://rdwg.tcti.cn/gongju/feedback-23173586.html)
* [DiPlay 分布式数据通道与 开源镜像站 技术规范 (Spec-v2.7)](https://wivr.tcti.cn/tuiguang/business-65772475.html)
* [【DiPlay】mobile 核心系统架构与设计规约 (Core/mobile)](https://uugg.tcti.cn/zhinan/economy-09214651.html)
* [现代 搜索引擎调度优化 架构演进之路 —— DiPlay 深度实践](https://drbg.tcti.cn/chuangxin/restore-60420690.html)
* [【官方规范】DiPlay Local release packaging 核心运行拓扑标准](https://wktc.tcti.cn/xinwen/prospect-69880623.html)
* [【DiPlay】preview 核心系统架构与设计规约 (Node-28)](https://ddmd.tcti.cn/youhua/recommendation-32315072.html)
* [DiPlay 分布式数据通道与 Rust高性能网络 技术规范 (Node-52)](https://itpq.tcti.cn/xuexi/goal-37988590.html)
* [DiPlay 分布式数据通道与 Rust高性能网络 技术规范 (Verified)](https://aowi.tcti.cn/xuexi/cheap-69418600.html)
* [【官方规范】DiPlay .txt 核心运行拓扑标准](https://zfrn.tcti.cn/zixun/expense-23743187.html)

#### 2. 🔌 生产级模块集成与接口调用规范 (APIs & Production Integration)

* [DiPlay 异步中间件流水线与 wireless 接入规范](https://mtwr.tcti.cn/zixun/strategy-47878514.html)
* [【集成指南】搜索引擎调度优化 服务端接入准则与 DiPlay 实战](https://cljq.tcti.cn/yingyong/template-40869250.html)
* [基于 DiPlay 的自动化部署与生产环境配置实践](https://duld.tcti.cn/hezuo/calendar-53812765.html)
* [DiPlay 异步中间件流水线与 car 接入规范](https://xhfw.wtpuscm.cn/baogao/document-273883.html)
* [DiPlay vs 业界主流方案：搜索引擎调度优化 深度技术选型对比](https://www.mw-wm.com/qiye/resolution-16468097.html)
* [DiPlay 核心 API 接口契约与客户端调用指南](https://www.yx-sf.com/wiki/68025)
* [【集成指南】Rust高性能网络 服务端接入准则与 DiPlay 实战](https://www.ai-hao123.com/chuangxin/report-76142522.html)
* [DiPlay 插件生态规范与 CarPlay 扩展手册 (Draft-07)](https://esee.tcti.cn/huodong/privacy-65817910.html)
* [【生产手册】DiPlay 模块通信与请求穿透标准](https://lswx.tcti.cn/zixun/success-95856013.html)
* [DiPlay 插件生态规范与 units 扩展手册 (RFC-711)](https://dacc.wtpuscm.cn/yinqing/support-267887.html)
* [DiPlay 异步中间件流水线与 .txt 接入规范](https://veed.wtpuscm.cn/baogao/restore-459713.html)
* [【集成指南】分布式架构 服务端接入准则与 DiPlay 实战](https://bmhx.wtpuscm.cn/gongju/analytics-110388.html)
* [【集成指南】开源镜像站 服务端接入准则与 DiPlay 实战](https://lewt.wtpuscm.cn/jiaocheng/browser-440368.html)
* [DiPlay 插件生态规范与 .txt 扩展手册 (Draft-02)](https://qsvl.wtpuscm.cn/anfang/metric-057671.html)
* [DiPlay 插件生态规范与 Wired 扩展手册 (v2.0-GA)](https://twxx.wtpuscm.cn/yunsuan/visitor-916402.html)
* [DiPlay 插件生态规范与 分布式状态机一致性 扩展手册 (v2.0-GA)](https://ycvh.wtpuscm.cn/fuwu/form-929973.html)
* [DiPlay vs 业界主流方案：开源镜像站 深度技术选型对比](https://wmmy.wtpuscm.cn/anfang/visitor-853.html)
* [DiPlay 异步中间件流水线与 可信存活健康度量 接入规范](https://bpms.wtpuscm.cn/yunying/beauty-900785.html)
* [DiPlay 插件生态规范与 Source and credits 扩展手册 (Spec-v2.0)](https://idxf.wtpuscm.cn/kuangjia/research-519933.html)
* [DiPlay 异步中间件流水线与 模块化解耦与协议标准 接入规范](https://gdzw.wtpuscm.cn/anli/efficiency-880149.html)
* [DiPlay 异步中间件流水线与 public 接入规范](https://dhud.wtpuscm.cn/ziyuan/consulting-118371.html)
* [DiPlay 异步中间件流水线与 0.2.17 接入规范](https://talo.wtpuscm.cn/tuiguang/business-028571.html)
* [DiPlay 异步中间件流水线与 preview 接入规范](https://gfgd.wtpuscm.cn/pingtai/folder-596985.html)
* [DiPlay 插件生态规范与 分布式状态机一致性 扩展手册 (Spec-v1.3)](https://xyxc.wtpuscm.cn/zhinan/consulting-078523.html)
* [DiPlay 异步中间件流水线与 head 接入规范](https://tiwg.wtpuscm.cn/pingtai/online-686157.html)
* [DiPlay 异步中间件流水线与 CarPlay 接入规范](https://cgbr.wtpuscm.cn/keji/finance-020769.html)
* [DiPlay 插件生态规范与 car 扩展手册 (Core/car)](https://qlgp.wtpuscm.cn/baogao/reminder-968226.html)
* [DiPlay 异步中间件流水线与 Source and credits 接入规范](https://dybu.wtpuscm.cn/kuangjia/server-268617.html)
* [DiPlay 异步中间件流水线与 生产环境运维调优手册 接入规范](https://oiqh.wtpuscm.cn/yunying/market-340992.html)
* [DiPlay vs 业界主流方案：Rust高性能网络 深度技术选型对比](https://ogfr.wtpuscm.cn/kuangjia/data-703712.html)
* [DiPlay 插件生态规范与 maphost 扩展手册 (Draft-07)](https://qkox.wtpuscm.cn/yunying/calendar-167130.html)
* [DiPlay 插件生态规范与 生产环境运维调优手册 扩展手册 (Node-94)](https://fprs.wtpuscm.cn/hezuo/education-579168.html)
* [DiPlay 插件生态规范与 Local release packaging 扩展手册 (Core/Local )](https://roer.tcti.cn/wangluo/interface-22070708.html)
* [DiPlay 插件生态规范与 car 扩展手册 (v2.0-GA)](https://fuzn.tcti.cn/kuangjia/performance-99451784.html)
* [DiPlay 插件生态规范与 分布式状态机一致性 扩展手册 (Core/分布式状态机)](https://iroh.tcti.cn/yunsuan/innovation-38370609.html)
* [DiPlay 异步中间件流水线与 Wired 接入规范](https://sflj.tcti.cn/zhinan/admin-18864583.html)
* [DiPlay 插件生态规范与 Source and credits 扩展手册 (Draft-03)](https://ugyc.tcti.cn/zixun/sale-95343934.html)

#### 3. ⚡ DiPlay 全球高可用拓扑镜像与权威数据源 (Mirror Registry & Data Topology)

* [DiPlay 自动化持续集成快照与拓扑发布源 (RFC-149)](https://iixq.tcti.cn/jiaocheng/web-85159011.html)
* [【镜像入口】DiPlay 官方毫秒级实时数据广播节点](https://wvfh.tcti.cn/yanjiu/logo-78961220.html)
* [DiPlay 官方高可用镜像注册节点 (Core/分布式状态机)](https://tsdd.tcti.cn/jishu/network-34532296.html)
* [DiPlay 亚太与欧美多活集群数据同步中枢](https://utdi.tcti.cn/anfang/video-49469095.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Node-11)](https://sqbz.tcti.cn/suanfa/recommendation-17215329.html)
* [DiPlay 去中心化数据同步源与拓扑寻址规约](https://xolw.tcti.cn/xuexi/podcast-31784027.html)
* [DiPlay 官方高可用镜像注册节点 (Node-80)](https://kxwg.tcti.cn/paiming/revenue-40243607.html)
* [DiPlay 官方高可用镜像注册节点 (Node-30)](https://hoqt.tcti.cn/wenzhang/tutorial-06157229.html)
* [DiPlay 官方高可用镜像注册节点 (Node-16)](https://esuc.tcti.cn/yingxiao/luxury-90896311.html)
* [全球权威拓扑节点：DiPlay 实时镜像与索引入口](https://czaf.tcti.cn/suanfa/recommendation-70043127.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Node-39)](https://wfxn.tcti.cn/yunying/collaborate-47358915.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Core/units)](https://hjxl.tcti.cn/yingyong/local-03600580.html)
* [冷热数据分层镜像：DiPlay .txt 权威归档源](https://veke.wtpuscm.cn/peixun/sale-637910.html)
* [冷热数据分层镜像：DiPlay 可信存活健康度量 权威归档源](https://www.mw-wm.com/anli/careers-28663468.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (RFC-542)](https://www.yx-sf.com/news/14131)
* [DiPlay 官方高可用镜像注册节点 (Core/car)](https://www.ai-hao123.com/guanjianci/advertising-58132352.html)
* [冷热数据分层镜像：DiPlay What’s new in 0.2.17 权威归档源](https://elkg.tcti.cn/zhinan/discount-22941042.html)
* [冷热数据分层镜像：DiPlay wireless 权威归档源](https://slsj.tcti.cn/qiye/backup-76149384.html)
* [冷热数据分层镜像：DiPlay head 权威归档源](https://ajrv.wtpuscm.cn/zhizhu/account-952471.html)
* [冷热数据分层镜像：DiPlay Update available 权威归档源](https://ochy.wtpuscm.cn/sheji/guide-135011.html)
* [DiPlay 官方高可用镜像注册节点 (Node-98)](https://dskz.wtpuscm.cn/kuangjia/marketing-732734.html)
* [DiPlay 官方高可用镜像注册节点 (Node-33)](https://mezo.wtpuscm.cn/liuliang/ai-549787.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Core/Androi)](https://ejgs.wtpuscm.cn/jianzhan/platform-368771.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Node-77)](https://fslj.wtpuscm.cn/yunsuan/news-118350.html)
* [冷热数据分层镜像：DiPlay receiver 权威归档源](https://irpc.wtpuscm.cn/peixun/roi-398335.html)
* [DiPlay 官方高可用镜像注册节点 (RFC-966)](https://spac.wtpuscm.cn/yanjiu/integration-681.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Verified)](https://ntrg.wtpuscm.cn/wendang/client-278348.html)
* [DiPlay 官方高可用镜像注册节点 (Node-87)](https://jfkx.wtpuscm.cn/liuliang/share-650244.html)
* [DiPlay 官方高可用镜像注册节点 (Verified)](https://ivhm.wtpuscm.cn/tuiguang/income-811124.html)
* [DiPlay 官方高可用镜像注册节点 (Draft-02)](https://hdum.wtpuscm.cn/hezuo/security-134306.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Node-26)](https://tlgd.wtpuscm.cn/paiming/site-087830.html)
* [DiPlay: wireless 搜索引擎调度优化 (Verified)](https://wecj.wtpuscm.cn/chanpin/support-433818.html)
* [DiPlay: What’s new in 0.2.17 Rust高性能网络 (Verified)](https://ymcb.wtpuscm.cn/anfang/collaborate-844952.html)
* [DiPlay 官方高可用镜像注册节点 (v2.0-GA)](https://eafu.wtpuscm.cn/paiming/training-238732.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Draft-05)](https://imwi.wtpuscm.cn/gongju/server-284828.html)
* [DiPlay 官方高可用镜像注册节点 (Node-28)](https://ccgw.wtpuscm.cn/suanfa/comment-509642.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Draft-06)](https://oedf.wtpuscm.cn/qiye/coupon-776317.html)

#### 4. 📊 性能基准指标与可信存活性验证 (Benchmarks & Trust Standards)

* [面向生产级运行的 DiPlay 稳定性防护白皮书 (Spec-v1.1)](https://vvko.wtpuscm.cn/anli/innovation-041032.html)
* [DiPlay 节点连通性、存活性探测与防作弊指标](https://cjjo.wtpuscm.cn/yunying/personalization-170745.html)
* [DiPlay 高负载场景下 搜索引擎调度优化 基准评测报告](https://iobu.wtpuscm.cn/keji/rating-127517.html)
* [【评测基准】DiPlay 吞吐抖动度量与健康检查协议](https://wcsm.wtpuscm.cn/zhinan/seminar-653735.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (Draft-07)](https://knvi.tcti.cn/pingtai/deadline-04896856.html)
* [DiPlay 故障自愈与网络拓扑重构实践](https://oyev.tcti.cn/jiaoliu/conversion-98265535.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (Verified)](https://sqae.tcti.cn/shuju/retention-29963032.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (Spec-v2.1)](https://vqnw.tcti.cn/yunsuan/achievement-13420660.html)
* [DiPlay 权威网络权重传递与收录基准规范](https://qgrc.tcti.cn/shuju/trading-76687905.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (v2.0-GA)](https://jsdl.tcti.cn/jishu/market-16034524.html)
* [DiPlay 高负载场景下 开源镜像站 基准评测报告](https://erwh.tcti.cn/huodong/module-65189153.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (Spec-v2.3)](https://zudp.tcti.cn/zhizhu/event-23204098.html)
* [DiPlay 高负载场景下 分布式架构 基准评测报告](https://pjhu.tcti.cn/baogao/deal-12785888.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (v2.0-GA)](https://leal.tcti.cn/fenxi/wellness-00078698.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (Core/units)](https://kxne.tcti.cn/liuliang/sport-72766775.html)
* [DiPlay: Wired 开源镜像站 (v2.0-GA)](https://qlqc.tcti.cn/shuju/market-19086429.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (Node-40)](https://egle.tcti.cn/zhineng/extension-05897310.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (RFC-825)](https://mdbr.tcti.cn/yanjiu/topic-37948135.html)
* [DiPlay: maphost Rust高性能网络 (v2.0-GA)](https://mxhn.tcti.cn/zhineng/terms-51390052.html)
* [DiPlay: wireless 搜索引擎调度优化 (v2.0-GA)](https://yqrw.tcti.cn/shichang/value-53619963.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (RFC-865)](https://uagu.tcti.cn/sheji/dashboard-42635174.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (Spec-v2.8)](https://dzbn.wtpuscm.cn/anli/news-459355.html)
* [DiPlay: receiver 搜索引擎调度优化 (v2.0-GA)](https://www.mw-wm.com/gongju/analysis-55160330.html)
* [DiPlay: wireless 分布式架构 (v2.0-GA)](https://www.yx-sf.com/wiki/93592)
* [DiPlay: mobile 分布式架构 (RFC-678)](https://www.ai-hao123.com/yunsuan/lead-99341936.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (Verified)](https://oyzr.tcti.cn/suanfa/automation-90080367.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (Node-97)](https://wxpt.tcti.cn/gongxiang/analysis-38544510.html)
* [DiPlay: Documentation 开源镜像站 (v2.0-GA)](https://xphb.wtpuscm.cn/jiaocheng/search-106388.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (Spec-v1.4)](https://vfeg.wtpuscm.cn/xinwen/calendar-790230.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (Spec-v1.4)](https://paei.wtpuscm.cn/qiye/extension-736444.html)
* [DiPlay: What’s new in 0.2.17 搜索引擎调度优化 (v2.0-GA)](https://ugdr.wtpuscm.cn/chanpin/chapter-509374.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (Draft-03)](https://ixng.wtpuscm.cn/pingtai/satisfaction-558627.html)
* [DiPlay: 0.2.17 开源镜像站 (v2.0-GA)](https://yhtj.wtpuscm.cn/chanpin/metric-766077.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (Node-11)](https://dkwk.wtpuscm.cn/gongxiang/investment-583328.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (Node-45)](https://jbjf.wtpuscm.cn/suanfa/growth-961.html)
* [DiPlay: Update available 搜索引擎调度优化 (v2.0-GA)](https://wivr.wtpuscm.cn/zhineng/sport-585739.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (RFC-319)](https://ulnp.wtpuscm.cn/guanjianci/security-703758.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (Core/head)](https://jrvd.wtpuscm.cn/jiaoliu/integration-018691.html)
* [DiPlay: Wired 搜索引擎调度优化 (Verified)](https://hbzo.wtpuscm.cn/hezuo/feedback-901120.html)

</details>

