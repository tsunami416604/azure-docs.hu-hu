---
title: Adatbázis lekérdezése a Node. js használatával
description: Hogyan használható a Node. js egy olyan program létrehozásához, amely Azure SQL Database-adatbázishoz kapcsolódik, és T-SQL-utasítások használatával kérdezi le.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.devlang: nodejs
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: v-masebo
ms.date: 03/25/2019
ms.custom: seo-javascript-september2019, seo-javascript-october2019, sqldbrb=2 
ms.openlocfilehash: 5f53d6b3e8b477d7b93eb1063679126a9533ef03
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2020
ms.locfileid: "84054478"
---
# <a name="quickstart-use-nodejs-to-query-a-microsoft-azure-sql-database"></a>Gyors útmutató: Microsoft Azure SQL-adatbázis lekérdezése a Node. js használatával
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Ebben a rövid útmutatóban a Node. js használatával csatlakozhat egy Azure SQL Database-adatbázishoz, és T-SQL-utasítások használatával kérdez le adatokat.

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató elvégzéséhez a következőkre lesz szüksége:

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

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


- [Node. js](https://nodejs.org)-hez kapcsolódó szoftverek

  # <a name="macos"></a>[macOS](#tab/macos)

  Telepítse a Homebrew-t és a Node. js-t, majd telepítse az ODBC-illesztőt és a SQLCMD-t a **1,2** -es és **1,3** -as lépések használatával a [Node. js-alkalmazások létrehozása SQL Server](https://www.microsoft.com/sql-server/developer-get-started/node/mac/)

  # <a name="ubuntu"></a>[Ubuntu](#tab/ubuntu)

  Telepítse a Node. js-t, majd telepítse az ODBC-illesztőt és a SQLCMD-t a **1,2** -es és **1,3** -as lépések használatával a [Node. js-alkalmazások létrehozása SQL Server használatával Ubuntu rendszeren](https://www.microsoft.com/sql-server/developer-get-started/node/ubuntu/)

  # <a name="windows"></a>[Windows](#tab/windows)

  Telepítse a csokoládét és a Node. js-t, majd telepítse az ODBC-illesztőt és a SQLCMD-t a **1,2** -es és **1,3** -as lépések használatával a [Node. js-alkalmazások létrehozása a Windows SQL Server](https://www.microsoft.com/sql-server/developer-get-started/node/windows/)

  ---

> [!IMPORTANT]
> A cikkben található parancsfájlok az **Adventure Works** -adatbázis használatára íródnak.

> [!NOTE]
> Dönthet úgy is, hogy egy Azure SQL felügyelt példányt használ.
>
> A létrehozásához és konfigurálásához használja a [Azure Portal](../managed-instance/instance-create-quickstart.md), a [PowerShellt](../managed-instance/scripts/create-configure-managed-instance-powershell.md)vagy a [CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44)-t, majd a helyszíni vagy a [VM](../managed-instance/connect-vm-instance-configure.md) [-](../managed-instance/point-to-site-p2s-configure.md) kapcsolat beállítását.
>
> Az betöltéssel kapcsolatban lásd: [visszaállítás a BACPAC](database-import.md) az [Adventure Works](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works) -fájllal, vagy lásd: [a Wide World](../managed-instance/restore-sample-database-quickstart.md)importing-adatbázis visszaállítása.

## <a name="get-sql-server-connection-information"></a>SQL Server-kapcsolatok adatainak beolvasása

Szerezze be a Azure SQL Databasehoz való kapcsolódáshoz szükséges kapcsolati adatokat. A közelgő eljárásokhoz szüksége lesz a teljes kiszolgálónévre vagy az állomásnévre, az adatbázis nevére és a bejelentkezési adatokra.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

2. Nyissa meg az **SQL-adatbázisok** vagy az SQL- **felügyelt példányok** lapot.

3. Az **Áttekintés** lapon tekintse át a teljes kiszolgálónevet a **kiszolgáló neve** mellett egy Azure SQL Database vagy a teljes kiszolgálónév (vagy IP-cím) mellett egy Azure SQL felügyelt példányhoz vagy egy Azure-beli **virtuális gépen SQL Server** . A kiszolgálónév vagy az állomásnév másolásához vigye a kurzort a fölé, és válassza a **Másolás** ikont.

> [!NOTE]
> Az Azure-beli virtuális gépek SQL Server kapcsolati információit lásd: [kapcsolódás SQL Serverhoz](../virtual-machines/windows/sql-vm-create-portal-quickstart.md#connect-to-sql-server)

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

## <a name="next-steps"></a>További lépések

- [Microsoft Node.js illesztőprogram az SQL Serverhez](/sql/connect/node-js/node-js-driver-for-sql-server)

- Kapcsolat és lekérdezés Windows/Linux/macOS rendszeren [.net Core](connect-query-dotnet-core.md), [Visual Studio Code](connect-query-vscode.md)vagy [SSMS](connect-query-ssms.md) (csak Windows rendszeren)

- [A .NET Core használatának első lépései Windows/Linux/macOS rendszeren a parancssor használatával](/dotnet/core/tutorials/using-with-xplat-cli)

- Az első Azure SQL Database megtervezése [.net](design-first-database-csharp-tutorial.md) vagy [SSMS](design-first-database-tutorial.md) használatával
