<properties
    pageTitle="如何使用適用於 Azure 行動服務的 iOS 用戶端程式庫"
    description="如何使用適用於行動服務的 iOS 用戶端程式庫"
    services="mobile-services"
    documentationCenter="ios"
    authors="krisragh"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="mobile-services"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-ios"
    ms.devlang="objective-c"
    ms.topic="article"
    ms.date="10/01/2015"
    ms.author="krisragh"/>


# 如何使用適用於 Azure 行動服務的 iOS 用戶端程式庫

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


[AZURE.INCLUDE [mobile-services-selector-client-library](../../includes/mobile-services-selector-client-library.md)]

本指南說明如何使用 Azure 行動服務 [iOS SDK] 執行一般案例。 如果您不熟悉行動服務中，第一個完成 [行動服務快速入門] 來設定您的帳戶，來建立資料表，並建立的行動服務。
> [AZURE.NOTE] 本指南使用最新 [iOS 行動服務 SDK](https://go.microsoft.com/fwLink/?LinkID=266533&clcid=0x409)。如果您的專案使用舊版的 SDK，請先升級 Xcode 中的架構。

[AZURE.INCLUDE [mobile-services-concepts](../../includes/mobile-services-concepts.md)]

## <a name="Setup"></a>設定和必要條件

本指南假設您已建立包含資料表的行動服務。 如需詳細資訊請參閱 [建立資料表]，或是重複使用 `TodoItem` [行動服務快速入門] 中建立資料表。 本指南假設資料表的結構描述與這些教學課程中的資料表相同。 本指南也假設您的 Xcode 參考 `WindowsAzureMobileServices.framework` 並匯入 `WindowsAzureMobileServices/WindowsAzureMobileServices.h`。

## <a name="create-client"></a>如何: 建立行動服務用戶端

若要存取 Azure 行動服務專案中的，建立 `MSClient` 用戶端物件。 取代 `AppUrl` 和 `AppKey` 取代為行動服務 URL 和應用程式金鑰儀表板值分別。

```
MSClient *client = [MSClient clientWithApplicationURLString:@"AppUrl" applicationKey:@"AppKey"];
```

## <a name="table-reference"></a>如何: 建立資料表參考

若要存取或更新 Azure 行動服務的資料，請建立資料表的參考。 取代 `TodoItem` 與您的資料表名稱。

```
    MSTable *table = [client tableWithName:@"TodoItem"];
```

## <a name="querying"></a>如何: 查詢資料

若要建立資料庫查詢，查詢 `MSTable` 物件。 下列查詢會取得所有項目 `TodoItem` 並記錄每個項目的文字。

```
[table readWithCompletion:^(MSQueryResult *result, NSError *error) {
        if(error) { // error is nil if no error occured
                NSLog(@"ERROR %@", error);
        } else {
                for(NSDictionary *item in result.items) { // items is NSArray of records that match query
                        NSLog(@"Todo Item: %@", [item objectForKey:@"text"]);
                }
        }
}];
```

## <a name="filtering"></a>如何: 篩選傳回的資料

若要篩選結果，有許多可用的選項。

若要使用述詞篩選，請使用 `NSPredicate` 和 `readWithPredicate`。 下列篩選器傳回的資料只尋找未完成的待辦事項。

```
// Create a predicate that finds items where complete is false
NSPredicate * predicate = [NSPredicate predicateWithFormat:@"complete == NO"];
// Query the TodoItem table and update the items property with the results from the service
[table readWithPredicate:predicate completion:^(MSQueryResult *result, NSError *error) {
        if(error) {
                NSLog(@"ERROR %@", error);
        } else {
                for(NSDictionary *item in result.items) {
                        NSLog(@"Todo Item: %@", [item objectForKey:@"text"]);
                }
        }
}];
```

## <a name="query-object"></a>如何: 使用 MSQuery

若要執行複雜的查詢 (包括排序和分頁)，請建立 `MSQuery` 物件直接或使用述詞:

```
    MSQuery *query = [table query];
    MSQuery *query = [table queryWithPredicate: [NSPredicate predicateWithFormat:@"complete == NO"]];
```

`MSQuery` 可讓您控制多個查詢行為，包括下列。 執行 `MSQuery` 查詢藉由呼叫 `readWithCompletion` 它下, 一個範例所示。
* 指定結果的順序
* 限制要傳回的欄位
* 限制傳回的記錄數目
* 指定回應中的總計數
* 在要求中指定自訂查詢字串參數
* 套用額外函數


## <a name="sorting"></a>如何: 使用 MSQuery 排序資料

我們來看一下範例如何排序結果。 若要先按照欄位 `文字` 然後按照欄位 `完成`, ，叫用 `MSQuery` 就像這樣:

```
[query orderByAscending:@"text"];
[query orderByDescending:@"complete"];
[query readWithCompletion:^(MSQueryResult *result, NSError *error) {
        if(error) {
                NSLog(@"ERROR %@", error);
        } else {
                for(NSDictionary *item in result.items) {
                        NSLog(@"Todo Item: %@", [item objectForKey:@"text"]);
                }
        }
}];
```

## <a name="paging"></a>如何: 使用 MSQuery 以分頁方式傳回資料

行動服務會限制單一回應中可傳回的記錄數。 若要控制顯示給使用者的記錄數，您必須實作分頁系統。 您可以使用 **MSQuery** 物件的下列三個屬性來執行分頁。

```
+   `BOOL includeTotalCount`
+   `NSInteger fetchLimit`
+   `NSInteger fetchOffset`
```

在下列範例中，簡單函數會向伺服器要求 5 筆記錄，然後將這些記錄附加到先前載入記錄的本機集合：

```
// Create and initialize these properties
@property (nonatomic, strong)   NSMutableArray *loadedItems; // Init via [[NSMutableArray alloc] init]
@property (nonatomic)                   BOOL moreResults;
```

```
-(void)loadResults
{
    MSQuery *query = [self.table query];

    query.includeTotalCount = YES;
    query.fetchLimit = 5;
    query.fetchOffset = self.loadedItems.count;


    [query readWithCompletion:^(MSQueryResult *result, NSError *error) {
        if(!error) {
            // Add the items to our local copy
            [self.loadedItems addObjectsFromArray:result.items];

            // Set a flag to keep track if there are any additional records we need to load
            self.moreResults = (self.loadedItems.count <= result.totalCount);
        }
    }];
}
```

## <a name="selecting"></a><a name="parameters"></a>如何: 限制欄位和展開查詢字串參數，使用 MSQuery

若要限制在查詢中傳回的欄位，請在 **selectFields** 屬性中指定欄位的名稱。 這僅會傳回文字和已完成欄位：

```
    query.selectFields = @[@"text", @"completed"];
```

若要在伺服器要求中包含其他查詢字串參數，(比方說，因為自訂伺服器端指令碼會使用它們)，請填入 `query.parameters` 就像這樣:

```
    query.parameters = @{
        @"myKey1" : @"value1",
        @"myKey2" : @"value2",
    };
```

## <a name="inserting"></a>如何: 插入資料

若要插入新的資料表資料列，建立新 `NSDictionary` 和叫用 `資料表插入`。 行動服務會自動產生新資料行是根據 `NSDictionary` 如果未啟用 [動態結構描述]。

如果 `識別碼` 未提供，則後端自動產生新的唯一識別碼。 提供您自己 `識別碼` 使用電子郵件地址、 使用者名稱，或您自己自訂的值做為識別碼。 提供您自己的識別碼可以讓聯結和商務導向的資料庫邏輯變得更容易。

```
    NSDictionary *newItem = @{@"id": @"custom-id", @"text": @"my new item", @"complete" : @NO};
    [self.table insert:newItem completion:^(NSDictionary *result, NSError *error) {
        // The result contains the new item that was inserted,
        // depending on your server scripts it may have additional or modified
        // data compared to what was passed to the server.
        if(error) {
                NSLog(@"ERROR %@", error);
        } else {
                        NSLog(@"Todo Item: %@", [result objectForKey:@"text"]);
        }
    }];
```

## <a name="modifying"></a>如何: 修改資料

若要更新現有的資料列，請修改項目並呼叫 `更新`:

```
    NSMutableDictionary *newItem = [oldItem mutableCopy]; // oldItem is NSDictionary
    [newItem setValue:@"Updated text" forKey:@"text"];
    [self.table update:newItem completion:^(NSDictionary *item, NSError *error) {
        // Handle error or perform additional logic as needed
    }];
```

或者，提供資料列識別碼和更新的欄位：

```
    [self.table update:@{@"id":@"37BBF396-11F0-4B39-85C8-B319C729AF6D", @"Complete":@YES} completion:^(NSDictionary *item, NSError *error) {
        // Handle error or perform additional logic as needed
    }];
```

最小值， `識別碼` 進行更新時，就必須設定屬性。

## <a name="deleting"></a>如何: 刪除資料

若要刪除的項目，叫用 `刪除` 與項目:

```
    [self.table delete:item completion:^(id itemId, NSError *error) {
        // Handle error or perform additional logic as needed
    }];
```

或者，提供資料列識別碼來進行刪除：

```
    [self.table deleteWithId:@"37BBF396-11F0-4B39-85C8-B319C729AF6D" completion:^(id itemId, NSError *error) {
        // Handle error or perform additional logic as needed
    }];
```

最小值， `識別碼` 進行刪除時，就必須設定屬性。

## <a name="#custom-api"></a>如何: 呼叫自訂 API

自訂 API 可讓您定義自訂端點，並用來公開無法對應插入、更新、刪除或讀取等操作的伺服器功能。 透過使用自訂 API，您可以進一步控制訊息，包括讀取與設定 HTTP 訊息標頭，並定義除了 JSON 以外的訊息內文格式。 如需如何在您的行動服務中建立自訂 API 的範例，請參閱 [How to: 定義自訂 API 端點](mobile-services-dotnet-backend-define-custom-api.md)。

[AZURE.INCLUDE [mobile-services-ios-call-custom-api](../../includes/mobile-services-ios-call-custom-api.md)]


## <a name="authentication"></a>如何: 驗證使用者

Azure 行動服務支援各種識別提供者。 如需基本教學課程，請參閱 [驗證]。

Azure 行動服務支援兩個驗證工作流程：

- **伺服器管理的登入**：Azure 行動服務會代表您的應用程式管理登入程序。 它會顯示提供者專屬的登入頁面，並且使用選擇的提供者進行驗證。

- **用戶端管理的登入**：_應用程式_向識別提供者要求權杖，然後向 Azure 行動服務出示此權杖以進行驗證。

當驗證成功時，您會取回具使用者識別碼值和驗證權杖的使用者物件。 若要使用此使用者識別碼來授權使用者，請參閱 [服務端授權]。 若要限制只有經驗證的使用者資料表存取，請參閱 [權限]。

### 伺服器管理的登入

以下是如何將伺服器管理的登入加入至 [行動服務快速入門] 的專案。您可以為其他專案使用類似的程式碼。 如需詳細資訊，以及查看有作用的端對端範例，請參閱 [驗證]。

[AZURE.INCLUDE [mobile-services-ios-authenticate-app](../../includes/mobile-services-ios-authenticate-app.md)]

### 用戶端管理的登入 (單一登入)

您可以在行動服務用戶端外部執行登入程序來啟用單一登入，或者您的應用程式是否要直接連絡識別提供者。 在這類情況下，您可以透過提供從支援的識別提供者獨立取得的權杖來登入行動服務。

下列範例會使用 [Live Connect SDK]，以啟用單一登入 iOS 應用程式。 這裡假設您有 **LiveConnectClient** 名為執行個體 `liveClient` 的控制站，使用者在登入。

```
    [client loginWithProvider:@"microsoftaccount"
        token:@{@"authenticationToken" : self.liveClient.session.authenticationToken}
        completion:^(MSUser *user, NSError *error) {
                // Handle success and errors
    }];
```

## <a name="caching-tokens"></a>如何: 快取驗證權杖

讓我們來看看如何快取語彙基元中的 [行動服務快速入門] 的專案。您可能會對任何專案套用類似的步驟。 [AZURE.INCLUDE [mobile-services-ios-authenticate-app-with-token](../../includes/mobile-services-ios-authenticate-app-with-token.md)]

## <a name="errors"></a>如何: 處理錯誤

當您呼叫行動服務時，completion 區塊會包含 `NSError * 錯誤` 參數。 發生錯誤時，此參數便會傳回非 Nil。 您應檢查程式碼中的此參數，並視需要處理錯誤。

檔案 [`< WindowsAzureMobileServices/MSError.h >`] (https://github.com/Azure/azure-mobile-services/blob/master/sdk/iOS/src/MSError.h) 會定義常數 `MSErrorResponseKey`, ，`MSErrorRequestKey`, ，和 `MSErrorServerItemKey` 以取得更多有關錯誤的資料。此外，檔案也定義每個錯誤代碼的常數。如需有關如何使用這些常數的範例，請參閱 [衝突處理常式] 及其用法的 `MSErrorServerItemKey` 和 `MSErrorPreconditionFailed`。







[what is mobile services]: #what-is 
[concepts]: #concepts 
[setup and prerequisites]: #Setup 
[how to: create the mobile services client]: #create-client 
[how to: create a table reference]: #table-reference 
[how to: query data from a mobile service]: #querying 
[filter returned data]: #filtering 
[sort returned data]: #sorting 
[return data in pages]: #paging 
[select specific columns]: #selecting 
[how to: bind data to the user interface]: #binding 
[how to: insert data into a mobile service]: #inserting 
[how to: modify data in a mobile service]: #modifying 
[how to: authenticate users]: #authentication 
[cache authentication tokens]: #caching-tokens 
[how to: upload images and large files]: #blobs 
[how to: handle errors]: #errors 
[how to: design unit tests]: #unit-testing 
[how to: customize the client]: #customizing 
[customize request headers]: #custom-headers 
[customize data type serialization]: #custom-serialization 
[next steps]: #next-steps 
[how to: use msquery]: #query-object 
[mobile services quick start]: mobile-services-ios-get-started.md 
[get started with mobile services]: mobile-services-ios-get-started.md 
[mobile services sdk]: https://go.microsoft.com/fwLink/p/?LinkID=266533 
[authentication]: /develop/mobile/tutorials/get-started-with-users-ios 
[ios sdk]: https://developer.apple.com/xcode 
[handling expired tokens]: http://go.microsoft.com/fwlink/p/?LinkId=301955 
[live connect sdk]: http://go.microsoft.com/fwlink/p/?LinkId=301960 
[permissions]: http://msdn.microsoft.com/library/windowsazure/jj193161.aspx 
[service-side authorization]: mobile-services-javascript-backend-service-side-authorization.md 
[dynamic schema]: http://go.microsoft.com/fwlink/p/?LinkId=296271 
[create a table]: http://msdn.microsoft.com/library/windowsazure/jj193162.aspx 
[nsdictionary object]: http://go.microsoft.com/fwlink/p/?LinkId=301965 
[ascii control codes c0 and c1]: http://en.wikipedia.org/wiki/Data_link_escape_character#C1_set 
[cli to manage mobile services tables]: ../virtual-machines-command-line-tools.md#Mobile_Tables 
[conflict-handler]: mobile-services-ios-handling-conflicts-offline-data.md#add-conflict-handling 

