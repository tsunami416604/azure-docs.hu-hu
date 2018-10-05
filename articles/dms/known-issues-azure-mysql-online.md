---
title: A cikk kapcsolatos ismert problémák és a migrálás korlátozások az online migrálást az Azure Database MySQL-hez |} A Microsoft Docs
description: Ismerje meg az online migrálást az Azure Database MySQL-hez készült ismert problémák és a migrálás korlátozások.
services: database-migration
author: HJToland3
ms.author: rajpo
manager: ''
ms.reviewer: ''
ms.service: database-migration
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 08/24/2018
ms.openlocfilehash: 3697b702545c7b67b961d9820166d8dbbcb050f0
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2018
ms.locfileid: "48802202"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-to-azure-db-for-mysql"></a>Ismert problémák és a migrálás korlátozások az online migrálást az Azure-adatbázis MySQL-hez

Ismert problémák és korlátozások online migrálást a MySQL az Azure Database for MySQL-hez társított a következő szakaszokban ismertetett. 

## <a name="online-migration-configuration"></a>Online migrálás konfiguráció
- A forrásul szolgáló MySQL-kiszolgáló verziójának a következőnek kell lennie: 5.6.35, 5.7.18 vagy újabb
- Azure Database for MySQL támogatja:
    - A MySQL közösségi kiadása
    - InnoDB motor
- Azonos verziójú áttelepítés. Azure database for MySQL 5.7-es áttelepítése MySQL 5.6 nem támogatott.
- Bináris naplózás engedélyezése a my.ini (Windows) vagy my.cnf (Unix)
    - Állítsa be a Server_id akárhány nagyobb vagy egyenlő 1, például a Server_id = 1 (csak a MySQL 5.6-os)
    - Állítsa be a log-bin = <path> (csak a MySQL 5.6-os)
    - Állítsa be a binlog_format = sor
    - Expire_logs_days = 5 (ajánlott – csak a MySQL 5.6-os)
- Felhasználónak a ReplicationAdmin szerepkörrel kell rendelkeznie.
- A forrás MySQL-adatbázishoz meghatározott rendezések ugyanazok, mint a kapcsolatok meghatározott cél Azure Database for MySQL-hez.
- MySQL-hez készült séma forrás MySQL-adatbázis és a céladatbázis, Azure Database-ben között meg kell egyeznie.
- A célhelyen, Azure Database for MySQL séma nem rendelkeznie kell a külső kulcsok. Idegen kulcsok használja a következő lekérdezést:
    ```
    SET group_concat_max_len = 8192;
    SELECT SchemaName, GROUP_CONCAT(DropQuery SEPARATOR ';\n') as DropQuery, GROUP_CONCAT(AddQuery SEPARATOR ';\n') as AddQuery
    FROM
    (SELECT 
    KCU.REFERENCED_TABLE_SCHEMA as SchemaName, KCU.TABLE_NAME, KCU.COLUMN_NAME,
        CONCAT('ALTER TABLE ', KCU.TABLE_NAME, ' DROP FOREIGN KEY ', KCU.CONSTRAINT_NAME) AS DropQuery,
        CONCAT('ALTER TABLE ', KCU.TABLE_NAME, ' ADD CONSTRAINT ', KCU.CONSTRAINT_NAME, ' FOREIGN KEY (`', KCU.COLUMN_NAME, '`) REFERENCES `', KCU.REFERENCED_TABLE_NAME, '` (`', KCU.REFERENCED_COLUMN_NAME, '`) ON UPDATE ',RC.UPDATE_RULE, ' ON DELETE ',RC.DELETE_RULE) AS AddQuery
        FROM INFORMATION_SCHEMA.KEY_COLUMN_USAGE KCU, information_schema.REFERENTIAL_CONSTRAINTS RC
        WHERE
          KCU.CONSTRAINT_NAME = RC.CONSTRAINT_NAME
          AND KCU.REFERENCED_TABLE_SCHEMA = RC.UNIQUE_CONSTRAINT_SCHEMA
      AND KCU.REFERENCED_TABLE_SCHEMA = ['schema_name']) Queries
      GROUP BY SchemaName;
    ```

    Futtassa a drop külső kulcs (amely a második oszlop) a lekérdezés eredményei.
- A célhelyen, Azure Database for MySQL séma kell nem tartozhat eseményindító. Eseményindítók eldobni a céladatbázisban:
    ```
    SELECT Concat('DROP TRIGGER ', Trigger_Name, ';') FROM  information_schema.TRIGGERS WHERE TRIGGER_SCHEMA = 'your_schema';
    ```

## <a name="datatype-limitations"></a>Adattípus-korlátozások
- **Korlátozás**: Ha a MySQL-adatbázisban egy JSON-adattípus, az áttelepítés sikertelen lesz a folyamatos szinkronizálás során.

    **Megkerülő megoldás**: Közepes szöveg-vagy MySQL-adatbázisban longtext módosítása JSON adattípus.

- **Korlátozás**: nincs elsődleges kulcs azokon a táblákon, ha a folyamatos szinkronizálás sikertelen lesz.
 
    **Megkerülő megoldás**: ideiglenesen állítsa az áttelepítés folytatásához a tábla elsődleges kulcsot. Adatok áttelepítés befejezése után eltávolíthatja az elsődleges kulcsot.

## <a name="lob-limitations"></a>LOB-korlátozások
Nagyméretű objektum (LOB) oszlopot, amely a nagy méretű tudta növelni oszlopok. A MySQL, közepes méretű szöveges Longtext, Blob, Mediumblob, Longblob, stb. néhány a adattípusokat a LOB-ot.

- **Korlátozás**: Ha a LOB-adattípusokat elsődleges kulcsként használja, a migrálás meghiúsul.

    **Megkerülő megoldás**: elsődleges kulcs cserélje le az oszlopokat, amelyeket nem az ÜZLETÁGI és más adattípusok.

- **Korlátozás**: Ha nagyméretű objektum (LOB) oszlop hossza 32 KB-nál nagyobb, adatok lerövidítheti a cél. Ez a lekérdezés használatával LOB oszlop hossza ellenőrizheti:
    ```
    SELECT max(length(description)) as LEN from catalog;
    ```

    **Megkerülő megoldás**: Ha LOB-objektum, amely 32 KB-nál nagyobb méretű, lépjen kapcsolatba a mérnöki csapathoz a következő címen [ dmsfeedback@microsoft.com ](mailto:dmsfeedback@microsoft.com). 

## <a name="other-limitations"></a>Egyéb korlátozások is érvényesek
- A jelszó karakterlánc, amely rendelkezik a nyitó és záró kapcsos zárójelek közé {} elején és végén a jelszó-karakterlánc nem támogatott. Ez a korlátozás vonatkozik, mindkét való kapcsolódás forrás MySQL és a cél Azure Database for MySQL-hez.
- A következő DDLs nem támogatottak:
    - Az összes partíció DDLs
    - DROP table
    - Táblázat átnevezése
- Használatával a *alter tábla < táblanév > < oszlopnév > oszlop hozzáadása* utasítással elején vagy közepén levő táblázat oszlopok hozzáadása nem támogatott. A *az alter table < table_name > < oszlopnév > oszlop hozzáadása* hozzáadja az oszlop a tábla végén.
- Csak az oszlopok adatainak egy része a létrehozott indexek nem támogatottak. A következő egy példa, amely létrehoz egy indexet az oszlopadatok csak egy részét használja a következő utasítást:
    ``` 
    CREATE INDEX partial_name ON customer (name(10));
    ```

- A DMS egy egyetlen migrálási tevékenységet a migrálni kívánt adatbázisok határérték négy.
