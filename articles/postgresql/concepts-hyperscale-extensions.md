---
title: PostgreSQL-bővítmények, Azure Database for PostgreSQL – nagy kapacitású (Citus) (előzetes verzió)
description: Lehetővé teszi az adatbázis-bővítmények az Azure Database for PostgreSQL használatával funkcióinak bővítése érdekében ismerteti.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: 32870f37781b4161de692af91c79fe47efb3737e
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65077320"
---
# <a name="postgresql-extensions-in-azure-database-for-postgresql---hyperscale-citus-preview"></a>PostgreSQL-bővítmények, Azure Database for PostgreSQL – nagy kapacitású (Citus) (előzetes verzió)

PostgreSQL lehetővé teszi az adatbázis-bővítményekkel funkcióinak bővítése érdekében. Bővítmények lehetővé teszik több kapcsolódó SQL objektumok együtt kötegelést betöltve, vagy eltávolítja az adatbázisból, egyetlen paranccsal egyetlen csomagban. Után az adatbázis betöltése, bővítmények működhet, mint a beépített funkciók. A PostgreSQL-bővítmények további információkért lásd: [csomagolási kapcsolódó objektumot egy bővítmény](https://www.postgresql.org/docs/9.6/static/extend-extensions.html).

## <a name="how-to-use-postgresql-extensions"></a>PostgreSQL-bővítmények használata

Az adatbázis PostgreSQL-bővítmények kell telepíteni, mielőtt használhatná őket. Egy adott bővítmény telepítéséhez futtassa a [kiterjesztés létrehozása](https://www.postgresql.org/docs/9.6/static/sql-createextension.html) parancsot a psql-jének eszköz a csomagolt objektumok betöltése az adatbázisba.

Azure Database for postgresql-hez jelenleg kulcs bővítmények egy részét támogatja, az alább felsorolt. Felsorolt túlmutató bővítményeket nem támogatott; Azure Database for PostgreSQL szolgáltatás nem hozható létre a saját bővítmény.

## <a name="extensions-supported-by-azure-database-for-postgresql"></a>PostgreSQL-hez készült Azure Database által támogatott bővítmények

Az alábbi táblázatok sorolják fel a standard szintű PostgreSQL-bővítmények által Azure Database for postgresql-hez jelenleg támogatott. Ez az információ érhető el is futtatásával `SELECT * FROM pg_available_extensions;`.

### <a name="data-types-extensions"></a>Adattípusok bővítmények

> [!div class="mx-tableFixed"]
> | **Extension** | **Leírás** |
> |---|---|
> | [citext](https://www.postgresql.org/docs/9.6/static/citext.html) | Egy karakterlánc kis-és karaktertípust biztosít. |
> | [cube](https://www.postgresql.org/docs/9.6/static/cube.html) | Többdimenziós kockák biztosít egy adattípust. |
> | [hstore](https://www.postgresql.org/docs/9.6/static/hstore.html) | Biztosít egy adattípus tárolása kulcs-érték párok készletét. |
> | [nem](https://www.postgresql.org/docs/9.6/static/isn.html) | Az adattípusok szabványok számozása nemzetközi termék biztosít. |
> | [lo](https://www.postgresql.org/docs/current/lo.html) | Nagy objektum karbantartási. |
> | [ltree](https://www.postgresql.org/docs/9.6/static/ltree.html) | Hierarchikus faszerkezetben struktúrák adattípust biztosít. |
> | [seg](https://www.postgresql.org/docs/current/seg.html) | Jelölő sor szegmensek vagy lebegőpontos időközök adattípust. |
> | [legjobb](https://github.com/citusdata/postgresql-topn/) | Írja be a felső n JSONB. |

### <a name="functions-extensions"></a>Functions-bővítmények

> [!div class="mx-tableFixed"]
> | **Extension** | **Leírás** |
> |---|---|
> | [autoinc](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.7) | Függvények mezők szegmenskulcsként való használatát. |
> | [earthdistance](https://www.postgresql.org/docs/9.6/static/earthdistance.html) | Lehetővé teszi a föld felszínén nagy-kör távokat kiszámításához. |
> | [fuzzystrmatch](https://www.postgresql.org/docs/9.6/static/fuzzystrmatch.html) | Meghatározhatja a hasonlóságokat és karakterláncok közötti távolság számos funkciót biztosít. |
> | [Helyezze be\_felhasználónév](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.8) | Az funkciók ki módosította egy táblát követésére. |
> | [intagg](https://www.postgresql.org/docs/current/intagg.html) | Egész szám gyűjtő és enumeráló modulja (elavult). |
> | [intarray](https://www.postgresql.org/docs/9.6/static/intarray.html) | Pole null-mentes az egész számok kezelésére szolgáló függvényeket és operátorokat biztosít. |
> | [moddatetime](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.9) | Függvények nyomon követése a legutóbbi módosításának időpontja. |
> | [pgcrypto](https://www.postgresql.org/docs/9.6/static/pgcrypto.html) | Titkosítási funkciókat biztosít. |
> | [pg\_partman](https://pgxn.org/dist/pg_partman/doc/pg_partman.html) | Kezeli a particionált táblákat idő vagy azonosítót. |
> | [pg\_trgm](https://www.postgresql.org/docs/9.6/static/pgtrgm.html) | Függvények és operátorok biztosít alfanumerikus szöveg trigram megfelelt a hasonlóságot meghatározásához. |
> | [refint](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.5) | (Elavult) a hivatkozási integritás végrehajtási függvények. |
> | munkamenet\_analytics | Az funkciók hstore tömbök lekérdezéséhez. |
> | [tablefunc](https://www.postgresql.org/docs/9.6/static/tablefunc.html) | Olyan függvények, amelyeknek teljes táblákat, beleértve a Kereszttábla módosítására. |
> | [tcn](https://www.postgresql.org/docs/current/tcn.html) | Aktivált értesítések. |
> | [timetravel](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.6) | Az funkciók megvalósításához az utazási idő. |
> | [uuid-ossp](https://www.postgresql.org/docs/9.6/static/uuid-ossp.html) | Univerzálisan egyedi azonosítói (az UUID azonosítók) hoz létre. |

### <a name="full-text-search-extensions"></a>Teljes szöveges keresés bővítmények

> [!div class="mx-tableFixed"]
> | **Extension** | **Leírás** |
> |---|---|
> | [dict\_int](https://www.postgresql.org/docs/9.6/static/dict-int.html) | Egy szöveges keresés szótár sablon biztosít az egész számok. |
> | [Dict\_xsyn](https://www.postgresql.org/docs/current/dict-xsyn.html) | Szöveges keresési szótár sablon kiterjesztett szinonimát feldolgozás céljából. |
> | [unaccent](https://www.postgresql.org/docs/9.6/static/unaccent.html) | Szöveg (diakritikus jelentkezik) akcentusokhoz távolít el lexemes keresési szótárba. |

### <a name="index-types-extensions"></a>Index típusú bővítmények

> [!div class="mx-tableFixed"]
> | **Extension** | **Leírás** |
> |---|---|
> | [bloom](https://www.postgresql.org/docs/current/bloom.html) | Hozzáférési módszer virágzó - souboru signatury alapú index. |
> | [btree\_gin](https://www.postgresql.org/docs/9.6/static/btree-gin.html) | Minta GIN üzemeltető osztályok bizonyos adattípusokkal viselkedése hasonló B-fa megvalósító biztosít. |
> | [btree\_gist](https://www.postgresql.org/docs/9.6/static/btree-gist.html) | GiST index üzemeltető osztályok B-fa megvalósító biztosít. |

### <a name="language-extensions"></a>Nyelvi kiterjesztések

> [!div class="mx-tableFixed"]
> | **Extension** | **Leírás** |
> |---|---|
> | [plpgsql](https://www.postgresql.org/docs/9.6/static/plpgsql.html) | PL/pgSQL oszlopszinten eljárási nyelv. |

### <a name="hyperscale-extensions"></a>Nagy kapacitású bővítmények

> [!div class="mx-tableFixed"]
> | **Extension** | **Leírás** |
> |---|---|
> | [citus](https://github.com/citusdata/citus) | Citus elosztott adatbázis. |
> | shard\_rebalancer | Csomópont hozzáadása vagy eltávolítása esetén egy kiszolgáló csoportban található adatok biztonságosan újraegyensúlyozására. |

### <a name="miscellaneous-extensions"></a>Személyi bővítmények

> [!div class="mx-tableFixed"]
> | **Extension** | **Leírás** |
> |---|---|
> | [AdminPack](https://www.postgresql.org/docs/current/adminpack.html) | PostgreSQL-hez készült felügyeleti funkciók. |
> | [amcheck](https://www.postgresql.org/docs/current/amcheck.html) | Függvények kapcsolat integritás ellenőrzése. |
> | [fájl\_fdw](https://www.postgresql.org/docs/current/file-fdw.html) | Egybesimított fájl hozzáférés burkoló idegen-adatokat. |
> | [pageinspect](https://www.postgresql.org/docs/current/pageinspect.html) | Alacsony adatbázis-oldalak tartalmának vizsgálata. |
> | [PG\_buffercache](https://www.postgresql.org/docs/9.6/static/pgbuffercache.html) | Arra szolgál, hogy mi történik a megosztott pufferből gyorsítótárban valós időben vizsgálja. |
> | [pg\_cron](https://github.com/citusdata/pg_cron) | A Feladatütemező a PostgreSQL-hez. |
> | [pg\_freespacemap](https://www.postgresql.org/docs/current/pgfreespacemap.html) | Vizsgálja meg a szabad terület térkép (FSM). |
> | [pg\_prewarm](https://www.postgresql.org/docs/9.6/static/pgprewarm.html) | Lehetővé teszi a puffer gyorsítótárba relációs adatok betöltésére. |
> | [PG\_stat\_utasítások](https://www.postgresql.org/docs/9.6/static/pgstatstatements.html) | Arra szolgál, hogy a kiszolgáló által végrehajtott összes SQL-utasítások végrehajtási statisztikák nyomon követése. (Lásd alább a kiterjesztés egy megjegyzés). |
> | [PG\_láthatósága](https://www.postgresql.org/docs/current/pgvisibility.html) | Ellenőrizze a láthatósági térkép (VM) és az oldalszintű látható-e adatokat. |
> | [pgrowlocks](https://www.postgresql.org/docs/9.6/static/pgrowlocks.html) | Arra szolgál, hogy megjeleníti a sorszintű zárolási kapcsolatos adatokat. |
> | [pgstattuple](https://www.postgresql.org/docs/9.6/static/pgstattuple.html) | Arra szolgál, hogy tuple szintű statisztika megjelenítése. |
> | [postgres\_fdw](https://www.postgresql.org/docs/9.6/static/postgres-fdw.html) | A data-idegen burkoló külső PostgreSQL-kiszolgálót a tárolt adatok elérésére használt. (Lásd alább a kiterjesztés egy megjegyzés).|
> | [sslinfo](https://www.postgresql.org/docs/current/sslinfo.html) | SSL-tanúsítványokkal kapcsolatos információk. |
> | [TSM\_rendszer\_sorok](https://www.postgresql.org/docs/current/tsm-system-rows.html) | TABLESAMPLE módszer, amely elfogadja a korlát a sorok száma. |
> | [TSM\_rendszer\_idő](https://www.postgresql.org/docs/current/tsm-system-time.html) | TABLESAMPLE módszer, amely elfogadja a korlátozására ideje (ezredmásodperc). |
> | [hypopg](https://hypopg.readthedocs.io/en/latest/) | Elméleti indexekhez nem követelnek CPU vagy a lemez létrehozása módszert biztosít. |
> | [dblink](https://www.postgresql.org/docs/current/dblink.html) | A modul, amely támogatja a többi PostgreSQL adatbázis-kapcsolatok egy adatbázis-munkameneten belül. (Lásd alább a kiterjesztés egy megjegyzés). |
> | [xml2](https://www.postgresql.org/docs/current/xml2.html) | XPath querying and XSLT. |


### <a name="postgis-extensions"></a>PostGIS bővítmények

> [!div class="mx-tableFixed"]
> | **Extension** | **Leírás** |
> |---|---|
> | [PostGIS](http://www.postgis.net/), postgis\_topológia, postgis\_tiger\_geocoder, postgis\_sfcgal | Térbeli és földrajzi objektum a PostgreSQL-hez. |
> | cím\_standardizer, cím\_standardizer\_adatok\_velünk a kapcsolatot | Használja az alkotóelemeket be egy címet elemezni. Támogatja a geokódolás cím normalizálási lépés segítségével. |
> | postgis\_sfcgal | PostGIS SFCGAL funkciók. |
> | postgis\_tiger\_geocoder | PostGIS tiger geocoder és fordított geocoder. |
> | postgis\_topológia | PostGIS topológia térbeli típusok és funkciók. |


## <a name="pgstatstatements"></a>pg_stat_statements
A [pg\_stat\_utasítások bővítmény](https://www.postgresql.org/docs/9.6/static/pgstatstatements.html) előre betöltött minden egyes, Azure Database for PostgreSQL-kiszolgáló használatával nyomon követheti az SQL-utasítások végrehajtási statisztikákat az azt jelenti, hogy a rendszer.
A beállítás `pg_stat_statements.track`, amely szabályozza, mely utasítások számításba vesszük, az alapértelmezett érték a bővítmény által `top`, ami azt jelenti, közvetlenül az ügyfelek által kiállított összes utasításokat követi. A két egyéb nyomkövetési szintek a következők `none` és `all`. Ez a beállítás nem konfigurálható keresztül kiszolgáló paraméterként a [az Azure portal](https://docs.microsoft.com/azure/postgresql/howto-configure-server-parameters-using-portal) vagy a [Azure CLI-vel](https://docs.microsoft.com/azure/postgresql/howto-configure-server-parameters-using-cli).

A lekérdezés végrehajtási adatok pg_stat_statements biztosít, és a kiszolgáló teljesítményére gyakorolt magával van, minden egyes SQL-utasítás naplózza. Ha Ön nem használja aktívan a pg_stat_statements bővítményt, azt javasoljuk, hogy állítsa `pg_stat_statements.track` való `none`. Vegye figyelembe, hogy egyes szolgáltatások, harmadik féltől származó pg_stat_statements lekérdezési teljesítmény elemzéseket kínálnak, ezért győződjön meg arról, hogy ez a helyzet, vagy nem hivatkozhatnak.

## <a name="dblink-and-postgresfdw"></a>dblink és postgres_fdw
dblink és postgres_fdw lehetővé teszi a másikra, vagy egy másik adatbázisba ugyanazon a kiszolgálón egy PostgreSQL-kiszolgálóhoz csatlakozni. A fogadó kiszolgálón kell, hogy a küldő kiszolgáló a tűzfalon keresztül érkező kapcsolatokat. Amikor ezek a bővítmények segítségével csatlakozik, Azure Database for PostgreSQL-kiszolgálók között, ezt "Azure-szolgáltatásokhoz való hozzáférés engedélyezése" ON értékre állításával teheti. Ez is van szükség, ha a bővítmények az iterációhoz vissza ugyanazon a kiszolgálón a használandó. Az "Azure-szolgáltatásokhoz való hozzáférés engedélyezése" beállítás az Postgres server kapcsolatbiztonság alatt az Azure portal oldalán található. "Hozzáférés engedélyezése az Azure-szolgáltatások" listáinak az összes Azure IP-címek bekapcsolásával.

Jelenleg PostgreSQL-hez készült Azure Database-ből kimenő kapcsolatok nem támogatottak, más, Azure Database for PostgreSQL-kiszolgálók kapcsolatok kivételével.
