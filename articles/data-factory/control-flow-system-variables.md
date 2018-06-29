---
title: Az Azure Data Factory rendszerváltozók |} Microsoft Docs
description: Ez a cikk ismerteti az Azure Data Factory által támogatott rendszerváltozók. Ezek a változók használhatja kifejezések adat-előállító entitások meghatározásakor.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/12/2018
ms.author: shlo
ms.openlocfilehash: 43ea8703bdbfc23511c831a5f91c9461948cc254
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37059075"
---
# <a name="system-variables-supported-by-azure-data-factory"></a>Azure Data Factory által támogatott rendszerváltozók
Ez a cikk ismerteti az Azure Data Factory által támogatott rendszerváltozók. Ezek a változók használhatja kifejezések adat-előállító entitások meghatározásakor.

## <a name="pipeline-scope"></a>Feldolgozási sor hatókör
Rendszer változókhoz az adatcsatorna JSON bárhol lehet hivatkozni.

| Változó neve | Leírás |
| --- | --- |
| @pipeline().DataFactory |A Futtatás futószalag adat-előállító nevét belül fut. |
| @pipeline().Pipeline |A folyamat neve |
| @pipeline().RunId | Futtassa a megadott folyamat azonosítója |
| @pipeline().TriggerType | Az eseményindító az adatcsatorna (Manuális, a Feladatütemező) definiálásra típusa |
| @pipeline().TriggerId| Az eseményindító, amely hívja meg a folyamat azonosítója |
| @pipeline().TriggerName| Az eseményindító, amely hívja meg a folyamat neve |
| @pipeline().TriggerTime| Időpontot, amikor az eseményindító a feldolgozási sor definiálásra. Az indítási idő az a tényleges égetett idő, nem a megadott időpont. Például `13:20:08.0149599Z` helyett adja vissza `13:20:00.00Z` |

## <a name="schedule-trigger-scope"></a>Ütemezés eseményindító hatóköre
Rendszer változókhoz hivatkozható bárhol az eseményindító JSON Ha az eseményindító típusú: "ScheduleTrigger."

| Változó neve | Leírás |
| --- | --- |
| @trigger.scheduledTime) |Idő, amikor az eseményindító meghívni a folyamat futtatása lett ütemezve. Például egy eseményindító, amely akkor következik be, minden 5 perc, a változó alakítanák vissza `2017-06-01T22:20:00Z`, `2017-06-01T22:25:00Z`, `2017-06-01T22:29:00Z` kulcsattribútumokkal.|
| @trigger.startTime) |Időpontot, amikor az eseményindító **ténylegesen** meghívni a Futtatás futószalag következik be. Például egy eseményindító, amely akkor következik be, minden 5 perc, ez a változó lehet, hogy térjen vissza ehhez hasonló `2017-06-01T22:20:00.4061448Z`, `2017-06-01T22:25:00.7958577Z`, `2017-06-01T22:29:00.9935483Z` kulcsattribútumokkal.|

## <a name="tumbling-window-trigger-scope"></a>Átfedésmentes ablak eseményindító hatóköre
Rendszer változókhoz hivatkozható bárhol az eseményindító JSON Ha az eseményindító típusú: "TumblingWindowTrigger."

| Változó neve | Leírás |
| --- | --- |
| @trigger(). outputs.windowStartTime |Ha az eseményindító meghívni a feldolgozási sor futtatása lett ütemezve. az ablak kezdő. Ha az átfedésmentes ablak eseményindító "óránként" gyakorisággal lenne az idő az óra elején.|
| @trigger(). outputs.windowEndTime |Ha az eseményindító meghívni a folyamat futtatása lett ütemezve. az ablak végét. Ha az átfedésmentes ablak eseményindító "óránként" gyakorisággal lenne az időpontot, az óra.|
## <a name="next-steps"></a>További lépések
Ezek a változók használata a kifejezések kapcsolatos információkért lásd: [kifejezés nyelvi & a funkciók](control-flow-expression-language-functions.md).
