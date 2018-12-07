---
title: Az Azure Application Insights – függőségi az automatikus gyűjtés |} A Microsoft Docs
description: Az Application Insights automatikusan gyűjtheti és függőségek vizualizálása
services: application-insights
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.topic: reference
ms.date: 10/16/2018
ms.author: mbullwin
ms.openlocfilehash: 76cdd4bd4d95c7c11906f9c02a0f5cfa2326390e
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2018
ms.locfileid: "53000779"
---
# <a name="application-insights-nuget-packages"></a>Application Insights NuGet-csomagok

Alább látható stabil kiadási NuGet-csomagok az Application Insights jelenlegi listája.

## <a name="common-packages-for-aspnet"></a>Közös csomagok ASP.NET-hez

| Csomag neve | Poslední stabilní Verze. | Leírás | Letöltés |
|-------------------------------|-----------------------|------------|----|
| Microsoft.ApplicationInsights | 2.8.0 | Application Insights telemetriai adatok minden típusának továbbításának alapvető funkciókat biztosít, és más Application Insights-csomagokat a függő csomag | [Csomag letöltése](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) |
|Microsoft.ApplicationInsights.Agent.Intercept | 2.4.0 | Lehetővé teszi a metódushívásokat megszerzésének | [Csomag letöltése](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Agent.Intercept/) |
| Microsoft.ApplicationInsights.DependencyCollector | 2.8.0 | Application Insights függőségi gyűjtő a .NET-alkalmazásokban. A és a függő csomag egy Application Insights platformspecifikus csomagok automatikus gyűjtését, a függőségek telemetriáját. | [Csomag letöltése](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector/) |
| Microsoft.ApplicationInsights.PerfCounterCollector | 2.8.0 | Application Insights teljesítmény számlálók gyűjtő lehetővé teszi, hogy küldjön az Application Insightsba teljesítményszámlálói által gyűjtött adatokat. | [Csomag letöltése](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector/) |
| Microsoft.ApplicationInsights.Web | 2.8.0 | .NET-hez készült Application Insights webes alkalmazások | [Csomag letöltése](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) |
| Microsoft.ApplicationInsights.WindowsServer | 2.8.0 | Application Insights Windows Server NuGet-csomag automatikus gyűjtését az application insights telemetria a .NET-alkalmazásokban biztosít. Ez a csomag használható az Application Insights platformspecifikus csomagokat egy függő csomagot vagy a .NET-alkalmazásokban, amelyek nem tartoznak platformspecifikus csomagokat (például a .NET-feldolgozói szerepkörök) egy különálló csomagként. | [Csomag letöltése](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/)  
| Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel | 2.8.0 | Révén a telemetria az Application Insights Windows Server SDK, amely megőrzi az telemetriát, offline forgatókönyvekben. | [Csomag letöltése](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel/) |

## <a name="common-packages-for-aspnet-core"></a>ASP.NET Core közös csomagjai

| Csomag neve | Poslední stabilní Verze. | Leírás | Letöltés |
|-------------------------------|-----------------------|------------|----|
| Microsoft.ApplicationInsights.AspNetCore | 2.5.0 | Application Insights az ASP.NET Core webes alkalmazások. | [Csomag letöltése](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore/) |
| Microsoft.ApplicationInsights | 2.8.0 | Ez a csomag számára az átvitelhez az Application Insights telemetriai adatok minden típusának alapvető funkciókat biztosít, és más Application Insights-csomagokat a függő csomag | [Csomag letöltése](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) |
| Microsoft.ApplicationInsights.DependencyCollector | 2.8.0 | Application Insights függőségi gyűjtő a .NET-alkalmazásokban. A és a függő csomag egy Application Insights platformspecifikus csomagok automatikus gyűjtését, a függőségek telemetriáját. | [Csomag letöltése](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector/) |
| Microsoft.ApplicationInsights.PerfCounterCollector | 2.8.0 | Application Insights teljesítmény számlálók gyűjtő lehetővé teszi, hogy küldjön az Application Insightsba teljesítményszámlálói által gyűjtött adatokat. | [Csomag letöltése](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector/) |
| Microsoft.ApplicationInsights.WindowsServer | 2.8.0 | Application Insights Windows Server NuGet-csomag automatikus gyűjtését az application insights telemetria a .NET-alkalmazásokban biztosít. Ez a csomag használható az Application Insights platformspecifikus csomagokat egy függő csomagot vagy a .NET-alkalmazásokban, amelyek nem tartoznak platformspecifikus csomagokat (például a .NET-feldolgozói szerepkörök) egy különálló csomagként. | [Csomag letöltése](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/)  |
| Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel | 2.8.0 | Révén a telemetria az Application Insights Windows Server SDK, amely megőrzi az telemetriát, offline forgatókönyvekben. | [Csomag letöltése](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel/) |

## <a name="listenerscollectorsappenders"></a>Figyelők/naplógyűjtők/appenders

| Csomag neve | Poslední stabilní Verze. | Leírás | Letöltés |
|-------------------------------|-----------------------|------------|----|
| Microsoft.ApplicationInsights.DiagnosticSourceListener | 2.7.2 |  Lehetővé teszi, hogy a DiagnosticSource eseménye továbbítását az Application Insightsba. | [Csomag letöltése](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener/) |
| Microsoft.ApplicationInsights.EventSourceListener | 2.7.2 | Application Insights EventSourceListener lehetővé teszi, hogy az adatok küldését EventSource eseményeket az Application Insightsba. | [Csomag letöltése](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener/) |
| Microsoft.ApplicationInsights.EtwCollector | 2.7.2 | Application Insights EtwCollector lehetővé teszi, hogy az esemény-nyomkövetése Windows (ETW) adatok küldése az Application Insightsba. | [Csomag letöltése](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EtwCollector/) |
| Microsoft.ApplicationInsights.TraceListener | 2.7.2 | Egyéni TraceListener, amely lehetővé teszi a nyomkövetési napló üzenetek küldése az Application Insights. | [Csomag letöltése](https://www.nuget.org/packages/Microsoft.ApplicationInsights.TraceListener/) |
| Microsoft.ApplicationInsights.Log4NetAppender | 2.7.2 | Egy egyéni naplóírói Log4Net naplózási üzenetek küldése az Application Insights lehetővé teszi. | [Csomag letöltése](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Log4NetAppender/)
| Microsoft.ApplicationInsights.NLogTarget | 2.7.2 |  egy egyéni cél NLog naplózási üzenetek küldése az Application Insights lehetővé teszi. | [Csomag letöltése](https://www.nuget.org/packages/Microsoft.ApplicationInsights.NLogTarget/)
| Microsoft.ApplicationInsights.SnapshotCollector | 1.3.1 | Az alkalmazás figyeli, és automatikusan összegyűjti a kapcsolat nélküli elemzéshez pillanatképeket. | [Csomag letöltése](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/)

## <a name="service-fabric"></a>Service Fabric

| Csomag neve | Poslední stabilní Verze. | Leírás | Letöltés |
|-------------------------------|-----------------------|------------|----|
| Microsoft.ApplicationInsights.ServiceFabric | 2.2.0 | Ez a csomag biztosít futtatja a telemetria a service fabric-környezet az alkalmazás az automatikus decoration. Ne használja a NuGet natív Service Fabric-alkalmazásokat. | [Csomag letöltése](https://www.nuget.org/packages/Microsoft.ApplicationInsights.ServiceFabric/) |
| Microsoft.ApplicationInsights.ServiceFabric.Native | 2.2.0 | Application Insights-modult a service fabric-alkalmazások. A nugettel csak a natív Service Fabric-alkalmazásokat. Tárolókban futó alkalmazások esetében használhatja Microsoft.ApplicationInsights.ServiceFabric csomagot. | [Csomag letöltése](https://www.nuget.org/packages/Microsoft.ApplicationInsights.ServiceFabric.Native/) |  

## <a name="status-monitor"></a>A figyelő állapota

| Csomag neve | Poslední stabilní Verze. | Leírás | Letöltés |
|-------------------------------|-----------------------|------------|----|
| Microsoft.ApplicationInsights.Agent_x64 | 2.2.1 |  Lehetővé teszi, hogy a futásidejű adatok gyűjtése x64 alkalmazások | [Csomag letöltése](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Agent_x64/) |
| Microsoft.ApplicationInsights.Agent_x86 | 2.2.1 |  Lehetővé teszi, hogy a futásidejű adatok gyűjtése x86 az alkalmazásokat. | [Csomag letöltése](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Agent_x86/) |

Ezek a csomagok futásidejű megfigyelése a legfontosabb funkcióit részét alkotó [Állapotfigyelőt](app-insights-monitor-performance-live-website-now.md). Nem kell közvetlenül letöltheti ezeket a csomagokat, használja az állapotfigyelő telepítőjét. Ha szeretné megtudni, további információ arról, hogy ezen csomagok dolgozhat technikai részletek [blogbejegyzés](https://apmtips.com/blog/2016/11/18/how-application-insights-status-monitor-not-monitors-dependencies/) fejlesztők egyik remek kezdőpont van.

## <a name="additional-packages"></a>További csomagok

| Csomag neve | Poslední stabilní Verze. | Leírás | Letöltés |
|-------------------------------|-----------------------|------------|----|
| Microsoft.ApplicationInsights.AzureWebSites | 2.6.5 | Ez a bővítmény lehetővé teszi, hogy az Application Insights-figyelést egy Azure App Service szolgáltatásban. SDK verziója 2.6.1. Utasításokat: Adja hozzá a rendszerállapotkulcsot "Állítani az APPINSIGHTS_INSTRUMENTATIONKEY" alkalmazásbeállításokat, és indítsa újra a webapp-érvénybe.| [Csomag letöltése](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AzureWebSites/) |
| Microsoft.ApplicationInsights.Injector | 2.6.7 | Ez a csomag adatfolyamat létrehozása kód nélküli Application Insights-injektálási szükséges fájlok találhatók. | [Csomag letöltése](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Injector/) |

## <a name="next-steps"></a>További lépések

- A figyelő [ASP.NET Core](app-insights-asp-net-core.md).
- Profiljának ASP.NET Core [Azure-beli Linuxos web apps](app-insights-profiler-aspnetcore-linux.md).
- Az ASP.NET Debug [pillanatképek](app-insights-snapshot-debugger.md).