---
title: A valós idejű irányítópultok az Azure Database for PostgreSQL – nagy kapacitású (Citus) (előzetes verzió) megtervezése oktatóanyag
description: Ez az oktatóanyag bemutatja, hogyan hozhat létre, feltölti és lekérdezése az elosztott táblák, Azure database for PostgreSQL, nagy kapacitású (Citus) (előzetes verzió).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.custom: mvc
ms.topic: tutorial
ms.date: 05/14/2019
ms.openlocfilehash: a5e4b2073a29785ee851b2733c12d6331afe59d8
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2019
ms.locfileid: "65791310"
---
# <a name="tutorial-design-a-real-time-analytics-dashboard-by-using-azure-database-for-postgresql--hyperscale-citus-preview"></a>Oktatóanyag: A valós idejű elemzési irányítópultok tervező által az Azure Database for PostgreSQL – nagy kapacitású (Citus) (előzetes verzió) használatával

Ebben az oktatóanyagban használja Azure Database for PostgreSQL – nagy kapacitású (Citus) (előzetes verzió) megtudhatja, hogyan lehet:

> [!div class="checklist"]
> * Hozzon létre egy nagy kapacitású (Citus) kiszolgálócsoportot
> * Hozzon létre egy sémát a psql segédprogram használatával
> * Szilánkleképezés táblák csomópont
> * Mintaadatok létrehozása
> * Hajtsa végre a kumulatív
> * Nyers és összesített adatok lekérdezése
> * Adatok elévülése

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [azure-postgresql-hyperscale-create-db](../../includes/azure-postgresql-hyperscale-create-db.md)]

## <a name="use-psql-utility-to-create-a-schema"></a>Hozzon létre egy sémát a psql segédprogram használatával

Az Azure-adatbázis PostgreSQL - hez csatlakozó nagy kapacitású (Citus) (előzetes verzió) psql, használatával hajthatja végre néhány alapvető feladatot. Ez az oktatóanyag végigvezeti a webes elemzés, valós idejű irányítópultok az adatok alapján kiértékeli az adatokat, majd összesítéséhez forgalmi adatok.

Hozzunk létre egy táblát, amely felhasználja a nyers webes forgalmi adatok mindegyikét. A Terminálszolgáltatások psql-jének futtassa a következő parancsokat:

```sql
CREATE TABLE http_request (
  site_id INT,
  ingest_time TIMESTAMPTZ DEFAULT now(),

  url TEXT,
  request_country TEXT,
  ip_address TEXT,

  status_code INT,
  response_time_msec INT
);
```

Hozunk létre egy táblát, amely tartalmazza majd a percenkénti összesítések és a egy táblát, amely megőrzi az utolsó kumulatív pozícióját is. Futtassa az alábbi parancsokat a psql is:

```sql
CREATE TABLE http_request_1min (
  site_id INT,
  ingest_time TIMESTAMPTZ, -- which minute this row represents

  error_count INT,
  success_count INT,
  request_count INT,
  average_response_time_msec INT,
  CHECK (request_count = error_count + success_count),
  CHECK (ingest_time = date_trunc('minute', ingest_time))
);

CREATE INDEX http_request_1min_idx ON http_request_1min (site_id, ingest_time);

CREATE TABLE latest_rollup (
  minute timestamptz PRIMARY KEY,

  CHECK (minute = date_trunc('minute', minute))
);
```

Most már a psql parancsot a táblák listáját az újonnan létrehozott táblák megtekintéséhez:

```postgres
\dt
```

## <a name="shard-tables-across-nodes"></a>Szilánkleképezés táblák csomópont

Nagy kapacitású központi telepítés a felhasználó által megadott oszlop értékei alapján különböző csomópontokon táblasorokat tárolja. A "elosztási oszlop" jelöli meg, hogyan adatok horizontálisan Skálázott a csomópontok között.

Állítsa az elosztási oszlop helyként\_azonosító, a szegmenskulcs. A psql-jének ezek a függvények futtatása:

  ```sql
SELECT create_distributed_table('http_request',      'site_id');
SELECT create_distributed_table('http_request_1min', 'site_id');
```

## <a name="generate-sample-data"></a>Mintaadatok létrehozása

A kiszolgálócsoport most már készen áll, hogy néhány adatot kell lennie. Futtassa a következő parancsot helyileg a `psql` kapcsolat folyamatosan szúrja be az adatokat.

```sql
DO $$
  BEGIN LOOP
    INSERT INTO http_request (
      site_id, ingest_time, url, request_country,
      ip_address, status_code, response_time_msec
    ) VALUES (
      trunc(random()*32), clock_timestamp(),
      concat('http://example.com/', md5(random()::text)),
      ('{China,India,USA,Indonesia}'::text[])[ceil(random()*4)],
      concat(
        trunc(random()*250 + 2), '.',
        trunc(random()*250 + 2), '.',
        trunc(random()*250 + 2), '.',
        trunc(random()*250 + 2)
      )::inet,
      ('{200,404}'::int[])[ceil(random()*2)],
      5+trunc(random()*150)
    );
    COMMIT;
    PERFORM pg_sleep(random() * 0.25);
  END LOOP;
END $$;
```

A lekérdezés a másodpercenként körülbelül nyolc sorok beszúrása. A sorok különböző munkavégző csomóponton vannak tárolva, útmutatása szerint az elosztási oszlop `site_id`.

   > [!NOTE]
   > Hagyja meg az generációs lekérdezés fut, és nyissa meg a többi parancsot egy második psql kapcsolatot ebben az oktatóanyagban.
   >

## <a name="query"></a>Lekérdezés

A nagy kapacitású üzemeltetési lehetővé teszi, hogy több csomópont sebességének párhuzamos lekérdezések feldolgozásához. Például az adatbázis összesítések mint az összeg és száma, a feldolgozó csomópontok számítja ki, és az eredmények egyesíti a végső választ.

Íme egy lekérdezést, webes kéréseket, és néhány statisztikai percenkénti számát.
Próbálja meg futtatni a psql-jének, és vizsgálja meg az eredményeket.

```sql
SELECT
  site_id,
  date_trunc('minute', ingest_time) as minute,
  COUNT(1) AS request_count,
  SUM(CASE WHEN (status_code between 200 and 299) THEN 1 ELSE 0 END) as success_count,
  SUM(CASE WHEN (status_code between 200 and 299) THEN 0 ELSE 1 END) as error_count,
  SUM(response_time_msec) / COUNT(1) AS average_response_time_msec
FROM http_request
WHERE date_trunc('minute', ingest_time) > now() - '5 minutes'::interval
GROUP BY site_id, minute
ORDER BY minute ASC;
```

## <a name="rolling-up-data"></a>A működés közbeni adatok

Az előző lekérdezés jól működik, a korai fázisában, de a teljesítményét csökkenti az adatok skálázását követve rugalmasan méretezhető. Elosztott feldolgozási mellett is, a gyorsabb előre számítási az adatokat, mint a újraszámítja, ismételten.

Gyors szerint összesített táblába a nyers adatok rendszeresen közbeni marad az irányítópultról is biztosítható. Az összesítési időtartama is kipróbálhat. A percalapú aggregációs táblában használtuk, de Ön sikerült felosztása data 5, 15 vagy 60 perc helyette.

Az összesítő könnyebben futtatásához a fogunk helyezzük el egy plpgsql függvényt. A psql-jének létrehozásához futtassa az alábbi parancsokat a `rollup_http_request` függvény.

```sql
-- initialize to a time long ago
INSERT INTO latest_rollup VALUES ('10-10-1901');

-- function to do the rollup
CREATE OR REPLACE FUNCTION rollup_http_request() RETURNS void AS $$
DECLARE
  curr_rollup_time timestamptz := date_trunc('minute', now());
  last_rollup_time timestamptz := minute from latest_rollup;
BEGIN
  INSERT INTO http_request_1min (
    site_id, ingest_time, request_count,
    success_count, error_count, average_response_time_msec
  ) SELECT
    site_id,
    date_trunc('minute', ingest_time),
    COUNT(1) as request_count,
    SUM(CASE WHEN (status_code between 200 and 299) THEN 1 ELSE 0 END) as success_count,
    SUM(CASE WHEN (status_code between 200 and 299) THEN 0 ELSE 1 END) as error_count,
    SUM(response_time_msec) / COUNT(1) AS average_response_time_msec
  FROM http_request
  -- roll up only data new since last_rollup_time
  WHERE date_trunc('minute', ingest_time) <@
          tstzrange(last_rollup_time, curr_rollup_time, '(]')
  GROUP BY 1, 2;

  -- update the value in latest_rollup so that next time we run the
  -- rollup it will operate on data newer than curr_rollup_time
  UPDATE latest_rollup SET minute = curr_rollup_time;
END;
$$ LANGUAGE plpgsql;
```

A függvény helyben hajtsa végre, hogy az adatok összesítő:

```sql
SELECT rollup_http_request();
```

És a egy előre összesített formában az adatokat a összesítő táblát ugyanaz a jelentés első, korábban lekérdezheti azt. A következő lekérdezés futtatásával:

```sql
SELECT site_id, ingest_time as minute, request_count,
       success_count, error_count, average_response_time_msec
  FROM http_request_1min
 WHERE ingest_time > date_trunc('minute', now()) - '5 minutes'::interval;
 ```

## <a name="expiring-old-data"></a>Lejáró régi adatok

A kumulatív gyorsabb, hatékonyabb lekérdezések, de továbbra is kell korlátlan tárolási költségek elkerülése érdekében a régi adatok lejárnak. Döntse el, hogy mennyi ideig szeretné megőrizni az adatokat az egyes részletességgel, és lekérdezéseket szokásos törli az elévült adatokat. A következő példában hogy úgy döntött, hogy a nyers adatot egy nap, de percalapú összesítések egy hónapig:

```sql
DELETE FROM http_request WHERE ingest_time < now() - interval '1 day';
DELETE FROM http_request_1min WHERE ingest_time < now() - interval '1 month';
```

Éles környezetben, ezeket a lekérdezéseket zabalit do egy függvényt, és adja neki egy cron feladat percenként sikerült.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az előző lépésekben létrehozott egy kiszolgálócsoport Azure-erőforrásokat. Ha várhatóan nincs ezekre az erőforrásokra a későbbiekben szüksége, a kiszolgálócsoport törlése. Nyomja le az *törlése* gombra a *áttekintése* a kiszolgálócsoport oldalán. Amikor a rendszer kéri, egy előugró oldalon, erősítse meg a kiszolgálói csoport nevére, majd kattintson az utolsó *törlése* gombra.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtanulta, hogyan helyezhet üzembe egy nagy kapacitású (Citus) kiszolgálócsoport. A psql-jének csatlakozik, létrehozott egy sémát és elosztott adatok. Használatával adatokat lekérdezni mindkét nyers formában, a segítségével megtanulta rendszeresen összesíteni az adatokat, a lekérdezések összesített táblák és régi adatok lejárnak.

Ezt követően ismerje meg a nagy kapacitású kapcsolatos fogalmakat.
> [!div class="nextstepaction"]
> [Nagy kapacitású csomóponttípusok](https://aka.ms/hyperscale-concepts)