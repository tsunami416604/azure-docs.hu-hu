---
title: Csatlakozás és lekérdezés a VS-kód dal
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
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "73826898"
---
# <a name="quickstart-use-visual-studio-code-to-connect-and-query-an-azure-sql-database"></a>Rövid útmutató: Azure SQL-adatbázis csatlakoztatása és lekérdezése a Visual Studio-kóddal

[A Visual Studio Code](https://code.visualstudio.com/docs) egy grafikus kódszerkesztő Linux, macOS és Windows rendszerekhez. Támogatja a bővítményeket, beleértve az [mssql bővítményt](https://aka.ms/mssql-marketplace) a Microsoft SQL Server, az Azure SQL Database és az SQL Data Warehouse lekérdezéséhez. Ebben a rövid útmutatóban a Visual Studio Code használatával csatlakozhat egy Azure SQL-adatbázishoz, majd futtathatja a Transact-SQL utasításokat az adatok lekérdezéséhez, beszúrásához, frissítéséhez és törléséhez.

## <a name="prerequisites"></a>Előfeltételek

- Azure SQL-adatbázis. Az alábbi rövid útmutatók egyikével hozhat létre, majd konfigurálhat egy adatbázist az Azure SQL Database-ben:

  || Önálló adatbázis | Felügyelt példány |
  |:--- |:--- |:---|
  | Létrehozás| [Portál](sql-database-single-database-get-started.md) | [Portál](sql-database-managed-instance-get-started.md) |
  || [parancssori felület](scripts/sql-database-create-and-configure-database-cli.md) | [parancssori felület](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [Powershell](scripts/sql-database-create-and-configure-database-powershell.md) | [Powershell](scripts/sql-database-create-configure-managed-instance-powershell.md) |
  | Konfigurálás | [Kiszolgálószintű IP-tűzfal szabály](sql-database-server-level-firewall-rule.md)| [Kapcsolódás virtuális gépről](sql-database-managed-instance-configure-vm.md)|
  |||[Helyszíni kapcsolat](sql-database-managed-instance-configure-p2s.md)
  |Adatok betöltése|A kalandworks betöltve egy rövid útmutató|[Széles világbeli importőrök visszaállítása](sql-database-managed-instance-get-started-restore.md)
  |||Kalandorok visszaállítása vagy importálása [a BACPAC-fájlból](sql-database-import.md) a [GitHubról](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)|
  |||

  > [!IMPORTANT]
  > A cikkben szereplő parancsfájlok a Kalandorbolt-adatbázis használatára íródnak. Felügyelt példány esetén vagy importálnia kell a Kalandorbolt-adatbázist egy példányadatbázisba, vagy módosítania kell a cikkparancsfájljait a Wide World Importers adatbázis használatához.

## <a name="install-visual-studio-code"></a>A Visual Studio Code telepítése

Győződjön meg arról, hogy telepítette a legújabb [Visual Studio kódot,](https://code.visualstudio.com/Download) és betöltötte az [mssql bővítményt.](https://aka.ms/mssql-marketplace) Az mssql bővítmény telepítésével kapcsolatos útmutatásért [lásd: Vs Code](https://docs.microsoft.com/sql/linux/sql-server-linux-develop-use-vscode#install-and-start-visual-studio-code) és [mssql for Visual Studio Code ](https://marketplace.visualstudio.com/items?itemName=ms-mssql.mssql).

## <a name="configure-visual-studio-code"></a>Visual Studio-kód konfigurálása

### <a name="mac-os"></a>**Mac OS**

MacOS esetén telepítenie kell az OpenSSL-t, amely az mssql kiterjesztés által használt .NET Core előfeltétele. Nyissa meg a terminált, és írja be a következő parancsokat a **brew** és az **OpenSSL**telepítéséhez.

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

## <a name="get-sql-server-connection-information"></a>SQL-kiszolgálókapcsolati adatok beszerezése

Az Azure SQL-adatbázishoz való csatlakozáshoz szükséges kapcsolati információk beszerezése. A közelgő eljárásokhoz szüksége lesz a teljesen minősített kiszolgáló- vagy állomásnévre, az adatbázis nevére és bejelentkezési adataira.

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com/)

2. Keresse meg az **SQL-adatbázisok** vagy az **SQL felügyelt példányok** lapot.

3. Az **Áttekintés** lapon tekintse át a **kiszolgáló neve** melletti teljesen minősített kiszolgálónevet egyetlen adatbázishoz, vagy a teljesen minősített kiszolgálónevet a felügyelt példány **gazdagépe** mellett. A kiszolgáló vagy az állomásnév másolásához mutasson rá, és válassza a **Másolás ikont.**

## <a name="set-language-mode-to-sql"></a>A nyelvmód SQL értékre állítása

A Visual Studio Code programban állítsa a nyelvi módot **SQL-re** az mssql parancsok és a T-SQL IntelliSense engedélyezéséhez.

1. Nyisson meg egy új Visual Studio Code-ablakot.

2. Nyomja le a Ctrl N **billentyűt.**+**N** Megnyílik egy új egyszerű szöveges fájl.

3. Az állapotsor jobb alsó sarkában válassza az **Egyszerű szöveg** lehetőséget.

4. A **Megnyíló Nyelvi mód kiválasztása** legördülő menüben válassza az **SQL**lehetőséget.

## <a name="connect-to-your-database"></a>Csatlakozás az adatbázishoz

A Visual Studio Code segítségével kapcsolatot hozhat létre az Azure SQL-adatbáziskiszolgálóval.

> [!IMPORTANT]
> A folytatás előtt győződjön meg arról, hogy a kiszolgáló és a bejelentkezési adatok készen állnak. Ha elkezdi beírni a kapcsolatprofil adatait, ha a Fókuszt a Visual Studio-kódról módosítja, újra kell indítania a profil létrehozását.

1. A Visual Studio-kódban nyomja le a **Ctrl+Shift+P** (vagy **F1)** billentyűkombinációt a parancspaletta megnyitásához.

2. Válassza **az MS SQL:Connect** és **az Enter**lehetőséget.

3. Válassza **a Kapcsolatprofil létrehozása lehetőséget.**

4. Az új profil kapcsolati tulajdonságainak megadásához kövesse az utasításokat. Az egyes értékek megadása után válassza az **Enter** gombot a folytatáshoz.

   | Tulajdonság       | Ajánlott érték | Leírás |
   | ------------ | ------------------ | ------------------------------------------------- |
   | **Kiszolgáló neve** | A teljes kiszolgálónév | Olyasmi, mint: **mynewserver20170313.database.windows.net**. |
   | **Adatbázis neve** | mySampleDatabase | Az adatbázis, amelyhez csatlakozni lehet. |
   | **Hitelesítés** | SQL-bejelentkezés| Ez az oktatóanyag AZ SQL-hitelesítést használja. |
   | **Felhasználónév** | Felhasználónév | A kiszolgáló létrehozásához használt kiszolgálófelügyeleti fiók felhasználóneve. |
   | **Jelszó (SQL-bejelentkezés)** | Jelszó | A kiszolgáló felügyeleti fiókjának a kiszolgáló létrehozásához használt jelszava. |
   | **Menti a jelszót?** | Igen vagy nem | Válassza az **Igen** lehetőséget, ha nem szeretné minden alkalommal megadni a jelszót. |
   | **Adja meg a profil kívánt nevét** | Profilnév, például **mySampleProfile** | A mentett profil felgyorsítja a kapcsolatot a későbbi bejelentkezések. |

   Ha sikeres, megjelenik egy értesítés arról, hogy a profil létrejött és csatlakoztatva van.

## <a name="query-data"></a>Adatok lekérdezése

Futtassa a következő [SELECT](https://msdn.microsoft.com/library/ms189499.aspx) Transact-SQL utasítást a kategória szerint a 20 legfontosabb termék lekérdezéséhez.

1. A szerkesztőablakban illessze be a következő SQL-lekérdezést.

   ```sql
   SELECT pc.Name as CategoryName, p.name as ProductName
   FROM [SalesLT].[ProductCategory] pc
   JOIN [SalesLT].[Product] p
   ON pc.productcategoryid = p.productcategoryid;
   ```

2. A Ctrl**Shift**+**E** **billentyűvel**+futtatja `Product` a lekérdezést, és megjeleníti a táblák ból `ProductCategory` származó eredményeket.

    ![Adatok lekérdezése két táblából](./media/sql-database-connect-query-vscode/query.png)

## <a name="insert-data"></a>Adat beszúrása

[Futtassa](https://msdn.microsoft.com/library/ms174335.aspx) a következő INSERT Transact-SQL utasítást, ha új terméket szeretne hozzáadni a `SalesLT.Product` táblához.

1. Cserélje le az előző lekérdezést erre.

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

2. Új sor beszúrásához nyomja le `Product` a **Ctrl**+**Shift**+**E** billentyűt.

## <a name="update-data"></a>Adatok frissítése

Futtassa a következő [UPDATE](https://msdn.microsoft.com/library/ms177523.aspx) Transact-SQL utasítást a hozzáadott termék frissítéséhez.

1. Az előző lekérdezés lecserélése erre:

   ```sql
   UPDATE [SalesLT].[Product]
   SET [ListPrice] = 125
   WHERE Name = 'myNewProduct';
   ```

2. A táblázat megadott sorának frissítéséhez `Product` nyomja **le**+**Shift**+az**E** billentyűt.

## <a name="delete-data"></a>Adat törlése

Az új termék eltávolításához futtassa a következő [DELETE](https://docs.microsoft.com/sql/t-sql/statements/delete-transact-sql) Transact-SQL utasítást.

1. Az előző lekérdezés lecserélése erre:

   ```sql
   DELETE FROM [SalesLT].[Product]
   WHERE Name = 'myNewProduct';
   ```

2. A táblázat megadott sorának törléséhez `Product` nyomja le az**Shift**+**E** **billentyűt.**+

## <a name="next-steps"></a>További lépések

- Ha az SQL Server Management Studio használatával szeretne csatlakozni és lekérdezést szeretne, [olvassa el a rövid útmutató: Az SQL Server Management Studio használata Azure SQL-adatbázishoz való csatlakozáshoz és lekérdezési adatokhoz című témakört.](sql-database-connect-query-ssms.md)
- Ha az Azure Portalhasználatával szeretne csatlakozni és lekérdezni, olvassa el a [rövid útmutató: Az SQL Query-szerkesztő használata az Azure Portalon az adatok csatlakoztatásához és lekérdezéséhez című témakört.](sql-database-connect-query-portal.md)
- Az MSDN magazin Visual Studio Code használatáról szóló cikkéhez lásd az [Adatbázis IDE létrehozása az MSSQL bővítménnyel blogbejegyzést](https://msdn.microsoft.com/magazine/mt809115).
