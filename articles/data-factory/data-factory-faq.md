<properties 
    pageTitle="Azure 資料處理站-常見問題集" 
    description="關於 Azure Data Factory 的常見問題。" 
    services="data-factory" 
    documentationCenter="" 
    authors="spelluru" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="data-factory" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="12/01/2015" 
    ms.author="spelluru"/>

# Azure 資料處理站-常見問題集

## 一般問題

### Azure 資料處理站是什麼？

Data Factory 是雲端架構資料整合服務，用來協調以及自動移動和轉換資料。 就像製造廠運轉設備來取得原物料並將之轉換成成品一樣，Data Factory 會協調現有服務的流程來收集原始資料，並將之轉換成隨時可用的資訊。 

Data Factory 會在內部部署、雲端資料來源和 SaaS 之間運作，以便擷取、準備、轉換、分析及發行您的資料。  使用 Data Factory 將服務撰寫到受管理的資料流程管線來轉換資料類 [Azure HDInsight (Hadoop)](http://azure.microsoft.com/documentation/services/hdinsight/) 和 [Azure 批次](http://azure.microsoft.com/documentation/services/batch/) 巨量資料的運算需求，以及與 [Azure Machine Learning](http://azure.microsoft.com/documentation/services/machine-learning/) 將您的分析解決方案的運作。  不僅僅是表格式的監視檢視，還使用了豐富的視覺效果呈現 Data Factory，來快速顯示歷程和資料管線之間的相依性。 從單一的統一檢視來監視所有的資料流程管線，輕鬆找出問題並設定監視警示。

請參閱 [概觀與重要概念](data-factory-introduction.md) 如需詳細資訊。 
 
### Data Factory 可解決何種客戶難題？

Azure 資料處理站藉由完全受管理之服務的控制和監視功能，可在與非結構化資料並存的傳統關聯式儲存體中，平衡且靈活運用不同資料儲存體、處理和搬移服務。

### 資料處理站的目標對象是誰？


- 資料開發人員：負責建置 Hadoop 與其他系統之間的整合服務：
    - 必須掌握及整合不斷變動和成長的資料環境
    - 必須撰寫自訂程式碼的資訊產品，它是昂貴、 難以維護，且不是高度可用或容錯

- IT 專業人員：想要將更多不同資料併入其 IT 基礎結構內：
    - 必須觀察組織內所有資料以衍生出豐富的業務深入見解
    - 必須跨越內部部署和雲端來管理計算資源和儲存體資源，以平衡成本和規模
    - 必須快速加入多個來源和處理動作以處理新的商務需求，同時維護所有計算資產和儲存體資產的可見度

### 哪裡可以找到 Azure 資料處理站的定價詳細資料？

請參閱 [Data Factory 定價詳細資料頁面][adf-pricing-details] 以 Azure Data Factory 定價詳細資料。  

### 如何開始使用 Azure Data Factory？

- 如需 Azure Data Factory 的概觀，請參閱 [簡介 Azure Data Factory][adf-introduction]。
- 如需快速教學課程，請參閱 [開始使用 Azure Data Factory][adfgetstarted]。
- 如需完整的文件，請參閱 [Azure Data Factory 文件][adf-documentation-landingpage]。

  
### 客戶如何存取資料處理站？

客戶可以取得存取 Data Factory 透過 [Azure 入口網站][azure-portal]。

### 什麼是資料處理站的區域可用性？

Data Factory 可在美國西部和北歐地區使用。 資料處理站所使用的計算服務和儲存體服務可以在其他區域使用。
 
### 資料處理站/管線/活動/資料集的數量有什麼限制？
 
請參閱 **Azure Data Factory 限制** 區段 [Azure 訂用帳戶和服務限制、 配額和條件約束](../azure-subscription-service-limits.md#data-factory-limits) 文件。


### Azure Data Factory 服務的撰寫/開發人員經驗為何？

您可以使用下列其中一項來撰寫/建立資料處理站：

- **Azure 入口網站**。 Azure 入口網站中的 Data Factory 刀鋒視窗提供豐富的使用者介面，讓您建立資料處理站和連結的服務。  **Data Factory 編輯器**, ，也是屬於入口網站中，讓您輕鬆地透過指定成品的 JSON 定義中建立連結的服務、 資料表、 資料集和管線。 請參閱 [Data Factory 編輯器][data-factory-editor] 編輯器的概觀和 [開始使用 Data Factory][datafactory-getstarted] 如需使用入口網站/編輯器來建立和部署 data factory 的範例。   
- **Azure PowerShell**。 如果您是 PowerShell 使用者，而且想要使用 PowerShell，而不是入口網站 UI，則可以使用 Azure PowerShell 隨附的 Azure Data Factory Cmdlet 來建立和部署資料處理站。 請參閱 [建立和監視 Azure Data Factory 使用 Azure PowerShell][create-data-factory-using-powershell] 取得簡單範例和 [教學課程: 移動和處理記錄檔使用 Data Factory][adf-tutorial] 的進階範例的使用 PowerShell cmdlet 建立和部署 data factory。 請參閱 [Data Factory Cmdlet 參考][adf-powershell-reference] MSDN Library 上的 Data Factory cmdlet 的完整文件內容。  
- **Visual Studio**。 您也可以使用 Visual Studio，以程式設計方式建立、監視及管理 Data Factory。 請參閱 [建立、 監視和管理 Azure data factory 使用 Data Factory.NET SDK](data-factory-create-data-factories-programmatically) 文章以取得詳細資料。  
- **.NET 類別庫**。 您可以使用 Data Factory .NET SDK，透過程式設計方式建立資料處理站。 請參閱 [建立、 監視和管理 data factory 使用.NET SDK][create-factory-using-dotnet-sdk] 為建立使用.NET SDK 的 data factory 的逐步解說。 請參閱 [Data Factory 類別庫參考][msdn-class-library-reference] 的 Data Factory.NET SDK 的完整文件。  
- **REST API**。 您也可以使用 Azure Data Factory 服務所公開的 REST API 來建立和部署資料處理站。 請參閱 [Data Factory REST API 參考][msdn-rest-api-reference] 的 Data Factory REST API 的完整文件。 

### 我是否可以重新命名資料處理站？
編號 如同其他 Azure 資源，無法變更 Azure data factory 的名稱。 

## 活動 - 常見問題集
### 有什麼可支援的資料來源和活動？

請參閱 [資料移動活動](data-factory-data-movement-activities.md) 和 [資料轉換活動](data-factory-data-transformation-activities.md) 支援的資料來源和活動的文章。  

### 何時執行活動？
 **可用性** 輸出資料表中的組態設定決定何時執行活動。 活動會檢查是否滿足所有輸入的資料相依性 (亦即， **準備** 狀態) 開始執行之前。

## 複製活動 - 常見問題集
### 最好是一個管線有多個活動，還是每個活動都有不同的管線？ 
管線依例應該有配套的相關活動。  邏輯上，如果管線外部的任何其他活動都未使用連接活動的資料表，則可以將活動保留在一個管線中。 如此一來，您就不需要鏈結管線作用期間，使其彼此一致。 此外，更新管線時，也會更適當地保留管線內部資料表中的資料完整性。 管線更新基本上會停止、移除並重新建立管線內的所有活動。 從撰寫觀點來看，可能也較容易看出管線的某個 JSON 檔案中相關活動內的資料流程。 

## HDInsight 活動 - 常見問題集

### HDInsight 支援哪些區域？

請參閱下列文件中的各地區上市: 或 [HDInsight 定價詳細資料][hdinsight-supported-regions]。

### 隨選 HDInsight 叢集使用哪一個區域？

隨選 HDInsight 叢集會建立在存有您指定用來使用叢集之儲存體的位置。    

### 如何讓其他儲存體帳戶與 HDInsight 叢集產生關聯？

如果使用專屬 HDInsight 叢集 (BYOC - 自備叢集 (Bring Your Own Cluster))，請參閱下列主題： 

- [搭配使用 HDInsight 叢集與替代儲存體帳戶和中繼存放區][hdinsight-alternate-storage]
- [搭配使用其他儲存體帳戶與 HDInsight Hive][hdinsight-alternate-storage-2]

如果您使用 Data Factory 服務所建立的隨選叢集，則需要指定 HDInsight 連結服務的其他儲存體帳戶，讓 Data Factory 服務代表您註冊它們。 在隨選連結服務 JSON 定義中，使用 **additionalLinkedServiceNames** 屬性來指定替代的儲存體帳戶，如下列 JSON 片段所示:
 
    {
        "name": "MyHDInsightOnDemandLinkedService",
        "properties":
        {
            "type": "HDInsightOnDemandLinkedService",
            "clusterSize": 1,
            "timeToLive": "00:01:00",
            "linkedServiceName": "LinkedService-SampleData",
            "additionalLinkedServiceNames": [ "otherLinkedServiceName1", "otherLinkedServiceName2" ] 
        }
    } 

在上述範例中，otherLinkedServiceName1 和 otherLinkedServiceName2 連結服務的定義，包含 HDInsight 叢集存取替代儲存體帳戶所需的認證。

## 配量 - 常見問題集

### 如何重新執行配量？
您可以利用下列方式之一來重新執行配量： 

- 按一下 [ **執行** 的命令列中 **資料配量** 入口網站中的配量] 分頁。 
- 執行 **組 AzureRmDataFactorySliceStatus** 指令程式的狀態設定為 **PendingExecution** 配量。   
    
        Set-AzureRmDataFactorySliceStatus -Status PendingExecution -ResourceGroupName $ResourceGroup -DataFactoryName $df -TableName $table -StartDateTime "02/26/2015 19:00:00" -EndDateTime "02/26/2015 20:00:00" 

請參閱 [組 AzureRmDataFactorySliceStatus][set-azure-datafactory-slice-status] 如需有關此指令程式的詳細資訊。 

### 處理配量需要多久的時間？
1. 按一下 [ **資料集** 磚 **DATA FACTORY** data factory] 分頁。
2. 按一下特定的資料集 **資料集** 刀鋒視窗。
3. 選取您感興趣的配量 **最近的配量** 清單 **資料表** 刀鋒視窗。
4. 按一下活動執行從 **活動執行** 清單 **資料配量** 刀鋒視窗。 
5. 按一下 [ **屬性** 磚 **活動執行詳細資料** 刀鋒視窗。 
6. 您應該會看到 **持續時間** 欄位與值。 這是處理配量所需的時間。   

### 如何停止執行中配量？
如果您需要停止執行管線，您可以使用 [暫止 AzureRmDataFactoryPipeline](https://msdn.microsoft.com/library/mt603721.aspx) 指令程式。 目前，擱置管線並不會停止正在進行的配量執行。 一旦進行中的執行完成，就不會再挑選任何額外的配量。

如果真的想要立即停止所有執行作業，唯一的方法就是刪除管線，然後再重新建立。 如果您選擇刪除管線，則「不」需要刪除管線所使用的資料表和連結服務。 



[image-rerun-slice]: ./media/data-factory-faq/rerun-slice.png

[adfgetstarted]: data-factory-get-started.md
[adf-introduction]: data-factory-introduction.md
[adf-troubleshoot]: data-factory-troubleshoot.md
[data-factory-editor]: data-factory-editor.md
[datafactory-getstarted]: data-factory-get-started.md
[create-data-factory-using-powershell]: data-factory-monitor-manage-using-powershell.md
[adf-tutorial]: data-factory-tutorial.md
[create-factory-using-dotnet-sdk]: data-factory-create-data-factories-programmatically.md
[msdn-class-library-reference]: https://msdn.microsoft.com/library/dn883654.aspx
[msdn-rest-api-reference]: https://msdn.microsoft.com/library/dn906738.aspx

[adf-powershell-reference]: https://msdn.microsoft.com/library/dn820234.aspx 
[adf-documentation-landingpage]: http://go.microsoft.com/fwlink/?LinkId=516909
[azure-portal]: http://portal.azure.com
[set-azure-datafactory-slice-status]: https://msdn.microsoft.com/library/mt603522.aspx

[adf-pricing-details]: http://go.microsoft.com/fwlink/?LinkId=517777
[hdinsight-supported-regions]: http://azure.microsoft.com/pricing/details/hdinsight/
[hdinsight-alternate-storage]: http://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx
[hdinsight-alternate-storage-2]: http://blogs.msdn.com/b/cindygross/archive/2014/05/05/use-additional-storage-accounts-with-hdinsight-hive.aspx
 

