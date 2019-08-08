---
title: A Azure Database for MySQL online áttelepítéssel kapcsolatos ismert problémákkal/áttelepítési korlátozásokkal foglalkozó cikk | Microsoft Docs
description: Ismerje meg a Azure Database for MySQL online áttelepítésével kapcsolatos ismert problémákat/áttelepítési korlátozásokat.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 08/06/2019
ms.openlocfilehash: 0b1632ab943026578eb753014575ab53d151c33f
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/08/2019
ms.locfileid: "68855020"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-to-azure-db-for-postgresql"></a>Ismert problémák/migrációs korlátozások online áttelepítéssel az Azure DB for PostgreSQL-hez

A PostgreSQL-ről Azure Database for PostgreSQLre való online áttelepítéssel kapcsolatos ismert problémák és korlátozások a következő szakaszokban olvashatók.

## <a name="online-migration-configuration"></a>Online áttelepítési konfiguráció

- A forrás PostgreSQL-kiszolgálónak 9.5.11, 9.6.7 vagy 10,3 vagy újabb verziójúnak kell futnia. További információkért lásd a [PostgreSQL-adatbázisok támogatott verzióit](../postgresql/concepts-supported-versions.md)ismertető cikket.
- Csak a verzió-áttelepítések támogatottak. Például a PostgreSQL-9.5.11 áttelepítése Azure Database for PostgreSQL 9.6.7 nem támogatott.

    > [!NOTE]
    > A PostgreSQL 10-es verziójában a DMS jelenleg csak az 10,3-es verzió áttelepítését támogatja Azure Database for PostgreSQLra. Hamarosan a PostgreSQL újabb verzióinak támogatását tervezzük.

- A logikai replikáció engedélyezéséhez a **forrás PostgreSQL PostgreSQL. conf** fájlban adja meg a következő paramétereket:
  - **wal_level** = logikai
  - **max_replication_slots** = [az adatbázisok maximális száma az áttelepítéshez]; Ha 4 adatbázist szeretne áttelepíteni, állítsa az értéket 4 értékre.
  - **max_wal_senders** = [egyidejűleg futó adatbázisok száma]; a javasolt érték 10
- A DMS-ügynök IP-címének hozzáadása a forrás PostgreSQL pg_hba. conf fájlhoz
  1. Jegyezze fel a DMS IP-címét, miután befejezte a DMS-példány üzembe helyezését.
  2. Adja hozzá az IP-címet az pg_hba. conf fájlhoz az alábbiak szerint:

        az összes 172.16.136.18/10 MD5 gazdagép replikációs postgres 172.16.136.18/10 MD5

- A felhasználónak rendelkeznie kell a felügyelői engedélyekkel a forrás-adatbázist üzemeltető kiszolgálón.
- A forrás-adatbázis sémáján kívül a forrás-és a célként megadott adatbázis sémáinak egyezniük kell.
- A cél Azure Database for PostgreSQL sémája nem rendelkezhet idegen kulcsokkal. A külső kulcsok eldobásához használja az alábbi lekérdezést:

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

- A cél Azure Database for PostgreSQL sémájában nem lehetnek eseményindítók. A következő paranccsal tilthatja le az eseményindítókat a célként megadott adatbázisban:

     ```
    SELECT Concat('DROP TRIGGER ', Trigger_Name, ';') FROM  information_schema.TRIGGERS WHERE TRIGGER_SCHEMA = 'your_schema';
     ```

## <a name="datatype-limitations"></a>Adattípus-korlátozások

- **Korlátozás**: Ha ENUMERÁLÁSi adattípus szerepel a forrás PostgreSQL-adatbázisban, az áttelepítés a folyamatos szinkronizálás során sikertelen lesz.

    **Áthidaló megoldás**: Módosítsa az ENUMERÁLÁSi adattípust a Azure Database for PostgreSQL eltérő karakterre.

- **Korlátozás**: Ha a táblákban nincs elsődleges kulcs, a folyamatos szinkronizálás sikertelen lesz.

    **Áthidaló megoldás**: A folytatáshoz átmenetileg állítsa be a tábla elsődleges kulcsát az áttelepítéshez. Az elsődleges kulcsot az adatáttelepítés befejeződése után is eltávolíthatja.

## <a name="lob-limitations"></a>LOB-korlátozások

A nagyméretű objektumok (LOB) oszlopai olyan oszlopok, amelyek nagy mennyiségű növekedést okozhatnak. A PostgreSQL esetében például a LOB-adattípusok például az XML, a JSON, a képek, a szöveg stb.

- **Korlátozás**: Ha a LOB-adattípusok elsődleges kulcsként használatosak, az áttelepítés sikertelen lesz.

    **Áthidaló megoldás**: Cserélje le az elsődleges kulcsot más adattípusokra vagy nem LOB oszlopokra.

- **Korlátozás**: Ha a nagyméretű objektum (LOB) oszlop hossza meghaladja a 32 KB-ot, a rendszer az adatmennyiséget csonkolja a célhelyen. A LOB-oszlop hosszát a következő lekérdezéssel tekintheti meg:

    ```
    SELECT max(length(cast(body as text))) as body FROM customer_mail
    ```

    **Áthidaló megoldás**: Ha 32 KB-nál nagyobb LOB-objektummal rendelkezik, forduljon a mérnöki csapathoz az [Azure-adatbázis](mailto:AskAzureDatabaseMigrations@service.microsoft.com)áttelepítésekor.

- **Korlátozás**: Ha a tábla tartalmaz LOB-oszlopokat, és nincs elsődleges kulcs beállítva a táblához, előfordulhat, hogy a rendszer nem telepíti át az adatátviteli kulcsot ehhez a táblához.

    **Áthidaló megoldás**: A folytatáshoz átmenetileg állítsa be a tábla elsődleges kulcsát az áttelepítéshez. Az elsődleges kulcsot az adatáttelepítés befejeződése után is eltávolíthatja.

## <a name="postgresql10-workaround"></a>PostgreSQL10 megkerülő megoldás

A PostgreSQL 10. x különböző módosításokat végez a pg_xlog, így az áttelepítés nem a várt módon fut. Ha a PostgreSQL 10. x verzióról Azure Database for PostgreSQL 10,3-re végez áttelepítést, hajtsa végre a következő parancsfájlt a forrás PostgreSQL-adatbázison, és hozzon létre burkoló függvényt a pg_xlog függvények köré.

```
BEGIN;
CREATE SCHEMA IF NOT EXISTS fnRenames;
CREATE OR REPLACE FUNCTION fnRenames.pg_switch_xlog() RETURNS pg_lsn AS $$ 
   SELECT pg_switch_wal(); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_xlog_replay_pause() RETURNS VOID AS $$ 
   SELECT pg_wal_replay_pause(); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_xlog_replay_resume() RETURNS VOID AS $$ 
   SELECT pg_wal_replay_resume(); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_current_xlog_location() RETURNS pg_lsn AS $$ 
   SELECT pg_current_wal_lsn(); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_is_xlog_replay_paused() RETURNS boolean AS $$ 
   SELECT pg_is_wal_replay_paused(); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_xlogfile_name(lsn pg_lsn) RETURNS TEXT AS $$ 
   SELECT pg_walfile_name(lsn); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_last_xlog_replay_location() RETURNS pg_lsn AS $$ 
   SELECT pg_last_wal_replay_lsn(); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_last_xlog_receive_location() RETURNS pg_lsn AS $$ 
   SELECT pg_last_wal_receive_lsn(); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_current_xlog_flush_location() RETURNS pg_lsn AS $$ 
   SELECT pg_current_wal_flush_lsn(); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_current_xlog_insert_location() RETURNS pg_lsn AS $$ 
   SELECT pg_current_wal_insert_lsn(); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_xlog_location_diff(lsn1 pg_lsn, lsn2 pg_lsn) RETURNS NUMERIC AS $$ 
   SELECT pg_wal_lsn_diff(lsn1, lsn2); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_xlogfile_name_offset(lsn pg_lsn, OUT TEXT, OUT INTEGER) AS $$ 
   SELECT pg_walfile_name_offset(lsn); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_create_logical_replication_slot(slot_name name, plugin name, 
   temporary BOOLEAN DEFAULT FALSE, OUT slot_name name, OUT xlog_position pg_lsn) RETURNS RECORD AS $$ 
   SELECT slot_name::NAME, lsn::pg_lsn FROM pg_catalog.pg_create_logical_replication_slot(slot_name, plugin, 
   temporary); $$ LANGUAGE SQL;
ALTER USER PG_User SET search_path = fnRenames, pg_catalog, "$user", public;

-- DROP SCHEMA fnRenames CASCADE;
-- ALTER USER PG_User SET search_path TO DEFAULT;
COMMIT;
```

## <a name="limitations-when-migrating-online-from-aws-rds-postgresql"></a>Az AWS RDS PostgreSQL-ről történő online áttelepítés korlátozásai

Ha az AWS RDS PostgreSQL-ről a Azure Database for PostgreSQLra próbál online áttelepítést végezni, a következő hibák merülhetnek fel.

- **Hiba**: A(z) „{database}” adatbázisban található „{table}” tábla „{column}” oszlopának alapértelmezett értéke eltérő a forrás- és a célkiszolgálón. Az érték „{value on source}” a forráson és „{value on target}” a célon.

  **Korlátozás**: Ez a hiba akkor fordul elő, ha egy oszlop sémájának alapértelmezett értéke eltér a forrás-és a cél-adatbázisok között.
  **Áthidaló megoldás**: Győződjön meg arról, hogy a célként megadott séma megfelel a forrás sémájának. A séma áttelepítésével kapcsolatos részletekért tekintse meg az [Azure PostgreSQL online](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online#migrate-the-sample-schema)áttelepítési dokumentációját.

- **Hiba**: A(z) „{database}” céladatbázis „{number of tables}” táblával rendelkezik, a(z) „{database}” forrásadatbázisban azonban „{number of tables}” tábla található. A forrás- és a céladatbázisok táblái számának azonosnak kell lennie.

  **Korlátozás**: Ez a hiba akkor fordul elő, ha a táblák száma eltér a forrás-és a cél-adatbázis között.
  **Áthidaló megoldás**: Győződjön meg arról, hogy a célként megadott séma megfelel a forrás sémájának. A séma áttelepítésével kapcsolatos részletekért tekintse meg az [Azure PostgreSQL online](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online#migrate-the-sample-schema)áttelepítési dokumentációját.

- **Hiba:** A forrás-adatbázis ({Database}) üres.

  **Korlátozás**: Ez a hiba akkor fordul elő, ha a forrásadatbázis üres. Ennek az lehet az oka, hogy rossz adatbázist választott ki forrásként.
  **Áthidaló megoldás**: Ellenőrizze az áttelepítéshez kiválasztott forrás-adatbázist, majd próbálkozzon újra.

- **Hiba:** A célként megadott adatbázis ({Database}) üres. Migrálja a sémát.

  **Korlátozás**: Ez a hiba akkor fordul elő, ha a céladatbázis nem rendelkezik sémával. Győződjön meg arról, hogy a cél sémája egyezik a forrásban található sémával.
  **Áthidaló megoldás**: Győződjön meg arról, hogy a célként megadott séma megfelel a forrás sémájának. A séma áttelepítésével kapcsolatos részletekért tekintse meg az [Azure PostgreSQL online](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online#migrate-the-sample-schema)áttelepítési dokumentációját.

## <a name="other-limitations"></a>Egyéb korlátozások

- Az adatbázis neve nem tartalmazhatja a pontosvesszőt (;).
- A kapcsos zárójelek ({}) megnyitása és bezárása nem támogatja a jelszó-karakterláncot. Ez a korlátozás a forrás PostgreSQL-hez és a TARGET Azure Database for PostgreSQLhoz való csatlakozásra egyaránt vonatkozik.
- A rögzített táblának rendelkeznie kell egy elsődleges kulccsal. Ha egy tábla nem rendelkezik elsődleges kulccsal, a törlés és a rekord frissítése művelet eredménye kiszámíthatatlan lesz.
- Az elsődleges kulcs szegmensének frissítése figyelmen kívül lesz hagyva. Ilyen esetekben az ilyen frissítés alkalmazását a cél olyan frissítésként azonosítja, amely nem módosította a sorokat, és egy, a kivételek táblába írt rekordot fog eredményezni.
- Több tábla ugyanazzal a névvel való áttelepítése, de egy másik eset (például Tábla1, TÁBLA1 és tábla1) kiszámíthatatlan működést eredményezhet, ezért nem támogatott.
- A (z) [CREATE | feldolgozásának módosítása ALTER | DROP] a Table DDLs csak akkor támogatottak, ha egy belső függvény/eljárás törzsében vagy más beágyazott szerkezetekben vannak tárolva. Például a következő módosítás nem lesz rögzítve:

    ```
    CREATE OR REPLACE FUNCTION pg.create_distributors1() RETURNS void
    LANGUAGE plpgsql
    AS $$
    BEGIN
    create table pg.distributors1(did serial PRIMARY KEY,name varchar(40)
    NOT NULL);
    END;
    $$;
    ```

- A CSONKOLT műveletek feldolgozásának módosítása (folyamatos szinkronizálás) nem támogatott. A particionált táblák áttelepítése nem támogatott. Particionált tábla észlelésekor a következő dolgok történnek:

  - Az adatbázis a szülő és a gyermek táblák listáját fogja jelenteni.
  - A tábla a célhelyen lesz létrehozva, a kijelölt táblákkal megegyező tulajdonságokkal.
  - Ha a forrásadatbázis fölérendelt táblája ugyanazzal az elsődleges kulccsal rendelkezik, mint a gyermektábla, "duplikált kulcs" hiba jön létre.

- A DMS-ben az egyetlen áttelepítési tevékenységben az áttelepítéshez szükséges adatbázisok maximális száma négy.
