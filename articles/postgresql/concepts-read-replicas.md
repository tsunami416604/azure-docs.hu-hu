---
title: Olvasási replikák az Azure Database for PostgreSQL-ben
description: Ez a cikk ismerteti a olvasható replika szolgáltatást az Azure Database for postgresql-hez.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 04/01/2019
ms.openlocfilehash: f340f1e42b6993a1f834ab05570c669d4241222b
ms.sourcegitcommit: 04716e13cc2ab69da57d61819da6cd5508f8c422
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2019
ms.locfileid: "58847180"
---
# <a name="read-replicas-in-azure-database-for-postgresql"></a>Olvasási replikák az Azure Database for PostgreSQL-ben

Az olvasási replika szolgáltatás replikálja az adatokat egy Azure Database for PostgreSQL-kiszolgálót egy írásvédett kiszolgáló teszi lehetővé. Ugyanazon Azure-régióban legfeljebb öt replikához replikálhatja a fölérendelt kiszolgálótól. Replika aszinkron módon frissülnek a PostgreSQL-adatbázismotor natív replikációs technológiával.

Replika, hogy a normál hasonló, Azure Database for PostgreSQL-kiszolgálók kezelése új kiszolgálók. Az egyes olvassa el a replika, a számlázás a virtuális magok kiépített számítási és tárolási GB / hó.

Ismerje meg, hogyan [létrehozása és kezelése a replikák](howto-read-replicas-portal.md).

## <a name="when-to-use-a-read-replica"></a>Ha egy olvasási-replika használatára
A olvasható replika szolgáltatás segítségével a teljesítményét és skálázhatóságát olvasásigényű munkaterhelésekhez javítása érdekében. Olvasási számítási feladatok is lehet a replikákra, elkülönített, miközben írási számítási feladatok a fő lehet irányítani.

Gyakran előfordul, hogy rendelkezzen a BI-ban, és elemzési számítási feladatok az olvasási-replika használatára adatforrásként a jelentéskészítéshez.

Replikák írásvédettek, mivel azok nem csökkenti a fő írási kapacitású terheinek közvetlenül. Ez a funkció nem célzó írási-igényes számítási feladatokhoz.

Az olvasási replika szolgáltatás PostgreSQL aszinkron replikációt használ. A szolgáltatás nem arra készült, a szinkron replikációs forgatókönyvek esetén. A master és a replika mérhető késleltetés lesz. A replikában tárolt idővel a fő lévő adatok konzisztens lesz. A funkció használatához a számítási feladatokhoz, amely képes kezelni ezt a késést.

## <a name="create-a-replica"></a>Replika létrehozása
Rendelkeznie kell a fölérendelt kiszolgáló a `azure.replication_support` paraméter beállítása **REPLIKA**. Amikor ez a paraméter módosul, a kiszolgáló újraindítására szükség a módosítás érvénybe léptetéséhez. (A `azure.replication_support` paraméter csak az általános célú és memóriahasználatra optimalizált csomagok vonatkozik).

Amikor a létrehozás replika munkafolyamat, egy üres, Azure Database for PostgreSQL-kiszolgáló jön létre. Az új kiszolgáló ki van töltve az adatokkal, amelyek a fő kiszolgálón volt. A létrehozás ideje függ a master és a múlt heti teljes biztonsági mentés óta eltelt idő adatok mennyisége. Az idő pár percet vagy akár több óráig terjedhet.

Az olvasási replika szolgáltatás PostgreSQL fizikai replikációt, nem logikai replikációt használ. Az alapértelmezett működési mód replikációs adatfolyam-replikáció tárhelyek használatával. Ha szükséges, a naplóküldés olvasásra szolgál.

> [!NOTE]
> A tárolási riasztási csoport nem rendelkezik a kiszolgálókon, azt javasoljuk, hogy lehetősége. A riasztás figyelmeztet, ha a kiszolgáló már majdnem elérte a tárolási kapacitását, amely hatással van a replikáció.

Ismerje meg, hogyan [olvasható replika létrehozásához az Azure Portalon](howto-read-replicas-portal.md).

## <a name="connect-to-a-replica"></a>Csatlakozás egy replika
Amikor létrehoz egy replikát, a tűzfal- és VNet-szolgáltatásvégpont főkiszolgálójának nem örökli. Ezek a szabályok egymástól függetlenül a replika kell állítani.

A replika örökli a fölérendelt kiszolgáló a rendszergazdai fiókkal. A fölérendelt kiszolgáló az összes felhasználói fiók replikálva vannak a olvasható replikákat. Csak csatlakozhat egy olvasási replikát a fölérendelt kiszolgáló elérhető a felhasználói fiókok használatával.

Csatlakozhat a replika használatával, az állomásnév és a egy érvényes felhasználói fiókot, mint egy normál, Azure Database for PostgreSQL-kiszolgálóhoz. A kiszolgáló neve **myreplica** a rendszergazda felhasználóneve a **myadmin**, a replika psql használatával csatlakozhat:

```
psql -h myreplica.postgres.database.azure.com -U myadmin@myreplica -d postgres
```

Amikor a rendszer kéri adja meg a felhasználói fiók jelszavát.

## <a name="monitor-replication"></a>A figyelő replikáció
Azure Database for PostgreSQL kínál a **Lag között replikák maximális száma** metrika az Azure monitorban. Ez a metrika csak a fő kiszolgálón érhető el. A metrika a késés a master és a legtöbb elmaradt replika közötti jeleníti meg. 

Azure Database for postgresql-hez is biztosít a **replika Lag** metrika az Azure monitorban. Ez a metrika csak a replikák számára érhető el. 

A mérték kiszámítása a `pg_stat_wal_receiver` megtekintése:

```SQL
EXTRACT (EPOCH FROM now() - pg_last_xact_replay_timestamp())
```

A replika Lag mérőszám látható az utolsó megismételt tranzakció óta eltelt idő. Nem léteznek tranzakciók a fő kiszolgálón jelentkezik, ha a metrika az idő elteltével jeleníti meg.

Állítsa be egy riasztás értesíti, amikor a replika lag eléri-e egy érték, amely nem fogadható el, a számítási feladatok számára. 

További elemzés, a lekérdezés a fölérendelt kiszolgáló közvetlenül lekérdezni a replikációs késés (bájt) minden replikán.

A PostgreSQL 10-es verzió:

```SQL
select pg_wal_lsn_diff(pg_current_wal_lsn(), stat.replay_lsn) 
AS total_log_delay_in_bytes from pg_stat_replication;
```

A PostgreSQL 9.6 és korábbi verzióiban:

```SQL
select pg_xlog_location_diff(pg_current_xlog_location(), stat.replay_location) 
AS total_log_delay_in_bytes from pg_stat_replication;
```

> [!NOTE]
> Ha egy fölérendelt kiszolgáló vagy olvasható replika újraindult, indítsa újra, és nézze át a szükséges időt a replika Lag metrika tükrözi.

## <a name="stop-replication"></a>Replikáció leállítása
A master és a egy replika közötti replikációt is leállíthatja. A leállítási művelet hatására a replikát, indítsa újra, és távolítsa el a replikációs beállításokat. Miután replikáció le van állítva, egy fölérendelt kiszolgáló és a egy olvasási replika között, a replika válik egy önálló kiszolgáló. Az önálló kiszolgáló adatai az adatokat, amelyek a stop replikációs parancs indításakor álltak rendelkezésre a replikán. Az önálló kiszolgáló kommunikálnak, nem történik meg.

> [!IMPORTANT]
> Az önálló kiszolgáló nem hajtható végre egy replika be újra.
> Mielőtt replikációleállítás olvasási replikán, győződjön meg arról, a replika rendelkezik a szükséges adatok.

Ismerje meg, hogyan [állítania a replikálást a replika](howto-read-replicas-portal.md).


## <a name="considerations"></a>Megfontolandó szempontok

Ebben a szakaszban olvasható replika szolgáltatásával kapcsolatos szempontokat foglalja össze.

### <a name="prerequisites"></a>Előfeltételek
Egy olvasási replika létrehozása előtt a `azure.replication_support` paramétert állítsa **REPLIKA** a fölérendelt kiszolgálón. Amikor ez a paraméter módosul, a kiszolgáló újraindítására szükség a módosítás érvénybe léptetéséhez. A `azure.replication_support` paraméter csak az általános célú és memóriahasználatra optimalizált csomagok vonatkozik.

### <a name="new-replicas"></a>Új replikák
Olvasási replika jön létre egy új, Azure Database for PostgreSQL-kiszolgálóhoz. Meglévő kiszolgáló, a replika nem hajtható végre. Olvasási replika csak ugyanabban a régióban az Azure fő hozható létre. Nem hozható létre egy másik olvasási replikára replikáját.

### <a name="replica-configuration"></a>Replikát konfigurációt
A kiszolgáló ugyanazt a konfigurációt a master létrehoztak egy replikát. Replika létrehozása után több beállítások egymástól függetlenül lehet módosítani a fő kiszolgálóról: a számítási generáció, virtuális magok, tárolási és biztonsági másolat megőrzési idejének. A tarifacsomag egymástól függetlenül is módosítható, vagy az alapszintű csomag kivételével.

> [!IMPORTANT]
> Egy fölérendelt kiszolgáló konfigurációs frissül az új értékekre, mielőtt frissíteni a replikát konfigurációt egyenlő vagy nagyobb értékre. Ez a művelet biztosítja, hogy a replika továbbra is a fő végzett módosítások.

PostgreSQL szükséges értéke a `max_connections` paramétert nagyobb vagy egyenlő a fő értékkel; egyéb kell olvasási replikán, a replika nem indul el. Az Azure Database for postgresql-hez a `max_connections` SKU paraméter értéke alapján. További információkért lásd: [korlátozások az Azure Database for postgresql-hez](concepts-limits.md). 

Próbálja meg frissíteni a kiszolgáló értékeket, de nem igazodik a határértékekhez, ha hibaüzenetet kap.

### <a name="stopped-replicas"></a>Leállított replikák
Ha megszakítja egy fölérendelt kiszolgáló és a egy olvasási replika közötti replikációt, a replika újraindítja a módosítás alkalmazására. A leállított replika egy önálló kiszolgáló, amely fogadja az olvasásokat és az írásokat válik. Az önálló kiszolgáló nem hajtható végre egy replika be újra.

### <a name="deleted-master-and-standalone-servers"></a>Törölt fő- és a különálló kiszolgálók
A törölt egy fölérendelt kiszolgáló az olvasási replikák mindegyike önálló kiszolgálók válnak. A replikák újra lesz indítva, ez a változás tükrözése érdekében.

## <a name="next-steps"></a>További lépések
Ismerje meg, hogyan [létrehozása és kezelése az Azure Portalon olvasható replikák](howto-read-replicas-portal.md).
