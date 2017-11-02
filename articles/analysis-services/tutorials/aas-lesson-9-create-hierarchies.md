---
title: "Azure Analysis Services oktatóanyag – 9. lecke: Hierarchiák létrehozása | Microsoft Docs"
description: 
services: analysis-services
documentationcenter: 
author: Minewiskan
manager: kfile
editor: 
tags: 
ms.assetid: 
ms.service: analysis-services
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 11/01/2017
ms.author: owend
ms.openlocfilehash: 28e4e24f5706e88ede25060d5459617befd4aea9
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/02/2017
---
# <a name="lesson-9-create-hierarchies"></a>9. lecke: Hierarchiák létrehozása

[!INCLUDE[analysis-services-appliesto-aas-sql2017-later](../../../includes/analysis-services-appliesto-aas-sql2017-later.md)]

Ebben a leckében hierarchiákat hozhat létre. A hierarchiák szintekbe rendezett oszlopcsoportok. A földrajzi hierarchia például tartalmazhatja az Ország, Állam, Megye és Város alszinteket. A hierarchiák a többi oszloptól külön is megjelenhetnek a jelentéskészítési ügyfélalkalmazás mezőlistájában, így az ügyfélfelhasználók könnyebben navigálhatnak közöttük és foglalhatják őket jelentésekbe. További tudnivalók: [Hierarchiák](https://docs.microsoft.com/sql/analysis-services/tabular-models/hierarchies-ssas-tabular)
  
Hierarchiák létrehozásához használja a modelltervezőt *Diagramnézetben*. A hierarchiák létrehozása és kezelése Adatnézetben nem támogatott.  
  
A lecke elvégzésének várható időtartama: **20 perc**.  
  
## <a name="prerequisites"></a>Előfeltételek  
Ez a témakör a táblázatos modellezéssel foglalkozó oktatóanyag részét képezi, amelyet a megfelelő sorrendben kell elvégezni. Az ebben a leckében található feladatok végrehajtása előtt be kell fejeznie az előző leckét: [8. lecke: Perspektívák létrehozása](../tutorials/aas-lesson-8-create-perspectives.md).  
  
## <a name="create-hierarchies"></a>Hierarchiák létrehozása  
  
#### <a name="to-create-a-category-hierarchy-in-the-dimproduct-table"></a>Category (Kategória) hierarchia létrehozása a DimProduct táblában  
  
1.  A modelltervezőben (diagramnézetben) kattintson a jobb gombbal a **DimProduct** táblára, majd kattintson a **Hierarchia létrehozása** elemre. Az új hierarchia a tábla ablakának alján jelenik majd meg. Nevezze át a hierarchiát a következőre: **Category**.  
  
2.  Kattintson a **ProductCategoryName** oszlopra, és húzza az új **Category** hierarchiába.  
  
3.  A **Category** hierarchiában kattintson a jobb gombbal a **ProductCategoryName** > **Átnevezés** elemre, majd írja be a **Category** kifejezést.  
  
    > [!NOTE]  
    > Ha átnevez egy oszlopot a hierarchiában, az oszlop neve nem változik a táblában. A hierarchiában található oszlopok csak a táblában lévő oszlopok ábrázolásai.  
  
4.  Kattintson a **ProductSubcategoryName** oszlopra, és húzza a **Category** hierarchiába. Nevezze át a következőre: **Subcategory**. 
  
5.  Kattintson a jobb gombbal a **ModelName** oszlopra, majd kattintson a **Hozzáadás a hierarchiához** elemre, és válassza a **Category** lehetőséget. Nevezze át a következőre: **Model**.

6.  Végül adja hozzá az **EnglishProductName** oszlopot a Category hierarchiához. Nevezze át a következőre: **Product**.  

    ![aas-lesson9-category](../tutorials/media/aas-lesson9-category.png)
  
#### <a name="to-create-hierarchies-in-the-dimdate-table"></a>Hierarchiák létrehozása a DimDate táblában  
  
1.  A **DimDate** táblában hozzon létre egy **Calendar** nevű hierarchiát.  
  
3.  Adja hozzá az alábbi oszlopokat a megadott sorrendben:

    *  CalendarYear
    *  CalendarSemester
    *  CalendarQuarter
    *  MonthCalendar
    *  DayNumberOfMonth
    
4.  A **DimDate** táblában hozzon létre egy **Fiscal** nevű hierarchiát. Adja meg az alábbi oszlopokat a megadott sorrendben:  
  
    *  FiscalYear
    *  FiscalSemester
    *  FiscalQuarter
    *  MonthCalendar
    *  DayNumberOfMonth
  
5.  A **DimDate** táblában hozzon létre egy **ProductionCalendar** nevű hierarchiát. Adja meg az alábbi oszlopokat a megadott sorrendben:  
    *  CalendarYear
    *  WeekNumberOfYear
    *  DayNumberOfWeek
  
 ## <a name="whats-next"></a>A következő lépések
[10. lecke: Partíciók létrehozása](../tutorials/aas-lesson-10-create-partitions.md). 
  
  
