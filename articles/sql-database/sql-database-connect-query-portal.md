---
title: SQL-adatbázis lekérdezése az Azure Portal lekérdezésszerkesztőjével
description: Megtudhatja, hogy a Lekérdezésszerkesztővel hogyan futtathat Transact-SQL (T-SQL) lekérdezéseket egy Azure SQL-adatbázison.
keywords: csatlakozás sql adatbázishoz,sql adatbázis lekérdezése, azure portal, portál, lekérdezésszerkesztő
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
ms.openlocfilehash: c072161db7a477b7973571a18d4f686b1b9c6202
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2020
ms.locfileid: "80985677"
---
# <a name="quickstart-use-the-azure-portals-query-editor-to-query-a-sql-database"></a>Rövid útmutató: Sql-adatbázis lekérdezéséhez használja az Azure Portal lekérdezésszerkesztőjét

A lekérdezésszerkesztő az Azure Portalon egy olyan eszköz, amely SQL-lekérdezéseket futtat az Azure SQL-adatbázisán vagy az Azure SQL Data Warehouse-on. 

Ebben a rövid útmutatóban a lekérdezésszerkesztővel fogja futtatni a Transact-SQL (T-SQL) lekérdezéseket egy Azure SQL-adatbázison.


## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató befejezéséhez az AdventureWorksLT mintaadatbázisra van szükség. Ha nem rendelkezik az AdventureWorksLT SQL-adatbázis működő másolatával, a következő rövid útmutató gyorsan létrehoz egyet:

- [Rövid útmutató: Hozzon létre egyetlen Azure SQL-adatbázist az Azure Portalon, a PowerShellen vagy az Azure CLI-n keresztül](sql-database-single-database-get-started.md) 

### <a name="configure-network-settings"></a>A hálózati beállítások konfigurálása

Ha a következő hibák valamelyike jelenik meg a lekérdezésszerkesztőben: *Előfordulhat, hogy a helyi hálózati beállítások megakadályozzák, hogy a Lekérdezésszerkesztő lekérdezéseket adjon ki. Kattintson ide a hálózati beállítások konfigurálására vonatkozó utasításokért,* vagy hogy *nem lehetett kapcsolatot létesíteni a kiszolgálóval. Ez a helyi tűzfal-konfiguráció val vagy a hálózati proxy beállításaival kapcsolatos problémát jelezhet,* ezért az alábbi fontos információk segíthetnek a megoldásban:

> [!IMPORTANT]
> A lekérdezésszerkesztő a 443-as és az 1443-as portot használja a kommunikációhoz. Győződjön meg arról, hogy engedélyezte a kimenő HTTPS-forgalmat ezeken a portokon. A kimenő IP-címet is hozzá kell [adnia a kiszolgáló engedélyezett tűzfalszabályaihoz](sql-database-server-level-firewall-rule.md) az adatbázisok és adattárházak eléréséhez.


## <a name="open-the-sql-database-query-editor"></a>Az SQL Database Query Editor megnyitása

1. Jelentkezzen be az [Azure Portalon,](https://portal.azure.com/) és válassza ki a lekérdezni kívánt SQL-adatbázist.

2. Az **SQL adatbázis** menüben válassza a **Lekérdezésszerkesztő (előnézet)** lehetőséget.

    ![lekérdezésszerkesztő keresése](./media/sql-database-connect-query-portal/find-query-editor.PNG)


## <a name="establish-a-connection-to-the-database"></a>Kapcsolat létrehozása az adatbázissal

Annak ellenére, hogy be van jelentkezve a portálon, továbbra is meg kell adnia a hitelesítő adatokat az SQL-adatbázis eléréséhez. Csatlakozhat SQL-hitelesítés vagy az Azure Active Directory az adatbázishoz való csatlakozáshoz.

### <a name="connect-using-sql-authentication"></a>Csatlakozás SQL-hitelesítés használatával

1. A **Bejelentkezés** lap **SQL-kiszolgáló hitelesítése**csoportban adja meg az adatbázishoz hozzáféréssel rendelkező felhasználó **bejelentkezési** és jelszó-jelszavát. **Password** Ha nem biztos benne, használja a bejelentkezési adatokat és a jelszót az adatbázis kiszolgálójának kiszolgálója számára.

    ![bejelentkezés](./media/sql-database-connect-query-portal/login-menu.png)

2. Válassza **az OK gombot.**


### <a name="connect-using-azure-active-directory"></a>Csatlakozás az Azure Active Directory használatával

Az Azure Active Directory (Azure AD) rendszergazdájának konfigurálása lehetővé teszi, hogy egyetlen identitáshasználatával jelentkezzen be az Azure Portalra és az SQL-adatbázisba. Ha az Azure AD használatával szeretne csatlakozni az adatbázishoz, kövesse az alábbi lépéseket az Azure AD-rendszergazda sql-kiszolgálóhoz való konfigurálásához.

> [!NOTE]
> * Az e-mail fiókok (például outlook.com, gmail.com, yahoo.com és így tovább) még nem támogatottak Az Azure AD-rendszergazdák számára. Győződjön meg arról, hogy válasszon egy felhasználót létrehozott vagy natívan az Azure AD-ben, vagy az Azure AD-be összevont.
> * Az Azure AD-rendszergazdai bejelentkezés nem működik olyan fiókok esetén, amelyeken engedélyezve van a kétfaktoros hitelesítés.

#### <a name="set-an-active-directory-admin-for-the-database-server"></a>Active Directory-rendszergazda beállítása az adatbázis-kiszolgálóhoz

1. Az Azure Portalon válassza ki az SQL-kiszolgálót.

2. Az **SQL server** menüben válassza az **Active Directory rendszergazdája**lehetőséget.

3. Az SQL-kiszolgáló **Active Directory felügyeleti** lapjának eszköztárán válassza a Rendszergazda **beállítása** lehetőséget, és válassza ki a felhasználót vagy csoportot Azure AD-rendszergazdaként.

    ![az Active Directory kiválasztása](./media/sql-database-connect-query-portal/select-active-directory.png)

4. A **Rendszergazda hozzáadása** lap keresőmezőjében adja meg a keresett felhasználót vagy csoportot, jelölje ki rendszergazdaként, és válassza a **Kijelölés** gombot.

5. Az SQL server **Active Directory felügyeleti** lapjának eszköztárán kattintson a Mentés **gombra.**

### <a name="connect-to-the-database"></a>Csatlakozás az adatbázishoz

6. Az **SQL server** menüben válassza az **SQL-adatbázisok**lehetőséget, majd jelölje ki az SQL-adatbázist.

7. Az **SQL adatbázis** menüben válassza a **Lekérdezésszerkesztő (előnézet)** lehetőséget. A **Bejelentkezés lap** Active **Directory hitelesítési** címke alatt egy üzenet jelenik meg, amely szerint be van jelentkezve, ha Ön az Azure AD-rendszergazda. Ezután válassza a **Folytatás felhasználóként** * \<vagy csoportazonosítóként>* gombot. Ha az oldal azt jelzi, hogy még nem sikerült bejelentkezett, előfordulhat, hogy frissítenie kell az oldalt.

## <a name="query-a-sql-database"></a>SQL-adatbázis lekérdezése

A következő példa lekérdezések sikeresen kell futnia az AdventureWorksLT mintaadatbázis.

### <a name="run-a-select-query"></a>SELECT lekérdezés futtatása

1. Illessze be a következő lekérdezést a lekérdezésszerkesztőbe:

   ```sql
    SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
    FROM SalesLT.ProductCategory pc
    JOIN SalesLT.Product p
    ON pc.productcategoryid = p.productcategoryid;
   ```

2. Válassza **a Futtatás lehetőséget,** majd tekintse át a kimenetet az **Eredmények** ablaktáblán.

   ![lekérdezésszerkesztő: eredmények](./media/sql-database-connect-query-portal/query-editor-results.png)

3. Szükség esetén a lekérdezést .sql fájlként is mentheti, vagy a visszaadott adatokat .json, .csv vagy .xml fájlként exportálhatja.

### <a name="run-an-insert-query"></a>INSERT lekérdezés futtatása

Új termék hozzáadásához futtassa a `SalesLT.Product` következő [INSERT](/sql/t-sql/statements/insert-transact-sql/) T-SQL utasítást.

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


2. Új **Run** sor beszúrásához válassza `Product` a Futtatás lehetőséget. Az **Üzenetek** ablaktábla megjeleníti **a Lekérdezés sikerült: Érintett sorok: 1**.


### <a name="run-an-update-query"></a>UPDATE lekérdezés futtatása

Futtassa a következő [UPDATE](/sql/t-sql/queries/update-transact-sql/) T-SQL utasítást az új termék módosításához.

1. Cserélje le az előző lekérdezést erre.

   ```sql
   UPDATE [SalesLT].[Product]
   SET [ListPrice] = 125
   WHERE Name = 'myNewProduct';
   ```

2. A **Run** táblázat megadott sorának `Product` frissítéséhez válassza a Futtatás lehetőséget. Az **Üzenetek** ablaktábla megjeleníti **a Lekérdezés sikerült: Érintett sorok: 1**.

### <a name="run-a-delete-query"></a>DELETE lekérdezés futtatása

Futtassa a következő [DELETE](/sql/t-sql/statements/delete-transact-sql/) T-SQL utasítást az új termék eltávolításához.

1. Az előző lekérdezés lecserélése erre:

   ```sql
   DELETE FROM [SalesLT].[Product]
   WHERE Name = 'myNewProduct';
   ```

2. A **Táblázat** megadott sorának `Product` törléséhez válassza a Futtatás lehetőséget. Az **Üzenetek** ablaktábla megjeleníti **a Lekérdezés sikerült: Érintett sorok: 1**.


## <a name="query-editor-considerations"></a>A lekérdezésszerkesztővel kapcsolatos szempontok

A lekérdezésszerkesztővel végzett munka során néhány tudnivalót kell tudni.

* A lekérdezésszerkesztő a 443-as és az 1443-as portot használja a kommunikációhoz. Győződjön meg arról, hogy engedélyezte a kimenő HTTPS-forgalmat ezeken a portokon. A kimenő IP-címet is hozzá kell adnia a kiszolgáló engedélyezett tűzfalszabályaihoz az adatbázisok és adattárházak eléréséhez.

* Ha privát kapcsolattal rendelkezik, a Lekérdezésszerkesztő anélkül működik, hogy hozzá kellene adnia az ügyfél ip-címét az SQL adatbázis tűzfalához

* Az **F5** billentyű lenyomásával frissül a lekérdezésszerkesztő lap, és a megdolgozott lekérdezések elvesznek.

* A Lekérdezésszerkesztő nem támogatja az `master` adatbázishoz való csatlakozást.

* A lekérdezés végrehajtásának 5 perces időtúlszáma van.

* A lekérdezésszerkesztő csak a földrajzi adattípusok hengeres vetületét támogatja.

* Az IntelliSense nem támogatja az adatbázistáblákat és -nézeteket, de a szerkesztő támogatja a már beírt nevek automatikus kiegészítését.




## <a name="next-steps"></a>További lépések

Ha többet szeretne megtudni az Azure SQL-adatbázisokban támogatott Transact-SQL (T-SQL) szolgáltatásról, olvassa [el a Transact-SQL-különbségek feloldása az SQL Database-re való áttelepítés során](sql-database-transact-sql-information.md)című témakört.
