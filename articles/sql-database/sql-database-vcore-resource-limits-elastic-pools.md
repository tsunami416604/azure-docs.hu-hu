---
title: Az Azure SQL Database Virtuálismag-alapú erőforráskorlátok – rugalmas készletek |} A Microsoft Docs
description: Ez az oldal néhány gyakori Virtuálismag-alapú erőforráskorlátok az Azure SQL Database rugalmas készletek ismerteti.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: carlrab
manager: craigg
ms.date: 03/06/2019
ms.openlocfilehash: a1e76aafa271ff021517c3d06c3c6e02103413d3
ms.sourcegitcommit: dd1a9f38c69954f15ff5c166e456fda37ae1cdf2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57571473"
---
# <a name="resource-limits-for-elastic-pools-using-the-vcore-based-purchasing-model-limits"></a>Rugalmas készletek használata a Virtuálismag-alapú vásárlási modell korlátok erőforráskorlátok

Ez a cikk a részletes erőforráskorlátok Azure SQL Database rugalmas készletek és a készletezett adatbázisokat, a Virtuálismag-alapú vásárlási modell használatával.

DTU-alapú vásárlási modell korlátozásairól lásd: [SQL Database DTU-alapú erőforráskorlátok – rugalmas készletek](sql-database-dtu-resource-limits-elastic-pools.md).

> [!IMPORTANT]
> Bizonyos körülmények között szükség lehet az adatbázis nem használt terület felszabadítását zsugorítani. További információkért lásd: [kezelése az Azure SQL Database területe](sql-database-file-space-management.md).

A szolgáltatási rétegben, a számítási méretét és a tárolási mennyiséget használatával beállíthatja a [az Azure portal](sql-database-elastic-pool-manage.md#azure-portal-manage-elastic-pools-and-pooled-databases), [PowerShell](sql-database-elastic-pool-manage.md#powershell-manage-elastic-pools-and-pooled-databases), a [Azure CLI-vel](sql-database-elastic-pool-manage.md#azure-cli-manage-elastic-pools-and-pooled-databases), vagy a [REST API](sql-database-elastic-pool-manage.md#rest-api-manage-elastic-pools-and-pooled-databases).

> [!IMPORTANT]
> Skálázás útmutatást és szempontokat, lásd: [rugalmas készlet méretezése](sql-database-elastic-pool-scale.md)
> [!NOTE]
> A rugalmas készletek az egyes adatbázisok erőforráskorlátok kívül címkészleteket, amelyek azonos az önálló adatbázisokat azonos számítási mérete általában. Például a maximális egyidejű feldolgozók GP_Gen4_1 adatbázishoz 200 feldolgozók. Tehát egy adatbázis GP_Gen4_1 készletben a maximális egyidejű feldolgozók egyben 200 feldolgozók. Vegye figyelembe, GP_Gen4_1 készlet egyidejű feldolgozók száma 210.

## <a name="general-purpose-service-tier-storage-sizes-and-compute-sizes"></a>Általános célú szolgáltatásszint: Tárterületet és számítási méretek

### <a name="general-purpose-service-tier-generation-4-compute-platform-part-1"></a>Általános célú szolgáltatásszint: 4. generációs számítási platform (1. rész)

|Számítási mérete|GP_Gen4_1|GP_Gen4_2|GP_Gen4_3|GP_Gen4_4|GP_Gen4_5|GP_Gen4_6
|:--- | --: |--: |--: |--: |--: |--: |
|H/W generálása|4|4|4|4|4|4|
|Virtuális magok|1|2|3|4|5|6|
|Memória (GB)|7|14|21|28|35|42|
|Oszlopcentrikus támogatása|Igen|Igen|Igen|Igen|Igen|Igen|
|Memóriabeli OLTP storage (GB)|–|N/A|N/A|N/A|N/A|–|
|Maximális adatméret (GB)|512|756|756|1536|1536|1536|
|Max. napló mérete|154|227|227|461|461|461|
|A TempDB mérete (GB)|32|64|96|128|160|192|
|Tárolási típus|Premium (Remote) Storage|Premium (Remote) Storage|Premium (Remote) Storage|Premium (Remote) Storage|Premium (Remote) Storage|Premium (Remote) Storage|
|IO-késés (becsült)|5 – 7 ms (írás)<br>5 – 10 ms (olvasás)|5 – 7 ms (írás)<br>5 – 10 ms (olvasás)|5 – 7 ms (írás)<br>5 – 10 ms (olvasás)|5 – 7 ms (írás)<br>5 – 10 ms (olvasás)|5 – 7 ms (írás)<br>5 – 10 ms (olvasás)|5 – 7 ms (írás)<br>5 – 10 ms (olvasás)|
|Cél IOPS (64 KB)|500|1000|1500|2000|2500|3000|
|Napló Sebességhatár (MB/s)|2.5|5|7.5|10|12.5|15|
|Egyidejű feldolgozók maximális száma készletenként (kérelmek) * |210|420|630|840|1050|1260|
|Egyidejű bejelentkezések maximális száma készletenként * |210|420|630|840|1050|1260|
|Maximális engedélyezett munkamenetek|30000|30000|30000|30000|30000|30000|
|Adatbázisok maximális száma készletenként|100|200|300|500|500|500|
|Az adatbázisonkénti minimális/maximális rugalmas készlet virtuális mag választási lehetőségek|0, 0.25, 0.5, 1|0, 0.25, 0.5, 1, 2|0, 0.25, 0.5, 1...3|0, 0.25, 0.5, 1...4|0, 0.25, 0.5, 1...5|0, 0.25, 0.5, 1...6|
|Replikák száma|1|1|1|1|1|1|
|Több-AZ|–|N/A|N/A|N/A|N/A|–|
|Felskálázás olvasása|–|N/A|N/A|N/A|N/A|–|
|Biztonsági mentési tárterület|1 X-adatbázis mérete|1 X-adatbázis mérete|1 X-adatbázis mérete|1 X-adatbázis mérete|1 X-adatbázis mérete|1 X-adatbázis mérete|

\* A maximális egyidejű feldolgozók (kérelmek) bármely önálló adatbázis, lásd: [egyetlen adatbázis erőforráskorlátok](sql-database-vcore-resource-limits-single-databases.md). Például ha Gen5 és a maximális virtuális mag értéke a rugalmas készlet használ, 2, a maximális egyidejű feldolgozók válik a 200-as.  Ha az adatbázisonkénti maximális virtuális mag 0,5, majd egyidejű feldolgozók maximális érték: 50 mivel a Gen5 legfeljebb 100 egyidejű feldolgozók száma virtuális mag.  Egyéb maximális virtuális mag beállítások, amelyek kevesebb 1 virtuális mag adatbázisonként vagy kevesebb, az egyidejű feldolgozók maximális száma a hasonlóképpen átméretezése.

### <a name="general-purpose-service-tier-generation-4-compute-platform-part-2"></a>Általános célú szolgáltatásszint: 4. generációs számítási platform (2. rész)

|Számítási mérete|GP_Gen4_7|GP_Gen4_8|GP_Gen4_9|GP_Gen4_10|GP_Gen4_16|GP_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |
|H/W generálása|4|4|4|4|4|4|
|Virtuális magok|7|8|9|10|16|24|
|Memória (GB)|49|56|63|70|112|168|
|Oszlopcentrikus támogatása|Igen|Igen|Igen|Igen|Igen|Igen|
|Memóriabeli OLTP storage (GB)|–|N/A|N/A|N/A|N/A|–|
|Maximális adatméret (GB)|1536|2048|2048|2048|3584|4096|
|Max. napló mérete (GB)|461|614|614|614|1075|1229|
|A TempDB mérete (GB)|224|256|288|320|384|384|
|Tárolási típus|Premium (Remote) Storage|Premium (Remote) Storage|Premium (Remote) Storage|Premium (Remote) Storage|Premium (Remote) Storage|Premium (Remote) Storage|
|IO-késés (becsült)|5 – 7 ms (írás)<br>5 – 10 ms (olvasás)|5 – 7 ms (írás)<br>5 – 10 ms (olvasás)|5 – 7 ms (írás)<br>5 – 10 ms (olvasás)|5 – 7 ms (írás)<br>5 – 10 ms (olvasás)|5 – 7 ms (írás)<br>5 – 10 ms (olvasás)|5 – 7 ms (írás)<br>5 – 10 ms (olvasás)|
|Cél IOPS (64 KB)|3500|4000|4500|5000|7000|7000|
|Napló Sebességhatár (MB/s)|17.5|20|20|20|20|20|
|Egyidejű feldolgozók maximális száma készletenként (kérelmek) *|1470|1680|1890|2100|3360|5040|
|Max. egyidejű bejelentkezések készlet (kérelmek) *|1470|1680|1890|2100|3360|5040|
|Maximális engedélyezett munkamenetek|30000|30000|30000|30000|30000|30000|
|Adatbázisok maximális száma készletenként|200|500|500|500|500|500|
|Az adatbázisonkénti minimális/maximális rugalmas készlet virtuális mag választási lehetőségek|0, 0.25, 0.5, 1...7|0, 0.25, 0.5, 1...8|0, 0.25, 0.5, 1...9|0, 0.25, 0.5, 1...10|0, 0.25, 0.5, 1...10, 16|0, 0.25, 0.5, 1...10, 16, 24|
|Replikák száma|1|1|1|1|1|1|
|Több-AZ|–|N/A|N/A|N/A|N/A|–|
|Felskálázás olvasása|–|N/A|N/A|N/A|N/A|–|
|Biztonsági mentési tárterület|1 X-adatbázis mérete|1 X-adatbázis mérete|1 X-adatbázis mérete|1 X-adatbázis mérete|1 X-adatbázis mérete|1 X-adatbázis mérete|

\* A maximális egyidejű feldolgozók (kérelmek) bármely önálló adatbázis, lásd: [egyetlen adatbázis erőforráskorlátok](sql-database-vcore-resource-limits-single-databases.md). Például ha Gen5 és a maximális virtuális mag értéke a rugalmas készlet használ, 2, a maximális egyidejű feldolgozók válik a 200-as.  Ha az adatbázisonkénti maximális virtuális mag 0,5, majd egyidejű feldolgozók maximális érték: 50 mivel a Gen5 legfeljebb 100 egyidejű feldolgozók száma virtuális mag.  Egyéb maximális virtuális mag beállítások, amelyek kevesebb 1 virtuális mag adatbázisonként vagy kevesebb, az egyidejű feldolgozók maximális száma a hasonlóképpen átméretezése.

### <a name="general-purpose-service-tier-generation-5-compute-platform-part-1"></a>Általános célú szolgáltatásszint: 5. generáció számítási platform (1. rész)

|Számítási mérete|GP_Gen5_2|GP_Gen5_4|GP_Gen5_6|GP_Gen5_8|GP_Gen5_10|GP_Gen5_12|GP_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |--: |
|H/W generálása|5|5|5|5|5|5|5|
|Virtuális magok|2|4|6|8|10|12|14|
|Memória (GB)|10.2|20.4|30.6|40.8|51|61.2|71.4|
|Oszlopcentrikus támogatása|Igen|Igen|Igen|Igen|Igen|Igen|Igen|
|Memóriabeli OLTP storage (GB)|–|N/A|N/A|N/A|N/A|N/A|–|
|Maximális adatméret (GB)|512|756|756|1536|1536|1536|
|Max. napló mérete (GB)|154|227|227|461|461|461|461|
|A TempDB mérete (GB)|64|128|192|256|320|384|384|
|Tárolási típus|Premium (Remote) Storage|Premium (Remote) Storage|Premium (Remote) Storage|Premium (Remote) Storage|Premium (Remote) Storage|Premium (Remote) Storage|Premium (Remote) Storage|
|IO-késés (becsült)|5 – 7 ms (írás)<br>5 – 10 ms (olvasás)|5 – 7 ms (írás)<br>5 – 10 ms (olvasás)|5 – 7 ms (írás)<br>5 – 10 ms (olvasás)|5 – 7 ms (írás)<br>5 – 10 ms (olvasás)|5 – 7 ms (írás)<br>5 – 10 ms (olvasás)|5 – 7 ms (írás)<br>5 – 10 ms (olvasás)|5 – 7 ms (írás)<br>5 – 10 ms (olvasás)|
|Cél IOPS (64 KB)|500|1000|1500|2000|2500|3000|3500|
|Napló Sebességhatár (MB/s)|2.5|56|7.5|10|12.5|15|17.5|
|Egyidejű feldolgozók maximális száma készletenként (kérelmek) *|210|420|630|840|1050|1260|1470|
|Egyidejű bejelentkezések maximális száma készletenként (kérelmek) *|210|420|630|840|1050|1260|1470|
|Maximális engedélyezett munkamenetek|30000|30000|30000|30000|30000|30000|30000|
|Adatbázisok maximális száma készletenként|200|500|500|500|500|500|500|
|Az adatbázisonkénti minimális/maximális rugalmas készlet virtuális mag választási lehetőségek|0, 0.25, 0.5, 1, 2|0, 0.25, 0.5, 1...4|0, 0.25, 0.5, 1...6|0, 0.25, 0.5, 1...8|0, 0.25, 0.5, 1...10|0, 0.25, 0.5, 1...12|0, 0.25, 0.5, 1...14|
|Replikák száma|1|1|1|1|1|1|1|
|Több-AZ|–|N/A|N/A|N/A|N/A|N/A|–|
|Felskálázás olvasása|–|N/A|N/A|N/A|N/A|N/A|–|
|Biztonsági mentési tárterület|1 X-adatbázis mérete|1 X-adatbázis mérete|1 X-adatbázis mérete|1 X-adatbázis mérete|1 X-adatbázis mérete|1 X-adatbázis mérete|1 X-adatbázis mérete|

\* A maximális egyidejű feldolgozók (kérelmek) bármely önálló adatbázis, lásd: [egyetlen adatbázis erőforráskorlátok](sql-database-vcore-resource-limits-single-databases.md). Például ha Gen5 és a maximális virtuális mag értéke a rugalmas készlet használ, 2, a maximális egyidejű feldolgozók válik a 200-as.  Ha az adatbázisonkénti maximális virtuális mag 0,5, majd egyidejű feldolgozók maximális érték: 50 mivel a Gen5 legfeljebb 100 egyidejű feldolgozók száma virtuális mag.  Egyéb maximális virtuális mag beállítások, amelyek kevesebb 1 virtuális mag adatbázisonként vagy kevesebb, az egyidejű feldolgozók maximális száma a hasonlóképpen átméretezése.

### <a name="general-purpose-service-tier-generation-5-compute-platform-part-2"></a>Általános célú szolgáltatásszint: 5. generáció számítási platform (2. rész)

|Számítási mérete|GP_Gen5_16|GP_Gen5_18|GP_Gen5_20|GP_Gen5_24|GP_Gen5_32|GP_Gen5_40|GP_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |--: |
|H/W generálása|5|5|5|5|5|5|5|
|Virtuális magok|16|18|20|24|32|40|80|
|Memória (GB)|81.6|91.8|102|122.4|163.2|204|408|
|Oszlopcentrikus támogatása|Igen|Igen|Igen|Igen|Igen|Igen|Igen|
|Memóriabeli OLTP storage (GB)|–|N/A|N/A|N/A|N/A|N/A|–|
|Maximális adatméret (GB)|2048|2048|3072|3072|4096|4096|4096|
|Max. napló mérete (GB)|614|614|922|922|1229|1229|1229|
|A TempDB mérete (GB)|384|384|384|384|384|384|384|
|Tárolási típus|Premium (Remote) Storage|Premium (Remote) Storage|Premium (Remote) Storage|Premium (Remote) Storage|Premium (Remote) Storage|Premium (Remote) Storage|Premium (Remote) Storage|
|IO-késés (becsült)|5 – 7 ms (írás)<br>5 – 10 ms (olvasás)|5 – 7 ms (írás)<br>5 – 10 ms (olvasás)|5 – 7 ms (írás)<br>5 – 10 ms (olvasás)|5 – 7 ms (írás)<br>5 – 10 ms (olvasás)|5 – 7 ms (írás)<br>5 – 10 ms (olvasás)|5 – 7 ms (írás)<br>5 – 10 ms (olvasás)|5 – 7 ms (írás)<br>5 – 10 ms (olvasás)|
|Cél IOPS (64 KB)|4000|4500|5000|6000|7000|7000|7000|
|Napló Sebességhatár (MB/s)|20|20|20|20|20|20|20|
|Egyidejű feldolgozók maximális száma készletenként (kérelmek) *|1680|1890|2100|2520|33600|4200|8400|
|Egyidejű bejelentkezések maximális száma készletenként (kérelmek) *|1680|1890|2100|2520|33600|4200|8400|
|Adatbázisok maximális száma készletenként|500|500|500|500|500|500|500|
|Az adatbázisonkénti minimális/maximális rugalmas készlet virtuális mag választási lehetőségek|0, 0.25, 0.5, 1...16|0, 0.25, 0.5, 1...18|0, 0.25, 0.5, 1...20|0, 0.25, 0.5, 1...20, 24|0, 0.25, 0.5, 1...20, 24, 32|0, 0.25, 0.5, 1...16, 24, 32, 40|0, 0.25, 0.5, 1...16, 24, 32, 40, 80|
|Replikák száma|1|1|1|1|1|1|1|
|Több-AZ|–|N/A|N/A|N/A|N/A|N/A|–|
|Felskálázás olvasása|–|N/A|N/A|N/A|N/A|N/A|–|
|Biztonsági mentési tárterület|1 X-adatbázis mérete|1 X-adatbázis mérete|1 X-adatbázis mérete|1 X-adatbázis mérete|1 X-adatbázis mérete|1 X-adatbázis mérete|1 X-adatbázis mérete|

\* A maximális egyidejű feldolgozók (kérelmek) bármely önálló adatbázis, lásd: [egyetlen adatbázis erőforráskorlátok](sql-database-vcore-resource-limits-single-databases.md). Például ha Gen5 és a maximális virtuális mag értéke a rugalmas készlet használ, 2, a maximális egyidejű feldolgozók válik a 200-as.  Ha az adatbázisonkénti maximális virtuális mag 0,5, majd egyidejű feldolgozók maximális érték: 50 mivel a Gen5 legfeljebb 100 egyidejű feldolgozók száma virtuális mag.  Egyéb maximális virtuális mag beállítások, amelyek kevesebb 1 virtuális mag adatbázisonként vagy kevesebb, az egyidejű feldolgozók maximális száma a hasonlóképpen átméretezése.

## <a name="business-critical-service-tier-storage-sizes-and-compute-sizes"></a>Üzleti kritikus fontosságú szolgáltatási szint: Tárterületet és számítási méretek

### <a name="business-critical-service-tier-generation-4-compute-platform-part-1"></a>Üzleti kritikus fontosságú szolgáltatási szint: 4. generációs számítási platform (1. rész)

|Számítási mérete|BC_Gen4_1|BC_Gen4_2|BC_Gen4_3|BC_Gen4_4|BC_Gen4_5|BC_Gen4_6|
|:--- | --: |--: |--: |--: |--: |--: |
|H/W generálása|4|4|4|4|4|4|
|Virtuális magok|1|2|3|4|5|6|
|Memória (GB)|7|14|21|28|35|42|
|Oszlopcentrikus támogatása|Igen|Igen|Igen|Igen|Igen|Igen|
|Memóriabeli OLTP storage (GB)|1|2|3|4|5|6|
|Tárolási típus|Helyi SSD|Helyi SSD|Helyi SSD|Helyi SSD|Helyi SSD|Helyi SSD|
|Maximális adatméret (GB)|650|650|650|650|650|650|
|Max. napló mérete (GB)|195|195|195|195|195|195|
|A TempDB mérete (GB)|32|64|96|128|160|192|
|IO-késés (becsült)|1 – 2 ms (írás)<br>1 – 2 ms (olvasás)|1 – 2 ms (írás)<br>1 – 2 ms (olvasás)|1 – 2 ms (írás)<br>1 – 2 ms (olvasás)|1 – 2 ms (írás)<br>1 – 2 ms (olvasás)|1 – 2 ms (írás)<br>1 – 2 ms (olvasás)|1 – 2 ms (írás)<br>1 – 2 ms (olvasás)|
|Cél IOPS (64 KB)|5000|10000|15 000|20000|25000|30000|
|Napló Sebességhatár (MB/s)|6|12|18|24|30|36|
|Egyidejű feldolgozók maximális száma készletenként (kérelmek) *|210|420|630|840|1050|1260|
|Egyidejű bejelentkezések maximális száma készletenként (kérelmek) *|210|420|630|840|1050|1260|
|Maximális engedélyezett munkamenetek|30000|30000|30000|30000|30000|30000|
|Adatbázisok maximális száma készletenként|Csak egyetlen adatbázisok támogatottak a számítási méret|50|100|100|100|100|
|Az adatbázisonkénti minimális/maximális rugalmas készlet virtuális mag választási lehetőségek|–|0, 0.25, 0.5, 1, 2|0, 0.25, 0.5, 1...3|0, 0.25, 0.5, 1...4|0, 0.25, 0.5, 1...5|0, 0.25, 0.5, 1...6|
|Replikák száma|4|4|4|4|4|4|
|Több-AZ|Igen|Igen|Igen|Igen|Igen|Igen|
|Felskálázás olvasása|Igen|Igen|Igen|Igen|Igen|Igen|
|Biztonsági mentési tárterület|1 X-adatbázis mérete|1 X-adatbázis mérete|1 X-adatbázis mérete|1 X-adatbázis mérete|1 X-adatbázis mérete|1 X-adatbázis mérete|

\* A maximális egyidejű feldolgozók (kérelmek) bármely önálló adatbázis, lásd: [egyetlen adatbázis erőforráskorlátok](sql-database-vcore-resource-limits-single-databases.md). Például ha Gen5 és a maximális virtuális mag értéke a rugalmas készlet használ, 2, a maximális egyidejű feldolgozók válik a 200-as.  Ha az adatbázisonkénti maximális virtuális mag 0,5, majd egyidejű feldolgozók maximális érték: 50 mivel a Gen5 legfeljebb 100 egyidejű feldolgozók száma virtuális mag.  Egyéb maximális virtuális mag beállítások, amelyek kevesebb 1 virtuális mag adatbázisonként vagy kevesebb, az egyidejű feldolgozók maximális száma a hasonlóképpen átméretezése.

### <a name="business-critical-service-tier-generation-4-compute-platform-part-2"></a>Üzleti kritikus fontosságú szolgáltatási szint: 4. generációs számítási platform (2. rész)

|Számítási mérete|BC_Gen4_7|BC_Gen4_8|BC_Gen4_9|BC_Gen4_10|BC_Gen4_16|BC_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |
|H/W generálása|4|4|4|4|4|4|
|Virtuális magok|7|8|9|10|16|24|
|Memória (GB)|81.6|91.8|102|122.4|163.2|204|408|
|Oszlopcentrikus támogatása|–|N/A|N/A|N/A|N/A|–|
|Memóriabeli OLTP storage (GB)|7|8|9.5|11|20|36|
|Tárolási típus|Helyi SSD|Helyi SSD|Helyi SSD|Helyi SSD|Helyi SSD|Helyi SSD|
|Maximális adatméret (GB)|650|650|650|650|1024|1024|
|Max. napló mérete (GB)|195|195|195|195|307|307|
|A TempDB mérete (GB)|224|256|288|320|384|384|
|IO-késés (becsült)|1 – 2 ms (írás)<br>1 – 2 ms (olvasás)|1 – 2 ms (írás)<br>1 – 2 ms (olvasás)|1 – 2 ms (írás)<br>1 – 2 ms (olvasás)|1 – 2 ms (írás)<br>1 – 2 ms (olvasás)|1 – 2 ms (írás)<br>1 – 2 ms (olvasás)|1 – 2 ms (írás)<br>1 – 2 ms (olvasás)|
|Cél IOPS (64 KB)|35000|40000|45000|50000|80000|120000|
|Napló Sebességhatár (MB/s)|
|Egyidejű feldolgozók maximális száma készletenként (kérelmek) *|1470|1680|1890|2100|3360|5040|
|Egyidejű bejelentkezések maximális száma készletenként (kérelmek) *|1470|1680|1890|2100|3360|5040|
|Maximális engedélyezett munkamenetek|30000|30000|30000|30000|30000|30000|
|Adatbázisok maximális száma készletenként|100|100|100|100|100|100|
|Az adatbázisonkénti minimális/maximális rugalmas készlet virtuális mag választási lehetőségek|0, 0.25, 0.5, 1...7|0, 0.25, 0.5, 1...8|0, 0.25, 0.5, 1...9|0, 0.25, 0.5, 1...10|0, 0.25, 0.5, 1...10, 16|0, 0.25, 0.5, 1...10, 16, 24|
|Replikák száma|4|4|4|4|4|4|
|Több-AZ|Igen|Igen|Igen|Igen|Igen|Igen|
|Felskálázás olvasása|Igen|Igen|Igen|Igen|Igen|Igen|
|Biztonsági mentési tárterület|1 X-adatbázis mérete|1 X-adatbázis mérete|1 X-adatbázis mérete|1 X-adatbázis mérete|1 X-adatbázis mérete|1 X-adatbázis mérete|

\* A maximális egyidejű feldolgozók (kérelmek) bármely önálló adatbázis, lásd: [egyetlen adatbázis erőforráskorlátok](sql-database-vcore-resource-limits-single-databases.md). Például ha Gen5 és a maximális virtuális mag értéke a rugalmas készlet használ, 2, a maximális egyidejű feldolgozók válik a 200-as.  Ha az adatbázisonkénti maximális virtuális mag 0,5, majd egyidejű feldolgozók maximális érték: 50 mivel a Gen5 legfeljebb 100 egyidejű feldolgozók száma virtuális mag.  Egyéb maximális virtuális mag beállítások, amelyek kevesebb 1 virtuális mag adatbázisonként vagy kevesebb, az egyidejű feldolgozók maximális száma a hasonlóképpen átméretezése.

#### <a name="business-critical-service-tier-generation-5-compute-platform-part-1"></a>Üzleti kritikus fontosságú szolgáltatási szint: 5. generáció számítási platform (1. rész)

|Számítási mérete|BC_Gen5_2|BC_Gen5_4|BC_Gen5_6|BC_Gen5_8|BC_Gen5_10|BC_Gen5_12|BC_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |--: |--: |--: |--: |--: |
|H/W generálása|5|5|5|5|5|5|5|
|Virtuális magok|2|4|6|8|10|12|14|
|Memória (GB)|10.2|20.4|30.6|40.8|51|61.2|71.4|
|Oszlopcentrikus támogatása|Igen|Igen|Igen|Igen|Igen|Igen|Igen|
|Memóriabeli OLTP storage (GB)|1.571|3.142|4.713|6.284|8.655|11.026|13.397|
|Maximális adatméret (GB)|1024|1024|1536|1536|1536|3072|3072|
|Max. napló mérete (GB)|307|307|307|461|461|922|922|
|A TempDB mérete (GB)|64|128|192|256|320|384|384|
|Tárolási típus|Helyi SSD|Helyi SSD|Helyi SSD|Helyi SSD|Helyi SSD|Helyi SSD|Helyi SSD|
|IO-késés (becsült)|1 – 2 ms (írás)<br>1 – 2 ms (olvasás)|1 – 2 ms (írás)<br>1 – 2 ms (olvasás)|1 – 2 ms (írás)<br>1 – 2 ms (olvasás)|1 – 2 ms (írás)<br>1 – 2 ms (olvasás)|1 – 2 ms (írás)<br>1 – 2 ms (olvasás)|1 – 2 ms (írás)<br>1 – 2 ms (olvasás)|1 – 2 ms (írás)<br>1 – 2 ms (olvasás)|
|Cél IOPS (64 KB)|5000|10000|15 000|20000|25000|30000|35000|
|Napló Sebességhatár (MB/s)|48|48|48|48|48|48|48|
|Egyidejű feldolgozók maximális száma készletenként (kérelmek) *|210|420|630|840|1050|1260|1470|
|Egyidejű bejelentkezések maximális száma készletenként (kérelmek) *|210|420|630|840|1050|1260|1470|
|Maximális engedélyezett munkamenetek|30000|30000|30000|30000|30000|30000|30000|
|Adatbázisok maximális száma készletenként|Csak egyetlen adatbázisok támogatottak a számítási méret|50|100|100|100|100|100|
|Az adatbázisonkénti minimális/maximális rugalmas készlet virtuális mag választási lehetőségek|–|0, 0.25, 0.5, 1...4|0, 0.25, 0.5, 1...6|0, 0.25, 0.5, 1...8|0, 0.25, 0.5, 1...10|0, 0.25, 0.5, 1...12|0, 0.25, 0.5, 1...14|
|Replikák száma|4|4|4|4|4|4|4|
|Több-AZ|Igen|Igen|Igen|Igen|Igen|Igen|
|Felskálázás olvasása|Igen|Igen|Igen|Igen|Igen|Igen|Igen|
|Biztonsági mentési tárterület|1 X-adatbázis mérete|1 X-adatbázis mérete|1 X-adatbázis mérete|1 X-adatbázis mérete|1 X-adatbázis mérete|1 X-adatbázis mérete|1 X-adatbázis mérete|

\* A maximális egyidejű feldolgozók (kérelmek) bármely önálló adatbázis, lásd: [egyetlen adatbázis erőforráskorlátok](sql-database-vcore-resource-limits-single-databases.md). Például ha Gen5 és a maximális virtuális mag értéke a rugalmas készlet használ, 2, a maximális egyidejű feldolgozók válik a 200-as.  Ha az adatbázisonkénti maximális virtuális mag 0,5, majd egyidejű feldolgozók maximális érték: 50 mivel a Gen5 legfeljebb 100 egyidejű feldolgozók száma virtuális mag.  Egyéb maximális virtuális mag beállítások, amelyek kevesebb 1 virtuális mag adatbázisonként vagy kevesebb, az egyidejű feldolgozók maximális száma a hasonlóképpen átméretezése.

#### <a name="business-critical-service-tier-generation-5-compute-platform-part-2"></a>Üzleti kritikus fontosságú szolgáltatási szint: 5. generáció számítási platform (2. rész)

|Számítási mérete|BC_Gen5_16|BC_Gen5_18|BC_Gen5_20|BC_Gen5_24|BC_Gen5_32|BC_Gen5_40|BC_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |--: |--: |--: |--: |--: |
|H/W generálása|5|5|5|5|5|5|5|
|Virtuális magok|16|18|20|24|32|40|80|
|Memória (GB)|81.6|91.8|102|122.4|163.2|204|408|
|Oszlopcentrikus támogatása|Igen|Igen|Igen|Igen|Igen|Igen|Igen|
|Memóriabeli OLTP storage (GB)|15.768|18.139|20.51|25.252|37.936|52.22|131.64|
|Maximális adatméret (GB)|3072|3072|3072|4096|4096|4096|4096|
|Max. napló mérete (GB)|922|922|922|1229|1229|1229|1229|
|A TempDB mérete (GB)|384|384|384|384|384|384|384|
|Tárolási típus|Helyi SSD|Helyi SSD|Helyi SSD|Helyi SSD|Helyi SSD|Helyi SSD|Helyi SSD|
|IO-késés (becsült)|1 – 2 ms (írás)<br>1 – 2 ms (olvasás)|1 – 2 ms (írás)<br>1 – 2 ms (olvasás)|1 – 2 ms (írás)<br>1 – 2 ms (olvasás)|1 – 2 ms (írás)<br>1 – 2 ms (olvasás)|1 – 2 ms (írás)<br>1 – 2 ms (olvasás)|1 – 2 ms (írás)<br>1 – 2 ms (olvasás)|1 – 2 ms (írás)<br>1 – 2 ms (olvasás)|
|Cél IOPS (64 KB)|40000|45000|50000|60000|80000|100000|200000|
|Napló Sebességhatár (MB/s)|48|48|48|48|48|48|48|
|Egyidejű feldolgozók maximális száma készletenként (kérelmek) *|1680|1890|2100|2520|3360|4200|8400|
|Egyidejű bejelentkezések maximális száma készletenként (kérelmek) *|1680|1890|2100|2520|3360|4200|8400|
|Maximális engedélyezett munkamenetek|30000|30000|30000|30000|30000|30000|30000|
|Adatbázisok maximális száma készletenként|100|100|100|100|100|100|100|
|Az adatbázisonkénti minimális/maximális rugalmas készlet virtuális mag választási lehetőségek|0, 0.25, 0.5, 1...16|0, 0.25, 0.5, 1...18|0, 0.25, 0.5, 1...20|0, 0.25, 0.5, 1...20, 24|0, 0.25, 0.5, 1...20, 24, 32|0, 0.25, 0.5, 1...20, 24, 32, 40|0, 0.25, 0.5, 1...20, 24, 32, 40, 80|
|Replikák száma|4|4|4|4|4|4|4|
|Több-AZ|Igen|Igen|Igen|Igen|Igen|Igen|Igen|
|Felskálázás olvasása|Igen|Igen|Igen|Igen|Igen|Igen|Igen|
|Biztonsági mentési tárterület|1 X-adatbázis mérete|1 X-adatbázis mérete|1 X-adatbázis mérete|1 X-adatbázis mérete|1 X-adatbázis mérete|1 X-adatbázis mérete|1 X-adatbázis mérete|

\* A maximális egyidejű feldolgozók (kérelmek) bármely önálló adatbázis, lásd: [egyetlen adatbázis erőforráskorlátok](sql-database-vcore-resource-limits-single-databases.md). Például ha Gen5 és a maximális virtuális mag értéke a rugalmas készlet használ, 2, a maximális egyidejű feldolgozók válik a 200-as.  Ha az adatbázisonkénti maximális virtuális mag 0,5, majd egyidejű feldolgozók maximális érték: 50 mivel a Gen5 legfeljebb 100 egyidejű feldolgozók száma virtuális mag.  Egyéb maximális virtuális mag beállítások, amelyek kevesebb 1 virtuális mag adatbázisonként vagy kevesebb, az egyidejű feldolgozók maximális száma a hasonlóképpen átméretezése.

Ha az összes virtuális mag a rugalmas készlet foglalt, a készletben lévő minden adatbázis ugyanannyi számítási erőforrásokat, amelyek a lekérdezések feldolgozásához kap. Az SQL Database szolgáltatás egyenlő erőforrás-megosztást biztosít az adatbázisok között azáltal, hogy mindegyiküknek egyenlő szeleteket ad a számítási időből. Mennyiségéhez garantált erőforrások minden egyes adatbázishoz. Ha a vcore magok adatbázisonkénti minimális értéke nem nulla értékre van állítva, a rugalmas készlet erőforrás-elosztást megosztást.

## <a name="database-properties-for-pooled-databases"></a>Készletezett adatbázisok adatbázis-tulajdonságai

A következő táblázat ismerteti a készletezett adatbázisok tulajdonságait.

| Tulajdonság | Leírás |
|:--- |:--- |
| Max. virtuális magok adatbázisonkénti |A készletben található adatbázisok mindegyike számára virtuális magok maximális számának felhasználhatja, ha elérhető a kihasználtság alapján más a készletben található adatbázisok által. Max. virtuális magok adatbázisonkénti nem garantálja az adatbázis-erőforrásokat. Ez a beállítás egy globális beállítás, amely a készletben található minden adatbázisra vonatkozik. Állítsa be elég nagy csúcsokkal is elbírjon az adatbázis-kihasználtsági adatbázisonkénti maximális virtuális maggal. Bizonyos fokú túlterhelt véglegesítése várható, mivel a készlet általában hullámzó használati mintákat adatbázisok feltételezi ahol minden adatbázis vannak nem egyidejűleg kiugró kihasználtságú.|
| Az adatbázisonkénti minimális magok |Minimális számú virtuális magot kapnak a készletben található adatbázisok mindegyike számára garantált. Ez a beállítás egy globális beállítás, amely a készletben található minden adatbázisra vonatkozik. A minimális virtuális magok adatbázisonkénti 0 értékre lehet beállítani, és egyben az alapértelmezett érték. Ez a tulajdonság értéke bárhol 0 és az átlagos virtuális magok adatbázisonkénti felhasználási között. A termék, a készlet és a minimális virtuális magok adatbázisonkénti adatbázisok száma nem haladhatja meg a virtuális magok száma készletenként.|
| Maximális tárterület adatbázisonként |A készletben található adatbázis a felhasználó által beállított adatbázis maximális méretét. Készletezett adatbázisok osztoznak a készlettárolón lefoglalt, elérheti egy adatbázis mérete korlátozott, így a kisebb fennmaradó készletéből, storage és az adatbázis méretét. Az adatbázis maximális mérete az adatfájlok maximális méretére vonatkozik, és nem tartalmazza a naplófájlok által használt területet. |
|||

## <a name="next-steps"></a>További lépések

- Önálló adatbázis erőforráskorlátok virtuális mag, lásd: [erőforráskorlátok és önálló adatbázisokat a Virtuálismag-alapú vásárlási modell használatával](sql-database-vcore-resource-limits-single-databases.md)
- Önálló adatbázis dtu-k erőforráskorlátok, lásd: [erőforráskorlátok és önálló adatbázisokat a DTU-alapú vásárlási modell használatával](sql-database-dtu-resource-limits-single-databases.md)
- Dtu-k erőforráskorlátok a rugalmas készletek, lásd: [erőforráskorlátok a rugalmas készletek a DTU-alapú vásárlási modell használatával](sql-database-dtu-resource-limits-elastic-pools.md)
- Erőforráskorlátok a felügyelt példányok, lásd: [felügyelt példány erőforráskorlátok](sql-database-managed-instance-resource-limits.md).
- Azure – általános korlátozások kapcsolatos információkért lásd: [Azure-előfizetés és a szolgáltatások korlátozásai, kvótái és megkötései](../azure-subscription-service-limits.md).
- Adatbázis-kiszolgáló erőforráskorlátok kapcsolatos információkért lásd: [az SQL Database-kiszolgálóhoz erőforráskorlátok áttekintése](sql-database-resource-limits-database-server.md) kapcsolatos korlátozásokat a kiszolgálók és az előfizetés szintjén.
