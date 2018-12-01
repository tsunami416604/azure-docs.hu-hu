---
title: PHP használata Azure SQL-adatbázis lekérdezéséhez |} A Microsoft Docs
description: Hogyan használható a PHP egy olyan program létrehozásához, amely egy Azure SQL Database-adatbázishoz kapcsolódik, és T-SQL-utasítások használatával lekérdezni.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.devlang: php
ms.topic: quickstart
author: CarlRabeler
ms.author: carlrab
ms.reviewer: v-masebo
manager: craigg
ms.date: 11/28/2018
ms.openlocfilehash: be3ac9fab6c89c65ad9673811e108cefe2c80d00
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/01/2018
ms.locfileid: "52724247"
---
# <a name="quickstart-use-php-to-query-an-azure-sql-database"></a>Gyors útmutató: PHP használata Azure SQL Database-adatbázis lekérdezéséhez

Ez a cikk bemutatja, hogyan használható [PHP](http://php.net/manual/en/intro-whatis.php) csatlakozni egy Azure SQL Database-adatbázishoz. Ezután használhatja a T-SQL-utasítások használatával adatokat lekérdezni.

## <a name="prerequisites"></a>Előfeltételek

Ez a minta, győződjön meg arról, hogy rendelkezik a következő előfeltételek vonatkoznak:

[!INCLUDE [prerequisites-create-db](../../includes/sql-database-connect-query-prerequisites-create-db-includes.md)]

- A [kiszolgálószintű tűzfalszabály](sql-database-get-started-portal-firewall.md) használ a számítógép nyilvános IP-cím

- PHP-kapcsolódó szoftverek az operációs rendszer telepítve:

    - **MacOS**, telepítse a homebrew-t és PHP, az ODBC-illesztőt és az Sqlcmd-t, majd telepítse a PHP-illesztőprogram SQL Serverhez készült. Lásd az [1.2, 1.3 és 2.1 lépést](https://www.microsoft.com/sql-server/developer-get-started/php/mac/).

    - **Ubuntu**, telepítse a PHP és a többi szükséges csomagot, majd telepítse a PHP-illesztőprogram SQL Serverhez készült. Lásd az [1.2 és 2.1 lépést](https://www.microsoft.com/sql-server/developer-get-started/php/ubuntu/).

    - **Windows**, a PHP telepítése az IIS Express és a chocolatey-t, majd telepítse az ODBC-illesztőt és az Sqlcmd-t. Lásd az [1.2 és 1.3 lépést](https://www.microsoft.com/sql-server/developer-get-started/php/windows/).

## <a name="get-database-connection"></a>Adatbázis-kapcsolat beolvasása

[!INCLUDE [prerequisites-server-connection-info](../../includes/sql-database-connect-query-prerequisites-server-connection-info-includes.md)]

## <a name="add-code-to-query-database"></a>Adja hozzá a kódot az adatbázis lekérdezése

1. Egy tetszőleges szövegszerkesztőben hozza létre a *sqltest.php* nevű új fájlt.  

1. Cserélje le annak tartalmát az alábbira. Majd adja hozzá a megfelelő értékeket a kiszolgáló, adatbázis, a felhasználó és a jelszavát.

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

1. Ellenőrizze az első 20 sort adja vissza, és az alkalmazás ablak bezárásához.

## <a name="next-steps"></a>További lépések

- [Az első SQL Database-adatbázis megtervezése](sql-database-design-first-database.md)

- [SQL Serverre készült Microsoft PHP-illesztőprogramok](https://github.com/Microsoft/msphpsql/)

- [Problémák jelentése és kérdezés](https://github.com/Microsoft/msphpsql/issues)

- [Példa újrapróbálkozási: logikára kapcsolódni az SQL, a PHP](/sql/connect/php/step-4-connect-resiliently-to-sql-with-php)
