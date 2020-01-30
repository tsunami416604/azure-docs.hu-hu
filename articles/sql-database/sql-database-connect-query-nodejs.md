---
title: Adatbázis lekérdezése a Node. js használatával
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
ms.openlocfilehash: c0da38a41bf613237ea3b164d70e4729a7284ca7
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2020
ms.locfileid: "76768600"
---
# <a name="quickstart-use-nodejs-to-query-an-azure-sql-database"></a>Rövid útmutató: Node.js használata Azure SQL-adatbázis lekérdezéséhez

Ebben a rövid útmutatóban a Node. js használatával csatlakozhat egy Azure SQL Database-adatbázishoz, és T-SQL-utasítások használatával kérdez le adatokat.

## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Egy [Azure SQL Database-adatbázis](sql-database-single-database-get-started.md)
- [Node. js](https://nodejs.org)-hez kapcsolódó szoftverek

  # <a name="macostabmacos"></a>[macOS](#tab/macos)

  Telepítse a Homebrew-t és a Node. js-t, majd telepítse az ODBC-illesztőt és a SQLCMD-t a **1,2** -es és **1,3** -as lépések használatával a [Node. js-alkalmazások létrehozása SQL Server](https://www.microsoft.com/sql-server/developer-get-started/node/mac/)

  # <a name="ubuntutabubuntu"></a>[Ubuntu](#tab/ubuntu)

  Telepítse a Node. js-t, majd telepítse az ODBC-illesztőt és a SQLCMD-t a **1,2** -es és **1,3** -as lépések használatával a [Node. js-alkalmazások létrehozása SQL Server használatával Ubuntu rendszeren](https://www.microsoft.com/sql-server/developer-get-started/node/ubuntu/)

  # <a name="windowstabwindows"></a>[Windows](#tab/windows)

  Telepítse a csokoládét és a Node. js-t, majd telepítse az ODBC-illesztőt és a SQLCMD-t a **1,2** -es és **1,3** -as lépések használatával a [Node. js-alkalmazások létrehozása a Windows SQL Server](https://www.microsoft.com/sql-server/developer-get-started/node/windows/)

  ---

> [!IMPORTANT]
> A cikkben található parancsfájlok az **Adventure Works** -adatbázis használatára íródnak.

> [!NOTE]
> Dönthet úgy is, hogy egy Azure SQL felügyelt példányt használ.
>
> A létrehozásához és konfigurálásához használja az [Azure Portalt](sql-database-managed-instance-get-started.md), a [PowerShellt](scripts/sql-database-create-configure-managed-instance-powershell.md)vagy a [CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44)-t, majd a helyszíni vagy a [VM](sql-database-managed-instance-configure-vm.md) [-](sql-database-managed-instance-configure-p2s.md) kapcsolat beállítását.
>
> Az betöltéssel kapcsolatban lásd: [visszaállítás a BACPAC](sql-database-import.md) az [Adventure Works](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works) -fájllal, vagy lásd: [a Wide World](sql-database-managed-instance-get-started-restore.md)importing-adatbázis visszaállítása.

## <a name="get-sql-server-connection-information"></a>SQL Server-kapcsolatok adatainak beolvasása

Az Azure SQL Database-adatbázishoz való kapcsolódáshoz szükséges kapcsolati adatok beolvasása. A közelgő eljárásokhoz szüksége lesz a teljes kiszolgálónévre vagy az állomásnévre, az adatbázis nevére és a bejelentkezési adatokra.

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com/).

2. Nyissa meg az **SQL-adatbázisok** vagy az SQL- **felügyelt példányok** lapot.

3. Az **Áttekintés** lapon tekintse át a teljes kiszolgálónevet a **kiszolgáló neve** mellett egyetlen adatbázishoz vagy a felügyelt példányhoz tartozó **gazdagép** melletti teljes kiszolgálónévhez. A kiszolgálónév vagy az állomásnév másolásához vigye a kurzort a fölé, és válassza a **Másolás** ikont. 

## <a name="create-the-project"></a>A projekt létrehozása

Nyisson meg egy parancssort, és hozzon létre egy *sqltest* nevű mappát. Nyissa meg a létrehozott mappát, és futtassa a következő parancsot:

  ```bash
  npm init -y
  npm install tedious
  ```

## <a name="add-code-to-query-database"></a>Kód hozzáadása az adatbázis lekérdezéséhez

1. A kedvenc szövegszerkesztőben hozzon létre egy új, *sqltest. js*fájlt.

1. Cserélje le a tartalmát a következő kódra. Ezután adja hozzá a kiszolgáló, az adatbázis, a felhasználó és a jelszó megfelelő értékeit.

    ```js
    const { Connection, Request } = require("tedious");

    // Create connection to database
    const config = {
      authentication: {
        options: {
          userName: "username", // update me
          password: "password" // update me
        },
        type: "default"
      },
      server: "your_server.database.windows.net", // update me
      options: {
        database: "your_database", //update me
        encrypt: true
      }
    };

    const connection = new Connection(config);

    // Attempt to connect and execute queries if connection goes through
    connection.on("connect", err => {
      if (err) {
        console.error(err.message);
      } else {
        queryDatabase();
      }
    });

    function queryDatabase() {
      console.log("Reading rows from the Table...");

      // Read all rows from table
      const request = new Request(
        `SELECT TOP 20 pc.Name as CategoryName,
                       p.name as ProductName
         FROM [SalesLT].[ProductCategory] pc
         JOIN [SalesLT].[Product] p ON pc.productcategoryid = p.productcategoryid`,
        (err, rowCount) => {
          if (err) {
            console.error(err.message);
          } else {
            console.log(`${rowCount} row(s) returned`);
          }
        }
      );

      request.on("row", columns => {
        columns.forEach(column => {
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

## <a name="next-steps"></a>Következő lépések

- [Microsoft Node.js illesztőprogram az SQL Serverhez](/sql/connect/node-js/node-js-driver-for-sql-server)

- Kapcsolat és lekérdezés Windows/Linux/macOS rendszeren [.net Core](sql-database-connect-query-dotnet-core.md), [Visual Studio Code](sql-database-connect-query-vscode.md)vagy [SSMS](sql-database-connect-query-ssms.md) (csak Windows rendszeren)

- [A .NET Core használatának első lépései Windows/Linux/macOS rendszeren a parancssor használatával](/dotnet/core/tutorials/using-with-xplat-cli)

- Az első Azure SQL-adatbázis megtervezése [.net](sql-database-design-first-database-csharp.md) vagy [SSMS](sql-database-design-first-database.md) használatával
