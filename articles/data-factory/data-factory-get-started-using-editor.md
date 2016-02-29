<properties 
    pageTitle="教學課程：使用 Data Factory 編輯器建立具有複製活動的管線" 
    description="在本教學課程中，您將使用 Azure 傳統入口網站中的 Data Factory 編輯器，來建立具有複製活動的 Azure Data Factory 管線。" 
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
    ms.topic="get-started-article" 
    ms.date="11/02/2015" 
    ms.author="spelluru"/>

# 教學課程：使用 Data Factory 編輯器建立具有複製活動的管線
> [AZURE.SELECTOR]
- [教學課程概觀](data-factory-get-started.md)
- [使用 Data Factory 編輯器](data-factory-get-started-using-editor.md)
- [使用 PowerShell](data-factory-monitor-manage-using-powershell.md)
- [使用 Visual Studio](data-factory-get-started-using-vs.md)



##本教學課程內容
本教學課程包含下列步驟：

步驟 | 說明
-----| -----------
[步驟 1：建立 Azure Data Factory](#CreateDataFactory) | 在此步驟中，您將建立名為 Azure data factory **ADFTutorialDataFactory**。  
[步驟 2：建立連結服務](#CreateLinkedServices) | 在此步驟中，您將建立兩個連結的服務: **StorageLinkedService** 和 **AzureSqlLinkedService**。 StorageLinkedService 會連結 Azure 儲存體，而 AzureSqlLinkedService 會將 Azure SQL Database 連結至 ADFTutorialDataFactory。 管線的輸入資料位於 Azure Blob 儲存體的 Blob 容器中，輸出資料則儲存在 Azure SQL Database 的資料表中。 因此，您可以將這兩個資料存放區以連結服務的形式新增至 Data Factory。      
[步驟 3：建立輸入和輸出資料表](#CreateInputAndOutputDataSets) | 在上一個步驟中，您所建立的連結服務參照含有輸入/輸出資料的資料存放區。 在此步驟中，您將定義兩個 data factory 資料表: **EmpTableFromBlob** 和 **EmpSQLTable** -代表資料存放區中的輸入/輸出資料。 您將針對 EmpTableFromBlob，指定所含 Blob 具有來源資料的 Blob 容器，而針對 EmpSQLTable 指定可儲存輸出資料的 SQL 資料表。 您也將指定其他屬性 (例如資料結構、資料可用性等)。 
[步驟 4：建立和執行管線](#CreateAndRunAPipeline) | 在此步驟中，您將建立名為管線 **ADFTutorialPipeline** ADFTutorialDataFactory 中。 管線會有 **複製活動** ，將輸入資料從 Azure blob 複製到輸出 Azure SQL 資料表。
[步驟 5：監視配量和管線](#MonitorDataSetsAndPipeline) | 在此步驟中，您將使用 Azure 入口網站來監視輸入和輸出資料表的配量。
 

## <a name="CreateDataFactory"></a>步驟 1：建立 Azure Data Factory
在此步驟中，您可以使用 Azure 入口網站建立名為 Azure data factory **ADFTutorialDataFactory**。

1.  登入後 [Azure 入口網站][azure-portal], ，按一下 [ **新增** 從左下角，選取 **資料分析** 中 **建立** 刀鋒視窗中，然後按一下 [ **Data Factory** 中 **資料分析** 刀鋒視窗。 

    ![新增->DataFactory][image-data-factory-new-datafactory-menu]    

6. 在 **新增 data factory** 刀鋒視窗中:
    1. 輸入 **ADFTutorialDataFactory** 的 **名稱**。 
    
        ![新增 Data Factory 刀鋒視窗][image-data-factory-getstarted-new-data-factory-blade]
    2. 按一下 [ **資源群組名稱** 並執行下列動作:
        1. 按一下 [ **建立新的資源群組**。
        2. 在 **建立資源群組** 刀鋒視窗中，輸入 **ADFTutorialResourceGroup** 的 **名稱** 資源群組，然後按一下 **確定**。 

            ![建立資源群組][image-data-factory-create-resource-group]

        在本教學課程步驟假設您使用的名稱: **ADFTutorialResourceGroup** 資源群組。 若要深入了解資源群組，請參閱 [使用資源群組來管理您的 Azure 資源](resource-group-overview.md)。  
7. 在 **新增 data factory** 刀鋒視窗中，請注意， **新增至開始面板** 已選取。
8. 按一下 [ **建立** 中 **新增 data factory** 刀鋒視窗。

    Azure Data Factory 的名稱在全域必須是唯一的。 如果您收到錯誤: **Data factory 名稱"ADFTutorialDataFactory"沒有**, 、 變更資料處理站 (例如 yournameADFTutorialDataFactory) 的名稱，然後嘗試重新建立。 請參閱 [Data Factory-命名規則](data-factory-naming-rules.md) Data Factory 成品的命名規則的主題。  
     
    ![Data Factory 名稱無法使用][image-data-factory-name-not-available]
    
    > [AZURE.NOTE] Data factory 的名稱可能會註冊為在未來，因此也會公開可見的 DNS 名稱。  

9. 按一下 [ **通知** 左側和從建立程序尋找通知中樞。 按一下 [ **X** 關閉 **通知** 分頁已開啟。 
10. 建立完成之後，您會看到 **DATA FACTORY** 分頁，如下所示。

    ![Data Factory 首頁][image-data-factory-get-stated-factory-home-page]

## <a name="CreateLinkedServices"></a>步驟 2：建立連結服務
連結服務會將資料存放區或計算服務連結至 Azure Data Factory。 資料存放區可以是 Azure 儲存體、Azure SQL Database 或在內部部署 SQL Server 資料庫。

在此步驟中，您將建立兩個連結的服務: **StorageLinkedService** 和 **AzureSqlLinkedService**。 StorageLinkedService 連結服務會連結 Azure 儲存體帳戶，而 AzureSqlLinkedService 會連結至 Azure SQL database **ADFTutorialDataFactory**。 您稍後將在本教學課程中建立管線，以將資料從 StorageLinkedService 中的 Blob 容器複製到 AzureSqlLinkedService 中的 SQL 資料表。

### 建立 Azure 儲存體帳戶的連結服務
1.  在 **DATA FACTORY** 刀鋒視窗中，按一下 [ **作者和部署** ] 磚來啟動 **編輯器** 的 data factory。

    ![[製作和部署] 磚][image-author-deploy-tile] 

    請參閱 [Data Factory 編輯器][data-factory-editor] 主題，以取得 Data Factory 編輯器的詳細概觀。 
     
5. 在 **編輯器**, ，按一下 [ **新增資料存放區** 工具列，然後選取按鈕 **Azure 儲存體** 從下拉式功能表。 在右窗格中，您應該會看到用來建立 Azure 儲存體連結服務的 JSON 範本。 

    ![編輯器 [新增資料存放區] 按鈕][image-editor-newdatastore-button]
    
6. 取代 **accountname** 和 **accountkey** 帳戶名稱與儲存體帳戶的帳戶金鑰值。 

    ![編輯器 Blob 儲存體 JSON][image-editor-blob-storage-json]    
    
    請參閱 [JSON 指令碼參考](http://go.microsoft.com/fwlink/?LinkId=516971) 如需 JSON 屬性的詳細資訊。

6. 按一下 [ **部署** 部署 StorageLinkedService 工具列上。 確認您看到訊息 **服務已成功建立連結** 標題列上。

    ![編輯器 Blob 儲存體部署][image-editor-blob-storage-deploy]

### 建立 Azure SQL Database 的連結服務
1. 在 **Data Factory 編輯器** , ，按一下 [ **新增資料存放區** 工具列，然後選取按鈕 **Azure SQL database** 從下拉式功能表。 在右窗格中，您應該會看到用來建立 Azure SQL 連結服務的 JSON 範本。

    ![編輯器 Azure SQL 設定][image-editor-azure-sql-settings]

2. 取代 **servername**, ，**databasename**, ，**username@servername**, ，和 **密碼** 取代為您 Azure SQL 伺服器、 資料庫、 使用者帳戶和密碼的名稱。 
3. 按一下 [ **部署** 工具列建立並部署 AzureSqlLinkedService。 
   

## <a name="CreateInputAndOutputDataSets"></a>步驟 3：建立輸入和輸出資料表
在上一個步驟中，您已建立連結的服務 **StorageLinkedService** 和 **AzureSqlLinkedService** ，將 Azure 儲存體帳戶和 Azure SQL database 連結至 data factory: **ADFTutorialDataFactory**。 在此步驟中，您將定義兩個 data factory 資料表: **EmpTableFromBlob** 和 **EmpSQLTable** -代表分別 StorageLinkedService 和 AzureSqlLinkedService 所參考的資料存放區中的輸入/輸出資料。 針對 EmpTableFromBlob 指定所含 blob 具有來源資料，而針對 EmpSQLTable 的 blob 容器，您會指定儲存輸出資料的 SQL 資料表。 

### 建立輸入資料表 
資料表是矩形的資料集，並具有的結構描述。 在此步驟中，您將建立名為的資料表 **Storagelinkedservice** ，它會指向所代表的 Azure 儲存體中的 blob 容器 **StorageLinkedService** 連結服務。

1. 在 **編輯器** Data Factory 中，按一下 [ **新的資料集** 按鈕的工具列，並按一下 **Blob 資料表** 從下拉式功能表。 
2. 將右窗格中的 JSON 替換為以下 JSON 片段： 

        {
          "name": "EmpTableFromBlob",
          "properties": {
            "structure": [
              {
                "name": "FirstName",
                "type": "String"
              },
              {
                "name": "LastName",
                "type": "String"
              }
            ],
            "type": "AzureBlob",
            "linkedServiceName": "StorageLinkedService",
            "typeProperties": {
              "folderPath": "adftutorial/",
              "fileName": "emp.txt",
              "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
              }
            },
            "external": true,
            "availability": {
              "frequency": "Hour",
              "interval": 1
            }
          }
        }

        
     請注意： 
    
    - 資料集 **類型** 設為 **AzureBlob**。
    - **linkedServiceName** 設為 **StorageLinkedService**。 您已在步驟 2 中建立此連結服務。
    - **folderPath** 設為 **adftutorial** 容器。 您也可以指定資料夾內的 Blob 的名稱。 由於您未指定 Blob 的名稱，容器中所有 Blob 的資料都會被視為輸入資料。  
    - 格式 **類型** 設為 **TextFormat**
    - 有兩個欄位中的文字檔案 – **FirstName** 和 **LastName** – 以逗號字元分隔 (**columnDelimiter**) 
    -  **可用性** 設為 **每小時** (**頻率** 設為 **小時** 和 **間隔** 設為 **1** )，因此 Data Factory 服務會尋找輸入資料每隔一小時中的根資料夾中的 blob 容器 (**adftutorial**) 在指定。 
    

    if you don't specify a **fileName** for an **input** **table**, all files/blobs from the input folder (**folderPath**) are considered as inputs. If you specify a fileName in the JSON, only the specified file/blob is considered asn input. See the sample files in the [tutorial][adf-tutorial] for examples.
 
    If you do not specify a **fileName** for an **output table**, the generated files in the **folderPath** are named in the following format: Data.&lt;Guid\&gt;.txt (example: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.).

    To set **folderPath** and **fileName** dynamically based on the **SliceStart** time, use the **partitionedBy** property. In the following example, folderPath uses Year, Month, and Day from from the SliceStart (start time of the slice being processed) and fileName uses Hour from the SliceStart. For example, if a slice is being produced for 2014-10-20T08:00:00, the folderName is set to wikidatagateway/wikisampledataout/2014/10/20 and the fileName is set to 08.csv. 

        "folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
        "fileName": "{Hour}.csv",
        "partitionedBy": 
        [
            { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
            { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } }, 
            { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } }, 
            { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } } 
        ],

    See [JSON Scripting Reference](http://go.microsoft.com/fwlink/?LinkId=516971) for details about JSON properties.

2. 按一下 [ **部署** 建立和部署] 工具列上 **EmpTableFromBlob** 資料表。 確認您看到 **已成功建立資料表** 編輯器的標題列上的訊息。

### 建立輸出資料表
在這部分的步驟中，您將建立輸出資料表名為 **EmpSQLTable** 指向 SQL 資料表中 Azure SQL 資料庫，由 **AzureSqlLinkedService** 連結服務。 

1. 在 **編輯器** Data Factory 中，按一下 [ **新的資料集** 按鈕的工具列，並按一下 **Azure SQL 資料表** 從下拉式功能表。 
2. 將右窗格中的 JSON 替換為以下 JSON 片段：

        {
          "name": "EmpSQLTable",
          "properties": {
            "structure": [
              {
                "name": "FirstName",
                "type": "String"
              },
              {
                "name": "LastName",
                "type": "String"
              }
            ],
            "type": "AzureSqlTable",
            "linkedServiceName": "AzureSqlLinkedService",
            "typeProperties": {
              "tableName": "emp"
            },
            "availability": {
              "frequency": "Hour",
              "interval": 1
            }
          }
        }

        
     請注意： 
    
    * 資料集 **類型** 設為 **AzureSQLTable**。
    * **linkedServiceName** 設為 **AzureSqlLinkedService** (您已在步驟 2 中建立此連結的服務)。
    * **tablename** 設為 **emp**。
    * 有三個資料行 – **識別碼**, ，**FirstName**, ，和 **LastName** – 的 emp 資料表中的資料庫，但 ID 是身分識別資料行，因此您只需要指定 **FirstName** 和 **LastName** 這裡。
    *  **可用性** 設為 **每小時** (**頻率** 設 **小時** 和 **間隔** 設 **1**)。  Data Factory 服務會產生輸出資料配量中的每小時 **emp** Azure SQL 資料庫資料表中的。


3. 按一下 [ **部署** 建立和部署] 工具列上 **EmpSQLTable** 資料表。


## <a name="CreateAndRunAPipeline"></a>步驟 4：建立和執行管線
在此步驟中，您會建立管線，其中含有 **複製活動** 使用 **EmpTableFromBlob** 做為輸入和 **EmpSQLTable** 做為輸出。

1. 在 **編輯器** Data Factory 中，按一下 [ **新增管線** 工具列上的按鈕。 如果沒看到此按鈕，請按一下工具列 **...(省略符號)**。 或者，您可以以滑鼠右鍵按一下 **管線** 樹狀檢視中按一下 **新增管線**。

    ![編輯器 [新增管線] 按鈕][image-editor-newpipeline-button]
 
2. 將右窗格中的 JSON 替換為以下 JSON 片段： 
        
        {
          "name": "ADFTutorialPipeline",
          "properties": {
            "description": "Copy data from a blob to Azure SQL table",
            "activities": [
              {
                "name": "CopyFromBlobToSQL",
                "description": "Push Regional Effectiveness Campaign data to Azure SQL database",
                "type": "Copy",
                "inputs": [
                  {
                    "name": "EmpTableFromBlob"
                  }
                ],
                "outputs": [
                  {
                    "name": "EmpSQLTable"
                  }
                ],
                "typeProperties": {
                  "source": {
                    "type": "BlobSource"
                  },
                  "sink": {
                    "type": "SqlSink",
                    "writeBatchSize": 10000,
                    "writeBatchTimeout": "60:00:00"
                  }
                },
                "Policy": {
                  "concurrency": 1,
                  "executionPriorityOrder": "NewestFirst",
                  "retry": 0,
                  "timeout": "01:00:00"
                }
              }
            ],
            "start": "2015-07-12T00:00:00Z",
            "end": "2015-07-13T00:00:00Z"
          }
        } 

    請注意：

    - 在活動區段中，會有一個活動的 **類型** 設為 **CopyActivity**。
    - 輸入的活動設定成 **EmpTableFromBlob** 的活動設定成輸出 **EmpSQLTable**。
    - 在 **轉換** ] 區段中， **BlobSource** 指定為來源類型和 **SqlSink** 指定為接收類型。

    值取代 **啟動** 屬性目前的日期和 **結束** 值取代為隔天的日期。 在日期時間中，您只指定日期部分，並略過時間部分。 例如，"2015-02-03"，這相當於 "2015-02-03T00:00:00Z"
    
    開始和結束日期時間必須是 [ISO 格式](http://en.wikipedia.org/wiki/ISO_8601)。 例如：2014-10-14T16:32:41Z。  **結束** 時間為選擇性，但我們將在本教學課程中使用它。 
    
    如果您未指定值 **結束** 屬性，它會計算為"**start + 48 小時**」。 若要無限期地執行管線，請指定 **9999-09-09** 做為值 **結束** 屬性。
    
    在上述範例中，由於每小時即產生一個資料配量，共會有 24 個資料配量。
    
    請參閱 [JSON 指令碼參考](http://go.microsoft.com/fwlink/?LinkId=516971) 如需 JSON 屬性的詳細資訊。

4. 按一下 [ **部署** 建立和部署] 工具列上 **ADFTutorialPipeline**。 確認您看到 **已成功建立管線** 訊息。
5. 現在，關閉 **編輯器** 刀鋒視窗中，依序按一下 **X**。 按一下 [ **X** ] 以關閉 [ADFTutorialDataFactory] 刀鋒視窗的工具列和樹狀檢視。 如果您看到 **將捨棄未儲存的編輯** 訊息中，按一下 **確定**。
6. 您應該會回到 **DATA FACTORY** 分頁 **ADFTutorialDataFactory**。

**恭喜您!**您已成功建立 Azure Data Factory、連結服務、資料表和管線，以及排定的管線。   
 
### 在圖表檢視中檢視 Data Factory 
1. 在 **DATA FACTORY** 刀鋒視窗中，按一下 [ **圖表**。

    ![Data Factory 刀鋒視窗：圖表磚][image-datafactoryblade-diagramtile]

2. 您應該會看到如下所示的圖表： 

    ![圖表檢視][image-data-factory-get-started-diagram-blade]

    您可以將管線和資料表放大、縮小、放大到 100%、縮放至適當比例和自動定位，以及顯示歷程資訊 (反白顯示所選取項目的上游和下游項目)。  您可以按兩下物件 (輸入/輸出資料表或管線) 查看其屬性。 
3. 以滑鼠右鍵按一下 **ADFTutorialPipeline** 圖表檢視中按一下 **開啟管線**。 您應該會看到管線中的活動，以及活動的輸入和輸出資料集。 在本教學課程中，管線中只能有一個活動 (複製活動) 將 EmpTableBlob 做為輸入資料集，並將 EmpSQLTable 做為輸出資料集。   

    ![開啟管線](./media/data-factory-get-started-using-editor/DiagramView-OpenPipeline.png)

4. 按一下 [ **Data factory** 左上角，回到圖表檢視中的階層。 圖表檢視會顯示所有管線。 在此範例中，您只建立了一個管線。   
 

## <a name="MonitorDataSetsAndPipeline"></a>步驟 5: 監視資料集和管線
在此步驟中，您將使用 Azure 傳統入口網站來監視 Azure Data Factory 的運作情形。 您也可以使用 PowerShell Cmdlet 來監視資料集和管線。 如需關於使用 cmdlet 進行監視的詳細資訊，請參閱 [監視和管理 Data Factory 使用 PowerShell Cmdlet][monitor-manage-using-powershell]。

1. 瀏覽至 [Azure 傳統入口網站 (預覽)][azure-portal] 如果您沒有開啟它。 
2. 如果分頁 **ADFTutorialDataFactory** 尚未開啟，按一下以開啟 **ADFTutorialDataFactory** 上 **儀表板**。 
3. 您應該會看到您在此分頁上建立之資料表和管線的計數和名稱。

    ![含名稱的首頁][image-data-factory-get-started-home-page-pipeline-tables]

4. 現在，按一下 [ **資料集** 並排顯示。
5. 在 **資料集** 刀鋒視窗中，按一下 [ **EmpTableFromBlob**。 這是輸入的資料表 **ADFTutorialPipeline**。

    ![已選取 EmpTableFromBlob 的資料集][image-data-factory-get-started-datasets-emptable-selected]   
5. 請注意，截至目前為止的資料配量都已產生，而且它們是 **準備** 因為 **emp.txt** 檔案存在於 blob 容器的所有時間: **adftutorial\input**。 確認任何配量顯示在 **最近失敗的配量** 底部區段。

    同時 **最近更新的配量** 和 **最近失敗的配量** 清單都依照 **上次更新時間**。 在下列情況中，配量的更新時間會變更。 
    

    -  You update the status of the slice manually, for example, by using the **Set-AzureRmDataFactorySliceStatus** (or) by clicking **RUN** on the **SLICE** blade for the slice.
    -  The slice changes status due to an execution (e.g. a run started, a run ended and failed, a run ended and succeeded, etc).
 
    Click on the title of the lists or **... (ellipses)** to see the larger list of slices. Click **Filter** on the toolbar to filter the slices.  
    
    To view the data slices sorted by the slice start/end times instead, click **Data slices (by slice time)** tile.   

    ![Data Slices by Slice Time][DataSlicesBySliceTime]   

6. 現在，在 **資料集** 刀鋒視窗中，按一下 [ **EmpSQLTable**。 這是輸出資料表 **ADFTutorialPipeline**。

    ![資料集刀鋒視窗][image-data-factory-get-started-datasets-blade]



     
6. 您應該會看到 **EmpSQLTable** 分頁，如下所示:

    ![資料表刀鋒視窗][image-data-factory-get-started-table-blade]
 
7. 請注意，截至目前為止的資料配量都已產生，而且它們是 **準備**。 在顯示任何配量 **問題配量** 底部區段。
8. 按一下 [ **...(省略符號)** 若要查看所有的配量。

    ![資料配量刀鋒視窗][image-data-factory-get-started-dataslices-blade]

9. 按一下從清單中的任何資料配量，您應該會看到 **資料配量** 刀鋒視窗。

    ![資料配量刀鋒視窗][image-data-factory-get-started-dataslice-blade]
  
    如果配量不是處於 **準備** 狀態，您可以看到未就緒且阻礙目前配量中執行的上游配量 **未就緒的上游配量** 清單。 

11. 在 **資料配量** 刀鋒視窗中，您應該會看到底部清單中的所有活動都執行。 按一下 [ **活動執行** 查看 **活動執行詳細資料** 刀鋒視窗。 

    ![活動執行詳細資料][image-data-factory-get-started-activity-run-details]

    
12. 按一下 [ **X** 關閉所有刀鋒視窗，直到您回到刀鋒 **ADFTutorialDataFactory**。
14. (選擇性)按一下 [ **管線** 首頁上 **ADFTutorialDataFactory**, ，按一下 [ **ADFTutorialPipeline** 中 **管線** 刀鋒視窗中，然後深入檢視輸入資料表 (**已使用**) 或輸出資料表 (**產生**)。
15. 啟動 **SQL Server Management Studio**, 、 連接到 Azure SQL 資料庫，並確認資料列已插入 **emp** 資料庫資料表中的。

    ![SQL 查詢結果][image-data-factory-get-started-sql-query-results]


## 摘要 
在本教學課程中，您已建立要將資料從 Azure Blob 複製到 Azure SQL 資料庫的 Azure Data Factory。 您已使用 Azure 入口網站建立 Data Factory、連結服務、資料表和管線。 以下是您在本教學課程中執行的高階步驟：  

1.  建立 Azure **資料 factory**。
2.  建立 **連結服務** 資料存放區和計算 (稱為 **連結服務**) 至 data factory。
3.  建立 **資料表** 描述輸入資料和輸出資料的管線。
4.  建立 **管線**。 管線由一或多個活動所組成，會處理輸入並產生輸出。 藉由設定管線的作用期間 **啟動** 時間和 **結束** 管線階段。 作用中期間會定義將要產生資料配量的持續時間。 


如需支援活動的清單，請參閱 [管線和活動][msdn-activities] 
主題，以及支援連結服務的清單，請參閱 [連結服務][msdn-linkedservices] 
MSDN Library 上的主題。
 
若要執行本教學課程中使用 Azure PowerShell，請參閱 [建立和監視 data factory 使用 Azure PowerShell][monitor-manage-using-powershell]。  


<!--Link references-->
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[msdn-activities]: https://msdn.microsoft.com/library/dn834988.aspx
[msdn-linkedservices]: https://msdn.microsoft.com/library/dn834986.aspx
[data-factory-naming-rules]: https://msdn.microsoft.com/library/azure/dn835027.aspx

[azure-portal]: https://portal.azure.com/
[download-azure-powershell]: http://azure.microsoft.com/documentation/articles/install-configure-powershell
[sql-management-studio]: http://azure.microsoft.com/documentation/articles/sql-database-manage-azure-ssms/#Step2
[sql-cmd-exe]: https://msdn.microsoft.com/library/azure/ee336280.aspx

[data-factory-editor]: data-factory-editor.md
[monitor-manage-using-powershell]: data-factory-monitor-manage-using-powershell.md
[adf-tutorial]: data-factory-tutorial.md
[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[data-factory-introduction]: data-factory-introduction.md
[data-factory-create-storage]: http://azure.microsoft.com/documentation/articles/storage-create-storage-account/#create-a-storage-account
[data-factory-create-sql-database]: ../sql-database-create-configure.md


[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456

<!--Image references-->

[DataSlicesBySliceTime]: ./media/data-factory-get-started-using-editor/DataSlicesBySliceTime.png

[image-data-factory-getstarted-new-everything]: ./media/data-factory-get-started-using-editor/GetStarted-New-Everything.png

[image-data-factory-gallery-storagecachebackup]: ./media/data-factory-get-started-using-editor/getstarted-gallery-datastoragecachebackup.png

[image-data-factory-gallery-storagecachebackup-seeall]: ./media/data-factory-get-started-using-editor/getstarted-gallery-datastoragecachebackup-seeall.png

[image-data-factory-getstarted-data-services-data-factory-selected]: ./media/data-factory-get-started-using-editor/getstarted-data-services-data-factory-selected.png

[image-data-factory-getstarted-data-factory-create-button]: ./media/data-factory-get-started-using-editor/getstarted-data-factory-create-button.png

[image-data-factory-getstarted-new-data-factory-blade]: ./media/data-factory-get-started-using-editor/getstarted-new-data-factory.png

[image-data-factory-get-stated-factory-home-page]: ./media/data-factory-get-started-using-editor/getstarted-data-factory-home-page.png

[image-author-deploy-tile]: ./media/data-factory-get-started-using-editor/getstarted-author-deploy-tile.png

[image-editor-newdatastore-button]: ./media/data-factory-get-started-using-editor/getstarted-editor-newdatastore-button.png

[image-editor-blob-storage-json]: ./media/data-factory-get-started-using-editor/getstarted-editor-blob-storage-json.png

[image-editor-blob-storage-deploy]: ./media/data-factory-get-started-using-editor/getstarted-editor-blob-storage-deploy.png

[image-editor-azure-sql-settings]: ./media/data-factory-get-started-using-editor/getstarted-editor-azure-sql-settings.png

[image-editor-newpipeline-button]: ./media/data-factory-get-started-using-editor/getstarted-editor-newpipeline-button.png

[image-datafactoryblade-diagramtile]: ./media/data-factory-get-started-using-editor/getstarted-datafactoryblade-diagramtile.png


[image-data-factory-get-started-startboard]: ./media/data-factory-get-started-using-editor/getstarted-data-factory-startboard.png

[image-data-factory-get-started-linked-services-link]: ./media/data-factory-get-started-using-editor/getstarted-data-factory-linked-services-link.png

[image-data-factory-get-started-linked-services-add-store-button]: ./media/data-factory-get-started-using-editor/getstarted-linked-services-add-store-button.png

[image-data-factory-linked-services-get-started-new-data-store]: ./media/data-factory-get-started-using-editor/getstarted-linked-services-new-data-store.png

[image-data-factory-get-started-new-data-store-with-storage]: ./media/data-factory-get-started-using-editor/getstarted-linked-services-new-data-store-with-storage.png

[image-data-factory-get-started-storage-account-name-key]: ./media/data-factory-get-started-using-editor/getstarted-storage-account-name-key.png

[image-data-factory-get-started-linked-services-list-with-myblobstore]: ./media/data-factory-get-started-using-editor/getstarted-linked-services-list-with-myblobstore.png

[image-data-factory-get-started-linked-azure-sql-properties]: ./media/data-factory-get-started-using-editor/getstarted-linked-azure-sql-properties.png

[image-data-factory-get-started-azure-sql-connection-string]: ./media/data-factory-get-started-using-editor/getstarted-azure-sql-connection-string.png

[image-data-factory-get-started-linked-services-list-two-stores]: ./media/data-factory-get-started-using-editor/getstarted-linked-services-list-two-stores.png

[image-data-factory-get-started-storage-explorer]: ./media/data-factory-get-started-using-editor/getstarted-storage-explorer.png

[image-data-factory-get-started-diagram-link]: ./media/data-factory-get-started-using-editor/getstarted-diagram-link.png

[image-data-factory-get-started-diagram-blade]: ./media/data-factory-get-started-using-editor/getstarted-diagram-blade.png

[image-data-factory-get-started-home-page-pipeline-tables]: ./media/data-factory-get-started-using-editor/getstarted-datafactory-home-page-pipeline-tables.png

[image-data-factory-get-started-datasets-blade]: ./media/data-factory-get-started-using-editor/getstarted-datasets-blade.png

[image-data-factory-get-started-table-blade]: ./media/data-factory-get-started-using-editor/getstarted-table-blade.png

[image-data-factory-get-started-dataslices-blade]: ./media/data-factory-get-started-using-editor/getstarted-dataslices-blade.png

[image-data-factory-get-started-dataslice-blade]: ./media/data-factory-get-started-using-editor/getstarted-dataslice-blade.png

[image-data-factory-get-started-sql-query-results]: ./media/data-factory-get-started-using-editor/getstarted-sql-query-results.png

[image-data-factory-get-started-datasets-emptable-selected]: ./media/data-factory-get-started-using-editor/DataSetsWithEmpTableFromBlobSelected.png

[image-data-factory-get-started-activity-run-details]: ./media/data-factory-get-started-using-editor/ActivityRunDetails.png

[image-data-factory-create-resource-group]: ./media/data-factory-get-started-using-editor/CreateNewResourceGroup.png

[image-data-factory-database-connection-string]: ./media/data-factory-get-started-using-editor/DatabaseConnectionString.png

[image-data-factory-new-datafactory-menu]: ./media/data-factory-get-started-using-editor/NewDataFactoryMenu.png

[image-data-factory-sql-management-console]: ./media/data-factory-get-started-using-editor/getstarted-azure-sql-management-console.png

[image-data-factory-sql-management-console-2]: ./media/data-factory-get-started-using-editor/getstarted-azure-sql-management-console-2.png

[image-data-factory-name-not-available]: ./media/data-factory-get-started-using-editor/getstarted-data-factory-not-available.png
 
