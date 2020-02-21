---
title: Extensions – nagy kapacitású (Citus) – Azure Database for PostgreSQL
description: Ismerteti az adatbázis funkcióinak kiterjesztését Azure Database for PostgreSQL-nagy kapacitású (Citus) bővítmények használatával
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 02/18/2020
ms.openlocfilehash: 84b28096904db49f98c16601c5927928ad38743b
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/20/2020
ms.locfileid: "77485403"
---
# <a name="postgresql-extensions-in-azure-database-for-postgresql--hyperscale-citus"></a>PostgreSQL-bővítmények Azure Database for PostgreSQLban – nagy kapacitású (Citus)

A PostgreSQL lehetővé teszi az adatbázis funkcióinak bővítését bővítmények használatával. A bővítmények lehetővé teszik több kapcsolódó SQL-objektum összefoglalását egyetlen csomagban, amelyet egyetlen paranccsal lehet betölteni vagy eltávolítani az adatbázisból. Az adatbázisba való betöltés után a bővítmények a beépített funkciókhoz hasonlóan működhetnek. A PostgreSQL-bővítményekkel kapcsolatos további információkért lásd: [kapcsolódó objektumok kicsomagolása bővítménybe](https://www.postgresql.org/docs/current/static/extend-extensions.html).

## <a name="use-postgresql-extensions"></a>PostgreSQL-bővítmények használata

A PostgreSQL-bővítményeket a használatuk előtt telepíteni kell az adatbázisba. Egy adott bővítmény telepítéséhez futtassa a [bővítmény létrehozása](https://www.postgresql.org/docs/current/static/sql-createextension.html) parancsot a psql eszközből a csomagolt objektumok adatbázisba való betöltéséhez.

A Azure Database for PostgreSQL-nagy kapacitású (Citus) jelenleg az itt felsorolt kulcs-kiterjesztések egy részhalmazát támogatja. A felsorolt bővítmények nem támogatottak. A Azure Database for PostgreSQL nem hozhat létre saját bővítményt.

## <a name="extensions-supported-by-azure-database-for-postgresql"></a>A Azure Database for PostgreSQL által támogatott bővítmények

A következő táblázat a Azure Database for PostgreSQL által jelenleg támogatott szabványos PostgreSQL-bővítményeket sorolja fel. Ezek az információk a `SELECT * FROM pg_available_extensions;`futtatásával is elérhetők.

### <a name="data-types-extensions"></a>Adattípusok bővítményei

> [!div class="mx-tableFixed"]
> | **Kiterjesztés** | **Leírás** |
> |---|---|
> | [citext](https://www.postgresql.org/docs/current/static/citext.html) | Kis-és nagybetűket nem megkülönböztető karakterlánc-típust biztosít. |
> | [adatkocka](https://www.postgresql.org/docs/current/static/cube.html) | Adattípust biztosít a többdimenziós kockákhoz. |
> | [hstore](https://www.postgresql.org/docs/current/static/hstore.html) | Adattípust biztosít a kulcs-érték párok készletének tárolására. |
> | [HLL](https://github.com/citusdata/postgresql-hll) | HyperLogLog adatstruktúrát biztosít. |
> | [helytelen átvitt](https://www.postgresql.org/docs/current/static/isn.html) | Adattípusokat biztosít a nemzetközi termékek számozási szabványainak. |
> | [Lo](https://www.postgresql.org/docs/current/lo.html) | Nagyméretű objektumok karbantartása. |
> | [ltree](https://www.postgresql.org/docs/current/static/ltree.html) | Adattípust biztosít a hierarchikus fastruktúrához hasonló struktúrákhoz. |
> | [seg](https://www.postgresql.org/docs/current/seg.html) | A vonalszakasz vagy a lebegőpontos intervallumok ábrázolására szolgáló adattípus. |
> | [legjobb n](https://github.com/citusdata/postgresql-topn/) | Adja meg a Top-n JSONB. |

### <a name="full-text-search-extensions"></a>Teljes szöveges keresési bővítmények

> [!div class="mx-tableFixed"]
> | **Kiterjesztés** | **Leírás** |
> |---|---|
> | [dict\_int](https://www.postgresql.org/docs/current/static/dict-int.html) | Szöveges keresési szótári sablont biztosít az egész számokhoz. |
> | [dict\_xsyn](https://www.postgresql.org/docs/current/dict-xsyn.html) | Szöveges keresési szótár sablon a kiterjesztett szinonimák feldolgozásához. |
> | [nem ékezetes](https://www.postgresql.org/docs/current/static/unaccent.html) | Szöveges keresési szótár, amely eltávolítja az ékezeteket (mellékjelek jeleit) a lexemes. |

### <a name="functions-extensions"></a>Functions-bővítmények

> [!div class="mx-tableFixed"]
> | **Kiterjesztés** | **Leírás** |
> |---|---|
> | [autoinc](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.7) | Függvények az automatikusan növekvő mezőkhöz. |
> | [earthdistance](https://www.postgresql.org/docs/current/static/earthdistance.html) | Lehetővé teszi a nagy kör alakú távolságok kiszámítását a Föld felszínén. |
> | [fuzzystrmatch](https://www.postgresql.org/docs/current/static/fuzzystrmatch.html) | Számos függvényt biztosít a karakterláncok közötti hasonlóságok és távolságok meghatározásához. |
> | [\_Felhasználónév beszúrása](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.8) | A táblákat módosító függvények. |
> | [intagg](https://www.postgresql.org/docs/current/intagg.html) | Egész számú gyűjtő és enumerálás (elavult). |
> | [intarray](https://www.postgresql.org/docs/current/static/intarray.html) | Függvényeket és operátorokat biztosít az egész számok null nélküli tömbbe való manipulálására. |
> | [moddatetime](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.9) | Függvények a legutóbbi módosítási idő nyomon követéséhez. |
> | [pgcrypto](https://www.postgresql.org/docs/current/static/pgcrypto.html) | Titkosítási funkciókat biztosít. |
> | [PG\_parti](https://pgxn.org/dist/pg_partman/doc/pg_partman.html) | A particionált táblákat idő vagy azonosító alapján kezeli. |
> | [PG\_trgm](https://www.postgresql.org/docs/current/static/pgtrgm.html) | A függvényeket és operátorokat biztosít az alfanumerikus szöveg hasonlóságának meghatározásához a trigram megfeleltetése alapján. |
> | [refint](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.5) | A hivatkozási integritás megvalósítására szolgáló függvények (elavult). |
> | munkamenet-\_elemzés | Függvények a hstore-tömbök lekérdezéséhez. |
> | [tablefunc](https://www.postgresql.org/docs/current/static/tablefunc.html) | Olyan függvényeket biztosít, amelyek a teljes táblákat, például a kereszttáblás funkciókat kezelik. |
> | [TCN](https://www.postgresql.org/docs/current/tcn.html) | Aktivált változási értesítések. |
> | [timetravel](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.6) | Az időutazás megvalósításához szükséges függvények. |
> | [UUID – ossp](https://www.postgresql.org/docs/current/static/uuid-ossp.html) | Univerzálisan egyedi azonosítókat (UUID-ket) generál. |

### <a name="hyperscale-extensions"></a>Nagy kapacitású-bővítmények

> [!div class="mx-tableFixed"]
> | **Kiterjesztés** | **Leírás** |
> |---|---|
> | [citus](https://github.com/citusdata/citus) | Citus elosztott adatbázis. |
> | Szilánk\_-kiegyensúlyozó | A kiszolgálókon tárolt adatok biztonságos átszámolása csomópontok hozzáadása vagy eltávolítása esetén. |

### <a name="index-types-extensions"></a>Index típusú bővítmények

> [!div class="mx-tableFixed"]
> | **Kiterjesztés** | **Leírás** |
> |---|---|
> | [Bloom](https://www.postgresql.org/docs/current/bloom.html) | A Bloom hozzáférési módszere – aláírási fájl alapú index. |
> | [fa\_gin](https://www.postgresql.org/docs/current/static/btree-gin.html) | Olyan minta GIN operátori osztályokat biztosít, amelyek bizonyos adattípusok esetén B-fa viselkedést implementálnak. |
> | [fa\_lényege](https://www.postgresql.org/docs/current/static/btree-gist.html) | A "B" fát implementáló lényegi index operátori osztályokat biztosít. |

### <a name="language-extensions"></a>Nyelvi bővítmények

> [!div class="mx-tableFixed"]
> | **Kiterjesztés** | **Leírás** |
> |---|---|
> | [plpgsql](https://www.postgresql.org/docs/current/static/plpgsql.html) | PL/pgSQL betölthető eljárási nyelv. |

### <a name="miscellaneous-extensions"></a>Egyéb bővítmények

> [!div class="mx-tableFixed"]
> | **Kiterjesztés** | **Leírás** |
> |---|---|
> | [AdminPack](https://www.postgresql.org/docs/current/adminpack.html) | A PostgreSQL-hez készült felügyeleti függvények. |
> | [amcheck](https://www.postgresql.org/docs/current/amcheck.html) | Függvények a kapcsolatok integritásának ellenőrzéséhez. |
> | [fájl\_FDW](https://www.postgresql.org/docs/current/file-fdw.html) | Idegen adatburkoló a lapos fájlokhoz való hozzáféréshez. |
> | [pageinspect](https://www.postgresql.org/docs/current/pageinspect.html) | Vizsgálja meg az adatbázis-lapok tartalmát alacsony szinten. |
> | [PG\_buffercache](https://www.postgresql.org/docs/current/static/pgbuffercache.html) | A lehetővé teszi a megosztott puffer gyorsítótárában zajló események valós idejű vizsgálatát. |
> | [PG\_cron](https://github.com/citusdata/pg_cron) | Feladatütemező a PostgreSQL-hez. |
> | [PG\_freespacemap](https://www.postgresql.org/docs/current/pgfreespacemap.html) | Vizsgálja meg a szabad terület leképezését (MSZÁ). |
> | [PG\_előmelegített](https://www.postgresql.org/docs/current/static/pgprewarm.html) | Lehetővé teszi a kapcsolatok betöltését a puffer gyorsítótárába. |
> | [PG\_stat\_utasítások](https://www.postgresql.org/docs/current/static/pgstatstatements.html) | A kiszolgáló által végrehajtott összes SQL-utasítás végrehajtási statisztikáinak nyomon követését teszi lehetővé. A bővítménysel kapcsolatos információkért tekintse meg a "pg_stat_statements" szakaszt. |
> | [PG\_láthatóság](https://www.postgresql.org/docs/current/pgvisibility.html) | Vizsgálja meg a láthatósági térképet (VM) és az oldal szintű láthatósági információkat. |
> | [pgrowlocks](https://www.postgresql.org/docs/current/static/pgrowlocks.html) | Lehetővé teszi a sor szintű zárolási információk megjelenítését. |
> | [pgstattuple](https://www.postgresql.org/docs/current/static/pgstattuple.html) | A rekord szintű statisztikák megjelenítését teszi lehetővé. |
> | [postgres\_FDW](https://www.postgresql.org/docs/current/static/postgres-fdw.html) | A külső PostgreSQL-kiszolgálókon tárolt adatforrásokhoz való hozzáféréshez használt idegen adatburkoló. A bővítménysel kapcsolatos információkért tekintse meg a "dblink és postgres_fdw" szakaszt.|
> | [sslinfo](https://www.postgresql.org/docs/current/sslinfo.html) | Az SSL-tanúsítványokkal kapcsolatos információk. |
> | [TSM\_System\_sorok](https://www.postgresql.org/docs/current/tsm-system-rows.html) | TABLESAMPLE metódus, amely a sorok számát korlátként fogadja el. |
> | [TSM\_a System\_idő](https://www.postgresql.org/docs/current/tsm-system-time.html) | TABLESAMPLE metódus, amely a korlátnak megfelelő időt fogad el ezredmásodpercben. |
> | [hypopg](https://hypopg.readthedocs.io/en/latest/) | A olyan feltételezett indexek létrehozására szolgál, amelyek nem a CPU-t vagy a lemezt terhelik. |
> | [dblink](https://www.postgresql.org/docs/current/dblink.html) | Egy modul, amely támogatja a más PostgreSQL-adatbázisokhoz való kapcsolódást egy adatbázis-munkameneten belülről. A bővítménysel kapcsolatos információkért tekintse meg a "dblink és postgres_fdw" szakaszt. |
> | [xml2](https://www.postgresql.org/docs/current/xml2.html) | XPath-lekérdezés és XSLT. |


### <a name="postgis-extensions"></a>PostGIS-bővítmények

> [!div class="mx-tableFixed"]
> | **Kiterjesztés** | **Leírás** |
> |---|---|
> | [PostGIS](https://www.postgis.net/), PostGIS\_topológia, PostGIS\_Tiger\_geocoder, PostGIS\_sfcgal | A PostgreSQL térbeli és földrajzi objektumai. |
> | a\_a szabványosító, a\_a szabványosító\_az\_US | A címek összetevőire való elemzéséhez használatos. A helymeghatározáshoz-címek normalizálása lépésének támogatásához használatos. |
> | PostGIS\_sfcgal | PostGIS SFCGAL függvények. |
> | PostGIS\_Tiger\_geocoder | PostGIS Tiger geocoder és fordított geocoder. |
> | PostGIS\_topológia | PostGIS-topológia térbeli típusai és funkciói |


## <a name="pg_stat_statements"></a>pg_stat_statements
A [pg\_stat\_utasítások bővítmény](https://www.postgresql.org/docs/current/pgstatstatements.html) előre be van töltve minden Azure Database for PostgreSQL-kiszolgálón, így biztosítva az SQL-utasítások végrehajtási statisztikáinak nyomon követését.

A beállítás `pg_stat_statements.track` szabályozza, hogy a bővítmény hogyan számítja ki az utasításokat. Alapértelmezés szerint `top`, ami azt jelenti, hogy az ügyfelek által közvetlenül kiadott összes utasítás nyomon van követve. A két másik követési szint `none` és `all`. Ez a beállítás kiszolgálói paraméterként konfigurálható a [Azure Portalon](https://docs.microsoft.com/azure/postgresql/howto-configure-server-parameters-using-portal) vagy az [Azure CLI](https://docs.microsoft.com/azure/postgresql/howto-configure-server-parameters-using-cli)-n keresztül.

A lekérdezés végrehajtási információi pg_stat_statements biztosít, valamint a kiszolgáló teljesítményére gyakorolt hatás, ahogy az egyes SQL-utasítások bejelentkezik. Ha nem használja aktívan a pg_stat_statements bővítményt, javasoljuk, hogy állítsa be a `pg_stat_statements.track`t `none`re. Előfordulhat, hogy egyes harmadik féltől származó figyelési szolgáltatások a lekérdezési teljesítmény megállapítására támaszkodnak pg_stat_statements, ezért ellenőrizze, hogy ez a helyzet-e az Ön számára.

## <a name="dblink-and-postgres_fdw"></a>dblink és postgres_fdw
A dblink és a postgres_fdw használatával csatlakozhat egy PostgreSQL-kiszolgálóról egy másikhoz, vagy egy kiszolgálón található másik adatbázishoz. A fogadó kiszolgálónak engedélyeznie kell a kapcsolódást a küldő kiszolgálóról a tűzfalon keresztül. Ha ezeket a bővítményeket Azure Database for PostgreSQL-kiszolgálók közötti kapcsolathoz szeretné használni, állítsa be az **Azure-szolgáltatásokhoz való hozzáférés engedélyezése** a következőre:. Ezt a beállítást akkor is be kell kapcsolni, ha a bővítményekkel vissza kíván térni ugyanarra a kiszolgálóra. Az **Azure-szolgáltatásokhoz való hozzáférés engedélyezése** beállítás a postgres-kiszolgáló Azure Portal lapján található, a **kapcsolat biztonsága**lehetőség alatt. Az **Azure-szolgáltatásokhoz való hozzáférés engedélyezése** az összes Azure-beli IP-címen.

Jelenleg a Azure Database for PostgreSQL kimenő kapcsolatai nem támogatottak, kivéve a más Azure Database for PostgreSQL kiszolgálókhoz való kapcsolódást.
