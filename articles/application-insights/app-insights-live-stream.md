---
title: Élő metrikák Stream egyéni metrikákkal és a diagnosztika az Azure Application Insights |} A Microsoft Docs
description: Egyéni metrikák valós időben a webalkalmazás figyelése, és diagnosztizálhatja a problémákat a hibák, nyomkövetéseket és események élő hírcsatornában.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 1f471176-38f3-40b3-bc6d-3f47d0cbaaa2
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 05/24/2018
ms.reviewer: sdash
ms.author: mbullwin
ms.openlocfilehash: 9105b7f44a9677b2b843305c30fec30c74dd8be5
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/02/2018
ms.locfileid: "50958491"
---
# <a name="live-metrics-stream-monitor--diagnose-with-1-second-latency"></a>Élő metrikák Stream: 1 másodperc késéssel diagnosztizálása & figyelése

Az élő metrikák Stream segítségével a valós idejű, éles webes alkalmazások válnak küldetése mintavételi [Application Insights](app-insights-overview.md). Válassza ki, és szűrje a mérőszámokhoz és a teljesítményszámlálókhoz valós időben, anélkül, hogy a szolgáltatás megzavarását megtekintéshez. Vizsgálja meg a minta sikertelen kérések és kivételek hívásláncait. A [Profiler](app-insights-profiler.md), [pillanatkép-hibakereső](app-insights-snapshot-debugger.md), és [Teljesítménytesztelés](app-insights-monitor-web-app-availability.md#performance-tests), élő metrikák Stream nem invazív, és hatékony diagnosztikai eszközt biztosít az élő webes Ezen a webhelyen.

Az élő Stream metrikák a következőket teheti:

* Ellenőrizze a javítás, akkor szabadul fel, miközben által figyelt teljesítmény és a hiba számát.
* Tekintse meg a hatásának tesztelhetjük a terheléseket, és diagnosztizálhatja a problémákat élő. 
* Adott teszt munkamenetek összpontosíthat, vagy szűrje ki ismert problémák, jelölje ki és szűrés a figyelni kívánt metrikákat.
* Kivétel nyomkövetések szerezhet azok.
* Kísérletezzen a következő szűrők megadásával megkereshető a leginkább releváns KPI-ket.
* Bármely Windows számláló élő teljesítmény figyelésére.
* Egy kiszolgáló, amelynek problémákat, és csak az adott kiszolgáló összes a KPI-t vagy élő hírcsatorna szűrő könnyen azonosíthatja.

[![Élő metrikák Stream-videó](./media/app-insights-live-stream/youtube.png)](https://www.youtube.com/watch?v=zqfHf1Oi5PY)

## <a name="get-started"></a>Bevezetés

1. Ha még nem [telepítve van az Application Insights](app-insights-asp-net.md) az ASP.NET-webalkalmazásban vagy [Windows server alkalmazás](app-insights-windows-services.md), most tegye meg. 
2. **A legújabb verzióra való frissítés** az Application Insights-csomag. A Visual Studióban kattintson jobb gombbal a projektre, és válassza a **Nuget-csomagok kezelése**. Nyissa meg a **frissítések** lapon jelölje **előzetes verzió**, és válassza ki a Microsoft.ApplicationInsights.*-csomagokat.

    Helyezze ismét üzembe alkalmazását.

3. Az a [az Azure portal](https://portal.azure.com), nyissa meg az Application Insights-erőforrást az alkalmazáshoz, és nyissa meg az élő Stream.

4. [A vezérlő csatornát](#secure-the-control-channel) használatakor előfordulhat, hogy bizalmas adatok, például az ügyfél-nevek a szűrőket.


![Az Áttekintés panelen kattintson az élő Stream](./media/app-insights-live-stream/live-stream-2.png)

### <a name="no-data-check-your-server-firewall"></a>Nincs adat? Ellenőrizze a kiszolgáló tűzfalán

Ellenőrizze a [kimenő portok az élő metrikák Stream](app-insights-ip-addresses.md#outgoing-ports) nyitva a tűzfalon a kiszolgálók. 


## <a name="how-does-live-metrics-stream-differ-from-metrics-explorer-and-analytics"></a>Élő metrikák Stream Miben különbözik a Metrikaböngésző és az Analytics?

| |Élő stream | Metrikaböngésző és elemzés |
|---|---|---|
|Késés|Egy második belül megjelenített adatok|Összesítjük percben|
|Nincsenek adatmegőrzési|Adatok továbbra is fennáll, míg a diagramra, és ezután a rendszer törli|[Az adatok 90 napig őrizzük meg](app-insights-data-retention-privacy.md#how-long-is-the-data-kept)|
|Igény szerinti|Adatok streamelése közben, nyissa meg az élő mérőszámok|Adatokat küld, amikor az SDK telepítése és engedélyezése|
|Ingyenes|Semmilyen díjat nem az élő Stream adatok|Státuszban [díjszabása](app-insights-pricing.md)
|Mintavételezés|Az összes kijelölt mérőszámok és -számlálók továbbít. Hibák és a hívásláncokat mintát. TelemetryProcessors sem lépnek érvénybe.|Lehet, hogy eseményeket [mintavételezés](app-insights-api-filtering-sampling.md)|
|Vezérlőcsatorna|Szűrő vezérlőelem jelekkel kell küldeni az SDK-t. Javasoljuk, hogy [biztonságos csatorna](#secure-channel).|Kommunikációs még csak egyirányú, a portálra|


## <a name="select-and-filter-your-metrics"></a>Válassza ki, és a mérőszámok szűrése

(A klasszikus ASP.NET-alkalmazások a legfrissebb SDK-val használható.)

Egyéni KPI élő tetszőleges szűrők alkalmazásával bármely Application Insights telemetria a portálról figyelheti. Kattintson a szűrő vezérlőelem megjelenít, amikor Ön rámutatáskor bármelyik a diagramok. Az alábbi ábra egy egyéni kérések száma KPI szűrőket URL-CÍMÉT és időtartama attribútummal van küldik az ábrázolást. Ellenőrizze a Stream előzetes szakasszal, amely egy élő adás a tetszőleges időpontban megadott feltételeknek megfelelő telemetria megjeleníti a szűrőket. 

![Egyéni kérés KPI](./media/app-insights-live-stream/live-stream-filteredMetric.png)

Megfigyelheti, hogy egy másik, innen: darabszám értéket. A beállítások adatfolyam, amely lehet bármelyik az Application Insights telemetria-típustól függnek: kérelmek, függőségek, kivételek, nyomkövetéseket, eseményeket vagy metrikákat. Azok a saját [egyéni mérték](app-insights-api-custom-events-metrics.md#properties):

![Érték beállításai](./media/app-insights-live-stream/live-stream-valueoptions.png)

Az Application Insights telemetriát, mellett is figyelheti bármely Windows-teljesítményszámláló jelölje ki, amely a stream-beállításokat, és a írja be a teljesítményszámláló nevét.

Összesítjük élő metrikák két pont: helyi minden kiszolgálón, majd a összes kiszolgáló között. Az alapértelmezett, vagy a megfelelő legördülő listákból egyéb lehetőségek kiválasztásával módosíthatja.

## <a name="sample-telemetry-custom-live-diagnostic-events"></a>Minta Telemetriai: Egyéni élő diagnosztikai események
Alapértelmezés szerint az események élő adás sikertelen kérelmek és a függőségi hívások, kivételek, események és nyomkövetések mintákat mutat be. Kattintson a szűrő ikonjára kattintva ellenőrizheti a alkalmazott tetszőleges időpontban. 

![Alapértelmezett élő adás](./media/app-insights-live-stream/live-stream-eventsdefault.png)

Igény szerint metrikákkal, megadhat bármilyen tetszőleges feltételeket az Application Insights telemetria típusok. Ebben a példában azt tulajdonképpen kiválasztja az adott kérés sikertelen, nyomkövetéseket és eseményeket. Azt is kiválasztja, kivételeket és a függőségi hibák.

![Egyéni élő adás](./media/app-insights-live-stream/live-stream-events.png)

Megjegyzés: Jelenleg a kivétel üzenet-alapú feltételek, a legkülső Kivételüzenet használja. Az előző példában, amellyel szűrheti a belső kivételre vonatkozó üzenet a jóindulatú kivétel (követi a "<--" elválasztó karakter) "klient byl odpojen." egy üzenet használata nem – "Hiba a kérés tartalma olvasása" feltételeket tartalmaz.

Ehhez kattintson rá egy elemet az élő adás részleteinek megtekintéséhez. Vagy kattintson a hírcsatorna akár szüneteltetheti **szüneteltetése** egyszerűen lefelé görgethető, vagy ha egy elemre kattint. Élő adás, görgessen a lap tetejére, vagy kattintson a számláló során fel lett függesztve gyűjti elemeket után folytatódik.

![A mintavételezett élő hibák](./media/app-insights-live-stream/live-metrics-eventdetail.png)

## <a name="filter-by-server-instance"></a>Szűrés a server-példány

Ha egy adott kiszolgáló szerepkörpéldány figyelni kívánt, kiszolgáló végezhet.

![A mintavételezett élő hibák](./media/app-insights-live-stream/live-stream-filter.png)

## <a name="sdk-requirements"></a>SDK használatának követelményei
Egyéni élő metrikák Stream 2.4.0-beta2 verzió érhető el, vagy az újabb [Application Insights SDK webes](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/). Ne felejtse el a NuGet-Csomagkezelő "Tartalmazza előzetes" beállítást.

## <a name="secure-the-control-channel"></a>A vezérlőcsatorna biztonságossá tétele
A megadott egyéni szűrők feltételek lesznek visszaküldve az élő mérőszámok az Application Insights SDK összetevőt. A szűrők potenciálisan bizalmas adatokat például customerIDs tartalmazhatnak. Hogy a csatorna biztonságos mellett a kialakítási kulcsot API titkos kulccsal.
### <a name="create-an-api-key"></a>API-kulcs létrehozása

![Api-kulcs létrehozása](./media/app-insights-live-stream/live-metrics-apikeycreate.png)

### <a name="add-api-key-to-configuration"></a>Konfigurációs API-kulcs hozzáadása

### <a name="classic-aspnet"></a>Klasszikus ASP.NET

Az applicationinsights.config fájlban a QuickPulseTelemetryModule a AuthenticationApiKey hozzá:
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

### <a name="aspnet-core-requires-application-insights-aspnet-core-sdk-230-beta-or-greater"></a>ASP.NET Core (kell Application Insights az ASP.NET Core SDK 2.3.0-beta vagy újabb)

A startup.cs fájlban módosítsa a következőképpen:

Először adjon hozzá

``` C#
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse;
```

Ezután belül a ConfigureServices metódus hozzáadása:

``` C#
services.ConfigureTelemetryModule<QuickPulseTelemetryModule>( module => module.AuthenticationApiKey = "YOUR-API-KEY-HERE");
```


Ha ismeri fel, és a csatlakoztatott kiszolgálókra megbízható, megpróbálhatja az egyéni szűrők nélkül a hitelesített csatornát. Ez a beállítás hat hónapig érhető el. Ez a felülbírálás kötelező egyszer minden új munkamenetet, vagy ha új kiszolgálót online állapotba kerül.

![Élő metrikák hitelesítési beállítások](./media/app-insights-live-stream/live-stream-auth.png)

>[!NOTE]
>Javasoljuk, hogy állítsa be a hitelesített csatornát a szűrési feltételeket a potenciálisan bizalmas adatokat, például a CustomerID megadása előtt.
>

## <a name="generating-a-performance-test-load"></a>Teljesítmény tesztelése terhelés generálása

Ha azt szeretné, tekintse meg a terhelés megnövekedése hatását, használja a teljesítményteszt panel. Azt szimulálja egy egyidejű felhasználók száma érkező kérelmeket. Vagy "manuális tesztek" Futtatás (ping tesztek) egyetlen URL-címet, vagy futhat egy [többlépéses webes teljesítménytesztelési](app-insights-monitor-web-app-availability.md#multi-step-web-tests) feltöltött (a rendelkezésre állási teszt azonos módon).

> [!TIP]
> Miután létrehozta a teljesítményteszt, nyissa meg a teszt- és az élő Stream panel külön windows. Megtekintheti a sorban álló teljesítményteszt indításakor, és tekintse meg az élő stream egyszerre.
>


## <a name="troubleshooting"></a>Hibaelhárítás

Nincs adat? Ha az alkalmazás egy védett hálózati: élő metrikák Stream használja egy másik IP-címek, mint más Application Insights telemetria. Győződjön meg arról, hogy [adott IP-címek](app-insights-ip-addresses.md) nyitva van a tűzfal.



## <a name="next-steps"></a>További lépések
* [Az Application Insights figyelési használat](app-insights-usage-overview.md)
* [Diagnosztikai keresés használata](app-insights-diagnostic-search.md)
* [Profilkészítő](app-insights-profiler.md)
* [Pillanatkép-hibakereső](app-insights-snapshot-debugger.md)
