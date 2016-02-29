<properties
    pageTitle="開始使用 Xamarin.Android 應用程式的行動服務 | Microsoft Azure"
    description="遵循此教學課程，可開始使用 Azure 行動服務進行 Xamarin Android 開發。"
    services="mobile-services"
    documentationCenter="xamarin"
    authors="lindydonna"
    manager="dwrede"
    editor="mollybos"/>

<tags
    ms.service="mobile-services"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin-android"
    ms.devlang="dotnet"
    ms.topic="get-started-article"
    ms.date="10/12/2015"
    ms.author="donnam"/>

# <a name="getting-started"> </a>開始使用行動服務

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


[AZURE.INCLUDE [mobile-services-selector-get-started](../../includes/mobile-services-selector-get-started.md)]
&nbsp;

>[AZURE.TIP] 如果您是使用 Microsoft Azure 的行動應用程式開發新手 [開始使用 Azure 行動應用程式](app-service-mobile-dotnet-backend-xamarin-android-get-started-preview.md) 而不是 Azure 行動服務。行動應用程式可讓您 [其他優點](app-service-mobile-value-prop-migration-from-mobile-services-preview.md)。

本教學課程顯示如何使用 Azure 行動服務，將雲端型後端服務新增到 Xamarin Android 應用程式。 在本教學課程中，您將建立新的行動服務，並建立可在新的行動服務中儲存應用程式資料的簡單_待辦事項清單_應用程式。 您所將建立的行動服務，會使用 Visual Studio 與支援的 .NET 語言撰寫伺服器端商務邏輯，並管理行動服務。 若要建立可讓您以 JavaScript 撰寫伺服器端商務邏輯的行動服務，請參閱 [JavaScript 後端版本] 本主題。

>[AZURE.NOTE]本主題說明如何使用 Azure 傳統入口網站建立新的行動服務專案。 使用 Visual Studio 2013 Update 2，可讓您將新的行動服務專案新增至現有的 Visual Studio 方案。 如需詳細資訊，請參閱 [快速入門: 新增行動服務 (.NET 後端)](http://msdn.microsoft.com/library/windows/apps/dn629482.aspx)

以下是完成應用程式的螢幕擷取畫面：

![][0]

完成本教學課程是 Xamarin Android 應用程式所有其他行動服務教學課程的先決條件。

>[AZURE.NOTE]若要完成此教學課程，您需要 Azure 帳戶。 如果您沒有帳戶，您可以註冊 Azure 試用版並取得高達 10 項的免費行動服務。此外，在試用期間結束後您仍可繼續使用這些服務。 如需詳細資料，請參閱 [Azure 免費試用](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-services-dotnet-backend-xamarin-android-get-started)。
>本教學課程需要 [Visual Studio Professional 2013](https://go.microsoft.com/fwLink/p/?LinkID=257546)。 您可以使用免費試用版。

## 建立新的行動服務

[AZURE.INCLUDE [mobile-services-dotnet-backend-create-new-service](../../includes/mobile-services-dotnet-backend-create-new-service.md)]

## 建立新的 Xamarin Android 應用程式

當您建立自己的行動服務之後，就可以依照 Azure 傳統入口網站中簡單的快速入門，來建立新的應用程式或修改現有的應用程式，以便連線到您的行動服務。

在本節中，您將為行動服務下載新的 Xamarin Android 應用程式和服務專案。

1. 在 [Azure 傳統入口網站]，按一下 [ **行動電話服務**, ，然後按一下您剛才建立的行動服務。

2. 在 [快速入門] 索引標籤中，按一下 [ **Xamarin** 下 **選擇平台** 展開 **建立新的 Xamarin 應用程式**。

    ![][6]

    這將顯示三個簡單步驟，可用來建立連接到您行動服務的 Xamarin Android 應用程式。

    ![][7]

3. 在您的本機電腦或虛擬機器下載並安裝 [Visual Studio Professional 2013](https://go.microsoft.com/fwLink/p/?LinkID=257546) (如果您尚未這麼做)。

4. 如果您尚未這樣做，請下載並安裝 [Xamarin Studio] 或 Xamarin for Visual Studio。

5. 在 **下載，並將服務發行至雲端**, ，請選取 **Android** 按一下 **下載**。

    這會下載一個方案包含專案與行動服務以及範例 _待辦事項清單_ 連線到您的行動服務的應用程式。 將此壓縮專案檔案儲存到您的本機電腦，並記錄儲存位置。

6. 下載您的發行設定檔、將下載的檔案儲存至本機電腦，並記下儲存位置。

## 測試行動服務

[AZURE.INCLUDE [mobile-services-dotnet-backend-test-local-service](../../includes/mobile-services-dotnet-backend-test-local-service.md)]

## 發佈行動服務

[AZURE.INCLUDE [mobile-services-dotnet-backend-publish-service](../../includes/mobile-services-dotnet-backend-publish-service.md)]

## 執行 Xamarin Android 應用程式

本教學課程的最後階段是建立並執行新的應用程式。

1. 在 Visual Studio 或 Xamarin Studio 中，導覽至行動服務方案內的用戶端專案。

2. 按下 **執行** ] 按鈕以建置專案並啟動應用程式。 系統將要求您選取模擬器或連接的 USB 裝置。

    > [AZURE.NOTE] 若要能夠在 Android 模擬器中執行專案，您必須定義至少一個 Android 虛擬裝置 (AVD)。 請使用 AVD 管理員來建立和管理這些裝置。

3. 在應用程式中，輸入有意義的文字，例如 _完成教學課程_ ，然後按一下加號 (**+**) 圖示。

    ![][10]

    如此會傳送 POST 要求到 Azure 中代管的新行動服務。 要求中的資料會插入 TodoItem 資料表中。 行動服務會傳回資料表中儲存的項目，而該資料會顯示在清單中。

    > [AZURE.NOTE]
    > 您可以檢閱存取行動服務以查詢與插入資料的程式碼，您可在 ToDoActivity.cs C# 檔案中找到此程式碼。

## 後續步驟
請注意，您已完成快速入門，並了解如何執行行動服務中的其他重要工作：

* [開始使用離線資料同步]
  <br/>了解快速入門如何使用離線資料同步處理，讓應用程式迅速回應而且穩固。

* [開始使用驗證]
  <br/>了解如何透過身分識別提供者來驗證您的應用程式使用者。

* [開始使用推播通知]
  <br/>了解如何將非常基本的推播通知傳送至您的應用程式。

* [行動服務.NET 後端的疑難排解]
  <br/> 了解如何診斷和修正行動服務 .NET 後端可能發生的問題。

[AZURE.INCLUDE [app-service-disqus-feedback-slug](../../includes/app-service-disqus-feedback-slug.md)]

<!-- Anchors. -->
[Getting started with Mobile Services]:#getting-started
[Create a new mobile service]:#create-new-service
[Next Steps]:#next-steps



<!-- Images. -->
[0]: ./media/mobile-services-dotnet-backend-xamarin-android-get-started/mobile-quickstart-completed-android.png
[6]: ./media/mobile-services-dotnet-backend-xamarin-android-get-started/mobile-portal-quickstart-xamarin.png
[7]: ./media/mobile-services-dotnet-backend-xamarin-android-get-started/mobile-quickstart-steps-xamarin-android.png
[8]: ./media/mobile-services-dotnet-backend-xamarin-android-get-started/mobile-xamarin-project-android-vs.png
[9]: ./media/mobile-services-dotnet-backend-xamarin-android-get-started/mobile-xamarin-project-android-xs.png
[10]: ./media/mobile-services-dotnet-backend-xamarin-android-get-started/mobile-quickstart-startup-android.png

<!-- URLs. -->
[Get started with offline data sync]: mobile-services-xamarin-android-get-started-offline-data.md
[Get started with authentication]: mobile-services-dotnet-backend-xamarin-android-get-started-users.md
[Get started with push notifications]: mobile-services-dotnet-backend-xamarin-android-get-started-push.md
[Visual Studio Professional 2013]: https://go.microsoft.com/fwLink/p/?LinkID=257546
[Mobile Services SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[JavaScript and HTML]: mobile-services-win8-javascript/
[Azure classic portal]: https://manage.windowsazure.com/
[JavaScript backend version]: mobile-services-android-get-started.md
[Troubleshoot a Mobile Services .NET backend]: mobile-services-dotnet-backend-how-to-troubleshoot.md


[Xamarin Studio]: http://xamarin.com/download
[Xcode]: https://go.microsoft.com/fwLink/?LinkID=266532&clcid=0x409
[Xamarin for Windows]: https://go.microsoft.com/fwLink/?LinkID=330242&clcid=0x409

