<properties
    pageTitle="使用 Visual Studio 建置您的第一個 Azure Data Factory 管線。"
    description="在本教學課程中，您將使用 Visual Studio，建立範例 Azure Data Factory 管線。"
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
    ms.topic="hero-article" 
    ms.date="11/02/2015"
    ms.author="spelluru"/>

# 使用 Visual Studio 建置您的第一個 Azure Data Factory 管線。
> [AZURE.SELECTOR]
- [教學課程概觀](data-factory-build-your-first-pipeline.md)
- [使用 Data Factory 編輯器](data-factory-build-your-first-pipeline-using-editor.md)
- [使用 PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
- [使用 Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
- [使用資源管理員範本](data-factory-build-your-first-pipeline-using-arm.md)


在本文中，您將了解如何使用 Visual Studio 來建立您的第一個管線。 本教學課程包含下列步驟：

2.  建立連結服務 (資料存放區、運算)。
3.  建立資料集。
3.  建立管線。
4.  建立 Data Factory，以及部署連結的服務、資料集和管線。 

本文不提供 Azure Data Factory 服務的概念性概觀。 如需服務的詳細概觀，請參閱 [簡介 Azure Data Factory](data-factory-introduction.md) 文件。

> [AZURE.IMPORTANT] 請 [教學課程概觀](data-factory-build-your-first-pipeline.md) 文件，先完成先決條件步驟才能執行本教學課程。  

## 逐步解說：使用 Visual Studio 建立及部署 Data Factory 實體 

### 必要條件

您必須已在電腦上安裝下列項目： 

- Visual Studio 2013 或 Visual Studio 2015
- 下載 Azure SDK for Visual Studio 2013 或 Visual Studio 2015。 瀏覽至 [Azure 下載頁面](http://azure.microsoft.com/downloads/) 按一下 **VS 2013** 或 **VS 2015** 中 **.NET** 一節。
- 下載最新的 Azure Data Factory 外掛程式適用於 Visual Studio: [VS 2013](https://visualstudiogallery.msdn.microsoft.com/754d998c-8f92-4aa7-835b-e89c8c954aa5) 或 [VS 2015](https://visualstudiogallery.msdn.microsoft.com/371a4cf9-0093-40fa-b7dd-be3c74f49005)。 如果您使用 Visual Studio 2013，您也可以更新外掛程式執行下列步驟: 在功能表上，按一下 [ **工具** ]-> [ **擴充功能和更新** ]-> [ **線上** ]-> [ **Visual Studio 元件庫** ]-> [ **Microsoft Azure Data Factory Tools for Visual Studio** ]-> [ **更新**。 
    
    

### 建立 Visual Studio 專案 
1. 啟動 **Visual Studio 2013** 或 **Visual Studio 2015**。 按一下 [ **檔案**, ，指向 [ **新增**, ，然後按一下 **專案**。 您應該會看到 **新的專案** 對話方塊。  
2. 在 **新的專案** 對話方塊中，選取 **DataFactory** 範本，然後按一下 [ **空白 Data Factory 專案**。   

    ![[新增專案] 對話方塊](./media/data-factory-build-your-first-pipeline-using-vs/new-project-dialog.png)

3. 輸入 **名稱** 的專案， **位置**, ，和名 **方案**, ，按一下 **確定**。

    ![Solution Explorer](./media/data-factory-build-your-first-pipeline-using-vs/solution-explorer.png)

### 建立連結服務
在此步驟中，您會將您的 Azure 儲存體帳戶和隨選 Azure HDInsight 叢集連結至 Data Factory，然後建立資料集以表示由 Hive 處理的輸出資料。


#### 建立 Azure 儲存體連結服務


4. 以滑鼠右鍵按一下 **連結服務** 在 [方案總管] 中，指向 **新增**, ，然後按一下 **新項目**。      
5. 在 **加入新項目** 對話方塊中，選取 **Azure 儲存體連結服務** 從清單中，然後按一下 **新增**。 

    ![新的連結服務](./media/data-factory-build-your-first-pipeline-using-vs/new-linked-service-dialog.png)
 
3. 取代 **accountname** 和 **accountkey** 與 Azure 儲存體帳戶和其索引鍵的名稱。 

    ![Azure 儲存體連結服務](./media/data-factory-build-your-first-pipeline-using-vs/azure-storage-linked-service.png)

4. 儲存 **AzureStorageLinkedService1.json** 檔案。

#### 建立 Azure HDInsight 連結服務
現在，您將為用來執行 Hive 指令碼的隨選 HDInsight 叢集建立連結服務。 

1. 在 **方案總管] 中**, ，以滑鼠右鍵按一下 **連結服務**, ，指向 **新增**, ，然後按一下 **新項目**。
2. 選取 **HDInsight 隨選連結服務**, ，然後按一下 **新增**。 
3. 取代 **JSON** 取代為下列:

        {
          "name": "HDInsightOnDemandLinkedService",
          "properties": {
            "type": "HDInsightOnDemand",
            "typeProperties": {
              "version": "3.2",
              "clusterSize": 1,
              "timeToLive": "00:30:00",
              "linkedServiceName": "AzureStorageLinkedService1"
            }
          }
        }
    
    下表提供程式碼片段中所使用之 JSON 屬性的描述：
    
    屬性 | 說明
    -------- | -----------
    版本 | 指定所建立的 HDInsight 版本為 3.2。 
    ClusterSize | 建立一個單一節點的 HDInsight 叢集。 
    TimeToLive | 指定 HDInsight 叢集在被刪除之前的閒置時間。
    linkedServiceName | 指定將用來儲存 HDInsight 產生之記錄檔的儲存體帳戶

4. 儲存 **HDInsightOnDemandLinkedService1.json** 檔案。
 
### 建立輸出資料集
現在，您將建立輸出資料集來代表 Azure Blob 儲存體中儲存的資料。 

1. 在 **方案總管] 中**, ，以滑鼠右鍵按一下指向 **新增**, ，然後按一下 **新項目**。 
2. 選取 **Azure Blob** 從清單中，然後按一下 **新增**。 
3. 取代 **JSON** 在編輯器中使用下列: 在 JSON 片段中，您要建立資料集稱為 **AzureBlobOutput**, ，並指定將由 Hive 指令碼產生的資料結構。 此外，您指定的結果會儲存在稱為 blob 容器 **資料** 和該資料夾命名為 **partitioneddata**。  **可用性** 區段指定每月為基礎產生輸出資料集。
    
        {
          "name": "AzureBlobOutput",
          "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService1",
            "typeProperties": {
              "folderPath": "data/partitioneddata",
              "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
              }
            },
            "availability": {
              "frequency": "Month",
              "interval": 1
            }
          }
        }

4. 儲存 **AzureBlobLocation1.json** 檔案。


### 建立您的第一個管線
在此步驟中，您將建立您的第一個管線。

1. 在 **方案總管] 中**, ，以滑鼠右鍵按一下 **管線**, ，指向 **新增**, ，然後按一下 **新項目。** 
2. 選取 **Hive 轉換管線** 從清單中，然後按一下 **新增**。 
3. 取代 **JSON** 與下列程式碼片段。

    > [AZURE.IMPORTANT] 取代 **storageaccountname** 與儲存體帳戶的名稱。

        {
          "name": "MyFirstPipeline",
          "properties": {
            "description": "My first Azure Data Factory pipeline",
            "activities": [
              {
                "type": "HDInsightHive",
                "typeProperties": {
                  "scriptPath": "script/partitionweblogs.hql",
                  "scriptLinkedService": "AzureStorageLinkedService1",
                  "defines": {
                    "partitionedtable": "wasb://data@<storageaccountname>.blob.core.windows.net/partitioneddata"
                  }
                },
                "outputs": [
                  {
                    "name": "AzureBlobOutput"
                  }
                ],
                "policy": {
                  "concurrency": 1,
                  "retry": 3
                },
                "name": "RunSampleHiveActivity",
                "linkedServiceName": "HDInsightOnDemandLinkedService"
              }
            ],
            "start": "2014-01-01",
            "end": "2014-01-02"
          }
        }

    您會在 JSON 程式碼片段中建立一個管線，其中包括在 HDInsight 叢集上使用 Hive 處理「資料」的單一活動。
    
    Hive 指令碼檔案中， **partitionweblogs.hql**, ，會儲存在 Azure 儲存體帳戶 (透過 scriptLinkedService，呼叫指定 **AzureStorageLinkedService1**)，且在容器中名為 **指令碼**。

     **定義** 區段用來指定將會傳遞到 hive 指令碼作為 Hive 設定值 (例如 ${hiveconf: partitioneddata}) 的執行階段設定。

     **啟動** 和 **結束** 管線屬性會指定管線作用期間。

    在活動 JSON 中，您可以指定 Hive 指令碼執行所連結的服務 – 指定的運算上 **HDInsightOnDemandLinkedService**。
3. 儲存 **HiveActivity1.json** 檔案。

### 新增 partitionweblogs.hql 做為相依性 

1. 以滑鼠右鍵按一下 **相依性** 中 **方案總管] 中** ] 視窗中，指向 **新增**, ，然後按一下 **現有項目**。  
2. 瀏覽至 **C:\ADFGettingStarted** ，然後選取 **partitionweblogs.hql** 檔案，然後按一下 **新增**。 

當您在下一個步驟中發佈方案時，HQL 檔案會上傳到 Blob 儲存體中的指令碼容器。  

### 發佈/部署 Data Factory 實體

18. 在 [方案總管] 中的專案上按一下滑鼠右鍵，然後按一下 [ **發行**。 
19. 如果您看到 **登入您的 Microsoft 帳戶** 對話方塊中，輸入您的認證具有 Azure 的訂用帳戶的帳戶，然後按一下 **登入**。
20. 您應該會看到下列對話方塊：

    ![發佈對話方塊](./media/data-factory-build-your-first-pipeline-using-vs/publish.png)

21. 在 [設定 Data Factory] 頁面中，執行下列作業： 
    1. 選取 **建立新的 Data Factory** 選項。
    2. 輸入 **FirstPipelineUsingVS** 的 **名稱**。 
    
        > [AZURE.IMPORTANT] Azure Data Factory 的名稱必須是全域唯一的。 如果您收到錯誤 **Data factory 名稱"FirstPipelineUsingVS"沒有** 發行時，請變更名稱 (例如，yournameFirstPipelineUsingVS)。 請參閱 [Data Factory-命名規則](data-factory-naming-rules.md) Data Factory 成品的命名規則的主題。 
        > 
        > Data Factory 的名稱未來可能會註冊為 DNS 名稱，因此會變成公開可見的名稱。
    3.選取適合的訂閱 **訂閱** 欄位。 
    4.選取 **資源群組** 建立的 data factory。 
    5.選取 **區域** 的 data factory。 
    6.按一下 [ **下一步** 切換至 **發行的項目** 頁面。 (按 **索引標籤上** 移出 [名稱] 欄位若 **下一步** 按鈕已停用。) 
23. 在 **發行的項目** 頁面上，確定資料處理站的所有實體都已選取，按一下 [ **下一步** 切換至 **摘要** 頁面。     
24. 檢閱摘要，並按一下 [ **下一步** 來啟動部署程序，並檢視 **部署狀態**。
25. 在 **部署狀態** ] 頁面上，您應該會看到部署程序的狀態。 部署完成後按一下 [完成]。 
 

## 使用 [伺服器總管] 檢視 Data Factory 實體

1. 在 **Visual Studio**, ，按一下 [ **檢視** ] 功能表上，然後按一下 [ **伺服器總管**。
2. 在 [伺服器總管] 視窗中，展開 **Azure** 展開 **Data Factory**。 如果您看到 **登入 Visual Studio**, ，輸入 **帳戶** 與您的 Azure 訂用帳戶，然後按一下相關聯 **繼續**。 輸入 **密碼**, ，然後按一下 **登入**。 Visual Studio 會嘗試取得訂用帳戶中所有 Azure Data Factory 的相關資訊。 您會看到這項作業中的狀態 **Data Factory 工作清單** 視窗。

    ![Server Explorer](./media/data-factory-build-your-first-pipeline-using-vs/server-explorer.png)
3. 您可以在 data factory，以滑鼠右鍵按一下並選取 **Data Factory 匯出至新的專案** 建立 Visual Studio 專案是根據現有的 data factory。

    ![匯出 Data Factory](./media/data-factory-build-your-first-pipeline-using-vs/export-data-factory-menu.png)

## 更新 Visual studio 的 Data Factory 工具

若要更新 Visual Studio 的 Azure Data Factory 工具，請執行下列作業：

1. 按一下 [ **工具** 功能表，然後選取 **擴充功能和更新**。
2. 選取 **更新** 左的窗格，然後選取 **Visual Studio 元件庫**。
3. 選取 **Visual Studio 的 Azure Data Factory 工具** 按一下 **更新**。 如果您看不到此項目，代表您已經有最新版本的工具。 

請參閱 [監視資料集和管線](data-factory-monitor-manage-pipelines.md) 如需如何使用 Azure 入口網站來監視管線和資料集的指示您在本教學課程中建立。
 

## 後續步驟
在本文中，您已經建立可在隨選 HDInsight 叢集上執行 Hive 指令碼，含有轉換活動 (HDInsight 活動) 的管線。 若要了解如何使用 「 複製活動將資料從 Azure Blob 複製到 Azure SQL，請參閱 [教學課程: 複製資料，從 Azure blob 至 SQL Azure](data-factory-get-started.md)。
  

