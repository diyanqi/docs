{% import "views/_data.njk" as data %}
{% import "views/_parts.html" as include %}
{% import "views/_helper.njk" as docs %}
{% import "views/_im.njk" as im %}
{% import "views/leanstorage-faq.md" as leanstorage %}

# 常见问题一览

## 账户和控制台常见问题

### LeanCloud 部署在哪个云平台上

LeanCloud 部署在国内多个云计算平台上，并采用在双线机房内同时使用虚拟机和实体机的混合部署策略，来保证应用的访问体验和可靠性。

{% if node!='qcloud' %}
### 获取客服支持有哪些途径

* 到免费的 [用户社区](https://forum.leancloud.cn/) 进行提问。
* 商用版应用的所有者可以进入 [工单系统](https://leanticket.cn/t/leancloud) 来提交问题。
* 账号相关事项，可发送邮件到 {{ include.supportEmail() }} 获取帮助。
* 紧急故障拨打客服电话：+8618625038918。
* 售前咨询请致电 +8613011098244。

### 如果没有缴费会怎么样

账户余额小于 0 时，账户服务将被停止，即云端会拒绝所有的请求，因此应用的统计数据也无法生成；应用数据被置于不可见模式，但仍会在 LeanCloud 云端保留 30 天。如需要恢复服务和访问应用数据，请登录控制台充值。

{{ docs.alert("欠费超过 30 天，应用内的数据（包括文件）会被删除且无法恢复。") }}

你可以在控制台设置告警余额，当账户余额小于设置的值时，我们会发送短信、邮件通知。请开发者务必关注账户的余额情况，以免对业务造成影响。

### 欠费期间应用产生的数据和请求能否找回

不能。因为欠费时应用处于禁用阶段，所有的请求都会被云端丢弃，与统计相关的数据也不会生成，所以当应用服务恢复后也无法找回这一期间的数据。为避免统计数据出现断档，请在控制台中设置告警余额，及时充值，保证服务的持续。

### 如何付费

* 支付宝充值

  进入 **控制台 > 财务 > 概览**，点击「余额充值」按钮时将会出现「支付宝充值」窗口。 我们将每天自动从您的账户余额里扣除前一日的费用。每次扣费优先使用充值金额，其次是赠送金额。

* 对公账户汇款

  账户信息请通过 **控制台 > 财务 > 概览** 查看（点击「余额充值」按钮后显示）。 

  <div class="callout callout-danger">请务必在汇款附言里中注明以下信息，以便我们账务确认汇款的来源和用途，及时入账。
  <ol>
  <li>您的 LeanCloud 用户名</li>
  <li>（或）注册邮箱</li>
  </ol>
  </div>

### 如何申请开具发票

请参考[控制台指南 > 账单和发票](dashboard_guide.html#账单和发票)一节的说明。

### 哪里获取平台的更新信息

通常情况下，我们新版本的更新周期为一到两周。获取更新信息可以通过：

* [官方博客](https://leancloudblog.com/)（每次更新的详细信息都会发布在那里）
* [官方微博](http://weibo.com/avoscloud)
* 官方微信公众号：LeanCloud通讯
* 每月初，我们会将每月的更新摘要发送到您的注册邮箱。
* 在控制台页面的右上方有消息中心，请注意查看新通知。
{% endif %}
### 如何重命名 Class？

LeanCloud 不支持重命名 Class。
你可以新建一个 Class，利用 [数据导入导出](rest_api.html#数据导出_API) 功能从旧 Class 导出数据，然后导入到新 Class。
导出导入期间不要往旧 Class 写数据，或者在旧 Class 和新 Class 双写数据，以避免数据不一致。

### 如何导入或者导出数据？

请参考《数据与安全》文档的 [导入数据](dashboard_guide.html#导入数据) 和 [导出数据](dashboard_guide.html#导出数据) 部分。


### 如何在 App 邮件内完全使用自己的品牌


  
### 创建唯一索引失败

请确认想要创建索引的列没有已经存在的重复值。

### 如何上传文件

任何一个 Class 如果有 File 类型的列，就可以直接在 **数据** 管理平台中将文件上传到该列。如果没有，请自行创建列，类型指定为 File。

### 如何在应用之间共享数据

请参考 [控制台指南](dashboard_guide.html#应用之间共享部分数据) 绑定 Class。

## 数据存储常见问题

### API 调用次数有什么限制吗

开发版提供每天三万次的免费额度。
推送服务免费使用，并不占用免费额度，推送消息接口的调用受频率限制，具体参考：[推送消息接口的限制](push_guide.html#推送消息接口) 文档。

默认情况下，商用版应用同一时刻最多可使用的工作线程数为 60，即同一时刻最多可以同时处理 60 个数据请求。**我们会根据应用运行状况以及运维需要调整该值**。如果需要提高这一上限，请 [提交工单](https://leanticket.cn/t/leancloud) 进行申请。

### API 调用次数的计算

对于数据存储来说，每次 `create` 和 `update` 一个对象的数据算 1 次请求，如调用 1 次 `object.saveInBackground` 算 1 次 API 请求。在 API 调用失败的情况下，如果是由于 [应用流控超限（错误码 429）](error_code.html#_429) 而被云端拒绝，则**不会**算成 1 次请求；如果是其他原因，例如 [权限不够（错误码 430）](error_code.html#_403)，那么仍会算为 1 次请求。

**一次请求**<br/>
- `create`
- `save`
- `fetch`
- `find`
- `delete`
- `deleteAll`

调用一次 `fetch` 或 `find` 通过 `include` 返回了 100 个关联对象，算 1 次 API 请求。调用一次 `find` 或 `deleteAll` 来查找或删除 500 条记录，只算 1 次 API 请求。

**多次请求**<br/>
- `saveAll`
- `fetchAll`

调用一次 `saveAll` 或 `fetchAll` 来保存或获取 array 里面 100 个 对象，算 100 次 API 请求。

{% if node != 'qcloud' %}
对于 [社交信息流](status_system.html)，`create` 和 `update` 按照 Status 和 Follower/Followee 的对象数量来计费。
{% endif %}

对于 query 则是按照请求数来计费，与结果的大小无关。`query.count` 算 1 次 API 请求。collection fetch 也是按照请求次数来计费。

### 如何获取 API 的访问日志

进入 **控制台 > 数据存储 > API 访问日志**，开启日志服务，稍后刷新页面，就可以看到从开启到当前时间产生的日志了。

### 其他语言调用 REST API 如何对参数进行编码

REST API 文档使用 curl 作为示范，其中 `--data-urlencode` 表示要对参数进行 URL encode 编码。如果是 GET 请求，直接将经过 URL encode 的参数通过 `&` 连接起来，放到 URL 的问号后。如 `https://{{host}}/1.1/login?username=xxxx&password=xxxxx`。

### 如何实现大小写不敏感的查询

目前不提供直接支持，可采用正则表达式查询的办法，具体参考 [StackOverflow - MongoDB: Is it possible to make a case-insensitive query](http://stackoverflow.com/questions/1863399/mongodb-is-it-possible-to-make-a-case-insensitive-query)。

使用各平台 SDK 的 AVQuery 对象提供的 `matchesRegex` 方法（Android SDK 用 `whereMatches` 方法）。

### 应用内用户的密码需要加密吗

不需要加密密码，我们的服务端已使用随机生成的 salt，自动对密码做了加密。 如果用户忘记了密码，可以调用 `requestResetPassword` 方法（具体查看 SDK 的 AVUser 用法），向用户注册的邮箱发送邮件，用户以此可自行重设密码。 在整个过程中，密码都不会有明文保存的问题，密码也不会在客户端保存，只是会保存 sessionToken 来标示用户的登录状态。

### 查询最多能返回多少结果？

- 查询最多返回 1000 条数据。
- 返回的查询结果不止一个时，总大小不超过 6 MB；返回一个查询结果时，大小不超过 16 MB。

### 查询结果最多只能返回 1000 条数据，当我需要的数据量超过了 1000 该怎么办？
可以通过每次变更查询条件，来继续从上一次的断点获取新的结果，譬如：
* 第一次查询，createdAt 时间在 2015-12-01 00:00:00 之后的 1000 条数据（最后一条的 createdAt 值是 x）；
* 第二次查询，createdAt 在 x 之后的 1000 条数据（最后一条的 createdAt 是 y）；
* 第三次查询，createdAt 在 y 之后的 1000 条数据（最后一条是 z）；

以此类推。

{{ data.innerQueryLimitation(heading="### 使用 CQL 子查询时查不到记录或查到的记录不全") }}

### 可以通过指定 objectId 范围来实现分页吗？

当前 LeanCloud 系统自动生成 `objectId` 的算法是基于时间的，因此确实可以通过指定 `objectId` 范围来实现分页。
但是，从代码可读性及严谨性出发（比如导入数据时可以指定不同格式的 objectId），推荐通过指定 `createdAt` 范围来实现分页。


{{ leanstorage.createIndexes() }}



### LeanCloud 查询支持 `Sum`、`Group By`、`Distinct` 这种函数吗？
LeanCloud 数据存储的查询接口不支持这些函数，可以查询到客户端后，在客户端中自己写逻辑进行这些操作。

如果要进行数据分析，可以使用我们的「[数据仓库](./datalake_guide.html)」功能。

### sessionToken 在什么情况下会失效？
如果在控制台的存储的设置中勾选了「密码修改后，强制客户端重新登录」，则用户修改密码后， sessionToken 会变更，需要重新登录。如果没有勾选这个选项，Token 就不会改变。当新建应用时，这个选项默认是被勾上的。

### 默认值的查询结果为什么不对

这是默认值的限制。MongoDB 本身是不支持默认值，我们提供的默认值只是应用层面的增强，老数据如不存在相应的 key，设置默认值后并不会订正数据，只是在查询后做了展现层的优化。相应地，变更默认值后，这些 key 也会显示为新的默认值。有两种解决方案：

1. 对老的数据做一次更新，查询出 key 不存在（whereDoesNotExist）的记录，再更新回去。
2. 查询条件加上 or 查询，or key 不存在（whereDoesNotExist）。

### 如何解决数据一致性或事务需求？

LeanCloud 目前并不提供完整的事务功能，但提供了一些保证数据一致性的特性，可以解决大部分的一致性需求：

- 在单个对象的一次 save 操作中，对多个字段的更新操作是原子地完成的。
- 使用 [increment](leanstorage_guide-js.html#更新计数器)（原子计数器）可以原子地更新数字字段。
- [唯一索引](dashboard_guide.html#给某个_Class_数据建索引) 可以保证在一个字段上有同样值的对象只有一个。
- [有条件更新对象](leanstorage_guide-js.html#有条件更新对象) 可以仅在满足某个查询条件时进行更新操作；在这个特性的基础上，你可以自己实现更加复杂的 [两阶段提交](http://www.howardliu.cn/translation-perform-two-phase-commits-in-mongodb/)。
- 在云引擎上还可以借助 [LeanCache](leancache_guide.html) 来实现自定义的 [排他锁](https://github.com/leancloud/leanengine-nodejs-demos/blob/master/functions/redlock.js)。

关于这个话题我们还录制了一期公开课视频：[在 LeanCloud 上解决数据一致性问题](https://www.bilibili.com/video/av12823801/)，其中有对上面这些特性的详细介绍，和解决常见场景的实例教程（包括实现两阶段提交）。
### 文件存储有 CDN 加速吗？

{{ data.cdn() }}

### 文件存储有大小限制吗？

没有。除了在浏览器里通过 JavaScript SDK 上传文件，或者通过我们网站直接上传文件，有 10 MB 的大小限制之外，其他 SDK 都没有限制。 JavaScript SDK 在 Node.js 环境中也没有大小限制。

### 存储图片可以做缩略图等处理吗？

可以。默认我们的 `AVFile` 类提供了缩略图获取方法，可以参见各个 SDK 的开发指南。如果要自己处理，可以通过获取 `AVFile` 的 `URL` 属性。
{% if node!='qcloud' %}
使用 [七牛图片处理 API](https://developer.qiniu.com/dora/manual/1279/basic-processing-images-imageview2) 执行处理，例如添加水印、裁剪等。
{% endif %}

### iOS 项目打包后的大小

创建一个全新的空白项目，使用 CocoaPod 安装了 AVOSCloud 和 AVOSCloudIM 模块，此时项目大小超过了 80 MB。打包之后体积会不会缩小？大概会有多大呢？

LeanCloud iOS SDK 二进制中包含了 i386、armv7、arm64 等 5 个 CPU slices。发布过程中，non-ARM 的符号和没有参与连接的符号会被 strip 掉。因此，最终应用体积不会增加超过 10 MB，请放心使用。

### 地理位置查询错误

如果错误信息类似于 `can't find any special indices: 2d (needs index), 2dsphere (needs index), for 字段名`，就代表用于查询的字段没有建立 2D 索引，可以在 Class 管理的 **其他** 菜单里找到 **索引** 管理，点击进入，找到字段名称，选择并创建「2dsphere」索引类型。

![image](images/geopoint_faq.png)

### Java SDK 对 AVObject 对象使用 getDate("createdAt") 方法读取创建时间为什么会返回 null

请用 `AVObject` 的 `getCreatedAt` 方法；获取 `updatedAt` 用 `getUpdatedAt`。
这两个方法会返回 Date 类型。
如果希望返回字符串类型，可以使用 `getUpdatedAtString()` 和 `getCreatedAtString()`。

### Android 设备每次启动时，installationId 为什么总会改变？如何才能不改变？
可能有以下两种原因导致这种情况：
* SDK 版本过旧，installationId 的生成逻辑在版本更迭中有修改。请更新至最新版本。
* 代码混淆引起的，注意在 proguard 文件中添加 [LeanCloud SDK 的混淆排除](faq.html#代码混淆怎么做)。

### Android 代码混淆怎么做
为了保证 SDK 在代码混淆后能正常运作，需要保证部分类和第三方库不被混淆，参考下列配置：

```
# proguard.cfg

-keepattributes Signature
-dontwarn com.jcraft.jzlib.**
-keep class com.jcraft.jzlib.**  { *;}

-dontwarn sun.misc.**
-keep class sun.misc.** { *;}

-dontwarn com.alibaba.fastjson.**
-keep class com.alibaba.fastjson.** { *;}

-dontwarn org.ligboy.retrofit2.**
-keep class org.ligboy.retrofit2.** { *;}

-dontwarn io.reactivex.rxjava2.**
-keep class io.reactivex.rxjava2.** { *;}

-dontwarn sun.security.**
-keep class sun.security.** { *; }

-dontwarn com.google.**
-keep class com.google.** { *;}

-dontwarn com.avos.**
-keep class com.avos.** { *;}

-dontwarn cn.leancloud.**
-keep class cn.leancloud.** { *;}

-keep public class android.net.http.SslError
-keep public class android.webkit.WebViewClient

-dontwarn android.webkit.WebView
-dontwarn android.net.http.SslError
-dontwarn android.webkit.WebViewClient

-dontwarn android.support.**

-dontwarn org.apache.**
-keep class org.apache.** { *;}

-dontwarn org.jivesoftware.smack.**
-keep class org.jivesoftware.smack.** { *;}

-dontwarn com.loopj.**
-keep class com.loopj.** { *;}

-dontwarn com.squareup.okhttp.**
-keep class com.squareup.okhttp.** { *;}
-keep interface com.squareup.okhttp.** { *; }

-dontwarn okio.**

-dontwarn org.xbill.**
-keep class org.xbill.** { *;}

-keepattributes *Annotation*

```

### Java SDK 出现 already has one request sending 错误是什么原因

日志中出现了 `com.avos.avoscloud.AVException: already has one request sending` 的错误信息，这说明存在对同一个 `AVObject` 实例对象同时进行了 2 次异步的 `save` 操作。为防止数据错乱，LeanCloud SDK 对于这种同一数据的并发写入做了限制，所以抛出了这个异常。

需要检查代码，通过打印 log 和断点的方式来定位究竟是由哪一行 `save` 所引发的。

### JavaScript SDK 有没有同步 API

JavaScript SDK 由于平台的特殊性（运行在单线程运行的浏览器或者 Node.js 环境中），不提供同步 API，所有需要网络交互的 API 都需要以 callback 的形式调用。我们提供了 [Promise 模式](leanstorage_guide-js.html#Promise) 来减少 callback 嵌套过多的问题。

### JavaScript SDK 在 AV.init 中用了 Master Key，但发出去的 AJAX 请求返回 206
目前 JavaScript SDK 在浏览器（而不是 Node）中工作时，是不会发送 Master Key 的，因为我们不鼓励在浏览器中使用 Master Key，Master Key 代表着对数据的最高权限，只应当在后端程序中使用。

如果你的应用的确是内部应用（做好了相关的安全措施，外部访问不到），可以在 `AV.init`之后增加下面的代码来让 JavaScript SDK 发送 Master Key：
```
AV.Cloud.useMasterKey(true);
```

### JavaScript SDK 会暴露 App Key 和 App Id，怎么保证安全性？
首先请阅读「[安全总览](data_security.html)」来了解 LeanCloud 完整的安全体系。其中提到，可以使用「[安全域名](data_security.html#Web_应用安全设置) 」，在没有域名的情况下，可以使用「[ACL](acl-guide.html)」。
理论上所有客户端都是不可信任的，所以需要在服务端对安全性进行设计。如果需要高级安全，可以使用 ACL 方式来管理，如果需要更高级的自定义方式，可以使用 [LeanEngine（云引擎）](leanengine_overview.html)。

## 即时通讯常见问题

### 即时通讯云端错误码说明

即时通讯的错误码会以 SDK 异常或 WebSocket 关闭状态码的形式返回给客户端。当出现异常情况时，SDK 会输出状态码到日志里，以下是对部分状态码的简单说明：

{{ im.errorCodes('table') }}

### 要让单个群组消息进入「免打扰模式」，该如何做

对于普通对话的新消息，LeanCloud 即时通讯服务有选项支持将消息以 Push Notification 的方式通知当前不在线的成员，但是有时候，这种推送会非常频繁对用户造成干扰。LeanCloud 提供选项，支持让单个用户关闭特定对话的离线消息推送。具体可以参考 [消息免打扰](realtime-guide-senior.html#消息免打扰) 文档。

### 聊天好友关系如何实现

LeanCloud 即时通讯服务是完全独立的即时通讯业务抽象，专注在即时通讯本身，所以即时通讯的业务逻辑中，并不含有好友关系，以及对应的聊天用户数据信息（如头像、名称等）。即时通讯与其他业务逻辑完全隔离，不耦合，唯一关联的就是 clientId。这样做的好处是显而易见的，比如你可以很容易让匿名用户直接通信，你也可以自定义一些好友逻辑，总之可以做成因为任意逻辑而匹配产生的聊天行为。

当然，如果你想维护一套好友关系，完全可以使用你自己的逻辑，只要存储着每个用户在即时通讯中的 clientId 即可。我们推荐使用 LeanCloud 的存储，即 LeanStorage，这样可以结合 LeanCloud 中的 User 相关对象来简单地实现账户系统，以及与之相关的存储，详情可以阅读对应的 SDK 开发指南。

### 聊天记录的保存时间和条数

{{ im.messagesLifespan("") }}

### 聊天消息没有收到

当出现聊天消息没有收到的情况，你可以按照以下思路排查：

* 调用消息记录 API 查看消息是否到达了云端
* 如果只有一个消息接收者，可以检查消息记录中对应条目的 `ack-at` 字段判断消息是否到达了客户端
* 在 **控制台 > 即时通讯 > 用户** 页面的文本框里输入对应的 Client ID，查看是否在线，以及是否有离线消息。
* 在 **控制台 > 即时通讯 > 对话** 页面的文本框里输入消息所属对话 ID，在 `消息与日志` 一栏里选日志，根据消息发送时间查看消息发送日志，看服务器是否有收到消息请求，消息是否有转发记录，转发消息时目标用户是否在线等信息。

### 为什么我收不到离线消息推送

首先请参考 [聊天消息没有收到](#聊天消息没有收到) 一节内容查看聊天消息是否有正常送达服务器。

其次请利用控制台即时消息页的用户状态查询页面来确认消息接收者是否真的处于离线状态，是否有未读消息产生，是否在 `_Installation` 表内有关联的设备，如下图所示。如果接收者处于在线状态能正常接收消息则不会有未读消息计数，也不会触发推送，请先让接收者离线后再测试离线消息推送。如果用户在 `_Installation` 表内没有关联的设备则也无法触发推送，对于 iOS 设备请确认接收者设备是否有正常从 APNs 申请到 Device Token，是否有正常存储设备记录在 `_Installation` 表中，对于 Android 设备请确认是否开启了混合推送，是否正常存储了设备记录在 `_Installation` 表中。

![image](images/realtime_faq_console.png)

接着请参考[离线推送通知](realtime-guide-intermediate.html#离线推送通知)一节内容确认您应用是否有配置默认的推送内容，或是否有通过云引擎 Hook 、消息附件方式为期望产生离线推送的消息动态设置了离线消息推送内容。没有设置离线消息推送内容也无法触发离线消息推送。

之后请在 **控制台 > 推送 > 在线发送** 页面尝试给接收者用户 Client ID 在 `_Installation` 表关联的设备单独发推送，查看推送是否能收到。可以通过推送记录查看是否有错误产生。如看到 `Invalid Token` 计数非 0 表示目标 iOS 设备的 Device Token 过期或 Device Token 和推送使用的证书不匹配或目标 Device Token 和推送使用的环境不匹配。请尝试切换推送证书，确认目标 Device Token 是 Production 环境还是 Development 环境后再重新推送，不匹配的证书或不匹配的推送环境均会导致推送失败。如何切换离线推送通知的证书请参考 [离线推送通知](realtime-guide-intermediate.html#离线推送通知)

检查方法总结如下：

* 检查消息是否正常发送到服务器
* 检查接收者用户是否离线，是否在 `_Installation` 表中有关联的设备记录
* 检查是否有设置推送内容
* 使用控制台推送在线发送工具实际发推送给目标设备查看推送是否出错，比如 iOS 证书不匹配，设备 Token 过期，设备 Token 和推送环境不匹配等

### Android 设备系统时间不准，会影响即时通讯服务吗？

可能会，取决于证书的时间。当错误的系统时间和当前时间的误差，大于证书的有效时间，就会导致 SSL 握手失败，进而让即时通讯服务整体不可用。

### 我只想实现两个用户的私聊，是不是每次都得重复创建对话？

不需要重复创建。我们推荐的方式是开发者可以用**自定义属性**来实现对私聊和群聊的标识，并且在进行私聊之前，需要查询当前两个参与对话的 ClientId 是否之前已经存在一个私聊的对话了。{% block goto_create_unique_conversation %}另外，SDK 已经提供了创建唯一对话的接口，请查看 [创建对话](#创建对话)。{% endblock %}


### 某个成员退出对话之后，再加入，在他离开的这段期间内的产生的聊天记录，他还能获取么？

可以。目前聊天记录从属关系是属于对话的，也就是说，只要对话 Id 不变，不论人员如何变动，只要这个对话产生的聊天记录，当前成员都可以获取。

### 我自己没有云端，如何实现签名的功能？

LeanCloud 云引擎提供了托管 Python 和 Node.js 运行的方式，开发者可以用这两种语言按照签名的算法实现签名，完全可以支持开发者的自定义权限控制。

## 推送通知常见问题
### 为什么成功设备数小于目标设备数？

「目标设备数」是指符合本次推送查询条件的有效设备数量，「成功设备数」是指这次推送成功到达的设备数量。没有到达设备一般有以下几种情况：

- Android 设备中的应用被杀掉，在网络中处于离线状态。对于这种情况，稍等一段时间待设备上线后，成功设备数就会逐渐增加。同时推荐集成 [混合推送](android_push_guide.html#混合推送) 来提升推送到达率。
- Android 用户删掉或重装应用生成了新的设备数据，之前的无效数据被包含到了「目标设备数」中。
- iOS 用户删掉或重装了应用，之前的无效数据被包含到了「目标设备数」中，这些数量就是 invalidTokens 数量。

### 为什么只能给三个月内活跃过的设备发消息？能否发全量设备？

我们只允许开发者给 `_Installation` 表中 **updatedAt** 值为**最近三个月以内**的设备推送消息。原因如下：

- 三个月没有活跃过的设备，用户打开推送的概率已经非常小了，我们将这些不活跃的设备视为无效设备。
- 成本原因。全量推送需要处理的无效设备数量会成倍增长，因而会消耗大量云端资源，其中一个能被用户感知到的影响就是推送时间，目标设备数成倍增长也意味着单次全量推送的时间也成倍增长。

基于以上考虑，我们默认只能向三个月以内活跃过的设备发消息。如果您确实需要给全量设备发消息，可以联系 {{ include.supportEmail() }} 使用我们的企业版服务，我们将为您的应用创建独立集群来提供推送服务。

### 为什么推送记录的目标设备数为 0？

在控制台的推送记录中，「目标设备数」是指符合本次推送查询条件的有效设备数量。该值为 0 时请检查<u>推送查询条件及目标设备是否有效</u>。另外请参考 [目标设备数的限制](#限制)。

### 为什么推送记录的内容为空？

在控制台的推送记录中，当针对不同目标设备类型设置了不同消息内容时，「内容」一栏会显示为空，需要点击消息的「ID」打开推送详情来查看具体的内容。

### 推送的到达率如何

关于到达率这个概念，业界并没有统一的标准。我们测试过，在线用户消息的到达率基本达到 100%。我们的 SDK 做了心跳和重连等功能，尽量维持对推送服务器的长连接存活，提升消息到达用户手机的实时性和可靠性。

### 使用 iOS 的 Token Authentication 证书推送是否区分测试环境和生产环境？
使用 Token Authentication 也是区分生产和测试环境的，是使用 prod 参数用来区分。同一个 key 可以给测试环境发消息，也能给正式环境发消息。

但是同一个设备的 deviceToken 只能成功发送一个环境。要么是正式环境，要么是测试环境。现象是给一个 deviceToken 推送，如果 dev 成功了，prod 就会报 invalid Tokens，不会在两个环境同时发送成功。

### 有一些 iOS 设备收不到推送，到控制台查看推送记录，发现 invalidTokens 的数量大于 0，是怎么回事？

invalidTokens 的数量由以下两部分组成：
* 选择的设备与选择的证书不匹配时，会增加 invalidTokens 的数量，例如使用开发证书给生产证书的设备推送。
* 目标设备移除或重装了对应的 App。

针对第一种情况，请检查 APNS 证书是否过期，并检查是否使用了正确的证书类型。

### Android 消息接收能不能自定义 Receiver 不弹出通知

可以。请参考《Android 推送开发指南》。

如果要自定义 receiver，必须在消息的 data 里带上自定义的 action。LeanCloud 在接收到消息后，将广播 action 为您定义的值的 intent 事件，您的 receiver 里也必须带上 `intent-filter` 来捕获该 action 值的 intent 事件。

### Android 应用进程被杀掉后无法收到推送消息
iOS 能做到这点，是因为当应用进程关闭后，Apple 和设备的系统之间还会存在连接，这条连接跟应用无关，所以无论应用是否被杀掉，消息都能发送到 APNs 再通过这条连接发送到设备。但对 Android 来说，如果是国内用户，因为众所周知的原因，Google 和设备之间的这条连接是无法使用的，所以应用只能自己去保持连接并在后台持续运行，一旦后台进程被杀掉，就无法收到推送消息了。虽然 LeanCloud SDK 已经采取了各种办法保持应用在后台运行，但随着 Android 系统版本的升级，权限控制越来越严，第三方推送通道的生命周期受到较大限制。因此 LeanCloud SDK 推出了混合推送的方案，对接国内主流厂商，保障了主流 Android 系统上的推送到达率。详见 [Android 混合推送开发指南](android_mixpush_guide.html)。

{% if node != 'qcloud' and node != 'us' %}
## 短信常见问题
请参见 [短信收发常见问题一览](sms-faq.html)。
{% endif %}
