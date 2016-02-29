<properties
    pageTitle="使用 Azure Data Factory 從內部部署 SQL Server 移動資料至 SQL Azure | Azure"
    description="請設定 ADF 管線來編寫兩個資料移轉活動，這兩個活動會每天在內部部署及雲端中的資料庫之間一同移動資料。"
    services="machine-learning"
    documentationCenter=""
    authors="fashah"
    manager="jacob.spoelstra"
    editor=""
    videoId=""
    scriptId="" />

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/06/2015"
    ms.author="fashah;bradsev" />


# 使用 Azure Data Factory 從內部部署的 SQL Server 移動資料至 SQL Azure

本主題說明如何使用 Azure Data Factory (ADF)，透過 Azure Blob 儲存體從內部部署的 SQL Server 資料庫移動資料至 SQL Azure Database。

## <a name="intro"></a>簡介: 何謂 ADF，當它應移轉資料?

Azure Data Factory 是完全受管理的雲端架構資料整合服務，用來協調及自動化資料的移動和轉換。 ADF 模型中的重要概念是管線。 管線是活動的邏輯群組，各個群組都會定義包含在資料集中的資料上要執行的動作。 連結的服務是用來定義 Data Factory 所需的資訊，以便連接到資料資源。

使用 ADF，您可將現有的資料處理服務組合成具高可用性的資料管線，並在雲端中管理。 這些資料管線可排程來內嵌、準備、轉換、分析和發佈資料，而 ADF 會管理並協調所有複雜的資料和處理中的相依項目。 您可以快速地在雲端中建置和部署解決方案，藉此連接逐漸增加的內部部署和雲端資料來源。

若資料需要持續在同時存取內部部署和雲端資源的混合式案例中移轉，或是資料有交易、需要修改，或者在移轉過程中新增了商務邏輯，請考慮使用 ADF。 ADF 允許使用定期管理資料移動的簡易 JSON 指令碼，來進行排程和監視的工作。 ADF 也有其他功能，例如支援複雜作業。 如需有關 ADF 的詳細資訊，請參閱文件，網址 [Azure Data Factory (ADF)](http://azure.microsoft.com/services/data-factory/)。

## <a name="scenario"></a>案例

我們會設定 ADF 管線，用於編寫兩個資料移轉活動，這兩個活動會每天在內部部署的 SQL Database 及雲端中的 Azure SQL Database 之間一同移動資料。 這兩個活動為：

* 將資料從內部部署 SQL Server 資料庫複製到 Azure Blob 儲存體帳戶
* 將資料從 Azure Blob 儲存體帳戶複製至 Azure SQL Database

**參考**: 顯示這裡已經過修改的更詳細的教學課程的步驟 [啟用管線以使用內部部署資料](data-factory-use-onpremises-datasources.md) 提供 ADF 小組和參考該主題的相關章節會提供適當的時候。


## <a name="prereqs"></a>先決條件
本教學課程假設您有：

*  **Azure 訂用帳戶**。 如果您沒有訂閱，您可以註冊 [免費試用版](https://azure.microsoft.com/pricing/free-trial/)。
*  **Azure 儲存體帳戶**。 在本教學課程中，您將使用 Azure 儲存體帳戶來儲存資料。 如果您沒有 Azure 儲存體帳戶，請參閱 [建立儲存體帳戶](storage-create-storage-account.md#create-a-storage-account) 文件。 建立儲存體帳戶之後，您必須取得用來存取儲存體的帳戶金鑰。 請參閱 [檢視、 複製和重新產生儲存體存取金鑰](storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys)。
* 若要存取 **Azure SQL Database**。 如果您必須設定 Azure SQL Database， [開始使用 Microsoft Azure SQL Database ](sql-database-get-started.md) 提供如何佈建 Azure SQL 資料庫的新執行個體的相關資訊。
* 安裝並設定 **PowerShell** 在本機。 如需指示，請參閱 [如何安裝和設定 Azure PowerShell](powershell-install-configure.md)。

> [AZURE.NOTE] 此程序使用 [Azure 入口網站](https://ms.portal.azure.com/)。

##<a name="upload-data"></a> 將資料上傳到您在內部部署 SQL Server

我們使用 [NYC 計程車資料集](http://chriswhong.com/open-data/foil_nyc_taxi/) 示範移轉程序。 NYC 計程車資料集，便可以，另有說明，請在那篇文章，在 Azure blob 儲存體 [NYC 計程車資料](http://www.andresmh.com/nyctaxitrips/)。 資料會有兩個檔案、 trip_data.csv 檔案包含車程詳細資訊和 trip_far.csv 檔案，其中包含針對每趟車程支付的費用的詳細資料。 範例及這些檔案的說明 [NYC 計程車車程資料集說明](machine-learning-data-science-process-sql-walkthrough.md#dataset)。


您可以將這裡提供的程序調整為自己的資料集，或者遵循上述步驟使用 NYC 計程車資料集。 若要將 NYC 計程車資料集上傳至您在內部部署 SQL Server 資料庫，請依照所述的程序 [資料大量匯入 SQL Server 資料庫](machine-learning-data-science-process-sql-walkthrough.md#dbload)。 這些指示適用於 Azure 虛擬機器上的 SQL Server，但將資料上傳至內部部署 SQL Server 的程序是相同的。

##<a name="create-adf"></a> 建立 Azure Data Factory

建立新的 Azure Data Factory 和資源群組中的指示 [Azure 入口網站](https://ms.portal.azure.com/) 提供 [建立 Azure Data Factory](data-factory-build-your-first-pipeline-using-editor.md#step-1-creating-the-data-factory)。 將新的執行個體 ADF *adfdsp* 建立資源群組的名稱和 *adfdsprg*。

## 安裝和設定資料管理閘道

若要在 Azure Data Factory 中啟用管線以使用內部部署的 SQL Server，您必須將其以連結服務形式新增至 Data Factory。 若要為內部部署 SQL Server 建立連結服務，您必須先下載 Microsoft 資料管理閘道並安裝在內部部署電腦上，然後設定要使用閘道之內部部署資料來源的連結服務。 資料管理閘道器會序列化和還原序列化託管之電腦上的來源與接收資料。

安裝指示和資料管理閘道器的詳細資訊，請參閱 [啟用管線以使用內部部署資料](data-factory-use-onpremises-datasources.md)


## <a name="adflinkedservices"></a>建立連接到資料資源的連結的服務

連結服務定義會定義 Azure Data Factory 所需的資訊，以便連接到資料資源。 建立連結的服務的逐步程序中提供 [建立連結的服務](data-factory-use-onpremises-datasources.md#step-2-create-linked-services)。

此案例中的三個資源都必須使用連結服務。

1. [內部部署 SQL Server 的連結服務](#adf-linked-service-onprem-sql)
2. [Azure Blob 儲存體的連結服務](#adf-linked-service-blob-store)
3. [Azure SQL Database 的連結服務](#adf-linked-service-azure-sql)


###<a name="adf-linked-service-onprem-sql"></a>內部部署 SQL Server 資料庫的連結服務
若要建立在內部部署 SQL Server 連結的服務，請按一下 **資料存放區** 在 Azure 傳統入口網站上 ADF 登陸頁面上，選取 *SQL* 輸入的認證和 *username* 和 *密碼* 在內部部署 SQL server。 您必須輸入伺服器名稱做為 **完整伺服器名稱的反斜線的執行個體名稱 (servername\instancename)**。 連結的服務的名稱 *adfonpremsql*。

###<a name="adf-linked-service-blob-store"></a>Blob 的連結服務
若要建立的 Azure Blob 儲存體帳戶的連結的服務，請按一下 **資料存放區** 在 Azure 傳統入口網站上 ADF 登陸頁面上，選取 *Azure 儲存體帳戶* ，然後輸入 Azure Blob 儲存體帳戶金鑰以及容器名稱。 連結服務的名稱 *adfds*。

###<a name="adf-linked-service-azure-sql"></a>Azure SQL Database 的連結服務
若要建立 Azure SQL Database 的連結的服務，請按一下 **資料存放區** 在 Azure 傳統入口網站上 ADF 登陸頁面上，選取 *Azure SQL* 輸入的認證和 *username* 和 *密碼* Azure SQL database。  *Username* 必須指定為 *user@servername*。   


##<a name="adf-tables"></a>定義及建立資料表來指定如何存取資料集

使用下列指令碼型程序，建立指定資料集結構、位置及可用性的資料表。 JSON 檔案可用來定義資料表。 如需有關這些檔案的結構的詳細資訊，請參閱 [資料集](data-factory-create-datasets.md)。

> [AZURE.NOTE]  您應該執行 `Add-AzureAccount` 指令程式在執行之前 [New-azuredatafactorytable](https://msdn.microsoft.com/library/azure/dn835096.aspx) 指令程式來確認命令執行已選取正確的 Azure 訂用帳戶。 這個指令程式的文件，請參閱 [Add-azureaccount](https://msdn.microsoft.com/library/azure/dn790372.aspx)。

資料表中的 JSON 型定義使用下列名稱：

*  **資料表名稱** 在內部部署 SQL server 是 *nyctaxi_data*
*  **容器名稱** 在 Azure Blob 儲存體帳戶是 *containername*  

此 ADF 管線所需的三個資料表定義為：

1. [SQL 內部部署資料表](#adf-table-onprem-sql)
2. [Blob 資料表 ](#adf-table-blob-store)
3. [SQL Azure 資料表](#adf-table-azure-sql)

> [AZURE.NOTE]  下列程序會使用 Azure PowerShell 來定義和建立 ADF 活動。 但是，這些工作也可以透過 Azure 入口網站來完成。 如需詳細資訊，請參閱 [建立輸入和輸出資料集](data-factory-use-onpremises-datasources.md#step-3-create-input-and-output-datasets)。

###<a name="adf-table-onprem-sql"></a>SQL 內部部署資料表

內部部署 SQL Server 的資料表定義已在下列 JSON 檔案中指定。

        {
            "name": "OnPremSQLTable",
            "properties":
            {
                "location":
                {
                "type": "OnPremisesSqlServerTableLocation",
                "tableName": "nyctaxi_data",
                "linkedServiceName": "adfonpremsql"
                },
                "availability":
                {
                "frequency": "Day",
                "interval": 1,   
                "waitOnExternal":
                {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
                }

                }
            }
        }
請注意，資料行名稱，並未包含在此，您可以子選取資料行名稱上，此處將它們包括 (如需詳細資訊，請核取 [ADF 文件](data-factory-copy-activity.md))。

複製檔案到資料表的 JSON 定義稱為 *onpremtabledef.json* 檔案，並將它儲存到已知位置 (這裡假設 *C:\temp\onpremtabledef.json*)。 使用下列 Azure PowerShell Cmdlet，在 ADF 中建立資料表。

    New-AzureDataFactoryTable -ResourceGroupName ADFdsprg -DataFactoryName ADFdsp –File C:\temp\onpremtabledef.json


###<a name="adf-table-blob-store"></a>Blob 資料表
輸出 Blob 位置的資料表定義如下 (此說明會將內嵌資料從內部部署對應至 Azure Blob)：

        {
            "name": "OutputBlobTable",
            "properties":
            {
                "location":
                {
                "type": "AzureBlobLocation",
                "folderPath": "containername",
                "format":
                {
                "type": "TextFormat",
                "columnDelimiter": "\t"
                },
                "linkedServiceName": "adfds"
                },
                "availability":
                {
                "frequency": "Day",
                "interval": 1
                }
            }
        }

複製檔案到資料表的 JSON 定義稱為 *bloboutputtabledef.json* 檔案，並將它儲存到已知位置 (這裡假設 *C:\temp\bloboutputtabledef.json*)。 使用下列 Azure PowerShell Cmdlet，在 ADF 中建立資料表。

    New-AzureDataFactoryTable -ResourceGroupName adfdsprg -DataFactoryName adfdsp -File C:\temp\bloboutputtabledef.json  

###<a name="adf-table-azure-sq"></a>SQL Azure 資料表
SQL Azure 輸出的資料表定義如下 (此結構描述會對應來自 Blob 的資料)：

    {
        "name": "OutputSQLAzureTable",
        "properties":
        {
            "structure":
            [
                { "name": "column1", type": "String"},
                { "name": "column2", type": "String"}                
            ],
            "location":
            {
                "type": "AzureSqlTableLocation",
                "tableName": "your_db_name",
                "linkedServiceName": "adfdssqlazure_linked_servicename"
            },
            "availability":
            {
                "frequency": "Day",
                "interval": 1            
            }
        }
    }

複製檔案到資料表的 JSON 定義稱為 *AzureSqlTable.json* 檔案，並將它儲存到已知位置 (這裡假設 *C:\temp\AzureSqlTable.json*)。 使用下列 Azure PowerShell Cmdlet，在 ADF 中建立資料表。

    New-AzureDataFactoryTable -ResourceGroupName adfdsprg -DataFactoryName adfdsp -File C:\temp\AzureSqlTable.json  

##<a name="adf-pipeline"></a>定義和建立管線

指定屬於管線的活動，並使用下列指令碼型程序建立管線。 JSON 檔案是用來定義管線屬性。

* 指令碼假設 **管線名稱** 是 *AMLDSProcessPipeline*。
* 此外請注意，我們會設定管線週期以每日執行，並使用預設的作業執行時間 (12 am UTC)。

> [AZURE.NOTE]  下列程序會使用 Azure PowerShell 來定義和建立 ADF 管線。 但是，此工作也可以透過 Azure 入口網站來完成。 如需詳細資訊，請參閱 [建立和執行管線](data-factory-use-onpremises-datasources.md#step-4-create-and-run-a-pipeline)。

使用上面提供的資料表定義，將 ADF 的管線定義指定為：

        {
            "name": "AMLDSProcessPipeline",
            "properties":
            {
                "description" : "This pipeline has one Copy activity that copies data from an on-premise SQL to Azure blob",
                 "activities":
                [
                    {
                        "name": "CopyFromSQLtoBlob",
                        "description": "Copy data from on-premise SQL server to blob",     
                        "type": "CopyActivity",
                        "inputs": [ {"name": "OnPremSQLTable"} ],
                        "outputs": [ {"name": "OutputBlobTable"} ],
                        "transformation":
                        {
                            "source":
                            {                               
                                "type": "SqlSource",
                                "sqlReaderQuery": "select * from nyctaxi_data"
                            },
                            "sink":
                            {
                                "type": "BlobSink"
                            }   
                        },
                        "Policy":
                        {
                            "concurrency": 3,
                            "executionPriorityOrder": "NewestFirst",
                            "style": "StartOfInterval",
                            "retry": 0,
                            "timeout": "01:00:00"
                        }       

                     },

                    {
                        "name": "CopyFromBlobtoSQLAzure",
                        "description": "Push data to Sql Azure",        
                        "type": "CopyActivity",
                        "inputs": [ {"name": "OutputBlobTable"} ],
                        "outputs": [ {"name": "OutputSQLAzureTable"} ],
                        "transformation":
                        {
                            "source":
                            {                               
                                "type": "BlobSource"
                            },
                            "sink":
                            {
                                "type": "SqlSink",
                                "WriteBatchTimeout": "00:5:00",             
                            }           
                        },
                        "Policy":
                        {
                            "concurrency": 3,
                            "executionPriorityOrder": "NewestFirst",
                            "style": "StartOfInterval",
                            "retry": 2,
                            "timeout": "02:00:00"
                        }
                     }
                ]
            }
        }

複製此管線 JSON 定義成檔案稱為 *pipelinedef.json* 檔案，並將它儲存到已知位置 (這裡假設 *C:\temp\pipelinedef.json*)。 使用下列 Azure PowerShell Cmdlet，在 ADF 中建立管線。

    New-AzureDataFactoryPipeline  -ResourceGroupName adfdsprg -DataFactoryName adfdsp -File C:\temp\pipelinedef.json

確認您可以在 Azure 傳統入口網站的 ADF 上看見管線，如下所示 (當您按一下圖表時)。

![](media/machine-learning-data-science-move-sql-azure-adf/DJP1kji.png)

##<a name="adf-pipeline-start"></a>啟動管線
現在可使用下列命令來執行管線：

    Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADFdsprg -DataFactoryName ADFdsp -StartDateTime startdateZ –EndDateTime enddateZ –Name AMLDSProcessPipeline

 *Startdate* 和 *enddate* 參數值必須換成您想要的管線，來執行實際的日期。

當管線執行時，您應該可以看到資料在選取用於 Blob 的容器中顯示 (每天一個檔案)。

請注意，我們尚未運用 ADF 提供的功能，以遞增方式輸送資料。 如需有關如何執行這和 ADF 所提供的其他功能的詳細資訊，請參閱 [ADF 文件](http://azure.microsoft.com/services/data-factory/)。

