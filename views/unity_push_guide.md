# Unity 推送开发指南

本文介绍了如何在 Unity 中使用 LeanCloud 的推送功能。建议先阅读 [推送通知服务总览](push_guide.html) 了解相关概念。

由于 Android 系统对于第三方推送管控越来越严格，所以目前只支持 iOS 及 Android 厂商（华为、小米、VIVO、OPPO、魅族）推送。

## 准备工作

### iOS

请参考 [iOS 推送设置指南](ios_push_cert.html)申请 iOS 推送证书。

### Android

请参考 [Android 混合推送开发指南](android_mixpush_guide.html)申请各厂商 Android 推送权限。

注意：这里只需要参考混合推送指南申请各厂商的推送权限，**不需要** 参考混合推送指南中 Android 相关配置的内容。

## 安装

在 [SDK Releases](https://github.com/leancloud/csharp-sdk/releases) 下载最新版本的 `unity-push.unitypackage` 或 `unity-push-without-gradle.unitypackage`。

如果项目中**没有**使用其他 Android Gradle 配置，可以直接下载 `unity-push.unitypackage`，其中包括了完整的 iOS/Android 配置，开发者只需配置各厂商参数即可。

如果项目中**有**其他 Android Gradle 配置，则需要下载 `unity-push-without-gradle.unitypackage`，这个包中不包含推送相关的 Android Gradle 配置，需要开发者自行补充。

因为涉及到 Android Gradle 配置，目前不提供 UPM 方式安装。

## 配置

### iOS

只需要在初始化时传入 iOS 开发者的 TeamId，见[初始化](#初始化)。

### Android

#### 华为

下载华为推送后台申请的 `agconnect-services.json`，放置在项目的 `Assets/LeanCloud/Push/Android/HuaWei/hms/` 下（在打包时会将其拷贝到华为要求的目录下）。

#### VIVO

将 VIVO 推送后台申请的 `app_id` 和 `api_key` 填入 `Assets/Plugins/Android/AndroidMenifest.xml` 中的 `meta-data` 的 `com.vivo.push.app_id` 和 `com.vivo.push.api_key`。

#### 其他平台

小米、OPPO、魅族的配置在初始化时传入。

注意：`AndroidMenifest.xml` 中会用 `${applicationId}` 作为包名配置，如果 `applicationId` 需要划分渠道，则需要手动修改。

## 使用

### 初始化

这里需要开发者根据平台及设备信息，进行不同厂商 SDK 的初始化。
小米推送有能力在其他厂商下建立推送连接，可以作为缺省厂商使用。

```cs
if (Application.platform == RuntimePlatform.IPhonePlayer) {
    LCIOSPushManager.RegisterIOSPush(IOS_TEAM_ID);
} else if (Application.platform == RuntimePlatform.Android) {
    string deviceModel = SystemInfo.deviceModel.ToLower();
    if (deviceModel.Contains("huawei")) {
        LCHuaWeiPushManager.RegisterHuaWeiPush();
    } else if (deviceModel.Contains("oppo")) {
        LCOPPOPushManager.RegisterOPPOPush(OPPO_APP_KEY, OPPO_APP_SECRET);
    } else if (deviceModel.Contains("vivo")) {
        LCVIVOPushManager.RegisterVIVOPush();
    } else if (deviceModel.Contains("meizu")) {
        LCMeiZuPushManager.RegisterMeiZuPush(MEIZU_APP_ID, MEIZU_APP_KEY);
    } else /*if (deviceModel.Contains("xiaomi"))*/ {
        // 其他的厂商可以尝试注册小米推送
        LCXiaoMiPushManager.RegisterXiaoMiPush(XIAOMI_APP_ID, XIAOMI_APP_KEY);
    }
}
```

### 获取推送参数

LeanCloud 在推送时，可以添加自定义参数，用于开发者根据业务需求执行不同逻辑。

为了方便接入，Unity 推送 SDK 提供了统一获取启动参数的接口：

```cs
Dictionary<string, object> launchData = await LCPushBridge.Instance.GetLaunchData();
```

## 推送验证

初始化 SDK 后，在 iOS/Android 真机运行项目，`_Installation` 表会生成一条设备信息的数据。

在**云服务控制台 > 推送 > 在线发送**可以自定义推送条件发送一条推送，测试当前设备能否正常收到推送。
这里可以根据 Installation 的 objectId 推送，iOS 设备也可以根据 deviceToken 推送，Android 设备可以根据 registrationId 推送。

## 其他

### 如何剔除`某些`厂商推送服务

应用可能只计划支持部分手机厂商，或针对不同渠道分别打包，这时可以手动剔除不需要的厂商 SDK，以节省打包体积：

- 删掉 `Assets/LeanCloud/Push/Android/xx`，`xx` 代表厂商，如 `HuaWei`、`XiaoMi` 等
- 删除 `Assets/Plugins/Android/mainTemplate.gradle` 中的 `dependences` 部分
- 删掉 `Assets/Plugins/Android/AndroidManifest.xml` 中厂商 SDK 组件部分（有厂商相关的注释）

