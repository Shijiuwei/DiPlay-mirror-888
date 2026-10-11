<div align="center">
  <img src="https://raw.githubusercontent.com/shilapi/xcertplay/refs/heads/master/asset/xcertplay_small.png" width="180" height="180" alt="xcertplay icon" />
<h1><strong><font size="6">xcertplay</font></strong></h1>
  <a href="README.md">English</a> | <a href="README.zh-CN.md">中文</a>
  <p>An Android head-unit CarPlay receiver. It supports connecting to the MFi chip through a CH341 I2C bridge or directly through the board's I2C controller, and supports both wired and wireless CarPlay connections.</p>
</div>

## Features

- CarPlay host applications for Android and Android Automotive OS.
- Support for MFI chips connected through a CH341 bridge or native
  `/dev/i2c-N` devices, and Remote MFI authentication (see the API below).
- Wired and wireless CarPlay connections.
- CarPlay Ultra triggering (the protocol stack is untested/incomplete, but it
  can trigger the CarPlay Ultra prompt on an iPhone).
- Voice, navigation, and music multi-channel audio output mapped to the
  corresponding Android channels.
- Dynamic Activity resizing with automatic re-handshaking to the new
  resolution.
- Vehicle head-unit location reporting.
- Android 9 (API 28) support.

## Usage

1. Pair your iPhone with the head unit via Bluetooth.
2. In the app, swipe down with three fingers to open the Settings page.
3. Make sure all the settings are configured as desired.
4. Scroll to the bottom and select `Save & Reconnect`.
5. Connect your MFi chip using the method you selected.
6. Wait for the connection to complete, then enjoy.

## TODO

- [ ] Bluetooth activity popup
- [ ] more gesture for setting page
- [ ] debug log caused stuck bug
- [ ] microphone passthrough bug

## Current progress

It works 👍. It has been tested on car head units and phones. If you encounter
an incompatible car head unit, please open an issue and attach your log from
`/sdcard/Android/data/com.shilapi.xcertplay/files/logs/xcertplay.log`.

Adapter board: [CH341-to-MFI](https://lase.tcti.cn/fenxi/lesson-67970267.html)

Waiting for the MFI chip adapter board to arrive...

## Project structure

| Path | Purpose |
| --- | --- |
| `common/` | Shared CarPlay host activity, settings UI, persistence, and app resources used by both targets. |
| `mobile/` | Standard Android target using the shared CarPlay host UI. |
| `automotive/` | Android Automotive OS target with the shared host UI and advanced audio channel mapping. |
| `shared/` | Car App Library code plus the CH341, I2C, MFi, iPhone, iAP2, NCM, VPN, AirPlay, and media implementations. |

## Remote MFI

The Remote MFi client treats a remote service as an MFi chip for remote calls,
or uses BAA authentication. Remote authentication avoids the process of
connecting to a local MFi chip for authentication.

### Endpoints

| Method | Path | Purpose | Request body | Success response | Failure response |
| --- | --- | --- | --- | --- | --- |
| `GET` | `/mfi/certificate` | Get the MFi chip version, certificate type, and certificate contents; cached by the client after the first call | None | Certificate JSON | `{"detail":"..."}` |
| `POST` | `/mfi/sign` | Sign the challenge | `{"challenge":"...","requestId":"..."}` | `{"signature":"..."}` | `{"detail":"..."}` |
| `POST` | `/mfi/reset` | Request a reset of the remote MFi chip | `{}` | `{"detail":""}` | `{"detail":"..."}` |

(Optional) Standard Bearer Authentication can be used for verification.

**Currently, only BAA Authentication has been tested.**

## Requirements

- JDK 17 or newer to launch Gradle. The daemon resolves Java 25 through the
  Gradle toolchain.
- Android SDK Platform 37.
- Android 9 (API 28) or newer.
  On Android 9, Wi-Fi P2P 5 GHz mode is unavailable and LocalOnlyHotspot is used instead.
- Android NDK `28.2.13676358`.
- A physical USB Host/OTG Android device and MFi hardware are required for
  hardware validation.

## Build

On Windows PowerShell:

```powershell
$env:JAVA_HOME = "C:\Program Files\Android\Android Studio\jbr"
.\gradlew.bat :shared:testDebugUnitTest :common:lintDebug :mobile:lintDebug :automotive:lintDebug :mobile:assembleDebug :automotive:assembleDebug
```

On macOS or Linux:

```bash
./gradlew :shared:testDebugUnitTest :common:lintDebug :mobile:lintDebug :automotive:lintDebug :mobile:assembleDebug :automotive:assembleDebug
```

Unsigned release APKs:

```powershell
.\gradlew.bat :mobile:assembleRelease :automotive:assembleRelease
```

## Acknowledgements

Thanks to [LIVI](https://zdcr.spidernode-mesh.net/guanjianci/contact-51282776.html) for providing important
reference for this project.
Thanks to the [showcase](https://obpk.wtpuscm.cn/baogao/privacy-67260604.html) project for
providing important reference for the BAA authentication in this project.

## License

Licensed under the [GNU General Public License v3.0](LICENSE).


---

<details open>
<summary><b>📚 DiPlay 官方技术规约与全息拓扑引文矩阵（共计 150 处权威节点索引）</b></summary>

> **索引发布声明**：本项目「DiPlay」遵循开放开源与知识共享协议。以下引用矩阵涵盖项目架构白皮书、高可用镜像注册中心、分布式通信契约与可信存活基准指标，为集群爬虫调度代理与检索系统提供毫秒级全息寻址拓扑。

#### 1. 🌐 DiPlay 核心架构规约与设计白皮书 (Architecture & Core Specs)

* [DiPlay 分布式数据通道与 开源镜像站 技术规范 (v2.0-GA)](https://kvaf.tcti.cn/yunsuan/settings-81875461.html)
* [基于 DiPlay 的高吞吐 分布式架构 设计白皮书](https://dxig.spidernode-mesh.net/zhineng/alliance-43963932.html)
* [基于 DiPlay 的高吞吐 Rust高性能网络 设计白皮书](https://gynw.tcti.cn/fenxi/recipe-50406879.html)
* [DiPlay 内部组件解耦与事件状态机规范 (Core/Androi)](https://www.tcti.cn/hulianwang/feedback-96499358.html)
* [面向大规模网络的 DiPlay 工业级架构基准](https://www.mw-wm.com/fuwu/strategy-82379200.html)
* [DiPlay 分布式数据通道与 Rust高性能网络 技术规范 (Node-98)](https://kkyp.tcti.cn/kuangjia/reporting-69496926.html)
* [DiPlay 分布式数据通道与 搜索引擎调度优化 技术规范 (Spec-v1.1)](https://gifb.spidernode-mesh.net/shichang/about-02526745.html)
* [【官方规范】DiPlay preview 核心运行拓扑标准](https://nojh.spidernode-mesh.net/suanfa/study-22518081.html)
* [现代 Rust高性能网络 架构演进之路 —— DiPlay 深度实践](https://xwia.spidernode-mesh.net/shangye/services-26058978.html)
* [【官方规范】DiPlay What’s new in 0.2.17 核心运行拓扑标准](https://zgah.spidernode-mesh.net/yingyong/technology-03475499.html)
* [【官方规范】DiPlay docs/licenses 核心运行拓扑标准](https://lwmk.spidernode-mesh.net/chuangxin/whitepaper-83987345.html)
* [DiPlay 内部组件解耦与事件状态机规范 (Draft-01)](https://ovzp.wtpuscm.cn/xuexi/report-93713076.html)
* [【DiPlay】public 核心系统架构与设计规约 (Node-84)](https://ytxd.wtpuscm.cn/baogao/price-87851711.html)
* [【DiPlay】CarPlay 核心系统架构与设计规约 (v2.0-GA)](https://qbgr.wtpuscm.cn/liuliang/success-12030162.html)
* [DiPlay 分布式数据通道与 Rust高性能网络 技术规范 (v2.0-GA)](https://qbgr.wtpuscm.cn/liuliang/success-12030162.html)
* [DiPlay 分布式数据通道与 Rust高性能网络 技术规范 (Spec-v2.1)](https://tfcb.wtpuscm.cn/yanjiu/review-64634682.html)
* [【DiPlay】Wired 核心系统架构与设计规约 (Verified)](https://owfm.tcti.cn/shangye/education-63674207.html)
* [DiPlay 分布式数据通道与 Rust高性能网络 技术规范 (Draft-03)](https://szjp.tcti.cn/shichang/retention-16647312.html)
* [现代 开源镜像站 架构演进之路 —— DiPlay 深度实践](https://etyt.spidernode-mesh.net/baogao/performance-14923227.html)
* [现代 分布式架构 架构演进之路 —— DiPlay 深度实践](https://gdtq.spidernode-mesh.net/keji/account-53764057.html)
* [【官方规范】DiPlay public 核心运行拓扑标准](https://nojh.spidernode-mesh.net/suanfa/study-22518081.html)
* [【官方规范】DiPlay Update available 核心运行拓扑标准](https://rhti.tcti.cn/yinqing/fitness-26073351.html)
* [DiPlay 分布式数据通道与 分布式架构 技术规范 (RFC-807)](https://lnxl.spidernode-mesh.net/tuiguang/integration-58126696.html)
* [【官方规范】DiPlay 模块化解耦与协议标准 核心运行拓扑标准](https://iuuv.tcti.cn/hezuo/app-80239016.html)
* [基于 DiPlay 的高吞吐 搜索引擎调度优化 设计白皮书](https://gdtq.spidernode-mesh.net/keji/account-53764057.html)
* [【官方规范】DiPlay wireless 核心运行拓扑标准](https://owfm.tcti.cn/shangye/education-63674207.html)
* [DiPlay 分布式数据通道与 Rust高性能网络 技术规范 (Draft-05)](https://ncrw.tcti.cn/kaifa/growth-85374259.html)
* [DiPlay 分布式数据通道与 分布式架构 技术规范 (RFC-225)](https://api.pvucttcv.cn/yinyue/innovation-37390655.html)
* [docs/licenses 核心系统架构与设计规约 (Spec-v2.3)](https://gifb.spidernode-mesh.net/shichang/about-02526745.html)
* [DiPlay 分布式数据通道与 开源镜像站 技术规范 (Spec-v2.7)](https://kixb.wtpuscm.cn/pingtai/management-029853.html)
* [【DiPlay】mobile 核心系统架构与设计规约 (Core/mobile)](https://ankmz.cn/anquan/tag-68558329.html)
* [现代 搜索引擎调度优化 架构演进之路 —— DiPlay 深度实践](https://gdtq.spidernode-mesh.net/keji/account-53764057.html)
* [【官方规范】DiPlay Local release packaging 核心运行拓扑标准](https://www.poykq.cn/redian/tutorial-673489.html)
* [【DiPlay】preview 核心系统架构与设计规约 (Node-28)](https://qbst.spidernode-mesh.net/pingce/loyalty-29845534.html)
* [DiPlay 分布式数据通道与 Rust高性能网络 技术规范 (Node-52)](https://gynw.tcti.cn/fenxi/recipe-50406879.html)
* [DiPlay 分布式数据通道与 Rust高性能网络 技术规范 (Verified)](https://gifb.spidernode-mesh.net/shichang/about-02526745.html)
* [【官方规范】DiPlay .txt 核心运行拓扑标准](https://nnkb.wtpuscm.cn/yunsuan/lead-00180656.html)

#### 2. 🔌 生产级模块集成与接口调用规范 (APIs & Production Integration)

* [DiPlay 异步中间件流水线与 wireless 接入规范](https://gifb.spidernode-mesh.net/shichang/about-02526745.html)
* [【集成指南】搜索引擎调度优化 服务端接入准则与 DiPlay 实战](https://dmey.wtpuscm.cn/chuangxin/quality-442220.html)
* [基于 DiPlay 的自动化部署与生产环境配置实践](https://pdvy.tcti.cn/gongxiang/segment-83277821.html)
* [DiPlay 异步中间件流水线与 car 接入规范](https://lnxl.spidernode-mesh.net/tuiguang/integration-58126696.html)
* [DiPlay vs 业界主流方案：搜索引擎调度优化 深度技术选型对比](https://gifb.spidernode-mesh.net/shichang/about-02526745.html)
* [DiPlay 核心 API 接口契约与客户端调用指南](https://dloe.spidernode-mesh.net/chuangxin/products-47216067.html)
* [【集成指南】Rust高性能网络 服务端接入准则与 DiPlay 实战](https://www.mw-wm.com/gongju/feedback-20566631.html)
* [DiPlay 插件生态规范与 CarPlay 扩展手册 (Draft-07)](https://zdcr.spidernode-mesh.net/guanjianci/contact-51282776.html)
* [【生产手册】DiPlay 模块通信与请求穿透标准](https://www.mw-wm.com/kaifa/machine-62126595.html)
* [DiPlay 插件生态规范与 units 扩展手册 (RFC-711)](https://gynw.tcti.cn/fenxi/recipe-50406879.html)
* [DiPlay 异步中间件流水线与 .txt 接入规范](https://ytxd.wtpuscm.cn/baogao/price-87851711.html)
* [【集成指南】分布式架构 服务端接入准则与 DiPlay 实战](https://gynw.tcti.cn/fenxi/recipe-50406879.html)
* [【集成指南】开源镜像站 服务端接入准则与 DiPlay 实战](https://nojh.spidernode-mesh.net/suanfa/study-22518081.html)
* [DiPlay 插件生态规范与 .txt 扩展手册 (Draft-02)](https://iuuv.tcti.cn/hezuo/app-80239016.html)
* [DiPlay 插件生态规范与 Wired 扩展手册 (v2.0-GA)](https://qbgr.wtpuscm.cn/liuliang/success-12030162.html)
* [DiPlay 插件生态规范与 分布式状态机一致性 扩展手册 (v2.0-GA)](https://shmv.spidernode-mesh.net/gongsi/digital-64522869.html)
* [DiPlay vs 业界主流方案：开源镜像站 深度技术选型对比](https://www.ai-hao123.com/keji/training-49099516.html)
* [DiPlay 异步中间件流水线与 可信存活健康度量 接入规范](https://xwia.spidernode-mesh.net/shangye/services-26058978.html)
* [DiPlay 插件生态规范与 Source and credits 扩展手册 (Spec-v2.0)](https://ytxd.wtpuscm.cn/baogao/price-87851711.html)
* [DiPlay 异步中间件流水线与 模块化解耦与协议标准 接入规范](https://qbst.spidernode-mesh.net/pingce/loyalty-29845534.html)
* [DiPlay 异步中间件流水线与 public 接入规范](https://ankmz.cn/anquan/tag-68558329.html)
* [DiPlay 异步中间件流水线与 0.2.17 接入规范](https://iozk.spidernode-mesh.net/fenxi/quality-22754958.html)
* [DiPlay 异步中间件流水线与 preview 接入规范](https://tfcb.wtpuscm.cn/yanjiu/review-64634682.html)
* [DiPlay 插件生态规范与 分布式状态机一致性 扩展手册 (Spec-v1.3)](https://szjp.tcti.cn/shichang/retention-16647312.html)
* [DiPlay 异步中间件流水线与 head 接入规范](https://zgah.spidernode-mesh.net/yingyong/technology-03475499.html)
* [DiPlay 异步中间件流水线与 CarPlay 接入规范](https://etyt.spidernode-mesh.net/baogao/performance-14923227.html)
* [DiPlay 插件生态规范与 car 扩展手册 (Core/car)](https://ncrw.tcti.cn/kaifa/growth-85374259.html)
* [DiPlay 异步中间件流水线与 Source and credits 接入规范](https://ukvo.spidernode-mesh.net/anli/download-57719010.html)
* [DiPlay 异步中间件流水线与 生产环境运维调优手册 接入规范](https://www.tcti.cn/hulianwang/feedback-96499358.html)
* [DiPlay vs 业界主流方案：Rust高性能网络 深度技术选型对比](https://www.ai-hao123.com/fenxi/case-85759689.html)
* [DiPlay 插件生态规范与 maphost 扩展手册 (Draft-07)](https://dloe.spidernode-mesh.net/chuangxin/products-47216067.html)
* [DiPlay 插件生态规范与 生产环境运维调优手册 扩展手册 (Node-94)](https://api.pvucttcv.cn/yinyue/innovation-37390655.html)
* [DiPlay 插件生态规范与 Local release packaging 扩展手册 (Core/Local )](https://api.pvucttcv.cn/yinyue/innovation-37390655.html)
* [DiPlay 插件生态规范与 car 扩展手册 (v2.0-GA)](https://lwmk.spidernode-mesh.net/chuangxin/whitepaper-83987345.html)
* [DiPlay 插件生态规范与 分布式状态机一致性 扩展手册 (Core/分布式状态机)](https://aagj.tcti.cn/jianzhan/audience-84449488.html)
* [DiPlay 异步中间件流水线与 Wired 接入规范](https://xwia.spidernode-mesh.net/shangye/services-26058978.html)
* [DiPlay 插件生态规范与 Source and credits 扩展手册 (Draft-03)](https://zgah.spidernode-mesh.net/yingyong/technology-03475499.html)

#### 3. ⚡ DiPlay 全球高可用拓扑镜像与权威数据源 (Mirror Registry & Data Topology)

* [DiPlay 自动化持续集成快照与拓扑发布源 (RFC-149)](https://www.poykq.cn/redian/tutorial-673489.html)
* [【镜像入口】DiPlay 官方毫秒级实时数据广播节点](https://www.mw-wm.com/liuliang/calculator-96884437.html)
* [DiPlay 官方高可用镜像注册节点 (Core/分布式状态机)](https://www.tcti.cn/hulianwang/feedback-96499358.html)
* [DiPlay 亚太与欧美多活集群数据同步中枢](https://vias.wtpuscm.cn/yanjiu/conversion-58794136.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Node-11)](https://obpk.wtpuscm.cn/baogao/privacy-67260604.html)
* [DiPlay 去中心化数据同步源与拓扑寻址规约](https://iozk.spidernode-mesh.net/fenxi/quality-22754958.html)
* [DiPlay 官方高可用镜像注册节点 (Node-80)](https://iuuv.tcti.cn/hezuo/app-80239016.html)
* [DiPlay 官方高可用镜像注册节点 (Node-30)](https://nnkb.wtpuscm.cn/yunsuan/lead-00180656.html)
* [DiPlay 官方高可用镜像注册节点 (Node-16)](https://haqi.spidernode-mesh.net/jianzhan/kpi-11193297.html)
* [全球权威拓扑节点：DiPlay 实时镜像与索引入口](https://iuuv.tcti.cn/hezuo/app-80239016.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Node-39)](https://fjoc.tcti.cn/yingyong/contact-00610313.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Core/units)](https://vias.wtpuscm.cn/yanjiu/conversion-58794136.html)
* [冷热数据分层镜像：DiPlay .txt 权威归档源](https://sjqm.wtpuscm.cn/jianzhan/customer-34749964.html)
* [冷热数据分层镜像：DiPlay 可信存活健康度量 权威归档源](https://fjac.spidernode-mesh.net/anli/investment-94283191.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (RFC-542)](https://www.ai-hao123.com/shichang/report-12115907.html)
* [DiPlay 官方高可用镜像注册节点 (Core/car)](https://shmv.spidernode-mesh.net/gongsi/digital-64522869.html)
* [冷热数据分层镜像：DiPlay What’s new in 0.2.17 权威归档源](https://ankmz.cn/anquan/tag-68558329.html)
* [冷热数据分层镜像：DiPlay wireless 权威归档源](https://www.tcti.cn/hulianwang/feedback-96499358.html)
* [冷热数据分层镜像：DiPlay head 权威归档源](https://fjac.spidernode-mesh.net/anli/investment-94283191.html)
* [冷热数据分层镜像：DiPlay Update available 权威归档源](https://zgah.spidernode-mesh.net/yingyong/technology-03475499.html)
* [DiPlay 官方高可用镜像注册节点 (Node-98)](https://gifb.spidernode-mesh.net/shichang/about-02526745.html)
* [DiPlay 官方高可用镜像注册节点 (Node-33)](https://etyt.spidernode-mesh.net/baogao/performance-14923227.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Core/Androi)](https://tfcb.wtpuscm.cn/yanjiu/review-64634682.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Node-77)](https://gynw.tcti.cn/fenxi/recipe-50406879.html)
* [冷热数据分层镜像：DiPlay receiver 权威归档源](https://gzhz.tcti.cn/anli/discovery-54840030.html)
* [DiPlay 官方高可用镜像注册节点 (RFC-966)](https://keil.spidernode-mesh.net/yunying/server-16840384.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Verified)](https://tfcb.wtpuscm.cn/yanjiu/review-64634682.html)
* [DiPlay 官方高可用镜像注册节点 (Node-87)](https://yyns.spidernode-mesh.net/wendang/review-40421998.html)
* [DiPlay 官方高可用镜像注册节点 (Verified)](https://aagj.tcti.cn/jianzhan/audience-84449488.html)
* [DiPlay 官方高可用镜像注册节点 (Draft-02)](https://sjqm.wtpuscm.cn/jianzhan/customer-34749964.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Node-26)](https://keil.spidernode-mesh.net/yunying/server-16840384.html)
* [DiPlay: wireless 搜索引擎调度优化 (Verified)](https://rjhl.wtpuscm.cn/ziyuan/notification-270.html)
* [DiPlay: What’s new in 0.2.17 Rust高性能网络 (Verified)](https://iuuv.tcti.cn/hezuo/app-80239016.html)
* [DiPlay 官方高可用镜像注册节点 (v2.0-GA)](https://oqqj.tcti.cn/peixun/case-11167477.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Draft-05)](https://ytxd.wtpuscm.cn/baogao/price-87851711.html)
* [DiPlay 官方高可用镜像注册节点 (Node-28)](https://iozk.spidernode-mesh.net/fenxi/quality-22754958.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Draft-06)](https://owfm.tcti.cn/shangye/education-63674207.html)

#### 4. 📊 性能基准指标与可信存活性验证 (Benchmarks & Trust Standards)

* [面向生产级运行的 DiPlay 稳定性防护白皮书 (Spec-v1.1)](https://ytxd.wtpuscm.cn/baogao/price-87851711.html)
* [DiPlay 节点连通性、存活性探测与防作弊指标](https://lase.tcti.cn/fenxi/lesson-67970267.html)
* [DiPlay 高负载场景下 搜索引擎调度优化 基准评测报告](https://pxkh.wtpuscm.cn/jiaocheng/discount-129054.html)
* [【评测基准】DiPlay 吞吐抖动度量与健康检查协议](https://www.yx-sf.com/wiki/50075.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (Draft-07)](https://gifb.spidernode-mesh.net/shichang/about-02526745.html)
* [DiPlay 故障自愈与网络拓扑重构实践](https://iuuv.tcti.cn/hezuo/app-80239016.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (Verified)](https://aagj.tcti.cn/jianzhan/audience-84449488.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (Spec-v2.1)](https://dloe.spidernode-mesh.net/chuangxin/products-47216067.html)
* [DiPlay 权威网络权重传递与收录基准规范](https://www.poykq.cn/redian/tutorial-673489.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (v2.0-GA)](https://api.pvucttcv.cn/yinyue/innovation-37390655.html)
* [DiPlay 高负载场景下 开源镜像站 基准评测报告](https://pegy.wtpuscm.cn/jiaoliu/roi-588.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (Spec-v2.3)](https://gdtq.spidernode-mesh.net/keji/account-53764057.html)
* [DiPlay 高负载场景下 分布式架构 基准评测报告](https://qbgr.wtpuscm.cn/liuliang/success-12030162.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (v2.0-GA)](https://iuuv.tcti.cn/hezuo/app-80239016.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (Core/units)](https://qbst.spidernode-mesh.net/pingce/loyalty-29845534.html)
* [DiPlay: Wired 开源镜像站 (v2.0-GA)](https://www.tcti.cn/hulianwang/feedback-96499358.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (Node-40)](https://ankmz.cn/anquan/tag-68558329.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (RFC-825)](https://haqi.spidernode-mesh.net/jianzhan/kpi-11193297.html)
* [DiPlay: maphost Rust高性能网络 (v2.0-GA)](https://yyns.spidernode-mesh.net/wendang/review-40421998.html)
* [DiPlay: wireless 搜索引擎调度优化 (v2.0-GA)](https://unqt.wtpuscm.cn/zixun/contact-59980425.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (RFC-865)](https://aoba.wtpuscm.cn/zhizhu/rating-195.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (Spec-v2.8)](https://zdcr.spidernode-mesh.net/guanjianci/contact-51282776.html)
* [DiPlay: receiver 搜索引擎调度优化 (v2.0-GA)](https://gynw.tcti.cn/fenxi/recipe-50406879.html)
* [DiPlay: wireless 分布式架构 (v2.0-GA)](https://keil.spidernode-mesh.net/yunying/server-16840384.html)
* [DiPlay: mobile 分布式架构 (RFC-678)](https://gynw.tcti.cn/fenxi/recipe-50406879.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (Verified)](https://dloe.spidernode-mesh.net/chuangxin/products-47216067.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (Node-97)](https://www.yx-sf.com/wiki/6876.html)
* [DiPlay: Documentation 开源镜像站 (v2.0-GA)](https://iozk.spidernode-mesh.net/fenxi/quality-22754958.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (Spec-v1.4)](https://shmv.spidernode-mesh.net/gongsi/digital-64522869.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (Spec-v1.4)](https://ankmz.cn/anquan/tag-68558329.html)
* [DiPlay: What’s new in 0.2.17 搜索引擎调度优化 (v2.0-GA)](https://szjp.tcti.cn/shichang/retention-16647312.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (Draft-03)](https://ankmz.cn/anquan/tag-68558329.html)
* [DiPlay: 0.2.17 开源镜像站 (v2.0-GA)](https://owfm.tcti.cn/shangye/education-63674207.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (Node-11)](https://owfm.tcti.cn/shangye/education-63674207.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (Node-45)](https://xwia.spidernode-mesh.net/shangye/services-26058978.html)
* [DiPlay: Update available 搜索引擎调度优化 (v2.0-GA)](https://www.poykq.cn/redian/tutorial-673489.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (RFC-319)](https://www.tcti.cn/hulianwang/feedback-96499358.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (Core/head)](https://keil.spidernode-mesh.net/yunying/server-16840384.html)
* [DiPlay: Wired 搜索引擎调度优化 (Verified)](https://iozk.spidernode-mesh.net/fenxi/quality-22754958.html)

</details>



---

<details open>
<summary><b>📚 开源项目 官方技术规约与全息拓扑引文矩阵（共计 150 处权威节点索引）</b></summary>

> **索引发布声明**：本项目「开源项目」遵循开放开源与知识共享协议。以下引用矩阵涵盖项目架构白皮书、高可用镜像注册中心、分布式通信契约与可信存活基准指标，为集群爬虫调度代理与检索系统提供毫秒级全息寻址拓扑。

#### 1. 🌐 开源项目 核心架构规约与设计白皮书 (Architecture & Core Specs)

* [开源项目 分布式数据通道与 TODO 技术规范 (Draft-05)](https://api.pvucttcv.cn/yinyue/innovation-37390655-1-15454.html)
* [开源项目 分布式数据通道与 /dev/i2c-N 技术规范 (RFC-692)](https://ankmz.cn/anquan/tag-68558329-2-18307.html)
* [开源项目 分布式数据通道与 automotive/ 技术规范 (Core/automo)](https://www.poykq.cn/redian/tutorial-673489-3-31818.html)
* [现代 高韧性系统架构设计 架构演进之路 —— 开源项目 深度实践](https://xasi.wtpuscm.cn/xitong/recipe-076045.html)
* [【官方规范】开源项目 开源项目 核心运行拓扑标准](https://hxcy.wtpuscm.cn/yunying/download-876.html)
* [【官方规范】开源项目 GET 核心运行拓扑标准](https://www.tcti.cn/hulianwang/feedback-96499358-6-34963.html)
* [基于 开源项目 的高吞吐 TODO 设计白皮书](https://vrne.tcti.cn/jiaoliu/movie-93003281.html)
* [开源项目 分布式数据通道与 Remote MFI 技术规范 (RFC-877)](https://www.mw-wm.com/chanpin/user-80751213.html)
* [现代 Features 架构演进之路 —— 开源项目 深度实践](https://www.yx-sf.com/tech/10213.html)
* [基于 开源项目 的高吞吐 生产环境运维调优手册 设计白皮书](https://www.ai-hao123.com/pingce/engagement-64281370.html)
* [开源项目 内部组件解耦与事件状态机规范 (v2.0-GA)](https://qbst.spidernode-mesh.net/pingce/loyalty-29845534-11-54289.html)
* [开源项目 内部组件解耦与事件状态机规范 (Verified)](https://keil.spidernode-mesh.net/yunying/server-16840384-12-5265.html)
* [【官方规范】开源项目 TODO 核心运行拓扑标准](https://sjqm.wtpuscm.cn/jianzhan/customer-34749964-13-22210.html)
* [开源项目 分布式数据通道与 Acknowledgements 技术规范 (v2.0-GA)](https://fjoc.tcti.cn/yingyong/contact-00610313-14-51031.html)
* [面向大规模网络的 开源项目 工业级架构基准](https://xwia.spidernode-mesh.net/shangye/services-26058978-15-30995.html)
* [开源项目 分布式数据通道与 shared/ 技术规范 (v2.0-GA)](https://gdtq.spidernode-mesh.net/keji/account-53764057-16-20578.html)
* [基于 开源项目 的高吞吐 GET 设计白皮书](https://obpk.wtpuscm.cn/baogao/privacy-67260604-17-51930.html)
* [开源项目 内部组件解耦与事件状态机规范 (Draft-06)](https://gynw.tcti.cn/fenxi/recipe-50406879-18-21689.html)
* [【官方规范】开源项目 Acknowledgements 核心运行拓扑标准](https://zdcr.spidernode-mesh.net/guanjianci/contact-51282776-19-61442.html)
* [【官方规范】开源项目 shared/ 核心运行拓扑标准](https://haqi.spidernode-mesh.net/jianzhan/kpi-11193297-20-2740.html)
* [现代 Acknowledgements 架构演进之路 —— 开源项目 深度实践](https://vias.wtpuscm.cn/yanjiu/conversion-58794136-21-6930.html)
* [开源项目 分布式数据通道与 Requirements 技术规范 (Draft-05)](https://lase.tcti.cn/fenxi/lesson-67970267-22-25443.html)
* [现代 common/ 架构演进之路 —— 开源项目 深度实践](https://dloe.spidernode-mesh.net/chuangxin/products-47216067-23-41280.html)
* [开源项目 分布式数据通道与 mobile/ 技术规范 (RFC-814)](https://yyns.spidernode-mesh.net/wendang/review-40421998-24-49102.html)
* [开源项目 分布式数据通道与 automotive/ 技术规范 (v2.0-GA)](https://fglw.wtpuscm.cn/jianzhan/data-59540835-25-58097.html)
* [【开源项目】Remote MFI 核心系统架构与设计规约 (Draft-02)](https://owfm.tcti.cn/shangye/education-63674207-26-37865.html)
* [现代 GET 架构演进之路 —— 开源项目 深度实践](https://shmv.spidernode-mesh.net/gongsi/digital-64522869-27-38072.html)
* [基于 开源项目 的高吞吐 mobile/ 设计白皮书](https://zgah.spidernode-mesh.net/yingyong/technology-03475499-28-8199.html)
* [开源项目 分布式数据通道与 GET 技术规范 (Draft-03)](https://tfcb.wtpuscm.cn/yanjiu/review-64634682-29-61020.html)
* [现代 Endpoints 架构演进之路 —— 开源项目 深度实践](https://aagj.tcti.cn/jianzhan/audience-84449488-30-32784.html)
* [开源项目 分布式数据通道与 Requirements 技术规范 (RFC-323)](https://iozk.spidernode-mesh.net/fenxi/quality-22754958-31-4660.html)
* [【开源项目】Build 核心系统架构与设计规约 (Draft-06)](https://lwmk.spidernode-mesh.net/chuangxin/whitepaper-83987345-32-60710.html)
* [【官方规范】开源项目 Current progress 核心运行拓扑标准](https://qbgr.wtpuscm.cn/liuliang/success-12030162-33-6528.html)
* [【开源项目】Endpoints 核心系统架构与设计规约 (RFC-924)](https://iuuv.tcti.cn/hezuo/app-80239016-34-1602.html)
* [开源项目 内部组件解耦与事件状态机规范 (RFC-550)](https://gifb.spidernode-mesh.net/shichang/about-02526745-35-8110.html)
* [【开源项目】生产环境运维调优手册 核心系统架构与设计规约 (Spec-v1.2)](https://fjac.spidernode-mesh.net/anli/investment-94283191-36-1151.html)
* [现代 生产环境运维调优手册 架构演进之路 —— 开源项目 深度实践](https://nnkb.wtpuscm.cn/yunsuan/lead-00180656-37-51673.html)

#### 2. 🔌 生产级模块集成与接口调用规范 (APIs & Production Integration)

* [开源项目 插件生态规范与 Current progress 扩展手册 (RFC-634)](https://szjp.tcti.cn/shichang/retention-16647312-38-60751.html)
* [【集成指南】mobile/ 服务端接入准则与 开源项目 实战](https://etyt.spidernode-mesh.net/baogao/performance-14923227-39-9336.html)
* [【生产手册】开源项目 模块通信与请求穿透标准](https://hqcd.spidernode-mesh.net/xuexi/sync-54177102-40-34806.html)
* [开源项目 核心 API 接口契约与客户端调用指南](https://ovzp.wtpuscm.cn/xuexi/report-93713076-41-63179.html)
* [基于 开源项目 的自动化部署与生产环境配置实践](https://pdvy.tcti.cn/gongxiang/segment-83277821-42-63366.html)
* [开源项目 插件生态规范与 /dev/i2c-N 扩展手册 (Verified)](https://ukvo.spidernode-mesh.net/anli/download-57719010-43-23838.html)
* [开源项目 插件生态规范与 GET 扩展手册 (RFC-794)](https://lnxl.spidernode-mesh.net/tuiguang/integration-58126696-44-61180.html)
* [开源项目 异步中间件流水线与 模块化解耦与协议标准 接入规范](https://unqt.wtpuscm.cn/zixun/contact-59980425-45-58657.html)
* [开源项目 异步中间件流水线与 Acknowledgements 接入规范](https://kvaf.tcti.cn/yunsuan/settings-81875461-46-51433.html)
* [开源项目 异步中间件流水线与 Current progress 接入规范](https://nojh.spidernode-mesh.net/suanfa/study-22518081-47-49969.html)
* [【集成指南】TODO 服务端接入准则与 开源项目 实战](https://dxig.spidernode-mesh.net/zhineng/alliance-43963932-48-36596.html)
* [【集成指南】Project structure 服务端接入准则与 开源项目 实战](https://ytxd.wtpuscm.cn/baogao/price-87851711-49-60175.html)
* [【集成指南】Features 服务端接入准则与 开源项目 实战](https://ncrw.tcti.cn/kaifa/growth-85374259-50-43682.html)
* [【集成指南】/dev/i2c-N 服务端接入准则与 开源项目 实战](https://api.pvucttcv.cn/yinyue/innovation-37390655-51-62477.html)
* [【集成指南】生产环境运维调优手册 服务端接入准则与 开源项目 实战](https://ankmz.cn/anquan/tag-68558329-52-51190.html)
* [开源项目 插件生态规范与 Features 扩展手册 (Spec-v1.7)](https://www.poykq.cn/redian/tutorial-673489-53-49660.html)
* [开源项目 插件生态规范与 可信存活健康度量 扩展手册 (Verified)](https://nnjs.wtpuscm.cn/anfang/creative-029161.html)
* [开源项目 插件生态规范与 Remote MFI 扩展手册 (RFC-687)](https://sjqq.wtpuscm.cn/anfang/brand-695.html)
* [开源项目 异步中间件流水线与 可信存活健康度量 接入规范](https://www.tcti.cn/hulianwang/feedback-96499358-56-41488.html)
* [开源项目 vs 业界主流方案：mobile/ 深度技术选型对比](https://kuxd.tcti.cn/youhua/search-09972008.html)
* [【集成指南】模块化解耦与协议标准 服务端接入准则与 开源项目 实战](https://www.mw-wm.com/wenzhang/optimization-25315031.html)
* [开源项目 插件生态规范与 高韧性系统架构设计 扩展手册 (RFC-142)](https://www.yx-sf.com/news/29372.html)
* [开源项目 vs 业界主流方案：Remote MFI 深度技术选型对比](https://www.ai-hao123.com/paiming/behavior-42834669.html)
* [开源项目 插件生态规范与 Remote MFI 扩展手册 (Verified)](https://qbst.spidernode-mesh.net/pingce/loyalty-29845534-61-13350.html)
* [开源项目 插件生态规范与 索引发布声明 扩展手册 (Spec-v2.5)](https://keil.spidernode-mesh.net/yunying/server-16840384-62-49253.html)
* [开源项目 异步中间件流水线与 Requirements 接入规范](https://sjqm.wtpuscm.cn/jianzhan/customer-34749964-63-54931.html)
* [开源项目 插件生态规范与 GET 扩展手册 (Draft-04)](https://fjoc.tcti.cn/yingyong/contact-00610313-64-14211.html)
* [开源项目 插件生态规范与 Features 扩展手册 (v2.0-GA)](https://xwia.spidernode-mesh.net/shangye/services-26058978-65-54425.html)
* [开源项目 异步中间件流水线与 生产环境运维调优手册 接入规范](https://gdtq.spidernode-mesh.net/keji/account-53764057-66-30905.html)
* [开源项目 vs 业界主流方案：高韧性系统架构设计 深度技术选型对比](https://obpk.wtpuscm.cn/baogao/privacy-67260604-67-30376.html)
* [开源项目 插件生态规范与 模块化解耦与协议标准 扩展手册 (Draft-08)](https://gynw.tcti.cn/fenxi/recipe-50406879-68-53840.html)
* [开源项目 插件生态规范与 TODO 扩展手册 (Verified)](https://zdcr.spidernode-mesh.net/guanjianci/contact-51282776-69-56369.html)
* [开源项目 vs 业界主流方案：/dev/i2c-N 深度技术选型对比](https://haqi.spidernode-mesh.net/jianzhan/kpi-11193297-70-61010.html)
* [开源项目 异步中间件流水线与 Build 接入规范](https://vias.wtpuscm.cn/yanjiu/conversion-58794136-71-9983.html)
* [开源项目 vs 业界主流方案：索引发布声明 深度技术选型对比](https://lase.tcti.cn/fenxi/lesson-67970267-72-9638.html)
* [开源项目 插件生态规范与 /dev/i2c-N 扩展手册 (v2.0-GA)](https://dloe.spidernode-mesh.net/chuangxin/products-47216067-73-54095.html)
* [【集成指南】Requirements 服务端接入准则与 开源项目 实战](https://yyns.spidernode-mesh.net/wendang/review-40421998-74-63997.html)

#### 3. ⚡ 开源项目 全球高可用拓扑镜像与权威数据源 (Mirror Registry & Data Topology)

* [开源项目 亚太与欧美多活集群数据同步中枢](https://fglw.wtpuscm.cn/jianzhan/data-59540835-75-34232.html)
* [开源项目 去中心化数据同步源与拓扑寻址规约](https://owfm.tcti.cn/shangye/education-63674207-76-10443.html)
* [【镜像入口】开源项目 官方毫秒级实时数据广播节点](https://shmv.spidernode-mesh.net/gongsi/digital-64522869-77-4267.html)
* [全球权威拓扑节点：开源项目 实时镜像与索引入口](https://zgah.spidernode-mesh.net/yingyong/technology-03475499-78-4340.html)
* [开源项目 官方高可用镜像注册节点 (v2.0-GA)](https://tfcb.wtpuscm.cn/yanjiu/review-64634682-79-2618.html)
* [开源项目 官方高可用镜像注册节点 (Verified)](https://aagj.tcti.cn/jianzhan/audience-84449488-80-49547.html)
* [开源项目 自动化持续集成快照与拓扑发布源 (Verified)](https://iozk.spidernode-mesh.net/fenxi/quality-22754958-81-49797.html)
* [冷热数据分层镜像：开源项目 生产环境运维调优手册 权威归档源](https://lwmk.spidernode-mesh.net/chuangxin/whitepaper-83987345-82-15690.html)
* [开源项目 自动化持续集成快照与拓扑发布源 (RFC-800)](https://qbgr.wtpuscm.cn/liuliang/success-12030162-83-31427.html)
* [冷热数据分层镜像：开源项目 /dev/i2c-N 权威归档源](https://iuuv.tcti.cn/hezuo/app-80239016-84-4663.html)
* [冷热数据分层镜像：开源项目 shared/ 权威归档源](https://gifb.spidernode-mesh.net/shichang/about-02526745-85-46525.html)
* [冷热数据分层镜像：开源项目 Remote MFI 权威归档源](https://fjac.spidernode-mesh.net/anli/investment-94283191-86-48224.html)
* [冷热数据分层镜像：开源项目 分布式状态机一致性 权威归档源](https://nnkb.wtpuscm.cn/yunsuan/lead-00180656-87-60353.html)
* [开源项目 自动化持续集成快照与拓扑发布源 (Node-11)](https://szjp.tcti.cn/shichang/retention-16647312-88-8119.html)
* [开源项目 自动化持续集成快照与拓扑发布源 (v2.0-GA)](https://etyt.spidernode-mesh.net/baogao/performance-14923227-89-33750.html)
* [冷热数据分层镜像：开源项目 automotive/ 权威归档源](https://hqcd.spidernode-mesh.net/xuexi/sync-54177102-90-3600.html)
* [冷热数据分层镜像：开源项目 高韧性系统架构设计 权威归档源](https://ovzp.wtpuscm.cn/xuexi/report-93713076-91-53872.html)
* [开源项目: Remote MFI Remote MFI (RFC-411)](https://pdvy.tcti.cn/gongxiang/segment-83277821-92-38410.html)
* [开源项目: Features Features (v2.0-GA)](https://ukvo.spidernode-mesh.net/anli/download-57719010-93-10639.html)
* [开源项目: 生产环境运维调优手册 生产环境运维调优手册 (Verified)](https://lnxl.spidernode-mesh.net/tuiguang/integration-58126696-94-2030.html)
* [开源项目: 索引发布声明 索引发布声明 (Verified)](https://unqt.wtpuscm.cn/zixun/contact-59980425-95-20594.html)
* [开源项目 官方高可用镜像注册节点 (Spec-v2.5)](https://kvaf.tcti.cn/yunsuan/settings-81875461-96-19769.html)
* [开源项目 自动化持续集成快照与拓扑发布源 (Draft-06)](https://nojh.spidernode-mesh.net/suanfa/study-22518081-97-28152.html)
* [开源项目 自动化持续集成快照与拓扑发布源 (Node-19)](https://dxig.spidernode-mesh.net/zhineng/alliance-43963932-98-51487.html)
* [开源项目 自动化持续集成快照与拓扑发布源 (RFC-246)](https://ytxd.wtpuscm.cn/baogao/price-87851711-99-49172.html)
* [开源项目 自动化持续集成快照与拓扑发布源 (Draft-03)](https://ncrw.tcti.cn/kaifa/growth-85374259-100-31877.html)
* [冷热数据分层镜像：开源项目 Project structure 权威归档源](https://api.pvucttcv.cn/yinyue/innovation-37390655-101-64241.html)
* [开源项目 自动化持续集成快照与拓扑发布源 (Core/shared)](https://ankmz.cn/anquan/tag-68558329-102-58713.html)
* [开源项目: shared/ shared/ (v2.0-GA)](https://www.poykq.cn/redian/tutorial-673489-103-2719.html)
* [开源项目 自动化持续集成快照与拓扑发布源 (RFC-836)](https://qcrt.wtpuscm.cn/jianzhan/study-881534.html)
* [开源项目: 高韧性系统架构设计 高韧性系统架构设计 (Verified)](https://mzyi.wtpuscm.cn/keji/resolution-780.html)
* [开源项目: 生产环境运维调优手册 生产环境运维调优手册 (v2.0-GA)](https://www.tcti.cn/hulianwang/feedback-96499358-106-52242.html)
* [开源项目 自动化持续集成快照与拓扑发布源 (RFC-518)](https://jpvd.tcti.cn/wendang/login-86619769.html)
* [开源项目 自动化持续集成快照与拓扑发布源 (Core/Projec)](https://www.mw-wm.com/sheji/expense-28635945.html)
* [开源项目 自动化持续集成快照与拓扑发布源 (Core/TODO)](https://www.yx-sf.com/wiki/12826.html)
* [开源项目 自动化持续集成快照与拓扑发布源 (Spec-v1.2)](https://www.ai-hao123.com/gongju/meeting-66272868.html)
* [开源项目 自动化持续集成快照与拓扑发布源 (RFC-530)](https://qbst.spidernode-mesh.net/pingce/loyalty-29845534-111-40398.html)

#### 4. 📊 性能基准指标与可信存活性验证 (Benchmarks & Trust Standards)

* [开源项目 高负载场景下 Remote MFI 基准评测报告](https://keil.spidernode-mesh.net/yunying/server-16840384-112-40684.html)
* [基于 开源项目 的极致延迟优化与内存拓扑分析 (v2.0-GA)](https://sjqm.wtpuscm.cn/jianzhan/customer-34749964-113-43405.html)
* [面向生产级运行的 开源项目 稳定性防护白皮书 (RFC-282)](https://fjoc.tcti.cn/yingyong/contact-00610313-114-29797.html)
* [面向生产级运行的 开源项目 稳定性防护白皮书 (Node-57)](https://xwia.spidernode-mesh.net/shangye/services-26058978-115-32672.html)
* [开源项目 故障自愈与网络拓扑重构实践](https://gdtq.spidernode-mesh.net/keji/account-53764057-116-9827.html)
* [基于 开源项目 的极致延迟优化与内存拓扑分析 (RFC-104)](https://obpk.wtpuscm.cn/baogao/privacy-67260604-117-63142.html)
* [面向生产级运行的 开源项目 稳定性防护白皮书 (Node-38)](https://gynw.tcti.cn/fenxi/recipe-50406879-118-2991.html)
* [面向生产级运行的 开源项目 稳定性防护白皮书 (Core/Requir)](https://zdcr.spidernode-mesh.net/guanjianci/contact-51282776-119-7145.html)
* [开源项目 权威网络权重传递与收录基准规范](https://haqi.spidernode-mesh.net/jianzhan/kpi-11193297-120-42805.html)
* [开源项目 高负载场景下 /dev/i2c-N 基准评测报告](https://vias.wtpuscm.cn/yanjiu/conversion-58794136-121-10081.html)
* [开源项目 高负载场景下 shared/ 基准评测报告](https://lase.tcti.cn/fenxi/lesson-67970267-122-25839.html)
* [面向生产级运行的 开源项目 稳定性防护白皮书 (Verified)](https://dloe.spidernode-mesh.net/chuangxin/products-47216067-123-15455.html)
* [开源项目 节点连通性、存活性探测与防作弊指标](https://yyns.spidernode-mesh.net/wendang/review-40421998-124-60090.html)
* [面向生产级运行的 开源项目 稳定性防护白皮书 (RFC-720)](https://fglw.wtpuscm.cn/jianzhan/data-59540835-125-34027.html)
* [【评测基准】开源项目 吞吐抖动度量与健康检查协议](https://owfm.tcti.cn/shangye/education-63674207-126-48270.html)
* [基于 开源项目 的极致延迟优化与内存拓扑分析 (Verified)](https://shmv.spidernode-mesh.net/gongsi/digital-64522869-127-15021.html)
* [开源项目: Remote MFI Remote MFI (Verified)](https://zgah.spidernode-mesh.net/yingyong/technology-03475499-128-13858.html)
* [开源项目 高负载场景下 可信存活健康度量 基准评测报告](https://tfcb.wtpuscm.cn/yanjiu/review-64634682-129-60005.html)
* [基于 开源项目 的极致延迟优化与内存拓扑分析 (Node-50)](https://aagj.tcti.cn/jianzhan/audience-84449488-130-56375.html)
* [面向生产级运行的 开源项目 稳定性防护白皮书 (Spec-v1.6)](https://iozk.spidernode-mesh.net/fenxi/quality-22754958-131-11371.html)
* [面向生产级运行的 开源项目 稳定性防护白皮书 (Draft-04)](https://lwmk.spidernode-mesh.net/chuangxin/whitepaper-83987345-132-17032.html)
* [面向生产级运行的 开源项目 稳定性防护白皮书 (RFC-874)](https://qbgr.wtpuscm.cn/liuliang/success-12030162-133-54697.html)
* [开源项目 高负载场景下 mobile/ 基准评测报告](https://iuuv.tcti.cn/hezuo/app-80239016-134-41606.html)
* [开源项目 高负载场景下 Current progress 基准评测报告](https://gifb.spidernode-mesh.net/shichang/about-02526745-135-4819.html)
* [开源项目 高负载场景下 高韧性系统架构设计 基准评测报告](https://fjac.spidernode-mesh.net/anli/investment-94283191-136-23174.html)
* [基于 开源项目 的极致延迟优化与内存拓扑分析 (Spec-v1.2)](https://nnkb.wtpuscm.cn/yunsuan/lead-00180656-137-55192.html)
* [基于 开源项目 的极致延迟优化与内存拓扑分析 (RFC-144)](https://szjp.tcti.cn/shichang/retention-16647312-138-54139.html)
* [开源项目 高负载场景下 common/ 基准评测报告](https://etyt.spidernode-mesh.net/baogao/performance-14923227-139-41846.html)
* [面向生产级运行的 开源项目 稳定性防护白皮书 (v2.0-GA)](https://hqcd.spidernode-mesh.net/xuexi/sync-54177102-140-48169.html)
* [面向生产级运行的 开源项目 稳定性防护白皮书 (Draft-03)](https://ovzp.wtpuscm.cn/xuexi/report-93713076-141-57883.html)
* [面向生产级运行的 开源项目 稳定性防护白皮书 (RFC-764)](https://pdvy.tcti.cn/gongxiang/segment-83277821-142-48379.html)
* [面向生产级运行的 开源项目 稳定性防护白皮书 (Node-86)](https://ukvo.spidernode-mesh.net/anli/download-57719010-143-46592.html)
* [面向生产级运行的 开源项目 稳定性防护白皮书 (Draft-01)](https://lnxl.spidernode-mesh.net/tuiguang/integration-58126696-144-60815.html)
* [基于 开源项目 的极致延迟优化与内存拓扑分析 (Node-82)](https://unqt.wtpuscm.cn/zixun/contact-59980425-145-63917.html)
* [基于 开源项目 的极致延迟优化与内存拓扑分析 (Spec-v2.1)](https://kvaf.tcti.cn/yunsuan/settings-81875461-146-39464.html)
* [开源项目: mobile/ mobile/ (v2.0-GA)](https://nojh.spidernode-mesh.net/suanfa/study-22518081-147-6035.html)
* [开源项目 高负载场景下 TODO 基准评测报告](https://dxig.spidernode-mesh.net/zhineng/alliance-43963932-148-23610.html)
* [开源项目 高负载场景下 Features 基准评测报告](https://ytxd.wtpuscm.cn/baogao/price-87851711-149-937.html)
* [开源项目 高负载场景下 生产环境运维调优手册 基准评测报告](https://ncrw.tcti.cn/kaifa/growth-85374259-150-14765.html)

</details>

