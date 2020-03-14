---
title: SQL Database lekérdezése a Azure Portal a lekérdezés-szerkesztő használatával
description: Ismerje meg, hogyan futtathat Transact-SQL-(T-SQL-) lekérdezéseket egy Azure SQL Databaseon a lekérdezéstervező használatával.
keywords: Kapcsolódás az SQL Database-hez, SQL Database lekérdezése, Azure Portal, portál, lekérdezés-szerkesztő
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: Ninarn
ms.author: ninarn
ms.reviewer: carlrab
ms.date: 03/12/2020
ms.openlocfilehash: 5847ef3033d257faef4831785b8abd864d54e835
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79209605"
---
# <a name="quickstart-use-the-azure-portals-query-editor-to-query-a-sql-database"></a>Gyors útmutató: SQL-adatbázis lekérdezése a Azure Portal lekérdezési szerkesztőjével

A lekérdezés-szerkesztő egy eszköz a Azure Portalban az SQL-lekérdezések Azure SQL Database-vagy Azure SQL Data Warehouse-alapú futtatásához. 

Ebben a rövid útmutatóban a lekérdezéstervező segítségével futtatja a Transact-SQL (T-SQL) lekérdezéseket egy Azure SQL Database-adatbázison.


## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató elvégzéséhez a AdventureWorksLT mintaadatbázis szükséges. Ha nem rendelkezik a AdventureWorksLT SQL Database egy működő példányával, a következő rövid útmutató gyorsan létrehoz egyet:

- [Gyors útmutató: egyetlen Azure SQL-adatbázis létrehozása a Azure Portal, a PowerShell vagy az Azure CLI használatával](sql-database-single-database-get-started.md) 

### <a name="configure-network-settings"></a>A hálózati beállítások konfigurálása

Ha a lekérdezési szerkesztőben a következő hibák valamelyike jelenik meg: *a helyi hálózati beállítások megakadályozhatják, hogy a lekérdezéstervező lekérdezéseket bocsásson ki. A hálózati beállítások konfigurálására*, illetve a *kiszolgálóhoz való kapcsolódásra vonatkozó utasításokért kattintson ide. Ez a helyi tűzfal-konfigurációval vagy a hálózati proxy-beállításokkal kapcsolatos problémára utalhat, és*a következő fontos információk segítenek a megoldásban:

> [!IMPORTANT]
> A lekérdezés-szerkesztő a 443-es és a 1443-es portot használja a kommunikációhoz. Győződjön meg arról, hogy engedélyezte a kimenő HTTPS-forgalmat ezeken a portokon. Emellett [hozzá kell adnia a kimenő IP-címét a kiszolgáló engedélyezett tűzfalszabályok](sql-database-server-level-firewall-rule.md) számára az adatbázisok és az adattárházak eléréséhez.


## <a name="open-the-sql-database-query-editor"></a>A SQL Database lekérdezés-szerkesztő megnyitása

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com/) , és válassza ki a lekérdezni kívánt SQL-adatbázist.

2. Az **SQL Database** menüben válassza a **Lekérdezés-szerkesztő (előzetes verzió)** lehetőséget.

    ![lekérdezésszerkesztő keresése](./media/sql-database-connect-query-portal/find-query-editor.PNG)


## <a name="establish-a-connection-to-the-database"></a>Kapcsolat létesítése az adatbázissal

Annak ellenére, hogy bejelentkezett a portálra, a hitelesítő adatokat is meg kell adnia az SQL-adatbázis eléréséhez. Az adatbázishoz való kapcsolódáshoz SQL-hitelesítés vagy Azure Active Directory használatával csatlakozhat.

### <a name="connect-using-sql-authentication"></a>Csatlakozás SQL-hitelesítés használatával

1. A **Bejelentkezés** lap **SQL Server-hitelesítés**területén adja meg az adatbázishoz hozzáféréssel rendelkező felhasználó **felhasználónevét** és **jelszavát** . Ha nem biztos benne, használja az adatbázis-kiszolgálójának kiszolgáló-rendszergazdájának felhasználónevét és jelszavát.

    ![bejelentkezés](./media/sql-database-connect-query-portal/login-menu.png)

2. Kattintson az **OK** gombra.


### <a name="connect-using-azure-active-directory"></a>Csatlakozás az Azure Active Directory használatával

Egy Azure Active Directory (Azure AD) rendszergazdájának konfigurálása lehetővé teszi, hogy egyetlen identitás használatával jelentkezzen be a Azure Portalba és az SQL-adatbázisába. Az adatbázishoz az Azure AD-vel való kapcsolódáshoz kövesse az alábbi lépéseket az Azure AD-rendszergazda konfigurálásához az SQL Serverhez.

> [!NOTE]
> * Az e-mail-fiókok (például outlook.com, gmail.com, yahoo.com stb.) még nem támogatottak az Azure AD-rendszergazdák számára. Ellenőrizze, hogy válassza ki a felhasználó vagy natív módon az Azure AD-ben létrehozott, vagy összevonták az Azure ad-ben.
> * Jelentkezzen be az Azure AD-rendszergazda nem működik olyan fiókokkal, amelyeken 2 többtényezős hitelesítés engedélyezve van.

#### <a name="set-an-active-directory-admin-for-the-database-server"></a>Active Directory-rendszergazda beállítása az adatbázis-kiszolgálóhoz

1. A Azure Portal válassza ki az SQL Servert.

2. Az **SQL Server** menüben válassza a **Active Directory rendszergazda**elemet.

3. Az SQL Server **Active Directory felügyeleti** oldal eszköztárán válassza a **rendszergazda beállítása** elemet, és válassza ki a felhasználót vagy csoportot az Azure ad-rendszergazdaként.

    ![az Active Directory kiválasztása](./media/sql-database-connect-query-portal/select-active-directory.png)

4. A **rendszergazda hozzáadása** oldalon a keresőmezőbe írja be a keresendő felhasználót vagy csoportot, válassza ki rendszergazdaként, majd kattintson a **kiválasztás** gombra.

5. Vissza az SQL Server **Active Directory felügyeleti** oldal eszköztárán válassza a **Mentés**lehetőséget.

### <a name="connect-to-the-database"></a>Csatlakozás az adatbázishoz

6. Az **SQL Server** menüben válassza az **SQL-adatbázisok**lehetőséget, majd válassza ki az SQL-adatbázist.

7. Az **SQL Database** menüben válassza a **Lekérdezés-szerkesztő (előzetes verzió)** lehetőséget. Ha Ön Azure AD-rendszergazda, a **bejelentkezési** oldalon a **Active Directory hitelesítési** címke alatt megjelenik egy üzenet, amely azt jelzi, hogy be van jelentkezve. Ezután válassza a **Folytatás másként** *\<a felhasználó vagy a csoport azonosítója >* gombra. Ha az oldal azt jelzi, hogy nem sikerült bejelentkeznie, lehet, hogy frissítenie kell a lapot.

## <a name="query-a-sql-database"></a>SQL-adatbázis lekérdezése

A következő példa lekérdezéseit sikeresen futtatni kell a AdventureWorksLT-mintaadatbázison.

### <a name="run-a-select-query"></a>VÁLASZTÓ lekérdezés futtatása

1. Illessze be a következő lekérdezést a lekérdezés-szerkesztőbe:

   ```sql
    SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
    FROM SalesLT.ProductCategory pc
    JOIN SalesLT.Product p
    ON pc.productcategoryid = p.productcategoryid;
   ```

2. Válassza a **Futtatás** lehetőséget, majd tekintse át a kimenetet az **eredmények** ablaktáblán.

   ![lekérdezésszerkesztő: eredmények](./media/sql-database-connect-query-portal/query-editor-results.png)

3. Lehetőség van arra is, hogy mentse a lekérdezést. SQL-fájlként, vagy exportálja a visszaadott értékeket. JSON,. csv vagy. XML fájlként.

### <a name="run-an-insert-query"></a>BESZÚRÁSi lekérdezés futtatása

A következő [Insert](/sql/t-sql/statements/insert-transact-sql/) T-SQL-utasítás futtatásával adjon hozzá egy új terméket a `SalesLT.Product` táblában.

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


### <a name="run-an-update-query"></a>FRISSÍTÉSI lekérdezés futtatása

A következő [Update](/sql/t-sql/queries/update-transact-sql/) T-SQL-utasítás futtatásával módosítsa az új terméket.

1. Ehhez cserélje le az előző lekérdezést.

   ```sql
   UPDATE [SalesLT].[Product]
   SET [ListPrice] = 125
   WHERE Name = 'myNewProduct';
   ```

2. Válassza a **Futtatás** lehetőséget a `Product` tábla megadott sorának frissítéséhez. Az **üzenetek** ablaktábla a **lekérdezés sikerességét jeleníti meg: érintett sorok: 1**.

### <a name="run-a-delete-query"></a>TÖRLŐ lekérdezés futtatása

Futtassa az alábbi [delete](/sql/t-sql/statements/delete-transact-sql/) T-SQL-utasítást az új termék eltávolításához.

1. Cserélje le az előző lekérdezés erre:

   ```sql
   DELETE FROM [SalesLT].[Product]
   WHERE Name = 'myNewProduct';
   ```

2. A **Futtatás** gombra kattintva törölheti a megadott sort a `Product` táblában. Az **üzenetek** ablaktábla a **lekérdezés sikerességét jeleníti meg: érintett sorok: 1**.


## <a name="query-editor-considerations"></a>Lekérdezés-szerkesztő szempontok

Ha a Lekérdezésszerkesztő néhány dolgot lehet.

* A lekérdezés-szerkesztő a 443-es és a 1443-es portot használja a kommunikációhoz. Győződjön meg arról, hogy engedélyezte a kimenő HTTPS-forgalmat ezeken a portokon. Emellett hozzá kell adnia a kimenő IP-címét a kiszolgáló engedélyezett tűzfalszabályok számára az adatbázisok és az adattárházak eléréséhez.

* A lekérdezés-szerkesztő privát hivatkozással működik anélkül, hogy az ügyfél IP-címét hozzá kellene adni a SQL Database tűzfalhoz

* Az **F5** billentyű lenyomásával frissíti a lekérdezés-szerkesztő oldalt, és a folyamatban lévő összes lekérdezés elvész.

* A Lekérdezéstervező nem támogatja a `master`-adatbázishoz való kapcsolódást.

* A lekérdezés végrehajtása 5 perces időtúllépés van.

* A Lekérdezésszerkesztő csak geográfiai adattípusokhoz tartozó hengervetületet támogatja.

* Nem támogatott az IntelliSense az adatbázis-táblákhoz és a nézetekhez, de a szerkesztő támogatja a már beírt nevek automatikus kiegészítését.




## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni az Azure SQL-adatbázisokban támogatott Transact-SQL (T-SQL) szolgáltatásról, tekintse meg a [Transact-SQL különbségek feloldása az áttelepítés során SQL Databasere](sql-database-transact-sql-information.md)című témakört.
