---
title: Az Azure Data Factoryban rendszerváltozók |} A Microsoft Docs
description: Ez a cikk ismerteti az Azure Data Factory által támogatott rendszerváltozók. Ezeket a változókat használhatja kifejezések Data Factory-entitások meghatározásakor.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 06/12/2018
ms.author: shlo
ms.openlocfilehash: 9a4d5acfe16a2fdbb3b631cb8baf6cb8e90a7d58
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54016289"
---
# <a name="system-variables-supported-by-azure-data-factory"></a>Az Azure Data Factory által támogatott rendszerváltozók
Ez a cikk ismerteti az Azure Data Factory által támogatott rendszerváltozók. Ezeket a változókat használhatja kifejezések Data Factory-entitások meghatározásakor.

## <a name="pipeline-scope"></a>Folyamat hatókör
Ezek rendszerváltozók bárhol lehet hivatkozni a folyamat JSON-Fájljában.

| Változó neve | Leírás |
| --- | --- |
| @pipeline().DataFactory |A data factory a folyamatfuttatás nevét belül fut. |
| @pipeline().Pipeline |A folyamat neve |
| @pipeline().RunId | Az adott folyamat futásának Azonosítóját |
| @pipeline().TriggerType | Az (Manuális, a Feladatütemező) a folyamatot elindító eseményindító típusa |
| @pipeline().TriggerId| A folyamat az eseményindító azonosítója |
| @pipeline().TriggerName| A folyamat az eseményindító neve |
| @pipeline().TriggerTime| Időpontot, amikor az a folyamatot elindító eseményindító. A trigger idő a tényleges aktivált idő, az ütemezett időpont nem az. Ha például `13:20:08.0149599Z` helyett adja vissza `13:20:00.00Z` |

## <a name="schedule-trigger-scope"></a>Ütemezési eseményindító hatóköre
Ezeket a rendszer változókat lehet hivatkozni bárhol az eseményindító JSON típusú trigger esetén: "ScheduleTrigger."

| Változó neve | Leírás |
| --- | --- |
| @trigger.scheduledTime) |Idő, amikor az eseményindító meghívni a folyamat futtatása lett ütemezve. Ha például egy eseményindító, amely akkor aktiválódik, 5 percenként, ezzel a változóval adna vissza `2017-06-01T22:20:00Z`, `2017-06-01T22:25:00Z`, `2017-06-01T22:29:00Z` jelölik.|
| @trigger.startTime) |Időpontot, amikor az eseményindító **ténylegesen** aktivált meghívni a folyamat futtatását. Például az eseményindító, amely akkor aktiválódik, 5 percenként, ez a változó lehet, hogy visszaadni valami ilyesmit `2017-06-01T22:20:00.4061448Z`, `2017-06-01T22:25:00.7958577Z`, `2017-06-01T22:29:00.9935483Z` jelölik.|

## <a name="tumbling-window-trigger-scope"></a>Az átfedésmentes ablakos eseményindító hatóköre
Ezeket a rendszer változókat lehet hivatkozni bárhol az eseményindító JSON típusú trigger esetén: "TumblingWindowTrigger."

| Változó neve | Leírás |
| --- | --- |
| @trigger(). outputs.windowStartTime |Az ablakot, amikor az eseményindító lett ütemezve, a folyamat futásának indítása kezdete. Ha az átfedésmentes ablakos eseményindító "óránkénti" gyakoriságú ez lenne az idő, az óra elején.|
| @trigger(). outputs.windowEndTime |Amikor az eseményindító meghívni a folyamat futtatása lett ütemezve. az ablak végén. "Óránként" gyakorisága az átfedésmentes ablakos eseményindító-e ez lenne az az idő az óra végén.|
## <a name="next-steps"></a>További lépések
Ezek a változók használata a kifejezések kapcsolatos információkért lásd: [kifejezésnyelveket & funkciók](control-flow-expression-language-functions.md).
