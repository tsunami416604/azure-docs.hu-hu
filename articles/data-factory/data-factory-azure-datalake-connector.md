<properties
    pageTitle="從 Azure 資料湖存放區來回移動資料 | Azure Data Factory"
    description="了解如何使用 Azure Data Factory 從 Azure 資料湖存放區來回移動資料"
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
    ms.date="11/09/2015"
    ms.author="spelluru"/>

# 使用 Azure Data Factory 從 Azure 資料湖存放區來回移動資料
本文概述如何在 Azure Data Factory 中透過複製活動，在 Azure 資料湖存放區與其他資料存放區之間移動資料。 這篇文章是根據 [資料移動活動](data-factory-data-movement-activities.md) 一文，該文呈現使用複製活動，而且支援的資料存放區組合的資料移動的一般概觀。

> [AZURE.NOTE]
> 您必須先建立 Azure 資料湖存放區帳戶，才能透過複製活動建立管線，以在 Azure 資料湖存放區中移入/移出資料。 若要深入了解 Azure 資料湖存放區，請參閱 [開始使用 Azure 資料湖市集](../data-lake-store/data-lake-store-get-started-portal.md)。
>  
> 請檢閱 [建置您的第一個管線教學課程](data-factory-build-your-first-pipeline.md) 的詳細步驟可建立資料處理站，如需連結服務、 資料集和管線。 搭配使用 JSON 片段和 Data Factory 編輯器或 Visual Studio 或 Azure PowerShell 來建立 Data Factory 實體。

## 範例：從 Azure Blob 複製資料到 Azure 資料湖存放區
下列範例顯示：

1.  連結的服務型別的 [AzureStorage](#azure-storage-linked-service-properties)。
2.  連結的服務型別的 [AzureDataLakeStore](#azure-data-lake-linked-service-properties)。
3.  輸入 [資料集](data-factory-create-datasets.md) 型別的 [AzureBlob](#azure-blob-dataset-type-properties)。
4.  輸出 [資料集](data-factory-create-datasets.md) 型別的 [AzureDataLakeStore](#azure-data-lake-dataset-type-properties)。
4.  A [管線](data-factory-create-pipelines.md) 具有使用 「 複製活動 [BlobSource](#azure-blob-copy-activity-type-properties) 和 [AzureDataLakeStoreSink](#azure-data-lake-copy-activity-type-properties)。

這個範例會複製資料屬於時間序列從 Azure Blob 儲存體到 Azure 資料湖存放區的每個小時。 範例後面的各節會說明這些範例中使用的 JSON 屬性。


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

**Azure 資料湖連結服務：**

    {
        "name": "AzureDataLakeStoreLinkedService",
        "properties": {
            "type": "AzureDataLakeStore",
            "typeProperties": {
                "dataLakeUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
                "sessionId": "<session ID>",
                "authorization": "<authorization URL>"
            }
        }
    }

### 使用 Data Factory 編輯器建立 Azure 資料湖連結服務
下列程序提供使用 Data Factory 編輯器建立 Azure 資料湖存放區連結服務的步驟。

1. 按一下 [ **新增資料存放區** 命令列，然後選取 **Azure 資料湖市集**。
2. 在 JSON 編輯器中，如 **datalakeUri** 屬性中，輸入資料湖的 URI。
3. 按一下 [ **授權** 命令列上的按鈕。 應該會出現一個快顯視窗。

    ![授權按鈕](./media/data-factory-azure-data-lake-connector/authorize-button.png)
4. 使用您的認證來登入和 **授權** 在 JSON 中的屬性應該被指派的值現在。
5. （選擇性）指定選擇性參數的值，例如 **accountName**, ，**subscriptionID** 和 **resourceGroupName** 在 JSON 中 （或） 從 JSON 刪除這些屬性。
6. 按一下 [ **部署** 命令列來部署連結的服務。


**Azure Blob 輸入資料集：**

每小時從新的 Blob 挑選資料 (頻率：小時，間隔：1)。 根據正在處理之配量的開始時間，以動態方式評估 Blob 的資料夾路徑和檔案名稱。 年、 月和日的開始時間的部分，會使用的資料夾路徑和檔案名稱使用開始時間的小時部分。 "external":"true"設定會通知 Data Factory 服務，此資料表是 data factory 外部的並不是由 data factory 中的活動所產生。

    {
      "name": "AzureBlobInput",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "Path": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}",
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
                "format": "MM"
              }
            },
            {
              "name": "Day",
              "value": {
                "type": "DateTime",
                "date": "SliceStart",
                "format": "dd"
              }
            },
            {
              "name": "Hour",
              "value": {
                "type": "DateTime",
                "date": "SliceStart",
                "format": "HH"
              }
            }
          ]
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


**Azure 資料湖輸出資料集：**

此範例會將資料複製到 Azure 資料湖存放區。 每個小時會將新資料複製到資料湖存放區。

    {
        "name": "AzureDataLakeStoreOutput",
        "properties": {
            "type": "AzureDataLakeStore",
            "linkedServiceName": " AzureDataLakeStoreLinkedService",
            "typeProperties": {
                "folderPath": "datalake/output/"
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            }
        }
    }



**具有複製活動的管線：**

此管線包含複製活動，該活動已設定為使用上述輸入和輸出資料集並排定為每小時執行。 在管線 JSON 定義 **來源** 類型設為 **BlobSource** 和 **接收器** 類型設為 **AzureDataLakeStoreSink**。

    {  
        "name":"SamplePipeline",
        "properties":
        {  
            "start":"2014-06-01T18:00:00",
            "end":"2014-06-01T19:00:00",
            "description":"pipeline with copy activity",
            "activities":
            [  
                {
                    "name": "AzureBlobtoDataLake",
                    "description": "Copy Activity",
                    "type": "Copy",
                    "inputs": [
                    {
                        "name": "AzureBlobInput"
                    }
                    ],
                    "outputs": [
                    {
                        "name": "AzureDataLakeStoreOutput"
                    }
                    ],
                    "typeProperties": {
                        "source": {
                            "type": "BlobSource",
                            "treatEmptyAsNull": true,
                            "blobColumnSeparators": ","
                        },
                        "sink": {
                            "type": "AzureDataLakeStoreSink"
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

## 範例：將資料從 Azure 資料湖存放區複製到 Azure Blob
下列範例顯示：

1.  連結的服務型別的 [AzureDataLakeStore](#azure-data-lake-linked-service-properties)。
2.  連結的服務型別的 [AzureStorage](#azure-storage-linked-service-properties)。
3.  輸入 [資料集](data-factory-create-datasets.md) 型別的 [AzureDataLakeStore](#azure-data-lake-dataset-type-properties)。
4.  輸出 [資料集](data-factory-create-datasets.md) 型別的 [AzureBlob](#azure-blob-dataset-type-properties)。
5.  A [管線](data-factory-create-pipelines.md) 具有使用 「 複製活動 [AzureDataLakeStoreSource](#azure-data-lake-copy-activity-type-properties) 和 [BlobSink](#azure-blob-copy-activity-type-properties)

此範例每小時會將屬於時間序列的資料從 Azure 資料湖存放區複製到 Azure Blob。 範例後面的各節會說明這些範例中使用的 JSON 屬性。

**Azure 資料湖存放區連結服務：**

    {
        "name": "AzureDataLakeStoreLinkedService",
        "properties": {
            "type": "AzureDataLakeStore",
            "typeProperties": {
                "dataLakeUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
                "sessionId": "<session ID>",
                "authorization": "<authorization URL>"
            }
        }
    }

> [AZURE.NOTE] 請參閱先前的範例，以取得授權 URL 中的步驟。  

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

**Azure 資料湖輸入資料集：**

設定 **"external": true** 並指定 **externalData** 原則即可通知 Azure Data Factory 服務，這是在 data factory 外部的且不是由 data factory 中的活動所產生的資料表。

    {
        "name": "AzureDataLakeStoreInput",
        "properties":
        {
            "type": "AzureDataLakeStore",
            "linkedServiceName": "AzureDataLakeStoreLinkedService",
            "typeProperties": {
                "folderPath": "datalake/input/",
                "fileName": "SearchLog.tsv",
                "format": {
                    "type": "TextFormat",
                    "rowDelimiter": "\n",
                    "columnDelimiter": "\t"
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
                "format": "MM"
              }
            },
            {
              "name": "Day",
              "value": {
                "type": "DateTime",
                "date": "SliceStart",
                "format": "dd"
              }
            },
            {
              "name": "Hour",
              "value": {
                "type": "DateTime",
                "date": "SliceStart",
                "format": "HH"
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

此管線包含複製活動，該活動已設定為使用上述輸入和輸出資料集並排定為每小時執行。 在管線 JSON 定義 **來源** 類型設為 **AzureDataLakeStoreSource** 和 **接收器** 類型設為 **BlobSink**。


    {  
        "name":"SamplePipeline",
        "properties":{  
            "start":"2014-06-01T18:00:00",
            "end":"2014-06-01T19:00:00",
            "description":"pipeline for copy activity",
            "activities":[  
                {
                    "name": "AzureDakeLaketoBlob",
                    "description": "copy activity",
                    "type": "Copy",
                    "inputs": [
                      {
                        "name": "AzureDataLakeStoreInput"
                      }
                    ],
                    "outputs": [
                      {
                        "name": "AzureBlobOutput"
                      }
                    ],
                    "typeProperties": {
                        "source": {
                            "type": "AzureDataLakeStoreSource",
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


## Azure 資料湖存放區連結服務屬性

您可以使用 Azure 儲存體連結服務，將 Azure 儲存體帳戶連結至 Azure Data Factory。 下表提供 Azure 儲存體連結服務專屬 JSON 元素的描述。

| 屬性 | 說明 | 必要 |
| :-------- | :----------- | :-------- |
| 類型 | Type 屬性必須設定為 ︰ **AzureDataLakeStore** | 是 |
| dataLakeUri | 指定有關 Azure 資料湖存放區帳戶的資訊。 它是以下列格式 ︰ https://<Azure Data Lake account name>.azuredatalakestore.net/webhdfs/v1 | 是 |
| authorization | 按一下 [ **授權** 按鈕 **Data Factory 編輯器** 並輸入您的認證，這會自動產生的授權 URL 指派給這個屬性。  | 是 |
| sessionId | OAuth 工作階段的 OAuth 工作階段識別碼。 每個工作階段識別碼都是獨一無二的，只能使用一次。 當您使用 Data Factory 編輯器時便會自動產生。 | 是 |  
| accountName | 資料湖帳戶名稱 | 否 |
| subscriptionId | Azure 訂用帳戶識別碼。 | 否 (如果未指定，便會使用 Data Factory 的訂用帳戶)。 |
| resourceGroupName |  Azure 資源群組名稱 | 否 (若未指定，便會使用 Data Factory 的資源群組)。 |


## Azure 資料湖資料集類型屬性

如需 JSON 區段和屬性可用來定義資料集的完整清單，請參閱 [建立資料集](data-factory-create-datasets.md) 文件。 資料集 JSON 的結構、可用性和原則等區段類似於所有的資料集類型 (SQL Azure、Azure Blob、Azure 資料表等)。

 **TypeProperties** 區段都不同的資料集的每一種，提供位置相關資訊等資料存放區中的資料格式。 類型的資料集的 typeProperties 區段 **AzureDataLakeStore** 資料集具有下列屬性。

| 屬性 | 說明 | 必要 |
| :-------- | :----------- | :-------- |
| folderPath | Azure 資料湖存放區中容器與資料夾的路徑。 | 是 |
| fileName | <p>在 Azure 資料湖存放區中的檔案名稱。 檔案名稱是選擇性的。 </p><p>如果您指定檔案名稱，活動 （包括複製） 適用於特定的檔案。</p><p>未指定 fileName 時，複製會將輸入資料集的 folderPath 包含所有檔案。</p><p>檔案名稱未指定輸出資料集，所產生檔案的名稱會在下列這種格式 ︰ 資料。<Guid>.txt (例如:: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt</p> | 否 |
| partitionedBy | partitionedBy 是選擇性的屬性。 您可以用來指定時間序列資料的動態 folderPath 和 filename。 例如，folderPath 可針對每小時的資料進行參數化。 如需詳細資訊和範例，請參閱下面的＜運用 partitionedBy 屬性＞一節。 | 否 |
| format | 支援兩種格式類型 ︰ **TextFormat**, ，**AvroFormat**。 您需要將格式底下的 type 屬性設定為這些值。 如果格式為 TextFormat，您可以指定格式的其他選擇性屬性。 請參閱 [指定 TextFormat](#specifying-textformat) 下面章節以取得詳細資料。 | 否 |
| compression | 指定此資料的壓縮類型和層級。 支援的類型為：GZip、Deflate 和 BZip2，而支援的層級為：最佳和最快。 請參閱 [壓縮支援](#compression-support) 一節以取得詳細資料。  | 否 |

### 運用 partitionedBy 屬性
如上所述，您可以指定的動態 folderPath 和 filename 時間序列資料 **partitionedBy** 區段、 Data Factory 巨集和系統變數 ︰ SliceStart 和 SliceEnd，表示指定的資料配量的開始和結束時間。

請參閱 [建立資料集](data-factory-create-datasets.md) 和 [排程和執行](data-factory-scheduling-and-execution.md) 文章，以了解更多詳細資料的時間序列資料集、 排程和配量。

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
| escapeChar | <p>用來逸出內容中顯示之資料行分隔符號的特殊字元。 此標記是選擇性的。 沒有預設值。 您必須指定這個屬性不能超過一個字元。</p><p>比方說，如果您以逗號 （，） 做為資料行分隔符號，但您想要有逗號字元的文字 (範例:"Hello，world")，您可以定義 '$' 做為逸出字元，並使用字串"Hello$，world"中的來源。</p><p>請注意，您無法指定 escapeChar 和 quoteChar 資料表。</p> | 否 | 
| quoteChar | <p>用來引用字串值的特殊字元。 引號字元內的資料行和資料列分隔符號會被視為字串值的一部分。 此標記是選擇性的。 沒有預設值。 您必須指定這個屬性不能超過一個字元。</p><p>比方說，如果您以逗號 （，） 做為資料行分隔符號，但您想要有逗號字元的文字 (範例 ︰ < Hello，world >)，您可以定義 '"' 做為引用字元並在來源中使用字串 <"Hello，world">。 這個屬性不適用於輸入和輸出資料表。</p><p>請注意，您無法指定 escapeChar 和 quoteChar 資料表。</p> | 否 |
| nullValue | <p>用來代表 Blob 檔案內容中 null 值的字元。 此標記是選擇性的。 預設值為"\N"。</p><p>例如，根據上述範例，blob 中的"NaN"會轉換成 null 值複製到 SQL Server 時。</p> | 否 |
| encodingName | 指定編碼名稱。 如需有效編碼名稱的清單，請參閱 ︰ [Encoding.EncodingName 屬性](https://msdn.microsoft.com/library/system.text.encoding.aspx)。 例如：windows-1250 或 shift_jis。 預設值為 UTF-8。 | 否 | 

#### 範例
下列範例顯示 TextFormat 的一些格式屬性。

    "typeProperties":
    {
        "folderPath": "mycontainer/myfolder",
        "fileName": "myfilename"
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


### 壓縮支援  
處理大型資料集可能會導致 I/O 和網路瓶頸。 因此，存放區中的壓縮資料不但可以跨網路加速資料傳輸和節省磁碟空間，也能在處理巨量資料時帶來顯著的效能提升。 在此階段中，Azure Blob 或內部部署檔案系統等以檔案為基礎的資料存放區支援壓縮。  

若要指定資料集的壓縮，請使用 **壓縮** 屬性在資料集 JSON，如下列範例所示 ︰   

    {  
        "name": "AzureDatalakeStoreDataSet",  
        "properties": {  
            "availability": {  
                "frequency": "Day",  
                "interval": 1  
            },  
            "type": "AzureDatalakeStore",  
            "linkedServiceName": "DataLakeStoreLinkedService",  
            "typeProperties": {  
                "fileName": "pagecounts.csv.gz",  
                "folderPath": "compression/file/",  
                "compression": {  
                    "type": "GZip",  
                    "level": "Optimal"  
                }  
            }  
        }  
    }  
 
請注意， **壓縮** 區段具有兩個屬性 ︰  
  
- **類型 ︰** 的壓縮轉碼器，它可以是 **GZIP**, ，**Deflate** 或 **BZIP2**。  
- **層級 ︰** 壓縮比，它可以是 **最佳** 或 **最快**。 
    - **最快速 ︰** 壓縮作業應該儘速完成，即使未以最佳方式壓縮所產生的檔案。 
    - **最佳**︰ 壓縮作業應該以最佳方式壓縮，即使該作業耗費很長的時間才能完成。 
    
    請參閱 [壓縮層級](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx) 主題取得詳細資訊。 

假設上述範例資料集做為複製活動的輸出，複製活動會透過使用最佳比率的 GZIP 轉碼器壓縮初輸資料，然後將壓縮的資料寫入到 Azure 資料湖存放區中名為 pagecounts.csv.gz 的檔案。   

當您在輸入資料集 JSON 中指定壓縮屬性，管線可以從來源讀取壓縮的資料，當您在輸出資料集 JSON 中指定屬性，複製活動可以將壓縮的資料寫入到目的地。 以下是一些範例案例： 

- 從 Azure 資料湖存放區讀取 GZIP 壓縮資料，將其解壓縮，並將結果資料寫入到 Azure SQL Database。 您可以在此情況下利用壓縮 JSON 屬性定義輸入 Azure 資料湖存放區。 
- 從來自內部部署檔案系統之純文字檔案讀取資料、使用 GZip 格式加以壓縮並將壓縮的資料寫入到 Azure 資料湖存放區。 您可以在此情況下利用壓縮 JSON 屬性定義輸出 Azure 資料湖資料集。  
- 從 Azure 資料湖存放區讀取 GZIP 壓縮資料，將其解壓縮、使用 BZIP2 將其壓縮，並將結果資料寫入到 Azure 資料湖存放區。 您會在此情況下利用設為 GZIP 的壓縮類型定義輸入 Azure 資料湖存放區資料集，並利用設為 BZIP2 的壓縮類型定義輸出資料集。   


## Azure 資料湖複製活動類型屬性  
如需區段和屬性可用來定義活動的完整清單，請參閱 [建立管線](data-factory-create-pipelines.md) 文件。 名稱、描述、輸入和輸出資料表、各種原則等屬性都適用於所有活動類型。

另一方面，活動的 typeProperties 區段中可用的屬性會隨著每個活動類型而有所不同，而在複製活動的案例中，可用的屬性會根據來源與接收的類型而有所不同。

**AzureDataLakeStoreSource** 支援下列屬性 **typeProperties** 區段 ︰

| 屬性 | 說明 | 允許的值 | 必要 |
| -------- | ----------- | -------------- | -------- |
| 遞迴 | 表示是否從子資料夾，或只有從指定的資料夾，以遞迴方式讀取資料。 | True (預設值)、False | 否 |



**AzureDataLakeStoreSink** 支援下列屬性 **typeProperties** 區段 ︰

| 屬性 | 說明 | 允許的值 | 必要 |
| -------- | ----------- | -------------- | -------- |
| copyBehavior | 指定複製行為。 | <p>**PreserveHierarchy:** ︰ 保留目標資料夾，亦即，來源資料夾的原始程式檔的相對路徑中的檔案階層完全相同的目標資料夾的目標檔案相對路徑。</p><p>**FlattenHierarchy:** 從來源資料夾的所有檔案會都出現在第一層的目標資料夾。 目標檔案會有自動產生的名稱。</p><p>**MergeFiles:** （稍後即將這項功能） 合併為一個檔案的來源資料夾的所有檔案。 如果已指定檔案/Blob 名稱，合併檔案名稱會是指定的名稱；否則，就會是自動產生的檔案名稱。</p> | 否 |


[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

[AZURE.INCLUDE [data-factory-type-conversion-sample](../../includes/data-factory-type-conversion-sample.md)]

[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]


