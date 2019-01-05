---
title: Az Azure Service Fabric Eseményelemzés az Application insights segítségével |} A Microsoft Docs
description: További információ megjelenítése és elemzése az Application Insights szolgáltatással monitorozást és diagnosztikát az Azure Service Fabric-fürtök események.
services: service-fabric
documentationcenter: .net
author: srrengar
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/21/2018
ms.author: srrengar
ms.openlocfilehash: efcd2e279d1bf387bc11c238a0592ecee6545cc4
ms.sourcegitcommit: d61faf71620a6a55dda014a665155f2a5dcd3fa2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54053619"
---
# <a name="event-analysis-and-visualization-with-application-insights"></a>Esemény elemzése és vizualizációs az Application insights segítségével

Az Azure Monitor az Application Insights része egy bővíthető platform az alkalmazások monitorozásához és diagnosztizálásához. Egy hatékony elemzési és eszköz, a testreszabható irányítópult megnyitásához és vizualizációkat tartalmaz, és további beállításokat, beleértve az automatizált riasztások. A Service Fabric Application Insights-integráció tartalmaz eszközöket a Visual Studio és az Azure portal, valamint a Service Fabric adott mérőszámok, átfogó-a-beépített naplózási biztosítása érdekében. Bár sok naplók automatikusan létrehozza és az Application Insights használatával gyűjtött, azt javasoljuk, hogy további olyan egyéni naplózási részletesebb diagnosztikai környezetet biztosít az alkalmazások.

Ez a cikk segít, oldja meg a következő gyakori kérdésekre:

* Hogyan tudhatom eseményeit saját alkalmazások és szolgáltatások összegyűjtési telemetriai belül?
* Hogyan háríthatom alkalmazásom, különösen az egymással való kommunikációhoz szolgáltatások?
* Hogyan kaphatok metrikák hogyan saját szolgáltatások hajt végre, például, a lapbetöltési idő, a HTTP-kérések?

Ez a cikk az a célja, hogy bemutatják, hogyan elemezheti és elhárítása az Application Insights belül. Ha szeretné, hogyan állíthatja be, és az Application Insights beállítása a Service Fabric, tekintse meg ezt [oktatóanyag](service-fabric-tutorial-monitoring-aspnet.md).

## <a name="monitoring-in-application-insights"></a>Figyelés az Application insights szolgáltatásban

Az Application Insights a kezdőélmény gazdag rendelkezik, amikor a Service Fabric használatával. Az Áttekintés oldal az Application Insights például a válaszidő és a feldolgozott kérések száma a szolgáltatás kapcsolatos legfontosabb tudnivalókat biztosít. Az oldal tetején a "Search" gombra kattintva az alkalmazás közelmúltbeli kérelmek listáját láthatja. Ezenkívül akkor tudná Itt a sikertelen kérelmek megtekintése, és előfordulhat, hogy mely hibák diagnosztizálásához.

![Az Application Insights áttekintése](media/service-fabric-diagnostics-event-analysis-appinsights/ai-overview.png)

A jobb oldali ablaktáblán az előző képen, a listában lévő bejegyzéseket két fő típusa van: kérelmek és eseményeket. Kérelmek ebben az esetben az alkalmazás API HTTP-kéréseken keresztül végzett hívásokat, és események egyéni eseményeket, amelyek a szerepét a kódban bárhol hozzáadható telemetriatípusok láthatók. Tovább részletezhető az alkalmazásait az szándékkal [Application Insights API egyéni eseményekhez és a metrikák](../azure-monitor/app/api-custom-events-metrics.md). Kattintson egy kérelmet a jeleníti meg, további részleteket a Service Fabric, ami az Application Insights a Service Fabric nuget-csomagot a gyűjtött konkrét adatokat is beleértve, az alábbi ábrán látható módon. Ezek az adatok akkor hasznos, hibaelhárítási és, hogy mi az az alkalmazás állapotát, és ezt az információt csak az Application Insights belül kereshető

![Application Insights kérés részletei](media/service-fabric-diagnostics-event-analysis-appinsights/ai-request-details.png)

Az Application Insights rendelkezik a kijelölt nézet az összes adattal érhető el. Az Application Insights portálon lépjen a Áttekintés lap felső részén kattintson a "Metrikaböngésző". Itt a lekérdezéseket futtassanak is említettük egyéni események, kérelmek, kivételek, teljesítményszámlálók és más metrikákkal a Kusto-lekérdezési nyelv segítségével. Az alábbi példa bemutatja a kérelmei (elmúlt 1 óra).

![Application Insights kérés részletei](media/service-fabric-diagnostics-event-analysis-appinsights/ai-metrics-explorer.png)

További fedezheti fel az Application Insights portálon képességeit, látogasson el a [Application Insights portál dokumentációja](../azure-monitor/app/app-insights-dashboards.md).

### <a name="configuring-application-insights-with-wad"></a>Az Application Insights konfigurálása WAD használatával

>[!NOTE]
>Ez jelenleg csak alkalmazható Windows-fürtök.

Kétféleképpen elsődleges WAD adatokat küldeni a WAD konfiguráció, ahogy az az ad hozzá egy Application Insights fogadó érhető el az Azure Application Insights [Ez a cikk](../azure-monitor/platform/diagnostics-extension-to-application-insights.md).

#### <a name="add-an-application-insights-instrumentation-key-when-creating-a-cluster-in-azure-portal"></a>Adja hozzá az Application Insights-kialakítási kulcsot, ha a fürt létrehozása az Azure Portalon

![Egy AIKey hozzáadása](media/service-fabric-diagnostics-event-analysis-appinsights/azure-enable-diagnostics.png)

A fürt létrehozásakor, ha diagnosztika be van kapcsolva "" mező nem kötelező megadnia egy Application Insights-kialakítási kulcsot jelennek meg. Az Application Insights-kulcs Ide illessze be azt, ha az Application Insights fogadó konfigurálása automatikusan történik meg, amellyel a fürt üzembe helyezése Resource Manager-sablon a.

#### <a name="add-the-application-insights-sink-to-the-resource-manager-template"></a>Az Application Insights fogadó a Resource Manager-sablon hozzáadása

A "WadCfg" Resource Manager-sablon vegyen fel egy "Sink" által többek között a következő két módosításokat:

1. A fogadó konfigurációra deklaráló után közvetlenül a `DiagnosticMonitorConfiguration` befejeződött:

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

2. A fogadó közé tartozik a `DiagnosticMonitorConfiguration` adja hozzá a következő sort a a `DiagnosticMonitorConfiguration` , a `WadCfg` (közvetlenül előtt a `EtwProviders` deklarált):

    ```json
    "sinks": "applicationInsights"
    ```

Mindkét a megelőző kódrészletek, a "applicationInsights" nevet használt a fogadó ismertetik. Ez nem követelmény, és mindaddig, amíg a fogadó neve "fogadóként" szerepel, a név és bármilyen karakterlánc megadható.

Jelenleg a fürt naplóinak megjelenjen **nyomkövetések** Application Insights naplófájl-megjelenítőjében. Mivel a bejövő nyomok a platformról a legtöbb "Tájékoztatási szintű" szint, is érdemes lehet csak a "Critical" vagy "Error". a naplók elküldése a fogadó konfiguráció módosítása Ezt megteheti a fogadó "Csatorna" hozzáadásával ahogyan az is [Ez a cikk](../azure-monitor/platform/diagnostics-extension-to-application-insights.md).

>[!NOTE]
>Ha a portálon vagy a Resource Manager-sablon használatával egy megfelelő Application Insights-kulcs, akkor manuálisan módosíthatja a kulcsot, és a fürt frissítése / telepíteni.

### <a name="configuring-application-insights-with-eventflow"></a>Az Application Insights konfigurálása az eventflow segítségével

Ha az események összesítése az eventflow segítségével használ, ügyeljen arra, hogy importálja a `Microsoft.Diagnostics.EventFlow.Output.ApplicationInsights`NuGet-csomagot. Az alábbi kód azért van szükség a *kimenete* szakaszában a *eventFlowConfig.json*:

```json
"outputs": [
    {
        "type": "ApplicationInsights",
        "instrumentationKey": "***ADD INSTRUMENTATION KEY HERE***"
    }
]
```

Ügyeljen arra, hogy a szükséges módosításokat a szűrőket, valamint bármely más bemeneti (valamint a megfelelő NuGet-csomagok) tartalmazza.

## <a name="application-insights-sdk"></a>Application Insights SDK letöltése

Javasoljuk, hogy együtt használja eventflow segítségével WAD összesítési megoldások, mivel lehetővé teszik a több moduláris megközelítés diagnosztikát és a figyelés, azaz ha meg szeretné változtatni a kimenetek az eventflow segítségével, nem igényel a tényleges rendszerállapot nem változik csak egy egyszerű módosítása a konfigurációs fájlban. Ha azonban úgy dönt, hogy az Application Insights-be, és valószínűleg nem módosíthatja egy másik platformon, akkor események összevonása, és elküldi azokat az Application Insights új Application Insights SDK-bA kell kinéznie. Ez azt jelenti, hogy többé nem lesz eventflow segítségével, hogy adatokat küldjön az Application Insights konfigurálása, de ehelyett a ApplicationInsight Service Fabric NuGet csomag telepíti. A csomag részletei találhatók [Itt](https://github.com/Microsoft/ApplicationInsights-ServiceFabric).

[Mikroszolgáltatások és tárolók támogatása az Application Insights](https://azure.microsoft.com/blog/app-insights-microservices/) mutatja, hogy néhány vannak szerkesztett (jelenleg még mindig beta) új szolgáltatásait, amelyek lehetővé teszik több-az-beépített figyelési lehetőségek az Application insights segítségével. Ezek közé tartozik a függőségi követési (a szolgáltatások és alkalmazások egy fürt és a köztük folyó kommunikációt az Alkalmazástérkép létrehozásakor használt), és az Ön szolgáltatásainak (segít jobban be a problémát a munkafolyamata felügyelő a bejövő nyomok jobb korreláció egy alkalmazás vagy a szolgáltatás).

Ha fejleszt, a .NET-ben, és valószínűleg használják majd néhány Service Fabric programozási modellek, és nem használható az Application Insights a platform jelenítenek meg, és esemény- és naplózási adatok elemzésére szolgáló, majd azt javasoljuk, hogy az Application Insights-n keresztül halad A monitorozási és diagnosztikai munkafolyamatként útvonal SDK-t. Olvasási [ez](../azure-monitor/app/asp-net-more.md) és [ez](../azure-monitor/app/asp-net-trace-logs.md) való ismerkedés az Application Insights segítségével begyűjtheti és megjelenítheti a naplókat.

## <a name="navigating-the-application-insights-resource-in-azure-portal"></a>Navigálás az Azure portal Application Insights-erőforrás

Miután konfigurálta az Application Insights az események és naplók kimenetként, információkat kell kezdenie, néhány perc alatt az Application Insights-erőforrásban jelenik meg. Keresse meg az Application Insights-erőforrást, amellyel az Application Insights-erőforrás irányítópultot. Kattintson a **keresési** az Application Insights tálcán, amely a fogadott legújabb nyomainak megtekintéséhez, és szűrheti ezeket.

*Metrikaböngésző* hasznos eszköz a metrikák, előfordulhat, hogy az alkalmazások, szolgáltatások és a fürt reporting alapján egyéni irányítópultokat hoz létre. Lásd: [metrikák felfedezése az Application Insights](../azure-monitor/app/metrics-explorer.md) beállításához néhány diagramok, saját magának a gyűjtött adatok alapján.

Kattintson a **Analytics** léphet az Application Insights-elemzési portálra, ahol lekérdezheti, ha az események és nagyobb hatókörrel és a lehetőség összefüggésbe a nyomkövetéseket. További információ a következő [az Application Insights Analytics](../azure-monitor/app/analytics.md).

## <a name="next-steps"></a>További lépések

* [A mesterséges Intelligencia riasztásokat állíthat be](../azure-monitor/app/alerts.md) szeretne értesítést kapni a teljesítmény vagy a használati változásai
* [Intelligens detektálás az Application Insights](../azure-monitor/app/proactive-diagnostics.md) hajt végre egy proaktív figyelmezteti a felhasználót, mert ez teljesítményproblémákat okozhat az Application Insightsnak küldött telemetriai adatok elemzése
