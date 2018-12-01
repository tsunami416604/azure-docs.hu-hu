---
title: Az Azure Monitor vizualizációja |} A Microsoft Docs
description: A metrikák tároló és a Log Analytics származó adatok többek között az Azure Monitor-ban tárolt adatok megjelenítése a választható módszerek összegzését tartalmazza.
author: bwren
manager: carmonm
editor: ''
services: azure-monitor
documentationcenter: azure-monitor
ms.service: azure-monitor
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 09/15/2018
ms.author: bwren
ms.openlocfilehash: b2011003306e72ac9fb243b640e388e6f3d5e7d3
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/01/2018
ms.locfileid: "52722207"
---
# <a name="visualizing-data-from-azure-monitor"></a>Az Azure Monitor az adatok megjelenítése
Ez a cikk az Azure Monitor-ban tárolt adatok megjelenítése a választható módszerek összegzését tartalmazza. Ez magában foglalja [mérőszámok az Azure-metrikagyűjtéshez áruházban](../azure-monitor/platform/data-collection.md#metrics) és [adatok Log Analytics naplóbeli](../azure-monitor/platform/data-collection.md#logs). 

Vizualizációk, például diagramokat és ábrákat segíthet a problémák részletezés és a minták azonosítása monitorozási adatok elemzése. Attól függően, a használt eszköz Vizualizációk megoszthatók más felhasználókkal belüli és kívüli a szervezet is előfordulhat.

## <a name="azure-dashboards"></a>Azure-irányítópultok
[Azure-irányítópultok](../azure-portal/azure-portal-dashboards.md) az elsődleges dashboarding technológia vannak az Azure-hoz. Ez különösen hasznos az egyetlen biztosít az Azure-infrastruktúra és a szolgáltatások, így gyorsan azonosíthatja a fontos problémák keresztül zajlik.

![Irányítópult](media/visualizations/dashboard.png)

### <a name="advantages"></a>Előnyök
- Szoros integráció az Azure-bA. Vizualizációkat több Azure oldalra, beleértve a metrikák explorer, a Log Analytics és az Application Insights kitűzhetők az irányítópultokra.
- Metrikák és naplók is támogatja.
- Többek között a kimenetét a több forrásból származó adatokat kombinálni [metrikaböngésző](../monitoring-and-diagnostics/monitoring-metric-charts.md), [Log Analytics-lekérdezések](../log-analytics/log-analytics-queries.md), és [térképek](../application-insights/app-insights-app-map.md) és [rendelkezésre állási]()az Application Insightsban.
- Saját vagy megosztott irányítópultok beállítása. Az Azure-ral integrálva [szerepköralapú hitelesítést (RBAC)](../role-based-access-control/overview.md).
- Automatikus frissítés. Metrikák frissítési időtartomány, legalább öt perc alatt függ. Naplók frissítése, egy perc.
- Paraméteres metrikák irányítópultok időbélyeg és egyéni paraméterekkel.
- Rugalmas elrendezés beállításai.
- Teljes képernyős módban.


### <a name="limitations"></a>Korlátozások
- Korlátozott szabályozhatja a Log Analytics megjelenítési módokat adattáblák nem támogatott. További adatsorok vannak összegyűjtve legfeljebb 10 az adatsorozatok száma egy _más_ gyűjtőbe.
- A Log Analytics-diagramok egyéni paraméterek nélkül támogatja.
- Log Analytics-diagramok korlátozva, az elmúlt 30 napban.
- Log Analytics diagramok csak megosztott irányítópultokat rögzíthet.
- Nem interaktív irányítópult adatokkal.
- Korlátozott környezetfüggő részletezése.

## <a name="azure-monitor-views"></a>Az Azure Monitor-nézetek
[Az Azure monitorban nézetek](../azure-monitor/platform/view-designer.md) hozhat létre egyéni vizualizációkat az Log Analytics szolgáltatásban tárolt naplóadatokért. Által használt [figyelési megoldások](../azure-monitor/insights/solutions.md) a gyűjtött adatok.

![Nézet](media/visualizations/view.png)

### <a name="advantages"></a>Előnyök
- Sokoldalú megjelenítési lehetőségek a Log Analytics-adatok.
- Exportálása és importálása a át más erőforráscsoportok és előfizetések nézeteket.
- Integrálja a Log Analytics felügyeleti modell munkaterületek és figyelési megoldásokkal.
- [Szűrők](../azure-monitor/platform/view-designer-filters.md) egyéni paraméterek.
- Interaktív, támogatja a többszintű részletezés-a (nézet, amely egy másik nézetre működéseinek)

### <a name="limitations"></a>Korlátozások
- Támogatja a naplók átjárómetrikákra nem vonatkozik.
- Személyes nézet sem. A munkaterület-hozzáféréssel rendelkező minden felhasználó számára elérhető.
- Nincs automatikus frissítését.
- Az elrendezési beállítások korlátozott.
- Log Analytics-munkaterületek és Application Insights-alkalmazások lekérdezése nem támogatott.
- Válasz mérete 110 másodperc 8MB, a lekérdezés végrehajtási ideje a lekérdezések korlátozott.



## <a name="application-insights-workbooks"></a>Application Insights munkafüzetek
[Munkafüzetek](../application-insights/app-insights-usage-workbooks.md) adataihoz, vizsgálati és együttműködési belül a team betekintést nyújtó interaktív dokumentumok. Útmutatók és az incidens postmortem konkrét példákat, ahol munkafüzetek hasznosak hibaelhárítást.

![Munkafüzet](media/visualizations/workbook.png)

### <a name="advantages"></a>Előnyök
- Metrikák és naplók is támogatja.
- Támogatja a paramétereket interaktív jelentéseket, ahol dinamikusan lesz egy elemet kell választania egy tábla engedélyezése társított diagramok és Vizualizációk frissítése.
- A dokumentum-hez hasonló folyamatot.
- Beállítás személyes vagy megosztott munkafüzetek.
- Egyszerű és együttműködő-barát szerkesztőfelületen.
- Sablonok nyilvános GitHub-alapú sablonkatalógus támogatja.

### <a name="limitations"></a>Korlátozások
- Nincs automatikus frissítését.
- Nincs sűrű elrendezés például irányítópultok, győződjön meg arról, munkafüzetek tekinthesse egyetlen ablaktábla kevésbé hasznos. Szánt többet részletesebb elemzéseket biztosít.


## <a name="power-bi"></a>Power BI
[Power bi-ban](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/) különösen hasznos üzleti-központú irányítópultokat és jelentéseket, valamint hosszú távú KPI-trendek elemzése jelentések létrehozásához. Is [importálja a Log Analytics-lekérdezés eredményeit](../log-analytics/log-analytics-powerbi.md) így kihasználhatja annak szolgáltatásait, például a különböző forrásokból származó adatok összevonása és a webes és mobil eszközök jelentések megosztása a Power BI-adatkészletbe.

![Power BI](media/visualizations/power-bi.png)

### <a name="advantages"></a>Előnyök
- Sokoldalú megjelenítési lehetőségek.
- Interaktív, beleértve a nagyítás és keresztszűrés külterjes.
- Könnyen megosztható a szervezeten belül.
- Integráció más különböző forrásokból származó adatokat.
- Jobb teljesítmény érdekében a kockákban lévő gyorsítótárazott eredményeket.


### <a name="limitations"></a>Korlátozások
- Támogatja a naplók átjárómetrikákra nem vonatkozik.
- Nem Azure-integráció. Nem tudja kezelni az irányítópultok és -modellek Azure Resource Manageren keresztül.
- Lekérdezési eredmények konfigurálása Power BI-modellben lehet importálni kell. Az eredmény méretének és frissítési korlátozása.
- Korlátozott adatfrissítési naponta 8 alkalommal.


## <a name="grafana"></a>Grafana
[Grafana](https://grafana.com/) nyílt platformon, amely az operatív irányítópultokat excels van. Ez különösen hasznos észlelése és elkülönítése és működési események sorolása. Hozzáadhat [Grafana az Azure Monitor adatforrás-beépülő modul](../monitoring-and-diagnostics/monitor-send-to-grafana.md) az Azure-előfizetéshez, hogy az Azure-metrikagyűjtéshez adatok megjelenítése.

![Grafana](media/visualizations/grafana.png)

### <a name="advantages"></a>Előnyök
- Sokoldalú megjelenítési lehetőségek.
- Gazdag ökoszisztémája az adatforrások.
- Adatok interaktív többek között a nagyítást.
- Paramétereket támogatja.

### <a name="limitations"></a>Korlátozások
- Támogatja a mérőszámok használatát, de nem naplózza.
- Nem Azure-integráció. Nem tudja kezelni az irányítópultok és -modellek Azure Resource Manageren keresztül.
- További Grafana infrastruktúrát vagy a további költségek támogatása a Grafana felhő költsége.


## <a name="build-your-own-custom-application"></a>A saját egyéni application készítése
Bármely REST-ügyfél, amely lehetővé teszi, hogy a saját egyéni webhelyek és alkalmazások készítése és használata saját API-n keresztül férhet hozzá az Azure-metrikák és a Log Analytics adataihoz.

### <a name="advantages"></a>Előnyök
- Felhasználói felület, képi megjelenítés, interaktív és funkciók teljes körű rugalmasság.
- Egyesítheti és naplózni az adatokat más adatforrásokkal.

### <a name="disadvantages"></a>Hátrányai
- Jelentős mérnöki erőfeszítés szükséges.


## <a name="next-steps"></a>További lépések
- További információ a [Azure Monitor által gyűjtött adatok](../azure-monitor/platform/data-collection.md).
- Ismerje meg [Azure-irányítópultok](../azure-portal/azure-portal-dashboards.md).
- Ismerje meg [nézetek az Azure Monitor](../azure-monitor/platform/view-designer.md).
- Ismerje meg [az Application Insights-munkafüzetek](../application-insights/app-insights-usage-workbooks.md).
- Ismerje meg [Teljesítménynapló-adatok importálása a Power BI-bA](../log-analytics/log-analytics-powerbi.md).
- További információ a [Grafana az Azure Monitor adatforrás-beépülő modul](../monitoring-and-diagnostics/monitor-send-to-grafana.md).
