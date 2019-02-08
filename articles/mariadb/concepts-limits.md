---
title: Azure Database for MariaDB korlátozásai
description: Ez a cikk ismerteti a korlátozások az Azure Database for MariaDB, például kapcsolat és a tárolási motort lehetőségek számát.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 02/07/2019
ms.openlocfilehash: 79d6e185b64fdaf332f877718487809ba6273441
ms.sourcegitcommit: e51e940e1a0d4f6c3439ebe6674a7d0e92cdc152
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/08/2019
ms.locfileid: "55895788"
---
# <a name="limitations-in-azure-database-for-mariadb"></a>Azure Database for MariaDB korlátozásai
A következő szakaszok ismertetik a kapacitást, motor támogatása, jogosultság támogatja, adatok adatkezelési utasítást, és az adatbázis-szolgáltatás működési korlátai.

## <a name="maximum-connections"></a>Kapcsolatok maximális száma
Tarifacsomag és virtuális magok száma kapcsolatok maximális száma a következők:

|**Tarifacsomag**|**vCore(s)**| **Kapcsolatok maximális száma**|
|---|---|---|
|Alapszintű| 1| 50|
|Alapszintű| 2| 100|
|Általános rendeltetés| 2| 300|
|Általános rendeltetés| 4| 625|
|Általános rendeltetés| 8| 1250|
|Általános rendeltetés| 16| 2500|
|Általános rendeltetés| 32| 5000|
|Memóriára optimalizált| 2| 600|
|Memóriára optimalizált| 4| 1250|
|Memóriára optimalizált| 8| 2500|
|Memóriára optimalizált| 16| 5000|

Amikor kapcsolatokat meghaladják a korlátot, a következő hiba jelenhet meg:
> ERROR 1040 (08004): Túl sok kapcsolat

## <a name="storage-engine-support"></a>Storage engine-támogatás

### <a name="supported"></a>Támogatott
- [InnoDB](https://mariadb.com/kb/en/library/xtradb-and-innodb/)
- [MEMÓRIA](https://mariadb.com/kb/en/library/memory-storage-engine/)

### <a name="unsupported"></a>Nem támogatott
- [MyISAM](https://mariadb.com/kb/en/library/myisam-storage-engine/)
- [BLACKHOLE](https://mariadb.com/kb/en/library/blackhole/)
- [ARCHÍVUM](https://mariadb.com/kb/en/library/archive/)

## <a name="privilege-support"></a>Jogosultság-támogatás

### <a name="unsupported"></a>Nem támogatott
- Adatbázis-szerepkör: Számos kiszolgálói paraméterek és beállítások véletlenül rontják a kiszolgáló teljesítményét vagy amelyet tagadni kell a Célrendszerben ACID tulajdonságait. Emiatt a szolgáltatás integritás és SLA-t, a termék szintjén fenntartására, ezt a szolgáltatást nem biztosít az adatbázis-szerepkör. Az alapértelmezett felhasználói fiók, amely jön létre egy új adatbázis-példány létrehozásakor, lehetővé teszi, hogy a felhasználó hajtani a legtöbb DDL és DML utasítást, a felügyelt adatbázis példányában.
- FELÜGYELŐI jogosultság: Hasonlóképpen [FELÜGYELŐI jogosultságok](https://mariadb.com/kb/en/library/grant/#global-privileges) is korlátozva.
- DEFINER: Hozhat létre felügyelői jogosultságokat igényel, és korlátozott. Ha egy biztonsági mentési adatokat importál, távolítsa el a `CREATE DEFINER` parancsok manuális vagy a `--skip-definer` parancsot a mysqldump végrehajtásakor.

## <a name="data-manipulation-statement-support"></a>Adatok adatkezelési utasítás támogatása

### <a name="supported"></a>Támogatott
- `LOAD DATA INFILE` támogatott, de a `[LOCAL]` paramétert kell a megadott és átirányítva, UNC elérési út (az Azure storage csatlakoztatása az SMB-n keresztül).

### <a name="unsupported"></a>Nem támogatott
- `SELECT ... INTO OUTFILE`

## <a name="functional-limitations"></a>Működési korlátai

### <a name="scale-operations"></a>A skálázási műveletek
- Dinamikus méretezés, és onnan az alapszintű tarifacsomagban is kapható jelenleg nem támogatott.
- Kiszolgáló-tároló méretének csökkentése nem támogatott.

### <a name="server-version-upgrades"></a>Kiszolgáló verziófrissítések
- Fő database engine verziói között az automatikus migrálási jelenleg nem támogatott.

### <a name="point-in-time-restore"></a>Időponthoz kötött visszaállítás
- A PITR a funkció használatakor az ugyanezzel a konfigurációval, mint a kiszolgáló-alapú, az új kiszolgáló jön létre.
- Törölt kiszolgáló visszaállítása nem támogatott.

### <a name="subscription-management"></a>Előfizetés-kezelés
- Dinamikusan előfizetést és erőforráscsoportot az előre létrehozott kiszolgálók áthelyezése jelenleg nem támogatott.

### <a name="vnet-service-endpoints"></a>Virtuális hálózati Szolgáltatásvégpontok
- Virtuális hálózati Szolgáltatásvégpontok támogatása csak az általános célú és memóriahasználatra optimalizált kiszolgálók esetében érhető el.

## <a name="current-known-issues"></a>Jelenlegi ismert problémák
- MariaDB-server-példány a megfelelő verziója a kapcsolat létrejötte után jeleníti meg. A megfelelő kiszolgáló példány motor verziója, amelyet a `select version();` parancsot.

## <a name="next-steps"></a>További lépések
- [Az egyes szolgáltatásszinteken elérhető](concepts-pricing-tiers.md)
- [MariaDB-adatbázis támogatott verziók](concepts-supported-versions.md)
