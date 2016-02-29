<properties 
    pageTitle="Hadoop 串流活動" 
    description="了解如何使用 Azure Data Factory 中的 Hadoop 串流活動，以在隨選/您自己的 HDInsight 叢集上執行 Hadoop 串流程式。" 
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

# Hadoop 串流活動
您可以使用 HDInsightStreamingActivity 活動從 Azure Data Factory 管線叫用 Hadoop 串流工作。 下列 JSON 片段會示範在管線 JSON 檔案中使用 HDInsightStreamingActivity 的語法。 

Data Factory 中的 HDInsight 串流活動 [管線](data-factory-create-pipelines.md) 上執行 Hadoop 串流程式 [自己](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) 或 [隨](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) Windows/Linux 為基礎的 HDInsight 叢集。 這篇文章是根據 [資料轉換活動](data-factory-data-transformation-activities.md) 文章，它呈現資料轉換和支援的轉換活動的一般概觀。

## JSON 範例
HDInsight 叢集會使用範例程式 (wc.exe 和 cat.exe) 和資料 (將 davinci.txt) 自動填入。 根據預設，HDInsight 叢集所使用的容器名稱是叢集本身的名稱。 例如，如果您的叢集名稱是 myhdicluster，相關聯的 Blob 容器名稱為 myhdicluster。 

    {
        "name": "HadoopStreamingPipeline",
        "properties": {
            "description": "Hadoop Streaming Demo",
            "activities": [
                {
                    "type": "HDInsightStreaming",
                    "typeProperties": {
                        "mapper": "cat.exe",
                        "reducer": "wc.exe",
                        "input": "wasb://<nameofthecluster>@spestore.blob.core.windows.net/example/data/gutenberg/davinci.txt",
                        "output": "wasb://<nameofthecluster>@spestore.blob.core.windows.net/example/data/StreamingOutput/wc.txt",
                        "filePaths": [
                            "<nameofthecluster>/example/apps/wc.exe",
                            "<nameofthecluster>/example/apps/cat.exe"
                        ],
                        "fileLinkedService": "StorageLinkedService",
                        "getDebugInfo": "Failure"
                    },
                    "outputs": [
                        {
                            "name": "StreamingOutputDataset"
                        }
                    ],
                    "policy": {
                        "timeout": "01:00:00",
                        "concurrency": 1,
                        "executionPriorityOrder": "NewestFirst",
                        "retry": 1
                    },
                    "scheduler": {
                        "frequency": "Day",
                        "interval": 1
                    },
                    "name": "RunHadoopStreamingJob",
                    "description": "Run a Hadoop streaming job",
                    "linkedServiceName": "HDInsightLinkedService"
                }
            ],
            "start": "2014-01-04T00:00:00Z",
            "end": "2014-01-05T00:00:00Z"
        }
    }

請注意：

1. 設定 **linkedServiceName** 串流 mapreduce 工作執行叢集指向您的 HDInsight 連結服務的名稱。
2. 設定活動的型別 **HDInsightStreaming**。
3. 如 **對應** 屬性，指定對應工具可執行檔的名稱。 在上述範例中，cat.exe 是對應程式可執行檔。
4. 如 **減壓器** 屬性，指定減壓器可執行檔的名稱。 在上述範例中，cat.exe 是減壓器可執行檔。
5. 如 **輸入** 輸入屬性，指定對應工具 (包括位置) 的輸入的檔。 在 "wasb://adfsample@<account name>.blob.core.windows.net/example/data/gutenberg/davinci.txt" 範例中：adfsample 是 blob 容器，example/data/Gutenberg 是資料夾，而 davinci.txt 是 blob。
6. 如 **輸出** 輸入屬性，指定減壓器的輸出檔 (包括位置)。 Hadoop 串流工作的輸出會寫入針對這個屬性指定的位置。
7. 在 **filePaths** 區段中，指定對應器和歸納器可執行檔的路徑。 在 "adfsample/example/apps/wc.exe" 範例中，adfsample 是 blob 容器，example/apps 是資料夾，而 wc.exe 是可執行檔。
8. 如 **{1>filelinkedservice** 屬性，指定代表 filePaths 區段中指定的檔案所在的 Azure 儲存體的 Azure 儲存體連結服務。
9. 如 **引數** 屬性，指定資料流工作的引數。
10.  **GetDebugInfo** 屬性是選擇性項目。 該屬性設定為 [失敗] 時，只能在執行失敗時下載記錄檔。 當其設定為「所有」時，無論執行狀態為何，一律下載記錄檔。

> [AZURE.NOTE] 此範例所示，您必須為 Hadoop 資料流處理活動的指定輸出資料集 **輸出** 屬性。 這只是驅動管線排程所需的空資料集。 您不需要指定任何輸入資料集的活動 **輸入** 屬性。  

    
## 範例
本逐步解說中的管線會在 Azure HDInsight 叢集上執行字數統計串流 Map/Reduce 程式。 

### 連結的服務

#### 儲存體連結服務
首先，建立連結的服務，將 Azure HDInsight 叢集使用的 Azure 儲存體連結到 Azure Data Factory。 如果您複製/貼上下列程式碼，請記得使用 Azure 儲存體的名稱和金鑰來取代帳戶名稱和帳戶金鑰。 

    {
        "name": "StorageLinkedService",
        "properties": {
            "type": "AzureStorage",
            "typeProperties": {
                "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>"
            }
        }
    }

#### Azure HDInsight 連結服務
接著，建立連結的服務，將 Azure HDInsight 叢集連結到 Azure Data Factory。 如果您複製/貼上下列程式碼，請使用您的 HDInsight 叢集的名稱來取代 HDInsight 叢集名稱，然後變更使用者名稱和密碼值。 
    
    {
        "name": "HDInsightLinkedService",
        "properties": {
            "type": "HDInsight",
            "typeProperties": {
                "clusterUri": "https://<HDInsight cluster name>.azurehdinsight.net",
                "userName": "admin",
                "password": "**********",
                "linkedServiceName": "StorageLinkedService"
            }
        }
    }

### 資料集

#### 輸出資料集
此範例中的管線不需要取得任何輸入。 您必須指定 HDInsight 串流活動的輸出資料集。 這只是驅動管線排程所需的空資料集。 

    {
        "name": "StreamingOutputDataset",
        "properties": {
            "published": false,
            "type": "AzureBlob",
            "linkedServiceName": "StorageLinkedService",
            "typeProperties": {
                "folderPath": "adftutorial/streamingdata/",
                "format": {
                    "type": "TextFormat",
                    "columnDelimiter": ","
                },
            },
            "availability": {
                "frequency": "Day",
                "interval": 1
            }
        }
    }

### 管線

在此範例管線有只有一個活動的型別: **HDInsightStreaming**。 

HDInsight 叢集會使用範例程式 (wc.exe 和 cat.exe) 和資料 (將 davinci.txt) 自動填入。 根據預設，HDInsight 叢集所使用的容器名稱是叢集本身的名稱。 例如，如果您的叢集名稱是 myhdicluster，相關聯的 Blob 容器名稱為 myhdicluster。  

    {
        "name": "HadoopStreamingPipeline",
        "properties": {
            "description": "Hadoop Streaming Demo",
            "activities": [
                {
                    "type": "HDInsightStreaming",
                    "typeProperties": {
                        "mapper": "cat.exe",
                        "reducer": "wc.exe",
                        "input": "wasb://<blobcontainer>@spestore.blob.core.windows.net/example/data/gutenberg/davinci.txt",
                        "output": "wasb://<blobcontainer>@spestore.blob.core.windows.net/example/data/StreamingOutput/wc.txt",
                        "filePaths": [
                            "<blobcontainer>/example/apps/wc.exe",
                            "<blobcontainer>/example/apps/cat.exe"
                        ],
                        "fileLinkedService": "StorageLinkedService"
                    },
                    "outputs": [
                        {
                            "name": "StreamingOutputDataset"
                        }
                    ],
                    "policy": {
                        "timeout": "01:00:00",
                        "concurrency": 1,
                        "executionPriorityOrder": "NewestFirst",
                        "retry": 1
                    },
                    "scheduler": {
                        "frequency": "Day",
                        "interval": 1
                    },
                    "name": "RunHadoopStreamingJob",
                    "description": "Run a Hadoop streaming job",
                    "linkedServiceName": "HDInsightLinkedService"
                }
            ],
            "start": "2014-01-04T00:00:00Z",
            "end": "2014-01-05T00:00:00Z"
        }
    }

