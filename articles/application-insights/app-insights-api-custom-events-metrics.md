<properties 
    pageTitle="自訂事件和度量的 Application Insights API" 
    description="在您的裝置或桌面應用程式、網頁或服務中插入幾行程式碼，來追蹤使用狀況及診斷問題。" 
    services="application-insights"
    documentationCenter="" 
    authors="alancameronwills" 
    manager="douge"/>

<tags 
    ms.service="application-insights" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="ibiza" 
    ms.devlang="multiple" 
    ms.topic="article" 
    ms.date="11/30/2015" 
    ms.author="awills"/>


# 自訂事件和度量的 Application Insights API

*Application Insights 目前僅供預覽。*

在您的應用程式中插入幾行程式碼，以了解使用者對它進行的動作或協助診斷問題。 您可以從裝置和桌面應用程式、Web 用戶端以及 Web 伺服器傳送遙測。

Application Insights 資料收集器會使用此 API 傳送標準遙測，例如頁面檢視和例外狀況報告，但是您也可以使用它來傳送您自己自訂的遙測。

## API summary

API 是跨所有平台統一的，除了一些小變化形式。

 方法| 用於
---|---
 [`TrackPageView`] (#page 檢視)| 頁面、畫面、刀鋒視窗或表單
 [`TrackEvent`] (#track 事件)| 使用者動作和其他事件。用來追蹤使用者行為，或監視效能。
 [`TrackMetric`] (#track 度量)| 效能度量，例如與特定事件不相關的佇列長度
 [`TrackException`] (#track 例外狀況)| 記錄診斷的例外狀況。追蹤與其他事件的發生相對位置，並且檢查堆疊追蹤。
 [`TrackRequest`] (#track 要求)| 記錄伺服器要求的頻率和持續時間以進行效能分析。
 [`TrackTrace`] (#track 追蹤)| 診斷記錄訊息。您也可以擷取第三方記錄檔。
 [`TrackDependency`] (#track 相依性)| 記錄對您的應用程式所依賴的外部元件的呼叫持續時間及頻率。

您可以 [附加屬性和度量](#properties) 大部分的遙測呼叫。


## <a name="prep"></a>開始之前

如果您尚未完成這些動作：

* 將 Application Insights SDK 加入至專案：
 * [ASP.NET 專案 ][greenbrown]
 * [[Windows] 的 Windows 專案][windows]
 * [Java 專案 ][java]
 * [每個網頁 ][client]

* 在裝置或 Web 伺服器程式碼中，加入：

    *C#:* `使用 Microsoft.ApplicationInsights;`

    *VB:* `匯入 Microsoft.ApplicationInsights`

    *Java:* `匯入 com.microsoft.applicationinsights.TelemetryClient;`

## 建構 TelemetryClient

建構 TelemetryClient 的執行個體 (除了在網頁中的 JavaScript)：

*C#:*

    private TelemetryClient telemetry = new TelemetryClient();

*VB:*

    Private Dim telemetry As New TelemetryClient

*Java*

    private TelemetryClient telemetry = new TelemetryClient();

TelemetryClient 具備執行緒安全。

我們建議使用的執行個體 `TelemetryClient` 的應用程式的每個模組。 比方說，您可能有一個 `TelemetryClient` 報表商務邏輯的活動報告內送 http 要求和中介軟體類別中的另一個 web 服務中。 您可以設定屬性，例如 `TelemetryClient.Context.User.Id` 來追蹤使用者和工作階段，或 `TelemetryClient.Context.Device.Id` 來識別機器。 這項資訊會附加至執行個體所傳送的所有事件。


## 追蹤事件

在 Application Insights *自訂事件* 是資料點，兩者都顯示在中 [計量瀏覽器 ][metrics] 為彙總的計數，以及個別項目中 [診斷搜尋 ][diagnostic]。 (它與 MVC 或其他架構的「事件」不相關。)

在您的程式碼中插入 TrackEvent 呼叫，以計算使用者選擇特定功能的頻率、達成特定目標的頻率，或可能製造特定類型的錯誤。

例如，在遊戲應用程式中，每當使用者贏得遊戲時傳送事件：

*JavaScript*

    appInsights.trackEvent("WinGame");

*C#*

    telemetry.TrackEvent("WinGame");

*VB*


    telemetry.TrackEvent("WinGame")

*Java*

    telemetry.trackEvent("WinGame");

這裡的 "WinGame" 是出現在 Application Insights 入口網站的名稱。

若要查看您的事件計數，請開啟 [計量瀏覽器](app-insights-metrics-explorer.md) 刀鋒視窗中，加入新的圖表，然後選取事件。

![](./media/app-insights-api-custom-events-metrics/01-custom.png)

若要比較不同事件的計數，請將圖表類型設為 [方格]，並依事件名稱進行分組：

![](./media/app-insights-api-custom-events-metrics/07-grid.png)


在方格中逐一點選事件名稱，以查看該事件的個別發生次數。

![鑽研事件](./media/app-insights-api-custom-events-metrics/03-instances.png)

按一下任何發生以查看詳細資料。

若要在「搜尋」或「計量瀏覽器」中專注查看特定事件，請將刀鋒視窗篩選器設為您感興趣的事件名稱：

![開啟 ](./media/app-insights-api-custom-events-metrics/06-filter.png)

## 追蹤度量

使用 TrackMetric 傳送未附加至特定事件的度量。 例如，您可以定期監視佇列長度。

度量會在計量瀏覽器中顯示為統計圖表，但不同於事件，您不能搜尋診斷搜尋中的個別發生次數。

度量值應該 > = 0，才能正確顯示。


*JavaScript*

    appInsights.trackMetric("Queue", queue.Length);

*C#*

    telemetry.TrackMetric("Queue", queue.Length);

*VB*

    telemetry.TrackMetric("Queue", queue.Length)

*Java*

    telemetry.trackMetric("Queue", queue.Length);

事實上，您可能會在背景執行緒中執行這個動作：

*C#*

    private void Run() {
     var appInsights = new TelemetryClient();
     while (true) {
      Thread.Sleep(60000);
      appInsights.TrackMetric("Queue", queue.Length);
     }
    }

若要查看結果，開啟 [計量瀏覽器] 並加入新的圖表。 將它設定為顯示您的度量。

![加入新的圖表或選取圖表，並在 ](./media/app-insights-api-custom-events-metrics/03-track-custom.png)

有一些 [度量的數目限制](#limits) 您可以使用。

## 頁面檢視

在裝置或網頁應用程式中，每個畫面或頁面載入時預設會傳送頁面檢視遙測。 但是，您可以變更為在其他或不同的時間追蹤頁面檢視。 例如，在顯示索引標籤或刀鋒視窗的應用程式中，您可能想要在使用者每次開啟新的刀鋒視窗時追蹤「頁面」。

![[概觀] 刀鋒視窗上的使用方式透鏡](./media/app-insights-api-custom-events-metrics/appinsights-47usage-2.png)

使用者和工作階段資料會與頁面檢視一起傳送為屬性，當有頁面檢視遙測時，讓使用者與工作階段圖表顯現。

#### 自訂頁面檢視

*JavaScript*

    appInsights.trackPageView("tab1");

*C#*

    telemetry.TrackPageView("GameReviewPage");

*VB*

    telemetry.TrackPageView("GameReviewPage")

如果您在不同的 HTML 網頁內有數個索引標籤，您也可以指定 URL：

    appInsights.trackPageView("tab1", "http://fabrikam.com/page1.htm");

## 追蹤要求

由伺服器 SDK 用來記錄 HTTP 要求。

如果您想要在沒有 Web 服務模組執行的內容中模擬要求，您也可以自行呼叫。

*C#*

    // At start of processing this request:
    
    // Operation Id and Name are attached to all telemetry and help you identify
    // telemetry associated with one request:
    telemetry.Context.Operation.Id = Guid.NewGuid().ToString();
    telemetry.Context.Operation.Name = requestName;
    
    var stopwatch = System.Diagnostics.Stopwatch.StartNew();
    
    // ... process the request ...
    
    stopwatch.Stop();
    telemetryClient.TrackRequest(requestName, DateTime.Now,
       stopwatch.Elapsed, 
       "200", true);  // Response code, success

## 追蹤例外狀況

傳送例外狀況至 Application Insights: 以 [計算 ][metrics], ，做為有問題; 以及頻率的指示 [檢查個別發生次數 ][diagnostic]。 報告包含堆疊追蹤。

*C#*

    try
    {
        ...
    }
    catch (Exception ex)
    {
       telemetry.TrackException(ex);
    }

*JavaScript*

    try
    {
       ...
    }
    catch (ex)
    {
       appInsights.trackException(ex);
    }

SDK 將自動攔截許多例外狀況，所以您不一定需要明確呼叫 TrackException。

* ASP.NET: [撰寫程式碼來攔截例外狀況](app-insights-asp-net-exceptions.md)
* J2EE: [會自動攔截例外狀況](app-insights-java-get-started.md#exceptions-and-request-failures)
* Windows 應用程式: [自動偵測到損毀](app-insights-windows-crashes.md)
* JavaScript：自動攔截。 如果您想要停用自動收集，請在您插入網頁的程式碼片段中加入一行：

    ```
    ({
      instrumentationKey: "your key"
      , disableExceptionTracking: true
    })
    ```


## 追蹤

使用此選項可協助您藉由將 'breadcrumb trail' 傳送至 Application Insights 來診斷問題。 您可以傳送診斷資料區塊，並檢查其 [診斷搜尋 ][diagnostic]。



[記錄配接器 ][trace] 使用此 API 將第三方記錄傳送至入口網站。


*C#*

    telemetry.TrackTrace(message, SeverityLevel.Warning, properties);

大小限制 `訊息` 比屬性上的限制高得多。 您可以搜尋訊息內容，但是 (不同於屬性值) 您無法在其中進行篩選。

## 追蹤相依性

您可以使用這個呼叫來追蹤回應時間以及呼叫外部程式碼片段的成功率。 結果會出現在入口網站中的相依性圖表中。

```C#

            var success = false;
            var startTime = DateTime.UtcNow;
            var timer = System.Diagnostics.Stopwatch.StartNew();
            try
            {
                success = dependency.Call();
            }
            finally
            {
                timer.Stop();
                telemetry.TrackDependency("myDependency", "myCall", startTime, timer.Elapsed, success);
            }
```

請記住，伺服器 Sdk 包含 [的相依性模組](app-insights-dependencies.md) ，探索和追蹤特定相依性呼叫自動-例如資料庫和 REST Api。 您必須在伺服器上安裝代理程式才能讓模組正常運作。 如果您想要追蹤不會由自動化追蹤攔截的呼叫，或不想安裝代理程式，您可以使用這個呼叫。

若要關閉標準的相依性追蹤模組，請編輯 [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md) 和刪除參考 `DependencyCollector.DependencyTrackingTelemetryModule`。



## 排清資料

通常 SDK 會在選擇的時間傳送資料以將對使用者的影響降到最低。 不過，在某些情況下您可能想要排清緩衝區，例如，如果您在會關閉的應用程式中使用 SDK。

*C#*

    telemetry.Flush();
    
    // Allow some time for flushing before shutdown.
    System.Threading.Thread.Sleep(1000);

請注意，函式是非同步的記憶體中的通道，但如果您選擇使用同步 [持續通道](app-insights-windows-desktop.md#persistence-channel)。


## 通過驗證的使用者

在 Web app 中，預設是透過 Cookie 來識別使用者。 如果使用者從不同的電腦或瀏覽器存取您的 app 或刪除 Cookie，就可能多次計算他們。

但是，如果使用者登入您的 app，您可以藉由在瀏覽器程式碼中設定經過驗證的使用者識別碼，來取得更正確的計數。

*JavaScript*

```JS
    // Called when my app has identified the user.
    function Authenticated(signInId) {
      var validatedId = signInId.replace(/[,;=| ]+/g, "_");
      appInsights.setAuthenticatedUserContext(validatedId);
      ...
    }
```

在 ASP.NET Web MVC 應用程式，例如：

*Razor*

        @if (Request.IsAuthenticated)
        {
            <script>
                appInsights.setAuthenticatedUserContext("@User.Identity.Name".replace(/[,;=| ]+/g, "_"));
            </script>
        }

這不需要用到使用者的實際登入名稱。 只需是使用者的唯一識別碼。 它不能包含空格，或任何字元 `,，; = |`。

使用者識別碼也會設定於工作階段 Cookie 中，並傳送到伺服器。 如果安裝了伺服器 SDK，則會傳送經過驗證的使用者識別碼以做為用戶端和伺服器遙測的內容屬性一部分，讓您能夠對它進行篩選和搜尋。

如果您的 app 會將使用者群組為帳戶，您也可以傳遞該帳戶的識別碼 (具有相同的字元限制)。


      appInsights.setAuthenticatedUserContext(validatedId, accountId);

在 [計量瀏覽器](app-insights-metrics-explorer.md), ，您可以建立圖表，以計算 **驗證的使用者，** 和 **使用者帳戶**。

您也可以 [搜尋 ][diagnostic] 含有特定的使用者名稱和帳戶的用戶端資料點。

## <a name="properties"></a>篩選、 搜尋和分割您的資料屬性

您可以將屬性和測量結果附加至您的事件 (同時還有度量，頁面檢視、例外狀況和其他的遙測資料)。

**屬性**是可在使用情況報告中用來篩選遙測的字串值。 例如，如果您的應用程式提供數個遊戲，則您可以將遊戲的名稱附加至每個事件，以了解哪些遊戲較受歡迎。

字串長度有 1k 的限制。 (如果您想要傳送大量的資料區塊，使用訊息參數的 [TrackTrace](#track-trace).)

**度量**是可以用圖表方式呈現的數值。 例如，您可能想要查看玩家達到的分數是否逐漸增加。 圖表可以依據隨事件傳送的屬性分割，讓您可以針對不同遊戲取得個別或堆疊圖表。

度量值應該 > = 0，才能正確顯示。


有一些 [屬性、 屬性值和度量的數目限制](#limits) ，您可以使用。


*JavaScript*

    appInsights.trackEvent
      ("WinGame",
         // String properties:
         {Game: currentGame.name, Difficulty: currentGame.difficulty},
         // Numeric metrics:
         {Score: currentGame.score, Opponents: currentGame.opponentCount}
         );
    
    appInsights.trackPageView
        ("page name", "http://fabrikam.com/pageurl.html",
          // String properties:
         {Game: currentGame.name, Difficulty: currentGame.difficulty},
         // Numeric metrics:
         {Score: currentGame.score, Opponents: currentGame.opponentCount}
         );

*C#*

    // Set up some properties and metrics:
    var properties = new Dictionary <string, string> 
       {{"game", currentGame.Name}, {"difficulty", currentGame.Difficulty}};
    var metrics = new Dictionary <string, double>
       {{"Score", currentGame.Score}, {"Opponents", currentGame.OpponentCount}};
    
    // Send the event:
    telemetry.TrackEvent("WinGame", properties, metrics);

*VB*

    ' Set up some properties:
    Dim properties = New Dictionary (Of String, String)
    properties.Add("game", currentGame.Name)
    properties.Add("difficulty", currentGame.Difficulty)
    
    Dim metrics = New Dictionary (Of String, Double)
    metrics.Add("Score", currentGame.Score)
    metrics.Add("Opponents", currentGame.OpponentCount)
    
    ' Send the event:
    telemetry.TrackEvent("WinGame", properties, metrics)

*Java*

    Map<String, String> properties = new HashMap<String, String>();
    properties.put("game", currentGame.getName());
    properties.put("difficulty", currentGame.getDifficulty());
    
    Map<String, Double> metrics = new HashMap<String, Double>();
    metrics.put("Score", currentGame.getScore());
    metrics.put("Opponents", currentGame.getOpponentCount());
    
    telemetry.trackEvent("WinGame", properties, metrics);

> [AZURE.NOTE] 切勿在屬性中記錄個人識別資訊。

**如果您使用度量**，請開啟 [計量瀏覽器]，然後從自訂群組中選取度量：

![開啟計量瀏覽器，選取圖表，並選取度量](./media/app-insights-api-custom-events-metrics/03-track-custom.png)

* 如果您的度量未顯示，或自訂標題不存在，請關閉選取刀鋒視窗，稍後再試。 有時候可能需要透過 pipeline.* 要彙總度量一小時

**如果您使用屬性和度量**，依據屬性分割度量：


![設定群組，然後在 ](./media/app-insights-api-custom-events-metrics/04-segment-metric-event.png)



**在「診斷搜尋」中**，您可以檢視事件個別發生次數的屬性和度量。


![選取執行個體，然後選取 ](./media/app-insights-api-custom-events-metrics/appinsights-23-customevents-4.png)


使用 [搜尋] 欄位來查看具有特定屬性值的事件出現次數。


![將詞彙輸入 ](./media/app-insights-api-custom-events-metrics/appinsights-23-customevents-5.png)

[深入了解搜尋運算式 ][diagnostic]。

#### 設定屬性和度量的替代方式

如果更加方便，您可以收集個別物件中事件的參數：

    var event = new EventTelemetry();
    
    event.Name = "WinGame";
    event.Metrics["processingTime"] = stopwatch.Elapsed.TotalMilliseconds;
    event.Properties["game"] = currentGame.Name;
    event.Properties["difficulty"] = currentGame.Difficulty;
    event.Metrics["Score"] = currentGame.Score;
    event.Metrics["Opponents"] = currentGame.Opponents.Length;
    
    telemetry.TrackEvent(event);

#### <a name="timed"></a> 計時事件

有時候您想要繪製執行某些動作耗費多少時間的圖表。 例如，您可能想要知道使用者在遊戲中思考選項時花費多少時間。 這是使用測量參數的實用範例。


*C#*

    var stopwatch = System.Diagnostics.Stopwatch.StartNew();
    
    // ... perform the timed action ...
    
    stopwatch.Stop();
    
    var metrics = new Dictionary <string, double>
       {{"processingTime", stopwatch.Elapsed.TotalMilliseconds}};
    
    // Set up some properties:
    var properties = new Dictionary <string, string> 
       {{"signalSource", currentSignalSource.Name}};
    
    // Send the event:
    telemetry.TrackEvent("SignalProcessed", properties, metrics);

## <a name="defaults"></a>自訂遙測的預設屬性

如果您想為您撰寫的一些自訂事件設定預設屬性值，您可以在 TelemetryClient 中設定它們。 它們會附加至從該用戶端傳送的每個遙測項目。

*C#*

    using Microsoft.ApplicationInsights.DataContracts;
    
    var gameTelemetry = new TelemetryClient();
    gameTelemetry.Context.Properties["Game"] = currentGame.Name;
    // Now all telemetry will automatically be sent with the context property:
    gameTelemetry.TrackEvent("WinGame");

*VB*

    Dim gameTelemetry = New TelemetryClient()
    gameTelemetry.Context.Properties("Game") = currentGame.Name
    ' Now all telemetry will automatically be sent with the context property:
    gameTelemetry.TrackEvent("WinGame")

*Java*

    import com.microsoft.applicationinsights.TelemetryClient;
    import com.microsoft.applicationinsights.TelemetryContext;
    ...
    
    
    TelemetryClient gameTelemetry = new TelemetryClient();
    TelemetryContext context = gameTelemetry.getContext();
    context.getProperties().put("Game", currentGame.Name);
    
    gameTelemetry.TrackEvent("WinGame");

個別遙測呼叫可以覆寫其屬性字典中的預設值。

**的 JavaScript web 用戶端**, ，[使用 JavaScript 遙測初始設定式](#js-initializer)。

**將內容新增至所有遙測** 包括標準集合模組中的資料 [建立的遙測初始設定式](app-insights-api-filtering-sampling.md#add-properties)。


## 取樣、篩選及處理遙測資料

您可以撰寫程式碼，在從 SDK 傳送遙測資料前加以處理。 處理包括從標準遙測模組 (如 HTTP 要求收集和相依性收集) 的資料。

* [將屬性加入](app-insights-api-filtering-sampling.md#add-properties) 遙測資料-例如，版本號碼值計算或從其他屬性。
* [取樣](app-insights-api-filtering-sampling.md#sampling) 可減少資料從您的應用程式傳送至入口網站中，而不會影響顯示的度量，而不會影響您診斷問題，例如例外狀況的相關項目之間巡覽的能力，要求和頁面檢視。
* [篩選](app-insights-api-filtering-sampling.md#filtering) 也會減少磁碟區。 您可控制要傳送或捨棄的項目，但是您必須考量這對您的度量的影響。 視您捨棄項目的方式而定，您可能會喪失在相關項目之間瀏覽的能力。

[了解更多](app-insights-api-filtering-sampling.md)


## 停用遙測

**動態停止和開始**收集及傳輸遙測資料：

*C#*

```C#

    using  Microsoft.ApplicationInsights.Extensibility;

    TelemetryConfiguration.Active.DisableTelemetry = true;
```

若要 **停用選取的標準收集器** -例如，效能計數器、 HTTP 要求或相依性-刪除或註解中相關的程式行會 [ApplicationInsights.config][config]。 例如，如果您想要傳送自己的 TrackRequest 資料，可以這麼做。

## <a name="debug"></a>開發人員模式

偵錯期間，讓您的遙測透過管線加速很有用，如此您就可以立即看到結果。 您也會取得額外的訊息，協助您追蹤任何遙測的問題。 在生產環境中將它關閉，因為它可能會拖慢您的應用程式。


*C#*

    TelemetryConfiguration.Active.TelemetryChannel.DeveloperMode = true;

*VB*

    TelemetryConfiguration.Active.TelemetryChannel.DeveloperMode = True

## <a name="ikey"></a> 設定已選取自訂遙測的檢測金鑰

*C#*

    var telemetry = new TelemetryClient();
    telemetry.Context.InstrumentationKey = "---my key---";
    // ...

## <a name="dynamic-ikey"></a> 動態檢測金鑰

若要避免混合來自開發、 測試和生產環境的遙測，您可以 [建立個別 Application Insights 資源 ][create] 並變更其金鑰的環境而定。

而不是從組態檔取得檢測金鑰，您可以在程式碼中設定。 在初始化方法中設定金鑰，例如 ASP.NET 服務中的 global.aspx.cs：

*C#*

    protected void Application_Start()
    {
      Microsoft.ApplicationInsights.Extensibility.
        TelemetryConfiguration.Active.InstrumentationKey = 
          // - for example -
          WebConfigurationManager.Settings["ikey"];
      ...

*JavaScript*

    appInsights.config.instrumentationKey = myKey; 

在網頁中，您可能想要從 Web 伺服器的狀態設定，而不是按其原義編碼至指令碼。 例如，在 ASP.NET 應用程式中產生的網頁：

*Razor 中的 JavaScript*

    <script type="text/javascript">
    // Standard Application Insights web page script:
    var appInsights = window.appInsights || function(config){ ...
    // Modify this part:
    }({instrumentationKey:  
      // Generate from server property:
      @Microsoft.ApplicationInsights.Extensibility.
         TelemetryConfiguration.Active.InstrumentationKey"
    }) // ...

## TelemetryContext

TelemetryClient 具有內容屬性，其中包含與所有遙測資料一起傳送的值數目。 它們通常由標準遙測模組設定，但是您也可以自行設定它們。 例如：

    telemetryClient.Context.Operation.Name = "MyOperationName";

如果您設定這些值自己，請考慮從相關的程式碼行移除 [ApplicationInsights.config][config], ，好讓您的值和標準值才不致混淆。

* **元件** 識別應用程式及其版本
* **裝置** 應用程式執行所在的裝置的相關資料 (在 Web 應用程式中，這是傳送遙測的伺服器或用戶端裝置)
* **InstrumentationKey** 識別 Azure 中遙測顯示之位置的 Application Insights 資源。 通常會從 ApplicationInsights.config 揀選
* **位置** 識別裝置的地理位置。
* **作業** 在 Web 應用程式中，目前的 HTTP 要求。 在其他應用程式類型中，您可以設定以將事件群組在一起。
 * **識別碼**：產生的值，與不同事件相互關聯，如此當您在 [診斷搜尋] 中檢查任何事件時，您可以發現「相關項目」
 * **名稱**：識別碼，通常是 HTTP 要求的 URL。
 * **SyntheticSource**：如果不為 null 或空白，這個字串表示要求的來源已被識別為傀儡程式或 Web 測試。 根據預設，會從計量瀏覽器的計算中排除。
* **屬性** 與所有遙測資料一起傳送的屬性。 可以在個別 Track* 呼叫中覆寫。
* **工作階段** 識別使用者的工作階段。 識別碼會設為產生的值，當使用者一段時間沒有作用時會變更。
* **使用者** 使用者資訊。



## 限制

每個應用程式 (亦即每個檢測金鑰) 都有一些度量和事件的數目限制。

1. 個別套用至每個檢測金鑰的每秒最大速率。 若超過限制，則系統會捨棄部分資料。
 * 針對 TrackTrace 呼叫與已擷取記錄資料的每秒資料點數量上限為 500 個。 (針對免費定價層則為每秒 100 個。)
 * 針對依我們模組或 TrackException 呼叫擷取的例外狀況，每秒資料點數量上限為 50 個。
 * 針對其他所有資料 (包括依 SDK 模組傳送的標準遙測，以及依您程式碼傳送的自訂事件、度量和其他遙測資料)，則每秒資料點數量上限為 500 個。 (針對免費定價層則為每秒 100 個。)
1. 每月的總數量的資料，取決於您 [定價層](app-insights-pricing.md)。
1.  您的應用程式具有最多 200 個唯一度量名稱和 200 個唯一屬性名稱。 度量包括透過 TrackMetric，以及其他資料類型，例如事件上的度量單位傳送的資料。 每個檢測金鑰的度量和屬性名稱是全域的，不只限於資料類型。
2.  只有在每個屬性具有少於 100 個唯一值時，屬性才能用於篩選和分組依據。 唯一值超過 100 之後，屬性仍可用於搜尋與篩選，但無法用於篩選器。
3.  標準屬性，例如要求名稱和網頁 URL 會限制為每週 1000 個唯一值。 超過 1000 個唯一值之後，額外值都會標示為「其他值」。 原始值仍然可以用於全文檢索搜尋和篩選。

*如何避免達到資料速率限制？*

* 安裝最新的 SDK，即可使用 [取樣](app-insights-sampling.md)。

*資料保留多久？*

* 請參閱 [資料保留和隱私權 ][data]。


## 參考文件

* [ASP.NET 參考](https://msdn.microsoft.com/library/dn817570.aspx)
* [Java 參考](http://dl.windowsazure.com/applicationinsights/javadoc/)
* [JavaScript 參考](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md)
* [Android SDK](https://github.com/Microsoft/ApplicationInsights-Android)
* [SDK iOS](https://github.com/Microsoft/ApplicationInsights-iOS)


## SDK 程式碼

* [ASP.NET 核心 SDK](https://github.com/Microsoft/ApplicationInsights-dotnet)
* [ASP.NET 5](https://github.com/Microsoft/ApplicationInsights-aspnet5)
* [Android SDK](https://github.com/Microsoft/ApplicationInsights-Android)
* [Java SDK](https://github.com/Microsoft/ApplicationInsights-Java)
* [JavaScript SDK](https://github.com/Microsoft/ApplicationInsights-JS)
* [SDK iOS](https://github.com/Microsoft/ApplicationInsights-iOS)
* [所有平台](https://github.com/Microsoft?utf8=%E2%9C%93&query=applicationInsights)

## 問題

* *Track_() 呼叫會擲回什麼例外狀況？*

    無。 您不需要將它們包裝在 try-catch 子句中。 如果 SDK 發生問題，它會記錄訊息，您可以在偵錯主控台輸出中查看。若訊息得以留存，也可在診斷搜尋中查看。

* *是否有 REST API？*

    是，但我們尚未發佈。

## <a name="next"></a>後續步驟

[搜尋事件和記錄檔 ][diagnostic]

[範例和逐步解說](app-insights-code-samples.md)

[疑難排解 ][qna]





[client]: app-insights-javascript.md 
[config]: app-insights-configuration-with-applicationinsights-config.md 
[create]: app-insights-create-new-resource.md 
[data]: app-insights-data-retention-privacy.md 
[diagnostic]: app-insights-diagnostic-search.md 
[exceptions]: app-insights-asp-net-exceptions.md 
[greenbrown]: app-insights-asp-net.md 
[java]: app-insights-java-get-started.md 
[metrics]: app-insights-metrics-explorer.md 
[qna]: app-insights-troubleshoot-faq.md 
[trace]: app-insights-search-diagnostic-logs.md 
[windows]: app-insights-windows-get-started.md 

