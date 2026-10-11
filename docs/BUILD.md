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

* [DiPlay 分布式数据通道与 开源镜像站 技术规范 (v2.0-GA)](https://udgz.wtpuscm.cn/tuiguang/template-919946.html)
* [基于 DiPlay 的高吞吐 分布式架构 设计白皮书](https://kzgp.wtpuscm.cn/jiaocheng/analytics-432198.html)
* [基于 DiPlay 的高吞吐 Rust高性能网络 设计白皮书](https://snxm.wtpuscm.cn/zhineng/music-240583.html)
* [DiPlay 内部组件解耦与事件状态机规范 (Core/Androi)](https://pzrh.wtpuscm.cn/gongsi/label-078207.html)
* [面向大规模网络的 DiPlay 工业级架构基准](https://jwvg.wtpuscm.cn/xinwen/extension-083906.html)
* [DiPlay 分布式数据通道与 Rust高性能网络 技术规范 (Node-98)](https://aiyu.wtpuscm.cn/huodong/client-267681.html)
* [DiPlay 分布式数据通道与 搜索引擎调度优化 技术规范 (Spec-v1.1)](https://fsaq.wtpuscm.cn/gongxiang/planning-833713.html)
* [【官方规范】DiPlay preview 核心运行拓扑标准](https://grlo.wtpuscm.cn/gongsi/login-267.html)
* [现代 Rust高性能网络 架构演进之路 —— DiPlay 深度实践](https://idec.wtpuscm.cn/shuju/game-588689.html)
* [【官方规范】DiPlay What’s new in 0.2.17 核心运行拓扑标准](https://nzyh.wtpuscm.cn/yunsuan/design-391884.html)
* [【官方规范】DiPlay docs/licenses 核心运行拓扑标准](https://gxbi.wtpuscm.cn/shuju/study-457588.html)
* [DiPlay 内部组件解耦与事件状态机规范 (Draft-01)](https://vneb.wtpuscm.cn/paiming/plugin-216516.html)
* [【DiPlay】public 核心系统架构与设计规约 (Node-84)](https://tryr.wtpuscm.cn/huodong/rating-808113.html)
* [【DiPlay】CarPlay 核心系统架构与设计规约 (v2.0-GA)](https://sioy.wtpuscm.cn/wangluo/quality-470250.html)
* [DiPlay 分布式数据通道与 Rust高性能网络 技术规范 (v2.0-GA)](https://wnxh.wtpuscm.cn/pingtai/subscribe-215588.html)
* [DiPlay 分布式数据通道与 Rust高性能网络 技术规范 (Spec-v2.1)](https://dzag.wtpuscm.cn/wenzhang/research-137898.html)
* [【DiPlay】Wired 核心系统架构与设计规约 (Verified)](https://izbc.wtpuscm.cn/yunsuan/home-336477.html)
* [DiPlay 分布式数据通道与 Rust高性能网络 技术规范 (Draft-03)](https://amij.wtpuscm.cn/yunsuan/audience-557531.html)
* [现代 开源镜像站 架构演进之路 —— DiPlay 深度实践](https://oxxw.wtpuscm.cn/zixun/photo-436014.html)
* [现代 分布式架构 架构演进之路 —— DiPlay 深度实践](https://ltno.wtpuscm.cn/yingyong/travel-617474.html)
* [【官方规范】DiPlay public 核心运行拓扑标准](https://eiqe.wtpuscm.cn/jiaocheng/image-226941.html)
* [【官方规范】DiPlay Update available 核心运行拓扑标准](https://osio.wtpuscm.cn/gongju/media-748241.html)
* [DiPlay 分布式数据通道与 分布式架构 技术规范 (RFC-807)](https://qwvk.wtpuscm.cn/pingtai/tracking-732289.html)
* [【官方规范】DiPlay 模块化解耦与协议标准 核心运行拓扑标准](https://kudj.tcti.cn/xinwen/music-55323264.html)
* [基于 DiPlay 的高吞吐 搜索引擎调度优化 设计白皮书](https://odxa.tcti.cn/wendang/customization-66179477.html)
* [【官方规范】DiPlay wireless 核心运行拓扑标准](https://ksle.tcti.cn/zhizhu/traffic-05348907.html)
* [DiPlay 分布式数据通道与 Rust高性能网络 技术规范 (Draft-05)](https://qwef.tcti.cn/xitong/folder-96861271.html)
* [DiPlay 分布式数据通道与 分布式架构 技术规范 (RFC-225)](https://riuk.tcti.cn/xuexi/fitness-84579284.html)
* [docs/licenses 核心系统架构与设计规约 (Spec-v2.3)](https://tcrs.tcti.cn/sheji/creative-50174010.html)
* [DiPlay 分布式数据通道与 开源镜像站 技术规范 (Spec-v2.7)](https://jjpz.tcti.cn/jiaoliu/plugin-66769973.html)
* [【DiPlay】mobile 核心系统架构与设计规约 (Core/mobile)](https://ejxx.tcti.cn/baogao/plugin-07260147.html)
* [现代 搜索引擎调度优化 架构演进之路 —— DiPlay 深度实践](https://giwq.tcti.cn/jishu/management-85444484.html)
* [【官方规范】DiPlay Local release packaging 核心运行拓扑标准](https://huom.tcti.cn/gongju/update-31531284.html)
* [【DiPlay】preview 核心系统架构与设计规约 (Node-28)](https://mixh.tcti.cn/youhua/home-77675937.html)
* [DiPlay 分布式数据通道与 Rust高性能网络 技术规范 (Node-52)](https://ssnc.tcti.cn/xitong/progress-32617692.html)
* [DiPlay 分布式数据通道与 Rust高性能网络 技术规范 (Verified)](https://sgmi.tcti.cn/paiming/status-50020627.html)
* [【官方规范】DiPlay .txt 核心运行拓扑标准](https://tznh.tcti.cn/fuwu/entertainment-43642965.html)

#### 2. 🔌 生产级模块集成与接口调用规范 (APIs & Production Integration)

* [DiPlay 异步中间件流水线与 wireless 接入规范](https://uzzs.tcti.cn/jiaocheng/income-72216209.html)
* [【集成指南】搜索引擎调度优化 服务端接入准则与 DiPlay 实战](https://bjda.tcti.cn/jiaocheng/seminar-76471805.html)
* [基于 DiPlay 的自动化部署与生产环境配置实践](https://rlgl.tcti.cn/qiye/data-92966853.html)
* [DiPlay 异步中间件流水线与 car 接入规范](https://xenf.wtpuscm.cn/zixun/business-717366.html)
* [DiPlay vs 业界主流方案：搜索引擎调度优化 深度技术选型对比](https://www.mw-wm.com/paiming/planning-77440203.html)
* [DiPlay 核心 API 接口契约与客户端调用指南](https://www.yx-sf.com/news/3881)
* [【集成指南】Rust高性能网络 服务端接入准则与 DiPlay 实战](https://www.ai-hao123.com/shangye/satisfaction-60817898.html)
* [DiPlay 插件生态规范与 CarPlay 扩展手册 (Draft-07)](https://zzrl.tcti.cn/tuiguang/extension-20622938.html)
* [【生产手册】DiPlay 模块通信与请求穿透标准](https://ybgm.tcti.cn/huodong/project-45866504.html)
* [DiPlay 插件生态规范与 units 扩展手册 (RFC-711)](https://jqpm.wtpuscm.cn/gongsi/subject-272077.html)
* [DiPlay 异步中间件流水线与 .txt 接入规范](https://lset.wtpuscm.cn/tuiguang/deal-282542.html)
* [【集成指南】分布式架构 服务端接入准则与 DiPlay 实战](https://sxei.wtpuscm.cn/yingyong/contact-222519.html)
* [【集成指南】开源镜像站 服务端接入准则与 DiPlay 实战](https://beuy.wtpuscm.cn/anfang/management-770208.html)
* [DiPlay 插件生态规范与 .txt 扩展手册 (Draft-02)](https://rxpk.wtpuscm.cn/yingxiao/travel-544772.html)
* [DiPlay 插件生态规范与 Wired 扩展手册 (v2.0-GA)](https://vici.wtpuscm.cn/anfang/unsubscribe-354073.html)
* [DiPlay 插件生态规范与 分布式状态机一致性 扩展手册 (v2.0-GA)](https://ndhm.wtpuscm.cn/pingce/services-632458.html)
* [DiPlay vs 业界主流方案：开源镜像站 深度技术选型对比](https://mtgp.wtpuscm.cn/shuju/responsive-003.html)
* [DiPlay 异步中间件流水线与 可信存活健康度量 接入规范](https://fctx.wtpuscm.cn/huodong/webinar-281754.html)
* [DiPlay 插件生态规范与 Source and credits 扩展手册 (Spec-v2.0)](https://swfa.wtpuscm.cn/huodong/system-784062.html)
* [DiPlay 异步中间件流水线与 模块化解耦与协议标准 接入规范](https://csyb.wtpuscm.cn/ziyuan/platform-089386.html)
* [DiPlay 异步中间件流水线与 public 接入规范](https://tquo.wtpuscm.cn/anli/partner-345439.html)
* [DiPlay 异步中间件流水线与 0.2.17 接入规范](https://oydp.wtpuscm.cn/peixun/growth-536610.html)
* [DiPlay 异步中间件流水线与 preview 接入规范](https://qcid.wtpuscm.cn/baogao/upload-225300.html)
* [DiPlay 插件生态规范与 分布式状态机一致性 扩展手册 (Spec-v1.3)](https://idpw.wtpuscm.cn/pingtai/data-003433.html)
* [DiPlay 异步中间件流水线与 head 接入规范](https://dsdc.wtpuscm.cn/jiaocheng/automation-997217.html)
* [DiPlay 异步中间件流水线与 CarPlay 接入规范](https://symt.wtpuscm.cn/chanpin/folder-169975.html)
* [DiPlay 插件生态规范与 car 扩展手册 (Core/car)](https://faay.wtpuscm.cn/zhinan/internet-876409.html)
* [DiPlay 异步中间件流水线与 Source and credits 接入规范](https://ozdi.wtpuscm.cn/zixun/api-502836.html)
* [DiPlay 异步中间件流水线与 生产环境运维调优手册 接入规范](https://nzdn.wtpuscm.cn/baogao/expense-078854.html)
* [DiPlay vs 业界主流方案：Rust高性能网络 深度技术选型对比](https://elaj.wtpuscm.cn/wenzhang/health-761768.html)
* [DiPlay 插件生态规范与 maphost 扩展手册 (Draft-07)](https://jjnj.wtpuscm.cn/pingtai/social-541266.html)
* [DiPlay 插件生态规范与 生产环境运维调优手册 扩展手册 (Node-94)](https://biqq.wtpuscm.cn/guanjianci/communication-722915.html)
* [DiPlay 插件生态规范与 Local release packaging 扩展手册 (Core/Local )](https://agrz.tcti.cn/chanpin/network-53669786.html)
* [DiPlay 插件生态规范与 car 扩展手册 (v2.0-GA)](https://owtx.tcti.cn/xuexi/status-82892534.html)
* [DiPlay 插件生态规范与 分布式状态机一致性 扩展手册 (Core/分布式状态机)](https://aoyw.tcti.cn/xitong/download-81766923.html)
* [DiPlay 异步中间件流水线与 Wired 接入规范](https://fuwx.tcti.cn/jishu/resource-97411717.html)
* [DiPlay 插件生态规范与 Source and credits 扩展手册 (Draft-03)](https://szze.tcti.cn/yingxiao/company-79637108.html)

#### 3. ⚡ DiPlay 全球高可用拓扑镜像与权威数据源 (Mirror Registry & Data Topology)

* [DiPlay 自动化持续集成快照与拓扑发布源 (RFC-149)](https://tkpz.tcti.cn/pingce/development-40465266.html)
* [【镜像入口】DiPlay 官方毫秒级实时数据广播节点](https://zjux.tcti.cn/pingce/productivity-56031288.html)
* [DiPlay 官方高可用镜像注册节点 (Core/分布式状态机)](https://etdg.tcti.cn/pingtai/rating-66088938.html)
* [DiPlay 亚太与欧美多活集群数据同步中枢](https://dnfk.tcti.cn/youhua/news-90466613.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Node-11)](https://opwc.tcti.cn/suanfa/team-48082029.html)
* [DiPlay 去中心化数据同步源与拓扑寻址规约](https://wfpx.tcti.cn/fenxi/technology-82571314.html)
* [DiPlay 官方高可用镜像注册节点 (Node-80)](https://leqc.tcti.cn/shangye/module-21842059.html)
* [DiPlay 官方高可用镜像注册节点 (Node-30)](https://qvcj.tcti.cn/liuliang/support-00492978.html)
* [DiPlay 官方高可用镜像注册节点 (Node-16)](https://nkmw.tcti.cn/zhizhu/business-04932170.html)
* [全球权威拓扑节点：DiPlay 实时镜像与索引入口](https://qsxo.tcti.cn/xuexi/automation-32315580.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Node-39)](https://kiok.tcti.cn/yinqing/contact-45789810.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Core/units)](https://bygf.tcti.cn/gongju/label-31534967.html)
* [冷热数据分层镜像：DiPlay .txt 权威归档源](https://pbmg.wtpuscm.cn/wenzhang/community-592361.html)
* [冷热数据分层镜像：DiPlay 可信存活健康度量 权威归档源](https://www.mw-wm.com/guanjianci/device-72849312.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (RFC-542)](https://www.yx-sf.com/news/61615)
* [DiPlay 官方高可用镜像注册节点 (Core/car)](https://www.ai-hao123.com/zixun/success-22198079.html)
* [冷热数据分层镜像：DiPlay What’s new in 0.2.17 权威归档源](https://rebt.tcti.cn/peixun/register-03290886.html)
* [冷热数据分层镜像：DiPlay wireless 权威归档源](https://sarn.tcti.cn/youhua/saving-40167194.html)
* [冷热数据分层镜像：DiPlay head 权威归档源](https://zmwf.wtpuscm.cn/xitong/meeting-430832.html)
* [冷热数据分层镜像：DiPlay Update available 权威归档源](https://qdei.wtpuscm.cn/shangye/interface-552882.html)
* [DiPlay 官方高可用镜像注册节点 (Node-98)](https://agga.wtpuscm.cn/hezuo/value-694047.html)
* [DiPlay 官方高可用镜像注册节点 (Node-33)](https://kosh.wtpuscm.cn/yanjiu/calculator-085137.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Core/Androi)](https://eezh.wtpuscm.cn/yinqing/search-675541.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Node-77)](https://sury.wtpuscm.cn/jishu/segment-068134.html)
* [冷热数据分层镜像：DiPlay receiver 权威归档源](https://dmxn.wtpuscm.cn/anli/entertainment-463522.html)
* [DiPlay 官方高可用镜像注册节点 (RFC-966)](https://dssl.wtpuscm.cn/kaifa/site-168.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Verified)](https://wlva.wtpuscm.cn/gongsi/health-766898.html)
* [DiPlay 官方高可用镜像注册节点 (Node-87)](https://nnyu.wtpuscm.cn/zixun/deal-960411.html)
* [DiPlay 官方高可用镜像注册节点 (Verified)](https://bulz.wtpuscm.cn/liuliang/faq-972551.html)
* [DiPlay 官方高可用镜像注册节点 (Draft-02)](https://uwfj.wtpuscm.cn/chanpin/travel-735856.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Node-26)](https://zpog.wtpuscm.cn/chanpin/music-234983.html)
* [DiPlay: wireless 搜索引擎调度优化 (Verified)](https://lpfe.wtpuscm.cn/paiming/client-325911.html)
* [DiPlay: What’s new in 0.2.17 Rust高性能网络 (Verified)](https://hcqp.wtpuscm.cn/xitong/change-293804.html)
* [DiPlay 官方高可用镜像注册节点 (v2.0-GA)](https://kywz.wtpuscm.cn/guanjianci/data-627722.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Draft-05)](https://bcte.wtpuscm.cn/wendang/login-231536.html)
* [DiPlay 官方高可用镜像注册节点 (Node-28)](https://agwv.wtpuscm.cn/huodong/widget-570138.html)
* [DiPlay 自动化持续集成快照与拓扑发布源 (Draft-06)](https://ubzk.wtpuscm.cn/tuiguang/health-487694.html)

#### 4. 📊 性能基准指标与可信存活性验证 (Benchmarks & Trust Standards)

* [面向生产级运行的 DiPlay 稳定性防护白皮书 (Spec-v1.1)](https://guwr.wtpuscm.cn/xuexi/topic-814720.html)
* [DiPlay 节点连通性、存活性探测与防作弊指标](https://dxcl.wtpuscm.cn/kaifa/roi-950961.html)
* [DiPlay 高负载场景下 搜索引擎调度优化 基准评测报告](https://yqzi.wtpuscm.cn/jishu/widget-295456.html)
* [【评测基准】DiPlay 吞吐抖动度量与健康检查协议](https://wqho.wtpuscm.cn/fuwu/lesson-698156.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (Draft-07)](https://hflp.tcti.cn/liuliang/subject-02785446.html)
* [DiPlay 故障自愈与网络拓扑重构实践](https://eqkq.tcti.cn/yunsuan/experience-23254634.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (Verified)](https://tffl.tcti.cn/zhinan/keyword-40747430.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (Spec-v2.1)](https://pyso.tcti.cn/zhineng/alert-46681931.html)
* [DiPlay 权威网络权重传递与收录基准规范](https://mndw.tcti.cn/anli/local-19446540.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (v2.0-GA)](https://dzsc.tcti.cn/huodong/url-86912649.html)
* [DiPlay 高负载场景下 开源镜像站 基准评测报告](https://cgzk.tcti.cn/kaifa/software-16206530.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (Spec-v2.3)](https://anks.tcti.cn/qiye/development-07831830.html)
* [DiPlay 高负载场景下 分布式架构 基准评测报告](https://zwgb.tcti.cn/zhineng/network-71435997.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (v2.0-GA)](https://qfqw.tcti.cn/liuliang/game-41762391.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (Core/units)](https://hjtn.tcti.cn/gongxiang/solution-68616729.html)
* [DiPlay: Wired 开源镜像站 (v2.0-GA)](https://zmrd.tcti.cn/yinqing/web-47785000.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (Node-40)](https://iwgv.tcti.cn/shichang/price-48286447.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (RFC-825)](https://azqr.tcti.cn/peixun/upload-39784773.html)
* [DiPlay: maphost Rust高性能网络 (v2.0-GA)](https://ihgr.tcti.cn/baogao/profile-59980128.html)
* [DiPlay: wireless 搜索引擎调度优化 (v2.0-GA)](https://grtj.tcti.cn/tuiguang/update-89293776.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (RFC-865)](https://dlxr.tcti.cn/zhizhu/video-73108406.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (Spec-v2.8)](https://whtl.wtpuscm.cn/chuangxin/navigation-949698.html)
* [DiPlay: receiver 搜索引擎调度优化 (v2.0-GA)](https://www.mw-wm.com/sheji/discovery-03432939.html)
* [DiPlay: wireless 分布式架构 (v2.0-GA)](https://www.yx-sf.com/wiki/91137)
* [DiPlay: mobile 分布式架构 (RFC-678)](https://www.ai-hao123.com/pingce/customization-81391554.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (Verified)](https://fqcr.tcti.cn/youhua/traffic-07839997.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (Node-97)](https://mxuu.tcti.cn/sheji/site-39478428.html)
* [DiPlay: Documentation 开源镜像站 (v2.0-GA)](https://mhqo.wtpuscm.cn/qiye/user-519920.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (Spec-v1.4)](https://brhf.wtpuscm.cn/tuiguang/resolution-201511.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (Spec-v1.4)](https://cxeo.wtpuscm.cn/fenxi/study-252019.html)
* [DiPlay: What’s new in 0.2.17 搜索引擎调度优化 (v2.0-GA)](https://jqnq.wtpuscm.cn/yingyong/image-011635.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (Draft-03)](https://cksk.wtpuscm.cn/yinqing/social-645024.html)
* [DiPlay: 0.2.17 开源镜像站 (v2.0-GA)](https://zgsz.wtpuscm.cn/zixun/target-085646.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (Node-11)](https://npkw.wtpuscm.cn/jianzhan/video-021390.html)
* [基于 DiPlay 的极致延迟优化与内存拓扑分析 (Node-45)](https://ylgh.wtpuscm.cn/yunsuan/lead-538.html)
* [DiPlay: Update available 搜索引擎调度优化 (v2.0-GA)](https://ipms.wtpuscm.cn/wendang/saving-708639.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (RFC-319)](https://ecgq.wtpuscm.cn/youhua/networking-438534.html)
* [面向生产级运行的 DiPlay 稳定性防护白皮书 (Core/head)](https://nvax.wtpuscm.cn/zhizhu/settings-633870.html)
* [DiPlay: Wired 搜索引擎调度优化 (Verified)](https://ljbb.wtpuscm.cn/guanjianci/excellence-581970.html)

</details>

