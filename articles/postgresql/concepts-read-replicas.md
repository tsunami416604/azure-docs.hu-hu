---
title: Olvasási replikák az Azure Database for PostgreSQL-ben
description: Ez a cikk ismerteti a olvasható replikák az Azure Database for postgresql-hez.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/22/2019
ms.openlocfilehash: cb02f0b786ff6f1c7dbef5471fb95ce6516f824c
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2019
ms.locfileid: "54466074"
---
# <a name="read-replicas-in-azure-database-for-postgresql"></a>Azure Database for PostgreSQL olvasható replika
Az olvasási replika funkció teszi, hogy az adatok replikálása az Azure Database for PostgreSQL-kiszolgáló (master) legfeljebb öt csak olvasható kiszolgálók (olvassa el a replikákat) azonos Azure-régióban. Olvasható replika aszinkron módon frissíti a PostgreSQL-motor natív replikációs technológiával.

Replikák olyan új, normál önálló Azure Database for PostgreSQL-kiszolgálók hasonló módon kezelhetők. Minden egyes olvasható replika számlázzuk ki a virtuális magok kiépített számítási és a felhasznált tárterület GB/hó.

## <a name="when-to-use-read-replicas"></a>Mikor érdemes használni az olvasási replikák
Az olvasási replika funkció célja javítsák a teljesítményét és skálázhatóságát olvasásigényű munkaterhelésekhez. Például az olvasási számítási feladatok lehet a replikákra, elkülönített közben a master írási számítási feladatok lehet irányítani.

Gyakran előfordul, hogy rendelkezzen a BI-ban, és elemzési számítási feladatok az olvasási-replika használatára adatforrásként a jelentéskészítéshez.

Mivel a replika csak olvasható, nem elmondja, közvetlenül a fő írási kapacitású terheket, és ezért ez a funkció nem irányuló írási-igényes számítási feladatokhoz.

A olvasható replika szolgáltatás PostgreSQL aszinkron replikációt használ, és ezért nem célja a szinkron replikációs forgatókönyvek esetén. A master és a replika mérhető késleltetés lesz. A replikában tárolt idővel konzisztenssé váljanak az adatok a főkiszolgálón lesz. A funkció használatához a számítási feladatokhoz, amely képes kezelni ezt a késést.

## <a name="creating-a-replica"></a>Replika létrehozása
Rendelkeznie kell a fölérendelt kiszolgáló a **azure.replication_support** REPLIKA beállítása. Ez a paraméter módosítása érvénybe léptetéséhez kiszolgáló újraindítását igényli. (**Azure.replication_support** paraméter csak az általános célú és memóriahasználatra optimalizált csomagokra vonatkozik).

A létrehozás replika munkafolyamat kezdeményezésekor jön létre egy üres, Azure Database for PostgreSQL-kiszolgálóhoz. Az új kiszolgáló ki van töltve az adatokkal, amelyek a fő kiszolgálón volt. Az új replika létrehozásához szükséges idő attól függ, hogy az adatok mennyisége a master és a múlt heti teljes biztonsági mentés óta eltelt idő. Ez néhány percet és közötti néhány óra alatt.

Az olvasási replika szolgáltatás a PostgreSQL fizikai replikációt (nem logikai replikáció) használ. Az alapértelmezett működési mód replikációt a replikációs tárolóhelyek segítségével streamelési. Ha szükséges, naplóküldés sugárzott szolgál.

> [!NOTE]
> Ha nem már rendelkezik a tárolási riasztási csoport a kiszolgálókon, ezért javasoljuk, hogy, így tájékoztatja, ha a kiszolgáló már majdnem elérte a tárolási kapacitását, mivel ez hatással lesz a replikálás.

[Ismerje meg, hogyan olvasható replika létrehozásához az Azure Portalon](howto-read-replicas-portal.md).

## <a name="connecting-to-a-replica"></a>Kapcsolódás a replika
Amikor létrehoz egy replikát, a tűzfal- és VNet-szolgáltatásvégpont főkiszolgálójának nem örököl. Ezek a szabályok egymástól függetlenül a replika kell állítani.

A replika örökli a fölérendelt kiszolgáló a rendszergazdai fiók. A fölérendelt kiszolgáló az összes felhasználói fiók replikálva vannak a olvasható replikákat. Csak olvasási replika érhető el a felhasználói fiókok használata a fő kiszolgálón csatlakozni.

Az állomásnév és a egy érvényes felhasználói fiókot használ a replika csatlakozhat, mint egy normál, Azure Database for PostgreSQL-kiszolgálóhoz. Például ha a kiszolgáló neve, myreplica, és a rendszergazdai felhasználónevet myadmin csatlakozhat hozzá psql módon:

```
psql -h myreplica.postgres.database.azure.com -U myadmin@myreplica -d postgres
```
és a parancssorba írja be a jelszót a felhasználói fiókhoz.

## <a name="monitoring-replication"></a>Replikáció figyelése
Van egy **replikák közötti maximális késésnek** elérhető az Azure monitorban metrika. Ez a metrika csak a fő kiszolgálón érhető el. A metrika a master és a legtöbb elmaradt replika közötti késés időpontot jeleníti meg. 

Is biztosítunk egy **replika Lag** metrika az Azure monitorban. Ez a metrika csak a replikák számára érhető el. 

A metrika a pg_stat_wal_receiver nézetből számítható ki:

```SQL
EXTRACT (EPOCH FROM now() - pg_last_xact_replay_timestamp())
```
Ne feledje, hogy a replika Lag metrika időt mutatja, mivel az utolsó játssza vissza a tranzakciót. Ha nem léteznek tranzakciók folyamatban lévő a master, a metrika az idő elteltével jeleníti meg.

Azt javasoljuk, hogy beállította-e riasztást, amely figyelmeztet, ha a replika lag elér egy érték, amely nem fogadható el, a számítási feladatok számára. 

További elemzés, a lekérdezés a fölérendelt kiszolgáló közvetlenül lekérdezni a replikációs késés (bájt) minden replikán.
A PG 10:
```SQL
select pg_wal_lsn_diff(pg_current_wal_lsn(), stat.replay_lsn) 
AS total_log_delay_in_bytes from pg_stat_replication;
```

A PG 9.6 és alatt:
```SQL
select pg_xlog_location_diff(pg_current_xlog_location(), stat.replay_location) 
AS total_log_delay_in_bytes from pg_stat_replication;
```

> [!NOTE]
> A master és a replika kiszolgáló újraindítása, ha a szükséges időt, és indítsa újra a majd nézze át a replika Lag metrika fog szerepelni.

## <a name="stopping-replication-to-a-replica"></a>A replika-replikáció leállítása
Ha szeretné, állítsa le a master és a egy replika közötti replikációt. Ennek hatására a replikációs beállítások eltávolításához indítsa újra a replikát. A master és a replikakiszolgáló közötti replikáció le lett állítva, a replikakiszolgáló egy önálló kiszolgáló lesz. Az önálló kiszolgáló adatai az adatokat, amely az időpontra a replikációs leállítási parancsot kezdeményezett a replikán elérhető volt. Az önálló kiszolgáló nem dolgozza a főkiszolgálóval.

Ez a kiszolgáló nem hajtható végre egy replika be újra.

Győződjön meg arról, hogy a replika rendelkezik-e a replikáció leállítása előtt szükséges összes adatot.

Is [megtudhatja, hogyan állítsa le az Útmutató dokumentáció replika](howto-read-replicas-portal.md).


## <a name="considerations"></a>Megfontolandó szempontok

### <a name="preparing-for-replica"></a>Replika előkészítése
**Azure.replication_support** be kell állítani a REPLIKA a fölérendelt kiszolgáló replika létrehozása előtt. Ez a paraméter módosítása érvénybe léptetéséhez kiszolgáló újraindítását igényli. Ez a paraméter csak az általános célú és memóriahasználatra optimalizált csomagokra vonatkozik.

### <a name="stopped-replicas"></a>Leállított replikák
Állítsa le a master és a replika közötti replikációt választja, a replika újraindul a alkalmazni ezeket a módosításokat. Ezt követően azt nem hajtható végre egy replika be újra.

### <a name="replicas-are-new-servers"></a>Replika új kiszolgálók
Replikák jönnek létre, új, Azure Database for PostgreSQL-kiszolgálót. Meglévő kiszolgálók nem hajtható végre, a replikákat.

### <a name="replica-server-configuration"></a>Adatbázisreplika-kiszolgáló konfigurációja
Replikakiszolgáló használatával server ugyanezzel a konfigurációval a master, amely tartalmazza a következő konfigurációk jön létre:
- Tarifacsomag
- Számítási generáció
- Virtuális magok
- Storage
- Biztonsági másolat megőrzési idejének
- Biztonsági mentési adatredundáns tárolási mód
- PostgreSQL engine version

Replika létrehozása után, a tarifacsomag (kivéve a, illetve onnan alapszintű), számítási generáció, a virtuális magok, a storage és a biztonsági másolat megőrzési idejének módosítható egymástól függetlenül a fölérendelt kiszolgálótól.

> [!IMPORTANT]
> Egy master kiszolgálókonfiguráció frissül az új értékekre, mielőtt a replikák konfigurációs egyenlő vagy nagyobb értékre kell frissíteni. Ez biztosítja, hogy a replikák meg tudják tartani a fő végzett módosításokat.

Különösen Postgres igényel a replika kiszolgáló értéke az a paraméter max_connections kell nagyobb vagy egyenlő a fő értékkel, ellenkező esetben a replika nem indul el. Az Azure Database for postgresql-hez max_connections értéke a kiválasztott sku függvényében. További információkért olvassa el [a korlátok doc](concepts-limits.md). 

Hiba történt kísérlet, amely megsérti a frissítés végrehajtásához vezet.


### <a name="deleting-the-master"></a>A fő törlése
A törölt egy fölérendelt kiszolgáló összes olvasható replika válnak önálló kiszolgálón. A replikák újra kell indítani a változás tükrözése érdekében.

### <a name="other"></a>Egyéb
- Olvasható replikák csak ugyanabban a régióban az Azure fő hozható létre.
- A replika egy replika létrehozása nem támogatott.

## <a name="next-steps"></a>További lépések
- [Hogyan hozhat létre és kezelhet, olvassa el az Azure Portalon replikák](howto-read-replicas-portal.md).
