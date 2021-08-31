# 排行榜开发指南 &middot; Objective-C

## 安装 SDK

排行榜是存储 SDK 中的一个模块，要在 Objective-C 运行环境中使用排行榜功能，需要安装存储 SDK，请参考《[ Objective C SDK 配置指南](sdk_setup-objc.html)》。

### Leaderboard

`LCLeaderboard` 类是对排行榜的抽象。`LCLeaderboard` 实例有以下属性：

|属性|类型|说明|
|:--:|:--:|--|
|`statisticName`|`NSString`|所排名的成绩名字|

### Statistic

排行榜是对用户的成绩进行排名的结果。SDK 提供了一个 `LCLeaderboardStatistic` 类来表示成绩。`LCLeaderboardStatistic` 实例有以下属性：

|属性|类型|说明|
|:--:|:--:|--|
|`name`|`NSString`|成绩名字，对应排行榜的 `statisticName`|
|`version`|`NSInteger`|版本|
|`value`|`double`|成绩值|
|`user`|`LCUser`|如果该成绩属于一个 `user`，则可通过此属性获取该 `LCUser`|
|`object`|`LCObject`|如果该成绩属于一个 `object`，则可通过此属性获取该 `LCObject`|
|`entity`|`NSString`|如果该成绩属于一个 `entity`，则可通过此属性获取该 `entity`|

### Ranking

|属性|类型|说明|
|:--:|:--:|--|
|`statisticName`|`NSString`|成绩名字，对应排行榜的 `statisticName`|
|`rank`|`NSInteger`|排名，从 0 开始|
|`value`|`double`|成绩值|
|`includedStatistics`|`NSArray<LCLeaderboardStatistic *>`|该 `user`/`object`/`entity` 的其他成绩|
|`user`|`LCUser`|如果该排名属于一个 `user`，则可通过此属性获取该 `LCUser`|
|`object`|`LCObject`|如果该排名属于一个 `object`，则可通过此属性获取该 `LCObject`|
|`entity`|`NSString`|如果该排名属于一个 `entity`，则可通过此属性获取该 `entity`|

## 成绩管理

可以通过 SDK 管理 `user`/`object`/`entity` 的成绩。

### 用户成绩管理

可以通过 SDK 更新、查询、删除 `LCUser` 的成绩。

#### 用户成绩更新

当 `user` 完成了一局游戏后，你可以更新该 `user` 的成绩：

```objc
[LCLeaderboard updateCurrentUserStatistics:@{
    @"score" : @3458,
    @"kills" : @28,
} callback:^(NSArray<LCLeaderboardStatistic *> * _Nullable statistics, NSError * _Nullable error) {
    if (statistics) {
        // statistics 是更新后你的最好/最新成绩
    } else if (error) {
        // 处理错误
    }
}];
```

`+[LCLeaderboard updateCurrentUserStatistics:callback:]` 方法的第一个参数是一个 `NSDictionary` 的对象，key 为要更新的 `statisticName`，value 为要更新的成绩。你可以一次更新多个不同的成绩。

**更新成绩需要用户登录，且用户只能更新自己的成绩。**

#### 用户成绩查询

你可以查询某 `user` 的在某些排行榜成绩：

```objc
[LCLeaderboard getStatisticsWithUserId:user.objectId
                        statisticNames:@[@"score", @"kills"]
                              callback:^(NSArray<LCLeaderboardStatistic *> * _Nullable statistics, NSError * _Nullable error) {
    if (statistics) {
        // statistics 是查询的成绩结果
    } else if (error) {
        // 处理错误
    }
}];
```

你也可以省略 `statisticNames` 选项用来查询某 `user` 的所有成绩：

```objc
[LCLeaderboard getStatisticsWithUserId:user.objectId
                        statisticNames:nil
                              callback:^(NSArray<LCLeaderboardStatistic *> * _Nullable statistics, NSError * _Nullable error) {
    if (statistics) {
        // statistics 是查询的成绩结果
    } else if (error) {
        // 处理错误
    }
}];
```

你还可以查询某个排行榜的一批 `user` 的成绩：

```objc
LCLeaderboard *leaderboard = [[LCLeaderboard alloc] initWithStatisticName:@"score"];
[leaderboard getStatisticsWithUserIds:@[user.objectId]
                             callback:^(NSArray<LCLeaderboardStatistic *> * _Nullable statistics, NSError * _Nullable error) {
    if (statistics) {
        // statistics 是查询的成绩结果
    } else if (error) {
        // 处理错误
    }
}];
```

#### 用户成绩删除

你可以删除当前登录用户在某些排行榜的成绩：

```objc
[LCLeaderboard deleteCurrentUserStatistics:@[@"score", @"kills"] callback:^(BOOL succeeded, NSError * _Nullable error) {
    if (succeeded) {
        // 删除成功
    } else if (error) {
        // 处理错误
    }
}];
```

### Object 成绩管理

可以通过 SDK 查询 `LCObject` 的成绩。

#### 用户成绩查询

你可以查询某 `object` 的在某些排行榜成绩：

```objc
[LCLeaderboard getStatisticsWithObjectId:object.objectId
                          statisticNames:@[@"score", @"kills"]
                                  option:nil
                                callback:^(NSArray<LCLeaderboardStatistic *> * _Nullable statistics, NSError * _Nullable error) {
    if (statistics) {
        // statistics 是查询的成绩结果
    } else if (error) {
        // 处理错误
    }
}];
```

你也可以省略 `statisticNames` 选项用来查询某 `object` 的所有成绩：

```objc
[LCLeaderboard getStatisticsWithObjectId:object.objectId
                          statisticNames:nil
                                  option:nil
                                callback:^(NSArray<LCLeaderboardStatistic *> * _Nullable statistics, NSError * _Nullable error) {
    if (statistics) {
        // statistics 是查询的成绩结果
    } else if (error) {
        // 处理错误
    }
}];
```

你还可以查询某个排行榜的一批 `object` 的成绩：

```objc
LCLeaderboard *leaderboard = [[LCLeaderboard alloc] initWithStatisticName:@"score"];
[leaderboard getStatisticsWithObjectIds:@[object.objectId]
                                 option:nil
                               callback:^(NSArray<LCLeaderboardStatistic *> * _Nullable statistics, NSError * _Nullable error) {
    if (statistics) {
        // statistics 是查询的成绩结果
    } else if (error) {
        // 处理错误
    }
}];
```

### Entity 成绩管理

可以通过 SDK 查询 `entity` 的成绩。

#### 用户成绩查询

你可以查询某 `entity` 的在某些排行榜成绩：

```objc
[LCLeaderboard getStatisticsWithEntity:entity
                        statisticNames:@[@"score", @"kills"]
                              callback:^(NSArray<LCLeaderboardStatistic *> * _Nullable statistics, NSError * _Nullable error) {
    if (statistics) {
        // statistics 是查询的成绩结果
    } else if (error) {
        // 处理错误
    }
}];
```

你也可以省略 `statisticNames` 选项用来查询某 `entity` 的所有成绩：

```objc
[LCLeaderboard getStatisticsWithEntity:entity
                        statisticNames:nil
                              callback:^(NSArray<LCLeaderboardStatistic *> * _Nullable statistics, NSError * _Nullable error) {
    if (statistics) {
        // statistics 是查询的成绩结果
    } else if (error) {
        // 处理错误
    }
}];
```

你还可以查询某个排行榜的一批 `entity` 的成绩：

```objc
LCLeaderboard *leaderboard = [[LCLeaderboard alloc] initWithStatisticName:@"score"];
[leaderboard getStatisticsWithEntities:@[entity] callback:^(NSArray<LCLeaderboardStatistic *> * _Nullable statistics, NSError * _Nullable error) {
    if (statistics) {
        // statistics 是查询的成绩结果
    } else if (error) {
        // 处理错误
    }
}];
```

## 获取排行榜结果

可以通过 SDK 获取 `user`/`object`/`entity` 的排行榜结果。

### 获取用户排行榜结果

通过 SDK 获取 `LCUser` 的排行榜结果。

#### 获取指定区间的用户排名

获取排行榜结果最常见的使用场景是获取排名前 N 的用户成绩：

```objc
LCLeaderboard *leaderboard = [[LCLeaderboard alloc] initWithStatisticName:@"world"];
leaderboard.limit = 10;
leaderboard.skip = 0;
[leaderboard getUserResultsWithOption:nil callback:^(NSArray<LCLeaderboardRanking *> * _Nullable rankings, NSInteger count, NSError * _Nullable error) {
    if (error) {
        // 处理错误
    } else {
        // 处理 rankings 和 count
    }
}];
```

#### 获取指定用户附近的排名

### 获取 Object 排行榜结果

通过 SDK 获取 `LCObject` 的排行榜结果。

#### 获取指定区间的 Object 排名

#### 获取指定 Object 附近的排名

### 获取 Entity 排行榜结果

通过 SDK 获取 `entity` 的排行榜结果。

#### 获取指定区间的 Entity 排名

#### 获取指定 Entity 附近的排名