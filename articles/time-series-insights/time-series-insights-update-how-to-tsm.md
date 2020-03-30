---
title: Adatmodellezés előzetes környezetekben – Azure Time Series Insights | Microsoft dokumentumok
description: Ismerje meg az Azure Time Series Insights előzetes verziójában történő adatmodellezést.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 02/18/2020
ms.custom: seodec18
ms.openlocfilehash: 39ebbf99ad31cce20eabc20fbdc056c889235713
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77470751"
---
# <a name="data-modeling-in-azure-time-series-insights-preview"></a>Adatmodellezés az Azure Time Series Insights előzetes verzióban

Ez a cikk ismerteti, hogyan működik együtt a Time Series modell az Azure Time Series Insights előzetes verzióban. Több gyakori adatforgatókönyvet részletez.

> [!TIP]
> * További információ az előzetes [idősorozat-modellről.](time-series-insights-update-tsm.md)
> * További információ az Előzetes felhasználói felület en való navigálásról az [Azure Time Series Insights előzetes verziójának kezelőjében.](./time-series-insights-update-explorer.md)

## <a name="instances"></a>példányszám

Az Azure Time Series Insights explorer támogatja a **példány create,** **READ,** **UPDATE**és **DELETE** műveleteket a böngészőben. 

Először válassza ki a **Modell** nézetet a Time Series Insights explorer **Analyze** nézetből.

### <a name="create-a-single-instance"></a>Egyetlen példány létrehozása

1. Nyissa meg a Time Series Model választópanelt, és válassza a **menü Példányok** parancsát. A kiválasztott Time Series Insights környezethez társított összes példány megjelenik.

    [![Hozzon létre egy példányt a Példányok első kiválasztásával.](media/v2-update-how-to-tsm/how-to-tsm-instances-panel.png)](media/v2-update-how-to-tsm/how-to-tsm-instances-panel.png#lightbox)

1. Válassza a **+ Hozzáadás** lehetőséget.

    [![Példány hozzáadása a + Hozzáadás gomb kiválasztásával.](media/v2-update-how-to-tsm/how-to-tsm-add-instance.png)](media/v2-update-how-to-tsm/how-to-tsm-add-instance.png#lightbox)

1. Adja meg a példány részleteit, válassza ki a típus- és hierarchiatársítást, és válassza a **Létrehozás gombot.**

### <a name="bulk-upload-one-or-more-instances"></a>Tömeges feltöltés egy vagy több példányra

> [!TIP]
> A json-i példányokat az asztalra mentheti. A letöltött JSON fájl ezután feltölthető a következő lépéseken keresztül.

1. Válassza **a JSON feltöltése**lehetőséget.
1. Jelölje ki a példányok hasznos adatát tartalmazó fájlt.

    [![Tömeges feltöltési példányok a JSON-on keresztül.](media/v2-update-how-to-tsm/how-to-tsm-bulk-upload-instances.png)](media/v2-update-how-to-tsm/how-to-tsm-bulk-upload-instances.png#lightbox)

1. Válassza a **Feltöltés** lehetőséget.

### <a name="edit-a-single-instance"></a>Egyetlen példány szerkesztése

1. Jelölje ki a példányt, és jelölje ki a **szerkesztés** vagy **a ceruza ikont**. 
1. Hajtsa végre a szükséges módosításokat, és válassza a **Mentés gombot.**

    [![Egyetlen példány szerkesztése.](media/v2-update-how-to-tsm/how-to-tsm-edit-instance.png)](media/v2-update-how-to-tsm/how-to-tsm-edit-instance.png#lightbox)

### <a name="delete-an-instance"></a>Példány törlése

1. Jelölje ki a példányt, és válassza a **törlési** vagy **hulladéktároló ikont**.

   [![Példány törlése a Törlés gombra kattintva.](media/v2-update-how-to-tsm/how-to-tsm-delete-instance.png)](media/v2-update-how-to-tsm/how-to-tsm-delete-instance.png#lightbox)

1. A törlés megerősítésének megerősítéséhez válassza a **Törlés**lehetőséget.

> [!NOTE]
> Egy példánynak sikeresen át kell mennie egy törlésre kerülő mezőérvényesítési ellenőrzésen.

## <a name="hierarchies"></a>Hierarchiák

Az Azure Time Series Insights explorer támogatja a hierarchia **CREATE,** **READ,** **UPDATE**és **DELETE** műveleteket a böngészőben. 

Először válassza ki a **Modell** nézetet a Time Series Insights explorer **Analyze** nézetből.

### <a name="create-a-single-hierarchy"></a>Egyetlen hierarchia létrehozása

1. Nyissa meg a Time Series Model választópanelt, és válassza a **menü Hierarchiák** elemét. A kiválasztott Time Series Insights környezethez társított összes hierarchia megjelenik.

    [![Hierarchia létrehozása az ablaktáblán keresztül.](media/v2-update-how-to-tsm/how-to-tsm-hierarchy-panel.png)](media/v2-update-how-to-tsm/how-to-tsm-hierarchy-panel.png#lightbox)

1. Válassza a **+ Hozzáadás** lehetőséget.

    [![Hierarchia + Hozzáadás gomb.](media/v2-update-how-to-tsm/how-to-tsm-add-new-hierarchy.png)](media/v2-update-how-to-tsm/how-to-tsm-add-new-hierarchy.png#lightbox)

1. Válassza a **+ Szint hozzáadása** lehetőséget a jobb oldali ablaktáblában.

    [![Szint hozzáadása a hierarchiához.](media/v2-update-how-to-tsm/how-to-tsm-save-hierarchy-levels.png)](media/v2-update-how-to-tsm/how-to-tsm-save-hierarchy-levels.png#lightbox)

1. Adja meg a hierarchia részleteit, és válassza a **Mentés gombot.**

    [![Adja meg a hierarchia részleteit.](media/v2-update-how-to-tsm/how-to-tsm-add-hierarchy-level.png)](media/v2-update-how-to-tsm/how-to-tsm-add-hierarchy-level.png#lightbox)

### <a name="bulk-upload-one-or-more-hierarchies"></a>Tömeges feltöltés egy vagy több hierarchia

> [!TIP]
> A hierarchiákat a JSON-ban mentheti az asztalra. A letöltött JSON fájl ezután feltölthető a következő lépéseken keresztül.

1. Válassza **a JSON feltöltése**lehetőséget.
1. Jelölje ki a hierarchia hasznos adatát tartalmazó fájlt.
1. Válassza a **Feltöltés** lehetőséget.

    [![A hierarchiák tömeges feltöltésének kiválasztása.](media/v2-update-how-to-tsm/how-to-tsm-bulk-upload-hierarchies.png)](media/v2-update-how-to-tsm/how-to-tsm-bulk-upload-hierarchies.png#lightbox)

### <a name="edit-a-single-hierarchy"></a>Egyetlen hierarchia szerkesztése

1. Jelölje ki a hierarchiát, és jelölje ki a **szerkesztés** vagy **a ceruza ikont**.
1. Hajtsa végre a szükséges módosításokat, és válassza a **Mentés gombot.**

    [![Kijelölések egyetlen hierarchia szerkesztéséhez.](media/v2-update-how-to-tsm/how-to-tsm-edit-hierarchy.png)](media/v2-update-how-to-tsm/how-to-tsm-edit-hierarchy.png#lightbox)

### <a name="delete-a-hierarchy"></a>Hierarchia törlése

1. Jelölje ki a hierarchiát, és válassza a **törlési** vagy **hulladéktároló ikont**. 

    [![Hierarchia törlése a Törlés gomb kiválasztásával.](media/v2-update-how-to-tsm/how-to-tsm-delete-hierarchy.png)](media/v2-update-how-to-tsm/how-to-tsm-delete-hierarchy.png#lightbox)

1. A törlés megerősítésének megerősítéséhez válassza a **Törlés**lehetőséget.

## <a name="types"></a>Típusok

Az Azure Time Series Insights explorer támogatja a Type **CREATE,** **READ,** **UPDATE**és **DELETE** műveleteket a böngészőben. 

Először válassza ki a **Modell** nézetet a Time Series Insights explorer **Analyze** nézetből.

### <a name="create-a-single-type"></a>Egyetlen típus létrehozása

1. Nyissa meg a Time Series Model választópanelt, és válassza a menü **Típusok** parancsát. A kiválasztott Time Series Insights környezethez társított összes típus megjelenik.

    [![Idősorozatmodell-típusok ablaktáblája.](media/v2-update-how-to-tsm/how-to-tsm-type-panel.png)](media/v2-update-how-to-tsm/how-to-tsm-type-panel.png#lightbox)

1. Válassza **a + Hozzáadás** lehetőséget az **Új típus** előugró mód hozzáadása beállításmegjelenítéséhez.
1. Adja meg a típus tulajdonságait és változóit. A belépés után válassza a **Mentés gombot.** 

    [![Konfigurációs beállítások egy típus hozzáadásához.](media/v2-update-how-to-tsm/how-to-tsm-add-new-type.png)](media/v2-update-how-to-tsm/how-to-tsm-add-new-type.png#lightbox)

### <a name="bulk-upload-one-or-more-types"></a>Tömeges feltöltés egy vagy több típusra

> [!TIP]
> A JSON-ban mentheti a típusokat az asztalra. A letöltött JSON fájl ezután feltölthető a következő lépéseken keresztül.

1. Válassza **a JSON feltöltése**lehetőséget.
1. Jelölje ki a hasznos adat típusú fájlt tartalmazó fájlt.
1. Válassza a **Feltöltés** lehetőséget.

    [![Tömeges típusok feltöltési beállításai.](media/v2-update-how-to-tsm/how-to-tsm-bulk-upload-types-json.png)](media/v2-update-how-to-tsm/how-to-tsm-bulk-upload-types-json.png#lightbox)

### <a name="edit-a-single-type"></a>Egyetlen típus szerkesztése

1. Jelölje ki a szöveget, és jelölje ki a **szerkesztés** vagy **a ceruza ikont**.
1. Hajtsa végre a szükséges módosításokat, és válassza a **Mentés gombot.**

    [![Szöveg szerkesztése az ablaktáblán.](media/v2-update-how-to-tsm/how-to-tsm-edit-type.png)](media/v2-update-how-to-tsm/how-to-tsm-edit-type.png#lightbox)

### <a name="delete-a-type"></a>Típus törlése

1. Válassza ki a típust, és válassza a **törlési** vagy **hulladéktároló ikont**. .

   [![A Törlés gombra kattintva törölhet egy típust.](media/v2-update-how-to-tsm/how-to-tsm-delete-type.png)](media/v2-update-how-to-tsm/how-to-tsm-delete-type.png#lightbox)

1. A törlés megerősítésének megerősítéséhez válassza a **Törlés**lehetőséget.

## <a name="next-steps"></a>További lépések

- Az Idősorozat-modellről az [Adatmodellezés](./time-series-insights-update-tsm.md)című olvasnivalócímű további információt talál.

- Ha többet szeretne megtudni az előzetes verzióról, olvassa el [a Visualize-adatok at the Azure Time Series Insights Preview explorer](./time-series-insights-update-explorer.md).

- A támogatott JSON-alakzatokról a [Támogatott JSON-alakzatok](./time-series-insights-send-events.md#supported-json-shapes)című olvasni tudó továbbcímű olvasni tudó.
