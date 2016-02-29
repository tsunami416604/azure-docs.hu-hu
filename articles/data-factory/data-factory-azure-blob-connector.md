<properties 
    pageTitle="從 Azure Blob 來回移動資料 | Azure Data Factory" 
    description="了解如何使用 Azure Data Factory 從 Azure Blob 儲存體來回移動資料" 
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
    ms.date="11/03/2015" 
    ms.author="spelluru"/>

# 使用 Azure Data Factory 從 Azure Blob 來回移動資料
本文概述如何在 Azure Data Factory 中使用複製活動，在 Azure Blob 與其他資料存放區之間移動資料。 這篇文章是根據 [資料移動活動](data-factory-data-movement-activities.md) 一文，該文呈現使用複製活動，而且支援的資料存放區組合的資料移動的一般概觀。

## 範例：從 Azure Blob 複製資料到 Azure SQL
下列範例顯示：

1.  連結的服務型別的 [AzureSqlDatabase](data-factory-azure-sql-connector.md#azure-sql-linked-service-properties)。
2.  連結的服務型別的 [AzureStorage](#azure-storage-linked-service-properties)。
3.  輸入 [資料集](data-factory-create-datasets.md) 型別的 [AzureBlob](#azure-blob-dataset-type-properties)。
4.  輸出 [資料集](data-factory-create-datasets.md) 型別的 [AzureSqlTable](data-factory-azure-sql-connector.md#azure-sql-dataset-type-properties)。
4.  A [管線](data-factory-create-pipelines.md) 具有使用 「 複製活動 [BlobSource](#azure-blob-copy-activity-type-properties) 和 [SqlSink](data-factory-azure-sql-connector.md#azure-sql-copy-activity-type-properties)。

此範例會每小時將屬於時間序列的資料從 Azure Blob 複製到 Azure SQL Database 中的資料表。 範例後面的各節會說明這些範例中使用的 JSON 屬性。 

**Azure SQL 連結服務：**

    {
      "name": "AzureSqlLinkedService",
      "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
          "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
        }
      }
    }

**Azure 儲存體連結服務：**

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
          "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/",
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

**Azure SQL 輸出資料集：**

此範例會將資料複製到 Azure SQL Database 中名為 "MyTable" 的資料表。 您應該在Azure SQL Database 中建立此資料表，其資料行的數目如您預期 Blob CSV 檔案要包含的數目。 此資料表會每小時加入新的資料列。

    {
      "name": "AzureSqlOutput",
      "properties": {
        "type": "AzureSqlTable",
        "linkedServiceName": "AzureSqlLinkedService",
        "typeProperties": {
          "tableName": "MyOutputTable"
        },
        "availability": {
          "frequency": "Hour",
          "interval": 1
        }
      }
    }

**具有複製活動的管線：**

此管線包含複製活動，該活動已設定為使用上述輸入和輸出資料集並排定為每小時執行。 在管線 JSON 定義 **來源** 類型設為 **BlobSource** 和 **接收器** 類型設為 **SqlSink**。 

    {  
        "name":"SamplePipeline",
        "properties":{  
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline with copy activity",
        "activities":[  
          {
            "name": "AzureBlobtoSQL",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [
              {
                "name": "AzureBlobInput"
              }
            ],
            "outputs": [
              {
                "name": "AzureSqlOutput"
              }
            ],
            "typeProperties": {
              "source": {
                "type": "BlobSource",
                "blobColumnSeparators": ","
              },
              "sink": {
                "type": "SqlSink"
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

## 範例：從 Azure SQL 複製資料到 Azure Blob
下列範例顯示：

1.  連結的服務型別的 [AzureSqlDatabase](data-factory-azure-sql-connector.md#azure-sql-linked-service-properties)。
2.  連結的服務型別的 [AzureStorage](#azure-storage-linked-service-properties)。
3.  輸入 [資料集](data-factory-create-datasets.md) 型別的 [AzureSqlTable](data-factory-azure-sql-connector.md#azure-sql-dataset-type-properties)。
4.  輸出 [資料集](data-factory-create-datasets.md) 型別的 [AzureBlob](#azure-blob-dataset-type-properties)。
4.  A [管線](data-factory-create-pipelines.md) 具有複製活動使用 [SqlSource](data-factory-azure-sql-connector.md#azure-sql-copy-activity-type-properties) 和 [BlobSink](#azure-blob-copy-activity-type-properties)。


此範例會每小時將屬於時間序列的資料從 Azure SQL Database 中的資料表複製到 Blob。 範例後面的各節會說明這些範例中使用的 JSON 屬性。 

**Azure SQL 連結服務：**

    {
      "name": "AzureSqlLinkedService",
      "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
          "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
        }
      }
    }

**Azure 儲存體連結服務：**

    {
      "name": "StorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
      }
    }

**Azure SQL 輸入資料集：**

此範例假設您已在 SQL Azure 中建立資料表 "MyTable"，其中包含時間序列資料的資料行 (名稱為 "timestampcolumn")。 

設定 “external”: ”true” 和指定 externalData 原則即可通知 Azure Data Factory 服務：這是 Data Factory 外部的資料表而且不是由 Data Factory 中的活動所產生。

    {
      "name": "AzureSqlInput",
      "properties": {
        "type": "AzureSqlTable",
        "linkedServiceName": "AzureSqlLinkedService",
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
          "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}/",
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

此管線包含複製活動，該活動已設定為使用上述輸入和輸出資料集並排定為每小時執行。 在管線 JSON 定義 **來源** 類型設為 **SqlSource** 和 **接收器** 類型設為 **BlobSink**。 針對指定的 SQL 查詢 **SqlReaderQuery** 屬性選取過去一小時內要複製的資料。


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
                        "name": "AzureSQLInput"
                      }
                    ],
                    "outputs": [
                      {
                        "name": "AzureBlobOutput"
                      }
                    ],
                    "typeProperties": {
                        "source": {
                            "type": "SqlSource",
                            "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
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

## Azure 儲存體連結服務屬性

您可以使用 Azure 儲存體連結服務，將 Azure 儲存體帳戶連結至 Azure Data Factory。 下表提供 Azure 儲存體連結服務專屬 JSON 元素的描述。

| 屬性 | 說明 | 必要 |
| -------- | ----------- | -------- |
| 類型 | Type 屬性必須設定為: **AzureStorage** | 是 |
| connectionString | 針對 connectionString 屬性指定連接到 Azure 儲存體所需的資訊。 您可以從 Azure 傳統入口網站取得 Azure 儲存體的 connectionString。 | 是 |

## Azure Blob 資料集類型屬性

如需 JSON 區段和屬性可用來定義資料集的完整清單，請參閱 [建立資料集](data-factory-create-datasets.md) 文件。 資料集 JSON 的結構、可用性和原則等區段類似於所有的資料集類型 (SQL Azure、Azure Blob、Azure 資料表等)。

 **TypeProperties** 區段都不同的資料集的每一種，提供位置相關資訊等資料存放區中的資料格式。 類型的資料集的 typeProperties 區段 **AzureBlob** 資料集具有下列屬性。

| 屬性 | 說明 | 必要 |
| -------- | ----------- | -------- | 
| folderPath | Blob 儲存體中容器和資料夾的路徑。 範例：myblobcontainer\myblobfolder\ | 是 |
| fileName | <p>Blob 的名稱。 檔案名稱是選擇性的。 </p><p>如果您指定檔案名稱，活動 (包括複製) 適用於特定的 Blob。</p><p>未指定 fileName 時，複本會將輸入資料集的 folderPath 包含所有 Blob。</p><p>檔案名稱未指定輸出資料集，所產生檔案的名稱會在下列這種格式: 資料。<Guid>.txt (例如:: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt</p> | 否 |
| partitionedBy | partitionedBy 是選擇性的屬性。 您可以用來指定時間序列資料的動態 folderPath 和 filename。 例如，folderPath 可針對每小時的資料進行參數化。 請參閱 [運用 partitionedBy 屬性區段](#Leveraging-partitionedBy-property) 如下如需詳細資訊。 | 否
| format | 支援兩種格式類型: **TextFormat**, ，**AvroFormat**。 您需要將格式底下的 type 屬性設定為這些值。 如果格式為 TextFormat，您可以指定格式的其他選擇性屬性。 請參閱 [指定 TextFormat](#specifying-textformat) 下面章節以取得詳細資料。 | 否
| compression | 指定此資料的壓縮類型和層級。 支援的類型為：GZip、Deflate 和 BZip2，而支援的層級為：最佳和最快。 請參閱 [壓縮支援](#compression-support) 一節以取得詳細資料。  | 否 |

### 運用 partitionedBy 屬性
如上所述，您可以指定的動態 folderPath 和 filename 時間序列資料 **partitionedBy** 區段、 Data Factory 巨集和系統變數: SliceStart 和 SliceEnd，表示指定的資料配量的開始和結束時間。

請參閱 [Data Factory 系統變數](data-factory-scheduling-and-execution.md#data-factory-system-variables) 和 [Data Factory 函式參考](data-factory-scheduling-and-execution.md#data-factory-functions-reference) 來了解 Data Factory 系統變數和函式，您可以使用 partitionedBy 一節。   

請參閱 [建立資料集](data-factory-create-datasets.md) 和 [排程和執行](data-factory-scheduling-and-execution.md) 文件，如需有關時間序列資料集、 排程和配量。

#### 範例 1

    "folderPath": "wikidatagateway/wikisampledataout/{Slice}",
    "partitionedBy": 
    [
        { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
    ],

在上述範例中，{Slice} 會取代成 Data Factory 系統變數 SliceStart 的值 (使用指定的格式 (YYYYMMDDHH))。 SliceStart 是指配量的開始時間。 每個配量的 folderPath 都不同。 例如：wikidatagateway/wikisampledataout/2014100103 或 wikidatagateway/wikisampledataout/2014100104

#### 範例 2

    "folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
    "fileName": "{Hour}.csv",
    "partitionedBy": 
     [
        { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
        { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } }, 
        { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } }, 
        { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } } 
    ],

在上述範例中，SliceStart 的年、月、日和時間會擷取到 folderPath 和 fileName 屬性所使用的個別變數。

### 指定 TextFormat

如果格式設為 **TextFormat**, ，您可以指定下列 **選擇性** 屬性在 **格式** 一節。

| 屬性 | 說明 | 必要 |
| -------- | ----------- | -------- |
| columnDelimiter | 在檔案中做為資料行分隔符號的字元。此標記是選擇性的。 預設值是逗號 (,)。 | 否 |
| rowDelimiter | 在檔案中做為資料列分隔符號的字元。 此標記是選擇性的。 預設值是下列任一項：[“\r\n”, “\r”,” \n”]。 | 否 |
| escapeChar | <p>用來逸出內容中顯示之資料行分隔符號的特殊字元。 此標記是選擇性的。 沒有預設值。 您必須指定這個屬性不能超過一個字元。</p><p>比方說，如果您以逗號 (，) 做為資料行分隔符號，但您想要有逗號字元的文字 (範例:"Hello，world")，您可以定義 '$' 做為逸出字元，並使用字串"Hello$，world"中的來源。</p><p>請注意，您無法指定 escapeChar 和 quoteChar 資料表。</p> | 否 | 
| quoteChar | <p>用來引用字串值的特殊字元。 引號字元內的資料行和資料列分隔符號會被視為字串值的一部分。 此標記是選擇性的。 沒有預設值。 您必須指定這個屬性不能超過一個字元。</p><p>比方說，如果您以逗號 (，) 做為資料行分隔符號，但您想要有逗號字元的文字 (範例: < Hello，world >)，您可以定義 '"' 做為引用字元並在來源中使用字串 <"Hello，world">。 這個屬性不適用於輸入和輸出資料表。</p><p>請注意，您無法指定 escapeChar 和 quoteChar 資料表。</p> | 否 |
| nullValue | <p>用來代表 Blob 檔案內容中 null 值的字元。 此標記是選擇性的。 預設值為"\N"。</p><p>例如，根據上述範例，blob 中的"NaN"會轉換成 null 值複製到 SQL Server 時。</p> | 否 |
| encodingName | 指定編碼名稱。 如需有效編碼名稱的清單，請參閱: [Encoding.EncodingName 屬性](https://msdn.microsoft.com/library/system.text.encoding.aspx)。 例如：windows-1250 或 shift_jis。 預設值為 UTF-8。 | 否 | 

#### 範例
下列範例顯示 TextFormat 的一些格式屬性。

    "typeProperties":
    {
        "folderPath": "mycontainer/myfolder",
        "fileName": "myblobname"
        "format":
        {
            "type": "TextFormat",
            "columnDelimiter": ",",
            "rowDelimiter": ";",
            "quoteChar": "\"",
            "NullValue": "NaN"
        }
    },

若要使用 escapeChar 而不是 quoteChar，請以下列內容取代具有 quoteChar 的行：

    "escapeChar": "$",

### 指定 AvroFormat
如果格式設為 AvroFormat，您不需要在 typeProperties 區段內的 Format 區段中指定任何屬性。 範例：

    "format":
    {
        "type": "AvroFormat",
    }

若要 Hive 資料表中使用 Avro 格式，您可以參考 [Apache Hive 的教學課程](https://cwiki.apache.org/confluence/display/Hive/AvroSerDe)。

[AZURE.INCLUDE [data-factory-compression](../../includes/data-factory-compression.md)]


## Azure Blob 複製活動類型屬性  
如需區段和屬性可用來定義活動的完整清單，請參閱 [建立管線](data-factory-create-pipelines.md) 文件。 名稱、描述、輸入和輸出資料表、各種原則等屬性都適用於所有活動類型。

另一方面，活動的 typeProperties 區段中可用的屬性會隨著每個活動類型而有所不同，而在複製活動的案例中，可用的屬性會根據來源與接收的類型而有所不同。

**BlobSource** 支援中的下列屬性 **typeProperties** 區段:

| 屬性 | 說明 | 允許的值 | 必要 |
| -------- | ----------- | -------------- | -------- | 
| treatEmptyAsNull | 指定是否將 null 或空字串視為 null 值。 | TRUE<br/>FALSE | 否 |
| skipHeaderLineCount | 指出需要略過多少行。 是適用於只使用輸入資料集 **TextFormat**。 | 0 與最大值之間的整數。 | 否 | 
| 遞迴 | 表示是否從子資料夾，或只有從指定的資料夾，以遞迴方式讀取資料。 | True (預設值)、False | 否 | 


**BlobSink** 支援下列屬性 **typeProperties** 區段:

| 屬性 | 說明 | 允許的值 | 必要 |
| -------- | ----------- | -------------- | -------- |
| blobWriterAddHeader | 指定是否要加入資料行定義的標頭。 | TRUE<br/>FALSE (預設值) | 否 |
| copyBehavior | 當來源為 BlobSource 或 FileSystem 時，定義複製行為。 | <p>有三種可能的值為 copyBehavior 屬性。 </p><ul><li>**PreserveHierarchy:** : 保留目標資料夾，亦即，來源資料夾的原始程式檔的相對路徑中的檔案階層完全相同的目標資料夾的目標檔案相對路徑。</li><li>**FlattenHierarchy:** 從來源資料夾的所有檔案會都出現在第一層的目標資料夾。 目標檔案會有自動產生的名稱。 </li><li>**MergeFiles: (預設值)** 合併到一個檔案的來源資料夾的所有檔案。 如果已指定檔案/Blob 名稱，合併檔案的名稱就會是指定的名稱；否則，就會是自動產生的檔案名稱。</li></ul> | 否 |

### 遞迴和 copyBehavior 範例
本節說明遞迴和 copyBehavior 值在不同組合的情況下，複製作業所產生的行為。 

遞迴 | copyBehavior | 產生的行為
--------- | ------------ | --------
true | preserveHierarchy | <p>來源資料夾 Folder1 具有下列結構:</p> <p>Folder1<br/>(& s) nbsp; & nbsp; & nbsp; (& s) nbsp;File1<br/>(& s) nbsp; & nbsp; & nbsp; (& s) nbsp;2<br/>(& s) nbsp; & nbsp; & nbsp; (& s) nbsp;Subfolder1<br/>(& s) nbsp; (& s) nbsp; & nbsp; (& s) nbsp; (& s) nbsp; (& s) nbsp; (& s) nbsp; (& s) nbsp;File3<br/>(& s) nbsp; (& s) nbsp; & nbsp; (& s) nbsp; (& s) nbsp; (& s) nbsp; (& s) nbsp; (& s) nbsp;File4<br/>(& s) nbsp; (& s) nbsp; & nbsp; (& s) nbsp; (& s) nbsp; (& s) nbsp; (& s) nbsp; (& s) nbsp;File5</p>目標資料夾 Folder1 會有相同的結構做為來源<p>Folder1<br/>(& s) nbsp; & nbsp; & nbsp; (& s) nbsp;File1<br/>(& s) nbsp; & nbsp; & nbsp; (& s) nbsp;2<br/>(& s) nbsp; & nbsp; & nbsp; (& s) nbsp;Subfolder1<br/>(& s) nbsp; (& s) nbsp; & nbsp; (& s) nbsp; (& s) nbsp; (& s) nbsp; (& s) nbsp; (& s) nbsp;File3<br/>(& s) nbsp; (& s) nbsp; & nbsp; (& s) nbsp; (& s) nbsp; (& s) nbsp; (& s) nbsp; (& s) nbsp;File4<br/>(& s) nbsp; (& s) nbsp; & nbsp; (& s) nbsp; (& s) nbsp; (& s) nbsp; (& s) nbsp; (& s) nbsp;File5</p>.  
true | flattenHierarchy | <p>來源資料夾 Folder1 具有下列結構:</p> <p>Folder1<br/>(& s) nbsp; & nbsp; & nbsp; (& s) nbsp;File1<br/>(& s) nbsp; & nbsp; & nbsp; (& s) nbsp;2<br/>(& s) nbsp; & nbsp; & nbsp; (& s) nbsp;Subfolder1<br/>(& s) nbsp; (& s) nbsp; & nbsp; (& s) nbsp; (& s) nbsp; (& s) nbsp; (& s) nbsp; (& s) nbsp;File3<br/>(& s) nbsp; (& s) nbsp; & nbsp; (& s) nbsp; (& s) nbsp; (& s) nbsp; (& s) nbsp; (& s) nbsp;File4<br/>(& s) nbsp; (& s) nbsp; & nbsp; (& s) nbsp; (& s) nbsp; (& s) nbsp; (& s) nbsp; (& s) nbsp;File5</p>目標資料夾 1 將會有下列結構: <p>Folder1<br/>(& s) nbsp; & nbsp; & nbsp; (& s) nbsp; 自動產生名稱 File1<br/>(& s) nbsp; (& s) nbsp; (& s) nbsp; & nbsp; File2 如自動產生的名稱<br/>(& s) nbsp; & nbsp; & nbsp; (& s) nbsp; File3 自動產生名稱<br/>(& s) nbsp; & nbsp; & nbsp; (& s) nbsp; File4 自動產生名稱<br/>(& s) nbsp; & nbsp; & nbsp; (& s) nbsp; File5 自動產生名稱</p>
true | mergeFiles | <p>來源資料夾 Folder1 具有下列結構:</p> <p>Folder1<br/>(& s) nbsp; & nbsp; & nbsp; (& s) nbsp;File1<br/>(& s) nbsp; & nbsp; & nbsp; (& s) nbsp;2<br/>(& s) nbsp; & nbsp; & nbsp; (& s) nbsp;Subfolder1<br/>(& s) nbsp; (& s) nbsp; & nbsp; (& s) nbsp; (& s) nbsp; (& s) nbsp; (& s) nbsp; (& s) nbsp;File3<br/>(& s) nbsp; (& s) nbsp; & nbsp; (& s) nbsp; (& s) nbsp; (& s) nbsp; (& s) nbsp; (& s) nbsp;File4<br/>(& s) nbsp; (& s) nbsp; & nbsp; (& s) nbsp; (& s) nbsp; (& s) nbsp; (& s) nbsp; (& s) nbsp;File5</p>目標資料夾 1 將會有下列結構: <p>Folder1<br/>(& s) nbsp; & nbsp; & nbsp; (& s) nbsp;1 + 2 + File3 + File4 + 5 檔案內容會合併成一個檔案，以自動產生的檔案名稱</p>
false | preserveHierarchy | <p>來源資料夾 Folder1 具有下列結構:</p> <p>Folder1<br/>(& s) nbsp; & nbsp; & nbsp; (& s) nbsp;File1<br/>(& s) nbsp; & nbsp; & nbsp; (& s) nbsp;2<br/>(& s) nbsp; & nbsp; & nbsp; (& s) nbsp;Subfolder1<br/>(& s) nbsp; (& s) nbsp; & nbsp; (& s) nbsp; (& s) nbsp; (& s) nbsp; (& s) nbsp; (& s) nbsp;File3<br/>(& s) nbsp; (& s) nbsp; & nbsp; (& s) nbsp; (& s) nbsp; (& s) nbsp; (& s) nbsp; (& s) nbsp;File4<br/>(& s) nbsp; (& s) nbsp; & nbsp; (& s) nbsp; (& s) nbsp; (& s) nbsp; (& s) nbsp; (& s) nbsp;File5</p>目標資料夾 Folder1 會有下列結構<p>Folder1<br/>(& s) nbsp; & nbsp; & nbsp; (& s) nbsp;File1<br/>(& s) nbsp; & nbsp; & nbsp; (& s) nbsp;2<br/></p><p>Subfolder1 File3、 File4，與 File5 不會收取。</p>.
false | flattenHierarchy | <p>來源資料夾 Folder1 具有下列結構:</p> <p>Folder1<br/>(& s) nbsp; & nbsp; & nbsp; (& s) nbsp;File1<br/>(& s) nbsp; & nbsp; & nbsp; (& s) nbsp;2<br/>(& s) nbsp; & nbsp; & nbsp; (& s) nbsp;Subfolder1<br/>(& s) nbsp; (& s) nbsp; & nbsp; (& s) nbsp; (& s) nbsp; (& s) nbsp; (& s) nbsp; (& s) nbsp;File3<br/>(& s) nbsp; (& s) nbsp; & nbsp; (& s) nbsp; (& s) nbsp; (& s) nbsp; (& s) nbsp; (& s) nbsp;File4<br/>(& s) nbsp; (& s) nbsp; & nbsp; (& s) nbsp; (& s) nbsp; (& s) nbsp; (& s) nbsp; (& s) nbsp;File5</p>目標資料夾 Folder1 會有下列結構<p>Folder1<br/>(& s) nbsp; & nbsp; & nbsp; (& s) nbsp; 自動產生名稱 File1<br/>(& s) nbsp; (& s) nbsp; (& s) nbsp; & nbsp; File2 如自動產生的名稱<br/></p><p>Subfolder1 File3、 File4，與 File5 不會收取。</p>.
false | mergeFiles | <p>來源資料夾 Folder1 具有下列結構:</p> <p>Folder1<br/>(& s) nbsp; & nbsp; & nbsp; (& s) nbsp;File1<br/>(& s) nbsp; & nbsp; & nbsp; (& s) nbsp;2<br/>(& s) nbsp; & nbsp; & nbsp; (& s) nbsp;Subfolder1<br/>(& s) nbsp; (& s) nbsp; & nbsp; (& s) nbsp; (& s) nbsp; (& s) nbsp; (& s) nbsp; (& s) nbsp;File3<br/>(& s) nbsp; (& s) nbsp; & nbsp; (& s) nbsp; (& s) nbsp; (& s) nbsp; (& s) nbsp; (& s) nbsp;File4<br/>(& s) nbsp; (& s) nbsp; & nbsp; (& s) nbsp; (& s) nbsp; (& s) nbsp; (& s) nbsp; (& s) nbsp;File5</p>目標資料夾 Folder1 會有下列結構<p>Folder1<br/>(& s) nbsp; & nbsp; & nbsp; (& s) nbsp;1 + 2 的內容會合併成一個檔案，以自動產生的檔案名稱。 自動產生名稱 File1</p><p>Subfolder1 File3、 File4，與 File5 不會收取。</p>.

  


[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

[AZURE.INCLUDE [data-factory-type-conversion-sample](../../includes/data-factory-type-conversion-sample.md)]

[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]




