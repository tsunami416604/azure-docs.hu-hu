---
title: Az Azure Database for PostgreSQL kiszolgáló fogalmak
description: Ez a cikk a szempontokat és irányelveket konfigurálása és kezelése az Azure Database for PostgreSQL-kiszolgálók tartalmaz.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: d03cfd49887adf1f6a4650e374d3e13eeca735a4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65077470"
---
# <a name="table-colocation-in-azure-database-for-postgresql--hyperscale-citus-preview"></a>Tábla közös elhelyezés az Azure Database for PostgreSQL – nagy kapacitású (Citus) (előzetes verzió)

A közös elhelyezés azt jelenti, hogy kapcsolódó adatok tárolására együtt ugyanazon a csomóponton. Lekérdezések meg gyors, ha minden szükséges adatot hálózati forgalmat nélkül érhető el. Kapcsolódó adatok közös elhelyezése a különböző csomópontokon lehetővé teszi, hogy a lekérdezések futtatására párhuzamosan minden egyes csomóponton.

## <a name="data-colocation-for-hash-distributed-tables"></a>Adatok közös elhelyezés kivonatoló elosztott táblák

A nagy kapacitású egy sor tárolt bérlőtartomány Ha kivonatát a terjesztési oszlopban lévő értéknek a szegmens kivonatoló tartományba esik. Szegmens ugyanazzal a tartománnyal kivonat mindig kerülnek ugyanazon a csomóponton. Az egyenlő leosztáshoz oszlopértékek sorok olyan mindig ugyanazon a csomóponton táblákat.

![A szegmensek](media/concepts-hyperscale-colocation/colocation-shards.png)

## <a name="a-practical-example-of-colocation"></a>A közös elhelyezés gyakorlati példa

Vegye figyelembe a következő táblázatok, amelyek egy több-bérlős webes elemzési SaaS része lehet:

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

Most szeretnénk választ ki a ügyfél felé irányuló irányítópult, például a lekérdezések: "Látogatások számának visszaadása az elmúlt héten kezdve minden lap" / blog "hat-bérlőben."

Az adatok egy egyetlen kiszolgálós telepítéshez lehetőség volt, a lekérdezést, számos SQL által kínált relációs műveletek használatával egyszerűen azt sikerült express:

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

Amíg a [munkakészletének](https://en.wikipedia.org/wiki/Working_set) esetében ez a lekérdezés elfér a memóriában, egy egyetlen kiszolgálós táblában egy megfelelő megoldással. Azonban vegyünk a méretezés az adatmodellt, nagy kapacitású központi telepítési beállítással a lehetőségeket.

### <a name="distributing-tables-by-id"></a>Definíciófrissítés-terjesztés táblák azonosító alapján

Egyetlen kiszolgálóból álló lekérdezések indítsa el a bérlők számának és az egyes bérlők számára tárolt adatok növekedésével lelassul. A munkakészlet a memóriában igyekeznek leáll, és a Processzor szűk keresztmetszetté válik.

Az adatok szegmensek ebben az esetben nagy kapacitású használatával több csomópont is. Az első és legfontosabb választás, győződjön meg arról, ha horizontális skálázási az elosztási oszlop kell. Kezdjük használatával natív többféle `event_id` esemény táblához és `page_id` számára a `page` tábla:

```sql
-- naively use event_id and page_id as distribution columns

SELECT create_distributed_table('event', 'event_id');
SELECT create_distributed_table('page', 'page_id');
```

Adatokat a rendszer különböző feldolgozók összes adatát, ha most nem lehet elvégezni az illesztés, mint hogy egy PostgreSQL-csomóponton. Ehelyett azt kell kiadása két lekérdezést:

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

Ezt követően a két lépésből származó eredmények kell az alkalmazás által kombinálhatók.

A lekérdezések futtatásának kell tekintse meg a többi részén Elszórva csomópont között szegmensekre adatokat.

![hatékony lekérdezések](media/concepts-hyperscale-colocation/colocation-inefficient-queries.png)

Ebben az esetben az adatok terjesztési jelentős hátrányokkal hoz létre:

-   Többletterhelést az egyes szegmensek több lekérdezés futtatása lekérdezése
-   Sok sor visszaadása az ügyfél Q1 gyűjteményértékelést kelljen végezni
-   2\. negyedévi nagy lenni
-   A kell lekérdezéseket írni több lépésben kell módosítani az alkalmazásban

Az adatokat a rendszer szétosztja között, mivel a lekérdezések párhuzamosíthatók a is. Azt azonban csak előnyös-e munkamennyiség, amelyet a lekérdezés lényegesen nagyobb, mint a terhelés több horizontális partícióra lekérdezését.

### <a name="distributing-tables-by-tenant"></a>Bérlő által táblák terjesztése

A nagy kapacitású azonos elosztási oszlop értékű sorok garantáltan ugyanazon a csomóponton. Kezdje újra, létrehozhatjuk a táblák `tenant_id` elosztási oszlopot.

```sql
-- co-locate tables by using a common distribution column
SELECT create_distributed_table('event', 'tenant_id');
SELECT create_distributed_table('page', 'tenant_id', colocate_with => 'event');
```

Most már nagy kapacitású segítségével választ kaphat az eredeti egyetlen kiszolgálóból álló lekérdezés (V1) módosítás nélkül:

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

Szűrő és tenant_id összekapcsolását nagy kapacitású tudja, hogy a teljes lekérdezést választ, amelyek tartalmazzák az adatokat, hogy az adott bérlő ugyanarra a szalagra szegmensek használatával. Egyetlen PostgreSQL csomópont segítségével választ kaphat a lekérdezés egyetlen lépésben.

![jobb lekérdezési](media/concepts-hyperscale-colocation/colocation-better-query.png)

Bizonyos esetekben lekérdezések és táblasémákat meg kell változtatni a bérlői azonosító egyedi korlátozások és a feltételek csatlakozzon. Ez azonban általában egy egyszerű módosítást.

## <a name="next-steps"></a>További lépések

- Tekintse meg, hogyan bérlő adatai a felhőátjárónak-e a [több-bérlős oktatóanyag](tutorial-design-database-hyperscale-multi-tenant.md)
