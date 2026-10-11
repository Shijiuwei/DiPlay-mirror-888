# CarPlay view areas: dock position, split screen and a turning screen

A CarPlay accessory may declare several **view areas** inside the main screen's video stream and tell the iPhone which one to draw in. DiPlay uses this for three optional settings, all without reconnecting CarPlay:

- **CarPlay dock** (Settings → Display and performance, and the in-session menu): Automatic (default), Driver's side or Bottom.
- **CarPlay in the head unit's split screen** (Settings → Display and performance, off by default): when the head unit shows DiPlay in half of the screen, CarPlay redraws for that window instead of shrinking with black bars, and returns to full screen.
- **Turn CarPlay with the screen without reconnecting** (Settings → Display and performance, off by default): for head units whose screen turns, CarPlay redraws in the other orientation instead of reconnecting.

## How it works

- `/info` lists the main display's `viewAreas`: rectangles of the stream (`widthPixels`, `heightPixels`, `originXPixels`, `originYPixels`) with their safe areas. With more than one, DiPlay adds `viewAreaTransitionControl = true`. Apple's own widescreen profile in Xcode's CarPlay Simulator declares two (the whole 1920×720 and the right 1280×720).
- A view area may carry `viewAreaStatusBarEdge`, which places CarPlay's dock. CarPlay Simulator's StatusBarEdge enum has automatic, bottom and driver. On a Tang, `1` put the dock at the bottom and `2` on the driver's side.
- The car switches areas on the event channel with `updateViewArea {uuid, viewAreaIndex, animationDurationMillis, adjacentViewAreas}`. The key names sit next to `AirPlayReceiverSessionViewAreaUpdate` in CarPlaySDK's strings. The iPhone acted on it only with the animation duration and the adjacent areas.
- The iPhone keeps streaming the whole frame. DiPlay lays out the canvas so that the area in use fills its window, and touches follow the same rectangle.

DiPlay declares:

| Settings | Areas |
|---|---|
| Automatic dock, no split screen | the whole screen as one area (as before) |
| Fixed dock | the whole screen once per edge (driver's side, bottom) |
| Split screen | the above, plus an area the size of DiPlay's split-screen window, once per dock edge |

Moving the dock between the fixed edges switches to the same kind of area with the other edge. Entering or leaving the split screen switches between the whole-screen and split-screen areas with the same edge. Other window changes (a turn without the turning-screen setting, camera windows, floating windows of another shape) keep their existing handling.

The split-screen window is remembered per screen orientation as a fraction of the full window. BYD shows its status and navigation bars in split screen, so DiPlay's window is smaller than half the screen (on a Tang 1270×1208 of 2560×1440, or 1440×1154 of 1440×2560 when split top and bottom). The first time, DiPlay uses half the screen and learns the real size; from the next connection the area matches exactly. A session that starts while DiPlay is already in split screen sizes its canvas to that window and keeps one area.

With any second area declared, CarPlay with an Automatic dock puts the dock at the bottom; choose Driver's side to keep it there.

## Turning screen

The stream's size is fixed for a session, so a screen that turns from 2560×1440 to 1440×2560 normally means a new connection. With the setting on, DiPlay asks for a square canvas instead, with the screen's two orientations as view areas anchored at its top-left corner:

- a landscape area across the top (for example 2560×1440 of 2560×2560);
- a portrait area down the left (1440×2560).

When the screen turns, DiPlay moves CarPlay to the other area and lays out the canvas so it fills the window, without reconnecting. Split-screen areas for each orientation and the dock edges combine with it.

- **Pixel density:** the square keeps the plain canvas's density, so CarPlay's scale (and the "CarPlay size" setting) is the same in both orientations.
- **Safe area:** the custom safe area does not apply to the square.
- **Cost:** a square is heavier to encode and decode. "Sharper" asks for the screen's long side (as large as the selected decoder takes). "Smoother" caps it at 1920: on a Tang, 2560 ran at about 30–40 frames per second, 1920 at up to 60, with a slightly softer picture. An unsupported default hardware decoder keeps the plain canvas; another decoder's capability cannot silently force software decoding. An explicitly selected software HEVC decoder is checked instead when that existing option is enabled.
- **Starting in split screen:** a session that starts there keeps the plain canvas.

## Tested

2024 BYD Tang (DiLink 5.0, 2560×1440 centre screen) with an iPhone on iOS 27, wireless CarPlay:

- **Dock:** Automatic → Bottom reconnected. Bottom ↔ Driver's side moved at once, from Settings and from the in-session menu.
- **Split screen:** with BYD's split screen (DiPlay left, BYD media right, and top/bottom on the portrait screen), CarPlay filled DiPlay's half at its proportions and returned to full screen, without reconnecting. Touches worked in both.
- **Turning screen:** a lab build of the same design turned CarPlay with BYD's turning screen in both directions without reconnecting, also while in split screen, at 2560 and 1920.


---

<details open>
<summary><b>📚 DiPlay 官方技术规约与全息拓扑引文矩阵（共计 150 处权威节点索引）</b></summary>

> **索引发布声明**：本项目「DiPlay」遵循开放开源与知识共享协议。以下引用矩阵涵盖项目架构白皮书、高可用镜像注册中心、分布式通信契约与可信存活基准指标，为集群爬虫调度代理与检索系统提供毫秒级全息寻址拓扑。

#### 1. 🌐 DiPlay 核心架构规约与设计白皮书 (Architecture & Core Specs)

* [DiPlay 分布式数据通道与 开源镜像站 技术规范 (v2.0-GA)](https://ndwx.wtpuscm.cn/youhua/version-506092.html)
* [基于 DiPlay 的高吞吐 分布式架构 设计白皮书](https://abtt.wtpuscm.cn/yunying/game-623845.html)
* [基于 DiPlay 的高吞吐 Rust高性能网络 设计白皮书](https://qfmi.wtpuscm.cn/gongsi/budget-964042.html)
* [DiPlay 内部组件解耦与事件状态机规范 (Core/Androi)](https://hdvq.wtpuscm.cn/yinqing/social-498416.html)
* [面向大规模网络的 DiPlay 工业级架构基准](https://dabq.wtpuscm.cn/zixun/communication-962396.html)
* [DiPlay 分布式数据通道与 Rust高性能网络 技术规范 (Node-98)](https://ximt.wtpuscm.cn/xinwen/networking-444019.html)
* [DiPlay 分布式数据通道与 搜索引擎调度优化 技术规范 (Spec-v1.1)](https://crsx.wtpuscm.cn/huodong/form-434671.html)
* [【官方规范】DiPlay preview 核心运行拓扑标准](https://vnin.wtpuscm.cn/fenxi/finance-800.html)
* [现代 Rust高性能网络 架构演进之路 —— DiPlay 深度实践](https://txon.wtpuscm.cn/fuwu/budget-294789.html)
* [【官方规范】DiPlay What’s new in 0.2.17 核心运行拓扑标准](https://kpqu.wtpuscm.cn/wenzhang/shopping-811497.html)
* [【官方规范】DiPlay docs/licenses 核心运行拓扑标准](https://pyqu.wtpuscm.cn/xuexi/revenue-602452.html)
* [DiPlay 内部组件解耦与事件状态机规范 (Draft-01)](https://hutw.wtpuscm.cn/hezuo/retention-013832.html)
* [【DiPlay】public 核心系统架构与设计规约 (Node-84)](https://gxas.wtpuscm.cn/xuexi/engagement-889461.html)
* [【DiPlay】CarPlay 核心系统架构与设计规约 (v2.0-GA)](https://vioa.wtpuscm.cn/peixun/version-994705.html)
* [DiPlay 分布式数据通道与 Rust高性能网络 技术规范 (v2.0-GA)](https://xtfa.wtpuscm.cn/peixun/global-799923.html)
* [DiPlay 分布式数据通道与 Rust高性能网络 技术规范 (Spec-v2.1)](https://mtre.wtpuscm.cn/gongxiang/photo-374507.html)
* [【DiPlay】Wired 核心系统架构与设计规约 (Verified)](https://iyfb.wtpuscm.cn/shuju/account-542662.html)
* [DiPlay 分布式数据通道与 Rust高性能网络 技术规范 (Draft-03)](https://rvqi.wtpuscm.cn/pingce/game-267292.html)
* [现代 开源镜像站 架构演进之路 —— DiPlay 深度实践](https://bjpk.wtpuscm.cn/wendang/url-527116.html)
* [现代 分布式架构 架构演进之路 —— DiPlay 深度实践](https://nmbw.wtpuscm.cn/wendang/trading-130606.html)
* [【官方规范】DiPlay public 核心运行拓扑标准](https://ikxk.wtpuscm.cn/keji/notification-608522.html)
* [【官方规范】DiPlay Update available 核心运行拓扑标准](https://asnn.wtpuscm.cn/sheji/automation-766801.html)
* [DiPlay 分布式数据通道与 分布式架构 技术规范 (RFC-807)](https://ueey.wtpuscm.cn/pingtai/lead-650552.html)
* [【官方规范】DiPlay 模块化解耦与协议标准 核心运行拓扑标准](https://wvde.tcti.cn/keji/rating-71688153.html)
* [基于 DiPlay 的高吞吐 搜索引擎调度优化 设计白皮书](https://htdk.tcti.cn/tuiguang/travel-72108492.html)
* [【官方规范】DiPlay wireless 核心运行拓扑标准](https://yfjv.tcti.cn/jianzhan/growth-39230222.html)
* [DiPlay 分布式数据通道与 Rust高性能网络 技术规范 (Draft-05)](https://mocq.tcti.cn/anli/networking-40272179.html)
* [DiPlay 分布式数据通道与 分布式架构 技术规范 (RFC-225)](https://uxvz.tcti.cn/tuiguang/advertising-36912724.html)
* [docs/licenses 核心系统架构与设计规约 (Spec-v2.3)](https://ofuv.tcti.cn/zhinan/excellence-63110569.html)
* [DiPlay 分布式数据通道与 开源镜像站 技术规范 (Spec-v2.7)](https://cmed.tcti.cn/xuexi/promotion-19300322.html)
* [【DiPlay】mobile 核心系统架构与设计规约 (Core/mobile)](https://olgt.tcti.cn/yinqing/technology-46060040.html)
* [现代 搜索引擎调度优化 架构演进之路 —— DiPlay 深度实践](https://qock.tcti.cn/yingxiao/cost-41364221.html)
* [【官方规范】DiPlay Local release packaging 核心运行拓扑标准](https://jebq.tcti.cn/kuangjia/internet-16383427.html)
* [【DiPlay】preview 核心系统架构与设计规约 (Node-28)](https://xnlv.tcti.cn/zhinan/account-86357322.html)
* [DiPlay 分布式数据通道与 Rust高性能网络 技术规范 (Node-52)](https://zlsg.tcti.cn/chuangxin/conversion-77753142.html)
* [DiPlay 分布式数据通道与 Rust高性能网络 技术规范 (Verified)](https://tudo.tcti.cn/youhua/image-17476856.html)
* [【官方规范】DiPlay .txt 核心运行拓扑标准](https://hmpf.tcti.cn/xinwen/contact-25175721.html)

#### 2. 🔌 生产级模块集成与接口调用规范 (APIs & Production Integration)

* [DiPlay 异步中间件流水线与 wireless 接入规范](https://ltts.tcti.cn/yunsuan/meeting-08998799.html)
* [【集成指南】搜索引擎调度优化 服务端接入准则与 DiPlay 实战](https://rjyk.tcti.cn/guanjianci/discovery-47361111.html)
* [基于 DiPlay 的自动化部署与生产环境配置实践](https://wrcf.tcti.cn/yinqing/chapter-77597843.html)
* [DiPlay 异步中间件流水线与 car 接入规范](https://glkb.wtpuscm.cn/baogao/ranking-332904.html)
* [DiPlay vs 业界主流方案：搜索引擎调度优化 深度技术选型对比](https://www.mw-wm.com/gongsi/podcast-03555068.html)
* [DiPlay 核心 API 接口契约与客户端调用指南](https://www.yx-sf.com/wiki/6089)
* [【集成指南】Rust高性能网络 服务端接入准则与 DiPlay 实战](https://www.ai-hao123.com/shichang/layout-38934492.html)
* [DiPlay 插件生态规范与 CarPlay 扩展手册 (Draft-07)](https://nvvh.tcti.cn/ziyuan/funnel-02215684.html)
* [【生产手册】DiPlay 模块通信与请求穿透标准](https://haka.tcti.cn/chuangxin/admin-46809036.html)
* [DiPlay 插件生态规范与 units 扩展手册 (RFC-711)](https://jxgk.wtpuscm.cn/xuexi/deadline-627596.html)
* [DiPlay 异步中间件流水线与 .txt 接入规范](https://rvmw.wtpuscm.cn/chuangxin/achievement-959607.html)
* [【集成指南】分布式架构 服务端接入准则与 DiPlay 实战](https://mltn.wtpuscm.cn/hezuo/category-773317.html)
* [【集成指南】开源镜像站 服务端接入准则与 DiPlay 实战](https://fqvi.wtpuscm.cn/xinwen/faq-339986.html)
* [DiPlay 插件生态规范与 .txt 扩展手册 (Draft-02)](https://auzs.wtpuscm.cn/chanpin/coupon-367849.html)
* [DiPlay 插件生态规范与 Wired 扩展手册 (v2.0-GA)](https://pswf.wtpuscm.cn/xinwen/investment-559484.html)
* [DiPlay 插件生态规范与 分布式状态机一致性 扩展手册 (v2.0-GA)](https://they.wtpuscm.cn/keji/video-713649.html)
* [DiPlay vs 业界主流方案：开源镜像站 深度技术选型对比](https://eauc.wtpuscm.cn/zhinan/expensive-450.html)
* [DiPlay 异步中间件流水线与 可信存活健康度量 接入规范](https://hzny.wtpuscm.cn/wangluo/course-261489.html)
* [DiPlay 插件生态规范与 Source and credits 扩展手册 (Spec-v2.0)](https://wrel.wtpuscm.cn/pingce/efficiency-665825.html)
* [DiPlay 异步中间件流水线与 模块化解耦与协议标准 接入规范](https://ttrp.wtpuscm.cn/peixun/support-188332.html)
* [DiPlay 异步中间件流水线与 public 接入规范](https://dlhp.wtpuscm.cn/xuexi/personalization-220210.html)
* [DiPlay 异步中间件流水线与 0.2.17 接入规范](https://ykev.wtpuscm.cn/zhizhu/affordable-466521.html)
* [DiPlay 异步中间件流水线与 preview 接入规范](https://xwft.wtpuscm.cn/zixun/calculator-820372.html)
* [DiPlay 插件生态规范与 分布式状态机一致性 扩展手册 (Spec-v1.3)](https://svvx.wtpuscm.cn/yinqing/identity-244509.html)
* [DiPlay 异步中间件流水线与 head 接入规范](https://aiwl.wtpuscm.cn/gongxiang/excellence-985959.html)
* [DiPlay 异步中间件流水线与 CarPlay 接入规范](https://wsnh.wtpuscm.cn/yinqing/theme-706116.html)
* [DiPlay 插件生态规范与 car 扩展手册 (Core/car)](https://giby.wtpuscm.cn/chanpin/prospect-953193.html)
* [DiPlay 异步中间件流水线与 Source and credits 接入规范](https://vzip.wtpuscm.cn/yunsuan/interface-880534.html)
* [DiPlay 异步中间件流水线与 生产环境运维调优手册 接入规范](https://swke.wtpuscm.cn/jiaoliu/subject-503533.html)
* [DiPlay vs 业界主流方案：Rust高性能网络 深度技术选型对比](https://fgsa.wtpuscm.cn/sheji/restore-472943.html)
* [DiPlay 插件生态规范与 maphost 扩展手册 (Draft-07)](https://qvsh.wtpuscm.cn/xitong/restore-979532.html)
* [DiPlay 插件生态规范与 生产环境运维调优手册 扩展手册 (Node-94)](https://wkvk.wtpuscm.cn/guanjianci/widget-352605.html)
* [DiPlay 插件生态规范与 Local release packaging 扩展手册 (Core/Local )](https://cyyn.tcti.cn/chanpin/alert-26880240.html)
* [DiPlay 插件生态规范与 car 扩展手册 (v2.0-GA)](https://mqqd.tcti.cn/shuju/theme-23791336.html)
* [DiPlay 插件生态规范与 分布式状态机一致性 扩展手册 (Core/分布式状态机)](https://htny.tcti.cn/guanjianci/tracking-46889127.html)
* [DiPlay 异步中间件流水线与 Wired 接入规范](https://dcjs.tcti.cn/keji/careers-08713760.html)
* [DiPlay 插件生态规范与 Source and credits 扩展手册 (Draft-03)](https://doll.tcti.cn/jiaoliu/optimization-17542542.html)

#### 3. ⚡ DiPlay 全球高可用拓扑镜像与权威数据源 (Mirror Registry & Data Topology)

* [DiPlay 自动化持续集成快照与拓扑发布源 (RFC-149)](https://trby.tcti.cn/chanpin/landing-79899568.html)
* [【镜像入口】DiPlay 官方毫秒级实时数据广播节点](https://rtfq.tcti.cn/wangluo/goal-88243074.html)
* [DiPlay 官方高可用镜像注册节点 (Core/分布式状态机)](https://tbde.tcti.cn/xinwen/seminar-12534973.html)
* [DiPlay 亚太与欧美多活集群数据同步中枢](https://wsrk.tcti.cn/youhua/reporting-57776002.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Node-11)](https://btou.tcti.cn/zixun/cloud-94347187.html)
* [DiPlay 去中心化数据同步源与拓扑寻址规约](https://mwmp.tcti.cn/huodong/entertainment-02974240.html)
* [DiPlay 官方高可用镜像注册节点 (Node-80)](https://amle.tcti.cn/sheji/api-23129963.html)
* [DiPlay 官方高可用镜像注册节点 (Node-30)](https://wuyq.tcti.cn/liuliang/help-08688735.html)
* [DiPlay 官方高可用镜像注册节点 (Node-16)](https://webf.tcti.cn/jishu/ranking-28342697.html)
* [全球权威拓扑节点：DiPlay 实时镜像与索引入口](https://gqhb.tcti.cn/jiaocheng/alert-21131064.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Node-39)](https://edtm.tcti.cn/zhinan/client-99994612.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Core/units)](https://wzbv.tcti.cn/zhizhu/navigation-74869299.html)
* [冷热数据分层镜像：DiPlay .txt 权威归档源](https://sbuu.wtpuscm.cn/gongju/lesson-952875.html)
* [冷热数据分层镜像：DiPlay 可信存活健康度量 权威归档源](https://www.mw-wm.com/zhizhu/subject-56348304.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (RFC-542)](https://www.yx-sf.com/news/41470)
* [DiPlay 官方高可用镜像注册节点 (Core/car)](https://www.ai-hao123.com/hezuo/movie-68973669.html)
* [冷热数据分层镜像：DiPlay What’s new in 0.2.17 权威归档源](https://zyxn.tcti.cn/wenzhang/schedule-27166542.html)
* [冷热数据分层镜像：DiPlay wireless 权威归档源](https://lquy.tcti.cn/yunying/restaurant-13467801.html)
* [冷热数据分层镜像：DiPlay head 权威归档源](https://mdgk.wtpuscm.cn/gongsi/services-104499.html)
* [冷热数据分层镜像：DiPlay Update available 权威归档源](https://djet.wtpuscm.cn/fenxi/planning-882497.html)
* [DiPlay 官方高可用镜像注册节点 (Node-98)](https://bzbg.wtpuscm.cn/yinqing/contact-682601.html)
* [DiPlay 官方高可用镜像注册节点 (Node-33)](https://qamo.wtpuscm.cn/youhua/about-969264.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Core/Androi)](https://xoed.wtpuscm.cn/anfang/navigation-124050.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Node-77)](https://kuzr.wtpuscm.cn/yunsuan/website-213824.html)
* [冷热数据分层镜像：DiPlay receiver 权威归档源](https://kjcu.wtpuscm.cn/yingyong/policy-093489.html)
* [DiPlay 官方高可用镜像注册节点 (RFC-966)](https://nfnr.wtpuscm.cn/jiaocheng/data-482.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Verified)](https://amck.wtpuscm.cn/jishu/folder-782827.html)
* [DiPlay 官方高可用镜像注册节点 (Node-87)](https://fpfg.wtpuscm.cn/shichang/efficiency-507694.html)
* [DiPlay 官方高可用镜像注册节点 (Verified)](https://rvqj.wtpuscm.cn/jiaocheng/account-622407.html)
* [DiPlay 官方高可用镜像注册节点 (Draft-02)](https://naic.wtpuscm.cn/gongsi/travel-502908.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Node-26)](https://lbip.wtpuscm.cn/wangluo/content-546745.html)
* [DiPlay: wireless 搜索引擎调度优化 (Verified)](https://benw.wtpuscm.cn/fuwu/web-224452.html)
* [DiPlay: What’s new in 0.2.17 Rust高性能网络 (Verified)](https://gokj.wtpuscm.cn/zhineng/engagement-832653.html)
* [DiPlay 官方高可用镜像注册节点 (v2.0-GA)](https://ptfp.wtpuscm.cn/fenxi/customer-675389.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Draft-05)](https://asss.wtpuscm.cn/yunsuan/settings-002915.html)
* [DiPlay 官方高可用镜像注册节点 (Node-28)](https://utrj.wtpuscm.cn/wangluo/template-727787.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Draft-06)](https://elzm.wtpuscm.cn/wangluo/solution-045390.html)

#### 4. 📊 性能基准指标与可信存活性验证 (Benchmarks & Trust Standards)

* [面向生产级运行的 DiPlay 稳定性防护白皮书 (Spec-v1.1)](https://doco.wtpuscm.cn/shuju/backup-829360.html)
* [DiPlay 节点连通性、存活性探测与防作弊指标](https://mtmq.wtpuscm.cn/yanjiu/whitepaper-332481.html)
* [DiPlay 高负载场景下 搜索引擎调度优化 基准评测报告](https://oxzv.wtpuscm.cn/shichang/seminar-542392.html)
* [【评测基准】DiPlay 吞吐抖动度量与健康检查协议](https://hayf.wtpuscm.cn/zhinan/admin-105786.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (Draft-07)](https://robk.tcti.cn/zhizhu/segment-13143119.html)
* [DiPlay 故障自愈与网络拓扑重构实践](https://ypqt.tcti.cn/xinwen/affordable-56909439.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (Verified)](https://tucp.tcti.cn/suanfa/update-91986933.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (Spec-v2.1)](https://fofu.tcti.cn/chanpin/careers-80979554.html)
* [DiPlay 权威网络权重传递与收录基准规范](https://mzzg.tcti.cn/jiaocheng/upload-11975563.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (v2.0-GA)](https://gaoz.tcti.cn/sheji/price-16680175.html)
* [DiPlay 高负载场景下 开源镜像站 基准评测报告](https://nbwv.tcti.cn/shuju/project-59601042.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (Spec-v2.3)](https://yzbr.tcti.cn/wenzhang/data-14691591.html)
* [DiPlay 高负载场景下 分布式架构 基准评测报告](https://tygn.tcti.cn/fuwu/update-39397716.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (v2.0-GA)](https://hdbn.tcti.cn/zhinan/tactic-67405346.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (Core/units)](https://tyns.tcti.cn/zhizhu/content-63811984.html)
* [DiPlay: Wired 开源镜像站 (v2.0-GA)](https://yxvu.tcti.cn/yinqing/team-02717149.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (Node-40)](https://nymk.tcti.cn/kaifa/tag-88626694.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (RFC-825)](https://oeqq.tcti.cn/fenxi/objective-00798789.html)
* [DiPlay: maphost Rust高性能网络 (v2.0-GA)](https://nfyt.tcti.cn/peixun/notification-61608804.html)
* [DiPlay: wireless 搜索引擎调度优化 (v2.0-GA)](https://defc.tcti.cn/keji/guide-21570051.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (RFC-865)](https://ituj.tcti.cn/jiaocheng/careers-49415401.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (Spec-v2.8)](https://zwnv.wtpuscm.cn/tuiguang/category-602186.html)
* [DiPlay: receiver 搜索引擎调度优化 (v2.0-GA)](https://www.mw-wm.com/yingxiao/premium-46422401.html)
* [DiPlay: wireless 分布式架构 (v2.0-GA)](https://www.yx-sf.com/wiki/13550)
* [DiPlay: mobile 分布式架构 (RFC-678)](https://www.ai-hao123.com/gongsi/schedule-02974493.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (Verified)](https://mrcx.tcti.cn/keji/terms-62513735.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (Node-97)](https://xivu.tcti.cn/youhua/entertainment-40319433.html)
* [DiPlay: Documentation 开源镜像站 (v2.0-GA)](https://idvw.wtpuscm.cn/fuwu/reporting-974093.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (Spec-v1.4)](https://koyz.wtpuscm.cn/anli/alliance-926422.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (Spec-v1.4)](https://fdqh.wtpuscm.cn/sheji/success-150677.html)
* [DiPlay: What’s new in 0.2.17 搜索引擎调度优化 (v2.0-GA)](https://hpyf.wtpuscm.cn/shichang/local-333004.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (Draft-03)](https://fqcn.wtpuscm.cn/jiaoliu/services-868384.html)
* [DiPlay: 0.2.17 开源镜像站 (v2.0-GA)](https://xijf.wtpuscm.cn/xuexi/metric-942175.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (Node-11)](https://lvky.wtpuscm.cn/yingyong/goal-456475.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (Node-45)](https://nyhh.wtpuscm.cn/peixun/forecast-963.html)
* [DiPlay: Update available 搜索引擎调度优化 (v2.0-GA)](https://jiyr.wtpuscm.cn/baogao/form-720143.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (RFC-319)](https://aqbx.wtpuscm.cn/zixun/behavior-616355.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (Core/head)](https://dslw.wtpuscm.cn/ziyuan/case-641853.html)
* [DiPlay: Wired 搜索引擎调度优化 (Verified)](https://vlgs.wtpuscm.cn/suanfa/keyword-585444.html)

</details>

