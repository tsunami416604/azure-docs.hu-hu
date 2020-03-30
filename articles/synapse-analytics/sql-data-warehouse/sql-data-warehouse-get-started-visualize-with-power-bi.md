---
title: Adatok megjelenítése a Microsoft Azure Power BI-val
description: SQL-adatraktár adatainak megjelenítése Power BI használatával
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/17/2018
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 7e764eeab6f681d90e1a602f02cdb03330d6fd3d
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350951"
---
# <a name="visualize-data-with-power-bi"></a>Adatok ábrázolása a Power BI használatával
Az alábbi oktatóanyagból megtudhatja, hogyan kapcsolódhat a Power BI-ból az SQL Data Warehouse-hoz. Az oktatóanyag emellett néhány alapvető adatábrázolás létrehozásának lépéseit is bemutatja.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Data-Warehouse-Sample-Data-and-PowerBI/player]
> 
> 

## <a name="prerequisites"></a>Előfeltételek
Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

* Egy SQL Data Warehouse, előre feltöltve az AdventureWorksDW adatbázisával. Az adattárház kiépítéséhez olvassa el az [SQL Data Warehouse létrehozása](create-data-warehouse-portal.md) című témakört, és válassza ki a mintaadatok betöltését. Ha már rendelkezik adattárházával, de nincsenek mintaadatai, [betöltheti a WideWorldImportersDW -et.](load-data-wideworldimportersdw.md)

## <a name="1-connect-to-your-database"></a>1. Csatlakozás az adatbázishoz
A Power BI megnyitása és csatlakozás az AdventureWorksDW adatbázishoz:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Kattintson az **SQL adatbázisok** elemre, és válassza ki az AdventureWorks nevű SQL Data Warehouse-adatbázist.
   
    ![Adatbázis keresése](./media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-find-database.png)
3. Kattintson a „Megnyitás Power BI-ban” gombra.
   
    ![Power BI gomb](./media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-button.png)
4. Az SQL Data Warehouse csatlakozási oldalán most meg kell jelennie az adatbázis webcímének. Kattintson a Tovább gombra.
   
    ![Power BI-csatlakozás](./media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-connect-to-azure.png)
5. Adja meg az Azure SQL-kiszolgáló felhasználónevét és jelszavát.
   
    ![Power BI-bejelentkezés](./media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-sign-in.png)
6. Az adatbázis megnyitásához kattintson az AdventureWorksDW adatkészletre a bal oldali panelen.
   
    ![Power BI, AdventureWorksDW megnyitása](./media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-open-adventureworks.png)

## <a name="2-create-a-report"></a>2. Jelentés létrehozása
Most már készen áll az AdventureWorksDW-mintaadatok elemzésére a Power BI használatával. Az elemzés végrehajtásához az AdventureWorksDW AggregateSales nevű nézete használható. Ebben a nézetben megtalálhatóak a vállalati értékesítés elemzéséhez szükséges fő mutatók.

1. Az értékesítési összegeket irányítószám szerint megjelenítő térkép létrehozásához kattintson a jobb oldali panelen az AggregateSales nézetre annak kibontásához. Kattintson a PostalCode és a SalesAmount oszlopra azok kiválasztásához.
   
    ![A Power BI kiválasztja az AggregateSales-t](./media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-aggregatesales.png)
   
    A Power BI automatikusan felismerte a földrajzi adatokat, és egy térképen helyezte el azadatokat.
   
    ![Power BI, térkép](./media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-map.png)

2. Ez a lépés egy oszlopdiagramot hoz létre, amely az értékesítési összegeket az ügyfélbevételek szerint jeleníti meg. Az oszlopdiagram létrehozásához nyissa meg a kibontott AggregateSales nézetet. Kattintson a SalesAmount mezőre. Húzza a CustomerIncome mezőt balra, és helyezze a Tengely alá.
   
    ![A Power BI kijelöli a tengelyt](./media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-chooseaxis.png)
   
    A sávdiagram a bal oldalon.
   
    ![Power BI, oszlop](./media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-bar.png)
3. Ez a lépés egy vonaldiagramot hoz létre, amely az értékesítési összegeket a megrendelési dátumok szerint mutatja. A vonaldiagram létrehozásához lépjen a kibontott AggregateSales nézetre. Kattintson a SalesAmount és az OrderDate oszlopra. A Vizualizációk oszlopban kattintson a Vonaldiagram ikonra, amely a vizualizációk alatti második sor első ikonja.
   
    ![A Power BI vonaldiagramot jelöl ki](./media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-prepare-line.png)
   
    Mostanra rendelkezésére áll egy jelentés, amely az adatok három különböző megjelenítését tartalmazza.
   
    ![Power BI, vonal](./media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-line.png)

A folyamatot bármikor mentheti a **Fájl** gombra kattintva, majd a **Mentés** lehetőséget választva.

## <a name="using-direct-connect"></a>A Direct Connect használata
Az Azure SQL Database-hez is az SQL Data Warehouse Direct Connect a Power BI elemzési képességei mellett lehetővé teszi a logikai leküldéses leküldéset. A Direct Connect használatával a lekérdezések valós időben kerülnek vissza az Azure SQL Data Warehouse-ba az adatok feltárása során.  Ez a funkció az SQL Data Warehouse léptékével kombinálva lehetővé teszi dinamikus jelentések készítését percek alatt terabájtnyi adatok kal szemben. Emellett az Open in Power BI gomb bevezetése lehetővé teszi a felhasználók számára, hogy közvetlenül összekapcsolják a Power BI-t az SQL Data Warehouse-val anélkül, hogy az Azure más részeiről gyűjtenek adatokat.

A Direct Connect használataesetén:

* Csatlakozáskor adja meg a teljesen minősített kiszolgáló nevét.
* Győződjön meg arról, hogy az adatbázis tűzfalszabályai az Azure-szolgáltatásokhoz való hozzáférés engedélyezésére vannak konfigurálva.
* Minden művelet, például egy oszlop kijelölése vagy egy szűrő hozzáadása, közvetlenül lekérdezi az adatraktárt.
* A csempék frissítése automatikusan és körülbelül 15 percenként történik.
* A Q&A nem érhető el a Direct Connect adatkészletek esetében.
* A sémamódosítások automatikusan beépülnek.
* Az összes Direct Connect-lekérdezés 2 perc elteltével idővel megszűnik.

Ezek a korlátozások és megjegyzések a tapasztalatok javulásának javulása után változhatnak.

## <a name="next-steps"></a>További lépések
Most, hogy adtunk egy kis időt, hogy felmelegedjen a minta adatok, hogyan [kell fejleszteni](sql-data-warehouse-overview-develop.md) vagy [betölteni](design-elt-data-loading.md). vagy körülnézhet a [Power BI webhelyén](https://www.powerbi.com/).
