---
title: Az SQL-készlet áttelepítése a Gen2 rendszerbe
description: Meglévő SQL-készlet Gen2-be való áttelepítésére és az áttelepítési ütemezés régiónkénti áttelepítésére vonatkozó utasítások.
services: synapse-analytics
author: mlee3gsd
ms.author: anjangsh
ms.reviewer: jrasnick
manager: craigg
ms.assetid: 04b05dea-c066-44a0-9751-0774eb84c689
ms.service: synapse-analytics
ms.topic: article
ms.date: 01/21/2020
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 6f2af826473bfd13f8100796a540d41cbedbb037
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80631567"
---
# <a name="upgrade-your-sql-pool-to-gen2"></a>Az SQL-készlet frissítése Gen2-re

A Microsoft segít csökkenteni az SQL-készlet futtatásának belépő szintű költségeit.  Alacsonyabb számítási szintek, amelyek képesek kezelni a nagy igénybevételt jelentő lekérdezések már elérhető az SQL-készlet. Olvassa el a teljes bejelentést [A Gen2 alacsonyabb számítási szinttámogatása](https://azure.microsoft.com/blog/azure-sql-data-warehouse-gen2-now-supports-lower-compute-tiers/). Az új ajánlat az alábbi táblázatban említett régiókban érhető el. Támogatott régiók esetén a meglévő Gen1 SQL-készletek a következők valamelyikén keresztül frissíthetők Gen2-re:

- **Az automatikus frissítési folyamat:** Az automatikus frissítések nem indulnak el, amint a szolgáltatás elérhető vé válik egy régióban.  Amikor egy adott régióban automatikus frissítések kezdődnek, az egyes adattárház-frissítések a kiválasztott karbantartási ütemezés során történnek.
- [**Saját frissítés gen2-re:**](#self-upgrade-to-gen2) A Gen2-re történő önfrissítéssel szabályozhatja, hogy mikor frissítsen. Ha a régió még nem támogatott, visszaállíthatja a visszaállítási pontról közvetlenül egy Gen2 példány egy támogatott régióban.

## <a name="automated-schedule-and-region-availability-table"></a>Automatikus ütemezési és területi rendelkezésre állási tábla

Az alábbi táblázat régiónként foglalja össze, amikor az Alsó Gen2 számítási szint elérhető lesz, és amikor az automatikus frissítések elindulnak. A dátumok változhatnak. Látogasson vissza, hogy mikor a régió elérhetővé válik.

\*azt jelzi, hogy a régió ra vonatkozó adott ütemezés jelenleg nem érhető el.

| **Régió** | **Alsó Gen2 elérhető** | **Megkezdődik az automatikus frissítés** |
|:--- |:--- |:--- |
| Kelet-Kanada |2020. június 1. |2020. július 1. |
| Kelet-Kína |\* |\* |
| Észak-Kína |\* |\* |
| Közép-Németország |\* |\* |
| Németország Nyugat-Közép |Elérhető |2020. május 1. |
| Nyugat-India |Elérhető |2020. május 1.  |

## <a name="automatic-upgrade-process"></a>Automatikus frissítési folyamat

A fenti rendelkezésre állási diagram alapján automatikus frissítéseket ütemezünk a Gen1 példányokhoz. Az SQL-készlet rendelkezésre állásának váratlan megszakítása érdekében az automatikus frissítések ütemezése a karbantartási ütemezés során lesz ütemezve. Az új Gen1 példány létrehozásának lehetősége le lesz tiltva a Gen2-re automatikus frissítésalatt lévő régiókban. Az automatikus frissítések befejezése után az 1. Az ütemezésről a [Karbantartási ütemezés megtekintése című](maintenance-scheduling.md#view-a-maintenance-schedule) témakörben talál további információt.

A frissítési folyamat magában foglalja a kapcsolat rövid csökkenését (körülbelül 5 min), ahogy újraindítjuk az SQL-készletet.  Az SQL-készlet újraindítása után teljes mértékben elérhető lesz a használatra. Azonban előfordulhat, hogy a teljesítmény romlása, míg a frissítési folyamat továbbra is frissíti az adatfájlokat a háttérben. A teljesítménycsökkenés teljes időtartama az adatfájlok méretétől függően változik.

Az adatfájl-frissítési folyamatot úgy is felgyorsíthatja, hogy az újraindítás után az összes elsődleges oszlopcentrikus táblában [futtatja](sql-data-warehouse-tables-index.md) az Alter Index rebuild-t egy nagyobb SLO és erőforrásosztály használatával.

> [!NOTE]
> Az Alter Index rebuild egy offline művelet, és a táblák nem lesznek elérhetők, amíg az újraépítés befejeződik.

## <a name="self-upgrade-to-gen2"></a>Saját frissítés a Gen2-re

Dönthet úgy, hogy önfrissít egy meglévő Gen1 SQL-készlet en az alábbi lépéseket. Ha önfrissítést választ, akkor az automatikus frissítési folyamat megkezdése előtt kell végrehajtania azt a régióban. Ezzel biztosítja, hogy elkerülje az automatikus frissítések ütközést okozó kockázatát.

Az önfejlesztés során két lehetőség van.  Frissítheti az aktuális SQL-készletet a helyén, vagy visszaállíthatja a Gen1 SQL-készletet egy Gen2 példányba.

- [Helybeni frissítés](upgrade-to-latest-generation.md) – Ez a beállítás a meglévő Gen1 SQL-készletet Gen2-re frissíti. A frissítési folyamat magában foglalja a kapcsolat rövid csökkenését (körülbelül 5 min), ahogy újraindítjuk az SQL-készletet.  Az SQL-készlet újraindítása után teljes mértékben elérhető lesz a használatra. Ha problémákat tapasztal a frissítés során, nyisson meg egy [támogatási kérelmet,](sql-data-warehouse-get-started-create-support-ticket.md) és a "Gen2 frissítés" hivatkozással a lehetséges okként.
- [Frissítés visszaállítási pontról](sql-data-warehouse-restore-points.md) – Hozzon létre egy felhasználó által definiált visszaállítási pontot az aktuális Gen1 SQL-készleten, majd állítsa vissza közvetlenül egy Gen2 példányra. A meglévő Gen1 SQL-készlet a helyén marad. Miután a visszaállítás befejeződött, a Gen2 SQL-készlet teljes mértékben elérhető lesz a használatra.  Miután futtatta az összes tesztelési és érvényesítési folyamatot a visszaállított Gen2 példányon, az eredeti Gen1 példány törölhető.

  - 1. lépés: Az Azure [Portalon hozzon létre egy felhasználó által definiált visszaállítási pontot.](sql-data-warehouse-restore-active-paused-dw.md)
  - 2. lépés: Amikor egy felhasználó által definiált visszaállítási pontról állít vissza, állítsa be a "teljesítményszintet" az előnyben részesített Gen2 szintre.

Visszaesést tapasztalhat a teljesítményben, miközben a frissítési folyamat az adatfájlok frissítését végzi a háttérben. A teljesítménycsökkenés teljes időtartama az adatfájlok méretétől függően változik.

A háttéradatok áttelepítési folyamatának meggyorsításához azonnal kényszerítheti az adatok mozgását az [Alter Index rebuild](sql-data-warehouse-tables-index.md) futtatásával az összes olyan elsődleges oszlopcentrikus táblán, amelyet egy nagyobb SLO- és erőforrásosztályban szeretne lekérdezni.

> [!NOTE]
> Az Alter Index rebuild egy offline művelet, és a táblák nem lesznek elérhetők, amíg az újraépítés befejeződik.

Ha bármilyen probléma merül fel az SQL-készlet, hozzon létre egy [támogatási kérelmet,](sql-data-warehouse-get-started-create-support-ticket.md) és hivatkozás "Gen2 frissítés" a lehetséges oka.

További információt a [Frissítés a Gen2 rendszerre](upgrade-to-latest-generation.md)című témakörben talál.

## <a name="migration-frequently-asked-questions"></a>Gyakori kérdések az áttelepítéssel kapcsolatban

**K: A Gen2 ugyanannyiba kerül, mint a Gen1?**

- V: Igen.

**K: Milyen hatással lesznek a frissítések az automatizálási parancsfájljaimra?**

- V: Minden olyan automatizálási parancsfájlt, amely egy szolgáltatásiszint-célkitűzésre hivatkozik, úgy kell módosítani, hogy megfeleljen a Gen2 megfelelőjének.  Lásd a részleteket [itt](upgrade-to-latest-generation.md#upgrade-in-a-supported-region-using-the-azure-portal).

**K: Általában mennyi ideig tart egy önfrissítés?**

- A: Frissíthet a helyén, vagy frissíthet egy visszaállítási pontról.

  - A helyben történő frissítés hatására az SQL-készlet egy pillanatra szünetelteti és folytatja.  A háttérfolyamat folytatódik, amíg az SQL-készlet online állapotban van.  
  - Hosszabb időt vesz igénybe, ha egy visszaállítási ponton keresztül frissít, mert a frissítés a teljes visszaállítási folyamaton megy keresztül.

**K: Mennyi ideig tart az automatikus frissítés?**

- A: A frissítés tényleges állásideje csak a szolgáltatás szüneteltetéséhez és folytatásához szükséges idő, amely 5 és 10 perc között van. A rövid állásidőt követően egy háttérfolyamat tárolómigrálást fog futtatni. A háttérfolyamat időtartama az SQL-készlet méretétől függ.

**K: Mikor lesz ez az automatikus frissítés?**

- A: A karbantartási ütemezés során. A kiválasztott karbantartási ütemterv kihasználásával minimálisra csökkenthető a vállalkozás zavara.

**K: Mi a teendő, ha úgy tűnik, hogy a háttérfrissítési folyamat beragadt?**

- A: Indítsa el az oszlopcentrikus táblák újraindexelését. Vegye figyelembe, hogy a tábla újraindexelése a művelet során offline lesz.

**K: Mi a teendő, ha a Gen2 nem rendelkezik a Gen1 szolgáltatásszintű célkitűzéssel?**

- V: Ha A DW600 vagy DW1200 a Gen1, akkor azt tanácsoljuk, hogy használja DW500c vagy DW1000c volt, mivel Gen2 nyújt több memóriát, erőforrásokat, és nagyobb teljesítményt, mint a Gen1.

**K: Letudom tiltani a geo-backup?Q: Can I Disable geo-backup?**

- V: Nem. A geo-backup egy vállalati szolgáltatás, amely megőrzi az SQL-készlet rendelkezésre állását abban az esetben, ha egy régió elérhetetlenné válik. Ha további aggályai vannak, nyisson meg egy [támogatási kérelmet.](sql-data-warehouse-get-started-create-support-ticket.md)

**K: Van különbség a T-SQL szintaxisban a Gen1 és a Gen2 között?**

- V: A T-SQL nyelv szintaxisa nem változott Gen1-ről Gen2-re.

**K: Támogatja a Gen2 a Maintenance Windows-t?**

- V: Igen.

**K: Létre tudok-e hozni egy új Gen1 példányt a régióm frissítése után?**

- V: Nem. A régió frissítése után az új Gen1 példányok létrehozása le lesz tiltva.

## <a name="next-steps"></a>További lépések

- [Frissítési lépések](upgrade-to-latest-generation.md)
- [Karbantartási időszakok](maintenance-scheduling.md)
- [Erőforrás-állapotfigyelő](../../service-health/resource-health-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
- [Ellenőrzés Az áttelepítés megkezdése előtt](upgrade-to-latest-generation.md#before-you-begin)
- [Frissítés helyben és frissítés visszaállítási pontról](upgrade-to-latest-generation.md)
- [Felhasználó által definiált visszaállítási pont létrehozása](sql-data-warehouse-restore-points.md)
- [Ismerje meg, hogyan kell visszaállítani a Gen2](sql-data-warehouse-restore-active-paused-dw.md)
- [SQL Data Warehouse támogatási kérelem megnyitása](https://go.microsoft.com/fwlink/?linkid=857950)
