---
title: virtuálismagos erőforráskorlátok – rugalmas készletek
description: Ez a lap néhány gyakori virtuálismag-erőforrás-korlátokat ismertet az Azure SQL Database rugalmas készletei számára.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: carlrab, sstein
ms.date: 03/03/2020
ms.openlocfilehash: b3c5594b8eef76dcb57903408dd1e77c96890eab
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80346270"
---
# <a name="resource-limits-for-elastic-pools-using-the-vcore-purchasing-model"></a>A virtuálismag-vásárlási modellt használó rugalmas készletek erőforráskorlátai

Ez a cikk az Azure SQL Database rugalmas készletek és a virtuálismag-vásárlási modell használatával készletezett adatbázisok részletes erőforrás-korlátokat tartalmaz.

A DTU beszerzési modell korlátok, lásd: [SQL Database DTU erőforrás korlátok - rugalmas készletek.](sql-database-dtu-resource-limits-elastic-pools.md)

> [!IMPORTANT]
> Bizonyos körülmények között előfordulhat, hogy a fel nem használt terület visszaszerzéséhez össze kell adnia egy adatbázist. További információt a [Fájlterület kezelése az Azure SQL Database-ben című témakörben talál.](sql-database-file-space-management.md)

Beállíthatja a szolgáltatási szint, a számítási méret és a tárolási összeg az [Azure Portalon,](sql-database-elastic-pool-manage.md#azure-portal-manage-elastic-pools-and-pooled-databases) [a PowerShell,](sql-database-elastic-pool-manage.md#powershell-manage-elastic-pools-and-pooled-databases)az [Azure CLI](sql-database-elastic-pool-manage.md#azure-cli-manage-elastic-pools-and-pooled-databases)vagy a [REST API használatával.](sql-database-elastic-pool-manage.md#rest-api-manage-elastic-pools-and-pooled-databases)

> [!IMPORTANT]
> A méretezési útmutatóés szempontok, [lásd: Rugalmas készlet méretezése](sql-database-elastic-pool-scale.md)

## <a name="general-purpose---provisioned-compute---gen4"></a>Általános célú - kiépített számítás - Gen4

> [!IMPORTANT]
> Az új Gen4-adatbázisok már nem támogatottak az Ausztrália keleti vagy brazíliai déli régióiban.

### <a name="general-purpose-service-tier-generation-4-compute-platform-part-1"></a>Általános célú szolgáltatási szint: 4. generációs számítási platform (1. rész)

|Számítási méret|GP_Gen4_1|GP_Gen4_2|GP_Gen4_3|GP_Gen4_4|GP_Gen4_5|GP_Gen4_6
|:--- | --: |--: |--: |--: |--: |--: |
|Számításgenerálás|Gen4 (1988)|Gen4 (1988)|Gen4 (1988)|Gen4 (1988)|Gen4 (1988)|Gen4 (1988)|
|virtuális magok|1|2|3|4|5|6|
|Memória (GB)|7|14|21|28|35|42|
|Maximális db-szám készletenként <sup>1</sup>|100|200|500|500|500|500|
|Oszlopcentrikus támogatás|Igen|Igen|Igen|Igen|Igen|Igen|
|Memórián belüli OLTP-tároló (GB)|N/A|N/A|N/A|N/A|N/A|N/A|
|Maximális adatméret (GB)|512|756|1536|1536|1536|2048|
|Napló maximális mérete|154|227|461|461|461|614|
|TempDB maximális adatméret (GB)|32|64|96|128|160|192|
|Tárolási típus|Prémium (távoli) tárhely|Prémium (távoli) tárhely|Prémium (távoli) tárhely|Prémium (távoli) tárhely|Prémium (távoli) tárhely|Prémium (távoli) tárhely|
|IO késés (hozzávetőleges)|5-7 ms (írás)<br>5-10 ms (olvasás)|5-7 ms (írás)<br>5-10 ms (olvasás)|5-7 ms (írás)<br>5-10 ms (olvasás)|5-7 ms (írás)<br>5-10 ms (olvasás)|5-7 ms (írás)<br>5-10 ms (olvasás)|5-7 ms (írás)<br>5-10 ms (olvasás)|
|Maximális adat IOPS készletenként <sup>2</sup> |400|800|1200|1600|2000|2400|
|Maximális naplósebesség készletenként (MBps)|4.7|9.4 verzió|14.1|18.8|23.4|28.1|
|Egyidejű dolgozók maximális százaléka készletenként (kérés) <sup>3</sup> |210|420|630|840|1050|1260|
|Egyidejű bejelentkezések maximális növekedése készletenként <sup>3</sup> |210|420|630|840|1050|1260|
|Egyidejű munkamenetek maximális száma|30,000|30,000|30,000|30,000|30,000|30,000|
|Min/max rugalmas készlet virtuálismag-választási lehetőségek adatbázisonként|0, 0.25, 0.5, 1|0, 0.25, 0.5, 1, 2|0, 0.25, 0.5, 1...3|0, 0.25, 0.5, 1...4|0, 0.25, 0.5, 1...5|0, 0.25, 0.5, 1...6|
|Replikák száma|1|1|1|1|1|1|
|Multi-AZ|N/A|N/A|N/A|N/A|N/A|N/A|
|Olvasási felskálázás|N/A|N/A|N/A|N/A|N/A|N/A|
|Mellékelt biztonsági mentési tároló|1X DB méret|1X DB méret|1X DB méret|1X DB méret|1X DB méret|1X DB méret|

<sup>1</sup> További szempontokat lásd: [Erőforrás-kezelés sűrű rugalmas készletekben.](sql-database-elastic-pool-resource-management.md)

<sup>2</sup> A 8 KB és 64 KB közötti i/o-méretek maximális értéke. A tényleges IOPS a munkaterheléstől függ. További részletek: [Data IO Governance](sql-database-resource-limits-database-server.md#resource-governance).

<sup>3</sup> Az egyes adatbázisok hoz a maximális egyidejű dolgozók (kérelmek) esetében [lásd: Egyetlen adatbázis-erőforrás-korlátok](sql-database-vcore-resource-limits-single-databases.md). Ha például a rugalmas készlet Gen5-öt használ, és az adatbázisonkénti maximális virtuális mag értéke 2, akkor a maximális egyidejű dolgozók értéke 200.  Ha az adatbázisonkénti maximális virtuális mag értéke 0,5, akkor a maximális egyidejű dolgozók értéke 50, mivel a Gen5-ön virtuálismagonként legfeljebb 100 egyidejű dolgozó van. Az adatbázisonkénti, legfeljebb 1 virtuális maggal kevesebb virtuális mag esetén a maximális egyidejű dolgozók száma hasonlóképpen átméretezve van.

### <a name="general-purpose-service-tier-generation-4-compute-platform-part-2"></a>Általános célú szolgáltatási szint: 4. generációs számítási platform (2. rész)

|Számítási méret|GP_Gen4_7|GP_Gen4_8|GP_Gen4_9|GP_Gen4_10|GP_Gen4_16|GP_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |
|Számításgenerálás|Gen4 (1988)|Gen4 (1988)|Gen4 (1988)|Gen4 (1988)|Gen4 (1988)|Gen4 (1988)|
|virtuális magok|7|8|9|10|16|24|
|Memória (GB)|49|56|63|70|112|159.5|
|Maximális db-szám készletenként <sup>1</sup>|500|500|500|500|500|500|
|Oszlopcentrikus támogatás|Igen|Igen|Igen|Igen|Igen|Igen|
|Memórián belüli OLTP-tároló (GB)|N/A|N/A|N/A|N/A|N/A|N/A|
|Maximális adatméret (GB)|2048|2048|2048|2048|3584|4096|
|Napló maximális mérete (GB)|614|614|614|614|1075|1229|
|TempDB maximális adatméret (GB)|224|256|288|320|512|768|
|Tárolási típus|Prémium (távoli) tárhely|Prémium (távoli) tárhely|Prémium (távoli) tárhely|Prémium (távoli) tárhely|Prémium (távoli) tárhely|Prémium (távoli) tárhely|
|IO késés (hozzávetőleges)|5-7 ms (írás)<br>5-10 ms (olvasás)|5-7 ms (írás)<br>5-10 ms (olvasás)|5-7 ms (írás)<br>5-10 ms (olvasás)|5-7 ms (írás)<br>5-10 ms (olvasás)|5-7 ms (írás)<br>5-10 ms (olvasás)|5-7 ms (írás)<br>5-10 ms (olvasás)|
|Maximális adat IOPS készletenként <sup>2</sup>|2800|3200|3600|4000|6400|9600|
|Maximális naplósebesség készletenként (MBps)|32.8|37.5|37.5|37.5|37.5|37.5|
|Egyidejű dolgozók maximális százaléka készletenként (kérés) <sup>3</sup>|1470|1680|1890|2100|3360|5040|
|Maximális egyidejű bejelentkezési készlet (kérések) <sup>3</sup>|1470|1680|1890|2100|3360|5040|
|Egyidejű munkamenetek maximális száma|30,000|30,000|30,000|30,000|30,000|30,000|
|Min/max rugalmas készlet virtuálismag-választási lehetőségek adatbázisonként|0, 0.25, 0.5, 1...7|0, 0.25, 0.5, 1...8|0, 0.25, 0.5, 1...9|0, 0.25, 0.5, 1...10|0, 0.25, 0.5, 1...10, 16|0, 0.25, 0.5, 1...10, 16, 24|
|Replikák száma|1|1|1|1|1|1|
|Multi-AZ|N/A|N/A|N/A|N/A|N/A|N/A|
|Olvasási felskálázás|N/A|N/A|N/A|N/A|N/A|N/A|
|Mellékelt biztonsági mentési tároló|1X DB méret|1X DB méret|1X DB méret|1X DB méret|1X DB méret|1X DB méret|

<sup>1</sup> További szempontokat lásd: [Erőforrás-kezelés sűrű rugalmas készletekben.](sql-database-elastic-pool-resource-management.md)

<sup>2</sup> A 8 KB és 64 KB közötti i/o-méretek maximális értéke. A tényleges IOPS a munkaterheléstől függ. További részletek: [Data IO Governance](sql-database-resource-limits-database-server.md#resource-governance).    

<sup>3</sup> Az egyes adatbázisok hoz a maximális egyidejű dolgozók (kérelmek) esetében [lásd: Egyetlen adatbázis-erőforrás-korlátok](sql-database-vcore-resource-limits-single-databases.md). Ha például a rugalmas készlet Gen5-öt használ, és az adatbázisonkénti maximális virtuális mag értéke 2, akkor a maximális egyidejű dolgozók értéke 200.  Ha az adatbázisonkénti maximális virtuális mag értéke 0,5, akkor a maximális egyidejű dolgozók értéke 50, mivel a Gen5-ön virtuálismagonként legfeljebb 100 egyidejű dolgozó van. Az adatbázisonkénti, legfeljebb 1 virtuális maggal kevesebb virtuális mag esetén a maximális egyidejű dolgozók száma hasonlóképpen átméretezve van.

## <a name="general-purpose---provisioned-compute---gen5"></a>Általános cél - kiépített számítás - Gen5

### <a name="general-purpose-service-tier-generation-5-compute-platform-part-1"></a>Általános célú szolgáltatási szint: 5. generációs számítási platform (1. rész)

|Számítási méret|GP_Gen5_2|GP_Gen5_4|GP_Gen5_6|GP_Gen5_8|GP_Gen5_10|GP_Gen5_12|GP_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Számításgenerálás|Gen5 (1998)|Gen5 (1998)|Gen5 (1998)|Gen5 (1998)|Gen5 (1998)|Gen5 (1998)|Gen5 (1998)|
|virtuális magok|2|4|6|8|10|12|14|
|Memória (GB)|10.4|20.8|31.1|41.5|51.9|62.3|72.7|
|Maximális db-szám készletenként <sup>1</sup>|100|200|500|500|500|500|500|
|Oszlopcentrikus támogatás|Igen|Igen|Igen|Igen|Igen|Igen|Igen|
|Memórián belüli OLTP-tároló (GB)|N/A|N/A|N/A|N/A|N/A|N/A|N/A|
|Maximális adatméret (GB)|512|756|1536|1536|1536|2048|2048|
|Napló maximális mérete (GB)|154|227|461|461|461|614|614|
|TempDB maximális adatméret (GB)|64|128|192|256|320|384|448|
|Tárolási típus|Prémium (távoli) tárhely|Prémium (távoli) tárhely|Prémium (távoli) tárhely|Prémium (távoli) tárhely|Prémium (távoli) tárhely|Prémium (távoli) tárhely|Prémium (távoli) tárhely|
|IO késés (hozzávetőleges)|5-7 ms (írás)<br>5-10 ms (olvasás)|5-7 ms (írás)<br>5-10 ms (olvasás)|5-7 ms (írás)<br>5-10 ms (olvasás)|5-7 ms (írás)<br>5-10 ms (olvasás)|5-7 ms (írás)<br>5-10 ms (olvasás)|5-7 ms (írás)<br>5-10 ms (olvasás)|5-7 ms (írás)<br>5-10 ms (olvasás)|
|Maximális adat IOPS készletenként <sup>2</sup>|800|1600|2400|3200|4000|4800|5600|
|Maximális naplósebesség készletenként (MBps)|9.4 verzió|18.8|28.1|37.5|37.5|37.5|37.5|
|Egyidejű dolgozók maximális százaléka készletenként (kérés) <sup>3</sup>|210|420|630|840|1050|1260|1470|
|Összesített egyidejű bejelentkezések készletenként (kérések) <sup>3</sup>|210|420|630|840|1050|1260|1470|
|Egyidejű munkamenetek maximális száma|30,000|30,000|30,000|30,000|30,000|30,000|30,000|
|Min/max rugalmas készlet virtuálismag-választási lehetőségek adatbázisonként|0, 0.25, 0.5, 1, 2|0, 0.25, 0.5, 1...4|0, 0.25, 0.5, 1...6|0, 0.25, 0.5, 1...8|0, 0.25, 0.5, 1...10|0, 0.25, 0.5, 1...12|0, 0.25, 0.5, 1...14|
|Replikák száma|1|1|1|1|1|1|1|
|Multi-AZ|N/A|N/A|N/A|N/A|N/A|N/A|N/A|
|Olvasási felskálázás|N/A|N/A|N/A|N/A|N/A|N/A|N/A|
|Mellékelt biztonsági mentési tároló|1X DB méret|1X DB méret|1X DB méret|1X DB méret|1X DB méret|1X DB méret|1X DB méret|

<sup>1</sup> További szempontokat lásd: [Erőforrás-kezelés sűrű rugalmas készletekben.](sql-database-elastic-pool-resource-management.md)

<sup>2</sup> A 8 KB és 64 KB közötti i/o-méretek maximális értéke. A tényleges IOPS a munkaterheléstől függ. További részletek: [Data IO Governance](sql-database-resource-limits-database-server.md#resource-governance).

<sup>3</sup> Az egyes adatbázisok hoz a maximális egyidejű dolgozók (kérelmek) esetében [lásd: Egyetlen adatbázis-erőforrás-korlátok](sql-database-vcore-resource-limits-single-databases.md). Ha például a rugalmas készlet Gen5-öt használ, és az adatbázisonkénti maximális virtuális mag értéke 2, akkor a maximális egyidejű dolgozók értéke 200.  Ha az adatbázisonkénti maximális virtuális mag értéke 0,5, akkor a maximális egyidejű dolgozók értéke 50, mivel a Gen5-ön virtuálismagonként legfeljebb 100 egyidejű dolgozó van. Az adatbázisonkénti, legfeljebb 1 virtuális maggal kevesebb virtuális mag esetén a maximális egyidejű dolgozók száma hasonlóképpen átméretezve van.

### <a name="general-purpose-service-tier-generation-5-compute-platform-part-2"></a>Általános célú szolgáltatási szint: 5. generációs számítási platform (2. rész)

|Számítási méret|GP_Gen5_16|GP_Gen5_18|GP_Gen5_20|GP_Gen5_24|GP_Gen5_32|GP_Gen5_40|GP_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Számításgenerálás|Gen5 (1998)|Gen5 (1998)|Gen5 (1998)|Gen5 (1998)|Gen5 (1998)|Gen5 (1998)|Gen5 (1998)|
|virtuális magok|16|18|20|24|32|40|80|
|Memória (GB)|83|93.4|103.8|124.6|166.1|207.6|415.2|
|Maximális db-szám készletenként <sup>1</sup>|500|500|500|500|500|500|500|
|Oszlopcentrikus támogatás|Igen|Igen|Igen|Igen|Igen|Igen|Igen|
|Memórián belüli OLTP-tároló (GB)|N/A|N/A|N/A|N/A|N/A|N/A|N/A|
|Maximális adatméret (GB)|2048|3072|3072|3072|4096|4096|4096|
|Napló maximális mérete (GB)|614|922|922|922|1229|1229|1229|
|TempDB maximális adatméret (GB)|512|576|640|768|1024|1280|2560|
|Tárolási típus|Prémium (távoli) tárhely|Prémium (távoli) tárhely|Prémium (távoli) tárhely|Prémium (távoli) tárhely|Prémium (távoli) tárhely|Prémium (távoli) tárhely|Prémium (távoli) tárhely|
|IO késés (hozzávetőleges)|5-7 ms (írás)<br>5-10 ms (olvasás)|5-7 ms (írás)<br>5-10 ms (olvasás)|5-7 ms (írás)<br>5-10 ms (olvasás)|5-7 ms (írás)<br>5-10 ms (olvasás)|5-7 ms (írás)<br>5-10 ms (olvasás)|5-7 ms (írás)<br>5-10 ms (olvasás)|5-7 ms (írás)<br>5-10 ms (olvasás)|
|Maximális adat IOPS készletenként <sup>2</sup> |6,400|7,200|8,000|9600|12,800|16000|32,000|
|Maximális naplósebesség készletenként (MBps)|37.5|37.5|37.5|37.5|37.5|37.5|37.5|
|Egyidejű dolgozók maximális százaléka készletenként (kérés) <sup>3</sup>|1680|1890|2100|2520|3360|4200|8400|
|Összesített egyidejű bejelentkezések készletenként (kérések) <sup>3</sup>|1680|1890|2100|2520|3360|4200|8400|
|Egyidejű munkamenetek maximális száma|30,000|30,000|30,000|30,000|30,000|30,000|30,000|
|Min/max rugalmas készlet virtuálismag-választási lehetőségek adatbázisonként|0, 0.25, 0.5, 1...16|0, 0.25, 0.5, 1...18|0, 0.25, 0.5, 1...20|0, 0.25, 0.5, 1...20, 24|0, 0.25, 0.5, 1...20, 24, 32|0, 0.25, 0.5, 1...16, 24, 32, 40|0, 0.25, 0.5, 1...16, 24, 32, 40, 80|
|Replikák száma|1|1|1|1|1|1|1|
|Multi-AZ|N/A|N/A|N/A|N/A|N/A|N/A|N/A|
|Olvasási felskálázás|N/A|N/A|N/A|N/A|N/A|N/A|N/A|
|Mellékelt biztonsági mentési tároló|1X DB méret|1X DB méret|1X DB méret|1X DB méret|1X DB méret|1X DB méret|1X DB méret|

<sup>1</sup> További szempontokat lásd: [Erőforrás-kezelés sűrű rugalmas készletekben.](sql-database-elastic-pool-resource-management.md)

<sup>2</sup> A 8 KB és 64 KB közötti i/o-méretek maximális értéke. A tényleges IOPS a munkaterheléstől függ. További részletek: [Data IO Governance](sql-database-resource-limits-database-server.md#resource-governance).

<sup>3</sup> Az egyes adatbázisok hoz a maximális egyidejű dolgozók (kérelmek) esetében [lásd: Egyetlen adatbázis-erőforrás-korlátok](sql-database-vcore-resource-limits-single-databases.md). Ha például a rugalmas készlet Gen5-öt használ, és az adatbázisonkénti maximális virtuális mag értéke 2, akkor a maximális egyidejű dolgozók értéke 200.  Ha az adatbázisonkénti maximális virtuális mag értéke 0,5, akkor a maximális egyidejű dolgozók értéke 50, mivel a Gen5-ön virtuálismagonként legfeljebb 100 egyidejű dolgozó van. Az adatbázisonkénti, legfeljebb 1 virtuális maggal kevesebb virtuális mag esetén a maximális egyidejű dolgozók száma hasonlóképpen átméretezve van.

## <a name="general-purpose---provisioned-compute---fsv2-series"></a>Általános célú - kiépített számítás - Fsv2 sorozat

### <a name="fsv2-series-compute-generation-preview"></a>Fsv2 sorozatú számításgenerálás (előzetes verzió)

|Számítási méret|GP_Fsv2_72|
|:--- | --: |
|Számításgenerálás|Fsv2 sorozat|
|virtuális magok|72|
|Memória (GB)|136.2|
|Maximális db-szám készletenként <sup>1</sup>|500|
|Oszlopcentrikus támogatás|Igen|
|Memórián belüli OLTP-tároló (GB)|N/A|
|Maximális adatméret (GB)|4096|
|Napló maximális mérete (GB)|1024|
|TempDB maximális adatméret (GB)|333|
|Tárolási típus|Prémium (távoli) tárhely|
|IO késés (hozzávetőleges)|5-7 ms (írás)<br>5-10 ms (olvasás)|
|Maximális adat IOPS készletenként <sup>2</sup>|16000|
|Maximális naplósebesség készletenként (MBps)|37.5|
|Egyidejű dolgozók maximális százaléka készletenként (kérés) <sup>3</sup>|3780|
|Összesített egyidejű bejelentkezések készletenként (kérések) <sup>3</sup>|3780|
|Egyidejű munkamenetek maximális száma|30,000|
|Min/max rugalmas készlet virtuálismag-választási lehetőségek adatbázisonként|0-72|
|Replikák száma|1|
|Multi-AZ|N/A|
|Olvasási felskálázás|N/A|
|Mellékelt biztonsági mentési tároló|1X DB méret|

<sup>1</sup> További szempontokat lásd: [Erőforrás-kezelés sűrű rugalmas készletekben.](sql-database-elastic-pool-resource-management.md)

<sup>2</sup> A 8 KB és 64 KB közötti i/o-méretek maximális értéke. A tényleges IOPS a munkaterheléstől függ. További részletek: [Data IO Governance](sql-database-resource-limits-database-server.md#resource-governance).

<sup>3</sup> Az egyes adatbázisok hoz a maximális egyidejű dolgozók (kérelmek) esetében [lásd: Egyetlen adatbázis-erőforrás-korlátok](sql-database-vcore-resource-limits-single-databases.md). Ha például a rugalmas készlet Gen5-öt használ, és az adatbázisonkénti maximális virtuális mag értéke 2, akkor a maximális egyidejű dolgozók értéke 200.  Ha az adatbázisonkénti maximális virtuális mag értéke 0,5, akkor a maximális egyidejű dolgozók értéke 50, mivel a Gen5-ön virtuálismagonként legfeljebb 100 egyidejű dolgozó van. Az adatbázisonkénti, legfeljebb 1 virtuális maggal kevesebb virtuális mag esetén a maximális egyidejű dolgozók száma hasonlóképpen átméretezve van.

## <a name="business-critical---provisioned-compute---gen4"></a>Üzleti legkritikusabb - kiépített számítás - Gen4

> [!IMPORTANT]
> Az új Gen4-adatbázisok már nem támogatottak az Ausztrália keleti vagy brazíliai déli régióiban.

### <a name="business-critical-service-tier-generation-4-compute-platform-part-1"></a>Üzleti legkritikusabb szolgáltatási szint: 4. generációs számítási platform (1. rész)

|Számítási méret|BC_Gen4_2|BC_Gen4_3|BC_Gen4_4|BC_Gen4_5|BC_Gen4_6|
|:--- | --: |--: |--: |--: |--: |--: |
|Számításgenerálás|Gen4 (1988)|Gen4 (1988)|Gen4 (1988)|Gen4 (1988)|Gen4 (1988)|
|virtuális magok|2|3|4|5|6|
|Memória (GB)|14|21|28|35|42|
|Maximális db-szám készletenként <sup>1</sup>|50|100|100|100|100|
|Oszlopcentrikus támogatás|Igen|Igen|Igen|Igen|Igen|
|Memórián belüli OLTP-tároló (GB)|2|3|4|5|6|
|Tárolási típus|Helyi SSD|Helyi SSD|Helyi SSD|Helyi SSD|Helyi SSD|
|Maximális adatméret (GB)|1024|1024|1024|1024|1024|
|Napló maximális mérete (GB)|307|307|307|307|307|
|TempDB maximális adatméret (GB)|64|96|128|160|192|
|IO késés (hozzávetőleges)|1-2 ms (írás)<br>1-2 ms (olvasás)|1-2 ms (írás)<br>1-2 ms (olvasás)|1-2 ms (írás)<br>1-2 ms (olvasás)|1-2 ms (írás)<br>1-2 ms (olvasás)|1-2 ms (írás)<br>1-2 ms (olvasás)|
|Maximális adat IOPS készletenként <sup>2</sup>|9000|13,500|18000|22,500|27,000|
|Maximális naplósebesség készletenként (MBps)|20|30|40|50|60|
|Egyidejű dolgozók maximális százaléka készletenként (kérés) <sup>3</sup>|420|630|840|1050|1260|
|Összesített egyidejű bejelentkezések készletenként (kérések) <sup>3</sup>|420|630|840|1050|1260|
|Egyidejű munkamenetek maximális száma|30,000|30,000|30,000|30,000|30,000|
|Min/max rugalmas készlet virtuálismag-választási lehetőségek adatbázisonként|0, 0.25, 0.5, 1, 2|0, 0.25, 0.5, 1...3|0, 0.25, 0.5, 1...4|0, 0.25, 0.5, 1...5|0, 0.25, 0.5, 1...6|
|Replikák száma|4|4|4|4|4|
|Multi-AZ|Igen|Igen|Igen|Igen|Igen|
|Olvasási felskálázás|Igen|Igen|Igen|Igen|Igen|
|Mellékelt biztonsági mentési tároló|1X DB méret|1X DB méret|1X DB méret|1X DB méret|1X DB méret|

<sup>1</sup> További szempontokat lásd: [Erőforrás-kezelés sűrű rugalmas készletekben.](sql-database-elastic-pool-resource-management.md)

<sup>2</sup> A 8 KB és 64 KB közötti i/o-méretek maximális értéke. A tényleges IOPS a munkaterheléstől függ. További részletek: [Data IO Governance](sql-database-resource-limits-database-server.md#resource-governance).

<sup>3</sup> Az egyes adatbázisok hoz a maximális egyidejű dolgozók (kérelmek) esetében [lásd: Egyetlen adatbázis-erőforrás-korlátok](sql-database-vcore-resource-limits-single-databases.md). Ha például a rugalmas készlet Gen5-öt használ, és az adatbázisonkénti maximális virtuális mag értéke 2, akkor a maximális egyidejű dolgozók értéke 200.  Ha az adatbázisonkénti maximális virtuális mag értéke 0,5, akkor a maximális egyidejű dolgozók értéke 50, mivel a Gen5-ön virtuálismagonként legfeljebb 100 egyidejű dolgozó van. Az adatbázisonkénti, legfeljebb 1 virtuális maggal kevesebb virtuális mag esetén a maximális egyidejű dolgozók száma hasonlóképpen átméretezve van.

### <a name="business-critical-service-tier-generation-4-compute-platform-part-2"></a>Üzleti legkritikusabb szolgáltatási szint: 4. generációs számítási platform (2. rész)

|Számítási méret|BC_Gen4_7|BC_Gen4_8|BC_Gen4_9|BC_Gen4_10|BC_Gen4_16|BC_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |
|Számításgenerálás|Gen4 (1988)|Gen4 (1988)|Gen4 (1988)|Gen4 (1988)|Gen4 (1988)|Gen4 (1988)|
|virtuális magok|7|8|9|10|16|24|
|Memória (GB)|49|56|63|70|112|159.5|
|Maximális db-szám készletenként <sup>1</sup>|100|100|100|100|100|100|
|Oszlopcentrikus támogatás|N/A|N/A|N/A|N/A|N/A|N/A|
|Memórián belüli OLTP-tároló (GB)|7|8|9.5|11|20|36|
|Tárolási típus|Helyi SSD|Helyi SSD|Helyi SSD|Helyi SSD|Helyi SSD|Helyi SSD|
|Maximális adatméret (GB)|1024|1024|1024|1024|1024|1024|
|Napló maximális mérete (GB)|307|307|307|307|307|307|
|TempDB maximális adatméret (GB)|224|256|288|320|512|768|
|IO késés (hozzávetőleges)|1-2 ms (írás)<br>1-2 ms (olvasás)|1-2 ms (írás)<br>1-2 ms (olvasás)|1-2 ms (írás)<br>1-2 ms (olvasás)|1-2 ms (írás)<br>1-2 ms (olvasás)|1-2 ms (írás)<br>1-2 ms (olvasás)|1-2 ms (írás)<br>1-2 ms (olvasás)|
|Maximális adat IOPS készletenként <sup>2</sup>|31,500|36,000|40,500|45.000|72,000|96,000|
|Maximális naplósebesség készletenként (MBps)|70|80|80|80|80|80|
|Egyidejű dolgozók maximális százaléka készletenként (kérés) <sup>3</sup>|1470|1680|1890|2100|3360|5040|
|Összesített egyidejű bejelentkezések készletenként (kérések) <sup>3</sup>|1470|1680|1890|2100|3360|5040|
|Egyidejű munkamenetek maximális száma|30,000|30,000|30,000|30,000|30,000|30,000|
|Min/max rugalmas készlet virtuálismag-választási lehetőségek adatbázisonként|0, 0.25, 0.5, 1...7|0, 0.25, 0.5, 1...8|0, 0.25, 0.5, 1...9|0, 0.25, 0.5, 1...10|0, 0.25, 0.5, 1...10, 16|0, 0.25, 0.5, 1...10, 16, 24|
|Replikák száma|4|4|4|4|4|4|
|Multi-AZ|Igen|Igen|Igen|Igen|Igen|Igen|
|Olvasási felskálázás|Igen|Igen|Igen|Igen|Igen|Igen|
|Mellékelt biztonsági mentési tároló|1X DB méret|1X DB méret|1X DB méret|1X DB méret|1X DB méret|1X DB méret|

<sup>1</sup> További szempontokat lásd: [Erőforrás-kezelés sűrű rugalmas készletekben.](sql-database-elastic-pool-resource-management.md)

<sup>2</sup> A 8 KB és 64 KB közötti i/o-méretek maximális értéke. A tényleges IOPS a munkaterheléstől függ. További részletek: [Data IO Governance](sql-database-resource-limits-database-server.md#resource-governance).

<sup>3</sup> Az egyes adatbázisok hoz a maximális egyidejű dolgozók (kérelmek) esetében [lásd: Egyetlen adatbázis-erőforrás-korlátok](sql-database-vcore-resource-limits-single-databases.md). Ha például a rugalmas készlet Gen5-öt használ, és az adatbázisonkénti maximális virtuális mag értéke 2, akkor a maximális egyidejű dolgozók értéke 200.  Ha az adatbázisonkénti maximális virtuális mag értéke 0,5, akkor a maximális egyidejű dolgozók értéke 50, mivel a Gen5-ön virtuálismagonként legfeljebb 100 egyidejű dolgozó van. Az adatbázisonkénti, legfeljebb 1 virtuális maggal kevesebb virtuális mag esetén a maximális egyidejű dolgozók száma hasonlóképpen átméretezve van.

## <a name="business-critical---provisioned-compute---gen5"></a>Üzleti legkritikusabb - kiépített számítás - Gen5

### <a name="business-critical-service-tier-generation-5-compute-platform-part-1"></a>Üzleti legkritikusabb szolgáltatási szint: 5. generációs számítási platform (1. rész)

|Számítási méret|BC_Gen5_4|BC_Gen5_6|BC_Gen5_8|BC_Gen5_10|BC_Gen5_12|BC_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Számításgenerálás|Gen5 (1998)|Gen5 (1998)|Gen5 (1998)|Gen5 (1998)|Gen5 (1998)|Gen5 (1998)|
|virtuális magok|4|6|8|10|12|14|
|Memória (GB)|20.8|31.1|41.5|51.9|62.3|72.7|
|Maximális db-szám készletenként <sup>1</sup>|50|100|100|100|100|100|
|Oszlopcentrikus támogatás|Igen|Igen|Igen|Igen|Igen|Igen|
|Memórián belüli OLTP-tároló (GB)|3.14|4.71|6.28|8.65|11.02|13.39|
|Maximális adatméret (GB)|1024|1536|1536|1536|3072|3072|
|Napló maximális mérete (GB)|307|307|461|461|922|922|
|TempDB maximális adatméret (GB)|128|192|256|320|384|448|
|Tárolási típus|Helyi SSD|Helyi SSD|Helyi SSD|Helyi SSD|Helyi SSD|Helyi SSD|
|IO késés (hozzávetőleges)|1-2 ms (írás)<br>1-2 ms (olvasás)|1-2 ms (írás)<br>1-2 ms (olvasás)|1-2 ms (írás)<br>1-2 ms (olvasás)|1-2 ms (írás)<br>1-2 ms (olvasás)|1-2 ms (írás)<br>1-2 ms (olvasás)|1-2 ms (írás)<br>1-2 ms (olvasás)|
|Maximális adat IOPS készletenként <sup>2</sup>|18000|27,000|36,000|45.000|54,000|63,000|
|Maximális naplósebesség készletenként (MBps)|60|90|120|120|120|120|
|Egyidejű dolgozók maximális százaléka készletenként (kérés) <sup>3</sup>|420|630|840|1050|1260|1470|
|Összesített egyidejű bejelentkezések készletenként (kérések) <sup>3</sup>|420|630|840|1050|1260|1470|
|Egyidejű munkamenetek maximális száma|30,000|30,000|30,000|30,000|30,000|30,000|
|Min/max rugalmas készlet virtuálismag-választási lehetőségek adatbázisonként|0, 0.25, 0.5, 1...4|0, 0.25, 0.5, 1...6|0, 0.25, 0.5, 1...8|0, 0.25, 0.5, 1...10|0, 0.25, 0.5, 1...12|0, 0.25, 0.5, 1...14|
|Replikák száma|4|4|4|4|4|4|
|Multi-AZ|Igen|Igen|Igen|Igen|Igen|Igen|
|Olvasási felskálázás|Igen|Igen|Igen|Igen|Igen|Igen|
|Mellékelt biztonsági mentési tároló|1X DB méret|1X DB méret|1X DB méret|1X DB méret|1X DB méret|1X DB méret|

<sup>1</sup> További szempontokat lásd: [Erőforrás-kezelés sűrű rugalmas készletekben.](sql-database-elastic-pool-resource-management.md)

<sup>2</sup> A 8 KB és 64 KB közötti i/o-méretek maximális értéke. A tényleges IOPS a munkaterheléstől függ. További részletek: [Data IO Governance](sql-database-resource-limits-database-server.md#resource-governance).

<sup>3</sup> Az egyes adatbázisok hoz a maximális egyidejű dolgozók (kérelmek) esetében [lásd: Egyetlen adatbázis-erőforrás-korlátok](sql-database-vcore-resource-limits-single-databases.md). Ha például a rugalmas készlet Gen5-öt használ, és az adatbázisonkénti maximális virtuális mag értéke 2, akkor a maximális egyidejű dolgozók értéke 200.  Ha az adatbázisonkénti maximális virtuális mag értéke 0,5, akkor a maximális egyidejű dolgozók értéke 50, mivel a Gen5-ön virtuálismagonként legfeljebb 100 egyidejű dolgozó van. Az adatbázisonkénti, legfeljebb 1 virtuális maggal kevesebb virtuális mag esetén a maximális egyidejű dolgozók száma hasonlóképpen átméretezve van.

### <a name="business-critical-service-tier-generation-5-compute-platform-part-2"></a>Üzleti legkritikusabb szolgáltatási szint: 5. generációs számítási platform (2. rész)

|Számítási méret|BC_Gen5_16|BC_Gen5_18|BC_Gen5_20|BC_Gen5_24|BC_Gen5_32|BC_Gen5_40|BC_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Számításgenerálás|Gen5 (1998)|Gen5 (1998)|Gen5 (1998)|Gen5 (1998)|Gen5 (1998)|Gen5 (1998)|Gen5 (1998)|
|virtuális magok|16|18|20|24|32|40|80|
|Memória (GB)|83|93.4|103.8|124.6|166.1|207.6|415.2|
|Maximális db-szám készletenként <sup>1</sup>|100|100|100|100|100|100|100|
|Oszlopcentrikus támogatás|Igen|Igen|Igen|Igen|Igen|Igen|Igen|
|Memórián belüli OLTP-tároló (GB)|15.77|18.14|20.51|25.25|37.94|52.23|131.68|
|Maximális adatméret (GB)|3072|3072|3072|4096|4096|4096|4096|
|Napló maximális mérete (GB)|922|922|922|1229|1229|1229|1229|
|TempDB maximális adatméret (GB)|512|576|640|768|1024|1280|2560|
|Tárolási típus|Helyi SSD|Helyi SSD|Helyi SSD|Helyi SSD|Helyi SSD|Helyi SSD|Helyi SSD|
|IO késés (hozzávetőleges)|1-2 ms (írás)<br>1-2 ms (olvasás)|1-2 ms (írás)<br>1-2 ms (olvasás)|1-2 ms (írás)<br>1-2 ms (olvasás)|1-2 ms (írás)<br>1-2 ms (olvasás)|1-2 ms (írás)<br>1-2 ms (olvasás)|1-2 ms (írás)<br>1-2 ms (olvasás)|1-2 ms (írás)<br>1-2 ms (olvasás)|
|Maximális adat IOPS készletenként <sup>2</sup>|72,000|81,000|90,000|108,000|144,000|180,000|256,000|
|Maximális naplósebesség készletenként (MBps)|120|120|120|120|120|120|120|
|Egyidejű dolgozók maximális százaléka készletenként (kérés) <sup>3</sup>|1680|1890|2100|2520|3360|4200|8400|
|Összesített egyidejű bejelentkezések készletenként (kérések) <sup>3</sup>|1680|1890|2100|2520|3360|4200|8400|
|Egyidejű munkamenetek maximális száma|30,000|30,000|30,000|30,000|30,000|30,000|30,000|
|Min/max rugalmas készlet virtuálismag-választási lehetőségek adatbázisonként|0, 0.25, 0.5, 1...16|0, 0.25, 0.5, 1...18|0, 0.25, 0.5, 1...20|0, 0.25, 0.5, 1...20, 24|0, 0.25, 0.5, 1...20, 24, 32|0, 0.25, 0.5, 1...20, 24, 32, 40|0, 0.25, 0.5, 1...20, 24, 32, 40, 80|
|Replikák száma|4|4|4|4|4|4|4|
|Multi-AZ|Igen|Igen|Igen|Igen|Igen|Igen|Igen|
|Olvasási felskálázás|Igen|Igen|Igen|Igen|Igen|Igen|Igen|
|Mellékelt biztonsági mentési tároló|1X DB méret|1X DB méret|1X DB méret|1X DB méret|1X DB méret|1X DB méret|1X DB méret|

<sup>1</sup> További szempontokat lásd: [Erőforrás-kezelés sűrű rugalmas készletekben.](sql-database-elastic-pool-resource-management.md)

<sup>2</sup> A 8 KB és 64 KB közötti i/o-méretek maximális értéke. A tényleges IOPS a munkaterheléstől függ. További részletek: [Data IO Governance](sql-database-resource-limits-database-server.md#resource-governance).

<sup>3</sup> Az egyes adatbázisok hoz a maximális egyidejű dolgozók (kérelmek) esetében [lásd: Egyetlen adatbázis-erőforrás-korlátok](sql-database-vcore-resource-limits-single-databases.md). Ha például a rugalmas készlet Gen5-öt használ, és az adatbázisonkénti maximális virtuális mag értéke 2, akkor a maximális egyidejű dolgozók értéke 200.  Ha az adatbázisonkénti maximális virtuális mag értéke 0,5, akkor a maximális egyidejű dolgozók értéke 50, mivel a Gen5-ön virtuálismagonként legfeljebb 100 egyidejű dolgozó van. Az adatbázisonkénti, legfeljebb 1 virtuális maggal kevesebb virtuális mag esetén a maximális egyidejű dolgozók száma hasonlóképpen átméretezve van.

## <a name="business-critical---provisioned-compute---m-series"></a>Üzleti legkritikusabb - kiépített számítás - M sorozat

### <a name="m-series-compute-generation-preview"></a>M sorozatú számításgenerálás (előzetes verzió)

|Számítási méret|BC_M_128|
|:--- | --: |
|Számításgenerálás|M sorozat|
|virtuális magok|128|
|Memória (GB)|3767.1|
|Maximális db-szám készletenként <sup>1</sup>|100|
|Oszlopcentrikus támogatás|Igen|
|Memórián belüli OLTP-tároló (GB)|1768|
|Maximális adatméret (GB)|4096|
|Napló maximális mérete (GB)|2048|
|TempDB maximális adatméret (GB)|4096|
|Tárolási típus|Helyi SSD|
|IO késés (hozzávetőleges)|1-2 ms (írás)<br>1-2 ms (olvasás)|
|Maximális adat IOPS készletenként <sup>2</sup>|200,000|
|Maximális naplósebesség készletenként (MBps)|333|
|Egyidejű dolgozók maximális százaléka készletenként (kérés) <sup>3</sup>|13,440|
|Összesített egyidejű bejelentkezések készletenként (kérések) <sup>3</sup>|13,440|
|Egyidejű munkamenetek maximális száma|30,000|
|Min/max rugalmas készlet virtuálismag-választási lehetőségek adatbázisonként|0-128|
|Replikák száma|4|
|Multi-AZ|Igen|
|Olvasási felskálázás|Igen|
|Mellékelt biztonsági mentési tároló|1X DB méret|

<sup>1</sup> További szempontokat lásd: [Erőforrás-kezelés sűrű rugalmas készletekben.](sql-database-elastic-pool-resource-management.md)

<sup>2</sup> A 8 KB és 64 KB közötti i/o-méretek maximális értéke. A tényleges IOPS a munkaterheléstől függ. További részletek: [Data IO Governance](sql-database-resource-limits-database-server.md#resource-governance).

<sup>3</sup> Az egyes adatbázisok hoz a maximális egyidejű dolgozók (kérelmek) esetében [lásd: Egyetlen adatbázis-erőforrás-korlátok](sql-database-vcore-resource-limits-single-databases.md). Ha például a rugalmas készlet Gen5-öt használ, és az adatbázisonkénti maximális virtuális mag értéke 2, akkor a maximális egyidejű dolgozók értéke 200.  Ha az adatbázisonkénti maximális virtuális mag értéke 0,5, akkor a maximális egyidejű dolgozók értéke 50, mivel a Gen5-ön virtuálismagonként legfeljebb 100 egyidejű dolgozó van. Az adatbázisonkénti, legfeljebb 1 virtuális maggal kevesebb virtuális mag esetén a maximális egyidejű dolgozók száma hasonlóképpen átméretezve van.

Ha egy rugalmas készlet összes virtuális magja foglalt, akkor a készlet minden adatbázisa azonos mennyiségű számítási erőforrást kap a lekérdezések feldolgozásához. Az SQL Database szolgáltatás egyenlő erőforrás-megosztást biztosít az adatbázisok között azáltal, hogy mindegyiküknek egyenlő szeleteket ad a számítási időből. Rugalmas készlet erőforrás-megosztási méltányosság mellett minden erőforrás egyébként garantált minden adatbázis, ha a virtuális mag min adatbázisonként van beállítva, hogy nem nulla érték.

## <a name="database-properties-for-pooled-databases"></a>Készletezésű adatbázisok adatbázis-tulajdonságai

Az alábbi táblázat a készletezett adatbázisok tulajdonságait ismerteti.

> [!NOTE]
> A rugalmas készletekben lévő egyes adatbázisok erőforráskorlátai általában megegyeznek az azonos számítási méretű készleten kívüli egyes adatbázisok erőforráskorlátaival. Egy GP_Gen4_1 adatbázis maximális egyidejű dolgozóinak maximális egyidejű dolgozója például 200 dolgozó. Tehát a GP_Gen4_1 halmazadatbázis maximális egyidejű dolgozói nak 200 dolgozója is van. Megjegyzés: GP_Gen4_1 állományban lévő egyidejű dolgozók teljes száma 210.

| Tulajdonság | Leírás |
|:--- |:--- |
| Maximális virtuális magok adatbázisonként |A készletben lévő adatbázisok által használható virtuális magok maximális száma, ha a készlet más adatbázisainak kihasználtsága alapján elérhető. Az adatbázisonkénti maximális virtuális magok nem garantálják az adatbázis erőforrás-garanciáját. Ez a beállítás egy globális beállítás, amely a készletben található minden adatbázisra vonatkozik. Állítsa be a maximális virtuális magadatbázis-ananelég magas ahhoz, hogy kezelni csúcsok adatbázis-kihasználtság. Bizonyos fokú túlzott véglegesítése várható, mivel a készlet általában feltételezi, hogy az adatbázisok, ahol az összes adatbázis nem éri el egyidejűleg.|
| Min virtuális magok adatbázisonként |A készletben lévő adatbázisok által garantált virtuális magok minimális száma. Ez a beállítás egy globális beállítás, amely a készletben található minden adatbázisra vonatkozik. Az adatbázisonkénti min virtuális magok értéke 0 lehet, és egyben az alapértelmezett érték is. Ez a tulajdonság értéke adatbázisonként 0 és az átlagos virtuális magok átlagos kihasználtsága között van beállítva. A készletben lévő adatbázisok és a min virtuális magok adatbázisonkénti szorzata nem haladhatja meg a készletenkénti virtuális magokat.|
| Maximális tárhely adatbázisonként |A készletben lévő adatbázis felhasználó által megadott maximális adatbázismérete. A készletezésű adatbázisok megosztják a lefoglalt készlettárolót, így az adatbázis által elérhető méret a fennmaradó készlettár és adatbázisméretének kisebb méretére korlátozódik. Az adatbázisok maximális mérete az adatfájlok maximális méretére vonatkozik, de nem tartalmazza a naplófájlok által használt területet. |
|||

## <a name="next-steps"></a>További lépések

- Egyetlen adatbázis virtuálismag-erőforrás-korlátaiért tekintse meg [az egyes adatbázisok erőforráskorlátait a virtuálismag-vásárlási modell használatával](sql-database-vcore-resource-limits-single-databases.md)
- Egyetlen adatbázis DTU erőforrás-korlátaihoz tekintse meg [az egyes adatbázisok erőforráskorlátait a DTU beszerzési modell használatával](sql-database-dtu-resource-limits-single-databases.md)
- A rugalmas készletek DTU erőforrás-korlátjaihoz tekintse meg [a DTU beszerzési modellt használó rugalmas készletek erőforráskorlátait](sql-database-dtu-resource-limits-elastic-pools.md)
- A felügyelt példányok erőforráskorlátairól a [Felügyelt példány erőforráskorlátai](sql-database-managed-instance-resource-limits.md)t.
- Az általános Azure-korlátokról az [Azure-előfizetési és szolgáltatáskorlátok, kvóták és korlátozások](../azure-resource-manager/management/azure-subscription-service-limits.md)című témakörben talál.
- Az adatbázis-kiszolgálón lévő erőforráskorlátokról a kiszolgálón és az előfizetési szinteken lévő korlátozásokról az [SQL Database-kiszolgálóerőforrás-korlátozások áttekintése című témakörben](sql-database-resource-limits-database-server.md) olvashat.
