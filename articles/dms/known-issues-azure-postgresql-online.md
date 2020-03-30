---
title: 'Ismert problémák: Online áttelepítések a PostgreSQL-ről a PostgreSQL-adatbázisba'
titleSuffix: Azure Database Migration Service
description: Ismerje meg a PostgreSQL-ből az Azure Database Migration Service használatával az Azure Database Migration Service használatával az Azure Database Migration Service használatával az online áttelepítésekkel kapcsolatos ismert problémákat és áttelepítési korlátozásokat.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom:
- seo-lt-2019
- seo-dt-2019
ms.topic: article
ms.date: 02/20/2020
ms.openlocfilehash: 3d1bc627ccb8814ab2dfb61fb0653ef0ac644038
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80235272"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-from-postgresql-to-azure-db-for-postgresql"></a>Ismert problémák/áttelepítési korlátozások a PostgreSQL-ről az Azure DB for PostgreSQL-re történő online áttelepítéssel kapcsolatban

A PostgreSQL-ről a PostgreSQL-re a PostgreSQL-adatbázisba való online áttelepítéssel kapcsolatos ismert problémákat és korlátozásokat a következő szakaszok ismertetik.

## <a name="online-migration-configuration"></a>Online áttelepítési konfiguráció

- A forrás PostgreSQL szerverének a 9.4-es, 9.5-ös, 9.6-os, 10-es vagy 11-es verziót kell futtatnia. További információt a [Támogatott PostgreSQL adatbázis-verziók](../postgresql/concepts-supported-versions.md)című cikkben talál.
- Csak az azonos vagy magasabb verzióra való áttelepítések támogatottak. Például a PostgreSQL 9.5 áttelepítése az Azure Database for PostgreSQL 9.6 vagy 10-re támogatott, de a PostgreSQL 11-ről a PostgreSQL 9.6-ra való áttelepítés nem támogatott.
- A logikai replikáció engedélyezéséhez a **Forrás PostgreSQL postgresql.conf** fájlban állítsa be a következő paramétereket:
  - **wal_level** = logikai
  - **max_replication_slots** = [legalább maximális számú adatbázis áttelepítéshez]; Ha négy adatbázist szeretne áttelepíteni, állítsa az értéket legalább 4-re.
  - **max_wal_senders** = [egyidejűleg futó adatbázisok száma]; az ajánlott érték 10
- DMS-ügynök IP hozzáadása a Forráshoz PostgreSQL pg_hba.conf
  1. Jegyezze fel a DMS IP-címét, miután befejezte az Azure Database Migration Service egy példányának kiépítését.
  2. Adja hozzá az IP-címet a pg_hba.conf fájlhoz az alábbi módon:

      ```
          host  all     172.16.136.18/10    md5
          host  replication postgres    172.16.136.18/10    md5
      ```

- A felhasználónak rendelkeznie kell a forrásadatbázist tároló kiszolgáló replikációs szerepkörrel.
- A forrás- és céladatbázis-sémáknak egyezniük kell.
- A séma a cél Azure Database for PostgreSQL-Single server nem rendelkezik idegen kulcsokkal. Az idegen kulcsok eldobásához használja az alábbi lekérdezést:

    ```
                                SELECT Queries.tablename
           ,concat('alter table ', Queries.tablename, ' ', STRING_AGG(concat('DROP CONSTRAINT ', Queries.foreignkey), ',')) as DropQuery
                ,concat('alter table ', Queries.tablename, ' ', 
                                                STRING_AGG(concat('ADD CONSTRAINT ', Queries.foreignkey, ' FOREIGN KEY (', column_name, ')', 'REFERENCES ', foreign_table_name, '(', foreign_column_name, ')' ), ',')) as AddQuery
        FROM
        (SELECT
        tc.table_schema, 
        tc.constraint_name as foreignkey, 
        tc.table_name as tableName, 
        kcu.column_name, 
        ccu.table_schema AS foreign_table_schema,
        ccu.table_name AS foreign_table_name,
        ccu.column_name AS foreign_column_name 
    FROM 
        information_schema.table_constraints AS tc 
        JOIN information_schema.key_column_usage AS kcu
          ON tc.constraint_name = kcu.constraint_name
          AND tc.table_schema = kcu.table_schema
        JOIN information_schema.constraint_column_usage AS ccu
          ON ccu.constraint_name = tc.constraint_name
          AND ccu.table_schema = tc.table_schema
    WHERE constraint_type = 'FOREIGN KEY') Queries
      GROUP BY Queries.tablename;
    
    ```

    Futtassa a ’drop foreign key’-t (ez a második oszlop) a lekérdezési eredményben.

- A séma a cél Azure Database for PostgreSQL-Single server nem rendelkezhet eseményindítók. Az alábbi műveletek letiltása a céladatbázisban:

     ```
    SELECT Concat('DROP TRIGGER ', Trigger_Name, ';') FROM  information_schema.TRIGGERS WHERE TRIGGER_SCHEMA = 'your_schema';
     ```

## <a name="datatype-limitations"></a>Adattípus-korlátozások

  **Korlátozás:** Ha nincs elsődleges kulcs a táblákon, előfordulhat, hogy a módosítások nem lesznek szinkronizálva a céladatbázissal.

  **Megoldás:** Ideiglenesen állítson be elsődleges kulcsot a tábla számára az áttelepítés folytatásához. Az elsődleges kulcsot az adatáttelepítés befejezése után távolíthatja el.

## <a name="limitations-when-migrating-online-from-aws-rds-postgresql"></a>Korlátozások az AWS RDS PostgreSQL-ről történő online áttelepítéskor

Amikor online áttelepítést próbál végrehajtani az AWS RDS PostgreSQL-ről a PostgreSQL-adatbázisba, a következő hibák léphetnek fel.

- **Hiba**: A(z) "{table}" tábla "{column}" oszlopának alapértelmezett értéke a forrás- és célkiszolgálókon eltérő. Az érték „{value on source}” a forráson és „{value on target}” a célon.

  **Korlátozás**: Ez a hiba akkor fordul elő, ha egy oszlopséma alapértelmezett értéke eltér a forrás- és a céladatbázisok között.
  **Megoldás:** Győződjön meg arról, hogy a célon lévő séma megegyezik a forrás sémájával. A séma áttelepítésével kapcsolatos részletekért tekintse meg az [Azure PostgreSQL online áttelepítési dokumentációját.](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online#migrate-the-sample-schema)

- **Hiba**: A(z) "{database}" céladatbázis "{tables}" táblákkal rendelkezik, ahol a(z) "{database}" forrásadatbázis "{tables}" számú táblával rendelkezik. A forrás- és a céladatbázisok táblái számának azonosnak kell lennie.

  **Korlátozás**: Ez a hiba akkor fordul elő, ha a táblák száma eltér a forrás- és a céladatbázis között.

  **Megoldás:** Győződjön meg arról, hogy a célon lévő séma megegyezik a forrás sémájával. A séma áttelepítésével kapcsolatos részletekért tekintse meg az [Azure PostgreSQL online áttelepítési dokumentációját.](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online#migrate-the-sample-schema)

- **Hiba:** A(z) {database} forrásadatbázis üres.

  **Korlátozás**: Ez a hiba akkor fordul elő, ha a forrásadatbázis üres. Ez valószínűleg azért van, mert rossz adatbázist választott ki forrásként.

  **Megoldás:** Ellenőrizze duplán az áttelepítéshez kijelölt forrásadatbázist, majd próbálkozzon újra.

- **Hiba:** A(z) {database} céladatbázis üres. Migrálja a sémát.

  **Korlátozás**: Ez a hiba akkor fordul elő, ha nincs séma a céladatbázisban. Győződjön meg arról, hogy a célséma megegyezik a forrás sémájával.
  **Megoldás:** Győződjön meg arról, hogy a célon lévő séma megegyezik a forrás sémájával. A séma áttelepítésével kapcsolatos részletekért tekintse meg az [Azure PostgreSQL online áttelepítési dokumentációját.](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online#migrate-the-sample-schema)

## <a name="other-limitations"></a>Egyéb korlátozások

- Az adatbázis neve nem tartalmazhat pontosvesszőt (;).
- A rögzített táblának elsődleges kulccsal kell rendelkeznie. Ha egy tábla nem rendelkezik elsődleges kulccsal, a DELETE és az UPDATE rekordműveletek eredménye kiszámíthatatlan lesz.
- Az elsődleges kulcs szegmensfrissítése figyelmen kívül lesz hagyva. Ilyen esetekben az ilyen frissítés alkalmazását a cél olyan frissítésként azonosítja, amely nem frissítette a sorokat, és a kivételek táblába írt rekordot eredményez.
- Több, azonos nevű, de eltérő kis- és nagybetűs (pl. table1, TABLE1 és Table1) áttelepítése kiszámíthatatlan viselkedést okozhat, ezért nem támogatott.
- A [CREATE | ALTER | DROP | TRUNCATE] tábla DDL-ek nem támogatottak.
- Az Azure Database Migration Service egyetlen áttelepítési tevékenység csak legfeljebb négy adatbázis befogadására.
