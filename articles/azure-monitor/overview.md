---
title: Az Azure Monitor áttekintése |} A Microsoft Docs
description: Az Azure-szolgáltatások és -alkalmazások teljes monitorozási stratégiájának részét képező Microsoft-szolgáltatások és funkciók áttekintése.
author: bwren
manager: carmonm
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.service: azure-monitor
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/26/2018
ms.author: bwren
ms.openlocfilehash: a3e0125a66c4b8bbda6e0b7087fe5e0e387d43e4
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52847388"
---
# <a name="azure-monitor-overview"></a>Az Azure Monitor áttekintése

Az Azure Monitor a lehető legnagyobbra növeli a az alkalmazások teljesítményét és rendelkezésre állását azáltal, hogy egy átfogó megoldás gyűjtéséhez, elemzéséhez és a telemetriai adatokat a felhőben működő és helyszíni környezeteket. Ez a szolgáltatás segít megérteni azt, hogy az alkalmazásai hogyan teljesítenek, valamint proaktív módon azonosítja a működésüket befolyásoló problémákat és azokat az erőforrásokat, amelyektől függenek.

> [!VIDEO https://www.youtube.com/embed/_hGff5bVtkM]

## <a name="overview"></a>Áttekintés
Az alábbi ábrán egy magas szintű áttekintést nyújt az Azure Monitor. A diagram közepén olyan metrikák és naplók, amely az adatokat használja az Azure Monitor két alapvető típusú adattárakban. A bal oldali vannak a [adatforrásokat, telemetriai adatok gyűjtése a különböző erőforrások figyelt](../azure-monitor/platform/data-sources.md) , és töltse fel a [adattárak](../azure-monitor/platform/data-collection.md). A jobb oldalon a különböző függvényeket, amely az Azure Monitor az elemzés, például a gyűjtött adatok hajt végre, riasztó és a külső rendszerekkel streamelési vannak.


![Az Azure Monitor áttekintése](media/overview/overview.png)


## <a name="monitoring-data-platform"></a>Figyelési adatplatform
Az Azure Monitor által gyűjtött összes adat illeszkedik a két alapvető típusok, [metrikák és naplók](../azure-monitor/platform/data-collection.md). [Metrikák](../azure-monitor/platform/data-collection.md#metrics) időben bizonyos elemeit a rendszer egy adott időpontban leíró numerikus érték. Azok a könnyen használható, és képes a közel valós idejű feldolgozásához. [Naplók](../azure-monitor/platform/data-collection.md#logs) tartalmazhat különböző típusú adatokkal rendelkező különböző tulajdonságokat az egyes rekordok vannak rendezve. Telemetriai adatokat – például az események és nyomok formájában tárolja naplók emellett teljesítményadatokká úgy, hogy azt az összes kombinálható is elemzés céljából.

Sok Azure-erőforrások látni fogja az áttekintő oldala az Azure Portalon az Azure Monitor jobb által gyűjtött adatokat. Például rendelkezik egy pillantást bármelyik virtuális gépet, és látni fogja a teljesítmény-mérőszámok megjelenítése több diagramot. Kattintson bármelyik, nyissa meg az adatokat a gráfok [metrika explorer](../monitoring-and-diagnostics/monitoring-metric-charts.md) az Azure Portalon, amely lehetővé teszi, hogy idővel több mérőszámok értékeit diagram.  A diagramok megjelenítése interaktív módon, vagy egy irányítópultot, megtekintheti őket más vizualizációkat rögzítheti őket.

![Mérőszámok](media/overview/metrics.png)

A Log Analyticsben, amely tartalmazza a tárolt naplóadatokat az Azure Monitor által gyűjtött egy [teljes funkcionalitású lekérdezésnyelvet](../azure-monitor/log-query/log-query-overview.md) gyorsan lekérni, konszolidálhatja és elemezheti az összegyűjtött adatokat.  Hozhat létre és tesztelheti a lekérdezéseket a [Log Analytics lapot](../log-analytics/log-analytics-log-search-portals.md) az Azure Portalon, majd vagy a közvetlenül elemezheti az adatokat ezekkel az eszközökkel, vagy mentheti a lekérdezéseket a segítségével [Vizualizációk](visualizations.md) vagy [ riasztási szabályok](../monitoring-and-diagnostics/monitoring-overview-alerts.md).

A Log Analytics lekérdezési nyelv egyszerű naplólekérdezések alkalmas, de tartoznak az olyan speciális funkciókat, például összesítés, illesztés és intelligens elemzés. Segítségével gyorsan megismerkedhet a lekérdezési nyelv használatával [több leckék](../log-analytics/query-language/get-started-queries.md) elérhető.  Azon felhasználók számára, akik már ismerik az [SQL](../log-analytics/query-language/sql-cheatsheet.md) és [Splunk](../log-analytics/query-language/splunk-cheatsheet.md) használatát, külön útmutató áll rendelkezésére.

![Logs](media/overview/logs.png)

## <a name="what-data-does-azure-monitor-collect"></a>Milyen adatokat gyűjt az Azure Monitor?
Az Azure Monitor tud adatokat gyűjteni a különböző forrásokból. Az alkalmazások és az alkalmazás, a bármely operációs rendszer és a szolgáltatások támaszkodik, lefelé a platform saját maga a csomagban az figyelési felfoghatók. Az Azure Monitor gyűjti az adatokat az alábbi szinteken mindegyike:

- **Alkalmazásfigyelési adatok**: adatok teljesítményéről és a kód funkciójának írt, a platformtól függetlenül.
- **Vendég operációs rendszerek monitorozási adatai**: az operációs rendszer, amelyen fut az alkalmazás adatait. Ez az Azure, egy másik felhőalapú vagy helyszíni fut. 
- **Azure-erőforrások monitorozási adatok**: egy Azure-erőforrás a művelet adatait.
- **Azure-előfizetés monitorozási adatok**: a művelet és a felügyeleti Azure-előfizetés adatait, valamint állapotának és az Azure működésének adatait magát. 
- **Az Azure-bérlő monitorozási adatok**: bérlői szintű Azure-szolgáltatások, például az Azure Active Directory vonatkozó adatokkal.

Amint hoz létre egy Azure-előfizetés és az erőforrások, például virtuális gépek és webalkalmazások hozzáadása kezdő, az Azure Monitor adatgyűjtés megkezdése.  [A Tevékenységnaplók](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md) jegyezze fel, ha erőforrások létrehozásakor vagy módosításakor. [Metrikák](../monitoring-and-diagnostics/monitoring-overview-metrics.md) mondja el, hogyan működik-e az erőforrás és az erőforrásokat, azt használja. 

Kiterjesztheti az adatokat gyűjt be az erőforrások a tényleges művelet [engedélyezi a diagnosztikát](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) és [hozzáadása egy ügynök](../azure-monitor/platform/agent-windows.md) számítási erőforrásokra. Ez a belső művelet az erőforrás telemetriai adatok gyűjtésére és konfigurálását lehetővé különböző [adatforrások](../azure-monitor/platform/agent-data-sources.md) naplók és mérőszámok gyűjtését a Windows és Linux rendszerű vendég operációs rendszer. 

[Adja hozzá az alkalmazáshoz egy kialakítási csomagot](../application-insights/app-insights-azure-web-apps.md), amely lehetővé teszi az Application Insights részletes információkat az alkalmazásról, beleértve a lapmegtekintések, alkalmazásokra irányuló kérések és kivételek gyűjtésére. Továbbá ellenőrizze az alkalmazás rendelkezésre állásának konfigurálásával egy [rendelkezésre állási teszt](../application-insights/app-insights-monitor-web-app-availability.md) érkező felhasználói forgalom szimulálásához.

### <a name="custom-sources"></a>Egyéni források
Az Azure Monitor naplózási adatokat gyűjt a bármely REST-ügyfél használatával a [adatgyűjtő API](../log-analytics/log-analytics-data-collector-api.md). Ez lehetővé teszi, hogy hozzon létre egyéni figyelési helyzeteket, és kiterjesztheti az erőforrásokhoz, amelyek nem teszik elérhetővé a telemetriai adatok segítségével más adatforrások figyelését.



## <a name="insights"></a>Insights
Monitorozási adatok csak akkor hasznos, ha, növelheti a betekintést a számítási környezet a műveletet. Az Azure Monitor számos funkciókat és eszközöket, amelyek az alkalmazások és más erőforrások függenek ezek értékes betekintést nyújtson tartalmazza. [Figyelési megoldások](../azure-monitor/insights/solutions.md) és a szolgáltatások, például a [Application Insights](../application-insights/app-insights-overview.md) és Container Insights nyújt betekintést az alkalmazás és az adott Azure-szolgáltatások különböző aspektusait. 

### <a name="application-insights"></a>Application Insights
[Az Application Insights](../application-insights/app-insights-overview.md) figyeli a rendelkezésre állási, teljesítmény és a webalkalmazások használatának a felhőben vagy a helyszínen tárolva van-e. A hatékony elemzési platform, a Log Analytics nyújt betekintést az alkalmazás-műveletek és hibák diagnosztizálása egy felhasználó jelenti azokat várakozás nélkül a modul. Application Insights kapcsolódási pontok számos különböző fejlesztői eszközöket tartalmaz, és integrálja a Visual Studiót, DevOps-folyamataiba támogatásához.

![App Insights](media/overview/app-insights.png)

### <a name="azure-monitor-for-containers"></a>Azure Monitor tárolókhoz
Az Azure Monitor for containers szolgáltatásban üzembe helyezett Azure Kubernetes Service (AKS) az üzemeltetett Kubernetes-fürtök felügyelt tárolókhoz kapcsolódó számítási feladatok teljesítményének figyelésére tervezett funkciója. Biztosít gyűjtését memória és a mérőszámok a processzor teljesítménye látható-e a tartományvezérlők, a csomópontok és a Kubernetes, a metrikák API-n keresztül a rendelkezésre álló tárolók. A rendszer a tárolónaplókat is gyűjti.  Miután engedélyezte a Kubernetes-fürtök figyelése, ezek a metrikák és naplók automatikusan egy tárolóalapú a Linuxhoz készült Log Analytics-ügynök verziója az Ön számára gyűjtött és tárolja a Log Analyticsben.

![Tároló állapota](media/overview/container-insights.png)

### <a name="azure-monitor-for-vms"></a>Azure Monitor virtuális gépekhez
Az Azure Monitor VM insights az Azure-beli virtuális gépek (VM) ipari méretekben elemzésével, a teljesítmény és a Windows és Linux rendszerű virtuális gépekhez, beleértve azok eltérő folyamatokat és egyéb erőforrások és a külső folyamatok összekapcsolt függőségek állapotát figyeli. A megoldás tartalmaz a teljesítmény figyelése támogatása, és a virtuális gépek alkalmazásfüggőségek üzemeltetett a helyszínen vagy egy másik felhőszolgáltatóval.  


![VM Insights](media/overview/vm-insights.png)

### <a name="monitoring-solutions"></a>Monitorozási megoldások
[Figyelési megoldások](../azure-monitor/insights/solutions.md) az Azure monitorban, amelyeket a csomagolt logika, amelyek elemzéseket nyújtanak egy adott alkalmazás vagy szolgáltatás esetében. Gyűjtenek adatokat a Log Analytics-be más figyelési adatokat, valamint [lekérdezések](../azure-monitor/log-query/log-query-overview.md) elemzéshez és [nézetek](../azure-monitor/platform/view-designer.md) a vizualizációt. Figyelési megoldások [a Microsoft](../azure-monitor/insights/solutions-inventory.md) és partnerek számára a különböző Azure-szolgáltatások és más alkalmazások figyelésére.

![Monitorozási megoldások](media/overview/solutions-overview.png)

## <a name="responding-to-critical-situations"></a>Válaszadás a kritikus fontosságú helyzetek
Mellett lehetővé teszi, hogy a monitorozási adatok interaktív elemzéséhez, egy hatékony monitorozási megoldást proaktív módon reagál a gyűjtött adatok között azonosított kritikus feltételek képesnek kell lennie. Ez sikerült kell küldése a szöveg- vagy e-mail rendszergazda egy probléma kivizsgálásának felelős. Vagy, elindíthat egy automatizált folyamattal, amely megpróbálja hibaállapot kijavítása érdekében.


### <a name="alerts"></a>Riasztások
[Az Azure monitorban riasztásokat](../monitoring-and-diagnostics/monitoring-overview-alerts.md) proaktívan értesíti kritikus feltételek és potenciálisan próbál végezni a javítási műveleteket. Riasztási szabályok metrikák alapján közel valós idejű riasztási numerikus érték alapján, amíg a naplók alapján szabályok átvitelét teszi lehetővé az összetett logikát a több forrásból származó adatokat.

Riasztási szabályok az Azure Monitor használata [Műveletcsoportok](../monitoring-and-diagnostics/monitoring-action-groups.md), amelyek tartalmaznak egyedi címzettek és műveleteket, amelyeket több szabály is megoszthatók. Igényei alapján Műveletcsoportok, webhookok segítségével indítsa el a külső műveletek riasztások vagy integrálása az ITSM-eszközeit az olyan műveleteket hajthat végre.

![Riasztások](media/overview/alerts.png)

### <a name="autoscale"></a>Automatikus méretezés
Az automatikus méretezés lehetővé teszi a megfelelő mennyiségű erőforrást fut az alkalmazás terhelés kezelésére. Lehetővé teszi, hogy mikor automatikusan adja hozzá az erőforrásokat a terhelés növekedését kezelni és is pénzt takaríthat meg, amelyek úgy vannak erőforrások eltávolítása az Azure Monitor által gyűjtött metrikák használó szabályok üresjárati létrehozásához. Azt adja meg a példányok és a logika mikor növelése vagy csökkentése érdekében az erőforrások minimális és maximális számát.

![Automatikus méretezés](media/overview/autoscale.png)

## <a name="visualizing-monitoring-data"></a>Monitorozási adatok vizualizációja
[Vizualizációk](visualizations.md) például diagramok és táblázatok összefoglalójához monitorozási adatok, és a különböző célközönségek bemutató hatékony eszközöket. Az Azure Monitor monitorozási adatok megjelenítéséhez a saját szolgáltatással rendelkezik, és más Azure-szolgáltatások különböző célközönségek közzéteszi használja.

### <a name="dashboards"></a>Irányítópultok
[Azure-irányítópultok](../azure-portal/azure-portal-dashboards.md) egyetlen ablaktábla be lehetővé teszik különböző típusú adatokkal, metrikák és a naplók, beleértve a [az Azure portal](https://portal.azure.com). Szükség esetén az irányítópultot megoszthatja más Azure-felhasználók. Az Azure Monitor eleme mellett minden lekérdezés vagy a metrikák diagram kimenete egy Azure-irányítópultra is hozzáadhatók. Létrehozhat például, hogy egy irányítópultot, amely egyesíti a csempék, amelyek a Log Analytics egy grafikont a metrikák, tevékenységeket tartalmazó naplók tábla, az Application Insightsból használati diagramot és a lekérdezés kimenetének megjelenítése.

![Irányítópult](media/overview/dashboard.png)

### <a name="views"></a>Nézetek
[Az Azure monitorban nézetek](../azure-monitor/platform/view-designer.md) vizuálisan jelenítik meg a naplóadatok a Log Analyticsben.  Mindegyik nézetről tartalmaz egy adott csempe, amely működéseinek Vizualizációk kombinációját le például sáv- és vonaldiagramokat kritikus fontosságú adatok mellett diagramok.  Figyelési megoldások tartalmaznak nézeteket, amelyek egy adott alkalmazáshoz tartozó adatokat, és az adatok bármely Log Analytics naplóbeli keresés a saját nézeteket is létrehozhat. Az Azure monitorban más elemek, például nézetek Azure-irányítópultok lehet hozzáadni.

![Log Analytics-nézet](media/overview/view.png)

### <a name="power-bi"></a>Power BI
[Power bi-ban](https://powerbi.microsoft.com) egy üzleti elemzési szolgáltatás, amely lehetővé teszi az adatforrások különböző interaktív vizualizációkat és a egy hatékony azt jelenti, hogy az adatok elérhetővé tétele mások belül, és a szervezeten kívül van. Beállíthatja, hogy a Power BI [automatikus importálása az Azure Monitor naplóadatok](../log-analytics/log-analytics-powerbi.md) ezeket a további vizualizációkat előnyeinek kihasználása érdekében.


![Power BI](media/overview/power-bi.png)


## <a name="integrate-and-export-data"></a>Integráció és az adatok exportálása
Milyen gyakran kell a követelmény az Azure Monitor integrálható más rendszerekkel és figyelési adatait használó egyéni megoldásokat hozhat létre. Más Azure-szolgáltatások ezt az integrációt biztosít az Azure Monitor szolgáltatással működik.

### <a name="event-hub"></a>Eseményközpont
[Az Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs) van egy streamelési platform és Eseményfeldolgozási szolgáltatás, amely képes adatokat átalakíthatja és tárolhatja bármilyen valós idejű elemzési szolgáltató vagy kötegelési/tárolóadapter segítségével. Az Event Hubs platformot használni [az Azure Monitor log-adatok streamelése az](../monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs.md) partnerré SIEM feladatütemezési és figyelési eszközöket.

> [!VIDEO https://www.youtube.com/embed/SPHxCgbcvSw]

### <a name="logic-apps"></a>Logic Apps
[A Logic Apps](https://azure.microsoft.com/services/logic-apps) egy szolgáltatás, amely lehetővé teszi, hogy a feladatok és a munkafolyamatok, amelyek integrálhatók a különböző rendszerek és szolgáltatások segítségével üzleti folyamatok automatizálása. Tevékenységek érhetők el, amely írási és olvasási metrikák és naplók az Azure monitorban, amely lehetővé teszi, hogy más rendszerek különböző integrálása munkafolyamatokat építhet rájuk.

![Logikai alkalmazás](platform/media/collect-activity-logs-subscriptions/log-analytics-logic-apps-activity-log-overview.png)

### <a name="api"></a>API
Több API-t írási és olvasási metrikákat és naplókat, és az Azure Monitor kívül által előállított riasztások érhetők el. Is konfigurálhatja, és beolvasni a riasztásokat. Ezzel biztosítható az egyéni megoldásokat, amelyek integrálhatók az Azure Monitor gyakorlatilag korlátlan lehetőségeket.

## <a name="next-steps"></a>További lépések
További információk:

* [Metrikák és naplók](../azure-monitor/platform/data-collection.md) az Azure Monitor által összegyűjtött adatokat.
* [Adatforrások](../azure-monitor/platform/data-sources.md) számára az alkalmazás összetevői hogyan telemetriai adatokat küldhet.
* [Log Analytics](../azure-monitor/log-query/log-query-overview.md) összegyűjtött adatok elemzéséhez.
