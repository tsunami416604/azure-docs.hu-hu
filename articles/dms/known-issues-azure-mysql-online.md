---
title: 'Ismert problémák: Online áttelepítések a MySQL Azure-adatbázisába'
titleSuffix: Azure Database Migration Service
description: Ismerje meg az Azure Database Migration Service használatakor az Azure Database Migration Service használatával kapcsolatos ismert problémákat és áttelepítési korlátozásokat az Azure Database Database for MySQL-be való áttelepítéssel kapcsolatban.
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
ms.openlocfilehash: 8c3de28ea934302086a5b14e61482e6a4ab9a7ca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80235281"
---
# <a name="online-migration-issues--limitations-to-azure-db-for-mysql-with-azure-database-migration-service"></a>Az Azure DB for MySQL & korlátozásokkal kapcsolatos online áttelepítési problémák az Azure Database Migration Service szolgáltatással

A MySQL-ből az Azure Database for MySQL-be történő online áttelepítéssel kapcsolatos ismert problémákat és korlátozásokat a következő szakaszok ismertetik.

## <a name="online-migration-configuration"></a>Online áttelepítési konfiguráció


- A forrás MySQL Server verziónak 5.6.35, 5.7.18 vagy újabb verziónak kell lennie
- Az Azure Database for MySQL támogatja:
  - MySQL közösségi kiadás
  - InnoDB motor
- Ugyanaz a verzió áttelepítése. A MySQL 5.6 áttelepítése az Azure Database for MySQL 5.7-re nem támogatott.
- Bináris naplózás engedélyezése a my.ini (Windows) vagy a my.cnf (Unix) fájlban
  - Állítsa Server_id tetszőleges nagyobb vagy egyenlő számra, például Server_id=1 (csak a MySQL 5.6 esetén)
  - Log-bin = \<elérési út> beállítása (csak a MySQL 5.6 esetén)
  - binlog_format = sor beállítása
  - Expire_logs_days = 5 (ajánlott - csak a MySQL 5.6)
- A felhasználónak ReplicationAdmin szerepkört kell játszania.
- A forrás MySQL-adatbázishoz definiált illesztések megegyeznek a mySQL cél Azure-adatbázisában definiált adatokkal.
- A sémának meg kell egyeznie a forrás MySQL-adatbázis és a céladatbázis között az Azure Database for MySQL-ben.
- Séma a cél Azure Database for MySQL nem rendelkezik idegen kulcsokkal. Az idegen kulcsok eldobásához használja az alábbi lekérdezést:
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
- Séma a cél Azure Database for MySQL nem rendelkezhet eseményindítók. Eseményindítók eldobása a céladatbázisban:
    ```
    SELECT Concat('DROP TRIGGER ', Trigger_Name, ';') FROM  information_schema.TRIGGERS WHERE TRIGGER_SCHEMA = 'your_schema';
    ```

## <a name="datatype-limitations"></a>Adattípus-korlátozások

- **Korlátozás**: Ha a forrás MySQL-adatbázisban JSON-adattípus található, az áttelepítés sikertelen lesz a folyamatos szinkronizálás során.

    **Megoldás:** Módosítsa a JSON-adattípust közepes szövegre vagy hosszú szövegre a Forrás MySQL adatbázisban.

- **Korlátozás**: Ha nincs elsődleges kulcs a táblákon, a folyamatos szinkronizálás sikertelen lesz.

    **Megoldás:** Ideiglenesen állítson be elsődleges kulcsot a tábla számára az áttelepítés folytatásához. Az elsődleges kulcsot az adatáttelepítés befejezése után távolíthatja el.

## <a name="lob-limitations"></a>Lob-korlátozások

A nagy objektumoszlopok (LOB) olyan oszlopok, amelyek mérete megnőhet. A MySQL, közepes szöveg, Longtext, Blob, Mediumblob, Longblob, stb, néhány adattípus a LOB.

- **Korlátozás**: Ha a LOB adattípusait elsődleges kulcsként használja, az áttelepítés sikertelen lesz.

    **Megoldás:** Cserélje le az elsődleges kulcsot más adattípusokra vagy oszlopokra, amelyek nem lob.Megoldás: Cserélje le az elsődleges kulcsot más adattípusokra vagy oszlopokra, amelyek nem lob.

- **Korlátozás:** Ha a nagy objektum (LOB) oszlopának hossza 32 KB-nál nagyobb, előfordulhat, hogy az adatok csonkulnak a célnál. A LOB-oszlop hosszát a lekérdezés segítségével ellenőrizheti:
    ```
    SELECT max(length(description)) as LEN from catalog;
    ```

    **Megoldás:** Ha 32 KB-nál nagyobb LOB-objektummal rendelkezik, lépjen kapcsolatba az Ask Azure Database Migrations mérnöki [csapatával.](mailto:AskAzureDatabaseMigrations@service.microsoft.com)

## <a name="limitations-when-migrating-online-from-aws-rds-mysql"></a>Korlátozások az aws RDS MySQL-ről történő online áttelepítéskor

Amikor online áttelepítést próbál végrehajtani az AWS RDS MySQL-ről a MySQL-alapú Azure Database-re, a következő hibákléphetnek fel.

- **Hiba:** Az{0}adatbázis ' ' ' célkulcs(oka)val rendelkezik. Javítsa a célt, és indítson új adatmigrálási tevékenységet. Az alábbi parancsfájl végrehajtása a célon az idegen kulcs(ok) listázásához

  **Korlátozás**: Ha idegen kulcsok vannak a sémában, az áttelepítés kezdeti terhelése és folyamatos szinkronizálása sikertelen lesz.
  **Megoldás:** A mySQL munkapadján hajtsa végre a következő parancsfájlt a drop foreign key parancsfájl kinyeréséhez és az idegen kulcsparancsfájl hozzáadásához:

  ```
  SET group_concat_max_len = 8192; SELECT SchemaName, GROUP_CONCAT(DropQuery SEPARATOR ';\n') as DropQuery, GROUP_CONCAT(AddQuery SEPARATOR ';\n') as AddQuery FROM (SELECT KCU.REFERENCED_TABLE_SCHEMA as SchemaName, KCU.TABLE_NAME, KCU.COLUMN_NAME, CONCAT('ALTER TABLE ', KCU.TABLE_NAME, ' DROP FOREIGN KEY ', KCU.CONSTRAINT_NAME) AS DropQuery, CONCAT('ALTER TABLE ', KCU.TABLE_NAME, ' ADD CONSTRAINT ', KCU.CONSTRAINT_NAME, ' FOREIGN KEY (`', KCU.COLUMN_NAME, '`) REFERENCES `', KCU.REFERENCED_TABLE_NAME, '` (`', KCU.REFERENCED_COLUMN_NAME, '`) ON UPDATE ',RC.UPDATE_RULE, ' ON DELETE ',RC.DELETE_RULE) AS AddQuery FROM INFORMATION_SCHEMA.KEY_COLUMN_USAGE KCU, information_schema.REFERENTIAL_CONSTRAINTS RC WHERE KCU.CONSTRAINT_NAME = RC.CONSTRAINT_NAME AND KCU.REFERENCED_TABLE_SCHEMA = RC.UNIQUE_CONSTRAINT_SCHEMA AND KCU.REFERENCED_TABLE_SCHEMA = 'SchemaName') Queries GROUP BY SchemaName;
  ```

- **Hiba:** Az{0}adatbázis ' ' nem létezik a kiszolgálón. A megadott MySQL-forráskiszolgáló esetében különbözőnek számítanak a kis- és nagybetűk. Ellenőrizze az adatbázis nevét.

  **Korlátozás**: Ha egy MySQL-adatbázist a PARANCSsori felület (CLI) segítségével az Azure-ba telepít, a felhasználók eltalálhatják ezt a hibát. A szolgáltatás nem találta az adatbázist a forráskiszolgálón, ami lehet, hogy helytelen adatbázisnevet adott meg, vagy az adatbázis nem létezik a felsorolt kiszolgálón. A megjegyzés adatbázisnevei ben a kis- és nagybetűk et nem lehet figyelembe venni.

  **Megoldás:** Adja meg az adatbázis pontos nevét, majd próbálkozzon újra.

- **Hiba:** A(z) "{database}" adatbázisban ugyanazzal a névvel rendelkező táblák találhatók. Az Azure Database for MySQL nem támogatja a kis- és nagybetűket megkülönböztető táblákat.

  **Korlátozás**: Ez a hiba akkor fordul elő, ha két azonos nevű tábla van a forrásadatbázisban. Az Azure Database for MySQL nem támogatja a kis- és nagybetűket megkülönböztető táblákat.

  **Megoldás:** Frissítse a táblaneveket egyedivé, majd próbálkozzon újra.

- **Hiba:** A(z) {database} céladatbázis üres. Migrálja a sémát.

  **Korlátozás:** Ez a hiba akkor fordul elő, ha a cél Azure Database for MySQL adatbázis nem rendelkezik a szükséges sémával. Séma áttelepítése szükséges az adatok áttelepítéséhez a cél.

  **Megoldás:** [A séma áttelepítése a](https://docs.microsoft.com/azure/dms/tutorial-mysql-azure-mysql-online#migrate-the-sample-schema) forrásadatbázisból a céladatbázisba.

## <a name="other-limitations"></a>Egyéb korlátozások

- A jelszókarakterlánc elején és végén nyitó és záró göndör zárójelek { } nem támogatottak. Ez a korlátozás a MySQL forráshoz való csatlakozásra és a MySQL célAzure-adatbázisához való csatlakozásra egyaránt vonatkozik.
- A következő DDL-ek nem támogatottak:
  - Minden partíció DDLs
  - Táblázat eldobása
  - Tábla átnevezése
- A *módosító tábla <table_name használata table_name,> <column_name>* utasítást nem ad hozzá a táblázat elejéhez vagy közepéhez. Módosítja *a táblázatot <table_name> hozzáadja <oszlopot column_name,>hozzáadja* az oszlopot a táblázat végén.
- Az oszlopadatoknak csak egy részén létrehozott indexek nem támogatottak. A következő utasítás egy példa, amely az oszlopadatoknak csak egy részét használó indexet hoz létre:

    ``` 
    CREATE INDEX partial_name ON customer (name(10));
    ```

- Az Azure Database Migration Service,a korlát adatbázisok egyetlen áttelepítési tevékenység négy.

- **Hiba:** A sor mérete túl nagy (> 8126). Néhány oszlop SZÖVEG vagy BLOB-ra való módosítása segíthet. Az aktuális sorformátumban a BLOB 0 bájtból álló előtagja a szövegközi tárolóhelyen tárolódik.

  **Korlátozás:** Ez a hiba akkor fordul elő, ha az InnoDB tárolómotorhasználatával a MySQL Azure Database for MySQL szolgáltatásba való áttelepítése során történik, és a táblasor mérete túl nagy (>8126 bájt).

  **Megoldás:** Frissítse a 8126 bájtnál nagyobb sorméretű tábla sémáját. Nem javasoljuk a szigorú mód módosítását, mert az adatok csonkulnak. A page_size módosítása nem támogatott.
