---
title: Korlátozások – Azure Database for MariaDB
description: Ez a cikk a Azure Database for MariaDB korlátozásait ismerteti, például a kapcsolatok számát és a tárolási motor beállításait.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 6/10/2020
ms.openlocfilehash: d217c579c5f2cb5c3b6b984c0f2e0c57f17df2c9
ms.sourcegitcommit: eeba08c8eaa1d724635dcf3a5e931993c848c633
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/10/2020
ms.locfileid: "84669766"
---
# <a name="limitations-in-azure-database-for-mariadb"></a>A Azure Database for MariaDB korlátozásai
A következő szakaszok ismertetik a kapacitást, a tárolási motor támogatását, a jogosultságok támogatását, az adatmanipulációs nyilatkozatok támogatását és az adatbázis-szolgáltatás működési korlátait.

## <a name="server-parameters"></a>Kiszolgálóparaméterek

> [!NOTE]
> Ha a (z) és a (z) kiszolgáló-paraméterek minimális/maximális értékeit keresi `max_connections` `innodb_buffer_pool_size` , akkor ezek az adatok a **[kiszolgálói paraméterek](./concepts-server-parameters.md)** cikkbe kerültek.

Azure Database for MySQL támogatja a kiszolgálói paraméterek értékének finomhangolását. Néhány paraméter minimális és maximális értéke (pl. `max_connections`, `join_buffer_size` , `query_cache_size` ) meghatározása a kiszolgáló díjszabási szintjével és virtuális mag történik. A korlátokkal kapcsolatos további információkért tekintse meg a [kiszolgálói paramétereket](./concepts-server-parameters.md) . 

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
- Leszűkítés: a létrehozáshoz és a korlátozásához Super jogosultságok szükségesek. Ha biztonsági másolat használatával importálja az adatimportálást, távolítsa el `CREATE DEFINER` manuálisan a parancsokat, vagy használja a `--skip-definer` parancsot a mysqldump végrehajtásakor.

## <a name="data-manipulation-statement-support"></a>Az adatkezelési utasítás támogatása

### <a name="supported"></a>Támogatott
- `LOAD DATA INFILE`támogatott, de a `[LOCAL]` paramétert meg kell adni, és egy UNC elérési útra kell irányítani (az Azure Storage csatlakoztatva van az SMB protokollon keresztül).

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

### <a name="vnet-service-endpoints"></a>VNet-szolgáltatásvégpontok
- A VNet szolgáltatás-végpontok támogatása csak a általános célú és a memóriára optimalizált kiszolgálók esetében támogatott.

### <a name="storage-size"></a>Tárterület mérete
- Tekintse meg [a díjszabási](concepts-pricing-tiers.md) szinten a tárterületre vonatkozó korlátozásokat.

## <a name="current-known-issues"></a>Aktuális ismert problémák
- A MariaDB-kiszolgálópéldány a kiszolgáló nem megfelelő verzióját jeleníti meg a kapcsolatok létrehozása után. A megfelelő kiszolgálópéldány-motor verziójának lekéréséhez használja a `select version();` parancsot.

## <a name="next-steps"></a>Következő lépések
- [Az egyes szolgáltatási szinteknél elérhető szolgáltatások](concepts-pricing-tiers.md)
- [Támogatott MariaDB-adatbázis-verziók](concepts-supported-versions.md)
