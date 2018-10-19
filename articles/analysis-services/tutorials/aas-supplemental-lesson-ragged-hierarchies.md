---
title: 'Azure Analysis Services oktatóanyag – kiegészítő lecke: Hézagos hierarchiák | Microsoft Docs'
description: Ismerteti a hézagos hierarchiák javításának módját az Azure Analysis Services oktatóanyagában.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/18/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: f5bd679c11139b7ac439247df81a7adb0b02b0fe
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/19/2018
ms.locfileid: "49426331"
---
# <a name="supplemental-lesson---ragged-hierarchies"></a>Kiegészítő lecke – Hézagos hierarchiák

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
    =LOOKUPVALUE(DimEmployee[FullName],DimEmployee[EmployeeKey],PATHITEM([Path],2,1)) 
    ```

    **Level3** 
    ```
    =LOOKUPVALUE(DimEmployee[FullName],DimEmployee[EmployeeKey],PATHITEM([Path],3,1)) 
    ```

    **Level4** 
    ```
    =LOOKUPVALUE(DimEmployee[FullName],DimEmployee[EmployeeKey],PATHITEM([Path],4,1)) 
    ```

    **Level5** 
    ```
    =LOOKUPVALUE(DimEmployee[FullName],DimEmployee[EmployeeKey],PATHITEM([Path],5,1)) 
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
