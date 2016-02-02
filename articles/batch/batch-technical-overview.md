<properties
    pageTitle="Azure Batch 服務基本概念 | Microsoft Azure"
    description="了解適用於大規模的平行和 HPC 工作負載之 Azure Batch 服務的概念、工作流程及案例"
    services="batch"
    documentationCenter=""
    authors="dlepow"
    manager="timlt"
    editor=""/>

<tags
    ms.service="batch"
    ms.workload="big-compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="11/19/2015"
    ms.author="danlep"/>


# Azure Batch 的基本概念

Azure 批次可協助您在雲端中，有效執行大規模的平行和高效能運算 (HPC) 應用程式。 它是一項平台服務，可排程要在受管理的虛擬機器 (計算節點) 集合上執行的計算密集型工作，而且可以調整計算資源以符合工作的需求。 利用 Batch 服務，您可以程式設計方式定義 Azure 計算資源以及隨需求或依排程執行的大型批次工作，而不需要手動設定和管理 HPC 叢集、個別的 VM、虛擬網路或工作排程器。

## 使用案例

Batch 是一項受管理的服務，可用於「批次處理」**或「批次運算」**- 執行大量的類似工作以得到期望的結果。 在依排程或隨需求而處理、轉換和分析大量資料的組織中，批次運算是一種常見的模式，其範例涵蓋於金融服務以至工程設計等領域。

批次很適合處理本質上平行 (有時稱為「窘迫平行」) 的應用程式或工作負載，而這些本身就適合在多部電腦上以平行工作執行。 請參閱 [圖 1]。

![平行工作][parallel]

* * [圖 1。 在多個電腦 * * 上執行的平行工作

範例包括：

* 財務風險模型
* 影像轉譯和影像處理
* 媒體編碼及轉碼
* 基因序列分析
* 軟體測試

Batch 也可以執行平行計算 (最後加上歸納步驟)，以及其他更複雜的平行工作負載。
>[AZURE.NOTE]在此階段，Batch 只支援在 Windows Server 型虛擬機器執行的工作負載。 此外，批次目前不支援訊息傳遞介面 (MPI) 應用程式。

與其他在 Azure 中的 HPC 解決方案選項的批次比較，請參閱 [批次和 HPC 解決方案](batch-hpc-solutions.md)。

## 使用 Batch 進行開發

使用 Batch API 進行開發，以建立和管理計算節點集區，以及排程在其上執行的作業及工作。 撰寫用戶端應用程式或前端來隨選、 依照排程，或做為較大的工作流程，例如管理工具的一部分執行的作業和工作 [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/)。

請參閱 [Azure 批次功能概觀](batch-api-basics.md) 如需詳細資訊的批次概念。

### 您必須備妥的帳戶

+ **Azure 帳戶和訂閱** -如果您沒有帳戶，則可以啟用您 [MSDN 訂閱者權益](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) 或申請 [免費試用版](http://azure.microsoft.com/pricing/free-trial/)。

+ **Batch 帳戶** - 您在進行 Batch API 呼叫時，會使用 Batch 帳戶的名稱和 URL 以及存取金鑰做為認證。 所有 Batch 資源 (如計算節點、集區、工作) 都與 Batch 帳戶相關聯。 其中一種方式建立批次帳戶及管理存取金鑰，該帳戶是使用 [Azure 入口網站](batch-account-create-portal.md)。

+ **儲存體帳戶** – 對於大部分的 Batch 案例，您需要有 Azure 儲存體帳戶來儲存資料輸入和輸出以及在計算節點上執行的指令碼或可執行檔。 若要建立儲存體帳戶，請參閱 [關於 Azure 儲存體帳戶](../storage/storage-create-storage-account.md)。

### Batch 開發程式庫和工具

搭配 Visual Studio 使用這些 .NET 程式庫和工具來開發和管理 Azure Batch 應用程式。

+ [批次用戶端.NET 程式庫](http://www.nuget.org/packages/Azure.Batch/) (NuGet) – 開發用戶端應用程式，以透過批次服務執行工作
+ [批次管理.NET 程式庫](http://www.nuget.org/packages/Microsoft.Azure.Management.Batch/) (NuGet) – 管理 「 批次帳戶
+ [批次總管](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer) (GitHub)-建置瀏覽此 GUI 應用程式、 存取和更新資源，批次帳戶，包括作業和工作，在計算節點和集區和計算節點上的檔案。 See the [blog post](http://blogs.technet.com/b/windowshpc/archive/2015/01/20/azure-batch-explorer-sample-walkthrough.aspx).


## 案例：相應放大平行工作負載

Batch API 的常見案例包括相應放大本質平行工作，例如計算節點集區上的影像轉譯最多可提供數千個計算核心。

圖 2 顯示使用 Batch .NET 用戶端應用程式來執行 Batch 平行工作負載的工作流程。


![工作項目工作流程][work_item_workflow]

* * [圖 2。 相應放大平行工作負載在批次 * *

1.  將作業所需的輸入檔 (例如來源資料或影像) 上傳至 Azure 儲存體帳戶。 執行工作時，Batch 服務會將檔案載入到計算節點。

2.  將要在計算節點上執行的程式檔案或指令碼上傳至儲存體帳戶。 這些可能包括二進位檔案及其相依的組件。 執行工作時，Batch 服務也會將這些檔案載入到計算節點。

3.  以程式設計方式建立您的 Batch 帳戶中 Batch 計算節點的集區，並指定屬性，例如其大小及其執行的作業系統。 您也可以定義如何數目的集區中的節點 [擴充或縮小](batch-automatic-scaling.md) 以回應工作負載。 執行工作時，系統會從這個集區指派節點。

4.  以程式設計方式定義 Batch 工作，以在節點集區上執行工作負載。

5.  將工作加入至作業。 每個工作會使用您已上傳的程式，以處理您上傳的檔案中的資訊。 根據您的工作負載，您可能想要執行 [多個工作同時](batch-parallel-node-tasks.md) 資源使用率最大化每個運算節點上。 批次也支援特殊 [作業準備和完成工作](batch-job-prep-release.md) 排定的工作執行前準備運算節點，以及之後清除。

6.  執行 Batch 工作負載及監視進度和結果。 此應用程式會透過 HTTPS 與 Batch 服務通訊。 若要改善應用程式效能，監視大量的集區、 工作和工作時，利用 [有效率的技巧，來查詢](batch-efficient-list-queries.md) 批次服務。






## 後續步驟

* 開始開發第一個應用程式與 [批次用戶端.NET 程式庫](batch-dotnet-get-started.md)
* 瀏覽批次程式碼範例，在 [GitHub](https://github.com/Azure/azure-batch-samples)


[parallel]: ./media/batch-technical-overview/parallel.png 
[work_item_workflow]: ./media/batch-technical-overview/work_item_workflow.png 

