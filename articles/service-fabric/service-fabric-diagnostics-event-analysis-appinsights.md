---
title: Az Azure Service Fabric esemény elemzése az Application insights szolgáltatással |} Microsoft Docs
description: Információ megjelenítése és eseményeket az Application Insights figyelési és az Azure Service Fabric-fürtök diagnosztika elemzése.
services: service-fabric
documentationcenter: .net
author: srrengar
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/04/2018
ms.author: dekapur; srrengar
ms.openlocfilehash: 3a7c7663bc13b7169ec9d31aa21365219ec39059
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2018
---
# <a name="event-analysis-and-visualization-with-application-insights"></a>Esemény elemzése és a képi megjelenítés az Application insights szolgáltatással

Az Azure Application Insights egy bővíthető platform, az alkalmazás figyelése és a diagnosztika. Ez magában foglalja egy hatékony elemzések és eszköz, a testre szabható irányítópult és a képi megjelenítések lekérdezése, és további beállításokat, többek között automatikus lehetőséget. A figyelés ajánlott platform és diagnosztika a Service Fabric-alkalmazások és szolgáltatások. Ez a cikk segít a következő gyakori kérdések

* Hogyan állapítható meg, mi a helyzet az alkalmazások és szolgáltatások összefog telemetriai belül
* Hogyan ezeket elhárítani a alkalmazást, különösen az egymással való kommunikációhoz szolgáltatások
* Hogyan szerezhetek hogyan a szolgáltatások hajtja végre, például, a lapbetöltési idő, a http-kérelmek metrikák

Ez a cikk célja bemutatják, hogyan megismerésében, illetve háríthatóak el a App Insights belül. Ha azt szeretné, hogyan állítson be és AI konfigurálása a Service Fabric, tekintse meg a [oktatóanyag](service-fabric-tutorial-monitoring-aspnet.md).

## <a name="monitoring-in-app-insights"></a>App Insights ellenőrzése

Az Application Insights egy gazdag kívül a Service Fabric mezőt tartalmaz. Az Áttekintés lap AI például a válaszidő és a feldolgozott kérelmek száma a szolgáltatás a főbb információkat biztosít. A lap tetején a "Search" gombra kattintva, lásd: a legutóbbi kérelmek listáját az alkalmazásban. Emellett akkor tudná ide a sikertelen kérelmek megtekintéséhez, és előfordulhat, hogy milyen hibák diagnosztizálása.

![AI áttekintése](media/service-fabric-diagnostics-event-analysis-appinsights/ai-overview.png)

A jobb oldali panelen az előző ábrán, a listában szereplő bejegyzések két fő típusa van: kérelmek és események. Kérelmek ebben az esetben az alkalmazás API HTTP-kérelmek keresztül felé indított hívások, és események egyéni események, amelyek bárhol adhat hozzá a kódban telemetriai összekötőként. További ismerje meg az alkalmazások tagolása [Application Insights API egyéni események és metrikák](../application-insights/app-insights-api-custom-events-metrics.md). Kattintson a kérés jeleníti meg, további részletek a Service Fabric, amely a AI Service Fabric nuget-csomag gyűjt vonatkozó adatokat is beleértve, a következő ábrán látható módon. Ezeket az adatokat hibaelhárításhoz, és mi az az alkalmazás állapotának ismerete hasznos, és az adatok csak az Application Insights belül kereshető

![AI részletei](media/service-fabric-diagnostics-event-analysis-appinsights/ai-request-details.png)

Az Application Insights rendelkezik a kijelölt nézet lekérdezése alapján minden adat érkezik. Kattintson a "Metrikaböngésző" a – áttekintés oldalra, navigáljon a AI portál felső részén található. Itt is futtathatók lekérdezések egyéni események említettük, kérelmek, kivételek, teljesítményszámlálók és más metrikákkal a Kusto lekérdezési nyelv használatával. A következő példa bemutatja a kérelmeket az elmúlt 1 órában.

![AI részletei](media/service-fabric-diagnostics-event-analysis-appinsights/ai-metrics-explorer.png)

A képességek a App Insights portál további megismeréséhez látogasson el a [Application Insights portál dokumentációjában](../application-insights/app-insights-dashboards.md).

### <a name="configuring-ai-with-wad"></a>ÜVEGVATTA AI konfigurálása

>[!NOTE]
>Ez a tulajdonság csak a Windows-fürtök alkalmazandó időpontjában.

Két módon elsődleges ÜVEGVATTA adatokat küldeni Azure AI, amely az érhető el egy AI fogadó ad hozzá a ÜVEGVATTA konfigurációs, ahogy az az [Ez a cikk](../monitoring-and-diagnostics/azure-diagnostics-configure-application-insights.md).

#### <a name="add-an-ai-instrumentation-key-when-creating-a-cluster-in-azure-portal"></a>Adja hozzá egy AI Instrumentation kulcsot, ha a fürt létrehozása az Azure-portálon

![Egy AIKey hozzáadása](media/service-fabric-diagnostics-event-analysis-appinsights/azure-enable-diagnostics.png)

Ha a fürt létrehozása, amikor engedélyezve van a diagnosztika "On", az Application Insights Instrumentation kulcs választható mező jelennek meg. Az Eszközintelligencia-kulcs Ide illessze be, ha a AI fogadó automatikusan be van állítva, a Resource Manager sablon, amellyel a fürt központi telepítése.

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

Mindkét a megelőző kódrészletek, a "applicationInsights" név megadásával írhatja le a fogadó lett megadva. Ez nem követelmény, és mindaddig, amíg a gyűjtő neve "mosdók" szerepel, állíthat be a nevet bármilyen karakterlánc.

Jelenleg a fürtből naplók megjelennek, **nyomkövetések** a naplófájl-megjelenítő AI meg. Mivel a platform érkező nyomkövetés legtöbb szint "Tájékoztató", is érdemes lehet a fogadó konfigurációját, és csak a "Kritikus" vagy "Error." típusú tartalmazó naplófájlok elküldése módosítása Ezt megteheti a fogadó "Csatornák" felvételével, ahogyan az [Ez a cikk](../monitoring-and-diagnostics/azure-diagnostics-configure-application-insights.md).

>[!NOTE]
>Ha a portálon vagy az erőforrás-kezelő sablonban AI helytelen kulcsot használ, akkor manuálisan módosíthatja a kulcs és a fürt frissítésére / újratelepítése.

### <a name="configuring-ai-with-eventflow"></a>EventFlow AI konfigurálása

Ha EventFlow az összegyűjtött eseményeket használ, ügyeljen arra, hogy importálja a `Microsoft.Diagnostics.EventFlow.Output.ApplicationInsights`NuGet-csomagot. Az alábbi kódra van szükség a *kimenete* szakasza a *eventFlowConfig.json*:

```json
"outputs": [
    {
        "type": "ApplicationInsights",
        "instrumentationKey": "***ADD INSTRUMENTATION KEY HERE***"
    }
]
```

Ügyeljen arra, hogy végezze el a szükséges módosításokat a szűrőket, valamint bármely más bemeneti (valamint a megfelelő NuGet-csomagok) tartalmazza.

## <a name="aisdk"></a>AI.SDK

Javasoljuk, hogy használható EventFlow és ÜVEGVATTA összesítési megoldások, mert lehetővé teszik több moduláris megközelítése diagnosztika és figyelést, vagyis ha szeretné módosítani a kimenetek a EventFlow, szükség van nem változik a tényleges instrumentation csak egy egyszerű módosítása a konfigurációs fájlhoz. Ha azonban úgy dönt, hogy az Application Insights segítségével beruházásának, és valószínűleg nem módosítható a különböző platform, kell keresnie az való összesítése eseményeket, és elküldi őket AI AI tartozó új SDK használatával. Ez azt jelenti, hogy már nem kell konfigurálnia a adatokat küldeni a AI EventFlow, de ehelyett a ApplicationInsight Service Fabric NuGet csomag telepíti. A csomag a részletek megtalálhatók [Itt](https://github.com/Microsoft/ApplicationInsights-ServiceFabric).

[Az Application Insights mikroszolgáltatások létrehozására és a tárolók támogatása](https://azure.microsoft.com/en-us/blog/app-insights-microservices/) elsajátíthatja, hogy néhány új szolgáltatásai a (jelenleg továbbra is a bétaverzió) működő, amely engedélyezi, hogy gazdagabb out-of-az-box figyelési beállítások AI rendelkezik. Ezek közé tartozik a függőségi követési (a szolgáltatások és alkalmazások egy fürt és a köztük folyó kommunikációt egy AppMap létrehozásakor használt), és a szolgáltatások (segíti a hatékonyabb felügyelő a munkafolyamat egy alkalmazás vagy szolgáltatás problémát) érkező nyomkövetési jobb összekapcsolását.

Ha a .NET fejleszt, és valószínűleg használ, a Service Fabric programozási modellek és AI használják a platform megjelenítése és esemény és naplózási adatok elemzése a hajlandó, majd ajánlott lépjen a keresztül a AI SDK útvonal, a figyelési, diagnosztikai munkafolyamat. Olvasási [ez](../application-insights/app-insights-asp-net-more.md) és [ez](../application-insights/app-insights-asp-net-trace-logs.md) használatába AI gyűjtése ki és jelenítheti meg a naplók segítségével.

## <a name="navigating-the-ai-resource-in-azure-portal"></a>Navigálás a AI erőforrás Azure-portálon

Miután konfigurálta a AI kimenetként az események és a naplókat, információkat kell kezdődnie, megjeleníti őket a AI erőforrás néhány perc múlva. Nyissa meg a AI erőforrás, amely az AI resource-irányítópultot. Kattintson a **keresési** tekintse meg a legújabb nyomkövetési adatokat kapott, és a rajtuk keresztül szűrheti AI tálcán.

*Metrikaböngésző* az eszköz lehet, hogy az alkalmazások, szolgáltatások és a fürt reporting metrikák alapján egyéni irányítópultok létrehozásához. Lásd: [felfedezése metrikákat az Application Insightsban](../application-insights/app-insights-metrics-explorer.md) néhány diagramok beállítása, a saját kezűleg a gyűjtött adatok alapján.

Kattintson a **Analytics** léphet vissza az Application Insights Analytics portálra, ahol események és nagyobb hatókörrel és a lehetőség a nyomkövetéseket lekérheti. További információk a következő [az Application Insightsban Analytics](../application-insights/app-insights-analytics.md).

## <a name="next-steps"></a>További lépések

* [Állítson be riasztásokat a AI](../application-insights/app-insights-alerts.md) teljesítmény vagy a használati változásaira vonatkozó értesítést
* [Az Application Insights az észlelést intelligens](../application-insights/app-insights-proactive-diagnostics.md) hajt végre egy proaktív figyelmezteti a felhasználót, mert ez teljesítményproblémákat okozhat AI küldött telemetriai adatok elemzése
