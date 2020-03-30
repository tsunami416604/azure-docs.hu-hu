---
title: Elosztott adatok – Nagykapacitású (Citus) – Azure-adatbázis a PostgreSQL-hez
description: Ismerje meg az elosztott táblák, referenciatáblák, helyi táblák és szilánkok az Azure Database for PostgreSQL.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: ade7632dc042741a07bdb59e34e30b3fb464e0e9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79243652"
---
# <a name="distributed-data-in-azure-database-for-postgresql--hyperscale-citus"></a>Elosztott adatok az Azure Database for PostgreSQL -For PostgreSQL - Hyperscale (Citus)

Ez a cikk ismerteti a három táblatípus az Azure Database for PostgreSQL – Hyperscale (Citus).
Azt mutatja be, hogyan osztott táblák szegmensekként tárolja, és a módját, hogy a szilánkok kerülnek a csomópontokon.

## <a name="table-types"></a>Táblázattípusok

Egy nagy méretű (Citus) kiszolgálócsoportban háromféle tábla létezik, amelyek mindegyike különböző célokra használatos.

### <a name="type-1-distributed-tables"></a>1. típus: Elosztott táblák

Az első típus és a leggyakoribb az elosztott táblák. Úgy tűnik, hogy az SQL-utasítások normál táblái, de vízszintesen vannak felosztva a feldolgozócsomópontok között. Ez azt jelenti, hogy a tábla sorai különböző csomópontokon, a szilánkok nevű töredéktáblákban tárolódnak.

A nagy kapacitású (Citus) nem csak SQL, hanem DDL-utasítások fut a fürtön.
Az elosztott tábla sémájának módosítása a tábla összes szegmensének frissítéséhez a dolgozók között.

#### <a name="distribution-column"></a>Elosztás oszlop

A nagyméretű (Citus) algoritmikus horizontális skálázást használ a sorok szegmensekhez rendeléséhez. A hozzárendelés determinisztikusan történik a terjesztési oszlopnak nevezett táblaoszlop értéke alapján. A fürtrendszergazdának ezt az oszlopot kell kijelölnie a tábla terjesztésekor.
A helyes választás fontos a teljesítmény és a funkcionalitás érdekében.

### <a name="type-2-reference-tables"></a>2. típus: Referenciatáblák

A referenciatábla olyan elosztott tábla, amelynek teljes tartalma egyetlen szegmensbe van koncentrálva. A szegmens replikálódik minden dolgozón. Lekérdezések bármely feldolgozó elérheti a referencia-információkat helyileg, anélkül, hogy a hálózati terhelést kér sorokat egy másik csomóponttól. A referenciatáblák nak nincs eloszlásoszlopa, mert nem szükséges különbséget tenni a soronkénti külön szegmensek között.

A referenciatáblák általában kicsik, és olyan adatok tárolására szolgálnak, amelyek bármely munkavégző csomóponton futó lekérdezésekhez relevánsak. Egy példa a számba vett értékek, például a rendelési állapotok vagy a termékkategóriák.

### <a name="type-3-local-tables"></a>3. típus: Helyi asztalok

A Hyperscale (Citus) használataesetén a koordinátori csomópont, amelyhez csatlakozik, egy rendszeres PostgreSQL-adatbázis. Létrehozhat rendes táblákat a koordinátoron, és dönthet úgy, hogy nem mántikálja őket.

A helyi táblák jó jelöltje kis felügyeleti táblák lennének, amelyek nem vesznek részt illesztési lekérdezésekben. Erre példa az alkalmazásbejelentkezés és -hitelesítés felhasználói táblája.

## <a name="shards"></a>Szilánkok

Az előző szakasz ismerteti, hogyan osztott táblák tárolódnak szegmensek feldolgozó csomópontokon. Ez a rész további technikai részleteket tárgyal.

A `pg_dist_shard` koordinátor metaadat-táblája a rendszer minden egyes elosztott táblájának minden szegmenséhez tartalmaz egy sort. A sor egy shard-azonosítót egy kivonattérben lévő egész számokkal (shardminvalue, shardmaxvalue) egyezik.

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

Ha a koordinátor csomópont meg szeretné `github_events`határozni, hogy melyik shard rendelkezik egy sorával, akkor kiírja a terjesztési oszlop értékét a sorban. Ezután a csomópont ellenőrzi, hogy melyik szegmenstartomány\'tartalmazza a kivonatolt értéket. A tartományok úgy vannak definiálva, hogy a kivonatoló függvény képe a különálló egységük.

### <a name="shard-placements"></a>Shard elhelyezések

Tegyük fel, hogy az 102027-es shard a kérdéses sorhoz van társítva. A sor olvasása vagy írása az egyik dolgozó által hívott `github_events_102027` táblában történik. Melyik munkás? Ezt teljes egészében a metaadattáblák határozzák meg. A szegmens leképezése a feldolgozó a shard elhelyezése.

A koordinátor idrét a lekérdezéseket olyan töredékekbe írja át, amelyek az adott táblákhoz hasonlóan `github_events_102027` hivatkoznak, és ezeket a töredékeket a megfelelő dolgozókon futtatja. Íme egy példa egy lekérdezés fut a színfalak mögött, hogy megtalálja a csomópont, amely 102027-es shard-azonosítót.

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
- További információ az elosztott táblák [terjesztési oszlopának kiválasztásáról.](concepts-hyperscale-choose-distribution-column.md)
