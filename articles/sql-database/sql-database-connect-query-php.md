---
title: Lekérdezés php használatával
description: A PHP használata egy Olyan program létrehozásához, amely egy Azure SQL-adatbázishoz csatlakozik, és T-SQL utasítások használatával lekérdezi azt.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.devlang: php
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: v-masebo
ms.date: 02/12/2019
ms.openlocfilehash: ae119dd23da670f16c0239b14119519c431e6326
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "73827015"
---
# <a name="quickstart-use-php-to-query-an-azure-sql-database"></a>Gyors útmutató: PHP használata Azure SQL-adatbázis lekérdezéséhez

Ez a cikk bemutatja, hogyan lehet a [PHP](https://php.net/manual/en/intro-whatis.php) segítségével csatlakozni egy Azure SQL-adatbázishoz. Ezután a T-SQL utasítások segítségével adatok lekérdezése.

## <a name="prerequisites"></a>Előfeltételek

A minta kitöltéséhez győződjön meg arról, hogy a következő előfeltételekkel rendelkezik:

- Azure SQL-adatbázis. Az alábbi rövid útmutatók egyikével hozhat létre, majd konfigurálhat egy adatbázist az Azure SQL Database-ben:

  || Önálló adatbázis | Felügyelt példány |
  |:--- |:--- |:---|
  | Létrehozás| [Portál](sql-database-single-database-get-started.md) | [Portál](sql-database-managed-instance-get-started.md) |
  || [parancssori felület](scripts/sql-database-create-and-configure-database-cli.md) | [parancssori felület](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [Powershell](scripts/sql-database-create-and-configure-database-powershell.md) | [Powershell](scripts/sql-database-create-configure-managed-instance-powershell.md) |
  | Konfigurálás | [Kiszolgálószintű IP-tűzfal szabály](sql-database-server-level-firewall-rule.md)| [Kapcsolódás virtuális gépről](sql-database-managed-instance-configure-vm.md)|
  |||[Helyszíni kapcsolat](sql-database-managed-instance-configure-p2s.md)
  |Adatok betöltése|A kalandworks betöltve egy rövid útmutató|[Széles világbeli importőrök visszaállítása](sql-database-managed-instance-get-started-restore.md)
  |||Kalandorok visszaállítása vagy importálása [a BACPAC-fájlból](sql-database-import.md) a [GitHubról](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)|
  |||

  > [!IMPORTANT]
  > A cikkben szereplő parancsfájlok a Kalandorbolt-adatbázis használatára íródnak. Felügyelt példány esetén vagy importálnia kell a Kalandorbolt-adatbázist egy példányadatbázisba, vagy módosítania kell a cikkparancsfájljait a Wide World Importers adatbázis használatához.

- PHP-vel kapcsolatos szoftver telepítve az operációs rendszerhez:

  - **MacOS**, telepítse a PHP-t, az ODBC illesztőprogramot, majd telepítse a PHP Driver for SQL Server-t. Lásd [az 1., 2.](/sql/connect/php/installation-tutorial-linux-mac)

  - **Linux**, telepítse a PHP-t, az ODBC illesztőprogramot, majd telepítse a PHP Driver for SQL Server-t. Lásd [az 1., 2.](/sql/connect/php/installation-tutorial-linux-mac)

  - **Windows**, telepítse a PHP-t az IIS Express és a Chocolatey számára, majd telepítse az ODBC illesztőprogramot és az SQLCMD-t. Lásd az [1.2 és 1.3 lépést](https://www.microsoft.com/sql-server/developer-get-started/php/windows/).

## <a name="get-sql-server-connection-information"></a>SQL-kiszolgálókapcsolati adatok beszerezése

Az Azure SQL-adatbázishoz való csatlakozáshoz szükséges kapcsolati információk beszerezése. A közelgő eljárásokhoz szüksége lesz a teljesen minősített kiszolgáló- vagy állomásnévre, az adatbázis nevére és bejelentkezési adataira.

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com/)

2. Keresse meg az **SQL-adatbázisok** vagy az **SQL felügyelt példányok** lapot.

3. Az **Áttekintés** lapon tekintse át a **kiszolgáló neve** melletti teljesen minősített kiszolgálónevet egyetlen adatbázishoz, vagy a teljesen minősített kiszolgálónevet a felügyelt példány **gazdagépe** mellett. A kiszolgáló vagy az állomásnév másolásához mutasson rá, és válassza a **Másolás ikont.**

## <a name="add-code-to-query-database"></a>Kód hozzáadása a lekérdezési adatbázishoz

1. Egy tetszőleges szövegszerkesztőben hozza létre a *sqltest.php* nevű új fájlt.  

1. Cserélje le a tartalmát a következő kódra. Ezután adja hozzá a kiszolgálónak, az adatbázisnak, a felhasználónak és a jelszónak megfelelő értékeket.

   ```PHP
   <?php
       $serverName = "your_server.database.windows.net"; // update me
       $connectionOptions = array(
           "Database" => "your_database", // update me
           "Uid" => "your_username", // update me
           "PWD" => "your_password" // update me
       );
       //Establishes the connection
       $conn = sqlsrv_connect($serverName, $connectionOptions);
       $tsql= "SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
            FROM [SalesLT].[ProductCategory] pc
            JOIN [SalesLT].[Product] p
            ON pc.productcategoryid = p.productcategoryid";
       $getResults= sqlsrv_query($conn, $tsql);
       echo ("Reading data from table" . PHP_EOL);
       if ($getResults == FALSE)
           echo (sqlsrv_errors());
       while ($row = sqlsrv_fetch_array($getResults, SQLSRV_FETCH_ASSOC)) {
        echo ($row['CategoryName'] . " " . $row['ProductName'] . PHP_EOL);
       }
       sqlsrv_free_stmt($getResults);
   ?>
   ```

## <a name="run-the-code"></a>A kód futtatása

1. A parancssorban futtassa az alkalmazást.

   ```bash
   php sqltest.php
   ```

1. Ellenőrizze, hogy a rendszer visszaadja-e a 20 legfontosabb sort, és zárja be az alkalmazásablakot.

## <a name="next-steps"></a>További lépések

- [Az első Azure SQL-adatbázis megtervezése](sql-database-design-first-database.md)

- [SQL Serverre készült Microsoft PHP-illesztőprogramok](https://github.com/Microsoft/msphpsql/)

- [Problémák jelentése és kérdezés](https://github.com/Microsoft/msphpsql/issues)

- [Példa újrapróbálkozási logikára: Rugalmas csatlakozás az SQL-hez PHP-vel](/sql/connect/php/step-4-connect-resiliently-to-sql-with-php)
