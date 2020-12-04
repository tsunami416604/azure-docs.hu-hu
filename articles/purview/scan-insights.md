---
title: Az Azure hatáskörébe tartozó adatelemzések vizsgálata
description: Ez a útmutató ismerteti, hogyan lehet megtekinteni és használni az adatain belüli, a hatáskörébe vonatkozó elemzések vizsgálatát.
author: SunetraVirdi
ms.author: suvirdi
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/20/2020
ms.openlocfilehash: 00f72e1de230cdc68f86010b7b25d86debaa5eb5
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/03/2020
ms.locfileid: "96575788"
---
# <a name="scan-insights-on-your-data-in-azure-purview"></a>Az Azure hatáskörébe tartozó adatelemzések vizsgálata

Ez a útmutató útmutatást nyújt az Azure-beli és az adataihoz való hozzáféréshez, megtekintéséhez és szűréséhez.

Ebben a útmutatóban megismerheti a következőket:

> [!div class="checklist"]
> * Megtekintheti a hatáskörébe tartozó fiókból származó bepillantást.
> * Megtekintheti a vizsgálatokhoz tartozó madártávlatból.

## <a name="prerequisites"></a>Előfeltételek

A hatáskörébe tartozó ismeretek megismerése előtt győződjön meg arról, hogy végrehajtotta a következő lépéseket:

* Állítsa be az Azure-erőforrásokat, és töltse fel a fiókot adatokkal.
* Állítson be és végezzen el egy vizsgálatot az adatforráson.

További információ: [adatforrások kezelése az Azure hatáskörébe (előzetes verzió)](manage-data-sources.md).

## <a name="use-purview-scan-insights"></a>A hatáskörébe-ellenőrzési ismeretek használata

Az Azure-ban a források regisztrálása és bevizsgálása végezhető el. A vizsgálat állapotát időben tekintheti meg a vizsgálati eredményekben. Az ismeretekből megtudhatja, hogy hány vizsgálat meghiúsult, sikeres volt, vagy egy adott időszakon belül megszakítva.

### <a name="view-scan-insights"></a>Vizsgálati eredmények megtekintése

1. Nyissa meg a Azure Portal **Azure hatáskörébe** tartozó példány képernyőjét, és válassza ki a hatáskörébe tartozó fiókot.

1. Az **Áttekintés** lap első **lépések** szakaszában válassza ki a **hatáskörébe tartozó Studio** -csempét.

   :::image type="content" source="./media/scan-insights/portal-access.png" alt-text="A hatáskörébe tartozó Azure Portal elindítása":::

1. A hatáskörébe tartozó **kezdőlapon** válassza az eredmények **megtekintése** csempét az információhoz való hozzáféréshez **Insights** :::image type="icon" source="media/scan-insights/ico-insights.png" border="false"::: .

   :::image type="content" source="./media/scan-insights/view-insights.png" alt-text="Tekintse meg az Azure Portal":::

1. Az **észlelések** :::image type="icon" source="media/scan-insights/ico-insights.png" border="false"::: területen válassza a vizsgálat lehetőséget **Scans** a hatáskörébe- **ellenőrzési** információk jelentés megjelenítéséhez.

### <a name="view-high-level-kpis-to-show-count-of-scans-by-status"></a>Magas szintű KPI-k megtekintése az állapot szerinti vizsgálatok számának megjelenítéséhez
 
A magas szintű KPI-k az adott időszakon belül futtatott összes vizsgálatot megjelenítik. Az időtartam az elmúlt 30 napban van megadva. Azonban az elmúlt hét nap is kiválasztható. Az Időszűrő alapján a KPI-értékek a megfelelő vizsgálatok számát tükrözik.


A kiválasztott Időszűrő érték alapján megtekintheti a sikeres, sikertelen és megszakított vizsgálatok eloszlását hetente vagy a gráf napján.

   :::image type="content" source="./media/scan-insights/scan-insights.png" alt-text="Vizsgálati eredmények megtekintése":::

## <a name="next-steps"></a>További lépések

További információ az Azure-beli felügyeleti jelentésekről az [Asset](./asset-insights.md) betekintéssel
