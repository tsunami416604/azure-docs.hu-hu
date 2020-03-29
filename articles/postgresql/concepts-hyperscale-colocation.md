---
title: Tábla közös elhelyezése - Nagykapacitású (Citus) - Azure-adatbázis a PostgreSQL-hez
description: A kapcsolódó információk együtt tárolása a gyorsabb lekérdezésekhez
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: 7e4073ec45f4c21f33d20924a9948e72f961c7f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74967337"
---
# <a name="table-colocation-in-azure-database-for-postgresql--hyperscale-citus"></a>Tábla közös elhelyezése az Azure Database for PostgreSQL -Hyperscale (Citus)

A közös elhelyezés azt jelenti, hogy a kapcsolódó információkat ugyanazon a csomóponton tárolják. A lekérdezések gyorsan haladhatnak, ha az összes szükséges adat elérhető hálózati forgalom nélkül. A kapcsolódó adatok különböző csomópontokon történő együttfoglalása lehetővé teszi, hogy a lekérdezések hatékonyan fussanak párhuzamosan az egyes csomópontokon.

## <a name="data-colocation-for-hash-distributed-tables"></a>A kivonatoló kosztott táblák közös elhelyezése

Az Azure Database for PostgreSQL – Hyperscale (Citus) egy sor egy szegmensben tárolódik, ha a terjesztési oszlopban szereplő érték kivonata a shard kivonattartományába esik. Az azonos kivonattartományú szilánkok mindig ugyanarra a csomópontra kerülnek. Az egyenlő eloszlásoszlop-értékekkel rendelkező sorok mindig ugyanazon a csomóponton vannak a táblák között.

![Szilánkok](media/concepts-hyperscale-colocation/colocation-shards.png)

## <a name="a-practical-example-of-colocation"></a>Gyakorlati példa a helymegosztásra

Vegye figyelembe az alábbi táblázatokat, amelyek egy több-bérlős webanalitikai SaaS részét vehetik:

```sql
CREATE TABLE event (
  tenant_id int,
  event_id bigint,
  page_id int,
  payload jsonb,
  primary key (tenant_id, event_id)
);

CREATE TABLE page (
  tenant_id int,
  page_id int,
  path text,
  primary key (tenant_id, page_id)
);
```

Most szeretnénk válaszolni az ügyfelekre néző irányítópult által esetleg kiadott lekérdezésekre. Példa lekérdezés "Adja vissza a látogatások száma az elmúlt héten az összes oldal kezdve "/ blog" a bérlő hat."

Ha az adataink az egykiszolgálós telepítési lehetőségben vannak, az SQL által kínált relációs műveletek gazdag készletével könnyedén kifejezhetjük a lekérdezésünket:

```sql
SELECT page_id, count(event_id)
FROM
  page
LEFT JOIN  (
  SELECT * FROM event
  WHERE (payload->>'time')::timestamptz >= now() - interval '1 week'
) recent
USING (tenant_id, page_id)
WHERE tenant_id = 6 AND path LIKE '/blog%'
GROUP BY page_id;
```

Mindaddig, amíg a lekérdezés [munkakészlete](https://en.wikipedia.org/wiki/Working_set) elfér a memóriában, az egykiszolgálós tábla megfelelő megoldás. Nézzük meg az adatmodell méretezésének lehetőségeit a Nagykapacitású (Citus) telepítési lehetőséggel.

### <a name="distribute-tables-by-id"></a>Táblák elosztása azonosító szerint

Az egykiszolgálós lekérdezések a bérlők számának növekedésével elindulnak. A munkakészlet nem illeszkedik a memóriába, és a CPU szűk keresztmetszetté válik.

Ebben az esetben a Hyperscale (Citus) használatával számos csomóponton szilánkosan is lenyomhatjuk az adatokat. Az első és legfontosabb választás, amit meg kell hoznunk, amikor úgy döntünk, hogy a szegmens a terjesztési oszlop. Kezdjük egy naiv választás segítségével `event_id` az esemény tábla és `page_id` az `page` asztal:

```sql
-- naively use event_id and page_id as distribution columns

SELECT create_distributed_table('event', 'event_id');
SELECT create_distributed_table('page', 'page_id');
```

Ha az adatok különböző dolgozók között vannak szétszórva, nem tudunk úgy csatlakozni, mint ha egyetlen PostgreSQL-csomóponton lennénk. Ehelyett két lekérdezést kell kibocsátanunk:

```sql
-- (Q1) get the relevant page_ids
SELECT page_id FROM page WHERE path LIKE '/blog%' AND tenant_id = 6;

-- (Q2) get the counts
SELECT page_id, count(*) AS count
FROM event
WHERE page_id IN (/*…page IDs from first query…*/)
  AND tenant_id = 6
  AND (payload->>'time')::date >= now() - interval '1 week'
GROUP BY page_id ORDER BY count DESC LIMIT 10;
```

Ezt követően a két lépés eredményeit az alkalmazásnak kombinálnia kell.

A lekérdezések futtatásához a csomópontok között szétszórt szegmensekben lévő adatokat kell megtekintenie.

![Nem hatékony lekérdezések](media/concepts-hyperscale-colocation/colocation-inefficient-queries.png)

Ebben az esetben az adatelosztás jelentős hátrányokat okoz:

-   Az egyes szegmensek lekérdezése és több lekérdezés futtatása többletterheléssel.
-   A Q1 terhelése sok sort ad vissza az ügyfélnek.
-   Q2 lesz nagy.
-   A lekérdezések több lépésben történő írásának szükségessége az alkalmazás módosítását igényli.

Az adatok szétszórva vannak, így a lekérdezések párhuzamosak lehetnek. Ez csak akkor hasznos, ha a munka mennyisége, hogy a lekérdezés nem lényegesen nagyobb, mint a terhelési sok szegmensek lekérdezése.

### <a name="distribute-tables-by-tenant"></a>Táblák elosztása bérlő szerint

A Nagykapacitás (Citus) területen az azonos eloszlásoszlop-értékkel rendelkező sorok garantáltan ugyanazon a csomóponton vannak. Az újrakezdéssel létrehozhatjuk `tenant_id` a táblákat terjesztési oszlopként.

```sql
-- co-locate tables by using a common distribution column
SELECT create_distributed_table('event', 'tenant_id');
SELECT create_distributed_table('page', 'tenant_id', colocate_with => 'event');
```

Most hyperscale (Citus) képes válaszolni az eredeti egykiszolgálós lekérdezés módosítás nélkül (Q1):

```sql
SELECT page_id, count(event_id)
FROM
  page
LEFT JOIN  (
  SELECT * FROM event
  WHERE (payload->>'time')::timestamptz >= now() - interval '1 week'
) recent
USING (tenant_id, page_id)
WHERE tenant_id = 6 AND path LIKE '/blog%'
GROUP BY page_id;
```

A tenant_id szűrése és illesztése miatt a Nagykapacitás (Citus) tudja, hogy a teljes lekérdezés válaszolható az adott bérlő adatait tartalmazó közös helyen lévő szilánkok készletével. Egyetlen PostgreSQL-csomópont egyetlen lépésben válaszolhat a lekérdezésre.

![Jobb lekérdezés](media/concepts-hyperscale-colocation/colocation-better-query.png)

Bizonyos esetekben a lekérdezéseket és a táblasémákat úgy kell módosítani, hogy a bérlői azonosítót egyedi megkötésekbe és illesztési feltételekbe foglalják. Ez a változás általában egyszerű.

## <a name="next-steps"></a>További lépések

- Tekintse meg, hogyan találhatók a bérlői adatok a [több-bérlős oktatóanyagban.](tutorial-design-database-hyperscale-multi-tenant.md)
