---
title: Az Azure SQL Database Virtuálismag-alapú erőforráskorlátok – önálló adatbázisok |} A Microsoft Docs
description: Ezen a lapon azt ismerteti, hogy néhány gyakori Virtuálismag-alapú erőforráskorlátok egyetlen Azure SQL Database-adatbázishoz.
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: conceptual
ms.date: 07/16/2018
ms.author: carlrab
ms.openlocfilehash: df7633028b9bafee8f9938432c86c7d7a24e2699
ms.sourcegitcommit: e32ea47d9d8158747eaf8fee6ebdd238d3ba01f7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/17/2018
ms.locfileid: "39092630"
---
# <a name="azure-sql-database-vcore-based-purchasing-model-limits-for-a-single-database"></a>Az Azure SQL Database Virtuálismag-alapú vásárlási modell korlátok egy önálló adatbázis

Ez a cikk a részletes erőforráskorlátok nyújt a Virtuálismag-alapú vásárlási modell használatával Azure SQL Database önálló adatbázisok számára.

DTU-alapú vásárlási modell korlátozásairól lásd: [SQL Database DTU-alapú erőforráskorlátok](sql-database-dtu-resource-limits.md).

## <a name="single-database-storage-sizes-and-performance-levels"></a>Önálló adatbázis: tárterületet és teljesítményszintek

Az önálló adatbázisok számára az alábbi táblázatok bemutatják az egyes szolgáltatási csomagot és teljesítményszintet szinten egy önálló adatbázis rendelkezésre álló erőforrások. A szolgáltatási rétegben, a teljesítményszintet és a egy önálló adatbázis a tárolókapacitás is megadhatja a [az Azure portal](sql-database-servers-databases-manage.md#azure-portal-manage-logical-servers-and-databases), [Transact-SQL](sql-database-servers-databases-manage.md#transact-sql-manage-logical-servers-and-databases), [PowerShell](sql-database-servers-databases-manage.md#powershell-manage-logical-servers-and-databases), a [Az azure CLI](sql-database-servers-databases-manage.md#azure-cli-manage-logical-servers-and-databases), vagy a [REST API-val](sql-database-servers-databases-manage.md#rest-api-manage-logical-servers-and-databases).

### <a name="general-purpose-service-tier"></a>Általános célú szolgáltatásszint

#### <a name="generation-4-compute-platform"></a>4. generációs számítási platform
|Teljesítményszint|GP_Gen4_1|GP_Gen4_2|GP_Gen4_4|GP_Gen4_8|GP_Gen4_16|GP_Gen4_24
|:--- | --: |--: |--: |--: |--: |--: |
|H/W generálása|4|4|4|4|4|4|
|Virtuális magok|1|2|4|8|16|24|
|Memória (GB)|7|14|28|56|112|168|
|Oszlopcentrikus támogatása|Igen|Igen|Igen|Igen|Igen|Igen|
|Memóriabeli OLTP storage (GB)|–|N/A|N/A|N/A|N/A|–|
|Tárolási típus|(Távoli) prémium szintű Storage|(Távoli) prémium szintű Storage|(Távoli) prémium szintű Storage|(Távoli) prémium szintű Storage|(Távoli) prémium szintű Storage|(Távoli) prémium szintű Storage|
|IO-késés (becsült)|5 – 7 ms (írás)<br>5 – 10 ms (olvasás)|5 – 7 ms (írás)<br>5 – 10 ms (olvasás)|5 – 7 ms (írás)<br>5 – 10 ms (olvasás)|5 – 7 ms (írás)<br>5 – 10 ms (olvasás)|5 – 7 ms (írás)<br>5 – 10 ms (olvasás)|5 – 7 ms (írás)<br>5 – 10 ms (olvasás)|
|Maximális adatméret (GB)|1024|1024|1536|3072|4096|4096|
|Max. napló mérete|307|307|461|922|1229|1229|
|A TempDB size(DB)|32|64|128|256|384|384|
|Cél IOPS (64 KB)|500|1000|2000|4000|7000|7000|
|Egyidejű feldolgozók (kérelmek) maximális|200|400|800|1600|3200|4800|
|Maximális engedélyezett munkamenetek|30000|30000|30000|30000|30000|30000|
|Replikák száma|1|1|1|1|1|1|
|Több-AZ|–|N/A|N/A|N/A|N/A|–|000
|Felskálázás olvasása|–|N/A|N/A|N/A|N/A|–|
|Biztonsági mentési tárterület|1 X-adatbázis mérete|1 X-adatbázis mérete|1 X-adatbázis mérete|1 X-adatbázis mérete|1 X-adatbázis mérete|1 X-adatbázis mérete|
|||

#### <a name="generation-5-compute-platform"></a>5. generáció számítási platform
|Teljesítményszint|GP_Gen5_2|GP_Gen5_4|GP_Gen5_8|GP_Gen5_16|GP_Gen5_24|GP_Gen5_32|GP_Gen5_40| GP_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |--: |--: |
|H/W generálása|5|5|5|5|5|5|5|
|Virtuális magok|2|4|8|16|24|32|40|80|
|Memória (GB)|11|22|44|88|132|176|220|440|
|Oszlopcentrikus támogatása|Igen|Igen|Igen|Igen|Igen|Igen|Igen|Igen|
|Memóriabeli OLTP storage (GB)|–|N/A|N/A|N/A|N/A|N/A|N/A|–|
|Tárolási típus|(Távoli) prémium szintű Storage|(Távoli) prémium szintű Storage|(Távoli) prémium szintű Storage|(Távoli) prémium szintű Storage|(Távoli) prémium szintű Storage|(Távoli) prémium szintű Storage|(Távoli) prémium szintű Storage|(Távoli) prémium szintű Storage|
|IO-késés (becsült)|5 – 7 ms (írás)<br>5 – 10 ms (olvasás)|5 – 7 ms (írás)<br>5 – 10 ms (olvasás)|5 – 7 ms (írás)<br>5 – 10 ms (olvasás)|5 – 7 ms (írás)<br>5 – 10 ms (olvasás)|5 – 7 ms (írás)<br>5 – 10 ms (olvasás)|5 – 7 ms (írás)<br>5 – 10 ms (olvasás)|5 – 7 ms (írás)<br>5 – 10 ms (olvasás)|5 – 7 ms (írás)<br>5 – 10 ms (olvasás)|
|Maximális adatméret (GB)|1024|1024|1536|3072|4096|4096|4096|4096|
|Max. napló mérete|307|307|461|614|1229|1229|1229|1229|
|A TempDB size(DB)|64|128|256|384|384|384|384|384|
|Cél IOPS (64 KB)|500|1000|2000|4000|6000|7000|7000|7000|
|Egyidejű feldolgozók (kérelmek) maximális|200|400|800|1600|2400|3200|4000|8000|
|Maximális engedélyezett munkamenetek|30000|30000|30000|30000|30000|30000|30000|30000|
|Replikák száma|1|1|1|1|1|1|1|1|
|Több-AZ|–|N/A|N/A|N/A|N/A|N/A|N/A|–|
|Felskálázás olvasása|–|N/A|N/A|N/A|N/A|N/A|N/A|–|
|Biztonsági mentési tárterület|1 X-adatbázis mérete|1 X-adatbázis mérete|1 X-adatbázis mérete|1 X-adatbázis mérete|1 X-adatbázis mérete|1 X-adatbázis mérete|1 X-adatbázis mérete|1 X-adatbázis mérete|
|||

### <a name="business-critical-service-tier"></a>Üzleti kritikus fontosságú szolgáltatási szint

#### <a name="generation-4-compute-platform"></a>4. generációs számítási platform
|Teljesítményszint|BC_Gen4_1|BC_Gen4_2|BC_Gen4_4|BC_Gen4_8|BC_Gen4_16|BC_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |
|H/W generálása|4|4|4|4|4|4|
|Virtuális magok|1|2|4|8|16|24|
|Memória (GB)|7|14|28|56|112|168|
|Oszlopcentrikus támogatása|Igen|Igen|Igen|Igen|Igen|Igen|
|Memóriabeli OLTP storage (GB)|1|2|4|8|20|36|
|Tárolási típus|Helyi SSD|Helyi SSD|Helyi SSD|Helyi SSD|Helyi SSD|Helyi SSD|
|Maximális adatméret (GB)|1024|1024|1024|1024|1024|1024|
|Max. napló mérete|307|307|307|307|307|307|
|A TempDB size(DB)|32|64|128|256|384|384|
|Cél IOPS (64 KB)|5000|10000|20000|40000|80000|120000|
|IO-késés (becsült)|1 – 2 ms (írás)<br>1 – 2 ms (olvasás)|1 – 2 ms (írás)<br>1 – 2 ms (olvasás)|1 – 2 ms (írás)<br>1 – 2 ms (olvasás)|1 – 2 ms (írás)<br>1 – 2 ms (olvasás)|1 – 2 ms (írás)<br>1 – 2 ms (olvasás)|1 – 2 ms (írás)<br>1 – 2 ms (olvasás)|
|Egyidejű feldolgozók (kérelmek) maximális|200|400|800|1600|3200|4800|
|Maximális engedélyezett munkamenetek|30000|30000|30000|30000|30000|30000|
|Replikák száma|3|3|3|3|3|3|
|Több-AZ|–|N/A|N/A|N/A|N/A|–|
|Felskálázás olvasása|Igen|Igen|Igen|Igen|Igen|Igen|
|Biztonsági mentési tárterület|1 X-adatbázis mérete|1 X-adatbázis mérete|1 X-adatbázis mérete|1 X-adatbázis mérete|1 X-adatbázis mérete|1 X-adatbázis mérete|
|||

#### <a name="generation-5-compute-platform"></a>5. generáció számítási platform
|Teljesítményszint|BC_Gen5_2|BC_Gen5_4|BC_Gen5_8|BC_Gen5_16|BC_Gen5_24|BC_Gen5_32|BC_Gen5_40|BC_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |--: |--: |--: |--: |--: |--: |
|H/W generálása|5|5|5|5|5|5|5|5|
|Virtuális magok|2|4|8|16|24|32|40|80|
|Memória (GB)|11|22|44|88|132|176|220|440|
|Oszlopcentrikus támogatása|Igen|Igen|Igen|Igen|Igen|Igen|Igen|Igen|
|Memóriabeli OLTP storage (GB)|1.571|3,142|6.284|15.768|25.252|37.936|52.22|131.64|
|Tárolási típus|Helyi SSD|Helyi SSD|Helyi SSD|Helyi SSD|Helyi SSD|Helyi SSD|Helyi SSD|Helyi SSD|
|IO-késés (becsült)|1 – 2 ms (írás)<br>1 – 2 ms (olvasás)|1 – 2 ms (írás)<br>1 – 2 ms (olvasás)|1 – 2 ms (írás)<br>1 – 2 ms (olvasás)|1 – 2 ms (írás)<br>1 – 2 ms (olvasás)|1 – 2 ms (írás)<br>1 – 2 ms (olvasás)|1 – 2 ms (írás)<br>1 – 2 ms (olvasás)|1 – 2 ms (írás)<br>1 – 2 ms (olvasás)|1 – 2 ms (írás)<br>1 – 2 ms (olvasás)|
|Maximális adatméret (GB)|1024|1024|1024|1024|2048|4096|4096|4096|
|Max. napló mérete|307|307|307|307|614|1229|1229|1229|
|A TempDB size(DB)|64|128|256|384|384|384|384|384|
|Cél IOPS (64 KB)|5000|10000|20000|40000|60000|80000|100000|200000
|Egyidejű feldolgozók (kérelmek) maximális|200|400|800|1600|2400|3200|4000|8000|
|Maximális engedélyezett munkamenetek|30000|30000|30000|30000|30000|30000|30000|30000|
|Replikák száma|3|3|3|3|3|3|3|3|
|Több-AZ|–|N/A|N/A|N/A|N/A|N/A|N/A|–|
|Felskálázás olvasása|–|N/A|N/A|N/A|N/A|N/A|N/A|–|
|Biztonsági mentési tárterület|1 X-adatbázis mérete|1 X-adatbázis mérete|1 X-adatbázis mérete|1 X-adatbázis mérete|1 X-adatbázis mérete|1 X-adatbázis mérete|1 X-adatbázis mérete|1 X-adatbázis mérete|
|||

## <a name="next-steps"></a>További lépések

- Lásd: [SQL Database: gyakori kérdések](sql-database-faq.md) kapcsolatos gyakori kérdésekre adott válaszokat.
- Azure – általános korlátozások kapcsolatos információkért lásd: [Azure-előfizetés és a szolgáltatások korlátozásai, kvótái és megkötései](../azure-subscription-service-limits.md).
