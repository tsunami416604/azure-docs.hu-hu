---
title: 'SSMS: az Azure SQL Database-ben lévő adatkapcsolat és-lekérdezés '
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
ms.openlocfilehash: 0744b8bc8e3582a23802d87424bba9f92bf43073
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73690888"
---
# <a name="quickstart-use-sql-server-management-studio-to-connect-and-query-an-azure-sql-database"></a>Gyors útmutató: Azure SQL Database-adatbázis összekapcsolásának és lekérdezésének SQL Server Management Studio használata

Ebben a rövid útmutatóban [SQL Server Management Studio][ssms-install-latest-84g] (SSMS) használatával csatlakozhat egy Azure SQL Database-adatbázishoz. Ezután a Transact-SQL-utasításokkal adatokat kell lekérdezni, beszúrni, frissíteni és törölni. A SSMS segítségével bármilyen SQL-infrastruktúrát kezelhet, SQL Server a Microsoft Windows SQL Databasehoz.  

## <a name="prerequisites"></a>Előfeltételek

Azure SQL-adatbázis. Az alábbi rövid útmutatók segítségével hozhat létre és konfigurálhat egy adatbázist Azure SQL Databaseban:

  || Önálló adatbázis | Felügyelt példány |
  |:--- |:--- |:---|
  | Létrehozás| [Portál](sql-database-single-database-get-started.md) | [Portál](sql-database-managed-instance-get-started.md) |
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

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com/).

2. Navigáljon az **SQL-adatbázisok** vagy az **SQL-felügyelt példányok** lapra.

3. Az **Áttekintés** lapon tekintse át a teljes kiszolgálónevet a **kiszolgáló neve** mellett egyetlen adatbázishoz vagy a felügyelt példányhoz tartozó **gazdagép** melletti teljes kiszolgálónévhez. A kiszolgálónév vagy az állomásnév másolásához vigye a kurzort a fölé, és válassza a **Másolás** ikont.

## <a name="connect-to-your-database"></a>Csatlakozás az adatbázishoz

Az SMS-ben csatlakozhat a Azure SQL Database-kiszolgálóhoz.

> [!IMPORTANT]
> Az Azure SQL Database-kiszolgáló a 1433-es portot figyeli. Ha SQL Database-kiszolgálóhoz szeretne csatlakozni a vállalati tűzfal mögött, a tűzfalnak nyitva kell lennie a porton.
>

1. Nyissa meg az SSMS-t. A **Connect to Server** (Kapcsolódás a kiszolgálóhoz) párbeszédpanel jelenik meg.

2. Adja meg a következő információkat:

   | Beállítás      | Ajánlott érték    | Leírás |
   | ------------ | ------------------ | ----------- |
   | **Kiszolgáló típusa** | Adatbázismotor | Szükséges érték. |
   | **Kiszolgálónév** | A teljes kiszolgálónév | Valami hasonló: **mynewserver20170313.database.Windows.net**. |
   | **Hitelesítés** | SQL Server-hitelesítés | Ez az oktatóanyag SQL-hitelesítést használ. |
   | **Bejelentkezés** | Kiszolgáló-rendszergazdai fiók felhasználói azonosítója | A kiszolgáló létrehozásához használt kiszolgálói rendszergazdai fiók felhasználói azonosítója. |
   | **Jelszó** | Kiszolgáló-rendszergazdai fiók jelszava | A kiszolgáló létrehozásához használt kiszolgáló-rendszergazdai fiók jelszava. |
   ||||

   ![kapcsolódás a kiszolgálóhoz](./media/sql-database-connect-query-ssms/connect.png)  

3. A **Kapcsolódás a kiszolgálóhoz** párbeszédpanelen válassza a **Beállítások lehetőséget** . A **Kapcsolódás az adatbázishoz** legördülő menüben válassza a **mySampleDatabase**lehetőséget.

   ![csatlakozás kiszolgálón található adatbázishoz](./media/sql-database-connect-query-ssms/options-connect-to-db.png)  

4. Kattintson a **Csatlakozás** gombra. Megnyílik a Object Explorer ablak.

5. Az adatbázis objektumainak megtekintéséhez bontsa ki az **adatbázisok** csomópontot, majd bontsa ki a **mySampleDatabase**elemet.

   ![mySampleDatabase objektumok](./media/sql-database-connect-query-ssms/connected.png)  

## <a name="query-data"></a>Adatok lekérdezése

Futtassa ezt a [Select](https://msdn.microsoft.com/library/ms189499.aspx) Transact-SQL-kódot az első 20 termék kategóriánkénti lekérdezéséhez.

1. A Object Explorer kattintson a jobb gombbal a **mySampleDatabase** elemre, és válassza az **Új lekérdezés**elemet. Megnyílik az adatbázishoz csatlakoztatott új lekérdezési ablak.

2. Illessze be ezt az SQL-lekérdezést a lekérdezési ablakba.

   ```sql
   SELECT pc.Name as CategoryName, p.name as ProductName
   FROM [SalesLT].[ProductCategory] pc
   JOIN [SalesLT].[Product] p
   ON pc.productcategoryid = p.productcategoryid;
   ```

3. Az eszköztáron kattintson az **Execute (végrehajtás** ) elemre az adatok `Product` és `ProductCategory` táblákból való lekéréséhez.

    ![lekérdezés a tábla termék-és ProductCategory adatainak lekéréséhez](./media/sql-database-connect-query-ssms/query2.png)

## <a name="insert-data"></a>Adat beszúrása

Futtassa ezt a [Insert](https://msdn.microsoft.com/library/ms174335.aspx) Transact-SQL-kódot egy új termék létrehozásához a `SalesLT.Product` táblában.

1. Cserélje le az előző lekérdezést ezzel az eggyel.

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

2. Válassza az **Execute (végrehajtás** ) lehetőséget egy új sor beszúrásához a `Product` táblában. Megjelenik az **üzenetek** ablaktábla **(1 sor érintett)** .

## <a name="view-the-result"></a>Az eredmény megtekintése

1. Cserélje le az előző lekérdezést ezzel az eggyel.

   ```sql
   SELECT * FROM [SalesLT].[Product]
   WHERE Name='myNewProduct'
   ```

2. Válassza a **Végrehajtás** lehetőséget. A következő eredmény jelenik meg.

   ![a Product Table lekérdezés eredménye](./media/sql-database-connect-query-ssms/result.png)

## <a name="update-data"></a>Adatok frissítése

Futtassa ezt a [frissítési](https://msdn.microsoft.com/library/ms177523.aspx) Transact-SQL-kódot az új termék módosításához.

1. Cserélje le az előző lekérdezést ezzel az eggyel.

   ```sql
   UPDATE [SalesLT].[Product]
   SET [ListPrice] = 125
   WHERE Name = 'myNewProduct';
   ```

2. Válassza a **végrehajtás** lehetőséget a `Product` tábla megadott sorának frissítéséhez. Megjelenik az **üzenetek** ablaktábla **(1 sor érintett)** .

## <a name="delete-data"></a>Adat törlése

Futtassa ezt a [delete](https://msdn.microsoft.com/library/ms189835.aspx) Transact-SQL-kódot az új termék eltávolításához.

1. Cserélje le az előző lekérdezést ezzel az eggyel.

   ```sql
   DELETE FROM [SalesLT].[Product]
   WHERE Name = 'myNewProduct';
   ```

2. Válassza a **végrehajtás** lehetőséget a megadott sor törléséhez a `Product` táblában. Megjelenik az **üzenetek** ablaktábla **(1 sor érintett)** .

## <a name="next-steps"></a>További lépések

- További információ a SSMS: [SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx).
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
