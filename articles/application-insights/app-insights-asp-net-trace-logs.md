---
title: .NET nyomkövetési naplók megtekintése az Application Insights felfedezése
description: Keresse meg a nyomkövetési, NLog és Log4Net létrehozott naplók.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 0c2a084f-6e71-467b-a6aa-4ab222f17153
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 05/03/2017
ms.author: mbullwin
ms.openlocfilehash: 574b11f9ba38bda775610f2f9e90fbb2d2b05868
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2018
---
# <a name="explore-net-trace-logs-in-application-insights"></a>.NET nyomkövetési naplók megtekintése az Application Insights felfedezése
Ha NLog, a log4Net, vagy a System.Diagnostics.Trace a diagnosztikai nyomkövetés az ASP.NET-alkalmazás lehet küldeni a naplókat [Azure Application Insights][start], ahol vizsgálatát, és keresse meg őket. A naplók az alkalmazásból érkező, hogy azonosítsa a nyomkövetési adatokat társított minden egyes felhasználói kérelem karbantartás, és a kivizsgált más események és a kivétel jelentések más telemetriai adatok egyesül.

> [!NOTE]
> A rögzítési naplómoduljának kell? A 3. fél figyelő szoftverek hasznos adaptert, de ha már nem használt NLog, log4Net, vagy a System.Diagnostics.Trace, fontolja meg a csak hívó [Application Insights TrackTrace()](app-insights-api-custom-events-metrics.md#tracktrace) közvetlenül.
>
>

## <a name="install-logging-on-your-app"></a>Bejelentkezés az alkalmazás telepítése
A kiválasztott naplózási keretrendszer telepítése a projektben. Ennek eredménye egy app.config vagy a web.config bejegyzése.

Ha System.Diagnostics.Trace használ, adjon hozzá egy bejegyzést a Web.config fájlba szeretné:

```XML

    <configuration>
     <system.diagnostics>
       <trace autoflush="false" indentsize="4">
         <listeners>
           <add name="myListener"
             type="System.Diagnostics.TextWriterTraceListener"
             initializeData="TextWriterOutput.log" />
           <remove name="Default" />
         </listeners>
       </trace>
     </system.diagnostics>
   </configuration>
```
## <a name="configure-application-insights-to-collect-logs"></a>Az Application Insights gyűjtött naplók konfigurálása
**[Az Application Insights hozzáadása a projekthez](app-insights-asp-net.md)**  még ezt nem tette meg, ha. Megjelenik egy lehetőség, hogy a naplógyűjtő tartalmazza.

Vagy **konfigurálja az Application Insights** kattintson a jobb gombbal a projektben a Megoldáskezelőre. Jelölje be a **gyűjtemény konfigurálása**.

*Nincs Application Insights menü vagy a napló adatgyűjtő lehetőség?* Próbálja [hibaelhárítási](#troubleshooting).

## <a name="manual-installation"></a>Manuális telepítés
Akkor használja ezt a módszert, ha a projekt típusa nem támogatott az Application Insights telepítővel (például a Windows asztali projekt).

1. Ha azt tervezi, a log4Net, vagy a NLog, telepítse a projektet.
2. A Megoldáskezelőben kattintson jobb gombbal a projektre, és válassza a **NuGet-csomagok kezelése**.
3. Az „Application Insights” kifejezés keresése
4. Válassza ki a megfelelő csomag - egyikét:

   * Microsoft.ApplicationInsights.TraceListener (System.Diagnostics.Trace hívások rögzítéséhez)
   * (Az EventSource események rögzítése) Microsoft.ApplicationInsights.EventSourceListener
   * (Az ETW-események rögzítése) Microsoft.ApplicationInsights.EtwListener
   * Microsoft.ApplicationInsights.NLogTarget
   * Microsoft.ApplicationInsights.Log4NetAppender

A NuGet csomag telepíti a szükséges szerelvényeket, valamint módosítja a web.config vagy az App.config fájlt.

## <a name="insert-diagnostic-log-calls"></a>Helyezze be a diagnosztikai naplófájl hívások
Ha a System.Diagnostics.Trace használatához tipikus hívás lenne:

    System.Diagnostics.Trace.TraceWarning("Slow response - database01");

Ha inkább log4net vagy NLog:

    logger.Warn("Slow response - database01");

## <a name="using-eventsource-events"></a>EventSource eseményeket használ
Konfigurálható [System.Diagnostics.Tracing.EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) eseményeket az Application Insights nyomkövetési adatokat, küldendő. Először telepítse a `Microsoft.ApplicationInsights.EventSourceListener` NuGet-csomagot. Szerkessze `TelemetryModules` szakasza a [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md) fájlt.

```xml
    <Add Type="Microsoft.ApplicationInsights.EventSourceListener.EventSourceTelemetryModule, Microsoft.ApplicationInsights.EventSourceListener">
      <Sources>
        <Add Name="MyCompany" Level="Verbose" />
      </Sources>
    </Add>
```

Az egyes források állíthatók be a következő paraméterekkel:
 * `Name` Adja meg a gyűjtendő EventSource neve.
 * `Level` Adja meg a naplózási szint gyűjtéséhez. Egyike lehet `Critical`, `Error`, `Informational`, `LogAlways`, `Verbose`, `Warning`.
 * `Keywords` (Nem kötelező) adja meg a kulcsszavak kombinációk használni az egész értéket.

## <a name="using-diagnosticsource-events"></a>DiagnosticSource eseményeket használ
Konfigurálható [System.Diagnostics.DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md) eseményeket az Application Insights nyomkövetési adatokat, küldendő. Először telepítse a [ `Microsoft.ApplicationInsights.DiagnosticSourceListener` ](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener) NuGet-csomagot. Szerkessze a `TelemetryModules` szakasza a [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md) fájlt.

```xml
    <Add Type="Microsoft.ApplicationInsights.DiagnosticSourceListener.DiagnosticSourceTelemetryModule, Microsoft.ApplicationInsights.DiagnosticSourceListener">
      <Sources>
        <Add Name="MyDiagnosticSourceName" />
      </Sources>
    </Add>
```

Az egyes DiagnosticSource követni kívánt, adja hozzá a bejegyzés a `Name` attribútum értékének beállítása a DiagnosticSource nevét.

## <a name="using-etw-events"></a>Használja az ETW-események
Application Insights nyomkövetési adatokat, küldendő ETW-események konfigurálása Először telepítse a `Microsoft.ApplicationInsights.EtwCollector` NuGet-csomagot. Szerkessze `TelemetryModules` szakasza a [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md) fájlt.

> [!NOTE] 
> ETW-események csak be kell, ha az SDK-t tartalmazó folyamat, amely tagja az "Teljesítménynapló felhasználói" vagy a rendszergazdák identitás alatt fut.

```xml
    <Add Type="Microsoft.ApplicationInsights.EtwCollector.EtwCollectorTelemetryModule, Microsoft.ApplicationInsights.EtwCollector">
      <Sources>
        <Add ProviderName="MyCompanyEventSourceName" Level="Verbose" />
      </Sources>
    </Add>
```

Az egyes források állíthatók be a következő paraméterekkel:
 * `ProviderName` a gyűjtendő ETW-szolgáltató neve van.
 * `ProviderGuid` Adja meg a gyűjteni kívánt ETW-szolgáltató GUID helyett használható `ProviderName`.
 * `Level` Beállítja a naplózási szint gyűjtéséhez. Egyike lehet `Critical`, `Error`, `Informational`, `LogAlways`, `Verbose`, `Warning`.
 * `Keywords` (Nem kötelező) kulcsszó kombinációk használni az egész értéket állítja be.

## <a name="using-the-trace-api-directly"></a>A nyomkövetés API közvetlen használatával
Hívása az Application Insights nyomkövetési API közvetlenül. A naplózási adapterek Ez az API használnak.

Példa:

    var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
    telemetry.TrackTrace("Slow response - database01");

TrackTrace előnye, hogy viszonylag hosszú adatok helyezhetik az üzenetben. Például sikerült kódolni nincs POST-adatokat.

Emellett egy súlyossági szintet adhat hozzá az üzenetet. És egyéb telemetriai adatok, például hozzáadhat, amelyek segítségével szűrőt, vagy keressen a nyomkövetési más-más részhalmazához. Példa:

    var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
    telemetry.TrackTrace("Slow database response",
                   SeverityLevel.Warning,
                   new Dictionary<string,string> { {"database", db.ID} });

Ez lehetővé tenné, hogy a [keresési][diagnostic], az adott adatbázishoz vonatkozó adott súlyossági szintet az üzenetek egyszerűen kiszűrésére.

## <a name="explore-your-logs"></a>A naplók felfedezés
Futtassa az alkalmazást, vagy a hibakeresési módban, vagy telepítheti élő.

Az alkalmazás áttekintése panelen a [az Application Insights portáljáról][portal], válassza a [keresési][diagnostic].

![Az Application insights részére válassza ki a keresés](./media/app-insights-asp-net-trace-logs/020-diagnostic-search.png)

![Keresés](./media/app-insights-asp-net-trace-logs/10-diagnostics.png)

Akkor is, például:

* A naplókivonatokat, vagy az adott tulajdonságokkal rendelkező elemek szűrése
* Egy adott cikk részletesen vizsgálhatja meg.
* Található más felhasználó kérésben vonatkozó telemetriai adatokat (Ez azt jelenti, hogy az azonos OperationID azonosítójú rendelkező)
* Ezen a lapon konfigurációjának mentése a Kedvencek közé

> [!NOTE]
> **Mintavételi.** Ha az alkalmazása sok adatot küld, és az Application Insights SDK-t az ASP.NET 2.0.0-beta3 vagy újabb verziójához használja, működhet az adaptív mintavételezés funkció és lehet, hogy csak a telemetria valamely százalékát küldi el. [További tudnivalók a mintavételezésről.](app-insights-sampling.md)
>
>

## <a name="next-steps"></a>További lépések
[Diagnosztizálja a hibákat és kivételeket az ASP.NET][exceptions]

[További információ a keresési][diagnostic].

## <a name="troubleshooting"></a>Hibaelhárítás
### <a name="how-do-i-do-this-for-java"></a>Hogyan ez Java?
Használja a [Java napló adapterek](app-insights-java-trace-logs.md).

### <a name="theres-no-application-insights-option-on-the-project-context-menu"></a>A projekt helyi menüben nincs Application Insights lehetőség
* Ellenőrizze, hogy az Application Insights-eszközök telepítve van a fejlesztési számítógépen. A Visual Studio menüjében eszközök bővítmények és frissítések, keresse meg az Application Insights-eszközökkel. Ha nem, akkor a telepített lap, nyissa meg az Online lapot, és telepítse.
* Ez a projekt Application Insights-eszközök által nem támogatott típusú lehet. Használjon [manuális telepítés](#manual-installation).

### <a name="no-log-adapter-option-in-the-configuration-tool"></a>A kiszolgálókonfigurációs eszköz nincs napló adapter lehetőség
* A naplózási keretrendszer először telepítenie kell.
* Ha a System.Diagnostics.Trace használata esetén ellenőrizze, hogy [legyen konfigurálva `web.config` ](https://msdn.microsoft.com/library/system.diagnostics.eventlogtracelistener.aspx).
* Rendelkezik készült Application Insights legújabb verzióját? A Visual Studio **eszközök** menüben válasszon **bővítmények és frissítések**, és nyissa meg a **frissítések** lapon. Fejlesztői elemzőeszközök nincs, kattintson a frissítést.

### <a name="emptykey"></a>Hiba jelenik meg: "Instrumentation kulcs nem lehet üres"
A jelek szerint a naplózás adapter Nuget-csomagot telepítette az Application Insights telepítése nélkül.

A Megoldáskezelőben kattintson a jobb gombbal `ApplicationInsights.config` válassza **frissítés az Application Insights**. Olyan párbeszédpanel, amely felkéri, hogy jelentkezzen be az Azure-bA kap, és létrehozza az Application Insights-erőforrást, vagy használja ismét egy meglévőt. Ez segít kell azt.

### <a name="i-can-see-traces-in-diagnostic-search-but-not-the-other-events"></a>Láthatom valahol mappában lévő diagnosztikai keresési, de nem tartozó más események
Azt is néha időigényes az összes olyan események és kérelmek lekérni a feldolgozási folyamaton keresztül.

### <a name="limits"></a>Mennyi adatot megmarad?
Számos tényező befolyásolja a megőrzött adatok mennyiségét. Tekintse meg a [korlátok](app-insights-api-custom-events-metrics.md#limits) szakasz az ügyfél esemény metrikák oldal további információt. 

### <a name="im-not-seeing-some-of-the-log-entries-that-i-expect"></a>Nem látható az egyes a naplóbejegyzéseket, amelyeket a várt
Ha az alkalmazása sok adatot küld, és az Application Insights SDK-t az ASP.NET 2.0.0-beta3 vagy újabb verziójához használja, működhet az adaptív mintavételezés funkció és lehet, hogy csak a telemetria valamely százalékát küldi el. [További tudnivalók a mintavételezésről.](app-insights-sampling.md)

## <a name="add"></a>Következő lépések
* [Rendelkezésre állási és reakcióidőt tesztek beállítása][availability]
* [Hibaelhárítás][qna]

<!--Link references-->

[availability]: app-insights-monitor-web-app-availability.md
[diagnostic]: app-insights-diagnostic-search.md
[exceptions]: app-insights-asp-net-exceptions.md
[portal]: https://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[start]: app-insights-overview.md
