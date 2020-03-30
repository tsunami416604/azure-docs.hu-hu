---
title: A .NET nyomkövetési naplók felfedezése az Application Insightsban
description: A Trace, az NLog vagy a Log4Net által létrehozott keresési naplók.
ms.topic: conceptual
ms.date: 05/08/2019
ms.openlocfilehash: 273d5a2f4e1155541e159332312bdaa68aa175d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276269"
---
# <a name="explore-netnet-core-and-python-trace-logs-in-application-insights"></a>A .NET/.NET Core és a Python nyomkövetési naplóinak felfedezése az Application Insightsban

Diagnosztikai nyomkövetési naplók küldése a ASP.NET/ASP.NET Core alkalmazás iLogger, NLog, log4Net vagy System.Diagnostics.Trace az [Azure Application Insights.][start] Python-alkalmazások esetén küldjön diagnosztikai nyomkövetési naplókat az AzureLogHandler használatával az OpenCensus Python for Azure Monitor használatával. Ezután felfedezheti és keresheti őket. Ezek a naplók egyesülnek az alkalmazás többi naplófájljával, így azonosíthatja az egyes felhasználói kérelmekhez társított nyomkövetéseket, és összevetheti őket más eseményekkel és kivételjelentésekkel.

> [!NOTE]
> Szüksége van a naplórögzítő modulra? Ez egy hasznos adapter a harmadik féltől származó adatgyűjtők. Ha azonban még nem használja az NLog, a log4Net vagy a System.Diagnostics.Trace alkalmazást, fontolja meg az [**Application Insights TrackTrace()**](../../azure-monitor/app/api-custom-events-metrics.md#tracktrace) közvetlen meghívását.
>
>
## <a name="install-logging-on-your-app"></a>Naplózás telepítése az alkalmazásba
Telepítse a kiválasztott naplózási keretrendszert a projektbe, amely nek bejegyzést kell eredményeznie az app.config vagy a web.config fájlba.

```XML
 <configuration>
  <system.diagnostics>
    <trace>
      <listeners>
        <add name="myAppInsightsListener" type="Microsoft.ApplicationInsights.TraceListener.ApplicationInsightsTraceListener, Microsoft.ApplicationInsights.TraceListener" />
      </listeners>
    </trace>
  </system.diagnostics>
</configuration>
```

## <a name="configure-application-insights-to-collect-logs"></a>Az Application Insights konfigurálása naplók gyűjtésére
[Adja hozzá az Application Insights-ot a projekthez,](../../azure-monitor/app/asp-net.md) ha még nem tette meg. Megjelenik egy lehetőség a naplógyűjtő felvételére.

Vagy kattintson a jobb gombbal a projektre a Solution Explorer alkalmazásban az **Application Insights konfigurálásához.** Válassza a **Nyomkövetés gyűjtés konfigurálása** lehetőséget.

> [!NOTE]
> Nincs Application Insights menü vagy naplógyűjtő lehetőség? Próbálja meg [a hibaelhárítást.](#troubleshooting)

## <a name="manual-installation"></a>Manuális telepítés
Akkor használja ezt a módszert, ha a projekttípust az Application Insights telepítője (például egy asztali Windows-projekt) nem támogatja.

1. Ha a log4Net vagy az NLog használatát tervezi, telepítse azt a projektbe.
2. A Megoldáskezelőben kattintson a jobb gombbal a projektre, és válassza **a NuGet-csomagok kezelése parancsot.**
3. Keressen rá az "Application Insights" kifejezésre.
4. Válasszon az alábbi csomagok közül:

   - ILogger esetén: [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights/)
[![NuGet](https://img.shields.io/nuget/vpre/Microsoft.Extensions.Logging.ApplicationInsights.svg)](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights/)
   - Nlog esetén: [Microsoft.ApplicationInsights.NLogTarget](https://www.nuget.org/packages/Microsoft.ApplicationInsights.NLogTarget/)
[![NuGet](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.NLogTarget.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.NLogTarget/)
   - Log4Net esetén: [Microsoft.ApplicationInsights.Log4NetAppender](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Log4NetAppender/)
[![NuGet](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.Log4NetAppender.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Log4NetAppender/)
   - System.Diagnostics esetén: [Microsoft.ApplicationInsights.TraceListener](https://www.nuget.org/packages/Microsoft.ApplicationInsights.TraceListener/)
[![NuGet](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.TraceListener.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.TraceListener/)
   - [Microsoft.ApplicationInsights.DiagnosticSourceListener](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener/)
[![NuGet](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.DiagnosticSourceListener.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener/)
   - [Microsoft.ApplicationInsights.EtwCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EtwCollector/)
[![NuGet](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.EtwCollector.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EtwCollector/)
   - [Microsoft.ApplicationInsights.EventSourceListener](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener/)
[![Nuget](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.EventSourceListener.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener/)

A NuGet csomag telepíti a szükséges szerelvényeket, és módosítja a web.config vagy app.config programot, ha ez alkalmazható.

## <a name="ilogger"></a>ILogger

Példák az Application Insights ILogger implementáció konzolalkalmazásokkal és ASP.NET Core használatával, [lásd: ApplicationInsightsLoggerProvider for .NET Core ILogger logók](ilogger.md).

## <a name="insert-diagnostic-log-calls"></a>Diagnosztikai naplóhívások beszúrása
Ha a System.Diagnostics.Trace-et használja, egy tipikus hívás a következő lehet:

    System.Diagnostics.Trace.TraceWarning("Slow response - database01");

Ha a log4netet vagy az NLog-t részesíti előnyben, használja a következőket:

    logger.Warn("Slow response - database01");

## <a name="use-eventsource-events"></a>EventSource-események használata
Konfigurálhatja [a System.Diagnostics.Tracing.EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) eseményeket az Application Insights nak nyomkövetésként való elküldésre. Először telepítse `Microsoft.ApplicationInsights.EventSourceListener` a NuGet csomagot. Ezután szerkeszti az `TelemetryModules` [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md) fájl szakaszát.

```xml
    <Add Type="Microsoft.ApplicationInsights.EventSourceListener.EventSourceTelemetryModule, Microsoft.ApplicationInsights.EventSourceListener">
      <Sources>
        <Add Name="MyCompany" Level="Verbose" />
      </Sources>
    </Add>
```

Az egyes forrásokhoz a következő paramétereket állíthatja be:
 * **A név** az összegyűjtendő EventSource nevét adja meg.
 * **A Szint** a következő naplózási szintet adja meg: *Kritikus*, *Hiba*, *Tájékoztató*, *LogAlways*, *Részletes*vagy *Figyelmeztetés*.
 * **A kulcsszavak** (nem kötelező) a használandó kulcsszókombinációk egész értékét határozzák meg.

## <a name="use-diagnosticsource-events"></a>DiagnosticSource-események használata
Konfigurálhatja [system.Diagnostics.DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md) eseményeket kell küldeni az Application Insights nyomkövetésként. Először telepítse [`Microsoft.ApplicationInsights.DiagnosticSourceListener`](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener) a NuGet csomagot. Ezután szerkesztse az [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md) fájl "TelemettryModules" szakaszát.

```xml
    <Add Type="Microsoft.ApplicationInsights.DiagnosticSourceListener.DiagnosticSourceTelemetryModule, Microsoft.ApplicationInsights.DiagnosticSourceListener">
      <Sources>
        <Add Name="MyDiagnosticSourceName" />
      </Sources>
    </Add>
```

Minden nyomon követni kívánt diagnosztikai forráshoz adjon hozzá egy bejegyzést, amelynek **Name** attribútuma a DiagnosticSource nevéhez van beállítva.

## <a name="use-etw-events"></a>ETW-események használata
Beállíthatja, hogy a Windows (ETW) események események ettrace nyomkövetésként kell küldeni. Először telepítse `Microsoft.ApplicationInsights.EtwCollector` a NuGet csomagot. Ezután szerkesztse az [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md) fájl "TelemettryModules" szakaszát.

> [!NOTE] 
> EtW-események csak akkor gyűjthetők, ha az SDK-t tartalmazó folyamat olyan identitás alatt fut, amely a Teljesítménynapló-felhasználók vagy a rendszergazdák tagja.

```xml
    <Add Type="Microsoft.ApplicationInsights.EtwCollector.EtwCollectorTelemetryModule, Microsoft.ApplicationInsights.EtwCollector">
      <Sources>
        <Add ProviderName="MyCompanyEventSourceName" Level="Verbose" />
      </Sources>
    </Add>
```

Az egyes forrásokhoz a következő paramétereket állíthatja be:
 * **ProviderName** a begyűjtendő ETW-szolgáltató neve.
 * **A ProviderGuid** a begyűjtő ETW-szolgáltató GUID azonosítóját adja meg. Ezt fel lehet `ProviderName`használni helyett .
 * **Szint** határozza meg a naplózási szintet gyűjteni. Ez lehet *kritikus*, *hiba*, *információs*, *LogAlways*, *részletes,* vagy *figyelmeztetés*.
 * **A kulcsszavak** (nem kötelező) a használandó kulcsszókombinációk egész értékét határozzák meg.

## <a name="use-the-trace-api-directly"></a>A Trace API közvetlen használata
Az Application Insights trace API-t közvetlenül hívhatja meg. A naplózási adapterek ezt az API-t használják.

Példa:

    var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
    telemetry.TrackTrace("Slow response - database01");

A TrackTrace előnye, hogy viszonylag hosszú adatokat helyezhet el az üzenetben. Itt is kódolhatja a POST-adatokat.

Súlyossági szintet is hozzáadhat az üzenethez. És, mint más telemetriai adatok, tulajdonságértékeket adhat hozzá, hogy segítsen szűrni vagy keresni a különböző nyomkövetések. Példa:

    var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
    telemetry.TrackTrace("Slow database response",
                   SeverityLevel.Warning,
                   new Dictionary<string,string> { {"database", db.ID} });

Ez lehetővé tenné, hogy könnyen kiszűrje a [Keresésben][diagnostic] az adott súlyossági szinthez kapcsolódó összes üzenetet, amely egy adott adatbázishoz kapcsolódik.

## <a name="azureloghandler-for-opencensus-python"></a>AzureLogHandler az OpenCensus Pythonhoz
Az Azure Monitor loghandler lehetővé teszi, hogy az Azure-figyelő naplóinak exportálása.

Az alkalmazás instrumental az [OpenCensus Python SDK](../../azure-monitor/app/opencensus-python.md) az Azure Monitorhoz.

Ebben a példában bemutatja, hogyan küldhet figyelmeztetőszintű naplót az Azure Monitornak.

```python
import logging

from opencensus.ext.azure.log_exporter import AzureLogHandler

logger = logging.getLogger(__name__)
logger.addHandler(AzureLogHandler(connection_string='InstrumentationKey=<your-instrumentation_key-here>'))
logger.warning('Hello, World!')
```

## <a name="explore-your-logs"></a>Fedezze fel a naplókat
Futtassa az alkalmazást hibakeresési módban, vagy telepítse élőben.

Az alkalmazás áttekintő ablaktábláján [az Application Insights portálon][portal]válassza a [Keresés lehetőséget.][diagnostic]

Például:

* Szűrje a naplónyomkövetésekre vagy a meghatározott tulajdonságokkal rendelkező elemekre.
* Egy adott elem részletes vizsgálata.
* Keresse meg az ugyanahhoz a felhasználói kérelemhez kapcsolódó egyéb rendszernapló-adatokat (ugyanaz a OperationId azonosítóval rendelkezik).
* Az oldal konfigurációjának mentése kedvencként.

> [!NOTE]
>Ha az alkalmazás sok adatot küld, és az Application Insights SDK-t használja ASP.NET 2.0.0-beta3 vagy újabb verziójához, az *adaptív mintavételezési* szolgáltatás működhet, és a telemetriai adatoknak csak egy részét küldheti el. [További tudnivalók a mintavételezésről.](../../azure-monitor/app/sampling.md)
>

## <a name="troubleshooting"></a>Hibaelhárítás
### <a name="how-do-i-do-this-for-java"></a>Hogyan tehetem ezt meg a Java?
Használja a [Java naplóadaptereket](../../azure-monitor/app/java-trace-logs.md).

### <a name="theres-no-application-insights-option-on-the-project-context-menu"></a>Nincs Application Insights lehetőség a projekt helyi menüjében
* Győződjön meg arról, hogy a Fejlesztői elemzési eszközök telepítve vannak a fejlesztői gépen. A Visual Studio **Tools** > **bővítményei és frissítései**lapon keresse meg a **Fejlesztői elemzési eszközök lehetőséget.** Ha nem található a **Telepített** lapon, nyissa meg az **Online** lapot, és telepítse azt.
* Ez lehet egy olyan projekttípus, amelyet a Devloper Analytics Tools nem támogat. Használja [a kézi telepítést](#manual-installation).

### <a name="theres-no-log-adapter-option-in-the-configuration-tool"></a>Nincs log adapter opció a konfigurációs eszközben
* Először telepítse a naplózási keretrendszert.
* Ha a System.Diagnostics.Trace programot használja, győződjön meg arról, hogy konfigurálva van [a *web.config*](https://msdn.microsoft.com/library/system.diagnostics.eventlogtracelistener.aspx).
* Győződjön meg arról, hogy az Application Insights legújabb verziójával rendelkezik. A Visual Studióban nyissa meg az **Eszközök** > **bővítmények és -frissítések**lapot, és nyissa meg a **Frissítések** lapot. Ha **a Developer Analytics Tools** is található, jelölje ki a frissítéshez.

### <a name="i-get-the-instrumentation-key-cannot-be-empty-error-message"></a><a name="emptykey"></a>A "Instrumentation key cannot cannot empty" hibaüzenet jelenik meg
Valószínűleg az Application Insights telepítése nélkül telepítette a Nuget naplózási adaptercsomagot. A Megoldáskezelőben kattintson a jobb gombbal *az ApplicationInsights.config fájlra,* és válassza **az Application Insights frissítése parancsot.** A rendszer kéri, hogy jelentkezzen be az Azure-ba, és hozzon létre egy Application Insights-erőforrást, vagy használjon fel egy meglévőt. Ez majd megoldja a problémát.

### <a name="i-can-see-traces-but-not-other-events-in-diagnostic-search"></a>Látom a nyomokat, de más eseményeket nem a diagnosztikai keresésben
Eltarthat egy ideig, amíg az összes esemény és kérés átjut a folyamaton.

### <a name="how-much-data-is-retained"></a><a name="limits"></a>Mennyi adatot őriz meg a rendszer?
Számos tényező befolyásolja a megőrzött adatok mennyiségét. További információ: Az ügyfélesemény-mérőszámok oldal [korlátok](../../azure-monitor/app/api-custom-events-metrics.md#limits) szakasza.

### <a name="i-dont-see-some-log-entries-that-i-expected"></a>Nem látok a várt naplóbejegyzéseket
Ha az alkalmazás terjedelmes mennyiségű adatot küld, és az Application Insights SDK-t használja ASP.NET 2.0.0-beta3 vagy újabb verziójához, az adaptív mintavételezési szolgáltatás működhet, és a telemetriai adatoknak csak egy részét küldheti el. [További tudnivalók a mintavételezésről.](../../azure-monitor/app/sampling.md)

## <a name="next-steps"></a><a name="add"></a>További lépések

* [Hibák és kivételek diagnosztizálása ASP.NET][exceptions]
* [További információ a keresésről][diagnostic]
* [Rendelkezésre állási és válaszidő-tesztek beállítása][availability]
* [hibaelhárítással][qna]

<!--Link references-->

[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[exceptions]: asp-net-exceptions.md
[portal]: https://portal.azure.com/
[qna]: ../../azure-monitor/app/troubleshoot-faq.md
[start]: ../../azure-monitor/app/app-insights-overview.md
