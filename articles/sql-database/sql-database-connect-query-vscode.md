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
ms.custom: quick start manage
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 03/17/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: ff5d156ab2b701233c4cdbf08e3d6e517c01b9fb
ms.lasthandoff: 04/12/2017


---
# <a name="azure-sql-database-use-visual-studio-code-to-connect-and-query-data"></a>Azure SQL Database: Csatlakozás, majd adatok lekérdezése a Visual Studio Code használatával

A [Visual Studio Code](https://code.visualstudio.com/docs) egy grafikus kódszerkesztő Linux, macOS és Windows rendszerekre, amely támogatja a bővítményeket, beleértve az [mssql bővítményt](https://aka.ms/mssql-marketplace) a Microsoft SQL Server, az Azure SQL Database és az SQL Data Warehouse lekérdezéséhez. Ez a gyors üzembehelyezési útmutató ismerteti, hogyan használható a Visual Studio Code egy Azure SQL Database-adatbázishoz való csatlakozáshoz, és hogyan lehet Transact-SQL-utasításokkal adatokat lekérdezni, beszúrni, frissíteni és törölni az adatbázisban.

Ez a rövid útmutató az alábbi rövid útmutatók egyikében létrehozott erőforrásokat használja kiindulási pontnak:

- [DB létrehozása – portál](sql-database-get-started-portal.md)
- [DB létrehozása – CLI](sql-database-get-started-cli.md)

A kezdés előtt győződjön meg arról, hogy a [Visual Studio Code](https://code.visualstudio.com/Download) legújabb verziója van telepítve, és be van töltve az [mssql bővítmény](https://aka.ms/mssql-marketplace). Az mssql bővítmény telepítési lépéseinek megismeréséhez olvassa el [a VS Code telepítését](https://docs.microsoft.com/sql/linux/sql-server-linux-develop-use-vscode#install-vs-code) és a [Visual Studio Code-hoz használható mssql-t](https://marketplace.visualstudio.com/items?itemName=ms-mssql.mssql) ismertető cikkeket. 

## <a name="configure-vs-code-mac-os-only"></a>Konfigurálás vagy kód (csak Mac OS esetén)

### <a name="mac-os"></a>**Mac OS**
Mac OS esetén telepíteni kell az OpenSSL-t, amely az mssql bővítmény által használt DotNet Core előfeltétele. Nyissa meg a terminált, és adja meg az alábbi parancsokat a **brew** és az **OpenSSL*** telepítéséhez. 

```bash
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
brew update
brew install openssl
mkdir -p /usr/local/lib
ln -s /usr/local/opt/openssl/lib/libcrypto.1.0.0.dylib /usr/local/lib/
ln -s /usr/local/opt/openssl/lib/libssl.1.0.0.dylib /usr/local/lib/
```

## <a name="get-connection-information"></a>Kapcsolatadatok lekérése

Kérje le az Azure SQL Database kiszolgáló teljes kiszolgálónevét az Azure Portalon. A teljes kiszolgálónévvel csatlakozhat a kiszolgálóhoz a Visual Studio Code-ot használva.

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com/).
2. Válassza az **SQL-adatbázisok** elemet a bal oldali menüben, majd kattintson az új adatbázisra az **SQL-adatbázisok** oldalon. 
3. Az Azure Portalon az adatbázishoz tartozó lap **Alapvető erőforrások** ablaktábláján keresse meg, majd másolja ki a **Kiszolgáló nevét**, amelyet majd később használ fel ebben a gyors üzembe helyezésben.

    <img src="./media/sql-database-connect-query-vscode/connection-information.png" alt="connection information" style="width: 780px;" />

## <a name="set-language-mode-to-sql"></a>A nyelvmód SQL értékre állítása

Az mssql-parancsok és a T-SQL IntelliSense engedélyezéséhez ügyeljen arra, hogy a nyelvmód beállítása **SQL** legyen a Visual Studio Code-ban.

1. Nyisson meg egy új Visual Studio Code-ablakot. 

2. A nyelvmód SQL értékre állításához nyomja le a **⌘+K,M** illetve a **CTRL+K,M** (előbbit Mac, utóbbit Windows esetén) billentyűkombinációt, írja be az **SQL** kifejezést, majd nyomja le az **ENTER** billentyűt. 

<img src="./media/sql-database-connect-query-vscode/vscode-language-mode.png" alt="SQL language mode" style="width: 780px;" />

## <a name="connect-to-the-server"></a>Csatlakozás a kiszolgálóhoz

A Visual Studio Code segítségével kapcsolatot hozhat létre az Azure SQL Database-kiszolgálóval.

1. A VS Code-ban nyomja le a **CTRL+SHIFT+P** billentyűkombinációt (vagy az **F1** billentyűt) a parancskatalógus megnyitásához.

2. Írja be az **sqlcon** parancsot, nyomja le az **ENTER** billentyűt, és állítsa a nyelvet **SQL** értékre.

3. A **Kapcsolati profil létrehozása** kiválasztásához nyomja le az **ENTER** billentyűt. Ezzel létrehoz egy kapcsolati profilt az SQL Server-példányhoz.

4. Az útmutatást követve adja meg az új kapcsolati profil kapcsolati tulajdonságait. Az egyes értékek kiválasztása után a folytatáshoz nyomja le az **ENTER** billentyűt. 

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

5. Az **ESC** billentyűt lenyomva zárja be a profil létrehozásáról és csatlakoztatásáról tájékoztató üzenetet.

6. Ellenőrizze a kapcsolatot az állapotsávon.

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

2. A **CTRL+SHIFT+E** billentyűkombinációt lenyomva lekérheti az adatokat a Product és ProductCategory táblából.

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

2. A **CTRL+SHFT+E** billentyűkombinációt lenyomva beszúrhat egy új sort a Product táblába.

## <a name="update-data"></a>Adatok frissítése

Használja az [UPDATE](https://msdn.microsoft.com/library/ms177523.aspx) Transact-SQL utasítást az Azure SQL-adatbázis adatainak frissítésére.

1.  A **szerkesztő** ablakában törölje az előző lekérdezést, és adja meg a következő lekérdezést:

   ```sql
   UPDATE [SalesLT].[Product]
   SET [ListPrice] = 125
   WHERE Name = 'myNewProduct';
   ```

2. A **CTRL+SHFT+E** billentyűkombinációt lenyomva frissítheti a megadott sort a Product táblában.

## <a name="delete-data"></a>Adat törlése

Használja az [DELETE](https://msdn.microsoft.com/library/ms189835.aspx) Transact-SQL utasítást az Azure SQL-adatbázis adatainak törlésére.

1. A **szerkesztő** ablakában törölje az előző lekérdezést, és adja meg a következő lekérdezést:

   ```sql
   DELETE FROM [SalesLT].[Product]
   WHERE Name = 'myNewProduct';
   ```

2. A **CTRL+SHFT+E** billentyűkombinációt lenyomva törölheti a megadott sort a Product táblából.

## <a name="next-steps"></a>Következő lépések

- Az SQL Server Management Studióval történő csatlakozáshoz és lekérdezéshez lásd [az SSMS segítségével történő csatlakozással és lekérdezéssel](sql-database-connect-query-ssms.md) foglalkozó témakört.
- A .NET használatával történő csatlakozásról és lekérdezésről lásd a [.NET használatával végzett csatlakozásról és lekérdezésről](sql-database-connect-query-dotnet.md) szóló témakört.
- A PHP-vel történő csatlakozásról és lekérdezésről lásd a [PHP-vel végzett csatlakozásról és lekérdezésről](sql-database-connect-query-php.md) szóló témakört.
- A Node.js használatával történő csatlakozásról és lekérdezésről lásd a [Node.js használatával végzett csatlakozásról és lekérdezésről](sql-database-connect-query-nodejs.md) szóló témakört.
- A Java használatával történő csatlakozásról és lekérdezésről lásd a [Java használatával végzett csatlakozásról és lekérdezésről](sql-database-connect-query-java.md) szóló témakört.
- A Python használatával történő csatlakozásról és lekérdezésről lásd a [Python használatával végzett csatlakozásról és lekérdezésről](sql-database-connect-query-python.md) szóló témakört.
- A Ruby használatával történő csatlakozásról és lekérdezésről lásd a [Ruby használatával végzett csatlakozásról és lekérdezésről](sql-database-connect-query-ruby.md) szóló témakört.

