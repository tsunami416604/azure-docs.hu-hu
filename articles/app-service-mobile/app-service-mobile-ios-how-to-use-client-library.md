<properties
    pageTitle="如何使用適用於 Azure Mobile Apps 的 iOS SDK"
    description="如何使用適用於 Azure Mobile Apps 的 iOS SDK"
    services="app-service\mobile"
    documentationCenter="ios"
    authors="krisragh"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-ios"
    ms.devlang="objective-c"
    ms.topic="article"
    ms.date="11/30/2015"
    ms.author="krisragh"/>


# 如何使用適用於 Azure Mobile Apps 的 iOS 用戶端程式庫

[AZURE.INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]
&nbsp;

[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

本指南說明如何使用最新版執行一般案例 [Azure 行動應用程式 iOS SDK](https://go.microsoft.com/fwLink/?LinkID=266533&clcid=0x409)。如果您不熟悉 Azure 行動應用程式，第一個完成 [Azure 行動應用程式快速入門] 來建立後端，建立資料表，並下載預先建置的 iOS Xcode 專案。在本指南中，我們會著重於用戶端 iOS SDK。若要深入了解.NET 伺服器端 SDK 後端，請參閱 [使用.NET 後端](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)

## 參考文件

IOS 用戶端 SDK 參考文件位於此處: [Azure 行動應用程式 iOS 用戶端參考](http://azure.github.io/azure-mobile-services/iOS/v3/)。

## <a name="Setup"></a>設定和必要條件

本指南假設您已建立包含資料表的後端。 本指南假設資料表的結構描述與這些教學課程中的資料表相同。 本指南也假設您的程式碼中參考 `WindowsAzureMobileServices.framework` 並匯入 `WindowsAzureMobileServices/WindowsAzureMobileServices.h`。

## <a name="create-client"></a>如何: 建立用戶端

若要存取的 Azure 行動應用程式後端專案中，建立 `MSClient`。 取代 `AppUrl` 與應用程式的 URL。 您可以保留 `gatewayURLString` 和 `applicationKey` 空白。 如果您設定閘道，以供驗證時，填入 `gatewayURLString` 的閘道 url。

```
MSClient *client = [MSClient clientWithApplicationURLString:@"AppUrl" gatewayURLString:@"" applicationKey:@""];
```

## <a name="table-reference"></a>如何: 建立資料表參考

若要存取或更新資料，請建立後端資料表的參考。 取代 `TodoItem` 與您的資料表名稱。

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

## <a name="templates"></a>如何: 註冊推播範本傳送跨平台通知

若要註冊範本，只要在用戶端應用程式中透過 **client.push registerDeviceToken** 方法傳遞範本即可。

        [client.push registerDeviceToken:deviceToken template:iOSTemplate completion:^(NSError *error) {
            ...
        }];

您的範本類型將為 NSDictionary，並且可能包含多個下列格式的範本：

        NSDictionary *iOSTemplate = @{ @"templateName": @{ @"body": @{ @"aps": @{ @"alert": @"$(message)" } } } };

請注意，所有的標記都將因安全性而移除。 若要將標記加入至安裝或安裝的範本，請參閱 [Azure 行動應用程式使用.NET 後端伺服器 SDK](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#tags)。

若要傳送通知，利用這些註冊的範本，使用 [ [通知中樞 Api](https://msdn.microsoft.com/library/azure/dn495101.aspx)

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
[azure mobile apps quick start]: app-service-mobile-ios-get-started.md 
[add mobile services to existing app]: /develop/mobile/tutorials/get-started-data 
[get started with mobile services]: /develop/mobile/tutorials/get-started-ios 
[validate and modify data in mobile services by using server scripts]: /develop/mobile/tutorials/validate-modify-and-augment-data-ios 
[mobile services sdk]: https://go.microsoft.com/fwLink/p/?LinkID=266533 
[authentication]: /develop/mobile/tutorials/get-started-with-users-ios 
[ios sdk]: https://developer.apple.com/xcode 
[handling expired tokens]: http://go.microsoft.com/fwlink/p/?LinkId=301955 
[live connect sdk]: http://go.microsoft.com/fwlink/p/?LinkId=301960 
[permissions]: http://msdn.microsoft.com/library/windowsazure/jj193161.aspx 
[service-side authorization]: mobile-services-javascript-backend-service-side-authorization.md 
[use scripts to authorize users]: /develop/mobile/tutorials/authorize-users-in-scripts-ios 
[dynamic schema]: http://go.microsoft.com/fwlink/p/?LinkId=296271 
[how to: access custom parameters]: /develop/mobile/how-to-guides/work-with-server-scripts#access-headers 
[create a table]: http://msdn.microsoft.com/library/windowsazure/jj193162.aspx 
[nsdictionary object]: http://go.microsoft.com/fwlink/p/?LinkId=301965 
[ascii control codes c0 and c1]: http://en.wikipedia.org/wiki/Data_link_escape_character#C1_set 
[cli to manage mobile services tables]: ../virtual-machines-command-line-tools.md#Mobile_Tables 
[conflict-handler]: mobile-services-ios-handling-conflicts-offline-data.md#add-conflict-handling 

