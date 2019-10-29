---
title: Adatmodellezés Azure Time Series Insights előzetes verzióban | Microsoft Docs
description: Az adatmodellezés ismertetése Azure Time Series Insights előzetes verzióban.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 09/04/2019
ms.custom: seodec18
ms.openlocfilehash: a77cdcf332d450b2d3d9219ebfbaed2f799e7e6d
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/28/2019
ms.locfileid: "72991175"
---
# <a name="data-modeling-in-azure-time-series-insights-preview"></a>Adatmodellezés Azure Time Series Insights előzetes verzióban

Ez a cikk bemutatja, hogyan dolgozhat az idősorozat-modellel Azure Time Series Insights előzetes verzióban. Számos gyakori adatesetet részletez.

További információ a frissítés használatáról: [Azure Time Series Insights Preview Explorer](./time-series-insights-update-explorer.md).

## <a name="types"></a>Típusú

### <a name="create-a-single-type"></a>Egyetlen típus létrehozása

1. Lépjen az idősorozat-modell kiválasztó paneljére, és válassza a menü **típusok** elemét. Csukja össze a panelt, hogy az idősorozat-modell típusaira összpontosítsanak.

    [!["típusok" panel](media/v2-update-how-to-tsm/portal-one.png)](media/v2-update-how-to-tsm/portal-one.png#lightbox)

1. Válassza a **+ Hozzáadás** lehetőséget.
1. Adja meg a típusokra vonatkozó összes adatot, majd válassza a **Létrehozás**lehetőséget. Ez a művelet típusokat hoz létre a környezetben.

    [a típus hozzáadásának![kiválasztása](media/v2-update-how-to-tsm/portal-two.png)](media/v2-update-how-to-tsm/portal-two.png#lightbox)

### <a name="bulk-upload-one-or-more-types"></a>Egy vagy több típus tömeges feltöltése

1. Válassza a **JSON feltöltése**lehetőséget.
1. Válassza ki azt a fájlt, amely a típus adattartalmát tartalmazza.
1. Válassza a **Feltöltés** lehetőséget.

    [egy vagy több típus tömeges feltöltésének![kiválasztása](media/v2-update-how-to-tsm/portal-three.png)](media/v2-update-how-to-tsm/portal-three.png#lightbox)

### <a name="edit-a-single-type"></a>Egyetlen típus szerkesztése

1. Válassza ki a típust, és válassza a **Szerkesztés**lehetőséget. 
1. Végezze el a szükséges módosításokat, majd kattintson a **Mentés**gombra.

    [a típus szerkesztésének![kiválasztása](media/v2-update-how-to-tsm/portal-four.png)](media/v2-update-how-to-tsm/portal-four.png#lightbox)

### <a name="delete-a-type"></a>Típus törlése

1. Válassza ki a típust, és válassza a **Törlés**lehetőséget.
1. Ha egyetlen példány sincs társítva a típusokhoz, azt a rendszer törli.

    [!["Törlés" gomb](media/v2-update-how-to-tsm/portal-five.png)](media/v2-update-how-to-tsm/portal-five.png#lightbox)

## <a name="hierarchies"></a>Hierarchiák

### <a name="create-a-single-hierarchy"></a>Egyetlen hierarchia létrehozása

1. Lépjen az idősorozat-modell kiválasztó paneljére, és válassza a menü **hierarchiák** elemét. Csukja össze a panelt az idősorozat-modell hierarchiáinak fókuszba helyezéséhez.

    [!["hierarchiák" panel](media/v2-update-how-to-tsm/portal-six.png)](media/v2-update-how-to-tsm/portal-six.png#lightbox)

1. Válassza a **+ Hozzáadás** lehetőséget.

    [!["Hozzáadás" gomb](media/v2-update-how-to-tsm/portal-seven.png)](media/v2-update-how-to-tsm/portal-seven.png#lightbox)

1. A jobb oldali ablaktáblán kattintson a **+ Hozzáadás szint** lehetőségre.

    [!["szint hozzáadása" gomb](media/v2-update-how-to-tsm/portal-eight.png)](media/v2-update-how-to-tsm/portal-eight.png#lightbox)

1. Adja meg a hierarchia részleteit, és válassza a **Létrehozás**lehetőséget.

    [![Hieararchy részletei és "létrehozás" gomb](media/v2-update-how-to-tsm/portal-nine.png)](media/v2-update-how-to-tsm/portal-nine.png#lightbox)

### <a name="bulk-upload-one-or-more-hierarchies"></a>Egy vagy több hierarchia tömeges feltöltése

1. Válassza a **JSON feltöltése**lehetőséget.
1. Válassza ki a hierarchia adattartalmát tartalmazó fájlt.
1. Válassza a **Feltöltés** lehetőséget.

    [a hierarchiák tömeges feltöltésének![kiválasztása](media/v2-update-how-to-tsm/portal-ten.png)](media/v2-update-how-to-tsm/portal-ten.png#lightbox)

### <a name="edit-a-single-hierarchy"></a>Egyetlen hierarchia szerkesztése

1. Válassza ki a hierarchiát, és válassza a **Szerkesztés**lehetőséget.
1. Végezze el a szükséges módosításokat, majd kattintson a **Mentés**gombra.

    [egyetlen hierarchia szerkesztésének![kiválasztása](media/v2-update-how-to-tsm/portal-eleven.png)](media/v2-update-how-to-tsm/portal-eleven.png#lightbox)

### <a name="delete-a-hierarchy"></a>Hierarchia törlése

1. Válassza ki a hierarchiát, és válassza a **Törlés**lehetőséget. 
1. Ha a hierarchiához nem tartozik példány társítva, akkor azt a rendszer törli.

    [!["Törlés" gomb](media/v2-update-how-to-tsm/portal-twelve.png)](media/v2-update-how-to-tsm/portal-twelve.png#lightbox)

## <a name="instances"></a>Példányok

### <a name="create-a-single-instance"></a>Egyetlen példány létrehozása

1. Nyissa meg az idősorozat-modell kiválasztása panelt, és válassza a menü **példányok** elemét. Csukja össze a panelt az idősorozat-modell példányaira koncentrálva.

    [!["példányok" panel](media/v2-update-how-to-tsm/portal-thirteen.png)](media/v2-update-how-to-tsm/portal-thirteen.png#lightbox)

1. Válassza a **Hozzáadás** lehetőséget.

    [példányok hozzáadásának![kiválasztása](media/v2-update-how-to-tsm/portal-fourteen.png)](media/v2-update-how-to-tsm/portal-fourteen.png#lightbox)

1. Adja meg a példány részleteit, válassza ki a típus és hierarchia társítást, és válassza a **Létrehozás**lehetőséget.

### <a name="bulk-upload-one-or-more-instances"></a>Egy vagy több példány tömeges feltöltése

1. Válassza a **JSON feltöltése**lehetőséget.
1. Válassza ki a példányok hasznos adatait tartalmazó fájlt.

    [egy vagy több példány tömeges feltöltésének![kiválasztása](media/v2-update-how-to-tsm/portal-fifteen.png)](media/v2-update-how-to-tsm/portal-fifteen.png#lightbox)

1. Válassza a **Feltöltés** lehetőséget.

### <a name="edit-a-single-instance"></a>Egyetlen példány szerkesztése

1. Válassza ki a példányt, és válassza a **Szerkesztés**lehetőséget. 
1. Végezze el a szükséges módosításokat, majd kattintson a **Mentés**gombra.

    [egyetlen példány szerkesztésének![kiválasztása](media/v2-update-how-to-tsm/portal-sixteen.png)](media/v2-update-how-to-tsm/portal-sixteen.png#lightbox)

## <a name="next-steps"></a>Következő lépések

- A Time Series-modellel kapcsolatos további információkért olvassa el az [adatmodellezést](./time-series-insights-update-tsm.md)ismertető témakört.

- Ha többet szeretne megtudni az előzetes verzióról, olvassa el [Az adatmegjelenítés az Azure Time Series Insights Preview Explorerben című részt](./time-series-insights-update-explorer.md).

- A támogatott JSON-alakzatokkal kapcsolatos további tudnivalókért olvassa el a [támogatott JSON-alakzatok](./time-series-insights-send-events.md#supported-json-shapes)című témakört.
