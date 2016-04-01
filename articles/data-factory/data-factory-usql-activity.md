<properties 
    pageTitle="從 Azure Data Factory 在 Azure 資料湖分析上執行 U-SQL 指令碼" 
    description="了解如何在 Azure 資料湖分析計算服務上執行 U-SQL 指令碼來處理資料。" 
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
    ms.date="10/27/2015" 
    ms.author="spelluru"/>

# 從 Azure Data Factory 在 Azure 資料湖分析上執行 U-SQL 指令碼 
Azure Data Factory 中的「管線」會使用連結的計算服務，來處理連結的儲存體服務中的資料。 它包含一系列活動，其中每個活動都會執行特定的處理作業。 本文說明 **資料湖分析 U SQL 活動** 執行  **U SQL** 指令碼上 **Azure 資料湖分析** 計算連結的服務。 

> [AZURE.NOTE] 
> 您必須先建立 Azure 資料湖分析帳戶，才能使用資料湖分析 U-SQL 活動建立管線。 若要深入了解 Azure 資料湖分析，請參閱 [開始使用 Azure 資料湖分析](../data-lake-analytics/data-lake-analytics-get-started-portal.md)。
>  
> 請檢閱 [建置您的第一個管線教學課程](data-factory-build-your-first-pipeline.md) 的詳細步驟可建立資料處理站，如需連結服務、 資料集和管線。 搭配使用 JSON 片段和 Data Factory 編輯器或 Visual Studio 或 Azure PowerShell 來建立 Data Factory 實體。

## Azure 資料湖分析連結服務
您建立 **Azure 資料湖分析** 連結的服務，將連結的 Azure 資料湖分析計算服務的 Azure data factory 管線中使用資料湖分析 U SQL 活動之前。 

下列範例提供 Azure 資料湖分析連結服務的 JSON 定義。 

    {
        "name": "AzureDataLakeAnalyticsLinkedService",
        "properties": {
            "type": "AzureDataLakeAnalytics",
            "typeProperties": {
                "accountName": "adftestaccount",
                "dataLakeAnalyticsUri": "datalakeanalyticscompute.net",
                "authorization": "<authcode>",
                "sessionId": "<session ID>", 
                "subscriptionId": "<subscription id>",
                "resourceGroupName": "<resource group name>"
            }
        }
    }


下表提供 JSON 定義中所使用之屬性的描述： 

屬性 | 說明 | 必要
-------- | ----------- | --------
類型 | Type 屬性應該設定為 ︰ **AzureDataLakeAnalytics**。 | 是
accountName | Azure 資料湖分析帳戶名稱。 | 是
dataLakeAnalyticsUri | Azure 資料湖分析 URI。 |  否 
authorization | 按一下之後，就會自動擷取授權碼 **授權** 按鈕在 Data Factory 編輯器中，並完成 OAuth 登入。 | 是 
subscriptionId | Azure 訂用帳戶識別碼 | 否 (如果未指定，便會使用 Data Factory 的訂用帳戶)。 
resourceGroupName | Azure 資源群組名稱 |  否 (若未指定，便會使用 Data Factory 的資源群組)。
sessionId | OAuth 授權工作階段的工作階段識別碼。 每個工作階段識別碼都是唯一的，只能使用一次。 這是在 Data Factory 編輯器中自動產生。 | 是

   
 
## 資料湖分析 U-SQL 活動 

下列 JSON 片段會定義具有資料湖分析 U-SQL 活動的管線。 活動定義具有您稍早建立的 Azure 資料湖分析連結服務的參考。   
  

    {
        "name": "ComputeEventsByRegionPipeline",
        "properties": {
            "description": "This is a pipeline to compute events for en-gb locale and date less than 2012/02/19.",
            "activities": 
            [
                {
                    "type": "DataLakeAnalyticsU-SQL",
                    "typeProperties": {
                        "scriptPath": "scripts\\kona\\SearchLogProcessing.txt",
                        "scriptLinkedService": "StorageLinkedService",
                        "degreeOfParallelism": 3,
                        "priority": 100,
                        "parameters": {
                            "in": "/datalake/input/SearchLog.tsv",
                            "out": "/datalake/output/Result.tsv"
                        }
                    },
                    "inputs": [
                        {
                            "name": "DataLakeTable"
                        }
                    ],
                    "outputs": 
                    [
                        {
                            "name": "EventsByRegionTable"
                        }
                    ],
                    "policy": {
                        "timeout": "06:00:00",
                        "concurrency": 1,
                        "executionPriorityOrder": "NewestFirst",
                        "retry": 1
                    },
                    "scheduler": {
                        "frequency": "Day",
                        "interval": 1
                    },
                    "name": "EventsByRegion",
                    "linkedServiceName": "AzureDataLakeAnalyticsLinkedService"
                }
            ],
            "start": "2015-08-08T00:00:00Z",
            "end": "2015-08-08T01:00:00Z",
            "isPaused": false
        }
    }


下表描述此活動特有的屬性之名稱和描述。 

屬性 | 說明 | 必要
:-------- | :----------- | :--------
類型 | Type 屬性必須設定為 **DataLakeAnalyticsU SQL**。 | 是
scriptPath | 包含 U-SQL 指令碼的資料夾的路徑。 | 否 (如果您使用指令碼)
scriptLinkedService | 連結服務會連結包含 Data Factory 的指令碼的儲存體 | 否 (如果您使用指令碼)
script | 指定內嵌指令碼而不是指定 scriptPath 和 scriptLinkedService。 例如："script" : "CREATE DATABASE test"。 | 否 (如果您使用 scriptPath 和 scriptLinkedService)
degreeOfParallelism | 同時用來執行工作的節點數目上限。 | 否
優先順序 | 判斷應該選取排入佇列的哪些工作首先執行。 編號愈低，優先順序愈高。 | 否 
參數 | U-SQL 指令碼的參數 | 否 


### 建立輸入和輸出資料集

#### 輸入資料集
在此範例中，輸入的資料是位於 Azure 資料湖存放區 (datalake/input 資料夾中的 SearchLog.tsv 檔案)。 

    {
        "name": "DataLakeTable",
        "properties": {
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
            "availability": {
                "frequency": "Day",
                "interval": 1
            }
        }
    }   

#### 輸出資料集
在此範例中，U-SQL 指令碼所產生的輸出資料會儲存在 Azure 資料湖存放區 (datalake/output 資料夾)。 

    {
        "name": "EventsByRegionTable",
        "properties": {
            "type": "AzureDataLakeStore",
            "linkedServiceName": "AzureDataLakeStoreLinkedService",
            "typeProperties": {
                "folderPath": "datalake/output/"
            },
            "availability": {
                "frequency": "Day",
                "interval": 1
            }
        }
    }

#### 範例 Azure 資料湖存放區連結服務
以下是上述輸入/輸出資料集所使用的範例 Azure 資料湖存放區連結服務的定義。 

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

請參閱 [從 Azure 資料湖存放區來回移動資料](data-factory-azure-datalake-connector.md) 如上述的 Azure 資料湖存放區中的 JSON 屬性的說明連結服務和資料集 JSON 程式碼片段。 


