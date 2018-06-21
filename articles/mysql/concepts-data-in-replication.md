---
title: A MySQL replikálja az adatokat az Azure-adatbázisba.
description: Ez a cikk ismerteti az adatok a replikáció az Azure Database MySQL.
services: mysql
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mysql
ms.topic: article
ms.date: 06/20/2018
ms.openlocfilehash: 72f8211ecc0534b15402911de8fc0ec3d541a835
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/20/2018
ms.locfileid: "36294904"
---
# <a name="replicate-data-into-azure-database-for-mysql"></a>A MySQL replikálja az adatokat az Azure-adatbázisba

Adatok a replikáció szinkronizálja az adatokat a helyszíni futtató virtuális gépek vagy adatbázis-szolgáltatásokat az Azure-adatbázisba MySQL szolgáltatáshoz más szolgáltatók által üzemeltetett MySQL kiszolgálóról teszi lehetővé. Adatok a replikáció a bináris napló (binlog) pozíció-alapú fájlreplikációs natív módon MySQL alapul. Binlog replikációval kapcsolatos további tudnivalókért tekintse meg a [MySQL binlog szolgáltatása-áttekintés](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html). 

## <a name="when-to-use-data-in-replication"></a>Mikor érdemes használni az adatok a replikáció
Adatok a replikáció használatával megfontolandó főbb forgatókönyvek a következők:

- **Hibrid adatszinkronizálás:** adatok a replikáció, a helyszíni kiszolgálók és az Azure-adatbázis között a MySQL szinkronizált adatok biztosítható. Hibrid alkalmazások létrehozása a szinkronizálás. Ez a módszer akkor tetszetős, ha olyan helyi adatbázis-kiszolgálóval rendelkezik, de szeretné, hogy az adatok áthelyezése egy régiót közelebb a végfelhasználók számára.
- **Több Felhőbeli szinkronizálásnak:** összetett megoldások, használja adatok a replikáció adatszinkronizálás MySQL az Azure-adatbázis és a különböző szolgáltatók, többek között a virtuális gépek és azokat a tárolt adatbázis szolgáltatások.

## <a name="limitations-and-considerations"></a>Korlátozások és megfontolások

### <a name="data-not-replicated"></a>Nem replikált adatok
A [ *mysql rendszeradatbázis* ](https://dev.mysql.com/doc/refman/5.7/en/system-database.html) az elsődleges kiszolgálón a rendszer nem replikálja. Fiókok és engedélyek az elsődleges kiszolgálón módosításai nem replikálódnak. Ha ezt a fiókot hozzá kell férniük a replikakiszolgáló az elsődleges kiszolgálón hozzon létre egy fiókot, majd manuálisan hozza létre ugyanazt a fiókot a replika kiszolgáló oldalán. Ha szeretné megtudni, milyen táblák találhatók a rendszerben adatbázisában, lásd: a [MySQL manuális](https://dev.mysql.com/doc/refman/5.7/en/system-database.html).

### <a name="requirements"></a>Követelmények
- Az elsődleges kiszolgáló verziója legalább kell MySQL 5.6 verzióját. 
- Az elsődleges és replika server-verziók azonosnak kell lennie. Például a MySQL 5.6 verzióját kell lennie, vagy a verzió 5.7 MySQL kell lennie.
- Minden tábla elsődleges kulccsal kell rendelkeznie.
- Elsődleges kiszolgálónak használnia kell a MySQL InnoDB motor.
- Felhasználói bináris naplózás konfigurálása, és hozzon létre új felhasználók az elsődleges kiszolgálón engedélyekkel kell rendelkeznie.

### <a name="other"></a>Egyéb
- Globális tranzakció azonosítók (GTID) használata nem támogatott.

## <a name="next-steps"></a>További lépések
- Megtudhatja, hogyan [adatok a replikáció beállítása](howto-data-in-replication.md)
