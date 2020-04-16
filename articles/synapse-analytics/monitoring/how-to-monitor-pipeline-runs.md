---
title: A figyelőfolyamat az Azure Synapse Studio-t futtatja (előzetes verzió)
description: Az Azure Synapse Studio segítségével figyelheti a munkaterületi folyamat fut.
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.date: 04/15/2020
ms.author: mahi
ms.reviewer: mahi
ms.openlocfilehash: 712dc62e29229f03dec12afdf18edbf55667dbdf
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81430785"
---
# <a name="use-azure-synapse-studio-to-monitor-your-workspace-pipeline-runs"></a>Az Azure Synapse Studio segítségével figyelheti a munkaterületi folyamat futásait

Az Azure Synapse Analytics segítségével összetett folyamatokat hozhat létre, amelyek automatizálhatják és koordinálhatják az adatmozgatást, az adatátalakítást és a számítási tevékenységeket a megoldáson belül. Ezeket a folyamatokat az Azure Synapse Studio használatával (előzetes verzió) hozhatja létre és figyelheti.

Ez a cikk ismerteti, hogyan figyelheti a folyamat fut, amely lehetővé teszi, hogy tartsa szemmel a legújabb állapotát, a problémákat és a folyamatok előrehaladását.

## <a name="access-the-list-of-pipeline-runs"></a>A folyamatfuttatások listájának elérése

A munkaterületen futó folyamatlistájának megtekintéséhez nyissa [meg először az Azure Synapse Studio-t,](https://web.azuresynapse.net/) és válassza ki a munkaterületet.

![Bejelentkezés a munkaterületre](./media/common/login-workspace.png)

Miután megnyitotta a munkaterületet, válassza a bal oldali **Figyelő** szakaszt.

![A Figyelő központ kiválasztása](./media/common/left-nav.png)

Válassza **a Folyamatfuttatások** lehetőséget a folyamatfuttatások listájának megtekintéséhez.

![Folyamatfuttatások kiválasztása](./media/how-to-monitor-pipeline-runs/monitor-hub-nav-pipelineruns.png)

## <a name="filtering-your-pipeline-runs"></a>A folyamat futtatásának szűrése

Szűrheti a folyamatfuttatások listáját az önt érdeklő folyamatokra. A képernyő tetején lévő szűrők lehetővé teszik, hogy megadjon egy mezőt, amelyre szűrni szeretne.

Szűrheti például a nézetet, hogy csak a "holiday" nevű folyamat folyamatfuttatása jelenjen meg:

![Szűrő gomb](./media/common/filter-button.png)

![Mintaszűrő](./media/how-to-monitor-pipeline-runs/filter-example.png)

## <a name="viewing-details-about-a-specific-pipeline-run"></a>Adott folyamatfuttatás részleteinek megtekintése

A folyamatfuttatásával kapcsolatos részletek megtekintéséhez válassza ki a folyamatfuttatást. Ezután tekintse meg a folyamatfuttatásához társított tevékenységfuttatásokat. Ha a folyamat még mindig fut, figyelheti a folyamatot. 
  
## <a name="next-steps"></a>További lépések

Az alkalmazások figyeléséről az [Apache Spark-alkalmazások figyelése](how-to-monitor-spark-applications.md) című cikkben olvashat bővebben. 
