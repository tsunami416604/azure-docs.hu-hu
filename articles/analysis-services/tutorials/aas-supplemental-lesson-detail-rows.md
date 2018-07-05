---
title: 'Azure Analysis Services oktatóanyag – kiegészítő lecke: Részletsorok | Microsoft Docs'
description: Ismerteti a részletsor-kifejezés létrehozását az Azure Analysis Services oktatóanyagban.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 07/03/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 626258488afec4b3c3f025ae85bd3b5866aa0cf3
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/03/2018
ms.locfileid: "37443977"
---
# <a name="supplemental-lesson---detail-rows"></a>Kiegészítő lecke – Részletsorok

Ebben a kiegészítő leckében a DAX-szerkesztőt használhatja egy egyéni részletsor-kifejezés megadására. A részletsor-kifejezés egy mérték egy olyan tulajdonsága, amely további információkat nyújt a végfelhasználóknak a mérték aggregált eredményeiről. 
  
A lecke elvégzésének várható időtartama: **10 perc**.  
  
## <a name="prerequisites"></a>Előfeltételek  
Ez a kiegészítő lecke a táblázatos modellezésről szóló oktatóanyag része. Az ebben a kiegészítő leckében található feladatok végrehajtása előtt be kell fejeznie minden előző leckét, vagy rendelkeznie kell egy befejezett Adventure Works internetes értékesítési minta modellprojekttel.  
  
## <a name="whats-the-issue"></a>Mi a probléma?
Tekintsük át az InternetTotalSales mérték részleteit egy Részletsor-kifejezés hozzáadása előtt.

1.  Az SSDT-ben kattintson a **Modell** menü > **Elemzés az Excelben** elemére az Excel megnyitásához és egy üres kimutatás létrehozásához.
  
2.  A **Kimutatás mezők** részben adja hozzá a **InternetTotalSales** mértéket a FactInternetSales táblából az **Értékekhez**, a **CalendarYear** értéket a DimDate táblából az **Oszlopokhoz**, valamint az **EnglishCountryRegionName** értéket a **Sorokhoz**. A kimutatás mostantól lehetővé teszi az egy összesített eredmények az InternetTotalSales mérték régiók és év szerint. 

    ![aas-lesson-detail-rows-pivottable](../tutorials/media/aas-lesson-detail-rows-pivottable.png)

3. A kimutatásban kattintson duplán egy évhez és régiónévhez tartozó aggregált értékre. Ausztrália és 2014-es évre érték. Megnyílik egy új, nem hasznos adatokat tartalmazó lap.

    ![aas-lesson-detail-rows-pivottable](../tutorials/media/aas-lesson-detail-rows-sheet.png)
  
A cél az oszlopokat és adatokat, amely hozzájárul az InternetTotalSales mérték aggregált eredményeihez sorokat tartalmazó tábla. Ehhez adja hozzá a mérték tulajdonságaként Részletsor-kifejezés.

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
[Kiegészítő lecke – dinamikus biztonság](../tutorials/aas-supplemental-lesson-dynamic-security.md)   
[Kiegészítő lecke – Hézagos hierarchiák](../tutorials/aas-supplemental-lesson-ragged-hierarchies.md)   
 