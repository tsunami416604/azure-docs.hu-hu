---
title: Csatlakozás SSMS segítségével
description: Az SQL Server Management Studio (SSMS) segítségével csatlakozzon és lekérdezze az Azure Synapse Analytics szolgáltatást.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 0d65ea9b684439cab384bbaa09fcdef1302b6bcf
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80743858"
---
# <a name="connect-to-azure-synapse-analytics-with-sql-server-management-studio-ssms"></a>Csatlakozás az Azure Synapse Analytics szolgáltatáshoz az SQL Server Management Studio (SSMS) segítségével

> [!div class="op_single_selector"]
>
> * [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
> * [Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
> * [Visual Studio](sql-data-warehouse-query-visual-studio.md)
> * [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md)
> * [SSMS](sql-data-warehouse-query-ssms.md)

Az SQL Server Management Studio (SSMS) használatával csatlakozhat az Azure Synapse-on belüli adattárházhoz, és lekérdezheti azt.

## <a name="prerequisites"></a>Előfeltételek

Ehhez az oktatóanyaghoz a következőkre lesz szüksége:

* Egy meglévő SQL-készlet. A létrehozáshoz olvassa el az [SQL-készlet létrehozása című témakört.](create-data-warehouse-portal.md)
* SQL Server Management Studio (SSMS) telepítve. [Töltse le az SSMS-t](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) ingyen, ha még nem rendelkezik vele.
* Az Azure SQL-kiszolgáló teljes neve. Az adatok megkereséséhez olvassa el [a Csatlakozás az SQL-készlethez című témakört.](sql-data-warehouse-connect-overview.md)

## <a name="1-connect-to-your-sql-pool"></a>1. Csatlakozás az SQL-készlethez

1. Nyissa meg az SSMS-t.
2. Az Objektumkezelő megnyitásához válassza a **Fájlcsatlakozási** > **objektumkezelő**t .

    ![SQL Server Object Explorer](./media/sql-data-warehouse-query-ssms/connect-object-explorer.png)
3. Töltse ki az összes mezőt a Connect to Server (Csatlakozás a kiszolgálóhoz) ablakban.

   ![Csatlakozás kiszolgálóhoz](./media/sql-data-warehouse-query-ssms/connect-object-explorer1.png)

   * **Kiszolgáló neve**. Adja meg a korábban azonosított **kiszolgálónevet**.
   * **Hitelesítés**. Válassza az **SQL Server Authentication** (SQL Server-hitelesítés) vagy az **Active Directory Integrated Authentication** (Active Directory beépített hitelesítés) lehetőséget.
   * **Felhasználónév** és **Jelszó**. Amennyiben az SQL Server-hitelesítést választotta, adja meg felhasználónevét és jelszavát.
   * Kattintson a **Csatlakozás** gombra.
4. A részletes megtekintéshez bontsa ki az Azure SQL-kiszolgálót. Megtekintheti a kiszolgálóhoz társított adatbázisokat. Bontsa ki az AdventureWorksDW elemet a mintaadatbázis tábláinak megtekintéséhez.

   ![Az AdventureWorksDW áttekintése](./media/sql-data-warehouse-query-ssms/explore-tables.png)

## <a name="2-run-a-sample-query"></a>2. Mintalekérdezés futtatása

Most, hogy létrejött a kapcsolat az adatbázissal, ideje lefuttatni egy lekérdezést.

1. Kattintson a jobb gombbal az adatbázisára az SQL Server Object Explorer alatt.
2. Válassza a **New Query** (Új lekérdezés) lehetőséget. Megnyílik egy új lekérdezési ablak.

   ![Új lekérdezés](./media/sql-data-warehouse-query-ssms/new-query.png)
3. Másolja a következő T-SQL lekérdezést a lekérdezésablakba:

   ```sql
   SELECT COUNT(*) FROM dbo.FactInternetSales;
   ```

4. Futtassa a `Execute` lekérdezést a következő `F5`parancsikonra kattintva vagy használva: .

   ![A lekérdezés futtatása](./media/sql-data-warehouse-query-ssms/execute-query.png)
5. Tekintse meg a lekérdezés eredményeit. Ebben a példában a FactInternetSales táblának 60 398 sora van.

   ![Lekérdezés eredményei](./media/sql-data-warehouse-query-ssms/results.png)

## <a name="next-steps"></a>További lépések

Most, hogy csatlakozhat és lekérdezhet, próbálja meg [viziviziálni az adatokat a Power BI-val.](sql-data-warehouse-get-started-visualize-with-power-bi.md) A környezet Azure Active Directory-hitelesítéshez való konfigurálásához olvassa el [a Hitelesítés az SQL-készletbe című témakört.](sql-data-warehouse-authentication.md)
