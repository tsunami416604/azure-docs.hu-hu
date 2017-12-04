---
title: "Azure Portal: Az Azure SQL Database lekérdezése a Lekérdezésszerkesztő használatával | Microsoft Docs"
description: "Megtudhatja, hogyan csatlakozhat az SQL Database-hez az Azure Portalon az SQL-lekérdezésszerkesztő segítségével. Ezután futtasson Transact-SQL (T-SQL) utasításokat az adatok lekérdezéséhez és szerkesztéséhez."
metacanonical: 
keywords: "csatlakozás SQL Database-adatbázishoz, Azure Portal, portál, lekérdezésszerkesztő"
services: sql-database
documentationcenter: 
author: ayoolubeko
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: mvc,DBs & servers
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 08/02/2017
ms.author: ayolubek
ms.openlocfilehash: ae7ce2c138a9948654d96e018c167d12356ae93d
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/29/2017
---
# <a name="azure-portal-use-the-sql-query-editor-to-connect-and-query-data"></a>Azure Portal: Csatlakozás és az adatok lekérdezése az SQL-lekérdezésszerkesztő használatával

Az SQL-lekérdezésszerkesztő egy böngészőből elérhető lekérdezési eszköz, amellyel hatékonyan és egyszerűen hajthat végre SQL-lekérdezéseket az Azure SQL Database-en vagy az Azure SQL Data Warehouse szolgáltatáson az Azure Portal elhagyása nélkül. Ez a rövid útmutató ismerteti, hogyan használható a Lekérdezésszerkesztő az SQL-adatbázisokhoz való csatlakozáshoz, majd hogyan lehet Transact-SQL-utasításokkal adatokat lekérdezni, beszúrni, frissíteni és törölni az adatbázisban. 

## <a name="prerequisites"></a>Előfeltételek

Ez a rövid útmutató az alábbi rövid útmutatókban létrehozott erőforrásokat használja kiindulási pontként:

[!INCLUDE [prerequisites-create-db](../../includes/sql-database-connect-query-prerequisites-create-db-includes.md)]

## <a name="log-in-to-the-azure-portal"></a>Jelentkezzen be az Azure portálra.

Jelentkezzen be az [Azure portálra](https://portal.azure.com/).


## <a name="connect-using-sql-authentication"></a>Csatlakozás SQL-hitelesítés használatával
> [!NOTE]
> Győződjön meg arról, hogy az SQL Server tűzfalbeállításaiban az „Azure-szolgáltatásokhoz való hozzáférés engedélyezése” beállítás „BE” értékre van állítva. Ez a beállítás hozzáférést biztosít az SQL-lekérdezésszerkesztőnek az adatbázisokhoz és az adattárházakhoz.

1. Kattintson az **SQL-adatbázisok** elemre a bal oldali menüben, majd kattintson a lekérdezni kívánt adatbázisra.

2. Az adatbázishoz tartozó SQL-adatbázis lapon kattintson az eszköztár **Eszközök** elemére. Megnyílik az Eszközök lap.

    ![eszközök menü](./media/sql-database-connect-query-portal/tools-menu.png)

3. Kattintson a **Lekérdezésszerkesztő (előzetes verzió)** lehetőségre, kattintson az **Előzetes verziójú szolgáltatás feltételei** jelölőnégyzetre, majd az **OK** gombra. Megnyílik a Lekérdezésszerkesztő lapja.

4. Kattintson a **Bejelentkezés** elemre, majd amikor a rendszer kéri, válassza ki az **SQL Server-hitelesítés** lehetőséget, és adja meg a kiszolgáló rendszergazdai bejelentkezési nevét és jelszavát, amelyeket az adatbázis létrehozásakor adott meg.

    ![bejelentkezés](./media/sql-database-connect-query-portal/login-menu.png) 

5. A **bejelentkezéshez kattintson az OK** gombra.


## <a name="connect-using-azure-ad"></a>Csatlakozás az Azure AD használatával

Az Active Directory-rendszergazda konfigurálása lehetővé teszi, hogy egyetlen identitással jelentkezzen be az Azure Portalra és az SQL-adatbázisba. Kövesse az alábbi lépéseket egy Active Directory-rendszergazda konfigurálásához a létrehozott SQL Server számára.

> [!NOTE]
> Az e-mail fiókok (például az outlook.com, hotmail.com, live.com, gmail.com, yahoo.com) Active Directory-rendszergazdaként való használata még nem támogatott. Mindenképpen olyan felhasználót használjon, amelyet az Azure Active Directoryban hoztak létre natív módon, vagy összevonták az Azure Active Directoryval.

1. Válassza ki az **SQL Server kiszolgálók** elemet a bal oldali menüben, majd válassza ki az SQL Server kiszolgálóját a kiszolgálólistából.

2. Válassza ki az **Active Directory-rendszergazda** beállítást az SQL Server beállítások menüjében.

3. Az Active Directory-rendszergazda panelen kattintson a **Rendszergazda beállítása** parancsra, majd válassza ki a felhasználót vagy csoportot, amely az Active Directory-rendszergazda lesz majd.

    ![az Active Directory kiválasztása](./media/sql-database-connect-query-portal/select-active-directory.png) 

4. Az Active Directory-rendszergazda panel tetején kattintson a **Mentés** parancsra az Active Directory-rendszergazda beállításához.

Lépjen a lekérdezni kívánt SQL-adatbázisra, kattintson az eszköztár **Eszközök parancsára**, majd válassza ki a **Lekérdezésszerkesztő (előzetes verzió)** lehetőséget. Megnyílik a Lekérdezésszerkesztő lapja, és automatikusan csatlakoztatja az adatbázishoz.


## <a name="run-query-using-query-editor"></a>Lekérdezés futtatása a Lekérdezésszerkesztő használatával

A hitelesítés után írja be a következő lekérdezést a Lekérdezésszerkesztő panelén az első 20 termék kategóriánkénti lekérdezéséhez.

```sql
 SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
 FROM SalesLT.ProductCategory pc
 JOIN SalesLT.Product p
 ON pc.productcategoryid = p.productcategoryid;
```

Kattintson a **Futtatás** gombra, majd tekintse át a lekérdezési eredményeket az **Eredmények** ablaktáblán.

![lekérdezésszerkesztő: eredmények](./media/sql-database-connect-query-portal/query-editor-results.png)

## <a name="insert-data-using-query-editor"></a>Adatok beszúrása a Lekérdezésszerkesztő használatával

A következő kód használatával beszúrhat egy új terméket a SalesLT.Product táblába az [INSERT](https://msdn.microsoft.com/library/ms174335.aspx) Transact-SQL-utasítással.

1. A lekérdezési ablakban cserélje le az előző lekérdezést a következő lekérdezésre:

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

2. Az eszköztáron kattintson az **Futtatás** elemre az új sor Product táblába történő beszúrásához.

## <a name="update-data-using-query-editor"></a>Adatok frissítése a Lekérdezésszerkesztő használatával

A következő kód használatával frissítheti az előzőleg hozzáadott új terméket az [UPDATE](https://msdn.microsoft.com/library/ms177523.aspx) Transact-SQL-utasítással.

1. A lekérdezési ablakban cserélje le az előző lekérdezést a következő lekérdezésre:

   ```sql
   UPDATE [SalesLT].[Product]
   SET [ListPrice] = 125
   WHERE Name = 'myNewProduct';
   ```

2. Az eszköztáron kattintson az **Futtatás** elemre a Product tábla egy megadott sorának frissítéséhez.

## <a name="delete-data-using-query-editor"></a>Adatok törlése a Lekérdezésszerkesztő használatával

A következő kód használatával törölheti az előzőleg hozzáadott új terméket a [DELETE](https://msdn.microsoft.com/library/ms189835.aspx) Transact-SQL-utasítással.

1. A lekérdezési ablakban cserélje le az előző lekérdezést a következő lekérdezésre:

   ```sql
   DELETE FROM [SalesLT].[Product]
   WHERE Name = 'myNewProduct';
   ```

2. Az eszköztáron kattintson az **Futtatás** elemre a Product tábla egy megadott sorának törléséhez.


## <a name="query-editor-considerations"></a>Lekérdezésszerkesztési szempontok

Néhány szempontot fontos szem előtt tartani a Lekérdezésszerkesztő előzetes verziójának használatakor:

1. Győződjön meg arról, hogy az Azure SQL Server tűzfalbeállításaiban az „Azure-szolgáltatásokhoz való hozzáférés engedélyezése” beállítást „BE” értékre állította. Ez a beállítás hozzáférést biztosít az SQL-lekérdezésszerkesztőnek az SQL-adatbázisokhoz és -adattárházakhoz.

2. Az Azure Active Directory rendszergazdai bejelentkezése nem működik olyan fiókokkal, amelyeken engedélyezve van a kéttényezős hitelesítés. 

3. Az e-mail fiókok (például az outlook.com, hotmail.com, live.com, gmail.com, yahoo.com) Active Directory-rendszergazdaként való használata még nem támogatott. Győződjön meg arról, hogy olyan felhasználót használ, amelyet az Azure Active Directoryban hoztak létre natív módon, vagy összevonták az Azure Active Directoryval

4. A Lekérdezésszerkesztő egyelőre nem támogatja a térbeli adattípusok lekérdezését. A térbeli oszlopok lekérdezése „System.IO.FileNotFoundException” hibát eredményez.

5. Az adatbázistáblákhoz és -nézetekhez tartozó IntelliSense nem támogatott. A szerkesztő azonban támogatja a korábban már beírt nevek automatikus kiegészítését. 

6. Az F5 billentyű lenyomásával frissíti a Lekérdezésszerkesztő lapját, és elveszti a jelenleg szerkesztett lekérdezést. A lekérdezések végrehajtásához használja az eszköztár Futtatás gombját.


## <a name="next-steps"></a>Következő lépések

- Az Azure SQL-adatbázisokban támogatott Transact-SQL-lel kapcsolatos információkért lásd a [Transact-SQL-lel kapcsolatos különbségek az SQL-adatbázisokban](sql-database-transact-sql-information.md) című cikket.
