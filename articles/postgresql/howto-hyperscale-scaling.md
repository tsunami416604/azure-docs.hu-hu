---
title: Azure Database for PostgreSQL-nagy kapacitású (Citus) kiszolgálócsoport méretezése
description: A kiszolgáló csoport memóriájának, lemezének és CPU-erőforrásainak módosítása a megnövekedett terhelés kezelésére
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 9/17/2019
ms.openlocfilehash: a69b4988a5ee835381de986edaa5899b09aa9e4e
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2019
ms.locfileid: "71262491"
---
# <a name="scale-a-hyperscale-citus-server-group"></a>Nagy kapacitású-(Citus-) kiszolgálócsoport méretezése

A Azure Database for PostgreSQL-nagy kapacitású (Citus) önkiszolgáló skálázást biztosít a megnövekedett terhelés kezelésére. A Azure Portal megkönnyíti az új munkavégző csomópontok hozzáadását.

Ehhez nyissa meg a configure ( **Konfigurálás** ) fület a nagy kapacitású (Citus) kiszolgálócsoport számára.
Húzza a **munkavégző csomópontok száma** csúszkát az érték megváltoztatásához.

![Erőforrás-csúszkák](./media/howto-hyperscale-scaling/01-sliders-workers.png)

Kattintson a Save (Mentés) gombra a módosított érték életbe léptetéséhez.

> [!NOTE]
> A növekedés és a mentés után a munkavégző csomópontok száma nem csökkenthető a csúszka használatával.
>
> Emellett a virtuális mag és a Storage még nem módosítható a koordinátoron vagy a munkavégzőn ezzel a felhasználói felülettel. Nyisson meg egy támogatási jegyet, ha a koordinátor vagy a munkavégző csomópontok méretezésének számítására van szükség.

Az újonnan hozzáadott csomópontok kihasználása érdekében újra kell osztania az [elosztott tábla](concepts-hyperscale-distributed-data.md#shards)szegmenseit, ami azt jelenti, hogy a meglévő csomópontokból származó szegmensek áthelyezése az újakra történik. A szegmensek újraegyensúlyának elindításához kapcsolódjon a psql-hez, és futtassa a következőt:

```sql
SELECT rebalance_table_shards('distributed_table_name');
```

A `rebalance_table_shards` függvény a (z) argumentumban megnevezett tábla együttes [elhelyezés](concepts-hyperscale-colocation.md) csoportjában lévő összes táblát újraegyenlíti. Így nem kell minden elosztott táblához meghívnia a függvényt, csak egy reprezentatív táblán kell meghívnia az egyes közös helyek csoportjából.

## <a name="next-steps"></a>További lépések

További információ a kiszolgálói csoportok [teljesítményének lehetőségeiről](concepts-hyperscale-configuration-options.md).
