---
title: Az Azure SQL Data Warehouse biztonsági mentési és visszaállítási - pillanatképeket, georedundáns |} Microsoft Docs
description: Ismerje meg, az Azure SQL Data Warehouse biztonsági mentési és visszaállítási működése. Az adatok adatraktár biztonsági használatával állítsa vissza az adatraktár egy visszaállítási pontot az elsődleges régióban, vagy georedundáns biztonsági mentések használatával állítsa vissza egy másik földrajzi régióban.
services: sql-data-warehouse
author: ronortloff
manager: jhubbard
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: design
ms.date: 03/28/2018
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: 7f540bca0d2eb2c9009a386bd14a5beda2912014
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2018
---
# <a name="backup-and-restore-in-azure-sql-data-warehouse"></a>Biztonsági mentés és visszaállítás az Azure SQL Data Warehouse
Ismerje meg, az Azure SQL Data Warehouse biztonsági mentési és visszaállítási működése. Az adatok adatraktár biztonsági használatával állítsa vissza az adatraktár egy visszaállítási pontot az elsődleges régióban, vagy georedundáns biztonsági mentések használatával állítsa vissza egy másik földrajzi régióban. 

## <a name="what-is-backup-and-restore"></a>Mi az biztonsági mentési és visszaállítási?
A *adatraktár biztonsági mentését* , melyekkel egy adatraktár visszaállítani az adatbázis másolata.  Mivel az SQL Data Warehouse egy elosztott rendszerek, adatok adatraktár biztonsági sok fájl található az Azure storage áll. Egy adatraktár biztonsági mentését a helyi adatbázis-pillanatképek és földrajzi-biztonsági mentések az összes adatbázist és egy adatraktár társított fájlok is tartalmaz. 

A *az adatraktár-visszaállítási* új adatraktárat egy meglévő biztonsági másolatából létrehozott vagy törölt data warehouse-bA. A visszaállított adatok adatraktár újra létrehozza a biztonsági másolat adatraktár adott időpontban. Az adatraktár a visszaállításakor nincs bármely üzleti folytonossági és vészhelyreállítási helyreállítási stratégia nagyon fontos részét képezik, mert az adatok véletlen sérülése vagy a törlés után újra létre.

A helyi és földrajzi visszaállítása az SQL Data Warehouse vész-helyreállítási funkciók részét képezik. 

## <a name="local-snapshot-backups"></a>Helyi pillanatfelvétel biztonsági mentések
Helyi pillanatképes biztonsági a szolgáltatás egy beépített szolgáltatás.  Nem kell engedélyezni őket. 

Az SQL Data Warehouse pillanatképek készítése az adatraktár, napjainkat vesz igénybe. A pillanatképek a hét napja érhetők el. Az SQL Data Warehouse a nyolc óra helyreállításipont-célkitűzés (RPO) támogatja. Az adatraktár az elsődleges régióban visszaállíthatja a pillanatképek hajtja végre, az elmúlt hét napban egyikét sem.

Az online SQL Data Warehouse a lekérdezés futtatása a legutolsó pillanatfelvétel indításakor megtekintéséhez. 

```sql
select   top 1 *
from     sys.pdw_loader_backup_runs 
order by run_id desc
;
```

### <a name="snapshot-retention-when-a-data-warehouse-is-paused"></a>Ha egy adatraktár fel van függesztve pillanatkép megőrzési
Az SQL Data Warehouse nem hoz létre pillanatképek, és nem jár le pillanatképek közben az adatraktár működése szünetel. A pillanatkép-kor nem változtatja meg során az adatraktár működése szünetel. Pillanatkép megőrzési az adatraktár online állapotban és nem naptári napokon napok számát alapul.

Például ha pillanatkép kezdődik. október 1 du. 4, és az adatraktár du. 4: 3. októberi szünetel, a pillanatképeket legfeljebb két napos definícióval. Az adatraktár ismét online elérhető lesz a pillanatkép esetén két napos definícióval. Az adatraktár online állapotba kerül. október 5 du. 4:, ha a pillanatkép két napos, és öt nap marad.

Az adatraktár ismét online elérhető lesz, amikor az SQL Data Warehouse folytatja az új pillanatképeket, és pillanatképek lejár, ha az adatok több mint hét nap.

### <a name="snapshot-retention-when-a-data-warehouse-is-dropped"></a>Ha egy adatraktár megszakad a pillanatkép megőrzési
Amikor egy adatraktár, az SQL Data Warehouse végső pillanatképet készít, és menti a hét napja. Az adatraktár visszaállíthatja a végső visszaállítási pont létrehozása, törlés. 

> [!IMPORTANT]
> Ha törli a logikai SQL server-példány, a példányhoz tartozó összes adatbázis is törlődnek, és nem állítható helyre. A Törölt kiszolgáló nem tudja visszaállítani.
> 

## <a name="geo-backups"></a>Földrajzi-biztonsági mentések
Az SQL Data Warehouse egy földrajzi-biztonsági mentés naponta egyszer indít hajt végre egy [párosított adatközpont](../best-practices-availability-paired-regions.md). A helyreállítási Időkorlát földrajzi-helyreállítás 24 óra. A kiszolgáló párosítása, földrajzi régióban visszaállíthatja a földrajzi-biztonsági mentés. Egy földrajzi – biztonsági másolat biztosítja, hogy visszaállíthassa data warehouse-ba, abban az esetben, ha nem fér hozzá a pillanatképeket az elsődleges régióban.

Földrajzi-biztonsági mentések a rendszer alapértelmezés szerint. Ha az adatraktár úgy optimalizálták, hogy a rugalmasság, akkor [nem vesznek részt](/powershell/module/azurerm.sql/set-azurermsqldatabasegeobackuppolicy) Ha. Nem tilthatják földrajzi-biztonsági másolatok a optimalizált számítási teljesítmény szinthez.

## <a name="backup-costs"></a>Biztonsági mentési költségek
Megfigyelheti, hogy az Azure számlázásának van egy sor eleme a prémium szintű Azure Storage és a sor elem georedundáns tárolást. A prémium szintű Storage kell fizetni az adatok tárolása az elsődleges régióban, köztük a pillanatképek teljes költsége.  A georedundáns kell fizetni hozzá van rendelve a költség, a földrajzi-biztonsági mentések tárolására.  

A teljes költség az elsődleges adatraktár és az Azure Blob-pillanatképek hét nap kerekíti a legközelebbi TB. Például ha az adatraktár 1,5 TB, és a pillanatképek a 100 GB, díját is felszámítjuk a 2 TB prémium szintű Azure Storage ütemben adat. 

> [!NOTE]
> Minden egyes pillanatkép üres kezdetben, és módosítja az elsődleges adatraktár nő. A data warehouse módosítás a méret növekedését összes pillanatképet. Ezért a pillanatképek tárolási költségek megfelelően módosítsa a aránya nő.
> 
> 

Ha georedundáns tárolást használ, akkor kap egy különálló tárhelyet kell fizetni. A georedundáns tárolás az írásvédett földrajzilag redundáns tárolás (RA-GRS) alapdíj lesz számlázva.

További információ az SQL Data Warehouse díjszabása: [SQL Data Warehouse díjszabása](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).

## <a name="restoring-from-restore-points"></a>A visszaállítási pontok visszaállítása
Minden pillanatképet egy visszaállítási pontot a pillanatkép elindításakor jelölő rendelkezik. Adatraktár visszaállításához válasszon visszaállítási pontot, és adja ki a restore parancsot.  

Az SQL Data Warehouse mindig visszaállítja a biztonsági mentés új adatraktárat. A visszaállított adatok adatraktár és az aktuális hagyja, vagy törölje az egyik legyen. Ha az aktuális adatraktár cserélje le a visszaállított adatok adatraktár szeretné, hogy át lehessen nevezni használatával [ALTER DATABASE (Azure SQL Data Warehouse)](/sql/t-sql/statements/alter-database-azure-sql-data-warehouse) a nevét módosíthatja kapcsolóval. 

Adatraktár visszaállítani, tekintse meg a [visszaállítása az Azure portál használatával adatraktár](sql-data-warehouse-restore-database-portal.md), [állítsa vissza a PowerShell használatával adatraktár](sql-data-warehouse-restore-database-powershell.md), vagy [visszaállítása egy T-SQL használatával adatraktár](sql-data-warehouse-restore-database-rest-api.md) .

A törölt vagy szüneteltetett adatraktár visszaállításához is [támogatási jegy létrehozása](sql-data-warehouse-get-started-create-support-ticket.md). 


## <a name="geo-redundant-restore"></a>Georedundáns helyreállítás
Visszaállíthatja az adatraktár minden régióban támogató Azure SQL Data Warehouse, a kiválasztott teljesítményszint szükséges. 

> [!NOTE]
> Georedundáns visszaállításhoz kell nem visszavonta igényét ezt a szolgáltatást.
> 
> 

## <a name="restore-timeline"></a>Az idősor visszaállítása
Visszaállíthatja egy adatbázis bármely elérhető visszaállítási pont az utóbbi hét napban. A pillanatképek négy és nyolc óránként start, és elérhető hét napja. Ha pillanatkép régebbi, mint hét nap, jár le, és a helyreállítási pont már nem érhető el. 

Biztonsági mentések nem kerül sor egy felfüggesztett adatraktár. Ha az adatraktár fel van függesztve, több mint hét napja, nem kell minden visszaállítási pontok. 

## <a name="restore-costs"></a>Állítsa vissza a költségek
A tárolási költség a visszaállított adatraktár számlázása a prémium szintű Azure Storage ütemben történik. 

A visszaállított adatok adatraktár szünetelteti, ha van szó, a prémium szintű Azure Storage díj tárolására. Felfüggesztés előnye nem kell fizetnie a számítási erőforrások.

További információ az SQL Data Warehouse díjszabása: [SQL Data Warehouse díjszabása](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).

## <a name="restore-use-cases"></a>Állítsa vissza a használati esetek
Az elsődleges a data warehouse visszaállítási használata adatok véletlen adatvesztést vagy -sérülés utáni helyreállításhoz. Data warehouse visszaállítási használatával több mint hét napja a biztonsági másolatok megőrzése. A biztonsági mentés helyreáll, amennyiben az adatraktár online rendelkezik, és akár szüneteltetheti is, hogy határozatlan ideig számítási költségek csökkentése érdekében. A felfüggesztett adatbázis terhel tárolási a prémium szintű Azure Storage díj. 

## <a name="next-steps"></a>További lépések
Katasztrófa tervezésével kapcsolatos további információkért lásd: [üzleti folytonosság – áttekintés](../sql-database/sql-database-business-continuity.md)
