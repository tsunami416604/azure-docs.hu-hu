---
title: 'SSMS: Csatlakozás és adatlekérdezés az Azure SQL Database |} A Microsoft Docs'
description: Ebből a cikkből megtudhatja, hogyan csatlakozhat az SQL Database-hez az Azure-ban az SQL Server Management Studio (SSMS) használatával. Ezután futtasson Transact-SQL (T-SQL) utasításokat az adatok lekérdezéséhez és szerkesztéséhez.
keywords: csatlakozás sql database-hez,sql server management studio
services: sql-database
ms.service: sql-database
ms.subservice: ''
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 12/04/2018
ms.openlocfilehash: 7049691e6635940e70795f0b5b4cc4c94cfd3631
ms.sourcegitcommit: 4eeeb520acf8b2419bcc73d8fcc81a075b81663a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/19/2018
ms.locfileid: "53607319"
---
# <a name="quickstart-use-sql-server-management-studio-to-connect-and-query-an-azure-sql-database"></a>Gyors útmutató: SQL Server Management Studio használatával csatlakozhat, és az Azure SQL Database-adatbázis lekérdezéséhez

Használhat [SQL Server Management Studio] [ ssms-install-latest-84g] (SSMS) bármely SQL-infrastruktúra kezelésére az SQL Servertől az SQL Database a Microsoft Windows. Ez a rövid útmutató bemutatja, hogyan csatlakozhat egy Azure SQL database, majd futtassa a Transact-SQL-utasítások használatával lekérdezni, beszúrni, frissíteni és törli az adatokat az SSMS használatával. 

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez a következőkre lesz szüksége:

[!INCLUDE [prerequisites-create-db](../../includes/sql-database-connect-query-prerequisites-create-db-includes.md)]

* Egy konfigurált kiszolgálószintű tűzfalszabályt. További információkért lásd: [kiszolgálószintű tűzfalszabály létrehozása](sql-database-get-started-portal-firewall.md).

#### <a name="install-the-latest-ssms"></a>Az SSMS legújabb verziójának telepítése

Mielőtt elkezdené, győződjön meg arról, hogy telepítette a legújabb [SSMS][ssms-install-latest-84g]. 

## <a name="sql-server-connection-information"></a>Az SQL-kiszolgáló kapcsolatadatai

[!INCLUDE [prerequisites-server-connection-info](../../includes/sql-database-connect-query-prerequisites-server-connection-info-includes.md)]

## <a name="connect-to-your-database"></a>Csatlakozás az adatbázishoz

Az SMSS csatlakozzon az Azure SQL Database-kiszolgálóhoz. 

> [!IMPORTANT]
> Egy Azure SQL Database logikai kiszolgáló figyel az 1433-as porton. A céges tűzfal mögül logikai kiszolgálóhoz csatlakozik, a tűzfal nyissa meg ezt a portot kell rendelkeznie.
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

   ![adatbázis-objektumok megtekintése](./media/sql-database-connect-query-ssms/connected.png)  

## <a name="query-data"></a>Adatok lekérdezése

Használja a következő [kiválasztása](https://msdn.microsoft.com/library/ms189499.aspx) Transact-SQL kód használatával lekérdezheti kategóriánként az első 20 terméket.

1. Az Object Explorerben kattintson a jobb gombbal **mySampleDatabase** válassza **új lekérdezés**. Megnyílik egy, az adatbázishoz csatlakoztatott üres lekérdezési ablak.

1. A lekérdezési ablakban illessze be az SQL-lekérdezést.

   ```sql
   SELECT pc.Name as CategoryName, p.name as ProductName
   FROM [SalesLT].[ProductCategory] pc
   JOIN [SalesLT].[Product] p
   ON pc.productcategoryid = p.productcategoryid;
   ```

3. Válassza az eszköztár **Execute** be adatokat a `Product` és `ProductCategory` táblákat.

    ![2 táblákból származó adatok beolvasására irányuló lekérdezés](./media/sql-database-connect-query-ssms/query2.png)

## <a name="insert-data"></a>Adat beszúrása

Használja a következő [BESZÚRÁSA](https://msdn.microsoft.com/library/ms174335.aspx) hozhat létre egy új terméket a Transact-SQL-kódot a `SalesLT.Product` tábla.

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

2. Válassza ki **Execute** , a Product tábla egy új sor beszúrásához. A **üzenetek** ablaktáblán megjelennek azok **(1 sort érint)**.

## <a name="view-the-result"></a>Az eredmény megtekintéséhez

1. Ehhez cserélje le az előző lekérdezést.

   ```sql
   SELECT * FROM [SalesLT].[Product] 
   WHERE Name='myNewProduct' 

2. Select **Execute**. The following result appears. 

   ![result](./media/sql-database-connect-query-ssms/result.png)

 
## Update data

Use the following [UPDATE](https://msdn.microsoft.com/library/ms177523.aspx) Transact-SQL code to modify the new product you just added.

1. Replace the previous query with this one.

   ```sql
   UPDATE [SalesLT].[Product]
   SET [ListPrice] = 125
   WHERE Name = 'myNewProduct';
   ```

2. Válassza ki **Execute** a Product tábla egy megadott sorának frissítéséhez. A **üzenetek** ablaktáblán megjelennek azok **(1 sort érint)**.

## <a name="delete-data"></a>Adat törlése

Használja a következő [törlése](https://msdn.microsoft.com/library/ms189835.aspx) Transact-SQL-kód eltávolítása az előzőleg hozzáadott új terméket.

1. Ehhez cserélje le az előző lekérdezést.

   ```sql
   DELETE FROM [SalesLT].[Product]
   WHERE Name = 'myNewProduct';
   ```

2. Válassza ki **Execute** a Product tábla egy megadott sorának törléséhez. A **üzenetek** ablaktáblán megjelennek azok **(1 sort érint)**.

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

