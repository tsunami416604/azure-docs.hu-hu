---
title: "SSMS: Csatlakozás és adatok lekérdezése Azure SQL Database adatbázisokból | Microsoft Docs"
description: "Ebből a cikkből megtudhatja, hogyan csatlakozhat az SQL Database-hez az Azure-ban az SQL Server Management Studio (SSMS) használatával. Ezután futtasson Transact-SQL (T-SQL) utasításokat az adatok lekérdezéséhez és szerkesztéséhez."
metacanonical: 
keywords: "csatlakozás sql database-hez,sql server management studio"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 7cd2a114-c13c-4ace-9088-97bd9d68de12
ms.service: sql-database
ms.custom: development
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 03/15/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: 9f149c3959f1b249a15f2c2714d12c7c9be94bbb
ms.lasthandoff: 03/21/2017


---
# <a name="azure-sql-database-use-sql-server-management-studio-to-connect-and-query-data"></a>Azure SQL Database: Az SQL Server Management Studio segítségével csatlakozhat és kérdezhet le adatokat

Használja az [SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx) (SSMS) szoftvert az SQL Server-erőforrások felhasználói felületről vagy szkriptekkel történő létrehozásához és kezeléséhez. Ez az útmutató ismerteti, hogyan használható az SSMS az Azure SQL Database adatbázishoz való kapcsolódásra, majd lekérdezési, beszúrási, frissítési és törlési utasítások végrehajtására.

Ez a rövid útmutató az alábbi rövid útmutatók egyikében létrehozott erőforrásokat használja kiindulási pontnak:

- [DB létrehozása – portál](sql-database-get-started-portal.md)
- [DB létrehozása – CLI](sql-database-get-started-cli.md)
- [DB létrehozása – PowerShell](sql-database-get-started-powershell.md) 

A kezdés előtt győződjön meg arról, hogy az [SSMS](https://msdn.microsoft.com/library/mt238290.aspx) legújabb verziója van telepítve. 

## <a name="get-connection-information"></a>Kapcsolatadatok lekérése

Kérje le az Azure SQL Database kiszolgáló teljes kiszolgálónevét az Azure Portalon. Használja a teljes kiszolgálónevet az SQL Server Management Studióban a kiszolgálóhoz történő csatlakozáshoz.

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com/).
2. Válassza az **SQL-adatbázisok** elemet a bal oldali menüben, majd kattintson az új adatbázisra az **SQL-adatbázisok** oldalon. 
3. Az Azure Portalon az adatbázishoz tartozó lap **Alapvető erőforrások** ablaktábláján keresse meg, majd másolja ki a **Kiszolgáló nevét**.

    <img src="./media/sql-database-connect-query-ssms/connection-information.png" alt="connection information" style="width: 780px;" />

## <a name="connect-to-the-server"></a>Csatlakozás a kiszolgálóhoz

Az SQL Server Management Studióban építse fel a kapcsolatot az Azure SQL Database kiszolgálóval.

1. Írja be a Windows keresőmezőbe az **SSMS** kifejezést, majd nyomja le az **Enter** billentyűt az SSMS megnyitásához.

2. A **Connect to Server** (Kapcsolódás a kiszolgálóhoz) párbeszédpanelen adja meg a következő adatokat:
   - **Server type** (Kiszolgáló típusa): Adja meg az adatbázismotort
   - **Server name** (Kiszolgáló neve): Írja be a teljes kiszolgálónevet, például **mynewserver20170313.database.windows.net**
   - **Authentication** (Hitelesítés): Válassza az SQL Server Authentication (SQL Server-hitelesítés) beállítást
   - **Login** (Bejelentkezés): Adja meg a kiszolgálói rendszergazdai fiókot
   - **Password** (Jelszó): Adja meg a kiszolgálói rendszergazdai fiók jelszavát
 
    <img src="./media/sql-database-connect-query-ssms/connect.png" alt="connect to server" style="width: 780px;" />

3. Kattintson a **Connect** (Csatlakozás) gombra. Megnyílik az Object Explorer ablak az SSMS-ben. 

    <img src="./media/sql-database-connect-query-ssms/connected.png" alt="connected to server" style="width: 780px;" />

4. Az Object Explorerben bontsa ki a **Database** (Adatbázisok), majd a **mySampleDatabese** csomópontot a mintaadatbázisban található objektumok megtekintéséhez.

## <a name="query-data"></a>Adatok lekérdezése

Használja a [SELECT](https://msdn.microsoft.com/library/ms189499.aspx) Transact-SQL utasítást az Azure SQL-adatbázis adatainak lekérdezéséhez.

1. Az Object Explorerben kattintson a jobb gombbal a **mySampleDatabase** adatbázisra, majd kattintson a **New Query** (Új lekérdezés) elemre. Megnyílik egy, az adatbázishoz csatlakoztatott üres lekérdezési ablak.
2. A lekérdezési ablakban írja be a következő lekérdezést a lekérdezésablakba:

   ```sql
   SELECT pc.Name as CategoryName, p.name as ProductName
   FROM [SalesLT].[ProductCategory] pc
   JOIN [SalesLT].[Product] p
   ON pc.productcategoryid = p.productcategoryid;
   ```

3. Az eszköztáron kattintson az **Execute** (Végrehajtás) elemre a Product és ProductCategory táblák adatainak lekérdezéséhez.

    <img src="./media/sql-database-connect-query-ssms/query.png" alt="query" style="width: 780px;" />

## <a name="insert-data"></a>Adat beszúrása

Használja az [INSERT](https://msdn.microsoft.com/library/ms174335.aspx) Transact-SQL utasítást az adatok Azure SQL-adatbázisba történő beszúrására.

1. Kattintson az eszköztáron a **New Query** (Új lekérdezés) elemre. Megnyílik egy, az adatbázishoz csatlakoztatott üres lekérdezési ablak.
2. A lekérdezési ablakban írja be a következő lekérdezést a lekérdezésablakba:

   ```sql
   INSERT INTO [SalesLT].[Product]
           ( [Name]
           , [ProductNumber]
           , [Color]
           , [ProductCategoryID]
           , [StandardCost]
           , [ListPrice]
           , [SellStartDate]
           )
     VALUES
           ('myNewProduct'
           ,123456789
           ,'NewColor'
           ,1
           ,100
           ,100
           ,GETDATE() );
   ```

3. Az eszköztáron kattintson az **Execute** (Végrehajtás) elemre az új sor Product táblába történő beszúrásához.

    <img src="./media/sql-database-connect-query-ssms/insert.png" alt="insert" style="width: 780px;" />

## <a name="update-data"></a>Adatok frissítése

Használja az [UPDATE](https://msdn.microsoft.com/library/ms177523.aspx) Transact-SQL utasítást az Azure SQL-adatbázis adatainak frissítésére.

1. Kattintson az eszköztáron a **New Query** (Új lekérdezés) elemre. Megnyílik egy, az adatbázishoz csatlakoztatott üres lekérdezési ablak.
2. A lekérdezési ablakban írja be a következő lekérdezést a lekérdezésablakba:

   ```sql
   UPDATE [SalesLT].[Product]
   SET [ListPrice] = 125
   WHERE Name = 'myNewProduct';
   ```

3. Az eszköztáron kattintson az **Execute** (Végrehajtás) elemre a Product tábla egy megadott sorának frissítéséhez.

    <img src="./media/sql-database-connect-query-ssms/update.png" alt="update" style="width: 780px;" />

## <a name="delete-data"></a>Adat törlése

Használja az [DELETE](https://msdn.microsoft.com/library/ms189835.aspx) Transact-SQL utasítást az Azure SQL-adatbázis adatainak törlésére.

1. Kattintson az eszköztáron a **New Query** (Új lekérdezés) elemre. Megnyílik egy, az adatbázishoz csatlakoztatott üres lekérdezési ablak.
2. A lekérdezési ablakban írja be a következő lekérdezést a lekérdezésablakba:

   ```sql
   DELETE FROM [SalesLT].[Product]
   WHERE Name = 'myNewProduct';
   ```

3. Az eszköztáron kattintson az **Execute** (Végrehajtás) elemre a Product tábla egy megadott sorának törléséhez.

    <img src="./media/sql-database-connect-query-ssms/delete.png" alt="delete" style="width: 780px;" />

## <a name="next-steps"></a>Következő lépések

- Az SSMS eszközről további információt [az SQL Server Management Studio használatát ismertető cikkben talál](https://msdn.microsoft.com/library/ms174173.aspx).
- A Visual Studio Code-ból végzett lekérdezésről és adatszerkesztés a [Visual Studio Code-ot](https://code.visualstudio.com/docs) leíró cikkben olvashat

