---
title: Kapcsolódás és lekérdezés a szinapszis SQL-hez a Visual Studióval és a SSDT
description: Az SQL-készlet lekérdezése az Azure szinapszis Analytics használatával a Visual Studióval.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: 3f98a9d6dd79ba1da1a3be1e9e187ed4e8eca8ee
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/05/2020
ms.locfileid: "85962986"
---
# <a name="connect-to-synapse-sql-with-visual-studio-and-ssdt"></a>Kapcsolódás a szinapszis SQLhoz a Visual Studióval és a SSDT
> [!div class="op_single_selector"]
> * [Azure Data Studio](get-started-azure-data-studio.md)
> * [Power BI](get-started-power-bi-professional.md)
> * [Visual Studio](get-started-visual-studio.md)
> * [Sqlcmd](get-started-connect-sqlcmd.md) 
> * [SSMS](get-started-ssms.md)
> 
> 

Az SQL-készlet lekérdezése az Azure szinapszis Analytics használatával a Visual Studióval. Ez a metódus a SQL Server Data Tools (SSDT) bővítményt használja a Visual Studio 2019-ben. 

> [!NOTE]
> A SSDT nem támogatja az SQL on-demand (előzetes verzió) használatát.

## <a name="prerequisites"></a>Előfeltételek
Az oktatóanyag használatához a következő összetevőket kell használnia:

* Egy meglévő SQL-készlet. Ha még nem rendelkezik ilyennel, tekintse meg az [SQL-készlet létrehozása](../sql-data-warehouse/create-data-warehouse-portal.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) az előfeltételek elvégzéséhez című témakört.
* SSDT a Visual Studióhoz. Ha rendelkezik a Visual Studióval, valószínűleg már rendelkezik ezzel az összetevővel. A telepítés menetéről és a beállításokról [A Visual Studio és az SSDT telepítése](../sql-data-warehouse/sql-data-warehouse-install-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) című cikkben olvashat bővebben.
* Az Azure SQL-kiszolgáló teljes neve. A kiszolgálónév megkereséséhez lásd: [Kapcsolódás az SQL-készlethez](connect-overview.md).

## <a name="1-connect-to-sql-pool"></a>1. kapcsolódás az SQL-készlethez
1. Nyissa meg a Visual Studio 2019 alkalmazást.
2. Nyissa meg a SQL Server Object Explorer a **nézet**SQL Server Object Explorer lehetőség kiválasztásával  >  **SQL Server Object Explorer**.
   
    ![SQL Server Object Explorer](./media/get-started-visual-studio/open-ssdt.png)
3. Kattintson az **Add SQL Server** (SQL Server hozzáadása) ikonra.
   
    ![SQL Server hozzáadása](./media/get-started-visual-studio/add-server.png)
4. Töltse ki az összes mezőt a Connect to Server (Csatlakozás a kiszolgálóhoz) ablakban.
   
    ![Csatlakozás kiszolgálóhoz](./media/get-started-visual-studio/connection-dialog.png)
   
   * **Kiszolgáló neve**: adja meg a korábban azonosított **kiszolgálónevet** .
   * **Hitelesítés**: válassza **SQL Server hitelesítés** vagy **Active Directory integrált hitelesítés**lehetőséget:
   * **Felhasználónév** és **jelszó**: adja meg felhasználónevét és jelszavát, ha SQL Server hitelesítés lett kiválasztva.
   * Kattintson a **Csatlakozás** gombra.
5. A részletes megtekintéshez bontsa ki az Azure SQL-kiszolgálót. Megtekintheti a kiszolgálóhoz társított adatbázisokat. Bontsa ki az AdventureWorksDW elemet a mintaadatbázis tábláinak megtekintéséhez.
   
    ![Az AdventureWorksDW áttekintése](./media/get-started-visual-studio/explore-sample.png)

## <a name="2-run-a-sample-query"></a>2. minta lekérdezés futtatása
Most, hogy létrejött egy kapcsolódás az adatbázishoz, meg kell írnia egy lekérdezést.

1. Kattintson a jobb gombbal az adatbázisára az SQL Server Object Explorer alatt.
2. Válassza a **New Query** (Új lekérdezés) lehetőséget. Megnyílik egy új lekérdezési ablak.
   
    ![Új lekérdezés](./media/get-started-visual-studio/new-query2.png)
3. Másolja a következő T-SQL-lekérdezést a lekérdezési ablakba:
   
    ```sql
    SELECT COUNT(*) FROM dbo.FactInternetSales;
    ```
4. A lekérdezés futtatásához kattintson a zöld nyílra, vagy használja a következő billentyűparancsot: `CTRL` + `SHIFT` + `E` .
   
    ![A lekérdezés futtatása](./media/get-started-visual-studio/run-query.png)
5. Tekintse meg a lekérdezés eredményeit. Ebben a példában a FactInternetSales táblának 60 398 sora van.
   
    ![Lekérdezés eredményei](./media/get-started-visual-studio/query-results.png)

## <a name="next-steps"></a>További lépések
Most, hogy tud-e kapcsolatot létesíteni és lekérdezni, próbálja meg [az Power bi segítségével megjeleníteni az adatmegjelenítést](get-started-power-bi-professional.md).
A környezet Azure Active Directory hitelesítéshez való konfigurálásával kapcsolatban lásd: [hitelesítés az SQL-készlettel](../sql-data-warehouse/sql-data-warehouse-authentication.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).
 