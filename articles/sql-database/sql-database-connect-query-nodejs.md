---
title: "Csatlakozás az Azure SQL Database-hez a Node.js használatával | Microsoft Docs"
description: "Egy Node.js-kódmintát ismertet, amellyel csatlakozhat egy Azure SQL Database-adatbázishoz, és lekérdezéseket hajthat végre."
services: sql-database
documentationcenter: 
author: LuisBosquez
manager: jhubbard
editor: 
ms.assetid: 53f70e37-5eb4-400d-972e-dd7ea0caacd4
ms.service: sql-database
ms.custom: mvc,develop apps
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: hero-article
ms.date: 05/24/2017
ms.author: lbosq
ms.translationtype: Human Translation
ms.sourcegitcommit: db18dd24a1d10a836d07c3ab1925a8e59371051f
ms.openlocfilehash: a7b1a504c6ae7850e5f986895adcf4d92fd5adc1
ms.contentlocale: hu-hu
ms.lasthandoff: 06/15/2017


---
<a id="azure-sql-database-use-nodejs-to-connect-and-query-data" class="xliff"></a>

# Azure SQL Database: Csatlakozás és adatlekérdezés a Node.js használatával

Ez a gyors üzembehelyezési útmutató ismerteti, hogyan használható a [Node.js](https://nodejs.org/en/) egy Azure SQL Database-adatbázishoz való csatlakozáshoz, majd hogyan lehet Transact-SQL-utasítások használatával adatokat lekérdezni, beszúrni, frissíteni és törölni az adatbázisban Windows, Ubuntu Linux és Mac platformokon.

<a id="prerequisites" class="xliff"></a>

## Előfeltételek

Ez a rövid útmutató az alábbi rövid útmutatók egyikében létrehozott erőforrásokat használja kiindulási pontnak:

- [DB létrehozása – portál](sql-database-get-started-portal.md)
- [DB létrehozása – CLI](sql-database-get-started-cli.md)
- [DB létrehozása – PowerShell](sql-database-get-started-powershell.md)

<a id="install-nodejs" class="xliff"></a>

## A Node.js telepítése 

A jelen szakaszban ismertetett lépések feltételezik, hogy Ön rendelkezik a Node.js-sel végzett fejlesztésekkel kapcsolatos tapasztalatokkal, az Azure SQL Database használatában pedig még járatlan. Ha még tapasztalatlan a Node.js-fejlesztésekben, lépjen az [alkalmazások SQL Serverrel történő összeállításával](https://www.microsoft.com/en-us/sql-server/developer-get-started/) foglalkozó témakörre, válassza ki a **Node.js** nyelvet, majd a használt operációs rendszert.

<a id="mac-os" class="xliff"></a>

### **Mac OS**
A **brew**, egy Mac OS X-szel és a **Node.js**-vel kompatibilis, egyszerűen használható csomagkezelő telepítéséhez adja meg a következő parancsokat.

```bash
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
brew install node
```

<a id="linux-ubuntu" class="xliff"></a>

### **Linux (Ubuntu)**
A **Node.js** és az **npm**, a Node.js csomagkezelőjének telepítéséhez használja a következő parancsokat.

```bash
sudo apt-get install -y nodejs npm
```

<a id="windows" class="xliff"></a>

### **Windows**
Látogasson el a [Node.js letöltési oldalára](https://nodejs.org/en/download/), és válassza ki a kívánt Windows telepítési lehetőséget.


<a id="install-the-tedious-sql-server-driver-for-nodejs" class="xliff"></a>

## A Node.js-hez készült Tedious SQL Server-illesztőprogram telepítése
A Node.js ajánlott illesztőprogramja a **[tedious](https://github.com/tediousjs/tedious)**. A Tedious egy Node.js-alkalmazásokhoz kötődő, minden platformon elérhető nyílt forráskódú kezdeményezés, amelyhez a Microsoft is hozzájárul. Ebben az oktatóanyagban szüksége lesz egy üres könyvtárra a kód és az `npm`-függőségek számára, amelyeket telepíteni fogunk.

A **tedious** illesztőprogram telepítéséhez futtassa a következő parancsot a könyvtárban:

```cmd
npm install tedious
```

<a id="get-connection-information" class="xliff"></a>

## Kapcsolatadatok lekérése

Kérje le az Azure SQL-adatbázishoz való csatlakozáshoz szükséges kapcsolatadatokat. A későbbi eljárásokban szüksége lesz a teljes kiszolgálónévre, az adatbázis nevére és a bejelentkezési adatokra.

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com/).
2. Válassza az **SQL-adatbázisok** elemet a bal oldali menüben, majd kattintson az új adatbázisra az **SQL-adatbázisok** oldalon. 
3. Az adatbázis **Áttekintés** lapján tekintse meg a teljes kiszolgálónevet, amint az alábbi képen is látható. Ha a mutatót a kiszolgáló neve fölé viszi, megjelenik a **Kattintson a másoláshoz** lehetőség. 

   ![server-name](./media/sql-database-connect-query-dotnet/server-name.png) 

4. Amennyiben elfelejtette Azure SQL Database-kiszolgálója bejelentkezési adatait, lépjen az SQL Database-kiszolgáló oldalára, és itt megtudhatja a kiszolgáló rendszergazdájának nevét, valamint szükség esetén visszaállíthatja a jelszót.
    
<a id="select-data" class="xliff"></a>

## Adatok kiválasztása

A következő kód használatával lekérdezheti kategóriánként az első 20 terméket az Azure SQL Database-adatbázisból. Először importálja az illesztőprogram csatlakozási és kérelmi osztályát a Tedious illesztőprogram könyvtárából. Ezt követően hozza létre a konfigurációs objektumot, és cserélje le a **username**, **password**, **server** és **database** változókat azokra az értékekre, amelyeket akkor határozott meg, amikor az AdventureWorksLT-mintaadatokkal létrehozta az adatbázist. Hozzon létre egy `Connection` objektumot a megadott `config` objektum használatával. Ezt követően a `queryDatabase()` függvény végrehajtásához határozzon meg visszahívást a `connection` objektum `connect` eseményére.

```js
var Connection = require('tedious').Connection;
var Request = require('tedious').Request;


// Create connection to database
var config = {
  userName: 'your_username', // update me
  password: 'your_password', // update me
  server: 'your_server.database.windows.net', // update me
  options: {
      database: 'your_database' //update me
  }
}
var connection = new Connection(config);

// Attempt to connect and execute queries if connection goes through
connection.on('connect', function(err) {
    if (err) {
        console.log(err)
    }
    else{
        queryDatabase()
    }
});

function queryDatabase(){
    console.log('Reading rows from the Table...');

    // Read all rows from table
    request = new Request(
        "SELECT TOP 1 pc.Name as CategoryName, p.name as ProductName FROM [SalesLT].[ProductCategory] pc JOIN [SalesLT].[Product] p ON pc.productcategoryid = p.productcategoryid",
        function(err, rowCount, rows) {
            console.log(rowCount + ' row(s) returned');
        }
    );
    
    request.on('row', function(columns) {
        columns.forEach(function(column) {
            console.log("%s\t%s", column.metadata.colName, column.value);
        });
    });

    connection.execSql(request);
}
```

<a id="insert-data-into-the-database" class="xliff"></a>

## Adatok beszúrása az adatbázisba
A következő kód használatával beszúrhat egy új terméket a SalesLT.Product táblába az `insertIntoDatabase()` függvénnyel és az [INSERT](https://docs.microsoft.com/sql/t-sql/statements/insert-transact-sql) Transact-SQL-utasítással. Cserélje le a **username**, **password**, **server** és **database** változókat azokra az értékekre, amelyeket akkor határozott meg, amikor az AdventureWorksLT-mintaadatokkal létrehozta az adatbázist. 

```js
var Connection = require('tedious').Connection;
var Request = require('tedious').Request;


// Create connection to database
var config = {
  userName: 'your_username', // update me
  password: 'your_password', // update me
  server: 'your_server.database.windows.net', // update me
  options: {
      database: 'your_database' //update me
  }
}

var connection = new Connection(config);

// Attempt to connect and execute queries if connection goes through
connection.on('connect', function(err) {
    if (err) {
        console.log(err)
    }
    else{
        insertIntoDatabase()
    }
});

function insertIntoDatabase(){
    console.log("Inserting a brand new product into database...");
    request = new Request(
        "INSERT INTO SalesLT.Product (Name, ProductNumber, Color, StandardCost, ListPrice, SellStartDate) OUTPUT INSERTED.ProductID VALUES ('BrandNewProduct', '200989', 'Blue', 75, 80, '7/1/2016')",
        function(err, rowCount, rows) {
            console.log(rowCount + ' row(s) inserted');
        }
    );
    connection.execSql(request);
}
```

<a id="update-data-in-the-database" class="xliff"></a>

## Az adatbázisban található adatok frissítése
A következő kód használatával törölheti az előzőleg hozzáadott új terméket az `updateInDatabase()` függvénnyel és az [UPDATE](https://docs.microsoft.com/sql/t-sql/queries/update-transact-sql) Transact-SQL-utasítással. Cserélje le a **username**, **password**, **server** és **database** változókat azokra az értékekre, amelyeket akkor határozott meg, amikor az AdventureWorksLT-mintaadatokkal létrehozta az adatbázist. Ez a minta az előző példában beszúrt terméknevet használja.

```js
var Connection = require('tedious').Connection;
var Request = require('tedious').Request;


// Create connection to database
var config = {
  userName: 'your_username', // update me
  password: 'your_password', // update me
  server: 'your_server.database.windows.net', // update me
  options: {
      database: 'your_database' //update me
  }
}

var connection = new Connection(config);

// Attempt to connect and execute queries if connection goes through
connection.on('connect', function(err) {
    if (err) {
        console.log(err)
    }
    else{
        updateInDatabase()
    }
});

function updateInDatabase(){
    console.log("Updating the price of the brand new product in database...");
    request = new Request(
        "UPDATE SalesLT.Product SET ListPrice = 50 WHERE Name = 'BrandNewProduct'",
        function(err, rowCount, rows) {
            console.log(rowCount + ' row(s) updated');
        }
    );
    connection.execSql(request);
}
```

<a id="delete-data-from-the-database" class="xliff"></a>

## Adatok törlése az adatbázisból
A következő kóddal törölhet adatokat az adatbázisból. Cserélje le a **username**, **password**, **server** és **database** változókat azokra az értékekre, amelyeket akkor határozott meg, amikor az AdventureWorksLT-mintaadatokkal létrehozta az adatbázist. Ezúttal használja a **DELETE utasítást** az `deleteFromDatabase()` függvényben. Ez a minta is az előző példában beszúrt terméknevet használja.

```js
var Connection = require('tedious').Connection;
var Request = require('tedious').Request;


// Create connection to database
var config = {
  userName: 'your_username', // update me
  password: 'your_password', // update me
  server: 'your_server.database.windows.net', // update me
  options: {
      database: 'your_database' //update me
  }
}

var connection = new Connection(config);

// Attempt to connect and execute queries if connection goes through
connection.on('connect', function(err) {
    if (err) {
        console.log(err)
    }
    else{
        deleteFromDatabase()
    }
});

function deleteFromDatabase(){
    console.log("Deleting the brand new product in database...");
    request = new Request(
        "DELETE FROM SalesLT.Product WHERE Name = 'BrandNewProduct'",
        function(err, rowCount, rows) {
            console.log(rowCount + ' row(s) returned');
        }
    );
    connection.execSql(request);
}
```


<a id="next-steps" class="xliff"></a>

## Következő lépések
- [Az első SQL Database-adatbázis megtervezése](sql-database-design-first-database.md)
- [Microsoft Node.js illesztőprogram az SQL Serverhez](https://docs.microsoft.com/sql/connect/node-js/node-js-driver-for-sql-server/)
- [Kapcsolódás és lekérdezés SSMS alkalmazásával](sql-database-connect-query-ssms.md)
- [Csatlakozás és lekérdezés Visual Studio Code használatával](sql-database-connect-query-vscode.md)



