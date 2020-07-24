---
title: Kapcsolódás a szinapszis SQLhoz a SQL Server Management Studio (SSMS) használatával
description: SQL Server Management Studio (SSMS) használatával kapcsolódhat a szinapszis SQL-hez az Azure szinapszis Analytics szolgáltatásban, és lekérdezheti azokat.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: dc467eebbd7346777765af7143d13c76627ab648
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87077721"
---
# <a name="connect-to-synapse-sql-with-sql-server-management-studio-ssms"></a>Kapcsolódás a szinapszis SQLhoz a SQL Server Management Studio (SSMS) használatával
> [!div class="op_single_selector"]
> * [Azure Data Studio](get-started-azure-data-studio.md)
> * [Power BI](get-started-power-bi-professional.md)
> * [Visual Studio](../sql-data-warehouse/sql-data-warehouse-query-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
> * [Sqlcmd](../sql/get-started-connect-sqlcmd.md)
> * [SSMS](get-started-ssms.md)
> 
> 

A (z) [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) használatával kapcsolódhat, és lekérdezheti a szinapszis SQL-t az Azure szinapszis Analytics szolgáltatásban az SQL on-demand (előzetes verzió) vagy az SQL Pool-erőforrások használatával. 

### <a name="supported-tools-for-sql-on-demand-preview"></a>Támogatott SQL-eszközök az igény szerinti használatra (előzetes verzió)

A [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio) teljes mértékben támogatott a 1.18.0 verziótól kezdődően. A SSMS részlegesen támogatott a 18,5-es verziótól kezdődően, csak a kapcsolódáshoz és a lekérdezésekhez használható.

> [!NOTE]
> Ha a HRE-bejelentkezés a lekérdezés végrehajtása során több mint 1 órát nyit meg, akkor a HRE-ra támaszkodó összes lekérdezés sikertelen lesz. Ez magában foglalja a tárolás lekérdezését a HRE átmenő és az HRE-t használó utasításokkal (például külső szolgáltató létrehozása). Ez hatással van minden olyan eszközre, amely megtartja a kapcsolatokat, például a SSMS és a HIRDETÉSEKET tartalmazó lekérdezés-szerkesztőben. A lekérdezés végrehajtásához új kapcsolódást megnyitó eszközök nem érintettek, például a szinapszis Studio.
> A probléma megoldásához újraindíthatja a SSMS, vagy csatlakozhat a HIRDETÉSekhez, és leválaszthatja azokat. .
## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, győződjön meg arról, hogy rendelkezik a következő előfeltételekkel:  

* [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms). 
* SQL-készlet esetén egy meglévő adattárházra van szükség. A létrehozáshoz tekintse meg [az SQL-készlet létrehozása](../quickstart-create-sql-pool-portal.md)című témakört. Az SQL igény szerint az egyiket a rendszer a létrehozáskor már üzembe helyezi a munkaterületen. 
* A teljesen minősített SQL Server neve. Ennek megkereséséhez lásd: [Kapcsolódás a SZINAPSZIS SQL](connect-overview.md)-hez.

## <a name="connect"></a>Kapcsolódás

### <a name="sql-pool"></a>SQL-készlet

Ha az SQL-készlet használatával szeretne csatlakozni a szinapszis SQL-hez, kövesse az alábbi lépéseket: 

1. Nyissa meg SQL Server Management Studio (SSMS). 
1. A **Kapcsolódás a kiszolgálóhoz** párbeszédpanelen adja meg a mezőket, majd válassza a **Kapcsolódás**lehetőséget: 
  
    ![Csatlakozás kiszolgálóhoz](../sql-data-warehouse/media/sql-data-warehouse-query-ssms/connect-object-explorer1.png)
   
   * **Kiszolgáló neve**: adja meg a korábban azonosított **kiszolgálónevet** .
   * **Hitelesítés**: válasszon hitelesítési típust, például **SQL Server hitelesítést** vagy **Active Directory integrált hitelesítést**.
   * **Felhasználónév** és **jelszó**: adja meg felhasználónevét és jelszavát, ha SQL Server hitelesítés lett kiválasztva.

1. Bontsa ki az Azure-SQL Server **Object Explorer**. Megtekintheti a kiszolgálóhoz társított adatbázisokat, például a minta AdventureWorksDW-adatbázist. Az adatbázis kibontásával megjelenítheti a táblákat:
   
    ![Az AdventureWorksDW áttekintése](../sql-data-warehouse/media/sql-data-warehouse-query-ssms/explore-tables.png)


### <a name="sql-on-demand-preview"></a>Igény szerinti SQL-verzió (előzetes verzió)

Ha az SQL on-demand használatával szeretne csatlakozni a szinapszis SQL-hez, kövesse az alábbi lépéseket: 

1. Nyissa meg SQL Server Management Studio (SSMS).
1. A **Kapcsolódás a kiszolgálóhoz** párbeszédpanelen adja meg a mezőket, majd válassza a **Kapcsolódás**lehetőséget: 
   
    ![Csatlakozás kiszolgálóhoz](./media/get-started-ssms/connect-object-explorer1.png)
   
   * **Kiszolgáló neve**: adja meg a korábban azonosított **kiszolgálónevet** .
   * **Hitelesítés**: válasszon hitelesítési típust, például **SQL Server hitelesítést** vagy **Active Directory integrált hitelesítést**:
   * **Felhasználónév** és **jelszó**: adja meg felhasználónevét és jelszavát, ha SQL Server hitelesítés lett kiválasztva.
   * Kattintson a **Csatlakozás** gombra.

4. A részletes megtekintéshez bontsa ki az Azure SQL-kiszolgálót. Megtekintheti a kiszolgálóhoz társított adatbázisokat. Bontsa ki a *bemutatót* a mintaadatbázis tartalmának megtekintéséhez.
   
    ![Az AdventureWorksDW áttekintése](./media/get-started-ssms/explore-tables.png)


## <a name="run-a-sample-query"></a>Mintalekérdezés futtatása

### <a name="sql-pool"></a>SQL-készlet

Most, hogy létrejött az adatbázis-kapcsolatok, lekérdezheti az adatforrást.

1. Kattintson a jobb gombbal az adatbázisára az SQL Server Object Explorer alatt.
2. Válassza az **Új lekérdezés** lehetőséget. Megnyílik egy új lekérdezési ablak.
   
    ![Új lekérdezés](../sql-data-warehouse/media/sql-data-warehouse-query-ssms/new-query.png)
3. Másolja ezt a T-SQL-lekérdezést a lekérdezési ablakba:
   
    ```sql
    SELECT COUNT(*) FROM dbo.FactInternetSales;
    ```
4. Futtassa a lekérdezést. Ehhez kattintson `Execute` vagy használja a következő billentyűparancsot: `F5` .
   
    ![A lekérdezés futtatása](../sql-data-warehouse/media/sql-data-warehouse-query-ssms/execute-query.png)
5. Tekintse meg a lekérdezés eredményeit. Ebben a példában a FactInternetSales táblának 60 398 sora van.
   
    ![Lekérdezés eredményei](../sql-data-warehouse/media/sql-data-warehouse-query-ssms/results.png)

### <a name="sql-on-demand"></a>Igény szerinti SQL

Most, hogy létrehozta az adatbázis-kapcsolatokat, lekérdezheti az adatforrást.

1. Kattintson a jobb gombbal az adatbázisára az SQL Server Object Explorer alatt.
2. Válassza az **Új lekérdezés** lehetőséget. Megnyílik egy új lekérdezési ablak.
   
    ![Új lekérdezés](./media/get-started-ssms/new-query.png)
3. Másolja a következő T-SQL-lekérdezést a lekérdezési ablakba:
   
    ```sql
    SELECT COUNT(*) FROM demo.dbo.usPopulationView
    ```
4. Futtassa a lekérdezést. Ehhez kattintson `Execute` vagy használja a következő billentyűparancsot: `F5` .
   
    ![A lekérdezés futtatása](./media/get-started-ssms/execute-query.png)
5. Tekintse meg a lekérdezés eredményeit. Ebben a példában a usPopulationView nézet 3664512 sorral rendelkezik.
   
    ![Lekérdezés eredményei](./media/get-started-ssms/results.png)

## <a name="next-steps"></a>További lépések
Most, hogy tud-e kapcsolatot létesíteni és lekérdezni, próbálja meg [az Power bi segítségével megjeleníteni az adatmegjelenítést](get-started-power-bi-professional.md).

A környezet Azure Active Directory hitelesítéshez való konfigurálásával kapcsolatban lásd: [hitelesítés a SZINAPSZIS SQL](../sql-data-warehouse/sql-data-warehouse-authentication.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)-ben.

