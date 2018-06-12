---
title: Application Insights API egyéni események és metrikák |} Microsoft Docs
description: Néhány sornyi kód beszúrása a eszköz- vagy asztali alkalmazások, a képernyőn látható weblapon vagy a szolgáltatás használatának nyomon követése és eseményadatokat.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 80400495-c67b-4468-a92e-abf49793a54d
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: conceptual
ms.date: 06/08/2018
ms.author: mbullwin
ms.openlocfilehash: 5c33e1a5568de5fffb5ea9cedb43bdc04aeaeba7
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35293841"
---
# <a name="application-insights-api-for-custom-events-and-metrics"></a>Application Insights API egyéni események és metrikák

Helyezze be néhány sornyi kódot az alkalmazásban, ha szeretné tudni, a felhasználók tevékenységeit vele, vagy problémák diagnosztizálásához. Eszköz- és asztali alkalmazások, a webes ügyfelekkel és a webkiszolgálók telemetriai adatokat küldhet. Használja a [Azure Application Insights](app-insights-overview.md) alapvető telemetriai API küldése egyéni események és metrikák és a saját szabványos telemetriai verziói. Ez az API az azonos API-t a szabványos Application Insights-adatgyűjtők használó.

## <a name="api-summary"></a>API összefoglaló
Az API-t egységes néhány kisebb eltérések mellett minden platformon.

| Módszer | A használt |
| --- | --- |
| [`TrackPageView`](#page-views) |Lapok, képernyők, paneleken vagy űrlap. |
| [`TrackEvent`](#trackevent) |Felhasználói műveletek és más események. Vagy a felhasználó viselkedésének nyomon, vagy teljesítményének figyelésére használható. |
| [`TrackMetric`](#trackmetric) |Például a várólista-hosszúságok meggátolják nem kapcsolódik az adott események TELJESÍTMÉNYMÉRÉSEK. |
| [`TrackException`](#trackexception) |Naplózási kivételek elemzés céljából. Nyomkövetés, ahonnan merülnek fel az eseményeket és vizsgálja meg a híváslánc megjelenik-e. |
| [`TrackRequest`](#trackrequest) |A naplózás a gyakoriság és a kiszolgálói kérelmek Teljesítményelemzési célokat időtartamát. |
| [`TrackTrace`](#tracktrace) |Diagnosztikai naplózás. Külső naplókat is rögzítheti. |
| [`TrackDependency`](#trackdependency) |A naplózás a telepítések időtartamát és az alkalmazás függ külső összetevőket hívások gyakorisága. |

Is [tulajdonságok és metrikákat](#properties) telemetriai hívásokat a legtöbb.

## <a name="prep"></a>Előkészületek
Ha egy hivatkozás még nem rendelkezik az Application Insights SDK:

* Az Application Insights SDK hozzáadása a projekthez:

  * [ASP.NET project](app-insights-asp-net.md)
  * [Java project](app-insights-java-get-started.md)
  * [NODE.js-projekt](app-insights-nodejs.md)
  * [Az összes weboldal JavaScript](app-insights-javascript.md) 
* Az eszköz vagy a webkiszolgáló kódjába illessze be a következőt:

    *C#:* `using Microsoft.ApplicationInsights;`

    *Visual Basic:* `Imports Microsoft.ApplicationInsights`

    *Java:* `import com.microsoft.applicationinsights.TelemetryClient;`
    
    *NODE.js:* `var applicationInsights = require("applicationinsights");`

## <a name="get-a-telemetryclient-instance"></a>A TelemetryClient példánya beolvasása
Egy példányát `TelemetryClient` (csak a JavaScript weblapok):

*C#*

    private TelemetryClient telemetry = new TelemetryClient();

*Visual Basic*

    Private Dim telemetry As New TelemetryClient

*Java*

    private TelemetryClient telemetry = new TelemetryClient();
    
*Node.js*

    var telemetry = applicationInsights.defaultClient;


TelemetryClient szálbiztos.

Az ASP.NET és a Java-projektek esetében a bejövő HTTP-kérelmek automatikus rögzítése. Előfordulhat, hogy kíván létrehozni az alkalmazás más modul TelemetryClient további példányai. Például előfordulhat, hogy egy TelemetryClient példány jelentés üzleti logika eseményekre a köztes osztályban. Beállítható például a felhasználói azonosítóját és DeviceId gépének azonosítását. Ez az információ minden események által küldött a példány van csatolva. 

*C#*

    TelemetryClient.Context.User.Id = "...";
    TelemetryClient.Context.Device.Id = "...";

*Java*

    telemetry.getContext().getUser().setId("...);
    telemetry.getContext().getDevice().setId("...");

A Node.js projektek használható `new applicationInsights.TelemetryClient(instrumentationKey?)` hozzon létre egy új példányát, de ez javasolt a singleton elkülönített konfigurációja igénylő forgatókönyvek esetén csak `defaultClient`.

## <a name="trackevent"></a>TrackEvent
Az Application Insights egy *egyéni esemény* a megjeleníthetők adatpont van [Metrikaböngésző](app-insights-metrics-explorer.md) egy összesített száma, és a [diagnosztikai keresési](app-insights-diagnostic-search.md) önálló események. (Ez nem kapcsolódó MVC vagy más keretrendszer "események.")

Helyezze be `TrackEvent` hívja be a kódot a különböző események száma. Milyen gyakran válassza ki a felhasználók egy adott szolgáltatással, milyen gyakran azok meghatározott célok elérése, vagy lehet, hogy milyen gyakran akkor hibák adott típusú.

Például játék alkalmazásban elküldeni egy eseményt, amikor egy felhasználó nyer:

*JavaScript*

    appInsights.trackEvent("WinGame");

*C#*

    telemetry.TrackEvent("WinGame");

*Visual Basic*

    telemetry.TrackEvent("WinGame")

*Java*

    telemetry.trackEvent("WinGame");
    
*Node.js*

    telemetry.trackEvent({name: "WinGame"});

### <a name="view-your-events-in-the-microsoft-azure-portal"></a>Az események megtekintése a Microsoft Azure portálon
Az események számát megtekintéséhez nyissa meg a [Metrikaböngésző](app-insights-metrics-explorer.md) panelen új diagram hozzáadása, és válassza ki **események**.  

![Tekintse meg az egyéni események száma](./media/app-insights-api-custom-events-metrics/01-custom.png)

Hasonlítsa össze a különböző események számát, állítsa a diagramtípus **rács**, és az esemény neve:

![Adja meg a diagram típusát és a csoportosítás](./media/app-insights-api-custom-events-metrics/07-grid.png)

A rács kattintson az esemény a nevét, tekintse meg az esemény egyedi előfordulását. További részletek - kattintson bármely előfordulásainak kívánt számát, a listában.

![Az események áthatoló részletezést](./media/app-insights-api-custom-events-metrics/03-instances.png)

Adott események keresési vagy a Metrikaböngésző összpontosíthat, állítsa be a panelt szűrő érdekli, az esemény nevét:

![Nyissa meg a szűrők, bontsa ki az esemény nevét, és válasszon egy vagy több érték](./media/app-insights-api-custom-events-metrics/06-filter.png)

### <a name="custom-events-in-analytics"></a>Egyéni események Analytics

A telemetriai adatok érhető el a `customEvents` a tábla [Application Insights Analytics](app-insights-analytics.md). Minden egyes hívásakor jelöl `trackEvent(..)` az alkalmazásban. 

Ha [mintavételi](app-insights-sampling.md) működik, az elemek száma tulajdonság jeleníti meg egy értéket 1-nél nagyobb. Példa az elemek száma a == 10 azt jelenti, hogy a trackevent() függvény 10 hívások, a mintavételi folyamat csak akkor továbbítódnak, ezek egyikét. Ahhoz, hogy helyes-e az egyéni események számát, használjon ezért használható kóddal például `customEvent | summarize sum(itemCount)`.


## <a name="trackmetric"></a>TrackMetric

Az Application Insights is diagram, amelyek nem kapcsolódnak adott események metrikák. Például felügyelheti a várólista hossza rendszeres időközönként. A metrika egyedi mérések kevésbé fontos, mint a változások és a trendeket, és így statisztikai diagramok lehetnek hasznosak.

Metrikák küldhet az Application Insights részére, használhatja a `TrackMetric(..)` API. Metrika küldendő két módja van: 

* Egyetlen értéket. Minden alkalommal, amikor az alkalmazás hajt végre egy mérték, az Application Insights küld a megfelelő értékkel. Tegyük fel például, hogy rendelkezik-e a tárolóban lévő elemek száma leíró metrikát. Egy adott időszakon belül mindhárom elem helyezze a tárolóba, és két elem távolítsa el. Ennek megfelelően meghívta `TrackMetric` kétszer: először átadja a érték `3` , majd értéke `-2`. Az Application Insights mindkét értéket tárolja az Ön nevében. 

* Összesítést. Az metrikák használatakor minden egyetlen mérési ritkán érdekében áll. Ehelyett fontos Mi történt egy adott időszakon belül összegzését. Ilyen összegzését nevezik _összesítési_. A fenti példában az adott időszakra vonatkozó összesített metrika összegük van `1` , a szám a metrika értékének `2`. Az összesítési módszer használata esetén csak indításakor `TrackMetric` egyszer egy adott időszakra vonatkozóan, valamint az összesített értékek küldéséhez. Ez az az ajánlott módszer, mivel jelentősen csökkentheti a költségek és a teljesítmény terhet elküldésével kevesebb adatpontok Application insights részére, továbbra is az összes vonatkozó információk összegyűjtése közben.

### <a name="examples"></a>Példák:

#### <a name="single-values"></a>Egyetlen értékek

Küldése a egyetlen metrika értékét:

*JavaScript*

 ```Javascript
     appInsights.trackMetric("queueLength", 42.0);
 ```

*C#*

```csharp
    var sample = new MetricTelemetry();
    sample.Name = "metric name";
    sample.Value = 42.3;
    telemetryClient.TrackMetric(sample);
```

*Java*

```Java
    
    telemetry.trackMetric("queueLength", 42.0);

```

*Node.js*

 ```Javascript
     telemetry.trackMetric({name: "queueLength", value: 42.0});
 ```

#### <a name="aggregating-metrics"></a>Metrikák összesítése

Javasoljuk, hogy összesített metrikák mielőtt elküldi őket az alkalmazásból, a sávszélesség csökkentése érdekében költségeket, és a teljesítmény javítása érdekében.
Itt látható egy példa összesítése kódot:

*C#*

```csharp
using System;
using System.Threading;
using System.Threading.Tasks;

using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.DataContracts;

namespace MetricAggregationExample
{
    /// <summary>
    /// Aggregates metric values for a single time period.
    /// </summary>
    internal class MetricAggregator
    {
        private SpinLock _trackLock = new SpinLock();

        public DateTimeOffset StartTimestamp    { get; }
        public int Count                        { get; private set; }
        public double Sum                       { get; private set; }
        public double SumOfSquares              { get; private set; }
        public double Min                       { get; private set; }
        public double Max                       { get; private set; }
        public double Average                   { get { return (Count == 0) ? 0 : (Sum / Count); } }
        public double Variance                  { get { return (Count == 0) ? 0 : (SumOfSquares / Count)
                                                                                  - (Average * Average); } }
        public double StandardDeviation         { get { return Math.Sqrt(Variance); } }

        public MetricAggregator(DateTimeOffset startTimestamp)
        {
            this.StartTimestamp = startTimestamp;
        }

        public void TrackValue(double value)
        {
            bool lockAcquired = false;

            try
            {
                _trackLock.Enter(ref lockAcquired);

                if ((Count == 0) || (value < Min))  { Min = value; }
                if ((Count == 0) || (value > Max))  { Max = value; }
                Count++;
                Sum += value;
                SumOfSquares += value * value;
            }
            finally
            {
                if (lockAcquired)
                {
                    _trackLock.Exit();
                }
            }
        }
    }   // internal class MetricAggregator

    /// <summary>
    /// Accepts metric values and sends the aggregated values at 1-minute intervals.
    /// </summary>
    public sealed class Metric : IDisposable
    {
        private static readonly TimeSpan AggregationPeriod = TimeSpan.FromSeconds(60);

        private bool _isDisposed = false;
        private MetricAggregator _aggregator = null;
        private readonly TelemetryClient _telemetryClient;

        public string Name { get; }

        public Metric(string name, TelemetryClient telemetryClient)
        {
            this.Name = name ?? "null";
            this._aggregator = new MetricAggregator(DateTimeOffset.UtcNow);
            this._telemetryClient = telemetryClient ?? throw new ArgumentNullException(nameof(telemetryClient));

            Task.Run(this.AggregatorLoopAsync);
        }

        public void TrackValue(double value)
        {
            MetricAggregator currAggregator = _aggregator;
            if (currAggregator != null)
            {
                currAggregator.TrackValue(value);
            }
        }

        private async Task AggregatorLoopAsync()
        {
            while (_isDisposed == false)
            {
                try
                {
                    // Wait for end end of the aggregation period:
                    await Task.Delay(AggregationPeriod).ConfigureAwait(continueOnCapturedContext: false);

                    // Atomically snap the current aggregation:
                    MetricAggregator nextAggregator = new MetricAggregator(DateTimeOffset.UtcNow);
                    MetricAggregator prevAggregator = Interlocked.Exchange(ref _aggregator, nextAggregator);

                    // Only send anything is at least one value was measured:
                    if (prevAggregator != null && prevAggregator.Count > 0)
                    {
                        // Compute the actual aggregation period length:
                        TimeSpan aggPeriod = nextAggregator.StartTimestamp - prevAggregator.StartTimestamp;
                        if (aggPeriod.TotalMilliseconds < 1)
                        {
                            aggPeriod = TimeSpan.FromMilliseconds(1);
                        }

                        // Construct the metric telemetry item and send:
                        var aggregatedMetricTelemetry = new MetricTelemetry(
                                Name,
                                prevAggregator.Count,
                                prevAggregator.Sum,
                                prevAggregator.Min,
                                prevAggregator.Max,
                                prevAggregator.StandardDeviation);
                        aggregatedMetricTelemetry.Properties["AggregationPeriod"] = aggPeriod.ToString("c");

                        _telemetryClient.Track(aggregatedMetricTelemetry);
                    }
                }
                catch(Exception ex)
                {
                    // log ex as appropriate for your application
                }
            }
        }

        void IDisposable.Dispose()
        {
            _isDisposed = true;
            _aggregator = null;
        }
    }   // public sealed class Metric
}
```

### <a name="custom-metrics-in-metrics-explorer"></a>Egyéni metrikák a Metrikaböngészőben

Az eredmények megtekintéséhez nyissa meg a Metrikaböngésző, és új diagram hozzáadása. A diagram megjelenítése a metrika szerkesztéséhez.

> [!NOTE]
> Az egyéni metrika megjelennek az elérhető mérőszámok listája több percig is eltarthat.
>

![Új diagram hozzáadása vagy a diagram az egyéni, válassza ki és a metrika](./media/app-insights-api-custom-events-metrics/03-track-custom.png)

### <a name="custom-metrics-in-analytics"></a>Egyéni metrikák Analytics

A telemetriai adatok érhető el a `customMetrics` a tábla [Application Insights Analytics](app-insights-analytics.md). Minden egyes hívásakor jelöl `trackMetric(..)` az alkalmazásban.
* `valueSum` -Ez az a mérések összege. Ahhoz, hogy a középérték, nullával `valueCount`.
* `valueCount` -A volt összesíti ebbe a mérések számát `trackMetric(..)` hívható meg.

## <a name="page-views"></a>Lapmegtekintések
Egy eszköz vagy a weblap alkalmazásban lap nézet telemetriai küldi alapértelmezés szerint ha egyes képernyőit vagy lapon be van töltve. De további vagy különböző időpontokban Lapmegtekintések nyomon követésére, hogy módosítható. Például egy alkalmazást, amely megjeleníti a tabulátorokat vagy paneleken, a kívánt nyomon követése lap, amikor a felhasználó megnyit egy új panelen.

![Használati fókuszban panelen – áttekintés](./media/app-insights-api-custom-events-metrics/appinsights-47usage-2.png)

Felhasználó és a munkamenet adatküldést Lapmegtekintések, valamint tulajdonságként, a felhasználó és a munkamenet diagramok származnak életben lap nézet telemetriai adat esetén.

### <a name="custom-page-views"></a>Egyéni Lapmegtekintések
*JavaScript*

    appInsights.trackPageView("tab1");

*C#*

    telemetry.TrackPageView("GameReviewPage");

*Java*

    telemetry.trackPageView("GameReviewPage");

*Visual Basic*

    telemetry.TrackPageView("GameReviewPage")


Ha másik HTML-lapok belül több lap van, megadhatja az URL-cím túl:

    appInsights.trackPageView("tab1", "http://fabrikam.com/page1.htm");

### <a name="timing-page-views"></a>Lapmegtekintések időzítése
Alapértelmezés szerint a idők jelentése szerint **lapmegtekintés betöltési ideje** mérik a, amikor a böngésző elküldi a kérelmet, amíg a böngésző lap betöltési esemény nevezik.

Ehelyett lehetőségek közül választhat:

* Egy explicit időtartamot beállítani a [trackPageView](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md#trackpageview) hívja: `appInsights.trackPageView("tab1", null, null, null, durationInMilliseconds);`.
* Az oldal nézetben hívások időzítése `startTrackPage` és `stopTrackPage`.

*JavaScript*

    // To start timing a page:
    appInsights.startTrackPage("Page1");

...

    // To stop timing and log the page:
    appInsights.stopTrackPage("Page1", url, properties, measurements);

A név első paraméterként használó társítja a kezdési és befejezési hívásokat. Alapértelmezés szerint az aktuális lap neve.

Az eredményül kapott lap betöltési időtartamok Metrikaböngésző jelenik meg a kezdési és befejezési hívások időközétől származik. Már Önnek milyen időköz ténylegesen idő.

### <a name="page-telemetry-in-analytics"></a>Az elemzés lap telemetria

A [Analytics](app-insights-analytics.md) két tábla browser műveletek adatait megjelenítése:

* A `pageViews` táblázat az URL-cím és a lap címét kapcsolatos adatokat tartalmaz.
* A `browserTimings` tábla ügyfél teljesítményét, például a bejövő adatok feldolgozásához szükséges idő kapcsolatos adatokat tartalmaz.

Kereséséhez, mennyi időt vesz igénybe a böngészőben a különböző oldalakhoz feldolgozásához:

```
browserTimings | summarize avg(networkDuration), avg(processingDuration), avg(totalDuration) by name 
```

A különböző böngészők popularities észlelése:

```
pageViews | summarize count() by client_Browser
```

Rendelje hozzá a lapmegtekintések AJAX-hívások, csatlakozik a függőségek:

```
pageViews | join (dependencies) on operation_Id 
```

## <a name="trackrequest"></a>TrackRequest
A kiszolgáló SDK TrackRequest használja a HTTP-kérések naplózására.

Akkor is is meghívhatja, ha szeretné szimulálni a kéréseket a környezetében fut webszolgáltatás modul esetében nem.

Azonban az ajánlott módszer a kérelem telemetriai adatokat küldhet, ahol a kérelem úgy működik, mint egy <a href="#operation-context">műveletkörnyezetet</a>.

## <a name="operation-context"></a>A művelet a környezetben
Telemetria elemek együtt összefüggéseket való műveletet a környezetben. A normál kérés-nyomkövetési modul elvégzi ezt a kivételeket és eseményeket, amelyek küldött HTTP-kérelem feldolgozása közben. A [keresési](app-insights-diagnostic-search.md) és [Analytics](app-insights-analytics.md), könnyedén megtalálhatja a művelettel azonosítóját. a kéréshez társított eseményeket

Lásd: [az Application Insights Telemetria korrelációs](application-insights-correlation.md) korrelációs olvashat.

Ha manuálisan, nyomkövetési telemetria telemetriai korrelációs érdekében ebben a mintában a legegyszerűbb módja:

*C#*

```csharp
// Establish an operation context and associated telemetry item:
using (var operation = telemetryClient.StartOperation<RequestTelemetry>("operationName"))
{
    // Telemetry sent in here will use the same operation ID.
    ...
    telemetryClient.TrackTrace(...); // or other Track* calls
    ...
    // Set properties of containing telemetry item--for example:
    operation.Telemetry.ResponseCode = "200";

    // Optional: explicitly send telemetry item:
    telemetryClient.StopOperation(operation);

} // When operation is disposed, telemetry item is sent.
```

Egy művelet környezet beállítása mellett `StartOperation` hoz létre a megadott típusú telemetriai elemet. A telemetriai adatok elemet elküldi meg azzal, hogy a művelet, vagy ha explicit módon hívja `StopOperation`. Ha `RequestTelemetry` a telemetria-típusként időtartama közötti kezdési és befejezési rendszeres időközönkénti értékre van állítva.

Egy művelet hatókörén belül jelentett telemetriai cikkek ilyen művelet "gyermekeinek" lesz. Sikerült a művelet környezetek beágyazott. 

A Keresés a műveletkörnyezetet létrehozásához használt a **kapcsolódó elemek** listája:

![Kapcsolódó elemek](./media/app-insights-api-custom-events-metrics/21.png)

Lásd: [nyomon követheti az Application Insights .NET SDK-val egyéni műveleteket](application-insights-custom-operations-tracking.md) további információt az egyéni műveletek nyomon követése.

### <a name="requests-in-analytics"></a>Az elemzés kérelmek 

A [Application Insights Analytics](app-insights-analytics.md), megjelenítése kéri fel a a `requests` tábla.

Ha [mintavételi](app-insights-sampling.md) van a művelet az elemek száma tulajdonság értékét fogja megjeleníteni a 1-nél nagyobb. Példa az elemek száma a == 10 azt jelenti, hogy trackRequest() 10 hívások, a mintavételi folyamat csak akkor továbbítódnak, ezek egyikét. Kérelmek és átlagos időtartama szegmentált kérelem neve helyes számát, amelyet kódot, mint:

```AIQL
requests | summarize count = sum(itemCount), avgduration = avg(duration) by name
```


## <a name="trackexception"></a>TrackException
Kivételek küldése az Application Insights:

* A [megszámolni](app-insights-metrics-explorer.md), mint arra utal, hogy egy probléma gyakorisága.
* A [vizsgálja meg az egyes előfordulások](app-insights-diagnostic-search.md).

A jelentései tartalmazzák a híváslánc megjelenik.

*C#*

    try
    {
        ...
    }
    catch (Exception ex)
    {
       telemetry.TrackException(ex);
    }

*Java*

    try {
        ...
    } catch (Exception ex) {
        telemetry.trackException(ex);
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
    
*Node.js*

    try
    {
       ...
    }
    catch (ex)
    {
       telemetry.trackException({exception: ex});
    }

Az SDK-k kivételeket sok automatikusan, így nem mindig kell TrackException explicit módon hívja.

* ASP.NET: [írhat kódot a kivételeket](app-insights-asp-net-exceptions.md).
* J2EE: [kivételek automatikusan észlelt](app-insights-java-get-started.md#exceptions-and-request-failures).
* JavaScript: Kivételek automatikusan észlelt. Ha le szeretné tiltani automatikus gyűjtemény, vonal felvétele a weblapok beillesztett a kódrészletet:

    ```
    ({
      instrumentationKey: "your key"
      , disableExceptionTracking: true
    })
    ```

### <a name="exceptions-in-analytics"></a>Az elemzés kivételek

A [Application Insights Analytics](app-insights-analytics.md), kivételek jelennek meg a `exceptions` tábla.

Ha [mintavételi](app-insights-sampling.md) működik, a `itemCount` tulajdonság jeleníti meg egy értéket 1-nél nagyobb. Példa az elemek száma a == 10 azt jelenti, hogy trackException() 10 hívások, a mintavételi folyamat csak akkor továbbítódnak, ezek egyikét. Kivétel típusa által szegmentált kivételek megfelelő számát, amelyet kódot, mint:

```
exceptions | summarize sum(itemCount) by type
```

A legtöbb fontos verem információ már kibontása külön változók, de lekérni a egymástól a `details` struktúra szeretné még többet. Mivel ez a struktúra dinamikus, akkor az eredmény, a várt típus kell konvertálni. Példa:

```AIQL
exceptions
| extend method2 = tostring(details[0].parsedStack[1].method)
```

Kapcsolódó kérésük kivételek társítani, használja az illesztés:

```
exceptions
| join (requests) on operation_Id 
```

## <a name="tracktrace"></a>TrackTrace
Használjon TrackTrace problémák diagnosztizálása az Application Insights "navigációs nyomokat" elküldésével. Adattömbök diagnosztikai adatok küldése, és vizsgálja meg azokat a [diagnosztikai keresési](app-insights-diagnostic-search.md).

A .NET [adapterek jelentkezzen](app-insights-asp-net-trace-logs.md) külső naplókat küld a portál az API segítségével.

A Java nyelven [szabványos figyelő szoftverek, például Log4J, Logback](app-insights-java-trace-logs.md) Application Insights Log4j vagy a Logback Appenders külső naplókat küld a portál használatával.

*C#*

    telemetry.TrackTrace(message, SeverityLevel.Warning, properties);

*Java*

    telemetry.trackTrace(message, SeverityLevel.Warning, properties);
    
*Node.js*

    telemetry.trackTrace({message: message, severity:applicationInsights.Contracts.SeverityLevel.Warning, properties:properties});


Üzenet tartalma kereshet, de (ellentétben a tulajdonságértékek) nem lehet szűrni rajta.

A méretkorlát a `message` sokkal nagyobb, mint a Tulajdonságok vonatkozó korlátozást.
TrackTrace előnye, hogy viszonylag hosszú adatok helyezhetik az üzenetben. Például lehet kódolása nincs POST-adatokat.  

Emellett egy súlyossági szintet adhat hozzá az üzenetet. És egyéb telemetriai adatok, például értékeket is hozzáadhat tulajdonság segítségével szűrőt, vagy keressen a nyomkövetési más-más részhalmazához. Példa:

*C#*

```C#
    var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
    telemetry.TrackTrace("Slow database response",
                   SeverityLevel.Warning,
                   new Dictionary<string,string> { {"database", db.ID} });
```

*Java*

```Java

    Map<String, Integer> properties = new HashMap<>();
    properties.put("Database", db.ID);
    telemetry.trackTrace("Slow Database response", SeverityLevel.Warning, properties);

```

A [keresési](app-insights-diagnostic-search.md), majd egyszerűen kiszűrheti az összes üzenet egy adott súlyossági szintet az adott adatbázishoz kapcsolódik.


### <a name="traces-in-analytics"></a>Nyomok Analytics

A [Application Insights Analytics](app-insights-analytics.md), TrackTrace hívások jelennek meg a `traces` tábla.

Ha [mintavételi](app-insights-sampling.md) működik, az elemek száma tulajdonság jeleníti meg egy értéket 1-nél nagyobb. Példa az elemek száma == 10 azt jelenti, hogy 10 hívásainak `trackTrace()`, a mintavételi folyamat csak akkor továbbítódnak, ezek egyikét. Ahhoz, hogy a helyes nyomkövetési indított hívások száma., kell ezért kódot használja, mint `traces | summarize sum(itemCount)`.

## <a name="trackdependency"></a>TrackDependency
A TrackDependency hívás segítségével nyomon követheti a válaszidejét és sikerességi arányát kód külső kódnak küldött hívások. A portál függőségi diagramjain jelennek meg.

*C#*

```csharp
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
     // The call above has been made obsolete in the latest SDK. The updated call follows this format:
     // TrackDependency (string dependencyTypeName, string dependencyName, string data, DateTimeOffset startTime, TimeSpan duration, bool success);
}
```

*Java*

```Java
    boolean success = false;
    long startTime = System.currentTimeMillis();
    try {
        success = dependency.call();
    }
    finally {
        long endTime = System.currentTimeMillis();
        long delta = endTime - startTime;
        RemoteDependencyTelemetry dependencyTelemetry = new RemoteDependencyTelemetry("My Dependency", "myCall", delta, success);
        telemetry.setTimeStamp(startTime);
        telemetry.trackDependency(dependencyTelemetry);
    }

```

*JavaScript*

```Javascript
var success = false;
var startTime = new Date().getTime();
try
{
    success = dependency.Call();
}
finally
{
    var elapsed = new Date() - startTime;
    telemetry.trackDependency({dependencyTypeName: "myDependency", name: "myCall", duration: elapsed, success:success});
}
```

Ne feledje, hogy a kiszolgáló SDK-k tartalmaz egy [függőségi modul](app-insights-asp-net-dependencies.md) , amely észleli, és nyomon követi az egyes függőségi hívások automatikusan – például adatbázisok és a REST API-k. Kell egy ügynököt telepít a kiszolgálót, és ellenőrizze a modul működik. 

A Java, bizonyos függőségi hívások esetében is nyomon követi használatával [Java ügynök](app-insights-java-agent.md).

Ha szeretné nyomon követni a hívásokat, amely a automatizált követési nem dolgozza fel, vagy ha nem kívánja telepíteni az ügynököt a hívás használja.

A szabványos függőségi követése modul C# kikapcsolásához szerkesztése [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md) , és törölje a hivatkozása `DependencyCollector.DependencyTrackingTelemetryModule`. A Java nem telepítse java ügynök Ha nem szeretne gyűjteni a szabványos függőségek automatikusan.

### <a name="dependencies-in-analytics"></a>Az elemzés függőségek

A [Application Insights Analytics](app-insights-analytics.md), trackDependency hívások megjelenítése a `dependencies` tábla.

Ha [mintavételi](app-insights-sampling.md) működik, az elemek száma tulajdonság jeleníti meg egy értéket 1-nél nagyobb. Példa az elemek száma a == 10 azt jelenti, hogy trackDependency() 10 hívások, a mintavételi folyamat csak akkor továbbítódnak, ezek egyikét. Cél összetevő szegmentált függőségek megfelelő számát, amelyet kódot, mint:

```
dependencies | summarize sum(itemCount) by target
```

Kapcsolódó kérésük függőségek társítani, használja az illesztés:

```
dependencies
| join (requests) on operation_Id 
```

## <a name="flushing-data"></a>Könyvelési adatok
Általában az SDK küldi az adatokat a felhasználó gyakorolt hatás minimalizálása érdekében időnként választott. Azonban bizonyos esetekben érdemes kiüríteni a puffer – például ha egy alkalmazás, amely leállítja az SDK-t használ.

*C#*
 
 ```C#
    telemetry.Flush();
    // Allow some time for flushing before shutdown.
    System.Threading.Thread.Sleep(5000);
```

*Java*

```Java
    telemetry.flush();
    //Allow some time for flushing before shutting down
    Thread.sleep(5000);
```

    
*Node.js*

    telemetry.flush();

Vegye figyelembe, hogy a függvény az aszinkron a [server telemetriai csatorna](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel/).

Ideális esetben flush() módszer használható az alkalmazás leállítás tevékenységben.

## <a name="authenticated-users"></a>Hitelesített felhasználók
A webes alkalmazás a felhasználók (alapértelmezés) azonosítja a cookie-k. Ha az alkalmazás hozzáférésük egy másik számítógép vagy a böngésző, vagy ha ezek a cookie-k törléséhez előfordulhat, hogy számba lehet vennni a felhasználó egynél többször.

Ha az alkalmazás a felhasználók bejelentkeznek, kaphat pontosabb számát úgy, hogy a hitelesített felhasználói Azonosítóját a böngésző-kódban:

*JavaScript*

```JS
// Called when my app has identified the user.
function Authenticated(signInId) {
    var validatedId = signInId.replace(/[,;=| ]+/g, "_");
    appInsights.setAuthenticatedUserContext(validatedId);
    ...
}
```

Az ASP.NET-webalkalmazás MVC alkalmazás, például:

*Razor*

        @if (Request.IsAuthenticated)
        {
            <script>
                appInsights.setAuthenticatedUserContext("@User.Identity.Name
                   .Replace("\\", "\\\\")"
                   .replace(/[,;=| ]+/g, "_"));
            </script>
        }

Nem használható a felhasználó tényleges bejelentkezési név szükséges. Csak akkor nem lehet, hogy a felhasználó egyedi Azonosítóját. Nem tartalmazhatnak szóközöket és a karakterek `,;=|`.

A felhasználói azonosító is beállította egy munkamenet-cookie-ban és a kiszolgálónak küld el. Ha a kiszolgáló SDK telepítve van, a hitelesített felhasználói azonosító küldött ügyfél- és telemetria környezeti tulajdonságainak részeként. Majd végezhet, és keresse meg azt.

Ha az alkalmazás felhasználók csoportosítja a fiókok, a fiók (az azonos karakter korlátozások) azonosítót is átadhatja.

      appInsights.setAuthenticatedUserContext(validatedId, accountId);

A [Metrikaböngésző](app-insights-metrics-explorer.md), létrehozhat egy diagram, amely megjeleníti **hitelesített felhasználók,**, és **felhasználói fiókok**.

Emellett [keresési](app-insights-diagnostic-search.md) az ügyfél adatpontok adott felhasználói neveket és fiókok.

## <a name="properties"></a>Szűrés, keresést és az adatok példájaként tulajdonságok felhasználásával
Tulajdonságok és mérések csatlakoztatni az események (szabadon is csatlakozva lapon a nézetek, kivételeket és egyéb telemetriai adatokat).

*Tulajdonságok* olyan karakterlánc értékek, amelyek segítségével a telemetriai adatokat a használati jelentések szűréséhez. Például ha az alkalmazás biztosít több játékok, csatolhat a játék neve minden esemény, hogy láthassa, melyik játékok népszerűbbnek.

A karakterlánc hossza 8192 korlátozva van. (Nagy méretű adattömböket írnak küldeni, használja az üzenet paramétere [TrackTrace](#track-trace).)

*Metrikák* számértékek jelenítheti meg grafikusan. Például előfordulhat, hogy kívánt van-e a eredmények, amelyek a gamers elérése fokozatos növekedését. Az eseményhez küldött tulajdonságait is szegmentált a diagramokon, így is ki lehet külön vagy a halmozott diagramok a különböző játékok számára.

A metrika értékek megfelelően megjeleníteni össze kell kisebb 0-nál nagyobb.

Van azonban néhány [tulajdonságait, a tulajdonságértékeket és a metrikák száma vonatkozó korlátozások](#limits) használható.

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

*Node.js*

    // Set up some properties and metrics:
    var properties = {"game": currentGame.Name, "difficulty": currentGame.Difficulty};
    var metrics = {"Score": currentGame.Score, "Opponents": currentGame.OpponentCount};

    // Send the event:
    telemetry.trackEvent({name: "WinGame", properties: properties, measurements: metrics});


*Visual Basic*

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


> [!NOTE]
> Ügyeljen arra, nem tulajdonságok-e jelentkezni személyes azonosításra alkalmas adatokat.
>
>

*Ha követte a metrikák*, nyissa meg a Metrikaböngésző, és válassza ki a a a **egyéni** csoport:

![Nyissa meg a Metrikaböngésző, válassza ki a diagramot, és válassza ki a](./media/app-insights-api-custom-events-metrics/03-track-custom.png)

> [!NOTE]
> Ha a metrika nem jelenik meg, vagy ha a **egyéni** fejléc nem létezik, zárja be a kijelölés panelt, és próbálkozzon újra később. Metrikák néha időigényes egy órával összesítse a feldolgozási folyamaton keresztül.

*Ha követte a tulajdonságok és a metrikák*, szegmentálja a metrika a tulajdonság:

![Állítsa be a csoportosítás, majd válassza ki a tulajdonság szerinti csoportosítás](./media/app-insights-api-custom-events-metrics/04-segment-metric-event.png)

*A diagnosztikai keresési*, megtekintheti a tulajdonságok és az esemény egyedi előfordulását.

![Válasszon ki egy példányt, és adja meg a "..."](./media/app-insights-api-custom-events-metrics/appinsights-23-customevents-4.png)

Használja a **keresési** mező esemény eseményeket, amelyek egy adott tulajdonság értéke.

![Írja be a keresőkifejezést keresési rendszerbe](./media/app-insights-api-custom-events-metrics/appinsights-23-customevents-5.png)

[További információ a keresési kifejezések](app-insights-diagnostic-search.md).

### <a name="alternative-way-to-set-properties-and-metrics"></a>Tulajdonságok és a metrikák alternatív módja
Sokkal kényelmesebb, ha egy külön objektumban esemény paramétereinek hozhatja létre:

    var event = new EventTelemetry();

    event.Name = "WinGame";
    event.Metrics["processingTime"] = stopwatch.Elapsed.TotalMilliseconds;
    event.Properties["game"] = currentGame.Name;
    event.Properties["difficulty"] = currentGame.Difficulty;
    event.Metrics["Score"] = currentGame.Score;
    event.Metrics["Opponents"] = currentGame.Opponents.Length;

    telemetry.TrackEvent(event);

> [!WARNING]
> A telemetriai adatok elem példányt nem használja fel (`event` ebben a példában) Track*() hívása többször. Emiatt a telemetriai adatok küldését a helytelen konfiguráció.
>
>

### <a name="custom-measurements-and-properties-in-analytics"></a>Egyéni mértékek és az elemzés tulajdonságok

A [Analytics](app-insights-analytics.md), egyéni metrikákkal és tulajdonságok megjelenítése a `customMeasurements` és `customDimensions` telemetriai rekordokban attribútumait.

Például hozzáadta a – kéréstelemetria "játék" nevű tulajdonságot, ha a lekérdezés eltérő értékeiből álló "játék" előfordulások száma, és az egyéni metrika "pontszám" átlaga megjelenítése:

```
requests
| summarize sum(itemCount), avg(todouble(customMeasurements.score)) by tostring(customDimensions.game) 
```

Figyelje meg, hogy:

* Egy érték a customDimensions vagy customMeasurements JSON kibontásakor dinamikus típust, és úgy kell alakítania azt `tostring` vagy `todouble`.
* Annak a lehetőségét figyelembe [mintavételi](app-insights-sampling.md), használjon `sum(itemCount)`, nem `count()`.



## <a name="timed"></a> Időzítési események
Egyes esetekben kívánt diagram, hogy mennyi ideig tart egy műveletet. Például előfordulhat, hogy szeretné tudni, hogy a felhasználók mennyi ideig kell figyelembe venni a választási lehetőségek egy játékban hajtsa végre a megfelelő. Ehhez használhatja a mérési paraméter.

*C#*

```C#
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
```

*Java*

```Java
    long startTime = System.currentTimeMillis();

    // perform timed action

    long endTime = System.currentTimeMillis();
    Map<String, Double> metrics = new HashMap<>();
    metrics.put("ProcessingTime", endTime-startTime);

    // Setup some propereties
    Map<String, String> properties = new HashMap<>();
    properties.put("signalSource", currentSignalSource.getName());

    //send the event
    telemetry.trackEvent("SignalProcessed", properties, metrics);

```


## <a name="defaults"></a>Egyéni telemetria alapértelmezett tulajdonságai
Ha tulajdonságértékeit állítja be alapértelmezett néhány, az egyéni események írást, akkor TelemetryClient példányt teheti meg. Minden telemetriai elemet, hogy az ügyfél által küldött vannak csatolva.

*C#*

    using Microsoft.ApplicationInsights.DataContracts;

    var gameTelemetry = new TelemetryClient();
    gameTelemetry.Context.Properties["Game"] = currentGame.Name;
    // Now all telemetry will automatically be sent with the context property:
    gameTelemetry.TrackEvent("WinGame");

*Visual Basic*

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
    
*Node.js*

    var gameTelemetry = new applicationInsights.TelemetryClient();
    gameTelemetry.commonProperties["Game"] = currentGame.Name;

    gameTelemetry.TrackEvent({name: "WinGame"});



Egyéni telemetria hívások felülbírálhatja az alapértelmezett értékeket a tulajdonság szótárak.

*JavaScript a webes ügyfelek*, [JavaScript telemetriai inicializálók használja](#js-initializer).

*Tulajdonságok hozzáadása az összes telemetriai adat*, beleértve a szabványos gyűjtési modulok adatait [megvalósítása `ITelemetryInitializer` ](app-insights-api-filtering-sampling.md#add-properties).

## <a name="sampling-filtering-and-processing-telemetry"></a>A mintavételi, szűrési és telemetriai adatainak feldolgozása
Írhat kódot a telemetriai adatok feldolgozásához, az SDK-ból elküldés előtt. A feldolgozás része a szabványos telemetriai modulok, például HTTP kérelem adatgyűjtési és -függőség gyűjtemény által küldött adatokat.

[Adja hozzá a Tulajdonságok](app-insights-api-filtering-sampling.md#add-properties) való alkalmazásával telemetriai `ITelemetryInitializer`. Például hozzáadhat verziószámok vagy számított értékeket más tulajdonságai közül.

[Szűrés](app-insights-api-filtering-sampling.md#filtering) módosíthatja vagy vesse el a telemetriai adatokat az SDK-ból implementálásával elküldése előtt `ITelemetryProcesor`. Megadhatja, mi küldik vagy elvetett, de a fiókot használja a metrikákat, hatással van. Attól függően, hogy hogyan elveti elemek kapcsolódó elemek közötti navigáláshoz képes elveszhetnek.

[A mintavételi](app-insights-api-filtering-sampling.md) egy csomagolt megoldás a portálra az alkalmazás által küldött adatok mennyisége csökkentése érdekében. Igen, a megjelenő metrikák befolyásolása nélkül. És így nem befolyásolja a problémák diagnosztizálásához például kivételek, a kérelmek és az oldalmegtekintéseket kapcsolódó elemek közötti lépjen arra a képességére kezeli.

[További információk](app-insights-api-filtering-sampling.md).

## <a name="disabling-telemetry"></a>Telemetria letiltása
A *dinamikusan leállítására és elindítására* a gyűjtemény és a telemetriai adatok továbbítása:

*C#*

```csharp

    using  Microsoft.ApplicationInsights.Extensibility;

    TelemetryConfiguration.Active.DisableTelemetry = true;
```

*Java*

```Java
    
    telemetry.getConfiguration().setTrackingDisabled(true);

```

A *tiltsa le a kiválasztott szabványos gyűjtők*– például teljesítményszámlálókat, HTTP-kérelmek vagy függőségek--törlése vagy a megfelelő sorok megjegyzéssé [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md). Ehhez, például, ha azt szeretné, hogy a saját TrackRequest adatküldéshez.

*Node.js*

```Javascript

    telemetry.config.disableAppInsights = true;
```

A *tiltsa le a kiválasztott szabványos gyűjtők*– például teljesítményszámlálókat, HTTP-kérelmek vagy függőségek--inicializálása során láncolt az SDK inicializálási kód konfigurációs módszerek:

```Javascript

    applicationInsights.setup()
        .setAutoCollectRequests(false)
        .setAutoCollectPerformance(false)
        .setAutoCollectExceptions(false)
        .setAutoCollectDependencies(false)
        .setAutoCollectConsole(false)
        .start();
```

Inicializálás után ezeket a gyűjtőket letiltásához a konfigurációs objektum használja: `applicationInsights.Configuration.setAutoCollectRequests(false)`

## <a name="debug"></a>Fejlesztői mód
Hibakeresési, az hasznos lehet a telemetriai adatok sürgős keresztül, hogy az eredmények azonnal láthatók. Akkor is get további üzeneteket, amelyek segítségével nyomon követni a telemetriai adatok problémákat. Kapcsolja ki a termelési, mert az alkalmazás lassíthatja.

*C#*

    TelemetryConfiguration.Active.TelemetryChannel.DeveloperMode = true;

*Visual Basic*

    TelemetryConfiguration.Active.TelemetryChannel.DeveloperMode = True


## <a name="ikey"></a> A kijelölt egyéni telemetria instrumentation kulcs beállítása
*C#*

    var telemetry = new TelemetryClient();
    telemetry.InstrumentationKey = "---my key---";
    // ...


## <a name="dynamic-ikey"></a> Dinamikus instrumentation kulcs
Felfelé telemetriai fejlesztési, tesztelési és éles környezetben keverése elkerüléséhez is [hozzon létre külön Application Insights-erőforrások](app-insights-create-new-resource.md) és azok kulcsait a környezettől függően módosítsa.

Helyett a instrumentation kulcs lekérése a konfigurációs fájlban, beállíthatja a kódban. A kulcs egy inicializálási metódust, például egy ASP.NET-szolgáltatásban Global.aspx.cs osztályból meg:

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



A weboldalakon érdemes lehet, hogy állítson be úgy a webalkalmazás-kiszolgáló állapota, nem pedig kódolási szó a parancsprogramba a. Például a egy weblap ASP.NET alkalmazás hozott létre:

*JavaScript Razor*

    <script type="text/javascript">
    // Standard Application Insights webpage script:
    var appInsights = window.appInsights || function(config){ ...
    // Modify this part:
    }({instrumentationKey:  
      // Generate from server property:
      @Microsoft.ApplicationInsights.Extensibility.
         TelemetryConfiguration.Active.InstrumentationKey;
    }) // ...


## <a name="telemetrycontext"></a>TelemetryContext
TelemetryClient rendelkezik egy környezeti tulajdonság, amely az összes telemetriai adatokkal együtt küldött értékeket tartalmaz. Általában állította a szabványos telemetriai modulok, de is beállíthatja azokat saját maga. Példa:

    telemetry.Context.Operation.Name = "MyOperationName";

Ha ezek bármelyike saját magának, fontolja meg a megfelelő sor eltávolítása [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md), így a és a szabványos értékek nem Zavarba.

* **Az összetevő**: az alkalmazás és annak verzióját.
* **Eszköz**: adatok az eszközről, amelyen fut az alkalmazás. (A webalkalmazásokban, ez az a kiszolgáló vagy a telemetriai adatok által küldött ügyfél-eszköz.)
* **InstrumentationKey**: az Application Insights-erőforrás hol jelenjenek meg a telemetriai adatok Azure-ban. Ez általában felvételre az ApplicationInsights.config.
* **Hely**: az eszköz földrajzi helye.
* **A művelet**: A webalkalmazásokban, az aktuális HTTP-kérelem. A más típusú alkalmazás állíthat események együtt.
  * **Azonosító**: egy generált érték különböző események hibához, így minden esetben a diagnosztikai keresési vizsgálja meg, ha található kapcsolódó elemeket.
  * **Név**: azonosítót, általában az URL-cím a HTTP-kérelem.
  * **SyntheticSource**: Ha nem null értékű vagy üres karakterlánc, amely azt jelzi, hogy a kérelem forrását robot vagy a webalkalmazás tesztjének néven azonosított. Alapértelmezés szerint az ki lesz zárva a Metrikaböngészőben számításból.
* **Tulajdonságok**: az összes telemetriai adatokat küldött tulajdonságok. Az egyes követése * hívások felülbírálható.
* **Munkamenet**: A felhasználói munkamenetet. Az azonosító értéke egy generált érték, amely megváltozik, miközben a felhasználó nem volt aktív egy ideig.
* **Felhasználói**: felhasználói adatokat.

## <a name="limits"></a>Korlátok
[!INCLUDE [application-insights-limits](../../includes/application-insights-limits.md)]

Szerezze meg a sávszélesség-korlátjának elkerüléséhez használja [mintavételi](app-insights-sampling.md).

Annak meghatározásához, hogy mennyi ideig megtartja adatokat, lásd: [az adatmegőrzés és az adatvédelmi](app-insights-data-retention-privacy.md).

## <a name="reference-docs"></a>Segédanyagok
* [ASP.NET-hivatkozás](https://msdn.microsoft.com/library/dn817570.aspx)
* [Java dokumentáció](http://dl.windowsazure.com/applicationinsights/javadoc/)
* [JavaScript-hivatkozás](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md)
* [Android SDK](https://github.com/Microsoft/ApplicationInsights-Android)
* [iOS SDK](https://github.com/Microsoft/ApplicationInsights-iOS)

## <a name="sdk-code"></a>SDK-kód
* [ASP.NET Core SDK](https://github.com/Microsoft/ApplicationInsights-aspnetcore)
* [ASP.NET 5](https://github.com/Microsoft/ApplicationInsights-dotnet)
* [Windows Server csomagok](https://github.com/Microsoft/applicationInsights-dotnet-server)
* [Java SDK](https://github.com/Microsoft/ApplicationInsights-Java)
* [Node.js SDK](https://github.com/Microsoft/ApplicationInsights-Node.js)
* [JavaScript SDK](https://github.com/Microsoft/ApplicationInsights-JS)
* [Összes platform](https://github.com/Microsoft?utf8=%E2%9C%93&query=applicationInsights)

## <a name="questions"></a>Kérdések
* *Milyen kivételek előfordulhat, hogy throw Track_() hívások?*

    Nincs. Ezeket csomagolásához a try-catch záradékban nem kell. Ha az SDK problémákat tapasztal, üzenetek naplózza a hibakeresési konzol kimeneti és – ha az üzenetek beolvasása használatával – diagnosztikai keresésben.
* *Van egy REST API-t adatok beszerzése a portálról?*

    Igen, a [adatelérési API](https://dev.applicationinsights.io/). Adatok kinyerése segítségével [Analytics exportálása a Power bi-bA](app-insights-export-power-bi.md) és [a folyamatos exportálás](app-insights-export-telemetry.md).

## <a name="next"></a>Következő lépések
* [Keresési események és a naplókat](app-insights-diagnostic-search.md)

* [hibaelhárítással](app-insights-troubleshoot-faq.md)


