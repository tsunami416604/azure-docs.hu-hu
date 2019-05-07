---
title: Elosztott adatok az Azure Database for PostgreSQL – nagy kapacitású (Citus) (előzetes verzió)
description: Táblák és terjesztése a kiszolgálócsoport a szegmensek.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: 9020ee690d93a1b477471fac4a482a909fca5935
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65077335"
---
# <a name="distributed-data-in-azure-database-for-postgresql--hyperscale-citus-preview"></a>Elosztott adatok az Azure Database for PostgreSQL – nagy kapacitású (Citus) (előzetes verzió)

Ez a cikk ismerteti a három táblatípusok a nagy kapacitású (Citus).
Azt mutatja, hogy az elosztott táblák tárolni szegmensek, valamint a szegmensek csomópontokon vannak elhelyezve.

## <a name="table-types"></a>Táblatípusok

Egy nagy kapacitású kiszolgálócsoport táblájában három típusa van, minden más célra szolgál.

### <a name="type-1-distributed-tables"></a>1. típus: elosztott táblák

Az első típusú, és a leggyakoribb, *elosztott* táblákat. Az SQL-utasítások a normál táblázatokhoz úgy tűnhet, de vannak vízszintesen *particionált* munkavégző csomópontok között. Ez azt jelenti, hogy a sorokat a tábla különböző csomópontokon nevű töredék táblákban tárolt *szegmensek*.

Nagy kapacitású fut, nem csak az SQL, de a tábla szegmensek frissíteni a feldolgozók között lépcsőzetesen DDL-utasítások egy fürthöz, így az elosztott tábla sémájának módosítása során.

#### <a name="distribution-column"></a>Elosztási oszlop

Nagy kapacitású algoritmikus horizontális skálázási sorok hozzárendelése szegmenseket használ. A hozzárendelés nevű tábla oszlop értékét determinisztikus módon alapján történik a *elosztási oszlop.* A fürt rendszergazdája kell kijelölnie az oszlop, egy tábla terjesztésekor.
Így a megfelelő választás fontos a teljesítmény- és funkciókat.

### <a name="type-2-reference-tables"></a>2. típus: referencia táblák

A referenciatábla olyan elosztott tábla, amelynek a teljes tartalmát is koncentrált be egyetlen szegmens. A szegmens minden feldolgozón, a rendszer replikálja, így minden worker-lekérdezéseket is hozzáférhetnek a helyileg, a referenciaadatok hálózati sorok kér egy másik csomópontjára anélkül. Referencia táblák nem elosztási oszlop van, mert a nem kell különbséget tenni a külön szegmensekben minden egyes sorára vonatkozóan.

Referencia táblák általában kicsi, és az összes munkavégző csomóponton futó lekérdezések számára adatok tárolására szolgálnak. Például a felsorolt értékeket, mint a rendelési állapotot vagy termékkategóriák.

### <a name="type-3-local-tables"></a>3. típus: helyi táblák

Nagy kapacitású használatakor a koordinátor-csomópont csatlakozik a rendszeres PostgreSQL-adatbázis. A koordinátor a szokványos táblák létrehozása, és nem szeretné a szegmensben őket.

Jó jelöltnek számít egy helyi táblák kisméretű felügyeleti táblák, amely nem szerepelhet join lekérdezéseket lenne. Például a felhasználók tábla az alkalmazás bejelentkezési és hitelesítési.

## <a name="shards"></a>A szegmensek

Az előző szakaszban leírtak szerint az elosztott táblák tárolni szegmensek a munkavégző csomópontok. Ez a szakasz további elmerülhet beolvasása.

A `pg_dist_shard` a koordinátor a metaadatok tábláját minden elosztott tábla minden egyes szegmens egy sort tartalmaz a rendszerben. A sor megfelel kivonat szóközzel (shardminvalue, shardmaxvalue) egész számok számos egy szegmens azonosítója:

```sql
SELECT * from pg_dist_shard;
 logicalrelid  | shardid | shardstorage | shardminvalue | shardmaxvalue 
---------------+---------+--------------+---------------+---------------
 github_events |  102026 | t            | 268435456     | 402653183
 github_events |  102027 | t            | 402653184     | 536870911
 github_events |  102028 | t            | 536870912     | 671088639
 github_events |  102029 | t            | 671088640     | 805306367
 (4 rows)
```

Ha a koordinátor-csomópont szeretné meghatározni, melyik szilánk tárolja egy sor `github_events`, kivonatolja a sorban az elosztási oszlop értékét, és ellenőrzi, melyik szilánk\'s tartomány tartalmaz a kivonatolt érték. (A tartományok van megadva, hogy a kivonatolási függvény képe a különálló union.)

### <a name="shard-placements"></a>Elhelyezés a szegmens

Tegyük fel, hogy adott szegmenst 102027 társítva az adott sort. A sor lesz írható és olvasható nevezett táblázat `github_events_102027` a dolgozók egyikében. Melyik worker? Amely határozza meg teljesen a metaadat-táblát, és a feldolgozó a szegmensben leképezése a szegmens az úgynevezett *elhelyezési*.

A koordinátor-csomóponton, például az adott táblákra hivatkozó töredék átírja a lekérdezések `github_events_102027`, és ezeket a töredékeket futtatja a megfelelő feldolgozókon. Íme egy példa a lekérdezés futtatása a szegmens Azonosítóját 102027 birtokló csomópont található a háttérben.

```sql
SELECT
    shardid,
    node.nodename,
    node.nodeport
FROM pg_dist_placement placement
JOIN pg_dist_node node
  ON placement.groupid = node.groupid
 AND node.noderole = 'primary'::noderole
WHERE shardid = 102027;
```

    ┌─────────┬───────────┬──────────┐
    │ shardid │ nodename  │ nodeport │
    ├─────────┼───────────┼──────────┤
    │  102027 │ localhost │     5433 │
    └─────────┴───────────┴──────────┘

## <a name="next-steps"></a>További lépések
- Ismerje meg, hogyan [elosztási oszlop kiválasztása](concepts-hyperscale-choose-distribution-column.md) elosztott táblák
