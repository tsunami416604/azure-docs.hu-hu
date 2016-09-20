<properties
   pageTitle="SQL-adatraktár adatainak megjelenítése Power BI használatával |Microsoft Azure"
   description="SQL-adatraktár adatainak megjelenítése Power BI használatával"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor="" />

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/16/2016"
   ms.author="lodipalm;barbkess;sonyama" />

# Adatok megjelenítése Power BI használatával

> [AZURE.SELECTOR]
- [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
- [Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
- [Visual Studio](sql-data-warehouse-query-visual-studio.md)
- [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md) 

Ez az oktatóanyag az SQL-adatraktárhoz Power BI-on keresztül történő kapcsolódást

> [AZURE.VIDEO azure-sql-data-warehouse-sample-data-and-powerbi]

## Előfeltételek

Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

- Egy SQL Data Warehouse, előre feltöltve az AdventureWorksDW adatbázisával. Ennek létrehozásához olvassa el az [SQL Data Warehouse létrehozása][] című cikket, és válassza a mintaadatok betöltését. Ha már rendelkezik egy adatraktárral, de nincsenek mintaadatai, [a mintaadatokat manuálisan is betöltheti][].


## 1. Csatlakozás az adatbázishoz

A Power BI megnyitása és csatlakozás az AdventureWorksDW adatbázishoz:

1. Jelentkezzen be az [Azure Portal][].
2. Kattintson az **SQL adatbázisok** elemre, és válassza ki az AdventureWorks nevű SQL Data Warehouse-adatbázist.

    ![Adatbázis keresése][1]

3. Kattintson a „Megnyitás Power BI-ban” gombra.

    ![Power BI gomb][2]

4. Az SQL Data Warehouse csatlakozási oldalán most meg kell jelennie az adatbázis webcímének. Kattintson a Tovább gombra.

    ![Power BI-csatlakozás][3]

6. Adja meg az Azure SQL-kiszolgálón használt felhasználónevét és jelszavát, és teljesen csatlakozni fog az SQL Data Warehouse-adatbázishoz.

    ![Power BI-bejelentkezés][4]

7. Ha bejelentkezett a Power BI-ba, kattintson az AdventureWorksDW adatkészletre a bal oldali panelen. Ekkor megnyílik az adatbázis.

    ![Power BI, AdventureWorksDW megnyitása][5]



## 2. Jelentés létrehozása

Most már készen áll az AdventureWorksDW-mintaadatok elemzésére a Power BI használatával. Az elemzés végrehajtásához az AdventureWorksDW AggregateSales nevű nézete használható. Ebben a nézetben megtalálhatóak a vállalati értékesítés elemzéséhez szükséges fő mutatók.

1. Az értékesítési összegeket irányítószám szerint megjelenítő térkép létrehozásához kattintson a jobb oldali panelen az AggregateSales nézetre annak kibontásához. Kattintson a PostalCode és a SalesAmount oszlopra azok kiválasztásához.

    ![Power BI, AggregateSales kijelölése][6]

    A Power BI automatikusan felismeri, hogy ezek földrajzi adatok, és egy térképen jeleníti meg azokat.

    ![Power BI, térkép][7]

2. Ez a lépés egy oszlopdiagramot hoz létre, amely az értékesítési összegeket az ügyfélbevételek szerint jeleníti meg. Ennek létrehozásához lépjen a kibontott AggregateSales nézetre. Kattintson a SalesAmount mezőre. Húzza a CustomerIncome mezőt balra, és helyezze a Tengely alá.

    ![Power BI, tengely kijelölése][8]

    Az oszlopdiagramot a bal oldalra helyeztük.

    ![Power BI, oszlop][9]

3. Ez a lépés egy vonaldiagramot hoz létre, amely az értékesítési összegeket a megrendelési dátumok szerint mutatja. Ennek létrehozásához lépjen a kibontott AggregateSales nézetre. Kattintson a SalesAmount és az OrderDate oszlopra. A Megjelenítések oszlopban kattintson a vonaldiagram ikonjára (ez az első ikon a második sorban a Megjelenítések alatt).

    ![Power BI, vonaldiagram kiválasztása][10]

    Mostanra rendelkezésére áll egy jelentés, amely az adatok három különböző megjelenítését tartalmazza.

    ![Power BI, vonal][11]

A folyamatot bármikor mentheti a **Fájl** gombra kattintva, majd a **Mentés** lehetőséget választva.

## Következő lépések
Most, hogy ízelítőt kapott a mintaadatok kezeléséből, megismerkedhet a [fejlesztés][], a [betöltés][] és az [áttelepítés][] folyamatával, vagy körülnézhet a [Power BI webhelyén][].

<!--Image references-->
[1]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-find-database.png
[2]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-button.png
[3]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-connect-to-azure.png
[4]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-sign-in.png
[5]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-open-adventureworks.png
[6]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-aggregatesales.png
[7]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-map.png
[8]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-chooseaxis.png
[9]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-bar.png
[10]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-prepare-line.png
[11]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-line.png
[12]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-save.png

<!--Article references-->
[áttelepítés]: sql-data-warehouse-overview-migrate.md
[fejlesztés]: sql-data-warehouse-overview-develop.md
[betöltés]: sql-data-warehouse-overview-load.md
[a mintaadatokat manuálisan is betöltheti]: sql-data-warehouse-load-sample-databases.md
[kapcsolódás az SQL-adatraktárhoz]: sql-data-warehouse-integrate-power-bi.md
[SQL Data Warehouse létrehozása]: sql-data-warehouse-get-started-provision.md

<!--Other-->
[Azure Portal]: https://portal.azure.com/
[Power BI webhelyén]: http://www.powerbi.com/



<!--HONumber=sep16_HO1-->


