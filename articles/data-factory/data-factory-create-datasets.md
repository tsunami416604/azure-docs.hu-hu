<properties 
    pageTitle="建立資料庫" 
    description="了解 Azure Data Factory 資料集並學習如何建立它們。" 
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


# 資料集

## 說明

資料集是資料的邏輯描述。 所說明的資料可能有所不同，包括簡單位元組、半結構化資料 (例如 CSV 檔案)，乃至於關聯式資料表甚或模型。 用來存取資料的機制 (位址、通訊協定、驗證結構描述) 已定義於連結服務中並可在資料集定義中參考。

## 語法

    {
        "name": "<name of dataset>",
        "properties":
        {
           "structure": [ ],
           "type": "<type of dataset>",
            "external": <boolean flag to indicate external data>,
            "typeProperties":
            {
            },
            "availability":
            {
    
            },
           "policy": 
            {      
    
            }
        }
    }

**語法詳細資料**

| 屬性| 說明| 必要| 預設值|
| -------- | ----------- | -------- | ------- |
| 名稱| 資料集的名稱| 是| NA|
| structure| <p>資料集的結構描述</p><p>看到 [資料集結構](#Structure) 一節以取得詳細資料</p>| 號| NA|
| 類型| 資料集的類型| 是| NA|
| typeProperties| <p>屬性對應至所選類型</p><p>看到 [資料集類型](#Type) 區段，如需有關支援的類型和其屬性。</p>| 是| NA|
| external| 用來指定資料集是否由 Data Factory 管線明確產生的布林值旗標| 否| false|
| availability| <p>定義處理時間範圍或資料集生產的切割模型。</p><p>請參閱 [資料集可用性](#Availability) 如需詳細資訊</p><p>看到 [排程和執行](data-factory-scheduling-and-execution.md) 如需有關資料集切割模型的文件</p>| 是| NA
| 原則| 定義資料集配量必須符合的準則或條件。<p>請參閱 [資料集原則](#Policy) 如需詳細資訊</p>| 否| NA|

### 範例

以下的資料集範例表示 **Azure SQL Database** 中名為 **MyTable** 的資料表。 Azure SQL Database 連接字串已定義於此資料集中參照的 **AzureSqlLinkedService**。 此資料集會每天切割。

    {
        "name": "DatasetSample",
        "properties": {
            "type": "AzureSqlTable",
            "linkedServiceName": "AzureSqlLinkedService",
            "typeProperties": 
            {
                "tableName": "MyTable"
            },
            "availability": 
            {
                "frequency": "Day",
                "interval": 1
            }
        }
    }

## <a name="Structure"></a>資料集結構

**Structure** 區段判斷提示資料集的結構描述。 其中包含資料行的集合和這些資料行的類型。 各資料行包含下列屬性：

 屬性| 說明| 必要| 預設值
-------- | ----------- | -------- | -------
 名稱| 資料行的名稱| 否| NA
 類型| 資料行的資料類型| 否| NA

### 範例

在下列範例中，資料集有三個資料行 slicetimestamp、projectname 和 pageviews。

    structure:  
    [ 
        { "name": "slicetimestamp", "type": "String"},
        { "name": "projectname", "type": "String"},
        { "name": "pageviews", "type": "Decimal"}
    ]

## <a name="Type"></a> 資料集類型

支援的資料來源和資料集類型會對應。 請參閱中參考的連接器主題 [資料移動活動](data-factory-data-movement-activities.md) 發行項的類型和資料集的組態資訊。

## <a name="Availability"></a> 資料集可用性

資料集中的 Availability 區段定義處理時間範圍或資料集生產的切割模型。 請參閱 [排程和執行](data-factory-scheduling-and-execution.md) 如需有關資料集切割和相依性模型的文章。

| 屬性| 說明| 必要| 預設值|
| -------- | ----------- | -------- | ------- |
| frequency| 指定資料集配量生產的時間單位。<p>**支援頻率**: 分鐘、 小時、 天、 週、 月</p>| 是| NA|
| interval| 指定頻率的倍數<p>「 頻率 x 間隔 」 會決定產生配量的頻率。</p><p>需要會每小時切割資料集時，如果您設定 **頻率** 至 **小時**, ，和 **間隔** 至 **1**。</p><p>**附註:** 如果您將 Frequency 指定為 Minute，我們建議您將間隔設定為不小於 15</p>| 是| NA|
| style| 指定是否應該產生配量開始/結束的間隔時間。<ul><li>StartOfInterval</li><li>EndOfInterval</li></ul><p>如果 Frequency 設為月樣式設為 EndOfInterval 上個月的最後一天, 產生配量。如果樣式設定為 StartOfInterval，每月的第一天產生配量。</p><p>如果 Frequency 設為一天，而且樣式設為 EndOfInterval，在一天的最後一個小時產生配量。</p>如果頻率設為小時且 style 設為 EndOfInterval，則配量會產生的小時結尾。比方說，1 PM – 2 PM 期間的配量，配量會在 2 PM 產生。</p>| 否| EndOfInterval|
| anchorDateTime| 定義排程器用來計算資料集配量界限的時間絕對的位置。<p>**附註:** 如果 AnchorDateTime 有比頻率更細微的日期部分，則會忽略更細微的部分。例如，如果 **間隔** 是 **每小時** (頻率: 小時，間隔: 1) 和 **AnchorDateTime** 包含 **分鐘和秒鐘**, ，則 **分鐘和秒鐘** 部分將會被忽略。 </p>| 否| 01/01/0001|
| Offset| 時間範圍的開始和結束的所有資料集配量移動。<p>**附註:** 如果已指定 anchorDateTime 和 offset，結果將是合併的移位。</p>| 否| NA|

### anchorDateTime 範例

**範例：**於 2007-04-19T08:00:00 開始的 23 小時資料集配量

    "availability": 
    {   
        "frequency": "Hour",        
        "interval": "23",   
        "anchorDateTime":"2007-04-19T08:00:00"  
    }

### 位移範例

於上午 6 點 (而非預設的午夜) 開始的每日配量。

    "availability":
    {
        "frequency": "Daily",
        "interval": "1",
        "offset": "06:00:00"
    }

在此情況下，SliceStart 會移位 6 小時，將會是上午 6 點。

12 個月 (頻率 = 月; 間隔 = 12) 排程，offset: 60.00:00:00 表示每 3 年月 2 日或 3 (從一年開始的 60 天如果樣式 = startofinterval，則為)，取決於該年份是否為閏年與否。



## <a name="Policy"></a>資料集原則

資料集中的 Policy 區段定義資料集配量必須符合的準則或條件。

### 驗證原則

| 原則名稱| 說明| 適用於| 必要| 預設值|
| ----------- | ----------- | ---------- | -------- | ------- |
| minimumSizeMB| 驗證 Azure Blob 中的資料是否符合最小的大小需求 (以 MB 為單位)。| Azure Blob| 否| NA|
| minimumRows| 驗證 Azure SQL Database 或 Azure 資料表中的資料是否包含最小的資料列數。| <ul><li>Azure SQL Database</li><li>Azure 資料表</li></ul>| 否| NA

#### 範例

**minimumSizeMB：**

    "policy":
    
    {
        "validation":
        {
            "minimumSizeMB": 10.0
        }
    }

**minimumRows**

    "policy":
    {
        "validation":
        {
            "minimumRows": 100
        }
    }

### ExternalData 原則

外部資料集是並非由 Data Factory 中的執行中管線所產生的資料集。 如果資料集標示為「外部」，則可定義 ExternalData 原則來影響資料集配量可用性的行為。

| 名稱| 說明| 必要| 預設值|
| ---- | ----------- | -------- | -------------- |
| dataDelay| <p>延遲指定配量之外部資料可用性檢查的時間。比方說，如果資料應該每小時可用，外部資料實際可用以及對應的配量已備妥的核取可能會因為延遲 dataDelay。</p><p>只適用於目前的時間; 比方說，如果它是下午 1:00 現在，這個值是 10 分鐘，則驗證會在 1:10 PM 啟動</p><p>此設定不會影響過去配量 (配量結束時間 + dataDelay 的配量 < 現在) 會被處理，而不需要任何延遲。</p><p>大於 23:59 小時必須指定使用 day.hours:minutes:seconds 格式的時間。例如，若要指定 24 小時，請不要使用 24:00:00；請改用 1.00:00:00。如果您使用 24:00:00，這會視同 24 天 (24.00:00:00)。1 天，4 小時，指定 1:04:00:00。 </p>| 否| 0|
| retryInterval| 失敗與下一步重試嘗試之間的等待時間。適用於當前的時間；如果先前嘗試失敗，我們會在上次嘗試之後等待這麼久。<p>如果是下午 1:00 現在，我們將開始第一次嘗試。如果完成第一次驗證檢查的持續時間是 1 分鐘且作業失敗，則下一次重試會在 1:00 + 1 分鐘 (持續時間) + 1 分鐘 (重試間隔) = 1:02pm。</p><p>在過去的配量，會有任何延遲。重試會立即執行。</p>| 否| 00:01:00 (1 分鐘)|
| retryTimeout| 每次重試逾時。<p>如果此值設為 10 分鐘，必須在 10 分鐘內完成驗證。如果要花超過 10 分鐘來執行驗證，重試會逾時。</p><p>所有驗證嘗試都逾時，如果配量將會標示為 TimedOut。</p>| 否| 00:10:00 (10 分鐘)|
| maximumRetry| 檢查外部資料可用性的次數。允許的最大值為 10。| 否| 3|

#### 更多範例

如果您需要在特定日期和時間 (假設在每月三號上午 8:00) 執行以每月為基礎的管線，您可以使用 [位移]**** 標記，以設定其應該要執行的日期和時間。

    {
      "name": "MyDataset",
      "properties": {
        "type": "AzureSqlTable",
        "linkedServiceName": "AzureSqlLinkedService",
        "typeProperties": {
          "tableName": "MyTable"
        },
        "availability": {
          "frequency": "Month",
          "interval": 1,
          "offset": "3.08:10:00",
          "style": "StartOfInterval"
        }
      }
    }



