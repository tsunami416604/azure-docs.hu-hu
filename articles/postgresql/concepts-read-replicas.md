---
title: Replikák olvasása – Azure Database for PostgreSQL – egyetlen kiszolgáló
description: Ez a cikk a Azure Database for PostgreSQL-Single Server olvasási replika funkcióját ismerteti.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 06/24/2020
ms.openlocfilehash: 0d678d900ec31b00d27eba19617d533c5010c1dc
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85367995"
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

>[!NOTE]
> Az alapszintű kiszolgálók csak azonos régióbeli replikációt támogatnak.

A főkiszolgáló bármely [Azure Database for PostgreSQL régióban](https://azure.microsoft.com/global-infrastructure/services/?products=postgresql)elérhető. A főkiszolgáló rendelkezhet replikával a párosított régiójában vagy az univerzális replika régiókban. Az alábbi képen látható, hogy mely replika régiók érhetők el a fő régiótól függően.

[![Replika-régiók olvasása](media/concepts-read-replica/read-replica-regions.png)](media/concepts-read-replica/read-replica-regions.png#lightbox)

### <a name="universal-replica-regions"></a>Univerzális replika-régiók
A következő régiókban bármikor létrehozhat egy olvasási replikát, függetlenül attól, hogy hol található a főkiszolgáló. Ezek az univerzális replika-régiók:

Kelet-Ausztrália, Délkelet-Ausztrália, USA középső régiója, Kelet-Ázsia, USA keleti régiója, USA 2. keleti régiója, Kelet-Japán, Nyugat-Japán, Dél-Korea, Dél-Korea, Észak-Európa, az USA déli középső régiója, Délkelet-Ázsia, Egyesült Királyság déli régiója, Egyesült Királyság nyugati régiója, Nyugat-Európa, USA nyugati régiója, USA 2. nyugati középső régiója

### <a name="paired-regions"></a>Párosított régiók
Az univerzális replika régión kívül egy olvasási replikát is létrehozhat a főkiszolgáló Azure párosított régiójában. Ha nem ismeri a régió pár elemét, többet is megtudhat az [Azure párosított régiókról szóló cikkből](../best-practices-availability-paired-regions.md).

Ha régiók közötti replikákat használ a vész-helyreállítási tervezéshez, javasoljuk, hogy a többi régió helyett a párosított régióban hozza létre a replikát. A párosított régiók elkerülik az egyidejű frissítéseket, és rangsorolják a fizikai elkülönítést és az adattárolást.  

A következő szempontokat kell figyelembe venni: 

* Regionális elérhetőség: Azure Database for PostgreSQL a közép-Franciaországban, Észak-és Közép-Németország középső régiójában érhető el. A párosított régiói azonban nem érhetők el.
    
* Egyirányú párok: egyes Azure-régiók csak egyetlen irányban vannak párosítva. Ezek a régiók közé tartoznak a Nyugat-India, Dél-Brazília. 
   Ez azt jelenti, hogy a Nyugat-Indiai főkiszolgáló létrehozhat egy replikát Dél-Indiában. A dél-indiai főkiszolgálók azonban nem hozhatnak létre replikát Nyugat-Indiában. Ennek az az oka, hogy Nyugat-India másodlagos régiója Dél-India, de Dél-India másodlagos régiója nem Nyugat-India.


## <a name="create-a-replica"></a>Replika létrehozása
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

## <a name="monitor-replication"></a>Replikáció monitorozása
Azure Database for PostgreSQL két mérőszámot biztosít a replikáció figyeléséhez. A két metrika a replikák és a **replika késések** **közötti maximális késés** . A metrikák megtekintésével kapcsolatos további információkért tekintse meg a replika **figyelése** című szakaszt a [replika olvasása című cikkben](howto-read-replicas-portal.md).

A **replikák közötti maximális késés** a fő és a legkésleltetett replika közötti késést mutatja bájtban. Ez a metrika csak a főkiszolgálón érhető el.

A **replika késésének** mérőszáma az utolsó visszajátszott tranzakció óta eltelt időt mutatja. Ha nincs tranzakció a főkiszolgálón, a metrika ezt az időbeli késést tükrözi. Ez a metrika csak replika kiszolgálók esetében érhető el. A replika késését a nézetből számítjuk ki `pg_stat_wal_receiver` :

```SQL
EXTRACT (EPOCH FROM now() - pg_last_xact_replay_timestamp());
```

Állítson be egy riasztást, amely tájékoztatja arról, ha a replika késése olyan értéket ér el, amely nem fogadható el a számítási feladathoz. 

További információk: lekérdezés a főkiszolgálóról közvetlenül a replikálás késésének lekéréséhez az összes replikán.

A PostgreSQL 10-es verziójában:

```SQL
select pg_wal_lsn_diff(pg_current_wal_lsn(), replay_lsn) 
AS total_log_delay_in_bytes from pg_stat_replication;
```

A PostgreSQL 9,6-es és korábbi verzióiban:

```SQL
select pg_xlog_location_diff(pg_current_xlog_location(), replay_location) 
AS total_log_delay_in_bytes from pg_stat_replication;
```

> [!NOTE]
> Ha a főkiszolgáló vagy az olvasási replika újraindul, az újraindításhoz és a betöltéshez szükséges idő a replika késési metrikájában jelenik meg.

## <a name="stop-replication"></a>Replikáció leállítása
Leállíthatja a replikációt egy főkiszolgáló és egy replika között. A Leállítás művelet a replika újraindítását és a replikációs beállítások eltávolítását eredményezi. Miután leállította a replikálást egy főkiszolgáló és egy olvasási replika között, a replika önálló kiszolgáló lesz. Az önálló kiszolgálóban található, a replikálás leállítása parancs elindításának időpontjában a replikán elérhető adatértékek. Az önálló kiszolgáló nem fog felzárkózni a főkiszolgálóval.

> [!IMPORTANT]
> Az önálló kiszolgáló nem hozható létre újra replikába.
> Mielőtt leállítja a replikálást egy olvasási replikán, győződjön meg arról, hogy a replika rendelkezik a szükséges összes adattal.

Ha leállítja a replikálást, a replika elveszíti az előző főkiszolgálóra és más replikára mutató összes hivatkozást.

Megtudhatja, hogyan [állíthatja le a replikálást egy replikára](howto-read-replicas-portal.md).

## <a name="failover"></a>Feladatátvétel
A fő-és a replika-kiszolgálók között nincs automatikus feladatátvétel. 

Mivel a replikáció aszinkron, a főkiszolgáló és a replika között késés van. A késés mértékét számos tényező befolyásolja, például a főkiszolgálón futó munkaterhelés, valamint az adatközpontok közötti késleltetés. A legtöbb esetben a replika-késés néhány másodperc és néhány perc között mozog. A tényleges replikációs késést a metrikai *replika késésének*használatával követheti nyomon, amely az egyes replikák esetében elérhető. Ez a metrika az utolsó újrajátszott tranzakció óta eltelt időt mutatja. Azt javasoljuk, hogy azonosítsa az átlagos késést úgy, hogy a replika késését egy adott időszakra figyelje. Beállíthat egy riasztást a replika késésével kapcsolatban, hogy ha az a várt tartományon kívül esik, megteheti a műveletet.

> [!Tip]
> Ha feladatátvételt hajt végre a replikára, akkor a replika a főkiszolgálóról való leválasztásakor a késés azt jelzi, hogy mekkora adatvesztés történik.

Ha úgy döntött, hogy feladatátvételt kíván replikálni egy replikára, 

1. A replika replikálásának leállítása<br/>
   Ez a lépés szükséges ahhoz, hogy a replika-kiszolgáló el tudja fogadni az írásokat. Ennek a folyamatnak a részeként a replika-kiszolgáló újraindul, és leválasztása a főkiszolgálóról történik. Miután elindította a replikálást, a háttérrendszer-folyamat általában 2 percet vesz igénybe. A művelet következményeinek megismeréséhez tekintse meg a jelen cikk [replikálás leállítása](#stop-replication) című szakaszát.
    
2. Az alkalmazás átirányítása a (korábbi) replikára<br/>
   Minden kiszolgálón egyedi a kapcsolatok karakterlánca. Frissítse az alkalmazást, hogy a főkiszolgáló helyett a (korábbi) replikára mutasson.
    
Miután az alkalmazás sikeresen feldolgozta az olvasásokat és az írásokat, befejezte a feladatátvételt. Az alkalmazás által tapasztalható állásidő mennyisége a probléma észlelése és a fenti 1. és 2. lépés elvégzése után függ.


## <a name="considerations"></a>Megfontolandó szempontok

Ez a szakasz az olvasási replika szolgáltatással kapcsolatos szempontokat foglalja össze.

### <a name="prerequisites"></a>Előfeltételek
Az olvasási replikák és a [logikai dekódolás](concepts-logical.md) a postgres írási előre megadott naplójától (Wal) függ. Ehhez a két szolgáltatáshoz különböző naplózási szintek szükségesek a postgres. A logikai dekódolás magasabb szintű naplózást igényel, mint az olvasási replikák.

A megfelelő naplózási szint konfigurálásához használja az Azure-replikáció támogatási paraméterét. Az Azure-beli replikáció támogatásának három beállítási lehetősége van:

* **Off** – a legkevesebb információt helyezi el a Wal-ben. Ez a beállítás nem érhető el a legtöbb Azure Database for PostgreSQL kiszolgálón.  
* **Replika** – részletesebb, mint a **kikapcsolás**. Az [olvasási replikák](concepts-read-replicas.md) működéséhez szükséges naplózás minimális szintje. Ez a beállítás az alapértelmezett a legtöbb kiszolgálón.
* **Logikai** – részletesebb, mint a **replika**. Ez a minimális szintű naplózás a logikai dekódolás működéséhez. Ebben a beállításban az olvasási replikák is működnek.

A kiszolgálót újra kell indítani a paraméter módosítása után. Belsőleg ez a paraméter a és a postgres paramétereket állítja be `wal_level` `max_replication_slots` `max_wal_senders` .

### <a name="new-replicas"></a>Új replikák
Az olvasási replika új Azure Database for PostgreSQL-kiszolgálóként jön létre. Egy meglévő kiszolgálót nem lehet replikába készíteni. Egy másik olvasási replika replikája nem hozható létre.

### <a name="replica-configuration"></a>Replika konfigurációja
A replika ugyanazokkal a számítási és tárolási beállításokkal jön létre, mint a főkiszolgáló. A replika létrehozása után több beállítás is módosítható a főkiszolgálótól függetlenül: számítási generáció, virtuális mag, tárterület és biztonsági mentés megőrzési ideje. Az árképzési szint külön is módosítható, kivéve az alapszintű csomagból vagy abból.

> [!IMPORTANT]
> A főbeállítás új értékre frissítése előtt frissítse a replika konfigurációját egy egyenlő vagy nagyobb értékre. Ez a művelet biztosítja, hogy a replika összhangban lehessen a főkiszolgálón végrehajtott módosításokkal.

A PostgreSQL megköveteli `max_connections` , hogy az olvasási replika paraméterének értéke nagyobb legyen, mint a főérték, ellenkező esetben a replika nem indul el. A Azure Database for PostgreSQL a `max_connections` paraméter értéke az SKU-on alapul. További információ: [Limits in Azure Database for PostgreSQL](concepts-limits.md). 

Ha a fent ismertetett kiszolgálói értékeket próbálja meg frissíteni, de nem tartja be a korlátozásokat, hibaüzenetet kap.

A tűzfalszabályok, a virtuális hálózati szabályok és a paraméterek beállításai nem öröklődnek a főkiszolgálóról a replikára a replika létrehozásakor vagy azt követően.

### <a name="basic-tier"></a>Alapszintű csomag
Az alapszintű kiszolgálók csak azonos régióbeli replikációt támogatnak.

### <a name="max_prepared_transactions"></a>max_prepared_transactions
A [PostgreSQL megköveteli](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-MAX-PREPARED-TRANSACTIONS) `max_prepared_transactions` , hogy az olvasási replika paraméterének értéke nagyobb legyen, mint a főérték, ellenkező esetben a replika nem indul el. Ha módosítani szeretné a `max_prepared_transactions` főkiszolgálót, először módosítsa a replikákat.

### <a name="stopped-replicas"></a>Leállított replikák
Ha leállítja a replikálást egy főkiszolgáló és egy olvasási replika között, a replika újraindul a módosítás alkalmazásához. A leállított replika önálló kiszolgáló lesz, amely fogadja az olvasásokat és az írásokat is. Az önálló kiszolgáló nem hozható létre újra replikába.

### <a name="deleted-master-and-standalone-servers"></a>Törölt fő-és önálló kiszolgálók
Főkiszolgáló törlésekor az összes olvasási replikája önálló kiszolgáló lesz. A rendszer újraindítja a replikákat, hogy tükrözze ezt a változást.

## <a name="next-steps"></a>További lépések
* Ismerje meg, hogyan [hozhat létre és kezelhet olvasási replikákat a Azure Portalban](howto-read-replicas-portal.md).
* Ismerje meg, hogyan [hozhat létre és kezelhet olvasási replikákat az Azure CLI-ben és a REST APIban](howto-read-replicas-cli.md).
