---
title: A kiszolgáló csoport skálázása – nagy kapacitású (Citus) – Azure Database for PostgreSQL
description: A kiszolgáló csoport memóriájának, lemezének és CPU-erőforrásainak módosítása a megnövekedett terhelés kezelésére
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 3/16/2020
ms.openlocfilehash: 1173defa8bbe66cbeaaf6bd5264b0730160a197b
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/08/2020
ms.locfileid: "86116828"
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

A függvény a (z `rebalance_table_shards` ) argumentumban megnevezett tábla együttes [elhelyezés](concepts-hyperscale-colocation.md) csoportjában lévő összes táblát újraegyenlíti. Így nem kell minden elosztott táblához meghívnia a függvényt, csak egy reprezentatív táblán kell meghívnia az egyes közös helyek csoportjából.

## <a name="increase-or-decrease-vcores-on-nodes"></a>Virtuális mag növelése vagy csökkentése a csomópontokon

> [!NOTE]
> Ez a szolgáltatás jelenleg előzetes kiadásban elérhető. Ha módosítani szeretné a virtuális mag lévő csomópontok módosítását, vegye fel a [kapcsolatot az Azure ügyfélszolgálatával](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

Az új csomópontok hozzáadásán kívül növelheti a meglévő csomópontok képességeit is. A számítási kapacitás felfelé és lefelé való beállítása hasznos lehet a teljesítmény-kísérleteknél, valamint a forgalmi igények rövid vagy hosszú távú változásaihoz.

Ha módosítani szeretné az összes munkavégző csomópont virtuális mag, állítsa be a **virtuális mag** csúszkát a **konfiguráció alatt (munkavégző csomópont)**. A koordinátor csomópontjának virtuális mag egymástól függetlenül is módosítható. Kattintson a **konfiguráció módosítása** hivatkozásra a **koordinátor csomópont**alatt. Ekkor megjelenik egy párbeszédpanel, amely a koordinátor virtuális mag és tárolókapacitására vonatkozó csúszkákkal jelenik meg. Módosítsa a csúszkákat a kívánt értékre, majd kattintson **az OK gombra**.

## <a name="next-steps"></a>Következő lépések

További információ a kiszolgálói csoportok [teljesítményének lehetőségeiről](concepts-hyperscale-configuration-options.md).
