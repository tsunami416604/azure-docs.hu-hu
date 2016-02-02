<properties
    pageTitle="處理通用 Windows 應用程式中的離線資料衝突 | Microsoft Azure"
    description="了解您在通用 Windows 應用程式中同步離線資料時應如何使用 Azure 行動服務處理衝突"
    documentationCenter="windows"
    authors="wesmc7777"
    manager="dwrede"
    editor=""
    services="mobile-services"/>

<tags
    ms.service="mobile-services"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-windows-store"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="11/12/2015"
    ms.author="glenga"/>



# 處理行動服務中的離線資料同步衝突

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


[AZURE.INCLUDE [mobile-services-selector-offline-conflicts](../../includes/mobile-services-selector-offline-conflicts.md)]

## 概觀

本主題將說明在使用 Azure 行動服務的離線功能時，應如何同步處理資料及處理衝突。

如果您想要看影片，下方片段播放的步驟與本教學課程相同。

> [AZURE.VIDEO build-offline-apps-with-mobile-services]

在本教學課程中，您會為支援處理離線同步處理衝突的應用程式，下載通用的 Windows C# 解決方案。 您將在應用程式中整合行動服務，然後執行 Windows 市集 8.1 和 Windows Phone 8.1 用戶端以產生同步衝突，並加以解決。

本教學課程為基礎的步驟和範例應用程式，從先前的教學課程 [開始使用離線資料]。 在開始本教學課程之前，您應該先完成 [開始使用離線資料]。


## 必要條件

本教學課程需要執行於 Windows 8.1 的 Visual Studio 2013。


## 下載範例專案

![][0]

本教學課程會逐步解說 [Todo 離線行動服務範例] 如何處理本機離線存放區與 Azure 中的行動服務資料庫之間的同步衝突。

1. 下載的 zip 檔案的 [行動服務範例 GitHub 儲存機制]，並將它解壓縮到工作目錄。

2. 如果您尚未安裝 SQLite for Windows 8.1 和 Windows Phone 8.1 中所述 [開始使用離線資料] 教學課程中，安裝這兩個執行階段。

3. 在 Visual Studio 2013，開啟 *mobile-services-samples\TodoOffline\WindowsUniversal\TodoOffline-Universal.sln* 方案檔。 請按 **F5** 鍵，以重新建置與執行專案。 確認 NuGet 封裝已還原，並已正確設定參考。
    >[AZURE.NOTE] 您可能需要刪除任何舊的 SQLite 執行階段參考，並取代更新過的參考中所述 [開始使用離線資料] 教學課程。

4. 在應用程式的 [插入 TodoItem]**** 中輸入一些文字，然後按一下 [儲存]****，來將一些 todo 項目加入本機存放區。 然後關閉應用程式。

請注意，應用程式尚未連接到任何行動服務，因此 [推送]**** 和 [提取]**** 按鈕將會擲出例外狀況。




## 測試您的行動服務應用程式

現在，我們將對行動服務進行應用程式測試。

1. 在 [Azure 傳統入口網站]，找出您的行動服務應用程式金鑰依序按一下 **管理金鑰** 命令列上的 **儀表板** ] 索引標籤。 複製 [應用程式金鑰]****。

2. 在 Visual Studio 的 [方案總管] 中，開啟 JavaScript 用戶端範例專案中的 App.xaml.cs 檔案。 變更 **MobileServiceClient** 的初始化，使其使用您的行動服務 URL 和應用程式金鑰：

         public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://your-mobile-service.azure-mobile.net/",
            "Your AppKey"
        );

3. 在 Visual Studio 中按 **F5** 鍵，以建置並重新執行此應用程式。

    ![][0]


## 更新後端中的資料以產生衝突

在實際情況中，如果有一個應用程式推送資料庫中某筆記錄的更新，然後又有另一個應用程式嘗試使用該記錄的過期版本欄位來推送同一筆記錄的更新，此時便會發生同步衝突。 如果您還記得從 [開始使用離線資料]，version 系統屬性才能支援離線同步處理功能。 每個資料庫更新都會檢查此版本資訊。 如果應用程式的執行個體嘗試使用過期的版本更新記錄時，會發生衝突，並且被當作加以攔截 `MobileServicePreconditionFailedException` 應用程式中。 如果應用程式未攔截 `MobileServicePreconditionFailedException` 則 `MobileServicePushFailedException` 將最後會擲回以說明發生多少同步錯誤。
>[AZURE.NOTE] 若要支援離線資料同步處理已刪除的記錄，您應該啟用 [虛刪除](mobile-services-using-soft-delete.md)。 否則，您必須手動移除本機存放區或呼叫中的 [記錄 `IMobileServiceSyncTable::PurgeAsync()` 才能清除本機存放區。


下列步驟說明如何使用範例同時執行 Windows Phone 8.1 和 Windows 市集 8.1 用戶端，以造成並解決衝突。

1. 在 Visual Studio 中，以滑鼠右鍵按一下 Windows Phone 8.1 專案，然後按一下 [設定為啟始專案]****。 然後按 **Ctrl+F5** 鍵，以在不偵錯的情形下執行 Windows Phone 8.1 用戶端。 一旦您在模擬器中啟動並執行 Windows Phone 8.1 用戶端，請按一下 [提取]**** 按鈕，以同步處理本機存放區與資料庫的目前狀態。

    ![][3]

2. 在 Visual Studio 中，以滑鼠右鍵按一下 Windows 8.1 執行階段專案，然後按一下 [設定為啟始專案]****，將它設回成啟始專案。 然後按 **F5** 執行此專案。 一旦您在模擬器中啟動並執行 Windows 市集 8.1 用戶端，請按一下 [提取]**** 按鈕，以同步處理本機存放區與資料庫的目前狀態。

    ![][4]

3. 此時，這兩個用戶端都已與資料庫同步處理。 兩個用戶端的程式碼也會使用增量同步處理，如此一來它們只會同步未完成的 todo 項目。 已完成的 todo 項目會被忽略。 選擇其中一個項目，並在這兩個用戶端中將相同項目的文字編輯成不同的值。 按一下 [推送]**** 按鈕，將這兩個變更同步處理到伺服器上的資料庫。

    ![][5]

    ![][6]

4. 執行推入的用戶端最後會發生衝突，使用者可以決定要認可至資料庫的值。 例外狀況會提供可用來解決衝突的正確版本值。

    ![][7]



## 檢閱處理同步衝突的程式碼

為使用行動服務的離線功能，您必須在本機資料庫和資料傳輸物件中包含版本資料行。 這可以藉由更新 `TodoItem` 類別的下列成員:

        [Version]
        public string Version { get; set; }

`__Version` 本機資料庫中的資料行包含  `onnavigatedto ()` 方法時 `TodoItem` 類別用來定義本機存放區。

若要使用程式碼處理離線同步衝突，您必須建立實作 `IMobileServiceSyncHandler` 的類別。 此型別的物件傳入的呼叫 `mobileserviceclient.synccontext.initializeasync ()`。 這也會發生在  `onnavigatedto ()` 方法的範例。

     await App.MobileService.SyncContext.InitializeAsync(store, new SyncHandler(App.MobileService));

類別 `SyncHandler` 中 **SyncHandler.cs** 實作 `IMobileServiceSyncHandler`。 每個推送作業傳送至伺服器時，都會呼叫 `ExecuteTableOperationAsync` 方法。 如果擲出了 `MobileServicePreconditionFailedException` 類型的例外狀況，表示項目的本機與遠端版本之間具有衝突。

若要以有利於本機項目的方式解決衝突，您應直接重試作業。 衝突發生後，本機項目版本會進行更新以符合伺服器版本，因此重新執行作業時，將會以本機變更覆寫伺服器變更：

    await operation.ExecuteAsync();

若要以有利於伺服器項目的方式解決衝突，請直接從 `ExecuteTableOperationAsync` 回復。 物件的本機版本將會被捨棄，並取代為伺服器中的值。

若要停止推送作業 (但保留佇列上的變更)，請使用 `AbortPush()` 方法：

    operation.AbortPush();

這將會停止目前的推送作業，但仍將持續執行所有擱置變更，包括目前的作業 (如果從 `ExecuteTableOperationAsync` 呼叫了 `AbortPush`)。 下次呼叫 `PushAsync()` 時，這些變更將會傳送至伺服器。

在取消推送時，`PushAsync` 將會擲出 `MobileServicePushFailedException`，且例外狀況屬性 `PushResult.Status` 的值將是 `MobileServicePushStatus.CancelledByOperation`。






[0]: ./media/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/mobile-services-handling-conflicts-app-run1.png 
[1]: ./media/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/javascript-backend-database.png 
[2]: ./media/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/dotnet-backend-database.png 
[3]: ./media/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/wp81-view.png 
[4]: ./media/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/win81-view.png 
[5]: ./media/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/wp81-edit-text.png 
[6]: ./media/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/win81-edit-text.png 
[7]: ./media/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/conflict.png 
[handling conflicts code sample]: http://go.microsoft.com/fwlink/?LinkId=394787 
[get started with mobile services]: ../mobile-services-windows-store-get-started.md 
[get started with offline data]: mobile-services-windows-store-dotnet-get-started-offline-data.md 
[sqlite for windows 8.1]: http://go.microsoft.com/fwlink/?LinkId=394776 
[azure classic portal]: https://manage.windowsazure.com/ 
[handling database conflicts]: mobile-services-windows-store-dotnet-handle-database-conflicts.md#test-app 
[mobile services samples github repository]: http://go.microsoft.com/fwlink/?LinkId=512865 
[todo offline mobile services sample]: http://go.microsoft.com/fwlink/?LinkId=512866 

