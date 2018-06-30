---
title: Az Azure SQL Database DTU-alapú erőforrás korlátozza az önálló adatbázisok |} Microsoft Docs
description: Ez a lap néhány gyakori DTU-alapú erőforrás korlátot, az önálló adatbázisok Azure SQL Database ismerteti.
services: sql-database
author: sachinpMSFT
manager: craigg
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: conceptual
ms.date: 06/29/2018
ms.author: carlrab
ms.openlocfilehash: 2d6660e1064959f2d04424ae1c3e9bc668231c92
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2018
ms.locfileid: "37131321"
---
# <a name="resource-limits-for-single-databases-using-the-dtu-based-purchasing-model"></a>Erőforrás-korlátozások az önálló adatbázisok a DTU-alapú alapjául szolgáló vásárlási modell használatával 

A cikkben a részletes erőforrás-korlátozások az Azure SQL Database rugalmas készletek a DTU-alapú alapjául szolgáló vásárlási modell használatával.

DTU-alapú vásárlási modell erőforrás-korlátok a rugalmas, lásd: [DTU-alapú erőforrás korlátok - rugalmas készletek](sql-database-vcore-resource-limits-elastic-pools.md). VCore alapuló erőforrás-korlátok, lásd: [vCore-alapú erőforrás korlátok - önálló adatbázisok](sql-database-vcore-resource-limits-single-databases.md) és [vCore-alapú erőforrás korlátok - rugalmas készletek](sql-database-vcore-resource-limits-elastic-pools.md).

## <a name="single-database-storage-sizes-and-performance-levels"></a>Önálló adatbázist: tárterületet és teljesítményszintek

Az önálló adatbázisok az alábbi táblázatokban minden szolgáltatás és teljesítményszintet szintjén egyszeri adatbázis rendelkezésre álló erőforrások. A szolgáltatási rétegben, teljesítményszintet és egy önálló adatbázis használatára vonatkozó mennyisége állíthatja be a [Azure-portálon](sql-database-servers-databases-manage.md#azure-portal-manage-logical-servers-and-databases), [PowerShell](sql-database-servers-databases-manage.md#powershell-manage-logical-servers-and-databases), a [Azure CLI](sql-database-servers-databases-manage.md#azure-cli-manage-logical-servers-and-databases), vagy a [REST API-t](sql-database-servers-databases-manage.md#rest-api-manage-logical-servers-and-databases).

### <a name="basic-service-tier"></a>Alapszintű szolgáltatásszint
| **Teljesítményszint szükséges** | **Basic** |
| :--- | --: |
| DTU-k maximális száma | 5 |
| Belefoglalt tárhely (GB) | 2 |
| Maximális tárolási lehetőségek (GB) | 2 |
| Memóriában tárolt OLTP-k maximális tárterülete (GB) |– |
| Maximális párhuzamos munkavállalók (kérelmek) | 30 |
| Egyidejű munkamenetek maximális száma | 300 |
|||

### <a name="standard-service-tier"></a>Standard szolgáltatásszint
| **Teljesítményszint szükséges** | **S0** | **S1** | **S2** | **S3** |
| :--- |---:| ---:|---:|---:|---:|
| DTU-k maximális száma | 10 | 20 | 50 | 100 |
| Belefoglalt tárhely (GB) | 250 | 250 | 250 | 250 |
| Maximális tárolási lehetőségek (GB) | 250 | 250 | 250 | 250, 500, 750, 1024 |
| Memóriában tárolt OLTP-k maximális tárterülete (GB) | – | N/A | N/A | – |
| Maximális párhuzamos munkavállalók (kérelmek)| 60 | 90 | 120 | 200 |
| Egyidejű munkamenetek maximális száma |600 | 900 | 1200 | 2400 |
||||||

### <a name="standard-service-tier-continued"></a>Standard szintű szolgáltatási réteg (Folytatás)
| **Teljesítményszint szükséges** | **S4** | **S6** | **S7** | **S9** | **S12** |
| :--- |---:| ---:|---:|---:|---:|---:|
| DTU-k maximális száma | 200 | 400 | 800 | 1600 | 3000 |
| Belefoglalt tárhely (GB) | 250 | 250 | 250 | 250 | 250 |
| Maximális tárolási lehetőségek (GB) | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 |
| Memóriában tárolt OLTP-k maximális tárterülete (GB) | – | N/A | N/A | N/A |– |
| Maximális párhuzamos munkavállalók (kérelmek)| 400 | 800 | 1600 | 3200 |6000 |
| Egyidejű munkamenetek maximális száma |4800 | 9600 | 19200 | 30000 |30000 |
|||||||

### <a name="premium-service-tier"></a>Prémium szolgáltatásszint 
| **Teljesítményszint szükséges** | **P1** | **P2** | **P4** | **P6** | **P11** | **P15** | 
| :--- |---:|---:|---:|---:|---:|---:|
| DTU-k maximális száma | 125 | 250 | 500 | 1000 | 1750 | 4000 |
| Belefoglalt tárhely (GB) | 500 | 500 | 500 | 500 | 4096 | 4096 |
| Maximális tárolási lehetőségek (GB) | 500, 750, 1024 | 500, 750, 1024 | 500, 750, 1024 | 500, 750, 1024 | 4096 | 4096 |
| Memóriában tárolt OLTP-k maximális tárterülete (GB) | 1 | 2 | 4 | 8 | 14 | 32 |
| Maximális párhuzamos munkavállalók (kérelmek)| 200 | 400 | 800 | 1600 | 2400 | 6400 |
| Egyidejű munkamenetek maximális száma | 30000 | 30000 | 30000 | 30000 | 30000 | 30000 |
|||||||


> [!IMPORTANT]
> Több mint 1 TB-nyi tárhelyre prémium tarifacsomagra a jelenleg rendelkezésre áll a következő kivételével minden régióban: nyugati középső Régiójában, Kína keleti, USDoDCentral, Németország központi, USDoDEast, USA – (kormányzati) délnyugati, Németország szerepel, USGovIowa, Kína északi. Más régiókban a Prémium szinthez tartozó tárterület maximuma 1 TB. Lásd: [P11–P15 – Aktuális korlátozások](#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb).  

## <a name="single-database-change-storage-size"></a>Önálló adatbázist: tároló méretének módosítása

- A DTU ára egy önálló adatbázis tárolási további költségek nélkül bizonyos mennyiségű tartalmaz. Megnövelt tárhely meghaladó szerepel az a maximális méretkorlát fel és 1 TB 250 GB-os lépésekben, majd a 256 GB felett 1 TB-os lépésekben akár további költségek telepíthető. Belefoglalt tárolási összegeket és a maximális méretkorlát [önálló adatbázist: tárterületet és teljesítményszintek](#single-database-storage-sizes-and-performance-levels).
- Egyetlen-adatbázis – megnövelt tárhely növeli a maximális méret használatával helyezhetők a [Azure-portálon](sql-database-servers-databases-manage.md#azure-portal-manage-logical-servers-and-databases), [Transact-SQL](/sql/t-sql/statements/alter-database-azure-sql-database#examples), [PowerShell](/powershell/module/azurerm.sql/set-azurermsqldatabase), a [Azure Parancssori felület](/cli/azure/sql/db#az_sql_db_update), vagy a [REST API-t](/rest/api/sql/databases/update).
- Egyetlen-adatbázis – megnövelt tárhely ára érték a megnövelt tárhely megszorozza a szolgáltatási rétegben – megnövelt tárhely egységárát. Extra tároló árának részletekért lásd: [SQL Database – díjszabás](https://azure.microsoft.com/pricing/details/sql-database/).

## <a name="single-database-change-dtus"></a>Önálló adatbázist: módosítás dtu-k

Után először válassza háttérszínnek. a szolgáltatási rétegben, teljesítményszintet és mennyisége, méretezheti egy önálló adatbázis felfelé vagy lefelé a tényleges élmény használatával dinamikusan alapján a [Azure-portálon](sql-database-servers-databases-manage.md#azure-portal-manage-logical-servers-and-databases), [Transact-SQL](/sql/t-sql/statements/alter-database-azure-sql-database#examples), [PowerShell](/powershell/module/azurerm.sql/set-azurermsqldatabase), a [Azure CLI](/cli/azure/sql/db#az_sql_db_update), vagy a [REST API-t](/rest/api/sql/databases/update). 

A következő videó bemutatja, hogy dinamikusan a teljesítményszint növelése egy önálló adatbázis elérhető dtu-i módosítását.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-dynamically-scale-up-or-scale-down/player]
>

Az adatbázis szolgáltatásszintjének és/vagy teljesítményszintjének megváltoztatásakor egy replika jön létre az eredeti adatbázisról az új teljesítményszinten, majd a rendszer átáll a replikához való csatlakozásra. A folyamat során nem történik adatvesztés, de a replikára való átálláskor egy pillanatra az adatbázis felé irányuló kapcsolatok le lesznek tiltva, így lehet, hogy néhány folyamatban lévő tranzakció vissza lesz állítva. A kapcsoló-over idő hosszával platformonként változó, de kisebb, mint 30 másodperc 99 %-ában. Ha nagy számát, a pillanatnyilag kapcsolatok útban tranzakciók le vannak tiltva, akkor lehet, hogy a kapcsoló-over időtartama hosszabb. 

A teljes felskálázási folyamat időtartama az adatbázis a módosítás előtti és utáni méretétől és szolgáltatásszintjétől függ. Például változik, a, vagy a szabványos szolgáltatásréteg, 250 GB-os adatbázis hat órán belül kell végrehajtani. Adatbázis méretének teljesítményszintet belül a prémium szolgáltatásszintet változik a méretezett három órán belül kell végrehajtani.

> [!TIP]
> A-e-mailekben műveletek figyeléséről lásd:: [SQL REST API használatával műveleteinek a felügyeletét,](/rest/api/sql/Operations/List), [parancssori felület használatával műveleteinek a felügyeletét,](/cli/azure/sql/db/op), [műveletek T-SQL használatával figyelheti](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) , és ez a két PowerShell-parancsok: [Get-AzureRmSqlDatabaseActivity](/powershell/module/azurerm.sql/get-azurermsqldatabaseactivity) és [Stop-AzureRmSqlDatabaseActivity](/powershell/module/azurerm.sql/stop-azurermsqldatabaseactivity).

* Ha frissíti a szolgáltatási szint vagy a teljesítmény a magasabb, az adatbázis maximális méretét nem növekszik, hacsak Ön kifejezetten megad egy nagyobb méretű (maxsize).
* Egy adatbázis megállapításában, a használt adatbázis-terület kisebb, mint a megengedett maximális méret a célként megadott szolgáltatás és teljesítményszintet szinten kell lennie. 
* Amikor alacsonyabb verziójúra változtatása a **prémium** számára a **szabványos** réteg, egy extra storage költséget akkor használható, ha mind a cél teljesítményszintjét támogatott (1) az adatbázis maximális méretét, és (2) a maximális mérete meghaladja a a cél teljesítményszintjét belefoglalt tárolási mennyisége. Például ha 500 GB maximális mérettel P1 adatbázis való S3 van downsized, majd egy – megnövelt tárhely költség érvényes óta S3 támogatja 500 GB-os maximális méretét, és a belefoglalt mennyisége csak 250 GB. Igen a megnövelt tárhely mérete 500 GB – 250 GB = 250 GB. Az árképzés további tárhelyet, lásd: [SQL Database – díjszabás](https://azure.microsoft.com/pricing/details/sql-database/). Ha a tényleges használt lemezterület mérete kisebb, mint a belefoglalt mennyisége, majd ez kapcsolódik további költség elkerülhető az adatbázis maximális méretét csökkentve a belefoglalt legkevesebb. 
* Ha az adatbázis frissítése [georeplikáció](sql-database-geo-replication-portal.md) engedélyezve van, frissítse a másodlagos adatbázisok a kívánt teljesítményt réteghez csak azután frissítse az elsődleges adatbázis (általános útmutatóként is szolgálhatnak a legjobb teljesítmény érdekében). Egy másik alkalmazásra történő verziófrissítés a másodlagos adatbázist frissíti először szükség.
* Amikor alacsonyabb verziójúra változtatása adatbázis [georeplikáció](sql-database-geo-replication-portal.md) engedélyezve van, alacsonyabb a kívánt teljesítményt réteghez elsődleges adatbázisainak előtt alacsonyabb verziójúra változtatása a másodlagos adatbázis (általános útmutatóként is szolgálhatnak a legjobb teljesítmény érdekében). Visszaminősítése másik kiadásra, az elsődleges adatbázis visszaminősítése először szükség.
* A visszaállítási szolgáltatásajánlatok eltérőek a különböző szolgáltatásszintek esetében. Ha alacsonyabb verzióra való visszatérést meg, hogy a **alapvető** réteg, alacsonyabb biztonsági mentés megőrzési idővel van – lásd [Azure SQL adatbázis biztonsági másolatait](sql-database-automated-backups.md).
* Az adatbázis új tulajdonságai csak akkor lesznek alkalmazva, ha a módosítások befejeződtek.

## <a name="single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb"></a>Önálló adatbázist: P11 és P15, ha a maximális méret 1 TB-nál nagyobb korlátozásai

Egy 1 TB-nál nagyobb, a P11 és P15 adatbázis a következő régiókban támogatott maximális mérete: Kelet-Ausztrália, Ausztrália délkeleti, Dél-Brazília, Kanada központi, Kanada keleti régiója, USA középső RÉGIÓJA, Franciaország központi, Németország központi, kelet-japán, Nyugat-japán, koreai központi Észak-USA középső RÉGIÓJA, Észak-Európa, USA déli középső RÉGIÓJA, Délkelet-Ázsia, Egyesült Királyság déli régiója, Egyesült Királyság nyugati régiója, USA East2, USA nyugati régiója, USA – (kormányzati) Virginia és Nyugat-Európa. Az alábbi szempontokat és korlátozásokat 1 TB-nál nagyobb maximális mérettel P11 és P15 adatbázisokra érvényesek:

- Ha 1 TB-nál nagyobb méretű egy adatbázist (értéke 4 TB-os vagy 4096 GB) létrehozásához, a Létrehozás parancs hibával meghiúsul, ha az adatbázis egy nem támogatott régióban lett kiépítve.
- A támogatott régiók egyikéhez sem található meglévő P11 és P15 adatbázisokhoz, növelheti a maximális tárhelyet túl az 1 TB-os lépésekben 256 GB akár 4 TB. Ha egy nagyobb méretű támogatott-e az adott régióban, használja a [DATABASEPROPERTYEX](/sql/t-sql/functions/databasepropertyex-transact-sql) működik, vagy vizsgálja meg az adatbázis mérete az Azure portálon. Egy meglévő P11 vagy P15 frissítése adatbázis csak hajtható végre egy kiszolgálószintű fő bejelentkezéssel, vagy a dbmanager adatbázis-szerepkör tagjai. 
- Ha egy frissítési művelet végrehajtása egy támogatott régióban a konfigurációja azonnal frissül. Az adatbázis online állapotban marad, a frissítési folyamat során. Azonban nem használhatja a teljes mennyisége túl az 1 TB-nyi tárhelyre tárolási csak az aktuális adatbázisfájlok frissítése után az új maximális méretét. A frissítés alatt álló adatbázis méretétől függ szükséges idő hosszát. 
- Létrehozásakor, vagy egy P11 vagy P15 adatbázis frissítése, csak választhat 1 TB méretű és 4 TB méretű maximális méret 256 GB-os lépésekben. Egy P11/P15 létrehozásakor az 1 TB-os alapértelmezett tárolási lehetőség az előre kijelölt. Az adatbázisok a támogatott régiók egyikéhez sem található a tároló maximális 4 TB-os legfeljebb egy új vagy meglévő egyetlen adatbázis növelhető. Minden más területen maximális mérete 1 TB-os fent nem növelhető. Az ár nem változtatja meg, ha 4 TB-nyi tárhelyre belefoglalt választja.
- Ha egy adatbázis maximális méretét értéke nagyobb, mint 1 TB-os, majd azt nem módosítható és 1 TB akkor is, ha a ténylegesen használt tárhely 1 TB alatt. Így akkor nem visszaminősítését egy P11 vagy P15 egy 1 TB-os P11 vagy 1 TB-os P15 vagy alacsonyabb teljesítményt réteg, például a P1-P6 1 TB-nál nagyobb maximális mérettel). Ez a korlátozás vonatkozik a visszaállítási és is-időpontban, például másolás forgatókönyvek georedundáns helyreállítás, hosszú-távú biztonsági mentés-adatmegőrzés és az adatbázis másolása. Amennyiben az adatbázis mérete nagyobb, mint 1 TB-os van beállítva, az adatbázis összes visszaállítási műveletek 1 TB-nál nagyobb maximális mérettel P11/P15 be kell futnia.
- Aktív georeplikáció forgatókönyvek esetén:
   - A georeplikáció kapcsolat beállítása: Ha az elsődleges adatbázis P11 vagy P15, a secondary(ies) is kell P11 vagy P15; alacsonyabb teljesítményt rétegek visszautasítja másodlagos adatbázist, mert azok nem képes a több mint 1 TB.
   - A georeplikáció kapcsolatban az elsődleges adatbázis frissítése: az elsődleges adatbázis több mint 1 TB maximális méretének módosítása váltja ki ezt a változtatást, a másodlagos adatbázison. Két frissítést a változtatás érvénybe léptetéséhez elsődleges sikeresnek kell lennie. A több mint 1 TB-beállítás régió korlátozások érvényesek. Ha a másodlagos régióban, amely nem támogatja a több mint 1 TB-os, az elsődleges nincs frissítve.
- A több mint 1 TB-os P11/P15 adatbázisokkal feltöltését Import/Export szolgáltatás használata nem támogatott. Használja a SqlPackage.exe [importálása](sql-database-import.md) és [exportálása](sql-database-export.md) adatokat.

## <a name="next-steps"></a>További lépések

- Lásd: [SQL-adatbázis – gyakori kérdések](sql-database-faq.md) gyakran feltett kérdésekre adott válaszokat.
- Általános Azure korlátozását kapcsolatos információkért lásd: [Azure-előfizetés és szolgáltatási korlátok, kvóták és megkötések](../azure-subscription-service-limits.md).
- További információ a Dtu és edtu-k: [Dtu és edtu-k](sql-database-service-tiers.md#what-are-database-transaction-units-dtus).
- A tempdb méretkorlátait kapcsolatos információkért lásd: https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database.
