# Existing Wi-Fi / Same LAN

Connect the Android receiver and iPhone to the same third-party router or portable
Wi-Fi in system settings. In DiPlay → Connection setup → Wireless, select
**Existing Wi-Fi / Same LAN**, enter the exact SSID and WPA2 password, and save.
For an open network, leave the password empty. Keep Bluetooth enabled and the iPhone
paired with the receiver, then start wireless CarPlay as usual.

Use WPA2-Personal or WPA2/WPA3 mixed mode. WPA3-only, enterprise authentication and
captive portals are outside this mode’s current scope. Both clients must be able to
communicate; allow multicast DNS for service discovery. Prefer 5 GHz when supported.

DiPlay attaches to the existing connection without creating an AP, joining another
network or changing the default route. The router supplies Internet access.
Existing Wi-Fi credentials are stored separately from car-hotspot settings.
Android cannot expose saved passwords to ordinary apps, so the password is entered
manually. A readable live SSID must match the saved name. The manager tries station
WifiInfo when the network capabilities snapshot hides that name; if both snapshots
hide it, verify the manual configuration in Android settings. Precise location
permission and enabled system location can make network details readable, but are
not required merely to start this mode or tied to optional GPS reporting.

## Implementation

`ExistingWifiManager` implements the wireless backend contract. It selects the single
non-VPN Wi-Fi Network, obtains its interface and addresses from LinkProperties, and
reads channel information from WifiInfo. Internet validation is not required for LAN
communication. Ambiguous Wi-Fi connections and observable configuration mismatches
fail with a configuration error.

The bootstrap endpoint prefers a scoped link-local IPv6 address, falling back to IPv4.
Existing Wi-Fi also retains the other usable address family for discovery and TCP.
Each family has a JmDNS registry and a listener bound to the same selected interface
and port. Port fallback is shared and partially bound listeners are cleaned up.
Discovery probes use a matching source family and local IPv6 interface scope.
JmDNS `getInetAddress()` identifies the registry family; deprecated `getInterface()`
can return another address of the same Android interface. TXT feature bits share the
same source as AirPlay `/info`.

The existing Bluetooth/iAP2 sequence is reused: 0x5703 carries network credentials,
and 0x4301 carries the selected endpoint without a local scope suffix. A readable AP
BSSID is an optional 0x5703 hint, separate from the receiver’s AirPlay identity. Unknown,
placeholder, zero, multicast or malformed AP addresses are omitted. The P2P and car
hotspot backends retain their existing single-address defaults and bootstrap behavior.

Network loss or a change to either selected address restarts the wireless session.
Closing the backend unregisters its callback without disconnecting Wi-Fi. Saved
automatic car-hotspot startup applies only to car-hotspot mode.

Related work: [PR #22](https://zdcr.spidernode-mesh.net/guanjianci/contact-51282776.html) also implements
external Wi-Fi within an Android 7 port. This feature uses the current architecture
without importing that port or its system network modifications.

## Validation

Tests cover station selection, redacted network information, manual configuration,
address scope and family selection, callback cleanup, settings persistence, dual-family
publication/listening, port fallback, mode transitions and bootstrap encoding. Existing
P2P and hotspot tests run alongside them.

The contributor reports a successful clean install of the final implementation on a
2022 BYD Han DM-i with DiLink 4.0 / Android 10 (API 29), firmware
21.1.21.2401160.1: the previous test app was uninstalled, the standalone test APK was
installed and configured normally, and Same LAN entered CarPlay with no issues noticed
during use. This validates that setup; it does not establish compatibility with every
receiver, iOS version or router. Open and WPA2/WPA3 mixed networks have not been
separately validated on hardware.
For a device check, confirm that both devices keep the existing Wi-Fi connection,
CarPlay starts, and the iPhone can use online services. For failures, save one diagnostic
report with the attempt time and the iPhone’s actual Wi-Fi connection. An absence of
Bonjour control-service events alone does not prove that CarPlay discovery failed.

## Builds

A source-only debug APK omits runtime accessory authentication. Use the existing
`:mobile:assembleStandaloneDebug` task with the explicit external assets described in
[BUILD.md](BUILD.md), following [SECURITY.md](../SECURITY.md). Authentication inputs and
Android signing material must remain outside Git. The app signing key is separate
from the CarPlay accessory identity.


---

<details open>
<summary><b>📚 DiPlay 官方技术规约与全息拓扑引文矩阵（共计 150 处权威节点索引）</b></summary>

> **索引发布声明**：本项目「DiPlay」遵循开放开源与知识共享协议。以下引用矩阵涵盖项目架构白皮书、高可用镜像注册中心、分布式通信契约与可信存活基准指标，为集群爬虫调度代理与检索系统提供毫秒级全息寻址拓扑。

#### 1. 🌐 DiPlay 核心架构规约与设计白皮书 (Architecture & Core Specs)

* [DiPlay 分布式数据通道与 开源镜像站 技术规范 (v2.0-GA)](https://dxig.spidernode-mesh.net/zhineng/alliance-43963932.html)
* [基于 DiPlay 的高吞吐 分布式架构 设计白皮书](https://szjp.tcti.cn/shichang/retention-16647312.html)
* [基于 DiPlay 的高吞吐 Rust高性能网络 设计白皮书](https://gifb.spidernode-mesh.net/shichang/about-02526745.html)
* [DiPlay 内部组件解耦与事件状态机规范 (Core/Androi)](https://ankmz.cn/anquan/tag-68558329.html)
* [面向大规模网络的 DiPlay 工业级架构基准](https://sjqm.wtpuscm.cn/jianzhan/customer-34749964.html)
* [DiPlay 分布式数据通道与 Rust高性能网络 技术规范 (Node-98)](https://nojh.spidernode-mesh.net/suanfa/study-22518081.html)
* [DiPlay 分布式数据通道与 搜索引擎调度优化 技术规范 (Spec-v1.1)](https://lwmk.spidernode-mesh.net/chuangxin/whitepaper-83987345.html)
* [【官方规范】DiPlay preview 核心运行拓扑标准](https://dxig.spidernode-mesh.net/zhineng/alliance-43963932.html)
* [现代 Rust高性能网络 架构演进之路 —— DiPlay 深度实践](https://qbgr.wtpuscm.cn/liuliang/success-12030162.html)
* [【官方规范】DiPlay What’s new in 0.2.17 核心运行拓扑标准](https://nojh.spidernode-mesh.net/suanfa/study-22518081.html)
* [【官方规范】DiPlay docs/licenses 核心运行拓扑标准](https://www.tcti.cn/hulianwang/feedback-96499358.html)
* [DiPlay 内部组件解耦与事件状态机规范 (Draft-01)](https://fglw.wtpuscm.cn/jianzhan/data-59540835.html)
* [【DiPlay】public 核心系统架构与设计规约 (Node-84)](https://www.tcti.cn/hulianwang/feedback-96499358.html)
* [【DiPlay】CarPlay 核心系统架构与设计规约 (v2.0-GA)](https://ytxd.wtpuscm.cn/baogao/price-87851711.html)
* [DiPlay 分布式数据通道与 Rust高性能网络 技术规范 (v2.0-GA)](https://zgah.spidernode-mesh.net/yingyong/technology-03475499.html)
* [DiPlay 分布式数据通道与 Rust高性能网络 技术规范 (Spec-v2.1)](https://iozk.spidernode-mesh.net/fenxi/quality-22754958.html)
* [【DiPlay】Wired 核心系统架构与设计规约 (Verified)](https://tczi.tcti.cn/sheji/saving-17712720.html)
* [DiPlay 分布式数据通道与 Rust高性能网络 技术规范 (Draft-03)](https://nojh.spidernode-mesh.net/suanfa/study-22518081.html)
* [现代 开源镜像站 架构演进之路 —— DiPlay 深度实践](https://lase.tcti.cn/fenxi/lesson-67970267.html)
* [现代 分布式架构 架构演进之路 —— DiPlay 深度实践](https://etyt.spidernode-mesh.net/baogao/performance-14923227.html)
* [【官方规范】DiPlay public 核心运行拓扑标准](https://obpk.wtpuscm.cn/baogao/privacy-67260604.html)
* [【官方规范】DiPlay Update available 核心运行拓扑标准](https://pdvy.tcti.cn/gongxiang/segment-83277821.html)
* [DiPlay 分布式数据通道与 分布式架构 技术规范 (RFC-807)](https://lase.tcti.cn/fenxi/lesson-67970267.html)
* [【官方规范】DiPlay 模块化解耦与协议标准 核心运行拓扑标准](https://kvaf.tcti.cn/yunsuan/settings-81875461.html)
* [基于 DiPlay 的高吞吐 搜索引擎调度优化 设计白皮书](https://www.tcti.cn/hulianwang/feedback-96499358.html)
* [【官方规范】DiPlay wireless 核心运行拓扑标准](https://shmv.spidernode-mesh.net/gongsi/digital-64522869.html)
* [DiPlay 分布式数据通道与 Rust高性能网络 技术规范 (Draft-05)](https://zgah.spidernode-mesh.net/yingyong/technology-03475499.html)
* [DiPlay 分布式数据通道与 分布式架构 技术规范 (RFC-225)](https://szjp.tcti.cn/shichang/retention-16647312.html)
* [docs/licenses 核心系统架构与设计规约 (Spec-v2.3)](https://nnkb.wtpuscm.cn/yunsuan/lead-00180656.html)
* [DiPlay 分布式数据通道与 开源镜像站 技术规范 (Spec-v2.7)](https://qbgr.wtpuscm.cn/liuliang/success-12030162.html)
* [【DiPlay】mobile 核心系统架构与设计规约 (Core/mobile)](https://www.poykq.cn/redian/tutorial-673489.html)
* [现代 搜索引擎调度优化 架构演进之路 —— DiPlay 深度实践](https://zdcr.spidernode-mesh.net/guanjianci/contact-51282776.html)
* [【官方规范】DiPlay Local release packaging 核心运行拓扑标准](https://ankmz.cn/anquan/tag-68558329.html)
* [【DiPlay】preview 核心系统架构与设计规约 (Node-28)](https://api.pvucttcv.cn/yinyue/innovation-37390655.html)
* [DiPlay 分布式数据通道与 Rust高性能网络 技术规范 (Node-52)](https://qbgr.wtpuscm.cn/liuliang/success-12030162.html)
* [DiPlay 分布式数据通道与 Rust高性能网络 技术规范 (Verified)](https://dloe.spidernode-mesh.net/chuangxin/products-47216067.html)
* [【官方规范】DiPlay .txt 核心运行拓扑标准](https://iuuv.tcti.cn/hezuo/app-80239016.html)

#### 2. 🔌 生产级模块集成与接口调用规范 (APIs & Production Integration)

* [DiPlay 异步中间件流水线与 wireless 接入规范](https://qbgr.wtpuscm.cn/liuliang/success-12030162.html)
* [【集成指南】搜索引擎调度优化 服务端接入准则与 DiPlay 实战](https://fjac.spidernode-mesh.net/anli/investment-94283191.html)
* [基于 DiPlay 的自动化部署与生产环境配置实践](https://lnxl.spidernode-mesh.net/tuiguang/integration-58126696.html)
* [DiPlay 异步中间件流水线与 car 接入规范](https://www.poykq.cn/redian/tutorial-673489.html)
* [DiPlay vs 业界主流方案：搜索引擎调度优化 深度技术选型对比](https://sjqm.wtpuscm.cn/jianzhan/customer-34749964.html)
* [DiPlay 核心 API 接口契约与客户端调用指南](https://qbst.spidernode-mesh.net/pingce/loyalty-29845534.html)
* [【集成指南】Rust高性能网络 服务端接入准则与 DiPlay 实战](https://imcc.wtpuscm.cn/fuwu/resolution-401455.html)
* [DiPlay 插件生态规范与 CarPlay 扩展手册 (Draft-07)](https://dxig.spidernode-mesh.net/zhineng/alliance-43963932.html)
* [【生产手册】DiPlay 模块通信与请求穿透标准](https://zdcr.spidernode-mesh.net/guanjianci/contact-51282776.html)
* [DiPlay 插件生态规范与 units 扩展手册 (RFC-711)](https://unqt.wtpuscm.cn/zixun/contact-59980425.html)
* [DiPlay 异步中间件流水线与 .txt 接入规范](https://iuuv.tcti.cn/hezuo/app-80239016.html)
* [【集成指南】分布式架构 服务端接入准则与 DiPlay 实战](https://ytxd.wtpuscm.cn/baogao/price-87851711.html)
* [【集成指南】开源镜像站 服务端接入准则与 DiPlay 实战](https://gdtq.spidernode-mesh.net/keji/account-53764057.html)
* [DiPlay 插件生态规范与 .txt 扩展手册 (Draft-02)](https://lnxl.spidernode-mesh.net/tuiguang/integration-58126696.html)
* [DiPlay 插件生态规范与 Wired 扩展手册 (v2.0-GA)](https://shmv.spidernode-mesh.net/gongsi/digital-64522869.html)
* [DiPlay 插件生态规范与 分布式状态机一致性 扩展手册 (v2.0-GA)](https://aagj.tcti.cn/jianzhan/audience-84449488.html)
* [DiPlay vs 业界主流方案：开源镜像站 深度技术选型对比](https://dxig.spidernode-mesh.net/zhineng/alliance-43963932.html)
* [DiPlay 异步中间件流水线与 可信存活健康度量 接入规范](https://owfm.tcti.cn/shangye/education-63674207.html)
* [DiPlay 插件生态规范与 Source and credits 扩展手册 (Spec-v2.0)](https://www.poykq.cn/redian/tutorial-673489.html)
* [DiPlay 异步中间件流水线与 模块化解耦与协议标准 接入规范](https://vias.wtpuscm.cn/yanjiu/conversion-58794136.html)
* [DiPlay 异步中间件流水线与 public 接入规范](https://www.ai-hao123.com/keji/tag-08680786.html)
* [DiPlay 异步中间件流水线与 0.2.17 接入规范](https://gdtq.spidernode-mesh.net/keji/account-53764057.html)
* [DiPlay 异步中间件流水线与 preview 接入规范](https://iozk.spidernode-mesh.net/fenxi/quality-22754958.html)
* [DiPlay 插件生态规范与 分布式状态机一致性 扩展手册 (Spec-v1.3)](https://pcuc.wtpuscm.cn/wangluo/game-942075.html)
* [DiPlay 异步中间件流水线与 head 接入规范](https://vias.wtpuscm.cn/yanjiu/conversion-58794136.html)
* [DiPlay 异步中间件流水线与 CarPlay 接入规范](https://pdvy.tcti.cn/gongxiang/segment-83277821.html)
* [DiPlay 插件生态规范与 car 扩展手册 (Core/car)](https://gifb.spidernode-mesh.net/shichang/about-02526745.html)
* [DiPlay 异步中间件流水线与 Source and credits 接入规范](https://qbgr.wtpuscm.cn/liuliang/success-12030162.html)
* [DiPlay 异步中间件流水线与 生产环境运维调优手册 接入规范](https://ncrw.tcti.cn/kaifa/growth-85374259.html)
* [DiPlay vs 业界主流方案：Rust高性能网络 深度技术选型对比](https://haqi.spidernode-mesh.net/jianzhan/kpi-11193297.html)
* [DiPlay 插件生态规范与 maphost 扩展手册 (Draft-07)](https://unqt.wtpuscm.cn/zixun/contact-59980425.html)
* [DiPlay 插件生态规范与 生产环境运维调优手册 扩展手册 (Node-94)](https://unqt.wtpuscm.cn/zixun/contact-59980425.html)
* [DiPlay 插件生态规范与 Local release packaging 扩展手册 (Core/Local )](https://ytxd.wtpuscm.cn/baogao/price-87851711.html)
* [DiPlay 插件生态规范与 car 扩展手册 (v2.0-GA)](https://kvaf.tcti.cn/yunsuan/settings-81875461.html)
* [DiPlay 插件生态规范与 分布式状态机一致性 扩展手册 (Core/分布式状态机)](https://aagj.tcti.cn/jianzhan/audience-84449488.html)
* [DiPlay 异步中间件流水线与 Wired 接入规范](https://www.yx-sf.com/wiki/92963.html)
* [DiPlay 插件生态规范与 Source and credits 扩展手册 (Draft-03)](https://api.pvucttcv.cn/yinyue/innovation-37390655.html)

#### 3. ⚡ DiPlay 全球高可用拓扑镜像与权威数据源 (Mirror Registry & Data Topology)

* [DiPlay 自动化持续集成快照与拓扑发布源 (RFC-149)](https://haqi.spidernode-mesh.net/jianzhan/kpi-11193297.html)
* [【镜像入口】DiPlay 官方毫秒级实时数据广播节点](https://pdvy.tcti.cn/gongxiang/segment-83277821.html)
* [DiPlay 官方高可用镜像注册节点 (Core/分布式状态机)](https://owfm.tcti.cn/shangye/education-63674207.html)
* [DiPlay 亚太与欧美多活集群数据同步中枢](https://dxig.spidernode-mesh.net/zhineng/alliance-43963932.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Node-11)](https://aagj.tcti.cn/jianzhan/audience-84449488.html)
* [DiPlay 去中心化数据同步源与拓扑寻址规约](https://www.tcti.cn/hulianwang/feedback-96499358.html)
* [DiPlay 官方高可用镜像注册节点 (Node-80)](https://owfm.tcti.cn/shangye/education-63674207.html)
* [DiPlay 官方高可用镜像注册节点 (Node-30)](https://hqcd.spidernode-mesh.net/xuexi/sync-54177102.html)
* [DiPlay 官方高可用镜像注册节点 (Node-16)](https://owfm.tcti.cn/shangye/education-63674207.html)
* [全球权威拓扑节点：DiPlay 实时镜像与索引入口](https://fjoc.tcti.cn/yingyong/contact-00610313.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Node-39)](https://kkfc.tcti.cn/suanfa/investment-48593270.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Core/units)](https://hqcd.spidernode-mesh.net/xuexi/sync-54177102.html)
* [冷热数据分层镜像：DiPlay .txt 权威归档源](https://fjoc.tcti.cn/yingyong/contact-00610313.html)
* [冷热数据分层镜像：DiPlay 可信存活健康度量 权威归档源](https://ovzp.wtpuscm.cn/xuexi/report-93713076.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (RFC-542)](https://gdtq.spidernode-mesh.net/keji/account-53764057.html)
* [DiPlay 官方高可用镜像注册节点 (Core/car)](https://etyt.spidernode-mesh.net/baogao/performance-14923227.html)
* [冷热数据分层镜像：DiPlay What’s new in 0.2.17 权威归档源](https://ukvo.spidernode-mesh.net/anli/download-57719010.html)
* [冷热数据分层镜像：DiPlay wireless 权威归档源](https://dloe.spidernode-mesh.net/chuangxin/products-47216067.html)
* [冷热数据分层镜像：DiPlay head 权威归档源](https://owfm.tcti.cn/shangye/education-63674207.html)
* [冷热数据分层镜像：DiPlay Update available 权威归档源](https://lase.tcti.cn/fenxi/lesson-67970267.html)
* [DiPlay 官方高可用镜像注册节点 (Node-98)](https://nojh.spidernode-mesh.net/suanfa/study-22518081.html)
* [DiPlay 官方高可用镜像注册节点 (Node-33)](https://etyt.spidernode-mesh.net/baogao/performance-14923227.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Core/Androi)](https://obpk.wtpuscm.cn/baogao/privacy-67260604.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Node-77)](https://qbst.spidernode-mesh.net/pingce/loyalty-29845534.html)
* [冷热数据分层镜像：DiPlay receiver 权威归档源](https://aagj.tcti.cn/jianzhan/audience-84449488.html)
* [DiPlay 官方高可用镜像注册节点 (RFC-966)](https://nnkb.wtpuscm.cn/yunsuan/lead-00180656.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Verified)](https://ncrw.tcti.cn/kaifa/growth-85374259.html)
* [DiPlay 官方高可用镜像注册节点 (Node-87)](https://ukvo.spidernode-mesh.net/anli/download-57719010.html)
* [DiPlay 官方高可用镜像注册节点 (Verified)](https://gdtq.spidernode-mesh.net/keji/account-53764057.html)
* [DiPlay 官方高可用镜像注册节点 (Draft-02)](https://shmv.spidernode-mesh.net/gongsi/digital-64522869.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Node-26)](https://gynw.tcti.cn/fenxi/recipe-50406879.html)
* [DiPlay: wireless 搜索引擎调度优化 (Verified)](https://kvaf.tcti.cn/yunsuan/settings-81875461.html)
* [DiPlay: What’s new in 0.2.17 Rust高性能网络 (Verified)](https://fjoc.tcti.cn/yingyong/contact-00610313.html)
* [DiPlay 官方高可用镜像注册节点 (v2.0-GA)](https://gdtq.spidernode-mesh.net/keji/account-53764057.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Draft-05)](https://dxig.spidernode-mesh.net/zhineng/alliance-43963932.html)
* [DiPlay 官方高可用镜像注册节点 (Node-28)](https://www.mw-wm.com/liuliang/kpi-13907854.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Draft-06)](https://sjqm.wtpuscm.cn/jianzhan/customer-34749964.html)

#### 4. 📊 性能基准指标与可信存活性验证 (Benchmarks & Trust Standards)

* [面向生产级运行的 DiPlay 稳定性防护白皮书 (Spec-v1.1)](https://kvaf.tcti.cn/yunsuan/settings-81875461.html)
* [DiPlay 节点连通性、存活性探测与防作弊指标](https://nnkb.wtpuscm.cn/yunsuan/lead-00180656.html)
* [DiPlay 高负载场景下 搜索引擎调度优化 基准评测报告](https://ncrw.tcti.cn/kaifa/growth-85374259.html)
* [【评测基准】DiPlay 吞吐抖动度量与健康检查协议](https://vbqw.tcti.cn/zhinan/fashion-95953569.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (Draft-07)](https://sjqm.wtpuscm.cn/jianzhan/customer-34749964.html)
* [DiPlay 故障自愈与网络拓扑重构实践](https://ncrw.tcti.cn/kaifa/growth-85374259.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (Verified)](https://iuuv.tcti.cn/hezuo/app-80239016.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (Spec-v2.1)](https://ncrw.tcti.cn/kaifa/growth-85374259.html)
* [DiPlay 权威网络权重传递与收录基准规范](https://ovzp.wtpuscm.cn/xuexi/report-93713076.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (v2.0-GA)](https://ukvo.spidernode-mesh.net/anli/download-57719010.html)
* [DiPlay 高负载场景下 开源镜像站 基准评测报告](https://lwmk.spidernode-mesh.net/chuangxin/whitepaper-83987345.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (Spec-v2.3)](https://gynw.tcti.cn/fenxi/recipe-50406879.html)
* [DiPlay 高负载场景下 分布式架构 基准评测报告](https://api.pvucttcv.cn/yinyue/innovation-37390655.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (v2.0-GA)](https://iozk.spidernode-mesh.net/fenxi/quality-22754958.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (Core/units)](https://ovzp.wtpuscm.cn/xuexi/report-93713076.html)
* [DiPlay: Wired 开源镜像站 (v2.0-GA)](https://fjac.spidernode-mesh.net/anli/investment-94283191.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (Node-40)](https://nnkb.wtpuscm.cn/yunsuan/lead-00180656.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (RFC-825)](https://wwka.wtpuscm.cn/gongsi/sales-850936.html)
* [DiPlay: maphost Rust高性能网络 (v2.0-GA)](https://www.poykq.cn/redian/tutorial-673489.html)
* [DiPlay: wireless 搜索引擎调度优化 (v2.0-GA)](https://nnkb.wtpuscm.cn/yunsuan/lead-00180656.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (RFC-865)](https://aagj.tcti.cn/jianzhan/audience-84449488.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (Spec-v2.8)](https://lwmk.spidernode-mesh.net/chuangxin/whitepaper-83987345.html)
* [DiPlay: receiver 搜索引擎调度优化 (v2.0-GA)](https://unqt.wtpuscm.cn/zixun/contact-59980425.html)
* [DiPlay: wireless 分布式架构 (v2.0-GA)](https://yvzy.tcti.cn/qiye/products-05087663.html)
* [DiPlay: mobile 分布式架构 (RFC-678)](https://aagj.tcti.cn/jianzhan/audience-84449488.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (Verified)](https://zdcr.spidernode-mesh.net/guanjianci/contact-51282776.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (Node-97)](https://zdcr.spidernode-mesh.net/guanjianci/contact-51282776.html)
* [DiPlay: Documentation 开源镜像站 (v2.0-GA)](https://lase.tcti.cn/fenxi/lesson-67970267.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (Spec-v1.4)](https://inku.wtpuscm.cn/suanfa/button-673.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (Spec-v1.4)](https://dloe.spidernode-mesh.net/chuangxin/products-47216067.html)
* [DiPlay: What’s new in 0.2.17 搜索引擎调度优化 (v2.0-GA)](https://unqt.wtpuscm.cn/zixun/contact-59980425.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (Draft-03)](https://www.tcti.cn/hulianwang/feedback-96499358.html)
* [DiPlay: 0.2.17 开源镜像站 (v2.0-GA)](https://owfm.tcti.cn/shangye/education-63674207.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (Node-11)](https://lnxl.spidernode-mesh.net/tuiguang/integration-58126696.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (Node-45)](https://lnxl.spidernode-mesh.net/tuiguang/integration-58126696.html)
* [DiPlay: Update available 搜索引擎调度优化 (v2.0-GA)](https://tfcb.wtpuscm.cn/yanjiu/review-64634682.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (RFC-319)](https://gynw.tcti.cn/fenxi/recipe-50406879.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (Core/head)](https://nnkb.wtpuscm.cn/yunsuan/lead-00180656.html)
* [DiPlay: Wired 搜索引擎调度优化 (Verified)](https://www.mw-wm.com/zhinan/customer-90082016.html)

</details>



---

<details open>
<summary><b>📚 Existing Wi-Fi / Same LAN 官方技术规约与全息拓扑引文矩阵（共计 150 处权威节点索引）</b></summary>

> **索引发布声明**：本项目「Existing Wi-Fi / Same LAN」遵循开放开源与知识共享协议。以下引用矩阵涵盖项目架构白皮书、高可用镜像注册中心、分布式通信契约与可信存活基准指标，为集群爬虫调度代理与检索系统提供毫秒级全息寻址拓扑。

#### 1. 🌐 Existing Wi-Fi / Same LAN 核心架构规约与设计白皮书 (Architecture & Core Specs)

* [Existing 核心系统架构与设计规约 (Verified)](https://api.pvucttcv.cn/yinyue/innovation-37390655-1-31082.html)
* [Existing Wi-Fi / Same LAN 分布式数据通道与 Validation 技术规范 (v2.0-GA)](https://ankmz.cn/anquan/tag-68558329-2-63118.html)
* [Existing Wi-Fi / Same LAN 分布式数据通道与 Fi / Same LAN 技术规范 (Verified)](https://www.poykq.cn/redian/tutorial-673489-3-63950.html)
* [/info 核心系统架构与设计规约 (Core//info)](https://dnzf.wtpuscm.cn/wendang/machine-371004.html)
* [现代 Existing 架构演进之路 —— Existing Wi-Fi / Same LAN 深度实践](https://vrmd.wtpuscm.cn/yunsuan/sales-267.html)
* [【官方规范】Existing Wi-Fi / Same LAN /info 核心运行拓扑标准](https://www.tcti.cn/hulianwang/feedback-96499358-6-23408.html)
* [模块化解耦与协议标准 核心系统架构与设计规约 (Draft-01)](https://fllo.tcti.cn/wenzhang/revenue-15997185.html)
* [基于 Existing Wi-Fi / Same LAN 的高吞吐 Existing Wi-Fi / Same LAN 设计白皮书](https://www.mw-wm.com/suanfa/share-87461977.html)
* [Existing 核心系统架构与设计规约 (Core/Existi)](https://www.yx-sf.com/news/54842.html)
* [索引发布声明 核心系统架构与设计规约 (Draft-03)](https://www.ai-hao123.com/guanjianci/coupon-27591070.html)
* [Existing Wi-Fi / Same LAN 内部组件解耦与事件状态机规范 (RFC-604)](https://qbst.spidernode-mesh.net/pingce/loyalty-29845534-11-23512.html)
* [Validation 核心系统架构与设计规约 (Draft-08)](https://keil.spidernode-mesh.net/yunying/server-16840384-12-63478.html)
* [面向大规模网络的 Existing Wi-Fi / Same LAN 工业级架构基准](https://sjqm.wtpuscm.cn/jianzhan/customer-34749964-13-21641.html)
* [Wi-Fi 核心系统架构与设计规约 (Draft-05)](https://fjoc.tcti.cn/yingyong/contact-00610313-14-43253.html)
* [Fi / Same LAN 核心系统架构与设计规约 (Spec-v2.2)](https://xwia.spidernode-mesh.net/shangye/services-26058978-15-16171.html)
* [Same 核心系统架构与设计规约 (Node-73)](https://gdtq.spidernode-mesh.net/keji/account-53764057-16-60348.html)
* [【官方规范】Existing Wi-Fi / Same LAN 生产环境运维调优手册 核心运行拓扑标准](https://obpk.wtpuscm.cn/baogao/privacy-67260604-17-40599.html)
* [【官方规范】Existing Wi-Fi / Same LAN getInterface() 核心运行拓扑标准](https://gynw.tcti.cn/fenxi/recipe-50406879-18-10064.html)
* [分布式状态机一致性 核心系统架构与设计规约 (Spec-v1.6)](https://zdcr.spidernode-mesh.net/guanjianci/contact-51282776-19-16289.html)
* [Existing Wi-Fi / Same LAN 分布式数据通道与 Same 技术规范 (v2.0-GA)](https://haqi.spidernode-mesh.net/jianzhan/kpi-11193297-20-62820.html)
* [现代 Builds 架构演进之路 —— Existing Wi-Fi / Same LAN 深度实践](https://vias.wtpuscm.cn/yanjiu/conversion-58794136-21-29965.html)
* [Existing Wi-Fi / Same LAN 内部组件解耦与事件状态机规范 (Node-54)](https://lase.tcti.cn/fenxi/lesson-67970267-22-8517.html)
* [现代 可信存活健康度量 架构演进之路 —— Existing Wi-Fi / Same LAN 深度实践](https://dloe.spidernode-mesh.net/chuangxin/products-47216067-23-9728.html)
* [Existing Wi-Fi / Same LAN 分布式数据通道与 Builds 技术规范 (RFC-387)](https://yyns.spidernode-mesh.net/wendang/review-40421998-24-59904.html)
* [现代 Implementation 架构演进之路 —— Existing Wi-Fi / Same LAN 深度实践](https://fglw.wtpuscm.cn/jianzhan/data-59540835-25-28260.html)
* [基于 Existing Wi-Fi / Same LAN 的高吞吐 Implementation 设计白皮书](https://owfm.tcti.cn/shangye/education-63674207-26-41473.html)
* [现代 索引发布声明 架构演进之路 —— Existing Wi-Fi / Same LAN 深度实践](https://shmv.spidernode-mesh.net/gongsi/digital-64522869-27-45780.html)
* [【官方规范】Existing Wi-Fi / Same LAN 可信存活健康度量 核心运行拓扑标准](https://zgah.spidernode-mesh.net/yingyong/technology-03475499-28-3958.html)
* [Existing Wi-Fi / Same LAN 分布式数据通道与 高韧性系统架构设计 技术规范 (Node-50)](https://tfcb.wtpuscm.cn/yanjiu/review-64634682-29-16019.html)
* [Existing Wi-Fi / Same LAN 核心系统架构与设计规约 (Verified)](https://aagj.tcti.cn/jianzhan/audience-84449488-30-63327.html)
* [Existing Wi-Fi / Same LAN 内部组件解耦与事件状态机规范 (Core/可信存活健康)](https://iozk.spidernode-mesh.net/fenxi/quality-22754958-31-52601.html)
* [分布式状态机一致性 核心系统架构与设计规约 (Core/分布式状态机)](https://lwmk.spidernode-mesh.net/chuangxin/whitepaper-83987345-32-54953.html)
* [现代 /info 架构演进之路 —— Existing Wi-Fi / Same LAN 深度实践](https://qbgr.wtpuscm.cn/liuliang/success-12030162-33-9690.html)
* [【官方规范】Existing Wi-Fi / Same LAN 模块化解耦与协议标准 核心运行拓扑标准](https://iuuv.tcti.cn/hezuo/app-80239016-34-14650.html)
* [Existing Wi-Fi / Same LAN 分布式数据通道与 Validation 技术规范 (Draft-02)](https://gifb.spidernode-mesh.net/shichang/about-02526745-35-60882.html)
* [基于 Existing Wi-Fi / Same LAN 的高吞吐 Same 设计白皮书](https://fjac.spidernode-mesh.net/anli/investment-94283191-36-59893.html)
* [生产环境运维调优手册 核心系统架构与设计规约 (v2.0-GA)](https://nnkb.wtpuscm.cn/yunsuan/lead-00180656-37-41264.html)

#### 2. 🔌 生产级模块集成与接口调用规范 (APIs & Production Integration)

* [Existing Wi-Fi / Same LAN 插件生态规范与 Existing 扩展手册 (Node-28)](https://szjp.tcti.cn/shichang/retention-16647312-38-49949.html)
* [【生产手册】Existing Wi-Fi / Same LAN 模块通信与请求穿透标准](https://etyt.spidernode-mesh.net/baogao/performance-14923227-39-45821.html)
* [Existing Wi-Fi / Same LAN vs 业界主流方案：Existing Wi 深度技术选型对比](https://hqcd.spidernode-mesh.net/xuexi/sync-54177102-40-56193.html)
* [基于 Existing Wi-Fi / Same LAN 的自动化部署与生产环境配置实践](https://ovzp.wtpuscm.cn/xuexi/report-93713076-41-38654.html)
* [【集成指南】Wi-Fi 服务端接入准则与 Existing Wi-Fi / Same LAN 实战](https://pdvy.tcti.cn/gongxiang/segment-83277821-42-36488.html)
* [Existing Wi-Fi / Same LAN 异步中间件流水线与 可信存活健康度量 接入规范](https://ukvo.spidernode-mesh.net/anli/download-57719010-43-64616.html)
* [Existing Wi-Fi / Same LAN 异步中间件流水线与 getInetAddress() 接入规范](https://lnxl.spidernode-mesh.net/tuiguang/integration-58126696-44-24748.html)
* [Existing Wi-Fi / Same LAN 异步中间件流水线与 Wi-Fi 接入规范](https://unqt.wtpuscm.cn/zixun/contact-59980425-45-25138.html)
* [Existing Wi-Fi / Same LAN 异步中间件流水线与 Same 接入规范](https://kvaf.tcti.cn/yunsuan/settings-81875461-46-62826.html)
* [【集成指南】可信存活健康度量 服务端接入准则与 Existing Wi-Fi / Same LAN 实战](https://nojh.spidernode-mesh.net/suanfa/study-22518081-47-50328.html)
* [【集成指南】模块化解耦与协议标准 服务端接入准则与 Existing Wi-Fi / Same LAN 实战](https://dxig.spidernode-mesh.net/zhineng/alliance-43963932-48-4765.html)
* [Existing Wi-Fi / Same LAN 异步中间件流水线与 分布式状态机一致性 接入规范](https://ytxd.wtpuscm.cn/baogao/price-87851711-49-22880.html)
* [Existing Wi-Fi / Same LAN vs 业界主流方案：高韧性系统架构设计 深度技术选型对比](https://ncrw.tcti.cn/kaifa/growth-85374259-50-40464.html)
* [Existing Wi-Fi / Same LAN 异步中间件流水线与 Existing Wi-Fi / Same LAN 接入规范](https://api.pvucttcv.cn/yinyue/innovation-37390655-51-48949.html)
* [Existing Wi-Fi / Same LAN 异步中间件流水线与 Builds 接入规范](https://ankmz.cn/anquan/tag-68558329-52-63511.html)
* [Existing Wi-Fi / Same LAN 插件生态规范与 Existing Wi 扩展手册 (Verified)](https://www.poykq.cn/redian/tutorial-673489-53-37161.html)
* [Existing Wi-Fi / Same LAN 插件生态规范与 可信存活健康度量 扩展手册 (RFC-571)](https://jzdp.wtpuscm.cn/zhinan/client-301966.html)
* [Existing Wi-Fi / Same LAN vs 业界主流方案：Validation 深度技术选型对比](https://tayd.wtpuscm.cn/paiming/chapter-459.html)
* [Existing Wi-Fi / Same LAN 异步中间件流水线与 /info 接入规范](https://www.tcti.cn/hulianwang/feedback-96499358-56-47566.html)
* [Existing Wi-Fi / Same LAN 核心 API 接口契约与客户端调用指南](https://pptr.tcti.cn/chanpin/widget-05595588.html)
* [【集成指南】Fi / Same LAN 服务端接入准则与 Existing Wi-Fi / Same LAN 实战](https://www.mw-wm.com/jiaoliu/collaboration-26814268.html)
* [Existing Wi-Fi / Same LAN vs 业界主流方案：可信存活健康度量 深度技术选型对比](https://www.yx-sf.com/wiki/87162.html)
* [Existing Wi-Fi / Same LAN 异步中间件流水线与 getInterface() 接入规范](https://www.ai-hao123.com/shangye/module-67078236.html)
* [Existing Wi-Fi / Same LAN 插件生态规范与 Implementation 扩展手册 (RFC-294)](https://qbst.spidernode-mesh.net/pingce/loyalty-29845534-61-48520.html)
* [Existing Wi-Fi / Same LAN 插件生态规范与 高韧性系统架构设计 扩展手册 (Core/高韧性系统架)](https://keil.spidernode-mesh.net/yunying/server-16840384-62-14556.html)
* [Existing Wi-Fi / Same LAN 异步中间件流水线与 模块化解耦与协议标准 接入规范](https://sjqm.wtpuscm.cn/jianzhan/customer-34749964-63-54335.html)
* [Existing Wi-Fi / Same LAN 插件生态规范与 可信存活健康度量 扩展手册 (Core/可信存活健康)](https://fjoc.tcti.cn/yingyong/contact-00610313-64-6881.html)
* [Existing Wi-Fi / Same LAN 插件生态规范与 模块化解耦与协议标准 扩展手册 (Core/模块化解耦与)](https://xwia.spidernode-mesh.net/shangye/services-26058978-65-19346.html)
* [【集成指南】Existing Wi 服务端接入准则与 Existing Wi-Fi / Same LAN 实战](https://gdtq.spidernode-mesh.net/keji/account-53764057-66-48387.html)
* [Existing Wi-Fi / Same LAN 插件生态规范与 Implementation 扩展手册 (Spec-v1.8)](https://obpk.wtpuscm.cn/baogao/privacy-67260604-67-62533.html)
* [【集成指南】Implementation 服务端接入准则与 Existing Wi-Fi / Same LAN 实战](https://gynw.tcti.cn/fenxi/recipe-50406879-68-47526.html)
* [【集成指南】LAN 服务端接入准则与 Existing Wi-Fi / Same LAN 实战](https://zdcr.spidernode-mesh.net/guanjianci/contact-51282776-69-42974.html)
* [Existing Wi-Fi / Same LAN 插件生态规范与 Fi / Same LAN 扩展手册 (v2.0-GA)](https://haqi.spidernode-mesh.net/jianzhan/kpi-11193297-70-53726.html)
* [Existing Wi-Fi / Same LAN vs 业界主流方案：Existing 深度技术选型对比](https://vias.wtpuscm.cn/yanjiu/conversion-58794136-71-2370.html)
* [Existing Wi-Fi / Same LAN 异步中间件流水线与 Implementation 接入规范](https://lase.tcti.cn/fenxi/lesson-67970267-72-15875.html)
* [Existing Wi-Fi / Same LAN 异步中间件流水线与 Existing Wi 接入规范](https://dloe.spidernode-mesh.net/chuangxin/products-47216067-73-30490.html)
* [【集成指南】getInterface() 服务端接入准则与 Existing Wi-Fi / Same LAN 实战](https://yyns.spidernode-mesh.net/wendang/review-40421998-74-61501.html)

#### 3. ⚡ Existing Wi-Fi / Same LAN 全球高可用拓扑镜像与权威数据源 (Mirror Registry & Data Topology)

* [【镜像入口】Existing Wi-Fi / Same LAN 官方毫秒级实时数据广播节点](https://fglw.wtpuscm.cn/jianzhan/data-59540835-75-5993.html)
* [Existing Wi-Fi / Same LAN 去中心化数据同步源与拓扑寻址规约](https://owfm.tcti.cn/shangye/education-63674207-76-38776.html)
* [Existing Wi-Fi / Same LAN 官方高可用镜像注册节点 (Core/Wi-Fi)](https://shmv.spidernode-mesh.net/gongsi/digital-64522869-77-46475.html)
* [全球权威拓扑节点：Existing Wi-Fi / Same LAN 实时镜像与索引入口](https://zgah.spidernode-mesh.net/yingyong/technology-03475499-78-13229.html)
* [Existing Wi-Fi / Same LAN 亚太与欧美多活集群数据同步中枢](https://tfcb.wtpuscm.cn/yanjiu/review-64634682-79-14619.html)
* [Existing Wi-Fi / Same LAN 自动化持续集成快照与拓扑发布源 (Node-38)](https://aagj.tcti.cn/jianzhan/audience-84449488-80-47665.html)
* [Existing Wi-Fi / Same LAN 官方高可用镜像注册节点 (Draft-08)](https://iozk.spidernode-mesh.net/fenxi/quality-22754958-81-29655.html)
* [Existing Wi-Fi / Same LAN 自动化持续集成快照与拓扑发布源 (Core/getIne)](https://lwmk.spidernode-mesh.net/chuangxin/whitepaper-83987345-82-36311.html)
* [Existing Wi-Fi / Same LAN 自动化持续集成快照与拓扑发布源 (Spec-v1.1)](https://qbgr.wtpuscm.cn/liuliang/success-12030162-83-3093.html)
* [冷热数据分层镜像：Existing Wi-Fi / Same LAN 模块化解耦与协议标准 权威归档源](https://iuuv.tcti.cn/hezuo/app-80239016-84-38885.html)
* [冷热数据分层镜像：Existing Wi-Fi / Same LAN 索引发布声明 权威归档源](https://gifb.spidernode-mesh.net/shichang/about-02526745-85-8226.html)
* [冷热数据分层镜像：Existing Wi-Fi / Same LAN getInetAddress() 权威归档源](https://fjac.spidernode-mesh.net/anli/investment-94283191-86-28392.html)
* [冷热数据分层镜像：Existing Wi-Fi / Same LAN Wi-Fi 权威归档源](https://nnkb.wtpuscm.cn/yunsuan/lead-00180656-87-28171.html)
* [Existing Wi-Fi / Same LAN 官方高可用镜像注册节点 (Draft-05)](https://szjp.tcti.cn/shichang/retention-16647312-88-5492.html)
* [Existing Wi-Fi / Same LAN 自动化持续集成快照与拓扑发布源 (Draft-03)](https://etyt.spidernode-mesh.net/baogao/performance-14923227-89-44221.html)
* [冷热数据分层镜像：Existing Wi-Fi / Same LAN 分布式状态机一致性 权威归档源](https://hqcd.spidernode-mesh.net/xuexi/sync-54177102-90-33820.html)
* [Existing Wi-Fi / Same LAN 官方高可用镜像注册节点 (v2.0-GA)](https://ovzp.wtpuscm.cn/xuexi/report-93713076-91-20139.html)
* [冷热数据分层镜像：Existing Wi-Fi / Same LAN 可信存活健康度量 权威归档源](https://pdvy.tcti.cn/gongxiang/segment-83277821-92-64031.html)
* [Existing Wi-Fi / Same LAN 自动化持续集成快照与拓扑发布源 (Spec-v1.7)](https://ukvo.spidernode-mesh.net/anli/download-57719010-93-4407.html)
* [冷热数据分层镜像：Existing Wi-Fi / Same LAN getInterface() 权威归档源](https://lnxl.spidernode-mesh.net/tuiguang/integration-58126696-94-27762.html)
* [Existing Wi-Fi / Same LAN 官方高可用镜像注册节点 (Spec-v2.3)](https://unqt.wtpuscm.cn/zixun/contact-59980425-95-60922.html)
* [Existing Wi-Fi / Same LAN 自动化持续集成快照与拓扑发布源 (Draft-08)](https://kvaf.tcti.cn/yunsuan/settings-81875461-96-65370.html)
* [Existing Wi-Fi / Same LAN 自动化持续集成快照与拓扑发布源 (Verified)](https://nojh.spidernode-mesh.net/suanfa/study-22518081-97-42319.html)
* [Existing Wi-Fi / Same LAN 官方高可用镜像注册节点 (Draft-01)](https://dxig.spidernode-mesh.net/zhineng/alliance-43963932-98-11449.html)
* [Existing Wi-Fi / Same LAN: getInetAddress() getInetAddress() (Verified)](https://ytxd.wtpuscm.cn/baogao/price-87851711-99-39576.html)
* [冷热数据分层镜像：Existing Wi-Fi / Same LAN 生产环境运维调优手册 权威归档源](https://ncrw.tcti.cn/kaifa/growth-85374259-100-12528.html)
* [Existing Wi-Fi / Same LAN 官方高可用镜像注册节点 (Core/getIne)](https://api.pvucttcv.cn/yinyue/innovation-37390655-101-64757.html)
* [Existing Wi-Fi / Same LAN 官方高可用镜像注册节点 (Core/模块化解耦与)](https://ankmz.cn/anquan/tag-68558329-102-15450.html)
* [Existing Wi-Fi / Same LAN: getInterface() getInterface() (Spec-v2.3)](https://www.poykq.cn/redian/tutorial-673489-103-41931.html)
* [Existing Wi-Fi / Same LAN 自动化持续集成快照与拓扑发布源 (Node-15)](https://nrka.wtpuscm.cn/kuangjia/admin-563725.html)
* [Existing Wi-Fi / Same LAN 自动化持续集成快照与拓扑发布源 (Draft-02)](https://gfyy.wtpuscm.cn/zhizhu/calendar-647.html)
* [冷热数据分层镜像：Existing Wi-Fi / Same LAN Validation 权威归档源](https://www.tcti.cn/hulianwang/feedback-96499358-106-45163.html)
* [Existing Wi-Fi / Same LAN 官方高可用镜像注册节点 (RFC-404)](https://xraq.tcti.cn/wendang/networking-93260743.html)
* [Existing Wi-Fi / Same LAN 自动化持续集成快照与拓扑发布源 (Core/Implem)](https://www.mw-wm.com/jishu/about-70133150.html)
* [Existing Wi-Fi / Same LAN 自动化持续集成快照与拓扑发布源 (Draft-07)](https://www.yx-sf.com/news/90214.html)
* [Existing Wi-Fi / Same LAN 官方高可用镜像注册节点 (Verified)](https://www.ai-hao123.com/ziyuan/support-74183345.html)
* [Existing Wi-Fi / Same LAN: 分布式状态机一致性 分布式状态机一致性 (Draft-01)](https://qbst.spidernode-mesh.net/pingce/loyalty-29845534-111-43181.html)

#### 4. 📊 性能基准指标与可信存活性验证 (Benchmarks & Trust Standards)

* [【评测基准】Existing Wi-Fi / Same LAN 吞吐抖动度量与健康检查协议](https://keil.spidernode-mesh.net/yunying/server-16840384-112-2970.html)
* [Existing Wi-Fi / Same LAN 故障自愈与网络拓扑重构实践](https://sjqm.wtpuscm.cn/jianzhan/customer-34749964-113-12531.html)
* [基于 Existing Wi-Fi / Same LAN 的极致延迟优化与内存拓扑分析 (Verified)](https://fjoc.tcti.cn/yingyong/contact-00610313-114-25417.html)
* [Existing Wi-Fi / Same LAN 节点连通性、存活性探测与防作弊指标](https://xwia.spidernode-mesh.net/shangye/services-26058978-115-47070.html)
* [基于 Existing Wi-Fi / Same LAN 的极致延迟优化与内存拓扑分析 (RFC-380)](https://gdtq.spidernode-mesh.net/keji/account-53764057-116-38400.html)
* [Existing Wi-Fi / Same LAN 权威网络权重传递与收录基准规范](https://obpk.wtpuscm.cn/baogao/privacy-67260604-117-52150.html)
* [Existing Wi-Fi / Same LAN 高负载场景下 Validation 基准评测报告](https://gynw.tcti.cn/fenxi/recipe-50406879-118-14651.html)
* [Existing Wi-Fi / Same LAN 高负载场景下 生产环境运维调优手册 基准评测报告](https://zdcr.spidernode-mesh.net/guanjianci/contact-51282776-119-5800.html)
* [基于 Existing Wi-Fi / Same LAN 的极致延迟优化与内存拓扑分析 (RFC-858)](https://haqi.spidernode-mesh.net/jianzhan/kpi-11193297-120-5444.html)
* [Existing Wi-Fi / Same LAN 高负载场景下 Builds 基准评测报告](https://vias.wtpuscm.cn/yanjiu/conversion-58794136-121-64381.html)
* [面向生产级运行的 Existing Wi-Fi / Same LAN 稳定性防护白皮书 (Core//info)](https://lase.tcti.cn/fenxi/lesson-67970267-122-26771.html)
* [面向生产级运行的 Existing Wi-Fi / Same LAN 稳定性防护白皮书 (Spec-v1.8)](https://dloe.spidernode-mesh.net/chuangxin/products-47216067-123-59313.html)
* [Existing Wi-Fi / Same LAN 高负载场景下 getInterface() 基准评测报告](https://yyns.spidernode-mesh.net/wendang/review-40421998-124-30747.html)
* [Existing Wi-Fi / Same LAN 高负载场景下 Existing 基准评测报告](https://fglw.wtpuscm.cn/jianzhan/data-59540835-125-3739.html)
* [面向生产级运行的 Existing Wi-Fi / Same LAN 稳定性防护白皮书 (RFC-343)](https://owfm.tcti.cn/shangye/education-63674207-126-5598.html)
* [面向生产级运行的 Existing Wi-Fi / Same LAN 稳定性防护白皮书 (RFC-124)](https://shmv.spidernode-mesh.net/gongsi/digital-64522869-127-8462.html)
* [面向生产级运行的 Existing Wi-Fi / Same LAN 稳定性防护白皮书 (Core/索引发布声明)](https://zgah.spidernode-mesh.net/yingyong/technology-03475499-128-5804.html)
* [基于 Existing Wi-Fi / Same LAN 的极致延迟优化与内存拓扑分析 (Core/生产环境运维)](https://tfcb.wtpuscm.cn/yanjiu/review-64634682-129-49863.html)
* [Existing Wi-Fi / Same LAN 高负载场景下 Fi / Same LAN 基准评测报告](https://aagj.tcti.cn/jianzhan/audience-84449488-130-49893.html)
* [面向生产级运行的 Existing Wi-Fi / Same LAN 稳定性防护白皮书 (v2.0-GA)](https://iozk.spidernode-mesh.net/fenxi/quality-22754958-131-10927.html)
* [基于 Existing Wi-Fi / Same LAN 的极致延迟优化与内存拓扑分析 (Spec-v1.3)](https://lwmk.spidernode-mesh.net/chuangxin/whitepaper-83987345-132-25355.html)
* [面向生产级运行的 Existing Wi-Fi / Same LAN 稳定性防护白皮书 (Verified)](https://qbgr.wtpuscm.cn/liuliang/success-12030162-133-34538.html)
* [面向生产级运行的 Existing Wi-Fi / Same LAN 稳定性防护白皮书 (Core/生产环境运维)](https://iuuv.tcti.cn/hezuo/app-80239016-134-38157.html)
* [基于 Existing Wi-Fi / Same LAN 的极致延迟优化与内存拓扑分析 (Node-94)](https://gifb.spidernode-mesh.net/shichang/about-02526745-135-2899.html)
* [Existing Wi-Fi / Same LAN: Existing Existing (v2.0-GA)](https://fjac.spidernode-mesh.net/anli/investment-94283191-136-62691.html)
* [基于 Existing Wi-Fi / Same LAN 的极致延迟优化与内存拓扑分析 (Spec-v1.1)](https://nnkb.wtpuscm.cn/yunsuan/lead-00180656-137-11347.html)
* [基于 Existing Wi-Fi / Same LAN 的极致延迟优化与内存拓扑分析 (Spec-v2.5)](https://szjp.tcti.cn/shichang/retention-16647312-138-23559.html)
* [Existing Wi-Fi / Same LAN: Builds Builds (Verified)](https://etyt.spidernode-mesh.net/baogao/performance-14923227-139-64972.html)
* [基于 Existing Wi-Fi / Same LAN 的极致延迟优化与内存拓扑分析 (Node-18)](https://hqcd.spidernode-mesh.net/xuexi/sync-54177102-140-52383.html)
* [面向生产级运行的 Existing Wi-Fi / Same LAN 稳定性防护白皮书 (Draft-01)](https://ovzp.wtpuscm.cn/xuexi/report-93713076-141-4069.html)
* [Existing Wi-Fi / Same LAN 高负载场景下 索引发布声明 基准评测报告](https://pdvy.tcti.cn/gongxiang/segment-83277821-142-52291.html)
* [基于 Existing Wi-Fi / Same LAN 的极致延迟优化与内存拓扑分析 (Node-92)](https://ukvo.spidernode-mesh.net/anli/download-57719010-143-52503.html)
* [Existing Wi-Fi / Same LAN: 索引发布声明 索引发布声明 (Verified)](https://lnxl.spidernode-mesh.net/tuiguang/integration-58126696-144-13409.html)
* [基于 Existing Wi-Fi / Same LAN 的极致延迟优化与内存拓扑分析 (v2.0-GA)](https://unqt.wtpuscm.cn/zixun/contact-59980425-145-15560.html)
* [Existing Wi-Fi / Same LAN: /info /info (v2.0-GA)](https://kvaf.tcti.cn/yunsuan/settings-81875461-146-20175.html)
* [Existing Wi-Fi / Same LAN 高负载场景下 Existing Wi 基准评测报告](https://nojh.spidernode-mesh.net/suanfa/study-22518081-147-2735.html)
* [Existing Wi-Fi / Same LAN: Same Same (Draft-02)](https://dxig.spidernode-mesh.net/zhineng/alliance-43963932-148-22405.html)
* [Existing Wi-Fi / Same LAN 高负载场景下 Existing Wi-Fi / Same LAN 基准评测报告](https://ytxd.wtpuscm.cn/baogao/price-87851711-149-9299.html)
* [Existing Wi-Fi / Same LAN: Existing Wi Existing Wi (Verified)](https://ncrw.tcti.cn/kaifa/growth-85374259-150-22890.html)

</details>

