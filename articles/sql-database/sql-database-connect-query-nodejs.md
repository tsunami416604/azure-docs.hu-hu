---
title: 'Gyors útmutató: A Node. js használata a Azure SQL Database lekérdezéséhez'
description: A Node. js használata olyan program létrehozásához, amely egy Azure SQL Database-adatbázishoz csatlakozik, és T-SQL-utasítások használatával kérdezi le.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.devlang: nodejs
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: v-masebo
ms.date: 03/25/2019
ms.custom: seo-javascript-september2019, seo-javascript-october2019
ms.openlocfilehash: 4d9c3954aea5fe64439190a2a0886fd8300c4cf9
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2019
ms.locfileid: "72178045"
---
# <a name="quickstart-use-nodejs-to-query-an-azure-sql-database"></a>Gyors útmutató: Node.js használata Azure SQL-adatbázis lekérdezéséhez

Ez a rövid útmutató bemutatja, hogyan használható a [Node. js](https://nodejs.org) egy Azure SQL Database-adatbázishoz való kapcsolódáshoz. Ezután a T-SQL-utasítások segítségével adatokat lehet lekérdezni.

## <a name="prerequisites"></a>Előfeltételek

A minta elvégzéséhez győződjön meg arról, hogy rendelkezik a következő előfeltételekkel:

- Azure SQL-adatbázis. Az alábbi rövid útmutatók segítségével hozhat létre és konfigurálhat egy adatbázist Azure SQL Databaseban:

  || Önálló adatbázis | Felügyelt példány |
  |:--- |:--- |:---|
  | Hozzon létre| [Portál](sql-database-single-database-get-started.md) | [Portál](sql-database-managed-instance-get-started.md) |
  || [Parancssori felület](scripts/sql-database-create-and-configure-database-cli.md) | [Parancssori felület](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [PowerShell](scripts/sql-database-create-and-configure-database-powershell.md) | [PowerShell](scripts/sql-database-create-configure-managed-instance-powershell.md) |
  | Konfigurálás | [Kiszolgálói szintű IP-tűzfalszabály](sql-database-server-level-firewall-rule.md)| [Kapcsolódás virtuális gépről](sql-database-managed-instance-configure-vm.md)|
  |||[Kapcsolódás a webhelyről](sql-database-managed-instance-configure-p2s.md)
  |Adatok betöltése|Adventure Works betöltve|[Széles körű globális importőrök visszaállítása](sql-database-managed-instance-get-started-restore.md)
  |||Adventure Works visszaállítása vagy importálása a [BACPAC](sql-database-import.md) -fájlból a [githubról](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)|
  |||

  > [!IMPORTANT]
  > A cikkben található parancsfájlok az Adventure Works-adatbázis használatára íródnak. Felügyelt példány esetén importálnia kell az Adventure Works-adatbázist egy példány-adatbázisba, vagy módosítania kell a jelen cikkben szereplő parancsfájlokat a Wide World Importálós adatbázis használatára.


- Node. js-hez kapcsolódó szoftver az operációs rendszerhez:

  - **MacOS**, install Homebrew és Node. js, majd telepítse az ODBC-illesztőt és a Sqlcmd. Lásd az [1.2 és 1.3 lépést](https://www.microsoft.com/sql-server/developer-get-started/node/mac/).
  
  - **Ubuntu**, telepítse a Node. js-t, majd telepítse az ODBC-illesztőt és a Sqlcmd. Lásd az [1.2 és 1.3 lépést](https://www.microsoft.com/sql-server/developer-get-started/node/ubuntu/).
  
  - **Windows**, telepítse a chocolatey és Node. js fájlt, majd telepítse az ODBC-illesztőt és a Sqlcmd. Lásd az [1.2 és 1.3 lépést](https://www.microsoft.com/sql-server/developer-get-started/node/windows/).

## <a name="get-sql-server-connection-information"></a>SQL Server-kapcsolatok adatainak beolvasása

Az Azure SQL Database-adatbázishoz való kapcsolódáshoz szükséges kapcsolati adatok beolvasása. A közelgő eljárásokhoz szüksége lesz a teljes kiszolgálónévre vagy az állomásnévre, az adatbázis nevére és a bejelentkezési adatokra.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

2. Nyissa meg az **SQL-adatbázisok** vagy az SQL- **felügyelt példányok** lapot.

3. Az **Áttekintés** lapon tekintse át a teljes kiszolgálónevet a **kiszolgáló neve** mellett egyetlen adatbázishoz vagy a felügyelt példányhoz tartozó **gazdagép** melletti teljes kiszolgálónévhez. A kiszolgálónév vagy az állomásnév másolásához vigye a kurzort a fölé, és válassza a **Másolás** ikont. 

## <a name="create-the-project"></a>A projekt létrehozása

Nyisson meg egy parancssort, és hozzon létre egy *sqltest* nevű mappát. Nyissa meg a létrehozott mappát, és futtassa a következő parancsot:

  ```bash
  npm init -y
  npm install tedious@5.0.3
  npm install async@2.6.2
  ```

## <a name="add-code-to-query-database"></a>Kód hozzáadása az adatbázis lekérdezéséhez

1. A kedvenc szövegszerkesztőben hozzon létre egy új, *sqltest. js*fájlt.

1. Cserélje le a tartalmát a következő kódra. Ezután adja hozzá a kiszolgáló, az adatbázis, a felhasználó és a jelszó megfelelő értékeit.

    ```js
    var Connection = require('tedious').Connection;
    var Request = require('tedious').Request;

    // Create connection to database
    var config =
    {
        authentication: {
            options: {
                userName: 'userName', // update me
                password: 'password' // update me
            },
            type: 'default'
        },
        server: 'your_server.database.windows.net', // update me
        options:
        {
            database: 'your_database', //update me
            encrypt: true
        }
    }
    var connection = new Connection(config);

    // Attempt to connect and execute queries if connection goes through
    connection.on('connect', function(err)
        {
            if (err)
            {
                console.log(err)
            }
            else
            {
                queryDatabase()
            }
        }
    );

    function queryDatabase()
    {
        console.log('Reading rows from the Table...');

        // Read all rows from table
        var request = new Request(
            "SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName FROM [SalesLT].[ProductCategory] pc "
                + "JOIN [SalesLT].[Product] p ON pc.productcategoryid = p.productcategoryid",
            function(err, rowCount, rows)
            {
                console.log(rowCount + ' row(s) returned');
                process.exit();
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

> [!NOTE]
> A Code példa az Azure SQL-hoz készült **AdventureWorksLT** mintaadatbázis használatával működik.

## <a name="run-the-code"></a>A kód futtatása

1. A parancssorban futtassa a programot.

    ```bash
    node sqltest.js
    ```

1. Ellenőrizze, hogy az első 20 sor vissza lett-e jelenítve, és az alkalmazás ablakának lezárása.

## <a name="next-steps"></a>További lépések

- [Microsoft Node.js illesztőprogram az SQL Serverhez](/sql/connect/node-js/node-js-driver-for-sql-server)

- Kapcsolat és lekérdezés Windows/Linux/macOS rendszeren [.net Core](sql-database-connect-query-dotnet-core.md), [Visual Studio Code](sql-database-connect-query-vscode.md)vagy [SSMS](sql-database-connect-query-ssms.md) (csak Windows rendszeren)

- [A .NET Core használatának első lépései Windows/Linux/macOS rendszeren a parancssor használatával](/dotnet/core/tutorials/using-with-xplat-cli)

- Az első Azure SQL-adatbázis megtervezése [.net](sql-database-design-first-database-csharp.md) vagy [SSMS](sql-database-design-first-database.md) használatával
