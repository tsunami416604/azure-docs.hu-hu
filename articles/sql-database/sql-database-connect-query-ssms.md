---
title: 'SSMS: Egy Azure SQL Database-adatbázisban lévő adatkapcsolat és-lekérdezés | Microsoft Docs'
description: Ebből a cikkből megtudhatja, hogyan csatlakozhat az SQL Database-hez az Azure-ban az SQL Server Management Studio (SSMS) használatával. Ezután futtasson Transact-SQL (T-SQL) utasításokat az adatok lekérdezéséhez és szerkesztéséhez.
keywords: csatlakozás sql database-hez,sql server management studio
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/25/2019
ms.openlocfilehash: 0a08ee9d38065b0dff13f68a7b5473aa93787cf5
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68569181"
---
# <a name="quickstart-use-sql-server-management-studio-to-connect-and-query-an-azure-sql-database"></a>Gyors útmutató: SQL Server Management Studio használata Azure SQL Database-adatbázishoz való kapcsolódáshoz és lekérdezéshez

Ebben a rövid útmutatóban [SQL Server Management Studio][ssms-install-latest-84g] (SSMS) használatával csatlakozhat egy Azure SQL Database-adatbázishoz. Ezután a Transact-SQL-utasításokkal adatokat kell lekérdezni, beszúrni, frissíteni és törölni. A SSMS segítségével bármilyen SQL-infrastruktúrát kezelhet, SQL Server a Microsoft Windows SQL Databasehoz.  

## <a name="prerequisites"></a>Előfeltételek

Azure SQL-adatbázis. Az alábbi rövid útmutatók segítségével hozhat létre és konfigurálhat egy adatbázist Azure SQL Databaseban:

  || Önálló adatbázis | Felügyelt példány |
  |:--- |:--- |:---|
  | Hozzon létre| [Portál](sql-database-single-database-get-started.md) | [Portál](sql-database-managed-instance-get-started.md) |
  || [Parancssori felület](scripts/sql-database-create-and-configure-database-cli.md) | [Parancssori felület](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [PowerShell](scripts/sql-database-create-and-configure-database-powershell.md) | [PowerShell](scripts/sql-database-create-configure-managed-instance-powershell.md) |
  | Konfigurálás | [Kiszolgálói szintű IP-tűzfalszabály](sql-database-server-level-firewall-rule.md)| [Kapcsolódás virtuális gépről](sql-database-managed-instance-configure-vm.md)|
  |||[Kapcsolódás a webhelyről](sql-database-managed-instance-configure-p2s.md)
  |Adatok betöltése|Adventure Works betöltve|[Széles körű globális importőrök visszaállítása](sql-database-managed-instance-get-started-restore.md)
  |||Adventure Works visszaállítása vagy importálása a [BACPAC](sql-database-import.md) -fájlból a [githubról](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)|

  > [!IMPORTANT]
  > A cikkben található parancsfájlok az Adventure Works-adatbázis használatára íródnak. Felügyelt példány esetén importálnia kell az Adventure Works-adatbázist egy példány-adatbázisba, vagy módosítania kell a jelen cikkben szereplő parancsfájlokat a Wide World Importálós adatbázis használatára.

## <a name="install-the-latest-ssms"></a>Az SSMS legújabb verziójának telepítése

Mielőtt elkezdené, győződjön meg arról, hogy telepítette a legújabb [SSMS][ssms-install-latest-84g].

## <a name="get-sql-server-connection-information"></a>SQL Server-kapcsolatok adatainak beolvasása

Az Azure SQL Database-adatbázishoz való kapcsolódáshoz szükséges kapcsolati adatok beolvasása. A közelgő eljárásokhoz szüksége lesz a teljes kiszolgálónévre vagy az állomásnévre, az adatbázis nevére és a bejelentkezési adatokra.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

2. Navigáljon az **SQL-adatbázisok** vagy az **SQL-felügyelt példányok** lapra.

3. Az **Áttekintés** lapon tekintse át a teljes kiszolgálónevet a **kiszolgáló neve** mellett egyetlen adatbázishoz vagy a felügyelt példányhoz tartozó **gazdagép** melletti teljes kiszolgálónévhez. A kiszolgálónév vagy az állomásnév másolásához vigye a kurzort a fölé, és válassza a **Másolás** ikont.

## <a name="connect-to-your-database"></a>Csatlakozás az adatbázishoz

Az SMSS csatlakozzon az Azure SQL Database-kiszolgálóhoz.

> [!IMPORTANT]
> Az Azure SQL Database-kiszolgáló a 1433-es portot figyeli. Ha SQL Database-kiszolgálóhoz szeretne csatlakozni a vállalati tűzfal mögött, a tűzfalnak nyitva kell lennie a porton.
>

1. Nyissa meg az SSMS-t. A **Connect to Server** (Kapcsolódás a kiszolgálóhoz) párbeszédpanel jelenik meg.

2. Adja meg a következő információkat:

   | Beállítás      | Ajánlott érték    | Leírás |
   | ------------ | ------------------ | ----------- |
   | **Kiszolgáló típusa** | Adatbázismotor | Az érték megadása kötelező. |
   | **Kiszolgálónév** | A teljes kiszolgálónév | Hasonló: **mynewserver20170313.database.windows.net**. |
   | **Hitelesítés** | SQL Server-hitelesítés | Ez az oktatóanyag az SQL-hitelesítést használ. |
   | **Bejelentkezés** | Kiszolgálói rendszergazdai fiók felhasználói azonosító | A felhasználói Azonosítót a a kiszolgáló létrehozásához használt kiszolgálói rendszergazdai fiókkal. |
   | **Jelszó** | Kiszolgálói rendszergazdai fiók jelszava | A kiszolgáló létrehozásához használt, a kiszolgálói rendszergazdai fiók jelszavát. |
   ||||

   ![kapcsolódás a kiszolgálóhoz](./media/sql-database-connect-query-ssms/connect.png)  

3. Válassza ki **beállítások** a a **kapcsolódás a kiszolgálóhoz** párbeszédpanel bezárásához. Az a **csatlakozhat az adatbázishoz** legördülő menüjében válassza **mySampleDatabase**.

   ![csatlakozás kiszolgálón található adatbázishoz](./media/sql-database-connect-query-ssms/options-connect-to-db.png)  

4. Kattintson a **Csatlakozás** gombra. Az Object Explorer ablak nyílik meg.

5. Az adatbázis-objektumok megtekintéséhez bontsa ki a **adatbázisok** majd **mySampleDatabase**.

   ![mySampleDatabase objektumok](./media/sql-database-connect-query-ssms/connected.png)  

## <a name="query-data"></a>Adatok lekérdezése

Futtassa ezt a [Select](https://msdn.microsoft.com/library/ms189499.aspx) Transact-SQL-kódot az első 20 termék kategóriánkénti lekérdezéséhez.

1. Az Object Explorerben kattintson a jobb gombbal **mySampleDatabase** válassza **új lekérdezés**. Megnyílik az adatbázishoz csatlakoztatott új lekérdezési ablak.

2. A lekérdezési ablakban illessze be az SQL-lekérdezést.

   ```sql
   SELECT pc.Name as CategoryName, p.name as ProductName
   FROM [SalesLT].[ProductCategory] pc
   JOIN [SalesLT].[Product] p
   ON pc.productcategoryid = p.productcategoryid;
   ```

3. Válassza az eszköztár **Execute** be adatokat a `Product` és `ProductCategory` táblákat.

    ![lekérdezés a tábla termék-és ProductCategory adatainak lekéréséhez](./media/sql-database-connect-query-ssms/query2.png)

## <a name="insert-data"></a>Adat beszúrása

Futtassa ezt a [Insert](https://msdn.microsoft.com/library/ms174335.aspx) Transact-SQL-kódot egy új termék létrehozásához `SalesLT.Product` a táblában.

1. Ehhez cserélje le az előző lekérdezést.

   ```sql
   INSERT INTO [SalesLT].[Product]
           ( [Name]
           , [ProductNumber]
           , [Color]
           , [ProductCategoryID]
           , [StandardCost]
           , [ListPrice]
           , [SellStartDate] )
     VALUES
           ('myNewProduct'
           ,123456789
           ,'NewColor'
           ,1
           ,100
           ,100
           ,GETDATE() );
   ```

2. Válassza a **végrehajtás** elemet egy új sor `Product` táblázatba való beszúrásához. A **üzenetek** ablaktáblán megjelennek azok **(1 sort érint)** .

## <a name="view-the-result"></a>Az eredmény megtekintéséhez

1. Ehhez cserélje le az előző lekérdezést.

   ```sql
   SELECT * FROM [SalesLT].[Product]
   WHERE Name='myNewProduct'
   ```

2. Válassza a **Végrehajtás** lehetőséget. A következő eredmény jelenik meg.

   ![a Product Table lekérdezés eredménye](./media/sql-database-connect-query-ssms/result.png)

## <a name="update-data"></a>Adatok frissítése

Futtassa ezt a [frissítési](https://msdn.microsoft.com/library/ms177523.aspx) Transact-SQL-kódot az új termék módosításához.

1. Ehhez cserélje le az előző lekérdezést.

   ```sql
   UPDATE [SalesLT].[Product]
   SET [ListPrice] = 125
   WHERE Name = 'myNewProduct';
   ```

2. Válassza a **végrehajtás** lehetőséget a `Product` tábla megadott sorának frissítéséhez. A **üzenetek** ablaktáblán megjelennek azok **(1 sort érint)** .

## <a name="delete-data"></a>Adat törlése

Futtassa ezt a [delete](https://msdn.microsoft.com/library/ms189835.aspx) Transact-SQL-kódot az új termék eltávolításához.

1. Ehhez cserélje le az előző lekérdezést.

   ```sql
   DELETE FROM [SalesLT].[Product]
   WHERE Name = 'myNewProduct';
   ```

2. Válassza a **végrehajtás** lehetőséget a `Product` tábla megadott sorának törléséhez. A **üzenetek** ablaktáblán megjelennek azok **(1 sort érint)** .

## <a name="next-steps"></a>További lépések

- Ssms használatával kapcsolatos információkért lásd: [SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx).
- Az Azure Portal használatával történő csatlakozásról és lekérdezésről lásd az [Azure Portal SQL-lekérdezésszerkesztővel végzett csatlakozásról és lekérdezésről](sql-database-connect-query-portal.md) szóló témakört.
- A Visual Studio Code használatával történő csatlakozásról és lekérdezésről lásd a [Visual Studio Code használatával végzett csatlakozásról és lekérdezésről](sql-database-connect-query-vscode.md) szóló témakört.
- A .NET használatával történő csatlakozásról és lekérdezésről lásd a [.NET használatával végzett csatlakozásról és lekérdezésről](sql-database-connect-query-dotnet.md) szóló témakört.
- A PHP-vel történő csatlakozásról és lekérdezésről lásd a [PHP-vel végzett csatlakozásról és lekérdezésről](sql-database-connect-query-php.md) szóló témakört.
- A Node.js használatával történő csatlakozásról és lekérdezésről lásd a [Node.js használatával végzett csatlakozásról és lekérdezésről](sql-database-connect-query-nodejs.md) szóló témakört.
- A Java használatával történő csatlakozásról és lekérdezésről lásd a [Java használatával végzett csatlakozásról és lekérdezésről](sql-database-connect-query-java.md) szóló témakört.
- A Python használatával történő csatlakozásról és lekérdezésről lásd a [Python használatával végzett csatlakozásról és lekérdezésről](sql-database-connect-query-python.md) szóló témakört.
- A Ruby használatával történő csatlakozásról és lekérdezésről lásd a [Ruby használatával végzett csatlakozásról és lekérdezésről](sql-database-connect-query-ruby.md) szóló témakört.

<!-- Article link references. -->

[ssms-install-latest-84g]: https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms
