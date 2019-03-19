---
title: SQL-adatraktár adatainak megjelenítése Power BI használatával |Microsoft Azure
description: SQL-adatraktár adatainak megjelenítése Power BI használatával
services: sql-data-warehouse
author: KavithaJonnakuti
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: consume
ms.date: 04/17/2018
ms.author: kavithaj
ms.reviewer: igorstan
ms.openlocfilehash: b2377466ba830eed9fed0e9a4033b3eca3d351c4
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57834604"
---
# <a name="visualize-data-with-power-bi"></a>Adatok ábrázolása a Power BI használatával
Ez az oktatóanyag az SQL-adatraktárhoz Power BI-on keresztül történő kapcsolódást

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Data-Warehouse-Sample-Data-and-PowerBI/player]
> 
> 

## <a name="prerequisites"></a>Előfeltételek
Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

* Egy SQL Data Warehouse, előre feltöltve az AdventureWorksDW adatbázisával. Kiépíthet egy adattárházat, lásd: [hozzon létre egy SQL Data Warehouse](create-data-warehouse-portal.md) , és válassza a mintaadatok betöltését. Ha már rendelkezik egy adatraktárral, de nincsenek benne mintaadatok, akkor az [tölthető be a WideWorldImportersDW](load-data-wideworldimportersdw.md).

## <a name="1-connect-to-your-database"></a>1. Csatlakozás az adatbázishoz
A Power BI megnyitása és csatlakozás az AdventureWorksDW adatbázishoz:

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com/).
2. Kattintson az **SQL adatbázisok** elemre, és válassza ki az AdventureWorks nevű SQL Data Warehouse-adatbázist.
   
    ![Adatbázis keresése](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-find-database.png)
3. Kattintson a „Megnyitás Power BI-ban” gombra.
   
    ![Power BI gomb](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-button.png)
4. Az SQL Data Warehouse csatlakozási oldalán most meg kell jelennie az adatbázis webcímének. Kattintson a Tovább gombra.
   
    ![Power BI-csatlakozás](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-connect-to-azure.png)
5. Adja meg az Azure SQL server-felhasználónevével és jelszavával.
   
    ![A Power BI-bejelentkezés](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-sign-in.png)
6. Nyissa meg az adatbázist, kattintson az AdventureWorksDW adatkészletre a bal oldali panelen.
   
    ![Power BI, AdventureWorksDW megnyitása](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-open-adventureworks.png)

## <a name="2-create-a-report"></a>2. Jelentés létrehozása
Most már készen áll az AdventureWorksDW-mintaadatok elemzésére a Power BI használatával. Az elemzés végrehajtásához az AdventureWorksDW AggregateSales nevű nézete használható. Ebben a nézetben megtalálhatóak a vállalati értékesítés elemzéséhez szükséges fő mutatók.

1. Az értékesítési összegeket irányítószám szerint megjelenítő térkép létrehozásához kattintson a jobb oldali panelen az AggregateSales nézetre annak kibontásához. Kattintson a PostalCode és a SalesAmount oszlopra azok kiválasztásához.
   
    ![A Power BI AggregateSales kijelölése](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-aggregatesales.png)
   
    A Power BI automatikusan felismeri a földrajzi adatok, és helyezze egy térkép.
   
    ![Power BI, térkép](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-map.png)

2. Ez a lépés egy oszlopdiagramot hoz létre, amely az értékesítési összegeket az ügyfélbevételek szerint jeleníti meg. Az oszlopdiagram létrehozásához nyissa meg a kibontott AggregateSales nézetre. Kattintson a SalesAmount mezőre. Húzza a CustomerIncome mezőt balra, és helyezze a Tengely alá.
   
    ![A Power BI tengely kijelölése](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-chooseaxis.png)
   
    Az oszlopdiagramot a bal oldalra.
   
    ![Power BI, oszlop](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-bar.png)
3. Ez a lépés egy vonaldiagramot hoz létre, amely az értékesítési összegeket a megrendelési dátumok szerint mutatja. A vonaldiagramot hoz létre, hogy nyissa meg a kibontott AggregateSales nézetre. Kattintson a SalesAmount és az OrderDate oszlopra. A megjelenítések oszlopban kattintson a vonaldiagram ikont, amely az első ikon a második sorban a megjelenítések alatt.
   
    ![A Power BI vonaldiagram kiválasztása](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-prepare-line.png)
   
    Mostanra rendelkezésére áll egy jelentés, amely az adatok három különböző megjelenítését tartalmazza.
   
    ![Power BI, vonal](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-line.png)

A folyamatot bármikor mentheti a **Fájl** gombra kattintva, majd a **Mentés** lehetőséget választva.

## <a name="using-direct-connect"></a>Használatával a közvetlen csatlakoztatása
Mint az Azure SQL Database, az SQL Data Warehouse közvetlen csatlakozás lehetővé teszi, hogy logikai legördülő lista mellett a Power BI elemzési képességeit. Közvetlen csatlakozás az küldi a lekérdezéseket vissza az Azure SQL Data Warehouse a valós idejű az adatok feltárásakor.  Ez a funkció az SQL Data Warehouse, a méretezési csoport kombinálva lehetővé teszi dinamikus jelentéseket hozhat létre percek alatt akár több terabájtnyi adat. Emellett a Megnyitás a Power BI gomb bevezetésének lehetővé teszi a közvetlenül csatlakozhat a Power BI az SQL Data Warehouse adatainak összegyűjtése az Azure egyéb részeitől nélkül.

Ha közvetlen kapcsolódás használatával:

* A kapcsolódáskor, adja meg a teljes kiszolgálónevet.
* Gondoskodjon róla, hogy az adatbázisra vonatkozó tűzfalszabályokban konfigurálva az Azure-szolgáltatásokhoz való hozzáférés engedélyezése.
* Minden művelet, például egy oszlop kiválasztása vagy egy szűrő felvétele közvetlenül lekérdezi az adatraktár.
* Csempék automatikusan, és körülbelül 15 percenként frissülnek.
* A Q & A a közvetlen kapcsolódás adatkészletek esetén nem érhető el.
* Sémaváltozások automatikusan a rendszer részét képezik.
* Közvetlen csatlakozás az összes lekérdezés 2 perc elteltével időtúllépést okoz.

Ezek a korlátozások és figyelmeztetések lehetséges, hogy módosítani, mert a élmény javítására.

## <a name="next-steps"></a>További lépések
Most, hogy ízelítőt kapott a mintaadatok kezeléséből, megismerkedhet a [fejlesztés](sql-data-warehouse-overview-develop.md), a [betöltés](design-elt-data-loading.md) és az [áttelepítés](sql-data-warehouse-overview-migrate.md) folyamatával, vagy körülnézhet a [Power BI webhelyén](https://www.powerbi.com/).
