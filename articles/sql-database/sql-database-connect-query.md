---
title: "Csatlakozás SQL Database adatbázishoz C# lekérdezéssel | Microsoft Docs"
description: "Program írása C# programozási nyelven lekérdezéséhez és SQL-adatbázishoz való csatlakozáshoz. Információk IP-címekről, kapcsolati sztringekről, biztonságos bejelentkezésről és ingyenes Visual Studio."
services: sql-database
keywords: "c# adatbázis-lekérdezés, c# lekérdezés, csatlakozás adatbázishoz, SQL C#"
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 676bd799-a571-4bb8-848b-fb1720007866
ms.service: sql-database
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: get-started-article
ms.date: 08/17/2016
ms.author: stevestein
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 333babec567a4700ca0882c883e4460442d844e1


---
# <a name="connect-to-a-sql-database-with-visual-studio"></a>Kapcsolódás SQL Database adatbázishoz Visual Studio használatával
> [!div class="op_single_selector"]
> * [Visual Studio](sql-database-connect-query.md)
> * [SSMS](sql-database-connect-query-ssms.md)
> * [Excel](sql-database-connect-excel.md)
> 
> 

Kapcsolódás Azure SQL Database adatbázishoz a Visual Studióban. 

## <a name="prerequisites"></a>Előfeltételek
SQL-adatbázishoz a Visual Studio használatával történő csatlakozáshoz a következők szükségesek: 

* Egy SQL-adatbázis, amelyhez csatlakozhat. Ebben a cikkben az **AdventureWorks** mintaadatbázist használjuk. Az AdventureWorks mintaadatbázis beszerzésével kapcsolatban lásd: [A bemutató-adatbázis létrehozása](sql-database-get-started.md).
* Visual Studio 2013 4. frissítés (vagy újabb). A Microsoft most *ingyenesen* biztosítja a Visual Studio Community-t.
  
  * [Visual Studio Community, letöltés](http://www.visualstudio.com/products/visual-studio-community-vs)
  * [Egyéb lehetőségek Visual Studio ingyenes letöltésére](http://www.visualstudio.com/products/free-developer-offers-vs.aspx)

## <a name="open-visual-studio-from-the-azure-portal"></a>Nyissa meg a Visual Studiót az Azure Portalon
1. Jelentkezzen be az [Azure portálra](https://portal.azure.com/).
2. Kattintson a **További szolgáltatások** > **SQL-adatbázisok** elemre.
3. Nyissa meg az **AdventureWorks** adatbázis paneljét. Ehhez keresse meg az *AdventureWorks* adatbázist, és kattintson rá.
4. Az adatbázis paneljének tetején kattintson az **Eszközök** gombra:
   
    ![Új lekérdezés. Csatlakozás SQL Database-kiszolgálóhoz: SQL Server Management Studio](./media/sql-database-connect-query/tools.png)
5. Kattintson a **Megnyitás a Visual Studióban** parancsra (ha nem rendelkezik a Visual Studióval, kattintson a letöltési hivatkozásra):
   
    ![Új lekérdezés. Csatlakozás SQL Database-kiszolgálóhoz: SQL Server Management Studio](./media/sql-database-connect-query/open-in-vs.png)
6. Megnyílik a Visual Studio, amelyben a **Connect to Server** (Csatlakozás kiszolgálóhoz) ablak már be van állítva a portálon kiválasztott kiszolgálóhoz és adatbázishoz való csatlakozáshoz.  (Kattintson az **Options** (Beállítások) elemre annak ellenőrzéséhez, hogy a megfelelő adatbázishoz való csatlakozás lett-e beállítva.) Írja be a kiszolgáló-rendszergazdai jelszót, majd kattintson a **Connect** (Csatlakozás) parancsra.

    ![Új lekérdezés. Csatlakozás SQL Database-kiszolgálóhoz: SQL Server Management Studio](./media/sql-database-connect-query/connect.png)


1. Ha nem állított be tűzfalszabályt a számítógép IP-címéhez, a *Cannot connect* (Nem lehet kapcsolódni) üzenet jelenik meg. Tűzfalszabály létrehozásával kapcsolatban lásd: [Azure SQL Database kiszolgálói szintű tűzfalszabály konfigurálása](sql-database-configure-firewall-settings.md).
2. A sikeres csatlakozás után megnyílik az **SQL Server Object Explorer** ablak, amely kapcsolódik az adatbázishoz.
   
    ![Új lekérdezés. Csatlakozás SQL Database-kiszolgálóhoz: SQL Server Management Studio](./media/sql-database-connect-query/sql-server-object-explorer.png)

## <a name="run-a-sample-query"></a>Mintalekérdezés futtatása
Az adatbázishoz való csatlakozást követően a következő lépések bemutatják, hogyan futtathat egy egyszerű lekérdezést:

1. Kattintson a jobb gombbal az adatbázisra, majd válassza az **Új lekérdezés** lehetőséget.
   
    ![Új lekérdezés. Csatlakozás SQL Database-kiszolgálóhoz: SQL Server Management Studio](./media/sql-database-connect-query/new-query.png)
2. A lekérdezésablakban másolja és illessze be a következő kódot.
   
        SELECT
        CustomerId
        ,Title
        ,FirstName
        ,LastName
        ,CompanyName
        FROM SalesLT.Customer;
3. Kattintson a **Végrehajt** gombra a lekérdezés futtatásához:
   
    ![Siker. Csatakozás SQL Database-kiszolgálóhoz: Visual Studio](./media/sql-database-connect-query/run-query.png)

## <a name="next-steps"></a>Következő lépések
* A Visual Studio az SQL Server Data Tools eszközöket használja SQL-adatbázisok megnyitásához. További információ: [SQL Server Data Tools](https://msdn.microsoft.com/library/hh272686.aspx).
* SQL-adatbázishoz kód használatával történő csatlakozással kapcsolatban lásd: [Connect to SQL Database by using .NET (C#)](sql-database-develop-dotnet-simple.md) (Csatlakozás SQL Database adatbázishoz a .NET (C#) használatával).




<!--HONumber=Nov16_HO2-->


