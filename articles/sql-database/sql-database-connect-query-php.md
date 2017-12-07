---
title: "PHP használata Azure SQL Database-adatbázis lekérdezéséhez | Microsoft Docs"
description: "Ez a témakör bemutatja, hogyan használhatja a PHP-t egy Azure SQL Database-adatbázishoz csatlakozó program létrehozásához, és hogyan hajthat végre lekérdezést Transact-SQL-utasításokkal."
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 4e71db4a-a 22f-4f1c-83e5-4a34a036ecf3
ms.service: sql-database
ms.custom: mvc,develop apps
ms.workload: On Demand
ms.tgt_pltfrm: na
ms.devlang: php
ms.topic: quickstart
ms.date: 11/29/2017
ms.author: carlrab
ms.openlocfilehash: b45acf8a7abdee070c6db2c5d7f4c108a073b1bb
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/30/2017
---
# <a name="use-php-to-query-an-azure-sql-database"></a>PHP használata Azure SQL Database-adatbázis lekérdezéséhez

Ez a rövid útmutató azt ismerteti, hogyan használható a [PHP](http://php.net/manual/en/intro-whatis.php) egy olyan program létrehozásához, amely egy Azure SQL Database-adatbázishoz csatlakozik, és hogyan lehet Transact-SQL-utasítások használatával adatokat lekérdezni.

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató elvégzéséhez győződjön meg arról, hogy rendelkezik az alábbiakkal:

[!INCLUDE [prerequisites-create-db](../../includes/sql-database-connect-query-prerequisites-create-db-includes.md)]

- A rövid útmutatóhoz használt számítógép nyilvános IP-címére vonatkozó [kiszolgálószintű tűzfalszabály](sql-database-get-started-portal.md#create-a-server-level-firewall-rule).

- Telepítette a PHP-t és az operációs rendszerének megfelelő kapcsolódó szoftvereket:

    - **MacOS**: Telepítse a Homebrew-t és a PHP-t, telepítse az ODBC-illesztőt és az SQLCMD-t, majd telepítse az SQL Serverhez készült PHP-illesztőt. Lásd az [1.2, 1.3 és 2.1 lépést](https://www.microsoft.com/en-us/sql-server/developer-get-started/php/mac/).
    - **Ubuntu**: Telepítse a PHP-t és a többi szükséges csomagot, majd telepítse az SQL Serverhez készült PHP-illesztőt. Lásd az [1.2 és 2.1 lépést](https://www.microsoft.com/sql-server/developer-get-started/php/ubuntu/).
    - **Windows**: Telepítse az IIS Expresshez készült PHP legújabb verzióját, az SQL Server Microsoft-illesztőinek legújabb verzióját az IIS Expressben, a Chocolatey-t, az ODBC-illesztőt és az SQLCMD-t. Lásd az [1.2 és 1.3 lépést](https://www.microsoft.com/sql-server/developer-get-started/php/windows/).    

## <a name="sql-server-connection-information"></a>Az SQL-kiszolgáló kapcsolatadatai

[!INCLUDE [prerequisites-server-connection-info](../../includes/sql-database-connect-query-prerequisites-server-connection-info-includes.md)]
    
## <a name="insert-code-to-query-sql-database"></a>Kód beszúrása SQL-adatbázis lekérdezéséhez

1. Egy tetszőleges szövegszerkesztőben hozza létre a **sqltest.php** nevű új fájlt.  

2. Cserélje le a tartalmat a következő kódra, és adja meg a kiszolgáló és az adatbázis megfelelő adatait, valamint a felhasználót és a jelszót.

   ```PHP
   <?php
   $serverName = "your_server.database.windows.net";
   $connectionOptions = array(
       "Database" => "your_database",
       "Uid" => "your_username",
       "PWD" => "your_password"
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

1. Futtassa az alábbi parancsokat a parancssorban:

   ```php
   php sqltest.php
   ```

2. Győződjön meg arról, hogy a parancssori felület visszaadta az első 20 sort, majd zárja be az alkalmazásablakot.

## <a name="next-steps"></a>Következő lépések
- [Az első SQL Database-adatbázis megtervezése](sql-database-design-first-database.md)
- [SQL Serverre készült Microsoft PHP-illesztőprogramok](https://github.com/Microsoft/msphpsql/)
- [Problémák jelentése és kérdezés](https://github.com/Microsoft/msphpsql/issues)
- [Példa újrapróbálkozási logikára: Rugalmas csatlakozás az SQL-hez PHP-vel][step-4-connect-resiliently-to-sql-with-php-p42h]


<!-- Link references. -->

[step-4-connect-resiliently-to-sql-with-php-p42h]: https://docs.microsoft.com/sql/connect/php/step-4-connect-resiliently-to-sql-with-php

