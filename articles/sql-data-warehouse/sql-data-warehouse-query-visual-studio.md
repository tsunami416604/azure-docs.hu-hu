<properties
   pageTitle="Az Azure SQL Data Warehouse lekérdezése (Visual Studio) | Microsoft Azure"
   description="Az SQL Data Warehouse lekérdezése a Visual Studióval."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyam"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/16/2016"
   ms.author="sonyama;barbkess"/>

# Az Azure SQL Data Warehouse lekérdezése (Visual Studio)

> [AZURE.SELECTOR]
- [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
- [Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
- [Visual Studio](sql-data-warehouse-query-visual-studio.md)
- [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md) 

A Visual Studio használatával néhány perc alatt lekérdezheti az Azure SQL Data Warehouse-t. Ez a módszer a Visual Studio SQL Server Data Tools (SSDT) bővítményét használja. 

## Előfeltételek

Ehhez az oktatóanyaghoz a következőkre lesz szüksége:

+ Egy létező SQL Data Warehouse. A létrehozás menetét az [SQL Data Warehouse létrehozása][] című cikkben találja.
+ SSDT a Visual Studióhoz. Ha rendelkezik a Visual Studióval, akkor valószínűleg már ezzel is. A telepítés menetéről és a beállításokról [Installing Visual Studio and SSDT][] című cikkben olvashat bővebben.
+ Az Azure SQL-kiszolgáló teljes neve. Ennek megkeresésével kapcsolatban olvassa el a [Connect to SQL Data Warehouse][] (Csatlakozás az SQL Data Warehouse-hoz) című cikket.

## 1. Csatlakozás az SQL Data Warehouse-hoz

1. Nyissa meg a Visual Studio 2013-at vagy 2015-öt.
2. Nyissa meg az SQL Server Object Explorert. Ehhez válassza a következőket: **View** (Nézet)  > **SQL Server Object Explorer**.

    ![SQL Server Object Explorer][1]

3. Kattintson az **Add SQL Server** (SQL Server hozzáadása) ikonra.

    ![SQL Server hozzáadása][2]

4. Töltse ki az összes mezőt a Connect to Server (Csatlakozás a kiszolgálóhoz) ablakban.

    ![Csatlakozás kiszolgálóhoz][3]

    - **Kiszolgálónév**. Adja meg a korábban azonosított **kiszolgálónevet**.
    - **Hitelesítés**. Válassza az **SQL Server Authentication** (SQL Server-hitelesítés) vagy az **Active Directory Integrated Authentication** (Active Directory beépített hitelesítés) lehetőséget.
    - **Felhasználónév** és **Jelszó**. Amennyiben az SQL Server-hitelesítést választotta, adja meg felhasználónevét és jelszavát.
    - Kattintson a **Csatlakozás** gombra.

5. A részletes megtekintéshez bontsa ki az Azure SQL-kiszolgálót. Megtekintheti a kiszolgálóhoz társított adatbázisokat. Bontsa ki az AdventureWorksDW elemet a mintaadatbázis tábláinak megtekintéséhez.

    ![Az AdventureWorksDW áttekintése][4]

## 2. Mintalekérdezés futtatása

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

## Következő lépések

Most, hogy képes csatlakozni és elvégezni a lekérdezéseket, próbálja [megjeleníteni az adatokat a PowerBI használatával][].

A környezet Azure Active Directory-hitelesítésre történő konfigurálásával kapcsolatban tekintse meg az [Authenticate to SQL Data Warehouse][] (Hitelesítés az SQL Data Warehouse-ban) című cikket.

<!--Arcticles-->
[Connect to SQL Data Warehouse]: sql-data-warehouse-connect-overview.md
[SQL Data Warehouse létrehozása]: sql-data-warehouse-get-started-provision.md
[Installing Visual Studio and SSDT]: sql-data-warehouse-install-visual-studio.md
[Authenticate to SQL Data Warehouse]: sql-data-warehouse-authentication.md
[megjeleníteni az adatokat a PowerBI használatával]: sql-data-warehouse-get-started-visualize-with-power-bi.md  

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



<!--HONumber=sep16_HO1-->


