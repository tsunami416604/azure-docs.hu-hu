<properties
    pageTitle="使用 Data Factory 編輯器建置您的第一個 Azure Data Factory 管線。"
    description="在本教學課程中，您將使用 Azure 入口網站中的 Data Factory 編輯器，建立範例 Azure Data Factory 管線。"
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

# 使用 Data Factory 編輯器 (Azure 入口網站) 建置您的第一個 Azure Data Factory 管線。
> [AZURE.SELECTOR]
- [教學課程概觀](data-factory-build-your-first-pipeline.md)
- [使用 Data Factory 編輯器](data-factory-build-your-first-pipeline-using-editor.md)
- [使用 PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
- [使用 Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
- [使用資源管理員範本](data-factory-build-your-first-pipeline-using-arm.md)


在本文中，您將學習如何使用 [Azure 入口網站](https://portal.azure.com/) 來建立您的第一個管線。 本教學課程包含下列步驟：

1.  建立 Data Factory
2.  建立連結服務 (資料存放區、計算) 和資料集
3.  建立管線

本文不提供 Azure Data Factory 服務的概念性概觀。 如需服務的詳細概觀，請參閱 [簡介 Azure Data Factory](data-factory-introduction.md) 文件。

> [AZURE.IMPORTANT] 請 [教學課程概觀](data-factory-build-your-first-pipeline.md) 文件，先完成先決條件步驟才能執行本教學課程。  

## 步驟 1：建立 Data Factory

1.  登入後 [Azure 入口網站](http://portal.azure.com/), ，執行下列動作 ︰
    1.  按一下 [ **新增** 左側功能表。 
    2.  按一下 [ **資料分析** 中 **建立** 刀鋒視窗。
    3.  按一下 [ **Data Factory** 上 **資料分析** 刀鋒視窗。

        ![建立刀鋒視窗](./media/data-factory-build-your-first-pipeline-using-editor/create-blade.png)

2.  在 **新增 data factory** 刀鋒視窗中，輸入 **DataFactoryMyFirstPipeline** 的名稱。

    ![新增 Data Factory 刀鋒視窗](./media/data-factory-build-your-first-pipeline-using-editor/new-data-factory-blade.png)

    > [AZURE.IMPORTANT] Azure data factory 的名稱必須是全域唯一的。 如果您收到錯誤 ︰ **Data factory 名稱"DataFactoryMyFirstPipeline"沒有**, 、 變更 data factory (比方說，yournameDataFactoryMyFirstPipeline) 的名稱，然後嘗試重新建立。 請參閱 [Data Factory-命名規則](data-factory-naming-rules.md) Data Factory 成品的命名規則的主題。
    >  
    > Data Factory 的名稱未來可能會註冊為 DNS 名稱，因此會變成公開可見的名稱。

3.  如果您尚未建立任何資源群組，您必須建立資源群組。 作法：
    1.  按一下 [ **資源群組名稱**。
    2.  選取 **建立新的資源群組** 中 **資源群組** 刀鋒視窗。
    3.  輸入 **ADF** 的 **名稱** 中 **建立資源群組** 刀鋒視窗。
    4.  按一下 [ **確定**。
    
        ![建立資源群組](./media/data-factory-build-your-first-pipeline-using-editor/create-resource-group.png)
4.  選取資源群組之後，請確認您使用的是要在其中建立 Data Factory 的正確訂用帳戶。
5.  按一下 [ **建立** 上 **新增 data factory** 刀鋒視窗。
6.  您會看到 data factory 中建立 **儀表板** 的 Azure 入口網站，如下所示 ︰   

    ![建立 Data Factory 狀態](./media/data-factory-build-your-first-pipeline-using-editor/creating-data-factory-image.png)
7. 恭喜！ 您已成功建立您的第一個 Data Factroy。 在 Data Factory 成功建立後，您會看到 Data Factory 頁面，顯示 Data Factory 的內容。  

    ![Data Factory 刀鋒視窗](./media/data-factory-build-your-first-pipeline-using-editor/data-factory-blade.png)

在後續步驟中，您將了解如何建立將在本教學課程使用的連結服務、資料集及管線。 

## 步驟 2：建立連結服務和資料集
在此步驟中，您會將您的 Azure 儲存體帳戶和隨選 Azure HDInsight 叢集連結至 Data Factory，然後建立資料集以表示由 Hive 處理的輸出資料。

### 建立 Azure 儲存體連結服務
1.  按一下 [ **作者和部署** 上 **DATA FACTORY** 分頁 **DataFactoryFirstPipeline**。 這會啟動 Data Factory 編輯器。 
     
    ![[製作和部署] 圖格](./media/data-factory-build-your-first-pipeline-using-editor/data-factory-author-deploy.png)
2.  按一下 [ **新增資料存放區** 選擇 **Azure 儲存體**
    
    ![Azure 儲存體連結服務](./media/data-factory-build-your-first-pipeline-using-editor/azure-storage-linked-service.png)

    在編輯器中，您應該會看到用來建立 Azure 儲存體連結服務的 JSON 指令碼。 
4. 取代 **帳戶名稱** 的 Azure 儲存體帳戶名稱和 **帳戶金鑰** 與 Azure 儲存體帳戶的存取金鑰。 若要了解如何取得儲存體存取金鑰，請參閱 [檢視、 複製和重新產生儲存體存取金鑰](../storage/storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys)
5. 按一下 [ **部署** 命令列來部署連結的服務。

    ![[部署] 按鈕](./media/data-factory-build-your-first-pipeline-using-editor/deploy-button.png)

### 建立 Azure HDInsight 連結服務
現在，您將為用來執行 Hive 指令碼的隨選 HDInsight 叢集建立連結服務。 

1. 在 **Data Factory 編輯器**, ，按一下 [ **新增計算** 命令列，然後選取 **隨 HDInsight 叢集**。

    ![新增計算](./media/data-factory-build-your-first-pipeline-using-editor/new-compute-menu.png)
2. 複製下列程式碼片段並貼到 Draft-1 視窗。 此 JSON 程式碼片段描述將用來建立隨選 HDInsight 叢集的屬性。 

        {
          "name": "HDInsightOnDemandLinkedService",
          "properties": {
            "type": "HDInsightOnDemand",
            "typeProperties": {
              "version": "3.2",
              "clusterSize": 1,
              "timeToLive": "00:30:00",
              "linkedServiceName": "StorageLinkedService"
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
3. 按一下 [ **部署** 命令列來部署連結的服務。 
4. 確認您有在左側的樹狀檢視中看到 StorageLinkedService 和 HDInsightOnDemandLinkedService。

    ![含連結服務的樹狀檢視](./media/data-factory-build-your-first-pipeline-using-editor/tree-view-linked-services.png)
 
### 建立輸出資料集
現在，您將建立輸出資料集來代表 Azure Blob 儲存體中儲存的資料。 

1. 在 **Data Factory 編輯器**, ，按一下 [ **新的資料集** 命令列，然後選取 **Azure Blob 儲存體**。  

    ![新增資料集](./media/data-factory-build-your-first-pipeline-using-editor/new-data-set.png)
2. 複製下列程式碼片段並貼到 Draft-1 視窗。 在 JSON 片段中，您要建立資料集稱為 **AzureBlobOutput**, ，並指定將由 Hive 指令碼產生的資料結構。 此外，您指定的結果會儲存在稱為 blob 容器 **資料** 和該資料夾命名為 **partitioneddata**。  **可用性** 區段指定每月為基礎產生輸出資料集。
    
        {
          "name": "AzureBlobOutput",
          "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "StorageLinkedService",
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

3. 按一下 [ **部署** 命令列來部署新建立的資料集。
4. 驗證資料集已成功建立。

    ![含連結服務的樹狀檢視](./media/data-factory-build-your-first-pipeline-using-editor/tree-view-data-set.png)

## 步驟 3：建立您的第一個管線
在此步驟中，您將建立您的第一個管線。

1. 在 **Data Factory 編輯器**, ，按一下 [ **省略符號 （...）** 然後按一下 [ **新增管線**。
    
    ![新增管線按鈕](./media/data-factory-build-your-first-pipeline-using-editor/new-pipeline-button.png)
2. 複製下列程式碼片段並貼到 Draft-1 視窗。

    > [AZURE.IMPORTANT] 取代 **storageaccountname** 在 JSON 中的儲存體帳戶的名稱。

        {
          "name": "MyFirstPipeline",
          "properties": {
            "description": "My first Azure Data Factory pipeline",
            "activities": [
              {
                "type": "HDInsightHive",
                "typeProperties": {
                  "scriptPath": "script/partitionweblogs.hql",
                  "scriptLinkedService": "StorageLinkedService",
                  "defines": {
                    "partitionedtable": "wasb://data@<storageaccountname>.blob.core.windows.net/partitioneddata"
                  }
                },
                "outputs": [
                  {
                    "name": "AzureBlobOutput"
                  }
                ],
                "scheduler": {
                    "frequency": "Month",
                    "interval": 1
                },
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
    
    Hive 指令碼檔案中， **partitionweblogs.hql**, ，會儲存在 Azure 儲存體帳戶 (透過 scriptLinkedService，呼叫指定 **StorageLinkedService**)，且在容器中名為 **指令碼**。

     **定義** 區段用來指定將會傳遞到 hive 指令碼作為 Hive 設定值 （例如 ${hiveconf: partitioneddata}） 的執行階段設定。

     **啟動** 和 **結束** 管線屬性會指定管線作用期間。

    在活動 JSON 中，您可以指定 Hive 指令碼執行所連結的服務 – 指定的運算上 **HDInsightOnDemandLinkedService**。
3. 按一下 [ **部署** 命令列來部署管線。
4. 確認您在樹狀檢視中看到管線。

    ![管線的樹狀檢視](./media/data-factory-build-your-first-pipeline-using-editor/tree-view-pipeline.png)
5. 恭喜，您已經成功建立您的第一個管線！
6. 按一下 [ **X** 關閉 Data Factory 編輯器] 刀鋒視窗，並以瀏覽回 [Data Factory] 分頁中，並按一下 [ **圖表**。
  
    ![[圖表] 磚](./media/data-factory-build-your-first-pipeline-using-editor/diagram-tile.png)
7. 在 [圖表檢視] 中，您會看到管線的概觀，以及在本教學課程中使用的資料集。
    
    ![圖表檢視](./media/data-factory-build-your-first-pipeline-using-editor/diagram-view-2.png) 
8. 在 [圖表] 檢視中，按兩下資料集 **AzureBlobOutput**。 您會看到目前正在處理的配量。

    ![Dataset](./media/data-factory-build-your-first-pipeline-using-editor/dataset-blade.png)
9. 當處理完成時，您會看到配量處於 **準備** 狀態。 請注意，建立隨選 HDInsight 叢集通常需要一些時間。 

    ![Dataset](./media/data-factory-build-your-first-pipeline-using-editor/dataset-slice-ready.png) 
10. 當配量處於 **準備** 狀態時，檢查 **partitioneddata** 資料夾中的 **資料** 輸出資料的 blob 儲存體中的容器。  
 

 

## 後續步驟
在本文中，您已經建立可在隨選 HDInsight 叢集上執行 Hive 指令碼，含有轉換活動 (HDInsight 活動) 的管線。 若要了解如何使用 「 複製活動將資料從 Azure Blob 複製到 Azure SQL，請參閱 [教學課程 ︰ 複製資料，從 Azure blob 至 SQL Azure](./data-factory-get-started.md)。
  



