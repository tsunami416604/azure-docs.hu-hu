---
title: A kiszolgálói fogalmak a Azure Database for PostgreSQL
description: Ez a cikk a Azure Database for PostgreSQL-kiszolgálók konfigurálásával és kezelésével kapcsolatos szempontokat és irányelveket ismerteti.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: 533958221898b620500b7363f3710f75f155934a
ms.sourcegitcommit: 4b8a69b920ade815d095236c16175124a6a34996
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/23/2019
ms.locfileid: "69998048"
---
# <a name="table-colocation-in-azure-database-for-postgresql--hyperscale-citus"></a>Táblázatos elhelyezés Azure Database for PostgreSQLban – nagy kapacitású (Citus)

A közös elhelyezés azt jelenti, hogy a kapcsolódó információk tárolása ugyanazon csomópontokon történik. A lekérdezések gyorsak lehetnek, ha az összes szükséges adatok hálózati forgalom nélkül elérhetők. A különböző csomópontokon található kapcsolódó adat-elhelyezés lehetővé teszi, hogy a lekérdezések hatékonyan, párhuzamosan fussanak az egyes csomópontokon.

## <a name="data-colocation-for-hash-distributed-tables"></a>Adattárolási helyek kivonatoláshoz – elosztott táblák

Azure Database for PostgreSQL – nagy kapacitású (Citus) előzetes verzióban a sor egy szegmensben tárolódik, ha a terjesztési oszlopban lévő érték kivonata a szegmens kivonatoló tartományán belülre esik. Az ugyanazzal a kivonatoló tartománnyal rendelkező szegmensek mindig ugyanarra a csomópontra kerülnek. Az egyenlő terjesztési oszlop értékekkel rendelkező sorok mindig ugyanazon a csomóponton vannak a táblák között.

![Szilánkok](media/concepts-hyperscale-colocation/colocation-shards.png)

## <a name="a-practical-example-of-colocation"></a>Gyakorlati példa az egyhelyes elhelyezésre

Vegye figyelembe a következő, több-bérlős webes elemzési SaaS részét képező táblázatokat:

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

Most szeretnénk megválaszolni azokat a lekérdezéseket, amelyeket egy ügyfélhez kapcsolódó irányítópulton lehet kibocsátani. Egy példa a "/blog" kezdetű oldalról a hatodik bérlőn "a látogatások számának visszaadása az elmúlt héten" kifejezésre.

Ha az adatok egykiszolgálós üzembe helyezési lehetőséggel voltak, könnyedén kihasználhatjuk a lekérdezését az SQL által kínált, széles körű kapcsolatok használatával:

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

Amíg a lekérdezés munkakészlete elfér a memóriában, egy egykiszolgálós tábla megfelelő megoldás. [](https://en.wikipedia.org/wiki/Working_set) Tekintsük át az adatmodell méretezésének lehetőségeit a nagy kapacitású (Citus) telepítési lehetőséggel.

### <a name="distribute-tables-by-id"></a>Táblák elosztása azonosító alapján

Az egykiszolgálós lekérdezések lelassulnak, mivel a bérlők száma és az egyes bérlők által tárolt adatmennyiség növekszik. A munkakészlet leáll a memóriában, és a CPU szűk keresztmetszetet eredményez.

Ebben az esetben a nagy kapacitású (Citus) segítségével több csomóponton is eloszthatja az adatmennyiséget. Az első és legfontosabb választás, amikor a szegmensre dönt, a terjesztési oszlop. Kezdjük egy naiv választási lehetőséggel `event_id` az Event tábla és `page_id` a `page` táblázat számára:

```sql
-- naively use event_id and page_id as distribution columns

SELECT create_distributed_table('event', 'event_id');
SELECT create_distributed_table('page', 'page_id');
```

Ha az adatok különböző munkatársak között oszlanak el, nem tudunk olyan illesztést végezni, mint egy PostgreSQL-csomóponton. Ehelyett két lekérdezést kell kiadnia:

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

Ezt követően a két lépés eredményeit össze kell kapcsolni az alkalmazással.

A lekérdezések futtatásához a csomópontokon elszórt szegmensekben lévő adatszegmensekben kell megkeresni az adategységeket.

![Nem hatékony lekérdezések](media/concepts-hyperscale-colocation/colocation-inefficient-queries.png)

Ebben az esetben az adateloszlás jelentős hátrányait hozza létre:

-   Az egyes szegmensek lekérdezésével és több lekérdezés futtatásával járó terhelés.
-   A Q1 terhelése sok sort ad vissza az ügyfélnek.
-   A Q2 nagy méretűvé válik.
-   A lekérdezéseknek több lépésben való írásához szükség van az alkalmazás változásaira.

Az adateloszlás megoszlik, így a lekérdezések párhuzamosak lehetnek. Ez csak akkor hasznos, ha a lekérdezés által végzett munka mennyisége jelentősen meghaladja a sok szegmens lekérdezésének terhelését.

### <a name="distribute-tables-by-tenant"></a>Táblák terjesztése bérlő szerint

A nagy kapacitású (Citus) esetében az azonos terjesztési oszlop értékével rendelkező sorok garantáltan ugyanazon a csomóponton lehetnek. A kezdéshez a táblázatokat `tenant_id` a terjesztési oszlopként is létrehozhatja.

```sql
-- co-locate tables by using a common distribution column
SELECT create_distributed_table('event', 'tenant_id');
SELECT create_distributed_table('page', 'tenant_id', colocate_with => 'event');
```

A now nagy kapacitású (Citus) az eredeti egykiszolgálós lekérdezést módosítás nélkül válaszolja meg (Q1):

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

A szűrés és a tenant_id-hez való csatlakozás miatt a nagy kapacitású (Citus) tudja, hogy a teljes lekérdezés válaszolhat az adott bérlő adatait tartalmazó, közösen elhelyezett szegmensek készletével. Egyetlen PostgreSQL-csomópont egyetlen lépésben válaszolhat a lekérdezésre.

![Jobb lekérdezés](media/concepts-hyperscale-colocation/colocation-better-query.png)

Bizonyos esetekben a lekérdezéseket és a táblázatos sémákat úgy kell módosítani, hogy a bérlői azonosítót egyedi korlátozásokkal és csatlakozási feltételekkel tartalmazzák. Ez a módosítás általában egyszerű.

## <a name="next-steps"></a>További lépések

- Tekintse meg, hogyan találhatók a bérlői információk a [több-bérlős oktatóanyagban](tutorial-design-database-hyperscale-multi-tenant.md).
