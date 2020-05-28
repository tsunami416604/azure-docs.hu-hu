---
title: SQL Database lekérdezése a Azure Portal a lekérdezés-szerkesztő használatával
description: Megtudhatja, hogyan futtathat Transact-SQL-(T-SQL-) lekérdezéseket a lekérdezéstervező használatával a Azure SQL Database adatbázisán.
keywords: Kapcsolódás az SQL Database-hez, SQL Database lekérdezése, Azure Portal, portál, lekérdezés-szerkesztő
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: quickstart
author: Ninarn
ms.author: ninarn
ms.reviewer: carlrab
ms.date: 03/12/2020
ms.openlocfilehash: 2bd53f62f6e4d05931b7e8b6c3c2f4265b6c396b
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2020
ms.locfileid: "84054424"
---
# <a name="quickstart-use-the-azure-portals-query-editor-to-query-a-database"></a>Gyors útmutató: adatbázis lekérdezése a Azure Portal lekérdezési szerkesztőjével

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

A lekérdezés-szerkesztő egy eszköz a Azure Portalban, amellyel SQL-lekérdezéseket futtathat az adatbázisán Azure SQL Database vagy adattárházban Azure SQL Data Warehouseban. 

Ebben a rövid útmutatóban a lekérdezéstervező segítségével futtatja a Transact-SQL (T-SQL) lekérdezéseket egy adatbázison.

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató elvégzéséhez a AdventureWorksLT mintaadatbázis szükséges. Ha nem rendelkezik a AdventureWorksLT mintaadatbázis SQL Database működő példányával, a következő rövid útmutató gyorsan létrehoz egyet:

- [Gyors útmutató: adatbázis létrehozása Azure SQL Database a Azure Portal, a PowerShell vagy az Azure CLI használatával](single-database-create-quickstart.md) 

### <a name="configure-network-settings"></a>A hálózati beállítások konfigurálása

Ha a lekérdezési szerkesztőben a következő hibák valamelyike jelenik meg: *a helyi hálózati beállítások megakadályozhatják, hogy a lekérdezéstervező lekérdezéseket bocsásson ki. A hálózati beállítások konfigurálására*, illetve a *kiszolgálóhoz való kapcsolódásra vonatkozó utasításokért kattintson ide. Ez a helyi tűzfal-konfigurációval vagy a hálózati proxy-beállításokkal kapcsolatos problémára utalhat, és*a következő fontos információk segítenek a megoldásban:

> [!IMPORTANT]
> A lekérdezés-szerkesztő a 443-es és a 1443-es portot használja a kommunikációhoz. Győződjön meg arról, hogy engedélyezte a kimenő HTTPS-forgalmat ezeken a portokon. Emellett [hozzá kell adnia a kimenő IP-címét a kiszolgáló engedélyezett tűzfalszabályok](firewall-create-server-level-portal-quickstart.md) számára az adatbázisok és az adattárházak eléréséhez.


## <a name="open-the-sql-database-query-editor"></a>A SQL Database lekérdezés-szerkesztő megnyitása

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com/) , és válassza ki a lekérdezni kívánt adatbázist.

2. Az **SQL Database** menüben válassza a **Lekérdezés-szerkesztő (előzetes verzió)** lehetőséget.

    ![lekérdezésszerkesztő keresése](./media/connect-query-portal/find-query-editor.PNG)


## <a name="establish-a-connection-to-the-database"></a>Kapcsolat létesítése az adatbázissal

Annak ellenére, hogy bejelentkezett a portálra, továbbra is meg kell adnia a hitelesítő adatokat az adatbázis eléréséhez. Az adatbázishoz való kapcsolódáshoz SQL-hitelesítés vagy Azure Active Directory használatával csatlakozhat.

### <a name="connect-using-sql-authentication"></a>Csatlakozás SQL-hitelesítés használatával

1. A **Bejelentkezés** lap **SQL Server-hitelesítés**területén adja meg az adatbázishoz hozzáféréssel rendelkező felhasználó **felhasználónevét** és **jelszavát** . Ha nem biztos benne, használja az adatbázis-kiszolgálójának kiszolgáló-rendszergazdájának felhasználónevét és jelszavát.

    ![bejelentkezés](./media/connect-query-portal/login-menu.png)

2. Kattintson az **OK** gombra.


### <a name="connect-using-azure-active-directory"></a>Kapcsolat Azure Active Directory használatával

Egy Azure Active Directory (Azure AD) rendszergazdájának konfigurálása lehetővé teszi, hogy egyetlen identitás használatával jelentkezzen be a Azure Portalba és az adatbázisába. Az adatbázishoz az Azure AD-vel való kapcsolódáshoz kövesse az alábbi lépéseket az Azure AD-rendszergazda konfigurálásához a SQL Server-példányhoz.

> [!NOTE]
> * Az e-mail-fiókok (például outlook.com, gmail.com, yahoo.com stb.) még nem támogatottak az Azure AD-rendszergazdák számára. Győződjön meg arról, hogy az Azure AD-ben natív módon létrehozott vagy az Azure AD-ba összevont felhasználót választ.
> * Az Azure AD rendszergazdai bejelentkezés nem működik olyan fiókokkal, amelyeken engedélyezve van a 2 faktoros hitelesítés.

#### <a name="set-an-active-directory-admin-for-the-server"></a>Active Directory-rendszergazda beállítása a kiszolgálóhoz

1. A Azure Portal válassza ki a SQL Server példányt.

2. Az **SQL Server** menüben válassza a **Active Directory rendszergazda**elemet.

3. A SQL Server **Active Directory felügyeleti** oldal eszköztárán válassza a **rendszergazda beállítása** elemet, és válassza ki a felhasználót vagy csoportot az Azure ad-rendszergazdaként.

    ![az Active Directory kiválasztása](./media/connect-query-portal/select-active-directory.png)

4. A **rendszergazda hozzáadása** oldalon a keresőmezőbe írja be a keresendő felhasználót vagy csoportot, válassza ki rendszergazdaként, majd kattintson a **kiválasztás** gombra.

5. Vissza a SQL Server **Active Directory felügyeleti** oldal eszköztárán válassza a **Mentés**lehetőséget.

### <a name="connect-to-the-database"></a>Csatlakozás az adatbázishoz

6. Az **SQL Server** menüben válassza az **SQL-adatbázisok**lehetőséget, majd válassza ki az adatbázist.

7. Az **SQL Database** menüben válassza a **Lekérdezés-szerkesztő (előzetes verzió)** lehetőséget. Ha Ön Azure AD-rendszergazda, a **bejelentkezési** oldalon a **Active Directory hitelesítési** címke alatt megjelenik egy üzenet, amely azt jelzi, hogy be van jelentkezve. Ezután válassza a **Folytatás** *\<your user or group ID>* gombot. Ha az oldal azt jelzi, hogy nem sikerült bejelentkeznie, lehet, hogy frissítenie kell a lapot.

## <a name="query-a-database-in-sql-database"></a>Adatbázis lekérdezése SQL Database

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

   ![lekérdezésszerkesztő: eredmények](./media/connect-query-portal/query-editor-results.png)

3. Lehetőség van arra is, hogy mentse a lekérdezést. SQL-fájlként, vagy exportálja a visszaadott értékeket. JSON,. csv vagy. XML fájlként.

### <a name="run-an-insert-query"></a>BESZÚRÁSi lekérdezés futtatása

Az alábbi [Insert](/sql/t-sql/statements/insert-transact-sql/) T-SQL-utasítás futtatásával vegyen fel egy új terméket a `SalesLT.Product` táblába.

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


2. Válassza a **Futtatás** elemet egy új sor táblázatba való beszúrásához `Product` . Az **üzenetek** ablaktábla a **lekérdezés sikerességét jeleníti meg: érintett sorok: 1**.


### <a name="run-an-update-query"></a>FRISSÍTÉSI lekérdezés futtatása

A következő [Update](/sql/t-sql/queries/update-transact-sql/) T-SQL-utasítás futtatásával módosítsa az új terméket.

1. Cserélje le az előző lekérdezést ezzel az eggyel.

   ```sql
   UPDATE [SalesLT].[Product]
   SET [ListPrice] = 125
   WHERE Name = 'myNewProduct';
   ```

2. Válassza a **Futtatás** lehetőséget a tábla megadott sorának frissítéséhez `Product` . Az **üzenetek** ablaktábla a **lekérdezés sikerességét jeleníti meg: érintett sorok: 1**.

### <a name="run-a-delete-query"></a>TÖRLŐ lekérdezés futtatása

Futtassa az alábbi [delete](/sql/t-sql/statements/delete-transact-sql/) T-SQL-utasítást az új termék eltávolításához.

1. Cserélje le az előző lekérdezést a következőre:

   ```sql
   DELETE FROM [SalesLT].[Product]
   WHERE Name = 'myNewProduct';
   ```

2. Válassza a **Futtatás** lehetőséget a tábla megadott sorának törléséhez `Product` . Az **üzenetek** ablaktábla a **lekérdezés sikerességét jeleníti meg: érintett sorok: 1**.


## <a name="query-editor-considerations"></a>A lekérdezési szerkesztő szempontjai

A lekérdezés-szerkesztő használatakor néhány tudnivalót is megtudhat.

* A lekérdezés-szerkesztő a 443-es és a 1443-es portot használja a kommunikációhoz. Győződjön meg arról, hogy engedélyezte a kimenő HTTPS-forgalmat ezeken a portokon. Emellett hozzá kell adnia a kimenő IP-címét a kiszolgáló engedélyezett tűzfalszabályok számára az adatbázisok és az adattárházak eléréséhez.

* Ha rendelkezik magánhálózati kapcsolattal, a lekérdezés-szerkesztő úgy működik, hogy az ügyfél IP-címét a SQL Database tűzfalon is fel kellene vennie.

* Az **F5** billentyű lenyomásával frissíti a lekérdezés-szerkesztő oldalt, és a folyamatban lévő összes lekérdezés elvész.

* A lekérdezés-szerkesztő nem támogatja az `master` adatbázishoz való kapcsolódást.

* A lekérdezés végrehajtásának 5 perces időtúllépése van.

* A lekérdezéstervező csak a földrajzi adattípusok hengeres leképezését támogatja.

* Nem támogatott az IntelliSense az adatbázis-táblákhoz és a nézetekhez, de a szerkesztő támogatja a már beírt nevek automatikus kiegészítését.




## <a name="next-steps"></a>További lépések

Ha többet szeretne megtudni a Azure SQL Database által támogatott Transact-SQL (T-SQL) szolgáltatásról, tekintse meg a [Transact-SQL-különbségek feloldása az áttelepítés során SQL Databasere](transact-sql-tsql-differences-sql-server.md)című témakört.
