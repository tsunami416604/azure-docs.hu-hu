<properties 
    pageTitle="使用 Data Factory 進行排程和執行" 
    description="了解 Azure Data Factory 應用程式模型的排程和執行層面。" 
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
    ms.date="10/20/2015" 
    ms.author="spelluru"/>

# 使用 Data Factory 進行排程和執行
  
本文說明 Azure Data Factory 應用程式模型的排程和執行層面。 這篇文章是根據 [建立管線](data-factory-create-pipelines.md) 和 [建立資料集](data-factory-create-datasets.md) 文件，並假設您已了解基本的資料處理站應用程式模型概念: 活動、 管線、 連結的服務和資料集。

## 排程活動

使用 **排程器** 區段在活動 JSON 中，您可以指定活動的週期性排程。 例如，您可以排程活動每小時執行，如下所示：

    "scheduler": {
        "frequency": "Hour",
        "interval": 1
    },  
    
![排程器範例](./media/data-factory-scheduling-and-execution/scheduler-example.png)

如上所示，指定每小時排程會建立對應到一系列輪轉視窗的活動執行。 輪轉視窗是一系列的固定大小、非重疊和連續的時間間隔。
 
目前正在執行的活動執行，視窗時間間隔可以存取與 **WindowStart** 和 **WindowEnd** 在活動 JSON 中的系統變數。 您可以在您的活動 JSON 和與活動 (包括從輸入選取資料、輸出代表時間序列資料的資料集) 相關聯的指令碼中針對不同用途使用這些變數。

如需有關不同的屬性包括在特定時間位移排程的排程器，設定要對齊的模式處理針對視窗在間隔的開頭或結尾請參閱 [建立管線](data-factory-create-pipelines.md) 文件。

## 時間序列資料集和資料配量

時間序列資料是連續順序的資料點，通常包含一段時間間隔的後續度量。 時間序列資料的常見範例包括感應器資料、應用程式遙測資料等。

使用 Azure Data Factory，您可以批次方式使用活動執行處理時間序列資料。 通常有週期性節奏，在該節奏下，輸入資料抵達以及產生需要的輸出資料。 這個節奏由指定模型化 **可用性** 區段的資料集，如下所示:

    "availability": {
      "frequency": "Hour",
      "interval": 1
    },

每個取用和產生的活動執行的資料單位稱為資料 **配量**。 下圖顯示活動的範例，該活動具有輸入時間序列資料集與輸出時間序列資料集，每個都有設定為每小時頻率的可用性集合。

![可用性排程器](./media/data-factory-scheduling-and-execution/availability-scheduler.png)

輸入和輸出資料集的每小時資料配量會顯示在上圖中。 下圖顯示 3 個輸入配量，已就緒可以進行處理，且進行中的 10-11AM 活動執行會產生 10-11AM 輸出配量。 

資料集 JSON 的變數來存取與目前所產生的配量相關聯的時間間隔 **SliceStart** 和 **SliceEnd**。

如需有關不同屬性適用於可用性區段請參閱 [建立資料集](data-factory-create-datasets.md) 文件。

## 範例 – 「複製活動」會將資料從 Azure SQL 移至 Azure Blob

讓我們放一些整理並且實際重新複製活動中所示的範例 [建立管線](data-factory-create-pipelines.md) 將資料從 Azure SQL 資料表到 Azure blob 每隔一小時的文章。

**輸入：Azure SQL 資料集**

    {
        "name": "AzureSqlInput",
        "properties": {
            "published": false,
            "type": "AzureSqlTable",
            "linkedServiceName": "AzureSqlLinkedService",
            "typeProperties": {
                "tableName": "MyTable"
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            },
            "external": true,
            "policy": {}
        }
    }


請注意， **頻率** 設為 **小時** 和 **間隔** 設為 **1** 中 **可用性** 一節。 

**輸出：Azure Blob 資料集**
    
    {
        "name": "AzureBlobOutput",
        "properties": {
            "published": false,
            "type": "AzureBlob",
            "linkedServiceName": "StorageLinkedService",
            "typeProperties": {
                "folderPath": "mypath/{Year}/{Month}/{Day}/{Hour}",
                "format": {
                    "type": "TextFormat"
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


請注意， **頻率** 設為 **小時** 和 **間隔** 設為 **1** 中 **可用性** 一節。



**活動：複製活動**

    {
        "name": "SamplePipeline",
        "properties": {
            "description": "copy activity",
            "activities": [
                {
                    "type": "Copy",
                    "name": "AzureSQLtoBlob",
                    "description": "copy activity", 
                    "typeProperties": {
                        "source": {
                            "type": "SqlSource",
                            "sqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
                        },
                        "sink": {
                            "type": "BlobSink",
                            "writeBatchSize": 100000,
                            "writeBatchTimeout": "00:05:00"
                        }
                    },
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
                    "scheduler": {
                        "frequency": "Hour",
                        "interval": 1
                    }
                }
            ],
            "start": "2015-01-01T08:00:00Z",
            "end": "2015-01-01T11:00:00Z"
        }
    }


上述範例顯示設為每小時頻率的活動排程和資料集可用性區段。 此範例會示範如何能夠運用 **WindowStart** 和 **WindowEnd** 變數，以選取指定活動的相關資料執行，並將它傳送至適當的動態與 blob **folderPath** 參數化以每小時有資料夾。

當 8 – 11 AM 的其中 3 個配量執行時，這就是範例 Azure 資料表和 Blob 的外觀。

假設 Azure SQL 中的資料如下所示：

![範例輸入](./media/data-factory-scheduling-and-execution/sample-input-data.png)

在部署上述管線時 Azure Blob 將會填入，如下所示：

1.  檔案 mypath/2015年/1/1/8/Data。<Guid>使用資料.txt 

        10002345,334,2,2015-01-01 08:24:00.3130000
        10002345,347,15,2015-01-01 08:24:00.6570000
        10991568,2,7,2015-01-01 08:56:34.5300000

    **注意:** <Guid> 會取代實際的 guid。 範例檔案名稱：Data.bcde1348-7620-4f93-bb89-0eed3455890b.txt
2.  檔案 mypath/2015年/1/1/9/Data。<Guid>使用資料.txt:

        10002345,334,1,2015-01-01 09:13:00.3900000
        24379245,569,23,2015-01-01 09:25:00.3130000
        16777799,21,115,2015-01-01 09:47:34.3130000
3.  檔案 mypath/2015年/1/1/10/Data。<Guid>沒有資料.txt。


## 資料配量、管線和並行配量執行的使用中週期

 [建立管線](data-factory-create-pipelines.md) 文章導入的設定，來指定管線的作用中期間概念 **啟動** 和 **結束** 的管線屬性。
 
您可以設定過去日期的管線使用中週期的開始日期，Data Factory 會自動計算 (回補) 過去的所有資料配量，並且開始處理。

回補資料配量之後，就可以設定它們以平行執行。 您可以這麼做在設定並行屬性 **原則** 活動中所示的 JSON 區段 [建立管線](data-factory-create-pipelines.md) 文件。

## 重新執行失敗的資料配量和自動資料相依性追蹤

您可以使用豐富的視覺化方式監視配量的執行。 請參閱 [監視及管理管線](data-factory-monitor-manage-pipelines.md) 文章以取得詳細資料。 

請思考一下會顯示兩個活動的下列範例。 Activity1 會產生時間序列資料集，具有由 Activity2 做為輸入取用的輸出配量，以產生最終輸出時間序列資料集。

![失敗的配量](./media/data-factory-scheduling-and-execution/failed-slice.png)

<br/>

上圖顯示 3 個最近的配量從時發生失敗，產生 9-10 AM 配量的 **Dataset2**。 Data Factory 會自動追蹤時間序列資料集的相依性，並且因而暫緩開始 9-10 AM 下游配量的活動執行。


Data Factory 監視和管理工具可讓您深入診斷記錄以了解失敗的配量，輕鬆地找出問題的根本原因並加以修正。 一旦您修正問題之後，也可以輕易地開始活動執行以產生失敗的配量。 如需有關如何開始重新執行的詳細資訊，了解狀態轉換的資料配量，請參閱 [監視和管理](data-factory-monitor-manage-pipelines.md) 文件。

一旦開始重新執行且 dataset2 的 9-10AM 配量已就緒之後，Data Factory 就會開始最終資料集的 9-10 AM 相依配量的執行，如下圖所示。

![重新執行失敗的配量](./media/data-factory-scheduling-and-execution/rerun-failed-slice.png)

若要深入剖析針對複雜的活動和資料集鏈結指定相依性和追蹤相依性，請參閱下列各節。

## 使用不同的頻率模型化資料集

在如上所示的範例中，輸入和輸出資料集和活動排程時段的頻率是相同的。 某些案例需要能夠以不同於一或多個輸入的頻率產生輸出。 Data Factory 支援模型化這些案例。

### 範例 1：對每小時可用的輸入資料產生每日輸出報告

假設我們有輸入度量資料，從可用之感應器每小時在 Azure Blob，因為我們想要產生每日的彙總報表，例如平均值、 最大值、 最小值等統計資料...使用 data factory 天 [Hive 活動](data-factory-hive-activity.md)。

以下是使用 Data Factory 模型化這個項目的方式：

**輸入 Azure Blob 資料集：**

每小時輸入檔案會針對指定日期在資料夾中卸除。 輸入的可用性設定為每小時 (頻率：小時、間隔：1)。

    {
      "name": "AzureBlobInput",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
          "partitionedBy": [
            { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
            { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "%M"}},
            { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "%d"}}
          ],
          "format": {
            "type": "TextFormat"
          }
        },
        "external": true,
        "availability": {
          "frequency": "Hour",
          "interval": 1
        }
      }
    }

**輸出 Azure Blob 資料集**

一個輸出檔會在資料夾中每日卸除。 輸出的可用性設定為每日 (頻率：日、間隔：1)。


    {
      "name": "AzureBlobOutput",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
          "partitionedBy": [
            { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
            { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "%M"}},
            { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "%d"}}
          ],
          "format": {
            "type": "TextFormat"
          }
        },
        "availability": {
          "frequency": "Day",
          "interval": 1
        }
      }
    }

**活動：管線中的 Hive 活動**

Hive 指令碼會收到適當的日期時間資訊做為參數運用 **WindowStart** 變數，如下所示。 Hive 指令碼會使用此變數將資料從右邊資料夾載入，並執行彙總來產生輸出。

        {  
            "name":"SamplePipeline",
            "properties":{  
            "start":"2015-01-01T08:00:00",
            "end":"2015-01-01T11:00:00",
            "description":"hive activity",
            "activities": [
                {
                    "name": "SampleHiveActivity",
                    "inputs": [
                        {
                            "name": "AzureBlobInput"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "AzureBlobOutput"
                        }
                    ],
                    "linkedServiceName": "HDInsightLinkedService",
                    "type": "HDInsightHive",
                    "typeProperties": {
                        "scriptPath": "adftutorial\\hivequery.hql",
                        "scriptLinkedService": "StorageLinkedService",
                        "defines": {
                            "Year": "$$Text.Format('{0:yyyy}',WindowsStart)",
                            "Month": "$$Text.Format('{0:%M}',WindowStart)",
                            "Day": "$$Text.Format('{0:%d}',WindowStart)"
                        }
                    },
                    "scheduler": {
                        "frequency": "Day",
                        "interval": 1
                    },          
                    "policy": {
                        "concurrency": 1,
                        "executionPriorityOrder": "OldestFirst",
                        "retry": 2,
                        "timeout": "01:00:00"
                    }
                 }
             ]
           }
        }

以下是它從資料相依性的觀點看起來的外觀。

![資料相依性](./media/data-factory-scheduling-and-execution/data-dependency.png)

每一天的輸出配量取決於輸入資料集之 24 小時每小時的配量。 Data Factory 會自動運算這些相依性，方法是釐清落在與要產生之輸出配量相同時間期間的輸入資料配量。 如果 24 個輸入配量中有任何一項無法使用 (例如，因為處理發生在產生該配量的活動上游)，Data Factory 會等待輸入配量就緒，才開始每日活動執行。


### 範例 2：使用運算式和 Data Factory 函式指定相依性

我們來看一下另一種案例。 假設您有處理兩個輸入資料集的 Hive 活動，其中一個每日有新資料，但是另一個會每週取得新資料。 假設您想要跨兩個輸入進行聯結作業，並且每日產生輸出。
 
目前為止簡單的方法是 Data Factory 會自動找出要處理的正確輸入配量，方法是包括與不再運作之輸出資料配量時間期間對齊的輸入資料配量。

您必須指定每個活動執行，Data Factory 應該針對每週輸入資料集使用上一週的資料配量。 您可以透過 Azure Data Factory 函式的協助執行此作業，如下所示。

**Input1：Azure Blob**

第一個輸入是 Azure blob 更新 **每日**。
    
    {
      "name": "AzureBlobInputDaily",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
          "partitionedBy": [
            { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
            { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "%M"}},
            { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "%d"}}
          ],
          "format": {
            "type": "TextFormat"
          }
        },
        "external": true,
        "availability": {
          "frequency": "Day",
          "interval": 1
        }
      }
    }

**Input2：Azure Blob**

Input2 是 Azure blob 更新 **每週**。

    {
      "name": "AzureBlobInputWeekly",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
          "partitionedBy": [
            { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
            { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "%M"}},
            { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "%d"}}
          ],
          "format": {
            "type": "TextFormat"
          }
        },
        "external": true,
        "availability": {
          "frequency": "Day",
          "interval": 7
        }
      }
    }

**輸出：Azure Blob**

一個輸出檔會在資料夾中每日卸除。 輸出的可用性設定為每日 (頻率：日、間隔：1)。
    
    {
      "name": "AzureBlobOutputDaily",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
          "partitionedBy": [
            { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
            { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "%M"}},
            { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "%d"}}
          ],
          "format": {
            "type": "TextFormat"
          }
        },
        "availability": {
          "frequency": "Day",
          "interval": 1
        }
      }
    }

**活動：管線中的 Hive 活動**

Hive 活動接受 2 個輸入，並且每日產生輸出配量。 您可以針對每週輸入指定每日的輸出配量是根據上一週的輸入配量，如下所示。
    
    {  
        "name":"SamplePipeline",
        "properties":{  
        "start":"2015-01-01T08:00:00",
        "end":"2015-01-01T11:00:00",
        "description":"hive activity",
        "activities": [
          {
            "name": "SampleHiveActivity",
            "inputs": [
              {
                "name": "AzureBlobInputDaily"
              },
              {
                "name": "AzureBlobInputWeekly",
                "startTime": "Date.AddDays(SliceStart,  -7 - Date.DayOfWeek(SliceStart))",
                "endTime": "Date.AddDays(SliceEnd,  -Date.DayOfWeek(SliceEnd))"  
              }
            ],
            "outputs": [
              {
                "name": "AzureBlobOutputDaily"
              }
            ],
            "linkedServiceName": "HDInsightLinkedService",
            "type": "HDInsightHive",
            "typeProperties": {
              "scriptPath": "adftutorial\\hivequery.hql",
              "scriptLinkedService": "StorageLinkedService",
              "defines": {
                "Year": "$$Text.Format('{0:yyyy}',WindowsStart)",
                "Month": "$$Text.Format('{0:%M}',WindowStart)",
                "Day": "$$Text.Format('{0:%d}',WindowStart)"
              }
            },
            "scheduler": {
              "frequency": "Day",
              "interval": 1
            },          
            "policy": {
              "concurrency": 1,
              "executionPriorityOrder": "OldestFirst",
              "retry": 2,  
              "timeout": "01:00:00"
            }
           } 
         ]
       }
    }


## 鏈結活動
您可以將一個活動的輸出資料集設為另一個活動的輸入資料集，藉此鏈結兩個活動。 活動可以在相同的管線中或在不同的管線中。 只有當第一個活動執行成功完成時，第二個活動才會執行。 此鏈結發生在時間配量層級 (在資料集內的不連續單位)。   

## Data Factory 系統變數

變數名稱 | 說明 | 物件範圍 | JSON 範圍和使用案例
------------- | ----------- | ------------ | ------------------------
WindowStart | 目前活動執行時段的時間間隔開始 | 活動 | <ol><li>指定資料選取範圍查詢。 請參閱所參照的連接器文章 [資料移動活動](data-factory-data-movement-activities.md) 文章。</li><li>傳遞參數給 Hive 指令碼 (如上所示的範例)。</li>
WindowEnd | 目前活動執行時段的時間間隔結束 | 活動 | 同上
SliceStart | 產生資料配量的時間間隔開始的 | 活動<br/>資料集 | <ol><li>指定動態資料夾路徑和檔案名稱時使用 [Azure Blob](data-factory-azure-blob-connector.md) 和 [檔案系統資料集](data-factory-onprem-file-system-connector.md)。</li><li>使用 data factory 函式在活動輸入集合中指定輸入相依性。</li></ol>
SliceEnd | 所產生之目前資料配量的時間間隔結束 | 活動<br/>資料集 | 同上。 

> [AZURE.NOTE] 目前 data factory 需要完全活動中指定的排程相符的輸出資料集可用性中指定的排程。 這表示 WindowStart、WindowEnd 和 SliceStart 與 SliceEnd 一律對應到相同的時間期間和單一輸出配量。
 
## Data Factory 函式參考

您可以針對下列目的搭配使用 Data Factory 中的函式與上述系統變數：

1.  指定資料選取範圍查詢 (請參閱參考的連接器文章 [資料移動活動](data-factory-data-movement-activities.md) 文件。

    叫用 data factory 函式的語法是: **$$<function>** 資料選取範圍查詢和其他屬性中的活動和資料集。  
2. 使用 Data Factory 函式在活動輸入集合中指定輸入相依性 (請參閱上述範例)。

    指定輸入相依性運算式不需要 $$。   

在下列範例中， **sqlReaderQuery** JSON 檔案中的屬性指派給所傳回的值 **Text.Format** 函式。 此範例也會使用系統變數，名為 **WindowStart**, ，代表活動執行時段的開始時間。
    
    {
        "Type": "SqlSource",
        "sqlReaderQuery": "$$Text.Format('SELECT * FROM MyTable WHERE StartTime = \\'{0:yyyyMMdd-HH}\\'', WindowStart)"
    }

### 函式

下表列出 Azure Data Factory 中的所有函式：

類別 | 函式 | 參數 | 說明
-------- | -------- | ---------- | ----------- 
時間 | AddHours(X,Y) | X：DateTime <p>Y:int</p> | 將 Y 小時加入至指定的時間 X。 <p>範例: 9/5/2013年 12:00:00 PM + 2 小時 = 9/5/2013年 2:00:00 PM</p>
時間 | AddMinutes(X,Y) | X：DateTime <p>Y:int</p> | 將 Y 分鐘加入至 X。<p>範例: 9/15/2013年 12: 00:00 PM + 15 分鐘 = 9/15/2013年 12: 15:00 PM</p>
時間 | StartOfHour(X) | X：Datetime | 取得 X 的小時元件所代表之小時的開始時間。 <p>範例: StartOfHour 9/15/2013年 05: 10:23 PM 是 9/15/2013年 05: 00:00 PM</p>
Date | AddDays(X,Y) | X：DateTime<p>Y:int</p> | 將 Y 天加入至 X。<p>範例: 9/15/2013年 12:00:00 PM + 2 天 = 9/17/2013年 12:00:00 PM</p>
Date | AddMonths(X,Y) | X：DateTime<p>Y:int</p> | 將 Y 月數加入至 X。<p>範例: 9/15/2013年 12:00:00 PM + 1 個月 = 10/15/2013年 12:00:00 PM</p> 
Date | AddQuarters(X,Y) | X：DateTime <p>Y:int</p> | 將 Y * 3 個月的時間 X。<p>範例: 9/15/2013年 12:00:00 PM + 1 季 = 12/15/2013年 12:00:00 PM</p>
Date | AddWeeks(X,Y) | X：DateTime<p>Y:int</p> | 將 Y * 7 天，為 X<p>範例: 9/15/2013年 12:00:00 PM + 1 週 = 9/22/2013年 12:00:00 PM</p>
Date | AddYears(X,Y) | X：DateTime<p>Y:int</p> | 將 Y 年加入至 X。<p>範例: 9/15/2013年 12:00:00 PM + 1 年 = 9/15/2014年 12:00:00 PM</p>
Date | Day(X) | X：DateTime | 取得 X 的日元件。<p>範例: 一天的 9/15/2013年 12:00:00 PM 是 9。 </p>
Date | DayOfWeek(X) | X：DateTime | 取得 X 的星期幾元件的日期。<p>範例: DayOfWeek 9/15/2013年 12:00:00 PM 是星期日。</p>
Date | DayOfYear(X) | X：DateTime | 取得由 X 的年元件代表年中的日期。<p>範例：<br/>2015 年 12 月 1 日: 2015年的第 335 天<br/>2015 年 12 月 31 日: 2015年的第 365 天<br/>12/31/2016年: 2016 年 (閏年) 的第 366 天</p>
Date | DaysInMonth(X) | X：DateTime | 取得參數 X 的月份元件所代表的當月天數。<p>範例: 9/15/2013年的 DaysInMonth 是 30，因為 9 月份有 30 天。</p>
Date | EndOfDay(X) | X：DateTime | 取得代表 X 的日期 (日期元件) 結束的日期時間。<p>範例: EndOfDay 9/15/2013年 05:10:23 PM 是 9/15/2013年 11:59:59 PM。</p>
Date | EndOfMonth(X) | X：DateTime | 取得參數 X 的月份元件所代表的當月結尾。 <p>範例: EndOfMonth 9/15/2013年 05:10:23 PM 是 9/30/2013年 11:59:59 PM (代表 9 月份的結束日期時間)</p>
Date | StartOfDay(X) | X：DateTime | 取得參數 X 的日期元件所代表的當天開頭。<p>範例: StartOfDay 9/15/2013年 05:10:23 PM 是 9/15/2013年 12:00:00 AM。</p>
DateTime | From(X) | X：字串 | 將字串 X 剖析為日期時間。
DateTime | Ticks(X) | X：DateTime | 取得參數 X 的刻度屬性。一個刻度等於 100 奈秒。 這個屬性的值代表從 0001 年 1 月 1 日午夜 12:00:00 經過的刻度數。 
文字 | Format(X) | X：字串變數 | 格式化文字。

#### Text.Format 範例

    "defines": { 
        "Year" : "$$Text.Format('{0:yyyy}',WindowStart)",
        "Month" : "$$Text.Format('{0:MM}',WindowStart)",
        "Day" : "$$Text.Format('{0:dd}',WindowStart)",
        "Hour" : "$$Text.Format('{0:hh}',WindowStart)"
    }

> [AZURE.NOTE] 使用另一個函式內的函數時，您不需要使用 **$$** 內部函式的前置詞。 例如: $$Text.Format (PartitionKey eq \\'my_pkey_filter_value\\'，RowKey ge \\'{0:yyyy-MM-dd hh: mm:} \\ '，Time.AddHours (SliceStart，-6))。 在此範例中，請注意， **$$** 前置詞不能用於 **Time.AddHours** 函式。 
  

## 資料相依性的深入探討

若要執行的活動所產生資料集配量，資料處理站會使用下列 **相依性模型** 來判斷活動取用的資料集和產生的活動之間的關聯性。

輸入資料集產生的輸出資料集配量所需的時間範圍稱為 **相依性期間**。

活動執行只會在相依性期間內輸入資料集的資料配量可用時產生資料集配量。 這表示可包含相依性期間的輸入配量必須在所有 **準備** 要執行的活動所產生的輸出資料集配量的狀態。 

若要產生資料集配量 [開始、結束]，需要函式以將資料集配量對應至其相依性期間。 此函式基本上是將資料集配量的開始和結束轉換為相依性期間的開始和結束的公式。 更正式的說法， 
    
    DatasetSlice = [start, end]
    DependecyPeriod = [f(start, end), g(start, end)]

其中 f 和 g 是對應函式，計算每個活動輸入的相依性期間的開始和結束。

如上述範例所示，大部分的情況下，相依性期間與產生資料配量的期間是相同的。 在這些情況下，Data Factory 會自動運算落在相依性期間內的輸入配量。  

例如：在上述彙總範例中，其中輸出是每日產生，而輸入資料每小時可用，資料配量期間為 24 小時。 Data Factory 會針對此時間期間尋找相關的每小時輸入配量，讓輸出配量相依於輸入配量。

您也可以針對相依性期間提供自己的對應，如上述範例所示，當中的其中一個輸入是每週，而輸出配量是每日產生。
   
## 資料相依性和驗證

資料集可以選擇性地具有定義的驗證原則，指定配量執行所產生的資料在供取用之前如何驗證。 請參閱 [建立資料集](data-factory-create-datasets.md) 文章以取得詳細資料。 

在這種情況下，一旦配量完成執行，輸出配量狀態會變更為 **等候** ，子狀態的 **驗證**。 配量驗證之後，配量狀態會變更為 **準備**。
   
如果已產生資料配量但是未通過驗證，將不會處理相依於驗證失敗的配量的下游配量活動執行。

Data factory 中的資料配量的各種狀態涵蓋 [監視和管理這些管線](data-factory-monitor-manage-pipelines.md) 文件。

## 外部資料

資料集可以標示為外部 (如以下的 JSON 所示)，意味著它尚未使用 Azure Data Factory 產生。 在這種情況下，資料集原則可以包含一組額外的參數，描述資料集的驗證和重試原則。  請參閱 [建立管線](data-factory-create-pipelines.md) 的所有屬性的描述。 

類似於外部資料的資料配量需要做好準備，才能處理相依配量的資料處理站所產生的資料集。

    {
        "name": "AzureSqlInput",
        "properties": 
        {
            "type": "AzureSqlTable",
            "linkedServiceName": "AzureSqlLinkedService",
            "typeProperties": 
            {
                "tableName": "MyTable"  
            },
            "availability": 
            {
                "frequency": "Hour",
                "interval": 1     
            },
            "external": true,
            "policy": 
            {
                "externalData": 
                {
                    "retryInterval": "00:01:00",
                    "retryTimeout": "00:10:00",
                    "maximumRetry": 3
                }
            }  
        } 
    } 






  









 
 












      

  





