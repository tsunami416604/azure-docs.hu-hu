---
title: Azure SQL Data Warehouse biztonsági mentés és visszaállítás – Pillanatképek, Geo-redundáns | Microsoft Docs
description: Ismerje meg, hogyan működik a biztonsági mentés és a visszaállítás a Azure SQL Data Warehouseban. Az adatraktár biztonsági másolatait használva állítsa vissza az adattárházat az elsődleges régió visszaállítási pontjára. A Geo-redundáns biztonsági mentések használatával egy másik földrajzi régióba állíthatja vissza a visszaállítást.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 04/30/2019
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 90544e182eb25f53232cee9a4dd0c05bd25508a3
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/13/2019
ms.locfileid: "68988470"
---
# <a name="backup-and-restore-in-azure-sql-data-warehouse"></a>Biztonsági mentés és visszaállítás Azure SQL Data Warehouse

Megtudhatja, hogyan használhatja a biztonsági mentést és a visszaállítást Azure SQL Data Warehouse. Az adatraktár-visszaállítási pontok használatával helyreállíthatja vagy átmásolhatja az adattárházat az elsődleges régió korábbi állapotára. Az adatraktár geo-redundáns biztonsági másolatai segítségével egy másik földrajzi régióba állíthatja vissza a visszaállítást.

## <a name="what-is-a-data-warehouse-snapshot"></a>Mi az az adatraktár-pillanatkép

Az *adatraktár-pillanatkép* egy visszaállítási pontot hoz létre, amellyel helyreállíthatja vagy átmásolhatja az adattárházat egy korábbi állapotba.  Mivel SQL Data Warehouse egy elosztott rendszer, az adatraktár-pillanatkép számos, az Azure Storage-ban található fájlból áll. A pillanatképek rögzítik az adattárházban tárolt adatok növekményes módosításait.

Az *adatraktár-visszaállítás* egy új adattárház, amely egy meglévő vagy törölt adattárház visszaállítási pontjából lett létrehozva. Az adattárház visszaállítása fontos része az üzletmenet folytonosságának és a vész-helyreállítási stratégiának, mivel a véletlen sérülés vagy törlés után újból létrehozza az adatait. Az adatraktár emellett hatékony mechanizmust biztosít az adattárház másolatának tesztelési vagy fejlesztési célokra történő létrehozásához.  SQL Data Warehouse visszaállítási díjak a forrás és a cél adatraktár adatbázisának méretétől és helyétől függően eltérőek lehetnek. Ugyanazon a régión belül az átlagos visszaállítási sebesség általában körülbelül 20 percet vesz igénybe. 

## <a name="automatic-restore-points"></a>Automatikus visszaállítási pontok

A pillanatképek a szolgáltatás beépített funkciója, amely visszaállítási pontokat hoz létre. Nem kell engedélyeznie ezt a funkciót. A felhasználók jelenleg nem törölhetnek Automatikus visszaállítási pontokat, ha a szolgáltatás ezeket a visszaállítási pontokat használja a SLA-nak a helyreállításhoz való fenntartásához.

SQL Data Warehouse a nap folyamán pillanatképeket készít az adattárházról a hét napig elérhető visszaállítási pontok létrehozásához. Ez a megőrzési időszak nem módosítható. SQL Data Warehouse támogatja a 8 órás helyreállítási időkorlátot (RPO). Az adattárházat az elsődleges régióban állíthatja vissza az elmúlt hét napban készített Pillanatképek közül.

Ha szeretné megtekinteni, hogy mikor indította el az utolsó pillanatképet, futtassa ezt a lekérdezést az online SQL Data Warehouse.

```sql
select   top 1 *
from     sys.pdw_loader_backup_runs
order by run_id desc
;
```

## <a name="user-defined-restore-points"></a>Felhasználó által definiált visszaállítási pontok

Ez a funkció lehetővé teszi a pillanatképek manuális indítását az adattárház visszaállítási pontjainak létrehozásához a nagy módosítások előtt és után. Ez a funkció biztosítja, hogy a visszaállítási pontok logikailag konzisztensek legyenek, ami további adatvédelmet biztosít a munkaterhelés-megszakítások vagy a gyors helyreállítási időpontra vonatkozó felhasználói hibák esetén. A felhasználó által definiált visszaállítási pontok hét napig érhetők el, és automatikusan törlődnek az Ön nevében. A felhasználó által definiált visszaállítási pontok megőrzési ideje nem módosítható. **42 a felhasználó által definiált visszaállítási pontok** bármely időpontban garantáltak, így azokat [törölni](https://go.microsoft.com/fwlink/?linkid=875299) kell egy másik visszaállítási pont létrehozása előtt. A pillanatképek elindításával felhasználó által definiált visszaállítási pontokat hozhat létre a [PowerShell](https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabaserestorepoint#examples) vagy a Azure Portal használatával.

> [!NOTE]
> Ha 7 napnál hosszabb visszaállítási pontra van szüksége, szavazzon erre a [](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/35114410-user-defined-retention-periods-for-restore-points)lehetőségre. A felhasználó által definiált visszaállítási pontot és a visszaállítást az újonnan létrehozott visszaállítási pontról egy új adattárházra is létrehozhatja. Miután visszaállította az adattárházat, a szolgáltatás online állapotba kerül, és határozatlan ideig szüneteltetheti a számítási költségek megtakarítását. A szüneteltetett adatbázis az Azure-Premium Storage díjszabása szerint terheli az adattárolási díjat. Ha a visszaállított adattárház aktív példányára van szüksége, akkor folytathatja, hogy a rendszer csak néhány percet vesz igénybe.

### <a name="restore-point-retention"></a>Visszaállítási pont megőrzése

A következő felsorolás a visszaállítási pontok megőrzési időszakait ismerteti:

1. SQL Data Warehouse törli a visszaállítási pontot, amikor eléri a 7 napos megőrzési időszakot **, és** ha legalább 42 teljes visszaállítási pont van (beleértve a felhasználó által definiált és az Automatikust is)
2. Az adatraktár szüneteltetése esetén a rendszer nem végez pillanatképeket
3. A visszaállítási pontok korát a visszaállítási pont elkészítésekor megjelenő, az adatraktár szüneteltetése után meghozott teljes naptári nap alapján méri a rendszer.
4. Egy adott időpontban az adattárházak garantáltan legfeljebb 42 felhasználó által definiált visszaállítási pontot és 42 Automatikus visszaállítási pontot tárolhatnak, amennyiben ezek a visszaállítási pontok nem értek el a 7 napos adatmegőrzési időszakot
5. Ha pillanatképet készít, az adattárházat a rendszer 7 napnál hosszabb ideig szünetelteti, majd folytatja, a visszaállítási pont mindaddig fennmarad, amíg 42 teljes visszaállítási pont (beleértve a felhasználó által definiált és az automatikus frissítést is)

### <a name="snapshot-retention-when-a-data-warehouse-is-dropped"></a>Pillanatkép-megőrzés adatraktár eldobásakor

Az adattárházak eldobásakor a SQL Data Warehouse létrehozza a végső pillanatképet, és hét napig elmenti. Az adatraktárat visszaállíthatja a törléskor létrehozott utolsó visszaállítási pontra.

> [!IMPORTANT]
> Ha töröl egy logikai SQL Server-példányt, a példányhoz tartozó összes adatbázis is törlődik, és nem állítható helyre. Törölt kiszolgálót nem lehet visszaállítani.

## <a name="geo-backups-and-disaster-recovery"></a>Geo – biztonsági mentések és vész-helyreállítás

SQL Data Warehouse naponta egyszer végez el geo-biztonsági mentést egy [párosított](../best-practices-availability-paired-regions.md)adatközpontba. A Geo-visszaállítás RPO 24 óra. Visszaállíthatja a Geo-biztonsági mentést bármely más régióban található kiszolgálóra, ahol a SQL Data Warehouse támogatott. A földrajzi biztonsági mentés lehetővé teszi az adatraktár visszaállítását abban az esetben, ha az elsődleges régióban nem fér hozzá a visszaállítási pontokhoz.

A Geo-biztonsági mentések alapértelmezés szerint be vannak kapcsolva. Ha az adattárháza Gen1, kiválaszthatja, hogy kívánja-e. [](/powershell/module/az.sql/set-azsqldatabasegeobackuppolicy) Nem lehet letiltani a Gen2 biztonsági mentését, mert az adatvédelem beépített garantált.

> [!NOTE]
> Ha rövidebb RPO van szüksége a földrajzi biztonsági mentéshez, akkor [itt](https://feedback.azure.com/forums/307516-sql-data-warehouse)is szavazzon erre a képességre. A felhasználó által definiált visszaállítási pontot és a visszaállítást az újonnan létrehozott visszaállítási pontról egy másik régióban lévő új adattárházra is létrehozhatja. Miután visszaállította az adattárházat, a szolgáltatás online állapotba kerül, és határozatlan ideig szüneteltetheti a számítási költségek megtakarítását. A szüneteltetett adatbázis az Azure-Premium Storage díjszabása szerint terheli az adattárolási díjat. Ha az adatraktár aktív példányára van szüksége, folytathatja a folytatást, amely mindössze néhány percet vesz igénybe.

## <a name="backup-and-restore-costs"></a>Biztonsági mentési és visszaállítási költségek

Megfigyelheti, hogy az Azure-számla tartalmaz egy sort a Storage szolgáltatáshoz, valamint egy sor tételt a vész-helyreállítási tároláshoz. A tárolási díj az elsődleges régióban tárolt adattárolás teljes költsége, valamint a pillanatképek által rögzített növekményes változások. A pillanatképek felszámolásának részletes ismertetését lásd: a pillanatképek felmerülésének [ismertetése](https://docs.microsoft.com/rest/api/storageservices/Understanding-How-Snapshots-Accrue-Charges?redirectedfrom=MSDN#snapshot-billing-scenarios). A Geo-redundáns díj magában foglalja a Geo-biztonsági mentések tárolásának költségeit.  

Az elsődleges adattárház teljes díja és a pillanatképek változásának hét napja a legközelebbi TB-ra lesz kerekítve. Ha például az adattárház 1,5 TB-os, és a pillanatképek a 100 GB-ot rögzítik, akkor az Azure Premium Storage díjszabása 2 TB-os adatforgalmi díjat számítunk fel.

Ha földrajzilag redundáns tárolást használ, külön tárolási díjat kap. A Geo-redundáns tárolót a standard olvasási hozzáférésű, földrajzilag redundáns tárolás (RA-GRS) díjszabás alapján számoljuk el.

További információ a SQL Data Warehouse díjszabásáról: [SQL Data Warehouse díjszabás]. A régiókban való visszaállításkor nem számítunk fel díjat a kimenő adatforgalomért.

## <a name="restoring-from-restore-points"></a>Visszaállítás visszaállítási pontokból

Mindegyik pillanatkép egy visszaállítási pontot hoz létre, amely a pillanatkép elindításának időpontját jelöli. Az adatraktár visszaállításához válasszon egy visszaállítási pontot, és adja meg a visszaállítási parancsot.  

Megtarthatja a visszaállított adattárházat és az aktuálisat, illetve törölheti is az egyiket. Ha az aktuális adattárházat a visszaállított adattárházra szeretné cserélni, a MÓDOSÍTÁSi [adatbázis (Azure SQL Data Warehouse)](/sql/t-sql/statements/alter-database-azure-sql-data-warehouse) használatával átnevezheti azt a név módosítása lehetőséggel.

Az adatraktár visszaállításával kapcsolatban lásd: [adatraktár visszaállítása a Azure Portal használatával, az](sql-data-warehouse-restore-database-portal.md)adattárház visszaállítása a [PowerShell használatával](sql-data-warehouse-restore-database-powershell.md), illetve az adattárház visszaállítása [REST API](sql-data-warehouse-restore-database-rest-api.md)-kkal.

A törölt vagy szüneteltetett adattárházak visszaállításához [létrehozhat egy támogatási jegyet](sql-data-warehouse-get-started-create-support-ticket.md).

## <a name="cross-subscription-restore"></a>Több előfizetés visszaállítása

Ha közvetlenül kell visszaállítani az előfizetést, szavazzon erre a [](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/36256231-enable-support-for-cross-subscription-restore)képességre. [Térjen](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources) vissza egy másik logikai kiszolgálóra, és a kiszolgáló áthelyezi a kiszolgálót az előfizetések között a több előfizetés visszaállításának végrehajtásához. 

## <a name="geo-redundant-restore"></a>Geo-redundáns visszaállítás

Az adattárházat bármely olyan régióra visszaállíthatja, amely a kiválasztott teljesítményszint SQL Data Warehouse támogatja. [](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-restore-from-geo-backup#restore-from-an-azure-geographical-region-through-powershell)

> [!NOTE]
> A Geo-redundáns visszaállítás végrehajtásához nem kell kijelölnie ezt a funkciót.

## <a name="next-steps"></a>További lépések

További információ a katasztrófák tervezéséről: az [üzletmenet folytonosságának áttekintése](../sql-database/sql-database-business-continuity.md)
