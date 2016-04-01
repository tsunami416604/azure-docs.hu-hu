<properties 
    pageTitle="使用 JavaScript 後端行動服務" 
    description="提供如何在 Azure 行動服務中定義、註冊和使用伺服器指令碼的範例。" 
    services="mobile-services" 
    documentationCenter="" 
    authors="RickSaling" 
    manager="dwrede" 
    editor=""/>

<tags 
    ms.service="mobile-services" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="javascript" 
    ms.devlang="multiple" 
    ms.topic="article" 
    ms.date="12/01/2015" 
    ms.author="ricksal"/>


# 使用 JavaScript 後端行動服務

本文提供如何在 Azure 行動服務中使用 JavaScript 後端的詳細資訊及範例。 

##<a name="intro"></a>簡介

在 JavaScript 後端行動服務中，您可以將自訂商務邏輯定義為在伺服器上儲存及執行的 JavaScript 程式碼。 這個伺服器指令碼會指派給下列其中一項伺服器功能：

+ [插入、 讀取、 更新或刪除作業在給定資料表上的][Table operations]。
+ [排定的工作][Job Scheduler]。
+ [定義自訂 API 中的 HTTP 方法][Custom API anchor]。 

伺服器指令碼中主要函數的特徵依指令碼的使用情境而異。 您也可以將常用指令碼定義為 nodes.js 模組，由指令碼共用。 如需詳細資訊，請參閱 [來源控制與共用程式碼][Source control, shared code, and helper functions]。

如需個別伺服器指令碼物件和函式的說明，請參閱 [Mobile Services server script reference]。 


##<a name="table-scripts"></a>資料表作業

資料表作業指令碼是伺服器指令碼註冊至資料表的作業 ︰ 插入、 讀取、 更新或刪除 (*del*)。 本節描述如何在 JavaScript 後端中使用資料表作業，包括下列各節：

+ [資料表作業的概觀][Basic table operations]
+ [作法：註冊資料表作業]
+ [作法：覆寫預設回應]
+ [作法：覆寫執行成功]
+ [作法：覆寫預設的錯誤處理]
+ [作法：產生唯一的識別碼值](#generate-guids)
+ [作法：新增自訂參數]
+ [處理資料表使用者][How to: Work with users]

###<a name="basic-table-ops"></a>資料表作業的概觀

指令碼的名稱必須符合註冊之作業的種類。 一個指定資料表作業只能註冊一個指令碼。 每次 REST 要求叫用該指定作業時，就會執行其指令碼，例如，收到要在資料表中插入項目的 POST 要求時。 行動服務不會保留指令碼執行之間的狀態。 因為指令碼每次執行都會建立新的全域內容，指令碼中定義的所有狀態變數都會重新初始化。 如果您想儲存兩個不同要求之間的狀態，請在行動服務中建立資料表，讀取狀態並將狀態寫入資料表。 如需詳細資訊，請參閱 [How to: Access tables from scripts]。

如果需要在執行作業時強制採用自訂的業務邏輯，請編寫資料表作業指令碼。 例如，當 `text` 欄位的字串長度超過 10 個字元時，下列指令碼就會拒絕插入作業： 

    function insert(item, user, request) {
        if (item.text.length > 10) {
            request.respond(statusCodes.BAD_REQUEST, 
                'Text length must be less than 10 characters');
        } else {
            request.execute();
        }
    }

資料表指令碼函數一律有三個引數。

- 第一個引數依資料表作業而異。 

    - 插入或更新，對於 **項目** 物件，這是作業所影響的資料列的 JSON 表示法。 這可讓您存取資料行值的名稱，例如 *項目。擁有者*, ，其中 *擁有者* 是 JSON 表示法的名稱。
    - 若是刪除，則為要刪除之記錄的 ID。 
    - 若是讀取，則為 [query object] ，指定要傳回的資料列集。

- 第二個引數一律是 [使用者物件][User object] 表示提交要求的使用者。 

- 第三個引數一律是 [要求物件][request object], ，您可以控制執行要求的作業和傳送至用戶端的回應。

以下是資料表作業的標準主要函數特徵： 

+ [插入][insert function]: `function insert (item, user, request) { ... }`
+ [更新][update function]: `function update (item, user, request) { ... }`
+ [刪除][delete function]: `function del (id, user, request) { ... }`
+ [讀取][read function]: `function read (query, user, request) { ... }`

>[AZURE.NOTE]註冊至刪除作業的函數必須命名為 _del_ 因為 delete 是 JavaScript 的保留的關鍵字。 

每個伺服器指令碼都有主要函數，可能也有選擇性的協助程式函數。 雖然伺服器指令碼可能是為了特定資料表而建立，它也可以參考相同資料庫中的其他資料表。 您也可以將常用函數定義為模組，讓所有指令碼共用。 如需詳細資訊，請參閱 [來源控制與共用程式碼][Source control, shared code, and helper functions]。

###<a name="register-table-scripts"></a>作法：註冊資料表指令碼

您可以用下列方式之一定義註冊到資料表作業的伺服器指令碼：

+ 在 [Azure classic portal]。 指令碼中存取資料表作業的 **指令碼** 指定資料表的索引標籤。 以下為註冊到 `TodoItem` 資料表之插入指令碼的預設程式碼。 您可用自己的自訂業務邏輯覆寫此程式碼。

    ![1][1]
    
    若要了解如何執行這項操作，請參閱 [Validate and modify data in Mobile Services by using server scripts]。  

+ 使用原始檔控制。 啟用原始檔控制後，只要在 git 儲存機制的 .\service\table 子資料夾中建立名為 <em>`<table>`</em>.<em>`<operation>`</em>.js 的檔案，其中 <em>`<table>`</em> 是資料表的名稱，而 <em>`<operation>`</em> 是所註冊的資料表作業。 如需詳細資訊，請參閱 [來源控制與共用程式碼][Source control, shared code, and helper functions]。

+ 從命令提示字元使用 Azure 命令列工具。 如需詳細資訊，請參閱 [Using the command line tool]。


資料表作業指令碼必須呼叫下列函數之一 [request object] 以確保用戶端收到回應。 
 
+ **執行函式**︰ 作業已如要求完成並已傳回標準回應。
 
+ **respond 函數**︰ 已傳回自訂回應。

> [AZURE.IMPORTANT] 當指令碼不具有程式碼路徑 **執行** 或 **回應** 是叫用，作業可能會變成沒有回應。

下列指令碼會呼叫 **執行** 函式來完成用戶端要求的資料作業 ︰ 

    function insert(item, user, request) { 
        request.execute(); 
    }

此範例會將項目插入資料庫，並將適當的狀態代碼傳回給使用者。 

當 **執行** 函式呼叫時， `item`, ，[查詢][query object], ，或 `id` 值，第一個引數傳遞至指令碼函式用來執行作業。 若是插入、 更新或查詢作業，您可以修改項目，或查詢才能呼叫 **執行**: 

    function insert(item, user, request) { 
        item.scriptComment =
            'this was added by a script and will be saved to the database'; 
        request.execute(); 
    } 
 
    function update(item, user, request) { 
        item.scriptComment = 
            'this was added by a script and will be saved to the database'; 
        request.execute(); 
    } 

    function read(query, user, request) { 
        // Only return records for the current user         
        query.where({ userid: user.userId}); 
        request.execute(); 
    }
 
>[AZURE.NOTE]在刪除指令碼中，變更提供的 userId 變數值不影響哪一個記錄會被刪除。

如需詳細資訊，請參閱 [Read and write data], ，[Modify the request] 和 [Validate data]。


###<a name="override-response"></a>作法：覆寫預設回應

您也可以使用指令碼實作可以覆寫預設回應行為的驗證邏輯。 如果驗證失敗，只要呼叫 **回應** 函數來代替 **執行** 函式，並寫入用戶端的回應 ︰ 

    function insert(item, user, request) {
        if (item.userId !== user.userId) {
            request.respond(statusCodes.FORBIDDEN, 
            'You may only insert records with your userId.');
        } else {
            request.execute();
        }
    }

在此範例中，則會拒絕要求時插入的項目並沒有 `userId` 屬性符合 `userId` 的 [user object] ，提供給已驗證的用戶端。 在此情況下，資料庫作業 (*插入*) 不會發生，並將含 403 HTTP 狀態碼和自訂錯誤訊息回應傳回給用戶端。 如需詳細資訊，請參閱 [Modify the response]。

###<a name="override-success"></a>作法：覆寫執行成功

根據預設，在資料表作業中， **執行** 函式會自動寫入回應。 不過，您可以傳遞兩個選擇性參數給 execute 函數，以覆寫其成功及/或發生錯誤時的行為。

透過傳入 **成功** 執行處理常式呼叫時，寫入回應之前，您可以修改查詢的結果。 在從資料庫讀取資料之後，寫入回應之前，下列範例會呼叫 `execute({ success: function(results) { ... })` 來執行額外的工作：

    function read(query, user, request) {
        request.execute({
            success: function(results) {
                results.forEach(function(r) {
                    r.scriptComment = 
                    'this was added by a script after querying the database';
                });
                request.respond();
            }
        });
    }

當您提供 **成功** 處理常式，以便 **執行** 函式，您還必須呼叫 **回應** 函式的一部分 **成功** 處理常式，讓執行階段知道指令碼已完成且可以寫入回應。 當您呼叫 **回應** 未傳遞任何引數，行動服務會產生預設回應。 

>[AZURE.NOTE]您可以呼叫 **回應** 不含引數來叫用預設回應，只有在您第一次呼叫之後，才 **執行** 函式。
 
###<a name="override-error"></a>作法：覆寫預設的錯誤處理

 **執行** 函式的資料庫、 物件無效或查詢不正確的連線中斷時可能會失敗。 依預設，當錯誤發生時，伺服器指令碼會記錄錯誤，並將錯誤結果寫入回應。 因為行動服務提供預設的錯誤處理，您不必處理服務可能發生的錯誤。 

如果想要在發生錯誤時採取特定的補償動作，或者想要使用全域主控台物件在記錄檔內寫入更詳細的資訊，您可以實作明確的錯誤處理，覆寫預設的錯誤處理。 作法是提供 **錯誤** 處理常式，以便 **執行** 函式 ︰

    function update(item, user, request) { 
      request.execute({ 
        error: function(err) { 
          // Do some custom logging, then call respond. 
          request.respond(); 
        } 
      }); 
    }
 

行動服務時提供錯誤處理常式時，會將錯誤結果傳回用戶端時 **回應** 呼叫。

您也可以同時提供 **成功** 和 **錯誤** 如果您想要的處理常式。

###<a name="generate-guids"></a>作法：產生唯一的識別碼值

行動服務支援資料表的唯一自訂字串值 **識別碼** 資料行。 這可讓應用程式使用自訂的值，例如電子郵件地址或使用者名稱作為識別碼。 

字串識別碼為您提供下列優點：

+ 不需要往返存取資料庫就能產生識別碼。
+ 輕鬆合併不同資料表或資料庫的記錄。
+ 識別碼值與應用程式邏輯的整合更理想。

插入的記錄上未設定字串識別碼值時，行動服務會產生唯一值做為識別碼。 您可以在伺服器指令碼中產生您自己的唯一識別碼值。 下列指令碼範例將產生自訂 GUID，並將其指派給新記錄的識別碼。 此識別碼與行動服務在您未傳入記錄識別碼值時所產生的識別碼值相類似。

    // Example of generating an id. This is not required since Mobile Services
    // will generate an id if one is not passed in.
    item.id = item.id || newGuid();
    request.execute();

    function newGuid() {
        var pad4 = function(str) { return "0000".substring(str.length) + str; };
        var hex4 = function () { return pad4(Math.floor(Math.random() * 0x10000 /* 65536 */ ).toString(16)); };
        return (hex4() + hex4() + "-" + hex4() + "-" + hex4() + "-" + hex4() + "-" + hex4() + hex4() + hex4());
    }


如果應用程式提供識別碼的值，則行動服務會原封不動地儲存它。 這包括開頭或結尾的空白字元。 值中的空白字元不會被去除。

`id` 的值必須是唯一的，且不可包含下列字元集中的字元：

+ 控制字元：[0x0000-0x001F] 和 [0x007F-0x009F]。 如需詳細資訊，請參閱 [ASCII 控制碼 C0 和 C1](http://en.wikipedia.org/wiki/Data_link_escape_character#C1_set)。
+  可列印的字元 ︰ **"**(0x0022)、 **\ +** (0x002B)、 **/** (0x002F)、 **嗎？** (0x003F)、 **\\** (0x005C)、 **'** (0x0060)
+  識別碼 "." 和 ".."

您也可以在資料表中使用整數識別碼。 若要使用整數識別碼，您必須使用 `--integerId` 選項，以 `mobile table create` 命令建立資料表。 此命令需要在 Azure 的命令列介面 (CLI) 中執行。 如需有關如何使用 CLI 的詳細資訊，請參閱 [CLI 管理行動服務資料表](../virtual-machines-command-line-tools.md#Mobile_Tables)。


###<a name="access-headers"></a>作法：存取自訂參數

當您傳送要求給行動服務，可以在要求的 URI 中加入自訂參數，指示您的資料表作業指令碼如何處理指定的要求。 然後修改指令碼，使其檢查參數以決定處理路徑。

例如，以下是 POST 要求的 URI，它告訴服務不要允許插入的新 *TodoItem* 擁有相同文字值 ︰

        https://todolist.azure-mobile.net/tables/TodoItem?duplicateText=false

這些自訂的查詢參數當做 JSON 值存取 **參數** 屬性 [request object]。  **要求** 行動服務所提供物件給任何已註冊至資料表作業的函數。 下列用於插入作業的伺服器指令碼，會先檢查 `duplicateText` 參數的值，再執行插入作業：

        function insert(item, user, request) {
            var todoItemTable = tables.getTable('TodoItem');
            // Check the supplied custom parameter to see if
            // we should allow duplicate text items to be inserted.        
            if (request.parameters.duplicateText === 'false') {
                // Find all existing items with the same text
                // and that are not marked 'complete'. 
                todoItemTable.where({
                    text: item.text,
                    complete: false
                }).read({
                    success: insertItemIfNotComplete
                });
            } else {
                request.execute();
            }

            function insertItemIfNotComplete(existingItems) {
                if (existingItems.length > 0) {
                    request.respond(statusCodes.CONFLICT, 
                        "Duplicate items are not allowed.");
                } else {
                    // Insert the item as normal. 
                    request.execute();
                }
            }
        }

請注意，在 **insertItemIfNotComplete**  **執行** 函式的 [request object] 叫用沒有重複文字時插入項目; 否則為 **回應** 函式會叫用來通知用戶端有重複。 

請注意，若要呼叫的語法 **成功** 上述程式碼中的函式 ︰

                }).read({
                    success: insertItemIfNotComplete
                });

在 JavaScript 中這是精簡版，等同於以下這段較長的程式碼： 

        success: function(results) 
        { 
            insertItemIfNotComplete(results); 
        }


###<a name="work-with-users"></a>作法：處理使用者

在 Azure 行動服務中，您可以使用身分識別提供者來驗證使用者。 如需詳細資訊，請參閱 [Get started with authentication]。 當已驗證的使用者叫用資料表作業時，行動服務會使用 [user object] 提供使用者註冊的指令碼函式的相關資訊。  **UserId** 屬性可以用來儲存及擷取使用者特定資訊。 下列範例設定根據項目的 owner 屬性 **userId** 通過驗證的使用者 ︰

    function insert(item, user, request) {
        item.owner = user.userId;
        request.execute();
    }

下一個範例會將額外的篩選加入至查詢是根據 **userId** 通過驗證的使用者。 此篩選條件限制結果為只屬於目前使用者的項目：  

    function read(query, user, request) {
        query.where({
            owner: user.userId
        });
        request.execute();
    }

##<a name="custom-api"></a>自訂 API

本節描述如何建立及使用自訂 API 端點，其中包括下列各節： 
    
+ [自訂 API 的概觀](#custom-api-overview)
+ [作法：定義自訂 API]
+ [作法：實作 HTTP 方法]
+ [作法：以 XML 傳送及接收資料]
+ [作法：在自訂 API 中處理使用者和標頭]
+ [作法：在自訂 API 中定義多個路由]

###<a name="custom-api-overview"></a>自訂 API 的概觀

自訂 API 是行動服務中的端點，由一或多個標準 HTTP 方法存取：GET、POST、PUT、PATCH、DELETE。 可以為自訂 API 支援的每個 HTTP 方法另外定義一個 export 函數，全部放在同一個指令碼檔案中。 當使用指定方法的自訂 API 收到要求，會叫用註冊的指令碼。 如需詳細資訊，請參閱 [Custom API]。

行動服務執行階段呼叫自訂 API 函數時同時 [要求][request object] 和 [回應][response object] 所提供的物件。 這些物件公開的功能 [express.js library], ，而您的指令碼可加以利用。 以下名為的自訂 API **hello** 是非常簡單的範例，會傳回 _Hello，world ！_ 在 POST 要求的回應 ︰

        exports.post = function(request, response) {
            response.send(200, "{ message: 'Hello, world!' }");
        } 

 **傳送** 函式上 [response object] 傳回您想要在用戶端的回應。 傳送 POST 要求給以下 URL 就會叫用此程式碼：

        https://todolist.azure-mobile.net/api/hello  

執行之間的全域狀態會被保留下來。 

###<a name="define-custom-api"></a>作法：定義自訂 API

您可以用下列方式之一，定義註冊到自訂 API 端點中 HTTP 方法的伺服器指令碼：

+ 在 [Azure classic portal]。 建立及修改自訂 API 指令碼 **API** ] 索引標籤。 伺服器指令碼位於 **指令碼** 索引標籤上指定的自訂 API。 以下是 POST 要求對 `CompleteAll` 自訂 API 端點叫用的指令碼。 

    ![2][2]
    
    在 [權限] 索引標籤中指派對自訂 API 方法的存取權限。 若要查看此自訂 API 的建立方式，請參閱 [Call a custom API from the client]。  

+ 使用原始檔控制。 啟用原始檔控制後，只要在 git 儲存機制的 .\service\api 子資料夾中建立名為 <em>`<custom_api>`</em>.js 的檔案，其中 <em>`<custom_api>`</em> 是所註冊之自訂 API 的名稱。 此指令碼檔案包含 _匯出_ 函式的自訂 API 公開的每個 HTTP 方法。 權限是在伴隨的 .json 檔案中定義。 如需詳細資訊，請參閱 [來源控制與共用程式碼][Source control, shared code, and helper functions]。

+ 從命令提示字元使用 Azure 命令列工具。 如需詳細資訊，請參閱 [Using the command line tool]。

###<a name="handle-methods"></a>作法：實作 HTTP 方法

自訂 API 可以處理一或多個 HTTP 方法：GET、POST、PUT、PATCH 和 DELETE。 為自訂 API 處理的每個 HTTP 方法定義 exported 函數。 單一自訂 API 程式碼檔案可以匯出下列其中一個函數或所有函數：

        exports.get = function(request, response) { ... };
        exports.post = function(request, response) { ... };
        exports.patch = function(request, response) { ... };
        exports.put = function(request, response) { ... };
        exports.delete = function(request, response) { ... };

在伺服器指令碼中未實作的 HTTP 方法無法呼叫自訂 API 端點，且會傳回 405 (不允許方法) 錯誤回應。 可以對每個支援 HTTP 方法指派不同的權限層級。

###<a name="api-return-xml"></a>作法：以 XML 傳送及接收資料

用戶端儲存和擷取資料時，行動服務使用 JavaScript Object Notation (JSON) 來表現訊息主體中的資料。 不過，有些情況下您會想使用 XML 來裝載資料。 例如，Windows 市集應用程式的內建定期通知功能需要服務發出 XML。 如需詳細資訊，請參閱 [Define a custom API that supports periodic notifications]。

下列 **OrderPizza** 自訂 API 函數會傳回簡單的 XML 文件做為回應裝載 ︰

        exports.get = function(request, response) {
          response.set('content-type', 'application/xml');
          var xml = '<?xml version="1.0"?><PizzaOrderForm><PizzaOrderForm/>';
          response.send(200, xml);
        };

傳送至以下端點的 HTTP GET 要求會叫用這個自訂 API 函數：

        https://todolist.azure-mobile.net/api/orderpizza

###<a name="get-api-user"></a>作法：在自訂 API 中處理使用者和標頭

在 Azure 行動服務中，您可以使用身分識別提供者來驗證使用者。 如需詳細資訊，請參閱 [Get started with authentication]。 當已驗證的使用者要求自訂 API 時，行動服務會使用 [user object] 提供給自訂 API 程式碼之使用者的相關資訊。  [user object] 從的 user 屬性存取 [request object]。  **UserId** 屬性可以用來儲存及擷取使用者特定資訊。 

下列 **OrderPizza** 自訂 API 函數設定根據項目的 owner 屬性 **userId** 通過驗證的使用者 ︰

        exports.post = function(request, response) {
            var userTable = request.service.tables.getTable('user');
            userTable.lookup(request.user.userId, {
                success: function(userRecord) {
                    callPizzaAPI(userRecord, request.body, function(orderResult) {
                        response.send(201, orderResult);
                    });
                }
            });
        
        };

傳送至以下端點的 HTTP POST 要求會叫用這個自訂 API 函數：

        https://<service>.azure-mobile.net/api/orderpizza

您也可以存取特定 HTTP 標頭從 [request object], ，如下列程式碼所示 ︰

        exports.get = function(request, response) {    
            var header = request.header('my-custom-header');
            response.send(200, "You sent: " + header);
        };

這個簡單的範例會讀取名為 `my-custom-header` 的自訂標頭，然後在回應中傳回值。

###<a name="api-routes"></a>作法：在自訂 API 中定義多個路由

行動服務可讓您在自訂 API 中定義多個路徑 (或稱為路由)。 例如，HTTP GET 要求中的下列 Url **計算機** 會叫用自訂 API **新增** 或 **減** 分別函式 ︰ 

+ `https://<service>.azure-mobile.net/api/calculator/add`
+ `https://<service>.azure-mobile.net/api/calculator/sub`

藉由匯出定義多個路由 **註冊** 函式，會傳遞 **api** 物件 (類似於 [express object in express.js]) 用來在自訂 API 端點之下註冊路由。 下列範例會實作 **新增** 和 **sub** 方法 **計算機** 自訂 API: 

        exports.register = function (api) {
            api.get('add', add);
            api.get('sub', subtract);
        }
        
        function add(req, res) {
            var result = parseInt(req.query.a) + parseInt(req.query.b);
            res.send(200, { result: result });
        }
        
        function subtract(req, res) {
            var result = parseInt(req.query.a) - parseInt(req.query.b);
            res.send(200, { result: result });
        }

 **Api** 物件傳遞給 **註冊** 函式會公開每個 HTTP 方法的函式 (**取得**, ，**張貼**, ，**放**, ，**修補程式**, ，**刪除**)。 這些函數會註冊特定 HTTP 方法之已定義函數的路由。 每個函數都有兩個參數，第一個是路由名稱，第二個是註冊到路由的函數。 

HTTP GET 要求可以如下叫用上述自訂 API 範例中的兩個路由 (也顯示回應)：

+ `https://<service>.azure-mobile.net/api/calculator/add?a=1&b=2`

        {"result":3}

+ `https://<service>.azure-mobile.net/api/calculator/sub?a=3&b=5`

        {"result":-2}

##<a name="scheduler-scripts"></a>工作排程器

行動服務可讓您定義伺服器指令碼是要當做按照固定排程執行的工作，或是從 Azure 傳統入口網站。 排程工作對於執行定期工作來說很實用，例如清除資料表資料、批次處理。 如需詳細資訊，請參閱 [Schedule jobs]。

註冊到排程工作的指令碼有一個主要函數，其名稱與排程的工作相同。 由於排程的指令碼不是由 HTTP 要求叫用，因此伺服器執行階段沒有內容可傳遞，函數不帶參數。 和其他種類的指令碼一樣，您可以有子常式函數、需要共用模組。 如需詳細資訊，請參閱 [Source control, shared code, and helper functions]。

###<a name="scheduler-scripts"></a>作法：定義排程工作指令碼

可將伺服器指令碼指派至行動服務排程器中定義的工作。 這些指令碼屬於該工作，會依照工作的排程執行。 (您也可以使用 [Azure classic portal] 視需要執行工作。）定義排程工作的指令碼沒有參數，因為行動服務不會傳遞任何資料給它；它就像 JavaScript 的一般函數一樣執行，且不會與行動服務直接互動。 

以下列方式之一定義排程工作： 

+ 在 [Azure classic portal] 中 **指令碼** 在排程器] 索引標籤 ︰

    ![3][3]

    如需如何執行這項操作的詳細資訊，請參閱 [Schedule backend jobs in Mobile Services]。 

+ 從命令提示字元使用 Azure 命令列工具。 如需詳細資訊，請參閱 [Using the command line tool]。

>[AZURE.NOTE]當您啟用原始檔控制時，您可以編輯在 git 儲存機制的.\service\scheduler 子資料夾中的排程的工作指令碼檔案。 如需詳細資訊，請參閱 [How to: Share code by using source control]。

##<a name="shared-code"></a>原始檔控制、共用程式碼及協助程式函數

本節顯示如何運用原始檔控制來加入您自己的自訂 node.js 模組、共用程式碼和其他程式碼重複使用策略，包括下列各節：

+ [運用共用程式碼的概觀](#leverage-source-control)
+ [作法：載入 Node.js 模組]
+ [作法：使用協助程式函數]
+ [作法：使用原始檔控制共用程式碼]
+ [作法：使用應用程式設定] 

###<a name="leverage-source-control"></a>運用共用程式碼的概觀

因為行動服務使用伺服器上的 Node.js，所以您的指令碼可以存取內建的 Node.js 模組。 您也可以使用原始檔控制來定義您自己的模組或新增其他 Node.js 模組至您的服務。

以下列出一些可加以利用您的指令碼中使用全域的實用模組的 **需要** 函式 ︰

+ **azure**︰ 可讓您的 Azure sdk for Node.js。 如需詳細資訊，請參閱 [Azure SDK for Node.js]。 
+ **密碼編譯**︰ 提供 OpenSSL 的加密功能。 如需詳細資訊，請參閱 [Node.js 文件][crypto API]。
+ **路徑**︰ 包含用於處理檔案路徑的公用程式。 如需詳細資訊，請參閱 [Node.js 文件][path API]。
+ **querystring**︰ 包含用於處理查詢字串公用程式。 如需詳細資訊，請參閱 [Node.js 文件][querystring API]。
+ **要求**︰ 傳送 HTTP 要求給外部 REST 服務，如 Twitter、 Facebook。 如需詳細資訊，請參閱 [Send HTTP request]。
+ **sendgrid**︰ 在 Azure 中使用 Sendgrid 電子郵件服務傳送電子郵件。 如需詳細資訊，請參閱 [Send email from Mobile Services with SendGrid]。
+ **url**︰ 包含用於剖析及解析 Url 的公用程式。 如需詳細資訊，請參閱 [Node.js 文件][url API]。
+ **util**︰ 包含各種公用程式，如字串格式化、 物件類型檢查。 如需詳細資訊，請參閱 [Node.js 文件][util API]。 
+ **zlib**︰ 公開壓縮功能，如 gzip、 deflate。 如需詳細資訊，請參閱 [Node.js 文件][zlib API]。 

###<a name="modules-helper-functions"></a>作法：運用模組

行動服務公開一組指令碼可以使用全域載入的模組 **需要** 函式。 例如，指令碼可以要求 **要求** 提出 HTTP 要求 ︰ 

    function update(item, user, request) { 
        var httpRequest = require('request'); 
        httpRequest('http://www.google.com', function(err, response, body) { 
            ... 
        }); 
    } 


###<a name="shared-code-source-control"></a>作法：使用原始檔控制共用程式碼

您可以使用原始檔控制搭配 Node.js 封裝管理員 (npm) 來控制您的行動服務可使用哪些模組。 作法有二：

+ 針對發行至 npm 以及由 npm 安裝的模組，使用 package.json 檔案宣告哪些封裝要由您的行動服務安裝。 這麼做時，您的服務一律有權存取所需封裝的最新版本。 package.json 檔案會留存在 `.\service` 目錄中。 如需詳細資訊，請參閱 [Support for package.json in Azure Mobile Services]。

+ 若為私人或自訂模組，您可以使用 npm 手動將模組安裝至原始檔控制的 `.\service\node_modules` 目錄中。 如需如何手動上傳模組的範例，請參閱 [Leverage shared code and Node.js modules in your server scripts]。

    >[AZURE.NOTE]當 `node_modules` 已經存在的目錄階層，NPM 將會建立 `\node-uuid` 子目錄中的，而不是建立新 `node_modules` 儲存機制中。 在此情況下，只要刪除現有的 `node_modules` 目錄。

您認可 package.json 檔案或自訂模組的儲存機制的行動服務之後，請使用 **需要** 依名稱參考該模組。   

>[AZURE.NOTE] 您在 package.json 中指定，或上傳至您的行動服務的模組，只會用於您的伺服器指令碼。 行動服務執行階段不會使用這些模組。

###<a name="helper-functions"></a>作法：使用協助程式函數

除了需要模組，個別伺服器指令碼還可以包含協助程式函數。 這是與主要函數分開的函數，可用於分解指令碼中的程式碼。 

在下列範例中，註冊資料表指令碼至插入作業，其中包含協助程式函數 **handleUnapprovedItem**:


    function insert(item, user, request) {
        if (!item.approved) {
            handleUnapprovedItem(item, user, request);
        } else {
            request.execute();
        }
    }
    
    function handleUnapprovedItem(item, user, request) {
        // Do something with the supplied item, user, or request objects.
    }
 
在指令碼中，必須在主要函數後宣告協助程式函數。 您必須在指令碼中宣告所有變數。 未宣告的變數可能會導致錯誤。

也可以定義協助程式函數一次，然後在伺服器指令碼之間共用。 若要在指令碼之間共用函數，必須將函數匯出，而指令碼檔案必須在 `.\service\shared\` 目錄中。 以下範本示範如何匯出 `.\services\shared\helpers.js` 檔案中的共用函數：

        exports.handleUnapprovedItem = function (tables, user, callback) {
            
            // Do something with the supplied tables or user objects and 
            // return a value to the callback function.
        };
 
然後在資料表作業指令碼中使用如下的函數：

        function insert(item, user, request) {
            var helper = require('../shared/helper');
            helper.handleUnapprovedItem(tables, user, function(result) {
                    
                    // Do something based on the result.
                    request.execute();
                }
            }
        }

在此範例中，您必須傳遞兩者 [tables object] 和 [user object] 給共用的函數。 這是因為共用指令碼無法存取全域 [tables object], ，而 [user object] 只存在於要求內容。

指令碼檔案上傳至共用目錄的方式使用 [原始檔控制][How to: Share code by using source control] 或使用 [命令列工具][Using the command line tool]。

###<a name="app-settings"></a>作法：使用應用程式設定

行動服務可安全地把值儲存為應用程式設定，再由您的伺服器指令碼在執行階段存取。  當您新增資料至行動服務的應用程式設定，名稱/值組會被加密儲存，您可以用伺服器指令碼存取它們，不需將它們硬式編碼至指令碼檔案中。 如需詳細資訊，請參閱 [App settings]。

以下的自訂 API 範例使用提供 [service object] 來擷取應用程式設定值。  

        exports.get = function(request, response) {
        
            // Get the MY_CUSTOM_SETTING value from app settings.
            var customSetting = 
                request.service.config.appSettings.my_custom_setting;
                
            // Do something and then send a response.

        }

以下程式碼使用組態模組擷取 Twitter 存取權杖值，這些值儲存在應用程式設定中，用於排程工作指令碼：

        // Get the service configuration module.
        var config = require('mobileservice-config');

        // Get the stored Twitter consumer key and secret. 
        var consumerKey = config.twitterConsumerKey,
            consumerSecret = config.twitterConsumerSecret
        // Get the Twitter access token from app settings.    
        var accessToken= config.appSettings.TWITTER_ACCESS_TOKEN,
            accessTokenSecret = config.appSettings.TWITTER_ACCESS_TOKEN_SECRET;

請注意，此程式碼也會擷取 Twitter 消費者金鑰值儲存在 **識別** 在入口網站] 索引標籤。 因為 **config 物件** 是資料表作業和排程的工作指令碼中無法使用，您必須要求組態模組存取應用程式設定。 如需完整範例，請參閱 [Schedule backend jobs in Mobile Services]。

<h2><a name="command-prompt"></a>使用命令列工具</h2>

在行動服務中，可以使用 Azure 命令列工具建立、修改及刪除伺服器指令碼。 上傳指令碼前，請確定您使用以下的目錄結構：

![4][4]

請注意，此目錄結構和使用原始檔控制時的 git 儲存機制相同。 

從命令列工具上傳指令碼檔案時，必須先瀏覽至 `.\services\` 目錄。 下列命令會從 `table` 子目錄上傳名為 `todoitem.insert.js` 的指令碼：

        ~$azure mobile script upload todolist table/todoitem.insert.js
        info:    Executing command mobile script upload
        info:    mobile script upload command OK

以下命令會傳回行動服務維護的每個指令碼檔案的相關資訊：

        ~$ azure mobile script list todolist
        info:    Executing command mobile script list
        + Retrieving script information
        info:    Table scripts
        data:    Name                       Size
        data:    -------------------------  ----
        data:    table/channels.insert      1980
        data:    table/TodoItem.insert      5504
        data:    table/TodoItem.read        64
        info:    Shared scripts
        data:    Name              Size
        data:    ----------------  ----
        data:    shared/helper.js  62
        data:    shared/uuid.js    7452
        info:    Scheduled job scripts
        data:    Job name    Script name           Status    Interval     Last run  Next run
        data:    ----------  --------------------  --------  -----------  --------  --------
        data:    getUpdates  scheduler/getUpdates  disabled  15 [minute]  N/A       N/A
        info:    Custom API scripts
        data:    Name                    Get          Put          Post         Patch        Delete
        data:    ----------------------  -----------  -----------  -----------  -----------  -----------
        data:    completeall             application  application  application  application  application
        data:    register_notifications  application  application  user         application  application
        info:    mobile script list command OK

如需詳細資訊，請參閱 [Commands to manage Azure Mobile Services]。 

##<a name="working-with-tables"></a>使用資料表

本節詳細說明直接使用 SQL 資料庫資料表資料的策略，包括下列各節：

+ [使用資料表的概觀](#overview-tables)
+ [作法：從指令碼存取資料表]
+ [作法：執行大量插入]
+ [作法：將 JSON 類型對應至資料庫類型]
+ [使用 Transact-SQL 存取資料表]

###<a name="overview-tables"></a>使用資料表的概觀

行動服務中許多案例都需要伺服器指令碼存取資料庫中的資料表。 例如。 因為行動服務不會保留指令碼執行之間的狀態，需要指令碼執行之間保存任何資料必須儲存在資料表中。 您可能也會想檢查權限資料表中的項目，或儲存稽核資料，而不只是寫入記錄檔；記錄檔中的資料有持續時間限制，且無法以編寫程式碼的方式存取。 

行動服務可以兩種方式存取資料表，方式使用 [table object] proxy 使用編輯 TRANSACT-SQL 查詢或 [mssql object]。  [table object] 可以輕鬆地存取資料表的資料從伺服器指令碼，但 [mssql object] 支援更複雜的資料作業，並提供最大的彈性。 

###<a name="access-tables"></a>作法：從指令碼存取資料表

從指令碼存取資料表的最簡單方式是使用 [tables object]。  **GetTable** 函式會傳回 [table object] 是 proxy 存取要求的執行個體。 然後您可以呼叫 Proxy 上的函數來存取及變更資料。 

註冊到資料表作業和排程的工作的指令碼可以存取 [tables object] 如同全域物件。 這行程式碼取得的 proxy *TodoItems* 從全域資料表 [tables object]: 

        var todoItemsTable = tables.getTable('TodoItems');

自訂 API 指令碼可以存取 [tables object] 從 <strong>服務</strong> 所提供的屬性 [request object]。 這行程式碼取得 [tables object] 要求 ︰

        var todoItemsTable = request.service.tables.getTable('TodoItem');

> [AZURE.NOTE] 無法存取共用的函數 **資料表** 直接物件。 在共用的函數中，您必須將 tables 物件傳遞給函數。

一旦 [table object], ，您可以呼叫一或多個資料表作業函數 ︰ 插入、 更新、 刪除或讀取。 此範例會讀取權限資料表中的使用者權限：

    function insert(item, user, request) {
        var permissionsTable = tables.getTable('permissions');
    
        permissionsTable
            .where({ userId: user.userId, permission: 'submit order'})
            .read({ success: checkPermissions });
            
        function checkPermissions(results) {
            if(results.length > 0) {
                // Permission record was found. Continue normal execution.
                request.execute();
            } else {
                console.log('User %s attempted to submit an order without permissions.', user.userId);
                request.respond(statusCodes.FORBIDDEN, 'You do not have permission to submit orders.');
            }
        }
    }

下一個範例會將稽核資訊寫入 **稽核** 資料表 ︰

    function update(item, user, request) {
        request.execute({ success: insertAuditEntry });
        
        function insertAuditEntry() {
            var auditTable = tables.getTable('audit');
            var audit = {
                record: 'checkins',
                recordId: item.id,
                timestamp: new Date(),
                values: JSON.stringify(item)
            };
            auditTable.insert(audit, {
                success: function() {
                    // Write to the response now that all data operations are complete
                    request.respond();
                }
            });
        }
    }

最後一個範例是在程式碼範例 ︰ [How to ︰ 存取自訂參數][How to: Add custom parameters]。

###<a name="bulk-inserts"></a>作法：執行大量插入

如果您使用 **的** 或 **時** 迴圈直接在資料表中插入大量的項目 （例如 1000 個），您可能會遇到 SQL 連線限制造成部分插入失敗。 您的要求可能永遠不會完成，或者可能傳回 HTTP 500 內部伺服器錯誤。  若要避免這個問題，可以用一次 10 個項目的方式，批次插入項目。 插入第一個批次後，再提交下一個批次，依此類推。

您可以使用以下指令碼，設定平行插入記錄的批次大小。 建議您設定較小的記錄數目。 函式 **insertItems** 呼叫其本身以遞迴方式當非同步插入批次已完成。 For 迴圈結尾插入一筆記錄的時間，並呼叫 **insertComplete** 成功和 **errorHandler** 發生錯誤。 **insertComplete**  控制項是否 **insertItems** 會遞迴呼叫下一個批次，或是否工作完成，應該要結束指令碼。

        var todoTable = tables.getTable('TodoItem');
        var recordsToInsert = 1000;
        var batchSize = 10; 
        var totalCount = 0;
        var errorCount = 0; 
        
        function insertItems() {        
            var batchCompletedCount = 0;  
        
            var insertComplete = function() { 
                batchCompletedCount++; 
                totalCount++; 
                if(batchCompletedCount === batchSize || totalCount === recordsToInsert) {                        
                    if(totalCount < recordsToInsert) {
                        // kick off the next batch 
                        insertItems(); 
                    } else { 
                        // or we are done, report the status of the job 
                        // to the log and don't do any more processing 
                        console.log("Insert complete. %d Records processed. There were %d errors.", totalCount, errorCount); 
                    } 
                } 
            }; 
        
            var errorHandler = function(err) { 
                errorCount++; 
                console.warn("Ignoring insert failure as part of batch.", err); 
                insertComplete(); 
            };
        
            for(var i = 0; i < batchSize; i++) { 
                var item = { text: "This is item number: " + totalCount + i }; 
                todoTable.insert(item, { 
                    success: insertComplete, 
                    error: errorHandler 
                }); 
            } 
        } 
        
        insertItems(); 


可以在此找到完整的程式碼範例中，以及相關的討論 [部落格文章](http://blogs.msdn.com/b/jpsanders/archive/2013/03/20/server-script-to-insert-table-items-in-windows-azure-mobile-services.aspx)。 如果您使用此程式碼，可以根據您的特定情況調整，並徹底測試。

###<a name="JSON-types"></a>作法：將 JSON 類型對應至資料庫類型

在用戶端上以及在行動服務資料庫資料表中的資料類型集合並不相同。 有時候兩者可以輕鬆地彼此對應，但有時候卻又行不通。 行動服務會在對應中執行一些類型轉換：

- 用戶端的語言特定類型會序列化放入 JSON 中。
- JSON 表現法出現在伺服器指令碼之前，會先被轉譯為 JavaScript。
- JavaScript 資料類型會轉換為 SQL 資料庫類型時使用儲存 [tables object]。

從用戶端結構描述轉換為 JSON 的過程依平台而異。  Windows 市集和 Windows Phone 用戶端使用 JSON.NET。 Android 用戶端使用 gson 程式庫。  iOS 用戶端使用 NSJSONSerialization 類別。 過程中將使用這些程式庫各自的預設序列化行為，唯有日期物件除外，它會轉換為 JSON 字串，包含以 ISO 8601 編碼的日期。

當您要撰寫可使用伺服器指令碼 [insert], ，[update], ，[read] 或 [delete] 函式，您可以存取您資料的 JavaScript 表示法。 行動服務使用 Node.js 的還原序列化函數 ([JSON.parse](http://es5.github.io/#x15.12)) 將在網路上的 JSON 轉換為 JavaScript 物件。 不過行動服務會進行轉換，以擷取 **日期** 從 ISO 8601 字串物件。

當您使用 [tables object] 或 [mssql object], ，或者單純執行資料表指令碼，已還原序列化的 JavaScript 物件會插入您的 SQL 資料庫。 在這個程序中，物件屬性會對應到 T-SQL 類型：

JavaScript 屬性|T-SQL 類型
---|---
Number|Float(53)
Boolean|Bit
Date|DateTimeOffset(3)|
String|Nvarchar(max)
Buffer|不支援
Object|不支援
Array|不支援
Stream|不支援

###<a name="TSQL"></a>使用 Transact-SQL 存取資料表

工作資料表資料從伺服器指令碼是使用最簡單的作法 [table object] proxy。 不過，某些進階案例不支援的 [table object], ，例如聯結查詢和其他複雜的查詢、 叫用預存程序。 在這些情況下，您必須執行 TRANSACT-SQL 陳述式針對關聯式資料表直接使用 [mssql object]。 此物件提供下列函數：

- **查詢**︰ 執行由 TSQL 字串指定的查詢結果傳回給 **成功** 回呼 **選項** 物件。 查詢可以包含參數，如果 *params* 參數已存在。
- **queryRaw**︰ 像是 *查詢* 不同之處在於從查詢傳回的結果集是 「 原始 」 格式 （請參閱下面範例）。
- **開啟**︰ 用來取得您的行動服務資料庫的連接，然後您可以使用連線物件叫用資料庫作業，例如交易。

這些方法讓您對查詢過程有更多低層級的控制。

+ [作法：執行靜態查詢]
+ [作法：執行動態查詢]
+ [作法：聯結關聯式資料表]
+ [如何 ︰ 執行查詢並傳回 *原始* 結果]
+ [作法：取得對資料庫連線的存取權] 

####<a name="static-query"></a>作法：執行靜態查詢

下列查詢沒有參數，並且會傳回 `statusupdate` 資料表中的三個記錄。 資料列集為標準 JSON 格式。

        mssql.query('select top 3 * from statusupdates', {
            success: function(results) {
                console.log(results);
            },
            error: function(err) {
                console.log("error is: " + err);
            }
        });


####<a name="dynamic-query"></a>作法：執行動態參數化查詢

以下範例實作的自訂授權，是從權限資料表中讀取各個使用者的權限。 執行查詢時，會以提供的參數取代預留位置 (?)。

            var sql = "SELECT _id FROM permissions WHERE userId = ? AND permission = 'submit order'";
            mssql.query(sql, [user.userId], {
                success: function(results) {
                    if (results.length > 0) {
                        // Permission record was found. Continue normal execution. 
                        request.execute();
                    } else {
                        console.log('User %s attempted to submit an order without permissions.', user.userId);
                        request.respond(statusCodes.FORBIDDEN, 'You do not have permission to submit orders.');
                    }
                },
                error: function(err) {
                    console.log("error is: " + err);
                }   
            });


####<a name="joins"></a>作法：聯結關聯式資料表

您可以使用來聯結兩個資料表 **查詢** 方法 [mssql object] 在實作聯結的 TSQL 程式碼中傳遞。 假設我們有一些項目我們 **ToDoItem** 資料表和資料表中的每個項目都有 **優先順序** 屬性，對應到資料表中的資料行。 項目可能看來像這樣：

        { text: 'Take out the trash', complete: false, priority: 1}

再假設我們有另一個資料表 **優先順序** 其資料列包含優先順序 **數目** 和文字 **描述**。 例如，優先順序 number 為 1 且 description 為 "Critical" 的物件看起來像這樣：

        { number: 1, description: 'Critical'}

我們現在可以取代 **優先順序** 編號，在我們的項目以優先順序 number 的文字描述。 作法是將兩個資料表做關聯式聯結。

        mssql.query('SELECT t.text, t.complete, p.description FROM ToDoItem as t INNER JOIN Priority as p ON t.priority = p.number', {
            success: function(results) {
                console.log(results);
            },
            error: function(err) {
                console.log("error is: " + err);
        });
    
此指令碼會聯結兩個資料表，並將結果寫入記錄檔。 結果物件看起來像這樣：

        { text: 'Take out the trash', complete: false, description: 'Critical'}


####<a name="raw"></a>如何 ︰ 執行查詢並傳回 *原始* 結果

此範例會如同前文所述執行查詢，但傳回未經處理格式的結果集，您需要逐列逐欄剖析結果集。 可能的應用案例之一，是您需要存取行動服務不支援的資料類型。 此程式碼只會將輸出寫入主控台記錄檔，因此您可以檢查未經處理的格式。

        mssql.queryRaw('SELECT * FROM ToDoItem', {
            success: function(results) {
                console.log(results);
            },
            error: function(err) {
                console.log("error is: " + err);
            }
        });

以下是執行此查詢的輸出。 其中包含資料表各欄的中繼資料，以及資料列和欄的表示法。

        { meta: 
           [ { name: 'id',
               size: 19,
               nullable: false,
               type: 'number',
               sqlType: 'bigint identity' },
             { name: 'text',
               size: 0,
               nullable: true,
               type: 'text',
               sqlType: 'nvarchar' },
             { name: 'complete',
               size: 1,
               nullable: true,
               type: 'boolean',
               sqlType: 'bit' },
             { name: 'priority',
               size: 53,
               nullable: true,
               type: 'number',
               sqlType: 'float' } ],
          rows: 
           [ [ 1, 'good idea for the future', null, 3 ],
             [ 2, 'this is important but not so much', null, 2 ],
             [ 3, 'fix this bug now', null, 0 ],
             [ 4, 'we need to fix this one real soon now', null, 1 ],
           ] }

####<a name="connection"></a>作法：取得對資料庫連線的存取權

您可以使用 **開啟** 方法取得對資料庫連線的存取權。 這麼做的可能原因之一是您需要使用資料庫交易。

成功執行 **開啟** 會傳遞給資料庫連線 **成功** 函式做為參數。 您可以在叫用任何下列函式 **連接** 物件 ︰ *關閉*, ，*queryRaw*, ，*查詢*, ，*beginTransaction*, ，*認可*, ，和 *回復*。

            mssql.open({
                success: function(connection) {
                    connection.query(//query to execute);
                },
                error: function(err) {
                    console.log("error is: " + err);
                }
            });

##<a name="debugging"></a>偵錯與疑難排解

替伺服器指令碼偵錯和疑難排解的主要方法是寫入服務記錄檔。 依預設，行動服務會將服務指令碼執行期間發生的錯誤寫入服務記錄檔。 您的指令碼也可以寫入記錄檔。 寫入記錄檔是替指令碼偵錯並確定其行為是否如預期的好方法

###<a name="write-to-logs"></a>作法：將輸出寫入行動服務記錄檔

若要寫入記錄檔，使用全域 [console object]。 使用 **記錄** 或 **資訊** 函數記錄資訊層級的警告。  **警告** 和 **錯誤** 函數記錄其各自的多寡，這會稱為向外的記錄檔中。 

> [AZURE.NOTE] 若要檢視您的行動服務的記錄檔，請登入 [Azure 傳統入口網站](https://manage.windowsazure.com/), 選取您的行動服務，然後選擇 [ **記錄** ] 索引標籤。

您也可以使用的登入函數 [console object] 您使用參數的訊息格式化。 以下範例提供 JSON 物件做為訊息字串的參數：

    function insert(item, user, request) {
        console.log("Inserting item '%j' for user '%j'.", item, user);  
        request.execute();
    }

請注意，字串 `%j` 是用來做為 JSON 物件的預留位置，而且參數是依序提供。 

為防止登入超載，您應該移除或停用在生產中不必要的 console.log() 呼叫。

<!-- Anchors. -->
[Introduction]: #intro
[Table operations]: #table-scripts
[Basic table operations]: #basic-table-ops
[How to: Register for table operations]: #register-table-scripts
[How to: Define table scripts]: #execute-operation
[How to: override the default response]: #override-response
[How to: Modify an operation]: #modify-operation
[How to: Override success and error]: #override-success-error
[How to: Override execute success]: #override-success
[How to: Override default error handling]: #override-error
[How to: Access tables from scripts]: #access-tables
[How to: Add custom parameters]: #access-headers
[How to: Work with users]: #work-with-users
[How to: Define scheduled job scripts]: #scheduler-scripts
[How to: Refine access to tables]: #authorize-tables
[Using Transact-SQL to access tables]: #TSQL
[How to: Run a static query]: #static-query
[How to: Run a dynamic query]: #dynamic-query
[How to: Run a query that returns *raw* results]: #raw
[How to: Get access to a database connection]: #connection
[How to: Join relational tables]: #joins
[How to: Perform Bulk Inserts]: #bulk-inserts
[How to: Map JSON types to database types]: #JSON-types
[How to: Load Node.js modules]: #modules-helper-functions
[How to: Write output to the mobile service logs]: #write-to-logs
[Source control, shared code, and helper functions]: #shared-code
[Using the command line tool]: #command-prompt
[Working with tables]: #working-with-tables
[Custom API anchor]: #custom-api
[How to: Define a custom API]: #define-custom-api
[How to: Share code by using source control]: #shared-code-source-control
[How to: Use helper functions]: #helper-functions
[Debugging and troubleshooting]: #debugging
[How to: Implement HTTP methods]: #handle-methods
[How to: Work with users and headers in a custom API]: #get-api-user
[How to: Access custom API request headers]: #get-api-headers
[Job Scheduler]: #scheduler-scripts
[How to: Define multiple routes in a custom API]: #api-routes
[How to: Send and receive data as XML]: #api-return-xml
[How to: Work with app settings]: #app-settings

[1]: ./media/mobile-services-how-to-use-server-scripts/1-mobile-insert-script-users.png
[2]: ./media/mobile-services-how-to-use-server-scripts/2-mobile-custom-api-script.png
[3]: ./media/mobile-services-how-to-use-server-scripts/3-mobile-schedule-job-script.png
[4]: ./media/mobile-services-how-to-use-server-scripts/4-mobile-source-local-cli.png

<!-- URLs. -->
[Mobile Services server script reference]: http://msdn.microsoft.com/library/windowsazure/jj554226.aspx
[Schedule backend jobs in Mobile Services]: /develop/mobile/tutorials/schedule-backend-tasks/
[request object]: http://msdn.microsoft.com/library/windowsazure/jj554218.aspx
[response object]: http://msdn.microsoft.com/library/windowsazure/dn303373.aspx
[User object]: http://msdn.microsoft.com/library/windowsazure/jj554220.aspx
[push object]: http://msdn.microsoft.com/library/windowsazure/jj554217.aspx
[insert function]: http://msdn.microsoft.com/library/windowsazure/jj554229.aspx
[insert]: http://msdn.microsoft.com/library/windowsazure/jj554229.aspx
[update function]: http://msdn.microsoft.com/library/windowsazure/jj554214.aspx
[delete function]: http://msdn.microsoft.com/library/windowsazure/jj554215.aspx
[read function]: http://msdn.microsoft.com/library/windowsazure/jj554224.aspx
[update]: http://msdn.microsoft.com/library/windowsazure/jj554214.aspx
[delete]: http://msdn.microsoft.com/library/windowsazure/jj554215.aspx
[read]: http://msdn.microsoft.com/library/windowsazure/jj554224.aspx
[query object]: http://msdn.microsoft.com/library/windowsazure/jj613353.aspx
[apns object]: http://msdn.microsoft.com/library/windowsazure/jj839711.aspx
[mpns object]: http://msdn.microsoft.com/library/windowsazure/jj871025.aspx
[wns object]: http://msdn.microsoft.com/library/windowsazure/jj860484.aspx
[table object]: http://msdn.microsoft.com/library/windowsazure/jj554210.aspx
[tables object]: http://msdn.microsoft.com/library/windowsazure/jj614364.aspx
[mssql object]: http://msdn.microsoft.com/library/windowsazure/jj554212.aspx
[console object]: http://msdn.microsoft.com/library/windowsazure/jj554209.aspx
[Read and write data]: http://msdn.microsoft.com/library/windowsazure/jj631640.aspx
[Validate data]: http://msdn.microsoft.com/library/windowsazure/jj631638.aspx
[Modify the request]: http://msdn.microsoft.com/library/windowsazure/jj631635.aspx
[Modify the response]: http://msdn.microsoft.com/library/windowsazure/jj631631.aspx
[Azure classic portal]: https://manage.windowsazure.com/
[Schedule jobs]: http://msdn.microsoft.com/library/windowsazure/jj860528.aspx
[Validate and modify data in Mobile Services by using server scripts]: /develop/mobile/tutorials/validate-modify-and-augment-data-dotnet/
[Commands to manage Azure Mobile Services]: ../virtual-machines-command-line-tools.md#Mobile_Scripts
[Windows Store Push]: /develop/mobile/tutorials/get-started-with-push-dotnet/
[Windows Phone Push]: /develop/mobile/tutorials/get-started-with-push-wp8/
[iOS Push]: /develop/mobile/tutorials/get-started-with-push-ios/
[Android Push]: /develop/mobile/tutorials/get-started-with-push-android/
[Azure SDK for Node.js]: http://go.microsoft.com/fwlink/p/?LinkId=275539
[Send HTTP request]: http://msdn.microsoft.com/library/windowsazure/jj631641.aspx
[Send email from Mobile Services with SendGrid]: /develop/mobile/tutorials/send-email-with-sendgrid/
[Get started with authentication]: http://go.microsoft.com/fwlink/p/?LinkId=287177
[crypto API]: http://go.microsoft.com/fwlink/p/?LinkId=288802
[path API]: http://go.microsoft.com/fwlink/p/?LinkId=288803
[querystring API]: http://go.microsoft.com/fwlink/p/?LinkId=288804
[url API]: http://go.microsoft.com/fwlink/p/?LinkId=288805
[util API]: http://go.microsoft.com/fwlink/p/?LinkId=288806
[zlib API]: http://go.microsoft.com/fwlink/p/?LinkId=288807
[Custom API]: http://msdn.microsoft.com/library/windowsazure/dn280974.aspx
[Call a custom API from the client]: /develop/mobile/tutorials/call-custom-api-dotnet/#define-custom-api
[express.js library]: http://go.microsoft.com/fwlink/p/?LinkId=309046
[Define a custom API that supports periodic notifications]: /develop/mobile/tutorials/create-pull-notifications-dotnet/
[express object in express.js]: http://expressjs.com/api.html#express
[Store server scripts in source control]: /develop/mobile/tutorials/store-scripts-in-source-control/
[Leverage shared code and Node.js modules in your server scripts]: /develop/mobile/tutorials/store-scripts-in-source-control/#use-npm
[service object]: http://msdn.microsoft.com/library/windowsazure/dn303371.aspx
[App settings]: http://msdn.microsoft.com/library/dn529070.aspx
[config module]: http://msdn.microsoft.com/library/dn508125.aspx
[Support for package.json in Azure Mobile Services]: http://go.microsoft.com/fwlink/p/?LinkId=391036
 


