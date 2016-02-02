<properties
    pageTitle="啟用 Azure 行動應用程式 (Xamarin iOS) 的離線同步處理"
    description="了解如何在 Xamarin iOS 應用程式中使用應用程式服務行動應用程式快取和同步離線資料"
    documentationCenter="xamarin"
    authors="wesmc7777"
    manager="dwrede"
    editor=""
    services="app-service\mobile"/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin-ios"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="12/02/2015"
    ms.author="wesmc"/>


# 啟用 Xamarin iOS 行動應用程式的離線同步處理

[AZURE.INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]
&nbsp;  
[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

## 概觀

此教學課程介紹適用於 Xamarin.iOS 之 Azure 行動應用程式的離線同步處理功能。 離線同步處理可讓使用者與行動應用程式進行互動--檢視、新增或修改資料--即使沒有網路連線進也可行。 變更會儲存在本機資料庫中︰裝置上線後，這些變更就會與遠端服務進行同步處理。

在本教學課程中，您將更新教學課程以支援 Azure 行動應用程式的離線功能的 [建立 Xamarin iOS 應用程式] 的 Xamarin.iOS 應用程式的專案。 如果您不要使用下載的快速入門伺服器專案，必須將資料存取擴充套件新增至您的專案。 如需伺服器擴充功能套件的詳細資訊，請參閱 [Azure 行動應用程式使用.NET 後端伺服器 SDK](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)。

若要深入了解離線同步功能，請參閱主題 [離線資料同步處理在 Azure 行動應用程式]。

## 需求

本教學課程需要下列各項：

* Visual Studio 2013
* Visual Studio 的 [Xamarin 擴充功能] **或** [Xamarin Studio] 在 OS X 上
* 完成本教學課程 [建立 Xamarin iOS 應用程式]。 本教學課程使用該教學課程中涵蓋的已完成應用程式。

## 檢閱用戶端同步處理程式碼

當您已經完成本教學課程 [建立 Xamarin iOS 應用程式] 時，您下載 Xamarin 用戶端專案包含支援使用本機的 SQLite 資料庫的離線同步處理的程式碼。 以下是已經包含在教學課程程式碼中之內容的簡要概觀。 此功能的概觀，請參閱 [離線 Azure 行動應用程式中的資料同步]。

* 必須先初始化本機存放區，才可以執行資料表作業。 本機存放區資料庫已初始化時 `QSTodoListViewController.ViewDidLoad()` 執行 `QSTodoService.InitializeStoreAsync()`。 這會建立新本機 SQLite 資料庫使用 `MobileServiceSQLiteStore` Azure 行動應用程式用戶端 SDK 所提供的類別。

  `DefineTable` 方法比對的欄位中提供的型別，在本機存放區中建立資料表 `ToDoItem` 在此情況下。 該類型不必包含遠端資料庫中的所有資料行。 可以只儲存資料行的子集。

      // QSTodoService.cs
    
      public async Task InitializeStoreAsync()
      {
          var store = new MobileServiceSQLiteStore(localDbPath);
          store.DefineTable<ToDoItem>();
    
          // Uses the default conflict handler, which fails on conflict
          await client.SyncContext.InitializeAsync(store);
      }

* `TodoTable` 成員 `QSTodoService` 的 `IMobileServiceSyncTable` 型別而非 `IMobileServiceTable`。 這會將所有建立、讀取、更新和刪除 (CRUD) 資料表作業導向至本機存放區資料庫。

  您可以決定當這些變更會推到 Azure 行動應用程式後端呼叫 `imobileservicesynccontext.pushasync ()` 用戶端連接使用的同步處理內容。 在同步處理內容可協助追蹤，並將變更推入用戶端應用程式有修改時的所有資料表中保留資料表的關聯性 `PushAsync` 呼叫。

  提供程式碼會呼叫 `QSTodoService.SyncAsync()` 要同步處理更新 todoitem 清單，或加入或已完成的 todoitem。 因此，它會在每次本機變更執行同步處理內容推送和同步處理資料表提取之後同步處理。 不過，請務必了解，如果針對具有內容追蹤之擱置中本機更新的資料表執行提取，該提取作業將會先自動觸發內容推送。 因此在這些情況下 (重新整理、 加入和完成項目)，您可以省略明確 `PushAsync` 呼叫。 它是多餘的。

  在提供的程式碼，所有記錄遠端中 `TodoItem` 會查詢資料表，但也可以藉由傳遞查詢識別碼篩選記錄，並查詢以 `PushAsync`。 如需詳細資訊，請參閱章節 *增量同步處理* [離線資料同步處理在 Azure 行動應用程式] 中。


      // QSTodoService.cs
    
      public async Task SyncAsync()
      {
          try
          {
              await client.SyncContext.PushAsync();
              await todoTable.PullAsync("allTodoItems", todoTable.CreateQuery()); // query ID is used for incremental sync
          }
    
          catch (MobileServiceInvalidOperationException e)
          {
              Console.Error.WriteLine(@"Sync Failed: {0}", e.Message);
          }
      }



## 執行用戶端應用程式

請執行用戶端應用程式至少一次，以填入本機存放區資料庫。 在下一節中，您將會模擬一個離線案例，並在應用程式離線時修改本機存放區中的資料。


## 更新用戶端應用程式的同步處理行為

在本節中，您將透過使用無效的後端應用程式 URL，修改用戶端專案來模擬離線案例。 當您新增或變更資料項目時，這些變更會存放在本機存放區，但不會同步處理到後端資料存放區，直到重新建立連線為止。

1. 在頂端 `QSTodoService.cs`, 的初始化變更 `applicationURL` 以指向無效的 URL:

        const string applicationURL = @"https://your-service.azurewebsites.xxx/"; 

2. 加入額外 `攔截` 的 `例外狀況` 類別 `QSTodoService.SyncAsync` ，會將例外狀況訊息寫入主控台。

        public async Task SyncAsync()
        {
            try
            {
                await client.SyncContext.PushAsync();
                await todoTable.PullAsync("allTodoItems", todoTable.CreateQuery()); // query ID is used for incremental sync
            }
            catch (MobileServiceInvalidOperationException e)
            {
                Console.Error.WriteLine(@"Sync Failed: {0}", e.Message);
            }
            catch (Exception ex)
            {
                Console.Error.WriteLine(@"Exception: {0}", ex.Message);
            }
        }

3. 建置並執行用戶端應用程式。 新增一些新的 todo 項目，並注意主控台中針對每次嘗試同步處理行動應用程式後端時所記錄的例外狀況。 這些新的項目在可推送至行動後端之前，都只會存留在本機存放區中。 用戶端應用程式的行為，會如同它已連接到支援所有建立、讀取、更新、刪除 (CRUD) 作業的後端。

4. 關閉應用程式並重新加以開啟，以驗證您所建立的新項目持續存留於本機存放區中。

5. (選擇性) 使用 Visual Studio 檢視您的 Azure SQL Database 資料表，以查看後端資料庫中的資料並無變更。

    在 Visual Studio 中，開啟 [伺服器總管]****。 瀏覽至 **Azure**->**SQL Database** 中您的資料庫。 在資料庫上按一下滑鼠右鍵，並選取 [在 SQL Server 物件總管中開啟]****。 現在您可以瀏覽至您的 SQL Database 資料表和其內容。

6. (選擇性)使用 Fiddler 或 Postman 等其他工具來查詢您的行動後端，在表單中使用 GET 查詢 `https://your-mobile-app-backend-name.azurewebsites.net/tables/TodoItem`。

## 更新用戶端應用程式，重新連接您的行動後端

在本節中，您會將應用程式重新連接到行動後端，而模擬回到線上狀態的應用程式。 當您執行重新整理動作時，資料將會同步處理至您的行動後端中。

1. 開啟 `QSTodoService.cs`。 更正 `applicationURL` 和 `gatewayURL` 以指向正確的 Url。

2. 重建並執行用戶端應用程式。 應用程式嘗試於啟動後與 Azure 行動應用程式後端同步處理。 請確認偵錯主控台沒有記錄任何例外狀況。

3. (選擇性) 使用 SQL Server 物件總管或 REST 工具 (例如 Fiddler) 檢視已更新的資料。 請注意，Azure 行動應用程式後端資料庫與本機存放區之間的資料已同步處理。

    請注意，資料庫與本機存放區之間的資料已同步處理，並包含應用程式中斷連接時您所新增的項目。

## 其他資源

* [Azure 行動應用程式中的離線資料同步]

* [雲端報導: Azure 行動服務中的離線同步處理] \ (附註: 的視訊會在行動服務，但是離線同步處理的運作方式類似的方式，在 Azure 行動應用程式)







[create a xamarin ios app]: ../app-service-mobile-xamarin-ios-get-started.md 
[offline data sync in azure mobile apps]: ../app-service-mobile-offline-data-sync.md 
[how to use the xamarin component client for azure mobile services]: ../partner-xamarin-mobile-services-how-to-use-client-library.md 
[xamarin studio]: http://xamarin.com/download 
[xamarin extension]: http://xamarin.com/visual-studio 
[cloud cover: offline sync in azure mobile services]: http://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri 

