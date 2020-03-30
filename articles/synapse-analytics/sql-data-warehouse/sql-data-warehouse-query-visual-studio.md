---
title: Csatlakozás a VSTS-hez
description: Azure Synapse Analytics lekérdezése a Visual Studióval.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 08/15/2019
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: e986515a911cf1bbd88dfc73c56efcc6e81826d6
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80351406"
---
# <a name="connect-to-azure-synapse-analytics-with-visual-studio-and-ssdt"></a>Csatlakozás az Azure Synapse Analytics szolgáltatáshoz a Visual Studio és az SSDT segítségével
> [!div class="op_single_selector"]
> * [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
> * [Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
> * [Vizuális stúdió](sql-data-warehouse-query-visual-studio.md)
> * [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md) 
> * [SSMS](sql-data-warehouse-query-ssms.md)
> 
> 

A Visual Studio segítségével néhány perc alatt lekérdezhet egy SQL-készletet az Azure Synapse-on belül. Ez a módszer az SQL Server Data Tools (SSDT) bővítményt használja a Visual Studio 2019-ben. 

## <a name="prerequisites"></a>Előfeltételek
Ehhez az oktatóanyaghoz a következőkre lesz szüksége:

* Egy meglévő SQL-készlet. A létrehozáshoz olvassa el az [SQL-készlet létrehozása című témakört.](create-data-warehouse-portal.md)
* SSDT a Visual Studióhoz. Ha rendelkezik a Visual Studio-val, akkor valószínűleg már rendelkezik SSDT for Visual Studio-val. A telepítés menetéről és a beállításokról [A Visual Studio és az SSDT telepítése](sql-data-warehouse-install-visual-studio.md) című cikkben olvashat bővebben.
* Az Azure SQL-kiszolgáló teljes neve. Az adatok megkereséséhez olvassa el [a Csatlakozás az SQL-készlethez című témakört.](sql-data-warehouse-connect-overview.md)

## <a name="1-connect-to-your-sql-pool"></a>1. Csatlakozás az SQL-készlethez
1. Nyissa meg a Visual Studio 2019-et.
2. Nyissa meg az SQL Server Object Explorer megnyitását az**SQL Server Object Explorer** **megtekintése** > lehetőséget választva.
   
    ![SQL Server Object Explorer](./media/sql-data-warehouse-query-visual-studio/open-ssdt.png)
3. Kattintson az **Add SQL Server** (SQL Server hozzáadása) ikonra.
   
    ![SQL Server hozzáadása](./media/sql-data-warehouse-query-visual-studio/add-server.png)
4. Töltse ki az összes mezőt a Connect to Server (Csatlakozás a kiszolgálóhoz) ablakban.
   
    ![Csatlakozás kiszolgálóhoz](./media/sql-data-warehouse-query-visual-studio/connection-dialog.png)
   
   * **Kiszolgáló neve**. Adja meg a korábban azonosított **kiszolgálónevet**.
   * **Hitelesítés**. Válassza az **SQL Server Authentication** (SQL Server-hitelesítés) vagy az **Active Directory Integrated Authentication** (Active Directory beépített hitelesítés) lehetőséget.
   * **Felhasználónév** és **Jelszó**. Amennyiben az SQL Server-hitelesítést választotta, adja meg felhasználónevét és jelszavát.
   * Kattintson a **Csatlakozás** gombra.
5. A részletes megtekintéshez bontsa ki az Azure SQL-kiszolgálót. Megtekintheti a kiszolgálóhoz társított adatbázisokat. Bontsa ki az AdventureWorksDW elemet a mintaadatbázis tábláinak megtekintéséhez.
   
    ![Az AdventureWorksDW áttekintése](./media/sql-data-warehouse-query-visual-studio/explore-sample.png)

## <a name="2-run-a-sample-query"></a>2. Mintalekérdezés futtatása
Most, hogy létrejött a kapcsolat az adatbázissal, ideje lefuttatni egy lekérdezést.

1. Kattintson a jobb gombbal az adatbázisára az SQL Server Object Explorer alatt.
2. Válassza a **New Query** (Új lekérdezés) lehetőséget. Megnyílik egy új lekérdezési ablak.
   
    ![Új lekérdezés](./media/sql-data-warehouse-query-visual-studio/new-query2.png)
3. Másolja a következő T-SQL lekérdezést a lekérdezésablakba:
   
    ```sql
    SELECT COUNT(*) FROM dbo.FactInternetSales;
    ```
4. Futtassa a lekérdezést a zöld nyílra `CTRL` + `SHIFT` + `E`kattintva, vagy használja a következő parancsikont: .
   
    ![A lekérdezés futtatása](./media/sql-data-warehouse-query-visual-studio/run-query.png)
5. Tekintse meg a lekérdezés eredményeit. Ebben a példában a FactInternetSales táblának 60 398 sora van.
   
    ![Lekérdezés eredményei](./media/sql-data-warehouse-query-visual-studio/query-results.png)

## <a name="next-steps"></a>További lépések
Most, hogy csatlakozhat és lekérdezhet, próbálja meg [viziviziálni az adatokat a Power BI-val.](sql-data-warehouse-get-started-visualize-with-power-bi.md)

A környezet Azure Active Directory-hitelesítéshez való konfigurálásához olvassa el [a Hitelesítés az SQL-készletbe című témakört.](sql-data-warehouse-authentication.md)