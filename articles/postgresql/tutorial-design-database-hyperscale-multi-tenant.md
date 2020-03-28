---
title: 'Oktatóanyag: Több-bérlős adatbázis tervezése – Nagy kapacitású (Citus) – Azure Database for PostgreSQL'
description: Ez az oktatóanyag bemutatja, hogyan hozhat létre, népethet fel és kérdezheti le az elosztott táblákat az Azure Database for PostgreSQL Hyperscale (Citus) adatbázisában.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.custom: mvc
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 05/14/2019
ms.openlocfilehash: 17ac29de243f4abfff1cfc83fc6424799978bf0e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "74978151"
---
# <a name="tutorial-design-a-multi-tenant-database-by-using-azure-database-for-postgresql--hyperscale-citus"></a>Oktatóanyag: több-bérlős adatbázis tervezése az Azure Database for PostgreSQL – Hyperscale (Citus) használatával

Ebben az oktatóanyagban az Azure Database for PostgreSQL - Hyperscale (Citus) segítségével megtudhatja, hogyan:

> [!div class="checklist"]
> * Rugalmas skálázási (Citus) kiszolgálócsoport létrehozása
> * Séma létrehozása a psql segédprogrammal
> * Szilánktáblák csomópontok között
> * Mintaadatok betöltése
> * Bérlői adatok lekérdezése
> * Adatok megosztása a bérlők között
> * Bérlőnkénti séma testreszabása

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [azure-postgresql-hyperscale-create-db](../../includes/azure-postgresql-hyperscale-create-db.md)]

## <a name="use-psql-utility-to-create-a-schema"></a>Séma létrehozása a psql segédprogrammal

Miután csatlakozott az Azure Database for PostgreSQL - Hyperscale (Citus) psql használatával, elvégezhet néhány alapvető feladatot. Ez az oktatóanyag bemutatja egy olyan webalkalmazás létrehozását, amely lehetővé teszi a hirdetők számára kampányaik nyomon követését.

Az alkalmazást több vállalat is használhatja, ezért hozzunk létre egy táblázatot a vállalatok és egy másik kampányaik tárolására. A psql konzolon futtassa a következő parancsokat:

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

Minden kampány fizet a hirdetések megjelenítéséért. Adjon hozzá egy táblázatot a hirdetésekhez is, a következő kódot futtatva a psql-ben a fenti kód után:

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

Végül nyomon követjük az egyes hirdetésekkel kapcsolatos kattintásokra és megjelenítésekre vonatkozó statisztikákat:

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

Az újonnan létrehozott táblákat a psql-ben lévő táblák listájában a következő futással láthatja:

```postgres
\dt
```

A több-bérlős alkalmazások csak bérlőnként kényszeríthetik ki az egyediséget, ezért minden elsődleges és idegen kulcs tartalmazza a vállalati azonosítót.

## <a name="shard-tables-across-nodes"></a>Szilánktáblák csomópontok között

A nagy kapacitású központi telepítés tárolja a táblasorokat a különböző csomópontokon a felhasználó által kijelölt oszlop értéke alapján. Ez a "terjesztési oszlop" azt jelzi, hogy melyik bérlő melyik sor a tulajdonosa.

Állítsuk be a terjesztési oszlopot vállalati\_azonosítónak, a bérlőazonosítónak. A psql-ben futtassa a következő függvényeket:

```sql
SELECT create_distributed_table('companies',   'id');
SELECT create_distributed_table('campaigns',   'company_id');
SELECT create_distributed_table('ads',         'company_id');
SELECT create_distributed_table('clicks',      'company_id');
SELECT create_distributed_table('impressions', 'company_id');
```

## <a name="ingest-sample-data"></a>Mintaadatok betöltése

A psql-en kívül most, a normál parancssorban töltse le a mintaadatkészleteket:

```bash
for dataset in companies campaigns ads clicks impressions geo_ips; do
  curl -O https://examples.citusdata.com/mt_ref_arch/${dataset}.csv
done
```

Vissza belül psql, tömeges betölteni az adatokat. Ügyeljen arra, hogy a psql-t ugyanabban a könyvtárban futtassa, ahol az adatfájlokat letöltötte.

```sql
SET CLIENT_ENCODING TO 'utf8';

\copy companies from 'companies.csv' with csv
\copy campaigns from 'campaigns.csv' with csv
\copy ads from 'ads.csv' with csv
\copy clicks from 'clicks.csv' with csv
\copy impressions from 'impressions.csv' with csv
```

Ezek az adatok mostantól feldolgozócsomópontok között lesznek elosztva.

## <a name="query-tenant-data"></a>Bérlői adatok lekérdezése

Amikor az alkalmazás egyetlen bérlő adatait kéri, az adatbázis egyetlen munkavégző csomóponton futtathatja a lekérdezést. Egybérlős lekérdezések egyetlen bérlőazonosító szerint szűr. Például a következő `company_id = 5` lekérdezési szűrők a hirdetésekhez és a megjelenítésekhez. Próbálja meg futtatni a psql-ben, hogy lássa az eredményeket.

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

## <a name="share-data-between-tenants"></a>Adatok megosztása a bérlők között

Eddig az összes táblát a `company_id`, de néhány adat természetesen nem "tartozik" bármely bérlő, különösen, és megosztható. Előfordulhat például, hogy a példaként szereplő hirdetési platform összes vállalata az IP-címek alapján szeretne földrajzi információkat kapni a közönségszámára.

Hozzon létre egy táblázatot a megosztott földrajzi adatok tárolására. Futtassa a következő parancsokat a psql-ben:

```sql
CREATE TABLE geo_ips (
  addrs cidr NOT NULL PRIMARY KEY,
  latlon point NOT NULL
    CHECK (-90  <= latlon[0] AND latlon[0] <= 90 AND
           -180 <= latlon[1] AND latlon[1] <= 180)
);
CREATE INDEX ON geo_ips USING gist (addrs inet_ops);
```

Ezután `geo_ips` készítsen egy "referenciatáblát", amely a tábla másolatát tárolja minden munkavégző csomóponton.

```sql
SELECT create_reference_table('geo_ips');
```

Töltse be a példaadatokkal. Ne felejtse el futtatni ezt a parancsot a psql-ben a könyvtáron belülről, ahol az adatkészletet letöltötte.

```sql
\copy geo_ips from 'geo_ips.csv' with csv
```

A kattintási táblázat\_geo ips-szel való összekapcsolódása minden csomóponton hatékony.
Itt van egy illesztés, hogy megtalálja a helyét mindenkinek, aki rákattintott a hirdetésre
290. Próbálja meg futtatni a lekérdezést a psql.Try running the query in psql.

```sql
SELECT c.id, clicked_at, latlon
  FROM geo_ips, clicks c
 WHERE addrs >> c.user_ip
   AND c.company_id = 5
   AND c.ad_id = 290;
```

## <a name="customize-the-schema-per-tenant"></a>Bérlőnkénti séma testreszabása

Előfordulhat, hogy minden bérlőnek olyan speciális információkat kell tárolnia, amelyekre másoknem van szükségük. Azonban minden bérlő közös infrastruktúrával rendelkezik egy azonos adatbázissémával. Hová mehetnek az extra adatok?

Az egyik trükk az, hogy egy nyílt végű oszlop típus, mint a PostgreSQL JSONB.  A sémánkban van egy `clicks` JSONB mező a . `user_data`
Egy vállalat (például az ötös vállalat) az oszlop segítségével nyomon követheti, hogy a felhasználó mobileszközön van-e.

Az alábbiakban lekérdezősjáték: mobilvagy hagyományos látogatók.

```sql
SELECT
  user_data->>'is_mobile' AS is_mobile,
  count(*) AS count
FROM clicks
WHERE company_id = 5
GROUP BY user_data->>'is_mobile'
ORDER BY count DESC;
```

Tudjuk optimalizálni ezt a lekérdezést egy cég létrehozásával [részleges index](https://www.postgresql.org/docs/current/static/indexes-partial.html).

```sql
CREATE INDEX click_user_data_is_mobile
ON clicks ((user_data->>'is_mobile'))
WHERE company_id = 5;
```

Általánosabban, létrehozhatunk egy [GIN indexet](https://www.postgresql.org/docs/current/static/gin-intro.html) az oszlopon belüli minden kulcshoz és értékhez.

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

Az előző lépésekben azure-erőforrásokat hozott létre egy kiszolgálócsoportban. Ha a jövőben nem várható, hogy szüksége lesz ezekre az erőforrásokra, törölje a kiszolgálócsoportot. Nyomja *meg* a Törlés gombot a *kiszolgálócsoport Áttekintés* lapján. Amikor a rendszer előugró lapon kéri, erősítse meg a kiszolgálócsoport nevét, és kattintson a végső *Törlés* gombra.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtanulta, hogyan építhetki ki egy nagykapacitású (Citus) kiszolgálócsoportot. A psql-rel kapcsolódott hozzá, létrehozott egy sémát és elosztott adatokat. Megtanulta az adatok lekérdezését a bérlőkön belül és azok között, és a bérlőnkénti séma testreszabását.

Ezután ismerje meg a hiperskála fogalmait.
> [!div class="nextstepaction"]
> [Nagy kapacitású csomóponttípusok](https://aka.ms/hyperscale-concepts)
