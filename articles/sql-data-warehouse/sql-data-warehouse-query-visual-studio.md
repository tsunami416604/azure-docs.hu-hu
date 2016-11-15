---
title: "Az Azure SQL Data Warehouse lekérdezése (Visual Studio) | Microsoft Docs"
description: "Az SQL Data Warehouse lekérdezése a Visual Studióval."
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jhubbard
editor: 
ms.assetid: daace889-95e5-4826-b2fc-047eac9d6d95
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 10/31/2016
ms.author: barbkess
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 5e97fefdf3cc13f2fd3e060da901e90ef5ef9a29


---
# <a name="query-azure-sql-data-warehouse-visual-studio"></a>Az Azure SQL Data Warehouse lekérdezése (Visual Studio)
> [!div class="op_single_selector"]
> * [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
> * [Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
> * [Visual Studio](sql-data-warehouse-query-visual-studio.md)
> * [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md) 
> * [SSMS](sql-data-warehouse-query-ssms.md)
> 
> 

A Visual Studio használatával néhány perc alatt lekérdezheti az Azure SQL Data Warehouse-t. Ez a módszer a Visual Studio SQL Server Data Tools (SSDT) bővítményét használja. 

## <a name="prerequisites"></a>Előfeltételek
Ehhez az oktatóanyaghoz a következőkre lesz szüksége:

* Egy létező SQL Data Warehouse. A létrehozás menetét az [SQL Data Warehouse létrehozása][SQL Data Warehouse létrehozása] című cikkben találja.
* SSDT a Visual Studióhoz. Ha rendelkezik a Visual Studióval, akkor valószínűleg már ezzel is. A telepítés menetéről és a beállításokról [A Visual Studio és az SSDT telepítése][A Visual Studio és az SSDT telepítése] című cikkben olvashat bővebben.
* Az Azure SQL-kiszolgáló teljes neve. Ennek megkeresésével kapcsolatban olvassa el a [Csatlakozás az SQL Data Warehouse-hoz][Csatlakozás az SQL Data Warehouse-hoz] című cikket.

## <a name="1-connect-to-your-sql-data-warehouse"></a>1. Csatlakozás az SQL Data Warehouse-hoz
1. Nyissa meg a Visual Studio 2013-at vagy 2015-öt.
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

## <a name="next-steps"></a>Következő lépések
Most, hogy képes csatlakozni és elvégezni a lekérdezéseket, következhet [az adatok megjelenítése a PowerBI használatával][az adatok megjelenítése a PowerBI használatával].

A környezet Azure Active Directory-hitelesítésre történő konfigurálásával kapcsolatban tekintse meg a [Hitelesítés az SQL Data Warehouse-ban][Hitelesítés az SQL Data Warehouse-ban] című cikket.

<!--Arcticles-->
[Csatlakozás az SQL Data Warehouse-hoz]: sql-data-warehouse-connect-overview.md
[SQL Data Warehouse létrehozása]: sql-data-warehouse-get-started-provision.md
[A Visual Studio és az SSDT telepítése]: sql-data-warehouse-install-visual-studio.md
[Hitelesítés az SQL Data Warehouse-ban]: sql-data-warehouse-authentication.md
[az adatok megjelenítése a PowerBI használatával]: sql-data-warehouse-get-started-visualize-with-power-bi.md  

<!--Other-->
[Azure Portal]: https://portal.azure.com

<!--Image references-->

[1]: media/sql-data-warehouse-query-visual-studio/open-ssdt.png
[2]: media/sql-data-warehouse-query-visual-studio/add-server.png
[3]: media/sql-data-warehouse-query-visual-studio/connection-dialog.png
[4]: media/sql-data-warehouse-query-visual-studio/explore-sample.png
[5]: media/sql-data-warehouse-query-visual-studio/new-query2.png
[6]: media/sql-data-warehouse-query-visual-studio/run-query.png
[7]: media/sql-data-warehouse-query-visual-studio/query-results.png



<!--HONumber=Nov16_HO2-->


