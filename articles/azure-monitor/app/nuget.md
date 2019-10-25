---
title: Azure Application Insights – függőségi automatikus adatgyűjtés | Microsoft Docs
description: A függőségek automatikus összegyűjtése és megjelenítése Application Insights
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: reference
author: mrbullwinkle
ms.author: mbullwin
ms.date: 10/16/2018
ms.openlocfilehash: 1d3259c32de7f937d4e5ac8e21c8cab60b9cc635
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2019
ms.locfileid: "72819171"
---
# <a name="application-insights-nuget-packages"></a>Application Insights NuGet-csomagok

Alább látható az Application Insights stabil kiadási NuGet csomagjainak aktuális listája.

## <a name="common-packages-for-aspnet"></a>Általános csomagok a ASP.NET

| Csomag neve | Stabil verzió | Leírás | Letöltés |
|-------------------------------|-----------------------|------------|----|
| Microsoft. ApplicationInsights | 2.8.0 | Alapvető funkciókat biztosít az összes Application Insights Telemetria-típus átviteléhez, és egy függő csomag az összes többi Application Insights csomaghoz | [Csomag letöltése](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) |
|Microsoft. ApplicationInsights. Agent. lehallgatás | 2.4.0 | Metódus-hívások elfogásának engedélyezése | [Csomag letöltése](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Agent.Intercept/) |
| Microsoft. ApplicationInsights. DependencyCollector | 2.8.0 | Application Insights függőségi gyűjtő .NET-alkalmazásokhoz. Ez egy függő csomag Application Insights platform-specifikus csomagok számára, és a függőségi telemetria automatikus gyűjtését teszi lehetővé. | [Csomag letöltése](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector/) |
| Microsoft. ApplicationInsights. PerfCounterCollector | 2.8.0 | A Application Insights teljesítményszámlálók gyűjtője lehetővé teszi, hogy a teljesítményszámlálók által gyűjtött adatokat Application Insights küldje el. | [Csomag letöltése](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector/) |
| Microsoft. ApplicationInsights. Web | 2.8.0 | Application Insights .NET-alapú webalkalmazásokhoz | [Csomag letöltése](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) |
| Microsoft. ApplicationInsights. WindowsServer | 2.8.0 | Application Insights Windows Server NuGet csomag lehetővé teszi a .NET-alkalmazásokhoz készült Application telemetria automatikus gyűjtését. Ez a csomag függő csomagként használható Application Insights platform-specifikus csomagok számára, vagy önálló csomagként olyan .NET-alkalmazásokhoz, amelyeket nem a platform-specifikus csomagok (például a .NET-feldolgozói szerepkörök) nem fedik le. | [Csomag letöltése](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/)  
| Microsoft. ApplicationInsights. WindowsServer. TelemetryChannel | 2.8.0 | Telemetria csatornát biztosít a Windows Server SDK Application Insights, amely offline helyzetekben megőrzi a telemetria-t. | [Csomag letöltése](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel/) |

## <a name="common-packages-for-aspnet-core"></a>ASP.NET Core közös csomagjai

| Csomag neve | Stabil verzió | Leírás | Letöltés |
|-------------------------------|-----------------------|------------|----|
| Microsoft. ApplicationInsights. AspNetCore | 2.5.0 | Application Insights ASP.NET Core webalkalmazásokhoz. | [Csomag letöltése](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore/) |
| Microsoft. ApplicationInsights | 2.8.0 | Ez a csomag alapvető funkciókat biztosít az összes Application Insights Telemetria-típus átviteléhez, és egy függő csomag az összes többi Application Insights csomaghoz | [Csomag letöltése](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) |
| Microsoft. ApplicationInsights. DependencyCollector | 2.8.0 | Application Insights függőségi gyűjtő .NET-alkalmazásokhoz. Ez egy függő csomag Application Insights platform-specifikus csomagok számára, és a függőségi telemetria automatikus gyűjtését teszi lehetővé. | [Csomag letöltése](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector/) |
| Microsoft. ApplicationInsights. PerfCounterCollector | 2.8.0 | A Application Insights teljesítményszámlálók gyűjtője lehetővé teszi, hogy a teljesítményszámlálók által gyűjtött adatokat Application Insights küldje el. | [Csomag letöltése](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector/) |
| Microsoft. ApplicationInsights. WindowsServer | 2.8.0 | Application Insights Windows Server NuGet csomag lehetővé teszi a .NET-alkalmazásokhoz készült Application telemetria automatikus gyűjtését. Ez a csomag függő csomagként használható Application Insights platform-specifikus csomagok számára, vagy önálló csomagként olyan .NET-alkalmazásokhoz, amelyeket nem a platform-specifikus csomagok (például a .NET-feldolgozói szerepkörök) nem fedik le. | [Csomag letöltése](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/)  |
| Microsoft. ApplicationInsights. WindowsServer. TelemetryChannel | 2.8.0 | Telemetria csatornát biztosít a Windows Server SDK Application Insights, amely offline helyzetekben megőrzi a telemetria-t. | [Csomag letöltése](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel/) |

## <a name="common-packages-for-python-using-opencensus"></a>Általános csomagok a Pythonhoz a OpenCensus használatával
| Csomag neve | Stabil verzió | Leírás | Letöltés |
|-------------------------------|-----------------------|------------|----|
| opencensus – ext – Azure | 1.0.0 | A Python-alkalmazások Application Insights a OpenCensus-on keresztül Azure Monitor. | [Csomag letöltése](https://pypi.org/project/opencensus-ext-azure/) |
| opencensus – ext-Django | 0.7.2 | Ez a csomag integrációt biztosít a Python [Django](https://pypi.org/project/django/) -könyvtárral. | [Csomag letöltése](https://pypi.org/project/opencensus-ext-django/) |
| opencensus – ext-lombik | 0.7.3 | Ez a csomag a Python- [lombik](https://pypi.org/project/flask/) függvénytárával való integrációt teszi lehetővé. | [Csomag letöltése](https://pypi.org/project/opencensus-ext-flask/) |
| opencensus – ext-httplib | 0.7.2 | Ez a csomag integrációt biztosít a Python [http. Client](https://docs.python.org/3/library/http.client.html) könyvtárából a Python3 és a [httplib](https://docs.python.org/2/library/httplib.html) for Python2 szolgáltatáshoz. | [Csomag letöltése](https://pypi.org/project/opencensus-ext-httplib/) |
| opencensus – ext-naplózás | 0.1.0 | Ez a csomag a nyomkövetési adatokkal gazdagítja a naplóbejegyzések adatait. | [Csomag letöltése](https://pypi.org/project/opencensus-ext-logging/) |
| opencensus – ext-MySQL | 0.1.2 | Ez a csomag biztosítja a Python [MySQL-Connector](https://pypi.org/project/mysql-connector/) függvénytár integrálását. | [Csomag letöltése](https://pypi.org/project/opencensus-ext-mysql/) |
| opencensus – ext-PostgreSQL | 0.1.2 | Ez a csomag integrációt biztosít a Python [psycopg2](https://pypi.org/project/psycopg2/) -könyvtárral. | [Csomag letöltése](https://pypi.org/project/opencensus-ext-postgresql/) |
| opencensus – ext-pymongo | 0.7.1 | Ez a csomag integrációt biztosít a Python [pymongo](https://pypi.org/project/pymongo/) -könyvtárral. | [Csomag letöltése](https://pypi.org/project/opencensus-ext-pymongo/) |
| opencensus – ext-pymysql | 0.1.2 | Ez a csomag integrációt biztosít a Python [PyMySQL](https://pypi.org/project/PyMySQL/) -könyvtárral. | [Csomag letöltése](https://pypi.org/project/opencensus-ext-pymysql/) |
| opencensus – ext-piramis | 0.7.1 | Ez a csomag a Python [piramis](https://pypi.org/project/pyramid/) könyvtárral való integrációt teszi lehetővé. | [Csomag letöltése](https://pypi.org/project/opencensus-ext-pyramid/) |
| opencensus – ext – kérelmek | 0.7.2 | Ez a csomag integrációt biztosít a Python- [kérelmek](https://pypi.org/project/requests/) könyvtárával. | [Csomag letöltése](https://pypi.org/project/opencensus-ext-requests/) |
| opencensus – ext-SQLAlchemy | 0.1.2 | Ez a csomag integrációt biztosít a Python [SQLAlchemy](https://pypi.org/project/SQLAlchemy/) -könyvtárral. | [Csomag letöltése](https://pypi.org/project/opencensus-ext-sqlalchemy/) |

## <a name="listenerscollectorsappenders"></a>Figyelők/gyűjtők/hozzáfűzése

| Csomag neve | Stabil verzió | Leírás | Letöltés |
|-------------------------------|-----------------------|------------|----|
| Microsoft. ApplicationInsights. DiagnosticSourceListener | 2.7.2 |  Lehetővé teszi, hogy az események továbbítása a DiagnosticSource a Application Insights. | [Csomag letöltése](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener/) |
| Microsoft. ApplicationInsights. EventSourceListener | 2.7.2 | Application Insights EventSourceListener lehetővé teszi adatok küldését a EventSource-eseményekről a Application Insightsba. | [Csomag letöltése](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener/) |
| Microsoft. ApplicationInsights. EtwCollector | 2.7.2 | Application Insights a EtwCollector lehetővé teszi az adatok Windows esemény-nyomkövetésból (ETW) való elküldését Application Insights. | [Csomag letöltése](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EtwCollector/) |
| Microsoft. ApplicationInsights. TraceListener | 2.7.2 | Egy egyéni TraceListener, amely lehetővé teszi nyomkövetési naplók küldését Application Insightsba. | [Csomag letöltése](https://www.nuget.org/packages/Microsoft.ApplicationInsights.TraceListener/) |
| Microsoft. ApplicationInsights. Log4NetAppender | 2.7.2 | Az egyéni hozzáfűzése lehetővé teszi, hogy Log4Net-üzeneteket küldjön a Application Insightsnak. | [Csomag letöltése](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Log4NetAppender/)
| Microsoft. ApplicationInsights. NLogTarget | 2.7.2 |  egy egyéni cél, amely lehetővé teszi, hogy NLog-üzeneteket küldjön a Application Insightsnak. | [Csomag letöltése](https://www.nuget.org/packages/Microsoft.ApplicationInsights.NLogTarget/)
| Microsoft. ApplicationInsights. Snapshotcollector nugetcsomag | 1.3.1 | Figyeli az alkalmazásban lévő kivételeket, és automatikusan összegyűjti a pillanatképeket az offline elemzéshez. | [Csomag letöltése](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/)

## <a name="service-fabric"></a>Service Fabric

| Csomag neve | Stabil verzió | Leírás | Letöltés |
|-------------------------------|-----------------------|------------|----|
| Microsoft. ApplicationInsights. ServiceFabric | 2.2.0 | Ez a csomag a telemetria automatikus díszítését biztosítja a Service Fabric-környezettel, amelyben az alkalmazás fut. Ne használja ezt a NuGet natív Service Fabric alkalmazásokhoz. | [Csomag letöltése](https://www.nuget.org/packages/Microsoft.ApplicationInsights.ServiceFabric/) |
| Microsoft. ApplicationInsights. ServiceFabric. Native | 2.2.0 | Application Insights modul a Service Fabric-alkalmazásokhoz. Ezt a NuGet csak natív Service Fabric alkalmazásokhoz használhatja. A tárolókban futó alkalmazások esetében használja a Microsoft. ApplicationInsights. ServiceFabric csomagot. | [Csomag letöltése](https://www.nuget.org/packages/Microsoft.ApplicationInsights.ServiceFabric.Native/) |  

## <a name="status-monitor"></a>Állapotfigyelő

| Csomag neve | Stabil verzió | Leírás | Letöltés |
|-------------------------------|-----------------------|------------|----|
| Microsoft. ApplicationInsights. Agent_x64 | 2.2.1 |  Lehetővé teszi a futásidejű adatgyűjtést az x64-alapú alkalmazásokhoz | [Csomag letöltése](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Agent_x64/) |
| Microsoft. ApplicationInsights. Agent_x86 | 2.2.1 |  Engedélyezi a futásidejű adatgyűjtést x86-alkalmazásokhoz. | [Csomag letöltése](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Agent_x86/) |

Ezek a csomagok a [Állapotmonitor](../../azure-monitor/app/monitor-performance-live-website-now.md)futtatókörnyezet-figyelésének alapvető funkcióinak részét képezik. Ezeket a csomagokat közvetlenül nem kell letöltenie, csak használja a Állapotmonitor telepítőjét. Ha többet szeretne megtudni arról, hogy ezek a csomagok hogyan működnek a motorháztető alatt, akkor az egyik fejlesztőtől származó [blogbejegyzés](https://apmtips.com/blog/2016/11/18/how-application-insights-status-monitor-not-monitors-dependencies/) is jó kezdés.

## <a name="additional-packages"></a>További csomagok

| Csomag neve | Stabil verzió | Leírás | Letöltés |
|-------------------------------|-----------------------|------------|----|
| Microsoft. ApplicationInsights. AzureWebSites | 2.6.5 | Ez a bővítmény engedélyezi a Application Insights figyelését egy Azure App Service. SDK 2.6.1-es verzió. Útmutató: adja hozzá a "APPINSIGHTS_INSTRUMENTATIONKEY" alkalmazás beállításait a rendszerállapotkulcsot, és indítsa újra a webappot, hogy az hatással legyen.| [Csomag letöltése](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AzureWebSites/) |
| Microsoft. ApplicationInsights. injektor | 2.6.7 | Ez a csomag a kód nélkül Application Insights injekcióhoz szükséges fájlokat tartalmazza | [Csomag letöltése](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Injector/) |

## <a name="next-steps"></a>Következő lépések

- [ASP.net Core](../../azure-monitor/app/asp-net-core.md)figyelése.
- Profil ASP.NET Core [Azure Linux Web Apps](../../azure-monitor/app/profiler-aspnetcore-linux.md).
- ASP.NET- [Pillanatképek](../../azure-monitor/app/snapshot-debugger.md)hibakeresése.