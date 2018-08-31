---
title: Az Azure SQL Data Warehouse – ssms-t kapcsolódás |} A Microsoft Docs
description: Az SQL Server Management Studio (SSMS) használatával csatlakozik, és az Azure SQL Data Warehouse lekérdezéséhez.
services: sql-data-warehouse
author: kavithaj
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: consume
ms.date: 04/17/2018
ms.author: kavithaj
ms.reviewer: igorstan
ms.openlocfilehash: 6079c3064699da38fad20468517eb97d6ab107f8
ms.sourcegitcommit: 1fb353cfca800e741678b200f23af6f31bd03e87
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/30/2018
ms.locfileid: "43307200"
---
# <a name="connect-to-sql-data-warehouse-with-sql-server-management-studio-ssms"></a>Csatlakozás az SQL Data Warehouse az SQL Server Management Studio (SSMS)
> [!div class="op_single_selector"]
> * [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
> * [Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
> * [Visual Studio](sql-data-warehouse-query-visual-studio.md)
> * [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md) 
> * [SSMS](sql-data-warehouse-query-ssms.md)
> 
> 

Az SQL Server Management Studio (SSMS) használatával csatlakozik, és az Azure SQL Data Warehouse lekérdezéséhez. 

## <a name="prerequisites"></a>Előfeltételek
Ehhez az oktatóanyaghoz a következőkre lesz szüksége:

* Egy létező SQL Data Warehouse. A létrehozás menetét az [SQL Data Warehouse létrehozását][Create a SQL Data Warehouse] ismertető cikkben találja.
* Az SQL Server Management Studio (SSMS) telepítve van. [Az SSMS telepítése] [ Install SSMS] ingyenesen, ha már nincs.
* Az Azure SQL-kiszolgáló teljes neve. Ennek megkeresésével kapcsolatban olvassa el [az SQL Data Warehouse-hoz történő csatlakozást][Connect to SQL Data Warehouse] ismertető cikket.

## <a name="1-connect-to-your-sql-data-warehouse"></a>1. Csatlakozás az SQL Data Warehouse-hoz
1. Nyissa meg az SSMS-t.
2. Nyissa meg az Object Explorerben. Ehhez válassza ki a **fájl** > **Object Explorer csatlakoztatása**.
   
    ![SQL Server Object Explorer][1]
3. Töltse ki az összes mezőt a Connect to Server (Csatlakozás a kiszolgálóhoz) ablakban.
   
    ![Csatlakozás kiszolgálóhoz][2]
   
   * **Kiszolgálónév**. Adja meg a korábban azonosított **kiszolgálónevet**.
   * **Hitelesítés**. Válassza az **SQL Server Authentication** (SQL Server-hitelesítés) vagy az **Active Directory Integrated Authentication** (Active Directory beépített hitelesítés) lehetőséget.
   * **Felhasználónév** és **Jelszó**. Amennyiben az SQL Server-hitelesítést választotta, adja meg felhasználónevét és jelszavát.
   * Kattintson a **Csatlakozás** gombra.
4. A részletes megtekintéshez bontsa ki az Azure SQL-kiszolgálót. Megtekintheti a kiszolgálóhoz társított adatbázisokat. Bontsa ki az AdventureWorksDW elemet a mintaadatbázis tábláinak megtekintéséhez.
   
    ![Az AdventureWorksDW áttekintése][3]

## <a name="2-run-a-sample-query"></a>2. Mintalekérdezés futtatása
Most, hogy létrejött a kapcsolat az adatbázissal, ideje lefuttatni egy lekérdezést.

1. Kattintson a jobb gombbal az adatbázisára az SQL Server Object Explorer alatt.
2. Válassza a **New Query** (Új lekérdezés) lehetőséget. Megnyílik egy új lekérdezési ablak.
   
    ![Új lekérdezés][4]
3. Másolja be ezt a TSQL-lekérdezést a lekérdezési ablakba:
   
    ```sql
    SELECT COUNT(*) FROM dbo.FactInternetSales;
    ```
4. Futtassa a lekérdezést. Ehhez kattintson `Execute` vagy használja a következő billentyűparancsot: `F5`.
   
    ![A lekérdezés futtatása][5]
5. Tekintse meg a lekérdezés eredményeit. Ebben a példában a FactInternetSales táblának 60 398 sora van.
   
    ![Lekérdezés eredményei][6]

## <a name="next-steps"></a>További lépések
Most, hogy képes csatlakozni és elvégezni a lekérdezéseket, próbálja [megjeleníteni az adatokat a PowerBI használatával][visualizing the data with PowerBI].

A környezet Azure Active Directory-hitelesítésre történő konfigurálásával kapcsolatban tekintse meg az [SQL Data Warehouse-zal történő hitelesítést][Authenticate to SQL Data Warehouse] ismertető cikket.

<!--Arcticles-->
[Connect to SQL Data Warehouse]: sql-data-warehouse-connect-overview.md
[Create a SQL Data Warehouse]: sql-data-warehouse-get-started-provision.md
[Authenticate to SQL Data Warehouse]: sql-data-warehouse-authentication.md
[visualizing the data with PowerBI]: sql-data-warehouse-get-started-visualize-with-power-bi.md 

<!--Other-->
[Azure portal]: https://portal.azure.com
[Install SSMS]: https://msdn.microsoft.com/library/hh213248.aspx


<!--Image references-->

[1]: media/sql-data-warehouse-query-ssms/connect-object-explorer.png
[2]: media/sql-data-warehouse-query-ssms/connect-object-explorer1.png
[3]: media/sql-data-warehouse-query-ssms/explore-tables.png
[4]: media/sql-data-warehouse-query-ssms/new-query.png
[5]: media/sql-data-warehouse-query-ssms/execute-query.png
[6]: media/sql-data-warehouse-query-ssms/results.png
