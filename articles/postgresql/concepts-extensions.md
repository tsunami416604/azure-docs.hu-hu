---
title: "PostgreSQL-bővítmények PostgreSQL Azure-adatbázis használata"
description: "Kiterjesztheti az Azure-adatbázis bővítményekkel PostgreSQL az adatbázis működését ismerteti."
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 0b4150fcd7d32c823173c3e2676e226634346a2b
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2018
---
# <a name="postgresql-extensions-in-azure-database-for-postgresql"></a>Azure adatbázisban a következő PostgreSQL PostgreSQL-bővítmények
PostgreSQL teszi lehetővé az adatbázis bővítmény használatával bővítése. Az SQL több kapcsolódó objektum együtt kötegelése tartalmazó egyetlen csomag betöltve, vagy az adatbázis egyetlen parancs eltávolítja a bővítmények lehetővé teszi. Az adatbázisban, betöltése után bővítmények beépített funkciók is működjön. PostgreSQL-bővítmények további információkért lásd: [csomagolás kapcsolódó objektumok egy bővítmény](https://www.postgresql.org/docs/9.6/static/extend-extensions.html).

## <a name="how-to-use-postgresql-extensions"></a>PostgreSQL-bővítmények használata
Az adatbázis PostgreSQL-bővítmények kell telepíteni, azok használata előtt. Egy adott bővítmény telepítéséhez futtassa a [kiterjesztés létrehozása](https://www.postgresql.org/docs/9.6/static/sql-createextension.html) parancs psql eszközből a csomagolt objektumok betölteni az adatbázisba.

Alább felsorolt PostgreSQL Azure-adatbázis jelenleg egy kulcs bővítmények részét támogatja. Meghaladja az alább felsorolt Extensions csomag használata nem támogatott; nem hozható létre saját bővítmény Azure Database PostgreSQL-szolgáltatás.

## <a name="extensions-supported-by-azure-database-for-postgresql"></a>Azure-adatbázis által támogatott PostgreSQL bővítmények
Az alábbi táblázatok a szabványos PostgreSQL-bővítmények PostgreSQL Azure-adatbázis által jelenleg támogatott. Ez az információ is rendelkezésre áll lekérdezésével `pg\_available\_extensions`.

### <a name="data-types-extensions"></a>Adattípus-bővítmények

> [!div class="mx-tableFixed"]
| **Bővítmény** | **Leírás** |
|---|---|
| [chkpass](https://www.postgresql.org/docs/9.6/static/chkpass.html) | Automatikus titkosított jelszavak adattípust biztosít. |
| [citext](https://www.postgresql.org/docs/9.6/static/citext.html) | Itt nem betűérzékeny karakter karakterlánc típusú. |
| [cube](https://www.postgresql.org/docs/9.6/static/cube.html) | Többdimenziós kockák adattípust biztosít. |
| [hstore](https://www.postgresql.org/docs/9.6/static/hstore.html) | Itt adattípus-készletek kulcs/érték párok tárolására. |
| [isn](https://www.postgresql.org/docs/9.6/static/isn.html) | Nemzetközi termék szabványok számozására adattípusok biztosít. |
| [ltree](https://www.postgresql.org/docs/9.6/static/ltree.html) | Adattípus biztosít hierarchikus faszerkezetben struktúrákat. |

### <a name="functions-extensions"></a>Funkciók bővítmények

> [!div class="mx-tableFixed"]
| **Bővítmény** | **Leírás** |
|---|---|
| [earthdistance](https://www.postgresql.org/docs/9.6/static/earthdistance.html) | Lehetővé teszi a föld felszínén nagy-kör távolság kiszámításához. |
| [fuzzystrmatch](https://www.postgresql.org/docs/9.6/static/fuzzystrmatch.html) | Az Hasonlóságok és karakterláncok közötti távolság meghatározásához számos funkciókat biztosít. |
| [intarray](https://www.postgresql.org/docs/9.6/static/intarray.html) | Biztosítja a funkciók és operátorok kezelésére szolgáló tömb null-mentes egész számok. |
| [pgcrypto](https://www.postgresql.org/docs/9.6/static/pgcrypto.html) | Titkosítási funkciókat biztosít. |
| [pg\_partman](https://pgxn.org/dist/pg_partman/doc/pg_partman.html) | Kezeli a particionált táblákat idő vagy azonosítója. |
| [pg\_trgm](https://www.postgresql.org/docs/9.6/static/pgtrgm.html) | Biztosítja a funkciók és operátorok trigram megfelelő alfanumerikus szöveg hasonlóság meghatározásához. |
| [tablefunc](https://www.postgresql.org/docs/9.6/static/tablefunc.html) | Teljes táblázatokat, beleértve az Kereszttábla kezelő funkciókat biztosít. |
| [uuid-ossp](https://www.postgresql.org/docs/9.6/static/uuid-ossp.html) | Univerzálisan egyedi azonosítói (UUID-k) hoz létre. |

### <a name="full-text-search-extensions"></a>A teljes szöveges keresés bővítmények

> [!div class="mx-tableFixed"]
| **Bővítmény** | **Leírás** |
|---|---|
| [dict\_int](https://www.postgresql.org/docs/9.6/static/dict-int.html) | Egy szöveges keresés szótár sablon biztosít egész számok. |
| [unaccent](https://www.postgresql.org/docs/9.6/static/unaccent.html) | Szöveg (diakritikus jeleket) ékezetek eltávolítása lexemes keresési szótár. |

### <a name="index-types-extensions"></a>Index típusú kiterjesztések

> [!div class="mx-tableFixed"]
| **Bővítmény** | **Leírás** |
|---|---|
| [btree\_gin](https://www.postgresql.org/docs/9.6/static/btree-gin.html) | A minta GIN operátor osztályokat, amelyek megvalósítják a B-fa egyes adattípusok viselkedése például biztosít. |
| [btree\_gist](https://www.postgresql.org/docs/9.6/static/btree-gist.html) | GiST index operátor osztályokat, amelyek megvalósítják az B-fa biztosít. |

### <a name="language-extensions"></a>Nyelvi bővítmények

> [!div class="mx-tableFixed"]
| **Bővítmény** | **Leírás** |
|---|---|
| [plpgsql](https://www.postgresql.org/docs/9.6/static/plpgsql.html) | PL/pgSQL betölthető eljárási nyelv. |

### <a name="miscellaneous-extensions"></a>Vegyes bővítmények

> [!div class="mx-tableFixed"]
| **Bővítmény** | **Leírás** |
|---|---|
| [a védelmi\_buffercache](https://www.postgresql.org/docs/9.6/static/pgbuffercache.html) | Arra szolgál, hogy mi történik a megosztott pufferből gyorsítótárban a valós idejű vizsgálata. |
| [pg\_prewarm](https://www.postgresql.org/docs/9.6/static/pgprewarm.html) | Relációs adatok betöltése a puffer gyorsítótárába lehetőséget biztosít. |
| [a védelmi\_stat\_utasítások](https://www.postgresql.org/docs/9.6/static/pgstatstatements.html) | Lehetővé teszi a kiszolgáló által végrehajtott összes SQL-utasítások végrehajtása statisztikáinak nyomon követése. |
| [pgrowlocks](https://www.postgresql.org/docs/9.6/static/pgrowlocks.html) | Lehetővé teszi a sorszintű zárolási adatok. |
| [pgstattuple](https://www.postgresql.org/docs/9.6/static/pgstattuple.html) | Lehetővé teszi a rekord szintű statisztika megjelenítése. |
| [postgres\_fdw](https://www.postgresql.org/docs/9.6/static/postgres-fdw.html) | A külső PostgreSQL-kiszolgálókon tárolt adatok eléréséhez használt idegen-adatok burkoló. |

### <a name="postgis-extensions"></a>PostGIS bővítmények

> [!div class="mx-tableFixed"]
| **Bővítmény** | **Leírás** |
|---|---|
| [PostGIS](http://www.postgis.net/), postgis\_topológia, postgis\_tiger\_geocoder, postgis\_sfcgal | Térbeli és földrajzi objektumok PostgreSQL. |
| cím\_standardizer, cím\_standardizer\_adatok\_us | Egy címet értelmezhető alkotóelemeket is. Használja a geokódolás cím normalizálási lépés támogatásához. |
| [pgrouting](http://pgrouting.org/) | Kiterjeszti a PostGIS / PostgreSQL földrajzi adatbázis adataival kínálnak a földrajzi útválasztási funkciót. |

## <a name="next-steps"></a>További lépések
Ha nem jelenik meg, amelyeket szeretne használni, ossza meg velünk kiterjesztéssel. Meglévő kérelmek szavazzon, vagy hozzon létre új visszajelzések és a kéréseket a [ügyfél-visszajelzési fórumon](https://feedback.azure.com/forums/597976-azure-database-for-postgresql).
