<properties
    pageTitle="開始使用行動服務中的離線資料同步 (iOS) | Microsoft Azure"
    description="了解如何使用 Azure 行動服務快取和同步 iOS 應用程式中的離線資料"
    documentationCenter="ios"
    authors="krisragh"
    manager="dwrede"
    editor=""
    services="mobile-services"/>

<tags
    ms.service="mobile-services"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-ios"
    ms.devlang="objective-c"
    ms.topic="article"
    ms.date="10/01/2015"
    ms.author="krisragh;donnam"/>


# 開始使用行動服務中的離線資料同步

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


[AZURE.INCLUDE [mobile-services-selector-offline](../../includes/mobile-services-selector-offline.md)]

使用離線同步可讓您即便在沒有網路連線的情況下，也能檢視、新增或修改行動應用程式中的資料。 在本教學課程中，您將會了解應用程式如何在本機離線資料庫中自動儲存變更，並且在回復連線時同步這些變更。

離線同步有幾個優點：

* 透過在裝置上本機快取伺服器資料，改善應用程式回應性
* 讓應用程式能夠在網路連線中斷後恢復
* 即使只有些微或沒有連線能力，也能讓您建立和修改資料
* 跨多個裝置同步資料
* 在兩個裝置修改同一筆記錄時偵測到衝突

> [AZURE.NOTE] 若要完成此教學課程，您需要 Azure 帳戶。 如果您沒有帳戶，您可以註冊 Azure 試用版並取得 [免費的行動服務，即使在試用期結束之後可繼續使用](http://azure.microsoft.com/pricing/details/mobile-services/)。 如需詳細資訊，請參閱 [Azure 免費試用] (http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AE564AB28 目標 ="_blank")。

本教學課程根據 [行動服務快速入門教學課程]，您必須先完成。 我們先來檢閱在＜快速入門＞中與離線同步相關的程式碼。

## <a name="review-sync"></a>檢閱行動服務同步處理程式碼

Azure 行動服務離線同步處理可讓使用者在無法存取網路時，仍可與本機資料庫互動。 若要使用這些功能在您的應用程式中，您會初始化同步處理內容 `MSClient` 以及參考本機存放區。 然後參考您的資料表，透過 `MSSyncTable` 介面。

* 在 **QSTodoService.m**, ，請注意成員的型別 `syncTable` 是 `MSSyncTable`。 離線同步處理會使用此而不是 `MSTable`。 使用同步處理資料表時，所有作業都會移至本機存放區，而且只會與具有明確推送和提取作業的遠端服務同步處理。

```
        @property (nonatomic, strong)   MSSyncTable *syncTable;
```

若要取得同步處理資料表的參考，請使用方法 `syncTableWithName`。 若要移除離線同步處理功能，請使用 `tableWithName` 改。

* 在 **QSTodoService.m**, ，本機存放區初始化，才執行資料表作業中 `QSTodoService.init`:

```
        MSCoreDataStore *store = [[MSCoreDataStore alloc] initWithManagedObjectContext:context];
        self.client.syncContext = [[MSSyncContext alloc] initWithDelegate:nil dataSource:store callback:nil];
```

這會建立本機存放區使用 `MSCoreDataStore` 介面。 您可以藉由實作提供不同的本機存放區 `MSSyncContextDataSource` 通訊協定。

第一個參數 `initWithDelegate` 指定衝突處理常式，但因為我們已經傳遞 `nil`, ，我們在任何衝突取得預設衝突處理常式就會失敗。 如需如何實作自訂衝突處理常式的詳細資訊，請參閱 [處理衝突的離線支援行動電話服務]。

* 在 **QSTodoService.m**, ，`syncData` 先推送新的變更，然後再呼叫 `pullData` 從遠端服務取得資料。 在 `syncData`, ，我們先呼叫 `pushWithCompletion` 同步處理內容上。 這個方法所屬 `MSSyncContext` (而非同步處理資料表本身) 因為它在所有資料表之間推送變更。 只有以某種方式在本機上修改過的記錄 (透過建立、更新或刪除作業)，才會傳送至伺服器。 在結尾處 `syncData`, ，我們呼叫 helper `pullData`。

在此範例中，推送作業並非絕對必要。 如果同步處理內容中正在進行推送作業的資料表有變更擱置，則提取一律會先發出推送。 不過，如果您有多個同步處理資料表，請明確呼叫推送，讓資料表之間都能一致。

```
      -(void)syncData:(QSCompletionBlock)completion
      {
          // push all changes in the sync context, then pull new data
          [self.client.syncContext pushWithCompletion:^(NSError *error) {
              [self logErrorIfNotNil:error];
              [self pullData:completion];
          }];
      }
```

* 接下來在 **QSTodoService.m**, ，`pullData` 取得符合查詢的新資料。 `pullData` 呼叫 `MSSyncTable.pullWithQuery` 來擷取遠端資料並儲存在本機。 `pullWithQuery` 也可讓您指定您想要擷取的查詢來篩選記錄。 在此範例中，查詢只會擷取遠端中的所有記錄 `TodoItem` 資料表。

第二個參數 `pullWithQuery` _incremental sync_ 的查詢識別碼。 增量同步處理擷取自上次同步處理，並使用記錄的修改過的記錄 `UpdatedAt` 時間戳記，稱為 `ms_updatedAt` 本機存放區中。 對您應用程式中的每個邏輯查詢而言，查詢識別碼是唯一的描述性字串。 若選擇不要增量同步處理，傳遞 `nil` 做為查詢識別碼。 這樣效率會不佳，因為它會擷取每個推送作業的所有記錄。

```
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
```


>[AZURE.NOTE] 若要刪除行動服務資料庫中，從裝置本機存放區移除記錄，請啟用 [虛刪除]。 否則，您的應用程式應定期呼叫 `MSSyncTable.purgeWithQuery` 才能清除本機存放區。


* 在 **QSTodoService.m**, ，方法 `addItem` 和 `completeItem` 叫用 `syncData` 之後修改資料。 在 **QSTodoListViewController.m**, ，方法 `重新整理` 也會叫用 `syncData` 以便在每次重新整理和啟動 UI 會顯示最新的資料 (`init` 呼叫 `重新整理`。)

因為應用程式會呼叫 `syncData` 每當您修改資料時，應用程式都會假設您在線上，每當您編輯應用程式中的資料。

## <a name="review-core-data"></a>檢閱核心資料模型

在使用「核心資料離線」存放區時，您需要在資料模型中定義特定資料表和欄位。 範例應用程式已經包含具有正確格式的資料模型。 在這一節中，我們會逐步介紹這些資料表以及其使用方式。

- 開啟 **QSDataModel.xcdatamodeld**。 已定義了四個資料表，其中三個由 SDK 使用，一個供 todo 項目本身使用：

      * MS_TableOperations: For tracking items to be synchronized with server
      * MS_TableOperationErrors: For tracking errors that happen during offline sync
      * MS_TableConfig: For tracking last updated time for last sync operation for all pull operations
      * TodoItem: For storing todo items. The system columns **ms_createdAt**, **ms_updatedAt**, and **ms_version** are optional system properties.


>[AZURE.NOTE] 行動服務 SDK 會保留資料行名稱開頭為"* *`ms_`* * 」。 請不要在系統資料行以外的項目使用此前置詞。 否則，您的資料行名稱會在使用遠端服務時被修改。

- 使用離線同步功能時，您必須先定義系統資料表，如下所示。

    # # # 系統資料表

    # # # MS_TableOperations

  | 屬性| 類型|
  |-------------- |   ------    |
  | 識別碼 (必要)| 整數 64|
  | itemId| String|
  | properties| 二進位資料|
  | 資料表| String|
  | tableKind| 整數 16|

    # # # MS_TableOperationErrors

  | 屬性| 類型|
  |-------------- | ----------  |
  | 識別碼 (必要)| String|
  | operationId| 整數 64|
  | properties| 二進位資料|
  | tableKind| 整數 16|

    # # # MS_TableConfig

  | 屬性| 類型|
  |-------------- | ----------  |
  | 識別碼 (必要)| String|
  | 索引鍵| String|
  | keyType| 整數 64|
  | 資料表| String|
  | value| String|

    # # # 資料表

    # # # TodoItem

  | 屬性| 類型| 注意|
  |-------------- |  ------ | -------------------------------------------------------|
  | 識別碼 (必要)| String| 遠端存放區中的主要金鑰|
  | 完成| Boolean| todo 項目欄位|
  | 文字| String| todo 項目欄位|
  | ms_createdAt| Date| (選擇性) 對應至 __createdAt 系統屬性|
  | ms_updatedAt| Date| (選擇性) 對應至 __updatedAt 系統屬性|
  | ms_version| String| (選擇性)，用來偵測衝突，對應至 __version|




## <a name="setup-sync"></a>變更應用程式的同步行為

本節中，您會修改應用程式，使它不會同步處理在應用程式啟動時，或插入及更新項目時，但只有在執行重新整理動作時。

* 在 **QSTodoListViewController.m**, ，變更 `viewDidLoad` 移除呼叫 `[自動重新整理]` 方法的結尾。 現在，資料不會在應用程式啟動時與伺服器進行同步處理，而只會在本機儲存。

* 在 **QSTodoService.m**, ，修改 `addItem` 使其不會在插入項目後同步處理。 移除 `本身 syncData` 區塊，並將它取代為下列:

```
        if (completion != nil) {
            dispatch_async(dispatch_get_main_queue(), completion);
        }
```

* 同樣地，再次在 **QSTodoService.m**, ，請在 `completeItem`, ，移除的區塊 `本身 syncData` 並取代為下列:

```
        if (completion != nil) {
            dispatch_async(dispatch_get_main_queue(), completion);
        }
```

## <a name="test-app"></a>測試應用程式

在本節中，您將會關閉模擬器的 Wi-Fi 以建立離線案例。 當您新增資料項目時，這些項目會存放在本機核心資料存放區，但不會同步到行動服務。

1. 關閉 Mac 上的網際網路連線。 只在 iOS 模擬器中關閉 WiFi 可能沒有作用，因為模擬器可能仍然會使用主機 Mac 的網際網路連線，因此關閉電腦本身的網際網路。 這會模擬離線案例。

2. 新增 todo 項目或完成某些項目。 結束模擬器 (或強制關閉應用程式)，然後重新啟動。 確認您的變更已保存下來。 請注意，資料項目仍然會顯示，因為它們都保留在本機核心資料存放區中。

3. 檢視遠端 TodoItem 資料表的內容。 請確認新項目_尚未_同步處理到伺服器。

   - 為 JavaScript 後端，移至 [Azure 傳統入口網站](http://manage.windowsazure.com), ，然後按一下 [資料] 索引標籤來檢視內容 `TodoItem` 資料表。
   - 若為 .NET 後端，使用 SQL 工具 (如 SQL Server Management Studio) 或 REST 用戶端 (如 Fiddler 或 Postman) 檢視資料表內容。

4. 開啟 iOS 模擬器的 Wi-Fi。 接著，拉下項目清單，以執行重新整理動作。 您會看到進度微調按鈕和「同步中...」文字。

5. 再次檢視 TodoItem 資料。 新的和變更的 TodoItems 現在應該會出現。

## 摘要

為了支援行動服務的離線功能，您使用 `MSSyncTable` 介面，並初始化 `MSClient.syncContext` 對本機存放區。 在此案例中，本機存放區是以核心資料為基礎的資料庫。

當使用核心資料本機存放區，您會定義具有數個資料表 [更正系統屬性 ][review the core data model]。 行動服務的正常作業工作一樣，應用程式仍然連接，但所有的作業都會對本機存放區。

在您使用以伺服器同步處理本機存放區， `MSSyncTable.pullWithQuery` 和 `MSClient.syncContext.pushWithCompletion`:

        * To push changes to the server, you called `pushWithCompletion`. This method is in `MSSyncContext` instead of the sync table because it will push changes across all tables. Only records that are modified in some way locally (through CUD operations) are be sent to the server.
    
        * To pull data from a table on the server to the app, you called `MSSyncTable.pullWithQuery`. A pull always issues a push first. This is to ensure all tables in the local store along with relationships remain consistent. `pullWithQuery` can also be used to filter the data that is stored on the client, by customizing the `query` parameter.

## 後續步驟

* [處理行動服務的離線支援衝突]

* [使用 ][soft delete]

## 其他資源

* [雲端報導: Azure 行動服務中的離線同步處理]

* [Azure Friday: Azure 行動服務中的離線應用程式] \ (附註: 示範適用於 Windows，但功能討論適用於所有平台)




[get the sample app]: #get-app 
[review the core data model]: #review-core-data 
[review the mobile services sync code]: #review-sync 
[change the sync behavior of the app]: #setup-sync 
[test the app]: #test-app 
[core-data-1]: ./media/mobile-services-ios-get-started-offline-data/core-data-1.png 
[core-data-2]: ./media/mobile-services-ios-get-started-offline-data/core-data-2.png 
[core-data-3]: ./media/mobile-services-ios-get-started-offline-data/core-data-3.png 
[defining-core-data-main-screen]: ./media/mobile-services-ios-get-started-offline-data/defining-core-data-main-screen.png 
[defining-core-data-model-editor]: ./media/mobile-services-ios-get-started-offline-data/defining-core-data-model-editor.png 
[defining-core-data-tableoperationerrors-entity]: ./media/mobile-services-ios-get-started-offline-data/defining-core-data-tableoperationerrors-entity.png 
[defining-core-data-tableoperations-entity]: ./media/mobile-services-ios-get-started-offline-data/defining-core-data-tableoperations-entity.png 
[defining-core-data-tableconfig-entity]: ./media/mobile-services-ios-get-started-offline-data/defining-core-data-tableconfig-entity.png 
[defining-core-data-todoitem-entity]: ./media/mobile-services-ios-get-started-offline-data/defining-core-data-todoitem-entity.png 
[update-framework-1]: ./media/mobile-services-ios-get-started-offline-data/update-framework-1.png 
[update-framework-2]: ./media/mobile-services-ios-get-started-offline-data/update-framework-2.png 
[core data model editor help]: https://developer.apple.com/library/mac/recipes/xcode_help-core_data_modeling_tool/Articles/about_cd_modeling_tool.html 
[creating an outlet connection]: https://developer.apple.com/library/mac/recipes/xcode_help-interface_builder/articles-connections_bindings/CreatingOutlet.html 
[build a user interface]: https://developer.apple.com/library/mac/documentation/ToolsLanguages/Conceptual/Xcode_Overview/Edit_User_Interfaces/edit_user_interface.html 
[adding a segue between scenes in a storyboard]: https://developer.apple.com/library/ios/recipes/xcode_help-IB_storyboard/chapters/StoryboardSegue.html#//apple_ref/doc/uid/TP40014225-CH25-SW1 
[adding a scene to a storyboard]: https://developer.apple.com/library/ios/recipes/xcode_help-IB_storyboard/chapters/StoryboardScene.html 
[core data]: https://developer.apple.com/library/ios/documentation/Cocoa/Conceptual/CoreData/cdProgrammingGuide.html 
[download the preview sdk here]: http://aka.ms/Gc6fex 
[how to use the mobile services client library for ios]: mobile-services-ios-how-to-use-client-library.md 
[offline ios sample]: https://github.com/Azure/mobile-services-samples/tree/master/TodoOffline/iOS/blog20140611 
[mobile services sample repository on github]: https://github.com/Azure/mobile-services-samples 
[get started with mobile services]: mobile-services-ios-get-started.md 
[handling conflicts with offline support for mobile services]: mobile-services-ios-handling-conflicts-offline-data.md 
[soft delete]: mobile-services-using-soft-delete.md 
[cloud cover: offline sync in azure mobile services]: http://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri 
[azure friday: offline-enabled apps in azure mobile services]: http://azure.microsoft.com/documentation/videos/azure-mobile-services-offline-enabled-apps-with-donna-malayeri/ 
[mobile services quick start tutorial]: mobile-services-ios-get-started.md 

