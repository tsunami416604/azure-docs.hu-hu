---
title: Azure Database for PostgreSQL-nagy kapacitású (Citus) kiszolgálócsoport méretezése
description: A kiszolgáló csoport memóriájának, lemezének és CPU-erőforrásainak módosítása a megnövekedett terhelés kezelésére
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 9/17/2019
ms.openlocfilehash: 6053ba37bf330f6b59e291dade822a5ca9de8c85
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73492308"
---
# <a name="scale-a-hyperscale-citus-server-group"></a>Nagy kapacitású-(Citus-) kiszolgálócsoport méretezése

A Azure Database for PostgreSQL-nagy kapacitású (Citus) önkiszolgáló skálázást biztosít a megnövekedett terhelés kezelésére. A Azure Portal megkönnyíti az új munkavégző csomópontok hozzáadását és a meglévő csomópontok kapacitásának növelését.

## <a name="add-worker-nodes"></a>Munkavégző csomópontok hozzáadása

Csomópontok hozzáadásához nyissa meg a **configure (Konfigurálás** ) lapot a nagy kapacitású (Citus) kiszolgálócsoport számára.  Ha a **munkavégző csomópontok száma** fölé húzza a csúszkát, az érték módosul.

![Erőforrás-csúszkák](./media/howto-hyperscale-scaling/01-sliders-workers.png)

Kattintson a **Save (Mentés** ) gombra a módosított érték életbe léptetéséhez.

> [!NOTE]
> A növekedés és a mentés után a munkavégző csomópontok száma nem csökkenthető a csúszka használatával.

### <a name="rebalance-shards"></a>Szegmensek kiegyensúlyozása

Az újonnan hozzáadott csomópontok kihasználása érdekében újra kell osztania az [elosztott tábla](concepts-hyperscale-distributed-data.md#shards)szegmenseit, ami azt jelenti, hogy a meglévő csomópontokból származó szegmensek áthelyezése az újakra történik. Először ellenőrizze, hogy az új feldolgozók sikeresen befejezték-e az üzembe helyezést. Ezután indítsa el a szegmens-újraelosztást a psql-mel és a-t futtató fürt-koordinátor csomóponthoz való csatlakozással:

```sql
SELECT rebalance_table_shards('distributed_table_name');
```

A `rebalance_table_shards` függvény a (z) argumentumban található tábla együttes [elhelyezés](concepts-hyperscale-colocation.md) csoportjában lévő összes táblát újraegyenlíti. Így nem kell minden elosztott táblához meghívnia a függvényt, csak egy reprezentatív táblán kell meghívnia az egyes közös helyek csoportjából.

## <a name="increase-vcores-or-storage-space"></a>Növelje a virtuális mag vagy a tárolóhelyet

Az új csomópontok hozzáadásán kívül növelheti a meglévő csomópontok képességeit is. Nyissa meg a **configure (Konfigurálás** ) fület a nagy kapacitású (Citus), és húzza a **virtuális mag** és **Storage** csúszkát az összes munkavégző csomópontra vonatkozó értékek módosításához. Ügyeljen rá, hogy a módosítások alkalmazásához kattintson a **Mentés** gombra.

## <a name="next-steps"></a>További lépések

További információ a kiszolgálói csoportok [teljesítményének lehetőségeiről](concepts-hyperscale-configuration-options.md).
