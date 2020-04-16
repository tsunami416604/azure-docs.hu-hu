---
title: Csatlakozás a Synapse SQL-hez és lekérdezés a Visual Studio és az SSDT segítségével
description: A Visual Studio segítségével lekérdezheti az SQL-készletet az Azure Synapse Analytics használatával.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: 5e725df5c875f23d82da1fda19bcdf28db548fc8
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81428562"
---
# <a name="connect-to-synapse-sql-with-visual-studio-and-ssdt"></a>Csatlakozás a Synapse SQL-hez a Visual Studio és az SSDT segítségével
> [!div class="op_single_selector"]
> * [Azure Data Studio](get-started-azure-data-studio.md)
> * [Power BI](get-started-power-bi-professional.md)
> * [Visual Studio](get-started-visual-studio.md)
> * [sqlcmd](get-started-connect-sqlcmd.md) 
> * [SSMS](get-started-ssms.md)
> 
> 

A Visual Studio segítségével lekérdezheti az SQL-készletet az Azure Synapse Analytics használatával. Ez a módszer az SQL Server Data Tools (SSDT) bővítményt használja a Visual Studio 2019-ben. 

### <a name="supported-tools-for-sql-on-demand-preview"></a>Támogatott eszközök az igény szerinti SQL-hez (előzetes verzió)

A Visual Studio jelenleg nem támogatott az SQL igény szerinti (előzetes verzió) esetén. Azonban az Azure Data Studi (előzetes verzió)o egy teljes mértékben támogatott eszköz. Az SQL Server Management Studio részben támogatja a 18.4-es verziót, és korlátozott funkciókkal rendelkezik, például a csatlakozással és a lekérdezéssel.

## <a name="prerequisites"></a>Előfeltételek
Az oktatóanyag használatához a következő összetevőkre van szükség:

* Egy meglévő SQL-készlet. Ha még nem rendelkezik ilyen, [olvassa el az SQL-készlet létrehozása](../sql-data-warehouse/create-data-warehouse-portal.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) az előfeltétel teljesítéséhez című témakört.
* SSDT a Visual Studióhoz. Ha rendelkezik a Visual Studio alkalmazásokkal, valószínűleg már rendelkezik ezzel az összetevővel. A telepítés menetéről és a beállításokról [A Visual Studio és az SSDT telepítése](../sql-data-warehouse/sql-data-warehouse-install-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) című cikkben olvashat bővebben.
* Az Azure SQL-kiszolgáló teljes neve. Ennek megkereséséhez olvassa el [a Csatlakozás az SQL-készlethez című témakört.](connect-overview.md)

## <a name="1-connect-to-sql-pool"></a>1. Csatlakozás SQL-készlethez
1. Nyissa meg a Visual Studio 2019-et.
2. Nyissa meg az SQL Server Object Explorert. Ehhez válassza az**SQL Server Object Explorer** **megtekintése** > lehetőséget.
   
    ![SQL Server Object Explorer](./media/get-started-visual-studio/open-ssdt.png)
3. Kattintson az **Add SQL Server** (SQL Server hozzáadása) ikonra.
   
    ![SQL Server hozzáadása](./media/get-started-visual-studio/add-server.png)
4. Töltse ki az összes mezőt a Connect to Server (Csatlakozás a kiszolgálóhoz) ablakban.
   
    ![Csatlakozás kiszolgálóhoz](./media/get-started-visual-studio/connection-dialog.png)
   
   * **Kiszolgáló neve**: Adja meg a korábban azonosított **kiszolgálónevet.**
   * **Hitelesítés**: Válassza az **SQL Server hitelesítés** vagy az Active Directory integrált hitelesítés **lehetőséget:**
   * **Felhasználónév** és **jelszó:** Adja meg a felhasználónevét és a jelszavát, ha az SQL Server hitelesítés e szerint lett kiválasztva.
   * Kattintson a **Csatlakozás** gombra.
5. A részletes megtekintéshez bontsa ki az Azure SQL-kiszolgálót. Megtekintheti a kiszolgálóhoz társított adatbázisokat. Bontsa ki az AdventureWorksDW elemet a mintaadatbázis tábláinak megtekintéséhez.
   
    ![Az AdventureWorksDW áttekintése](./media/get-started-visual-studio/explore-sample.png)

## <a name="2-run-a-sample-query"></a>2. Mintalekérdezés futtatása
Most, hogy létrejött a kapcsolat az adatbázissal, lekérdezést fog írni.

1. Kattintson a jobb gombbal az adatbázisára az SQL Server Object Explorer alatt.
2. Válassza a **New Query** (Új lekérdezés) lehetőséget. Megnyílik egy új lekérdezési ablak.
   
    ![Új lekérdezés](./media/get-started-visual-studio/new-query2.png)
3. Másolja a következő T-SQL lekérdezést a lekérdezésablakba:
   
    ```sql
    SELECT COUNT(*) FROM dbo.FactInternetSales;
    ```
4. Futtassa a lekérdezést. Ehhez kattintson a zöld nyílra, vagy használja a következő billentyűparancsot: `CTRL`+`SHIFT`+`E`.
   
    ![A lekérdezés futtatása](./media/get-started-visual-studio/run-query.png)
5. Tekintse meg a lekérdezés eredményeit. Ebben a példában a FactInternetSales táblának 60 398 sora van.
   
    ![Lekérdezés eredményei](./media/get-started-visual-studio/query-results.png)

## <a name="next-steps"></a>További lépések
Most, hogy csatlakozhat és lekérdezhet, próbálja meg [viziviziálni az adatokat a Power BI-val.](get-started-power-bi-professional.md)
A környezet Azure Active Directory-hitelesítéshez való konfigurálásához olvassa el [a Hitelesítés az SQL-készletbe című témakört.](../sql-data-warehouse/sql-data-warehouse-authentication.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
 