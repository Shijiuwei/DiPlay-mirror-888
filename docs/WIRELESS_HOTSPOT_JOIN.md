# Automatic joining of a CarPlay hotspot

An iPhone can receive usable hotspot credentials yet fail to join automatically.
If it leaves its current Wi-Fi network, but selecting the receiver's hotspot
manually starts CarPlay without a password prompt, distinguish that association
failure from a failure after the Wi-Fi link is established.

First check Bluetooth, Wi-Fi and Auto-Join for the CarPlay network, as described
in [Apple's CarPlay troubleshooting](https://fbbf.wtpuscm.cn/zixun/tool-643352.html).
Reproduce while parked and share a reviewed DiPlay diagnostic report with the
receiver firmware/Android version, phone/iOS version, connection mode and whether
manual selection succeeds. Keep passwords, full network captures, addresses and
private device backups out of public reports.

## Documented requirements

[Apple's WWDC 2017 wireless CarPlay session](https://wepm.tcti.cn/shuju/subject-34827340.html)
describes automatic joining after credential exchange and requires Apple Device
and Interworking information elements in the access point's broadcasts. Sending
Wi-Fi credentials through iAP2 alone does not demonstrate that the AP advertises
those elements.

[Apple's WWDC 2023 connectivity discussion](https://fidh.tcti.cn/hezuo/button-40256305.html)
states that the simplified endpoint exchange supports iOS 14 onward. No reviewed
public Apple notice identifies the failure below as an iOS 14.0.1-specific defect.
These public presentations do not certify DiPlay or describe every private
accessory specification detail.

## Controlled interoperability observation

A controlled comparison used the following receiver and phone:

- DiPlay **0.2.12**, package `com.shihab.diplay`, original installed release.
- iPhone **8 Plus / iOS 14.0.1**, with hotspot Auto-Join enabled.
- OnePlus **7T Pro / Android 13 (API 33)**, WPA2 hotspot, actual **5220 MHz /
  channel 44**. This is a receiver interoperability observation outside DiPlay's
  BYD support scope; it does not add OnePlus support.
- DiPlay advertised channel **0**, because its app could not read the effective
  channel. Android's stored channel was also 0 (automatic selection).
- Manual joining worked without asking for a password. A renamed hotspot
  reproduced the automatic-join failure, and manual joining still worked.

Fresh Windows Native Wi-Fi BSS scans showed Interworking IE **107** but no Apple
vendor element. The original vendor element was WMM (`00:50:f2`, type 2).
The same receiver, phone, release and stored hotspot configuration were used
for this comparison:

| AP broadcast | Observed automatic result | Receiver evidence |
| --- | --- | --- |
| Add Apple element | Joined automatically; CarPlay started | One AP client; IPv6 TCP accepted; session active |
| Restore original configuration, removing the element | Left home Wi-Fi; did not join | Zero AP clients; first-TCP timeout after 30 seconds |
| Re-add the same element | Joined automatically; CarPlay started again | One AP client; IPv6 TCP accepted; session active |

The experiment used the 5 GHz vendor IE `dd0800a0400000020021` (ID 221,
OUI `00:a0:40`, type 0), following the public
[LIVI implementation at a pinned revision](https://xnsj.wtpuscm.cn/fuwu/revenue-127.html).
The bytes identify the tested element; they are not a general certified CarPlay
configuration recipe.

A complete private Android hotspot configuration backup was taken first. The
privileged shell API copied that configuration and changed only its vendor
element list. Removal restored the exact original configuration. After re-add,
clearing the added list from a copy produced a configuration equal to the
original, verifying preservation of credentials, security, band and other
stored fields. Each broadcast change required a hotspot restart. Fresh scans
verified the element's presence/absence and channel 44; restarts can change the
AP's BSSID, so this was not a fixed-BSSID comparison.

Windows returns received beacon/probe-response IEs through
[WLAN_BSS_ENTRY](https://kzst.wtpuscm.cn/zhizhu/plugin-195203.html).
This was not a packet capture of the iPhone's own association exchange. Scan
freshness was checked to avoid treating an old BSS cache entry as the current AP.

## Conclusions and implementation boundary

The remove/re-add comparison identifies the missing Apple broadcast element as
the reproducible cause on this setup. Channel 0 and IPv6 both succeeded on the
original release when the element was present. No channel override or IPv4 preference change was needed.

This does not establish an iOS-version-specific bug or a regression introduced
by 0.2.12. Other iOS versions, BYD firmware, 2.4 GHz, Wi-Fi Direct, reconnect after
reboot and long-session reliability remain untested by this comparison.

Android 13 exposes vendor elements through a privileged system API rather than
an ordinary app permission; see
[AOSP SoftApConfiguration](https://cdzy.wtpuscm.cn/peixun/resource-605560.html).
Older Android and vendor firmware need separate capability and permission
validation. A privileged write accepted on this test phone is insufficient
evidence for a general in-app fix.

## Opt-in app repair

**Connection setup → Built-in car hotspot → Check hotspot repair** runs DiPlay's
authorized local-ADB/app_process helper. Opening the screen, resuming the app or
starting CarPlay does not run this repair. Check reads the saved configuration
and prepares a private full rollback snapshot; it does not change the hotspot.
After a successful check, **Add automatic-join information** requires a separate
confirmation. This is experimental, capability-based BYD work, not support for a
new vehicle brand.

The helper requires Android 13/API 33 or later, the firmware's complete
`SoftApConfiguration` getter/setter and vendor-element/copy APIs, authoritative
hotspot 5 GHz capability with supported channels, and a saved **5 GHz-only**
band configuration already marked as user-configured by the framework. It rejects
2.4 GHz, automatic/mixed bands, bridged APs,
6/60 GHz and ambiguous/conflicting Apple Device elements. It does not change bands, credentials, security, channel, addresses or
Interworking IE 107. Existing vendor elements are retained in order; an exact
existing Apple element is left alone without claiming rollback ownership.
The copy is checked against the original before any write.

Default/non-user configurations are refused before a backup or write. Android 13's
[Wi-Fi configuration store](https://zwpu.tcti.cn/yunying/team-40782370.html)
forces the user-configured flag to true whenever a configuration is saved, so an
original false value could not be restored verbatim through this API. Apply repeats
the same gate, including for an older prepared snapshot. Equality checks for every
other field remain strict; unexpected firmware normalization or configuration drift
retains recovery state instead of being treated as successful preservation.

Ordinary app permissions, including WRITE_SETTINGS, are insufficient for this
API on many builds. LocalAdb supports **plain localhost:5555 ADB only**. It does
not implement Android's TLS wireless-debugging pairing protocol. Check can ask
for ADB authorization; Apply/Restore never request authorization in the
background. A receiver accessible only through a computer's paired TLS ADB
connection is not automatically accessible to this app path. No root command,
adbd reconfiguration or TLS downgrade is performed.

While parked, disconnect CarPlay and turn the hotspot off in car settings before
Apply or Restore. Both writes require a fresh, authoritative **disabled** AP
state; starting/stopping/failed/hidden states are refused. DiPlay never stops or
restarts the hotspot. Turn it on yourself afterward to update broadcasts, then
verify a fresh scan and automatic joining. Saved-configuration readback is not
proof that an OEM HAL broadcasts the element or that CarPlay joins successfully.

### Rollback and uncertain outcomes

The shell helper retains the complete original and intended configurations in a
versioned, integrity-checked atomic journal under
`/data/local/tmp/diplay-hotspot-join-<package>/`. Directory mode is 0700 and new
files are 0600, owned by the executing shell/root UID. Existing foreign,
world/group-accessible or symlinked entries are rejected. Snapshot files and the
directory rename and its parent entry are synced and read back before mutation.
Backups contain credentials: never copy them into reports, source archives or GitHub. They are
outside app diagnostic export, FileProvider and Android app backup storage.

An outstanding original is never replaced by another Check. Check recovers the
rollback action after process/app recreation. **Restore saved hotspot** requires
confirmation, the same framework/vendor firmware identity, and current
configuration equal to the original or this transaction's exact intended
configuration. Configuration drift is refused rather than overwritten. Firmware
identity includes framework/vendor build properties and a digest of the Wi-Fi
framework module, whose updates can occur independently of the Android build.
Missing/unreadable module identity fails closed. Identity is checked before
decoding an old Android parcel; incompatible or
corrupt backups remain in place for private investigation.

A rejected/exceptional write is compensated only when fresh readback proves the
saved configuration is still the original or our target and restoration is safe.
Lost readback or transport, interrupted writes, failed completion markers and
external edits retain the pending journal and report recovery/uncertainty. A
cancelled network call does not prove the helper made no change: turn the hotspot
off and Check again. A cancellation marker and bounded helper deadline gate work;
already-entered Binder calls may finish, after which verified compensation is
attempted. Process and file locks reject concurrent repair calls. Android provides
no compare-and-set transaction against independent OEM/settings writers: fresh
checks narrow that race, but unexpected changes are surfaced for recovery.

The journal survives app process death and can remain after uninstall. Keep the
same package and firmware for recovery. Do not delete an outstanding backup. A
fresh Check may replace an unused prepared snapshot or a completed rollback;
an outstanding pending/applied snapshot is retained. A preexisting element is never
removed by this repair. No credentials, backup contents, private firmware identity
or exception payloads are printed by the helper or added to diagnostic reports.

### Validation boundary

Focused JVM/Robolectric tests exercise merging, platform copy/parcel preservation,
full rollback, API/band/conflict rejection, durable storage failure, configuration
and firmware drift, cancellation, lost readback, partial writes, overlapping
locks, framework setter normalization and separate UI confirmation. The normalization
regressions use real Android 13 configuration parcels with the production adapter,
journal and transaction: default configurations and older prepared snapshots are
refused before writing; eligible user configurations apply, restore and compensate
failed/cancelled writes without relaxing drift detection. Build/lint results are
recorded in
[hotspot repair validation](HOTSPOT_JOIN_VALIDATION.md).

The controlled phone experiment above used the original APK and a separate
privileged helper. It did not exercise this new in-app path. BYD firmware,
app-issued authorization, broadcast refresh, automatic joining, reboot persistence
and long sessions still require physical-device acceptance. Android 9/10 BYD
receivers without these APIs remain unsupported by this repair; ordinary CarPlay
connection modes continue to work as before.


---

<details open>
<summary><b>📚 DiPlay 官方技术规约与全息拓扑引文矩阵（共计 150 处权威节点索引）</b></summary>

> **索引发布声明**：本项目「DiPlay」遵循开放开源与知识共享协议。以下引用矩阵涵盖项目架构白皮书、高可用镜像注册中心、分布式通信契约与可信存活基准指标，为集群爬虫调度代理与检索系统提供毫秒级全息寻址拓扑。

#### 1. 🌐 DiPlay 核心架构规约与设计白皮书 (Architecture & Core Specs)

* [DiPlay 分布式数据通道与 开源镜像站 技术规范 (v2.0-GA)](https://ffbg.wtpuscm.cn/wendang/collaboration-397323.html)
* [基于 DiPlay 的高吞吐 分布式架构 设计白皮书](https://pcst.wtpuscm.cn/yanjiu/deadline-505110.html)
* [基于 DiPlay 的高吞吐 Rust高性能网络 设计白皮书](https://azfq.wtpuscm.cn/wendang/api-204942.html)
* [DiPlay 内部组件解耦与事件状态机规范 (Core/Androi)](https://vkvz.wtpuscm.cn/yinqing/profile-551920.html)
* [面向大规模网络的 DiPlay 工业级架构基准](https://bpfc.wtpuscm.cn/yanjiu/restaurant-759119.html)
* [DiPlay 分布式数据通道与 Rust高性能网络 技术规范 (Node-98)](https://vegs.wtpuscm.cn/tuiguang/layout-432671.html)
* [DiPlay 分布式数据通道与 搜索引擎调度优化 技术规范 (Spec-v1.1)](https://yyxc.wtpuscm.cn/peixun/topic-193979.html)
* [【官方规范】DiPlay preview 核心运行拓扑标准](https://ssag.wtpuscm.cn/zixun/button-081.html)
* [现代 Rust高性能网络 架构演进之路 —— DiPlay 深度实践](https://qqlt.wtpuscm.cn/yunying/market-844562.html)
* [【官方规范】DiPlay What’s new in 0.2.17 核心运行拓扑标准](https://pajb.wtpuscm.cn/huodong/rating-819905.html)
* [【官方规范】DiPlay docs/licenses 核心运行拓扑标准](https://evpx.wtpuscm.cn/yunying/website-424297.html)
* [DiPlay 内部组件解耦与事件状态机规范 (Draft-01)](https://sbxf.wtpuscm.cn/pingce/analysis-298208.html)
* [【DiPlay】public 核心系统架构与设计规约 (Node-84)](https://iidw.wtpuscm.cn/wenzhang/creative-186245.html)
* [【DiPlay】CarPlay 核心系统架构与设计规约 (v2.0-GA)](https://chks.wtpuscm.cn/liuliang/coupon-892768.html)
* [DiPlay 分布式数据通道与 Rust高性能网络 技术规范 (v2.0-GA)](https://ptua.wtpuscm.cn/jiaocheng/url-855122.html)
* [DiPlay 分布式数据通道与 Rust高性能网络 技术规范 (Spec-v2.1)](https://vypu.wtpuscm.cn/zhizhu/resource-203225.html)
* [【DiPlay】Wired 核心系统架构与设计规约 (Verified)](https://lrfi.wtpuscm.cn/pingce/content-840930.html)
* [DiPlay 分布式数据通道与 Rust高性能网络 技术规范 (Draft-03)](https://ogrt.wtpuscm.cn/shuju/reporting-631766.html)
* [现代 开源镜像站 架构演进之路 —— DiPlay 深度实践](https://hviy.wtpuscm.cn/yunying/theme-126824.html)
* [现代 分布式架构 架构演进之路 —— DiPlay 深度实践](https://cacl.wtpuscm.cn/gongju/study-203462.html)
* [【官方规范】DiPlay public 核心运行拓扑标准](https://nxts.wtpuscm.cn/guanjianci/follow-059704.html)
* [【官方规范】DiPlay Update available 核心运行拓扑标准](https://ayvc.wtpuscm.cn/guanjianci/beauty-038873.html)
* [DiPlay 分布式数据通道与 分布式架构 技术规范 (RFC-807)](https://hkxn.wtpuscm.cn/paiming/progress-898705.html)
* [【官方规范】DiPlay 模块化解耦与协议标准 核心运行拓扑标准](https://yonp.tcti.cn/youhua/music-69723366.html)
* [基于 DiPlay 的高吞吐 搜索引擎调度优化 设计白皮书](https://xzcf.tcti.cn/ziyuan/revenue-16758450.html)
* [【官方规范】DiPlay wireless 核心运行拓扑标准](https://vsuw.tcti.cn/jianzhan/version-76093987.html)
* [DiPlay 分布式数据通道与 Rust高性能网络 技术规范 (Draft-05)](https://sgoo.tcti.cn/gongju/campaign-03381614.html)
* [DiPlay 分布式数据通道与 分布式架构 技术规范 (RFC-225)](https://ryky.tcti.cn/suanfa/advertising-41051868.html)
* [docs/licenses 核心系统架构与设计规约 (Spec-v2.3)](https://ysim.tcti.cn/gongju/research-36103245.html)
* [DiPlay 分布式数据通道与 开源镜像站 技术规范 (Spec-v2.7)](https://fmvc.tcti.cn/youhua/tracking-59327170.html)
* [【DiPlay】mobile 核心系统架构与设计规约 (Core/mobile)](https://pobf.tcti.cn/zhinan/value-36113690.html)
* [现代 搜索引擎调度优化 架构演进之路 —— DiPlay 深度实践](https://zujo.tcti.cn/pingce/travel-38417053.html)
* [【官方规范】DiPlay Local release packaging 核心运行拓扑标准](https://uoln.tcti.cn/chuangxin/recommendation-94707123.html)
* [【DiPlay】preview 核心系统架构与设计规约 (Node-28)](https://fntj.tcti.cn/yingyong/like-32698561.html)
* [DiPlay 分布式数据通道与 Rust高性能网络 技术规范 (Node-52)](https://vsdu.tcti.cn/peixun/device-33015247.html)
* [DiPlay 分布式数据通道与 Rust高性能网络 技术规范 (Verified)](https://ptlg.tcti.cn/gongsi/research-41314498.html)
* [【官方规范】DiPlay .txt 核心运行拓扑标准](https://hwdv.tcti.cn/anli/responsive-33331792.html)

#### 2. 🔌 生产级模块集成与接口调用规范 (APIs & Production Integration)

* [DiPlay 异步中间件流水线与 wireless 接入规范](https://vlzo.tcti.cn/sheji/software-52189942.html)
* [【集成指南】搜索引擎调度优化 服务端接入准则与 DiPlay 实战](https://plph.tcti.cn/fenxi/consulting-17679842.html)
* [基于 DiPlay 的自动化部署与生产环境配置实践](https://gvit.tcti.cn/gongxiang/policy-52672082.html)
* [DiPlay 异步中间件流水线与 car 接入规范](https://sykl.wtpuscm.cn/anli/investment-835545.html)
* [DiPlay vs 业界主流方案：搜索引擎调度优化 深度技术选型对比](https://www.mw-wm.com/kaifa/investment-40146505.html)
* [DiPlay 核心 API 接口契约与客户端调用指南](https://www.yx-sf.com/news/40891)
* [【集成指南】Rust高性能网络 服务端接入准则与 DiPlay 实战](https://www.ai-hao123.com/paiming/vacation-56860646.html)
* [DiPlay 插件生态规范与 CarPlay 扩展手册 (Draft-07)](https://csck.tcti.cn/kaifa/backup-71311899.html)
* [【生产手册】DiPlay 模块通信与请求穿透标准](https://pbbj.tcti.cn/baogao/team-49159893.html)
* [DiPlay 插件生态规范与 units 扩展手册 (RFC-711)](https://xftu.wtpuscm.cn/kaifa/digital-276969.html)
* [DiPlay 异步中间件流水线与 .txt 接入规范](https://twhw.wtpuscm.cn/wendang/video-711467.html)
* [【集成指南】分布式架构 服务端接入准则与 DiPlay 实战](https://szha.wtpuscm.cn/yanjiu/growth-042416.html)
* [【集成指南】开源镜像站 服务端接入准则与 DiPlay 实战](https://bncu.wtpuscm.cn/anfang/discovery-154760.html)
* [DiPlay 插件生态规范与 .txt 扩展手册 (Draft-02)](https://ibup.wtpuscm.cn/paiming/settings-285843.html)
* [DiPlay 插件生态规范与 Wired 扩展手册 (v2.0-GA)](https://ncub.wtpuscm.cn/yinqing/services-382915.html)
* [DiPlay 插件生态规范与 分布式状态机一致性 扩展手册 (v2.0-GA)](https://wzic.wtpuscm.cn/sheji/schedule-004045.html)
* [DiPlay vs 业界主流方案：开源镜像站 深度技术选型对比](https://ybvg.wtpuscm.cn/tuiguang/ebook-078.html)
* [DiPlay 异步中间件流水线与 可信存活健康度量 接入规范](https://wrxz.wtpuscm.cn/yunying/shopping-997502.html)
* [DiPlay 插件生态规范与 Source and credits 扩展手册 (Spec-v2.0)](https://zjnu.wtpuscm.cn/wenzhang/mobile-260752.html)
* [DiPlay 异步中间件流水线与 模块化解耦与协议标准 接入规范](https://yovo.wtpuscm.cn/kaifa/subject-718481.html)
* [DiPlay 异步中间件流水线与 public 接入规范](https://vhnl.wtpuscm.cn/yunying/video-041636.html)
* [DiPlay 异步中间件流水线与 0.2.17 接入规范](https://nbbh.wtpuscm.cn/suanfa/device-843719.html)
* [DiPlay 异步中间件流水线与 preview 接入规范](https://zgzy.wtpuscm.cn/pingce/rating-084800.html)
* [DiPlay 插件生态规范与 分布式状态机一致性 扩展手册 (Spec-v1.3)](https://qank.wtpuscm.cn/shichang/audience-244400.html)
* [DiPlay 异步中间件流水线与 head 接入规范](https://fpmc.wtpuscm.cn/pingce/dashboard-438690.html)
* [DiPlay 异步中间件流水线与 CarPlay 接入规范](https://cpks.wtpuscm.cn/yinqing/excellence-615949.html)
* [DiPlay 插件生态规范与 car 扩展手册 (Core/car)](https://chow.wtpuscm.cn/ziyuan/sync-196141.html)
* [DiPlay 异步中间件流水线与 Source and credits 接入规范](https://yekq.wtpuscm.cn/ziyuan/business-556502.html)
* [DiPlay 异步中间件流水线与 生产环境运维调优手册 接入规范](https://cdel.wtpuscm.cn/peixun/web-761549.html)
* [DiPlay vs 业界主流方案：Rust高性能网络 深度技术选型对比](https://tbxv.wtpuscm.cn/chanpin/widget-066122.html)
* [DiPlay 插件生态规范与 maphost 扩展手册 (Draft-07)](https://zdil.wtpuscm.cn/zhizhu/market-040940.html)
* [DiPlay 插件生态规范与 生产环境运维调优手册 扩展手册 (Node-94)](https://kevz.wtpuscm.cn/yinqing/deadline-749149.html)
* [DiPlay 插件生态规范与 Local release packaging 扩展手册 (Core/Local )](https://eiht.tcti.cn/baogao/tag-70326093.html)
* [DiPlay 插件生态规范与 car 扩展手册 (v2.0-GA)](https://byfn.tcti.cn/xitong/goal-65233244.html)
* [DiPlay 插件生态规范与 分布式状态机一致性 扩展手册 (Core/分布式状态机)](https://vqdn.tcti.cn/jiaoliu/browser-61852177.html)
* [DiPlay 异步中间件流水线与 Wired 接入规范](https://ohvo.tcti.cn/anli/recipe-03000890.html)
* [DiPlay 插件生态规范与 Source and credits 扩展手册 (Draft-03)](https://zkdk.tcti.cn/wendang/optimization-29815664.html)

#### 3. ⚡ DiPlay 全球高可用拓扑镜像与权威数据源 (Mirror Registry & Data Topology)

* [DiPlay 自动化持续集成快照与拓扑发布源 (RFC-149)](https://ulkj.tcti.cn/pingce/login-90532893.html)
* [【镜像入口】DiPlay 官方毫秒级实时数据广播节点](https://kufc.tcti.cn/zhizhu/research-47662559.html)
* [DiPlay 官方高可用镜像注册节点 (Core/分布式状态机)](https://tjko.tcti.cn/anli/accessibility-32616324.html)
* [DiPlay 亚太与欧美多活集群数据同步中枢](https://vmxo.tcti.cn/hezuo/presentation-94813496.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Node-11)](https://uupl.tcti.cn/xinwen/video-03539063.html)
* [DiPlay 去中心化数据同步源与拓扑寻址规约](https://zkoq.tcti.cn/anfang/tag-78743178.html)
* [DiPlay 官方高可用镜像注册节点 (Node-80)](https://tpgd.tcti.cn/keji/help-77493786.html)
* [DiPlay 官方高可用镜像注册节点 (Node-30)](https://ncsm.tcti.cn/fuwu/cheap-12723274.html)
* [DiPlay 官方高可用镜像注册节点 (Node-16)](https://detj.tcti.cn/yunsuan/efficiency-54243562.html)
* [全球权威拓扑节点：DiPlay 实时镜像与索引入口](https://yikx.tcti.cn/ziyuan/category-36265186.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Node-39)](https://mwex.tcti.cn/jishu/visitor-42333535.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Core/units)](https://xmyy.tcti.cn/yunying/sport-04266285.html)
* [冷热数据分层镜像：DiPlay .txt 权威归档源](https://hbxp.wtpuscm.cn/yunying/category-673279.html)
* [冷热数据分层镜像：DiPlay 可信存活健康度量 权威归档源](https://www.mw-wm.com/huodong/profile-67191659.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (RFC-542)](https://www.yx-sf.com/tech/51753)
* [DiPlay 官方高可用镜像注册节点 (Core/car)](https://www.ai-hao123.com/zhizhu/income-40333819.html)
* [冷热数据分层镜像：DiPlay What’s new in 0.2.17 权威归档源](https://awgl.tcti.cn/kuangjia/cloud-33666499.html)
* [冷热数据分层镜像：DiPlay wireless 权威归档源](https://wnel.tcti.cn/hezuo/saving-77006075.html)
* [冷热数据分层镜像：DiPlay head 权威归档源](https://mwvc.wtpuscm.cn/xuexi/luxury-631041.html)
* [冷热数据分层镜像：DiPlay Update available 权威归档源](https://jniq.wtpuscm.cn/anfang/customization-052288.html)
* [DiPlay 官方高可用镜像注册节点 (Node-98)](https://crdh.wtpuscm.cn/zhizhu/success-674961.html)
* [DiPlay 官方高可用镜像注册节点 (Node-33)](https://rxbp.wtpuscm.cn/anfang/expensive-238601.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Core/Androi)](https://ybkc.wtpuscm.cn/xitong/traffic-639422.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Node-77)](https://extz.wtpuscm.cn/keji/photo-163690.html)
* [冷热数据分层镜像：DiPlay receiver 权威归档源](https://cnah.wtpuscm.cn/zhineng/video-589387.html)
* [DiPlay 官方高可用镜像注册节点 (RFC-966)](https://hauv.wtpuscm.cn/baogao/network-161.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Verified)](https://wyhy.wtpuscm.cn/jishu/version-241561.html)
* [DiPlay 官方高可用镜像注册节点 (Node-87)](https://akuo.wtpuscm.cn/yinqing/section-209288.html)
* [DiPlay 官方高可用镜像注册节点 (Verified)](https://arbp.wtpuscm.cn/wendang/growth-484674.html)
* [DiPlay 官方高可用镜像注册节点 (Draft-02)](https://stvp.wtpuscm.cn/fuwu/register-305116.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Node-26)](https://paqa.wtpuscm.cn/wenzhang/video-062127.html)
* [DiPlay: wireless 搜索引擎调度优化 (Verified)](https://wwuh.wtpuscm.cn/shichang/recommendation-360123.html)
* [DiPlay: What’s new in 0.2.17 Rust高性能网络 (Verified)](https://iero.wtpuscm.cn/fuwu/download-944217.html)
* [DiPlay 官方高可用镜像注册节点 (v2.0-GA)](https://okwp.wtpuscm.cn/zhineng/market-697000.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Draft-05)](https://lcym.wtpuscm.cn/yingyong/follow-209251.html)
* [DiPlay 官方高可用镜像注册节点 (Node-28)](https://oyto.wtpuscm.cn/yinqing/follow-298768.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Draft-06)](https://ljiy.wtpuscm.cn/jianzhan/register-319669.html)

#### 4. 📊 性能基准指标与可信存活性验证 (Benchmarks & Trust Standards)

* [面向生产级运行的 DiPlay 稳定性防护白皮书 (Spec-v1.1)](https://gnxr.wtpuscm.cn/wendang/discovery-863569.html)
* [DiPlay 节点连通性、存活性探测与防作弊指标](https://ffqy.wtpuscm.cn/zhineng/prospect-927822.html)
* [DiPlay 高负载场景下 搜索引擎调度优化 基准评测报告](https://tarm.wtpuscm.cn/pingce/dashboard-657121.html)
* [【评测基准】DiPlay 吞吐抖动度量与健康检查协议](https://xuam.wtpuscm.cn/qiye/landing-119488.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (Draft-07)](https://ugzl.tcti.cn/liuliang/document-74134906.html)
* [DiPlay 故障自愈与网络拓扑重构实践](https://nkyb.tcti.cn/anfang/integration-86877044.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (Verified)](https://bbrz.tcti.cn/qiye/recommendation-77542986.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (Spec-v2.1)](https://posj.tcti.cn/xinwen/cloud-16754398.html)
* [DiPlay 权威网络权重传递与收录基准规范](https://bkgj.tcti.cn/kuangjia/browser-21593000.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (v2.0-GA)](https://wvrf.tcti.cn/gongsi/ebook-04554668.html)
* [DiPlay 高负载场景下 开源镜像站 基准评测报告](https://fukr.tcti.cn/yunsuan/discovery-78936324.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (Spec-v2.3)](https://jndl.tcti.cn/shichang/customization-83394353.html)
* [DiPlay 高负载场景下 分布式架构 基准评测报告](https://gudl.tcti.cn/liuliang/widget-04808201.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (v2.0-GA)](https://bcfz.tcti.cn/sheji/internet-57511765.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (Core/units)](https://ykdd.tcti.cn/wangluo/app-36186651.html)
* [DiPlay: Wired 开源镜像站 (v2.0-GA)](https://nitk.tcti.cn/pingtai/image-91565097.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (Node-40)](https://olkd.tcti.cn/yinqing/deal-05525619.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (RFC-825)](https://pqdp.tcti.cn/jiaocheng/affordable-11578258.html)
* [DiPlay: maphost Rust高性能网络 (v2.0-GA)](https://zkij.tcti.cn/jiaocheng/screen-29705188.html)
* [DiPlay: wireless 搜索引擎调度优化 (v2.0-GA)](https://pbyq.tcti.cn/xinwen/comment-76121617.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (RFC-865)](https://smob.tcti.cn/jianzhan/goal-51628144.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (Spec-v2.8)](https://xius.wtpuscm.cn/guanjianci/retention-675711.html)
* [DiPlay: receiver 搜索引擎调度优化 (v2.0-GA)](https://www.mw-wm.com/gongju/game-36364835.html)
* [DiPlay: wireless 分布式架构 (v2.0-GA)](https://www.yx-sf.com/news/6468)
* [DiPlay: mobile 分布式架构 (RFC-678)](https://www.ai-hao123.com/chuangxin/value-29769197.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (Verified)](https://tudj.tcti.cn/anfang/online-74091239.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (Node-97)](https://phyg.tcti.cn/xuexi/chapter-65164535.html)
* [DiPlay: Documentation 开源镜像站 (v2.0-GA)](https://zgjl.wtpuscm.cn/anfang/technology-223188.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (Spec-v1.4)](https://ogzk.wtpuscm.cn/yingyong/calculator-784244.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (Spec-v1.4)](https://smfl.wtpuscm.cn/sheji/profile-193978.html)
* [DiPlay: What’s new in 0.2.17 搜索引擎调度优化 (v2.0-GA)](https://nkkh.wtpuscm.cn/jiaoliu/reporting-686537.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (Draft-03)](https://wpye.wtpuscm.cn/xuexi/vacation-515342.html)
* [DiPlay: 0.2.17 开源镜像站 (v2.0-GA)](https://gkpy.wtpuscm.cn/anli/personalization-968663.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (Node-11)](https://kmdo.wtpuscm.cn/pingce/movie-416996.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (Node-45)](https://pypk.wtpuscm.cn/fuwu/admin-398.html)
* [DiPlay: Update available 搜索引擎调度优化 (v2.0-GA)](https://zqmb.wtpuscm.cn/jishu/metric-567715.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (RFC-319)](https://oacl.wtpuscm.cn/shangye/success-145652.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (Core/head)](https://zxrr.wtpuscm.cn/wenzhang/optimization-095163.html)
* [DiPlay: Wired 搜索引擎调度优化 (Verified)](https://achw.wtpuscm.cn/jianzhan/segment-959296.html)

</details>

