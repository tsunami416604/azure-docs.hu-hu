---
title: A VS Code használata a kapcsolódáshoz és a lekérdezéshez
titleSuffix: Azure SQL Database & SQL Managed Instance
description: Ismerje meg, hogyan csatlakozhat Azure SQL Database vagy SQL felügyelt példányhoz az Azure-ban a Visual Studio Code használatával. Ezután futtasson Transact-SQL (T-SQL) utasításokat az adatok lekérdezéséhez és szerkesztéséhez.
keywords: csatlakozás SQL Database-adatbázishoz
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/25/2019
ms.openlocfilehash: b67748a9bfb22eed4afb76c960f992a56c10e546
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2020
ms.locfileid: "84054358"
---
# <a name="quickstart-use-visual-studio-code-to-connect-and-query"></a>Rövid útmutató: a Visual Studio Code használata a kapcsolódáshoz és a lekérdezéshez 
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

A [Visual Studio Code](https://code.visualstudio.com/docs) egy grafikus Kódszerkesztő Linux, MacOS és Windows rendszerekhez. Támogatja a bővítményeket, beleértve az [MSSQL bővítményt](https://aka.ms/mssql-marketplace) Microsoft SQL Server, Azure SQL Database, az Azure SQL felügyelt példányának és az Azure szinapszis Analytics lekérdezéséhez. Ebben a rövid útmutatóban a Visual Studio Code használatával csatlakozik Azure SQL Database vagy az Azure SQL felügyelt példányához, majd Transact-SQL-utasítások futtatásával kérdezheti le, szúrhatja be, frissítheti és törölheti az adatokat.

## <a name="prerequisites"></a>Előfeltételek

- Egy Azure SQL Database vagy SQL felügyelt példánya. Az alábbi rövid útmutatók segítségével hozhat létre és konfigurálhat egy adatbázist Azure SQL Databaseban:

  || Azure SQL Database | SQL Managed Instance |
  |:--- |:--- |:---|
  | Létrehozás| [Portál](single-database-create-quickstart.md) | [Portál](../managed-instance/instance-create-quickstart.md) |
  || [parancssori felület](scripts/create-and-configure-database-cli.md) | [parancssori felület](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [PowerShell](scripts/create-and-configure-database-powershell.md) | [PowerShell](../managed-instance/scripts/create-configure-managed-instance-powershell.md) |
  | Konfigurálás | [Kiszolgálói szintű IP-tűzfalszabály](firewall-create-server-level-portal-quickstart.md))| [Kapcsolódás virtuális gépről](../managed-instance/connect-vm-instance-configure.md)|
  |||[Kapcsolódás a webhelyről](../managed-instance/point-to-site-p2s-configure.md)
  |Adatok betöltése|Adventure Works betöltve|[Széles körű globális importőrök visszaállítása](../managed-instance/restore-sample-database-quickstart.md)
  |||Adventure Works visszaállítása vagy importálása a [BACPAC](database-import.md) -fájlból a [githubról](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)|
  |||

  > [!IMPORTANT]
  > A cikkben található parancsfájlok az Adventure Works-adatbázis használatára íródnak. A felügyelt SQL-példányok esetében importálnia kell az Adventure Works-adatbázist egy példány-adatbázisba, vagy módosítania kell a jelen cikkben szereplő parancsfájlokat a Wide World importőr-adatbázis használatára.

## <a name="install-visual-studio-code"></a>A Visual Studio Code telepítése

Győződjön meg arról, hogy telepítette a legújabb [Visual Studio-kódot](https://code.visualstudio.com/Download) , és betöltötte az [MSSQL bővítményt](https://aka.ms/mssql-marketplace). Az MSSQL-bővítmény telepítésével kapcsolatos útmutatásért lásd: a [vs Code](https://docs.microsoft.com/sql/linux/sql-server-linux-develop-use-vscode#install-and-start-visual-studio-code) és [az MSSQL telepítése a Visual Studio Code ](https://marketplace.visualstudio.com/items?itemName=ms-mssql.mssql)-hoz.

## <a name="configure-visual-studio-code"></a>A Visual Studio Code konfigurálása

### <a name="macos"></a>**macOS**

MacOS esetén telepítenie kell az OpenSSL-t, amely az MSSQL-bővítmény által használt .NET Core előfeltétele. Nyissa meg a terminált, és adja meg a következő parancsokat a **Brew** és az **OpenSSL**telepítéséhez.

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

## <a name="get-sql-server-connection-information"></a>SQL Server-kapcsolatok adatainak beolvasása

Szerezze be a Azure SQL Databasehoz való kapcsolódáshoz szükséges kapcsolati adatokat. A közelgő eljárásokhoz szüksége lesz a teljes kiszolgálónévre vagy az állomásnévre, az adatbázis nevére és a bejelentkezési adatokra.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

2. Navigáljon az **SQL-adatbázisok** vagy az **SQL-felügyelt példányok** lapra.

3. Az **Áttekintés** lapon tekintse át a teljes kiszolgálónevet a SQL Database **kiszolgáló neve** mellett, vagy a **gazdagép** melletti teljes kiszolgálónevet egy SQL felügyelt példányhoz. A kiszolgálónév vagy az állomásnév másolásához vigye a kurzort a fölé, és válassza a **Másolás** ikont.

## <a name="set-language-mode-to-sql"></a>A nyelvmód SQL értékre állítása

A Visual Studio Code-ban állítsa a nyelvi módot **SQL** -re az MSSQL-parancsok és a T-SQL IntelliSense engedélyezéséhez.

1. Nyisson meg egy új Visual Studio Code-ablakot.

2. Nyomja le a **CTRL** + **N**billentyűkombinációt. Megnyílik egy új egyszerű szövegfájl.

3. Válassza az **egyszerű szöveg** elemet az állapotsor jobb alsó sarkában.

4. A megnyíló **nyelvi mód kiválasztása** legördülő menüben válassza az **SQL**lehetőséget.

## <a name="connect-to-your-database"></a>Csatlakozás az adatbázishoz

Hozzon létre kapcsolatot a kiszolgálóval a Visual Studio Code használatával.

> [!IMPORTANT]
> A folytatás előtt győződjön meg arról, hogy a kiszolgáló és a bejelentkezési adatok készen állnak. Ha megkezdte a kapcsolódási profil adatainak beírását, akkor a fókuszt a Visual Studio Code-ról kell újraindítani.

1. A Visual Studio Code-ban nyomja le a **CTRL + SHIFT + P** (vagy **F1**) billentyűkombinációt a parancssor megnyitásához.

2. Válassza az **MS SQL: kapcsolat** lehetőséget, és válassza az **ENTER billentyűt**.

3. Válassza a **kapcsolatprofil létrehozása**lehetőséget.

4. Az új profil kapcsolódási tulajdonságainak megadásához kövesse az utasításokat. Az egyes értékek megadása után a folytatáshoz válassza az **ENTER billentyűt** .

   | Tulajdonság       | Ajánlott érték | Leírás |
   | ------------ | ------------------ | ------------------------------------------------- |
   | **Kiszolgáló neve** | A teljes kiszolgálónév | Valami hasonló: **mynewserver20170313.database.Windows.net**. |
   | **Adatbázis neve** | mySampleDatabase | Az adatbázis, amelyhez csatlakozni kíván. |
   | **Hitelesítés** | SQL-bejelentkezés| Ez az oktatóanyag SQL-hitelesítést használ. |
   | **Felhasználónév** | Felhasználónév | A kiszolgáló létrehozásához használt kiszolgálói rendszergazdai fiók felhasználóneve. |
   | **Jelszó (SQL-bejelentkezés)** | Jelszó | A kiszolgáló létrehozásához használt kiszolgálói rendszergazdai fiók jelszava. |
   | **Menti a jelszót?** | Igen vagy nem | Válassza az **Igen** lehetőséget, ha nem szeretné minden alkalommal megadni a jelszót. |
   | **Adja meg a profil kívánt nevét** | Egy profil neve, például **mySampleProfile** | A mentett profil a későbbi bejelentkezések során felgyorsítja a kapcsolatokat. |

   Ha a művelet sikeres, megjelenik egy értesítés, amely azt jelzi, hogy a profil létrejött és csatlakoztatva van.

## <a name="query-data"></a>Adatok lekérdezése

Futtassa a következő [Select](https://msdn.microsoft.com/library/ms189499.aspx) Transact-SQL-utasítást az első 20 termék kategóriánkénti lekérdezéséhez.

1. Illessze be a következő SQL-lekérdezést a szerkesztő ablakba.

   ```sql
   SELECT pc.Name as CategoryName, p.name as ProductName
   FROM [SalesLT].[ProductCategory] pc
   JOIN [SalesLT].[Product] p
   ON pc.productcategoryid = p.productcategoryid;
   ```

2. A **CTRL** + **billentyűt**lenyomva + **E** futtassa a lekérdezést, és jelenítse meg az eredményeket a `Product` és a `ProductCategory` táblákból.

    ![Lekérdezés az adatok 2 táblából való lekéréséhez](./media/connect-query-vscode/query.png)

## <a name="insert-data"></a>Adat beszúrása

Futtassa az alábbi [Insert](https://msdn.microsoft.com/library/ms174335.aspx) Transact-SQL-utasítást egy új termék táblába való felvételéhez `SalesLT.Product` .

1. Cserélje le az előző lekérdezést ezzel az eggyel.

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

2. Nyomja le a **CTRL** + **billentyűt** + **E** , és szúrjon be egy új sort a `Product` táblázatba.

## <a name="update-data"></a>Adatok frissítése

Futtassa a következő [frissítési](https://msdn.microsoft.com/library/ms177523.aspx) Transact-SQL-utasítást a hozzáadott termék frissítéséhez.

1. Cserélje le az előző lekérdezést a következőre:

   ```sql
   UPDATE [SalesLT].[Product]
   SET [ListPrice] = 125
   WHERE Name = 'myNewProduct';
   ```

2. Nyomja le a **CTRL**E billentyűt a + **Shift** + **E** tábla megadott sorának frissítéséhez `Product` .

## <a name="delete-data"></a>Adat törlése

Futtassa az alábbi [delete](https://docs.microsoft.com/sql/t-sql/statements/delete-transact-sql) Transact-SQL utasítást az új termék eltávolításához.

1. Cserélje le az előző lekérdezést a következőre:

   ```sql
   DELETE FROM [SalesLT].[Product]
   WHERE Name = 'myNewProduct';
   ```

2. Nyomja le a **CTRL**E billentyűt a + **Shift** + **E** tábla megadott sorának törléséhez `Product` .

## <a name="next-steps"></a>További lépések

- A SQL Server Management Studio használatával történő kapcsolódáshoz és lekérdezéshez tekintse meg a rövid útmutató [: SQL Server Management Studio használata a Azure SQL Databasehoz való kapcsolódáshoz és az adatlekérdezéshez](connect-query-ssms.md)című témakört.
- A Azure Portal használatával történő kapcsolódáshoz és lekérdezéshez tekintse meg a gyors útmutató [: az SQL-lekérdezési szerkesztő használata a Azure Portal a kapcsolódáshoz és az adatlekérdezéshez](connect-query-portal.md).
- Az MSDN magazin Visual Studio Code használatáról szóló cikkéhez lásd az [Adatbázis IDE létrehozása az MSSQL bővítménnyel blogbejegyzést](https://msdn.microsoft.com/magazine/mt809115).
