---
title: "Csatlakozás az Azure SQL Database-hez a PHP-vel | Microsoft Docs"
description: "Egy PHP-kódmintát ismertet, amellyel csatlakozhat egy Azure SQL Database-adatbázishoz, és lekérdezéseket hajthat végre."
services: sql-database
documentationcenter: 
author: meet-bhagdev
manager: jhubbard
editor: 
ms.assetid: 4e71db4a-a22f-4f1c-83e5-4a34a036ecf3
ms.service: sql-database
ms.custom: mvc,develop apps
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: php
ms.topic: hero-article
ms.date: 05/24/2017
ms.author: meetb
ms.translationtype: Human Translation
ms.sourcegitcommit: db18dd24a1d10a836d07c3ab1925a8e59371051f
ms.openlocfilehash: 5b90c9b49448c6bc225f9d6ba7227782b81fc5ed
ms.contentlocale: hu-hu
ms.lasthandoff: 06/15/2017


---
<a id="azure-sql-database-use-php-to-connect-and-query-data" class="xliff"></a>

# Azure SQL Database: Csatlakozás és adatlekérdezés a PHP használatával

Ez a gyors üzembehelyezési útmutató ismerteti, hogyan használható a [PHP](http://php.net/manual/en/intro-whatis.php) egy Azure SQL Database-adatbázishoz való csatlakozáshoz, majd hogyan lehet Transact-SQL-utasítások használatával adatokat lekérdezni, beszúrni, frissíteni és törölni az adatbázisban Mac OS, Ubuntu Linux és Windows platformokon.

<a id="prerequisites" class="xliff"></a>

## Előfeltételek

Ez a rövid útmutató az alábbi rövid útmutatók egyikében létrehozott erőforrásokat használja kiindulási pontnak:

- [DB létrehozása – portál](sql-database-get-started-portal.md)
- [DB létrehozása – CLI](sql-database-get-started-cli.md)
- [DB létrehozása – PowerShell](sql-database-get-started-powershell.md)

<a id="install-php-and-database-communications-software" class="xliff"></a>

## A PHP és az adatbázis kommunikációs szoftverének telepítése

A jelen szakaszban ismertetett lépések feltételezik, hogy Ön rendelkezik a PHP-vel végzett fejlesztésekkel kapcsolatos tapasztalatokkal, az Azure SQL Database használatában pedig még járatlan. Ha még tapasztalatlan a PHP-fejlesztésekben, lépjen az [alkalmazások SQL Serverrel történő összeállításával](https://www.microsoft.com/en-us/sql-server/developer-get-started/) foglalkozó témakörre, válassza ki a **PHP** nyelvet, majd a használt operációs rendszert.

<a id="mac-os" class="xliff"></a>

### **Mac OS**
Nyissa meg a terminált, és adja meg az alábbi parancsokat a **brew**, a **Mac rendszerre készült Microsoft ODBC-illesztő** és az **SQL Serverre készült Microsoft PHP-illesztőprogramok** telepítéséhez. 

```bash
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
brew tap microsoft/msodbcsql https://github.com/Microsoft/homebrew-msodbcsql-preview
brew update
brew install msodbcsql 
#for silent install ACCEPT_EULA=y brew install msodbcsql 
pecl install sqlsrv-4.1.7preview
pecl install pdo_sqlsrv-4.1.7preview
```

<a id="linux-ubuntu" class="xliff"></a>

### **Linux (Ubuntu)**
A **Linux rendszerre készült Microsoft ODBC-illesztő** és az **SQL Serverre készült Microsoft PHP-illesztőprogramok** telepítéséhez használja a következő parancsokat.

```bash
sudo su
curl https://packages.microsoft.com/keys/microsoft.asc | apt-key add -
curl https://packages.microsoft.com/config/ubuntu/16.04/prod.list > /etc/apt/sources.list.d/mssql.list
exit
sudo apt-get update
sudo apt-get install msodbcsql mssql-tools unixodbc-dev gcc g++ php-dev
sudo pecl install sqlsrv pdo_sqlsrv
sudo echo "extension= pdo_sqlsrv.so" >> `php --ini | grep "Loaded Configuration" | sed -e "s|.*:\s*||"`
sudo echo "extension= sqlsrv.so" >> `php --ini | grep "Loaded Configuration" | sed -e "s|.*:\s*||"`
```

<a id="windows" class="xliff"></a>

### **Windows**
- Telepítse a PHP 7.1.1-es (x64) verzióját [a WebPlatform Installerről](https://www.microsoft.com/web/downloads/platform.aspx?lang=) 
- Telepítse a [Microsoft ODBC-illesztő 13.1-es verzióját](https://www.microsoft.com/download/details.aspx?id=53339). 
- Töltse le az [QL Serverre készült Microsoft PHP-illesztőprogram](https://pecl.php.net/package/sqlsrv/4.1.6.1/windows) illesztőprogramhoz tartozó non-thread safe (NTS) dll-eket, és helyezze a bináris fájlokat a PHP\v7.x\ext mappába.
- Ezután módosítsa a php.ini (C:\Program Files\PHP\v7.1\php.ini) fájlt úgy, hogy hozzáadja a dll-re mutató hivatkozást. Példa:
      
      extension=php_sqlsrv.dll
      extension=php_pdo_sqlsrv.dll

Ezen a ponton regisztrálni kell a dll-eket a PHP-nál.

<a id="get-connection-information" class="xliff"></a>

## Kapcsolatadatok lekérése

Kérje le az Azure SQL-adatbázishoz való csatlakozáshoz szükséges kapcsolatadatokat. A későbbi eljárásokban szüksége lesz a teljes kiszolgálónévre, az adatbázis nevére és a bejelentkezési adatokra.

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com/).
2. Válassza az **SQL-adatbázisok** elemet a bal oldali menüben, majd kattintson az új adatbázisra az **SQL-adatbázisok** oldalon. 
3. Az adatbázis **Áttekintés** lapján tekintse meg a teljes kiszolgálónevet, amint az alábbi képen is látható. Ha a mutatót a kiszolgáló neve fölé viszi, megjelenik a **Kattintson a másoláshoz** lehetőség.  

   ![server-name](./media/sql-database-connect-query-dotnet/server-name.png) 

4. Amennyiben elfelejtette a kiszolgálója bejelentkezési adatait, lépjen az SQL Database-kiszolgáló oldalára, és itt megtudhatja a kiszolgáló rendszergazdájának nevét, valamint szükség esetén új jelszót kérhet.     
    
<a id="select-data" class="xliff"></a>

## Adatok kiválasztása
A következő kód használatával lekérdezheti kategóriánként az első 20 terméket az [sqlsrv_query()](https://docs.microsoft.com/sql/connect/php/sqlsrv-query) függvény egy [SELECT](https://docs.microsoft.com/sql/t-sql/queries/select-transact-sql) Transact-SQL-utasítással való használatával. Az sqlsrv_query függvénnyel lekérheti egy lekérdezés eredményhalmazát egy SQL Database-adatbázisból. Ez a függvény elfogad egy lekérdezést és visszaad egy eredményhalmazt, amely megismételhető az [sqlsrv_fetch_array()](http://php.net/manual/en/function.sqlsrv-fetch-array.php) segítségével. Cserélje le a kiszolgáló, az adatbázis, a felhasználónév és a jelszó paramétereit azokra az értékekre, amelyeket akkor határozott meg, amikor az AdventureWorksL- mintaadatokkal létrehozta az adatbázist. 

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


<a id="insert-data" class="xliff"></a>

## Adat beszúrása
A következő kód használatával beszúrhat egy új terméket a SalesLT.Product táblába az [sqlsrv_query()](https://docs.microsoft.com/sql/connect/php/sqlsrv-query) függvénnyel és az [INSERT](https://docs.microsoft.com/sql/t-sql/statements/insert-transact-sql) Transact-SQL-utasítással. Cserélje le a kiszolgáló, az adatbázis, a felhasználónév és a jelszó paramétereit azokra az értékekre, amelyeket akkor határozott meg, amikor az AdventureWorksL- mintaadatokkal létrehozta az adatbázist. 

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
$tsql= "INSERT INTO SalesLT.Product (Name, ProductNumber, Color, StandardCost, ListPrice, SellStartDate) VALUES (?,?,?,?,?,?);";
$params = array("BrandNewProduct", "200989", "Blue", 75, 80, "7/1/2016");
$getResults= sqlsrv_query($conn, $tsql, $params);
if ($getResults == FALSE)
    echo print_r(sqlsrv_errors(), true);
else{
    $rowsAffected = sqlsrv_rows_affected($getResults);
    echo ($rowsAffected. " row(s) inserted" . PHP_EOL);
    sqlsrv_free_stmt($getResults);
}
?>
```

<a id="update-data" class="xliff"></a>

## Adatok frissítése
A következő kód használatával frissítheti az adatokat az Azure SQL Database-adatbázisban az [sqlsrv_query()](https://docs.microsoft.com/sql/connect/php/sqlsrv-query) függvénnyel és az [UPDATE](https://docs.microsoft.com/sql/t-sql/queries/update-transact-sql) Transact-SQL-utasítással. Cserélje le a kiszolgáló, az adatbázis, a felhasználónév és a jelszó paramétereit azokra az értékekre, amelyeket akkor határozott meg, amikor az AdventureWorksL- mintaadatokkal létrehozta az adatbázist.

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
$tsql= "UPDATE SalesLT.Product SET ListPrice =? WHERE Name = ?";
$params = array(50,"BrandNewProduct");
$getResults= sqlsrv_query($conn, $tsql, $params);
if ($getResults == FALSE)
    echo print_r(sqlsrv_errors(), true);
else{
    $rowsAffected = sqlsrv_rows_affected($getResults);
    echo ($rowsAffected. " row(s) updated" . PHP_EOL);
    sqlsrv_free_stmt($getResults);
}
?>
```

<a id="delete-data" class="xliff"></a>

## Adat törlése
A következő kód használatával törölheti az előzőleg hozzáadott új terméket az [sqlsrv_query()](https://docs.microsoft.com/sql/connect/php/sqlsrv-query) függvénnyel és a [DELETE](https://docs.microsoft.com/sql/t-sql/statements/delete-transact-sql) Transact-SQL-utasítással. Cserélje le a kiszolgáló, az adatbázis, a felhasználónév és a jelszó paramétereit azokra az értékekre, amelyeket akkor határozott meg, amikor az AdventureWorksL- mintaadatokkal létrehozta az adatbázist.

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
$tsql= "DELETE FROM SalesLT.Product WHERE Name = ?";
$params = array("BrandNewProduct");
$getResults= sqlsrv_query($conn, $tsql, $params);
if ($getResults == FALSE)
    echo print_r(sqlsrv_errors(), true);
else{
    $rowsAffected = sqlsrv_rows_affected($getResults);
    echo ($rowsAffected. " row(s) deleted" . PHP_EOL);
    sqlsrv_free_stmt($getResults);
}
```

<a id="next-steps" class="xliff"></a>

## Következő lépések
- [Az első SQL Database-adatbázis megtervezése](sql-database-design-first-database.md)
- [SQL Serverre készült Microsoft PHP-illesztőprogramok](https://github.com/Microsoft/msphpsql/)
- [Hibák jelentése/kérdések felvetése](https://github.com/Microsoft/msphpsql/issues)


