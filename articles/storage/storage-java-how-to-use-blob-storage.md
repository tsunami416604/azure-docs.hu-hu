<properties
    pageTitle="如何使用 Java 的 Azure Blob 儲存體 | Microsoft Azure"
    description="了解如何使用 Azure Blob 儲存體來上傳、下載、列出及刪除 Blob 內容。 範例以 Java 撰寫。"
    services="storage"
    documentationCenter="java"
    authors="rmcmurray"
    manager="wpickett"
    editor="jimbe"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="12/01/2015" 
    ms.author="robmcm"/>

# 如何使用 Java 的 Blob 儲存體

[AZURE.INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)]

## 概觀

本文將示範如何使用 Microsoft Azure Blob 儲存體執行一般案例。 範例會以 JAVA 和撰寫使用 [JAVA 的 Azure 存放裝置 SDK][]。 涵蓋的案例包括 **上載**, ，**列出**, ，**下載**, ，和 **刪除** blob。 如需有關 blob 的詳細資訊，請參閱 [接下來的步驟](#NextSteps) 一節。

> [AZURE.NOTE] SDK 適用於開發人員使用 Azure 存放在 Android 的裝置上。 如需詳細資訊，請參閱 [Android 的 Azure 存放裝置 SDK][]。

[AZURE.INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## 建立 Java 應用程式

在本文中，您將使用儲存體功能，這些功能可執行於本機的 Java 應用程式內，也可執行於在 Azure 中之 Web 角色或背景工作角色內執行的程式碼中。

若要這樣做，您需要安裝 Java Development Kit (JDK)，並在 Azure 訂用帳戶中建立 Azure 儲存體帳戶。 一旦您這麼做，您必須確認您開發的系統是否符合最低需求和相依性，也就列在 [JAVA 的 Azure 存放裝置 SDK][] GitHub 上的儲存機制。 如果系統符合這些需求，則您可以依照指示，從該儲存機制中下載 Azure Storage Libraries for Java 並安裝在系統上。 完成這些工作之後，您就能夠利用本文中的範例來建立 Java 應用程式。

## 設定您的應用程式以存取 Blob 儲存體

在您要使用 Azure 儲存體 API 來存取 Blob 的 Java 檔案頂端，新增下列 import 陳述式：

    // Include the following imports to use blob APIs.
    import com.microsoft.azure.storage.*;
    import com.microsoft.azure.storage.blob.*;

## 設定 Azure 儲存體連接字串

Azure 儲存用戶端都會使用儲存連接字串儲存
存取資料管理服務時所用的端點與認證。 在用戶端應用程式執行時，您必須提供儲存連接字串，以下列格式，使用 [儲存帳戶的名稱，並儲存帳戶的主要的便捷鍵會列在 [Azure 入口網站](portal.azure.com) 的 *AccountName* 和 *AccountKey* 的值。 下列範例將示範如何宣告靜態欄位來存放連接字串。

    // Define the connection-string with your values
    public static final String storageConnectionString =
        "DefaultEndpointsProtocol=http;" +
        "AccountName=your_storage_account;" +
        "AccountKey=your_storage_account_key";

在應用程式開始執行在 Microsoft Azure 中的角色中，這個字串可以儲存在服務設定檔中， *ServiceConfiguration.cscfg*, ，而且可以存取與呼叫 **RoleEnvironment.getConfigurationSettings** 方法。 Followng 範例取得的連接字串從 **設定** 元素名為 *StorageConnectionString* 服務組態檔中。

    // Retrieve storage account from connection-string.
    String storageConnectionString =
        RoleEnvironment.getConfigurationSettings().get("StorageConnectionString");

下列範例假設您已經使用這兩個方法之一來取得儲存體連接字串。

## 建立容器

A **CloudBlobClient** 物件可讓您取得容器和 blob 中的參考物件。 下列程式碼會建立 **CloudBlobClient** 物件。

> [AZURE.NOTE] 有其他的方式，來建立 **CloudStorageAccount** 物件; 如需詳細資訊，請參閱 **CloudStorageAccount** 在 [Azure Storage Client SDK Reference]。

[AZURE.INCLUDE [storage-container-naming-rules-include](../../includes/storage-container-naming-rules-include.md)]

使用 **CloudBlobClient** 以取得您想要使用的容器參考的物件。 您可以建立容器，如果沒有與 **createIfNotExists** 方法，否則會傳回現有的容器。 根據預設，新容器屬私人性質，您必須指定儲存體存取金鑰 (如先前所做) 才能從此容器下載 Blob。

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);

        // Create the blob client.
       CloudBlobClient blobClient = storageAccount.createCloudBlobClient();

       // Get a reference to a container.
       // The container name must be lower case
       CloudBlobContainer container = blobClient.getContainerReference("mycontainer");

       // Create the container if it does not exist.
        container.createIfNotExists();
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

### 選擇性步驟：設定公用存取的容器

依預設會將容器的權限設定為私人存取，但針對網際網路上的所有使用者，您可以輕鬆地將容器的權限設定為公用、唯讀存取：

    // Create a permissions object.
    BlobContainerPermissions containerPermissions = new BlobContainerPermissions();

    // Include public access in the permissions object.
    containerPermissions.setPublicAccess(BlobContainerPublicAccessType.CONTAINER);

    // Set the permissions on the container.
    container.uploadPermissions(containerPermissions);

## 將 Blob 上傳至容器

若要將檔案上傳至 Blob，請取得容器參考，並使用該參考來取得 Blob 參考。 擁有 Blob 參考後，即可在 Blob 參考上呼叫 upload，上傳任何資料流。 如果 Blob 不存在，此作業將予以建立，若已存在，則予以覆寫。 下列程式碼範例顯示此點，並假設已經建立好容器。

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);

        // Create the blob client.
        CloudBlobClient blobClient = storageAccount.createCloudBlobClient();

       // Retrieve reference to a previously created container.
        CloudBlobContainer container = blobClient.getContainerReference("mycontainer");

        // Define the path to a local file.
        final String filePath = "C:\\myimages\\myimage.jpg";

        // Create or overwrite the "myimage.jpg" blob with contents from a local file.
        CloudBlockBlob blob = container.getBlockBlobReference("myimage.jpg");
        File source = new File(filePath);
        blob.upload(new FileInputStream(source), source.length());
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## 列出容器中的 Blob

若要列出容器中的 Blob，請先取得容器參考，就像上傳 Blob 那樣。 您可以使用容器的 **listBlobs** 方法以 **的** 迴圈。 下列程式碼將容器中每個 Blob 的 URI 輸出到主控台。

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);

        // Create the blob client.
        CloudBlobClient blobClient = storageAccount.createCloudBlobClient();

        // Retrieve reference to a previously created container.
        CloudBlobContainer container = blobClient.getContainerReference("mycontainer");

        // Loop over blobs within the container and output the URI to each of them.
        for (ListBlobItem blobItem : container.listBlobs()) {
           System.out.println(blobItem.getUri());
       }
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

請注意，為 Blob 命名時，您可以在其名稱中包含路徑資訊。 這會建立虛擬目錄結構，讓您能夠組織及周遊，就像使用傳統檔案系統一樣。 請注意，目錄結構僅限虛擬目錄結構 - Blog 儲存體中唯一可用的資源為容器和 blob。 不過，用戶端程式庫提供 **CloudBlobDirectory** 物件參考到的虛擬目錄，並簡化使用這種方式組織而成的 blob 的程序。

例如，您可能有名為 "photos" 的容器，當中您可能上傳名為 "rootphoto1"、"2010/photo1"、"2010/photo2" 和 "2011/photo1" 的 Blob。 這會在 "photos" 容器內建立虛擬目錄 "2010" 和 "2011"。 當您呼叫 **listBlobs** 傳回的集合會包含 「 相片 」 容器中， **CloudBlobDirectory** 和 **CloudBlob** 物件代表包含在頂部層級的 blob 與目錄。 在此情況下，系統會傳回目錄 "2010" 和 "2011"，以及照片 "rootphoto1"。 您可以使用 **instanceof** 運算子來區分這些物件。

(選擇性) 您可以傳入參數 **listBlobs** 與方法
 **useFlatBlobListing** 參數設定為 true。 如此會導致
不論目錄為何，都會傳回每個 Blob。 如需
詳細資訊，請參閱 **CloudBlobContainer.listBlobs** 在 [Azure Storage Client SDK Reference]。

## 下載 Blob

若要下載 Blob，請遵循與上傳 Blob 相同的步驟，以取得 Blob 參考。 在上傳範例中，您對 blob 物件呼叫了 upload。 在下列範例中，呼叫要傳送 blob 內容資料流物件，例如下載 **FileOutputStream** ，您可以使用保存至本機檔案 blob。

    try
    {
        // Retrieve storage account from connection-string.
       CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);

       // Create the blob client.
       CloudBlobClient blobClient = storageAccount.createCloudBlobClient();

       // Retrieve reference to a previously created container.
       CloudBlobContainer container = blobClient.getContainerReference("mycontainer");

       // Loop through each blob item in the container.
       for (ListBlobItem blobItem : container.listBlobs()) {
           // If the item is a blob, not a virtual directory.
           if (blobItem instanceof CloudBlob) {
               // Download the item and save it to a file with the same name.
                CloudBlob blob = (CloudBlob) blobItem;
                blob.download(new FileOutputStream("C:\\mydownloads\\" + blob.getName()));
            }
        }
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## 刪除 Blob

若要刪除的 blob、 取得 blob 的參考和呼叫 **deleteIfExists**。

    try
    {
       // Retrieve storage account from connection-string.
       CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);

       // Create the blob client.
       CloudBlobClient blobClient = storageAccount.createCloudBlobClient();

       // Retrieve reference to a previously created container.
       CloudBlobContainer container = blobClient.getContainerReference("mycontainer");

       // Retrieve reference to a blob named "myimage.jpg".
       CloudBlockBlob blob = container.getBlockBlobReference("myimage.jpg");

       // Delete the blob.
       blob.deleteIfExists();
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## 刪除 Blob 容器

最後，若要刪除 Blob 容器，請取得 Blob 容器參考，然後
呼叫 **deleteIfExists**。

    try
    {
       // Retrieve storage account from connection-string.
       CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);

       // Create the blob client.
       CloudBlobClient blobClient = storageAccount.createCloudBlobClient();

       // Retrieve reference to a previously created container.
       CloudBlobContainer container = blobClient.getContainerReference("mycontainer");

       // Delete the blob container.
       container.deleteIfExists();
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## 後續步驟

了解 Blob 儲存體的基礎概念之後，請參考下列連結以了解有關更複雜的儲存工作。

- [Azure Storage SDK for Java][]
- [Azure 儲存體用戶端 SDK 參考][]
- [Azure 儲存體 REST API][]
- [Azure 儲存體團隊部落格][]

如需詳細資訊，請參閱 [JAVA 開發人員中心](/develop/java/)。

[Azure SDK for Java]: http://go.microsoft.com/fwlink/?LinkID=525671
[Azure Storage SDK for Java]: https://github.com/azure/azure-storage-java
[Azure Storage SDK for Android]: https://github.com/azure/azure-storage-android
[Azure Storage Client SDK Reference]: http://dl.windowsazure.com/storage/javadoc/
[Azure Storage REST API]: https://msdn.microsoft.com/library/azure/dd179355.aspx
[Azure Storage Team Blog]: http://blogs.msdn.com/b/windowsazurestorage/

