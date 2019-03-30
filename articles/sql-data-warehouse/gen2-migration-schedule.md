---
title: Áttelepíteni a meglévő Azure SQL Data Warehouse Gen2-re |} A Microsoft Docs
description: Útmutató az áttelepítés egy meglévő data warehouse Gen2 és régió szerint áttelepítés ütemezését.
services: sql-data-warehouse
author: mlee3gsd
ms.author: anumjs
ms.reviewer: jrasnick
manager: craigg
ms.assetid: 04b05dea-c066-44a0-9751-0774eb84c689
ms.service: sql-data-warehouse
ms.topic: article
ms.date: 02/09/2019
ms.openlocfilehash: 575b6384d910abac1c0a1184aef4aa72f686538c
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58648573"
---
# <a name="upgrade-your-data-warehouse-to-gen2"></a>Frissítse az adattárház Gen2-re

A Microsoft lehetővé teszi, hogy belépő szintű költségének adattárház végezhetőek.  Csökkentse számítási szinten képes legyen kezelni az erőforrás-igényes lekérdezéseket mostantól elérhetők az Azure SQL Data warehouse-hoz. A teljes bejelentés [alacsonyabb szintű támogatás Gen2 számítási](https://azure.microsoft.com/blog/azure-sql-data-warehouse-gen2-now-supports-lower-compute-tiers/). Az új ajánlat az alábbi táblázatban feltüntetett régiókban érhető el. A támogatott régiók Gen1 a korábbi adattárházak Gen2-re keresztül frissíthető vagy:

- **Az automatikus frissítési folyamat:** Az automatikus frissítéseket, amint az egy régióban érhető el a szolgáltatás ne indítsa.  Az automatikus frissítések elindulását, egy adott régióban, az egyes DW-frissítések során a kiválasztott karbantartási ütemezés kerül sor.
- [**Helyi frissítés Gen2-re:**](#self-upgrade-to-gen2) Szabályozhatja, mikor kell frissíteni, tegye a helyi frissítés Gen2-re. Ha még nem támogatott a régióban, visszaállíthatja közvetlenül egy helyreállítási pontról Gen2-példányra egy támogatott régióban.

## <a name="automated-schedule-and-region-availability-table"></a>Az Automatikus ütemezés és a régió rendelkezésre állási tábla

A következő táblázat összefoglalja régió szerint, amikor a alacsonyabb Gen2 számítási kapacitás rendelkezésre áll, és automatikus frissítéseket indításakor. A dátumok változhat. Ellenőrizze, hogy vissza amikor elérhetővé válik a régióban.

\* azt jelzi, hogy egy adott ütemezés alapján a régióban jelenleg nem érhető el.

| **Régió** | **Alacsonyabb Gen2 érhető el** | **Az automatikus frissítések megkezdése** |
|:--- |:--- |:--- |
| Kelet-Ausztrália |Elérhető |2019. május 1. |
| Délkelet-Ausztrália |2019. április 02. |2019. június 1. |
| Dél-Brazília |2019. május 15. |\* |
| Közép-Kanada |Elérhető |2019. május 1. |
| Kelet-Kanada |\* |\* |
| USA középső régiója |Elérhető |2019. május 1. |
| Kelet-Kína |\* |\* |
| Kelet-Kína 2 |\* |\* |
| Észak-Kína |\* |\* |
| Észak-Kína 2 |\* |\* |
| Kelet-Ázsia |Elérhető |2019. május 1. |
| USA keleti régiója |Elérhető |2019. május 1. |
| USA 2. keleti régiója |Elérhető |Május 1. 2019 |
| Közép-Franciaország |\* |\* |
| Közép-Németország |\* |\* |
| Németország nyugati középső régiója |2019. szeptember 1.|2020. január 2. |
| Közép-India |Elérhető |2019. május 1. |
| Dél-India |2019. április 02. |2019. június 1. |
| Kelet-Japán |Elérhető |2019. május 1. |
| Nyugat-Japán |Elérhető |2019. június 1. |
| Korea középső régiója |2019. április 02. |2019. május 1. |
| Korea déli régiója |2019. április 02. |2019. június 1. |
| USA északi középső régiója |2019. április 02. |2019. június 1. |
| Észak-Európa |Elérhető |2019. május 1. |
| USA déli középső régiója |Elérhető |2019. május 1. |
| Délkelet-Ázsia |Elérhető |2019. május 1. |
| Az Egyesült Királyság déli régiója |2019. április 02. |2019. május 1. |
| Az Egyesült Királyság nyugati régiója |\*|\* |
| USA nyugati középső régiója |2019. szeptember 2. |2020. január 2.|
| Nyugat-Európa |Elérhető |2019. május 1. |
| USA nyugati régiója |2019. április 02. |2019. június 1. |
| USA nyugati régiója, 2. |Elérhető |2019. május 1. |

## <a name="automatic-upgrade-process"></a>Az automatikus frissítési folyamat

A fenti rendelkezésre állási diagram alapján, azt fogja ütemezni a Gen1-példányok automatikus frissítéseket. Váratlan megakadás rendelkezésre az adatraktár elkerülése érdekében az automatikus frissítésekre lesz ütemezve a karbantartási ütemezéshez során. Ütemezések szerint további információkért lásd: [karbantartási ütemezés megtekintése](viewing-maintenance-schedule.md)

A frissítési folyamat is szükség lesz egy rövid dobja el a kapcsolat (körülbelül 5 perc), hogy indítsa újra az adattárházba.  Az adattárház újra lett indítva, miután teljes mértékben elérhető lesz. Azonban a teljesítménye léphetnek fel, a frissítési folyamat továbbra is a háttérben lévő adatfájlok frissítése. Változnak, hogy a teljesítményromlást fordított időt az adatfájlokat méretétől függ.

A fájl adatfrissítési folyamat futtatásával is felgyorsíthatja [Alter Index rebuild](sql-data-warehouse-tables-index.md) az összes elsődleges oszlopcentrikus táblákba, az újraindítás után egy nagyobb slo-t és az erőforrás osztály használatával.

> [!NOTE]
> Az ALTER Index rebuild egy offline művelet, és a táblák nem lesz elérhető az újjáépítést befejeződéséig.

## <a name="self-upgrade-to-gen2"></a>Helyi frissítés Gen2-re

Választhat, saját frissítéséhez az alábbi lépéseket egy meglévő Gen1 adattárházra. Ha önálló frissíteni kívánja, azt az automatikus frissítési folyamat megkezdése az Ön régiójában előtt kell végrehajtania. Ez biztosítja, hogy az automatikus frissítések ütközést okozó veszélyének elkerülése érdekében.

Két lehetőség van során egy helyi frissítése.  Az aktuális data warehouse helyben vagy frissítheti, vagy Gen1 adattárház állíthatja be egy Gen2-példányt.

- [Frissítés helyben](upgrade-to-latest-generation.md) – Ez a beállítás frissíti a meglévő Gen1 adattárház Gen2-re. A frissítési folyamat is szükség lesz egy rövid dobja el a kapcsolat (körülbelül 5 perc), hogy indítsa újra az adattárházba.  Az adattárház újra lett indítva, miután teljes mértékben elérhető lesz. Ha a frissítés során problémákat tapasztal, nyisson meg egy [támogatási kérelem](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket) és "Frissítés Gen2" lehetséges okaként hivatkoznak.
- [Frissítés a visszaállítási pont](sql-data-warehouse-restore.md) – egy felhasználói visszaállítási pont létrehozása az aktuális Gen1 adattárházra, és állítsa vissza a közvetlenül egy Gen2-példányt. A meglévő Gen1 adatraktár érvényben marad. A visszaállítás befejezését követően az Gen2-adattárház teljes mértékben elérhető lesz.  A visszaállított Gen2-példányon összes tesztelés és ellenőrzés folyamat futtatása után az eredeti Gen1 példány lehet törölni.

   - 1. lépés: Az Azure Portalról [egy felhasználói visszaállítási pont létrehozása a](sql-data-warehouse-restore.md#create-a-user-defined-restore-point-using-the-azure-portal).
   - 2. lépés: Visszaállításakor a felhasználó által megadott visszaállítási pont, a "teljesítmény szint" értékre az előnyben részesített Gen2 réteghez.

A frissítési folyamat továbbra is a háttérben lévő adatfájlok frissítése akár teljesítménycsökkenés bizonyos tapasztalhat. Változnak, hogy a teljesítményromlást fordított időt az adatfájlokat méretétől függ.

Gyorsíthatja fel az adatok áttelepítési háttérfolyamatként, azonnal kényszerítheti az adatmozgatás futtatásával [Alter Index rebuild](sql-data-warehouse-tables-index.md) az összes elsődleges oszlopcentrikus táblák, akkor lekérdezése nagyobb slo-t és az erőforrás osztályra.

> [!NOTE]
> Az ALTER Index rebuild egy offline művelet, és a táblák nem lesz elérhető az újjáépítést befejeződéséig.

Ha problémát tapasztal az adatraktárban, hozzon létre egy [támogatási kérelem](sql-data-warehouse-get-started-create-support-ticket.md) és "Frissítés Gen2" lehetséges okaként hivatkoznak.

További információkért lásd: [frissítés Gen2-re](upgrade-to-latest-generation.md).

## <a name="migration-frequently-asked-questions"></a>Migrálás – gyakori kérdések

**K: Nem a Gen2 költség ugyanaz, mint Gen1?**

- V: Igen.

**K: Hogyan érinti a frissítéseket az automatizálási szkriptek?**

- V: Minden olyan automation-szkript, amely hivatkozik a szolgáltatási szint célkitűzésének kell módosítani, hogy a Gen2 megfelelője.  Részletek [Itt](upgrade-to-latest-generation.md#sign-in-to-the-azure-portal).

**K: Mennyi helyi frissítés általában tart?**

- V: Frissítés helyben vagy egy visszaállítási pont frissítése is.  
   - A hely frissítése miatt az adattárház rövid ideig szüneteltetéséről és folytatásáról.  Egy háttérfolyamat továbbra is közben az adatraktár online állapotban.  
   - Hosszabb ideig tart, ha frissíti a keresztül egy visszaállítási pontot, mert a frissítés fog áthaladni a teljes visszaállítási folyamat.

**K: Mennyi ideig lesz az automatikus frissítés igénybe?**

- V: A tényleges állásidő a frissítéshez az csak az az idő, szüneteltetése és folytatása a szolgáltatást, amely közötti 5-10 percet vesz igénybe. A rövid lefelé-idő után egy háttérfolyamat tárterület-áttelepítés fog futni. Mennyi ideig a rétegzést szolgáltatás az adattárház méretétől függ.

**K: Ha az automatikus frissítés történik?**

- V: Során a karbantartási ütemezéshez. A kiválasztott karbantartási ütemezés kihasználva fog minimalizálása érdekében az Ön számára.

**K: Mit tegyek, ha a háttérben futó frissítés úgy tűnik, hogy elakadt?**

 - V: Elindít egy reindex az Oszlopcentrikus táblák. Vegye figyelembe, hogy a tábla újraindexelés lesz kapcsolva a hálózatról a művelet közben.

**K: Mi történik, ha Gen2 nem rendelkezik a szolgáltatási szint célkitűzésének kell a Gen1?**
- V: Ha egy dw600 értékre vagy DW1200 Gen1 futnak, célszerű DW500c vagy DW1000c rendre használata óta Gen2 több memória, erőforrások és Gen1-nál nagyobb teljesítményt biztosít.

**K: Letilthatja a georedundáns biztonsági mentési?**
- V: Nem. Georedundáns biztonsági mentési rendszer vállalati felhasználásra szánt szolgáltatása az adatok megőrzése érdekében rendelkezésre állási adatraktár-abban az esetben, ha egy régió elérhetetlenné válik. Nyissa meg a [támogatási kérelem](sql-data-warehouse-get-started-create-support-ticket.md) Ha kétségei vannak további.

**K: Van különbség a T-SQL-szintaxisban Gen1 és Gen2 között?**

- V: Nem történik változás a T-SQL nyelvi szintaxist a Gen1 Gen2-re.

**K: Gen2 támogatja a Windows karbantartási?**

- V: Igen.

**K: Átvihetem létre tudja hozni egy új Gen1 példányt, miután frissítette az én régiómban?**

- V: Nem. Miután frissítette a régiót, új Gen1 példányok létrehozását letiltásra kerül.

## <a name="next-steps"></a>További lépések

- [Frissítési lépései](upgrade-to-latest-generation.md)
- [Karbantartási időszakok](maintenance-scheduling.md)
- [Erőforrás állapotának figyelése](https://docs.microsoft.com/azure/service-health/resource-health-overview)
- [Az áttelepítés megkezdése előtt tekintse át](upgrade-to-latest-generation.md#before-you-begin)
- [Frissítés helyben és a egy visszaállítási pont frissítése](upgrade-to-latest-generation.md)
- [Hozzon létre egy felhasználói visszaállítási pont](sql-data-warehouse-restore.md#restore-through-the-azure-portal)
- [Ismerje meg, hogyan lehet visszaállítani Gen2-re](sql-data-warehouse-restore.md#restore-an-active-or-paused-database-using-the-azure-portal)
- [Hozzon létre egy SQL Data Warehouse támogatási kérést](https://go.microsoft.com/fwlink/?linkid=857950)
