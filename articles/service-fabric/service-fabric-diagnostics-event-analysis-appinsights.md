---
title: Azure Service Fabric eseményelemzés az Application Insights segítségével
description: Ismerje meg az események vizualizálását és elemzését az Application Insights használatával az Azure Service Fabric-fürtök figyeléséhez és diagnosztikájához.
author: srrengar
ms.topic: conceptual
ms.date: 11/21/2018
ms.author: srrengar
ms.openlocfilehash: 5c80dc9f350fec30469a9bce2ed836c276ef3a7f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75464755"
---
# <a name="event-analysis-and-visualization-with-application-insights"></a>Eseményelemzés és vizualizáció az Application Insights segítségével

Az Azure Monitor részeként az Application Insights egy bővíthető platform az alkalmazások figyeléséhez és diagnosztikához. Hatékony elemző és lekérdező eszközt, testreszabható irányítópultot és vizualizációkat, valamint további lehetőségeket, többek között automatikus riasztást tartalmaz. Az Application Insights integrálása a Service Fabric-tel eszközkezelési élményeket tartalmaz a Visual Studio és az Azure Portal számára, valamint a Service Fabric-specifikus metrikákat, amelyek átfogó, beépített naplózási élményt nyújtanak. Bár az Application Insights segítségével a rendszer automatikusan létrehoz és gyűjt számos naplót, azt javasoljuk, hogy adjon hozzá további egyéni naplózást az alkalmazásokhoz egy gazdagabb diagnosztikai élmény létrehozásához.

Ez a cikk a következő gyakori kérdések megoldásában nyújt segítséget:

* Honnan tudhatom, hogy mi történik az alkalmazásomban és a szolgáltatásokban, és gyűjtsem össze a telemetriai adatokat?
* Hogyan háríthatom el az alkalmazásomat, különösen az egymással kommunikáló szolgáltatásokat?
* Hogyan kaphatok metrikákat arról, hogy a szolgáltatásaim hogyan teljesítenek, például az oldal betöltési ideje, a HTTP-kérelmek?

Ez a cikk célja, hogy bemutassa, hogyan nyerhet betekintést és hibaelhárítást az Application Insights-ból. Ha szeretné megtudni, hogyan állíthatja be és konfigurálhatja az Application Insights szolgáltatást a Service Fabric szolgáltatással, tekintse meg ezt az [oktatóanyagot.](service-fabric-tutorial-monitoring-aspnet.md)

## <a name="monitoring-in-application-insights"></a>Figyelés az Application Insightsban

Az Application Insights a Service Fabric használatakor egy gazdag out of the box élményt. Az áttekintő lapon az Application Insights kulcsfontosságú információkat nyújt a szolgáltatásról, például a válaszidőről és a feldolgozott kérelmek számáról. A tetején látható "Keresés" gombra kattintva megtekintheti az alkalmazásban szereplő legutóbbi kérések listáját. Emellett itt láthatja a sikertelen kérelmeket, és diagnosztizálhatja, hogy milyen hibák fordulhattak elő.

![Alkalmazáselemzések – áttekintés](media/service-fabric-diagnostics-event-analysis-appinsights/ai-overview.png)

Az előző kép jobb oldali paneljén két fő bejegyzéstípus található a listában: kérések és események. A kérelmek az alkalmazás API-jára HTTP-kérelmeken keresztül kezdeményezett hívások ebben az esetben, és az események egyéni események, amelyek telemetriaként működnek, amelyet a kód bármely pontjára hozzáadhat. Az Application Insights API-ban az [egyéni események és mérőszámok](../azure-monitor/app/api-custom-events-metrics.md)segítségével tovább vizsgálhatja az alkalmazások instrumentingját. Egy kérelemre kattintva további részleteket jelenít meg az alábbi képen látható, beleértve a Service Fabric, amely az Application Insights Service Fabric nuget csomag ban gyűjtött adatokat. Ez az információ hibaelhárításhoz és az alkalmazás állapotának megismeréséhez hasznos, és ezek az információk az Application Insights ban kereshetőek.

![Az Application Insights-kérelmek részletei](media/service-fabric-diagnostics-event-analysis-appinsights/ai-request-details.png)

Az Application Insights rendelkezik egy kijelölt nézetlekérdezési az összes beérkezik adatok. Kattintson a "Metrika-kezelő" az áttekintés lap tetején keresse meg az Application Insights portálon. Itt futtathat lekérdezéseket a korábban említett egyéni eseményekre, kérésekre, kivételekre, teljesítményszámlálókra és egyéb metrikákra a Kusto lekérdezési nyelv használatával. A következő példa az elmúlt 1 óra összes kérelmét bemutatja.

![Az Application Insights-kérelmek részletei](media/service-fabric-diagnostics-event-analysis-appinsights/ai-metrics-explorer.png)

Az Application Insights-portál képességeinek további megismeréséhez tekintse meg az [Application Insights-portál dokumentációját.](../azure-monitor/app/app-insights-dashboards.md)

### <a name="configuring-application-insights-with-eventflow"></a>Az Application Insights konfigurálása az EventFlow-val

Ha az EventFlow-t események összesítésére `Microsoft.Diagnostics.EventFlow.Outputs.ApplicationInsights`használja, győződjön meg arról, hogy importálja a NuGet csomagot. Az *eventFlowConfig.json* *kimenetek* szakaszában a következő kód szükséges:

```json
"outputs": [
    {
        "type": "ApplicationInsights",
        "instrumentationKey": "***ADD INSTRUMENTATION KEY HERE***"
    }
]
```

Győződjön meg arról, hogy a szükséges módosításokat a szűrők, valamint a többi bemenet (valamint a vonatkozó NuGet csomagok).

## <a name="application-insights-sdk"></a>Application Insights SDK

Javasoljuk, hogy az EventFlow és a WAD-t aggregációs megoldásként használja, mivel lehetővé teszik a diagnosztika és a figyelés modulárisabb megközelítését, azaz ha módosítani szeretné az EventFlow kimeneteit, nem igényel változást a tényleges műszerezésben, csak egy egyszerű módosítását a config fájlt. Ha azonban úgy dönt, hogy fektessenek be az Application Insights használatával, és nem valószínű, hogy egy másik platformra, meg kell vizsgálnia az Application Insights új SDK-események összesítése és az Application Insights küldése. Ez azt jelenti, hogy a továbbiakban nem kell konfigurálnia az EventFlow-t az adatok alkalmazáselemzési elküldi, hanem telepíti az ApplicationInsight Service Fabric NuGet csomag. Részletek a csomag megtalálható [itt](https://github.com/Microsoft/ApplicationInsights-ServiceFabric).

[Application Insights mikroszolgáltatások és tárolók támogatása](https://azure.microsoft.com/blog/app-insights-microservices/) megmutatja néhány új funkciók, amelyek jelenleg még béta verzióban), amely lehetővé teszi, hogy gazdagabb out-of-the-box figyelési lehetőségek Application Insights. Ezek közé tartozik a függőségkövetés (amelyet a fürtben lévő összes szolgáltatás és alkalmazás AppMap-rendszerének létrehozásához és a közöttük lévő kommunikációhoz használnak), valamint a szolgáltatásokból érkező nyomkövetések jobb korrelációja (segít jobban meghatározni egy problémát a munkafolyamatban egy alkalmazás vagy szolgáltatás).

Ha a .NET-ben fejleszt, és valószínűleg a Service Fabric néhány programozási modelljét fogja használni, és hajlandó az Application Insights-ot platformként használni az esemény- és naplóadatok megjelenítéséhez és elemzéséhez, akkor azt javasoljuk, hogy az Application Insights SDK-útvonal, mint a figyelési és diagnosztikai munkafolyamat. Olvassa el [ezt](../azure-monitor/app/asp-net-more.md) és [ezt](../azure-monitor/app/asp-net-trace-logs.md) az Alkalmazáselemzési adatok használatával a naplók összegyűjtéséhez és megjelenítéséhez.

## <a name="navigating-the-application-insights-resource-in-azure-portal"></a>Navigálás az Application Insights-erőforrásban az Azure Portalon

Miután konfigurálta az Application Insights kimenetként az események és a naplók, az információk meg kell kezdeni, hogy megjelenik az Application Insights erőforrás néhány percen belül. Keresse meg az Application Insights-erőforrást, amely az Application Insights erőforrás-irányítópultjára irányítja. Kattintson a **Keresés** az Application Insights tálcán a kapott legújabb nyomkövetések megtekintéséhez, és hogy képes legyen szűrni rajtuk keresztül.

*A Metrikakezelő* hasznos eszköz egyéni irányítópultok létrehozásához olyan mérőszámok alapján, amelyeket az alkalmazások, szolgáltatások és fürtjelenthetnek. [A Metrikák feltárása az Application Insightsban](../azure-monitor/app/metrics-explorer.md) című témakörben néhány diagramot állíthat be saját maga számára az összegyűjtött adatok alapján.

Az **Analytics** elemre kattintva az Application Insights Analytics portálra juthat, ahol nagyobb hatókörrel és választhatósággal kérdezheti le az eseményeket és a nyomkövetéseket. Erről bővebben az Analytics in Application Insights ban olvashat [bővebben.](../azure-monitor/app/analytics.md)

## <a name="next-steps"></a>További lépések

* Riasztások beállítása a a [a kban,](../azure-monitor/app/alerts.md) hogy értesítést kapjon a teljesítmény vagy a használat változásairól
* [Az Application Insights intelligens észlelése](../azure-monitor/app/proactive-diagnostics.md) proaktív elemzést végez az Application Insightsnak küldött telemetriai adatokról, hogy figyelmeztesse önt a lehetséges teljesítményproblémákra
