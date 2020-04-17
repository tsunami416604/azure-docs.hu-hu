---
title: Bővítmények – Nagykapacitású (Citus) – Azure-adatbázis a PostgreSQL-hez
description: Az adatbázis funkcióinak kiterjesztésének lehetősége az Azure Database for PostgreSQL – Hyperscale (Citus) bővítményeinek használatával
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 04/16/2020
ms.openlocfilehash: 5d0798c77135b15e26c9787d9844cd9525cf12c5
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81532017"
---
# <a name="postgresql-extensions-in-azure-database-for-postgresql--hyperscale-citus"></a>PostgreSQL-bővítmények az Azure Database for PostgreSQL -accessy – Hyperscale (Citus)

A PostgreSQL lehetővé teszi az adatbázis funkcionalitásának kiterjesztését bővítmények használatával. A bővítmények lehetővé teszik több kapcsolódó SQL-objektum egyetlen csomagban történő összekapcsolását, amelyek egyetlen paranccsal tölthetők be vagy távolíthatók el az adatbázisból. Az adatbázisba való betöltés után a bővítmények beépített szolgáltatásokhoz hasonlóan működhetnek. A PostgreSQL-bővítményekről a [Kapcsolódó objektumok csomagja bővítménybe című témakörben](https://www.postgresql.org/docs/current/static/extend-extensions.html)talál további információt.

## <a name="use-postgresql-extensions"></a>PostgreSQL-bővítmények használata

A PostgreSQL-bővítményeket használat előtt telepíteni kell az adatbázisba. Egy adott bővítmény telepítéséhez futtassa a [CREATE EXTENSION](https://www.postgresql.org/docs/current/static/sql-createextension.html) parancsot a psql eszközből a csomagolt objektumok adatbázisba való betöltéséhez.

A PostgreSQL-hez készült Azure Database for PostgreSQL – Hyperscale (Citus) jelenleg támogatja a kulcsbővítmények egy részhalmazát az itt felsoroltak szerint. A felsoroltaktól eltérő bővítmények nem támogatottak. Nem hozhat létre saját bővítményt az Azure Database for PostgreSQL használatával.

## <a name="extensions-supported-by-azure-database-for-postgresql"></a>Az Azure Database for PostgreSQL által támogatott bővítmények

Az alábbi táblázatok azokat a szabványos PostgreSQL-bővítményeket sorolják fel, amelyeket az Azure Database for PostgreSQL jelenleg támogat. Ez az információ a `SELECT * FROM pg_available_extensions;`futtatásával is elérhető.

### <a name="data-types-extensions"></a>Adattípus-bővítmények

> [!div class="mx-tableFixed"]
> | **Mellék** | **Leírás** |
> |---|---|
> | [citext](https://www.postgresql.org/docs/current/static/citext.html) | Kis- és nagybetűk et nem megkülönböztető karakterlánctípust biztosít. |
> | [Kocka](https://www.postgresql.org/docs/current/static/cube.html) | Többdimenziós kockák adattípusát adja meg. |
> | [háruház](https://www.postgresql.org/docs/current/static/hstore.html) | Adattípust biztosít a kulcsérték-párok halmazainak tárolására. |
> | [hll](https://github.com/citusdata/postgresql-hll) | HyperLogLog adatstruktúrát biztosít. |
> | [Isn](https://www.postgresql.org/docs/current/static/isn.html) | A nemzetközi termékszámozási szabványok adattípusait tartalmazza. |
> | [Lo](https://www.postgresql.org/docs/current/lo.html) | Nagy objektum karbantartás. |
> | [itree (ifa)](https://www.postgresql.org/docs/current/static/ltree.html) | Adattípust biztosít a hierarchikus faszerű struktúrákhoz. |
> | [Seg](https://www.postgresql.org/docs/current/seg.html) | A vonalszakaszok vagy lebegőpontos intervallumok ábrázolásához szolgáló adattípus. |
> | [felül](https://github.com/citusdata/postgresql-topn/) | Írja be a top-n JSONB típust. |

### <a name="full-text-search-extensions"></a>Teljes szöveges keresési bővítmények

> [!div class="mx-tableFixed"]
> | **Mellék** | **Leírás** |
> |---|---|
> | [diktált\_int](https://www.postgresql.org/docs/current/static/dict-int.html) | Szövegkereső szótársablont biztosít egész számokhoz. |
> | [diktált\_xsyn](https://www.postgresql.org/docs/current/dict-xsyn.html) | Szövegkereső szótársablon a kiterjesztett szinonimafeldolgozáshoz. |
> | [kiemelés nélkül](https://www.postgresql.org/docs/current/static/unaccent.html) | Szövegkereső szótár, amely eltávolítja az ékezeteket (mellékjeleket) a lexemes-ből. |

### <a name="functions-extensions"></a>Függvénybővítmények

> [!div class="mx-tableFixed"]
> | **Mellék** | **Leírás** |
> |---|---|
> | [autoinc](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.7) | Mezők automatikus növekményezéséhez szükséges függvények. |
> | [földtávolság](https://www.postgresql.org/docs/current/static/earthdistance.html) | Lehetővé teszi a föld felszínén lévő nagy körtávolságok kiszámítására. |
> | [fuzzystrmatch között](https://www.postgresql.org/docs/current/static/fuzzystrmatch.html) | Számos függvényt biztosít a karakterláncok hasonlóságának és távolságának meghatározásához. |
> | [felhasználónév beszúrása\_](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.8) | A tábla módosítási végzőinek nyomon követésére szolgáló függvények. |
> | [intagg](https://www.postgresql.org/docs/current/intagg.html) | Egész összesítő és enumerátor (elavult). |
> | [intarray](https://www.postgresql.org/docs/current/static/intarray.html) | Funkciókat és operátorokat biztosít az egész számok nullmentes tömbjeinek kezeléséhez. |
> | [moddatetime](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.9) | Funkciók az utolsó módosítás időpontjának nyomon követéséhez. |
> | [pgcrypto](https://www.postgresql.org/docs/current/static/pgcrypto.html) | Kriptográfiai függvényeket biztosít. |
> | [pg\_partman](https://pgxn.org/dist/pg_partman/doc/pg_partman.html) | A particionált táblákat idő vagy azonosító szerint kezeli. |
> | [pg\_trgm között](https://www.postgresql.org/docs/current/static/pgtrgm.html) | Az alfanumerikus szöveg hasonlóságának meghatározásához funkciókat és operátorokat biztosít a trigrammegfeleltetés alapján. |
> | [refint](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.5) | A hivatkozási integritás megvalósításának funkciói (elavultak). |
> | munkamenet-elemzés\_ | Hstore tömbök lekérdezésére szolgáló függvények. |
> | [asztali](https://www.postgresql.org/docs/current/static/tablefunc.html) | Olyan függvényeket tartalmaz, amelyek teljes táblázatokat kezelnek, beleértve a kereszttáblát is. |
> | [tcn](https://www.postgresql.org/docs/current/tcn.html) | Módosított változásokról szóló értesítések. |
> | [időutazás](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.6) | Az időutazás megvalósításának funkciói. |
> | [uuid-ossp](https://www.postgresql.org/docs/current/static/uuid-ossp.html) | Univerzálisan egyedi azonosítókat (UUID)-t hoz létre. |

### <a name="hyperscale-extensions"></a>Nagy kapacitású kiterjesztések

> [!div class="mx-tableFixed"]
> | **Mellék** | **Leírás** |
> |---|---|
> | [citus között](https://github.com/citusdata/citus) | Citus elosztott adatbázis. |
> | shard\_rebalancer | Csomópont hozzáadása vagy eltávolítása esetén biztonságosan egyensúlyba hozza az adatokat egy kiszolgálócsoportban. |

### <a name="index-types-extensions"></a>Indextípusok bővítményei

> [!div class="mx-tableFixed"]
> | **Mellék** | **Leírás** |
> |---|---|
> | [Virágzás](https://www.postgresql.org/docs/current/bloom.html) | Bloom hozzáférési módszer - aláírás fájlalapú index. |
> | [bfa\_gin](https://www.postgresql.org/docs/current/static/btree-gin.html) | Minta GIN operátori osztályokat biztosít, amelyek bizonyos adattípusokhoz B-fa-szerű viselkedést valósítanak meg. |
> | [bfa\_lényege](https://www.postgresql.org/docs/current/static/btree-gist.html) | GiST indexoperátor-osztályokat biztosít, amelyek a B-fát valósítják meg. |

### <a name="language-extensions"></a>Nyelvi bővítmények

> [!div class="mx-tableFixed"]
> | **Mellék** | **Leírás** |
> |---|---|
> | [plpgsql](https://www.postgresql.org/docs/current/static/plpgsql.html) | PL/pgSQL betölthető eljárási nyelv. |

### <a name="miscellaneous-extensions"></a>Egyéb kiterjesztések

> [!div class="mx-tableFixed"]
> | **Mellék** | **Leírás** |
> |---|---|
> | [adminpack](https://www.postgresql.org/docs/current/adminpack.html) | A PostgreSQL adminisztratív funkciói. |
> | [amcheck](https://www.postgresql.org/docs/current/amcheck.html) | A kapcsolat integritásának ellenőrzésére szolgáló funkciók. |
> | [fájl\_fdw](https://www.postgresql.org/docs/current/file-fdw.html) | Külföldi adatburkoló a fájlokhoz való közvetlen hozzáféréshez. |
> | [oldalvizsgálat](https://www.postgresql.org/docs/current/pageinspect.html) | Az adatbázislapok tartalmának alacsony szintű vizsgálata. |
> | [pg\_puffergyorsítótár](https://www.postgresql.org/docs/current/static/pgbuffercache.html) | Valós időben vizsgálja meg, hogy mi történik a megosztott puffergyorsítótárban. |
> | [pg\_cron](https://github.com/citusdata/pg_cron) | A PostgreSQL munkaütemezője. |
> | [pg\_freespacemap](https://www.postgresql.org/docs/current/pgfreespacemap.html) | Vizsgálja meg a szabad terület térkép (FSM). |
> | [pg\_előmelegen](https://www.postgresql.org/docs/current/static/pgprewarm.html) | A kapcsolati adatok betöltésének módját a puffergyorsítótárba. |
> | [pg\_\_stat utasítások](https://www.postgresql.org/docs/current/static/pgstatstatements.html) | A kiszolgáló által végrehajtott összes SQL-utasítás végrehajtási statisztikájának nyomon követésére szolgál. A kiterjesztéssel kapcsolatos tudnivalókat a "pg_stat_statements" című részben talál. |
> | [pg\_láthatóság](https://www.postgresql.org/docs/current/pgvisibility.html) | Vizsgálja meg a láthatósági térkép (VM) és az oldalszintű láthatósági információkat. |
> | [pgrowlocks (pgrowlocks)](https://www.postgresql.org/docs/current/static/pgrowlocks.html) | A sorszintű zárolási információk megjelenítésének eszköze. |
> | [pgstattuple között](https://www.postgresql.org/docs/current/static/pgstattuple.html) | A négyszeres statisztika megjelenítésének eszköze. |
> | [postgres\_fdw](https://www.postgresql.org/docs/current/static/postgres-fdw.html) | A külső PostgreSQL-kiszolgálókon tárolt adatok eléréséhez használt külföldi adatburkoló. A kiterjesztéssel kapcsolatos információkért lásd a "dblink and postgres_fdw" című részt.|
> | [sslinfo](https://www.postgresql.org/docs/current/sslinfo.html) | Információ az SSL-tanúsítványokról. |
> | [tsm\_\_rendszersorok](https://www.postgresql.org/docs/current/tsm-system-rows.html) | TABLESAMPLE metódus, amely a sorok számát korlátként fogadja el. |
> | [tsm\_\_rendszeridő](https://www.postgresql.org/docs/current/tsm-system-time.html) | TABLESAMPLE metódus, amely az időt ezredmásodpercben fogadja el korlátként. |
> | [hipopg](https://hypopg.readthedocs.io/en/latest/) | Olyan hipotetikus indexek létrehozására is lehetőséget biztosít, amelyek nem kerülnek processzorba vagy lemezbe. |
> | [dblink között](https://www.postgresql.org/docs/current/dblink.html) | Olyan modul, amely támogatja a más PostgreSQL-adatbázisokkal való kapcsolatokat egy adatbázis-munkameneten belül. A kiterjesztéssel kapcsolatos információkért lásd a "dblink and postgres_fdw" című részt. |
> | [xml2](https://www.postgresql.org/docs/current/xml2.html) | XPath lekérdezése és XSLT. |


### <a name="postgis-extensions"></a>PostGIS-bővítmények

> [!div class="mx-tableFixed"]
> | **Mellék** | **Leírás** |
> |---|---|
> | [PostGIS](https://www.postgis.net/),\_postgis topológia,\_\_postgis tigris geokódoló, postgis\_sfcgal | Térbeli és földrajzi objektumok a PostgreSQL számára. |
> | cím\_standardizer,\_cím\_\_standardizer adatok at | Cím alkotóelemekké történő elemzésére szolgál. A geokódolási cím normalizálási lépésének támogatására szolgál. |
> | postgis\_sfcgal | PostGIS SFCGAL funkciók. |
> | postgis\_\_tigris geokódoló | PostGIS tigris geokódoló és fordított geokódoló. |
> | postgis\_topológia | PostGIS topológia térbeli típusok és funkciók. |


## <a name="pg_stat_statements"></a>pg_stat_statements
A [\_pg\_stat utasítások bővítmény](https://www.postgresql.org/docs/current/pgstatstatements.html) előre be van töltve minden Azure Database for PostgreSQL kiszolgálón, hogy az SQL-utasítások végrehajtási statisztikáinak nyomon követését biztosítsa.

A `pg_stat_statements.track` beállítás határozza meg, hogy a bővítmény milyen állításokat számol. Alapértelmezés szerint `top`a , ami azt jelenti, hogy az ügyfelek által közvetlenül kiadott összes utasítás nyomon követhető. A másik két `none` követési `all`szint a és a. Ez a beállítás kiszolgálóparaméterként konfigurálható az [Azure Portalon](https://docs.microsoft.com/azure/postgresql/howto-configure-server-parameters-using-portal) vagy az [Azure CLI-n](https://docs.microsoft.com/azure/postgresql/howto-configure-server-parameters-using-cli)keresztül.

Van egy kompromisszum a lekérdezés végrehajtási információkat, pg_stat_statements biztosít, és a hatása a kiszolgáló teljesítményét, mert naplózza az egyes SQL utasítás. Ha nem használja aktívan a pg_stat_statements bővítményt, `pg_stat_statements.track` `none`azt javasoljuk, hogy állítsa a . Egyes külső monitorozási szolgáltatások támaszkodhat nak pg_stat_statements lekérdezési teljesítmény elemzések, ezért erősítse meg, hogy ez a helyzet az Ön számára, vagy sem.

## <a name="dblink-and-postgres_fdw"></a>dblink és postgres_fdw
A dblink és postgres_fdw segítségével csatlakozhat az egyik PostgreSQL szerverről a másikra, vagy egy másik adatbázishoz ugyanazon a szerveren. A fogadó kiszolgálónak engedélyeznie kell a küldő kiszolgáló tól a tűzfalon keresztülérkező kapcsolatokat. Ha ezeket a bővítményeket szeretné használni az Azure Database for PostgreSQL-kiszolgálók közötti csatlakozáshoz, állítsa be **az Azure-szolgáltatások elérésének engedélyezése** BE beállítást. Akkor is be kell kapcsolnia ezt a beállítást, ha a bővítményeket arra szeretné használni, hogy visszakerüljön ugyanarra a kiszolgálóra. Az **Azure-szolgáltatásokhoz való hozzáférés engedélyezése** beállítás a Postgres-kiszolgáló Azure portalja oldalon, a **Kapcsolatbiztonság**csoportban található. Hozzáférés **engedélyezése az Azure-szolgáltatásokhoz az Azure-szolgáltatásokhoz** az összes Azure IP-szolgáltató tanusaként.

Jelenleg az Azure Database for PostgreSQL kimenő kapcsolatai nem támogatottak, kivéve a PostgreSQL-kiszolgálókhoz tartozó más Azure-adatbázissal való kapcsolatokat.
