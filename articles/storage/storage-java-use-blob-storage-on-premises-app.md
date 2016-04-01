<properties
    pageTitle="搭配 Blob 儲存體 (Java) 的內部部署應用程式 | Microsoft Azure"
    description="了解如何建立可將影像上傳至 Azure，然後在瀏覽器中顯示此影像的主控台應用程式。 程式碼範例以 Java 撰寫。"
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

# 搭配 Blob 儲存體的內部部署應用程式

## 概觀

下列範例說明如何使用 Azure 儲存體
在 Azure 中儲存影像。 這篇文章中的程式碼適用於主控台
應用程式將影像上傳到 Azure 然後建立
HTML 檔案在瀏覽器中顯示影像。

## 必要條件

- 已安裝 Java Developer Kit (JDK) 1.6 版或更新版本。
- 已安裝 Azure SDK。
- Azure libraries for Java 和任何適用 JAR
    相依性 Jar，已安裝，且所使用的組建路徑中位於
    Java 編輯器。 如需安裝 Azure Libraries for Java 的詳細資訊，請參閱 [下載
   Azure SDK for Java][]。
- 已設定 Azure 儲存體帳戶。 帳戶名稱
    程式碼將使用儲存體帳戶的帳戶金鑰
    在這篇文章。 請參閱 [How to Create a Storage Account] 如需建立儲存體帳戶，
    和 [如何管理儲存體帳戶][] 如需擷取資訊
    帳戶金鑰。
- 您已建立名為的本機影像檔案儲存於路徑
    c:\\myimages\\image1.jpg。 或者，修改
    **FileInputStream** 在範例中，若要使用不同的建構函式
    影像路徑和檔案名稱。

[AZURE.INCLUDE [create-account-note](../../includes/create-account-note.md)]

## 使用 Azure Blob 儲存體上傳檔案

以下為逐步程序。 如果您想要跳過，
在本文後面提供完整的程式碼。

程式碼一開始先匯入 Azure 核心儲存體
類別、Azure Blob 用戶端類別、Java IO 類別和
 **URISyntaxException** 類別。

    import com.microsoft.azure.storage.*;
    import com.microsoft.azure.storage.blob.*;
    import java.io.*;
    import java.net.URISyntaxException;

宣告類別，名為 **StorageSample**, ，並包含左括號
**{**.

    public class StorageSample {

內 **StorageSample** 類別中，宣告字串變數，
包含預設端點通訊協定、您的儲存體帳戶名稱，以及
您的儲存體存取金鑰 (如同 Azure 儲存體中所指定)
。 取代預留位置值 **your\_account\_name** 和
**your\_account\_key** 以您自己的帳戶名稱和帳戶金鑰
。

    public static final String storageConnectionString =
           "DefaultEndpointsProtocol=http;" +
               "AccountName=your_account_name;" +
               "AccountKey=your_account_name";

在您的宣告中加入 **主要**, ，包括 **嘗試** 區塊，和
包含必要的左括號， **{**。

    public static void main(String[] args)
    {
        try
        {

宣告下列類型的變數 (該描述說明如何
用於此範例)：

-   **CloudStorageAccount**︰ 用來初始化帳戶物件，
    您的 Azure 儲存體帳戶名稱和金鑰，並建立
    blob 用戶端物件。
-   **CloudBlobClient**︰ 用來存取 blob 服務。
-   **CloudBlobContainer**︰ 用來建立 blob 容器，清單
    容器和刪除容器中的 blob。
-   **CloudBlockBlob**︰ 用來上傳至本機影像檔案
    。

<!-- -->

    CloudStorageAccount account;
    CloudBlobClient serviceClient;
    CloudBlobContainer container;
    CloudBlockBlob blob;

指派值給 **帳戶** 變數。

    account = CloudStorageAccount.parse(storageConnectionString);

指派值給 **serviceClient** 變數。

    serviceClient = account.createCloudBlobClient();

指派值給 **容器** 變數。 我們將取得
容器名稱 **gettingstarted**。

    // Container name must be lower case.
    container = serviceClient.getContainerReference("gettingstarted");

// 建立容器。 如果不是，這個方法會建立容器
存在 (並傳回 **true**)。 如果容器存在，則會傳回
**false**。 除了 **createIfNotExists** 是 **建立**
方法 （如果容器已存在，則會傳回錯誤）。

    container.createIfNotExists();

設定容器的匿名存取。

    // Set anonymous access on the container.
    BlobContainerPermissions containerPermissions;
    containerPermissions = new BlobContainerPermissions();
    containerPermissions.setPublicAccess(BlobContainerPublicAccessType.CONTAINER);
    container.uploadPermissions(containerPermissions);

取得區塊 Blob 的參考，此參考將可代表
Azure 儲存體中的 Blob。

    blob = container.getBlockBlobReference("image1.jpg");

使用 **檔案** 建構函式來取得在本機建立的參考
您將上傳的檔案。 請確定您已建立此檔案之前
執行程式碼。

    File fileReference = new File ("c:\\myimages\\image1.jpg");

透過呼叫本機檔案上傳 **CloudBlockBlob.upload**
方法將程式碼部署至服務。 第一個參數 **CloudBlockBlob.upload** 方法
**FileInputStream** 物件，代表將本機檔案
上傳至 Azure 儲存體。 第二個參數的大小是在
檔案的位元組。

    blob.upload(new FileInputStream(fileReference), fileReference.length());

呼叫 helper 函式，名為 **MakeHTMLPage**, 來製作基本的 HTML 頁面
其中包含 **& lt; 映像 & gt;** 元素，且來源設定的 blob，
現在為 Azure 儲存體帳戶。 程式碼
**MakeHTMLPage** 將在本文稍後討論。

    MakeHTMLPage(container);

列印關於已建立之 HTML 頁面的狀態訊息和相關資訊。

    System.out.println("Processing complete.");
    System.out.println("Open index.html to see the images stored in your storage account.");

關閉 **嘗試** 區塊透過插入右括號 ︰ **}**

處理下列例外狀況：

-   **FileNotFoundException**︰ 可能擲回 **FileInputStream**
    或 **FileOutputStream** 建構函式。
-   **StorageException**: Azure 用戶端可以擲回
    儲存體程式庫。
-   **URISyntaxException**︰ 可能擲回 **ListBlobItem.getUri**
    方法將程式碼部署至服務。
-   **例外狀況**︰ 一般例外狀況處理。

<!-- -->

    catch (FileNotFoundException fileNotFoundException)
    {
        System.out.print("FileNotFoundException encountered: ");
        System.out.println(fileNotFoundException.getMessage());
        System.exit(-1);
    }
    catch (StorageException storageException)
    {
        System.out.print("StorageException encountered: ");
        System.out.println(storageException.getMessage());
        System.exit(-1);
    }
    catch (URISyntaxException uriSyntaxException)
    {
        System.out.print("URISyntaxException encountered: ");
        System.out.println(uriSyntaxException.getMessage());
        System.exit(-1);
    }
    catch (Exception e)
    {
        System.out.print("Exception encountered: ");
        System.out.println(e.getMessage());
        System.exit(-1);
    }

關閉 **主要** 透過插入右括號 ︰ **}**

建立一個名為方法 **MakeHTMLPage** 建立基本的 HTML 頁面。 此
方法包含參數的型別 **CloudBlobContainer**, ，而這將是
用來逐一查看上傳的 blob 清單。 這個方法會
擲回例外狀況型別的 **FileNotFoundException**, ，這可能會擲回
由 **FileOutputStream** 建構函式，和 **URISyntaxException**,，
這可能會擲回由 **ListBlobItem.getUri** 方法。 包含
左括號 **{**。

    public static void MakeHTMLPage(CloudBlobContainer container) throws FileNotFoundException, URISyntaxException
    {

建立名為的本機檔案 **index.html**。

    PrintStream stream;
    stream = new PrintStream(new FileOutputStream("index.html"));

寫入本機檔案，加入 **& lt; html 和 gt;**, ，**& lt; 標頭及 gt;**, ，和
**& l t; 本文 & gt;** 項目。

    stream.println("<html>");
    stream.println("<header/>");
    stream.println("<body>");

逐一取得已上傳的 Blob 清單。 為每個 blob，在 HTML
頁面上建立 **& lt; i m g （& s) gt;** 具有項目，其 **src** 屬性傳送至
Azure 儲存體帳戶存在於與 blob 的 URI。
雖然您加入只有一個映像在此範例中，如果您加入的詳細資訊，
此程式碼會重複列舉全部影像。

為了方便起見，此範例假設每個已上傳的 Blob 是一張影像。 If
您已更新除了影像以外的 blob 或分頁 blob，而不是區塊
blob，視需要調整程式碼。

    // Enumerate the uploaded blobs.
    for (ListBlobItem blobItem : container.listBlobs()) {
    // List each blob as an <img> element in the HTML body.
    stream.println("<img src='" + blobItem.getUri() + "'/><br/>");
    }

關閉 **& lt; 本文 & gt;** 項目和 **（& s) lt; html 和 gt;** 項目。

    stream.println("</body>");
    stream.println("</html>");

關閉本機檔案。

    stream.close();

關閉 **MakeHTMLPage** 透過插入右括號 ︰ **}**

關閉 **StorageSample** 透過插入右括號 ︰ **}**

下列是此範例的完整程式碼。 請記得修改
預留位置值 **your\_account\_name** 和
**your\_account\_key** 以便使用您的帳戶名稱和帳戶金鑰
。

    import com.microsoft.azure.storage.*;
    import com.microsoft.azure.storage.blob.*;
    import java.io.*;
    import java.net.URISyntaxException;

    // Create an image, c:\myimages\image1.jpg, prior to running this sample.
    // Alternatively, change the value used by the FileInputStream constructor
    // to use a different image path and file that you have already created.
    public class StorageSample {

        public static final String storageConnectionString =
                "DefaultEndpointsProtocol=http;" +
                       "AccountName=your_account_name;" +
                       "AccountKey=your_account_name";

        public static void main(String[] args) {
            try {
                CloudStorageAccount account;
                CloudBlobClient serviceClient;
                CloudBlobContainer container;
                CloudBlockBlob blob;

                account = CloudStorageAccount.parse(storageConnectionString);
                serviceClient = account.createCloudBlobClient();
                // Container name must be lower case.
                container = serviceClient.getContainerReference("gettingstarted");
                container.createIfNotExists();

                // Set anonymous access on the container.
                BlobContainerPermissions containerPermissions;
                containerPermissions = new BlobContainerPermissions();
                containerPermissions.setPublicAccess(BlobContainerPublicAccessType.CONTAINER);
                container.uploadPermissions(containerPermissions);

                // Upload an image file.
                blob = container.getBlockBlobReference("image1.jpg");

                File fileReference = new File("c:\\myimages\\image1.jpg");
                blob.upload(new FileInputStream(fileReference), fileReference.length());

                // At this point the image is uploaded.
                // Next, create an HTML page that lists all of the uploaded images.
                MakeHTMLPage(container);

                System.out.println("Processing complete.");
                System.out.println("Open index.html to see the images stored in your storage account.");

            } catch (FileNotFoundException fileNotFoundException) {
                System.out.print("FileNotFoundException encountered: ");
                System.out.println(fileNotFoundException.getMessage());
                System.exit(-1);
            } catch (StorageException storageException) {
                System.out.print("StorageException encountered: ");
                System.out.println(storageException.getMessage());
                System.exit(-1);
            } catch (URISyntaxException uriSyntaxException) {
                System.out.print("URISyntaxException encountered: ");
                System.out.println(uriSyntaxException.getMessage());
                System.exit(-1);
            } catch (Exception e) {
                System.out.print("Exception encountered: ");
                System.out.println(e.getMessage());
                System.exit(-1);
            }
        }

        // Create an HTML page that can be used to display the uploaded images.
        // This example assumes all of the blobs are for images.
        public static void MakeHTMLPage(CloudBlobContainer container) throws FileNotFoundException, URISyntaxException
        {
            PrintStream stream;
            stream = new PrintStream(new FileOutputStream("index.html"));

            // Create the opening <html>, <header>, and <body> elements.
            stream.println("<html>");
            stream.println("<header/>");
            stream.println("<body>");

            // Enumerate the uploaded blobs.
            for (ListBlobItem blobItem : container.listBlobs()) {
                // List each blob as an <img> element in the HTML body.
                stream.println("<img src='" + blobItem.getUri() + "'/><br/>");
            }

            stream.println("</body>");

            // Complete the <html> element and close the file.
            stream.println("</html>");
            stream.close();
        }
    }

除了將本機影像檔案上傳至 Azure 儲存體，
範例程式碼會建立本機檔案 namedindex.html，您可以
若要查看您已上傳的映像瀏覽器中開啟。

由於此程式碼包含您的帳戶名稱和帳戶金鑰，可確保
您的程式碼是安全的。

## 刪除容器

由於您需支付儲存體，您可能想要刪除
**gettingstarted** 容器後您完成體驗此
範例。 若要刪除容器，請使用 **CloudBlobContainer.delete**
方法將程式碼部署至服務。

    container = serviceClient.getContainerReference("gettingstarted");
    container.delete();

若要呼叫 **CloudBlobContainer.delete** 方法、 的程序
初始化 **CloudStorageAccount**, ，**ClodBlobClient**, ，和 **CloudBlobContainer** 物件的方式相同，如所示
**createIfNotExist** 方法。 下列是完整的範例，
會刪除名為容器 **gettingstarted**。

    import com.microsoft.azure.storage.*;
    import com.microsoft.azure.storage.blob.*;

    public class DeleteContainer {

        public static final String storageConnectionString =
                "DefaultEndpointsProtocol=http;" +
                   "AccountName=your_account_name;" +
                   "AccountKey=your_account_key";

        public static void main(String[] args)
        {
            try
            {
                CloudStorageAccount account;
                CloudBlobClient serviceClient;
                CloudBlobContainer container;

                account = CloudStorageAccount.parse(storageConnectionString);
                serviceClient = account.createCloudBlobClient();
                // Container name must be lower case.
                container = serviceClient.getContainerReference("gettingstarted");
                container.delete();

                System.out.println("Container deleted.");

            }
            catch (StorageException storageException)
            {
                System.out.print("StorageException encountered: ");
                System.out.println(storageException.getMessage());
                System.exit(-1);
            }
            catch (Exception e)
            {
                System.out.print("Exception encountered: ");
                System.out.println(e.getMessage());
                System.exit(-1);
            }
        }
    }

如需其他 blob 儲存體類別和方法的概觀，請參閱 [How to
使用 Java 的 blob 儲存體服務]。

## 後續步驟

請遵循下列連結以深入了解更複雜的儲存體工作。

- [Azure Storage SDK for Java][]
- [Azure 儲存體用戶端 SDK 參考][]
- [Azure 儲存體 REST API][]
- [Azure 儲存體團隊部落格][]

  [Download the Azure SDK for Java]: http://go.microsoft.com/fwlink/?LinkID=525671
  [How to Create a Storage Account]: storage-create-storage-account.md#create-a-storage-account
  [How to Manage Storage Accounts]: storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys
  [How to Use the Blob Storage Service from Java]: storage-java-how-to-use-blob-storage.md
  [Azure Storage SDK for Java]: https://github.com/azure/azure-storage-java
  [Azure Storage Client SDK Reference]: http://dl.windowsazure.com/storage/javadoc/
  [Azure Storage Team Blog]: http://blogs.msdn.com/b/windowsazurestorage/


