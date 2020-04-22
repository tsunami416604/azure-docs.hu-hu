---
title: Az Azure Application Insights felülbírálja az alapértelmezett SDK-végpontokat
description: Módosítsa az azure-figyelő application insights SDK-végpontok olyan régiókban, mint az Azure Government.
ms.topic: conceptual
ms.date: 07/26/2019
ms.openlocfilehash: b43bd13c73f77c6292e2062db88d68a20e5bf480
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81729529"
---
# <a name="application-insights-overriding-default-endpoints"></a>Az Application Insights felülbírálja az alapértelmezett végpontokat

Ha adatokat szeretne küldeni az Application Insightsból bizonyos régiókba, felül kell bírálnia az alapértelmezett végpontcímeket. Minden SDK némileg eltérő módosításokat igényel, amelyek mindegyike ebben a cikkben ismertetjük. Ezek a módosítások a mintakód módosítását `QuickPulse_Endpoint_Address`és `TelemetryChannel_Endpoint_Address`a `Profile_Query_Endpoint_address` helyőrző értékek cseréjét igénylik az on, és az adott régió tényleges végpontcímeivel. A cikk vége a végpontcímekre mutató hivatkozásokat tartalmaz azon régiók esetében, ahol ez a konfiguráció szükséges.

> [!NOTE]
> [A kapcsolati karakterláncok](https://docs.microsoft.com/azure/azure-monitor/app/sdk-connection-string?tabs=net) az egyéni végpontok beállításának új előnyben részesített metódusai az Application Insightsban.

---

## <a name="sdk-code-changes"></a>Az SDK-kód módosításai

# <a name="net"></a>[.NET](#tab/net)

> [!NOTE]
> Az applicationinsights.config fájl automatikusan felülírja az SDK-frissítés végrehajtásakor. Az SDK-frissítés végrehajtása után győződjön meg róla, hogy adja meg újra a régióspecifikus végpontértékeket.

```xml
<ApplicationInsights>
  ...
  <TelemetryModules>
    <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse.QuickPulseTelemetryModule, Microsoft.AI.PerfCounterCollector">
      <QuickPulseServiceEndpoint>Custom_QuickPulse_Endpoint_Address</QuickPulseServiceEndpoint>
    </Add>
  </TelemetryModules>
    ...
  <TelemetryChannel>
     <EndpointAddress>TelemetryChannel_Endpoint_Address</EndpointAddress>
  </TelemetryChannel>
  ...
  <ApplicationIdProvider Type="Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId.ApplicationInsightsApplicationIdProvider, Microsoft.ApplicationInsights">
    <ProfileQueryEndpoint>Profile_Query_Endpoint_address</ProfileQueryEndpoint>
  </ApplicationIdProvider>
  ...
</ApplicationInsights>
```

# <a name="net-core"></a>[.NET Core](#tab/netcore)

Módosítsa az appsettings.json fájlt a projektben az alábbiak szerint a fő végpont módosításához:

```json
"ApplicationInsights": {
    "InstrumentationKey": "instrumentationkey",
    "TelemetryChannel": {
      "EndpointAddress": "TelemetryChannel_Endpoint_Address"
    }
  }
```

Az élő metrikák és a profillekérdezésvégpont értékei csak kódsegítségével állíthatók be. Ha felül szeretné bírni az összes végpontérték alapértelmezett értékeit a `Startup.cs` kódsegítségével, végezze el a következő módosításokat a `ConfigureServices` fájl metódusában:

```csharp
using Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId;
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse; //Place at top of Startup.cs file

   services.ConfigureTelemetryModule<QuickPulseTelemetryModule>((module, o) => module.QuickPulseServiceEndpoint="QuickPulse_Endpoint_Address");

   services.AddSingleton<IApplicationIdProvider, ApplicationInsightsApplicationIdProvider>(_ => new ApplicationInsightsApplicationIdProvider() { ProfileQueryEndpoint = "Profile_Query_Endpoint_address" });

   services.AddSingleton<ITelemetryChannel>(_ => new ServerTelemetryChannel() { EndpointAddress = "TelemetryChannel_Endpoint_Address" });

    //Place in the ConfigureServices method. Place this before services.AddApplicationInsightsTelemetry("instrumentation key"); if it's present
```

# <a name="azure-functions"></a>[Azure Functions](#tab/functions)

### <a name="azure-functions-v2x"></a>Az Azure Functions 2.x-es ével

Telepítse a következő csomagokat a függvényprojektbe:

- Microsoft.ApplicationInsights verzió 2.10.0
- Microsoft.ApplicationInsights.PerfCounterCollector verzió 2.10.0
- Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel 2.10.0-s verziója

Ezután adja hozzá (vagy módosítsa) a függvényalkalmazás indítókódját:

```csharp
[assembly: WebJobsStartup(typeof(Example.Startup))]
namespace Example
{
  class Startup : FunctionsStartup
  {
      public override void Configure(IWebJobsBuilder builder)
      {
          var quickPulseFactory = builder.Services.FirstOrDefault(sd => sd.ServiceType == typeof(ITelemetryModule) && 
                                               sd.ImplementationType == typeof(QuickPulseTelemetryModule));
          if (quickPulseFactory != null)
          {
              builder.Services.Remove(quickPulseFactory);
          }

          var appIdFactory = builder.Services.FirstOrDefault(sd => sd.ServiceType == typeof(IApplicationIdProvider));
          if (appIdFactory != null)
          {
              builder.Services.Remove(appIdFactory);
          }

          var channelFactory = builder.Services.FirstOrDefault(sd => sd.ServiceType == typeof(ITelemetryChannel));
          if (channelFactory != null)
          {
              builder.Services.Remove(channelFactory);
          }

          builder.Services.AddSingleton<ITelemetryModule, QuickPulseTelemetryModule>(_ =>
              new QuickPulseTelemetryModule
              {
                  QuickPulseServiceEndpoint = "QuickPulse_Endpoint_Address"
              });

          builder.Services.AddSingleton<IApplicationIdProvider, ApplicationInsightsApplicationIdProvider>(_ => new ApplicationInsightsApplicationIdProvider() { ProfileQueryEndpoint = "Profile_Query_Endpoint_address" });

          builder.Services.AddSingleton<ITelemetryChannel>(_ => new ServerTelemetryChannel() { EndpointAddress = "TelemetryChannel_Endpoint_Address" });
      }
  }
}
```

# <a name="java"></a>[Java](#tab/java)

Módosítsa az applicationinsights.xml fájlt az alapértelmezett végpontcím módosításához.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">
  <InstrumentationKey>ffffeeee-dddd-cccc-bbbb-aaaa99998888</InstrumentationKey>
  <TelemetryModules>
    <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebRequestTrackingTelemetryModule"/>
    <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebSessionTrackingTelemetryModule"/>
    <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebUserTrackingTelemetryModule"/>
  </TelemetryModules>
  <TelemetryInitializers>
    <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationIdTelemetryInitializer"/>
    <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationNameTelemetryInitializer"/>
    <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebSessionTelemetryInitializer"/>
    <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserTelemetryInitializer"/>
    <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserAgentTelemetryInitializer"/>
  </TelemetryInitializers>
  <!--Add the following Channel value to modify the Endpoint address-->
  <Channel type="com.microsoft.applicationinsights.channel.concrete.inprocess.InProcessTelemetryChannel">
  <EndpointAddress>TelemetryChannel_Endpoint_Address</EndpointAddress>
  </Channel>
</ApplicationInsights>
```

### <a name="spring-boot"></a>Spring Boot

Módosítsa `application.properties` a fájlt, és adja hozzá:

```yaml
azure.application-insights.channel.in-process.endpoint-address= TelemetryChannel_Endpoint_Address
```

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

```javascript
var appInsights = require("applicationinsights");
appInsights.setup('INSTRUMENTATION_KEY');
appInsights.defaultClient.config.endpointUrl = "TelemetryChannel_Endpoint_Address"; // ingestion
appInsights.defaultClient.config.profileQueryEndpoint = "Profile_Query_Endpoint_address"; // appid/profile lookup
appInsights.defaultClient.config.quickPulseHost = "QuickPulse_Endpoint_Address"; //live metrics
appInsights.Configuration.start();
```

A végpontok környezeti változókon keresztül is konfigurálhatók:

```
Instrumentation Key: "APPINSIGHTS_INSTRUMENTATIONKEY"
Profile Endpoint: "Profile_Query_Endpoint_address"
Live Metrics Endpoint: "QuickPulse_Endpoint_Address"
```

# <a name="javascript"></a>[JavaScript](#tab/js)

```javascript
<script type="text/javascript">
    var sdkInstance="appInsightsSDK";window[sdkInstance]="appInsights";var aiName=window[sdkInstance],aisdk=window[aiName]||function(e){function n(e){t[e]=function(){var n=arguments;t.queue.push(function(){t[e].apply(t,n)})}}var t={config:e};t.initialize=!0;var i=document,a=window;setTimeout(function(){var n=i.createElement("script");n.src=e.url||"https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js",i.getElementsByTagName("script")[0].parentNode.appendChild(n)});try{t.cookie=i.cookie}catch(e){}t.queue=[],t.version=2;for(var r=["Event","PageView","Exception","Trace","DependencyData","Metric","PageViewPerformance"];r.length;)n("track"+r.pop());n("startTrackPage"),n("stopTrackPage");var s="Track"+r[0];if(n("start"+s),n("stop"+s),n("setAuthenticatedUserContext"),n("clearAuthenticatedUserContext"),n("flush"),!(!0===e.disableExceptionTracking||e.extensionConfig&&e.extensionConfig.ApplicationInsightsAnalytics&&!0===e.extensionConfig.ApplicationInsightsAnalytics.disableExceptionTracking)){n("_"+(r="onerror"));var o=a[r];a[r]=function(e,n,i,a,s){var c=o&&o(e,n,i,a,s);return!0!==c&&t["_"+r]({message:e,url:n,lineNumber:i,columnNumber:a,error:s}),c},e.autoExceptionInstrumented=!0}return t}(
    {
      instrumentationKey:"INSTRUMENTATION_KEY",
      endpointUrl: "TelemetryChannel_Endpoint_Address"
    }
    );window[aiName]=aisdk,aisdk.queue&&0===aisdk.queue.length&&aisdk.trackPageView({});
</script>
```

# <a name="python"></a>[Python](#tab/python)

Az opencensus-python SDK betöltési végpontjának módosításához az [opencensus-python repo-hoz tájékozódhat.](https://github.com/census-instrumentation/opencensus-python/blob/af284a92b80bcbaf5db53e7e0813f96691b4c696/contrib/opencensus-ext-azure/opencensus/ext/azure/common/__init__.py)

---

## <a name="regions-that-require-endpoint-modification"></a>Végpont-módosítást igénylő régiók

Jelenleg csak az Azure Government és az [Azure China](https://docs.microsoft.com/azure/azure-government/documentation-government-services-monitoringandmanagement#application-insights) szükséges végpontmódosításokat igénylő [régiók.](https://docs.microsoft.com/azure/china/resources-developer-guide)

|Régió |  Végpont neve | Érték |
|-----------------|:------------|:-------------|
| Azure China | Telemetriai csatorna | `https://dc.applicationinsights.azure.cn/v2/track` |
| Azure China | QuickPulse (élő mérőszámok) |`https://live.applicationinsights.azure.cn/QuickPulseService.svc` |
| Azure China | Profillekérdezés |`https://dc.applicationinsights.azure.cn/api/profiles/{0}/appId`  |
| Azure Government | Telemetriai csatorna |`https://dc.applicationinsights.us/v2/track` |
| Azure Government | QuickPulse (élő mérőszámok) |`https://quickpulse.applicationinsights.us/QuickPulseService.svc` |
| Azure Government | Profillekérdezés |`https://dc.applicationinsights.us/api/profiles/{0}/appId` |

Ha jelenleg az [Application Insights REST API-t](https://dev.applicationinsights.io/
) használja, amely általában a "api.applicationinsights.io" keresztül érhető el, akkor a régió helyi végpontját kell használnia:

|Régió |  Végpont neve | Érték |
|-----------------|:------------|:-------------|
| Azure China | REST API | `api.applicationinsights.azure.cn` |
| Azure Government | REST API | `api.applicationinsights.us`|

> [!NOTE]
> Az Azure App Services kód nélküli ügynök-/bővítményalapú figyelése **jelenleg nem támogatott** ezekben a régiókban. Amint ez a funkció elérhetővé válik, ez a cikk frissül.

## <a name="next-steps"></a>További lépések

- Ha többet szeretne megtudni az Azure Government egyéni módosításairól, olvassa el az [Azure figyelési és -kezelési részletes útmutatóját.](https://docs.microsoft.com/azure/azure-government/documentation-government-services-monitoringandmanagement#application-insights)
- Ha többet szeretne megtudni az Azure China-ról, olvassa el az [Azure China forgatókönyvét.](https://docs.microsoft.com/azure/china/)
