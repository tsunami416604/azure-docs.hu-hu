<properties
    pageTitle="啟用 Azure 行動應用程式的離線同步處理 (iOS)"
    description="了解如何在 iOS 應用程式中使用應用程式服務行動應用程式快取和同步離線資料"
    documentationCenter="ios"
    authors="krisragh"
    manager="dwrede"
    editor=""
    services="app-service\mobile"/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-ios"
    ms.devlang="objective-c"
    ms.topic="article"
    ms.date="12/01/2015"
    ms.author="krisragh"/>

# 啟用 iOS 行動應用程式的離線同步處理

[AZURE.INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]
&nbsp;  
[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

## 概觀

本教學課程說明 iOS 之 Azure Mobile Apps 的離線同步功能。 離線同步處理可讓使用者與行動應用程式進行互動 - 檢視、新增或修改資料 - 即使沒有網路連線也可行。 變更會儲存在本機資料庫中︰裝置上線後，這些變更就會與遠端後端進行同步處理。

如果這是您第一次使用 Azure 行動應用程式，您應該先完成本教學課程 [Create an iOS App]。 如果您不要使用下載的快速入門伺服器專案，必須將資料存取擴充套件新增至您的專案。 如需伺服器擴充功能套件的詳細資訊，請參閱 [Azure 行動應用程式使用.NET 後端伺服器 SDK](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)。 

若要深入了解離線同步功能，請參閱本主題 [Offline Data Sync in Azure Mobile Apps]。

## <a name="review-sync"></a>檢閱用戶端同步程式碼 

本教學課程中下載用戶端專案 [Create an iOS App] 已包含程式碼支援使用本機核心資料為基礎的資料庫的離線同步處理。 這一節是已包含在教學課程程式碼中的內容摘要。 此功能的概觀，請參閱 [Offline Data Sync in Azure Mobile Apps]。

Azure Mobile Apps 的離線資料同步功能可讓使用者在無法存取網路時，仍可與本機資料庫互動。 若要在您的應用程式中使用這些功能，您可初始化 `MSClient` 的同步處理內容以及參考本機存放區。 接著，請透過 `MSSyncTable` 介面參考您的資料表。

1. 在 **QSTodoService.m**, ，請注意成員的型別 `syncTable` 是 `MSSyncTable`。 離線同步處理會使用此同步處理資料表介面，而不是 `MSTable`。 使用同步處理資料表時，所有作業都會移至本機存放區，且只有在執行明確的發送和提取作業時才會與遠端後端同步處理。

    若要取得同步處理資料表的參考，請使用 `syncTableWithName` 方法。 若要移除離線同步處理功能，請改用 `tableWithName`。

2. 必須先初始化本機存放區，才可以執行資料表作業。 這是 `QSTodoService.init` 方法中的相關程式碼：

        MSCoreDataStore *store = [[MSCoreDataStore alloc] initWithManagedObjectContext:context];

        self.client.syncContext = [[MSSyncContext alloc] initWithDelegate:nil dataSource:store callback:nil];

    這會建立一個本機存放區，其採用行動應用程式 SDK 中提供的 `MSCoreDataStore` 介面。 您可以實作 `MSSyncContextDataSource` 通訊協定，改為提供不同的本機存放區。

    `initWithDelegate` 的第一個參數用來指定衝突處理常式。 由於我們已傳遞 `nil`，所以我們會取得預設衝突處理常式，但該處理常式在任何衝突時都會失敗。

    <!-- For details on how to implement a custom conflict handler, see the tutorial [Handling conflicts with offline support for Mobile Services]. -->

3. `pullData` 和 `syncData` 方法會執行實際同步處理作業︰`syncData` 會先推送新的變更，然後呼叫 `pullData` 以從遠端服務取得資料。

        -(void)syncData:(QSCompletionBlock)completion
        {
            // push all changes in the sync context, then pull new data
            [self.client.syncContext pushWithCompletion:^(NSError *error) {
                [self logErrorIfNotNil:error];
                [self pullData:completion];
            }];
        }

    然而，`pullData` 方法會取得符合查詢的新資料︰

        -(void)pullData:(QSCompletionBlock)completion
        {
            MSQuery *query = [self.syncTable query];

            // Pulls data from the remote server into the local table.
            // We're pulling all items and filtering in the view
            // query ID is used for incremental sync
            [self.syncTable pullWithQuery:query queryId:@"allTodoItems" completion:^(NSError *error) {
                [self logErrorIfNotNil:error];

                // Let the caller know that we have finished
                if (completion != nil) {
                    dispatch_async(dispatch_get_main_queue(), completion);
                }
            }];
        }

    在 `syncData` 中，我們會先在同步處理內容上先呼叫 `pushWithCompletion`。 這個方法所屬 `MSSyncContext` （而非同步處理資料表本身） 因為它會在所有資料表之間推送變更。 只有以某種方式在本機上修改過的記錄 (透過 CUD 作業)，才會傳送至伺服器。 接著會呼叫 `pullData` 協助程式，該程式會呼叫 `MSSyncTable.pullWithQuery` 來擷取遠端資料並存放在本機資料庫中。

    請注意，在此範例中，推送作業並非絕對必要。 如果同步處理內容中正在進行推送作業的資料表有任何變更擱置，則提取一律會先發出推送。 不過，如果您有一個以上的同步處理資料表，最好能明確呼叫推送，以確保所有的相關資料表都能一致。

    `pullWithQuery` 方法可讓您指定查詢，以篩選您想要擷取的記錄。 在此範例中，查詢只會擷取遠端 `TodoItem` 資料表中的所有記錄。

    第二個參數 `pullWithQuery` 所用的查詢識別碼 *增量同步處理*。 增量同步處理會使用記錄的 `UpdatedAt` 時間戳記 (在本機存放區中稱為 `updatedAt`)，僅擷取自上次同步處理後修改的記錄。 對您應用程式中的每個邏輯查詢而言，查詢識別碼應該是唯一的描述性字串。 若選擇不要增量同步處理，請傳遞 `nil` 做為查詢識別碼。 請注意這可能是潛在效率不佳，因為它會擷取每項提取作業的所有記錄。

    <!--     >[AZURE.NOTE] To remove records from the device local store when they have been deleted in your mobile service database, you should enable [Soft Delete]. Otherwise, your app should periodically call `MSSyncTable.purgeWithQuery` to purge the local store.
 -->

5. 在 `QSTodoService` 類別中，`syncData` 方法會在修改資料的作業 (`addItem` 和 `completeItem`) 之後呼叫。 此方法也會從 `QSTodoListViewController.refresh` 呼叫，以便使用者每次執行重新整理動作時都能取得最新的資料。 應用程式也會在啟動時執行同步處理，因為 `QSTodoListViewController.init` 會呼叫 `refresh`。

    因為每次修改資料時都會呼叫 `syncData`，所以此應用程式假設使用者每次在編輯資料時都處於線上狀態。 在另一節中，我們將會更新應用程式，讓使用者即使是離線狀態也能進行編輯。

## <a name="review-core-data"></a>檢閱核心資料模型

在使用「核心資料離線」存放區時，您需要在資料模型中定義特定資料表和欄位。 範例應用程式已經包含具有正確格式的資料模型。 本這一節中，我們將逐步介紹這些資料表以及其使用方式。

- 開啟 **QSDataModel.xcdatamodeld**。 已定義四個資料表--其中三個由 SDK 使用，而一個適用於 todo 項目本身：
      * MS_TableOperations ︰ 用於追蹤需要與伺服器同步處理的項目
      * MS_TableOperationErrors ︰ 用於追蹤離線同步處理期間發生任何錯誤
      * Ms_tableconfig ︰ 用來追蹤最後一次更新所有提取作業最後一次同步處理作業
      * TodoItem ︰ 用來儲存 todo 項目。 系統資料行 **createdAt**, ，**updatedAt**, ，和 **版本** 為選擇性系統屬性。

>[AZURE.NOTE] Azure 行動應用程式 SDK 會保留資料行名稱以"**'**」。 您不得在系統資料行以外的任何項目上使用此前置詞，否則會在使用遠端後端時修改您的資料行名稱。

- 使用離線同步功能時，您必須先定義系統資料表，如下所示。

    ### 系統資料表

    **MS_TableOperations**

    ![][defining-core-data-tableoperations-entity]

  	| 屬性  |    類型     |
  	|----------- |   ------    |
  	| id         | 整數 64  |
  	| itemId     | String      |
  	| properties | 二進位資料 |
  	| 資料表      | String      |
  	| tableKind  | 整數 16  |

    <br>**MS_TableOperationErrors**

    ![][defining-core-data-tableoperationerrors-entity]

  	| 屬性  |    類型     |
  	|----------- |   ------    |
  	| id         | String      |
  	| operationId | 整數 64 |
  	| properties | 二進位資料 |
  	| tableKind  | 整數 16  |

    <br>**MS_TableConfig**

    ![][defining-core-data-tableconfig-entity]

  	| 屬性  |    類型     |
  	|----------- |   ------    |
  	| id         | String      |
  	| 索引鍵        | String      |
  	| keyType    | 整數 64  |
  	| 資料表      | String      |
  	| value      | String      |

    ### 資料表

    **TodoItem**

  	| 屬性    |  類型   | 注意                                                   |
  	|-----------   |  ------ | -------------------------------------------------------|
  	| id           | 字串 (標示為必要)  | 遠端存放區中的主索引鍵                            |
  	| 完成     | Boolean | todo 項目欄位                                        |
  	| 文字         | String  | todo 項目欄位                                        |
  	| 建立時間 | 日期    | (選擇性) 對應至 createdAt 系統屬性         |
  	| 更新時間 | 日期    | (選擇性) 對應至 updatedAt 系統屬性         |
  	| 版本   | String  | (選擇性步驟) 用來偵測衝突，對應至版本 |


## <a name="setup-sync"></a>變更應用程式的同步處理行為

在本節中，您將修改應用程式，使其不會在應用程式啟動時，或插入及更新項目時同步處理，但只會在執行重新整理動作按鈕時同步處理。

1. 在 **QSTodoListViewController.m**, ，變更 **viewDidLoad** 方法來移除對呼叫 `[self refresh]` 方法的結尾。 現在，資料不會在應用程式啟動時與伺服器進行同步處理，但是成為本機存放區的內容。

2. 在 **QSTodoService.m**, ，修改的定義 `addItem` 使其不會在插入項目後同步處理。 移除 `self syncData` 區塊並以下列項目取代：

            if (completion != nil) {
                dispatch_async(dispatch_get_main_queue(), completion);
            }

3. 如上所述修改 `completeItem` 的定義 ；移除 `self syncData` 的區塊並以下列項目取代：

            if (completion != nil) {
                dispatch_async(dispatch_get_main_queue(), completion);
            }

## <a name="test-app"></a>測試應用程式

在本節中，將連接至無效的 URL，以模擬離線情況。 當您新增資料項目時，這些項目會存放在本機核心資料存放區，但不會同步到行動後端。

1. 變更中的行動應用程式 URL **QSTodoService.m** 無效的 URL，並再次執行應用程式 ︰

        self.client = [MSClient clientWithApplicationURLString:@"https://sitename.azurewebsites.net.fail"];

2. 新增 todo 項目或完成某些項目。 結束模擬器 (或強制關閉應用程式)，然後重新啟動。 確認您的變更已保存下來。

3. 檢視遠端 TodoItem 資料表的內容：

    + Node.js 後端，移至 [Azure 入口網站](https://portal.azure.com/), ，在您的行動應用程式後端按一下 **簡單資料表** > **TodoItem** 若要檢視的內容 `TodoItem` 資料表。
    + 若為 .NET 後端，請使用 SQL 工具 (例如 SQL Server Management Studio) 或 REST 用戶端 (例如 Fiddler 或 Postman) 檢視資料表內容。

    請確認新的項目有 *不* 已同步處理到伺服器 ︰

4. 變更 URL 回正確 **QSTodoService.m** ，然後重新執行應用程式。 將項目清單往下拉，執行重新整理動作。 您會看到進度微調按鈕和「同步中...」文字。

5. 再次檢視 TodoItem 資料。 新的和變更的 TodoItems 現在應該會出現。

## 摘要

為了支援離線同步處理功能，我們使用了 `MSSyncTable` 介面，並對本機存放區初始化 `MSClient.syncContext`。 在此案例中，本機存放區是以核心資料為基礎的資料庫。

當使用核心資料本機存放區，您必須定義數個資料表與 [更正系統屬性](#review-core-data)。

正常情況下，在 Azure Mobile Apps 的 CRUD 作業執行時，應用程式會如同仍處於連線狀態，但所有的作業都會對本機存放區執行。

當我們要同步處理本機存放區與伺服器時，我們使用 `MSSyncTable.pullWithQuery` 和 `MSClient.syncContext.pushWithCompletion` 方法。

*  為了將變更推送至伺服器，我們呼叫了 `pushWithCompletion`。 此方法是 `MSSyncContext` 的成員之一 (而不是同步資料表)，因為它會在所有資料表之間推送變更。

    只有以某種方式在本機上修改過的記錄 (透過 CUD 作業)，才會傳送至伺服器。

* 為了將資料從伺服器上的資料表提取至應用程式，我們呼叫了 `MSSyncTable.pullWithQuery`。

    提取一律會先發出推送動作。 這是為了確保本機存放區中的所有資料表和關聯性都保持一致。

    請注意，`pullWithQuery` 可藉由自訂 `query` 參數，用來篩選存放在用戶端上的資料。

* 若要啟用增量同步處理，請將查詢識別碼傳遞至 `pullWithQuery`。 查詢識別碼用來儲存上次提取作業所產生的上次更新時間戳記。 對您應用程式中的每個邏輯查詢而言，查詢識別碼應該是唯一的描述性字串。 如果查詢有一個參數，則相同的參數值必須屬於查詢識別碼的一部分。

    如果您想選擇不要增量同步處理，則傳遞 `nil` 做為查詢識別碼。 在此情況下，每次呼叫 `pullWithQuery` 時都會擷取所有的記錄，這可能會沒有效率。

<!-- * To remove records from the device local store when they have been deleted in your mobile service database, you should enable [Soft Delete]. Otherwise, your app should periodically call `MSSyncTable.purgeWithQuery` to remove records from the local database, in case they have been deleted in the remote service.
 -->

## 其他資源

* [Azure 行動應用程式中的離線資料同步處理]

* [Cloud Cover: Offline Sync in Azure Mobile Services] \ (附註 ︰ 的視訊會在行動服務，但是離線同步處理的運作方式類似的方式，在 Azure 行動應用程式)

<!-- URLs. -->


[Create an iOS App]: ../app-service-mobile-ios-get-started.md
[Offline Data Sync in Azure Mobile Apps]: ../app-service-mobile-offline-data-sync.md

[defining-core-data-tableoperationerrors-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-tableoperationerrors-entity.png
[defining-core-data-tableoperations-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-tableoperations-entity.png
[defining-core-data-tableconfig-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-tableconfig-entity.png
[defining-core-data-todoitem-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-todoitem-entity.png

[Cloud Cover: Offline Sync in Azure Mobile Services]: http://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri
[Azure Friday: Offline-enabled apps in Azure Mobile Services]: http://azure.microsoft.com/en-us/documentation/videos/azure-mobile-services-offline-enabled-apps-with-donna-malayeri/
 


