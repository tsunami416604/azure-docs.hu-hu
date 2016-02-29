<properties 
    pageTitle="SQL Server 預存程序活動" 
    description="深入了解如何使用 SQL Server 預存程序活動，以從 Data Factory 管線叫用 Azure SQL Database 或 Azure SQL 資料倉儲中的預存程序。" 
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
    ms.date="10/12/2015" 
    ms.author="spelluru"/>

# SQL Server 預存程序活動

您可以在 Data Factory 中使用 SQL Server 預存程序活動 [管線](data-factory-create-pipelines.md) 來叫用中的預存程序 **Azure SQL Database** 或 **Azure SQL 資料倉儲** 。 這篇文章是根據 [資料轉換活動](data-factory-data-transformation-activities.md) 文章，它呈現資料轉換和支援的轉換活動的一般概觀。

## 語法
    {
        "name": "SQLSPROCActivity",
        "description": "description", 
        "type": "SqlServerStoredProcedure",
        "inputs":  [ { "name": "inputtable"  } ],
        "outputs":  [ { "name": "outputtable" } ],
        "typeProperties":
        {
            "storedProcedureName": "<name of the stored procedure>",
            "storedProcedureParameters":  
            {
                "param1": "param1Value"
                …
            }
        }
    }

## 語法詳細資料

屬性 | 說明 | 必要
-------- | ----------- | --------
名稱 | 活動的名稱 | 是
說明 | 說明活動用途的文字 | 否
類型 | SqlServerStoredProcedure | 是
輸入 | 輸入資料集必須可供使用 (「就緒」狀態)，才能執行預存程序活動。 將預存程序活動與其他活動鏈結時，此活動的輸入僅用於相依性管理。 在預存程序中輸入資料集無法做為參數取用 | 否
輸出 | 預存程序活動產生的輸出資料集。 請確定輸出資料表會使用連結的服務，該服務將 Azure SQL Database 或 Azure SQL 資料倉儲連結至 Data Factory。 預存程序活動中的輸出，可以做為傳遞預存程序活動結果的方式，以進行後續的處理，及/或在此活動與其他活動鏈結時，用於相依性管理 | 是
storedProcedureName | 指定 Azure SQL 資料庫或輸出資料表使用的連結服務所代表的 Azure SQL 資料倉儲中的預存程序的名稱。 | 是
storedProcedureParameters | 指定預存程序參數的值 | 否

## 範例逐步解說

### 範例資料表與預存程序
1. 建立下列 **資料表** 中使用 SQL Server Management Studio 或其他任何工具，您可以輕鬆地與您 Azure SQL Database。 Datetimestamp 資料行是產生對應識別碼的日期和時間。 

        CREATE TABLE dbo.sampletable
        (
            Id uniqueidentifier,
            datetimestamp nvarchar(127)
        )
        GO

        CREATE CLUSTERED INDEX ClusteredID ON dbo.sampletable(Id);
        GO

    識別碼必須是唯一的識別和 datetimestamp 資料行是日期和時間時產生對應的識別碼。
    ![範例資料](./media/data-factory-stored-proc-activity/sample-data.png)

2. 建立下列 **預存程序** ，插入資料至 **此處範例資料表**。

        CREATE PROCEDURE sp_sample @DateTime nvarchar(127)
        AS
        
        BEGIN
            INSERT INTO [sampletable]
            VALUES (newid(), @DateTime)
        END

    > [AZURE.IMPORTANT] **名稱** 和 **大小寫** 的參數 (在此範例中的日期時間) 必須符合在管線/活動 JSON 中指定的參數。 在預存程序定義中，確定 **@** 參數做為前置詞。
    
### 建立 Data Factory  
4. 登入後 [Azure 入口網站](http://portal.azure.com/), ，執行下列動作:
    1.  按一下 [ **新增** 左側功能表。 
    2.  按一下 [ **資料分析** 中 **建立** 刀鋒視窗。
    3.  按一下 [ **Data Factory** 上 **資料分析** 刀鋒視窗。
4.  在 **新增 data factory** 刀鋒視窗中，輸入 **SProcDF** 的名稱。 Azure Data Factory 名稱必須是全域唯一的。 您必須在 Data Factory 的名稱前面加上您的名稱，才能成功建立 Factory。 
3.  如果您尚未建立任何資源群組，您必須建立資源群組。 作法：
    1.  按一下 [ **資源群組名稱**。
    2.  選取 **建立新的資源群組** 中 **資源群組** 刀鋒視窗。
    3.  輸入 **ADFTutorialResourceGroup** 的 **名稱** 中 **建立資源群組** 刀鋒視窗。
    4.  按一下 [ **確定**。
4.  選取資源群組之後，請確認您使用的是要在其中建立 Data Factory 的正確訂用帳戶。
5.  按一下 [ **建立** 上 **新增 data factory** 刀鋒視窗。
6.  您會看到 data factory 中建立 **儀表板** 的 Azure 入口網站。 在 Data Factory 成功建立後，您會看到 Data Factory 頁面，顯示 Data Factory 的內容。

### 建立 Azure SQL 連結服務  
建立 Data Factory 之後，您可以建立 Azure SQL 連結的服務，將 Azure SQL Database 連結到 Data Factory。 這是包含 sampletable 資料表和 sp_sample 預存程序的資料庫。

7.  按一下 [ **作者和部署** 上 **DATA FACTORY** 分頁 **SProcDF**。 這會啟動 Data Factory 編輯器。 
2.  按一下 [ **新增資料存放區** 命令列，然後選擇 [ **Azure SQL**。 您應該會在編輯器中看到用來建立 Azure SQL 連結服務的 JSON 指令碼。 
4. 取代 **servername** Azure SQL Database 伺服器的名稱與 **databasename** 與資料庫建立資料表和預存程序， **username@servername** 以具有資料庫存取權的使用者帳戶和 **密碼** 取代為使用者帳戶的密碼。 
5. 按一下 [ **部署** 命令列來部署連結的服務。

### 建立輸出資料表
6. 按一下 [ **新的資料集** 命令列，然後選取 **Azure SQL**。
7. 將下列 JSON 指令碼複製/貼到 JSON 編輯器。

        {               
            "name": "sprocsampleout",
            "properties": {
                "type": "AzureSqlTable",
                "linkedServiceName": "AzureSqlLinkedService",
                "typeProperties": {
                    "tableName": "sampletable"
                },
                "availability": {
                    "frequency": "Hour",
                    "interval": 1
                }
            }
        }
7. 按一下 [ **部署** 命令列來部署資料集。 

### 使用 SqlServerStoredProcedure 活動建立管線
現在，讓我們使用 SqlServerStoredProcedure 活動來建立管線。
 
9. 按一下 [ **...(省略符號)** 命令列，然後按一下 [ **新增管線**。 
9. 複製/貼上下列 JSON 程式碼片段。   **StoredProcedureName** 設 **sp_sample**。 名稱和參數的大小寫 **DateTime** 必須符合的名稱和大小寫的預存程序定義中的參數。  

        {
            "name": "SprocActivitySamplePipeline",
            "properties": {
                "activities": [
                    {
                        "type": "SqlServerStoredProcedure",
                        "typeProperties": {
                            "storedProcedureName": "sp_sample",
                            "storedProcedureParameters": {
                                "DateTime": "$$Text.Format('{0:yyyy-MM-dd HH:mm:ss}', SliceStart)"
                            }
                        },
                        "outputs": [
                            {
                                "name": "sprocsampleout"
                            }
                        ],
                        "scheduler": {
                            "frequency": "Hour",
                            "interval": 1
                        },
                        "name": "SprocActivitySample"
                    }
                ],
                "start": "2015-01-02T00:00:00Z",
                "end": "2015-01-03T00:00:00Z",
                "isPaused": false
            }
        }
9. 按一下 [ **部署** 部署管線] 工具列上。  

### 監視管線

6. 按一下 [ **X** 關閉 Data Factory 編輯器] 刀鋒視窗，並以瀏覽回 [Data Factory] 分頁中，並按一下 [ **圖表**。
7. 在 [圖表檢視] 中，您會看到管線的概觀，以及在本教學課程中使用的資料集。 
8. 在 [圖表] 檢視中，按兩下資料集 **sprocsampleout**。 您將會看到就緒狀態的配量。 由於配量是針對 2015/01/02 和 2015/01/03 之間的每一個小時所產生，因此，應該會有 24 個配量。 
10. 當配量處於 **準備** 執行狀態 **選取 * 從 sampledata** 查詢 Azure SQL 資料庫，以確認資料已插入至資料表，預存程序。

    ![輸出資料](./media/data-factory-stored-proc-activity/output.png)

    請參閱 [監視管線](data-factory-monitor-manage-pipelines.md) 需監視 Azure Data Factory 管線的詳細資訊。  

> [AZURE.NOTE] 在上述範例中，sprocactivitysample 沒有輸入。 如果您想要鏈結這個項目與活動上游 (也就是 一個處理)、 上游活動的輸出可以用作此活動的輸入。  在這種情況下，此活動不會執行直到上游活動完成且上游活動的輸出可用 (處於就緒狀態)。 輸入無法直接做為預存程序活動的參數使用。

## 傳遞靜態值 
現在我們來考量在包含稱為「文件範例」的靜態值的資料表中，新增另一個名為「案例」的資料行。

![範例資料 2](./media/data-factory-stored-proc-activity/sample-data-2.png)

    CREATE PROCEDURE sp_sample @DateTime nvarchar(127) , @Scenario nvarchar(127)
    
    AS
    
    BEGIN
        INSERT INTO [sampletable]
        VALUES (newid(), @DateTime, @Scenario)
    END

若要這麼做，從預存程序活動傳遞「案例」參數和值。 在上述範例中，typeproperties 區段如下：

    "typeProperties":
    {
        "storedProcedureName": "sp_sample",
        "storedProcedureParameters": 
        {
            "DateTime": "$$Text.Format('{0:yyyy-MM-dd HH:mm:ss}', SliceStart)",
            "Scenario": "Document sample"
        }
    }

