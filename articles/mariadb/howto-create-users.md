---
title: Felhasználók létrehozása – Azure Database for MariaDB
description: Ez a cikk azt ismerteti, hogyan hozhat létre új felhasználói fiókokat Azure Database for MariaDB-kiszolgálóval való kommunikációhoz.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 4/2/2020
ms.openlocfilehash: 1b79a49b2fb87ebf180aaaa40447f40c5a982c2e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80632283"
---
# <a name="create-users-in-azure-database-for-mariadb"></a>Felhasználók létrehozása az Azure Database for MariaDB-ben 
Ez a cikk azt ismerteti, hogyan hozhatók létre felhasználók a Azure Database for MariaDBban.

Amikor először hozta létre a Azure Database for MariaDB, a kiszolgáló-rendszergazdai bejelentkezési felhasználónevet és jelszót adott meg. További információt a rövid útmutatóban [talál.](quickstart-create-mariadb-server-database-using-azure-portal.md) A kiszolgáló-rendszergazdai bejelentkezési felhasználónevet megkeresheti a Azure Portal.

A kiszolgáló-rendszergazda felhasználó bizonyos jogosultságokat kap a kiszolgáló számára a felsoroltak szerint: kijelölés, Beszúrás, frissítés, törlés, létrehozás, eldobás, Újratöltés, feldolgozás, hivatkozások, INDEX, ALTER, adatbázisok megjelenítése, ideiglenes táblák létrehozása, táblák ZÁROLÁSa, végrehajtás, REPLIKÁLÁSi SLAVE, replikációs ügyfél, létrehozási nézet, a nézet megjelenítése, RUTIN létrehozása

A Azure Database for MariaDB-kiszolgáló létrehozása után az első kiszolgáló-rendszergazdai felhasználói fiókkal további felhasználókat hozhat létre, és rendszergazdai hozzáférést adhat hozzájuk. A kiszolgálói rendszergazdai fiókkal kevesebb jogosultsággal rendelkező felhasználó hozható létre, akik egyéni adatbázis-sémákkal rendelkeznek hozzáféréssel.

> [!NOTE]
> A SUPER Privilege és a DBA szerepkör nem támogatott. Tekintse át a korlátozások című cikkben szereplő [jogosultságokat](concepts-limits.md#privilege-support) , hogy megtudja, mi nem támogatott a szolgáltatásban.

## <a name="create-additional-admin-users"></a>További rendszergazda felhasználók létrehozása
1. Kérje le a kapcsolatfelvételi adatokat és a rendszergazda felhasználónevét.
   Az adatbázis-kiszolgálóhoz való csatlakozáshoz szüksége van a teljes kiszolgálónévre és a rendszergazdai bejelentkezési hitelesítő adatokra. A kiszolgáló és a bejelentkezési adatok könnyen megtalálhatók a kiszolgáló **Áttekintés** lapján vagy a Azure Portal **Tulajdonságok** lapján. 

2. Az adatbázis-kiszolgálóhoz való kapcsolódáshoz használja a rendszergazdai fiókot és a jelszót. Használhatja az előnyben részesített ügyfélprogramot, például a MySQL Workbench, a MySQL. exe, a HeidiSQL vagy más eszközöket. 
   Ha nem tudja, hogyan csatlakozhat, tekintse meg a következőt: a [MySQL Workbench használata a kapcsolódáshoz és az adatlekérdezéshez](./connect-workbench.md)

3. Szerkessze és futtassa a következő SQL-kódot. Cserélje le az új felhasználónevet a helyőrző értékre `new_master_user`. Ez a szintaxis megadja a felsorolt jogosultságokat az összes adatbázis-sémán (*.*) a felhasználónévre (new_master_user ebben a példában). 

   ```sql
   CREATE USER 'new_master_user'@'%' IDENTIFIED BY 'StrongPassword!';
   
   GRANT SELECT, INSERT, UPDATE, DELETE, CREATE, DROP, RELOAD, PROCESS, REFERENCES, INDEX, ALTER, SHOW DATABASES, CREATE TEMPORARY TABLES, LOCK TABLES, EXECUTE, REPLICATION SLAVE, REPLICATION CLIENT, CREATE VIEW, SHOW VIEW, CREATE ROUTINE, ALTER ROUTINE, CREATE USER, EVENT, TRIGGER ON *.* TO 'new_master_user'@'%' WITH GRANT OPTION; 
   
   FLUSH PRIVILEGES;
   ```

4. A támogatások ellenőrzése 
   ```sql
   USE sys;
   
   SHOW GRANTS FOR 'new_master_user'@'%';
   ```

## <a name="create-database-users"></a>Adatbázis-felhasználók létrehozása

1. Kérje le a kapcsolatfelvételi adatokat és a rendszergazda felhasználónevét.
   Az adatbázis-kiszolgálóhoz való csatlakozáshoz szüksége van a teljes kiszolgálónévre és a rendszergazdai bejelentkezési hitelesítő adatokra. A kiszolgáló és a bejelentkezési adatok könnyen megtalálhatók a kiszolgáló **Áttekintés** lapján vagy a Azure Portal **Tulajdonságok** lapján. 

2. Az adatbázis-kiszolgálóhoz való kapcsolódáshoz használja a rendszergazdai fiókot és a jelszót. Használhatja az előnyben részesített ügyfélprogramot, például a MySQL Workbench, a MySQL. exe, a HeidiSQL vagy más eszközöket. 
   Ha nem tudja, hogyan csatlakozhat, tekintse meg a következőt: a [MySQL Workbench használata a kapcsolódáshoz és az adatlekérdezéshez](./connect-workbench.md)

3. Szerkessze és futtassa a következő SQL-kódot. Cserélje le a helyőrző `db_user` értékét a kívánt új felhasználónévre, és adja meg `testdb` a helyőrző értékét a saját adatbázisának nevével.

   Ez az SQL Code-szintaxis egy új, testdb nevű adatbázist hoz létre példaként. Ezután létrehoz egy új felhasználót a Azure Database for MariaDB szolgáltatásban, és minden jogosultságot biztosít az adott felhasználóhoz tartozó új adatbázis-\*sémához (testdb.). 

   ```sql
   CREATE DATABASE testdb;
   
   CREATE USER 'db_user'@'%' IDENTIFIED BY 'StrongPassword!';
   
   GRANT ALL PRIVILEGES ON testdb . * TO 'db_user'@'%';
   
   FLUSH PRIVILEGES;
   ```

4. Ellenőrizze az adatbázison belüli támogatást.
   ```sql
   USE testdb;
   
   SHOW GRANTS FOR 'db_user'@'%';
   ```

5. Jelentkezzen be a kiszolgálóra, és adja meg a kijelölt adatbázist az új Felhasználónév és jelszó használatával. Ez a példa a MySQL parancssort jeleníti meg. Ezzel a paranccsal a rendszer a felhasználónévhez tartozó jelszót kéri. Cserélje le a saját kiszolgáló nevét, az adatbázis nevét és a felhasználónevet.

   ```bash
   mysql --host mydemoserver.mariadb.database.azure.com --database testdb --user db_user@mydemoserver -p
   ```
   A felhasználói fiókok kezelésével kapcsolatos további információkért lásd: MariaDB dokumentáció a [felhasználói fiókok kezeléséhez](https://mariadb.com/kb/en/library/user-account-management/), a jogosultságok [megadása](https://mariadb.com/kb/en/library/grant/)és a [jogosultságok](https://mariadb.com/kb/en/library/grant/#privilege-levels).

## <a name="next-steps"></a>További lépések
Nyissa meg a tűzfalat az új felhasználói gépek IP-címei számára a kapcsolódáshoz: [Azure Database for MariaDB tűzfalszabályok létrehozása és kezelése a Azure Portal használatával](howto-manage-firewall-portal.md)  

<!--or [Azure CLI](howto-manage-firewall-using-cli.md).-->
