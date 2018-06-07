---
title: Élő metrikák adatfolyamot egyéni metrikákkal és diagnosztika a Azure Application Insights |} Microsoft Docs
description: A webalkalmazás egyéni metrikák valós idejű figyelése és a hibák, a nyomkövetések és az események élő adatcsatornára eseményadatokat.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 1f471176-38f3-40b3-bc6d-3f47d0cbaaa2
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 05/24/2018
ms.author: mbullwin; Soubhagya.Dash
ms.openlocfilehash: 352fff53d9e35ddd8d8e0c107e969357d9c766b3
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34599235"
---
# <a name="live-metrics-stream-monitor--diagnose-with-1-second-latency"></a>Metrikák adatfolyamot: Figyelő & Diagnosztizálás és 1 másodperc késleltetés

Az élő, éles a webes alkalmazás ki szívveréseket lelke mintavételi származó élő metrikák adatfolyam használatával [Application Insights](app-insights-overview.md). Válassza ki, és valós időben, a szolgáltatás zavarok nélkül bemutató metrikák és számlálóinak szűréséhez. Vizsgálja meg a minta sikertelen kérelmek és kivételek híváslánc megjelenik. Együtt [Profilkészítő](app-insights-profiler.md), [pillanatkép hibakereső](app-insights-snapshot-debugger.md), és [Teljesítménytesztelés](app-insights-monitor-web-app-availability.md#performance-tests), metrikák adatfolyamot egy hatékony és nem zavarja a munkában diagnosztikai eszköz biztosít a élő webhelyet.

Az élő metrikák adatfolyam-továbbítás segítségével:

* Egy javítást érvényesítése közben megjelent, amelyet figyeli a teljesítmény és a hiba számát.
* Hatásának tesztelése terhelések, és a problémák diagnosztizálásához tekintse meg élő. 
* Adott teszt munkamenetek összpontosítson, vagy kiválasztásával, és meg szeretné nézni a metrikák szűrés szűrheti ismert problémákkal kapcsolatban.
* Első kivétel nyomkövetési adatokat, akkor fordulhat elő.
* A legfontosabb KPI-k található szűrők kísérletezhet.
* Bármely Windows-teljesítmény számláló élő figyelése.
* A kiszolgáló, amelynek problémákat, és minden a KPI/live-hírcsatornát, hogy csak az adott kiszolgálón szűrő könnyebb azonosításához.

[![Élő adatfolyam metrikák videó](./media/app-insights-live-stream/youtube.png)](https://www.youtube.com/watch?v=zqfHf1Oi5PY)

## <a name="get-started"></a>Bevezetés

1. Ha még nem [telepítve az Application Insights](app-insights-asp-net.md) az ASP.NET web app alkalmazásban vagy [Windows server alkalmazás](app-insights-windows-services.md), most tegye. 
2. **A legújabb verzióra frissítés** az Application Insights-csomag. A Visual Studióban, kattintson jobb gombbal a projektre, és válassza a **kezelése Nuget-csomagok**. Nyissa meg a **frissítések** lapon jelölje **közé tartozik a prerelease**, és válassza ki a Microsoft.ApplicationInsights.* csomagokat.

    Helyezze ismét üzembe alkalmazását.

3. Az a [Azure-portálon](https://portal.azure.com), nyissa meg az Application Insights-erőforrást az alkalmazáshoz, és nyissa meg az élő adatfolyam.

4. [A vezérlő csatornát](#secure-the-control-channel) ha bizalmas adatok, például ügyfélnevek használhatja a szűrők.


![Az Áttekintés paneljén kattintson az élő adatfolyam](./media/app-insights-live-stream/live-stream-2.png)

### <a name="no-data-check-your-server-firewall"></a>Nincs adat? Ellenőrizze a kiszolgáló tűzfal

Ellenőrizze a [kimenő portok a metrikák adatfolyamot](app-insights-ip-addresses.md#outgoing-ports) nyitva a tűzfalon a kiszolgálók. 


## <a name="how-does-live-metrics-stream-differ-from-metrics-explorer-and-analytics"></a>Eltérések a Metrikaböngésző és az elemzések adatfolyamot metrikák?

| |Élő stream | Metrikaböngésző és elemzés |
|---|---|---|
|Késés|Egy második belül megjelenített adatok|Perc alatt összesített értéket|
|Visszatartás nem|Adatok továbbra is fennáll, amíg ez a diagram, és utána eldobja|[Az adatok 90 napig őrzi meg](app-insights-data-retention-privacy.md#how-long-is-the-data-kept)|
|Igény szerinti|Amíg nyissa meg a metrikák élő adatok továbbítja adatfolyamként|Az adatokat küldi el, amikor az SDK telepítése és engedélyezése|
|Ingyenes|Az élő adatfolyam adatok ingyenesek|Közölt [díjszabása](app-insights-pricing.md)
|Mintavételezés|Az összes kijelölt metrikák és számlálók továbbít. Hibák és híváslánc megjelenik mintát. TelemetryProcessors nem érvényesek.|Lehet, hogy események [mintát](app-insights-api-filtering-sampling.md)|
|Vezérlőcsatorna|Az SDK jelek küldött szűrővezérlés. Javasoljuk, hogy [a csatornát](#secure-channel).|Kommunikációs még csak egyirányú, a portálra|


## <a name="select-and-filter-your-metrics"></a>Válassza ki, és a metrikák szűrése

(A klasszikus ASP.NET-alkalmazásokra a legújabb SDK-val elérhető.)

Egyéni KPI élő tetszőleges szűrők alkalmazásával bármely Application Insights telemetria a portálról figyelheti. Kattintson a szűrő vezérlő bemutatja, amikor Ön rámutatáskor a diagramok bármelyikét. A következő diagram van egy egyéni kérelem száma KPI URL-cím és időtartama attribútumok szűrőkkel ábrázolásához. A szűrők a adatfolyam Preview szakasz bemutatja, a feltételeknek megfelelő időben megadott bármikor telemetriai adatot élő hírcsatorna érvényesítése. 

![Egyéni kérelem KPI](./media/app-insights-live-stream/live-stream-filteredMetric.png)

Figyelheti a roleservice száma. A beállítások függ az adatfolyam, amely lehet bármely Application Insights telemetria: kérelmek, függőségek, kivételek, nyomkövetések, eseményeket és metrikákat. Azok a saját [egyéni mérési](app-insights-api-custom-events-metrics.md#properties):

![Érték-beállítások](./media/app-insights-live-stream/live-stream-valueoptions.png)

Az Application Insights telemetria mellett is figyelheti bármely Windows teljesítményszámláló jelölje ki, hogy az adatfolyam lehetőségek közül, és a teljesítményszámláló nevét.

Élő metrikák összesítése két időpontokban: helyileg az egyes kiszolgálókon, majd a összes kiszolgáló között. Módosíthatja az alapértelmezett bármelyik más beállítások kiválasztja a megfelelő legördülő listákat.

## <a name="sample-telemetry-custom-live-diagnostic-events"></a>A minta Telemetriai: Egyéni élő diagnosztikai eseményei
Alapértelmezés szerint az élő események jelennek meg a sikertelen kérelmek és függőségi hívások esetében, kivételek, eseményeket, valamint nyomkövetési adatokat. Kattintson a ellenőrizheti a alkalmazott bármikor időben. 

![Alapértelmezett működés közbeni adatcsatorna](./media/app-insights-live-stream/live-stream-eventsdefault.png)

Mint a metrikákat, feltételeket is megadhat bármilyen tetszőleges bármelyik az Application Insights telemetria típusú. Ebben a példában azt kiválasztása adott kérelem sikertelen, a nyomkövetések és az események. Azt is kiválasztásával, kivételeket és a függőségi hibák.

![Egyéni élő adatcsatorna](./media/app-insights-live-stream/live-stream-events.png)

Megjegyzés: Jelenleg kivétel üzenetalapú feltétel, a legkülső kivétel üzenetét használja. Az előző példában, amellyel szűrheti a belső kivétel üzenet a jóindulatú kivétel (követi a "<--" elválasztó) "az ügyfél kapcsolata megszakadt." Használjon egy üzenet nem-"Hibaüzenet kérés tartalma" feltételeket tartalmaz.

Ehhez kattintson rá az élő adatcsatornában elem részleteinek megtekintéséhez. Akár szüneteltetheti is, az adatcsatorna vagy kattintson a **szüneteltetése** vagy egyszerűen a lefelé görgetéshez használható, vagy elemet. Élő adatcsatorna után a lap tetejére, vagy kattintson a cikkek szüneteltetése során gyűjtött számláló görgessen fog folytatódni.

![Élő hibák mintát](./media/app-insights-live-stream/live-metrics-eventdetail.png)

## <a name="filter-by-server-instance"></a>Szűrés a server-példány

Ha egy adott server-példányon figyelje, kiszolgáló szerint szűrheti.

![Élő hibák mintát](./media/app-insights-live-stream/live-stream-filter.png)

## <a name="sdk-requirements"></a>SDK-követelmények
Egyéni élő metrikák adatfolyama verzió 2.4.0-beta2 érhető el vagy az újabb [Web Application Insights SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/). Ne felejtse el a NuGet-Csomagkezelő "Előzetes kiadásain tartalmazza" beállítást.

## <a name="secure-the-control-channel"></a>A vezérlő csatornát
A megadott egyéni szűrők feltételek rendszer küldi vissza az élő metrikákat az Application Insights SDK összetevőt. A szűrők tartalmazhat customerIDs például bizalmas adatokat. Hogy a csatorna biztonságos mellett a instrumentation kulcs titkos API-kulcs.
### <a name="create-an-api-key"></a>API-kulcs létrehozása

![Api-kulcs létrehozása](./media/app-insights-live-stream/live-metrics-apikeycreate.png)

### <a name="add-api-key-to-configuration"></a>A konfigurációs API-kulcs hozzáadása

### <a name="classic-aspnet"></a>Klasszikus ASP.NET

Az applicationinsights.config fájlban vegye fel a AuthenticationApiKey a QuickPulseTelemetryModule:
``` XML

<Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse.QuickPulseTelemetryModule, Microsoft.AI.PerfCounterCollector">
      <AuthenticationApiKey>YOUR-API-KEY-HERE</AuthenticationApiKey>
</Add>

```
Vagy a kódban, állítsa be a QuickPulseTelemetryModule meg:

```csharp
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse;
using Microsoft.ApplicationInsights.Extensibility;

             TelemetryConfiguration configuration = new TelemetryConfiguration();
            configuration.InstrumentationKey = "YOUR-IKEY-HERE";

            QuickPulseTelemetryProcessor processor = null;

            configuration.TelemetryProcessorChainBuilder
                .Use((next) =>
                {
                    processor = new QuickPulseTelemetryProcessor(next);
                    return processor;
                })
                        .Build();

            var QuickPulse = new QuickPulseTelemetryModule()
            {

                AuthenticationApiKey = "YOUR-API-KEY"
            };
            QuickPulse.Initialize(configuration);
            QuickPulse.RegisterTelemetryProcessor(processor);
            foreach (var telemetryProcessor in configuration.TelemetryProcessors)
                {
                if (telemetryProcessor is ITelemetryModule telemetryModule)
                    {
                    telemetryModule.Initialize(configuration);
                    }
                }

```

### <a name="aspnet-core-requires-application-insights-aspnet-core-sdk-230-beta-or-greater"></a>Az ASP.NET Core (Application Insights az ASP.NET Core SDK szükséges 2.3.0-beta vagy újabb)

Az alábbiak szerint módosítsa a startup.cs fájlt:

Először adjon hozzá

``` C#
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse;
```

Majd belül a ConfigureServices metódus hozzáadása:

``` C#
services.ConfigureTelemetryModule<QuickPulseTelemetryModule>( module => module.AuthenticationApiKey = "YOUR-API-KEY-HERE");
```


Ha ismeri fel, és a csatlakoztatott kiszolgálókra megbízható, megpróbálhatja az egyéni szűrők a hitelesített csatornát nélkül. Ez a beállítás hat hónapig érhető el. Ez a felülbírálás kell egyszer minden új munkamenet, vagy ha új kiszolgáló online elérhető lesz.

![Élő metrikák hitelesítési beállítások](./media/app-insights-live-stream/live-stream-auth.png)

>[!NOTE]
>Határozottan javasoljuk, hogy beállította a hitelesített csatornát a szűrési feltételeket a potenciálisan bizalmas adatok, például a CustomerID megadása előtt.
>

## <a name="generating-a-performance-test-load"></a>Egy teszt víruskeresőké létrehozása

Ha meg szeretné nézni a hatását, hogy a terhelés növelését, használja a vizsgálat panelen. Egyidejű felhasználók száma kérelmeinek szimulálja azt. Vagy "manuális tesztek" Futtatás (ping-vizsgálatok) egyetlen URL-címet, vagy futtatható egy [többlépéses webteszt teljesítmény](app-insights-monitor-web-app-availability.md#multi-step-web-tests) feltöltött (a ugyanúgy, mint egy elérhetőségi teszt).

> [!TIP]
> Miután létrehozta a vizsgálat, nyissa meg a teszt- és az élő adatfolyam panel külön windows. A várólistára helyezett vizsgálat indításakor, és tekintse meg élő adatfolyam tekintheti meg egyszerre.
>


## <a name="troubleshooting"></a>Hibaelhárítás

Nincs adat? Ha az alkalmazás egy védett hálózati: metrikák élő adatfolyam használ a különböző IP-címeket, mint más Application Insights telemetria. Győződjön meg arról, hogy [adott IP-címek](app-insights-ip-addresses.md) nyitva a tűzfalon.



## <a name="next-steps"></a>További lépések
* [Az Application insights szolgáltatással megfigyelési kihasználtsága](app-insights-web-track-usage.md)
* [Diagnosztikai keresés](app-insights-diagnostic-search.md)
* [Profilkészítő](app-insights-profiler.md)
* [Pillanatkép hibakereső](app-insights-snapshot-debugger.md)
