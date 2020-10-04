---
title: Replikák olvasása – Azure Database for PostgreSQL – egyetlen kiszolgáló
description: Ez a cikk a Azure Database for PostgreSQL-Single Server olvasási replika funkcióját ismerteti.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: conceptual
ms.date: 08/10/2020
ms.openlocfilehash: 2d0ee0e4c5cf3f7c2f4b623f0270ecf5eb01fc36
ms.sourcegitcommit: 19dce034650c654b656f44aab44de0c7a8bd7efe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/04/2020
ms.locfileid: "91710515"
---
# <a name="read-replicas-in-azure-database-for-postgresql---single-server"></a>Replikák olvasása Azure Database for PostgreSQL – egyetlen kiszolgáló

Az olvasási replika funkció lehetővé teszi az adatok replikálását egy Azure Database for PostgreSQL-kiszolgálóról egy írásvédett kiszolgálóra. Az elsődleges kiszolgálóról legfeljebb öt replikára lehet replikálni. A replikák aszinkron módon frissülnek a PostgreSQL-motor natív replikációs technológiájával.

A replikák új, Ön által kezelt kiszolgálók, amelyek hasonlítanak a hagyományos Azure Database for PostgreSQL-kiszolgálókhoz. Az egyes olvasási replikák esetében a virtuális mag és a Storage szolgáltatásban a kiépített számítási kapacitást a GB/hó értékben számítjuk fel.

Megtudhatja, hogyan [hozhat létre és kezelhet replikákat](howto-read-replicas-portal.md).

## <a name="when-to-use-a-read-replica"></a>Mikor használjon olvasási replikát
Az olvasási replika funkcióval javítható a nagy olvasási igényű számítási feladatok teljesítménye és méretezése. Az olvasási munkaterhelések elkülöníthetők a replikákkal, míg az írási munkaterhelések az elsődlegesre irányíthatók.

Gyakori forgatókönyv, hogy a BI-és analitikai munkaterhelések az olvasási replikát használják adatforrásként a jelentéskészítéshez.

Mivel a replikák csak olvashatók, nem csökkentik közvetlenül a írási kapacitás terheit az elsődlegesen. Ez a funkció nem a nagy írási igényű számítási feladatokhoz ideális.

Az olvasási replika funkció PostgreSQL aszinkron replikálást használ. A funkció nem a szinkron replikációs forgatókönyvek esetében jelent meg. Az elsődleges és a replika között mérhető késés lesz. A replikán lévő adatértékek végül konzisztensek az elsődlegesen tárolt értékekkel. Használja ezt a szolgáltatást olyan számítási feladatokhoz, amelyek alkalmasak erre a késésre.

## <a name="cross-region-replication"></a>Régiók közötti replikáció
Az elsődleges kiszolgálótól eltérő régióban is létrehozhat egy olvasási replikát. A régiók közötti replikáció hasznos lehet olyan forgatókönyvek esetén, mint például a vész-helyreállítási tervezés vagy az adatok közelebb hozása a felhasználókhoz.

>[!NOTE]
> Az alapszintű kiszolgálók csak azonos régióbeli replikációt támogatnak.

Egyetlen [Azure Database for PostgreSQL régióban](https://azure.microsoft.com/global-infrastructure/services/?products=postgresql)is rendelkezhet elsődleges kiszolgálóval. Egy elsődleges kiszolgálónak lehetnek replikái a párosított régiójában vagy az univerzális replika régiókban. Az alábbi képen látható, hogy mely replika régiók érhetők el az elsődleges régiótól függően.

[:::image type="content" source="media/concepts-read-replica/read-replica-regions.png" alt-text="Replika-régiók olvasása":::](media/concepts-read-replica/read-replica-regions.png#lightbox)

### <a name="universal-replica-regions"></a>Univerzális replika-régiók
A következő régiókban bármikor létrehozhat egy olvasási replikát, függetlenül attól, hogy az elsődleges kiszolgáló hol található. Ezek az univerzális replika-régiók:

Kelet-Ausztrália, Délkelet-Ausztrália, USA középső régiója, Kelet-Ázsia, USA keleti régiója, USA 2. keleti régiója, Kelet-Japán, Nyugat-Japán, Dél-Korea, Dél-Korea, Észak-Európa, az USA déli középső régiója, Délkelet-Ázsia, Egyesült Királyság déli régiója, Egyesült Királyság nyugati régiója, Nyugat-Európa, USA nyugati régiója, USA 2. nyugati középső régiója

### <a name="paired-regions"></a>Párosított régiók
Az univerzális replika régión kívül az elsődleges kiszolgáló Azure párosított régiójában is létrehozhat egy olvasási replikát. Ha nem ismeri a régió pár elemét, többet is megtudhat az [Azure párosított régiókról szóló cikkből](../best-practices-availability-paired-regions.md).

Ha régiók közötti replikákat használ a vész-helyreállítási tervezéshez, javasoljuk, hogy a többi régió helyett a párosított régióban hozza létre a replikát. A párosított régiók elkerülik az egyidejű frissítéseket, és rangsorolják a fizikai elkülönítést és az adattárolást.  

A következő szempontokat kell figyelembe venni: 

* Regionális elérhetőség: Azure Database for PostgreSQL a közép-Franciaországban, Észak-és Közép-Németország középső régiójában érhető el. A párosított régiói azonban nem érhetők el.
    
* Egyirányú párok: egyes Azure-régiók csak egyetlen irányban vannak párosítva. Ezek a régiók közé tartoznak a Nyugat-India, Dél-Brazília. 
   Ez azt jelenti, hogy a Nyugat-Indiában található elsődleges kiszolgáló létrehoz egy replikát Dél-Indiában. A Dél-India elsődleges kiszolgálója azonban nem tud replikát létrehozni Nyugat-Indiában. Ennek az az oka, hogy Nyugat-India másodlagos régiója Dél-India, de Dél-India másodlagos régiója nem Nyugat-India.


## <a name="create-a-replica"></a>Replika létrehozása
A replika létrehozása munkafolyamat indításakor létrejön egy üres Azure Database for PostgreSQL-kiszolgáló. Az új kiszolgáló az elsődleges kiszolgálón található adattal van feltöltve. A létrehozási idő az elsődleges és az utolsó heti teljes biztonsági mentés óta eltelt idő mennyiségétől függ. Az idő néhány perctől akár több órára is terjedhet.

Minden replika engedélyezve van a tárterület [automatikus növelésére](concepts-pricing-tiers.md#storage-auto-grow). Az automatikus növekedés funkció lehetővé teszi, hogy a replika megőrizze az általa replikált adatmennyiséget, és megakadályozza, hogy a replikálás megszakítása a tárolási hibák miatt meghiúsult.

Az olvasási replika funkció PostgreSQL fizikai replikálást, nem logikai replikálást használ. A replikációs tárolóhelyek szolgáltatással történő folyamatos átvitel az alapértelmezett működési mód. Ha szükséges, a rendszer naplózza a szállítást a felzárkózás érdekében.

Megtudhatja, hogyan [hozhat létre olvasási replikát a Azure Portalban](howto-read-replicas-portal.md).

## <a name="connect-to-a-replica"></a>Kapcsolódás replikához
Replika létrehozásakor nem örökli az elsődleges kiszolgáló tűzfalszabályok vagy VNet szolgáltatásának végpontját. Ezeket a szabályokat a replika egymástól függetlenül kell beállítani.

A replika örökli a rendszergazdai fiókot az elsődleges kiszolgálóról. Az elsődleges kiszolgálón lévő összes felhasználói fiók replikálódik az olvasási replikára. Csak olvasási replikához csatlakozhat az elsődleges kiszolgálón elérhető felhasználói fiókok használatával.

A replikához a hostname és egy érvényes felhasználói fiók használatával kapcsolódhat, ahogy azt egy normál Azure Database for PostgreSQL-kiszolgálón tenné. A **replika** és a rendszergazdai Felhasználónév **myadmin**nevű kiszolgáló esetén a psql használatával kapcsolódhat a replikához:

```
psql -h myreplica.postgres.database.azure.com -U myadmin@myreplica -d postgres
```

A parancssorba írja be a felhasználói fiókhoz tartozó jelszót.

## <a name="monitor-replication"></a>Replikáció monitorozása
Azure Database for PostgreSQL két mérőszámot biztosít a replikáció figyeléséhez. A két metrika a replikák és a **replika késések** **közötti maximális késés** . A metrikák megtekintésével kapcsolatos további információkért tekintse meg a replika **figyelése** című szakaszt a [replika olvasása című cikkben](howto-read-replicas-portal.md).

A **replikák közötti maximális késés** az elsődleges és a legkésleltető replika közötti késést mutatja bájtban. Ez a metrika csak az elsődleges kiszolgálón érhető el, és csak akkor lesz elérhető, ha legalább az egyik olvasási replika csatlakozik az elsődlegeshez.

A **replika késésének** mérőszáma az utolsó visszajátszott tranzakció óta eltelt időt mutatja. Ha az elsődleges kiszolgálón nincsenek tranzakciók, akkor a metrika ezt az időbeli késést tükrözi. Ez a metrika csak replika kiszolgálók esetében érhető el. A replika késését a nézetből számítjuk ki `pg_stat_wal_receiver` :

```SQL
EXTRACT (EPOCH FROM now() - pg_last_xact_replay_timestamp());
```

Állítson be egy riasztást, amely tájékoztatja arról, ha a replika késése olyan értéket ér el, amely nem fogadható el a számítási feladathoz. 

További információk: az elsődleges kiszolgáló közvetlen lekérdezése a replikálás késésének lekéréséhez az összes replika bájtjain.

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
> Ha egy elsődleges kiszolgáló vagy olvasási replika újraindul, az újraindításhoz és a betöltéshez szükséges idő a replika késési metrikájában jelenik meg.

## <a name="stop-replication"></a>Replikáció leállítása
Leállíthatja az elsődleges és a replika közötti replikációt. A Leállítás művelet a replika újraindítását és a replikációs beállítások eltávolítását eredményezi. Miután leállította egy elsődleges kiszolgáló és egy olvasási replika közötti replikálást, a replika önálló kiszolgáló lesz. Az önálló kiszolgálóban található, a replikálás leállítása parancs elindításának időpontjában a replikán elérhető adatértékek. Az önálló kiszolgáló nem kapja meg az elsődleges kiszolgálót.

> [!IMPORTANT]
> Az önálló kiszolgáló nem hozható létre újra replikába.
> Mielőtt leállítja a replikálást egy olvasási replikán, győződjön meg arról, hogy a replika rendelkezik a szükséges összes adattal.

Ha leállítja a replikálást, a replika elveszíti az összes korábbi elsődleges és más replikára mutató hivatkozást.

Megtudhatja, hogyan [állíthatja le a replikálást egy replikára](howto-read-replicas-portal.md).

## <a name="failover"></a>Feladatátvétel
Az elsődleges és a replika kiszolgálók között nincs automatikus feladatátvétel. 

Mivel a replikáció aszinkron, az elsődleges és a replika között késés van. A késés mértékét számos tényező befolyásolja, például az elsődleges kiszolgálón futó munkaterhelés, valamint az adatközpontok közötti késleltetés. A legtöbb esetben a replika késése pár másodperc vagy pár perc. A tényleges replikációs késést a metrikai *replika késésének*használatával követheti nyomon, amely az egyes replikák esetében elérhető. Ez a metrika az utolsó újrajátszott tranzakció óta eltelt időt mutatja. Azt javasoljuk, hogy azonosítsa az átlagos késést úgy, hogy a replika késését egy adott időszakra figyelje. Beállíthat egy riasztást a replika késésével kapcsolatban, hogy ha az a várt tartományon kívül esik, megteheti a műveletet.

> [!Tip]
> Ha feladatátvételt végez a replikára, akkor a replika elsődlegesről való leválasztásakor a késés azt jelzi, hogy mekkora adatvesztés történik.

Ha úgy döntött, hogy feladatátvételt kíván replikálni egy replikára, 

1. A replika replikálásának leállítása<br/>
   Ez a lépés szükséges ahhoz, hogy a replika-kiszolgáló el tudja fogadni az írásokat. Ennek a folyamatnak a részeként a replika-kiszolgáló újraindul, és az elsődlegesről lesz lecsatolva. Miután elindította a replikálást, a háttérrendszer-folyamat általában 2 percet vesz igénybe. A művelet következményeinek megismeréséhez tekintse meg a jelen cikk [replikálás leállítása](#stop-replication) című szakaszát.
    
2. Az alkalmazás átirányítása a (korábbi) replikára<br/>
   Minden kiszolgálón egyedi a kapcsolatok karakterlánca. Frissítse az alkalmazást, hogy az elsődleges helyett a (korábbi) replikára mutasson.
    
Miután az alkalmazás sikeresen feldolgozta az olvasásokat és az írásokat, befejezte a feladatátvételt. Az alkalmazás által tapasztalható állásidő mennyisége a probléma észlelése és a fenti 1. és 2. lépés elvégzése után függ.

### <a name="disaster-recovery"></a>Vészhelyreállítás

Ha van olyan súlyos katasztrófa-esemény, mint például a rendelkezésre állási zóna szintű vagy regionális meghibásodások, akkor a vész-helyreállítási műveletet az olvasási replika előléptetésével hajthatja végre. A felhasználói felület portálon navigáljon az olvasási replika kiszolgálóra. Ezután kattintson a replikáció fülre, és állítsa le a replikát úgy, hogy az egy független kiszolgáló legyen. Azt is megteheti, hogy az [Azure CLI](https://docs.microsoft.com/cli/azure/postgres/server/replica?view=azure-cli-latest#az_postgres_server_replica_stop) használatával leállítja és előlépteti a másodpéldány-kiszolgálót.

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
A replika ugyanazokkal a számítási és tárolási beállításokkal jön létre, mint az elsődleges. A replika létrehozása után több beállítás is módosítható, beleértve a tárolási és a biztonsági másolatok megőrzési időszakát.

A tűzfalszabályok, a virtuális hálózati szabályok és a paraméterek beállításai nem öröklődnek az elsődleges kiszolgálóról a replikára a replika létrehozásakor vagy azt követően.

### <a name="scaling"></a>Méretezés
A virtuális mag méretezése, illetve a általános célú és a memória optimalizálása:
* A PostgreSQL megköveteli `max_connections` , hogy a másodlagos kiszolgáló beállítása nagyobb legyen, [mint az elsődlegesnél megadott beállítás](https://www.postgresql.org/docs/current/hot-standby.html), ellenkező esetben a másodlagos nem indul el.
* Azure Database for PostgreSQL az egyes kiszolgálók maximálisan engedélyezett kapcsolatai a számítási SKU-ra vannak rögzítve, mivel a kapcsolatok memóriát foglalnak magukban. További információ a [max_connections és a számítási SKU közötti leképezésről](concepts-limits.md).
* **Felskálázás**: először a replika számítási felskálázása, majd az elsődleges skálázás. Ez a sorrend megakadályozza, hogy a hibák megsértsék a `max_connections` követelményt.
* **Lefelé méretezés**: először az elsődleges számítási felskálázás, majd a replika skálázása. Ha az elsődlegesnél alacsonyabbra próbálja méretezni a replikát, akkor a rendszer hibát jelez, mivel ez megsérti a `max_connections` követelményt.

Tárterület skálázása:
* Az összes replikán engedélyezve van a tárterület automatikus növekedése, hogy megakadályozza a teljes replika replikálási problémáit. Ez a beállítás nem tiltható le.
* A tárterületet manuálisan is méretezheti, ahogy bármely más kiszolgálón


### <a name="basic-tier"></a>Alapszint
Az alapszintű kiszolgálók csak azonos régióbeli replikációt támogatnak.

### <a name="max_prepared_transactions"></a>max_prepared_transactions
A [PostgreSQL megköveteli](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-MAX-PREPARED-TRANSACTIONS) `max_prepared_transactions` , hogy az olvasási replika paraméterének értéke nagyobb legyen, mint az elsődleges érték, ellenkező esetben a replika nem indul el. Ha módosítani kívánja `max_prepared_transactions` az elsődlegest, először módosítsa a replikákat.

### <a name="stopped-replicas"></a>Leállított replikák
Ha leállítja egy elsődleges kiszolgáló és egy olvasási replika közötti replikációt, a replika újraindul, hogy alkalmazza a módosítást. A leállított replika önálló kiszolgáló lesz, amely fogadja az olvasásokat és az írásokat is. Az önálló kiszolgáló nem hozható létre újra replikába.

### <a name="deleted-primary-and-standalone-servers"></a>Elsődleges és önálló kiszolgálók törölve
Elsődleges kiszolgáló törlésekor az összes olvasási replikája önálló kiszolgáló lesz. A rendszer újraindítja a replikákat, hogy tükrözze ezt a változást.

## <a name="next-steps"></a>Következő lépések
* Ismerje meg, hogyan [hozhat létre és kezelhet olvasási replikákat a Azure Portalban](howto-read-replicas-portal.md).
* Ismerje meg, hogyan [hozhat létre és kezelhet olvasási replikákat az Azure CLI-ben és a REST APIban](howto-read-replicas-cli.md).
