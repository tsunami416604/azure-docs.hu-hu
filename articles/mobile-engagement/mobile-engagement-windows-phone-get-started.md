<properties
    pageTitle="開始使用適用於 Windows Phone Silverlight 應用程式的 Azure Mobile Engagement"
    description="了解如何使用適用於 Windows Phone Silverlight 應用程式的 Azure Mobile Engagement 執行分析和傳送推播通知。"
    services="mobile-engagement"
    documentationCenter="windows"
    authors="piyushjo"
    manager="dwrede"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-windows-phone"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="09/22/2015"
    ms.author="piyushjo" />


# 開始使用適用於 Windows Phone Silverlight 應用程式的 Azure Mobile Engagement

> [AZURE.SELECTOR]
- [Windows Universal](mobile-engagement-windows-store-dotnet-get-started.md)
- [Windows Phone Silverlight](mobile-engagement-windows-phone-get-started.md)
- [iOS | Obj C](mobile-engagement-ios-get-started.md)
- [iOS | Swift](mobile-engagement-ios-swift-get-started.md)
- [Android](mobile-engagement-android-get-started.md)
- [Cordova](mobile-engagement-cordova-get-started.md)


本主題說明如何使用 Azure Mobile Engagement 了解您的應用程式使用狀況，以及傳送推播通知給 Windows Phone Silverlight 應用程式的分佈使用者。
本教學課程將示範使用 Mobile Engagement 的簡單廣播案例。 在課程中，您將建立一個空白的 Windows Phone Silverlight 應用程式，以使用 Microsoft 推播通知服務 (MPNS) 來收集基本資料及接收推播通知。
> [AZURE.NOTE] 如果您的目標 Windows Phone 8.1 (非 Silverlight)，請參閱 [Windows 通用教學課程](mobile-engagement-windows-store-dotnet-get-started.md)。

本教學課程需要下列各項：

+ Visual Studio 2013
+ [MicrosoftAzure.MobileEngagement] Nuget 封裝

> [AZURE.IMPORTANT] 完成本教學課程是與 Windows Phone Silverlight 應用程式有關之所有其他 Mobile Engagement 教學課程的先決條件，而且若要完成本教學課程，您必須擁有有效的 Azure 帳戶。 如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。 如需詳細資訊，請參閱 <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fen-us%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Azure 免費試用</a>。

## <a id="setup-azme"></a>設定 Mobile Engagement windows Phone 應用程式

[AZURE.INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal.md)]

## <a id="connecting-app"></a>應用程式連接到 Mobile Engagement 後端

本教學課程將說明「基本整合」，這是收集資料及傳送推播通知時必要的最低設定。 完整的整合文件，請參閱 [Mobile Engagement Windows Phone SDK 整合](../mobile-engagement-windows-phone-sdk-overview/)

我們將會使用 Visual Studio 建立基本應用程式來示範整合。

### 建立新的 Windows Phone Silverlight 專案

即使下列步驟與舊版 Visual Studio 中的步驟類似，但這些步驟假設使用的是 Visual Studio 2015。

1. 啟動 Visual Studio，並在 [首頁]**** 畫面上選取 [新增專案]****。

2. 在快顯視窗中，依序選取 [Windows 8]**** -> [Windows Phone]**** -> [空白應用程式 (Windows Phone Silverlight)]****。 填入 App 的**名稱**和**方案名稱**，然後按一下 [確定]****。

    ![][1]

3. 您可以選擇要將目標設為 **Windows Phone 8.0** 或 **Windows Phone 8.1**。

現在已建立新的 Windows Phone Silverlight 應用程式，接著我們要將 Azure Mobile Engagement SDK 整合至其中。

### 將您的應用程式連線至 Mobile Engagement 後端

1. 在專案中安裝 [MicrosoftAzure.MobileEngagement] nuget 封裝。

2. 開啟 `WMAppManifest.xml` (在 [屬性] 資料夾中)，並確定已宣告下列 (新增它們如果不是) 在 `< 功能 / >` 標記:

        <Capability Name="ID_CAP_NETWORKING" />
        <Capability Name="ID_CAP_IDENTITY_DEVICE" />

    ![][2]

3. 現在貼上您稍早為 Mobile Engagement 應用程式複製的連接字串，並將它貼 `Resources\EngagementConfiguration.xml` 檔案之間 `< connectionString >` 和 `< / connectionString >` 標記:

    ![][3]

4. 在 `App.xaml.cs` 檔案:

    a. 新增 `使用` 陳述式:

            using Microsoft.Azure.Engagement;

    b. 初始化 SDK 中的 `Application_Launching` 方法:

            private void Application_Launching(object sender, LaunchingEventArgs e)
            {
              EngagementAgent.Instance.Init();
            }

    c. 中插入以下 `Application_Activated`:

            private void Application_Activated(object sender, ActivatedEventArgs e)
            {
               EngagementAgent.Instance.OnActivated(e);
            }


## <a id="monitor"></a>若要啟用即時監視

若要開始傳送資料並確定使用者正在使用，您必須至少傳送一個畫面 (活動) 到 Mobile Engagement 後端。

1. 在 MainPage.xaml.cs 中新增 `使用` 陳述式:

        using Microsoft.Azure.Engagement;

2. 請以 **EngagementPage** 取代 **MainPage** 的基礎類別 (在 **PhoneApplicationPage** 之前)：

        class MainPage : EngagementPage 

3. 在您 `MainPage.xml` 檔案:

    a. 新增至命名空間宣告：

         xmlns:engagement="clr-namespace:Microsoft.Azure.Engagement;assembly=Microsoft.Azure.Engagement.EngagementAgent.WP"

    b. 取代 `電話: PhoneApplicationPage` XML 標籤名稱內 `engagement: EngagementPage`。

## <a id="monitor"></a>應用程式與即時監視連線

[AZURE.INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

## <a id="integrate-push"></a>啟用推播通知與應用程式內傳訊

Mobile Engagement 可讓您透過「推播通知」和「應用程式內傳訊」，於活動進行時與使用者互動和觸達。 此模組在 Mobile Engagement 入口網站中稱為觸達 (REACH)。
以下各節將設定您的用程式來接收它們。

### 啟用您的應用程式接收 MPNS 推播通知

加入新的功能，您 `WMAppManifest.xml` 檔案:

        ID_CAP_PUSH_NOTIFICATION
        ID_CAP_WEBBROWSERCOMPONENT

   ![][5]

### 初始化 REACH SDK

1. 在 `App.xaml.cs`, ，呼叫 `EngagementReach.Instance.Init();` 中 **Application_Launching** 函式，代理程式初始化後:

        private void Application_Launching(object sender, LaunchingEventArgs e)
        {
           EngagementAgent.Instance.Init();
           EngagementReach.Instance.Init();
        }

2. 在 `App.xaml.cs`, ，呼叫 `EngagementReach.Instance.OnActivated(e);` 中 **Application_Activated** 函式，代理程式初始化後:

        private void Application_Activated(object sender, ActivatedEventArgs e)
        {
           EngagementAgent.Instance.OnActivated(e);
           EngagementReach.Instance.OnActivated(e);
        }


您全都準備好了。 現在我們要驗證您已正確完成這項基本整合。

## <a id="send"></a>將通知傳送至您的應用程式

[AZURE.INCLUDE [Create Windows Push campaign](../../includes/mobile-engagement-windows-push-campaign.md)]

您現在應該會在裝置上看到通知，其會顯示為應用程式內通知 (如果應用程式已開啟)，否則會顯示為快顯通知，如下所示：

![][6]




[microsoftazure.mobileengagement]: http://go.microsoft.com/?linkid=9874664 
[mobile engagement windows phone sdk documentation]: ../mobile-engagement-windows-phone-integrate-engagement/ 
[1]: ./media/mobile-engagement-windows-phone-get-started/project-properties.png 
[2]: ./media/mobile-engagement-windows-phone-get-started/wmappmanifest-capabilities.png 
[3]: ./media/mobile-engagement-windows-phone-get-started/add-connection-string.png 
[5]: ./media/mobile-engagement-windows-phone-get-started/reach-capabilities.png 
[6]: ./media/mobile-engagement-windows-phone-get-started/push-screenshot.png 

