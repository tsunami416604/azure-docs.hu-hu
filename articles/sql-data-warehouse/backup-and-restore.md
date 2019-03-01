---
title: Az Azure SQL Data Warehouse biztonsági mentési és visszaállítási - pillanatképeket, georedundáns |} A Microsoft Docs
description: Ismerje meg a biztonsági mentés és visszaállítás működését az Azure SQL Data Warehouse. Használat adattárházak biztonsági másolatai az adatraktár visszaállítása egy visszaállítási pontot az elsődleges régióba. Georedundáns biztonsági mentések segítségével állíthatja vissza egy másik földrajzi régióba.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 09/06/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 0d2c7fbba8184fba81be5e93eae1dd816687ecb4
ms.sourcegitcommit: f7f4b83996640d6fa35aea889dbf9073ba4422f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2019
ms.locfileid: "56992668"
---
# <a name="backup-and-restore-in-azure-sql-data-warehouse"></a>Biztonsági mentés és visszaállítás az Azure SQL Data Warehouse

Ismerje meg, hogyan biztonsági mentése és visszaállítása az Azure SQL Data Warehouse. Használja a data warehouse visszaállítási pontok helyre vagy az adattárház másolja az elsődleges régióban egy korábbi állapotba. Használja az adatraktár-georedundáns biztonsági másolatokat állíthatja vissza egy másik földrajzi régióban.

## <a name="what-is-a-data-warehouse-snapshot"></a>Mi a data warehouse pillanatkép

A *data warehouse pillanatkép* visszaállítási pontot hoz létre, mellyel helyreállítása vagy másolása az adattárház korábbi állapotába.  Az SQL Data Warehouse egy elosztott rendszer, mivel számos olyan fájlok, az Azure storage-ban található data warehouse pillanatkép áll. Pillanatképek rögzítése az adatraktárban tárolt adatok a növekményes változásokat.

A *az adatraktár-visszaállítási* egy új adattárház, amely jön létre egy visszaállítási pontot egy meglévő vagy egy törölt adatraktárat. Az adattárház visszaállítása azért nagyon fontos részét bármely üzleti folytonossági és vészhelyreállítási stratégia az adatok véletlen sérülés vagy a törlés után újra létrehoz. Data warehouse-ba is egy hatékony mechanizmus, amellyel a másolatokat az adatraktárról, tesztelési és fejlesztési célokra.  Az SQL Data Warehouse gyors visszaállítási mechanizmust használ, az azonos régióban, amely bármilyen adatméret kisebb, mint 20 perc alatt mért.

## <a name="automatic-restore-points"></a>Automatikus visszaállítási pontok

A szolgáltatás, amely létrehoz egy beépített funkciót a visszaállítási pontok pillanatképeket. Nem rendelkezik, ez a funkció engedélyezéséhez. Automatikus visszaállítási pontok jelenleg nem lehet törölni a felhasználók, ahol a szolgáltatás által használt, ezek a visszaállítási fenntartandó szolgáltatásiszint-szerződések helyreállítási pontok.

Az SQL Data Warehouse a nap, hét napja elérhető helyreállítási pontok létrehozása során az adatraktár pillanatképeket készít. A megőrzési időszak nem lehet módosítani. SQL Data Warehouse támogat egy 8 órás helyreállításipont-célkitűzés (RPO). Az elmúlt hét napban készített pillanatképeket azon állíthatja vissza az adattárház az elsődleges régióba.

A legutolsó pillanatfelvétel indításakor jelenik meg, hogy a lekérdezés futtatása az online SQL Data Warehouse.

```sql
select   top 1 *
from     sys.pdw_loader_backup_runs
order by run_id desc
;
```

## <a name="user-defined-restore-points"></a>Felhasználó által definiált visszaállítási pontok

Ez a funkció lehetővé teszi az adatraktár visszaállítási pontok létrehozása előtt és után a módosítások nagy manuális eseményindító pillanatképeket. Ez a funkció biztosítja, hogy a visszaállítási pontok logikailag konzisztens, amely további védelmet biztosít bármilyen számítási feladat megszakítások vagy felhasználói hibák esetén a gyors helyreállítási idő. Felhasználó által definiált visszaállítási pontok érhetők el hét napja, és automatikusan törlődnek az Ön nevében. A felhasználó által definiált visszaállítási pontok megőrzési időtartama nem módosítható. **felhasználó által definiált 42 visszaállítási pontok** garantáltan bármikor időben kell lenniük, [törölt](https://go.microsoft.com/fwlink/?linkid=875299) egy másik létrehozása előtt visszaállítási pont. A pillanatképek révén a felhasználó által definiált visszaállítási pontok létrehozása is beállíthat [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.sql/new-azurermsqldatabaserestorepoint?view=azurermps-6.2.0#examples) vagy az Azure Portalon.

> [!NOTE]
> Ha a 7 napnál hosszabb visszaállítási pontok van szüksége, adjon szavazzon ezt a képességet [Itt](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/35114410-user-defined-retention-periods-for-restore-points). Is hozzon létre egy felhasználói visszaállítási pont és az újonnan létrehozott visszaállításipont visszaállítása egy új data warehouse-bA. Miután visszaállította, az adatraktár online rendelkezik, és akár szüneteltetheti is, hogy határozatlan ideig számítási költségeit. A szüneteltetett adatbázis tárolási díjak pedig az Azure Premium Storage tekintetében. Ha egy aktív másolata, a visszaállított adatraktár van szüksége, folytathatja, amelynek csak néhány percet vehet igénybe.
>

### <a name="restore-point-retention"></a>Visszaállítási pont megőrzése

A következő listák adatokat a helyreállítási pont megőrzési időtartamú:

1. Az SQL Data Warehouse törli egy visszaállítási pontot, ha a 7 napos megőrzési idő elér **és** ha vannak, legalább 42 visszaállítási pontok összesen (beleértve a felhasználó által definiált és automatikus)
2. A pillanatképek a rendszer nem hajtja végre, ha az adattárház szüneteltetve van
3. Visszaállítási pont korát mérjük az idő a visszaállítási pont lesz végrehajtva, amikor az adattárház szüneteltetve van abszolút naptári napok
4. Bármely időpontra adattárház garantáltan képes akár 42 felhasználó által megadott helyreállítási pontokat tárolni, és amennyiben ezek a visszaállítási pontok 42 automatikus visszaállítási pontok nem éri el a 7 napos megőrzési időszak
5. Ha egy pillanatképet készít, az adattárház szüneteltetve van majd a 7 napnál, és ezután folytatja, lehetséges visszaállítási pont továbbra is fennáll, addig amíg 42 visszaállítási pontok összesen (beleértve a felhasználó által definiált és automatikus)

### <a name="snapshot-retention-when-a-data-warehouse-is-dropped"></a>Pillanatkép megőrzése, ha megszakad a data warehouse-bA

Amikor egy adattárházat, az SQL Data Warehouse végső pillanatképet készít, és menti a hét napja. A végleges visszaállítási pont törlése a létrehozott visszaállíthatja az adatraktárba.

> [!IMPORTANT]
> Ha töröl egy logikai SQL server-példányt, a példányhoz tartozó összes adatbázis is törlődik, és nem állítható helyre. Kiszolgáló törlése nem állítható vissza.
>

## <a name="geo-backups-and-disaster-recovery"></a>GEO-biztonsági mentések és a katasztrófa utáni helyreállítás

Az SQL Data Warehouse egy georedundáns biztonsági mentés naponta egyszer elvégzi a [párosított adatközpontba](../best-practices-availability-paired-regions.md). Az rpo-t, a georedundáns visszaállítás 24 órán keresztül. A georedundáns biztonsági mentési visszaállíthatja egy bármelyik más régióban, ahol támogatott az SQL Data Warehouse-kiszolgálóhoz. Georedundáns biztonsági biztosítja, visszaállíthatja a data warehouse-bA abban az esetben, ha a visszaállítási pontok az elsődleges régióban nem férhet hozzá.

GEO-biztonsági mentések alapállapotban be van kapcsolva. Ha az adattárház Gen1, is [kikapcsolhatja az újat](/powershell/module/azurerm.sql/set-azurermsqldatabasegeobackuppolicy) Ha szeretné. Nem tilthatók le geo-biztonsági mentések Gen2 a garantált beépített adatvédelem-jébe.

> [!NOTE]
> Földrajzi – biztonsági másolatok a rövidebb rpo miatt van szükség, ha ez a funkció szavazzon [Itt](https://feedback.azure.com/forums/307516-sql-data-warehouse). Hozhat létre egy felhasználói visszaállítási pontot, és az újonnan létrehozott visszaállítási pontból vissza egy új data warehouse egy másik régióban. Miután visszaállította, az adatraktár online rendelkezik, és akár szüneteltetheti is, hogy határozatlan ideig számítási költségeit. A szüneteltetett adatbázis tárolási díjak pedig az Azure Premium Storage tekintetében. Szüksége van egy aktív másolata, az adatraktárban, folytathatja, amelynek csak néhány percet vehet igénybe.
>

## <a name="backup-and-restore-costs"></a>Biztonsági mentés és visszaállítás költségek

Megfigyelheti az Azure-számlán egy egysoros tételt tárolás és a egy egysoros tételt vész-helyreállítási Storage. A tárolás díja a teljes költség az adatok tárolására és a növekményes változásokat rögzíti a pillanatképeket az elsődleges régióban. Hogyan számlázzuk a pillanatképek részletesebb ismertetése, tekintse meg [ismertetése, hogyan lépheti túl a pillanatképek díjak](https://docs.microsoft.com/rest/api/storageservices/Understanding-How-Snapshots-Accrue-Charges?redirectedfrom=MSDN#snapshot-billing-scenarios). A georedundáns díja fedezi a geo-biztonsági mentések tárolására költségeit.  

A teljes költségét az elsődleges adatraktár és a hét napig pillanatkép változások a legközelebbi egész TB lesz kerekítve. Például ha az adattárház 1,5 TB-os és a pillanatképek rögzíti a 100 GB-os, akkor számlázása 2 TB adat, az Azure Premium Storage szolgáltatás díjszabása.

Ha használ georedundáns tárolást, külön tárolási díjat jelenik meg. A georedundáns tárolás az írásvédett Georedundáns Társzolgáltatás (RA-GRS) alapdíjjal számoljuk fel.

További információ az SQL Data Warehouse díjszabása: [SQL Data Warehouse díjszabása](https://azure.microsoft.com/pricing/details/sql-data-warehouse/) és [kimenő forgalom díjak](https://azure.microsoft.com/pricing/details/bandwidth/) régiók közötti visszaállításakor.

## <a name="restoring-from-restore-points"></a>A visszaállítási pontok visszaállítása

Minden pillanatképet hoz létre egy visszaállítási pontot, amely a pillanatkép indításakor jelöli. Egy adatraktár helyreállításához válasszon visszaállítási pontot, és a restore parancsot.  

Megtarthatja a visszaállított adatraktár és az aktuális, vagy törölheti őket. Ha szeretné a jelenlegi adatraktárban cserélje le a visszaállított adatraktár, átnevezheti a [ALTER DATABASE (Azure SQL Data Warehouse)](/sql/t-sql/statements/alter-database-azure-sql-data-warehouse) a NEVÉNEK módosítása lehetőséggel.

Visszaállíthatja adatraktárát, lásd: [visszaállíthatja adatraktárát az Azure portal használatával](sql-data-warehouse-restore-database-portal.md), [visszaállíthatja adatraktárát PowerShell-lel](sql-data-warehouse-restore-database-powershell.md), vagy [visszaállíthatja adatraktárát használja a REST API-k](sql-data-warehouse-restore-database-rest-api.md).

Egy törölt vagy szüneteltetett data warehouse-adatbázis visszaállításához is [hozzon létre egy támogatási jegyet](sql-data-warehouse-get-started-create-support-ticket.md).

## <a name="geo-redundant-restore"></a>Georedundáns visszaállítás

Is [az adatraktár visszaállítása](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-restore-database-powershell#restore-from-an-azure-geographical-region) bármely régióba támogatása az SQL Data Warehouse a kiválasztott teljesítményi szinten.

> [!NOTE]
> A georedundáns visszaállítás végrehajtásához kell nem visszavonta a funkció.
>

## <a name="next-steps"></a>További lépések

Vészhelyreállítási tervezésével kapcsolatos további információkért lásd: [üzleti folytonosság – áttekintés](../sql-database/sql-database-business-continuity.md)
