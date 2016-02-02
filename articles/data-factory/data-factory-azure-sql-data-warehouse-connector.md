<properties 
    pageTitle="從 Azure SQL 資料倉儲來回移動資料 | Azure Data Factory" 
    description="了解如何使用 Azure Data Factory 從 Azure SQL 資料倉儲來回移動資料" 
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


# 使用 Azure Data Factory 從 Azure SQL 資料倉儲來回移動資料

本文概述如何使用 Data Factory 複製活動，在 Azure SQL 資料倉儲與其他資料存放區之間移動資料。 這篇文章是根據 [資料移動活動](data-factory-data-movement-activities.md) 一文，該文呈現使用複製活動，而且支援的資料存放區組合的資料移動的一般概觀。

## 範例：將資料從 Azure SQL 資料倉儲複製到 Azure Blob

下列範例顯示：

1. 連結的服務型別的 [AzureSqlDW](#azure-sql-data-warehouse-linked-service-properties)。
2. 連結的服務型別的 [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties)。
3. 輸入 [資料集](data-factory-create-datasets.md) 型別的 [AzureSqlDWTable](#azure-sql-data-warehouse-dataset-type-properties)。
4. An output [dataset](data-factory-create-datasets.md) of type [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
4. A [管線](data-factory-create-pipelines.md) 具有複製活動使用 [SqlDWSource](#azure-sql-data-warehouse-copy-activity-type-properties) 和 [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties)。

此範例會每小時將屬於時間序列的資料從 Azure SQL 資料倉儲資料庫中的資料表複製到 Blob。 範例後面的各節會說明這些範例中使用的 JSON 屬性。

**Azure SQL 資料倉儲連結服務：**

    {
      "name": "AzureSqlDWLinkedService",
      "properties": {
        "type": "AzureSqlDW",
        "typeProperties": {
          "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
        }
      }
    }

**Azure Blob 儲存體連結服務：**

    {
      "name": "StorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
      }
    }

**Azure SQL 資料倉儲輸入資料集：**

此範例假設您已在 SQL Azure 資料倉儲中建立資料表 "MyTable"，其中包含時間序列資料的資料行 (名稱為 "timestampcolumn")。

設定 “external”: ”true” 和指定 externalData 原則即可通知 Data Factory 服務：這是 Data Factory 外部的資料表而且不是由 Data Factory 中的活動所產生。

    {
      "name": "AzureSqlDWInput",
      "properties": {
        "type": "AzureSqlDWTable",
        "linkedServiceName": "AzureSqlDWLinkedService",
        "typeProperties": {
          "tableName": "MyTable"
        },
        "external": true,
        "availability": {
          "frequency": "Hour",
          "interval": 1
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

資料會每小時寫入至新的 Blob (頻率：小時，間隔：1)。 根據正在處理之配量的開始時間，以動態方式評估 Blob 的資料夾路徑。 資料夾路徑會使用開始時間的年、月、日和小時部分。

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

此管線包含複製活動，該活動已設定為使用上述輸入和輸出資料集並排定為每小時執行。 在管線 JSON 定義中，**source** 類型設為 **SqlDWSource**，而 **sink** 類型設為 **BlobSink**。 針對 **SqlReaderQuery** 屬性指定的 SQL 查詢會選取過去一小時內要複製的資料。

    {  
        "name":"SamplePipeline",
        "properties":{  
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline for copy activity",
        "activities":[  
          {
            "name": "AzureSQLDWtoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [
              {
                "name": "AzureSqlDWInput"
              }
            ],
            "outputs": [
              {
                "name": "AzureBlobOutput"
              }
            ],
            "typeProperties": {
              "source": {
                "type": "SqlDWSource",
                "sqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
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

> [AZURE.NOTE] 在上述範例中，已為 SqlDWSource 指定 **sqlReaderQuery**。 複製活動會針對 Azure SQL 資料倉儲來源執行這項查詢以取得資料。
>  
> 或者，您可以藉由指定 **sqlReaderStoredProcedureName** 和 **storedProcedureParameters** (如果預存程序接受參數) 來指定預存程序。
>  
> 如果您未指定 sqlReaderQuery 或 sqlReaderStoredProcedureName，就會使用資料集 JSON 的結構區段中定義的資料行來建立一個查詢，以對 Azure SQL 資料倉儲執行 (從 mytable 選取 column1、column2)。 如果資料集定義沒有結構，則會從資料表中選取所有資料行。

## 範例：將資料從 Azure Blob 複製到 Azure SQL 資料倉儲

下列範例顯示：

1.  連結的服務型別的 [AzureSqlDW](#azure-sql-data-warehouse-linked-service-properties)。
2.  連結的服務型別的 [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties)。
3.  An [dataset](data-factory-create-datasets.md) dataset of type [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
4.  [資料集](data-factory-create-datasets.md) 類型的資料集 [AzureSqlDWTable](#azure-sql-data-warehouse-dataset-type-properties)。
4.  A [管線](data-factory-create-pipelines.md) 具有複製活動使用 [BlobSource](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties) 和 [SqlDWSink](#azure-sql-data-warehouse-copy-activity-type-properties)。


此範例會每小時將屬於時間序列的資料從 Azure Blob 複製到 Azure SQL 資料倉儲資料庫中的資料表。 範例後面的各節會說明這些範例中使用的 JSON 屬性。

**Azure SQL 資料倉儲連結服務：**

    {
      "name": "AzureSqlDWLinkedService",
      "properties": {
        "type": "AzureSqlDW",
        "typeProperties": {
          "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
        }
      }
    }

**Azure Blob 儲存體連結服務：**

    {
      "name": "StorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
      }
    }

**Azure Blob 輸入資料集：**

每小時從新的 Blob 挑選資料 (頻率：小時，間隔：1)。 根據正在處理之配量的開始時間，以動態方式評估 Blob 的資料夾路徑和檔案名稱。 年、 月和日的開始時間的部分，會使用的資料夾路徑和檔案名稱使用開始時間的小時部分。 "external":"true"設定會通知 Data Factory 服務，此資料表是 data factory 外部的並不是由 data factory 中的活動所產生。

    {
      "name": "AzureBlobInput",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}",
          "fileName": "{Hour}.csv",
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
            "columnDelimiter": ",",
            "rowDelimiter": "\n"
          }
        },
        "external": true,
        "availability": {
          "frequency": "Hour",
          "interval": 1
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

**Azure SQL 資料倉儲輸出資料集：**

此範例會將資料複製到 Azure SQL 資料倉儲中名為 "MyTable" 的資料表。 您應該在Azure SQL 資料倉儲中建立此資料表，其資料行的數目如您預期 Blob CSV 檔案要包含的數目。 此資料表會每小時加入新的資料列。

    {
      "name": "AzureSqlDWOutput",
      "properties": {
        "type": "AzureSqlDWTable",
        "linkedServiceName": "AzureSqlDWLinkedService",
        "typeProperties": {
          "tableName": "MyOutputTable"
        },
        "availability": {
          "frequency": "Hour",
          "interval": 1
        }
      }
    }

**具有複製活動的管線**

此管線包含複製活動，該活動已設定為使用上述輸入和輸出資料集並排定為每小時執行。 在管線 JSON 定義中，**source** 類型設為 **BlobSource**，而 **sink** 類型設為 **SqlDWSink**。

    {  
        "name":"SamplePipeline",
        "properties":{  
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline with copy activity",
        "activities":[  
          {
            "name": "AzureBlobtoSQLDW",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [
              {
                "name": "AzureBlobInput"
              }
            ],
            "outputs": [
              {
                "name": "AzureSqlDWOutput"
              }
            ],
            "typeProperties": {
              "source": {
                "type": "BlobSource",
                "blobColumnSeparators": ","
              },
              "sink": {
                "type": "SqlDWSink"
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

請參閱 [載入資料，使用 Azure Data Factory](../sql-data-warehouse/sql-data-warehouse-get-started-load-with-azure-data-factory.md) Azure SQL 資料倉儲文件的逐步解說中的文件。

## Azure SQL 資料倉儲連結服務屬性

下表提供 Azure SQL 資料倉儲連結服務專屬 JSON 元素的描述。

 屬性| 說明| 必要
-------- | ----------- | --------
 類型| 類型屬性必須設為：**AzureSqlDW**| 是
 **connectionString**| 針對 connectionString 屬性指定連線到 Azure SQL 資料倉儲執行個體所需的資訊。| 是

注意: 您需要設定 [Azure SQL Database 防火牆](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure)。 您需要將資料庫伺服器設定成 [允許 Azure 服務存取伺服器](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure)。 此外，如果您要從 Azure 外部 (包括從具有 Fata Factory 閘道器的內部部署資料來源) 將資料複製到 Azure SQL 資料倉儲，則必須為傳送資料到 Azure SQL 資料倉儲的機器設定適當的 IP 位址範圍。

## Azure SQL 資料倉儲資料集類型屬性

如需區段和屬性可用來定義資料集的完整清單，請參閱 [建立資料集](data-factory-create-datasets.md) 文件。資料集 JSON 的結構、可用性和原則等區段類似於所有的資料集類型 (SQL Azure、Azure Blob、Azure 資料表等)。

每個資料集類型的 typeProperties 區段都不同，可提供資料存放區中資料的位置相關資訊。 **AzureSqlDWTable** 類型資料集的 **typeProperties** 區段具有下列屬性。

| 屬性| 說明| 必要|
| -------- | ----------- | -------- |
| tableName| Azure SQL 資料倉儲資料庫中連結服務所參照的資料表名稱。| 是|

## Azure SQL 資料倉儲複製活動類型屬性

如需區段和屬性可用來定義活動的完整清單，請參閱 [建立管線](data-factory-create-pipelines.md) 文件。名稱、描述、輸入和輸出資料表、各種原則等屬性都適用於所有活動類型。

**附註：**複製活動只會採用一個輸入，而且只產生一個輸出。

另一方面，活動的 typeProperties 區段中可用的屬性會隨著每個活動類型而有所不同，而在複製活動的案例中，可用的屬性會根據來源與接收的類型而有所不同。

### SqlDWSource

在複製活動的案例中，如果來源類型為 **SqlDWSource**，則 **typeProperties** 區段可使用下列屬性：

| 屬性| 說明| 允許的值| 必要|
| -------- | ----------- | -------------- | -------- |
| sqlReaderQuery| 使用自訂查詢來讀取資料。| SQL 查詢字串。例如：select * from MyTable。| 否|
| sqlReaderStoredProcedureName| 從來源資料表讀取資料的預存程序名稱。| 預存程序的名稱。| 否|
| storedProcedureParameters| 預存程序的參數。| 名稱/值組。參數的名稱和大小寫必須符合預存程序參數的名稱和大小寫。| 否|

如果已為 SqlDWSource 指定 **sqlReaderQuery**，複製活動會針對 Azure SQL 資料倉儲來源執行這項查詢以取得資料。

或者，您可以藉由指定 **sqlReaderStoredProcedureName** 和 **storedProcedureParameters** (如果預存程序接受參數) 來指定預存程序。

如果您未指定 sqlReaderQuery 或 sqlReaderStoredProcedureName，就會使用資料集 JSON 的結構區段中定義的資料行來建立一個查詢，以對 Azure SQL 資料倉儲執行 (從 mytable 選取 column1、column2)。 如果資料集定義沒有結構，則會從資料表中選取所有資料行。

#### SqlDWSource 範例

    "source": {
        "type": "SqlDWSource",
        "sqlReaderStoredProcedureName": "CopyTestSrcStoredProcedureWithParameters",
        "storedProcedureParameters": {
            "stringData": { "value": "str3" },
            "id": { "value": "$$Text.Format('{0:yyyy}', SliceStart)", "type": "Int"}
        }
    }

**預存程序定義：**

    CREATE PROCEDURE CopyTestSrcStoredProcedureWithParameters
    (
        @stringData varchar(20),
        @id int
    )
    AS
    SET NOCOUNT ON;
    BEGIN
         select *
         from dbo.UnitTestSrcTable
         where dbo.UnitTestSrcTable.stringData != stringData
        and dbo.UnitTestSrcTable.id != id
    END
    GO

### SqlDWSink

**SqlDWSink** 支援下列屬性：

| 屬性| 說明| 允許的值| 必要|
| -------- | ----------- | -------------- | -------- |
| writeBatchSize| 當緩衝區大小達到 writeBatchSize 時，將資料插入 SQL 資料表中| 整數。(單位 = 資料列計數)| 否 (預設值 = 10000)|
| writeBatchTimeout| 在逾時前等待批次插入作業完成的時間。| (單位 = 時間範圍) 範例：“00:30:00” (30 分鐘)。| 否|
| sqlWriterCleanupScript| 使用者指定了可供複製活動執行的查詢，以便清除特定配量的資料。如需詳細資訊，請參閱下面「重複性」一節。| 查詢陳述式。| 否|
| sliceIdentifierColumnName| 使用者指定了可供複製活動使用自動產生的配量識別碼填入的資料行名稱，在重新執行時將用來清除特定配量的資料。如需詳細資訊，請參閱下面「重複性」一節。| 資料類型為 binary(32) 之資料行的資料行名稱。| 否|

#### SqlDWSink 範例

    "sink": {
        "type": "SqlDWSink",
        "writeBatchSize": 1000000,
        "writeBatchTimeout": "00:05:00",
    }

[AZURE.INCLUDE [data-factory-type-repeatability-for-sql-sources](../../includes/data-factory-type-repeatability-for-sql-sources.md)]

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

### Azure SQL 資料倉儲的類型對應

中所述 [資料移動活動](data-factory-data-movement-activities.md) 文章複製活動會執行轉換成接收類型具有下列 2 個步驟的方法的來源類型的自動類型轉換的自動類型轉換:

1. 從原生來源類型轉換成 .NET 類型
2. 從 .NET 類型轉換成原生接收類型

從 Azure SQL、SQL Server、Sybase 來回移動資料時，將使用下列從 SQL 類型到 .NET 類型的對應，反之亦然。

對應是相同 [ADO.NET 的 SQL Server 資料類型對應](https://msdn.microsoft.com/library/cc716729.aspx)。

| SQL Server Database Engine 類型| .NET Framework 類型|
| ------------------------------- | ------------------- |
| bigint| Int64|
| binary| Byte]|
| bit| Boolean|
| char| 字串，Char]|
| 日期| DateTime|
| Datetime| DateTime|
| datetime2| DateTime|
| Datetimeoffset| DateTimeOffset|
| 十進位| 十進位|
| FILESTREAM 屬性 (varbinary(max))| Byte]|
| Float| 兩倍|
| image| Byte]|
| int| Int32|
| money| 十進位|
| nchar| 字串，Char]|
| ntext| 字串，Char]|
| numeric| 十進位|
| nvarchar| 字串，Char]|
| real| 單一|
| rowversion| Byte]|
| smalldatetime| DateTime|
| smallint| Int16|
| smallmoney| 十進位|
| sql_variant| 物件 *|
| text| 字串，Char]|
| 分析| TimeSpan|
| timestamp| Byte]|
| tinyint| 位元組|
| uniqueidentifier| Guid|
| varbinary| Byte]|
| varchar| 字串，Char]|
| xml| Xml|



[AZURE.INCLUDE [data-factory-type-conversion-sample](../../includes/data-factory-type-conversion-sample.md)]

[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

















