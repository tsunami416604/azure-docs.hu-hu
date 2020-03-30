---
title: Tárolt eljárások kezelése – Azure-adatbázis a MariaDB-hez
description: Ismerje meg, hogy mely tárolt eljárások at Azure Database for MariaDB hasznos az adatok replikációjának konfigurálásához, az időzóna beállításához és a lekérdezések leállításához.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 2f6d1e20db64cb0c2a64771ea26b971b22031fd9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79529990"
---
# <a name="azure-database-for-mariadb-management-stored-procedures"></a>Azure Database for MariaDB management tárolt eljárások

A tárolt eljárások elérhetők az Azure Database for MariaDB-kiszolgálókon a MariaDB-kiszolgáló kezeléséhez. Ez magában foglalja a kiszolgáló kapcsolatainak, lekérdezéseinek és az adatreplikáció beállításának kezelését.  

## <a name="data-in-replication-stored-procedures"></a>Tárolt adatreplikációs eljárások

A beérkező adatokra épülő replikáció lehetővé teszi, hogy szinkronizálja egy helyszínen, virtuális gépeken vagy más felhőszolgáltatók által üzemeltetett adatbázis-szolgáltatásokban futó MariaDB-kiszolgáló adatait az Azure Database for MariaDB-szolgáltatásba.

A következő tárolt eljárások segítségével állítsa be vagy távolítsa el a replikát és a kópia közötti adatreplikációt.

|**Tárolt eljárás neve**|**Bemeneti paraméterek**|**Kimeneti paraméterek**|**Használati megjegyzés**|
|-----|-----|-----|-----|
|*mysql.az_replication_change_master*|master_host<br/>master_user<br/>master_password<br/>master_port<br/>master_log_file<br/>master_log_pos<br/>master_ssl_ca|N/A|Az SSL-módban történő adatátvitelhez adja át a hitelesítésszolgáltatói tanúsítvány környezetét a master_ssl_ca paraméterbe. </br><br>Ha SSL nélküli adatátvitelhez üres karakterláncot szeretne átadni a master_ssl_ca paraméterbe.|
|*mysql.az_replication _start*|N/A|N/A|Elindítja a replikációt.|
|*mysql.az_replication _stop*|N/A|N/A|Leállítja a replikációt.|
|*mysql.az_replication _remove_master*|N/A|N/A|Eltávolítja a főkiszolgáló és a replika közötti replikációs kapcsolatot.|
|*mysql.az_replication_skip_counter*|N/A|N/A|Kihagy egy replikációs hibát.|

A főkiszolgáló és a kópia közötti adatreplikáció beállításához olvassa el az Adatreplikáció konfigurálását az Azure Database for [MariaDB-ben.](howto-data-in-replication.md)

## <a name="other-stored-procedures"></a>Egyéb tárolt eljárások

A következő tárolt eljárások érhetők el az Azure Database for MariaDB a kiszolgáló kezeléséhez.

|**Tárolt eljárás neve**|**Bemeneti paraméterek**|**Kimeneti paraméterek**|**Használati megjegyzés**|
|-----|-----|-----|-----|
|*mysql.az_kill*|processlist_id|N/A|Egyenértékű [`KILL CONNECTION`](https://dev.mysql.com/doc/refman/8.0/en/kill.html) a parancs. Megszakítja a megadott processlist_id kapcsolódó kapcsolatot a kapcsolat által futtatott bármely utasítás megszüntetése után.|
|*mysql.az_kill_query*|processlist_id|N/A|Egyenértékű [`KILL QUERY`](https://dev.mysql.com/doc/refman/8.0/en/kill.html) a parancs. Megszakítja a kapcsolat által jelenleg végrehajtó utasítást. Életben hagyja magát a kapcsolatot.|
|*mysql.az_load_timezone*|N/A|N/A|Időzóna-táblák betöltése, hogy a `time_zone` paraméter elnevezett értékekre legyen állítva (pl. "US/Pacific").|

## <a name="next-steps"></a>További lépések
- Az [adatreplikáció](howto-data-in-replication.md) beállítása
- Az [időzóna-táblák](howto-server-parameters.md#working-with-the-time-zone-parameter) használatának elsajátítása