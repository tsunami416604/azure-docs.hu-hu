---
title: 'SSMS: kapcsolat és adatlekérdezés'
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
ms.date: 03/10/2020
ms.openlocfilehash: 31bd47128a272e75d7021180b536fe6bf7420f55
ms.sourcegitcommit: c29b7870f1d478cec6ada67afa0233d483db1181
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79299294"
---
# <a name="quickstart-use-sql-server-management-studio-to-connect-and-query-an-azure-sql-database"></a>Gyors útmutató: Használata SQL Server Management Studio és az Azure SQL Database-adatbázis lekérdezéséhez

Ebből a rövid útmutatóból megtudhatja, hogyan használható SQL Server Management Studio (SSMS) egy Azure SQL Database-adatbázishoz való kapcsolódáshoz és néhány lekérdezés futtatásához.

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató elvégzéséhez a következő elemek szükségesek:

- [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms/).
- A AdventureWorksLT minta adatbázisa. Ha a AdventureWorksLT-adatbázisnak egy működő példányára van szüksége, hozzon létre egyet az [Azure SQL Database](sql-database-single-database-get-started.md) gyors útmutatójának létrehozásával.
    - A cikkben található parancsfájlok a AdventureWorksLT-adatbázis használatára íródnak. Felügyelt példány használata esetén importálnia kell a AdventureWorks-adatbázist egy példány-adatbázisba, vagy módosítania kell a jelen cikkben szereplő parancsfájlokat a Wide World importőr-adatbázis használatára.

Ha csak néhány alkalmi lekérdezést szeretne futtatni a SSMS telepítése nélkül, tekintse meg a gyors útmutató [: az SQL-adatbázisok lekérdezéséhez használja a Azure Portal lekérdezési szerkesztőjét](sql-database-connect-query-portal.md).

## <a name="get-sql-server-connection-information"></a>SQL Server-kapcsolatok adatainak beolvasása

Az adatbázishoz való kapcsolódáshoz szükséges kapcsolati adatok beolvasása. A rövid útmutató elvégzéséhez szüksége lesz a teljes kiszolgálónévre vagy az állomásnévre, az adatbázis nevére és a bejelentkezési adatokra.

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com/).

2. Navigáljon a lekérdezni kívánt **SQL-adatbázishoz** vagy **SQL felügyelt példányhoz** .

3. Az **Áttekintés** lapon másolja a teljes kiszolgálónevet. Ez egy önálló adatbázis **kiszolgálójának neve** , vagy a felügyelt példányhoz tartozó **gazdagép** melletti teljes kiszolgálónév. A teljes név a következőképpen néz ki: *servername.database.Windows.net*, kivéve, ha a tényleges kiszolgáló neve.

## <a name="connect-to-your-database"></a>Csatlakozás az adatbázishoz

A SSMS-ben kapcsolódjon a Azure SQL Database-kiszolgálóhoz.

> [!IMPORTANT]
> Az Azure SQL Database-kiszolgáló a 1433-es portot figyeli. Ha SQL Database-kiszolgálóhoz szeretne csatlakozni a vállalati tűzfal mögött, a tűzfalnak nyitva kell lennie a porton.

1. Nyissa meg az SSMS-t.

2. A **Connect to Server** (Kapcsolódás a kiszolgálóhoz) párbeszédpanel jelenik meg. Adja meg a következő információkat:

   | Beállítás      | Ajánlott érték    | Leírás |
   | ------------ | ------------------ | ----------- |
   | **Kiszolgáló típusa** | Adatbázismotor | Az érték megadása kötelező. |
   | **Kiszolgálónév** | A teljes kiszolgálónév | Valami hasonló: **servername.database.Windows.net**. |
   | **Hitelesítés** | SQL Server-hitelesítés | Ez az oktatóanyag az SQL-hitelesítést használ. |
   | **Bejelentkezés** | Kiszolgálói rendszergazdai fiók felhasználói azonosító | A felhasználói Azonosítót a a kiszolgáló létrehozásához használt kiszolgálói rendszergazdai fiókkal. |
   | **Jelszó** | Kiszolgálói rendszergazdai fiók jelszava | A kiszolgáló létrehozásához használt, a kiszolgálói rendszergazdai fiók jelszavát. |
   ||||

   ![kapcsolódás a kiszolgálóhoz](./media/sql-database-connect-query-ssms/connect.png)  

3. A **Kapcsolódás a kiszolgálóhoz** párbeszédpanelen válassza a **Beállítások lehetőséget** . A **Kapcsolódás az adatbázishoz** legördülő menüben válassza a **mySampleDatabase**lehetőséget. Az [Előfeltételek szakaszban](#prerequisites) a rövid útmutató elvégzésével létrehoz egy MySampleDatabase nevű AdventureWorksLT-adatbázist. Ha a AdventureWorks-adatbázis munkapéldánya eltérő névvel rendelkezik, mint a mySampleDatabase, válassza ki helyette.

   ![csatlakozás kiszolgálón található adatbázishoz](./media/sql-database-connect-query-ssms/options-connect-to-db.png)  

4. Kattintson a **Csatlakozás** gombra. Az Object Explorer ablak nyílik meg.

5. Az adatbázis objektumainak megtekintéséhez bontsa ki az **adatbázisok** elemet, majd bontsa ki az adatbázis csomópontot.

   ![mySampleDatabase objektumok](./media/sql-database-connect-query-ssms/connected.png)  

## <a name="query-data"></a>Adatok lekérdezése

Futtassa ezt a [Select](https://msdn.microsoft.com/library/ms189499.aspx) Transact-SQL-kódot az első 20 termék kategóriánkénti lekérdezéséhez.

1. A Object Explorer kattintson a jobb gombbal a **mySampleDatabase** elemre, és válassza az **Új lekérdezés**elemet. Megnyílik az adatbázishoz csatlakoztatott új lekérdezési ablak.

2. A lekérdezési ablakban illessze be a következő SQL-lekérdezést:

   ```sql
   SELECT pc.Name as CategoryName, p.name as ProductName
   FROM [SalesLT].[ProductCategory] pc
   JOIN [SalesLT].[Product] p
   ON pc.productcategoryid = p.productcategoryid;
   ```

3. Az eszköztáron kattintson az **Execute (végrehajtás** ) elemre a lekérdezés futtatásához és az adatok lekéréséhez a `Product` és `ProductCategory` táblákból.

    ![lekérdezés a tábla termék-és ProductCategory adatainak lekéréséhez](./media/sql-database-connect-query-ssms/query2.png)

### <a name="insert-data"></a>Adat beszúrása

Futtassa ezt a [Insert](https://msdn.microsoft.com/library/ms174335.aspx) Transact-SQL-kódot egy új termék létrehozásához a `SalesLT.Product` táblában.

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

2. Válassza az **Execute (végrehajtás** ) lehetőséget egy új sor beszúrásához a `Product` táblában. Megjelenik az **üzenetek** ablaktábla **(1 sor érintett)** .

#### <a name="view-the-result"></a>Az eredmény megtekintéséhez

1. Ehhez cserélje le az előző lekérdezést.

   ```sql
   SELECT * FROM [SalesLT].[Product]
   WHERE Name='myNewProduct'
   ```

2. Válassza a **Végrehajtás** lehetőséget. A következő eredmény jelenik meg.

   ![a Product Table lekérdezés eredménye](./media/sql-database-connect-query-ssms/result.png)

### <a name="update-data"></a>Adatok frissítése

Futtassa ezt a [frissítési](https://msdn.microsoft.com/library/ms177523.aspx) Transact-SQL-kódot az új termék módosításához.

1. Cserélje le az előző lekérdezést, amely a korábban létrehozott új rekordot adja vissza:

   ```sql
   UPDATE [SalesLT].[Product]
   SET [ListPrice] = 125
   WHERE Name = 'myNewProduct';
   ```

2. Válassza a **végrehajtás** lehetőséget a `Product` tábla megadott sorának frissítéséhez. Megjelenik az **üzenetek** ablaktábla **(1 sor érintett)** .

### <a name="delete-data"></a>Adat törlése

Futtassa ezt a [delete](https://msdn.microsoft.com/library/ms189835.aspx) Transact-SQL-kódot az új termék eltávolításához.

1. Ehhez cserélje le az előző lekérdezést.

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
