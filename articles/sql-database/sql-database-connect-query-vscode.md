---
title: A VS Code használata a kapcsolódáshoz és a lekérdezéshez
description: Ebből a cikkből megtudhatja, hogyan csatlakozhat az SQL Database-hez az Azure-ban a Visual Studio Code segítségével. Ezután futtasson Transact-SQL (T-SQL) utasításokat az adatok lekérdezéséhez és szerkesztéséhez.
keywords: csatlakozás SQL Database-adatbázishoz
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/25/2019
ms.openlocfilehash: 28b35f273783b2e4d0b8f59c5bc5be384b933ba2
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73826898"
---
# <a name="quickstart-use-visual-studio-code-to-connect-and-query-an-azure-sql-database"></a>Rövid útmutató: a Visual Studio Code használata egy Azure SQL Databasehoz való kapcsolódáshoz és lekérdezéshez

A [Visual Studio Code](https://code.visualstudio.com/docs) egy grafikus Kódszerkesztő Linux, MacOS és Windows rendszerekhez. Támogatja a bővítményeket, beleértve az [MSSQL bővítményt](https://aka.ms/mssql-marketplace) Microsoft SQL Server, Azure SQL Database és SQL Data Warehouse lekérdezéséhez. Ebben a rövid útmutatóban a Visual Studio Code használatával csatlakozik egy Azure SQL Database-adatbázishoz, majd Transact-SQL-utasítások futtatásával kérdezheti le, szúrhatja be, frissítheti és törölheti az adatokat.

## <a name="prerequisites"></a>Előfeltételek

- Azure SQL-adatbázis. Az alábbi rövid útmutatók segítségével hozhat létre és konfigurálhat egy adatbázist Azure SQL Databaseban:

  || Önálló adatbázis | Felügyelt példány |
  |:--- |:--- |:---|
  | Létrehozás| [Portál](sql-database-single-database-get-started.md) | [Portál](sql-database-managed-instance-get-started.md) |
  || [Parancssori felület](scripts/sql-database-create-and-configure-database-cli.md) | [Parancssori felület](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [PowerShell](scripts/sql-database-create-and-configure-database-powershell.md) | [PowerShell](scripts/sql-database-create-configure-managed-instance-powershell.md) |
  | Konfigurálás | [Kiszolgálói szintű IP-tűzfalszabály](sql-database-server-level-firewall-rule.md)| [Kapcsolódás virtuális gépről](sql-database-managed-instance-configure-vm.md)|
  |||[Kapcsolódás a webhelyről](sql-database-managed-instance-configure-p2s.md)
  |Adatok betöltése|Adventure Works betöltve|[Széles körű globális importőrök visszaállítása](sql-database-managed-instance-get-started-restore.md)
  |||Adventure Works visszaállítása vagy importálása a [BACPAC](sql-database-import.md) -fájlból a [githubról](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)|
  |||

  > [!IMPORTANT]
  > A cikkben található parancsfájlok az Adventure Works-adatbázis használatára íródnak. Felügyelt példány esetén importálnia kell az Adventure Works-adatbázist egy példány-adatbázisba, vagy módosítania kell a jelen cikkben szereplő parancsfájlokat a Wide World Importálós adatbázis használatára.

## <a name="install-visual-studio-code"></a>A Visual Studio Code telepítése

Győződjön meg arról, hogy telepítette a legújabb [Visual Studio-kódot](https://code.visualstudio.com/Download) , és betöltötte az [MSSQL bővítményt](https://aka.ms/mssql-marketplace). Az MSSQL-bővítmény telepítésével kapcsolatos útmutatásért lásd: a [vs Code](https://docs.microsoft.com/sql/linux/sql-server-linux-develop-use-vscode#install-and-start-visual-studio-code) és [az MSSQL telepítése a Visual Studio Code ](https://marketplace.visualstudio.com/items?itemName=ms-mssql.mssql)-hoz.

## <a name="configure-visual-studio-code"></a>A Visual Studio Code konfigurálása

### <a name="mac-os"></a>**Mac OS**

MacOS esetén telepítenie kell az OpenSSL-t, amely az MSSQL-bővítmény által használt .NET Core előfeltétele. Nyissa meg a terminált, és adja meg az alábbi parancsokat a **brew** és az **OpenSSL** telepítéséhez.

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

Az Azure SQL Database-adatbázishoz való kapcsolódáshoz szükséges kapcsolati adatok beolvasása. A közelgő eljárásokhoz szüksége lesz a teljes kiszolgálónévre vagy az állomásnévre, az adatbázis nevére és a bejelentkezési adatokra.

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com/).

2. Navigáljon az **SQL-adatbázisok** vagy az **SQL-felügyelt példányok** lapra.

3. Az **Áttekintés** lapon tekintse át a teljes kiszolgálónevet a **kiszolgáló neve** mellett egyetlen adatbázishoz vagy a felügyelt példányhoz tartozó **gazdagép** melletti teljes kiszolgálónévhez. A kiszolgálónév vagy az állomásnév másolásához vigye a kurzort a fölé, és válassza a **Másolás** ikont.

## <a name="set-language-mode-to-sql"></a>A nyelvmód SQL értékre állítása

A Visual Studio Code-ban állítsa a nyelvi módot **SQL** -re az MSSQL-parancsok és a T-SQL IntelliSense engedélyezéséhez.

1. Nyisson meg egy új Visual Studio Code-ablakot.

2. Nyomja le a **CTRL billentyűt**+**N**billentyűkombinációt. Megnyílik egy új egyszerű szövegfájl.

3. Válassza az **egyszerű szöveg** elemet az állapotsor jobb alsó sarkában.

4. A megnyíló **nyelvi mód kiválasztása** legördülő menüben válassza az **SQL**lehetőséget.

## <a name="connect-to-your-database"></a>Csatlakozás az adatbázishoz

A Visual Studio Code segítségével kapcsolatot hozhat létre az Azure SQL-adatbáziskiszolgálóval.

> [!IMPORTANT]
> A folytatás előtt győződjön meg arról, hogy a kiszolgáló és a bejelentkezési adatok készen állnak. Ha megkezdte a kapcsolódási profil adatainak beírását, akkor a fókuszt a Visual Studio Code-ról kell újraindítani.

1. A Visual Studio Code-ban nyomja le a **CTRL + SHIFT + P** (vagy **F1**) billentyűkombinációt a parancssor megnyitásához.

2. Válassza az **MS SQL: kapcsolat** lehetőséget, és válassza az **ENTER billentyűt**.

3. Válassza a **kapcsolatprofil létrehozása**lehetőséget.

4. Az új profil kapcsolódási tulajdonságainak megadásához kövesse az utasításokat. Az egyes értékek megadása után a folytatáshoz válassza az **ENTER billentyűt** .

   | Tulajdonság       | Ajánlott érték | Leírás |
   | ------------ | ------------------ | ------------------------------------------------- |
   | **Kiszolgálónév** | A teljes kiszolgálónév | Valami hasonló: **mynewserver20170313.database.Windows.net**. |
   | **Adatbázis neve** | mySampleDatabase | Az adatbázis, amelyhez csatlakozni kíván. |
   | **Hitelesítés** | SQL-bejelentkezés| Ez az oktatóanyag SQL-hitelesítést használ. |
   | **Felhasználónév** | Felhasználónév | A kiszolgáló létrehozásához használt kiszolgálói rendszergazdai fiók felhasználóneve. |
   | **Jelszó (SQL-bejelentkezés)** | Jelszó | A kiszolgáló létrehozásához használt kiszolgálói rendszergazdai fiók jelszava. |
   | **Menti a jelszót?** | Igen vagy Nem | Válassza az **Igen** lehetőséget, ha nem szeretné minden alkalommal megadni a jelszót. |
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

2. Nyomja le a **CTRL** billentyűt+**SHIFT**+**E** billentyűkombinációt a lekérdezés futtatásához és a `Product` és `ProductCategory` táblák eredményeinek megjelenítéséhez.

    ![Lekérdezés az adatok 2 táblából való lekéréséhez](./media/sql-database-connect-query-vscode/query.png)

## <a name="insert-data"></a>Adat beszúrása

A következő [Insert](https://msdn.microsoft.com/library/ms174335.aspx) Transact-SQL-utasítás futtatásával vegyen fel egy új terméket a `SalesLT.Product` táblába.

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

2. Nyomja le a **CTRL** billentyűt+**SHIFT**+**E** billentyűkombinációt egy új sor beszúrásához a `Product` táblába.

## <a name="update-data"></a>Adatok frissítése

Futtassa a következő [frissítési](https://msdn.microsoft.com/library/ms177523.aspx) Transact-SQL-utasítást a hozzáadott termék frissítéséhez.

1. Cserélje le az előző lekérdezést a következőre:

   ```sql
   UPDATE [SalesLT].[Product]
   SET [ListPrice] = 125
   WHERE Name = 'myNewProduct';
   ```

2. Nyomja le a **CTRL** billentyűt+**SHIFT**+**E** billentyűkombinációt a `Product` tábla megadott sorának frissítéséhez.

## <a name="delete-data"></a>Adat törlése

Futtassa az alábbi [delete](https://docs.microsoft.com/sql/t-sql/statements/delete-transact-sql) Transact-SQL utasítást az új termék eltávolításához.

1. Cserélje le az előző lekérdezést a következőre:

   ```sql
   DELETE FROM [SalesLT].[Product]
   WHERE Name = 'myNewProduct';
   ```

2. Nyomja le a **CTRL** billentyűt+**SHIFT**+**E** billentyűkombinációt a `Product` táblázatban megadott sor törléséhez.

## <a name="next-steps"></a>További lépések

- A SQL Server Management Studio használatával történő kapcsolódáshoz és lekérdezéshez tekintse meg a rövid útmutató [: SQL Server Management Studio használata a Azure SQL Databasehoz való kapcsolódáshoz és az adatlekérdezéshez](sql-database-connect-query-ssms.md)című témakört.
- A Azure Portal használatával történő kapcsolódáshoz és lekérdezéshez tekintse meg a gyors útmutató [: az SQL-lekérdezési szerkesztő használata a Azure Portal a kapcsolódáshoz és az adatlekérdezéshez](sql-database-connect-query-portal.md).
- Az MSDN magazin Visual Studio Code használatáról szóló cikkéhez lásd az [Adatbázis IDE létrehozása az MSSQL bővítménnyel blogbejegyzést](https://msdn.microsoft.com/magazine/mt809115).
