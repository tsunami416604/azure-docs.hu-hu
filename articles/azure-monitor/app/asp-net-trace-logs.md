---
title: Ismerkedés a .NET hívásláncnaplók megtekintése az Application Insights
description: Nyomkövetési, NLog és Log4Net által létrehozott naplók keresése.
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
ms.openlocfilehash: 74cb1b3ec4e0570aa4316e6f45e99719f36815d1
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65150701"
---
# <a name="explore-netnet-core-trace-logs-in-application-insights"></a>Nyomkövetési naplók az Application Insights.NET/.NET Core bemutatása

Diagnosztikai nyomkövetési naplók ASP.NET/ASP.NET Core alkalmazás küldése ILogger, az NLog, a log4Net vagy a System.Diagnostics.trace keretrendszert használja, a [Azure Application Insights][start]. Ismerje meg, majd kereshet bennük. Ezeket a naplókat az alkalmazásból, további naplófájlok egyesülnek, így azonosíthatja a nyomkövetéseket, és minden egyes felhasználói kéréshez társítva összefüggésbe hozva azokat az egyéb események és a kivételekről szóló jelentések.

> [!NOTE]
> Szükség van a napló-rögzítési modult? Egy harmadik fél másolása hasznos adaptert. De ha System.Diagnostics.Trace, NLog és log4Net már nem használ, érdemes lehet csak hívó [ **Application Insights TrackTrace()** ](../../azure-monitor/app/api-custom-events-metrics.md#tracktrace) közvetlenül.
>
>
## <a name="install-logging-on-your-app"></a>Bejelentkezés az alkalmazás telepítése
A kiválasztott naplózási keretrendszer telepítése a projekt, amely egy app.config vagy a web.config bejegyzésében kell eredményeznie.

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
[Az Application Insights hozzáadása a projekthez](../../azure-monitor/app/asp-net.md) még nem tette, hogy ha. Láthatja, hogy egy naplógyűjtő lehetősége.

Vagy kattintson a jobb gombbal a projektre a Solution Explorer **Application Insights konfigurálása**. Válassza ki a **nyomkövetési gyűjtésének konfigurálása** lehetőséget.

> [!NOTE]
> Nincs az Application Insights menüből vagy a napló gyűjtő lehetőség? Próbálja ki [hibaelhárítási](#troubleshooting).

## <a name="manual-installation"></a>Manuális telepítés
Ezt a módszert akkor használja, ha a projekt típusa nem támogatja az Application Insights-telepítő (például egy Windows asztali projekt).

1. Ha azt tervezi, a log4Net, NLog, vagy használja, telepítse a projektben.
2. A Megoldáskezelőben kattintson jobb gombbal a projektre, és válassza ki **NuGet-csomagok kezelése**.
3. Keressen a "Application Insights."
4. Válassza ki a következő csomagok egyikét:

   - A ILogger: [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights/)
[![NuGet](https://img.shields.io/nuget/vpre/Microsoft.Extensions.Logging.ApplicationInsights.svg)](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights/)
   - Az NLog: [Microsoft.ApplicationInsights.NLogTarget](https://www.nuget.org/packages/Microsoft.ApplicationInsights.NLogTarget/)
[![NuGet](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.NLogTarget.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.NLogTarget/)
   - A Log4Net: [Microsoft.ApplicationInsights.Log4NetAppender](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Log4NetAppender/)
[![NuGet](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.Log4NetAppender.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Log4NetAppender/)
   - A System.Diagnostics: [Microsoft.ApplicationInsights.TraceListener](https://www.nuget.org/packages/Microsoft.ApplicationInsights.TraceListener/)
[![NuGet](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.TraceListener.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.TraceListener/)
   - [Microsoft.ApplicationInsights.DiagnosticSourceListener](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener/)
[![NuGet](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.DiagnosticSourceListener.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener/)
   - [Microsoft.ApplicationInsights.EtwCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EtwCollector/)
[![NuGet](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.EtwCollector.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EtwCollector/)
   - [Microsoft.ApplicationInsights.EventSourceListener](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener/)
[![Nuget](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.EventSourceListener.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener/)

A NuGet csomag telepíti a szükséges szerelvényeket, és módosítja a web.config vagy az App.config fájlt, ha alkalmazható.

## <a name="ilogger"></a>ILogger

Az Application Insights ILogger megvalósítási használata konzolalkalmazással és az ASP.NET Core példákért lásd [naplózza a .NET Core ILogger ApplicationInsightsLoggerProvider](ilogger.md).

## <a name="insert-diagnostic-log-calls"></a>Szúrja be a diagnosztikai napló
Használja a System.Diagnostics.trace keretrendszert használja, ha egy tipikus hívást a következő lesz:

    System.Diagnostics.Trace.TraceWarning("Slow response - database01");

Log4net, NLog, vagy igény szerint használja:

    logger.Warn("Slow response - database01");

## <a name="use-eventsource-events"></a>EventSource események
Konfigurálható [System.Diagnostics.Tracing.EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) kell küldeni az Application Insights-nyomkövetéseket, eseményeket. Először telepítse a `Microsoft.ApplicationInsights.EventSourceListener` NuGet-csomagot. Szerkessze a `TelemetryModules` szakaszában a [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md) fájlt.

```xml
    <Add Type="Microsoft.ApplicationInsights.EventSourceListener.EventSourceTelemetryModule, Microsoft.ApplicationInsights.EventSourceListener">
      <Sources>
        <Add Name="MyCompany" Level="Verbose" />
      </Sources>
    </Add>
```

Az egyes források állíthatja be a következő paraméterekkel:
 * **Név** gyűjtéséhez az eseményforrás nevét adja meg.
 * **Szint** megadja a gyűjtése a naplózási szint: *Kritikus fontosságú*, *hiba*, *tájékoztató*, *LogAlways*, *részletes*, vagy *figyelmeztetés*.
 * **A kulcsszavak** (nem kötelező) adja meg a használandó kulcsszó kombinációk egész számú értékét.

## <a name="use-diagnosticsource-events"></a>DiagnosticSource események használata
Konfigurálható [System.Diagnostics.DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md) kell küldeni az Application Insights-nyomkövetéseket, eseményeket. Először telepítse a [ `Microsoft.ApplicationInsights.DiagnosticSourceListener` ](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener) NuGet-csomagot. A "Fájl TelemetryModules" szakasz, majd szerkessze a [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md) fájlt.

```xml
    <Add Type="Microsoft.ApplicationInsights.DiagnosticSourceListener.DiagnosticSourceTelemetryModule, Microsoft.ApplicationInsights.DiagnosticSourceListener">
      <Sources>
        <Add Name="MyDiagnosticSourceName" />
      </Sources>
    </Add>
```

A követni kívánt minden egyes DiagnosticSource, adjon hozzá egy bejegyzést, és a **neve** attribútum beállítása a DiagnosticSource nevére.

## <a name="use-etw-events"></a>ETW-események
Konfigurálhatja az Application Insights nyomkövetésként küldendő esemény-nyomkövetése Windows (ETW) eseményei. Először telepítse a `Microsoft.ApplicationInsights.EtwCollector` NuGet-csomagot. A "Fájl TelemetryModules" szakasz, majd szerkessze a [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md) fájlt.

> [!NOTE] 
> Ha a folyamat, az SDK-t futtató, amely tagja a Teljesítménynapló felhasználói vagy a rendszergazdák identitás alatt fut ETW-események csak lehessen gyűjteni.

```xml
    <Add Type="Microsoft.ApplicationInsights.EtwCollector.EtwCollectorTelemetryModule, Microsoft.ApplicationInsights.EtwCollector">
      <Sources>
        <Add ProviderName="MyCompanyEventSourceName" Level="Verbose" />
      </Sources>
    </Add>
```

Az egyes források állíthatja be a következő paraméterekkel:
 * **ProviderName** gyűjtéséhez az ETW-szolgáltató neve.
 * **SzolgáltatóGUID** gyűjtéséhez az ETW-szolgáltató GUID Azonosítóját adja. Helyett használható `ProviderName`.
 * **Szint** beállítja a naplózási szint gyűjtéséhez. Ez lehet *kritikus*, *hiba*, *tájékoztató*, *LogAlways*, *részletes*, vagy *Figyelmeztetés*.
 * **A kulcsszavak** (nem kötelező) állítsa be az egész értéket kulcsszó kombinációk használatára.

## <a name="use-the-trace-api-directly"></a>A nyomkövetés API-t közvetlenül használni
Az Application Insights nyomkövetés API-t hívhatja közvetlenül. A naplózás adapterek ezen API-val.

Példa:

    var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
    telemetry.TrackTrace("Slow response - database01");

TrackTrace előnye, hogy viszonylag hosszú adatok helyezheti az üzenetben. Hiba a POST data kódolása például is.

Az üzenethez is hozzáadhat egy súlyossági szintet. És egyéb telemetriát, például a tulajdonságértékeket szűrőt, vagy keressen a nyomkövetések más-más részhalmazához adhat hozzá. Példa:

    var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
    telemetry.TrackTrace("Slow database response",
                   SeverityLevel.Warning,
                   new Dictionary<string,string> { {"database", db.ID} });

Ez akkor lehetővé egyszerűen kiszűrhetők az [keresési] [ diagnostic] összes üzenetet egy adott súlyossági szint, amely egy adott adatbázishoz kapcsolódik.

## <a name="explore-your-logs"></a>Ismerje meg a naplók
Az alkalmazás hibakeresési módban futtatja, vagy élő telepítheti.

Az alkalmazás áttekintése panelen a [az Application Insights portálon][portal]válassza [keresési][diagnostic].

Akkor is, például:

* Nyomkövetési naplók vagy adott tulajdonságokkal rendelkező elemek szűrése.
* Vizsgálja meg az adott elem részletei.
* Keresse meg a többi rendszer naplóadatokat, hogy az ugyanazon felhasználói kérelem vonatkozik (az azonos Műveletazonosítóval rendelkezik).
* A konfiguráció az oldal mentése a Kedvencek közé.

> [!NOTE]
>Ha az alkalmazás nagy mennyiségű adatot küld el, és használja az Application Insights SDK-t az ASP.NET 2.0.0-beta3 vagy újabb, illetve a *adaptív mintavételezés* funkció is működnek, és csak egy része a telemetriai adatok küldése. [További tudnivalók a mintavételezésről.](../../azure-monitor/app/sampling.md)
>

## <a name="troubleshooting"></a>Hibaelhárítás
### <a name="how-do-i-do-this-for-java"></a>Hogyan készíthetek a Javához készült?
Használja a [Java log adapterek](../../azure-monitor/app/java-trace-logs.md).

### <a name="theres-no-application-insights-option-on-the-project-context-menu"></a>Nem az Application Insights beállítani a projekt helyi menüjében
* Győződjön meg arról, hogy az Application Insights Tools telepítve van-e a fejlesztői gépen. A Visual Studio **eszközök** > **bővítmények és frissítések**, keressen **az Application Insights Tools**. Ha a nem a **telepített** lap meg van nyitva a **Online** lapra, és telepítse azt.
* Ez lehet egy projekt típusa, amely az Application Insights Tools nem támogatja. Használat [manuális telepítés](#manual-installation).

### <a name="theres-no-log-adapter-option-in-the-configuration-tool"></a>Nem log adapter beállítani a konfigurációs eszköz
* Először telepítse a naplózási keretrendszer.
* Ha System.Diagnostics.Trace használ, győződjön meg arról, azt hogy [konfigurált *web.config*](https://msdn.microsoft.com/library/system.diagnostics.eventlogtracelistener.aspx).
* Győződjön meg arról, hogy az Application Insights a legújabb verzióra. A Visual Studióban nyissa meg **eszközök** > **bővítmények és frissítések**, és nyissa meg a **frissítések** fülre. Ha **Developer Analytics Tools** van, válassza ki, hogy frissítse azt.

### <a name="emptykey"></a>A "kialakítási kulcsot nem lehet üres" hibaüzenet jelenik meg:
Valószínűleg telepítve van a naplózás adapter Nuget-csomagot az Application Insights telepítése nélkül. A Megoldáskezelőben kattintson a jobb gombbal *ApplicationInsights.config*, és válassza ki **Update Application Insights**. Fogja kéri, jelentkezzen be az Azure-ba, és hozzon létre egy Application Insights-erőforrást vagy felhasználhatja egy már meglévőt. Amely kell kijavítani a problémát.

### <a name="i-can-see-traces-but-not-other-events-in-diagnostic-search"></a>Láthatom, nyomkövetéseket, de nem olyan eseményeket, a diagnosztikai keresés
Amíg az összes esemény és -kéréseinek keresztül is igénybe vehet.

### <a name="limits"></a>Mennyi adatot megmarad?
Számos tényező befolyásolja a megőrzött adatok mennyisége. További információkért lásd: a [korlátok](../../azure-monitor/app/api-custom-events-metrics.md#limits) szakaszának az ügyfél esemény metrikákat.

### <a name="i-dont-see-some-log-entries-that-i-expected"></a>Nem látom néhány, a várt bejegyzései
Ha az alkalmazás terjedelmes mennyiségű adatot küld el, és használja az Application Insights SDK-t az ASP.NET 2.0.0-beta3 vagy újabb, az adaptív mintavételezés funkció működjön, és csak egy része a telemetriai adatok küldése. [További tudnivalók a mintavételezésről.](../../azure-monitor/app/sampling.md)

## <a name="add"></a>Következő lépések

* [Diagnosztizálhatja a hibákat és kivételeket az ASP.NET-ben][exceptions]
* [További tudnivalók a keresés][diagnostic]
* [Állítsa be a rendelkezésre állás és a válaszképesség tesztek][availability]
* [Hibaelhárítás][qna]

<!--Link references-->

[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[exceptions]: asp-net-exceptions.md
[portal]: https://portal.azure.com/
[qna]: ../../azure-monitor/app/troubleshoot-faq.md
[start]: ../../azure-monitor/app/app-insights-overview.md