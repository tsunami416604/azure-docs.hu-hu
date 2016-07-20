<properties
   pageTitle="Csatlakozás az SQL Data Warehouse-hoz a Visual Studióval | Microsoft Azure"
   description="Az SQL Data Warehouse-kapcsolat létrehozásának és a lekérdezések futtatásának első lépései."
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
   ms.date="05/13/2016"
   ms.author="sonyama;barbkess"/>

# Csatlakozás az SQL Data Warehouse-hoz a Visual Studióval

> [AZURE.SELECTOR]
- [Visual Studio](sql-data-warehouse-get-started-connect.md)
- [SQLCMD](sql-data-warehouse-get-started-connect-sqlcmd.md)
- [AAD](sql-data-warehouse-get-started-connect-aad-authentication.md)

Ez a bemutató ismerteti, hogyan lehet néhány perc alatt csatlakozni az Azure SQL Data Warehouse-hoz a Visual Studio SQL Server Data Tools (SSDT) bővítményével. A csatlakozás után le fog futtatni egy egyszerű lekérdezést.

## Előfeltételek

+ AdventureWorksDW-mintaadatok az SQL Data Warehouse-ban. A létrehozás menetét az [SQL Data Warehouse létrehozása][] című cikkben találja.
+ SQL Server Data Tools for Visual Studio. A telepítés menetéről és a beállításokról [A Visual Studio és az SSDT telepítése][] című cikkben olvashat bővebben.

## 1. lépés: A teljes Azure SQL-kiszolgálónév lekérdezése

Az SQL Data Warehouse-adatbázis egy Azure SQL Server-példányhoz van társítva. Az adatbázishoz való csatlakozáshoz szükség van a kiszolgáló teljes nevére (**kiszolgálónév**.database.windows.net*).

A teljes kiszolgálónév lekérdezése.

1. Nyissa meg az [Azure portált][].
2. Kattintson az **SQL-adatbázisok** elemre, majd kattintson arra az adatbázisra, amelyhez csatlakozni kíván. Ebben a példában az AdventureWorksDW mintaadatbázist használjuk.
3. Keresse meg a teljes kiszolgálónevet.

    ![Teljes kiszolgálónév][1]

## 2. lépés: Csatlakozás az SQL Data Warehouse-hoz

1. Nyissa meg a Visual Studio 2013-at vagy 2015-öt.
2. Nyissa meg az SQL Server Object Explorert. Ehhez válassza a következőket: **View** (Nézet)  > **SQL Server Object Explorer**.

    ![SQL Server Object Explorer][2]

3. Kattintson az **Add SQL Server** (SQL Server hozzáadása) ikonra.

    ![SQL Server hozzáadása][3]

4. Töltse ki az összes mezőt a Connect to Server (Csatlakozás a kiszolgálóhoz) ablakban.

    ![Csatlakozás kiszolgálóhoz][4]

    - **Kiszolgálónév**. Adja meg a korábban azonosított **kiszolgálónevet**.
    - **Hitelesítés**. Válassza az **SQL Server Authentication** (SQL Server-hitelesítés) vagy az **Active Directory Integrated Authentication** (Active Directory beépített hitelesítés) lehetőséget.
    - **Felhasználónév** és **Jelszó**. Amennyiben az SQL Server-hitelesítést választotta, adja meg felhasználónevét és jelszavát.
    - Kattintson a **Csatlakozás** gombra.

5. A részletes megtekintéshez bontsa ki az Azure SQL-kiszolgálót. Megtekintheti a kiszolgálóhoz társított adatbázisokat. Bontsa ki az AdventureWorksDW elemet a mintaadatbázis tábláinak megtekintéséhez.

    ![Az AdventureWorksDW áttekintése][5]

## 3. lépés: Mintalekérdezés futtatása

Most, hogy létrejött a kapcsolat az adatbázissal, ideje lefuttatni egy lekérdezést.

1. Kattintson a jobb gombbal az adatbázisára az SQL Server Object Explorer alatt.

2. Válassza a **New Query** (Új lekérdezés) lehetőséget. Megnyílik egy új lekérdezési ablak.

    ![Új lekérdezés][6]

3. Másolja be ezt a TSQL-lekérdezést a lekérdezési ablakba:

    ```sql
    SELECT COUNT(*) FROM dbo.FactInternetSales;
    ```

4. Futtassa a lekérdezést. Ehhez kattintson a zöld nyílra, vagy használja a következő billentyűparancsot: `CTRL`+`SHIFT`+`E`.

    ![A lekérdezés futtatása][7]

5. Tekintse meg a lekérdezés eredményeit. Ebben a példában a FactInternetSales táblának 60 398 sora van.

    ![Lekérdezés eredményei][8]

## További lépések

Most, hogy képes csatlakozni és elvégezni a lekérdezéseket, próbálja [megjeleníteni az adatokat a PowerBI használatával][].

A környezet Windows-hitelesítésre való konfigurálásához tekintse meg a [Csatlakozás az SQL Database vagy az SQL Data Warehouse szolgáltatáshoz Azure Active Directory-hitelesítéssel][] című cikket.

<!--Arcticles-->
[SQL Data Warehouse létrehozása]: sql-data-warehouse-get-started-provision.md
[A Visual Studio és az SSDT telepítése]: sql-data-warehouse-install-visual-studio.md
[Csatlakozás az SQL Database vagy az SQL Data Warehouse szolgáltatáshoz Azure Active Directory-hitelesítéssel]: ../sql-data-warehouse/sql-data-warehouse-get-started-connect-aad-authentication.md
[megjeleníteni az adatokat a PowerBI használatával]: ./sql-data-warehouse-get-started-visualize-with-power-bi.md  

<!--Other-->
[Azure portált]: https://portal.azure.com

<!--Image references-->

[1]: ./media/sql-data-warehouse-get-started-connect/get-server-name.png
[2]: ./media/sql-data-warehouse-get-started-connect/open-ssdt.png
[3]: ./media/sql-data-warehouse-get-started-connect/add-server.png
[4]: ./media/sql-data-warehouse-get-started-connect/connection-dialog.png
[5]: ./media/sql-data-warehouse-get-started-connect/explore-sample.png
[6]: ./media/sql-data-warehouse-get-started-connect/new-query2.png
[7]: ./media/sql-data-warehouse-get-started-connect/run-query.png
[8]: ./media/sql-data-warehouse-get-started-connect/query-results.png



<!--HONumber=Jun16_HO2-->


