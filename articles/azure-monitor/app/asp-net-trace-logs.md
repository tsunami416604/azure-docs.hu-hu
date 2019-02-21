---
title: Ismerkedés a .NET hívásláncnaplók megtekintése az Application Insights
description: A nyomkövetés, NLog és Log4Net létrehozott naplók keresése.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 0c2a084f-6e71-467b-a6aa-4ab222f17153
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: mbullwin
ms.openlocfilehash: f89eca6fb8893210f4c65adc42598ab0e0b531f4
ms.sourcegitcommit: 75fef8147209a1dcdc7573c4a6a90f0151a12e17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/20/2019
ms.locfileid: "56454317"
---
# <a name="explore-netnet-core-trace-logs-in-application-insights"></a>Nyomkövetési naplók az Application Insights.NET/.NET Core bemutatása

Ha használ ILogger, az NLog, a log4Net vagy a System.Diagnostics.trace keretrendszert használja a diagnosztikai nyomkövetés az ASP.NET/ASP.NET Core-alkalmazást, lehetőség van a küldött naplók [Azure Application Insights][start], ahol Ön Ismerje meg, és kereshet bennük. A naplók fogja egyesíthető az alkalmazásból érkező, hogy a karbantartási minden egyes felhasználói kéréshez társított nyomkövetési azonosításához, és összefüggésbe hozva azokat az egyéb események és a kivételekről szóló jelentések egyéb telemetriai adatokat.

> [!NOTE]
> Szükség van a rögzítési naplómoduljának? 3. fél másolása egy hasznos adaptere, de ha már nem használja az NLog, log4Net, vagy a System.Diagnostics.trace keretrendszert használja, érdemes lehet csak hívó [Application Insights TrackTrace()](../../azure-monitor/app/api-custom-events-metrics.md#tracktrace) közvetlenül.
>
>

## <a name="install-logging-on-your-app"></a>Bejelentkezés az alkalmazás telepítése
A kiválasztott naplózási keretrendszer telepítéséhez a projektben. Ennek eredménye egy bejegyzést az app.config vagy a web.config.

```XML
    <configuration>
      <system.diagnostics>
    <trace autoflush="true" indentsize="0">
      <listeners>
        <add name="myAppInsightsListener" type="Microsoft.ApplicationInsights.TraceListener.ApplicationInsightsTraceListener, Microsoft.ApplicationInsights.TraceListener" />
      </listeners>
    </trace>
  </system.diagnostics>
   </configuration>
```

## <a name="configure-application-insights-to-collect-logs"></a>Naplók gyűjtése az Application Insights beállítása
**[Az Application Insights hozzáadása a projekthez](../../azure-monitor/app/asp-net.md)**  még nem tette, hogy ha. Láthatja, hogy egy naplógyűjtő lehetősége.

Vagy **Application Insights konfigurálása** kattintson a jobb gombbal a projektre a Megoldáskezelőben. Válassza a **nyomkövetési gyűjtésének konfigurálása**.

*Nincs az Application Insights menüből vagy a napló gyűjtő lehetőség?* Próbálja ki [hibaelhárítási](#troubleshooting).

## <a name="manual-installation"></a>Manuális telepítés
Ezt a módszert akkor használja, ha a projekt típusa nem támogatja az Application Insights-telepítő (például egy Windows asztali projekt).

1. Ha azt tervezi, a log4Net, NLog, vagy használja, telepítse a projektben.
2. A Megoldáskezelőben kattintson jobb gombbal a projektre, és válassza a **NuGet-csomagok kezelése**.
3. Az „Application Insights” kifejezés keresése
4. Válassza ki a következő csomagok egyikét:

   - A ILogger: [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights/)
[![Nuget](https://img.shields.io/nuget/vpre/Microsoft.Extensions.Logging.ApplicationInsights.svg)](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights/)
   - Az NLog: [Microsoft.ApplicationInsights.NLogTarget](http://www.nuget.org/packages/Microsoft.ApplicationInsights.NLogTarget/)
[![Nuget](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.NLogTarget.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.NLogTarget/)
   - A Log4Net: [Microsoft.ApplicationInsights.Log4NetAppender](http://www.nuget.org/packages/Microsoft.ApplicationInsights.Log4NetAppender/)
[![Nuget](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.Log4NetAppender.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Log4NetAppender/)
   - A System.Diagnostics: [Microsoft.ApplicationInsights.TraceListener](http://www.nuget.org/packages/Microsoft.ApplicationInsights.TraceListener/)
[![Nuget](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.TraceListener.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.TraceListener/)
   - [Microsoft.ApplicationInsights.DiagnosticSourceListener](http://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener/)
[![Nuget](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.DiagnosticSourceListener.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener/)
   - [Microsoft.ApplicationInsights.EtwCollector](http://www.nuget.org/packages/Microsoft.ApplicationInsights.EtwCollector/)
[![Nuget](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.EtwCollector.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EtwCollector/)
   - [Microsoft.ApplicationInsights.EventSourceListener](http://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener/)
[![Nuget](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.EventSourceListener.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener/)

A NuGet csomag telepíti a szükséges szerelvényeket, és adott esetben módosítja a web.config vagy az App.config fájlt.

## <a name="ilogger"></a>ILogger

A példa az Application Insights ILogger az konzolalkalmazással és az ASP.NET Core-implementáció tekintse meg ezt [cikk](ilogger.md).

## <a name="insert-diagnostic-log-calls"></a>Szúrja be a diagnosztikai napló
Használja a System.Diagnostics.trace keretrendszert használja, ha egy tipikus hívást a következő lesz:

    System.Diagnostics.Trace.TraceWarning("Slow response - database01");

Log4net, NLog, vagy igény szerint:

    logger.Warn("Slow response - database01");

## <a name="using-eventsource-events"></a>EventSource események használata
Konfigurálható [System.Diagnostics.Tracing.EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) kell küldeni az Application Insights-nyomkövetéseket, eseményeket. Először telepítse a `Microsoft.ApplicationInsights.EventSourceListener` NuGet-csomagot. Szerkesszen `TelemetryModules` szakaszában a [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md) fájlt.

```xml
    <Add Type="Microsoft.ApplicationInsights.EventSourceListener.EventSourceTelemetryModule, Microsoft.ApplicationInsights.EventSourceListener">
      <Sources>
        <Add Name="MyCompany" Level="Verbose" />
      </Sources>
    </Add>
```

Az egyes források állíthatja be a következő paraméterekkel:
 * `Name` Meghatározza a gyűjtéséhez az eseményforrás nevét.
 * `Level` Adja meg a naplózási szint gyűjtéséhez. Lehetnek `Critical`, `Error`, `Informational`, `LogAlways`, `Verbose`, `Warning`.
 * `Keywords` (Nem kötelező) Itt adhatja meg a kulcsszavak kombinációja használni az egész értéket.

## <a name="using-diagnosticsource-events"></a>DiagnosticSource események használata
Konfigurálható [System.Diagnostics.DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md) kell küldeni az Application Insights-nyomkövetéseket, eseményeket. Először telepítse a [ `Microsoft.ApplicationInsights.DiagnosticSourceListener` ](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener) NuGet-csomagot. Szerkessze a `TelemetryModules` szakaszában a [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md) fájlt.

```xml
    <Add Type="Microsoft.ApplicationInsights.DiagnosticSourceListener.DiagnosticSourceTelemetryModule, Microsoft.ApplicationInsights.DiagnosticSourceListener">
      <Sources>
        <Add Name="MyDiagnosticSourceName" />
      </Sources>
    </Add>
```

A követni kívánt minden egyes DiagnosticSource, adjon hozzá egy bejegyzést, és a `Name` attribútum beállítása a DiagnosticSource nevére.

## <a name="using-etw-events"></a>ETW-események használatával
Konfigurálhatja az Application Insights nyomkövetésként küldendő ETW-események. Először telepítse a `Microsoft.ApplicationInsights.EtwCollector` NuGet-csomagot. Szerkesszen `TelemetryModules` szakaszában a [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md) fájlt.

> [!NOTE] 
> Ha az SDK-t tartalmazó folyamat, amely tagja a rendszergazdák vagy a "Teljesítménynapló felhasználói" identitás alatt fut ETW-események csak lehessen gyűjteni.

```xml
    <Add Type="Microsoft.ApplicationInsights.EtwCollector.EtwCollectorTelemetryModule, Microsoft.ApplicationInsights.EtwCollector">
      <Sources>
        <Add ProviderName="MyCompanyEventSourceName" Level="Verbose" />
      </Sources>
    </Add>
```

Az egyes források állíthatja be a következő paraméterekkel:
 * `ProviderName` az a név az ETW-szolgáltató gyűjtéséhez.
 * `ProviderGuid` Adja meg az ETW-szolgáltató gyűjtse össze a globálisan egyedi Azonosítót helyett használható `ProviderName`.
 * `Level` Beállítja a naplózási szint gyűjtéséhez. Lehetnek `Critical`, `Error`, `Informational`, `LogAlways`, `Verbose`, `Warning`.
 * `Keywords` (Nem kötelező) az egész értéket kulcsszó kombinációk használatára állítja be.

## <a name="using-the-trace-api-directly"></a>A nyomkövetés API közvetlen használatával
Az Application Insights nyomkövetés API-t hívhatja közvetlenül. A naplózás adapterek ezen API-val.

Példa:

    var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
    telemetry.TrackTrace("Slow response - database01");

TrackTrace előnye, hogy viszonylag hosszú adatok helyezheti az üzenetben. Hiba a POST data kódolása például sikerült.

Emellett is hozzáadhat egy súlyossági szintet az üzenetet. És egyéb telemetriát, például a tulajdonságértékeket, amellyel szűrőt, vagy keressen a nyomkövetések más-más részhalmazához adhat hozzá. Példa:

    var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
    telemetry.TrackTrace("Slow database response",
                   SeverityLevel.Warning,
                   new Dictionary<string,string> { {"database", db.ID} });

Ez lehetővé tenné, hogy a [keresési][diagnostic], egyszerűen egy adott súlyossági szintet, hogy adott adatbázissal kapcsolatos, az üzenetek szűréséhez.

## <a name="explore-your-logs"></a>Ismerje meg a naplók
Futtassa az alkalmazást, vagy hibakeresési módban, vagy élő telepítheti.

Az alkalmazás áttekintése panelen a [az Application Insights portálon][portal], válassza a [keresési][diagnostic].

Akkor is, például:

* Nyomkövetési naplók, vagy adott tulajdonságokkal rendelkező elemek szűrése
* Vizsgálja meg az adott elem részletei.
* Keresse meg az ugyanazon felhasználói kérésre vonatkozó egyéb telemetriai (azaz az azonos Műveletazonosítóval rendelkező)
* Ezen a lapon-konfigurációjának mentése a Kedvencek közé

> [!NOTE]
> **Mintavétel.** Ha az alkalmazása sok adatot küld, és az Application Insights SDK-t az ASP.NET 2.0.0-beta3 vagy újabb verziójához használja, működhet az adaptív mintavételezés funkció és lehet, hogy csak a telemetria valamely százalékát küldi el. [További tudnivalók a mintavételezésről.](../../azure-monitor/app/sampling.md)
>
>

## <a name="next-steps"></a>További lépések
[Diagnosztizálhatja a hibákat és kivételeket az ASP.NET-ben][exceptions]

[További tudnivalók a keresési][diagnostic].

## <a name="troubleshooting"></a>Hibaelhárítás
### <a name="how-do-i-do-this-for-java"></a>Hogyan készíthetek a Javához készült?
Használja a [Java log adapterek](../../azure-monitor/app/java-trace-logs.md).

### <a name="theres-no-application-insights-option-on-the-project-context-menu"></a>Nem az Application Insights beállítani a projekt helyi menüjében
* Ellenőrizze, hogy az Application Insights-eszközök telepítve vannak-e a fejlesztői gépen. A Visual Studio menüjében eszközök, bővítmények és frissítések keresse meg az Application Insights Tools. Ha nem szerepel a telepített fülre, nyissa meg az Online lapot, és telepítse.
* Ez lehet egy Application Insights tools nem támogatja a projekt típusa. Használat [manuális telepítés](#manual-installation).

### <a name="no-log-adapter-option-in-the-configuration-tool"></a>Nincs napló adapter lehetőség a konfigurációs eszköz
* A naplózási keretrendszer először telepítenie kell.
* Ha System.Diagnostics.Trace használ, ellenőrizze, hogy [konfigurálva a `web.config` ](https://msdn.microsoft.com/library/system.diagnostics.eventlogtracelistener.aspx).
* Nem rendelkezik működik a legújabb Application Insights? A Visual Studióban **eszközök** menüben válassza a **bővítmények és frissítések**, és nyissa meg a **frissítések** lapon. Developer Analytics tools vannak-e, ha kattintva frissítse azt.

### <a name="emptykey"></a>"Kialakítási kulcsot nem lehet üres" hibaüzenetet kapok
Úgy tűnik, a naplózás adapter Nuget-csomagot telepítette az Application Insights telepítése nélkül.

A Megoldáskezelőben kattintson a jobb gombbal `ApplicationInsights.config` válassza **Update Application Insights**. Kap egy párbeszédpanel, amely felkéri, hogy jelentkezzen be az Azure-ba, és hozzon létre egy Application Insights-erőforrást, vagy újra felhasználhatja egy már meglévőt. Meg kell határoznia azt.

### <a name="i-can-see-traces-in-diagnostic-search-but-not-the-other-events"></a>Látható, hogy a diagnosztikai keresés, de nem az egyéb események nyomkövetések
Egyes esetekben is eltarthat, amíg az összes esemény és -kéréseinek keresztül.

### <a name="limits"></a>Mennyi adatot megmarad?
Számos tényező befolyásolhatja a megőrzött adatok mennyisége. Tekintse meg a [korlátok](../../azure-monitor/app/api-custom-events-metrics.md#limits) szakaszban további információk az ügyfél esemény metrikák oldal. 

### <a name="im-not-seeing-some-of-the-log-entries-that-i-expect"></a>Nem látok az egyes a naplóbejegyzéseket, amelyeket a várt
Ha az alkalmazása sok adatot küld, és az Application Insights SDK-t az ASP.NET 2.0.0-beta3 vagy újabb verziójához használja, működhet az adaptív mintavételezés funkció és lehet, hogy csak a telemetria valamely százalékát küldi el. [További tudnivalók a mintavételezésről.](../../azure-monitor/app/sampling.md)

## <a name="add"></a>Következő lépések
* [Állítsa be a rendelkezésre állás és a válaszképesség tesztek][availability]
* [Hibaelhárítás][qna]

<!--Link references-->

[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[exceptions]: asp-net-exceptions.md
[portal]: https://portal.azure.com/
[qna]: ../../azure-monitor/app/troubleshoot-faq.md
[start]: ../../azure-monitor/app/app-insights-overview.md
