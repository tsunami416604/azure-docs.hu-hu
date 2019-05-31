---
title: Adatok modellezése az Azure Time Series Insights – előzetes |} A Microsoft Docs
description: Ismerje meg, adatmodellezést Azure Time Series Insights előzetes verziója.
author: ashannon7
ms.author: dpalled
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 05/07/2019
ms.custom: seodec18
ms.openlocfilehash: 73384868deb8f0e33b233e363c42a12adbcbe402
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2019
ms.locfileid: "66237568"
---
# <a name="data-modeling-in-azure-time-series-insights-preview"></a>Adatok modellezése az Azure Time Series Insights előzetes verziója

Ez a dokumentum ismerteti, hogyan használható a Time Series modellek az Azure Time Series Insights előzetes verziója a következő. Számos gyakori adatforgatókönyvek azt részletezi.

A frissítés használatával kapcsolatos további tudnivalókért olvassa el a [Azure Time Series Insights – előzetes explorer](./time-series-insights-update-explorer.md).

## <a name="types"></a>Típusok

### <a name="create-a-single-type"></a>Hozzon létre egy egyetlen típusa

1. Nyissa meg a Time Series modellek választó panel, és válassza ki **típusok** a menüből. A Time Series modellek típusok összpontosíthat a panel összecsukása.

    [![Hozzon létre egy egyetlen típusa](media/v2-update-how-to-tsm/portal_one.png)](media/v2-update-how-to-tsm/portal_one.png#lightbox)

1. Válassza a **Hozzáadás** lehetőséget.
1. Adjon meg, amelyeket típusok is vonatkozik, és válassza a **létrehozás**. Ez a művelet rekordtípust hoz létre a környezetben.

    [![-Típus hozzáadása](media/v2-update-how-to-tsm/portal_two.png)](media/v2-update-how-to-tsm/portal_two.png#lightbox)

### <a name="bulk-upload-one-or-more-types"></a>Tömeges feltöltés egy vagy több típusa

1. Válassza ki **JSON feltöltése**.
1. Válassza ki a típusú hasznos adatait tartalmazó fájlt.
1. Válassza a **Feltöltés** lehetőséget.

    [![Töltse fel a JSON-ban](media/v2-update-how-to-tsm/portal_three.png)](media/v2-update-how-to-tsm/portal_three.png#lightbox)

### <a name="edit-a-single-type"></a>Egyetlen gazdagéptípust szerkesztése

1. Válassza ki a típusát, és válassza ki **szerkesztése**. 
1. Végezze el a szükséges módosításokat, és válassza ki **mentése**.

    [![A típus szerkesztése](media/v2-update-how-to-tsm/portal_four.png)](media/v2-update-how-to-tsm/portal_four.png#lightbox)

### <a name="delete-a-type"></a>A típus törlése

1. Válassza ki a típusát, és válassza ki **törlése**.
1. Ha nincsenek példányok hozzárendelve a típusok, akkor az törlődik.

    [![A típus törlése](media/v2-update-how-to-tsm/portal_five.png)](media/v2-update-how-to-tsm/portal_five.png#lightbox)

## <a name="hierarchies"></a>Hierarchiák

### <a name="create-a-single-hierarchy"></a>Egyetlen hierarchia létrehozása

1. Nyissa meg a Time Series modellek választó panel, és válassza ki **hierarchiák** a menüből. A Time Series modellek hierarchiák összpontosíthat a panel összecsukása.

    [![Válassza ki a hierarchiák](media/v2-update-how-to-tsm/portal_six.png)](media/v2-update-how-to-tsm/portal_six.png#lightbox)

1. Válassza a **Hozzáadás** lehetőséget.

    [![Egy hierarchia hozzáadása](media/v2-update-how-to-tsm/portal_seven.png)](media/v2-update-how-to-tsm/portal_seven.png#lightbox)

1. Válassza ki **hozzáadása szint** a jobb oldali ablaktáblán.

    [![Adjon hozzá egy szint](media/v2-update-how-to-tsm/portal_eight.png)](media/v2-update-how-to-tsm/portal_eight.png#lightbox)

1. Adja meg a hierarchia adatait, és válassza ki **létrehozás**.

    [![Hozzon létre egy szint](media/v2-update-how-to-tsm/portal_nine.png)](media/v2-update-how-to-tsm/portal_nine.png#lightbox)

### <a name="bulk-upload-one-or-more-hierarchies"></a>Tömeges feltöltés egy vagy több hierarchiák

1. Válassza ki **JSON feltöltése**.
1. Válassza ki a hierarchia hasznos adatait tartalmazó fájlt.
1. Válassza a **Feltöltés** lehetőséget.

    [![Tömeges feltöltés hierarchiák](media/v2-update-how-to-tsm/portal_ten.png)](media/v2-update-how-to-tsm/portal_ten.png#lightbox)

### <a name="edit-a-single-hierarchy"></a>Egyetlen hierarchia szerkesztése

1. Válassza ki a hierarchiában, és válassza ki **szerkesztése**.
1. Végezze el a szükséges módosításokat, és válassza ki **mentése**.

    [![Egyetlen hierarchia szerkesztése](media/v2-update-how-to-tsm/portal_eleven.png)](media/v2-update-how-to-tsm/portal_eleven.png#lightbox)

### <a name="delete-a-hierarchy"></a>Egy hierarchia törlése

1. Válassza ki a hierarchiában, és válassza ki **törlése**. 
1. Ha nincsenek példányok hozzárendelve a hierarchiában, akkor az törlődik.

    [![Egy hierarchia törlése](media/v2-update-how-to-tsm/portal_twelve.png)](media/v2-update-how-to-tsm/portal_twelve.png#lightbox)

## <a name="instances"></a>példányok

### <a name="create-a-single-instance"></a>Egyetlen példány létrehozása

1. Nyissa meg a Time Series modellek választó panel, és válassza ki **példányok** a menüből. A Time Series modellek példányok összpontosíthat a panel összecsukása.

    [![Egyetlen példány létrehozása](media/v2-update-how-to-tsm/portal_thirteen.png)](media/v2-update-how-to-tsm/portal_thirteen.png#lightbox)

1. Válassza a **Hozzáadás** lehetőséget.

    [![Egy példány hozzáadása](media/v2-update-how-to-tsm/portal_fourteen.png)](media/v2-update-how-to-tsm/portal_fourteen.png#lightbox)

1. Adja meg a példány részleteit, típusa és a hierarchia társítás, válassza ki és **létrehozás**.

### <a name="bulk-upload-one-or-more-instances"></a>Tömeges feltöltés egy vagy több példány

1. Válassza ki **JSON feltöltése**.
1. Válassza ki a példányok hasznos adatait tartalmazó fájlt.

    [![Tömeges feltöltés egy vagy több példány](media/v2-update-how-to-tsm/portal_fifteen.png)](media/v2-update-how-to-tsm/portal_fifteen.png#lightbox)

1. Válassza a **Feltöltés** lehetőséget.

### <a name="edit-a-single-instance"></a>Egyetlen példány szerkesztése

1. Válassza ki azt a példányt, és válassza ki **szerkesztése**. 
1. Végezze el a szükséges módosításokat, és válassza ki **mentése**.

    [![Egyetlen példány szerkesztése](media/v2-update-how-to-tsm/portal_sixteen.png)](media/v2-update-how-to-tsm/portal_sixteen.png#lightbox)

## <a name="next-steps"></a>További lépések

- Time Series modellek kapcsolatos további információkért olvassa el a [adatmodellezés](./time-series-insights-update-tsm.md).

- További tudnivalók az előzetes verzióra, olvassa el [adatok megjelenítése az az Azure Time Series Insights – előzetes explorer](./time-series-insights-update-explorer.md).

- Támogatott JSON-alakzatok kapcsolatos további információkért olvassa el a [támogatott JSON-alakzatok](./time-series-insights-send-events.md#json).
