---
title: SQL-adatraktár adatainak megjelenítése Power BI használatával |Microsoft Azure
description: SQL-adatraktár adatainak megjelenítése Power BI használatával
services: sql-data-warehouse
author: kavithaj
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: consume
ms.date: 04/17/2018
ms.author: kavithaj
ms.reviewer: igorstan
ms.openlocfilehash: 52581a87caac419a79caab647cc9c5a4ee7453ba
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2018
---
# <a name="visualize-data-with-power-bi"></a>Adatok ábrázolása a Power BI használatával
Ez az oktatóanyag az SQL-adatraktárhoz Power BI-on keresztül történő kapcsolódást

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Data-Warehouse-Sample-Data-and-PowerBI/player]
> 
> 

## <a name="prerequisites"></a>Előfeltételek
Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

* Egy SQL Data Warehouse, előre feltöltve az AdventureWorksDW adatbázisával. Adatraktár kiépítéséhez, lásd: [SQL Data Warehouse létrehozása](create-data-warehouse-portal.md) , és válassza a mintaadatok betöltése. Ha már rendelkezik adatraktárral, de nem rendelkeznek a mintaadatok, akkor [WideWorldImportersDW betöltése](load-data-wideworldimportersdw.md).

## <a name="1-connect-to-your-database"></a>1. Csatlakozás az adatbázishoz
A Power BI megnyitása és csatlakozás az AdventureWorksDW adatbázishoz:

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com/).
2. Kattintson az **SQL adatbázisok** elemre, és válassza ki az AdventureWorks nevű SQL Data Warehouse-adatbázist.
   
    ![Adatbázis keresése](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-find-database.png)
3. Kattintson a „Megnyitás Power BI-ban” gombra.
   
    ![Power BI gomb](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-button.png)
4. Az SQL Data Warehouse csatlakozási oldalán most meg kell jelennie az adatbázis webcímének. Kattintson a Tovább gombra.
   
    ![Power BI-csatlakozás](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-connect-to-azure.png)
5. Adja meg az Azure SQL server felhasználónevet és jelszót.
   
    ![A Power BI-bejelentkezés](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-sign-in.png)
6. Az adatbázis megnyitásához kattintson az AdventureWorksDW adatkészletre a bal oldali panelen.
   
    ![Power BI, AdventureWorksDW megnyitása](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-open-adventureworks.png)

## <a name="2-create-a-report"></a>2. Jelentés létrehozása
Most már készen áll az AdventureWorksDW-mintaadatok elemzésére a Power BI használatával. Az elemzés végrehajtásához az AdventureWorksDW AggregateSales nevű nézete használható. Ebben a nézetben megtalálhatóak a vállalati értékesítés elemzéséhez szükséges fő mutatók.

1. Az értékesítési összegeket irányítószám szerint megjelenítő térkép létrehozásához kattintson a jobb oldali panelen az AggregateSales nézetre annak kibontásához. Kattintson a PostalCode és a SalesAmount oszlopra azok kiválasztásához.
   
    ![A Power BI AggregateSales kijelölése](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-aggregatesales.png)
   
    A Power BI automatikusan ismeri fel a földrajzi adatot, és helyezze egy térképen jeleníti meg.
   
    ![Power BI, térkép](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-map.png)

2. Ez a lépés egy oszlopdiagramot hoz létre, amely az értékesítési összegeket az ügyfélbevételek szerint jeleníti meg. Az oszlopdiagramot létrehozásához lépjen a kibontott AggregateSales nézetre. Kattintson a SalesAmount mezőre. Húzza a CustomerIncome mezőt balra, és helyezze a Tengely alá.
   
    ![A Power BI tengely kijelölése](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-chooseaxis.png)
   
    Az oszlopdiagramot a bal oldalra.
   
    ![Power BI, oszlop](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-bar.png)
3. Ez a lépés egy vonaldiagramot hoz létre, amely az értékesítési összegeket a megrendelési dátumok szerint mutatja. A vonaldiagram létrehozásához lépjen a kibontott AggregateSales nézetre. Kattintson a SalesAmount és az OrderDate oszlopra. A képi megjelenítések oszlopban kattintson a vonaldiagram ikonjára, amely az első ikon a második sorban a megjelenítések alatt.
   
    ![A Power BI vonaldiagram kiválasztása](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-prepare-line.png)
   
    Mostanra rendelkezésére áll egy jelentés, amely az adatok három különböző megjelenítését tartalmazza.
   
    ![Power BI, vonal](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-line.png)

A folyamatot bármikor mentheti a **Fájl** gombra kattintva, majd a **Mentés** lehetőséget választva.

## <a name="using-direct-connnect"></a>Közvetlen kapcsolat használatával
Mint az Azure SQL Database, SQL Data Warehouse közvetlen kapcsolódás lehetővé teszi, hogy a Power bi analitikai funkciók mellett leküldéses logikai közzététele. Közvetlen csatlakozás esetén a lekérdezések rendszer küldi vissza az Azure SQL Data Warehouse a valós idejű, az adatokba.  Ezzel a szolgáltatással együtt az SQL Data warehouse, amely lehetővé teszi, hogy létrehozhat dinamikus jelentéseket elleni terabájtos adatkészleteket percben. A Megnyitás Power BI gomb bevezetése emellett lehetővé teszi a felhasználók kapcsolódhatnak közvetlenül a Power BI az SQL Data Warehouse Azure egyéb részeitől információk gyűjtése nélkül.

Ha közvetlen kapcsolódás használatával:

* Adja meg a teljes kiszolgálónév, kapcsolódás esetén.
* Ellenőrizze az adatbázisra vonatkozó tűzfalszabályok az Azure-szolgáltatásokhoz való hozzáférés engedélyezése.
* Jelöljön ki egy oszlopot, vagy hozzáad egy szűrőt, például minden műveletet közvetlenül lekérdezi az adatraktárba.
* Csempék frissítése automatikusan és körülbelül 15 percenként.
* A kérdések és válaszok a közvetlen kapcsolódás adatkészletek esetében nem érhető el.
* Sémaváltozások eszköz automatikusan.
* Minden közvetlen kapcsolódás lekérdezések időtúllépést okoz 2 perc múlva.

E korlátozások és a megjegyzések is módosítható, mert a élmény javítása.

## <a name="next-steps"></a>További lépések
Most, hogy ízelítőt kapott a mintaadatok kezeléséből, megismerkedhet a [fejlesztés](sql-data-warehouse-overview-develop.md), a [betöltés](design-elt-data-loading.md) és az [áttelepítés](sql-data-warehouse-overview-migrate.md) folyamatával, vagy körülnézhet a [Power BI webhelyén](http://www.powerbi.com/).
