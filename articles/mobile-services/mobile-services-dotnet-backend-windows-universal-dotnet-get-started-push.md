<properties
    pageTitle="將推播通知新增至通用 Windows 8.1 應用程式 | Microsoft Azure"
    description="了解如何使用 Azure 通知中樞從 .NET 後端行動服務傳送推播通知給通用 Windows 8.1 應用程式。"
    services="mobile-services,notification-hubs"
    documentationCenter="windows"
    authors="ggailey777"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="mobile-services"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-windows-store"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="11/11/2015"
    ms.author="glenga"/>


# 將推播通知新增至行動服務應用程式

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


[AZURE.INCLUDE [mobile-services-selector-get-started-push](../../includes/mobile-services-selector-get-started-push.md)]

## 概觀

本主題說明如何使用 Azure 行動服務與 .NET 後端傳送推播通知至通用 Windows 應用程式。 在本教學課程中，您會透過 Azure 通知中心，啟用通用 Windows 應用程式專案中的推播通知功能。 完成之後，每次 TodoList 資料表中插入記錄時，您的行動服務將會從 .NET 後端將推播通知傳送至所有註冊的 Windows 市集與 Windows Phone 市集應用程式。 您所建立的通知中心可透過行動服務免費使用、可在行動服務以外個別管理，並且可供其他應用程式和服務使用。

若要完成此教學課程，您需要下列項目：

* 使用中 [Microsoft 市集帳戶](http://go.microsoft.com/fwlink/p/?LinkId=280045)。
* <a href="https://go.microsoft.com/fwLink/p/?LinkID=391934" target="_blank">Visual Studio Community 2013</a>。

## <a id="register"></a>針對推播通知註冊應用程式

[AZURE.INCLUDE [mobile-services-create-new-push-vs2013](../../includes/mobile-services-create-new-push-vs2013.md)]

&nbsp;&nbsp;6. 瀏覽至 `\Services\MobileServices\your_service_name` 專案資料夾，開啟產生的 push.register.cs 程式碼檔案，然後檢查用來將裝置通道 URL 向通知中樞註冊的 **UploadChannel** 方法。

&nbsp;&nbsp;7. 開啟 shared App.xaml.cs 程式碼檔案，並注意新的 **UploadChannel** 方法呼叫已經新增到 **OnLaunched** 事件處理常式。 這可確保在每次啟動應用程式時嘗試註冊裝置。

&nbsp;&nbsp;8. 重複上述步驟將所有推播通知新增至 Windows Phone 市集應用程式專案，然後從共用的 App.xaml.cs 檔案中移除額外的 **UploadChannel** 呼叫與剩餘的 `#if...#endif` 條件式包裝函式。 這兩個專案現在都會共用 **UploadChannel** 的單一呼叫。
> [AZURE.NOTE] 您也可以藉此簡化產生的程式碼 `#if...#endif` 包裝 [MobileServiceClient](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx) 定義統一至單一未包裝定義這兩個版本的應用程式所使用。

現在應用程式已經啟用了推播通知，您必須更新行動服務以傳送推播通知。

## <a id="update-service"></a>更新服務以傳送推播通知

以下步驟會更新現有的 TodoItemController 類別，以便在插入新的項目時將推播通知傳送給所有註冊的裝置。 您可以在任何自訂實作類似的程式碼 [ApiController](https://msdn.microsoft.com/library/system.web.http.apicontroller.aspx), ，[TableController](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobile.service.tables.tablecontroller.aspx), ，或是後端服務裡。

[AZURE.INCLUDE [mobile-services-dotnet-backend-update-server-push](../../includes/mobile-services-dotnet-backend-update-server-push.md)]

## <a id="local-testing"></a> 啟用推播通知以進行本機測試

[AZURE.INCLUDE [mobile-services-dotnet-backend-configure-local-push-vs2013](../../includes/mobile-services-dotnet-backend-configure-local-push-vs2013.md)]

本節剩下步驟皆為選擇使用。 您可使用這些步驟，測試您的應用程式與本機電腦上執行的行動服務是否相互運作正常。 如果您打算使用 Azure 執行的行動服務來測試推播通知，可以直接跳至最後一節。 這是因為 [新增推播通知] 精靈已經將您的應用程式設定為將您的應用程式連接至 Azure 上執行的服務。
>[AZURE.NOTE]切勿使用生產環境的行動服務來進行測試與開發作業。 請一律將您的行動服務專案發佈至個別的預備服務以利測試。

&nbsp;&nbsp;5. 開啟共用的 App.xaml.cs 專案檔案並找到任何建立新類別的執行個體 [MobileServiceClient] 來存取行動服務在 Azure 中執行的程式碼行。

&nbsp;&nbsp;6. 註解化此程式碼加入相同名稱的程式碼會建立新的 [MobileServiceClient]，而使用本機主機 URL 中的建構函式，如下所示:

    // This MobileServiceClient has been configured to communicate with your local
    // test project for debugging purposes.
    public static MobileServiceClient todolistClient = new MobileServiceClient(
        "http://localhost:4584"
    );

(& s) nbsp; & nbsp;使用此 [MobileServiceClient] 時，應用程式會連接到本機服務，而不是在 Azure 中代管的版本。 當您想要切換回，並在 Azure 中代管的行動服務上執行應用程式時，將變更回原來的 [MobileServiceClient] 定義。

## <a id="test"></a> 在您的應用程式中測試推播通知

[AZURE.INCLUDE [mobile-services-dotnet-backend-windows-universal-test-push](../../includes/mobile-services-dotnet-backend-windows-universal-test-push.md)]

## <a name="next-steps"> </a>後續步驟

本教學課程示範如何啟用 Windows 市集應用程式以便使用行動服務與通知中心來傳送推播通知的基礎。 接下來，考慮完成下一個教學課程中，[傳送推播通知給已驗證的使用者]，其中會顯示如何使用標記從行動服務傳送推播通知給已驗證的使用者。

在下列主題中深入了解行動服務和通知中心：

* [將行動服務新增至現有的應用程式 ][get started with data]
  <br/>深入了解如何儲存和查詢使用行動服務的資料。

* [將驗證新增至您的應用程式 ][get started with authentication]
  <br/>了解如何使用不同帳戶類型，使用行動服務驗證您的應用程式的使用者。

* [什麼是通知中心]?
  <br/>進一步了解通知中心將通知傳遞到您的應用程式跨所有主要用平台的運作方式。

* [偵錯通知中樞應用程式](http://go.microsoft.com/fwlink/p/?linkid=386630)
  </br>取得指引疑難排解和偵錯通知中樞解決方案。

* [如何使用 Azure 行動服務.NET 用戶端]
  <br/>進一步了解如何使用 C# Windows 應用程式的行動服務。







[submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582 
[my applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039 
[live sdk for windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253 
[get started with mobile services]: mobile-services-dotnet-backend-windows-store-dotnet-get-started.md 
[get started with data]: mobile-services-dotnet-backend-windows-universal-dotnet-get-started-data.md 
[get started with authentication]: mobile-services-dotnet-backend-windows-universal-dotnet-get-started-users.md 
[send push notifications to authenticated users]: mobile-services-dotnet-backend-windows-store-dotnet-push-notifications-app-users.md 
[what are notification hubs?]: ../notification-hubs-overview.md 
[how to use a .net client for azure mobile services]: mobile-services-windows-dotnet-how-to-use-client-library.md 
[mobileserviceclient]: http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx 

