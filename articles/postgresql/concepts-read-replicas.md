---
title: Replikák olvasása Azure Database for PostgreSQL – egyetlen kiszolgáló
description: Ez a cikk a Azure Database for PostgreSQL-Single Server olvasási replika funkcióját ismerteti.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 08/12/2019
ms.openlocfilehash: 928a85c9d03148198fe3e965636740812ce732f7
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/13/2019
ms.locfileid: "68976289"
---
# <a name="read-replicas-in-azure-database-for-postgresql---single-server"></a>Replikák olvasása Azure Database for PostgreSQL – egyetlen kiszolgáló

Az olvasási replika funkció lehetővé teszi az adatok replikálását egy Azure Database for PostgreSQL-kiszolgálóról egy írásvédett kiszolgálóra. A főkiszolgálóról legfeljebb öt replikára másolhatja az adatokat. A replikák aszinkron módon frissülnek a PostgreSQL motor natív replikációs technológiájának használatával.

A replikák olyan új kiszolgálók, amelyeket a rendszeres Azure Database for PostgreSQL-kiszolgálókhoz hasonló módon kezel. Az egyes olvasási replikák esetében a virtuális mag és a Storage szolgáltatásban a kiépített számítási kapacitást a GB/hó értékben számítjuk fel.

Megtudhatja, hogyan [hozhat létre és kezelhet replikákat](howto-read-replicas-portal.md).

## <a name="when-to-use-a-read-replica"></a>Mikor használjon olvasási replikát
Az olvasási replika funkcióval javítható a nagy olvasási igényű számítási feladatok teljesítménye és méretezése. Az olvasási munkaterhelések elkülöníthetők a replikákkal, míg az írási munkaterhelések a főkiszolgálóhoz irányíthatók.

Gyakori forgatókönyv, hogy a BI-és analitikai munkaterhelések az olvasási replikát használják adatforrásként a jelentéskészítéshez.

Mivel a replikák csak olvashatók, nem csökkentik közvetlenül az írási kapacitás terheit a főkiszolgálón. Ez a funkció nem a nagy írási igényű munkaterhelésekre irányul.

Az olvasási replika funkció PostgreSQL aszinkron replikálást használ. A funkció nem a szinkron replikációs forgatókönyvek esetében jelent meg. A főkiszolgáló és a replika között mérhető késés lesz. A replikán lévő adatok végül konzisztensek maradnak a főkiszolgálón lévő adatokkal. Használja ezt a szolgáltatást olyan számítási feladatokhoz, amelyek alkalmasak erre a késésre.

## <a name="cross-region-replication"></a>Régiók közötti replikáció
Az olvasási replikát a főkiszolgálótól eltérő régióban is létrehozhatja. A régiók közötti replikáció hasznos lehet olyan forgatókönyvek esetén, mint például a vész-helyreállítási tervezés vagy az adatok közelebb hozása a felhasználókhoz.

> [!IMPORTANT]
> A régiók közötti replikáció jelenleg nyilvános előzetes verzióban érhető el.

A főkiszolgáló bármely [Azure Database for PostgreSQL régióban](https://azure.microsoft.com/global-infrastructure/services/?products=postgresql)elérhető.  A főkiszolgáló rendelkezhet replikával a párosított régiójában vagy az univerzális replika régiókban.

### <a name="universal-replica-regions"></a>Univerzális replika-régiók
A következő régiókban bármikor létrehozhat egy olvasási replikát, függetlenül attól, hogy hol található a főkiszolgáló. Ezek az univerzális replika-régiók:

Kelet-Ausztrália, Délkelet-Ausztrália, USA középső régiója, Kelet-Ázsia, USA keleti régiója, USA 2. keleti régiója, Kelet-Japán, Nyugat-Japán, Dél-Korea, Dél-Korea, Észak-Európa, az USA déli középső régiója, Délkelet-Ázsia, Egyesült Királyság déli régiója, Egyesült Királyság nyugati régiója, Nyugat-Európa, USA nyugati régiója 2.


### <a name="paired-regions"></a>Párosított régiók
Az univerzális replika régión kívül egy olvasási replikát is létrehozhat a főkiszolgáló Azure párosított régiójában. Ha nem ismeri a régió pár elemét, többet is megtudhat az [Azure párosított régiókról](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)szóló cikkből.

Ha régiók közötti replikákat használ a vész-helyreállítási tervezéshez, javasoljuk, hogy a többi régió helyett a párosított régióban hozza létre a replikát. A párosított régiók elkerülik az egyidejű frissítéseket, és rangsorolják a fizikai elkülönítést és az adattárolást.  

Azonban a következő szempontokat kell figyelembe venni: 

* Regionális elérhetőség: Azure Database for PostgreSQL az USA 2. nyugati régiójában, Közép-Franciaországban, Észak-Egyesült Arab Emírségekben és Közép-Németországban érhető el. A párosított régiói azonban nem érhetők el.
    
* UNI-irányú párok: Egyes Azure-régiók csak egy irányban vannak párosítva. Ezek a régiók közé tartoznak a Nyugat-India, Dél-Brazília és US Gov Virginia. 
   Ez azt jelenti, hogy a Nyugat-Indiai főkiszolgáló létrehozhat egy replikát Dél-Indiában. A dél-indiai főkiszolgálók azonban nem hozhatnak létre replikát Nyugat-Indiában. Ennek az az oka, hogy Nyugat-India másodlagos régiója Dél-India, de Dél-India másodlagos régiója nem Nyugat-India.


## <a name="create-a-replica"></a>Replika létrehozása
A főkiszolgálónak a ( `azure.replication_support` z) paraméterrel kell megadnia a **replikát**. Ha módosítja ezt a paramétert, a módosítás érvénybe léptetéséhez újra kell indítani a kiszolgálót. (A `azure.replication_support` paraméter csak a általános célú és a memória optimalizált szintjeire vonatkozik.)

A replika létrehozása munkafolyamat indításakor létrejön egy üres Azure Database for PostgreSQL-kiszolgáló. Az új kiszolgáló a főkiszolgálón található adatokkal van feltöltve. A létrehozási idő a főkiszolgálón tárolt adatok mennyiségétől és az utolsó heti teljes biztonsági mentés óta eltelt idővel függ. Az idő néhány perctől akár több órára is terjedhet.

Minden replika engedélyezve van a tárterület [automatikus növelésére](concepts-pricing-tiers.md#storage-auto-grow). Az automatikus növekedés funkció lehetővé teszi, hogy a replika megőrizze az általa replikált adatmennyiséget, és megakadályozza, hogy a replikálás megszakítása a tárolási hibák miatt meghiúsult.

Az olvasási replika funkció PostgreSQL fizikai replikálást, nem logikai replikálást használ. A replikációs tárolóhelyek szolgáltatással történő folyamatos átvitel az alapértelmezett működési mód. Ha szükséges, a rendszer naplózza a szállítást a felzárkózás érdekében.

Megtudhatja, hogyan [hozhat létre olvasási replikát a Azure Portalban](howto-read-replicas-portal.md).

## <a name="connect-to-a-replica"></a>Kapcsolódás replikához
Replika létrehozásakor nem örökli a főkiszolgáló tűzfalszabályok vagy VNet szolgáltatási végpontját. Ezeket a szabályokat a replika egymástól függetlenül kell beállítani.

A replika örökli a rendszergazdai fiókot a főkiszolgálóról. A főkiszolgáló összes felhasználói fiókja replikálódik az olvasási replikára. Csak olvasási replikához csatlakozhat a főkiszolgálón elérhető felhasználói fiókok használatával.

A replikához a hostname és egy érvényes felhasználói fiók használatával kapcsolódhat, ahogy azt egy normál Azure Database for PostgreSQL-kiszolgálón tenné. A **replika** és a rendszergazdai Felhasználónév **myadmin**nevű kiszolgáló esetén a psql használatával kapcsolódhat a replikához:

```
psql -h myreplica.postgres.database.azure.com -U myadmin@myreplica -d postgres
```

A parancssorba írja be a felhasználói fiókhoz tartozó jelszót.

## <a name="monitor-replication"></a>Replikáció figyelése
Azure Database for PostgreSQL két mérőszámot biztosít a replikáció figyeléséhez. A két metrika a replikák és a **replika késések** **közötti maximális késés** . A metrikák megtekintésével kapcsolatos további információkért tekintse meg a replika figyelése című szakaszt a [replika olvasása című cikkben](howto-read-replicas-portal.md).

A **replikák közötti maximális késés** a fő és a legkésleltetett replika közötti késést mutatja bájtban. Ez a metrika csak a főkiszolgálón érhető el.

A **replika késésének** mérőszáma az utolsó visszajátszott tranzakció óta eltelt időt mutatja. Ha nincs tranzakció a főkiszolgálón, a metrika ezt az időbeli késést tükrözi. Ez a metrika csak replika kiszolgálók esetében érhető el. A replika késését a `pg_stat_wal_receiver` nézetből számítjuk ki:

```SQL
EXTRACT (EPOCH FROM now() - pg_last_xact_replay_timestamp());
```

Állítson be egy riasztást, amely tájékoztatja arról, ha a replika késése olyan értéket ér el, amely nem fogadható el a számítási feladathoz. 

További információk: lekérdezés a főkiszolgálóról közvetlenül a replikálás késésének lekéréséhez az összes replikán.

A PostgreSQL 10-es verziójában:

```SQL
select pg_wal_lsn_diff(pg_current_wal_lsn(), stat.replay_lsn) 
AS total_log_delay_in_bytes from pg_stat_replication;
```

A PostgreSQL 9,6-es és korábbi verzióiban:

```SQL
select pg_xlog_location_diff(pg_current_xlog_location(), stat.replay_location) 
AS total_log_delay_in_bytes from pg_stat_replication;
```

> [!NOTE]
> Ha a főkiszolgáló vagy az olvasási replika újraindul, az újraindításhoz és a betöltéshez szükséges idő a replika késési metrikájában jelenik meg.

## <a name="stop-replication"></a>Replikáció leállítása
Leállíthatja a replikációt egy főkiszolgáló és egy replika között. A Leállítás művelet a replika újraindítását és a replikációs beállítások eltávolítását eredményezi. Miután leállította a replikálást egy főkiszolgáló és egy olvasási replika között, a replika önálló kiszolgáló lesz. Az önálló kiszolgálóban található, a replikálás leállítása parancs elindításának időpontjában a replikán elérhető adatértékek. Az önálló kiszolgáló nem fog felzárkózni a főkiszolgálóval.

> [!IMPORTANT]
> Az önálló kiszolgáló nem hozható létre újra replikába.
> Mielőtt leállítja a replikálást egy olvasási replikán, győződjön meg arról, hogy a replika rendelkezik a szükséges összes adattal.

Ha leállítja a replikálást, a replika elveszíti az előző főkiszolgálóra és más replikára mutató összes hivatkozást. A fő és a replika között nincs automatikus feladatátvétel. 

Megtudhatja, hogyan [állíthatja le a replikálást egy replikára](howto-read-replicas-portal.md).


## <a name="considerations"></a>Megfontolandó szempontok

Ez a szakasz az olvasási replika szolgáltatással kapcsolatos szempontokat foglalja össze.

### <a name="prerequisites"></a>Előfeltételek
Olvasási replika létrehozása előtt a `azure.replication_support` paramétert a főkiszolgálón lévő **replika** értékre kell beállítani. Ha módosítja ezt a paramétert, a módosítás érvénybe léptetéséhez újra kell indítani a kiszolgálót. A `azure.replication_support` paraméter csak a általános célú és a memória optimalizált szintjeire vonatkozik.

### <a name="new-replicas"></a>Új replikák
Az olvasási replika új Azure Database for PostgreSQL-kiszolgálóként jön létre. Egy meglévő kiszolgálót nem lehet replikába készíteni. Egy másik olvasási replika replikája nem hozható létre.

### <a name="replica-configuration"></a>Replika konfigurációja
A replikát ugyanazzal a kiszolgáló-konfigurációval hozza létre a rendszer, mint a főkiszolgálót. A replika létrehozása után több beállítás is módosítható a főkiszolgálótól függetlenül: számítási generáció, virtuális mag, tárterület és biztonsági mentés megőrzési ideje. Az árképzési szint külön is módosítható, kivéve az alapszintű csomagból vagy abból.

> [!IMPORTANT]
> A főkiszolgálói konfiguráció új értékekre frissítése előtt frissítse a replika konfigurációját az értékekkel egyenlő vagy nagyobb értékre. Ez a művelet biztosítja, hogy a replika képes legyen lépést tartani a főkiszolgálón végrehajtott módosításokkal.

A PostgreSQL megköveteli, hogy az `max_connections` olvasási replika paraméterének értéke nagyobb legyen, mint a főérték, ellenkező esetben a replika nem indul el. A Azure Database for PostgreSQL `max_connections` a paraméter értéke az SKU-on alapul. További információ: Limits [in Azure Database for PostgreSQL](concepts-limits.md). 

Ha megpróbálja frissíteni a kiszolgáló értékeit, de nem tartja be a korlátozásokat, hibaüzenetet kap.

### <a name="max_prepared_transactions"></a>max_prepared_transactions
A [PostgreSQL megköveteli](https://www.postgresql.org/docs/10/runtime-config-resource.html#GUC-MAX-PREPARED-TRANSACTIONS) , hogy az `max_prepared_transactions` olvasási replika paraméterének értéke nagyobb legyen, mint a főérték, ellenkező esetben a replika nem indul el. Ha módosítani `max_prepared_transactions` szeretné a főkiszolgálót, először módosítsa a replikákat.

### <a name="stopped-replicas"></a>Leállított replikák
Ha leállítja a replikálást egy főkiszolgáló és egy olvasási replika között, a replika újraindul a módosítás alkalmazásához. A leállított replika önálló kiszolgáló lesz, amely fogadja az olvasásokat és az írásokat is. Az önálló kiszolgáló nem hozható létre újra replikába.

### <a name="deleted-master-and-standalone-servers"></a>Törölt fő-és önálló kiszolgálók
Főkiszolgáló törlésekor az összes olvasási replikája önálló kiszolgáló lesz. A rendszer újraindítja a replikákat, hogy tükrözze ezt a változást.

## <a name="next-steps"></a>További lépések
Ismerje meg, hogyan [hozhat létre és kezelhet olvasási replikákat a Azure Portalban](howto-read-replicas-portal.md).
