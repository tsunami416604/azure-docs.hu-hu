<properties 
    pageTitle="從 Oracle 移動資料 | Azure Data Factory" 
    description="了解如何使用 Azure Data Factory 從內部部署 Oracle 資料庫來回移動資料。" 
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

# 使用 Azure Data Factory 從內部部署的 Oracle 移動資料 

本文概述如何使用資料處理站複製活動將資料從 Oracle 移動到另一個資料存放區。 這篇文章是根據 [資料移動活動](data-factory-data-movement-activities.md) 一文，該文呈現使用複製活動，而且支援的資料存放區組合的資料移動的一般概觀。

## 安裝 
如果是能夠連接到您內部部署 Oracle 資料庫的 Azure Data Factory 服務，您就必須安裝下列項目： 

- 位於裝載資料庫的同一部電腦上或個別電腦上的資料管理閘道，可避免與資料庫競用資源。 資料管理閘道是一套透過安全且可管理的方式，將內部部署資料來源連結至雲端服務的軟體。 請參閱 [內部和雲端之間移動資料](data-factory-move-data-between-onprem-and-cloud.md) 文件的詳細資料管理閘道器。 
- [Oracle 資料存取元件 (ODAC) 適用於 Windows](http://www.oracle.com/technetwork/topics/dotnet/downloads/index.html)。 它必須安裝於安裝閘道的主機電腦上。


## 範例：從 Oracle 複製資料到 Azure Blob

下列範例顯示：

1.  連結的服務型別的 [OnPremisesOracle](data-factory-onprem-oracle-connector.md#oracle-linked-service-properties)。
2.  連結的服務型別的 [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties)。
3.  輸入 [資料集](data-factory-create-datasets.md) 型別的 [OracleTable](data-factory-onprem-oracle-connector.md#oracle-dataset-type-properties)。 
4.  輸出 [資料集](data-factory-create-datasets.md) 型別的 [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties)。
5.  A [管線](data-factory-create-pipelines.md) 具有複製活動使用 [OracleSource](data-factory-onprem-oracle-connector.md#oracle-copy-activity-type-properties) 做為來源和 [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties) 做為接收。

此範例會每小時將資料從內部部署 Oracle 資料庫中的資料表移動到 Blob。 如需下列範例中所使用之各種屬性的詳細資訊，請參閱範例後章節中不同屬性的文件。

**Oracle 連結服務：**

    {
      "name": "OnPremisesOracleLinkedService",
      "properties": {
        "type": "OnPremisesOracle",
        "typeProperties": {
          "ConnectionString": "data source=<data source>;User Id=<User Id>;Password=<Password>;",
          "gatewayName": "<gateway name>"
        }
      }
    }

**Azure Blob 儲存體連結服務：**

    {
      "name": "StorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<Account key>"
        }
      }
    }

**Oracle 輸入資料集：**

此範例假設您已在 Oracle 中建立資料表 "MyTable"，其中包含時間序列資料的資料行 (名稱為 "timestampcolumn")。 

設定 “external”: ”true” 和指定 externalData 原則即可通知 Azure Data Factory：這是 Data Factory 外部的資料表而且不是由 Data Factory 中的活動所產生。

    {
        "name": "OracleInput",
        "properties": {
            "type": "OracleTable",
            "linkedServiceName": "OnPremisesOracleLinkedService",
            "typeProperties": {
                "tableName": "MyTable"
            },
               "external": true,
            "availability": {
                "offset": "01:00:00",
                "interval": "1",
                "anchorDateTime": "2014-02-27T12:00:00",
                "frequency": "Hour"
            },
          "policy": {     
               "externalData": {        
                    "retryInterval": "00:01:00",    
                    "retryTimeout": "00:10:00",       
                    "maximumRetry": 3       
                }     
              }
        }
    }


**Azure Blob 輸出資料集：**

資料會每小時寫入至新的 Blob (頻率：小時，間隔：1)。 根據正在處理之配量的開始時間，以動態方式評估 Blob 的資料夾路徑和檔案名稱。 資料夾路徑會使用開始時間的年、月、日和小時部分。
    
    {
      "name": "AzureBlobOutput",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
          "partitionedBy": [
            {
              "name": "Year",
              "value": {
                "type": "DateTime",
                "date": "SliceStart",
                "format": "yyyy"
              }
            },
            {
              "name": "Month",
              "value": {
                "type": "DateTime",
                "date": "SliceStart",
                "format": "%M"
              }
            },
            {
              "name": "Day",
              "value": {
                "type": "DateTime",
                "date": "SliceStart",
                "format": "%d"
              }
            },
            {
              "name": "Hour",
              "value": {
                "type": "DateTime",
                "date": "SliceStart",
                "format": "%H"
              }
            }
          ],
          "format": {
            "type": "TextFormat",
            "columnDelimiter": "\t",
            "rowDelimiter": "\n"
          }
        },
        "availability": {
          "frequency": "Hour",
          "interval": 1
        }
      }
    }


**具有複製活動的管線：**

此管線包含複製活動，該活動已設定為使用上述輸入和輸出資料集並排定為每小時執行。 在管線 JSON 定義 **來源** 類型設為 **RelationalSource** 和 **接收器** 類型設為 **BlobSink**。  使用指定的 SQL 查詢 **oracleReaderQuery** 屬性選取過去一小時內要複製的資料。

    
    {  
        "name":"SamplePipeline",
        "properties":{  
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline for copy activity",
        "activities":[  
          {
            "name": "AzureSQLtoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [
              {
                "name": " OracleInput"
              }
            ],
            "outputs": [
              {
                "name": "AzureBlobOutput"
              }
            ],
            "typeProperties": {
              "source": {
                "type": "OracleSource",
                "oracleReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
              },
              "sink": {
                "type": "BlobSink"
              }
            },
           "scheduler": {
              "frequency": "Hour",
              "interval": 1
            },
            "policy": {
              "concurrency": 1,
              "executionPriorityOrder": "OldestFirst",
              "retry": 0,
              "timeout": "01:00:00"
            }
          }
         ]
       }
    }

## Oracle 連結服務屬性

下表提供 Oracle 連結服務專屬 JSON 元素的描述。 

屬性 | 說明 | 必要
-------- | ----------- | --------
類型 | Type 屬性必須設定為 ︰ **OnPremisesOracle** | 是
connectionString | 針對 connectionString 屬性指定連接到 Oracle 資料庫執行個體所需的資訊。 | 是 
gatewayName | 將用來連接到內部部署 Oracle 伺服器的閘道器名稱 | 是

請參閱 [設定認證和安全性](data-factory-move-data-between-onprem-and-cloud.md#setting-credentials-and-security) 如需詳細資訊設定內部部署 Oracle 資料來源的認證。
## Oracle 資料集類型屬性

如需區段和屬性可用來定義資料集的完整清單，請參閱 [建立資料集](data-factory-create-datasets.md) 文件。 資料集 JSON 的結構、可用性和原則等區段類似於所有的資料集類型 (Oracle、Azure Blob、Azure 資料表等)。
 
每個資料集類型的 typeProperties 區段都不同，可提供資料存放區中資料的位置相關資訊。 OracleTable 資料集類型的 typeProperties 區段具有下列屬性。

屬性 | 說明 | 必要
-------- | ----------- | --------
tableName | Oracle 資料庫中連結服務所參照的資料表名稱。 | 否 (如果 **oracleReaderQuery** 的 **SqlSource** 指定)

## Oracle 複製活動類型屬性

如需區段和屬性可用來定義活動的完整清單，請參閱 [建立管線](data-factory-create-pipelines.md) 文件。 名稱、描述、輸入和輸出資料表、各種原則等屬性都適用於所有活動類型。 

**注意 ︰** 複製活動會採用一個輸入，而且只產生一個輸出。

另一方面，活動的 typeProperties 區段中可用的屬性會隨著每個活動類型而有所不同，而在複製活動的案例中，可用的屬性會根據來源與接收的類型而有所不同。

在複製活動類型的來源時 **OracleSource** 下列屬性可用於 **typeProperties** 區段 ︰

屬性 | 說明 |允許的值 | 必要
-------- | ----------- | ------------- | --------
oracleReaderQuery | 使用自訂查詢來讀取資料。 | SQL 查詢字串。 
例如 ︰ 選取 * from MyTable <p>如果未指定，執行 SQL 陳述式 ︰ 選取 * from MyTable</p> | 否 (如果 **tableName** 的 **資料集** 指定)

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

### Oracle 的類型對應

中所述 [資料移動活動](data-factory-data-movement-activities.md) 文章複製活動會執行轉換成接收類型具有下列 2 個步驟的方法的來源類型的自動類型轉換的自動類型轉換 ︰

1. 從原生來源類型轉換成 .NET 類型
2. 從 .NET 類型轉換成原生接收類型

從 Oracle 移動資料時，將使用下列從 Oracle 資料類型到 .NET 類型的對應，反之亦然。

Oracle 資料類型 | .NET Framework 資料類型
---------------- | ------------------------
BFILE | Byte]
BLOB | Byte]
CHAR | String
CLOB | String
日期 | DateTime
FLOAT | 十進位
INTEGER | 十進位
間隔年至月 | Int32
間隔日至秒鐘 | TimeSpan
長 | String
長 RAW | Byte]
NCHAR | String
NCLOB | String
數字 | 十進位
NVARCHAR2 | String
RAW | Byte]
ROWID | String
時間戳記 | DateTime
本地時區的時間戳記 | DateTime
時區的時間戳記 | DateTime
不帶正負號的整數 | Number
VARCHAR2 | String
XML | String

## 疑難排解秘訣

* * 問題: * *
您會看到下列 **錯誤訊息**︰ 複製活動遇到無效的參數: 'UnknownParameterName'，詳細的訊息 ︰ 找不到要求的.Net Framework 資料提供者。 可能尚未安裝」。  

**可能的原因**

1. 未安裝 .NET Framework Data Provider for Oracle。
2. .NET Framework Data Provider for Oracle 已安裝於 .NET Framework 2.0，而且在 .NET Framework 4.0 資料夾中找不到。 

**解析/因應措施**

1. 如果您尚未安裝 Oracle 的.NET 提供者，請 [安裝](http://www.oracle.com/technetwork/topics/dotnet/utilsoft-086879.html) ，然後重試的案例。 
2. 如果您即使在安裝提供者之後還是會收到錯誤訊息，請執行下列作業： 
    1. 從資料夾開啟.NET 2.0 的機器組態 ︰ <system disk>: \Windows\Microsoft.NET\Framework64\v2.0.50727\CONFIG\machine.config。
    2. 搜尋 **適用於.NET 的 Oracle 資料提供者**, ，您應該能夠找到一個項目類似下面 **system.data** ]-> [ **DbProviderFactories**:下列 **system.data** 下的-> **DbProviderFactories**:
            “<add name="Oracle Data Provider for .NET" invariant="Oracle.DataAccess.Client" description="Oracle Data Provider for .NET" type="Oracle.DataAccess.Client.OracleClientFactory, Oracle.DataAccess, Version=2.112.3.0, Culture=neutral, PublicKeyToken=89b483f429c47342" />”
2.  將此項目複製到下列 v4.0 資料夾中的 machine.config 檔案中 ︰ <system disk>: \Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config，以及變更要 4.xxx.x.x 的版本。
3.  執行 「 gacutil /i [提供者路徑]""< ODP.NET 安裝路徑 > \11.2.0\client_1\odp.net\bin\4\Oracle.DataAccess.dll 」 安裝到全域組件快取 (GAC)。



[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]





