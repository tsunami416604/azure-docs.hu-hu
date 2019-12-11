---
title: Korlátozások – Azure Database for MariaDB
description: Ez a cikk a Azure Database for MariaDB korlátozásait ismerteti, például a kapcsolatok számát és a tárolási motor beállításait.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 12/09/2019
ms.openlocfilehash: 1f5824f349650e340e395221785266096da16d6f
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2019
ms.locfileid: "74969547"
---
# <a name="limitations-in-azure-database-for-mariadb"></a>A Azure Database for MariaDB korlátozásai
A következő szakaszok ismertetik a kapacitást, a tárolási motor támogatását, a jogosultságok támogatását, az adatmanipulációs nyilatkozatok támogatását és az adatbázis-szolgáltatás működési korlátait.

## <a name="maximum-connections"></a>Kapcsolatok maximális száma
A kapcsolatok maximális száma az árképzési szinten és a virtuális mag a következő:

|**Tarifacsomag**|**Virtuális mag (ok)**| **Kapcsolatok maximális száma**|
|---|---|---|
|Basic| 1| 50|
|Basic| 2| 100|
|Általános rendeltetés| 2| 600|
|Általános rendeltetés| 4| 1250|
|Általános rendeltetés| 8| 2500|
|Általános rendeltetés| 16| 5000|
|Általános rendeltetés| 32| 10000|
|Általános rendeltetés| 64| 20000|
|Memóriára optimalizált| 2| 800|
|Memóriára optimalizált| 4| 2500|
|Memóriára optimalizált| 8| 5000|
|Memóriára optimalizált| 16| 10000|
|Memóriára optimalizált| 32| 20000|

Ha a kapcsolatok túllépik a korlátot, a következő hibaüzenet jelenhet meg:
> HIBA 1040 (08004): túl sok kapcsolat

## <a name="storage-engine-support"></a>A Storage Engine támogatása

### <a name="supported"></a>Támogatott
- [InnoDB](https://mariadb.com/kb/en/library/xtradb-and-innodb/)
- [MEMÓRIA](https://mariadb.com/kb/en/library/memory-storage-engine/)

### <a name="unsupported"></a>Nem támogatott
- [MyISAM](https://mariadb.com/kb/en/library/myisam-storage-engine/)
- [BLACKHOLE](https://mariadb.com/kb/en/library/blackhole/)
- [Archívum](https://mariadb.com/kb/en/library/archive/)

## <a name="privilege-support"></a>Jogosultságok támogatása

### <a name="unsupported"></a>Nem támogatott
- DBA-szerepkör: számos kiszolgáló-paraméter és-beállítás akaratlanul csökkentheti a kiszolgáló teljesítményét, vagy megtagadja az adatbázis-kezelők által nyújtott savas tulajdonságokat. A szolgáltatás integritásának és az SLA-nak a termék szintjén történő fenntartása érdekében ez a szolgáltatás nem teszi elérhetővé a DBA-szerepkört. Az alapértelmezett felhasználói fiók, amely új adatbázis-példány létrehozásakor jön létre, lehetővé teszi, hogy a felhasználó a felügyelt adatbázis-példányban a DDL-és DML-utasítások többségét elvégezze.
- SZUPER jogosultság: a hasonló [Super jogosultság](https://mariadb.com/kb/en/library/grant/#global-privileges) is korlátozott.
- Leszűkítés: a létrehozáshoz és a korlátozásához Super jogosultságok szükségesek. Ha biztonsági másolat használatával importálja az adatimportálást, távolítsa el manuálisan a `CREATE DEFINER` parancsokat, vagy a mysqldump végrehajtásakor a `--skip-definer` parancs használatával.

## <a name="data-manipulation-statement-support"></a>Az adatkezelési utasítás támogatása

### <a name="supported"></a>Támogatott
- a `LOAD DATA INFILE` támogatott, de a `[LOCAL]` paramétert meg kell adni, és át kell irányítani egy UNC elérési útra (Azure Storage csatlakoztatva az SMB-n keresztül).

### <a name="unsupported"></a>Nem támogatott
- `SELECT ... INTO OUTFILE`

## <a name="functional-limitations"></a>Funkcionális korlátozások

### <a name="scale-operations"></a>Skálázási műveletek
- A dinamikus skálázás az alapszintű díjszabási szintekre és a rendszerből nem támogatott.
- A kiszolgáló tárolási méretének csökkentése nem támogatott.

### <a name="server-version-upgrades"></a>Kiszolgáló verziófrissítése
- A fő adatbázismotor-verziók közötti automatikus áttelepítés jelenleg nem támogatott.

### <a name="point-in-time-restore"></a>Időponthoz kötött visszaállítás
- A PITR szolgáltatás használatakor az új kiszolgáló ugyanazzal a konfigurációval jön létre, mint a kiszolgáló.
- A törölt kiszolgáló visszaállítása nem támogatott.

### <a name="subscription-management"></a>Előfizetés-kezelés
- Az előre létrehozott kiszolgálók az előfizetés és az erőforráscsoport között történő dinamikus áthelyezése jelenleg nem támogatott.

### <a name="vnet-service-endpoints"></a>VNet szolgáltatási végpontok
- A VNet szolgáltatás-végpontok támogatása csak a általános célú és a memóriára optimalizált kiszolgálók esetében támogatott.

### <a name="storage-size"></a>Tárterület mérete
- Tekintse meg [a díjszabási](concepts-pricing-tiers.md) szinten a tárterületre vonatkozó korlátozásokat.

## <a name="current-known-issues"></a>Aktuális ismert problémák
- A MariaDB-kiszolgálópéldány a kiszolgáló nem megfelelő verzióját jeleníti meg a kapcsolatok létrehozása után. A megfelelő kiszolgálópéldány-motor verziójának beszerzéséhez használja a `select version();` parancsot.

## <a name="next-steps"></a>Következő lépések
- [Az egyes szolgáltatási szinteknél elérhető szolgáltatások](concepts-pricing-tiers.md)
- [Támogatott MariaDB-adatbázis-verziók](concepts-supported-versions.md)
