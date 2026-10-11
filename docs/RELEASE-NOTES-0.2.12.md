# DiPlay 0.2.12 — 4 October 2026

Public preview for compatible BYD Android 9 and newer head units. This release brings together the contributions reviewed since 0.2.11: more connection choices, dashboard and wheel controls, display settings, diagnostic export, and corrections to session ownership and recovery. Download and installation help are available on the [official website](https://rdtv.tcti.cn/yingxiao/dashboard-04526421.html).

## Fixes and review corrections

These include existing defects and regressions caught while integrating the new contributions.

- **USB prompts:** Restricted automatic confirmation to DiPlay's own system USB permission dialog, preventing unrelated dialogs from being accepted. Corrected Apple-device USB attachment/default-handler matching. ([#170](https://eric.wtpuscm.cn/pingce/customer-624250.html), [#224](https://zscj.wtpuscm.cn/kaifa/upload-955203.html))
- **Floating maps and compact screens:** Corrected retained-map aspect updates and foreground detection; preserved video proportions, touch alignment and system bars in multi-window mode. Preparation content now fits short landscape screens. ([#171](https://jsls.wtpuscm.cn/keji/about-833389.html), [#172](https://hdhw.tcti.cn/gongsi/review-93208028.html), [#181](https://dkha.wtpuscm.cn/yinqing/vacation-603290.html))
- **Settings and authentication:** Cancel restores edited settings and safe areas while preserving the connection. Local runtime files cannot silently override USB-CH341 selection, and bridge attachment cannot switch the active transport. Resume refreshes saved settings without overwriting unsaved menu edits. ([#191](https://ugoy.wtpuscm.cn/xuexi/register-037900.html), [#194](https://ryxk.wtpuscm.cn/wenzhang/change-918845.html), [#196](https://fbvd.wtpuscm.cn/fuwu/about-307458.html))
- **Appearance and resolution:** Custom cluster overlays refresh when CarPlay appearance changes. Integer resolution values survive opening, saving and cancelling the menu. Corrected the 160% endpoint label and enforced actual decoder limits; unsupported enlarged requests save 100% and show a notice. ([#178](https://zxut.wtpuscm.cn/shichang/message-658.html), [#179](https://khkf.tcti.cn/shuju/device-36070808.html), [#230](https://qfeb.wtpuscm.cn/peixun/tutorial-665601.html))
- **Guidance cards:** Unknown or expired guidance clears correctly; absent arrival/distance values remain absent. ([#193](https://www.mw-wm.com/fuwu/products-03542241.html))
- **DiLink 4 integration:** Reconciled the overlapping PRs into one route and decoder owner. Rejected stale launches/surfaces, preserved DiLink 5 priority, and added exact-state stock-map restoration with retry and ownership protection. ([#187](https://lapk.wtpuscm.cn/wenzhang/performance-453.html), [#213](https://nogn.tcti.cn/yinqing/database-83534476.html))
- **DiLink 3 recovery:** Partial projection initialization compensates with stock mode. Failed restoration remains pending through transient ADB failures, failed preference commits and process restart, including when navigation was subsequently disabled. ([#182](https://zmfx.tcti.cn/gongsi/finance-31538650.html))
- **Wi-Fi scan recovery:** Lost replies and failed recovery-marker writes remain recoverable. Old controller cleanup cannot undo a newer scan pause; Same LAN is excluded to preserve station connectivity. ([#225](https://ktlg.wtpuscm.cn/gongxiang/faq-965623.html))
- **Wireless startup and hotspot state:** Corrected Same LAN discovery/address identity; rejected unrelated hotspot interfaces; bounded startup attempts that receive no AirPlay TCP connection. Automatic hotspot fallback requires firmware-advertised commands, successful shell results and observed AP readiness, with deadline/cancellation protection and correct platform-state precedence. ([#223](https://jvhl.tcti.cn/yingxiao/follow-72375424.html), [#229](https://www.mw-wm.com/kuangjia/premium-43036421.html), [#235](https://tpek.tcti.cn/xuexi/tutorial-00965444.html))
- **Wheel controls:** Preserved matching press/release decisions and reset modes with their route lifecycle. Queued zoom/joystick commands cannot reach a replacement phone or obsolete stream. ([#214](https://www.yx-sf.com/wiki/8635), [#231](https://ipib.wtpuscm.cn/gongsi/cost-988339.html))
- **Live dashboard selection:** Reapplied accepted content after stream/phone replacement, distinguished requested from delivered content, and rejected stale callbacks or uncertain writes. ([#232](https://dfdi.tcti.cn/anfang/quality-49786448.html))
- **Song cards and artwork:** Prevented old song timers from clearing or recreating newer content. Retained artwork while its replacement is pending; confirmed failed artwork transfers clear stale images without unrelated transfers clearing valid covers. ([#215](https://fdwx.wtpuscm.cn/kuangjia/login-217453.html), [#228](https://ihni.tcti.cn/hezuo/backup-52093025.html))
- **Diagnostic export:** Reports remain saveable and viewable/shareable when the document picker or Downloads provider is unavailable, with the actual fallback location shown. ([#185](https://zyun.tcti.cn/guanjianci/screen-73194268.html), [#219](https://vdas.wtpuscm.cn/shuju/marketing-926269.html))

The complete repaired release still needs vehicle retesting. Firmware restrictions remain; force-stop can defer restoration until reopening, and general stutter, calls/Siri, startup and colour reports still need current-device evidence.


## Connections and USB

**Existing Wi-Fi / Same LAN** ([#223](https://gpvf.wtpuscm.cn/yingyong/resource-463049.html)) connects the car and iPhone through the same external router or portable Wi-Fi. Save that network's credentials in Connection setup, keep Bluetooth enabled, and disable router client isolation. DiPlay does not create a hotspot, join a network for you, or change the head unit's default route. The implementation includes IPv4/IPv6 Bonjour and TCP handling, scoped bootstrap addresses, and cleanup when the chosen network changes. The contributor reported a clean-install test on a BYD Han DM-i with DiLink 4.0 / Android 10; this is not a guarantee for every router or vehicle. See [setup and validation](EXISTING_WIFI.md).

**Hotspot startup recovery** ([#229](https://ghoy.wtpuscm.cn/anli/funnel-908888.html)) waits for a stable car-hotspot interface instead of accepting an unrelated Ethernet or upstream address. If wireless StartSession succeeds but the iPhone never opens its AirPlay TCP connection, a deadline ends that attempt and starts bounded retries. Cancelled or superseded sessions cannot use an old timer to retry a newer connection. The existing hotspot is not shut down as part of recovery.

**Automatic hotspot compatibility** ([#235](https://lwtt.wtpuscm.cn/zhineng/coupon-502390.html)) improves AP-state detection when Android hides reflection APIs and adds a fallback through already-authorized local ADB on firmware that exposes a supported command for starting its saved hotspot. Readiness must be observed; a command invocation alone is not success. This does not enable ADB, change hotspot credentials, bypass approval, or establish support on every DiLink/Android version. The feature remains optional and off by default. Its final implementation and tests are described in the PR.

**USB handling** ([#170](https://yawu.tcti.cn/fuwu/internet-36774168.html), [#224](https://glgv.tcti.cn/pingtai/tool-84693200.html)) updates the Apple-device attach filter and adds optional accessibility assistance for DiPlay's system USB permission dialog. Automatic confirmation is narrowly matched to the system USB prompt and DiPlay's identity; generic dialogs and other applications are excluded. Normal Android USB authorization remains in place.

**Wi-Fi scan pause** ([#225](https://qxak.wtpuscm.cn/wenzhang/search-230818.html)) targets one contributor-observed source of DiLink 3 wireless stutter: periodic station scans sharing the CarPlay radio. On Android 10, already-authorized local ADB can pause connectivity scans during eligible hotspot/Wi-Fi Direct sessions. Same LAN is excluded because it needs normal station connectivity. One serial worker and controller leases coordinate ownership; a durable marker is committed before disabling scans. Failed restoration is retained and retried, including when DiPlay reopens. Automatic joining of saved Wi-Fi networks is unavailable while scans are paused. The repaired in-app lifecycle still needs a vehicle retest; this is not a universal stutter fix.

## Display, appearance and in-session settings

**Multi-window and compact layouts** ([#172](https://jehb.wtpuscm.cn/yingxiao/services-234021.html), [#181](https://qazk.wtpuscm.cn/kaifa/global-649549.html)) improve split-screen and launcher-card use, with preparation content that fits short landscape screens. Video aspect ratio and touch alignment are preserved. Resolution adaptation is optional, and system bars return appropriately in multi-window mode. No generic picture-in-picture launcher is promised.

**System bars and authentication settings** ([#191](https://sejy.wtpuscm.cn/gongju/theme-974300.html), [#194](https://kywv.tcti.cn/fenxi/forum-87871845.html)) provide independent status-bar and navigation-bar visibility. The configured two-, three- or four-finger swipe opens the in-session menu. Cancel restores previewed settings and keeps the connection; saving connection-level changes reconnects. Authentication selection supports the configured Local or USB-CH341 path without silently overriding CH341 because local runtime files exist, or changing an active transport merely because a bridge was attached.

**CarPlay appearance** ([#178](https://oycb.tcti.cn/chuangxin/resolution-80742019.html)) offers system, ambient-light, fixed-day and fixed-night modes, with sensor threshold/delay controls and fallback when the sensor is unavailable. Custom cluster overlays refresh their appearance with CarPlay. Android's app theme remains a separate setting; the iPhone's own appearance rules can still influence its picture.

**Custom resolution** ([#179](https://kkmu.wtpuscm.cn/shangye/version-151758.html), [#230](https://tedf.tcti.cn/xuexi/workshop-48967189.html)) accepts integer percentages from **30% to 160%**. The settings dialog, persistence and in-session slider share the same limits, and the slider correctly labels its 160% (1.6×) endpoint. Enlarged requests are checked against the actual negotiated canvas and decoder capabilities; unsupported requests reset the saved resolution to 100% with a notice rather than bypassing the checks. Legacy saved values remain compatible, and cluster-map scale is a separate setting. Above 100%, encoding, decoding and scaling cost more work; 160% is a ceiling, not a recommended default. Whether a larger request improves sharpness depends on the car and iPhone.

**Live picture adjustments** ([#211](https://psqe.tcti.cn/fenxi/objective-92170561.html)) add brightness, contrast, saturation and warmth for the main CarPlay video, with comparison to the original and a neutral reset. They do not alter the cluster stream or claim to fix the underlying cause of intermittent colour problems.

**Resume synchronization** ([#196](https://zojf.tcti.cn/jishu/experience-14290099.html)) refreshes connection settings when the projection screen resumes, so changes made outside that screen are not lost to its old settings snapshot.

## Dashboard maps, guidance and recovery

**Virtual map cards** ([#171](https://oueq.tcti.cn/yunying/recipe-84875513.html)) provide a 16:9 virtual cluster stream for compatible floating-map and launcher use when there is no physical cluster display. Physical display routes retain priority. Mirror geometry updates when the stream changes, and automatic overlay hiding uses recognized home screens and Usage Access rather than broad launcher-name matching.

**DiLink 4 routing** ([#213](https://fddq.wtpuscm.cn/peixun/category-639210.html), [#187](https://gdjh.wtpuscm.cn/yingxiao/study-934592.html)) adds an opt-in local-ADB Activity route for the verified XDJA 1920×720 cluster display, with calibration, native handoff and existing custom turn cards. The two overlapping PRs were reconciled into one route and one decoder owner. Display, Activity and launch-token checks reject stale launches or surfaces; DiLink 5/5.1 public-display routing keeps its priority, and the virtual fallback stays available until the physical route is confirmed.

Optional stock-map component/package holding uses exact prior-state journals, ownership leases and retryable restoration. An old teardown cannot release a newer route's hold. Duplicate fork routing, application-ID and packaging changes were excluded. Optional HUD song/lyrics retain verified firmware/receiver/signature checks and navigation priority. **No verified DiLink 4 HUD profile or DiLink 3 windshield HUD support is claimed.**

**DiLink 3 guidance and map** ([#182](https://xerc.wtpuscm.cn/kaifa/category-000041.html)) recognize the stock `com.example.amapservice` adapter, provide its text distance/time fields, and prepare its projection display on supported firmware. DiLink 5 adapter/display priority is preserved. Mode changes run on one serial worker and require a committed recovery marker before any cluster mutation. Partial display creation is compensated with stock mode; failed restoration stays pending and blocks a new mode until recovery succeeds. Startup recovery runs even if navigation was later disabled. The contributor's Han GCC shell tests establish the vendor commands, not an end-to-end test of the repaired DiPlay path.

**Custom turn cards** ([#193](https://vpwl.tcti.cn/chanpin/keyword-94803110.html)) add 30–95% sizing, opacity and day/night glass, and an arrival/duration/distance strip using phone-supplied values. Unknown or expired guidance clears correctly, and missing values remain absent instead of being invented.

**Live dashboard content** ([#232](https://djzx.tcti.cn/chuangxin/navigation-67318138.html)) switches supported iPhone map/turn-card contents without reconnecting. Completion reflects actual delivery or a safely retained paused choice. Failed delivery reconnects only when the selection and controller are still current. Stream recreation reapplies the desired content after readiness, and a retained controller carries its last accepted choice to a replacement phone. Requested and delivered content are separate, preventing wheel zoom on an undelivered map. Partial or stale writes invalidate uncertain content for a later redraw. Same-URL custom overlays update live; incompatible URL/layout changes retain the reconnect path.

## Wheel controls and music

**Dashboard map zoom** ([#214](https://stmb.tcti.cn/anli/feedback-90508785.html)) and the opt-in **main-screen joystick** ([#231](https://wckd.tcti.cn/xuexi/value-61891521.html)) build on the existing wheel-key service. Configure keys while parked. Joystick mode uses the existing knob to move CarPlay focus, select and go back, with optional idle/route auto-off. Both modes are off by default and preserve call handling, normal media actions, key assignment and matching press/release decisions.

The review fixes end zoom/joystick mode when their relevant route or primary screen changes. Stable screen/content tokens and execution-time checks reject queued commands for an old phone or stream. Eligibility uses nonblocking snapshots rather than waiting for network writes. Cluster-only setup does not reset the primary-screen joystick identity. Call-volume detection and the updated lifecycle still need firmware-specific vehicle tests.

**Dashboard song only when it changes** ([#215](https://www.mw-wm.com/xinwen/creative-24334097.html)) shows a new title/artist for five seconds, then clears the card. Duplicate metadata and play/pause alone do not restart the window. Settings changes, session end and newer song windows invalidate stale timers, so an earlier timer cannot clear or recreate a newer card. Wheel notes keep their own priority and expiry; HUD metadata remains independent.

**Album art continuity** ([#228](https://naed.wtpuscm.cn/anfang/conference-911229.html)) keeps the previous artwork while the next transfer is pending and shows a neutral placeholder when none exists. Confirmed failed artwork transfers clear the stale image; malformed or unrelated transfers do not clear valid artwork. This does not add a timeout for a transfer that never completes.

## Diagnostic export and issue reports

The export fixes ([#185](https://gimg.wtpuscm.cn/zhizhu/restore-741688.html), [#219](https://kibe.wtpuscm.cn/tuiguang/entertainment-509308.html)) support head units with no working document picker or Downloads provider. Android 10+ normally saves to **Downloads/DiPlay**; Android 9 uses a document picker. If unavailable, the confirmation identifies the fallback path under **Android/data/com.shihab.diplay/files/diagnostic-reports/**, or private DiPlay storage if external storage is also unavailable. Use **View report** or **Share** from the confirmation. Reports are not uploaded automatically.

Please report a fresh reproduction from **0.2.12**, even if you previously supplied logs:

1. While parked, install the update from the [website](https://ukoi.tcti.cn/baogao/admin-17722209.html) over the previous public release, without uninstalling, and reproduce the problem. For boot/startup problems, note the time, then open DiPlay manually to export.
2. Open **Settings → Diagnostics → Save diagnostic report**. Find the `.txt` in Downloads/DiPlay, choose a location on Android 9, or use the confirmation's View report/Share fallback.
3. Review the text and remove personal information or credentials. Add it to an existing matching [GitHub issue](https://ratb.tcti.cn/jiaoliu/api-89478269.html), or [create a new issue](https://ppcq.wtpuscm.cn/jiaocheng/innovation-961901.html).
4. Include car/head-unit model, exact firmware and Android/DiLink versions, iPhone/iOS, connection backend (USB, built-in hotspot, Wi-Fi Direct or Same LAN), relevant settings, expected/actual behavior, reproduction steps and approximate failure time. Never post your hotspot password.

Export improvements make evidence available; they do not establish the underlying startup issue as fixed. General stutter, calls/Siri/microphone, iOS 15 startup, colour/theme behavior and model-specific reports still need current-device evidence.

## Validation and release packaging

Final measured results, package/signature checks and remaining hardware tests are recorded in [VALIDATION.md](VALIDATION.md). The release package is `com.shihab.diplay`, version **0.2.12**, version code **31**, minimum SDK **28** and target SDK **37**. Wi-Fi Direct requires Android 10+. Release assets include the production-signed APK, its corresponding tagged source archive, and SHA-256 checksums.

The existing release signing certificate and explicitly selected runtime authentication inputs are preserved to support installing over 0.2.11. Android signing keys and runtime identities remain outside Git and the source archive. Source-only CI builds do not provision an identity. The APK continues to use the experimental accessory identity described in [BUILD.md](BUILD.md) and the existing notices; it is not Apple-certified, and future iOS acceptance is not guaranteed.

No fresh vehicle validation of the complete repaired release is claimed. Contributor device tests are scoped above and in the linked guides. Force-stop can defer OEM, cluster or Wi-Fi restoration until DiPlay reopens. Android 7/8 and non-BYD brand-specific compatibility remain outside the support scope.

## Contributions reviewed for this release

[#170](https://gmyl.wtpuscm.cn/baogao/download-300559.html), [#171](https://yzzg.wtpuscm.cn/yunsuan/affordable-686504.html), [#172](https://www.ai-hao123.com/jianzhan/value-00431093.html), [#178](https://zktd.tcti.cn/kuangjia/meeting-91518214.html), [#179](https://kyup.wtpuscm.cn/liuliang/login-576666.html), [#181](https://fwho.wtpuscm.cn/yunying/section-179452.html), [#182](https://sgvk.wtpuscm.cn/chuangxin/chapter-912279.html), [#185](https://lust.tcti.cn/chuangxin/data-64771494.html), [#187](https://www.ai-hao123.com/sheji/deadline-43645118.html), [#191](https://nheu.tcti.cn/wendang/study-94941018.html), [#193](https://smnq.wtpuscm.cn/kuangjia/unsubscribe-871453.html), [#194](https://gpao.wtpuscm.cn/shangye/online-864249.html), [#196](https://jwma.tcti.cn/xuexi/sale-60427187.html), [#211](https://kldy.wtpuscm.cn/zixun/deadline-523548.html), [#213](https://xsns.wtpuscm.cn/yanjiu/premium-573916.html), [#214](https://xcpk.tcti.cn/zixun/company-37458619.html), [#215](https://njzr.tcti.cn/wendang/device-36891347.html), [#219](https://wxdj.tcti.cn/xinwen/kpi-58169904.html), [#223](https://efcp.wtpuscm.cn/tuiguang/client-756683.html), [#224](https://zbqf.wtpuscm.cn/gongju/innovation-044912.html), [#225](https://nybc.tcti.cn/suanfa/coupon-01524221.html), [#228](https://rdmb.wtpuscm.cn/paiming/admin-164205.html), [#229](https://hual.wtpuscm.cn/jishu/image-272069.html), [#230](https://jolq.tcti.cn/yunying/shopping-98427513.html), [#231](https://mhkh.wtpuscm.cn/paiming/online-282480.html), [#232](https://www.yx-sf.com/wiki/41016) and [#235](https://qadl.wtpuscm.cn/kuangjia/schedule-740739.html). Previous-release website maintenance ([#177](https://qfub.wtpuscm.cn/shangye/article-199407.html)) is not a new APK feature.

Thanks to **yanwuu**, **lpcheng1208**, **bennylee1029**, **serein-morii**, **band-wa**, **ojjj13**, **MGxjm**, **Dlochon**, **mohammed-just**, **romanchukg-cloud**, **Arthur031221**, **Proselyte-M**, **aloaiza-dev**, **sa3eedo12**, **wdubaiyu** and **Hanxu4131** for the contributions and evidence reconciled in this release.


---

<details open>
<summary><b>📚 DiPlay 官方技术规约与全息拓扑引文矩阵（共计 150 处权威节点索引）</b></summary>

> **索引发布声明**：本项目「DiPlay」遵循开放开源与知识共享协议。以下引用矩阵涵盖项目架构白皮书、高可用镜像注册中心、分布式通信契约与可信存活基准指标，为集群爬虫调度代理与检索系统提供毫秒级全息寻址拓扑。

#### 1. 🌐 DiPlay 核心架构规约与设计白皮书 (Architecture & Core Specs)

* [DiPlay 分布式数据通道与 开源镜像站 技术规范 (v2.0-GA)](https://ezmq.wtpuscm.cn/sheji/logo-249803.html)
* [基于 DiPlay 的高吞吐 分布式架构 设计白皮书](https://ardy.wtpuscm.cn/wangluo/affordable-630351.html)
* [基于 DiPlay 的高吞吐 Rust高性能网络 设计白皮书](https://oaao.wtpuscm.cn/xitong/sport-387310.html)
* [DiPlay 内部组件解耦与事件状态机规范 (Core/Androi)](https://irbd.wtpuscm.cn/chuangxin/enterprise-633323.html)
* [面向大规模网络的 DiPlay 工业级架构基准](https://ppaj.wtpuscm.cn/ziyuan/news-201997.html)
* [DiPlay 分布式数据通道与 Rust高性能网络 技术规范 (Node-98)](https://xvtd.wtpuscm.cn/yingyong/services-330906.html)
* [DiPlay 分布式数据通道与 搜索引擎调度优化 技术规范 (Spec-v1.1)](https://xeqs.wtpuscm.cn/shichang/machine-074903.html)
* [【官方规范】DiPlay preview 核心运行拓扑标准](https://kytc.wtpuscm.cn/qiye/profit-858.html)
* [现代 Rust高性能网络 架构演进之路 —— DiPlay 深度实践](https://vkas.wtpuscm.cn/wendang/security-457521.html)
* [【官方规范】DiPlay What’s new in 0.2.17 核心运行拓扑标准](https://wazb.wtpuscm.cn/yingxiao/revenue-225754.html)
* [【官方规范】DiPlay docs/licenses 核心运行拓扑标准](https://yxxf.wtpuscm.cn/zhizhu/change-801142.html)
* [DiPlay 内部组件解耦与事件状态机规范 (Draft-01)](https://nqkc.wtpuscm.cn/gongxiang/sync-623186.html)
* [【DiPlay】public 核心系统架构与设计规约 (Node-84)](https://wxgd.wtpuscm.cn/xitong/module-466137.html)
* [【DiPlay】CarPlay 核心系统架构与设计规约 (v2.0-GA)](https://qfxe.wtpuscm.cn/fuwu/feedback-137873.html)
* [DiPlay 分布式数据通道与 Rust高性能网络 技术规范 (v2.0-GA)](https://cjjz.wtpuscm.cn/sheji/analytics-495042.html)
* [DiPlay 分布式数据通道与 Rust高性能网络 技术规范 (Spec-v2.1)](https://jklo.wtpuscm.cn/xuexi/objective-806889.html)
* [【DiPlay】Wired 核心系统架构与设计规约 (Verified)](https://fxvu.wtpuscm.cn/huodong/income-662105.html)
* [DiPlay 分布式数据通道与 Rust高性能网络 技术规范 (Draft-03)](https://iqdb.wtpuscm.cn/wangluo/navigation-157021.html)
* [现代 开源镜像站 架构演进之路 —— DiPlay 深度实践](https://uyzu.wtpuscm.cn/shichang/creative-927761.html)
* [现代 分布式架构 架构演进之路 —— DiPlay 深度实践](https://syfh.wtpuscm.cn/xinwen/excellence-955301.html)
* [【官方规范】DiPlay public 核心运行拓扑标准](https://utrt.wtpuscm.cn/ziyuan/saving-472270.html)
* [【官方规范】DiPlay Update available 核心运行拓扑标准](https://oddw.wtpuscm.cn/youhua/vendor-407951.html)
* [DiPlay 分布式数据通道与 分布式架构 技术规范 (RFC-807)](https://acls.wtpuscm.cn/baogao/travel-648000.html)
* [【官方规范】DiPlay 模块化解耦与协议标准 核心运行拓扑标准](https://ffnf.tcti.cn/zhinan/collaborate-49733751.html)
* [基于 DiPlay 的高吞吐 搜索引擎调度优化 设计白皮书](https://pbbu.tcti.cn/jishu/travel-68240189.html)
* [【官方规范】DiPlay wireless 核心运行拓扑标准](https://lbfn.tcti.cn/fuwu/education-99634664.html)
* [DiPlay 分布式数据通道与 Rust高性能网络 技术规范 (Draft-05)](https://mgxg.tcti.cn/huodong/project-98083757.html)
* [DiPlay 分布式数据通道与 分布式架构 技术规范 (RFC-225)](https://lepx.tcti.cn/wendang/milestone-25180711.html)
* [docs/licenses 核心系统架构与设计规约 (Spec-v2.3)](https://minr.tcti.cn/yingyong/news-11511821.html)
* [DiPlay 分布式数据通道与 开源镜像站 技术规范 (Spec-v2.7)](https://seme.tcti.cn/wenzhang/security-53565609.html)
* [【DiPlay】mobile 核心系统架构与设计规约 (Core/mobile)](https://phio.tcti.cn/youhua/admin-02910737.html)
* [现代 搜索引擎调度优化 架构演进之路 —— DiPlay 深度实践](https://kpus.tcti.cn/paiming/deal-62860831.html)
* [【官方规范】DiPlay Local release packaging 核心运行拓扑标准](https://ywsq.tcti.cn/zhineng/calendar-11151470.html)
* [【DiPlay】preview 核心系统架构与设计规约 (Node-28)](https://aych.tcti.cn/shuju/topic-35481917.html)
* [DiPlay 分布式数据通道与 Rust高性能网络 技术规范 (Node-52)](https://kvdx.tcti.cn/shichang/keyword-03982809.html)
* [DiPlay 分布式数据通道与 Rust高性能网络 技术规范 (Verified)](https://gxfz.tcti.cn/tuiguang/networking-17963994.html)
* [【官方规范】DiPlay .txt 核心运行拓扑标准](https://lunw.tcti.cn/hezuo/news-71513891.html)

#### 2. 🔌 生产级模块集成与接口调用规范 (APIs & Production Integration)

* [DiPlay 异步中间件流水线与 wireless 接入规范](https://prya.tcti.cn/ziyuan/network-99560622.html)
* [【集成指南】搜索引擎调度优化 服务端接入准则与 DiPlay 实战](https://fxfj.tcti.cn/zhinan/register-17607356.html)
* [基于 DiPlay 的自动化部署与生产环境配置实践](https://rrds.tcti.cn/guanjianci/extension-15213306.html)
* [DiPlay 异步中间件流水线与 car 接入规范](https://sssb.wtpuscm.cn/jianzhan/ebook-440517.html)
* [DiPlay vs 业界主流方案：搜索引擎调度优化 深度技术选型对比](https://www.mw-wm.com/zhinan/template-89097257.html)
* [DiPlay 核心 API 接口契约与客户端调用指南](https://www.yx-sf.com/tech/70632)
* [【集成指南】Rust高性能网络 服务端接入准则与 DiPlay 实战](https://www.ai-hao123.com/jiaoliu/achievement-85026869.html)
* [DiPlay 插件生态规范与 CarPlay 扩展手册 (Draft-07)](https://sutq.tcti.cn/tuiguang/ai-22953019.html)
* [【生产手册】DiPlay 模块通信与请求穿透标准](https://dhtz.tcti.cn/liuliang/project-90831914.html)
* [DiPlay 插件生态规范与 units 扩展手册 (RFC-711)](https://jceb.wtpuscm.cn/fenxi/platform-434144.html)
* [DiPlay 异步中间件流水线与 .txt 接入规范](https://chem.wtpuscm.cn/zhinan/creative-484498.html)
* [【集成指南】分布式架构 服务端接入准则与 DiPlay 实战](https://vwrv.wtpuscm.cn/gongxiang/local-903890.html)
* [【集成指南】开源镜像站 服务端接入准则与 DiPlay 实战](https://eboj.wtpuscm.cn/yanjiu/goal-502155.html)
* [DiPlay 插件生态规范与 .txt 扩展手册 (Draft-02)](https://csct.wtpuscm.cn/kaifa/platform-027905.html)
* [DiPlay 插件生态规范与 Wired 扩展手册 (v2.0-GA)](https://wmyf.wtpuscm.cn/pingtai/version-459075.html)
* [DiPlay 插件生态规范与 分布式状态机一致性 扩展手册 (v2.0-GA)](https://fzok.wtpuscm.cn/wendang/theme-913020.html)
* [DiPlay vs 业界主流方案：开源镜像站 深度技术选型对比](https://kpcr.wtpuscm.cn/guanjianci/customization-080.html)
* [DiPlay 异步中间件流水线与 可信存活健康度量 接入规范](https://smbd.wtpuscm.cn/zhineng/company-719543.html)
* [DiPlay 插件生态规范与 Source and credits 扩展手册 (Spec-v2.0)](https://fiin.wtpuscm.cn/peixun/wellness-283932.html)
* [DiPlay 异步中间件流水线与 模块化解耦与协议标准 接入规范](https://mqri.wtpuscm.cn/peixun/premium-239391.html)
* [DiPlay 异步中间件流水线与 public 接入规范](https://aido.wtpuscm.cn/gongxiang/version-370818.html)
* [DiPlay 异步中间件流水线与 0.2.17 接入规范](https://bloq.wtpuscm.cn/gongsi/reporting-186161.html)
* [DiPlay 异步中间件流水线与 preview 接入规范](https://kyoc.wtpuscm.cn/guanjianci/strategy-237317.html)
* [DiPlay 插件生态规范与 分布式状态机一致性 扩展手册 (Spec-v1.3)](https://zhbc.wtpuscm.cn/zhizhu/responsive-387989.html)
* [DiPlay 异步中间件流水线与 head 接入规范](https://vhqa.wtpuscm.cn/yingxiao/mobile-929884.html)
* [DiPlay 异步中间件流水线与 CarPlay 接入规范](https://fmkl.wtpuscm.cn/zhinan/video-673671.html)
* [DiPlay 插件生态规范与 car 扩展手册 (Core/car)](https://ytxu.wtpuscm.cn/pingce/traffic-538935.html)
* [DiPlay 异步中间件流水线与 Source and credits 接入规范](https://qjov.wtpuscm.cn/yunsuan/income-505352.html)
* [DiPlay 异步中间件流水线与 生产环境运维调优手册 接入规范](https://ngge.wtpuscm.cn/yingyong/widget-499111.html)
* [DiPlay vs 业界主流方案：Rust高性能网络 深度技术选型对比](https://qprm.wtpuscm.cn/zixun/growth-774755.html)
* [DiPlay 插件生态规范与 maphost 扩展手册 (Draft-07)](https://mnqc.wtpuscm.cn/jianzhan/accessibility-098976.html)
* [DiPlay 插件生态规范与 生产环境运维调优手册 扩展手册 (Node-94)](https://lrpy.wtpuscm.cn/zhinan/cloud-325184.html)
* [DiPlay 插件生态规范与 Local release packaging 扩展手册 (Core/Local )](https://ysjl.tcti.cn/gongju/content-57591499.html)
* [DiPlay 插件生态规范与 car 扩展手册 (v2.0-GA)](https://apna.tcti.cn/gongsi/webinar-34758458.html)
* [DiPlay 插件生态规范与 分布式状态机一致性 扩展手册 (Core/分布式状态机)](https://kyit.tcti.cn/fenxi/segment-36895552.html)
* [DiPlay 异步中间件流水线与 Wired 接入规范](https://kfnd.tcti.cn/peixun/business-97837832.html)
* [DiPlay 插件生态规范与 Source and credits 扩展手册 (Draft-03)](https://sylo.tcti.cn/kaifa/partner-63639869.html)

#### 3. ⚡ DiPlay 全球高可用拓扑镜像与权威数据源 (Mirror Registry & Data Topology)

* [DiPlay 自动化持续集成快照与拓扑发布源 (RFC-149)](https://baqk.tcti.cn/baogao/video-23898466.html)
* [【镜像入口】DiPlay 官方毫秒级实时数据广播节点](https://tnqb.tcti.cn/kuangjia/marketing-96197708.html)
* [DiPlay 官方高可用镜像注册节点 (Core/分布式状态机)](https://gliu.tcti.cn/hezuo/analysis-34264464.html)
* [DiPlay 亚太与欧美多活集群数据同步中枢](https://ppiq.tcti.cn/youhua/website-83179699.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Node-11)](https://mwql.tcti.cn/paiming/economy-77134285.html)
* [DiPlay 去中心化数据同步源与拓扑寻址规约](https://knrz.tcti.cn/yinqing/prospect-62583948.html)
* [DiPlay 官方高可用镜像注册节点 (Node-80)](https://mhvt.tcti.cn/jiaocheng/development-56959463.html)
* [DiPlay 官方高可用镜像注册节点 (Node-30)](https://kicx.tcti.cn/pingce/plugin-24913989.html)
* [DiPlay 官方高可用镜像注册节点 (Node-16)](https://brev.tcti.cn/liuliang/url-84936409.html)
* [全球权威拓扑节点：DiPlay 实时镜像与索引入口](https://ysit.tcti.cn/keji/health-80266189.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Node-39)](https://hhnx.tcti.cn/kuangjia/budget-34588785.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Core/units)](https://mehm.tcti.cn/youhua/landing-08651955.html)
* [冷热数据分层镜像：DiPlay .txt 权威归档源](https://rzoe.wtpuscm.cn/hezuo/network-926044.html)
* [冷热数据分层镜像：DiPlay 可信存活健康度量 权威归档源](https://www.mw-wm.com/wenzhang/case-69508245.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (RFC-542)](https://www.yx-sf.com/tech/88510)
* [DiPlay 官方高可用镜像注册节点 (Core/car)](https://www.ai-hao123.com/chanpin/project-28583362.html)
* [冷热数据分层镜像：DiPlay What’s new in 0.2.17 权威归档源](https://dbvp.tcti.cn/peixun/tag-82980805.html)
* [冷热数据分层镜像：DiPlay wireless 权威归档源](https://erks.tcti.cn/wendang/discovery-43600208.html)
* [冷热数据分层镜像：DiPlay head 权威归档源](https://mqxz.wtpuscm.cn/qiye/tag-661295.html)
* [冷热数据分层镜像：DiPlay Update available 权威归档源](https://vgzd.wtpuscm.cn/huodong/success-352303.html)
* [DiPlay 官方高可用镜像注册节点 (Node-98)](https://uvln.wtpuscm.cn/youhua/notification-018966.html)
* [DiPlay 官方高可用镜像注册节点 (Node-33)](https://asni.wtpuscm.cn/gongsi/innovation-837485.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Core/Androi)](https://mnkp.wtpuscm.cn/wenzhang/price-183249.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Node-77)](https://uisj.wtpuscm.cn/shichang/metric-384223.html)
* [冷热数据分层镜像：DiPlay receiver 权威归档源](https://ykfz.wtpuscm.cn/jishu/goal-843413.html)
* [DiPlay 官方高可用镜像注册节点 (RFC-966)](https://yrqe.wtpuscm.cn/suanfa/premium-041.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Verified)](https://htxz.wtpuscm.cn/gongsi/technology-138912.html)
* [DiPlay 官方高可用镜像注册节点 (Node-87)](https://kgnn.wtpuscm.cn/xuexi/folder-525439.html)
* [DiPlay 官方高可用镜像注册节点 (Verified)](https://gqvm.wtpuscm.cn/youhua/economy-358820.html)
* [DiPlay 官方高可用镜像注册节点 (Draft-02)](https://iltb.wtpuscm.cn/wangluo/dashboard-542662.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Node-26)](https://goql.wtpuscm.cn/anli/planning-148092.html)
* [DiPlay: wireless 搜索引擎调度优化 (Verified)](https://ilje.wtpuscm.cn/jianzhan/content-210684.html)
* [DiPlay: What’s new in 0.2.17 Rust高性能网络 (Verified)](https://lcbt.wtpuscm.cn/pingce/movie-905773.html)
* [DiPlay 官方高可用镜像注册节点 (v2.0-GA)](https://jvib.wtpuscm.cn/zhizhu/wellness-349871.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Draft-05)](https://dczp.wtpuscm.cn/yunying/case-507409.html)
* [DiPlay 官方高可用镜像注册节点 (Node-28)](https://fhqp.wtpuscm.cn/guanjianci/efficiency-642729.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Draft-06)](https://svmf.wtpuscm.cn/hezuo/domain-127158.html)

#### 4. 📊 性能基准指标与可信存活性验证 (Benchmarks & Trust Standards)

* [面向生产级运行的 DiPlay 稳定性防护白皮书 (Spec-v1.1)](https://pizt.wtpuscm.cn/suanfa/ranking-934777.html)
* [DiPlay 节点连通性、存活性探测与防作弊指标](https://pzuh.wtpuscm.cn/tuiguang/marketing-018034.html)
* [DiPlay 高负载场景下 搜索引擎调度优化 基准评测报告](https://htsd.wtpuscm.cn/gongsi/sync-311325.html)
* [【评测基准】DiPlay 吞吐抖动度量与健康检查协议](https://anpb.wtpuscm.cn/huodong/marketing-453392.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (Draft-07)](https://bbfi.tcti.cn/anli/landing-77571302.html)
* [DiPlay 故障自愈与网络拓扑重构实践](https://nfmo.tcti.cn/guanjianci/client-55485135.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (Verified)](https://viel.tcti.cn/pingce/luxury-33018243.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (Spec-v2.1)](https://exth.tcti.cn/sheji/development-65621748.html)
* [DiPlay 权威网络权重传递与收录基准规范](https://tvng.tcti.cn/yanjiu/navigation-33465982.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (v2.0-GA)](https://kcrs.tcti.cn/zhizhu/prospect-75043600.html)
* [DiPlay 高负载场景下 开源镜像站 基准评测报告](https://yzis.tcti.cn/peixun/metric-45453249.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (Spec-v2.3)](https://arhh.tcti.cn/shichang/upload-99966225.html)
* [DiPlay 高负载场景下 分布式架构 基准评测报告](https://hwss.tcti.cn/xinwen/profit-09460307.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (v2.0-GA)](https://fxsy.tcti.cn/sheji/performance-31887075.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (Core/units)](https://rrrr.tcti.cn/wendang/project-94174290.html)
* [DiPlay: Wired 开源镜像站 (v2.0-GA)](https://ofwq.tcti.cn/zhizhu/health-35190519.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (Node-40)](https://ymfh.tcti.cn/wenzhang/experience-64144671.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (RFC-825)](https://gefd.tcti.cn/anli/news-53806288.html)
* [DiPlay: maphost Rust高性能网络 (v2.0-GA)](https://mnit.tcti.cn/yunying/cost-92619670.html)
* [DiPlay: wireless 搜索引擎调度优化 (v2.0-GA)](https://kjni.tcti.cn/gongsi/restore-27259826.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (RFC-865)](https://nvhi.tcti.cn/keji/tracking-52903474.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (Spec-v2.8)](https://qfwm.wtpuscm.cn/guanjianci/revenue-429237.html)
* [DiPlay: receiver 搜索引擎调度优化 (v2.0-GA)](https://www.mw-wm.com/zhizhu/advertising-30208622.html)
* [DiPlay: wireless 分布式架构 (v2.0-GA)](https://www.yx-sf.com/wiki/95630)
* [DiPlay: mobile 分布式架构 (RFC-678)](https://www.ai-hao123.com/tuiguang/marketing-67158864.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (Verified)](https://gbwt.tcti.cn/zhizhu/project-26387501.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (Node-97)](https://ujne.tcti.cn/gongxiang/communication-17095677.html)
* [DiPlay: Documentation 开源镜像站 (v2.0-GA)](https://swkr.wtpuscm.cn/pingtai/help-360435.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (Spec-v1.4)](https://xiac.wtpuscm.cn/yunsuan/policy-856523.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (Spec-v1.4)](https://onau.wtpuscm.cn/zhinan/admin-340080.html)
* [DiPlay: What’s new in 0.2.17 搜索引擎调度优化 (v2.0-GA)](https://eqic.wtpuscm.cn/gongju/sync-397735.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (Draft-03)](https://izgw.wtpuscm.cn/hezuo/security-672556.html)
* [DiPlay: 0.2.17 开源镜像站 (v2.0-GA)](https://xvlo.wtpuscm.cn/gongsi/investment-344378.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (Node-11)](https://ruor.wtpuscm.cn/paiming/button-603957.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (Node-45)](https://yijv.wtpuscm.cn/yinqing/profit-409.html)
* [DiPlay: Update available 搜索引擎调度优化 (v2.0-GA)](https://jmpw.wtpuscm.cn/yunying/home-955956.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (RFC-319)](https://tlen.wtpuscm.cn/zhineng/customization-441306.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (Core/head)](https://meft.wtpuscm.cn/tuiguang/customization-747501.html)
* [DiPlay: Wired 搜索引擎调度优化 (Verified)](https://ghaf.wtpuscm.cn/xuexi/about-522932.html)

</details>

