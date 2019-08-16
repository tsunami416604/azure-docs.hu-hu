---
title: Csatlakozás az Azure SQL Data Warehouse-hoz – VSTS | Microsoft Docs
description: Az SQL Data Warehouse lekérdezése a Visual Studióval.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 08/15/2019
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: aea2b3229dd793bc31adc0038763e09340a5f85a
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/16/2019
ms.locfileid: "69534775"
---
# <a name="connect-to-sql-data-warehouse-with-visual-studio-and-ssdt"></a>Csatlakozás a SQL Data Warehouse-hoz a Visual Studio és az SSDT használatával
> [!div class="op_single_selector"]
> * [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
> * [Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
> * [Visual Studio](sql-data-warehouse-query-visual-studio.md)
> * [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md) 
> * [SSMS](sql-data-warehouse-query-ssms.md)
> 
> 

A Visual Studio használatával néhány perc alatt lekérdezheti az Azure SQL Data Warehouse-t. Ez a metódus a SQL Server Data Tools (SSDT) bővítményt használja a Visual Studio 2019-ben. 

## <a name="prerequisites"></a>Előfeltételek
Ehhez az oktatóanyaghoz a következőkre lesz szüksége:

* Egy létező SQL Data Warehouse. A létrehozás menetét az [SQL Data Warehouse létrehozása][Create a SQL Data Warehouse] című cikkben találja.
* SSDT a Visual Studióhoz. Ha rendelkezik a Visual Studióval, akkor valószínűleg már ezzel is. A telepítés menetéről és a beállításokról [Installing Visual Studio and SSDT][Installing Visual Studio and SSDT] című cikkben olvashat bővebben.
* Az Azure SQL-kiszolgáló teljes neve. Ennek megkeresésével kapcsolatban olvassa el a [Connect to SQL Data Warehouse][Connect to SQL Data Warehouse] (Csatlakozás az SQL Data Warehouse-hoz) című cikket.

## <a name="1-connect-to-your-sql-data-warehouse"></a>1. Csatlakozás az SQL Data Warehouse-hoz
1. Nyissa meg a Visual Studio 2019 alkalmazást.
2. Nyissa meg az SQL Server Object Explorert. Ehhez válassza a következőket: **View** (Nézet)  > **SQL Server Object Explorer**.
   
    ![SQL Server Object Explorer][1]
3. Kattintson az **Add SQL Server** (SQL Server hozzáadása) ikonra.
   
    ![SQL Server hozzáadása][2]
4. Töltse ki az összes mezőt a Connect to Server (Csatlakozás a kiszolgálóhoz) ablakban.
   
    ![Csatlakozás kiszolgálóhoz][3]
   
   * **Kiszolgálónév**. Adja meg a korábban azonosított **kiszolgálónevet**.
   * **Hitelesítés**. Válassza az **SQL Server Authentication** (SQL Server-hitelesítés) vagy az **Active Directory Integrated Authentication** (Active Directory beépített hitelesítés) lehetőséget.
   * **Felhasználónév** és **Jelszó**. Amennyiben az SQL Server-hitelesítést választotta, adja meg felhasználónevét és jelszavát.
   * Kattintson a **Csatlakozás** gombra.
5. A részletes megtekintéshez bontsa ki az Azure SQL-kiszolgálót. Megtekintheti a kiszolgálóhoz társított adatbázisokat. Bontsa ki az AdventureWorksDW elemet a mintaadatbázis tábláinak megtekintéséhez.
   
    ![Az AdventureWorksDW áttekintése][4]

## <a name="2-run-a-sample-query"></a>2. Mintalekérdezés futtatása
Most, hogy létrejött a kapcsolat az adatbázissal, ideje lefuttatni egy lekérdezést.

1. Kattintson a jobb gombbal az adatbázisára az SQL Server Object Explorer alatt.
2. Válassza a **New Query** (Új lekérdezés) lehetőséget. Megnyílik egy új lekérdezési ablak.
   
    ![Új lekérdezés][5]
3. Másolja be ezt a TSQL-lekérdezést a lekérdezési ablakba:
   
    ```sql
    SELECT COUNT(*) FROM dbo.FactInternetSales;
    ```
4. Futtassa a lekérdezést. Ehhez kattintson a zöld nyílra, vagy használja a következő billentyűparancsot: `CTRL`+`SHIFT`+`E`.
   
    ![A lekérdezés futtatása][6]
5. Tekintse meg a lekérdezés eredményeit. Ebben a példában a FactInternetSales táblának 60 398 sora van.
   
    ![Lekérdezés eredményei][7]

## <a name="next-steps"></a>További lépések
Most, hogy képes csatlakozni és elvégezni a lekérdezéseket, próbálja [megjeleníteni az adatokat a PowerBI használatával][visualizing the data with PowerBI].

A környezet Azure Active Directory-hitelesítésre történő konfigurálásával kapcsolatban tekintse meg az [Authenticate to SQL Data Warehouse][Authenticate to SQL Data Warehouse] (Hitelesítés az SQL Data Warehouse-ban) című cikket.

<!--Arcticles-->
[Connect to SQL Data Warehouse]: sql-data-warehouse-connect-overview.md
[Create a SQL Data Warehouse]: sql-data-warehouse-get-started-provision.md
[Installing Visual Studio and SSDT]: sql-data-warehouse-install-visual-studio.md
[Authenticate to SQL Data Warehouse]: sql-data-warehouse-authentication.md
[visualizing the data with PowerBI]: sql-data-warehouse-get-started-visualize-with-power-bi.md  

<!--Other-->
[Azure portal]: https://portal.azure.com

<!--Image references-->

[1]: media/sql-data-warehouse-query-visual-studio/open-ssdt.png
[2]: media/sql-data-warehouse-query-visual-studio/add-server.png
[3]: media/sql-data-warehouse-query-visual-studio/connection-dialog.png
[4]: media/sql-data-warehouse-query-visual-studio/explore-sample.png
[5]: media/sql-data-warehouse-query-visual-studio/new-query2.png
[6]: media/sql-data-warehouse-query-visual-studio/run-query.png
[7]: media/sql-data-warehouse-query-visual-studio/query-results.png
