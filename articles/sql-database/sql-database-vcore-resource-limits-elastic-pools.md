---
title: Az Azure SQL Database vCore-alapú erőforrás korlátok - rugalmas készletek |} Microsoft Docs
description: Ez a lap az Azure SQL Database rugalmas készletek néhány gyakori vCore-alapú erőforrás korlátairól ismerteti.
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: conceptual
ms.date: 06/20/2018
ms.author: carlrab
ms.openlocfilehash: 01f213c7cf5f6be3ef84601a50bb4455422faf22
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/21/2018
ms.locfileid: "36309917"
---
# <a name="azure-sql-database-vcore-based-purchasing-model-limits-for-elastic-pools-preview"></a>Az Azure SQL Database vCore alapú vásárlási modell korlátozhatja a rugalmas készletek (előzetes verzió)

A cikkben a részletes erőforrás-korlátozások az Azure SQL Database rugalmas készletek és a készletezett adatbázisait a vCore-alapú alapjául szolgáló vásárlási modell.

DTU-alapú vásárlási modell-korlátok, lásd: [SQL Database DTU-alapú erőforrás korlátok - rugalmas készletek](sql-database-dtu-resource-limits-elastic-pools.md).


## <a name="elastic-pool-storage-sizes-and-performance-levels"></a>A rugalmas készlet: tárterületet és teljesítményszintek

SQL rugalmas adatbáziskészletek az alábbi táblázatok bemutatják a minden egyes szolgáltatást és teljesítményszintet szinten elérhető erőforrások. A szolgáltatási rétegben, a teljesítményszintet és a tárolási összeg használatával állíthatja be a [Azure-portálon](sql-database-elastic-pool-manage.md#azure-portal-manage-elastic-pools-and-pooled-databases), [PowerShell](sql-database-elastic-pool-manage.md#powershell-manage-elastic-pools-and-pooled-databases), a [Azure CLI](sql-database-elastic-pool-manage.md#azure-cli-manage-elastic-pools-and-pooled-databases), vagy a [REST API](sql-database-elastic-pool-manage.md#rest-api-manage-elastic-pools-and-pooled-databases).

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
|Teljesítményszint|GP_Gen5_2|GP_Gen5_4|GP_Gen5_8|GP_Gen5_16|GP_Gen5_24|GP_Gen5_32|GP_Gen5_40|GP_Gen5_80|
|:--- | --: |--: |--: |--: |--: |--: |--: |--: |
|H/W létrehozása|5|5|5|5|5|5|5|5|
|Virtuális magok|2|4|8|16|24|32|40|80|
|Memória (GB)|11|22|44|88|132|176|220|440|
|Oszlopcentrikus támogatása|Igen|Igen|Igen|Igen|Igen|Igen|Igen|Igen|
|Memórián belüli online Tranzakciófeldolgozási tárhely (GB)|–|N/A|N/A|N/A|N/A|N/A|N/A|–|
|Tárolási típus|Prémium () távtároló|Prémium () távtároló|Prémium () távtároló|Prémium () távtároló|Prémium () távtároló|Prémium () távtároló|Prémium () távtároló|Prémium () távtároló|
|Maximális adatok (GB)|512|756|1536|2048|3072|4096|4096|4096|
|Maximális naplófájlméret|154|227|461|614|922|1229|1229|1229|
|A TempDB size(DB)|64|128|256|384|384|384|384|384|
|Cél IOPS (64 KB)|500|1000|2000|4000|6000|7000|7000|7000|
|I/O várakozási ideje (hozzávetőleges)|5-7 ms (írás)<br>5-10 ms (olvasni.)|5-7 ms (írás)<br>5-10 ms (olvasni.)|5-7 ms (írás)<br>5-10 ms (olvasni.)|5-7 ms (írás)<br>5-10 ms (olvasni.)|5-7 ms (írás)<br>5-10 ms (olvasni.)|5-7 ms (írás)<br>5-10 ms (olvasni.)|5-7 ms (írás)<br>5-10 ms (olvasni.)|5-7 ms (írás)<br>5-10 ms (olvasni.)|5-7 ms (írás)<br>5-10 ms (olvasni.)|
|Maximális párhuzamos munkavállalók (kérelmek)|210|420|840|1680|2520|3360|4200|8400
|A maximális munkamenet engedélyezett|30000|30000|30000|30000|30000|30000|30000|30000|
|Maximális készlet sűrűség|100|200|500|500|500|500|500|500|
|Minimális és maximális rugalmas készlet kattintson-leáll|0, 0,25, 0,5, 1, 2. régiója|0, 0,25, 0,5, 1, 2, 4|0, 0,25, 0,5, 1, 2, 4, 8|0, 0,25, 0,5, 1, 2, 4, 8, 16|0, 0,25, 0,5, 1, 2, 4, 8, 16, 24|0, 0,5, 1, 2, 4, 8, 16, 24, 32|0, 0,5, 1, 2, 4, 8, 16, 24, 32, 40|0, 0,5, 1, 2, 4, 8, 16, 24, 32, 40, 80|
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
|Replikák száma|3|3|3|3|3|3|
|Több-AZ|–|N/A|N/A|N/A|N/A|–|
|Olvassa el a kibővített|Igen|Igen|Igen|Igen|Igen|Igen|
|Biztonsági másolatok tárolásának tartalmazza|1 X-adatbázis mérete|1 X-adatbázis mérete|1 X-adatbázis mérete|1 X-adatbázis mérete|1 X-adatbázis mérete|1 X-adatbázis mérete|
|||

#### <a name="generation-5-compute-platform"></a>5. generációs számítógépes platform
|Teljesítményszint|BC_Gen5_2|BC_Gen5_4|BC_Gen5_8|BC_Gen5_16|BC_Gen5_24|BC_Gen5_32|BC_Gen5_40|BC_Gen5_80|
|:--- | --: |--: |--: |--: |--: |--: |--: |--: |
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
|Maximális párhuzamos munkavállalók (kérelmek)|210|420|840|1680|2520|3360|5040|8400|
|A maximális munkamenet engedélyezett|30000|30000|30000|30000|30000|30000|30000|30000|
|Maximális készlet sűrűség|–|50|100|100|100|100|100|100|
|Minimális és maximális rugalmas készlet kattintson-leáll|–|0, 0,25, 0,5, 1, 2, 4|0, 0,25, 0,5, 1, 2, 4, 8|0, 0,25, 0,5, 1, 2, 4, 8, 16|0, 0,25, 0,5, 1, 2, 4, 8, 16, 24|0, 0,5, 1, 2, 4, 8, 16, 24, 32|0, 0,5, 1, 2, 4, 8, 16, 24, 32, 40|0, 0,5, 1, 2, 4, 8, 16, 24, 32, 40, 80|
|Replikák száma|3|3|3|3|3|3|3|3|
|Több-AZ|–|N/A|N/A|N/A|N/A|N/A|N/A|–|
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
 
## <a name="next-steps"></a>További lépések

- Lásd: [SQL-adatbázis – gyakori kérdések](sql-database-faq.md) gyakran feltett kérdésekre adott válaszokat.
- Általános Azure korlátozását kapcsolatos információkért lásd: [Azure-előfizetés és szolgáltatási korlátok, kvóták és megkötések](../azure-subscription-service-limits.md).
