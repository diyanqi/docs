{% import "views/_helper.njk" as docs %}
{% set backtick = "注意：别名或以下划线开头的表名都需要放在反引号内，如 <code>as &#96;total&#96;</code>、<code>&#96;_User&#96;.name</code>。" %}

数据仓库 (Beta) 使用指南
====

基于现代的数据库 ClickHouse ，我们构建了新一代的数据仓库。与云服务紧密集成，支持将存储数据，业务日志自动入库，从而为业务运营的统计分析提供强大的数据支持。

新的数据仓库将提供以下独特的功能，

1. 基于列的存储，支持高比率的压缩，可以大幅度缩减存储成本；
1. 基于列的遍历和向量的运算支持高效的查询效率，可以将原本需要分钟级别的复杂查询，缩短到秒级别；
1. 直观的数据视图功能，支持存储中间结果，配合 join 查询，可支持高效率且复杂的二阶查询；
1. 更多样的函数，支持更复杂的 SQL 查询，比如 visitParamExtract 提取 JSON 字段；
1. 无缝对接日志表，可支持多样的数据源实时入库和查询，从而更灵活地集成外部数据源，为业务提供更完备的数据分析能力。

数据入库
----

在对数据进行查询前，我们首先需要将数据入库。目前，我们主要支持数据存储 class 的入库。数据存储 class 又具体分为两类，一类是日志表，另一类则是普通 class 。

### 日志表

日志表是数据存储中的一类特殊表，是为了满足业务存储事件，日志等「不变型」数据的需求而设计，这类数据的特点是写入之后不会再修改。这些数据在被收集起来之后，可以应用于业务审计与运营分析，开发者对事件进行追踪等场景。由于该类数据只有追加，没有修改，我们能够提供更大的并发写入吞吐。

#### 开启和接入日志表

在控制台「数据存储」页面，点击创建 Class ，勾选「日志表」选项即可创建日志表。比如我们可以创建一个名为 `EventLog` 的日志表。

在 SDK 层面，日志表对象的创建与普通对象的创建一致，

```
// for Android
AVObject event = new AVObject("EventLog");
event.put("eventType", "buttonClick");
event.put("eventName", "orderSubmit");
event.put("eventDate", new Date());
event.saveInBackground();
```

日志提交后会直接入库数据仓库，并且实时可查。


### 普通 Class 的同步

普通 Class 对象，由于支持更新，同步到数据仓库的流程要稍微复杂一些。

在控制台「数据存储」-「数据仓库」中，点击创建 Class 同步，选择需要在数据仓库中查询分析的字段，即可开启 Class 到数据仓库的同步。存量数据会即可开始同步，取决于数据量的大小，同步可能持续较长时间，请耐心等待。

开启同步的 Class ，我们会在次日凌晨同步前一日更新过的数据。因此更新的对象需要在次日才可见。我们还会持续优化该流程，以期实现更实时的同步。

查询语法
---

离线分析仅支持 `select` 查询。语法结构大致如下，

```sql
SELECT column1, column2, expr ...
FROM table | (subquery)
[INNER|LEFT|RIGHT|FULL|CROSS] JOIN (subquery)|table (ON <expr_list>)
[WHERE expr]
[GROUP BY expr_list]
[HAVING expr]
[ORDER BY expr_list]
[LIMIT [skip, ]n]
[UNION  ...]
```

注意字符串，日期等值必需用**单引号**引用；而对于字段或表名中有特殊字符的情况，可使用反引号进行引用。比如

```sql
WHERE order_status = 'delivered' 
  AND `from` = 'example.com'
```

更多查询语法请参考 [ClickHouse 文档指南](https://clickhouse.com/docs/en/sql-reference/statements/select/)。

常见用例推荐
---

#### 使用 visitParamExtract* 提取 json 字段

在数据同步的过程中，多层嵌套的 object 对象会以 json 字符串的形式入库。为了提取 json 字符串中的字段，推荐使用 `visitParamExtract*` 来提取字段，比如

```sql
SELECT visitParamExtractString('{"abc":"\\u263a"}', 'abc') = '☺';
```

在嵌套复杂，且出现有重名字段时，可使用 `JSONExtract*`，比如

```sql
SELECT JSONExtractFloat('{"a": "hello", "b": [-100, 200.0, 300]}', 'b', 2) = 200.0
```

更多的 JSON 提取函数可参考[官方文档](https://clickhouse.com/docs/en/sql-reference/functions/json-functions/#visitparamextractuintparams-name)。

#### 使用 toTimeZone 进行时区转换

日期的展示默认以服务端时区展示，如果不符合预期，可以使用 `toTimeZone` 将日期转换到特定的时区展示。 

```sql
toTimeZone(createdAt, 'Asia/Shanghai')
```

#### 使用 toYYYYMMDD 进行按日期统计

在按照日期进行统计分析时，可以使用 `toYYYYMMDD` 将日期字符串化后进行 group by 统计。同时需要注意时区的处理，比如，

```sql
GROUP BY toYYYYMMDD(toTimeZone(createdAt, 'Asia/Shanghai'))
```

#### 使用 argMax 提取「最新版本」数据

在遇到重复数据时，可以使用 argMax 来提取最后一条数据作为「最新版本」的数据。比如我们可以提取某一个订单的最新状态，

```sql
SELECT
  orderId,
  argMax(status, updatedAt) AS status
FROM my_class
GROUP BY orderId
```


其它限制
---

* 基于安全原因，`_User` 表的 `sessionToken`、`password`、`salt`、`authData` 字段，以及所有表的 `ACL` 字段都不支持同步。
* 基于性能考量，`_Conversation` 表的 `m` 和 `mu` 这样的大数组字段暂不支持同步。
* 为了更好的隔离应用之间的影响，我们会对来自同一个应用的慢查询进行配额限制。比如，我们仅允许最多不超过 3 个慢查询同时进行。

REST API
----

Stay tuned ...

计费
----

离线分析仅对商用版应用开放，且目前处于 Beta 预览阶段，暂不收费。进入正式阶段，我们将对「存储空间」和「计算资源」两个维度进行计费。

