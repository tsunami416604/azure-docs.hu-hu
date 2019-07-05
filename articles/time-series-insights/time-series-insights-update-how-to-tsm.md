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
ms.date: 06/26/2019
ms.custom: seodec18
ms.openlocfilehash: 05faf77d22f77da87e7c22d47473e6debf0f77c8
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67460898"
---
# <a name="data-modeling-in-azure-time-series-insights-preview"></a>Adatok modellezése az Azure Time Series Insights előzetes verziója

Ez a dokumentum ismerteti, hogyan használható a Time Series modellek az Azure Time Series Insights előzetes verziója a következő. Számos gyakori adatforgatókönyvek azt részletezi.

A frissítés használatával kapcsolatos további tudnivalókért olvassa el a [Azure Time Series Insights – előzetes explorer](./time-series-insights-update-explorer.md).

## <a name="types"></a>Típusok

### <a name="create-a-single-type"></a>Hozzon létre egy egyetlen típusa

1. Nyissa meg a Time Series modellek választó panel, és válassza ki **típusok** a menüből. A Time Series modellek típusok összpontosíthat a panel összecsukása.

    [![Hozzon létre egy egyetlen típusa](media/v2-update-how-to-tsm/portal-one.png)](media/v2-update-how-to-tsm/portal-one.png#lightbox)

1. Válassza a **Hozzáadás** lehetőséget.
1. Adjon meg, amelyeket típusok is vonatkozik, és válassza a **létrehozás**. Ez a művelet rekordtípust hoz létre a környezetben.

    [![-Típus hozzáadása](media/v2-update-how-to-tsm/portal-two.png)](media/v2-update-how-to-tsm/portal-two.png#lightbox)

### <a name="bulk-upload-one-or-more-types"></a>Tömeges feltöltés egy vagy több típusa

1. Válassza ki **JSON feltöltése**.
1. Válassza ki a típusú hasznos adatait tartalmazó fájlt.
1. Válassza a **Feltöltés** lehetőséget.

    [![Töltse fel a JSON-ban](media/v2-update-how-to-tsm/portal-three.png)](media/v2-update-how-to-tsm/portal-three.png#lightbox)

### <a name="edit-a-single-type"></a>Egyetlen gazdagéptípust szerkesztése

1. Válassza ki a típusát, és válassza ki **szerkesztése**. 
1. Végezze el a szükséges módosításokat, és válassza ki **mentése**.

    [![A típus szerkesztése](media/v2-update-how-to-tsm/portal-four.png)](media/v2-update-how-to-tsm/portal-four.png#lightbox)

### <a name="delete-a-type"></a>A típus törlése

1. Válassza ki a típusát, és válassza ki **törlése**.
1. Ha nincsenek példányok hozzárendelve a típusok, akkor az törlődik.

    [![A típus törlése](media/v2-update-how-to-tsm/portal-five.png)](media/v2-update-how-to-tsm/portal-five.png#lightbox)

## <a name="hierarchies"></a>Hierarchiák

### <a name="create-a-single-hierarchy"></a>Egyetlen hierarchia létrehozása

1. Nyissa meg a Time Series modellek választó panel, és válassza ki **hierarchiák** a menüből. A Time Series modellek hierarchiák összpontosíthat a panel összecsukása.

    [![Válassza ki a hierarchiák](media/v2-update-how-to-tsm/portal-six.png)](media/v2-update-how-to-tsm/portal-six.png#lightbox)

1. Válassza a **Hozzáadás** lehetőséget.

    [![Egy hierarchia hozzáadása](media/v2-update-how-to-tsm/portal-seven.png)](media/v2-update-how-to-tsm/portal-seven.png#lightbox)

1. Válassza ki **hozzáadása szint** a jobb oldali ablaktáblán.

    [![Adjon hozzá egy szint](media/v2-update-how-to-tsm/portal-eight.png)](media/v2-update-how-to-tsm/portal-eight.png#lightbox)

1. Adja meg a hierarchia adatait, és válassza ki **létrehozás**.

    [![Hozzon létre egy szint](media/v2-update-how-to-tsm/portal-nine.png)](media/v2-update-how-to-tsm/portal-nine.png#lightbox)

### <a name="bulk-upload-one-or-more-hierarchies"></a>Tömeges feltöltés egy vagy több hierarchiák

1. Válassza ki **JSON feltöltése**.
1. Válassza ki a hierarchia hasznos adatait tartalmazó fájlt.
1. Válassza a **Feltöltés** lehetőséget.

    [![Tömeges feltöltés hierarchiák](media/v2-update-how-to-tsm/portal-ten.png)](media/v2-update-how-to-tsm/portal-ten.png#lightbox)

### <a name="edit-a-single-hierarchy"></a>Egyetlen hierarchia szerkesztése

1. Válassza ki a hierarchiában, és válassza ki **szerkesztése**.
1. Végezze el a szükséges módosításokat, és válassza ki **mentése**.

    [![Egyetlen hierarchia szerkesztése](media/v2-update-how-to-tsm/portal-eleven.png)](media/v2-update-how-to-tsm/portal-eleven.png#lightbox)

### <a name="delete-a-hierarchy"></a>Egy hierarchia törlése

1. Válassza ki a hierarchiában, és válassza ki **törlése**. 
1. Ha nincsenek példányok hozzárendelve a hierarchiában, akkor az törlődik.

    [![Egy hierarchia törlése](media/v2-update-how-to-tsm/portal-twelve.png)](media/v2-update-how-to-tsm/portal-twelve.png#lightbox)

## <a name="instances"></a>példányok

### <a name="create-a-single-instance"></a>Egyetlen példány létrehozása

1. Nyissa meg a Time Series modellek választó panel, és válassza ki **példányok** a menüből. A Time Series modellek példányok összpontosíthat a panel összecsukása.

    [![Egyetlen példány létrehozása](media/v2-update-how-to-tsm/portal-thirteen.png)](media/v2-update-how-to-tsm/portal-thirteen.png#lightbox)

1. Válassza a **Hozzáadás** lehetőséget.

    [![Egy példány hozzáadása](media/v2-update-how-to-tsm/portal-fourteen.png)](media/v2-update-how-to-tsm/portal-fourteen.png#lightbox)

1. Adja meg a példány részleteit, típusa és a hierarchia társítás, válassza ki és **létrehozás**.

### <a name="bulk-upload-one-or-more-instances"></a>Tömeges feltöltés egy vagy több példány

1. Válassza ki **JSON feltöltése**.
1. Válassza ki a példányok hasznos adatait tartalmazó fájlt.

    [![Tömeges feltöltés egy vagy több példány](media/v2-update-how-to-tsm/portal-fifteen.png)](media/v2-update-how-to-tsm/portal-fifteen.png#lightbox)

1. Válassza a **Feltöltés** lehetőséget.

### <a name="edit-a-single-instance"></a>Egyetlen példány szerkesztése

1. Válassza ki azt a példányt, és válassza ki **szerkesztése**. 
1. Végezze el a szükséges módosításokat, és válassza ki **mentése**.

    [![Egyetlen példány szerkesztése](media/v2-update-how-to-tsm/portal-sixteen.png)](media/v2-update-how-to-tsm/portal-sixteen.png#lightbox)

## <a name="next-steps"></a>További lépések

- Time Series modellek kapcsolatos további információkért olvassa el a [adatmodellezés](./time-series-insights-update-tsm.md).

- További tudnivalók az előzetes verzióra, olvassa el [adatok megjelenítése az az Azure Time Series Insights – előzetes explorer](./time-series-insights-update-explorer.md).

- Támogatott JSON-alakzatok kapcsolatos további információkért olvassa el a [támogatott JSON-alakzatok](./time-series-insights-send-events.md#json).
