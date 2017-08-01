---
title: "Azure Analysis Services oktatóanyag – kiegészítő lecke: Hézagos hierarchiák | Microsoft Docs"
description: "Ismerteti a hézagos hierarchiák javításának módját az Azure Analysis Services oktatóanyagában."
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
tags: 
ms.assetid: 
ms.service: analysis-services
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 05/26/2017
ms.author: owend
ms.translationtype: Human Translation
ms.sourcegitcommit: 43aab8d52e854636f7ea2ff3aae50d7827735cc7
ms.openlocfilehash: 0f02ff73eb126cc397312e87bde50b3ee2d6ce53
ms.contentlocale: hu-hu
ms.lasthandoff: 06/03/2017

---
# <a name="supplemental-lesson---ragged-hierarchies"></a>Kiegészítő lecke – Hézagos hierarchiák

[!INCLUDE[analysis-services-appliesto-aas-sql2017-later](../../../includes/analysis-services-appliesto-aas-sql2017-later.md)]

Ebben a kiegészítő leckében a különböző szinteken üres értékéket (tagokat) tartalmazó hierarchiák kimutatásokba való felvételének gyakori problémáját oldhatja meg. Például egy szervezetnél, ahol egy magas szintű vezető közvetlen beosztottjai közé részlegvezetők és nem vezető beosztású alkalmazottak is tartoznak. Vagy azok az ország-régió-város földrajzi hierarchiák, ahol egyes városoknak nincs szülőállama vagy megyéje, mint például Washington D.C. vagy Vatikánváros esetében. Ha egy hierarchia üres tagokkal rendelkezik, gyakran különböző vagy hézagos szinteket eredményez.

![aas-lesson-detail-ragged-hierarchies-table](../tutorials/media/aas-lesson-detail-ragged-hierarchies-table.png)

A táblázatos modellek az 1400-as kompatibilitási szinten egy további **Tagok elrejtése** tulajdonsággal rendelkeznek a hierarchiákhoz. Az **Alapértelmezett** beállítás feltételezi, hogy egyik szinten sincsenek üres tagok. Az **Üres tagok elrejtése** beállítás kizárja az üres tagokat a hierarchiából a kimutatáshoz vagy jelentéshez való hozzáadáskor.  
  
A lecke elvégzésének várható időtartama: **20 perc**.  
  
## <a name="prerequisites"></a>Előfeltételek  
Ez a kiegészítőlecke-témakör a táblázatos modellezésről szóló oktatóanyag része. Az ebben a kiegészítő leckében található feladatok végrehajtása előtt be kell fejeznie minden előző leckét, vagy rendelkeznie kell egy befejezett Adventure Works internetes értékesítési minta modellprojekttel. 

Ha az oktatóanyag részeként hozta létre az AW internetes értékesítési projektet, a modell még nem tartalmaz hézagos adatokat vagy hierarchiákat. A kiegészítő lecke teljesítéséhez először létre kell hoznia a problémát néhány további táblázat hozzáadásával, kapcsolatok, számított oszlopok, egy mérték és egy új szervezeti hierarchia létrehozásával. Ez a rész nagyjából 15 percet vesz igénybe. Ezután néhány perc alatt megoldhatja a problémát.  

## <a name="add-tables-and-objects"></a>Táblázatok és objektumok hozzáadása
  
### <a name="to-add-new-tables-to-your-model"></a>Új táblázatok hozzáadása a modellhez
  
1.  A Tabular Model Explorerben bontsa ki az **Adatforrások** szakaszt, és kattintson a jobb gombbal a kapcsolatra, majd pedig az **Új táblázatok importálása** gombra.
  
2.  A Kezelőben válassza a **DimEmployee** és a **FactResellerSales** lehetőséget, majd kattintson az **OK** gombra.

3.  A Lekérdezésszerkesztőben kattintson az **Importálás** lehetőségre

4.  Hozza létre a következő [kapcsolatokat](../tutorials/aas-lesson-4-create-relationships.md):

    | 1. táblázat           | Oszlop       | Szűrés iránya   | 2. táblázat     | Oszlop      | Aktív |
    |-------------------|--------------|--------------------|-------------|-------------|--------|
    | FactResellerSales | OrderDateKey | Alapértelmezett            | DimDate     | Dátum        | Igen    |
    | FactResellerSales | DueDate      | Alapértelmezett            | DimDate     | Dátum        | Nem     |
    | FactResellerSales | ShipDateKey  | Alapértelmezett            | DimDate     | Dátum        | Nem     |
    | FactResellerSales | ProductKey   | Alapértelmezett            | DimProduct  | ProductKey  | Igen    |
    | FactResellerSales | EmployeeKey  | Mindkét táblázathoz | DimEmployee | EmployeeKey | Igen    |

5. Hozza létre a következő [Számított oszlopokat](../tutorials/aas-lesson-5-create-calculated-columns.md) a **DimEmployee** táblában: 

    **Elérési út** 
    ```
    =PATH([EmployeeKey],[ParentEmployeeKey])
    ```

    **FullName** 
    ```
    =[FirstName] & " " & [MiddleName] & " " & [LastName]
    ```

    **Level1** 
    ```
    =LOOKUPVALUE(DimEmployee[FullName],DimEmployee[EmployeeKey],PATHITEM([Path],1,1)) 
    ```

    **Level2** 
    ```
    =LOOKUPVALUE(DimEmployee[FullName],DimEmployee[EmployeeKey],PATHITEM([Path],1,2)) 
    ```

    **Level3** 
    ```
    =LOOKUPVALUE(DimEmployee[FullName],DimEmployee[EmployeeKey],PATHITEM([Path],1,3)) 
    ```

    **Level4** 
    ```
    =LOOKUPVALUE(DimEmployee[FullName],DimEmployee[EmployeeKey],PATHITEM([Path],1,4)) 
    ```

    **Level5** 
    ```
    =LOOKUPVALUE(DimEmployee[FullName],DimEmployee[EmployeeKey],PATHITEM([Path],1,5)) 
    ```

6.  A **DimEmployee** táblában hozzon létre egy **Szervezet** nevű [hierarchiát](../tutorials/aas-lesson-9-create-hierarchies.md). Adja meg a következő oszlopokat a megadott sorrendben: **Level1**, **Level2**, **Level3**, **Level4**, **Level5**.

7.  Hozza létre a következő [mértékeket](../tutorials/aas-lesson-6-create-measures.md) a **FactResellerSales** táblában:

    ```
    ResellerTotalSales:=SUM([SalesAmount])
    ```

8.  Használja az [Elemzés az Excelben](../tutorials/aas-lesson-12-analyze-in-excel.md) lehetőséget az Excel megnyitásához és egy kimutatás automatikus létrehozásához.

9.  A **Kimutatás mezők** részben adja hozzá a **Szervezet** hierarchiát a **DimEmployee** táblából a **Sorokhoz**, valamint a **ResellerTotalSales** mértéket a **FactResellerSales** táblából az **Értékekhez**.

    ![aas-lesson-detail-ragged-hierarchies-pivottable](../tutorials/media/aas-lesson-detail-ragged-hierarchies-pivottable.png)

    Ahogy a kimutatásban látható, a hierarchia megjeleníti a hézagos sorokat. Több sor is látható, amely üres tagokat tartalmaz.

## <a name="to-fix-the-ragged-hierarchy-by-setting-the-hide-members-property"></a>A hézagos hierarchia javítása a Tagok elrejtése tulajdonság beállításával

1.  A **Táblázatos modelltallózóban** bontsa ki a következőt: **Táblák** > **DimEmployee** > **Hierarchiák** > **Szervezet**.

2.  A **Tulajdonságok** > **Tagok elrejtése** részen válassza az **Üres tagok elrejtése** lehetőséget. 

    ![aas-lesson-detail-ragged-hierarchies-hidemembers](../tutorials/media/aas-lesson-detail-ragged-hierarchies-hidemembers.png)

3.  Frissítse a kimutatást az Excelben. 

    ![aas-lesson-detail-ragged-hierarchies-pivottable-refresh](../tutorials/media/aas-lesson-detail-ragged-hierarchies-pivottable-refresh.png)

    Így már sokkal jobban néz ki!

## <a name="see-also"></a>Lásd még:   
[9. lecke: Hierarchiák létrehozása](../tutorials/aas-lesson-9-create-hierarchies.md)  
[Kiegészítő lecke – Dinamikus biztonság](../tutorials/aas-supplemental-lesson-dynamic-security.md)  
[Kiegészítő lecke – Részletsorok](../tutorials/aas-supplemental-lesson-detail-rows.md)  
