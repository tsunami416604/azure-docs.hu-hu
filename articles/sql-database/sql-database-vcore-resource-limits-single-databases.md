---
title: Az Azure SQL Database Virtuálismag-alapú erőforráskorlátok – önálló adatbázisok |} A Microsoft Docs
description: Ezen a lapon azt ismerteti, hogy néhány gyakori Virtuálismag-alapú erőforráskorlátok egyetlen Azure SQL Database-adatbázishoz.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 04/22/2019
ms.openlocfilehash: ca29bfdb381c5cab0625a320679331c82f63c887
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67118074"
---
# <a name="resource-limits-for-single-databases-using-the-vcore-based-purchasing-model"></a>A Virtuálismag-alapú vásárlási modell segítségével az önálló adatbázisok számára erőforráskorlátok

Ez a cikk a részletes erőforráskorlátok nyújt a Virtuálismag-alapú vásárlási modell használatával Azure SQL Database önálló adatbázisok számára.

DTU-alapú vásárlási modell korlátok és a egy SQL Database-kiszolgáló önálló adatbázisokat, lásd: [erőforrás áttekintése korlátozza az SQL Database-kiszolgálón](sql-database-resource-limits-database-server.md).

> [!IMPORTANT]
> Bizonyos körülmények között szükség lehet az adatbázis nem használt terület felszabadítását zsugorítani. További információkért lásd: [kezelése az Azure SQL Database területe](sql-database-file-space-management.md).

A szolgáltatási rétegben, a számítási méretét és a egy önálló adatbázis a tárolókapacitás is megadhatja a [az Azure portal](sql-database-single-databases-manage.md#manage-an-existing-sql-database-server), [Transact-SQL](sql-database-single-databases-manage.md#transact-sql-manage-sql-database-servers-and-single-databases), [PowerShell](sql-database-single-databases-manage.md#powershell-manage-sql-database-servers-and-single-databases), a [ Az Azure CLI](sql-database-single-databases-manage.md#azure-cli-manage-sql-database-servers-and-single-databases), vagy a [REST API-val](sql-database-single-databases-manage.md#rest-api-manage-sql-database-servers-and-single-databases).

> [!IMPORTANT]
> Skálázás útmutatást és szempontokat, lásd: [a különálló adatbázisok](sql-database-single-database-scale.md).

## <a name="general-purpose-service-tier-storage-sizes-and-compute-sizes"></a>Általános célú szolgáltatásszint: Tárterületet és számítási méretek

### <a name="general-purpose-service-tier-generation-4-compute-platform-part-1"></a>Általános célú szolgáltatásszint: 4. generációs számítási platform (1. rész)

|Számítási mérete|GP_Gen4_1|GP_Gen4_2|GP_Gen4_3|GP_Gen4_4|GP_Gen4_5|GP_Gen4_6
|:--- | --: |--: |--: |--: |--: |--: |
|H/W generálása|4|4|4|4|4|4|
|Virtuális magok|1|2|3|4|5|6|
|Memória (GB)|7|14|21|28|35|42|
|Oszlopcentrikus támogatása|Igen|Igen|Igen|Igen|Igen|Igen|
|Memóriabeli OLTP storage (GB)|–|N/A|N/A|N/A|N/A|–|
|Maximális adatméret (GB)|1024|1024|1024|1536|1536|1536|
|Max. napló mérete (GB)|307|307|307|461|461|461|
|A TempDB mérete (GB)|32|64|96|128|160|192|
|Tárolási típus|Premium (Remote) Storage|Premium (Remote) Storage|Premium (Remote) Storage|Premium (Remote) Storage|Premium (Remote) Storage|Premium (Remote) Storage|
|IO-késés (becsült)|5 – 7 ms (írás)<br>5 – 10 ms (olvasás)|5 – 7 ms (írás)<br>5 – 10 ms (olvasás)|5 – 7 ms (írás)<br>5 – 10 ms (olvasás)|5 – 7 ms (írás)<br>5 – 10 ms (olvasás)|5 – 7 ms (írás)<br>5 – 10 ms (olvasás)|5 – 7 ms (írás)<br>5 – 10 ms (olvasás)|
|Cél IOPS (64 KB)|500|1000|1500|2000|2500|3000|
|Napló Sebességhatár (MB/s)|3.75|7.5|11.25|15|18.75|22.5|
|Egyidejű feldolgozók (kérelmek) maximális|200|400|600|800|1000|1200|
|Maximális engedélyezett munkamenetek|30000|30000|30000|30000|30000|30000|
|Replikák száma|1|1|1|1|1|1|
|Több-AZ|–|N/A|N/A|N/A|N/A|–|
|Felskálázás olvasása|–|N/A|N/A|N/A|N/A|–|
|Biztonsági mentési tárterület|1 X-adatbázis mérete|1 X-adatbázis mérete|1 X-adatbázis mérete|1 X-adatbázis mérete|1 X-adatbázis mérete|1 X-adatbázis mérete|

### <a name="general-purpose-service-tier-generation-4-compute-platform-part-2"></a>Általános célú szolgáltatásszint: 4. generációs számítási platform (2. rész)

|Számítási mérete|GP_Gen4_7|GP_Gen4_8|GP_Gen4_9|GP_Gen4_10|GP_Gen4_16|GP_Gen4_24
|:--- | --: |--: |--: |--: |--: |--: |
|H/W generálása|4|4|4|4|4|4|
|Virtuális magok|7|8|9|10|16|24|
|Memória (GB)|49|56|63|70|112|168|
|Oszlopcentrikus támogatása|Igen|Igen|Igen|Igen|Igen|Igen|
|Memóriabeli OLTP storage (GB)|–|N/A|N/A|N/A|N/A|–|
|Maximális adatméret (GB)|1536|3072|3072|3072|4096|4096|
|Max. napló mérete (GB)|461|922|922|922|1229|1229|
|A TempDB mérete (GB)|224|256|288|320|384|384|
|Tárolási típus|Premium (Remote) Storage|Premium (Remote) Storage|Premium (Remote) Storage|Premium (Remote) Storage|Premium (Remote) Storage|Premium (Remote) Storage|
|IO-késés (becsült)|5 – 7 ms (írás)<br>5 – 10 ms (olvasás)|5 – 7 ms (írás)<br>5 – 10 ms (olvasás)|5 – 7 ms (írás)<br>5 – 10 ms (olvasás)|5 – 7 ms (írás)<br>5 – 10 ms (olvasás)|5 – 7 ms (írás)<br>5 – 10 ms (olvasás)|5 – 7 ms (írás)<br>5 – 10 ms (olvasás)
|Cél IOPS (64 KB)|3500|4000|4500|5000|7000|7000|
|Napló Sebességhatár (MB/s)|26.25|30|30|30|30|30|
|Egyidejű feldolgozók (kérelmek) maximális|1400|1600|1800|2000|3200|4800|
|Maximális engedélyezett munkamenetek|30000|30000|30000|30000|30000|30000|
|Replikák száma|1|1|1|1|1|1|
|Több-AZ|–|N/A|N/A|N/A|N/A|–|
|Felskálázás olvasása|–|N/A|N/A|N/A|N/A|–|
|Biztonsági mentési tárterület|1 X-adatbázis mérete|1 X-adatbázis mérete|1 X-adatbázis mérete|1 X-adatbázis mérete|1 X-adatbázis mérete|1 X-adatbázis mérete|

### <a name="general-purpose-service-tier-generation-5-compute-platform-part-1"></a>Általános célú szolgáltatásszint: 5. generáció számítási platform (1. rész)

|Számítási mérete|GP_Gen5_2|GP_Gen5_4|GP_Gen5_6|GP_Gen5_8|GP_Gen5_10|GP_Gen5_12|GP_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|H/W generálása|5|5|5|5|5|5|5|
|Virtuális magok|2|4|6|8|10|12|14|
|Memória (GB)|10.2|20.4|30.6|40.8|51|61.2|71.4|
|Oszlopcentrikus támogatása|Igen|Igen|Igen|Igen|Igen|Igen|Igen|
|Memóriabeli OLTP storage (GB)|–|N/A|N/A|N/A|N/A|N/A|–|
|Maximális adatméret (GB)|1024|1024|1536|1536|1536|3072|3072|
|Max. napló mérete (GB)|307|307|307|461|461|461|461|
|A TempDB mérete (GB)|64|128|192|256|320|384|384|
|Tárolási típus|Premium (Remote) Storage|Premium (Remote) Storage|Premium (Remote) Storage|Premium (Remote) Storage|Premium (Remote) Storage|Premium (Remote) Storage|Premium (Remote) Storage|
|IO-késés (becsült)|5 – 7 ms (írás)<br>5 – 10 ms (olvasás)|5 – 7 ms (írás)<br>5 – 10 ms (olvasás)|5 – 7 ms (írás)<br>5 – 10 ms (olvasás)|5 – 7 ms (írás)<br>5 – 10 ms (olvasás)|5 – 7 ms (írás)<br>5 – 10 ms (olvasás)|5 – 7 ms (írás)<br>5 – 10 ms (olvasás)|5 – 7 ms (írás)<br>5 – 10 ms (olvasás)|
|Cél IOPS (64 KB)|1000|2000|3000|4000|5000|6000|7000|
|Napló Sebességhatár (MB/s)|3.75|7.5|11.25|15|18.75|22.5|26.25|
|Egyidejű feldolgozók (kérelmek) maximális|200|400|600|800|1000|1200|1400|
|Maximális engedélyezett munkamenetek|30000|30000|30000|30000|30000|30000|30000|
|Replikák száma|1|1|1|1|1|1|1|
|Több-AZ|–|N/A|N/A|N/A|N/A|N/A|–|
|Felskálázás olvasása|–|N/A|N/A|N/A|N/A|N/A|–|
|Biztonsági mentési tárterület|1 X-adatbázis mérete|1 X-adatbázis mérete|1 X-adatbázis mérete|1 X-adatbázis mérete|1 X-adatbázis mérete|1 X-adatbázis mérete|1 X-adatbázis mérete|

### <a name="general-purpose-service-tier-generation-5-compute-platform-part-2"></a>Általános célú szolgáltatásszint: 5. generáció számítási platform (2. rész)

|Számítási mérete|GP_Gen5_16|GP_Gen5_18|GP_Gen5_20|GP_Gen5_24|GP_Gen5_32|GP_Gen5_40|GP_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|H/W generálása|5|5|5|5|5|5|5|
|Virtuális magok|16|18|20|24|32|40|80|
|Memória (GB)|81.6|91.8|102|122.4|163.2|204|408|
|Oszlopcentrikus támogatása|Igen|Igen|Igen|Igen|Igen|Igen|Igen|
|Memóriabeli OLTP storage (GB)|–|N/A|N/A|N/A|N/A|N/A|–|
|Maximális adatméret (GB)|3072|3072|3072|4096|4096|4096|4096|
|Max. napló mérete (GB)|922|922|922|1229|1229|1229|1229|
|A TempDB mérete (GB)|384|384|384|384|384|384|384|
|Tárolási típus|Premium (Remote) Storage|Premium (Remote) Storage|Premium (Remote) Storage|Premium (Remote) Storage|Premium (Remote) Storage|Premium (Remote) Storage|Premium (Remote) Storage|
|IO-késés (becsült)|5 – 7 ms (írás)<br>5 – 10 ms (olvasás)|5 – 7 ms (írás)<br>5 – 10 ms (olvasás)|5 – 7 ms (írás)<br>5 – 10 ms (olvasás)|5 – 7 ms (írás)<br>5 – 10 ms (olvasás)|5 – 7 ms (írás)<br>5 – 10 ms (olvasás)|5 – 7 ms (írás)<br>5 – 10 ms (olvasás)|5 – 7 ms (írás)<br>5 – 10 ms (olvasás)|
|Cél IOPS (64 KB)|7000|7000|7000|7000|7000|7000|7000|
|Napló Sebességhatár (MB/s)|30|30|30|30|30|30|30|
|Egyidejű feldolgozók (kérelmek) maximális|1600|1800|2000|2400|3200|4000|8000|
|Maximális engedélyezett munkamenetek|30000|30000|30000|30000|30000|30000|30000|
|Replikák száma|1|1|1|1|1|1|1|
|Több-AZ|–|N/A|N/A|N/A|N/A|N/A|–|
|Felskálázás olvasása|–|N/A|N/A|N/A|N/A|N/A|–|
|Biztonsági mentési tárterület|1 X-adatbázis mérete|1 X-adatbázis mérete|1 X-adatbázis mérete|1 X-adatbázis mérete|1 X-adatbázis mérete|1 X-adatbázis mérete|1 X-adatbázis mérete|

### <a name="serverless-compute-tier"></a>Kiszolgáló nélküli számítási szint

A [kiszolgáló nélküli számítási kapacitás](sql-database-serverless.md) előzetes verzióban érhető el, és csak az önálló adatbázisok használatával, a virtuális mag vásárlási modell.

#### <a name="generation-5-compute-platform"></a>5\. generáció számítási platform

|Számítási mérete|GP_S_Gen5_1|GP_S_Gen5_2|GP_S_Gen5_4|
|:--- | --: |--: |--: |
|H/W generálása|5|5|5|
|Min-max virtuális magok|0.5-1|0.5-2|0.5-4|
|Min-max memória (GB)|2.02-3|2.05-6|2.10-12|
|Automatikus szüneteltetési minimális késleltetés (óra)|6|6|6|
|Oszlopcentrikus támogatása|Igen|Igen|Igen|
|Memóriabeli OLTP storage (GB)|–|N/A|–|
|Maximális adatméret (GB)|512|1024|1024|
|Max. napló mérete (GB)|12|24|48|
|A TempDB mérete (GB)|32|64|128|
|Tárolási típus|Premium (Remote) Storage|Premium (Remote) Storage|Premium (Remote) Storage|
|IO-késés (becsült)|5 – 7 ms (írás)<br>5 – 10 ms (olvasás)|5 – 7 ms (írás)<br>5 – 10 ms (olvasás)|5 – 7 ms (írás)<br>5 – 10 ms (olvasás)|
|Cél IOPS (64 KB)|500|1000|2000|
|Napló Sebességhatár (MB/s)|2.5|5.6|10|
|Egyidejű feldolgozók (kérelmek) maximális|75|150|300|
|Maximális engedélyezett munkamenetek|30000|30000|30000|
|Replikák száma|1|1|1|
|Több-AZ|–|N/A|–|
|Felskálázás olvasása|–|N/A|–|
|Biztonsági mentési tárterület|1 X-adatbázis mérete|1 X-adatbázis mérete|1 X-adatbázis mérete|

## <a name="business-critical-service-tier-for-provisioned-compute-tier"></a>Üzleti kritikus szolgáltatási szinten a kiépített számítási kapacitás

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
|Napló Sebességhatár (MB/s)|8|16|24|32|40|48|
|Egyidejű feldolgozók (kérelmek) maximális|200|400|600|800|1000|1200|
|Egyidejű bejelentkezések maximális száma|200|400|600|800|1000|1200|
|Maximális engedélyezett munkamenetek|30000|30000|30000|30000|30000|30000|
|Replikák száma|4|4|4|4|4|4|
|Több-AZ|Igen|Igen|Igen|Igen|Igen|Igen|
|Felskálázás olvasása|Igen|Igen|Igen|Igen|Igen|Igen|
|Biztonsági mentési tárterület|1 X-adatbázis mérete|1 X-adatbázis mérete|1 X-adatbázis mérete|1 X-adatbázis mérete|1 X-adatbázis mérete|1 X-adatbázis mérete|

### <a name="business-critical-service-tier-generation-4-compute-platform-part-2"></a>Üzleti kritikus fontosságú szolgáltatási szint: 4. generációs számítási platform (2. rész)

|Számítási mérete|BC_Gen4_7|BC_Gen4_8|BC_Gen4_9|BC_Gen4_10|BC_Gen4_16|BC_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |
|H/W generálása|4|4|4|4|4|4|
|Virtuális magok|7|8|9|10|16|24|
|Memória (GB)|49|56|63|70|112|168|
|Oszlopcentrikus támogatása|Igen|Igen|Igen|Igen|Igen|Igen|
|Memóriabeli OLTP storage (GB)|7|8|9.5|11|20|36|
|Tárolási típus|Helyi SSD|Helyi SSD|Helyi SSD|Helyi SSD|Helyi SSD|Helyi SSD|
|Maximális adatméret (GB)|650|650|650|650|1024|1024|
|Max. napló mérete (GB)|195|195|195|195|307|307|
|A TempDB mérete (GB)|224|256|288|320|384|384|
|IO-késés (becsült)|1 – 2 ms (írás)<br>1 – 2 ms (olvasás)|1 – 2 ms (írás)<br>1 – 2 ms (olvasás)|1 – 2 ms (írás)<br>1 – 2 ms (olvasás)|1 – 2 ms (írás)<br>1 – 2 ms (olvasás)|1 – 2 ms (írás)<br>1 – 2 ms (olvasás)|1 – 2 ms (írás)<br>1 – 2 ms (olvasás)|
|Cél IOPS (64 KB)|35000|40000|45000|50000|80000|120000|
|Napló Sebességhatár (MB/s)|56|64|64|64|64|64|
|Egyidejű feldolgozók (kérelmek) maximális|1400|1600|1800|2000|3200|4800|
|Egyidejű bejelentkezések maximális száma (kérelmek)|1400|1600|1800|2000|3200|4800|
|Maximális engedélyezett munkamenetek|30000|30000|30000|30000|30000|30000|
|Replikák száma|4|4|4|4|4|4|
|Több-AZ|Igen|Igen|Igen|Igen|Igen|Igen|
|Felskálázás olvasása|Igen|Igen|Igen|Igen|Igen|Igen|
|Biztonsági mentési tárterület|1 X-adatbázis mérete|1 X-adatbázis mérete|1 X-adatbázis mérete|1 X-adatbázis mérete|1 X-adatbázis mérete|1 X-adatbázis mérete|

### <a name="business-critical-service-tier-generation-5-compute-platform-part-1"></a>Üzleti kritikus fontosságú szolgáltatási szint: 5. generáció számítási platform (1. rész)

|Számítási mérete|BC_Gen5_2|BC_Gen5_4|BC_Gen5_6|BC_Gen5_8|BC_Gen5_10|BC_Gen5_12|BC_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |
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
|Cél IOPS (64 KB)|8000|16000|24000|32000|40000|48000|56000|
|Napló Sebességhatár (MB/s)|12|24|36|48|60|72|84|
|Egyidejű feldolgozók (kérelmek) maximális|200|400|600|800|1000|1200|1400|
|Egyidejű bejelentkezések maximális száma|200|400|600|800|1000|1200|1400|
|Maximális engedélyezett munkamenetek|30000|30000|30000|30000|30000|30000|30000|
|Replikák száma|4|4|4|4|4|4|4|
|Több-AZ|Igen|Igen|Igen|Igen|Igen|Igen|Igen|
|Felskálázás olvasása|Igen|Igen|Igen|Igen|Igen|Igen|Igen|
|Biztonsági mentési tárterület|1 X-adatbázis mérete|1 X-adatbázis mérete|1 X-adatbázis mérete|1 X-adatbázis mérete|1 X-adatbázis mérete|1 X-adatbázis mérete|1 X-adatbázis mérete|

### <a name="business-critical-service-tier-generation-5-compute-platform-part-2"></a>Üzleti kritikus fontosságú szolgáltatási szint: 5. generáció számítási platform (2. rész)

|Számítási mérete|BC_Gen5_16|BC_Gen5_18|BC_Gen5_20|BC_Gen5_24|BC_Gen5_32|BC_Gen5_40|BC_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |
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
|Cél IOPS (64 KB)|64000|72000|80000|96000|128000|160000|320000|
|Napló Sebességhatár (MB/s)|96|96|96|96|96|96|96|
|Egyidejű feldolgozók (kérelmek) maximális|1600|1800|2000|2400|3200|4000|8000|
|Egyidejű bejelentkezések maximális száma|1600|1800|2000|2400|3200|4000|8000|
|Maximális engedélyezett munkamenetek|30000|30000|30000|30000|30000|30000|30000|
|Replikák száma|4|4|4|4|4|4|4|
|Több-AZ|Igen|Igen|Igen|Igen|Igen|Igen|Igen|
|Felskálázás olvasása|Igen|Igen|Igen|Igen|Igen|Igen|Igen|
|Biztonsági mentési tárterület|1 X-adatbázis mérete|1 X-adatbázis mérete|1 X-adatbázis mérete|1 X-adatbázis mérete|1 X-adatbázis mérete|1 X-adatbázis mérete|1 X-adatbázis mérete|

## <a name="hyperscale-service-tier"></a>Rugalmas skálázás szolgáltatási szint

### <a name="generation-5-compute-platform"></a>5\. generáció számítási platform

|Teljesítményszint|HS_Gen5_2|HS_Gen5_4|HS_Gen5_8|HS_Gen5_16|HS_Gen5_24|HS_Gen5_32|HS_Gen5_40|HS_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |--: |
|H/W generálása|5|5|5|5|5|5|5|5|
|Virtuális magok|2|4|8|16|24|32|40|80|
|Memória (GB)|10.2|20.4|40.8|81.6|122.4|163.2|204|408|
|Oszlopcentrikus támogatása|Igen|Igen|Igen|Igen|Igen|Igen|Igen|Igen|
|Memóriabeli OLTP storage (GB)|–|N/A|N/A|N/A|N/A|N/A|N/A|–|
|Maximális adatméret (TB)|100 |100 |100 |100 |100 |100 |100 |100 |
|Max. napló mérete (TB)|1 |1 |1 |1 |1 |1 |1 |1 |
|A TempDB mérete (GB)|64|128|256|384|384|384|384|384|
|Tárolási típus|Helyi SSD|Helyi SSD|Helyi SSD|Helyi SSD|Helyi SSD|Helyi SSD|Helyi SSD|Helyi SSD|
|Cél IOPS (64 KB)|Meghatározott|Meghatározott|Meghatározott|Meghatározott|Meghatározott|Meghatározott|Meghatározott|Meghatározott|
|IO-késés (becsült)|Meghatározott|Meghatározott|Meghatározott|Meghatározott|Meghatározott|Meghatározott|Meghatározott|Meghatározott|
|Egyidejű feldolgozók (kérelmek) maximális|200|400|800|1600|2400|3200|4000|8000|
|Maximális engedélyezett munkamenetek|30000|30000|30000|30000|30000|30000|30000|30000|
|Replikák száma|2|2|2|2|2|2|2|2|
|Több-AZ|–|N/A|N/A|N/A|N/A|N/A|N/A|–|
|Felskálázás olvasása|Igen|Igen|Igen|Igen|Igen|Igen|Igen|Igen|
|Biztonsági mentési tárterület |7|7|7|7|7|7|7|7|
|||

## <a name="next-steps"></a>További lépések

- Önálló adatbázis dtu-k erőforráskorlátok, lásd: [erőforráskorlátok és önálló adatbázisokat a DTU-alapú vásárlási modell használatával](sql-database-dtu-resource-limits-single-databases.md)
- Rugalmas készletek erőforráskorlátok virtuális mag, lásd: [erőforráskorlátok a rugalmas készletek a Virtuálismag-alapú vásárlási modell használatával](sql-database-vcore-resource-limits-elastic-pools.md)
- Dtu-k erőforráskorlátok a rugalmas készletek, lásd: [erőforráskorlátok a rugalmas készletek a DTU-alapú vásárlási modell használatával](sql-database-dtu-resource-limits-elastic-pools.md)
- Erőforráskorlátok a felügyelt példányok, lásd: [felügyelt példány erőforráskorlátok](sql-database-managed-instance-resource-limits.md).
- Azure – általános korlátozások kapcsolatos információkért lásd: [Azure-előfizetés és a szolgáltatások korlátozásai, kvótái és megkötései](../azure-subscription-service-limits.md).
- Adatbázis-kiszolgáló erőforráskorlátok kapcsolatos információkért lásd: [az SQL Database-kiszolgálóhoz erőforráskorlátok áttekintése](sql-database-resource-limits-database-server.md) kapcsolatos korlátozásokat a kiszolgálók és az előfizetés szintjén.