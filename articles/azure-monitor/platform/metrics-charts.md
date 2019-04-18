---
title: Fejlett funkcióira Metrikaböngészőt Azure
description: Az Azure Monitor Metrikaböngészőjének speciális funkcióinak megismerése
author: vgorbenko
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 01/22/2019
ms.author: vitalyg
ms.subservice: metrics
ms.openlocfilehash: 67e4281b24a7489cf202d82bdddbe99992aac095
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59271679"
---
# <a name="advanced-features-of-azure-metrics-explorer"></a>Fejlett funkcióira Metrikaböngészőt Azure

> [!NOTE]
> Ez a cikk feltételezi, hogy Ön ismeri a Metrikaböngésző alapszintű szolgáltatásaihoz. Ha szeretné megtudni, hogyan hozzon létre az első metrikadiagram, és új felhasználó [Ismerkedés az Azure Metrikaböngésző](metrics-getting-started.md).

## <a name="metrics-in-azure"></a>Metrikák az Azure-ban

[Metrikák az Azure monitorban](data-platform-metrics.md) mért értékek sorozata, összegyűjtött és tárolt idővel számát is. Nincsenek metrikák standard (vagy "platform"), és egyéni mérőszámok. A standard mérőszámok az Azure platform saját maga által biztosított Önnek. Standard mérőszámok az Azure-erőforrások állapotát és a használati statisztikáit tükrözik. Mivel az egyéni metrikák használatával az alkalmazások által az Azure-bA küldött a [Application Insights API egyéni eseményekhez és a metrikák](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics), [Windows Azure Diagnostics (WAD) bővítmény](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostics-extension-overview), vagy [Azure Figyelheti a REST API-val](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-store-custom-rest-api).

## <a name="create-views-with-multiple-metrics-and-charts"></a>Nézetek létrehozása több mértékek és diagramok

Diagramok, amelyek több metrikák vonal megrajzolásához vagy több mérőszám-diagramok megjelenítése egyszerre hozhat létre. Ez a funkció lehetővé teszi:

- az ugyanazon a grafikonon egy érték összevetését kapcsolódó metrikák kapcsolódik egy másik
- metrikák megjelenítése a közvetlen egymás közelében lévő különböző mértékegységek
- vizuálisan összesíteni, és hasonlítsa össze a különböző erőforrásokból származó metrikák

Például ha 5 tárfiókok rendelkezik, és meg szeretné ismerni a teljes lemezterület felhasznált közöttük, létrehozhat egy (halmozott) területdiagramot, amely az egyéni és az összes érték összegét mutatja adott időpontokban időben.

### <a name="multiple-metrics-on-the-same-chart"></a>Több metrika ugyanezen a diagramon

Először [hozzon létre egy új diagramot](metrics-getting-started.md#create-your-first-metric-chart). Kattintson a **metrika hozzáadása** , és ismételje meg a lépéseket egy másik metrika hozzáadása ugyanezen a diagramon.

   > [!NOTE]
   > Általában nem érdemes metrikák különböző mértékegységben (pl. "ezredmásodperc" és "kilobájt") vagy jelentősen eltérő méretezési rendelkezik egy diagram. Ehelyett érdemes lehet több diagramot. Kattintson a több diagram létrehozása a metrikaböngészőben diagram hozzáadása gombra.

### <a name="multiple-charts"></a>Több diagram

Kattintson a **Hozzáadás diagram** , és hozzon létre egy másik diagramot egy másik metrikát választani.

### <a name="order-or-delete-multiple-charts"></a>Megrendelés vagy több diagram törlése

Rendelés, vagy törli a több diagramot, kattintson a három pontra ( **...**  ) a diagram menü megnyitásához, és válassza ki a megfelelő menüelem a szimbólum **feljebb**, **lejjebb**, vagy **törlése**.

## <a name="apply-filters-to-charts"></a>Szűrőket alkalmazhat a diagramok

A diagramok, amelyek megmutatják a dimenziókkal rendelkező metrikák szűrőket is alkalmazhat. Például, ha a "Tranzakciók száma" metrika van egy dimenzió, "Választípus", amely jelzi, hogy a tranzakció válasza sikeres vagy sikertelen volt, majd szűrése e dimenzión lenne jeleníti meg egy diagram sort csak a sikeres (vagy csak sikertelen) tranzakciók. 

### <a name="to-add-a-filter"></a>Szűrő hozzáadása

1. Válassza ki **szűrő hozzáadása** a diagram felett

2. Válassza ki a szűrni kívánt dimenzió (tulajdonság)

   ![metrika kép](./media/metrics-charts/00006.png)

3. Válassza ki, melyik dimenzióértékek fel szeretne venni a diagram (Ez a példa bemutatja a sikeres tárolási tranzakciók kiszűrése) azonosításakor:

   ![metrika kép](./media/metrics-charts/00007.png)

4. A szűrő értékeit kiválasztását követően kattintson a forrásadatok a választó bezárásához. Most a diagram bemutatja, hogy hány tranzakció sikertelen volt:

   ![metrika kép](./media/metrics-charts/00008.png)

5. Megismételheti a lépéseket több szűrőket alkalmaz az azonos diagramok 1 – 4.



## <a name="apply-splitting-to-a-chart"></a>Alkalmazza a megosztáshoz, hogy a diagram

Jeleníthetik meg egymással szembeni metrika összehasonlítási különböző szegmensek dimenzió egy metrikát felosztás és a egy dimenzió lakatlan szegmensek azonosítja.

### <a name="apply-splitting"></a>Felosztás alkalmazása

1. Kattintson a **alkalmazni a felosztás** a diagram felett.
 
   > [!NOTE]
   > Felosztás a diagramok, amelyek több metrika nem használható. De bármilyen egy diagramon a alkalmazni csak egyetlen felosztási dimenzió több szűrőt is rendelkezhet.

2. Válassza ki, amelyen vissza szeretné a diagram szegmentálni dimenzió:

   ![metrika kép](./media/metrics-charts/00010.png)

   A diagram most már több sort, az egyes szegmensekben dimenzió egyik jeleníti meg:

   ![metrika kép](./media/metrics-charts/00012.png)

3. Kattintson a a **csoportosítási választó** bezárásához.

   > [!NOTE]
   > Szűrés és a megosztás egyazon dimenzió használatával a szegmensek nem releváns, mivel a forgatókönyvhöz, és könnyebben olvashatóvá teszi a diagramok elrejtése.

## <a name="lock-boundaries-of-chart-y-axis"></a>Zárolási határok diagram y tengely

Az y tengely skáláját zárolás akkor válik fontossá, amikor a diagram bemutatja a nagyobb érték kisebb ingadozások által megkövetelt. 

Például ha sikeres kérések mennyisége lenyílik 99,99 % 99,5 %-ra, azt tüntetheti jelentős csökkenését a szolgáltatásminőség. Azonban egy kis numerikus érték ingadozása okainak lenne nehezen vagy egyáltalán nem lehetséges a diagram alapértelmezett beállításokkal. Ebben az esetben lehetett zárolni 99 %-os, amely biztosítja, kis legördülő több kétségtelenül fennáll-e a diagram legalacsonyabb határait. 

Egy másik példa egy ingadozás a rendelkezésre álló memória, amely az érték technikailag soha nem eléri a 0. Értéke a tartomány rögzítése előfordulhat, hogy könnyebben a csepp a rendelkezésre álló memória helyszíni. 

Az y tengely tartománya szabályozni, használja a "..." diagram menüt, és válassza a **diagram szerkesztése** speciális diagram beállítások eléréséhez. Az y tengely tartománya szakaszban szereplő értékeket módosíthatja, vagy használjon **automatikus** Alapértelmezések visszaállítása gombra.

![metrika kép](./media/metrics-charts/00014-manually-set-granularity.png)

> [!WARNING]
> Y tengely, hogy a diagramok, amelyek nyomon követik a különböző határain zárolásának számolja, vagy idő (és így használatban lévők száma, sum, minimum vagy maximum összesítések) egy adott időszakban összegzi általában igényel, adjon meg egy rögzített idő részletesség, nem pedig az automatikus alapértelmezett hagyatkoznia. Erre azért szükség, mert a diagramok az értékeket módosíthatja, ha az idő részletessége automatikusan módosította a böngészőablak átméretezése, vagy egy képernyőfelbontás fog egy másik felhasználó. A létrejövő idő részletesség hatásokat megjelenésének módosítása a diagram y tengely tartomány kijelölt érvénytelenítése.

## <a name="pin-charts-to-dashboards"></a>PIN-kód diagramok, irányítópultok

Miután a diagramok, érdemes hozzáadása az irányítópultokhoz, így megtekintheti azt újra, esetleg egyéb figyelési telemetriát összefüggésben vagy megosztásra a csapatával.

Az irányítópult konfigurált diagram rögzítése:

Miután a diagramot, kattintson a a **diagram műveletek** menüben kattintson a jobb felső sarokban, a diagram, és kattintson a **rögzítés az irányítópulton**.

![metrika kép](./media/metrics-charts/00013.png)

## <a name="create-alert-rules"></a>Riasztási szabályok létrehozása

A feltételeknek, a metrikák megjelenítéséhez metrika alapján alapuló riasztási szabály meg is használhatja. Az Új riasztási szabály a célként megadott erőforrás, metrika, felosztását és szűrő dimenziók a diagram tartalmazza. Fogja tudni módosítani ezeket a beállításokat később a riasztási szabály létrehozása panelen.

### <a name="to-create-a-new-alert-rule-click-new-alert-rule"></a>Egy új riasztási szabály létrehozásához kattintson a **Új riasztási szabály**

![Vörös színnel Új riasztási szabály gomb](./media/metrics-charts/015.png)

Megnyílik a riasztási szabály létrehozása panelre az alapul szolgáló metrika dimenziókkal létrehozni az egyéni riasztási szabályok könnyebb előre fel van töltve a diagram.

![Riasztási szabály létrehozása](./media/metrics-charts/016.png)

Tekintse meg ezt [cikk](alerts-metric.md) további metrikákhoz kapcsolódó riasztások beállításával kapcsolatban.

## <a name="troubleshooting"></a>Hibaelhárítás

*A diagram nem látható semmilyen adatot.*

* Szűrők a diagramok a panelen a alkalmazni. Győződjön meg arról, hogy Ön összpontosítunk, egy diagram, amíg nem állította be egy szűrőt, amely nem tartalmazza a másik az adatokat.

* Ha azt szeretné, a különböző szűrőivel beállíthatja a különböző diagramok különböző paneljein hozzon létre őket, mentse azokat, külön Kedvencek. Ha azt szeretné, is rögzítheti őket az irányítópulton, hogy tekintheti meg őket egymással párhuzamosan.

* Ha az egyik tulajdonsága, amely nincs meghatározva a metrikát a diagram szegmentálhatja, majd lesz semmi a diagramra. Törölje a Szegmentálás (megosztás), vagy válasszon egy másik tulajdonságot.

## <a name="next-steps"></a>További lépések

  Olvasási [egyéni KPI irányítópultok létrehozása](https://docs.microsoft.com/azure/application-insights/app-insights-tutorial-dashboards) ajánlott eljárást a gyakorlatban hasznosítható irányítópultokat hoz létre a metrikákkal kapcsolatos.

