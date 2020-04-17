---
title: Replikák olvasása - Azure Database for PostgreSQL - Single Server
description: Ez a cikk ismerteti az olvasási replika szolgáltatás az Azure Database for PostgreSQL - Single Server.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/23/2020
ms.openlocfilehash: 545d04bdede76a6ce25c9e4665f39c01ff6caa73
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81531983"
---
# <a name="read-replicas-in-azure-database-for-postgresql---single-server"></a>Replikák olvasása az Azure Database for PostgreSQL -Single Server szolgáltatásban

Az olvasási replika szolgáltatás lehetővé teszi az adatok replikálását egy Azure Database for PostgreSQL-kiszolgálóról egy írásvédett kiszolgálóra. A főkiszolgálóról legfeljebb öt replikára másolhatja az adatokat. A replikák aszinkron módon frissülnek a PostgreSQL motor natív replikációs technológiájával.

A replikák olyan új kiszolgálók, amelyeket a postgreSQL-kiszolgálókhoz készült normál Azure Database szolgáltatáshoz hasonló módon kezel. Minden egyes olvasási replika, a kiosztott számítási virtuális magok és a tárolás GB/ hó.

További információ a [kópiák létrehozásáról és kezeléséről.](howto-read-replicas-portal.md)

## <a name="when-to-use-a-read-replica"></a>Mikor kell olvasni a kópiát használni?
Az olvasási replika szolgáltatás segít az olvasási igényű munkaterhelések teljesítményének és méretezésének javításában. Olvasási számítási feladatok elkülöníthetők a replikák, míg az írási számítási feladatok a főkiszolgálóra irányítható.

Gyakori forgatókönyv, hogy az üzleti intelligencia és az analitikus számítási feladatok az olvasási replika, mint a jelentési forrás.

Mivel a replikák írásvédettek, nem csökkentik közvetlenül a főkiszolgáló írási kapacitásterheit. Ez a funkció nem az írásigényes számítási feladatok.

Az olvasási replika szolgáltatás postgreSQL aszinkron replikációt használ. A szolgáltatás nem szinkron replikációs forgatókönyvekhez való. Mérhető késleltetés lesz a mester és a replika között. A kópia adatai végül konzisztensek lesznek a főkiszolgálón lévő adatokkal. Ezt a szolgáltatást olyan számítási feladatokhoz használja, amelyek képesek ezt a késleltetést kezelni.

## <a name="cross-region-replication"></a>Régiók közötti replikáció
A főkiszolgálótól eltérő régióban hozhat létre olvasási kópiát. A régiók közötti replikáció hasznos lehet olyan esetekben, mint a vész-helyreállítási tervezés vagy az adatok közelebb hozása a felhasználókhoz.

A [PostgreSQL-régióban bármely Azure Database for PostgreSQL régióban](https://azure.microsoft.com/global-infrastructure/services/?products=postgresql)rendelkezhet főkiszolgálóval. A főkiszolgáló rendelkezhet egy replika a párosított régióban vagy az univerzális replika régiókban. Az alábbi képen látható, hogy mely replikarégiók érhetők el a fő régiótól függően.

[![Replikaterületek olvasása](media/concepts-read-replica/read-replica-regions.png)](media/concepts-read-replica/read-replica-regions.png#lightbox)

### <a name="universal-replica-regions"></a>Univerzális replikaterületek
Olvasási kópiát mindig létrehozhat a következő régiók bármelyikében, függetlenül attól, hogy a főkiszolgáló hol található. Ezek az univerzális replikarégiók:

Kelet-Ausztrália, Ausztrália Délkelet, USA középső része, Kelet-Ázsia, USA keleti régiója 2, Japán kelet, Nyugat-Japán, Korea Közép-Korea, Korea South, Észak-Közép-USA, Észak-Európa, Usa középső középső régiója, Délkelet-Ázsia, Egyesült Királyság déli, egyesült királyságbeli nyugati, nyugat-európai, usa nyugati régiója.

*Az USA nyugati régiója 2 átmenetileg nem érhető el régióközi replikahelyként.


### <a name="paired-regions"></a>Párosított régiók
Az univerzális replikarégiók mellett létrehozhat egy olvasási replikát a főkiszolgáló Azure-ban párosított régióban. Ha nem ismeri a régió párját, további információaz [Azure Paired Regions cikkből.](../best-practices-availability-paired-regions.md)

Ha régióközi replikákat használ a vész-helyreállítási tervezéshez, javasoljuk, hogy a más régiók helyett a párosított régióban hozza létre a replikát. A párosított régiók elkerülik az egyidejű frissítéseket, és rangsorolják a fizikai elkülönítést és az adatok rezidensségét.  

Vannak korlátozások, amelyeket figyelembe kell venni: 

* Regionális elérhetőség: Az Azure Database for PostgreSQL az USA nyugati részén 2, Franciaország középső részén, az Egyesült Arab Emírségek északi részén és Németország központi részén érhető el. A párosított régiók azonban nem érhetők el.
    
* Egyirányú párok: Egyes Azure-régiók csak egy irányban vannak párosítva. Ezek a régiók közé tartozik Nyugat-India, Brazília Dél. 
   Ez azt jelenti, hogy a nyugat-indiai főkiszolgáló replikát hozhat létre Dél-Indiában. A dél-indiai főkiszolgáló azonban nem hozhat létre kópiát Nyugat-Indiában. Ez azért van, mert Nyugat-India másodlagos régió Dél-India, de Dél-India másodlagos régió nem Nyugat-India.


## <a name="create-a-replica"></a>Replika létrehozása
A replika-munkafolyamat létrehozásakor létrejön egy üres Azure-adatbázis a PostgreSQL-kiszolgálóhoz. Az új kiszolgáló tele van a főkiszolgálón lévő adatokkal. A létrehozási idő a főkiszolgálón lévő adatok mennyiségétől és az utolsó heti teljes biztonsági mentés óta eltelt időtől függ. Az idő néhány perctől több óráig terjedhet.

Minden replika engedélyezve van a tároló [automatikus növekedéséhez.](concepts-pricing-tiers.md#storage-auto-grow) Az automatikus növekedés funkció lehetővé teszi, hogy a replika lépést tartson a replikált adatokkal, és megakadályozza a replikáció tanévben a tárolási hibák által okozott megszakítást.

Az olvasási replika szolgáltatás PostgreSQL fizikai replikációt használ, nem logikai replikációt. A replikációs tárolóhelyek használatával történő streamelési replikáció az alapértelmezett üzemmód. Szükség esetén a naplószállítást használják a felzárkózáshoz.

Ismerje meg, hogyan [hozhat létre olvasási replikát az Azure Portalon.](howto-read-replicas-portal.md)

## <a name="connect-to-a-replica"></a>Csatlakozás kópiához
Kópia létrehozásakor nem örökli a főkiszolgáló tűzfalszabályait vagy virtuális hálózatszolgáltatás-végpontját. Ezeket a szabályokat egymástól függetlenül kell beállítani a kópiához.

A replika örökli a rendszergazdai fiókot a főkiszolgálótól. A főkiszolgáló összes felhasználói fiókja replikálása az olvasási replikákra történik. Az olvasási kópiához csak a főkiszolgálón elérhető felhasználói fiókok használatával lehet csatlakozni.

Csatlakozhat a replika segítségével a hostname és egy érvényes felhasználói fiókot, ahogy azt egy normál Azure Database for PostgreSQL-kiszolgálón. A kiszolgáló nevű **replika** a admin felhasználónév **myadmin**, akkor csatlakozhat a replika segítségével psql:

```
psql -h myreplica.postgres.database.azure.com -U myadmin@myreplica -d postgres
```

A kérdésben adja meg a felhasználói fiók jelszavát.

## <a name="monitor-replication"></a>Replikáció monitorozása
A PostgreSQL Azure Database két metrikát biztosít a replikáció figyeléséhez. A két mutató **a Max Lag Across Replicas** és a Replica **Lag**. A metrikák megtekintéséről a [Replika-útmutató cikk](howto-read-replicas-portal.md) **Replika figyelő szakasza** című témakörben olvashat.

A **Max Lag Across Replicas metrika** a fő és a legelmaradottabb replika közötti késést jeleníti meg bájtban. Ez a mérőszám csak a főkiszolgálón érhető el.

A **Replika késés** metrika az utolsó visszajátszott tranzakció óta eltelt időt mutatja. Ha a főkiszolgálón nem történnek tranzakciók, a metrika ezt az időeltolódást tükrözi. Ez a metrika csak replikakiszolgálók esetén érhető el. A Replika `pg_stat_wal_receiver` késés számítása a következő nézetből történik:

```SQL
EXTRACT (EPOCH FROM now() - pg_last_xact_replay_timestamp());
```

Állítson be egy riasztást, amely tájékoztatja, ha a replika késése eléri a számítási feladatokhoz nem elfogadható értéket. 

További betekintést, a lekérdezés a fő kiszolgáló közvetlenül lekérni a replikációs lagte az összes replikák.

A PostgreSQL 10-es verziójában:

```SQL
select pg_wal_lsn_diff(pg_current_wal_lsn(), replay_lsn) 
AS total_log_delay_in_bytes from pg_stat_replication;
```

A PostgreSQL 9.6-os és korábbi verziójában:

```SQL
select pg_xlog_location_diff(pg_current_xlog_location(), replay_location) 
AS total_log_delay_in_bytes from pg_stat_replication;
```

> [!NOTE]
> Ha egy főkiszolgáló vagy olvasni replikák újraindul, az újraindításhoz és a felzárkózáshoz szükséges idő tükröződik a Replika késés metrika.

## <a name="stop-replication"></a>Replikáció leállítása
A főkiszolgáló és a kópia közötti replikáció leállíthatja. A leállítási művelet hatására a kópia újraindul, és eltávolítja a replikációs beállításokat. A főkiszolgáló és az olvasási kópia közötti replikáció leállítása után a replika önálló kiszolgálóvá válik. Az önálló kiszolgálón lévő adatok azok az adatok, amelyek a replikáció leállításakor a replikán rendelkezésre állnak. Az önálló kiszolgáló nem éri el a főkiszolgálót.

> [!IMPORTANT]
> Az önálló kiszolgáló nem alakítható újra kópiává.
> Mielőtt leállítja a replikációt egy olvasási kópián, győződjön meg arról, hogy a replika rendelkezik az összes szükséges adatkal.

A replikáció leállításakor a replika elveszíti az előző kópia és más kópiák összes csatolását.

További információ a [kópia replikációjának leállításáról.](howto-read-replicas-portal.md)

## <a name="failover"></a>Feladatátvétel
A fő- és a replikakiszolgálók között nincs automatikus feladatátvétel. 

Mivel a replikáció aszinkron, a főkiszolgáló és a replika között késés van. A késés mértékét számos tényező befolyásolhatja, például a főkiszolgálón futó munkaterhelés és az adatközpontok közötti késés. A legtöbb esetben a replika késése néhány másodperc és néhány perc között mozog. Nyomon követheti a tényleges replikációs késés a metria késés metria *kópia,* amely elérhető az egyes replika. Ez a mérőszám az utolsó visszajátszott tranzakció óta eltelt időt mutatja. Azt javasoljuk, hogy azonosítsa, mi az átlagos késés a replika késésének megfigyelésével egy ideig. Beállíthat egy riasztást a replika késése, így ha ez kívül esik a várt tartományban, akkor műveleteket.

> [!Tip]
> Ha feladatátvételt a replikát, a késés a kópia leválasztásakor a mester replikát jelzi, hogy mennyi adat vesznek el.

Miután eldöntötte, hogy feladatátvételt szeretne egy replika 

1. A kópia replikációjának leállítása<br/>
   Ez a lépés szükséges ahhoz, hogy a replikakiszolgáló képes legyen az írások elfogadására. A folyamat részeként a replikakiszolgáló újraindul, és a főkiszolgálóról leválik a rendszerből. A replikáció leállítása után a háttérfolyamat általában körülbelül 2 percet vesz igénybe. A művelet következményeinek megismeréséhez tekintse meg a cikk [leállítási replikációs](#stop-replication) szakaszát.
    
2. Az alkalmazás rámutatása az (előző) kópiára<br/>
   Minden kiszolgáló egyedi kapcsolati karakterlánccal rendelkezik. Frissítse az alkalmazást úgy, hogy a főkiszolgáló helyett az (előző) kópiára mutasson.
    
Miután az alkalmazás sikeresen feldolgozza az olvasási és írási műveleteket, befejezte a feladatátvételt. Az alkalmazásélmény ek állásideje attól függ, hogy mikor észlel problémát, és végezze el a fenti 1.


## <a name="considerations"></a>Megfontolandó szempontok

Ez a szakasz összefoglalja az olvasási replika szolgáltatással kapcsolatos szempontokat.

### <a name="prerequisites"></a>Előfeltételek
Az olvasási replika `azure.replication_support` létrehozása előtt a paramétert **a főkiszolgálón a REP-re** kell állítani. Ha ez a paraméter megváltozik, a módosítás érvénybe léptetéséhez kiszolgáló-újraindításszükséges. A `azure.replication_support` paraméter csak az általános célú és a memória optimalizált szintekre vonatkozik.

### <a name="new-replicas"></a>Új kópiák
Az olvasási replika jön létre, mint egy új Azure-adatbázis PostgreSQL-kiszolgáló. Meglévő kiszolgáló nem alakítható át kópiává. Nem hozhat létre más olvasott kópiát.

### <a name="replica-configuration"></a>Replika konfigurációja
A kópia a főkiszolgálóval azonos számítási és tárolási beállításokkal jön létre. A replika létrehozása után számos beállítás módosítható a fő kiszolgálótól függetlenül: számítási generálás, virtuális magok, tárolás és biztonsági mentés megőrzési időszaka. A tarifacsomag is lehet változtatni egymástól függetlenül, kivéve, hogy vagy az alapszintű szintről.

> [!IMPORTANT]
> Mielőtt a főbeállítás új értékre frissülne, frissítse a replika konfigurációját azonos vagy nagyobb értékre. Ez a művelet biztosítja, hogy a replika összhangban lehessen a főkiszolgálón végrehajtott módosításokkal.

A PostgreSQL megköveteli, `max_connections` hogy az olvasási replika paraméterének értéke nagyobb vagy egyenlő legyen a törzsértéknél; ellenkező esetben a replika nem indul el. A PostgreSQL Azure Database-ben a `max_connections` paraméter értéke a termékváltozaton alapul. További információ: [Limits in Azure Database for PostgreSQL](concepts-limits.md). 

Ha megpróbálja frissíteni a fent leírt kiszolgálói értékeket, de nem tartja be a határértékeket, hibaüzenetet kap.

A tűzfalszabályok, a virtuális hálózati szabályok és a paraméterbeállítások nem öröklődnek a főkiszolgálótól a kópiához a kópia létrehozásakor vagy azt követően.

### <a name="max_prepared_transactions"></a>max_prepared_transactions
[A PostgreSQL megköveteli,](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-MAX-PREPARED-TRANSACTIONS) hogy az `max_prepared_transactions` olvasási replika paraméterének értéke nagyobb vagy egyenlő legyen a törzsértéknél; ellenkező esetben a replika nem indul el. Ha módosítani `max_prepared_transactions` szeretné a mesterkiszolgálót, először módosítsa azt a kópiákon.

### <a name="stopped-replicas"></a>Leállított replikák
Ha leállítja a replikációt a főkiszolgáló és az olvasási kópia között, a kópia újraindul a módosítás alkalmazásához. A leállított replika önálló kiszolgálóvá válik, amely elfogadja az olvasást és az írást is. Az önálló kiszolgáló nem alakítható újra kópiává.

### <a name="deleted-master-and-standalone-servers"></a>Törölt fő- és önálló kiszolgálók
A főkiszolgáló törlésekor az összes olvasási replikája önálló kiszolgálóvá válik. A replikák újraindulnak, hogy tükrözzék ezt a változást.

## <a name="next-steps"></a>További lépések
* Ismerje meg, hogyan [hozhat létre és kezelhet olvasási replikákat az Azure Portalon.](howto-read-replicas-portal.md)
* Ismerje meg, hogyan [hozhat létre és kezelhet olvasási replikákat az Azure CLI és REST API-ban.](howto-read-replicas-cli.md)
