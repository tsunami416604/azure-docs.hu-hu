---
title: Biztonsági mentés és visszaállítás – Pillanatképek, Geo-redundáns
description: Ismerje meg, hogyan működik a biztonsági mentés és visszaállítás az Azure szinapszis Analytics SQL-készletben. A biztonsági másolatok használatával visszaállíthatja az adattárházat az elsődleges régióban található visszaállítási pontra. A Geo-redundáns biztonsági mentések használatával egy másik földrajzi régióba állíthatja vissza a visszaállítást.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 03/04/2020
ms.author: anjangsh
ms.reviewer: igorstan
ms.custom: seo-lt-2019"
ms.openlocfilehash: d4a08035b03c104555c39311bfb812218cca44b1
ms.sourcegitcommit: 1d9f7368fa3dadedcc133e175e5a4ede003a8413
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2020
ms.locfileid: "85482547"
---
# <a name="backup-and-restore-in-azure-synapse-sql-pool"></a>Biztonsági mentés és visszaállítás az Azure szinapszis SQL-készletben

Ismerje meg, hogyan használhatja a biztonsági mentést és a visszaállítást az Azure szinapszis SQL-készletben. Az SQL Pool visszaállítási pontjaival helyreállíthatja vagy átmásolhatja az adattárházat az elsődleges régió korábbi állapotába. Az adatraktár geo-redundáns biztonsági másolatai segítségével egy másik földrajzi régióba állíthatja vissza a visszaállítást.

## <a name="what-is-a-data-warehouse-snapshot"></a>Mi az az adatraktár-pillanatkép

Az *adatraktár-pillanatkép* egy visszaállítási pontot hoz létre, amellyel helyreállíthatja vagy átmásolhatja az adattárházat egy korábbi állapotba.  Mivel az SQL-készlet egy elosztott rendszer, az adatraktár-pillanatkép számos, az Azure Storage-ban található fájlból áll. A pillanatképek rögzítik az adattárházban tárolt adatok növekményes módosításait.

Az *adatraktár-visszaállítás* egy új adattárház, amely egy meglévő vagy törölt adattárház visszaállítási pontjából lett létrehozva. Az adattárház visszaállítása fontos része az üzletmenet folytonosságának és a vész-helyreállítási stratégiának, mivel a véletlen sérülés vagy törlés után újból létrehozza az adatait. Az adatraktár emellett hatékony mechanizmust biztosít az adattárház másolatának tesztelési vagy fejlesztési célokra történő létrehozásához.  Az SQL-készlet visszaállítási díjai a forrás és a cél adatraktár adatbázisának méretétől és helyétől függően változhatnak.

## <a name="automatic-restore-points"></a>Automatikus visszaállítási pontok

A pillanatképek egy beépített szolgáltatás, amely visszaállítási pontokat hoz létre. Nem kell engedélyeznie ezt a funkciót. Az SQL-készletnek azonban aktív állapotban kell lennie a visszaállítási pont létrehozásához. Ha az SQL-készletet gyakran szüneteltetik, előfordulhat, hogy az Automatikus visszaállítási pontok nem hozhatók létre, ezért mindenképpen hozzon létre felhasználó által definiált visszaállítási pontot az SQL-készlet szüneteltetése előtt. A felhasználók jelenleg nem törölhetik az Automatikus visszaállítási pontokat, mivel a szolgáltatás ezeket a visszaállítási pontokat használja a SLA-nak a helyreállításhoz való fenntartásához.

Az adattárház pillanatképeit a rendszer a nap folyamán hozza létre a hét napig elérhető visszaállítási pontok létrehozásához. Ez a megőrzési időszak nem módosítható. Az SQL-készlet támogatja a 8 órás helyreállítási időkorlátot (RPO). Az adattárházat az elsődleges régióban állíthatja vissza az elmúlt hét napban készített Pillanatképek közül.

Ha meg szeretné tekinteni, hogy mikor indította el az utolsó pillanatképet, futtassa ezt a lekérdezést az online SQL-készleten.

```sql
select   top 1 *
from     sys.pdw_loader_backup_runs
order by run_id desc
;
```

## <a name="user-defined-restore-points"></a>Felhasználó által definiált visszaállítási pontok

Ez a funkció lehetővé teszi a pillanatképek manuális indítását az adattárház visszaállítási pontjainak létrehozásához a nagy módosítások előtt és után. Ez a funkció biztosítja, hogy a visszaállítási pontok logikailag konzisztensek legyenek, ami további adatvédelmet biztosít a munkaterhelés-megszakítások vagy a gyors helyreállítási időpontra vonatkozó felhasználói hibák esetén. A felhasználó által definiált visszaállítási pontok hét napig érhetők el, és automatikusan törlődnek az Ön nevében. A felhasználó által definiált visszaállítási pontok megőrzési ideje nem módosítható. **42 a felhasználó által definiált visszaállítási pontok** bármely időpontban garantáltak, így azokat [törölni](https://go.microsoft.com/fwlink/?linkid=875299) kell egy másik visszaállítási pont létrehozása előtt. A pillanatképek elindításával felhasználó által definiált visszaállítási pontokat hozhat létre a [PowerShell](/powershell/module/az.sql/new-azsqldatabaserestorepoint?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.jsont#examples) vagy a Azure Portal használatával.

> [!NOTE]
> Ha 7 napnál hosszabb visszaállítási pontra van szüksége, szavazzon erre a [lehetőségre](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/35114410-user-defined-retention-periods-for-restore-points). A felhasználó által definiált visszaállítási pontot és a visszaállítást az újonnan létrehozott visszaállítási pontról egy új adattárházra is létrehozhatja. Miután visszaállította, az SQL-készlet online állapotba kerül, és határozatlan ideig szüneteltetheti a számítási költségek megtakarítását. A szüneteltetett adatbázis az Azure-Premium Storage díjszabása szerint terheli az adattárolási díjat. Ha a visszaállított adattárház aktív példányára van szüksége, akkor folytathatja, hogy a rendszer csak néhány percet vesz igénybe.

### <a name="restore-point-retention"></a>Visszaállítási pont megőrzése

A következő felsorolás a visszaállítási pontok megőrzési időszakait ismerteti:

1. Az SQL-készlet törli a visszaállítási pontot, amikor eléri a 7 napos megőrzési időszakot **, és** ha legalább 42 teljes visszaállítási pont van (beleértve a felhasználó által definiált és az Automatikust is).
2. A pillanatképek nem lesznek elvégezve, amikor egy SQL-készlet szüneteltetve van.
3. A visszaállítási pontok korát a visszaállítási ponttól számított abszolút naptári napok határozzák meg, beleértve az SQL-készlet szüneteltetését.
4. Egy SQL-készlet bármikor garantálható, hogy legfeljebb 42 felhasználó által definiált visszaállítási pontot és 42 Automatikus visszaállítási pontot tud tárolni, amennyiben ezek a visszaállítási pontok nem értek el a 7 napos adatmegőrzési időszakot.
5. Ha pillanatképet készít, az SQL-készletet a rendszer 7 napnál hosszabb ideig szünetelteti, majd folytatja, a visszaállítási pont mindaddig megmarad, amíg 42 teljes visszaállítási pont (beleértve a felhasználó által definiált és az automatikus szolgáltatást is)

### <a name="snapshot-retention-when-a-sql-pool-is-dropped"></a>Pillanatkép-megőrzés egy SQL-készlet eldobásakor

Egy SQL-készlet eldobásakor a rendszer a végleges pillanatképet hozza létre és hét napig menti. Az SQL-készletet visszaállíthatja a törléskor létrehozott utolsó visszaállítási pontra. Ha az SQL-készlet szüneteltetve állapotban van, a rendszer nem készít pillanatképet. Ebben az esetben ügyeljen arra, hogy egy felhasználó által definiált visszaállítási pontot hozzon létre az SQL-készlet eldobása előtt.

> [!IMPORTANT]
> Ha törli az SQL-készletet futtató kiszolgálót, a kiszolgálóhoz tartozó összes adatbázis is törlődik, és nem állítható helyre. Törölt kiszolgálót nem lehet visszaállítani.

## <a name="geo-backups-and-disaster-recovery"></a>Geo – biztonsági mentések és vész-helyreállítás

A rendszer naponta egyszer hozza létre a Geo-biztonsági mentést egy [párosított adatközpontba](../../best-practices-availability-paired-regions.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). A Geo-visszaállítás RPO 24 óra. Visszaállíthatja a Geo-biztonsági mentést bármely más régióban található kiszolgálóra, ahol az SQL-készlet támogatott. A földrajzi biztonsági mentés lehetővé teszi az adatraktár visszaállítását abban az esetben, ha az elsődleges régióban nem fér hozzá a visszaállítási pontokhoz.

> [!NOTE]
> Ha rövidebb RPO van szüksége a földrajzi biztonsági mentéshez, akkor [itt](https://feedback.azure.com/forums/307516-sql-data-warehouse)is szavazzon erre a képességre. A felhasználó által definiált visszaállítási pontot és a visszaállítást az újonnan létrehozott visszaállítási pontról egy másik régióban lévő új adattárházra is létrehozhatja. Miután visszaállította az adattárházat, a szolgáltatás online állapotba kerül, és határozatlan ideig szüneteltetheti a számítási költségek megtakarítását. A szüneteltetett adatbázis az Azure-Premium Storage díjszabása szerint terheli az adattárolási díjat. Ha az adatraktár aktív példányára van szüksége, folytathatja a folytatást, amely mindössze néhány percet vesz igénybe.

## <a name="backup-and-restore-costs"></a>Biztonsági mentési és visszaállítási költségek

Megfigyelheti, hogy az Azure-számla tartalmaz egy sort a Storage szolgáltatáshoz, valamint egy sor tételt a vész-helyreállítási tároláshoz. A tárolási díj az elsődleges régióban tárolt adattárolás teljes költsége, valamint a pillanatképek által rögzített növekményes változások. A pillanatképek felszámolásának részletes ismertetését lásd: a [Pillanatképek felmerülésének ismertetése](/rest/api/storageservices/Understanding-How-Snapshots-Accrue-Charges?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). A Geo-redundáns díj magában foglalja a Geo-biztonsági mentések tárolásának költségeit.  

Az elsődleges adattárház teljes díja és a pillanatképek változásának hét napja a legközelebbi TB-ra lesz kerekítve. Ha például az adattárház 1,5 TB-os, és a pillanatképek a 100 GB-ot rögzítik, akkor az Azure Premium Storage díjszabása 2 TB-os adatforgalmi díjat számítunk fel.

Ha földrajzilag redundáns tárolást használ, külön tárolási díjat kap. A Geo-redundáns tárolót a standard olvasási hozzáférésű, földrajzilag redundáns tárolás (RA-GRS) díjszabás alapján számoljuk el.

Az Azure szinapszis díjszabásával kapcsolatos további információkért lásd: az [Azure szinapszis díjszabása](https://azure.microsoft.com/pricing/details/sql-data-warehouse/gen2/). A régiókban való visszaállításkor nem számítunk fel díjat a kimenő adatforgalomért.

## <a name="restoring-from-restore-points"></a>Visszaállítás visszaállítási pontokból

Mindegyik pillanatkép egy visszaállítási pontot hoz létre, amely a pillanatkép elindításának időpontját jelöli. Az adatraktár visszaállításához válasszon egy visszaállítási pontot, és adja meg a visszaállítási parancsot.  

Megtarthatja a visszaállított adattárházat és az aktuálisat, illetve törölheti is az egyiket. Ha az aktuális adattárházat a visszaállított adattárházra szeretné cserélni, a MÓDOSÍTÁSi [adatbázis (SQL-készlet)](/sql/t-sql/statements/alter-database-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) használatával átnevezheti azt a név módosítása lehetőséggel.

Az adatraktár visszaállításáról lásd: [SQL-készlet visszaállítása](sql-data-warehouse-restore-points.md#create-user-defined-restore-points-through-the-azure-portal).

A törölt vagy szüneteltetett adattárházak visszaállításához [létrehozhat egy támogatási jegyet](sql-data-warehouse-get-started-create-support-ticket.md).

## <a name="cross-subscription-restore"></a>Több előfizetés visszaállítása

Ha közvetlenül kell visszaállítani az előfizetést, szavazzon erre a [képességre](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/36256231-enable-support-for-cross-subscription-restore). Állítsa vissza egy másik kiszolgálóra, és [Helyezze](../../azure-resource-manager/management/move-resource-group-and-subscription.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) át a kiszolgálót az előfizetések között a több előfizetés visszaállításának végrehajtásához.

## <a name="geo-redundant-restore"></a>Geo-redundáns visszaállítás

Az SQL-készletet bármely olyan régióra [visszaállíthatja](sql-data-warehouse-restore-from-geo-backup.md#restore-from-an-azure-geographical-region-through-powershell) , amely támogatja az SQL-készletet az Ön által választott teljesítményszint esetén.

> [!NOTE]
> A Geo-redundáns visszaállítás végrehajtásához nem kell kijelölnie ezt a funkciót.

## <a name="next-steps"></a>További lépések

A visszaállítási pontokkal kapcsolatos további információkért lásd: [felhasználó által definiált visszaállítási pontok](sql-data-warehouse-restore-points.md)