---
title: "Rugalmas lekérdezési oktatóanyag az Azure SQL Data Warehouse szolgáltatással |} Microsoft Docs"
description: "Rugalmas lekérdezés használata az Azure SQL Data Warehouse szolgáltatással"
services: sql-data-warehouse
documentationcenter: NA
author: hirokib
manager: johnmac
editor: 
ms.assetid: e2dc8f3f-10e3-4589-a4e2-50c67dfcf67g
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: integrate
ms.date: 11/03/2017
ms.author: elbutter
ms.openlocfilehash: a13b81213b7a47cb7209bc914f514fa10aede5c4
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2017
---
# <a name="configure-elastic-query-with-sql-data-warehouse"></a>Rugalmas lekérdezési konfigurálja az SQL Data Warehouse szolgáltatással

Ebben az oktatóanyagban elsajátíthatja rugalmas lekérdezés használata küldje el a lekérdezést az SQL-adatbázis az SQL Data Warehouse-lesz. Rugalmas lekérdezési funkciók Azure SQL termékek között már. Egy fogalom, rugalmas lekérdezési kapcsolatos további információkért lásd: [ **rugalmas lekérdezés használata az SQL Data Warehouse**][How to use Elastic Query with SQL Data Warehouse].

## <a name="prerequisites-for-the-tutorial"></a>Az oktatóanyag előfeltételei

Az oktatóanyag elkezdéséhez rendelkeznie kell a következő előfeltételek teljesülését:

1. Telepített SQL Server Management Studio (SSMS).
2. Hozza létre az Azure SQL-kiszolgáló egy adatbázisról és az adatraktárról belül ehhez a kiszolgálóhoz.
3. Az Azure SQL-kiszolgáló eléréséhez tűzfalszabályok beállítása.

## <a name="set-up-connection-between-sql-data-warehouse-and-sql-database-instances"></a>Az SQL Data Warehouse és SQL Database-példányok közötti kapcsolat 

1. SSMS vagy egy másik lekérdezés ügyfél, nyissa meg egy új adatbázis-lekérdezést **fő** a logikai kiszolgálón.

2. A bejelentkezési és a felhasználói adatok adatraktár kapcsolatnak az SQL-adatbázis jelölő létrehozása.

  ```sql
  CREATE LOGIN SalesDBLogin WITH PASSWORD = 'aReallyStrongPassword!@#';
  ```

3. Az új lekérdezés SSMS vagy egy másik lekérdezés ügyfél, nyissa meg a **SQL data warehouse példány** a logikai kiszolgálón.

4. Az adatraktár példánya a 2. lépésben létrehozott bejelentkezéskor a felhasználó létrehozása

   ```sql
   CREATE USER SalesDBUser FOR LOGIN SalesDBLogin;
   ```

5. A hozzáférési jogot a felhasználó szeretné az SQL-adatbázisban szeretné végrehajtani, 4. lépését. Ebben a példában engedély csak nyújtott válassza egy adott séma, hogyan azt korlátozhatják lekérdezések az SQL-adatbázis a megadott tartomány ábrázoló. 

   ```sql
   GRANT SELECT ON SCHEMA :: [dbo] TO SalesDBUser;
   ```

6. Az új lekérdezés SSMS vagy egy másik lekérdezés ügyfél, nyissa meg a **SQL database-példányt** a logikai kiszolgálón.

7. Hozzon létre egy főkulcsot, ha még nem rendelkezik egy. 

   ```sql
   CREATE MASTER KEY; 
   ```

8. 2. lépésben létrehozott hitelesítő adatok használatával adatbázishoz kötődő hitelesítő adatok létrehozása.

   ```sql
   CREATE DATABASE SCOPED CREDENTIAL SalesDBElasticCredential
   WITH IDENTITY = 'SalesDBLogin',
   SECRET = 'aReallyStrongPassword@#!';
   ```

9. Hozzon létre egy külső adatforrásból mutat, az adatraktár példánya.

  ```sql
  CREATE EXTERNAL DATA SOURCE EnterpriseDwSrc WITH 
      (TYPE = RDBMS, 
      LOCATION = '<SERVER NAME>.database.windows.net', 
      DATABASE_NAME = '<SQL DATA WAREHOUSE NAME>', 
      CREDENTIAL = SalesDBElasticCredential, 
  ) ;
  ```

10. Mostantól létrehozhat külső táblákra hivatkozó a külső adatforráshoz. Azokat a táblákat használata lekérdezések kerülnek az adatraktár példánya feldolgozni és küldi vissza az adatbázispéldány fölött.


## <a name="elastic-query-from-sql-database-to-sql-data-warehouse"></a>Az SQL data warehouse rugalmas SQL-adatbázis lekérdezése

A következő néhány lépésben leírtak létre fogunk hozni egy táblát az az adatraktár példánya a több értékkel rendelkező. Fog majd bemutatjuk, hogyan állíthatja be a külső tábla lekérdezése az adatraktár példánya a az adatbázispéldány fölött.

1. Az új lekérdezés SSMS vagy egy másik lekérdezés ügyfél, nyissa meg a **SQL Data Warehouse** a logikai kiszolgálón.

2. Küldje el a következő lekérdezés futtatásával hozzon létre egy **OrdersInformation** a az adatraktár példánya táblájában.

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

3. Az új lekérdezés SSMS vagy egy másik lekérdezés ügyfél, nyissa meg a **SQL-adatbázis** a logikai kiszolgálón.

4. Küldje el a következő lekérdezés futtatásával hozzon létre egy külső tábla definíciójának mutat, a **OrdersInformation** az adatraktár példánya táblájában.

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

5. Figyelje meg, hogy most már rendelkezik egy külső tábla definíciójában a **SQL database-példányt**.

   ![Rugalmas lekérdezési külső tábla definíciójában](./media/sql-data-warehouse-elastic-query-with-sql-database/elastic-query-external-table.png)


6. Küldje el a következő lekérdezést, amely lekérdezi az adatraktár példánya. A 2. lépésben beszúrt öt értékeket kell kapnia. 

```sql
SELECT * FROM [dbo].[OrderInformation];
```

> [!NOTE]
>
> Figyelje meg, hogy annak ellenére, hogy néhány érték, ez a lekérdezés vissza jelentős időt vesz igénybe. Rugalmas lekérdezéssel adatraktárban, egy gondolja át az általános költségek adatátviteli és a lekérdezés feldolgozása a hálózaton keresztül. Rugalmas lekérdezés távoli végrehajtás használatára, ha a számítási teljesítményt, nem várakozási ideje, a prioritás.

Gratulálunk, állította be a rugalmas lekérdezés nagyon alapjait. 




<!--Image references-->

<!--Article references-->

[How to use Elastic Query with SQL Data Warehouse]: ./how-to-use-elastic-query-with-sql-data-warehouse.md

<!--MSDN references-->

<!--Other Web references-->