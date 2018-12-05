---
title: Az adatmodellezés az Azure Time Series Insightsban |} A Microsoft Docs
description: Adatok modellezése az Azure Time Series Insights ismertetése
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/03/2018
ms.openlocfilehash: dc6244b6e263d3fb963d40b2f0c626cdfa9ecff8
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52873461"
---
# <a name="data-modeling-in-azure-time-series-insights"></a>Az Azure Time Series Insightsban adatmodellezési

Ez a dokumentum azt ismerteti, hogyan használható a **Time Series modellek** követve az Azure Time Series Insights (előzetes verzió). Számos gyakori adatforgatókönyvek azt részletezi.

Olvassa el a [(előzetes verzió) az Azure TSI explorer](./time-series-insights-update-explorer.md) cikkben további információt a frissítés megnyitásával.

## <a name="types"></a>Típusok

### <a name="how-to-create-a-single-type"></a>Egyetlen gazdagéptípust létrehozása

1. Első lépésként a TSM modell választó panel fejlécére, és a menüből válassza ki a típusokat. Ezt követően összecsukása TSM típusok összpontosíthat a panelen:

    ![portal_one][1]

1. Kattintson a **Hozzáadás** gombra.
1. Bemeneti típusok tartozó összes adatot, és kattintson a **létrehozás**. Így hozzunk létre típusok a környezetben:

    ![portal_two][2]

### <a name="how-to-bulk-upload-one-or-more-types"></a>Tömeges feltöltés egy vagy több típusa

1. Kattintson a **JSON feltöltése**.
1. Válassza ki, hogy a típus hasznos adatokat tartalmazó fájlt.
1. Kattintson a **feltöltése**

    ![portal_three][3]

### <a name="how-to-edit-a-single-type"></a>Egyetlen gazdagéptípust szerkesztése

* Válassza ki a típusát, majd kattintson a **szerkesztése** gombra. A szükséges módosításokat, és kattintson a **mentése**:

    ![portal_four][4]

### <a name="how-to-delete-a-type"></a>A típus törlése

* Válassza ki a típusát, majd kattintson a **törlése** gombra. Ha nincsenek példányok azon típusainak társítva, akkor törlődik:

    ![portal_five][5]

## <a name="hierarchies"></a>Hierarchiák

### <a name="how-to-create-a-single-hierarchy"></a>Egyetlen hierarchia létrehozása

1. Első lépésként a TSM modell választó panel fejlécére, és válassza ki a hierarchiák a menüből. Ezt követően összecsukása TSM típusok összpontosíthat a panelen:

    ![portal_six][6]

1. Kattintson a **hozzáadása**

    ![portal_seven][7]

1. Kattintson a **hozzáadása szint** a jobb oldali ablaktáblán:

    ![portal_eight][8]

1. Adja meg a hierarchia adatait, és kattintson a **létrehozás**:

    ![portal_nine][9]

### <a name="how-to-bulk-upload-one-or-more-hierarchies"></a>Tömeges feltöltés egy vagy több hierarchiák

1. Kattintson a **JSON feltöltése**.
1. Válassza ki a fájlt, amely tartalmazza a hierarchia hasznos.
1. Kattintson a **feltöltése**:

    ![portal_ten][10]

### <a name="how-to-edit-a-single-hierarchy"></a>Egyetlen hierarchia szerkesztése

* Válassza ki a hierarchiában, és kattintson a a **szerkesztése** gombra. A szükséges módosításokat, és kattintson a **mentése**:

    ![portal_eleven][11]

### <a name="how-to-delete-a-hierarchy"></a>Egy hierarchia törlése

* Válassza ki a hierarchiában, és kattintson a **törlése** gombra. Ha nincsenek példányok társítva a hierarchiában, törlődik.

    ![portal_twelve][12]

## <a name="instances"></a>Példányok

### <a name="how-to-create-a-single-instance"></a>Egyetlen példány létrehozása

1. Első lépésként a TSM modell választó panel fejlécére, és a menüből válassza ki a példányokat. Ezt követően összecsukása TSM típusok összpontosíthat a panelen:

    ![portal_thirteen][13]

1. Kattintson a **hozzáadása**:

    ![portal_fourteen][14]

1. Adja meg a példány részleteit, válassza ki a hozzárendelés típusa és a hierarchia, kattintson a **létrehozás**.

### <a name="how-to-bulk-upload-one-or-more-instances"></a>Tömeges feltöltés egy vagy több példány

1. Kattintson a **JSON feltöltése**.
1. Válassza ki a fájlt, amely tartalmazza a példányok hasznos:

    ![portal_fifteen][15]

1. Kattintson a **feltöltése**.

### <a name="how-to-edit-a-single-instance"></a>Egyetlen példány szerkesztése

* Válassza ki azt a példányt, és kattintson a **szerkesztése** gombra. A szükséges módosításokat, és kattintson a **mentése**:

    ![portal_sixteen][16]

### <a name="how-to-delete-an-instance"></a>Egy példány törlése

* Válassza ki azt a példányt, majd kattintson a **törlése** gombra. Ha nincsenek események a példányok társítva, akkor törlődik.

## <a name="next-steps"></a>További lépések

További információ [adatmodellezés](./time-series-insights-update-tsm.md) bővebben **Time Series modellek**.

Megtekintheti a (előzetes verzió) az Azure TSI Explorerben engedélyezett [cikk](./time-series-insights-update-explorer.md) további információ az előzetes verzióra.

További információ a támogatott JSON-alakzatok, olvassa el [támogatott JSON-alakzatok](./time-series-insights-send-events.md#json).

<!-- Images -->
[1]: media/v2-update-how-to-tsm/portal_one.png
[2]: media/v2-update-how-to-tsm/portal_two.png
[3]: media/v2-update-how-to-tsm/portal_three.png
[4]: media/v2-update-how-to-tsm/portal_four.png
[5]: media/v2-update-how-to-tsm/portal_five.png
[6]: media/v2-update-how-to-tsm/portal_six.png
[7]: media/v2-update-how-to-tsm/portal_seven.png
[8]: media/v2-update-how-to-tsm/portal_eight.png
[9]: media/v2-update-how-to-tsm/portal_nine.png
[10]: media/v2-update-how-to-tsm/portal_ten.png
[11]: media/v2-update-how-to-tsm/portal_eleven.png
[12]: media/v2-update-how-to-tsm/portal_twelve.png
[13]: media/v2-update-how-to-tsm/portal_thirteen.png
[14]: media/v2-update-how-to-tsm/portal_fourteen.png
[15]: media/v2-update-how-to-tsm/portal_fifteen.png
[16]: media/v2-update-how-to-tsm/portal_sixteen.png