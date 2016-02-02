<properties
    pageTitle="開始使用適用於 iOS (Swift) 的 Azure Mobile Engagement"
    description="了解如何使用 iOS 應用程式的 Azure Mobile Engagement 與分析和推播通知。"
    services="mobile-engagement"
    documentationCenter="Mobile"
    authors="piyushjo"
    manager="dwrede"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-ios"
    ms.devlang="swift"
    ms.topic="get-started-article"
    ms.date="09/22/2015"
    ms.author="piyushjo" />


# 開始使用適用於 iOS 應用程式 (Swift) 的 Azure Mobile Engagement

> [AZURE.SELECTOR]
- [Universal Windows](mobile-engagement-windows-store-dotnet-get-started.md)
- [Windows Phone Silverlight](mobile-engagement-windows-phone-get-started.md)
- [iOS | Obj C](mobile-engagement-ios-get-started.md)
- [iOS | Swift](mobile-engagement-ios-swift-get-started.md)
- [Android](mobile-engagement-android-get-started.md)
- [Cordova](mobile-engagement-cordova-get-started.md)


本主題說明如何使用 Azure Mobile Engagement 來了解您的應用程式使用量，並傳送推播通知給 iOS 應用程式的區隔使用者。
在本教學課程中，您將使用 Apple 推播通知服務 (APNS)，建立可收集基本資料，並接收推播通知的空白 iOS 應用程式。

本教學課程需要下列各項：

+ Xcode 6 或 XCode 7，可以從您的 MAC App Store 安裝
+ [Mobile Engagement iOS SDK]
+ 推播通知憑證 (.p12)，您可以在 Apple Dev Center 取得

> [AZURE.NOTE] 本教學課程使用 Swift 2.0 版。 

完成本教學課程是所有其他 iOS 應用程式 Mobile Engagement 教學課程的先決條件。
> [AZURE.IMPORTANT] 完成本教學課程是所有其他 iOS 應用程式 Mobile Engagement 教學課程的先決條件，若要完成此課程，您必須擁有有效的 Azure 帳戶。 如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。 如需詳細資訊，請參閱 <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fen-us%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Azure 免費試用</a>。

## <a id="setup-azme"></a>IOS 應用程式設定 Mobile Engagement

[AZURE.INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal.md)]

## <a id="connecting-app"></a>應用程式連接到 Mobile Engagement 後端

本教學課程將說明「基本整合」，這是收集資料及傳送推播通知時必要的最低設定。 完整的整合文件，請參閱 [Mobile Engagement iOS SDK 整合](../mobile-engagement-ios-sdk-overview/)

我們將會使用 XCode 建立基本應用程式來示範整合：

### 建立新的 iOS 專案

[AZURE.INCLUDE [Create a new iOS Project](../../includes/mobile-engagement-create-new-ios-app.md)]

### 將您的應用程式連線至 Mobile Engagement 後端

1. 下載 [Mobile Engagement iOS SDK]
2. 將 .tar.gz 檔案解壓縮到電腦中的資料夾
3. 以滑鼠右鍵按一下專案，然後選取 [Add files to ...]

    ![][1]

4. 瀏覽至您解壓縮 SDK 和選取的資料夾 `EngagementSDK` 資料夾，然後按 [確定]。

    ![][2]

5. 開啟 `建置階段` ] 索引標籤和 `Link Binary With Libraries` 功能表中新增框架，如下所示:

    ![][3]

6. 如 **XCode 7** -新增 `libxml2.tbd` 而不是 `libxml2.dylib`。

7. 選擇 [File] > [New] > [File] > [iOS] > [Source] > [Header File] 來建立「橋接」標頭，以使用 SDK 的 Objective C API。

    ![][4]

8. 編輯橋接標頭檔案來將 Mobile Engagement Objective-C 程式碼公開至 Swift 程式碼，並新增以下匯入：

        /* Mobile Engagement Agent */
        #import "AEModule.h"
        #import "AEPushMessage.h"
        #import "AEStorage.h"
        #import "EngagementAgent.h"
        #import "EngagementTableViewController.h"
        #import "EngagementViewController.h"
        #import "AEIdfaProvider.h"

9. 在 [Build Settings]，請確定在 [Swift Compiler - Code Generation] 下的 [Objective-C Bridging Header] 組件設定有指向此標頭的路徑。 路徑的範例：**$(SRCROOT)/MySuperApp/MySuperApp-Bridging-Header.h (依路徑而定)**

    ![][6]

10. 回到 Azure 入口網站中您應用程式的 [連線資訊]** 頁面，並複製 [連線字串]。

    ![][5]

11. 現在貼上連接字串中的 `didFinishLaunchingWithOptions` 委派

        func application(application: UIApplication, didFinishLaunchingWithOptions launchOptions: [NSObject: AnyObject]?) -> Bool
        {
            [...]
                EngagementAgent.init("Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}")
            [...]
        }


## <a id="monitor"></a>啟用即時監視

若要開始傳送資料並確定使用者正在使用，您必須至少傳送一個畫面 (活動) 到 Mobile Engagement 後端。

1. 開啟 **ViewController.swift** 檔案，取代 **ViewController** 的基底類別為 **EngagementViewController**：

    `類別 ViewController: EngagementViewController {`

## <a id="monitor"></a>應用程式與即時監視連線

[AZURE.INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

## <a id="integrate-push"></a>啟用推播通知與應用程式內傳訊

Mobile Engagement 可讓您透過「推播通知」和「應用程式內傳訊」，於活動進行時與使用者互動和觸達 (REACH)。 此模組在 Mobile Engagement 入口網站中稱為觸達 (REACH)。
以下各節將設定您的用程式來接收它們。

### 啟用應用程式接收無聲推播通知

[AZURE.INCLUDE [mobile-engagement-ios-silent-push](../../includes/mobile-engagement-ios-silent-push.md)]

### 將觸達程式庫加入至專案

1. 以滑鼠右鍵按一下您的專案
2. 選取 `將檔案新增到...`
3. 瀏覽至您解壓縮 SDK 所在的資料夾
4. 選取 `EngagementReach` 資料夾
5. 按一下 [新增]
6. 編輯橋接標頭檔案來將 Mobile Engagement Objective-C 觸達標頭公開，並新增以下匯入：

        /* Mobile Engagement Reach */
        #import "AEAnnouncementViewController.h"
        #import "AEAutorotateView.h"
        #import "AEContentViewController.h"
        #import "AEDefaultAnnouncementViewController.h"
        #import "AEDefaultNotifier.h"
        #import "AEDefaultPollViewController.h"
        #import "AEInteractiveContent.h"
        #import "AENotificationView.h"
        #import "AENotifier.h"
        #import "AEPollViewController.h"
        #import "AEReachAbstractAnnouncement.h"
        #import "AEReachAnnouncement.h"
        #import "AEReachContent.h"
        #import "AEReachDataPush.h"
        #import "AEReachDataPushDelegate.h"
        #import "AEReachModule.h"
        #import "AEReachNotifAnnouncement.h"
        #import "AEReachPoll.h"
        #import "AEReachPollQuestion.h"
        #import "AEViewControllerUtil.h"
        #import "AEWebAnnouncementJsBridge.h"


### 修改您的應用程式代理人

1. 內部 `didFinishLaunchingWithOptions` -建立觸達模組並將它傳遞到您現有的 Engagement 初始化行:

        func application(application: UIApplication, didFinishLaunchingWithOptions launchOptions: [NSObject: AnyObject]?) -> Bool {
            let reach = AEReachModule.moduleWithNotificationIcon(UIImage(named:"icon.png")) as! AEReachModule
            EngagementAgent.init("Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}", modulesArray:[reach])
            [...]
            return true
        }


### 讓您的應用程式能接收 APNS 推播通知

1. 將下列行加入 `didFinishLaunchingWithOptions` 方法:

        /* Ask user to receive push notifications */
        if #available(iOS 8.0, *)
        {
           let settings = UIUserNotificationSettings(forTypes: [UIUserNotificationType.Alert, UIUserNotificationType.Badge, UIUserNotificationType.Sound], categories: nil)
           application.registerUserNotificationSettings(settings)
           application.registerForRemoteNotifications()
        }
        else
        {
           application.registerForRemoteNotificationTypes([UIRemoteNotificationType.Alert, UIRemoteNotificationType.Badge, UIRemoteNotificationType.Sound])
        }

2. 新增 `didRegisterForRemoteNotificationsWithDeviceToken` 方法，如下所示:

        func application(application: UIApplication, didRegisterForRemoteNotificationsWithDeviceToken deviceToken: NSData)
        {
            EngagementAgent.shared().registerDeviceToken(deviceToken)
        }

3. 新增 `didReceiveRemoteNotification:fetchCompletionHandler:` 方法，如下所示:

        func application(application: UIApplication, didReceiveRemoteNotification userInfo: [NSObject : AnyObject], fetchCompletionHandler completionHandler: (UIBackgroundFetchResult) -> Void)
        {
            EngagementAgent.shared().applicationDidReceiveRemoteNotification(userInfo, fetchCompletionHandler:completionHandler)
        }


[AZURE.INCLUDE [mobile-engagement-ios-send-push-push](../../includes/mobile-engagement-ios-send-push.md)]




[mobile engagement ios sdk]: http://aka.ms/qk2rnj 
[1]: ./media/mobile-engagement-ios-get-started/xcode-add-files.png 
[2]: ./media/mobile-engagement-ios-get-started/xcode-select-engagement-sdk.png 
[3]: ./media/mobile-engagement-ios-get-started/xcode-build-phases.png 
[4]: ./media/mobile-engagement-ios-swift-get-started/add-header-file.png 
[5]: ./media/mobile-engagement-ios-get-started/app-connection-info-page.png 
[6]: ./media/mobile-engagement-ios-swift-get-started/add-bridging-header.png 

