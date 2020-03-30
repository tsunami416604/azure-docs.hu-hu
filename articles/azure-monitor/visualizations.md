---
title: Adatok megjelenítése az Azure Monitorból | Microsoft dokumentumok
description: Az Azure Monitorban tárolt metrika- és naplóadatok megjelenítéséhez rendelkezésre álló módszerek összegzését tartalmazza.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/17/2020
ms.openlocfilehash: 877616f6fd31bdfbe193bd8f03efb3f79317ad42
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79535369"
---
# <a name="visualizing-data-from-azure-monitor"></a>Adatok megjelenítése az Azure Monitorból
Ez a cikk az Azure Monitorban tárolt napló- és metrikaadatok megjelenítésére rendelkezésre álló módszerek összegzését tartalmazza.

A vizualizációk, például a diagramok és a grafikonok segítségével elemezheti a figyelési adatokat a problémák leásása és a minták azonosítása érdekében. A használt eszköztől függően lehetősége van arra is, hogy a vizualizációkat a szervezeten belüli és kívüli más felhasználókkal is megossza.

## <a name="workbooks"></a>Munkafüzetek
[A munkafüzetek](../azure-monitor/app/usage-workbooks.md) olyan interaktív dokumentumok, amelyek betekintést nyújtanak az adatokba, a vizsgálatba és a csapaton belüli együttműködésbe. Konkrét példák, ahol a munkafüzetek hasznosak a hibaelhárítási útmutatók és az incidens postmortem.

![Munkafüzet](media/visualizations/workbook.png)

### <a name="advantages"></a>Előnyök
- Támogatja a metrikák és a naplók.
- Támogatja az interaktív jelentéseket engedélyező paramétereket, ahol egy tábla elemének kijelölése dinamikusan frissíti a kapcsolódó diagramokat és vizualizációkat.
- Dokumentumszerű folyamat.
- Lehetőség személyes vagy megosztott munkafüzetekhez.
- Egyszerű, együttműködésen alapuló szerzői élmény.
- A sablonok támogatják a nyilvános GitHub-alapú sablongyűjteményt.

### <a name="limitations"></a>Korlátozások
- Nincs automatikus frissítés.
- Nincs olyan sűrű elrendezés, mint az irányítópultok, amelyek a munkafüzeteket egyetlen üvegtáblaként kevésbé hasznossá teszik. Célja, hogy mélyebb betekintést nyújtson.


## <a name="azure-dashboards"></a>Azure-irányítópultok
[Az Azure-irányítópultok](../azure-portal/azure-portal-dashboards.md) az Azure elsődleges irányítópult-technológiája. Különösen hasznosak az Azure-infrastruktúrán és -szolgáltatásokon keresztül egyetlen üvegtábla biztosításában, amely lehetővé teszi a fontos problémák gyors azonosítását.

![Irányítópult](media/visualizations/dashboard.png)

### <a name="advantages"></a>Előnyök
- Mély integráció az Azure-ba. A vizualizációk több Azure-lapról, például a Metrics Explorer, a Log Analytics és az Application Insights irányítópultjaira rögzíthetők.
- Támogatja a metrikák és a naplók.
- Több forrásból származó adatokat egyesíthet, például [a metrikakezelőből,](platform/metrics-charts.md) [a lekérdezések naplózásából,](log-query/log-query-overview.md)valamint a [térképekből](app/app-map.md) és az Application Insights ban való rendelkezésre állásból származó adatokat.
- Lehetőség személyes vagy megosztott irányítópultokhoz. Integrálva az Azure [szerepköralapú hitelesítésével (RBAC).](../role-based-access-control/overview.md)
- Automatikus frissítés. A metrikák frissítése legalább öt perc időtartománytól függ. A naplók óránként frissülnek, igény szerint manuális frissítési lehetőséggel, ha egy adott vizualizáció "frissítés" ikonjára kattintanak, vagy frissítik a teljes irányítópultot.
- Parametrizált metrikák irányítópultok időbélyeggel és egyéni paraméterekkel.
- Rugalmas elrendezési lehetőségek.
- Teljes képernyős módban.


### <a name="limitations"></a>Korlátozások
- A naplóvizualizációk korlátozott vezérlése adattáblák támogatása nélkül. Az adatsorok teljes száma 10-re korlátozódik, és további adatsorok egy _másik_ gyűjtő alá vannak csoportosítva.
- A naplódiagramok nem támogatják az egyéni paramétereket.
- A naplódiagramok az elmúlt 30 napra korlátozódnak.
- A naplódiagramok csak megosztott irányítópultokhoz rögzíthetők.
- Nincs interaktivitás az irányítópult adataival.
- Korlátozott környezetfüggő részletezés.


## <a name="power-bi"></a>Power BI
A [Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/) különösen hasznos az üzleti központú irányítópultok és jelentések létrehozásához, valamint a hosszú távú KPI-trendek elemzéséhez. A [naplólekérdezés eredményeit importálhatja egy](platform/powerbi.md) Power BI-adatkészletbe, így kihasználhatja annak funkcióit, például a különböző forrásokból származó adatok kombinálását és a jelentések megosztását az interneten és a mobileszközökön.

![Power BI](media/visualizations/power-bi.png)

### <a name="advantages"></a>Előnyök
- Gazdag képi megjelenítések.
- Kiterjedt interaktivitás, beleértve a nagyítást és a keresztszűrést.
- Könnyen megosztható az egész szervezetben.
- Integráció több adatforrásból származó más adatokkal.
- Jobb teljesítmény a kockában gyorsítótárazott eredményekkel.


### <a name="limitations"></a>Korlátozások
- Támogatja a naplókat, de a metrikákat nem.
- Nincs Azure-integráció. Az Azure Resource Manager segítségével nem lehet kezelni az irányítópultokat és a modelleket.
- A beállításokhoz a lekérdezési eredményeket importálni kell a Power BI-modellbe. Az eredmény méretének és frissítésének korlátozása.
- Korlátozott adatfrissítés naponta nyolc alkalommal.


## <a name="grafana"></a>Grafana
[A Grafana](https://grafana.com/) egy nyílt platform, amely kiemelkedik az operatív irányítópultokon. Különösen hasznos a működési események észleléséhez, elkülönítéséhez és osztályozásáért. A [Grafana Azure Monitor adatforrás-beépülő modult](platform/grafana-plugin.md) hozzáadhatja az Azure-előfizetéséhez, hogy vizualizálja az Azure-metrikák adatait.

![Grafana](media/visualizations/grafana.png)

### <a name="advantages"></a>Előnyök
- Gazdag képi megjelenítések.
- Az adatforrások gazdag ökoszisztémája.
- Adatinteraktivitás, beleértve a nagyítást.
- Támogatja a paramétereket.

### <a name="limitations"></a>Korlátozások
- Nincs Azure-integráció. Az Azure Resource Manager segítségével nem lehet kezelni az irányítópultokat és a modelleket.
- A Grafana infrastruktúra további támogatásának költsége vagy a Grafana Cloud többletköltsége.


## <a name="build-your-own-custom-application"></a>Saját egyéni alkalmazás létrehozása
Az Azure Monitorban a napló- és metrikaadatokhoz bármely REST-ügyfél használatával hozzáférhet a napló- és metrikaadatokhoz, így saját egyéni webhelyeket és alkalmazásokat hozhat létre.

### <a name="advantages"></a>Előnyök
- Teljes rugalmasság a felhasználói felületen, a vizualizációban, az interaktivitásban és a funkciókban.
- A mérőszámok és a naplóadatok kombinálása más adatforrásokkal.

### <a name="disadvantages"></a>Hátrányok
- Jelentős mérnöki erőfeszítésre van szükség.


## <a name="azure-monitor-views"></a>Azure figyelőnézetek

> [!IMPORTANT]
> A nézetek elavultak. Tekintse meg az [Azure Monitor nézet tervezője a munkafüzetek átmeneti útmutató](platform/view-designer-conversion-overview.md) útmutatást konvertáló nézetek munkafüzetek.

[Az Azure Monitor nézetei](platform/view-designer.md) lehetővé teszik, hogy egyéni vizualizációkat hozzon létre a naplóadatokkal. Ezeket [a felügyeleti megoldások](insights/solutions.md) segítségével használják az általuk gyűjtött adatok bemutatására.


![Nézet](media/visualizations/view.png)

### <a name="advantages"></a>Előnyök
- A naplóadatok bővített vizualizációi.
- Nézetek exportálása és importálása más erőforráscsoportokba és előfizetésekbe való átviteléhez.
- Integrálható az Azure Monitor felügyeleti modelljébe munkaterületekkel és figyelési megoldásokkal.
- [Szűrők](platform/view-designer-filters.md) egyéni paraméterekhez.
- Interaktív, támogatja a többszintű részletezést (egy másik nézetben fúródó nézet)

### <a name="limitations"></a>Korlátozások
- Támogatja a naplókat, de a metrikákat nem.
- Nincs személyes véleményem. A munkaterülethez hozzáféréssel rendelkező összes felhasználó számára elérhető.
- Nincs automatikus frissítés.
- Korlátozott elrendezési lehetőségek.
- Nem támogatja a több munkaterületen vagy az Application Insights-alkalmazások ban lekérdezés.
- A lekérdezések válaszmérete 8 MB és a lekérdezés 110 másodperces végrehajtási ideje korlátozott.

## <a name="next-steps"></a>További lépések
- Ismerje meg az [Azure Monitor által gyűjtött adatokat.](platform/data-platform.md)
- További információ [az Azure-irányítópultokról.](../azure-portal/azure-portal-dashboards.md)
- További információ [az Azure Monitor nézeteiről.](platform/view-designer.md)
- További információ [a munkafüzetekről.](../azure-monitor/app/usage-workbooks.md)
- További információ [a naplóadatok Power BI-ba történő importálásáról.](../azure-monitor/platform/powerbi.md)
- További információ a [Grafana Azure Monitor adatforrásbeépülő modulról.](../azure-monitor/platform/grafana-plugin.md)

