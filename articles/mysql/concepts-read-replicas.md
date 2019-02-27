---
title: Olvassa el a replikákat az Azure Database MySQL-hez.
description: Ez a cikk ismerteti olvasható replikák az Azure Database for MySQL-hez.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 02/26/2019
ms.openlocfilehash: 6e33c7571dc735ce9984a0ce1b37275a6c4c7eca
ms.sourcegitcommit: 24906eb0a6621dfa470cb052a800c4d4fae02787
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2019
ms.locfileid: "56888468"
---
# <a name="read-replicas-in-azure-database-for-mysql"></a>Olvassa el a replikákat az Azure Database for MySQL-hez

Az olvasási replika funkció lehetővé teszi, hogy replikálja az adatokat egy Azure Database for MySQL-kiszolgáló (master) legfeljebb öt csak olvasható kiszolgálók (replikák) azonos Azure-régióban. Csak olvasható replika aszinkron módon frissíti a MySQL-motor natív bináris napló (binlog) fájl elhelyezése-alapú replikációs technológiával. Binlog replikációval kapcsolatos további tudnivalókért tekintse meg a [MySQL binlog replikálációs szolgáltatása-áttekintés](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html).

Az Azure Database for MySQL szolgáltatáshoz készült replika új kiszolgálókat, amelyek normál vagy önálló MySQL-kiszolgálók azonos módon kezelhetők. A díj minden olvasási replika esetén a virtuális magok kiépített számítási feladatain és a GB/hó alapon megszabott tárhelyen alapul.

MySQL replikációs szolgáltatásai és problémákkal kapcsolatos további információkért tekintse meg a [MySQL replikációs dokumentáció](https://dev.mysql.com/doc/refman/5.7/en/replication-features.html).

## <a name="when-to-use-read-replicas"></a>Mikor érdemes használni az olvasási replikák

Alkalmazások és számítási feladatok nagy számításigényű olvasható a csak olvasható replikák kiszolgálható. Olvasási replikák növeléséhez képest Ha csak egyetlen kiszolgáló használata olvasási és írási olvasási rendelkezésre álló kapacitás mennyisége. Az olvasási számítási feladatok is lehet a replikákra, elkülönített, miközben a írási számítási feladatok a fő lehet irányítani.

Gyakran előfordul, hogy rendelkezzen a BI-ban, és elemzési számítási feladatok az olvasási-replika használatára adatforrásként a jelentéskészítéshez.

## <a name="considerations-and-limitations"></a>Megfontolandó szempontok és korlátozások

### <a name="pricing-tiers"></a>Árképzési szintek

Olvasható replika jelenleg csak az általános célú és memóriahasználatra optimalizált tarifacsomagok érhető el.

### <a name="master-server-restart"></a>Fő kiszolgáló újraindítása

Ha a replika tartozik egy főkiszolgálóhoz, amely nem tartalmaz meglévő másodpéldányokat hoz létre, a fő először újraindul felkészüljön a replikáció. Figyelembe kell venni ezt, és hajtsa végre ezeket a műveleteket egy csúcsidőn kívüli időszakban.

### <a name="stopping-replication"></a>Replikáció leállítása

Ha szeretné, a master és a replikakiszolgáló közötti replikáció leállítása. Replikáció leállítása eltávolítja a replikációs kapcsolatot a fő és az adatbázisreplika-kiszolgáló között.

Replikáció le lett állítva, a replikakiszolgáló egy önálló kiszolgáló lesz. Az önálló kiszolgáló adatai az adatokat, amely a időben, a "replikáció leállítása" parancsot kezdeményezett a replikán elérhető volt. Az önálló kiszolgáló nem dolgozza a főkiszolgálóval. Ez a kiszolgáló nem hajtható végre egy replika be újra.

### <a name="replicas-are-new-servers"></a>Replika új kiszolgálók

Replikák jönnek létre, új, Azure Database for MySQL-kiszolgálók. Meglévő kiszolgálók nem hajtható végre, a replikákat.

### <a name="replica-server-configuration"></a>Adatbázisreplika-kiszolgáló konfigurációja

Replikakiszolgáló használatával server ugyanezzel a konfigurációval a master, amely tartalmazza a következő konfigurációk jön létre:

- Tarifacsomag
- Számítási generáció
- Virtuális magok
- Storage
- Biztonsági másolat megőrzési idejének
- Biztonsági mentési adatredundáns tárolási mód
- MySQL engine version
- Tűzfalszabályok

Replika létrehozása után módosíthatja a tarifacsomagot (kivéve a, illetve onnan alapszintű), számítási generáció, virtuális magok, tárolási és alkalmazástól függetlenül a fölérendelt kiszolgáló a biztonsági másolatok.

### <a name="master-server-configuration"></a>Fő kiszolgálókonfiguráció

Ha egy fő kiszolgálókonfiguráció (például. virtuális maggal és tárolás) frissüljön, a replikák konfigurációs frissíteni kell, egyenlő vagy nagyobb értékre. E nélkül az adatbázisreplika-kiszolgáló nem tud lépést tartani a fő végrehajtott módosítások, és emiatt leállhat.

Új tűzfalszabályok a fölérendelt kiszolgáló hozzáadott egy adatbázisreplika-kiszolgáló létrehozása után nem lesznek replikálva a replika. A replika-e új tűzfalszabályt is frissíteni kell.

### <a name="deleting-the-master-server"></a>A fölérendelt kiszolgáló törlése

A törölt egy fölérendelt kiszolgáló replikációja leáll minden olvasási replikákra. Ezek a replikák önálló kiszolgálók válnak. A fölérendelt kiszolgáló maga törlődik.

### <a name="user-accounts"></a>Felhasználói fiókok

A fölérendelt kiszolgáló a felhasználók a olvasható replikák replikálódnak. Csak olvasási replika érhető el a felhasználói fiókok használata a fő kiszolgálón csatlakozni.

### <a name="other"></a>Egyéb

- Globális tranzakciók azonosítók (GTID) használata nem támogatott.
- A replika egy replika létrehozása nem támogatott.
- Előfordulhat, hogy a táblák replikák szinkronizálva lesz. Ez az egyik korlátozása a MySQL replikációs technológiával. További információ: a [MySQL dokumentációja](https://dev.mysql.com/doc/refman/5.7/en/replication-features-memory.html) további információt.
- Hangolási a [ `innodb_file_per_table` ](https://dev.mysql.com/doc/refman/5.7/en/innodb-multiple-tablespaces.html) paraméter egy fő kiszolgálón után lesz szinkronizálva a replika egy adatbázisreplika-kiszolgáló létrehozása okozhat. Az adatbázisreplika-kiszolgáló még nem ismeri, a másik táblahelyeket.
- Győződjön meg arról, a fölérendelt kiszolgáló táblák elsődleges kulcsot tartalmaznak. Elsődleges kulcsok hiánya a master és a replikák közötti replikációs késés eredményezhet.
- Tekintse át a MySQL replikációs korlátozások a teljes listáját a [MySQL dokumentációja](https://dev.mysql.com/doc/refman/5.7/en/replication-features.html)


## <a name="next-steps"></a>További lépések

- Ismerje meg, hogyan [létrehozása és kezelése az Azure portal használatával olvasható replikák](howto-read-replicas-portal.md)
- Ismerje meg, hogyan [létrehozása és kezelése az Azure CLI használatával olvasható replikák](howto-read-replicas-cli.md)
