---
title: Rendszerváltozók a Azure Data Factoryban
description: Ez a cikk a Azure Data Factory által támogatott rendszerváltozókat ismerteti. Ezeket a változókat kifejezésekben használhatja Data Factory entitások definiálásához.
services: data-factory
documentationcenter: ''
author: dcstwh
ms.author: weetok
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/12/2018
ms.openlocfilehash: 1780b4a64de349c1e272158fe6bfde9cab6f8369
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/02/2020
ms.locfileid: "96486045"
---
# <a name="system-variables-supported-by-azure-data-factory"></a>Azure Data Factory által támogatott rendszerváltozók
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ez a cikk a Azure Data Factory által támogatott rendszerváltozókat ismerteti. Ezeket a változókat kifejezésekben használhatja Data Factory entitások definiálásához.

## <a name="pipeline-scope"></a>Folyamat hatóköre
Ezeket a rendszerváltozókat a folyamat JSON-ban bárhol lehet hivatkozni.

| Változó neve | Description |
| --- | --- |
| @pipeline(). DataFactory |Annak az adatelőállítónak a neve, amelyen a folyamat futása fut |
| @pipeline(). Folyamat |A folyamat neve |
| @pipeline(). RunId | Az adott folyamat futtatásának azonosítója |
| @pipeline(). TriggerType | Az adatfolyamatot meghívó eseményindító típusa (manuális, ütemező) |
| @pipeline(). TriggerId| A folyamatot meghívó trigger azonosítója |
| @pipeline(). TriggerName| A folyamatot meghívó trigger neve |
| @pipeline(). TriggerTime| A folyamatot meghívó eseményindító időpontja. Az aktiválási idő a tényleges kilőtt idő, nem az ütemezett időpont. Például a `13:20:08.0149599Z` visszaadott érték a következő helyett: `13:20:00.00Z` |

## <a name="schedule-trigger-scope"></a>Trigger hatókörének ütemterve
Ezek a rendszerváltozók az trigger JSON-ban bárhol szerepelhetnek, ha az trigger típusa: "ScheduleTrigger".

| Változó neve | Description |
| --- | --- |
| @trigger().scheduledTime |Az az idő, amikor az eseményindító ütemezve lett a folyamat futásának meghívására. Például egy 5 percenként elindított trigger esetén ez a változó a következőt fogja visszaadni: `2017-06-01T22:20:00Z` `2017-06-01T22:25:00Z` `2017-06-01T22:30:00Z` .|
| @trigger(). kezdő időpont |Az az idő, amikor az eseményindító **ténylegesen** elindította a folyamat futtatását. Például egy 5 percenként elindított trigger esetén ez a változó a következőhöz hasonló értéket adhat vissza: `2017-06-01T22:20:00.4061448Z` `2017-06-01T22:25:00.7958577Z` `2017-06-01T22:30:00.9935483Z` . (Megjegyzés: az időbélyeg alapértelmezés szerint ISO 8601 formátumú)|

## <a name="tumbling-window-trigger-scope"></a>Kiesési ablak Triggerének hatóköre
Ezek a rendszerváltozók az trigger JSON-ban bárhol szerepelhetnek, ha az trigger típusa: "TumblingWindowTrigger".
(Megjegyzés: az időbélyeg alapértelmezés szerint ISO 8601 formátumú)

| Változó neve | Description |
| --- | --- |
| @trigger(). outputs. windowStartTime |Az ablak elindítása, amikor az eseményindító ütemezve lett a folyamat futásának meghívására. Ha a kieséses ablak triggerének gyakorisága "óránként", ez az idő az óra elején.|
| @trigger(). outputs. windowEndTime |Az ablak vége, amikor az eseményindító ütemezve lett a folyamat futásának meghívására. Ha a kieséses ablak triggerének "óránként" gyakorisága van, akkor ez az idő az óra végén.|
## <a name="next-steps"></a>További lépések
További információ a változók kifejezésekben való használatáról: [Expression language & functions](control-flow-expression-language-functions.md).
