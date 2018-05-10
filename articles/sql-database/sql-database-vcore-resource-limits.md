---
title: Az Azure SQL Database vCore-alapú erőforrás korlátok |} Microsoft Docs
description: Ez a lap az Azure SQL Database néhány gyakori vCore-alapú erőforrás korlátot ismerteti.
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: article
ms.date: 05/07/2018
ms.author: carlrab
ms.openlocfilehash: fc32ba4858e7be901d2cd4d773491247e9e0e672
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2018
---
# <a name="azure-sql-database-vcore-based-purchasing-model-limits-preview"></a>Az Azure SQL Database vCore alapú vásárlási modell korlátok (előzetes verzió)

> [!IMPORTANT]
> DTU-alapú vásárlási modell-korlátok, lásd: [SQL Database DTU-alapú erőforrás korlátok](sql-database-dtu-resource-limits.md).

## <a name="single-database-storage-sizes-and-performance-levels"></a>Önálló adatbázist: tárterületet és teljesítményszintek

Az önálló adatbázisok az alábbi táblázatokban minden szolgáltatás és teljesítményszintet szintjén egyszeri adatbázis rendelkezésre álló erőforrások. A szolgáltatási rétegben, teljesítményszintet és egy önálló adatbázis használatára vonatkozó mennyisége állíthatja be a [Azure-portálon](sql-database-single-database-resources.md#manage-single-database-resources-using-the-azure-portal), [Transact-SQL](sql-database-single-database-resources.md#manage-single-database-resources-using-transact-sql), [PowerShell](sql-database-single-database-resources.md#manage-single-database-resources-using-powershell), a [Azure CLI](sql-database-single-database-resources.md#manage-single-database-resources-using-the-azure-cli), vagy a [REST API-t](sql-database-single-database-resources.md#manage-single-database-resources-using-the-rest-api).

### <a name="general-purpose-service-tier"></a>Általános célú szolgáltatási rétegben

#### <a name="generation-4-compute-platform"></a>4. generációs számítógépes platform
|Teljesítményszint|GP_Gen4_1|GP_Gen4_2|GP_Gen4_4|GP_Gen4_8|GP_Gen4_16|GP_Gen4_24
|:--- | --: |--: |--: |--: |--: |--: |
|H/W létrehozása|4|4|4|4|4|4|
|Virtuális magok|1|2|4|8|16|24|
|Memória (GB)|7|14|28|56|112|168|
|Oszlopcentrikus támogatása|Igen|Igen|Igen|Igen|Igen|Igen|
|Memórián belüli online Tranzakciófeldolgozási tárhely (GB)|–|N/A|N/A|N/A|N/A|–|
|Tárolási típus|Prémium () távtároló|Prémium () távtároló|Prémium () távtároló|Prémium () távtároló|Prémium () távtároló|Prémium () távtároló|
|I/O várakozási ideje (hozzávetőleges)|5-7 ms (írás)<br>5-10 ms (olvasni.)|5-7 ms (írás)<br>5-10 ms (olvasni.)|5-7 ms (írás)<br>5-10 ms (olvasni.)|5-7 ms (írás)<br>5-10 ms (olvasni.)|5-7 ms (írás)<br>5-10 ms (olvasni.)|5-7 ms (írás)<br>5-10 ms (olvasni.)|
|Maximális adatok (GB)|1024|1024|1536|3072|4096|4096|
|Maximális naplófájlméret|307|307|461|922|1229|1229|
|A TempDB size(DB)|32|64|128|256|384|384|
|Cél IOPS (64 KB)|500|1000|2000|4000|7000|7000|
|Maximális párhuzamos munkavállalók (kérelmek)|200|400|800|1600|3200|4800|
|A maximális munkamenet engedélyezett|30000|30000|30000|30000|30000|30000|
|Replikák száma|1|1|1|1|1|1|
|Több-AZ|–|N/A|N/A|N/A|N/A|–|000
|Olvassa el a kibővített|–|N/A|N/A|N/A|N/A|–|
|Biztonsági másolatok tárolásának tartalmazza|1 X-adatbázis mérete|1 X-adatbázis mérete|1 X-adatbázis mérete|1 X-adatbázis mérete|1 X-adatbázis mérete|1 X-adatbázis mérete|
|||

#### <a name="generation-5-compute-platform"></a>5. generációs számítógépes platform
|Teljesítményszint|GP_Gen5_2|GP_Gen5_4|GP_Gen5_8|GP_Gen5_16|GP_Gen5_24|GP_Gen5_32|GP_Gen5_48| GP_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |--: |--: |
|H/W létrehozása|5|5|5|5|5|5|5|
|Virtuális magok|2|4|8|16|24|32|48|80|
|Memória (GB)|11|22|44|88|132|176|264|440|
|Oszlopcentrikus támogatása|Igen|Igen|Igen|Igen|Igen|Igen|Igen|Igen|
|Memórián belüli online Tranzakciófeldolgozási tárhely (GB)|–|N/A|N/A|N/A|N/A|N/A|N/A|–|
|Tárolási típus|Prémium () távtároló|Prémium () távtároló|Prémium () távtároló|Prémium () távtároló|Prémium () távtároló|Prémium () távtároló|Prémium () távtároló|Prémium () távtároló|
|I/O várakozási ideje (hozzávetőleges)|5-7 ms (írás)<br>5-10 ms (olvasni.)|5-7 ms (írás)<br>5-10 ms (olvasni.)|5-7 ms (írás)<br>5-10 ms (olvasni.)|5-7 ms (írás)<br>5-10 ms (olvasni.)|5-7 ms (írás)<br>5-10 ms (olvasni.)|5-7 ms (írás)<br>5-10 ms (olvasni.)|5-7 ms (írás)<br>5-10 ms (olvasni.)|5-7 ms (írás)<br>5-10 ms (olvasni.)|
|Maximális adatok (GB)|1024|1024|1536|3072|4096|4096|4096|4096|
|Maximális naplófájlméret|307|307|461|614|1229|1229|1229|1229|
|A TempDB size(DB)|64|128|256|384|384|384|384|384|
|Cél IOPS (64 KB)|500|1000|2000|4000|6000|7000|7000|7000|
|Maximális párhuzamos munkavállalók (kérelmek)|200|400|800|1600|2400|3200|4800|8000|
|A maximális munkamenet engedélyezett|30000|30000|30000|30000|30000|30000|30000|30000|
|Replikák száma|1|1|1|1|1|1|1|1|
|Több-AZ|–|N/A|N/A|N/A|N/A|N/A|N/A|–|
|Olvassa el a kibővített|–|N/A|N/A|N/A|N/A|N/A|N/A|–|
|Biztonsági másolatok tárolásának tartalmazza|1 X-adatbázis mérete|1 X-adatbázis mérete|1 X-adatbázis mérete|1 X-adatbázis mérete|1 X-adatbázis mérete|1 X-adatbázis mérete|1 X-adatbázis mérete|1 X-adatbázis mérete|
|||

### <a name="business-critical-service-tier"></a>Üzleti kritikus szolgáltatási rétegben

#### <a name="generation-4-compute-platform"></a>4. generációs számítógépes platform
|Teljesítményszint|BC_Gen4_1|BC_Gen4_2|BC_Gen4_4|BC_Gen4_8|BC_Gen4_16|BC_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |
|H/W létrehozása|4|4|4|4|4|4|
|Virtuális magok|1|2|4|8|16|24|
|Memória (GB)|7|14|28|56|112|168|
|Oszlopcentrikus támogatása|Igen|Igen|Igen|Igen|Igen|Igen|
|Memórián belüli online Tranzakciófeldolgozási tárhely (GB)|1|2|4|8|20|36|
|Tárolási típus|Helyi SSD|Helyi SSD|Helyi SSD|Helyi SSD|Helyi SSD|Helyi SSD|
|Maximális adatok (GB)|1024|1024|1024|1024|1024|1024|
|Maximális naplófájlméret|307|307|307|307|307|307|
|A TempDB size(DB)|32|64|128|256|384|384|
|Cél IOPS (64 KB)|5000|10000|20000|40000|80000|120000|
|I/O várakozási ideje (hozzávetőleges)|1-2 ms (írás)<br>1-2 ms (olvasni.)|1-2 ms (írás)<br>1-2 ms (olvasni.)|1-2 ms (írás)<br>1-2 ms (olvasni.)|1-2 ms (írás)<br>1-2 ms (olvasni.)|1-2 ms (írás)<br>1-2 ms (olvasni.)|1-2 ms (írás)<br>1-2 ms (olvasni.)|
|Maximális párhuzamos munkavállalók (kérelmek)|200|400|800|1600|3200|4800|
|A maximális munkamenet engedélyezett|30000|30000|30000|30000|30000|30000|
|Replikák száma|3|3|3|3|3|3|
|Több-AZ|Igen|Igen|Igen|Igen|Igen|Igen|
|Olvassa el a kibővített|Igen|Igen|Igen|Igen|Igen|Igen|
|Biztonsági másolatok tárolásának tartalmazza|1 X-adatbázis mérete|1 X-adatbázis mérete|1 X-adatbázis mérete|1 X-adatbázis mérete|1 X-adatbázis mérete|1 X-adatbázis mérete|
|||

#### <a name="generation-5-compute-platform"></a>5. generációs számítógépes platform
|Teljesítményszint|BC_Gen5_2|BC_Gen5_4|BC_Gen5_8|BC_Gen5_16|BC_Gen5_24|BC_Gen5_32|BC_Gen5_48|BC_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |--: |--: |--: |--: |--: |--: |
|H/W létrehozása|5|5|5|5|5|5|5|5|
|Virtuális magok|2|4|8|16|24|32|48|80|
|Memória (GB)|11|22|44|88|132|176|264|440|
|Oszlopcentrikus támogatása|Igen|Igen|Igen|Igen|Igen|Igen|Igen|Igen|
|Memórián belüli online Tranzakciófeldolgozási tárhely (GB)|1.571|3,142|6.284|15.768|25.252|37.936|68.104|131.64|
|Tárolási típus|Helyi SSD|Helyi SSD|Helyi SSD|Helyi SSD|Helyi SSD|Helyi SSD|Helyi SSD|Helyi SSD|
|I/O várakozási ideje (hozzávetőleges)|1-2 ms (írás)<br>1-2 ms (olvasni.)|1-2 ms (írás)<br>1-2 ms (olvasni.)|1-2 ms (írás)<br>1-2 ms (olvasni.)|1-2 ms (írás)<br>1-2 ms (olvasni.)|1-2 ms (írás)<br>1-2 ms (olvasni.)|1-2 ms (írás)<br>1-2 ms (olvasni.)|1-2 ms (írás)<br>1-2 ms (olvasni.)|1-2 ms (írás)<br>1-2 ms (olvasni.)|
|Maximális adatok (GB)|1024|1024|1024|1024|2048|4096|4096|4096|
|Maximális naplófájlméret|307|307|307|307|614|1229|1229|1229|
|A TempDB size(DB)|64|128|256|384|384|384|384|384|
|Cél IOPS (64 KB)|5000|10000|20000|40000|60000|80000|120000|200000
|Maximális párhuzamos munkavállalók (kérelmek)|200|400|800|1600|2400|3200|4800|8000|
|A maximális munkamenet engedélyezett|30000|30000|30000|30000|30000|30000|30000|30000|
|Replikák száma|1|1|1|1|1|1|1|1|
|Több-AZ|–|N/A|N/A|N/A|N/A|N/A|N/A|–|
|Olvassa el a kibővített|–|N/A|N/A|N/A|N/A|N/A|N/A|–|
|Biztonsági másolatok tárolásának tartalmazza|1 X-adatbázis mérete|1 X-adatbázis mérete|1 X-adatbázis mérete|1 X-adatbázis mérete|1 X-adatbázis mérete|1 X-adatbázis mérete|1 X-adatbázis mérete|1 X-adatbázis mérete|
|||

## <a name="single-database-change-storage-size"></a>Önálló adatbázist: tároló méretének módosítása

- Tárolók kiépítésével legfeljebb 1 GB-os lépésekben használatával a maximális méretkorlátot. A minimális konfigurálható adattárolási 5GB 
- Egy adatbázis-tároló növelésével vagy csökkentésével, a maximális méret használatával helyezhetők a [Azure-portálon](sql-database-single-database-resources.md#manage-single-database-resources-using-the-azure-portal), [Transact-SQL](/sql/t-sql/statements/alter-database-azure-sql-database#examples), [PowerShell](/powershell/module/azurerm.sql/set-azurermsqldatabase), a [Azure CLI](/cli/azure/sql/db#az_sql_db_update), vagy a [REST API-t](/rest/api/sql/databases/update).
- SQL-adatbázis automatikusan lefoglalja számára további tárhely 30 %-át a naplófájlok és 32GB / vCore a TempDB adatbázishoz, de nem haladhatja meg a 384GB. A TempDB egy csatolt SSD összes szolgáltatásrétegekben található.
- Egy adatbázis-tároló árának összege adatok tárolási és a naplófájlok tárolási megszorozza a szolgáltatási rétegben tárolási egységárát. A TempDB költségét vCore ára tartalmazza. Extra tároló árának részletekért lásd: [SQL Database – díjszabás](https://azure.microsoft.com/pricing/details/sql-database/).

## <a name="single-database-change-vcores"></a>Önálló adatbázist: vCores módosítása

Után először válassza háttérszínnek vCores száma, méretezheti egy önálló adatbázis felfelé vagy lefelé a tényleges élmény használatával dinamikusan alapján a [Azure-portálon](sql-database-single-database-resources.md#manage-single-database-resources-using-the-azure-portal), [Transact-SQL](/sql/t-sql/statements/alter-database-azure-sql-database#examples), [PowerShell](/powershell/module/azurerm.sql/set-azurermsqldatabase), a [Azure CLI](/cli/azure/sql/db#az_sql_db_update), vagy a [REST API-t](/rest/api/sql/databases/update). 

Az adatbázis szolgáltatásszintjének és/vagy teljesítményszintjének megváltoztatásakor egy replika jön létre az eredeti adatbázisról az új teljesítményszinten, majd a rendszer átáll a replikához való csatlakozásra. A folyamat során nem történik adatvesztés, de a replikára való átálláskor egy pillanatra az adatbázis felé irányuló kapcsolatok le lesznek tiltva, így lehet, hogy néhány folyamatban lévő tranzakció vissza lesz állítva. A kapcsoló-over idő hosszával platformonként változó, de általában a 4 másodperc érték kisebb, mint 30 másodperc 99 %-ában. Ha nagy számát, a pillanatnyilag kapcsolatok útban tranzakciók le vannak tiltva, akkor lehet, hogy a kapcsoló-over időtartama hosszabb. 

A teljes felskálázási folyamat időtartama az adatbázis a módosítás előtti és utáni méretétől és szolgáltatásszintjétől függ. Például, a, vagy a egy általános célú szolgáltatásszint-jal 250 GB-os adatbázist hat órán belül kell végeznie. Adatbázis méretének teljesítményszintet belül a fontos üzleti szolgáltatásréteg változik a méretezett három órán belül kell végrehajtani.

> [!TIP]
> A-e-mailekben műveletek figyeléséről lásd:: [SQL REST API használatával műveleteinek a felügyeletét,](/rest/api/sql/Operations/List), [parancssori felület használatával műveleteinek a felügyeletét,](/cli/azure/sql/db/op), [műveletek T-SQL használatával figyelheti](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) , és ez a két PowerShell-parancsok: [Get-AzureRmSqlDatabaseActivity](/powershell/module/azurerm.sql/get-azurermsqldatabaseactivity) és [Stop-AzureRmSqlDatabaseActivity](/powershell/module/azurerm.sql/stop-azurermsqldatabaseactivity).

* Ha frissíti a szolgáltatási szint vagy a teljesítmény a magasabb, az adatbázis maximális méretét nem növekszik, hacsak Ön kifejezetten megad egy nagyobb méretű (maxsize).
* Egy adatbázis megállapításában, a használt adatbázis-terület kisebb, mint a megengedett maximális méret a célként megadott szolgáltatás és teljesítményszintet szinten kell lennie. 
* Ha az adatbázis frissítése [georeplikáció](sql-database-geo-replication-portal.md) engedélyezve van, frissítse a másodlagos adatbázisok a kívánt teljesítményt réteghez csak azután frissítse az elsődleges adatbázis (általános útmutatóként is szolgálhatnak a legjobb teljesítmény érdekében). Egy másik alkalmazásra történő verziófrissítés a másodlagos adatbázist frissíti először szükség.
* Amikor alacsonyabb verziójúra változtatása adatbázis [georeplikáció](sql-database-geo-replication-portal.md) engedélyezve van, alacsonyabb a kívánt teljesítményt réteghez elsődleges adatbázisainak előtt alacsonyabb verziójúra változtatása a másodlagos adatbázis (általános útmutatóként is szolgálhatnak a legjobb teljesítmény érdekében). Visszaminősítése másik kiadásra, az elsődleges adatbázis visszaminősítése először szükség.
* Az adatbázis új tulajdonságai csak akkor lesznek alkalmazva, ha a módosítások befejeződtek.

## <a name="elastic-pool-storage-sizes-and-performance-levels"></a>A rugalmas készlet: tárterületet és teljesítményszintek

SQL rugalmas adatbáziskészletek az alábbi táblázatok bemutatják a minden egyes szolgáltatást és teljesítményszintet szinten elérhető erőforrások. A szolgáltatási rétegben, a teljesítményszintet és a tárolási összeg használatával állíthatja be a [Azure-portálon](sql-database-elastic-pool.md#manage-elastic-pools-and-databases-using-the-azure-portal), [PowerShell](sql-database-elastic-pool.md#manage-elastic-pools-and-databases-using-powershell), a [Azure CLI](sql-database-elastic-pool.md#manage-elastic-pools-and-databases-using-the-azure-cli), vagy a [REST API](sql-database-elastic-pool.md#manage-elastic-pools-and-databases-using-the-rest-api).

> [!NOTE]
> Az egyes adatbázisokat rugalmas készletek erőforrás korlátai által megszabott megegyeznek általában önálló adatbázisok kívül erőforráskészleteket, amelyekben az azonos teljesítmény szinttel rendelkezik. A maximális párhuzamos munkavállalók GP_Gen4_1 adatbázis például 200 munkavállalók. Igen lévő GP_Gen4_1 készlet maximális párhuzamos munkavállalók egyben 200 munkavállalók. Vegye figyelembe, száma párhuzamos munkavállalók GP_Gen4_1 készletben 210.

### <a name="general-purpose-service-tier"></a>Általános célú szolgáltatási rétegben

#### <a name="generation-4-compute-platform"></a>4. generációs számítógépes platform
|Teljesítményszint|GP_Gen4_1|GP_Gen4_2|GP_Gen4_4|GP_Gen4_8|GP_Gen4_16|GP_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |
|H/W létrehozása|4|4|4|4|4|4|
|Virtuális magok|1|2|4|8|16|24|
|Memória (GB)|7|14|28|56|112|168|
|Oszlopcentrikus támogatása|Igen|Igen|Igen|Igen|Igen|Igen|
|Memórián belüli online Tranzakciófeldolgozási tárhely (GB)|–|N/A|N/A|N/A|N/A|–|
|Tárolási típus|Prémium () távtároló|Prémium () távtároló|Prémium () távtároló|Prémium () távtároló|Prémium () távtároló|Prémium () távtároló|
|Maximális adatok (GB)|512|756|1536|2048|3584|4096|
|Maximális naplófájlméret|154|227|461|614|1075|1229|
|A TempDB size(DB)|32|64|128|256|384|384|
|Cél IOPS (64 KB)|500|1000|2000|4000|7000|7000|
|I/O várakozási ideje (hozzávetőleges)|5-7 ms (írás)<br>5-10 ms (olvasni.)|5-7 ms (írás)<br>5-10 ms (olvasni.)|5-7 ms (írás)<br>5-10 ms (olvasni.)|5-7 ms (írás)<br>5-10 ms (olvasni.)|5-7 ms (írás)<br>5-10 ms (olvasni.)|5-7 ms (írás)<br>5-10 ms (olvasni.)|5-7 ms (írás)<br>5-10 ms (olvasni.)|
|Maximális párhuzamos munkavállalók (kérelmek)|210|420|840|1680|3360|5040|
|A maximális munkamenet engedélyezett|30000|30000|30000|30000|30000|30000|
|Maximális készlet sűrűség|100|200|500|500|500|500|
|Minimális és maximális rugalmas készlet kattintson-leáll|0, 0,25, 0,5, 1|0, 0,25, 0,5, 1, 2. régiója|0, 0,25, 0,5, 1, 2, 4|0, 0,25, 0,5, 1, 2, 4, 8|0, 0,25, 0,5, 1, 2, 4, 8, 16|0, 0,25, 0,5, 1, 2, 4, 8, 16, 24|
|Replikák száma|1|1|1|1|1|1|
|Több-AZ|–|N/A|N/A|N/A|N/A|–|
|Olvassa el a kibővített|–|N/A|N/A|N/A|N/A|–|
|Biztonsági másolatok tárolásának tartalmazza|1 X-adatbázis mérete|1 X-adatbázis mérete|1 X-adatbázis mérete|1 X-adatbázis mérete|1 X-adatbázis mérete|1 X-adatbázis mérete|
|||

#### <a name="generation-5-compute-platform"></a>5. generációs számítógépes platform
|Teljesítményszint|BC_Gen5_2|BC_Gen5_4|BC_Gen5_8|BC_Gen5_16|BC_Gen5_24|BC_Gen5_32|BC_Gen5_48|BC_Gen5_80|
|:--- | --: |--: |--: |--: |--: |--: |--: |--: |
|H/W létrehozása|5|5|5|5|5|5|5|5|
|Virtuális magok|2|4|8|16|24|32|48|80|
|Memória (GB)|11|22|44|88|132|176|264|440|
|Oszlopcentrikus támogatása|Igen|Igen|Igen|Igen|Igen|Igen|Igen|Igen|
|Memórián belüli online Tranzakciófeldolgozási tárhely (GB)|–|N/A|N/A|N/A|N/A|N/A|N/A|–|
|Tárolási típus|Prémium () távtároló|Prémium () távtároló|Prémium () távtároló|Prémium () távtároló|Prémium () távtároló|Prémium () távtároló|Prémium () távtároló|Prémium () távtároló|
|Maximális adatok (GB)|512|756|1536|2048|3072|4096|4096|4096|
|Maximális naplófájlméret|154|227|461|614|922|1229|1229|1229|
|A TempDB size(DB)|64|128|256|384|384|384|384|384|
|Cél IOPS (64 KB)|500|1000|2000|4000|6000|7000|7000|7000|
|I/O várakozási ideje (hozzávetőleges)|5-7 ms (írás)<br>5-10 ms (olvasni.)|5-7 ms (írás)<br>5-10 ms (olvasni.)|5-7 ms (írás)<br>5-10 ms (olvasni.)|5-7 ms (írás)<br>5-10 ms (olvasni.)|5-7 ms (írás)<br>5-10 ms (olvasni.)|5-7 ms (írás)<br>5-10 ms (olvasni.)|5-7 ms (írás)<br>5-10 ms (olvasni.)|5-7 ms (írás)<br>5-10 ms (olvasni.)|5-7 ms (írás)<br>5-10 ms (olvasni.)|
|Maximális párhuzamos munkavállalók (kérelmek)|210|420|840|1680|2520|3360|5040|8400
|A maximális munkamenet engedélyezett|30000|30000|30000|30000|30000|30000|30000|30000|
|Maximális készlet sűrűség|100|200|500|500|500|500|500|500|
|Minimális és maximális rugalmas készlet kattintson-leáll|0, 0,25, 0,5, 1, 2. régiója|0, 0,25, 0,5, 1, 2, 4|0, 0,25, 0,5, 1, 2, 4, 8|0, 0,25, 0,5, 1, 2, 4, 8, 16|0, 0,25, 0,5, 1, 2, 4, 8, 16, 24|0, 0,5, 1, 2, 4, 8, 16, 24, 32|0, 0,5, 1, 2, 4, 8, 16, 24, 32, 48|0, 0,5, 1, 2, 4, 8, 16, 24, 32, 48, 80|
|Replikák száma|1|1|1|1|1|1|1|1|
|Több-AZ|–|N/A|N/A|N/A|N/A|N/A|N/A|–|
|Olvassa el a kibővített|–|N/A|N/A|N/A|N/A|N/A|N/A|–|
|Biztonsági másolatok tárolásának tartalmazza|1 X-adatbázis mérete|1 X-adatbázis mérete|1 X-adatbázis mérete|1 X-adatbázis mérete|1 X-adatbázis mérete|1 X-adatbázis mérete|1 X-adatbázis mérete|1 X-adatbázis mérete|
|||

### <a name="business-critical-service-tier"></a>Üzleti kritikus szolgáltatási rétegben

#### <a name="generation-4-compute-platform"></a>4. generációs számítógépes platform
|Teljesítményszint|BC_Gen4_1|BC_Gen4_2|BC_Gen4_4|BC_Gen4_8|BC_Gen4_16|BC_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |
|H/W létrehozása|4|4|4|4|4|4|
|Virtuális magok|1|2|4|8|16|24|
|Memória (GB)|7|14|28|56|112|168|
|Oszlopcentrikus támogatása|Igen|Igen|Igen|Igen|Igen|Igen|
|Memórián belüli online Tranzakciófeldolgozási tárhely (GB)|1|2|4|8|20|36|
|Tárolási típus|Helyi SSD|Helyi SSD|Helyi SSD|Helyi SSD|Helyi SSD|Helyi SSD|
|Maximális adatok (GB)|1024|1024|1024|1024|1024|1024|
|Maximális naplófájlméret|307|307|307|307|307|307|
|A TempDB size(DB)|32|64|128|256|384|384|
|Cél IOPS (64 KB)|5000|10000|20000|40000|80000|120000|
|I/O várakozási ideje (hozzávetőleges)|1-2 ms (írás)<br>1-2 ms (olvasni.)|1-2 ms (írás)<br>1-2 ms (olvasni.)|1-2 ms (írás)<br>1-2 ms (olvasni.)|1-2 ms (írás)<br>1-2 ms (olvasni.)|1-2 ms (írás)<br>1-2 ms (olvasni.)|1-2 ms (írás)<br>1-2 ms (olvasni.)|
|Maximális párhuzamos munkavállalók (kérelmek)|210|420|840|1680|3360|5040|
|A maximális munkamenet engedélyezett|30000|30000|30000|30000|30000|30000|
|Maximális készlet sűrűség|–|50|100|100|100|100|
|Minimális és maximális rugalmas készlet kattintson-leáll|–|0, 0,25, 0,5, 1, 2. régiója|0, 0,25, 0,5, 1, 2, 4|0, 0,25, 0,5, 1, 2, 4, 8|0, 0,25, 0,5, 1, 2, 4, 8, 16|0, 0,25, 0,5, 1, 2, 4, 8, 16, 24|
|Több-AZ|Igen|Igen|Igen|Igen|Igen|Igen|
|Olvassa el a kibővített|Igen|Igen|Igen|Igen|Igen|Igen|
|Biztonsági másolatok tárolásának tartalmazza|1 X-adatbázis mérete|1 X-adatbázis mérete|1 X-adatbázis mérete|1 X-adatbázis mérete|1 X-adatbázis mérete|1 X-adatbázis mérete|
|||

#### <a name="generation-5-compute-platform"></a>5. generációs számítógépes platform
|Teljesítményszint|BC_Gen5_2|BC_Gen5_4|BC_Gen5_8|BC_Gen5_16|BC_Gen5_24|BC_Gen5_32|BC_Gen5_48|BC_Gen5_80|
|:--- | --: |--: |--: |--: |--: |--: |--: |--: |
|H/W létrehozása|5|5|5|5|5|5|5|5|
|Virtuális magok|2|4|8|16|24|48|64|80|
|Memória (GB)|11|22|44|88|132|176|264|440|
|Oszlopcentrikus támogatása|Igen|Igen|Igen|Igen|Igen|Igen|Igen|Igen|
|Memórián belüli online Tranzakciófeldolgozási tárhely (GB)|1.571|3,142|6.284|15.768|25.252|37.936|68.104|131.64|
|Tárolási típus|Helyi SSD|Helyi SSD|Helyi SSD|Helyi SSD|Helyi SSD|Helyi SSD|Helyi SSD|Helyi SSD|
|I/O várakozási ideje (hozzávetőleges)|1-2 ms (írás)<br>1-2 ms (olvasni.)|1-2 ms (írás)<br>1-2 ms (olvasni.)|1-2 ms (írás)<br>1-2 ms (olvasni.)|1-2 ms (írás)<br>1-2 ms (olvasni.)|1-2 ms (írás)<br>1-2 ms (olvasni.)|1-2 ms (írás)<br>1-2 ms (olvasni.)|1-2 ms (írás)<br>1-2 ms (olvasni.)|1-2 ms (írás)<br>1-2 ms (olvasni.)|
|Maximális adatok (GB)|1024|1024|1024|1024|2048|4096|4096|4096|
|Maximális naplófájlméret|307|307|307|307|614|1229|1229|1229|
|A TempDB size(DB)|64|128|256|384|384|384|384|384|
|Cél IOPS (64 KB)|5000|10000|20000|40000|60000|80000|120000|200000
|Maximális párhuzamos munkavállalók (kérelmek)|210|420|840|1680|2520|3360|5040|8400|
|A maximális munkamenet engedélyezett|30000|30000|30000|30000|30000|30000|30000|30000|
|Maximális készlet sűrűség|–|50|100|100|100|100|100|100|
|Minimális és maximális rugalmas készlet kattintson-leáll|–|0, 0,25, 0,5, 1, 2, 4|0, 0,25, 0,5, 1, 2, 4, 8|0, 0,25, 0,5, 1, 2, 4, 8, 16|0, 0,25, 0,5, 1, 2, 4, 8, 16, 24|0, 0,5, 1, 2, 4, 8, 16, 24, 32|0, 0,5, 1, 2, 4, 8, 16, 24, 32, 48|0, 0,5, 1, 2, 4, 8, 16, 24, 32, 48, 80|
|Több-AZ|Igen|Igen|Igen|Igen|Igen|Igen|Igen|Igen|
|Olvassa el a kibővített|Igen|Igen|Igen|Igen|Igen|Igen|Igen|Igen|
|Biztonsági másolatok tárolásának tartalmazza|1 X-adatbázis mérete|1 X-adatbázis mérete|1 X-adatbázis mérete|1 X-adatbázis mérete|1 X-adatbázis mérete|1 X-adatbázis mérete|1 X-adatbázis mérete|1 X-adatbázis mérete|
|||

Ha a rugalmas készlet összes vCores foglalt, a készlet minden egyes adatbázis egy számítási erőforrásokat a lekérdezések feldolgozásához nagyobb mennyiségű kap. Az SQL Database szolgáltatás egyenlő erőforrás-megosztást biztosít az adatbázisok között azáltal, hogy mindegyiküknek egyenlő szeleteket ad a számítási időből. A rugalmas készlet erőforrás-megosztás fürtjében ellenkező esetben az egyes adatbázis biztosítani, ha a vCore-k adatbázisonkénti minimális értéke nem nulla értékű erőforrás számú kívül van.

### <a name="database-properties-for-pooled-databases"></a>Készletezett adatbázisok adatbázis-tulajdonságai

A következő táblázat ismerteti a készletezett adatbázisok tulajdonságait.

| Tulajdonság | Leírás |
|:--- |:--- |
| Adatbázisonként maximális vCores |VCores, amely a készletben található összes adatbázis is használja, ha elérhető használat alapján a készlet más adatbázisok maximális számát. Adatbázisonként maximális vCores nincs adatbázis erőforrás növekvő. Ez a beállítás egy globális beállítás, amely a készletben található minden adatbázisra vonatkozik. Állítsa be a maximális vCores adatbázisonként elég nagy a adatbázis terhelése csúcsait kezelésére. Elvárható, hogy a szükségesnél valamivel nagyobb értéket adjon meg, mivel a készlet általában hullámzó használati mintákat feltételez az adatbázisokkal kapcsolatban, amelyekben az adatbázisok kihasználtsága nem egyszerre éri el a csúcsértéket.|
| Adatbázisonkénti minimális vCores |A készlet összes adatbázis garantált vCores minimális száma. Ez a beállítás egy globális beállítás, amely a készletben található minden adatbázisra vonatkozik. Az adatbázisonkénti minimális vCores 0 lehet beállítani, és egyben az alapértelmezett érték. Ez a tulajdonság értéke bárhol 0 és az átlagos vCores kihasználtsági adatbázisonként között. A termék a készlet és az adatbázisonkénti minimális vCores adatbázisok száma nem haladhatja meg a készletenként vCores.|
| Adatbázisonként maximális tárolási |Az adatbázis maximális korlátot adatbázis esetén a felhasználó által a készletben. Készletezett adatbázisok megoszthatja a lefoglalt tárolókészlet, így az adatbázis el lehet érni mérete legfeljebb maradt a kisebb tárolási verem és az adatbázis méretét. Maximális adatbázis mérete a naplófájlok méretét, az adatok hivatkozik, és nem tartalmazza a fájlok által elfoglalt hely. |
|||
 
## <a name="elastic-pool-change-storage-size"></a>A rugalmas készlet: tároló méretének módosítása

- A maximális méretkorlát legfeljebb tárolók kiépítésével: 
 - Standard szintű tárolást, az növeli vagy 10 GB-os lépésekben méretének csökkentése
 - Prémium szintű storage, az növeli vagy 250 GB-os lépésekben méretének csökkentése
- Egy rugalmas készlet tárolási növelésével vagy csökkentésével, a maximális méret használatával helyezhetők a [Azure-portálon](sql-database-elastic-pool.md#manage-elastic-pools-and-databases-using-the-azure-portal), [PowerShell](/powershell/module/azurerm.sql/set-azurermsqlelasticpool), a [Azure CLI](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_update), vagy a [ REST API-t](/rest/api/sql/elasticpools/update).
- Rugalmas készletek tároló árának érték a tároló és a szolgáltatási rétegben tárolási egységárát. Extra tároló árának részletekért lásd: [SQL Database – díjszabás](https://azure.microsoft.com/pricing/details/sql-database/).

## <a name="elastic-pool-change-vcores"></a>A rugalmas készlet: vCores módosítása

Növelheti vagy csökkentheti a teljesítményszintet igények erőforrást alapuló rugalmas készletek a [Azure-portálon](sql-database-elastic-pool.md#manage-elastic-pools-and-databases-using-the-azure-portal), [PowerShell](/powershell/module/azurerm.sql/set-azurermsqlelasticpool), a [Azure CLI](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_update), vagy a [REST API-t](/rest/api/sql/elasticpools/update).

- Amikor készletbe vCores rescaling, adatbázis-kapcsolatok röviden megszakadnak. Ez az a kívánt viselkedést eredményező beállítást, akkor fordul elő, amikor rescaling dtu-k (nem a készlet) egyetlen adatbázis. A telepítések időtartamát és kapcsolatok megszakadnak adatbázis rescaling műveletek során hatását a részletekért lásd: [Rescaling dtu-i egy önálló adatbázis](#single-database-change-storage-size). 
- Készlet vCores átméretezése idejét a készletben lévő összes adatbázisok által felhasznált tárterület teljes mennyisége függ. Általában a rescaling várakozási átlagolja 90 percig vagy / 100 GB-nál kisebb. Például ha használja a teljes lemezterület a készletben található összes adatbázis 200 GB-os, akkor a várt késését a készlet rescaling 3 óra vagy annál kisebb. Bizonyos esetekben a szabványos vagy Basic szint belül a rescaling várakozási függetlenül a használt terület mennyisége a öt perc lehet.
- Módosítsa a / adatbázis vagy a maximális vCores adatbázisonkénti minimális vCores időtartamot általában öt perc vagy kisebb.
- Amikor készletbe vCores downsizing, használt készlet mérete kisebb, mint a megengedett maximális méretét a célként megadott szolgáltatás réteg és a készlet vCores kell lennie.

## <a name="what-is-the-maximum-number-of-servers-and-databases"></a>Mi az, hogy a kiszolgálók és adatbázisok maximális számát?

| Maximum | Érték |
| :--- | :--- |
| Adatbázis kiszolgálónként | 5000 |
| Régiónként előfizetésenként kiszolgálók száma | 20 |
|||

> [!IMPORTANT]
> Mivel az adatbázisok számának megközelíti a felső határ az egyes kiszolgáló, a következő akkor fordulhat elő:
> <br> • Növelése késés futó lekérdezések a master adatbázisban.  Ez magában foglalja az erőforrás-kihasználtságának statisztikája sys.resource_stats például nézetek.
> <br> • A felügyeleti műveletek késés növelése és a portál nézőpontok, például az adatbázisok a kiszolgálón számbavétele megjelenítése.

## <a name="what-happens-when-database-and-elastic-pool-resource-limits-are-reached"></a>Mi történik az adatbázis és a rugalmas készlet erőforrás korlátok elérése után?

### <a name="compute-vcores"></a>Számítási (vCores)

(VCore kihasználtsági mérhető) adatbázis számítási mértéke túlságosan megnő, ha a késés növekedése lekérdezni, és még akkor is, időtúllépés is. Ilyen körülmények lekérdezések előfordulhat, hogy az a szolgáltatás nem helyezi várólistára, és biztosított erőforrások erőforrás-ként történő végrehajtásnak felszabadul.
Magas számítási kihasználtsági észlelt, amikor megoldás lehetőségek a következők:

- Az adatbázis vagy a rugalmas készlet biztosítani az adatbázisban több vCores teljesítményszintjének növelését. Lásd: [önálló adatbázist: cVcores módosítása](#single-database-change-vcores) és [rugalmas készlet: vCores módosítása](#elastic-pool-change-vcores).
- Minden egyes lekérdezés az erőforrás-használat csökkentésére lekérdezések optimalizálása. További információkért lásd: [lekérdezés hangolása/Hinting](sql-database-performance-guidance.md#query-tuning-and-hinting).

### <a name="storage"></a>Storage

Felhasznált lemezterület adatbázis mérete eléri a maximális méretkorlátot, adatbázis beszúrása és frissítések, amelyek az adatok méretének növelése sikertelen és elküld az ügyfélgépeknek egy [hibaüzenet](sql-database-develop-error-messages.md). Adatbázis-választja ki, és törli továbbra is sikeres.

Amikor magas lemezterület-kihasználás, amikor megoldás lehetőségek a következők:

- Az adatbázis vagy a rugalmas készlet vagy módosítsa a teljesítményszintet növeli a maximális tárolási maximális méretének növelését. Lásd: [SQL-adatbázis vCore-alapú erőforrás korlátok](sql-database-vcore-resource-limits.md).
- Ha az adatbázis rugalmas készlethez, majd azt is megteheti az adatbázis helyezheti át a készlet kívül, hogy annak tárolóhelyét nem megosztott a többi adatbázissal.

### <a name="sessions-and-workers-requests"></a>Munkamenetek és alkalmazottak (kérelmek) 

A munkamenetek és alkalmazottak maximális száma a szolgáltatási szint és a teljesítmény szint határozza meg. Új kérelmek azért lettek elutasítva, amikor munkamenet vagy munkavégző korlát elérésekor, és az ügyfelek hibaüzenetet kap. Során rendelkezésre álló kapcsolatok száma az alkalmazás által szabályozható, a száma párhuzamos munkavállalók gyakran nehezebben becsléséhez, és szabályozhatja. Ez különösen igaz csúcsterhelési időszakokat amikor adatbázis erőforrás gyűjtésének elérésekor és miatt hosszabb futó lekérdezések munkavállalók egymásra, során. 

Munkamenet vagy munkavégző magas kihasználtsága észlelt, amikor megoldás lehetőségek a következők:
- Az adatbázis vagy a rugalmas készlet szolgáltatási szint vagy a teljesítmény szint növelését. Lásd: [önálló adatbázist: tároló méretének módosítása](#single-database-change-storage-size), [egyetlen adatbázis: vCores módosítása](#single-database-change-vcores), [rugalmas készlet: tároló méretének módosítása](#elastic-pool-change-storage-size), és [rugalmas készlet: vCores módosítása ](#elastic-pool-change-vcores).
- Optimalizálás lekérdezések minden egyes lekérdezés az erőforrás-használat csökkentésére, ha nagyobb munkavégző kihasználtsági oka miatt a versengés a számítási erőforrásokat. További információkért lásd: [lekérdezés hangolása/Hinting](sql-database-performance-guidance.md#query-tuning-and-hinting).

## <a name="next-steps"></a>További lépések

- Lásd: [SQL-adatbázis – gyakori kérdések](sql-database-faq.md) gyakran feltett kérdésekre adott válaszokat.
- Általános Azure korlátozását kapcsolatos információkért lásd: [Azure-előfizetés és szolgáltatási korlátok, kvóták és megkötések](../azure-subscription-service-limits.md).
