---
title: 'Oktatóanyag: Valós idejű irányítópult tervezése - Hiperskála (Citus) - Azure Database for PostgreSQL'
description: Ez az oktatóanyag bemutatja, hogyan hozhat létre, népethet fel és kérdezheti le az elosztott táblákat az Azure Database for PostgreSQL Hyperscale (Citus) adatbázisában.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.custom: mvc
ms.topic: tutorial
ms.date: 05/14/2019
ms.openlocfilehash: f4eeb646de8b68c2c8d30586d0c75cece5317e40
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "76716327"
---
# <a name="tutorial-design-a-real-time-analytics-dashboard-by-using-azure-database-for-postgresql--hyperscale-citus"></a>Oktatóanyag: Valós idejű elemzési irányítópult tervezése az Azure Database for PostgreSQL – Hyperscale (Citus) használatával

Ebben az oktatóanyagban az Azure Database for PostgreSQL - Hyperscale (Citus) segítségével megtudhatja, hogyan:

> [!div class="checklist"]
> * Rugalmas skálázási (Citus) kiszolgálócsoport létrehozása
> * Séma létrehozása a psql segédprogrammal
> * Szilánktáblák csomópontok között
> * Mintaadatok létrehozása
> * Összesítések végrehajtása
> * Nyers és összesített adatok lekérdezése
> * Lejárati adatok

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [azure-postgresql-hyperscale-create-db](../../includes/azure-postgresql-hyperscale-create-db.md)]

## <a name="use-psql-utility-to-create-a-schema"></a>Séma létrehozása a psql segédprogrammal

Miután csatlakozott az Azure Database for PostgreSQL - Hyperscale (Citus) psql használatával, elvégezhet néhány alapvető feladatot. Ez az oktatóanyag végigvezeti a webes elemzések forgalmi adatainak betöltésén, majd az adatok gördülékenyítése az adatok on-t tartalmazó valós idejű irányítópultok biztosításához.

Hozzunk létre egy táblázatot, amely felhasználja az összes nyers webes forgalmi adatokat. Futtassa a következő parancsokat a psql terminálon:

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

Létrehozunk egy táblázatot is, amely megtartja a percenkénti aggregátumokat, és egy táblázatot, amely fenntartja az utolsó összesítésünk pozícióját. Futtassa a következő parancsokat a psql-ben is:

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

Láthatjuk az újonnan létrehozott táblák a táblázatok listájában most ezzel a psql paranccsal:

```postgres
\dt
```

## <a name="shard-tables-across-nodes"></a>Szilánktáblák csomópontok között

A nagy kapacitású központi telepítés tárolja a táblasorokat a különböző csomópontokon a felhasználó által kijelölt oszlop értéke alapján. Ez a "terjesztési oszlop" azt jelzi, hogy az adatok hogyan vannak szilánkosan a csomópontok között.

Állítsuk be a terjesztési oszlopot helyazonosítóként,\_a szegmenskulcsot. A psql-ben futtassa a következő függvényeket:

  ```sql
SELECT create_distributed_table('http_request',      'site_id');
SELECT create_distributed_table('http_request_1min', 'site_id');
```

## <a name="generate-sample-data"></a>Mintaadatok létrehozása

Most a szervercsoport készen kell áll arra, hogy betöltése néhány adatot. A következő helyi adatokat `psql` futtathatjuk a kapcsolatunkból, hogy folyamatosan beszúrjuk az adatokat.

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

A lekérdezés másodpercenként körülbelül nyolc sort szúr be. A sorok at különböző munkavégző csomópontokon tárolja a felosztási oszlop utasításai szerint. `site_id`

   > [!NOTE]
   > Hagyja futni az adatgenerálási lekérdezést, és nyisson meg egy második psql-kapcsolatot az oktatóanyag többi parancsához.
   >

## <a name="query"></a>Lekérdezés

A nagy kapacitású üzemeltetési beállítás lehetővé teszi, hogy több csomópont a lekérdezések párhuzamosan a sebesség. Az adatbázis például kiszámítja az összesítéseket, például a SZUM és a DARAB munkavégző csomópontokon, és az eredményeket egy végső válaszba egyesíti.

Íme egy lekérdezés a webes kérelmek percenkénti számlálásához néhány statisztikával együtt.
Próbálja meg futtatni a psql-ben, és figyelje meg az eredményeket.

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

## <a name="rolling-up-data"></a>Adatok kinagyulása

Az előző lekérdezés jól működik a korai szakaszban, de a teljesítmény romlik, mint az adatok méretezése. Még az elosztott feldolgozással is gyorsabb az adatok előzetes kiszámítása, mint ismételt újraszámítása.

Biztosíthatjuk, hogy irányítópultunk gyors maradjon, ha rendszeresen összesíti a nyers adatokat egy összesített táblába. Kísérletezhet az összesítés időtartamával. Egy percenkénti összesítési táblázatot használtunk, de az adatokat 5, 15 vagy 60 percre bonthatja.

Ahhoz, hogy ezt a roll-up könnyebben, fogunk tenni, hogy egy plpgsql funkciót. Futtassa ezeket a `rollup_http_request` parancsokat a psql-ben a függvény létrehozásához.

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

A funkció a helyén, végre, hogy összesítse az adatokat:

```sql
SELECT rollup_http_request();
```

Az adatok előre összesített formában történő lekérdezésével lekérdezhetjük az összesítő táblát, hogy ugyanazt a jelentést kapjuk, mint korábban. Futtassa az alábbi lekérdezést:

```sql
SELECT site_id, ingest_time as minute, request_count,
       success_count, error_count, average_response_time_msec
  FROM http_request_1min
 WHERE ingest_time > date_trunc('minute', now()) - '5 minutes'::interval;
 ```

## <a name="expiring-old-data"></a>Lejáró régi adatok

Az összesítések gyorsabbá teszik a lekérdezéseket, de a nem kötött tárolási költségek elkerülése érdekében továbbra is le kell járnunk a régi adatokat. Döntse el, hogy mennyi ideig szeretné megőrizni az egyes részletességre vonatkozó adatokat, és a lejárt adatok törléséhez szabványos lekérdezésekkel törölje. A következő példában úgy döntöttünk, hogy a nyers adatokat egy napig, a percösszesítéseket pedig egy hónapig őrizzük meg:

```sql
DELETE FROM http_request WHERE ingest_time < now() - interval '1 day';
DELETE FROM http_request_1min WHERE ingest_time < now() - interval '1 month';
```

Éles környezetben ezeket a lekérdezéseket egy függvénybe csomagolhatja, és egy cron-feladatban percenként meghívhatja.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az előző lépésekben azure-erőforrásokat hozott létre egy kiszolgálócsoportban. Ha a jövőben nem várható, hogy szüksége lesz ezekre az erőforrásokra, törölje a kiszolgálócsoportot. Nyomja *meg* a Törlés gombot a *kiszolgálócsoport Áttekintés* lapján. Amikor a rendszer előugró lapon kéri, erősítse meg a kiszolgálócsoport nevét, és kattintson a végső *Törlés* gombra.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtanulta, hogyan építhetki ki egy nagykapacitású (Citus) kiszolgálócsoportot. A psql-rel kapcsolódott hozzá, létrehozott egy sémát és elosztott adatokat. Megtanultuk az adatok lekérdezését a nyers űrlapon, rendszeresen összesíteni az adatokat, lekérdezni az összesített táblákat, és lejárni a régi adatokat.

Ezután ismerje meg a hiperskála fogalmait.
> [!div class="nextstepaction"]
> [Nagy kapacitású csomóponttípusok](https://aka.ms/hyperscale-concepts)
