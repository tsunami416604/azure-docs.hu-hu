---
title: Adatbázis lekérdezése a Node.js fájlhasználatával
description: Node.js használatával hozzon létre egy olyan programot, amely csatlakozik egy Azure SQL-adatbázishoz, és lekérdezi azt t-SQL utasítások használatával.
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
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "76768600"
---
# <a name="quickstart-use-nodejs-to-query-an-azure-sql-database"></a>Rövid útmutató: Node.js használata Azure SQL-adatbázis lekérdezéséhez

Ebben a rövid útmutatóban a Node.js használatával csatlakozhat egy Azure SQL-adatbázishoz, és t-SQL-utasítások használatával adatoklekérdezéséhez.

## <a name="prerequisites"></a>Előfeltételek

- Egy aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Azure SQL-adatbázis](sql-database-single-database-get-started.md)
- [Node.js](https://nodejs.org)-kapcsolódó szoftver

  # <a name="macos"></a>[Macos](#tab/macos)

  Telepítse a Homebrew és a Node.js, majd az ODBC-illesztőprogramot és az SQLCMD-t az **1.2-** és **1.3-as** lépésekkel [a Node.js alkalmazások létrehozása macOS rendszeren az SQL Server használatával](https://www.microsoft.com/sql-server/developer-get-started/node/mac/)című témakörben.

  # <a name="ubuntu"></a>[Ubuntu](#tab/ubuntu)

  Telepítse a Node.js fájlrendszert, majd telepítse az ODBC illesztőprogramot és az SQLCMD-t az **1.2** és **1.3** lépésekkel a [Node.js alkalmazások létrehozása az SQL Server használatával ubuntuban](https://www.microsoft.com/sql-server/developer-get-started/node/ubuntu/).

  # <a name="windows"></a>[Windows](#tab/windows)

  Telepítse a Chocolatey és a Node.js fájlrendszert, majd telepítse az ODBC illesztőprogramot és az SQLCMD-t az **1.2** és **1.3** lépésekkel a [Node.js alkalmazások létrehozása windowsos SQL Server használatával](https://www.microsoft.com/sql-server/developer-get-started/node/windows/)című témakörben.

  ---

> [!IMPORTANT]
> A cikkben szereplő parancsfájlok a **Kalandorbolt-adatbázis** használatára íródnak.

> [!NOTE]
> Igény szerint választhat egy Azure SQL felügyelt példány használata.
>
> Létrehozása és konfigurálása, használja az [Azure Portal](sql-database-managed-instance-get-started.md), [PowerShell](scripts/sql-database-create-configure-managed-instance-powershell.md)vagy [CLI,](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44)majd állítsa be [a helyszínen](sql-database-managed-instance-configure-p2s.md) vagy [virtuális gép](sql-database-managed-instance-configure-vm.md) kapcsolat.
>
> Az adatok betöltéséhez olvassa el a [VISSZAÁLLÍTÁS a BACPAC fájllal](sql-database-import.md) című témakört a [Kalandorbolt](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works) fájllal, vagy olvassa el [a Wide World Importers adatbázis visszaállítása című témakört.](sql-database-managed-instance-get-started-restore.md)

## <a name="get-sql-server-connection-information"></a>SQL-kiszolgálókapcsolati adatok beszerezése

Az Azure SQL-adatbázishoz való csatlakozáshoz szükséges kapcsolati információk beszerezése. A közelgő eljárásokhoz szüksége lesz a teljesen minősített kiszolgáló- vagy állomásnévre, az adatbázis nevére és bejelentkezési adataira.

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com/)

2. Nyissa meg az **SQL-adatbázisok** vagy az **SQL felügyelt példányok** lapot.

3. Az **Áttekintés** lapon tekintse át a **kiszolgáló neve** melletti teljesen minősített kiszolgálónevet egyetlen adatbázishoz, vagy a teljesen minősített kiszolgálónevet a felügyelt példány **gazdagépe** mellett. A kiszolgáló vagy az állomásnév másolásához mutasson rá, és válassza a **Másolás ikont.** 

## <a name="create-the-project"></a>A projekt létrehozása

Nyisson meg egy parancssort, és hozzon létre egy *sqltest* nevű mappát. Nyissa meg a létrehozott mappát, és futtassa a következő parancsot:

  ```bash
  npm init -y
  npm install tedious
  ```

## <a name="add-code-to-query-database"></a>Kód hozzáadása a lekérdezési adatbázishoz

1. A kedvenc szövegszerkesztő, hozzon létre egy új fájlt, *sqltest.js*.

1. Cserélje le a tartalmát a következő kódra. Ezután adja hozzá a kiszolgálónak, az adatbázisnak, a felhasználónak és a jelszónak megfelelő értékeket.

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
> A példakód az **AdventureWorksLT** mintaadatbázist használja az Azure SQL-hez.

## <a name="run-the-code"></a>A kód futtatása

1. A parancssorban futtassa a programot.

    ```bash
    node sqltest.js
    ```

1. Ellenőrizze, hogy a rendszer a 20 legfontosabb sort adja-e vissza, és zárja be az alkalmazásablakot.

## <a name="next-steps"></a>További lépések

- [Microsoft Node.js illesztőprogram az SQL Serverhez](/sql/connect/node-js/node-js-driver-for-sql-server)

- Csatlakozás és lekérdezés Windows/Linux/macOS rendszeren [Visual Studio Code](sql-database-connect-query-vscode.md) [.](sql-database-connect-query-dotnet-core.md) [SSMS](sql-database-connect-query-ssms.md)

- [A .NET Core használatának első lépései Windows/Linux/macOS rendszeren a parancssorból](/dotnet/core/tutorials/using-with-xplat-cli)

- Az első Azure SQL-adatbázis tervezése [.NET](sql-database-design-first-database-csharp.md) vagy [SSMS](sql-database-design-first-database.md) használatával
