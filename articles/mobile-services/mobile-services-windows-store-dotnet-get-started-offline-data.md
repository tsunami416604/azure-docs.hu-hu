<properties
    pageTitle="在您的通用 Windows 應用程式中使用離線資料 | Microsoft Azure"
    description="了解如何使用 Azure 行動服務快取及同步通用 Windows 應用程式中的離線資料。"
    documentationCenter="mobile-services"
    authors="lindydonna"
    manager="dwrede"
    editor=""
    services="mobile-services"/>

<tags
    ms.service="mobile-services"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-windows"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="11/06/2015"
    ms.author="donnam"/>


# 在行動服務中使用離線資料同步

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


[AZURE.INCLUDE [mobile-services-selector-offline](../../includes/mobile-services-selector-offline.md)]

本教學課程會示範如何使用 Azure 行動服務將離線支援加入至 Windows 通用市集應用程式。 當您的應用程式處於離線狀態時，離線支援可讓您與本機資料庫互動。 一旦您的應用程式與後端資料庫連線之後，您就可使用離線功能來同步處理本機變更。

如果想要看影片，右邊片段播放的步驟與本教學課程相同。

> [AZURE.VIDEO build-offline-apps-with-mobile-services]

在本教學課程中，您會更新通用應用程式專案，從 [開始使用行動服務] 教學課程，以支援 Azure 行動服務的離線功能。 然後您會在中斷連線的離線狀態下新增資料、 同步處理至線上資料庫，這些項目，然後登入 [Azure 傳統入口網站] 檢視執行應用程式時所做的資料所做的變更。
>[AZURE.NOTE] 本教學課程旨在協助您深入了解如何透過行動服務，來使用 Azure 儲存並擷取 Windows 市集應用程式中的資料。 如果這是您第一次接觸行動服務，您應先完成本教學課程 [開始使用行動服務] 第一次。

## 必要條件

本教學課程需要下列各項：

* 執行於 Windows 8.1 的 Visual Studio 2013。
* 完成 [開始使用行動服務]。
* [Azure 行動服務 SDK 1.3.0 (或更新版本) ][mobile services sdk nuget]
* [Azure 行動服務 SQLite Store 1.0.0 版 (或更新版本) ][sqlite store nuget]
* [SQLite for Windows 8.1](http://www.sqlite.org/download.html)
* 一個 Azure 帳戶。 如果您沒有帳戶，您可以註冊 Azure 試用版並取得高達 10 項的免費行動服務。此外，在試用期間結束後您仍可繼續使用這些服務。 如需詳細資訊，請參閱 [Azure 免費試用](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AE564AB28)。

## <a name="enable-offline-app"></a>更新應用程式，以支援離線功能

Azure 行動服務的離線功能可讓您在行動服務處於離線狀態時，仍可與本機資料庫互動。 若要在應用程式中使用這些功能，您初始化 `MobileServiceClient.SyncContext` 本機存放區。 接著，請透過 `IMobileServiceSyncTable` 介面參考您的資料表。 在本教學課程中，我們會使用 SQLite 作為本機存放區。
>[AZURE.NOTE] 您可以略過本節，直接從行動服務的 GitHub 範例儲存機制取得已具有離線支援的範例專案。 具有離線支援的範例專案位於這裡，[TodoList 離線範例]。

1. 安裝適用於 Windows 8.1 和 Windows Phone 8.1 的 SQLite Runtime。

    * **Windows 8.1 執行階段:** 安裝 [適用於 Windows 8.1 的 SQLite]。
    * **Windows Phone 8.1:** 安裝 [SQLite for Windows Phone 8.1]。
    >[AZURE.NOTE] 如果您要使用 Internet Explorer，當您按一下安裝 SQLite 的連結時，系統會提示您下載 .zip 檔案格式的 .vsix。 請以 .vsix 副檔名將此檔案儲存至您的硬碟，而不要使用 .zip。 在 Windows 檔案總管中按兩下此 .vsix 檔案，以執行安裝。

2. 在 Visual Studio 中開啟的專案，您在完成 [開始使用行動服務] 教學課程。 安裝適用於 Windows 8.1 執行階段和 Windows Phone 8.1 專案的 **WindowsAzure.MobileServices.SQLiteStore** NuGet 封裝。

    * **Windows 8.1：**在 [方案總管] 中，以滑鼠右鍵按一下 Windows 8.1 專案，然後按一下 [管理 Nuget 封裝]****，以執行 NuGet 封裝管理員。 搜尋 **SQLiteStore**，以安裝 `WindowsAzure.MobileServices.SQLiteStore` 封裝。
    * **Windows Phone 8.1：**以滑鼠右鍵按一下 Windows Phone 8.1 專案，然後按一下 [管理 NuGet 封裝]****，以執行 NuGet 封裝管理員。 搜尋 **SQLiteStore**，以安裝 `WindowsAzure.MobileServices.SQLiteStore` 封裝。
    >[AZURE.NOTE] 如果安裝建立舊版 SQLite 的參考，您可以只刪除該重複的參考。

    ![][2]

2. 在方案總管中，以滑鼠右鍵按一下 Windows 8.1 執行階段和 Windows Phone 8.1 平台專案的 [參考]****，並確定有 SQLite 的參考位於 [擴充功能]**** 區段中。

    ![][1]
    </br>

    **Windows 8.1 執行階段**

    ![][11]
    </br>

    **Windows Phone 8.1**

3. SQLite Runtime 會要求您變更要建置到 **x86**、**x64** 或 **ARM** 之專案的處理器架構。 [任何 CPU]**** 不受支援。 在方案總管中，按一下上方的 [方案]，將處理器架構下拉式方塊變更為您想測試的其中一項支援的設定。

    ![][13]

5. 在方案總管中，開啟共用專案中的 MainPage.cs 檔案。 在檔案頂端取消註解下列 using 陳述式：

        using Microsoft.WindowsAzure.MobileServices.SQLiteStore;  // offline sync
        using Microsoft.WindowsAzure.MobileServices.Sync;         // offline sync

6. 在 MainPage.cs 中，註解的定義 `todoTable` 並取消註解下列一行中的呼叫 `Mobileservicesclient.getsynctable`:

        //private IMobileServiceTable<TodoItem> todoTable = App.MobileService.GetTable<TodoItem>();
        private IMobileServiceSyncTable<TodoItem> todoTable = App.MobileService.GetSyncTable<TodoItem>(); // offline sync

7. 在 MainPage.cs 中標示為的區域 `離線同步處理`, ，取消註解方法 `InitLocalStoreAsync` 和 `SyncAsync`。 此方法 `InitLocalStoreAsync` 初始化與 SQLite 存放區的用戶端同步處理內容。

     private async Task InitLocalStoreAsync()
     {
         if (!App.MobileService.SyncContext.IsInitialized)
         {
             var store = new MobileServiceSQLiteStore("localstore.db");
             store.DefineTable<TodoItem>();
             await App.MobileService.SyncContext.InitializeAsync(store);
         }
    
         await SyncAsync();
     }
    
     private async Task SyncAsync()
     {
         await App.MobileService.SyncContext.PushAsync();
         await todoTable.PullAsync("todoItems", todoTable.CreateQuery());
     }

8. 在 `OnNavigatedTo` 事件處理常式中，取消註解來呼叫 `InitLocalStoreAsync`:

        protected override async void OnNavigatedTo(NavigationEventArgs e)
        {
            await InitLocalStoreAsync(); // offline sync
            await RefreshTodoItems();
        }

9. 取消註解 3 呼叫 `SyncAsync` 方法中 `InsertTodoItem`, ，`UpdateCheckedTodoItem`, ，和 `ButtonRefresh_Click`:

     private async Task InsertTodoItem(TodoItem todoItem)
     {
         await todoTable.InsertAsync(todoItem);
         items.Add(todoItem);
    
         await SyncAsync(); // offline sync
     }
    
     private async Task UpdateCheckedTodoItem(TodoItem item)
     {
         await todoTable.UpdateAsync(item);
         items.Remove(item);
         ListItems.Focus(Windows.UI.Xaml.FocusState.Unfocused);
    
         await SyncAsync(); // offline sync
     }
    
     private async void ButtonRefresh_Click(object sender, RoutedEventArgs e)
     {
         ButtonRefresh.IsEnabled = false;
    
         await SyncAsync(); // offline sync
         await RefreshTodoItems();
    
         ButtonRefresh.IsEnabled = true;
     }

10. 新增例外狀況處理常式中的 `SyncAsync` 方法:

    private async Task SyncAsync()
    {
        String errorString = null;
    
        try
        {
            await App.MobileService.SyncContext.PushAsync();
            await todoTable.PullAsync("todoItems", todoTable.CreateQuery()); // first param is query ID, used for incremental sync
        }
    
        catch (MobileServicePushFailedException ex)
        {
            errorString = "Push failed because of sync errors: " +
              ex.PushResult.Errors.Count + " errors, message: " + ex.Message;
        }
        catch (Exception ex)
        {
            errorString = "Pull failed: " + ex.Message +
              "\n\nIf you are still in an offline scenario, " +
              "you can try your Pull again when connected with your Mobile Serice.";
        }
    
        if (errorString != null)
        {
            MessageDialog d = new MessageDialog(errorString);
            await d.ShowAsync();
        }
    }

在此範例中，我們擷取遠端中的所有記錄 `todoTable`, ，但是它也可以傳遞查詢來篩選記錄。 第一個參數 `PullAsync` 用於增量同步處理，會使用的查詢識別碼 `UpdatedAt` 時間戳記取得自從上次同步後修改過的記錄。 對您應用程式中的每個邏輯查詢而言，查詢識別碼應該是唯一的描述性字串。 若選擇不要增量同步處理，傳遞 `null` 做為查詢識別碼。 這會擷取每個提取作業的所有記錄，而可能降低效能。
>[AZURE.NOTE] * 若要移除記錄裝置本機存放區中已刪除的行動服務資料庫中，您應該啟用 [虛刪除]。 否則，您的應用程式應定期呼叫 `IMobileServiceSyncTable.PurgeAsync()` 才能清除本機存放區。

請注意， `MobileServicePushFailedException` 推送和提取作業可能會發生。 它可能發生於提取，因為提取作業會在內部執行推入，以確定所有資料表及任何關聯性都一致。 下一個教學課程，[處理行動服務的離線支援衝突]，將說明如何處理這些同步處理相關的例外狀況。

11. 在 Visual Studio 中按 **F5** 鍵，以重新建置並執行此應用程式。 應用程式的行為會與離線同步變更之前相同，因為它對插入、更新和重新整理作業執行了同步處理作業。

## <a name="update-sync"></a>更新應用程式的同步處理行為

在本節中，您將修改應用程式，使其不會對插入和更新作業執行同步處理，但僅限於按下 [重新整理]**** 按鈕時。 然後，您將中斷行動服務的應用程式連線，以模擬離線狀態。 當您新增資料項目時，這些項目會存放在本機存放區中，但不會同步處理至行動服務。

1. 開啟共用專案中的 MainPage.cs。 編輯方法 `InsertTodoItem` 和 `UpdateCheckedTodoItem` 註解化對 `SyncAsync`。

2. 編輯共用專案中的 App.xaml.cs。 註解化 **MobileServiceClient** 的初始化，並新增使用無效行動服務 URL 的以下幾行：

         public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://your-mobile-service.azure-mobile.xxx/",
            "AppKey"
        );

3. 在 `InitLocalStoreAsync()`, ，標記為註解來呼叫 `SyncAsync()`, ，以便在啟動應用程式不會執行同步處理。

4. 按 **F5** 以建置並執行應用程式。 輸入一些新的 todo 項目，然後為每個項目按一下 [儲存]****。 新的 todo 項目在可推送至行動服務之前，都只會存留在本機存放區中。 用戶端應用程式的行為，會如同它已連接到支援所有建立、讀取、更新、刪除 (CRUD) 作業的行動服務。

5. 關閉應用程式並重新加以開啟，以驗證您所建立的新項目持續存留於本機存放區中。

## <a name="update-online-app"></a>更新應用程式以重新連接您的行動服務

在本節中，您會將應用程式重新連接至行動服務。 您將藉此模擬應用程式在行動服務中從離線狀態恢復為線上狀態的情境。 當您按 [重新整理] 按鈕時，資料會同步處理至您的行動服務。

1. 開啟共用專案中的 App.xaml.cs。 取消註解您先前初始化的 `MobileServiceClient` 以重新新增正確的行動服務 URL 和應用程式金鑰。

2. 按 **F5** 鍵，以重新建置與執行應用程式。 請注意，雖然應用程式此時已連接到行動服務，但資料看起來仍會與離線狀態下相同。 這是因為此應用程式一律會使用指向本機存放區的 `IMobileServiceSyncTable`。

3. 登入 [Azure 傳統入口網站]，並查看您的行動服務的資料庫。 如果您的行動服務使用 JavaScript 後端，則您可以從行動服務的 [資料]**** 索引標籤瀏覽資料。

    如果您將 .NET 後端用於行動服務，請在 Visual Studio 中移至 [伺服器總管]**** -> [Azure]**** -> [SQL 資料庫]****。 在資料庫上按一下滑鼠右鍵，並選取 [在 SQL Server 物件總管中開啟]****。

    請注意，資料並未與資料庫和本機存放區進行同步處理。

    ![][6]

4. 在應用程式中，按 [重新整理]**** 按鈕。 這會導致應用程式呼叫 `PushAsync` 和 `PullAsync`。 此推送作業會將本機存放區項目傳送至行動服務，然後從行動服務中擷取新的資料。

    推送作業會從 `MobileServiceClient.SyncContext` 執行 (而非 `IMobileServicesSyncTable`)，並推送該同步內容所有相關資料表的變更。 這是為了解說資料表之間有所關聯的情況。

    ![][7]

5. 在應用程式中，按一下幾個項目旁邊的核取方塊，以在本機存放區中完成它們。

    ![][8]

6. 推入 **重新整理** 按鈕，這樣會造成 `SyncAsync` 呼叫。 `SyncAsync` 呼叫推送和提取，但在此情況下我們可能已移除的呼叫 `PushAsync`。 這是因為**提取一律會先執行推送**。 這是為了確保本機存放區中的所有資料表和關聯性都保持一致。

    ![][10]


## 摘要

[AZURE.INCLUDE [mobile-services-offline-summary-csharp](../../includes/mobile-services-offline-summary-csharp.md)]

## 後續步驟

* [處理行動服務的離線支援衝突]

* [使用 ][soft delete]





[update the app to support offline features]: #enable-offline-app 
[update the sync behavior of the app]: #update-sync 
[update the app to reconnect your mobile service]: #update-online-app 
[next steps]: #next-steps 
[1]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-services-add-reference-sqlite-dialog.png 
[2]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-services-sqlitestore-nuget.png 
[6]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-data-browse.png 
[7]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-data-browse2.png 
[8]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-services-online-app-run2.png 
[10]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-data-browse3.png 
[11]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-services-add-wp81-reference-sqlite-dialog.png 
[12]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/new-synchandler-class.png 
[13]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/cpu-architecture.png 
[handling conflicts with offline support for mobile services]: mobile-services-windows-store-dotnet-handling-conflicts-offline-data.md 
[todolist offline sample]: http://go.microsoft.com/fwlink/?LinkId=394777 
[get started with mobile services]: /develop/mobile/tutorials/get-started/#create-new-service 
[getting started]: ../mobile-services-dotnet-backend-windows-phone-get-started.md 
[get started with mobile services]: ../mobile-services-windows-store-get-started.md 
[sqlite for windows 8.1]: http://go.microsoft.com/fwlink/?LinkId=394776 
[sqlite for windows phone 8.1]: http://go.microsoft.com/fwlink/?LinkId=397953 
[soft delete]: mobile-services-using-soft-delete.md 
[mobile services sdk nuget]: http://www.nuget.org/packages/WindowsAzure.MobileServices/1.3.0 
[sqlite store nuget]: http://www.nuget.org/packages/WindowsAzure.MobileServices.SQLiteStore/1.0.0 
[azure classic portal]: https://manage.windowsazure.com 

