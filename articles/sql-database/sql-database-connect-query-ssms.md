---
title: 'SSMS: Csatlakozás és adatlekérdezés egy Azure SQL database-ben |} A Microsoft Docs'
description: Ebből a cikkből megtudhatja, hogyan csatlakozhat az SQL Database-hez az Azure-ban az SQL Server Management Studio (SSMS) használatával. Ezután futtasson Transact-SQL (T-SQL) utasításokat az adatok lekérdezéséhez és szerkesztéséhez.
keywords: csatlakozás sql database-hez,sql server management studio
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 03/25/2019
ms.openlocfilehash: 2348b4293b8726c406b1f06b2f88c37dfb00e80c
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2019
ms.locfileid: "58447754"
---
# <a name="quickstart-use-sql-server-management-studio-to-connect-and-query-an-azure-sql-database"></a>Gyors útmutató: SQL Server Management Studio használatával csatlakozhat, és az Azure SQL Database-adatbázis lekérdezéséhez

Ez a rövid útmutatóban használni kívánt [SQL Server Management Studio] [ ssms-install-latest-84g] (SSMS) egy Azure SQL Database-adatbázishoz csatlakozhat. Ezután futtassa a Transact-SQL-utasítások használatával lekérdezni, beszúrni, frissítése és adatok törlése. SSMS segítségével bármilyen SQL infrastruktúra kezelésére az SQL Servertől az SQL Database a Microsoft Windows.  

## <a name="prerequisites"></a>Előfeltételek

- Azure SQL Database-adatbázis. Az alábbi rövid útmutatókban hozhat létre, és válassza az Azure SQL Database egy adatbázis is használja:

  || Önálló adatbázis | Felügyelt példány |
  |:--- |:--- |:---|
  | Létrehozás| [Portál](sql-database-single-database-get-started.md) | [Portál](sql-database-managed-instance-get-started.md) |
  || [Parancssori felület](scripts/sql-database-create-and-configure-database-cli.md) | [Parancssori felület](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [PowerShell](scripts/sql-database-create-and-configure-database-powershell.md) | [PowerShell](scripts/sql-database-create-configure-managed-instance-powershell.md) |
  | Konfigurálás | [kiszolgálószintű IP-tűzfalszabály](sql-database-server-level-firewall-rule.md)| [Kapcsolat egy virtuális gépről](sql-database-managed-instance-configure-vm.md)|
  |||[Helyszíni kapcsolat](sql-database-managed-instance-configure-p2s.md)
  |Adatok betöltése|Az Adventure Works betöltött száma a rövid útmutató|[Állítsa vissza a Wide World Importers](sql-database-managed-instance-get-started-restore.md)
  |||Állítsa vissza vagy importálása az Adventure Works [BACPAC](sql-database-import.md) fájlt [GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)|
  |||

  > [!IMPORTANT]
  > Ebben a cikkben a parancsfájlok az Adventure Works adatbázisa használatához készültek. Felügyelt példánnyal Ha az Adventure Works adatbázisa importálása-példány adatbázis, vagy módosítsa a parancsfájlokat ebben a cikkben a Wide World Importers-adatbázis használatára.

## <a name="install-the-latest-ssms"></a>Az SSMS legújabb verziójának telepítése

Mielőtt elkezdené, győződjön meg arról, hogy telepítette a legújabb [SSMS][ssms-install-latest-84g]. 

## <a name="get-sql-server-connection-information"></a>Az SQL server-kapcsolati adatok lekéréséhez

Az Azure SQL-adatbázishoz való csatlakozáshoz szükséges kapcsolati információkat kaphat. A következő eljárások szüksége a kiszolgáló teljes nevét vagy a gazdagépnév, az adatbázis neve és a bejelentkezési adatait.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

2. Keresse meg a **SQL-adatbázisok** vagy **SQL felügyelt példányai** lapot.

3. A a **áttekintése** lapon, tekintse át a teljes kiszolgálónevet melletti **kiszolgálónév** egy önálló adatbázis vagy a kiszolgáló teljes neve melletti **gazdagép** számára egy felügyelt a példány. Másolja ki a kiszolgáló nevét vagy az állomásnevet, rámutatnak, és válassza a **másolási** ikonra.

## <a name="connect-to-your-database"></a>Csatlakozás az adatbázishoz

Az SMSS csatlakozzon az Azure SQL Database-kiszolgálóhoz. 

> [!IMPORTANT]
> Egy Azure SQL Database server a 1433-as portot figyeli. A céges tűzfal mögül egy SQL Database-kiszolgálóhoz csatlakozni, a tűzfal nyissa meg ezt a portot kell rendelkeznie.
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

Futtassa a következő parancsot [kiválasztása](https://msdn.microsoft.com/library/ms189499.aspx) Transact-SQL kód használatával lekérdezheti kategóriánként az első 20 terméket.

1. Az Object Explorerben kattintson a jobb gombbal **mySampleDatabase** válassza **új lekérdezés**. Megnyílik egy új, az adatbázishoz kapcsolódó lekérdezési ablakot.

2. A lekérdezési ablakban illessze be az SQL-lekérdezést.

   ```sql
   SELECT pc.Name as CategoryName, p.name as ProductName
   FROM [SalesLT].[ProductCategory] pc
   JOIN [SalesLT].[Product] p
   ON pc.productcategoryid = p.productcategoryid;
   ```

3. Válassza az eszköztár **Execute** be adatokat a `Product` és `ProductCategory` táblákat.

    ![adatok két tábla lekérdezése](./media/sql-database-connect-query-ssms/query2.png)

## <a name="insert-data"></a>Adat beszúrása

Futtassa a következő parancsot [BESZÚRÁSA](https://msdn.microsoft.com/library/ms174335.aspx) hozhat létre egy új terméket a Transact-SQL-kódot a `SalesLT.Product` tábla.

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

2. Válassza ki **Execute** az új sor beszúrásához a `Product` tábla. A **üzenetek** ablaktáblán megjelennek azok **(1 sort érint)**.

## <a name="view-the-result"></a>Az eredmény megtekintéséhez

1. Ehhez cserélje le az előző lekérdezést.

   ```sql
   SELECT * FROM [SalesLT].[Product] 
   WHERE Name='myNewProduct' 
   ```
   
2. Válassza a **Végrehajtás** lehetőséget. Az alábbi eredmény jelenik meg. 

   ![Eredmény](./media/sql-database-connect-query-ssms/result.png)

 
## <a name="update-data"></a>Adatok frissítése

Futtassa a következő parancsot [frissítés](https://msdn.microsoft.com/library/ms177523.aspx) módosíthatja az új termék Transact-SQL-kódot.

1. Ehhez cserélje le az előző lekérdezést.

   ```sql
   UPDATE [SalesLT].[Product]
   SET [ListPrice] = 125
   WHERE Name = 'myNewProduct';
   ```

2. Válassza ki **Execute** egy megadott sorának frissítéséhez a `Product` tábla. A **üzenetek** ablaktáblán megjelennek azok **(1 sort érint)**.

## <a name="delete-data"></a>Adat törlése

Futtassa a következő parancsot [törlése](https://msdn.microsoft.com/library/ms189835.aspx) Transact-SQL-kódot az új termék eltávolítása.

1. Ehhez cserélje le az előző lekérdezést.

   ```sql
   DELETE FROM [SalesLT].[Product]
   WHERE Name = 'myNewProduct';
   ```

2. Válassza ki **Execute** egy megadott sorának törléséhez a `Product` tábla. A **üzenetek** ablaktáblán megjelennek azok **(1 sort érint)**.

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

