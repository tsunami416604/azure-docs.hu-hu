---
title: Kiszolgálócsoport méretezése - Nagy kapacitású (Citus) – Azure-adatbázis a PostgreSQL-hez
description: A kiszolgálócsoport memóriájának, lemezének és processzorerőforrásainak beállítása a megnövekedett terhelés kezeléséhez
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 3/16/2020
ms.openlocfilehash: fa48ca287c248155a0271b5134be782d8db1c785
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80063104"
---
# <a name="scale-a-hyperscale-citus-server-group"></a>Nagyméretű (Citus) kiszolgálócsoport méretezése

Azure Database for PostgreSQL – Hyperscale (Citus) önkiszolgáló skálázást biztosít a megnövekedett terhelés kezeléséhez. Az Azure Portalon egyszerűen adhat hozzá új feldolgozócsomópontokat, és növelheti a meglévő csomópontok virtuális magjait.

## <a name="add-worker-nodes"></a>Munkavégző csomópontok hozzáadása

Csomópontok hozzáadásához nyissa meg a Nagykapacitású (Citus) kiszolgálócsoport **Konfigurálás** lapját.  A **munkavégző csomópontok számának** csúszkájának húzása megváltoztatja az értéket.

![Erőforrás csúszkák](./media/howto-hyperscale-scaling/01-sliders-workers.png)

A **Mentett** gombra kattintva a módosított érték érvénybe léptetéséhez kattintson.

> [!NOTE]
> A növelést és mentést követően a munkavégző csomópontok száma nem csökkenthető a csúszka használatával.

### <a name="rebalance-shards"></a>Szilánkok egyensúlyának helyreállítása

Az újonnan hozzáadott csomópontok előnyeinek kihasználásához újra egyensúlyoznia kell az elosztott [táblaszilánkokat,](concepts-hyperscale-distributed-data.md#shards)ami azt jelenti, hogy néhány szegmenst áthelyez a meglévő csomópontokról az újakra. Először ellenőrizze, hogy az új dolgozók sikeresen befejezték-e a kiépítést. Ezután indítsa el a shard rebalancer, a fürt koordinátorcsomópont psql és futás:

```sql
SELECT rebalance_table_shards('distributed_table_name');
```

A `rebalance_table_shards` függvény újraegyensúlyozza az argumentumában megnevezett tábla [helymegosztási](concepts-hyperscale-colocation.md) csoportjának összes tábláját. Így nem kell minden elosztott tábla függvényét meghívnia, csak hívja meg az egyes helymegosztási csoportok reprezentatív tábláján.

## <a name="increase-or-decrease-vcores-on-nodes"></a>Virtuális magok növelése vagy csökkentése csomópontokon

> [!NOTE]
> Ez a szolgáltatás jelenleg előzetes kiadásban elérhető. Ha a kiszolgálócsoport csomópontjainak virtuális magjaiban szeretne változtatni, forduljon az [Azure ügyfélszolgálatához.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)

Az új csomópontok hozzáadása mellett növelheti a meglévő csomópontok képességeit. A számítási kapacitás fel- és leigazítása hasznos lehet a teljesítménykísérletekhez, valamint a forgalmi igények rövid vagy hosszú távú változásaihoz.

Az összes munkavégző csomópont virtuális magjainak módosításához módosítsa a **virtuális magok** csúszkáját a **Konfiguráció (munkavégző csomópontonként)** csoportban. A koordinátori csomópont virtuális magjai egymástól függetlenül módosíthatók. Kattintson a **Konfiguráció módosítása** hivatkozásra a **koordinátor csomópont**alatt . Megjelenik egy párbeszédpanel a koordinátor virtuális magok és tárolókapacitásának csúszkáival. Módosítsa a csúszkákat a kívánt módon, és válassza **az OK gombot.**

## <a name="next-steps"></a>További lépések

További információ a kiszolgálócsoport [teljesítménybeállításairól.](concepts-hyperscale-configuration-options.md)
