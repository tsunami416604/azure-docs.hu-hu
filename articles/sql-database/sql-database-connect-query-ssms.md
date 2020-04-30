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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "79299294"
---
# <a name="quickstart-use-sql-server-management-studio-to-connect-and-query-an-azure-sql-database"></a>Gyors útmutató: Azure SQL Database-adatbázis összekapcsolásának és lekérdezésének SQL Server Management Studio használata

Ebből a rövid útmutatóból megtudhatja, hogyan használható SQL Server Management Studio (SSMS) egy Azure SQL Database-adatbázishoz való kapcsolódáshoz és néhány lekérdezés futtatásához.

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató elvégzéséhez a következő elemek szükségesek:

- [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms/).
- A AdventureWorksLT minta adatbázisa. Ha a AdventureWorksLT-adatbázisnak egy működő példányára van szüksége, hozzon létre egyet az [Azure SQL Database](sql-database-single-database-get-started.md) gyors útmutatójának létrehozásával.
    - A cikkben található parancsfájlok a AdventureWorksLT-adatbázis használatára íródnak. Felügyelt példány használata esetén importálnia kell a AdventureWorks-adatbázist egy példány-adatbázisba, vagy módosítania kell a jelen cikkben szereplő parancsfájlokat a Wide World importőr-adatbázis használatára.

Ha csak néhány alkalmi lekérdezést szeretne futtatni a SSMS telepítése nélkül, tekintse meg a gyors útmutató [: az SQL-adatbázisok lekérdezéséhez használja a Azure Portal lekérdezési szerkesztőjét](sql-database-connect-query-portal.md).

## <a name="get-sql-server-connection-information"></a>SQL Server-kapcsolatok adatainak beolvasása

Az adatbázishoz való kapcsolódáshoz szükséges kapcsolati adatok beolvasása. A rövid útmutató elvégzéséhez szüksége lesz a teljes kiszolgálónévre vagy az állomásnévre, az adatbázis nevére és a bejelentkezési adatokra.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

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
   | **Kiszolgáló típusa** | Adatbázismotor | Szükséges érték. |
   | **Kiszolgáló neve** | A teljes kiszolgálónév | Valami hasonló: **servername.database.Windows.net**. |
   | **Hitelesítés** | SQL Server-hitelesítés | Ez az oktatóanyag SQL-hitelesítést használ. |
   | **Bejelentkezés** | Kiszolgáló-rendszergazdai fiók felhasználói azonosítója | A kiszolgáló létrehozásához használt kiszolgálói rendszergazdai fiók felhasználói azonosítója. |
   | **Jelszó** | Kiszolgáló-rendszergazdai fiók jelszava | A kiszolgáló létrehozásához használt kiszolgáló-rendszergazdai fiók jelszava. |
   ||||

   ![kapcsolódás a kiszolgálóhoz](./media/sql-database-connect-query-ssms/connect.png)  

3. A **Kapcsolódás a kiszolgálóhoz** párbeszédpanelen válassza a **Beállítások lehetőséget** . A **Kapcsolódás az adatbázishoz** legördülő menüben válassza a **mySampleDatabase**lehetőséget. Az [Előfeltételek szakaszban](#prerequisites) a rövid útmutató elvégzésével létrehoz egy MySampleDatabase nevű AdventureWorksLT-adatbázist. Ha a AdventureWorks-adatbázis munkapéldánya eltérő névvel rendelkezik, mint a mySampleDatabase, válassza ki helyette.

   ![csatlakozás kiszolgálón található adatbázishoz](./media/sql-database-connect-query-ssms/options-connect-to-db.png)  

4. Kattintson a **Csatlakozás** gombra. Megnyílik a Object Explorer ablak.

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

3. Az eszköztáron kattintson az **Execute (végrehajtás** ) elemre a lekérdezés futtatásához `Product` és `ProductCategory` az adatok lekéréséhez a és a táblákból.

    ![lekérdezés a tábla termék-és ProductCategory adatainak lekéréséhez](./media/sql-database-connect-query-ssms/query2.png)

### <a name="insert-data"></a>Adat beszúrása

Futtassa ezt a [Insert](https://msdn.microsoft.com/library/ms174335.aspx) Transact-SQL-kódot egy új termék létrehozásához `SalesLT.Product` a táblában.

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

2. Válassza a **végrehajtás** elemet egy új sor `Product` táblázatba való beszúrásához. Megjelenik az **üzenetek** ablaktábla **(1 sor érintett)**.

#### <a name="view-the-result"></a>Az eredmény megtekintése

1. Cserélje le az előző lekérdezést ezzel az eggyel.

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

2. Válassza a **végrehajtás** lehetőséget a `Product` tábla megadott sorának frissítéséhez. Megjelenik az **üzenetek** ablaktábla **(1 sor érintett)**.

### <a name="delete-data"></a>Adat törlése

Futtassa ezt a [delete](https://msdn.microsoft.com/library/ms189835.aspx) Transact-SQL-kódot az új termék eltávolításához.

1. Cserélje le az előző lekérdezést ezzel az eggyel.

   ```sql
   DELETE FROM [SalesLT].[Product]
   WHERE Name = 'myNewProduct';
   ```

2. Válassza a **végrehajtás** lehetőséget a `Product` tábla megadott sorának törléséhez. Megjelenik az **üzenetek** ablaktábla **(1 sor érintett)**.

## <a name="next-steps"></a>További lépések

- További információ a SSMS: [SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx).
- A Azure Portal használatával történő kapcsolódáshoz és lekérdezéshez lásd: [Kapcsolódás és lekérdezés a Azure Portal SQL-lekérdezés szerkesztőjével](sql-database-connect-query-portal.md).
- A Visual Studio Code használatával történő csatlakozásról és lekérdezésről lásd a [Visual Studio Code használatával végzett csatlakozásról és lekérdezésről](sql-database-connect-query-vscode.md) szóló témakört.
- A .NET használatával történő csatlakozásról és lekérdezésről lásd a [.NET használatával végzett csatlakozásról és lekérdezésről](sql-database-connect-query-dotnet.md) szóló témakört.
- A PHP-vel történő csatlakozásról és lekérdezésről lásd a [PHP-vel végzett csatlakozásról és lekérdezésről](sql-database-connect-query-php.md) szóló témakört.
- A Node.js használatával történő csatlakozásról és lekérdezésről lásd a [Node.js használatával végzett csatlakozásról és lekérdezésről](sql-database-connect-query-nodejs.md) szóló témakört.
- A Java használatával történő csatlakozásról és lekérdezésről lásd a [Java használatával végzett csatlakozásról és lekérdezésről](sql-database-connect-query-java.md) szóló témakört.
- A Python használatával történő csatlakozásról és lekérdezésről lásd a [Python használatával végzett csatlakozásról és lekérdezésről](sql-database-connect-query-python.md) szóló témakört.
- A Ruby használatával történő csatlakozásról és lekérdezésről lásd a [Ruby használatával végzett csatlakozásról és lekérdezésről](sql-database-connect-query-ruby.md) szóló témakört.
