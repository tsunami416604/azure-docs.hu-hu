---
title: Az Azure SQL Database Virtuálismag-alapú erőforráskorlátok – rugalmas készletek |} A Microsoft Docs
description: Ez az oldal néhány gyakori Virtuálismag-alapú erőforráskorlátok az Azure SQL Database rugalmas készletek ismerteti.
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: conceptual
ms.date: 07/16/2018
ms.author: carlrab
ms.openlocfilehash: 5503ffaf8a429221a0a0730fc999cb7a90f43785
ms.sourcegitcommit: e32ea47d9d8158747eaf8fee6ebdd238d3ba01f7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/17/2018
ms.locfileid: "39092120"
---
# <a name="azure-sql-database-vcore-based-purchasing-model-limits-for-elastic-pools"></a>Az Azure SQL Database Virtuálismag-alapú vásárlási modell a rugalmas készletek korlátai

Ez a cikk a részletes erőforráskorlátok Azure SQL Database rugalmas készletek és a készletezett adatbázisokat, a Virtuálismag-alapú vásárlási modell használatával.

DTU-alapú vásárlási modell korlátozásairól lásd: [SQL Database DTU-alapú erőforráskorlátok – rugalmas készletek](sql-database-dtu-resource-limits-elastic-pools.md).


## <a name="elastic-pool-storage-sizes-and-performance-levels"></a>Rugalmas készlet: tárterületet és teljesítményszintek

Az SQL Database rugalmas készletek esetén az alábbi táblázatok bemutatják az egyes szolgáltatási csomagot és teljesítményszintet szinten elérhető erőforrásokat. A szolgáltatási rétegben, a teljesítményszint és a tárolási összeg használatával beállíthatja a [az Azure portal](sql-database-elastic-pool-manage.md#azure-portal-manage-elastic-pools-and-pooled-databases), [PowerShell](sql-database-elastic-pool-manage.md#powershell-manage-elastic-pools-and-pooled-databases), a [Azure CLI-vel](sql-database-elastic-pool-manage.md#azure-cli-manage-elastic-pools-and-pooled-databases), vagy a [REST API](sql-database-elastic-pool-manage.md#rest-api-manage-elastic-pools-and-pooled-databases).

> [!NOTE]
> A rugalmas készletek az egyes adatbázisok erőforráskorlátok megegyeznek a általában a teljesítményszint rendelkező készletek kívül önálló adatbázisok. Például a maximális egyidejű feldolgozók GP_Gen4_1 adatbázishoz 200 feldolgozók. Tehát egy adatbázis GP_Gen4_1 készletben a maximális egyidejű feldolgozók egyben 200 feldolgozók. Vegye figyelembe, GP_Gen4_1 készlet egyidejű feldolgozók száma 210.

### <a name="general-purpose-service-tier"></a>Általános célú szolgáltatásszint

#### <a name="generation-4-compute-platform"></a>4. generációs számítási platform
|Teljesítményszint|GP_Gen4_1|GP_Gen4_2|GP_Gen4_4|GP_Gen4_8|GP_Gen4_16|GP_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |
|H/W generálása|4|4|4|4|4|4|
|Virtuális magok|1|2|4|8|16|24|
|Memória (GB)|7|14|28|56|112|168|
|Oszlopcentrikus támogatása|Igen|Igen|Igen|Igen|Igen|Igen|
|Memóriabeli OLTP storage (GB)|–|N/A|N/A|N/A|N/A|–|
|Tárolási típus|(Távoli) prémium szintű Storage|(Távoli) prémium szintű Storage|(Távoli) prémium szintű Storage|(Távoli) prémium szintű Storage|(Távoli) prémium szintű Storage|(Távoli) prémium szintű Storage|
|Maximális adatméret (GB)|512|756|1536|2048|3584|4096|
|Max. napló mérete|154|227|461|614|1075|1229|
|A TempDB size(DB)|32|64|128|256|384|384|
|Cél IOPS (64 KB)|500|1000|2000|4000|7000|7000|
|IO-késés (becsült)|5 – 7 ms (írás)<br>5 – 10 ms (olvasás)|5 – 7 ms (írás)<br>5 – 10 ms (olvasás)|5 – 7 ms (írás)<br>5 – 10 ms (olvasás)|5 – 7 ms (írás)<br>5 – 10 ms (olvasás)|5 – 7 ms (írás)<br>5 – 10 ms (olvasás)|5 – 7 ms (írás)<br>5 – 10 ms (olvasás)|5 – 7 ms (írás)<br>5 – 10 ms (olvasás)|
|Egyidejű feldolgozók (kérelmek) maximális|210|420|840|1680|3360|5040|
|Maximális engedélyezett munkamenetek|30000|30000|30000|30000|30000|30000|
|Maximális készlet sűrűség|100|200|500|500|500|500|
|Minimális/maximális rugalmas készlet kattintson-leáll|0, 0,25, 0.5-ös, 1|0, 0,25, 0.5-ös, 1, 2|0, 0,25, 0.5-ös, 1, 2, 4|0, 0,25, 0.5-ös, 1, 2, 4, 8|0, 0,25, 0.5-ös, 1, 2, 4, 8, 16|0, 0,25, 0.5-ös, 1, 2, 4, 8, 16, 24|
|Replikák száma|1|1|1|1|1|1|
|Több-AZ|–|N/A|N/A|N/A|N/A|–|
|Felskálázás olvasása|–|N/A|N/A|N/A|N/A|–|
|Biztonsági mentési tárterület|1 X-adatbázis mérete|1 X-adatbázis mérete|1 X-adatbázis mérete|1 X-adatbázis mérete|1 X-adatbázis mérete|1 X-adatbázis mérete|
|||

#### <a name="generation-5-compute-platform"></a>5. generáció számítási platform
|Teljesítményszint|GP_Gen5_2|GP_Gen5_4|GP_Gen5_8|GP_Gen5_16|GP_Gen5_24|GP_Gen5_32|GP_Gen5_40|GP_Gen5_80|
|:--- | --: |--: |--: |--: |--: |--: |--: |--: |
|H/W generálása|5|5|5|5|5|5|5|5|
|Virtuális magok|2|4|8|16|24|32|40|80|
|Memória (GB)|11|22|44|88|132|176|220|440|
|Oszlopcentrikus támogatása|Igen|Igen|Igen|Igen|Igen|Igen|Igen|Igen|
|Memóriabeli OLTP storage (GB)|–|N/A|N/A|N/A|N/A|N/A|N/A|–|
|Tárolási típus|(Távoli) prémium szintű Storage|(Távoli) prémium szintű Storage|(Távoli) prémium szintű Storage|(Távoli) prémium szintű Storage|(Távoli) prémium szintű Storage|(Távoli) prémium szintű Storage|(Távoli) prémium szintű Storage|(Távoli) prémium szintű Storage|
|Maximális adatméret (GB)|512|756|1536|2048|3072|4096|4096|4096|
|Max. napló mérete|154|227|461|614|922|1229|1229|1229|
|A TempDB size(DB)|64|128|256|384|384|384|384|384|
|Cél IOPS (64 KB)|500|1000|2000|4000|6000|7000|7000|7000|
|IO-késés (becsült)|5 – 7 ms (írás)<br>5 – 10 ms (olvasás)|5 – 7 ms (írás)<br>5 – 10 ms (olvasás)|5 – 7 ms (írás)<br>5 – 10 ms (olvasás)|5 – 7 ms (írás)<br>5 – 10 ms (olvasás)|5 – 7 ms (írás)<br>5 – 10 ms (olvasás)|5 – 7 ms (írás)<br>5 – 10 ms (olvasás)|5 – 7 ms (írás)<br>5 – 10 ms (olvasás)|5 – 7 ms (írás)<br>5 – 10 ms (olvasás)|5 – 7 ms (írás)<br>5 – 10 ms (olvasás)|
|Egyidejű feldolgozók (kérelmek) maximális|210|420|840|1680|2520|3360|4200|8400
|Maximális engedélyezett munkamenetek|30000|30000|30000|30000|30000|30000|30000|30000|
|Maximális készlet sűrűség|100|200|500|500|500|500|500|500|
|Minimális/maximális rugalmas készlet kattintson-leáll|0, 0,25, 0.5-ös, 1, 2|0, 0,25, 0.5-ös, 1, 2, 4|0, 0,25, 0.5-ös, 1, 2, 4, 8|0, 0,25, 0.5-ös, 1, 2, 4, 8, 16|0, 0,25, 0.5-ös, 1, 2, 4, 8, 16, 24|0, 0.5-ös, 1, 2, 4, 8, 16, 24, 32|0, 0.5-ös, 1, 2, 4, 8, 16, 24, 32, 40|0, 0.5-ös, 1, 2, 4, 8, 16, 24, 32, 40, 80|
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
|Egyidejű feldolgozók (kérelmek) maximális|210|420|840|1680|3360|5040|
|Maximális engedélyezett munkamenetek|30000|30000|30000|30000|30000|30000|
|Maximális készlet sűrűség|–|50|100|100|100|100|
|Minimális/maximális rugalmas készlet kattintson-leáll|–|0, 0,25, 0.5-ös, 1, 2|0, 0,25, 0.5-ös, 1, 2, 4|0, 0,25, 0.5-ös, 1, 2, 4, 8|0, 0,25, 0.5-ös, 1, 2, 4, 8, 16|0, 0,25, 0.5-ös, 1, 2, 4, 8, 16, 24|
|Replikák száma|3|3|3|3|3|3|
|Több-AZ|–|N/A|N/A|N/A|N/A|–|
|Felskálázás olvasása|Igen|Igen|Igen|Igen|Igen|Igen|
|Biztonsági mentési tárterület|1 X-adatbázis mérete|1 X-adatbázis mérete|1 X-adatbázis mérete|1 X-adatbázis mérete|1 X-adatbázis mérete|1 X-adatbázis mérete|
|||

#### <a name="generation-5-compute-platform"></a>5. generáció számítási platform
|Teljesítményszint|BC_Gen5_2|BC_Gen5_4|BC_Gen5_8|BC_Gen5_16|BC_Gen5_24|BC_Gen5_32|BC_Gen5_40|BC_Gen5_80|
|:--- | --: |--: |--: |--: |--: |--: |--: |--: |
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
|Egyidejű feldolgozók (kérelmek) maximális|210|420|840|1680|2520|3360|5040|8400|
|Maximális engedélyezett munkamenetek|30000|30000|30000|30000|30000|30000|30000|30000|
|Maximális készlet sűrűség|–|50|100|100|100|100|100|100|
|Minimális/maximális rugalmas készlet kattintson-leáll|–|0, 0,25, 0.5-ös, 1, 2, 4|0, 0,25, 0.5-ös, 1, 2, 4, 8|0, 0,25, 0.5-ös, 1, 2, 4, 8, 16|0, 0,25, 0.5-ös, 1, 2, 4, 8, 16, 24|0, 0.5-ös, 1, 2, 4, 8, 16, 24, 32|0, 0.5-ös, 1, 2, 4, 8, 16, 24, 32, 40|0, 0.5-ös, 1, 2, 4, 8, 16, 24, 32, 40, 80|
|Replikák száma|3|3|3|3|3|3|3|3|
|Több-AZ|–|N/A|N/A|N/A|N/A|N/A|N/A|–|
|Felskálázás olvasása|Igen|Igen|Igen|Igen|Igen|Igen|Igen|Igen|
|Biztonsági mentési tárterület|1 X-adatbázis mérete|1 X-adatbázis mérete|1 X-adatbázis mérete|1 X-adatbázis mérete|1 X-adatbázis mérete|1 X-adatbázis mérete|1 X-adatbázis mérete|1 X-adatbázis mérete|
|||

Ha az összes virtuális mag a rugalmas készlet foglalt, a készletben lévő minden adatbázis ugyanannyi számítási erőforrásokat, amelyek a lekérdezések feldolgozásához kap. Az SQL Database szolgáltatás egyenlő erőforrás-megosztást biztosít az adatbázisok között azáltal, hogy mindegyiküknek egyenlő szeleteket ad a számítási időből. Mennyiségéhez garantált erőforrások minden egyes adatbázishoz. Ha a vcore magok adatbázisonkénti minimális értéke nem nulla értékre van állítva, a rugalmas készlet erőforrás-elosztást megosztást.

### <a name="database-properties-for-pooled-databases"></a>Készletezett adatbázisok adatbázis-tulajdonságai

A következő táblázat ismerteti a készletezett adatbázisok tulajdonságait.

| Tulajdonság | Leírás |
|:--- |:--- |
| Max. virtuális magok adatbázisonkénti |A készletben található adatbázisok mindegyike számára virtuális magok maximális számának felhasználhatja, ha elérhető a kihasználtság alapján más a készletben található adatbázisok által. Max. virtuális magok adatbázisonkénti nem garantálja az adatbázis-erőforrásokat. Ez a beállítás egy globális beállítás, amely a készletben található minden adatbázisra vonatkozik. Állítsa be elég nagy csúcsokkal is elbírjon az adatbázis-kihasználtsági adatbázisonkénti maximális virtuális maggal. Elvárható, hogy a szükségesnél valamivel nagyobb értéket adjon meg, mivel a készlet általában hullámzó használati mintákat feltételez az adatbázisokkal kapcsolatban, amelyekben az adatbázisok kihasználtsága nem egyszerre éri el a csúcsértéket.|
| Az adatbázisonkénti minimális magok |Minimális számú virtuális magot kapnak a készletben található adatbázisok mindegyike számára garantált. Ez a beállítás egy globális beállítás, amely a készletben található minden adatbázisra vonatkozik. A minimális virtuális magok adatbázisonkénti 0 értékre lehet beállítani, és egyben az alapértelmezett érték. Ez a tulajdonság értéke bárhol 0 és az átlagos virtuális magok adatbázisonkénti felhasználási között. A termék, a készlet és a minimális virtuális magok adatbázisonkénti adatbázisok száma nem haladhatja meg a virtuális magok száma készletenként.|
| Maximális tárterület adatbázisonként |A készletben található adatbázis a felhasználó által beállított adatbázis maximális méretét. Készletezett adatbázisok osztoznak a készlettárolón lefoglalt, elérheti egy adatbázis mérete korlátozott, így a kisebb fennmaradó készletéből, storage és az adatbázis méretét. Az adatbázis maximális mérete az adatfájlok maximális méretére vonatkozik, és nem tartalmazza a naplófájlok által használt területet. |
|||
 
## <a name="next-steps"></a>További lépések

- Lásd: [SQL Database: gyakori kérdések](sql-database-faq.md) kapcsolatos gyakori kérdésekre adott válaszokat.
- Azure – általános korlátozások kapcsolatos információkért lásd: [Azure-előfizetés és a szolgáltatások korlátozásai, kvótái és megkötései](../azure-subscription-service-limits.md).
