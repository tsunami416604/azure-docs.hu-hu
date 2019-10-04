---
title: Tárolt eljárások Azure Database for MariaDB
description: Ez a cikk a Azure Database for MariaDBra vonatkozó tárolt eljárásokat mutatja be.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/20/2019
ms.openlocfilehash: d9daaf619a19c0f4e4a591d4bbb4925679fd1fcb
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/20/2019
ms.locfileid: "71174902"
---
# <a name="azure-database-for-mariadb-stored-procedures"></a>Tárolt eljárások Azure Database for MariaDB

A tárolt eljárások Azure Database for MariaDB-kiszolgálókon érhetők el a MariaDB-kiszolgáló kezelésének elősegítése érdekében. Ide tartozik a kiszolgáló kapcsolatainak kezelése, a lekérdezések és a felhőbe irányuló replikálás beállítása.  

## <a name="data-in-replication-stored-procedures"></a>Tárolt eljárások felhőbe irányuló replikálás

A beérkező adatokra épülő replikáció lehetővé teszi, hogy szinkronizálja egy helyszínen, virtuális gépeken vagy más felhőszolgáltatók által üzemeltetett adatbázis-szolgáltatásokban futó MariaDB-kiszolgáló adatait az Azure Database for MariaDB-szolgáltatásba.

A következő tárolt eljárások a főkiszolgálók és a replikák közötti felhőbe irányuló replikálás beállítására és eltávolítására szolgálnak.

|**Tárolt eljárás neve**|**Bemeneti paraméterek**|**Kimeneti paraméterek**|**Használati Megjegyzés**|
|-----|-----|-----|-----|
|*mysql.az_replication_change_master*|master_host<br/>master_user<br/>master_password<br/>master_port<br/>master_log_file<br/>master_log_pos<br/>master_ssl_ca|–|Az adatok SSL-móddal történő átviteléhez adja át a HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány környezetét a master_ssl_ca paraméternek. </br><br>Az adatok SSL nélküli átviteléhez adjon meg egy üres karakterláncot a master_ssl_ca paraméternek.|
|*mysql.az_replication _start*|–|–|Elindítja a replikálást.|
|*mysql.az_replication _stop*|–|–|Leállítja a replikálást.|
|*mysql.az_replication _remove_master*|–|–|Eltávolítja a replikálási kapcsolatot a fő és a replika között.|
|*mysql.az_replication_skip_counter*|–|–|Egy replikációs hiba kihagyása.|

A Azure Database for MariaDB a Master és a replika közötti felhőbe irányuló replikálás beállításához tekintse meg a [felhőbe irányuló replikálás konfigurálását ismertető témakört](howto-data-in-replication.md).

## <a name="other-stored-procedures"></a>Egyéb tárolt eljárások

A következő tárolt eljárások érhetők el Azure Database for MariaDB a kiszolgáló kezeléséhez.

|**Tárolt eljárás neve**|**Bemeneti paraméterek**|**Kimeneti paraméterek**|**Használati Megjegyzés**|
|-----|-----|-----|-----|
|*MySQL. az _Kill*|processlist_id|–|Egyenértékű a [`KILL CONNECTION`](https://dev.mysql.com/doc/refman/8.0/en/kill.html) paranccsal. A leállítja a megadott processlist_id társított kapcsolatokat a kapcsolatok végrehajtásához szükséges összes utasítás megszakítása után.|
|*MySQL. az _kill_query*|processlist_id|–|Egyenértékű a [`KILL QUERY`](https://dev.mysql.com/doc/refman/8.0/en/kill.html) paranccsal. Leállítja azt az utasítást, amely szerint a kapcsolatok jelenleg végrehajtás alatt állnak. Maga a kapcsolatok maradnak életben.|
|*MySQL. az _load_timezone*|–|–|Betölti az időzóna-táblákat, hogy a `time_zone` paraméter megnevezett értékre legyen beállítva (pl. "USA/csendes-óceáni térség").|

## <a name="next-steps"></a>További lépések
- További információ a [felhőbe irányuló replikálás](howto-data-in-replication.md) beállításáról
- Az [időzóna-táblázatok](howto-server-parameters.md#working-with-the-time-zone-parameter) használatának ismertetése