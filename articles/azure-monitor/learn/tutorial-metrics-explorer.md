---
title: Oktatóanyag – Metrikadiagram létrehozása az Azure Monitorban
description: Ismerje meg, hogyan hozhatja létre az első metrikadiagramot az Azure metrics explorer segítségével.
author: bwren
ms.author: bwren
ms.subservice: metrics
ms.topic: tutorial
ms.date: 03/09/2020
ms.openlocfilehash: d8f72471dd3109bf584d18f032ec2f4d949a4993
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "79082812"
---
# <a name="tutorial-create-a-metrics-chart-in-azure-monitor"></a>Oktatóanyag: Metrikadiagram létrehozása az Azure Monitorban
A Metrikakezelő az Azure-portálAzure Monitorszolgáltatásának egyik szolgáltatása, amely lehetővé teszi, hogy metrikaértékekből hozzon létre diagramokat, vizuálisan korrelálja a trendeket, és vizsgálja meg a metrikaértékek kiugrásait és csökkenését. A metrikakezelő segítségével vizsgálja meg az Azure-erőforrások állapotát és kihasználtságát, vagy ábrázolja a diagramokat az egyéni metrikákból. 

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Válassza ki azt a mérőszámot, amelynek diagramot szeretne ábrázolni
> * Metrikus értékek különböző összesítéseinek végrehajtása
> * A diagram időtartományának és részletességének módosítása

A következőkben egy videó, amely bemutatja egy kiterjedtebb forgatókönyv, mint a cikkben ismertetett eljárás. Ha most ismered a metrikákat, javasoljuk, hogy először olvasd el ezt a cikket, majd nézd meg a videót, hogy több konkrétumot láthass. 

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4qO59]

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag befejezéséhez egy Azure-erőforrást kell figyelnie. Az Azure-előfizetésben bármilyen erőforrást használhat, amely támogatja a metrikákat. Annak megállapításához, hogy egy erőforrás támogatja-e a metrikákat, nyissa meg a menüt az Azure Portalon, és ellenőrizze, hogy van-e **metrikák** lehetőség a **figyelési** szakaszban a menüben.


## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba
Jelentkezzen be az Azure [https://portal.azure.com](https://portal.azure.com)Portalon a rendszerbe.

## <a name="open-metrics-explorer-and-select-a-scope"></a>Nyissa meg a metrikakezelőt, és válasszon ki egy hatókört
Metrika-kezelőt az Azure Monitor menüből vagy az Azure Portal egyik erőforrás-menüjéből nyithat meg. Az összes erőforrás metrikák érhetők el, függetlenül attól, hogy melyik lehetőséget használja. 

1. Válassza ki a **metrikák** az **Azure Monitor** menüben, vagy a **figyelési** szakaszegy erőforrás menüben.

1. Válassza ki a **Hatókör**lehetőséget, amely az az erőforrás, amelyhez a metrikákat látni szeretné. A hatókör már ki van töltve, ha egy erőforrás menüjéből megnyitotta a metrikakezelőt.

    ![Hatókör kijelölése](media/tutorial-metrics-explorer/scope-picker.png)

2. Ha a hatókör egynél több névtérrel rendelkezik, jelöljön ki **egy névteret.** A névtér csak egy módja annak, hogy a metrikákat rendszerezze, így könnyen megtalálhatja őket. A tárfiókok például külön névterekkel rendelkeznek a fájlok, táblázatok, blobok és várólisták metrikák tárolására. Sok erőforrástípusnak csak egy névtere van.

3. Válasszon ki egy metrikát a kiválasztott hatókörhöz és névtérhez rendelkezésre álló metrikák listájából.

    ![Mérőszám kiválasztása](media/tutorial-metrics-explorer/metric-picker.png)

4. Szükség esetén módosítsa a metrika **összesítését.** Ez határozza meg, hogy a metrikaértékek hogyan lesznek összesítve a diagram időrészletessége között. Ha például az idő részletessége 15 percre van állítva, és az összesítés összegre van állítva, akkor a diagram minden pontja az egyes 15 perces szegmensek összes összegyűjtött értékének összege lesz.

    ![Diagram](media/tutorial-metrics-explorer/chart.png)

5. Használja a **Metrika hozzáadása** gombot, és ismételje meg ezeket a lépéseket, ha több metrikát szeretne ábrázolni ugyanabban a diagramban. Ha egy nézetben több diagramot szeretne, válassza az **Új diagram gombot.**

## <a name="select-a-time-range-and-granularity"></a>Időtartomány és részletesség kiválasztása

Alapértelmezés szerint a diagram a legutóbbi 24 órányi mérőszámadatot jeleníti meg. Az időválasztóval módosíthatja a diagram **időtartományát** vagy az **idő részletességét,** amely az egyes adatpontok időtartományát határozza meg. A diagram a megadott összesítés thasználja az összes mintavételezett értéket a megadott idő részletessége alapján.

![Időtartomány-panel módosítása](media/tutorial-metrics-explorer/time-picker.png)


Használja az **időecsetet,** hogy vizsgálja meg egy érdekes terület a diagram, mint a tüske vagy a dip. Helyezze az egérmutatót a terület elejére, kattintson a bal egérgombbal, húzza a mutatót a terület másik oldalára, és engedje fel a gombot. A diagram ráközelít az adott időtartományra. 

![Időecset](media/tutorial-metrics-explorer/time-brush.png)

## <a name="apply-dimension-filters-and-splitting"></a>Dimenziószűrők és felosztás alkalmazása
Tekintse meg az alábbi hivatkozásokat a speciális funkciók, amelyek lehetővé teszik, hogy további elemzést a metrikák, és azonosítani a potenciális kiugró értékek az adatokban.

- [A szűrés](../platform/metrics-charts.md#apply-filters-to-charts) lehetővé teszi annak kiválasztását, hogy mely dimenzióértékek szerepeljenek a diagramban. Előfordulhat például, hogy csak a sikeres kérelmeket szeretné megjeleníteni a *kiszolgáló válaszidejének* ábrázolásakor. 

- [A felosztás](../platform/metrics-charts.md#apply-splitting-to-a-chart) határozza meg, hogy a diagram külön sorokat jelenít-e meg egy dimenzió egyes értékéhez, vagy az értékeket egyetlen sorba összesíti. Előfordulhat például, hogy egy sort szeretne látni egy átlagos válaszidőhöz az összes kiszolgálópéldányon, vagy minden kiszolgálóhoz külön sorokat szeretne. 

Példák a szűrést és felosztást alkalmazó [diagramokra.](../platform/metric-chart-samples.md)

## <a name="advanced-chart-settings"></a>Speciális diagrambeállítások

Testre szabhatja a diagram stílusát, címét és módosíthatja a speciális diagrambeállításokat. Ha végzett a testreszabással, rögzítse egy irányítópultra a munka mentéséhez. Metrikák riasztások is konfigurálhatja. Tekintse meg [az Azure Metrics Explorer speciális funkcióit,](../platform/metrics-charts.md#lock-boundaries-of-chart-y-axis) és ismerje meg ezeket és az Azure Monitor metrikakezelőjének egyéb speciális funkcióit.


## <a name="next-steps"></a>További lépések
Most, hogy megtanulta, hogyan dolgozhat a metrikákkal az Azure Monitorban, megtudhatja, hogyan használhatja a metrikákat proaktív riasztások küldésére.

> [!div class="nextstepaction"]
> [Metrikariasztások létrehozása, megtekintése és kezelése az Azure Monitorral](../platform/alerts-metric.md)

