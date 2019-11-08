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
ms.openlocfilehash: 3990d7ec63c312d38168fe76269e1a920f1a6817
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73827112"
---
# <a name="quickstart-use-the-azure-portals-sql-query-editor-to-connect-and-query-data"></a>Rövid útmutató: a Azure Portal SQL-lekérdezési szerkesztőjével csatlakozhat, és lekérdezheti azokat

Az SQL-lekérdezés szerkesztője egy Azure Portal böngésző eszköz, amely egyszerű módszert kínál az SQL-lekérdezések futtatására Azure SQL Database vagy Azure SQL Data Warehouse. Ebben a rövid útmutatóban a lekérdezéstervező segítségével csatlakozhat egy SQL-adatbázishoz, majd a Transact-SQL-utasítások futtatásával lekérdezheti, beszúrhatja, frissítheti és törölheti az adatokat.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez a következőkre lesz szüksége:

- Azure SQL-adatbázis. Az alábbi rövid útmutatók segítségével hozhat létre és konfigurálhat egy adatbázist Azure SQL Databaseban:

  || Önálló adatbázis |
  |:--- |:--- |
  | Létrehozás| [Portál](sql-database-single-database-get-started.md) |
  || [Parancssori felület](scripts/sql-database-create-and-configure-database-cli.md) |
  || [PowerShell](scripts/sql-database-create-and-configure-database-powershell.md) |
  | Konfigurálás | [Kiszolgálói szintű IP-tűzfalszabály](sql-database-server-level-firewall-rule.md)|
  |||

> [!NOTE]
> A lekérdezés-szerkesztő a 443-es és a 1443-es portot használja a kommunikációhoz.  Győződjön meg arról, hogy engedélyezte a kimenő HTTPS-forgalmat ezeken a portokon. Emellett hozzá kell adnia a kimenő IP-címét a kiszolgáló engedélyezett tűzfalszabályok számára az adatbázisok és az adattárházak eléréséhez.

## <a name="sign-in-the-azure-portal"></a>Jelentkezzen be a Azure Portal

Jelentkezzen be az [Azure portálra](https://portal.azure.com/).

## <a name="connect-using-sql-authentication"></a>SQL-hitelesítéssel történő összekapcsolás

1. Nyissa meg az Azure Portal egy SQL-adatbázishoz való kapcsolódáshoz. Keresse meg és válassza ki az **SQL-adatbázisokat**.

    ![Navigáljon az SQL Database listára, Azure Portal](./media/sql-database-connect-query-portal/search-for-sql-databases.png)

2. Válassza ki az SQL-adatbázist.

    ![Válasszon ki egy SQL-adatbázist, Azure Portal](./media/sql-database-connect-query-portal/select-a-sql-database.png)

3. Az **SQL Database** menüben válassza a **Lekérdezés-szerkesztő (előzetes verzió)** lehetőséget.

    ![lekérdezésszerkesztő keresése](./media/sql-database-connect-query-portal/find-query-editor.PNG)

4. A **bejelentkezési** oldalon az **SQL Server-hitelesítés** címke alatt adja meg az adatbázis létrehozásához használt kiszolgálói rendszergazdai fiók **bejelentkezési** azonosítóját és **jelszavát** . Ezután kattintson az **OK** gombra.

    ![bejelentkezés](./media/sql-database-connect-query-portal/login-menu.png)

## <a name="connect-using-azure-active-directory"></a>Kapcsolat Azure Active Directory használatával

Egy Azure Active Directory (Azure AD) rendszergazdájának konfigurálása lehetővé teszi, hogy egyetlen identitás használatával jelentkezzen be a Azure Portalba és az SQL-adatbázisába. Az alábbi lépések végrehajtásával konfigurálhat egy Azure AD-rendszergazdát az SQL Serverhez.

> [!NOTE]
> * Az e-mail-fiókok (például outlook.com, gmail.com, yahoo.com stb.) még nem támogatottak az Azure AD-rendszergazdák számára. Győződjön meg arról, hogy az Azure AD-ben natív módon létrehozott vagy az Azure AD-ba összevont felhasználót választ.
> * Az Azure AD rendszergazdai bejelentkezés nem működik olyan fiókokkal, amelyeken engedélyezve van a 2 faktoros hitelesítés.

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

1. A hitelesítés után illessze be a következő SQL-t a lekérdezés-szerkesztőbe az első 20 termék kategóriánkénti lekéréséhez.

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


2. Válassza a **Futtatás** lehetőséget egy új sor beszúrásához a `Product` táblában. Az **üzenetek** ablaktábla a **lekérdezés sikerességét jeleníti meg: érintett sorok: 1**.


## <a name="update-data"></a>Adatok frissítése

Futtassa az alábbi [Update](https://msdn.microsoft.com/library/ms177523.aspx) Transact-SQL-utasítást az új termék módosításához.

1. Cserélje le az előző lekérdezést ezzel az eggyel.

   ```sql
   UPDATE [SalesLT].[Product]
   SET [ListPrice] = 125
   WHERE Name = 'myNewProduct';
   ```

2. Válassza a **Futtatás** lehetőséget a `Product` tábla megadott sorának frissítéséhez. Az **üzenetek** ablaktábla a **lekérdezés sikerességét jeleníti meg: érintett sorok: 1**.

## <a name="delete-data"></a>Adat törlése

Futtassa az alábbi [delete](https://msdn.microsoft.com/library/ms189835.aspx) Transact-SQL utasítást az új termék eltávolításához.

1. Cserélje le az előző lekérdezést a következőre:

   ```sql
   DELETE FROM [SalesLT].[Product]
   WHERE Name = 'myNewProduct';
   ```

2. A **Futtatás** gombra kattintva törölheti a megadott sort a `Product` táblában. Az **üzenetek** ablaktábla a **lekérdezés sikerességét jeleníti meg: érintett sorok: 1**.


## <a name="query-editor-considerations"></a>A lekérdezési szerkesztő szempontjai

A lekérdezés-szerkesztő használatakor néhány tudnivalót is megtudhat.

* A lekérdezés-szerkesztő a 443-es és a 1443-es portot használja a kommunikációhoz.  Győződjön meg arról, hogy engedélyezte a kimenő HTTPS-forgalmat ezeken a portokon. Emellett hozzá kell adnia a kimenő IP-címét a kiszolgáló engedélyezett tűzfalszabályok számára az adatbázisok és az adattárházak eléréséhez.

* Az F5 billentyű lenyomásával frissíti a lekérdezés-szerkesztő oldalt, és a folyamatban lévő összes lekérdezés elvész.

* A Lekérdezéstervező nem támogatja a `master`-adatbázishoz való kapcsolódást.

* A lekérdezés végrehajtásának 5 perces időtúllépése van.

* A lekérdezéstervező csak a földrajzi adattípusok hengeres leképezését támogatja.

* Nem támogatott az IntelliSense az adatbázis-táblákhoz és a nézetekhez. A szerkesztő azonban támogatja az automatikus kiegészítést a már beírt neveknél.


## <a name="next-steps"></a>További lépések

Ha többet szeretne megtudni az Azure SQL-adatbázisokban támogatott Transact-SQL-ről, tekintse meg a [Transact-SQL különbségek feloldása az áttelepítés során SQL Databasere](sql-database-transact-sql-information.md)című témakört.
