<properties
   pageTitle="SQL Data Warehouse-adatok ábrázolása a Power BI használatával |Microsoft Azure"
   description="SQL Data Warehouse-adatok ábrázolása a Power BI használatával"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="03/03/2016"
   ms.author="lodipalm;barbkess;sonyama"/>

# Adatok ábrázolása a Power BI használatával

Az alábbi oktatóanyagból megtudhatja, hogyan kapcsolódhat a Power BI-ból az SQL Data Warehouse-hoz. Az oktatóanyag emellett néhány alapvető adatábrázolás létrehozásának lépéseit is bemutatja.

> [AZURE.NOTE] Az oktatóanyag teljesítéséhez egy SQL Data Warehouse-adatbázis szükséges, amely előzetesen fel lett töltve az AdventureWorksDW mintaadatbázis adataival. Az adatbázis létrehozásának módjáról az [SQL Data Warehouse-adatraktár létrehozása](sql-data-warehouse-get-started-provision.md) című cikkben olvashat.
>
> Ha már létrehozta saját SQL Data Warehouse-adatbázisát, de még nem rendelkezik mintaadatokkal, [manuálisan is betöltheti a mintaadatokat][].

> [AZURE.VIDEO azure-sql-data-warehouse-sample-data-and-powerbi]

## Kapcsolódás az AdventureWorksDW mintaadatbázishoz

A Power BI megnyitásához, és az AdventureWorksDW mintaadatbázishoz való kapcsolódásához tegye a következőket:

1. Jelentkezzen be az [Azure-portálra][].
2. Kattintson az **SQL adatbázisok** elemre, és válassza ki az AdventureWorks nevű SQL Data Warehouse-adatbázist.

    ![Az adatbázis megkeresése][1]

3. Kattintson a Megnyitás a Power BI-ban gombra.

    ![Power BI-gomb][2]

4. Ekkor megjelenik az adatbázis webcímét tartalmazó SQL Data Warehouse-kapcsolódási oldal. Kattintson a Tovább gombra.

    ![Power BI-kapcsolat][3]

6. Adja meg az Azure-beli SQL-kiszolgálóhoz tartozó felhasználónevét és jelszavát, és ezután már teljes mértékben hozzáférhet saját SQL Data Warehouse-adatbázisához.

    ![Bejelentkezés a Power BI-ba][4]

1. Miután bejelentkezett Power BI-fiókjába, kattintson a bal oldali panelen lévő AdventureWorksDW adatkészletre. Ekkor megnyílik az adatbázis.

    ![Az AdventureWorksDW adatbázis megnyitása a Power BI-ban][5]



## Power BI-jelentés létrehozása a mintaadatok elemzéséhez

Ezek után már elemezheti az AdventureWorksDW mintaadatait a Power BI segítségével. Az elemzéshez használja az AdventureWorksDW adatbázis AggregateSales (Eladások összesítése) nézetét. Ez a nézet olyan alapvető metrikákat tartalmaz, amelyek alapján elemezhetők a példában szereplő vállalat eladásai.

1. Ha az eladások összegéről irányítószám alapján térképet szeretne készíteni, kattintással bontsa ki a mezők jobb oldali ablaktábláján lévő AggregateSales (Eladások összesítése) nézetet. Kattintással jelölje ki a PostalCode (Irányítószám) és a SalesAmount (Eladási összeg) oszlopot.

    ![Az AggregateSales nézet kiválasztása a Power BI-ban][6]

    A Power BI automatikusan felismeri a földrajzi adatokat, és térképet készít ezekből.

    ![Power BI-térkép][7]

2. Ebben a lépésben olyan sávdiagramot hozhat létre, amely az eladások összegét az egyes ügyfelektől származó bevétel szerinti bontásban szemlélteti. Ennek létrehozásához kattintson a kibontott AggregateSales (Eladások összesítése) nézetben a SalesAmount (Eladási összeg) mezőre. Jelölje ki a Customer Income (Ügyféltől származó bevétel) mezőt, és húzza a bal oldali Tengely szakaszra.

    ![Tengely kiválasztása a Power BI-ban][8]

    Így áthelyezte a bal oldalra a sávdiagramot.

    ![Power BI-sávdiagram][9]

3. Ez a vonaldiagram rendelési dátum szerinti bontásban szemlélteti az eladási összegeket. Ennek létrehozásához kattintson a kibontott AggregateSales (Eladások összesítése) nézetben a Sales Amount (Eladási összeg) és az OrderDate (Rendelési dátum) elemre. A Megjelenítések oszlopban kattintson a Vonaldiagram ikonra (a második sorban az első ikon).

    ![Vonaldiagram kiválasztása Power BI-ban][10]

    Így készíthet jelentést, amely három különböző módon szemlélteti az adatokat.

    ![Power BI-vonaldiagram][11]

Munkáját bármikor mentheti a **Fájl** menü **Mentés** menüpontjára kattintva.

## Következő lépések
Ha már biztosan mozog a mintaadatok kezelésében, ismerkedjen meg az adatok [fejlesztésének][], [betöltésének][], és [áttelepítésének][] módjával.

<!--Image references-->
[1]:./media/sql-data-warehouse-get-started-analyze-data-with-power-bi/pbi-find-database.png
[2]:./media/sql-data-warehouse-get-started-analyze-data-with-power-bi/pbi-button.png
[3]:./media/sql-data-warehouse-get-started-analyze-data-with-power-bi/pbi-connect-to-azure.png
[4]:./media/sql-data-warehouse-get-started-analyze-data-with-power-bi/pbi-sign-in.png
[5]:./media/sql-data-warehouse-get-started-analyze-data-with-power-bi/pbi-open-adventureworks.png
[6]:./media/sql-data-warehouse-get-started-analyze-data-with-power-bi/pbi-aggregatesales.png
[7]:./media/sql-data-warehouse-get-started-analyze-data-with-power-bi/pbi-map.png
[8]:./media/sql-data-warehouse-get-started-analyze-data-with-power-bi/pbi-chooseaxis.png
[9]:./media/sql-data-warehouse-get-started-analyze-data-with-power-bi/pbi-bar.png
[10]:./media/sql-data-warehouse-get-started-analyze-data-with-power-bi/pbi-prepare-line.png
[11]:./media/sql-data-warehouse-get-started-analyze-data-with-power-bi/pbi-line.png
[12]:./media/sql-data-warehouse-get-started-analyze-data-with-power-bi/pbi-save.png

<!--Article references-->
[áttelepítés]: ./sql-data-warehouse-overview-migrate.md
[fejlesztésének]: ./sql-data-warehouse-overview-develop.md
[betöltés]: ./sql-data-warehouse-overview-load.md
[manuálisan is betöltheti a mintaadatokat]: ./sql-data-warehouse-get-started-manually-load-samples.md
[Azure-portálra]: https://portal.azure.com/
[Power BI]: http://www.powerbi.com/
[kapcsolódás az SQL Data Warehouse-hoz]: ./sql-data-warehouse-integrate-power-bi.md
[SQL Data Warehouse-adatraktár létrehozása]: ./sql-data-warehouse-get-started-provision.md



<!--HONumber=Jun16_HO2-->


