---
title: Azure Database for MySQL-adatok a replikálási tárolt eljárások
description: Ez a cikk az adatok a replikációhoz használt összes tárolt eljárások be.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 08/31/2018
ms.openlocfilehash: a3c88953eea95871529e8ab257f52b694db443a9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "61244310"
---
# <a name="azure-database-for-mysql-data-in-replication-stored-procedures"></a>Azure Database for MySQL-adatok a replikálási tárolt eljárások

Adatok a replikáció lehetővé teszi egy MySQL-kiszolgálót a helyszínen futó virtuális gépeket vagy adatbázis-szolgáltatások az Azure Database for MySQL-szolgáltatás az egyéb felhőszolgáltatók által üzemeltetett adatai szinkronizálva.

A következő tárolt eljárások segítségével állítsa be, vagy távolítsa el az adatok a replikáció a master és a replika között.

|**Tárolt eljárás neve**|**Bemeneti paraméterek**|**A kimeneti paraméterek**|**Használati Megjegyzés**|
|-----|-----|-----|-----|
|*mysql.az_replication_change_master*|master_host<br/>master_user<br/>master_password<br/>master_port<br/>master_log_file<br/>master_log_pos<br/>master_ssl_ca|–|Adatok áthelyezése az SSL-mód, át kell adnia a hitelesítésszolgáltató tanúsítványát a környezetben be master_ssl_ca paraméter. </br><br>SSL nélküli adatátvitelhez, adja át egy üres karakterlánc master_ssl_ca paraméter be.|
|*mysql.az_replication _start*|–|–|Elindítja a replikáció.|
|*mysql.az_replication _stop*|–|–|Replikáció leállítása.|
|*mysql.az_replication _remove_master*|–|–|Eltávolítja a replikációs kapcsolatot a master és a replika között.|
|*mysql.az_replication_skip_counter*|–|–|Egy replikációs hiba kihagyja.|

Állítsa be adatokat a közötti replikáció egy fő- és a egy replikát, az Azure Database for MySQL-hez, tekintse meg [adatok a replikáció konfigurálása](howto-data-in-replication.md).
