---
title: Webszolgáltatások felhasználása az Excelben
titleSuffix: ML Studio (classic) - Azure
description: A Azure Machine Learning Studio (klasszikus) megkönnyíti a webszolgáltatások közvetlen meghívását az Excelből anélkül, hogy kódot kellene írnia.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.custom: seodec18
ms.date: 02/01/2018
ms.openlocfilehash: 2e95c4bfbe7342e251e6d845fd4acfed6ff6109a
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/12/2020
ms.locfileid: "77150102"
---
# <a name="consuming-an-azure-machine-learning-studio-classic-web-service-from-excel"></a>Azure Machine Learning Studio (klasszikus) webszolgáltatás felhasználása az Excelből

 A Azure Machine Learning Studio (klasszikus) megkönnyíti a webszolgáltatások közvetlen meghívását az Excelből anélkül, hogy kódot kellene írnia.

Ha az Excel 2013-as (vagy újabb) vagy az Excel online-t használja, javasoljuk, hogy használja az Excel [Excel-bővítményt](excel-add-in-for-web-services.md).



## <a name="steps"></a>Lépések
Webszolgáltatás közzététele. [3. Oktatóanyag: a hitelkockázat-modell üzembe helyezése](tutorial-part3-credit-risk-deploy.md) elmagyarázza, hogyan teheti meg. Az Excel-munkafüzet funkció jelenleg csak egy egyetlen kimeneti (azaz egy pontozó egycímkés) kérés/válasz szolgáltatások esetében támogatott. 

Ha rendelkezik webszolgáltatással, kattintson a Studio bal oldalán található **WEBszolgáltatások** szakaszra, majd válassza ki az Excelből használni kívánt webszolgáltatást.

**Klasszikus webszolgáltatás**

1. A webszolgáltatás **irányítópult** lapján a **kérelem/válasz** szolgáltatás sora szerepel. Ha a szolgáltatás egyetlen kimenettel rendelkezett, akkor az **Excel-munkafüzet letöltése** hivatkozásra kell mutatnia az adott sorban.

    ![Excel-munkafüzet letöltése a Studio (klasszikus) webszolgáltatás-portál használatával](./media/consuming-from-excel/excellink.png)
2. Kattintson az **Excel-munkafüzet letöltése**elemre.

**Új webszolgáltatás**

1. A Azure Machine Learning webszolgáltatási portálon válassza **a**felhasználás elemet.
2. A **Felhasználás lap webszolgáltatás-használat beállításai** szakaszában kattintson az Excel ikonra.

**A munkafüzet használata**

1. Nyissa meg a munkafüzetet.
2. Megjelenik egy biztonsági figyelmeztetés; kattintson a **Szerkesztés engedélyezése** gombra.

    ![Szerkesztés engedélyezése a védett nézet biztonsági figyelmeztetésének eltávolításához](./media/consuming-from-excel/enableeditting.png)
3. Biztonsági figyelmeztetés jelenik meg. Kattintson a **tartalom engedélyezése** gombra a makrók futtatásához a számolótáblán.

    ![Tartalom engedélyezése a makrók letiltására vonatkozó biztonsági figyelmeztetés elvetéséhez](./media/consuming-from-excel/enablecontent.png)
4. Ha makrók engedélyezve vannak, egy tábla jön létre. A kék színű oszlopoknak az ERŐFORRÁSREKORDOK webszolgáltatásba vagy a **paraméterekbe**való bevitelre van szükségük. Jegyezze fel a ERŐFORRÁSREKORD-szolgáltatás kimenetét, az **előre jelzett értékeket** zöld színnel. Amikor egy adott sorának az összes oszlop ki vannak töltve, a munkafüzet automatikusan a pontozási API- és a pontozott eredményeit jeleníti meg.

    ![A paraméterek bemenetei és az eredményül kapott előre jelzett értékek táblázata](./media/consuming-from-excel/sampletable.png)
5. Pontszámot rendelni az egynél több sorral, adja meg a második sor az adatok és az előre jelzett értékek előállítása. Több sor egyszerre is beillesztheti.

Használhatja az Excel-szolgáltatások (diagramokat, a power mappel, feltételes formázás, stb.) az előre jelzett értékek segítségével jelenítheti meg az adatokat.

## <a name="sharing-your-workbook"></a>A munkafüzet megosztása
A makrók működjön az API-kulcsot a számolótábla részét kell lennie. Ez azt jelenti, hogy ossza meg a munkafüzetet csak a megbízható entitások/szolgáltatásra.

## <a name="automatic-updates"></a>Automatikus frissítések
Az RRS kezdeményezték a két ezekben a helyzetekben:

1. Az első alkalommal, amikor egy sor tartalma az összes **paraméterében** szerepel
2. A **Paraméterek** bármelyike olyan sorban változik, amelyben minden **paraméter** be van írva.