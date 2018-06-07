---
title: 'Azure Analysis Services oktatóanyag – kiegészítő lecke: Részletsorok | Microsoft Docs'
description: Ismerteti a részletsor-kifejezés létrehozását az Azure Analysis Services oktatóanyagban.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 04/12/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 6e15788992e66fdb8e742b290e5bcf5bce8d20fb
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34596100"
---
# <a name="supplemental-lesson---detail-rows"></a>Kiegészítő lecke – Részletsorok

Ebben a kiegészítő leckében a DAX-szerkesztőt használhatja egy egyéni részletsor-kifejezés megadására. A részletsor-kifejezés egy mérték egy olyan tulajdonsága, amely további információkat nyújt a végfelhasználóknak a mérték aggregált eredményeiről. 
  
A lecke elvégzésének várható időtartama: **10 perc**.  
  
## <a name="prerequisites"></a>Előfeltételek  
Ez a kiegészítő lecke a táblázatos modellezésről szóló oktatóanyag része. Az ebben a kiegészítő leckében található feladatok végrehajtása előtt be kell fejeznie minden előző leckét, vagy rendelkeznie kell egy befejezett Adventure Works internetes értékesítési minta modellprojekttel.  
  
## <a name="whats-the-issue"></a>Mi a probléma?
Nézzük InternetTotalSales mérték részleteit egy részletes sorok kifejezés hozzáadása előtt.

1.  Az SSDT-ben kattintson a **Modell** menü > **Elemzés az Excelben** elemére az Excel megnyitásához és egy üres kimutatás létrehozásához.
  
2.  A **Kimutatás mezők** részben adja hozzá a **InternetTotalSales** mértéket a FactInternetSales táblából az **Értékekhez**, a **CalendarYear** értéket a DimDate táblából az **Oszlopokhoz**, valamint az **EnglishCountryRegionName** értéket a **Sorokhoz**. A kimutatás most egy összesített eredményeket ad a InternetTotalSales intézkedés régiók és év. 

    ![aas-lesson-detail-rows-pivottable](../tutorials/media/aas-lesson-detail-rows-pivottable.png)

3. A kimutatásban kattintson duplán egy évhez és régiónévhez tartozó aggregált értékre. Ausztrália és az év 2014 érték. Megnyílik egy új, nem hasznos adatokat tartalmazó lap.

    ![aas-lesson-detail-rows-pivottable](../tutorials/media/aas-lesson-detail-rows-sheet.png)
  
A cél, amelyek az összesített eredmény a InternetTotalSales mérték adatok sorok és oszlopok tartalmazó tábla lesz. Ehhez adja hozzá a részletes sorok kifejezés a mérték tulajdonság.

## <a name="add-a-detail-rows-expression"></a>Részletsor-kifejezés hozzáadása

#### <a name="to-create-a-detail-rows-expression"></a>Részletsor-kifejezés létrehozása 
  
1. A FactInternetSales tábla mértékrácsában kattintson az **InternetTotalSales** mértékre. 

2. A **Tulajdonságok** > **Részletsor-kifejezés** részben kattintson a szerkesztőgombra a DAX-szerkesztő megnyitásához.

    ![aas-lesson-detail-rows-ellipse](../tutorials/media/aas-lesson-detail-rows-ellipse.png)

3. Írja be a következő kifejezést a DAX-szerkesztőbe:

    ```
    SELECTCOLUMNS(
    FactInternetSales,
    "Sales Order Number", FactInternetSales[SalesOrderNumber],
    "Customer First Name", RELATED(DimCustomer[FirstName]),
    "Customer Last Name", RELATED(DimCustomer[LastName]),
    "City", RELATED(DimGeography[City]),
    "Order Date", FactInternetSales[OrderDate],
    "Internet Total Sales", [InternetTotalSales]
    )

    ```

    Ez a kifejezés neveket, oszlopokat és mértékeredményeket ad meg a FactInternetSales táblából, és a kapcsolódó táblázatokat akkor adja vissza a rendszer, amikor a felhasználó duplán rákattint egy összesített eredményre a kimutatásban vagy jelentésben.

4. Az Excelben törölje a 3. lépésben létrehozott lapot, majd kattintson duplán egy aggregált értékre. Ezúttal egy több hasznos információt tartalmazó, a mértékhez meghatározott részletsor-kifejezés tulajdonsággal rendelkező új lap nyílik meg.

    ![aas-lesson-detail-rows-detailsheet](../tutorials/media/aas-lesson-detail-rows-detailsheet.png)

5. Helyezze ismét üzembe a modellt.

  
## <a name="see-also"></a>Lásd még  

[SELECTCOLUMNS függvény (DAX)](https://msdn.microsoft.com/library/mt761759.aspx)   
[Kiegészítő lecke - dinamikus biztonsági](../tutorials/aas-supplemental-lesson-dynamic-security.md)   
[Kiegészítő lecke – Hézagos hierarchiák](../tutorials/aas-supplemental-lesson-ragged-hierarchies.md)   
 