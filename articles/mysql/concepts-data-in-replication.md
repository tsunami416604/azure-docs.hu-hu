---
title: Adatreplikálás – Azure Database for MySQL
description: További információ a külső kiszolgálóról a Azure Database for MySQL szolgáltatásba való szinkronizáláshoz szükséges adatreplikálás használatával.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 8/7/2020
ms.openlocfilehash: a9d6c1b2438f20a06062842b96b147e094760238
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/10/2020
ms.locfileid: "88031217"
---
# <a name="replicate-data-into-azure-database-for-mysql"></a>Az adatreplikálás Azure Database for MySQLba

Felhőbe irányuló replikálás lehetővé teszi, hogy szinkronizálja az adatokat egy külső MySQL-kiszolgálóról a Azure Database for MySQL szolgáltatásba. A külső kiszolgáló lehet helyszíni, virtuális gépek vagy más felhőalapú szolgáltatók által üzemeltetett adatbázis-szolgáltatás. A beérkező adatokra épülő replikáció a MySQL natív bináris naplójának (binlog) fájlpozíció-alapú replikációján alapul. A BinLog-replikációval kapcsolatos további tudnivalókért tekintse meg a [MySQL BinLog-replikáció áttekintése](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html)című témakört. 

## <a name="when-to-use-data-in-replication"></a>Mikor kell használni a felhőbe irányuló replikálás
A felhőbe irányuló replikálás használatának főbb forgatókönyvei:

- **Hibrid adatszinkronizálás:** A felhőbe irányuló replikálás segítségével megtarthatja a helyszíni kiszolgálók és a Azure Database for MySQL között szinkronizált adatokat. Ez a szinkronizálás a hibrid alkalmazások létrehozásához hasznos. Ez a módszer akkor fordul elő, ha egy meglévő helyi adatbázis-kiszolgálóval rendelkezik, de az adott régióban szeretné áthelyezni az információkat a végfelhasználók számára.
- **Több felhős szinkronizálás:** Összetett felhőalapú megoldások esetén a felhőbe irányuló replikálás segítségével szinkronizálhat adatokat Azure Database for MySQL és különböző felhőalapú szolgáltatók között, beleértve a felhőben üzemeltetett virtuális gépeket és adatbázis-szolgáltatásokat.
 
Áttelepítési forgatókönyvek esetén használja a [Azure Database Migration Service](https://azure.microsoft.com/services/database-migration/)(DMS).

## <a name="limitations-and-considerations"></a>Korlátozások és szempontok

### <a name="data-not-replicated"></a>Nem replikált adatértékek
A főkiszolgálón található [*MySQL rendszeradatbázis*](https://dev.mysql.com/doc/refman/5.7/en/system-schema.html) nem replikálódik. Nem replikálódnak a fiókok és engedélyek módosításai a főkiszolgálón. Ha létrehoz egy fiókot a főkiszolgálón, és ennek a fióknak el kell érnie a másodpéldány-kiszolgálót, akkor manuálisan hozza létre ugyanazt a fiókot a replika-kiszolgáló oldalán. A rendszeradatbázisban található táblák megismeréséhez tekintse meg a [MySQL-kézikönyvet](https://dev.mysql.com/doc/refman/5.7/en/system-schema.html).

### <a name="filtering"></a>Szűrés
A (helyszíni, virtuális gépeken üzemeltetett vagy más felhőalapú szolgáltatók által üzemeltetett adatbázis-szolgáltatás) tábláinak replikálásának kihagyásához a (z) `replicate_wild_ignore_table` paramétert a rendszer támogatja. Ha szükséges, frissítse ezt a paramétert az Azure-ban üzemeltetett replika-kiszolgálón a [Azure Portal](howto-server-parameters.md) vagy az [Azure CLI](howto-configure-server-parameters-using-cli.md)használatával.

A paraméterrel kapcsolatos további információkért tekintse meg a [MySQL dokumentációját](https://dev.mysql.com/doc/refman/8.0/en/replication-options-replica.html#option_mysqld_replicate-wild-ignore-table) .

### <a name="requirements"></a>Követelmények
- A főkiszolgáló verziójának legalább a MySQL 5,6-es verziójának kell lennie. 
- A fő-és a replika-kiszolgáló verziószámának azonosnak kell lennie. Például mindkettőnek a MySQL 5,6-es vagy újabb verziójúnak kell lennie a MySQL 5,7-es verziójának.
- Minden táblának rendelkeznie kell egy elsődleges kulccsal.
- A főkiszolgálónak a MySQL InnoDB motort kell használnia.
- A felhasználónak rendelkeznie kell engedéllyel a bináris naplózás konfigurálásához és új felhasználók létrehozásához a főkiszolgálón.
- Ha a főkiszolgálón engedélyezve van az SSL, ellenőrizze, hogy a tartományhoz megadott SSL HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány szerepel-e a `mysql.az_replication_change_master` tárolt eljárásban. Tekintse át az alábbi [példákat](https://docs.microsoft.com/azure/mysql/howto-data-in-replication#link-master-and-replica-servers-to-start-data-in-replication) és a `master_ssl_ca` paramétert.
- Győződjön meg arról, hogy a fő kiszolgáló IP-címe hozzá lett adva az Azure Database for MySQL replikakiszolgálójának tűzfalszabályaihoz. A tűzfalszabályokat az [Azure Portallal](https://docs.microsoft.com/azure/mysql/howto-manage-firewall-using-portal) vagy az [Azure CLI-vel](https://docs.microsoft.com/azure/mysql/howto-manage-firewall-using-cli) frissítheti.
- Győződjön meg arról, hogy a főkiszolgálót üzemeltető gép engedélyezi a bejövő és kimenő forgalmat is a 3306-os porton.
- Győződjön meg arról, hogy a főkiszolgáló **nyilvános IP-címmel**rendelkezik, a DNS nyilvánosan elérhető, vagy rendelkezik teljes tartománynévvel (FQDN).

### <a name="other"></a>Egyéb
- Az adatreplikálás csak általános célú és a memória optimalizált díjszabási szintjein támogatott.
- A globális tranzakciós azonosítók (GTID-EK) nem támogatottak.

## <a name="next-steps"></a>További lépések
- Ismerje meg, hogyan [állíthatja be az adatreplikációt](howto-data-in-replication.md)
- Tudnivalók [Az Azure-beli replikálásról olvasási replikákkal](concepts-read-replicas.md)
- Az [adatáttelepítés minimális állásidővel való áttelepítésének ismertetése a DMS használatával](howto-migrate-online.md)