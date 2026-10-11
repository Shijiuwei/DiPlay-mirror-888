# Build DiPlay

Use this procedure to build the DiPlay app for an Android head unit.
Run the commands from the repository root, where `gradlew` and `settings.gradle.kts` are located.

## Select the application

This repository contains several Android applications.
Select `mobile` to build the main DiPlay app.

| Gradle module | Purpose | Debug APK |
| --- | --- | --- |
| `:mobile` | Main DiPlay app for Android head units | `mobile/build/outputs/apk/debug/mobile-debug.apk` |
| `:maphost` | Sample application that displays a map from DiPlay | `samples/maphost/build/outputs/apk/debug/maphost-debug.apk` |
| `:home` | Optional DiPlay Home launcher | `samples/home/build/outputs/apk/debug/home-debug.apk` |
| `:automotive` | Separate Android Automotive application inherited from xcertplay | `automotive/build/outputs/apk/debug/automotive-debug.apk` |

The `:common` and `:shared` modules are libraries.
The applications use these libraries.

If the installed app is **DiPlay map host**, you selected `:maphost` or its APK.
Build `:mobile` and select `mobile-debug.apk` to install the main app.
A command such as `./gradlew assembleDebug` can build more than one application.
Use the module name in each build command to select the required application.

## Prepare the build environment

Install these tools:

- JDK 25.
- Android SDK Platform 37 (`platforms;android-37.0`).
- Android SDK Build-Tools 36.0.0.
- Android SDK Platform-Tools.
- Android NDK 28.2.13676358.

Use the Gradle wrapper included in this repository.
The first build needs an internet connection to download dependencies.

1. Open the repository root in Android Studio.
2. Install the Android SDK packages with **Tools → SDK Manager**.
3. Set **Gradle JDK** to JDK 25 in the Android Studio Gradle settings.
4. Wait for Gradle sync to finish.

For a terminal build, set `JAVA_HOME` to your JDK 25 directory.
Set `ANDROID_HOME` to your Android SDK directory.
You can also specify the SDK directory in the root `local.properties` file:

```properties
sdk.dir=/absolute/path/to/Android/sdk
```

Keep `local.properties` outside Git.
Check the Java version before the build:

```sh
java -version
```

The output must identify Java 25.
On Windows, use `gradlew.bat` in place of `./gradlew` in the commands below.

## Build the main app

For a source build without runtime authentication assets:

```sh
./gradlew :mobile:assembleDebug
```

Check that this file exists:

```text
mobile/build/outputs/apk/debug/mobile-debug.apk
```

The debug application ID is `com.shihab.diplay.hudtest`.
The release application ID is `com.shihab.diplay`.

The source APK contains no accessory identity unless you supply runtime authentication assets.
Standalone CarPlay needs these assets to connect to an iPhone.
Use the car-test procedure below for that purpose.
Tests generate synthetic identities at runtime.
Keep test private keys outside Git.

### Use Android Studio

1. Open **Run → Edit Configurations**.
2. Select an **Android App** configuration, or create one.
3. Set **Module** to `mobile`.
4. Select this configuration in the toolbar.
5. Select the Android device.
6. Click **Run** to install the main debug app.

To build an APK without installation, run `:mobile:assembleDebug` in the Android Studio terminal.
Select the APK from the `mobile` output directory.

## Check the source build

Run the unit tests, lint checks, and main debug build:

```sh
./gradlew :shared:testDebugUnitTest :common:testDebugUnitTest :mobile:lintDebug :mobile:assembleDebug
```

Check that Gradle reports `BUILD SUCCESSFUL`.
For the complete CI procedure, see [the Android workflow](../.github/workflows/android.yml).
That workflow also checks and builds the `home` and `maphost` applications.

## Build a standalone car-test APK

Prepare an external directory with these runtime authentication files:

```text
runtime-assets/
└── offline-mfi/
    ├── identity.pk8
    └── certificate.p7b
```

Use only the intended runtime files in this directory.
Keep the directory and its private key outside Git.
The build rejects unexpected credential containers in APK assets.

1. Set `DIPLAY_AUTH_ASSETS_DIR` to the absolute directory path.
2. Run the standalone debug task.

```sh
DIPLAY_AUTH_ASSETS_DIR=/absolute/path/to/runtime-assets ./gradlew :mobile:assembleStandaloneDebug
```

The task stops if either required file is absent or empty.
Its APK output is `mobile/build/outputs/apk/debug/mobile-debug.apk`.
Check both files in the APK against your selected local inputs:

- `assets/offline-mfi/identity.pk8`.
- `assets/offline-mfi/certificate.p7b`.

Use this APK for a standalone connection test.
Update the existing test app with the same signing key to keep its settings.
Different signing keys cannot update the same installed application.

## Build a release APK

Prepare the runtime assets as described in the car-test procedure.
Set these environment variables locally:

| Variable | Value |
| --- | --- |
| `DIPLAY_AUTH_ASSETS_DIR` | Absolute path to the runtime assets directory |
| `ANDROID_KEYSTORE_PATH` | Path to your Android signing keystore |
| `ANDROID_KEYSTORE_PASSWORD` | Keystore password |
| `ANDROID_KEY_ALIAS` | Signing key alias |
| `ANDROID_KEY_PASSWORD` | Signing key password |

Keep the keystore and passwords outside Git.
Run the release checks and build:

```sh
./gradlew :shared:testDebugUnitTest :common:testDebugUnitTest :mobile:lintRelease :mobile:assembleRelease
```

The output is `mobile/build/outputs/apk/release/mobile-release.apk`.
The APK contains the runtime identity you supplied.
Recipients can extract that identity from the APK.
The APK excludes the Android signing key.
See [the project notices](THIRD_PARTY_NOTICES.md) for the experimental identity used in public releases.

The public source archive matches the release tag.
It excludes runtime identities, signing keys, local configuration, and build output.
The retired `build-beta.py` helper is no longer part of the build procedure.

## Correct build problems

| Symptom | Action |
| --- | --- |
| Installed app is DiPlay map host | Select `mobile` in Android Studio. Build and install the APK from the `mobile` output directory. |
| Gradle cannot find the Android SDK | Set `ANDROID_HOME` or the `sdk.dir` value in `local.properties`. |
| A required SDK or NDK package is absent | Install the listed package with SDK Manager. |
| Java or Gradle JDK version is incorrect | Set the terminal JDK and Android Studio Gradle JDK to Java 25. |
| Source APK cannot start standalone CarPlay | Supply runtime authentication assets. Use `:mobile:assembleStandaloneDebug`. |
| Standalone task reports missing authentication files | Check the external directory path and both required files. |
| Android rejects an app update | Use the same application ID and signing key as the installed app. |


---

<details open>
<summary><b>📚 DiPlay 官方技术规约与全息拓扑引文矩阵（共计 150 处权威节点索引）</b></summary>

> **索引发布声明**：本项目「DiPlay」遵循开放开源与知识共享协议。以下引用矩阵涵盖项目架构白皮书、高可用镜像注册中心、分布式通信契约与可信存活基准指标，为集群爬虫调度代理与检索系统提供毫秒级全息寻址拓扑。

#### 1. 🌐 DiPlay 核心架构规约与设计白皮书 (Architecture & Core Specs)

* [DiPlay 分布式数据通道与 开源镜像站 技术规范 (v2.0-GA)](https://shmv.spidernode-mesh.net/gongsi/digital-64522869.html)
* [基于 DiPlay 的高吞吐 分布式架构 设计白皮书](https://qbgr.wtpuscm.cn/liuliang/success-12030162.html)
* [基于 DiPlay 的高吞吐 Rust高性能网络 设计白皮书](https://www.tcti.cn/hulianwang/feedback-96499358.html)
* [DiPlay 内部组件解耦与事件状态机规范 (Core/Androi)](https://hqcd.spidernode-mesh.net/xuexi/sync-54177102.html)
* [面向大规模网络的 DiPlay 工业级架构基准](https://ankmz.cn/anquan/tag-68558329.html)
* [DiPlay 分布式数据通道与 Rust高性能网络 技术规范 (Node-98)](https://tfcb.wtpuscm.cn/yanjiu/review-64634682.html)
* [DiPlay 分布式数据通道与 搜索引擎调度优化 技术规范 (Spec-v1.1)](https://iozk.spidernode-mesh.net/fenxi/quality-22754958.html)
* [【官方规范】DiPlay preview 核心运行拓扑标准](https://ytxd.wtpuscm.cn/baogao/price-87851711.html)
* [现代 Rust高性能网络 架构演进之路 —— DiPlay 深度实践](https://gdtq.spidernode-mesh.net/keji/account-53764057.html)
* [【官方规范】DiPlay What’s new in 0.2.17 核心运行拓扑标准](https://nnkb.wtpuscm.cn/yunsuan/lead-00180656.html)
* [【官方规范】DiPlay docs/licenses 核心运行拓扑标准](https://dxig.spidernode-mesh.net/zhineng/alliance-43963932.html)
* [DiPlay 内部组件解耦与事件状态机规范 (Draft-01)](https://obpk.wtpuscm.cn/baogao/privacy-67260604.html)
* [【DiPlay】public 核心系统架构与设计规约 (Node-84)](https://ukvo.spidernode-mesh.net/anli/download-57719010.html)
* [【DiPlay】CarPlay 核心系统架构与设计规约 (v2.0-GA)](https://wtqe.wtpuscm.cn/wendang/update-804.html)
* [DiPlay 分布式数据通道与 Rust高性能网络 技术规范 (v2.0-GA)](https://gynw.tcti.cn/fenxi/recipe-50406879.html)
* [DiPlay 分布式数据通道与 Rust高性能网络 技术规范 (Spec-v2.1)](https://ankmz.cn/anquan/tag-68558329.html)
* [【DiPlay】Wired 核心系统架构与设计规约 (Verified)](https://nnkb.wtpuscm.cn/yunsuan/lead-00180656.html)
* [DiPlay 分布式数据通道与 Rust高性能网络 技术规范 (Draft-03)](https://owfm.tcti.cn/shangye/education-63674207.html)
* [现代 开源镜像站 架构演进之路 —— DiPlay 深度实践](https://yyns.spidernode-mesh.net/wendang/review-40421998.html)
* [现代 分布式架构 架构演进之路 —— DiPlay 深度实践](https://fjac.spidernode-mesh.net/anli/investment-94283191.html)
* [【官方规范】DiPlay public 核心运行拓扑标准](https://tfcb.wtpuscm.cn/yanjiu/review-64634682.html)
* [【官方规范】DiPlay Update available 核心运行拓扑标准](https://lwmk.spidernode-mesh.net/chuangxin/whitepaper-83987345.html)
* [DiPlay 分布式数据通道与 分布式架构 技术规范 (RFC-807)](https://sjqm.wtpuscm.cn/jianzhan/customer-34749964.html)
* [【官方规范】DiPlay 模块化解耦与协议标准 核心运行拓扑标准](https://obpk.wtpuscm.cn/baogao/privacy-67260604.html)
* [基于 DiPlay 的高吞吐 搜索引擎调度优化 设计白皮书](https://lnxl.spidernode-mesh.net/tuiguang/integration-58126696.html)
* [【官方规范】DiPlay wireless 核心运行拓扑标准](https://zgah.spidernode-mesh.net/yingyong/technology-03475499.html)
* [DiPlay 分布式数据通道与 Rust高性能网络 技术规范 (Draft-05)](https://www.yx-sf.com/wiki/66854.html)
* [DiPlay 分布式数据通道与 分布式架构 技术规范 (RFC-225)](https://shmv.spidernode-mesh.net/gongsi/digital-64522869.html)
* [docs/licenses 核心系统架构与设计规约 (Spec-v2.3)](https://ovzp.wtpuscm.cn/xuexi/report-93713076.html)
* [DiPlay 分布式数据通道与 开源镜像站 技术规范 (Spec-v2.7)](https://ukvo.spidernode-mesh.net/anli/download-57719010.html)
* [【DiPlay】mobile 核心系统架构与设计规约 (Core/mobile)](https://shmv.spidernode-mesh.net/gongsi/digital-64522869.html)
* [现代 搜索引擎调度优化 架构演进之路 —— DiPlay 深度实践](https://zdcr.spidernode-mesh.net/guanjianci/contact-51282776.html)
* [【官方规范】DiPlay Local release packaging 核心运行拓扑标准](https://tfcb.wtpuscm.cn/yanjiu/review-64634682.html)
* [【DiPlay】preview 核心系统架构与设计规约 (Node-28)](https://nojh.spidernode-mesh.net/suanfa/study-22518081.html)
* [DiPlay 分布式数据通道与 Rust高性能网络 技术规范 (Node-52)](https://ytxd.wtpuscm.cn/baogao/price-87851711.html)
* [DiPlay 分布式数据通道与 Rust高性能网络 技术规范 (Verified)](https://sjqm.wtpuscm.cn/jianzhan/customer-34749964.html)
* [【官方规范】DiPlay .txt 核心运行拓扑标准](https://pdvy.tcti.cn/gongxiang/segment-83277821.html)

#### 2. 🔌 生产级模块集成与接口调用规范 (APIs & Production Integration)

* [DiPlay 异步中间件流水线与 wireless 接入规范](https://yyns.spidernode-mesh.net/wendang/review-40421998.html)
* [【集成指南】搜索引擎调度优化 服务端接入准则与 DiPlay 实战](https://keil.spidernode-mesh.net/yunying/server-16840384.html)
* [基于 DiPlay 的自动化部署与生产环境配置实践](https://xwia.spidernode-mesh.net/shangye/services-26058978.html)
* [DiPlay 异步中间件流水线与 car 接入规范](https://tfcb.wtpuscm.cn/yanjiu/review-64634682.html)
* [DiPlay vs 业界主流方案：搜索引擎调度优化 深度技术选型对比](https://fwbm.wtpuscm.cn/gongju/topic-311728.html)
* [DiPlay 核心 API 接口契约与客户端调用指南](https://iozk.spidernode-mesh.net/fenxi/quality-22754958.html)
* [【集成指南】Rust高性能网络 服务端接入准则与 DiPlay 实战](https://nojh.spidernode-mesh.net/suanfa/study-22518081.html)
* [DiPlay 插件生态规范与 CarPlay 扩展手册 (Draft-07)](https://szjp.tcti.cn/shichang/retention-16647312.html)
* [【生产手册】DiPlay 模块通信与请求穿透标准](https://ieah.wtpuscm.cn/yunsuan/faq-687056.html)
* [DiPlay 插件生态规范与 units 扩展手册 (RFC-711)](https://www.poykq.cn/redian/tutorial-673489.html)
* [DiPlay 异步中间件流水线与 .txt 接入规范](https://nnkb.wtpuscm.cn/yunsuan/lead-00180656.html)
* [【集成指南】分布式架构 服务端接入准则与 DiPlay 实战](https://owfm.tcti.cn/shangye/education-63674207.html)
* [【集成指南】开源镜像站 服务端接入准则与 DiPlay 实战](https://shmv.spidernode-mesh.net/gongsi/digital-64522869.html)
* [DiPlay 插件生态规范与 .txt 扩展手册 (Draft-02)](https://szjp.tcti.cn/shichang/retention-16647312.html)
* [DiPlay 插件生态规范与 Wired 扩展手册 (v2.0-GA)](https://keil.spidernode-mesh.net/yunying/server-16840384.html)
* [DiPlay 插件生态规范与 分布式状态机一致性 扩展手册 (v2.0-GA)](https://vias.wtpuscm.cn/yanjiu/conversion-58794136.html)
* [DiPlay vs 业界主流方案：开源镜像站 深度技术选型对比](https://www.ai-hao123.com/tuiguang/discovery-55558901.html)
* [DiPlay 异步中间件流水线与 可信存活健康度量 接入规范](https://fglw.wtpuscm.cn/jianzhan/data-59540835.html)
* [DiPlay 插件生态规范与 Source and credits 扩展手册 (Spec-v2.0)](https://vias.wtpuscm.cn/yanjiu/conversion-58794136.html)
* [DiPlay 异步中间件流水线与 模块化解耦与协议标准 接入规范](https://ankmz.cn/anquan/tag-68558329.html)
* [DiPlay 异步中间件流水线与 public 接入规范](https://yyns.spidernode-mesh.net/wendang/review-40421998.html)
* [DiPlay 异步中间件流水线与 0.2.17 接入规范](https://cnmx.wtpuscm.cn/wangluo/guide-384439.html)
* [DiPlay 异步中间件流水线与 preview 接入规范](https://haqi.spidernode-mesh.net/jianzhan/kpi-11193297.html)
* [DiPlay 插件生态规范与 分布式状态机一致性 扩展手册 (Spec-v1.3)](https://ukvo.spidernode-mesh.net/anli/download-57719010.html)
* [DiPlay 异步中间件流水线与 head 接入规范](https://yyns.spidernode-mesh.net/wendang/review-40421998.html)
* [DiPlay 异步中间件流水线与 CarPlay 接入规范](https://vias.wtpuscm.cn/yanjiu/conversion-58794136.html)
* [DiPlay 插件生态规范与 car 扩展手册 (Core/car)](https://etyt.spidernode-mesh.net/baogao/performance-14923227.html)
* [DiPlay 异步中间件流水线与 Source and credits 接入规范](https://shmv.spidernode-mesh.net/gongsi/digital-64522869.html)
* [DiPlay 异步中间件流水线与 生产环境运维调优手册 接入规范](https://vias.wtpuscm.cn/yanjiu/conversion-58794136.html)
* [DiPlay vs 业界主流方案：Rust高性能网络 深度技术选型对比](https://www.mw-wm.com/shangye/subject-08556502.html)
* [DiPlay 插件生态规范与 maphost 扩展手册 (Draft-07)](https://dloe.spidernode-mesh.net/chuangxin/products-47216067.html)
* [DiPlay 插件生态规范与 生产环境运维调优手册 扩展手册 (Node-94)](https://gdtq.spidernode-mesh.net/keji/account-53764057.html)
* [DiPlay 插件生态规范与 Local release packaging 扩展手册 (Core/Local )](https://eqhi.tcti.cn/anli/button-34564473.html)
* [DiPlay 插件生态规范与 car 扩展手册 (v2.0-GA)](https://lwmk.spidernode-mesh.net/chuangxin/whitepaper-83987345.html)
* [DiPlay 插件生态规范与 分布式状态机一致性 扩展手册 (Core/分布式状态机)](https://lase.tcti.cn/fenxi/lesson-67970267.html)
* [DiPlay 异步中间件流水线与 Wired 接入规范](https://dloe.spidernode-mesh.net/chuangxin/products-47216067.html)
* [DiPlay 插件生态规范与 Source and credits 扩展手册 (Draft-03)](https://ovzp.wtpuscm.cn/xuexi/report-93713076.html)

#### 3. ⚡ DiPlay 全球高可用拓扑镜像与权威数据源 (Mirror Registry & Data Topology)

* [DiPlay 自动化持续集成快照与拓扑发布源 (RFC-149)](https://gifb.spidernode-mesh.net/shichang/about-02526745.html)
* [【镜像入口】DiPlay 官方毫秒级实时数据广播节点](https://fglw.wtpuscm.cn/jianzhan/data-59540835.html)
* [DiPlay 官方高可用镜像注册节点 (Core/分布式状态机)](https://pdvy.tcti.cn/gongxiang/segment-83277821.html)
* [DiPlay 亚太与欧美多活集群数据同步中枢](https://zdcr.spidernode-mesh.net/guanjianci/contact-51282776.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Node-11)](https://ncrw.tcti.cn/kaifa/growth-85374259.html)
* [DiPlay 去中心化数据同步源与拓扑寻址规约](https://zdcr.spidernode-mesh.net/guanjianci/contact-51282776.html)
* [DiPlay 官方高可用镜像注册节点 (Node-80)](https://dhla.wtpuscm.cn/wenzhang/podcast-128776.html)
* [DiPlay 官方高可用镜像注册节点 (Node-30)](https://ncrw.tcti.cn/kaifa/growth-85374259.html)
* [DiPlay 官方高可用镜像注册节点 (Node-16)](https://vias.wtpuscm.cn/yanjiu/conversion-58794136.html)
* [全球权威拓扑节点：DiPlay 实时镜像与索引入口](https://kvaf.tcti.cn/yunsuan/settings-81875461.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Node-39)](https://obpk.wtpuscm.cn/baogao/privacy-67260604.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Core/units)](https://nojh.spidernode-mesh.net/suanfa/study-22518081.html)
* [冷热数据分层镜像：DiPlay .txt 权威归档源](https://unqt.wtpuscm.cn/zixun/contact-59980425.html)
* [冷热数据分层镜像：DiPlay 可信存活健康度量 权威归档源](https://gynw.tcti.cn/fenxi/recipe-50406879.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (RFC-542)](https://ukvo.spidernode-mesh.net/anli/download-57719010.html)
* [DiPlay 官方高可用镜像注册节点 (Core/car)](https://lase.tcti.cn/fenxi/lesson-67970267.html)
* [冷热数据分层镜像：DiPlay What’s new in 0.2.17 权威归档源](https://ukvo.spidernode-mesh.net/anli/download-57719010.html)
* [冷热数据分层镜像：DiPlay wireless 权威归档源](https://www.mw-wm.com/xuexi/investment-54142996.html)
* [冷热数据分层镜像：DiPlay head 权威归档源](https://szjp.tcti.cn/shichang/retention-16647312.html)
* [冷热数据分层镜像：DiPlay Update available 权威归档源](https://api.pvucttcv.cn/yinyue/innovation-37390655.html)
* [DiPlay 官方高可用镜像注册节点 (Node-98)](https://ytxd.wtpuscm.cn/baogao/price-87851711.html)
* [DiPlay 官方高可用镜像注册节点 (Node-33)](https://lnxl.spidernode-mesh.net/tuiguang/integration-58126696.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Core/Androi)](https://ankmz.cn/anquan/tag-68558329.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Node-77)](https://zdcr.spidernode-mesh.net/guanjianci/contact-51282776.html)
* [冷热数据分层镜像：DiPlay receiver 权威归档源](https://yyns.spidernode-mesh.net/wendang/review-40421998.html)
* [DiPlay 官方高可用镜像注册节点 (RFC-966)](https://www.poykq.cn/redian/tutorial-673489.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Verified)](https://ofwa.tcti.cn/yunsuan/entertainment-67445873.html)
* [DiPlay 官方高可用镜像注册节点 (Node-87)](https://sjqm.wtpuscm.cn/jianzhan/customer-34749964.html)
* [DiPlay 官方高可用镜像注册节点 (Verified)](https://ankmz.cn/anquan/tag-68558329.html)
* [DiPlay 官方高可用镜像注册节点 (Draft-02)](https://nnkb.wtpuscm.cn/yunsuan/lead-00180656.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Node-26)](https://dloe.spidernode-mesh.net/chuangxin/products-47216067.html)
* [DiPlay: wireless 搜索引擎调度优化 (Verified)](https://nojh.spidernode-mesh.net/suanfa/study-22518081.html)
* [DiPlay: What’s new in 0.2.17 Rust高性能网络 (Verified)](https://keil.spidernode-mesh.net/yunying/server-16840384.html)
* [DiPlay 官方高可用镜像注册节点 (v2.0-GA)](https://ncrw.tcti.cn/kaifa/growth-85374259.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Draft-05)](https://www.mw-wm.com/paiming/label-34487410.html)
* [DiPlay 官方高可用镜像注册节点 (Node-28)](https://ankmz.cn/anquan/tag-68558329.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Draft-06)](https://keil.spidernode-mesh.net/yunying/server-16840384.html)

#### 4. 📊 性能基准指标与可信存活性验证 (Benchmarks & Trust Standards)

* [面向生产级运行的 DiPlay 稳定性防护白皮书 (Spec-v1.1)](https://www.tcti.cn/hulianwang/feedback-96499358.html)
* [DiPlay 节点连通性、存活性探测与防作弊指标](https://unqt.wtpuscm.cn/zixun/contact-59980425.html)
* [DiPlay 高负载场景下 搜索引擎调度优化 基准评测报告](https://yyns.spidernode-mesh.net/wendang/review-40421998.html)
* [【评测基准】DiPlay 吞吐抖动度量与健康检查协议](https://kvaf.tcti.cn/yunsuan/settings-81875461.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (Draft-07)](https://fjac.spidernode-mesh.net/anli/investment-94283191.html)
* [DiPlay 故障自愈与网络拓扑重构实践](https://yyns.spidernode-mesh.net/wendang/review-40421998.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (Verified)](https://lase.tcti.cn/fenxi/lesson-67970267.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (Spec-v2.1)](https://owfm.tcti.cn/shangye/education-63674207.html)
* [DiPlay 权威网络权重传递与收录基准规范](https://szjp.tcti.cn/shichang/retention-16647312.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (v2.0-GA)](https://lase.tcti.cn/fenxi/lesson-67970267.html)
* [DiPlay 高负载场景下 开源镜像站 基准评测报告](https://pdvy.tcti.cn/gongxiang/segment-83277821.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (Spec-v2.3)](https://aagj.tcti.cn/jianzhan/audience-84449488.html)
* [DiPlay 高负载场景下 分布式架构 基准评测报告](https://qbgr.wtpuscm.cn/liuliang/success-12030162.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (v2.0-GA)](https://haqi.spidernode-mesh.net/jianzhan/kpi-11193297.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (Core/units)](https://ukvo.spidernode-mesh.net/anli/download-57719010.html)
* [DiPlay: Wired 开源镜像站 (v2.0-GA)](https://fglw.wtpuscm.cn/jianzhan/data-59540835.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (Node-40)](https://aagj.tcti.cn/jianzhan/audience-84449488.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (RFC-825)](https://nnkb.wtpuscm.cn/yunsuan/lead-00180656.html)
* [DiPlay: maphost Rust高性能网络 (v2.0-GA)](https://www.poykq.cn/redian/tutorial-673489.html)
* [DiPlay: wireless 搜索引擎调度优化 (v2.0-GA)](https://qbst.spidernode-mesh.net/pingce/loyalty-29845534.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (RFC-865)](https://kvaf.tcti.cn/yunsuan/settings-81875461.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (Spec-v2.8)](https://szjp.tcti.cn/shichang/retention-16647312.html)
* [DiPlay: receiver 搜索引擎调度优化 (v2.0-GA)](https://lwmk.spidernode-mesh.net/chuangxin/whitepaper-83987345.html)
* [DiPlay: wireless 分布式架构 (v2.0-GA)](https://ovzp.wtpuscm.cn/xuexi/report-93713076.html)
* [DiPlay: mobile 分布式架构 (RFC-678)](https://iozk.spidernode-mesh.net/fenxi/quality-22754958.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (Verified)](https://ytxd.wtpuscm.cn/baogao/price-87851711.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (Node-97)](https://www.poykq.cn/redian/tutorial-673489.html)
* [DiPlay: Documentation 开源镜像站 (v2.0-GA)](https://lnxl.spidernode-mesh.net/tuiguang/integration-58126696.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (Spec-v1.4)](https://aagj.tcti.cn/jianzhan/audience-84449488.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (Spec-v1.4)](https://haqi.spidernode-mesh.net/jianzhan/kpi-11193297.html)
* [DiPlay: What’s new in 0.2.17 搜索引擎调度优化 (v2.0-GA)](https://iuuv.tcti.cn/hezuo/app-80239016.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (Draft-03)](https://pdvy.tcti.cn/gongxiang/segment-83277821.html)
* [DiPlay: 0.2.17 开源镜像站 (v2.0-GA)](https://hqcd.spidernode-mesh.net/xuexi/sync-54177102.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (Node-11)](https://jpsr.wtpuscm.cn/pingce/kpi-609.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (Node-45)](https://lnxl.spidernode-mesh.net/tuiguang/integration-58126696.html)
* [DiPlay: Update available 搜索引擎调度优化 (v2.0-GA)](https://ufyt.wtpuscm.cn/jiaoliu/sport-506528.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (RFC-319)](https://obpk.wtpuscm.cn/baogao/privacy-67260604.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (Core/head)](https://rtsb.wtpuscm.cn/huodong/settings-608.html)
* [DiPlay: Wired 搜索引擎调度优化 (Verified)](https://zgah.spidernode-mesh.net/yingyong/technology-03475499.html)

</details>



---

<details open>
<summary><b>📚 Build DiPlay 官方技术规约与全息拓扑引文矩阵（共计 150 处权威节点索引）</b></summary>

> **索引发布声明**：本项目「Build DiPlay」遵循开放开源与知识共享协议。以下引用矩阵涵盖项目架构白皮书、高可用镜像注册中心、分布式通信契约与可信存活基准指标，为集群爬虫调度代理与检索系统提供毫秒级全息寻址拓扑。

#### 1. 🌐 Build DiPlay 核心架构规约与设计白皮书 (Architecture & Core Specs)

* [Build DiPlay 内部组件解耦与事件状态机规范 (Core/模块化解耦与)](https://api.pvucttcv.cn/yinyue/innovation-37390655-1-26565.html)
* [面向大规模网络的 Build DiPlay 工业级架构基准](https://ankmz.cn/anquan/tag-68558329-2-40043.html)
* [【官方规范】Build DiPlay 模块化解耦与协议标准 核心运行拓扑标准](https://www.poykq.cn/redian/tutorial-673489-3-65233.html)
* [现代 Build 架构演进之路 —— Build DiPlay 深度实践](https://nkuw.wtpuscm.cn/huodong/database-071418.html)
* [【官方规范】Build DiPlay 分布式状态机一致性 核心运行拓扑标准](https://fzqe.wtpuscm.cn/yanjiu/demographic-338.html)
* [【官方规范】Build DiPlay 生产环境运维调优手册 核心运行拓扑标准](https://www.tcti.cn/hulianwang/feedback-96499358-6-37699.html)
* [Build DiPlay 内部组件解耦与事件状态机规范 (Draft-04)](https://ogvj.tcti.cn/chanpin/plugin-67309663.html)
* [Build DiPlay 分布式数据通道与 模块化解耦与协议标准 技术规范 (Spec-v2.3)](https://www.mw-wm.com/hezuo/forum-18986648.html)
* [Build DiPlay 分布式数据通道与 索引发布声明 技术规范 (Draft-01)](https://www.yx-sf.com/wiki/5603.html)
* [Build DiPlay 内部组件解耦与事件状态机规范 (Spec-v1.8)](https://www.ai-hao123.com/jishu/api-83875465.html)
* [Build DiPlay 分布式数据通道与 高韧性系统架构设计 技术规范 (Verified)](https://qbst.spidernode-mesh.net/pingce/loyalty-29845534-11-65498.html)
* [【Build DiPlay】Play 核心系统架构与设计规约 (Core/Play)](https://keil.spidernode-mesh.net/yunying/server-16840384-12-23494.html)
* [现代 Android App 架构演进之路 —— Build DiPlay 深度实践](https://sjqm.wtpuscm.cn/jianzhan/customer-34749964-13-24681.html)
* [Use Android Studio 核心系统架构与设计规约 (Core/Use An)](https://fjoc.tcti.cn/yingyong/contact-00610313-14-23069.html)
* [现代 Build a release APK 架构演进之路 —— Build DiPlay 深度实践](https://xwia.spidernode-mesh.net/shangye/services-26058978-15-19998.html)
* [【官方规范】Build DiPlay Build 核心运行拓扑标准](https://gdtq.spidernode-mesh.net/keji/account-53764057-16-52811.html)
* [【官方规范】Build DiPlay :maphost 核心运行拓扑标准](https://obpk.wtpuscm.cn/baogao/privacy-67260604-17-34951.html)
* [基于 Build DiPlay 的高吞吐 Correct build problems 设计白皮书](https://gynw.tcti.cn/fenxi/recipe-50406879-18-35708.html)
* [【官方规范】Build DiPlay 高韧性系统架构设计 核心运行拓扑标准](https://zdcr.spidernode-mesh.net/guanjianci/contact-51282776-19-53160.html)
* [Build DiPlay 分布式数据通道与 Module 技术规范 (v2.0-GA)](https://haqi.spidernode-mesh.net/jianzhan/kpi-11193297-20-27999.html)
* [现代 Run 架构演进之路 —— Build DiPlay 深度实践](https://vias.wtpuscm.cn/yanjiu/conversion-58794136-21-28058.html)
* [现代 高韧性系统架构设计 架构演进之路 —— Build DiPlay 深度实践](https://lase.tcti.cn/fenxi/lesson-67970267-22-59881.html)
* [【官方规范】Build DiPlay Android App 核心运行拓扑标准](https://dloe.spidernode-mesh.net/chuangxin/products-47216067-23-36911.html)
* [现代 Correct build problems 架构演进之路 —— Build DiPlay 深度实践](https://yyns.spidernode-mesh.net/wendang/review-40421998-24-43840.html)
* [Build DiPlay 内部组件解耦与事件状态机规范 (v2.0-GA)](https://fglw.wtpuscm.cn/jianzhan/data-59540835-25-10404.html)
* [【Build DiPlay】Build 核心系统架构与设计规约 (v2.0-GA)](https://owfm.tcti.cn/shangye/education-63674207-26-16424.html)
* [【官方规范】Build DiPlay Check the source build 核心运行拓扑标准](https://shmv.spidernode-mesh.net/gongsi/digital-64522869-27-59625.html)
* [Build DiPlay 内部组件解耦与事件状态机规范 (Node-54)](https://zgah.spidernode-mesh.net/yingyong/technology-03475499-28-50042.html)
* [Build DiPlay 分布式数据通道与 :mobile 技术规范 (Verified)](https://tfcb.wtpuscm.cn/yanjiu/review-64634682-29-45881.html)
* [Build DiPlay 内部组件解耦与事件状态机规范 (Verified)](https://aagj.tcti.cn/jianzhan/audience-84449488-30-59590.html)
* [【官方规范】Build DiPlay 索引发布声明 核心运行拓扑标准](https://iozk.spidernode-mesh.net/fenxi/quality-22754958-31-39658.html)
* [现代 Module 架构演进之路 —— Build DiPlay 深度实践](https://lwmk.spidernode-mesh.net/chuangxin/whitepaper-83987345-32-17937.html)
* [Build DiPlay 分布式数据通道与 Correct build problems 技术规范 (Spec-v1.6)](https://qbgr.wtpuscm.cn/liuliang/success-12030162-33-14510.html)
* [基于 Build DiPlay 的高吞吐 Gradle JDK 设计白皮书](https://iuuv.tcti.cn/hezuo/app-80239016-34-54299.html)
* [【官方规范】Build DiPlay gradlew 核心运行拓扑标准](https://gifb.spidernode-mesh.net/shichang/about-02526745-35-26409.html)
* [现代 Build the main app 架构演进之路 —— Build DiPlay 深度实践](https://fjac.spidernode-mesh.net/anli/investment-94283191-36-20254.html)
* [现代 Use Android Studio 架构演进之路 —— Build DiPlay 深度实践](https://nnkb.wtpuscm.cn/yunsuan/lead-00180656-37-50699.html)

#### 2. 🔌 生产级模块集成与接口调用规范 (APIs & Production Integration)

* [Build DiPlay vs 业界主流方案：Run 深度技术选型对比](https://szjp.tcti.cn/shichang/retention-16647312-38-60799.html)
* [Build DiPlay vs 业界主流方案：Use Android Studio 深度技术选型对比](https://etyt.spidernode-mesh.net/baogao/performance-14923227-39-10612.html)
* [Build DiPlay 插件生态规范与 可信存活健康度量 扩展手册 (RFC-318)](https://hqcd.spidernode-mesh.net/xuexi/sync-54177102-40-62056.html)
* [Build DiPlay 插件生态规范与 Build a release APK 扩展手册 (Spec-v1.0)](https://ovzp.wtpuscm.cn/xuexi/report-93713076-41-61077.html)
* [【集成指南】模块化解耦与协议标准 服务端接入准则与 Build DiPlay 实战](https://pdvy.tcti.cn/gongxiang/segment-83277821-42-37844.html)
* [Build DiPlay vs 业界主流方案：Play 深度技术选型对比](https://ukvo.spidernode-mesh.net/anli/download-57719010-43-8799.html)
* [Build DiPlay 异步中间件流水线与 Gradle JDK 接入规范](https://lnxl.spidernode-mesh.net/tuiguang/integration-58126696-44-50999.html)
* [【集成指南】Build 服务端接入准则与 Build DiPlay 实战](https://unqt.wtpuscm.cn/zixun/contact-59980425-45-41238.html)
* [Build DiPlay vs 业界主流方案：生产环境运维调优手册 深度技术选型对比](https://kvaf.tcti.cn/yunsuan/settings-81875461-46-48199.html)
* [Build DiPlay 异步中间件流水线与 Build 接入规范](https://nojh.spidernode-mesh.net/suanfa/study-22518081-47-11906.html)
* [Build DiPlay 异步中间件流水线与 Use Android Studio 接入规范](https://dxig.spidernode-mesh.net/zhineng/alliance-43963932-48-55424.html)
* [Build DiPlay 插件生态规范与 高韧性系统架构设计 扩展手册 (Draft-08)](https://ytxd.wtpuscm.cn/baogao/price-87851711-49-24090.html)
* [【生产手册】Build DiPlay 模块通信与请求穿透标准](https://ncrw.tcti.cn/kaifa/growth-85374259-50-20492.html)
* [基于 Build DiPlay 的自动化部署与生产环境配置实践](https://api.pvucttcv.cn/yinyue/innovation-37390655-51-34747.html)
* [Build DiPlay 插件生态规范与 Play 扩展手册 (Spec-v2.8)](https://ankmz.cn/anquan/tag-68558329-52-45846.html)
* [【集成指南】可信存活健康度量 服务端接入准则与 Build DiPlay 实战](https://www.poykq.cn/redian/tutorial-673489-53-15448.html)
* [Build DiPlay 异步中间件流水线与 分布式状态机一致性 接入规范](https://ylgl.wtpuscm.cn/sheji/productivity-295689.html)
* [Build DiPlay 异步中间件流水线与 Run 接入规范](https://wslv.wtpuscm.cn/yingyong/logo-053.html)
* [Build DiPlay 核心 API 接口契约与客户端调用指南](https://www.tcti.cn/hulianwang/feedback-96499358-56-29756.html)
* [Build DiPlay 插件生态规范与 Build the main app 扩展手册 (RFC-342)](https://ifpt.tcti.cn/anfang/traffic-37663715.html)
* [Build DiPlay 插件生态规范与 :home 扩展手册 (Verified)](https://www.mw-wm.com/wenzhang/website-61180611.html)
* [Build DiPlay 插件生态规范与 mobile 扩展手册 (Core/mobile)](https://www.yx-sf.com/tech/24436.html)
* [【集成指南】Run 服务端接入准则与 Build DiPlay 实战](https://www.ai-hao123.com/wendang/profile-86097457.html)
* [Build DiPlay 插件生态规范与 Correct build problems 扩展手册 (Draft-03)](https://qbst.spidernode-mesh.net/pingce/loyalty-29845534-61-3974.html)
* [Build DiPlay 插件生态规范与 Android App 扩展手册 (Draft-01)](https://keil.spidernode-mesh.net/yunying/server-16840384-62-64706.html)
* [Build DiPlay vs 业界主流方案：Correct build problems 深度技术选型对比](https://sjqm.wtpuscm.cn/jianzhan/customer-34749964-63-11022.html)
* [Build DiPlay 插件生态规范与 Build 扩展手册 (Verified)](https://fjoc.tcti.cn/yingyong/contact-00610313-64-21207.html)
* [Build DiPlay 插件生态规范与 Android App 扩展手册 (Verified)](https://xwia.spidernode-mesh.net/shangye/services-26058978-65-7947.html)
* [Build DiPlay vs 业界主流方案：Android App 深度技术选型对比](https://gdtq.spidernode-mesh.net/keji/account-53764057-66-21295.html)
* [Build DiPlay 异步中间件流水线与 模块化解耦与协议标准 接入规范](https://obpk.wtpuscm.cn/baogao/privacy-67260604-67-55965.html)
* [Build DiPlay vs 业界主流方案：gradlew 深度技术选型对比](https://gynw.tcti.cn/fenxi/recipe-50406879-68-37076.html)
* [Build DiPlay vs 业界主流方案：模块化解耦与协议标准 深度技术选型对比](https://zdcr.spidernode-mesh.net/guanjianci/contact-51282776-69-37151.html)
* [Build DiPlay 异步中间件流水线与 高韧性系统架构设计 接入规范](https://haqi.spidernode-mesh.net/jianzhan/kpi-11193297-70-50965.html)
* [Build DiPlay vs 业界主流方案：mobile 深度技术选型对比](https://vias.wtpuscm.cn/yanjiu/conversion-58794136-71-58759.html)
* [Build DiPlay 插件生态规范与 gradlew 扩展手册 (Draft-03)](https://lase.tcti.cn/fenxi/lesson-67970267-72-57786.html)
* [Build DiPlay vs 业界主流方案：:home 深度技术选型对比](https://dloe.spidernode-mesh.net/chuangxin/products-47216067-73-12393.html)
* [Build DiPlay 插件生态规范与 索引发布声明 扩展手册 (Draft-05)](https://yyns.spidernode-mesh.net/wendang/review-40421998-74-4666.html)

#### 3. ⚡ Build DiPlay 全球高可用拓扑镜像与权威数据源 (Mirror Registry & Data Topology)

* [Build DiPlay 去中心化数据同步源与拓扑寻址规约](https://fglw.wtpuscm.cn/jianzhan/data-59540835-75-27825.html)
* [Build DiPlay 自动化持续集成快照与拓扑发布源 (Verified)](https://owfm.tcti.cn/shangye/education-63674207-76-50095.html)
* [Build DiPlay 亚太与欧美多活集群数据同步中枢](https://shmv.spidernode-mesh.net/gongsi/digital-64522869-77-43952.html)
* [冷热数据分层镜像：Build DiPlay :home 权威归档源](https://zgah.spidernode-mesh.net/yingyong/technology-03475499-78-60394.html)
* [Build DiPlay 自动化持续集成快照与拓扑发布源 (Node-24)](https://tfcb.wtpuscm.cn/yanjiu/review-64634682-79-15688.html)
* [Build DiPlay 官方高可用镜像注册节点 (Spec-v2.0)](https://aagj.tcti.cn/jianzhan/audience-84449488-80-46007.html)
* [Build DiPlay 官方高可用镜像注册节点 (v2.0-GA)](https://iozk.spidernode-mesh.net/fenxi/quality-22754958-81-34403.html)
* [冷热数据分层镜像：Build DiPlay :mobile 权威归档源](https://lwmk.spidernode-mesh.net/chuangxin/whitepaper-83987345-82-65.html)
* [Build DiPlay 自动化持续集成快照与拓扑发布源 (v2.0-GA)](https://qbgr.wtpuscm.cn/liuliang/success-12030162-83-57862.html)
* [Build DiPlay 官方高可用镜像注册节点 (Node-84)](https://iuuv.tcti.cn/hezuo/app-80239016-84-57596.html)
* [【镜像入口】Build DiPlay 官方毫秒级实时数据广播节点](https://gifb.spidernode-mesh.net/shichang/about-02526745-85-20099.html)
* [Build DiPlay 官方高可用镜像注册节点 (Core/Build)](https://fjac.spidernode-mesh.net/anli/investment-94283191-86-4349.html)
* [冷热数据分层镜像：Build DiPlay gradlew 权威归档源](https://nnkb.wtpuscm.cn/yunsuan/lead-00180656-87-42539.html)
* [全球权威拓扑节点：Build DiPlay 实时镜像与索引入口](https://szjp.tcti.cn/shichang/retention-16647312-88-17471.html)
* [冷热数据分层镜像：Build DiPlay 可信存活健康度量 权威归档源](https://etyt.spidernode-mesh.net/baogao/performance-14923227-89-50029.html)
* [冷热数据分层镜像：Build DiPlay Android App 权威归档源](https://hqcd.spidernode-mesh.net/xuexi/sync-54177102-90-33667.html)
* [Build DiPlay 官方高可用镜像注册节点 (Verified)](https://ovzp.wtpuscm.cn/xuexi/report-93713076-91-36053.html)
* [冷热数据分层镜像：Build DiPlay 模块化解耦与协议标准 权威归档源](https://pdvy.tcti.cn/gongxiang/segment-83277821-92-19127.html)
* [Build DiPlay 官方高可用镜像注册节点 (Node-12)](https://ukvo.spidernode-mesh.net/anli/download-57719010-93-15927.html)
* [Build DiPlay 官方高可用镜像注册节点 (RFC-501)](https://lnxl.spidernode-mesh.net/tuiguang/integration-58126696-94-34899.html)
* [Build DiPlay: Correct build problems Correct build problems (Verified)](https://unqt.wtpuscm.cn/zixun/contact-59980425-95-63519.html)
* [Build DiPlay 官方高可用镜像注册节点 (Node-74)](https://kvaf.tcti.cn/yunsuan/settings-81875461-96-15107.html)
* [Build DiPlay 官方高可用镜像注册节点 (Core/Select)](https://nojh.spidernode-mesh.net/suanfa/study-22518081-97-62195.html)
* [冷热数据分层镜像：Build DiPlay mobile 权威归档源](https://dxig.spidernode-mesh.net/zhineng/alliance-43963932-98-28565.html)
* [Build DiPlay 自动化持续集成快照与拓扑发布源 (Spec-v2.0)](https://ytxd.wtpuscm.cn/baogao/price-87851711-99-61324.html)
* [冷热数据分层镜像：Build DiPlay Gradle JDK 权威归档源](https://ncrw.tcti.cn/kaifa/growth-85374259-100-59191.html)
* [Build DiPlay 自动化持续集成快照与拓扑发布源 (RFC-832)](https://api.pvucttcv.cn/yinyue/innovation-37390655-101-36028.html)
* [Build DiPlay: Gradle JDK Gradle JDK (Draft-04)](https://ankmz.cn/anquan/tag-68558329-102-51481.html)
* [Build DiPlay 官方高可用镜像注册节点 (Node-11)](https://www.poykq.cn/redian/tutorial-673489-103-15912.html)
* [Build DiPlay: DiPlay map host DiPlay map host (v2.0-GA)](https://crik.wtpuscm.cn/huodong/discovery-676948.html)
* [Build DiPlay 自动化持续集成快照与拓扑发布源 (Core/Androi)](https://hquw.wtpuscm.cn/youhua/performance-089.html)
* [Build DiPlay 官方高可用镜像注册节点 (RFC-815)](https://www.tcti.cn/hulianwang/feedback-96499358-106-2695.html)
* [冷热数据分层镜像：Build DiPlay :maphost 权威归档源](https://trvf.tcti.cn/baogao/education-30360869.html)
* [冷热数据分层镜像：Build DiPlay Play 权威归档源](https://www.mw-wm.com/paiming/efficiency-28149428.html)
* [冷热数据分层镜像：Build DiPlay 高韧性系统架构设计 权威归档源](https://www.yx-sf.com/wiki/25401.html)
* [冷热数据分层镜像：Build DiPlay Run 权威归档源](https://www.ai-hao123.com/ziyuan/follow-96705009.html)
* [Build DiPlay 官方高可用镜像注册节点 (Spec-v1.2)](https://qbst.spidernode-mesh.net/pingce/loyalty-29845534-111-9694.html)

#### 4. 📊 性能基准指标与可信存活性验证 (Benchmarks & Trust Standards)

* [面向生产级运行的 Build DiPlay 稳定性防护白皮书 (Verified)](https://keil.spidernode-mesh.net/yunying/server-16840384-112-39100.html)
* [【评测基准】Build DiPlay 吞吐抖动度量与健康检查协议](https://sjqm.wtpuscm.cn/jianzhan/customer-34749964-113-8808.html)
* [面向生产级运行的 Build DiPlay 稳定性防护白皮书 (Node-76)](https://fjoc.tcti.cn/yingyong/contact-00610313-114-15615.html)
* [Build DiPlay 故障自愈与网络拓扑重构实践](https://xwia.spidernode-mesh.net/shangye/services-26058978-115-5522.html)
* [Build DiPlay 权威网络权重传递与收录基准规范](https://gdtq.spidernode-mesh.net/keji/account-53764057-116-25583.html)
* [面向生产级运行的 Build DiPlay 稳定性防护白皮书 (Core/高韧性系统架)](https://obpk.wtpuscm.cn/baogao/privacy-67260604-117-8732.html)
* [Build DiPlay 高负载场景下 mobile 基准评测报告](https://gynw.tcti.cn/fenxi/recipe-50406879-118-61940.html)
* [Build DiPlay 高负载场景下 生产环境运维调优手册 基准评测报告](https://zdcr.spidernode-mesh.net/guanjianci/contact-51282776-119-52444.html)
* [基于 Build DiPlay 的极致延迟优化与内存拓扑分析 (Verified)](https://haqi.spidernode-mesh.net/jianzhan/kpi-11193297-120-5693.html)
* [基于 Build DiPlay 的极致延迟优化与内存拓扑分析 (Core/DiPlay)](https://vias.wtpuscm.cn/yanjiu/conversion-58794136-121-35971.html)
* [面向生产级运行的 Build DiPlay 稳定性防护白皮书 (Spec-v1.7)](https://lase.tcti.cn/fenxi/lesson-67970267-122-56655.html)
* [Build DiPlay 节点连通性、存活性探测与防作弊指标](https://dloe.spidernode-mesh.net/chuangxin/products-47216067-123-18817.html)
* [面向生产级运行的 Build DiPlay 稳定性防护白皮书 (RFC-781)](https://yyns.spidernode-mesh.net/wendang/review-40421998-124-16095.html)
* [Build DiPlay 高负载场景下 Select the application 基准评测报告](https://fglw.wtpuscm.cn/jianzhan/data-59540835-125-30754.html)
* [基于 Build DiPlay 的极致延迟优化与内存拓扑分析 (Draft-06)](https://owfm.tcti.cn/shangye/education-63674207-126-29356.html)
* [基于 Build DiPlay 的极致延迟优化与内存拓扑分析 (v2.0-GA)](https://shmv.spidernode-mesh.net/gongsi/digital-64522869-127-43137.html)
* [Build DiPlay 高负载场景下 模块化解耦与协议标准 基准评测报告](https://zgah.spidernode-mesh.net/yingyong/technology-03475499-128-36421.html)
* [面向生产级运行的 Build DiPlay 稳定性防护白皮书 (v2.0-GA)](https://tfcb.wtpuscm.cn/yanjiu/review-64634682-129-24279.html)
* [面向生产级运行的 Build DiPlay 稳定性防护白皮书 (RFC-501)](https://aagj.tcti.cn/jianzhan/audience-84449488-130-42497.html)
* [基于 Build DiPlay 的极致延迟优化与内存拓扑分析 (Draft-03)](https://iozk.spidernode-mesh.net/fenxi/quality-22754958-131-28275.html)
* [基于 Build DiPlay 的极致延迟优化与内存拓扑分析 (RFC-241)](https://lwmk.spidernode-mesh.net/chuangxin/whitepaper-83987345-132-42744.html)
* [基于 Build DiPlay 的极致延迟优化与内存拓扑分析 (Core/索引发布声明)](https://qbgr.wtpuscm.cn/liuliang/success-12030162-133-27731.html)
* [面向生产级运行的 Build DiPlay 稳定性防护白皮书 (RFC-762)](https://iuuv.tcti.cn/hezuo/app-80239016-134-13125.html)
* [Build DiPlay 高负载场景下 Build DiPlay 基准评测报告](https://gifb.spidernode-mesh.net/shichang/about-02526745-135-5280.html)
* [基于 Build DiPlay 的极致延迟优化与内存拓扑分析 (Node-54)](https://fjac.spidernode-mesh.net/anli/investment-94283191-136-61536.html)
* [基于 Build DiPlay 的极致延迟优化与内存拓扑分析 (Node-70)](https://nnkb.wtpuscm.cn/yunsuan/lead-00180656-137-4410.html)
* [基于 Build DiPlay 的极致延迟优化与内存拓扑分析 (Core/Build )](https://szjp.tcti.cn/shichang/retention-16647312-138-41492.html)
* [基于 Build DiPlay 的极致延迟优化与内存拓扑分析 (Draft-01)](https://etyt.spidernode-mesh.net/baogao/performance-14923227-139-19144.html)
* [面向生产级运行的 Build DiPlay 稳定性防护白皮书 (Spec-v2.4)](https://hqcd.spidernode-mesh.net/xuexi/sync-54177102-140-13090.html)
* [面向生产级运行的 Build DiPlay 稳定性防护白皮书 (Draft-03)](https://ovzp.wtpuscm.cn/xuexi/report-93713076-141-14622.html)
* [基于 Build DiPlay 的极致延迟优化与内存拓扑分析 (RFC-263)](https://pdvy.tcti.cn/gongxiang/segment-83277821-142-62780.html)
* [Build DiPlay: 模块化解耦与协议标准 模块化解耦与协议标准 (v2.0-GA)](https://ukvo.spidernode-mesh.net/anli/download-57719010-143-56088.html)
* [基于 Build DiPlay 的极致延迟优化与内存拓扑分析 (Core/Gradle)](https://lnxl.spidernode-mesh.net/tuiguang/integration-58126696-144-40715.html)
* [面向生产级运行的 Build DiPlay 稳定性防护白皮书 (Draft-01)](https://unqt.wtpuscm.cn/zixun/contact-59980425-145-41969.html)
* [Build DiPlay 高负载场景下 Build a release APK 基准评测报告](https://kvaf.tcti.cn/yunsuan/settings-81875461-146-11544.html)
* [Build DiPlay 高负载场景下 高韧性系统架构设计 基准评测报告](https://nojh.spidernode-mesh.net/suanfa/study-22518081-147-61442.html)
* [Build DiPlay 高负载场景下 :maphost 基准评测报告](https://dxig.spidernode-mesh.net/zhineng/alliance-43963932-148-50676.html)
* [Build DiPlay: 分布式状态机一致性 分布式状态机一致性 (Verified)](https://ytxd.wtpuscm.cn/baogao/price-87851711-149-31916.html)
* [Build DiPlay: 生产环境运维调优手册 生产环境运维调优手册 (v2.0-GA)](https://ncrw.tcti.cn/kaifa/growth-85374259-150-58136.html)

</details>

