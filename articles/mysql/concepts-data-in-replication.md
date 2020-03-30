---
title: Adatreplikáció – Azure-adatbázis a MySQL-hez
description: Ismerje meg, hogyan használatával adat-in replikáció szinkronizálása egy külső kiszolgálóról az Azure Database for MySQL szolgáltatás.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 20be34191355e6ade40e0f3b218818bfa5345a28
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79533232"
---
# <a name="replicate-data-into-azure-database-for-mysql"></a>Adatok replikálása a MySQL Azure-adatbázisába

A Data-in Replication lehetővé teszi a külső MySQL-kiszolgáló adatainak szinkronizálását az Azure Database for MySQL szolgáltatásba. A külső kiszolgáló lehet a helyszíni, a virtuális gépek, vagy egy adatbázis-szolgáltatás üzemelteti más felhőszolgáltatók által üzemeltetett. A beérkező adatokra épülő replikáció a MySQL natív bináris naplójának (binlog) fájlpozíció-alapú replikációján alapul. A binlog replikációról a [MySQL-adatok replikációjának áttekintése](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html)című témakörben olvashat bővebben. 

## <a name="when-to-use-data-in-replication"></a>Mikor kell használni az adatreplikációt?
Az adatreplikáció főbb forgatókönyvei a következők:

- **Hibrid adatszinkronizálás:** A Data-in replication segítségével a helyszíni kiszolgálók és az Azure Database for MySQL között szinkronizálhatja az adatokat. Ez a szinkronizálás hibrid alkalmazások létrehozásához hasznos. Ez a módszer akkor vonzó, ha meglévő helyi adatbázis-kiszolgálóval rendelkezik, de az adatokat egy régióba szeretné áthelyezni a végfelhasználókhoz közelebb.
- **Többfelhős szinkronizálás:** Összetett felhőalapú megoldások esetén a Data-in Replication használatával szinkronizálhatja az adatokat az Azure Database for MySQL és a különböző felhőszolgáltatók között, beleértve a virtuális gépeket és az ezekben a felhőkben üzemeltetett adatbázis-szolgáltatásokat.
 
Áttelepítési forgatókönyvek esetén használja az [Azure Database Migration Service](https://azure.microsoft.com/services/database-migration/)(DMS) szolgáltatást.

## <a name="limitations-and-considerations"></a>Korlátozások és megfontolások

### <a name="data-not-replicated"></a>Nem replikált adatok
A főkiszolgálón lévő [*mysql rendszeradatbázis*](https://dev.mysql.com/doc/refman/5.7/en/system-schema.html) nem replikálódik. A főkiszolgálón lévő fiókok és engedélyek módosításai nem replikálódnak. Ha fiókot hoz létre a főkiszolgálón, és ennek a fióknak hozzá kell férnie a replikakiszolgálóhoz, manuálisan hozza létre ugyanazt a fiókot a replikakiszolgáló oldalán. Ha meg szeretné tudni, hogy a rendszeradatbázis milyen táblákat tartalmaz, olvassa el a [MySQL kézikönyvet.](https://dev.mysql.com/doc/refman/5.7/en/system-schema.html)

### <a name="requirements"></a>Követelmények
- A fő kiszolgáló verziónak legalább a MySQL 5.6-os verziójának kell lennie. 
- A fő- és replikakiszolgáló verzióinak meg kell egyeznek. Például mindkettőnek MySQL 5.6-os verziónak kell lennie, vagy mindkettőnek MySQL 5.7-es verziónak kell lennie.
- Minden táblának rendelkeznie kell egy elsődleges kulccsal.
- A főkiszolgálónak a MySQL InnoDB motort kell használnia.
- A felhasználónak engedéllyel kell rendelkeznie a bináris naplózás konfigurálásához és új felhasználók létrehozásához a főkiszolgálón.
- Ha a főkiszolgálón engedélyezve van az SSL, győződjön meg arról, hogy a tartományhoz megadott SSL hitelesítésszolgáltatói tanúsítvány szerepel a `mysql.az_replication_change_master` tárolt eljárásban. Tekintse meg a következő `master_ssl_ca` [példákat](https://docs.microsoft.com/azure/mysql/howto-data-in-replication#link-master-and-replica-servers-to-start-data-in-replication) és a paramétert.
- Győződjön meg arról, hogy a fő kiszolgáló IP-címe hozzá lett adva az Azure Database for MySQL replikakiszolgálójának tűzfalszabályaihoz. A tűzfalszabályokat az [Azure Portal](https://docs.microsoft.com/azure/mysql/howto-manage-firewall-using-portal) vagy az [Azure CLI](https://docs.microsoft.com/azure/mysql/howto-manage-firewall-using-cli) használatával frissítheti.
- Győződjön meg arról, hogy a főkiszolgálót üzemeltető gép engedélyezi a bejövő és kimenő forgalmat is a 3306-os porton.
- Győződjön meg arról, hogy a főkiszolgáló **rendelkezik nyilvános IP-címmel,** a DNS nyilvánosan elérhető, vagy teljesen minősített tartománynévvel (FQDN) rendelkezik.

### <a name="other"></a>Egyéb
- Az adatok on-in replikációcsak általános célú és memóriaoptimalizált tarifaszintek támogatottak.
- A globális tranzakcióazonosítók (GTID) nem támogatottak.

## <a name="next-steps"></a>További lépések
- Az [adatreplikáció beállítása](howto-data-in-replication.md)
- További információ [az Azure-beli replikálásról olvasási replikákkal](concepts-read-replicas.md)
- Tudnivalók az [adatok minimális állásidő vel történő áttelepítéséről a DMS használatával](howto-migrate-online.md)