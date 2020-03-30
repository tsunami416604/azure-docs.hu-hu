---
title: Diagnosztizálás élő metrikák streamelésével – Azure Application Insights
description: A webalkalmazást valós időben figyelheti egyéni mérőszámokkal, és diagnosztizálhatja a hibák, nyomkövetések és események élő hírcsatornájával kapcsolatos problémákat.
ms.topic: conceptual
ms.date: 04/22/2019
ms.reviewer: sdash
ms.openlocfilehash: ea0d786d0b8b96941d791bcc8e92fad9a869c5f3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77670100"
---
# <a name="live-metrics-stream-monitor--diagnose-with-1-second-latency"></a>Élő metrikák stream: Monitor & diagnosztizálása 1 másodperces késleltetéssel

Az Élő metrikák streamelése az [Application Insightsból](../../azure-monitor/app/app-insights-overview.md)használatával vizsgálja meg az élő, éles környezetben lévő webes alkalmazás dobogó szívét. Válassza ki és szűrje a mérőszámokat és a teljesítményszámlálókat, amelyeket valós időben szeretne figyelni, a szolgáltatás zavarása nélkül. Vizsgálja meg a minta sikertelen kérelmek és kivételek veremnyomkövetéseit. A [Profiler](../../azure-monitor/app/profiler.md), [Snapshot debugger](../../azure-monitor/app/snapshot-debugger.md)segítségével együtt. A Live Metrics Stream hatékony és nem invazív diagnosztikai eszközt biztosít az élő webhelyhez.

Az Élő mérőszámok streamelésével a következőket teheti:

* Ellenőrizze a javítást, amíg fel szabadul, a teljesítmény és a hibaszám figyelésével.
* Nézze meg a tesztterhelések hatását, és diagnosztizálja a problémákat élőben.
* Összpontosítson az egyes tesztmunkamenetekre, vagy szűrje ki az ismert problémákat a megfigyelni kívánt mérőszámok kiválasztásával és szűrésével.
* Kivételnyomokat kaphat, ahogy történnek.
* Kísérletezzen szűrőkkel a legfontosabb kpi-k megkereséséhez.
* A Windows teljesítményszámlálójának figyelése élőben.
* Könnyen azonosíthatja a problémákkal rendelkező kiszolgálót, és szűrheti az összes KPI/élő hírcsatornát csak az adott kiszolgálóra.

[![Élő mutatók streamelése – videó](./media/live-stream/youtube.png)](https://www.youtube.com/watch?v=zqfHf1Oi5PY)

Az élő metrikák jelenleg támogatottak ASP.NET, ASP.NET Core, Azure Functions, Java és Node.js alkalmazások.

## <a name="get-started"></a>Bevezetés

1. Ha még nem [telepítette az Application Insightsalkalmazást](../../azure-monitor/azure-monitor-app-hub.yml) a webalkalmazásban, tegye meg most.
2. A [microsoft.ApplicationInsights.PerfCounterCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector/) szabványos Application Insights-csomagok mellett az Élő metrikák streamengedélyezéséhez is szükség van.
3. **Frissítsen az** Application Insights-csomag legújabb verziójára. A Visual Studio jobb gombbal kattintson a projektre, és válassza **a Nuget-csomagok kezelése parancsot.** Nyissa meg a **Frissítések** lapot, és jelölje ki az összes Microsoft.ApplicationInsights.* csomagot.

    Helyezze ismét üzembe alkalmazását.

3. Az [Azure Portalon](https://portal.azure.com)nyissa meg az Application Insights-erőforrást az alkalmazáshoz, majd nyissa meg az élő közvetítést.

4. [Biztosítsa a vezérlőcsatornát,](#secure-the-control-channel) ha bizalmas adatokat, például ügyfélneveket használhat a szűrőkben.

### <a name="no-data-check-your-server-firewall"></a>Nincs adat? A kiszolgáló tűzfalának ellenőrzése

Ellenőrizze, hogy az [Élő metrikák stream kimenő portjai](../../azure-monitor/app/ip-addresses.md#outgoing-ports) meg vannak nyitva a kiszolgálók tűzfalán.

## <a name="how-does-live-metrics-stream-differ-from-metrics-explorer-and-analytics"></a>Miben különbözik az élő mérőszámok streamje a Metrikakezelőtől és az Analytics-től?

| |Élő stream | Mérőszámok Explorer és Analytics |
|---|---|---|
|Késés|Egy másodpercen belül megjelenített adatok|Percek alatt összesítve|
|Nincs megőrzés|Az adatok a diagramon maradnak, majd elvetik őket|[90 napig megőrzött adatok](../../azure-monitor/app/data-retention-privacy.md#how-long-is-the-data-kept)|
|Igény szerinti|Az adatok streamelése az Élő metrikák megnyitásakor történik|Az adatok küldése az SDK telepítésekor és engedélyezésénél|
|Ingyenes|Az élő közvetítés adataiért nem számítunk fel díjat|Az [árképzés függvénye](../../azure-monitor/app/pricing.md)
|Mintavételezés|Az összes kiválasztott mérőszámok és számlálók továbbítják. A hibák és a veremnyomkövetések mintavételezésre kerülnek. TelemettryProcesszorok nem alkalmazzák.|Az események [ből lehet mintát venni](../../azure-monitor/app/api-filtering-sampling.md)|
|Vezérlőcsatorna|A rendszer szűrővezérlő jeleket küld az SDK-nak. Javasoljuk, hogy biztosítsa ezt a csatornát.|A kommunikáció az egyik módja, hogy a portál|

## <a name="select-and-filter-your-metrics"></a>A mérőszámok kiválasztása és szűrése

(Elérhető ASP.NET, ASP.NET Core és Azure Functions (v2))

Egyéni KPI-t figyelhet élőben, ha tetszőleges szűrőket alkalmaz a portálról az Application Insights telemetriai adataira. Kattintson arra a szűrővezérlőre, amely azt jelzi, hogy mikor egérke a diagramok valamelyikén. A következő diagram egy egyéni kérelemszám KPI-t ábrázol, amely az URL- és időtartam-attribútumokat szűri. Ellenőrizze a szűrőket a Stream előzetes verziójával, amely a telemetriai adatok élő hírcsatornáját jeleníti meg, amely megfelel a megadott feltételeknek.

![Egyéni kérelem Fő teljesítménymutatója](./media/live-stream/live-stream-filteredMetric.png)

A Count-tól eltérő értéket figyelhet. A beállítások az adatfolyam típusától függenek, amely bármely Application Insights telemetriai adat lehet: kérések, függőségek, kivételek, nyomkövetések, események vagy metrikák. Ez lehet a saját [egyéni mérés:](../../azure-monitor/app/api-custom-events-metrics.md#properties)

![Értékbeállítások](./media/live-stream/live-stream-valueoptions.png)

Az Application Insights telemetriai adatok mellett a Windows teljesítményszámlálóját is figyelheti, ha ezt az adatfolyam-beállítások közül kiválasztja, és megadja a teljesítményszámláló nevét.

Az élő metrikák összesítése két ponton történik: helyileg az egyes kiszolgálókon, majd az összes kiszolgálón. Az alapértelmezett beállítást bármelyikkor módosíthatja, ha a megfelelő legördülő menüben más beállításokat is kiválaszt.

## <a name="sample-telemetry-custom-live-diagnostic-events"></a>Minta telemetriai adatok: Egyéni élő diagnosztikai események
Alapértelmezés szerint az események élő hírcsatornája a sikertelen kérelmek és függőségi hívások, kivételek, események és nyomkövetések mintáit jeleníti meg. Kattintson a szűrő ikonra az alkalmazott feltételek megtekintéséhez bármikor. 

![Alapértelmezett élő hírcsatorna](./media/live-stream/live-stream-eventsdefault.png)

A metrikákhoz is tetszőleges feltételeket adhat meg az Application Insights telemetriai típusok bármelyikéhez. Ebben a példában konkrét kéréshibákat, nyomkövetéseket és eseményeket választunk ki. Az összes kivételt és függőségi hibát is kiválasztjuk.

![Egyéni élő hírcsatorna](./media/live-stream/live-stream-events.png)

Megjegyzés: Jelenleg az Exception üzenetalapú feltételek esetében használja a legkülső kivételüzenetet. Az előző példában a belső kivételüzenettel (a "< --" határolóval) "Az ügyfél leválasztott" jóindulatú kivétel kiszűréséhez. ne használjon "Hibaolvasási kérelem tartalmának" feltételt tartalmazó üzenetet.

Az élő hírfolyamban lévő elemek részleteit kattintással megtekintheti. A hírcsatorna szüneteltetéséhez kattintson a **Szünet gombra,** vagy egyszerűen csak lefelé görget, vagy kattintson egy elemre. Az élő hírfolyam a lapozás után folytatódik a lap tetejére, vagy a szüneteltetés alatt összegyűjtött elemek számlálójára kattintva.

![Mintavételezett élő hibák](./media/live-stream/live-metrics-eventdetail.png)

## <a name="filter-by-server-instance"></a>Szűrés kiszolgálópéldány szerint

Ha egy adott kiszolgálói szerepkörpéldányt szeretne figyelni, kiszolgáló szerint szűrhet.

![Mintavételezett élő hibák](./media/live-stream/live-stream-filter.png)

## <a name="secure-the-control-channel"></a>A vezérlőcsatorna biztosítása
A megadott egyéni szűrők feltételek et visszaküldi az Application Insights SDK Élő metrikák összetevőjének. A szűrők potenciálisan bizalmas információkat, például ügyfélazonosítókat tartalmazhatnak. A csatornát egy titkos API-kulcs mellett egy titkos API-kulcs segítségével biztonságossá teheti.
### <a name="create-an-api-key"></a>API-kulcs létrehozása

![API-kulcs létrehozása](./media/live-stream/live-metrics-apikeycreate.png)

### <a name="add-api-key-to-configuration"></a>API-kulcs hozzáadása a konfigurációhoz

### <a name="classic-aspnet"></a>Klasszikus ASP.NET

Az applicationinsights.config fájlban adja hozzá az AuthenticationApiKey kulcsot a QuickPulseTelemetryModule modulhoz:
``` XML

<Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse.QuickPulseTelemetryModule, Microsoft.AI.PerfCounterCollector">
      <AuthenticationApiKey>YOUR-API-KEY-HERE</AuthenticationApiKey>
</Add>

```
Vagy a kódban állítsa be a QuickPulseTelemetryModule:

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

### <a name="azure-function-apps"></a>Azure-függvényalkalmazások

Az Azure Function Apps (v2) esetében a csatorna API-kulccsal történő védelme egy környezeti változóval valósítható meg.

Hozzon létre egy API-kulcsot az Application Insights-erőforrásból, és nyissa meg a **Függvényalkalmazás-beállítások lehetőséget.** Válassza **az Új beállítás** hozzáadása `APPINSIGHTS_QUICKPULSEAUTHAPIKEY` lehetőséget, és adja meg az API-kulcsnak megfelelő nevet és értéket.

### <a name="aspnet-core-requires-application-insights-aspnet-core-sdk-230-or-greater"></a>ASP.NET Core (Alkalmazáselemzési ASP.NET Core SDK 2.3.0 vagy nagyobb)

Módosítsa a startup.cs fájlt az alábbiak szerint:

Első hozzáadás

```csharp
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse;
```

Ezután a ConfigureServices metóduson belül adja hozzá:

```csharp
services.ConfigureTelemetryModule<QuickPulseTelemetryModule> ((module, o) => module.AuthenticationApiKey = "YOUR-API-KEY-HERE");
```

Ha azonban felismeri és megbízik az összes csatlakoztatott kiszolgálóban, megpróbálhatja az egyéni szűrőket a hitelesített csatorna nélkül. Ez a lehetőség hat hónapig áll rendelkezésre. Ez a felülbírálás minden új munkamenet után, vagy amikor egy új kiszolgáló online állapotba kerül.

![Élő metrikák hitelesítési beállításai](./media/live-stream/live-stream-auth.png)

>[!NOTE]
>Javasoljuk, hogy állítsa be a hitelesített csatornát, mielőtt potenciálisan bizalmas adatokat, például CustomerID-t ad meg a szűrőfeltételekben.
>

## <a name="supported-features-table"></a>Támogatott szolgáltatások táblázat

| Nyelv                         | Alapvető metrikák       | Teljesítmény-mérőszámok | Egyéni szűrés    | Minta telemetria    | A PROCESSZOR folyamat szerint felosztott |
|----------------------------------|:--------------------|:--------------------|:--------------------|:--------------------|:---------------------|
| .NET                             | Támogatott (2.7.2+) | Támogatott (2.7.2+) | Támogatott (2.7.2+) | Támogatott (2.7.2+) | Támogatott (2.7.2+)  |
| .NET Core (cél=.NET keretrendszer)| Támogatott (V2.4.1+) | Támogatott (V2.4.1+) | Támogatott (V2.4.1+) | Támogatott (V2.4.1+) | Támogatott (V2.4.1+)  |
| .NET Core (cél=.NET core)     | Támogatott (V2.4.1+) | Támogatott*          | Támogatott (V2.4.1+) | Támogatott (V2.4.1+) | **Nem támogatott**    |
| Azure Functions v2               | Támogatott           | Támogatott           | Támogatott           | Támogatott           | **Nem támogatott**    |
| Java                             | Támogatott (2.0.0+) | Támogatott (2.0.0+) | **Nem támogatott**   | **Nem támogatott**   | **Nem támogatott**    |
| Node.js                          | Támogatott (1.3.0+) | Támogatott (1.3.0+) | **Nem támogatott**   | Támogatott (1.3.0+) | **Nem támogatott**    |

Az alapvető metrikák közé tartozik a kérelem, a függőség és a kivétel aránya. A teljesítménymutatók (teljesítményszámlálók) közé tartozik a memória és a processzor. Minta telemetriai adatok at jeleníti meg a sikertelen kérelmek és függőségek, kivételek, események és nyomkövetések részletes információkat.

 \*A PerfCounters támogatása a .NET Core azon verziói ban kissé eltérő, amelyek nem célozzák meg a .NET keretrendszert:

- PerfCounters metrikák támogatottak, ha fut az Azure App Service for Windows. (AspNetCore SDK 2.4.1-es vagy újabb verzió)
- PerfCounters támogatottak, ha az alkalmazás fut bármilyen Windows gépek (VM vagy Cloud Service vagy On-prem stb)PerfCounters are supported when app is running in any Windows machines (VM or Cloud Service or On-prem etc.) (AspNetCore SDK 2.7.1-es vagy újabb verzió), de a .NET Core 2.0 vagy újabb verziót célzó alkalmazások esetében.
- PerfCounters támogatottak, ha az alkalmazás fut BÁRHOL (Linux, Windows, app service for Linux, konténerek, stb) a legújabb béta (azaz AspNetCore SDK 2.8.0-béta1 vagy újabb verzió), de a .NET Core 2.0 vagy újabb verziót célzó alkalmazások esetében.

Alapértelmezés szerint az élő metrikák le vannak tiltva a Node.js SDK.By Default Live Metrics is disabled in the Node.js SDK. Az élő metrikák engedélyezéséhez adja hozzá `setSendLiveMetrics(true)` a [konfigurációs módszereket](https://github.com/Microsoft/ApplicationInsights-node.js#configuration) az SDK inicializálásakor.

## <a name="troubleshooting"></a>Hibaelhárítás

Nincs adat? Ha az alkalmazás egy védett hálózat: Live Metrics Stream más IP-címeket használ, mint más Application Insights telemetriai adatok. Győződjön meg arról, hogy [ezek az IP-címek](../../azure-monitor/app/ip-addresses.md) meg vannak nyitva a tűzfalon.

## <a name="next-steps"></a>További lépések
* [Használat figyelése az Application Insights segítségével](../../azure-monitor/app/usage-overview.md)
* [A diagnosztikai keresés használata](../../azure-monitor/app/diagnostic-search.md)
* [Profilkészítő](../../azure-monitor/app/profiler.md)
* [Pillanatkép-hibakereső](../../azure-monitor/app/snapshot-debugger.md)
