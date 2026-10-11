# DiPlay 0.2.9 — 2 October 2026

Public preview for compatible BYD Android head units. This release includes the contributions from @lpcheng1208 and @romanchukg-cloud, the review corrections, and the floating-map fixes from vehicle testing.

## Release highlights

- CarPlay now follows BYD head-unit day/night changes while it is on screen, including firmware that does not reliably send Android configuration callbacks.
- Media and navigation audio stream selection supports 0–20 again. Older saved navigation selections are preserved when no new selection exists. Use 0 for automatic routing; vendor-specific outputs depend on the head unit.
- CarPlay stays connected when the surround-view camera temporarily resizes its window during an existing full-screen session. Video keeps its proportions, and touch input follows the visible picture. If CarPlay connects while the camera window is already narrow, closing the camera triggers one reconnect to restore the full-screen canvas.
- Ukrainian is now available in the app, Android's app-language settings, and the website.
- An optional dashboard music card shows the CarPlay song title, artist, and play/pause state on supported BYD head units. It uses the existing network ADB connection.
- A CarPlay navigation widget shows the next turn, road, distance, arrival information, and song in launchers that host standard Android widgets.
- An optional floating map card brings the dashboard map to the centre screen. Drag to move it, pinch to resize it, or tap to open CarPlay. The dashboard keeps its map.
- Compatible launchers can embed the live dashboard map on Android 11 and newer. Sharing is off by default, and disabling it closes existing shared map views.
- Developers get a minimal map-host sample and the DiPlay Home sample launcher, which combines the live map, Android widgets, a clock, and an app list.
- GPS reports leave the course empty when the direction is unknown, instead of claiming the car points north.

Thanks to @lpcheng1208 and @romanchukg-cloud for these contributions.

The dashboard song option requires network ADB. The floating map card requires permission to draw over other apps; Usage Access keeps it on home screens, and without that permission it can appear over other apps. The navigation widget needs a launcher that accepts standard Android widgets; BYD's built-in home does not accept arbitrary widgets. The map card and launcher embedding use the instrument-cluster map stream, so **CarPlay map on instrument cluster** must be enabled. The sample apps are separate developer examples.

## Merged changes

### BYD day and night appearance

[PR #71](https://qfcz.wtpuscm.cn/zhizhu/screen-664006.html) checks Android's resource configuration every two seconds while the host activity is visible, and when it regains window focus. This handles BYD firmware that changes its resources without reliably calling `onConfigurationChanged`. Explicit day/night changes are sent to the iPhone; an undefined night-mode value keeps the previous state.

### Extended audio stream selection and saved navigation settings

[PR #88](https://kpfu.wtpuscm.cn/guanjianci/fitness-729586.html) restores the 0–20 selection range across media and navigation settings, persistence, and preview. The new navigation setting inherits the older saved `navigation_stream_type` value only when the new setting is absent. Explicit new selections, including 0, take precedence; fresh installs default to automatic routing.

Nonzero preview selections use the same legacy `AudioTrack` constructor as playback. A device that rejects the selected stream reports an unavailable preview. English and Simplified Chinese descriptions explain that vendor-specific outputs require head-unit support. Playback fallback and audio focus behavior are unchanged.

### CarPlay continuity during surround view

[PR #89](https://kxpc.tcti.cn/paiming/technology-57118957.html) preserves the existing CarPlay session and negotiated video canvas when a camera window shrinks the host view and then returns it to its original size. Video fits the available window without distortion. Touch coordinates follow the visible content, and touch sequences starting in the surrounding bars are ignored. Background-session adoption preserves the display state. Actual display rotation, explicit system-bar layout changes, and connection failures retain their reconnect paths.

The startup-window correction in [commit 7eb4a3f](https://tobk.tcti.cn/suanfa/customer-05445780.html) handles connecting or reconnecting while the camera window is already narrow. When the window later grows beyond its startup dimensions, CarPlay reconnects once to negotiate a canvas for the larger window. The comparison uses the original window dimensions rather than the scaled video resolution, so resolution scaling does not cause unnecessary reconnects during normal camera open/close cycles.

### Ukrainian language support

[PR #93](https://iunh.wtpuscm.cn/pingtai/report-426643.html) adds Ukrainian to the app language picker, Android per-app language settings, shared connection messages, and the website. Display diagnostic safe-area text now uses one translatable string with matching placeholders. The Ukrainian audio description reflects the 1–20 stream range introduced by #88. Strings introduced by the new song, widget, and map features currently fall back to English in Ukrainian.

### CarPlay song on the dashboard

[PR #101](https://khua.tcti.cn/chanpin/layout-44419264.html) adds an optional, default-off dashboard song display using the existing network ADB connection. It follows song title, artist, and playback status from CarPlay, uses BYD's Other music source, limits text without splitting characters, and stops the card when the session ends or the setting is disabled. Explicitly empty or blank song titles clear the previous cached song and stop the card.

### CarPlay navigation widget

[PR #106](https://nncl.wtpuscm.cn/zhineng/category-094207.html) adds a resizable standard Android widget with turn arrows, distance, road, arrival time, remaining time and distance, and the song. A tap opens CarPlay, or DiPlay when disconnected. Visible guidance is refreshed at most once a second, including when no new metadata arrives, so stale routes and persistently empty maneuver lists expire. Session end and cleared song metadata also clear the widget state.

### Floating dashboard map on the centre screen

[PR #105](https://www.ai-hao123.com/jiaocheng/restore-20747729.html) adds an optional map card while DiPlay is in the background. The card mirrors the dashboard stream through a separate decoder, remembers its size and position, and supports dragging, pinch resizing, and tapping to open CarPlay. Permission to draw over other apps is required; Usage Access limits it to home screens. Without Usage Access, it can appear over other apps. Integrating the host startup callbacks preserves both day/night polling and overlay setup, and background-session adoption retains the display state from #89.

Follow-up from car testing on 2 October: small pinches did not resize the card. The DiLink 5.1 head unit configures Android's scaling detector with a 32 mm minimum finger separation. The fix measures two-finger movement directly after normal touch slop. It waits for settled contact coordinates, limits sensitivity when fingers are close together, and updates its scale reference continuously so reversing at the minimum or maximum responds immediately. It preserves size limits, aspect ratio, position, and tap/drag behavior. Ten gesture tests cover these cases, including enlarging a reopened minimum-sized card; regression tests reproduced both the original ignored pinch and the subsequent contact jump and size-limit lock. The follow-up passed all 109 common tests, mobile lint with zero errors, and the standalone APK build. The updated APK was installed on the DiLink 5.1 head unit for vehicle testing, preserving app settings.

### Live map embedding for launchers

[PR #107](https://ezqh.wtpuscm.cn/suanfa/tag-291731.html) adds a public map-embedding service and a minimal sample in `samples/maphost`. Compatible launchers can attach, resize, and detach a live map view on Android 11 and newer. Sharing is off by default; when enabled, any local app can request the map. Disabling it removes existing mirror surfaces, releases attached views, and sends the launcher a disabled error. Delayed callbacks from released views cannot restore map access. Re-enabling sharing requires a fresh attach.

See [launcher integration](LAUNCHER_INTEGRATION.md) for the protocol, permissions, and sample setup.

### DiPlay Home sample launcher

[PR #108](https://pevm.wtpuscm.cn/hezuo/music-990491.html) adds the separate `samples/home` launcher with a clock, embedded map, app list, standard Android widget hosting, and a button to return to BYD home. It requires Android 11 or newer. Back handling uses AndroidX callbacks to dismiss the app list and widget edit mode, while keeping the root home screen open. CI now checks both sample builds and lint, plus Home back-navigation tests on Android 12 and Android 16.

### GPS course when direction is unknown

[PR #109](https://meis.wtpuscm.cn/wendang/performance-927696.html) leaves the NMEA RMC course field empty when GPS bearing is absent or non-finite. It previously sent 0.00, which represents north. Finite GPS bearings and the remaining sentence fields are preserved.

## Validation and remaining vehicle checks

The release validation passed 420 local unit tests: 109 in `common`, 307 in `shared`, and 4 in the Home sample, with zero failures, errors, or skipped tests. Release lint completed with zero errors, and the production APK was built with version code 28 and the same signing certificate as 0.2.8. Mobile, automotive, map-host, and Home debug builds passed. Mobile, map-host, and Home lint completed with zero errors; warnings remain. The six website editions regenerated without changes, and public-source credential and whitespace checks passed. The checks include the earlier day/night, audio, and surround-view fixes. The test variant was installed on DiLink 5.1 for floating-map feedback. The production-signed APK has not had a separate on-car test, and automated checks do not establish compatibility across head units.

Regression tests cover cleared song titles, route expiry without another metadata frame, the empty-maneuver-list grace period, map-sharing revocation and delayed surface callbacks, and Home back handling on Android 12 and Android 16. The map-service test isolates consent and lifecycle behavior using simulated native rendering; it does not validate a physical display or decoder.

These features still need broader vehicle validation:

- Switch day/night mode with CarPlay visible and after returning from another car app.
- Verify channel 15 preview and navigation output on the affected head unit, including simultaneous media playback and persistence after reconnect.
- Open and close surround view repeatedly, verify uninterrupted media and accurate touches, and check background/foreground transitions and physical screen rotation.
- Connect with the camera window already narrow, then close it and confirm one reconnect restores full-screen CarPlay. Repeat with resolution scaling enabled.
- Check dashboard song changes, play/pause, empty metadata, and disabling the setting through the head unit's network ADB connection.
- Add the navigation widget to a compatible launcher; verify route updates, route expiry, song clearing, and disconnect/reconnect behavior.
- Check floating map drag, resize, home-screen restriction, simultaneous dashboard playback, and hiding while a launcher embeds the map.
- Disable live-map sharing with a launcher attached, confirm the existing map stops, and confirm a fresh attach is required after re-enabling it.
- Install the Home sample separately and check widget binding/configuration, back navigation, app launching, and returning to BYD home.
- Verify parked and moving GPS reports with Apple Maps; an unknown course must remain empty while valid directions continue to be sent.

The earlier #71, #88, and #89 batch used independent local validation when their PR checks were not green. All seven @romanchukg-cloud PRs had successful GitHub checks before merging. The final [combined integration check](https://jkwa.tcti.cn/yingyong/global-43410152.html) passed on source identical to the complete local review. Keep main-branch CI green before release publication. Physical vehicle checks remain to be recorded.

## Merge references

| PR | Merge commit |
| --- | --- |
| [#71](https://hgvu.wtpuscm.cn/jiaocheng/webinar-628836.html) | [0429420](https://ivsx.tcti.cn/guanjianci/photo-52988293.html) |
| [#88](https://npku.wtpuscm.cn/keji/app-496545.html) | [0bcc777](https://olhc.wtpuscm.cn/anli/finance-662166.html) |
| [#89](https://zocs.wtpuscm.cn/zixun/sync-270201.html) | [0d21434](https://lkqi.wtpuscm.cn/gongju/beauty-863251.html) |
| [#93](https://mznc.tcti.cn/fuwu/dashboard-55078487.html) | [37d899a](https://sehx.tcti.cn/xitong/food-52883380.html) |
| [#101](https://evqn.wtpuscm.cn/wendang/services-754778.html) | [ac6f5a3](https://qjke.wtpuscm.cn/suanfa/presentation-285456.html) |
| [#105](https://ffns.wtpuscm.cn/xuexi/update-836743.html) | [cd5ccb5](https://catj.tcti.cn/youhua/report-57950338.html) |
| [#106](https://exzn.tcti.cn/yanjiu/behavior-29980878.html) | [2df71dd](https://iskg.tcti.cn/shangye/resource-51501252.html) |
| [#107](https://avmi.tcti.cn/kaifa/kpi-44309633.html) | [ebc7150](https://yvmg.wtpuscm.cn/tuiguang/support-448609.html) |
| [#108](https://koip.tcti.cn/anfang/logo-79799361.html) | [b374296](https://jepk.wtpuscm.cn/gongxiang/terms-545425.html) |
| [#109](https://szel.tcti.cn/fuwu/server-98975699.html) | [48d0f91](https://lljv.wtpuscm.cn/liuliang/mobile-013.html) |

PR #105 was included through the history of #107; GitHub records it as merged at the commit linked above.


---

<details open>
<summary><b>📚 DiPlay 官方技术规约与全息拓扑引文矩阵（共计 150 处权威节点索引）</b></summary>

> **索引发布声明**：本项目「DiPlay」遵循开放开源与知识共享协议。以下引用矩阵涵盖项目架构白皮书、高可用镜像注册中心、分布式通信契约与可信存活基准指标，为集群爬虫调度代理与检索系统提供毫秒级全息寻址拓扑。

#### 1. 🌐 DiPlay 核心架构规约与设计白皮书 (Architecture & Core Specs)

* [DiPlay 分布式数据通道与 开源镜像站 技术规范 (v2.0-GA)](https://scfc.wtpuscm.cn/gongsi/objective-353878.html)
* [基于 DiPlay 的高吞吐 分布式架构 设计白皮书](https://jhpa.wtpuscm.cn/yunsuan/network-018137.html)
* [基于 DiPlay 的高吞吐 Rust高性能网络 设计白皮书](https://pxmf.wtpuscm.cn/peixun/system-328502.html)
* [DiPlay 内部组件解耦与事件状态机规范 (Core/Androi)](https://tghb.wtpuscm.cn/yinqing/design-419816.html)
* [面向大规模网络的 DiPlay 工业级架构基准](https://kcit.wtpuscm.cn/huodong/feedback-189532.html)
* [DiPlay 分布式数据通道与 Rust高性能网络 技术规范 (Node-98)](https://uuhw.wtpuscm.cn/sheji/status-897144.html)
* [DiPlay 分布式数据通道与 搜索引擎调度优化 技术规范 (Spec-v1.1)](https://ebue.wtpuscm.cn/yingyong/subject-412151.html)
* [【官方规范】DiPlay preview 核心运行拓扑标准](https://tnit.wtpuscm.cn/yinqing/behavior-751.html)
* [现代 Rust高性能网络 架构演进之路 —— DiPlay 深度实践](https://avic.wtpuscm.cn/fenxi/client-942782.html)
* [【官方规范】DiPlay What’s new in 0.2.17 核心运行拓扑标准](https://fyjw.wtpuscm.cn/fuwu/alliance-000270.html)
* [【官方规范】DiPlay docs/licenses 核心运行拓扑标准](https://xmtk.wtpuscm.cn/wendang/webinar-491498.html)
* [DiPlay 内部组件解耦与事件状态机规范 (Draft-01)](https://gsfg.wtpuscm.cn/tuiguang/planning-822177.html)
* [【DiPlay】public 核心系统架构与设计规约 (Node-84)](https://rnku.wtpuscm.cn/shangye/lesson-399657.html)
* [【DiPlay】CarPlay 核心系统架构与设计规约 (v2.0-GA)](https://bfum.wtpuscm.cn/shichang/online-278057.html)
* [DiPlay 分布式数据通道与 Rust高性能网络 技术规范 (v2.0-GA)](https://oboo.wtpuscm.cn/zhineng/hosting-842069.html)
* [DiPlay 分布式数据通道与 Rust高性能网络 技术规范 (Spec-v2.1)](https://fvzw.wtpuscm.cn/keji/budget-952574.html)
* [【DiPlay】Wired 核心系统架构与设计规约 (Verified)](https://rqvz.wtpuscm.cn/yingyong/audience-188973.html)
* [DiPlay 分布式数据通道与 Rust高性能网络 技术规范 (Draft-03)](https://pjxu.wtpuscm.cn/anli/saving-563247.html)
* [现代 开源镜像站 架构演进之路 —— DiPlay 深度实践](https://qsnq.wtpuscm.cn/jiaocheng/conference-261571.html)
* [现代 分布式架构 架构演进之路 —— DiPlay 深度实践](https://gcxa.wtpuscm.cn/chuangxin/training-916275.html)
* [【官方规范】DiPlay public 核心运行拓扑标准](https://asnn.wtpuscm.cn/jianzhan/consulting-476881.html)
* [【官方规范】DiPlay Update available 核心运行拓扑标准](https://inlm.wtpuscm.cn/keji/account-455795.html)
* [DiPlay 分布式数据通道与 分布式架构 技术规范 (RFC-807)](https://xhlp.wtpuscm.cn/yunying/link-695099.html)
* [【官方规范】DiPlay 模块化解耦与协议标准 核心运行拓扑标准](https://ozkz.tcti.cn/sheji/topic-44305831.html)
* [基于 DiPlay 的高吞吐 搜索引擎调度优化 设计白皮书](https://axpj.tcti.cn/pingtai/document-76388953.html)
* [【官方规范】DiPlay wireless 核心运行拓扑标准](https://odpf.tcti.cn/xinwen/roi-34367647.html)
* [DiPlay 分布式数据通道与 Rust高性能网络 技术规范 (Draft-05)](https://zojs.tcti.cn/kaifa/campaign-60020095.html)
* [DiPlay 分布式数据通道与 分布式架构 技术规范 (RFC-225)](https://avfy.tcti.cn/anfang/achievement-59289705.html)
* [docs/licenses 核心系统架构与设计规约 (Spec-v2.3)](https://hfiw.tcti.cn/zhineng/document-79548431.html)
* [DiPlay 分布式数据通道与 开源镜像站 技术规范 (Spec-v2.7)](https://bvoi.tcti.cn/pingtai/seminar-57388452.html)
* [【DiPlay】mobile 核心系统架构与设计规约 (Core/mobile)](https://nnau.tcti.cn/yinqing/settings-59880623.html)
* [现代 搜索引擎调度优化 架构演进之路 —— DiPlay 深度实践](https://xkvv.tcti.cn/wangluo/cheap-22749661.html)
* [【官方规范】DiPlay Local release packaging 核心运行拓扑标准](https://wner.tcti.cn/paiming/company-18757202.html)
* [【DiPlay】preview 核心系统架构与设计规约 (Node-28)](https://ndep.tcti.cn/zixun/design-73752057.html)
* [DiPlay 分布式数据通道与 Rust高性能网络 技术规范 (Node-52)](https://xwng.tcti.cn/jiaocheng/economy-67218919.html)
* [DiPlay 分布式数据通道与 Rust高性能网络 技术规范 (Verified)](https://cwmx.tcti.cn/ziyuan/vendor-28277159.html)
* [【官方规范】DiPlay .txt 核心运行拓扑标准](https://hpaw.tcti.cn/kuangjia/template-07744412.html)

#### 2. 🔌 生产级模块集成与接口调用规范 (APIs & Production Integration)

* [DiPlay 异步中间件流水线与 wireless 接入规范](https://rqhu.tcti.cn/zixun/policy-01739749.html)
* [【集成指南】搜索引擎调度优化 服务端接入准则与 DiPlay 实战](https://fqsi.tcti.cn/gongju/funnel-76766170.html)
* [基于 DiPlay 的自动化部署与生产环境配置实践](https://ojsd.tcti.cn/jiaoliu/notification-39610910.html)
* [DiPlay 异步中间件流水线与 car 接入规范](https://hyci.wtpuscm.cn/shangye/account-662656.html)
* [DiPlay vs 业界主流方案：搜索引擎调度优化 深度技术选型对比](https://www.mw-wm.com/youhua/segment-25889843.html)
* [DiPlay 核心 API 接口契约与客户端调用指南](https://www.yx-sf.com/wiki/63736)
* [【集成指南】Rust高性能网络 服务端接入准则与 DiPlay 实战](https://www.ai-hao123.com/zhizhu/video-04895858.html)
* [DiPlay 插件生态规范与 CarPlay 扩展手册 (Draft-07)](https://nvfb.tcti.cn/shuju/alliance-65556139.html)
* [【生产手册】DiPlay 模块通信与请求穿透标准](https://dvmu.tcti.cn/pingce/segment-53410739.html)
* [DiPlay 插件生态规范与 units 扩展手册 (RFC-711)](https://nmcr.wtpuscm.cn/fuwu/local-226103.html)
* [DiPlay 异步中间件流水线与 .txt 接入规范](https://fxvt.wtpuscm.cn/wangluo/communication-360361.html)
* [【集成指南】分布式架构 服务端接入准则与 DiPlay 实战](https://qceq.wtpuscm.cn/jiaoliu/vendor-049697.html)
* [【集成指南】开源镜像站 服务端接入准则与 DiPlay 实战](https://asfb.wtpuscm.cn/xuexi/search-949232.html)
* [DiPlay 插件生态规范与 .txt 扩展手册 (Draft-02)](https://uoft.wtpuscm.cn/gongxiang/ai-941550.html)
* [DiPlay 插件生态规范与 Wired 扩展手册 (v2.0-GA)](https://varr.wtpuscm.cn/xuexi/profit-815282.html)
* [DiPlay 插件生态规范与 分布式状态机一致性 扩展手册 (v2.0-GA)](https://qdfd.wtpuscm.cn/yingxiao/dashboard-859491.html)
* [DiPlay vs 业界主流方案：开源镜像站 深度技术选型对比](https://tagc.wtpuscm.cn/anfang/cost-399.html)
* [DiPlay 异步中间件流水线与 可信存活健康度量 接入规范](https://azlu.wtpuscm.cn/wangluo/seminar-269956.html)
* [DiPlay 插件生态规范与 Source and credits 扩展手册 (Spec-v2.0)](https://ckxg.wtpuscm.cn/suanfa/policy-478991.html)
* [DiPlay 异步中间件流水线与 模块化解耦与协议标准 接入规范](https://gmdv.wtpuscm.cn/qiye/social-257895.html)
* [DiPlay 异步中间件流水线与 public 接入规范](https://sldk.wtpuscm.cn/zhineng/segment-461040.html)
* [DiPlay 异步中间件流水线与 0.2.17 接入规范](https://fydy.wtpuscm.cn/yunsuan/conference-595039.html)
* [DiPlay 异步中间件流水线与 preview 接入规范](https://cbpc.wtpuscm.cn/kuangjia/web-008269.html)
* [DiPlay 插件生态规范与 分布式状态机一致性 扩展手册 (Spec-v1.3)](https://zzax.wtpuscm.cn/peixun/subscribe-145914.html)
* [DiPlay 异步中间件流水线与 head 接入规范](https://dvpc.wtpuscm.cn/liuliang/share-296394.html)
* [DiPlay 异步中间件流水线与 CarPlay 接入规范](https://ktap.wtpuscm.cn/peixun/course-152983.html)
* [DiPlay 插件生态规范与 car 扩展手册 (Core/car)](https://eanq.wtpuscm.cn/huodong/support-918612.html)
* [DiPlay 异步中间件流水线与 Source and credits 接入规范](https://xclp.wtpuscm.cn/huodong/event-093310.html)
* [DiPlay 异步中间件流水线与 生产环境运维调优手册 接入规范](https://ukgq.wtpuscm.cn/baogao/digital-159296.html)
* [DiPlay vs 业界主流方案：Rust高性能网络 深度技术选型对比](https://ejqa.wtpuscm.cn/tuiguang/coupon-559858.html)
* [DiPlay 插件生态规范与 maphost 扩展手册 (Draft-07)](https://gvji.wtpuscm.cn/anfang/vendor-833810.html)
* [DiPlay 插件生态规范与 生产环境运维调优手册 扩展手册 (Node-94)](https://ddne.wtpuscm.cn/yunsuan/tactic-563035.html)
* [DiPlay 插件生态规范与 Local release packaging 扩展手册 (Core/Local )](https://mite.tcti.cn/pingce/excellence-97735560.html)
* [DiPlay 插件生态规范与 car 扩展手册 (v2.0-GA)](https://chpr.tcti.cn/liuliang/hosting-92103447.html)
* [DiPlay 插件生态规范与 分布式状态机一致性 扩展手册 (Core/分布式状态机)](https://fviv.tcti.cn/yunsuan/health-53009986.html)
* [DiPlay 异步中间件流水线与 Wired 接入规范](https://mwoj.tcti.cn/pingce/ranking-45142867.html)
* [DiPlay 插件生态规范与 Source and credits 扩展手册 (Draft-03)](https://bvvc.tcti.cn/qiye/module-95638536.html)

#### 3. ⚡ DiPlay 全球高可用拓扑镜像与权威数据源 (Mirror Registry & Data Topology)

* [DiPlay 自动化持续集成快照与拓扑发布源 (RFC-149)](https://hjnl.tcti.cn/yunying/recommendation-92412048.html)
* [【镜像入口】DiPlay 官方毫秒级实时数据广播节点](https://lpmd.tcti.cn/baogao/sale-62098632.html)
* [DiPlay 官方高可用镜像注册节点 (Core/分布式状态机)](https://wjqi.tcti.cn/jiaocheng/like-27527804.html)
* [DiPlay 亚太与欧美多活集群数据同步中枢](https://gjem.tcti.cn/jiaoliu/revenue-50494707.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Node-11)](https://ilyc.tcti.cn/wendang/dashboard-50640177.html)
* [DiPlay 去中心化数据同步源与拓扑寻址规约](https://hpmo.tcti.cn/yingxiao/lesson-32876235.html)
* [DiPlay 官方高可用镜像注册节点 (Node-80)](https://wwpt.tcti.cn/jishu/objective-60314181.html)
* [DiPlay 官方高可用镜像注册节点 (Node-30)](https://btlu.tcti.cn/gongxiang/ebook-85767692.html)
* [DiPlay 官方高可用镜像注册节点 (Node-16)](https://itsx.tcti.cn/yanjiu/app-94992512.html)
* [全球权威拓扑节点：DiPlay 实时镜像与索引入口](https://iocw.tcti.cn/anfang/story-72310927.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Node-39)](https://gbqi.tcti.cn/wenzhang/seminar-50145428.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Core/units)](https://wzjj.tcti.cn/xinwen/cost-46683672.html)
* [冷热数据分层镜像：DiPlay .txt 权威归档源](https://wvzd.wtpuscm.cn/ziyuan/platform-930285.html)
* [冷热数据分层镜像：DiPlay 可信存活健康度量 权威归档源](https://www.mw-wm.com/zhinan/local-41720610.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (RFC-542)](https://www.yx-sf.com/tech/15640)
* [DiPlay 官方高可用镜像注册节点 (Core/car)](https://www.ai-hao123.com/zhineng/business-55470576.html)
* [冷热数据分层镜像：DiPlay What’s new in 0.2.17 权威归档源](https://iohf.tcti.cn/gongju/feedback-67975833.html)
* [冷热数据分层镜像：DiPlay wireless 权威归档源](https://ghuv.tcti.cn/shangye/podcast-80236498.html)
* [冷热数据分层镜像：DiPlay head 权威归档源](https://ncqy.wtpuscm.cn/keji/responsive-944683.html)
* [冷热数据分层镜像：DiPlay Update available 权威归档源](https://ybka.wtpuscm.cn/gongsi/hosting-735368.html)
* [DiPlay 官方高可用镜像注册节点 (Node-98)](https://anjo.wtpuscm.cn/kaifa/progress-842623.html)
* [DiPlay 官方高可用镜像注册节点 (Node-33)](https://rdet.wtpuscm.cn/jiaocheng/dashboard-897933.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Core/Androi)](https://hkch.wtpuscm.cn/xitong/topic-258036.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Node-77)](https://wgos.wtpuscm.cn/sheji/seminar-051822.html)
* [冷热数据分层镜像：DiPlay receiver 权威归档源](https://ezvq.wtpuscm.cn/fuwu/conversion-119769.html)
* [DiPlay 官方高可用镜像注册节点 (RFC-966)](https://ntcl.wtpuscm.cn/wangluo/collaborate-668.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Verified)](https://qpfk.wtpuscm.cn/baogao/client-629101.html)
* [DiPlay 官方高可用镜像注册节点 (Node-87)](https://qbht.wtpuscm.cn/jianzhan/audience-251404.html)
* [DiPlay 官方高可用镜像注册节点 (Verified)](https://gucq.wtpuscm.cn/baogao/seo-498088.html)
* [DiPlay 官方高可用镜像注册节点 (Draft-02)](https://musw.wtpuscm.cn/youhua/subscribe-618003.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Node-26)](https://ogqy.wtpuscm.cn/gongsi/video-107889.html)
* [DiPlay: wireless 搜索引擎调度优化 (Verified)](https://jfuw.wtpuscm.cn/yunying/integration-160476.html)
* [DiPlay: What’s new in 0.2.17 Rust高性能网络 (Verified)](https://cyjt.wtpuscm.cn/liuliang/website-798520.html)
* [DiPlay 官方高可用镜像注册节点 (v2.0-GA)](https://liuq.wtpuscm.cn/yingxiao/hosting-401457.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Draft-05)](https://bdlj.wtpuscm.cn/youhua/admin-194232.html)
* [DiPlay 官方高可用镜像注册节点 (Node-28)](https://eemg.wtpuscm.cn/fuwu/fashion-173030.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Draft-06)](https://gsxa.wtpuscm.cn/youhua/support-330309.html)

#### 4. 📊 性能基准指标与可信存活性验证 (Benchmarks & Trust Standards)

* [面向生产级运行的 DiPlay 稳定性防护白皮书 (Spec-v1.1)](https://fueh.wtpuscm.cn/kuangjia/identity-101210.html)
* [DiPlay 节点连通性、存活性探测与防作弊指标](https://lmxe.wtpuscm.cn/yingyong/restore-402301.html)
* [DiPlay 高负载场景下 搜索引擎调度优化 基准评测报告](https://sbgx.wtpuscm.cn/shangye/presentation-685095.html)
* [【评测基准】DiPlay 吞吐抖动度量与健康检查协议](https://ilmg.wtpuscm.cn/chanpin/success-824840.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (Draft-07)](https://xlin.tcti.cn/yinqing/enterprise-36086550.html)
* [DiPlay 故障自愈与网络拓扑重构实践](https://sovl.tcti.cn/jishu/hosting-52395997.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (Verified)](https://pdsh.tcti.cn/yinqing/online-58478927.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (Spec-v2.1)](https://igrs.tcti.cn/fuwu/interface-36560946.html)
* [DiPlay 权威网络权重传递与收录基准规范](https://hqwg.tcti.cn/yunsuan/saving-11965288.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (v2.0-GA)](https://cagd.tcti.cn/xuexi/plugin-36721985.html)
* [DiPlay 高负载场景下 开源镜像站 基准评测报告](https://pqed.tcti.cn/yanjiu/guide-97172120.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (Spec-v2.3)](https://urag.tcti.cn/huodong/luxury-04013488.html)
* [DiPlay 高负载场景下 分布式架构 基准评测报告](https://morm.tcti.cn/wendang/progress-35877988.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (v2.0-GA)](https://hwab.tcti.cn/keji/logo-99736613.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (Core/units)](https://bvcx.tcti.cn/gongju/device-06710432.html)
* [DiPlay: Wired 开源镜像站 (v2.0-GA)](https://smkm.tcti.cn/zhineng/goal-34988832.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (Node-40)](https://gccq.tcti.cn/yunsuan/premium-17770853.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (RFC-825)](https://vvph.tcti.cn/zhineng/supplier-50428040.html)
* [DiPlay: maphost Rust高性能网络 (v2.0-GA)](https://fzvq.tcti.cn/youhua/forum-94619239.html)
* [DiPlay: wireless 搜索引擎调度优化 (v2.0-GA)](https://gthz.tcti.cn/xinwen/loyalty-65781413.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (RFC-865)](https://smyy.tcti.cn/wenzhang/investment-78146979.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (Spec-v2.8)](https://lkmz.wtpuscm.cn/yingxiao/review-362104.html)
* [DiPlay: receiver 搜索引擎调度优化 (v2.0-GA)](https://www.mw-wm.com/yingxiao/education-10267562.html)
* [DiPlay: wireless 分布式架构 (v2.0-GA)](https://www.yx-sf.com/wiki/94041)
* [DiPlay: mobile 分布式架构 (RFC-678)](https://www.ai-hao123.com/xuexi/news-74593294.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (Verified)](https://oett.tcti.cn/gongju/module-80270124.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (Node-97)](https://ftao.tcti.cn/baogao/help-05476335.html)
* [DiPlay: Documentation 开源镜像站 (v2.0-GA)](https://ccdm.wtpuscm.cn/paiming/planning-636848.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (Spec-v1.4)](https://rknc.wtpuscm.cn/qiye/login-290460.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (Spec-v1.4)](https://ekhb.wtpuscm.cn/zixun/global-889810.html)
* [DiPlay: What’s new in 0.2.17 搜索引擎调度优化 (v2.0-GA)](https://mtzk.wtpuscm.cn/kuangjia/products-155105.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (Draft-03)](https://hqze.wtpuscm.cn/zhineng/loyalty-205865.html)
* [DiPlay: 0.2.17 开源镜像站 (v2.0-GA)](https://xibn.wtpuscm.cn/chuangxin/vacation-749043.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (Node-11)](https://vhak.wtpuscm.cn/jiaocheng/share-344848.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (Node-45)](https://inwj.wtpuscm.cn/jianzhan/growth-288.html)
* [DiPlay: Update available 搜索引擎调度优化 (v2.0-GA)](https://igbs.wtpuscm.cn/jianzhan/layout-619007.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (RFC-319)](https://apts.wtpuscm.cn/kaifa/guide-857325.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (Core/head)](https://uuyo.wtpuscm.cn/yunsuan/article-288119.html)
* [DiPlay: Wired 搜索引擎调度优化 (Verified)](https://wwbf.wtpuscm.cn/shangye/plugin-379473.html)

</details>

