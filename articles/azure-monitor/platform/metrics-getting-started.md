---
title: Ismerkedés az Azure metrikaböngésző
description: Ismerje meg, hogyan hozhat létre az első metrikadiagram Azure metrikaböngésző.
author: vgorbenko
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: vitalyg
ms.subservice: metrics
ms.openlocfilehash: e611f1564896cfdecb3ce34ab7c5361e5200b48a
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57537337"
---
# <a name="getting-started-with-azure-metrics-explorer"></a>Ismerkedés az Azure Metrikaböngésző

## <a name="where-do-i-start"></a>Hogyan kezdjek hozzá
Az Azure Monitor metrikaböngészőjének összetevő, amely lehetővé teszi a ábrázolási diagramok, vizuálisan naplókezelője trendeket, és vizsgálja a csúcsok és süllyedések értékeihez ' a Microsoft Azure-portálon. A metrikaböngésző használatával vizsgálja meg az állapotát és az erőforrások kihasználását. Indítsa el a következő sorrendben:

1. [Válasszon ki egy erőforrást és a egy metrika](#create-your-first-metric-chart) és a egy egyszerű diagramot láthatja. Ezután [jelöljön ki egy időtartományt](#select-a-time-range) Ez a vizsgálat céljából.

1. Próbálja ki [dimenzió szűrők alkalmazása és a felosztás](#apply-dimension-filters-and-splitting). A szűrők és a felosztás, elemezheti, hogy melyik szegmens a mérőszám a teljes metrikaérték hozzájárul, és lehetséges kiugró értékek azonosításához.

1. Használat [speciális beállítások](#advanced-chart-settings-and-next-steps) szabhatja testre a diagram az irányítópultokon rögzítés előtt. [Riasztások beállítása](alerts-metric-overview.md) , hogy értesítést kapjon, amikor metrika értéke meghaladja a, vagy a küszöbérték alá csökken.

## <a name="create-your-first-metric-chart"></a>Az első metrikadiagram létrehozása

Metrikadiagram, az erőforrás, erőforráscsoport, előfizetés vagy az Azure Monitor nézet létrehozásához nyissa meg a **metrikák** lapra, és kövesse az alábbi lépéseket:

1. Az erőforrás-választó, válassza ki azt az erőforrás, amelynek meg szeretné tekinteni a metrikákat. (Az erőforrás előre kiválasztott, ha megnyitotta **metrikák** keretén belül egy adott erőforrás).

    > ![Erőforrás kiválasztása](./media/metrics-getting-started/resource-picker.png)

2. Az egyes erőforrásokat válasszon ki egy névteret. A névtér csak egy módja metrikák rendszerezheti, hogy könnyen megtalálhassa őket. Ha például tárfiókban elérhető metrikák fájlok, táblák, Blobok és üzenetsorok tárolására szolgáló különálló névterek. Számos erőforrástípusok csak egy névtérrel rendelkezzen.

3. Elérhető mérőszámok listájából válassza ki egy metrikát.

    > ![Metrika kiválasztása](./media/metrics-getting-started/metric-picker.png)

4. A metrika aggregációs igény szerint módosíthatja. Például érdemes lehet a diagram a mérőszám a minimális, maximális és átlagos értékek megjelenítése.

> [!NOTE]
> Használja a **metrika hozzáadása** gombra, és ismételje meg ezeket a lépéseket, ha szeretné megtekinteni a több metrikát a diagram ugyanaz megjelennek. Egyetlen nézetben több diagramot, válassza a **Hozzáadás diagram** felül gombra.

## <a name="select-a-time-range"></a>Időtartomány megadása

Alapértelmezés szerint a diagram bemutatja a metrikák adatait a legutóbbi 24 óra. Használja a **időválasztó** panelen módosíthatja az időtartományt, nagyítás vagy kicsinyítés a diagramon. 

![Módosítás ideje értéktartomány panel](./media/metrics-getting-started/time-picker.png)

## <a name="apply-dimension-filters-and-splitting"></a>Dimenzió szűrőket és a felosztás

[Szűrés](metrics-charts.md#apply-filters-to-charts) és [felosztás](metrics-charts.md#apply-splitting-to-a-chart) hatékony diagnosztikai eszközöket a mérőszámhoz, amely rendelkezik a dimenziók vannak. Ezek a funkciók különböző metrika szegmensek ("dimenzió values") hatás megjelenítése a mérőszám a teljes értékét, és lehetővé teszi lehetséges kiugró értékek azonosításához.

- **Szűrés** kiválaszthatja, melyik dimenzió értékei szerepelnek a diagramot. Például érdemes sikeres kérések megjelenítése, ha a diagram a *kiszolgáló válaszideje* metrikát. A szűrőt a alkalmazni kell a *kérés sikeres* dimenzió. 

- **Felosztás** szabályozza-e a diagram megjelenítése külön minden egyes érték egy dimenzió vonalak vagy összesíti az értékeket egyetlen sorba. Például egy sor találja az átlagos válaszidő az összes server-példányok között, vagy tekintse meg a minden kiszolgálón külön sorban. Felosztás a alkalmazni kell a *kiszolgálópéldány* dimenzió külön sorban megtekintéséhez.

Lásd: [példák a diagramok](metric-chart-samples.md) , szűrés és a felosztás alkalmazott. A cikk ismerteti a lépéseket a diagramok konfigurálásához használt.

## <a name="advanced-chart-settings-and-next-steps"></a>Speciális diagram beállításai és a további lépésekről

Diagram stílus, cím, és a speciális diagram beállításainak módosítása. Miután végzett a testreszabás, rögzítheti is egy irányítópultot, hogy mentse a munkáját. Metrikák riasztásokat is lehet konfigurálni. Hajtsa végre a [termékdokumentáció](metrics-charts.md) ezeket és más speciális funkciókat az Azure Monitor metrikaböngészőjének.

## <a name="next-steps"></a>További lépések

* [Rendelkezésre álló metrikák az Azure-szolgáltatások listáját lásd:](metrics-supported.md)
* [További információ a speciális funkcióit Metrikaböngésző](metrics-charts.md)
* [Példák: konfigurált diagramok](metric-chart-samples.md)
