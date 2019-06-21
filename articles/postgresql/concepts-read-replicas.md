---
title: Azure Database for PostgreSQL - kiszolgáló egyetlen olvasható replika
description: Ez a cikk ismerteti a olvasható replika szolgáltatást az Azure Database for PostgreSQL – egyetlen kiszolgáló.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 06/14/2019
ms.openlocfilehash: c98247b0ba8b670a59dec9aa3ec87e949f1dda78
ms.sourcegitcommit: 72f1d1210980d2f75e490f879521bc73d76a17e1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/14/2019
ms.locfileid: "67147926"
---
# <a name="read-replicas-in-azure-database-for-postgresql---single-server"></a>Azure Database for PostgreSQL - kiszolgáló egyetlen olvasható replika

Az olvasási replika szolgáltatás replikálja az adatokat egy Azure Database for PostgreSQL-kiszolgálót egy írásvédett kiszolgáló teszi lehetővé. Legfeljebb öt replikához replikálhatja a fölérendelt kiszolgálótól. Replika aszinkron módon frissülnek a PostgreSQL-adatbázismotor natív replikációs technológiával.

> [!IMPORTANT]
> Olvasási replikát hozhat létre, mint a fölérendelt kiszolgáló ugyanabban a régióban, vagy bármely más Azure-régióban a választott. Régiók közötti replikáció jelenleg nyilvános előzetes verzióban érhető el.

Replika, hogy a normál hasonló, Azure Database for PostgreSQL-kiszolgálók kezelése új kiszolgálók. Az egyes olvassa el a replika, a számlázás a virtuális magok kiépített számítási és tárolási GB / hó.

Ismerje meg, hogyan [létrehozása és kezelése a replikák](howto-read-replicas-portal.md).

## <a name="when-to-use-a-read-replica"></a>Ha egy olvasási-replika használatára
A olvasható replika szolgáltatás segítségével a teljesítményét és skálázhatóságát olvasásigényű munkaterhelésekhez javítása érdekében. Olvasási számítási feladatok is lehet a replikákra, elkülönített, miközben írási számítási feladatok a fő lehet irányítani.

Gyakran előfordul, hogy rendelkezzen a BI-ban, és elemzési számítási feladatok az olvasási-replika használatára adatforrásként a jelentéskészítéshez.

Replikák írásvédettek, mivel azok nem csökkenti a fő írási kapacitású terheinek közvetlenül. Ez a funkció nem célzó írási-igényes számítási feladatokhoz.

Az olvasási replika szolgáltatás PostgreSQL aszinkron replikációt használ. A szolgáltatás nem arra készült, a szinkron replikációs forgatókönyvek esetén. A master és a replika mérhető késleltetés lesz. A replikában tárolt idővel a fő lévő adatok konzisztens lesz. A funkció használatához a számítási feladatokhoz, amely képes kezelni ezt a késést.

Olvasható replikák javíthatja a vészhelyreállítási tervet. Először ki kell rendelkeznie egy replikát egy másik Azure-régióban a főágból. Régió vészhelyzet esetén replikációleállítás erre a replikára, és a számítási feladatok átirányítása. Replikáció leállítása lehetővé teszi, hogy a replika a kezdéshez fogadja az írási műveletek, valamint beolvassa. További információ: a [replikációleállítás](#stop-replication) szakaszban. 

## <a name="create-a-replica"></a>Replika létrehozása
Rendelkeznie kell a fölérendelt kiszolgáló a `azure.replication_support` paraméter beállítása **REPLIKA**. Amikor ez a paraméter módosul, a kiszolgáló újraindítására szükség a módosítás érvénybe léptetéséhez. (A `azure.replication_support` paraméter csak az általános célú és memóriahasználatra optimalizált csomagok vonatkozik).

Amikor a létrehozás replika munkafolyamat, egy üres, Azure Database for PostgreSQL-kiszolgáló jön létre. Az új kiszolgáló ki van töltve az adatokkal, amelyek a fő kiszolgálón volt. A létrehozás ideje függ a master és a múlt heti teljes biztonsági mentés óta eltelt idő adatok mennyisége. Az idő pár percet vagy akár több óráig terjedhet.

Minden replika engedélyezve van a storage [automatikus növekedést](concepts-pricing-tiers.md#storage-auto-grow). A auto-grow funkció lehetővé teszi, hogy naprakész maradhat, replikálja az adatokat, és megakadályozza a tartományon kívül a tárolási hibák által okozott replikációban szünet a replikát.

Az olvasási replika szolgáltatás PostgreSQL fizikai replikációt, nem logikai replikációt használ. Az alapértelmezett működési mód replikációs adatfolyam-replikáció tárhelyek használatával. Ha szükséges, a naplóküldés olvasásra szolgál.

Ismerje meg, hogyan [olvasható replika létrehozásához az Azure Portalon](howto-read-replicas-portal.md).

## <a name="connect-to-a-replica"></a>Csatlakozás egy replika
Amikor létrehoz egy replikát, a tűzfal- és VNet-szolgáltatásvégpont főkiszolgálójának nem örökli. Ezek a szabályok egymástól függetlenül a replika kell állítani.

A replika örökli a fölérendelt kiszolgáló a rendszergazdai fiókkal. A fölérendelt kiszolgáló az összes felhasználói fiók replikálva vannak a olvasható replikákat. Csak csatlakozhat egy olvasási replikát a fölérendelt kiszolgáló elérhető a felhasználói fiókok használatával.

Csatlakozhat a replika használatával, az állomásnév és a egy érvényes felhasználói fiókot, mint egy normál, Azure Database for PostgreSQL-kiszolgálóhoz. A kiszolgáló neve **saját replika** a rendszergazda felhasználóneve a **myadmin**, a replika psql használatával csatlakozhat:

```
psql -h myreplica.postgres.database.azure.com -U myadmin@myreplica -d postgres
```

Amikor a rendszer kéri adja meg a felhasználói fiók jelszavát.

## <a name="monitor-replication"></a>A figyelő replikáció
Azure Database for postgresql-hez két mérőszámait replikálásának figyelése. A két mérőszám játszik **Lag között replikák maximális száma** és **replika Lag**. Ezek a metrikák megtekintése kapcsolatban lásd: a **replika figyelése** szakaszában a [a cikk a replika útmutató](howto-read-replicas-portal.md).

A **Lag között replikák maximális száma** metrika a master és a legtöbb elmaradt replika közötti jeleníti meg a késés. Ez a metrika csak a fő kiszolgálón érhető el.

A **replika Lag** metrika időpontot jeleníti meg, mert az utolsó játssza vissza a tranzakciót. Nem léteznek tranzakciók a fő kiszolgálón jelentkezik, ha a metrika az idő elteltével jeleníti meg. Ez a metrika csak a replikakiszolgálót érhető el. Replika Lag kiszámítása a `pg_stat_wal_receiver` megtekintése:

```SQL
EXTRACT (EPOCH FROM now() - pg_last_xact_replay_timestamp());
```

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

Replikáció leállítása, ha a replika összes hivatkozás az előző fő és további replikák megszűnik. Nincs a master és a replika között nincs automatikus feladatátvétellel. 

Ismerje meg, hogyan [állítania a replikálást a replika](howto-read-replicas-portal.md).


## <a name="considerations"></a>Megfontolandó szempontok

Ebben a szakaszban olvasható replika szolgáltatásával kapcsolatos szempontokat foglalja össze.

### <a name="prerequisites"></a>Előfeltételek
Egy olvasási replika létrehozása előtt a `azure.replication_support` paramétert állítsa **REPLIKA** a fölérendelt kiszolgálón. Amikor ez a paraméter módosul, a kiszolgáló újraindítására szükség a módosítás érvénybe léptetéséhez. A `azure.replication_support` paraméter csak az általános célú és memóriahasználatra optimalizált csomagok vonatkozik.

### <a name="new-replicas"></a>Új replikák
Olvasási replika jön létre egy új, Azure Database for PostgreSQL-kiszolgálóhoz. Meglévő kiszolgáló, a replika nem hajtható végre. Nem hozható létre egy másik olvasási replikára replikáját.

### <a name="replica-configuration"></a>Replikát konfigurációt
A kiszolgáló ugyanazt a konfigurációt a master létrehoztak egy replikát. Replika létrehozása után több beállítások egymástól függetlenül lehet módosítani a fő kiszolgálóról: a számítási generáció, virtuális magok, tárolási és biztonsági másolat megőrzési idejének. A tarifacsomag egymástól függetlenül is módosítható, vagy az alapszintű csomag kivételével.

> [!IMPORTANT]
> Egy fölérendelt kiszolgáló konfigurációs frissül az új értékekre, mielőtt frissíteni a replikát konfigurációt egyenlő vagy nagyobb értékre. Ez a művelet biztosítja, hogy a replika továbbra is a fő végzett módosítások.

PostgreSQL szükséges értéke a `max_connections` paramétert nagyobb vagy egyenlő a fő értékkel; egyéb kell olvasási replikán, a replika nem indul el. Az Azure Database for postgresql-hez a `max_connections` SKU paraméter értéke alapján. További információkért lásd: [korlátozások az Azure Database for postgresql-hez](concepts-limits.md). 

Próbálja meg frissíteni a kiszolgáló értékeket, de nem igazodik a határértékekhez, ha hibaüzenetet kap.

### <a name="maxpreparedtransactions"></a>max_prepared_transactions
[PostgreSQL igényel](https://www.postgresql.org/docs/10/runtime-config-resource.html#GUC-MAX-PREPARED-TRANSACTIONS) értékét a `max_prepared_transactions` paramétert nagyobb vagy egyenlő a fő értékkel; egyéb kell olvasási replikán, a replika nem indul el. Ha meg szeretné változtatni `max_prepared_transactions` főkiszolgálón, először módosítsa azt a replikákon.

### <a name="stopped-replicas"></a>Leállított replikák
Ha megszakítja egy fölérendelt kiszolgáló és a egy olvasási replika közötti replikációt, a replika újraindítja a módosítás alkalmazására. A leállított replika egy önálló kiszolgáló, amely fogadja az olvasásokat és az írásokat válik. Az önálló kiszolgáló nem hajtható végre egy replika be újra.

### <a name="deleted-master-and-standalone-servers"></a>Törölt fő- és a különálló kiszolgálók
A törölt egy fölérendelt kiszolgáló az olvasási replikák mindegyike önálló kiszolgálók válnak. A replikák újra lesz indítva, ez a változás tükrözése érdekében.

## <a name="next-steps"></a>További lépések
Ismerje meg, hogyan [létrehozása és kezelése az Azure Portalon olvasható replikák](howto-read-replicas-portal.md).
