---
title: Meglévő Azure SQL Data Warehouse migrálása a Gen2-be | Microsoft Docs
description: Útmutató egy meglévő adattárház Gen2-re történő áttelepítéséhez és az áttelepítési ütemterv régiónként történő áttelepítéséhez.
services: sql-data-warehouse
author: mlee3gsd
ms.author: anjangsh
ms.reviewer: jrasnick
manager: craigg
ms.assetid: 04b05dea-c066-44a0-9751-0774eb84c689
ms.service: sql-data-warehouse
ms.topic: article
ms.date: 07/22/2019
ms.openlocfilehash: d4724672510d6ccbbc819691d621400cb00d8c9a
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2019
ms.locfileid: "68405439"
---
# <a name="upgrade-your-data-warehouse-to-gen2"></a>Az adattárház frissítése a Gen2

A Microsoft segít elvezetni az adatraktár futtatásának belépési szintű költségeit.  A nagy teljesítményű lekérdezések kezelésére alkalmas alacsonyabb számítási rétegek már elérhetők Azure SQL Data Warehouse számára. Olvassa el a teljes bejelentést a [Gen2-hez készült, alacsonyabb számítási szintű támogatással](https://azure.microsoft.com/blog/azure-sql-data-warehouse-gen2-now-supports-lower-compute-tiers/). Az új ajánlat az alábbi táblázatban említett régiókban érhető el. A támogatott régiók esetében a meglévő Gen1-adattárházak a következőkre frissíthetők: Gen2

- **Az automatikus frissítési folyamat:** Az automatikus frissítések nem indulnak el, amint a szolgáltatás elérhető egy régióban.  Ha az automatikus frissítések egy adott régióban kezdődnek, az egyes DW-frissítések a kiválasztott karbantartási ütemezés szerint zajlanak.
- [**Saját frissítés a Gen2-re:** ](#self-upgrade-to-gen2) A frissítéshez beállíthatja, hogy mikor kell frissíteni a Gen2. Ha a régiója még nem támogatott, a visszaállítási pontról közvetlenül egy támogatott régió Gen2-példányára állíthatja vissza.

## <a name="automated-schedule-and-region-availability-table"></a>Automatizált ütemezések és régiók rendelkezésre állási táblázata

A következő táblázat összefoglalja a régiót, ha az alsó Gen2 számítási szintje elérhető lesz, és az automatikus frissítés elindul. A dátumok változhatnak. Térjen vissza, és tekintse meg, hogy mikor válik elérhetővé a régió.

\*azt jelzi, hogy a régióhoz megadott ütemterv jelenleg nem érhető el.

| **Régió** | **Alacsonyabb Gen2 érhető el** | **Automatikus frissítések kezdete** |
|:--- |:--- |:--- |
| Kelet-Ausztrália |Elérhető |Kész |
| Délkelet-Ausztrália |Elérhető |Kész |
| Dél-Brazília |Elérhető |Kész |
| Közép-Kanada |Elérhető |Kész |
| Kelet-Kanada |2020. június 1. |2020. július 1. |
| USA középső régiója |Elérhető |Kész |
| Kelet-Kína |\* |\* |
| Kelet-Kína 2 |Elérhető |Kész |
| Észak-Kína |\* |\* |
| Észak-Kína 2 |Elérhető |Kész |
| Kelet-Ázsia |Elérhető |Kész |
| East US |Elérhető |Kész |
| USA 2. keleti régiója |Elérhető |Kész |
| Közép-Franciaország |Elérhető |Folyamatban |
| Közép-Németország |\* |\* |
| Németország nyugati középső régiója |2019. szeptember 1.|2019. október 1. |
| Közép-India |Elérhető |Kész |
| Dél-India |Elérhető |Kész |
| Nyugat-India |2019. július 1. |Folyamatban |
| Kelet-Japán |Elérhető |Kész |
| Nyugat-Japán |Elérhető |Kész |
| Korea középső régiója |Elérhető |Kész |
| Korea déli régiója |Elérhető |Kész |
| USA északi középső régiója |Elérhető |Kész |
| Észak-Európa |Elérhető |Kész |
| Dél-Afrika északi régiója |2019. július 12. |Kész |
| USA déli középső régiója |Elérhető |Kész |
| Délkelet-Ázsia |Elérhető |Kész |
| Egyesült Arab Emírségek északi régiója |2019. július 20. |Kész |
| Az Egyesült Királyság déli régiója |Elérhető |Folyamatban |
| Az Egyesült Királyság nyugati régiója |Elérhető |Folyamatban |
| USA nyugati középső régiója |2019. szeptember 1. |2019. október 1.|
| Nyugat-Európa |Elérhető |Kész |
| USA nyugati régiója |Elérhető |Kész |
| USA nyugati régiója, 2. |Elérhető |Kész |

## <a name="automatic-upgrade-process"></a>Automatikus frissítési folyamat

A fenti rendelkezésre állási diagram alapján a Gen1-példányok automatikus frissítéseit ütemezjük. Az adatraktár rendelkezésre állásával kapcsolatos váratlan megszakítások elkerülése érdekében az automatikus frissítések ütemezése a karbantartási ütemezés szerint történik. Az új Gen1-példány létrehozásának lehetősége le lesz tiltva a Gen2-re történő automatikus frissítéssel rendelkező régiókban. Az automatikus frissítések befejezését követően a Gen1 elavulttá válik. További információ az ütemtervekről: [karbantartási ütemterv megtekintése](viewing-maintenance-schedule.md)

A frissítési folyamat során az adattárház újraindításakor a kapcsolat rövid csökkenése (körülbelül 5 perc) lesz.  Az adattárház újraindítása után a szolgáltatás teljes mértékben elérhető lesz. A teljesítmény romlása azonban akkor is előfordulhat, ha a frissítési folyamat továbbra is frissíti az adatfájlokat a háttérben. A teljesítménycsökkenés teljes időtartama az adatfájlok méretétől függően változik.

Az adatfájlok frissítési folyamatát is felgyorsíthatja az [Alter index](sql-data-warehouse-tables-index.md) Újraépítés az összes elsődleges oszlopcentrikus-táblán való futtatásával, az újraindítás után egy nagyobb slo-t és egy erőforrás-osztályt használva.

> [!NOTE]
> Az Alter index Rebuild egy offline művelet, és a táblák addig nem lesznek elérhetők, amíg az Újraépítés be nem fejeződik.

## <a name="self-upgrade-to-gen2"></a>Saját frissítés a Gen2-re

Az alábbi lépéseket egy meglévő Gen1-adattárházon végezheti el: saját frissítés. Ha úgy dönt, hogy saját frissítést végez, az automatikus frissítési folyamat megkezdése előtt el kell végeznie azt a régióban. Így biztosítható, hogy az automatikus frissítések kockázata ne okozzon ütközést.

Önfrissítéskor két lehetőség közül választhat.  Frissítheti az aktuális adattárházat helyben, vagy visszaállíthatja egy Gen1-adattárházat egy Gen2-példányba.

- [Helyben történő frissítés](upgrade-to-latest-generation.md) – ez a beállítás a meglévő Gen1-adattárházat a Gen2-re frissíti. A frissítési folyamat során az adattárház újraindításakor a kapcsolat rövid csökkenése (körülbelül 5 perc) lesz.  Az adattárház újraindítása után a szolgáltatás teljes mértékben elérhető lesz. Ha a frissítés során problémák merülnek fel, nyisson meg egy [támogatási kérést](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket) , és hivatkozzon a "Gen2 upgrade" kifejezésre a lehetséges okok miatt.
- [Frissítés visszaállítási pontról](sql-data-warehouse-restore.md) – hozzon létre egy felhasználó által definiált visszaállítási pontot az aktuális Gen1-adatraktárban, majd állítsa vissza közvetlenül egy Gen2-példányra. A meglévő Gen1-adattárház továbbra is érvényben marad. A visszaállítás befejezése után a Gen2-adattárház teljes mértékben elérhető lesz a használatra.  Miután futtatta az összes tesztelési és érvényesítési folyamatot a visszaállított Gen2-példányon, törölheti az eredeti Gen1-példányt.

   - 1\. lépés: A Azure Portal [hozza létre a felhasználó által definiált visszaállítási pontot](sql-data-warehouse-restore.md#create-a-user-defined-restore-point-using-the-azure-portal).
   - 2\. lépés: Felhasználó által definiált visszaállítási pontról történő visszaállításkor állítsa a "teljesítményszint" értéket az előnyben részesített Gen2-szintre.

Visszaesést tapasztalhat a teljesítményben, miközben a frissítési folyamat az adatfájlok frissítését végzi a háttérben. A teljesítménycsökkenés teljes időtartama az adatfájlok méretétől függően változik.

A háttérben futó adatáttelepítési folyamat meggyorsítása érdekében azonnal kényszerítheti az adatáthelyezést az [Alter index](sql-data-warehouse-tables-index.md) -Újraépítés futtatásával minden olyan elsődleges oszlopcentrikus-táblán, amelyet egy nagyobb slo és Resource osztályban szeretne lekérdezni.

> [!NOTE]
> Az Alter index Rebuild egy offline művelet, és a táblák addig nem lesznek elérhetők, amíg az Újraépítés be nem fejeződik.

Ha bármilyen probléma merül fel az adattárházban, hozzon létre egy [támogatási kérést](sql-data-warehouse-get-started-create-support-ticket.md) , és hivatkozzon a "Gen2 upgrade" kifejezésre a lehetséges okok miatt.

További információ: [verziófrissítés a Gen2](upgrade-to-latest-generation.md).

## <a name="migration-frequently-asked-questions"></a>Áttelepítés – gyakori kérdések

**K: A Gen2 ára ugyanaz, mint a Gen1?**

- V: Igen.

**K: Hogyan befolyásolja a frissítések az Automation-parancsfájlokat?**

- V: A szolgáltatási szintre vonatkozó célkitűzésre hivatkozó Automation-parancsfájlokat úgy kell módosítani, hogy azok megfeleljenek a Gen2-nek.  Tekintse meg [a részleteket.](upgrade-to-latest-generation.md#sign-in-to-the-azure-portal)

**K: Mennyi ideig tart az önálló frissítés?**

- V: Egy visszaállítási pontról frissítheti a helyet vagy a frissítést.  
   - A helyben történő frissítés során az adattárház egy pillanatra szüneteltethető és folytatható.  A háttérben futó folyamat folytatódni fog, amíg az adattárház online állapotban van.  
   - Ha egy visszaállítási ponton keresztül frissíti a szolgáltatást, a frissítés a teljes visszaállítási folyamaton keresztül történik.

**K: Mennyi ideig tart az automatikus frissítés?**

- V: A frissítéssel járó tényleges állásidő csak az az időtartam, amely a szolgáltatás szüneteltetéséhez és újraindításához szükséges, azaz 5-10 perc. A rövid állásidőt követően egy háttérfolyamat tárolómigrálást fog futtatni. Az adattárház méretétől függ, hogy a háttérfolyamat mennyi idő alatt hajtja végre a migrálást.

**K: Mikor kerül sor az automatikus frissítésre?**

- V: Karbantartási ütemterve alatt. Ha kihasználja a kiválasztott karbantartási ütemtervet, a rendszer a vállalkozása számára csökkentheti a fennakadást.

**K: Mi a teendő, ha úgy tűnik, hogy a háttérben futó frissítési folyamat beragadt?**

 - V: Indítsa el a Oszlopcentrikus-táblák újraindexelését. Vegye figyelembe, hogy a tábla újraindexelése a művelet során offline állapotba kerül.

**K: Mi a teendő, ha a Gen2 nem rendelkezik a szolgáltatási szint célkitűzéssel a Gen1?**
- V: Ha kor DW600 vagy DW1200 futtat a Gen1-on, javasolt a DW500c vagy a DW1000c használata, mivel a Gen2 több memóriát, erőforrást és nagyobb teljesítményt nyújt, mint a Gen1.

**K: Letilthatom a Geo-biztonsági mentést?**
- V: Nem. A Geo-Backup egy vállalati szolgáltatás, amellyel megőrizheti az adattárház rendelkezésre állását abban az esetben, ha egy régió elérhetetlenné válik. Ha további kérdései vannak, nyisson meg egy [támogatási kérést](sql-data-warehouse-get-started-create-support-ticket.md) .

**K: Van különbség a Gen1 és a Gen2 között a T-SQL szintaxisban?**

- V: A T-SQL nyelvi szintaxisa nem változik a Gen1 és a Gen2 között.

**K: Támogatja a Gen2 a karbantartási időszakokat?**

- V: Igen.

**K: Létrehozhatok új Gen1-példányt a régióm frissítése után?**

- V: Nem. A régió frissítése után az új Gen1-példányok létrehozása le lesz tiltva.

## <a name="next-steps"></a>További lépések

- [Frissítési lépések](upgrade-to-latest-generation.md)
- [Karbantartási időszakok](maintenance-scheduling.md)
- [Erőforrás-állapot figyelője](https://docs.microsoft.com/azure/service-health/resource-health-overview)
- [Áttekintés az áttelepítés megkezdése előtt](upgrade-to-latest-generation.md#before-you-begin)
- [Helyben történő frissítés frissítése visszaállítási pontról](upgrade-to-latest-generation.md)
- [Felhasználó által definiált visszaállítási pont létrehozása](sql-data-warehouse-restore.md#restore-through-the-azure-portal)
- [Útmutató a Gen2 való visszaállításhoz](sql-data-warehouse-restore.md#restore-an-active-or-paused-database-using-the-azure-portal)
- [SQL Data Warehouse támogatási kérelem megnyitása](https://go.microsoft.com/fwlink/?linkid=857950)
