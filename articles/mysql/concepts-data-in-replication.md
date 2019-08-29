---
title: Az adatreplikálás Azure Database for MySQLba.
description: Ez a cikk az Azure Database for MySQL adatreplikálását ismerteti.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 02/01/2019
ms.openlocfilehash: 889c2e75e9eee0586c709b032dbb6d1c58d45102
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2019
ms.locfileid: "70142052"
---
# <a name="replicate-data-into-azure-database-for-mysql"></a>Az adatreplikálás Azure Database for MySQLba

Felhőbe irányuló replikálás lehetővé teszi, hogy szinkronizálja az adatokat egy külső MySQL-kiszolgálóról a Azure Database for MySQL szolgáltatásba. A külső kiszolgáló lehet helyszíni, virtuális gépek vagy más felhőalapú szolgáltatók által üzemeltetett adatbázis-szolgáltatás. A beérkező adatokra épülő replikáció a MySQL natív bináris naplójának (binlog) fájlpozíció-alapú replikációján alapul. A BinLog-replikációval kapcsolatos további tudnivalókért tekintse meg a [MySQL BinLog-replikáció áttekintése](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html)című témakört. 

## <a name="when-to-use-data-in-replication"></a>Mikor kell használni a felhőbe irányuló replikálás
A felhőbe irányuló replikálás használatának főbb forgatókönyvei:

- **Hibrid adatszinkronizálás:** A felhőbe irányuló replikálás segítségével megtarthatja a helyszíni kiszolgálók és a Azure Database for MySQL között szinkronizált adatokat. Ez a szinkronizálás a hibrid alkalmazások létrehozásához hasznos. Ez a módszer akkor fordul elő, ha egy meglévő helyi adatbázis-kiszolgálóval rendelkezik, de az adott régióban szeretné áthelyezni az információkat a végfelhasználók számára.
- **Több felhős szinkronizálás:** Összetett felhőalapú megoldások esetén a felhőbe irányuló replikálás segítségével szinkronizálhat adatokat Azure Database for MySQL és különböző felhőalapú szolgáltatók között, beleértve a felhőben üzemeltetett virtuális gépeket és adatbázis-szolgáltatásokat.

## <a name="limitations-and-considerations"></a>Korlátozások és megfontolások

### <a name="data-not-replicated"></a>Nem replikált adatértékek
A főkiszolgálón található [*MySQL rendszeradatbázis*](https://dev.mysql.com/doc/refman/5.7/en/system-database.html) nem replikálódik. Nem replikálódnak a fiókok és engedélyek módosításai a főkiszolgálón. Ha létrehoz egy fiókot a főkiszolgálón, és ennek a fióknak el kell érnie a másodpéldány-kiszolgálót, akkor manuálisan hozza létre ugyanazt a fiókot a replika-kiszolgáló oldalán. A rendszeradatbázisban található táblák megismeréséhez tekintse meg a [MySQL](https://dev.mysql.com/doc/refman/5.7/en/system-database.html)-kézikönyvet.

### <a name="requirements"></a>Követelmények
- A főkiszolgáló verziójának legalább a MySQL 5,6-es verziójának kell lennie. 
- A fő-és a replika-kiszolgáló verziószámának azonosnak kell lennie. Például mindkettőnek a MySQL 5,6-es vagy újabb verziójúnak kell lennie a MySQL 5,7-es verziójának.
- Minden táblának elsődleges kulccsal kell rendelkeznie.
- A főkiszolgálónak a MySQL InnoDB motort kell használnia.
- A felhasználónak rendelkeznie kell engedéllyel a bináris naplózás konfigurálásához és új felhasználók létrehozásához a főkiszolgálón.
- Ha a főkiszolgálón engedélyezve van az SSL, ellenőrizze, hogy a tartományhoz megadott SSL hitelesítésszolgáltatói tanúsítvány szerepel- `mysql.az_replication_change_master` e a tárolt eljárásban. Tekintse át az [](https://docs.microsoft.com/azure/mysql/howto-data-in-replication#link-master-and-replica-servers-to-start-data-in-replication) alábbi példákat `master_ssl_ca` és a paramétert.
- Győződjön meg arról, hogy a fő kiszolgáló IP-címe hozzá lett adva az Azure Database for MySQL replikakiszolgálójának tűzfalszabályaihoz. A tűzfalszabályokat az [Azure Portal](https://docs.microsoft.com/azure/mysql/howto-manage-firewall-using-portal) vagy az [Azure CLI](https://docs.microsoft.com/azure/mysql/howto-manage-firewall-using-cli) használatával frissítheti.
- Győződjön meg arról, hogy a főkiszolgálót üzemeltető gép engedélyezi a bejövő és kimenő forgalmat is a 3306-os porton.
- Győződjön meg arról, hogy a főkiszolgáló **nyilvános IP-címmel** rendelkezik, vagy a DNS nyilvános elérhető.

### <a name="other"></a>Egyéb
- Az adatreplikálás csak általános célú és a memória optimalizált díjszabási szintjein támogatott.
- A globális tranzakciós azonosítók (GTID-EK) nem támogatottak.

## <a name="next-steps"></a>További lépések
- Ismerje meg, hogyan [állíthatja be](howto-data-in-replication.md) az adatreplikációt
- Tudnivalók [Az Azure-beli replikálásról olvasási replikákkal](concepts-read-replicas.md)
