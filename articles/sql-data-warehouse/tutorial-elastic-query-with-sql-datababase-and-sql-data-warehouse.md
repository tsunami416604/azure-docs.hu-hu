---
title: 'Oktatóanyag: Az Azure SQL Data Warehouse rugalmas lekérdezés |} A Microsoft Docs'
description: Ebben az oktatóanyagban egy Azure SQL Database Elastic Query szolgáltatását használja Azure SQL Data Warehouse lekérdezéséhez.
services: sql-data-warehouse
author: hirokib
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: implement
ms.date: 04/14/2018
ms.author: elbutter
ms.reviewer: igorstan
ms.openlocfilehash: b1ac2edd39ac2e5a765eaf6223ba01c9f9e5df91
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/30/2019
ms.locfileid: "55238337"
---
# <a name="tutorial-use-elastic-query-to-access-data-in-azure-sql-data-warehouse-from-azure-sql-database"></a>Oktatóanyag: Hozzáférés az adatokhoz az Azure SQL Data Warehouse egy Azure SQL Database rugalmas lekérdezés használatával

Ebben az oktatóanyagban egy Azure SQL Database Elastic Query szolgáltatását használja Azure SQL Data Warehouse lekérdezéséhez. 

## <a name="prerequisites-for-the-tutorial"></a>Az oktatóanyag előfeltételei

Az oktatóanyag elkezdéséhez az alábbi előfeltételekkel kell rendelkeznie:

1. Installed SQL Server Management Studio (SSMS).
2. Létrehozott egy Azure SQL server egy belül a kiszolgáló adatbázisába és adatraktárába.
3. Az Azure SQL-kiszolgáló eléréséhez szükséges tűzfalszabályok beállítása.

## <a name="set-up-connection-between-sql-data-warehouse-and-sql-database-instances"></a>Az SQL Data Warehouse és az SQL Database-példányok közötti kapcsolat beállítása 

1. SSMS vagy más lekérdezésügyfél használatával nyisson meg egy új lekérdezést a adatbázis **fő** a logikai kiszolgálón.

2. Hozzon létre bejelentkezést és felhasználót, hogy az SQL database data warehouse kapcsolatot jelöli.

   ```sql
   CREATE LOGIN SalesDBLogin WITH PASSWORD = 'aReallyStrongPassword!@#';
   ```

3. SSMS vagy más lekérdezésügyfél használatával nyisson meg egy új lekérdezést a **SQL data warehouse-példányhoz** a logikai kiszolgálón.

4. Felhasználó létrehozása az adattárházpéldányt a 2. lépésben létrehozott bejelentkezéssel

   ```sql
   CREATE USER SalesDBUser FOR LOGIN SalesDBLogin;
   ```

5. Engedélyek megadása a felhasználónak, amely az SQL Database szeretné végrehajtani kívánt 4. lépését. Ebben a példában csak folyamatban van az engedélyt válassza ki az egy adott séma, így bemutatja, hogyan hogy előfordulhat, hogy korlátozni a lekérdezések az SQL database-ből egy adott tartományhoz. 

   ```sql
   GRANT SELECT ON SCHEMA :: [dbo] TO SalesDBUser;
   ```

6. SSMS vagy más lekérdezésügyfél használatával nyisson meg egy új lekérdezést a **SQL database-példány** a logikai kiszolgálón.

7. Hozzon létre egy főkulcsot, ha Ön még nem rendelkezik egy. 

   ```sql
   CREATE MASTER KEY; 
   ```

8. Hozzon létre egy adatbázishoz kötődő hitelesítő adatok a 2. lépésben létrehozott hitelesítő adatok használatával.

   ```sql
   CREATE DATABASE SCOPED CREDENTIAL SalesDBElasticCredential
   WITH IDENTITY = 'SalesDBLogin',
   SECRET = 'aReallyStrongPassword@#!';
   ```

9. Hozzon létre egy külső adatforrást, amely a data warehouse-példányhoz.

   ```sql
   CREATE EXTERNAL DATA SOURCE EnterpriseDwSrc WITH 
       (TYPE = RDBMS, 
       LOCATION = '<SERVER NAME>.database.windows.net', 
       DATABASE_NAME = '<SQL DATA WAREHOUSE NAME>', 
       CREDENTIAL = SalesDBElasticCredential, 
   ) ;
   ```

10. Most már létrehozhatja a külső táblákra hivatkozó külső adatforrás. Az adatraktár példánya feldolgozni és küld vissza az adatbázispéldányt küldi a lekérdezéseket a táblázatok használatával.


## <a name="elastic-query-from-sql-database-to-sql-data-warehouse"></a>Az SQL data warehouse az SQL database rugalmas lekérdezése

A következő néhány lépésben hozunk létre egy táblát a data warehouse-példányhoz több értékekkel. Ezután bemutatjuk, hogyan állítható be a külső tábla lekérdezése az adattárházpéldányt a database-példány.

1. SSMS vagy más lekérdezésügyfél használatával nyisson meg egy új lekérdezést a **SQL Data Warehouse** a logikai kiszolgálón.

2. Küldje el a következő lekérdezést, hozzon létre egy **OrdersInformation** táblájában az adattárház-példányt.

   ```sql
   CREATE TABLE [dbo].[OrderInformation]
   ( 
       [OrderID] [int] NOT NULL 
   ,   [CustomerID] [int] NOT NULL 
   ) 
   INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (123, 1) 
   INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (149, 2) 
   INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (857, 2) 
   INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (321, 1) 
   INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (564, 8)
   ```

3. SSMS vagy más lekérdezésügyfél használatával nyisson meg egy új lekérdezést a **SQL-adatbázis** a logikai kiszolgálón.

4. Küldje el a következő lekérdezést, amely a külső tábla definíció létrehozása a **OrdersInformation** táblájában a data warehouse-példányhoz.

   ```sql
   CREATE EXTERNAL TABLE [dbo].[OrderInformation]
   ( 
       [OrderID] [int] NOT NULL
   ,   [CustomerID] [int] NOT NULL 
   ) 
   WITH 
   (
        DATA_SOURCE = EnterpriseDwSrc
   ,    SCHEMA_NAME = N'dbo'
   ,    OBJECT_NAME = N'OrderInformation'
   )
   ```

5. Figyelje meg, hogy most már rendelkezik egy külső tábla definíciójában a **SQL database-példány**.

   ![Rugalmas lekérdezés külső tábla definíciójának](media/sql-data-warehouse-elastic-query-with-sql-database/elastic-query-external-table.png)


6. Küldje el a következő lekérdezést, amely lekérdezi a data warehouse-példányhoz. A 2. lépésben beszúrt öt értékeket kell kapnia. 

```sql
SELECT * FROM [dbo].[OrderInformation];
```

> [!NOTE]
>
> Figyelje meg, hogy annak ellenére, hogy néhány értékek, ez a lekérdezés vissza hosszabb időt vesz igénybe. Rugalmas lekérdezés használatával a data warehouse-ba, amikor a hálózaton keresztül kell figyelembe venni a lekérdezés-feldolgozás és az adatátviteli költségek. Rugalmas lekérdezés távoli végrehajtás használatára, ha a számítási teljesítményt, nem a késés, a prioritás.

Gratulálunk, az meg van adva a rugalmas lekérdezés nagyon alapjait. 

## <a name="next-steps"></a>További lépések
További javaslatok: [ajánlott eljárások az Azure SQL Data Warehouse rugalmas lekérdezés használatával](how-to-use-elastic-query-with-sql-data-warehouse.md).
