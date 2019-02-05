---
title: Replikálják az adatokat MySQL-hez készült Azure Database-be.
description: Ez a cikk ismerteti az adatok a replikáció az Azure Database MySQL-hez.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 02/01/2019
ms.openlocfilehash: f91a6da9a305c6620e4e01ab7aa3c554374cb5d7
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/04/2019
ms.locfileid: "55691522"
---
# <a name="replicate-data-into-azure-database-for-mysql"></a>Replikálják az adatokat MySQL-hez készült Azure Database-be

Adatok a replikáció lehetővé teszi egy külső MySQL-kiszolgáló adatai szinkronizálva, az Azure Database for MySQL-szolgáltatás. A külső kiszolgáló lehet a helyszínen, a virtuális gépek vagy egyéb felhőszolgáltatók által üzemeltetett egy adatbázis-szolgáltatás. Adatok a replikáció a bináris napló (binlog) pozíció-alapú fájlreplikációs natív, a MySQL-hez alapul. Binlog replikációval kapcsolatos további tudnivalókért tekintse meg a [MySQL binlog replikálációs szolgáltatása-áttekintés](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html). 

## <a name="when-to-use-data-in-replication"></a>Mikor érdemes használni az adatok a replikáció
A fő forgatókönyvet kell figyelembe venni, a replikációs adatokat a rendszer:

- **Hibrid adatok szinkronizálása:** Az adatok a replikáció beállíthatja, hogy adatokat szinkronizálja között a helyszíni kiszolgálók és az Azure Database for MySQL-hez. Hibrid alkalmazások létrehozása a szinkronizálás hasznos. Ezt a módszert akkor viszont, ha rendelkezik meglévő helyi adatbázis-kiszolgáló, de szeretne áthelyezni az adatokat egy régió közelebb a végfelhasználók számára.
- **Több felhőalapú szinkronizálás:** Összetett felhőalapú megoldásokat, az adatok a replikáció használatával adatszinkronizálás, Azure Database for MySQL és a különböző felhőszolgáltatók, beleértve a virtuális gépeket és ezeket a felhőben lévő üzemeltetett adatbázis-szolgáltatások.

## <a name="limitations-and-considerations"></a>Korlátozások és szempontok

### <a name="data-not-replicated"></a>Nem replikált adatok
A [ *mysql rendszeradatbázis* ](https://dev.mysql.com/doc/refman/5.7/en/system-database.html) a fő kiszolgálón nem replikálódnak. Fiókok és engedélyek a master kiszolgálón módosításait a rendszer nem replikálja. Ha a fölérendelt kiszolgálón hozzon létre egy fiókot, és ezt a fiókot hozzá kell férniük a replikakiszolgáló, manuálisan létrehozhat ugyanazzal a fiókkal a replika kiszolgálói oldalon. Milyen táblák tárolják a rendszeradatbázis megismeréséhez tekintse meg a [MySQL manuális](https://dev.mysql.com/doc/refman/5.7/en/system-database.html).

### <a name="requirements"></a>Követelmények
- A fő verziója kell, hogy a MySQL 5.6-os verziója. 
- A master és a replika server-verziók azonosnak kell lennie. Például a MySQL 5.6-os verziója is kell lennie, vagy a MySQL 5.7-es verzióra kell lennie.
- Minden táblának elsődleges kulccsal kell rendelkeznie.
- Fő kiszolgálóhoz a MySQL InnoDB motor kell használnia.
- Felhasználó bináris naplózás konfigurálása és az új felhasználók létrehozására a fölérendelt kiszolgáló a szükséges engedélyekkel kell rendelkeznie.

### <a name="other"></a>Egyéb
- Adatok a replikáció csak támogatott az általános célú és memóriahasználatra optimalizált tarifacsomagok van.
- Globális tranzakciók azonosítók (GTID) használata nem támogatott.

## <a name="next-steps"></a>További lépések
- Ismerje meg, hogyan [adatok a replikáció beállítása](howto-data-in-replication.md)
- Ismerje meg [replikák replikálása az Azure-ban olvasása](concepts-read-replicas.md)
