---
title: Felhasználók létrehozása - Azure Database for MySQL
description: Ez a cikk bemutatja, hogyan hozhat létre új felhasználói fiókokat a MySQL-kiszolgálóazure-adatbázisával való együttműködéshez.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 4/2/2020
ms.openlocfilehash: 99b614de87c666d1cb1fb8a34eaafadf6fa82849
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80632551"
---
# <a name="create-users-in-azure-database-for-mysql-server"></a>Felhasználók létrehozása az Azure Database for MySQL-kiszolgálón

Ez a cikk azt ismerteti, hogyan hozhat létre felhasználókat egy Azure Database for MySQL-kiszolgálóban.

Amikor először hozta létre az Azure Database for MySQL, megadta a kiszolgáló rendszergazdai bejelentkezési felhasználónevet és jelszót. További információt a Rövid [útmutató ban](quickstart-create-mysql-server-database-using-azure-portal.md)talál. Az Azure Portalon megkeresheti a kiszolgáló rendszergazdájának bejelentkezési felhasználónevét.

A kiszolgáló rendszergazdája bizonyos jogosultságokat kap a kiszolgálóhoz a következő ként: SELECT, INSERT, UPDATE, DELETE, CREATE, DROP, RELOAD, PROCESS, REFERENCES, INDEX, ALTER, SHOW DATABASES, CREATE TEMPORARY TABLES, LOCK TABLES, EXECUTE, REPLICATION SLAVE, REPLICATION CLIENT, CREATE VIEW, SHOW VIEW, CREATE ROUTINE, ALTER ROUTINE, CREATE USER, EVENT, TRIGGER

Az Azure Database for MySQL-kiszolgáló létrehozása után az első kiszolgálófelügyeleti felhasználói fiókkal további felhasználókat hozhat létre, és rendszergazdai hozzáférést biztosíthat hozzájuk. Emellett a kiszolgálófelügyeleti fiók segítségével kevésbé kiemelt jogosultságú felhasználókat hozhat létre, akik hozzáférnek az egyes adatbázissémákhoz.

> [!NOTE]
> A SUPER jogosultság és a DBA szerepkör nem támogatott. Tekintse át a [jogosultságokat](concepts-limits.md#privilege-support) a korlátozások cikkben, hogy mi nem támogatott a szolgáltatásban.

## <a name="how-to-create-additional-admin-users-in-azure-database-for-mysql"></a>További rendszergazdai felhasználók létrehozása a MySQL Azure Database szolgáltatásában

1. A kapcsolat adatainak és a rendszergazdai felhasználónévnek a beszerezése.
   Az adatbázis-kiszolgálóhoz való csatlakozáshoz szüksége van a teljes kiszolgálónévre és a rendszergazdai bejelentkezési hitelesítő adatokra. A kiszolgáló nevét és bejelentkezési adatait könnyen megtalálhatja a kiszolgáló **áttekintése** lapon vagy az Azure Portal **Tulajdonságok** lapján.

2. Az adatbázis-kiszolgálóhoz való csatlakozáshoz használja a rendszergazdai fiókot és a jelszót. Használja a kívánt ügyféleszközt, például a MySQL Workbench, a mysql.exe, a HeidiSQL vagy mások.
   Ha nem biztos a csatlakozás módjában, olvassa el [a MySQL Workbench használata adatok csatlakoztatásához és lekérdezéséhez című témakört.](./connect-workbench.md)

3. A következő SQL-kód szerkesztése és futtatása. Cserélje le az új felhasználónevet `new_master_user`a helyőrző értékre. Ez a szintaxis az összes adatbázisséma (*.*) felsorolt jogosultságait a felhasználónévre (new_master_user adja meg ebben a példában).

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

## <a name="how-to-create-database-users-in-azure-database-for-mysql"></a>Adatbázis-felhasználók létrehozása az Azure Database for MySQL-ben

1. A kapcsolat adatainak és a rendszergazdai felhasználónévnek a beszerezése.
   Az adatbázis-kiszolgálóhoz való csatlakozáshoz szüksége van a teljes kiszolgálónévre és a rendszergazdai bejelentkezési hitelesítő adatokra. A kiszolgáló nevét és bejelentkezési adatait könnyen megtalálhatja a kiszolgáló **áttekintése** lapon vagy az Azure Portal **Tulajdonságok** lapján.

2. Az adatbázis-kiszolgálóhoz való csatlakozáshoz használja a rendszergazdai fiókot és a jelszót. Használja a kívánt ügyféleszközt, például a MySQL Workbench, a mysql.exe, a HeidiSQL vagy mások.
   Ha nem biztos a csatlakozás módjában, olvassa el [a MySQL Workbench használata adatok csatlakoztatásához és lekérdezéséhez című témakört.](./connect-workbench.md)

3. A következő SQL-kód szerkesztése és futtatása. Cserélje le a `db_user` helyőrző értéket a kívánt új `testdb` felhasználónévre, és a helyőrző értéket a saját adatbázisnevére.

   Ez az sql kód szintaxis a testdb nevű új adatbázist hoz létre például célokra. Ezután létrehoz egy új felhasználót a MySQL szolgáltatásban, és minden jogosultságot\*biztosít az új adatbázissémának (testdb. ) az adott felhasználó számára.

   ```sql
   CREATE DATABASE testdb;

   CREATE USER 'db_user'@'%' IDENTIFIED BY 'StrongPassword!';

   GRANT ALL PRIVILEGES ON testdb . * TO 'db_user'@'%';

   FLUSH PRIVILEGES;
   ```

4. Ellenőrizze a támogatások at az adatbázisban.

   ```sql
   USE testdb;

   SHOW GRANTS FOR 'db_user'@'%';
   ```

5. Jelentkezzen be a kiszolgálóra, adja meg a kijelölt adatbázist az új felhasználónév és jelszó használatával. Ez a példa a mysql parancssorát mutatja. Ezzel a paranccsal a rendszer kéri a felhasználónév jelszavát. Cserélje le saját kiszolgálónevét, adatbázisnevét és felhasználónevét.

   ```azurecli-interactive
   mysql --host mydemoserver.mysql.database.azure.com --database testdb --user db_user@mydemoserver -p
   ```

## <a name="next-steps"></a>További lépések

Nyissa meg a tűzfalat az új felhasználók gépeinek IP-címeihez, hogy azok csatlakozni tudjanak: [Azure Database for MySQL tűzfalszabályok létrehozása és kezelése az Azure Portalon](howto-manage-firewall-using-portal.md) vagy az [Azure CLI](howto-manage-firewall-using-cli.md)használatával.

A felhasználói fiókok kezelésével kapcsolatos további információkért olvassa el a Felhasználói [fiókok kezeléséhez](https://dev.mysql.com/doc/refman/5.7/en/user-account-management.html), a [GRANT szintaxishoz](https://dev.mysql.com/doc/refman/5.7/en/grant.html)és [a Jogosultságokhoz](https://dev.mysql.com/doc/refman/5.7/en/privileges-provided.html)tartozó MySQL termékdokumentációját.
