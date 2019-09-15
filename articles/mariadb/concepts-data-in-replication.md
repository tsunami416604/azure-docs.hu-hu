---
title: Az adatreplikálás Azure Database for MariaDBba.
description: Ez a cikk az Azure Database for MariaDB adatreplikálását ismerteti.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/13/2019
ms.openlocfilehash: 3ceb8b4f3c2c50ac0ac3bd12831b5497f9a05afb
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/14/2019
ms.locfileid: "70993029"
---
# <a name="replicate-data-into-azure-database-for-mariadb"></a>Az adatreplikálás Azure Database for MariaDBba

A beérkező adatokra épülő replikáció lehetővé teszi, hogy szinkronizálja egy helyszínen, virtuális gépeken vagy más felhőszolgáltatók által üzemeltetett adatbázis-szolgáltatásokban futó MariaDB-kiszolgáló adatait az Azure Database for MariaDB-szolgáltatásba. A beérkező adatokra épülő replikáció a MariaDB natív bináris naplójának (binlog) fájlpozíció-alapú replikációján alapul. A BinLog replikálásával kapcsolatos további tudnivalókért tekintse meg a [BinLog-replikáció áttekintése](https://mariadb.com/kb/en/library/replication-overview/)című témakört.

## <a name="when-to-use-data-in-replication"></a>Mikor kell használni a felhőbe irányuló replikálás
A felhőbe irányuló replikálás használatának főbb forgatókönyvei:

- **Hibrid adatszinkronizálás:** A felhőbe irányuló replikálás segítségével megtarthatja a helyszíni kiszolgálók és a Azure Database for MariaDB között szinkronizált adatokat. Ez a szinkronizálás a hibrid alkalmazások létrehozásához hasznos. Ez a módszer akkor fordul elő, ha egy meglévő helyi adatbázis-kiszolgálóval rendelkezik, de az adott régióban szeretné áthelyezni az információkat a végfelhasználók számára.
- **Több felhős szinkronizálás:** Összetett felhőalapú megoldások esetén a felhőbe irányuló replikálás segítségével szinkronizálhat adatokat Azure Database for MariaDB és különböző felhőalapú szolgáltatók között, beleértve a felhőben üzemeltetett virtuális gépeket és adatbázis-szolgáltatásokat.

## <a name="limitations-and-considerations"></a>Korlátozások és megfontolások

### <a name="data-not-replicated"></a>Nem replikált adatértékek
A főkiszolgálón található [*MySQL rendszeradatbázis*](https://mariadb.com/kb/en/library/the-mysql-database-tables/) nem replikálódik. A rendszer nem replikálja a fiókok és engedélyek módosításait a főkiszolgálón. Ha létrehoz egy fiókot a főkiszolgálón, és ennek a fióknak el kell érnie a másodpéldány-kiszolgálót, akkor manuálisan kell létrehoznia ugyanazt a fiókot a replika-kiszolgáló oldalán. A rendszeradatbázisban található táblák megismeréséhez tekintse meg a [MariaDB dokumentációját](https://mariadb.com/kb/en/library/the-mysql-database-tables/).

### <a name="requirements"></a>Követelmények
- A főkiszolgáló verziójának legalább MariaDB 10,2-es verziójúnak kell lennie.
- A fő-és a replika-kiszolgáló verziószámának azonosnak kell lennie. Például mindkettőnek a 10,2-es verzió MariaDB kell lennie.
- Minden táblának elsődleges kulccsal kell rendelkeznie.
- A főkiszolgálónak a InnoDB motort kell használnia.
- A felhasználónak rendelkeznie kell engedéllyel a bináris naplózás konfigurálásához és új felhasználók létrehozásához a főkiszolgálón.
- Ha a főkiszolgálón engedélyezve van az SSL, ellenőrizze, hogy a tartományhoz megadott SSL hitelesítésszolgáltatói tanúsítvány szerepel- `mariadb.az_replication_change_master` e a tárolt eljárásban. Tekintse át az alábbi [példákat](https://docs.microsoft.com/azure/mariadb/howto-data-in-replication#link-the-master-and-replica-servers-to-start-data-in-replication) és a `master_ssl_ca` paramétert.
- Győződjön meg arról, hogy a fő kiszolgáló IP-címe hozzá lett adva az Azure Database for MariaDB replikakiszolgálójának tűzfalszabályaihoz. A tűzfalszabályokat az [Azure Portal](https://docs.microsoft.com/azure/mariadb/howto-manage-firewall-portal) vagy az [Azure CLI](https://docs.microsoft.com/azure/mariadb/howto-manage-firewall-cli) használatával frissítheti.
- Győződjön meg arról, hogy a főkiszolgálót üzemeltető gép engedélyezi a bejövő és kimenő forgalmat is a 3306-os porton.
- Győződjön meg arról, hogy a főkiszolgáló **nyilvános IP-címmel**rendelkezik, a DNS nyilvánosan elérhető, vagy rendelkezik teljes tartománynévvel (FQDN).

### <a name="other"></a>Egyéb
- Az adatreplikálás csak általános célú és a memória optimalizált díjszabási szintjein támogatott.

## <a name="next-steps"></a>További lépések
- Megtudhatja, hogyan [állíthatja be az adatreplikációt](howto-data-in-replication.md).
