---
title: Az adatmegjelenítés Power BI Microsoft Azure
description: SQL-adatraktár adatainak megjelenítése Power BI használatával
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: integration
ms.date: 04/17/2018
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: eea4e3b0b1f0e4ec3eaf3e0aba8952f6693d2921
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73685793"
---
# <a name="visualize-data-with-power-bi"></a>Adatok ábrázolása a Power BI használatával
Az alábbi oktatóanyagból megtudhatja, hogyan kapcsolódhat a Power BI-ból az SQL Data Warehouse-hoz. Az oktatóanyag emellett néhány alapvető adatábrázolás létrehozásának lépéseit is bemutatja.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Data-Warehouse-Sample-Data-and-PowerBI/player]
> 
> 

## <a name="prerequisites"></a>Előfeltételek
Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

* Egy SQL Data Warehouse, előre feltöltve az AdventureWorksDW adatbázisával. Az adattárházak kiépítéséhez lásd: [SQL Data Warehouse létrehozása](create-data-warehouse-portal.md) és a mintaadatok betöltésének kiválasztása. Ha már rendelkezik adatraktárral, de nem rendelkezik mintaadatok használatával, [betöltheti az wideworldimportersdw adattárházat](load-data-wideworldimportersdw.md).

## <a name="1-connect-to-your-database"></a>1. kapcsolódás az adatbázishoz
A Power BI megnyitása és csatlakozás az AdventureWorksDW adatbázishoz:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Kattintson az **SQL-adatbázisok** lehetőségre, és válassza ki az AdventureWorks SQL Data Warehouse-adatbázist.
   
    ![Adatbázis keresése](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-find-database.png)
3. Kattintson a „Megnyitás Power BI-ban” gombra.
   
    ![Power BI gomb](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-button.png)
4. Az SQL Data Warehouse csatlakozási oldalán most meg kell jelennie az adatbázis webcímének. Kattintson a Tovább gombra.
   
    ![Power BI-csatlakozás](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-connect-to-azure.png)
5. Adja meg az Azure SQL Server-felhasználónevét és-jelszavát.
   
    ![Bejelentkezés Power BI](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-sign-in.png)
6. Az adatbázis megnyitásához kattintson a bal oldali panelen található AdventureWorksDW adatkészletre.
   
    ![Power BI, AdventureWorksDW megnyitása](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-open-adventureworks.png)

## <a name="2-create-a-report"></a>2. jelentés létrehozása
Most már készen áll az AdventureWorksDW-mintaadatok elemzésére a Power BI használatával. Az elemzés végrehajtásához az AdventureWorksDW AggregateSales nevű nézete használható. Ebben a nézetben megtalálhatóak a vállalati értékesítés elemzéséhez szükséges fő mutatók.

1. Az értékesítési összegeket irányítószám szerint megjelenítő térkép létrehozásához kattintson a jobb oldali panelen az AggregateSales nézetre annak kibontásához. Kattintson a PostalCode és a SalesAmount oszlopra azok kiválasztásához.
   
    ![Power BI kiválasztja a AggregateSales](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-aggregatesales.png)
   
    A Power BI automatikusan felismeri a földrajzi és a térképen.
   
    ![Power BI, térkép](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-map.png)

2. Ez a lépés egy oszlopdiagramot hoz létre, amely az értékesítési összegeket az ügyfélbevételek szerint jeleníti meg. A oszlopdiagram létrehozásához nyissa meg a kibontott AggregateSales nézetet. Kattintson a SalesAmount mezőre. Húzza a CustomerIncome mezőt balra, és helyezze a Tengely alá.
   
    ![Power BI tengely kiválasztása](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-chooseaxis.png)
   
    A oszlopdiagram a bal oldalon.
   
    ![Power BI, oszlop](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-bar.png)
3. Ez a lépés egy vonaldiagramot hoz létre, amely az értékesítési összegeket a megrendelési dátumok szerint mutatja. A diagram létrehozásához nyissa meg a kibontott AggregateSales nézetet. Kattintson a SalesAmount és az OrderDate oszlopra. A vizualizációk oszlopban kattintson a vonal diagram ikonjára, amely az első ikon a második sorban a vizualizációk alatt.
   
    ![Power BI diagram kiválasztása](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-prepare-line.png)
   
    Mostanra rendelkezésére áll egy jelentés, amely az adatok három különböző megjelenítését tartalmazza.
   
    ![Power BI, vonal](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-line.png)

A folyamatot bármikor mentheti a **Fájl** gombra kattintva, majd a **Mentés** lehetőséget választva.

## <a name="using-direct-connect"></a>Közvetlen kapcsolat használata
A Azure SQL Databasehoz hasonlóan SQL Data Warehouse közvetlen kapcsolat is lehetővé teszi a logikai pushdown a Power BI analitikai képességei mellett. A közvetlen kapcsolódást követően a rendszer valós időben visszaküldi a lekérdezéseket a Azure SQL Data Warehousera az adatelemzés során.  Ez a funkció a SQL Data Warehouse méretezésével együtt lehetővé teszi, hogy percek alatt hozzon létre dinamikus jelentéseket. Emellett a Megnyitás a Power BIban gomb lehetővé teszi, hogy a felhasználók közvetlenül Power BI kapcsolódjanak a SQL Data Warehousehoz anélkül, hogy az Azure más részeiről kellene adatokat gyűjteniük.

Közvetlen csatlakozás használata esetén:

* A csatlakozáskor adja meg a kiszolgáló teljes nevét.
* Győződjön meg arról, hogy az adatbázishoz tartozó tűzfalszabályok úgy vannak konfigurálva, hogy engedélyezzék az Azure-szolgáltatásokhoz való hozzáférést.
* Minden művelet, például egy oszlop kijelölése vagy egy szűrő hozzáadása, közvetlenül az adattárházat kérdezi le.
* A csempék automatikusan és körülbelül 15 percenként frissülnek.
* A Q & A nem érhető el a közvetlen csatlakoztatási adatkészletekhez.
* A séma módosításai automatikusan beépülnek.
* Az összes közvetlen csatlakozási lekérdezés 2 perc elteltével időtúllépést eredményez.

Ezek a korlátozások és megjegyzések a tapasztalatok javításával változhatnak.

## <a name="next-steps"></a>További lépések
Most, hogy kaptunk egy kis időt a mintaadatok betöltésére, tekintse meg a [fejlesztéssel](sql-data-warehouse-overview-develop.md) és [betöltéssel](design-elt-data-loading.md)foglalkozó témakört. vagy körülnézhet a [Power BI webhelyén](https://www.powerbi.com/).
