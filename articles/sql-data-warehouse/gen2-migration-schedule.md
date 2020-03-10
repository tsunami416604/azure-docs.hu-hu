---
title: SQL-készlet migrálása a Gen2-be
description: A meglévő SQL-készletek Gen2 és az áttelepítési ütemterv régiónként való áttelepítésére vonatkozó utasítások.
services: sql-data-warehouse
author: mlee3gsd
ms.author: anjangsh
ms.reviewer: jrasnick
manager: craigg
ms.assetid: 04b05dea-c066-44a0-9751-0774eb84c689
ms.service: sql-data-warehouse
ms.topic: article
ms.date: 01/21/2020
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 00180c1791e765240f3f8feac188b9250162408e
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78380793"
---
# <a name="upgrade-your-sql-pool-to-gen2"></a>SQL-készlet frissítése Gen2

A Microsoft segít csökkenteni az SQL-készletek beléptetési szintű költségeit.  Az SQL-készlethez már elérhetők az alacsonyabb számítási szintek, amelyek képesek az igényes lekérdezések kezelésére. Olvassa el a teljes bejelentést a [Gen2-hez készült, alacsonyabb számítási szintű támogatással](https://azure.microsoft.com/blog/azure-sql-data-warehouse-gen2-now-supports-lower-compute-tiers/). Az új ajánlat az alábbi táblázatban említett régiókban érhető el. A támogatott régiók esetében a meglévő Gen1 SQL-készletek a következő módon frissíthetők Gen2-re:

- **Az automatikus frissítési folyamat:** Az automatikus frissítések nem indulnak el, amint a szolgáltatás elérhető egy régióban.  Ha az automatikus frissítések egy adott régióban kezdődnek, az egyes adatraktár-frissítések a kiválasztott karbantartási ütemezés szerint zajlanak.
- [**Saját frissítés a Gen2-re:** ](#self-upgrade-to-gen2) A frissítéshez beállíthatja, hogy mikor kell frissíteni a Gen2. Ha a régiója még nem támogatott, a visszaállítási pontról közvetlenül egy támogatott régió Gen2-példányára állíthatja vissza.

## <a name="automated-schedule-and-region-availability-table"></a>Automatizált ütemezések és régiók rendelkezésre állási táblázata

A következő táblázat összefoglalja a régiót, ha az alsó Gen2 számítási szintje elérhető lesz, és az automatikus frissítés elindul. A dátumok változhatnak. Térjen vissza, és tekintse meg, hogy mikor válik elérhetővé a régió.

\* azt jelzi, hogy a régióhoz megadott ütemterv jelenleg nem érhető el.

| **Régió** | **Alacsonyabb Gen2 érhető el** | **Automatikus frissítések kezdete** |
|:--- |:--- |:--- |
| Kelet-Kanada |2020. június 1. |2020. július 1. |
| Kelet-Kína |\* |\* |
| Észak-Kína |\* |\* |
| Közép-Németország |\* |\* |
| Középnyugat-Németország |Elérhető |2020. május 1. |
| Nyugat-India |Elérhető |2020. május 1.  |

## <a name="automatic-upgrade-process"></a>Automatikus frissítési folyamat

A fenti rendelkezésre állási diagram alapján a Gen1-példányok automatikus frissítéseit ütemezjük. Az SQL-készlet rendelkezésre állásával kapcsolatos váratlan megszakítások elkerülése érdekében az automatikus frissítések ütemezése a karbantartási ütemezés szerint történik. Az új Gen1-példány létrehozásának lehetősége le lesz tiltva a Gen2-re történő automatikus frissítéssel rendelkező régiókban. Az automatikus frissítések befejezését követően a Gen1 elavulttá válik. További információ az ütemtervekről: [karbantartási ütemterv megtekintése](viewing-maintenance-schedule.md)

A frissítési folyamat egy rövid, a kapcsolat (körülbelül 5 perc) bekapcsolását is magában foglalja az SQL-készlet újraindításakor.  Az SQL-készlet újraindítása után a szolgáltatás teljes mértékben elérhető lesz. A teljesítmény romlása azonban akkor is előfordulhat, ha a frissítési folyamat továbbra is frissíti az adatfájlokat a háttérben. A teljesítménycsökkenés teljes időtartama az adatfájlok méretétől függően változik.

Az adatfájlok frissítési folyamatát is felgyorsíthatja az [Alter index Újraépítés](sql-data-warehouse-tables-index.md) az összes elsődleges oszlopcentrikus-táblán való futtatásával, az újraindítás után egy nagyobb slo-t és egy erőforrás-osztályt használva.

> [!NOTE]
> Az Alter index Rebuild egy offline művelet, és a táblák addig nem lesznek elérhetők, amíg az Újraépítés be nem fejeződik.

## <a name="self-upgrade-to-gen2"></a>Saját frissítés a Gen2-re

Az alábbi lépéseket egy meglévő Gen1 SQL-készleten hajthatja végre. Ha úgy dönt, hogy saját frissítést végez, az automatikus frissítési folyamat megkezdése előtt el kell végeznie azt a régióban. Így biztosítható, hogy az automatikus frissítések kockázata ne okozzon ütközést.

Önfrissítéskor két lehetőség közül választhat.  Az aktuális SQL-készletet helyben is frissítheti, vagy visszaállíthatja egy Gen1 SQL-készletet egy Gen2-példányba.

- [Helyben történő frissítés](upgrade-to-latest-generation.md) – ez a beállítás a meglévő Gen1 SQL-készletet a Gen2-re frissíti. A frissítési folyamat egy rövid, a kapcsolat (körülbelül 5 perc) bekapcsolását is magában foglalja az SQL-készlet újraindításakor.  Az SQL-készlet újraindítása után a szolgáltatás teljes mértékben elérhető lesz. Ha a frissítés során problémák merülnek fel, nyisson meg egy [támogatási kérést](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket) , és hivatkozzon a "Gen2 upgrade" kifejezésre a lehetséges okok miatt.
- [Frissítés visszaállítási pontról](sql-data-warehouse-restore.md) – hozzon létre egy felhasználó által definiált visszaállítási pontot az aktuális Gen1 SQL-készleten, majd állítsa vissza közvetlenül egy Gen2-példányra. A meglévő Gen1 SQL-készlet továbbra is érvényben marad. A visszaállítás befejezése után a Gen2 SQL-készlete teljes mértékben elérhető lesz a használatra.  Miután futtatta az összes tesztelési és érvényesítési folyamatot a visszaállított Gen2-példányon, törölheti az eredeti Gen1-példányt.

   - 1\. lépés: a Azure Portalból [hozzon létre egy felhasználó által definiált visszaállítási pontot](sql-data-warehouse-restore-active-paused-dw.md).
   - 2\. lépés: a felhasználó által definiált visszaállítási pontról történő visszaállításkor állítsa a "teljesítményszint" értéket az előnyben részesített Gen2-szintre.

Visszaesést tapasztalhat a teljesítményben, miközben a frissítési folyamat az adatfájlok frissítését végzi a háttérben. A teljesítménycsökkenés teljes időtartama az adatfájlok méretétől függően változik.

A háttérben futó adatáttelepítési folyamat meggyorsítása érdekében azonnal kényszerítheti az adatáthelyezést az [Alter index-Újraépítés](sql-data-warehouse-tables-index.md) futtatásával minden olyan elsődleges oszlopcentrikus-táblán, amelyet egy nagyobb slo és Resource osztályban szeretne lekérdezni.

> [!NOTE]
> Az Alter index Rebuild egy offline művelet, és a táblák addig nem lesznek elérhetők, amíg az Újraépítés be nem fejeződik.

Ha bármilyen probléma merül fel az SQL-készlettel kapcsolatban, hozzon létre egy [támogatási kérést](sql-data-warehouse-get-started-create-support-ticket.md) , és hivatkozzon a "Gen2 upgrade" kifejezésre a lehetséges okok miatt.

További információ: [verziófrissítés a Gen2](upgrade-to-latest-generation.md).

## <a name="migration-frequently-asked-questions"></a>Áttelepítés – gyakori kérdések

**K: a Gen2 ára ugyanaz, mint a Gen1?**

- V: Igen.

**K: Hogyan érintik a frissítések az Automation-parancsfájlokat?**

- A: a szolgáltatási szintre vonatkozó célkitűzésekre hivatkozó Automation-parancsfájlokat úgy kell módosítani, hogy azok megfeleljenek a Gen2-nek.  Tekintse meg [a részleteket.](upgrade-to-latest-generation.md#sign-in-to-the-azure-portal)

**K: mennyi ideig tart az önálló frissítés normál esetben?**

- Válasz: frissítheti a helyet, vagy frissíthet egy visszaállítási pontról.  
   - A helyben történő frissítés során az SQL-készletet egy pillanatra szüneteltetheti és folytathatja.  A háttérben futó folyamat folytatódni fog, amíg az SQL-készlet online állapotban van.  
   - Ha egy visszaállítási ponton keresztül frissíti a szolgáltatást, a frissítés a teljes visszaállítási folyamaton keresztül történik.

**K: mennyi ideig tart az automatikus frissítés?**

- A: a frissítés tényleges állásidője csak a szolgáltatás szüneteltetéséhez és folytatásához szükséges idő, amely 5 – 10 percet vesz igénybe. A rövid állásidőt követően egy háttérfolyamat tárolómigrálást fog futtatni. A háttérben futó folyamat időtartama az SQL-készlet méretétől függ.

**K: Mikor kerül sor az automatikus frissítésre?**

- A: a karbantartási ütemterv során. Ha kihasználja a kiválasztott karbantartási ütemtervet, a rendszer a vállalkozása számára csökkentheti a fennakadást.

**K: mit tegyek, ha úgy tűnik, hogy a háttérben futó frissítési folyamat beragadt?**

 - A: indítsa el a Oszlopcentrikus-táblák újraindexelését. Vegye figyelembe, hogy a tábla újraindexelése a művelet során offline állapotba kerül.

**K: mi a teendő, ha a Gen2 nem rendelkezik a szolgáltatási szint célkitűzéssel a Gen1?**
- A: Ha kor DW600 vagy DW1200 futtat a Gen1-on, javasolt a DW500c vagy a DW1000c használata, mivel a Gen2 több memóriát, erőforrást és nagyobb teljesítményt nyújt, mint a Gen1.

**K: Letilthatom a Geo-biztonsági mentést?**
- V: nem. A Geo-Backup egy vállalati szolgáltatás, amellyel megőrizheti az SQL-készlet rendelkezésre állását abban az esetben, ha egy régió elérhetetlenné válik. Ha további kérdései vannak, nyisson meg egy [támogatási kérést](sql-data-warehouse-get-started-create-support-ticket.md) .

**K: van különbség a Gen1 és a Gen2 között a T-SQL szintaxisban?**

- Válasz: a T-SQL nyelvi szintaxisa nem változik a Gen1 és a Gen2 között.

**K: a Gen2 támogatja a karbantartási időszakokat?**

- V: Igen.

**K: Létrehozhatok új Gen1-példányt a régióm frissítése után?**

- V: nem. A régió frissítése után az új Gen1-példányok létrehozása le lesz tiltva.

## <a name="next-steps"></a>Következő lépések

- [Frissítési lépések](upgrade-to-latest-generation.md)
- [Karbantartási időszakok](maintenance-scheduling.md)
- [Erőforrás-állapot figyelője](https://docs.microsoft.com/azure/service-health/resource-health-overview)
- [Áttekintés az áttelepítés megkezdése előtt](upgrade-to-latest-generation.md#before-you-begin)
- [Helyben történő frissítés frissítése visszaállítási pontról](upgrade-to-latest-generation.md)
- [Felhasználó által definiált visszaállítási pont létrehozása](sql-data-warehouse-restore-points.md)
- [Útmutató a Gen2 való visszaállításhoz](sql-data-warehouse-restore-active-paused-dw.md)
- [SQL Data Warehouse támogatási kérelem megnyitása](https://go.microsoft.com/fwlink/?linkid=857950)
