---
title: A kiszolgáló csoport skálázása – nagy kapacitású (Citus) – Azure Database for PostgreSQL
description: A kiszolgáló csoport memóriájának, lemezének és CPU-erőforrásainak módosítása a megnövekedett terhelés kezelésére
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 12/17/2019
ms.openlocfilehash: bec2a40d8cf5fb178418ec6bb59a52a0bfe3eb8c
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75453055"
---
# <a name="scale-a-hyperscale-citus-server-group"></a>Nagy kapacitású-(Citus-) kiszolgálócsoport méretezése

A Azure Database for PostgreSQL-nagy kapacitású (Citus) önkiszolgáló skálázást biztosít a megnövekedett terhelés kezelésére. A Azure Portal megkönnyíti az új munkavégző csomópontok hozzáadását és a meglévő csomópontok virtuális mag növelését.

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

## <a name="increase-vcores"></a>Virtuális mag javítása

Az új csomópontok hozzáadásán kívül növelheti a meglévő csomópontok képességeit is. Ez a funkció jelenleg előzetes verzióban érhető el – Ha nagyobb virtuális mag szeretne kérni a kiszolgálócsoport csomópontjaihoz, vegye [fel a kapcsolatot az Azure ügyfélszolgálatával](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="next-steps"></a>Következő lépések

További információ a kiszolgálói csoportok [teljesítményének lehetőségeiről](concepts-hyperscale-configuration-options.md).
