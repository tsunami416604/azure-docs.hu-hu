---
title: Szűrés és előfeldolgozás az Azure Application Insights SDK-ban | Microsoft dokumentumok
description: Telemetriai processzorok és telemetriai inicializálók az SDK-hoz szűrni vagy hozzáadni tulajdonságokat az adatokhoz, mielőtt a telemetriai adatok az Application Insights-portálra.
ms.topic: conceptual
ms.date: 11/23/2016
ms.openlocfilehash: 53b6ecc51961feba35d571eab3115c8e7ccf9964
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80366310"
---
# <a name="filtering-and-preprocessing-telemetry-in-the-application-insights-sdk"></a>Telemetriai adatok szűrése és előfeldolgozása az Application Insights SDK-ban

Az Application Insights SDK beépülő moduljait megírhatja és konfigurálhatja, így testelemetriát tehet ünk és dolgozhat fel, mielőtt elküldené az Application Insights-szolgáltatásnak.

* [Mintavételezés](sampling.md) csökkenti a telemetriai adatok mennyiségét anélkül, hogy befolyásolná a statisztikákat. A kapcsolódó adatpontokat összetartja, így a probléma diagnosztizálásakor navigálhat közöttük. A portálon az összes szám megszorozzuk a mintavételi kompenzáció.
* Telemetriai processzorokkal való szűrés lehetővé teszi a telemetriai adatok kiszűrését az SDK-ban, mielőtt a kiszolgálóra küldenék. Például csökkentheti a telemetriai adatok mennyiségét a robotoktól érkező kérelmek kizárásával. A szűrés a forgalom csökkentésének alapvetőbb megközelítése, mint a mintavételezés. Lehetővé teszi, hogy jobban szabályozhatja a továbbított adatokat, de tisztában kell lennie azzal, hogy ez hatással van a statisztikáira - például ha kiszűri az összes sikeres kérést.
* [Telemetriai initializerek hozzá, vagy módosíthatja a tulajdonságokat](#add-properties) az alkalmazásból küldött telemetriai adatok, beleértve a telemetriai adatokat a szabványos modulok. Hozzáadhat például számított értékeket; vagy verziószámok, amelyek alapján szűrni az adatokat a portálon.
* [Az SDK API](../../azure-monitor/app/api-custom-events-metrics.md) egyéni események és metrikák küldésére szolgál.

Előkészületek:

* Telepítse az alkalmazáshoz megfelelő SDK-t: [ASP.NET](asp-net.md), [ASP.NET Core](asp-net-core.md), [Nem HTTP/Worker for .NET/.NET Core](worker-service.md), [Java](../../azure-monitor/app/java-get-started.md) vagy [JavaScript](javascript.md)

<a name="filtering"></a>

## <a name="filtering"></a>Szűrés

Ez a módszer közvetlen vezérlést biztosít a telemetriai adatfolyamból való beágyazásvagy kizárás felett. A szűrés segítségével eldobhatja a telemetriai elemeket az Application Insightsnak való elküldésből. Használhatja együtt mintavételi, vagy külön-külön.

A telemetriai adatok szűréséhez írjon egy telemetriai processzort, és regisztrálja azt a. `TelemetryConfiguration` Az összes telemetriai adatok megy keresztül a processzor, és választhat, hogy dobja el az adatfolyamból, vagy adja meg a lánc következő processzora. Ez magában foglalja a telemetriai adatokat a szabványos modulok, például a HTTP-kérelem gyűjtő és a függőség gyűjtője, és a telemetriai ön maga által nyomon követett. Kiszűrheti például a robotoktól érkező kérelmek telemetriáját, vagy a sikeres függőségi hívásokat.

> [!WARNING]
> Az SDK-ból küldött telemetriai adatok processzorok használatával történő szűrése torzíthatja a portálon látható statisztikákat, és megnehezítheti a kapcsolódó elemek követését.
>
> Ehelyett fontolja meg a [mintavételhasználatát.](../../azure-monitor/app/sampling.md)
>
>

### <a name="create-a-telemetry-processor-c"></a>Telemetriai processzor létrehozása (C#)

1. Szűrő létrehozásához valósítsa meg a programot. `ITelemetryProcessor`

    Figyelje meg, hogy a telemetriai processzorok egy feldolgozási láncot hozlétre. Telemetriai processzor példányosításakor a lánc következő processzorára mutató hivatkozást kap. Amikor egy telemetriai adatpontot ad át a folyamat metódus, akkor a munkáját, és majd meghívja (vagy nem hívja) a lánc következő telemetriai processzora.

    ```csharp
    using Microsoft.ApplicationInsights.Channel;
    using Microsoft.ApplicationInsights.Extensibility;

    public class SuccessfulDependencyFilter : ITelemetryProcessor
    {
        private ITelemetryProcessor Next { get; set; }

        // next will point to the next TelemetryProcessor in the chain.
        public SuccessfulDependencyFilter(ITelemetryProcessor next)
        {
            this.Next = next;
        }

        public void Process(ITelemetry item)
        {
            // To filter out an item, return without calling the next processor.
            if (!OKtoSend(item)) { return; }

            this.Next.Process(item);
        }

        // Example: replace with your own criteria.
        private bool OKtoSend (ITelemetry item)
        {
            var dependency = item as DependencyTelemetry;
            if (dependency == null) return true;

            return dependency.Success != true;
        }
    }
    ```

2. Adja hozzá a processzort.

**alkalmazások ASP.NET** Szúrja be ezt a kódrészletet az ApplicationInsights.config fájlba:

```xml
<TelemetryProcessors>
  <Add Type="WebApplication9.SuccessfulDependencyFilter, WebApplication9">
     <!-- Set public property -->
     <MyParamFromConfigFile>2-beta</MyParamFromConfigFile>
  </Add>
</TelemetryProcessors>
```

A .config fájlból karakterlánc-értékeket adhat át, ha nyilvános nevű tulajdonságokat ad meg az osztályban.

> [!WARNING]
> Ügyeljen arra, hogy a .config fájlban szereplő típusnevet és tulajdonságneveket a kódban szereplő osztály- és tulajdonságnevekhez igazítsa. Ha a .config fájl nem létező típusra vagy tulajdonságra hivatkozik, előfordulhat, hogy az SDK csendben nem küld telemetriai adatokat.
>

**Azt is megteheti,** hogy inicializálja a szűrőt a kódban. Egy megfelelő inicializációs osztályban - például AppStart in `Global.asax.cs` - helyezze be a processzort a láncba:

```csharp
var builder = TelemetryConfiguration.Active.DefaultTelemetrySink.TelemetryProcessorChainBuilder;
builder.Use((next) => new SuccessfulDependencyFilter(next));

// If you have more processors:
builder.Use((next) => new AnotherProcessor(next));

builder.Build();
```

TelemetriaI-ügyfelek után létrehozott ezen a ponton fogja használni a processzorok.

**ASP.NET Core/ Worker Service alkalmazások**

> [!NOTE]
> Processzor hozzáadása `ApplicationInsights.config` vagy `TelemetryConfiguration.Active` használata nem érvényes ASP.NET Core alkalmazások, vagy ha a Microsoft.ApplicationInsights.WorkerService SDK.

A core vagy ASP.NET [workerservice](worker-service.md#adding-telemetry-processors)használatával `TelemetryProcessor` írt alkalmazások `AddApplicationInsightsTelemetryProcessor` esetében `IServiceCollection` [az](asp-net-core.md#adding-telemetry-processors) új hozzáadása a bővítménymetódus használatával történik, az alábbiak szerint. Ez a módszer `ConfigureServices` az `Startup.cs` osztály metódusa.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        // ...
        services.AddApplicationInsightsTelemetry();
        services.AddApplicationInsightsTelemetryProcessor<SuccessfulDependencyFilter>();

        // If you have more processors:
        services.AddApplicationInsightsTelemetryProcessor<AnotherProcessor>();
    }
```

### <a name="example-filters"></a>Példa szűrők

#### <a name="synthetic-requests"></a>Szintetikus kérések

Szűrje ki a botokat és a webes teszteket. Bár a Metrika-kezelő lehetőséget ad a szintetikus források kiszűrésére, ez a beállítás csökkenti a forgalmat és a betöltési méretet azáltal, hogy szűri őket az SDK-n.

```csharp
public void Process(ITelemetry item)
{
  if (!string.IsNullOrEmpty(item.Context.Operation.SyntheticSource)) {return;}

  // Send everything else:
  this.Next.Process(item);
}
```

#### <a name="failed-authentication"></a>Sikertelen hitelesítés

A kérelmek kiszűrése "401" válaszsal.

```csharp
public void Process(ITelemetry item)
{
    var request = item as RequestTelemetry;

    if (request != null &&
    request.ResponseCode.Equals("401", StringComparison.OrdinalIgnoreCase))
    {
        // To filter out an item, return without calling the next processor.
        return;
    }

    // Send everything else
    this.Next.Process(item);
}
```

#### <a name="filter-out-fast-remote-dependency-calls"></a>Gyors távoli függőségi hívások kiszűrése

Ha csak lassú hívásokat szeretne diagnosztizálni, szűrje ki a gyorshívásokat.

> [!NOTE]
> Ez megdönti a portálon látható statisztikákat.
>
>

```csharp
public void Process(ITelemetry item)
{
    var request = item as DependencyTelemetry;

    if (request != null && request.Duration.TotalMilliseconds < 100)
    {
        return;
    }
    this.Next.Process(item);
}
```

#### <a name="diagnose-dependency-issues"></a>Függőségi problémák diagnosztizálása

[Ez](https://azure.microsoft.com/blog/implement-an-application-insights-telemetry-processor/) a blog leírja a függőségi problémák diagnosztizálására irányuló projektet azáltal, hogy automatikusan rendszeres pingeket küld a függőségeknek.

<a name="add-properties"></a>

### <a name="javascript-web-applications"></a>JavaScript-webalkalmazások

**Szűrés Az ITelemettryInitializer használatával**

1. Telemetriai inicializáló visszahívási függvény létrehozása. A visszahívási `ITelemetryItem` függvény paraméterként, amely a feldolgozás alatt álló esemény. A `false` visszahívásból való visszatérés a telemetriai elem kiszűrendő.  

   ```JS
   var filteringFunction = (envelope) => {
     if (envelope.data.someField === 'tobefilteredout') {
        return false;
     }
  
     return true;
   };
   ```

2. Adja hozzá a telemetriai inicializáló visszahívását:

   ```JS
   appInsights.addTelemetryInitializer(filteringFunction);
   ```

## <a name="addmodify-properties-itelemetryinitializer"></a>Tulajdonságok hozzáadása/módosítása: ITelemetryInitializer


Telemetriai inicializálók használatával további információkkal gazdagíthatja a telemetriai adatokat, és/vagy felülbírálhatja a szabványos telemetriai modulok által beállított telemetriai tulajdonságokat.

Például az Application Insights for Web csomag telemetriai adatokat gyűjt a HTTP-kérelmek. Alapértelmezés szerint a >= 400 válaszkóddal rendelkező kérelmek et sikertelennek jelzőknek tekinti. De ha azt szeretné, hogy a 400-at sikeresként kezelje, biztosíthat egy telemetriai inicializálót, amely beállítja a Success tulajdonságot.

Ha telemetriai inicializálót ad meg, akkor a neve akkor lesz, amikor a Track*() metódusok bármelyikét meghívják. Ez `Track()` magában foglalja a szabványos telemetriai modulok által megnevezett metódusokat. Megegyezés szerint ezek a modulok nem állítanak be olyan tulajdonságot, amelyet már beállított egy inicializáló. Telemetriai inicializálók hívják telemetriai processzorok hívása előtt. Tehát minden dúsítások által végzett inicializálók látható a processzorok.

**A inicializáló meghatározása**

*C #*

```csharp
using System;
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.DataContracts;
using Microsoft.ApplicationInsights.Extensibility;

namespace MvcWebRole.Telemetry
{
  /*
   * Custom TelemetryInitializer that overrides the default SDK
   * behavior of treating response codes >= 400 as failed requests
   *
   */
  public class MyTelemetryInitializer : ITelemetryInitializer
  {
    public void Initialize(ITelemetry telemetry)
    {
        var requestTelemetry = telemetry as RequestTelemetry;
        // Is this a TrackRequest() ?
        if (requestTelemetry == null) return;
        int code;
        bool parsed = Int32.TryParse(requestTelemetry.ResponseCode, out code);
        if (!parsed) return;
        if (code >= 400 && code < 500)
        {
            // If we set the Success property, the SDK won't change it:
            requestTelemetry.Success = true;

            // Allow us to filter these requests in the portal:
            requestTelemetry.Properties["Overridden400s"] = "true";
        }
        // else leave the SDK to set the Success property
    }
  }
}
```

**ASP.NET alkalmazások: A inicializáló betöltése**

Az ApplicationInsights.config fájlban:

```xml
<ApplicationInsights>
  <TelemetryInitializers>
    <!-- Fully qualified type name, assembly name: -->
    <Add Type="MvcWebRole.Telemetry.MyTelemetryInitializer, MvcWebRole"/>
    ...
  </TelemetryInitializers>
</ApplicationInsights>
```

*Másik lehetőségként* a kódban is létrehozhatja az inicializálót, például Global.aspx.cs:

```csharp
protected void Application_Start()
{
    // ...
    TelemetryConfiguration.Active.TelemetryInitializers.Add(new MyTelemetryInitializer());
}
```

[A mintából még többet láthatsz.](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/MvcWebRole)

**core/worker service alkalmazások ASP.NET: Töltse be az inicializálót**

> [!NOTE]
> Initializer hozzáadása `ApplicationInsights.config` használatával `TelemetryConfiguration.Active` vagy használatával nem érvényes ASP.NET Core alkalmazások, vagy ha a Microsoft.ApplicationInsights.WorkerService SDK.

A core vagy [ASP.NET a](asp-net-core.md#adding-telemetryinitializers) [WorkerService](worker-service.md#adding-telemetryinitializers) `TelemetryInitializer` használatával írt alkalmazások esetében az új hozzáadása a függőségi injektálás tárolóhoz való hozzáadásával történik, az alábbiak szerint. Ez a `Startup.ConfigureServices` módszerrel történik.

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;
 public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<ITelemetryInitializer, MyTelemetryInitializer>();
}
```

### <a name="java-telemetry-initializers"></a>Java telemetriai inicializálók

[Java SDK dokumentáció](https://docs.microsoft.com/java/api/com.microsoft.applicationinsights.extensibility.telemetryinitializer?view=azure-java-stable)

```Java
public interface TelemetryInitializer
{ /** Initializes properties of the specified object. * @param telemetry The {@link com.microsoft.applicationinsights.telemetry.Telemetry} to initialize. */

void initialize(Telemetry telemetry); }
```

Ezután regisztrálja az egyéni inicializálót az applicationinsights.xml fájlban.

```xml
<Add type="mypackage.MyConfigurableContextInitializer">
    <Param name="some_config_property" value="some_value" />
</Add>
```

### <a name="javascript-telemetry-initializers"></a>JavaScript telemetriai inicializálók
*Javascript*

Telemetriai inicializáló beszúrása közvetlenül a portálról kapott inicializálási kód után:

```JS
<script type="text/javascript">
    // ... initialization code
    ...({
        instrumentationKey: "your instrumentation key"
    });
    window.appInsights = appInsights;


    // Adding telemetry initializer.
    // This is called whenever a new telemetry item
    // is created.

    appInsights.queue.push(function () {
        appInsights.context.addTelemetryInitializer(function (envelope) {
            var telemetryItem = envelope.data.baseData;

            // To check the telemetry items type - for example PageView:
            if (envelope.name == Microsoft.ApplicationInsights.Telemetry.PageView.envelopeType) {
                // this statement removes url from all page view documents
                telemetryItem.url = "URL CENSORED";
            }

            // To set custom properties:
            telemetryItem.properties = telemetryItem.properties || {};
            telemetryItem.properties["globalProperty"] = "boo";

            // To set custom metrics:
            telemetryItem.measurements = telemetryItem.measurements || {};
            telemetryItem.measurements["globalMetric"] = 100;
        });
    });

    // End of inserted code.

    appInsights.trackPageView();
</script>
```

A telemettryItem elemen elérhető nem egyéni tulajdonságok összegzését az [Application Insights exportadat-modell című témakörben](../../azure-monitor/app/export-data-model.md)talál.

Annyi inicializálót adhat hozzá, amennyit csak szeretne, és a hozzáadásuk sorrendjében hívják őket.

### <a name="opencensus-python-telemetry-processors"></a>OpenCensus Python telemetriai processzorok

Telemetriai processzorok OpenCensus Python egyszerűen visszahívási függvények, amelyeket a telemetriai adatok feldolgozására, mielőtt azok exportálása. A visszahívási függvénynek paraméterként el kell fogadnia egy [boríték](https://github.com/census-instrumentation/opencensus-python/blob/master/contrib/opencensus-ext-azure/opencensus/ext/azure/common/protocol.py#L86) adattípusát. Ha ki szeretné szűrni a telemetriai adatokat az exportálásból, győződjön meg arról, hogy a visszahívási függvény visszaadja. `False` Az Azure Monitor adattípusaia a borítékokközött [itt](https://github.com/census-instrumentation/opencensus-python/blob/master/contrib/opencensus-ext-azure/opencensus/ext/azure/common/protocol.py)látható.

> [!NOTE]
> A `cloud_RoleName` módosításával az `ai.cloud.role` attribútum a `tags` mezőben.

```python
def callback_function(envelope):
    envelope.tags['ai.cloud.role'] = 'new_role_name.py'
```

```python
# Example for log exporter
import logging

from opencensus.ext.azure.log_exporter import AzureLogHandler

logger = logging.getLogger(__name__)

# Callback function to append '_hello' to each log message telemetry
def callback_function(envelope):
    envelope.data.baseData.message += '_hello'
    return True

handler = AzureLogHandler(connection_string='InstrumentationKey=<your-instrumentation_key-here>')
handler.add_telemetry_processor(callback_function)
logger.addHandler(handler)
logger.warning('Hello, World!')
```
```python
# Example for trace exporter
import requests

from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.trace import config_integration
from opencensus.trace.samplers import ProbabilitySampler
from opencensus.trace.tracer import Tracer

config_integration.trace_integrations(['requests'])

# Callback function to add os_type: linux to span properties
def callback_function(envelope):
    envelope.data.baseData.properties['os_type'] = 'linux'
    return True

exporter = AzureExporter(
    connection_string='InstrumentationKey=<your-instrumentation-key-here>'
)
exporter.add_telemetry_processor(callback_function)
tracer = Tracer(exporter=exporter, sampler=ProbabilitySampler(1.0))
with tracer.span(name='parent'):
response = requests.get(url='https://www.wikipedia.org/wiki/Rabbit')
```

```python
# Example for metrics exporter
import time

from opencensus.ext.azure import metrics_exporter
from opencensus.stats import aggregation as aggregation_module
from opencensus.stats import measure as measure_module
from opencensus.stats import stats as stats_module
from opencensus.stats import view as view_module
from opencensus.tags import tag_map as tag_map_module

stats = stats_module.stats
view_manager = stats.view_manager
stats_recorder = stats.stats_recorder

CARROTS_MEASURE = measure_module.MeasureInt("carrots",
                                            "number of carrots",
                                            "carrots")
CARROTS_VIEW = view_module.View("carrots_view",
                                "number of carrots",
                                [],
                                CARROTS_MEASURE,
                                aggregation_module.CountAggregation())

# Callback function to only export the metric if value is greater than 0
def callback_function(envelope):
    return envelope.data.baseData.metrics[0].value > 0

def main():
    # Enable metrics
    # Set the interval in seconds in which you want to send metrics
    exporter = metrics_exporter.new_metrics_exporter(connection_string='InstrumentationKey=<your-instrumentation-key-here>')
    exporter.add_telemetry_processor(callback_function)
    view_manager.register_exporter(exporter)

    view_manager.register_view(CARROTS_VIEW)
    mmap = stats_recorder.new_measurement_map()
    tmap = tag_map_module.TagMap()

    mmap.measure_int_put(CARROTS_MEASURE, 1000)
    mmap.record(tmap)
    # Default export interval is every 15.0s
    # Your application should run for at least this amount
    # of time so the exporter will meet this interval
    # Sleep can fulfill this
    time.sleep(60)

    print("Done recording metrics")

if __name__ == "__main__":
    main()
```
Annyi processzort adhat hozzá, amennyit csak szeretne, és a hozzáadásuk sorrendjében hívják őket. Ha egy processzor kivételt tesz, az nem érinti a következő processzorokat.

### <a name="example-telemetryinitializers"></a>Példa Telemetriai Inicionizálók

#### <a name="add-custom-property"></a>Egyéni tulajdonság hozzáadása

A következő minta inicializáló hozzáad egy egyéni tulajdonságot minden nyomon követett telemetriai adatokhoz.

```csharp
public void Initialize(ITelemetry item)
{
  var itemProperties = item as ISupportProperties;
  if(itemProperties != null && !itemProperties.Properties.ContainsKey("customProp"))
    {
        itemProperties.Properties["customProp"] = "customValue";
    }
}
```

#### <a name="add-cloud-role-name"></a>Felhőbeli szerepkör nevének hozzáadása

A következő minta inicializáló beállítja a felhőszerepkör nevét minden nyomon követett telemetriai adatok.

```csharp
public void Initialize(ITelemetry telemetry)
{
    if(string.IsNullOrEmpty(telemetry.Context.Cloud.RoleName))
    {
        telemetry.Context.Cloud.RoleName = "MyCloudRoleName";
    }
}
```

## <a name="itelemetryprocessor-and-itelemetryinitializer"></a>ITelemettryProcesszor és ITelemetryInitializer

Mi a különbség a telemetriai processzorok és a telemetriai inicializálók között?

* Vannak átfedések, hogy mit tehet velük: mindkettő használható telemetriai tulajdonságok hozzáadásához vagy módosításához, bár ajánlott inicializálók e célra.
* Telemetriai initializers mindig fut telemettryprocesszorok előtt.
* TelemetriaI initializers lehet nevezni többször. Megegyezés szerint nem állítanak be olyan tulajdonságot, amely már be van állítva.
* TelemetriaI-feldolgozók lehetővé teszi, hogy teljesen cserélje ki vagy dobja el a telemetriai elem.
* Minden regisztrált telemetriai initializerek garantáltan kell hívni minden telemetriai elem. Telemetriai processzorok, SDK garantálja a legelső telemetriai processzor hívása. Azt, hogy a többi processzort hívják-e meg vagy sem, az előző telemetriai processzorok döntik el.
* Telemetriai initializers használatával további tulajdonságokkal gazdagíthatja a telemetriai adatokat, vagy felülbírálhatja a meglévőt. TelemettryProcessor segítségével kiszűrheti a telemetriai adatokat.

## <a name="troubleshooting-applicationinsightsconfig"></a>Az ApplicationInsights.config hibaelhárítása

* Ellenőrizze, hogy a teljesen minősített típusnév és kódösszeállítás neve helyes-e.
* Ellenőrizze, hogy az applicationinsights.config fájl a kimeneti könyvtárban van-e, és tartalmazza-e a legutóbbi módosításokat.

## <a name="reference-docs"></a>Referenciadokumentumok

* [Az API áttekintése](../../azure-monitor/app/api-custom-events-metrics.md)
* [ASP.NET hivatkozás](https://msdn.microsoft.com/library/dn817570.aspx)

## <a name="sdk-code"></a>SDK-kód

* [ASP.NET Core SDK](https://github.com/Microsoft/ApplicationInsights-aspnetcore)
* [ASP.NET SDK](https://github.com/Microsoft/ApplicationInsights-dotnet)
* [JavaScript SDK](https://github.com/Microsoft/ApplicationInsights-JS)

## <a name="next-steps"></a><a name="next"></a>További lépések
* [Események és naplók keresése](../../azure-monitor/app/diagnostic-search.md)
* [Mintavételezés](../../azure-monitor/app/sampling.md)
* [hibaelhárítással](../../azure-monitor/app/troubleshoot-faq.md)
