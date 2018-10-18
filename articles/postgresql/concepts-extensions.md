---
title: PostgreSQL-bővítmények használata az Azure Database for postgresql-hez
description: Lehetővé teszi az adatbázis-bővítmények az Azure Database for PostgreSQL használatával funkcióinak bővítése érdekében ismerteti.
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 05/30/2018
ms.openlocfilehash: f9216800f0e91d71f1da3f2bac16ecfcbf8b4850
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2018
ms.locfileid: "49376550"
---
# <a name="postgresql-extensions-in-azure-database-for-postgresql"></a>PostgreSQL-bővítmények az Azure Database for postgresql-hez
PostgreSQL lehetővé teszi az adatbázis-bővítményekkel funkcióinak bővítése érdekében. Bővítmények lehetővé teszik több kapcsolódó SQL objektumok együtt kötegelést betöltve, vagy eltávolítja az adatbázisból, egyetlen paranccsal egyetlen csomagban. Után az adatbázis betöltése, bővítmények működhet, mint a beépített funkciók. A PostgreSQL-bővítmények további információkért lásd: [csomagolási kapcsolódó objektumot egy bővítmény](https://www.postgresql.org/docs/9.6/static/extend-extensions.html).

## <a name="how-to-use-postgresql-extensions"></a>PostgreSQL-bővítmények használata
Az adatbázis PostgreSQL-bővítmények kell telepíteni, mielőtt használhatná őket. Egy adott bővítmény telepítéséhez futtassa a [kiterjesztés létrehozása](https://www.postgresql.org/docs/9.6/static/sql-createextension.html) parancsot a psql-jének eszköz a csomagolt objektumok betöltése az adatbázisba.

Azure Database for postgresql-hez jelenleg kulcs bővítmények egy részét támogatja, az alább felsorolt. Felsorolt túlmutató bővítményeket nem támogatott; Azure Database for PostgreSQL szolgáltatás nem hozható létre a saját bővítmény.

## <a name="extensions-supported-by-azure-database-for-postgresql"></a>PostgreSQL-hez készült Azure Database által támogatott bővítmények
Az alábbi táblázatok sorolják fel a standard szintű PostgreSQL-bővítmények által Azure Database for postgresql-hez jelenleg támogatott. Ez az információ érhető el is futtatásával `SELECT * FROM pg_available_extensions;`.

### <a name="data-types-extensions"></a>Adattípusok bővítmények

> [!div class="mx-tableFixed"]
| **Bővítmény** | **Leírás** |
|---|---|
| [chkpass](https://www.postgresql.org/docs/9.6/static/chkpass.html) | Jelszavak automatikus titkosított adattípust biztosít. |
| [citext](https://www.postgresql.org/docs/9.6/static/citext.html) | Egy karakterlánc kis-és karaktertípust biztosít. |
| [adatkocka](https://www.postgresql.org/docs/9.6/static/cube.html) | Többdimenziós kockák biztosít egy adattípust. |
| [hstore](https://www.postgresql.org/docs/9.6/static/hstore.html) | Biztosít egy adattípus tárolása kulcs-érték párok készletét. |
| [nem](https://www.postgresql.org/docs/9.6/static/isn.html) | Az adattípusok szabványok számozása nemzetközi termék biztosít. |
| [ltree](https://www.postgresql.org/docs/9.6/static/ltree.html) | Hierarchikus faszerkezetben struktúrák adattípust biztosít. |

### <a name="functions-extensions"></a>Functions-bővítmények

> [!div class="mx-tableFixed"]
| **Bővítmény** | **Leírás** |
|---|---|
| [earthdistance](https://www.postgresql.org/docs/9.6/static/earthdistance.html) | Lehetővé teszi a föld felszínén nagy-kör távokat kiszámításához. |
| [fuzzystrmatch](https://www.postgresql.org/docs/9.6/static/fuzzystrmatch.html) | Meghatározhatja a hasonlóságokat és karakterláncok közötti távolság számos funkciót biztosít. |
| [intarray](https://www.postgresql.org/docs/9.6/static/intarray.html) | Pole null-mentes az egész számok kezelésére szolgáló függvényeket és operátorokat biztosít. |
| [pgcrypto](https://www.postgresql.org/docs/9.6/static/pgcrypto.html) | Titkosítási funkciókat biztosít. |
| [pg\_partman](https://pgxn.org/dist/pg_partman/doc/pg_partman.html) | Kezeli a particionált táblákat idő vagy azonosítót. |
| [PG\_trgm](https://www.postgresql.org/docs/9.6/static/pgtrgm.html) | Függvények és operátorok biztosít alfanumerikus szöveg trigram megfelelt a hasonlóságot meghatározásához. |
| [tablefunc](https://www.postgresql.org/docs/9.6/static/tablefunc.html) | Olyan függvények, amelyeknek teljes táblákat, beleértve a Kereszttábla módosítására. |
| [uuid-ossp](https://www.postgresql.org/docs/9.6/static/uuid-ossp.html) | Univerzálisan egyedi azonosítói (az UUID azonosítók) hoz létre. |

### <a name="full-text-search-extensions"></a>Teljes szöveges keresés bővítmények

> [!div class="mx-tableFixed"]
| **Bővítmény** | **Leírás** |
|---|---|
| [dict\_int](https://www.postgresql.org/docs/9.6/static/dict-int.html) | Egy szöveges keresés szótár sablon biztosít az egész számok. |
| [unaccent](https://www.postgresql.org/docs/9.6/static/unaccent.html) | Szöveg (diakritikus jelentkezik) akcentusokhoz távolít el lexemes keresési szótárba. |

### <a name="index-types-extensions"></a>Index típusú bővítmények

> [!div class="mx-tableFixed"]
| **Bővítmény** | **Leírás** |
|---|---|
| [fának\_gin](https://www.postgresql.org/docs/9.6/static/btree-gin.html) | Minta GIN üzemeltető osztályok bizonyos adattípusokkal viselkedése hasonló B-fa megvalósító biztosít. |
| [fának\_gist](https://www.postgresql.org/docs/9.6/static/btree-gist.html) | GiST index üzemeltető osztályok B-fa megvalósító biztosít. |

### <a name="language-extensions"></a>Nyelvi kiterjesztések

> [!div class="mx-tableFixed"]
| **Bővítmény** | **Leírás** |
|---|---|
| [plpgsql](https://www.postgresql.org/docs/9.6/static/plpgsql.html) | PL/pgSQL oszlopszinten eljárási nyelv. |

### <a name="miscellaneous-extensions"></a>Személyi bővítmények

> [!div class="mx-tableFixed"]
| **Bővítmény** | **Leírás** |
|---|---|
| [PG\_buffercache](https://www.postgresql.org/docs/9.6/static/pgbuffercache.html) | Arra szolgál, hogy mi történik a megosztott pufferből gyorsítótárban valós időben vizsgálja. |
| [pg\_prewarm](https://www.postgresql.org/docs/9.6/static/pgprewarm.html) | Lehetővé teszi a puffer gyorsítótárba relációs adatok betöltésére. |
| [PG\_stat\_utasítások](https://www.postgresql.org/docs/9.6/static/pgstatstatements.html) | Arra szolgál, hogy a kiszolgáló által végrehajtott összes SQL-utasítások végrehajtási statisztikák nyomon követése. (Lásd alább a kiterjesztés egy megjegyzés). |
| [pgrowlocks](https://www.postgresql.org/docs/9.6/static/pgrowlocks.html) | Arra szolgál, hogy megjeleníti a sorszintű zárolási kapcsolatos adatokat. |
| [pgstattuple](https://www.postgresql.org/docs/9.6/static/pgstattuple.html) | Arra szolgál, hogy tuple szintű statisztika megjelenítése. |
| [postgres\_fdw](https://www.postgresql.org/docs/9.6/static/postgres-fdw.html) | A data-idegen burkoló külső PostgreSQL-kiszolgálót a tárolt adatok elérésére használt. |
| [hypopg](https://hypopg.readthedocs.io/en/latest/) | Elméleti indexekhez nem követelnek CPU vagy a lemez létrehozása módszert biztosít. |
| [plv8](https://plv8.github.io/) | A Javascript nyelvi bővítmény, amely a tárolt eljárások, eseményindítók és egyéb is használható a PostgreSQL-hez. |

### <a name="postgis-extensions"></a>PostGIS bővítmények

> [!div class="mx-tableFixed"]
| **Bővítmény** | **Leírás** |
|---|---|
| [PostGIS](http://www.postgis.net/), postgis\_topológia, postgis\_tiger\_geocoder, postgis\_sfcgal | Térbeli és földrajzi objektum a PostgreSQL-hez. |
| cím\_standardizer, cím\_standardizer\_adatok\_velünk a kapcsolatot | Használja az alkotóelemeket be egy címet elemezni. Támogatja a geokódolás cím normalizálási lépés segítségével. |
| [pgrouting](http://pgrouting.org/) | Kiterjeszti a PostGIS / PostgreSQL térinformatikai adatbázis adataival kínálnak térinformatikai funkcióit útválasztási. |


### <a name="using-pgstatstatements"></a>Pg_stat_statements használatával
A [pg\_stat\_utasítások bővítmény](https://www.postgresql.org/docs/9.6/static/pgstatstatements.html) előre betöltött minden egyes, Azure Database for PostgreSQL-kiszolgáló használatával nyomon követheti az SQL-utasítások végrehajtási statisztikákat az azt jelenti, hogy a rendszer.
A beállítás `pg_stat_statements.track`, amely szabályozza, mely utasítások számításba vesszük, az alapértelmezett érték a bővítmény által `top`, ami azt jelenti, közvetlenül az ügyfelek által kiállított összes utasításokat követi. A két egyéb nyomkövetési szintek a következők `none` és `all`. Ez a beállítás nem konfigurálható keresztül kiszolgáló paraméterként a [az Azure portal](https://docs.microsoft.com/azure/postgresql/howto-configure-server-parameters-using-portal) vagy a [Azure CLI-vel](https://docs.microsoft.com/azure/postgresql/howto-configure-server-parameters-using-cli).

A lekérdezés végrehajtási adatok pg_stat_statements biztosít, és a kiszolgáló teljesítményére gyakorolt magával van, minden egyes SQL-utasítás naplózza. Ha Ön nem használja aktívan a pg_stat_statements bővítményt, azt javasoljuk, hogy állítsa `pg_stat_statements.track` való `none`. Vegye figyelembe, hogy egyes szolgáltatások, harmadik féltől származó pg_stat_statements lekérdezési teljesítmény elemzéseket kínálnak, ezért győződjön meg arról, hogy ez a helyzet, vagy nem hivatkozhatnak.


## <a name="next-steps"></a>További lépések
Ha nem lát, amelyet szeretne használni, ossza meg velünk bővítmény. Szavazzon meglévő kérelmeket, vagy hozzon létre új visszajelzést és a kéréseket a [ügyfél-visszajelzési fórumán](https://feedback.azure.com/forums/597976-azure-database-for-postgresql).
