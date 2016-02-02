<properties
    pageTitle="在 iOS 應用程式中開始使用 Azure 行動服務"
    description="遵循此教學課程，可開始使用 Azure 行動服務進行 iOS 開發。"
    services="mobile-services"
    documentationCenter="ios"
    authors="krisragh"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="mobile-services"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-ios"
    ms.devlang="objective-c"
    ms.topic="article"
    ms.date="11/04/2015"
    ms.author="krisragh"/>


# <a name="getting-started"> </a>開始使用行動服務

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


[AZURE.INCLUDE [mobile-services-selector-get-started](../../includes/mobile-services-selector-get-started.md)]
&nbsp;
>[AZURE.TIP] 如果您是使用 Microsoft Azure 的行動應用程式開發新手 [開始使用 Azure 行動應用程式](app-service-mobile-dotnet-backend-ios-get-started-preview.md) 而不是 Azure 行動服務。行動應用程式可讓您 [其他優點](app-service-mobile-value-prop-migration-from-mobile-services-preview.md)。

本教學課程說明如何使用 Azure 行動服務在 iOS 應用程式中新增雲端型後端服務。 在本教學課程中，您將建立新的行動服務，並建立可在新的行動服務中儲存應用程式資料的簡單「_待辦事項清單_」應用程式。 行動服務會使用 .NET 和 Visual Studio 於伺服器端商務邏輯。 若要在 JavaScript 伺服器端商務邏輯建立行動服務，請參閱 [JavaScript 後端版本] 本主題。
> [AZURE.NOTE] 若要完成此教學課程，您需要 Azure 帳戶。 如果您沒有帳戶，您可以註冊 Azure 試用版並取得 [免費的行動服務，即使在試用期結束之後可繼續使用](http://azure.microsoft.com/pricing/details/mobile-services/)。 如需詳細資訊，請參閱 [Azure 免費試用](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AE564AB28&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fdocumentation%2Farticles%2Fmobile-services-dotnet-backend-ios-get-started%2F)。

## <a name="create-new-service"> </a>建立新的行動服務

[AZURE.INCLUDE [mobile-services-dotnet-backend-create-new-service](../../includes/mobile-services-dotnet-backend-create-new-service.md)]

## 將行動服務與應用程式下載至您的本機電腦

現在您已建立行動服務，下載可以在本機執行的專案。

1. 按一下您剛剛建立的行動服務，然後在 [快速入門] 索引標籤中，按一下 [選擇平台]**** 下的 [iOS]****，並展開 [建立新的 iOS 應用程式]****。

2. 在您的 Windows 電腦上，按一下 [下載您的服務並發佈至雲端]**** 下的 [下載]****。 這會下載實作行動服務的 Visual Studio 專案。 請將壓縮的專案檔案儲存至本機電腦，並記下儲存位置。

3. 在您的 Mac 上，按一下 [下載並執行您的應用程式]**** 下的 [下載]****。 如此會下載與您行動服務連線之範例應用程式 _To do list_ 的專案，以及行動服務 iOS SDK。 請將壓縮的專案檔案儲存至本機電腦，並記下儲存位置。

## 測試行動服務

[AZURE.INCLUDE [mobile-services-dotnet-backend-test-local-service](../../includes/mobile-services-dotnet-backend-test-local-service.md)]

## 發佈行動服務

[AZURE.INCLUDE [mobile-services-dotnet-backend-publish-service](../../includes/mobile-services-dotnet-backend-publish-service.md)]


## 執行新的 iOS 應用程式

[AZURE.INCLUDE [mobile-services-ios-run-app](../../includes/mobile-services-ios-run-app.md)]


## <a name="next-steps"> </a>後續步驟

這會說明如何對執行於 Azure 中的行動服務執行新的用戶端應用程式。 您必須設定 Web 伺服器和防火牆以允許來自您 iOS 開發電腦的存取，才能對執行於本機電腦上的行動服務測試 iOS 應用程式。 如需詳細資訊，請參閱 [設定本機 web 伺服器，以允許連接到本機行動服務](mobile-services-dotnet-backend-how-to-configure-iis-express.md)。

了解如何在行動服務中執行其他重要工作：

* [開始使用離線資料同步]
  <br/>了解如何使用離線資料同步，讓您的應用程式更穩健及具備。

* [將驗證新增至現有的應用程式]
  <br/>了解如何驗證應用程式的身分識別提供者的使用者。

* [將推播通知新增至現有的應用程式]
  <br/>了解如何將非常基本的推播通知傳送至您的應用程式。

* [行動服務.NET 後端的疑難排解]
  <br/> 了解如何診斷及修復行動服務.NET 後端可能發生的問題。

[AZURE.INCLUDE [app-service-disqus-feedback-slug](../../includes/app-service-disqus-feedback-slug.md)]





[getting started with mobile services]: #getting-started 
[create a new mobile service]: #create-new-service 
[define the mobile service instance]: #define-mobile-service-instance 
[next steps]: #next-steps 
[0]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-quickstart-completed-ios.png 
[1]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-quickstart-steps-vs.png 
[6]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-portal-quickstart-ios.png 
[7]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-quickstart-steps-ios.png 
[8]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-xcode-project.png 
[10]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-quickstart-startup-ios.png 
[11]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-data-tab.png 
[12]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-data-browse.png 
[get started with offline data sync]: mobile-services-ios-get-started-offline-data.md 
[add authentication to an existing app]: mobile-services-dotnet-backend-ios-get-started-users.md 
[add push notifications to an existing app]: mobile-services-dotnet-backend-ios-get-started-push.md 
[troubleshoot mobile services .net backend]: mobile-services-dotnet-backend-how-to-troubleshoot.md 
[mobile services ios sdk]: https://go.microsoft.com/fwLink/p/?LinkID=266533 
[xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532 
[javascript backend version]: mobile-services-ios-get-started.md 

