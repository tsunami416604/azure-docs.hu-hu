---
title: "A Machine Learning webszolgáltatásba az Excelből felhasználása |} Microsoft Docs"
description: "Az Azure Machine Learning webszolgáltatásba az Excelből felhasználása"
services: machine-learning
documentationcenter: 
author: tedway
manager: jhubbard
editor: cgronlun
ms.assetid: 3f3cdd2f-1816-487e-ab78-530e01e9788f
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 2/1/2018
ms.author: tedway
ms.openlocfilehash: cb0d02bc580822dcc9451dc6c22bd28d895dbbf7
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/03/2018
---
# <a name="consuming-an-azure-machine-learning-web-service-from-excel"></a>Azure Machine Learning webszolgáltatások használata az Excel programból
 Az Azure Machine Learning Studio megkönnyíti a webes szolgáltatások hívja közvetlenül az Excelből kód írása nélkül.

Ha az Excel 2013 (vagy újabb verzió) vagy az Excel Online, akkor azt javasoljuk, hogy használja-e az Excel [Excel-bővítmény](excel-add-in-for-web-services.md).

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="steps"></a>Lépések
A webszolgáltatás közzététele. [Ezen a lapon](walkthrough-5-publish-web-service.md) ismerteti, hogyan teheti meg. Az Excel-munkafüzet szolgáltatás jelenleg csak egy egyetlen kimeneti (Ez azt jelenti, hogy egy pontozási egycímkés) rendelkező kérelem/válasz szolgáltatásokat támogatott. 

Miután egy webszolgáltatás, kattintson a a **WEBSZOLGÁLTATÁSOK** bal oldalán a studio szakaszt, és válassza ki a webszolgáltatás számára, Excel felhasználását.

**Klasszikus webszolgáltatás**

1. Az a **IRÁNYÍTÓPULT** lap a webkiszolgáló egy sort a **kérelem/válasz** szolgáltatás. Ha ez a szolgáltatás egyetlen kimeneti rendelkezett, láthatja a **töltse le az Excel-munkafüzet** sorhoz hivatkozásra.
   
    ![][1]
2. Kattintson a **töltse le az Excel-munkafüzet**.

**Új webszolgáltatás**

1. Válassza az Azure Machine Learning webszolgáltatás portál **felhasználás**.
2. A felhasználás lapon a a **webes szolgáltatás felhasználásához beállításai** területen kattintson az Excel ikonra.

**A munkafüzet használatát**

1. Nyissa meg a munkafüzetet.
2. Biztonsági figyelmeztetés jelenik meg; Kattintson a **szerkesztésének engedélyezése** gombra.
   
    ![][2]
3. Biztonsági figyelmeztetés jelenik meg. Kattintson a **tartalom engedélyezése** gombra a makrók futtatását a munkalapon.
   
    ![][3]
4. Ha makrók engedélyezve vannak, egy tábla jön létre. Oszlopok kék is szükséges az RRS webszolgáltatás bemeneteként vagy **paraméterek**. Vegye figyelembe a RR-EKET szolgáltatás kimeneti **ELŐREJELZETT ÉRTÉKEKET** zöld. Minden oszlop egy adott sor kitöltődnek, ha a munkafüzet automatikusan a pontozási API-t és a pontozott eredményeit jeleníti meg.
   
    ![][4]
5. Pontozásához egynél több sorban, a második sor adatok és az előre jelzett értékek kitöltés előállítása. Több sort is beillesztheti egyszerre.

Segítségével (diagramokat, energiagazdálkodási térkép, formázási, stb.) az Excel-szolgáltatások egyikét sem az előre jelzett értékek az adatok megjelenítése érdekében.    

## <a name="sharing-your-workbook"></a>A munkafüzet megosztása
A makrók működjön az API-kulcs a táblázat részének kell lennie. Ez azt jelenti, hogy ossza meg a munkafüzet csak a megbízható entitások/személyeknek.

## <a name="automatic-updates"></a>Automatikus frissítések
Az RRS kezdeményezték a két ezekben a helyzetekben:

1. Először egy sor tartalma az összes annak **paraméterek**
2. Amikor bármelyik a **paraméterek** kellett az összes sor változásairól a **paraméterek** megadott.

[1]: ./media/consuming-from-excel/excellink.png
[2]: ./media/consuming-from-excel/enableeditting.png
[3]: ./media/consuming-from-excel/enablecontent.png
[4]: ./media/consuming-from-excel/sampletable.png
