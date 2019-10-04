---
title: 'Azure Portal: Lekérdezés Azure SQL Database a Query Editor használatával | Microsoft Docs'
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
ms.date: 06/28/2019
ms.openlocfilehash: 3702c88d0a5cdc7aa1f854f71e3aee8a42d9c22c
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68569161"
---
# <a name="quickstart-use-the-azure-portals-sql-query-editor-to-connect-and-query-data"></a>Gyors útmutató: A Azure Portal SQL-lekérdezés-szerkesztője segítségével csatlakozhat és lekérdezheti azokat

Az SQL-Lekérdezésszerkesztő egy SQL-lekérdezések végrehajtása az Azure SQL Database vagy Azure SQL Data Warehouse egyszerű módot biztosít az Azure portal böngésző eszköz. Ebben a rövid útmutatóban a lekérdezéstervező segítségével csatlakozhat egy SQL-adatbázishoz, majd a Transact-SQL-utasítások futtatásával lekérdezheti, beszúrhatja, frissítheti és törölheti az adatokat.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez a következőkre lesz szüksége:

- Azure SQL-adatbázis. Az alábbi rövid útmutatók segítségével hozhat létre és konfigurálhat egy adatbázist Azure SQL Databaseban:

  || Önálló adatbázis |
  |:--- |:--- |
  | Hozzon létre| [Portál](sql-database-single-database-get-started.md) |
  || [Parancssori felület](scripts/sql-database-create-and-configure-database-cli.md) |
  || [PowerShell](scripts/sql-database-create-and-configure-database-powershell.md) |
  | Konfigurálás | [Kiszolgálói szintű IP-tűzfalszabály](sql-database-server-level-firewall-rule.md)|
  |||

> [!NOTE]
> A lekérdezés-szerkesztő a 443-es és a 1443-es portot használja a kommunikációhoz.  Győződjön meg arról, hogy engedélyezte a kimenő HTTPS-forgalmat ezeken a portokon. Emellett hozzá kell adnia a kimenő IP-címét a kiszolgáló engedélyezett tűzfalszabályok számára az adatbázisok és az adattárházak eléréséhez.

## <a name="sign-in-the-azure-portal"></a>Jelentkezzen be az Azure Portalon

Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

## <a name="connect-using-sql-authentication"></a>Csatlakozás az SQL-hitelesítés használata

1. Válassza ki **SQL-adatbázisok** a bal oldali menüben válassza ki **mySampleDatabase**.

2. A bal oldali menüben, kereséséhez és kijelöléséhez **Lekérdezésszerkesztő (előzetes verzió)** . A **bejelentkezési** lap jelenik meg.

    ![lekérdezésszerkesztő keresése](./media/sql-database-connect-query-portal/find-query-editor.PNG)

3. Az a **engedélyezési típus** legördülő menüjében válassza **SQL Server-hitelesítés** , és adja meg a felhasználói Azonosítót és jelszót a kiszolgálói rendszergazdai fiók az adatbázis létrehozásakor használt.

    ![bejelentkezés](./media/sql-database-connect-query-portal/login-menu.png)

4. Kattintson az **OK** gombra.


## <a name="connect-using-azure-active-directory"></a>Csatlakozás az Azure Active Directory használatával

Az Active Directory (AD) rendszergazda konfigurálása lehetővé teszi, hogy egyetlen identitással jelentkezzen be az Azure portal és az SQL-adatbázis. Az SQL Server egy AD-rendszergazda konfigurálása az alábbi lépésekkel.

> [!NOTE]
> * E-mail-fiókok (például outlook.com, gmail.com, yahoo.com és így tovább) még nem támogatottak az AD-rendszergazdaként. Ellenőrizze, hogy válassza ki a felhasználó vagy natív módon az Azure AD-ben létrehozott, vagy összevonták az Azure ad-ben.
> * Jelentkezzen be az Azure AD-rendszergazda nem működik olyan fiókokkal, amelyeken 2 többtényezős hitelesítés engedélyezve van.

1. Válassza ki **összes erőforrás** elemet a bal oldali menüben, majd az SQL Serverhez.

2. Az SQL Server **Beállítások** menüjében válassza a **Active Directory rendszergazda**elemet.

3. AD admin oldal eszköztárán válassza **rendszergazda beállítása** , és válassza ki a felhasználó vagy csoport az AD-rendszergazdaként

    ![az Active Directory kiválasztása](./media/sql-database-connect-query-portal/select-active-directory.png)

4. AD admin oldal eszköztárán válassza **mentése**.

5. Keresse meg a **mySampleDatabase** adatbázisra, majd a bal oldali menüből válassza **Lekérdezésszerkesztő (előzetes verzió)** . A **bejelentkezési** lap jelenik meg. Ha Ön AD-rendszergazda, akkor a jobb oldalon **Active Directory egyszeri bejelentkezés**alatt megjelenik egy üzenet, amely azt jelzi, hogy be van jelentkezve.

6. Kattintson az **OK** gombra.


## <a name="view-data"></a>Adatok megtekintése

1. Miután, a hitelesítés, illessze be a következő SQL kategóriánként az első 20 terméket beolvasni a Lekérdezésszerkesztő.

   ```sql
    SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
    FROM SalesLT.ProductCategory pc
    JOIN SalesLT.Product p
    ON pc.productcategoryid = p.productcategoryid;
   ```

2. Válassza az eszköztár **futtatása** és tekintse át a kimenetet a a **eredmények** ablaktáblán.

![lekérdezésszerkesztő: eredmények](./media/sql-database-connect-query-portal/query-editor-results.png)

## <a name="insert-data"></a>Adat beszúrása

Az alábbi [Insert](https://msdn.microsoft.com/library/ms174335.aspx) Transact-SQL-utasítás futtatásával adjon hozzá egy új terméket `SalesLT.Product` a táblához.

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


2. Válassza a **Futtatás** elemet egy új sor `Product` táblázatba való beszúrásához. Az **üzenetek** ablaktábla a **lekérdezés sikerességét jeleníti meg: Érintett sorok: 1**.


## <a name="update-data"></a>Adatok frissítése

Futtassa az alábbi [Update](https://msdn.microsoft.com/library/ms177523.aspx) Transact-SQL-utasítást az új termék módosításához.

1. Ehhez cserélje le az előző lekérdezést.

   ```sql
   UPDATE [SalesLT].[Product]
   SET [ListPrice] = 125
   WHERE Name = 'myNewProduct';
   ```

2. Válassza a **Futtatás** lehetőséget a `Product` tábla megadott sorának frissítéséhez. Az **üzenetek** ablaktábla a **lekérdezés sikerességét jeleníti meg: Érintett sorok: 1**.

## <a name="delete-data"></a>Adat törlése

Futtassa az alábbi [delete](https://msdn.microsoft.com/library/ms189835.aspx) Transact-SQL utasítást az új termék eltávolításához.

1. Cserélje le az előző lekérdezés erre:

   ```sql
   DELETE FROM [SalesLT].[Product]
   WHERE Name = 'myNewProduct';
   ```

2. Válassza a **Futtatás** lehetőséget a `Product` tábla megadott sorának törléséhez. Az **üzenetek** ablaktábla a **lekérdezés sikerességét jeleníti meg: Érintett sorok: 1**.


## <a name="query-editor-considerations"></a>Lekérdezés-szerkesztő szempontok

Ha a Lekérdezésszerkesztő néhány dolgot lehet.

* A lekérdezés-szerkesztő a 443-es és a 1443-es portot használja a kommunikációhoz.  Győződjön meg arról, hogy engedélyezte a kimenő HTTPS-forgalmat ezeken a portokon. Emellett hozzá kell adnia a kimenő IP-címét a kiszolgáló engedélyezett tűzfalszabályok számára az adatbázisok és az adattárházak eléréséhez.

* F5 billentyű lenyomásával frissíti a Lekérdezésszerkesztő lapja, és bármilyen szerkesztett lekérdezést elvész.

* A lekérdezés-szerkesztő nem támogatja az `master` adatbázishoz való kapcsolódást.

* A lekérdezés végrehajtása 5 perces időtúllépés van.

* A Lekérdezésszerkesztő csak geográfiai adattípusokhoz tartozó hengervetületet támogatja.

* Nincs adatbázis-táblák és nézetek tartozó IntelliSense nem támogatott. A szerkesztő azonban támogatja az automatikus kiegészítés, amely már beírt nevek alapján.


## <a name="next-steps"></a>További lépések

Az Azure SQL-adatbázisokban támogatott Transact-SQL kapcsolatos további információkért lásd: [feloldása Transact-SQL különbségek az SQL Database áttelepítése során](sql-database-transact-sql-information.md).
