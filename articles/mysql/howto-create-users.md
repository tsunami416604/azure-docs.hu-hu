---
title: Felhasználók létrehozása az Azure Database for MySQL-kiszolgáló
description: Ez a cikk bemutatja, hogyan kommunikálhat egy Azure Database for MySQL-kiszolgáló új felhasználói fiókokat hozhat létre.
author: jasonwhowell
ms.author: jasonh
ms.service: mysql
ms.topic: conceptual
ms.date: 02/28/2018
ms.openlocfilehash: e8714777b1f9f08de4d02fcb44c25197cdc48899
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/17/2018
ms.locfileid: "53546011"
---
# <a name="create-users-in-azure-database-for-mysql-server"></a>Felhasználók létrehozása az Azure Database for MySQL-kiszolgáló 
Ez a cikk bemutatja, hogyan hozhat létre felhasználókat az Azure Database for MySQL-kiszolgálót.

Első létrehozásakor az Azure Database for MySQL-hez, a megadott egy kiszolgálói rendszergazda felhasználónevét és jelszavát. További információkért kövesse a [rövid](quickstart-create-mysql-server-database-using-azure-portal.md). Megkeresheti a kiszolgáló rendszergazdai bejelentkezési felhasználónevet, az Azure Portalról.

A kiszolgáló-rendszergazdai felhasználó néhány jogosultsággal, a kiszolgáló felsorolt beolvasása: VÁLASSZA KI, BESZÚRÁSA, FRISSÍTÉSE, TÖRLÉSE, LÉTREHOZÁSA, DOBJA EL, TÖLTSE BE ÚJRA, FELDOLGOZNI, HIVATKOZÁSOK, INDEX, ALTER, ADATBÁZISOK, MEGJELENÍTÉSE LÉTREHOZÁSA IDEIGLENES TÁBLÁK, TÁBLÁZATOK, ZÁROLÁSA HAJTHATÓ VÉGRE, A REPLIKÁCIÓ ALÁRENDELT, REPLIKÁCIÓS ÜGYFÉL, HOZZON LÉTRE MEGTEKINTÉSE, NÉZET MEGJELENÍTÉSE, LÉTREHOZÁSA RUTIN, ALTER RUTIN, FELHASZNÁLÓ LÉTREHOZÁSA , EVENT, ESEMÉNYINDÍTÓ

Az Azure Database for MySQL-kiszolgáló létrehozása után további felhasználók létrehozása az első kiszolgálói rendszergazdai felhasználói fiók segítségével, rendszergazdai hozzáférést biztosít számukra. Emellett a kiszolgálói rendszergazdai fiókkal, amelyhez hozzáférése egyes adatbázissémák kevesebb jogosultsággal rendelkező felhasználók létrehozásához használható.

## <a name="how-to-create-additional-admin-users-in-azure-database-for-mysql"></a>További rendszergazdai felhasználók létrehozása az Azure Database MySQL-hez
1. Kérje le a kapcsolati információkat és a rendszergazdai felhasználó nevét.
   Az adatbázis-kiszolgálóhoz való csatlakozáshoz szüksége van a teljes kiszolgálónévre és a rendszergazdai bejelentkezési hitelesítő adatokra. Megtalálhatja a kiszolgáló nevét és bejelentkezési adatait a kiszolgálóról **áttekintése** lap vagy az **tulajdonságok** oldal az Azure Portalon. 

2. A rendszergazdai fiókot és jelszót használja az adatbázis-kiszolgálóhoz való kapcsolódáshoz. Használja az ügyfél előnyben részesített eszközt, például a MySQL Workbench, mysql.exe, HeidiSQL vagy mások. 
   Ha biztos benne, hogy hogyan kapcsolódhat, [való csatlakozás és adatlekérdezés a MySQL Workbench](./connect-workbench.md)

3. Szerkessze és futtassa a következő SQL-kódot. Cserélje le a helyőrző értékét az új felhasználói nevet `new_master_user`. Ezt a szintaxist biztosít a listában szereplő összes adatbázissémák jogosultságokkal (*.*), a felhasználó nevét (ebben a példában new_master_user). 

   ```sql
   CREATE USER 'new_master_user'@'%' IDENTIFIED BY 'StrongPassword!';
   
   GRANT SELECT, INSERT, UPDATE, DELETE, CREATE, DROP, RELOAD, PROCESS, REFERENCES, INDEX, ALTER, SHOW DATABASES, CREATE TEMPORARY TABLES, LOCK TABLES, EXECUTE, REPLICATION SLAVE, REPLICATION CLIENT, CREATE VIEW, SHOW VIEW, CREATE ROUTINE, ALTER ROUTINE, CREATE USER, EVENT, TRIGGER ON *.* TO 'new_master_user'@'%' WITH GRANT OPTION; 
   
   FLUSH PRIVILEGES;
   ```

4. A jogok ellenőrzése 
   ```sql
   USE sys;
   
   SHOW GRANTS FOR 'new_master_user'@'%';
   ```

## <a name="how-to-create-database-users-in-azure-database-for-mysql"></a>Adatbázis-felhasználók létrehozása az Azure Database MySQL-hez

1. Kérje le a kapcsolati információkat és a rendszergazdai felhasználó nevét.
   Az adatbázis-kiszolgálóhoz való csatlakozáshoz szüksége van a teljes kiszolgálónévre és a rendszergazdai bejelentkezési hitelesítő adatokra. Megtalálhatja a kiszolgáló nevét és bejelentkezési adatait a kiszolgálóról **áttekintése** lap vagy az **tulajdonságok** oldal az Azure Portalon. 

2. A rendszergazdai fiókot és jelszót használja az adatbázis-kiszolgálóhoz való kapcsolódáshoz. Használja az ügyfél előnyben részesített eszközt, például a MySQL Workbench, mysql.exe, HeidiSQL vagy mások. 
   Ha biztos benne, hogy hogyan kapcsolódhat, [való csatlakozás és adatlekérdezés a MySQL Workbench](./connect-workbench.md)

3. Szerkessze és futtassa a következő SQL-kódot. A helyőrző értékét cserélje le `db_user` a kívánt új felhasználónevet és a helyőrző értékét `testdb` a saját adatbázis nevére.

   Az sql-kódot szintaxis létrehoz egy új adatbázist testdb nevű példa célokra. Ezután létrehoz egy új felhasználót a MySQL szolgáltatásban, és az új adatbázis-séma az összes jogosultságot biztosít (testdb.\*) az adott felhasználó. 

   ```sql
   CREATE DATABASE testdb;
   
   CREATE USER 'db_user'@'%' IDENTIFIED BY 'StrongPassword!';
   
   GRANT ALL PRIVILEGES ON testdb . * TO 'db_user'@'%';
   
   FLUSH PRIVILEGES;
   ```

4. Ellenőrizze a engedélyezi az adatbázison belül.
   ```sql
   USE testdb;
   
   SHOW GRANTS FOR 'db_user'@'%';
   ```

5. Jelentkezzen be a kiszolgálóra, és adja meg a kijelölt adatbázis, az új felhasználónévvel és jelszóval. Ez a példa bemutatja a mysql parancssorból. Ezzel a paranccsal kéri a felhasználónévhez tartozó jelszót. Cserélje le a saját kiszolgáló nevét, az adatbázis neve és a felhasználó neve.

   ```azurecli-interactive
   mysql --host mydemoserver.mysql.database.azure.com --database testdb --user db_user@mydemoserver -p
   ```

## <a name="next-steps"></a>További lépések
Megnyitja a tűzfalat, hogy azok tudjanak csatlakozni az új felhasználók gépek IP-címek: [Hozzon létre és kezelhető az Azure Database for MySQL tűzfalszabályok az Azure portal használatával](howto-manage-firewall-using-portal.md) vagy [Azure CLI-vel](howto-manage-firewall-using-cli.md).

Fiókkezeléssel kapcsolatos további információkért tekintse meg a MySQL termékhez tartozó dokumentáció [felhasználóifiók-kezelés](https://dev.mysql.com/doc/refman/5.7/en/user-account-management.html), [GRANT szintaxis](https://dev.mysql.com/doc/refman/5.7/en/grant.html), és [jogosultságokkal](https://dev.mysql.com/doc/refman/5.7/en/privileges-provided.html).
