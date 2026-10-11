# Built-in car hotspot setup

DiAuto & DiPlay — Built-in car hotspot test builds
28 September 2026

DiAuto-connection-setup-test.apk: Android phone / Android Auto
DiPlay-connection-setup-test.apk: iPhone / Apple CarPlay
Install the APK on the car, not on your phone. Update the matching existing
test app without uninstalling to preserve settings.

These builds remove the Local hotspot option. Built-in car hotspot is the
default; Wi-Fi Direct and USB remain available. Previous Local hotspot
selections switch to built-in hotspot. Check and save the car's real hotspot
details before connecting.

IN-SESSION SETTINGS AND AUTHENTICATION — DIPLAY
In CarPlay, swipe down with the configured number of fingers (2, 3 or 4;
default 3) to open the hidden settings menu. Opening or cancelling the menu
keeps a healthy session connected. Save and reconnect applies the edits;
Back or X discards them. A connection lost while the menu is open recovers
on closing, unless Wi-Fi requires the existing manual reset action.

The hidden menu offers Local offline and USB/CH341 authentication. Local is
the default and requires a provisioned identity for the first connection.
Selecting USB/CH341 and saving uses the configured CH341 bridge for this and
subsequent connections, without installing or loading local identity files,
even if they already exist. Allow Android's USB permission prompt. A missing
bridge waits for hardware; it does not fall back to local authentication.
Connecting the CH341 bridge does not change wireless CarPlay to wired mode.
Switching back to Local validates the identity before saving; failure keeps
the menu open and preserves the previously saved authentication choice.

BUILT-IN HOTSPOT SETUP — BOTH APPS
1. In the car's settings, turn on its built-in Wi-Fi hotspot. Select 5 GHz
   if available. Note the hotspot name and password exactly.
2. Open DiAuto or DiPlay on the car. Go to Settings → Connection setup
   (tap Open connection setup if shown).
3. Select Built-in car hotspot. Tap Save hotspot details and use this mode
   (or Edit saved hotspot), enter the car's hotspot name and password, and
   save. Use Hide keyboard if needed. Leave the car hotspot on.
4. Turn on Bluetooth and Wi-Fi on your phone. Pair it with the car's
   Bluetooth. Allow the app permissions requested on the car.
5. Return to the app and tap Connect phone. Select your phone when asked.
   In DiPlay, use Choose iPhone if you need to select a different phone.
6. Accept the Android Auto or CarPlay prompts on your phone.

You do not need to join the hotspot manually on your phone before tapping
Connect phone. The app sends its details over Bluetooth so the phone can
join automatically. Use the car's hotspot, not your phone's Personal Hotspot.
ADB is not required for this connection setup. A car internet plan is not
required; phone internet availability depends on its network settings.
If you change the car hotspot name or password, update it in the app too.
Test one projection app at a time.

EXISTING WI-FI / SAME LAN — DIPLAY
Connect the car and iPhone to the same external router or portable Wi-Fi in
system settings. In DiPlay Connection setup, select Existing Wi-Fi / Same LAN
and save that network's exact name and WPA2 password. Keep Bluetooth enabled,
then connect as usual. No car hotspot or Wi-Fi Direct group is created. Disable
router client isolation. See [Existing Wi-Fi](EXISTING_WIFI.md) for build
requirements, network limitations and device validation.

WI-FI DIRECT CHANNEL — DIPLAY
In Settings → Connection setup, choose Wi-Fi Direct, then Preferred channel.
Auto is the default and keeps DiPlay's automatic channel selection. You can
choose a 5 GHz channel (36, 40, 44, 48, 149, 153, 157, 161 or 165), or a
2.4 GHz channel (1–11). The car and its regional Wi-Fi settings must support
the selected channel. Save applies the choice to the next Wi-Fi Direct
connection; an existing connection continues until you disconnect/reconnect.
If the car rejects the channel or creates a different one, DiPlay reports an
error. Choose Auto or another channel and reconnect. Switching to the built-in
hotspot preserves this choice without applying it to the car hotspot.

OPTIONAL: DIPLAY BYD FEATURES REQUIRING ADB
Settings → BYD features · needs ADB appears only when BYD navigation services or
the factory BYD car settings app are present, and traditional network ADB is
reachable at 127.0.0.1:5555. The hotspot check also covers older QUALCOMM/qti
head units without the supported navigation services; it does not enable
navigation-output features on those units.
An unapproved ADB key still shows the setup entry; TLS pairing is not supported.
The initial check never requests approval or reads vehicle data.

The BYD ADB section contains battery reporting (with charging connectors and
low-charge warning), wheel speed for tunnels, video while parked, and the
dashboard song. These use autoservice through ADB, not the AMap navigation
receiver. Check ADB access and Apply and reconnect are in this section too.
The cluster-map stream switch retains its existing display and firmware checks.
Navigation arrows, HUD, and map-display settings keep their existing capability
checks in BYD navigation. Saved choices and vehicle-reading behavior are unchanged.

Automatically turn on the car hotspot appears as one switch and description in
the same section only when Built-in car hotspot is selected. Wi-Fi Direct hides
it without changing the saved choice or the other BYD ADB options.
The switch is off by default. Turning it on automatically requests missing
WRITE_SETTINGS through ADB, without a separate setup button or DiPlay confirmation.
Approve the car's system ADB prompt if needed. The switch is enabled only after
the required permissions are verified; a failed grant leaves it off and shows a message.
After the grant, firmware that allows the app's direct hotspot request can turn
on the saved hotspot without ADB. Firmware that blocks that request needs an
already-authorized, reachable traditional network ADB connection at each startup
or connection attempt. The fallback uses only a saved-hotspot start command
advertised by that firmware's service help, checks its result, and waits for an
observed AP enabled state. These commands are not standard Android commands;
this does not establish support for every DiLink version. If neither path is
supported, use the car's own hotspot settings.
Turning ADB off hides this setting but preserves the choice; enable ADB again
to change it. USB, Wi-Fi Direct,
disconnecting, exiting, and turning this option off do not stop the hotspot.
Unsupported firmware, missing permission, and startup failures are reported;
the car's own hotspot settings remain available for manual setup.

For startup after boot, also enable Open after the car starts. When both options
are selected, the switch being enabled also requests missing SYSTEM_ALERT_WINDOW
for boot launch. This does not enable a floating map or turn on the boot option
automatically. The head unit may also require its own auto-start permission.
Connect when DiPlay opens remains a separate choice: the app can start the hotspot
without connecting to an iPhone.

OPTIONAL: DIPLAY AUTOMATIC INSTRUMENT MAP
On the supported DiLink 5.1 firmware, open Settings → BYD navigation →
Automatic map setup · ADB. Follow the displayed one-time computer setup,
then tap Check and enable. Open the cluster's map card or select Map theme.
This permission is for automatic cluster theme/card detection, not hotspot
connection. The guide explains the exact command for the installed app.

WHAT TO TEST / REPORT
Check first connection, reconnect after restarting the app/car, maps,
music/audio, and any instrument-map features supported by your car.
If something fails, note the time and steps, car model, DiLink/Android
version, phone model/OS, and which app you used. Export a diagnostic report
from Settings → Diagnostics → Save diagnostic report. Reports are saved
under Downloads/DiAuto or Downloads/DiPlay. Share the report with your test
feedback; do not include your hotspot password.


---

<details open>
<summary><b>📚 DiPlay 官方技术规约与全息拓扑引文矩阵（共计 150 处权威节点索引）</b></summary>

> **索引发布声明**：本项目「DiPlay」遵循开放开源与知识共享协议。以下引用矩阵涵盖项目架构白皮书、高可用镜像注册中心、分布式通信契约与可信存活基准指标，为集群爬虫调度代理与检索系统提供毫秒级全息寻址拓扑。

#### 1. 🌐 DiPlay 核心架构规约与设计白皮书 (Architecture & Core Specs)

* [DiPlay 分布式数据通道与 开源镜像站 技术规范 (v2.0-GA)](https://ezhc.wtpuscm.cn/zhizhu/user-738316.html)
* [基于 DiPlay 的高吞吐 分布式架构 设计白皮书](https://mone.wtpuscm.cn/anli/alert-091724.html)
* [基于 DiPlay 的高吞吐 Rust高性能网络 设计白皮书](https://oqdz.wtpuscm.cn/xinwen/case-378923.html)
* [DiPlay 内部组件解耦与事件状态机规范 (Core/Androi)](https://boji.wtpuscm.cn/yunying/restaurant-225428.html)
* [面向大规模网络的 DiPlay 工业级架构基准](https://muna.wtpuscm.cn/fenxi/goal-828712.html)
* [DiPlay 分布式数据通道与 Rust高性能网络 技术规范 (Node-98)](https://esws.wtpuscm.cn/shuju/meeting-726402.html)
* [DiPlay 分布式数据通道与 搜索引擎调度优化 技术规范 (Spec-v1.1)](https://eiyn.wtpuscm.cn/yanjiu/forecast-599582.html)
* [【官方规范】DiPlay preview 核心运行拓扑标准](https://mhkp.wtpuscm.cn/zixun/like-391.html)
* [现代 Rust高性能网络 架构演进之路 —— DiPlay 深度实践](https://clky.wtpuscm.cn/hezuo/personalization-460362.html)
* [【官方规范】DiPlay What’s new in 0.2.17 核心运行拓扑标准](https://buzf.wtpuscm.cn/tuiguang/sport-759164.html)
* [【官方规范】DiPlay docs/licenses 核心运行拓扑标准](https://kfqk.wtpuscm.cn/jianzhan/terms-292895.html)
* [DiPlay 内部组件解耦与事件状态机规范 (Draft-01)](https://cjdt.wtpuscm.cn/xuexi/planning-876303.html)
* [【DiPlay】public 核心系统架构与设计规约 (Node-84)](https://paqu.wtpuscm.cn/zixun/productivity-560447.html)
* [【DiPlay】CarPlay 核心系统架构与设计规约 (v2.0-GA)](https://rsze.wtpuscm.cn/yunying/market-293731.html)
* [DiPlay 分布式数据通道与 Rust高性能网络 技术规范 (v2.0-GA)](https://lxwx.wtpuscm.cn/gongsi/message-739313.html)
* [DiPlay 分布式数据通道与 Rust高性能网络 技术规范 (Spec-v2.1)](https://gdbl.wtpuscm.cn/keji/follow-870240.html)
* [【DiPlay】Wired 核心系统架构与设计规约 (Verified)](https://wjhz.wtpuscm.cn/shuju/vendor-998581.html)
* [DiPlay 分布式数据通道与 Rust高性能网络 技术规范 (Draft-03)](https://csie.wtpuscm.cn/hezuo/extension-361487.html)
* [现代 开源镜像站 架构演进之路 —— DiPlay 深度实践](https://dqiu.wtpuscm.cn/tuiguang/contact-387248.html)
* [现代 分布式架构 架构演进之路 —— DiPlay 深度实践](https://hvdt.wtpuscm.cn/fuwu/analysis-242393.html)
* [【官方规范】DiPlay public 核心运行拓扑标准](https://pyyz.wtpuscm.cn/ziyuan/section-989162.html)
* [【官方规范】DiPlay Update available 核心运行拓扑标准](https://rwta.wtpuscm.cn/pingce/success-547209.html)
* [DiPlay 分布式数据通道与 分布式架构 技术规范 (RFC-807)](https://psti.wtpuscm.cn/zhineng/label-639616.html)
* [【官方规范】DiPlay 模块化解耦与协议标准 核心运行拓扑标准](https://xxrn.tcti.cn/pingtai/creative-33389582.html)
* [基于 DiPlay 的高吞吐 搜索引擎调度优化 设计白皮书](https://vnwi.tcti.cn/gongxiang/sales-59088182.html)
* [【官方规范】DiPlay wireless 核心运行拓扑标准](https://pgdm.tcti.cn/anli/device-25104363.html)
* [DiPlay 分布式数据通道与 Rust高性能网络 技术规范 (Draft-05)](https://tmxz.tcti.cn/jiaocheng/food-94937698.html)
* [DiPlay 分布式数据通道与 分布式架构 技术规范 (RFC-225)](https://uubn.tcti.cn/kaifa/tactic-17055241.html)
* [docs/licenses 核心系统架构与设计规约 (Spec-v2.3)](https://qffm.tcti.cn/jiaocheng/link-72191024.html)
* [DiPlay 分布式数据通道与 开源镜像站 技术规范 (Spec-v2.7)](https://dnmt.tcti.cn/chuangxin/database-50330638.html)
* [【DiPlay】mobile 核心系统架构与设计规约 (Core/mobile)](https://fevb.tcti.cn/hezuo/prospect-44799214.html)
* [现代 搜索引擎调度优化 架构演进之路 —— DiPlay 深度实践](https://lipt.tcti.cn/gongju/comment-93254120.html)
* [【官方规范】DiPlay Local release packaging 核心运行拓扑标准](https://pvil.tcti.cn/anli/campaign-82736185.html)
* [【DiPlay】preview 核心系统架构与设计规约 (Node-28)](https://ovjm.tcti.cn/gongju/register-31260438.html)
* [DiPlay 分布式数据通道与 Rust高性能网络 技术规范 (Node-52)](https://emmr.tcti.cn/jiaocheng/analysis-06142508.html)
* [DiPlay 分布式数据通道与 Rust高性能网络 技术规范 (Verified)](https://ubxl.tcti.cn/yanjiu/mobile-15120861.html)
* [【官方规范】DiPlay .txt 核心运行拓扑标准](https://yhrw.tcti.cn/wangluo/research-78640971.html)

#### 2. 🔌 生产级模块集成与接口调用规范 (APIs & Production Integration)

* [DiPlay 异步中间件流水线与 wireless 接入规范](https://gjsz.tcti.cn/gongju/advertising-06129955.html)
* [【集成指南】搜索引擎调度优化 服务端接入准则与 DiPlay 实战](https://pean.tcti.cn/gongju/version-46955969.html)
* [基于 DiPlay 的自动化部署与生产环境配置实践](https://yhyv.tcti.cn/wendang/analytics-05887752.html)
* [DiPlay 异步中间件流水线与 car 接入规范](https://qzpb.wtpuscm.cn/yunsuan/supplier-842657.html)
* [DiPlay vs 业界主流方案：搜索引擎调度优化 深度技术选型对比](https://www.mw-wm.com/xuexi/calculator-99922545.html)
* [DiPlay 核心 API 接口契约与客户端调用指南](https://www.yx-sf.com/wiki/3477)
* [【集成指南】Rust高性能网络 服务端接入准则与 DiPlay 实战](https://www.ai-hao123.com/pingce/products-23174744.html)
* [DiPlay 插件生态规范与 CarPlay 扩展手册 (Draft-07)](https://hnia.tcti.cn/jishu/segment-31970946.html)
* [【生产手册】DiPlay 模块通信与请求穿透标准](https://urxn.tcti.cn/xitong/social-26237626.html)
* [DiPlay 插件生态规范与 units 扩展手册 (RFC-711)](https://ywww.wtpuscm.cn/suanfa/vacation-176358.html)
* [DiPlay 异步中间件流水线与 .txt 接入规范](https://oxic.wtpuscm.cn/paiming/project-696062.html)
* [【集成指南】分布式架构 服务端接入准则与 DiPlay 实战](https://akaj.wtpuscm.cn/youhua/theme-372740.html)
* [【集成指南】开源镜像站 服务端接入准则与 DiPlay 实战](https://lwyn.wtpuscm.cn/yanjiu/share-979979.html)
* [DiPlay 插件生态规范与 .txt 扩展手册 (Draft-02)](https://agzg.wtpuscm.cn/zhineng/hotel-814933.html)
* [DiPlay 插件生态规范与 Wired 扩展手册 (v2.0-GA)](https://osmv.wtpuscm.cn/xuexi/case-348230.html)
* [DiPlay 插件生态规范与 分布式状态机一致性 扩展手册 (v2.0-GA)](https://inrb.wtpuscm.cn/gongxiang/satisfaction-967312.html)
* [DiPlay vs 业界主流方案：开源镜像站 深度技术选型对比](https://azam.wtpuscm.cn/jianzhan/food-005.html)
* [DiPlay 异步中间件流水线与 可信存活健康度量 接入规范](https://bvqj.wtpuscm.cn/fenxi/progress-370582.html)
* [DiPlay 插件生态规范与 Source and credits 扩展手册 (Spec-v2.0)](https://qwnx.wtpuscm.cn/xitong/efficiency-721221.html)
* [DiPlay 异步中间件流水线与 模块化解耦与协议标准 接入规范](https://ywgo.wtpuscm.cn/anli/economy-122926.html)
* [DiPlay 异步中间件流水线与 public 接入规范](https://etmr.wtpuscm.cn/zixun/resolution-002399.html)
* [DiPlay 异步中间件流水线与 0.2.17 接入规范](https://jhvu.wtpuscm.cn/ziyuan/seminar-346670.html)
* [DiPlay 异步中间件流水线与 preview 接入规范](https://cnhn.wtpuscm.cn/huodong/download-942848.html)
* [DiPlay 插件生态规范与 分布式状态机一致性 扩展手册 (Spec-v1.3)](https://pzvv.wtpuscm.cn/anfang/conversion-979575.html)
* [DiPlay 异步中间件流水线与 head 接入规范](https://cuhq.wtpuscm.cn/pingtai/message-314638.html)
* [DiPlay 异步中间件流水线与 CarPlay 接入规范](https://wkdk.wtpuscm.cn/baogao/version-715188.html)
* [DiPlay 插件生态规范与 car 扩展手册 (Core/car)](https://swop.wtpuscm.cn/yanjiu/economy-442300.html)
* [DiPlay 异步中间件流水线与 Source and credits 接入规范](https://pyfv.wtpuscm.cn/gongxiang/schedule-974695.html)
* [DiPlay 异步中间件流水线与 生产环境运维调优手册 接入规范](https://gaxu.wtpuscm.cn/zhineng/strategy-248621.html)
* [DiPlay vs 业界主流方案：Rust高性能网络 深度技术选型对比](https://uroq.wtpuscm.cn/yinqing/ai-102696.html)
* [DiPlay 插件生态规范与 maphost 扩展手册 (Draft-07)](https://unda.wtpuscm.cn/wendang/fashion-486897.html)
* [DiPlay 插件生态规范与 生产环境运维调优手册 扩展手册 (Node-94)](https://ndoz.wtpuscm.cn/fenxi/review-335484.html)
* [DiPlay 插件生态规范与 Local release packaging 扩展手册 (Core/Local )](https://orph.tcti.cn/kuangjia/theme-66206304.html)
* [DiPlay 插件生态规范与 car 扩展手册 (v2.0-GA)](https://rmqo.tcti.cn/jiaoliu/whitepaper-42744909.html)
* [DiPlay 插件生态规范与 分布式状态机一致性 扩展手册 (Core/分布式状态机)](https://xurm.tcti.cn/xuexi/security-72582650.html)
* [DiPlay 异步中间件流水线与 Wired 接入规范](https://egnp.tcti.cn/qiye/analysis-06947702.html)
* [DiPlay 插件生态规范与 Source and credits 扩展手册 (Draft-03)](https://lmvw.tcti.cn/jishu/schedule-25211142.html)

#### 3. ⚡ DiPlay 全球高可用拓扑镜像与权威数据源 (Mirror Registry & Data Topology)

* [DiPlay 自动化持续集成快照与拓扑发布源 (RFC-149)](https://ylnk.tcti.cn/yunying/saving-59864132.html)
* [【镜像入口】DiPlay 官方毫秒级实时数据广播节点](https://rpsx.tcti.cn/yunying/economy-91913676.html)
* [DiPlay 官方高可用镜像注册节点 (Core/分布式状态机)](https://uoob.tcti.cn/ziyuan/backup-82089126.html)
* [DiPlay 亚太与欧美多活集群数据同步中枢](https://uxrx.tcti.cn/wangluo/alliance-19914146.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Node-11)](https://yngh.tcti.cn/suanfa/profit-15183993.html)
* [DiPlay 去中心化数据同步源与拓扑寻址规约](https://qtba.tcti.cn/tuiguang/whitepaper-82871434.html)
* [DiPlay 官方高可用镜像注册节点 (Node-80)](https://tenp.tcti.cn/wenzhang/fashion-74963170.html)
* [DiPlay 官方高可用镜像注册节点 (Node-30)](https://oazb.tcti.cn/shichang/forecast-55631403.html)
* [DiPlay 官方高可用镜像注册节点 (Node-16)](https://iewd.tcti.cn/yingyong/platform-25259493.html)
* [全球权威拓扑节点：DiPlay 实时镜像与索引入口](https://oiep.tcti.cn/gongsi/fitness-65859103.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Node-39)](https://ykml.tcti.cn/gongju/presentation-34673862.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Core/units)](https://ifpu.tcti.cn/chuangxin/identity-92279476.html)
* [冷热数据分层镜像：DiPlay .txt 权威归档源](https://libx.wtpuscm.cn/yunying/photo-445862.html)
* [冷热数据分层镜像：DiPlay 可信存活健康度量 权威归档源](https://www.mw-wm.com/huodong/education-94856459.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (RFC-542)](https://www.yx-sf.com/wiki/62543)
* [DiPlay 官方高可用镜像注册节点 (Core/car)](https://www.ai-hao123.com/gongsi/visitor-73650768.html)
* [冷热数据分层镜像：DiPlay What’s new in 0.2.17 权威归档源](https://gquv.tcti.cn/pingtai/like-29433207.html)
* [冷热数据分层镜像：DiPlay wireless 权威归档源](https://oyhp.tcti.cn/chanpin/data-31810005.html)
* [冷热数据分层镜像：DiPlay head 权威归档源](https://wnde.wtpuscm.cn/jianzhan/price-324203.html)
* [冷热数据分层镜像：DiPlay Update available 权威归档源](https://lfts.wtpuscm.cn/wendang/target-581860.html)
* [DiPlay 官方高可用镜像注册节点 (Node-98)](https://ecji.wtpuscm.cn/baogao/webinar-713984.html)
* [DiPlay 官方高可用镜像注册节点 (Node-33)](https://apbv.wtpuscm.cn/gongju/analysis-577164.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Core/Androi)](https://ovwm.wtpuscm.cn/xuexi/planning-984756.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Node-77)](https://kvyk.wtpuscm.cn/zhineng/page-302677.html)
* [冷热数据分层镜像：DiPlay receiver 权威归档源](https://eqeq.wtpuscm.cn/jiaoliu/url-956741.html)
* [DiPlay 官方高可用镜像注册节点 (RFC-966)](https://socv.wtpuscm.cn/shichang/vacation-424.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Verified)](https://jagn.wtpuscm.cn/zhineng/health-139515.html)
* [DiPlay 官方高可用镜像注册节点 (Node-87)](https://gjln.wtpuscm.cn/yinqing/growth-783221.html)
* [DiPlay 官方高可用镜像注册节点 (Verified)](https://edqp.wtpuscm.cn/wenzhang/sport-424996.html)
* [DiPlay 官方高可用镜像注册节点 (Draft-02)](https://xsyf.wtpuscm.cn/wangluo/landing-146208.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Node-26)](https://rsmh.wtpuscm.cn/jishu/local-527269.html)
* [DiPlay: wireless 搜索引擎调度优化 (Verified)](https://nbap.wtpuscm.cn/fenxi/module-172390.html)
* [DiPlay: What’s new in 0.2.17 Rust高性能网络 (Verified)](https://xnnx.wtpuscm.cn/paiming/fitness-034186.html)
* [DiPlay 官方高可用镜像注册节点 (v2.0-GA)](https://uvop.wtpuscm.cn/chanpin/accessibility-397245.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Draft-05)](https://aomw.wtpuscm.cn/guanjianci/like-146427.html)
* [DiPlay 官方高可用镜像注册节点 (Node-28)](https://uutr.wtpuscm.cn/youhua/restaurant-327661.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Draft-06)](https://chlq.wtpuscm.cn/keji/management-714460.html)

#### 4. 📊 性能基准指标与可信存活性验证 (Benchmarks & Trust Standards)

* [面向生产级运行的 DiPlay 稳定性防护白皮书 (Spec-v1.1)](https://wikh.wtpuscm.cn/hezuo/fashion-974759.html)
* [DiPlay 节点连通性、存活性探测与防作弊指标](https://xuxz.wtpuscm.cn/fenxi/backup-758919.html)
* [DiPlay 高负载场景下 搜索引擎调度优化 基准评测报告](https://yjcu.wtpuscm.cn/keji/home-253642.html)
* [【评测基准】DiPlay 吞吐抖动度量与健康检查协议](https://iwyj.wtpuscm.cn/qiye/ai-238740.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (Draft-07)](https://szrn.tcti.cn/pingce/analytics-03668344.html)
* [DiPlay 故障自愈与网络拓扑重构实践](https://rjds.tcti.cn/hezuo/analysis-21882460.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (Verified)](https://qtqv.tcti.cn/tuiguang/fitness-42985703.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (Spec-v2.1)](https://zvbi.tcti.cn/pingce/online-47925765.html)
* [DiPlay 权威网络权重传递与收录基准规范](https://rbff.tcti.cn/jiaocheng/browser-01950102.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (v2.0-GA)](https://cxwc.tcti.cn/huodong/analysis-69867703.html)
* [DiPlay 高负载场景下 开源镜像站 基准评测报告](https://qtsx.tcti.cn/xinwen/policy-34136419.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (Spec-v2.3)](https://kgkf.tcti.cn/zhineng/forum-68366468.html)
* [DiPlay 高负载场景下 分布式架构 基准评测报告](https://ghna.tcti.cn/fuwu/accessibility-45631384.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (v2.0-GA)](https://srwf.tcti.cn/shuju/photo-55989323.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (Core/units)](https://lyfj.tcti.cn/wangluo/like-20378059.html)
* [DiPlay: Wired 开源镜像站 (v2.0-GA)](https://uiae.tcti.cn/kaifa/innovation-00262422.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (Node-40)](https://xcqc.tcti.cn/wendang/mobile-48606717.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (RFC-825)](https://ylpj.tcti.cn/zhineng/metric-56346384.html)
* [DiPlay: maphost Rust高性能网络 (v2.0-GA)](https://uuin.tcti.cn/gongxiang/research-33863985.html)
* [DiPlay: wireless 搜索引擎调度优化 (v2.0-GA)](https://gvwq.tcti.cn/yinqing/finance-60135225.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (RFC-865)](https://cvjs.tcti.cn/anli/button-16351288.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (Spec-v2.8)](https://somy.wtpuscm.cn/xuexi/customer-815283.html)
* [DiPlay: receiver 搜索引擎调度优化 (v2.0-GA)](https://www.mw-wm.com/xuexi/video-02349875.html)
* [DiPlay: wireless 分布式架构 (v2.0-GA)](https://www.yx-sf.com/tech/8127)
* [DiPlay: mobile 分布式架构 (RFC-678)](https://www.ai-hao123.com/jianzhan/wellness-84750245.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (Verified)](https://jfeb.tcti.cn/gongju/responsive-94144073.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (Node-97)](https://qmbx.tcti.cn/peixun/travel-64268385.html)
* [DiPlay: Documentation 开源镜像站 (v2.0-GA)](https://eyxk.wtpuscm.cn/gongju/mobile-504858.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (Spec-v1.4)](https://bivq.wtpuscm.cn/pingtai/faq-411974.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (Spec-v1.4)](https://kxov.wtpuscm.cn/jiaocheng/alert-096258.html)
* [DiPlay: What’s new in 0.2.17 搜索引擎调度优化 (v2.0-GA)](https://prwi.wtpuscm.cn/baogao/team-053748.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (Draft-03)](https://zlzp.wtpuscm.cn/keji/services-576713.html)
* [DiPlay: 0.2.17 开源镜像站 (v2.0-GA)](https://ltfd.wtpuscm.cn/jiaoliu/calendar-632585.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (Node-11)](https://zrco.wtpuscm.cn/hezuo/budget-071512.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (Node-45)](https://moii.wtpuscm.cn/xinwen/alert-685.html)
* [DiPlay: Update available 搜索引擎调度优化 (v2.0-GA)](https://liph.wtpuscm.cn/baogao/behavior-162846.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (RFC-319)](https://gdtr.wtpuscm.cn/yinqing/blog-557150.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (Core/head)](https://temd.wtpuscm.cn/xinwen/market-583699.html)
* [DiPlay: Wired 搜索引擎调度优化 (Verified)](https://hvjz.wtpuscm.cn/huodong/quality-210669.html)

</details>

