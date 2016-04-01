<properties
    pageTitle="如何使用 Node.js 中的 Blob 儲存體 | Microsoft Azure"
    description="了解如何使用 Azure Blob 服務來上傳、下載、列出及刪除 Blob 內容。 範例是以 Node.js 撰寫的。"
    services="storage"
    documentationCenter="nodejs"
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="nodejs"
    ms.topic="article"
    ms.date="12/01/2015"
    ms.author="robmcm"/>



# 如何使用 Node.js 的 Blob 儲存體

[AZURE.INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)]

## 概觀

本文示範如何使用 Azure Blob 服務執行一般案例。 這些範例透過 Node.js API 撰寫。 涵蓋的案例包括如何上傳、列出、下載及刪除 blob。

[AZURE.INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## 建立 Node.js 應用程式

如需有關如何建立 Node.js 應用程式的指示，請參閱 [建立並部署 Node.js 應用程式至 Azure 網站]， [Node.js 雲端服務][Node.js Cloud Service] （使用 Windows PowerShell） 或 [Web app with WebMatrix]。

## 設定您的應用程式以存取儲存體

若要使用 Azure 儲存體，您需要 Azure Storage SDK for Node.js，這包含一組便利程式庫，能與儲存體 REST 服務通訊。

### 使用 Node Package Manager (NPM) 取得封裝

1.  使用命令列介面，例如 **PowerShell** (Windows)、 **終端機** (Mac) 或 **Bash** (Unix)，瀏覽至您用來建立範例應用程式的資料夾。

2.  型別 **npm 安裝 azure 儲存體** 命令視窗中。 此命令的輸出類似下列程式碼範例。

        azure-storage@0.5.0 node_modules\azure-storage
        +-- extend@1.2.1
        +-- xmlbuilder@0.4.3
        +-- mime@1.2.11
        +-- node-uuid@1.4.3
        +-- validator@3.22.2
        +-- underscore@1.4.4
        +-- readable-stream@1.0.33 (string_decoder@0.10.31, isarray@0.0.1, inherits@2.0.1, core-util-is@1.0.1)
        +-- xml2js@0.2.7 (sax@0.5.2)
        +-- request@2.57.0 (caseless@0.10.0, aws-sign2@0.5.0, forever-agent@0.6.1, stringstream@0.0.4, oauth-sign@0.8.0, tunnel-agent@0.4.1, isstream@0.1.2, json-stringify-safe@5.0.1, bl@0.9.4, combined-stream@1.0.5, qs@3.1.0, mime-types@2.0.14, form-data@0.2.0, http-signature@0.11.0, tough-cookie@2.0.0, hawk@2.3.1, har-validator@1.8.0)

3.  您可以手動執行 **ls** 命令，確認 **node\_modules** 建立資料夾。 在該資料夾內找到 **azure 儲存體** 封裝，其中包含您要存取儲存體的程式庫。

### 匯入封裝

使用記事本或其他文字編輯器，將下列加入至頂端 **server.js** 您打算使用儲存體的應用程式檔案 ︰

    var azure = require('azure-storage');

## 設定 Azure 儲存體連接

Azure 模組會讀取環境變數 `AZURE_STORAGE_ACCOUNT` 及 `AZURE_STORAGE_ACCESS_KEY`，或讀取 `AZURE_STORAGE_CONNECTION_STRING` 以取得連接 Azure 儲存體帳戶所需的資訊。 如果未設定這些環境變數，呼叫時，您必須指定帳戶資訊 **createBlobService**。

如需範例中設定環境變數的 [Azure 入口網站](portal.azure.com) Azure web 應用程式，請參閱 [Node.js Web Application with Storage]

## 建立容器

 **BlobService** 物件可讓您能使用容器及 blob。 下列程式碼會建立 **BlobService** 物件。 加入下列的頂端附近 **server.js**:

    var blobSvc = azure.createBlobService();

> [AZURE.NOTE] 您可以使用，以匿名方式存取 blob **createBlobServiceAnonymous** 並提供主機位址。 例如，使用 `var blobSvc = azure.createBlobServiceAnonymous('https://myblob.blob.core.windows.net/');`。

[AZURE.INCLUDE [storage-container-naming-rules-include](../../includes/storage-container-naming-rules-include.md)]

若要建立新的容器，請使用 **createContainerIfNotExists**。 以下程式碼範例會建立一個名為 'mycontainer' 的新容器：

    blobSvc.createContainerIfNotExists('mycontainer', function(error, result, response){
      if(!error){
        // Container exists and allows
        // anonymous read access to blob
        // content and metadata within this container
      }
    });

如果是新建立的容器，`result` 為 true。 如果容器已存在， `result` 為 false。 `response` 包含作業有關的資訊，包括 [ETag](http://en.wikipedia.org/wiki/HTTP_ETag) 容器的資訊。

### 容器安全性

依預設，新的容器屬私人性質，無法以匿名方式存取。 若要將容器變成公開，以供匿名存取，您可以設定容器的存取層級 **blob** 或 **容器**。

* **blob** -允許匿名讀取存取的 blob 內容和中繼資料內此容器，但不是包含對容器中繼資料，例如列出容器內的所有 blob

* **容器** -允許對 blob 內容和中繼資料，以及容器中繼資料的匿名讀取存取

下列程式碼範例將示範如何設定存取層級 **blob**:

    blobSvc.createContainerIfNotExists('mycontainer', {publicAccessLevel : 'blob'}, function(error, result, response){
      if(!error){
        // Container exists and is private
      }
    });

或者，您可以修改容器的存取層級使用 **setContainerAcl** 指定的存取層級。 下列程式碼範例會將存取等級變更為 container：

    blobSvc.setContainerAcl('mycontainer', null /* signedIdentifiers */, 'container' /* publicAccessLevel*/, function(error, result, response){
      if(!error){
        // Container access level set to 'container'
      }
    });

結果會包含作業有關的資訊，包括目前 **ETag** 容器。

### 篩選器

您可以將選用的篩選作業套用至使用執行的作業 **BlobService**。 篩選作業可包含記錄、自動重試等。篩選器是使用簽章實作方法的物件：

        function handle (requestOptions, next)

在對要求選項進行前處理之後，方法需要呼叫 "next" 並傳遞具有下列簽章的回呼：

        function (returnObject, finalCallback, next)

在此回呼中，以及處理 returnObject (來自對伺服器之要求的回應) 之後，回呼需要叫用 next (如果存在) 以繼續處理其他篩選，或是直接叫用 finalCallback 結束服務叫用。

實作重試邏輯的兩個篩選器隨附於 Azure SDK for Node.js **ExponentialRetryPolicyFilter** 和 **LinearRetryPolicyFilter**。 下列範例會建立 **BlobService** 物件，使用 **ExponentialRetryPolicyFilter**:

    var retryOperations = new azure.ExponentialRetryPolicyFilter();
    var blobSvc = azure.createBlobService().withFilter(retryOperations);

## 將 Blob 上傳至容器

Blob 可以區塊或分頁為基礎。 Block 區塊可讓您更有效率地上傳大型資料，而分頁 Blob 最適合讀寫操作。 如需詳細資訊，請參閱 [了解區塊 blob 和分頁 blob](http://msdn.microsoft.com/library/azure/ee691964.aspx)。

### 區塊 Blob

若要將資料上傳至區塊 Blob，請使用下列方法：

* **createBlockBlobFromLocalFile** -建立新的區塊 blob 並上傳檔案的內容

* **createBlockBlobFromStream** -建立新的區塊 blob 並上傳的資料流的內容

* **createBlockBlobFromText** -建立新的區塊 blob 並上傳字串的內容

* **createWriteStreamToBlockBlob** -提供對區塊 blob 的寫入串流

下列程式碼範例會將上傳的內容 **test.txt** 檔案 **myblob**。

    blobSvc.createBlockBlobFromLocalFile('mycontainer', 'myblob', 'test.txt', function(error, result, response){
      if(!error){
        // file uploaded
      }
    });

 `result` 由這些方法傳回包含作業的詳細資訊，例如 **ETag** 的 blob。

### 分頁 Blob

若要將資料上傳至分頁 Blob，請使用下列方法：

* **createPageBlob** -建立特定長度的新分頁 blob

* **createPageBlobFromLocalFile** -建立新的分頁 blob 並上傳檔案的內容

* **createPageBlobFromStream** -建立新的分頁 blob 並上傳的資料流的內容

* **createWriteStreamToExistingPageBlob** -提供對現有分頁 blob 的寫入串流

* **createWriteStreamToNewPageBlob** -建立新的 blob，然後提供串流來寫入它

下列程式碼範例會將上傳的內容 **test.txt** 檔案 **mypageblob**。

    blobSvc.createPageBlobFromLocalFile('mycontainer', 'mypageblob', 'test.txt', function(error, result, response){
      if(!error){
        // file uploaded
      }
    });

> [AZURE.NOTE] 分頁 blob 是由 512 位元組的 「 分頁 」 組成。 如果上傳的資料大小不是 512 的倍數，可能會發生錯誤。

## 列出容器中的 Blob

若要列出容器中的 blob，使用 **listBlobsSegmented** 方法。 如果您想要傳回具有特定首碼的 blob，使用 **listBlobsSegmentedWithPrefix**。

    blobSvc.listBlobsSegmented('mycontainer', null, function(error, result, response){
      if(!error){
        // result.entries contains the entries
        // If not all blobs were returned, result.continuationToken has the continuation token.
      }
    });

`result` 包含 `entries` 集合，此集合是描述每個 Blob 的物件陣列。 若無法傳回所有 Blob，`result` 也會提供 `continuationToken`，其可作為第二個參數來擷取更多項目。

## 下載 Blob

若要從 Blob 下載資料，請使用下列方法：

* **getBlobToLocalFile** -將 blob 內容檔案

* **getBlobToStream** -將 blob 內容寫入資料流

* **getBlobToText** -將 blob 內容寫入字串

* **createReadStream** -提供串流來讀取 blob

下列程式碼範例示範如何使用 **getBlobToStream** 的內容下載 **myblob** blob，然後將其儲存至 **output.txt** 使用資料流的檔案 ︰

    var fs = require('fs');
    blobSvc.getBlobToStream('mycontainer', 'myblob', fs.createWriteStream('output.txt'), function(error, result, response){
      if(!error){
        // blob retrieved
      }
    });

 `result` 包含 blob 的相關資訊包括 **ETag** 資訊。

## 刪除 Blob

最後，若要刪除 blob，請呼叫 **deleteBlob**。 下列程式碼範例會刪除名為的 blob **myblob**。

    blobSvc.deleteBlob(containerName, 'myblob', function(error, response){
      if(!error){
        // Blob has been deleted
      }
    });

## 並行存取

若要支援從多個用戶端或多個處理序執行個體的並行存取 blob，您可以使用 **Etag** 或 **租用**。

* **Etag** -提供方法來偵測 blob 或容器已修改的另一個處理序

* **租用** -提供方法來取得獨佔、 可更新、 寫入或刪除 blob 的存取權的一段時間

### ETag

若您需要允許多個用戶端或執行個體同時寫入 Blob，請使用 ETag。 ETag 可讓您判斷容器或 Blob 自從您最初讀取或建立它之後是否已修改，這樣可讓您避免覆寫另一個用戶端或程序已認可的變更。

使用選用的 `options.accessConditions` 參數，可以設定 ETag 條件。 下列程式碼範例只上傳 **test.txt** 所包含的檔案，如果 blob 已存在且 ETag 值 `etagToMatch`。

    blobSvc.createBlockBlobFromLocalFile('mycontainer', 'myblob', 'test.txt', { accessConditions: { 'if-match': etagToMatch} }, function(error, result, response){
      if(!error){
        // file uploaded
      }
    });

使用 ETag 時，一般模式為：

1. 取得執行建立、列出或取得操作之後的 ETag。

2. 執行動作，檢查 ETag 值未被修改。

若值已被修改，這表示另一個用戶端或執行個體自從您取得 ETag 值之後已修改 Blob 或容器。

### 租用

您可以使用，以取得新的租用 **acquireLease** 方法，並指定您想要取得租用的 blob 或容器。 例如，下列程式碼會取得租用上 **myblob**。

    blobSvc.acquireLease('mycontainer', 'myblob', function(error, result, response){
      if(!error) {
        console.log('leaseId: ' + result.id);
      }
    });

後續操作 **myblob** 必須提供 `options.leaseId` 參數。 租用識別碼會當成 `result.id` 從 **acquireLease**。

> [AZURE.NOTE] 根據預設，租用期間會無限期。 若要指定有限期間 (15 到 60 秒)，您可以提供 `options.leaseDuration` 參數。

若要移除租用，請使用 **releaseLease**。 若要中止租用，但防止其他人取得新的租用，直到在原始期間到期為止，使用 **breakLease**。

## 使用共用存取簽章

共用存取簽章 (SAS) 可安全地提供對 Blob 和容器的精確存取，而不必提供您的儲存體帳戶名稱或金鑰。 共用存取簽章通常用來提供對資料的有限存取，例如允許行動應用程式存取 Blob。

> [AZURE.NOTE] 您也可以允許匿名存取 blob，而共用的存取簽章可讓您提供更受控制的存取，因為您必須產生 SAS。

信任的應用程式，例如雲端服務會產生共用的存取簽章使用 **generateSharedAccessSignature** 的 **BlobService**, ，並提供它給不受信任或不完全信任的應用程式等行動裝置應用程式。 共用存取簽章是使用原則來產生，該原則描述共用存取簽章有效期間的開始和結束日期，以及授與共用存取簽章持有者的存取等級。

下列程式碼範例會產生新的共用的存取原則，可讓執行讀取的作業上的共用的存取簽章持有者 **myblob** blob，並於建立它之後的 100 分鐘過期。

    var startDate = new Date();
    var expiryDate = new Date(startDate);
    expiryDate.setMinutes(startDate.getMinutes() + 100);
    startDate.setMinutes(startDate.getMinutes() - 100);

    var sharedAccessPolicy = {
      AccessPolicy: {
        Permissions: azure.BlobUtilities.SharedAccessPermissions.READ,
        Start: startDate,
        Expiry: expiryDate
      },
    };

    var blobSAS = blobSvc.generateSharedAccessSignature('mycontainer', 'myblob', sharedAccessPolicy);
    var host = blobSvc.host;

請注意，也必須提供主機資訊，因為共用存取簽章持有者嘗試存取容器時需要此資訊。

然後，用戶端應用程式會使用共用的存取簽章與 **BlobServiceWithSAS** 對 blob 執行作業。 以下會取得資訊的相關 **myblob**。

    var sharedBlobSvc = azure.createBlobServiceWithSas(host, blobSAS);
    sharedBlobSvc.getBlobProperties('mycontainer', 'myblob', function (error, result, response) {
      if(!error) {
        // retrieved info
      }
    });

由於共用存取簽章是利用唯讀存取權所產生，如果嘗試修改 blob，就會傳回錯誤。

### 存取控制清單

您也可以使用存取控制清單 (ACL) 來設定 SAS 的存取原則。 若您要允許用戶端存取容器，但對每個用戶端提供不同的存取原則，則這會很有用。

ACL 是使用存取原則陣列來實作，每個原則有相關聯的識別碼。 下列程式碼範例定義兩個原則，其中一個用於 'user1'，另一個用於 'user2'：

    var sharedAccessPolicy = [
      {
        AccessPolicy: {
          Permissions: azure.BlobUtilities.SharedAccessPermissions.READ,
          Start: startDate,
          Expiry: expiryDate
        },
        Id: 'user1'
      },
      {
        AccessPolicy: {
          Permissions: azure.BlobUtilities.SharedAccessPermissions.WRITE,
          Start: startDate,
          Expiry: expiryDate
        },
        Id: 'user2'
      }
    ];

下列程式碼範例會取得的目前 ACL **mycontainer**, ，然後加入新的原則使用 **setBlobAcl**。 此方法允許：

    blobSvc.getBlobAcl('mycontainer', function(error, result, response) {
      if(!error){
        //push the new policy into signedIdentifiers
        result.signedIdentifiers.push(sharedAccessPolicy);
        blobSvc.setBlobAcl('mycontainer', result, function(error, result, response){
          if(!error){
            // ACL set
          }
        });
      }
    });

設定 ACL 之後，您可以根據原則的識別碼來建立共用存取簽章。 下列程式碼範例會為 'user2' 建立新的共用存取簽章：

    blobSAS = blobSvc.generateSharedAccessSignature('mycontainer', { Id: 'user2' });

## 後續步驟

如需詳細資訊，請參閱下列資源：

-   [Azure Storage SDK for Node API 參考][]
-   [Azure 儲存體團隊部落格][]
-   [Azure Storage SDK for Node][] GitHub 上的儲存機制
-   [Node.js 開發人員中心](/develop/nodejs/)
-   [使用 AzCopy 命令列公用程式傳輸資料](storage-use-azcopy)

[Azure Storage SDK for Node]: https://github.com/Azure/azure-storage-node
[Create and deploy a Node.js application to an Azure Web Site]: /develop/nodejs/tutorials/create-a-website-(mac)/
[Node.js Cloud Service with Storage]: ../storage-nodejs-use-table-storage-cloud-service-app.md
[Node.js Web Application with Storage]: ../storage-nodejs-use-table-storage-web-site.md
[Web app with WebMatrix]: ../web-sites-nodejs-use-webmatrix.md
[Using the REST API]: http://msdn.microsoft.com/library/azure/hh264518.aspx
[Azure Portal]: portal.azure.com
[Node.js Cloud Service]: ../cloud-services-nodejs-develop-deploy-app.md
[Azure Storage Team Blog]: http://blogs.msdn.com/b/windowsazurestorage/
[Azure Storage SDK for Node API Reference]: http://dl.windowsazure.com/nodestoragedocs/index.html


