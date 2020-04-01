---
title: Kapcsolati karakterláncok az Azure Application Insightsban | Microsoft dokumentumok
description: Kapcsolati karakterláncok használata.
ms.topic: conceptual
author: timothymothra
ms.author: tilee
ms.date: 01/17/2020
ms.reviewer: mbullwin
ms.openlocfilehash: 25eda0ae2b0d873fe9850e5b886489a5f2590e69
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2020
ms.locfileid: "80410615"
---
# <a name="connection-strings"></a>Kapcsolati sztringek

## <a name="overview"></a>Áttekintés

A kapcsolati karakterláncok egyetlen konfigurációs beállítást biztosítanak az Application Insight-felhasználók számára, így nincs szükség több proxybeállításra. Rendkívül hasznos az intranetes webkiszolgálók, szuverén vagy hibrid felhőkörnyezetek számára, amelyek adatokat szeretnének küldeni a figyelési szolgáltatásnak.

A kulcsérték-párok segítségével a felhasználók egyszerűen definiálhatja az egyes Application Insights(AI) szolgáltatások/termékek előtag-utótagkombinációját.

> [!IMPORTANT]
> Nem javasoljuk a kapcsolati karakterlánc és a instrumentation kulcs beállítását. Abban az esetben, ha a felhasználó mindkettőt beállítja, attól függően, hogy melyik volt utoljára beállítva, az lesz az elsőbbség. 


## <a name="scenario-overview"></a>Forgatókönyv áttekintése 

Ügyfél-forgatókönyvek, ahol ezt a legnagyobb hatással képzeljük el:

- Tűzfalkivételek vagy proxyátirányítások 

    Azokban az esetekben, ahol az intranetes webkiszolgáló figyelése szükséges, a korábbi megoldás arra kérte az ügyfeleket, hogy adja hozzá az egyes szolgáltatás végpontok a konfigurációhoz. További információ: [itt](https://docs.microsoft.com/azure/azure-monitor/app/troubleshoot-faq#can-i-monitor-an-intranet-web-server). 
    A kapcsolati karakterláncok jobb alternatívát kínálnak, ha ezt az erőfeszítést egyetlen beállításra csökkentik. Egy egyszerű előtag, utótag módosítása lehetővé teszi az automatikus sokaság és átirányítása az összes végpontok a megfelelő szolgáltatásokhoz. 

- Szuverén vagy hibrid felhőkörnyezetek

    A felhasználók adatokat küldhetnek egy meghatározott [Azure Government-régióba.](https://docs.microsoft.com/azure/azure-government/documentation-government-services-monitoringandmanagement#application-insights)
    A kapcsolati karakterláncok lehetővé teszik az intranetes kiszolgálók vagy a hibrid felhőbeállítások végpontbeállításainak meghatározását. 

## <a name="getting-started"></a>Első lépések

### <a name="finding-my-connection-string"></a>Megtalálta a kapcsolati karakterláncot?

A kapcsolati karakterlánc jelenik meg az Application Insights-erőforrás áttekintése panelen.

![kapcsolati karakterlánc az áttekintő panelen](media/overview-dashboard/overview-connection-string.png)

### <a name="schema"></a>Séma

#### <a name="max-length"></a>Maximális hossz

A kapcsolat maximális támogatott hossza 4096 karakter.

#### <a name="key-value-pairs"></a>Kulcs-érték párok

A kapcsolati karakterlánc a pontosvesszővel elválasztott kulcsérték-párokként ábrázolt beállítások listájából áll:`key1=value1;key2=value2;key3=value3`

#### <a name="syntax"></a>Szintaxis

- `InstrumentationKey`(pl. 0000000-0000-0000-0000-0000000000000000000000000000000000000000000000000000000000000000000000000  A kapcsolati karakterlánc **kötelező** mező.
- `Authorization`(pl. ikey) (Ez a beállítás nem kötelező, mert ma már csak az ikey-hitelesítést támogatjuk.)
- `EndpointSuffix`(pl. applicationinsights.azure.cn) A végpontutótag beállítása utasítja az SDK-t, amelyhez az Azure-felhőhöz csatlakozni szeretne. Az SDK az egyes szolgáltatások végpontjának többi részét is összeállítja.
- Explicit végpontok.
  Bármely szolgáltatás explicit módon felülbírálható a kapcsolati karakterláncban.
   - `IngestionEndpoint`(pl.https://dc.applicationinsights.azure.com)
   - `LiveEndpoint`(pl.https://live.applicationinsights.azure.com)
   - `ProfilerEndpoint`(pl.https://profiler.applicationinsights.azure.com)
   - `SnapshotEndpoint`(pl.https://snapshot.applicationinsights.azure.com)

#### <a name="endpoint-schema"></a>Végpontséma

`<prefix>.<suffix>`
- Előtag: Egy szolgáltatást határoz meg. 
- Utótag: A közös tartománynevet határozza meg.

##### <a name="valid-suffixes"></a>Érvényes utótagok

Itt az érvényes utótagok listája 
- applicationinsights.azure.cn
- applicationinsights.us


Lásd még:https://docs.microsoft.com/azure/azure-monitor/app/custom-endpoints#regions-that-require-endpoint-modification


##### <a name="valid-prefixes"></a>Érvényes előtagok

- [Telemetriai betöltés:](./app-insights-overview.md)`dc`
- [Élő mérőszámok:](./live-stream.md)`live`
- [Profilozó](./profiler-overview.md):`profiler`
- [Pillanatkép](./snapshot-debugger.md):`snapshot`



## <a name="connection-string-examples"></a>Példák a kapcsolati karakterláncra


### <a name="minimal-valid-connection-string"></a>Minimális érvényes kapcsolati karakterlánc

`InstrumentationKey=00000000-0000-0000-0000-000000000000;`

Ebben a példában csak a instrumentation kulcs van beállítva.

- Az engedélyezési séma alapértelmezés szerint "ikey" 
- Műszerezési kulcs: 0000000-0000-0000-0000-0000000000000
- A regionális szolgáltatás URI-i az [SDK alapértelmezett beállításain](https://github.com/microsoft/ApplicationInsights-dotnet/blob/e50d569cebf485e72e98f4a08a0bc0e30cdf42bc/BASE/src/Microsoft.ApplicationInsights/Extensibility/Implementation/Endpoints/Constants.cs#L6) alapulnak, és a nyilvános globális Azure-hoz csatlakoznak:
   - Lenyelés:https://dc.services.visualstudio.com/
   - Élő mutatók:https://rt.services.visualstudio.com/
   - Profiler:https://agent.azureserviceprofiler.net/
   - Hibakereső:https://agent.azureserviceprofiler.net/  



### <a name="connection-string-with-endpoint-suffix"></a>Csatlakozási karakterlánc végpontutótaggal

`InstrumentationKey=00000000-0000-0000-0000-000000000000;EndpointSuffix=ai.contoso.com;`

Ebben a példában ez a kapcsolati karakterlánc adja meg a végpontutótagot, és az SDK szolgáltatásvégpontokat hoz létre.

- Az engedélyezési séma alapértelmezés szerint "ikey" 
- Műszerezési kulcs: 0000000-0000-0000-0000-0000000000000
- A regionális szolgáltatási URI-k a megadott végpontutótagon alapulnak: 
   - Lenyelés:https://dc.ai.contoso.com
   - Élő mutatók:https://live.ai.contoso.com
   - Profiler:https://profiler.ai.contoso.com 
   - Hibakereső:https://snapshot.ai.contoso.com   



### <a name="connection-string-with-explicit-endpoint-overrides"></a>Kapcsolati karakterlánc explicit végpont-felülbírálásokkal 

`InstrumentationKey=00000000-0000-0000-0000-000000000000;IngestionEndpoint=https://custom.com:111/;LiveEndpoint=https://custom.com:222/;ProfilerEndpoint=https://custom.com:333/;SnapshotEndpoint=https://custom.com:444/;`

Ebben a példában ez a kapcsolati karakterlánc minden szolgáltatás explicit felülbírálását határozza meg. Az SDK a módosítás nélkül megadott pontos végpontokat fogja használni.

- Az engedélyezési séma alapértelmezés szerint "ikey" 
- Műszerezési kulcs: 0000000-0000-0000-0000-0000000000000
- A regionális szolgáltatási URI-k az explicit felülbírálási értékeken alapulnak: 
   - Lenyelés: https:\//custom.com:111/
   - Élő mutatók: https:\//custom.com:222/
   - Profilozó: https:\//custom.com:333/ 
   - Hibakereső: https:\//custom.com:444/   


## <a name="how-to-set-a-connection-string"></a>Kapcsolati karakterlánc beállítása

A kapcsolati karakterláncokat a következő SDK-verziók támogatják:
- .NET és .NET Core v2.12.0
- Java v2.5.1 és Java 3.0
- Javascript verzió 2.3.0-s verzió
- NodeJS 1.5.0-s v.-je
- Python-1.0.0-s

A kapcsolati karakterláncot kód-, környezeti változóban vagy konfigurációs fájlban állíthatja be.



### <a name="environment-variable"></a>Környezeti változó

- Kapcsolati karakterlánc:`APPLICATIONINSIGHTS_CONNECTION_STRING`

# <a name="netnetcore"></a>[.NET/.NetCore](#tab/net)

TelemettryConfiguration.ConnectionString:https://github.com/microsoft/ApplicationInsights-dotnet/blob/add45ceed35a817dc7202ec07d3df1672d1f610d/BASE/src/Microsoft.ApplicationInsights/Extensibility/TelemetryConfiguration.cs#L271-L274

.Net Explicit set:
```csharp
var configuration = new TelemetryConfiguration
{
    ConnectionString = "InstrumentationKey=00000000-0000-0000-0000-000000000000;"
};
```

.Net konfigurációs fájl:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">
    <ConnectionString>InstrumentationKey=00000000-0000-0000-0000-000000000000</ConnectionString>
</ApplicationInsights>
```


NetCore config.json: 

```json
{
  "ApplicationInsights": {
    "ConnectionString" : "InstrumentationKey=00000000-0000-0000-0000-000000000000;"
    }
  }
```


# <a name="java"></a>[Java](#tab/java)


Java (v2.5.x) Explicit set:
```java
TelemetryConfiguration.getActive().setConnectionString("InstrumentationKey=00000000-0000-0000-0000-000000000000");
```

ApplicationInsights.xml fájl
```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">
    <ConnectionString>InstrumentationKey=00000000-0000-0000-0000-000000000000;</ConnectionString>
</ApplicationInsights>
```

# <a name="javascript"></a>[JavaScript](#tab/js)

Fontos: A Javascript nem támogatja a környezeti változók használatát.

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


Kézi beállítás:
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

A kapcsolati karakterlánc explicit beállítása:

```python
from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.trace.samplers import ProbabilitySampler
from opencensus.trace.tracer import Tracer

tracer = Tracer(exporter=AzureExporter(connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000'), sampler=ProbabilitySampler(1.0))
```


## <a name="next-steps"></a>További lépések

Első lépések futtatáskor:

* [Az Azure virtuális gép és az Azure virtuálisgép-méretezési készlete IIS által üzemeltetett alkalmazások](../../azure-monitor/app/azure-vm-vmss-apps.md)
* [IIS-kiszolgáló](../../azure-monitor/app/monitor-performance-live-website-now.md)
* [Azure Web Apps](../../azure-monitor/app/azure-web-apps.md)

Első lépések fejlesztéskor:

* [ASP.NET](../../azure-monitor/app/asp-net.md)
* [ASP.NET Core](../../azure-monitor/app/asp-net-core.md)
* [Java](../../azure-monitor/app/java-get-started.md)
* [Node.js](../../azure-monitor/app/nodejs.md)
* [Python](../../azure-monitor/app/opencensus-python.md)
