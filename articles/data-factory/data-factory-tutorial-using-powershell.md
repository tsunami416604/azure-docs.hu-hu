<properties 
    pageTitle="使用 Azure Data Factory 移動和處理記錄檔 (Azure PowerShell)" 
    description="此進階教學課程說明接近現實情況的案例，並使用 Azure Data Factory 服務和 Azure PowerShell 來實作案例。" 
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
    ms.date="10/29/2015" 
    ms.author="spelluru"/>

# 教學課程：使用 Data Factory 移動及處理記錄檔 [PowerShell]
本文章提供的端對端逐步解說，是有關使用 Azure Data Factory，將記錄檔的資料轉換成見解的記錄檔處理程序標準案例。

## 案例
Contoso 是為多個平台建立遊戲的遊戲公司，包含遊戲主機、手持裝置與個人電腦 (PC)。 每個遊戲都會產生大量記錄檔。 Contoso 的目標是要收集和分析這些遊戲所產生的記錄檔以取得使用量資訊、識別向上銷售與交叉銷售機會、開發新的強大功能等，以改善業務並為客戶提供更好的經驗。
 
在本逐步解說中，我們會收集範例記錄檔、處理並以參考資料加以充實，然後轉換資料以評估 Contoso 最近推出的行銷活動有效性。

## 準備開始教學課程
1.  讀取 [簡介 Azure Data Factory][adfintroduction] 來取得 Azure Data Factory 的概觀並了解最高階的概念。
2.  您必須擁有 Azure 訂閱，才能執行本教學課程。 如需取得訂用帳戶的詳細資訊，請參閱 [購買選項] [azure-purchase-options], ，[成員優惠][azure-member-offers], ，或 [免費試用版][azure-free-trial]。
3.  您必須下載並安裝 [PowerShell][download-azure-powershell] 電腦上。

    這篇文章並未涵蓋所有的 Data Factory Cmdlet。 請參閱 [Data Factory Cmdlet 參考](https://msdn.microsoft.com/library/dn820234.aspx) 如需 Data Factory cmdlet 的完整文件。
    
    如果您使用 Azure PowerShell 的 **1.0 版 <**, ，您必須使用指令程式所記載 [這裡][old-cmdlet-reference]。 您也必須在使用 Data Factory Cmdlet 之前，先執行下列命令： 

    1. 執行 **Add-azureaccount** ，然後輸入使用者名稱和密碼用於登入 Azure 入口網站。
    2. 執行 **Get-azuresubscription** 若要檢視此帳戶的所有訂閱。
    3. 執行 **Select-azuresubscription** 來選取您想要使用的訂閱。 此訂用帳戶應該與您在 Azure 入口網站中使用的相同。
    
    將 Azure PowerShell 維持在開啟狀態，直到本教學課程結束為止。 如果您關閉並重新開啟，則需要再次執行這些命令。

2. Azure Data Factory cmdlet 可在此模式時，切換至 AzureResourceManager 模式 ︰ **Switch-azuremode AzureResourceManager**。
 
2.  **（建議選項）** 檢閱並練習中的教學課程 [開始使用 Azure Data Factory][adfgetstarted] 課程來熟悉入口網站和 cmdlet 的簡易教學課程的文件。
3.  **（建議選項）** 檢閱並練習中的逐步解說 [使用 Pig 和 Hive 搭配 Azure Data Factory][usepigandhive] 文章的逐步解說建立管線，將資料從內部部署資料來源移至 Azure blob 存放區。
4.  下載 [ADFWalkthrough][adfwalkthrough-download] 檔案到 **C:\ADFWalkthrough** 資料夾 **並保留資料夾結構**:
    - **管線 ︰** 它包含 JSON 檔案，其中包含管線的定義。
    - **資料表 ︰** 它包含 JSON 檔案，其中包含資料表的定義。
    - **LinkedServices:** 包含 JSON 檔案包含定義您的儲存體和計算 (HDInsight) 叢集 
    - **指令碼 ︰** 它包含 Hive 和 Pig 指令碼，用於處理資料並從管線叫用
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
    - 執行 **登入 AzureRmAccount** 並輸入使用者名稱和密碼，用來登入 Azure 入口網站。  
    - 執行 **Get-azuresubscription** 若要檢視此帳戶的所有訂閱。
    - 執行 **Select-azuresubscription** 來選取您想要使用的訂閱。 此訂用帳戶應該與您在 Azure 入口網站中使用的訂用帳戶相同。
    

## 概觀
端對端工作流程的說明如下：
    ![教學課程端對端流程][image-data-factory-tutorial-end-to-end-flow]

1.  **PartitionGameLogsPipeline** 從 blob 儲存體 (RawGameEventsTable) 讀取原始遊戲事件，並根據年、 月和日 (PartitionedGameEventsTable) 建立分割區。
2.  **EnrichGameLogsPipeline** 聯結分割的遊戲事件 （PartitionedGameEvents 資料表，其為 PartitionGameLogsPipeline 的輸出） 與地區代碼 (RefGetoCodeDictionaryTable) 和 IP 位址對應到相對應的地理位置 (EnrichedGameEventsTable) 的來充實資料。
3.  **AnalyzeMarketingCampaignPipeline** 管線運用已充實的資料 （EnrichGameLogsPipeline 所產生），並處理與廣告資料 （refmarketingcampaignntable） 處理，以建立行銷活動成效的最終輸出複製到 Azure SQL database (MarketingCampainEffectivensessSQLTable) 和分析 Azure blob 儲存體 (MarketingCampaignEffectivenessBlobTable)。
    
## 逐步解說：建立、部署和監視工作流程
1. [步驟 1 ︰ 上傳範例資料和指令碼](#MainStep1)。 在此步驟中，您將上傳所有範例資料 (包括所有記錄檔和參考資料) 和將由工作流程執行的 Hive/Pig 指令碼。 您執行的指令碼也會建立 Azure SQL 資料庫 (名為 MarketingCampaigns)、資料表、使用者定義型別和預存程序。
2. [步驟 2 ︰ 建立 Azure data factory](#MainStep2)。 在此步驟中，您將建立名為 LogProcessingFactory 的 Azure Data Factory。
3. [步驟 3 ︰ 建立連結的服務](#MainStep3)。 在此步驟中，您將建立下列連結的服務： 
    
    -   **StorageLinkedService**。 Azure 儲存體位置的連結，其中包含原始遊戲事件、分割的遊戲事件、充實的遊戲事件、行銷活動有效性資訊、參考資料的地區代碼，以及 LogProcessingFactory 行銷活動資料的參考資料   
    -   **AzureSqlLinkedService**。 連結 Azure SQL 資料庫，其中包含行銷活動有效性資訊。 
    -   **HDInsightStorageLinkedService**。 連結與 HDInsightLinkedService 所參照 HDInsight 叢集相關聯的 Azure Blob 儲存體。 
    -   **HDInsightLinkedService**。 將Azure HDInsight 叢集與 LogProcessingFactory 連結。 這個叢集用來對資料執行 pig/hive 處理。 
        
4. [步驟 4 ︰ 建立資料表](#MainStep4)。 在此步驟中，您將建立下列資料表：     
    
    - **RawGameEventsTable**。 此資料表指定 StorageLinkedService 所定義的 Azure Blob 儲存體中，原始遊戲事件資料的位置 (adfwalkthrough/logs/rawgameevents/)。 
    - **PartitionedGameEventsTable**。 此資料表指定 StorageLinkedService 所定義的 Azure Blob 儲存體中分割的遊戲事件資料的位置 (adfwalkthrough/logs/partitionedgameevents/)。 
    - **RefGeoCodeDictionaryTable**。 此資料表指定 StorageLinkedService 所定義的 Azure Blob 儲存體中，參考資料地區代碼的位置 (adfwalkthrough/refdata/refgeocodedictionary/)。
    - **RefMarketingCampaignTable**。 此資料表指定 StorageLinkedService 所定義的 Azure Blob 儲存體中，參考行銷活動資料的位置 (adfwalkthrough/refdata/refmarketingcampaign/)。
    - **EnrichedGameEventsTable**。 此資料表指定 StorageLinkedService 所定義的 Azure Blob 儲存體中，充實遊戲事件資料的位置 (adfwalkthrough/logs/enrichedgameevents/)。
    - **MarketingCampaignEffectivenessSQLTable**。此資料表指定的 SQL 資料表 (MarketingCampaignEffectiveness)，其中包含行銷活動成效資料 AzureSqlLinkedService 所定義的 Azure SQL 資料庫中。 
    - **MarketingCampaignEffectivenessBlobTable**。 此資料表指定 StorageLinkedService 所定義的 Azure Blob 儲存體中，行銷活動有效性資料的位置 (adfwalkthrough/marketingcampaigneffectiveness/)。 

    
5. [步驟 5 ︰ 建立和排程管線](#MainStep5)。 在此步驟中，您將建立下列管線：
    - **PartitionGameLogsPipeline**。 管線會從 Blob 儲存體 (RawGameEventsTable) 讀取原始遊戲事件，並建立以年、月和日為基礎的資料分割 (PartitionedGameEventsTable)。 


        ![PartitionGamesLogs pipeline][image-data-factory-tutorial-partition-game-logs-pipeline]


    - **EnrichGameLogsPipeline**. This pipeline joins partitioned game events (PartitionedGameEvents table, which is an output of the PartitionGameLogsPipeline) with geo-code (RefGetoCodeDictionaryTable) and enriches the data by mapping an IP address to the corresponding geo-location (EnrichedGameEventsTable) 

        ![EnrichedGameLogsPipeline][image-data-factory-tutorial-enrich-game-logs-pipeline]

    - **AnalyzeMarketingCampaignPipeline**. This pipeline leverages the enriched game event data (EnrichedGameEventTable produced by the EnrichGameLogsPipeline) and processes it with the advertising data (RefMarketingCampaignnTable) to create the final output of marketing campaign effectiveness, which is copied to the Azure SQL database (MarketingCampainEffectivensessSQLTable) and an Azure blob storage (MarketingCampaignEffectivenessBlobTable) for analytics


        ![MarketingCampaignPipeline][image-data-factory-tutorial-analyze-marketing-campaign-pipeline]


6. [步驟 6 ︰ 監視管線和資料配量](#MainStep6)。 在此步驟中，您將使用 Azure 傳統入口網站監視管線、資料表和資料配量。

## <a name="MainStep1"></a> 步驟 1：上傳範例資料和指令碼
在此步驟中，您將上傳所有範例資料 (包括所有記錄檔和參考資料) 和將由工作流程叫用的 Hive/Pig 指令碼。 您也執行的指令碼會建立名為 Azure SQL 資料庫 **MarketingCampaigns**, 、 資料表、 使用者定義型別和預存程序。 

將行銷活動有效性結果從 Azure Blob 儲存體移至 Azure SQL 資料庫時，會使用資料表、使用者定義型別和預存程序。

1. 開啟 **uploadSampleDataAndScripts.ps1** 從 **C:\ADFWalkthrough** 資料夾 （或包含解壓縮的檔案的資料夾） 中您最愛的編輯器中，您的叢集資訊取代反白顯示，並儲存檔案。


        $storageAccount = <storage account name>
        $storageKey = <storage account key>
        $azuresqlServer = <sql azure server>.database.windows.net
        $azuresqlUser = <sql azure user>@<sql azure server>
        $azuresqlPassword = <sql azure password>

 
    This script requires you have sqlcmd utility installed on your machine. If you have SQL Server isntalled, you already have it. Otherwise, [download][sqlcmd-install] and install the utility. 
    
    Alternatively, you can use the files in the folder: C:\ADFWalkthrough\Scripts to upload pig/hive scripts and sample files to the adfwalkthrough container in the blob storage, and create MarketingCampaignEffectiveness table in the MarketingCamapaigns Azure SQL database.
   
2. 確認您的本機電腦可以存取 Azure SQL Database。 若要啟用存取，請使用 [Azure 傳統入口網站](http://manage.windowsazure.com) 或 **sp_set_firewall_rule** 對 master 資料庫來建立您的電腦的 IP 位址的防火牆規則。 可能需要五分鐘的時間，這項變更才會生效。 請參閱 [設定 Azure SQL 的防火牆規則][azure-sql-firewall]。
4. 在 Azure PowerShell 中，瀏覽至您解壓縮範例的位置 (例如 ︰ **C:\ADFWalkthrough**)
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


## <a name="MainStep2"></a> 步驟 2：建立 Azure Data Factory
您可以在此步驟中，建立名為 Azure data factory **LogProcessingFactory**。

1. 切換至 **PowerShell** 如果已開啟 （或） 啟動 **PowerShell**。 如果您已經關閉並重新開啟 Azure PowerShell，您需要執行下列命令： 
    - 執行 **登入 AzureRmAccount** 並輸入使用者名稱和密碼，用來登入 Azure 入口網站。  
    - 執行 **Get-azuresubscription** 若要檢視此帳戶的所有訂閱。
    - 執行 **Select-azuresubscription** 來選取您想要使用的訂閱。 此訂用帳戶應該與您在 Azure 入口網站中使用的訂用帳戶相同。 

2. 建立名為 Azure 資源群組 **ADFTutorialResourceGroup** （如果您尚未建立） 執行下列命令。

        New-AzureRmResourceGroup -Name ADFTutorialResourceGroup  -Location "West US"

    本教學課程的某些步驟假設您使用名為 ADFTutorialResourceGroup 的資源群組。 如果使用不同的資源群組，您必須以該群組取代本教學課程中的 ADFTutorialResourceGroup。
4. 執行 **新增 AzureRmDataFactory** 指令程式可建立名為 DataFactoryMyFirstPipelinePSH data factory。  

        New-AzureRmDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name LogProcessingFactory –Location "West US"

    > [AZURE.IMPORTANT] Azure Data Factory 的名稱必須是全域唯一的。 如果您收到錯誤 **Data factory 名稱"LogProcessingFactory"沒有**, ，變更名稱 (例如，yournameLogProcessingFactory)。 執行本教學課程中的步驟時，請使用此名稱來取代 LogProcessingFactory。 請參閱 [Data Factory-命名規則](data-factory-naming-rules.md) Data Factory 成品的命名規則的主題。
    > 
    > Data Factory 的名稱未來可能會註冊為 DNS 名稱，因此會變成公開可見的名稱。

 
## <a name="MainStep3"></a> 步驟 3：建立連結服務

> [AZURE.NOTE] 此文章會使用 Azure PowerShell 建立連結的服務、 資料表和管線。 請參閱 [教學課程使用 Data Factory 編輯器][adftutorial-using-editor] 如果您想要執行本教學課程中使用 Azure 入口網站中，特別是 Data Factory 編輯器。 

在此步驟中，您將建立下列連結服務：StorageLinkedService、AzureSqlLinkedService、HDInsightStorageLinkedService 和 HDInsightLinkedService。

16. 在 Azure PowerShell 中，瀏覽至 **LinkedServices** 子資料夾中的 **C:\ADFWalkthrough** （或） 從您解壓縮檔案的位置的資料夾。
17. 使用下列命令，將 $df 變數設定為 Data Factory 的名稱。

        $df = “LogProcessingFactory”
17. 開啟 **C:\adfgetstartedpsh** 在您喜好的編輯器中，輸入 **帳戶名稱** 和 **帳戶金鑰** 值，然後儲存檔案。
17. 使用 cmdlet **新增 AzureRmDataFactoryLinkedService** 來建立連結服務，如下所示。 

        New-AzureRmDataFactoryLinkedService -ResourceGroupName ADF -DataFactoryName $df -File .\StorageLinkedService.json
    
18. 開啟 **C:\adfgetstartedpsh** 在您喜好的編輯器中，輸入 **帳戶名稱** 和 **帳戶金鑰** 值，然後儲存檔案。
19. 建立 **HDInsightStorageLinkedService**。

        New-AzureRmDataFactoryLinkedService -ResourceGroupName ADF -DataFactoryName $df -File .\HDInsightStorageLinkedService.json
 
19. 開啟 **AzureSqlLinkedService.json** 在您喜好的編輯器中，輸入 **azure sql 伺服器** 名稱 **使用者名稱** 和 **密碼** 值，然後儲存檔案。
19. 使用 cmdlet **新增 AzureRmDataFactoryLinkedService** 來建立連結服務，如下所示。 

        New-AzureRmDataFactoryLinkedService -ResourceGroupName ADF -DataFactoryName $df -File .\AzureSqlLinkedService.json
19. 開啟 **HDInsightLinkedService.json** 中您最愛的編輯器並注意類型設為 **HDInsightOnDemandLinkedService**。

    Azure Data Factory 服務支援建立隨選叢集，並使用它處理輸入來產生輸出資料。 您也可以使用自己的叢集執行相同作業。 當您使用隨選 HDInsight 叢集時，系統會為每個配量建立叢集。 然而，當您使用自己的 HDInsight 叢集時，叢集就可以立即處理配量。 因此，在使用隨選叢集時，可能無法像使用自己的叢集那麼快看到輸出資料。 基於範例的目的，讓我們使用隨選叢集。 
    
    HDInsightLinkedService 將隨選 HDInsight 叢集連結至 Data Factory。 若要使用您自己的 HDInsight 叢集，請更新 HDInsightLinkedService.json 檔案的 Properties 區段 (使用適當的值取代 clustername、username 和 password)，如下所示： 
    
        "Properties": 
        {
            "Type": "HDInsightBYOCLinkedService",
            "ClusterUri": "https://<clustername>.azurehdinsight.net/",
            "UserName": "<username>",
            "Password": "<password>",
            "LinkedServiceName": "HDInsightStorageLinkedService"
        }
        


19. 使用 cmdlet **新增 AzureRmDataFactoryLinkedService** 來建立連結服務，如下所示。 開始使用儲存體帳戶：

        New-AzureRmDataFactoryLinkedService -ResourceGroupName ADF -DataFactoryName $df -File .\HDInsightLinkedService.json
 
    如果您對 ResourceGroupName、DataFactoryName 或 LinkedService 名稱使用不同的名稱，請在上述 Cmdlet 加以參考。 另外，只在找不到檔案時，才提供連結的服務 JSON 檔案的完整檔案路徑。

 

## <a name="MainStep4"></a> 步驟 4：建立資料表 
在此步驟中，您將建立下列資料表： 

- RawGameEventsTable
- PartitionedGameEventsTable
- RefGeoCodeDictionaryTable
- RefMarketingCampaignTable
- EnrichedGameEventsTable
- MarketingCampaignEffectivenessSQLTable
- MarketingCampaignEffectivenessBlobTable

    ![教學課程端對端流程][image-data-factory-tutorial-end-to-end-flow]
 
上圖顯示的管線在中間資料列，而資料表在頂端和底部資料列。 

Azure 傳統入口網站尚不支援建立資料集/資料表，因此在此版本中，您必須使用 Azure PowerShell 來建立資料表。

### 建立資料表

1.  在 Azure PowerShell 中，瀏覽至 **資料表** 資料夾 (**C:\ADFWalkthrough\Tables\**) 從您解壓縮範例的位置。 
2.  使用 cmdlet **新增 AzureRmDataFactoryDataset** 來建立資料集，如下所示的 **RawGameEventsTable**.json  


        New-AzureRmDataFactoryDataset -ResourceGroupName ADF -DataFactoryName $df –File .\RawGameEventsTable.json

    If you are using a different name for ResourceGroupName and DataFactoryName, refer them in the above cmdlet. Also, provide the full file path of the Table JSON file if the file cannot be found by the cmdlet.

3. 重複上述步驟來建立下列資料表： 
        
        New-AzureRmDataFactoryDataset -ResourceGroupName ADF -DataFactoryName $df –File .\PartitionedGameEventsTable.json
        
        New-AzureRmDataFactoryDataset -ResourceGroupName ADF -DataFactoryName $df –File .\RefGeoCodeDictionaryTable.json
            
        New-AzureRmDataFactoryDataset -ResourceGroupName ADF -DataFactoryName $df –File .\RefMarketingCampaignTable.json
            
        New-AzureRmDataFactoryDataset -ResourceGroupName ADF -DataFactoryName $df –File .\EnrichedGameEventsTable.json
            
        New-AzureRmDataFactoryDataset -ResourceGroupName ADF -DataFactoryName $df –File .\MarketingCampaignEffectivenessSQLTable.json
            
        New-AzureRmDataFactoryDataset -ResourceGroupName ADF -DataFactoryName $df –File .\MarketingCampaignEffectivenessBlobTable.json



4. 在 **Azure 入口網站**, ，按一下 [ **資料集** 中 **DATA FACTORY** 分頁 **LogProcessingFactory** 並確認您看到的所有資料集 （資料表是矩形的資料集）。 

    ![所有資料集][image-data-factory-tutorial-datasets-all]

    您也可以從 Azure PowerShell 使用下列命令：
            
        Get-AzureRmDataFactoryDataset –ResourceGroupName ADF –DataFactoryName $df

    


## <a name="MainStep5"></a> 步驟 5：建立和排程管線
在此步驟中，您將建立下列管線：PartitionGameLogsPipeline、EnrichGameLogsPipeline 和 AnalyzeMarketingCampaignPipeline。

1. 在 **Windows 檔案總管**, ，瀏覽至 **管線** 子資料夾中的 **C:\ADFWalkthrough** 資料夾 （或從您解壓縮範例的位置）。
2.  開啟 **PartitionGameLogsPipeline.json** 在您喜好的編輯器，您的儲存體帳戶資料的儲存體帳戶資訊取代反白顯示，並儲存檔案。
            
        "RAWINPUT": "wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/logs/rawgameevents/",
        "PARTITIONEDOUTPUT": "wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/logs/partitionedgameevents/",

3. 重複步驟來建立下列管線：
    1. **EnrichGameLogsPipeline**.json （3 次）
    2. **AnalyzeMarketingCampaignPipeline**.json （3 次）

    **重要事項 ︰** 確認您已取代所有 <storageaccountname> 使用您的儲存體帳戶名稱。 
 
4.  在 **PowerShell**, ，瀏覽至 **管線** 子資料夾中的 **C:\ADFWalkthrough** 資料夾 （或從您解壓縮範例的位置）。
5.  使用 cmdlet **新增 AzureRmDataFactoryPipeline** 來建立管線，如下所示的 **PartitionGameLogspeline**.json    
            
        New-AzureRmDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName $df –File .\PartitionGameLogsPipeline.json

    如果您對 ResourceGroupName、DataFactoryName 或 Pipeline 名稱使用不同的名稱，請在上述 Cmdlet 加以參考。 另外，提供管線 JSON 檔案的完整檔案路徑。
6. 重複上述步驟來建立下列管線：
    1. **EnrichGameLogsPipeline**
            
            New-AzureRmDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName $df –File .\EnrichGameLogsPipeline.json

    2. **AnalyzeMarketingCampaignPipeline**
                
            New-AzureRmDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName $df –File .\AnalyzeMarketingCampaignPipeline.json

7. 使用 cmdlet **Get AzureRmDataFactoryPipeline** 來取得管線的清單。
            
        Get-AzureRmDataFactoryPipeline –ResourceGroupName ADF –DataFactoryName $df

8. 管線建立之後，您可以指定將發生資料處理的持續時間。 藉由指定管線的作用期間，依據對每個 ADF 資料表所定義之可用性屬性，您會定義將處理資料配量的持續時間。

若要指定管線的作用期間，您可以使用 Cmdlet Set-AzureRmDataFactoryPipelineActivePeriod。 本逐步解說中，範例資料是從 05/01 到 05/05。 使用 2014-05-01 做為 StartDateTime。 EndDateTime 是選擇性的。
            
        Set-AzureRmDataFactoryPipelineActivePeriod -ResourceGroupName ADF -DataFactoryName $df -StartDateTime 2014-05-01Z -EndDateTime 2014-05-05Z –Name PartitionGameLogsPipeline
  
9. 確認以設定管線的作用期間。
            
            Confirm
            Are you sure you want to set pipeline 'PartitionGameLogsPipeline' active period from '05/01/2014 00:00:00' to '05/05/2014 00:00:00'?
            [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): n

10. 重複上述的兩個步驟來設定下列管線的作用期間。
    1. **EnrichGameLogsPipeline**
            
            Set-AzureRmDataFactoryPipelineActivePeriod -ResourceGroupName ADF -DataFactoryName $df -StartDateTime 2014-05-01Z –EndDateTime 2014-05-05Z –Name EnrichGameLogsPipeline

    2. **AnalyzeMarketingCampaignPipeline** 
            
            Set-AzureRmDataFactoryPipelineActivePeriod -ResourceGroupName ADF -DataFactoryName $df -StartDateTime 2014-05-01Z -EndDateTime 2014-05-05Z –Name AnalyzeMarketingCampaignPipeline

11. 在 **Azure 入口網站**, ，按一下 [ **管線** 磚 （不在管線的名稱） 中 **DATA FACTORY** 分頁 **LogProcessingFactory**, ，您應該會看到您所建立的管線。

    ![所有管線][image-data-factory-tutorial-pipelines-all]

12. 在 **DATA FACTORY** 分頁 **LogProcessingFactory**, ，按一下 [ **圖表**。

    ![圖表連結][image-data-factory-tutorial-diagram-link]

13. 您可以重新排列所看見的圖表，這裡是重新排列的圖表，在頂端顯示直接輸入，在底端則是輸出。 您可以看到的輸出 **PartitionGameLogsPipeline** 中傳遞至 enrichgamelogspipeline 的輸出做為輸入 **EnrichGameLogsPipeline** 傳遞至 **AnalyzeMarketingCampaignPipeline**。 按兩下某個標題，以查看分頁所代表之成品的詳細資訊。

    ![圖表檢視][image-data-factory-tutorial-diagram-view]

    **恭喜！** 您已成功建立 Azure Data Factory、連結的服務、管線、資料表，並開始工作流程。 


## <a name="MainStep6"></a> 步驟 6：監視管線和資料配量 

1.  如果您的 LogProcessingFactory 的 Data Factory 分頁未開啟，您可以執行下列其中一項：
    1.  按一下 [ **LogProcessingFactory** 上 **儀表板**。 建立資料處理站時 **新增至開始面板** 自動核取選項。

        ![監視「開始面板」][image-data-factory-monitoring-startboard]

    2. 按一下 [ **瀏覽** 中樞，然後按一下 [ **一切**。
        
        ![監視中樞所有項目][image-data-factory-monitoring-hub-everything]

        在 **瀏覽** 分頁中，選取 **Data factory** ，然後選取 **LogProcessingFactory** 中 **Data factory** 刀鋒視窗。

        ![監視瀏覽 Data Factory][image-data-factory-monitoring-browse-datafactories]
2. 您可以以數種方式監視您的 Data Factory。 您可以從管線或資料集開始。 讓我們從管線開始，並進一步向下鑽研。 
3.  按一下 [ **管線** 上 **DATA FACTORY** 刀鋒視窗。 
4.  按一下 [ **PartitionGameLogsPipeline** 管線分頁中。 
5.  在 **管線** 分頁 **PartitionGameLogsPipeline**, ，您會看見管線使用 **RawGameEventsTable** 資料集。  按一下 [ **RawGameEventsTable**。

    ![管線已取用且已生產][image-data-factory-monitoring-pipeline-consumed-produced]

6. 中的資料表分頁 **RawGameEventsTable**, ，您會看見所有配量。 在下列螢幕擷取畫面，所有的配量都處於 **準備** 狀態並沒有任何問題配量。 這表示資料已就緒可供處理。 

    ![RawGameEventsTable [資料表] 刀鋒視窗][image-data-factory-monitoring-raw-game-events-table]
 
7. 現在，在 **管線** 分頁 **PartiionGameLogsPipeline**, ，按一下 [ **產生**。 
8. 您應該會看到這個管線產生的資料集的清單： 
9. 按一下 [ **PartitionedGameEvents** 資料表中 **產生資料集** 刀鋒視窗。 
10. 確認 **狀態** 的所有配量設 **準備**。 
11. 按一下其中一個配量， **準備** 查看 **資料配量** 該配量分頁。

    ![RawGameEventsTable [資料配量] 刀鋒視窗][image-data-factory-monitoring-raw-game-events-table-dataslice-blade]

    如果發生錯誤，您在這裡會看到 **[Failed] **狀態。  您也可能會看到狀態是兩個配量 **準備**, ，或兩者皆擁有狀態 **PendingValidation**, ，根據處理配量的速度。
 
    請參閱 [Azure Data Factory 開發人員參考][developer-reference] ，以了解所有可能的配量狀態。

12. 在 **資料配量** 刀鋒視窗中，按一下 [從執行 **活動執行** 清單。 您應該會看到該配量的「活動執行」分頁。 您應該會看到下列 **活動執行詳細資料** 刀鋒視窗。

    ![[活動執行詳細資料] 刀鋒視窗][image-data-factory-monitoring-activity-run-details]

13. 按一下 [ **下載** 下載檔案。 對 HDInsight 處理的錯誤進行疑難排解時，這個畫面會特別有用。 
     
    
當所有管線都完成執行時，您可以考慮 **Marketingcampaigns** 中 **MarketingCampaigns** Azure SQL 資料庫，以檢視結果。 

**恭喜！** 您現在可以監視和疑難排解工作流程。 您已經學會如何使用 Azure Data Factory 來處理資料，並取得分析。

## 延伸教學課程來使用內部部署資料
在本文逐步解說的記錄檔處理案例的最後一個步驟中，行銷活動成效輸出已複製到 Azure SQL Database。 您也可以在組織內，將此資料移動到內部部署 SQL Server 以進行分析。
 
若要從 Azure Blob 將行銷活動成效資料複製到內部部署 SQL Server，您需要建立本文逐步解說中所介紹的額外內部部署連結服務、資料表和管線。

練習 [逐步解說 ︰ 使用內部部署資料來源][tutorial-onpremises-using-powershell] 以了解如何建立管線，以將行銷活動成效資料複製到內部部署 SQL Server 資料庫。
 

[monitor-manage-using-powershell]: data-factory-monitor-manage-using-powershell.md
[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456

[adftutorial-using-editor]: data-factory-tutorial.md

[adfgetstarted]: data-factory-get-started.md
[adfintroduction]: data-factory-introduction.md
[usepigandhive]: data-factory-data-transformation-activities.md
[tutorial-onpremises-using-powershell]: data-factory-tutorial-extend-onpremises-using-powershell.md
[download-azure-powershell]: ../powershell-install-configure.md

[azure-portal]: http://portal.azure.com
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[sqlcmd-install]: http://www.microsoft.com/download/details.aspx?id=35580
[azure-sql-firewall]: http://msdn.microsoft.com/library/azure/jj553530.aspx


[adfwalkthrough-download]: http://go.microsoft.com/fwlink/?LinkId=517495
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908

[old-cmdlet-reference]: https://msdn.microsoft.com/library/azure/dn820234(v=azure.98).aspx


[image-data-factory-tutorial-end-to-end-flow]: ./media/data-factory-tutorial-using-powershell/EndToEndWorkflow.png

[image-data-factory-tutorial-partition-game-logs-pipeline]: ./media/data-factory-tutorial-using-powershell/PartitionGameLogsPipeline.png

[image-data-factory-tutorial-enrich-game-logs-pipeline]: ./media/data-factory-tutorial-using-powershell/EnrichGameLogsPipeline.png

[image-data-factory-tutorial-analyze-marketing-campaign-pipeline]: ./media/data-factory-tutorial-using-powershell/AnalyzeMarketingCampaignPipeline.png


[image-data-factory-tutorial-egress-to-onprem-pipeline]: ./media/data-factory-tutorial-using-powershell/EgreeDataToOnPremPipeline.png

[image-data-factory-tutorial-set-firewall-rules-azure-db]: ./media/data-factory-tutorial-using-powershell/SetFirewallRuleForAzureDatabase.png

[image-data-factory-tutorial-portal-new-everything]: ./media/data-factory-tutorial-using-powershell/PortalNewEverything.png

[image-data-factory-tutorial-datastorage-cache-backup]: ./media/data-factory-tutorial-using-powershell/DataStorageCacheBackup.png

[image-data-factory-tutorial-dataservices-blade]: ./media/data-factory-tutorial-using-powershell/DataServicesBlade.png

[image-data-factory-tutorial-new-datafactory-blade]: ./media/data-factory-tutorial-using-powershell/NewDataFactoryBlade.png

[image-data-factory-tutorial-resourcegroup-blade]: ./media/data-factory-tutorial-using-powershell/ResourceGroupBlade.png

[image-data-factory-tutorial-create-resourcegroup]: ./media/data-factory-tutorial-using-powershell/CreateResourceGroup.png

[image-data-factory-tutorial-datafactory-homepage]: ./media/data-factory-tutorial-using-powershell/DataFactoryHomePage.png

[image-data-factory-tutorial-create-datafactory]: ./media/data-factory-tutorial-using-powershell/CreateDataFactory.png

[image-data-factory-tutorial-linkedservice-tile]: ./media/data-factory-tutorial-using-powershell/LinkedServiceTile.png

[image-data-factory-tutorial-linkedservices-add-datstore]: ./media/data-factory-tutorial-using-powershell/LinkedServicesAddDataStore.png

[image-data-factory-tutorial-datastoretype-azurestorage]: ./media/data-factory-tutorial-using-powershell/DataStoreTypeAzureStorageAccount.png

[image-data-factory-tutorial-azurestorage-settings]: ./media/data-factory-tutorial-using-powershell/AzureStorageSettings.png

[image-data-factory-tutorial-storage-key]: ./media/data-factory-tutorial-using-powershell/StorageKeyFromAzurePortal.png

[image-data-factory-tutorial-linkedservices-blade-storage]: ./media/data-factory-tutorial-using-powershell/LinkedServicesBladeWithAzureStorage.png

[image-data-factory-tutorial-azuresql-settings]: ./media/data-factory-tutorial-using-powershell/AzureSQLDatabaseSettings.png

[image-data-factory-tutorial-azuresql-database-connection-string]: ./media/data-factory-tutorial-using-powershell/DatabaseConnectionString.png

[image-data-factory-tutorial-linkedservices-all]: ./media/data-factory-tutorial-using-powershell/LinkedServicesAll.png

[image-data-factory-tutorial-datasets-all]: ./media/data-factory-tutorial-using-powershell/DataSetsAllTables.png

[image-data-factory-tutorial-pipelines-all]: ./media/data-factory-tutorial-using-powershell/AllPipelines.png

[image-data-factory-tutorial-diagram-link]: ./media/data-factory-tutorial-using-powershell/DataFactoryDiagramLink.png

[image-data-factory-tutorial-diagram-view]: ./media/data-factory-tutorial-using-powershell/DiagramView.png

[image-data-factory-monitoring-startboard]: ./media/data-factory-tutorial-using-powershell/MonitoringStartBoard.png

[image-data-factory-monitoring-hub-everything]: ./media/data-factory-tutorial-using-powershell/MonitoringHubEverything.png

[image-data-factory-monitoring-browse-datafactories]: ./media/data-factory-tutorial-using-powershell/MonitoringBrowseDataFactories.png

[image-data-factory-monitoring-pipeline-consumed-produced]: ./media/data-factory-tutorial-using-powershell/MonitoringPipelineConsumedProduced.png

[image-data-factory-monitoring-raw-game-events-table]: ./media/data-factory-tutorial-using-powershell/MonitoringRawGameEventsTable.png

[image-data-factory-monitoring-raw-game-events-table-dataslice-blade]: ./media/data-factory-tutorial-using-powershell/MonitoringPartitionGameEventsTableDataSliceBlade.png

[image-data-factory-monitoring-activity-run-details]: ./media/data-factory-tutorial-using-powershell/MonitoringActivityRunDetails.png

[image-data-factory-datamanagementgateway-configuration-manager]: ./media/data-factory-tutorial-using-powershell/DataManagementGatewayConfigurationManager.png

[image-data-factory-new-datafactory-menu]: ./media/data-factory-tutorial-using-powershell/NewDataFactoryMenu.png

[image-data-factory-new-datafactory-create-button]: ./media/data-factory-tutorial-using-powershell/DataFactoryCreateButton.png 

