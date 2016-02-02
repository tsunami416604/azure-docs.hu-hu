<properties
    pageTitle="Azure Mobile Engagement iOS SDK 升級程序"
    description="Azure Mobile Engagement iOS SDK 的最新更新與程序"
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="MehrdadMzfr"
    manager="dwrede"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-ios"
    ms.devlang="objective-c"
    ms.topic="article"
    ms.date="08/05/2015"
    ms.author="MehrdadMzfr" />


# 升級程序

如果您已經整合舊版 Engagement 到您的應用程式，在升級 SDK 時您必須考慮以下幾點。

針對每個新版 SDK，您必須先取代 (在 xcode 中移除並重新匯入) EngagementSDK 與 EngagementReach 資料夾。

## 從 2.0.0 到 3.0.0

停止支援 iOS 4.X。 從此版本開始，您的應用程式部署目標必須至少為 iOS 6。

如果您在應用程式中使用範圍，您必須新增 `遠端通知` 值 `UIBackgroundModes` 以接收遠端通知 Info.plist 檔案中的陣列。

此方法 `應用程式: didReceiveRemoteNotification:` 需要更換的 `應用程式: didReceiveRemoteNotification:fetchCompletionHandler:` 應用程式委派中。

"AEPushDelegate.h" 是已被取代的介面，且您必須移除所有參考。 這包括在移除 `[[共用的 EngagementAgent] setPushDelegate:self]` 和委派方法，從您的應用程式委派:

    -(void)willRetrieveLaunchMessage;
    -(void)didFailToRetrieveLaunchMessage;
    -(void)didReceiveLaunchMessage:(AEPushMessage*)launchMessage;

## 從 1.16.0 到 2.0.0

以下說明如何將 SDK 整合從 Capptain SAS 提供的 Capptain 服務，移轉到由 Azure Mobile Engagement 提供的應用程式內。
如果您是從較早版本移轉，請參閱 Capptain 網站，先移轉到 1.16 後再套用以下程序。

>[Azure.IMPORTANT] Capptain 和 Mobile Engagement 是不同的服務，而以下程序只用於移轉用戶端應用程式。 移轉應用程式中的 SDK「不會」將您的資料從 Capptain 伺服器移轉到 Mobile Engagement 伺服器

### 代理程式

此方法 `registerApp:` 已被新方法取代 `init:`。 您的應用程式委派必須隨之更新，並使用連接字串：

            - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
            {
              [...]
              [EngagementAgent init:@"YOUR_CONNECTION_STRING"];
              [...]
            }

您只需要移除的所有執行個體的 SDK 中已移除 SmartAd 追蹤 `AETrackModule` 類別

### 類別名稱變更

執行品牌再造時，有幾個類別/檔案名稱需要變更。

所有首碼為 "CP" 的類別，都使用 "AE" 首碼重新命名。

範例：

-   `CPModule.h` 已重新命名為 `AEModule.h`。

所有首碼為 "Capptain" 的類別，都已使用 "Engagement" 首碼重新命名。

範例：

-   類別 `CapptainAgent` 已重新命名為 `EngagementAgent`。
-   類別 `CapptainTableViewController` 已重新命名為 `EngagementTableViewController`。
-   類別 `CapptainUtils` 已重新命名為 `EngagementUtils`。
-   類別 `CapptainViewController` 已重新命名為 `EngagementViewController`。





