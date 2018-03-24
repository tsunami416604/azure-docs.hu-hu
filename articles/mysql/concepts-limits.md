---
title: MySQL az Azure-adatbázis korlátozásai
description: Ez a cikk ismerteti a MySQL, például a kapcsolat és a tárolási motor lehetőségei az Azure Database korlátozásai.
services: mysql
author: kamathsun
ms.author: sukamat
manager: kfile
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 03/20/2018
ms.openlocfilehash: 2fa69182b4238cfd19fcc9571e4327512e9528c1
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
---
# <a name="limitations-in-azure-database-for-mysql"></a>MySQL az Azure-adatbázis korlátozásai
A következő szakaszok ismertetik a kapacitás, tárolási motor támogatja, jogosultság, adatok adatkezelési utasítás támogatásának, és az adatbázis szolgáltatásban működik korlátok. Lásd még: [általános korlátozások](https://dev.mysql.com/doc/mysql-reslimits-excerpt/5.6/en/limits.html) a MySQL adatbázis-kezelő alkalmazandó.

## <a name="service-tier-maximums"></a>Szolgáltatási szint méretkorlát
Azure MySQL-adatbázis egy kiszolgáló létrehozásakor választhat több szolgáltatásszinttel rendelkezik. További információkért lásd: [tarifacsomagok MySQL az Azure-adatbázis](concepts-pricing-tiers.md).  

Van a kapcsolatok, a számítási egység és a tárolás, az egyes szolgáltatásszintek tartalmának maximális száma az alábbiak szerint: 

|**Tarifacsomag**| **Számítási létrehozása**|**vCore(s)**| **Kapcsolatok maximális száma**|
|---|---|---|---|
|Alapszintű| Gen 4| 1| 50|
|Alapszintű| Gen 4| 2| 100|
|Alapszintű| Gen 5| 1| 50|
|Alapszintű| Gen 5| 2| 100|
|Általános rendeltetés| Gen 4| 2| 300|
|Általános rendeltetés| Gen 4| 4| 625|
|Általános rendeltetés| Gen 4| 8| 1250|
|Általános rendeltetés| Gen 4| 16| 2500|
|Általános rendeltetés| Gen 4| 32| 5000|
|Általános rendeltetés| Gen 5| 2| 300|
|Általános rendeltetés| Gen 5| 4| 625|
|Általános rendeltetés| Gen 5| 8| 1250|
|Általános rendeltetés| Gen 5| 16| 2500|
|Általános rendeltetés| Gen 5| 32| 5000|
|Memóriára optimalizált| Gen 5| 2| 600|
|Memóriára optimalizált| Gen 5| 4| 1250|
|Memóriára optimalizált| Gen 5| 8| 2500|
|Memóriára optimalizált| Gen 5| 16| 5000|

Túl sok a kapcsolat elérésekor a következő hibaüzenet jelenhet meg:
> 1040 (08004). hiba: Túl sok a kapcsolat

## <a name="storage-engine-support"></a>Storage engine támogatása

### <a name="supported"></a>Támogatott
- [InnoDB](https://dev.mysql.com/doc/refman/5.7/en/innodb-introduction.html)
- [MEMÓRIA](https://dev.mysql.com/doc/refman/5.7/en/memory-storage-engine.html)

### <a name="unsupported"></a>Nem támogatott
- [MyISAM](https://dev.mysql.com/doc/refman/5.7/en/myisam-storage-engine.html)
- [BLACKHOLE](https://dev.mysql.com/doc/refman/5.7/en/blackhole-storage-engine.html)
- [ARCHÍV](https://dev.mysql.com/doc/refman/5.7/en/archive-storage-engine.html)
- [ÖSSZEVONT](https://dev.mysql.com/doc/refman/5.7/en/federated-storage-engine.html)

## <a name="privilege-support"></a>Jogosultság-támogatás

### <a name="unsupported"></a>Nem támogatott
- DBA szerepkör: több kiszolgáló paraméterek és beállítások is véletlenül rontják a kiszolgáló teljesítményét vagy az adatbázis-kezelő ACID tulajdonságainak semlegesítsék. A szolgáltatás integritásra és a termék szinten SLA fenntartása érdekében ez a szolgáltatás nem fedi fel a DBA szerepkör. Az alapértelmezett felhasználói fiók, amely egy új adatbázis-példány létrehozásakor összeállított, lehetővé teszi a felhasználónak DDL és DML utasítást a legtöbb felügyelt adatbázispéldány. 
- SUPER jogosultság: hasonlóképpen [SUPER jogosultság](https://dev.mysql.com/doc/refman/5.7/en/privileges-provided.html#priv_super) is korlátozódik.

## <a name="data-manipulation-statement-support"></a>Adatok adatkezelési utasítás támogatása

### <a name="supported"></a>Támogatott
- TERHELÉSELOSZTÁSI adatok BEMENETI_FÁJL - támogatott, de adjon meg a [helyi] paraméter, amely egy UNC elérési utat (Azure storage XSMB keresztül csatlakoztatott) van átirányítva.

### <a name="unsupported"></a>Nem támogatott
- VÁLASSZON... AZ EREDMÉNYFÁJL

## <a name="functional-limitations"></a>Működési korlátai

### <a name="scale-operations"></a>A skálázási műveletek
- Dinamikus méretezés a kiszolgálók közötti tarifacsomagok jelenleg nem támogatott. Ez azt jelenti, hogy közötti váltásról alapvető, általános célú és Memóriaoptimalizált tarifacsomag szükséges.
- Kiszolgáló tároló méretének csökkentése nem támogatott.

### <a name="server-version-upgrades"></a>Kiszolgáló verziófrissítések
- Fő adatbázis motor verziók közötti automatikus áttelepítési jelenleg nem támogatott.

### <a name="point-in-time-restore"></a>Időponthoz kötött visszaállítás
- Különböző szolgáltatási rétegben és/vagy számítási egység és a tárhely mérete visszaállítása nem engedélyezett.
- A Törölt kiszolgáló visszaállítása nem támogatott.

## <a name="functional-limitations"></a>Működési korlátai

### <a name="subscription-management"></a>Előfizetés kezelése
- Dinamikusan áthelyezése előfizetés és az erőforráscsoport előre létrehozott kiszolgálók jelenleg nem támogatott.

## <a name="current-known-issues"></a>Jelenlegi ismert problémái
- Kapcsolat létrejötte után a MySQL server-példány helytelen verzióját jeleníti meg. A megfelelő kiszolgálói példány versioning használatához válassza version(); a MySQL-parancssorba parancsot.

## <a name="next-steps"></a>További lépések
- [Az egyes szolgáltatásszinteken elérhető](concepts-pricing-tiers.md)
- [MySQL-adatbázis támogatott verziói](concepts-supported-versions.md)
