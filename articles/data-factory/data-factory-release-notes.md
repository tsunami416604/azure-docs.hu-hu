<properties 
    pageTitle="Data Factory - 版本資訊 | Microsoft Azure" 
    description="Data Factory 版本資訊" 
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
    ms.date="10/09/2015" 
    ms.author="spelluru"/>

# Azure Data Factory 版本資訊
請參閱 [Data Factory-.NET API 變更記錄檔](data-factory-api-change-log.md) 文件，以了解 Data Factory.NET SDK 變更特定的版本。  

## Data Factory 07/17/2015 版本的注意事項
將下列 JSON 變更導入 2015 年 7 月版的 Azure PowerShell。 

## 連結的服務、資料表和活動等類型的更新
資源類型 | 在 JSON 中的目前名稱 | 在 JSON 中的新名稱
------------- | -------------------- | ----------------
連結的服務 (資料來源) | AzureSqlLinkedService | AzureSqlDatabase
連結的服務 (資料來源) | AzureStorageLinkedService | AzureStorage
連結的服務 (資料來源) | DocumentDbLinkedService | DocumentDb
連結的服務 (資料來源) | OnPremisesFileSystemLinkedService | OnPremisesFileServer
連結的服務 (資料來源) | OnPremisesOracleLinkedService | OnPremisesOracle
連結的服務 (資料來源) | OnPremisesSqlLinkedService | OnPremisesSqlServer
連結的服務 (資料來源) | OnPremisesMySqlLinkedService | OnPremisesMySql
連結的服務 (資料來源) | OnPremisesDb2LinkedService | OnPremisesDb2
連結的服務 (資料來源) | OnPremisesTeradataLinkedService | OnPremisesTeradata
連結的服務 (資料來源) | OnPremisesSybaseLinkedService | OnPremisesSybase
連結的服務 (資料來源) | OnPremisesPostgreSqlLinkedService | OnPremisesPostgreSql
連結的服務 (計算) | AzureMlLinkedService | AzureML
連結的服務 (計算) | HDInsightBYOCLinkedService | HDInsight
連結的服務 (計算) | HDInsightOnDemandLinkedService | HDInsightOnDemand
連結的服務 (計算) | AzureBatchLinkedService | AzureBatch
Dataset | AzureBlobLocation | AzureBlob
Dataset | AzureTableLocation | AzureTable
Dataset | AzureSqlTableLocation | AzureSqlTable
Dataset | DocumentDbCollectionLocation | DocumentDbCollection 
Dataset | OnPremisesFileSystemLocation | FileShare
Dataset | OnPremisesOracleTableLocation | OracleTable
Dataset | OnPremisesSqlServerTableLocation | SqlServerTable
Dataset | RelationTableLocation | RelationalTable
活動 | CopyActivity | 複製
活動 | HDInsightActivity (Hive 轉換) | HDInsightHive
活動 | HDInsightActivity (Pig 轉換) | HDInsightPig
活動 | HDInsightActivity (MapReduce 轉換) | HDInsightMapReduce
活動 | HDInsightActivity (串流) | HDInsightHadoopStreaming
活動 | AzureMLBatchScoringActivity | AzureMLBatchScoring
活動 | StoredProcedureActivity | SqlServerStoredProcedure

## 新 typeProperties 元素
新 **typeProperties** 元素包含連結服務/資料表/活動的類型特定屬性。 

### 舊的連結服務 JSON
    {
        "name": "StorageLinkedService",
        "properties":
        {
            "type": "AzureStorageLinkedService",
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>" "
        }
    }

### 新的連結服務 JSON
    {
      "name": "StorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<Account key>"
        }
      }
    }

請注意： 

-  **類型** 屬性是移動的一個層級，並且設定為 **AzureStorage** (從變更 **AzureStorageLinkedService** 至 **AzureStorage**) 
- 新 **typeProperties** 包含 Azure 儲存體連結服務支援的屬性項目 (**connectionString** 在此範例中)。  

### 舊資料集 JSON
    {
        "name": "EmpTable",
        "properties":
        {
            "location":
            {
                "type": "AzureTableLocation",
                "tableName": "myazuretable",
                "linkedServiceName": "MyLinkedService"
            },
            "availability":
            {
                "frequency": "Hour",
                "interval": 1
            }
        }
    }

### 新資料集 JSON

    {
        "name": "EmpTable",
        "properties": {
            "type": "AzureTable",
            "linkedServiceName": "MyLinkedServiceName",
            "typeProperties": {
                "tableName": "myazuretable"
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            }
        }
    }

請注意：

-  **類型** 屬性會向上移動一個層級，而型別名稱 **AzureTableLocation** 已變更為 **AzureTable**。
-  **LinkedServiceName** 會向上移動一個層級。 
-  **位置** 移除項目現在和類型特定屬性，例如 **tableName** 中指定 **位置** 區段會指定在新 **typeProperties** 一節。  

### 舊活動 JSON

    {
        "name": "CopyFromSQLtoBlob   ",
        "description": "description", 
        "type": "CopyActivity",
        "inputs":  [ { "name": "InputSqlDA"  } ],
        "outputs":  [ { "name": "OutputBlobDA" } ],
        "transformation":
        {
            "source":
            {
                "type": "SqlSource",
                "sqlReaderQuery": "select * from MyTable"
            },
            "sink":
            {
                "type": "BlobSink"
            }
        }
    }   

### 新活動 JSON
    
    {
        "name": "CopyFromSQLtoBlob   ",
        "description": "description", 
        "type": "Copy",
        "inputs":  [ { "name": "InputSqlDA"  } ],
        "outputs":  [ { "name": "OutputBlobDA" } ],
        "typeProperties":
        {
            "source":
            {
                "type": "SqlSource",
                "sqlReaderQuery": "select * from MyTable"
            },
            "sink":
            {
                "type": "BlobSink"
            }
        }
    }

請注意：

- 請注意， **轉換** 元素已取代為新 **typeProperties** 項目。

## waitOnExternal 元素已移除
 **WaitOnExternal** 項目會取代為新 **外部** 和 **externalData** 屬性。        

### 舊 JSON
    {
        "name": "EmpTableFromBlob",
        "properties":
        {
            "location": 
            {
                "type": "AzureBlobLocation",
                "folderPath": "adftutorial/",
                "format":
                {
                    "type": "TextFormat",
                    "columnDelimiter": ","
                },
                "linkedServiceName": "StorageLinkedService"
            },
            "availability": 
            {
                "frequency": "hour",
                "interval": 1,
                "waitOnExternal": 
                {
                    "retryInterval": "00:01:00",
                    "retryTimeout": "00:10:00",
                    "maximumRetry": "3"         
                }
            }
        }
    } 

### 新 JSON
    {
      "name": "EmpTableFromBlob",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "folderPath": "adftutorial/",
          "format": {
            "type": "TextFormat",
            "columnDelimiter": ","
          }
        },
        "external": true,
        "availability": {
          "frequency": "hour",
          "interval": 1
        },
        "policy": {
          "externalData": {
            "retryInterval": "00:01:00",
            "retryTimeout": "00:10:00",
            "maximumRetry": "3"
          }
        }
      }
    }

請注意：

-  **WaitOnExternal** 屬性移除 **可用性** 區段 
- 新 **外部** 屬性向上移動一個層級，而且設定為 **true** 對外部資料表。 
- 內容 **waitOnExternal** 項目，例如 **retryInterval** 會加入至新 **externalData** 一節中 **原則** 項目。
-  **ExternalData** 項目是選擇性項目。 
- 當您使用 **externalData** 項目，您必須具有 **外部** 屬性設定為 **true**。 
 

## BlobSink 的新 copyBehavior 屬性
 **BlobSink** 支援名為的新屬性 ︰ **copyBehavior**。 這個屬性會定義複製行為，當來源 **BlobSource** 或 **FileSystem**。 有三種可能值 **copyBehavior** 屬性。 

**PreserveHierarchy**:: 保留目標資料夾，亦即，來源資料夾的原始程式檔的相對路徑中的檔案階層完全相同的目標資料夾的目標檔案相對路徑。 


**FlattenHierarchy**:: 從來源資料夾的所有檔案會都出現在第一層的目標資料夾。 目標檔案會有自動產生的名稱。 


**MergeFiles**︰ 會合併到一個檔案的來源資料夾的所有檔案。 如果已指定檔案/Blob 名稱，合併檔案名稱會是指定的名稱；否則，就會是自動產生的檔案名稱。 
 
## 所有 HDInsight 活動的新 getDebugInfo 屬性
HDInsight 活動 （Hive、 Pig、 MapReduce、 Hadoop 串流） 支援新的屬性 ︰  **getDebugInfo** 屬性。  **GetDebugInfo** 屬性是選擇性項目。 當設定為 **失敗**, ，下載記錄檔只能在執行失敗。 當設定為 **所有**, ，無論執行狀態為何，一律下載記錄檔。 當設定為 **無**, ，會下載任何記錄檔。

  
     

## Data Factory 2015/04/10 版本的注意事項
您會看到 **最近更新的配量** 和 **最近失敗的配量** 上列出 **資料表** 現在刀鋒視窗。 這些清單會依配量的更新時間排序。 在下列情況中，配量的更新時間會變更。    

-  您的配量狀態以手動方式更新，例如，使用 **組 AzureRmDataFactorySliceStatus** （或） 依序按一下 **執行** 上 **配量** 配量的分頁。
-  配量因為執行而變更狀態 (例如，開始執行、執行結束但失敗、執行結束且成功等)。

按一下清單的標題或 **...（省略符號）** 若要查看更長的配量清單。 按一下 [ **篩選** 篩選配量] 工具列上。
 
您仍然可以檢視配量時間排序的配量，即可 **資料配量 （依配量時間）** 並排顯示。 這些集合中的配量會依配量時間排序。 例如，如果它是每小時的排程，配量會 ︰
- 2015 年 4 月 4 日下午 5 點，在進行中 
- 2015 年 4 月 4 日下午 4 成功
- 2015 年 4 月 4 日下午 3 點失敗

但是，如果重新執行較舊的配量，即使這可能是使用者最感興趣的配量，也不會顯示在此清單頂端。

## Data Factory 2015/3/31 版本的注意事項
- 更新 **資料管理閘道器** 安裝套件已公佈 [Microsoft 下載中心][adf-gateway-download]。
- 複製 **在內部部署檔案系統至 Azure blob** 現在支援。 如需詳細資訊，請參閱下列主題。
    -  [內部部署檔案系統連結服務](https://msdn.microsoft.com/library/dn930836.aspx)
    -  [資料表 JSON 中的 OnPremisesFileSystemLocation 屬性](https://msdn.microsoft.com/library/dn894089.aspx#OnPremFileSystem)
    -  [支援的來源與接收](https://msdn.microsoft.com/library/dn894007.aspx)。 請參閱更新的複製對照表和 **FileSystemSource** 屬性。 
-  複製 **在內部部署 Oracle 資料庫至 Azure blob** 現在支援。 如需詳細資訊，請參閱下列主題。 
    -  [內部部署 Oracle 連結服務](https://msdn.microsoft.com/library/dn948537.aspx)
    -  [資料表 JSON 中的 OnPremisesOracleTableLocation 屬性](https://msdn.microsoft.com/library/dn894089.aspx#Oracle) 
    -  [支援的來源與接收](https://msdn.microsoft.com/library/dn894007.aspx)。 請參閱更新的複製對照表和 **OracleSource** 屬性。
-  您可以對 Azure Blob 中的文字檔案指定編碼。 請參閱新 [encodingName 屬性](https://msdn.microsoft.com/library/dn894089.aspx#AzureBlob)。 
- 複製到 SQL 接收器時，您可以搭配其他參數來叫用預存程序。    

請參閱 blob 文章 ︰ [Azure Data Factory 更新-新的資料存放區](http://azure.microsoft.com/blog/2015/03/30/azure-data-factory-update-new-data-stores/) 如需詳細資訊，包括範例。  

## Data Factory 2015/2/27 版本的注意事項

### 新的改進功能
- **Azure Data Factory 編輯器**。 Data Factory 編輯器是 Azure 入口網站的一部分，可讓您建立、編輯及部署 JSON 檔案，此檔案定義連結服務、資料集和管線。編輯器的主要目標是提供快速又輕巧的使用者介面 (UI)，讓您不需要安裝 Azure PowerShell 或學習使用 PowerShell Cmdlet，即可建立 Azure Data Factory 成品。 請參閱 [Azure Data Factory 編輯器-輕量型 Web 編輯器][adf-editor-blog] 的快速概觀和影片需 Data Factory 編輯器的部落格文章。 如需編輯器的詳細概觀，請參閱 [Data Factory 編輯器][adf-editor] 文件。          

### 變更

## Data Factory 2015/1/26 版本的注意事項 ##

### 變更
- 更新 **資料管理閘道器** 安裝套件已公佈 [Microsoft 下載中心][adf-gateway-download]。 從這個版本開始，您可以在此下載位置找到搭配 Azure Data Factory 使用的最新「資料管理閘道器」。 此安裝套件支援 Azure Data Factory 和 Power BI for Office 365 服務。 如果您同時使用這兩種服務，請注意，Data Factory 和 Power BI 的閘道器必須安裝在不同電腦上，並根據 Data Factory 或 Power BI 文件的指導方針，以不同方式加以設定。
-  **複製活動** 現在支援內部部署 SQL Server 和 Azure SQL database 之間複製資料。 
- **SqlSink** 支援新的屬性 ︰ **WriteBatchTimeout**。 這個屬性可讓您彈性地設定在批次插入作業逾時之前，要等候多久讓作業完成。 若要執行混合式複製 (複製作業涉及內部部署資料來源和雲端資料來源)，您必須有 1.4 版或更新版本的閘道器，才能使用這個屬性。 
- **SQL Server 連結服務** 現在支援 **Windows 驗證**。 
    - 現在使用入口網站建立 SQL Server 連結服務時，可以選擇使用 Windows 驗證，並設定適當的認證。 您需要具有 1.4 版或更新版本的閘道器。 
    - 建立 SQL Server 連結服務時使用 Azure PowerShell，您可以指定連接資訊以純文字或加密連接資訊，使用更新 [新增 AzureRmDataFactoryEncryptValue cmdlet](https://msdn.microsoft.com/library/mt603802.aspx) 中連結的服務 JSON 承載的 Connection String 屬性然後使用加密的字串。 New-AzureRmDataFactoryEncryptValue Cmdlet 尚不支援加密功能。 

## Data Factory 2014/12/11 版本的注意事項 ##

### 新的改進功能

- Azure Machine Learning 整合
    - 這一版的 Azure Data Factory 服務可讓您將 Azure Data Factory 與 Azure Machine Learning (ML) 整合使用 **AzureMLLinkedService** 和 **AzureMLBatchScoringActivity**。 請參閱 [使用 Data Factory 和 Azure Machine Learning 建立預測管線][adf-azure-ml] 如需詳細資訊。 
- 提供閘道器版本狀態
    - 如果有比目前安裝之版本還要新的閘道器，「Azure 入口網站」和 Get-AzureRmDataFactoryGateway Cmdlet 的輸出中就會顯示 "NewVersionAvailable" 狀態。 您可以接著依照入口網站的程序下載新的安裝檔案 (.msi)，並加以執行，安裝最新的閘道器。 需要進行任何額外的設定。

### 變更

- 已移除 HdInsightOnDemandLinkedService 中的 JobsContainer。
    - 在 HDInsightOnDemandLinkedService 的 JSON 定義中，您不需要指定 **jobsContainer** 屬性。 如果您有為隨選連結服務指定的屬性，則會忽略此屬性。 您可以從連結服務的 JSON 定義中移除屬性，然後使用 New-AzureRmDataFactoryLinkedService Cmdlet 來更新連結服務的定義。
- HDInsightOnDemandLinkedService 的選擇性組態參數
    - 此版本針對 HDInsightOnDemandLinked (隨選 HDInsight 叢集) 導入一些選擇性組態參數支援。 請參閱 [ClusterCreateParameters 屬性][on-demand-hdi-parameters] 如需詳細資訊。
- 已移除閘道器位置
    - 透過入口網站或 PowerShell (New-AzureRmDataFactoryGateway) 建立 Azure Data Factory 閘道器時，您不再需要指定閘道器的位置。 將會繼承 Data Factory 區域。 同樣地，若要使用 JSON 來設定 SQL Server 連結服務，也不再需要 "gatewayLocation" 屬性。 Data Factory .NET SDK 也已更新來反映這些變更。
    - 如果您使用較舊版的 SDK 和 Azure PowerShell，則仍然必須提供位置設定。
 
     

#### 重大變更
    
- CustomActivity 變更為 DotNetActivity
    - **ICustomActivity** 介面已重新命名為 **IDotNetActivity**。 您將必須更新 Data Factory NuGet 封裝，將您自訂活動之原始程式碼中的 ICustomActivity 變更為 IDotNetActivity。  
    - 在您自訂活動的 JSON 定義中的自訂活動的型別必須從變更 **CustomActivity** 至 **DotNetActivity**。 
    -  **CustomActivity** 和 **CustomActivityProperties** 類別已重新命名為 **DotNetActivity** 和 **DotNetActivityProperties** 具有相同的屬性集。

        如果您使用舊版 SDK 和 Azure PowerShell，則可以繼續使用 CustomActivity，而不需改成 DotNetActivity。
    
        請參閱 [Azure Data Factory 管線中使用自訂活動][adf-custom-activities] 有關如何建立自訂活動，並使用 Azure Data Factory 管線中的逐步解說。  

[adf-azure-ml]: data-factory-create-predictive-pipelines.md
[adf-custom-activities]: data-factory-use-custom-activities.md
[adf-editor]: data-factory-editor.md

[adf-editor-video]: http://channel9.msdn.com/Blogs/Windows-Azure/New-Azure-Data-Factory-Editor-UI
[adf-editor-blog]: http://azure.microsoft.com/blog/2015/03/02/azure-data-factory-editor-a-light-weight-web-editor/
[on-demand-hdi-parameters]: http://msdn.microsoft.com/library/microsoft.windowsazure.management.hdinsight.clustercreateparameters_properties.aspx
[adf-gateway-download]: http://www.microsoft.com/download/details.aspx?id=39717
[adf-github-samples]: https://github.com/Azure/Azure-DataFactory/tree/master/Samples/JSON
[adf-msdn-linked-services]: https://msdn.microsoft.com/library/dn834986.aspx
[adf-encrypt-value-cmdlet]: https://msdn.microsoft.com/library/dn834940.aspx



 


