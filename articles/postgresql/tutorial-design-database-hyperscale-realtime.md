---
title: A valós idejű irányítópultok az Azure Database for PostgreSQL – nagy kapacitású (Citus) (előzetes verzió) megtervezése oktatóanyag
description: Ez az oktatóanyag bemutatja, hogyan hozhat létre, feltölti és lekérdezése az elosztott táblák, Azure database for PostgreSQL, nagy kapacitású (Citus) (előzetes verzió).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.custom: mvc
ms.topic: tutorial
ms.date: 05/06/2019
ms.openlocfilehash: 7324ab1d7aa6e42100c9c6760c17b0ea6445f21d
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65080743"
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

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

## <a name="create-an-azure-database-for-postgresql"></a>Azure-adatbázis létrehozása PostgreSQL-hez

Kövesse az alábbi lépéseket az Azure-adatbázis PostgreSQL-kiszolgálóhoz létrehozásához:
1. Kattintson az Azure Portal bal felső sarkában található **Erőforrás létrehozása** gombra.
2. Az **Új** panelen válassza az **Adatbázisok** lehetőséget, majd az **Adatbázisok** panelen válassza az **Azure-adatbázis PostgreSQL-kiszolgálóhoz** lehetőséget.
3. Az üzembe helyezési lehetőséget választotta, kattintson a **létrehozás** gomb alatt **nagy kapacitású (Citus) kiszolgálócsoport - előzetes verzió.**
4. Adja meg az alábbi adatokat az új kiszolgálóűrlapon:
   - Erőforráscsoport: kattintson a **új létrehozása** a szövegmezőbe, a mező alatti hivatkozás. Írjon be egy nevet, például **myresourcegroup**.
   - Kiszolgálócsoport neve: **mydemoserver** (név, egy kiszolgáló, amely leképezhető DNS-nevet, és kötelező megadni globálisan egyedinek kell lennie).
   - Rendszergazdai felhasználónév: **myadmin** (azt használja később csatlakozni az adatbázishoz).
   - Jelszó: legalább nyolc karakter hosszúságúnak kell lennie, és tartalmaznia kell hármat a következő kategóriákban – angol nagybetűs karakterek, angol kisbetűs karakterek, számjegyek (0 – 9) és nem alfanumerikus karakterek (!, $, #, %, stb.)
   - Hely: tegye lehetővé számukra a lehető leggyorsabb hozzáférést biztosítsa az adatokhoz a felhasználók legközelebb eső helyet használja.

   > [!IMPORTANT]
   > A kiszolgáló itt megadott rendszergazdai bejelentkezési nevét és jelszavát kell majd használnia az oktatóanyag későbbi szakaszaiban a kiszolgálóra és az adatbázisaira való bejelentkezéshez. Jegyezze meg vagy jegyezze fel ezt az információt későbbi használatra.

5. Kattintson a **konfigurálása kiszolgálócsoport**. Ebben a szakaszban nem változik, hagyja meg a beállításokat, és kattintson a **mentése**.
6. Kattintson a **felülvizsgálat + létrehozása** , majd **létrehozás** a kiszolgáló üzembe helyezéséhez. Az üzembe helyezés eltarthat néhány percig.
7. Az oldal átirányítja központi telepítésének figyeléséhez. Amikor az élő állapota változik **az üzembe helyezés folyamatban van** való **az üzembe helyezés befejeződése**, kattintson a **kimenetek** menüpont az oldal bal oldalán található.
8. A kimenetek lap tartalmaz egy gomb mellett, hogy az érték másolása a vágólapra a koordinátor állomásnévvel. Jegyezze fel ezt az információt későbbi használatra.

## <a name="configure-a-server-level-firewall-rule"></a>Kiszolgálószintű tűzfalszabály konfigurálása

Az Azure Database for PostgreSQL szolgáltatás a kiszolgáló szintjén használja a tűzfalat. Alapértelmezés szerint a tűzfal megakadályozza, hogy külső alkalmazások és eszközök csatlakozzanak a kiszolgálóhoz vagy a kiszolgálón lévő adatbázisokhoz. Azt hozzá kell adnia egy szabályt, amely egy adott IP-címtartomány számára megnyitja a tűzfalat.

1. Az a **kimenetek** szakaszt, ahol korábban másolt a koordinátor-csomópont állomásnév, kattintson ismét a **áttekintése** menüpontot.

2. Keresse meg a méretezési csoport a központi telepítést, a lista az erőforrások, és kattintson rá. (A név lesz a következő előtaggal "sg –".)

3. Kattintson a **tűzfal** alatt **biztonsági** a bal oldali menüben.

4. Kattintson a hivatkozásra **+ Hozzáadás tűzfalszabályt az aktuális ügyfél IP-cím**. Végül kattintson a **mentése** gombra.

5. Kattintson a **Save** (Mentés) gombra.

   > [!NOTE]
   > Azure PostgreSQL-kiszolgáló az 5432-es porton keresztül kommunikál. Ha vállalati hálózaton belülről próbál csatlakozni, elképzelhető, hogy a hálózati tűzfal nem engedélyezi a kimenő forgalmat az 5432-es porton keresztül. Ebben az esetben nem tud csatlakozni az Azure SQL-adatbáziskiszolgálóhoz, ha az informatikai részleg nem nyitja meg az 5432-es portot.
   >

## <a name="connect-to-the-database-using-psql-in-cloud-shell"></a>Csatlakozás az adatbázishoz a psql használatával a Cloud Shellben

Használjuk a [psql](https://www.postgresql.org/docs/current/app-psql.html) parancssori segédprogramot az Azure Database for PostgreSQL-kiszolgálóhoz való kapcsolódáshoz.
1. Indítsa el az Azure Cloud Shell-t a felső navigációs ablakban található terminálikonnal.

   ![Azure-adatbázis PostgreSQL-hez - Azure Cloud Shell terminálikon](./media/tutorial-design-database-hyperscale-realtime/psql-cloud-shell.png)

2. Az Azure Cloud Shell megnyílik a böngészőben, amely lehetővé teszi a bash-parancsok beírását.

   ![Azure-adatbázis PostgreSQL-hez - Azure Shell Bash parancssor](./media/tutorial-design-database-hyperscale-realtime/psql-bash.png)

3. A Cloud Shell parancssornál csatlakozzon az Azure-adatbázis PostgreSQL-kiszolgálóhoz a psql-parancsok használatával. A következő formátum segítségével kapcsolódhat egy [psql](https://www.postgresql.org/docs/9.6/static/app-psql.html) segédprogrammal rendelkező Azure-adatbázis PostgreSQL-kiszolgálóhoz:
   ```bash
   psql --host=<myserver> --username=myadmin --dbname=citus
   ```

   Például a következő parancsot a nevű alapértelmezett adatbázishoz kapcsolódik **citus** PostgreSQL-kiszolgálón **mydemoserver.postgres.database.azure.com** hozzáférési hitelesítő adatok használatával. Adja meg a kiszolgáló-rendszergazdai jelszavát, amikor a rendszer kéri.

   ```bash
   psql --host=mydemoserver.postgres.database.azure.com --username=myadmin --dbname=citus
   ```

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

Hozunk létre egy táblát, amely tartalmazza majd a percenkénti összesítések és a egy táblát, amely megőrzi az utolsó kumulatív pozícióját is. Futtassa a következő psql is:

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
    PERFORM pg_sleep(random() * 0.25);
  END LOOP;
END $$;
```

A lekérdezés egy sort ad hozzá minden negyedév körülbelül másodpercenként. A sorok különböző munkavégző csomóponton vannak tárolva, útmutatása szerint az elosztási oszlop `site_id`.

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

## <a name="performing-rollups"></a>Frissítések végrehajtása

A fenti lekérdezés jól működik, a korai fázisában, de a teljesítményét csökkenti az adatok skálázását követve rugalmasan méretezhető. Még az elosztott feldolgozási a gyorsabb előre számítási ezeket az adatokat, mint újraszámítja, ismételten.

Gyors szerint összesített táblába a nyers adatok rendszeresen közbeni marad az irányítópultról is biztosítható. Ebben az esetben azt összegzi azokat az egy perces aggregációs táblában, de is sikerült rendelkezhet 5 perc, 15 perc, egy órát, és így tovább.

Mivel folyamatosan hajtunk az összesítő fogunk hozzon létre egy függvényt a végrehajtáshoz. A psql-jének létrehozásához futtassa az alábbi parancsokat a `rollup_http_request` függvény.

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

És a egy előre összesített formában az adatokat a összesítő táblát ugyanaz a jelentés első, korábban lekérdezheti azt. Futtassa a következőt:

```sql
SELECT site_id, ingest_time as minute, request_count,
       success_count, error_count, average_response_time_msec
  FROM http_request_1min
 WHERE ingest_time > date_trunc('minute', now()) - '5 minutes'::interval;
 ```

## <a name="expiring-old-data"></a>Lejáró régi adatok

A kumulatív gyorsabb, hatékonyabb lekérdezések, de továbbra is kell korlátlan tárolási költségek elkerülése érdekében a régi adatok lejárnak. Egyszerűen döntse el, hogy mennyi ideig szeretné megőrizni az adatokat az egyes részletességgel, és lekérdezéseket szokásos törli az elévült adatokat. A következő példában hogy úgy döntött, hogy a nyers adatot egy nap, de percalapú összesítések egy hónapig:

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