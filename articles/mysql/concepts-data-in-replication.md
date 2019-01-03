---
title: Replikálják az adatokat MySQL-hez készült Azure Database-be.
description: Ez a cikk ismerteti az adatok a replikáció az Azure Database MySQL-hez.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 08/31/2018
ms.openlocfilehash: ca748dff67be2a37ca61f34602f207265cc77aaa
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/17/2018
ms.locfileid: "53544226"
---
# <a name="replicate-data-into-azure-database-for-mysql"></a>Replikálják az adatokat MySQL-hez készült Azure Database-be

Adatok a replikáció lehetővé teszi egy MySQL-kiszolgálót a helyszínen futó virtuális gépeket vagy adatbázis-szolgáltatások az Azure Database for MySQL-szolgáltatás az egyéb felhőszolgáltatók által üzemeltetett adatai szinkronizálva. Adatok a replikáció a bináris napló (binlog) pozíció-alapú fájlreplikációs natív, a MySQL-hez alapul. Binlog replikációval kapcsolatos további tudnivalókért tekintse meg a [MySQL binlog replikálációs szolgáltatása-áttekintés](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html). 

## <a name="when-to-use-data-in-replication"></a>Mikor érdemes használni az adatok a replikáció
A fő forgatókönyvet kell figyelembe venni, a replikációs adatokat a rendszer:

- **Hibrid adatok szinkronizálása:** Az adatok a replikáció beállíthatja, hogy adatokat szinkronizálja között a helyszíni kiszolgálók és az Azure Database for MySQL-hez. Hibrid alkalmazások létrehozása a szinkronizálás hasznos. Ez a módszer akkor tetszetős, ha már rendelkezik egy meglévő helyi adatbázis-kiszolgáló, de szeretne áthelyezni az adatokat egy régió közelebb a végfelhasználók számára.
- **Több felhőalapú szinkronizálás:** Összetett felhőalapú megoldásokat, az adatok a replikáció használatával adatszinkronizálás, Azure Database for MySQL és a különböző felhőszolgáltatók, beleértve a virtuális gépeket és ezeket a felhőben lévő üzemeltetett adatbázis-szolgáltatások.

## <a name="limitations-and-considerations"></a>Korlátozások és szempontok

### <a name="data-not-replicated"></a>Nem replikált adatok
A [ *mysql rendszeradatbázis* ](https://dev.mysql.com/doc/refman/5.7/en/system-database.html) a fölérendelt kiszolgálón nincs replikálva. Fiókok és engedélyek a fölérendelt kiszolgáló a módosítások nem lesznek replikálva. Ha a fölérendelt kiszolgálón hozzon létre egy fiókot, és ezt a fiókot hozzá kell férniük a replikakiszolgáló, manuálisan hozzon létre ugyanazt a fiókot a replika kiszolgálói oldalon. Milyen táblák tárolják a rendszeradatbázis megismeréséhez tekintse meg a [MySQL manuális](https://dev.mysql.com/doc/refman/5.7/en/system-database.html).

### <a name="requirements"></a>Követelmények
- A fő verziója kell, hogy a MySQL 5.6-os verziója. 
- A master és a replika server-verziók azonosnak kell lennie. Például a MySQL 5.6-os verziója is kell lennie, vagy a MySQL 5.7-es verzióra kell lennie.
- Minden táblának elsődleges kulccsal kell rendelkeznie.
- Fő kiszolgálóhoz a MySQL InnoDB motor kell használnia.
- Felhasználó bináris naplózás konfigurálása és az új felhasználók létrehozására a fölérendelt kiszolgáló a szükséges engedélyekkel kell rendelkeznie.

### <a name="other"></a>Egyéb
- Replikációs adatokat a rendszer csak támogatott az általános célú és memóriahasználatra optimalizált tarifacsomagok
- Globális tranzakciók azonosítók (GTID) használata nem támogatott.

## <a name="next-steps"></a>További lépések
- Ismerje meg, hogyan [adatok a replikáció beállítása](howto-data-in-replication.md)
