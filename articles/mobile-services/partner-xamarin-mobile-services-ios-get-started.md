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

# <a name="getting-started"> </a>開始使用行動服務

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


[AZURE.INCLUDE [mobile-services-selector-get-started](../../includes/mobile-services-selector-get-started.md)]
&nbsp;

[AZURE.INCLUDE [mobile-services-hero-slug](../../includes/mobile-services-hero-slug.md)]

本教學課程將示範如何使用 Azure 行動服務，以將雲端後端服務新增至 Xamarin.iOS 應用程式。 在本教學課程中，您將建立新的行動服務，並建立可在新的行動服務中儲存應用程式資料的簡單*待辦事項清單*應用程式。

如果您想要看影片，下方片段播放的步驟與本教學課程相同。

影片: 「 取得開始使用 Xamarin 和 Azure 行動服務 」，Craig Dunn Xamarin 開發人員推廣者 (持續時間: 10:05 最小值)

> [AZURE.VIDEO getting-started-with-xamarin-and-mobile-services]

以下是完成應用程式的螢幕擷取畫面：

![][0]

完成本教學課程需要 XCode 和 [Xamarin Studio] OS X 或 Visual Studio 外掛程式，可在 Windows 上的 Visual Studio。 範例將在 iOS 5.0 和更新版本上執行。

> [AZURE.IMPORTANT] 若要完成本教學課程，您需要 Azure 帳戶。 如果您沒有帳戶，您可以註冊 Azure 試用版並取得高達 10 項的免費行動服務。此外，在試用期間結束後您仍可繼續使用這些服務。 如需詳細資料，請參閱 [Azure 免費試用](http://azure.microsoft.com/pricing/free-trial/)。

## <a name="create-new-service"> </a>建立新的行動服務

[AZURE.INCLUDE [mobile-services-create-new-service](../../includes/mobile-services-create-new-service.md)]

## 建立新的 Xamarin.iOS 應用程式

當您建立自己的行動服務之後，就可以依照 Azure 傳統入口網站中簡單的快速入門，來建立新的應用程式或修改現有的應用程式，以便連線到您的行動服務。

在本節中，您將建立可連接到您行動服務的新 Xamarin.iOS 應用程式。

1.  在 [Azure classic portal], ，按一下 [ **行動電話服務**, ，然後按一下您剛才建立的行動服務。

2. 在 [快速入門] 索引標籤中，按一下 [ **Xamarin.iOS** 下 **選擇平台** 展開 **建立新的 Xamarin.iOS 應用程式**。

    ![][6]

    這將顯示三個簡單步驟，可用來建立連接到您行動服務的 Xamarin.iOS 應用程式。

    ![][7]

3. 如果您尚未這樣做，請下載並安裝的 Xcode (我們建議最新版 Xcode 6.0 或更新版本) 和 [Xamarin Studio]。

4. 按一下 [ **Create TodoItems table** 來建立儲存應用程式資料的資料表。

5. 在 **下載並執行應用程式**, ，按一下 [ **下載**。

    這會下載範例專案 _待辦事項清單_ 連線到您的行動服務，以及參考 Xamarin.iOS 的 Azure 行動服務元件的應用程式。 請將壓縮的專案檔案儲存至本機電腦，並記下儲存位置。

## 執行新的 Xamarin.iOS 應用程式

本教學課程的最後階段是建立並執行新的應用程式。

1. 瀏覽至儲存壓縮的專案檔案的位置，您的電腦上展開檔案，並開啟 **XamarinTodoQuickStart.iOS.sln** 使用 Xamarin Studio 或 Visual Studio 方案檔。

    ![][8]

    ![][9]

2. 按下 **執行** ] 按鈕以建置專案並啟動應用程式在 iPhone 模擬器中，這是此專案的預設值。

3. 在應用程式中，輸入有意義的文字，例如 _完成教學課程_ ，然後按一下加號 (**+**) 圖示。

    ![][10]

    如此會傳送 POST 要求到 Azure 中代管的新行動服務。 要求中的資料會插入 TodoItem 資料表中。 行動服務會傳回資料表中儲存的項目，而該資料會顯示在清單中。

    > [AZURE.NOTE] 您可以檢閱存取行動服務來查詢和插入資料，可在 TodoService.cs C# 檔案中找到的程式碼。

4. 回到 [Azure classic portal], ，按一下 [ **資料** 標籤，然後按一下 **TodoItems** 資料表。

    ![][11]

    如此可讓您瀏覽由應用程式插入資料表中的資料。

    ![][12]


## 後續步驟
請注意，您已完成快速入門，並了解如何執行行動服務中的其他重要工作：

* [開始使用離線資料同步]
  了解快速入門如何使用離線資料同步處理，讓應用程式迅速回應而且穩固。

* [開始使用驗證]
  了解如何透過身分識別提供者來驗證您的應用程式使用者。

* [開始使用推播通知]
  了解如何將非常基本的推播通知傳送至您的應用程式。

* [如何使用 Azure 行動服務的 Xamarin 元件用戶端](partner-xamarin-mobile-services-how-to-use-client-library.md)
   了解如何查詢行動服務、 處理資料，以及存取自訂 Api。


[AZURE.INCLUDE [app-service-disqus-feedback-slug](../../includes/app-service-disqus-feedback-slug.md)]

<!-- Anchors. -->
[Getting started with Mobile Services]:#getting-started
[Create a new mobile service]:#create-new-service
[Define the mobile service instance]:#define-mobile-service-instance
[Next Steps]:#next-steps

<!-- Images. -->
[0]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-quickstart-completed-ios.png
[6]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-portal-quickstart-xamarin-ios.png
[7]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-quickstart-steps-xamarin-ios.png
[8]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-xamarin-project-ios-xs.png
[9]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-xamarin-project-ios-vs.png
[10]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-quickstart-startup-ios.png
[11]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-data-tab.png
[12]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-data-browse.png


<!-- URLs. -->
[Get started with offline data sync]: mobile-services-xamarin-ios-get-started-offline-data.md
[Get started with authentication]: partner-xamarin-mobile-services-ios-get-started-users.md
[Get started with push notifications]: partner-xamarin-mobile-services-ios-get-started-push.md

[Xamarin Studio]: http://xamarin.com/download
[Mobile Services iOS SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533

[Azure classic portal]: https://manage.windowsazure.com/

