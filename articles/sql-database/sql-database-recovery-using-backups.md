---
title: Azure SQL-adatbázis visszaállítása biztonsági másolatból |} Microsoft Docs
description: További információk a pont időponthoz kötött visszaállítás, amely lehetővé teszi, hogy visszaállítása egy Azure SQL-adatbázis egy korábbi időpontra idő (akár 35 nap).
services: sql-database
author: anosov1960
manager: craigg
ms.service: sql-database
ms.custom: business continuity
ms.topic: conceptual
ms.date: 04/04/2018
ms.author: sashan
ms.reviewer: carlrab
ms.openlocfilehash: 027a10e687673bdeedf2858b4c23ff459df61b70
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34649108"
---
# <a name="recover-an-azure-sql-database-using-automated-database-backups"></a>Automatikus adatbázis biztonsági mentését használó Azure SQL-adatbázis helyreállítása
SQL-adatbázis biztosítja ezeket a beállításokat, az adatbázis helyreállítási használatával [adatbázis biztonsági másolatait automatikus](sql-database-automated-backups.md) és [hosszú távú megőrzési a biztonsági másolatok](sql-database-long-term-retention.md). Egy adatbázis biztonsági másolatát arra állíthatja vissza:

* Egy új adatbázist ugyanazon a kiszolgálón logikai helyreállítani egy adott pontra a megőrzési időn belül. 
* Az azonos logikai kiszolgáló, a törlés időpontjával, a törölt adatbázisok a helyreállított adatbázis.
* Új adatbázis bármely régióban, a legutóbbi napi biztonsági mentések georeplikált blob Storage (RA-GRS) pont helyre egyetlen logikai kiszolgálón.

> [!IMPORTANT]
> Meglévő adatbázis visszaállítása során nem írható felül.
>

A visszaállított adatbázis költséget egy – megnövelt tárhely áll, az alábbi feltételek: 
- Ha az adatbázis maximális méretét 500 GB-nál nagyobb helyreállítása P11 – P15 S4-S12 vagy P1 – P6.
- Ha az adatbázis maximális mérete 250 GB-nál nagyobb helyreállítása P1 – P6 S4-S12 számára.

A felesleges költségek az az oka a visszaállított adatbázis maximális mérete nagyobb, mint a tárolókapacitást a teljesítményszintet része, és bármely fent a belefoglalt összeg kiépített – megnövelt tárhely extra fel van töltve.  Megnövelt tárhely díjszabása, tekintse meg a [árképzést ismertető oldalra SQL-adatbázis](https://azure.microsoft.com/pricing/details/sql-database/).  Ha a tényleges használt lemezterület mérete kisebb, mint a tároló tartalmazza, majd ez kapcsolódik további költség elkerülhető az adatbázis maximális méretét csökkentve a belefoglalt legkevesebb. Adatbázis tárterületet, és az adatbázis maximális méretének módosítása kapcsolatos további információkért lásd: [egyetlen adatbázis DTU-alapú erőforrás korlátok](sql-database-dtu-resource-limits.md#single-database-storage-sizes-and-performance-levels) és [egyetlen adatbázis vCore-alapú erőforrás korlátok](sql-database-vcore-resource-limits.md#single-database-storage-sizes-and-performance-levels).  

> [!NOTE]
> [Adatbázis biztonsági másolatait automatikus](sql-database-automated-backups.md) használják, amikor létrehoz egy [adatbázis másolása](sql-database-copy.md). 
>


## <a name="recovery-time"></a>A helyreállítási idő
Állítson vissza egy adatbázist automatizált adatbázis biztonsági mentése a helyreállítási idő számos tényezőtől kihatással van: 

* Az adatbázis mérete
* Az adatbázis teljesítményszintjét
* A tranzakciós naplók érintett száma
* Folyó tevékenység, amelyet a helyreállítást a helyreállítási pont bejegyzéseit meg kell ismételni
* A hálózati sávszélesség, ha a visszaállítást az, hogy egy másik régióban található 
* A cél régióban feldolgozás alatt álló egyidejű visszaállítás kérelmek száma. 
  
  Nagyon nagy és/vagy aktív-adatbázis a visszaállítás több óráig is eltarthat. Nincs leállás hosszabb régióban, akkor lehetséges, hogy vannak-e más régiókban által feldolgozott georedundáns helyreállítás kérelmek nagy mennyiségű. Ha sok kérelmet, a helyreállítás ideje megnőhet az adatbázisok az adott régióban. A legtöbb adatbázis visszaállítása befejeződött 12 órában.

Egyetlen előfizetéssel, vannak bizonyos korlátai számú párhuzamos visszaállítási kérések (beleértve a visszaállítás egy korábbi időpontra, a georedundáns helyreállítás és a hosszú távú megőrzési biztonsági másolatból való visszaállítása) alatt benyújtott és kezdett a:
|  | **Párhuzamos feldolgozás alatt álló kérelmek maximális száma** | **Nem továbbíthatók egyidejű kérelmek maximális száma** |
| :--- | --: | --: |
|Önálló adatbázis (előfizetésenként)|10|60|
|A rugalmas készlet (készletenként)|4|200|
||||

Nincs tömeges visszaállítási beépített funkció sem. A [Azure SQL Database: a teljes kiszolgáló helyreállítási](https://gallery.technet.microsoft.com/Azure-SQL-Database-Full-82941666) parancsfájl Ez a feladat parancsokról egyik módja egy példát.

> [!IMPORTANT]
> Automatikus biztonsági mentés használatával állítsa helyre, az SQL Server közreműködő szerepkört az előfizetés tagja, vagy az előfizetés tulajdonosa lesz. A helyreállítást az Azure Portal, a PowerShell vagy a REST API használatával végezheti. A Transact-SQL nem használható. 
> 

## <a name="point-in-time-restore"></a>Adott időpontnak megfelelő helyreállítás

Visszaállíthatja egy meglévő adatbázist egy korábbi időpontbeli ugyanazon a kiszolgálón logikai az Azure portál használatával új adatbázisként időben [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.sql/restore-azurermsqldatabase), vagy a [REST API](https://msdn.microsoft.com/library/azure/mt163685.aspx). 

> [!TIP]
> A PowerShell-parancsfájlpélda bemutatja, hogyan hajtsa végre egy adatbázis-időpontban visszaállítását, lásd: [állítson vissza egy SQL-adatbázist PowerShell](scripts/sql-database-restore-database-powershell.md).
>

Az adatbázis visszaállítása végezhető el a szolgáltatási szint vagy a teljesítmény szint, és egyetlen adatbázisként vagy rugalmas készletbe. Gondoskodjon arról, hogy elegendő erőforrással rendelkezik, a logikai kiszolgálón vagy a rugalmas készletben, amelyre az adatbázis visszaállítását végzi. Művelet befejeződése után a visszaállított adatbázis nem egy normál, teljes mértékben érhető el, online adatbázis. A visszaállított adatbázis fel van töltve, a normál díjszabás alapján a szolgáltatás és teljesítményszintet szintje. Nem függő díj terheli mindaddig, amíg az adatbázis visszaállítása befejeződött.

Általában egy korábbi időpontbeli helyreállítási célokra adatbázis visszaállítása. Annak során, akkor kezelje a visszaállított adatbázis helyettesíti az eredeti adatbázist a, vagy adatok beolvasásához, és módosítsa az eredeti adatbázist. 

* ***Adatbázis-csere:*** célja, hogy a visszaállított adatbázis az eredeti adatbázis helyett, ha, ellenőrizni kell a teljesítményszintet és/vagy a szolgáltatási rétegben megfelelőek, és szükség esetén az adatbázis méretezése. Nevezze át az eredeti adatbázist, és adja meg a visszaállított adatbázis eredeti nevét használja a [ALTER DATABASE](/sql/t-sql/statements/alter-database-azure-sql-database) T-SQL-parancsot. 
* ***Adat-helyreállítás:*** szeretne adatainak lekérése a visszaállított adatbázis helyreállítása egy felhasználó vagy alkalmazás hiba, ha szeretné-e írási és végrehajtási a szükséges adatok helyreállítási parancsfájlokat, ha adatokat szeretne kinyerni a visszaállított adatbázis az eredeti adatbázist. Bár a visszaállítási művelet elvégzéséhez hosszú ideig is eltarthat, a visszaállítási adatbázis a visszaállítási folyamat során az adatbázisok listája látható. Ha törli az adatbázist a visszaállítás során, a visszaállítási művelet megszakadt, és nem kell fizetnie az adatbázis, amely nem tudta végrehajtani a visszaállítást. 

### <a name="azure-portal"></a>Azure Portal

Helyreállítása pontra állapotra az Azure portál használatával, nyissa meg az adatbázis lapját, és kattintson **visszaállítása** az eszköztáron.

![point-in-time-restore](./media/sql-database-recovery-using-backups/point-in-time-recovery.png)

## <a name="deleted-database-restore"></a>A törölt adatbázis visszaállítása
Visszaállíthatja a törölt adatbázisok a törlés időpontjával, az azonos logikai kiszolgáló, az Azure-portált használja, a törölt adatbázis [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.sql/restore-azurermsqldatabase), vagy a [REST (createMode = visszaállítása)](https://msdn.microsoft.com/library/azure/mt163685.aspx). Visszaállíthatja a törölt adatbázisok egy korábbi állapotra a megőrzési használata közben [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.sql/restore-azurermsqldatabase).

> [!TIP]
> A PowerShell-parancsfájlpélda bemutatja, hogyan törölt adatbázis visszaállítása, lásd: [állítson vissza egy SQL-adatbázist PowerShell](scripts/sql-database-restore-database-powershell.md).
>

> [!IMPORTANT]
> Ha töröl egy Azure SQL adatbázis-kiszolgálópéldányra, az összes adatbázis is törlődnek, és nem állítható helyre. Jelenleg egy törölt kiszolgáló helyreállítása nem támogatott.
> 

### <a name="azure-portal"></a>Azure Portal

Során törölt adatbázis helyreállítása a [DTU-alapú modell megőrzési időszak](sql-database-service-tiers-dtu.md) vagy [vCore-alapú modell megőrzési időszak](sql-database-service-tiers-vcore.md) az Azure portál használatával, nyissa meg az oldalt, a kiszolgáló és a műveletek területen, kattintson a **Adatbázisait törölte a**.

![deleted-database-restore-1](./media/sql-database-recovery-using-backups/deleted-database-restore-1.png)


![deleted-database-restore-2](./media/sql-database-recovery-using-backups/deleted-database-restore-2.png)

## <a name="geo-restore"></a>Georedundáns helyreállítás
SQL-adatbázis minden olyan kiszolgálón bármely Azure régióban állíthatja vissza a legutóbbi georeplikált teljes és különbözeti biztonsági másolatok. Georedundáns helyreállítás adatforrása georedundáns biztonsági másolatból, és segítségével olyan adatbázis helyreállításához, akkor is, ha az adatbázis vagy datacenter kimaradás miatt nem érhető el. 

Georedundáns helyreállítás beállítás az alapértelmezett helyreállítási Ha az adatbázis nem érhető el a régióban, ahol az adatbázis egy incidens miatt. Ha a felügyeleti teendők központjaként, amelyhez a régió eredmények hiányában az adatbázis-alkalmazás, a kiszolgáló más régióban a georeplikált biztonsági másolatból visszaállíthatja egy adatbázis. Amikor a különbözeti biztonsági másolatot készít-e és egy Azure-bA georeplikált közötti késés blob egy másik régióban. Ez a késés lehet akár egy óra alatt tehát ha katasztrófa történik, lehet fel egy órával adatok veszhetnek el. A következő ábrán az adatbázis visszaállítása a legutóbbi elérhető biztonsági másolat egy másik régióban.

![georedundáns helyreállítás](./media/sql-database-geo-restore/geo-restore-2.png)

> [!TIP]
> A PowerShell-parancsfájlpélda bemutatja, hogyan hajtsa végre a georedundáns helyreállítás, lásd: [állítson vissza egy SQL-adatbázist PowerShell](scripts/sql-database-restore-database-powershell.md).
> 

Egy földrajzi másodlagos időpontban visszaállítás jelenleg nem támogatott. Csak az elsődleges adatbázis pont időponthoz kötött visszaállítás végezhető. Georedundáns helyreállítás kimaradás helyreállítás használatával kapcsolatos részletes információkért lásd: [kimaradás helyreállíthatók](sql-database-disaster-recovery.md).

> [!IMPORTANT]
> A biztonsági mentésekből helyreállítási a legalapvetőbb a leghosszabb helyreállítási-célkitűzés (RPO) és a becsült helyreállítási idő (Beszúrása) SQL-adatbázisban elérhető vész-helyreállítási megoldások. Kis méret adatbázisok (pl. Basic szolgáltatási rétegben vagy kis méretű adatbázisok rugalmas készletek bérlői) alkalmazó megoldások georedundáns helyreállítás esetén gyakran egy ésszerű vész-Helyreállítási megoldást egy Beszúrása 12 órát. A megoldások nagy adatbázisokhoz is használ, és rövidebb helyreállítási időpontokat, érdemes [feladatátvételi csoportok és aktív georeplikáció](sql-database-geo-replication-overview.md). Aktív georeplikáció sokkal rövidebb RPO és Beszúrása kínál csak szükséges, hogy kezdeményezze a feladatátvételt a folyamatosan replikált másodlagos. Üzleti folytonosság döntések további információkért lásd: [az üzletmenet folytonossága áttekintése](sql-database-business-continuity.md).
> 

### <a name="azure-portal"></a>Azure Portal

Georedundáns helyreállítás során az adatbázis számára a [DTU-alapú modell megőrzési időszak](sql-database-service-tiers-dtu.md) vagy [vCore-alapú modell megőrzési időszak](sql-database-service-tiers-vcore.md) az Azure portál használatával, nyissa meg az SQL-adatbázisok lapját, és kattintson **hozzáadása** . Az a **forrás kiválasztása** válassza ki a szövegmezőben **biztonsági mentés**. Adja meg a biztonsági mentés, amelyből a régióban, és a kiszolgálón, az Ön által választott, végezze el a helyreállítást. 

## <a name="programmatically-performing-recovery-using-automated-backups"></a>Programozott módon az automatikus biztonsági mentés segítségével történő helyreállítás végrehajtása
Korábban már említettük, az Azure portálon kívül, mert az adatbázis helyreállításának végrehajtható programozott módon, Azure PowerShell vagy a REST API-t. Az alábbi táblázatban láthatók az elérhető parancsok készlete.

### <a name="powershell"></a>PowerShell
| Parancsmag | Leírás |
| --- | --- |
| [Get-AzureRmSqlDatabase](/powershell/module/azurerm.sql/get-azurermsqldatabase) |Egy vagy több adatbázist kér le. |
| [Get-AzureRMSqlDeletedDatabaseBackup](/powershell/module/azurerm.sql/get-azurermsqldeleteddatabasebackup) | Lekér egy törölt adatbázist, amelyet visszaállíthat. |
| [Get-AzureRmSqlDatabaseGeoBackup](/powershell/module/azurerm.sql/get-azurermsqldatabasegeobackup) |Lekéri egy adatbázis georedundáns biztonsági másolatát. |
| [Restore-AzureRmSqlDatabase](/powershell/module/azurerm.sql/restore-azurermsqldatabase) |Visszaállít egy SQL-adatbázist. |
|  | |

### <a name="rest-api"></a>REST API
| API | Leírás |
| --- | --- |
| [REST (createMode helyreállítási =)](https://msdn.microsoft.com/library/azure/mt163685.aspx) |Adatbázis visszaállítása |
| [Get létrehozása, vagy az adatbázis állapotának frissítése](https://msdn.microsoft.com/library/azure/mt643934.aspx) |A visszaállítási művelet során állapotának visszaadása |
|  | |

## <a name="summary"></a>Összegzés
Az automatikus biztonsági mentés az adatbázisok védelméhez a felhasználói és a alkalmazáshibák, a véletlen adatbázisok törlése, a hosszabb kimaradások esetén. A beépített kapacitásprofilokban összes szolgáltatásszintek és teljesítményszintek érhető el. 

## <a name="next-steps"></a>További lépések
* Egy üzleti folytonosság – áttekintés és forgatókönyvek: [üzleti folytonosság – áttekintés](sql-database-business-continuity.md).
* További tudnivalók az Azure SQL adatbázis automatikus biztonsági mentés című [SQL-adatbázis biztonsági mentések automatikus](sql-database-automated-backups.md).
* Hosszú távú megőrzési kapcsolatos további tudnivalókért lásd: [hosszú távú megőrzési](sql-database-long-term-retention.md).
* Gyorsabb helyreállítási beállításokkal kapcsolatos további tudnivalókért lásd: [feladatátvételi csoportok és aktív georeplikáció](sql-database-geo-replication-overview.md).  
