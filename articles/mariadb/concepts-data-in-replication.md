---
title: Adatok replikálása MariaDB-hez készült Azure Database-be.
description: Ez a cikk ismerteti az adatok a replikáció Azure Database for MariaDB-hez.
author: ajlam
ms.author: andrela
editor: jasonwhowell
services: mariadb
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: a6bb6ae45c4240f21b1e0baf4956b053d4b98720
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46948179"
---
# <a name="replicate-data-into-azure-database-for-mariadb"></a>Replikálják az adatokat MariaDB-hez készült Azure Database-be

Adatok a replikáció lehetővé teszi, hogy szinkronizálja az adatokat a helyszínen futó virtuális gépeket vagy adatbázis-szolgáltatások be az Azure Database for MariaDB szolgáltatás egyéb felhőszolgáltatók által üzemeltetett MariaDB-kiszolgálóról. Adatok a replikáció a bináris napló (binlog) pozíció-alapú fájlreplikációs MariaDB natív alapul. Binlog replikációval kapcsolatos további tudnivalókért tekintse meg a [binlog replikálációs szolgáltatása-áttekintés](https://mariadb.com/kb/en/library/replication-overview/).

## <a name="when-to-use-data-in-replication"></a>Mikor érdemes használni az adatok a replikáció
A fő forgatókönyvet kell figyelembe venni, a replikációs adatokat a rendszer:

- **Hibrid adatok szinkronizálása:** adatok replikációs között a helyszíni kiszolgálók és az Azure Database for MariaDB szinkronizált adatok tárolhatja. Hibrid alkalmazások létrehozása a szinkronizálás hasznos. Ez a módszer akkor tetszetős, ha már rendelkezik egy meglévő helyi adatbázis-kiszolgáló, de szeretne áthelyezni az adatokat egy régió közelebb a végfelhasználók számára.
- **Több felhőalapú szinkronizálás:** összetett felhőalapú megoldások, a használati adatok a replikáció adatszinkronizálás az Azure Database for MariaDB és eltérő felhőszolgáltatókat, beleértve a virtuális gépeket és ezeket a felhőben lévő üzemeltetett adatbázis-szolgáltatásokat.

## <a name="limitations-and-considerations"></a>Korlátozások és szempontok

### <a name="data-not-replicated"></a>Nem replikált adatok
A [ *mysql rendszeradatbázis* ](https://mariadb.com/kb/en/library/the-mysql-database-tables/) a fölérendelt kiszolgálón nincs replikálva. Fiókok és engedélyek a fölérendelt kiszolgáló a módosítások nem lesznek replikálva. Ha a fölérendelt kiszolgálón hozzon létre egy fiókot, és ezt a fiókot hozzá kell férniük a replikakiszolgáló, manuálisan hozzon létre ugyanazt a fiókot a replika kiszolgálói oldalon. Milyen táblák tárolják a rendszeradatbázis megismeréséhez tekintse meg a [MariaDB dokumentáció](https://mariadb.com/kb/en/library/the-mysql-database-tables/).

### <a name="requirements"></a>Követelmények
- A fő kiszolgálónak kell lennie legalább MariaDB 10.2 verzió.
- A master és a replika server-verziók azonosnak kell lennie. Például mindkét verziója 10.2 MariaDB kell lennie.
- Minden táblának elsődleges kulccsal kell rendelkeznie.
- Főkiszolgáló a InnoDB motor kell használnia.
- Felhasználó bináris naplózás konfigurálása és az új felhasználók létrehozására a fölérendelt kiszolgáló a szükséges engedélyekkel kell rendelkeznie.

### <a name="other"></a>Egyéb
- Adatok a replikáció csak támogatott az általános célú és memóriahasználatra optimalizált tarifacsomagok van.
- Globális tranzakciók azonosítók (GTID) használata nem támogatott.

## <a name="next-steps"></a>További lépések
- Ismerje meg, hogyan [adatok a replikáció beállítása](howto-data-in-replication.md).
