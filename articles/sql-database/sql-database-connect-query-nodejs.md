---
title: Node.js használata Azure SQL Database-adatbázis lekérdezéséhez | Microsoft Docs
description: Hogyan használható a Node.js egy olyan program létrehozásához, amely egy Azure SQL Database-adatbázishoz kapcsolódik, és T-SQL-utasítások használatával lekérdezni.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.devlang: nodejs
ms.topic: quickstart
author: CarlRabeler
ms.author: carlrab
ms.reviewer: v-masebo
manager: craigg
ms.date: 11/26/2018
ms.openlocfilehash: 3a6060c59c2b338a2fad3327fe89dcf3df955ef5
ms.sourcegitcommit: 345b96d564256bcd3115910e93220c4e4cf827b3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/28/2018
ms.locfileid: "52496698"
---
# <a name="quickstart-use-nodejs-to-query-an-azure-sql-database"></a>Rövid útmutató: Node.js használata Azure SQL Database-adatbázis lekérdezéséhez

Ez a cikk bemutatja, hogyan használható [Node.js](https://nodejs.org) csatlakozni egy Azure SQL Database-adatbázishoz. Ezután használhatja a T-SQL-utasítások használatával adatokat lekérdezni.

## <a name="prerequisites"></a>Előfeltételek

Ez a minta, győződjön meg arról, hogy rendelkezik a következő előfeltételek vonatkoznak:

[!INCLUDE [prerequisites-create-db](../../includes/sql-database-connect-query-prerequisites-create-db-includes.md)]

- A [kiszolgálószintű tűzfalszabály](sql-database-get-started-portal-firewall.md) használ a számítógép nyilvános IP-cím

- NODE.js-kapcsolódó szoftverek az operációs rendszer:

  - **MacOS**, telepítse a homebrew-t és a Node.js, majd telepítse az ODBC-illesztőt és az Sqlcmd-t. Lásd az [1.2 és 1.3 lépést](https://www.microsoft.com/sql-server/developer-get-started/node/mac/).
  
  - **Ubuntu**, a Node.js telepítéséhez, majd telepítse az ODBC-illesztőt és az Sqlcmd-t. Lásd az [1.2 és 1.3 lépést](https://www.microsoft.com/sql-server/developer-get-started/node/ubuntu/).
  
  - **Windows**, telepíti a chocolatey-t és a Node.js, majd telepítse az ODBC-illesztőt és az Sqlcmd-t. Lásd az [1.2 és 1.3 lépést](https://www.microsoft.com/sql-server/developer-get-started/node/windows/).

## <a name="get-database-connection"></a>Adatbázis-kapcsolat beolvasása

[!INCLUDE [prerequisites-server-connection-info](../../includes/sql-database-connect-query-prerequisites-server-connection-info-includes.md)]

> [!IMPORTANT]
> Rendelkeznie kell egy tűzfalszabállyal azon számítógép nyilvános IP-címéhez, amelyen ezt az oktatóanyagot elvégzi. Ha egy másik számítógépen, vagy másik nyilvános IP-címmel rendelkezik, hozzon létre egy [kiszolgálószintű tűzfalszabályt az Azure portal használatával](sql-database-get-started-portal-firewall.md).

## <a name="create-the-project"></a>A projekt létrehozása

Nyisson meg egy parancssort, és hozzon létre egy *sqltest* nevű mappát. Keresse meg a létrehozott mappát, és futtassa a következő parancsot:

  ```bash
  npm init -y
  npm install tedious
  npm install async
  ```

## <a name="add-code-to-query-database"></a>Adja hozzá a kódot az adatbázis lekérdezése

1. Tetszőleges szövegszerkesztőben hozzon létre egy új fájlt *sqltest.js*.

1. Cserélje le annak tartalmát az alábbira. Majd adja hozzá a megfelelő értékeket a kiszolgáló, adatbázis, a felhasználó és a jelszavát.

    ```js
    var Connection = require('tedious').Connection;
    var Request = require('tedious').Request;

    // Create connection to database
    var config =
    {
        userName: 'your_username', // update me
        password: 'your_password', // update me
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
        request = new Request(
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
> A példakód az **AdventureWorksLT** mintaadatbázist az Azure SQL.

## <a name="run-the-code"></a>A kód futtatása

1. A parancssorban futtassa a programot.

    ```bash
    node sqltest.js
    ```

1. Ellenőrizze az első 20 sort adja vissza, és zárja be az alkalmazásablakot.

## <a name="next-steps"></a>További lépések

- [Microsoft Node.js illesztőprogram az SQL Serverhez](/sql/connect/node-js/node-js-driver-for-sql-server)

- Kapcsolódás és lekérdezés Windows/Linux/MacOS rendszeren a [.NET core](sql-database-connect-query-dotnet-core.md), [Visual Studio Code](sql-database-connect-query-vscode.md), vagy [SSMS](sql-database-connect-query-ssms.md) (csak Windows)

- [Windows/Linux/MacOS rendszeren a parancssorral .NET Core használatának első lépései](/dotnet/core/tutorials/using-with-xplat-cli)

- Az első Azure SQL database-adatbázishoz megtervezése [.NET](sql-database-design-first-database-csharp.md) vagy [ssms használatával](sql-database-design-first-database.md)
