---
title: Olvassa el a replikákat az Azure Database MySQL-hez.
description: Ez a cikk ismerteti olvasható replikák az Azure Database for MySQL-hez.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 04/30/2019
ms.openlocfilehash: 2d70e1b5434b2fb263d1f4587888d4758fac2828
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2019
ms.locfileid: "66225360"
---
# <a name="read-replicas-in-azure-database-for-mysql"></a>Olvassa el a replikákat az Azure Database for MySQL-hez

Az olvasási replika szolgáltatás replikálja az adatokat egy Azure Database for MySQL-kiszolgálót egy írásvédett kiszolgáló teszi lehetővé. Legfeljebb öt replikához replikálhatja a fölérendelt kiszolgálótól. Replikák frissülnek, aszinkron módon használja a MySQL-motor natív bináris napló (binlog) fájl elhelyezése-alapú replikációs technológiával. Binlog replikációval kapcsolatos további tudnivalókért tekintse meg a [MySQL binlog replikálációs szolgáltatása-áttekintés](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html).

> [!IMPORTANT]
> Olvasási replikát hozhat létre, mint a fölérendelt kiszolgáló ugyanabban a régióban, vagy bármely más Azure-régióban a választott. Régiók közötti replikáció jelenleg nyilvános előzetes verzióban érhető el.

Replika, hogy a normál hasonló, Azure Database for MySQL-kiszolgálók kezelése új kiszolgálók. Az egyes olvassa el a replika, a számlázás a virtuális magok kiépített számítási és tárolási GB / hó.

MySQL replikációs szolgáltatásai és problémákkal kapcsolatos további információkért tekintse meg a [MySQL replikációs dokumentáció](https://dev.mysql.com/doc/refman/5.7/en/replication-features.html).

## <a name="when-to-use-a-read-replica"></a>Ha egy olvasási-replika használatára

A olvasható replika szolgáltatás segítségével a teljesítményét és skálázhatóságát olvasásigényű munkaterhelésekhez javítása érdekében. Olvasási számítási feladatok is lehet a replikákra, elkülönített, miközben írási számítási feladatok a fő lehet irányítani.

Gyakran előfordul, hogy rendelkezzen a BI-ban, és elemzési számítási feladatok az olvasási-replika használatára adatforrásként a jelentéskészítéshez.

Replikák írásvédettek, mivel azok nem csökkenti a fő írási kapacitású terheinek közvetlenül. Ez a funkció nem célzó írási-igényes számítási feladatokhoz.

Az olvasási replika funkció MySQL aszinkron replikációt használ. A szolgáltatás nem arra készült, a szinkron replikációs forgatókönyvek esetén. A master és a replika mérhető késleltetés lesz. A replikában tárolt idővel a fő lévő adatok konzisztens lesz. A funkció használatához a számítási feladatokhoz, amely képes kezelni ezt a késést.

Olvasható replikák javíthatja a vészhelyreállítási tervet. Ha regionális katasztrófa, és a fölérendelt kiszolgáló nem érhető el, a számítási feladat egy replikának egy másik régióban irányíthatók. Ehhez először lehetővé teszik a replikát, fogadja el az írási műveletek a stop-replikáció függvény használatával. Az alkalmazás ezután átirányíthatja a kapcsolati karakterlánc módosításával. További információ: a [replikációleállítás](#stop-replication) szakaszban.

## <a name="create-a-replica"></a>Replika létrehozása

Ha a fő kiszolgálón nem meglévő replika kiszolgálók, a fő először újraindul felkészüljön a replikációhoz.

Amikor a létrehozás replika munkafolyamat, egy üres, Azure Database for MySQL-kiszolgáló jön létre. Az új kiszolgáló ki van töltve az adatokkal, amelyek a fő kiszolgálón volt. A létrehozás ideje függ a master és a múlt heti teljes biztonsági mentés óta eltelt idő adatok mennyisége. Az idő pár percet vagy akár több óráig terjedhet.

Minden replika engedélyezve van a storage [automatikus növekedést](concepts-pricing-tiers.md#storage-auto-grow). A auto-grow funkció lehetővé teszi, hogy naprakész maradhat, replikálja az adatokat, és megakadályozza a tartományon kívül a tárolási hibák által okozott replikációban szünet a replikát.

Ismerje meg, hogyan [olvasható replika létrehozásához az Azure Portalon](howto-read-replicas-portal.md).

## <a name="connect-to-a-replica"></a>Csatlakozás egy replika

Amikor létrehoz egy replikát, a tűzfal- és VNet-szolgáltatásvégpont főkiszolgálójának nem örökli. Ezek a szabályok egymástól függetlenül a replika kell állítani.

A replika örökli a fölérendelt kiszolgáló a rendszergazdai fiókkal. A fölérendelt kiszolgáló az összes felhasználói fiók replikálva vannak a olvasható replikákat. Csak csatlakozhat egy olvasási replikát a fölérendelt kiszolgáló elérhető a felhasználói fiókok használatával.

Csatlakozhat a replika használatával, az állomásnév és a egy érvényes felhasználói fiókot, mint egy normál, Azure Database for MySQL-kiszolgálóhoz. A kiszolgáló neve **myreplica** a rendszergazda felhasználóneve a **myadmin**, a replika a mysql parancssori felület használatával csatlakozhat:

```bash
mysql -h myreplica.mysql.database.azure.com -u myadmin@myreplica -p
```

Amikor a rendszer kéri adja meg a felhasználói fiók jelszavát.

## <a name="monitor-replication"></a>A figyelő replikáció

Azure Database for MySQL kínál a **replikációs késés másodpercben** metrika az Azure monitorban. Ez a metrika csak a replikák számára érhető el.

Ez a metrika számítja ki a `seconds_behind_master` a MySQL-ben elérhető metrika `SHOW SLAVE STATUS` parancsot.

Állítsa be egy riasztás értesíti, amikor a replikációs késés eléri-e egy érték, amely nem fogadható el, a számítási feladatok számára.

## <a name="stop-replication"></a>Replikáció leállítása

A master és a egy replika közötti replikációt is leállíthatja. Miután replikáció le van állítva, egy fölérendelt kiszolgáló és a egy olvasási replika között, a replika válik egy önálló kiszolgáló. Az önálló kiszolgáló adatai az adatokat, amelyek a stop replikációs parancs indításakor álltak rendelkezésre a replikán. Az önálló kiszolgáló kommunikálnak, nem történik meg.

Ha egy replika replikációleállítási választja, az összes hivatkozás elveszíti az előző fő és további replikák. Nincs a master és a replika között nincs automatikus feladatátvétellel.

> [!IMPORTANT]
> Az önálló kiszolgáló nem hajtható végre egy replika be újra.
> Mielőtt replikációleállítás olvasási replikán, győződjön meg arról, a replika rendelkezik a szükséges adatok.

Ismerje meg, hogyan [állítania a replikálást a replika](howto-read-replicas-portal.md).

## <a name="considerations-and-limitations"></a>Megfontolandó szempontok és korlátozások

### <a name="pricing-tiers"></a>Árképzési szintek

Olvasható replika jelenleg csak az általános célú és memóriahasználatra optimalizált tarifacsomagok érhető el.

### <a name="master-server-restart"></a>Fő kiszolgáló újraindítása

Ha a replika tartozik egy főkiszolgálóhoz, amely nem tartalmaz meglévő másodpéldányokat hoz létre, a fő először újraindul felkészüljön a replikáció. Figyelembe kell venni ezt, és hajtsa végre ezeket a műveleteket egy csúcsidőn kívüli időszakban.

### <a name="new-replicas"></a>Új replikák

Olvasási replika jön létre egy új, Azure Database for MySQL-kiszolgálóhoz. Meglévő kiszolgáló, a replika nem hajtható végre. Nem hozható létre egy másik olvasási replikára replikáját.

### <a name="replica-configuration"></a>Replikát konfigurációt

A kiszolgáló ugyanazt a konfigurációt a master létrehoztak egy replikát. Replika létrehozása után több beállítások egymástól függetlenül lehet módosítani a fő kiszolgálóról: a számítási generáció, virtuális magok, tárolási, biztonsági másolat megőrzési idejének és MySQL-motor verziója. A tarifacsomag egymástól függetlenül is módosítható, vagy az alapszintű csomag kivételével.

> [!IMPORTANT]
> Egy fölérendelt kiszolgáló konfigurációs frissül az új értékekre, mielőtt frissíteni a replikát konfigurációt egyenlő vagy nagyobb értékre. Ez a művelet biztosítja, hogy a replika továbbra is a fő végzett módosítások.

### <a name="stopped-replicas"></a>Leállított replikák

Ha megszakítja egy fölérendelt kiszolgáló és a egy olvasási replika közötti replikációt, a leállított replika válik egy önálló kiszolgáló, amely fogadja az olvasásokat és az írásokat. Az önálló kiszolgáló nem hajtható végre egy replika be újra.

### <a name="deleted-master-and-standalone-servers"></a>Törölt fő- és a különálló kiszolgálók

A törölt egy fölérendelt kiszolgáló replikációja leáll minden olvasási replikákra. Ezek a replikák önálló kiszolgálók válnak. A fölérendelt kiszolgáló maga törlődik.

### <a name="user-accounts"></a>Felhasználói fiókok

A fölérendelt kiszolgáló a felhasználók a olvasható replikák replikálódnak. Csak olvasási replika érhető el a felhasználói fiókok használata a fő kiszolgálón csatlakozni.

### <a name="server-parameters"></a>Kiszolgálói paraméterek

Megakadályozza az adatok szinkronizálva váljon, és esetleges adatvesztés vagy -sérülés elkerülése érdekében, néhány kiszolgálóparaméterekkel frissüljön, amikor használatával olvassa el a replikákat zárolva vannak.

A következő kiszolgáló paramétereinek a fő-és a replika zárolva vannak:
- [`innodb_file_per_table`](https://dev.mysql.com/doc/refman/5.7/en/innodb-multiple-tablespaces.html) 
- [`log_bin_trust_function_creators`](https://dev.mysql.com/doc/refman/5.7/en/replication-options-binary-log.html#sysvar_log_bin_trust_function_creators)

A [ `event_scheduler` ](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_event_scheduler) paraméter a replikakiszolgálókon zárolva van. 

### <a name="other"></a>Egyéb

- Globális tranzakciók azonosítók (GTID) használata nem támogatott.
- A replika egy replika létrehozása nem támogatott.
- Előfordulhat, hogy a táblák replikák szinkronizálva lesz. Ez az egyik korlátozása a MySQL replikációs technológiával. További információ: a [MySQL dokumentációja](https://dev.mysql.com/doc/refman/5.7/en/replication-features-memory.html) további információt.
- Győződjön meg arról, a fölérendelt kiszolgáló táblák elsődleges kulcsot tartalmaznak. Elsődleges kulcsok hiánya a master és a replikák közötti replikációs késés eredményezhet.
- Tekintse át a MySQL replikációs korlátozások a teljes listáját a [MySQL dokumentációja](https://dev.mysql.com/doc/refman/5.7/en/replication-features.html)

## <a name="next-steps"></a>További lépések

- Ismerje meg, hogyan [létrehozása és kezelése az Azure portal használatával olvasható replikák](howto-read-replicas-portal.md)
- Ismerje meg, hogyan [létrehozása és kezelése az Azure CLI használatával olvasható replikák](howto-read-replicas-cli.md)
