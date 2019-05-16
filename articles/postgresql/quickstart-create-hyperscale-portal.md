---
title: Azure Database for PostgreSQL – nagy kapacitású (Citus) (előzetes verzió) a rövid útmutató
description: 'Gyors útmutató: létrehozása és lekérdezése elosztott táblák, Azure database for PostgreSQL, nagy kapacitású (Citus) (előzetes verzió).'
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.custom: mvc
ms.topic: quickstart
ms.date: 05/14/2019
ms.openlocfilehash: efc3801ab03f739761a41bec754f975fe43dcd8e
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2019
ms.locfileid: "65757521"
---
# <a name="quickstart-create-an-azure-database-for-postgresql---hyperscale-citus-preview-in-the-azure-portal"></a>Gyors útmutató: Hozzon létre egy Azure Database for PostgreSQL – nagy kapacitású (Citus) (előzetes verzió) az Azure Portalon

Az Azure Database for PostgreSQL egy felügyelt szolgáltatás, amely lehetővé teszi magas rendelkezésre állású PostgreSQL-adatbázisok futtatását, felügyeletét és skálázását a felhőben. Ez a rövid útmutató bemutatja, hogyan hozhat létre egy Azure Database for PostgreSQL – nagy kapacitású (Citus) (előzetes verzió) kiszolgáló csoportot az Azure portal használatával. Vizsgáljuk meg, hogy elosztott adatokon: a horizontális skálázás táblák történik a csomópontokon, mintául szolgáló adatok bevitele, és több csomóponton végrehajtott lekérdezések futtatása.

[!INCLUDE [azure-postgresql-hyperscale-create-db](../../includes/azure-postgresql-hyperscale-create-db.md)]

## <a name="create-and-distribute-tables"></a>Létrehozása és terjesztése a táblák

Miután csatlakozott a psql használatával nagy kapacitású koordinátor-csomóponton, néhány alapvető feladatot is elvégezheti.

Nagy kapacitású belüli kiszolgálók van háromféle táblák:

- Elosztott vagy horizontálisan particionált táblák (terjednek a teljesítmény és a párhuzamos feldolgozás méretezés segítségével)
- Referencia táblák (több példányban karbantartása)
- Helyi táblákkal (gyakran használt belső felügyeleti táblák)

Ez a rövid útmutatóban elsősorban az fogunk összpontosítani elosztott táblák és az első ismeri azokat.

Az adatmodell fogunk dolgozni használata egyszerű: a GitHub felhasználói és az esemény adatait. Események közé az elágazásában létrehozása, a git véglegesíti az egy szervezet, és egyéb kapcsolódó.

Miután csatlakozott a psql-jén keresztül, hozzuk létre a táblázatok. A psql-konzol futtatása:

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

A `payload` mezőjében `github_events` egy JSONB datatype adattípusa. JSONB a JSON-adattípus szerepel a Postgres bináris formátum. Az adattípus egyszerűen egy rugalmas séma tárolásához egy oszlopban.

Postgres hozhat létre egy `GIN` index, típus, amely indexeli-e minden kulcs és a benne lévő érték. Az index, válik a gyors és egyszerű lekérdezési rendelkező különböző körülmények között. Most pedig hozzon létre néhány indexek, hogy az adatok betöltése előtt. A psql-jének:

```sql
CREATE INDEX event_type_index ON github_events (event_type);
CREATE INDEX payload_index ON github_events USING GIN (payload jsonb_path_ops);
```

Ezután azt fogja Postgres táblák igénybe a koordinátor-csomóponton, és mondja el nagy kapacitású szegmensre azokat a feldolgozók között. Ehhez egy lekérdezést minden táblához, a kulcs szegmensre fogjuk futtatni azt az. Ebben a példában az események és a felhasználók tábla szegmens küldünk `user_id`:

```sql
SELECT create_distributed_table('github_events', 'user_id');
SELECT create_distributed_table('github_users', 'user_id');
```

Készen állunk betölteni az adatokat. A psql-jének továbbra is, rendszerhéj a fájlok letöltésére:

```sql
\! curl -O https://examples.citusdata.com/users.csv
\! curl -O https://examples.citusdata.com/events.csv
```

Ezután az adatok betöltése az a fájlokat az elosztott táblák:

```sql
\copy github_events from 'events.csv' WITH CSV
\copy github_users from 'users.csv' WITH CSV
```

## <a name="run-queries"></a>Lekérdezések futtatása

Most a visszatöltött azt is, valójában a lekérdezések futtatásának részt. Kezdjük az egyszerű `count (*)` mennyi adatot betöltése sikerült megtekintéséhez:

```sql
SELECT count(*) from github_events;
```

Szépen működőképes. Térjen vissza a rendezési kicsit az összesítés, egyelőre most nézzük meg néhány lekérdezést. A JSONB belül `payload` oszlop van egy jó kis adatokat, de, eseménytípus alapján változik. `PushEvent` események olyan méretnél, amely tartalmazza a leküldés egyedi véglegesítések számát tartalmazza. Véglegesítések száma óránként összesített számának használhatók:

```sql
SELECT date_trunc('hour', created_at) AS hour,
       sum((payload->>'distinct_size')::int) AS num_commits
FROM github_events
WHERE event_type = 'PushEvent'
GROUP BY hour
ORDER BY hour;
```

Eddig vesz részt a lekérdezések rendelkezik a github\_események kizárólag, de hogy kombinálhatja ezeket az információkat github\_felhasználók. Óta, hogy horizontálisan skálázott felhasználóinak és események az azonos azonosítóval (`user_id`), az egyező felhasználói azonosítók mindkét tábla sorait lesz [védelmicsoport-készletek](https://docs.citusdata.com/en/stable/sharding/data_modeling.html#colocation) ugyanazon adatbázis-csomópont, és könnyedén összekapcsolható a szabályzattípussal.

Ha azt csatlakozzon `user_id`, nagy kapacitású is leküldhetik az illesztési végrehajtási párhuzamos végrehajtás szegmensekre munkavégző csomóponton. Például keressük meg felhasználók számára létrehozott tárházak legnagyobb száma:

```sql
SELECT login, count(*)
FROM github_events ge
JOIN github_users gu
ON ge.user_id = gu.user_id
WHERE event_type = 'CreateEvent' AND
      payload @> '{"ref_type": "repository"}'
GROUP BY login
ORDER BY count(*) DESC;
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az előző lépésekben létrehozott egy kiszolgálócsoport Azure-erőforrásokat. Ha várhatóan nincs ezekre az erőforrásokra a későbbiekben szüksége, a kiszolgálócsoport törlése. Nyomja le az **törlése** gombra a **áttekintése** a kiszolgálócsoport oldalán. Amikor a rendszer kéri, egy előugró oldalon, erősítse meg a kiszolgálói csoport nevére, majd kattintson az utolsó **törlése** gombra.

## <a name="next-steps"></a>További lépések

Ebből a gyors útmutatóból megtudhatta, hogyan lehet kiépíteni egy nagy kapacitású (Citus) kiszolgálócsoport. A psql-jének csatlakozik, létrehozott egy sémát és elosztott adatok.

Ezután az oktatóanyag egy méretezhető, több-bérlős alkalmazások létrehozásához.
> [!div class="nextstepaction"]
> [Több-Bérlős adatbázis tervezése](https://aka.ms/hyperscale-tutorial-multi-tenant)
