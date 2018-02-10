---
title: "Az Azure Service Fabric esemény elemzése az Application insights szolgáltatással |} Microsoft Docs"
description: "Információ megjelenítése és eseményeket az Application Insights figyelési és az Azure Service Fabric-fürtök diagnosztika elemzése."
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/15/2017
ms.author: dekapur
ms.openlocfilehash: 479e486dca432020d5fcbaf98971a9803888bf98
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2018
---
# <a name="event-analysis-and-visualization-with-application-insights"></a>Esemény elemzése és a képi megjelenítés az Application insights szolgáltatással

Az Azure Application Insights egy bővíthető platform, az alkalmazás figyelése és a diagnosztika. Ez magában foglalja egy hatékony elemzések és eszköz, a testre szabható irányítópult és a képi megjelenítések lekérdezése, és további beállításokat, többek között automatikus lehetőséget. A figyelés ajánlott platform és diagnosztika a Service Fabric-alkalmazások és szolgáltatások.

## <a name="setting-up-application-insights"></a>Az Application Insights beállítása

### <a name="creating-an-ai-resource"></a>Az Eszközintelligencia-erőforrás létrehozása

Hozzon létre egy AI erőforrást, az Azure piactéren keresztül head, és keresse meg az "Application Insights". Akkor kell jelenik meg az első megoldásként (már a "Web + mobil" kategória). Kattintson a **létrehozása** amikor nézi, a jobb oldali erőforrás (Győződjön meg arról, hogy az elérési út megegyezik-e az alábbi képen).

![Új Application Insights-erőforrás](media/service-fabric-diagnostics-event-analysis-appinsights/create-new-ai-resource.png)

Szüksége lesz néhány adatra, hogy az erőforrás kiépítésekor kitöltéséhez. Az a *alkalmazástípus* mezőjét, használja a "ASP.NET web application" Ha fog használni a Service Fabric programozási modellek vagy közzététele a fürt .NET-alkalmazásokat. Használja az "Általános", ha a Vendég végrehajtható fájlok és a tárolók meg fog telepítéséhez. Általában az alapértelmezett "ASP.NET web application" használatával megnyitva, a beállítások a jövőben. A név igény szerint akár, és az erőforráscsoportot és az előfizetés az erőforrás telepítés utáni módosítható. Azt javasoljuk, hogy a AI erőforrás megtalálható-e a fürt ugyanabban az erőforráscsoportban. Ha további tájékoztatásra van szüksége, tekintse át [Application Insights-erőforrás létrehozása](../application-insights/app-insights-create-new-resource.md)

Az esemény összesítési eszközzel AI konfigurálása a AI Instrumentation kulcs van szüksége. Miután az Eszközintelligencia-erőforrás (a központi telepítés érvényesítését követően néhány percet vesz igénybe) be van állítva, keresse meg a fájlt, és keresse a **tulajdonságok** szakasz a bal oldali navigációs sávon. Egy új panel nyílik meg, amely egy *INSTRUMENTATION kulcs*. Ha az előfizetés vagy az erőforrás erőforráscsoport módosítani szeretné, azt is megteheti itt is.

### <a name="configuring-ai-with-wad"></a>ÜVEGVATTA AI konfigurálása

>[!NOTE]
>Ez a tulajdonság csak a Windows-fürtök alkalmazandó időpontjában.

Két módon elsődleges ÜVEGVATTA adatokat küldeni Azure AI, amely az érhető el egy AI fogadó ad hozzá a ÜVEGVATTA konfigurációs, ahogy az az [Ez a cikk](../monitoring-and-diagnostics/azure-diagnostics-configure-application-insights.md).

#### <a name="add-an-ai-instrumentation-key-when-creating-a-cluster-in-azure-portal"></a>Adja hozzá egy AI Instrumentation kulcsot, ha a fürt létrehozása az Azure-portálon

![Egy AIKey hozzáadása](media/service-fabric-diagnostics-event-analysis-appinsights/azure-enable-diagnostics.png)

Ha a fürt létrehozása, amikor engedélyezve van a diagnosztika "On", az Application Insights Instrumentation kulcs választható mező jelennek meg. A AI IKey Ide illessze be, ha a AI fogadó automatikusan megtörténik, a Resource Manager sablon, amellyel a fürt központi telepítése.

#### <a name="add-the-ai-sink-to-the-resource-manager-template"></a>Adja hozzá a AI fogadó a Resource Manager-sablon

A "WadCfg" Resource Manager-sablon vegyen fel egy "fogadó" által többek között a következő két módosításokat:

1. A fogadó konfiguráció után közvetlenül a deklaráló hozzáadása a `DiagnosticMonitorConfiguration` befejezése:

    ```json
    "SinksConfig": {
        "Sink": [
            {
                "name": "applicationInsights",
                "ApplicationInsights": "***ADD INSTRUMENTATION KEY HERE***"
            }
        ]
    }

    ```

2. A fogadó tartalmazza a `DiagnosticMonitorConfiguration` adja hozzá a következő sort a a `DiagnosticMonitorConfiguration` , a `WadCfg` (közvetlenül előtt a `EtwProviders` deklarált):

    ```json
    "sinks": "applicationInsights"
    ```

Mindkét kódrészletek fenti, a "applicationInsights" név megadásával írhatja le a fogadó lett megadva. Ez nem követelmény, és mindaddig, amíg a gyűjtő neve "mosdók" szerepel, állíthat be a nevet bármilyen karakterlánc.

Jelenleg a fürtből naplók állapotúként jelenik meg lévő AI tartozó naplófájl-megjelenítő. Mivel a platform érkező nyomkövetés legtöbb szint "Tájékoztató", is érdemes lehet a fogadó konfigurációját, és csak a "Kritikus" vagy "Error" típusú tartalmazó naplófájlok elküldése módosítása. Ezt megteheti a fogadó "Csatornák" felvételével, ahogyan az [Ez a cikk](../monitoring-and-diagnostics/azure-diagnostics-configure-application-insights.md).

>[!NOTE]
>Portál vagy a Resource Manager-sablon használatakor egy helytelen AI IKey, akkor manuálisan módosíthatja a kulcs és a fürt frissítésére / újratelepítése. 

### <a name="configuring-ai-with-eventflow"></a>EventFlow AI konfigurálása

Ha EventFlow az összegyűjtött eseményeket használ, ügyeljen arra, hogy importálja a `Microsoft.Diagnostics.EventFlow.Output.ApplicationInsights`NuGet-csomagot. A következő szereplő rendelkezik a *kimenete* szakasza a *eventFlowConfig.json*:

```json
"outputs": [
    {
        "type": "ApplicationInsights",
        // (replace the following value with your AI resource's instrumentation key)
        "instrumentationKey": "00000000-0000-0000-0000-000000000000"
    }
]
```

Ügyeljen arra, hogy végezze el a szükséges módosításokat a szűrőket, valamint bármely más bemeneti (valamint a megfelelő NuGet-csomagok) tartalmazza.

## <a name="aisdk"></a>AI.SDK

Általában javasolt EventFlow és ÜVEGVATTA használandó összesítési megoldások, mivel lehetővé teszik több moduláris megközelítésre diagnosztika és a figyelését, azaz a EventFlow a kimenetek módosítani szeretné, ha nem módosítja a tényleges instrumentation, a konfigurációs fájl csak egy egyszerű módosítását igényli. Ha azonban úgy dönt, hogy az Application Insights segítségével beruházásának, és valószínűleg nem módosítható a különböző platform, kell keresnie az való összesítése eseményeket, és elküldi őket AI AI tartozó új SDK használatával. Ez azt jelenti, hogy már nem kell konfigurálnia a adatokat küldeni a AI EventFlow, de ehelyett a ApplicationInsight Service Fabric NuGet csomag telepíti. A csomag a részletek megtalálhatók [Itt](https://github.com/Microsoft/ApplicationInsights-ServiceFabric).

[Az Application Insights mikroszolgáltatások létrehozására és a tárolók támogatása](https://azure.microsoft.com/en-us/blog/app-insights-microservices/) elsajátíthatja, hogy néhány új szolgáltatásai a (jelenleg továbbra is a bétaverzió) működő, amely engedélyezi, hogy gazdagabb out-of-az-box figyelési beállítások AI rendelkezik. Ezek közé tartozik a függőségi követési (a szolgáltatások és alkalmazások egy fürt és a köztük folyó kommunikációt egy AppMap létrehozásakor használt), és a szolgáltatások (segíti a hatékonyabb felügyelő a munkafolyamat egy alkalmazás vagy szolgáltatás problémát) érkező nyomkövetési jobb összekapcsolását.

Ha a .NET fejleszt, és valószínűleg használ, a Service Fabric programozási modellek és AI használják a platform megjelenítése és esemény és naplózási adatok elemzése a hajlandó, majd ajánlott lépjen a keresztül a AI SDK útvonal, a figyelési, diagnosztikai munkafolyamat. Olvasási [ez](../application-insights/app-insights-asp-net-more.md) és [ez](../application-insights/app-insights-asp-net-trace-logs.md) használatába AI gyűjtése ki és jelenítheti meg a naplók segítségével.

## <a name="navigating-the-ai-resource-in-azure-portal"></a>Navigálás a AI erőforrás Azure-portálon

Miután konfigurálta a AI kimenetként az események és a naplókat, információkat kell kezdődnie, megjeleníti őket a AI erőforrás néhány perc múlva. Nyissa meg a AI erőforrás, amely az AI resource-irányítópultot. Kattintson a **keresési** tekintse meg a legújabb nyomkövetési adatokat kapott, és a rajtuk keresztül szűrheti AI tálcán.

*Metrikaböngésző* az eszköz lehet, hogy az alkalmazások, szolgáltatások és a fürt reporting metrikák alapján egyéni irányítópultok létrehozásához. Lásd: [felfedezése metrikákat az Application Insightsban](../application-insights/app-insights-metrics-explorer.md) néhány diagramok beállítása, a saját kezűleg a gyűjtött adatok alapján.

Kattintson a **Analytics** léphet vissza az Application Insights Analytics portálra, ahol események és nagyobb hatókörrel és a lehetőség a nyomkövetéseket lekérheti. További információk a következő [az Application Insightsban Analytics](../application-insights/app-insights-analytics.md).

## <a name="next-steps"></a>További lépések

* [Állítson be riasztásokat a AI](../application-insights/app-insights-alerts.md) teljesítmény vagy a használati változásaira vonatkozó értesítést
* [Az Application Insights az észlelést intelligens](../application-insights/app-insights-proactive-diagnostics.md) hajt végre egy proaktív figyelmezteti a felhasználót, mert ez teljesítményproblémákat okozhat AI küldött telemetriai adatok elemzése
