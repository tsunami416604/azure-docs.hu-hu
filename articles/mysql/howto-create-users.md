---
title: Felhasználók Azure-adatbázis létrehozása a MySQL-kiszolgáló
description: Ez a cikk ismerteti, hogyan kommunikál a MySQL-kiszolgáló egy Azure-adatbázis új felhasználói fiókokat hozhat létre.
services: mysql
author: jasonwhowell
ms.author: jasonh
editor: jasonwhowell
manager: kfile
ms.service: mysql
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: ee74ea9e114f6401bfcafe44ca3caedfcd0005c5
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35265618"
---
# <a name="create-users-in-azure-database-for-mysql-server"></a>Felhasználók Azure-adatbázis létrehozása a MySQL-kiszolgáló 
Ez a cikk ismerteti, hogyan hozhat létre felhasználók MySQL-kiszolgáló egy Azure-adatbázis.

A MySQL a az Azure-adatbázis kezdeti létrehozásakor megadott egy kiszolgálói rendszergazda bejelentkezési felhasználónevet és jelszót. További információkért kövesse a [gyors üzembe helyezés](quickstart-create-mysql-server-database-using-azure-portal.md). A kiszolgáló rendszergazdai bejelentkezési felhasználónevét, ha az Azure portálról keresheti meg.

A kiszolgálói rendszergazda felhasználó néhány jogosultsággal, a kiszolgáló lekérdezi, lásd: válassza ki, BESZÚRÁSA, frissítése, törlése, hozzon létre, dobja el, ÚJRABETÖLTÉS, folyamat, hivatkozások, INDEX, ALTER, ADATBÁZISOK megjelenítése, az ideiglenes táblák létrehozása, ZÁROLÁS táblák, végrehajtás, replikációs az alárendelt, replikációs ÜGYFÉL, NÉZET LÉTREHOZÁSA, MEGTEKINTÉS, RUTIN LÉTREHOZÁSA, RUTIN ALTER, FELHASZNÁLÓ, ESEMÉNY, ESEMÉNYINDÍTÓ LÉTREHOZÁSA

A MySQL-kiszolgálóhoz tartozó Azure-adatbázis létrehozása után az első kiszolgáló-rendszergazda felhasználói fiók segítségével további felhasználók létrehozása és a rendszergazda hozzáférést biztosíthat számukra. A kiszolgáló-rendszergazdai fiókot is, a korlátozott engedélyekkel rendelkező egyes adatbázis sémák hozzáféréssel rendelkező felhasználók létrehozásához használható.

## <a name="how-to-create-additional-admin-users-in-azure-database-for-mysql"></a>További rendszergazda felhasználóknak MySQL az Azure-adatbázis létrehozása
1. A kapcsolat információkat és a rendszergazda felhasználónév olvasható be.
   Az adatbázis-kiszolgálóhoz való csatlakozáshoz szüksége van a teljes kiszolgálónévre és a rendszergazdai bejelentkezési hitelesítő adatokra. Könnyedén megtalálhatja a kiszolgáló nevét és a bejelentkezési adatait a kiszolgálóról **áttekintése** lap vagy az **tulajdonságok** oldal az Azure portálon. 

2. A rendszergazdai fiókot és jelszót használja az adatbázis-kiszolgálóhoz való csatlakozáshoz. Eszközzel az előnyben részesített ügyfél, például a MySQL-munkaterületet, mysql.exe, HeidiSQL vagy mások számára. 
   Ha biztos benne, hogy hogyan csatlakoztathat, lásd: [MySQL-munkaterület használata kapcsolódási és lekérdezési adatok](./connect-workbench.md)

3. Szerkessze, és futtassa a következő SQL-kódot. Cserélje le a helyőrző értékét új felhasználónevét `new_master_user`. Ez a szintaxis a felsorolt jogosultságok az adatbázis-sémákat biztosít (*.*) a felhasználó neve (new_master_user ebben a példában). 

   ```sql
   CREATE USER 'new_master_user'@'%' IDENTIFIED BY 'StrongPassword!';
   
   GRANT SELECT, INSERT, UPDATE, DELETE, CREATE, DROP, RELOAD, PROCESS, REFERENCES, INDEX, ALTER, SHOW DATABASES, CREATE TEMPORARY TABLES, LOCK TABLES, EXECUTE, REPLICATION SLAVE, REPLICATION CLIENT, CREATE VIEW, SHOW VIEW, CREATE ROUTINE, ALTER ROUTINE, CREATE USER, EVENT, TRIGGER ON *.* TO 'new_master_user'@'%' WITH GRANT OPTION; 
   
   FLUSH PRIVILEGES;
   ```

4. Ellenőrizze a biztosít 
   ```sql
   USE sys;
   
   SHOW GRANTS FOR 'new_master_user'@'%';
   ```

## <a name="how-to-create-database-users-in-azure-database-for-mysql"></a>Adatbázis-felhasználók MySQL az Azure-adatbázis létrehozása

1. A kapcsolat információkat és a rendszergazda felhasználónév olvasható be.
   Az adatbázis-kiszolgálóhoz való csatlakozáshoz szüksége van a teljes kiszolgálónévre és a rendszergazdai bejelentkezési hitelesítő adatokra. Könnyedén megtalálhatja a kiszolgáló nevét és a bejelentkezési adatait a kiszolgálóról **áttekintése** lap vagy az **tulajdonságok** oldal az Azure portálon. 

2. A rendszergazdai fiókot és jelszót használja az adatbázis-kiszolgálóhoz való csatlakozáshoz. Eszközzel az előnyben részesített ügyfél, például a MySQL-munkaterületet, mysql.exe, HeidiSQL vagy mások számára. 
   Ha biztos benne, hogy hogyan csatlakoztathat, lásd: [MySQL-munkaterület használata kapcsolódási és lekérdezési adatok](./connect-workbench.md)

3. Szerkessze, és futtassa a következő SQL-kódot. Cserélje le a helyőrző értékét `db_user` a kívánt új felhasználó nevét, és a helyőrző értékét `testdb` a saját adatbázis nevével.

   Az sql-kódot szintaxis létrehoz egy új adatbázist testdb nevű példa. Akkor hoz létre egy új felhasználót a MySQL-szolgáltatás, és engedélyezi az összes szükséges engedélyekkel az új adatbázis-séma (testdb.\*) az adott felhasználó. 

   ```sql
   CREATE DATABASE testdb;
   
   CREATE USER 'db_user'@'%' IDENTIFIED BY 'StrongPassword!';
   
   GRANT ALL PRIVILEGES ON testdb . * TO 'db_user'@'%';
   
   FLUSH PRIVILEGES;
   ```

4. Ellenőrizze az adatbázis biztosít.
   ```sql
   USE testdb;
   
   SHOW GRANTS FOR 'db_user'@'%';
   ```

5. Jelentkezzen be a kiszolgálóra, adja meg a kijelölt adatbázis, az új felhasználónevet és jelszót használja. Ez a példa bemutatja a mysql parancssorban. Ezzel a paranccsal kéri a jelszót a felhasználónévvel. Cserélje le a saját kiszolgáló neve, az adatbázisnév és a felhasználó neve.

   ```azurecli-interactive
   mysql --host mydemoserver.mysql.database.azure.com --database testdb --user db_user@mydemoserver -p
   ```

## <a name="next-steps"></a>További lépések
Nyissa meg a tűzfalon a IP-címekhez, hogy azok tudjanak csatlakozni az új felhasználók gépek: [hozzon létre és kezelheti az Azure-adatbázis tűzfalszabályok MySQL az Azure portál használatával](howto-manage-firewall-using-portal.md) vagy [Azure CLI](howto-manage-firewall-using-cli.md).

Felhasználóifiók-kezelés kapcsolatos további információkért tekintse meg a termékhez tartozó dokumentáció MySQL [felhasználóifiók-kezelés](https://dev.mysql.com/doc/refman/5.7/en/user-account-management.html), [ADJON szintaxis](https://dev.mysql.com/doc/refman/5.7/en/grant.html), és [jogosultságokkal](https://dev.mysql.com/doc/refman/5.7/en/privileges-provided.html).
