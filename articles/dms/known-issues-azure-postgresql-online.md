---
title: A cikk kapcsolatos ismert problémák és a migrálás korlátozások az online migrálást az Azure Database MySQL-hez |} A Microsoft Docs
description: Ismerje meg az online migrálást az Azure Database MySQL-hez készült ismert problémák és a migrálás korlátozások.
services: database-migration
author: HJToland3
ms.author: scphang
manager: ''
ms.reviewer: ''
ms.service: database-migration
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 09/22/2018
ms.openlocfilehash: b83c889e72acb320c308c3ad5ee6243e715fd523
ms.sourcegitcommit: 022cf0f3f6a227e09ea1120b09a7f4638c78b3e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/21/2018
ms.locfileid: "52282876"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-to-azure-db-for-postgresql"></a>Ismert problémák és a migrálás korlátozások az online migrálást az Azure-adatbázis PostgreSQL-hez

Ismert problémák és korlátozások online migrálást a PostgreSQL az Azure Database for postgresql-hez társított a következő szakaszokban ismertetett. 

## <a name="online-migration-configuration"></a>Online migrálás konfiguráció
- A PostgreSQL-kiszolgáló forrás 9.5.11, 9.6.7 vagy 10.3 verzióját kell futtatniuk vagy újabb. További információkért tekintse meg a cikket [PostgreSQL-adatbázis verziója támogatott](../postgresql/concepts-supported-versions.md).
- Csak ugyanazon verzió az áttelepítés támogatott. Például az Azure Database for postgresql-hez 9.6.7 PostgreSQL 9.5.11 áttelepítése nem támogatott.
- A logikai replikáció engedélyezéséhez a **PostgreSQL postgresql.conf forrás** fájlt, állítsa be a következő paraméterekkel:
    - **wal_level** logikai =
    - **max_replication_slots** = [áttelepítés adatbázisok maximális száma]; Ha azt szeretné, 4 adatbázis áttelepítése, állítsa az értékét 4
    - **max_wal_senders** = [több egyidejűleg futó adatbázisok száma]; ajánlott értéke 10
- A forrás PostgresSQL pg_hba.conf hozzáadni a DMS ügynök IP-Címét
    1. Jegyezze fel a DMS IP-cím egy példányát a DMS-kiépítés befejezése után.
    2. Az IP-cím hozzáadása a pg_hba.conf fájl látható módon:

        az összes 172.16.136.18/10 md5 gazdagép replikációs postgres 172.16.136.18/10 md5 üzemeltetése

- A felhasználó a felügyelő, aki engedéllyel kell rendelkeznie a forráshely adatbázisára üzemeltető kiszolgálón
- A forrás és cél adatbázissémák azokat, kivéve a forrás-adatbázis sémájának ENUM létesíteni, meg kell egyeznie.
- A cél Azure Database for postgresql-hez a sémát nem lehet külső kulcsok. Idegen kulcsok használja a következő lekérdezést:

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

- A séma a célhelyen, Azure Database for PostgreSQL kell nem tartozhat eseményindító. Használja az alábbi letiltása a céladatbázisban eseményindítók:

     ```
    SELECT Concat('DROP TRIGGER ', Trigger_Name, ';') FROM  information_schema.TRIGGERS WHERE TRIGGER_SCHEMA = 'your_schema';
     ```

## <a name="datatype-limitations"></a>Adattípus-korlátozások

- **Korlátozás**: Ha a forrás-adatbázis PostgreSQL-számbavételi datatype, az áttelepítés sikertelen lesz-e a folyamatos szinkronizálás során.

    **Megkerülő megoldás**: módosítása ENUM datatype karakternek változó az Azure Database for postgresql-hez.

- **Korlátozás**: nincs elsődleges kulcs azokon a táblákon, ha a folyamatos szinkronizálás sikertelen lesz.

    **Megkerülő megoldás**: beállítják ideiglenesen folytatja az áttelepítéshez a tábla elsődleges kulcsot. Adatok áttelepítés befejezése után eltávolíthatja az elsődleges kulcsot.

## <a name="lob-limitations"></a>LOB-korlátozások
Nagyméretű objektum (LOB) oszlopok olyan oszlopot, amely nagy növelhető. A PostgreSQL LOB adattípus például XML, JSON, kép, szöveg stb.

- **Korlátozás**: Ha a LOB-adattípusokat elsődleges kulcsként használja, a migrálás meghiúsul.

    **Megkerülő megoldás**: csere elsődleges kulcs, amelyek nem LOB oszlop és más adattípusok.

- **Korlátozás**: Ha nagyméretű objektum (LOB) oszlop hossza 32 KB-nál nagyobb, adatok lerövidítheti a cél. Ez a lekérdezés használatával LOB oszlop hossza ellenőrizheti:

    ```
    SELECT max(length(cast(body as text))) as body FROM customer_mail
    ```

    **Megkerülő megoldás**: Ha LOB-objektum, amely 32 KB-nál nagyobb méretű, lépjen kapcsolatba a mérnöki csapathoz a következő címen [ dmsfeedback@microsoft.com ](mailto:dmsfeedback@microsoft.com).

- **Korlátozás**: Ha nincsenek a LOB-oszlopok a tábla, és nincs elsődleges kulcs beállítva a táblához, adatok előfordulhat, hogy nem lesz áttelepítve ehhez a táblához.

    **Megkerülő megoldás**: beállítják ideiglenesen folytatja az áttelepítéshez a tábla elsődleges kulcsot. Adatok áttelepítés befejezése után eltávolíthatja az elsődleges kulcsot.

## <a name="postgresql10-workaround"></a>PostgreSQL10 megkerülő megoldás
PostgreSQL 10.x különböző módosítást hajt végre pg_xlog mappanevek, és ezért okozza az áttelepítése nem a várt módon fut. Ha a PostgreSQL migráláshoz 10.x, Azure database for PostgreSQL 10.3, hajtsa végre a következő parancsfájlt a forrás burkoló funkció körül pg_xlog függvények létrehozása PostgreSQL-adatbázishoz.

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

## <a name="other-limitations"></a>Egyéb korlátozások is érvényesek
- Az adatbázis neve nem tartalmazhat egy pontosvesszővel (;).
- Jelszó-karakterlánc, amely rendelkezik a nyitó és záró kapcsos zárójelek közé {} nem támogatott. Ez a korlátozás vonatkozik mindkét csatlakozás PostgreSQL forrás és a cél Azure Database for postgresql-hez.
- Egy rögzített táblának elsődleges kulccsal kell rendelkeznie. Ha egy tábla nem rendelkezik elsődleges kulccsal, törlése és a frissítési rekord művelet eredménye előre nem látható lesz.
- Egy elsődleges kulcs szegmens frissítése figyelmen kívül hagyja. Ezekben az esetekben egy frissítés telepítése azonosítja a cél szerint, frissítés, amely nem frissített azokat a sorokat, és a egy rekordot, a kivételek táblába írja eredményez.
- Több tábla ugyanazzal a névvel, de egy másik eset (Példa: tábla1, TABLE1 és tábla1) migrálásának előre nem látható viselkedéshez vezethet, és ezért nem támogatott.
- Módosítsa a feldolgozása [létrehozás |} AZ ALTER |} KÖZVETLEN] tábla DDLs támogatottak, ha egy belső funkció vagy eljárás törzsének letiltása vagy egyéb beágyazott szerkezetek tárolják őket. Ha például az alábbi módosítás nem rögzíti a rendszer:

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

- Módosítás feldolgozásával (folyamatos szinkronizálása) CSONKOLÁSI operations nem támogatott. Particionált táblák áttelepítése nem támogatott. Ha egy particionált táblához észlel, a következő dolog történik:
    - Az adatbázis jelentést készít a szülő és gyermek táblák listája.
    - A tábla a célként megadott létrejön a kijelölt táblázatokat az azonos tulajdonságokkal rendelkező normál táblázatként.
    - Ha a szülőtábla a forrásadatbázis annak gyermek táblák elsődleges kulcs értékét, a "ismétlődő kulcsot" hiba jön létre.
- A DMS egy egyetlen migrálási tevékenységet a migrálni kívánt adatbázisok határérték négy.