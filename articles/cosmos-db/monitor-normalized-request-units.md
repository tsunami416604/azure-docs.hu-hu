---
title: Az Azure Cosmos-tárolóhoz vagy-fiókhoz tartozó normalizált RU/s figyelése
description: Megtudhatja, hogyan figyelheti egy művelet normalizált kérési egység általi használatát Azure Cosmos DBban. Egy Azure Cosmos DB fiók tulajdonosai megtudhatják, hogy mely műveletek használják a több kérési egységet.
ms.service: cosmos-db
ms.topic: how-to
author: kanshiG
ms.author: govindk
ms.date: 06/25/2020
ms.openlocfilehash: 8709389208ba1320685b1834b20893f08ef33ed7
ms.sourcegitcommit: 1d9f7368fa3dadedcc133e175e5a4ede003a8413
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2020
ms.locfileid: "85482904"
---
# <a name="how-to-monitor-normalized-rus-for-an-azure-cosmos-container-or-an-account"></a>A normalizált RU/s figyelése Azure Cosmos-tárolóhoz vagy-fiókhoz

A Azure Cosmos DB Azure Monitor metrikai nézetet biztosít a fiók figyeléséhez és az irányítópultok létrehozásához. A rendszer alapértelmezés szerint összegyűjti a Azure Cosmos DB metrikákat, így nem kell explicit módon engedélyeznie vagy konfigurálnia a szolgáltatást.

A **normalizált ru** -használati metrika azt mutatja, hogy mennyire telítettek a replikák a kérelmek egységei számára a partíciós kulcs tartományán keresztül. Azure Cosmos DB az átviteli sebességet egyenlően osztja el az összes fizikai partíció között. Ez a metrika egy másodpéldányon belüli maximális átviteli sebességre mutató másodpercenkénti nézetet biztosít. Ezzel a metrikával számíthatja ki az RU/s használatát az adott tároló partíciói között. Ha ezt a metrikát használja, ha a kérési egységek kihasználtságának nagy hányadát látja, növelje az átviteli sebességet, hogy megfeleljen a számítási feladatok igényeinek.

## <a name="what-to-expect-and-do-when-normalized-rus-is-higher"></a>Mire számíthat, és ha a normalizált RU/s magasabb

Ha a normalizált RU/s-használat eléri a 100%-ot, az ügyfél megkapja a sebességet korlátozó hibákat. Az ügyfélnek meg kell tartania a várakozási időt, és újra kell próbálkoznia. Ha van egy rövid szeg, amely eléri a 100%-os kihasználtságot, az azt jelenti, hogy a replika átviteli sebessége elérte a maximális teljesítmény korlátját. Például egyetlen művelet, például egy tárolt eljárás, amely egy replika összes RU/s-példányát használja, egy rövid tüske lesz a normalizált RU/s-fogyasztásban. Ilyen esetekben a rendszer nem korlátozza az azonnali díjszabást, ha a kérések aránya alacsony. Ennek az az oka, hogy Azure Cosmos DB lehetővé teszi, hogy a kérések több, mint a kiépített RU/s díjat számolnak fel az adott kérelemre vonatkozóan, és az adott időszakon belül más kérelmek esetében a díjszabás korlátozott.

A Azure Monitor metrikák segítségével megkeresheti a műveletekhez tartozó állapotkódot a **kérelmek teljes** metrikájának használatával. Később a 429 állapotkód alapján szűrheti ezeket a kéréseket, és a **művelet típusa**alapján feloszthatja őket.

A korlátozott díjszabású kérelmek megkereséséhez az ajánlott módszer az információk diagnosztikai naplókon keresztüli beszerzése.

Ha folyamatos csúcsa 100%-ban normalizált RU/s-fogyasztás, vagy a 100%-hoz közeledik, ajánlott az átviteli sebesség növelése. Az Azure monitor metrikáinak és az Azure monitor naplóinak használatával megtudhatja, hogy mely műveletek jelentősek és a maximális kihasználtságuk.

A **normalizált ru** -használati metrika azt is felhasználja, hogy a partíciós kulcs tartománya milyen mértékben meleg a használat szempontjából. így biztosíthatja a partíciós kulcs tartományára irányuló adatátvitelt. Később nyomon követheti a **PartitionKeyRUConsumption** -naplót, Azure monitor naplókat, amelyekkel információkat kaphat arról, hogy mely logikai partíciós kulcsok használják a használati feltételeket.

## <a name="view-the-normalized-request-unit-consumption-metric"></a>A normalizált kérési egység felhasználási metrikájának megtekintése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

2. Válassza a **figyelő** lehetőséget a bal oldali navigációs sávon, és válassza a **metrikák**lehetőséget.

   :::image type="content" source="./media/monitor-normalized-request-units/monitor-metrics-blade.png" alt-text="Metrikák ablaktábla Azure Monitor":::

3. A **metrikák** ablaktáblán > **válasszon ki egy erőforrást** > válassza ki a szükséges **előfizetést**és **erőforráscsoportot**. Az **erőforrástípus**mezőben válassza a **Azure Cosmos db fiókok**lehetőséget, válasszon ki egy meglévő Azure Cosmos-fiókot, majd válassza az **alkalmaz**lehetőséget.

   :::image type="content" source="./media/monitor-normalized-request-units/select-cosmos-db-account.png" alt-text="Válasszon ki egy Azure Cosmos-fiókot a metrikák megtekintéséhez":::

4. Következő lépésként kiválaszthat egy mérőszámot az elérhető metrikák listájából. Kiválaszthatja a kérelmekhez tartozó mérőszámokat, a tárolást, a késést, a rendelkezésre állást, a Cassandra és másokat. A listán szereplő összes mérőszám részletes megismeréséhez tekintse meg a [metrikák kategória szerint](monitor-cosmos-db-reference.md) című cikket. Ebben a példában a **normalizált ru** használati metrikát és a **Max** értéket adjuk meg az összesítési értékként.

   Ezen részletek mellett kiválaszthatja a metrikák **időtartományát** és **időrészletességét** is. A maximális értéknél megtekintheti az elmúlt 30 nap mérőszámait.  A szűrő alkalmazása után egy diagram jelenik meg a szűrő alapján.

   :::image type="content" source="./media/monitor-normalized-request-units/normalized-request-unit-usage-metric.png" alt-text="Metrika kiválasztása a Azure Portal":::

### <a name="filters-for-normalized-request-unit-consumption"></a>A normalizált kérelmek egységének felhasználására szolgáló szűrők

A mérőszámokat és a diagramot egy adott **CollectionName**, a **databasename**, a **PartitionKeyRangeID**és a **régió**alapján is szűrheti. A metrikák szűréséhez válassza a **szűrő hozzáadása** lehetőséget, és válassza ki a szükséges tulajdonságot, például a **CollectionName** és a megfelelő értéket. A gráf ezután megjeleníti a megadott időszakra vonatkozóan a tárolóhoz felhasznált normalizált RU-felhasználási egységeket.  

A metrikákat a **felosztás alkalmazása** lehetőség használatával csoportosíthatja.  

Az egyes tárolók normalizált kérések egységenkénti mérőszáma az alábbi képen látható módon jelenik meg:

:::image type="content" source="./media/monitor-normalized-request-units/normalized-request-unit-usage-filters.png" alt-text="Szűrők alkalmazása a normalizált kérelmek egységének felhasználási metrikája":::

## <a name="next-steps"></a>További lépések

* Az Azure-ban [diagnosztikai beállításokkal](cosmosdb-monitor-resource-logs.md) figyelheti Azure Cosmos db az adataikat.
* [Azure Cosmos DB vezérlési sík műveleteinek naplózása](audit-control-plane-logs.md)
