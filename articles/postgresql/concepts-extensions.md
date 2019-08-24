---
title: PostgreSQL-bővítmények használata Azure Database for PostgreSQL – egyetlen kiszolgálón
description: Leírja, hogy az adatbázis funkciói kiterjeszthetők-e az Azure Database for PostgreSQL-egyetlen kiszolgáló bővítményeivel.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 08/23/2019
ms.openlocfilehash: 93cc02fafcfa153c452f37c2bc69bb47e2629f1d
ms.sourcegitcommit: 4b8a69b920ade815d095236c16175124a6a34996
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/23/2019
ms.locfileid: "69998062"
---
# <a name="postgresql-extensions-in-azure-database-for-postgresql---single-server"></a>PostgreSQL-bővítmények Azure Database for PostgreSQL – egyetlen kiszolgáló
A PostgreSQL lehetőséget nyújt az adatbázis funkcióinak kiterjesztésére a bővítmények használatával. A bővítmények több kapcsolódó SQL-objektumot egyesítenek egyetlen csomagban, amely egyetlen paranccsal tölthető be vagy távolítható el az adatbázisból. Az adatbázisba való betöltés után a bővítmények a beépített funkciókhoz hasonlóan működnek.

## <a name="how-to-use-postgresql-extensions"></a>A PostgreSQL-bővítmények használata
A PostgreSQL-bővítményeket a használatuk előtt telepíteni kell az adatbázisba. Egy adott bővítmény telepítéséhez futtassa a [bővítmény](https://www.postgresql.org/docs/current/static/sql-createextension.html) létrehozása parancsot a psql eszközből a csomagolt objektumok adatbázisba való betöltéséhez.

A Azure Database for PostgreSQL a következő listában szereplő kulcs-kiterjesztések részhalmazát támogatja. A felsorolt bővítmények nem támogatottak. Nem hozhat létre saját bővítményt Azure Database for PostgreSQLban.

## <a name="extensions-supported-by-azure-database-for-postgresql"></a>A Azure Database for PostgreSQL által támogatott bővítmények
A következő táblázat a Azure Database for PostgreSQL által jelenleg támogatott szabványos PostgreSQL-bővítményeket sorolja fel. Ezek az információk a futtatásával `SELECT * FROM pg_available_extensions;`is elérhetők.

### <a name="data-types-extensions"></a>Adattípusok bővítményei

> [!div class="mx-tableFixed"]
> | **Kiterjesztés** | **Leírás** |
> |---|---|
> | [chkpass](https://www.postgresql.org/docs/9.6/static/chkpass.html) | Adattípust biztosít az automatikusan titkosított jelszavakhoz. |
> | [citext](https://www.postgresql.org/docs/9.6/static/citext.html) | Kis-és nagybetűket nem megkülönböztető karakterlánc-típust biztosít. |
> | [cube](https://www.postgresql.org/docs/9.6/static/cube.html) | Adattípust biztosít a többdimenziós kockákhoz. |
> | [hstore](https://www.postgresql.org/docs/9.6/static/hstore.html) | Adattípust biztosít a kulcs/érték párok készletének tárolására. |
> | [helytelen átvitt](https://www.postgresql.org/docs/9.6/static/isn.html) | Adattípusokat biztosít a nemzetközi termékek számozási szabványainak. |
> | [ltree](https://www.postgresql.org/docs/9.6/static/ltree.html) | Adattípust biztosít a hierarchikus fastruktúrához hasonló struktúrákhoz. |

### <a name="functions-extensions"></a>Functions-bővítmények

> [!div class="mx-tableFixed"]
> | **Kiterjesztés** | **Leírás** |
> |---|---|
> | [earthdistance](https://www.postgresql.org/docs/9.6/static/earthdistance.html) | Lehetővé teszi a nagy kör alakú távolságok kiszámítását a Föld felszínén. |
> | [fuzzystrmatch](https://www.postgresql.org/docs/9.6/static/fuzzystrmatch.html) | Számos függvényt biztosít a karakterláncok közötti hasonlóságok és távolságok meghatározásához. |
> | [intarray](https://www.postgresql.org/docs/9.6/static/intarray.html) | Függvényeket és operátorokat biztosít az egész számok null nélküli tömbbe való manipulálására. |
> | [pgcrypto](https://www.postgresql.org/docs/9.6/static/pgcrypto.html) | Titkosítási funkciókat biztosít. |
> | [pg\_partman](https://pgxn.org/dist/pg_partman/doc/pg_partman.html) | A particionált táblákat idő vagy azonosító alapján kezeli. |
> | [PG\_trgm](https://www.postgresql.org/docs/9.6/static/pgtrgm.html) | A függvényeket és operátorokat biztosít az alfanumerikus szöveg hasonlóságának meghatározásához a trigram megfeleltetése alapján. |
> | [tablefunc](https://www.postgresql.org/docs/9.6/static/tablefunc.html) | Olyan függvényeket biztosít, amelyek a teljes táblákat, például a kereszttáblás funkciókat kezelik. |
> | [uuid-ossp](https://www.postgresql.org/docs/9.6/static/uuid-ossp.html) | Univerzálisan egyedi azonosítókat (UUID-ket) generál. (Lásd alább a bővítmény megjegyzéseit). |
> | [orafce](https://github.com/orafce/orafce) | A kereskedelmi adatbázisokból emulált függvények és csomagok egy részhalmazát adja meg. |

### <a name="full-text-search-extensions"></a>Teljes szöveges keresési bővítmények

> [!div class="mx-tableFixed"]
> | **Kiterjesztés** | **Leírás** |
> |---|---|
> | [dict\_int](https://www.postgresql.org/docs/9.6/static/dict-int.html) | Szöveges keresési szótári sablont biztosít az egész számokhoz. |
> | [unaccent](https://www.postgresql.org/docs/9.6/static/unaccent.html) | Szöveges keresési szótár, amely eltávolítja az ékezeteket (mellékjelek jeleit) a lexemes. |

### <a name="index-types-extensions"></a>Index típusú bővítmények

> [!div class="mx-tableFixed"]
> | **Kiterjesztés** | **Leírás** |
> |---|---|
> | [btree\_gin](https://www.postgresql.org/docs/9.6/static/btree-gin.html) | Olyan minta GIN operátori osztályokat biztosít, amelyek bizonyos adattípusok esetében a B-fa viselkedését implementálják. |
> | [btree\_gist](https://www.postgresql.org/docs/9.6/static/btree-gist.html) | A "B" fát implementáló lényegi index operátori osztályokat biztosít. |

### <a name="language-extensions"></a>Nyelvi bővítmények

> [!div class="mx-tableFixed"]
> | **Kiterjesztés** | **Leírás** |
> |---|---|
> | [plpgsql](https://www.postgresql.org/docs/9.6/static/plpgsql.html) | PL/pgSQL betölthető eljárási nyelv. |
> | [plv8](https://plv8.github.io/) | A PostgreSQL-hez készült JavaScript nyelvi bővítmény, amely tárolt eljárásokhoz, triggerekhez stb. használható. |

### <a name="miscellaneous-extensions"></a>Egyéb bővítmények

> [!div class="mx-tableFixed"]
> | **Kiterjesztés** | **Leírás** |
> |---|---|
> | [PG\_buffercache](https://www.postgresql.org/docs/9.6/static/pgbuffercache.html) | A lehetővé teszi a megosztott puffer gyorsítótárában zajló események valós idejű vizsgálatát. |
> | [pg\_prewarm](https://www.postgresql.org/docs/9.6/static/pgprewarm.html) | Lehetővé teszi a kapcsolatok betöltését a puffer gyorsítótárába. |
> | [PG\_stat\_-utasítások](https://www.postgresql.org/docs/9.6/static/pgstatstatements.html) | A kiszolgáló által végrehajtott összes SQL-utasítás végrehajtási statisztikáinak nyomon követését teszi lehetővé. (Lásd alább a bővítmény megjegyzéseit). |
> | [pgrowlocks](https://www.postgresql.org/docs/9.6/static/pgrowlocks.html) | Lehetővé teszi a sor szintű zárolási információk megjelenítését. |
> | [pgstattuple](https://www.postgresql.org/docs/9.6/static/pgstattuple.html) | A rekord szintű statisztikák megjelenítését teszi lehetővé. |
> | [postgres\_FDW](https://www.postgresql.org/docs/9.6/static/postgres-fdw.html) | A külső PostgreSQL-kiszolgálókon tárolt adatforrásokhoz való hozzáféréshez használt idegen adatburkoló. (Lásd alább a bővítmény megjegyzéseit).|
> | [hypopg](https://hypopg.readthedocs.io/en/latest/) | A olyan feltételezett indexek létrehozására szolgál, amelyek nem a CPU-t vagy a lemezt terhelik. |
> | [dblink](https://www.postgresql.org/docs/current/dblink.html) | Egy modul, amely támogatja a más PostgreSQL-adatbázisokhoz való kapcsolódást egy adatbázis-munkameneten belülről. (Lásd alább a bővítmény megjegyzéseit). |


### <a name="postgis-extensions"></a>PostGIS-bővítmények

> [!div class="mx-tableFixed"]
> | **Kiterjesztés** | **Leírás** |
> |---|---|
> | [PostGIS](https://www.postgis.net/), PostGIS\_topológia, PostGIS\_Tiger\_geocoder, PostGIS\_sfcgal | A PostgreSQL térbeli és földrajzi objektumai. |
> | a címek\_szabványosítása, a\_\_szabványosítási adatkezelési szolgáltatás\_ | A címek összetevőire való elemzéséhez használatos. A helymeghatározáshoz-címek normalizálása lépésének támogatásához használatos. |
> | [pgrouting](https://pgrouting.org/) | Kibővíti a PostGIS/PostgreSQL térinformatikai adatbázist a térinformatikai útválasztási funkciók biztosításához. |


### <a name="time-series-extensions"></a>Idősorozat-bővítmények

> [!div class="mx-tableFixed"]
> | **Kiterjesztés** | **Leírás** |
> |---|---|
> | [TimescaleDB](https://docs.timescale.com/latest) | Egy idősorozatos SQL-adatbázis, amely támogatja az automatizált particionálást a gyorsabb betöltéshez és lekérdezésekhez. Időalapú analitikai funkciókat, optimalizálásokat és a PostgreSQL skáláját biztosítja az idősorozatos munkaterhelésekhez. A TimescaleDB fejlesztése és az [időskála, Inc.](https://www.timescale.com/) bejegyzett védjegye. (Lásd alább a bővítmény megjegyzéseit). |

## <a name="postgres-11-extensions"></a>Postgres 11 bővítmény

A következő bővítmények érhetők el a postgres 11-es verzióját tartalmazó Azure Database for PostgreSQL-kiszolgálókon.

> [!div class="mx-tableFixed"]
> | **Kiterjesztés**| **Bővítmény verziója** | **Leírás** |
> |---|---|---|
> |[address_standardizer](http://postgis.net/docs/Address_Standardizer.html)         | 2.5.1           | A címek összetevőire való elemzéséhez használatos. |
> |[address_standardizer_data_us](http://postgis.net/docs/Address_Standardizer.html) | 2.5.1           | Példa az USA-beli szabványosító adathalmazra|
> |[btree_gin](https://www.postgresql.org/docs/11/btree-gin.html)                    | 1.3             | gyakori adattípusok indexelésének támogatása a GIN-ben|
> |[btree_gist](https://www.postgresql.org/docs/11/btree-gist.html)                   | 1.5             | gyakori adattípusok indexelésének támogatása a lényegeben|
> |[citext](https://www.postgresql.org/docs/11/static/citext.html)                       | 1.5             | kis-és nagybetűket megkülönböztető karakterláncok adattípusa|
> |[cube](https://www.postgresql.org/docs/11/static/cube.html)                         | 1.4             | többdimenziós kockák adattípusa|
> |[dblink](https://www.postgresql.org/docs/11/dblink.html)                       | 1.2             | Kapcsolódás más PostgreSQL-adatbázisokhoz egy adatbázison belülről|
> |[dict_int](https://www.postgresql.org/docs/11/static/dict-int.html)                     | 1.0             | szöveges keresési szótár sablonja egész számokhoz|
> |[earthdistance](https://www.postgresql.org/docs/11/static/earthdistance.html)                | 1.1             | nagy hatótávolságú távolságok kiszámítása a Föld felszínén|
> |[fuzzystrmatch](https://www.postgresql.org/docs/11/static/fuzzystrmatch.html)                | 1.1             | a karakterláncok közötti hasonlóságok és távolságok meghatározása|
> |[hstore](https://www.postgresql.org/docs/11/static/hstore.html)                       | 1.5             | a (kulcs, érték) párok tárolására szolgáló adattípus|
> |[hypopg](https://hypopg.readthedocs.io/en/latest/)                       | 1.1.2           | Feltételezett indexek a PostgreSQL-hez|
> |[intarray](https://www.postgresql.org/docs/11/static/intarray.html)                     | 1.2             | függvények, operátorok és indexek támogatása egész számok 1-D tömbhöz|
> |[helytelen átvitt](https://www.postgresql.org/docs/11/static/isn.html)                          | 1.2             | a nemzetközi termékek számozási szabványainak adattípusai|
> |[ltree](https://www.postgresql.org/docs/11/static/ltree.html)                        | 1.1             | adattípus a hierarchikus fastruktúrához hasonló struktúrákhoz|
> |[orafce](https://github.com/orafce/orafce)                       | 3.7             | A függvények és csomagok egy részhalmazát emuláló függvények és operátorok kereskedelmi RDBMS|
> |[pgcrypto](https://www.postgresql.org/docs/11/static/pgcrypto.html)                     | 1.3             | titkosítási függvények|
> |[pgrouting](https://pgrouting.org/)                    | 2.6.2           | pgRouting-bővítmény|
> |[pgrowlocks](https://www.postgresql.org/docs/11/static/pgrowlocks.html)                   | 1.2             | sor szintű zárolási információk megjelenítése|
> |[pgstattuple](https://www.postgresql.org/docs/11/static/pgstattuple.html)                  | 1.5             | rekord szintű statisztikák megjelenítése|
> |[pg_buffercache](https://www.postgresql.org/docs/11/static/pgbuffercache.html)               | 1.3             | a megosztott puffer gyorsítótárának vizsgálata|
> |[pg_partman](https://github.com/pgpartman/pg_partman)                   | 4.0.0           | Bővítmény a particionált táblák kezeléséhez idő vagy azonosító alapján|
> |[pg_prewarm](https://www.postgresql.org/docs/11/pgprewarm.html)                   | 1.2             | előmelegítő kapcsolatok adatvédelme|
> |[pg_stat_statements](https://www.postgresql.org/docs/11/static/pgstatstatements.html)           | 1.6             | az összes végrehajtott SQL-utasítás végrehajtási statisztikájának nyomon követése|
> |[pg_trgm](https://www.postgresql.org/docs/11/static/pgtrgm.html)                      | 1.4             | a szöveg hasonlóságának mérése és indexelése Trigrams alapján|
> |[plpgsql](https://www.postgresql.org/docs/11/static/plpgsql.html)                      | 1.0             | PL/pgSQL eljárási nyelv|
> |[plv8](https://plv8.github.io/)                         | 2.3.11          | PL/JavaScript (V8) megbízható eljárási nyelv|
> |[PostGIS](https://www.postgis.net/)                      | 2.5.1           | Geometriai, földrajzi és raszteres térbeli típusok és függvények PostGIS|
> |[postgis_sfcgal](https://www.postgis.net/)               | 2.5.1           | PostGIS SFCGAL függvények|
> |[postgis_tiger_geocoder](https://www.postgis.net/)       | 2.5.1           | PostGIS Tiger geocoder és fordított geocoder|
> |[postgis_topology](https://postgis.net/docs/Topology.html)             | 2.5.1           | PostGIS-topológia térbeli típusai és funkciói|
> |[postgres_fdw](https://www.postgresql.org/docs/11/static/postgres-fdw.html)                 | 1.0             | idegen adatburkolók távoli PostgreSQL-kiszolgálókhoz|
> |[tablefunc](https://www.postgresql.org/docs/11/static/tablefunc.html)                    | 1.0             | a teljes táblákat, például a kereszttáblás funkciókat kezelő függvények|
> |[unaccent](https://www.postgresql.org/docs/11/static/unaccent.html)                     | 1.1             | ékezeteket eltávolító szöveges keresési szótár|
> |[uuid-ossp](https://www.postgresql.org/docs/11/static/uuid-ossp.html)                    | 1.1             | univerzálisan egyedi azonosítók (UUID-EK) előállítása|


## <a name="pg_stat_statements"></a>pg_stat_statements
A pg_stat_statements-bővítmény előre be van töltve minden Azure Database for PostgreSQL-kiszolgálón, így biztosíthatja az SQL-utasítások végrehajtási statisztikáinak nyomon követését.
A beállítás `pg_stat_statements.track`, amely azt szabályozza, hogy a bővítmény mely utasításokat számítja ki, az `top`alapértelmezett érték, ami azt jelenti, hogy az ügyfelek által közvetlenül kiadott összes utasítás nyomon van követve. A két másik követési szint `none` a `all`és a. Ez a beállítás kiszolgálói paraméterként konfigurálható a Azure Portalon [](https://docs.microsoft.com/azure/postgresql/howto-configure-server-parameters-using-portal) vagy az [Azure CLI](https://docs.microsoft.com/azure/postgresql/howto-configure-server-parameters-using-cli)-n keresztül.

A lekérdezés végrehajtási információi pg_stat_statements és a kiszolgáló teljesítményére gyakorolt hatás a különböző SQL-utasítások naplózása között kompromisszumot jelent. Ha nem használja aktívan a pg_stat_statements bővítményt, javasoljuk, hogy állítsa `pg_stat_statements.track` a `none`következőre:. Vegye figyelembe, hogy egyes harmadik féltől származó figyelési szolgáltatások pg_stat_statements támaszkodhat a lekérdezési teljesítmény megállapítására, ezért győződjön meg arról, hogy ez a helyzet az Ön számára.

## <a name="dblink-and-postgres_fdw"></a>dblink és postgres_fdw
a dblink és a postgres_fdw lehetővé teszi, hogy egy PostgreSQL-kiszolgálóról egy másikra, vagy ugyanabban a kiszolgálón található másik adatbázishoz kapcsolódjon. A fogadó kiszolgálónak engedélyeznie kell a kapcsolódást a küldő kiszolgálóról a tűzfalon keresztül. Ha ezekkel a bővítményekkel csatlakozik Azure Database for PostgreSQL kiszolgálók között, ezt az "Azure-szolgáltatásokhoz való hozzáférés engedélyezése" beállítással teheti meg. Erre akkor is szükség van, ha a bővítmények használatával szeretne visszakapcsolni ugyanarra a kiszolgálóra. Az "Azure-szolgáltatásokhoz való hozzáférés engedélyezése" beállítás megtalálhatók a postgres-kiszolgáló Azure Portal lapján, a kapcsolat biztonsága lehetőségnél. Az "Azure-szolgáltatásokhoz való hozzáférés engedélyezése" beállítás bekapcsolásával az összes Azure-beli IP-címet az engedélyezési listán helyezheti el.

A Azure Database for PostgreSQL kimenő kapcsolatai jelenleg nem támogatottak, kivéve a más Azure Database for PostgreSQL kiszolgálókhoz való kapcsolódást.

## <a name="uuid"></a>uuid
Ha az UUID-ossp bővítményből tervezi használni a használatát, érdemes lehet `uuid_generate_v4()` `gen_random_uuid()` összehasonlítást alkalmazni a pgcrypto-bővítménnyel a teljesítménnyel kapcsolatos előnyökhöz.


## <a name="timescaledb"></a>TimescaleDB
A TimescaleDB egy idősorozat-adatbázis, amely a PostgreSQL-bővítményként van csomagolva. A TimescaleDB időalapú analitikai funkciókat, optimalizálásokat és skálázási postgres biztosít az idősoros számítási feladatokhoz.

[További információ a TimescaleDB-ről](https://docs.timescale.com/latest): az [időskála, Inc.](https://www.timescale.com/) bejegyzett védjegye.

### <a name="installing-timescaledb"></a>A TimescaleDB telepítése
A TimescaleDB telepítéséhez fel kell vennie azt a kiszolgáló megosztott előtelepítő könyvtáraiba. A postgres `shared_preload_libraries` paraméterének módosítása a **kiszolgáló** újraindítását igényli. A paramétereket a [Azure Portal](howto-configure-server-parameters-using-portal.md) vagy az [Azure CLI](howto-configure-server-parameters-using-cli.md)használatával módosíthatja.

> [!NOTE]
> A TimescaleDB a 9,6-es és a 10-es Azure Database for PostgreSQL-verziókon engedélyezhető

A [Azure Portal](https://portal.azure.com/)használata:

1. Válassza ki az Azure Database for PostgreSQL kiszolgálóját.

2. Az oldalsávon válassza a **kiszolgálói paraméterek**lehetőséget.

3. Keresse meg a `shared_preload_libraries` paramétert.

4. Válassza a **TimescaleDB**lehetőséget.

5. A módosítások megőrzése érdekében válassza a **Mentés** lehetőséget. A módosítás mentése után értesítést kap. 

6. Az értesítés után **indítsa újra** a kiszolgálót a módosítások alkalmazásához. A kiszolgálók újraindításával kapcsolatos további információkért lásd: [Azure Database for PostgreSQL kiszolgáló](howto-restart-server-portal.md)újraindítása.


Mostantól engedélyezheti a TimescaleDB a postgres-adatbázisban. Kapcsolódjon az adatbázishoz, és adja ki a következő parancsot:
```sql
CREATE EXTENSION IF NOT EXISTS timescaledb CASCADE;
```
> [!TIP]
> Ha hibaüzenet jelenik meg, ellenőrizze, hogy a shared_preload_libraries mentése után újraindította-e [a kiszolgálót](howto-restart-server-portal.md) . 

Most már létrehozhat egy TimescaleDB-hypertable [a semmiből](https://docs.timescale.com/getting-started/creating-hypertables) , vagy áttelepítheti a [meglévő idősoros adatok a PostgreSQL-ben](https://docs.timescale.com/getting-started/migrating-data).


## <a name="next-steps"></a>További lépések
Ha nem látja a használni kívánt bővítményt, tudassa velünk. Szavazzon a meglévő kérelmekre, vagy hozzon létre új visszajelzési kéréseket a [visszajelzési fórumban](https://feedback.azure.com/forums/597976-azure-database-for-postgresql).
