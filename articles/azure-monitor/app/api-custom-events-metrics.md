---
title: Application Insights API egyéni eseményekhez és a mérőszámok |} A Microsoft Docs
description: Helyezzen be néhány sornyi kóddal az eszköz vagy az asztali alkalmazás, weblap vagy szolgáltatás, a használat nyomon követése, és diagnosztizálhatja a problémákat.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 80400495-c67b-4468-a92e-abf49793a54d
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/16/2018
ms.author: mbullwin
ms.openlocfilehash: 0a31f5450ad5847951393e18e8af648060eb2e1f
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/02/2019
ms.locfileid: "53971358"
---
# <a name="application-insights-api-for-custom-events-and-metrics"></a>Application Insights API egyéni eseményekhez és metrikák

Helyezze be néhány sornyi kóddal az alkalmazásban, ismerje meg, milyen felhasználók általi használatát, vagy a problémák diagnosztizálásához. Eszköz- és asztali alkalmazások, a webes ügyfelektől és a webkiszolgálók telemetriai adatokat küldhet. Használja a [Azure Application Insights](../../application-insights/app-insights-overview.md) core API-t telemetria küldése egyéni események és metrikák és a saját normál telemetriai verziói. Az API-t az azonos API a standard Application Insights-adatgyűjtők használó.

> [!NOTE]
> `TrackMetric()` már nem az előnyben részesített módja a .NET-keretrendszerhez készült egyéni mérőszámok küldése-alapú alkalmazások. A [2.60 – beta 3-as verziójú](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/develop/CHANGELOG.md#version-260-beta3) , az Application Insights .NET SDK egy új módszer [ `TelemetryClient.GetMetric()` ](https://docs.microsoft.com/dotnet/api/microsoft.applicationinsights.telemetryclient.getmetric?view=azure-dotnet) jelent meg. Az Application Insights .NET SDK-tól [verzió 2.72](https://docs.microsoft.com/dotnet/api/microsoft.applicationinsights.telemetryclient.getmetric?view=azure-dotnet) ezt a funkciót már stabil kiadásában.

## <a name="api-summary"></a>API összefoglaló

A core API-t a egységes szereplőkkel néhány változata létezik például minden platformon `GetMetric`(csak a .NET).

| Módszer | Használt |
| --- | --- |
| [`TrackPageView`](#page-views) |Lapok, képernyők, a többi panelen vagy űrlapokat. |
| [`TrackEvent`](#trackevent) |Felhasználói műveletek és az eseményeket. A felhasználók viselkedésének nyomon követésére vagy teljesítményének figyelésére használható. |
| [`GetMetric`](#getmetric) |Nulla és többdimenziós metrikák, központilag konfigurált összesítés, C# csak. |
| [`TrackMetric`](#trackmetric) |TELJESÍTMÉNYMÉRÉSEK, például a várólista-hosszúságok nem kapcsolódó eseményeket. |
| [`TrackException`](#trackexception) |Diagnosztikai naplózás kivételek. Nyomon követheti, ahol azok merülnek fel az eseményeket, és vizsgálja meg a hívásláncokat. |
| [`TrackRequest`](#trackrequest) |A naplózás gyakoriságát és Teljesítményelemzés kiszolgálói kérelem időtartama. |
| [`TrackTrace`](#tracktrace) |Diagnosztikai napló üzeneteket. Külső naplókat is rögzítheti. |
| [`TrackDependency`](#trackdependency) |A naplózás az időtartam és a külső összetevők, amelyek az alkalmazása függ-hívások gyakorisága. |

Is [tulajdonságok és a metrikák](#properties) legtöbb telemetriai hívások.

## <a name="prep"></a>Előkészületek

Ha a hivatkozás még nem rendelkezik az Application Insights SDK:

* Az Application Insights SDK hozzáadása a projekthez:

  * [ASP.NET project](../../azure-monitor/app/asp-net.md)
  * [Java project](../../azure-monitor/app/java-get-started.md)
  * [NODE.js-projektet](../../application-insights/app-insights-nodejs.md)
  * [Az egyes weblap JavaScript](../../azure-monitor/app/javascript.md) 
* Az eszköz vagy a webkiszolgáló kódjába illessze be a következőt:

    *C#:* `using Microsoft.ApplicationInsights;`

    *A Visual Basic:* `Imports Microsoft.ApplicationInsights`

    *Java:* `import com.microsoft.applicationinsights.TelemetryClient;`

    *NODE.js:* `var applicationInsights = require("applicationinsights");`

## <a name="get-a-telemetryclient-instance"></a>A TelemetryClient-példány beolvasása

Első példányát `TelemetryClient` (csak a JavaScript weblapokon):

*C#*

```csharp
private TelemetryClient telemetry = new TelemetryClient();
```

*Visual Basic*

```vb
Private Dim telemetry As New TelemetryClient
```

*Java*

```java
private TelemetryClient telemetry = new TelemetryClient();
``` 

*Node.js*

```javascript
var telemetry = applicationInsights.defaultClient;
```

TelemetryClient szálbiztos.

Az ASP.NET és a Java-projektek a bejövő HTTP-kérések automatikus rögzítése. Előfordulhat, hogy szeretne létrehozni az alkalmazás más modul TelemetryClient további példányait. Például előfordulhat, hogy egy TelemetryClient példány tartalmaz a jelentés üzleti logika események közbenső osztályt. Tulajdonságok, mint például a felhasználói azonosítót és a gép azonosítása DeviceId állíthatja be. Ezt az információt, amely a példány elküld minden eseményhez van csatolva. 

*C#*

```csharp
TelemetryClient.Context.User.Id = "...";
TelemetryClient.Context.Device.Id = "...";
```

*Java*

```java
telemetry.getContext().getUser().setId("...");
telemetry.getContext().getDevice().setId("...");
```

A projektek, a Node.js használhatja `new applicationInsights.TelemetryClient(instrumentationKey?)` hozhat létre egy új példányt, de ez csak ajánlott elkülönített konfigurációt az Egypéldányos igénylő forgatókönyvek `defaultClient`.

## <a name="trackevent"></a>TrackEvent

Az Application insights szolgáltatásban egy *egyéni esemény* van, amely megjelenítheti, hogy egy adatpont [Metrikaböngésző](../../application-insights/app-insights-metrics-explorer.md) összegzett darabszámát, és a [diagnosztikai keresés](../../azure-monitor/app/diagnostic-search.md) önálló események. (Ez nem kapcsolódó MVC vagy más keretrendszer "események".)

Helyezze be `TrackEvent` meghívja a kódban történt különféle események számát. Milyen gyakran a felhasználók megadhatják, egy adott funkció, milyen gyakran adott célok eléréséhez, vagy előfordulhat, hogy milyen gyakran vállalnak bizonyos típusú hibákkal.

Például a játék alkalmazásban, küldünk egy eseményt, amikor egy felhasználó nyer:

*JavaScript*

```javascript
appInsights.trackEvent("WinGame");
```

*C#*

```csharp
telemetry.TrackEvent("WinGame");
```

*Visual Basic*

```vb
telemetry.TrackEvent("WinGame")
```

*Java*

```java
telemetry.trackEvent("WinGame");
```

*Node.js*

```javascript
telemetry.trackEvent({name: "WinGame"});
```

### <a name="custom-events-in-analytics"></a>Egyéni események az Analyticsben

A telemetria érhető el a `customEvents` táblára [Application Insights-elemzési](analytics.md). Minden egyes sora jelöl hívása `trackEvent(..)` az alkalmazásban.

Ha [mintavételi](../../application-insights/app-insights-sampling.md) van folyamatban, az elemek száma tulajdonság egy értéket 1-nél nagyobb mutatja. A példában az elemek száma a == 10 azt jelenti, hogy a trackEvent() a 10 hívás a mintavételi folyamata csak továbbított adatok köre az egyiket. Egyéni események megfelelő száma lekéréséhez használjon ezért használható kóddal például `customEvents | summarize sum(itemCount)`.

## <a name="getmetric"></a>GetMetric

### <a name="examples"></a>Példák

*C#*

```csharp
#pragma warning disable CA1716  // Namespace naming

namespace User.Namespace.Example01
{
    using System;
    using Microsoft.ApplicationInsights;
    using TraceSeveretyLevel = Microsoft.ApplicationInsights.DataContracts.SeverityLevel;

    /// <summary>
    /// Most simple cases are one-liners.
    /// This is all possible without even importing an additional namespace.
    /// </summary>

    public class Sample01
    {
        /// <summary />
        public static void Exec()
        {
            // *** SENDING METRICS ***

            // Recall how you send custom telemetry with Application Insights in other cases, e.g. Events.
            // The following will result in an EventTelemetry object to be sent to the cloud right away.
            TelemetryClient client = new TelemetryClient();
            client.TrackEvent("SomethingInterestingHappened");

            // Metrics work very similar. However, the value is not sent right away.
            // It is aggregated with other values for the same metric, and the resulting summary (aka "aggregate" is sent automatically every minute.
            // To mark this difference, we use a pattern that is similar, but different from the established TrackXxx(..) pattern that sends telemetry right away:

            client.GetMetric("CowsSold").TrackValue(42);

            // *** MULTI-DIMENSIONAL METRICS ***

            // The above example shows a zero-dimensional metric.
            // Metrics can also be multi-dimensional.
            // In the initial version we are supporting up to 2 dimensions, and we will add support for more in the future as needed.
            // Here is an example for a one-dimensional metric:

            Metric animalsSold = client.GetMetric("AnimalsSold", "Species");

            animalsSold.TrackValue(42, "Pigs");
            animalsSold.TrackValue(24, "Horses");

            // The values for Pigs and Horses will be aggregated separately from each other and will result in two distinct aggregates.
            // You can control the maximum number of number data series per metric (and thus your resource usage and cost).
            // The default limits are no more than 1000 total data series per metric, and no more than 100 different values per dimension.
            // We discuss elsewhere how to change them.
            // We use a common .Net pattern: TryXxx(..) to make sure that the limits are observed.
            // If the limits are already reached, Metric.TrackValue(..) will return False and the value will not be tracked. Otherwise it will return True.
            // This is particularly useful if the data for a metric originates from user input, e.g. a file:

            Tuple<int, string> countAndSpecies = ReadSpeciesFromUserInput();
            int count = countAndSpecies.Item1;
            string species = countAndSpecies.Item2;

            if (!animalsSold.TrackValue(count, species))

            {
                client.TrackTrace($"Data series or dimension cap was reached for metric {animalsSold.Identifier.MetricId}.", TraceSeveretyLevel.Error);
            }

            // You can inspect a metric object to reason about its current state. For example:
            int currentNumberOfSpecies = animalsSold.GetDimensionValues(1).Count;
        }

        private static void ResetDataStructure()
        {
            // Do stuff
        }

        private static Tuple<int, string> ReadSpeciesFromUserInput()
        {
            return Tuple.Create(18, "Cows");
        }

        private static int AddItemsToDataStructure()
        {
            // Do stuff
            return 5;
        }
    }
}
```

## <a name="trackmetric"></a>TrackMetric

> [!NOTE]
> A .NET SDK-ban Microsoft.ApplicationInsights.TelemetryClient.TrackMetric elavult. Metrikák mindig kell előre összesített között egy adott időszakban elküldése előtt. A GetMetric(..) túlterheléssel valamelyikével SDK előzetes összesítéssel képességek eléréséhez egy metrika objektum beolvasása. Ha saját előzetes összesítéssel logika, a nyomon követése (ITelemetry metricTelemetry) metódus használatával az eredményül kapott összesítések küldése. Ha az alkalmazás egy külön telemetriai elem küldése minden alkalommal, amikor az összesítés nélkül, idő között, valószínűleg rendelkezik egy alkalmazási helyzet az esemény telemetriát; Tekintse meg a TelemetryClient.TrackEvent (Microsoft.Applicationlnsights.DataContracts.EventTelemetry).

Az Application Insights is diagram, amelyek nem kapcsolódnak az egyes események metrikákat. A várólista hossza például rendszeres időközönként sikerült figyelése. Metrikákkal az egyes mérések érdekesek lehetnek kevesebb, mint a változatok és a trendeket, és így statisztikai diagramok hasznosak.

Annak érdekében, hogy a mérőszámok küldése az Application Insightsba, használhatja a `TrackMetric(..)` API-t. Metrika küldése két módja van:

* Egyetlen érték. Minden alkalommal, amikor az alkalmazás egy mérték hajt végre, a megfelelő értéket küld az Application Insights. Vegyük például, hogy rendelkezik-e a tárolóban lévő elemek száma leíró metrikát. Egy adott időszak során először mindössze három elemet a tárolót, és majd két elemet távolít el. Ennek megfelelően a meghívta `TrackMetric` kétszer: először átadott az érték `3` , majd az értéke `-2`. Az Application Insights mindkét értéket tárolja az Ön nevében.

* Összesítés. Metrikák az használatakor minden egyetlen mérték ritkán érdekében áll. Ehelyett fontos Mi történt egy adott időszakban összegzését. Az ilyen összegzését nevezzük _összesítési_. A fenti példában az adott időszakra vonatkozó összesített metrika összeg van `1` és száma, a metrikaértékek `2`. Az összesítési módszer használata esetén csak hívhat meg `TrackMetric` egyszer egy adott időszakra vonatkozóan, és az összesített értékek küldje. Ez az az ajánlott módszer, mivel azt jelentősen csökkentheti a költségeket és a teljesítmény terhelés küldésével kevesebb adatpontot az Application Insightsba, továbbra is az összes kapcsolódó információt összegyűjtése közben.

### <a name="examples"></a>Példák

#### <a name="single-values"></a>Egyetlen értékként

Egy egyetlen metrikaérték küldése:

*JavaScript*

 ```javascript
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

```java
telemetry.trackMetric("queueLength", 42.0);
```

*Node.js*

 ```javascript
telemetry.trackMetric({name: "queueLength", value: 42.0});
 ```

### <a name="custom-metrics-in-analytics"></a>Egyéni metrikák Analytics

A telemetria érhető el a `customMetrics` táblára [Application Insights-elemzési](analytics.md). Minden egyes sora jelöl hívása `trackMetric(..)` az alkalmazásban.

* `valueSum` – Ez a mérés összege. A középérték lekéréséhez nullával `valueCount`.
* `valueCount` -Mérések száma, amelyek jelenleg lettek összesítve `trackMetric(..)` hívja.

## <a name="page-views"></a>Lapmegtekintések

Lapmegtekintések telemetriai adatai egy eszköz vagy weblap alkalmazásban zajlik alapértelmezés szerint minden képernyőre vagy lapra betöltésekor. Azonban módosíthatja, amely nyomon követéséhez a lapmegtekintések további vagy különböző időpontokban. Például egy adott alkalmazásban, amely megjeleníti a lapok vagy panelek, érdemes nyomon követni egy oldal, amikor a felhasználó egy új panel nyílik meg.

Felhasználói és munkamenetadatok weblapok látogatottságát, valamint tulajdonságokként zajlik, így a felhasználók és munkamenetek diagramok életben származnak, ha nincs a lapmegtekintések telemetriai adatai.

### <a name="custom-page-views"></a>Egyéni lapmegtekintés

*JavaScript*

```javascript
appInsights.trackPageView("tab1");
```

*C#*

```csharp
telemetry.TrackPageView("GameReviewPage");
```

*Visual Basic*

```vb
telemetry.TrackPageView("GameReviewPage")
```

*Java*

```java
telemetry.trackPageView("GameReviewPage");
```

Ha több különböző HTML-lapok utasítások, túl adhatja meg az URL-cím:

```javascript
appInsights.trackPageView("tab1", "http://fabrikam.com/page1.htm");
```

### <a name="timing-page-views"></a>Időzítési lapmegtekintés

Alapértelmezés szerint az időpontokat jelentett **lapmegtekintés betöltési ideje** mérik a, amikor a böngésző elküldi a kérést, amíg a böngésző oldal betöltési esemény neve.

Ehelyett közül választhat:

* Egy explicit időtartam beállítása a [trackPageView](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md#trackpageview) hívása: `appInsights.trackPageView("tab1", null, null, null, durationInMilliseconds);`.
* Használja az oldal nézet hívások időzítését `startTrackPage` és `stopTrackPage`.

*JavaScript*

```javascript
// To start timing a page:
appInsights.startTrackPage("Page1");

...

// To stop timing and log the page:
appInsights.stopTrackPage("Page1", url, properties, measurements);
```

Az első paraméterként a nevet az Indítás és leállítás hívások társítja. A rendszer alapértelmezés szerint az aktuális oldal nevét.

Az eredményül kapott oldal jelenik meg a Metrikaböngészőben terhelés időtartamok származik az Indítás és leállítás hívások közötti időtartam. Arra, milyen időköz ténylegesen ideje.

### <a name="page-telemetry-in-analytics"></a>Az elemzési lap telemetriai

A [Analytics](analytics.md) két táblázat böngésző műveleti adatok megjelenítése:

* A `pageViews` táblázat az URL-cím és a lap címét adatait tartalmazza.
* A `browserTimings` táblázat ügyfél teljesítményét, például a bejövő adatok feldolgozásához szükséges idő adatait tartalmazza.

Megkeresése, mennyi időt vesz igénybe a böngésző feldolgozása különböző oldalakat:

```kusto
browserTimings
| summarize avg(networkDuration), avg(processingDuration), avg(totalDuration) by name
```

A különböző böngészők popularities felderítése:

```kusto
pageViews
| summarize count() by client_Browser
```

Az AJAX-hívások lapmegtekintés társítani, csatlakozik a függőségek:

```kusto
pageViews
| join (dependencies) on operation_Id 
```

## <a name="trackrequest"></a>TrackRequest

A kiszolgáló SDK TrackRequest használja a HTTP-kérések naplózására.

Akkor is is meghívhatja, ha szeretné szimulálni a kéréseket a környezetek ahol nem fut webszolgáltatás modul rendelkezik.

Azonban az ajánlott módszer a kérelem telemetriát küldjön, ahol működik-e a kérés egy <a href="#operation-context">műveletkörnyezetet</a>.

## <a name="operation-context"></a>Műveleti környezet

Telemetriai együtt kapcsolhatja össze műveleti környezet alkalmazásával. A normál kérés-követési modult azért teszi ezt a kivételeket és eseményeket, amelyek nem érkeznek, míg a HTTP-kérés feldolgozása folyamatban van. A [keresési](../../azure-monitor/app/diagnostic-search.md) és [Analytics](analytics.md), megtalálhatja minden olyan események, kapcsolódó a kérést a művelet azonosítója.

Lásd: [az Application Insights Telemetriai korreláció](../../azure-monitor/app/correlation.md) korrelációs további részleteiért.

Amikor telemetriai követési manuálisan, a legegyszerűbben úgy, hogy telemetriai korreláció ezt a mintát használja:

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

Egy műveleti környezet beállításával együtt `StartOperation` egy Ön által megadott típusú telemetriát elemet hoz létre. Elküldi a telemetriai elem, valamint lezárhatja azokat a műveletet, vagy ha explicit módon hívja `StopOperation`. Ha `RequestTelemetry` a telemetriai adatok típusa időtartamát az időközönkénti közötti indítás és leállítás van beállítva.

Egy művelet hatókörén belül jelentett telemetriai elem "gyermek" ilyen művelet válnak. A művelet környezetek sikerült ágyazható be.

A Keresés a műveleti környezet létrehozásához használt az **kapcsolódó elemek** lista:

![Kapcsolódó elemek](./media/api-custom-events-metrics/21.png)

Lásd: [Application Insights .NET SDK-val egyéni műveletek követése](../../azure-monitor/app/custom-operations-tracking.md) további információt az egyéni műveletek követése.

### <a name="requests-in-analytics"></a>Analytics kérelmek

A [Application Insights-elemzési](analytics.md), show kéri fel a a `requests` tábla.

Ha [mintavételi](../../application-insights/app-insights-sampling.md) van folyamatban, az elemek száma tulajdonság értékét fogja megjeleníteni egy 1-nél nagyobb. A példában az elemek száma a == 10 azt jelenti, hogy trackRequest() a 10 hívás a mintavételi folyamata csak továbbított adatok köre az egyiket. Kérelmek és a kérelmek név szerint szegmentált átlagos időtartamának megfelelő megszámlálása használja például a kódot:

```kusto
requests
| summarize count = sum(itemCount), avgduration = avg(duration) by name
```

## <a name="trackexception"></a>TrackException

Kivételek küldése az Application Insights:

* A [azok megszámlálása](../../application-insights/app-insights-metrics-explorer.md), mint arra utalhat, hogy a probléma gyakoriságát.
* A [vizsgálja meg az egyes események](../../azure-monitor/app/diagnostic-search.md).

A jelentések közé tartozik a hívásláncokat.

*C#*

```csharp
try
{
    ...
}
catch (Exception ex)
{
    telemetry.TrackException(ex);
}
```

*Java*

```java
try {
    ...
} catch (Exception ex) {
    telemetry.trackException(ex);
}
```

*JavaScript*

```javascript
try
{
    ...
}
catch (ex)
{
    appInsights.trackException(ex);
}
```

*Node.js*

```javascript
try
{
    ...
}
catch (ex)
{
    telemetry.trackException({exception: ex});
}
```

Az SDK-k számos-komponensek kivételeinek kezelése automatikusan, így mindig nincs explicit módon hívja a TrackException.

* ASP.NET: [-Komponensek kivételeinek kezelése kód írása](../../azure-monitor/app/asp-net-exceptions.md).
* J2EE-KISZOLGÁLÓKON: [Kivételek automatikusan észlelt](../../azure-monitor/app/java-get-started.md#exceptions-and-request-failures).
* JavaScript: Kivételek automatikusan észlelt. Ha szeretné tiltani az automatikus gyűjtését, adjon hozzá egy sort a weblapok beillesztett kódrészlet:

```javascript
({
    instrumentationKey: "your key",
    disableExceptionTracking: true
})
```

### <a name="exceptions-in-analytics"></a>Kivételek az Analyticsben

A [Application Insights-elemzési](analytics.md), kivételek jelennek meg az a `exceptions` tábla.

Ha [mintavételi](../../application-insights/app-insights-sampling.md) van folyamatban, a `itemCount` tulajdonság egy értéket 1-nél nagyobb mutatja. A példában az elemek száma a == 10 azt jelenti, hogy trackexception() hívásait a 10 hívás a mintavételi folyamata csak továbbított adatok köre az egyiket. Kivétel típusa alapján szegmentált kivételek megfelelő számát, amelyet kód például:

```kusto
exceptions
| summarize sum(itemCount) by type
```

A legtöbb fontos stack információ már kinyert külön változók, de egymástól kérheti le a `details` hatékonyabban struktúra. Mivel ez a struktúra dinamikus, az eredmény a várt típussal kell szavazattal. Példa:

```kusto
exceptions
| extend method2 = tostring(details[0].parsedStack[1].method)
```

Kivételek társítani a kapcsolódó kéréseket, használja a csatlakozzon:

```kusto
exceptions
| join (requests) on operation_Id
```

## <a name="tracktrace"></a>TrackTrace

TrackTrace a segítségével diagnosztizálhatja a problémákat azzal, hogy az Application Insights "útkövetés" küld. Adattömbök diagnosztikai adatokat küld, és vizsgálja meg azokat a [diagnosztikai keresés](../../azure-monitor/app/diagnostic-search.md).

A .NET-ben [adapterek jelentkezzen](../../azure-monitor/app/asp-net-trace-logs.md) külső naplók elküldése a portálra az API használatával.

A Java nyelven [Standard másolása, például Log4J, a Logback](../../azure-monitor/app/java-trace-logs.md) használja az Application Insights Log4j, vagy Logback Appenders külső naplók elküldése a portálra.

*C#*

```csharp
telemetry.TrackTrace(message, SeverityLevel.Warning, properties);
```

*Java*

```java
telemetry.trackTrace(message, SeverityLevel.Warning, properties);
```

*Node.js*

```javascript
telemetry.trackTrace({
    message: message,
    severity: applicationInsights.Contracts.SeverityLevel.Warning,
    properties: properties
});
```

Az üzenet tartalmát kereshet, de (ellentétben a tulajdonságértékek), nem lehet szűrni.

A méretkorlát a `message` jóval meghaladja a korlátot, a tulajdonságok.
TrackTrace előnye, hogy viszonylag hosszú adatok helyezheti az üzenetben. Hiba a POST data kódolása például is.  

Emellett is hozzáadhat egy súlyossági szintet az üzenetet. És egyéb telemetriát, például szűrő vagy más-más részhalmazához nyomkövetések keressen segítséget a tulajdonságértékek adhat hozzá. Példa:

*C#*

```csharp
var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
telemetry.TrackTrace("Slow database response",
                SeverityLevel.Warning,
                new Dictionary<string,string> { {"database", db.ID} });
```

*Java*

```java
Map<String, Integer> properties = new HashMap<>();
properties.put("Database", db.ID);
telemetry.trackTrace("Slow Database response", SeverityLevel.Warning, properties);
```

A [keresési](../../azure-monitor/app/diagnostic-search.md), majd könnyen kiszűrheti az összes üzenetet egy adott súlyossági szint, amely egy adott adatbázishoz kapcsolódik.

### <a name="traces-in-analytics"></a>Nyomok Analytics

A [Application Insights-elemzési](analytics.md), megjelennek TrackTrace hívásokat a `traces` tábla.

Ha [mintavételi](../../application-insights/app-insights-sampling.md) van folyamatban, az elemek száma tulajdonság egy értéket 1-nél nagyobb mutatja. A példában az elemek száma == 10-es azt jelenti, hogy a 10 hívás `trackTrace()`, mintavételi folyamata továbbított csak az egyiket. Megszerezni a nyomkövetési hívásokat megfelelő számát, használjon ezért kód például `traces | summarize sum(itemCount)`.

## <a name="trackdependency"></a>TrackDependency

A TrackDependency hívás segítségével nyomon követheti a válaszidők és a sikerességi arányokat kód külső kódnak küldött hívások. Az eredmények a portál függőségi diagramjain jelennek meg.

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

```java
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

```javascript
var success = false;
var startTime = new Date().getTime();
try
{
    success = dependency.Call();
}
finally
{
    var elapsed = new Date() - startTime;
    telemetry.trackDependency({
        dependencyTypeName: "myDependency",
        name: "myCall",
        duration: elapsed,
        success: success
    });
}
```

Ne feledje, hogy tartalmazza-e a kiszolgáló SDK-k egy [függőségi modul](../../azure-monitor/app/asp-net-dependencies.md) , amely felderíti és nyomon követi az egyes függőségi hívások automatikusan – például adatbázisok és a REST API-k. Rendelkezik ügynök telepítéséhez a kiszolgálón, hogy a modul működik. 

A Java, az egyes függőségi hívások automatikusan követhető használatával [Java ügynököt](../../azure-monitor/app/java-agent.md).

Ha szeretné nyomon követni a automatizált követési nem tényleges hívások, vagy ha nem szeretné telepíteni az ügynököt a hívás használhatja.

A normál függőség-követési modult a C# kikapcsolásához szerkesztése [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md) és mutató hivatkozás törlése `DependencyCollector.DependencyTrackingTelemetryModule`. Java-környezetben nem telepítse a java ügynököt Ha nem szeretné automatikusan gyűjteni standard függőségek.

### <a name="dependencies-in-analytics"></a>Függőségek Analytics

A [Application Insights-elemzési](analytics.md), trackDependency hívások show a `dependencies` tábla.

Ha [mintavételi](../../application-insights/app-insights-sampling.md) van folyamatban, az elemek száma tulajdonság egy értéket 1-nél nagyobb mutatja. A példában az elemek száma a == 10 azt jelenti, hogy trackDependency() a 10 hívás a mintavételi folyamata csak továbbított adatok köre az egyiket. A célként megadott összetevő által szegmentált függőségek megfelelő számát, amelyet kód például:

```kusto
dependencies
| summarize sum(itemCount) by target
```

Függőségek társítani a kapcsolódó kéréseket, használja a csatlakozzon:

```kusto
dependencies
| join (requests) on operation_Id
```

## <a name="flushing-data"></a>Könyvelési adatok

Normális esetben az SDK elküldi az adatokat esetenként választotta, a felhasználó gyakorolt hatás minimalizálása érdekében. Azonban bizonyos esetekben előfordulhat, hogy szeretné kiüríteni a puffer – például ha az SDK-t használja egy alkalmazásban, hogy leállítja.

*C#*

 ```csharp
telemetry.Flush();
// Allow some time for flushing before shutdown.
System.Threading.Thread.Sleep(5000);
```

*Java*

```java
telemetry.flush();
//Allow some time for flushing before shutting down
Thread.sleep(5000);
```

*Node.js*

```javascript
telemetry.flush();
```

Vegye figyelembe, hogy a függvény az aszinkron a [kiszolgáló telemetriai csatorna](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel/).

Ideális esetben az alkalmazás leállítása tevékenység flush() metódus használandó.

## <a name="authenticated-users"></a>Hitelesített felhasználók

A web app alkalmazásban felhasználók (alapértelmezés szerint) azonosítja a cookie-k. A felhasználó számítanak előfordulhat, hogy egynél többször férnek hozzá az alkalmazás egy másik gépen vagy a böngészőben, vagy ha ezek a cookie-k törléséhez.

Ha a felhasználók bejelentkeznek az alkalmazásba, a hitelesített felhasználó azonosítója a böngésző-kód beállításával kaphat egy pontosabb száma:

*JavaScript*

```javascript
// Called when my app has identified the user.
function Authenticated(signInId) {
    var validatedId = signInId.replace(/[,;=| ]+/g, "_");
    appInsights.setAuthenticatedUserContext(validatedId);
    ...
}
```

Az ASP.NET-webalkalmazás MVC-alkalmazás, például:

*Razor*

```cshtml
@if (Request.IsAuthenticated)
{
    <script>
        appInsights.setAuthenticatedUserContext("@User.Identity.Name
            .Replace("\\", "\\\\")"
            .replace(/[,;=| ]+/g, "_"));
    </script>
}
```

Nem a tényleges bejelentkezési felhasználónév használata szükséges. Csak azt nem lehet, hogy a felhasználó egyedi Azonosítóját. Nem tartalmazhat szóközt, és a karakterek egyikét sem `,;=|`.

A felhasználói azonosító van is egy munkamenet-cookie-ban és a kiszolgálónak küldött. Ha a kiszolgáló SDK telepítve van, a hitelesített felhasználó azonosítója küldött ügyfél- és telemetria környezeti tulajdonsága részeként. Majd szűrheti, és keressen rá.

Ha az alkalmazás felhasználók csoportosítja a fiókok, is átadhat a fiók (az azonos karakter korlátozások) azonosítója.

```javascript
appInsights.setAuthenticatedUserContext(validatedId, accountId);
```

A [Metrikaböngésző](../../application-insights/app-insights-metrics-explorer.md), létrehozhat egy diagram, amely counts **, felhasználók hitelesítése**, és **felhasználói fiókok**.

Emellett [keresési](../../azure-monitor/app/diagnostic-search.md) ügyfél adatpontokhoz az adott felhasználói neveket és fiókok.

## <a name="properties"></a>Szűrést, a Keresés és az adatok példájaként tulajdonságok felhasználásával

Is csatlakoztathat tulajdonságait és a mértékek az események (, valamint is a metrikákhoz, nézetek, kivételeket és egyéb telemetriai adatokat oldalon).

*Tulajdonságok* olyan karakterlánc-értékek, amelyek segítségével szűrheti a telemetria a használati jelentésekben. Például ha az alkalmazás biztosít több játékok, csatlakoztathat a játék neve minden egyes esemény, hogy melyik játékok további népszerű látható.

A karakterlánc hosszának a 8192 korlátozva van. (Ha szeretne küldeni az adatok nagy méretű adattömböket, használja az üzenet paraméterében [TrackTrace](#track-trace).)

*Metrikák* numerikus érték, amely jelenítheti meg grafikusan. Érdemes például, hogy van-e a pontszámok, amely a játékélményt nyújthat érhető el a fokozatos növekedését. A gráfok lesznek elküldve az esemény tulajdonságainak szegmentálható, annak érdekében, hogy is külön vagy a halmozott diagramok különböző játékok.

A metrikaértékek megfelelően megjeleníteni kívánt kell nagyobb, mint 0 vagy nagyobb.

Van azonban néhány [tulajdonságai, a tulajdonságértékeket és a metrikák vonatkozó korlátok](#limits) használható.

*JavaScript*

```javascript
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
```

*C#*

```csharp
// Set up some properties and metrics:
var properties = new Dictionary <string, string>
    {{"game", currentGame.Name}, {"difficulty", currentGame.Difficulty}};
var metrics = new Dictionary <string, double>
    {{"Score", currentGame.Score}, {"Opponents", currentGame.OpponentCount}};

// Send the event:
telemetry.TrackEvent("WinGame", properties, metrics);
```

*Node.js*

```javascript
// Set up some properties and metrics:
var properties = {"game": currentGame.Name, "difficulty": currentGame.Difficulty};
var metrics = {"Score": currentGame.Score, "Opponents": currentGame.OpponentCount};

// Send the event:
telemetry.trackEvent({name: "WinGame", properties: properties, measurements: metrics});
```

*Visual Basic*

```vb
' Set up some properties:
Dim properties = New Dictionary (Of String, String)
properties.Add("game", currentGame.Name)
properties.Add("difficulty", currentGame.Difficulty)

Dim metrics = New Dictionary (Of String, Double)
metrics.Add("Score", currentGame.Score)
metrics.Add("Opponents", currentGame.OpponentCount)

' Send the event:
telemetry.TrackEvent("WinGame", properties, metrics)
```

*Java*

```java
Map<String, String> properties = new HashMap<String, String>();
properties.put("game", currentGame.getName());
properties.put("difficulty", currentGame.getDifficulty());

Map<String, Double> metrics = new HashMap<String, Double>();
metrics.put("Score", currentGame.getScore());
metrics.put("Opponents", currentGame.getOpponentCount());

telemetry.trackEvent("WinGame", properties, metrics);
```

> [!NOTE]
> Ügyeljen, nem arra, hogy személyes azonosításra alkalmas adatokat jelentkezzen be a tulajdonságokat.
>
>

### <a name="alternative-way-to-set-properties-and-metrics"></a>Állítsa be a tulajdonságokat és a metrikák történő megadásának alternatív módja

Ha kényelmesebb, begyűjtheti a paramétereket egy külön objektumban esemény:

```csharp
var event = new EventTelemetry();

event.Name = "WinGame";
event.Metrics["processingTime"] = stopwatch.Elapsed.TotalMilliseconds;
event.Properties["game"] = currentGame.Name;
event.Properties["difficulty"] = currentGame.Difficulty;
event.Metrics["Score"] = currentGame.Score;
event.Metrics["Opponents"] = currentGame.Opponents.Length;

telemetry.TrackEvent(event);
```

> [!WARNING]
> Ne használja újra ugyanazon telemetriai elem (`event` ebben a példában) Track*() hívni többször is feldolgozza. Emiatt előfordulhat, hogy a telemetria küldésének helytelen konfiguráció.
>
>

### <a name="custom-measurements-and-properties-in-analytics"></a>Egyéni mérések és az Analytics tulajdonságok

A [Analytics](analytics.md), egyéni metrikákkal és tulajdonságok megjelenítése a `customMeasurements` és `customDimensions` telemetriai rekordokban attribútumai.

Például hozzáadta a kérelmek telemetriai adatai "játék" nevű tulajdonságot, ha ez a lekérdezés a különböző értékek "játék" előfordulását számolja, és az egyéni metrika "pontszám" átlagát megjelenítése:

```kusto
requests
| summarize sum(itemCount), avg(todouble(customMeasurements.score)) by tostring(customDimensions.game)
```

Figyelje meg, hogy:

* Egy érték kinyerése a customDimensions vagy customMeasurements JSON, ha dinamikus típusa van, és úgy kell alakítania azt `tostring` vagy `todouble`.
* Annak a lehetőségét figyelembe [mintavételi](../../application-insights/app-insights-sampling.md), használjon `sum(itemCount)`, nem `count()`.

## <a name="timed"></a> Időzítési események

Néha érdemes diagram, mennyi ideig tart egy műveletet. Például érdemes tudni, hogy mennyi ideig felhasználók lehetőségek a játék figyelembe vesz igénybe. Ennek a mérési paraméter is használhatja.

*C#*

```csharp
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

```java
long startTime = System.currentTimeMillis();

// Perform timed action

long endTime = System.currentTimeMillis();
Map<String, Double> metrics = new HashMap<>();
metrics.put("ProcessingTime", endTime-startTime);

// Setup some properties
Map<String, String> properties = new HashMap<>();
properties.put("signalSource", currentSignalSource.getName());

// Send the event
telemetry.trackEvent("SignalProcessed", properties, metrics);
```

## <a name="defaults"></a>Egyéni telemetriai alapértelmezett tulajdonságai

Szeretne beállított alapértelmezett tulajdonságértékeket néhány egyéni eseményt írt, teheti meg egy TelemetryClient-példányban. Minden, hogy az ügyfél által küldött telemetriai elem vannak csatolva.

*C#*

```csharp
using Microsoft.ApplicationInsights.DataContracts;

var gameTelemetry = new TelemetryClient();
gameTelemetry.Context.Properties["Game"] = currentGame.Name;
// Now all telemetry will automatically be sent with the context property:
gameTelemetry.TrackEvent("WinGame");
```

*Visual Basic*

```vb
Dim gameTelemetry = New TelemetryClient()
gameTelemetry.Context.Properties("Game") = currentGame.Name
' Now all telemetry will automatically be sent with the context property:
gameTelemetry.TrackEvent("WinGame")
```

*Java*

```java
import com.microsoft.applicationinsights.TelemetryClient;
import com.microsoft.applicationinsights.TelemetryContext;
...


TelemetryClient gameTelemetry = new TelemetryClient();
TelemetryContext context = gameTelemetry.getContext();
context.getProperties().put("Game", currentGame.Name);

gameTelemetry.TrackEvent("WinGame");
```

*Node.js*

```javascript
var gameTelemetry = new applicationInsights.TelemetryClient();
gameTelemetry.commonProperties["Game"] = currentGame.Name;

gameTelemetry.TrackEvent({name: "WinGame"});
```

Egyéni telemetriát hívások felülbírálhatja az alapértelmezett értékeket a saját tulajdonság szótárak.

*JavaScript a webes ügyfél*, [használata JavaScript telemetriai inicializálók](#js-initializer).

*Tulajdonságok hozzáadása összes telemetriai adat*, beleértve a szabványos adatgyűjtő modulok, adatait [megvalósítása `ITelemetryInitializer` ](../../azure-monitor/app/api-filtering-sampling.md#add-properties).

## <a name="sampling-filtering-and-processing-telemetry"></a>Mintavétel, a szűrést és a telemetriai adatok feldolgozása

Írhat kódot a telemetriai adatok feldolgozásához, az SDK-val való továbbítás előtt. A feldolgozás a normál telemetriai modulok, például a HTTP kérés és a függőségi gyűjtemény által küldött adatokat tartalmaz.

[Adja hozzá tulajdonságokat](../../azure-monitor/app/api-filtering-sampling.md#add-properties) alkalmazásával a telemetriai `ITelemetryInitializer`. Például hozzáadhat vagy verziószámokat, amelyek kiszámítása más tulajdonságok alapján.

[Szűrés](../../azure-monitor/app/api-filtering-sampling.md#filtering) módosíthatja vagy elveti a telemetriai adatokat, az SDK-ból az életbe léptetésével elküldése előtt `ITelemetryProcesor`. Meghatározhatja, mi küldött vagy elvetett, de a hatása a metrikák számolnia. Attól függően, hogyan elemek elveti elveszítheti a kapcsolódó elemek között lehetővé teszi.

[Mintavételi](../../azure-monitor/app/api-filtering-sampling.md) egy csomagolt megoldás a portál az alkalmazásából származó küldött adatmennyiség csökkentése érdekében. Így a megjelenített mérőszámok befolyásolása nélkül hajtja végre. És azt úgy valósítja meg a problémák diagnosztizálása a kapcsolódó elemeket, például a kivételeket, a kérelmektől és a lapmegtekintés között navigálva képessége befolyásolása nélkül.

[További információk](../../azure-monitor/app/api-filtering-sampling.md).

## <a name="disabling-telemetry"></a>Telemetria letiltása

A *dinamikusan leállítására és elindítására* a gyűjtemény és a telemetriai adatok továbbítása:

*C#*

```csharp
using  Microsoft.ApplicationInsights.Extensibility;

TelemetryConfiguration.Active.DisableTelemetry = true;
```

*Java*

```java
telemetry.getConfiguration().setTrackingDisabled(true);
```

A *tiltsa le a kiválasztott standard naplógyűjtők*– például teljesítményszámlálók, a HTTP-kérések vagy a függőségek--törlése vagy tegye megjegyzésbe a megfelelő a [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md). Ehhez, például, ha azt szeretné, a saját TrackRequest adatküldéshez.

*Node.js*

```javascript
telemetry.config.disableAppInsights = true;
```

A *tiltsa le a kiválasztott standard naplógyűjtők*– például teljesítményszámlálók, a HTTP-kérések vagy a függőségek--inicializálási időpontban láncolt SDK inicializálási kódot való konfigurálásának módját:

```javascript
applicationInsights.setup()
    .setAutoCollectRequests(false)
    .setAutoCollectPerformance(false)
    .setAutoCollectExceptions(false)
    .setAutoCollectDependencies(false)
    .setAutoCollectConsole(false)
    .start();
```

Tiltsa le ezeket a gyűjtőket inicializálása után, a konfigurációs objektum használja: `applicationInsights.Configuration.setAutoCollectRequests(false)`

## <a name="debug"></a>Fejlesztői mód

Hibakeresés során hasznos kell jelenniük az, hogy az eredmények azonnal láthatók a telemetria. Akkor is get további üzeneteket, amelyek segítségével nyomon követheti a problémával, a telemetriai adatokat. Kapcsolja éles környezetben, mivel az alkalmazás lassíthatja.

*C#*

```csharp
TelemetryConfiguration.Active.TelemetryChannel.DeveloperMode = true;
```

*Visual Basic*

```vb
TelemetryConfiguration.Active.TelemetryChannel.DeveloperMode = True
```

## <a name="ikey"></a> A kiválasztott egyéni telemetriához kialakítási kulcs beállítása

*C#*

```csharp
var telemetry = new TelemetryClient();
telemetry.InstrumentationKey = "---my key---";
// ...
```

## <a name="dynamic-ikey"></a> A dinamikus kialakítási kulcs

A fejlesztési, tesztelési és éles környezetek származó telemetriai adatok mentése keverése elkerüléséhez is [külön Application Insights-erőforrások létrehozása](../../application-insights/app-insights-create-new-resource.md) és azok kulcsait a környezettől függően módosítsa.

Helyett a kialakítási kulcs lekérése a konfigurációs fájlban, beállíthatja a kódban. A kulcsot meg például az ASP.NET-szolgáltatások Global.aspx.cs osztályból, egy inicializálási metódust:

*C#*

```csharp
protected void Application_Start()
{
    Microsoft.ApplicationInsights.Extensibility.
    TelemetryConfiguration.Active.InstrumentationKey =
        // - for example -
        WebConfigurationManager.Settings["ikey"];
    ...
}
```

*JavaScript*

```javascript
appInsights.config.instrumentationKey = myKey;
```

A weboldalakon érdemes állítható be azt a webalkalmazás-kiszolgáló állapota, nem pedig kódolási szó szerint a parancsfájlba. Ha például az egy weblap ASP.NET-alkalmazás létrehozva:

*A Razor JavaScript*

```cshtml
<script type="text/javascript">
// Standard Application Insights webpage script:
var appInsights = window.appInsights || function(config){ ...
// Modify this part:
}({instrumentationKey:  
    // Generate from server property:
    @Microsoft.ApplicationInsights.Extensibility.
        TelemetryConfiguration.Active.InstrumentationKey;
}) // ...
```

## <a name="telemetrycontext"></a>TelemetryContext

TelemetryClient rendelkezik egyik környezeti tulajdonsága, amely küldött telemetriai adatokkal együtt értékeket tartalmaz. Általában a normál telemetriai modulok által vannak beállítva, de is állíthatja őket saját maga. Példa:

```csharp
telemetry.Context.Operation.Name = "MyOperationName";
```

Ha megadta ezeket az értékeket saját magának, érdemes eltávolítani a megfelelő sort a [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md), hogy az értékek és a standard szintű értékek nem Zavarba.

* **Összetevő**: Az alkalmazás és annak verzióját.
* **Eszköz**: Adatok az eszközről, ahol az alkalmazás fut-e. (A web apps szolgáltatásban, ez az a kiszolgáló vagy ügyfél eszköz által küldött telemetriát.)
* **InstrumentationKey**: Az Application Insights-erőforrást az Azure-ban hol jelenjenek meg a telemetriát. Ez általában mértékének az applicationinsights.config fájlban.
* **Hely**: Az eszköz földrajzi helye.
* **A művelet**: A web apps, a jelenlegi HTTP-kérelem. A más típusú alkalmazások beállíthatja ezt események együtt.
  * **ID**: Egy generált érték, amely a különböző események, így minden esetben a diagnosztikai keresés nézze meg, amikor kapcsolódó elemek találhatók.
  * **Név**: Egy azonosító általában az URL-címét a HTTP-kérelem.
  * **SyntheticSource**: Ha nem null értékű vagy üres, a karakterlánc, amely azt jelzi, hogy a forrás, a kérelem egy robot vagy a webes teszt során megállapítottuk. Alapértelmezés szerint az ki lesz zárva a számítások a Metrikaböngészőben.
* **Tulajdonságok**: Az összes telemetriai adatokat küldött tulajdonságai. Az egyes hívások nyomon követése * felülbírálható.
* **Munkamenet**: A felhasználói munkamenetet. Az azonosító értéke egy generált érték, amely megváltozik, amikor a felhasználó nem volt aktív egy ideig.
* **Felhasználói**: Felhasználói adatok.

## <a name="limits"></a>Korlátok

[!INCLUDE [application-insights-limits](../../../includes/application-insights-limits.md)]

A sávszélesség-korlátjának elkerülje, használja a [mintavételi](../../application-insights/app-insights-sampling.md).

Mennyi ideig meghatározásához adatért, lásd: [adatok megőrzésére és az adatvédelmi](../../azure-monitor/app/data-retention-privacy.md).

## <a name="reference-docs"></a>Segédanyagok

* [ASP.NET-referencia](https://msdn.microsoft.com/library/dn817570.aspx)
* [Java-referencia](http://dl.windowsazure.com/applicationinsights/javadoc/)
* [JavaScript-referencia](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md)
* [Android SDK](https://github.com/Microsoft/ApplicationInsights-Android)
* [iOS SDK](https://github.com/Microsoft/ApplicationInsights-iOS)

## <a name="sdk-code"></a>SDK-kód

* [ASP.NET Core SDK](https://github.com/Microsoft/ApplicationInsights-aspnetcore)
* [ASP.NET 5](https://github.com/Microsoft/ApplicationInsights-dotnet)
* [A Windows Server-csomagok](https://github.com/Microsoft/applicationInsights-dotnet-server)
* [Java SDK](https://github.com/Microsoft/ApplicationInsights-Java)
* [Node.js SDK](https://github.com/Microsoft/ApplicationInsights-Node.js)
* [JavaScript SDK](https://github.com/Microsoft/ApplicationInsights-JS)
* [Összes platform](https://github.com/Microsoft?utf8=%E2%9C%93&query=applicationInsights)

## <a name="questions"></a>Kérdések

* *Milyen kivételek előfordulhat, hogy throw Track_() hívások?*

    Nincs. Nem kell őket try-catch záradékok wrap funkciót. Ha az SDK problémákat észlel, üzenetek naplózza a hibakeresési konzol kimeneteként és – ha az üzeneteket kaphat – a diagnosztikai keresésben.
* *Van egy REST API-t az adatok beszerzése a portálról?*

    Igen, a [adathozzáférési API](https://dev.applicationinsights.io/). Más módokon is gyűjthet ki adatokat tartalmaznak [Analytics exportálhat a Power bi-bA](../../application-insights/app-insights-export-power-bi.md) és [a folyamatos exportálás](../../azure-monitor/app/export-telemetry.md).

## <a name="next"></a>Következő lépések

* [Keresési események és naplók](../../azure-monitor/app/diagnostic-search.md)
* [hibaelhárítással](../../application-insights/app-insights-troubleshoot-faq.md)
