<properties
    pageTitle="加入 Application Insights SDK 監視 ASP.NET App | Microsoft Azure"
    description="使用 Application Insights 分析內部部署或 Microsoft Azure Web 應用程式的使用情況、可用性和效能。"
    services="application-insights"
    documentationCenter=".net"
    authors="alancameronwills"
    manager="douge"/>

<tags
    ms.service="application-insights"
    ms.workload="tbd"
    ms.tgt_pltfrm="ibiza"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/04/2015"
    ms.author="awills"/>



# 加入 Application Insights SDK 監視 ASP.NET 應用程式

*Application Insights 目前僅供預覽。*

[AZURE.INCLUDE [app-insights-selector-get-started](../../includes/app-insights-selector-get-started.md)]


Visual Studio Application Insights 監視即時應用程式可協助您 [偵測及診斷效能問題和例外狀況 ][detect], ，和 [探索您的應用程式的方式使用 ][knowusers]。 它可以搭配各種不同的應用程式類型。 這適用於裝載在專屬內部部署 IIS 伺服器或 Azure VM 上的應用程式，以及 Azure Web 應用程式。



![範例效能監視圖表](./media/app-insights-start-monitoring-app-health-usage/10-perf.png)

*另請參閱：*

* [ASP.NET 5](app-insights-asp-net-five.md)
* [裝置應用程式和 Java 伺服器 ][platforms]

#### 開始之前

對於許多應用程式類型， [Visual Studio 可以加入您的應用程式的 Application Insights](#ide) 幾乎您不會察覺。 為了讓您清楚了解整個運作情形，本文將帶您手動完成這些步驟。


您需要：

* 若要訂閱 [Microsoft Azure](http://azure.com)。 如果您的小組或組織擁有 Azure 訂用帳戶，擁有者可以將您加入它，使用您 [Microsoft 帳戶](http://live.com)。
* Visual Studio 2013 或更新版本。

## <a name="add"></a> 1.建立 Application Insights 資源

登入 [Azure 入口網站 ][portal], ，並建立新的 Application Insights 資源。 選擇 ASP.NET 做為應用程式類型。

![按一下 ](./media/app-insights-start-monitoring-app-health-usage/01-new-asp.png)

A [資源 ][roles] 在 Azure 中是服務的執行個體。 此資源是來自您應用程式的遙測將經過分析並呈現的地方。

應用程式類型的選擇會設定的資源刀鋒視窗和屬性的預設內容顯示在 [計量瀏覽器 ][metrics]。

#### 複製檢測金鑰

該金鑰識別資源，您很快就會將它安裝在 SDK 中，以將資源導向資料。

![按一下 ](./media/app-insights-start-monitoring-app-health-usage/02-props-asp.png)

您剛才所完成用來建立新資源的步驟是開始監視任何應用程式的好方法。 現在您可以將資料傳送給它。

## <a name="sdk"></a> 2.在應用程式中安裝 SDK

安裝和設定 Application Insights SDK 會視您正在使用的平台而有所不同。 對於 ASP.NET 應用程式而言，這非常輕鬆。

1. 在 Visual Studio 中，編輯 Web 應用程式專案的 NuGet 封裝。

    ![以滑鼠右鍵按一下專案，然後選取 ](./media/app-insights-start-monitoring-app-health-usage/03-nuget.png)

2. 安裝 Web Apps 適用的 Application Insights SDK。

    ![搜尋「Application Insights」](./media/app-insights-start-monitoring-app-health-usage/04-ai-nuget.png)

3. 編輯 ApplicationInsights.config (已由 NuGet 安裝加入)。 在結尾標記前面插入此內容：

    `< InstrumentationKey >` *您複製的檢測金鑰* `< / InstrumentationKey >`

    (或者，您可以 [撰寫一些程式碼 ][apikey] 應用程式中。)

#### 若要升級至未來的 SDK 版本

我們隨時會發行新版的 SDK。

若要升級至 [新版的 SDK](app-insights-release-notes-dotnet.md), ，再次開啟 NuGet 封裝管理員，並篩選出已安裝的封裝。 選取 **Microsoft.ApplicationInsights.Web** 然後選擇 [升級]****。

如果您已對 ApplicationInsights.config 進行任何的自訂，請在升級前儲存複本，並在升級後合併您的變更到新版本中。


## <a name="run"></a> 3.執行專案

使用 **F5** 執行應用程式並立即試用：開啟不同的頁面來產生一些遙測。

在 Visual Studio 中，您可以看見已傳送到的事件計數。

![](./media/app-insights-start-monitoring-app-health-usage/appinsights-09eventcount.png)

## <a name="monitor"></a> 4.檢視遙測

返回 [Azure 入口網站 ][portal] 並瀏覽至 Application Insights 資源。


在 [概觀] 圖表中尋找資料。 剛開始的時候，您只會看見一或兩個資料點。 例如：

![Click through to more data](./media/app-insights-start-monitoring-app-health-usage/12-first-perf.png)

逐一點選任何圖表以查看更詳細的度量。 [進一步了解度量。][perf]

#### 沒有資料？

* 使用應用程式、開啟不同頁面，以產生一些遙測。
* 開啟 [搜尋 ][diagnostic] ] 磚來查看個別事件。 有時候，事件通過計量管線所需的時間較長。
* 請稍等片刻，然後按一下 [重新整理****]。 圖表會定期自行重新整理，但是如果您在等待一些要顯示的資料，您可以手動重新整理。
* 請參閱 [疑難排解 ][qna]。

## 發佈您的應用程式

現在請將應用程式部署至 IIS 或 Azure，並觀看資料累積情形。

![使用 Visual Studio 來發佈您的應用程式](./media/app-insights-start-monitoring-app-health-usage/15-publish.png)

以偵錯模式執行時，系統會透過管線迅速傳送遙測資料，因此您應該可以在幾秒內看見資料。 以發行組態部署應用程式時，資料累積會較為緩慢。

#### 發佈資料到伺服器之後，卻沒有資料？

請在您的伺服器防火牆中，開啟這些連出流量的連接埠：

+ `dc.services.visualstudio.com:443`
+ `f5.services.visualstudio.com:443`


#### 組建伺服器發生問題？

請參閱 [此疑難排解項目](app-insights-troubleshoot-faq.md#NuGetBuild)。
> [AZURE.NOTE] 如果您的應用程式會產生大量遙測 (且您使用的是 ASP.NET SDK 版本 2.0.0-beta3 或較新)，調適性取樣模型會自動藉由僅傳送事件代表性片段，以減少傳送到入口網站的量。 不過，與同一個要求相關的事件會選取或取消選取為群組，讓您可以在相關事件之間瀏覽。 
> [深入了解取樣](app-insights-sampling.md)。


## 5.加入相依性追蹤 (和 IIS 效能計數器)

SDK 需要一些協助，才能取得某些資料的存取權。 特別是，您需要這個額外步驟才能自動測量您的應用程式對資料庫、REST API 和其他外部元件的呼叫。 這些相依性度量對於協助您診斷效能問題的價值難以衡量。

如果您在 IIS 伺服器上執行，這個步驟也可讓系統效能計數器，以顯示在 [計量瀏覽器](app-insights-metrics-explorer.md)。

#### 如果您的應用程式是在您的 IIS 伺服器中執行

登入您的伺服器與系統管理員權限，並安裝 [Application Insights 狀態監視器](http://go.microsoft.com/fwlink/?LinkId=506648)。

您可能需要 [在防火牆中開啟額外的傳出連接埠](app-insights-monitor-performance-live-website-now.md#troubleshooting)。

這個步驟也可讓 [報告的效能計數器](app-insights-web-monitor-performance.md#system-performance-counters) 例如 CPU、 記憶體、 網路佔用量。

#### 如果您的 app 是 Azure Web 應用程式

在您的 Azure Web 應用程式的控制台中，加入 Application Insights 延伸模組。

![在您的 Web 應用程式中，依序按一下 ](./media/app-insights-start-monitoring-app-health-usage/05-extend.png)


#### 如果是 Azure 雲端服務專案

[Web 和背景工作角色中加入指令碼](app-insights-cloudservices.md)。



## 6.加入用戶端監視

您已安裝會從應用程式的伺服器 (後端) 傳送遙測資料的 SDK。 現在您可以加入用戶端監視。 這可提供有關在瀏覽器中發生的使用者、工作階段、頁面檢視和任何例外狀況或損毀的相關資料。 您也可以撰寫自己的程式碼，來追蹤您的使用者如何使用您的應用程式，徹底得知詳細的點按和按鍵層級。


將 JavaScript 程式碼片段加入每一頁。 從您的 Application Insights 資源取得程式碼：

![在您的 Web 應用程式中，開啟 ](./media/app-insights-start-monitoring-app-health-usage/02-monitor-web-page.png)

請注意，此程式碼包含可識別您的應用程式資源的檢測金鑰。

[深入了解網頁追蹤。](app-insights-web-track-usage.md)


## 追蹤應用程式版本

請確定 `buildinfo.config` 產生您的 MSBuild 處理程序。 在您的 .csproj 檔案中加入：

```XML

    <PropertyGroup>
      <GenerateBuildInfoConfigFile>true</GenerateBuildInfoConfigFile>    <IncludeServerNameInBuildInfo>true</IncludeServerNameInBuildInfo>
    </PropertyGroup> 
```

當它有組建資訊時，Application Insights Web 模組會自動新增**應用程式版本**，做為每個遙測項目的屬性。 可讓您在執行時，依版本篩選 [診斷搜尋 ][diagnostic] 或 [探索度量 ][metrics]。

但請注意，組建版本號碼只能由 MS 組建產生，不是 Visual Studio 中的開發人員組建產生。

## 7.完成安裝

若要取得您的應用程式的全方位檢視，您還可以執行一些動作：

* [設定 web 測試的 ][availability] 以確定您的應用程式處於線上且能夠回應。
* [擷取記錄追蹤 ][netlogs] 從您最喜愛的記錄架構
* [追蹤自訂事件和度量 ][api] 用戶端或伺服器或兩者，若要深入了解如何使用您的應用程式。

## <a name="ide"></a> 自動化的方式

我們在這份文件的開頭提過，我們會告訴您如何以手動方式建立 Application Insights 資源，然後再安裝 SDK。 我們相信這可以協助您了解該程序的兩個部分。 但對於 ASP.NET 應用程式 (以及其他許多應用程式) 而言，還有更快速的自動化方式。

您將需要 [Visual Studio](http://go.microsoft.com/fwlink/?linkid=397827&clcid=0x409) (2013 update 3 或更新版本) 中的帳戶和 [Microsoft Azure](http://azure.com)。

#### 對於新專案

當您在 Visual Studio 中建立新專案時，請務必選取 [加入 Application Insights]****。


![Create an ASP.NET project](./media/app-insights-start-monitoring-app-health-usage/appinsights-01-vsnewp1.png)

Visual Studio Application Insights 中建立資源將 SDK 加入至專案，並將中的索引鍵 `.config` 檔案。

如果您的專案有網頁，同時也增加了 [JavaScript SDK ][client] 至主版網頁。

#### ... 對於現有專案

以滑鼠右鍵按一下 [方案總管] 中的專案，然後選擇 [加入 Application Insights]****。

![Choose Add Application Insights](./media/app-insights-start-monitoring-app-health-usage/appinsights-03-addExisting.png)

Visual Studio Application Insights 中建立資源將 SDK 加入至專案，並將中的索引鍵 `.config` 檔案。

在此情況下，它並不會加入 [JavaScript SDK ][client] 加入至網頁-建議您這樣的下一個步驟。

#### 設定選項

如果這是您第一次使用，系統會要求您登入或註冊 Microsoft Azure Preview。

如果此應用程式是更大應用程式的一部分，您可以使用 [**組態設定**]，將它放在與其他元件相同的資源群組中。

*沒有 Application Insights 選項嗎？ 請檢查您正在使用 Visual Studio 2013 Update 3 或更新版本，以及擴充功能和 Updates.* 中啟用 Application Insights Tools

#### 從專案開啟 Application Insights

![Right-click your project and open the Azure portal](./media/app-insights-start-monitoring-app-health-usage/appinsights-04-openPortal.png)




## <a name="video"></a>視訊

> [AZURE.VIDEO getting-started-with-application-insights]





[api]: app-insights-api-custom-events-metrics.md 
[apikey]: app-insights-api-custom-events-metrics.md#ikey 
[availability]: app-insights-monitor-web-app-availability.md 
[azure]: ../insights-perf-analytics.md 
[client]: app-insights-javascript.md 
[detect]: app-insights-detect-triage-diagnose.md 
[diagnostic]: app-insights-diagnostic-search.md 
[knowusers]: app-insights-overview-usage.md 
[metrics]: app-insights-metrics-explorer.md 
[netlogs]: app-insights-asp-net-trace-logs.md 
[perf]: app-insights-web-monitor-performance.md 
[platforms]: app-insights-platforms.md 
[portal]: http://portal.azure.com/ 
[qna]: app-insights-troubleshoot-faq.md 
[redfield]: app-insights-monitor-performance-live-website-now.md 
[roles]: app-insights-resources-roles-access-control.md 
[start]: app-insights-overview.md 

