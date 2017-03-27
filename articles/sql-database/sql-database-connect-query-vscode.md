---
title: "VS Code: Csatlakozás és adatok lekérdezése Azure SQL Database-adatbázisokból | Microsoft Docs"
description: "Ebből a cikkből megtudhatja, hogyan csatlakozhat az SQL Database-hez az Azure-ban a Visual Studio Code segítségével. Ezután futtasson Transact-SQL (T-SQL) utasításokat az adatok lekérdezéséhez és szerkesztéséhez."
metacanonical: 
keywords: "csatlakozás SQL Database-adatbázishoz"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 676bd799-a571-4bb8-848b-fb1720007866
ms.service: sql-database
ms.custom: manage
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 03/17/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: 9192ed7b69e52c59efe33981e1e557b634679196
ms.lasthandoff: 03/21/2017


---
# <a name="azure-sql-database-use-visual-studio-code-to-connect-and-query-data"></a>Azure SQL Database: Csatlakozás, majd adatok lekérdezése a Visual Studio Code használatával

A [Visual Studio Code](https://code.visualstudio.com/docs) egy grafikus kódszerkesztő a kiterjesztéseket támogató Linux, macOS és Windows rendszerekre. A Visual Studio Code-ot az [mssql bővítménnyel](https://aka.ms/mssql-marketplace) használva csatlakozhat egy Azure SQL Database-adatbázishoz, és lekérdezéseket hajthat végre. Ez az útmutató ismerteti, hogyan használható a Visual Studio Code az Azure SQL Database-adatbázishoz való csatlakozáshoz, majd lekérdezési, beszúrási, frissítési és törlési utasítások végrehajtására.

Ez a rövid útmutató az alábbi rövid útmutatók egyikében létrehozott erőforrásokat használja kiindulási pontnak:

- [DB létrehozása – portál](sql-database-get-started-portal.md)
- [DB létrehozása – CLI](sql-database-get-started-cli.md)
- [DB létrehozása – PowerShell](sql-database-get-started-powershell.md) 

A kezdés előtt győződjön meg arról, hogy a [Visual Studio Code](https://code.visualstudio.com/Download) legújabb verziója van telepítve, és be van töltve az [mssql bővítmény](https://aka.ms/mssql-marketplace). Az mssql bővítmény telepítési lépéseinek megismeréséhez olvassa el[a VS Code telepítését](https://docs.microsoft.com/sql/linux/sql-server-linux-develop-use-vscode#install-vs-code) ismertető cikket. 

## <a name="get-connection-information"></a>Kapcsolatadatok lekérése

Kérje le az Azure SQL Database kiszolgáló teljes kiszolgálónevét az Azure Portalon. A teljes kiszolgálónévvel csatlakozhat a kiszolgálóhoz a Visual Studio Code-ot használva.

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com/).
2. Válassza az **SQL-adatbázisok** elemet a bal oldali menüben, majd kattintson az új adatbázisra az **SQL-adatbázisok** oldalon. 
3. Az Azure Portalon az adatbázishoz tartozó lap **Alapvető erőforrások** ablaktábláján keresse meg, majd másolja ki a **Kiszolgáló nevét**, amelyet majd később használ fel ebben a gyors üzembe helyezésben.

    <img src="./media/sql-database-connect-query-ssms/connection-information.png" alt="connection information" style="width: 780px;" />

## <a name="set-language-mode-to-sql"></a>A nyelvmód SQL értékre állítása

Az mssql-parancsok és a T-SQL IntelliSense engedélyezéséhez ügyeljen arra, hogy a nyelvmód beállítása **SQL** legyen a Visual Studio Code-ban.

1. Nyisson meg egy új Visual Studio Code-ablakot. 

2. A nyelvmód SQL értékre állításához nyomja le a **CTRL+K,M** billentyűkombinációt, írja be az **SQL** kifejezést, majd nyomja le az **ENTER** billentyűt. 

<img src="./media/sql-database-connect-query-vscode/vscode-language-mode.png" alt="SQL language mode" style="width: 780px;" />

## <a name="connect-to-the-server"></a>Csatlakozás a kiszolgálóhoz

A Visual Studio Code segítségével kapcsolatot hozhat létre az Azure SQL Database-kiszolgálóval.

1. A VS Code-ban nyomja le a **CTRL+SHIFT+P** billentyűkombinációt (vagy az **F1** billentyűt) a parancskatalógus megnyitásához.

2. Írja be az **sqlcon** parancsot, és nyomja le az **ENTER** billentyűt.

3. A nyelv **SQL**-re állításához kattintson az **Igen** gombra.

4. A **Kapcsolati profil létrehozása** kiválasztásához nyomja le az **ENTER** billentyűt. Ezzel létrehoz egy kapcsolati profilt az SQL Server-példányhoz.

5. Az útmutatást követve adja meg az új kapcsolati profil kapcsolati tulajdonságait. Az egyes értékek kiválasztása után a folytatáshoz nyomja le az **ENTER** billentyűt. 

   A következő táblázat ismerteti a kapcsolati profil tulajdonságait.

   | Beállítás | Leírás |
   |-----|-----|
   | **Kiszolgálónév** | Írja be a teljes kiszolgálónevet, például: **mynewserver20170313.database.windows.net** |
   | **Adatbázis neve** | Írja be az adatbázisnevet, például: **mySampleDatabase** |
   | **Hitelesítés** | Válassza ki az SQL-bejelentkezést |
   | **Felhasználónév** | Adja meg a kiszolgálói rendszergazdai fiók nevét |
   | **Jelszó (SQL-bejelentkezés)** | Adja meg a kiszolgálói rendszergazdai fiók jelszavát | 
   | **Menti a jelszót?** | Válassza az **Igen** vagy a **Nem** lehetőséget. |
   | **[Opcionális] A profil elnevezése** | Adjon egy nevet a kapcsolati profilnak, például: **mySampleDatabase**. 

6. Az **ESC** billentyűt lenyomva zárja be a profil létrehozásáról és csatlakoztatásáról tájékoztató üzenetet.

7. Ellenőrizze a kapcsolatot az állapotsávon.

   <img src="./media/sql-database-connect-query-vscode/vscode-connection-status.png" alt="Connection status" style="width: 780px;" />

## <a name="query-data"></a>Adatok lekérdezése

Használja a [SELECT](https://msdn.microsoft.com/library/ms189499.aspx) Transact-SQL utasítást az Azure SQL-adatbázis adatainak lekérdezéséhez.

1. A **szerkesztő** ablakában írja be a következő lekérdezést az üres lekérdezési ablakba:

   ```sql
   SELECT pc.Name as CategoryName, p.name as ProductName
   FROM [SalesLT].[ProductCategory] pc
   JOIN [SalesLT].[Product] p
   ON pc.productcategoryid = p.productcategoryid;
   ```

3. A **CTRL+SHIFT+E** billentyűkombinációt lenyomva lekérheti az adatokat a Product és ProductCategory táblából.

    <img src="./media/sql-database-connect-query-vscode/query.png" alt="Query" style="width: 780px;" />

## <a name="insert-data"></a>Adat beszúrása

Használja az [INSERT](https://msdn.microsoft.com/library/ms174335.aspx) Transact-SQL utasítást az adatok Azure SQL-adatbázisba történő beszúrására.

1. A **szerkesztő** ablakában törölje az előző lekérdezést, és adja meg a következő lekérdezést:

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

3. A **CTRL+SHFT+E** billentyűkombinációt lenyomva beszúrhat egy új sort a Product táblába.

## <a name="update-data"></a>Adatok frissítése

Használja az [UPDATE](https://msdn.microsoft.com/library/ms177523.aspx) Transact-SQL utasítást az Azure SQL-adatbázis adatainak frissítésére.

1.  A **szerkesztő** ablakában törölje az előző lekérdezést, és adja meg a következő lekérdezést:

   ```sql
   UPDATE [SalesLT].[Product]
   SET [ListPrice] = 125
   WHERE Name = 'myNewProduct';
   ```

3. A **CTRL+SHFT+E** billentyűkombinációt lenyomva frissítheti a megadott sort a Product táblában.

## <a name="delete-data"></a>Adat törlése

Használja az [DELETE](https://msdn.microsoft.com/library/ms189835.aspx) Transact-SQL utasítást az Azure SQL-adatbázis adatainak törlésére.

1. A **szerkesztő** ablakában törölje az előző lekérdezést, és adja meg a következő lekérdezést:

   ```sql
   DELETE FROM [SalesLT].[Product]
   WHERE Name = 'myNewProduct';
   ```

3. A **CTRL+SHFT+E** billentyűkombinációt lenyomva törölheti a megadott sort a Product táblából.

## <a name="next-steps"></a>Következő lépések

- A Visual Studio Code-ról további információt a [Visual Studio Code-ot](https://code.visualstudio.com/docs) ismertető témakörben olvashat
- Az SQL Server Management Studióval végzett lekérdezésről és adatszerkesztésről az [SSMS](https://msdn.microsoft.com/library/ms174173.aspx) témakörében olvashat bővebben.
