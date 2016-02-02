<properties
    pageTitle="開始使用 Xamarin iOS 應用程式的行動服務 | Microsoft Azure"
    description="遵循此教學課程，可開始使用 Azure 行動服務進行 Xamarin iOS 開發。"
    services="mobile-services"
    documentationCenter="xamarin"
    authors="conceptdev"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="mobile-services"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin-ios"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="11/05/2015"
    ms.author="craig.dunn@xamarin.com"/>


# 

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


[AZURE.INCLUDE [mobile-services-selector-get-started](../../includes/mobile-services-selector-get-started.md)]
&nbsp;

[AZURE.INCLUDE [mobile-services-hero-slug](../../includes/mobile-services-hero-slug.md)]

本教學課程將示範如何使用 Azure 行動服務，以將雲端後端服務新增至 Xamarin.iOS 應用程式。 在本教學課程中，您將建立新的行動服務，並建立可在新的行動服務中儲存應用程式資料的簡單*待辦事項*應用程式。

如果您想要看影片，下方片段播放的步驟與本教學課程相同。



> [AZURE.VIDEO getting-started-with-xamarin-and-mobile-services]

以下是完成應用程式的螢幕擷取畫面：

![][0]

範例將在 iOS 5.0 和更新版本上執行。
> [AZURE.IMPORTANT] 若要完成此教學課程，您需要 Azure 帳戶。 如果您沒有帳戶，您可以註冊 Azure 試用版並取得高達 10 項的免費行動服務。此外，在試用期間結束後您仍可繼續使用這些服務。

## 

[AZURE.INCLUDE [mobile-services-create-new-service](../../includes/mobile-services-create-new-service.md)]

## 建立新的 Xamarin.iOS 應用程式

當您建立自己的行動服務之後，就可以依照 Azure 傳統入口網站中簡單的快速入門，來建立新的應用程式或修改現有的應用程式，以便連線到您的行動服務。

在本節中，您將建立可連接到您行動服務的新 Xamarin.iOS 應用程式。

1.  

2. 在快速入門索引標籤中，按一下 [Choose platform]**** 下的 [Xamarin.iOS]****，並展開 [Create a new Xamarin.iOS app]****。

    ![][6]

    這將顯示三個簡單步驟，可用來建立連接到您行動服務的 Xamarin.iOS 應用程式。

    ![][7]

3. 

4. 按一下 [Create TodoItems table]**** 以建立儲存應用程式資料的資料表。

5. 按一下 [Download and run app]**** 下的 [下載]****。

    這會下載與您的行動服務連接的範例 _To do list_ 應用程式的專案，以及參考 Xamarin.iOS 的 Azure 行動服務元件。 請將壓縮的專案檔案儲存至本機電腦，並記下儲存位置。

## 執行新的 Xamarin.iOS 應用程式

本教學課程的最後階段是建立並執行新的應用程式。

1. 瀏覽至您儲存此壓縮專案檔案的位置、在您的電腦上展開檔案，並使用 Xamarin Studio 或 Visual Studio 來開啟 **XamarinTodoQuickStart.iOS.sln** 解決方案檔案。

    ![][8]

    ![][9]

2. 按 [執行]**** 按鈕以建立專案，並在 iPhone 模擬器中啟動應用程式 (此專案的預設選項)。

3. 

    ![][10]

    如此會傳送 POST 要求到 Azure 中代管的新行動服務。 要求中的資料會插入 TodoItem 資料表中。 行動服務會傳回資料表中儲存的項目，而該資料會顯示在清單中。
    > [AZURE.NOTE] 您可以檢閱存取行動服務以查詢與插入資料的程式碼，您可在 TodoService.cs C# 檔案中找到此程式碼。

4. 

    ![][11]

    如此可讓您瀏覽由應用程式插入資料表中的資料。

    ![][12]


## 後續步驟

請注意，您已完成快速入門，並了解如何執行行動服務中的其他重要工作：

* 
  了解快速入門如何使用離線資料同步處理，讓應用程式迅速回應而且穩固。

* 
  了解如何透過身分識別提供者來驗證您的應用程式使用者。

* 
  了解如何將非常基本的推播通知傳送至您的應用程式。

* 
   


[AZURE.INCLUDE [app-service-disqus-feedback-slug](../../includes/app-service-disqus-feedback-slug.md)]





[getting started with mobile services]: #getting-started 
[create a new mobile service]: #create-new-service 
[define the mobile service instance]: #define-mobile-service-instance 
[next steps]: #next-steps 
[0]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-quickstart-completed-ios.png 
[6]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-portal-quickstart-xamarin-ios.png 
[7]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-quickstart-steps-xamarin-ios.png 
[8]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-xamarin-project-ios-xs.png 
[9]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-xamarin-project-ios-vs.png 
[10]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-quickstart-startup-ios.png 
[11]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-data-tab.png 
[12]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-data-browse.png 
[get started with offline data sync]: mobile-services-xamarin-ios-get-started-offline-data.md 
[get started with authentication]: partner-xamarin-mobile-services-ios-get-started-users.md 
[get started with push notifications]: partner-xamarin-mobile-services-ios-get-started-push.md 
[xamarin studio]: http://xamarin.com/download 
[mobile services ios sdk]: https://go.microsoft.com/fwLink/p/?LinkID=266533 
[azure classic portal]: https://manage.windowsazure.com/ 

