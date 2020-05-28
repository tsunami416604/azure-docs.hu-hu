---
title: A PHP használata a lekérdezéshez
description: Hogyan használható a PHP egy olyan program létrehozásához, amely Azure SQL Database-adatbázishoz kapcsolódik, és T-SQL-utasítások használatával kérdezi le.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.devlang: php
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: v-masebo
ms.date: 02/12/2019
ms.custom: sqldbrb=2 
ms.openlocfilehash: d3c064b590e5e3c49f8c42e913467596080a0887
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2020
ms.locfileid: "84054466"
---
# <a name="quickstart-use-php-to-query-a-microsoft-azure-sql-database"></a>Gyors útmutató: Microsoft Azure SQL-adatbázis lekérdezése a PHP használatával
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Ez a cikk bemutatja, hogyan használható a [php](https://php.net/manual/en/intro-whatis.php) egy Azure SQL Database-adatbázishoz való kapcsolódáshoz. Ezután a T-SQL-utasítások segítségével adatokat lehet lekérdezni.

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató elvégzéséhez a következőkre lesz szüksége:

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Azure SQL-adatbázis. Az alábbi rövid útmutatók segítségével hozhat létre és konfigurálhat egy adatbázist az Azure SQL-ben:

  || SQL Database | SQL Managed Instance | Azure virtuális gépen futó SQL Server |
  |:--- |:--- |:---|:---|
  | Létrehozás| [Portál](single-database-create-quickstart.md) | [Portál](../managed-instance/instance-create-quickstart.md) | [Portál](../virtual-machines/windows/sql-vm-create-portal-quickstart.md)
  || [parancssori felület](scripts/create-and-configure-database-cli.md) | [parancssori felület](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [PowerShell](scripts/create-and-configure-database-powershell.md) | [PowerShell](../managed-instance/scripts/create-configure-managed-instance-powershell.md) | [PowerShell](../virtual-machines/windows/sql-vm-create-powershell-quickstart.md)
  | Konfigurálás | [Kiszolgálói szintű IP-tűzfalszabály](firewall-create-server-level-portal-quickstart.md)| [Kapcsolódás virtuális gépről](../managed-instance/connect-vm-instance-configure.md)|
  |||[Kapcsolódás a webhelyről](../managed-instance/point-to-site-p2s-configure.md) | [Csatlakozás az SQL Serverhez](../virtual-machines/windows/sql-vm-create-portal-quickstart.md)
  |Adatok betöltése|Adventure Works betöltve|[Széles körű globális importőrök visszaállítása](../managed-instance/restore-sample-database-quickstart.md) | [Széles körű globális importőrök visszaállítása](../managed-instance/restore-sample-database-quickstart.md) |
  |||Adventure Works visszaállítása vagy importálása a [BACPAC](database-import.md) -fájlból a [githubról](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)| Adventure Works visszaállítása vagy importálása a [BACPAC](database-import.md) -fájlból a [githubról](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)|
  |||




  > [!IMPORTANT]
  > A cikkben található parancsfájlok az Adventure Works-adatbázis használatára íródnak. A felügyelt SQL-példányok esetében importálnia kell az Adventure Works-adatbázist egy példány-adatbázisba, vagy módosítania kell a jelen cikkben szereplő parancsfájlokat a Wide World importőr-adatbázis használatára.

- Az operációs rendszerhez telepített PHP-vel kapcsolatos szoftverek:

  - **MacOS**, telepítse a PHP-t, az ODBC-illesztőt, majd telepítse a PHP-illesztőprogramot a SQL Serverhoz. Lásd: [1., 2. és 3. lépés](/sql/connect/php/installation-tutorial-linux-mac).

  - **Linux**, telepítse a PHP-t, az ODBC-illesztőt, majd telepítse a PHP-illesztőprogramot a SQL Serverhoz. Lásd: [1., 2. és 3. lépés](/sql/connect/php/installation-tutorial-linux-mac).

  - **Windows**, telepítse a PHP-t IIS Express és a chocolatey-t, majd telepítse az ODBC-illesztőt és a Sqlcmd. Lásd az [1.2 és 1.3 lépést](https://www.microsoft.com/sql-server/developer-get-started/php/windows/).

## <a name="get-sql-server-connection-information"></a>SQL Server-kapcsolatok adatainak beolvasása

Az Azure SQL Database-adatbázishoz való kapcsolódáshoz szükséges kapcsolati adatok beolvasása. A közelgő eljárásokhoz szüksége lesz a teljes kiszolgálónévre vagy az állomásnévre, az adatbázis nevére és a bejelentkezési adatokra.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

2. Navigáljon az **SQL-adatbázisok** vagy az **SQL-felügyelt példányok** lapra.

3. Az **Áttekintés** lapon tekintse át a teljes kiszolgálónevet a **kiszolgáló neve** mellett egy Azure SQL Database vagy a teljes kiszolgálónév (vagy IP-cím) mellett egy Azure SQL felügyelt példányhoz vagy egy Azure-beli **virtuális gépen SQL Server** . A kiszolgálónév vagy az állomásnév másolásához vigye a kurzort a fölé, és válassza a **Másolás** ikont.

> [!NOTE]
> Az Azure-beli virtuális gépek SQL Server kapcsolati információit lásd: [kapcsolódás SQL Serverhoz](../virtual-machines/windows/sql-vm-create-portal-quickstart.md#connect-to-sql-server)

## <a name="add-code-to-query-database"></a>Kód hozzáadása az adatbázis lekérdezéséhez

1. Egy tetszőleges szövegszerkesztőben hozza létre a *sqltest.php* nevű új fájlt.  

1. Cserélje le a tartalmát a következő kódra. Ezután adja hozzá a kiszolgáló, az adatbázis, a felhasználó és a jelszó megfelelő értékeit.

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

1. Ellenőrizze, hogy az első 20 sor vissza lett-e jelenítve, és az alkalmazás ablakának lezárása.

## <a name="next-steps"></a>További lépések

- [Az első Azure SQL Database megtervezése](design-first-database-tutorial.md)

- [SQL Serverre készült Microsoft PHP-illesztőprogramok](https://github.com/Microsoft/msphpsql/)

- [Problémák jelentése és kérdezés](https://github.com/Microsoft/msphpsql/issues)

- [Példa újrapróbálkozási logikára: Rugalmas csatlakozás az SQL-hez PHP-vel](/sql/connect/php/step-4-connect-resiliently-to-sql-with-php)
