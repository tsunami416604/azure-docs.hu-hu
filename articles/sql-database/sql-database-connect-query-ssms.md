---
title: 'SSMS: Adatok csatlakoztatása és lekérdezése'
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
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "79299294"
---
# <a name="quickstart-use-sql-server-management-studio-to-connect-and-query-an-azure-sql-database"></a>Rövid útmutató: Azure SQL-adatbázis csatlakoztatása és lekérdezése az SQL Server Management Studio használatával

Ebben a rövid útmutatóban megtudhatja, hogyan csatlakozhat az SQL Server Management Studio (SSMS) segítségével egy Azure SQL-adatbázishoz, és hogyan futtathat bizonyos lekérdezéseket.

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató befejezéséhez a következő elemekre van szükség:

- [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms/).
- Az AdventureWorksLT mintaadatbázisa. Ha szüksége van egy működő példányát a AdventureWorksLT adatbázis, hozzon létre egyet az [Azure SQL-adatbázis létrehozása](sql-database-single-database-get-started.md) rövid útmutató kitöltésével.
    - A cikkben szereplő parancsfájlok az AdventureWorksLT adatbázis használatára vannak megírva. Ha felügyelt példányt használ, vagy importálnia kell az AdventureWorks-adatbázist egy példányadatbázisba, vagy módosítania kell a cikkben szereplő parancsfájlokat a Wide World Importers adatbázis használatához.

Ha egyszerűen csak az SSMS telepítése nélkül szeretne futtatni néhány ad-hoc lekérdezést, [olvassa el a Rövid útmutató: Az Azure Portal lekérdezésszerkesztőjének használata SQL-adatbázis lekérdezéséhez](sql-database-connect-query-portal.md)című témakört.

## <a name="get-sql-server-connection-information"></a>SQL-kiszolgálókapcsolati adatok beszerezése

Az adatbázishoz való csatlakozáshoz szükséges csatlakozási adatok beszerezése. A rövid útmutató befejezéséhez a teljesen minősített kiszolgálónévre vagy állomásnévre, adatbázisnévre és bejelentkezési adatokra lesz szüksége.

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com/)

2. Keresse meg a lekérdezni kívánt **SQL-adatbázist** vagy **SQL felügyelt példányt.**

3. Az **Áttekintés** lapon másolja a teljesen minősített kiszolgáló nevét. Ez az egyetlen adatbázis **kiszolgálóneve,** vagy a felügyelt példány **gazdagépe** melletti teljesen minősített kiszolgálónév mellett található. A teljesen minősített név így néz ki: *servername.database.windows.net*, kivéve, hogy rendelkezik a tényleges kiszolgálónévvel.

## <a name="connect-to-your-database"></a>Csatlakozás az adatbázishoz

Az SSMS-ben csatlakozzon az Azure SQL Database-kiszolgálóhoz.

> [!IMPORTANT]
> Egy Azure SQL Database-kiszolgáló figyel az 1433-as porton. Ha vállalati tűzfal mögül szeretne csatlakozni egy SQL Database-kiszolgálóhoz, a tűzfalnak meg kell nyitnia ezt a portot.

1. Nyissa meg az SSMS-t.

2. A **Connect to Server** (Kapcsolódás a kiszolgálóhoz) párbeszédpanel jelenik meg. Adja meg a következő információkat:

   | Beállítás      | Ajánlott érték    | Leírás |
   | ------------ | ------------------ | ----------- |
   | **Kiszolgáló típusa** | Adatbázismotor | Kötelező érték. |
   | **Kiszolgáló neve** | A teljes kiszolgálónév | Valami ilyesmi: **servername.database.windows.net**. |
   | **Hitelesítés** | SQL Server-hitelesítés | Ez az oktatóanyag AZ SQL-hitelesítést használja. |
   | **Bejelentkezés** | Kiszolgálórendszergazdai fiók felhasználói azonosítója | A kiszolgáló létrehozásához használt kiszolgálófelügyeleti fiók felhasználói azonosítója. |
   | **Jelszó** | Kiszolgálórendszergazdai fiók jelszava | A kiszolgáló felügyeleti fiókjának a kiszolgáló létrehozásához használt jelszava. |
   ||||

   ![kapcsolódás a kiszolgálóhoz](./media/sql-database-connect-query-ssms/connect.png)  

3. A Csatlakozás **kiszolgálóhoz** párbeszédpanelbeállítások parancsában válassza a **Beállítások lehetőséget.** A **Csatlakozás adatbázishoz** legördülő menüben válassza a **MySampleDatabase lehetőséget.** A rövid útmutató befejezése az [Előfeltételek szakaszban](#prerequisites) létrehoz egy AdventureWorksLT adatbázis nevű mySampleDatabase. Ha az AdventureWorks-adatbázis munkapéldányának neve eltér a mySampleDatabase-éétól, akkor jelölje ki azt.

   ![csatlakozás kiszolgálón található adatbázishoz](./media/sql-database-connect-query-ssms/options-connect-to-db.png)  

4. Kattintson a **Csatlakozás** gombra. Megnyílik az Objektumkezelő ablak.

5. Az adatbázis objektumainak megtekintéséhez bontsa ki az **Adatbázisok csomópontot, majd bontsa** ki az adatbáziscsomópontot.

   ![mySampleDatabase objektumok](./media/sql-database-connect-query-ssms/connected.png)  

## <a name="query-data"></a>Adatok lekérdezése

Futtassa ezt [a SELECT](https://msdn.microsoft.com/library/ms189499.aspx) Transact-SQL kódot, hogy lekérdezze a top 20 terméket kategória szerint.

1. Az Objektumkezelőben kattintson a jobb gombbal **a MySampleDatabase elemre,** és válassza az **Új lekérdezés parancsot.** Megnyílik az adatbázishoz csatlakoztatott új lekérdezési ablak.

2. A lekérdezésablakban illessze be a következő SQL-lekérdezést:

   ```sql
   SELECT pc.Name as CategoryName, p.name as ProductName
   FROM [SalesLT].[ProductCategory] pc
   JOIN [SalesLT].[Product] p
   ON pc.productcategoryid = p.productcategoryid;
   ```

3. Az eszköztáron válassza a **Végrehajtás** lehetőséget a `Product` lekérdezés `ProductCategory` futtatásához és az adatok és táblákból való beolvasásához.

    ![lekérdezés az adatok beolvasásához a Product és a ProductCategory táblából](./media/sql-database-connect-query-ssms/query2.png)

### <a name="insert-data"></a>Adat beszúrása

Futtassa ezt [az INSERT](https://msdn.microsoft.com/library/ms174335.aspx) Transact-SQL `SalesLT.Product` kódot, hogy új terméket hozzon létre a táblában.

1. Cserélje le az előző lekérdezést erre.

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

2. Új **Execute** sor beszúrásához válassza `Product` a Végrehajtás lehetőséget. Megjelenik **az Üzenetek** ablaktábla **(az 1 sort érintve).**

#### <a name="view-the-result"></a>Az eredmény megtekintése

1. Cserélje le az előző lekérdezést erre.

   ```sql
   SELECT * FROM [SalesLT].[Product]
   WHERE Name='myNewProduct'
   ```

2. Válassza a **Végrehajtás** lehetőséget. A következő eredmény jelenik meg.

   ![Terméktábla-lekérdezés eredménye](./media/sql-database-connect-query-ssms/result.png)

### <a name="update-data"></a>Adatok frissítése

Futtassa ezt [az UPDATE](https://msdn.microsoft.com/library/ms177523.aspx) Transact-SQL kódot az új termék módosításához.

1. Cserélje le az előző lekérdezést erre, amely a korábban létrehozott új rekordot adja vissza:

   ```sql
   UPDATE [SalesLT].[Product]
   SET [ListPrice] = 125
   WHERE Name = 'myNewProduct';
   ```

2. A **tábla** megadott sorának `Product` frissítéséhez válassza a Végrehajtás lehetőséget. Megjelenik **az Üzenetek** ablaktábla **(az 1 sort érintve).**

### <a name="delete-data"></a>Adat törlése

Futtassa ezt [a DELETE](https://msdn.microsoft.com/library/ms189835.aspx) Transact-SQL kódot az új termék eltávolításához.

1. Cserélje le az előző lekérdezést erre.

   ```sql
   DELETE FROM [SalesLT].[Product]
   WHERE Name = 'myNewProduct';
   ```

2. A **táblázat** megadott sorának `Product` törléséhez válassza a Végrehajtás lehetőséget. Megjelenik **az Üzenetek** ablaktábla **(az 1 sort érintve).**

## <a name="next-steps"></a>További lépések

- Az SSMS szolgáltatásról az [SQL Server Management Studio című témakörben talál](https://msdn.microsoft.com/library/ms174173.aspx)további információt.
- Ha az Azure Portalon szeretne csatlakozni és kérdezni, olvassa el [a Csatlakozás és lekérdezés az Azure Portal SQL Query szerkesztőjével című témakört.](sql-database-connect-query-portal.md)
- A Visual Studio Code használatával történő csatlakozásról és lekérdezésről lásd a [Visual Studio Code használatával végzett csatlakozásról és lekérdezésről](sql-database-connect-query-vscode.md) szóló témakört.
- A .NET használatával történő csatlakozásról és lekérdezésről lásd a [.NET használatával végzett csatlakozásról és lekérdezésről](sql-database-connect-query-dotnet.md) szóló témakört.
- A PHP-vel történő csatlakozásról és lekérdezésről lásd a [PHP-vel végzett csatlakozásról és lekérdezésről](sql-database-connect-query-php.md) szóló témakört.
- A Node.js használatával történő csatlakozásról és lekérdezésről lásd a [Node.js használatával végzett csatlakozásról és lekérdezésről](sql-database-connect-query-nodejs.md) szóló témakört.
- A Java használatával történő csatlakozásról és lekérdezésről lásd a [Java használatával végzett csatlakozásról és lekérdezésről](sql-database-connect-query-java.md) szóló témakört.
- A Python használatával történő csatlakozásról és lekérdezésről lásd a [Python használatával végzett csatlakozásról és lekérdezésről](sql-database-connect-query-python.md) szóló témakört.
- A Ruby használatával történő csatlakozásról és lekérdezésről lásd a [Ruby használatával végzett csatlakozásról és lekérdezésről](sql-database-connect-query-ruby.md) szóló témakört.
