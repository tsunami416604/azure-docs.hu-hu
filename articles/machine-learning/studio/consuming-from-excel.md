---
title: Webszolgáltatás használata az Excelben
titleSuffix: ML Studio (classic) - Azure
description: Az Azure Machine Learning Studio (klasszikus) megkönnyíti a webszolgáltatások közvetlen hívását az Excelből anélkül, hogy bármilyen kódot kellene írnia.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 02/01/2018
ms.openlocfilehash: 333ed411ab818cff77a7cba6c7de4f42c36f5b6b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79218213"
---
# <a name="consuming-an-azure-machine-learning-studio-classic-web-service-from-excel"></a>Azure Machine Learning Studio (klasszikus) webszolgáltatás fogyasztása az Excelből

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Az Azure Machine Learning Studio (klasszikus) megkönnyíti a webszolgáltatások közvetlen hívását az Excelből anélkül, hogy bármilyen kódot kellene írnia.

Ha Excel 2013-at (vagy újabb) vagy Excel Online-t használ, javasoljuk, hogy használja az Excel [Excel bővítményt.](excel-add-in-for-web-services.md)



## <a name="steps"></a>Lépések
Webszolgáltatás közzététele. [3. oktatóanyag: A hitelkockázati modell üzembe helyezése](tutorial-part3-credit-risk-deploy.md) ismerteti, hogyan kell csinálni. Jelenleg az Excel-munkafüzet szolgáltatás csak olyan kérés/válasz szolgáltatások esetében támogatott, amelyek egyetlen kimenettel (azaz egyetlen pontozási címkével) rendelkeznek. 

Miután rendelkezik webszolgáltatással, kattintson a stúdió bal oldalán található **WEBSERVICES** szakaszra, majd válassza ki az Excelből használni kívánt webszolgáltatást.

**Klasszikus webszolgáltatás**

1. A **DASHBOARD** lapon a webszolgáltatás egy sor a **KÉRELEM/VÁLASZ** szolgáltatás. Ha ez a szolgáltatás egyetlen kimenettel volt, ebben a sorban az **Excel-munkafüzet letöltése** hivatkozásnak kell lennie.

    ![Excel-munkafüzet letöltése a Studio (klasszikus) webszolgáltatási portálon](./media/consuming-from-excel/excellink.png)
2. Kattintson **az Excel-munkafüzet letöltése gombra.**

**Új webszolgáltatás**

1. Az Azure Machine Learning webszolgáltatás-portálon válassza a **Felhasználás lehetőséget.**
2. A Használat lap **Webszolgáltatás-felhasználási beállítások** csoportjában kattintson az Excel ikonra.

**A munkafüzet használata**

1. Nyissa meg a munkafüzetet.
2. Megjelenik egy biztonsági figyelmeztetés; kattintson a **Szerkesztés engedélyezése** gombra.

    ![Szerkesztés engedélyezése a védett nézet biztonsági figyelmeztetésének eltávolításához](./media/consuming-from-excel/enableeditting.png)
3. Megjelenik egy biztonsági figyelmeztetés. A számolótábla makróinak futtatásához kattintson a **Tartalom engedélyezése** gombra.

    ![Tartalom engedélyezése a biztonsági figyelmeztetés letiltására vonatkozó makrók elvetéséhez](./media/consuming-from-excel/enablecontent.png)
4. Ha a makrók engedélyezve vannak, a program táblázatot hoz létre. A kék színű oszlopok szükségesek az RRS webszolgáltatásba vagy a **PARAMETERS-be való bevitelhez.** Figyelje meg az RRS szolgáltatás kimenetét, **a becsült értékekzöld** színnel. Ha egy adott sor összes oszlopa ki van töltve, a munkafüzet automatikusan meghívja a pontozási API-t, és megjeleníti a pontozott eredményeket.

    ![A paraméterbemenetek és az eredményül kapott előre jelzett értékek táblázata](./media/consuming-from-excel/sampletable.png)
5. Egynél több sor pontozásához töltse ki a második sort adatokkal, és az előre jelzett értékek keletkeznek. Akár több sort is beilleszthet egyszerre.

Az Előre jelzett értékekkel az Excel bármely szolgáltatását (grafikonok, power map, feltételes formázás stb.) használhatja az előre jelzett értékekkel, hogy segítsen az adatok megjelenítésében.

## <a name="sharing-your-workbook"></a>A munkafüzet megosztása
Ahhoz, hogy a makrók működjenek, az API-kulcsnak a számolótábla részét kell, hogy legyen. Ez azt jelenti, hogy a munkafüzetet csak megbízható entitásokkal/személyekkel oszthatja meg.

## <a name="automatic-updates"></a>Automatikus frissítések
Az RRS-hívás a következő két helyzetben történik:

1. Az első alkalommal, amikor egy sor tartalma az összes **paraméterében megjelenik**
2. Bármikor, amikor a **PARAMÉTEREK bármelyike** megváltozik egy olyan sorban, amelyen az összes **PARAMÉTER be van** írva.