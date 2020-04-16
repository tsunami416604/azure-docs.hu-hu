---
title: 'SSMS: Synapse SQL csatlakoztatása és lekérdezése'
description: Az SQL Server Management Studio (SSMS) segítségével csatlakozzon és lekérdezze a Synapse SQL-t az Azure Synapse Analytics szolgáltatásban.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: 704da86fd1d816dbf5d6cd9cf67dfee53fce2622
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81423739"
---
# <a name="connect-to-synapse-sql-with-sql-server-management-studio-ssms"></a>Csatlakozás a Synapse SQL-hez az SQL Server Management Studio (SSMS) segítségével
> [!div class="op_single_selector"]
> * [Azure Data Studio](get-started-azure-data-studio.md)
> * [Power BI](get-started-power-bi-professional.md)
> * [Visual Studio](../sql-data-warehouse/sql-data-warehouse-query-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
> * [sqlcmd](../sql/get-started-connect-sqlcmd.md)
> * [SSMS](get-started-ssms.md)
> 
> 

Az [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) segítségével csatlakozhat és lekérdezheti a Synapse SQL-t az Azure Synapse Analytics-ben az SQL on-demand (előzetes verzió) vagy az SQL-készlet erőforrásainak használatával. 

### <a name="supported-tools-for-sql-on-demand-preview"></a>Támogatott eszközök az igény szerinti SQL-hez (előzetes verzió)

Az SSMS a 18.5-ös verziótól kezdve részlegesen támogatott, korlátozott funkciókkal, például csatlakozással és lekérdezéssel. [Az Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio) teljes mértékben támogatott.

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, győződjön meg arról, hogy a következő előfeltételek:  

* [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms). 
* Az SQL-készlethez egy meglévő adattárházra van szükség. A létrehozáshoz olvassa el az [SQL-készlet létrehozása című témakört.](../quickstart-create-sql-pool.md) Az IGÉNY szerinti SQL esetén már ki van építve a munkaterületen a létrehozás időpontjában. 
* A teljesen minősített SQL Server név. Ennek megkereséséhez olvassa el [a Csatlakozás a Synapse SQL-hez](connect-overview.md)című témakört.

## <a name="connect"></a>Kapcsolódás

### <a name="sql-pool"></a>SQL-készlet

Ha sql készlethasználatával szeretne csatlakozni a Synapse SQL-hez, kövesse az alábbi lépéseket: 

1. Nyissa meg az SQL Server Management Studio (SSMS) alkalmazást. 
1. A **Csatlakozás kiszolgálóhoz** párbeszédpanelen töltse ki a mezőket, és válassza a **Csatlakozás**lehetőséget: 
  
    ![Csatlakozás kiszolgálóhoz](../sql-data-warehouse/media/sql-data-warehouse-query-ssms/connect-object-explorer1.png)
   
   * **Kiszolgáló neve**: Adja meg a korábban azonosított **kiszolgálónevet.**
   * **Hitelesítés**: Válasszon egy hitelesítési típust, például **az SQL Server hitelesítést** vagy **az Active Directory integrált hitelesítést.**
   * **Felhasználónév** és **jelszó:** Adja meg a felhasználónevét és a jelszavát, ha az SQL Server hitelesítés e szerint lett kiválasztva.

1. Bontsa ki az Azure SQL Server alkalmazást az **Object Explorer**ben. Megtekintheti a kiszolgálóhoz társított adatbázisokat, például az AdventureWorksDW-adatbázis mintáját. Az adatbázis tágíthatja a táblák megtekintéséhez:
   
    ![Az AdventureWorksDW áttekintése](../sql-data-warehouse/media/sql-data-warehouse-query-ssms/explore-tables.png)


### <a name="sql-on-demand-preview"></a>SQL igény szerinti (előzetes verzió)

Ha igény szerint az SQL-t használja a Synapse SQL-hez, kövesse az alábbi lépéseket: 

1. Nyissa meg az SQL Server Management Studio (SSMS) alkalmazást.
1. A **Csatlakozás kiszolgálóhoz** párbeszédpanelen töltse ki a mezőket, és válassza a **Csatlakozás**lehetőséget: 
   
    ![Csatlakozás kiszolgálóhoz](./media/get-started-ssms/connect-object-explorer1.png)
   
   * **Kiszolgáló neve**: Adja meg a korábban azonosított **kiszolgálónevet.**
   * **Hitelesítés**: Válasszon egy hitelesítési típust, például **az SQL Server hitelesítést** vagy **az Active Directory integrált hitelesítést:**
   * **Felhasználónév** és **jelszó:** Adja meg a felhasználónevét és a jelszavát, ha az SQL Server hitelesítés e szerint lett kiválasztva.
   * Kattintson a **Csatlakozás** gombra.

4. A részletes megtekintéshez bontsa ki az Azure SQL-kiszolgálót. Megtekintheti a kiszolgálóhoz társított adatbázisokat. Bontsa ki a *bemutatót* a mintaadatbázis tartalmának megtekintéséhez.
   
    ![Az AdventureWorksDW áttekintése](./media/get-started-ssms/explore-tables.png)


## <a name="run-a-sample-query"></a>Mintalekérdezés futtatása

### <a name="sql-pool"></a>SQL-készlet

Most, hogy létrejött egy adatbázis-kapcsolat, lekérdezheti az adatokat.

1. Kattintson a jobb gombbal az adatbázisára az SQL Server Object Explorer alatt.
2. Válassza a **New Query** (Új lekérdezés) lehetőséget. Megnyílik egy új lekérdezési ablak.
   
    ![Új lekérdezés](../sql-data-warehouse/media/sql-data-warehouse-query-ssms/new-query.png)
3. Másolja a T-SQL lekérdezést a lekérdezésablakba:
   
    ```sql
    SELECT COUNT(*) FROM dbo.FactInternetSales;
    ```
4. Futtassa a lekérdezést. Ehhez kattintson `Execute` vagy használja a `F5`következő parancsikont: .
   
    ![A lekérdezés futtatása](../sql-data-warehouse/media/sql-data-warehouse-query-ssms/execute-query.png)
5. Tekintse meg a lekérdezés eredményeit. Ebben a példában a FactInternetSales táblának 60 398 sora van.
   
    ![Lekérdezés eredményei](../sql-data-warehouse/media/sql-data-warehouse-query-ssms/results.png)

### <a name="sql-on-demand"></a>SQL igény szerint

Most, hogy létrehozott egy adatbázis-kapcsolatot, lekérdezheti az adatokat.

1. Kattintson a jobb gombbal az adatbázisára az SQL Server Object Explorer alatt.
2. Válassza a **New Query** (Új lekérdezés) lehetőséget. Megnyílik egy új lekérdezési ablak.
   
    ![Új lekérdezés](./media/get-started-ssms/new-query.png)
3. Másolja a következő T-SQL lekérdezést a lekérdezésablakba:
   
    ```sql
    SELECT COUNT(*) FROM demo.dbo.usPopulationView
    ```
4. Futtassa a lekérdezést. Ehhez kattintson `Execute` vagy használja a `F5`következő parancsikont: .
   
    ![A lekérdezés futtatása](./media/get-started-ssms/execute-query.png)
5. Tekintse meg a lekérdezés eredményeit. Ebben a példában a usPopulationView nézet 3664512 sort tartalmaz.
   
    ![Lekérdezés eredményei](./media/get-started-ssms/results.png)

## <a name="next-steps"></a>További lépések
Most, hogy csatlakozhat és lekérdezhet, próbálja meg [viziviziálni az adatokat a Power BI-val.](get-started-power-bi-professional.md)

A környezet Azure Active Directory-hitelesítéshez való konfigurálásához olvassa el a [Hitelesítés a szinapszis SQL-be című témakört.](../sql-data-warehouse/sql-data-warehouse-authentication.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

