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
ms.date: 04/17/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 8c4e33a63f39d22c336efd9d77def098bd4fa0df
ms.openlocfilehash: 45405c7bb9993d1fd529b25b599c3cd7f459843c
ms.lasthandoff: 04/19/2017


---
# <a name="azure-sql-database-use-visual-studio-code-to-connect-and-query-data"></a>Azure SQL Database: Csatlakozás, majd adatok lekérdezése a Visual Studio Code használatával

A [Visual Studio Code](https://code.visualstudio.com/docs) egy grafikus kódszerkesztő Linux, macOS és Windows rendszerekre, amely támogatja a bővítményeket, beleértve az [mssql bővítményt](https://aka.ms/mssql-marketplace) a Microsoft SQL Server, az Azure SQL Database és az SQL Data Warehouse lekérdezéséhez. Ez a gyors üzembehelyezési útmutató ismerteti, hogyan használható a Visual Studio Code egy Azure SQL Database-adatbázishoz való csatlakozáshoz, és hogyan lehet Transact-SQL-utasításokkal adatokat lekérdezni, beszúrni, frissíteni és törölni az adatbázisban.

Ez a rövid útmutató az alábbi rövid útmutatók egyikében létrehozott erőforrásokat használja kiindulási pontnak:

- [DB létrehozása – portál](sql-database-get-started-portal.md)
- [DB létrehozása – CLI](sql-database-get-started-cli.md)

A kezdés előtt győződjön meg arról, hogy a [Visual Studio Code](https://code.visualstudio.com/Download) legújabb verziója van telepítve, és be van töltve az [mssql bővítmény](https://aka.ms/mssql-marketplace). Az mssql bővítmény telepítési lépéseinek megismeréséhez olvassa el [a VS Code telepítését](https://docs.microsoft.com/sql/linux/sql-server-linux-develop-use-vscode#install-vs-code) és a [Visual Studio Code-hoz használható mssql-t](https://marketplace.visualstudio.com/items?itemName=ms-mssql.mssql) ismertető cikkeket. 

## <a name="configure-vs-code"></a>A VS Code konfigurálása 

### <a name="mac-os"></a>**Mac OS**
Mac OS esetén telepíteni kell az OpenSSL-t, amely az mssql bővítmény által használt DotNet Core előfeltétele. Nyissa meg a terminált, és adja meg az alábbi parancsokat a **brew** és az **OpenSSL** telepítéséhez. 

```bash
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
brew update
brew install openssl
mkdir -p /usr/local/lib
ln -s /usr/local/opt/openssl/lib/libcrypto.1.0.0.dylib /usr/local/lib/
ln -s /usr/local/opt/openssl/lib/libssl.1.0.0.dylib /usr/local/lib/
```

### <a name="linux-ubuntu"></a>**Linux (Ubuntu)**

Nincs szükség különleges konfigurációra.

### <a name="windows"></a>**Windows**

Nincs szükség különleges konfigurációra.

## <a name="get-connection-information"></a>Kapcsolatadatok lekérése

Kérje le az Azure SQL-adatbázishoz való csatlakozáshoz szükséges kapcsolatadatokat. A későbbi eljárásokban szüksége lesz a teljes kiszolgálónévre, az adatbázis nevére és a bejelentkezési adatokra.

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com/).
2. Válassza az **SQL-adatbázisok** elemet a bal oldali menüben, majd kattintson az új adatbázisra az **SQL-adatbázisok** oldalon. 
3. Az adatbázis **Áttekintés** oldalán tekintse meg a teljes kiszolgálónevet, amint az az alábbi képen látható. Ha a mutatót a kiszolgáló neve fölé viszi, megjelenik a **Kattintson a másoláshoz** lehetőség.

   ![kapcsolatadatok](./media/sql-database-connect-query-ssms/connection-information.png) 

4. Amennyiben elfelejtette Azure SQL Database-kiszolgálója bejelentkezési adatait, lépjen az SQL Database-kiszolgáló oldalára, és itt megtudhatja a kiszolgáló rendszergazdájának nevét, valamint szükség esetén visszaállíthatja a jelszót. 

## <a name="set-language-mode-to-sql"></a>A nyelvmód SQL értékre állítása

Az mssql-parancsok és a T-SQL IntelliSense engedélyezéséhez ügyeljen arra, hogy a nyelvmód beállítása **SQL** legyen a Visual Studio Code-ban.

1. Nyisson meg egy új Visual Studio Code-ablakot. 

2. Kattintson az **Egyszerű szöveg** elemre az állapotsor jobb alsó sarkában.
3. A megnyíló **Nyelvmód kiválasztása** legördülő menübe írja be az **SQL** kifejezést, majd nyomja le az **ENTER** billentyűt a nyelvmód SQL értékre állításához. 

   ![SQL nyelvmód](./media/sql-database-connect-query-vscode/vscode-language-mode.png)

## <a name="connect-to-your-database-in-the-sql-database-logical-server"></a>Csatlakozás az adatbázishoz az SQL Database logikai kiszolgálóban

A Visual Studio Code segítségével kapcsolatot hozhat létre az Azure SQL Database-kiszolgálóval.

> [!IMPORTANT]
> A folytatás előtt győződjön meg arról, hogy rendelkezésére állnak a kiszolgálóval és az adatbázissal kapcsolatos, valamint a bejelentkezési adatok. Ha elkezdi beírni a csatlakozási profil információit, és áthelyezi a fókuszt a Visual Studio Code-ról, újra kell kezdenie a csatlakozási profil létrehozását.
>

1. A VS Code-ban nyomja le a **CTRL+SHIFT+P** billentyűkombinációt (vagy az **F1** billentyűt) a parancskatalógus megnyitásához.

2. Írja be az **sqlcon** parancsot, és nyomja le az **ENTER** billentyűt.

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

   ![A kapcsolat állapota](./media/sql-database-connect-query-vscode/vscode-connection-status.png)

## <a name="query-data"></a>Adatok lekérdezése

A következő kód használatával lekérdezheti kategóriánként az első 20 terméket a [SELECT](https://msdn.microsoft.com/library/ms189499.aspx) Transact-SQL-utasítással.

1. A **szerkesztő** ablakában írja be a következő lekérdezést az üres lekérdezési ablakba:

   ```sql
   SELECT pc.Name as CategoryName, p.name as ProductName
   FROM [SalesLT].[ProductCategory] pc
   JOIN [SalesLT].[Product] p
   ON pc.productcategoryid = p.productcategoryid;
   ```

2. A **CTRL+SHIFT+E** billentyűkombinációt lenyomva lekérheti az adatokat a Product és ProductCategory táblából.

    ![Lekérdezés](./media/sql-database-connect-query-vscode/query.png)

## <a name="insert-data"></a>Adat beszúrása

A következő kód használatával beszúrhat egy új terméket a SalesLT.Product táblába az [INSERT](https://msdn.microsoft.com/library/ms174335.aspx) Transact-SQL-utasítással.

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

A következő kód használatával frissítheti az előzőleg hozzáadott új terméket az [UPDATE](https://msdn.microsoft.com/library/ms177523.aspx) Transact-SQL-utasítással.

1.  A **szerkesztő** ablakában törölje az előző lekérdezést, és adja meg a következő lekérdezést:

   ```sql
   UPDATE [SalesLT].[Product]
   SET [ListPrice] = 125
   WHERE Name = 'myNewProduct';
   ```

2. A **CTRL+SHFT+E** billentyűkombinációt lenyomva frissítheti a megadott sort a Product táblában.

## <a name="delete-data"></a>Adat törlése

A következő kód használatával törölheti az előzőleg hozzáadott új terméket a [DELETE](https://msdn.microsoft.com/library/ms189835.aspx) Transact-SQL-utasítással.

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

