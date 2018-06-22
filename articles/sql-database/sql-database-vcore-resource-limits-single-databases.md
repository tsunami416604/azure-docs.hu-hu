---
title: Az Azure SQL Database vCore-alapú erőforrás korlátok - egyetlen adatbázis |} Microsoft Docs
description: Ez a lap néhány gyakori vCore-alapú erőforrás korlátot az Azure SQL-adatbázis egy adatbázis ismerteti.
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: conceptual
ms.date: 06/20/2018
ms.author: carlrab
ms.openlocfilehash: 1a14e1a7c50f458067491a8605a0518056ac0aa8
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/21/2018
ms.locfileid: "36309896"
---
# <a name="azure-sql-database-vcore-based-purchasing-model-limits-for-a-single-database-preview"></a>Az Azure SQL Database vCore alapú vásárlási modell korlátairól egy adatbázist (előzetes verzió)

A cikkben a részletes erőforrás-korlátozások az Azure SQL Database önálló adatbázisok a vCore-alapú alapjául szolgáló vásárlási modell használatával.

DTU-alapú vásárlási modell-korlátok, lásd: [SQL Database DTU-alapú erőforrás korlátok](sql-database-dtu-resource-limits.md).

## <a name="single-database-storage-sizes-and-performance-levels"></a>Önálló adatbázist: tárterületet és teljesítményszintek

Az önálló adatbázisok az alábbi táblázatokban minden szolgáltatás és teljesítményszintet szintjén egyszeri adatbázis rendelkezésre álló erőforrások. A szolgáltatási rétegben, teljesítményszintet és egy önálló adatbázis használatára vonatkozó mennyisége állíthatja be a [Azure-portálon](sql-database-servers-databases-manage.md#azure-portal-manage-logical-servers-and-databases), [Transact-SQL](sql-database-servers-databases-manage.md#transact-sql-manage-logical-servers-and-databases), [PowerShell](sql-database-servers-databases-manage.md#powershell-manage-logical-servers-and-databases), a [Azure CLI](sql-database-servers-databases-manage.md#azure-cli-manage-logical-servers-and-databases), vagy a [REST API-t](sql-database-servers-databases-manage.md#rest-api-manage-logical-servers-and-databases).

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
|Teljesítményszint|GP_Gen5_2|GP_Gen5_4|GP_Gen5_8|GP_Gen5_16|GP_Gen5_24|GP_Gen5_32|GP_Gen5_40| GP_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |--: |--: |
|H/W létrehozása|5|5|5|5|5|5|5|
|Virtuális magok|2|4|8|16|24|32|40|80|
|Memória (GB)|11|22|44|88|132|176|220|440|
|Oszlopcentrikus támogatása|Igen|Igen|Igen|Igen|Igen|Igen|Igen|Igen|
|Memórián belüli online Tranzakciófeldolgozási tárhely (GB)|–|N/A|N/A|N/A|N/A|N/A|N/A|–|
|Tárolási típus|Prémium () távtároló|Prémium () távtároló|Prémium () távtároló|Prémium () távtároló|Prémium () távtároló|Prémium () távtároló|Prémium () távtároló|Prémium () távtároló|
|I/O várakozási ideje (hozzávetőleges)|5-7 ms (írás)<br>5-10 ms (olvasni.)|5-7 ms (írás)<br>5-10 ms (olvasni.)|5-7 ms (írás)<br>5-10 ms (olvasni.)|5-7 ms (írás)<br>5-10 ms (olvasni.)|5-7 ms (írás)<br>5-10 ms (olvasni.)|5-7 ms (írás)<br>5-10 ms (olvasni.)|5-7 ms (írás)<br>5-10 ms (olvasni.)|5-7 ms (írás)<br>5-10 ms (olvasni.)|
|Maximális adatok (GB)|1024|1024|1536|3072|4096|4096|4096|4096|
|Maximális naplófájlméret|307|307|461|614|1229|1229|1229|1229|
|A TempDB size(DB)|64|128|256|384|384|384|384|384|
|Cél IOPS (64 KB)|500|1000|2000|4000|6000|7000|7000|7000|
|Maximális párhuzamos munkavállalók (kérelmek)|200|400|800|1600|2400|3200|4000|8000|
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
|Több-AZ|–|N/A|N/A|N/A|N/A|–|
|Olvassa el a kibővített|Igen|Igen|Igen|Igen|Igen|Igen|
|Biztonsági másolatok tárolásának tartalmazza|1 X-adatbázis mérete|1 X-adatbázis mérete|1 X-adatbázis mérete|1 X-adatbázis mérete|1 X-adatbázis mérete|1 X-adatbázis mérete|
|||

#### <a name="generation-5-compute-platform"></a>5. generációs számítógépes platform
|Teljesítményszint|BC_Gen5_2|BC_Gen5_4|BC_Gen5_8|BC_Gen5_16|BC_Gen5_24|BC_Gen5_32|BC_Gen5_40|BC_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |--: |--: |--: |--: |--: |--: |
|H/W létrehozása|5|5|5|5|5|5|5|5|
|Virtuális magok|2|4|8|16|24|32|40|80|
|Memória (GB)|11|22|44|88|132|176|220|440|
|Oszlopcentrikus támogatása|Igen|Igen|Igen|Igen|Igen|Igen|Igen|Igen|
|Memórián belüli online Tranzakciófeldolgozási tárhely (GB)|1.571|3,142|6.284|15.768|25.252|37.936|52.22|131.64|
|Tárolási típus|Helyi SSD|Helyi SSD|Helyi SSD|Helyi SSD|Helyi SSD|Helyi SSD|Helyi SSD|Helyi SSD|
|I/O várakozási ideje (hozzávetőleges)|1-2 ms (írás)<br>1-2 ms (olvasni.)|1-2 ms (írás)<br>1-2 ms (olvasni.)|1-2 ms (írás)<br>1-2 ms (olvasni.)|1-2 ms (írás)<br>1-2 ms (olvasni.)|1-2 ms (írás)<br>1-2 ms (olvasni.)|1-2 ms (írás)<br>1-2 ms (olvasni.)|1-2 ms (írás)<br>1-2 ms (olvasni.)|1-2 ms (írás)<br>1-2 ms (olvasni.)|
|Maximális adatok (GB)|1024|1024|1024|1024|2048|4096|4096|4096|
|Maximális naplófájlméret|307|307|307|307|614|1229|1229|1229|
|A TempDB size(DB)|64|128|256|384|384|384|384|384|
|Cél IOPS (64 KB)|5000|10000|20000|40000|60000|80000|100000|200000
|Maximális párhuzamos munkavállalók (kérelmek)|200|400|800|1600|2400|3200|4000|8000|
|A maximális munkamenet engedélyezett|30000|30000|30000|30000|30000|30000|30000|30000|
|Replikák száma|3|3|3|3|3|3|3|3|
|Több-AZ|–|N/A|N/A|N/A|N/A|N/A|N/A|–|
|Olvassa el a kibővített|–|N/A|N/A|N/A|N/A|N/A|N/A|–|
|Biztonsági másolatok tárolásának tartalmazza|1 X-adatbázis mérete|1 X-adatbázis mérete|1 X-adatbázis mérete|1 X-adatbázis mérete|1 X-adatbázis mérete|1 X-adatbázis mérete|1 X-adatbázis mérete|1 X-adatbázis mérete|
|||

## <a name="next-steps"></a>További lépések

- Lásd: [SQL-adatbázis – gyakori kérdések](sql-database-faq.md) gyakran feltett kérdésekre adott válaszokat.
- Általános Azure korlátozását kapcsolatos információkért lásd: [Azure-előfizetés és szolgáltatási korlátok, kvóták és megkötések](../azure-subscription-service-limits.md).
