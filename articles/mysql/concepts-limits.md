---
title: Korlátozások – Azure Database for MySQL
description: Ez a cikk a Azure Database for MySQL korlátozásait ismerteti, például a kapcsolatok számát és a tárolási motor beállításait.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 12/2/2019
ms.openlocfilehash: 07feb3ebf9720d70da441486fd0b2e6e274b68e4
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74770917"
---
# <a name="limitations-in-azure-database-for-mysql"></a>A Azure Database for MySQL korlátozásai
A következő szakaszok ismertetik a kapacitást, a tárolási motor támogatását, a jogosultságok támogatását, az adatmanipulációs nyilatkozatok támogatását és az adatbázis-szolgáltatás működési korlátait. Lásd még a MySQL-adatbázismotor esetében alkalmazandó [általános korlátozásokat](https://dev.mysql.com/doc/mysql-reslimits-excerpt/5.6/en/limits.html) is.

## <a name="maximum-connections"></a>Kapcsolatok maximális száma
A kapcsolatok maximális száma az árképzési szinten és a virtuális mag a következő: 

|**Tarifacsomag**|**Virtuális mag (ok)**| **Kapcsolatok maximális száma**|
|---|---|---|
|Basic| 1| 50|
|Basic| 2| 100|
|Általános rendeltetés| 2| 300|
|Általános rendeltetés| 4| 625|
|Általános rendeltetés| 8| 1250|
|Általános rendeltetés| 16| 2500|
|Általános rendeltetés| 32| 5000|
|Általános rendeltetés| 64| 10000|
|Memóriára optimalizált| 2| 600|
|Memóriára optimalizált| 4| 1250|
|Memóriára optimalizált| 8| 2500|
|Memóriára optimalizált| 16| 5000|
|Memóriára optimalizált| 32| 10000|

Ha a kapcsolatok túllépik a korlátot, a következő hibaüzenet jelenhet meg:
> HIBA 1040 (08004): túl sok kapcsolat

## <a name="storage-engine-support"></a>A Storage Engine támogatása

### <a name="supported"></a>Támogatott
- [InnoDB](https://dev.mysql.com/doc/refman/5.7/en/innodb-introduction.html)
- [MEMÓRIA](https://dev.mysql.com/doc/refman/5.7/en/memory-storage-engine.html)

### <a name="unsupported"></a>Nem támogatott
- [MyISAM](https://dev.mysql.com/doc/refman/5.7/en/myisam-storage-engine.html)
- [BLACKHOLE](https://dev.mysql.com/doc/refman/5.7/en/blackhole-storage-engine.html)
- [Archívum](https://dev.mysql.com/doc/refman/5.7/en/archive-storage-engine.html)
- [ÖSSZEVONT](https://dev.mysql.com/doc/refman/5.7/en/federated-storage-engine.html)

## <a name="privilege-support"></a>Jogosultságok támogatása

### <a name="unsupported"></a>Nem támogatott
- DBA-szerepkör: számos kiszolgáló-paraméter és-beállítás akaratlanul csökkentheti a kiszolgáló teljesítményét, vagy megtagadja az adatbázis-kezelők által nyújtott savas tulajdonságokat. A szolgáltatás integritásának és az SLA-nak a termék szintjén történő fenntartása érdekében ez a szolgáltatás nem teszi elérhetővé a DBA-szerepkört. Az alapértelmezett felhasználói fiók, amely új adatbázis-példány létrehozásakor jön létre, lehetővé teszi, hogy a felhasználó a felügyelt adatbázis-példányban a DDL-és DML-utasítások többségét elvégezze. 
- SZUPER jogosultság: a hasonló [Super jogosultság](https://dev.mysql.com/doc/refman/5.7/en/privileges-provided.html#priv_super) is korlátozott.
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
- A fő adatbázismotor-verziók közötti automatikus áttelepítés jelenleg nem támogatott. Ha a következő főverzióra szeretne frissíteni, hozzon létre egy [memóriaképet, és állítsa vissza](./concepts-migrate-dump-restore.md) egy olyan kiszolgálóra, amely az új motor verziójával lett létrehozva.

### <a name="point-in-time-restore"></a>Időponthoz kötött visszaállítás
- A PITR szolgáltatás használatakor az új kiszolgáló ugyanazzal a konfigurációval jön létre, mint a kiszolgáló.
- A törölt kiszolgáló visszaállítása nem támogatott.

### <a name="vnet-service-endpoints"></a>VNet szolgáltatási végpontok
- A VNet szolgáltatás-végpontok támogatása csak a általános célú és a memóriára optimalizált kiszolgálók esetében támogatott.

### <a name="storage-size"></a>Tárterület mérete
- Tekintse meg [a díjszabási](concepts-pricing-tiers.md) szinten a tárterületre vonatkozó korlátozásokat.

## <a name="current-known-issues"></a>Aktuális ismert problémák
- A MySQL-kiszolgáló példánya a kiszolgáló nem megfelelő verzióját jeleníti meg a kapcsolatok létrehozása után. A megfelelő kiszolgálópéldány-motor verziójának beszerzéséhez használja a `select version();` parancsot.

## <a name="next-steps"></a>Következő lépések
- [Az egyes szolgáltatási szinteknél elérhető szolgáltatások](concepts-pricing-tiers.md)
- [Támogatott MySQL-adatbázis-verziók](concepts-supported-versions.md)
