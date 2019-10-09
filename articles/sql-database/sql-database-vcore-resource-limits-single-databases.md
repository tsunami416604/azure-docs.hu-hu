---
title: Azure SQL Database virtuális mag-alapú erőforrás-korlátok – önálló adatbázis | Microsoft Docs
description: Ez a lap a Azure SQL Database egyetlen adatbázisának általános virtuális mag-alapú erőforrás-korlátozásait ismerteti.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 08/29/2019
ms.openlocfilehash: ae7baf09df42a5824e5f59e7ebb372f4d9f6350c
ms.sourcegitcommit: f9e81b39693206b824e40d7657d0466246aadd6e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/08/2019
ms.locfileid: "72032866"
---
# <a name="resource-limits-for-single-databases-using-the-vcore-based-purchasing-model"></a>Az virtuális mag-alapú vásárlási modellt használó önálló adatbázisok erőforrás-korlátai

Ez a cikk a virtuális mag-alapú vásárlási modellt használó, Azure SQL Database önálló adatbázisok részletes erőforrás-korlátozásait ismerteti.

A SQL Database-kiszolgálókon található önálló adatbázisokra vonatkozó DTU-alapú beszerzési modellekre vonatkozó korlátokat lásd: [SQL Database-kiszolgálók erőforrás-korlátainak áttekintése](sql-database-resource-limits-database-server.md).

> [!IMPORTANT]
> Bizonyos körülmények között szükség lehet az adatbázis nem használt terület felszabadítását zsugorítani. További információkért lásd: [kezelése az Azure SQL Database területe](sql-database-file-space-management.md).

Az [Azure Portal](sql-database-single-databases-manage.md#manage-an-existing-sql-database-server), a [Transact-SQL](sql-database-single-databases-manage.md#transact-sql-manage-sql-database-servers-and-single-databases), a [PowerShell](sql-database-single-databases-manage.md#powershell-manage-sql-database-servers-and-single-databases), az [Azure CLI](sql-database-single-databases-manage.md#azure-cli-manage-sql-database-servers-and-single-databases)vagy a [REST API](sql-database-single-databases-manage.md#rest-api-manage-sql-database-servers-and-single-databases)használatával megadhatja a szolgáltatási szintet, a számítási méretet és a tárterületet egyetlen adatbázishoz.

> [!IMPORTANT]
> Az útmutatás és a megfontolások méretezésével kapcsolatban lásd: [önálló adatbázis](sql-database-single-database-scale.md)skálázása.

## <a name="general-purpose-service-tier-for-provisioned-compute"></a>általános célú szolgáltatási szintet a kiépített számítási feladatokhoz

> [!IMPORTANT]
> Az új Gen4-adatbázisok már nem támogatottak a Kelet-Ausztrália vagy Brazília déli régiójában.

### <a name="gen4-compute-generation-part-1"></a>Gen4 számítási generációja (1. rész)

|Számítási méret|GP_Gen4_1|GP_Gen4_2|GP_Gen4_3|GP_Gen4_4|GP_Gen4_5|GP_Gen4_6
|:--- | --: |--: |--: |--: |--: |--: |
|Számítási generáció|Gen4|Gen4|Gen4|Gen4|Gen4|Gen4|
|Virtuális mag|1|2|3|4|5|6|
|Memória (GB)|7|14|21|28|35|42|
|Oszlopcentrikus-támogatás|Igen|Igen|Igen|Igen|Igen|Igen|
|Memóriában tárolt OLTP-tároló (GB)|–|N/A|N/A|N/A|N/A|–|
|Maximális adatméret (GB)|1024|1024|1024|1536|1536|1536|
|Napló maximális mérete (GB)|307|307|307|461|461|461|
|TempDB maximális adatméret (GB)|32|64|96|128|160|192|
|Tárolási típus|Távoli SSD|Távoli SSD|Távoli SSD|Távoli SSD|Távoli SSD|Távoli SSD|
|IO-késés (becsült)|5-7 MS (írás)<br>5-10 MS (olvasás)|5-7 MS (írás)<br>5-10 MS (olvasás)|5-7 MS (írás)<br>5-10 MS (olvasás)|5-7 MS (írás)<br>5-10 MS (olvasás)|5-7 MS (írás)<br>5-10 MS (olvasás)|5-7 MS (írás)<br>5-10 MS (olvasás)|
|Maximális adatmennyiség IOPS (64 KB)|500|1000|1500|2000|2500|3000|
|Maximális naplózási arány (MBps)|3.75|7.5|11.25|15|18,75|22.5|
|Egyidejű feldolgozók maximális száma (kérelem)|200|400|600|800|1000|1200|
|Egyidejű munkamenetek maximális száma|30000|30000|30000|30000|30000|30000|
|Replikák száma|1|1|1|1|1|1|
|Több – AZ|–|N/A|N/A|N/A|N/A|–|
|Felskálázás|–|N/A|N/A|N/A|N/A|–|
|Mellékelt biztonsági mentési tár|1X DB méret|1X DB méret|1X DB méret|1X DB méret|1X DB méret|1X DB méret|

### <a name="gen4-compute-generation-part-2"></a>Gen4 számítási generációja (2. rész)

|Számítási méret|GP_Gen4_7|GP_Gen4_8|GP_Gen4_9|GP_Gen4_10|GP_Gen4_16|GP_Gen4_24
|:--- | --: |--: |--: |--: |--: |--: |
|Számítási generáció|Gen4|Gen4|Gen4|Gen4|Gen4|Gen4|
|Virtuális mag|7|8|9|10|16|24|
|Memória (GB)|49|56|63|70|112|168|
|Oszlopcentrikus-támogatás|Igen|Igen|Igen|Igen|Igen|Igen|
|Memóriában tárolt OLTP-tároló (GB)|–|N/A|N/A|N/A|N/A|–|
|Maximális adatméret (GB)|1536|3072|3072|3072|4096|4096|
|Napló maximális mérete (GB)|461|922|922|922|1229|1229|
|TempDB maximális adatméret (GB)|224|256|288|320|384|384|
|Tárolási típus|Távoli SSD|Távoli SSD|Távoli SSD|Távoli SSD|Távoli SSD|Távoli SSD|
|IO-késés (becsült)|5-7 MS (írás)<br>5-10 MS (olvasás)|5-7 MS (írás)<br>5-10 MS (olvasás)|5-7 MS (írás)<br>5-10 MS (olvasás)|5-7 MS (írás)<br>5-10 MS (olvasás)|5-7 MS (írás)<br>5-10 MS (olvasás)|5-7 MS (írás)<br>5-10 MS (olvasás)
|Maximális adatmennyiség IOPS (64 KB)|3500|4000|4500|5000|8000|12000|
|Maximális naplózási arány (MBps)|26,25|30|30|30|30|30|
|Egyidejű feldolgozók maximális száma (kérelem)|1400|1600|1800|2000|3200|4800|
|Egyidejű munkamenetek maximális száma|30000|30000|30000|30000|30000|30000|
|Replikák száma|1|1|1|1|1|1|
|Több – AZ|–|N/A|N/A|N/A|N/A|–|
|Felskálázás|–|N/A|N/A|N/A|N/A|–|
|Mellékelt biztonsági mentési tár|1X DB méret|1X DB méret|1X DB méret|1X DB méret|1X DB méret|1X DB méret|

### <a name="gen5-compute-generation-part-1"></a>Gen5 számítási generációja (1. rész)

|Számítási méret|GP_Gen5_2|GP_Gen5_4|GP_Gen5_6|GP_Gen5_8|GP_Gen5_10|GP_Gen5_12|GP_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Számítási generáció|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|Virtuális mag|2|4|6|8|10|12|14|
|Memória (GB)|10.2|20,4|30,6|40,8|51|61,2|71,4|
|Oszlopcentrikus-támogatás|Igen|Igen|Igen|Igen|Igen|Igen|Igen|
|Memóriában tárolt OLTP-tároló (GB)|–|N/A|N/A|N/A|N/A|N/A|–|
|Maximális adatméret (GB)|1024|1024|1536|1536|1536|3072|3072|
|Napló maximális mérete (GB)|307|307|307|461|461|461|461|
|TempDB maximális adatméret (GB)|64|128|192|256|320|384|384|
|Tárolási típus|Távoli SSD|Távoli SSD|Távoli SSD|Távoli SSD|Távoli SSD|Távoli SSD|Távoli SSD|
|IO-késés (becsült)|5-7 MS (írás)<br>5-10 MS (olvasás)|5-7 MS (írás)<br>5-10 MS (olvasás)|5-7 MS (írás)<br>5-10 MS (olvasás)|5-7 MS (írás)<br>5-10 MS (olvasás)|5-7 MS (írás)<br>5-10 MS (olvasás)|5-7 MS (írás)<br>5-10 MS (olvasás)|5-7 MS (írás)<br>5-10 MS (olvasás)|
|Maximális adatmennyiség IOPS (64 KB)|1000|2000|3000|4000|5000|6000|7000|
|Maximális naplózási arány (MBps)|3.75|7.5|11.25|15|18,75|22.5|26,25|
|Egyidejű feldolgozók maximális száma (kérelem)|200|400|600|800|1000|1200|1400|
|Egyidejű munkamenetek maximális száma|30000|30000|30000|30000|30000|30000|30000|
|Replikák száma|1|1|1|1|1|1|1|
|Több – AZ|–|N/A|N/A|N/A|N/A|N/A|–|
|Felskálázás|–|N/A|N/A|N/A|N/A|N/A|–|
|Mellékelt biztonsági mentési tár|1X DB méret|1X DB méret|1X DB méret|1X DB méret|1X DB méret|1X DB méret|1X DB méret|

### <a name="gen5-compute-generation-part-2"></a>Gen5 számítási generációja (2. rész)

|Számítási méret|GP_Gen5_16|GP_Gen5_18|GP_Gen5_20|GP_Gen5_24|GP_Gen5_32|GP_Gen5_40|GP_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Számítási generáció|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|Virtuális mag|16|18|20|24|32|40|80|
|Memória (GB)|81,6|91,8|102|122,4|163,2|204|408|
|Oszlopcentrikus-támogatás|Igen|Igen|Igen|Igen|Igen|Igen|Igen|
|Memóriában tárolt OLTP-tároló (GB)|–|N/A|N/A|N/A|N/A|N/A|–|
|Maximális adatméret (GB)|3072|3072|3072|4096|4096|4096|4096|
|Napló maximális mérete (GB)|922|922|922|1229|1229|1229|1229|
|TempDB maximális adatméret (GB)|384|384|384|384|384|384|384|
|Tárolási típus|Távoli SSD|Távoli SSD|Távoli SSD|Távoli SSD|Távoli SSD|Távoli SSD|Távoli SSD|
|IO-késés (becsült)|5-7 MS (írás)<br>5-10 MS (olvasás)|5-7 MS (írás)<br>5-10 MS (olvasás)|5-7 MS (írás)<br>5-10 MS (olvasás)|5-7 MS (írás)<br>5-10 MS (olvasás)|5-7 MS (írás)<br>5-10 MS (olvasás)|5-7 MS (írás)<br>5-10 MS (olvasás)|5-7 MS (írás)<br>5-10 MS (olvasás)|
|Maximális adatmennyiség IOPS (64 KB)|8000|9000|10000|12000|16000|20000|40000|
|Maximális naplózási arány (MBps)|30|30|30|30|30|30|30|
|Egyidejű feldolgozók maximális száma (kérelem)|1600|1800|2000|2400|3200|4000|8000|
|Egyidejű munkamenetek maximális száma|30000|30000|30000|30000|30000|30000|30000|
|Replikák száma|1|1|1|1|1|1|1|
|Több – AZ|–|N/A|N/A|N/A|N/A|N/A|–|
|Felskálázás|–|N/A|N/A|N/A|N/A|N/A|–|
|Mellékelt biztonsági mentési tár|1X DB méret|1X DB méret|1X DB méret|1X DB méret|1X DB méret|1X DB méret|1X DB méret|

## <a name="general-purpose-service-tier-for-serverless-compute"></a>általános célú szolgáltatási szintet kiszolgáló nélküli számítási feladatokhoz

A [kiszolgáló nélküli számítási rétegek](sql-database-serverless.md) előzetes verzióban érhetők el.

### <a name="gen5-compute-generation-part-1"></a>Gen5 számítási generációja (1. rész)

|Számítási méret|GP_S_Gen5_1|GP_S_Gen5_2|GP_S_Gen5_4|GP_S_Gen5_6|GP_S_Gen5_8|
|:--- | --: |--: |--: |--: |--: |
|Számítási generáció|Gen5|Gen5|Gen5|Gen5|Gen5|
|Minimális – maximális virtuális mag|0,5 – 1|0,5 – 2|0,5 – 4|0,75 – 6|1.0 – 8|
|Minimális memória maximális mérete (GB)|2.02 – 3|2.05 – 6|2.10-12|2,25 – 18|3,00 – 24|
|Automatikus szüneteltetés minimális késleltetése (perc)|60|60|60|60|60|
|Oszlopcentrikus-támogatás|Igen|Igen|Igen|Igen|Igen|
|Memóriában tárolt OLTP-tároló (GB)|–|N/A|N/A|N/A|–|
|Maximális adatméret (GB)|512|1024|1024|1024|1536|
|Napló maximális mérete (GB)|154|307|307|307|461|
|TempDB maximális adatméret (GB)|32|64|128|192|256|
|Tárolási típus|Távoli SSD|Távoli SSD|Távoli SSD|Távoli SSD|Távoli SSD|
|IO-késés (becsült)|5-7 MS (írás)<br>5-10 MS (olvasás)|5-7 MS (írás)<br>5-10 MS (olvasás)|5-7 MS (írás)<br>5-10 MS (olvasás)|5-7 MS (írás)<br>5-10 MS (olvasás)|5-7 MS (írás)<br>5-10 MS (olvasás)|
|Maximális adatmennyiség IOPS (64 KB)|500|1000|2000|3000|4000|
|Maximális naplózási arány (MBps)|2.5|5.6|10|15|20|
|Egyidejű feldolgozók maximális száma (kérelem)|75|150|300|450|600|
|Egyidejű munkamenetek maximális száma|30000|30000|30000|30000|30000|
|Replikák száma|1|1|1|1|1|
|Több – AZ|–|N/A|N/A|N/A|–|
|Felskálázás|–|N/A|N/A|N/A|–|
|Mellékelt biztonsági mentési tár|1X DB méret|1X DB méret|1X DB méret|1X DB méret|1X DB méret|

### <a name="gen5-compute-generation-part-2"></a>Gen5 számítási generációja (2. rész)

|Számítási méret|GP_S_Gen5_10|GP_S_Gen5_12|GP_S_Gen5_14|GP_S_Gen5_16|
|:--- | --: |--: |--: |--: |
|Számítási generáció|Gen5|Gen5|Gen5|Gen5|
|Minimális – maximális virtuális mag|1,25 – 10|1,50 – 12|1,75 – 14|2,00 – 16|
|Minimális memória maximális mérete (GB)|3,75 – 30|4.50 – 36|5.25 – 42|6,00 – 48|
|Automatikus szüneteltetés minimális késleltetése (perc)|60|60|60|60|
|Oszlopcentrikus-támogatás|Igen|Igen|Igen|Igen|
|Memóriában tárolt OLTP-tároló (GB)|–|N/A|N/A|–|
|Maximális adatméret (GB)|1536|1536|1536|3072|
|Napló maximális mérete (GB)|461|461|461|922|
|TempDB maximális adatméret (GB)|320|384|448|512|
|Tárolási típus|Távoli SSD|Távoli SSD|Távoli SSD|Távoli SSD|
|IO-késés (becsült)|5-7 MS (írás)<br>5-10 MS (olvasás)|5-7 MS (írás)<br>5-10 MS (olvasás)|5-7 MS (írás)<br>5-10 MS (olvasás)|5-7 MS (írás)<br>5-10 MS (olvasás)|
|Maximális adatmennyiség IOPS (64 KB)|5000|6000|7000|8000|
|Maximális naplózási arány (MBps)|20|20|20|20|
|Egyidejű feldolgozók maximális száma (kérelem)|750|900|1050|1200|
|Egyidejű munkamenetek maximális száma|30000|30000|30000|30000|
|Replikák száma|1|1|1|1|
|Több – AZ|–|N/A|N/A|–|
|Felskálázás|–|N/A|N/A|–|
|Mellékelt biztonsági mentési tár|1X DB méret|1X DB méret|1X DB méret|1X DB méret|

## <a name="business-critical-service-tier-for-provisioned-compute"></a>üzletileg kritikus szolgáltatási szintet a kiépített számítási feladatokhoz

> [!IMPORTANT]
> Az új Gen4-adatbázisok már nem támogatottak a Kelet-Ausztrália vagy Brazília déli régiójában.

### <a name="gen4-compute-generation-part-1"></a>Gen4 számítási generációja (1. rész)

|Számítási méret|BC_Gen4_1|BC_Gen4_2|BC_Gen4_3|BC_Gen4_4|BC_Gen4_5|BC_Gen4_6|
|:--- | --: |--: |--: |--: |--: |--: |
|Számítási generáció|Gen4|Gen4|Gen4|Gen4|Gen4|Gen4|
|Virtuális mag|1|2|3|4|5|6|
|Memória (GB)|7|14|21|28|35|42|
|Oszlopcentrikus-támogatás|Igen|Igen|Igen|Igen|Igen|Igen|
|Memóriában tárolt OLTP-tároló (GB)|1|2|3|4|5|6|
|Tárolási típus|Helyi SSD|Helyi SSD|Helyi SSD|Helyi SSD|Helyi SSD|Helyi SSD|
|Maximális adatméret (GB)|650|650|650|650|650|650|
|Napló maximális mérete (GB)|195|195|195|195|195|195|
|TempDB maximális adatméret (GB)|32|64|96|128|160|192|
|IO-késés (becsült)|1-2 MS (írás)<br>1-2 MS (olvasás)|1-2 MS (írás)<br>1-2 MS (olvasás)|1-2 MS (írás)<br>1-2 MS (olvasás)|1-2 MS (írás)<br>1-2 MS (olvasás)|1-2 MS (írás)<br>1-2 MS (olvasás)|1-2 MS (írás)<br>1-2 MS (olvasás)|
|Maximális adatmennyiség IOPS (64 KB)|5000|10000|15 000|20000|25000|30000|
|Maximális naplózási arány (MBps)|8|16|24|32|40|48|
|Egyidejű feldolgozók maximális száma (kérelem)|200|400|600|800|1000|1200|
|Egyidejű bejelentkezések maximális száma|200|400|600|800|1000|1200|
|Egyidejű munkamenetek maximális száma|30000|30000|30000|30000|30000|30000|
|Replikák száma|4|4|4|4|4|4|
|Több – AZ|Igen|Igen|Igen|Igen|Igen|Igen|
|Felskálázás|Igen|Igen|Igen|Igen|Igen|Igen|
|Mellékelt biztonsági mentési tár|1X DB méret|1X DB méret|1X DB méret|1X DB méret|1X DB méret|1X DB méret|

### <a name="gen4-compute-generation-part-2"></a>Gen4 számítási generációja (2. rész)

|Számítási méret|BC_Gen4_7|BC_Gen4_8|BC_Gen4_9|BC_Gen4_10|BC_Gen4_16|BC_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |
|Számítási generáció|Gen4|Gen4|Gen4|Gen4|Gen4|Gen4|
|Virtuális mag|7|8|9|10|16|24|
|Memória (GB)|49|56|63|70|112|168|
|Oszlopcentrikus-támogatás|Igen|Igen|Igen|Igen|Igen|Igen|
|Memóriában tárolt OLTP-tároló (GB)|7|8|9.5|11|20|36|
|Tárolási típus|Helyi SSD|Helyi SSD|Helyi SSD|Helyi SSD|Helyi SSD|Helyi SSD|
|Maximális adatméret (GB)|650|650|650|650|1024|1024|
|Napló maximális mérete (GB)|195|195|195|195|307|307|
|TempDB maximális adatméret (GB)|224|256|288|320|384|384|
|IO-késés (becsült)|1-2 MS (írás)<br>1-2 MS (olvasás)|1-2 MS (írás)<br>1-2 MS (olvasás)|1-2 MS (írás)<br>1-2 MS (olvasás)|1-2 MS (írás)<br>1-2 MS (olvasás)|1-2 MS (írás)<br>1-2 MS (olvasás)|1-2 MS (írás)<br>1-2 MS (olvasás)|
|Maximális adatmennyiség IOPS (64 KB)|35000|40000|45000|50000|80000|120000|
|Maximális naplózási arány (MBps)|56|64|64|64|64|64|
|Egyidejű feldolgozók maximális száma (kérelem)|1400|1600|1800|2000|3200|4800|
|Egyidejű bejelentkezések maximális száma (kérelmek)|1400|1600|1800|2000|3200|4800|
|Egyidejű munkamenetek maximális száma|30000|30000|30000|30000|30000|30000|
|Replikák száma|4|4|4|4|4|4|
|Több – AZ|Igen|Igen|Igen|Igen|Igen|Igen|
|Felskálázás|Igen|Igen|Igen|Igen|Igen|Igen|
|Mellékelt biztonsági mentési tár|1X DB méret|1X DB méret|1X DB méret|1X DB méret|1X DB méret|1X DB méret|

### <a name="gen5-compute-compute-part-1"></a>Gen5 számítási számítás (1. rész)

|Számítási méret|BC_Gen5_2|BC_Gen5_4|BC_Gen5_6|BC_Gen5_8|BC_Gen5_10|BC_Gen5_12|BC_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Számítási generáció|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|Virtuális mag|2|4|6|8|10|12|14|
|Memória (GB)|10.2|20,4|30,6|40,8|51|61,2|71,4|
|Oszlopcentrikus-támogatás|Igen|Igen|Igen|Igen|Igen|Igen|Igen|
|Memóriában tárolt OLTP-tároló (GB)|1,571|3,142|4,713|6,284|8,655|11,026|13,397|
|Maximális adatméret (GB)|1024|1024|1536|1536|1536|3072|3072|
|Napló maximális mérete (GB)|307|307|307|461|461|922|922|
|TempDB maximális adatméret (GB)|64|128|192|256|320|384|384|
|Tárolási típus|Helyi SSD|Helyi SSD|Helyi SSD|Helyi SSD|Helyi SSD|Helyi SSD|Helyi SSD|
|IO-késés (becsült)|1-2 MS (írás)<br>1-2 MS (olvasás)|1-2 MS (írás)<br>1-2 MS (olvasás)|1-2 MS (írás)<br>1-2 MS (olvasás)|1-2 MS (írás)<br>1-2 MS (olvasás)|1-2 MS (írás)<br>1-2 MS (olvasás)|1-2 MS (írás)<br>1-2 MS (olvasás)|1-2 MS (írás)<br>1-2 MS (olvasás)|
|Maximális adatmennyiség IOPS (64 KB)|8000|16000|24000|32000|40000|48000|56000|
|Maximális naplózási arány (MBps)|12|24|36|48|60|72|84|
|Egyidejű feldolgozók maximális száma (kérelem)|200|400|600|800|1000|1200|1400|
|Egyidejű bejelentkezések maximális száma|200|400|600|800|1000|1200|1400|
|Egyidejű munkamenetek maximális száma|30000|30000|30000|30000|30000|30000|30000|
|Replikák száma|4|4|4|4|4|4|4|
|Több – AZ|Igen|Igen|Igen|Igen|Igen|Igen|Igen|
|Felskálázás|Igen|Igen|Igen|Igen|Igen|Igen|Igen|
|Mellékelt biztonsági mentési tár|1X DB méret|1X DB méret|1X DB méret|1X DB méret|1X DB méret|1X DB méret|1X DB méret|

### <a name="gen5-compute-generation-part-2"></a>Gen5 számítási generációja (2. rész)

|Számítási méret|BC_Gen5_16|BC_Gen5_18|BC_Gen5_20|BC_Gen5_24|BC_Gen5_32|BC_Gen5_40|BC_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Számítási generáció|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|Virtuális mag|16|18|20|24|32|40|80|
|Memória (GB)|81,6|91,8|102|122,4|163,2|204|408|
|Oszlopcentrikus-támogatás|Igen|Igen|Igen|Igen|Igen|Igen|Igen|
|Memóriában tárolt OLTP-tároló (GB)|15.768|18,139|20,51|25,252|37,936|52,22|131,64|
|Maximális adatméret (GB)|3072|3072|3072|4096|4096|4096|4096|
|Napló maximális mérete (GB)|922|922|922|1229|1229|1229|1229|
|TempDB maximális adatméret (GB)|384|384|384|384|384|384|384|
|Tárolási típus|Helyi SSD|Helyi SSD|Helyi SSD|Helyi SSD|Helyi SSD|Helyi SSD|Helyi SSD|
|IO-késés (becsült)|1-2 MS (írás)<br>1-2 MS (olvasás)|1-2 MS (írás)<br>1-2 MS (olvasás)|1-2 MS (írás)<br>1-2 MS (olvasás)|1-2 MS (írás)<br>1-2 MS (olvasás)|1-2 MS (írás)<br>1-2 MS (olvasás)|1-2 MS (írás)<br>1-2 MS (olvasás)|1-2 MS (írás)<br>1-2 MS (olvasás)|
|Maximális adatmennyiség IOPS (64 KB)|64000|72000|80000|96000|128000|160000|320000|
|Maximális naplózási arány (MBps)|96|96|96|96|96|96|96|
|Egyidejű feldolgozók maximális száma (kérelem)|1600|1800|2000|2400|3200|4000|8000|
|Egyidejű bejelentkezések maximális száma|1600|1800|2000|2400|3200|4000|8000|
|Egyidejű munkamenetek maximális száma|30000|30000|30000|30000|30000|30000|30000|
|Replikák száma|4|4|4|4|4|4|4|
|Több – AZ|Igen|Igen|Igen|Igen|Igen|Igen|Igen|
|Felskálázás|Igen|Igen|Igen|Igen|Igen|Igen|Igen|
|Mellékelt biztonsági mentési tár|1X DB méret|1X DB méret|1X DB méret|1X DB méret|1X DB méret|1X DB méret|1X DB méret|

## <a name="hyperscale-service-tier-for-provisioned-compute"></a>Nagy kapacitású szolgáltatási szintje kiépített számítási feladatokhoz

### <a name="gen5-compute-generation"></a>Gen5 számítási generáció

|Teljesítményszint|HS_Gen5_2|HS_Gen5_4|HS_Gen5_8|HS_Gen5_16|HS_Gen5_24|HS_Gen5_32|HS_Gen5_40|HS_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |--: |
|Számítási generáció|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|Virtuális mag|2|4|8|16|24|32|40|80|
|Memória (GB)|10.2|20,4|40,8|81,6|122,4|163,2|204|408|
|[RBPEX](sql-database-service-tier-hyperscale.md#compute) Méret|3X memória|3X memória|3X memória|3X memória|3X memória|3X memória|3X memória|3X memória|
|Oszlopcentrikus-támogatás|Igen|Igen|Igen|Igen|Igen|Igen|Igen|Igen|
|Memóriában tárolt OLTP-tároló (GB)|–|N/A|N/A|N/A|N/A|N/A|N/A|–|
|Maximális adatméret (TB)|100 |100 |100 |100 |100 |100 |100 |100 |
|Napló maximális mérete (TB)|1 |1 |1 |1 |1 |1 |1 |1 |
|TempDB maximális adatméret (GB)|64|128|256|384|384|384|384|384|
|Tárolási típus| [1. Megjegyzés](#notes) |[1. Megjegyzés](#notes)|[1. Megjegyzés](#notes) |[1. Megjegyzés](#notes) |[1. Megjegyzés](#notes) |[1. Megjegyzés](#notes) |[1. Megjegyzés](#notes) | [1. Megjegyzés](#notes) |
|Maximális adatmennyiség IOPS (64 KB)|[2. Megjegyzés](#notes)|[2. Megjegyzés](#notes)|[2. Megjegyzés](#notes)|[2. Megjegyzés](#notes)|[2. Megjegyzés](#notes)|[2. Megjegyzés](#notes)|[2. Megjegyzés](#notes)|[2. Megjegyzés](#notes)|
|IO-késés (becsült)|[3. Megjegyzés](#notes)|[3. Megjegyzés](#notes)|[3. Megjegyzés](#notes)|[3. Megjegyzés](#notes)|[3. Megjegyzés](#notes)|[3. Megjegyzés](#notes)|[3. Megjegyzés](#notes)|[3. Megjegyzés](#notes)|
|Egyidejű feldolgozók maximális száma (kérelem)|200|400|800|1600|2400|3200|4000|8000|
|Egyidejű munkamenetek maximális száma|30000|30000|30000|30000|30000|30000|30000|30000|
|Másodlagos replikák|0-4|0-4|0-4|0-4|0-4|0-4|0-4|0-4|
|Több – AZ|–|N/A|N/A|N/A|N/A|N/A|N/A|–|
|Felskálázás|Igen|Igen|Igen|Igen|Igen|Igen|Igen|Igen|
|Biztonsági mentési tár megőrzése|7 nap|7 nap|7 nap|7 nap|7 nap|7 nap|7 nap|7 nap|
|||

#### <a name="notes"></a>Megjegyzések

**1. Megjegyzés**: A nagy kapacitású egy többrétegű architektúra, külön számítási és tárolási összetevőkkel: [Nagy kapacitású szolgáltatási szintek architektúrája](sql-database-service-tier-hyperscale.md#distributed-functions-architecture)

**2. Megjegyzés**: A nagy kapacitású többrétegű architektúrák több szinten is gyorsítótárazást igényelnek. A hatékony IOPS a munkaterheléstől függ.

**3. Megjegyzés**: A késés 1-2 MS a RBPEX SSD-alapú gyorsítótárban lévő adatokhoz a számítási replikák esetében, amely a leggyakrabban használt adatlapokat gyorsítótárazza. A lapozófájlokból beolvasott adatok nagyobb késése.

## <a name="next-steps"></a>További lépések

- Egyetlen adatbázis DTU erőforrás-korlátaival kapcsolatban lásd: [önálló adatbázisok erőforrás-korlátai a DTU-alapú vásárlási modell használatával](sql-database-dtu-resource-limits-single-databases.md)
- A rugalmas készletek virtuális mag erőforrás-korlátaival kapcsolatban lásd: [rugalmas készletek erőforrás-korlátai a virtuális mag-alapú vásárlási modell használatával](sql-database-vcore-resource-limits-elastic-pools.md)
- A rugalmas készletek DTU erőforrás-korlátaival kapcsolatban lásd: [rugalmas készletek erőforrás-korlátai a DTU-alapú vásárlási modell használatával](sql-database-dtu-resource-limits-elastic-pools.md)
- A felügyelt példányok erőforrás-korlátaival kapcsolatban lásd: [felügyelt példányok erőforrás-korlátai](sql-database-managed-instance-resource-limits.md).
- Az általános Azure-korlátokkal kapcsolatos információkért lásd: [Azure-előfizetések és-szolgáltatások korlátai, kvótái és megkötései](../azure-subscription-service-limits.md).
- Az adatbázis-kiszolgálók erőforrás-korlátaival kapcsolatos információkért tekintse meg a kiszolgáló és az előfizetési szint korlátaival kapcsolatos információkat a [SQL Database kiszolgálók erőforrás-korlátainak áttekintése](sql-database-resource-limits-database-server.md) című témakörben.
