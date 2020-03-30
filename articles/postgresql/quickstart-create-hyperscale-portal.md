---
title: Elosztott táblák létrehozása - Nagy kapacitású (Citus) - Azure Database for PostgreSQL
description: Gyorsútmutató az elosztott táblák létrehozásához és lekérdezéséhez az Azure Database for PostgreSQL Hyperscale (Citus) adatbázisában.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.custom: mvc
ms.topic: quickstart
ms.date: 05/14/2019
ms.openlocfilehash: 02e009e6fff2e717693d1579d409199ab179d941
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "79241513"
---
# <a name="quickstart-create-an-azure-database-for-postgresql---hyperscale-citus-in-the-azure-portal"></a>Rövid útmutató: Azure-adatbázis létrehozása a PostgreSQL-hez – Hiperscale (Citus) az Azure Portalon

Az Azure Database for PostgreSQL egy felügyelt szolgáltatás, amely lehetővé teszi magas rendelkezésre állású PostgreSQL-adatbázisok futtatását, felügyeletét és skálázását a felhőben. Ez a rövid útmutató bemutatja, hogyan hozhat létre egy Azure Database for PostgreSQL – Hyperscale (Citus) kiszolgálócsoportot az Azure Portal használatával. A következő elosztott adatokat fogja feltárni: táblák csomópontok közötti szilánkolása, mintaadatok betöltése és több csomóponton futó lekérdezések.

[!INCLUDE [azure-postgresql-hyperscale-create-db](../../includes/azure-postgresql-hyperscale-create-db.md)]

## <a name="create-and-distribute-tables"></a>Táblák létrehozása és elosztása

Miután csatlakozott a psql használatával a nagy kapacitású koordinátor csomóponthoz, elvégezhet néhány alapvető feladatot.

A nagy kapacitású kiszolgálókon belül háromféle tábla létezik:

- Elosztott vagy szilánkos táblák (szétosztva a teljesítmény és a párhuzamosítás méretezésének elősegítésére)
- Referenciatáblák (több példányban karbantartott)
- Helyi táblák (gyakran használják belső rendszergazdai táblázatokhoz)

Ebben a rövid útmutatóban elsősorban az elosztott táblákra összpontosítunk, és megismerjük őket.

Az adatmodell, amelyet dolgozni fogunk, egyszerű: felhasználói és eseményadatok a GitHubról. Az események közé tartozik a villa létrehozása, a git véglegesítése egy szervezethez kapcsolódóan, és így tovább.

Miután csatlakozott a psql-en keresztül, hozzuk létre asztalainkat. A psql konzol futás:

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

A `payload` mező `github_events` JSONB adattípussal rendelkezik. A JSONB a JSON-adattípus bináris formában a Postgres-ben. Az adattípus megkönnyíti a rugalmas séma egyetlen oszlopban való tárolását.

A postgres `GIN` létrehozhat egy indexet az adott típuson, amely indexeli az összes kulcsot és értéket. Egy index, ez lesz a gyors és könnyű lekérdezni a hasznos teher különböző feltételek mellett. Menjünk előre, és hozzon létre egy pár indexek, mielőtt betöltjük az adatokat. A psql:

```sql
CREATE INDEX event_type_index ON github_events (event_type);
CREATE INDEX payload_index ON github_events USING GIN (payload jsonb_path_ops);
```

Ezután fogjuk azokat a Postgres táblákat a koordinátor csomóponton, és megmondjuk a Hyperscale-nek, hogy a dolgozók között szilánkosodjanak. Ehhez minden táblához lefuttatunk egy lekérdezést, amely megadja azt a kulcsot, amelyen szilánkot szeretne adni. Az aktuális példában az események és a felhasználók `user_id`táblázatát is shard-ot fogjuk sújtani:

```sql
SELECT create_distributed_table('github_events', 'user_id');
SELECT create_distributed_table('github_users', 'user_id');
```

Készen állunk az adatok betöltésére. A psql még, shell ki letölteni a fájlokat:

```sql
\! curl -O https://examples.citusdata.com/users.csv
\! curl -O https://examples.citusdata.com/events.csv
```

Ezután töltse be az adatokat a fájlokból az elosztott táblákba:

```sql
SET CLIENT_ENCODING TO 'utf8';

\copy github_events from 'events.csv' WITH CSV
\copy github_users from 'users.csv' WITH CSV
```

## <a name="run-queries"></a>Lekérdezések futtatása

Most itt az ideje a szórakoztató rész, valójában fut néhány lekérdezést. Kezdjük egy egyszerű, `count (*)` hogy mennyi adatot töltöttünk be:

```sql
SELECT count(*) from github_events;
```

Amit munkás gondosan. Majd visszatér, hogy egy fajta összesítés egy kicsit, de most nézzük meg néhány más lekérdezések. A JSONB `payload` oszlopban van egy jó kis adat, de az esemény típusától függően változik. `PushEvent`események olyan méretet tartalmaznak, amely tartalmazza a leküldéses különböző véglegesítések számát. Használhatjuk, hogy megtaláljuk az óránkénti véglegesítések teljes számát:

```sql
SELECT date_trunc('hour', created_at) AS hour,
       sum((payload->>'distinct_size')::int) AS num_commits
FROM github_events
WHERE event_type = 'PushEvent'
GROUP BY hour
ORDER BY hour;
```

Eddig a lekérdezések kizárólag a\_github-eseményeket érintették, de\_ezt az információt kombinálhatjuk a github-felhasználókkal. Mivel a felhasználókat és az eseményeket`user_id`is ugyanazon az azonosítón ( sántikáltuk), a két egyező felhasználói azonosítóval rendelkező tábla sorai ugyanazon az adatbázis-csomóponton lesznek [elhelyezve,](https://docs.citusdata.com/en/stable/sharding/data_modeling.html#colocation) és könnyen összenyithatók.

Ha csatlakozunk, `user_id`a hiperskálázás lelökheti az illesztési végrehajtást a feldolgozócsomópontokon párhuzamosan végrehajtásra szolgáló szegmensekbe. Például keressük meg azokat a felhasználókat, akik a legtöbb adattárat hozták létre:

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

Az előző lépésekben azure-erőforrásokat hozott létre egy kiszolgálócsoportban. Ha a jövőben nem várható, hogy szüksége lesz ezekre az erőforrásokra, törölje a kiszolgálócsoportot. Nyomja **meg** a Törlés gombot a **kiszolgálócsoport Áttekintés** lapján. Amikor a rendszer előugró lapon kéri, erősítse meg a kiszolgálócsoport nevét, és kattintson a végső **Törlés** gombra.

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban megtanulta, hogyan építhet ki egy nagykapacitású (Citus) kiszolgálócsoportot. A psql-rel kapcsolódott hozzá, létrehozott egy sémát és elosztott adatokat.

Ezután kövesse az oktatóanyagot a méretezhető több-bérlős alkalmazások létrehozásához.
> [!div class="nextstepaction"]
> [Több-bérlős adatbázis tervezése](https://aka.ms/hyperscale-tutorial-multi-tenant)
