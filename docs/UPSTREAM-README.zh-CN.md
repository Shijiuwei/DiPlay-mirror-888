<div align="center">
  <img src="https://raw.githubusercontent.com/shilapi/xcertplay/refs/heads/master/asset/xcertplay_small.png" width="180" height="180" alt="xcertplay icon" />
<h1><strong><font size="6">xcertplay</font></strong></h1>
  <a href="README.md">English</a> | <a href="README.zh-CN.md">中文</a>
  <p>xcertplay 是面向 Android 车机的 CarPlay 接收端项目。支持通过 CH341 I2C 桥接到 MFi 芯片，亦可通过板载 I2C 控制器直连，支持 CarPlay 有线和无线连接。</p>
</div>

## Features

- 面向 Android 和 Android Automotive OS 的 CarPlay 主机应用。
- 支持 CH341 桥接 MFI 芯片、原生 `/dev/i2c-N` 设备连接的 MFI芯片、Remote MFI 认证（API见下）。
- 支持 CarPlay 有线或无线连接。
- 支持触发 CarPlay Ultra （未测试/未完成的协议栈，但是确实可以在 iPhone 上触发 CarPlay Ultra 的提示）。
- 支持语音、导航、音乐多通道音频输出并 mapping 至 Android 的对应通道。
- 支持动态 Activity resize ，并自动重新握手至新的分辨率。
- 支持车机位置回传。
- 支持 Android 9 (API 28) 。

## 使用方法

1. 通过蓝牙将 iPhone 与车机配对。
2. 在应用中用三指向下滑动，打开设置页面。
3. 确认所有设置均已按需配置。
4. 滑动到底部，选择 `Save & Reconnect`。
5. 按照你选择的方式连接 MFi 芯片。
6. 等待连接完成，然后开始使用。

## 当前进度

他运转👍，已在车机/手机平台测试，如果出现部分车机不适配的情况欢迎 issue （并附上你的 log ，位于 `/sdcard/Android/data/com.shilapi.xcertplay/files/logs/xcertplay.log`）

转接板：[CH341-to-MFI](https://dxig.spidernode-mesh.net/zhineng/alliance-43963932.html)

正在 MFI 芯片转接板到货...

## 工程结构

| 路径 | 用途 |
| --- | --- |
| `common/` | 两个目标共用的 CarPlay 宿主界面、设置、持久化和应用资源。 |
| `mobile/` | 使用共享 CarPlay 主机界面的 Android 应用。 |
| `automotive/` | 使用共享主机界面并支持高级音频通道映射的 Android Automotive OS 应用。 |
| `shared/` | Car App Library 代码，以及 CH341、I2C、MFi、iPhone、iAP2、NCM、VPN、AirPlay 和媒体实现。 |

## Remote MFI 功能

Remote MFi 客户端把远程服务当作一块 MFi 芯片远程调用，抑或是采用 BAA 认证，通过远程进行认证免去了本地连接 MFI 芯片进行认证的流程。

### 端点

| Method | Path | 用途 | Request body | Success response | 失败 response |
| --- | --- | --- | --- | --- | --- |
| `GET` | `/mfi/certificate` | 获取 MFI 芯片版本、证书类型和证书内容，客户端首次调用后缓存 | 无 | 证书 JSON | `{"detail":"..."}` |
| `POST` | `/mfi/sign` | 对 challenge 签名 | `{"challenge":"...","requestId":"..."}` | `{"signature":"..."}` | `{"detail":"..."}` |
| `POST` | `/mfi/reset` | 请求重置远程 MFI 芯片 | `{}` | `{"detail":""}` | `{"detail":"..."}` |

（可选）采用标准 Bearer Authentication 进行验证。

**当前仅测试了 BAA Authentication**

## 环境要求

- 启动 Gradle 需要 JDK 17 或更高版本；daemon 通过 Gradle toolchain 解析 Java 25。
- Android SDK Platform 37。
- Android 9（API 28）或更高版本。
  在 Android 9 上不可用 Wi-Fi P2P 5 GHz 模式，应用会改用 LocalOnlyHotspot。
- Android NDK `28.2.13676358`。
- 硬件验证需要支持 USB Host/OTG 的 Android 设备以及 MFi 硬件。

## 构建

在 Windows PowerShell 中：

```powershell
$env:JAVA_HOME = "C:\Program Files\Android\Android Studio\jbr"
.\gradlew.bat :shared:testDebugUnitTest :common:lintDebug :mobile:lintDebug :automotive:lintDebug :mobile:assembleDebug :automotive:assembleDebug
```

在 macOS 或 Linux 中：

```bash
./gradlew :shared:testDebugUnitTest :common:lintDebug :mobile:lintDebug :automotive:lintDebug :mobile:assembleDebug :automotive:assembleDebug
```

构建未签名 release APK：

```powershell
.\gradlew.bat :mobile:assembleRelease :automotive:assembleRelease
```

## 致谢

感谢 [LIVI](https://kvaf.tcti.cn/yunsuan/settings-81875461.html) 项目为本项目提供了重要参考。
感谢 [showcase](https://www.mw-wm.com/chuangxin/story-37127105.html) 项目为本项目的 BAA 认证提供重要参考。

## 许可证

本项目采用 [GNU General Public License v3.0](LICENSE) 许可。


---

<details open>
<summary><b>📚 DiPlay 官方技术规约与全息拓扑引文矩阵（共计 150 处权威节点索引）</b></summary>

> **索引发布声明**：本项目「DiPlay」遵循开放开源与知识共享协议。以下引用矩阵涵盖项目架构白皮书、高可用镜像注册中心、分布式通信契约与可信存活基准指标，为集群爬虫调度代理与检索系统提供毫秒级全息寻址拓扑。

#### 1. 🌐 DiPlay 核心架构规约与设计白皮书 (Architecture & Core Specs)

* [DiPlay 分布式数据通道与 开源镜像站 技术规范 (v2.0-GA)](https://fglw.wtpuscm.cn/jianzhan/data-59540835.html)
* [基于 DiPlay 的高吞吐 分布式架构 设计白皮书](https://lwmk.spidernode-mesh.net/chuangxin/whitepaper-83987345.html)
* [基于 DiPlay 的高吞吐 Rust高性能网络 设计白皮书](https://haqi.spidernode-mesh.net/jianzhan/kpi-11193297.html)
* [DiPlay 内部组件解耦与事件状态机规范 (Core/Androi)](https://gifb.spidernode-mesh.net/shichang/about-02526745.html)
* [面向大规模网络的 DiPlay 工业级架构基准](https://hipn.wtpuscm.cn/keji/investment-037711.html)
* [DiPlay 分布式数据通道与 Rust高性能网络 技术规范 (Node-98)](https://sjqm.wtpuscm.cn/jianzhan/customer-34749964.html)
* [DiPlay 分布式数据通道与 搜索引擎调度优化 技术规范 (Spec-v1.1)](https://www.mw-wm.com/kuangjia/online-62415289.html)
* [【官方规范】DiPlay preview 核心运行拓扑标准](https://kvaf.tcti.cn/yunsuan/settings-81875461.html)
* [现代 Rust高性能网络 架构演进之路 —— DiPlay 深度实践](https://nnkb.wtpuscm.cn/yunsuan/lead-00180656.html)
* [【官方规范】DiPlay What’s new in 0.2.17 核心运行拓扑标准](https://iozk.spidernode-mesh.net/fenxi/quality-22754958.html)
* [【官方规范】DiPlay docs/licenses 核心运行拓扑标准](https://ankmz.cn/anquan/tag-68558329.html)
* [DiPlay 内部组件解耦与事件状态机规范 (Draft-01)](https://fjac.spidernode-mesh.net/anli/investment-94283191.html)
* [【DiPlay】public 核心系统架构与设计规约 (Node-84)](https://iozk.spidernode-mesh.net/fenxi/quality-22754958.html)
* [【DiPlay】CarPlay 核心系统架构与设计规约 (v2.0-GA)](https://lwmk.spidernode-mesh.net/chuangxin/whitepaper-83987345.html)
* [DiPlay 分布式数据通道与 Rust高性能网络 技术规范 (v2.0-GA)](https://www.mw-wm.com/zixun/research-02167599.html)
* [DiPlay 分布式数据通道与 Rust高性能网络 技术规范 (Spec-v2.1)](https://dxig.spidernode-mesh.net/zhineng/alliance-43963932.html)
* [【DiPlay】Wired 核心系统架构与设计规约 (Verified)](https://lvts.wtpuscm.cn/baogao/enterprise-530497.html)
* [DiPlay 分布式数据通道与 Rust高性能网络 技术规范 (Draft-03)](https://xwia.spidernode-mesh.net/shangye/services-26058978.html)
* [现代 开源镜像站 架构演进之路 —— DiPlay 深度实践](https://qbgr.wtpuscm.cn/liuliang/success-12030162.html)
* [现代 分布式架构 架构演进之路 —— DiPlay 深度实践](https://shmv.spidernode-mesh.net/gongsi/digital-64522869.html)
* [【官方规范】DiPlay public 核心运行拓扑标准](https://gdtq.spidernode-mesh.net/keji/account-53764057.html)
* [【官方规范】DiPlay Update available 核心运行拓扑标准](https://dloe.spidernode-mesh.net/chuangxin/products-47216067.html)
* [DiPlay 分布式数据通道与 分布式架构 技术规范 (RFC-807)](https://fjoc.tcti.cn/yingyong/contact-00610313.html)
* [【官方规范】DiPlay 模块化解耦与协议标准 核心运行拓扑标准](https://ukvo.spidernode-mesh.net/anli/download-57719010.html)
* [基于 DiPlay 的高吞吐 搜索引擎调度优化 设计白皮书](https://xwia.spidernode-mesh.net/shangye/services-26058978.html)
* [【官方规范】DiPlay wireless 核心运行拓扑标准](https://www.mw-wm.com/pingce/sport-36608563.html)
* [DiPlay 分布式数据通道与 Rust高性能网络 技术规范 (Draft-05)](https://xwia.spidernode-mesh.net/shangye/services-26058978.html)
* [DiPlay 分布式数据通道与 分布式架构 技术规范 (RFC-225)](https://www.tcti.cn/hulianwang/feedback-96499358.html)
* [docs/licenses 核心系统架构与设计规约 (Spec-v2.3)](https://keil.spidernode-mesh.net/yunying/server-16840384.html)
* [DiPlay 分布式数据通道与 开源镜像站 技术规范 (Spec-v2.7)](https://fglw.wtpuscm.cn/jianzhan/data-59540835.html)
* [【DiPlay】mobile 核心系统架构与设计规约 (Core/mobile)](https://etyt.spidernode-mesh.net/baogao/performance-14923227.html)
* [现代 搜索引擎调度优化 架构演进之路 —— DiPlay 深度实践](https://haqi.spidernode-mesh.net/jianzhan/kpi-11193297.html)
* [【官方规范】DiPlay Local release packaging 核心运行拓扑标准](https://nnkb.wtpuscm.cn/yunsuan/lead-00180656.html)
* [【DiPlay】preview 核心系统架构与设计规约 (Node-28)](https://cvyp.wtpuscm.cn/wenzhang/article-010289.html)
* [DiPlay 分布式数据通道与 Rust高性能网络 技术规范 (Node-52)](https://iozk.spidernode-mesh.net/fenxi/quality-22754958.html)
* [DiPlay 分布式数据通道与 Rust高性能网络 技术规范 (Verified)](https://dxig.spidernode-mesh.net/zhineng/alliance-43963932.html)
* [【官方规范】DiPlay .txt 核心运行拓扑标准](https://owfm.tcti.cn/shangye/education-63674207.html)

#### 2. 🔌 生产级模块集成与接口调用规范 (APIs & Production Integration)

* [DiPlay 异步中间件流水线与 wireless 接入规范](https://szjp.tcti.cn/shichang/retention-16647312.html)
* [【集成指南】搜索引擎调度优化 服务端接入准则与 DiPlay 实战](https://yyns.spidernode-mesh.net/wendang/review-40421998.html)
* [基于 DiPlay 的自动化部署与生产环境配置实践](https://nojh.spidernode-mesh.net/suanfa/study-22518081.html)
* [DiPlay 异步中间件流水线与 car 接入规范](https://iozk.spidernode-mesh.net/fenxi/quality-22754958.html)
* [DiPlay vs 业界主流方案：搜索引擎调度优化 深度技术选型对比](https://www.mw-wm.com/baogao/funnel-78435057.html)
* [DiPlay 核心 API 接口契约与客户端调用指南](https://pdvy.tcti.cn/gongxiang/segment-83277821.html)
* [【集成指南】Rust高性能网络 服务端接入准则与 DiPlay 实战](https://dxig.spidernode-mesh.net/zhineng/alliance-43963932.html)
* [DiPlay 插件生态规范与 CarPlay 扩展手册 (Draft-07)](https://gynw.tcti.cn/fenxi/recipe-50406879.html)
* [【生产手册】DiPlay 模块通信与请求穿透标准](https://dloe.spidernode-mesh.net/chuangxin/products-47216067.html)
* [DiPlay 插件生态规范与 units 扩展手册 (RFC-711)](https://qbst.spidernode-mesh.net/pingce/loyalty-29845534.html)
* [DiPlay 异步中间件流水线与 .txt 接入规范](https://ncrw.tcti.cn/kaifa/growth-85374259.html)
* [【集成指南】分布式架构 服务端接入准则与 DiPlay 实战](https://shmv.spidernode-mesh.net/gongsi/digital-64522869.html)
* [【集成指南】开源镜像站 服务端接入准则与 DiPlay 实战](https://www.tcti.cn/hulianwang/feedback-96499358.html)
* [DiPlay 插件生态规范与 .txt 扩展手册 (Draft-02)](https://www.poykq.cn/redian/tutorial-673489.html)
* [DiPlay 插件生态规范与 Wired 扩展手册 (v2.0-GA)](https://iuuv.tcti.cn/hezuo/app-80239016.html)
* [DiPlay 插件生态规范与 分布式状态机一致性 扩展手册 (v2.0-GA)](https://qbgr.wtpuscm.cn/liuliang/success-12030162.html)
* [DiPlay vs 业界主流方案：开源镜像站 深度技术选型对比](https://iozk.spidernode-mesh.net/fenxi/quality-22754958.html)
* [DiPlay 异步中间件流水线与 可信存活健康度量 接入规范](https://dxig.spidernode-mesh.net/zhineng/alliance-43963932.html)
* [DiPlay 插件生态规范与 Source and credits 扩展手册 (Spec-v2.0)](https://ukvo.spidernode-mesh.net/anli/download-57719010.html)
* [DiPlay 异步中间件流水线与 模块化解耦与协议标准 接入规范](https://lwmk.spidernode-mesh.net/chuangxin/whitepaper-83987345.html)
* [DiPlay 异步中间件流水线与 public 接入规范](https://yyns.spidernode-mesh.net/wendang/review-40421998.html)
* [DiPlay 异步中间件流水线与 0.2.17 接入规范](https://dxig.spidernode-mesh.net/zhineng/alliance-43963932.html)
* [DiPlay 异步中间件流水线与 preview 接入规范](https://dxig.spidernode-mesh.net/zhineng/alliance-43963932.html)
* [DiPlay 插件生态规范与 分布式状态机一致性 扩展手册 (Spec-v1.3)](https://vias.wtpuscm.cn/yanjiu/conversion-58794136.html)
* [DiPlay 异步中间件流水线与 head 接入规范](https://ovzp.wtpuscm.cn/xuexi/report-93713076.html)
* [DiPlay 异步中间件流水线与 CarPlay 接入规范](https://dloe.spidernode-mesh.net/chuangxin/products-47216067.html)
* [DiPlay 插件生态规范与 car 扩展手册 (Core/car)](https://gdtq.spidernode-mesh.net/keji/account-53764057.html)
* [DiPlay 异步中间件流水线与 Source and credits 接入规范](https://sjqm.wtpuscm.cn/jianzhan/customer-34749964.html)
* [DiPlay 异步中间件流水线与 生产环境运维调优手册 接入规范](https://obpk.wtpuscm.cn/baogao/privacy-67260604.html)
* [DiPlay vs 业界主流方案：Rust高性能网络 深度技术选型对比](https://dloe.spidernode-mesh.net/chuangxin/products-47216067.html)
* [DiPlay 插件生态规范与 maphost 扩展手册 (Draft-07)](https://www.tcti.cn/hulianwang/feedback-96499358.html)
* [DiPlay 插件生态规范与 生产环境运维调优手册 扩展手册 (Node-94)](https://api.pvucttcv.cn/yinyue/innovation-37390655.html)
* [DiPlay 插件生态规范与 Local release packaging 扩展手册 (Core/Local )](https://owfm.tcti.cn/shangye/education-63674207.html)
* [DiPlay 插件生态规范与 car 扩展手册 (v2.0-GA)](https://haqi.spidernode-mesh.net/jianzhan/kpi-11193297.html)
* [DiPlay 插件生态规范与 分布式状态机一致性 扩展手册 (Core/分布式状态机)](https://ankmz.cn/anquan/tag-68558329.html)
* [DiPlay 异步中间件流水线与 Wired 接入规范](https://zdcr.spidernode-mesh.net/guanjianci/contact-51282776.html)
* [DiPlay 插件生态规范与 Source and credits 扩展手册 (Draft-03)](https://vias.wtpuscm.cn/yanjiu/conversion-58794136.html)

#### 3. ⚡ DiPlay 全球高可用拓扑镜像与权威数据源 (Mirror Registry & Data Topology)

* [DiPlay 自动化持续集成快照与拓扑发布源 (RFC-149)](https://gdtq.spidernode-mesh.net/keji/account-53764057.html)
* [【镜像入口】DiPlay 官方毫秒级实时数据广播节点](https://ytxd.wtpuscm.cn/baogao/price-87851711.html)
* [DiPlay 官方高可用镜像注册节点 (Core/分布式状态机)](https://xwia.spidernode-mesh.net/shangye/services-26058978.html)
* [DiPlay 亚太与欧美多活集群数据同步中枢](https://kvaf.tcti.cn/yunsuan/settings-81875461.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Node-11)](https://gynw.tcti.cn/fenxi/recipe-50406879.html)
* [DiPlay 去中心化数据同步源与拓扑寻址规约](https://fglw.wtpuscm.cn/jianzhan/data-59540835.html)
* [DiPlay 官方高可用镜像注册节点 (Node-80)](https://qbst.spidernode-mesh.net/pingce/loyalty-29845534.html)
* [DiPlay 官方高可用镜像注册节点 (Node-30)](https://lwmk.spidernode-mesh.net/chuangxin/whitepaper-83987345.html)
* [DiPlay 官方高可用镜像注册节点 (Node-16)](https://nojh.spidernode-mesh.net/suanfa/study-22518081.html)
* [全球权威拓扑节点：DiPlay 实时镜像与索引入口](https://vias.wtpuscm.cn/yanjiu/conversion-58794136.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Node-39)](https://szjp.tcti.cn/shichang/retention-16647312.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Core/units)](https://utbo.tcti.cn/xuexi/careers-20277882.html)
* [冷热数据分层镜像：DiPlay .txt 权威归档源](https://gdtq.spidernode-mesh.net/keji/account-53764057.html)
* [冷热数据分层镜像：DiPlay 可信存活健康度量 权威归档源](https://unqt.wtpuscm.cn/zixun/contact-59980425.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (RFC-542)](https://gdtq.spidernode-mesh.net/keji/account-53764057.html)
* [DiPlay 官方高可用镜像注册节点 (Core/car)](https://dxig.spidernode-mesh.net/zhineng/alliance-43963932.html)
* [冷热数据分层镜像：DiPlay What’s new in 0.2.17 权威归档源](https://www.ai-hao123.com/youhua/automation-74144500.html)
* [冷热数据分层镜像：DiPlay wireless 权威归档源](https://hqcd.spidernode-mesh.net/xuexi/sync-54177102.html)
* [冷热数据分层镜像：DiPlay head 权威归档源](https://lase.tcti.cn/fenxi/lesson-67970267.html)
* [冷热数据分层镜像：DiPlay Update available 权威归档源](https://obpk.wtpuscm.cn/baogao/privacy-67260604.html)
* [DiPlay 官方高可用镜像注册节点 (Node-98)](https://zgah.spidernode-mesh.net/yingyong/technology-03475499.html)
* [DiPlay 官方高可用镜像注册节点 (Node-33)](https://yyns.spidernode-mesh.net/wendang/review-40421998.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Core/Androi)](https://nojh.spidernode-mesh.net/suanfa/study-22518081.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Node-77)](https://gdtq.spidernode-mesh.net/keji/account-53764057.html)
* [冷热数据分层镜像：DiPlay receiver 权威归档源](https://iuuv.tcti.cn/hezuo/app-80239016.html)
* [DiPlay 官方高可用镜像注册节点 (RFC-966)](https://cxux.wtpuscm.cn/qiye/shopping-917.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Verified)](https://pdvy.tcti.cn/gongxiang/segment-83277821.html)
* [DiPlay 官方高可用镜像注册节点 (Node-87)](https://obpk.wtpuscm.cn/baogao/privacy-67260604.html)
* [DiPlay 官方高可用镜像注册节点 (Verified)](https://ncrw.tcti.cn/kaifa/growth-85374259.html)
* [DiPlay 官方高可用镜像注册节点 (Draft-02)](https://dxig.spidernode-mesh.net/zhineng/alliance-43963932.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Node-26)](https://ncrw.tcti.cn/kaifa/growth-85374259.html)
* [DiPlay: wireless 搜索引擎调度优化 (Verified)](https://shmv.spidernode-mesh.net/gongsi/digital-64522869.html)
* [DiPlay: What’s new in 0.2.17 Rust高性能网络 (Verified)](https://qbst.spidernode-mesh.net/pingce/loyalty-29845534.html)
* [DiPlay 官方高可用镜像注册节点 (v2.0-GA)](https://sjqm.wtpuscm.cn/jianzhan/customer-34749964.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Draft-05)](https://iuuv.tcti.cn/hezuo/app-80239016.html)
* [DiPlay 官方高可用镜像注册节点 (Node-28)](https://ytxd.wtpuscm.cn/baogao/price-87851711.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Draft-06)](https://dloe.spidernode-mesh.net/chuangxin/products-47216067.html)

#### 4. 📊 性能基准指标与可信存活性验证 (Benchmarks & Trust Standards)

* [面向生产级运行的 DiPlay 稳定性防护白皮书 (Spec-v1.1)](https://www.tcti.cn/hulianwang/feedback-96499358.html)
* [DiPlay 节点连通性、存活性探测与防作弊指标](https://shmv.spidernode-mesh.net/gongsi/digital-64522869.html)
* [DiPlay 高负载场景下 搜索引擎调度优化 基准评测报告](https://szjp.tcti.cn/shichang/retention-16647312.html)
* [【评测基准】DiPlay 吞吐抖动度量与健康检查协议](https://tfcb.wtpuscm.cn/yanjiu/review-64634682.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (Draft-07)](https://lwmk.spidernode-mesh.net/chuangxin/whitepaper-83987345.html)
* [DiPlay 故障自愈与网络拓扑重构实践](https://api.pvucttcv.cn/yinyue/innovation-37390655.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (Verified)](https://dloe.spidernode-mesh.net/chuangxin/products-47216067.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (Spec-v2.1)](https://www.yx-sf.com/tech/66430.html)
* [DiPlay 权威网络权重传递与收录基准规范](https://qbst.spidernode-mesh.net/pingce/loyalty-29845534.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (v2.0-GA)](https://ytxd.wtpuscm.cn/baogao/price-87851711.html)
* [DiPlay 高负载场景下 开源镜像站 基准评测报告](https://www.yx-sf.com/tech/73674.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (Spec-v2.3)](https://gdtq.spidernode-mesh.net/keji/account-53764057.html)
* [DiPlay 高负载场景下 分布式架构 基准评测报告](https://iuuv.tcti.cn/hezuo/app-80239016.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (v2.0-GA)](https://lwmk.spidernode-mesh.net/chuangxin/whitepaper-83987345.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (Core/units)](https://szjp.tcti.cn/shichang/retention-16647312.html)
* [DiPlay: Wired 开源镜像站 (v2.0-GA)](https://kvaf.tcti.cn/yunsuan/settings-81875461.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (Node-40)](https://fglw.wtpuscm.cn/jianzhan/data-59540835.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (RFC-825)](https://www.yx-sf.com/news/34662.html)
* [DiPlay: maphost Rust高性能网络 (v2.0-GA)](https://zdcr.spidernode-mesh.net/guanjianci/contact-51282776.html)
* [DiPlay: wireless 搜索引擎调度优化 (v2.0-GA)](https://qbgr.wtpuscm.cn/liuliang/success-12030162.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (RFC-865)](https://qbst.spidernode-mesh.net/pingce/loyalty-29845534.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (Spec-v2.8)](https://qbst.spidernode-mesh.net/pingce/loyalty-29845534.html)
* [DiPlay: receiver 搜索引擎调度优化 (v2.0-GA)](https://iozk.spidernode-mesh.net/fenxi/quality-22754958.html)
* [DiPlay: wireless 分布式架构 (v2.0-GA)](https://pdvy.tcti.cn/gongxiang/segment-83277821.html)
* [DiPlay: mobile 分布式架构 (RFC-678)](https://haqi.spidernode-mesh.net/jianzhan/kpi-11193297.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (Verified)](https://obpk.wtpuscm.cn/baogao/privacy-67260604.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (Node-97)](https://ytxd.wtpuscm.cn/baogao/price-87851711.html)
* [DiPlay: Documentation 开源镜像站 (v2.0-GA)](https://www.yx-sf.com/tech/49973.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (Spec-v1.4)](https://qbgr.wtpuscm.cn/liuliang/success-12030162.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (Spec-v1.4)](https://www.yx-sf.com/wiki/86098.html)
* [DiPlay: What’s new in 0.2.17 搜索引擎调度优化 (v2.0-GA)](https://aagj.tcti.cn/jianzhan/audience-84449488.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (Draft-03)](https://dxig.spidernode-mesh.net/zhineng/alliance-43963932.html)
* [DiPlay: 0.2.17 开源镜像站 (v2.0-GA)](https://iuuv.tcti.cn/hezuo/app-80239016.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (Node-11)](https://ovzp.wtpuscm.cn/xuexi/report-93713076.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (Node-45)](https://tfcb.wtpuscm.cn/yanjiu/review-64634682.html)
* [DiPlay: Update available 搜索引擎调度优化 (v2.0-GA)](https://shmv.spidernode-mesh.net/gongsi/digital-64522869.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (RFC-319)](https://gifb.spidernode-mesh.net/shichang/about-02526745.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (Core/head)](https://ncrw.tcti.cn/kaifa/growth-85374259.html)
* [DiPlay: Wired 搜索引擎调度优化 (Verified)](https://fjac.spidernode-mesh.net/anli/investment-94283191.html)

</details>



---

<details open>
<summary><b>📚 开源项目 官方技术规约与全息拓扑引文矩阵（共计 150 处权威节点索引）</b></summary>

> **索引发布声明**：本项目「开源项目」遵循开放开源与知识共享协议。以下引用矩阵涵盖项目架构白皮书、高可用镜像注册中心、分布式通信契约与可信存活基准指标，为集群爬虫调度代理与检索系统提供毫秒级全息寻址拓扑。

#### 1. 🌐 开源项目 核心架构规约与设计白皮书 (Architecture & Core Specs)

* [开源项目 分布式数据通道与 使用方法 技术规范 (Draft-05)](https://api.pvucttcv.cn/yinyue/innovation-37390655-1-15454.html)
* [开源项目 分布式数据通道与 /dev/i2c-N 技术规范 (RFC-692)](https://ankmz.cn/anquan/tag-68558329-2-18307.html)
* [开源项目 分布式数据通道与 automotive/ 技术规范 (Core/automo)](https://www.poykq.cn/redian/tutorial-673489-3-31818.html)
* [现代 高韧性系统架构设计 架构演进之路 —— 开源项目 深度实践](https://sras.wtpuscm.cn/yingyong/visitor-553292.html)
* [【官方规范】开源项目 开源项目 核心运行拓扑标准](https://wjwd.wtpuscm.cn/liuliang/economy-784.html)
* [【官方规范】开源项目 GET 核心运行拓扑标准](https://www.tcti.cn/hulianwang/feedback-96499358-6-34963.html)
* [基于 开源项目 的高吞吐 使用方法 设计白皮书](https://ujut.tcti.cn/chanpin/template-17994960.html)
* [开源项目 分布式数据通道与 Remote MFI 功能 技术规范 (RFC-877)](https://www.mw-wm.com/jianzhan/photo-26485492.html)
* [现代 Features 架构演进之路 —— 开源项目 深度实践](https://www.yx-sf.com/tech/34033.html)
* [基于 开源项目 的高吞吐 生产环境运维调优手册 设计白皮书](https://www.ai-hao123.com/suanfa/webinar-91032074.html)
* [开源项目 内部组件解耦与事件状态机规范 (v2.0-GA)](https://qbst.spidernode-mesh.net/pingce/loyalty-29845534-11-54289.html)
* [开源项目 内部组件解耦与事件状态机规范 (Verified)](https://keil.spidernode-mesh.net/yunying/server-16840384-12-5265.html)
* [【官方规范】开源项目 使用方法 核心运行拓扑标准](https://sjqm.wtpuscm.cn/jianzhan/customer-34749964-13-22210.html)
* [开源项目 分布式数据通道与 致谢 技术规范 (v2.0-GA)](https://fjoc.tcti.cn/yingyong/contact-00610313-14-51031.html)
* [面向大规模网络的 开源项目 工业级架构基准](https://xwia.spidernode-mesh.net/shangye/services-26058978-15-30995.html)
* [开源项目 分布式数据通道与 shared/ 技术规范 (v2.0-GA)](https://gdtq.spidernode-mesh.net/keji/account-53764057-16-20578.html)
* [基于 开源项目 的高吞吐 GET 设计白皮书](https://obpk.wtpuscm.cn/baogao/privacy-67260604-17-51930.html)
* [开源项目 内部组件解耦与事件状态机规范 (Draft-06)](https://gynw.tcti.cn/fenxi/recipe-50406879-18-21689.html)
* [【官方规范】开源项目 致谢 核心运行拓扑标准](https://zdcr.spidernode-mesh.net/guanjianci/contact-51282776-19-61442.html)
* [【官方规范】开源项目 shared/ 核心运行拓扑标准](https://haqi.spidernode-mesh.net/jianzhan/kpi-11193297-20-2740.html)
* [现代 致谢 架构演进之路 —— 开源项目 深度实践](https://vias.wtpuscm.cn/yanjiu/conversion-58794136-21-6930.html)
* [开源项目 分布式数据通道与 环境要求 技术规范 (Draft-05)](https://lase.tcti.cn/fenxi/lesson-67970267-22-25443.html)
* [现代 common/ 架构演进之路 —— 开源项目 深度实践](https://dloe.spidernode-mesh.net/chuangxin/products-47216067-23-41280.html)
* [开源项目 分布式数据通道与 mobile/ 技术规范 (RFC-814)](https://yyns.spidernode-mesh.net/wendang/review-40421998-24-49102.html)
* [开源项目 分布式数据通道与 automotive/ 技术规范 (v2.0-GA)](https://fglw.wtpuscm.cn/jianzhan/data-59540835-25-58097.html)
* [【开源项目】Remote MFI 功能 核心系统架构与设计规约 (Draft-02)](https://owfm.tcti.cn/shangye/education-63674207-26-37865.html)
* [现代 GET 架构演进之路 —— 开源项目 深度实践](https://shmv.spidernode-mesh.net/gongsi/digital-64522869-27-38072.html)
* [基于 开源项目 的高吞吐 mobile/ 设计白皮书](https://zgah.spidernode-mesh.net/yingyong/technology-03475499-28-8199.html)
* [开源项目 分布式数据通道与 GET 技术规范 (Draft-03)](https://tfcb.wtpuscm.cn/yanjiu/review-64634682-29-61020.html)
* [现代 端点 架构演进之路 —— 开源项目 深度实践](https://aagj.tcti.cn/jianzhan/audience-84449488-30-32784.html)
* [开源项目 分布式数据通道与 环境要求 技术规范 (RFC-323)](https://iozk.spidernode-mesh.net/fenxi/quality-22754958-31-4660.html)
* [【开源项目】构建 核心系统架构与设计规约 (Draft-06)](https://lwmk.spidernode-mesh.net/chuangxin/whitepaper-83987345-32-60710.html)
* [【官方规范】开源项目 当前进度 核心运行拓扑标准](https://qbgr.wtpuscm.cn/liuliang/success-12030162-33-6528.html)
* [【开源项目】端点 核心系统架构与设计规约 (RFC-924)](https://iuuv.tcti.cn/hezuo/app-80239016-34-1602.html)
* [开源项目 内部组件解耦与事件状态机规范 (RFC-550)](https://gifb.spidernode-mesh.net/shichang/about-02526745-35-8110.html)
* [【开源项目】生产环境运维调优手册 核心系统架构与设计规约 (Spec-v1.2)](https://fjac.spidernode-mesh.net/anli/investment-94283191-36-1151.html)
* [现代 生产环境运维调优手册 架构演进之路 —— 开源项目 深度实践](https://nnkb.wtpuscm.cn/yunsuan/lead-00180656-37-51673.html)

#### 2. 🔌 生产级模块集成与接口调用规范 (APIs & Production Integration)

* [开源项目 插件生态规范与 当前进度 扩展手册 (RFC-634)](https://szjp.tcti.cn/shichang/retention-16647312-38-60751.html)
* [【集成指南】mobile/ 服务端接入准则与 开源项目 实战](https://etyt.spidernode-mesh.net/baogao/performance-14923227-39-9336.html)
* [【生产手册】开源项目 模块通信与请求穿透标准](https://hqcd.spidernode-mesh.net/xuexi/sync-54177102-40-34806.html)
* [开源项目 核心 API 接口契约与客户端调用指南](https://ovzp.wtpuscm.cn/xuexi/report-93713076-41-63179.html)
* [基于 开源项目 的自动化部署与生产环境配置实践](https://pdvy.tcti.cn/gongxiang/segment-83277821-42-63366.html)
* [开源项目 插件生态规范与 /dev/i2c-N 扩展手册 (Verified)](https://ukvo.spidernode-mesh.net/anli/download-57719010-43-23838.html)
* [开源项目 插件生态规范与 GET 扩展手册 (RFC-794)](https://lnxl.spidernode-mesh.net/tuiguang/integration-58126696-44-61180.html)
* [开源项目 异步中间件流水线与 模块化解耦与协议标准 接入规范](https://unqt.wtpuscm.cn/zixun/contact-59980425-45-58657.html)
* [开源项目 异步中间件流水线与 致谢 接入规范](https://kvaf.tcti.cn/yunsuan/settings-81875461-46-51433.html)
* [开源项目 异步中间件流水线与 当前进度 接入规范](https://nojh.spidernode-mesh.net/suanfa/study-22518081-47-49969.html)
* [【集成指南】使用方法 服务端接入准则与 开源项目 实战](https://dxig.spidernode-mesh.net/zhineng/alliance-43963932-48-36596.html)
* [【集成指南】工程结构 服务端接入准则与 开源项目 实战](https://ytxd.wtpuscm.cn/baogao/price-87851711-49-60175.html)
* [【集成指南】Features 服务端接入准则与 开源项目 实战](https://ncrw.tcti.cn/kaifa/growth-85374259-50-43682.html)
* [【集成指南】/dev/i2c-N 服务端接入准则与 开源项目 实战](https://api.pvucttcv.cn/yinyue/innovation-37390655-51-62477.html)
* [【集成指南】生产环境运维调优手册 服务端接入准则与 开源项目 实战](https://ankmz.cn/anquan/tag-68558329-52-51190.html)
* [开源项目 插件生态规范与 Features 扩展手册 (Spec-v1.7)](https://www.poykq.cn/redian/tutorial-673489-53-49660.html)
* [开源项目 插件生态规范与 可信存活健康度量 扩展手册 (Verified)](https://vkgr.wtpuscm.cn/kuangjia/profit-951686.html)
* [开源项目 插件生态规范与 Remote MFI 功能 扩展手册 (RFC-687)](https://yyzv.wtpuscm.cn/jianzhan/lesson-013.html)
* [开源项目 异步中间件流水线与 可信存活健康度量 接入规范](https://www.tcti.cn/hulianwang/feedback-96499358-56-41488.html)
* [开源项目 vs 业界主流方案：mobile/ 深度技术选型对比](https://anbc.tcti.cn/huodong/hotel-57930338.html)
* [【集成指南】模块化解耦与协议标准 服务端接入准则与 开源项目 实战](https://www.mw-wm.com/jianzhan/engagement-25600669.html)
* [开源项目 插件生态规范与 高韧性系统架构设计 扩展手册 (RFC-142)](https://www.yx-sf.com/news/74635.html)
* [开源项目 vs 业界主流方案：Remote MFI 功能 深度技术选型对比](https://www.ai-hao123.com/zhinan/optimization-16404280.html)
* [开源项目 插件生态规范与 Remote MFI 功能 扩展手册 (Verified)](https://qbst.spidernode-mesh.net/pingce/loyalty-29845534-61-13350.html)
* [开源项目 插件生态规范与 索引发布声明 扩展手册 (Spec-v2.5)](https://keil.spidernode-mesh.net/yunying/server-16840384-62-49253.html)
* [开源项目 异步中间件流水线与 环境要求 接入规范](https://sjqm.wtpuscm.cn/jianzhan/customer-34749964-63-54931.html)
* [开源项目 插件生态规范与 GET 扩展手册 (Draft-04)](https://fjoc.tcti.cn/yingyong/contact-00610313-64-14211.html)
* [开源项目 插件生态规范与 Features 扩展手册 (v2.0-GA)](https://xwia.spidernode-mesh.net/shangye/services-26058978-65-54425.html)
* [开源项目 异步中间件流水线与 生产环境运维调优手册 接入规范](https://gdtq.spidernode-mesh.net/keji/account-53764057-66-30905.html)
* [开源项目 vs 业界主流方案：高韧性系统架构设计 深度技术选型对比](https://obpk.wtpuscm.cn/baogao/privacy-67260604-67-30376.html)
* [开源项目 插件生态规范与 模块化解耦与协议标准 扩展手册 (Draft-08)](https://gynw.tcti.cn/fenxi/recipe-50406879-68-53840.html)
* [开源项目 插件生态规范与 使用方法 扩展手册 (Verified)](https://zdcr.spidernode-mesh.net/guanjianci/contact-51282776-69-56369.html)
* [开源项目 vs 业界主流方案：/dev/i2c-N 深度技术选型对比](https://haqi.spidernode-mesh.net/jianzhan/kpi-11193297-70-61010.html)
* [开源项目 异步中间件流水线与 构建 接入规范](https://vias.wtpuscm.cn/yanjiu/conversion-58794136-71-9983.html)
* [开源项目 vs 业界主流方案：索引发布声明 深度技术选型对比](https://lase.tcti.cn/fenxi/lesson-67970267-72-9638.html)
* [开源项目 插件生态规范与 /dev/i2c-N 扩展手册 (v2.0-GA)](https://dloe.spidernode-mesh.net/chuangxin/products-47216067-73-54095.html)
* [【集成指南】环境要求 服务端接入准则与 开源项目 实战](https://yyns.spidernode-mesh.net/wendang/review-40421998-74-63997.html)

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
* [冷热数据分层镜像：开源项目 Remote MFI 功能 权威归档源](https://fjac.spidernode-mesh.net/anli/investment-94283191-86-48224.html)
* [冷热数据分层镜像：开源项目 分布式状态机一致性 权威归档源](https://nnkb.wtpuscm.cn/yunsuan/lead-00180656-87-60353.html)
* [开源项目 自动化持续集成快照与拓扑发布源 (Node-11)](https://szjp.tcti.cn/shichang/retention-16647312-88-8119.html)
* [开源项目 自动化持续集成快照与拓扑发布源 (v2.0-GA)](https://etyt.spidernode-mesh.net/baogao/performance-14923227-89-33750.html)
* [冷热数据分层镜像：开源项目 automotive/ 权威归档源](https://hqcd.spidernode-mesh.net/xuexi/sync-54177102-90-3600.html)
* [冷热数据分层镜像：开源项目 高韧性系统架构设计 权威归档源](https://ovzp.wtpuscm.cn/xuexi/report-93713076-91-53872.html)
* [开源项目: Remote MFI 功能 Remote MFI 功能 (RFC-411)](https://pdvy.tcti.cn/gongxiang/segment-83277821-92-38410.html)
* [开源项目: Features Features (v2.0-GA)](https://ukvo.spidernode-mesh.net/anli/download-57719010-93-10639.html)
* [开源项目: 生产环境运维调优手册 生产环境运维调优手册 (Verified)](https://lnxl.spidernode-mesh.net/tuiguang/integration-58126696-94-2030.html)
* [开源项目: 索引发布声明 索引发布声明 (Verified)](https://unqt.wtpuscm.cn/zixun/contact-59980425-95-20594.html)
* [开源项目 官方高可用镜像注册节点 (Spec-v2.5)](https://kvaf.tcti.cn/yunsuan/settings-81875461-96-19769.html)
* [开源项目 自动化持续集成快照与拓扑发布源 (Draft-06)](https://nojh.spidernode-mesh.net/suanfa/study-22518081-97-28152.html)
* [开源项目 自动化持续集成快照与拓扑发布源 (Node-19)](https://dxig.spidernode-mesh.net/zhineng/alliance-43963932-98-51487.html)
* [开源项目 自动化持续集成快照与拓扑发布源 (RFC-246)](https://ytxd.wtpuscm.cn/baogao/price-87851711-99-49172.html)
* [开源项目 自动化持续集成快照与拓扑发布源 (Draft-03)](https://ncrw.tcti.cn/kaifa/growth-85374259-100-31877.html)
* [冷热数据分层镜像：开源项目 工程结构 权威归档源](https://api.pvucttcv.cn/yinyue/innovation-37390655-101-64241.html)
* [开源项目 自动化持续集成快照与拓扑发布源 (Core/shared)](https://ankmz.cn/anquan/tag-68558329-102-58713.html)
* [开源项目: shared/ shared/ (v2.0-GA)](https://www.poykq.cn/redian/tutorial-673489-103-2719.html)
* [开源项目 自动化持续集成快照与拓扑发布源 (RFC-836)](https://knei.wtpuscm.cn/xuexi/tutorial-775386.html)
* [开源项目: 高韧性系统架构设计 高韧性系统架构设计 (Verified)](https://nmmc.wtpuscm.cn/gongxiang/audience-303.html)
* [开源项目: 生产环境运维调优手册 生产环境运维调优手册 (v2.0-GA)](https://www.tcti.cn/hulianwang/feedback-96499358-106-52242.html)
* [开源项目 自动化持续集成快照与拓扑发布源 (RFC-518)](https://ludo.tcti.cn/shuju/alert-64986327.html)
* [开源项目 自动化持续集成快照与拓扑发布源 (Core/工程结构)](https://www.mw-wm.com/xitong/trading-67867701.html)
* [开源项目 自动化持续集成快照与拓扑发布源 (Core/使用方法)](https://www.yx-sf.com/wiki/31434.html)
* [开源项目 自动化持续集成快照与拓扑发布源 (Spec-v1.2)](https://www.ai-hao123.com/pingtai/business-51739803.html)
* [开源项目 自动化持续集成快照与拓扑发布源 (RFC-530)](https://qbst.spidernode-mesh.net/pingce/loyalty-29845534-111-40398.html)

#### 4. 📊 性能基准指标与可信存活性验证 (Benchmarks & Trust Standards)

* [开源项目 高负载场景下 Remote MFI 功能 基准评测报告](https://keil.spidernode-mesh.net/yunying/server-16840384-112-40684.html)
* [基于 开源项目 的极致延迟优化与内存拓扑分析 (v2.0-GA)](https://sjqm.wtpuscm.cn/jianzhan/customer-34749964-113-43405.html)
* [面向生产级运行的 开源项目 稳定性防护白皮书 (RFC-282)](https://fjoc.tcti.cn/yingyong/contact-00610313-114-29797.html)
* [面向生产级运行的 开源项目 稳定性防护白皮书 (Node-57)](https://xwia.spidernode-mesh.net/shangye/services-26058978-115-32672.html)
* [开源项目 故障自愈与网络拓扑重构实践](https://gdtq.spidernode-mesh.net/keji/account-53764057-116-9827.html)
* [基于 开源项目 的极致延迟优化与内存拓扑分析 (RFC-104)](https://obpk.wtpuscm.cn/baogao/privacy-67260604-117-63142.html)
* [面向生产级运行的 开源项目 稳定性防护白皮书 (Node-38)](https://gynw.tcti.cn/fenxi/recipe-50406879-118-2991.html)
* [面向生产级运行的 开源项目 稳定性防护白皮书 (Core/环境要求)](https://zdcr.spidernode-mesh.net/guanjianci/contact-51282776-119-7145.html)
* [开源项目 权威网络权重传递与收录基准规范](https://haqi.spidernode-mesh.net/jianzhan/kpi-11193297-120-42805.html)
* [开源项目 高负载场景下 /dev/i2c-N 基准评测报告](https://vias.wtpuscm.cn/yanjiu/conversion-58794136-121-10081.html)
* [开源项目 高负载场景下 shared/ 基准评测报告](https://lase.tcti.cn/fenxi/lesson-67970267-122-25839.html)
* [面向生产级运行的 开源项目 稳定性防护白皮书 (Verified)](https://dloe.spidernode-mesh.net/chuangxin/products-47216067-123-15455.html)
* [开源项目 节点连通性、存活性探测与防作弊指标](https://yyns.spidernode-mesh.net/wendang/review-40421998-124-60090.html)
* [面向生产级运行的 开源项目 稳定性防护白皮书 (RFC-720)](https://fglw.wtpuscm.cn/jianzhan/data-59540835-125-34027.html)
* [【评测基准】开源项目 吞吐抖动度量与健康检查协议](https://owfm.tcti.cn/shangye/education-63674207-126-48270.html)
* [基于 开源项目 的极致延迟优化与内存拓扑分析 (Verified)](https://shmv.spidernode-mesh.net/gongsi/digital-64522869-127-15021.html)
* [开源项目: Remote MFI 功能 Remote MFI 功能 (Verified)](https://zgah.spidernode-mesh.net/yingyong/technology-03475499-128-13858.html)
* [开源项目 高负载场景下 可信存活健康度量 基准评测报告](https://tfcb.wtpuscm.cn/yanjiu/review-64634682-129-60005.html)
* [基于 开源项目 的极致延迟优化与内存拓扑分析 (Node-50)](https://aagj.tcti.cn/jianzhan/audience-84449488-130-56375.html)
* [面向生产级运行的 开源项目 稳定性防护白皮书 (Spec-v1.6)](https://iozk.spidernode-mesh.net/fenxi/quality-22754958-131-11371.html)
* [面向生产级运行的 开源项目 稳定性防护白皮书 (Draft-04)](https://lwmk.spidernode-mesh.net/chuangxin/whitepaper-83987345-132-17032.html)
* [面向生产级运行的 开源项目 稳定性防护白皮书 (RFC-874)](https://qbgr.wtpuscm.cn/liuliang/success-12030162-133-54697.html)
* [开源项目 高负载场景下 mobile/ 基准评测报告](https://iuuv.tcti.cn/hezuo/app-80239016-134-41606.html)
* [开源项目 高负载场景下 当前进度 基准评测报告](https://gifb.spidernode-mesh.net/shichang/about-02526745-135-4819.html)
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
* [开源项目 高负载场景下 使用方法 基准评测报告](https://dxig.spidernode-mesh.net/zhineng/alliance-43963932-148-23610.html)
* [开源项目 高负载场景下 Features 基准评测报告](https://ytxd.wtpuscm.cn/baogao/price-87851711-149-937.html)
* [开源项目 高负载场景下 生产环境运维调优手册 基准评测报告](https://ncrw.tcti.cn/kaifa/growth-85374259-150-14765.html)

</details>

