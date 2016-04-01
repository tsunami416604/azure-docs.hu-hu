<properties 
    pageTitle="使用 ApplicationInsights.config 或 .xml 設定 Application Insights SDK" 
    description="啟用或停用資料收集模組，以及加入效能計數器和其他參數" 
    services="application-insights"
    documentationCenter="" 
    authors="OlegAnaniev-MSFT"
    editor="alancameronwills" 
    manager="douge"/>
 
<tags 
    ms.service="application-insights" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="ibiza" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="11/05/2015" 
    ms.author="awills"/>

# 使用 ApplicationInsights.config 或 .xml 設定 Application Insights SDK

Application Insights .NET SDK 是由數個 NuGet 封裝所組成。 Auch die Eigenschaften 
[核心封裝](http://www.nuget.org/packages/Microsoft.ApplicationInsights) 傳送遙測，以提供 API 
Application Insights。 [其他封裝](http://www.nuget.org/packages?q=Microsoft.ApplicationInsights) 提供 
遙測 _模組_ 和 _初始設定式_ 自動從您的應用程式和其內容追蹤遙測。 依據 
調整組態檔，您可以啟用或停用遙測模組和初始設定式，並設定參數 
有。

組態檔的名稱 `ApplicationInsights.config` 或 `ApplicationInsights.xml`, 的類型而定，您
重新接收。 它會自動加入至您的專案時您 [安裝大部分的 SDK 版本][start]。 它也會加入至 web 應用程式 
由 [IIS 伺服器上的狀態監視器][redfield], ，或當您選取應用程式見解 
[Azure 網站或 VM 延伸模組][azure]。

沒有同等的檔案可以控制 [在網頁上的 SDK][client]。

本文件說明您在組態檔中看到的內容、控制 SDK 元件的方式，以及哪些 NuGet 封裝載入這些元件。

## 遙測模組 (ASP.NET)

每個遙測模組收集特定類型的資料，以及使用核心 API 來傳送資料。 模組由不同的 NuGet 封裝安裝，也會將必要的行加入 .config 檔案。

組態檔中的每個模組都有一個節點。 若要停用模組，請刪除節點或將其註解化。



### 相依性追蹤

[相依性追蹤](app-insights-dependencies.md) 會收集有關您的應用程式對資料庫和外部服務和資料庫的呼叫遙測。 若要允許此模組用於 IIS 伺服器，您需要 [安裝狀態監視器][redfield]。 若要使用 Azure web 應用程式或 Vm， [選取 Application Insights 延伸模組][azure]。

您也可以撰寫您自己的相依性追蹤程式碼使用 [TrackDependency API](app-insights-api-custom-events-metrics.md#track-dependency)。


* `Microsoft.ApplicationInsights.DependencyCollector.DependencyTrackingTelemetryModule`
* [Microsoft.ApplicationInsights.DependencyCollector](http://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector) NuGet 封裝。

### 效能收集器

[收集系統效能計數器](app-insights-web-monitor-performance.md#system-performance-counters) 例如 CPU、 記憶體和網路負載從 IIS 安裝。 您可以指定要收集哪些計數器，包括您自己所設定的效能計數器。

* `Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule`
* [Microsoft.ApplicationInsights.PerfCounterCollector](http://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector) NuGet 封裝。


### Application Insights 診斷遙測

`DiagnosticsTelemetryModule` 報告 Application Insights 檢測程式碼本身中的錯誤。 例如， 
如果程式碼無法存取效能計數器或 `ITelemetryInitializer` 擲回例外狀況。 追蹤遙測 
追蹤此模組會出現在 [診斷搜尋][diagnostic]。
 
* `Microsoft.ApplicationInsights.Extensibility.Implementation.Tracing.DiagnosticsTelemetryModule`
* [Microsoft.ApplicationInsights](http://www.nuget.org/packages/Microsoft.ApplicationInsights) NuGet 封裝。 如果您只安裝這個封裝，不會自動建立 ApplicationInsights.config 檔案。 

### 開發人員模式

`DeveloperModeWithDebuggerAttachedTelemetryModule` 會強制 Application Insights `TelemetryChannel` 立即傳送資料，在偵錯工具附加至應用程式程序時一次傳送一個遙測項目。 這會減少您的應用程式追蹤遙測時，與當遙測出現在 Application Insights 入口網站時之間的時間量。 但是會造成 CPU 和網路頻寬明顯的負擔。

* `Microsoft.ApplicationInsights.WindowsServer.DeveloperModeWithDebuggerAttachedTelemetryModule`
* [應用程式了解 Windows Server](http://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/) NuGet 封裝

### Web 要求追蹤

報表 [回應時間與結果碼](app-insights-asp-net.md) 的 HTTP 要求。 

* `Microsoft.ApplicationInsights.Web.RequestTrackingTelemetryModule`
* [Microsoft.ApplicationInsights.Web](http://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) NuGet 封裝

### 例外狀況追蹤

`ExceptionTrackingTelemetryModule` 追蹤 Web 應用程式中未處理的例外狀況。 請參閱 [失敗和例外狀況][exceptions]。

* `Microsoft.ApplicationInsights.Web.ExceptionTrackingTelemetryModule`
* [Microsoft.ApplicationInsights.Web](http://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) NuGet 封裝


* `Microsoft.ApplicationInsights.WindowsServer.UnobservedExceptionTelemetryModule` -追蹤 [工作例外狀況未觀察到](http://blogs.msdn.com/b/pfxteam/archive/2011/09/28/task-exception-handling-in-net-4-5.aspx)。
* `Microsoft.ApplicationInsights.WindowsServer.UnhandledExceptionTelemetryModule` - 追蹤背景工作角色、Windows 服務和主控台應用程式的未處理例外狀況。
* [應用程式了解 Windows Server](http://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/) NuGet 封裝。

### 核心 API

核心封裝提供 [核心 API](https://msdn.microsoft.com/library/mt420197.aspx) 的 sdk。 其他的遙測模組使用此功能，以及您也可以 [使用它來定義您自己的遙測](app-insights-api-custom-events-metrics.md)。

* ApplicationInsights.config 中沒有項目。
* [Microsoft.ApplicationInsights](http://www.nuget.org/packages/Microsoft.ApplicationInsights) NuGet 封裝。 如果您只安裝此 NuGet，不會產生任何 .config 檔案。

## 遙測通道

遙測通道管理遙測的緩衝處理和到 Application Insights 服務的傳輸。 

* `Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel` 是服務的預設通道。 它會在記憶體中緩衝資料。
* `Microsoft.ApplicationInsights.PersistenceChannel` 是主控台應用程式的替代通道。 它可以在您的 app 關閉時將任何未清除的資料儲存到永續性儲存體，並在 app 重新啟動時再次傳送資料。


## 遙測初始設定式 (ASP.NET)

遙測初始設定式設定與遙測的每個項目一起傳送的內容屬性。 

您可以 [撰寫您自己的初始設定式](app-insights-api-filtering-sampling.md#add-properties) 設定內容屬性。

標準的初始設定式已由 Web 或 WindowsServer NuGet 封裝設定：


* `AccountIdTelemetryInitializer` 設定 AccountId 屬性。
* `AuthenticatedUserIdTelemetryInitializer` 如 JavaScript SDK 設定般設定 AuthenticatedUserId 屬性。
* 針對具有從 Azure 執行階段環境擷取之資訊的所有遙測項目，`AzureRoleEnvironmentTelemetryInitializer` 會更新 `Device` 內容的 `RoleName` 和 `RoleInstance` 屬性。
* 針對具有從 MS 組建所產生之 `BuildInfo.config` 檔案擷取值的所有遙測項目，`BuildInfoConfigComponentVersionTelemetryInitializer` 會更新 `Component` 內容的 `Version` 屬性。
* `ClientIpHeaderTelemetryInitializer` 會根據要求的 `X-Forwarded-For` HTTP 標頭來更新所有遙測項目之 `Location` 內容的 `Ip` 屬性。
* `DeviceTelemetryInitializer` 會更新所有遙測項目 `Device` 內容的下列屬性。
 - `Type` 設定為 "PC"
 - `Id` 設定為 Web 應用程式執行所在電腦的網域名稱。
 - `OemName` 設定為使用 WMI 從 `Win32_ComputerSystem.Manufacturer` 欄位擷取的值。
 - `Model` 設定為使用 WMI 從 `Win32_ComputerSystem.Model` 欄位擷取的值。
 - `NetworkType` 設定為從 `NetworkInterface` 擷取的值。
 - `Language` 設定為 `CurrentCulture` 的名稱。
* `DomainNameRoleInstanceTelemetryInitializer` 更新 `RoleInstance` 屬性 `Device` 所有內容
遙測項目使用的網域名稱的 web 應用程式執行所在的電腦。
* `OperationNameTelemetryInitializer` 會根據 HTTP 方法，以及 ASP.NET MVC 控制器的名稱和叫用來處理要求的動作，更新所有遙測項目 `RequestTelemetry` 之 `Name` 屬性和 `Operation` 內容的 `Name` 屬性。
* `OperationIdTelemetryInitializer` 在處理具有自動產生的 `RequestTelemetry.Id` 的要求時，會更新追蹤的所有遙測項目的 `Operation.Id` 內容屬性。
* 針對具有從使用者瀏覽器中執行的 Application Insights JavaScript 檢測程式碼所產生之 `ai_session` Cookie 擷取值的所有遙測項目，`SessionTelemetryInitializer` 會更新 `Session` 內容的 `Id` 屬性。 
* `SyntheticTelemetryInitializer` 在處理來自綜合來源 (例如可用性測試或搜尋引擎 Bot) 要求時，會更新追蹤之所有遙測項目的 `User`、`Session` 和 `Operation` 內容屬性。 根據預設， [計量瀏覽器](app-insights-metrics-explorer.md) 不會顯示綜合遙測。
* `UserAgentTelemetryInitializer` 會根據要求的 `User-Agent` HTTP 標頭來更新所有遙測項目之 `User` 內容的 `UserAgent` 屬性。
* 針對具有從使用者瀏覽器中執行之 Application Insights JavaScript 檢測程式碼所產生的 `ai_user` Cookie 擷取值的所有遙測項目，`UserTelemetryInitializer` 會更新 `User` 內容的 `Id` 和 `AcquisitionDate` 屬性。


## 遙測處理器 (ASP.NET)

遙測處理器可以在遙測從 SDK 傳送至入口網站之前篩選並修改每個遙測項目。

您可以 [撰寫您自己的遙測處理器](app-insights-api-filtering-sampling.md#filtering)。


#### 自適性取樣遙測處理器 (自 2.0.0-beta3 起)

此選項預設為啟用狀態。 如果您的應用程式傳送許多遙測，此處理器會移除其中一些遙測。

```xml

    <TelemetryProcessors>
      <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
        <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
      </Add>
    </TelemetryProcessors>

```

參數會提供演算法嘗試要達成的目標。 每個 SDK 執行個體都獨立運作，因此如果您的伺服器是數個機器的叢集，遙測的實際數量會隨之加乘。

[深入了解取樣](app-insights-sampling.md)。



#### 固定取樣遙測處理器 (自 2.0.0-beta1 起)

另外還有一種標準 [取樣遙測處理器](app-insights-api-filtering-sampling.md#sampling) （從 2.0.1):

```XML

    <TelemetryProcessors>
     <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.SamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">

     <!-- Set a percentage close to 100/N where N is an integer. -->
     <!-- E.g. 50 (=100/2), 33.33 (=100/3), 25 (=100/4), 20, 1 (=100/100), 0.1 (=100/1000) -->
     <SamplingPercentage>10</SamplingPercentage>
     </Add>
   </TelemetryProcessors>

```



## 通道參數 (Java)

這些參數會影響 Java SDK 應該儲存和排清它所收集之遙測資料的方式。

#### MaxTelemetryBufferCapacity

可以儲存在 SDK 記憶體內儲存空間中的遙測項目數。 達到這個數目時，會排清遙測緩衝區，也就是將遙測項目傳送至 Application Insights 伺服器。

-   最小值：1
-   最大值：1000
-   預設值：500

```

  <ApplicationInsights>
      ...
      <Channel>
       <MaxTelemetryBufferCapacity>100</MaxTelemetryBufferCapacity>
      </Channel>
      ...
  </ApplicationInsights>
```

#### FlushIntervalInSeconds 

決定應該以何種頻率排清儲存在記憶體內儲存空間的資料 (並傳送至 Application Insights)。

-   最小值：1
-   最大值：300
-   預設值：5

```

    <ApplicationInsights>
      ...
      <Channel>
        <FlushIntervalInSeconds>100</FlushIntervalInSeconds>
      </Channel>
      ...
    </ApplicationInsights>
```

#### MaxTransmissionStorageCapacityInMB

決定分配給本機磁碟上永續性存放裝置的大小上限 (MB)。 此存放裝置會用於保存無法傳送至 Application Insights 端點的遙測項目。 存放裝置大小達到上限時，會捨棄新的遙測項目。

-   最小值：1
-   最大值：100
-   預設值：10

```

   <ApplicationInsights>
      ...
      <Channel>
        <MaxTransmissionStorageCapacityInMB>50</MaxTransmissionStorageCapacityInMB>
      </Channel>
      ...
   </ApplicationInsights>
```



## InstrumentationKey

這會決定顯示您資料的 Application Insights 資源。 通常您會針對每個應用程式，用個別的金鑰建立個別資源。

如果想要以動態方式設定金鑰 (例如，想要將應用程式的結果傳送到不同的資源)，您可以在組態檔中省略金鑰，並將金鑰設定在程式碼中。

若要針對 TelemetryClient 的所有執行個體 (包括標準遙測模組) 設定金鑰，請在 TelemetryConfiguration.Active 中設定金鑰。 請在初始化方法中這麼做，例如 ASP.NET 服務中的 global.aspx.cs：

```C#

    protected void Application_Start()
    {
      Microsoft.ApplicationInsights.Extensibility.
        TelemetryConfiguration.Active.InstrumentationKey = 
          // - for example -
          WebConfigurationManager.Settings["ikey"];
      //...
```

如果您只想將特定的一組事件傳送至不同的資源，可以針對特定的 TelemetryClient 設定金鑰：

```C#

    var tc = new TelemetryClient();
    tc.Context.InstrumentationKey = "----- my key ----";
    tc.TrackEvent("myEvent");
    // ...

```

[深入了解 API][api]。

若要取得新的金鑰， [在 Application Insights 入口網站中建立新的資源][new]。

<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[azure]: ../insights-perf-analytics.md
[client]: app-insights-javascript.md
[diagnostic]: app-insights-diagnostic-search.md
[exceptions]: app-insights-asp-net-exceptions.md
[netlogs]: app-insights-asp-net-trace-logs.md
[new]: app-insights-create-new-resource.md
[redfield]: app-insights-monitor-performance-live-website-now.md
[start]: app-insights-overview.md



