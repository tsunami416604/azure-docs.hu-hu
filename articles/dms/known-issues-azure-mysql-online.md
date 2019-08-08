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
ms.openlocfilehash: fc5565ab9e3be21b96ce5aa5a938cf22ec3caeb0
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/08/2019
ms.locfileid: "68848488"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-to-azure-db-for-mysql"></a>Ismert problémák/áttelepítési korlátozások a MySQL-hez készült Azure DB-vel való online áttelepítéssel

A MySQL-ről Azure Database for MySQLre való online áttelepítéssel kapcsolatos ismert problémák és korlátozások a következő szakaszokban olvashatók.

## <a name="online-migration-configuration"></a>Online áttelepítési konfiguráció

- A forrás MySQL-kiszolgáló verziójának 5.6.35, 5.7.18 vagy újabb verziójúnak kell lennie
- Azure Database for MySQL a következőket támogatja:
  - MySQL Community Edition
  - InnoDB motor
- Azonos verziójú áttelepítés. A MySQL 5,6 áttelepítése Azure Database for MySQL 5,7-re nem támogatott.
- Bináris naplózás engedélyezése a My. ini (Windows) vagy a My. cnf (UNIX) rendszeren
  - Server_id beállítása tetszőleges számú vagy egyenlő értékre, például Server_id = 1 (csak MySQL 5,6 esetén)
  - A log-bin = \<Path > beállítása (csak MySQL 5,6 esetén)
  - Binlog_format beállítása = sor
  - Expire_logs_days = 5 (csak a MySQL 5,6 esetén ajánlott)
- A felhasználónak rendelkeznie kell a ReplicationAdmin szerepkörrel.
- A forrás MySQL-adatbázishoz definiált rendezések ugyanazok, mint a TARGET Azure Database for MySQLban definiált beállítások.
- A sémának meg kell egyeznie a forrás MySQL-adatbázis és a célként megadott adatbázis között Azure Database for MySQL.
- A cél Azure Database for MySQL sémája nem rendelkezhet idegen kulcsokkal. A külső kulcsok eldobásához használja az alábbi lekérdezést:
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

    Futtassa a ’drop foreign key’-t (ez a második oszlop) a lekérdezési eredményben.
- A cél Azure Database for MySQL sémájában nem lehetnek eseményindítók. Eseményindítók eldobása a céladatbázisben:
    ```
    SELECT Concat('DROP TRIGGER ', Trigger_Name, ';') FROM  information_schema.TRIGGERS WHERE TRIGGER_SCHEMA = 'your_schema';
    ```

## <a name="datatype-limitations"></a>Adattípus-korlátozások

- **Korlátozás**: Ha JSON-adattípust talál a forrás MySQL-adatbázisban, a Migrálás a folyamatos szinkronizálás során meghiúsul.

    **Áthidaló megoldás**: A JSON-adattípust közepes szöveggé vagy LONGTEXT módosíthatja a forrás MySQL-adatbázisban.

- **Korlátozás**: Ha a táblákban nincs elsődleges kulcs, a folyamatos szinkronizálás sikertelen lesz.

    **Áthidaló megoldás**: A folytatáshoz átmenetileg állítsa be a tábla elsődleges kulcsát az áttelepítéshez. Az elsődleges kulcsot az adatáttelepítés befejeződése után is eltávolíthatja.

## <a name="lob-limitations"></a>LOB-korlátozások

A nagyméretű objektumok (LOB) oszlopai olyan oszlopok, amelyek mérete nagy méretekben nőhet. MySQL-hez, közepes szövegekhez, LONGTEXT, Blobhoz, mediumblob, dereglye stb., a LOB egyes adattípusai.

- **Korlátozás**: Ha a LOB-adattípusok elsődleges kulcsként használatosak, az áttelepítés sikertelen lesz.

    **Áthidaló megoldás**: Cserélje le az elsődleges kulcsot más adattípusokra vagy nem LOB oszlopokra.

- **Korlátozás**: Ha a nagyméretű objektum (LOB) oszlop hossza meghaladja a 32 KB-ot, a rendszer az adatmennyiséget csonkolja a célhelyen. A LOB-oszlop hosszát a következő lekérdezéssel tekintheti meg:
    ```
    SELECT max(length(description)) as LEN from catalog;
    ```

    **Áthidaló megoldás**: Ha 32 KB-nál nagyobb LOB-objektummal rendelkezik, forduljon a mérnöki csapathoz az [Azure-adatbázis](mailto:AskAzureDatabaseMigrations@service.microsoft.com)áttelepítésekor. 

## <a name="limitations-when-migrating-online-from-aws-rds-mysql"></a>Korlátozások az AWS RDS MySQL-ről való online áttelepítés során

Ha az AWS RDS MySQL-ről a Azure Database for MySQLra próbál online áttelepítést végezni, akkor a következő hibák merülhetnek fel.

- **Hiba:** A ({0}z) "" adatbázis külső kulcsa a cél. Javítsa a célt, és indítson új adatmigrálási tevékenységet. Futtassa az alábbi szkriptet a célhelyen a külső kulcs (ok) listázásához

  **Korlátozás**: Ha a sémában idegen kulcsok szerepelnek, a migrálás első betöltése és folyamatos szinkronizálása sikertelen lesz.
  **Áthidaló megoldás**: Futtassa az alábbi szkriptet a MySQL Workbenchben a drop foreign key szkript (külső kulcs elvetése) és az add foreign key (külső kulcs hozzáadása) szkript kibontásához:

  ```
  SET group_concat_max_len = 8192; SELECT SchemaName, GROUP_CONCAT(DropQuery SEPARATOR ';\n') as DropQuery, GROUP_CONCAT(AddQuery SEPARATOR ';\n') as AddQuery FROM (SELECT KCU.REFERENCED_TABLE_SCHEMA as SchemaName, KCU.TABLE_NAME, KCU.COLUMN_NAME, CONCAT('ALTER TABLE ', KCU.TABLE_NAME, ' DROP FOREIGN KEY ', KCU.CONSTRAINT_NAME) AS DropQuery, CONCAT('ALTER TABLE ', KCU.TABLE_NAME, ' ADD CONSTRAINT ', KCU.CONSTRAINT_NAME, ' FOREIGN KEY (`', KCU.COLUMN_NAME, '`) REFERENCES `', KCU.REFERENCED_TABLE_NAME, '` (`', KCU.REFERENCED_COLUMN_NAME, '`) ON UPDATE ',RC.UPDATE_RULE, ' ON DELETE ',RC.DELETE_RULE) AS AddQuery FROM INFORMATION_SCHEMA.KEY_COLUMN_USAGE KCU, information_schema.REFERENTIAL_CONSTRAINTS RC WHERE KCU.CONSTRAINT_NAME = RC.CONSTRAINT_NAME AND KCU.REFERENCED_TABLE_SCHEMA = RC.UNIQUE_CONSTRAINT_SCHEMA AND KCU.REFERENCED_TABLE_SCHEMA = 'SchemaName') Queries GROUP BY SchemaName;
  ```

- **Hiba:** A ({0}z) "" adatbázis nem létezik a kiszolgálón. A megadott MySQL-forráskiszolgáló esetében különbözőnek számítanak a kis- és nagybetűk. Ellenőrizze az adatbázis nevét.

  **Korlátozás**: A MySQL-adatbázis a parancssori felület használatával az Azure-ba migrálásakor a felhasználók tapasztalhatják ezt a hibát. A szolgáltatás nem találta meg az adatbázist a forráskiszolgálón, mert lehetséges, hogy helytelen adatbázisnevet adott meg, vagy az adatbázis nem létezik a felsorolt kiszolgálón. Megjegyzés: az adatbázisok nevei megkülönböztetik a kis-és nagybetűket.

  **Áthidaló megoldás**: Adja meg az adatbázis pontos nevét, majd próbálkozzon újra.

- **Hiba:** A (z) {Database} adatbázisban azonos nevű táblák találhatók. Az Azure Database for MySQL nem támogatja a kis- és nagybetűket megkülönböztető táblákat.

  **Korlátozás**: Ez a hiba akkor fordul elő, ha két azonos nevű tábla található a forrásadatbázisban. A Azure Database for MySQL nem támogatja a kis-és nagybetűket megkülönböztető táblákat.

  **Áthidaló megoldás**: Frissítse a táblák nevét egyedire, majd próbálkozzon újra.

- **Hiba:** A célként megadott adatbázis ({Database}) üres. Migrálja a sémát.

  **Korlátozás**: Ez a hiba akkor fordul elő, ha a célként megadott Azure Database for MySQL adatbázis nem rendelkezik a szükséges sémával. A séma áttelepítése szükséges ahhoz, hogy lehetővé váljon az adatáttelepítés a célhelyre.

  **Áthidaló megoldás**: [Telepítse át a sémát](https://docs.microsoft.com/azure/dms/tutorial-mysql-azure-mysql-online#migrate-the-sample-schema) a forrás-adatbázisból a célként megadott adatbázisba.

## <a name="other-limitations"></a>Egyéb korlátozások

- A jelszó karakterláncának elején és végén lévő kapcsos zárójelek {} nyitó és záró karakterlánca nem támogatott. Ez a korlátozás a Source MySQL-hez és a TARGET Azure Database for MySQLhoz való csatlakozásra egyaránt vonatkozik.
- A következő DDLs nem támogatottak:
  - Minden partíciós DDLs
  - Táblázat eldobása
  - Táblázat átnevezése
- Az *Alter table < table_name > oszlop hozzáadása < column_name >* utasítás az oszlopok a tábla elejéhez vagy közepéhez való hozzáadásához nem támogatott. Az *Alter table < table_name > oszlop hozzáadása < column_name >* hozzáadja a tábla végén található oszlopot.
- Az oszlopok adattípusának csak egy részén létrehozott indexek nem támogatottak. Az alábbi utasítás egy olyan példát mutat be, amely egy indexet hoz létre az oszlop adatai csak egy részének használatával:

    ``` 
    CREATE INDEX partial_name ON customer (name(10));
    ```

- A DMS-ben az egyetlen áttelepítési tevékenységben az áttelepítéshez szükséges adatbázisok maximális száma négy.
