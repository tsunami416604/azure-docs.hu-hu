<properties
    pageTitle="在 Windows 市集應用程式中使用 Azure 儲存體 | Microsoft Azure"
    description="了解如何使用 Azure Blob、佇列和資料表來儲存 Windows 市集應用程式的資料。"
    services="storage"
    documentationCenter=""
    authors="tamram"
    manager="adinah" />
<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="mobile-windows-store"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="09/03/2015"
    ms.author="tamram"/>
# 如何在 Windows 市集應用程式中使用 Azure 儲存體

## 概觀

本指南說明如何開始著手開發採用 Azure 儲存體的 Windows 市集應用程式。

## 下載所需工具

- [Visual Studio 2012](http://msdn.microsoft.com/library/windows/apps/br211384) 輕鬆地建置、 偵錯、 當地語系化、 封裝和部署 Windows 市集應用程式。
- [Azure Storage Client Library for Windows Runtime](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/11/05/windows-azure-storage-client-library-for-windows-runtime.aspx) 類別庫提供使用 Azure 儲存體。
- [WCF 資料服務的 Windows 市集應用程式工具](http://www.microsoft.com/download/details.aspx?id=30714) 延伸的 Visual Studio 2012 及更新版本的 Windows 市集應用程式的用戶端 OData 支援新增服務參照體驗。

## 開發應用程式

### 準備就緒

在 Visual Studio 2012 或更新版本中建立新的 Windows 市集應用程式專案：

![store-apps-storage-vs-project][store-apps-storage-vs-project]

接下來，新增 Azure 儲存體用戶端程式庫的參考，以滑鼠右鍵按一下 **參考**, 、 **加入參考**, ，然後瀏覽至儲存體用戶端程式庫的 Windows 執行階段您下載:

![store-apps-storage-choose-library][store-apps-storage-choose-library]

### 使用搭配 Blob 和佇列服務的程式庫

此時您的應用程式已準備好可呼叫 Azure Blob 和佇列服務。 新增下列 **使用** 陳述式，以便可以直接參考 Azure 儲存體類型:

    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;

接著，新增頁面按鈕。 加入下列程式碼，以其 **按一下** 事件使用及修改事件處理常式方法的 [async 關鍵字](http://msdn.microsoft.com/library/vstudio/hh156513.aspx):

    var credentials = new StorageCredentials(accountName, accountKey);
    var account = new CloudStorageAccount(credentials, true);
    var blobClient = account.CreateCloudBlobClient();
    var container = blobClient.GetContainerReference("container1");
    await container.CreateIfNotExistsAsync();

此程式碼假設您有兩個字串變數 *accountName* 和 *accountKey*。 它們代表儲存體帳戶和該帳戶相關聯之帳戶金鑰的名稱。

建置並執行應用程式。 按一下按鈕，將會檢查容器是否命名 *container1* 存在於您的帳戶，而且如果沒有的話則建立。

### 使用搭配資料表服務的程式庫

用來與 Azure 資料表服務通訊的類型會視 Windows 市集應用程式程式庫適用的 WCF 資料服務而定。 接著，透過使用 Package Manager Console 加入所需的 WCF 程式庫參考：

![store-apps-storage-package-manager][store-apps-storage-package-manager]

使用下列命令將 [Package Manager] 指向您機器上的位置：

    Install-Package Microsoft.Data.OData.WindowsStore -Source "C:\Program Files (x86)\Microsoft WCF Data Services\5.0\bin\NuGet"

此命令會自動將所有所需的參考加入您的專案。 若不想使用封裝管理員主控台，您可以將 WCF Data Services NuGet 資料夾在本機電腦新增至套件來源清單，然後再新增透過 UI，參考中, 所述 [使用對話方塊管理 NuGet 封裝](http://docs.nuget.org/docs/start-here/Managing-NuGet-Packages-Using-The-Dialog)。

當您參考 WCF 資料服務 NuGet 封裝時，請變更您的按鈕中的程式碼 **按一下** 事件:

    var credentials = new StorageCredentials(accountName, accountKey);
    var account = new CloudStorageAccount(credentials, true);
    var tableClient = account.CreateCloudTableClient();
    var table = tableClient.GetTableReference("table1");
    await table.CreateIfNotExistsAsync();

此程式碼會檢查是否有資料表 *table1* 存在於您的帳戶，然後建立它，如果不是。

您也可以加入 Microsoft.WindowsAzure.Storage.Table.dll (您可以在下載的相同封裝中找到) 的參考。 此程式庫包含其他功能，例如反映式序列化和一般查詢。 請注意，此程式庫不支援 JavaScript。



[store-apps-storage-vs-project]: ./media/storage-use-store-apps/store-apps-storage-vs-project.png
[store-apps-storage-choose-library]: ./media/storage-use-store-apps/store-apps-storage-choose-library.png
[store-apps-storage-package-manager]: ./media/storage-use-store-apps/store-apps-storage-package-manager.png

