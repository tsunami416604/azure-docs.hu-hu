---
title: Azure Database for PostgreSQL – nagy kapacitású (Citus) gyors útmutató
description: Gyors útmutató elosztott táblák létrehozásához és lekérdezéséhez Azure Database for PostgreSQL nagy kapacitású (Citus).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.custom: mvc
ms.topic: quickstart
ms.date: 05/14/2019
ms.openlocfilehash: 6b5bfbf16e76cbf90a5536332d8e3bf1035f983a
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73500077"
---
# <a name="quickstart-create-an-azure-database-for-postgresql---hyperscale-citus-in-the-azure-portal"></a>Gyors útmutató: Azure Database for PostgreSQL-nagy kapacitású (Citus) létrehozása a Azure Portal

Az Azure Database for PostgreSQL egy felügyelt szolgáltatás, amely lehetővé teszi magas rendelkezésre állású PostgreSQL-adatbázisok futtatását, felügyeletét és skálázását a felhőben. Ez a rövid útmutató bemutatja, hogyan hozhat létre Azure Database for PostgreSQL-nagy kapacitású (Citus) kiszolgálói csoportot a Azure Portal használatával. Tekintse át az elosztott adatmennyiségeket: a csomópontok közötti horizontális skálázást, a mintaadatok betöltését és a több csomóponton futtatott lekérdezések futtatását.

[!INCLUDE [azure-postgresql-hyperscale-create-db](../../includes/azure-postgresql-hyperscale-create-db.md)]

## <a name="create-and-distribute-tables"></a>Táblák létrehozása és terjesztése

Miután csatlakozott a nagy kapacitású koordinátori csomóponthoz a psql használatával, elvégezhet néhány alapvető feladatot.

A nagy kapacitású-kiszolgálókon belül háromféle tábla létezik:

- Elosztott vagy felosztott táblák (a teljesítmény és a párhuzamos skálázásának elősegítése érdekében)
- Hivatkozási táblák (több másolat is karbantartva)
- Helyi táblák (gyakran használt belső felügyeleti táblákhoz)

Ebben a rövid útmutatóban elsősorban az elosztott táblákra koncentrálunk, és ismerkedjen meg velük.

A következő adatmodell egyszerű: felhasználó-és esemény-adatok a GitHubról. Az események közé tartozik az elágazás létrehozása, a git-véglegesítés egy szervezettel kapcsolatban, és így tovább.

Miután kapcsolódott a psql-on keresztül, hozzuk létre a táblázatokat. A psql-konzolon futtassa a következőket:

```sql
CREATE TABLE github_events
(
    event_id bigint,
    event_type text,
    event_public boolean,
    repo_id bigint,
    payload jsonb,
    repo jsonb,
    user_id bigint,
    org jsonb,
    created_at timestamp
);

CREATE TABLE github_users
(
    user_id bigint,
    url text,
    login text,
    avatar_url text,
    gravatar_id text,
    display_login text
);
```

`github_events` `payload` mezője JSONB adattípussal rendelkezik. A JSONB a postgres bináris formátumú JSON-adattípusa. Az adattípussal könnyedén tárolhat egy rugalmas sémát egyetlen oszlopban.

Az postgres létrehozhat egy `GIN` indexet ezen a típuson, amely a benne található összes kulcsot és értéket indexeli. Az indextel gyorsan és egyszerűen lekérdezheti a hasznos adatokat különböző feltételekkel. Nézzük meg, és hozzon létre néhány indexet az adatbetöltése előtt. A psql-ben:

```sql
CREATE INDEX event_type_index ON github_events (event_type);
CREATE INDEX payload_index ON github_events USING GIN (payload jsonb_path_ops);
```

Ezután ezeket a postgres táblázatokat a koordinátori csomóponton fogjuk kiadni, és megmondjuk, hogy nagy kapacitású a feldolgozók között. Ehhez le kell futtatni egy lekérdezést minden olyan táblára vonatkozóan, amely megadja, hogy a kulcs a szegmensbe kerüljön. Az aktuális példában az eseményeket és a felhasználók táblát is a `user_id`juk:

```sql
SELECT create_distributed_table('github_events', 'user_id');
SELECT create_distributed_table('github_users', 'user_id');
```

Készen áll az betöltésre. A psql továbbra is kipróbálhatja a fájlok letöltését:

```sql
\! curl -O https://examples.citusdata.com/users.csv
\! curl -O https://examples.citusdata.com/events.csv
```

Ezután töltse be az adatok a fájlokból az elosztott táblákba:

```sql
SET CLIENT_ENCODING TO 'utf8';

\copy github_events from 'events.csv' WITH CSV
\copy github_users from 'users.csv' WITH CSV
```

## <a name="run-queries"></a>Lekérdezések futtatása

Itt az ideje, hogy a szórakoztató rész ténylegesen futtasson néhány lekérdezést. Kezdjük egy egyszerű `count (*)` a betöltött adatmennyiség megtekintéséhez:

```sql
SELECT count(*) from github_events;
```

Ez szépen működött. Egy kicsit vissza fogunk térni erre a fajta összesítésre, de most nézzük meg néhány más lekérdezést. A JSONB `payload` oszlopban van egy jó kis mennyiségű adattípus, de az esemény típusa alapján változhat. `PushEvent` események olyan méretet tartalmaznak, amely tartalmazza a leküldések eltérő véglegesítő számát. A felhasználható, hogy megkeresse az óránkénti véglegesítés teljes számát:

```sql
SELECT date_trunc('hour', created_at) AS hour,
       sum((payload->>'distinct_size')::int) AS num_commits
FROM github_events
WHERE event_type = 'PushEvent'
GROUP BY hour
ORDER BY hour;
```

Eddig a lekérdezések kizárólag a GitHub-\_eseményeket érintették, de ezeket az információkat a GitHub\_felhasználókkal kombináljuk. Mivel a felhasználókat és az eseményeket ugyanazon az azonosítón (`user_id`) osztottuk fel, a megfelelő felhasználói azonosítókkal rendelkező táblák sorai ugyanazon adatbázis-csomópontokon [helyezkednek el](https://docs.citusdata.com/en/stable/sharding/data_modeling.html#colocation) , és könnyen csatlakoztathatók.

Ha `user_id`csatlakozunk, a nagy kapacitású a munkavégző csomópontokon párhuzamosan végezheti el a csatlakozás végrehajtását a szegmensekre. Például keresse meg a legtöbb tárházat létrehozó felhasználókat:

```sql
SELECT gu.login, count(*)
  FROM github_events ge
  JOIN github_users gu
    ON ge.user_id = gu.user_id
 WHERE ge.event_type = 'CreateEvent'
   AND ge.payload @> '{"ref_type": "repository"}'
 GROUP BY gu.login
 ORDER BY count(*) DESC;
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az előző lépésekben Azure-erőforrásokat hozott létre egy kiszolgálócsoport számára. Ha nem várható, hogy a jövőben szüksége lesz ezekre az erőforrásokra, törölje a kiszolgálót. A kiszolgálócsoport **Áttekintés** lapján kattintson a **Törlés** gombra. Amikor a rendszer rákérdez egy előugró oldalra, erősítse meg a kiszolgálócsoport nevét, és kattintson a végleges **Törlés** gombra.

## <a name="next-steps"></a>További lépések

Ebből a rövid útmutatóból megtudhatta, hogyan építhet ki egy nagy kapacitású-(Citus-) kiszolgáló csoportot. Csatlakoztatta azt a psql-hoz, létrehozott egy sémát és egy elosztott adatkészletet.

Ezután kövessen egy oktatóanyagot a méretezhető több-bérlős alkalmazások létrehozásához.
> [!div class="nextstepaction"]
> [Több-bérlős adatbázis tervezése](https://aka.ms/hyperscale-tutorial-multi-tenant)
