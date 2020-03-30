---
title: Azure Monitor Application Insights NuGet-csomagok
description: Azure Monitor Application Insights NuGet csomagok listái ASP.NET, ASP.NET Core, Python
ms.topic: reference
ms.date: 10/16/2018
ms.openlocfilehash: 5675c0fad61f1b75aa7e93db02246eed43967ec2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77669998"
---
# <a name="application-insights-nuget-packages"></a>Application Insights NuGet csomagok

Az alábbiakban a stabil kiadás NuGet csomagok Application Insights.

## <a name="common-packages-for-aspnet"></a>Közös csomagok ASP.NET

| Csomag neve | Stabil verzió | Leírás | Letöltés |
|-------------------------------|-----------------------|------------|----|
| Microsoft.ApplicationInsights | 2.12.0 | Alapvető funkciókat biztosít az Összes Application Insights-telemetriai típus átviteléhez, és függő csomag az összes többi Application Insights-csomaghoz | [Csomag letöltése](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) |
|Microsoft.ApplicationInsights.Agent.Intercept | 2.4.0 | Lehetővé teszi a metódushívások lehallgatását | [Csomag letöltése](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Agent.Intercept/) |
| Microsoft.ApplicationInsights.DependencyCollector | 2.12.0 | Application Insights függőséggyűjtő . Ez egy függő csomag Az Application Insights platformspecifikus csomagok, és a függőségi telemetriai adatok automatikus gyűjtését biztosítja. | [Csomag letöltése](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector/) |
| Microsoft.ApplicationInsights.PerfCounterCollector | 2.12.0 | Az Application Insights teljesítményszámlálói nak gyűjtője lehetővé teszi, hogy a teljesítményszámlálók által gyűjtött adatokat küldje el az Application Insightsnak. | [Csomag letöltése](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector/) |
| Microsoft.ApplicationInsights.Web | 2.12.0 | Application Insights .NET webalkalmazásokhoz | [Csomag letöltése](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) |
| Microsoft.ApplicationInsights.WindowsServer | 2.12.0 | Az Application Insights Windows Server NuGet csomag automatikusan gyűjti az alkalmazáselemzési telemetriai adatokat a .NET alkalmazásokhoz. Ez a csomag használható függő csomagként az Application Insights platformspecifikus csomagokhoz, vagy önálló csomagként . | [Csomag letöltése](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/)  
| Microsoft.ApplicationInsights.WindowsServer.TelemettryChannel | 2.12.0 | Telemetriai csatornát biztosít az Application Insights Windows Server SDK számára, amely megőrzi a telemetriai adatokat kapcsolat nélküli helyzetekben. | [Csomag letöltése](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel/) |

## <a name="common-packages-for-aspnet-core"></a>Közös csomagok ASP.NET Core számára

| Csomag neve | Stabil verzió | Leírás | Letöltés |
|-------------------------------|-----------------------|------------|----|
| Microsoft.ApplicationInsights.AspNetCore | 2.5.0 | Application Insights ASP.NET Core webalkalmazások. | [Csomag letöltése](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore/) |
| Microsoft.ApplicationInsights | 2.12.0 | Ez a csomag alapvető funkciókat biztosít az Application Insights telemetriai típusok továbbításához, és függő csomag az összes többi Application Insights-csomaghoz | [Csomag letöltése](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) |
| Microsoft.ApplicationInsights.DependencyCollector | 2.12.0 | Application Insights függőséggyűjtő . Ez egy függő csomag Az Application Insights platformspecifikus csomagok, és a függőségi telemetriai adatok automatikus gyűjtését biztosítja. | [Csomag letöltése](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector/) |
| Microsoft.ApplicationInsights.PerfCounterCollector | 2.12.0 | Az Application Insights teljesítményszámlálói nak gyűjtője lehetővé teszi, hogy a teljesítményszámlálók által gyűjtött adatokat küldje el az Application Insightsnak. | [Csomag letöltése](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector/) |
| Microsoft.ApplicationInsights.WindowsServer | 2.12.0 | Az Application Insights Windows Server NuGet csomag automatikusan gyűjti az alkalmazáselemzési telemetriai adatokat a .NET alkalmazásokhoz. Ez a csomag használható függő csomagként az Application Insights platformspecifikus csomagokhoz, vagy önálló csomagként . | [Csomag letöltése](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/)  |
| Microsoft.ApplicationInsights.WindowsServer.TelemettryChannel | 2.12.0 | Telemetriai csatornát biztosít az Application Insights Windows Server SDK számára, amely megőrzi a telemetriai adatokat kapcsolat nélküli helyzetekben. | [Csomag letöltése](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel/) |

## <a name="common-packages-for-python-using-opencensus"></a>Közös csomagok python használatával OpenCensus
| Csomag neve | Stabil verzió | Leírás | Letöltés |
|-------------------------------|-----------------------|------------|----|
| opencensus-ext-azure | 1.0.0 | Application Insights for Python-alkalmazások az Azure Monitor opencensus keresztül. | [Csomag letöltése](https://pypi.org/project/opencensus-ext-azure/) |
| opencensus-ext-django | 0.7.2 | Ez a csomag integrációt biztosít a Python [django](https://pypi.org/project/django/) könyvtárral. | [Csomag letöltése](https://pypi.org/project/opencensus-ext-django/) |
| opencensus-ext-flask | 0.7.3 | Ez a csomag integrációt biztosít a Python [flask könyvtárral.](https://pypi.org/project/flask/) | [Csomag letöltése](https://pypi.org/project/opencensus-ext-flask/) |
| opencensus-ext-httplib | 0.7.2 | Ez a csomag integrációt biztosít a Python [http.client](https://docs.python.org/3/library/http.client.html) kódtár python3 és [httplib](https://docs.python.org/2/library/httplib.html) python2. | [Csomag letöltése](https://pypi.org/project/opencensus-ext-httplib/) |
| opencensus-ext-fakitermelés | 0.1.0 | Ez a csomag nyomkövetési adatokkal gazdagítja a naplórekordokat. | [Csomag letöltése](https://pypi.org/project/opencensus-ext-logging/) |
| opencensus-ext-mysql | 0.1.2 | Ez a csomag integrációt biztosít a Python [mysql-connector](https://pypi.org/project/mysql-connector/) könyvtárral. | [Csomag letöltése](https://pypi.org/project/opencensus-ext-mysql/) |
| opencensus-ext-postgresql | 0.1.2 | Ez a csomag integrációt biztosít a Python [psycopg2](https://pypi.org/project/psycopg2/) könyvtárral. | [Csomag letöltése](https://pypi.org/project/opencensus-ext-postgresql/) |
| opencensus-ext-pymongo | 0.7.1 | Ez a csomag integrációt biztosít a Python [pymongo](https://pypi.org/project/pymongo/) könyvtárral. | [Csomag letöltése](https://pypi.org/project/opencensus-ext-pymongo/) |
| opencensus-ext-pymysql | 0.1.2 | Ez a csomag integrációt biztosít a Python [PyMySQL](https://pypi.org/project/PyMySQL/) könyvtárral. | [Csomag letöltése](https://pypi.org/project/opencensus-ext-pymysql/) |
| opencensus-ext-piramis | 0.7.1 | Ez a csomag integrációt biztosít a [Python-piramis](https://pypi.org/project/pyramid/) könyvtárral. | [Csomag letöltése](https://pypi.org/project/opencensus-ext-pyramid/) |
| opencensus-ext-kérelmek | 0.7.2 | Ez a csomag integrációt biztosít a [Python-kérelmek könyvtárával.](https://pypi.org/project/requests/) | [Csomag letöltése](https://pypi.org/project/opencensus-ext-requests/) |
| opencensus-ext-sqlalchemy | 0.1.2 | Ez a csomag integrációt biztosít a Python [SQLAlchemy](https://pypi.org/project/SQLAlchemy/) könyvtárral. | [Csomag letöltése](https://pypi.org/project/opencensus-ext-sqlalchemy/) |

## <a name="listenerscollectorsappenders"></a>Hallgatók/gyűjtők/appenders

| Csomag neve | Stabil verzió | Leírás | Letöltés |
|-------------------------------|-----------------------|------------|----|
| Microsoft.ApplicationInsights.DiagnosticSourceListener | 2.7.2 |  Lehetővé teszi az események továbbítását a DiagnosticSource-ról az Application Insightsba. | [Csomag letöltése](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener/) |
| Microsoft.ApplicationInsights.EventSourceListener | 2.7.2 | Az Application Insights EventSourceListener lehetővé teszi az EventSource-eseményekből származó adatok küldését az Application Insights ba. | [Csomag letöltése](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener/) |
| Microsoft.ApplicationInsights.EtwCollector | 2.7.2 | Az Application Insights EtwCollector lehetővé teszi az adatok küldését a Windows eseménykövetés (ETW) az Application Insights. | [Csomag letöltése](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EtwCollector/) |
| Microsoft.ApplicationInsights.TraceListener | 2.7.2 | Egyéni TraceListener, amely lehetővé teszi, hogy nyomkövetési naplóüzeneteket küldjön az Application Insights. | [Csomag letöltése](https://www.nuget.org/packages/Microsoft.ApplicationInsights.TraceListener/) |
| Microsoft.ApplicationInsights.Log4NetAppender | 2.7.2 | Egyéni appender, amely lehetővé teszi, hogy Log4Net naplóüzeneteket küldjön az Application Insights nak. | [Csomag letöltése](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Log4NetAppender/)
| Microsoft.ApplicationInsights.NLogTarget | 2.7.2 |  egyéni cél, amely lehetővé teszi, hogy NLog log üzeneteket küldjön az Application Insightsnak. | [Csomag letöltése](https://www.nuget.org/packages/Microsoft.ApplicationInsights.NLogTarget/)
| Microsoft.ApplicationInsights.SnapshotCollector | 1.3.1 | Figyeli a kivételeket az alkalmazásban, és automatikusan összegyűjti a pillanatképeket az offline elemzéshez. | [Csomag letöltése](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/)

## <a name="service-fabric"></a>Service Fabric

| Csomag neve | Stabil verzió | Leírás | Letöltés |
|-------------------------------|-----------------------|------------|----|
| Microsoft.ApplicationInsights.ServiceFabric | 2.2.0 | Ez a csomag a telemetriai adatok automatikus díszítését biztosítja az alkalmazás által futtatott szolgáltatásháló környezetével. Ne használja ezt a NuGet natív service Fabric-alkalmazások. | [Csomag letöltése](https://www.nuget.org/packages/Microsoft.ApplicationInsights.ServiceFabric/) |
| Microsoft.ApplicationInsights.ServiceFabric.Natív | 2.2.0 | Application Insights modul service fabric-alkalmazásokhoz. Ezt a NuGet-et csak natív service Fabric-alkalmazásokhoz használja. Tárolókban futó alkalmazások esetén használja a Microsoft.ApplicationInsights.ServiceFabric csomagot. | [Csomag letöltése](https://www.nuget.org/packages/Microsoft.ApplicationInsights.ServiceFabric.Native/) |  

## <a name="status-monitor"></a>Állapotfigyelő

| Csomag neve | Stabil verzió | Leírás | Letöltés |
|-------------------------------|-----------------------|------------|----|
| Microsoft.ApplicationInsights.Agent_x64 | 2.2.1 |  Futásidejű adatgyűjtés engedélyezése x64-es alkalmazásokhoz | [Csomag letöltése](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Agent_x64/) |
| Microsoft.ApplicationInsights.Agent_x86 | 2.2.1 |  Lehetővé teszi az x86-os alkalmazások futásidejű adatgyűjtését. | [Csomag letöltése](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Agent_x86/) |

Ezek a csomagok alkotják a futásidejű figyelés alapvető funkcióinak részét az [Állapotfigyelő](../../azure-monitor/app/monitor-performance-live-website-now.md)ben. Nem kell letölteni ezeket a csomagokat közvetlenül, csak használja a Status Monitor telepítő. Ha szeretné megérteni többet arról, hogy ezek a csomagok a munka a motorháztető alatt ez a [blogbejegyzést](https://apmtips.com/blog/2016/11/18/how-application-insights-status-monitor-not-monitors-dependencies/) az egyik fejlesztő egy jó kezdet.

## <a name="additional-packages"></a>További csomagok

| Csomag neve | Stabil verzió | Leírás | Letöltés |
|-------------------------------|-----------------------|------------|----|
| Microsoft.ApplicationInsights.AzureWebWebhelyek | 2.6.5 | Ez a bővítmény lehetővé teszi az Application Insights figyelését egy Azure App Service-en. SDK 2.6.1-es verziója. Utasítások: Adja hozzá a "APPINSIGHTS_INSTRUMENTATIONKEY" alkalmazás beállításait az ikey-hoz, és indítsa újra a webappot, hogy érvénybe lépjen.| [Csomag letöltése](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AzureWebSites/) |
| Microsoft.ApplicationInsights.Injektor | 2.6.7 | Ez a csomag a kód nélküli Application Insights injekcióhoz szükséges fájlokat tartalmazza | [Csomag letöltése](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Injector/) |

## <a name="next-steps"></a>További lépések

- Monitor [ASP.NET Core](../../azure-monitor/app/asp-net-core.md).
- Profil ASP.NET Core [Azure Linux webalkalmazások.](../../azure-monitor/app/profiler-aspnetcore-linux.md)
- Hibakeresés ASP.NET [pillanatképek](../../azure-monitor/app/snapshot-debugger.md).
