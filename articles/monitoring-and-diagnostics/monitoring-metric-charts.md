---
title: Az Azure Monitor metrikaböngészőjének
description: Az Azure Monitor Metrikaböngészőjének új szolgáltatásainak megismerése
author: vgorbenko
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/17/2017
ms.author: vitaly.gorbenko
ms.component: metrics
ms.openlocfilehash: f82b4dff20e2b26e62889c41b3ff3c27bc86066a
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/10/2018
ms.locfileid: "48901413"
---
# <a name="azure-monitor-metrics-explorer"></a>Az Azure Monitor Metrikaböngészőjének

Az Azure Monitor Metrikaböngészőjének a Microsoft Azure-portálon, amely lehetővé teszi, hogy a diagramok küldik az ábrázolást, vizuálisan naplókezelője trendeket és kivizsgálása adatforgalmi csúcsokhoz és a ' értékeihez süllyedések egyik összetevője. Metrikaböngésző egy alapvető kiindulási pontként különböző teljesítmény- és az alkalmazások és az Azure-ban üzemeltetett vagy az Azure Monitor szolgáltatás által figyelt infrastruktúra rendelkezésre állási problémák kivizsgálása. 

## <a name="what-are-metrics-in-azure"></a>Mik azok a metrikák az Azure-ban?

A Microsoft Azure-ban mérőszámok olyan mért értékek sorozata, összegyűjtött és tárolt idővel számát. Nincsenek metrikák standard (vagy "platform"), és egyéni mérőszámok. A standard mérőszámok az Azure platform saját maga által biztosított Önnek. Standard mérőszámok az Azure-erőforrások állapotát és a használati statisztikáit tükrözik. Mivel az egyéni metrikák használatával az alkalmazások által az Azure-bA küldött a [Application Insights API egyéni eseményekhez](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics). Egyéni metrikák és más alkalmazás adott mérőszámok az Application Insights-erőforrások vannak tárolva.

## <a name="how-do-i-create-a-new-chart"></a>Hogyan hozhatok létre egy új diagramot?

1. Nyissa meg az Azure Portalon
2. Lépjen az új **figyelő** lapra, és válassza ki **metrikák**.

   ![Metrikák kép](./media/monitoring-metric-charts/0001.png)

3. A **metrika választó** automatikusan lesz nyissa meg az Ön számára. Válassza ki egy erőforrást a listából, a kapcsolódó metrikákat tekinthet meg. Csak a metrikák az erőforrások listájában jelennek meg.

   ![Metrikák kép](./media/monitoring-metric-charts/0002.png)

   > [!NOTE]
   >Ha egynél több Azure-előfizetéssel, Metrikaböngésző lekéri az erőforrásokat az összes előfizetés, amely ki van jelölve, a portál beállításaiban -> szűrő által az előfizetések listája. Módosításához kattintson a portál beállítások fogaskerék ikonra a képernyő fölött, és válassza ki a használni kívánt melyik előfizetések.

4. Néhány erőforrás esetében (Storage-fiókok és a virtuális gépek) metrika kijelölése előtt ki kell választania egy **Namespace**. Minden névtér csak ezt a névteret, és más névterek nem releváns metrikákat a saját készletét hordozza.

   Például minden Azure Storage, a "BLOB", "Fájlok", "Üzenetsorok" és "Táblák", a storage-fiók minden részét képező subservices metrikáit. A metrika "Üzenetsorbeli üzenetek száma" azonban csak a subservice "Várólista" és a bármely más tároló-subservices természetes módon alkalmazható.

   ![Metrikák kép](./media/monitoring-metric-charts/0003.png)

5. Válasszon ki egy metrikát a listából. Ha ismeri a mérőszám kívánt név, kezdje el beírni a rendelkezésre álló metrikák szűrt listájának megtekintéséhez:

   ![Metrikák kép](./media/monitoring-metric-charts/0004.png)

6. Miután egy metrikát, a diagram az alapértelmezett összesítés a kiválasztott metrika az jelenik meg. Ezen a ponton, egyszerűen kattintson a a **metrikák választó** bezárásához. A diagram egy másik összesítésre is igény szerint válthat. Bizonyos metrikák összesítési váltás lehetővé teszi, hogy kiválaszthatja, melyik értéket meg szeretné tekinteni a diagramra. Például átválthat az átlagos, minimális és maximális értékek között. 

7. A metrika hozzáadása ikonra kattintva ![metrika ikon](./media/monitoring-metric-charts/icon001.png) és ismételje meg a 3-6 lépést adhat hozzá további metrikák ugyanezen a diagramon.

   > [!NOTE]
   > Általában nem érdemes metrikák különböző mértékegységben (pl. "ezredmásodperc" és "kilobájt") vagy jelentősen eltérő méretezési rendelkezik egy diagram. Ehelyett érdemes lehet több diagramot. Kattintson a több diagram létrehozása a Metrikaböngészőben diagram hozzáadása gombra.

## <a name="how-do-i-apply-filters-to-the-charts"></a>Hogyan szűrőket alkalmaz a diagramok?

A diagramok, amelyek megmutatják a dimenziókkal rendelkező metrikák szűrőket is alkalmazhat. Például, ha a "Tranzakciók száma" metrika van egy dimenzió, "Választípus", amely jelzi, hogy a tranzakció válasza sikeres vagy sikertelen volt, majd szűrése e dimenzión lenne jeleníti meg egy diagram sort csak a sikeres (vagy csak sikertelen) tranzakciók. 

### <a name="to-add-a-filter"></a>Szűrő hozzáadása:

1. Kattintson a szűrő hozzáadása ikonra ![Szűrő ikon](./media/monitoring-metric-charts/icon002.png) a diagram felett

2. Válassza ki a szűrni kívánt dimenzió (tulajdonság)

   ![metrika kép](./media/monitoring-metric-charts/0006.png)

3. Válassza ki, melyik dimenzióértékek fel szeretne venni a diagram (Ez a példa bemutatja a sikeres tárolási tranzakciók kiszűrése) azonosításakor:

   ![metrika kép](./media/monitoring-metric-charts/0007.png)

4. A szűrő értékeit kiválasztását követően kattintson a forrásadatok a választó bezárásához. Most a diagram bemutatja, hogy hány tranzakció sikertelen volt:

   ![metrika kép](./media/monitoring-metric-charts/0008.png)

5. Megismételheti a lépéseket több szűrőket alkalmaz az azonos diagramok 1 – 4.

## <a name="how-do-i-segment-a-chart"></a>Hogyan oszthatja meg a diagramot?

Jeleníthetik meg egymással szembeni metrika összehasonlítási különböző szegmensek dimenzió egy metrikát felosztás és a egy dimenzió lakatlan szegmensek azonosítja. 

### <a name="to-segment-a-chart"></a>A diagram szegmens:

1. Kattintson az ikonra a csoportosítás hozzáadása  ![metrika kép](./media/monitoring-metric-charts/icon003.png) a diagram felett.
 
   > [!NOTE]
   > Több szűrő, de csak egy csoportosítási bármilyen egy diagram is rendelkezhet.

2. Válassza ki, amelyen vissza szeretné a diagram szegmentálni dimenzió: 

   ![metrika kép](./media/monitoring-metric-charts/0010.png)

   A diagram most már több sort, az egyes szegmensekben dimenzió egyik jeleníti meg:

   ![metrika kép](./media/monitoring-metric-charts/0012.png)

3. Kattintson a a **csoportosítási választó** bezárásához.

   > [!NOTE]
   > Szűrés és a csoportosítási egyazon dimenzió használatával a szegmensek nem releváns, mivel a forgatókönyvhöz, és könnyebben olvashatóvá teszi a diagramok elrejtése.

## <a name="how-do-i-pin-charts-to-dashboards"></a>Hogyan rögzítheti a diagramokat az irányítópultokon?

Miután a diagramok, érdemes hozzáadása az irányítópultokhoz, így megtekintheti azt újra, esetleg egyéb figyelési telemetriát összefüggésben vagy megosztásra a csapatával. 

Az irányítópult konfigurált diagram rögzítése:

Miután a diagramot, kattintson a a **diagram műveletek** menüben kattintson a jobb felső sarokban, a diagram, és kattintson a **rögzítés az irányítópulton**.

   ![metrika kép](./media/monitoring-metric-charts/0013.png)

## <a name="next-steps"></a>További lépések

  Olvasási [egyéni KPI irányítópultok létrehozása](https://docs.microsoft.com/azure/application-insights/app-insights-tutorial-dashboards) ajánlott eljárást a gyakorlatban hasznosítható irányítópultokat hoz létre a metrikákkal kapcsolatos.