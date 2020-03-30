---
title: Extensions - Azure Database for PostgreSQL - Single Server
description: További információ az Azure Database for PostgreSQL – Single Server azure database elérhető Postgres-bővítményeiről
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 02/13/2020
ms.openlocfilehash: a12738f5de783c8a34718b8d9cb4bbf54f230589
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77201271"
---
# <a name="postgresql-extensions-in-azure-database-for-postgresql---single-server"></a>PostgreSQL-bővítmények az Azure Database for PostgreSQL-ben – Egykiszolgálós
A PostgreSQL lehetővé teszi az adatbázis funkcionalitásának kiterjesztését bővítmények használatával. A bővítmények több kapcsolódó SQL-objektumot kötegelnek össze egyetlen csomagban, amelyek egyetlen paranccsal tölthetők be vagy távolíthatók el az adatbázisból. Az adatbázisba való betöltés után a bővítmények úgy működnek, mint a beépített funkciók.

## <a name="how-to-use-postgresql-extensions"></a>A PostgreSQL kiterjesztések használata
A PostgreSQL-bővítményeket használat előtt telepíteni kell az adatbázisba. Egy adott bővítmény telepítéséhez futtassa a [CREATE EXTENSION](https://www.postgresql.org/docs/current/sql-createextension.html) parancsot a psql eszközből a csomagolt objektumok adatbázisba való betöltéséhez.

Az Azure Database for PostgreSQL támogatja a kulcsbővítmények egy részét az alábbiak szerint. Ez az információ a `SELECT * FROM pg_available_extensions;`futtatásával is elérhető. A felsoroltakon kívüli bővítmények nem támogatottak. Nem hozhat létre saját bővítményt az Azure Database for PostgreSQL-ben.

## <a name="postgres-11-extensions"></a>Postgres 11 kiterjesztés

A következő bővítmények érhetők el az Azure Database for PostgreSQL-kiszolgálók, amelyek Postgres 11-es verzióval. 

> [!div class="mx-tableFixed"]
> | **Mellék**| **Bővítmény verziója** | **Leírás** |
> |---|---|---|
> |[address_standardizer](http://postgis.net/docs/Address_Standardizer.html)         | 2.5.1           | Cím alkotóelemekké történő elemzésére szolgál. |
> |[address_standardizer_data_us](http://postgis.net/docs/Address_Standardizer.html) | 2.5.1           | Példa a Standardizer us adatkészletének|
> |[btree_gin](https://www.postgresql.org/docs/11/btree-gin.html)                    | 1.3             | a közös adattípusok indexelése a GIN-ben|
> |[btree_gist](https://www.postgresql.org/docs/11/btree-gist.html)                   | 1.5             | a gyakori adattípusok indexelésének támogatása a GiST-ben|
> |[citext](https://www.postgresql.org/docs/11/citext.html)                       | 1.5             | a kis- és nagybetűket nem megkülönböztető karakterláncok adattípusa|
> |[Kocka](https://www.postgresql.org/docs/11/cube.html)                         | 1.4             | többdimenziós kockák adattípusa|
> |[dblink között](https://www.postgresql.org/docs/11/dblink.html)                       | 1.2             | csatlakozás más PostgreSQL adatbázisokhoz egy adatbázisból|
> |[dict_int](https://www.postgresql.org/docs/11/dict-int.html)                     | 1.0             | szövegkereső szótársablon egész számokhoz|
> |[földtávolság](https://www.postgresql.org/docs/11/earthdistance.html)                | 1.1             | kiszámítja a nagy kör távolságok a Föld felszínén|
> |[fuzzystrmatch között](https://www.postgresql.org/docs/11/fuzzystrmatch.html)                | 1.1             | hasonlóságok és a húrok közötti távolság meghatározása|
> |[háruház](https://www.postgresql.org/docs/11/hstore.html)                       | 1.5             | adattípus (kulcs, érték) párok tárolására|
> |[hipopg](https://hypopg.readthedocs.io/en/latest/)                       | 1.1.2           | A PostgreSQL hipotetikus indexei|
> |[intarray](https://www.postgresql.org/docs/11/intarray.html)                     | 1.2             | függvények, operátorok és indextámogatás egész számok 1-D tömbjeihez|
> |[Isn](https://www.postgresql.org/docs/11/isn.html)                          | 1.2             | a nemzetközi termékszámozási szabványok adattípusai|
> |[itree (ifa)](https://www.postgresql.org/docs/11/ltree.html)                        | 1.1             | hierarchikus faszerű műtárgyak adattípusa|
> |[orafce között](https://github.com/orafce/orafce)                       | 3.7             | Olyan függvények és operátorok, amelyek a kereskedelmi RDBMS-ből származó függvények és csomagok egy részét emulálják|
> |[pgaudit](https://www.pgaudit.org/)                     | 1.3.1             | naplózási funkciót biztosít|
> |[pgcrypto](https://www.postgresql.org/docs/11/pgcrypto.html)                     | 1.3             | kriptográfiai függvények|
> |[pgrouting](https://pgrouting.org/)                    | 2.6.2           | pgRouting kiterjesztés|
> |[pgrowlocks (pgrowlocks)](https://www.postgresql.org/docs/11/pgrowlocks.html)                   | 1.2             | sorszintű zárolási információk megjelenítése|
> |[pgstattuple között](https://www.postgresql.org/docs/11/pgstattuple.html)                  | 1.5             | négyszeres szintű statisztikák megjelenítése|
> |[pg_buffercache](https://www.postgresql.org/docs/11/pgbuffercache.html)               | 1.3             | a megosztott puffergyorsítótár vizsgálata|
> |[pg_partman](https://github.com/pgpartman/pg_partman)                   | 4.0.0           | Kiterjesztés particionált táblák kezelésére idő vagy azonosító szerint|
> |[pg_prewarm](https://www.postgresql.org/docs/11/pgprewarm.html)                   | 1.2             | előmelegen kapcsolatadatai|
> |[pg_stat_statements](https://www.postgresql.org/docs/11/pgstatstatements.html)           | 1.6             | nyomon követheti az összes végrehajtott SQL utasítás végrehajtási statisztikáját|
> |[pg_trgm](https://www.postgresql.org/docs/11/pgtrgm.html)                      | 1.4             | szöveg hasonlóság mérése és index keresés alapján trigrams|
> |[plpgsql](https://www.postgresql.org/docs/11/plpgsql.html)                      | 1.0             | PL/pgSQL eljárási nyelv|
> |[plv8](https://plv8.github.io/)                         | 2.3.11          | PL/JavaScript (v8) megbízható eljárási nyelv|
> |[postgis](https://www.postgis.net/)                      | 2.5.1           | PostGIS geometria, földrajz és rasztertérbeli típusok és funkciók|
> |[postgis_sfcgal](https://www.postgis.net/)               | 2.5.1           | PostGIS SFCGAL funkciók|
> |[postgis_tiger_geocoder](https://www.postgis.net/)       | 2.5.1           | PostGIS tigris geokódoló és fordított geokódoló|
> |[postgis_topology](https://postgis.net/docs/Topology.html)             | 2.5.1           | PostGIS topológia térbeli típusok és funkciók|
> |[postgres_fdw](https://www.postgresql.org/docs/11/postgres-fdw.html)                 | 1.0             | külföldi adatburkoló távoli PostgreSQL szerverekhez|
> |[asztali](https://www.postgresql.org/docs/11/tablefunc.html)                    | 1.0             | funkciók, amelyek egész táblázatokat kezelnek, beleértve a kereszttáblás|
> |[időskáladb](https://docs.timescale.com/latest)                    | 1.3.2             | Méretezhető beszúrások és összetett lekérdezések engedélyezése az idősorozat-adatokhoz|
> |[kiemelés nélkül](https://www.postgresql.org/docs/11/unaccent.html)                     | 1.1             | ékezeteket eltávolító szövegkereső szótár|
> |[uuid-ossp](https://www.postgresql.org/docs/11/uuid-ossp.html)                    | 1.1             | univerzálisan egyedi azonosítók (UUID-k) létrehozása|

## <a name="postgres-10-extensions"></a>Postgres 10 kiterjesztés 

A következő bővítmények érhetők el az Azure Database for PostgreSQL-kiszolgálók, amelyek Postgres 10-es verziójú.

> [!div class="mx-tableFixed"]
> | **Mellék**| **Bővítmény verziója** | **Leírás** |
> |---|---|---|
> |[address_standardizer](http://postgis.net/docs/Address_Standardizer.html)         | 2.5.1           | Cím alkotóelemekké történő elemzésére szolgál. |
> |[address_standardizer_data_us](http://postgis.net/docs/Address_Standardizer.html) | 2.5.1           | Példa a Standardizer us adatkészletének|
> |[btree_gin](https://www.postgresql.org/docs/10/btree-gin.html)                    | 1.3             | a közös adattípusok indexelése a GIN-ben|
> |[btree_gist](https://www.postgresql.org/docs/10/btree-gist.html)                   | 1.5             | a gyakori adattípusok indexelésének támogatása a GiST-ben|
> |[chkpass között](https://www.postgresql.org/docs/10/chkpass.html)                       | 1.0             | automatikusan titkosított jelszavak adattípusa|
> |[citext](https://www.postgresql.org/docs/10/citext.html)                       | 1.4             | a kis- és nagybetűket nem megkülönböztető karakterláncok adattípusa|
> |[Kocka](https://www.postgresql.org/docs/10/cube.html)                         | 1.2             | többdimenziós kockák adattípusa|
> |[dblink között](https://www.postgresql.org/docs/10/dblink.html)                       | 1.2             | csatlakozás más PostgreSQL adatbázisokhoz egy adatbázisból|
> |[dict_int](https://www.postgresql.org/docs/10/dict-int.html)                     | 1.0             | szövegkereső szótársablon egész számokhoz|
> |[földtávolság](https://www.postgresql.org/docs/10/earthdistance.html)                | 1.1             | kiszámítja a nagy kör távolságok a Föld felszínén|
> |[fuzzystrmatch között](https://www.postgresql.org/docs/10/fuzzystrmatch.html)                | 1.1             | hasonlóságok és a húrok közötti távolság meghatározása|
> |[háruház](https://www.postgresql.org/docs/10/hstore.html)                       | 1.4             | adattípus (kulcs, érték) párok tárolására|
> |[hipopg](https://hypopg.readthedocs.io/en/latest/)                       | 1.1.1           | A PostgreSQL hipotetikus indexei|
> |[intarray](https://www.postgresql.org/docs/10/intarray.html)                     | 1.2             | függvények, operátorok és indextámogatás egész számok 1-D tömbjeihez|
> |[Isn](https://www.postgresql.org/docs/10/isn.html)                          | 1.1             | a nemzetközi termékszámozási szabványok adattípusai|
> |[itree (ifa)](https://www.postgresql.org/docs/10/ltree.html)                        | 1.1             | hierarchikus faszerű műtárgyak adattípusa|
> |[orafce között](https://github.com/orafce/orafce)                       | 3.7             | Olyan függvények és operátorok, amelyek a kereskedelmi RDBMS-ből származó függvények és csomagok egy részét emulálják|
> |[pgaudit](https://www.pgaudit.org/)                     | 1.2             | naplózási funkciót biztosít|
> |[pgcrypto](https://www.postgresql.org/docs/10/pgcrypto.html)                     | 1.3             | kriptográfiai függvények|
> |[pgrouting](https://pgrouting.org/)                    | 2.5.2           | pgRouting kiterjesztés|
> |[pgrowlocks (pgrowlocks)](https://www.postgresql.org/docs/10/pgrowlocks.html)                   | 1.2             | sorszintű zárolási információk megjelenítése|
> |[pgstattuple között](https://www.postgresql.org/docs/10/pgstattuple.html)                  | 1.5             | négyszeres szintű statisztikák megjelenítése|
> |[pg_buffercache](https://www.postgresql.org/docs/10/pgbuffercache.html)               | 1.3             | a megosztott puffergyorsítótár vizsgálata|
> |[pg_partman](https://github.com/pgpartman/pg_partman)                   | 2.6.3           | Kiterjesztés particionált táblák kezelésére idő vagy azonosító szerint|
> |[pg_prewarm](https://www.postgresql.org/docs/10/pgprewarm.html)                   | 1.1             | előmelegen kapcsolatadatai|
> |[pg_stat_statements](https://www.postgresql.org/docs/10/pgstatstatements.html)           | 1.6             | nyomon követheti az összes végrehajtott SQL utasítás végrehajtási statisztikáját|
> |[pg_trgm](https://www.postgresql.org/docs/10/pgtrgm.html)                      | 1.3             | szöveg hasonlóság mérése és index keresés alapján trigrams|
> |[plpgsql](https://www.postgresql.org/docs/10/plpgsql.html)                      | 1.0             | PL/pgSQL eljárási nyelv|
> |[plv8](https://plv8.github.io/)                         | 2.1.0          | PL/JavaScript (v8) megbízható eljárási nyelv|
> |[postgis](https://www.postgis.net/)                      | 2.4.3           | PostGIS geometria, földrajz és rasztertérbeli típusok és funkciók|
> |[postgis_sfcgal](https://www.postgis.net/)               | 2.4.3           | PostGIS SFCGAL funkciók|
> |[postgis_tiger_geocoder](https://www.postgis.net/)       | 2.4.3           | PostGIS tigris geokódoló és fordított geokódoló|
> |[postgis_topology](https://postgis.net/docs/Topology.html)             | 2.4.3           | PostGIS topológia térbeli típusok és funkciók|
> |[postgres_fdw](https://www.postgresql.org/docs/10/postgres-fdw.html)                 | 1.0             | külföldi adatburkoló távoli PostgreSQL szerverekhez|
> |[asztali](https://www.postgresql.org/docs/10/tablefunc.html)                    | 1.0             | funkciók, amelyek egész táblázatokat kezelnek, beleértve a kereszttáblás|
> |[időskáladb](https://docs.timescale.com/latest)                    | 1.1.1             | Méretezhető beszúrások és összetett lekérdezések engedélyezése az idősorozat-adatokhoz|
> |[kiemelés nélkül](https://www.postgresql.org/docs/10/unaccent.html)                     | 1.1             | ékezeteket eltávolító szövegkereső szótár|
> |[uuid-ossp](https://www.postgresql.org/docs/10/uuid-ossp.html)                    | 1.1             | univerzálisan egyedi azonosítók (UUID-k) létrehozása|

## <a name="postgres-96-extensions"></a>Postgres 9.6 kiterjesztések 

A következő bővítmények érhetők el az Azure Database for PostgreSQL-kiszolgálók, amelyek Postgres 9.6-os verzióval.

> [!div class="mx-tableFixed"]
> | **Mellék**| **Bővítmény verziója** | **Leírás** |
> |---|---|---|
> |[address_standardizer](http://postgis.net/docs/Address_Standardizer.html)         | 2.3.2           | Cím alkotóelemekké történő elemzésére szolgál. |
> |[address_standardizer_data_us](http://postgis.net/docs/Address_Standardizer.html) | 2.3.2           | Példa a Standardizer us adatkészletének|
> |[btree_gin](https://www.postgresql.org/docs/9.6/btree-gin.html)                    | 1.0             | a közös adattípusok indexelése a GIN-ben|
> |[btree_gist](https://www.postgresql.org/docs/9.6/btree-gist.html)                   | 1.2             | a gyakori adattípusok indexelésének támogatása a GiST-ben|
> |[chkpass között](https://www.postgresql.org/docs/9.6/chkpass.html)                       | 1.0             | automatikusan titkosított jelszavak adattípusa|
> |[citext](https://www.postgresql.org/docs/9.6/citext.html)                       | 1.3             | a kis- és nagybetűket nem megkülönböztető karakterláncok adattípusa|
> |[Kocka](https://www.postgresql.org/docs/9.6/cube.html)                         | 1.2             | többdimenziós kockák adattípusa|
> |[dblink között](https://www.postgresql.org/docs/9.6/dblink.html)                       | 1.2             | csatlakozás más PostgreSQL adatbázisokhoz egy adatbázisból|
> |[dict_int](https://www.postgresql.org/docs/9.6/dict-int.html)                     | 1.0             | szövegkereső szótársablon egész számokhoz|
> |[földtávolság](https://www.postgresql.org/docs/9.6/earthdistance.html)                | 1.1             | kiszámítja a nagy kör távolságok a Föld felszínén|
> |[fuzzystrmatch között](https://www.postgresql.org/docs/9.6/fuzzystrmatch.html)                | 1.1             | hasonlóságok és a húrok közötti távolság meghatározása|
> |[háruház](https://www.postgresql.org/docs/9.6/hstore.html)                       | 1.4             | adattípus (kulcs, érték) párok tárolására|
> |[hipopg](https://hypopg.readthedocs.io/en/latest/)                       | 1.1.1           | A PostgreSQL hipotetikus indexei|
> |[intarray](https://www.postgresql.org/docs/9.6/intarray.html)                     | 1.2             | függvények, operátorok és indextámogatás egész számok 1-D tömbjeihez|
> |[Isn](https://www.postgresql.org/docs/9.6/isn.html)                          | 1.1             | a nemzetközi termékszámozási szabványok adattípusai|
> |[itree (ifa)](https://www.postgresql.org/docs/9.6/ltree.html)                        | 1.1             | hierarchikus faszerű műtárgyak adattípusa|
> |[orafce között](https://github.com/orafce/orafce)                       | 3.7             | Olyan függvények és operátorok, amelyek a kereskedelmi RDBMS-ből származó függvények és csomagok egy részét emulálják|
> |[pgaudit](https://www.pgaudit.org/)                     | 1.1.2             | naplózási funkciót biztosít|
> |[pgcrypto](https://www.postgresql.org/docs/9.6/pgcrypto.html)                     | 1.3             | kriptográfiai függvények|
> |[pgrouting](https://pgrouting.org/)                    | 2.3.2           | pgRouting kiterjesztés|
> |[pgrowlocks (pgrowlocks)](https://www.postgresql.org/docs/9.6/pgrowlocks.html)                   | 1.2             | sorszintű zárolási információk megjelenítése|
> |[pgstattuple között](https://www.postgresql.org/docs/9.6/pgstattuple.html)                  | 1.4             | négyszeres szintű statisztikák megjelenítése|
> |[pg_buffercache](https://www.postgresql.org/docs/9.6/pgbuffercache.html)               | 1.2             | a megosztott puffergyorsítótár vizsgálata|
> |[pg_partman](https://github.com/pgpartman/pg_partman)                   | 2.6.3           | Kiterjesztés particionált táblák kezelésére idő vagy azonosító szerint|
> |[pg_prewarm](https://www.postgresql.org/docs/9.6/pgprewarm.html)                   | 1.1             | előmelegen kapcsolatadatai|
> |[pg_stat_statements](https://www.postgresql.org/docs/9.6/pgstatstatements.html)           | 1.4             | nyomon követheti az összes végrehajtott SQL utasítás végrehajtási statisztikáját|
> |[pg_trgm](https://www.postgresql.org/docs/9.6/pgtrgm.html)                      | 1.3             | szöveg hasonlóság mérése és index keresés alapján trigrams|
> |[plpgsql](https://www.postgresql.org/docs/9.6/plpgsql.html)                      | 1.0             | PL/pgSQL eljárási nyelv|
> |[plv8](https://plv8.github.io/)                         | 2.1.0          | PL/JavaScript (v8) megbízható eljárási nyelv|
> |[postgis](https://www.postgis.net/)                      | 2.3.2           | PostGIS geometria, földrajz és rasztertérbeli típusok és funkciók|
> |[postgis_sfcgal](https://www.postgis.net/)               | 2.3.2           | PostGIS SFCGAL funkciók|
> |[postgis_tiger_geocoder](https://www.postgis.net/)       | 2.3.2           | PostGIS tigris geokódoló és fordított geokódoló|
> |[postgis_topology](https://postgis.net/docs/Topology.html)             | 2.3.2           | PostGIS topológia térbeli típusok és funkciók|
> |[postgres_fdw](https://www.postgresql.org/docs/9.6/postgres-fdw.html)                 | 1.0             | külföldi adatburkoló távoli PostgreSQL szerverekhez|
> |[asztali](https://www.postgresql.org/docs/9.6/tablefunc.html)                    | 1.0             | funkciók, amelyek egész táblázatokat kezelnek, beleértve a kereszttáblás|
> |[időskáladb](https://docs.timescale.com/latest)                    | 1.1.1             | Méretezhető beszúrások és összetett lekérdezések engedélyezése az idősorozat-adatokhoz|
> |[kiemelés nélkül](https://www.postgresql.org/docs/9.6/unaccent.html)                     | 1.1             | ékezeteket eltávolító szövegkereső szótár|
> |[uuid-ossp](https://www.postgresql.org/docs/9.6/uuid-ossp.html)                    | 1.1             | univerzálisan egyedi azonosítók (UUID-k) létrehozása|

## <a name="postgres-95-extensions"></a>Postgres 9,5 kiterjesztés 

A következő bővítmények érhetők el az Azure Database for PostgreSQL-kiszolgálók, amelyek Postgres 9.5-ös verzióval.

> [!div class="mx-tableFixed"]
> | **Mellék**| **Bővítmény verziója** | **Leírás** |
> |---|---|---|
> |[address_standardizer](http://postgis.net/docs/Address_Standardizer.html)         | 2.3.0           | Cím alkotóelemekké történő elemzésére szolgál. |
> |[address_standardizer_data_us](http://postgis.net/docs/Address_Standardizer.html) | 2.3.0           | Példa a Standardizer us adatkészletének|
> |[btree_gin](https://www.postgresql.org/docs/9.5/btree-gin.html)                    | 1.0             | a közös adattípusok indexelése a GIN-ben|
> |[btree_gist](https://www.postgresql.org/docs/9.5/btree-gist.html)                   | 1.1             | a gyakori adattípusok indexelésének támogatása a GiST-ben|
> |[chkpass között](https://www.postgresql.org/docs/9.5/chkpass.html)                       | 1.0             | automatikusan titkosított jelszavak adattípusa|
> |[citext](https://www.postgresql.org/docs/9.5/citext.html)                       | 1.1             | a kis- és nagybetűket nem megkülönböztető karakterláncok adattípusa|
> |[Kocka](https://www.postgresql.org/docs/9.5/cube.html)                         | 1.0             | többdimenziós kockák adattípusa|
> |[dblink között](https://www.postgresql.org/docs/9.5/dblink.html)                       | 1.1             | csatlakozás más PostgreSQL adatbázisokhoz egy adatbázisból|
> |[dict_int](https://www.postgresql.org/docs/9.5/dict-int.html)                     | 1.0             | szövegkereső szótársablon egész számokhoz|
> |[földtávolság](https://www.postgresql.org/docs/9.5/earthdistance.html)                | 1.0             | kiszámítja a nagy kör távolságok a Föld felszínén|
> |[fuzzystrmatch között](https://www.postgresql.org/docs/9.5/fuzzystrmatch.html)                | 1.0             | hasonlóságok és a húrok közötti távolság meghatározása|
> |[háruház](https://www.postgresql.org/docs/9.5/hstore.html)                       | 1.3             | adattípus (kulcs, érték) párok tárolására|
> |[hipopg](https://hypopg.readthedocs.io/en/latest/)                       | 1.1.1           | A PostgreSQL hipotetikus indexei|
> |[intarray](https://www.postgresql.org/docs/9.5/intarray.html)                     | 1.0             | függvények, operátorok és indextámogatás egész számok 1-D tömbjeihez|
> |[Isn](https://www.postgresql.org/docs/9.5/isn.html)                          | 1.0             | a nemzetközi termékszámozási szabványok adattípusai|
> |[itree (ifa)](https://www.postgresql.org/docs/9.5/ltree.html)                        | 1.0             | hierarchikus faszerű műtárgyak adattípusa|
> |[orafce között](https://github.com/orafce/orafce)                       | 3.7             | Olyan függvények és operátorok, amelyek a kereskedelmi RDBMS-ből származó függvények és csomagok egy részét emulálják|
> |[pgaudit](https://www.pgaudit.org/)                     | 1.0.7             | naplózási funkciót biztosít|
> |[pgcrypto](https://www.postgresql.org/docs/9.5/pgcrypto.html)                     | 1.2             | kriptográfiai függvények|
> |[pgrouting](https://pgrouting.org/)                    | 2.3.0           | pgRouting kiterjesztés|
> |[pgrowlocks (pgrowlocks)](https://www.postgresql.org/docs/9.5/pgrowlocks.html)                   | 1.1             | sorszintű zárolási információk megjelenítése|
> |[pgstattuple között](https://www.postgresql.org/docs/9.5/pgstattuple.html)                  | 1.3             | négyszeres szintű statisztikák megjelenítése|
> |[pg_buffercache](https://www.postgresql.org/docs/9.5/pgbuffercache.html)               | 1.1             | a megosztott puffergyorsítótár vizsgálata|
> |[pg_partman](https://github.com/pgpartman/pg_partman)                   | 2.6.3           | Kiterjesztés particionált táblák kezelésére idő vagy azonosító szerint|
> |[pg_prewarm](https://www.postgresql.org/docs/9.5/pgprewarm.html)                   | 1.0             | előmelegen kapcsolatadatai|
> |[pg_stat_statements](https://www.postgresql.org/docs/9.5/pgstatstatements.html)           | 1.3             | nyomon követheti az összes végrehajtott SQL utasítás végrehajtási statisztikáját|
> |[pg_trgm](https://www.postgresql.org/docs/9.5/pgtrgm.html)                      | 1.1             | szöveg hasonlóság mérése és index keresés alapján trigrams|
> |[plpgsql](https://www.postgresql.org/docs/9.5/plpgsql.html)                      | 1.0             | PL/pgSQL eljárási nyelv|
> |[postgis](https://www.postgis.net/)                      | 2.3.0           | PostGIS geometria, földrajz és rasztertérbeli típusok és funkciók|
> |[postgis_sfcgal](https://www.postgis.net/)               | 2.3.0           | PostGIS SFCGAL funkciók|
> |[postgis_tiger_geocoder](https://www.postgis.net/)       | 2.3.0           | PostGIS tigris geokódoló és fordított geokódoló|
> |[postgis_topology](https://postgis.net/docs/Topology.html)             | 2.3.0           | PostGIS topológia térbeli típusok és funkciók|
> |[postgres_fdw](https://www.postgresql.org/docs/9.5/postgres-fdw.html)                 | 1.0             | külföldi adatburkoló távoli PostgreSQL szerverekhez|
> |[asztali](https://www.postgresql.org/docs/9.5/tablefunc.html)                    | 1.0             | funkciók, amelyek egész táblázatokat kezelnek, beleértve a kereszttáblás|
> |[kiemelés nélkül](https://www.postgresql.org/docs/9.5/unaccent.html)                     | 1.0             | ékezeteket eltávolító szövegkereső szótár|
> |[uuid-ossp](https://www.postgresql.org/docs/9.5/uuid-ossp.html)                    | 1.0             | univerzálisan egyedi azonosítók (UUID-k) létrehozása|


## <a name="pg_stat_statements"></a>pg_stat_statements
A [pg_stat_statements bővítmény](https://www.postgresql.org/docs/current/pgstatstatements.html) előre be van töltve minden Azure Database for PostgreSQL kiszolgálón, hogy lehetővé tegye az SQL-utasítások végrehajtási statisztikáinak nyomon követését.
A `pg_stat_statements.track`beállítás , amely azt szabályozza, hogy a `top`bővítmény milyen kimutatásokat számlál, alapértelmezés szerint a , azaz az ügyfelek által közvetlenül kiadott összes utasítás nyomon követése. A másik két `none` követési `all`szint a és a. Ez a beállítás kiszolgálóparaméterként konfigurálható az [Azure Portalon](https://docs.microsoft.com/azure/postgresql/howto-configure-server-parameters-using-portal) vagy az [Azure CLI-n](https://docs.microsoft.com/azure/postgresql/howto-configure-server-parameters-using-cli)keresztül.

Van egy kompromisszum a lekérdezés végrehajtási információk pg_stat_statements biztosít, és a hatása a kiszolgáló teljesítményét, mert naplózza az egyes SQL utasítás. Ha nem használja aktívan a pg_stat_statements bővítményt, `none`javasoljuk, hogy állítsa a . `pg_stat_statements.track` Vegye figyelembe, hogy egyes külső monitorozási szolgáltatások támaszkodhat pg_stat_statements lekérdezési teljesítmény elemzések, ezért erősítse meg, hogy ez a helyzet az Ön számára, vagy sem.

## <a name="dblink-and-postgres_fdw"></a>dblink és postgres_fdw
[A dblink](https://www.postgresql.org/docs/current/contrib-dblink-function.html) és [postgres_fdw](https://www.postgresql.org/docs/current/postgres-fdw.html) lehetővé teszik, hogy az egyik PostgreSQL szerverről egy másikra vagy egy másik adatbázishoz csatlakozzon ugyanazon a szerveren. A fogadó kiszolgálónak engedélyeznie kell a küldő kiszolgáló tól a tűzfalon keresztülérkező kapcsolatokat. Ha ezeket a bővítményeket használja a PostgreSQL-kiszolgálókhoz készült Azure Database közötti csatlakozáshoz, ezt úgy teheti meg, hogy az "Azure-szolgáltatásokhoz való hozzáférés engedélyezése" beállítást BE-re állítja. Erre akkor is szükség van, ha a bővítményeket arra szeretné használni, hogy visszakerüljön ugyanarra a kiszolgálóra. Az "Azure-szolgáltatásokhoz való hozzáférés engedélyezése" beállítás a Postgres-kiszolgáló Azure portallapján, a Kapcsolatbiztonság csoportban található. Az "Azure-szolgáltatások hoz való hozzáférés engedélyezése" bekapcsolása az összes Azure IP-szolgáltatót az engedélyezési listára helyezi.

Jelenleg a PostgreSQL Azure Database for Azure Database kimenő kapcsolatai nem támogatottak, kivéve a PostgreSQL-kiszolgálókhoz tartozó más Azure-adatbázissal való kapcsolatokat.

## <a name="uuid"></a>uuid
Ha az [uuid-ossp kiterjesztésből](https://www.postgresql.org/docs/current/uuid-ossp.html)tervezi használni, `gen_random_uuid()` `uuid_generate_v4()` fontolja meg a [pgcrypto kiterjesztés](https://www.postgresql.org/docs/current/pgcrypto.html) összehasonlítását a teljesítményelőnyök érdekében.

## <a name="pgaudit"></a>pgAudit
A [pgAudit bővítmény](https://github.com/pgaudit/pgaudit/blob/master/README.md) munkamenet- és objektumnaplózást biztosít. Ha meg szeretné tudni, hogyan használhatja ezt a bővítményt az Azure Database for PostgreSQL-ben, olvassa el a [naplózási fogalmakról szóló cikket.](concepts-audit.md) 

## <a name="pg_prewarm"></a>pg_prewarm
A pg_prewarm bővítmény betölti a relációs adatokat a gyorsítótárba. A gyorsítótárak előmelegítése azt jelenti, hogy a lekérdezések jobb válaszidővel rendelkeznek az újraindítás utáni első futtatáskor. A Postgres 10-es és az alatti területen a prewarming manuálisan történik az [előmelegedés funkció](https://www.postgresql.org/docs/10/pgprewarm.html)használatával.

A Postgres 11 és újabb, beállíthatja prewarming történni [automatikusan](https://www.postgresql.org/docs/current/pgprewarm.html). A módosítás alkalmazásához `shared_preload_libraries` fel kell tüntetnie pg_prewarm a paraméter listájában, és újra kell indítania a kiszolgálót. Paraméterek az [Azure Portalon,](howto-configure-server-parameters-using-portal.md) [CLI,](howto-configure-server-parameters-using-cli.md)REST API vagy ARM sablonban állíthatók be. 

## <a name="timescaledb"></a>IdőskálaDB
A TimescaleDB egy idősorozat-adatbázis, amely a PostgreSQL kiterjesztéseként van csomagolva. TimescaleDB időorientált analitikus funkciókat biztosít, optimalizálás, és skálázhatja a Postgres az idősorozat számítási feladatok.

[Tudjon meg többet a TimescaleDB-ről,](https://docs.timescale.com/latest)a [Timescale, Inc.](https://www.timescale.com/)bejegyzett védjegyéről. A PostgreSQL Azure Database az Időskála nyílt forráskódú verzióját biztosítja. Ha meg szeretné tudni, hogy mely időskálás funkciók érhetők el ebben a verzióban, olvassa el [az Időskála termékösszehasonlítása című témakört.](https://www.timescale.com/products/)

### <a name="installing-timescaledb"></a>Az időskála telepítéseDB
A TimescaleDB telepítéséhez fel kell tüntetnie azt a kiszolgáló megosztott előtöltő könyvtáraiban. A Postgres paraméterének `shared_preload_libraries` módosítása a **kiszolgáló újraindításához** szükséges. A paramétereket módosíthatja az [Azure Portalon](howto-configure-server-parameters-using-portal.md) vagy az [Azure CLI](howto-configure-server-parameters-using-cli.md)használatával.

Az [Azure Portal használata:](https://portal.azure.com/)

1. Válassza ki az Azure Database for PostgreSQL kiszolgálóját.

2. Az oldalsávon válassza a **Kiszolgálóparaméterei lehetőséget.**

3. Keresse meg `shared_preload_libraries` a paramétert.

4. Válassza **az TimescaleDB lehetőséget.**

5. A módosítások megőrzéséhez válassza a **Mentés** lehetőséget. A módosítás mentése után értesítést kap. 

6. Az értesítést követően **indítsa újra** a kiszolgálót a módosítások alkalmazásához. A kiszolgáló újraindításáról az [Azure Database for PostgreSQL-kiszolgáló újraindítása](howto-restart-server-portal.md)című témakörben olvashat.


Most már engedélyezheti a TimescaleDB-t a Postgres adatbázisban. Csatlakozzon az adatbázishoz, és adja ki a következő parancsot:
```sql
CREATE EXTENSION IF NOT EXISTS timescaledb CASCADE;
```
> [!TIP]
> Ha hibaüzenetet lát, ellenőrizze, hogy [újraindította-e a kiszolgálót](howto-restart-server-portal.md) a shared_preload_libraries mentése után. 

Most már létrehozhat egy TimescaleDB hipertable [a semmiből,](https://docs.timescale.com/getting-started/creating-hypertables) vagy áttelepíteni [a meglévő idő-sorozat adatok PostgreSQL](https://docs.timescale.com/getting-started/migrating-data).

### <a name="restoring-a-timescale-database"></a>Időskála-adatbázis visszaállítása
Ha pg_dump és pg_restore használatával szeretne visszaállítani egy időskálás adatbázist, `timescaledb_pre_restore()` `timescaledb_post restore()`két segítő eljárást kell futtatnia a céladatbázisban: és .

Először készítse elő a céladatbázist:

```SQL
--create the new database where you'll perform the restore
CREATE DATABASE tutorial;
\c tutorial --connect to the database 
CREATE EXTENSION timescaledb;

SELECT timescaledb_pre_restore();
```

Most futtathatja pg_dump az eredeti adatbázison, majd pg_restore. A visszaállítás után mindenképpen futtassa a következő parancsot a visszaállított adatbázisban:

```SQL
SELECT timescaledb_post_restore();
```


## <a name="next-steps"></a>További lépések
Ha nem látja a használni kívánt bővítményt, tudassa velünk. Szavazzon a meglévő kérésekre, vagy hozzon létre új visszajelzési kéréseket [visszajelzési](https://feedback.azure.com/forums/597976-azure-database-for-postgresql)fórumunkban.
