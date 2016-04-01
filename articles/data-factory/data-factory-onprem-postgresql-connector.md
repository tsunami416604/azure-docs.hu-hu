<properties 
    pageTitle="從 PostgreSQL 移動資料 | Azure Data Factory" 
    description="了解如何使用 Azure Data Factory 從 PostgreSQL 資料庫移動資料。" 
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

# 使用 Azure Data Factory 從 PostgreSQL 移動資料

本文將概述如何使用 Azure 資料處理站中的複製活動將資料從 PostgreSQL 移動到另一個資料存放區。 這篇文章是根據 [資料移動活動](data-factory-data-movement-activities.md) 一文，該文呈現使用複製活動，而且支援的資料存放區組合的資料移動的一般概觀。

Data Factory 服務支援使用資料管理閘道器連接至內部部署 PostgreSQL 來源。 請參閱 [在內部部署位置與雲端之間移動資料](data-factory-move-data-between-onprem-and-cloud.md) 文章來了解資料管理閘道器和設定閘道器的逐步指示。 

**注意 ︰** 您需要運用閘道器連接到 PostgreSQL，即使裝載於 Azure IaaS Vm 中。 如果您正嘗試連接到裝載於雲端中的 PostgreSQL 執行個體，您也可以在 IaaS VM 中安裝閘道器執行個體。

資料處理站只支援將資料從 PostgreSQL 移至其他資料存放區，而不支援從其他資料存放區移至 PostgreSQL。

## 安裝 

資料管理閘道器連接到 PostgreSQL 資料庫，您需要安裝 [PostgreSQL 的 Ngpsql 資料提供者](http://go.microsoft.com/fwlink/?linkid=282716) 與資料管理閘道器相同的系統上。

## 範例：從 PostgreSQL 複製資料到 Azure Blob

下列範例顯示：

1.  連結的服務型別的 [OnPremisesPostgreSql](data-factory-onprem-postgresql-connector.md#postgresql-linked-service-properties)。
2.  連結的服務型別的 [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties)。
3.  輸入 [資料集](data-factory-create-datasets.md) 型別的 [RelationalTable](data-factory-onprem-postgresql-connector.md#postgresql-dataset-type-properties)。
4.  輸出 [資料集](data-factory-create-datasets.md) 型別的 [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties)。
4.   [管線](data-factory-create-pipelines.md) 具有複製活動使用 [RelationalSource](data-factory-onprem-postgresql-connector.md#postgresql-copy-activity-type-properties) 和 [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties)。 

此範例會每個小時將資料從 PostgreSQL 資料庫中的查詢結果複製到 Blob。 範例後面的各節會說明這些範例中使用的 JSON 屬性。 

第一個步驟中，設定資料管理閘道器中的指示 [在內部部署位置與雲端之間移動資料](data-factory-move-data-between-onprem-and-cloud.md) 文件。

**PostgreSQL 連結服務：**

    {
        "name": "OnPremPostgreSqlLinkedService",
        "properties": {
            "type": "OnPremisesPostgreSql",
            "typeProperties": {
                "server": "<server>",
                "database": "<database>",
                "schema": "<schema>",
                "authenticationType": "<authentication type>",
                "username": "<username>",
                "password": "<password>",
                "gatewayName": "<gatewayName>"
            }
        }
    }

**Azure Blob 儲存體連結服務：**
        
    {
      "name": "AzureStorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=<AccountName>;AccountKey=<AccountKey>"
        }
      }
    }

**PostgreSQL 輸入資料集：**

此範例假設您已在 PostgreSQL 中建立資料表 "MyTable"，其中包含時間序列資料的資料行 (名稱為 "timestamp")。

設定 “external”: true 和指定 externalData 原則即可通知 Azure Data Factory：這是 Data Factory 外部的資料表而且不是由 Data Factory 中的活動所產生。

    {
        "name": "PostgreSqlDataSet",
        "properties": {
            "type": "RelationalTable",
            "linkedServiceName": "OnPremPostgreSqlLinkedService",
            "typeProperties": {},
            "availability": {
                "frequency": "Hour",
                "interval": 1
            },
            "external": true,
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
        "name": "AzureBlobPostgreSqlDataSet",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "folderPath": "mycontainer/postgresql/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
                "format": {
                    "type": "TextFormat",
                    "rowDelimiter": "\n",
                    "columnDelimiter": "\t"
                },
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
                ]
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            }
        }
    }


**複製活動：**

此管線包含複製活動，該活動已設定為使用上述輸入和輸出資料集並排定為每小時執行。 在管線 JSON 定義 **來源** 類型設為 **RelationalSource** 和 **接收器** 類型設為 **BlobSink**。 針對指定的 SQL 查詢 **查詢** 屬性從 PostgreSQL 資料庫中的公用 usstates 資料表選取資料。
    
    {
        "name": "CopyPostgreSqlToBlob",
        "properties": {
            "description": "pipeline for copy activity",
            "activities": [
                {
                    "type": "Copy",
                    "typeProperties": {
                        "source": {
                            "type": "RelationalSource",
                            "query": "select * from public.usstates"
                        },
                        "sink": {
                            "type": "BlobSink"
                        }
                    },
                    "inputs": [
                        {
                            "name": "PostgreSqlDataSet"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "AzureBlobPostgreSqlDataSet"
                        }
                    ],
                    "policy": {
                        "timeout": "01:00:00",
                        "concurrency": 1
                    },
                    "scheduler": {
                        "frequency": "Hour",
                        "interval": 1
                    },
                    "name": "PostgreSqlToBlob"
                }
            ],
            "start": "2014-06-01T18:00:00Z",
            "end": "2014-06-01T19:00:00Z"
        }
    }


## PostgreSQL 連結服務屬性

下表提供 PostgreSQL 連結服務專屬 JSON 元素的描述。

屬性 | 說明 | 必要
-------- | ----------- | --------
類型 | Type 屬性必須設定為 ︰ **OnPremisesPostgreSql** | 是
伺服器 | PostgreSQL 伺服器的名稱。 | 是 
資料庫 | PostgreSQL 資料庫的名稱。 | 是 
結構描述 | 在資料庫中的結構描述名稱。 | 否 
authenticationType | 用來連接到 PostgreSQL 資料庫的驗證類型。 可能的值為：匿名、基本和 Windows。 | 是 
username | 如果您使用基本或 Windows 驗證，請指定使用者名稱。 | 否 
password | 指定您為使用者名稱所指定之使用者帳戶的密碼。 | 否 
gatewayName | Data Factory 服務應該用來連接到內部部署 PostgreSQL 資料庫的閘道器名稱。 | 是 

請參閱 [設定認證和安全性](data-factory-move-data-between-onprem-and-cloud.md#setting-credentials-and-security) 如需詳細資訊設定內部部署 PostgreSQL 資料來源的認證。

## PostgreSQL 資料集類型屬性

如需區段和屬性可用來定義資料集的完整清單，請參閱 [建立資料集](data-factory-create-datasets.md) 文件。 資料集 JSON 的結構、可用性和原則等區段類似於所有的資料集類型 (SQL Azure、Azure Blob、Azure 資料表等)。

每個資料集類型的 typeProperties 區段都不同，可提供資料存放區中資料的位置相關資訊。 類型的資料集的 typeProperties 區段 **RelationalTable** （包含 PostgreSQL 資料集） 具有下列屬性。

屬性 | 說明 | 必要
-------- | ----------- | --------
tableName | PostgreSQL 資料庫執行個體中連結服務所參照的資料表名稱。 | 否 (如果 **查詢** 的 **RelationalSource** 指定) 

## PostgreSQL 複製活動類型屬性

如需區段和屬性可用來定義活動的完整清單，請參閱 [建立管線](data-factory-create-pipelines.md) 文件。 名稱、描述、輸入和輸出資料表、各種原則等屬性都適用於所有活動類型。 

另一方面，活動的 typeProperties 區段中可用的屬性會隨著每個活動類型而有所不同，而在複製活動的案例中，可用的屬性會根據來源與接收的類型而有所不同。

在複製活動類型的來源時 **RelationalSource** （包含 PostgreSQL） 在 typeProperties 區段中會提供下列屬性 ︰

屬性 | 說明 | 允許的值 | 必要
-------- | ----------- | -------------- | --------
query | 使用自訂查詢來讀取資料。 | SQL 查詢字串。 例如：select * from MyTable。 | 否 (如果 **tableName** 的 **資料集** 指定)

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

## PostgreSQL 的類型對應

中所述 [資料移動活動](data-factory-data-movement-activities.md) 文章複製活動會執行轉換成接收類型具有下列 2 個步驟的方法的來源類型的自動類型轉換的自動類型轉換 ︰

1. 從原生來源類型轉換成 .NET 類型
1. 從 .NET 類型轉換成原生接收類型

將資料移到 PostgreSQL 時，下列對應將用於從 PostgreSQL 類型.NET 類型。

PostgreSQL 資料庫類型 |  PostgresSQL 別名 | .NET Framework 類型
------------------------ | -------------------- | ---------------------
abstime |  | Datetime
bigint | int8 | Int64
bigserial | serial8 | Int64
位元 [ (n) ] |  | Byte [] 中字串
位元不同 [ (n) ] | varbit | Byte [] 中字串
布林值 | 布林 | Boolean
方塊 |  | Byte [] 中字串
bytea |  | Byte [] 中字串
字元 [ (n) ] | char [ (n) ] | String
字元不同 [ (n) ] | varchar [ (n) ] | String
cid |  | String
cidr | | String
圓形 | | Byte [] 中字串
日期 | | Datetime
daterange | | String
雙精度| float8 | 兩倍
inet | | Byte [] 中字串
intarry | | String
int4range | | String
int8range | | String
integer | int, int4 | Int32
間隔 [ 欄位 ] [ (p) ] | | Timespan
json | | String
jsonb | | Byte]
線條 | | Byte [] 中字串
lseg | | Byte [] 中字串
macaddr | | Byte [] 中字串
money | | 十進位
數值 [ (p, s) ] | 十進位 [ (p, s) ] | 十進位
numrange | | String
oid | | Int32
路徑 | | Byte [] 中字串
pg_lsn | | Int64
點 | | Byte [] 中字串
多邊形 | | Byte [] 中字串
real | float4 | 單一
smallint | int2 | Int16
smallserial | serial2 | Int16
serial | serial4 | Int32
文字 | | String


[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

[AZURE.INCLUDE [data-factory-type-repeatability-for-relational-sources](../../includes/data-factory-type-repeatability-for-relational-sources.md)]


