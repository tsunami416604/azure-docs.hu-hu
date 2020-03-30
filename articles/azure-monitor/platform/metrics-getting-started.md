---
title: Az Azure metrics explorer – első lépések
description: Ismerje meg, hogyan hozhatja létre az első metrikadiagramot az Azure metrics explorer segítségével.
author: vgorbenko
services: azure-monitor
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: vitalyg
ms.subservice: metrics
ms.openlocfilehash: 920ff19b162843d1c2d631ad192ce1b3c4273a68
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79248774"
---
# <a name="getting-started-with-azure-metrics-explorer"></a>Bevezetés az Azure Metrikaböngésző használatába

## <a name="where-do-i-start"></a>Hol kezdjem
Az Azure Monitor metrikakezelője a Microsoft Azure portal egyik összetevője, amely lehetővé teszi a diagramok nyomtatását, a trendek vizuális korrelációját, valamint a metrikák értékeinek kiugrásait és csökkenését. A metrikakezelő segítségével vizsgálja meg az erőforrások állapotát és kihasználtságát. Kezdje a következő sorrendben:

1. [Válasszon ki egy erőforrást és egy mutatót,](#create-your-first-metric-chart) és megjelenik egy alapdiagram. Ezután válassza ki a vizsgálat szempontjából releváns [időtartományt.](#select-a-time-range)

1. Próbálja meg [alkalmazni a dimenziószűrőket és a felosztást.](#apply-dimension-filters-and-splitting) A szűrők és a felosztás lehetővé teszi annak elemzését, hogy a metrika mely szegmensei járulnak hozzá a teljes metrikaértékhez, és azonosíthatja a lehetséges kiugró értékeket.

1. Speciális [beállításokkal](#advanced-chart-settings) testreszabhatja a diagramot, mielőtt az irányítópultokra tűzne. [Állítsa be a riasztásokat,](alerts-metric-overview.md) hogy értesítéseket kapjon, ha a metrika értéke meghaladja vagy egy küszöbérték alá csökken.

## <a name="create-your-first-metric-chart"></a>Az első mérőszámdiagram létrehozása

Metrikadiagram létrehozásához az erőforrás-, erőforráscsoport-, előfizetési vagy Azure Monitor nézetből nyissa meg a **Metrikák** lapot, és kövesse az alábbi lépéseket:

1. Az erőforrás-választó használatával válassza ki azt az erőforrást, amelynek a metrikákat szeretné látni. (Az erőforrás előre be van jelölve, ha egy adott erőforrás környezetében nyitotta meg a **Metrikákat).**

    > ![Erőforrás kiválasztása](./media/metrics-getting-started/resource-picker.png)

2. Egyes erőforrások hoz ki kell választania egy névteret. A névtér csak egy módja annak, hogy a metrikákat rendszerezze, így könnyen megtalálhatja őket. A tárfiókok például külön névterekkel rendelkeznek a fájlok, táblázatok, blobok és várólisták metrikák tárolására. Sok erőforrástípusnak csak egy névtere van.

3. Válasszon ki egy metrikát az elérhető mérőszámok listájából.

    > ![Mérőszám kiválasztása](./media/metrics-getting-started/metric-picker.png)

4. Szükség esetén módosíthatja a metrika összesítését. Előfordulhat például, hogy azt szeretné, hogy a diagram a mérőszám minimális, maximális vagy átlagos értékeit jelenítse meg.

> [!NOTE]
> Használja a **Metrika hozzáadása** gombot, és ismételje meg ezeket a lépéseket, ha több metrikát szeretne ábrázolni ugyanabban a diagramban. Ha egy nézetben több diagramot szeretne, kattintson felül a **Diagram hozzáadása** gombra.

## <a name="select-a-time-range"></a>Időtartomány kiválasztása

Alapértelmezés szerint a diagram a legutóbbi 24 órányi mérőszámadatot jeleníti meg. Az **időválasztó** panelsegítségével módosíthatja az időtartományt, nagyíthat vagy kicsinyíthet a diagramon. 

![Időtartomány-panel módosítása](./media/metrics-getting-started/time-picker.png)

> [!NOTE]
> Használja az **idő ecsetet,** hogy vizsgálja meg egy érdekes terület a diagram (tüske vagy dip). Helyezze az egérmutatót a terület elejére, kattintson a bal egérgombbal, húzza a mutatót a terület másik oldalára, majd engedje fel a gombot. A diagram ráközelít az adott időtartományra. 

## <a name="apply-dimension-filters-and-splitting"></a>Dimenziószűrők és felosztás alkalmazása

[A szűrés](metrics-charts.md#apply-filters-to-charts) és [a felosztás](metrics-charts.md#apply-splitting-to-a-chart) hatékony diagnosztikai eszközök a dimenziókkal rendelkező metrikákhoz. Ezek a funkciók azt mutatják, hogy a különböző metrikaszegmensek ("dimenzióértékek") hogyan befolyásolják a metrika teljes értékét, és lehetővé teszik a lehetséges kiugró értékek azonosítását.

- **A szűrés** lehetővé teszi annak kiválasztását, hogy mely dimenzióértékek szerepeljenek a diagramban. Előfordulhat például, hogy a *kiszolgáló válaszidejének* ábrázolásakor sikeres kérelmeket szeretne megjeleníteni. A szűrőt a *kérelemdimenzió sikerességére* kell alkalmaznia. 

- **A felosztás** határozza meg, hogy a diagram külön sorokat jelenít-e meg egy dimenzió egyes értékéhez, vagy az értékeket egyetlen sorba összesíti. Például egy sort láthat az átlagos válaszidőből az összes kiszolgálópéldányon, vagy külön sorokat láthat az egyes kiszolgálókhoz. A külön sorok megtekintéséhez felosztást kell alkalmaznia a *kiszolgálópéldány* dimenziójára.

Példák a szűrést és felosztást alkalmazó [diagramokra.](metric-chart-samples.md) A cikk a diagramok konfigurálásához használt lépéseket mutatja be.

## <a name="advanced-chart-settings"></a>Speciális diagrambeállítások

Testre szabhatja a diagram stílusát, címét és módosíthatja a speciális diagrambeállításokat. Ha végzett a testreszabással, rögzítse egy irányítópultra a munka mentéséhez. Metrikák riasztások is konfigurálhatja. A [termékdokumentációban](metrics-charts.md) megismerheti az Azure Monitor metrikakezelőjének ezeket és egyéb speciális funkcióit.

## <a name="next-steps"></a>További lépések

* [További információ a Metrics Explorer speciális funkcióiról](metrics-charts.md)
* [A Metrikaböngésző hibaelhárítása](metrics-troubleshoot.md)
* [Az Azure-szolgáltatásokhoz elérhető metrikák listájának megtekintése](metrics-supported.md)
* [A konfigurált diagramok példáinak megtekintése](metric-chart-samples.md)
