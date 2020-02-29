---
title: Kapcsolat a VSTS
description: Az Azure szinapszis Analytics lekérdezése a Visual Studióval.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 08/15/2019
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 88dc534b8753311e49cafa9f84705258cdb0883d
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/29/2020
ms.locfileid: "78198623"
---
# <a name="connect-to-azure-synapse-analytics-with-visual-studio-and-ssdt"></a>Kapcsolódás az Azure szinapszis Analyticshez a Visual Studióval és a SSDT
> [!div class="op_single_selector"]
> * [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
> * [Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
> * [Visual Studio](sql-data-warehouse-query-visual-studio.md)
> * [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md) 
> * [SSMS](sql-data-warehouse-query-ssms.md)
> 
> 

A Visual Studióval mindössze néhány perc alatt lekérdezheti az SQL-készletet az Azure szinapszison belül. Ez a metódus a SQL Server Data Tools (SSDT) bővítményt használja a Visual Studio 2019-ben. 

## <a name="prerequisites"></a>Előfeltételek
Ehhez az oktatóanyaghoz a következőkre lesz szüksége:

* Egy meglévő SQL-készlet. A létrehozáshoz tekintse meg [az SQL-készlet létrehozása](sql-data-warehouse-get-started-provision.md)című témakört.
* SSDT a Visual Studióhoz. Ha rendelkezik a Visual Studióval, valószínűleg már rendelkezik SSDT a Visual Studióval. A telepítés menetéről és a beállításokról [Installing Visual Studio and SSDT](sql-data-warehouse-install-visual-studio.md) című cikkben olvashat bővebben.
* Az Azure SQL-kiszolgáló teljes neve. Az információk megkereséséhez lásd: [Kapcsolódás az SQL-készlethez](sql-data-warehouse-connect-overview.md).

## <a name="1-connect-to-your-sql-pool"></a>1. kapcsolódás az SQL-készlethez
1. Nyissa meg a Visual Studio 2019 alkalmazást.
2. SQL Server Object Explorer megnyitásához válassza a **nézet** > **SQL Server Object Explorer**lehetőséget.
   
    ![SQL Server Object Explorer](media/sql-data-warehouse-query-visual-studio/open-ssdt.png)
3. Kattintson az **Add SQL Server** (SQL Server hozzáadása) ikonra.
   
    ![SQL Server hozzáadása](media/sql-data-warehouse-query-visual-studio/add-server.png)
4. Töltse ki az összes mezőt a Connect to Server (Csatlakozás a kiszolgálóhoz) ablakban.
   
    ![Csatlakozás kiszolgálóhoz](media/sql-data-warehouse-query-visual-studio/connection-dialog.png)
   
   * **Kiszolgálónév**. Adja meg a korábban azonosított **kiszolgálónevet**.
   * **Hitelesítés**. Válassza az **SQL Server Authentication** (SQL Server-hitelesítés) vagy az **Active Directory Integrated Authentication** (Active Directory beépített hitelesítés) lehetőséget.
   * **Felhasználónév** és **Jelszó**. Amennyiben az SQL Server-hitelesítést választotta, adja meg felhasználónevét és jelszavát.
   * Kattintson a **Csatlakozás** gombra.
5. A részletes megtekintéshez bontsa ki az Azure SQL-kiszolgálót. Megtekintheti a kiszolgálóhoz társított adatbázisokat. Bontsa ki az AdventureWorksDW elemet a mintaadatbázis tábláinak megtekintéséhez.
   
    ![Az AdventureWorksDW áttekintése](media/sql-data-warehouse-query-visual-studio/explore-sample.png)

## <a name="2-run-a-sample-query"></a>2. minta lekérdezés futtatása
Most, hogy létrejött a kapcsolat az adatbázissal, ideje lefuttatni egy lekérdezést.

1. Kattintson a jobb gombbal az adatbázisára az SQL Server Object Explorer alatt.
2. Válassza a **New Query** (Új lekérdezés) lehetőséget. Megnyílik egy új lekérdezési ablak.
   
    ![Új lekérdezés](media/sql-data-warehouse-query-visual-studio/new-query2.png)
3. Másolja a következő T-SQL-lekérdezést a lekérdezési ablakba:
   
    ```sql
    SELECT COUNT(*) FROM dbo.FactInternetSales;
    ```
4. A lekérdezés futtatásához kattintson a zöld nyílra, vagy használja a következő billentyűparancsot: `CTRL`+`SHIFT`+`E`.
   
    ![A lekérdezés futtatása](media/sql-data-warehouse-query-visual-studio/run-query.png)
5. Tekintse meg a lekérdezés eredményeit. Ebben a példában a FactInternetSales táblának 60 398 sora van.
   
    ![Lekérdezés eredményei](media/sql-data-warehouse-query-visual-studio/query-results.png)

## <a name="next-steps"></a>További lépések
Most, hogy tud-e kapcsolatot létesíteni és lekérdezni, próbálja meg [az Power bi segítségével megjeleníteni az adatmegjelenítést](sql-data-warehouse-get-started-visualize-with-power-bi.md).

A környezet Azure Active Directory hitelesítéshez való konfigurálásával kapcsolatban lásd: [hitelesítés az SQL-készlettel](sql-data-warehouse-authentication.md).