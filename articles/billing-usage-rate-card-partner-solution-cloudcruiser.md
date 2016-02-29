<properties
   pageTitle="Cloud Cruiser 和 Microsoft Azure 計費 API 整合 |Microsoft Azure"
   description="提供 Microsoft Azure 計費合作夥伴 Cloud Cruiser 將 Azure 計費 API 整合至其產品的經驗所得來的獨特觀點。  這特別適用於有興趣使用/嘗試將 Cloud Cruiser 用於 Microsoft Azure Pack 的客戶。"
   services="billing"
   documentationCenter=""
   authors="BryanLa"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="billing"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="billing"
   ms.date="11/02/2015"
   ms.author="mobandyo;sirishap;bryanla"/>

# Cloud Cruiser 和 Microsoft Azure 計費 API 整合

本文描述從新的 Microsoft Azure 計費 API 所收集來的資訊如何用來在 Cloud Cruiser 中進行工作流程成本模擬與分析。

## Azure RateCard API
RateCard API 提供來自 Azure 的費率資訊。 以適當的認證進行驗證之後，您可以查詢 API 以收集 Azure 上可用服務的中繼資料，以及與優惠識別碼相關聯的費率。

以下是來自 API 的範例回應，顯示 A0 (Windows) 執行個體的價格：

    {
        "MeterId": "0e59ad56-03e5-4c3d-90d4-6670874d7e29",
        "MeterName": "Compute Hours",
        "MeterCategory": "Virtual Machines",
        "MeterSubCategory": "A0 VM (Windows)",
        "Unit": "Hours",
        "MeterRates":
        {
            "0": 0.029
        },
        "EffectiveDate": "2014-08-01T00:00:00Z",
        "IncludedQuantity": 0.0
    },

### Azure RateCard API 的 Cloud Cruiser 介面
Cloud Cruiser 可以用不同的方式運用 RateCard API 資訊。 在這篇文章中，我們將說明如何使用它進行 IaaS 工作負載成本模擬及分析。

為了示範這個使用案例，請想像執行於 Microsoft Azure Pack (WAP) 之數個執行個體的工作負載。 目標是要在 Azure 上模擬相同的工作負載，並評估這類移轉的成本。 若要建立這個模擬，有兩個主要的工作要執行：

1. **匯入和處理從 RateCard API 收集的服務資訊** -在活頁簿，其中從 RateCard API 擷取轉換和發行到新的費率方案也執行這項工作。 新的費率方案將在模擬中用來評估 Azure 價格。

2. **標準化 WAP 服務和 IaaS 的 Azure 服務** -根據預設，WAP 服務以個別資源 (CPU、 記憶體大小、 磁碟大小等)，而 Azure 服務會根據執行個體大小 (A0、 A1、 A2 等等)。 第一個工作可以由 Cloud Cruiser 的 ETL 引擎執行，稱為活頁簿，其中資源整合為執行個體大小，類似 Azure 執行個體服務。

### 從 RateCard API 匯入資料

Cloud Cruiser 活頁簿提供自動化的方式收集和處理來自 RateCard API 的資訊。  ETL (擷取-轉換-載入) 活頁簿可讓您設定資料的集合、轉換和發佈至 Cloud Cruiser 資料庫。

每個活頁簿可以有一個或多個集合。 這可讓您將來自不同資源的資訊相互關聯，以補充或強化使用狀況資料。 在兩個螢幕擷取畫面，顯示 [建立新 *集合* 中現有的活頁簿，並匯入資訊到 *集合* 來自 RateCard API:

![圖 1 - 建立新的集合][1]

![圖 2 - 從新集合匯入資料][2]

將資料匯入活頁簿之後，就可以建立多個步驟及轉換程序，以修改資料與建立資料模型。 以這個範例而言，因為我們只對基礎結構即為服務 (IaaS) 感興趣，所以我們可以使用轉換步驟移除不必要的資料列或記錄，它們和服務相關，而不是 IaaS。

下方螢幕擷取畫面顯示轉換步驟，用來處理從 RateCard API 所收集的資料：

![圖 3 - 處理收集自 RateCard API 之資料的轉換步驟][3]

### 定義新的服務和費率方案

有不同的方式可定義 Cloud Cruiser 上的服務。 其中一個選項是從使用情況資料匯入服務。 使用公用雲端，其中的服務已由提供者定義時，通常會使用這個方法。

費率方案是一組費率或價格，可根據生效日期、客戶群組或其他選項套用至不同服務。 費率方案也可以在 Cloud Cruiser 上用來建立模擬或「假設」案例，以了解服務中的變更如何影響工作負載的總成本。

在此範例中，我們將使用來自 RateCard API 的服務資訊定義 Cloud Cruiser 中的新服務。 同樣地，我們可以使用和服務相關聯的費率，在 Cloud Cruiser 上建立新的費率方案。

在轉換程序結束時，就可以建立新的步驟，並將來自 RateCard API 的資料發佈做為新的服務和費率。

![圖 4- 發佈來自 RateCard API 的資料做為新的服務和費率][4]

### 確認 Azure 服務和費率

發佈服務及費率之後，您可以確認匯入的服務，在 Cloud Cruiser 清單 *服務* ] 索引標籤:

![圖 5- 驗證新的服務][5]

在 *費率方案* 索引標籤上，您可以檢查名為"AzureSimulation"的費率匯入自 RateCard API 的費率方案。

![圖 6- 驗證新的費率方案及相關聯的費率][6]

### 標準化 WAP 和 Azure 服務

根據預設，WAP 會根據計算、記憶體和網路資源等使用情況提供使用情況資訊。 在 Cloud Cruiser 中，您可以直接根據這些資源的配置或計量使用情況來定義服務。 例如，您可以設定每小時 CPU 使用量的基本費率，或為配置給執行個體的 GB 記憶體收費。

以這個範例而言，為了比較 WAP 和 Azure 之間的成本，我們必須將 WAP 上的資源使用情況彙總套組，進而將其對應至 Azure 服務。 此轉換可以在活頁簿中輕鬆地實作：

![圖 7 - 轉換 WAP 資料以正規化服務][7]

活頁簿的最後一個步驟是將資料發佈至 Cloud Cruiser 資料庫。 在此步驟期間，使用情況資料現在整合為服務 (進而對應至 Azure 服務)，並繫結至預設的費率來建立費用。

完成活頁簿之後，您可以在排程器上加入新的工作，並指定活頁簿執行的頻率和時間，即可自動化資料的處理。

![圖 8 - 活頁簿排程][8]

### 建立工作負載成本模擬分析的報告

收集使用情況並將費用載入 Cloud Cruiser 資料庫之後，我們就可以利用 Cloud Cruiser Insights 模組 – 進階的分析報告工具 – 建立我們想要的工作負載成本模擬。

為了示範這種案例，我們建立了下列報告：

![成本比較][9]

上圖顯示由服務細分的成本比較，並比較在 WAP (深藍色) 與 Azure (淺藍色) 之間為每個特定服務執行工作負載的價格。

下圖顯示相同的資料，但由部門細分，示範每個部門在 WAP 和 Azure 上執行其工作負載的成本，以及兩者之間的差異 – 節約列 (綠色)。

## Azure 使用情況 API


### 簡介

Microsoft 最近
介紹 Azure 使用量 API 允許以程式設計的方式提取訂閱者
若要深入了解其耗用量的使用量資料。 這是最棒的消息，定域機組
Cruiser 客戶可以利用更豐富的資料集
透過此 API。

Cloud Cruiser 可以數種方式運用和 Usage API 的整合。 資料粒度
(每小時的使用量資訊) 及透過可用的資源中繼資料資訊
API 提供必要的資料集支援彈性回報或
計費模型。 

在本教學課程中，我們將
目前的一個範例中的 Cloud Cruiser 如何受益使用量 API
資訊。 更具體來說，我們將在 Azure 上，建立新的資源群組
關聯帳戶結構的標記，並接著說明的程序
提取和處理 Cloud Cruiser 將標記資訊。
 
最終的目標是要能夠
若要建立類似的報表，而且能夠分析成本和
根據帳戶結構填入標記的耗用量。

![圖 10 - 具備使用標記之細項的報告][10]

### Microsoft Azure 標記

可透過 Azure Usage API 使用的資料不僅包括耗用量資訊，還包括內含於其相關聯之任何標記的資源中繼資料。 標記可提供簡單的方式組織您的資源，但是為了有效率，您必須確認：

- 標記在佈建階段正確套用至資源
- 標記正確用於回報/退款程序，以將使用情況繫結至組織的帳戶結構。

兩個需求都頗具挑戰性，特別是當佈建或收費端上有某種手動程序時。 輸入錯誤、不正確或甚至是遺漏標記，都是客戶使用標籤時的常見抱怨，這些錯誤可能會使收費端的工作非常困難。

使用新的 Azure Usage API，Cloud Cruiser 可以提取資源標記資訊，並透過非常複雜的 ETL 工具 (稱為活頁簿) 修正這些常見的標記錯誤。 透過利用規則運算式和資料的轉換步驟
相互關聯，Cloud Cruiser 就能識別不正確地加上標記的資源，並套用正確的標記，填滿間距，以及確保資源的取用者使用正確的關聯。

在充電端，
Cloud Cruiser 自動化回報/計費程序，而且會將
將繫結至適當的消費者 (部門使用的標記資訊
部門、 專案等)。 這項自動化提供非常重大的改進，並可以
確保一致且可稽核的充電程序。
 

### 使用 Microsoft Azure 上的標記建立資源群組
本教學課程的第一個步驟是在 Azure 入口網站上建立新的資源群組，然後建立新的標記以產生和資源之間的關聯。 此範例中，我們將建立
下列標記: 部門，環境中，擁有者、 專案。

以下 Azure 入口網站的螢幕擷取畫面會顯示具有相關聯標記的範例資源群組。

![圖 11 - 在 Azure 入口網站上具有相關聯標記的資源群組][11]

下一步是將資訊從 Usage API 提取到 Cloud Cruiser。 Usage API 目前提供 JSON 格式的回應。 擷取的資料範例如下：


    {
      "id": "/subscriptions/bb678b04-0e48-4b44-XXXX-XXXXXXX/providers/Microsoft.Commerce/UsageAggregates/Daily_BRSDT_20150623_0000",
      "name": "Daily_BRSDT_20150623_0000",
      "type": "Microsoft.Commerce/UsageAggregate",
      "properties":
      {
        "subscriptionId": "bb678b04-0e48-4b44-XXXX-XXXXXXXXX",
        "usageStartTime": "2015-06-22T00:00:00+00:00",
        "usageEndTime": "2015-06-23T00:00:00+00:00",
        "meterName": "Compute Hours",
        "meterRegion": "",
        "meterCategory": "Virtual Machines",
        "meterSubCategory": "Standard_D1 VM (Non-Windows)",
        "unit": "Hours",
        "instanceData": "{\"Microsoft.Resources\":{\"resourceUri\":\"/subscriptions/bb678b04-0e48-4b44-XXXX-XXXXXXXX/resourceGroups/DEMOUSAGEAPI/providers/Microsoft.Compute/virtualMachines/MyDockerVM\",\"location\":\"eastus\",\"tags\":{\"Department\":\"Sales\",\"Project\":\"Demo Usage API\",\"Environment\":\"Test\",\"Owner\":\"RSE\"},\"additionalInfo\":{\"ImageType\":\"Canonical\",\"ServiceType\":\"Standard_D1\"}}}",
        "meterId": "e60caf26-9ba0-413d-a422-6141f58081d6",
        "infoFields": {},
        "quantity": 8

      },
    },


### 將資料從 Usage API 匯入到 Cloud Cruiser

Cloud Cruiser 活頁簿提供自動化的方式收集和處理來自 Usage API 的資訊。 ETL (擷取-轉換-載入) 活頁簿可讓您設定
集合、 轉換和資料發佈至 Cloud Cruiser 資料庫。

每個活頁簿可以有一個或多個集合。 這可讓您將來自不同資源的資訊相互關聯，以補充或強化使用狀況資料。 針對此範例中，我們將 Azure 範本活頁簿中建立新的工作表 (_UsageAPI)_ ，並設定新 _集合_ 使用量 API 從匯入資訊。

![圖 3 - 匯入至 UsageAPI 工作表的 Usage API 資料][12]

請注意此活頁簿已有其他工作表，從 Azure 匯入服務 (_ImportServices_)，並處理從計費 API 耗用量資訊 (_PublishData_)。

我們要擷取及處理使用量 API 中的資訊上 _UsageAPI_ 表，然後建立相互關聯性的資訊與從計費 API 的耗用量資料 _PublishData_ 工作表。

### 處理來自 Usage API 的標記資訊

資料匯入活頁簿之後，我們將建立中的轉換步驟 _UsageAPI_ 工作表，以便處理從 API 的資訊。 第一個步驟是使用 「 JSON 分割 」 的處理器標記擷取的單一欄位 (如在匯入從 API)，並針對其中每一個建立新的欄位 (部門、 專案中，擁有者和
環境)。

![圖 4 - 建立新的標記資訊欄位][13]

請注意，「 網路功能 」 服務遺失標記資訊 (黃色方塊)，但我們可以告訴這項服務是相同的資源群組的一部分，藉由查看 _ResourceGroupName_ 欄位。 由於我們已經擁有相同資源群組中其他資源的標記，所以稍後我們可以在程序中使用這項資訊，將遺漏的標記套用至此資源。

下一個步驟是建立查閱資料表關聯至標記中的資訊 _ResourceGroupName_。 下一個步驟將使用此查閱資料表，以利用標記資訊充實消耗量資料。

### 將標記資訊加入至消耗量資料

現在我們可以跳到 _PublishData_ 工作表，其處理耗用量資訊從計費的 API，並加入從標記擷取的欄位。 藉由查看上一個步驟中，建立查閱資料表執行此程序使用 _資源群組名稱_
做為查閱索引鍵。

![圖 5 - 利用來自查閱的資訊填入帳戶結構中][14]

請注意，已套用「網路」服務的適當帳戶結構欄位，利用遺漏標記修正問題。 我們也會填入帳戶結構
我們使用 「 其他 」，以區別它們在報表上的目標資源群組之外的資源欄位。

現在我們只需要加入另一個步驟，即可發佈使用情況資料。 在此步驟中，我們的費率方案上定義每個服務的適當速度將會套用使用方式
詳細資訊，以及產生的費用會載入至資料庫。

最棒的部分是您只需要完成此程序一次。 完成活頁簿，您只需要將它加入至排程器會每小時執行或每日
排定的時間。 這樣就只需建立新的報表，或自訂
若要以視覺化方式檢視和分析資料，以取得有意義的現有的
深入了解您定域機組的使用量。

### 後續步驟

+ 如需建立 Cloud Cruiser 活頁簿和報告的詳細指示，請參閱 Cloud cruiser 的線上 [文件](http://docs.cloudcruiser.com/) (所需的有效登入)。  如需有關 Cloud Cruiser 的詳細資訊，請連絡 [info@cloudcruiser.com](mailto:info@cloudcruiser.com)。
+ 請參閱 [深入了解您的 Microsoft Azure 資源耗用量](billing-usage-rate-card-overview.md) Azure 資源使用情況和 RateCard Api 的概觀。
+ 簽出 [Azure 計費 REST API 參考](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c) 如需有關這兩個 Api 的詳細資訊，這是 Azure 資源管理員所提供的 Api 集的一部分。
+ 如果您想要探究範例程式碼，請參閱我們的 Microsoft Azure 計費 API 程式碼範例在 [Azure 程式碼範例](https://azure.microsoft.com/documentation/samples/?term=billing)。

### 詳細資訊
+ 請參閱 [Azure 資源管理員概觀](resource-group-overview.md) 文件，以深入了解 Azure 資源管理員。

<!--Image references-->
 
[1]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Create-New-Workbook-Collection.png "Figure 1 - Creating a new collection"
[2]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Import-Data-From-RateCard.png "Figure 2 - Import data from the new collection"
[3]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Transformation-Steps-Process-RateCard-Data.png "Figure 3 - Transformation steps to process collected data from RateCard API"
[4]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Publish-RateCard-Data-New-Services-Rates.png "Figure 4 - Publishing the data from the RateCard API as new Services and Rates"
[5]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Verify-Azure-Services-And-Pricing1.png "Figure 5 - Verifying the new Services"
[6]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Verify-Azure-Services-And-Pricing2.png "Figure 6 - Verifying the new Rate Plan and associated rates"
[7]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Transforming-WAP-Normalize-Services.png "Figure 7 - Transforming WAP data to normalize services"
[8]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Workbook-Scheduling.png "Figure 8 - Workbook scheduling"
[9]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Workload-Cost-Simulation-Report.png "Figure 9 - Sample Report for the Workload cost comparison scenario"
[10]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/1_ReportWithTags.png "Figure 10 - Report with breakdowns using tags"
[11]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/2_ResourceGroupsWithTags.png "Figure 11 - Resource Group with associated tags on Azure Portal"
[12]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/3_ImportIntoUsageAPISheet.png "Figure 12 - Usage API data imported into the UsageAPI sheet"
[13]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/4_NewTagField.png "Figure 13 - Create new fields for the tag information"
[14]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/5_PopulateAccountStructure.png "Figure 14 - Populating the account structure with the information from the lookups"

