---
title: PostgreSQL-bővítmények használata az Azure Database for postgresql-hez
description: Lehetővé teszi az adatbázis-bővítmények az Azure Database for PostgreSQL használatával funkcióinak bővítése érdekében ismerteti.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 1/22/2019
ms.openlocfilehash: 6c6fec968efdd85eaf6249459f8e1a0384f2ea11
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2019
ms.locfileid: "54462181"
---
# <a name="postgresql-extensions-in-azure-database-for-postgresql"></a>PostgreSQL-bővítmények az Azure Database for postgresql-hez
PostgreSQL lehetővé teszi az adatbázis-bővítményekkel funkcióinak bővítése érdekében. Bővítmények lehetővé teszik több kapcsolódó SQL objektumok együtt kötegelést betöltve, vagy eltávolítja az adatbázisból, egyetlen paranccsal egyetlen csomagban. Után az adatbázis betöltése, bővítmények működhet, mint a beépített funkciók. A PostgreSQL-bővítmények további információkért lásd: [csomagolási kapcsolódó objektumot egy bővítmény](https://www.postgresql.org/docs/9.6/static/extend-extensions.html).

## <a name="how-to-use-postgresql-extensions"></a>PostgreSQL-bővítmények használata
Az adatbázis PostgreSQL-bővítmények kell telepíteni, mielőtt használhatná őket. Egy adott bővítmény telepítéséhez futtassa a [kiterjesztés létrehozása](https://www.postgresql.org/docs/9.6/static/sql-createextension.html) parancsot a psql-jének eszköz a csomagolt objektumok betöltése az adatbázisba.

Azure Database for postgresql-hez jelenleg kulcs bővítmények egy részét támogatja, az alább felsorolt. Felsorolt túlmutató bővítményeket nem támogatott; Azure Database for PostgreSQL szolgáltatás nem hozható létre a saját bővítmény.

## <a name="extensions-supported-by-azure-database-for-postgresql"></a>PostgreSQL-hez készült Azure Database által támogatott bővítmények
Az alábbi táblázatok sorolják fel a standard szintű PostgreSQL-bővítmények által Azure Database for postgresql-hez jelenleg támogatott. Ez az információ érhető el is futtatásával `SELECT * FROM pg_available_extensions;`.

### <a name="data-types-extensions"></a>Adattípusok bővítmények

> [!div class="mx-tableFixed"]
| **Extension** | **Leírás** |
|---|---|
| [chkpass](https://www.postgresql.org/docs/9.6/static/chkpass.html) | Jelszavak automatikus titkosított adattípust biztosít. |
| [citext](https://www.postgresql.org/docs/9.6/static/citext.html) | Egy karakterlánc kis-és karaktertípust biztosít. |
| [cube](https://www.postgresql.org/docs/9.6/static/cube.html) | Többdimenziós kockák biztosít egy adattípust. |
| [hstore](https://www.postgresql.org/docs/9.6/static/hstore.html) | Biztosít egy adattípus tárolása kulcs-érték párok készletét. |
| [nem](https://www.postgresql.org/docs/9.6/static/isn.html) | Az adattípusok szabványok számozása nemzetközi termék biztosít. |
| [ltree](https://www.postgresql.org/docs/9.6/static/ltree.html) | Hierarchikus faszerkezetben struktúrák adattípust biztosít. |

### <a name="functions-extensions"></a>Functions-bővítmények

> [!div class="mx-tableFixed"]
| **Extension** | **Leírás** |
|---|---|
| [earthdistance](https://www.postgresql.org/docs/9.6/static/earthdistance.html) | Lehetővé teszi a föld felszínén nagy-kör távokat kiszámításához. |
| [fuzzystrmatch](https://www.postgresql.org/docs/9.6/static/fuzzystrmatch.html) | Meghatározhatja a hasonlóságokat és karakterláncok közötti távolság számos funkciót biztosít. |
| [intarray](https://www.postgresql.org/docs/9.6/static/intarray.html) | Pole null-mentes az egész számok kezelésére szolgáló függvényeket és operátorokat biztosít. |
| [pgcrypto](https://www.postgresql.org/docs/9.6/static/pgcrypto.html) | Titkosítási funkciókat biztosít. |
| [pg\_partman](https://pgxn.org/dist/pg_partman/doc/pg_partman.html) | Kezeli a particionált táblákat idő vagy azonosítót. |
| [pg\_trgm](https://www.postgresql.org/docs/9.6/static/pgtrgm.html) | Függvények és operátorok biztosít alfanumerikus szöveg trigram megfelelt a hasonlóságot meghatározásához. |
| [tablefunc](https://www.postgresql.org/docs/9.6/static/tablefunc.html) | Olyan függvények, amelyeknek teljes táblákat, beleértve a Kereszttábla módosítására. |
| [uuid-ossp](https://www.postgresql.org/docs/9.6/static/uuid-ossp.html) | Univerzálisan egyedi azonosítói (az UUID azonosítók) hoz létre. |

### <a name="full-text-search-extensions"></a>Teljes szöveges keresés bővítmények

> [!div class="mx-tableFixed"]
| **Extension** | **Leírás** |
|---|---|
| [dict\_int](https://www.postgresql.org/docs/9.6/static/dict-int.html) | Egy szöveges keresés szótár sablon biztosít az egész számok. |
| [unaccent](https://www.postgresql.org/docs/9.6/static/unaccent.html) | Szöveg (diakritikus jelentkezik) akcentusokhoz távolít el lexemes keresési szótárba. |

### <a name="index-types-extensions"></a>Index típusú bővítmények

> [!div class="mx-tableFixed"]
| **Extension** | **Leírás** |
|---|---|
| [btree\_gin](https://www.postgresql.org/docs/9.6/static/btree-gin.html) | Minta GIN üzemeltető osztályok bizonyos adattípusokkal viselkedése hasonló B-fa megvalósító biztosít. |
| [btree\_gist](https://www.postgresql.org/docs/9.6/static/btree-gist.html) | GiST index üzemeltető osztályok B-fa megvalósító biztosít. |

### <a name="language-extensions"></a>Nyelvi kiterjesztések

> [!div class="mx-tableFixed"]
| **Extension** | **Leírás** |
|---|---|
| [plpgsql](https://www.postgresql.org/docs/9.6/static/plpgsql.html) | PL/pgSQL oszlopszinten eljárási nyelv. |
| [plv8](https://plv8.github.io/) | A Javascript nyelvi bővítmény, amely a tárolt eljárások, eseményindítók és egyéb is használható a PostgreSQL-hez. |

### <a name="miscellaneous-extensions"></a>Személyi bővítmények

> [!div class="mx-tableFixed"]
| **Extension** | **Leírás** |
|---|---|
| [PG\_buffercache](https://www.postgresql.org/docs/9.6/static/pgbuffercache.html) | Arra szolgál, hogy mi történik a megosztott pufferből gyorsítótárban valós időben vizsgálja. |
| [pg\_prewarm](https://www.postgresql.org/docs/9.6/static/pgprewarm.html) | Lehetővé teszi a puffer gyorsítótárba relációs adatok betöltésére. |
| [PG\_stat\_utasítások](https://www.postgresql.org/docs/9.6/static/pgstatstatements.html) | Arra szolgál, hogy a kiszolgáló által végrehajtott összes SQL-utasítások végrehajtási statisztikák nyomon követése. (Lásd alább a kiterjesztés egy megjegyzés). |
| [pgrowlocks](https://www.postgresql.org/docs/9.6/static/pgrowlocks.html) | Arra szolgál, hogy megjeleníti a sorszintű zárolási kapcsolatos adatokat. |
| [pgstattuple](https://www.postgresql.org/docs/9.6/static/pgstattuple.html) | Arra szolgál, hogy tuple szintű statisztika megjelenítése. |
| [postgres\_fdw](https://www.postgresql.org/docs/9.6/static/postgres-fdw.html) | A data-idegen burkoló külső PostgreSQL-kiszolgálót a tárolt adatok elérésére használt. (Lásd alább a kiterjesztés egy megjegyzés).|
| [hypopg](https://hypopg.readthedocs.io/en/latest/) | Elméleti indexekhez nem követelnek CPU vagy a lemez létrehozása módszert biztosít. |
| [dblink](https://www.postgresql.org/docs/current/dblink.html) | A modul, amely támogatja a többi PostgreSQL adatbázis-kapcsolatok egy adatbázis-munkameneten belül. (Lásd alább a kiterjesztés egy megjegyzés). |


### <a name="postgis-extensions"></a>PostGIS bővítmények

> [!div class="mx-tableFixed"]
| **Extension** | **Leírás** |
|---|---|
| [PostGIS](http://www.postgis.net/), postgis\_topológia, postgis\_tiger\_geocoder, postgis\_sfcgal | Térbeli és földrajzi objektum a PostgreSQL-hez. |
| cím\_standardizer, cím\_standardizer\_adatok\_velünk a kapcsolatot | Használja az alkotóelemeket be egy címet elemezni. Támogatja a geokódolás cím normalizálási lépés segítségével. |
| [pgrouting](https://pgrouting.org/) | Kiterjeszti a PostGIS / PostgreSQL térinformatikai adatbázis adataival kínálnak térinformatikai funkcióit útválasztási. |


### <a name="using-pgstatstatements"></a>Pg_stat_statements használatával
A [pg\_stat\_utasítások bővítmény](https://www.postgresql.org/docs/9.6/static/pgstatstatements.html) előre betöltött minden egyes, Azure Database for PostgreSQL-kiszolgáló használatával nyomon követheti az SQL-utasítások végrehajtási statisztikákat az azt jelenti, hogy a rendszer.
A beállítás `pg_stat_statements.track`, amely szabályozza, mely utasítások számításba vesszük, az alapértelmezett érték a bővítmény által `top`, ami azt jelenti, közvetlenül az ügyfelek által kiállított összes utasításokat követi. A két egyéb nyomkövetési szintek a következők `none` és `all`. Ez a beállítás nem konfigurálható keresztül kiszolgáló paraméterként a [az Azure portal](https://docs.microsoft.com/azure/postgresql/howto-configure-server-parameters-using-portal) vagy a [Azure CLI-vel](https://docs.microsoft.com/azure/postgresql/howto-configure-server-parameters-using-cli).

A lekérdezés végrehajtási adatok pg_stat_statements biztosít, és a kiszolgáló teljesítményére gyakorolt magával van, minden egyes SQL-utasítás naplózza. Ha Ön nem használja aktívan a pg_stat_statements bővítményt, azt javasoljuk, hogy állítsa `pg_stat_statements.track` való `none`. Vegye figyelembe, hogy egyes szolgáltatások, harmadik féltől származó pg_stat_statements lekérdezési teljesítmény elemzéseket kínálnak, ezért győződjön meg arról, hogy ez a helyzet, vagy nem hivatkozhatnak.

### <a name="using-dblink-and-postgresfdw"></a>Using dblink and postgres_fdw
dblink és postgres_fdw lehetővé teszi a másikra, vagy egy másik adatbázisba ugyanazon a kiszolgálón egy PostgreSQL-kiszolgálóhoz csatlakozni. A fogadó kiszolgálón kell, hogy a küldő kiszolgáló a tűzfalon keresztül érkező kapcsolatokat. Amikor ezek a bővítmények segítségével csatlakozik, Azure Database for PostgreSQL-kiszolgálók között, ezt "Azure-szolgáltatásokhoz való hozzáférés engedélyezése" ON értékre állításával teheti. Ez is van szükség, ha a bővítmények az iterációhoz vissza ugyanazon a kiszolgálón a használandó. Az "Azure-szolgáltatásokhoz való hozzáférés engedélyezése" beállítás az Postgres server kapcsolatbiztonság alatt az Azure portal oldalán található. "Hozzáférés engedélyezése az Azure-szolgáltatások" listáinak az összes Azure IP-címek bekapcsolásával.


## <a name="next-steps"></a>További lépések
Ha nem lát, amelyet szeretne használni, ossza meg velünk bővítmény. Szavazzon meglévő kérelmeket, vagy hozzon létre új visszajelzést és a kéréseket a [ügyfél-visszajelzési fórumán](https://feedback.azure.com/forums/597976-azure-database-for-postgresql).
