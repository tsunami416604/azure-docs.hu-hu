<properties 
    pageTitle="Azure Data Factory 編輯器" 
    description="描述 Azure Data Factory 編輯器，它可讓您使用簡易型 Web 使用者介面，建立、編輯和部署連結服務、資料表和管線的 JSON 定義。" 
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
    ms.date="09/22/2015" 
    ms.author="spelluru"/>

# Azure Data Factory 編輯器
Azure Data Factory 編輯器是 Azure 入口網站中的簡易型 Web 編輯器，可讓您建立、編輯和部署所有 Azure Data Factory 實體的 JSON 檔案。 這可讓您使用 Data Factory 服務隨附的 JSON 範本，建立連結服務、資料集和管線。 如果您未使用過 PowerShell，這讓您不需要安裝和熟悉 Azure PowerShell，也能建立 Azure Data Factory。

## 啟動 Data Factory 編輯器
若要啟動 Data Factory 編輯器中，按一下 [ **製作和部署** 磚 **Data Factory** Azure data factory 刀鋒視窗。 

![[製作和部署] 磚][author-and-deploy-tile]

您會看到如下圖所示的 Data Factory 編輯器：
 
![Data Factory 編輯器][data=factory-editor]
 
## 建立新的連結服務、資料集和管線
工具列有四個按鈕可用來建立 Azure Data Factory 實體。
 
- **新增資料存放區** 可建立資料存放區連結的服務。 按一下此按鈕會出現含有下列選項的功能表：[Azure 儲存體]、[Azure SQL Database]、[內部部署 SQL Server 資料庫]。
- **新的計算** 可建立計算連結服務。 按一下此按鈕會出現含有下列選項的功能表：[隨選 HDInsight 叢集]、[HDInsight 叢集]、[AzureML 連結服務]。      
- **新的資料集** ，建立資料集。 按一下此按鈕會出現下列選項：[Blob 資料表、[Azure SQL 資料表]、[內部部署資料表]。  
- **新增管線** 可建立管線。 按一下 [ **...(省略符號)** 如果您沒看到此按鈕在工具列上，工具列。
 
### 建立儲存體連結服務
1. 按一下 [ **新增資料存放區**, ，按一下其中一個功能表中的選項。
 
    ![[新增資料存放區] 功能表][new-data-store-menu] 
2. 您會看到 JSON 範本，讓您建立儲存體連結服務 **編輯器畫布** 右邊。 您也會看到草稿節點之下 **草稿**。 執行下列動作：
    1. 如 **Azure 儲存體**: 取代 **< {1>accountname<1} >** 和 **< accountkey\<2\ >** 與 Azure 儲存體帳戶的名稱和金鑰。
    2. 如 **Azure SQL database**: 取代 **< servername\<2\ >** 取代為您的 Azure SQL server 名稱 **< databasename >** 與資料庫的名稱 **< 使用者名稱 > @< servername\<2\ >** 的使用者名稱和 **< 密碼 >** 取代為使用者帳戶的密碼。 
    3. 如 **內部部署 SQL server 資料庫**: 取代 **< servername\<6\ >** 取代為您的內部部署 SQL server 名稱 **< databasename >** 的資料庫名稱 **< 使用者名稱 >** 的使用者名稱和 **< 密碼 >** 取代為使用者帳戶的密碼。
4. 按一下 [ **部署** 部署連結的服務] 工具列上。 您可以按一下 **捨棄** ，捨棄您建立的 JSON 草稿。
 
    ![[部署] 按鈕][deploy-button]

1. 標題列應該會顯示部署作業的狀態。

    ![部署成功訊息][deploy-success-message]
2. 如果部署作業成功，左側的樹狀檢視中會出現已建立的連結服務。
  
    ![樹狀檢視中的 StorageLinkedService][storagelinkedservice-in-treview]

### 建立計算連結服務
1. 按一下 [ **新增計算** 按一下其中一個功能表中的選項。
 
    ![[新增計算] 功能表][new-compute-menu] 
2. 右側的編輯器畫布會顯示 JSON 範本，讓您建立計算連結服務。 執行下列動作：
    1. 如 **隨 HDInsight 叢集**, ，指定下列屬性的值: 
        1. 如 **clusterSize** 屬性，指定您希望 Data Factory 服務在執行階段建立的 HDInsight 叢集的大小。 
        3. 如 **timeToLive** 屬性，指定允許的閒置時間之前刪除 HDInsight 叢集。 例如：00:05:00 表示叢集閒置 5 分鐘後，就應該刪除叢集。
        4. 如 **版本** 屬性，指定叢集的 HDInsight 版本 (預設: 3.1 版)。
        5. 如 **linkedServiceName** 屬性，指定要與 HDInsight 叢集相關聯的 Azure 儲存體連結服務。 
    6. 如 **hdinsight** (提到-您為自己)，指定下列屬性的值:
        1. 如 **clusterUri** 屬性，指定您自己的 HDInsight 叢集的 URL。 
        2. 如 **userName** 屬性，指定 Data Factory 服務應該用來連接到 HDInsight 叢集的使用者帳戶。 
        3. 如 **密碼** 屬性，指定使用者帳戶的密碼。 
        4. 如 **linkedServiceName** 屬性，指定您的 HDInsight 叢集相關聯的 Azure 儲存體連結服務。
    5. 如 **AzureML 連結服務**, ，指定下列屬性:
        1. 如 **mlEndPoint** 屬性，指定 Azure Machine Learning 批次評分 URL。
        2. 如 **apiKey** 屬性，指定已發行的工作區模型的 API 金鑰。
3. 按一下 [ **部署** 部署連結的服務] 工具列上。

> [AZURE.NOTE] 請參閱 [連結服務][msdn-linkedservices-reference] MSDN Library 中的主題說明的用來定義 Azure Data Factory 的 JSON 元素的連結服務...  

### 建立新的資料集
1. 按一下 [ **新的資料集** 按一下其中一個功能表中的選項。
2. 右側的編輯器畫布會顯示 JSON 範本，讓您建立資料集。 執行下列動作： 
    1. 如 **Blob 資料表**, ，指定下列屬性的值:
    2. 如 **Azure SQL 資料表** 或 **內部資料表**, ，指定下列屬性的值: 
        1. 在 **位置** 區段: 
            2. 如 **linkedServiceName** 屬性，指定服務參考到 Azure SQL/內部部署 SQL Server 資料庫的連結名稱。
            2. 如 **tableName** 屬性，指定資料表的名稱在 Azure SQL Database 執行個體/內部部署 SQL server 中的連結的服務參考到。
        3. 在 **可用性** 區段:
            1. 如 **頻率** 屬性，指定資料配量生產的時間單位。 支援的頻率值：Minute、Hour、Day、Week、Month。
            2. 如 **間隔** 屬性，指定定義頻率內的間隔。 **頻率** 設 **小時** 和 **間隔** 設 **1** 指出應該每小時產生新的資料配量。 
        3. 在 **結構** 區段: 
            1. 指定資料行的名稱和類型，如下列範例所示：
                
                    "structure":
                    [
                        { "name": "FirstName", "type": "String"},
                        { "name": "LastName", "type": "String"}
                    ],
         
> [AZURE.NOTE] 請參閱 [資料表][msdn-tables-reference] MSDN Library 中用來定義 Azure Data Factory 資料表的 JSON 元素的說明主題。  
                   
### 建立及啟動管線 
1. 按一下 [ **新增管線** 工具列上。 如果看不到 **新增管線** 按鈕，再按一下 **...(省略符號)** 若要查看它。   
2. 右側的編輯器畫布會顯示 JSON 範本，讓您建立管線。 執行下列動作： 
    1. 如 **描述** 屬性，指定管線的描述。
    2. 如 **活動** 區段中，將活動新增至管線。 範例：
     
            "activities":   
            [
                {
                    "name": "CopyFromBlobToSQL",
                    "description": "Push Regional Effectiveness Campaign data to Azure SQL",
                    "type": "CopyActivity",
                    "inputs": [ {"name": "EmpTableFromBlob"} ],
                    "outputs": [ {"name": "EmpSQLTable"} ],     
                    "transformation":
                    {
                        "source":
                        {                               
                            "type": "BlobSource"
                        },
                        "sink":
                        {
                            "type": "SqlSink"
                        }   
                    },
                    "Policy":
                    {
                        "concurrency": 1,
                        "executionPriorityOrder": "NewestFirst",
                        "style": "StartOfInterval",
                        "retry": 0,
                        "timeout": "01:00:00"
                    }       
                }
            ]
    3. 如 **啟動** 屬性，指定資料處理開始或資料配量處理的時間。 範例：2014-05-01T00:00:00Z。
    4. 如 **結束** 屬性，指定資料處理結束的時間。 範例：2014-05-01T00:00:00Z。       

> [AZURE.NOTE] 請參閱 [管線和活動][msdn-pipelines-reference] MSDN Library 中用來定義 Azure Data Factory 管線的 JSON 元素的說明主題。

## 將活動定義加入至管線 JSON
可以即可將活動定義加入至管線 JSON **新增活動** 工具列上。 按一下此按鈕時，您可以選擇想要加入至管線的活動類型。  

![[加入活動] 選項][add-activity-options]

如果您想要將資料從 Azure SQL database 複製到 Azure blob 儲存體與 HDInsight 叢集上使用 Pig 指令碼處理 blob 儲存體中的資料，您先新增 **複製活動** ，然後新增 **Pig 活動** 至管線。 這會建立兩個區段活動的管線 JSON 的 [] 一節。 Pig 活動只是 HDInsight 活動再加上 Pig 轉換。 

    "activities": [
        {
            "name": "CopyFromTabletoBlob",
            "type": "CopyActivity",
            ...
        }
        {
            "name": "ProcessBlobDataWithPigScript",
            "type": "HDInsightActivity",
            ...
            "transformation": {
                "type": "Pig",
                ...
            }
        }
    ]

## 啟動管線
您可以在 JSON 中指定 start 和 end 屬性的值，以指定管線的開始日期時間和結束日期時間。 

    {
        "name": "ADFTutorialPipeline",
        "properties":
        {   
            "description" : "Copy data from a blob to Azure SQL table",
            "activities":   
            [
                ...
            ],
    
            "start": "2015-02-13T00:00:00Z",
            "end": "2015-02-14T00:00:00Z",
            "isPaused": false
        }
    } 
  
## 編輯器中的草稿
當您在 Data Factory 中切換環境或瀏覽至不同實體時，草稿可讓您暫時儲存工作。 「草稿」的存留期間與瀏覽器工作階段有關。 如果您關閉瀏覽器或使用另一部電腦，就會失去草稿。

## 捨棄 Data Factory 實體的 JSON 草稿
您可以按一下 [捨棄 Azure Data Factory 實體的 JSON 定義 **捨棄** 工具列上的按鈕。   

## 複製 Data Factory 實體
您可以複製現有的 Azure Data Factory 實體 (連結的服務、 資料表或管線) 的樹狀檢視中選取實體，再按一下 **複製** 工具列上的按鈕。

![複製 Data Factory 實體][clone-datafactory-entity]

您會看到下建立新的草稿 **草稿** 樹狀結構檢視中的節點。 

## 刪除 Data Factory 實體
您可以刪除 Azure Data Factory 實體 (連結的服務、 資料表或管線)，將選取的樹狀檢視中的實體，然後按一下 **刪除** 工具列上 (或) 實體上按一下滑鼠右鍵，然後按一下 **刪除**。

![刪除 Data Factory 實體][delete-datafactory-entity] 

## 另請參閱
請參閱 [開始使用 Azure Data Factory][data-factory-get-started] 主題，以取得使用 Data Factory 編輯器建立 Azure data factory 的逐步指示。 

[msdn-tables-reference]: https://msdn.microsoft.com/library/dn835002.aspx
[msdn-linkedservices-reference]: https://msdn.microsoft.com/library/dn834986.aspx       
[msdn-pipelines-reference]: https://msdn.microsoft.com/library/dn834988.aspx  

[data-factory-get-started]: data-factory-get-started.md

[author-and-deploy-tile]: ./media/data-factory-editor/author-and-deploy-tile.png
[data=factory-editor]: ./media/data-factory-editor/data-factory-editor.png
[new-data-store-menu]: ./media/data-factory-editor/new-data-store-menu.png
[new-compute-menu]: ./media/data-factory-editor/new-compute-menu.png
[deploy-button]: ./media/data-factory-editor/deploy-button.png
[deploy-success-message]: ./media/data-factory-editor/deploy-success-message.png
[storagelinkedservice-in-treview]: ./media/data-factory-editor/storagelinkedservice-in-treeview.png
[delete-datafactory-entity]: ./media/data-factory-editor/delete-datafactory-entity.png
[clone-datafactory-entity]: ./media/data-factory-editor/clone-datafactory-entity.png
[add-activity-options]: ./media/data-factory-editor/add-activity-options.png 
