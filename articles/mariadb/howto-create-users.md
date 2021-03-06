---
title: Felhasználók létrehozása – Azure Database for MariaDB
description: Ez a cikk azt ismerteti, hogyan hozhat létre új felhasználói fiókokat Azure Database for MariaDB-kiszolgálóval való kommunikációhoz.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: how-to
ms.date: 10/1/2020
ms.openlocfilehash: 882c8365bda87e97bfbc3bee9bdd320b312b4114
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/12/2020
ms.locfileid: "94542711"
---
# <a name="create-users-in-azure-database-for-mariadb"></a>Felhasználók létrehozása az Azure Database for MariaDB-ben 
Ez a cikk azt ismerteti, hogyan hozhatók létre felhasználók a Azure Database for MariaDBban.

> [!NOTE]
> Elfogultság – ingyenes kommunikáció
>
> A Microsoft sokféle és befogadó környezetet támogat. Ez a cikk a _Slave_ kifejezésre mutató hivatkozásokat tartalmaz. Az [elfogultság nélküli kommunikációhoz használható Microsoft-stílus útmutatója](https://github.com/MicrosoftDocs/microsoft-style-guide/blob/master/styleguide/bias-free-communication.md) ezt a kizáró szót ismeri fel. A szó a jelen cikkben a konzisztencia miatt használatos, mert jelenleg a szoftverben megjelenő szó. Ha a szoftver frissítve lett a szó eltávolítására, a rendszer a cikket úgy frissíti, hogy az legyen az igazítás.
>

Amikor először hozta létre a Azure Database for MariaDB, a kiszolgáló-rendszergazdai bejelentkezési felhasználónevet és jelszót adott meg. További információt a rövid útmutatóban [talál.](quickstart-create-mariadb-server-database-using-azure-portal.md) A kiszolgáló-rendszergazdai bejelentkezési felhasználónevet megkeresheti a Azure Portal.

A kiszolgáló-rendszergazda felhasználó bizonyos jogosultságokat kap a kiszolgáló számára a felsoroltak szerint: kijelölés, Beszúrás, frissítés, törlés, létrehozás, eldobás, Újratöltés, feldolgozás, hivatkozások, INDEX, ALTER, adatbázisok megjelenítése, ideiglenes táblák létrehozása, táblák ZÁROLÁSa, végrehajtás, REPLIKÁLÁSi SLAVE, replikációs ügyfél, létrehozási nézet, a nézet megjelenítése, RUTIN létrehozása

A Azure Database for MariaDB-kiszolgáló létrehozása után az első kiszolgáló-rendszergazdai felhasználói fiókkal további felhasználókat hozhat létre, és rendszergazdai hozzáférést adhat hozzájuk. A kiszolgálói rendszergazdai fiókkal kevesebb jogosultsággal rendelkező felhasználó hozható létre, akik egyéni adatbázis-sémákkal rendelkeznek hozzáféréssel.

> [!NOTE]
> A SUPER Privilege és a DBA szerepkör nem támogatott. Tekintse át a korlátozások című cikkben szereplő [jogosultságokat](concepts-limits.md#privileges--data-manipulation-support) , hogy megtudja, mi nem támogatott a szolgáltatásban.<br><br>
> A szolgáltatás nem támogatja a jelszavakat, például a "validate_password" és a "caching_sha2_password".

## <a name="create-additional-admin-users"></a>További rendszergazda felhasználók létrehozása
1. Kérje le a kapcsolatfelvételi adatokat és a rendszergazda felhasználónevét.
   Az adatbázis-kiszolgálóhoz való csatlakozáshoz szüksége van a teljes kiszolgálónévre és a rendszergazdai bejelentkezési hitelesítő adatokra. A kiszolgáló és a bejelentkezési adatok könnyen megtalálhatók a kiszolgáló **Áttekintés** lapján vagy a Azure Portal **Tulajdonságok** lapján. 

2. Az adatbázis-kiszolgálóhoz való kapcsolódáshoz használja a rendszergazdai fiókot és a jelszót. Használhatja az előnyben részesített ügyfélprogramot, például a MySQL Workbench, a mysql.exe, a HeidiSQL vagy más eszközöket. 
   Ha nem tudja, hogyan csatlakozhat, tekintse meg a következőt: a [MySQL Workbench használata a kapcsolódáshoz és az adatlekérdezéshez](./connect-workbench.md)

3. Szerkessze és futtassa a következő SQL-kódot. Cserélje le az új felhasználónevet a helyőrző értékre `new_master_user` . Ez a szintaxis megadja a felsorolt jogosultságokat az összes adatbázis-sémán ( *.* ) a felhasználónévre (new_master_user ebben a példában). 

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

2. Az adatbázis-kiszolgálóhoz való kapcsolódáshoz használja a rendszergazdai fiókot és a jelszót. Használhatja az előnyben részesített ügyfélprogramot, például a MySQL Workbench, a mysql.exe, a HeidiSQL vagy más eszközöket. 
   Ha nem tudja, hogyan csatlakozhat, tekintse meg a következőt: a [MySQL Workbench használata a kapcsolódáshoz és az adatlekérdezéshez](./connect-workbench.md)

3. Szerkessze és futtassa a következő SQL-kódot. Cserélje le a helyőrző értékét a `db_user` kívánt új felhasználónévre, és adja meg a helyőrző értékét a `testdb` saját adatbázisának nevével.

   Ez az SQL Code-szintaxis egy új, testdb nevű adatbázist hoz létre példaként. Ezután létrehoz egy új felhasználót a Azure Database for MariaDB szolgáltatásban, és minden jogosultságot biztosít az adott felhasználóhoz tartozó új adatbázis-sémához (testdb. \* ). 

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

## <a name="azure_superuser"></a>azure_superuser

Az összes Azure Database for MySQL-kiszolgáló egy "azure_superuser" nevű felhasználóval jön létre. Ez egy, a Microsoft által létrehozott rendszerfiók, amely a kiszolgáló felügyeletét, biztonsági mentését és egyéb rendszeres karbantartását végzi. A hívási mérnökök ezt a fiókot is használhatják a kiszolgáló elérésére a tanúsítványalapú hitelesítéssel rendelkező incidensek során, és az igény szerinti (JIT) folyamatokat kell használniuk.

## <a name="next-steps"></a>Következő lépések
Nyissa meg a tűzfalat az új felhasználói gépek IP-címei számára a kapcsolódáshoz: [Azure Database for MariaDB tűzfalszabályok létrehozása és kezelése a Azure Portal használatával](howto-manage-firewall-portal.md)  

<!--or [Azure CLI](howto-manage-firewall-using-cli.md).-->
