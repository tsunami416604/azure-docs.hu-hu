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

如果您不要使用下載的快速入門伺服器專案，必須將資料存取擴充套件新增至您的專案。



## 

這一節是已包含在教學課程程式碼中的內容摘要。

Azure Mobile Apps 的離線資料同步功能可讓使用者在無法存取網路時，仍可與本機資料庫互動。

1. 使用同步處理資料表時，所有作業都會移至本機存放區，且只有在執行明確的發送和提取作業時才會與遠端後端同步處理。

    

2. 必須先初始化本機存放區，才可以執行資料表作業。

        MSCoreDataStore *store = [[MSCoreDataStore alloc] initWithManagedObjectContext:context];
       
        self.client.syncContext = [[MSSyncContext alloc] initWithDelegate:nil dataSource:store callback:nil];

    

    


3. 

     -(void)syncData:(QSCompletionBlock)completion
     {
         // push all changes in the sync context, then pull new data
         [self.client.syncContext pushWithCompletion:^(NSError *error) {
             [self logErrorIfNotNil:error];
             [self pullData:completion];
         }];
     }

 

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

 只有以某種方式在本機上修改過的記錄 (透過 CUD 作業)，才會傳送至伺服器。

 請注意，在此範例中，推送作業並非絕對必要。 如果同步處理內容中正在進行推送作業的資料表有任何變更擱置，則提取一律會先發出推送。 不過，如果您有一個以上的同步處理資料表，最好能明確呼叫推送，以確保所有的相關資料表都能一致。

 

 對您應用程式中的每個邏輯查詢而言，查詢識別碼應該是唯一的描述性字串。  請注意這可能是潛在效率不佳，因為它會擷取每項提取作業的所有記錄。


5. 

    在另一節中，我們將會更新應用程式，讓使用者即使是離線狀態也能進行編輯。

## 

在使用「核心資料離線」存放區時，您需要在資料模型中定義特定資料表和欄位。 範例應用程式已經包含具有正確格式的資料模型。 本這一節中，我們將逐步介紹這些資料表以及其使用方式。

- 開啟 **QSDataModel.xcdatamodeld**。 已定義四個資料表--其中三個由 SDK 使用，而一個適用於 todo 項目本身：
      
      
      
      系統資料行 **createdAt**、**updatedAt** 和 **version** 為可選擇性使用的系統屬性。

>[AZURE.NOTE] Azure Mobile Apps SDK 會保留開頭為「**``**」的資料行名稱。 您不得在系統資料行以外的任何項目上使用此前置詞，否則會在使用遠端後端時修改您的資料行名稱。

- 使用離線同步功能時，您必須先定義系統資料表，如下所示。

    

    **MS_TableOperations**

    ![][defining-core-data-tableoperations-entity]

  | 屬性| 類型|
  |----------- |   ------    |
  | id| 整數 64|
  | itemId| String|
  | properties| 二進位資料|
  | 資料表| String|
  | tableKind| 整數 16|

    

    ![][defining-core-data-tableoperationerrors-entity]

  | 屬性| 類型|
  |----------- |   ------    |
  | id| String|
  | operationId| 整數 64|
  | properties| 二進位資料|
  | tableKind| 整數 16|

    

    ![][defining-core-data-tableconfig-entity]

  | 屬性| 類型|
  |----------- |   ------    |
  | id| String|
  | 索引鍵| String|
  | keyType| 整數 64|
  | 資料表| String|
  | value| String|

    

    **TodoItem**

  | 屬性| 類型| 注意|
  |-----------   |  ------ | -------------------------------------------------------|
  | id| 字串 (標示為必要)| 遠端存放區中的主索引鍵|
  | 完成| Boolean| todo 項目欄位|
  | 文字| String| todo 項目欄位|
  | 建立時間| 日期| (選擇性) 對應至 createdAt 系統屬性|
  | 更新時間| 日期| (選擇性) 對應至 updatedAt 系統屬性|
  | 版本| String| (選擇性) 用來偵測衝突，對應至版本|



## 

在本節中，您將修改應用程式，使其不會在應用程式啟動時，或插入及更新項目時同步處理，但只會在執行重新整理動作按鈕時同步處理。

1. 現在，資料不會在應用程式啟動時與伺服器進行同步處理，但是成為本機存放區的內容。

2. 

            if (completion != nil) {
                dispatch_async(dispatch_get_main_queue(), completion);
            }

3. 

            if (completion != nil) {
                dispatch_async(dispatch_get_main_queue(), completion);
            }


## 

在本節中，將連接至無效的 URL，以模擬離線情況。 當您新增資料項目時，這些項目會存放在本機核心資料存放區，但不會同步到行動後端。

1. 將 **QSTodoService.m** 中的行動應用程式 URL 變更為無效的 URL，然後再次執行該應用程式：

        self.client = [MSClient clientWithApplicationURLString:@"https://sitename.azurewebsites.net.fail"];

2. 新增 todo 項目或完成某些項目。 結束模擬器 (或強制關閉應用程式)，然後重新啟動。 確認您的變更已保存下來。

3. 檢視遠端 TodoItem 資料表的內容：

    + 
    + 若為 .NET 後端，請使用 SQL 工具 (例如 SQL Server Management Studio) 或 REST 用戶端 (例如 Fiddler 或 Postman) 檢視資料表內容。

    請確認新項目*尚未* 同步處理到伺服器：

4. 請將 **QSTodoService.m** 中的 URL 變更回正確的 URL，然後重新執行應用程式。 將項目清單往下拉，執行重新整理動作。 您會看到進度微調按鈕和「同步中...」文字。

5. 再次檢視 TodoItem 資料。 新的和變更的 TodoItems 現在應該會出現。

## 摘要

在此案例中，本機存放區是以核心資料為基礎的資料庫。



正常情況下，在 Azure Mobile Apps 的 CRUD 作業執行時，應用程式會如同仍處於連線狀態，但所有的作業都會對本機存放區執行。

當我們想要與伺服器同步處理本機存放區中時，我們使用 `MSSyncTable.pullWithQuery` 和 `MSClient.syncContext.pushWithCompletion` 方法。

*  為了將變更推送至伺服器，我們呼叫 `pushWithCompletion`。 這個方法所屬 `MSSyncContext` 而不是同步資料表，因為它會在所有資料表之間推送變更。

    只有以某種方式在本機上修改過的記錄 (透過 CUD 作業)，才會傳送至伺服器。

* 若要將資料從資料表提取至應用程式伺服器上，我們呼叫 `MSSyncTable.pullWithQuery`。

    提取一律會先發出推送動作。 這是為了確保本機存放區中的所有資料表和關聯性都保持一致。

    請注意， `pullWithQuery` 可以用來篩選資料儲存在用戶端，藉由自訂 `查詢` 參數。

* 若要啟用增量同步處理，將查詢識別碼，來傳遞 `pullWithQuery`。 查詢識別碼用來儲存上次提取作業所產生的上次更新時間戳記。 對您應用程式中的每個邏輯查詢而言，查詢識別碼應該是唯一的描述性字串。 如果查詢有一個參數，則相同的參數值必須屬於查詢識別碼的一部分。

    如果您想選擇不要增量同步處理，則傳遞 `nil` 做為查詢識別碼。 在此情況下，擷取所有的記錄在每次呼叫 `pullWithQuery`, ，這可能會沒有效率。



## 其他資源

* 

* [雲端報導: Azure 行動服務中的離線同步處理] \ (附註: 的視訊會在行動服務，但是離線同步處理的運作方式類似的方式，在 Azure 行動應用程式)





[create an ios app]: ../app-service-mobile-ios-get-started.md 
[offline data sync in azure mobile apps]: ../app-service-mobile-offline-data-sync.md 
[defining-core-data-tableoperationerrors-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-tableoperationerrors-entity.png 
[defining-core-data-tableoperations-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-tableoperations-entity.png 
[defining-core-data-tableconfig-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-tableconfig-entity.png 
[defining-core-data-todoitem-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-todoitem-entity.png 
[cloud cover: offline sync in azure mobile services]: http://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri 
[azure friday: offline-enabled apps in azure mobile services]: http://azure.microsoft.com/en-us/documentation/videos/azure-mobile-services-offline-enabled-apps-with-donna-malayeri/ 

