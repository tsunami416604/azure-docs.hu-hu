---
title: Adatok replikálása MariaDB-hez készült Azure Database-be.
description: Ez a cikk ismerteti az adatok a replikáció Azure Database for MariaDB-hez.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 0a1ead1580f6764fec7d1d18daa38bf093f242f2
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/17/2018
ms.locfileid: "53547609"
---
# <a name="replicate-data-into-azure-database-for-mariadb"></a>Replikálják az adatokat MariaDB-hez készült Azure Database-be

Adatok a replikáció lehetővé teszi, hogy szinkronizálja az adatokat a helyszínen futó virtuális gépeket vagy adatbázis-szolgáltatások be az Azure Database for MariaDB szolgáltatás egyéb felhőszolgáltatók által üzemeltetett MariaDB-kiszolgálóról. Adatok a replikáció a bináris napló (binlog) pozíció-alapú fájlreplikációs MariaDB natív alapul. Binlog replikációval kapcsolatos további tudnivalókért tekintse meg a [binlog replikálációs szolgáltatása-áttekintés](https://mariadb.com/kb/en/library/replication-overview/).

## <a name="when-to-use-data-in-replication"></a>Mikor érdemes használni az adatok a replikáció
A fő forgatókönyvet kell figyelembe venni, a replikációs adatokat a rendszer:

- **Hibrid adatok szinkronizálása:** Az adatok a replikáció beállíthatja, hogy a helyszíni kiszolgálók között az Azure Database for MariaDB szinkronizált adatok. Hibrid alkalmazások létrehozása a szinkronizálás hasznos. Ez a módszer akkor tetszetős, ha már rendelkezik egy meglévő helyi adatbázis-kiszolgáló, de szeretne áthelyezni az adatokat egy régió közelebb a végfelhasználók számára.
- **Több felhőalapú szinkronizálás:** Összetett felhőalapú megoldásokhoz adatok a replikáció használata, Azure Database for MariaDB és eltérő felhőszolgáltatókat, beleértve a virtuális gépeket és ezeket a felhőben lévő üzemeltetett adatbázis-szolgáltatások közötti adatszinkronizáláshoz.

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
