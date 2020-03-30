---
title: Biztonsági mentés és visszaállítás - pillanatképek, georedundáns
description: Ismerje meg, hogyan működik a biztonsági mentés és a visszaállítás az Azure Synapse Analytics SQL-készletben. A biztonsági mentések segítségével visszaállíthatja az adatraktárt az elsődleges régió egy visszaállítási pontjára. Georedundáns biztonsági mentések segítségével visszaállíthatja egy másik földrajzi régióba.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 03/04/2020
ms.author: anjangsh
ms.reviewer: igorstan
ms.custom: seo-lt-2019"
ms.openlocfilehash: ae53380572e753a8bcfa20fcd165fa015766263e
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80349270"
---
# <a name="backup-and-restore-in-azure-synapse-sql-pool"></a>Biztonsági mentés és visszaállítás az Azure Synapse SQL-készletében

Ismerje meg, hogyan használhatja a biztonsági mentést és visszaállítást az Azure Synapse SQL-készletben. Az SQL-készlet-visszaállítási pontok segítségével helyreállíthatja vagy másolhatja az adatraktárt az elsődleges régió egy korábbi állapotába. Az adattárház georedundáns biztonsági mentéseisegítségével visszaállíthatja egy másik földrajzi régióba.

## <a name="what-is-a-data-warehouse-snapshot"></a>Mi az adattárház pillanatképe?

Az *adattárház pillanatképe* létrehoz egy visszaállítási pontot, amelyet az adattárház egy korábbi állapotba való másolásához használhat.  Mivel az SQL-készlet egy elosztott rendszer, az adattárház pillanatképe számos, az Azure storage-ban található fájlból áll. A pillanatképek növekményes változásokat rögzítnek az adatraktárban tárolt adatokból.

Az *adattárház-visszaállítás* egy új adattárház, amely egy meglévő vagy törölt adattárház visszaállítási pontjáról jön létre. Az adattárház visszaállítása minden üzletmenet-folytonossági és vész-helyreállítási stratégia alapvető része, mivel véletlen sérülés vagy törlés után újra létrehozza az adatokat. Az adattárház hatékony mechanizmus az adattárház másolásának létrehozásához tesztelési vagy fejlesztési célokra.  Az SQL-készlet visszaállítási díjai az adatbázis méretétől és a forrás- és céladattár-tárház helyétől függően változhatnak. 

## <a name="automatic-restore-points"></a>Automatikus visszaállítási pontok

A pillanatképek a szolgáltatás beépített szolgáltatása, amely visszaállítási pontokat hoz létre. Ezt a funkciót nem kell engedélyeznie. Az SQL-készletnek azonban aktív állapotban kell lennie a visszaállítási pont létrehozásához. Ha az SQL-készlet gyakran szünetel, előfordulhat, hogy nem jönnek létre automatikus visszaállítási pontok, ezért az SQL-készlet szüneteltetése előtt hozzon létre felhasználó által definiált visszaállítási pontot. Az automatikus visszaállítási pontokat jelenleg a felhasználók nem törölhetik, mivel a szolgáltatás ezeket a visszaállítási pontokat használja az SLO-k helyreállításához.

Az adattárház pillanatképei a nap folyamán a hét napig elérhető visszaállítási pontok létrehozása során készülnek. Ez a megőrzési időszak nem módosítható. Az SQL-készlet támogatja a nyolc órás helyreállítási pont célkitűzést (RPO). Az adattárház az elsődleges régióban az elmúlt hét nap bármelyik pillanatképéből visszaállíthatja.

Ha meg szeretné tekinteni, hogy mikor kezdődött el az utolsó pillanatkép, futtassa ezt a lekérdezést az online SQL-készleten.

```sql
select   top 1 *
from     sys.pdw_loader_backup_runs
order by run_id desc
;
```

## <a name="user-defined-restore-points"></a>Felhasználó által definiált visszaállítási pontok

Ez a funkció lehetővé teszi, hogy manuálisan indítsa el a pillanatképeket az adattárház visszaállítási pontjainak nagy módosítások előtti és utáni létrehozásához. Ez a funkció biztosítja, hogy a visszaállítási pontok logikailag konzisztensek legyenek, ami további adatvédelmet biztosít a munkaterhelés megszakítása vagy a felhasználói hibák esetén a gyors helyreállítási idő érdekében. A felhasználó által definiált visszaállítási pontok hét napig érhetők el, és automatikusan törlődnek az Ön nevében. A felhasználó által definiált visszaállítási pontok megőrzési időszaka nem módosítható. **42 felhasználó által definiált visszaállítási pont** garantált bármely időpontban, ezért [törölni](https://go.microsoft.com/fwlink/?linkid=875299) kell őket, mielőtt létrehozna egy másik visszaállítási pontot. Pillanatképek et indíthat el a felhasználó által definiált visszaállítási pontok létrehozásához a [PowerShellen](https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabaserestorepoint#examples) vagy az Azure Portalon keresztül.

> [!NOTE]
> Ha 7 napnál hosszabb visszaállítási pontra van szüksége, kérjük, szavazzon erre a képességre [itt](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/35114410-user-defined-retention-periods-for-restore-points). Létrehozhat egy felhasználó által definiált visszaállítási pontot is, és visszaállíthatja az újonnan létrehozott visszaállítási pontot egy új adattárházba. Miután visszaállította, az SQL-készlet online állapotban van, és határozatlan időre szüneteltetheti azt a számítási költségek megtakarítása érdekében. A szüneteltetett adatbázis tárolási díjakat számít fel az Azure Premium Storage díja mellett. Ha a visszaállított adattárház aktív másolatára van szüksége, folytathatja, amely csak néhány percet vesz igénybe.

### <a name="restore-point-retention"></a>Pontmegőrzés visszaállítása

Az alábbi lista a visszaállítási pontok megőrzési időszakait sorolja fel:

1. Az SQL-készlet törli a visszaállítási pontot, amikor eléri a 7 napos megőrzési időszakot, **és** ha legalább 42 teljes visszaállítási pont van (beleértve a felhasználó által definiált és automatikus pontokat is).
2. A pillanatképek nem készülnek el, ha egy SQL-készlet szünetel.
3. A visszaállítási pont korát a visszaállítási pont elkészültétől származó abszolút naptári napok mérik, beleértve az SQL-készlet szüneteltetésének módját is.
4. Bármikor, egy SQL készlet garantáltan képes tárolni legfeljebb 42 felhasználó által definiált visszaállítási pontok és 42 automatikus visszaállítási pontok, amíg ezek a visszaállítási pontok nem érték el a 7 napos megőrzési időszak
5. Ha egy pillanatkép készül, az SQL-készlet szünetel, majd szünetel, majd folytatódik, a visszaállítási pont megmarad, amíg nincs 42 teljes visszaállítási pont (beleértve a felhasználó által definiált és automatikus)

### <a name="snapshot-retention-when-a-sql-pool-is-dropped"></a>Pillanatkép-megőrzés SQL-készlet eldobásakor

Sql-készlet eldobásakor létrejön egy végső pillanatkép, amely hét napig mentésre kerül. Visszaállíthatja az SQL-készletet a törléskor létrehozott végső visszaállítási pontra. Ha az SQL-készlet szüneteltetésre kerül, nem történik pillanatkép. Ebben az esetben győződjön meg arról, hogy hozzon létre egy felhasználó által definiált visszaállítási pontot, mielőtt eldobna az SQL-készletet.

> [!IMPORTANT]
> Ha töröl egy logikai SQL-kiszolgálópéldányt, a példányhoz tartozó összes adatbázis is törlődik, és nem állítható helyre. Törölt kiszolgáló nem állítható vissza.

## <a name="geo-backups-and-disaster-recovery"></a>Geo-biztonsági mentések és vészhelyreállítás

A rendszer naponta egyszer létrehoz egy geobiztonsági mentést egy [párosított adatközpontba.](../../best-practices-availability-paired-regions.md) A geo-visszaállításra vonatkozó RPO 24 óra. A geobiztonsági mentés bármely más régióban, ahol az SQL-készlet támogatott, visszaállíthatja a geobiztonsági mentést. A geo-backup biztosítja az adattárház visszaállítását abban az esetben, ha nem tudja elérni az elsődleges régió visszaállítási pontjait.

> [!NOTE]
> Ha rövidebb RPO-ra van szüksége a geo-backups, szavazz erre a képességre [itt](https://feedback.azure.com/forums/307516-sql-data-warehouse). Létrehozhat egy felhasználó által definiált visszaállítási pontot is, és visszaállíthatja az újonnan létrehozott visszaállítási pontot egy másik régió új adattárházára. Miután visszaállította, online állapotban van az adattárház, és határozatlan időre szüneteltetheti azt a számítási költségek megtakarítása érdekében. A szüneteltetett adatbázis tárolási díjakat számít fel az Azure Premium Storage díja mellett. Ha az adattárház aktív másolatára van szüksége, folytathatja, amely csak néhány percet vesz igénybe.

## <a name="backup-and-restore-costs"></a>Biztonsági mentés és visszaállítás költségei

Észre fogja venni, hogy az Azure-számla rendelkezik egy sortárral a storage és egy sor a vész-helyreállítási storage.You will notice the Azure bill has a line item for Storage and a line item for Disaster Recovery Storage. A tárolási díj az adatok elsődleges régióban való tárolásának teljes költsége, valamint a pillanatképek által rögzített növekményes módosítások. A pillanatképek terhelésének részletesebb magyarázatát a [Pillanatképek elhatárolási díjainak ismertetése](https://docs.microsoft.com/rest/api/storageservices/Understanding-How-Snapshots-Accrue-Charges?redirectedfrom=MSDN#snapshot-billing-scenarios)című témakörben olvashatja el. A georedundáns díj fedezi a geo-biztonsági mentések tárolásának költségeit.  

Az elsődleges adattárház teljes költsége és a pillanatkép hét napos módosítása a legközelebbi TB-re lesz kerekítve. Ha például az adattárház 1,5 TB-os, és a pillanatképek 100 GB-ot rögzítenek, 2 TB-os adatdíjat számlázunk az Azure Premium Storage díjszabása szerint.

Ha georedundáns tárolást használ, külön tárolási díjat kap. A georedundáns tárolás számlázása a szabványos olvasási földrajzilag redundáns tárolási (RA-GRS) díj.

Az Azure Synapse díjszabásáról az [Azure Synapse díjszabása](https://azure.microsoft.com/pricing/details/sql-data-warehouse/gen2/)című témakörben talál további információt. A régiók közötti visszaállításkor nem kell fizetnie az adatforgalomért.

## <a name="restoring-from-restore-points"></a>Visszaállítás visszaállítási pontokról

Minden pillanatkép létrehoz egy visszaállítási pontot, amely a pillanatkép indításának idejét jelöli. Az adattárház visszaállításához válasszon egy visszaállítási pontot, és adjon ki egy visszaállítási parancsot.  

Megtarthatja a visszaállított adatraktárt és az aktuálisat, vagy törölheti az egyiket. Ha az aktuális adatraktárt a visszaállított adattárházra szeretné cserélni, átnevezheti azt [az ALTER DATABASE (SQL pool)](/sql/t-sql/statements/alter-database-azure-sql-data-warehouse) használatával a NÉV MÓDOSÍTÁSA beállítással.

Adattárház visszaállításáról az [SQL-készlet visszaállítása](sql-data-warehouse-restore-points.md#create-user-defined-restore-points-through-the-azure-portal)című témakörben található.

Törölt vagy szüneteltetett adattárház visszaállításához [hozzon létre egy támogatási jegyet.](sql-data-warehouse-get-started-create-support-ticket.md)

## <a name="cross-subscription-restore"></a>Előfizetések közötti visszaállítás

Ha közvetlenül vissza kell állítania az [here](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/36256231-enable-support-for-cross-subscription-restore)előfizetések között, itt szavazzon erre a funkcióra. Visszaállítás egy másik logikai kiszolgálóra, és ["Helyezze át"](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources) a kiszolgálót az előfizetések között, hogy keresztelőfizetés-visszaállítást hajtson végre. 

## <a name="geo-redundant-restore"></a>Georedundáns visszaállítás

Az [SQL-készlet bármely](sql-data-warehouse-restore-from-geo-backup.md#restore-from-an-azure-geographical-region-through-powershell) régióban támogatja az SQL-készlet a kiválasztott teljesítményszinten.

> [!NOTE]
> Georedundáns visszaállítás végrehajtásához nem szabad kilépnie ebből a funkcióból.

## <a name="next-steps"></a>További lépések

A katasztrófatervezéssel kapcsolatos további információkért lásd: [Üzletmenet-folytonossági áttekintés](../../sql-database/sql-database-business-continuity.md)
