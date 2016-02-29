<properties 
 pageTitle="如何使用 Azure 排程器建立複雜的排程和進階週期" 
 description="" 
 services="scheduler" 
 documentationCenter=".NET" 
 authors="krisragh" 
 manager="dwrede" 
 editor=""/>
<tags 
 ms.service="scheduler" 
 ms.workload="infrastructure-services" 
 ms.tgt_pltfrm="na" 
 ms.devlang="dotnet" 
 ms.topic="article" 
 ms.date="12/04/2015" 
 ms.author="krisragh"/>

# 如何使用 Azure 排程器建立複雜的排程和進階週期  

## 概觀

Azure 排程器的核心工作是 *排程*。 排程會決定排程器何時和如何執行工作。

Azure 排程器可讓您指定不同的一次和週期工作排程。 *單次* 排程引發一次在指定時間 – 實際上，它們是 *週期性* 僅執行一次的排程。 週期排程會根據預先決定的頻率引發。

由於這種彈性，Azure 排程器可讓您支援各種商務案例：

-   定期清除資料 – 例如，每一天，刪除所有超過 3 個月的推文
-   保存 – 例如，每個月，將發票歷程記錄推送至備份服務
-   要求外部資料 – 例如，每隔 15 分鐘，從 NOAA 提取新的 Ski 氣象報告
-   映像處理 – 例如，每個工作天，在離峰時段，使用雲端運算壓縮當天上傳的映像


在本文中，我們會逐步引導您完成您可以使用 Azure 排程器建立的範例工作。 我們提供描述每個排程的 JSON 資料。 如果您使用 [排程器 REST API](https://msdn.microsoft.com/library/azure/dn528946.aspx), ，您可以使用這個相同的 JSON， [建立 Azure 排程器工作](https://msdn.microsoft.com/library/azure/dn528937.aspx)。

## 支援的案例

本主題中的許多範例將說明 Azure 排程器支援之案例的廣度。 廣義來說，這些範例說明如何為許多行為模式建立排程，包括下列排程：

-   在特定日期和時間執行一次
-   執行並重複執行明確的次數
-   立即執行並重複執行 
-   執行並重複執行每個 *n* 分鐘、 小時、 天、 週或幾個月，在特定時間開始
-   執行並每週或每月的頻率但僅限於特定幾天、 一週的特定幾天或每月的特定天數重複執行
-   執行並在某個期間執行多次 – 例如，每個月的最後一個星期五和星期一，或在每天上午 5:15 和下午 5:15

## 日期和日期時間

Azure 排程器工作中的日期遵循 [ISO 8601 規格](http://en.wikipedia.org/wiki/ISO_8601) 和只包含日期。

Azure 排程器工作中的日期時間參考遵循 [ISO 8601 規格](http://en.wikipedia.org/wiki/ISO_8601) 和包含日期和時間的組件。 未指定 UTC 時差的日期時間假設為 UTC。  

## 如何：使用 JSON 和 REST API 建立排程

若要建立簡單的排程，在這篇文章和 Azure 排程器 REST API 中的 JSON 範例 [第一次建立雲端服務](https://msdn.microsoft.com/library/azure/dn528943.aspx), ，[然後建立工作集合](https://msdn.microsoft.com/library/azure/dn528940.aspx), ，和 [最後建立工作](https://msdn.microsoft.com/library/azure/dn528937.aspx)。 當建立工作時，您可以使用如下摘錄的 JSON 指定排程與週期：

    {
        "startTime": "2012-08-04T00:00Z", // optional
         …
        "recurrence":                     // optional
        {
            "frequency": "week",     // can be "year" "month" "day" "week" "hour" "minute"
            "interval": 1,                // optional, how often to fire (default to 1)
            "schedule":                   // optional (advanced scheduling specifics)
            {
                "weekDays": ["monday", "wednesday", "friday"],
                "hours": [10, 22]                      
            },
            "count": 10,                  // optional (default to recur infinitely)
            "endTime": "2012-11-04",      // optional (default to recur infinitely)
        },
        …
    }
    
## 概觀：工作結構描述的基本概念

下表提供與工作中週期和排程相關之主要元素的高階概觀： 

|**JSON 名稱**|**說明**|
|:--|:--|
|**_startTime_**|_startTime_ 是日期時間。 簡單的排程， _startTime_ 是第一個出現項目和複雜的排程工作會啟動一 _startTime_。|
|**_週期_**| _循環_ 物件會指定工作的循環規則，並將以執行工作的循環。 Recurrence 物件支援的項目 _頻率、 間隔、 endTime、 count_ 和 _排程_。 如果 _循環_ 定義， _頻率_ 是必要項，而其他的元素 _循環_ 是選擇性的。|
|**_frequency_**| _頻率_ 字串，表示工作是否重複發生的頻率單位。 支援的值為 _"minute"、"hour"、"day"、"week"_ 或 _「 月 」。_|
|**_interval_**| _間隔_ 這是一個正整數，表示的間隔 _頻率_ ，決定工作將執行的頻率。 例如，如果 _間隔_ 為 3 和 _頻率_ 為"week"，工作重複每隔 3 週。 Azure 排程器最多可支援 _間隔_ 18 個月的每月、 每週頻率 78 週或每日頻率和 548 天的頻率。 Hour 和 minute 頻率，支援的範圍是 1 < = _間隔_ < = 1000年。|
|**_endTime_**| _EndTime_ 字串指定的工作不應執行的過去日期時間。 具有無效 _endTime_ 過去。 如果沒有 _endTime_ 或計數指定，則工作會無限期執行。 同時 _endTime_ 和 _計數_ 不能包含相同工作。|
|**_計數_**|<p> _計數_ 是正整數 (大於零)，指定應該在完成之前執行此作業的次數。</p><p> _計數_ 表示之前被判斷為已完成的工作執行的次數。 例如，對於每天執行的工作 _計數_ 5 和開始日期為星期一、 星期五執行之後完成的工作。 如果開始日期是在過去，第一次執行會從建立時間計算。</p><p>如果沒有 _endTime_ 或 _計數_ 指定，則工作會無限期執行。 同時 _endTime_ 和 _計數_ 不能包含相同工作。</p>|
|**_排程_**|具有指定頻率的工作會根據週期排程來改變其週期。 A _排程_ 包含根據分鐘、 小時、 天、 月份天數和週數的修改。|

## 概觀：工作結構描述的預設值、限制和範例

在本概觀之後，讓我們詳細討論其中每一個元素。

|**JSON 名稱**|**值類型**|**必要？**|**預設值**|**有效值**|**範例**|
|:---|:---|:---|:---|:---|:---|
|**_startTime_**|String|否|None|ISO 8601 日期時間|<code>"startTime":"2013年-01-09T09:30:00-08:00"</code>|
|**_週期_**|Object|否|None|Recurrence 物件|<code>"recurrence": {「 頻率 」: 「 每月 」、 「 間隔 」: 1}</code>|
|**_frequency_**|String|是|None|"minute"、"hour"、"day"、"week"、"month"|<code>「 頻率 」: 「 小時 」</code> |
|**_interval_**|Number|否|1|1 到 1000。|<code>「 間隔 」: 10</code>|
|**_endTime_**|String|否|None|代表未來時間的日期時間值|<code>「 結束 」:"2013年-02-09T09:30:00-08:00"</code> |
|**_計數_**|Number|否|None|>= 1|<code>「 計數 」: 5</code>|
|**_排程_**|Object|否|None|Schedule 物件|<code>「 排程 」: {"minute": [30]，"hour": [8,17]}</code>|

## 深入探討: _開始時間_

下表如何擷取 _startTime_ 控制工作的執行方式。

|**startTime 值**|**無週期**|**循環。 沒有排程**|**搭配排程的週期**|
|:--|:--|:--|:--|
|**没有開始時間**|立即執行一次|立即執行一次。 根據從上次執行時間算出的時間執行後續的執行作業|<p>立即執行一次</p><p>執行後續執行作業，根據週期排程</p>|
|**過去的開始時間**|立即執行一次|<p>計算開始時間之後的第一個未來執行時間和執行在該時間</p><p>從上次執行時間執行後續執行基礎算</p><p>如需進一步說明此資料表之後，請參閱範例</p>|<p>作業啟動 _不一比_ 指定的開始時間。 第一個週期根據從開始時間計算的排程</p><p>根據週期排程執行後續的執行作業</p>|
|**未來或目前的開始時間**|在指定的開始時間執行一次|<p>在指定的開始時間執行一次</p><p>根據從上次執行時間算出的時間執行後續的執行作業</p>|<p>作業啟動 _不一比_ 指定的開始時間。 第一個週期根據從開始時間計算的排程</p><p>根據週期排程執行後續的執行作業</p>|

我們來看看會發生什麼情況的範例位置 _startTime_ 在過去，是使用 _循環_ 但不含 _排程_。  假設目前的時間是 2015年-04-08 13:00， _startTime_ 是 2015年-04-07 14:00，和 _循環_ 是每 2 天 (以定義 _頻率_: 天和 _間隔_: 2。)請注意， _startTime_ 在過去，與目前時間之前發生

這些條件 _第一次執行_ 會在 14:00\ 2015年-04-09。 排程器引擎會從開始時間計算執行週期。  過去的任何執行個體都會遭到捨棄。 引擎會使用下一個在未來發生的執行個體。  因此在此情況下， _startTime_ 是 2015年-04-07 下午 2:00，，因此下一個執行個體是從該時間是 2015年-04-09 下午 2:00 的 2 天。 

請注意，第一次執行就會相同，即使 startTime 為 2015年-04-05 14:00 或 2015年-04-01 14:00\。 第一次執行之後，後續執行是使用排程來計算 – 因此，執行時間將為 2015-04-11 下午 2:00，接著 2015-04-13 下午 2:00，然後 2015-04-15 下午 2:00，依此類推。

最後，當工作具有排程時，如果未在排程中設定小時及/或分鐘，則它們會分別預設為第一次執行的小時及/或分鐘。

## 深入探討: _排程_

在一方面， _排程_ 可以 _限制_ 作業執行次數。  例如，如果工作，並將"month"頻率有 _排程_ 唯一日期 31 上的執行時，工作執行，在具有第 31 這些月份<sup>st</sup> 天。

相反地， _排程_ 也可以 _展開_ 作業執行次數。 例如，如果工作，並將"month"頻率有 _排程_ 月 1 日及 2 上的執行時，工作執行，在 1<sup>st</sup> 和 2<sup>nd</sup> 而不是只執行一次月每月的天數。

如果指定了多個 schedule 元素，則評估的順序是從最大到最小 – 週數、月日、星期幾、小時和分鐘。

下表描述 _排程_ 詳細資料中的項目。

|**JSON 名稱**|**說明**|**有效值**|
|:---|:---|:---|
|**minutes**|一小時內工作將執行的分鐘數|<ul><li>整數，或</li><li>整數的陣列</li></ul>|
|**hours**|一天內工作將執行的小時數|<ul><li>整數，或</li><li>整數的陣列</li></ul>|
|**weekDays**|工作將執行的星期幾。 只能搭配 weekly 頻率指定。|<ul><li>「 星期一 」、 「 星期二 」、"Wednesday"、"Thursday"、 「 星期五 」、 「 星期六 」 或 「 星期日 」</li><li>一連串上述值 (最大值的陣列大小 7)</li></ul>_不_ 區分大小寫|
|**monthlyOccurrences**|決定將在當月哪幾天執行工作。 只能搭配 monthly 頻率指定。|<ul><li>一連串 monthlyOccurence 物件:</li></ul> <pre>{"day": _天_,，<br />  "occurrence": _出現_<br />}</pre><p> _天_ 工作將執行的一週天數，例如 {Sunday} 是當月的每個星期日。 所需。</p><p>會發生 _出現_ 月、 每日例如 {Sunday，-1} 是當月的最後一個星期日。 選擇性的。</p>|
|**monthDays**|工作將執行的月日。 只能搭配 monthly 頻率指定。|<ul><li>任何值 < =-1 和 > =-31。</li><li>任何值 > = 1 和 < = 31。</li><li>上述值的陣列</li></ul>|

## 範例：週期排程

以下是週期排程的各種範例 – 將焦點放在 schedule 物件和其子元素。

以下排程全都假設 _間隔_ 設為 1 \。 此外，一個必須採用正確頻率是根據 _排程_ – 例如，一個不能使用頻率"day"，在排程中具有"monthDays"修改。 上面說明了這類限制。

|**範例**|**說明**|
|:---|:---|
|<code>{"hours": [5]}</code>|在每天上午 5 點執行。 Azure 排程器會將 "hours" 中的每個值與 "minutes" 中的每個值逐一比對，以建立一個清單，列出將執行工作的所有時間。|
|<code>{[分鐘]: [15]，"hours": [5]}</code>|在每天上午 5:15 執行|
|<code>{[分鐘]: [15]，"hours": [5,17]}</code>|在每天上午 5:15 和下午 5:15 執行|
|<code>{[分鐘]: [15,45]，"hours": [5,17]}</code>|在每天上午 5:15、上午 5:45、下午 5:15 和下午 5:45 執行|
|<code>{[分鐘]: [0,15,30,45]}</code>|每隔 15 分鐘執行一次|
|<code>{hours": [0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23]}</code>|每小時執行一次。 這項工作每小時執行一次。 分鐘會受到 _startTime_, ，如果有指定，或如果未指定，依據建立時間。 比方說，如果開始時間或建立時間 (無論套用哪一個) 為下午 12:25，則工作將會在 00:25、01:25、02:25、…、23:25 執行。 排程相當於擁有工作，並將 _頻率_ 為"hour"， _間隔_ 的 1，但不含任何 _排程_。 差別在於，此排程也可以搭配不同 _頻率_ 和 _間隔_ 來建立其他工作。 例如，如果 _頻率_ 為"month"，排程會執行一次而不是每天月份，如果 _頻率_ 為"day"|
|<code>{分鐘的時間: [0]}</code>|在每小時整點執行。 這項工作也會每小時執行，但在整點 (例如上午 12 點、上午 1 點、上午 2 點等等。)這相當於 frequency 為 "hour"、startTime 為零分鐘且沒有 schedule (如果 frequency 為 "day") 的工作，但如果 frequency 為 "week" 或 "month"，則排程會分別在一週某一天或一個月某一天執行。|
|<code>{[分鐘]: [15]}</code>|在過去每小時 15 分鐘執行。 每小時執行，開始於上午 00:15、上午 1:15、上午 2:15 等等，並結束於下午 10:15 和下午 11:15。|
|<code>{"hours": [17]，"工作日": ["saturday"]}</code>|在每週星期六下午 5 點執行|
|<code>{hours": [17]，"工作日": ["monday"、"wednesday"、"friday"]}</code>|在每週星期一、星期三、星期五下午 5 點執行|
|<code>{「 分鐘 」: [15,45]，"hours": [17]，"工作日": ["monday"、"wednesday"、"friday"]}</code>|在每週星期一、星期三、星期五下午 5:15PM 和 5:45 執行|
|<code>{"hours": [5,17]，"工作日": ["monday"、"wednesday"、"friday"]}</code>|在每週星期一、星期三、星期五上午 5 點和下午 5 點執行|
|<code>{「 分鐘 」: [15,45]，"hours": [5,17]，"工作日": ["monday"、"wednesday"、"friday"]}</code>|在每週星期一、星期三、星期五上午 5:15、上午 5:45、下午 5:15 和下午 5:45 執行|
|<code>{「 分鐘 」: [0,15,30,45]，"工作日": ["monday"、"tuesday"、"wednesday"、"thursday"、"friday"]}</code>|在工作日每隔 15 分鐘執行一次|
|<code>{「 分鐘 」: [0,15,30,45]，"hours": [9、 10、 11、 12、 13、 14、 15、 16] 「 工作日": ["monday"、"tuesday"、"wednesday"、"thursday"、"friday"]}</code>|在工作日上午 9 點與下午 4:45 之間每隔 15 分鐘執行一次|
|<code>{"工作日": ["sunday"]}</code>|在星期日開始時間執行|
|<code>{"工作日": ["tuesday"、"thursday"]}</code>|在星期二和星期四開始時間執行|
|<code>{[分鐘]: [0]，"hours": [6]，"monthDays": [28]}</code>|在每個月的第 28 天上午 6 點執行 (假設 frequency 為 month)|
|<code>{[分鐘]: [0]，"hours": [6]，"monthDays": [-1]}</code>|在當月最後一天上午 6 點執行。 如果您想要在當月最後一天執行工作，請使用-1，而不是 28、29、30 或 31。|
|<code>{[分鐘]: [0]，"hours": [6]，"monthDays": [1，-1]}</code>|在每個月第一天和最後一天上午 6 點執行|
|<code>{monthDays": [1，-1]}</code>|在每個月第一天和最後一天的開始時間執行|
|<code>{monthDays": [1,14]}</code>|在每個月第一天和第十四天的開始時間執行|
|<code>{monthDays": [2]}</code>|在當月第二天的開始時間執行|
|<code>{「 分鐘 」: [0]，"hours": [5]，"monthlyOccurrences": [{"day":"friday"，"occurrence": 1}]}</code>|在每個月第一個星期五上午 5 點執行|
|<code>{"monthlyOccurrences": [{"day":"friday"，"occurrence": 1}]}</code>|在每個月第一個星期五的開始時間執行|
|<code>{"monthlyOccurrences": [{"day":"friday"，"occurrence":-3}]}</code>|在每個月倒數第三個星期五的開始時間執行|
|<code>{「 分鐘 」: [15]，"hours": [5]，"monthlyOccurrences": [{"day":"friday"，"occurrence": 1}，{"day":"friday"，"occurrence":-1}]}</code>|在每個月第一個和最後一個星期五上午 5:15 執行|
|<code>{"monthlyOccurrences": [{"day":"friday"，"occurrence": 1}，{"day":"friday"，"occurrence":-1}]}</code>|在每個月第一個和最後一個星期五的開始時間執行|
|<code>{"monthlyOccurrences": [{"day":"friday"，"occurrence": 5}]}</code>|在每個月第五個星期五的開始時間執行 如果一個月沒有第五個星期五，則此工作不會執行，因為它是排定為只在第五個星期五執行。 如果您想要在當月最後一個出現的星期五執行工作，則可以考慮對 occurrence 使用 -1 而不是 5。|
|<code>{「 分鐘 」: [0,15,30,45]，"monthlyOccurrences": [{"day":"friday"，"occurrence":-1}]}</code>|在當月最後一個星期五每隔 15 分鐘執行一次|
|<code>{「 分鐘 」: [15,45]，"hours": [5,17]，"monthlyOccurrences": [{"day":"wednesday"、"occurrence": 3}]}</code>|在每個月第 3 個星期三上午 5:15、上午 5:45、下午 5:15 和下午 5:45 執行|

## 另請參閱
 

 [排程器是什麼？](scheduler-intro.md)
 
 [Azure 排程器概念、術語及實體階層](scheduler-concepts-terms.md)

 [在 Azure 入口網站中開始使用排程器](scheduler-get-started-portal.md)

 [Azure 排程器的計劃和計費](scheduler-plans-billing.md)

 [Azure 排程器 REST API 參考](https://msdn.microsoft.com/library/dn528946)

 [Azure 排程器 PowerShell Cmdlet 參考](scheduler-powershell-reference.md)

 [Azure 排程器高可用性和可靠性](scheduler-high-availability-reliability.md)

 [Azure 排程器限制、預設值和錯誤碼](scheduler-limits-defaults-errors.md)

 [Azure 排程器輸出驗證](scheduler-outbound-authentication.md)
 
  

