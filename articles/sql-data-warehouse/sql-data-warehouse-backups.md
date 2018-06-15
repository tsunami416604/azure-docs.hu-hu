---
title: Azure SQL Data Warehouse biztonsági mentések - pillanatképeket, georedundáns |} Microsoft Docs
description: Ismerje meg az SQL Data Warehouse beépített adatbázis biztonsági mentését, amelyek segítségével állíthatja vissza az Azure SQL Data Warehouse visszaállítási pont vagy egy másik földrajzi régióban.
services: sql-data-warehouse
documentationcenter: ''
author: barbkess
manager: jhubbard
editor: ''
ms.assetid: b5aff094-05b2-4578-acf3-ec456656febd
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.custom: backup-restore
ms.date: 10/23/2017
ms.author: jrj;barbkess
ms.openlocfilehash: 159a1d34caba829750da33dbc4ad403fb21cd147
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
ms.locfileid: "30198504"
---
# <a name="backup-and-restore-in-sql-data-warehouse"></a>Biztonsági mentés és visszaállítás az SQL Data Warehouse
Ez a cikk ismerteti az SQL Data Warehouse a biztonsági másolatok tulajdonságait. Az adatok adatraktár biztonsági használatával állítsa vissza egy pillanatképet készíteni az elsődleges régióban adatbázist, vagy földrajzi – biztonsági másolat visszaállítása a párosítása, földrajzi régióban. 

## <a name="what-is-a-data-warehouse-backup"></a>Mi az az adatraktár biztonsági mentését?
Egy adatraktár biztonsági mentését az adatbázis, adatraktár visszaállítására használható másolatát.  Mivel az SQL Data Warehouse egy elosztott rendszerek, adatok adatraktár biztonsági sok fájl található az Azure storage áll. Egy adatraktár biztonsági mentését a helyi adatbázis-pillanatképek és földrajzi-biztonsági mentések az összes adatbázist és egy adatraktár társított fájlok is tartalmaz. 

## <a name="local-snapshot-backups"></a>Helyi pillanatfelvétel biztonsági mentések
Az SQL Data Warehouse pillanatképek készítése az adatraktár, napjainkat vesz igénybe. A pillanatképek a hét napja érhetők el. Az SQL Data Warehouse a nyolc óra helyreállításipont-célkitűzés (RPO) támogatja. Az adatraktár az elsődleges régióban visszaállíthatja a pillanatképek hajtja végre, az elmúlt hét napban egyikét sem.

Az online SQL Data Warehouse a lekérdezés futtatása a legutolsó pillanatfelvétel indításakor megtekintéséhez. 

```sql
select   top 1 *
from     sys.pdw_loader_backup_runs 
order by run_id desc
;
```

## <a name="geo-backups"></a>Földrajzi-biztonsági mentések
Az SQL Data Warehouse egy földrajzi-biztonsági mentés naponta egyszer indít hajt végre egy [párosított adatközpont](../best-practices-availability-paired-regions.md). A helyreállítási Időkorlát földrajzi-helyreállítás 24 óra. A kiszolgáló párosítása, földrajzi régióban visszaállíthatja a földrajzi-biztonsági mentés. földrajzi – biztonsági másolat biztosítja, hogy visszaállíthassa data warehouse-ba, abban az esetben, ha nem fér hozzá a pillanatképeket az elsődleges régióban.

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

## <a name="snapshot-retention-when-a-data-warehouse-is-paused"></a>Ha egy adatraktár fel van függesztve pillanatkép megőrzési
Az SQL Data Warehouse nem hoz létre pillanatképek, és nem jár le pillanatképek közben az adatraktár működése szünetel. A pillanatkép-kor nem változtatja meg során az adatraktár működése szünetel. Pillanatkép megőrzési az adatraktár online állapotban és nem naptári napokon napok számát alapul.

Például ha pillanatkép kezdődik. október 1 du. 4, és az adatraktár du. 4: 3. októberi szünetel, a pillanatképeket legfeljebb két napos definícióval. Az adatraktár ismét online elérhető lesz a pillanatkép esetén két napos definícióval. Az adatraktár online állapotba kerül. október 5 du. 4:, ha a pillanatkép két napos, és öt nap marad.

Az adatraktár ismét online elérhető lesz, amikor az SQL Data Warehouse folytatja az új pillanatképeket, és pillanatképek lejár, ha az adatok több mint hét nap.

## <a name="can-i-restore-a-dropped-data-warehouse"></a>Visszaállíthatja egy eldobott data warehouse?
Amikor egy adatraktár, az SQL Data Warehouse végső pillanatképet készít, és menti a hét napja. Az adatraktár visszaállíthatja a végső visszaállítási pont létrehozása, törlés. 

> [!IMPORTANT]
> Ha törli a logikai SQL server-példány, a példányhoz tartozó összes adatbázis is törlődnek, és nem állítható helyre. A Törölt kiszolgáló nem tudja visszaállítani.
> 

## <a name="next-steps"></a>További lépések
SQL data warehouse visszaállítani, tekintse meg a [visszaállítása az SQL data warehouse](sql-data-warehouse-restore-database-overview.md).

Üzleti folytonosság áttekintéséért lásd: [üzleti folytonosság – áttekintés](../sql-database/sql-database-business-continuity.md)
