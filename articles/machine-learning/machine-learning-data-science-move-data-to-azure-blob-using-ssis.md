<properties 
    pageTitle="使用 SSIS 連接器的 Azure Blob 儲存體來回移動資料 |Microsoft Azure" 
    description="使用 SSIS 連接器的 Azure Blob 儲存體來回移動資料。" 
    services="machine-learning,storage" 
    documentationCenter="" 
    authors="bradsev" 
    manager="paulettm" 
    editor="cgronlun" />

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/28/2015" 
    ms.author="bradsev" />


# 使用 SSIS 連接器的 Azure Blob 儲存體來回移動資料

以下是有關從 Azure Blob 儲存體來回移動資料所使用之技術的指引連結：

[AZURE.INCLUDE [blob-storage-tool-selector](../../includes/machine-learning-blob-storage-tool-selector.md)]

## 簡介

[SQL Server Integration Services 功能套件適用於 Azure](https://msdn.microsoft.com/library/mt146770.aspx) 提供元件連接到 Azure，Azure 和內部部署資料來源和處理資料儲存在 Azure 中間傳送資料。

客戶將內部部署資料移至雲端後，便能從任何 Azure 服務存取該資料，以利用 Azure 技術套件的完整功能。 例如，可用在 Azure 機器學習服務或 HDInsight 叢集中。

這通常會是第一步 [SQL](https://azure.microsoft.com/documentation/articles/machine-learning-data-science-process-sql-walkthrough/) 和 [HDInsight](https://azure.microsoft.com/documentation/articles/machine-learning-data-science-process-hive-walkthrough/) 逐步解說。

如需使用 SSIS 完成混合式資料整合案例中常見的商務需求的標準案例的討論，請參閱 [這麼做，以 SQL Server Integration Services 功能套件適用於 Azure](http://blogs.msdn.com/b/ssis/archive/2015/06/25/doing-more-with-sql-server-integration-services-feature-pack-for-azure.aspx) 部落格。
> [AZURE.NOTE] 如需 Azure blob 儲存體的完整介紹，請參閱 [Azure Blob 基本概念](../storage-dotnet-how-to-use-blobs.md) 和  [Azure Blob 服務](https://msdn.microsoft.com/library/azure/dd179376.aspx)。 

## 必要條件

若要執行本文中所述的工作，您必須具有 Azure 訂用帳戶和設定 Azure 儲存體帳戶。 您必須知道您 Azure 儲存體帳戶名稱和帳戶金鑰才能上傳或下載資料。

- 若要設定 **Azure 訂用帳戶**, ，請參閱 [免費試用一個月](https://azure.microsoft.com/pricing/free-trial/)。
- 如需建立 **儲存體帳戶** 以及取得帳戶和金鑰資訊，請參閱 [關於 Azure 儲存體帳戶](../storage-create-storage-account.md)。


若要使用 **SSIS 連接器**，您必須下載:

- **SQL Server 2014 或 2016 標準版 (或更新版本)**：安裝包含 SQL Server 整合服務。
- **Microsoft SQL Server 2014 或 2016 Integration Services 功能套件適用於 Azure**: 可以下載這些，分別從 [SQL Server 2014 Integration Services](http://www.microsoft.com/download/details.aspx?id=47366) 和 [SQL Server 2016 Integration Services](https://www.microsoft.com/download/details.aspx?id=49492) 頁面。

> [AZURE.NOTE] SSIS 會隨同 SQL Server 安裝，但未包含在 Express 版本中。 如需各種 SQL Server 版本中包含哪些應用程式資訊，請參閱 [SQL Server 版本](http://www.microsoft.com/en-us/server-cloud/products/sql-server-editions/)

有關 SSIS 的教育訓練資料，請參閱 [手上的 SSIS 訓練](http://www.microsoft.com/download/details.aspx?id=20766)

如需如何取得最多執行使用建置簡單的擷取、 轉換和載入 (ETL) 封裝，請參閱 SISS [SSIS 教學課程: 建立簡易 ETL 封裝](https://msdn.microsoft.com/library/ms169917.aspx)。

## 下載紐約計程車資料集

此處所描述的範例使用的公開可用的資料集- [NYC 計程車車程](http://www.andresmh.com/nyctaxitrips/) 資料集。 此資料集收集了 2013 年紐約 1 億 7 千 3 百萬筆計程車行程資料。 資料有兩種：路線詳細資料及車費資料。 由於每個月都有一個檔案，因此總共有 24 個檔案，每個檔案未壓縮的大小約 2GB。


## 將資料上傳至 Azure Blob 儲存體

若要移動資料使用 SSIS 功能套件從內部部署至 Azure blob 儲存體，我們使用的執行個體 [* * Azure Blob 上傳工作 * *](https://msdn.microsoft.com/library/mt146776.aspx), ，如下所示:

![configure-data-science-vm](./media/machine-learning-data-science-move-data-to-azure-blob-using-ssis/ssis-azure-blob-upload-task.png)


此工作使用的參數如下所述：


 欄位| 說明|
----------------------|----------------|
 **AzureStorageConnection**| 指定現有的或建立新的 Azure 儲存體連線管理員，以參照 Azure 儲存體帳戶；該帳戶指向託管 Blob 檔案的位置。|
 **BlobContainer**| 指定將保存上傳的檔案作為 Blob 的 Blob 容器名稱。|
 **BlobDirectory**| 指定上傳的檔案將儲存在哪一個 Blob 目錄中以儲存成區塊 Blob。Blob 目錄是虛擬的階層式結構。如果 Blob 已經存在，則會被取代。|
 **LocalDirectory**| 指定包含要上傳的檔案的本機目錄。|
 **檔名**| 指定名稱篩選器以選取採用指定名稱模式的檔案。例如，MySheet\*。 xls\* 包含檔案，例如 MySheet001.xls 和 MySheetABC.xlsx|
 **TimeRangeFrom/TimeRangeTo**| 指定時間範圍篩選器。將包含在 *TimeRangeFrom* 後和 *TimeRangeTo* 前修改的檔案。|

> [AZURE.NOTE] **AzureStorageConnection** 認證必須更正且 **BlobContainer** 必須存在，才能嘗試傳輸。

## 從 Azure Blob 儲存體下載資料

若要從 Azure blob 儲存體下載資料，與 SSIS 的內部儲存體，請使用的執行個體 [Azure Blob 上傳工作](https://msdn.microsoft.com/library/mt146779.aspx)。

## 更進階的 SSIS Azure 案例

我們注意到 SSIS 功能封裝允許封裝工作一起處理更複雜的流程。 例如，Blob 資料可直接饋送到 HDInsight 叢集，其輸出可以回頭下載到 Blob，然後下載到內部部署儲存體。 SSIS 可使用其他 SSIS 連接器在 HDInsight 叢集上執行 Hive 與 Pig 工作：

- 若要執行 Hive 指令碼與 SSIS 的 Azure HDInsight 叢集上，使用 [Azure HDInsight Hive 工作](https://msdn.microsoft.com/library/mt146771.aspx)。
- 若要與 SSIS 的 Azure HDInsight 叢集上執行 Pig 指令碼，使用 [Azure HDInsight Pig 工作](https://msdn.microsoft.com/library/mt146781.aspx)。





