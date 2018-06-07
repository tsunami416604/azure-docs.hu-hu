---
title: A MySQL-adatok a replikáció Azure-adatbázis tárolt eljárások
description: Ez a cikk be adatok a replikáció használt összes tárolt eljárásokat.
services: mysql
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 05/18/2018
ms.openlocfilehash: 8683aacb2ae41cb4c57aa70b375eca9db6106492
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34655192"
---
# <a name="azure-database-for-mysql-data-in-replication-stored-procedures"></a>A MySQL-adatok a replikáció Azure-adatbázis tárolt eljárások

Adatok a replikáció szinkronizálja az adatokat a helyszíni futtató virtuális gépek vagy adatbázis-szolgáltatásokat az Azure-adatbázisba MySQL szolgáltatáshoz más szolgáltatók által üzemeltetett MySQL kiszolgálóról teszi lehetővé.

A következő tárolt eljárások segítségével állítsa be, vagy egy elsődleges kiszolgáló és a replikakiszolgáló közötti adatok a replikáció.

|**A tárolt eljárás neve**|**A bemeneti paraméterek**|**A kimeneti paraméterek**|**Használati Megjegyzés**|
|-----|-----|-----|-----|
|*MySQL.az_replication_change_primary*|master_host<br/>master_user<br/>master_password<br/>master_port<br/>master_log_file<br/>master_log_pos<br/>master_ssl_ca|–|Az SSL-mód adattovábbításra, továbbítja a hitelesítésszolgáltató tanúsítványát a környezetben a master_ssl_ca paraméter. </br><br>Adatátvitel SSL nélküli, adjon át egy üres karakterlánc a master_ssl_ca paraméterben.|
|*MySQL.az_replication _induljon el*|–|–|Elindítja a replikáció.|
|*MySQL.az_replication _stop*|–|–|Leállítja a replikáció.|
|*MySQL.az_replication _remove_primary*|–|–|Megszünteti a replikációs kapcsolatot az elsődleges és a replika között.|
|*MySQL.az_replication_skip_counter*|–|–|Egy replikációs hiba kihagyja.|

Adatok az elsődleges és replika közötti replikációt egy Azure-adatbázisban a MySQL beállításához tekintse meg [adatok a replikáció konfigurálásával](howto-data-in-replication.md).