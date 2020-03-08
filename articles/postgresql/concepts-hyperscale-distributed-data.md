---
title: Elosztott adatforgalom – nagy kapacitású (Citus) – Azure Database for PostgreSQL
description: Ismerje meg az elosztott táblákat, a táblákat, a helyi táblákat és a szegmenseket Azure Database for PostgreSQLban.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: ade7632dc042741a07bdb59e34e30b3fb464e0e9
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78363420"
---
# <a name="distributed-data-in-azure-database-for-postgresql--hyperscale-citus"></a>Elosztott adatforgalom Azure Database for PostgreSQLban – nagy kapacitású (Citus)

Ez a cikk a Azure Database for PostgreSQL – nagy kapacitású (Citus) három típusú táblázatát ismerteti.
Azt mutatja be, hogyan tárolódnak az elosztott táblák a szegmensek között, és hogy a szegmensek hogyan legyenek elhelyezve a csomópontokon.

## <a name="table-types"></a>Táblák típusai

A nagy kapacitású-(Citus-) kiszolgálócsoport három típusa van, amelyek mindegyike különböző célokra szolgál.

### <a name="type-1-distributed-tables"></a>1\. típus: elosztott táblák

Az első típus és a leggyakoribb érték az elosztott táblák. Úgy tűnik, hogy az SQL-utasításoknak normális táblák, de horizontálisan particionálva vannak a munkavégző csomópontok között. Ez azt jelenti, hogy a tábla sorait különböző csomópontok tárolják, a szilánkok nevű töredék táblákban.

A nagy kapacitású (Citus) nem csak az SQL-, hanem a DDL-utasításokat futtatja a fürtben.
Az elosztott táblák sémájának módosítása a tábla összes munkaterületének a feldolgozók közötti frissítéséhez.

#### <a name="distribution-column"></a>Terjesztési oszlop

A nagy kapacitású (Citus) algoritmusos horizontális skálázást használ a sorok szegmensekhez rendeléséhez. A hozzárendelés determinisztikus módon a Distribution oszlop nevű Table oszlop értéke alapján történik. A fürt rendszergazdájának meg kell jelölnie ezt az oszlopot egy tábla terjesztésekor.
A megfelelő választás a teljesítmény és a funkcionalitás szempontjából fontos.

### <a name="type-2-reference-tables"></a>2\. típus: hivatkozási táblák

A hivatkozási tábla olyan elosztott tábla, amelynek teljes tartalma egyetlen szegmensbe van koncentrálva. A szegmens minden feldolgozón replikálódik. A munkavégzők lekérdezései helyileg is hozzáférhetnek a hivatkozási információkhoz anélkül, hogy egy másik csomóponttól származó sorokat kellene megkérniük. A hivatkozási táblák nem rendelkeznek terjesztési oszlopokkal, mert nem kell Megkülönböztetniük a különálló szegmenseket egymás után.

A hivatkozási táblák általában kicsik, és a rendszer a munkavégző csomóponton futó lekérdezésekhez kapcsolódó adatokat tárolja. Ilyen például az enumerált értékek, például a megrendelés állapota vagy a termékkategóriák.

### <a name="type-3-local-tables"></a>3\. típus: helyi táblák

Ha a nagy kapacitású (Citus) használja, a csatlakozáshoz használt koordinátori csomópont egy normál PostgreSQL-adatbázis. Létrehozhat hagyományos táblákat a koordinátoron, és megadhatja, hogy ne legyenek szétválasztva.

A helyi táblák jó jelöltje olyan kis adminisztrációs táblák, amelyek nem vesznek részt a csatlakoztatási lekérdezésekben. Ilyen például az alkalmazások bejelentkezésének és hitelesítésének felhasználói táblázata.

## <a name="shards"></a>Szilánkok

Az előző szakasz azt ismerteti, hogy az elosztott táblák hogyan tárolódnak a munkavégző csomópontokon. Ez a szakasz a technikai részleteket ismerteti.

A koordinátor `pg_dist_shard` metaadat-táblázata tartalmaz egy sort a rendszer minden egyes elosztott táblájához. A sor egy egész számmal rendelkező szegmens-AZONOSÍTÓra illeszkedik egy kivonatoló térben (shardminvalue, shardmaxvalue).

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

Ha a koordinátor-csomópont szeretné meghatározni, hogy melyik szegmens tartalmaz egy `github_events`sort, akkor az a sorban lévő eloszlás oszlop értékét kivonata. Ezután a csomópont ellenőrzi, hogy a szegmens\'s tartománya tartalmazza-e a kivonatos értéket. A tartományok úgy vannak meghatározva, hogy a kivonatoló függvény képe a különálló Unió legyen.

### <a name="shard-placements"></a>Szilánkok

Tegyük fel, hogy a szegmens 102027 a kérdéses sorhoz van társítva. A sor olvasása vagy írása egy `github_events_102027` nevű táblában történik az egyik feldolgozóban. Melyik feldolgozót? Ezt teljes mértékben a metaadat-táblázatok határozzák meg. A szegmensek feldolgozóra való hozzárendelését nevezzük a szilánkok elhelyezésének.

A koordinátor csomópontja olyan töredékekre írja a lekérdezéseket, amelyek az adott táblákra hivatkoznak, például az `github_events_102027`ra, és futtatja ezeket a töredékeket a megfelelő munkatársain. Íme egy példa arra, hogy egy lekérdezés a háttérben fusson, hogy megtalálja a csomópontot, amely a 102027-es szegmens AZONOSÍTÓját tárolja.

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

## <a name="next-steps"></a>Következő lépések
- Megtudhatja, hogyan [választhat terjesztési oszlopot](concepts-hyperscale-choose-distribution-column.md) az elosztott táblákhoz.
