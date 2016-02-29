<properties
   pageTitle="設定 Azure 診斷以將資料傳送至 Application Insights | Microsoft Azure"
   description="更新 Azure 診斷公用組態以傳送資料至 Application Insights。"
   services="multiple"
   documentationCenter=".net"
   authors="sbtron"
   manager=""
   editor="" />
<tags
   ms.service="application-insights"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="12/15/2015"
   ms.author="saurabh" />

# 設定 Azure 診斷將資料傳送至 Application Insights

Azure 診斷會將資料儲存至 Azure 儲存體資料表。  不過，您也可以在使用 Azure 診斷擴充 1.5 或更新版本時，藉由在組態中設定「接收器」和「通道」，將全部資料或資料的子集管線處理到 Application Insights。 

這篇文章說明如何為 Azure 診斷擴充建立公用組態，以便將組態設定為傳送資料至 Application Insights。

## 將 Application Insights 設定為接收器

Azure 診斷延伸模組 1.5 介紹 **<SinksConfig>** 公用組態中的項目。 這會定義其他 *接收器* 傳送 Azure 診斷資料的位置。 您可以指定您要將 Azure 診斷資料傳送的這一部分的 Application Insights 資源的詳細資料 **<SinksConfig>**。
範例 **SinksConfig** 看起來像這樣的  

    <SinksConfig>
        <Sink name="ApplicationInsights">
          <ApplicationInsights>{Insert InstrumentationKey}</ApplicationInsights>
          <Channels>
            <Channel logLevel="Error" name="MyTopDiagData"  />
            <Channel logLevel="Verbose" name="MyLogData"  />
          </Channels>
        </Sink>
      </SinksConfig> 

如 **接收器** 元素 *名稱* 屬性會指定將用於接收唯一參考的字串值。
 **ApplicationInsights** 項目會指定 Azure 診斷資料傳送的位置的 Application insights 資源的檢測金鑰。 如果您沒有現有的 Application Insights 資源，請參閱 [建立新的 Application Insights 資源](app-insights-create-new-resource.md) 如需有關建立資源，並取得檢測金鑰。

如果您正在開發的雲端服務專案與 Azure SDK 2.8 此檢測金鑰會自動填入中公開的組態，根據 **APPINSIGHTS_INSTRUMENTATIONKEY** 封裝雲端服務專案時，服務組態設定。 請參閱 [對雲端服務的問題進行疑難排解的 Azure 診斷使用 Application Insights](cloud-services-dotnet-diagnostics-applicationinsights.md)。

 **通道** 項目可讓您定義一個或多個 **通道** 將傳送至接收資料的項目。 通道可用作篩選器，並可讓您選取想要傳送給接收器的特定記錄層級。 比方說，您可以收集詳細資訊記錄檔並將它們傳送至儲存體，但您可以選擇定義具有「錯誤」的記錄層級，並且當您透過該通道傳送記錄檔時，只會傳送錯誤記錄檔到該接收器。
如 **通道**  *名稱* 屬性用來唯一參考該通道。 
 *Loglevel* 屬性可讓您指定記錄層級，讓通道。 順序的最小資訊的可用記錄層級
 - 詳細資訊
 - 資訊
 - 警告
 - 錯誤
 - 重大

## 將資料傳送至 Application Insights 接收器
定義的 Application Insights 接收之後您可以將資料傳送至該接收加 *接收器* 屬性下的項目 **DiagnosticMonitorConfiguration** 節點。 新增 *接收* 每個節點的項目會指定您想要從該節點和其傳送至指定的接收下的任何節點收集的資料。 

例如，如果您想要傳送所有資料，則您可以新增收集 Azure 診斷 *接收器* 屬性直接 **DiagnosticMonitorConfiguration** 節點。 設定的值 *接收* 接收名稱中所指定的 **SinkConfig**。

    <DiagnosticMonitorConfiguration overallQuotaInMB="4096" sinks="ApplicationInsights">

如果您想要傳送至 Application Insights 的唯一錯誤記錄檔接收器，則您可以將 *接收* 接收名稱後面加上句點隔開的通道名稱的值 ("。")。 例如，若只要將錯誤記錄檔傳送至 Application Insights 接收器，請使用上述 SinksConfig 中定義的 MyTopDiagdata 通道。  
 
    <DiagnosticMonitorConfiguration overallQuotaInMB="4096" sinks="ApplicationInsights.MyTopDiagdata">

如果您只想要傳送至 Application Insights 的詳細資訊的應用程式記錄檔，則您可以加入 *接收* 屬性設定為 **記錄** 節點。 
    
    <Logs scheduledTransferPeriod="PT1M" scheduledTransferLogLevelFilter="Verbose" sinks="ApplicationInsights.MyLogData"/>

您也可以在階層的不同層級的組態中包含多個接收器。 在此情況下，在階層最上層指定的接收器會做為全域設定，而在個別元素指定的接收器則做為該全域設定的覆寫。    

以下是將所有錯誤都傳送至 Application Insights 的公用設定檔的完整範例 (在指定 **DiagnosticMonitorConfiguration** 節點)，並另外的詳細資訊層級記錄應用程式記錄檔 (在指定 **記錄** 節點)。 

    <WadCfg>
      <DiagnosticMonitorConfiguration overallQuotaInMB="4096"
           sinks="ApplicationInsights.MyTopDiagData"> <!-- All info below sent to this channel -->
        <DiagnosticInfrastructureLogs />
        <PerformanceCounters>
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT3M" sinks="ApplicationInsights.MyLogData/>
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Available MBytes" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\Bytes Total/Sec" sampleRate="PT3M" />
        </PerformanceCounters>
        <WindowsEventLog scheduledTransferPeriod="PT1M">
          <DataSource name="Application!*" />
        </WindowsEventLog>
        <Logs scheduledTransferPeriod="PT1M" scheduledTransferLogLevelFilter="Verbose"
                sinks="ApplicationInsights.MyLogData"/> <!-- This specific info sent to this channel -->
      </DiagnosticMonitorConfiguration>

    <SinksConfig>
        <Sink name="ApplicationInsights">
          <ApplicationInsights>{Insert InstrumentationKey}</ApplicationInsights>
          <Channels>
            <Channel logLevel="Error" name="MyTopDiagData"  />
            <Channel logLevel="Verbose" name="MyLogData"  />
          </Channels>
        </Sink>
      </SinksConfig>
    </WadCfg>

![診斷公用組態](./media/azure-diagnostics-configure-applicationinsights/diagnostics-publicconfig.png)

對於這項功能有一些要注意的限制

- 通道只是為了配合記錄類型 (而不是效能計數器) 使用。 如果您對效能計數器元素指定通道，將會忽略它。 
- 通道的記錄層級不能超過 Azure 診斷所要收集的記錄層級。 例如：您不能在「記錄」元素中收集「應用程式記錄」錯誤，並且嘗試傳送「詳細資訊」記錄至 Application Insight 接收器。  *ScheduledTransferLogLevelFilter* 屬性一律必須收集相等或更多記錄於記錄檔要傳送到接收器。 
- 您無法將 Azure 診斷擴充收集的任何 blob 資料傳送至 Application Insights。 比方說的任何項目底下指定 *目錄* 節點。 針對損毀傾印，實際損毀傾印將仍可傳送至 blob 儲存體，並只會將損毀傾印所產生的通知傳送至 Application Insights。 


## 後續步驟

- 使用 [PowerShell](cloud-services-diagnostics-powershell.md) 來啟用您的應用程式的 Azure 診斷擴充功能。 
- 使用 [Visual Studio](vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines.md) 來啟用您的應用程式的 Azure 診斷擴充功能 
