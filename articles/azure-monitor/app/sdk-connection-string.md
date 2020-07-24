---
title: A kapcsolatok karakterláncai az Azure Application Insightsban | Microsoft Docs
description: A kapcsolódási karakterláncok használata.
ms.topic: conceptual
author: timothymothra
ms.author: tilee
ms.date: 01/17/2020
ms.reviewer: mbullwin
ms.openlocfilehash: 81e27c0f62e921eb468c78521e426c8917da250a
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87014138"
---
# <a name="connection-strings"></a>Kapcsolati sztringek

## <a name="overview"></a>Áttekintés

A kapcsolatok karakterláncai egyetlen konfigurációs beállítással biztosítják az alkalmazás-betekintési felhasználókat, így nincs szükség több proxybeállítások megadására. Az intranetes webkiszolgálók, a szuverén vagy a hibrid felhőalapú környezetek esetében igen hasznosak, akik az adataikat a figyelési szolgáltatásba küldik.

A kulcs érték párok egyszerű módszert biztosítanak a felhasználók számára az egyes Application Insights (AI) szolgáltatásokhoz/termékekhez tartozó előtag-utótagok definiálására.

> [!IMPORTANT]
> Nem ajánlott a kapcsolatok karakterláncának és a kialakítási kulcsnak a beállítása. Abban az esetben, ha a felhasználó mindkettőt beállította, akkor a legutóbb beállított érték elsőbbséget élvez. 


## <a name="scenario-overview"></a>A forgatókönyv áttekintése 

Felhasználói forgatókönyvek, ahol a legnagyobb hatással van a következőre:

- Tűzfal-kivételek vagy proxy-átirányítások 

    Ha az intranetes webkiszolgáló figyelésére van szükség, a korábbi megoldásunk arra kérte az ügyfeleket, hogy egyéni szolgáltatási végpontokat adjanak hozzá a konfigurációhoz. További információ: [itt](../faq.md#can-i-monitor-an-intranet-web-server). 
    A kapcsolódási karakterláncok jobb alternatívát nyújtanak, ha csökkenti ezt az erőfeszítést egyetlen beállításra. Az egyszerű előtag, az utótagok módosítása lehetővé teszi az összes végpont automatikus populációjának és átirányításának megfelelő szolgáltatásokat. 

- Szuverén vagy hibrid felhőalapú környezetek

    A felhasználók egy meghatározott [Azure Government régióba](../../azure-government/compare-azure-government-global-azure.md#application-insights)küldhetik az adatküldést.
    A kapcsolódási karakterláncok lehetővé teszik a végponti beállítások megadását az intranetes kiszolgálókhoz vagy a hibrid felhő beállításaihoz. 

## <a name="getting-started"></a>Első lépések

### <a name="finding-my-connection-string"></a>A saját kapcsolatok karakterláncának megkeresése?

A Application Insights erőforrás áttekintés paneljén megjelenik a kapcsolatok karakterlánca.

![a kapcsolatok karakterlánca az Áttekintés panelen](media/overview-dashboard/overview-connection-string.png)

### <a name="schema"></a>Séma

#### <a name="max-length"></a>Maximális hossz

A kapcsolatok maximális hossza 4096 karakter.

#### <a name="key-value-pairs"></a>Kulcs-érték párok

A kapcsolatok karakterlánca a kulcs-érték párokat tartalmazó, pontosvesszővel elválasztott beállítások listájából áll:`key1=value1;key2=value2;key3=value3`

#### <a name="syntax"></a>Syntax

- `InstrumentationKey`(pl.: 00000000-0000-0000-0000-000000000000)  A kapcsolatok karakterlánca mező **kitöltése kötelező** .
- `Authorization`(pl.: rendszerállapotkulcsot) (Ez a beállítás nem kötelező, mert ma már csak a rendszerállapotkulcsot-hitelesítés támogatott.)
- `EndpointSuffix`(pl.: applicationinsights.azure.cn) A végpont utótagjának beállítása arra utasítja az SDK-t, amelyhez az Azure Cloud csatlakozik. Az SDK össze fogja állítani a végpont többi részét az egyes szolgáltatásokhoz.
- Explicit végpontok.
  Bármely szolgáltatás explicit módon felülbírálható a kapcsolatok karakterláncában.
   - `IngestionEndpoint`(pl.: `https://dc.applicationinsights.azure.com` )
   - `LiveEndpoint`(pl.: `https://live.applicationinsights.azure.com` )
   - `ProfilerEndpoint`(pl.: `https://profiler.applicationinsights.azure.com` )
   - `SnapshotEndpoint`(pl.: `https://snapshot.applicationinsights.azure.com` )

#### <a name="endpoint-schema"></a>Végpont sémája

`<prefix>.<suffix>`
- Előtag: a szolgáltatást definiálja. 
- Utótag: a közös tartománynevet határozza meg.

##### <a name="valid-suffixes"></a>Érvényes utótagok

Itt látható az érvényes utótagok listája 
- applicationinsights.azure.cn
- applicationinsights.us


Lásd még:https://docs.microsoft.com/azure/azure-monitor/app/custom-endpoints#regions-that-require-endpoint-modification


##### <a name="valid-prefixes"></a>Érvényes előtagok

- [Telemetria](./app-insights-overview.md)betöltése:`dc`
- [Élő metrikák](./live-stream.md):`live`
- [Profiler](./profiler-overview.md):`profiler`
- [Pillanatkép](./snapshot-debugger.md):`snapshot`



## <a name="connection-string-examples"></a>Példák a kapcsolatok karakterláncára


### <a name="minimal-valid-connection-string"></a>Minimális érvényes kapcsolatok karakterlánca

`InstrumentationKey=00000000-0000-0000-0000-000000000000;`

Ebben a példában csak a kialakítási kulcs van beállítva.

- Az engedélyezési séma alapértelmezett értéke "rendszerállapotkulcsot" 
- Instrumentation-kulcs: 00000000-0000-0000-0000-000000000000
- A regionális szolgáltatási URI-k az [SDK alapértelmezett értékein](https://github.com/microsoft/ApplicationInsights-dotnet/blob/e50d569cebf485e72e98f4a08a0bc0e30cdf42bc/BASE/src/Microsoft.ApplicationInsights/Extensibility/Implementation/Endpoints/Constants.cs#L6) alapulnak, és a nyilvános globális Azure-hoz csatlakoznak:
   - Lenyelés`https://dc.services.visualstudio.com/`
   - Élő metrikák:`https://rt.services.visualstudio.com/`
   - Profiler`https://agent.azureserviceprofiler.net/`
   - Hibakereső`https://agent.azureserviceprofiler.net/`



### <a name="connection-string-with-endpoint-suffix"></a>Végponti utótaggal rendelkező kapcsolatok karakterlánca

`InstrumentationKey=00000000-0000-0000-0000-000000000000;EndpointSuffix=ai.contoso.com;`

Ebben a példában ez a kapcsolatot megadó karakterlánc megadja a végpont utótagját, és az SDK létrehozza a szolgáltatási végpontokat.

- Az engedélyezési séma alapértelmezett értéke "rendszerállapotkulcsot" 
- Instrumentation-kulcs: 00000000-0000-0000-0000-000000000000
- A regionális szolgáltatási URI-k a megadott végponti utótagon alapulnak: 
   - Lenyelés`https://dc.ai.contoso.com`
   - Élő metrikák:`https://live.ai.contoso.com`
   - Profiler`https://profiler.ai.contoso.com`
   - Hibakereső`https://snapshot.ai.contoso.com`  



### <a name="connection-string-with-explicit-endpoint-overrides"></a>Explicit végponti felülbírálásokkal rendelkező kapcsolatok karakterlánca 

`InstrumentationKey=00000000-0000-0000-0000-000000000000;IngestionEndpoint=https://custom.com:111/;LiveEndpoint=https://custom.com:222/;ProfilerEndpoint=https://custom.com:333/;SnapshotEndpoint=https://custom.com:444/;`

Ebben a példában ez a hálózati karakterlánc explicit felülbírálásokat határoz meg minden szolgáltatáshoz. Az SDK a módosítás nélkül megadott pontos végpontokat fogja használni.

- Az engedélyezési séma alapértelmezett értéke "rendszerállapotkulcsot" 
- Instrumentation-kulcs: 00000000-0000-0000-0000-000000000000
- A regionális szolgáltatási URI-k az explicit felülbírálási értékeken alapulnak: 
   - Lenyelés`https://custom.com:111/`
   - Élő metrikák:`https://custom.com:222/`
   - Profiler`https://custom.com:333/`
   - Hibakereső`https://custom.com:444/`  


## <a name="how-to-set-a-connection-string"></a>A kapcsolódási karakterlánc beállítása

A következő SDK-verziók támogatják a kapcsolatok karakterláncait:
- .NET és .NET Core v 2.12.0
- Java v 2.5.1 és Java 3,0
- JavaScript v 2.3.0
- NodeJS v 1.5.0
- Python v 1.0.0

A kapcsolatok karakterláncát kód, környezeti változó vagy konfigurációs fájl alapján lehet beállítani.



### <a name="environment-variable"></a>Környezeti változó

- Kapcsolatok karakterlánca:`APPLICATIONINSIGHTS_CONNECTION_STRING`

# <a name="netnetcore"></a>[.NET/. NetCore](#tab/net)

TelemetryConfiguration. ConnectionString:https://github.com/microsoft/ApplicationInsights-dotnet/blob/add45ceed35a817dc7202ec07d3df1672d1f610d/BASE/src/Microsoft.ApplicationInsights/Extensibility/TelemetryConfiguration.cs#L271-L274

.NET explicit módon beállítva:
```csharp
var configuration = new TelemetryConfiguration
{
    ConnectionString = "InstrumentationKey=00000000-0000-0000-0000-000000000000;"
};
```

.NET-konfigurációs fájl:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">
    <ConnectionString>InstrumentationKey=00000000-0000-0000-0000-000000000000</ConnectionString>
</ApplicationInsights>
```

Explicit módon beállított NetCore:
```csharp
public void ConfigureServices(IServiceCollection services)
{
    var options = new ApplicationInsightsServiceOptions { ConnectionString = "InstrumentationKey=00000000-0000-0000-0000-000000000000;" };
    services.AddApplicationInsightsTelemetry(options: options);
}
```

NetCore config.jsa következőn: 

```json
{
  "ApplicationInsights": {
    "ConnectionString" : "InstrumentationKey=00000000-0000-0000-0000-000000000000;"
    }
  }
```


# <a name="java"></a>[Java](#tab/java)


A Java (v 2.5. x) explicit módon be van állítva:
```java
TelemetryConfiguration.getActive().setConnectionString("InstrumentationKey=00000000-0000-0000-0000-000000000000");
```

ApplicationInsights.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">
    <ConnectionString>InstrumentationKey=00000000-0000-0000-0000-000000000000;</ConnectionString>
</ApplicationInsights>
```

# <a name="javascript"></a>[JavaScript](#tab/js)

Fontos: a JavaScript nem támogatja a környezeti változók használatát.

A kódrészlet használata:

```javascript
<script type="text/javascript">
var sdkInstance="appInsightsSDK";window[sdkInstance]="appInsights";var aiName=window[sdkInstance],aisdk=window[aiName]||function(e){function n(e){t[e]=function(){var n=arguments;t.queue.push(function(){t[e].apply(t,n)})}}var t={config:e};t.initialize=!0;var i=document,a=window;setTimeout(function(){var n=i.createElement("script");n.src=e.url||"https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js",i.getElementsByTagName("script")[0].parentNode.appendChild(n)});try{t.cookie=i.cookie}catch(e){}t.queue=[],t.version=2;for(var r=["Event","PageView","Exception","Trace","DependencyData","Metric","PageViewPerformance"];r.length;)n("track"+r.pop());n("startTrackPage"),n("stopTrackPage");var s="Track"+r[0];if(n("start"+s),n("stop"+s),n("addTelemetryInitializer"),n("setAuthenticatedUserContext"),n("clearAuthenticatedUserContext"),n("flush"),t.SeverityLevel={Verbose:0,Information:1,Warning:2,Error:3,Critical:4},!(!0===e.disableExceptionTracking||e.extensionConfig&&e.extensionConfig.ApplicationInsightsAnalytics&&!0===e.extensionConfig.ApplicationInsightsAnalytics.disableExceptionTracking)){n("_"+(r="onerror"));var o=a[r];a[r]=function(e,n,i,a,s){var c=o&&o(e,n,i,a,s);return!0!==c&&t["_"+r]({message:e,url:n,lineNumber:i,columnNumber:a,error:s}),c},e.autoExceptionInstrumented=!0}return t}(
{
  connectionString:"InstrumentationKey=00000000-0000-0000-0000-000000000000;"
}
);window[aiName]=aisdk,aisdk.queue&&0===aisdk.queue.length&&aisdk.trackPageView({});
</script>
```


Manuális telepítés:
```javascript
import { ApplicationInsights } from '@microsoft/applicationinsights-web'

const appInsights = new ApplicationInsights({ config: {
  connectionString: 'InstrumentationKey=00000000-0000-0000-0000-000000000000;'
  /* ...Other Configuration Options... */
} });
appInsights.loadAppInsights();
appInsights.trackPageView();
```

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

```javascript
const appInsights = require("applicationinsights");
appInsights.setup("InstrumentationKey=00000000-0000-0000-0000-000000000000;");
appInsights.start();
```

# <a name="python"></a>[Python](#tab/python)

Javasoljuk, hogy a felhasználók állítsa be a környezeti változót.

A kapcsolódási karakterlánc explicit beállítása:

```python
from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.trace.samplers import ProbabilitySampler
from opencensus.trace.tracer import Tracer

tracer = Tracer(exporter=AzureExporter(connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000'), sampler=ProbabilitySampler(1.0))
```


## <a name="next-steps"></a>További lépések

Első lépések futtatáskor:

* [Azure-beli virtuális gépek és Azure-beli virtuálisgép-méretezési csoport – IIS által üzemeltetett alkalmazások](../../azure-monitor/app/azure-vm-vmss-apps.md)
* [IIS-kiszolgáló](../../azure-monitor/app/monitor-performance-live-website-now.md)
* [Azure-webalkalmazások](../../azure-monitor/app/azure-web-apps.md)

Első lépések fejlesztéskor:

* [ASP.NET](../../azure-monitor/app/asp-net.md)
* [ASP.NET Core](../../azure-monitor/app/asp-net-core.md)
* [Java](../../azure-monitor/app/java-get-started.md)
* [Node.js](../../azure-monitor/app/nodejs.md)
* [Python](../../azure-monitor/app/opencensus-python.md)
