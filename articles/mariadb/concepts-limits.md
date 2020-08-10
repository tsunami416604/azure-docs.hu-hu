---
title: Korlátozások – Azure Database for MariaDB
description: Ez a cikk a Azure Database for MariaDB korlátozásait ismerteti, például a kapcsolatok számát és a tárolási motor beállításait.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 6/25/2020
ms.openlocfilehash: 51aff856aa5bdeb042493d47f100be0ca32dfbbb
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/10/2020
ms.locfileid: "88032679"
---
# <a name="limitations-in-azure-database-for-mariadb"></a>A Azure Database for MariaDB korlátozásai
A következő szakaszok ismertetik a kapacitást, a tárolási motor támogatását, a jogosultságok támogatását, az adatmanipulációs nyilatkozatok támogatását és az adatbázis-szolgáltatás működési korlátait.

## <a name="server-parameters"></a>Kiszolgálóparaméterek

> [!NOTE]
> Ha a (z) és a (z) kiszolgáló-paraméterek minimális/maximális értékeit keresi `max_connections` `innodb_buffer_pool_size` , akkor ezek az adatok a **[kiszolgálói paraméterek](./concepts-server-parameters.md)** cikkbe kerültek.

Azure Database for MariaDB támogatja a kiszolgálói paraméterek értékének finomhangolását. Néhány paraméter minimális és maximális értéke (pl. `max_connections`, `join_buffer_size` , `query_cache_size` ) meghatározása a kiszolgáló díjszabási szintjével és virtuális mag történik. A korlátokkal kapcsolatos további információkért tekintse meg a [kiszolgálói paramétereket](./concepts-server-parameters.md) .

A kezdeti üzembe helyezés után az Azure for MariaDB-kiszolgáló az időzóna-információkhoz tartozó rendszertáblákat tartalmazza, de ezek a táblák nem lesznek feltöltve. Az időzóna-táblázatok úgy tölthetők fel, hogy meghívja a `mysql.az_load_timezone` tárolt eljárást egy eszközről, például a MySQL-parancssorból vagy a MySQL Workbenchből. A tárolt eljárás meghívásához és a globális vagy munkamenet szintű időzónák beállításához tekintse meg a [Azure Portal](howto-server-parameters.md#working-with-the-time-zone-parameter) vagy az [Azure CLI](howto-configure-server-parameters-cli.md#working-with-the-time-zone-parameter) -cikkeket.

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
- Rendszeradatbázisok: Azure Database for MariaDB-ben a [MySQL rendszeradatbázis](https://mariadb.com/kb/en/the-mysql-database-tables/) csak olvasható, mivel a rendszer a különböző Pásti szolgáltatási funkciók támogatásához használatos. Vegye figyelembe, hogy a rendszeradatbázisban nem lehet bármit módosítani `mysql` .

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

## <a name="next-steps"></a>További lépések
- [Az egyes szolgáltatási szinteknél elérhető szolgáltatások](concepts-pricing-tiers.md)
- [Támogatott MariaDB-adatbázis-verziók](concepts-supported-versions.md)
