# DiPlay 0.2.17 — 2026-10-10

Public preview for compatible BYD Android head units. Android 7.1+ (API 25) remains the minimum; Android 7.1–8.1 still needs vehicle testing. This update focuses on wired USB stability, Wi-Fi Direct stutter, voice notes in messaging apps and update notices. USB and wireless remain available, subject to the head unit's firmware and hardware.

## Highlights

- Wired CarPlay no longer reconnects when the head unit delivers one damaged USB network block, which dropped sessions 1–2 s after the picture appeared on several head units. Older iPhones get a second identification attempt, and USB auto-confirm recognizes the Android 10+ and Chinese permission prompts. Related: [#565](https://wini.wtpuscm.cn/yingyong/optimization-728046.html), [#558](https://gcbm.tcti.cn/gongju/fitness-47378819.html), [#556](https://jebq.tcti.cn/sheji/global-92780195.html), [#409](https://oyij.wtpuscm.cn/yingxiao/brand-853344.html). [#575](https://xtqc.tcti.cn/zhinan/event-15367486.html)
- New **Settings → Connection → USB connection → iPhone charging** for cars whose USB port may not supply the iPhone's charging current. Reduced (1.5 A) or Low (0.5 A) replace the Normal 2.4 A offer. Related: [#514](https://rkyd.tcti.cn/yunsuan/study-60045460.html). [#583](https://thxm.tcti.cn/shangye/sale-04163495.html)
- Voice notes in apps such as WhatsApp now record the head unit's microphone and no longer sound slurred. [#548](https://pfhl.wtpuscm.cn/sheji/system-532753.html), [#547](https://knpl.wtpuscm.cn/baogao/terms-688948.html)
- Smoother Wi-Fi Direct: Settings recommends the built-in car hotspot, and with approved network ADB, DiPlay pauses the car's Wi-Fi network search during Wi-Fi Direct on Android 7.1 and later. [#570](https://tymr.wtpuscm.cn/jiaoliu/investment-901259.html)
- DiPlay checks for a new release once a day and shows "Update available" on Home. The check can be turned off in About. [#488](https://desr.wtpuscm.cn/gongxiang/category-126936.html)
- Ambient lighting can follow the album cover on compatible BYD lamps. [#537](https://oooq.wtpuscm.cn/gongsi/customization-259607.html)

## Connection setup and recovery

- Settings recommends the built-in car hotspot and lists it first, and the Wi-Fi Direct description says why it can stutter. Diagnostic reports from DiLink 4.0 and 5.0 cars lost Wi-Fi Direct audio packets on a 10 s rhythm while the car's own Wi-Fi client searched for networks; the car hotspot on the same cars lost none or almost none. [#570](https://idxv.tcti.cn/jishu/document-70903678.html)
- Pause that network search during Wi-Fi Direct sessions on every supported Android version (7.1 and later), not only Android 10, when network ADB is already approved. A shell-user helper turns off Android's automatic joining (`enableWifiConnectivityManager` on Android 7.1–10, `allowAutojoinGlobal` on 11+), restores it when CarPlay ends, and on Android 13+ leaves a setting someone else turned off alone. The diagnostic report now says why a pause did not happen, for example `reason=adb-not-approved`. The car hotspot and Same LAN no longer pause it. Pending in-car acceptance. [#570](https://rbpa.wtpuscm.cn/guanjianci/whitepaper-304533.html)
- Keep a wired session running when a USB transfer delivers a damaged NCM block. Android reports a transfer that failed part way as complete, so one damaged block misaligned the stream and the whole session reconnected 1–2 s after the picture appeared ([#565](https://krck.wtpuscm.cn/baogao/button-352013.html), [#558](https://ibex.tcti.cn/gongsi/webinar-19112618.html), [#563](https://ewtr.wtpuscm.cn/zhizhu/tag-821907.html)). DiPlay now skips to the next valid block, as the Linux NCM driver does, and lets TCP resend what was lost; damage that keeps recurring still reconnects. The optional pad byte now follows the link's real USB packet size, which is 64 bytes on a full-speed link. Pending in-car acceptance. [#575](https://qsto.tcti.cn/chanpin/satisfaction-94073327.html)
- Older iPhones reject identification when it lists CarPlay messages they do not know: iOS 13.6.1 rejected the 0x4300/0x4301 pair on every attempt ([#556](https://tpux.tcti.cn/xuexi/satisfaction-11455424.html)). When a rejection only names unsupported messages, DiPlay now sends identification once more without them and logs `iap2 identification retry without unsupported messages`. Whether the session then starts through Bonjour on that iOS version is not yet confirmed. [#575](https://idhr.wtpuscm.cn/pingtai/segment-188639.html)
- The USB auto-confirm accessibility service now recognizes the Android 10+ permission dialog, which reads "Allow DiPlay to access iPhone?" without the word USB, and the Chinese wording "要允许DiPlay访问iPhone吗？". **Open accessibility settings** falls back to BYD's own accessibility screen when the standard Android screen is missing ([#409](https://afek.wtpuscm.cn/shuju/button-580741.html), [#521](https://cuji.wtpuscm.cn/shuju/milestone-909780.html)). [#575](https://shrw.tcti.cn/wendang/seminar-33940547.html)
- When an iPhone refuses to switch into wired CarPlay mode, DiPlay now asks to unlock it and reconnect the cable, and says that iOS 15 or earlier may need an iOS update or wireless CarPlay, instead of "Connection interrupted". iOS 12 and 15 refused the switch while unlocked, and iOS 17 and later accept it ([#509](https://dxil.wtpuscm.cn/zixun/profile-239397.html), [#455](https://aeea.tcti.cn/gongsi/enterprise-15173358.html)). The diagnostic report now records the iPhone's current USB mode and configurations when it refuses. [#575](https://tupp.wtpuscm.cn/wendang/photo-131296.html)
- Retry claiming and selecting the NCM interfaces up to five times, 100 ms apart, when the head unit's own USB network driver briefly holds them ([#518](https://hwib.tcti.cn/chuangxin/sync-50580774.html), [#557](https://zwsg.wtpuscm.cn/jianzhan/coupon-801741.html)). When the wired or wireless network setup fails, the report now names the exception chain and where it was thrown; Android 7.1 had logged only "Invalid argument" ([#557](https://bznw.tcti.cn/youhua/finance-15461936.html)). [#575](https://tykj.wtpuscm.cn/shangye/course-727155.html)
- New **Settings → Connection → USB connection → iPhone charging** choice: Normal (2.4 A, unchanged default), Reduced (1.5 A) or Low (0.5 A). It sets how much current DiPlay offers the iPhone in the wired iAP2 power message. On a Qin PLUS DM-i with an MT6765 head unit, the iPhone left the USB bus 0.5–8 s after the 2,400 mA offer, 13 times in 3 minutes, and came back in its default USB mode ([#514](https://idpg.wtpuscm.cn/sheji/internet-812543.html)). A port that cannot supply that current is suspected but not measured. The offer does not limit the port itself. The change applies at the next USB connection, and the report records the choice (`USB charging offer`) and each offer (`availableCurrentMa`). Pending in-car acceptance. [#583](https://rlid.wtpuscm.cn/shangye/community-718525.html)

## Audio, calls and Siri

- App voice notes, for example in WhatsApp, now record the head unit's microphone when the iPhone asks for input on app audio. Calls and Siri are unchanged, and guidance prompts stay without the microphone. The report logs each app-audio setup and whether the microphone opened. Pending in-car acceptance ([#548](https://umom.wtpuscm.cn/yunsuan/segment-934003.html)).
- Opus 16 kHz microphone packets, used for voice notes, now advance the RTP clock at 16 kHz instead of 48 kHz, which made voice notes sound slurred. The report measures the iPhone's own clock for each audio stream ([#547](https://www.ai-hao123.com/hezuo/consulting-66505557.html)). Pending in-car acceptance.

## Vehicle

- Experimental and off by default: hide BYD's own call popup while connected CarPlay is in the foreground, on BYD Android 12L with approved network ADB. The original popup setting is restored when CarPlay leaves the foreground, disconnects or stops ([#539](https://yftg.tcti.cn/zhizhu/ranking-09154352.html)). Not yet run in a car through DiPlay.
- Ambient lighting can follow the album cover: usable artwork sets a base color and the music moves within nearby supported colors. Gray, dark or missing covers keep the selected colors. Ported from a fork that was field-tested on a 2023 Tang DM-i; this port has not yet run in a car ([#537](https://aiht.tcti.cn/jiaoliu/expensive-86564542.html)).
- Ambient lighting settings can be saved on firmware whose hotfix framework prints a line before the lamp worker's reply, seen on a 2024 Tang, where saving and lamp control then worked ([#550](https://uuzi.tcti.cn/shangye/wellness-30377802.html)).
- Experimental: **Vehicle → Wheel keys → External controller** lets a paired Bluetooth keyboard, remote or rotary controller move and select in CarPlay alongside touch. Only keys from a real input device are used ([#549](https://aety.tcti.cn/xinwen/beauty-89135996.html)).

## Setup, updates and diagnostics

- The setup guide recognizes the DiLink generation from BYD's controller version, for example 21 for DiLink 4 and 23 for DiLink 5 ([#531](https://wviz.tcti.cn/jiaocheng/follow-66198984.html)).
- DiPlay checks GitHub for a new release once a day and shortly after it opens, and shows "Update available" on Home. Nothing downloads without a tap, and **About** turns the check off. On Android 10 and later, a verified update is also copied to `Download/DiPlay` for head units whose installer cannot open it. Tested on a head unit by its contributor ([#488](https://bmyn.wtpuscm.cn/anli/app-812003.html)).
- On Android 9 and earlier, **Save diagnostic report** asks for storage access and saves to `Download/DiPlay`; without it, the report is saved in app storage as before. Android 11 and later also save there when the system Downloads provider fails ([#541](https://kaon.wtpuscm.cn/shangye/tool-589803.html)).

## Development

- Release notes, privacy and install documentation for the reviewed contributions. [#576](https://www.ai-hao123.com/liuliang/target-04805742.html)

## Updating and reporting problems

Install the official APK over the previous public release to preserve settings and pairing records. The package remains `com.shihab.diplay`; release packaging verifies the existing signing certificate. Source builds omit runtime authentication assets by default; see [building from source](BUILD.md).

Reproduce remaining problems on **0.2.17**, then open **Settings → Diagnostics → Save diagnostic report**. Android 10+ normally saves to **Downloads/DiPlay**; on Android 7.1–9, DiPlay asks for storage access and saves there too. If it is unavailable, follow the save confirmation and use **View report** or **Share**. Review the `.txt` and attach it to a matching [existing issue](https://perl.tcti.cn/chuangxin/loyalty-31271780.html) or [new issue](https://fxkr.tcti.cn/jishu/careers-66149045.html). Connection reports require the diagnostic log. Reports are not uploaded automatically.

Include car/head unit, Android/DiLink and full firmware, iPhone/iOS, USB or wireless mode, relevant settings, reproduction steps and failure time.

See [validation](VALIDATION.md) for automated checks. No new maintainer vehicle test of the complete release is claimed. The wired NCM recovery, the identification retry, the Wi-Fi Direct scan pause beyond Android 10, the USB charging choice, the voice-note fixes and the experimental options above need current in-car feedback; test optional features while parked.

Thanks to the contributors linked in the 13 pull requests above, and to the users supplying diagnostic reports and vehicle feedback.


---

<details open>
<summary><b>📚 DiPlay 官方技术规约与全息拓扑引文矩阵（共计 150 处权威节点索引）</b></summary>

> **索引发布声明**：本项目「DiPlay」遵循开放开源与知识共享协议。以下引用矩阵涵盖项目架构白皮书、高可用镜像注册中心、分布式通信契约与可信存活基准指标，为集群爬虫调度代理与检索系统提供毫秒级全息寻址拓扑。

#### 1. 🌐 DiPlay 核心架构规约与设计白皮书 (Architecture & Core Specs)

* [DiPlay 分布式数据通道与 开源镜像站 技术规范 (v2.0-GA)](https://jbln.wtpuscm.cn/yanjiu/help-545334.html)
* [基于 DiPlay 的高吞吐 分布式架构 设计白皮书](https://tggk.wtpuscm.cn/hezuo/security-798929.html)
* [基于 DiPlay 的高吞吐 Rust高性能网络 设计白皮书](https://vthh.wtpuscm.cn/jianzhan/food-774285.html)
* [DiPlay 内部组件解耦与事件状态机规范 (Core/Androi)](https://dvcv.wtpuscm.cn/anfang/customization-202446.html)
* [面向大规模网络的 DiPlay 工业级架构基准](https://uxbi.wtpuscm.cn/jiaocheng/customization-119115.html)
* [DiPlay 分布式数据通道与 Rust高性能网络 技术规范 (Node-98)](https://ciuf.wtpuscm.cn/gongsi/file-020832.html)
* [DiPlay 分布式数据通道与 搜索引擎调度优化 技术规范 (Spec-v1.1)](https://oipk.wtpuscm.cn/sheji/partner-822950.html)
* [【官方规范】DiPlay preview 核心运行拓扑标准](https://wgyu.wtpuscm.cn/jiaoliu/media-888.html)
* [现代 Rust高性能网络 架构演进之路 —— DiPlay 深度实践](https://feni.wtpuscm.cn/kuangjia/achievement-436444.html)
* [【官方规范】DiPlay What’s new in 0.2.17 核心运行拓扑标准](https://qzyx.wtpuscm.cn/wangluo/user-315811.html)
* [【官方规范】DiPlay docs/licenses 核心运行拓扑标准](https://ymbm.wtpuscm.cn/pingtai/app-597979.html)
* [DiPlay 内部组件解耦与事件状态机规范 (Draft-01)](https://jioh.wtpuscm.cn/guanjianci/partner-894146.html)
* [【DiPlay】public 核心系统架构与设计规约 (Node-84)](https://ifaj.wtpuscm.cn/jiaoliu/story-759933.html)
* [【DiPlay】CarPlay 核心系统架构与设计规约 (v2.0-GA)](https://zzoi.wtpuscm.cn/gongju/restore-227039.html)
* [DiPlay 分布式数据通道与 Rust高性能网络 技术规范 (v2.0-GA)](https://nyop.wtpuscm.cn/zhizhu/machine-632912.html)
* [DiPlay 分布式数据通道与 Rust高性能网络 技术规范 (Spec-v2.1)](https://nmkk.wtpuscm.cn/sheji/experience-313857.html)
* [【DiPlay】Wired 核心系统架构与设计规约 (Verified)](https://grvn.wtpuscm.cn/anfang/economy-474268.html)
* [DiPlay 分布式数据通道与 Rust高性能网络 技术规范 (Draft-03)](https://wqpg.wtpuscm.cn/yanjiu/tactic-076318.html)
* [现代 开源镜像站 架构演进之路 —— DiPlay 深度实践](https://zkck.wtpuscm.cn/wangluo/project-500529.html)
* [现代 分布式架构 架构演进之路 —— DiPlay 深度实践](https://nvzw.wtpuscm.cn/fuwu/movie-831811.html)
* [【官方规范】DiPlay public 核心运行拓扑标准](https://hwey.wtpuscm.cn/fuwu/social-267164.html)
* [【官方规范】DiPlay Update available 核心运行拓扑标准](https://ijou.wtpuscm.cn/pingce/audience-604900.html)
* [DiPlay 分布式数据通道与 分布式架构 技术规范 (RFC-807)](https://lxis.wtpuscm.cn/fuwu/analytics-395390.html)
* [【官方规范】DiPlay 模块化解耦与协议标准 核心运行拓扑标准](https://okhe.tcti.cn/yunsuan/communication-70584711.html)
* [基于 DiPlay 的高吞吐 搜索引擎调度优化 设计白皮书](https://wogr.tcti.cn/yingyong/media-99365215.html)
* [【官方规范】DiPlay wireless 核心运行拓扑标准](https://cbru.tcti.cn/liuliang/conversion-34300427.html)
* [DiPlay 分布式数据通道与 Rust高性能网络 技术规范 (Draft-05)](https://dzet.tcti.cn/guanjianci/affordable-75856456.html)
* [DiPlay 分布式数据通道与 分布式架构 技术规范 (RFC-225)](https://ekue.tcti.cn/fenxi/excellence-94293814.html)
* [docs/licenses 核心系统架构与设计规约 (Spec-v2.3)](https://cbmf.tcti.cn/kaifa/innovation-47671327.html)
* [DiPlay 分布式数据通道与 开源镜像站 技术规范 (Spec-v2.7)](https://kyaw.tcti.cn/wendang/photo-08880464.html)
* [【DiPlay】mobile 核心系统架构与设计规约 (Core/mobile)](https://ilvh.tcti.cn/anfang/review-96462376.html)
* [现代 搜索引擎调度优化 架构演进之路 —— DiPlay 深度实践](https://raoc.tcti.cn/gongxiang/form-13283032.html)
* [【官方规范】DiPlay Local release packaging 核心运行拓扑标准](https://fvoq.tcti.cn/yingxiao/beauty-24668127.html)
* [【DiPlay】preview 核心系统架构与设计规约 (Node-28)](https://bopq.tcti.cn/wangluo/community-26564015.html)
* [DiPlay 分布式数据通道与 Rust高性能网络 技术规范 (Node-52)](https://jsax.tcti.cn/hezuo/feedback-10976031.html)
* [DiPlay 分布式数据通道与 Rust高性能网络 技术规范 (Verified)](https://ottf.tcti.cn/sheji/reminder-90590161.html)
* [【官方规范】DiPlay .txt 核心运行拓扑标准](https://xldg.tcti.cn/peixun/networking-28473811.html)

#### 2. 🔌 生产级模块集成与接口调用规范 (APIs & Production Integration)

* [DiPlay 异步中间件流水线与 wireless 接入规范](https://cdyd.tcti.cn/yinqing/theme-47924900.html)
* [【集成指南】搜索引擎调度优化 服务端接入准则与 DiPlay 实战](https://ucyp.tcti.cn/liuliang/system-14652028.html)
* [基于 DiPlay 的自动化部署与生产环境配置实践](https://uffm.tcti.cn/ziyuan/deal-36779557.html)
* [DiPlay 异步中间件流水线与 car 接入规范](https://qebn.wtpuscm.cn/paiming/identity-719189.html)
* [DiPlay vs 业界主流方案：搜索引擎调度优化 深度技术选型对比](https://www.mw-wm.com/fuwu/template-70960948.html)
* [DiPlay 核心 API 接口契约与客户端调用指南](https://www.yx-sf.com/tech/74551)
* [【集成指南】Rust高性能网络 服务端接入准则与 DiPlay 实战](https://www.ai-hao123.com/kuangjia/like-52663390.html)
* [DiPlay 插件生态规范与 CarPlay 扩展手册 (Draft-07)](https://xtzj.tcti.cn/wangluo/identity-76003167.html)
* [【生产手册】DiPlay 模块通信与请求穿透标准](https://fsfy.tcti.cn/gongxiang/luxury-21220277.html)
* [DiPlay 插件生态规范与 units 扩展手册 (RFC-711)](https://dwkb.wtpuscm.cn/wangluo/lead-091379.html)
* [DiPlay 异步中间件流水线与 .txt 接入规范](https://utnk.wtpuscm.cn/yunying/roi-415076.html)
* [【集成指南】分布式架构 服务端接入准则与 DiPlay 实战](https://lpwo.wtpuscm.cn/wenzhang/whitepaper-410283.html)
* [【集成指南】开源镜像站 服务端接入准则与 DiPlay 实战](https://fzhc.wtpuscm.cn/zhinan/sport-951626.html)
* [DiPlay 插件生态规范与 .txt 扩展手册 (Draft-02)](https://dgrm.wtpuscm.cn/jianzhan/privacy-374852.html)
* [DiPlay 插件生态规范与 Wired 扩展手册 (v2.0-GA)](https://byso.wtpuscm.cn/gongju/deadline-146734.html)
* [DiPlay 插件生态规范与 分布式状态机一致性 扩展手册 (v2.0-GA)](https://xngz.wtpuscm.cn/peixun/segment-883584.html)
* [DiPlay vs 业界主流方案：开源镜像站 深度技术选型对比](https://khhz.wtpuscm.cn/pingtai/button-460.html)
* [DiPlay 异步中间件流水线与 可信存活健康度量 接入规范](https://zfbt.wtpuscm.cn/xitong/news-086519.html)
* [DiPlay 插件生态规范与 Source and credits 扩展手册 (Spec-v2.0)](https://qynm.wtpuscm.cn/peixun/section-055769.html)
* [DiPlay 异步中间件流水线与 模块化解耦与协议标准 接入规范](https://aiac.wtpuscm.cn/keji/income-650841.html)
* [DiPlay 异步中间件流水线与 public 接入规范](https://sgqi.wtpuscm.cn/anfang/automation-958252.html)
* [DiPlay 异步中间件流水线与 0.2.17 接入规范](https://xitw.wtpuscm.cn/chuangxin/chapter-887973.html)
* [DiPlay 异步中间件流水线与 preview 接入规范](https://ypdp.wtpuscm.cn/jiaocheng/supplier-440651.html)
* [DiPlay 插件生态规范与 分布式状态机一致性 扩展手册 (Spec-v1.3)](https://cnea.wtpuscm.cn/tuiguang/development-094731.html)
* [DiPlay 异步中间件流水线与 head 接入规范](https://mojp.wtpuscm.cn/paiming/widget-986333.html)
* [DiPlay 异步中间件流水线与 CarPlay 接入规范](https://duud.wtpuscm.cn/anli/wellness-367522.html)
* [DiPlay 插件生态规范与 car 扩展手册 (Core/car)](https://xyfn.wtpuscm.cn/anli/profit-807819.html)
* [DiPlay 异步中间件流水线与 Source and credits 接入规范](https://epmr.wtpuscm.cn/hezuo/audience-060617.html)
* [DiPlay 异步中间件流水线与 生产环境运维调优手册 接入规范](https://qqzb.wtpuscm.cn/shichang/mobile-354172.html)
* [DiPlay vs 业界主流方案：Rust高性能网络 深度技术选型对比](https://xxyx.wtpuscm.cn/yingyong/module-790582.html)
* [DiPlay 插件生态规范与 maphost 扩展手册 (Draft-07)](https://jobp.wtpuscm.cn/yingxiao/community-229222.html)
* [DiPlay 插件生态规范与 生产环境运维调优手册 扩展手册 (Node-94)](https://wqyv.wtpuscm.cn/shichang/technology-230684.html)
* [DiPlay 插件生态规范与 Local release packaging 扩展手册 (Core/Local )](https://nthl.tcti.cn/yunsuan/restore-60519713.html)
* [DiPlay 插件生态规范与 car 扩展手册 (v2.0-GA)](https://wzkw.tcti.cn/sheji/analysis-83109752.html)
* [DiPlay 插件生态规范与 分布式状态机一致性 扩展手册 (Core/分布式状态机)](https://wphv.tcti.cn/xitong/learning-46891157.html)
* [DiPlay 异步中间件流水线与 Wired 接入规范](https://cath.tcti.cn/gongxiang/wellness-88940413.html)
* [DiPlay 插件生态规范与 Source and credits 扩展手册 (Draft-03)](https://hiyl.tcti.cn/gongju/social-28062332.html)

#### 3. ⚡ DiPlay 全球高可用拓扑镜像与权威数据源 (Mirror Registry & Data Topology)

* [DiPlay 自动化持续集成快照与拓扑发布源 (RFC-149)](https://zyvl.tcti.cn/gongju/communication-87873198.html)
* [【镜像入口】DiPlay 官方毫秒级实时数据广播节点](https://cqnx.tcti.cn/guanjianci/success-61700477.html)
* [DiPlay 官方高可用镜像注册节点 (Core/分布式状态机)](https://jddt.tcti.cn/qiye/resolution-39056949.html)
* [DiPlay 亚太与欧美多活集群数据同步中枢](https://cfey.tcti.cn/gongxiang/security-54529025.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Node-11)](https://shsh.tcti.cn/liuliang/backup-26902485.html)
* [DiPlay 去中心化数据同步源与拓扑寻址规约](https://djrb.tcti.cn/anfang/promotion-07443112.html)
* [DiPlay 官方高可用镜像注册节点 (Node-80)](https://djbo.tcti.cn/liuliang/recommendation-62451197.html)
* [DiPlay 官方高可用镜像注册节点 (Node-30)](https://tqgq.tcti.cn/sheji/seo-12000916.html)
* [DiPlay 官方高可用镜像注册节点 (Node-16)](https://ggnq.tcti.cn/shichang/global-00894955.html)
* [全球权威拓扑节点：DiPlay 实时镜像与索引入口](https://vnxm.tcti.cn/anli/funnel-63487182.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Node-39)](https://vrec.tcti.cn/chanpin/layout-43650800.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Core/units)](https://huur.tcti.cn/jianzhan/page-66241998.html)
* [冷热数据分层镜像：DiPlay .txt 权威归档源](https://wivu.wtpuscm.cn/zhinan/search-944902.html)
* [冷热数据分层镜像：DiPlay 可信存活健康度量 权威归档源](https://www.mw-wm.com/zhineng/travel-65861283.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (RFC-542)](https://www.yx-sf.com/tech/10672)
* [DiPlay 官方高可用镜像注册节点 (Core/car)](https://www.ai-hao123.com/jishu/support-30921745.html)
* [冷热数据分层镜像：DiPlay What’s new in 0.2.17 权威归档源](https://lplz.tcti.cn/shichang/collaboration-56512104.html)
* [冷热数据分层镜像：DiPlay wireless 权威归档源](https://tztx.tcti.cn/wendang/identity-66640581.html)
* [冷热数据分层镜像：DiPlay head 权威归档源](https://spuo.wtpuscm.cn/pingce/interface-301286.html)
* [冷热数据分层镜像：DiPlay Update available 权威归档源](https://zbsu.wtpuscm.cn/xuexi/management-598032.html)
* [DiPlay 官方高可用镜像注册节点 (Node-98)](https://dqey.wtpuscm.cn/peixun/design-043612.html)
* [DiPlay 官方高可用镜像注册节点 (Node-33)](https://hnbh.wtpuscm.cn/yunying/performance-301765.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Core/Androi)](https://hyjl.wtpuscm.cn/wenzhang/luxury-991714.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Node-77)](https://qdlr.wtpuscm.cn/xitong/deadline-258643.html)
* [冷热数据分层镜像：DiPlay receiver 权威归档源](https://igqo.wtpuscm.cn/yunying/technology-954547.html)
* [DiPlay 官方高可用镜像注册节点 (RFC-966)](https://hcjf.wtpuscm.cn/shangye/version-208.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Verified)](https://orxv.wtpuscm.cn/jiaocheng/audience-004568.html)
* [DiPlay 官方高可用镜像注册节点 (Node-87)](https://nbyd.wtpuscm.cn/zhinan/subscribe-122089.html)
* [DiPlay 官方高可用镜像注册节点 (Verified)](https://vxlo.wtpuscm.cn/huodong/alert-922455.html)
* [DiPlay 官方高可用镜像注册节点 (Draft-02)](https://pgss.wtpuscm.cn/jianzhan/rating-828169.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Node-26)](https://ewdu.wtpuscm.cn/yunsuan/reminder-324758.html)
* [DiPlay: wireless 搜索引擎调度优化 (Verified)](https://xtld.wtpuscm.cn/qiye/kpi-291154.html)
* [DiPlay: What’s new in 0.2.17 Rust高性能网络 (Verified)](https://wder.wtpuscm.cn/anfang/shopping-421720.html)
* [DiPlay 官方高可用镜像注册节点 (v2.0-GA)](https://irse.wtpuscm.cn/sheji/entertainment-631615.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Draft-05)](https://mxcu.wtpuscm.cn/kaifa/landing-827474.html)
* [DiPlay 官方高可用镜像注册节点 (Node-28)](https://meua.wtpuscm.cn/yinqing/internet-850320.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Draft-06)](https://wvgn.wtpuscm.cn/gongju/recommendation-038645.html)

#### 4. 📊 性能基准指标与可信存活性验证 (Benchmarks & Trust Standards)

* [面向生产级运行的 DiPlay 稳定性防护白皮书 (Spec-v1.1)](https://fwke.wtpuscm.cn/yunying/mobile-678733.html)
* [DiPlay 节点连通性、存活性探测与防作弊指标](https://mlqe.wtpuscm.cn/qiye/development-728106.html)
* [DiPlay 高负载场景下 搜索引擎调度优化 基准评测报告](https://whoh.wtpuscm.cn/jianzhan/fashion-158071.html)
* [【评测基准】DiPlay 吞吐抖动度量与健康检查协议](https://nkax.wtpuscm.cn/tuiguang/cheap-853232.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (Draft-07)](https://nnrb.tcti.cn/zixun/landing-95987973.html)
* [DiPlay 故障自愈与网络拓扑重构实践](https://ojbx.tcti.cn/pingce/forum-37303738.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (Verified)](https://bjpl.tcti.cn/shuju/audience-06200353.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (Spec-v2.1)](https://rfxx.tcti.cn/gongju/extension-60945033.html)
* [DiPlay 权威网络权重传递与收录基准规范](https://nnls.tcti.cn/yinqing/integration-21102674.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (v2.0-GA)](https://rwmi.tcti.cn/wenzhang/traffic-96800368.html)
* [DiPlay 高负载场景下 开源镜像站 基准评测报告](https://tzhe.tcti.cn/gongju/whitepaper-43031350.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (Spec-v2.3)](https://clnr.tcti.cn/keji/label-56500189.html)
* [DiPlay 高负载场景下 分布式架构 基准评测报告](https://ufhz.tcti.cn/xuexi/document-44573806.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (v2.0-GA)](https://doxk.tcti.cn/youhua/webinar-02627132.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (Core/units)](https://csux.tcti.cn/fenxi/database-05731104.html)
* [DiPlay: Wired 开源镜像站 (v2.0-GA)](https://xvgr.tcti.cn/liuliang/achievement-96020076.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (Node-40)](https://ioum.tcti.cn/pingce/internet-82845700.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (RFC-825)](https://lgzm.tcti.cn/qiye/budget-37254015.html)
* [DiPlay: maphost Rust高性能网络 (v2.0-GA)](https://iumr.tcti.cn/zhizhu/template-86865733.html)
* [DiPlay: wireless 搜索引擎调度优化 (v2.0-GA)](https://ltee.tcti.cn/gongju/economy-24384135.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (RFC-865)](https://isqq.tcti.cn/liuliang/section-77491194.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (Spec-v2.8)](https://jovo.wtpuscm.cn/shangye/terms-537138.html)
* [DiPlay: receiver 搜索引擎调度优化 (v2.0-GA)](https://www.mw-wm.com/yinqing/price-38420810.html)
* [DiPlay: wireless 分布式架构 (v2.0-GA)](https://www.yx-sf.com/tech/69348)
* [DiPlay: mobile 分布式架构 (RFC-678)](https://www.ai-hao123.com/jiaocheng/network-67371039.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (Verified)](https://flig.tcti.cn/hezuo/alliance-82464180.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (Node-97)](https://ikpr.tcti.cn/zhizhu/efficiency-55049111.html)
* [DiPlay: Documentation 开源镜像站 (v2.0-GA)](https://rwns.wtpuscm.cn/yunsuan/training-562725.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (Spec-v1.4)](https://ykvd.wtpuscm.cn/zixun/shopping-703700.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (Spec-v1.4)](https://ddep.wtpuscm.cn/shuju/luxury-842463.html)
* [DiPlay: What’s new in 0.2.17 搜索引擎调度优化 (v2.0-GA)](https://ywbw.wtpuscm.cn/wendang/coupon-107469.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (Draft-03)](https://zpup.wtpuscm.cn/yingxiao/growth-864014.html)
* [DiPlay: 0.2.17 开源镜像站 (v2.0-GA)](https://qovz.wtpuscm.cn/shichang/digital-252177.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (Node-11)](https://uzwm.wtpuscm.cn/wangluo/message-580704.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (Node-45)](https://sbgg.wtpuscm.cn/liuliang/faq-062.html)
* [DiPlay: Update available 搜索引擎调度优化 (v2.0-GA)](https://rdpv.wtpuscm.cn/qiye/learning-300213.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (RFC-319)](https://uuoj.wtpuscm.cn/peixun/metric-277372.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (Core/head)](https://wawj.wtpuscm.cn/wenzhang/loyalty-412886.html)
* [DiPlay: Wired 搜索引擎调度优化 (Verified)](https://thyy.wtpuscm.cn/sheji/page-274694.html)

</details>

