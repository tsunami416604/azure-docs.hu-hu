<properties 
    pageTitle="在五分鐘內開始使用 Azure 儲存體 | Microsoft Azure" 
    description="使用 Azure 快速入門、Visual Studio 和 Azure 儲存體模擬器，快速掌握 Microsoft Azure Blob、資料表和佇列。 在五分鐘內執行第一個 Azure 儲存體應用程式" 
    services="storage" 
    documentationCenter=".net" 
    authors="tamram" 
    manager="adinah" 
    editor=""/>

<tags 
    ms.service="storage" 
    ms.workload="storage" 
    ms.tgt_pltfrm="na" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="11/12/2015" 
    ms.author="tamram;selcint"/>

# 在五分鐘內開始使用 Azure 儲存體 

## 概觀

Azure SDK 包含「快速啟動」範本，方便從 Microsoft Visual Studio 使用 Azure 儲存體。 您可以 

對 Azure 儲存體開始進行開發相當容易。 本教學課程示範如何取得 Azure 儲存體應用程式，並快速執行。 
我們將示範兩種案例，用以輕鬆加強 Azure 儲存體：

如果您想要深入了解 Azure 儲存體一頭栽進程式碼前，請參閱 [下一步](#next-steps)。

## 必要條件

請確定您已備妥這些必要條件，然後再開始：

1. 若要編譯及建置應用程式，您需要的版本 [Visual Studio](https://www.visualstudio.com/) 安裝在電腦上。 
2. 安裝最新版 [Azure SDK for.NET](http://azure.microsoft.com/downloads/)。 SDK 包含 Azure 快速入門範例專案、 Azure 儲存體模擬器中，而 [Azure Storage Client Library for.NET](https://msdn.microsoft.com/library/azure/wa_storage_30_reference_home.aspx)。
3. 請確定您有 [.NET Framework 4.5](http://www.microsoft.com/download/details.aspx?id=30653) 安裝您的電腦上，因為它為我們在本教學課程將使用 Azure 快速入門範例專案所需。 如果您不確定您的電腦中安裝.NET Framework 版本，請參閱 [How to ︰ 判斷安裝的.NET Framework 版本是](https://msdn.microsoft.com/vstudio/hh925568.aspx)。 或者，按 **啟動** 按鈕或 Windows 鍵，輸入 **控制台**。 然後按一下 [ **程式** > **程式和功能**, ，並判斷已安裝的程式之間是否列出.NET Framework 4.5。

最新版的 Azure 儲存體用戶端程式庫二進位檔位於 [NuGet](https://www.nuget.org/packages/WindowsAzure.Storage/)。


## 針對 Azure 儲存體模擬器在本機執行第一個 Azure 儲存體應用程式

在開發使用 Azure 儲存體的應用程式時，您可以在對執行 [Azure 儲存體模擬器](storage-use-emulator.md)。 此儲存體模擬器提供了模擬 Azure Blob、佇列和資料表服務的本機環境，以供進行開發。 您可以使用儲存體模擬器在本機測試儲存體應用程式，而不需建立 Azure 訂用帳戶或儲存體帳戶，也不會產生任何費用。

若要試用，讓我們在 Visual Studio 中使用其中一個 Azure 快速啟動範例專案，建立簡單的 Azure 儲存體應用程式。 本教學課程著重於 **Azure Blob 儲存體**, ，**Azure 資料表儲存體**, ，和 **Azure 佇列儲存體** 範例專案 ︰

1. 啟動 Visual Studio。
2. 從 **檔案** ] 功能表上，按一下 [ **新的專案**。
3. 在 **新的專案** 對話方塊中，按一下 [ **已安裝** > **範本** > **Visual C#** > **定域機組** > **[快速入門** > **Data Services**。
    - 3.a.  選擇下列其中一個範本：Azure Blob 儲存體、Azure 資料表儲存體或 Azure 儲存體佇列。 
    - 3.b. 請確定 **.NET Framework 4.5** 選取作為目標架構。   
    - 3.c. 指定您專案的名稱，並建立新的 Visual Studio 方案，如下所示：
    
    ![Azure 快速入門][Image1]

您可能想要在執行應用程式前檢閱原始程式碼。 若要檢閱程式碼，請選取 **方案總管] 中** 上 **檢視** Visual Studio 中的功能表。 然後，按兩下 Program.cs 檔案。 

接下來，在 Azure 儲存體模擬器中執行範例應用程式：

1.  按下 **啟動** 按鈕或 Windows 鍵，搜尋 *Azure 儲存體模擬器*, ，並啟動應用程式。
2.  在 Visual Studio 中，按一下 [ **建置方案** 上 **建置** 功能表。 
3.  在 **偵錯** 功能表上，按 **F11** 執行逐步解說，此方案，或按 **F5** 從開始到結束執行方案。

## 針對雲端 Azure 儲存體執行第一個 Azure 儲存體應用程式

若要在雲端中針對 Azure 儲存體執行，您將需要 Azure 訂用帳戶和儲存體帳戶 (如果您還沒有的話)： 

- 若要取得 Azure 訂用帳戶，請參閱 [免費試用版](http://azure.microsoft.com/pricing/free-trial/), ，[購買選項](http://azure.microsoft.com/pricing/purchase-options/), ，和 [會員優惠](http://azure.microsoft.com/pricing/member-offers/) （適用於 MSDN、 Microsoft Partner Network、 BizSpark 和其他 Microsoft 程式的成員）。
- 若要在 Azure 中建立儲存體帳戶，請參閱 [如何建立、 管理或刪除儲存體帳戶](storage-create-storage-account.md)。

一旦您有了帳戶，您就可以在 Visual Studio 中使用其中一個 Azure 快速入門範例專案，建立簡單的 Azure 儲存體應用程式。 本教學課程著重於 **Azure Blob 儲存體**, ，**Azure 資料表儲存體**, ，和 **Azure 佇列儲存體** 範例專案 ︰

1. 啟動 Visual Studio。
2. 從 **檔案** ] 功能表上，按一下 [ **新的專案**。
3. 在 **新的專案** 對話方塊中，按一下 [ **已安裝** > **範本** > **Visual C#** > **定域機組** > **[快速入門** > **Data Services**。
    - 3.a. 選擇下列其中一個範本：Azure Blob 儲存體、Azure 資料表儲存體或 Azure 儲存體佇列。
    - 3.b. 請確定 **.NET Framework 4.5** 選取作為目標架構。
    - 3.c. 指定您專案的名稱，並建立新的 Visual Studio 方案。 

您可能想要在執行應用程式前檢閱原始程式碼。 若要檢閱程式碼，請選取 **方案總管] 中** 上 **檢視** Visual Studio 中的功能表。 然後，按兩下 Program.cs 檔案。 

現在，執行範例應用程式：

1.  在 Visual Studio 中，選取 **方案總管] 中** 上 **檢視** 功能表。 按兩下 App.config 檔案並註解化 Azure SDK 儲存體模擬器的連接字串：

    `<!--<add key="StorageConnectionString" value = "UseDevelopmentStorage=true;"/>-->`

2.  取消註解 Azure 儲存體服務的連接字串，並提供 App.config 檔案中的儲存體帳戶名稱和存取金鑰：
    `<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=[AccountName];AccountKey=[AccountKey]"`

    若要尋找儲存體帳戶名稱和存取金鑰，請參閱 [什麼是儲存體帳戶](storage-whatis-account.md)。

3.  提供儲存體帳戶名稱和存取金鑰在 App.config 檔案中之後在, **檔案** ] 功能表上，按一下 [ **全部儲存** 儲存所有的專案檔案。
4.  在 **建置** ] 功能表上，按一下 [ **建置方案**。
5.  在 **偵錯** 功能表上，按 **F11** 執行逐步解說的方案，或按 **F5** 執行方案。


## 後續步驟

若要深入了解 Azure 儲存體，請參閱下列資源：

* [Microsoft Azure 儲存體簡介](storage-introduction.md)
* [如何使用 .NET 的 Blob 儲存體](storage-dotnet-how-to-use-blobs.md)
* [如何使用 .NET 的資料表儲存體](storage-dotnet-how-to-use-tables.md)
* [如何使用 .NET 的佇列儲存體](storage-dotnet-how-to-use-queues.md)
* [使用 AzCopy 命令列公用程式傳輸資料](storage-use-azcopy)
* [Azure 儲存體文件](http://azure.microsoft.com/documentation/services/storage/)
* [Azure 儲存體用戶端程式庫](https://msdn.microsoft.com/library/azure/wa_storage_30_reference_home.aspx)
* [Azure 儲存體 REST API](https://msdn.microsoft.com/library/azure/dd179355.aspx)

[Image1]: ./media/storage-getting-started-guide/QuickStart.png
 


