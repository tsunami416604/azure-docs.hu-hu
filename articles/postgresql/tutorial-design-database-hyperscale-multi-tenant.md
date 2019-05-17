---
title: Egy több-bérlős adatbázis az Azure Database for PostgreSQL – nagy kapacitású (Citus) (előzetes verzió) megtervezése oktatóanyag
description: Ez az oktatóanyag bemutatja, hogyan hozhat létre, feltölti és lekérdezése az elosztott táblák, Azure database for PostgreSQL, nagy kapacitású (Citus) (előzetes verzió).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.custom: mvc
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 05/14/2019
ms.openlocfilehash: 73d7aebf3dbff59320e0ef92cbd54811503c71b4
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2019
ms.locfileid: "65792277"
---
# <a name="tutorial-design-a-multi-tenant-database-by-using-azure-database-for-postgresql--hyperscale-citus-preview"></a>Oktatóanyag: Azure Database for PostgreSQL – nagy kapacitású (Citus) (előzetes verzió) használatával a több-bérlős adatbázis tervezése

Ebben az oktatóanyagban használja Azure Database for PostgreSQL – nagy kapacitású (Citus) (előzetes verzió) megtudhatja, hogyan lehet:

> [!div class="checklist"]
> * Hozzon létre egy nagy kapacitású (Citus) kiszolgálócsoportot
> * Hozzon létre egy sémát a psql segédprogram használatával
> * Szilánkleképezés táblák csomópont
> * Mintaadatok betöltése
> * Bérlő adatokat lekérdezni.
> * Bérlők közötti adatmegosztást
> * A séma bérlőnkénti testreszabása

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [azure-postgresql-hyperscale-create-db](../../includes/azure-postgresql-hyperscale-create-db.md)]

## <a name="use-psql-utility-to-create-a-schema"></a>Hozzon létre egy sémát a psql segédprogram használatával

Az Azure-adatbázis PostgreSQL - hez csatlakozó nagy kapacitású (Citus) (előzetes verzió) psql, használatával hajthatja végre néhány alapvető feladatot. Ez az oktatóanyag végigvezeti egy webalkalmazást, amely lehetővé teszi a reklámcégeknek, nyomon követheti a kampányok létrehozását.

Több vállalatot is használhatják az alkalmazást, ezért hozzunk létre egy táblát, amely a vállalatok és a egy másik, a kampányok tárolja. A psql-konzolon, futtassa a következő parancsokat:

```sql
CREATE TABLE companies (
  id bigserial PRIMARY KEY,
  name text NOT NULL,
  image_url text,
  created_at timestamp without time zone NOT NULL,
  updated_at timestamp without time zone NOT NULL
);

CREATE TABLE campaigns (
  id bigserial,
  company_id bigint REFERENCES companies (id),
  name text NOT NULL,
  cost_model text NOT NULL,
  state text NOT NULL,
  monthly_budget bigint,
  blacklisted_site_urls text[],
  created_at timestamp without time zone NOT NULL,
  updated_at timestamp without time zone NOT NULL,

  PRIMARY KEY (company_id, id)
);
```

Minden egyes kampány futtatásához ads fizet. Adja hozzá a táblát, hirdetések, a következő kód futtatásával psql a fenti kód után:

```sql
CREATE TABLE ads (
  id bigserial,
  company_id bigint,
  campaign_id bigint,
  name text NOT NULL,
  image_url text,
  target_url text,
  impressions_count bigint DEFAULT 0,
  clicks_count bigint DEFAULT 0,
  created_at timestamp without time zone NOT NULL,
  updated_at timestamp without time zone NOT NULL,

  PRIMARY KEY (company_id, id),
  FOREIGN KEY (company_id, campaign_id)
    REFERENCES campaigns (company_id, id)
);
```

Végül a kattintások és az egyes hirdetések esetében benyomások statisztikája fogja nyomon:

```sql
CREATE TABLE clicks (
  id bigserial,
  company_id bigint,
  ad_id bigint,
  clicked_at timestamp without time zone NOT NULL,
  site_url text NOT NULL,
  cost_per_click_usd numeric(20,10),
  user_ip inet NOT NULL,
  user_data jsonb NOT NULL,

  PRIMARY KEY (company_id, id),
  FOREIGN KEY (company_id, ad_id)
    REFERENCES ads (company_id, id)
);

CREATE TABLE impressions (
  id bigserial,
  company_id bigint,
  ad_id bigint,
  seen_at timestamp without time zone NOT NULL,
  site_url text NOT NULL,
  cost_per_impression_usd numeric(20,10),
  user_ip inet NOT NULL,
  user_data jsonb NOT NULL,

  PRIMARY KEY (company_id, id),
  FOREIGN KEY (company_id, ad_id)
    REFERENCES ads (company_id, id)
);
```

A psql-jének most már a táblák listáját az újonnan létrehozott táblák futtatásával tekintheti meg:

```postgres
\dt
```

Több-bérlős alkalmazások kényszerítheti az egyediség csak bérlőnként, ezért az összes elsődleges és külső kulcsok tartalmazza a vállalati azonosítót.

## <a name="shard-tables-across-nodes"></a>Szilánkleképezés táblák csomópont

Nagy kapacitású központi telepítés a felhasználó által megadott oszlop értékei alapján különböző csomópontokon táblasorokat tárolja. A "elosztási oszlop" jeleket, melyik bérlőhöz tulajdonában van, hogy mely sorokat.

Állítsa az elosztási oszlopot vállalati\_azonosító, a bérlőazonosító. A psql-jének ezek a függvények futtatása:

```sql
SELECT create_distributed_table('companies',   'id');
SELECT create_distributed_table('campaigns',   'company_id');
SELECT create_distributed_table('ads',         'company_id');
SELECT create_distributed_table('clicks',      'company_id');
SELECT create_distributed_table('impressions', 'company_id');
```

## <a name="ingest-sample-data"></a>Mintaadatok betöltése

Külső psql, a normál parancssorban mintaadatkészleteinek letöltése:

```bash
for dataset in companies campaigns ads clicks impressions geo_ips; do
  curl -O https://examples.citusdata.com/mt_ref_arch/${dataset}.csv
done
```

Vissza a psql, belül tömegesen betölteni az adatokat. Győződjön meg arról, psql futtatásához ugyanabban a könyvtárban, ahová letöltötte az adatfájlokat.

```sql
\copy companies from 'companies.csv' with csv
\copy campaigns from 'campaigns.csv' with csv
\copy ads from 'ads.csv' with csv
\copy clicks from 'clicks.csv' with csv
\copy impressions from 'impressions.csv' with csv
```

Ezek az adatok most már elosztva feldolgozó csomópontokat.

## <a name="query-tenant-data"></a>Bérlő adatokat lekérdezni.

Ha az alkalmazás egy adott bérlő kér adatokat, az adatbázis is hajtsa végre a lekérdezést egy egyetlen munkavégző csomóponton. Egybérlős lekérdezések szűrjön rá egy egyetlen bérlő azonosítója. Ha például az alábbi lekérdezés a szűrők `company_id = 5` hirdetések és kapcsolatos benyomásairól. Próbálja meg futtatni azt a psql-jének kattintva megjelenítjük az eredményeket.

```sql
SELECT a.campaign_id,
       RANK() OVER (
         PARTITION BY a.campaign_id
         ORDER BY a.campaign_id, count(*) desc
       ), count(*) as n_impressions, a.id
  FROM ads as a
  JOIN impressions as i
    ON i.company_id = a.company_id
   AND i.ad_id      = a.id
 WHERE a.company_id = 5
GROUP BY a.campaign_id, a.id
ORDER BY a.campaign_id, n_impressions desc;
```

## <a name="share-data-between-tenants"></a>Bérlők közötti adatmegosztást

Mostanáig minden tábla rendelkezik lett-e terjesztve `company_id`, de bizonyos adatok nem természetes módon "" minden bérlőhöz tartozik különösen, és megoszthatók. Például az a példában ad platformon minden vállalat érdemes a saját IP-címek alapján célközönség földrajzi információk.

Hozzon létre egy táblát, megosztott földrajzi információk tárolására. A psql-jének futtassa a következő parancsokat:

```sql
CREATE TABLE geo_ips (
  addrs cidr NOT NULL PRIMARY KEY,
  latlon point NOT NULL
    CHECK (-90  <= latlon[0] AND latlon[0] <= 90 AND
           -180 <= latlon[1] AND latlon[1] <= 180)
);
CREATE INDEX ON geo_ips USING gist (addrs inet_ops);
```

Ezután ellenőrizze `geo_ips` "referenciatábla" másolatot készíteni a tábla összes munkavégző csomóponton.

```sql
SELECT create_reference_table('geo_ips');
```

Betöltés, a példaadatokat. Ne feledje, a parancs futtatásához a psql származó a könyvtárán belül található, ahová letöltötte az adatkészlet.

```sql
\copy geo_ips from 'geo_ips.csv' with csv
```

A geo-kattintással tábla\_IP-címek az összes csomóponton hatékony.
Íme a Mindenki, aki az ad-ben lehetőségre kattintott, a helyeken található illesztés
290. Próbálja meg futtatni a lekérdezést a psql-jének.

```sql
SELECT c.id, clicked_at, latlon
  FROM geo_ips, clicks c
 WHERE addrs >> c.user_ip
   AND c.company_id = 5
   AND c.ad_id = 290;
```

## <a name="customize-the-schema-per-tenant"></a>A séma bérlőnkénti testreszabása

Minden egyes bérlőhöz szükség lehet a mások által nincs szükség speciális információk tárolására. Azonban az összes bérlőre közös infrastruktúra megosztása egy azonos adatbázissémát. Hová is a további adatokat?

Egy fontos, hogy egy nyílt oszloptípussal, például a PostgreSQL JSONB.  A séma rendelkezik JSONB mező `clicks` nevű `user_data`.
Egy cég (például: öt), az oszlop segítségével nyomon követheti a mobileszközökön a felhasználó szerepel-e.

A következő lekérdezést a számára kattint, további: mobil, vagy a hagyományos látogatók.

```sql
SELECT
  user_data->>'is_mobile' AS is_mobile,
  count(*) AS count
FROM clicks
WHERE company_id = 5
GROUP BY user_data->>'is_mobile'
ORDER BY count DESC;
```

Azt is optimalizálhatja a lekérdezés egy vállalat létrehozásával egy [részleges index](https://www.postgresql.org/docs/current/static/indexes-partial.html).

```sql
CREATE INDEX click_user_data_is_mobile
ON clicks ((user_data->>'is_mobile'))
WHERE company_id = 5;
```

Általánosságban, létrehozhatunk egy [GIN indexek](https://www.postgresql.org/docs/current/static/gin-intro.html) minden kulcs és az oszlopban lévő értéket.

```sql
CREATE INDEX click_user_data
ON clicks USING gin (user_data);

-- this speeds up queries like, "which clicks have
-- the is_mobile key present in user_data?"

SELECT id
  FROM clicks
 WHERE user_data ? 'is_mobile'
   AND company_id = 5;
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az előző lépésekben létrehozott egy kiszolgálócsoport Azure-erőforrásokat. Ha várhatóan nincs ezekre az erőforrásokra a későbbiekben szüksége, a kiszolgálócsoport törlése. Nyomja le az *törlése* gombra a *áttekintése* a kiszolgálócsoport oldalán. Amikor a rendszer kéri, egy előugró oldalon, erősítse meg a kiszolgálói csoport nevére, majd kattintson az utolsó *törlése* gombra.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtanulta, hogyan helyezhet üzembe egy nagy kapacitású (Citus) kiszolgálócsoport. A psql-jének csatlakozik, létrehozott egy sémát és elosztott adatok. Megtanulta, hogyan belül és bérlők között, és testre szabhatja a séma bérlőnként adatokat lekérdezni.

Ezt követően ismerje meg a nagy kapacitású kapcsolatos fogalmakat.
> [!div class="nextstepaction"]
> [Nagy kapacitású csomóponttípusok](https://aka.ms/hyperscale-concepts)
