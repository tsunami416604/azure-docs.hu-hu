---
title: Bővítmények – Azure Database for PostgreSQL – rugalmas kiszolgáló
description: Tudnivalók a Azure Database for PostgreSQL-rugalmas kiszolgáló elérhető postgres-bővítményeiről
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: 1126c218f8e80b7d89183746890a3fae1357d29d
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90935687"
---
# <a name="postgresql-extensions-in-azure-database-for-postgresql---flexible-server"></a>PostgreSQL-bővítmények Azure Database for PostgreSQL – rugalmas kiszolgáló

> [!IMPORTANT]
> Azure Database for PostgreSQL – a rugalmas kiszolgáló előzetes verzióban érhető el

A PostgreSQL lehetőséget nyújt az adatbázis funkcióinak kiterjesztésére a bővítmények használatával. A bővítmények több kapcsolódó SQL-objektumot is megadhatnak egyetlen csomagban, amely egy paranccsal tölthető be vagy távolítható el az adatbázisból. Az adatbázisba való betöltés után a bővítmények ugyanúgy működnek, mint a beépített funkciók.

## <a name="how-to-use-postgresql-extensions"></a>A PostgreSQL-bővítmények használata
A PostgreSQL-bővítményeket a használatuk előtt telepíteni kell az adatbázisba. Egy adott bővítmény telepítéséhez futtassa a [bővítmény létrehozása](https://www.postgresql.org/docs/current/sql-createextension.html) parancsot. Ez a parancs betölti a becsomagolt objektumokat az adatbázisba.

A Azure Database for PostgreSQL a következő listában szereplő kulcs-kiterjesztések részhalmazát támogatja. Ezek az információk a futtatásával is elérhetők `SHOW azure.extensions;` . A jelen dokumentumban nem szereplő bővítmények Azure Database for PostgreSQL rugalmas kiszolgálókon nem támogatottak. Nem hozhat létre és nem tölthet be saját bővítményt Azure Database for PostgreSQLban.


## <a name="postgres-12-extensions"></a>Postgres 12 bővítmény

A következő bővítmények érhetők el Azure Database for PostgreSQL rugalmas kiszolgálókon, amelyeken a 12-es verziójú postgres van. 

> [!div class="mx-tableFixed"]
> | **Mellék**| **Bővítmény verziója** | **Leírás** |
> |---|---|---|
> |[amcheck](https://www.postgresql.org/docs/12/amcheck.html)                    | 1,2             | a kapcsolatok integritásának ellenőrzéséhez szükséges függvények|
> |[Bloom](https://www.postgresql.org/docs/12/bloom.html)                    | 1,0             | Bloom hozzáférési módszer – aláírási fájl alapú index|
> |[btree_gin](https://www.postgresql.org/docs/12/btree-gin.html)                    | 1.3             | gyakori adattípusok indexelésének támogatása a GIN-ben|
> |[btree_gist](https://www.postgresql.org/docs/12/btree-gist.html)                   | 1.5             | gyakori adattípusok indexelésének támogatása a lényegeben|
> |[citext](https://www.postgresql.org/docs/12/citext.html)                       | 1.6             | kis-és nagybetűket megkülönböztető karakterláncok adattípusa|
> |[adatkocka](https://www.postgresql.org/docs/12/cube.html)                         | 1.4             | többdimenziós kockák adattípusa|
> |[dblink](https://www.postgresql.org/docs/12/dblink.html)                       | 1,2             | Kapcsolódás más PostgreSQL-adatbázisokhoz egy adatbázison belülről|
> |[dict_int](https://www.postgresql.org/docs/12/dict-int.html)                     | 1,0             | szöveges keresési szótár sablonja egész számokhoz|
> |[dict_xsyn](https://www.postgresql.org/docs/12/dict-xsyn.html)                     | 1,0             | szöveges keresési szótár sablon a kiterjesztett szinonimák feldolgozásához|
> |[earthdistance](https://www.postgresql.org/docs/12/earthdistance.html)                | 1,1             | nagy hatótávolságú távolságok kiszámítása a Föld felszínén|
> |[fuzzystrmatch](https://www.postgresql.org/docs/12/fuzzystrmatch.html)                | 1,1             | a karakterláncok közötti hasonlóságok és távolságok meghatározása|
> |[hstore](https://www.postgresql.org/docs/12/hstore.html)                       | 1.6             | a (kulcs, érték) párok tárolására szolgáló adattípus|
> |[intagg](https://www.postgresql.org/docs/12/intagg.html)                     | 1,1             | egész számú gyűjtő és enumerálás. Elavult|
> |[intarray](https://www.postgresql.org/docs/12/intarray.html)                     | 1,2             | függvények, operátorok és indexek támogatása egész számok 1-D tömbhöz|
> |[helytelen átvitt](https://www.postgresql.org/docs/12/isn.html)                          | 1,2             | a nemzetközi termékek számozási szabványainak adattípusai|
> |[ltree](https://www.postgresql.org/docs/12/ltree.html)                        | 1,1             | adattípus a hierarchikus fastruktúrához hasonló struktúrákhoz|
> |[pageinspect](https://www.postgresql.org/docs/12/pageinspect.html)                        | 1,7             | az adatbázis-lapok tartalmának alacsony szintű vizsgálata|
> |[pg_buffercache](https://www.postgresql.org/docs/12/pgbuffercache.html)               | 1.3             | a megosztott puffer gyorsítótárának vizsgálata|
> |[pg_freespacemap](https://www.postgresql.org/docs/12/pgfreespacemap.html)               | 1,2             | a szabad terület térképének (MSZÁ) vizsgálata|
> |[pg_prewarm](https://www.postgresql.org/docs/12/pgprewarm.html)                   | 1,2             | előmelegítő kapcsolatok adatvédelme|
> |[pg_stat_statements](https://www.postgresql.org/docs/12/pgstatstatements.html)           | 1,7             | az összes végrehajtott SQL-utasítás végrehajtási statisztikájának nyomon követése|
> |[pg_trgm](https://www.postgresql.org/docs/12/pgtrgm.html)                      | 1.4             | a szöveg hasonlóságának mérése és indexelése Trigrams alapján|
> |[pg_visibility](https://www.postgresql.org/docs/12/pgvisibility.html)                      | 1,2             | vizsgálja meg a láthatósági térképet (VM) és az oldal szintű láthatósági adatokat|
> |[pgaudit](https://www.pgaudit.org/)                     | 1.4             | naplózási funkciókat biztosít|
> |[pgcrypto](https://www.postgresql.org/docs/12/pgcrypto.html)                     | 1.3             | titkosítási függvények|
> |[pgrowlocks](https://www.postgresql.org/docs/12/pgrowlocks.html)                   | 1,2             | sor szintű zárolási információk megjelenítése|
> |[pgstattuple](https://www.postgresql.org/docs/12/pgstattuple.html)                  | 1.5             | rekord szintű statisztikák megjelenítése|
> |[plpgsql](https://www.postgresql.org/docs/12/plpgsql.html)                      | 1,0             | PL/pgSQL eljárási nyelv|
> |[PostGIS](https://www.postgis.net/)                      | 3.0.0           | Geometriai, földrajzi és raszteres térbeli típusok és függvények PostGIS|
> |[postgres_fdw](https://www.postgresql.org/docs/12/postgres-fdw.html)                 | 1,0             | idegen adatburkolók távoli PostgreSQL-kiszolgálókhoz|
> |[sslinfo](https://www.postgresql.org/docs/12/sslinfo.html)                    | 1,2             | az SSL-tanúsítványokkal kapcsolatos információk|
> |[tsm_system_rows](https://www.postgresql.org/docs/12/tsm-system-rows.html)                    | 1,0             |  TABLESAMPLE metódus, amely a sorok számát korlátozza|
> |[tsm_system_time](https://www.postgresql.org/docs/12/tsm-system-time.html)                    | 1,0             |  TABLESAMPLE-metódus, amely az időt ezredmásodpercben, korlátként fogadja el|
> |[nem ékezetes](https://www.postgresql.org/docs/12/unaccent.html)                     | 1,1             | ékezeteket eltávolító szöveges keresési szótár|
> |[UUID – ossp](https://www.postgresql.org/docs/12/uuid-ossp.html)                    | 1,1             | univerzálisan egyedi azonosítók (UUID-EK) előállítása|

## <a name="postgres-11-extensions"></a>Postgres 11 bővítmény

A következő bővítmények érhetők el Azure Database for PostgreSQL rugalmas kiszolgálókon, amelyeken a postgres 11-es verziója található. 

> [!div class="mx-tableFixed"]
> | **Mellék**| **Bővítmény verziója** | **Leírás** |
> |---|---|---|
> |[amcheck](https://www.postgresql.org/docs/11/amcheck.html)                    | 1,1             | a kapcsolatok integritásának ellenőrzéséhez szükséges függvények|
> |[Bloom](https://www.postgresql.org/docs/11/bloom.html)                    | 1,0             | Bloom hozzáférési módszer – aláírási fájl alapú index|
> |[btree_gin](https://www.postgresql.org/docs/11/btree-gin.html)                    | 1.3             | gyakori adattípusok indexelésének támogatása a GIN-ben|
> |[btree_gist](https://www.postgresql.org/docs/11/btree-gist.html)                   | 1.5             | gyakori adattípusok indexelésének támogatása a lényegeben|
> |[citext](https://www.postgresql.org/docs/11/citext.html)                       | 1.5             | kis-és nagybetűket megkülönböztető karakterláncok adattípusa|
> |[adatkocka](https://www.postgresql.org/docs/11/cube.html)                         | 1.4             | többdimenziós kockák adattípusa|
> |[dblink](https://www.postgresql.org/docs/11/dblink.html)                       | 1,2             | Kapcsolódás más PostgreSQL-adatbázisokhoz egy adatbázison belülről|
> |[dict_int](https://www.postgresql.org/docs/11/dict-int.html)                     | 1,0             | szöveges keresési szótár sablonja egész számokhoz|
> |[dict_xsyn](https://www.postgresql.org/docs/11/dict-xsyn.html)                     | 1,0             | szöveges keresési szótár sablon a kiterjesztett szinonimák feldolgozásához|
> |[earthdistance](https://www.postgresql.org/docs/11/earthdistance.html)                | 1,1             | nagy hatótávolságú távolságok kiszámítása a Föld felszínén|
> |[fuzzystrmatch](https://www.postgresql.org/docs/11/fuzzystrmatch.html)                | 1,1             | a karakterláncok közötti hasonlóságok és távolságok meghatározása|
> |[hstore](https://www.postgresql.org/docs/11/hstore.html)                       | 1.5             | a (kulcs, érték) párok tárolására szolgáló adattípus|
> |[intagg](https://www.postgresql.org/docs/11/intagg.html)                     | 1,1             | egész számú gyűjtő és enumerálás. Elavult|
> |[intarray](https://www.postgresql.org/docs/11/intarray.html)                     | 1,2             | függvények, operátorok és indexek támogatása egész számok 1-D tömbhöz|
> |[helytelen átvitt](https://www.postgresql.org/docs/11/isn.html)                          | 1,2             | a nemzetközi termékek számozási szabványainak adattípusai|
> |[ltree](https://www.postgresql.org/docs/11/ltree.html)                        | 1,1             | adattípus a hierarchikus fastruktúrához hasonló struktúrákhoz|
> |[pageinspect](https://www.postgresql.org/docs/11/pageinspect.html)                        | 1,7             | az adatbázis-lapok tartalmának alacsony szintű vizsgálata|
> |[pg_buffercache](https://www.postgresql.org/docs/11/pgbuffercache.html)               | 1.3             | a megosztott puffer gyorsítótárának vizsgálata|
> |[pg_freespacemap](https://www.postgresql.org/docs/11/pgfreespacemap.html)               | 1,2             | a szabad terület térképének (MSZÁ) vizsgálata|
> |[pg_prewarm](https://www.postgresql.org/docs/11/pgprewarm.html)                   | 1,2             | előmelegítő kapcsolatok adatvédelme|
> |[pg_stat_statements](https://www.postgresql.org/docs/11/pgstatstatements.html)           | 1.6             | az összes végrehajtott SQL-utasítás végrehajtási statisztikájának nyomon követése|
> |[pg_trgm](https://www.postgresql.org/docs/11/pgtrgm.html)                      | 1.4             | a szöveg hasonlóságának mérése és indexelése Trigrams alapján|
> |[pg_visibility](https://www.postgresql.org/docs/11/pgvisibility.html)                      | 1,2             | vizsgálja meg a láthatósági térképet (VM) és az oldal szintű láthatósági adatokat|
> |[pgaudit](https://www.pgaudit.org/)                     | 1.3.1             | naplózási funkciókat biztosít|
> |[pgcrypto](https://www.postgresql.org/docs/11/pgcrypto.html)                     | 1.3             | titkosítási függvények|
> |[pgrowlocks](https://www.postgresql.org/docs/11/pgrowlocks.html)                   | 1,2             | sor szintű zárolási információk megjelenítése|
> |[pgstattuple](https://www.postgresql.org/docs/11/pgstattuple.html)                  | 1.5             | rekord szintű statisztikák megjelenítése|
> |[plpgsql](https://www.postgresql.org/docs/11/plpgsql.html)                      | 1,0             | PL/pgSQL eljárási nyelv|
> |[PostGIS](https://www.postgis.net/)                      | 2.5.1           | Geometriai, földrajzi és raszteres térbeli típusok és függvények PostGIS|
> |[postgres_fdw](https://www.postgresql.org/docs/11/postgres-fdw.html)                 | 1,0             | idegen adatburkolók távoli PostgreSQL-kiszolgálókhoz|
> |[sslinfo](https://www.postgresql.org/docs/11/sslinfo.html)                    | 1,2             | az SSL-tanúsítványokkal kapcsolatos információk|
> |[tablefunc](https://www.postgresql.org/docs/11/tablefunc.html)                    | 1,0             | a teljes táblákat, például a kereszttáblás funkciókat kezelő függvények|
> |[tsm_system_rows](https://www.postgresql.org/docs/11/tsm-system-rows.html)                    | 1,0             |  TABLESAMPLE metódus, amely a sorok számát korlátozza|
> |[tsm_system_time](https://www.postgresql.org/docs/11/tsm-system-time.html)                    | 1,0             |  TABLESAMPLE-metódus, amely az időt ezredmásodpercben, korlátként fogadja el|
> |[nem ékezetes](https://www.postgresql.org/docs/11/unaccent.html)                     | 1,1             | ékezeteket eltávolító szöveges keresési szótár|
> |[UUID – ossp](https://www.postgresql.org/docs/11/uuid-ossp.html)                    | 1,1             | univerzálisan egyedi azonosítók (UUID-EK) előállítása|


## <a name="pg_prewarm"></a>pg_prewarm

A pg_prewarm-bővítmény a kapcsolatot a gyorsítótárba tölti be. A gyorsítótárak előmelegítése azt jelenti, hogy a lekérdezések az újraindítás után az első futtatáskor jobb válaszidőt rendelkeznek. Az automatikus előmelegítő funkció jelenleg nem érhető el Azure Database for PostgreSQL rugalmas kiszolgálón.


## <a name="next-steps"></a>Következő lépések

Ha nem látja a használni kívánt bővítményt, tudassa velünk. Szavazzon a meglévő kérelmekre, vagy hozzon létre új visszajelzési kéréseket a [visszajelzési fórumban](https://feedback.azure.com/forums/597976-azure-database-for-postgresql).