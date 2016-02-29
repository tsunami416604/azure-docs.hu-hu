<properties 
    pageTitle="使用 Azure Data Factory 移動和處理記錄檔 (Azure 傳統入口網站)" 
    description="此進階教學課程說明接近現實情況的案例，並使用 Azure 傳統入口網站中的 Azure Data Factory 服務和 Data Factory 編輯器實作案例。" 
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
    ms.date="11/12/2015" 
    ms.author="spelluru"/>

# 教學課程：衡量行銷活動的成效  
Contoso 是為多個平台建立遊戲的遊戲公司：遊戲主機、手持裝置與個人電腦 (PC)。 這些遊戲會產生大量的記錄檔，Contoso 的目標是要收集並分析這些記錄檔，以深入瞭解客戶的喜好設定、族群結構特徵、使用行為等，以發掘向上銷售與交叉銷售機會，開發新的強大功能，以期推動業務成長並為客戶提供更好的體驗。

在本教學課程中，您將建立 Data Factory 管線，透過收集範例記錄檔、處理並以參考資料加以充實，然後轉換資料，以評估 Contoso 最近展開的行銷活動的成效。 有下列三個管線：

1.   **PartitionGameLogsPipeline** 從 blob 儲存體讀取原始遊戲事件，並根據年、 月和日建立分割區。
2.   **EnrichGameLogsPipeline** 聯結分割的遊戲事件與地區代碼參考資料和 IP 位址對應到相對應的地理位置來充實資料。
3.   **AnalyzeMarketingCampaignPipeline** 管線運用已充實的資料，並處理與廣告資料來建立包含行銷活動成效的最終輸出。

## 準備開始教學課程
1.  讀取 [簡介 Azure Data Factory][adfintroduction] 來取得 Azure Data Factory 的概觀並了解最高階的概念。
2.  您必須擁有 Azure 訂閱，才能執行本教學課程。 如需取得訂用帳戶的詳細資訊，請參閱 [購買選項](http://azure.microsoft.com/pricing/purchase-options/), ，[成員優惠](http://azure.microsoft.com/pricing/member-offers/), ，或 [免費試用版](http://azure.microsoft.com/pricing/free-trial/)。
3.  您必須下載並安裝 [PowerShell][download-azure-powershell] 電腦上。 您將會執行 Data Factory Cmdlet 將範例資料和 Pig/Hive 指令碼上傳至 Blob 儲存體。 
2.  **(建議選項)** 檢閱並練習中的教學課程 [開始使用 Azure Data Factory][adfgetstarted] 課程來熟悉入口網站和 cmdlet 的簡易教學課程的文件。
3.  **(建議選項)** 檢閱並練習中的逐步解說 [使用 Pig 和 Hive 搭配 Azure Data Factory][usepigandhive] 文章的逐步解說建立管線，將資料從內部部署資料來源移至 Azure blob 存放區。
4.  下載 [ADFWalkthrough][adfwalkthrough-download] 檔案到 **C:\ADFWalkthrough** 資料夾 **並保留資料夾結構**:
    - **管線:** 它包含 JSON 檔案，其中包含管線的定義。
    - **資料表:** 它包含 JSON 檔案，其中包含資料表的定義。
    - **LinkedServices:** 包含 JSON 檔案包含定義您的儲存體和計算 (HDInsight) 叢集 
    - **指令碼:** 它包含 Hive 和 Pig 指令碼，用於處理資料並從管線叫用
    - **SampleData:** 包含在這個逐步解說的範例資料
    - **OnPremises:** 包含 JSON 檔案和指令碼，用於示範存取內部部署資料
    - **uploadSampleDataAndScripts.ps1:** 這個指令碼將上傳範例資料和指令碼至 Azure。
5. 請確定您已建立下列 Azure 資源：            
    - Azure 儲存體帳戶。
    - Azure SQL Database
    - Azure HDInsight 叢集 3.1 版或以上 (或使用 Data Factory 服務自動建立的隨選 HDInsight 叢集)   
7. Azure 資源建立之後，請確定您有連接到每個資源所需的資訊。
    - **Azure 儲存體帳戶** -帳戶名稱和帳戶金鑰。  
    - **Azure SQL Database** -伺服器、 資料庫、 使用者名稱和密碼。
    - **Azure HDInsight 叢集**。 -HDInsight 叢集、 使用者名稱、 密碼和帳戶名稱以及與此叢集相關聯的 Azure 儲存體帳戶金鑰的名稱。 如果您想要使用隨選 HDInsight 叢集，而不是您自己的 HDInsight 叢集，則可以略過此步驟。  
8. 啟動 **PowerShell** 並執行下列命令。 保持開啟 Azure PowerShell。 如果您關閉並重新開啟，則需要再次執行這些命令。
    - 執行 **Add-azureaccount** 並輸入使用者名稱和密碼，用來登入 Azure 入口網站。  
    - 執行 **Get-azuresubscription** 若要檢視此帳戶的所有訂閱。
    - 執行 **Select-azuresubscription** 來選取您想要使用的訂閱。 此訂用帳戶應該與您在 Azure 入口網站中使用的訂用帳戶相同。 

## 概觀
端對端工作流程的說明如下：

![教學課程端對端流程][image-data-factory-tutorial-end-to-end-flow]

1.  **PartitionGameLogsPipeline** 從 blob 儲存體 (RawGameEventsTable) 讀取原始遊戲事件，並根據年、 月和日 (PartitionedGameEventsTable) 建立分割區。
2.  **EnrichGameLogsPipeline** 聯結分割的遊戲事件 (PartitionedGameEvents 資料表，其為 PartitionGameLogsPipeline 的輸出) 與地區代碼 (RefGetoCodeDictionaryTable) 和 IP 位址對應到相對應的地理位置 (EnrichedGameEventsTable) 的來充實資料。
3.  **AnalyzeMarketingCampaignPipeline** 管線運用已充實的資料 (EnrichGameLogsPipeline 所產生)，並處理與廣告資料 (refmarketingcampaignntable) 處理，以建立行銷活動成效的最終輸出複製到 Azure SQL database (MarketingCampainEffectivensessSQLTable) 和分析 Azure blob 儲存體 (MarketingCampaignEffectivenessBlobTable)。
    
## 逐步解說：建立、部署和監視工作流程
1. [步驟 1: 上傳範例資料和指令碼](#MainStep1)。 在此步驟中，您將上傳所有範例資料 (包括所有記錄檔和參考資料) 和將由工作流程執行的 Hive/Pig 指令碼。 您執行的指令碼也會建立 Azure SQL 資料庫 (名為 MarketingCampaigns)、資料表、使用者定義型別和預存程序。
2. [步驟 2: 建立 Azure data factory](#MainStep2)。 在此步驟中，您將建立名為 LogProcessingFactory 的 Azure Data Factory。
3. [步驟 3: 建立連結的服務](#MainStep3)。 在此步驟中，您將建立下列連結的服務： 
    
    -   **StorageLinkedService**。 Azure 儲存體位置的連結，其中包含原始遊戲事件、分割的遊戲事件、充實的遊戲事件、行銷活動有效性資訊、參考資料的地區代碼，以及 LogProcessingFactory 行銷活動資料的參考資料   
    -   **AzureSqlLinkedService**。 連結 Azure SQL 資料庫，其中包含行銷活動有效性資訊。 
    -   **HDInsightStorageLinkedService**。 連結與 HDInsightLinkedService 所參照 HDInsight 叢集相關聯的 Azure Blob 儲存體。 
    -   **HDInsightLinkedService**。 將Azure HDInsight 叢集與 LogProcessingFactory 連結。 這個叢集用來對資料執行 pig/hive 處理。 
        
4. [步驟 4: 建立資料表](#MainStep4)。 在此步驟中，您將建立下列資料表：     
    
    - **RawGameEventsTable**。 此資料表指定 StorageLinkedService 所定義的 Azure Blob 儲存體中，原始遊戲事件資料的位置 (adfwalkthrough/logs/rawgameevents/)。 
    - **PartitionedGameEventsTable**。 此資料表指定 StorageLinkedService 所定義的 Azure Blob 儲存體中分割的遊戲事件資料的位置 (adfwalkthrough/logs/partitionedgameevents/)。 
    - **RefGeoCodeDictionaryTable**。 此資料表指定 StorageLinkedService 所定義的 Azure Blob 儲存體中，參考資料地區代碼的位置 (adfwalkthrough/refdata/refgeocodedictionary/)。
    - **RefMarketingCampaignTable**。 此資料表指定 StorageLinkedService 所定義的 Azure Blob 儲存體中，參考行銷活動資料的位置 (adfwalkthrough/refdata/refmarketingcampaign/)。
    - **EnrichedGameEventsTable**。 此資料表指定 StorageLinkedService 所定義的 Azure Blob 儲存體中，充實遊戲事件資料的位置 (adfwalkthrough/logs/enrichedgameevents/)。
    - **MarketingCampaignEffectivenessSQLTable**。此資料表指定的 SQL 資料表 (MarketingCampaignEffectiveness)，其中包含行銷活動成效資料 AzureSqlLinkedService 所定義的 Azure SQL 資料庫中。 
    - **MarketingCampaignEffectivenessBlobTable**。 此資料表指定 StorageLinkedService 所定義的 Azure Blob 儲存體中，行銷活動有效性資料的位置 (adfwalkthrough/marketingcampaigneffectiveness/)。 

    
5. [步驟 5: 建立和排程管線](#MainStep5)。 在此步驟中，您將建立下列管線：
    - **PartitionGameLogsPipeline**。 管線會從 Blob 儲存體 (RawGameEventsTable) 讀取原始遊戲事件，並建立以年、月和日為基礎的資料分割 (PartitionedGameEventsTable)。 


        ![PartitionGamesLogs pipeline][image-data-factory-tutorial-partition-game-logs-pipeline]


    - **EnrichGameLogsPipeline**. This pipeline joins partitioned game events (PartitionedGameEvents table, which is an output of the PartitionGameLogsPipeline) with geo-code (RefGetoCodeDictionaryTable) and enriches the data by mapping an IP address to the corresponding geo-location (EnrichedGameEventsTable) 

        ![EnrichedGameLogsPipeline][image-data-factory-tutorial-enrich-game-logs-pipeline]

    - **AnalyzeMarketingCampaignPipeline**. This pipeline leverages the enriched game event data (EnrichedGameEventTable produced by the EnrichGameLogsPipeline) and processes it with the advertising data (RefMarketingCampaignnTable) to create the final output of marketing campaign effectiveness, which is copied to the Azure SQL database (MarketingCampainEffectivensessSQLTable) and an Azure blob storage (MarketingCampaignEffectivenessBlobTable) for analytics


        ![MarketingCampaignPipeline][image-data-factory-tutorial-analyze-marketing-campaign-pipeline]


6. [步驟 6: 監視管線和資料配量](#MainStep6)。 在此步驟中，您將使用 Azure 傳統入口網站監視管線、資料表和資料配量。

## <a name="MainStep1"></a> 步驟 1: 上傳範例資料和指令碼
在此步驟中，您將上傳所有範例資料 (包括所有記錄檔和參考資料) 和將由工作流程叫用的 Hive/Pig 指令碼。 您也執行的指令碼會建立名為 Azure SQL 資料庫 **MarketingCampaigns**, 、 資料表、 使用者定義型別和預存程序。 

將行銷活動有效性結果從 Azure Blob 儲存體移至 Azure SQL 資料庫時，會使用資料表、使用者定義型別和預存程序。

1. 開啟 **uploadSampleDataAndScripts.ps1** 從 **C:\ADFWalkthrough** 資料夾 (或包含解壓縮的檔案的資料夾) 中您最愛的編輯器中，您的叢集資訊取代反白顯示，並儲存檔案。


        $storageAccount = <storage account name>
        $storageKey = <storage account key>
        $azuresqlServer = <sql azure server>.database.windows.net
        $azuresqlUser = <sql azure user>@<sql azure server>
        $azuresqlPassword = <sql azure password>

 
    This script requires you have sqlcmd utility installed on your machine. If you have SQL Server isntalled, you already have it. Otherwise, [download][sqlcmd-install] and install the utility. 
    
    Alternatively, you can use the files in the folder: C:\ADFWalkthrough\Scripts to upload pig/hive scripts and sample files to the adfwalkthrough container in the blob storage, and create MarketingCampaignEffectiveness table in the MarketingCamapaigns Azure SQL database.
   
2. 確認您的本機電腦可以存取 Azure SQL Database。 若要啟用存取，請使用 [Azure 傳統入口網站](http://manage.windowsazure.com) 或 **sp_set_firewall_rule** 對 master 資料庫來建立您的電腦的 IP 位址的防火牆規則。 可能需要五分鐘的時間，這項變更才會生效。 請參閱 [設定 Azure SQL 的防火牆規則][azure-sql-firewall]。
4. 在 Azure PowerShell 中，瀏覽至您解壓縮範例的位置 (例如: **C:\ADFWalkthrough**)
5. 執行 **uploadSampleDataAndScripts.ps1** 
6. 一旦指令碼執行成功，您會看到下列項目：

        $storageAccount = <storage account name>
        PS C:\ ADFWalkthrough> & '.\uploadSampleDataAndScripts.ps1'

        Name            PublicAccess        LastModified
        -----           --------        ------
        ADFWalkthrough      off         6/6/2014 6:53:34 PM +00:00
    
        Uploading sample data and script files to the storage container [adfwalkthrough]

        Container Uri: https://<yourblobstorage>.blob.core.windows.net/adfwalkthrough

        Name                        BlobType   Length   ContentType               LastModified                        
        ----                        --------   ------   -----------               ------------                        
        logs/rawgameevents/raw1.csv  BlockBlob  12308   application/octet-stream  6/6/2014 6:54:35 PM 
        logs/rawgameevents/raw2.csv  BlockBlob  16119   application/octet-stream  6/6/2014 6:54:35 PM 
        logs/rawgameevents/raw3.csv  BlockBlob  16062   application/octet-stream  6/6/2014 6:54:35 PM 
        logs/rawgameevents/raw4.csv  BlockBlob  16245   application/octet-stream  6/6/2014 6:54:35 PM 
        refdata/refgeocodedictiona.. BlockBlob  18647   application/octet-stream  6/6/2014 6:54:36 PM 
        refdata/refmarketingcampai.. BlockBlob  8808    application/octet-stream  6/6/2014 6:54:36 PM
        scripts/partitionlogs.hql    BlockBlob  2449    application/octet-stream  6/6/2014 6:54:36 PM 
        scripts/enrichlogs.pig       BlockBlob  1631    application/octet-stream  6/6/2014 6:54:36 PM
        scripts/transformdata.hql    BlockBlob  1753    application/octet-stream  6/6/2014 6:54:36 PM

        6/6/2014 11:54:36 AM Summary
        6/6/2014 11:54:36 AM 1. Uploaded Sample Data Files to blob container.
        6/6/2014 11:54:36 AM 2. Uploaded Sample Script Files to blob container.
        6/6/2014 11:54:36 AM 3. Created ‘MarketingCampaigns’ Azure SQL database and tables.
        6/6/2014 11:54:36 AM You are ready to deploy Linked Services, Tables and Pipelines. 

## <a name="MainStep2"></a> 步驟 2: 建立 Azure data factory
您可以在此步驟中，建立名為 Azure data factory **LogProcessingFactory**。

1.  登入後 [Azure 入口網站][azure-portal], ，按一下 [ **新增** 從左下角，按一下 [ **資料分析** 中 **建立** 刀鋒視窗中，然後按一下 [ **Data Factory** 上 **資料分析** 刀鋒視窗。 

    ![新增->DataFactory][image-data-factory-new-datafactory-menu] 

5. 在 **新增 data factory** 刀鋒視窗中，輸入 **LogProcessingFactory** 的 **名稱**。

    ![Data Factory 刀鋒視窗][image-data-factory-tutorial-new-datafactory-blade]

6. 如果您尚未建立名為 Azure 資源群組 **ADF** ，請執行下列動作:
    1. 按一下 [ **資源群組名稱**, ，然後按一下 **建立新的資源群組**。
    
        ![[資源群組] 刀鋒視窗][image-data-factory-tutorial-resourcegroup-blade]
    2. 在 **建立資源群組** 刀鋒視窗中，輸入 **ADF** 之名稱的資源群組，然後按一下 **確定**。
    
        ![建立資源群組][image-data-factory-tutorial-create-resourcegroup]
7. 選取 **ADF** 的 **資源群組名稱**。  
8.  在 **新增 data factory** 刀鋒視窗中，請注意， **新增至開始面板** 預設會選取。 這會將連結加入「開始面板」上的 Data Factory (登入 Azure 入口網站時會看見)。

    ![[建立 Data Factory] 刀鋒視窗][image-data-factory-tutorial-create-datafactory]

9.  在 **新增 data factory** 刀鋒視窗中，按一下 [ **建立** 建立 data factory。
10. 建立 data factory 之後，您應該會看到 **DATA FACTORY** 刀鋒視窗標題為 **LogProcessingFactory**。

    ![Data Factory 首頁][image-data-factory-tutorial-datafactory-homepage]

    
    If you do not see it, do one of the following:

    - Click **LogProcessingFactory** on the **Startboard** (home page)
    - Click **BROWSE** on the left, click **Everything**, click **Data factories**, and click the data factory.
 
    The name of the Azure data factory must be globally unique. If you receive the error: **Data factory name “LogProcessingFactory” is not available**, change the name (for example, yournameLogProcessingFactory). Use this name in place of LogProcessingFactory while performing steps in this tutorial.
 
## <a name="MainStep3"></a> 步驟 3：建立連結服務

> [AZURE.NOTE] 本文使用 Azure 傳統入口網站，特別是 Data Factory 編輯器，來建立連結的服務、 資料表和管線。 請參閱 [教學課程使用 Azure PowerShell][adftutorial-using-powershell] 如果您想要執行本教學課程中使用 Azure PowerShell。 

在此步驟中，您將建立下列連結的服務：

- StorageLinkedService
- AzureSqlLinkedService
- HDInsightStorageLinkedService
- HDInsightLinkedService。 

### 建立 StorageLinkedService 和 HDInsightStorageLinkedService

1.  在 **DATA FACTORY** 刀鋒視窗中，按一下 [ **作者和部署** ] 磚來啟動 **編輯器** 的 data factory。

    ![[製作和部署] 磚][image-author-deploy-tile] 

    請參閱 [Data Factory 編輯器][data-factory-editor] 主題，以取得 Data Factory 編輯器的詳細概觀。

2.  在 **編輯器**, ，按一下 [ **新增資料存放區** 工具列，然後選取按鈕 **Azure 儲存體** 從下拉式功能表。 在右窗格中，您應該會看到用來建立 Azure 儲存體連結服務的 JSON 範本。    
    
    ![編輯器 [新增資料存放區] 按鈕][image-editor-newdatastore-button]

3. 取代 **accountname** 和 **accountkey** 帳戶名稱與儲存體帳戶的帳戶金鑰值。

    ![編輯器 Blob 儲存體 JSON][image-editor-blob-storage-json]    
    
    請參閱 [JSON 指令碼參考](http://go.microsoft.com/fwlink/?LinkId=516971) 如需 JSON 屬性的詳細資訊。

4. 按一下 [ **部署** 部署 StorageLinkedService 工具列上。 確認您看到訊息 **服務已成功建立連結** 標題列上。

    ![編輯器 Blob 儲存體部署][image-editor-blob-storage-deploy]

5. 重複執行步驟，建立另一個 Azure 儲存體連結服務: **HDInsightStorageLinkedService** 與 HDInsight 叢集相關聯的儲存體。 在連結服務的 JSON 指令碼，變更值 **名稱** 屬性 **HDInsightStorageLinkedService**。 

### 建立 AzureSqlLinkedService
1. 在 **Data Factory 編輯器** , ，按一下 [ **新增資料存放區** 工具列，然後選取按鈕 **Azure SQL database** 從下拉式功能表。 在右窗格中，您應該會看到用來建立 Azure SQL 連結服務的 JSON 範本。
2. 取代 **servername**, ，**username@servername**, ，和 **密碼** 與 Azure SQL 伺服器、 使用者帳戶和密碼的名稱。  
3. 取代 **databasename** 與 **MarketingCampaigns**。 這是您在步驟 1 中執行的指令碼所建立的 Azure SQL Database。 您應該確認這些指令碼確實已建立此資料庫 (以避免有錯誤)。 
3. 按一下 [ **部署** 工具列建立並部署 AzureSqlLinkedService。

### 建立 HDInsightLinkedService
Azure Data Factory 服務支援建立隨選叢集，並使用它處理輸入來產生輸出資料。 您也可以使用自己的叢集執行相同作業。 當您使用隨選 HDInsight 叢集時，系統會為每個配量建立叢集。 然而，當您使用自己的 HDInsight 叢集時，叢集就可以立即處理配量。 因此，在使用隨選叢集時，可能無法像使用自己的叢集那麼快看到輸出資料。 基於範例的目的，讓我們使用隨選叢集。 

#### 若要使用隨選 HDInsight 叢集
1. 按一下 [ **新增計算** 從命令列，然後選取 **隨 HDInsight 叢集** 從功能表。
2. 在 JSON 指令碼中，執行下列動作： 
    1. 如 **clusterSize** 屬性，指定 HDInsight 叢集的大小。
    3. 如 **timeToLive** 屬性，指定時間長度會在刪除之前客戶可處於閒置狀態。 
    4. 如 **版本** 屬性，指定您想要使用的 HDInsight 版本。 如果您排除此屬性，則會使用最新版本。  
    5. 如 **linkedServiceName**, ，指定 **HDInsightStorageLinkedService** 您已建立在 「 開始使用 」 教學課程。 

            {
                "name": "HDInsightOnDemandLinkedService",
                "properties": {
                    "type": "HDInsightOnDemand",
                    "description": "",
                    "typeProperties": {
                        "clusterSize": "4",
                        "timeToLive": "00:30:00",
                        "version": "3.2",
                        "linkedServiceName": "StorageLinkedService"
                    }
                }
            }

        請注意， **類型** 連結服務設定為 **HDInsightOnDemand**。

2. 按一下 [ **部署** 命令列來部署連結的服務。
   
   
#### 若要使用您自己的 HDInsight 叢集： 

1. 按一下 [ **新增計算** 從命令列，然後選取 **hdinsight** 從功能表。
2. 在 JSON 指令碼中，執行下列動作： 
    1. 如 **clusterUri** 屬性中，輸入您的 HDInsight 的 URL。 例如：https://<clustername>.azurehdinsight.net/     
    2. 如 **UserName** 屬性中，輸入具有 HDInsight 叢集存取權的使用者名稱。
    3. 如 **密碼** 屬性中，輸入使用者的密碼。 
    4. 如 **LinkedServiceName** 屬性中，輸入 **StorageLinkedService**。 這是您在「開始使用」教學課程中建立的連結服務。 

    請 **類型** 連結服務設定為 **HDInsightBYOCLinkedService** (BYOC 代表將您自己的叢集)。 

2. 按一下 [ **部署** 命令列來部署連結的服務。


## <a name="MainStep4"></a> 步驟 4: 建立資料表
 
在此步驟中，您將建立下列 Data Factory 資料表： 

- RawGameEventsTable
- PartitionedGameEventsTable
- RefGeoCodeDictionaryTable
- RefMarketingCampaignTable
- EnrichedGameEventsTable
- MarketingCampaignEffectivenessSQLTable
- MarketingCampaignEffectivenessBlobTable

    ![教學課程端對端流程][image-data-factory-tutorial-end-to-end-flow]
 
上圖顯示的管線在中間資料列，而資料表在頂端和底部資料列。 

### 建立資料表
    
1. 在 **編輯器** Data Factory 中，按一下 [ **新的資料集** 按鈕的工具列，並按一下 **Azure Blob 儲存體** 從下拉式功能表。 
2. JSON，取代右窗格中的 JSON 指令碼 **C:\adfwalkthrough\tables** 檔案從 **C:\ADFWalkthrough\Tables** 資料夾。
3. 按一下 [ **部署** 建立並部署資料表] 工具列上。 確認您看到 **已成功建立資料表** 編輯器的標題列上的訊息。
4. 使用下列檔案的內容，重複步驟 1 到 3： 
    1. PartitionedGameEventsTable.json
    2. RefGeoCodeDictionaryTable.json
    3. RefMarketingCampaignTable.json
    4. EnrichedGameEventsTable.json
    5. MarketingCampaignEffectivenessBlobTable.json 
5. 使用下列檔案的內容，重複步驟 1 到 3。 但選取 **Azure Sql** 按一下之後 **新的資料集**。
    1. MarketingCampaignEffectivenessSQLTable.json
    

## <a name="MainStep5"></a> 步驟 5: 建立和排程管線
在此步驟中，您將建立下列管線： 

- PartitionGameLogsPipeline
- EnrichGameLogsPipeline
- AnalyzeMarketingCampaignPipeline

### 若要建立管線

1. 在 **Data Factory 編輯器**, ，按一下 [ **新增管線** 工具列上的按鈕。 如果沒看到此按鈕，請按一下工具列 **...(省略符號)**。 或者，您可以以滑鼠右鍵按一下 **管線** 樹狀檢視中按一下 **新增管線**。
2. JSON，取代右窗格中的 JSON 指令碼 **PartitionGameLogsPipeline.json** 檔案從 **C:\ADFWalkthrough\Pipelines** 資料夾。
3. 新增 **逗號 ('，')** 結尾 **右方括弧 ('] ')** 在 JSON 中，然後在右方括弧之後加入下列三行。 

        "start": "2014-05-01T00:00:00Z",
        "end": "2014-05-05T00:00:00Z",
        "isPaused": false

    請注意，開始和結束時間設為 05/01/2014 和 05/05/2014，因為此逐步解說中的範例資料是從 05/01/2014 到 05/05/2014。 
    
    如果您使用隨選 HDInsight 連結服務，設定 **linkedServiceName** 屬性 **HDInsightOnDemandLinkedService**。
3. 按一下 [ **部署** 建立並部署管線] 工具列上。 確認您看到 **已成功建立管線** 編輯器的標題列上的訊息。
4. 使用下列檔案的內容，重複步驟 1 到 3： 
    1. EnrichGameLogsPipeline.json
    2. AnalyzeMarketingCampaignPipeline.json
4. 關閉 Data Factory 刀鋒視窗，按下 **X** (右上角)，以查看首頁 (* * DATA FACTORY * * 刀鋒視窗) Data factory。 

### 圖表檢視

1. 在 **DATA FACTORY** 分頁 **LogProcessingFactory**, ，按一下 [ **圖表**。 

    ![圖表連結][image-data-factory-tutorial-diagram-link]

2. 您可以重新排列所看見的圖表，這裡是重新排列的圖表，在頂端顯示直接輸入，在底端則是輸出。 您可以看到的輸出 **PartitionGameLogsPipeline** 中傳遞至 enrichgamelogspipeline 的輸出做為輸入 **EnrichGameLogsPipeline** 傳遞至 **AnalyzeMarketingCampaignPipeline**。 按兩下某個標題，以查看分頁所代表之成品的詳細資訊。

    ![圖表檢視][image-data-factory-tutorial-diagram-view]

3. 以滑鼠右鍵按一下 **AnalyzeMarketingCampaignPipeline**, ，然後按一下 **開啟管線**。 您應該會看到管線中的所有活動，以及活動的輸入和輸出資料集。 
 
    ![開啟管線](./media/data-factory-tutorial/AnalyzeMarketingCampaignPipeline-OpenPipeline.png)

    按一下 [ **Data factory** 回到圖表檢視所有管線左上角階層連結。  


**恭喜您!**您已成功建立 Azure Data Factory、連結的服務、管線、資料表，並開始工作流程。 


## <a name="MainStep6"></a> 步驟 6: 監視管線和資料配量 

1.  如果您不需要 **DATA FACTORY** 分頁 **LogProcessingFactory** 開啟，您可以執行下列其中之一:
    1.  按一下 [ **LogProcessingFactory** 上 **儀表板**。 建立資料處理站時 **新增至開始面板** 自動核取選項。

        ![監視「開始面板」][image-data-factory-monitoring-startboard]

    2. 按一下 [ **瀏覽**, 中 **瀏覽** 分頁中，選取 **Data factory** ，然後選取 **LogProcessingFactory** 中 **Data factory** 刀鋒視窗。

        ![監視瀏覽 Data Factory][image-data-factory-monitoring-browse-datafactories]
2. 您可以以數種方式監視您的 Data Factory。 您可以從管線或資料集開始。 讓我們從管線開始，並進一步向下鑽研。 
3.  按一下 [ **管線** 上 **DATA FACTORY** 刀鋒視窗。 
4.  按一下 [ **PartitionGameLogsPipeline** 管線分頁中。 
5.  在 **管線** 分頁 **PartitionGameLogsPipeline**, ，您會看見管線使用 **RawGameEventsTable** 資料集。  按一下 [ **RawGameEventsTable**。

    ![管線已取用且已生產][image-data-factory-monitoring-pipeline-consumed-produced]

6. 中的資料表分頁 **RawGameEventsTable**, ，您會看見所有配量。 在下列螢幕擷取畫面，所有的配量都處於 **準備** 狀態並沒有任何問題配量。 這表示資料已就緒可供處理。 

    ![RawGameEventsTable [資料表] 刀鋒視窗][image-data-factory-monitoring-raw-game-events-table]

    同時 **最近更新的配量** 和 **最近失敗的配量** 清單都依照 **上次更新時間**。 在下列情況中，配量的更新時間會變更。    

    -  您的配量狀態以手動方式更新，例如，使用 **組 AzureRmDataFactorySliceStatus** (或) 依序按一下 **執行** 上 **配量** 配量的分頁。
    -  配量因為執行而變更狀態 (例如，開始執行、執行結束但失敗、執行結束且成功等)。
 
    按一下清單的標題或 **...(省略符號)** 若要查看更長的配量清單。 按一下 [ **篩選** 篩選配量] 工具列上。  
    
    若要檢視 [資料配量時間排序的配量開始/結束改為 **資料配量 (依配量時間)** 並排顯示。  

    ![資料配量 (依配量時間)][DataSlicesBySliceTime]
 
7. 現在，在 **管線** 分頁 **PartiionGameLogsPipeline**, ，按一下 [ **產生**。 
8. 您應該會看到這個管線產生的資料集的清單： 
9. 按一下 [ **PartitionedGameEvents** 資料表中 **產生資料集** 刀鋒視窗。 
10. 確認 **狀態** 的所有配量設 **準備**。 
11. 按一下其中一個配量， **準備** 查看 **資料配量** 該配量分頁。

    ![RawGameEventsTable [資料配量] 刀鋒視窗][image-data-factory-monitoring-raw-game-events-table-dataslice-blade]

    如果發生錯誤，您在這裡會看到 **[Failed] **狀態。  您也可能會看到狀態是兩個配量 **準備**, ，或兩者皆擁有狀態 **PendingValidation**, ，根據處理配量的速度。

    如果配量不是處於 **準備** 狀態，您可以看到未就緒且阻礙目前配量中執行的上游配量 **未就緒的上游配量** 清單。
 
    請參閱 [Azure Data Factory 開發人員參考][developer-reference] ，以了解所有可能的配量狀態。

12. 在 **資料配量** 刀鋒視窗中，按一下 [從執行 **活動執行** 清單。 您應該會看到該配量的「活動執行」分頁。 您應該會看到下列 **活動執行詳細資料** 刀鋒視窗。

    ![[活動執行詳細資料] 刀鋒視窗][image-data-factory-monitoring-activity-run-details]

13. 按一下 [ **下載** 下載檔案。 對 HDInsight 處理的錯誤進行疑難排解時，這個畫面會特別有用。 
     
    
當所有管線都完成執行時，您可以考慮 **Marketingcampaigns** 中 **MarketingCampaigns** Azure SQL 資料庫，以檢視結果。 

**恭喜您!**您現在可以監視和疑難排解工作流程。 您已經學會如何使用 Azure Data Factory 來處理資料，並取得分析。

## 延伸教學課程來使用內部部署資料
在本文逐步解說的記錄檔處理案例的最後一個步驟中，行銷活動成效輸出已複製到 Azure SQL Database。 您也可以在組織內，將此資料移動到內部部署 SQL Server 以進行分析。
 
若要從 Azure Blob 將行銷活動成效資料複製到內部部署 SQL Server，您需要建立本文逐步解說中所介紹的額外內部部署連結服務、資料表和管線。

練習 [逐步解說: 使用內部部署資料來源][tutorial-onpremises] 以了解如何建立管線，以將行銷活動成效資料複製到內部部署 SQL Server 資料庫。


[monitor-manage-using-powershell]: data-factory-monitor-manage-using-powershell.md
[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456
[data-factory-editor]: data-factory-editor.md

[adfsamples]: data-factory-samples.md
[adfgetstarted]: data-factory-get-started.md
[adftutorial-using-powershell]: data-factory-tutorial-using-powershell.md
[adfintroduction]: data-factory-introduction.md
[usepigandhive]: data-factory-data-transformation-activities.md
[tutorial-onpremises]: data-factory-tutorial-extend-onpremises.md
[download-azure-powershell]: ../powershell-install-configure.md

[azure-portal]: http://portal.azure.com
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[sqlcmd-install]: http://www.microsoft.com/download/details.aspx?id=35580
[azure-sql-firewall]: https://azure.microsoft.com/documentation/articles/sql-database-configure-firewall-settings/


[adfwalkthrough-download]: http://go.microsoft.com/fwlink/?LinkId=517495
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908

[DataSlicesBySliceTime]: ./media/data-factory-tutorial/DataSlicesBySliceTime.png
[image-author-deploy-tile]: ./media/data-factory-tutorial/author-deploy-tile.png
[image-editor-newdatastore-button]: ./media/data-factory-tutorial/editor-newdatastore-button.png
[image-editor-blob-storage-json]: ./media/data-factory-tutorial/editor-blob-storage-json.png
[image-editor-blob-storage-deploy]: ./media/data-factory-tutorial/editor-blob-storage-deploy.png

[image-data-factory-tutorial-end-to-end-flow]: ./media/data-factory-tutorial/EndToEndWorkflow.png

[image-data-factory-tutorial-partition-game-logs-pipeline]: ./media/data-factory-tutorial/PartitionGameLogsPipeline.png

[image-data-factory-tutorial-enrich-game-logs-pipeline]: ./media/data-factory-tutorial/EnrichGameLogsPipeline.png

[image-data-factory-tutorial-analyze-marketing-campaign-pipeline]: ./media/data-factory-tutorial/AnalyzeMarketingCampaignPipeline.png


[image-data-factory-tutorial-egress-to-onprem-pipeline]: ./media/data-factory-tutorial/EgreeDataToOnPremPipeline.png

[image-data-factory-tutorial-set-firewall-rules-azure-db]: ./media/data-factory-tutorial/SetFirewallRuleForAzureDatabase.png

[image-data-factory-tutorial-portal-new-everything]: ./media/data-factory-tutorial/PortalNewEverything.png

[image-data-factory-tutorial-datastorage-cache-backup]: ./media/data-factory-tutorial/DataStorageCacheBackup.png

[image-data-factory-tutorial-dataservices-blade]: ./media/data-factory-tutorial/DataServicesBlade.png

[image-data-factory-tutorial-new-datafactory-blade]: ./media/data-factory-tutorial/NewDataFactoryBlade.png

[image-data-factory-tutorial-resourcegroup-blade]: ./media/data-factory-tutorial/ResourceGroupBlade.png

[image-data-factory-tutorial-create-resourcegroup]: ./media/data-factory-tutorial/CreateResourceGroup.png

[image-data-factory-tutorial-datafactory-homepage]: ./media/data-factory-tutorial/DataFactoryHomePage.png

[image-data-factory-tutorial-create-datafactory]: ./media/data-factory-tutorial/CreateDataFactory.png

[image-data-factory-tutorial-linkedservice-tile]: ./media/data-factory-tutorial/LinkedServiceTile.png

[image-data-factory-tutorial-linkedservices-add-datstore]: ./media/data-factory-tutorial/LinkedServicesAddDataStore.png

[image-data-factory-tutorial-datastoretype-azurestorage]: ./media/data-factory-tutorial/DataStoreTypeAzureStorageAccount.png

[image-data-factory-tutorial-azurestorage-settings]: ./media/data-factory-tutorial/AzureStorageSettings.png

[image-data-factory-tutorial-storage-key]: ./media/data-factory-tutorial/StorageKeyFromAzurePortal.png

[image-data-factory-tutorial-linkedservices-blade-storage]: ./media/data-factory-tutorial/LinkedServicesBladeWithAzureStorage.png

[image-data-factory-tutorial-azuresql-settings]: ./media/data-factory-tutorial/AzureSQLDatabaseSettings.png

[image-data-factory-tutorial-azuresql-database-connection-string]: ./media/data-factory-tutorial/DatabaseConnectionString.png

[image-data-factory-tutorial-linkedservices-all]: ./media/data-factory-tutorial/LinkedServicesAll.png

[image-data-factory-tutorial-datasets-all]: ./media/data-factory-tutorial/DataSetsAllTables.png

[image-data-factory-tutorial-pipelines-all]: ./media/data-factory-tutorial/AllPipelines.png

[image-data-factory-tutorial-diagram-link]: ./media/data-factory-tutorial/DataFactoryDiagramLink.png

[image-data-factory-tutorial-diagram-view]: ./media/data-factory-tutorial/DiagramView.png

[image-data-factory-monitoring-startboard]: ./media/data-factory-tutorial/MonitoringStartBoard.png

[image-data-factory-monitoring-hub-everything]: ./media/data-factory-tutorial/MonitoringHubEverything.png

[image-data-factory-monitoring-browse-datafactories]: ./media/data-factory-tutorial/MonitoringBrowseDataFactories.png

[image-data-factory-monitoring-pipeline-consumed-produced]: ./media/data-factory-tutorial/MonitoringPipelineConsumedProduced.png

[image-data-factory-monitoring-raw-game-events-table]: ./media/data-factory-tutorial/MonitoringRawGameEventsTable.png

[image-data-factory-monitoring-raw-game-events-table-dataslice-blade]: ./media/data-factory-tutorial/MonitoringPartitionGameEventsTableDataSliceBlade.png

[image-data-factory-monitoring-activity-run-details]: ./media/data-factory-tutorial/MonitoringActivityRunDetails.png

[image-data-factory-datamanagementgateway-configuration-manager]: ./media/data-factory-tutorial/DataManagementGatewayConfigurationManager.png

[image-data-factory-new-datafactory-menu]: ./media/data-factory-tutorial/NewDataFactoryMenu.png

[image-data-factory-new-datafactory-create-button]: ./media/data-factory-tutorial/DataFactoryCreateButton.png 
