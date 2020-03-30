---
title: Be-
description: Ismerje meg, hogyan használatával adat-in replikáció szinkronizálása egy külső kiszolgálóról az Azure Database for MariaDB szolgáltatás.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 1fbcc1fb27d5e6df4641f79c0d634580f74000b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79532060"
---
# <a name="replicate-data-into-azure-database-for-mariadb"></a>Adatok replikálása a MariaDB Azure-adatbázisába

A beérkező adatokra épülő replikáció lehetővé teszi, hogy szinkronizálja egy helyszínen, virtuális gépeken vagy más felhőszolgáltatók által üzemeltetett adatbázis-szolgáltatásokban futó MariaDB-kiszolgáló adatait az Azure Database for MariaDB-szolgáltatásba. A beérkező adatokra épülő replikáció a MariaDB natív bináris naplójának (binlog) fájlpozíció-alapú replikációján alapul. A binlog replikációról a [binlog replikáció – áttekintés című témakörben olvashat bővebben.](https://mariadb.com/kb/en/library/replication-overview/)

## <a name="when-to-use-data-in-replication"></a>Mikor kell használni az adatreplikációt?
Az adatreplikáció főbb forgatókönyvei a következők:

- **Hibrid adatszinkronizálás:** Az adatreplikációval a helyszíni kiszolgálók és a MariaDB Azure Database között szinkronizálhatja az adatokat. Ez a szinkronizálás hibrid alkalmazások létrehozásához hasznos. Ez a módszer akkor vonzó, ha meglévő helyi adatbázis-kiszolgálóval rendelkezik, de az adatokat egy régióba szeretné áthelyezni a végfelhasználókhoz közelebb.
- **Többfelhős szinkronizálás:** Összetett felhőalapú megoldások esetén a Data-in Replication használatával szinkronizálhatja az adatokat az Azure Database for MariaDB és a különböző felhőszolgáltatók között, beleértve a virtuális gépeket és az ezekben a felhőkben üzemeltetett adatbázis-szolgáltatásokat.

## <a name="limitations-and-considerations"></a>Korlátozások és megfontolások

### <a name="data-not-replicated"></a>Nem replikált adatok
A főkiszolgálón lévő [*mysql rendszeradatbázis*](https://mariadb.com/kb/en/library/the-mysql-database-tables/) replikálása nem történik meg. A főkiszolgálón lévő fiókok és engedélyek módosításai nem replikálódnak. Ha létrehoz egy fiókot a főkiszolgálón, és ennek a fióknak hozzá kell férnie a replikakiszolgálóhoz, akkor manuálisan hozza létre ugyanazt a fiókot a replikakiszolgáló oldalán. A rendszeradatbázisban található táblák megértéséhez olvassa el a [MariaDB dokumentációját.](https://mariadb.com/kb/en/library/the-mysql-database-tables/)

### <a name="requirements"></a>Követelmények
- A fő kiszolgáló verziójának legalább A MariaDB 10.2-es verziójának kell lennie.
- A fő- és replikakiszolgáló verzióinak meg kell egyeznek. Például mindkettőnek MariaDB 10.2-es verziójának kell lennie.
- Minden táblának rendelkeznie kell egy elsődleges kulccsal.
- A főkiszolgálónak az InnoDB motort kell használnia.
- A felhasználónak engedéllyel kell rendelkeznie a bináris naplózás konfigurálásához és új felhasználók létrehozásához a főkiszolgálón.
- Ha a főkiszolgálón engedélyezve van az SSL, győződjön meg arról, hogy a tartományhoz megadott SSL hitelesítésszolgáltatói tanúsítvány szerepel a `mariadb.az_replication_change_master` tárolt eljárásban. Tekintse meg a következő `master_ssl_ca` [példákat](https://docs.microsoft.com/azure/mariadb/howto-data-in-replication#link-the-master-and-replica-servers-to-start-data-in-replication) és a paramétert.
- Győződjön meg arról, hogy a fő kiszolgáló IP-címe hozzá lett adva az Azure Database for MariaDB replikakiszolgálójának tűzfalszabályaihoz. A tűzfalszabályokat az [Azure Portal](https://docs.microsoft.com/azure/mariadb/howto-manage-firewall-portal) vagy az [Azure CLI](https://docs.microsoft.com/azure/mariadb/howto-manage-firewall-cli) használatával frissítheti.
- Győződjön meg arról, hogy a főkiszolgálót üzemeltető gép engedélyezi a bejövő és kimenő forgalmat is a 3306-os porton.
- Győződjön meg arról, hogy a főkiszolgáló **rendelkezik nyilvános IP-címmel,** a DNS nyilvánosan elérhető, vagy teljesen minősített tartománynévvel (FQDN) rendelkezik.

### <a name="other"></a>Egyéb
- Az adatok on-in replikációcsak általános célú és memóriaoptimalizált tarifaszintek támogatottak.

## <a name="next-steps"></a>További lépések
- További információ az [adatreplikáció beállításáról.](howto-data-in-replication.md)
