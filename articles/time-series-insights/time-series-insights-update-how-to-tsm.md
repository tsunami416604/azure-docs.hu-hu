---
title: Az Azure Time Series Insights adatmodellezés – adatok modellezése az Azure Time Series Insights – előzetes |} A Microsoft Docs
description: Ismerje meg, adatmodellezést Azure Time Series Insights előzetes verziója.
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/10/2018
ms.custom: seodec18
ms.openlocfilehash: e68bc01d2c0781333454fa753992d0136fac0c06
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/12/2018
ms.locfileid: "53269090"
---
# <a name="data-modeling-in-azure-time-series-insights-preview"></a>Adatok modellezése az Azure Time Series Insights előzetes verziója

Ez a dokumentum ismerteti, hogyan használható a Time Series modellek az Azure Time Series Insights előzetes verziója a következő. Számos gyakori adatforgatókönyvek azt részletezi.

A frissítés használatával kapcsolatos további tudnivalókért olvassa el a [Azure Time Series Insights – előzetes explorer](./time-series-insights-update-explorer.md).

## <a name="types"></a>Típusok

### <a name="create-a-single-type"></a>Hozzon létre egy egyetlen típusa

1. Nyissa meg a Time Series modellek választó panel, és válassza ki **típusok** a menüből. A Time Series modellek típusok összpontosíthat a panel összecsukása.

    ![Portal_one][1]

1. Válassza a **Hozzáadás** lehetőséget.
1. Adjon meg, amelyeket típusok is vonatkozik, és válassza a **létrehozás**. Ez a művelet rekordtípust hoz létre a környezetben.

    ![Portal_two][2]

### <a name="bulk-upload-one-or-more-types"></a>Tömeges feltöltés egy vagy több típusa

1. Válassza ki **JSON feltöltése**.
1. Válassza ki a típusú hasznos adatait tartalmazó fájlt.
1. Válassza a **Feltöltés** lehetőséget.

    ![Portal_three][3]

### <a name="edit-a-single-type"></a>Egyetlen gazdagéptípust szerkesztése

Válassza ki a típusát, és válassza ki **szerkesztése**. Végezze el a szükséges módosításokat, és válassza ki **mentése**.

![Portal_four][4]

### <a name="delete-a-type"></a>A típus törlése

Válassza ki a típusát, és válassza ki **törlése**. Ha nincsenek példányok hozzárendelve a típusok, akkor az törlődik.

![Portal_five][5]

## <a name="hierarchies"></a>Hierarchiák

### <a name="create-a-single-hierarchy"></a>Egyetlen hierarchia létrehozása

1. Nyissa meg a Time Series modellek választó panel, és válassza ki **hierarchiák** a menüből. A Time Series modellek hierarchiák összpontosíthat a panel összecsukása.

    ![Portal_six][6]

1. Válassza a **Hozzáadás** lehetőséget.

    ![Portal_seven][7]

1. Válassza ki **hozzáadása szint** a jobb oldali ablaktáblán.

    ![Portal_eight][8]

1. Adja meg a hierarchia adatait, és válassza ki **létrehozás**.

    ![Portal_nine][9]

### <a name="bulk-upload-one-or-more-hierarchies"></a>Tömeges feltöltés egy vagy több hierarchiák

1. Válassza ki **JSON feltöltése**.
1. Válassza ki a hierarchia hasznos adatait tartalmazó fájlt.
1. Válassza a **Feltöltés** lehetőséget.

    ![Portal_ten][10]

### <a name="edit-a-single-hierarchy"></a>Egyetlen hierarchia szerkesztése

Válassza ki a hierarchiában, és válassza ki **szerkesztése**. Végezze el a szükséges módosításokat, és válassza ki **mentése**.

![Portal_eleven][11]

### <a name="delete-a-hierarchy"></a>Egy hierarchia törlése

Válassza ki a hierarchiában, és válassza ki **törlése**. Ha nincsenek példányok hozzárendelve a hierarchiában, akkor az törlődik.

![Portal_twelve][12]

## <a name="instances"></a>Példányok

### <a name="create-a-single-instance"></a>Egyetlen példány létrehozása

1. Nyissa meg a Time Series modellek választó panel, és válassza ki **példányok** a menüből. A Time Series modellek példányok összpontosíthat a panel összecsukása.

    ![Portal_thirteen][13]

1. Válassza a **Hozzáadás** lehetőséget.

    ![Portal_fourteen][14]

1. Adja meg a példány részleteit, típusa és a hierarchia társítás, válassza ki és **létrehozás**.

### <a name="bulk-upload-one-or-more-instances"></a>Tömeges feltöltés egy vagy több példány

1. Válassza ki **JSON feltöltése**.
1. Válassza ki a példányok hasznos adatait tartalmazó fájlt.

    ![Portal_fifteen][15]

1. Válassza a **Feltöltés** lehetőséget.

### <a name="edit-a-single-instance"></a>Egyetlen példány szerkesztése

Válassza ki azt a példányt, és válassza ki **szerkesztése**. Végezze el a szükséges módosításokat, és válassza ki **mentése**.

![Portal_sixteen][16]

### <a name="delete-an-instance"></a>Példány törlése

Válassza ki azt a példányt, és válassza ki **törlése**. Ha nincsenek események a példányok társítva, akkor az törlődik.

## <a name="next-steps"></a>További lépések

- Time Series modellek kapcsolatos további információkért olvassa el a [adatmodellezés](./time-series-insights-update-tsm.md).
- További tudnivalók az előzetes verzióra, olvassa el [adatok megjelenítése az az Azure Time Series Insights – előzetes explorer](./time-series-insights-update-explorer.md).
- Támogatott JSON-alakzatok kapcsolatos további információkért olvassa el a [támogatott JSON-alakzatok](./time-series-insights-send-events.md#json).

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