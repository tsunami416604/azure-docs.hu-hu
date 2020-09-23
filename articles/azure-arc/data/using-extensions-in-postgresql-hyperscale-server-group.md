---
title: PostgreSQL-bővítmények használata
description: PostgreSQL-bővítmények használata
titleSuffix: Azure Arc enabled data services
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 19b2ec283619df0cc8d3c880cb2df6f53f6fb332
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90936035"
---
# <a name="use-postgresql-extensions-in-your-azure-arc-enabled-postgresql-hyperscale-server-group"></a>PostgreSQL-bővítmények használata az Azure arc-kompatibilis PostgreSQL nagy kapacitású-kiszolgálói csoportban

A PostgreSQL a legjobb, ha bővítményekkel használja. Valójában a saját nagy kapacitású funkciójának kulcsfontosságú eleme a Microsoft által biztosított `citus` , alapértelmezés szerint telepített bővítmény, amely lehetővé teszi, hogy a postgres transzparens módon, több csomóponton keresztül lehessen átadni az adatmennyiséget.


[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="list-of-extensions"></a>Bővítmények listája
A bővítmények mellett [`contrib`](https://www.postgresql.org/docs/12/contrib.html) Az Azure arc-kompatibilis PostgreSQL nagy kapacitású-kiszolgálócsoport tárolójában lévő bővítmények listája a (z):
- `citus`, v: 9,4
- `pg_cron`, v: 1,2
- `plpgsql`, v: 1,0
- `postgis`, v: 3.0.2

Ez a lista a túlórát és a frissítéseket is közzéteszi a dokumentumban. Még nem lehetséges, hogy a fentiekben felsorolt kiterjesztéseket is hozzáadja.

Ez az útmutató a következő két bővítmény használatát teszi elérhetővé:
- [PostGIS](https://postgis.net/)
- [`pg_cron`](https://github.com/citusdata/pg_cron)


## <a name="manage-extensions"></a>Bővítmények kezelése

### <a name="enable-extensions"></a>Bővítmények engedélyezése
Ez a lépés nem szükséges a részét képező bővítményekhez `contrib` .
A bővítmények engedélyezésére szolgáló parancs általános formátuma a következő:

#### <a name="enable-an-extension-at-the-creation-time-of-a-server-group"></a>Bővítmény engedélyezése egy kiszolgálócsoport létrehozási időpontjában:
```console
azdata arc postgres server create -n <name of your postgresql server group> --extensions <extension names>
```
#### <a name="enable-an-extension-on-an-instance-that-already-exists"></a>Bővítmény engedélyezése olyan példányon, amely már létezik:
```console
azdata arc postgres server edit -n <name of your postgresql server group> --extensions <extension names>
```

#### <a name="get-the-list-of-extensions-enabled"></a>Az engedélyezett bővítmények listájának beolvasása:
Futtassa az alábbi parancs egyikét.

##### <a name="with-azdata"></a>Azdata
```console
azdata arc postgres server show -n <server group name>
```
Görgessen a kimenetben, és figyelje meg a engine\extensions fejezeteit. Például:
```console
"engine": {
      "extensions": [
        {
          "name": "citus"
        },
        {
          "name": "pg_cron"
        }
      ]
    },
```
##### <a name="with-kubectl"></a>Kubectl
```console
kubectl describe postgresql-12s/postgres02
```
Görgessen a kimenetben, és figyelje meg a engine\extensions fejezeteit. Például:
```console
Engine:
    Extensions:
      Name:  citus
      Name:  pg_cron
```


### <a name="create-extensions"></a>Bővítmények létrehozása:
Kapcsolódjon a kiszolgálói csoporthoz az Ön által választott ügyfél-eszközzel, és futtassa a standard PostgreSQL-lekérdezést:
```console
CREATE EXTENSION <extension name>;
```

### <a name="get-the-list-of-extension-created-in-your-server-group"></a>A kiszolgáló csoportjában létrehozott bővítmény listájának beolvasása:
Kapcsolódjon a kiszolgálói csoporthoz az Ön által választott ügyfél-eszközzel, és futtassa a standard PostgreSQL-lekérdezést:
```console
select * from pg_extension;
```

### <a name="drop-an-extension-from-your-server-group"></a>Bővítmény eldobása a kiszolgáló csoportjából:
Kapcsolódjon a kiszolgálói csoporthoz az Ön által választott ügyfél-eszközzel, és futtassa a standard PostgreSQL-lekérdezést:
```console
drop extension <extension name>;
```

## <a name="use-the-postgis-and-the-pg_cron-extensions"></a>A PostGIS és a Pg_cron bővítmények használata

### <a name="the-postgis-extension"></a>Az PostGIS bővítmény

Engedélyezheti a PostGIS bővítményt egy meglévő kiszolgálócsoport számára, vagy létrehozhat egy újat a bővítmény már engedélyezve van:

**Bővítmény engedélyezése egy kiszolgálócsoport létrehozási időpontjában:**
```console
azdata arc postgres server create -n <name of your postgresql server group> --extensions <extension names>

#Example:
azdata arc postgres server create -n pg2 -w 2 --extensions postgis
```

**A bővítmény engedélyezése olyan példányon, amely már létezik:**
```console
azdata arc postgres server edit -n <name of your postgresql server group> --extensions <extension names>

#Example:
azdata arc postgres server edit --extensions postgis -n pg2
```

A bővítmények telepítésének ellenőrzéséhez használja a következő standard PostgreSQL-parancsot a példányhoz való csatlakozás után a kedvenc PostgreSQL-ügyfél eszközzel, például Azure Data Studio:
```console
select * from pg_extension;
```

A PostGIS példaként először [olvassa el az MIT & a](http://duspviz.mit.edu/tutorials/intro-postgis/) tervezésért felelős minisztérium osztályát. Előfordulhat, hogy a virtuális gép a teszteléshez való használatakor futtatnia kell a parancsot a `apt-get install unzip` telepítéshez.

```console
wget http://duspviz.mit.edu/_assets/data/intro-postgis-datasets.zip
unzip intro-postgis-datasets.zip
```

Kapcsolódjon az adatbázishoz, és hozza létre a PostGIS bővítményt:

```console
CREATE EXTENSION postgis;
```

> [!NOTE]
> Ha a csomag egyik bővítményét szeretné használni (például,, `postgis` `postgis_raster` `postgis_topology` `postgis_sfcgal` , `fuzzystrmatch` ...), először létre kell hoznia a PostGIS-bővítményt, majd létre kell hoznia a másik kiterjesztést. Például: `CREATE EXTENSION postgis` ; `CREATE EXTENSION postgis_raster` ;

És hozza létre a sémát:

```sql
CREATE TABLE coffee_shops (
  id serial NOT NULL,
  name character varying(50),
  address character varying(50),
  city character varying(50),
  state character varying(50),
  zip character varying(10),
  lat numeric,
  lon numeric,
  geom geometry(POINT,4326)
);
CREATE INDEX coffee_shops_gist ON coffee_shops USING gist (geom);
```

A PostGIS a kibővítési funkcióval kombinálva a coffee_shops táblázatot terjesztettük el:

```sql
SELECT create_distributed_table('coffee_shops', 'id');
```

Töltsön be néhány adattal:

```console
\copy coffee_shops(id,name,address,city,state,zip,lat,lon) from cambridge_coffee_shops.csv CSV HEADER;
```

És töltse ki a `geom` mezőt a megfelelő kódolású szélességgel és hosszúsággal a PostGIS `geometry` adattípusban:

```sql
UPDATE coffee_shops SET geom = ST_SetSRID(ST_MakePoint(lon,lat),4326);
```

Most már listázhatja az MIT legközelebb lévő kávéházi üzleteket (77 Massachusetts Ave, 42,359055,-71,093500):

```sql
SELECT name, address FROM coffee_shops ORDER BY geom <-> ST_SetSRID(ST_MakePoint(-71.093500,42.359055),4326);
```


### <a name="the-pg_cron-extension"></a>A pg_cron bővítmény

A `pg_cron` PostGIS mellett engedélyezze a PostgreSQL-kiszolgáló csoportját:

```console
azdata postgres server update -n pg2 -ns arc --extensions postgis,pg_cron
```

Vegye figyelembe, hogy ezzel újraindítja a csomópontokat, és telepíti a további bővítményeket, amelyek 2-3 percet is igénybe vehetnek.

Most már kapcsolódhat, és létrehozhatja a `pg_cron` bővítményt:

```sql
CREATE EXTENSION pg_cron;
```

Tesztelési célokra lehetővé teszi, hogy egy olyan táblát hozzon, `the_best_coffee_shop` amely véletlenszerű nevet vesz a korábbi `coffee_shops` táblázatból, és beállítja a táblázat tartalmát:

```sql
CREATE TABLE the_best_coffee_shop(name text);
```

`cron.schedule`Több SQL-utasítást is használhatunk egy véletlenszerű tábla nevének beszerzéséhez (figyelje meg egy ideiglenes tábla használatát egy elosztott lekérdezési eredmény tárolásához), és tárolja azt a következő helyre `the_best_coffee_shop` :

```sql
SELECT cron.schedule('* * * * *', $$
  TRUNCATE the_best_coffee_shop;
  CREATE TEMPORARY TABLE tmp AS SELECT name FROM coffee_shops ORDER BY random() LIMIT 1;
  INSERT INTO the_best_coffee_shop SELECT * FROM tmp;
  DROP TABLE tmp;
$$);
```

És most egy perc múlva egy másik nevet fogunk kapni:

```sql
SELECT * FROM the_best_coffee_shop;
```

```console
      name
-----------------
 B & B Snack Bar
(1 row)
```

A szintaxissal kapcsolatos részletekért tekintse meg a [PG_CRON readme](https://github.com/citusdata/pg_cron) című témakört.

>[!NOTE]
>A bővítmény eldobása nem támogatott `citus` . A `citus` nagy kapacitású-élmény biztosításához a bővítmény szükséges.

