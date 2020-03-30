---
title: virtuális magerőforrás-korlátok – egyetlen adatbázis
description: Ez a lap néhány gyakori virtuálismag-erőforrás-korlátot ismertet az Azure SQL Database egyetlen adatbázisához.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 03/11/2020
ms.openlocfilehash: 38d44f5a2ce22de15cb14ea5aa7a9ca1ea7c03cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481065"
---
# <a name="resource-limits-for-single-databases-using-the-vcore-purchasing-model"></a>Erőforráskorlátok a virtuálismag-vásárlási modellt használó egyedi adatbázisokhoz

Ez a cikk az Azure SQL Database egyetlen adatbázisok részletes erőforrás-korlátait tartalmazza a virtuálismag-vásárlási modell használatával.

Az SQL Database-kiszolgálón lévő egyes adatbázisokDTU-vásárlási modellkorlátairól [az SQL Database-kiszolgálóerőforrás-korlátok áttekintése című témakörben](sql-database-resource-limits-database-server.md)olvashat.

Egyetlen adatbázis szolgáltatási szintjét, számítási méretét és tárolási mennyiségét az [Azure Portal](sql-database-single-databases-manage.md#manage-an-existing-sql-database-server), a [Transact-SQL,](sql-database-single-databases-manage.md#transact-sql-manage-sql-database-servers-and-single-databases)a [PowerShell,](sql-database-single-databases-manage.md#powershell-manage-sql-database-servers-and-single-databases)az [Azure CLI](sql-database-single-databases-manage.md#azure-cli-manage-sql-database-servers-and-single-databases)vagy a [REST API](sql-database-single-databases-manage.md#rest-api-manage-sql-database-servers-and-single-databases)használatával állíthatja be.

> [!IMPORTANT]
> A méretezéssel kapcsolatos útmutatásért és szempontokért olvassa el [az Egyetlen adatbázis méretezése](sql-database-single-database-scale.md)című témakört.

## <a name="general-purpose---serverless-compute---gen5"></a>Általános célú - kiszolgáló nélküli számítás - Gen5

A [kiszolgáló nélküli számítási szint](sql-database-serverless.md) jelenleg csak a Gen5 hardveren érhető el.

### <a name="gen5-compute-generation-part-1"></a>Gen5 számítási generáció (1. rész)

|Számítási méret|GP_S_Gen5_1|GP_S_Gen5_2|GP_S_Gen5_4|GP_S_Gen5_6|GP_S_Gen5_8|
|:--- | --: |--: |--: |--: |--: |
|Számításgenerálás|Gen5 (1998)|Gen5 (1998)|Gen5 (1998)|Gen5 (1998)|Gen5 (1998)|
|Max mamag|0.5-1|0.5-2|0.5-4|0.75-6|1.0-8|
|Min-max memória (GB)|2.02-3|2.05-6|2.10-12|2.25-18|3.00-24|
|Min automatikus szüneteltetési késleltetés (perc)|60|60|60|60|60|
|Oszlopcentrikus támogatás|Igen|Igen|Igen|Igen|Igen|
|Memórián belüli OLTP-tároló (GB)|N/A|N/A|N/A|N/A|N/A|
|Maximális adatméret (GB)|512|1024|1024|1024|1536|
|Napló maximális mérete (GB)|154|307|307|307|461|
|TempDB maximális adatméret (GB)|32|64|128|192|256|
|Tárolási típus|Távoli SSD|Távoli SSD|Távoli SSD|Távoli SSD|Távoli SSD|
|IO késés (hozzávetőleges)|5-7 ms (írás)<br>5-10 ms (olvasás)|5-7 ms (írás)<br>5-10 ms (olvasás)|5-7 ms (írás)<br>5-10 ms (olvasás)|5-7 ms (írás)<br>5-10 ms (olvasás)|5-7 ms (írás)<br>5-10 ms (olvasás)|
|Maximális adat IOPS *|320|640|1280|1920|2560|
|Maximális naplósebesség (Mb/s)|3.8|7,5|15|22.5|30|
|Egyidejű dolgozók maximális alkalmazása (kérések)|75|150|300|450|600|
|Egyidejű munkamenetek maximális száma|30,000|30,000|30,000|30,000|30,000|
|Replikák száma|1|1|1|1|1|
|Multi-AZ|N/A|N/A|N/A|N/A|N/A|
|Olvasási felskálázás|N/A|N/A|N/A|N/A|N/A|
|Mellékelt biztonsági mentési tároló|1X DB méret|1X DB méret|1X DB méret|1X DB méret|1X DB méret|

\*A 8 KB és 64 KB közötti i/o-méretek maximális értéke. A tényleges IOPS a munkaterheléstől függ. További részletek: [Data IO Governance](sql-database-resource-limits-database-server.md#resource-governance).

### <a name="gen5-compute-generation-part-2"></a>Gen5 számítási generáció (2. rész)

|Számítási méret|GP_S_Gen5_10|GP_S_Gen5_12|GP_S_Gen5_14|GP_S_Gen5_16|
|:--- | --: |--: |--: |--: |
|Számításgenerálás|Gen5 (1998)|Gen5 (1998)|Gen5 (1998)|Gen5 (1998)|
|Max mamag|1.25-10|1.50-12|1.75-14|2.00-16|
|Min-max memória (GB)|3.75-30|4.50-36|5.25-42|6.00-48|
|Min automatikus szüneteltetési késleltetés (perc)|60|60|60|60|
|Oszlopcentrikus támogatás|Igen|Igen|Igen|Igen|
|Memórián belüli OLTP-tároló (GB)|N/A|N/A|N/A|N/A|
|Maximális adatméret (GB)|1536|3072|3072|3072|
|Napló maximális mérete (GB)|461|461|461|922|
|TempDB maximális adatméret (GB)|320|384|448|512|
|Tárolási típus|Távoli SSD|Távoli SSD|Távoli SSD|Távoli SSD|
|IO késés (hozzávetőleges)|5-7 ms (írás)<br>5-10 ms (olvasás)|5-7 ms (írás)<br>5-10 ms (olvasás)|5-7 ms (írás)<br>5-10 ms (olvasás)|5-7 ms (írás)<br>5-10 ms (olvasás)|
|Maximális adat IOPS *|3200|3840|4480|5120|
|Maximális naplósebesség (Mb/s)|30|30|30|30|
|Egyidejű dolgozók maximális alkalmazása (kérések)|750|900|1050|1200|
|Egyidejű munkamenetek maximális száma|30,000|30,000|30,000|30,000|
|Replikák száma|1|1|1|1|
|Multi-AZ|N/A|N/A|N/A|N/A|
|Olvasási felskálázás|N/A|N/A|N/A|N/A|
|Mellékelt biztonsági mentési tároló|1X DB méret|1X DB méret|1X DB méret|1X DB méret|

\*A 8 KB és 64 KB közötti i/o-méretek maximális értéke. A tényleges IOPS a munkaterheléstől függ. További részletek: [Data IO Governance](sql-database-resource-limits-database-server.md#resource-governance).

## <a name="hyperscale---provisioned-compute---gen4"></a>Nagy kapacitás - kiépített számítás - Gen4

### <a name="gen4-compute-generation-part-1"></a>Gen4 számítási generáció (1. rész)

|Teljesítményszint|HS_Gen4_1|HS_Gen4_2|HS_Gen4_3|HS_Gen4_4|HS_Gen4_5|HS_Gen4_6|
|:--- | --: |--: |--: |---: | --: |--: |
|Számításgenerálás|Gen4 (1988)|Gen4 (1988)|Gen4 (1988)|Gen4 (1988)|Gen4 (1988)|Gen4 (1988)|
|virtuális magok|1|2|3|4|5|6|
|Memória (GB)|7|14|21|28|35|42|
|[RBPEX között](sql-database-service-tier-hyperscale.md#compute) Méret|3X memória|3X memória|3X memória|3X memória|3X memória|3X memória|
|Oszlopcentrikus támogatás|Igen|Igen|Igen|Igen|Igen|Igen|
|Memórián belüli OLTP-tároló (GB)|N/A|N/A|N/A|N/A|N/A|N/A|
|Maximális adatméret (TB)|100 |100 |100 |100 |100 |100|
|Maximális naplóméret (TB)|1 |1 |1 |1 |1 |1 |
|TempDB maximális adatméret (GB)|32|64|96|128|160|192|
|Tárolási típus| [1. megjegyzés](#notes) |[1. megjegyzés](#notes)|[1. megjegyzés](#notes) |[1. megjegyzés](#notes) |[1. megjegyzés](#notes) |[1. megjegyzés](#notes) |
|Maximális adat IOPS *|[2. megjegyzés](#notes)|[2. megjegyzés](#notes)|[2. megjegyzés](#notes)|[2. megjegyzés](#notes)|[2. megjegyzés](#notes)|[2. megjegyzés](#notes)|
|Maximális naplósebesség (Mb/s)|100 |100 |100 |100 |100 |100 |
|IO késés (hozzávetőleges)|[3. megjegyzés](#notes)|[3. megjegyzés](#notes)|[3. megjegyzés](#notes)|[3. megjegyzés](#notes)|[3. megjegyzés](#notes)|[3. megjegyzés](#notes)|
|Egyidejű dolgozók maximális alkalmazása (kérések)|200|400|600|800|1000|1200|
|Egyidejű munkamenetek maximális száma|30,000|30,000|30,000|30,000|30,000|30,000|
|Másodlagos replikák|0-4|0-4|0-4|0-4|0-4|0-4|
|Multi-AZ|N/A|N/A|N/A|N/A|N/A|N/A|
|Olvasási felskálázás|Igen|Igen|Igen|Igen|Igen|Igen|
|Biztonsági mentési tároló megőrzése|7 nap|7 nap|7 nap|7 nap|7 nap|7 nap|
|||

### <a name="gen4-compute-generation-part-2"></a>Gen4 számítási generáció (2. rész)

|Teljesítményszint|HS_Gen4_7|HS_Gen4_8|HS_Gen4_9|HS_Gen4_10|HS_Gen4_16|HS_Gen4_24|
|:--- | ---: |--: |--: | --: |--: |--: |
|Számításgenerálás|Gen4 (1988)|Gen4 (1988)|Gen4 (1988)|Gen4 (1988)|Gen4 (1988)|Gen4 (1988)|
|virtuális magok|7|8|9|10|16|24|
|Memória (GB)|49|56|63|70|112|159.5|
|[RBPEX között](sql-database-service-tier-hyperscale.md#compute) Méret|3X memória|3X memória|3X memória|3X memória|3X memória|3X memória|
|Oszlopcentrikus támogatás|Igen|Igen|Igen|Igen|Igen|Igen|
|Memórián belüli OLTP-tároló (GB)|N/A|N/A|N/A|N/A|N/A|N/A|
|Maximális adatméret (TB)|100 |100 |100 |100 |100 |100 |
|Maximális naplóméret (TB)|1 |1 |1 |1 |1 |1 |
|TempDB maximális adatméret (GB)|224|256|288|320|512|768|
|Tárolási típus| [1. megjegyzés](#notes) |[1. megjegyzés](#notes) |[1. megjegyzés](#notes) |[1. megjegyzés](#notes) |[1. megjegyzés](#notes) |[1. megjegyzés](#notes) |
|Maximális adat IOPS *|[2. megjegyzés](#notes)|[2. megjegyzés](#notes)|[2. megjegyzés](#notes)|[2. megjegyzés](#notes)|[2. megjegyzés](#notes)|[2. megjegyzés](#notes)|
|Maximális naplósebesség (Mb/s)|100 |100 |100 |100 |100 |100 |
|IO késés (hozzávetőleges)|[3. megjegyzés](#notes)|[3. megjegyzés](#notes)|[3. megjegyzés](#notes)|[3. megjegyzés](#notes)|[3. megjegyzés](#notes)|[3. megjegyzés](#notes)|
|Egyidejű dolgozók maximális alkalmazása (kérések)|1400|1600|1800|2000|3200|4800|
|Egyidejű munkamenetek maximális száma|30,000|30,000|30,000|30,000|30,000|30,000|
|Másodlagos replikák|0-4|0-4|0-4|0-4|0-4|0-4|
|Multi-AZ|N/A|N/A|N/A|N/A|N/A|N/A|
|Olvasási felskálázás|Igen|Igen|Igen|Igen|Igen|Igen|
|Biztonsági mentési tároló megőrzése|7 nap|7 nap|7 nap|7 nap|7 nap|7 nap|
|||

\*A 8 KB és 64 KB közötti i/o-méretek maximális értéke. A tényleges IOPS a munkaterheléstől függ. További részletek: [Data IO Governance](sql-database-resource-limits-database-server.md#resource-governance).

## <a name="hyperscale---provisioned-compute---gen5"></a>Nagy kapacitás - kiépített számítás - Gen5

### <a name="gen5-compute-generation-part-1"></a>Gen5 számítási generáció (1. rész)

|Teljesítményszint|HS_Gen5_2|HS_Gen5_4|HS_Gen5_6|HS_Gen_8|HS_Gen5_10|HS_Gen5_12|HS_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |--: |
|Számításgenerálás|Gen5 (1998)|Gen5 (1998)|Gen5 (1998)|Gen5 (1998)|Gen5 (1998)|Gen5 (1998)|Gen5 (1998)|
|virtuális magok|2|4|6|8|10|12|14|
|Memória (GB)|10.4|20.8|31.1|41.5|51.9|62.3|72.7|
|[RBPEX között](sql-database-service-tier-hyperscale.md#compute) Méret|3X memória|3X memória|3X memória|3X memória|3X memória|3X memória|3X memória|
|Oszlopcentrikus támogatás|Igen|Igen|Igen|Igen|Igen|Igen|Igen|
|Memórián belüli OLTP-tároló (GB)|N/A|N/A|N/A|N/A|N/A|N/A|N/A|
|Maximális adatméret (TB)|100 |100 |100 |100 |100 |100 |100|
|Maximális naplóméret (TB)|1 |1 |1 |1 |1 |1 |1 |
|TempDB maximális adatméret (GB)|64|128|192|256|320|384|448|
|Tárolási típus| [1. megjegyzés](#notes) |[1. megjegyzés](#notes)|[1. megjegyzés](#notes) |[1. megjegyzés](#notes) |[1. megjegyzés](#notes) |[1. megjegyzés](#notes) |[1. megjegyzés](#notes) |
|Maximális adat IOPS *|[2. megjegyzés](#notes)|[2. megjegyzés](#notes)|[2. megjegyzés](#notes)|[2. megjegyzés](#notes)|[2. megjegyzés](#notes)|[2. megjegyzés](#notes)|[2. megjegyzés](#notes)|
|Maximális naplósebesség (Mb/s)|100 |100 |100 |100 |100 |100 |100 |
|IO késés (hozzávetőleges)|[3. megjegyzés](#notes)|[3. megjegyzés](#notes)|[3. megjegyzés](#notes)|[3. megjegyzés](#notes)|[3. megjegyzés](#notes)|[3. megjegyzés](#notes)|[3. megjegyzés](#notes)|
|Egyidejű dolgozók maximális alkalmazása (kérések)|200|400|600|800|1000|1200|1400|
|Egyidejű munkamenetek maximális száma|30,000|30,000|30,000|30,000|30,000|30,000|30,000|
|Másodlagos replikák|0-4|0-4|0-4|0-4|0-4|0-4|0-4|
|Multi-AZ|N/A|N/A|N/A|N/A|N/A|N/A|N/A|
|Olvasási felskálázás|Igen|Igen|Igen|Igen|Igen|Igen|Igen|
|Biztonsági mentési tároló megőrzése|7 nap|7 nap|7 nap|7 nap|7 nap|7 nap|7 nap|
|||

\*A 8 KB és 64 KB közötti i/o-méretek maximális értéke. A tényleges IOPS a munkaterheléstől függ. További részletek: [Data IO Governance](sql-database-resource-limits-database-server.md#resource-governance).

### <a name="gen5-compute-generation-part-2"></a>Gen5 számítási generáció (2. rész)

|Teljesítményszint|HS_Gen5_16|HS_Gen5_18|HS_Gen5_20|HS_Gen_24|HS_Gen5_32|HS_Gen5_40|HS_Gen5_80|
|:--- | --: |--: |--: |--: |---: |--: |--: |
|Számításgenerálás|Gen5 (1998)|Gen5 (1998)|Gen5 (1998)|Gen5 (1998)|Gen5 (1998)|Gen5 (1998)|Gen5 (1998)|
|virtuális magok|16|18|20|24|32|40|80|
|Memória (GB)|83|93.4|103.8|124.6|166.1|207.6|415.2|
|[RBPEX között](sql-database-service-tier-hyperscale.md#compute) Méret|3X memória|3X memória|3X memória|3X memória|3X memória|3X memória|3X memória|
|Oszlopcentrikus támogatás|Igen|Igen|Igen|Igen|Igen|Igen|Igen|
|Memórián belüli OLTP-tároló (GB)|N/A|N/A|N/A|N/A|N/A|N/A|N/A|
|Maximális adatméret (TB)|100 |100 |100 |100 |100 |100 |100 |
|Maximális naplóméret (TB)|1 |1 |1 |1 |1 |1 |1 |
|TempDB maximális adatméret (GB)|512|576|640|768|1024|1280|2560|
|Tárolási típus| [1. megjegyzés](#notes) |[1. megjegyzés](#notes)|[1. megjegyzés](#notes)|[1. megjegyzés](#notes) |[1. megjegyzés](#notes) |[1. megjegyzés](#notes) |[1. megjegyzés](#notes) |
|Maximális adat IOPS *|[2. megjegyzés](#notes)|[2. megjegyzés](#notes)|[2. megjegyzés](#notes)|[2. megjegyzés](#notes)|[2. megjegyzés](#notes)|[2. megjegyzés](#notes)|[2. megjegyzés](#notes)|
|Maximális naplósebesség (Mb/s)|100 |100 |100 |100 |100 |100 |100 |
|IO késés (hozzávetőleges)|[3. megjegyzés](#notes)|[3. megjegyzés](#notes)|[3. megjegyzés](#notes)|[3. megjegyzés](#notes)|[3. megjegyzés](#notes)|[3. megjegyzés](#notes)|[3. megjegyzés](#notes)|
|Egyidejű dolgozók maximális alkalmazása (kérések)|1600|1800|2000|2400|3200|4000|8000|
|Egyidejű munkamenetek maximális száma|30,000|30,000|30,000|30,000|30,000|30,000|30,000|
|Másodlagos replikák|0-4|0-4|0-4|0-4|0-4|0-4|0-4|
|Multi-AZ|N/A|N/A|N/A|N/A|N/A|N/A|N/A|
|Olvasási felskálázás|Igen|Igen|Igen|Igen|Igen|Igen|Igen|
|Biztonsági mentési tároló megőrzése|7 nap|7 nap|7 nap|7 nap|7 nap|7 nap|7 nap|
|||

\*A 8 KB és 64 KB közötti i/o-méretek maximális értéke. A tényleges IOPS a munkaterheléstől függ. További részletek: [Data IO Governance](sql-database-resource-limits-database-server.md#resource-governance).

#### <a name="notes"></a>Megjegyzések

**1. megjegyzés:** A nagykapacitás egy többszintű architektúra, külön számítási és tárolási összetevőkkel: [A szolgáltatási réteg architektúrája](sql-database-service-tier-hyperscale.md#distributed-functions-architecture)

**2. megjegyzés:** A többszintű nagyléptékű architektúra több szinten is gyorsítótárazást jelent. A hatékony IOPS a munkaterheléstől függ.

**3. megjegyzés:** A késés 1–2 ezredrészben érhető el az RBPEX SSD-alapú gyorsítótárban lévő adatok esetében a számítási replikákon, amely gyorsítótárazza a leggyakrabban használt adatlapokat. A lapkiszolgálókról beolvasott adatok nagyobb késése.

## <a name="general-purpose---provisioned-compute---gen4"></a>Általános célú - kiépített számítás - Gen4

> [!IMPORTANT]
> Az új Gen4-adatbázisok már nem támogatottak az Ausztrália keleti vagy brazíliai déli régióiban.

### <a name="gen4-compute-generation-part-1"></a>Gen4 számítási generáció (1. rész)

|Számítási méret|GP_Gen4_1|GP_Gen4_2|GP_Gen4_3|GP_Gen4_4|GP_Gen4_5|GP_Gen4_6
|:--- | --: |--: |--: |--: |--: |--: |
|Számításgenerálás|Gen4 (1988)|Gen4 (1988)|Gen4 (1988)|Gen4 (1988)|Gen4 (1988)|Gen4 (1988)|
|virtuális magok|1|2|3|4|5|6|
|Memória (GB)|7|14|21|28|35|42|
|Oszlopcentrikus támogatás|Igen|Igen|Igen|Igen|Igen|Igen|
|Memórián belüli OLTP-tároló (GB)|N/A|N/A|N/A|N/A|N/A|N/A|
|Maximális adatméret (GB)|1024|1024|1536|1536|1536|3072|
|Napló maximális mérete (GB)|307|307|461|461|461|922|
|TempDB maximális adatméret (GB)|32|64|96|128|160|192|
|Tárolási típus|Távoli SSD|Távoli SSD|Távoli SSD|Távoli SSD|Távoli SSD|Távoli SSD|
|IO késés (hozzávetőleges)|5-7 ms (írás)<br>5-10 ms (olvasás)|5-7 ms (írás)<br>5-10 ms (olvasás)|5-7 ms (írás)<br>5-10 ms (olvasás)|5-7 ms (írás)<br>5-10 ms (olvasás)|5-7 ms (írás)<br>5-10 ms (olvasás)|5-7 ms (írás)<br>5-10 ms (olvasás)|
|Maximális adat IOPS *|320|640|960|1280|1600|1920|
|Maximális naplósebesség (Mb/s)|3,75|7,5|11.25|15|18.75|22.5|
|Egyidejű dolgozók maximális alkalmazása (kérések)|200|400|600|800|1000|1200|
|Egyidejű munkamenetek maximális száma|30,000|30,000|30,000|30,000|30,000|30,000|
|Replikák száma|1|1|1|1|1|1|
|Multi-AZ|N/A|N/A|N/A|N/A|N/A|N/A|
|Olvasási felskálázás|N/A|N/A|N/A|N/A|N/A|N/A|
|Mellékelt biztonsági mentési tároló|1X DB méret|1X DB méret|1X DB méret|1X DB méret|1X DB méret|1X DB méret|

\*A 8 KB és 64 KB közötti i/o-méretek maximális értéke. A tényleges IOPS a munkaterheléstől függ. További részletek: [Data IO Governance](sql-database-resource-limits-database-server.md#resource-governance).

### <a name="gen4-compute-generation-part-2"></a>Gen4 számítási generáció (2. rész)

|Számítási méret|GP_Gen4_7|GP_Gen4_8|GP_Gen4_9|GP_Gen4_10|GP_Gen4_16|GP_Gen4_24
|:--- | --: |--: |--: |--: |--: |--: |
|Számításgenerálás|Gen4 (1988)|Gen4 (1988)|Gen4 (1988)|Gen4 (1988)|Gen4 (1988)|Gen4 (1988)|
|virtuális magok|7|8|9|10|16|24|
|Memória (GB)|49|56|63|70|112|159.5|
|Oszlopcentrikus támogatás|Igen|Igen|Igen|Igen|Igen|Igen|
|Memórián belüli OLTP-tároló (GB)|N/A|N/A|N/A|N/A|N/A|N/A|
|Maximális adatméret (GB)|3072|3072|3072|3072|4096|4096|
|Napló maximális mérete (GB)|922|922|922|922|1229|1229|
|TempDB maximális adatméret (GB)|224|256|288|320|512|768|
|Tárolási típus|Távoli SSD|Távoli SSD|Távoli SSD|Távoli SSD|Távoli SSD|Távoli SSD|
|IO késés (hozzávetőleges)|5-7 ms (írás)<br>5-10 ms (olvasás)|5-7 ms (írás)<br>5-10 ms (olvasás)|5-7 ms (írás)<br>5-10 ms (olvasás)|5-7 ms (írás)<br>5-10 ms (olvasás)|5-7 ms (írás)<br>5-10 ms (olvasás)|5-7 ms (írás)<br>5-10 ms (olvasás)
|Maximális adat IOPS *|2240|2560|2880|3200|5120|7680|
|Maximális naplósebesség (Mb/s)|26.3|30|30|30|30|30|
|Egyidejű dolgozók maximális alkalmazása (kérések)|1400|1600|1800|2000|3200|4800|
|Egyidejű munkamenetek maximális száma|30,000|30,000|30,000|30,000|30,000|30,000|
|Replikák száma|1|1|1|1|1|1|
|Multi-AZ|N/A|N/A|N/A|N/A|N/A|N/A|
|Olvasási felskálázás|N/A|N/A|N/A|N/A|N/A|N/A|
|Mellékelt biztonsági mentési tároló|1X DB méret|1X DB méret|1X DB méret|1X DB méret|1X DB méret|1X DB méret|

\*A 8 KB és 64 KB közötti i/o-méretek maximális értéke. A tényleges IOPS a munkaterheléstől függ. További részletek: [Data IO Governance](sql-database-resource-limits-database-server.md#resource-governance).

## <a name="general-purpose---provisioned-compute---gen5"></a>Általános cél - kiépített számítás - Gen5

### <a name="gen5-compute-generation-part-1"></a>Gen5 számítási generáció (1. rész)

|Számítási méret|GP_Gen5_2|GP_Gen5_4|GP_Gen5_6|GP_Gen5_8|GP_Gen5_10|GP_Gen5_12|GP_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Számításgenerálás|Gen5 (1998)|Gen5 (1998)|Gen5 (1998)|Gen5 (1998)|Gen5 (1998)|Gen5 (1998)|Gen5 (1998)|
|virtuális magok|2|4|6|8|10|12|14|
|Memória (GB)|10.4|20.8|31.1|41.5|51.9|62.3|72.7|
|Oszlopcentrikus támogatás|Igen|Igen|Igen|Igen|Igen|Igen|Igen|
|Memórián belüli OLTP-tároló (GB)|N/A|N/A|N/A|N/A|N/A|N/A|N/A|
|Maximális adatméret (GB)|1024|1024|1536|1536|1536|3072|3072|
|Napló maximális mérete (GB)|307|307|461|461|461|922|922|
|TempDB maximális adatméret (GB)|64|128|192|256|320|384|384|
|Tárolási típus|Távoli SSD|Távoli SSD|Távoli SSD|Távoli SSD|Távoli SSD|Távoli SSD|Távoli SSD|
|IO késés (hozzávetőleges)|5-7 ms (írás)<br>5-10 ms (olvasás)|5-7 ms (írás)<br>5-10 ms (olvasás)|5-7 ms (írás)<br>5-10 ms (olvasás)|5-7 ms (írás)<br>5-10 ms (olvasás)|5-7 ms (írás)<br>5-10 ms (olvasás)|5-7 ms (írás)<br>5-10 ms (olvasás)|5-7 ms (írás)<br>5-10 ms (olvasás)|
|Maximális adat IOPS *|640|1280|1920|2560|3200|3840|4480|
|Maximális naplósebesség (Mb/s)|7,5|15|22.5|30|30|30|30|
|Egyidejű dolgozók maximális alkalmazása (kérések)|200|400|600|800|1000|1200|1400|
|Egyidejű munkamenetek maximális száma|30,000|30,000|30,000|30,000|30,000|30,000|30,000|
|Replikák száma|1|1|1|1|1|1|1|
|Multi-AZ|N/A|N/A|N/A|N/A|N/A|N/A|N/A|
|Olvasási felskálázás|N/A|N/A|N/A|N/A|N/A|N/A|N/A|
|Mellékelt biztonsági mentési tároló|1X DB méret|1X DB méret|1X DB méret|1X DB méret|1X DB méret|1X DB méret|1X DB méret|

\*A 8 KB és 64 KB közötti i/o-méretek maximális értéke. A tényleges IOPS a munkaterheléstől függ. További részletek: [Data IO Governance](sql-database-resource-limits-database-server.md#resource-governance).

### <a name="gen5-compute-generation-part-2"></a>Gen5 számítási generáció (2. rész)

|Számítási méret|GP_Gen5_16|GP_Gen5_18|GP_Gen5_20|GP_Gen5_24|GP_Gen5_32|GP_Gen5_40|GP_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Számításgenerálás|Gen5 (1998)|Gen5 (1998)|Gen5 (1998)|Gen5 (1998)|Gen5 (1998)|Gen5 (1998)|Gen5 (1998)|
|virtuális magok|16|18|20|24|32|40|80|
|Memória (GB)|83|93.4|103.8|124.6|166.1|207.6|415.2|
|Oszlopcentrikus támogatás|Igen|Igen|Igen|Igen|Igen|Igen|Igen|
|Memórián belüli OLTP-tároló (GB)|N/A|N/A|N/A|N/A|N/A|N/A|N/A|
|Maximális adatméret (GB)|3072|3072|3072|4096|4096|4096|4096|
|Napló maximális mérete (GB)|922|922|922|1229|1229|1229|1229|
|TempDB maximális adatméret (GB)|512|576|640|768|1024|1280|2560|
|Tárolási típus|Távoli SSD|Távoli SSD|Távoli SSD|Távoli SSD|Távoli SSD|Távoli SSD|Távoli SSD|
|IO késés (hozzávetőleges)|5-7 ms (írás)<br>5-10 ms (olvasás)|5-7 ms (írás)<br>5-10 ms (olvasás)|5-7 ms (írás)<br>5-10 ms (olvasás)|5-7 ms (írás)<br>5-10 ms (olvasás)|5-7 ms (írás)<br>5-10 ms (olvasás)|5-7 ms (írás)<br>5-10 ms (olvasás)|5-7 ms (írás)<br>5-10 ms (olvasás)|
|Maximális adat IOPS *|5120|5760|6400|7680|10240|12800|25600|
|Maximális naplósebesség (Mb/s)|30|30|30|30|30|30|30|
|Egyidejű dolgozók maximális alkalmazása (kérések)|1600|1800|2000|2400|3200|4000|8000|
|Egyidejű munkamenetek maximális száma|30,000|30,000|30,000|30,000|30,000|30,000|30,000|
|Replikák száma|1|1|1|1|1|1|1|
|Multi-AZ|N/A|N/A|N/A|N/A|N/A|N/A|N/A|
|Olvasási felskálázás|N/A|N/A|N/A|N/A|N/A|N/A|N/A|
|Mellékelt biztonsági mentési tároló|1X DB méret|1X DB méret|1X DB méret|1X DB méret|1X DB méret|1X DB méret|1X DB méret|

\*A 8 KB és 64 KB közötti i/o-méretek maximális értéke. A tényleges IOPS a munkaterheléstől függ. További részletek: [Data IO Governance](sql-database-resource-limits-database-server.md#resource-governance).

## <a name="general-purpose---provisioned-compute---fsv2-series"></a>Általános célú - kiépített számítás - Fsv2 sorozat

### <a name="fsv2-series-compute-generation-preview"></a>Fsv2 sorozatú számításgenerálás (előzetes verzió)

|Számítási méret|GP_Fsv2_72|
|:--- | --: |
|Számításgenerálás|Fsv2 sorozat|
|virtuális magok|72|
|Memória (GB)|136.2|
|Oszlopcentrikus támogatás|Igen|
|Memórián belüli OLTP-tároló (GB)|N/A|
|Maximális adatméret (GB)|4096|
|Napló maximális mérete (GB)|1024|
|TempDB maximális adatméret (GB)|333|
|Tárolási típus|Távoli SSD|
|IO késés (hozzávetőleges)|5-7 ms (írás)<br>5-10 ms (olvasás)|
|Maximális adat IOPS *|12,800|
|Maximális naplósebesség (Mb/s)|30|
|Egyidejű dolgozók maximális alkalmazása (kérések)|3600|
|Egyidejű bejelentkezések maximális száma|3600|
|Egyidejű munkamenetek maximális száma|30,000|
|Replikák száma|1|
|Multi-AZ|N/A|
|Olvasási felskálázás|N/A|
|Mellékelt biztonsági mentési tároló|1X DB méret|

\*A 8 KB és 64 KB közötti i/o-méretek maximális értéke. A tényleges IOPS a munkaterheléstől függ. További részletek: [Data IO Governance](sql-database-resource-limits-database-server.md#resource-governance).

## <a name="business-critical---provisioned-compute---gen4"></a>Üzleti legkritikusabb - kiépített számítás - Gen4

> [!IMPORTANT]
> Az új Gen4-adatbázisok már nem támogatottak az Ausztrália keleti vagy brazíliai déli régióiban.

### <a name="gen4-compute-generation-part-1"></a>Gen4 számítási generáció (1. rész)

|Számítási méret|BC_Gen4_1|BC_Gen4_2|BC_Gen4_3|BC_Gen4_4|BC_Gen4_5|BC_Gen4_6|
|:--- | --: |--: |--: |--: |--: |--: |
|Számításgenerálás|Gen4 (1988)|Gen4 (1988)|Gen4 (1988)|Gen4 (1988)|Gen4 (1988)|Gen4 (1988)|
|virtuális magok|1|2|3|4|5|6|
|Memória (GB)|7|14|21|28|35|42|
|Oszlopcentrikus támogatás|Igen|Igen|Igen|Igen|Igen|Igen|
|Memórián belüli OLTP-tároló (GB)|1|2|3|4|5|6|
|Tárolási típus|Helyi SSD|Helyi SSD|Helyi SSD|Helyi SSD|Helyi SSD|Helyi SSD|
|Maximális adatméret (GB)|1024|1024|1024|1024|1024|1024|
|Napló maximális mérete (GB)|307|307|307|307|307|307|
|TempDB maximális adatméret (GB)|32|64|96|128|160|192|
|IO késés (hozzávetőleges)|1-2 ms (írás)<br>1-2 ms (olvasás)|1-2 ms (írás)<br>1-2 ms (olvasás)|1-2 ms (írás)<br>1-2 ms (olvasás)|1-2 ms (írás)<br>1-2 ms (olvasás)|1-2 ms (írás)<br>1-2 ms (olvasás)|1-2 ms (írás)<br>1-2 ms (olvasás)|
|Maximális adat IOPS *|4,000|8,000|12 000|16000|20000|24,000|
|Maximális naplósebesség (Mb/s)|8|16|24|32|40|48|
|Egyidejű dolgozók maximális alkalmazása (kérések)|200|400|600|800|1000|1200|
|Egyidejű bejelentkezések maximális száma|200|400|600|800|1000|1200|
|Egyidejű munkamenetek maximális száma|30,000|30,000|30,000|30,000|30,000|30,000|
|Replikák száma|4|4|4|4|4|4|
|Multi-AZ|Igen|Igen|Igen|Igen|Igen|Igen|
|Olvasási felskálázás|Igen|Igen|Igen|Igen|Igen|Igen|
|Mellékelt biztonsági mentési tároló|1X DB méret|1X DB méret|1X DB méret|1X DB méret|1X DB méret|1X DB méret|

\*A 8 KB és 64 KB közötti i/o-méretek maximális értéke. A tényleges IOPS a munkaterheléstől függ. További részletek: [Data IO Governance](sql-database-resource-limits-database-server.md#resource-governance).

### <a name="gen4-compute-generation-part-2"></a>Gen4 számítási generáció (2. rész)

|Számítási méret|BC_Gen4_7|BC_Gen4_8|BC_Gen4_9|BC_Gen4_10|BC_Gen4_16|BC_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |
|Számításgenerálás|Gen4 (1988)|Gen4 (1988)|Gen4 (1988)|Gen4 (1988)|Gen4 (1988)|Gen4 (1988)|
|virtuális magok|7|8|9|10|16|24|
|Memória (GB)|49|56|63|70|112|159.5|
|Oszlopcentrikus támogatás|Igen|Igen|Igen|Igen|Igen|Igen|
|Memórián belüli OLTP-tároló (GB)|7|8|9.5|11|20|36|
|Tárolási típus|Helyi SSD|Helyi SSD|Helyi SSD|Helyi SSD|Helyi SSD|Helyi SSD|
|Maximális adatméret (GB)|1024|1024|1024|1024|1024|1024|
|Napló maximális mérete (GB)|307|307|307|307|307|307|
|TempDB maximális adatméret (GB)|224|256|288|320|512|768|
|IO késés (hozzávetőleges)|1-2 ms (írás)<br>1-2 ms (olvasás)|1-2 ms (írás)<br>1-2 ms (olvasás)|1-2 ms (írás)<br>1-2 ms (olvasás)|1-2 ms (írás)<br>1-2 ms (olvasás)|1-2 ms (írás)<br>1-2 ms (olvasás)|1-2 ms (írás)<br>1-2 ms (olvasás)|
|Maximális adat IOPS |28,000|32,000|36,000|40,000|64,000|76 800|
|Maximális naplósebesség (Mb/s)|56|64|64|64|64|64|
|Egyidejű dolgozók maximális alkalmazása (kérések)|1400|1600|1800|2000|3200|4800|
|Egyidejű bejelentkezések maximális|1400|1600|1800|2000|3200|4800|
|Egyidejű munkamenetek maximális száma|30,000|30,000|30,000|30,000|30,000|30,000|
|Replikák száma|4|4|4|4|4|4|
|Multi-AZ|Igen|Igen|Igen|Igen|Igen|Igen|
|Olvasási felskálázás|Igen|Igen|Igen|Igen|Igen|Igen|
|Mellékelt biztonsági mentési tároló|1X DB méret|1X DB méret|1X DB méret|1X DB méret|1X DB méret|1X DB méret|

\*A 8 KB és 64 KB közötti i/o-méretek maximális értéke. A tényleges IOPS a munkaterheléstől függ. További részletek: [Data IO Governance](sql-database-resource-limits-database-server.md#resource-governance).

## <a name="business-critical---provisioned-compute---gen5"></a>Üzleti legkritikusabb - kiépített számítás - Gen5

### <a name="gen5-compute-generation-part-1"></a>Gen5 számítási generáció (1. rész)

|Számítási méret|BC_Gen5_2|BC_Gen5_4|BC_Gen5_6|BC_Gen5_8|BC_Gen5_10|BC_Gen5_12|BC_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Számításgenerálás|Gen5 (1998)|Gen5 (1998)|Gen5 (1998)|Gen5 (1998)|Gen5 (1998)|Gen5 (1998)|Gen5 (1998)|
|virtuális magok|2|4|6|8|10|12|14|
|Memória (GB)|10.4|20.8|31.1|41.5|51.9|62.3|72.7|
|Oszlopcentrikus támogatás|Igen|Igen|Igen|Igen|Igen|Igen|Igen|
|Memórián belüli OLTP-tároló (GB)|1.57|3.14|4.71|6.28|8.65|11.02|13.39|
|Maximális adatméret (GB)|1024|1024|1536|1536|1536|3072|3072|
|Napló maximális mérete (GB)|307|307|461|461|461|922|922|
|TempDB maximális adatméret (GB)|64|128|192|256|320|384|448|
|Tárolási típus|Helyi SSD|Helyi SSD|Helyi SSD|Helyi SSD|Helyi SSD|Helyi SSD|Helyi SSD|
|IO késés (hozzávetőleges)|1-2 ms (írás)<br>1-2 ms (olvasás)|1-2 ms (írás)<br>1-2 ms (olvasás)|1-2 ms (írás)<br>1-2 ms (olvasás)|1-2 ms (írás)<br>1-2 ms (olvasás)|1-2 ms (írás)<br>1-2 ms (olvasás)|1-2 ms (írás)<br>1-2 ms (olvasás)|1-2 ms (írás)<br>1-2 ms (olvasás)|
|Maximális adat IOPS *|8000|16000|24,000|32,000|40,000|48,000|56,000|
|Maximális naplósebesség (Mb/s)|24|48|72|96|96|96|96|
|Egyidejű dolgozók maximális alkalmazása (kérések)|200|400|600|800|1000|1200|1400|
|Egyidejű bejelentkezések maximális száma|200|400|600|800|1000|1200|1400|
|Egyidejű munkamenetek maximális száma|30,000|30,000|30,000|30,000|30,000|30,000|30,000|
|Replikák száma|4|4|4|4|4|4|4|
|Multi-AZ|Igen|Igen|Igen|Igen|Igen|Igen|Igen|
|Olvasási felskálázás|Igen|Igen|Igen|Igen|Igen|Igen|Igen|
|Mellékelt biztonsági mentési tároló|1X DB méret|1X DB méret|1X DB méret|1X DB méret|1X DB méret|1X DB méret|1X DB méret|

\*A 8 KB és 64 KB közötti i/o-méretek maximális értéke. A tényleges IOPS a munkaterheléstől függ. További részletek: [Data IO Governance](sql-database-resource-limits-database-server.md#resource-governance).

### <a name="gen5-compute-generation-part-2"></a>Gen5 számítási generáció (2. rész)

|Számítási méret|BC_Gen5_16|BC_Gen5_18|BC_Gen5_20|BC_Gen5_24|BC_Gen5_32|BC_Gen5_40|BC_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Számításgenerálás|Gen5 (1998)|Gen5 (1998)|Gen5 (1998)|Gen5 (1998)|Gen5 (1998)|Gen5 (1998)|Gen5 (1998)|
|virtuális magok|16|18|20|24|32|40|80|
|Memória (GB)|83|93.4|103.8|124.6|166.1|207.6|415.2|
|Oszlopcentrikus támogatás|Igen|Igen|Igen|Igen|Igen|Igen|Igen|
|Memórián belüli OLTP-tároló (GB)|15.77|18.14|20.51|25.25|37.94|52.23|131.64|
|Maximális adatméret (GB)|3072|3072|3072|4096|4096|4096|4096|
|Napló maximális mérete (GB)|922|922|922|1229|1229|1229|1229|
|TempDB maximális adatméret (GB)|512|576|640|768|1024|1280|2560|
|Tárolási típus|Helyi SSD|Helyi SSD|Helyi SSD|Helyi SSD|Helyi SSD|Helyi SSD|Helyi SSD|
|IO késés (hozzávetőleges)|1-2 ms (írás)<br>1-2 ms (olvasás)|1-2 ms (írás)<br>1-2 ms (olvasás)|1-2 ms (írás)<br>1-2 ms (olvasás)|1-2 ms (írás)<br>1-2 ms (olvasás)|1-2 ms (írás)<br>1-2 ms (olvasás)|1-2 ms (írás)<br>1-2 ms (olvasás)|1-2 ms (írás)<br>1-2 ms (olvasás)|
|Maximális adat IOPS *|64,000|72,000|80,000|96,000|128,000|160 000|204,800|
|Maximális naplósebesség (Mb/s)|96|96|96|96|96|96|96|
|Egyidejű dolgozók maximális alkalmazása (kérések)|1600|1800|2000|2400|3200|4000|8000|
|Egyidejű bejelentkezések maximális száma|1600|1800|2000|2400|3200|4000|8000|
|Egyidejű munkamenetek maximális száma|30,000|30,000|30,000|30,000|30,000|30,000|30,000|
|Replikák száma|4|4|4|4|4|4|4|
|Multi-AZ|Igen|Igen|Igen|Igen|Igen|Igen|Igen|
|Olvasási felskálázás|Igen|Igen|Igen|Igen|Igen|Igen|Igen|
|Mellékelt biztonsági mentési tároló|1X DB méret|1X DB méret|1X DB méret|1X DB méret|1X DB méret|1X DB méret|1X DB méret|

\*A 8 KB és 64 KB közötti i/o-méretek maximális értéke. A tényleges IOPS a munkaterheléstől függ. További részletek: [Data IO Governance](sql-database-resource-limits-database-server.md#resource-governance).

## <a name="business-critical---provisioned-compute---m-series"></a>Üzleti legkritikusabb - kiépített számítás - M sorozat

### <a name="m-series-compute-generation-preview"></a>M sorozatú számításgenerálás (előzetes verzió)

|Számítási méret|BC_M_128|
|:--- | --: |
|Számításgenerálás|M sorozat|
|virtuális magok|128|
|Memória (GB)|3767.1|
|Oszlopcentrikus támogatás|Igen|
|Memórián belüli OLTP-tároló (GB)|1768|
|Maximális adatméret (GB)|4096|
|Napló maximális mérete (GB)|2048|
|TempDB maximális adatméret (GB)|4096|
|Tárolási típus|Helyi SSD|
|IO késés (hozzávetőleges)|1-2 ms (írás)<br>1-2 ms (olvasás)|
|Maximális adat IOPS *|160 000|
|Maximális naplósebesség (Mb/s)|264|
|Egyidejű dolgozók maximális alkalmazása (kérések)|12,800|
|Egyidejű bejelentkezések maximális száma|12,800|
|Egyidejű munkamenetek maximális száma|30000|
|Replikák száma|4|
|Multi-AZ|Igen|
|Olvasási felskálázás|Igen|
|Mellékelt biztonsági mentési tároló|1X DB méret|

\*A 8 KB és 64 KB közötti i/o-méretek maximális értéke. A tényleges IOPS a munkaterheléstől függ. További részletek: [Data IO Governance](sql-database-resource-limits-database-server.md#resource-governance).

> [!IMPORTANT]
> Bizonyos körülmények között előfordulhat, hogy a fel nem használt terület visszaszerzéséhez össze kell adnia egy adatbázist. További információt a [Fájlterület kezelése az Azure SQL Database-ben című témakörben talál.](sql-database-file-space-management.md)

## <a name="next-steps"></a>További lépések

- Egyetlen adatbázis DTU erőforrás-korlátaihoz tekintse meg [az egyes adatbázisok erőforráskorlátait a DTU beszerzési modell használatával](sql-database-dtu-resource-limits-single-databases.md)
- A rugalmas készletek virtuálismag-erőforrás-korlátairól lásd: [a virtuálismag-vásárlási modellt használó rugalmas készletek erőforráskorlátai](sql-database-vcore-resource-limits-elastic-pools.md)
- A rugalmas készletek DTU erőforrás-korlátjaihoz tekintse meg [a DTU beszerzési modellt használó rugalmas készletek erőforráskorlátait](sql-database-dtu-resource-limits-elastic-pools.md)
- A felügyelt példányok erőforráskorlátairól a [Felügyelt példány erőforráskorlátai](sql-database-managed-instance-resource-limits.md)t.
- Az általános Azure-korlátokról az [Azure-előfizetési és szolgáltatáskorlátok, kvóták és korlátozások](../azure-resource-manager/management/azure-subscription-service-limits.md)című témakörben talál.
- Az adatbázis-kiszolgálón lévő erőforráskorlátokról a kiszolgálón és az előfizetési szinteken lévő korlátozásokról az [SQL Database-kiszolgálóerőforrás-korlátozások áttekintése című témakörben](sql-database-resource-limits-database-server.md) olvashat.
