---
title: 'Azure Portal: lekérdezés a lekérdezés-szerkesztő használatával'
description: Megtudhatja, hogyan csatlakozhat az SQL Database-hez az Azure Portalon az SQL-lekérdezésszerkesztő segítségével. Ezután futtasson Transact-SQL (T-SQL) utasításokat az adatok lekérdezéséhez és szerkesztéséhez.
keywords: csatlakozás SQL Database-adatbázishoz, Azure Portal, portál, lekérdezésszerkesztő
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: Ninarn
ms.author: ninarn
ms.reviewer: carlrab
ms.date: 10/24/2019
ms.openlocfilehash: b3ccc2a5343cf02127990dca80a1300959fa06a3
ms.sourcegitcommit: 20429bc76342f9d365b1ad9fb8acc390a671d61e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/11/2020
ms.locfileid: "79087185"
---
# <a name="quickstart-use-the-azure-portals-sql-query-editor-to-connect-and-query-data"></a>Gyors útmutató: Az Azure portal SQL-Lekérdezésszerkesztő való csatlakozás és adatlekérdezés használja.

Az SQL-Lekérdezésszerkesztő egy SQL-lekérdezések végrehajtása az Azure SQL Database vagy Azure SQL Data Warehouse egyszerű módot biztosít az Azure portal böngésző eszköz. Ebben a rövid útmutatóban a lekérdezéstervező segítségével csatlakozhat egy SQL-adatbázishoz, majd a Transact-SQL-utasítások futtatásával lekérdezheti, beszúrhatja, frissítheti és törölheti az adatokat.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez a következőkre lesz szüksége:

- Azure SQL-adatbázis. Az alábbi rövid útmutatók segítségével hozhat létre és konfigurálhat egy adatbázist Azure SQL Databaseban:

  || Önálló adatbázis |
  |:--- |:--- |
  | Létrehozás| [Portal](sql-database-single-database-get-started.md) |
  || [Parancssori felület](scripts/sql-database-create-and-configure-database-cli.md) |
  || [PowerShell](scripts/sql-database-create-and-configure-database-powershell.md) |
  | Konfigurálás | [Kiszolgálói szintű IP-tűzfalszabály](sql-database-server-level-firewall-rule.md)|
  |||

> [!NOTE]
> A lekérdezés-szerkesztő a 443-es és a 1443-es portot használja a kommunikációhoz.  Győződjön meg arról, hogy engedélyezte a kimenő HTTPS-forgalmat ezeken a portokon. Emellett hozzá kell adnia a kimenő IP-címét a kiszolgáló engedélyezett tűzfalszabályok számára az adatbázisok és az adattárházak eléréséhez.

## <a name="sign-in-the-azure-portal"></a>Jelentkezzen be az Azure Portalon

Jelentkezzen be az [Azure Portal](https://portal.azure.com/).

## <a name="connect-using-sql-authentication"></a>Csatlakozás az SQL-hitelesítés használata

1. Nyissa meg az Azure Portal egy SQL-adatbázishoz való kapcsolódáshoz. Keresse meg és válassza ki az **SQL-adatbázisokat**.

    ![Navigáljon az SQL Database listára, Azure Portal](./media/sql-database-connect-query-portal/search-for-sql-databases.png)

2. Válassza ki az SQL-adatbázist.

    ![Válasszon ki egy SQL-adatbázist, Azure Portal](./media/sql-database-connect-query-portal/select-a-sql-database.png)

3. Az **SQL Database** menüben válassza a **Lekérdezés-szerkesztő (előzetes verzió)** lehetőséget.

    ![lekérdezésszerkesztő keresése](./media/sql-database-connect-query-portal/find-query-editor.PNG)

4. A **bejelentkezési** oldalon az **SQL Server-hitelesítés** címke alatt adja meg az adatbázis létrehozásához használt kiszolgálói rendszergazdai fiók **bejelentkezési** azonosítóját és **jelszavát** . Ezután kattintson az **OK** gombra.

    ![bejelentkezés](./media/sql-database-connect-query-portal/login-menu.png)

## <a name="connect-using-azure-active-directory"></a>Csatlakozás az Azure Active Directory használatával

Egy Azure Active Directory (Azure AD) rendszergazdájának konfigurálása lehetővé teszi, hogy egyetlen identitás használatával jelentkezzen be a Azure Portalba és az SQL-adatbázisába. Az alábbi lépések végrehajtásával konfigurálhat egy Azure AD-rendszergazdát az SQL Serverhez.

> [!NOTE]
> * Az e-mail-fiókok (például outlook.com, gmail.com, yahoo.com stb.) még nem támogatottak az Azure AD-rendszergazdák számára. Ellenőrizze, hogy válassza ki a felhasználó vagy natív módon az Azure AD-ben létrehozott, vagy összevonták az Azure ad-ben.
> * Jelentkezzen be az Azure AD-rendszergazda nem működik olyan fiókokkal, amelyeken 2 többtényezős hitelesítés engedélyezve van.

1. A Azure Portal menüben vagy a **Kezdőlap** lapon válassza a **minden erőforrás**elemet.

2. Válassza ki az SQL Servert.

3. Az **SQL Server** menüjének **Beállítások**területén válassza a **Active Directory rendszergazda**elemet.

4. Az SQL Server **Active Directory felügyeleti** oldal eszköztárán válassza a **rendszergazda beállítása** elemet, és válassza ki a felhasználót vagy csoportot az Azure ad-rendszergazdaként.

    ![az Active Directory kiválasztása](./media/sql-database-connect-query-portal/select-active-directory.png)

5. A **rendszergazda hozzáadása** oldalon a keresőmezőbe írja be a keresendő felhasználót vagy csoportot, válassza ki rendszergazdaként, majd kattintson a **kiválasztás** gombra.

6. Vissza az SQL Server **Active Directory felügyeleti** oldal eszköztárán válassza a **Mentés**lehetőséget.

7. Az **SQL Server** menüben válassza az **SQL-adatbázisok**lehetőséget, majd válassza ki az SQL-adatbázist.

8. Az **SQL Database** menüben válassza a **Lekérdezés-szerkesztő (előzetes verzió)** lehetőséget. Ha Ön Azure AD-rendszergazda, a **bejelentkezési** oldalon a **Active Directory hitelesítési** címke alatt megjelenik egy üzenet, amely azt jelzi, hogy be van jelentkezve. Ezután válassza a **Folytatás másként** *\<a felhasználó vagy a csoport azonosítója >* gombra.

## <a name="view-data"></a>Adatok megtekintése

1. Miután, a hitelesítés, illessze be a következő SQL kategóriánként az első 20 terméket beolvasni a Lekérdezésszerkesztő.

   ```sql
    SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
    FROM SalesLT.ProductCategory pc
    JOIN SalesLT.Product p
    ON pc.productcategoryid = p.productcategoryid;
   ```

2. Az eszköztáron válassza a **Futtatás** lehetőséget, majd tekintse át a kimenetet az **eredmények** ablaktáblán.

   ![lekérdezésszerkesztő: eredmények](./media/sql-database-connect-query-portal/query-editor-results.png)

## <a name="insert-data"></a>Adat beszúrása

A következő [Insert](https://msdn.microsoft.com/library/ms174335.aspx) Transact-SQL-utasítás futtatásával vegyen fel egy új terméket a `SalesLT.Product` táblába.

1. Ehhez cserélje le az előző lekérdezést.

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


2. Válassza a **Futtatás** lehetőséget egy új sor beszúrásához a `Product` táblában. Az **üzenetek** ablaktábla a **lekérdezés sikerességét jeleníti meg: érintett sorok: 1**.


## <a name="update-data"></a>Adatok frissítése

Futtassa az alábbi [Update](https://msdn.microsoft.com/library/ms177523.aspx) Transact-SQL-utasítást az új termék módosításához.

1. Ehhez cserélje le az előző lekérdezést.

   ```sql
   UPDATE [SalesLT].[Product]
   SET [ListPrice] = 125
   WHERE Name = 'myNewProduct';
   ```

2. Válassza a **Futtatás** lehetőséget a `Product` tábla megadott sorának frissítéséhez. Az **üzenetek** ablaktábla a **lekérdezés sikerességét jeleníti meg: érintett sorok: 1**.

## <a name="delete-data"></a>Adat törlése

Futtassa az alábbi [delete](https://msdn.microsoft.com/library/ms189835.aspx) Transact-SQL utasítást az új termék eltávolításához.

1. Cserélje le az előző lekérdezés erre:

   ```sql
   DELETE FROM [SalesLT].[Product]
   WHERE Name = 'myNewProduct';
   ```

2. A **Futtatás** gombra kattintva törölheti a megadott sort a `Product` táblában. Az **üzenetek** ablaktábla a **lekérdezés sikerességét jeleníti meg: érintett sorok: 1**.


## <a name="query-editor-considerations"></a>Lekérdezés-szerkesztő szempontok

Ha a Lekérdezésszerkesztő néhány dolgot lehet.

* A lekérdezés-szerkesztő a 443-es és a 1443-es portot használja a kommunikációhoz.  Győződjön meg arról, hogy engedélyezte a kimenő HTTPS-forgalmat ezeken a portokon. Emellett hozzá kell adnia a kimenő IP-címét a kiszolgáló engedélyezett tűzfalszabályok számára az adatbázisok és az adattárházak eléréséhez.

* A lekérdezés-szerkesztő privát hivatkozással működik anélkül, hogy az ügyfél IP-címét hozzá kellene adni a SQL Database tűzfalhoz

* F5 billentyű lenyomásával frissíti a Lekérdezésszerkesztő lapja, és bármilyen szerkesztett lekérdezést elvész.

* A Lekérdezéstervező nem támogatja a `master`-adatbázishoz való kapcsolódást.

* A lekérdezés végrehajtása 5 perces időtúllépés van.

* A Lekérdezésszerkesztő csak geográfiai adattípusokhoz tartozó hengervetületet támogatja.

* Nincs adatbázis-táblák és nézetek tartozó IntelliSense nem támogatott. A szerkesztő azonban támogatja az automatikus kiegészítés, amely már beírt nevek alapján.




## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni az Azure SQL-adatbázisokban támogatott Transact-SQL-ről, tekintse meg a [Transact-SQL különbségek feloldása az áttelepítés során SQL Databasere](sql-database-transact-sql-information.md)című témakört.
