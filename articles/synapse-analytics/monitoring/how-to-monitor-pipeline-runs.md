---
title: Figyelő folyamat futtatja az Azure szinapszis Studio (előzetes verzió)
description: Az Azure szinapszis Studio használatával figyelheti a munkaterület-folyamat futtatását.
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: monitoring
ms.date: 04/15/2020
ms.author: mahi
ms.reviewer: mahi
ms.openlocfilehash: 16400d7f292ad5844add3d4a5fc019e84bd27127
ms.sourcegitcommit: 3988965cc52a30fc5fed0794a89db15212ab23d7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/22/2020
ms.locfileid: "85194892"
---
# <a name="use-azure-synapse-studio-to-monitor-your-workspace-pipeline-runs"></a>A munkaterület-folyamat futtatásának figyelése az Azure szinapszis Studio használatával

Az Azure szinapszis Analytics segítségével összetett folyamatokat hozhat létre, amelyek automatizálják és összehangolják az adatáthelyezést, az adatátalakítást és a számítási tevékenységeket a megoldáson belül. Ezeket a folyamatokat az Azure szinapszis Studio (előzetes verzió) használatával hozhatja létre és figyelheti.

Ez a cikk ismerteti, hogyan figyelheti meg a folyamat futásait, így nyomon követheti a folyamatok legújabb állapotát, problémáit és előrehaladását.

## <a name="access-the-list-of-pipeline-runs"></a>A folyamat-futtatások listájának elérése

A munkaterületen futó folyamatok listájának megtekintéséhez először [nyissa meg az Azure szinapszis Studio](https://web.azuresynapse.net/) alkalmazást, és válassza ki a munkaterületet.

![Bejelentkezés a munkaterületre](./media/common/login-workspace.png)

Miután megnyitotta a munkaterületet, kattintson a bal oldalon található **figyelő** szakaszra.

![Figyelő központ kiválasztása](./media/common/left-nav.png)

Válassza a **folyamat futtatása** lehetőséget a folyamat-futtatások listájának megtekintéséhez.

![Folyamat futtatásának kiválasztása](./media/how-to-monitor-pipeline-runs/monitor-hub-nav-pipelineruns.png)

## <a name="filtering-your-pipeline-runs"></a>A folyamat futtatásának szűrése

A folyamatok listájának szűrésével megtekintheti azokat, amelyekre kíváncsi. A képernyő felső részén található szűrők segítségével megadhat egy mezőt, amely alapján szűrni szeretne.

A nézetet szűrheti például úgy, hogy csak a "Holiday" nevű folyamathoz tartozó folyamat fusson.

![Szűrő gomb](./media/common/filter-button.png)

![Minta szűrő](./media/how-to-monitor-pipeline-runs/filter-example.png)

## <a name="viewing-details-about-a-specific-pipeline-run"></a>Adott folyamat futtatási részleteinek megtekintése

A folyamat futtatásával kapcsolatos részletek megtekintéséhez válassza ki a folyamat futtatását. Ezután tekintse meg a folyamat futtatásához társított tevékenységek futtatását. Ha a folyamat továbbra is fut, nyomon követheti a folyamatot. 
  
## <a name="next-steps"></a>További lépések

Az alkalmazások figyelésével kapcsolatos további tudnivalókért tekintse meg a [Apache Spark alkalmazások figyelése](how-to-monitor-spark-applications.md) című cikket. 
